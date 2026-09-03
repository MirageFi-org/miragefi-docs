---
description: Where the venue's revenue comes from.
---

# Business model

MirageFi takes a small, itemised cut of the value it delivers: fees on fills plus a share of vault spread revenue. That is the whole list. There are no emissions, no payment for order flow, no market-making against users and no sale of data.

## Revenue lines

| Line | Rate (initial) | Scales with |
| --- | --- | --- |
| Vault swap fee | 2 bps of notional | Volume |
| RFQ settlement fee | 2 bps of notional | Block volume |
| Vault spread share | 10% of realised spread | Volume × spread, so it pays most exactly when LPs are earning most |

Each of the three is a `ParamController` value, timelocked, shown on every ticket and written into every fill event. See [Fees](../protocol/fees.md).

## The arithmetic

With 10M USDG in average daily volume and an average all-in half-spread of 12 bps: roughly 730K USDG a year from fees and roughly 438K from the spread share, about 1.2M USDG a year in total. Run the same arithmetic at 50M daily volume and the figure is about 5.8M a year. As a reference point, tokenized stocks did about $9B of on-chain volume across all venues in the first eight months of 2026; the model works with a modest share of a market that is growing several hundred percent a year.

## Why volume should come

* **Structural pricing edge on RWAs.** Anchored quotes neither drift nor hand LP value to arbitrageurs, which lets the venue hold tighter spreads than generic pools at the same LP return. On flow routed by aggregators, the better quote wins by itself.
* **The only 24/7 venue that prices sessions.** Weekend and overnight flow is real (the chain's tokens trade at all hours), and MirageFi serves it at disclosed premiums rather than turning it away or pricing it wrongly.
* **Distribution through the funnel.** Users of the chain self-custody Stock Tokens straight out of a broker app; the wallet directory, the aggregators and the API are the routes to reach them. See [Ecosystem integrations](../chain/ecosystem.md).
* **Infrastructure compounding.** Each wallet or agent that integrates the [API](../architecture/api.md) sends future flow with no acquisition cost.

## Costs

The audits and the bounty, oracle and data services, RPC and indexing, the per-attestation fees charged by the KYC provider, and the team. Fees accrue to the `FeeCollector` and pay for these before anything else; once the module takes over, the [governance](../transparency/governance.md) log shows how they are allocated.
