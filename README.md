---
description: Trade tokenized real-world assets on Robinhood Chain, where every quote, fill and fee settles on-chain and can be inspected by anyone.
---

# What is MirageFi

MirageFi is a swap venue for tokenized real-world assets on Robinhood Chain, and it holds no custody of user funds. Traders exchange Stock Tokens (tokenized AAPL, NVDA, SPY and others), and in time tokenized treasuries and gold, for USDG at prices tied to the same Chainlink feeds that price those assets everywhere else. Each quote, each fill and each fee settles on Robinhood Chain, open to inspection by anyone.

## The idea in one paragraph

A generic AMM derives its price from the reserves it holds. For assets whose price is formed on-chain that is the correct design; for assets whose authoritative price is formed on an exchange it is the wrong one. MirageFi quotes in the opposite direction. Each market is an [anchor vault](protocol/anchor-vaults.md) that prices every swap at the Chainlink mid plus a spread which depends on the market session, the vault's inventory and the asset's tier. While the underlying market is closed, spreads widen, trade sizes shrink, and the ticket states this. Trades larger than the vault's clip are routed to professional market makers through [RFQ](protocol/rfq.md), signed off-chain and settled atomically on-chain. For each trade the [router](protocol/routing.md) selects the better price, and the trader receives a single quote with the mid, the spread and the fee shown as separate figures.

## What makes it different

| Property           | Generic AMM pool                                | MirageFi                                                              |
| ------------------ | ----------------------------------------------- | -------------------------------------------------------------------- |
| Price discovery    | Ratio of reserves                               | Tied to the Chainlink mid, with guards as bounds                     |
| Closed markets     | Treats a Saturday quote like a Tuesday one      | Spreads widen and clips shrink, and the ticket says so               |
| Corporate actions  | The pool breaks or must be migrated             | Handled by the ERC-8056 multiplier in the price; trading halts for the event |
| LP economics       | Adversely selected on each reference-price move | Earns spread income from quotes banded around the oracle             |
| Large trades       | Slippage along the curve                        | Attested market makers compete through RFQ                           |
| Compliance         | None                                            | Eligibility checked at the protocol boundary                         |
| Fees               | Buried in the curve                             | Itemised on each quote and published on-chain                        |

For crypto-native pairs, constant-product pools are the right instrument. For assets that have an authoritative external price, set trading hours and corporate actions, they give value away to arbitrageurs and wander from fair value during thin weekends. Such assets need RWA-native market structure, and MirageFi fills that gap.

## Key facts

| | |
| --- | --- |
| Chain | Robinhood Chain (Arbitrum Nitro L2, chain ID 4663) |
| Quote asset | USDG (Paxos Global Dollar) |
| Launch assets | Robinhood Stock Tokens |
| Next assets | Long-tail Stock Tokens, followed by tokenized treasuries and gold (see the [Asset roadmap](assets/asset-roadmap.md)) |
| Execution | Oracle-anchored vaults for standard size, RFQ for block size, a single router |
| Pricing | Chainlink Data Feeds and Data Streams, aware of the session, guarded |
| Custody | None. Funds move at settlement only; immutable contracts hold vault inventory |
| Fees | One itemised protocol fee; no hidden share of the spread |

## What MirageFi does not do

* Take custody of user assets or convert fiat.
* Issue, mint or redeem RWAs. MirageFi is secondary-market infrastructure built over assets that regulated issuers issue.
* Provide margin, leverage or perpetuals.

## Where to go next

* If the venue is new to you: begin with [Why now](introduction/why-now.md) and [Design principles](introduction/design-principles.md).
* If you want the mechanics: read the [Protocol overview](protocol/overview.md), followed by [Anchor vaults](protocol/anchor-vaults.md), [Pricing and spreads](protocol/pricing-and-spreads.md) and [Trading regimes](protocol/trading-regimes.md).
* If you are building on MirageFi or integrating with it: see [System overview](architecture/overview.md), [Smart contracts](architecture/smart-contracts.md) and [API and SDK](architecture/api.md).
* If you are assessing risk: see the [Risk framework](risk/framework.md) and [Lessons from RWA trading](risk/lessons.md).
* If you are trading or providing liquidity: see [For traders](users/traders.md) and [For liquidity providers](users/liquidity-providers.md).
