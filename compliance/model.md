---
description: The principle behind MirageFi's compliance design, and how it is enforced.
---

# Compliance model

{% hint style="info" %}
This section explains how the protocol enforces compliance. It is not legal advice.
{% endhint %}

## Principle

The protocol is non-custodial. Because the assets carry no on-chain restrictions of their own, compliance is enforced at the **protocol boundary**.

Stock Tokens are plain ERC-20s. Robinhood applies eligibility at its own interface and at KYC'd issuance and redemption. On-chain, anyone can hold the tokens, so any venue that lists them has to decide for itself who is allowed to trade. MirageFi takes that decision at every entry point via the [eligibility registry](../architecture/eligibility.md), and it mirrors the issuer's jurisdiction list instead of inventing a looser one.

## What is enforced

| Boundary | Rule |
| --- | --- |
| Swapping | `TRADER` attestation: KYC'd and in a non-restricted jurisdiction, mirroring the Stock Token eligibility list |
| Providing liquidity | `LP` attestation; limited to professional clients at launch |
| Making markets | `MAKER` attestation; limited to professional trading firms |
| Vault share transfers | The recipient must hold an `LP` attestation |
| Front-end access | Restricted jurisdictions are geo-fenced |

Withdrawal is never gated. An LP's exit comes with holding shares and is not a permission, because a compliance system able to trap funds has turned into a custodian.

## How it is enforced

1. **Attestations, not allowlists.** After identity, sanctions and residency checks, a KYC provider issues an EAS attestation to the wallet. It carries role, jurisdiction class and expiry, and nothing personal.
2. **Continuous re-screening.** Attestations expire and are renewed. Revocation takes effect immediately and applies at the next action.
3. **Two screening layers.** The KYC provider screens at attestation and at renewal, and the sequencer runs its own sanctions screening (TRM Labs is integrated on the chain).
4. **Modular policy.** Because the registry is an adapter, both the policy engine (EAS, ONCHAINID, Chainlink ACE) and the set of accepted issuers can be changed through the timelock without redeploying the core. If a regulator or the issuer demands it, rules can be tightened quickly.
5. **Permissionless reads.** All view functions remain open, so explorers and aggregators can index the protocol.

## What the protocol does not do

* It holds no user funds outside atomic settlement and the LP-owned vaults.
* It performs no fiat conversion.
* It exercises no discretion over any individual fill. A quote is a formula over public state, settlement is a signature check, and no one at MirageFi is able to improve, worsen or block a particular trade.

It is these properties that keep the venue's regulatory posture narrow: the operating company is the party that faces users through the front-end and is assessed for authorisation where that is required, while the protocol stays non-custodial, non-discretionary software.

## Privacy

Personal data stays with the KYC provider and never reaches the chain. Through the optional zero-knowledge credential path, a wallet can prove it is eligible without disclosing which provider ran the check or any underlying attribute.

## Relationship to the issuer's rules

Trader eligibility on MirageFi mirrors the Stock Token eligibility list. Should Robinhood tighten that list, the attestation issuer's rules follow suit. Should Stock Tokens later acquire on-chain transfer hooks (ERC-7943 or ERC-3643), the vaults and settlement contracts will honour them and will be allowlisted themselves. See [Asset roadmap](../assets/asset-roadmap.md).
