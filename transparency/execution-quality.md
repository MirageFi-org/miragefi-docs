---
description: The venue grades its own execution publicly, against the reference price, on every single fill.
---

# Execution quality

Execution quality is what the venue sells, so it is measured and published the way infrastructure publishes uptime: continuously, without filtering, and against a benchmark that nobody at the venue controls.

## The metric

For each fill: the signed distance, in basis points, between the realised price and the guarded Chainlink mid recorded in the oracle round of the fill event. A positive value means the trader paid above mid (the usual case, by the spread); a negative value means price improvement (competition in RFQ can beat the vault's spread). Since the mid used is the very one the fill was checked against, anyone can recompute the metric exactly from chain data, and the venue has no way to substitute a kinder benchmark afterwards.

## What is published

| Cut | Contents |
| --- | --- |
| Live | Rolling distributions by market, venue and regime; median, p90 and the worst fill |
| Daily | The complete fill-level dataset, downloadable, with no minimum size filter and nothing excluded |
| By regime | `OPEN` and `CLOSED` costs shown side by side, making the weekend premium a published figure instead of a suspicion |
| RFQ | Aggregated maker price improvement relative to the vault quote at the same instant |
| Reverts | Slippage-bound and band reverts expressed as a rate, since a venue can make its fills look better by failing them |

## How to read it

* For a Tier A market, the median `OPEN` fill should land close to the base half-spread plus fee. Sustained drift above that level means the parameters need retuning, and the timelocked retune will cite this dataset.
* The `CLOSED` distribution is expected to be wider and more expensive; that is gap risk being priced. What it must never show is fills near the band edge in calm conditions.
* When band margins compress suddenly across many fills, that is the early signature of an oracle problem, which is why the operations stack alerts on the same public numbers shown on this page.

## Why publish the bad days too

A dataset with exclusions is marketing. Publishing every fill, the expensive weekend ones and the reverts included, means that an LP underwriting a vault, a maker calibrating quotes and a trader deciding whether Saturday can wait until Monday all work from the same record the venue itself is judged against. Venues that grade themselves honestly improve; this dataset is the venue's own tuning input, made public.

The raw data lives at `/v1/execution-quality` in the [API](../architecture/api.md), and every aggregate on this page links through to the underlying fills in the [trade explorer](../users/trade-explorer.md).
