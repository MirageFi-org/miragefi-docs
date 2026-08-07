---
description: The quote asset that every MirageFi market is priced against.
---

# Quote asset: USDG

**USDG**, the Paxos Global Dollar, is the quote asset for every market. Spreads, fees and vault accounting are all denominated in it, and token-to-token swaps are routed through it.

## Why USDG

| Reason | Detail |
| --- | --- |
| Native to the chain | USDG is issued natively on Robinhood Chain, not bridged, so bridge risk is absent from the settlement path. |
| The chain's dollar | Robinhood Earn and the chain's lending markets already operate on USDG through Morpho. The dollar traders arrive with is therefore the dollar the venue quotes in. |
| Regulatory footing | In the EU, USDG is regulated under MiCA, which matters to a venue whose primary market is the Stock Token jurisdiction list. |
| Clean semantics | A standard ERC-20 with no fee-on-transfer and no rebasing: exactly the properties vault accounting relies on. |

## A single quote asset is a design choice

Putting everything through one quote asset concentrates liquidity rather than splitting each Stock Token across several stablecoin pairs. It also makes every price directly comparable and keeps two-leg [routing](../protocol/routing.md) simple enough to verify on-chain. The price is a dependency on a single issuer, disclosed alongside the other dependencies in the [Risk framework](../risk/framework.md).

## Additional quote assets

Because the market configuration is keyed on the quote asset, a second one (USDC via CCTP once bridged liquidity on the chain is confirmed, or USDe, which already exists on the chain) would form a new and isolated set of markets, leaving existing ones untouched. A new quote asset is added only after bridged liquidity has been confirmed and the token has passed the same bytecode review as any listed asset, and it goes through the timelock like every other parameter.

## Decimals and precision

USDG has 6 decimals; Stock Tokens have 18. `OracleRouter` returns prices in quote-asset base units per whole token, vault value is calculated as `usdgBalance + tokenBalance × price / 10^18`, and the invariant suite exercises the arithmetic across both decimal regimes, including the rounding direction on share mints and burns.
