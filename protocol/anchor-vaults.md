---
description: The per-asset vaults that hold inventory and quote both sides of each market.
---

# Anchor vaults

One anchor vault is one market: a single Stock Token, USDG, and the LPs who fund both sides of it. Except during a halt, the vault always quotes a bid and an ask around the oracle mid, fills swaps up to its clip size, and passes the spread through to its LPs.

## Inventory model

Every vault targets a configured inventory mix (50/50 by value at launch) and operates within a hard band around that target.

| Parameter | Meaning | Tier A initial value |
| --- | --- | --- |
| `targetRatioBps` | Share of vault value the vault aims to hold in the Stock Token | 5,000 (50%) |
| `inventoryBandBps` | How far the mix may drift from target before one side stops quoting | 2,000 (±20%) |
| `maxClip` | The largest single swap the vault will fill | 50,000 USDG |
| `dailyVolumeCap` | Volume ceiling per market during the guarded launch | 2,000,000 USDG |

As traders buy the token, the vault's token inventory drops and its USDG grows; it is now short of target, and [skew pricing](pricing-and-spreads.md) improves its bid while worsening its ask, which pays arbitrageurs and makers to push inventory back toward target. When flow is one-way enough to hit the edge of the band, the vault stops quoting that side altogether instead of accepting unbounded inventory risk. The opposite side continues to quote.

## Why inventory, not a curve

A constant-product pool holds inventory too, but its price is derived from that inventory, so each move in the reference price leaves it showing stale quotes that arbitrageurs collect. An anchor vault derives its price from the oracle; inventory only shifts the spread. The vault is never the last participant to learn what the asset is worth, so its LPs collect the spread rather than funding the arbitrage. The trade-off is stated plainly: the vault relies on the oracle, and every guard on that reliance is described in [Oracles and market sessions](../risk/oracles.md).

## Value accounting

Vault value equals `usdgBalance + tokenBalance × mid`, marked at the guarded oracle price. LP shares use ERC-4626-style accounting over that value: a deposit mints shares at the current value per share, spread revenue accrues into that value, and a withdrawal burns shares in exchange for a pro-rata, in-kind mix of both assets. Since withdrawals are in kind, an LP leaving never forces the vault to trade, and a pause can never trap LP funds. The details and the LP economics are covered in [Liquidity provision](liquidity-provision.md).

## Lifecycle

| State | Meaning |
| --- | --- |
| `ACTIVE` | Both sides quoting, subject to the regime |
| `ONE_SIDED` | Inventory at the band edge; only the rebalancing side quotes |
| `HALTED` | No quotes: the oracle is stale, `oraclePaused()` is set, a corporate action is in progress, or a guardian has paused. Deposits and withdrawals stay open |
| `RETIRED` | Delisted: quoting is off for good, withdrawals only |

The `VaultFactory` deploys each vault from the parameters fixed at [listing](../assets/listing-framework.md), and each parameter is a `ParamController` value that can only change through the timelock.

## Invariants

These are the properties the contract enforces and the test suite proves:

* In any state and under any parameter set, no fill ever clears outside the oracle band.
* A swap never reduces value per share (spread revenue is non-negative by construction).
* Neither `HALTED` nor `RETIRED` ever restricts withdrawals.
* The vault's own quoting can never push inventory beyond the band.
