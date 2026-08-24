---
description: Where the contracts are deployed, how deployments are verified, and how addresses get published.
---

# Deployment

{% hint style="warning" %}
The only places MirageFi contract addresses are published are `contracts/deployments/4663.json` in the open-source repository, the registry linked from this page, and [miragefi.org](https://miragefi.org). Treat any address circulating elsewhere as hostile. Verify before you approve anything.
{% endhint %}

## Deployments

| Network | Chain ID | Status |
| --- | --- | --- |
| Robinhood Chain mainnet | 4663 | Production |
| Robinhood Chain testnet | Published in the repository | Persistent staging; each release goes here first |

The testnet deployment mirrors mainnet parameters and runs against Robinhood's testnet Stock Tokens and faucet assets, which lets integrators exercise the complete path (attestation, quote, swap, withdrawal) with no value at risk.

## The deployment record

For every contract, `contracts/deployments/4663.json` records the address, source commit, compiler settings, constructor arguments, and the deployment transaction. Every contract is verified on Blockscout, and on each release a CI job asserts that the on-chain bytecode matches the tagged source. That turns "the audited code is the deployed code" into a claim anyone can check rather than a promise.

## Release process

1. Tag, audit or review the delta, and publish the source.
2. Deploy to testnet; run the invariant suite plus a scripted end-to-end pass against the live testnet oracles.
3. Deploy to mainnet using `CREATE2` so addresses stay stable across environments.
4. Set parameters through the timelock (never in constructors, so the governance log is complete from block one).
5. Update the deployment record and the verification, then announce.

Since logic is immutable, a post-launch "release" is one of two things: a new market from the factory (parameters only, no new code), or a new protocol version deployed beside the old one, with LPs migrating when they choose, as [Smart contracts](smart-contracts.md) describes. Old versions are never remotely switched off; there is nothing there to switch.

## Configuration authenticity

Everything a client needs (addresses, the market list, tiers, oracle adapters) can be read on-chain from `ParamController` and the factory. The SDK carries no baked-in market list; it reads the chain instead, so a stale or tampered client configuration cannot direct users to the wrong markets without failing the signature and address checks.
