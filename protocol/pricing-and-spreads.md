---
description: The formula that produces every vault quote, together with each input that feeds it.
---

# Pricing and spreads

Each vault quote is constructed in exactly the same way. No part of a price is discretionary; every term comes from a public contract read, which means anyone can rebuild any quote from on-chain state.

## The formula

```
ask = mid × (1 + (baseHalfSpread × regimeMultiplier + skewTerm + feeBps) / 10_000)
bid = mid × (1 − (baseHalfSpread × regimeMultiplier − skewTerm + feeBps) / 10_000)
```

| Term | Source | What it prices |
| --- | --- | --- |
| `mid` | `OracleRouter`, the guarded Chainlink price | The current value of the asset |
| `baseHalfSpread` | Tier parameter | The cost of holding inventory plus residual oracle latency during the regular session |
| `regimeMultiplier` | [Regime](trading-regimes.md) state | Gap risk while the underlying market is in extended hours or closed |
| `skewTerm` | Vault inventory | The cost of moving the vault further from its target (signed: negative on the side that rebalances) |
| `feeBps` | `ParamController` | The protocol fee, shown as its own line on the ticket |

## Initial parameters

Half-spreads are in basis points, set per [tier](../assets/listing-framework.md), and can change only through the timelock.

| Tier | Base half-spread | Regular clip | Examples |
| --- | --- | --- | --- |
| A | 10 bps | 50,000 USDG | SPY, QQQ, AAPL, MSFT, NVDA |
| B | 20 bps | 20,000 USDG | Liquid single names not in Tier A |
| C | 40 bps | 5,000 USDG | Long-tail Stock Tokens |

| Regime | Multiplier | Clip factor |
| --- | --- | --- |
| `OPEN` | 1.0 | 1.0 |
| `EXTENDED` | 1.5 | 0.75 |
| `CLOSED` | 3.0 | 0.5 |
| `HALTED` | No quotes | 0 |

During regular hours a Tier A trade costs roughly 10 bps over mid plus the protocol fee. On a Saturday the same trade costs roughly 30 bps, and the ticket explains the reason. These are the launch values; the realised figures are published on the [execution quality](../transparency/execution-quality.md) page, and parameters are retuned against those figures through the timelock.

## The skew term

`skewTerm = maxSkewBps × (currentRatio − targetRatio) / inventoryBand`

The term is linear in drift and capped at `maxSkewBps` (15 bps for Tier A initially) at the band edge. The sign convention carries the logic: a vault that is long of tokens shows a cheaper ask and a meaner bid, so flow that helps the vault pays less and flow that hurts it pays more. At the band edge the harmful side stops quoting altogether. That is the whole rebalancing mechanism; there are no rebalancing auctions, no keeper trades and no discretionary interventions. The skew pays makers and arbitrageurs to do the work.

## The band

Separately from the spread arithmetic, `SwapRouter` applies a hard band around the mid (75 bps for Tier A initially, wider for B and C), and no fill can clear outside it, whether from a vault or from RFQ. The band is the final defence against a mispriced quote, a manipulated maker signature or a parameter error: no matter what fails upstream, a fill further than the band from the guarded oracle price reverts.

## What the trader sees

The ticket lays out the formula term by term: mid (with the oracle round it was taken from), spread in bps and USDG, protocol fee in bps and USDG, the regime badge, and the all-in price to which the slippage bound applies. The quote the trader signs is the one the chain enforces; if state moves such that the fill would be worse than the signed bound, the transaction reverts instead of filling.
