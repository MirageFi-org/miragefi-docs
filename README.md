---
description: Trade tokenized real-world assets on Robinhood Chain, where every quote, fill and fee settles on-chain and can be inspected by anyone.
---

# What is MirageFi

MirageFi is a swap venue for tokenized real-world assets on Robinhood Chain, and it holds no custody of user funds. Traders exchange Stock Tokens (tokenized AAPL, NVDA, SPY and others), and in time tokenized treasuries and gold, for USDG at prices tied to the same Chainlink feeds that price those assets everywhere else. Each quote, each fill and each fee settles on Robinhood Chain, open to inspection by anyone.

## The idea in one paragraph

A generic AMM derives its price from the reserves it holds. For assets whose price is formed on-chain that is the correct design; for assets whose authoritative price is formed on an exchange it is the wrong one. MirageFi quotes in the opposite direction. Each market is an [anchor vault](protocol/anchor-vaults.md) that prices every swap at the Chainlink mid plus a spread which depends on the market session, the vault's inventory and the asset's tier. While the underlying market is closed, spreads widen, trade sizes shrink, and the ticket states this. Trades larger than the vault's clip are routed to professional market makers through [RFQ](protocol/rfq.md), signed off-chain and settled atomically on-chain. For each trade the [router](protocol/routing.md) selects the better price, and the trader receives a single quote with the mid, the spread and the fee shown as separate figures.
