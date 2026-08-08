---
description: The asset classes MirageFi will list after Stock Tokens, and the prerequisites for each.
---

# Asset roadmap

Expansion happens by asset class, in whatever order the listing requirements can genuinely be satisfied on Robinhood Chain. Every stage is gated on infrastructure facts rather than announcements.

## Stage 1: the Stock Token long tail (live and expanding)

Tier A came first. Tier B and C markets open in batches as feeds are confirmed and bytecode reviews are completed. The long tail is where an RWA-native venue counts most: these are precisely the assets that pooled designs cap hard or exclude, and that thin generic pools misprice worst. See the [Listing framework](listing-framework.md).

## Stage 2: tokenized treasuries

After stablecoins, tokenized US Treasuries are the largest transferable RWA category (~$16B), which makes them the obvious second asset class: a trader leaving equity exposure should be able to move into yield without leaving the venue.

Before the first treasury market opens:

* A treasury token deployed on Robinhood Chain, either natively or bridged via CCIP, from a regulated issuer and with a freely transferable design (USDY-class, not allowlist-gated).
* A NAV-consistent Chainlink feed on chain 4663 for the exact token.
* The [listing checklist](listing-framework.md), plus the NAV addendum: NAV-type assets are quoted within a tight band around NAV, and the closed-regime logic gives way to the fund's own accrual behaviour, because treasuries have no weekend gap in the equity sense.

Where the token is transfer-restricted at the contract level, the vault is deployed in its permissioned variant and allowlisted by the issuer, with eligibility enforced both by the issuer's hooks and by MirageFi's registry.

## Stage 3: tokenized gold

PAXG-class tokenized gold trades 24/7 against a spot reference and has no session structure, which makes it the simplest asset the venue will ever list: `OPEN` at all hours, with the spread driven only by feed cadence and inventory. The requirement is the same one that applies everywhere: the exact token bridged to chain 4663 with a Chainlink feed of its own, plus a bridge review that covers the mint path, the custodian attestations and pause behaviour.

## Stage 4: whatever the chain issues next

Robinhood has signalled private-market tokens and additional RWA classes on its chain. Whatever arrives is measured against the same checklist. An asset that cannot pass it (no per-token feed, no session signal, undisclosed issuer terms) is not listed, however strong the demand.

## What will not be listed

* Wrappers, vault shares or receipt tokens whose price would have to be derived rather than fed. Pricing the exact token is a hard rule; the July 2026 wGOOGLx incident is the case study in [Lessons from RWA trading](../risk/lessons.md).
* Synthetics that lack a regulated issuer and 1:1 backing.
* Any asset whose bytecode review turns up undisclosed transfer controls.
