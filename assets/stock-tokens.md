---
description: An overview of Robinhood Stock Tokens and the properties that matter once they are traded.
---

# Stock Tokens

MirageFi launches with Robinhood Stock Tokens. They are why the venue lives on Robinhood Chain and nowhere else: equities tokenized by a listed broker, backed 1:1, tradeable 24/7 as ordinary ERC-20s, and priced through Chainlink.

## What they are

| Property          | Detail                                                                                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Issuer            | Robinhood Assets (Jersey) Ltd                                                                                                                                |
| Legal nature      | Tokenized **debt securities**. A token is a claim against the issuer that tracks the underlying share; it is not the share itself.                            |
| Backing           | 1:1 by shares held in US custody                                                                                                                             |
| Standard          | ERC-20, 18 decimals, with the ERC-8056 corporate-action interface on top                                                                                     |
| Availability      | 120+ countries.                                                                                                                                               |
| Transfer controls | **None on-chain.** The documentation shows no allowlist, no transfer hook and no on-chain freeze function. Eligibility is applied in Robinhood's UI and at KYC'd primary issuance and redemption. |
| Pricing           | Chainlink Data Feeds (24/5) and Data Streams carrying market status; the ERC-8056 multiplier is already included in the price                                |

## What the properties mean for trading

* Because they are **plain ERC-20s**, the vaults and RFQ settlement can hold and move them without any issuer allowlisting, and an attested wallet trades without per-token ceremony.
* Because **ERC-8056 handles dividends and splits**, value accrues into the token and its feed. There are no dividend claims to administer and no ex-dividend cliffs to misprice. See [Corporate actions and dividends](../protocol/corporate-actions.md).
* **Chainlink pricing with market status** is the precondition for anchor quoting and for [trading regimes](../protocol/trading-regimes.md): the mid and the session come from one guarded source.
* **No on-chain compliance** has two sides. The tokens can sit in any address, including ones that would fail Robinhood's own screen. That is exactly why MirageFi applies eligibility itself at the protocol boundary, through the [eligibility registry](../architecture/eligibility.md). Every trader, LP and maker is checked on every relevant action.

Should Stock Tokens adopt ERC-7943 (uRWA) or ERC-3643 hooks in future, the vaults and settlement contracts already call `canTransfer` and `canReceive` defensively, and they will then need allowlisting by the issuer. See [Asset roadmap](asset-roadmap.md).

## Issuer and freeze risk

Every Stock Token position carries two risks. The venue discloses and prices both instead of ignoring them:

* **Issuer credit risk.** The token is a debt claim on Robinhood Assets (Jersey) Ltd. Should the issuer fail, what the token is worth depends on recovery from custody rather than on the share price. This claim is carried by vault LPs and by traders holding tokens alike.
* **Freeze rights.** Under the issuer's terms, tokens may be suspended, frozen or restricted in certain circumstances. No on-chain freeze function appears in the published documentation. Before any listed token's market opens, its deployed bytecode is reviewed for pause, freeze, blacklist and forced-transfer roles, and the result is published on the listing page. Where such a role is found, the tier assignment and caps reflect it.

See [Issuer risk](../risk/issuer.md).

## What is listed

Tokenized SPY, QQQ, AAPL, MSFT and NVDA make up Tier A. The complete tier assignment, together with the criteria a token has to satisfy before its market opens, is set out in the [Listing framework](listing-framework.md).
