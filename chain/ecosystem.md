---
description: The protocols and services that MirageFi integrates with on Robinhood Chain.
---

# Ecosystem integrations

Rather than rebuilding what Robinhood Chain already has, MirageFi is built to interoperate with it. Every integration below sits either in the settlement path or among the surrounding services.

## Chainlink

**Role:** pricing, market status, sequencer status, and a candidate policy engine for eligibility.

24/5 equity pricing comes from Data Feeds and Data Streams, with the ERC-8056 multiplier already applied, together with the `marketStatus` field on which the regime engine runs. The post-outage grace is driven by the Sequencer Uptime Feed. CCIP is the bridge route for the treasury and gold listings on the [Asset roadmap](../assets/asset-roadmap.md). Once generally available on chain 4663, Chainlink ACE and CCID are a candidate adapter for the eligibility registry. See [Oracles and market sessions](../risk/oracles.md).

## Paxos and USDG

**Role:** quote asset.

USDG is native to the chain, regulated under MiCA, and already the dollar of the chain's lending markets, which means traders arrive already holding it. The go-to-market plan includes co-marketing with Paxos. See [Quote asset: USDG](../assets/usdg.md).

## Uniswap, 1inch and aggregators

**Role:** arbitrage flow and distribution through aggregators.

Uniswap v2, v3 and v4 and 1inch are all deployed on the chain. Arbitrageurs who keep generic pools aligned with MirageFi's anchored quotes are welcome flow, and skew pricing rewards them for rebalancing vaults. Integrating with aggregators through the public router interface is a distribution goal: whenever its anchored quote beats a curve, MirageFi captures the routed flow.

## Morpho Blue

**Role:** the chain's lending layer and a source of natural flow.

The chain's USDG lending markets run on Morpho (roughly $80M to $90M of RWA deposits, sitting behind Robinhood Earn). Lenders and borrowers shifting between yield positions and Stock Token exposure are precisely the rotation flow that a swap venue serves.

## Account abstraction providers

**Role:** gasless onboarding and batched transactions.

ERC-4337 entry points and EIP-7702 are both live. For embedded accounts and gas sponsorship the front-end uses Alchemy Account Kit, with ZeroDev as the alternative. A first swap takes one user operation: approve via Permit2 and swap, with the gas sponsored. Smart accounts sign RFQ quotes via EIP-1271.

## Robinhood Wallet

**Role:** primary trader wallet.

The front-end deep-links into Robinhood Wallet, and a listing in the wallet's dApp directory is a distribution target: MirageFi's first audience is the set of users who self-custody Stock Tokens outside the Robinhood app.

## Blockscout

**Role:** transaction transparency.

In the trade explorer, every fill, parameter change and fee accrual links to its transaction on Blockscout.

## TRM Labs

**Role:** sanctions and AML screening.

TRM is integrated at the sequencer level of the chain. The KYC provider that issues MirageFi eligibility attestations screens independently, at attestation time and again on renewal, so there are two separate layers.

## Indexing

The trade explorer, the execution-quality dashboards and the keeper service are powered by Ponder (self-hosted, TypeScript) or Envio HyperIndex running over chain 4663. See [Off-chain services](../architecture/off-chain-services.md).

## Arbitrum ecosystem

Robinhood Chain is an Arbitrum Orbit chain. MirageFi intends to apply for audit subsidies from the Arbitrum Foundation and to take part in the buildathon track of Arbitrum Open House 2026 and in Chainlink BUILD.
