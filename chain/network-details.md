---
description: Reference information for Robinhood Chain.
---

# Network details

{% hint style="info" %}
Robinhood publishes RPC endpoints, explorer URLs and its own contract addresses at [docs.robinhood.com/chain](https://docs.robinhood.com/chain/). The MirageFi contract addresses live in `contracts/deployments/4663.json` and are described on the [Deployment](../architecture/deployment.md) page. Verify every address against those sources before sending funds.
{% endhint %}

## Network

| Network         | Chain ID | Status                    | MirageFi               |
| --------------- | -------- | ------------------------- | ---------------------- |
| Robinhood Chain | 4663     | Mainnet since 1 July 2026 | Production deployment  |

## Chain parameters

| Parameter                | Value                                                                                  |
| ------------------------ | -------------------------------------------------------------------------------------- |
| Execution                | Arbitrum Nitro, EVM-equivalent, ArbOS 40 (EIP-7702 supported)                          |
| Settlement layer         | Ethereum                                                                               |
| Data availability        | Ethereum blobs (rollup mode)                                                           |
| Block time               | About 250 ms                                                                           |
| Preconfirmation latency  | About 100 ms                                                                           |
| Gas token                | ETH                                                                                    |
| Median transaction cost  | About $0.001                                                                           |
| Canonical withdrawal     | 7 days                                                                                 |
| Sequencer                | One sequencer, run by Robinhood, with sanctions screening applied there                |
| Forced inclusion         | L1 delayed inbox, 24-hour delay                                                        |
| Explorer                 | Blockscout                                                                             |

## Precompiles and conventions used by MirageFi

| Item                      | Usage                                                                                   |
| ------------------------- | --------------------------------------------------------------------------------------- |
| `block.timestamp`         | Every piece of time-based logic: quote expiries, staleness bounds, grace periods and the timelock |
| `block.number`            | Never relied on for time, since on Arbitrum it reflects the L1 block                    |
| `ArbSys.arbBlockNumber()` | Used only where an L2 block height is needed, for instance in event metadata            |
| Compressed calldata       | Because L1 data cost dominates fees, RFQ quote structs are packed and signatures are compacted |

## Standards in use

| Standard   | Where                                                                                   |
| ---------- | --------------------------------------------------------------------------------------- |
| ERC-20     | Stock Tokens (18 decimals) and USDG (6 decimals)                                        |
| ERC-8056   | Corporate actions on Stock Tokens, via `uiMultiplier()`                                 |
| ERC-4626   | Share accounting in the vaults (extended for dual assets and in-kind withdrawal)        |
| EIP-712    | Signing of RFQ quotes                                                                   |
| EIP-1271   | Validating maker quote signatures from smart accounts                                   |
| ERC-4337   | Account abstraction entry points, v0.6 to v0.8                                          |
| EIP-7702   | Delegation of an EOA to smart-account code                                              |
| Permit2    | Allowance-based pulls at settlement, for takers and makers alike                        |
| EAS        | Attestations of eligibility                                                             |

## Chainlink services on chain 4663

| Service                    | Purpose in MirageFi                                    |
| -------------------------- | ------------------------------------------------------ |
| Data Feeds (24/5 equities) | The mid used for vault quoting and band checks         |
| Data Streams (v11 RWA)     | Classifying the session and cross-checking fills       |
| Sequencer Uptime Feed      | The trading grace after an outage                      |
| CCIP                       | Bridged treasuries and gold (on the roadmap)           |
| ACE / CCID                 | An alternative eligibility adapter                     |
