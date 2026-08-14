---
description: The properties of Robinhood Chain that make it the right place for MirageFi to live.
---

# Why Robinhood Chain

The decisive point is a simple one: the RWAs on Robinhood Chain are plain, self-custodied ERC-20s backed by a regulated issuer and priced by Chainlink feeds. An oracle-anchored swap venue needs exactly that combination, and no other chain offers it today together with a broker's own distribution.

## Property by property

The facts below come from Robinhood's documentation and the Arbitrum Foundation.

| Property | Robinhood Chain | Why it matters to MirageFi |
| --- | --- | --- |
| Stack | Dedicated Arbitrum Nitro chain (Orbit) in rollup mode, settling to Ethereum with blob data availability; 7-day canonical withdrawal | Ethereum security and standard Arbitrum tooling |
| Status | On mainnet since 1 July 2026 (chain ID 4663); roughly $540M TVL by mid-August 2026 (sources put the figure anywhere from $536M to $1.4B) | Live, with real assets and real users |
| Deployment | Permissionless and EVM-equivalent; Solidity and Vyper, Foundry and Hardhat; Stylus (Rust and WASM) available | Venue contracts face no gatekeeping |
| Block time and fees | Blocks of about 250 ms and 100 ms latency via preconfirmations; median transaction roughly $0.001; gas denominated in ETH | A quote a few seconds old is still fresh; sub-cent settlement makes small swaps economic |
| Sequencer | A single sequencer run by Robinhood, first-come-first-served, with sanctions screening at the sequencer; forced inclusion via the L1 delayed inbox after 24 hours | Design around a 24-hour outage; Chainlink Sequencer Uptime Feed available |
| Native RWAs | **Stock Tokens**: ERC-20, 18 decimals, issued by Robinhood Assets (Jersey) Ltd, backed 1:1 by shares in US custody, available in 120+ countries | The tradeable universe, issued by a listed broker |
| Corporate actions | ERC-8056 `uiMultiplier()`: dividends are reinvested and splits are expressed as a shares-per-token ratio | Nothing to administer for dividends; no ex-dividend cliffs to misprice |
| Oracles | Chainlink Data Feeds (24/5), Data Streams (v11 RWA schema with `marketStatus`), CCIP, Sequencer Uptime Feed; the multiplier is baked into the price | Production-grade anchor pricing and session awareness from day one |
| Stablecoins | USDG (Paxos, MiCA-regulated) is native and already serves as the chain's lending dollar on Morpho; USDe is present; USDC via CCTP is *unconfirmed* | The quote asset |
| Account abstraction | ERC-4337 EntryPoints v0.6 to v0.8, EIP-7702 (ArbOS 40); Alchemy Gas Manager, ZeroDev | Gasless onboarding; approve and swap in a single user operation |
| Incumbent DeFi | Morpho Blue, Uniswap v2, v3 and v4, Lighter, Arcus, 1inch | Arbitrage participants and aggregator flow are already on-chain |
| Compliance layer | **None on-chain** for Stock Tokens: neither allowlist nor transfer hook; eligibility is applied in Robinhood's UI and at KYC'd primary issuance and redemption | Eligibility is enforced by MirageFi itself at the protocol boundary |
| Ecosystem economics | Robinhood remits 10% of chain net revenue to the Arbitrum DAO; $1M sponsorship of Arbitrum Open House 2026 | Builders can access grants and audit subsidies |

## What no other chain offers today

* On Solana, tokenized stocks (xStocks, distributed by Kraken) trade mainly on centralised books and generic AMMs, and the issuers are third parties rather than the broker that has the users.
* On Base, tokenized stocks (Coinbase, since 24 August 2026) are more recent, and no venue layer has yet been built around them.
* Ethereum mainnet has the deepest DeFi, but it has no native broker-issued equities, and its gas costs make small RWA swaps uneconomic.

Robinhood Chain brings the assets, the pricing, the quote asset, the account abstraction and the fee profile together in one place, and adds the distribution of the broker that issues the assets.

## What the chain does not do for us

Naming the gaps clearly is part of the case:

* It does not enforce Stock Token eligibility on-chain. MirageFi does, via the [eligibility registry](../architecture/eligibility.md).
* It offers no protection against a sequencer outage. MirageFi does, via the recovery grace and the delayed inbox path. See [Sequencer and chain risk](../risk/sequencer-and-chain.md).
* It does not price closed-market gaps. MirageFi does, via the regime engine. See [Trading regimes](../protocol/trading-regimes.md).
* Its incumbent DEXs price RWAs the same as any other token. That gap is why the venue exists. See [Why now](../introduction/why-now.md).
