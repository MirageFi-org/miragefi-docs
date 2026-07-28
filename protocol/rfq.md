---
description: How competing makers price block-size swaps, and how those swaps settle atomically on-chain.
---

# RFQ settlement

For sizes above the vault clip, and for any trade on which a maker beats the vault, execution moves to request-for-quote: professional market makers sign short-lived quotes off-chain, and the taker settles the best of them on-chain in one atomic transaction. A signature costs a maker nothing, so makers can stream competitive quotes without pause; the chain is paid only when a fill happens.

## The quote

A maker quote is an EIP-712 typed message:

| Field | Meaning |
| --- | --- |
| `maker` | The address that signs, which must hold a `MAKER` attestation |
| `tokenIn`, `tokenOut` | The pair; one side is always USDG |
| `amountIn`, `amountOut` | The precise size and price; partial fills are not allowed |
| `taker` | The trader who asked, or zero for an open quote |
| `expiry` | Unix seconds; a quote lives for seconds rather than minutes |
| `nonce` | Used once; can also be cancelled in bulk |

The quote service relays a trader's request to every connected maker, gathers signatures for a few hundred milliseconds, and returns the best one together with the vault price. The router settles whichever of the two is better for the trader.

## Settlement

`RfqSettlement.settle(quote, signature)` checks the maker's signature and attestation, the taker's `TRADER` attestation, the nonce and the expiry, and that the implied price lies within the [oracle band](pricing-and-spreads.md). Assets then move directly: `tokenIn` passes from taker to maker, `tokenOut` from maker to taker, and the RFQ protocol fee goes to the `FeeCollector`. The maker's assets are drawn from the maker's own wallet through a standing Permit2 allowance; at no point before, during or after does MirageFi hold anything.

If a quote has expired, if its nonce has been spent or cancelled, or if its price has moved outside the band by the time of inclusion, it reverts, and the maker bears no cost beyond their own hedging decisions.

## Why makers come

* **Hedgeable flow.** RFQ flow arrives with known size and direction, in assets that have a liquid underlying. A maker who fills 200,000 USDG of tokenized NVDA can hedge on the underlying market within seconds while it is open, or price weekend gap risk explicitly while it is closed.
* **The skew subsidy.** Vault [inventory skew](pricing-and-spreads.md) publicly signals which side each vault will pay to be lifted on. Makers who track skew earn a spread for rebalancing vaults, which is the protocol's rebalancing mechanism doing exactly what it was designed to do.
* **No exchange fees, no queue.** To win is to sign. To lose costs nothing.

## Failure containment

Each guard on the vault path applies here as well: the band bounds any bad quote, attestations bound who may make, expiries bound how long a stale price survives, and atomic settlement means that a failed leg reverts the entire fill. The worst a malicious or compromised maker can do is fill trades inside the band, which is the same worst case as an aggressive but honest maker.

Maker onboarding, tooling and the quote protocol specification live in [For market makers](../users/market-makers.md) and [API and SDK](../architecture/api.md).
