---
description: The legal structure that sits behind the protocol, and the company that runs the platform.
---

# Corporate structure

## Two entities, one protocol

| Entity | Role |
| --- | --- |
| **MirageFi Foundation** | Holds the immutable contracts and protocol governance. Controls the timelocked parameter multisig and the guardian until the governance module takes them over. |
| **MirageFi Labs** (operating company) | Builds and operates the platform, the quote service, the indexer and the keeper software. Employs the team. Holds the licence wherever one is required. |

## Why they are separate

* The contracts are immutable and non-custodial. Housing them in a foundation keeps protocol governance apart from the commercial incentives of the company building around it.
* The operating company is the one that faces users through a front-end and holds licences where required. Keeping it distinct narrows the regulatory surface of the protocol itself.
* Were the operating company to cease, the venue would carry on: vaults quote from on-chain state, withdrawals are unconditional, and the open-source code lets anyone run the quote service, keeper, indexer and front-end. Convenience and the pace of new listings would suffer; the market would not.

## Governance path

1. **Today:** parameter changes pass through a timelocked multisig held by the foundation, and each proposal is published with a rationale.
2. **Next:** parameter control passes to a governance module. The properties that matter are preserved: the timelock, the public rationale, no access to funds, no power to gate withdrawals, and no power to touch an individual fill.
