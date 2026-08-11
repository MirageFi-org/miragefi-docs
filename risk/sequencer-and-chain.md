---
description: What the venue does when the chain underneath it misbehaves.
---

# Sequencer and chain risk

Robinhood Chain is an Arbitrum Nitro rollup whose single sequencer is operated by Robinhood. For an L2 at this stage that is the norm, and it brings specific failure modes that a 24/7 trading venue must design for rather than hope past.

## What can go wrong

| Scenario | Effect on users |
| --- | --- |
| Sequencer outage | For the duration, no transactions are included on the L2. The underlying market keeps moving. |
| Sequencer censorship | Particular transactions (an LP withdrawal, say, or a maker's quote cancellation) are left out. |
| Delayed L1 finality | Withdrawals to Ethereum take the canonical 7 days; on-L2 trading is unaffected. |
| Chain configuration change | A change to DA mode, the validator set, or the enabling of Timeboost or BoLD could alter finality and ordering assumptions. |

## Mitigations

### Trading grace after an outage

`OracleRouter` reads the Chainlink L2 Sequencer Uptime Feed. When that feed reports the sequencer has just returned, every market remains `HALTED` for a grace period of **one hour**. While the sequencer was down the underlying market kept moving, LPs could not withdraw and parameters could not respond; the grace period gives oracle rounds time to refresh and participants time to act before anyone can trade against a vault that was frozen mid-move. On recovery, withdrawals and cancellations work at once; only quoting waits.

### Everything is reachable through the L1 delayed inbox

Any state-changing function in MirageFi can be submitted via Arbitrum's delayed inbox on Ethereum. A transaction the sequencer has not included within 24 hours can be force-included. In practice:

* a censoring sequencer cannot stop an LP from withdrawing,
* a censoring sequencer cannot stop a maker from cancelling quotes by nonce,
* the guardian's pause and the timelock's parameter changes are equally uncensorable.

Forced inclusion is not meaningful for swaps themselves, because a quote goes stale long before 24 hours pass, but nothing that protects funds depends on the sequencer cooperating.

### Timestamps, not block numbers

On Arbitrum-stack chains, `block.number` returns a value derived from L1. Quote expiries, staleness bounds, grace periods and the timelock therefore use `block.timestamp` exclusively. Where an L2 block height is ever required, the protocol calls `ArbSys.arbBlockNumber()`.

### Calldata cost

The L1 data portion of gas is priced on compressed calldata size. RFQ quote structs are tightly packed and signatures are passed in the compact 64-byte form wherever possible, which keeps settlement below a cent even when blob prices spike.

## Ordering policy watch

Today the sequencer is first-come-first-served, with preconfirmations of about 100 ms. Whether Timeboost (express-lane ordering) or BoLD (permissionless validation) get enabled on Robinhood Chain changes how quote competition and forced inclusion behave. MirageFi watches both. The design already assumes no favourable ordering, since fills are bounded by the signed slippage limit and the oracle band whatever their position in a block, and any configuration change triggers a published review in the governance log.
