---
description: The route a swap takes through MirageFi, from the first quote to settlement on-chain.
---

# Protocol overview

Behind a single entry point, MirageFi runs three mechanisms: oracle-anchored vaults for standard-sized trades, RFQ for block-sized trades, and a router that verifies eligibility and settles on whichever venue gives the trade the better price.

## The path of a swap

1. **Quote.** The trader requests a price, for example on 2,000 USDG of tokenized NVDA. The quote service simulates what the router will do: it reads the guarded Chainlink mid from `OracleRouter`, the current [regime](trading-regimes.md), and the inventory of the [anchor vault](anchor-vaults.md), while in parallel gathering signed [RFQ quotes](rfq.md) from makers when the size justifies it. The trader is shown one price, broken down into mid, spread and fee.
2. **Sign.** The trader signs one transaction: a call to `SwapRouter` that carries the swap parameters, a slippage bound, a deadline, and a Permit2 pull for the input asset.
3. **Verify.** On-chain, the router checks the trader's `TRADER` attestation, recomputes the vault quote from the oracle and the vault state (or verifies the maker's EIP-712 signature), and confirms that the fill stays inside both the trader's bound and the protocol's oracle band.
4. **Settle.** Assets move atomically: the input leaves the trader, the output leaves the vault or the maker, and the fee goes to the `FeeCollector`. One transaction, one fill event, visible on Blockscout within seconds.

No order book needs maintaining, and there is no deposit step or withdrawal step. Custody lasts for the length of one transaction, inside immutable contracts.

## Multi-asset swaps

All markets are quoted against USDG. A swap between two listed assets (tokenized AAPL into tokenized SPY, or a Stock Token into tokenized gold once that is listed) is executed as two legs through USDG inside one atomic router call. The trader sees one price; the fill event records each leg. Should either leg fail to clear inside its band, the entire swap reverts.

## The division of labour

| Size | Venue | Why |
| --- | --- | --- |
| Up to the vault clip | Anchor vault | Instant, needs no counterparty coordination, and the spread comes from a formula |
| Above the clip | RFQ | Makers can hedge on the underlying market, so they price size better than a formula can |
| Any size, maker quote better | RFQ | The router settles the better price every time; makers compete against the vault tick by tick |

The clip is a per-market parameter rather than a hard split: makers are free to quote inside the clip and take retail flow whenever they beat the vault.

## What is deliberately absent

* **No pooled cross-asset exposure.** A vault holds a single asset against USDG. LPs pick their exposure market by market.
* **No protocol discretion at fill time.** A vault quote is a formula over public state, and the RFQ path is a signature check. No one at MirageFi can improve, worsen or block any individual fill.
* **No netting or deferred settlement.** Each fill is final the moment it is included.

## Reading order

The mechanics are laid out from the inside outward: [Anchor vaults](anchor-vaults.md) covers the inventory model, [Pricing and spreads](pricing-and-spreads.md) the quote formula, [Trading regimes](trading-regimes.md) sessions and halts, [RFQ settlement](rfq.md) block flow, [Routing](routing.md) how the pieces compose, [Liquidity provision](liquidity-provision.md) the LP side, [Corporate actions](corporate-actions.md) events, and [Fees](fees.md) what the protocol charges.
