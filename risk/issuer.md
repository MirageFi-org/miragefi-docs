---
description: The counterparty risk inside every tokenized security, and how the venue accounts for it.
---

# Issuer risk

A Stock Token is not a share. It is a tokenized debt security, issued by Robinhood Assets (Jersey) Ltd, that tracks a share held in US custody. Every position traded or held on MirageFi carries that distinction inside it, and the venue treats it as a first-class risk.

## What the risk is

| Component | Description |
| --- | --- |
| Credit risk | Were the issuer to become insolvent, token holders would be creditors with a claim on the custodied shares. Recovery could be partial or delayed. |
| Freeze and restriction | Under the issuer's terms, the right to suspend, freeze or restrict tokens in certain circumstances is reserved. If it were exercised, vault inventory and trader holdings could become untransferable. |
| Redemption terms | Primary redemption at the issuer is KYC-gated. The venue never depends on redemption; it depends on secondary-market pricing through the oracle. |
| Regulatory action | A regulator could compel the issuer to change its terms, restrict jurisdictions or halt issuance. |

## How the venue accounts for it

### Disclosure before exposure

Every market's listing page names the issuer, states the legal nature of the token and reports the bytecode review finding. Before a trader's first swap and before an LP's first deposit, both pass through a plain-language disclosure of the debt-claim structure. No venue can engineer issuer risk away; what it can do is make the risk impossible to hold without knowing.

### Bytecode review before a market opens

According to the published documentation, Stock Tokens carry no on-chain freeze function. That alone is not sufficient. Before any listed token's market opens, its deployed bytecode is reviewed for pause, freeze, blacklist or forced-transfer roles, and the finding goes on the listing page. Where such a role exists, the tier assignment and the caps reflect it.

### Proof of reserve

If a Chainlink Proof-of-Reserve feed, or an equivalent attestation, exists for the issuer's custodied shares, `OracleRouter` reads it and the listing page shows it beside the market. Markets that lack such a feed say so explicitly.

### Caps and a single-issuer number

Each exposure is bounded by per-market TVL and volume caps. Since every Stock Token shares one issuer, the [trade explorer](../users/trade-explorer.md) also publishes total vault inventory exposed to Robinhood Assets (Jersey) Ltd as one number, so that the concentration is a stated fact rather than something to be discovered.

### Isolation

Markets share nothing, so an issuer event on one token, or on every Stock Token, cannot touch a market whose asset is a bridged treasury or gold token. Widening the issuer set is a stated goal of the [Asset roadmap](../assets/asset-roadmap.md).

## What LPs and traders should know

To hold a Stock Token, whether in a wallet or through a vault share, is to hold a claim on a regulated broker's custody arrangement, not the share itself. If the issuer fails, the venue's contracts carry on working (in-kind withdrawals always function), but what comes out is the claim, and its value becomes a legal question rather than a market one. The caps and disclosures represent the venue's view of how to bound that; participants may choose to be more conservative.
