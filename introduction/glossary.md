---
description: The terms used across this documentation, each defined once.
---

# Glossary

| Term | Definition |
| --- | --- |
| **Anchor vault** | A contract, one per asset, that holds Stock Token and USDG inventory and quotes both sides of the market around the oracle mid. See [Anchor vaults](../protocol/anchor-vaults.md). |
| **Attestation** | A record on the Ethereum Attestation Service that ties a wallet to a role (trader, LP, maker) once off-chain verification is complete. Contains no personal data. See [Eligibility registry](../architecture/eligibility.md). |
| **Band** | The furthest distance from the oracle mid at which a fill may clear. Any quote that falls outside the band reverts. |
| **Clip** | The largest size a single swap against a vault may have. Beyond the clip, the router quotes through RFQ. |
| **Data Feeds** | The push-based on-chain price feeds from Chainlink; their 24/5 equity feeds price Stock Tokens throughout the week. |
| **Data Streams** | The pull-based low-latency price service from Chainlink (v11 RWA schema). Its reports include a `marketStatus` field, which is used to classify the session. |
| **ERC-8056 multiplier** | The `uiMultiplier()` value a Stock Token reports, giving underlying shares per token. Splits and dividends are expressed through it, and it is already reflected in Chainlink prices. |
| **Execution quality** | The signed gap, in basis points, between a fill price and the oracle mid at inclusion. Published for each fill. See [Execution quality](../transparency/execution-quality.md). |
| **Half-spread** | The gap between the mid and one side of a quote. MirageFi states its parameters as half-spreads in basis points. |
| **Inventory skew** | The extent to which a vault's holdings have moved away from its target mix. Skew shifts quotes so as to attract flow that rebalances the vault. |
| **Maker** | An attested market maker that signs RFQ quotes off-chain and settles them on-chain atomically. |
| **Mid** | The current guarded oracle price for an asset, as supplied by `OracleRouter`. Every quote begins from it. |
| **Quote asset** | USDG, the asset against which every market trades and in which every fee is denominated. |
| **Regime** | The state a market occupies: `OPEN`, `EXTENDED`, `CLOSED` or `HALTED`. It sets spread multipliers and clip sizes. See [Trading regimes](../protocol/trading-regimes.md). |
| **RFQ** | Request for quote. EIP-712 quotes signed by makers, valid for a matter of seconds, which the taker settles atomically. See [RFQ settlement](../protocol/rfq.md). |
| **Router** | The single point of entry (`SwapRouter`); it checks eligibility, compares the vault price with the RFQ price and settles whichever is better. |
| **Session** | The state of the underlying market (regular, extended hours, closed) as the oracle reports it. Sessions are what regimes are derived from. |
| **Stock Token** | A tokenized equity from Robinhood Assets (Jersey) Ltd: an ERC-20 debt security backed 1:1 by shares held in US custody. See [Stock Tokens](../assets/stock-tokens.md). |
| **Tier** | The listing class of an asset (A, B or C). It determines the asset's base spread, clip, band and caps. See [Listing framework](../assets/listing-framework.md). |
| **USDG** | The Paxos Global Dollar, a stablecoin regulated under MiCA and native to Robinhood Chain. See [Quote asset: USDG](../assets/usdg.md). |
| **Vault share** | The ERC-4626-style token issued to an LP on deposit. It represents a pro-rata claim on the vault's inventory and the spread revenue it has accrued. |
