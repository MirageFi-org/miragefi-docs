---
description: What is live, what is capped, and the order in which the venue expands.
---

# What is live and what is next

The venue grows in phases, and each gate between phases is a published criterion rather than a date. Caps rise and features ship when the record justifies them. The dates below are targets; the gates are the commitments.

## Phase 0: testnet (complete)

The full protocol ran on Robinhood Chain testnet against Robinhood's testnet Stock Tokens and faucet: vaults, RFQ, router, eligibility, explorer. That testnet deployment remains as staging, and every release passes through it. See [Deployment](../architecture/deployment.md).

## Phase 1: guarded mainnet (live)

* Tier A markets: tokenized SPY, QQQ, AAPL, MSFT, NVDA, each against USDG.
* Anchor vaults on launch parameters, with per-market TVL and daily volume caps as set in the [listing framework](../assets/listing-framework.md).
* RFQ running with founding makers; trader, LP and maker attestations being issued.
* The trade explorer and the [execution quality](../transparency/execution-quality.md) dataset publishing from the very first fill.

**Gate to Phase 2:** two clean audits published, 60 days of execution-quality history with median `OPEN` fills inside tier targets, and zero band or withdrawal invariant events.

## Phase 2: catalogue and caps

* Tier B and C Stock Token markets added in batches as feeds and bytecode reviews are completed: the long tail is the point.
* Cap raises via the timelock, each one citing the record accumulated so far.
* LP onboarding opened beyond the founding group, jurisdiction by jurisdiction as the [compliance](../compliance/model.md) work lands.
* Portfolio surfaces finished: cost basis, share-term display, LP analytics, CSV export.

**Gate to Phase 3:** sustained two-sided vault performance across tiers through at least one high-volatility week and one long weekend, with no manual intervention.

## Phase 3: infrastructure

* The public [API and SDK](../architecture/api.md) leave beta; aggregator and wallet integrations; agentic order flow treated as a first-class integrator, given where the chain is heading.
* Cross-chain listings start, following the [asset roadmap](../assets/asset-roadmap.md): tokenized treasuries first, then gold, over CCIP, each passing the full listing checklist.
* Execution algorithms for working large orders over time (TWAP along the vault path).

## Phase 4: governance handover

Parameter control passes from the foundation multisig to the governance module, keeping the timelock, the published rationale and the [prohibitions](../transparency/governance.md) intact. What changes is who proposes, not what a proposal can do.

## What is deliberately not on this roadmap

Leverage, margin, perpetuals or lending. Adjacent products belong to other protocols; the venue's ambition is to be the place where RWAs trade properly, and every phase of the roadmap is spent on exactly that.
