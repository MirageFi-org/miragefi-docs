---
description: The public window onto everything the venue does.
---

# Trade explorer

The trade explorer is the venue's public record: each fill, the live state of each market, every parameter and every change made to one, all rendered from chain events with a link back to each. No wallet is needed to load it, because nothing in it is anyone's private information. "Transparently on-chain" can be checked here, or it means nothing.

## Per market

| Panel | Contents |
| --- | --- |
| Live quote | The current bid, ask and mid with the full breakdown, priced exactly as the router would price it now |
| Regime | The current regime, the signals it is derived from (feed timestamp, stream market status), and how long it has been in that state |
| Inventory | The vault mix relative to target and band; the direction and size of skew |
| Parameters | Tier, spreads, clip, caps, band, each one read from `ParamController` |
| History | Fills with their itemised breakdowns; spread revenue by regime; the halt log with reasons |
| Listing record | Tier evidence, the bytecode review finding, the issuer disclosure, and proof-of-reserve status wherever a feed exists |

## Venue-wide

* Volume, fill count and revenue broken down by market, by venue (vault or RFQ) and by regime.
* Total vault inventory exposed to Robinhood Assets (Jersey) Ltd, expressed as one number, per [Issuer risk](../risk/issuer.md).
* The [execution quality](../transparency/execution-quality.md) dataset: how far each fill sat from the oracle mid, aggregated and available for download.
* The governance log: every parameter proposal with its rationale, its timelock window and its execution, both past and pending.

## Every number is a citation

Every figure links to the event, transaction or contract read on Blockscout that produced it. Because the explorer runs on the open-source indexer, anyone can rerun it against any RPC and diff the result; any discrepancy between explorer and chain is a reportable bug, and the chain is authoritative.

## For auditors and integrators

All of the rendered data is available raw through the [API](../architecture/api.md) (`/v1/markets`, `/v1/fills`, `/v1/execution-quality`) and as CSV exports, including full per-position LP histories for account holders. Researchers are welcome to use the data; the venue's view is that an RWA market whose record cannot be reconstructed independently has no record at all.
