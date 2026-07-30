---
description: How LPs fund the vaults, what they earn from them, and precisely which risks they take on.
---

# Liquidity provision

LPs fund the anchor vaults and collect the spread on each fill a vault makes. The design aims for an LP's return to come from a genuine service, immediacy in a market that has an external reference price, and not from acting as the unpaid counterparty to arbitrage.

## Depositing

Deposits are made per market and are open to anyone holding an `LP` attestation. A deposit may consist of USDG, the market's Stock Token, or both; it is valued at the guarded oracle mid and mints shares at the current value per share. There are no deposit fees and no lockups.

Shares use ERC-4626-style accounting. Value per share is `(usdgBalance + tokenBalance × mid) / totalShares`, and it rises as spread revenue accrues. Shares can be transferred only between attested LPs.

## Withdrawing

A withdrawal burns shares in exchange for a **pro-rata, in-kind** slice of the vault's current inventory: an LP with 1% of the shares receives 1% of the vault's USDG and 1% of its tokens. In-kind withdrawal is a deliberate property, and it has three consequences:

* An exit never forces the vault to trade, so exits cannot move the market and cannot be sandwiched.
* Withdrawals function in every vault state, `HALTED` and `RETIRED` included. No pause, halt or parameter is able to trap LP funds.
* The mix an LP receives mirrors the vault's current skew. An LP who wants only USDG swaps the token leg as any trader would.

## What LPs earn

On every fill, the vault's realised spread less the protocol's 10% [fee share](fees.md) accrues continuously into value per share. There are no emissions and no points; the yield is the market's real cost of immediacy. Realised spread revenue for each market is published live on the [trade explorer](../users/trade-explorer.md), so an LP can underwrite a vault on its record instead of a projection.

## What LPs risk

Set out plainly, since an LP who has not priced these should not deposit:

| Risk | Nature | Mitigation |
| --- | --- | --- |
| Price exposure | The vault holds the Stock Token, so its value tracks the stock. By design, LPs are long the asset roughly half of the time | Per-market isolation; the LP decides which assets to hold |
| Gap risk | Fills made overnight and at weekends happen at prices that can gap when the market opens | The closed-regime multipliers and clip reductions exist specifically to charge for this |
| Oracle risk | A wrong mid produces wrong quotes | The guard stack described in [Oracles and market sessions](../risk/oracles.md); the band limits the damage on any one fill |
| Issuer risk | Stock Tokens are debt claims on the issuer | See [Issuer risk](../risk/issuer.md); it is disclosed, not diversified away |
| Adverse flow | Persistent one-way flow leaves the vault sitting at its band edge | The vault turns one-sided instead of absorbing unbounded inventory |

What LPs do **not** carry: leverage, liquidation risk, losses socialised from other markets, or exposure to assets they did not select. Every vault is an island.

## Guarded launch

Throughout the guarded phase, per-market TVL caps together with the daily volume caps limit total exposure while parameters are tuned against realised data. The caps are raised through the timelock as the [execution quality](../transparency/execution-quality.md) record builds up, following the schedule in the [Roadmap](../roadmap/phases.md).
