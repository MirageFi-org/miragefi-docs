---
description: The one entry point that combines vaults and RFQ into a single quote.
---

# Routing

Traders interact with exactly one contract, `SwapRouter`. It has four responsibilities: eligibility, venue selection, multi-leg composition, and enforcing every bound a fill has to respect.

## Venue selection

For a given size, the router weighs the anchor vault (provided the size fits within the clip and the regime permits quoting) against any valid maker quote passed in with the call. It settles whichever path yields the better all-in output for the trader. That comparison happens on-chain and is exact: the vault quote is recomputed from oracle and vault state inside the same transaction rather than trusted from the front-end, and the maker quote is checked by signature. A front-end cannot misroute a trade to a worse price, since the router refuses to settle a supplied path that the vault beats.

## Two-leg swaps

A token-to-token swap runs as two legs through USDG, atomically:

1. The first leg sells `tokenIn` to its vault or maker for USDG.
2. The second leg spends that USDG on `tokenOut` from its vault or maker.

Venue selection happens separately for each leg, so one leg may fill against a vault while the other fills against a maker. Each leg is subject to its own market's band and regime; if one fails, both revert. The fill event records both legs with itemised pricing, and the trader's slippage bound is applied to the end-to-end rate.

## What the router enforces on every fill

| Check | Failure mode prevented |
| --- | --- |
| `TRADER` attestation on the caller | Participation by ineligible parties |
| Oracle band per leg | Any fill away from the guarded reference price |
| Regime permits quoting | Trading during a halt |
| Clip and daily volume caps | Oversized flow while the launch is guarded |
| Trader's slippage bound and deadline | Filling on state that has moved |
| Permit2 pull matches the signed amounts | Misuse of the allowance |

## Ordering and MEV

Robinhood Chain uses a first-come-first-served sequencer with preconfirmations of about 100 ms, which eliminates the public-mempool sandwich as it exists on Ethereum today. The design does not depend on that remaining the case: quotes are already bounded by the signed slippage limit and the oracle band, so the most any ordering advantage can extract is the width the trader already accepted. Should the chain enable Timeboost or change its ordering policy in some other way, the review is published in the governance log. See [Sequencer and chain risk](../risk/sequencer-and-chain.md).

## Integration surface

The router can be called by anyone; it is a public contract with a stable interface, and eligibility is checked per caller rather than per front-end. Wallets, portfolio apps and agents get pricing through the [quote API and SDK](../architecture/api.md) and submit directly to the router. The reference front-end at [miragefi.org/platform](https://miragefi.org/platform) has no privileged path of its own.
