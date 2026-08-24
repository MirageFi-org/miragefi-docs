---
description: The reference front-end at miragefi.org/platform, and the experience standards it must meet.
---

# Platform

The platform is the reference client: a Next.js application at [miragefi.org/platform](https://miragefi.org/platform) that consumes the same public [API](api.md) and contracts every integrator can use. It holds no keys, sees no funds, and has no privileged path. Its purpose is to make the honest mechanics legible.

## The ticket

The swap ticket is the centre of the product, and instead of hiding the [pricing formula](../protocol/pricing-and-spreads.md) it renders it:

* Mid, spread, and fee as separate labelled numbers, in both bps and USDG, with the oracle round linkable.
* The regime badge right on the ticket face: "US markets closed. Weekend spread ×3 applies." No trader discovers the session after the fill.
* The all-in rate protected by the slippage bound, plus a countdown on RFQ quote validity.
* On two-leg swaps, both legs are shown and both are itemised.

A fill arrives as a receipt carrying the on-chain breakdown and a Blockscout link. Since the event carries the same decomposition, what was quoted is provably what was charged, every single time.

## Onboarding

Connect Robinhood Wallet, MetaMask or any standard wallet, or create an embedded smart account (Alchemy Account Kit) using a passkey. Eligibility onboarding runs the attestation flow with the KYC provider; eligibility state is shown by the platform before anything is signed, and an ineligible user is told which rule applies rather than being shown a revert. A first swap bundles the Permit2 approval and the swap into a single sponsored user operation.

## Portfolio

A wallet-centric view of listed holdings: balances in token and share terms (via `uiMultiplier()`), valuations drawn from the same guarded oracles the vaults quote from, cost basis and realised PnL rebuilt from on-chain history, and LP positions showing value per share, accrued spread income and the current inventory mix for each vault.

## Explorer surfaces

The [trade explorer](../users/trade-explorer.md) and [execution quality](../transparency/execution-quality.md) pages belong to the platform and load with no wallet connected. Transparency surfaces are public by definition; no part of the venue's record sits behind a login.

## Experience standards

The bar is the one set out in [Design principles](../introduction/design-principles.md): calm, technical, precise. In concrete terms:

* Numbers set in tabular figures and aligned; bps and USDG never share a column; timestamps in the user's zone, with UTC on hover.
* Empty, loading and error states written as full sentences saying what happened and what to do next.
* Degradation is made explicit: when the quote service is down, the platform quotes vault-only from the RPC and says so; when the RPC is down, it says that rather than spinning.
* No dark patterns whatsoever: no preselected slippage above the default, no urgency theatre, no hidden fees. The venue's economics can survive being understood.

## Self-hosting

The platform is open source. Running `pnpm dev` against any RPC and the public API reproduces the whole experience apart from the hosted KYC flow, which can be replaced by any issuer the registry accepts.
