---
description: The eight principles against which every MirageFi design decision is tested.
---

# Design principles

## 1. The reference price is the truth

The authoritative price of an RWA is formed on the venue where the underlying trades, and MirageFi never pretends otherwise. Each quote is built outward from the Chainlink mid, each fill is bounded against it, and [execution quality](../transparency/execution-quality.md) is measured against it and published. The protocol does not exist to discover the price of Apple; it exists to deliver that price on-chain, at any hour, with the cost of doing so stated plainly.

## 2. Honest quotes

The mid, the spread and the protocol fee appear on a quote as three separate, labelled numbers. The session badge is on the ticket rather than in a tooltip: while US markets are closed, the trader reads "closed session, spreads widened" before signing, never afterwards. There is no payment for order flow, no hidden markup and no fee buried in the curve. Economics that cannot survive being itemised are the wrong economics for the venue.

## 3. Non-custodial, always

User assets are never held by MirageFi. Traders keep custody themselves and sign every swap; vault inventory belongs to LPs and sits in immutable contracts; RFQ settlement moves assets straight from maker to taker. The emergency pause can halt new swaps, but it can never reach funds or prevent an LP from withdrawing.

## 4. Compliance at the protocol boundary

Because Stock Tokens carry no on-chain transfer restrictions, any venue that lists them has to decide for itself who may trade. MirageFi enforces eligibility at every entry point using attestations, keeps personal data off-chain, and tells an ineligible user why before they sign instead of reverting afterwards. See the [Compliance model](../compliance/model.md).

## 5. Sessions are first-class

At 3pm on Tuesday and at 3am on Sunday an equity token is a different asset, and the protocol prices that difference rather than ignoring it. Spreads, clip sizes and halts are all determined by the [trading regime](../protocol/trading-regimes.md), which is driven by the oracle's market-status field and never by guesswork.

## 6. Isolation per market

Every listed asset has its own vault, with its own inventory, its own parameters and its own LPs. A dislocation, halt or delisting in one market cannot reach another. There are no shared pools and no socialised losses.

## 7. Everything is inspectable

Each fill links to its transaction on Blockscout. Each parameter can be read from a public contract. Each parameter change passes through a timelock with a published rationale. The [trade explorer](../users/trade-explorer.md) exists so that "transparently on-chain" is a claim anyone can verify, not a slogan.

## 8. Design for the chain's failure modes

A single sequencer can fail while the underlying market continues to move. Oracles can go stale. Corporate actions pause feeds. Every one of these has a designed response (halt, widen, grace period), documented in the [Risk](../risk/framework.md) section, because a venue that works only when everything else works is not infrastructure.

## What these principles rule out

* No liquidity funded by emissions. Liquidity is paid for out of real spread revenue or not at all.
* No leverage, margin or liquidations. The venue does a single thing.
* No discretionary intervention in pricing. Parameters change through the timelock, and quotes come from the formula.
