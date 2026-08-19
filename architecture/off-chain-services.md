---
description: The stateless services that surround the contracts, and why none of them needs to be trusted.
---

# Off-chain services

The contracts are surrounded by three services. All of them share one design constraint: a service is allowed to make the venue faster or more convenient, but never more trusted. When any of them fails, convenience degrades and the guarantees stay exactly as they were.

## Quote service

The quote service answers the question "what would this swap cost right now". It simulates `SwapRouter` against the current oracle and vault state, sends RFQ requests out to connected makers, gathers signatures for a few hundred milliseconds, and returns the best composite quote together with its full breakdown. Both the platform ticket and the public [API](api.md) sit on top of it.

What keeps it honest:

* It holds no keys and never touches funds. Its output is a preview and, in the RFQ case, maker signatures that the chain verifies on its own.
* Vault pricing is re-derived on-chain by the router at fill time, so an incorrect preview causes a revert against the trader's signed bound rather than a bad fill.
* It has no way to suppress the vault path: whenever the vault beats every quote supplied, the router settles through the vault.

Should the service go down, vault swaps continue to work from any interface able to call the contract, at exactly the same prices. What is lost is RFQ aggregation and convenience, nothing more.

## Keeper service

Regime transitions, staleness halts and sequencer-grace expiries are all permissionless pokes; the keeper service is nothing more than the process that calls them promptly. Since every quote recomputes the regime inside the transaction anyway, a slow keeper cannot produce stale-session pricing. The only effect is a delay before the explorer's display catches up. The reference keeper is open source and anyone can run one.

## Indexer

Fill and parameter events on chain 4663 are turned by Ponder (self-hosted, TypeScript) or Envio HyperIndex into the [trade explorer](../users/trade-explorer.md), the [execution quality](../transparency/execution-quality.md) dataset, and per-market LP statistics. Every figure displayed links back to the event it was derived from, so anyone who distrusts the indexer can recompute the lot from Blockscout.

## Maker gateways

Market makers connect to the quote service through a WebSocket protocol described in [API and SDK](api.md): request broadcasts come in, signed quotes go back. The gateways are infrastructure the makers own; the MirageFi side does nothing but relay and rank. If a maker's connection drops, their quotes leave the auction and nothing else changes.

## Monitoring

The operations stack observes the same public state that anyone else can: oracle staleness and divergence, vault skew relative to bands, halt states, sequencer uptime, fill-versus-band margins, and drift in execution quality. Alerts go to the guardian on-call. There is no privileged telemetry; everything worth monitoring lives on-chain, which is as much a design statement as an operational one.

## Running your own

All of the above is open source. Integrators who want independence can run the quote service, keeper and indexer from the public repositories against any RPC provider, and the [platform](platform.md) front-end is a reference client for the same public APIs it documents.
