---
description: How the venue decides who may trade, provide liquidity and make markets, while keeping personal data off-chain.
---

# Eligibility registry

Stock Tokens have no transfer restrictions on-chain. Robinhood enforces eligibility at its own interface and at KYC'd issuance and redemption. Any venue listing those tokens must therefore enforce eligibility on its own, and MirageFi does this at the **protocol boundary**: each swap, each deposit, each RFQ settlement and each share transfer is checked.

## Roles

| Role | Who | Status |
| --- | --- | --- |
| `TRADER` | KYC'd individuals and entities in non-restricted jurisdictions, matching the Stock Token eligibility list | Live |
| `LP` | Liquidity providers; at launch these are professional clients, because of the inventory exposure vaults carry | Live |
| `MAKER` | Professional market makers admitted to RFQ | Live |
| `RELAYER` | Services submitting swaps on a user's behalf (account-abstraction bundlers, integrators) | Live |

## Attestations

Eligibility rests on **Ethereum Attestation Service** attestations, compatible with ONCHAINID, which the KYC provider issues once identity, sanctions and residency checks are complete.

Each attestation holds:

* the wallet address,
* the role,
* a jurisdiction class (the country itself only if the user chooses to disclose it),
* an investor class where relevant,
* an expiry.

No name, no document and no personal identifier is included. Attestations expire and are renewed through continuous re-screening; a revocation takes effect at once.

### Zero-knowledge path

An optional credential path (Privado ID or zkPass) allows a wallet to prove "eligible in jurisdiction class X, investor class Y" without disclosing which provider performed the check or any underlying attribute. Either form is accepted by the registry.

## The check

```solidity
function isEligible(address account, bytes32 role) external view returns (bool);
```

`SwapRouter`, `AnchorVault` and `RfqSettlement` invoke this for each relevant party. When the check fails the call reverts; the front-end resolves the same view before anything is signed, so an ineligible user learns the reason up front instead of spending gas to discover it. View functions remain permissionless so indexers, explorers and aggregators can read everything.

### Where the check runs

| Action | Checked parties |
| --- | --- |
| `swap` | Trader (`TRADER`); the recipient too, if different |
| RFQ `settle` | Taker (`TRADER`), maker (`MAKER`) |
| Vault `deposit` | Depositor (`LP`) |
| Vault `withdraw` | Gated by nothing beyond share ownership: withdrawal is a property, not a permission |
| Vault share transfer | Recipient (`LP`) |
| Relayed submission | Relayer (`RELAYER`), together with the underlying user's own role |

## Adapters

The registry exposes an adapter interface, so the policy engine can be swapped without touching the core:

* **EAS adapter** (launch): reads attestations by schema and issuer.
* **ONCHAINID adapter**: for identities compatible with ERC-3643.
* **Chainlink ACE / CCID adapter**: an alternative policy engine, enabled through the timelock if needed.

The set of accepted issuers and schemas is a `ParamController` value, which makes tightening or replacing a provider a timelocked, logged change instead of a redeploy.

## Permissioned assets

Should Stock Tokens or bridged RWAs adopt ERC-7943 (uRWA) or ERC-3643 hooks, the vaults and `RfqSettlement` call `canTransfer` and `canReceive` defensively and are allowlisted by the issuer themselves. Both the token's own rules and the venue's rules are satisfied, with neither side having to trust the other. See [Asset roadmap](../assets/asset-roadmap.md).

## Geo-fencing

Restricted jurisdictions are excluded at several layers:

1. Wallets in restricted jurisdictions are not issued attestations.
2. Every wallet must provide a jurisdiction self-attestation at onboarding.
3. IP-based geo-fencing for restricted jurisdictions is applied by the front-end.
4. The sequencer runs its own sanctions screening.

The restricted list mirrors the availability list of the Stock Token issuer and is maintained by the attestation issuer; see the [Compliance model](../compliance/model.md).
