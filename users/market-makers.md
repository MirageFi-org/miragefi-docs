---
description: "A working guide for professional makers: getting admitted, quoting, settling and trading the vault-rebalancing opportunity."
---

# For market makers

Makers supply the venue's professional liquidity: they win block flow through [RFQ](../protocol/rfq.md), compete against the vaults for whatever flow they can price better, and are paid through [skew pricing](../protocol/pricing-and-spreads.md) for keeping vault inventory balanced. Losing a quote costs nothing; winning one settles atomically.

## Admission

RFQ needs a `MAKER` attestation, which professional trading firms obtain through the standard verification flow. At launch there are no quoting obligations, membership fees or volume commitments; consistent quoting earns priority in request routing, and no other tiering exists.

In operational terms you need an EIP-712 signing key (an EOA or an EIP-1271 smart account), a standing Permit2 allowance from your settlement wallet to `RfqSettlement`, inventory in USDG and in the tokens you quote, and a connection to the maker gateway.

## The loop

1. Connect to the gateway WebSocket ([protocol spec](../architecture/api.md)) and receive broadcast requests: pair, size, side and the taker's attestation class.
2. Price the request. In the `OPEN` regime you can hedge on the underlying within seconds; in `CLOSED` you are explicitly pricing weekend gap risk, which is precisely when your quotes matter most and the vault's are at their widest.
3. Sign and return the quote inside the collection window (a few hundred milliseconds). The best quote wins; at settlement the chain verifies your signature, attestation, nonce, expiry and the oracle band.
4. Settlement pulls `tokenOut` from your wallet and delivers `tokenIn` in one atomic step. If there is no fill, nothing moves.

Nonces allow bulk cancellation on-chain, and cancellation remains reachable through the L1 delayed inbox even if the sequencer censors. Keep quote expiries to seconds; the band ensures a stale quote reverts instead of filling at a bad price, but your hedging assumptions remain your own responsibility.

## The rebalancing trade

Vault skew is public state: `AnchorVault.quote()` reveals the side on which each vault improves its price to attract rebalancing flow, up to the maximum skew term at the band edge. Trading against a skewed vault captures spread relative to the oracle mid without risk, with size bounded by the clip, and it is the protocol's intended rebalancing mechanism rather than a loophole. Makers who do this systematically keep the vaults two-sided and collect the skew in return.

## What settlement enforces on you

| Check | Consequence |
| --- | --- |
| Signature, nonce, expiry | Standard; a replay cannot succeed |
| `MAKER` attestation live at fill time | If the attestation is revoked, settlement stops immediately |
| Oracle band per fill | Outside the band you can neither be filled nor fill anyone |
| Atomicity | If a pull fails on either side, the entire fill reverts |

The band is intentionally two-sided: it limits the damage a compromised maker key can do to takers, and it limits what a stale quote can cost you.

## Reporting

Your fills show up in the [trade explorer](trade-explorer.md) alongside everyone else's, with the maker anonymised by default. The API's authenticated endpoints return your own fill and quote-performance history, including win rate and price improvement against the vault, which is the number routing priority is derived from.
