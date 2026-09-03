---
description: Straight answers to the questions traders, LPs and integrators really ask.
---

# FAQ

## Trading

**Is MirageFi an exchange?**
It is a non-custodial swap venue: oracle-anchored vaults and competing makers supply the quotes, settlement happens atomically on Robinhood Chain, and the venue never holds your assets. There is no order book, and no account you need to fund.

**Why is the spread wider tonight than it was this afternoon?**
Because the underlying market has closed. Quotes in the closed regime carry a ×3 spread multiplier and smaller clips, since LPs are carrying the gap to the next open. The regime badge on the ticket states exactly which multiplier applies. See [Trading regimes](../protocol/trading-regimes.md).

**Can I trade while a market is halted?**
No, and nobody else can either; that is the whole idea. Halts track the oracle (staleness, corporate actions, implausible jumps) and clear automatically along with it. Your assets remain in your wallet the entire time.

**Do I own Apple shares once I buy tokenized AAPL?**
No. What you hold is a Stock Token: an ERC-20 debt security issued by Robinhood Assets (Jersey) Ltd that tracks the share, with dividends accreting into the token through its multiplier. Read [Stock Tokens](../assets/stock-tokens.md) before you trade in size.

**Why was my swap rejected before I even signed?**
Either your wallet has no live `TRADER` attestation, or your jurisdiction is restricted for that particular asset. The platform tells you which rule failed. See [Eligibility registry](../architecture/eligibility.md).

**Where are the dividends?**
Inside the token. Stock Tokens reinvest dividends by raising `uiMultiplier()`, and the price reflects that. There is nothing for you to claim. See [Corporate actions and dividends](../protocol/corporate-actions.md).

## Liquidity

**What do LPs really earn?**
The realised spread on the fills their vault makes, less the protocol's 10% share, accruing into value per share. There are no emissions; the yield is what the market pays for immediacy. Realised figures for each market are public in the [trade explorer](../users/trade-explorer.md).

**Can a pause or halt lock my funds?**
No. Withdrawal works in every vault state, halts and the guardian pause included, and that property is protected by invariant. What you withdraw is a pro-rata, in-kind mix of the vault's assets. See [Liquidity provision](../protocol/liquidity-provision.md).

**Is this impermanent loss?**
Not in the AMM sense. Anchored vaults do not leak value to reference-price arbitrage, which is where curve LPs lose money. Vault LPs do carry real inventory exposure (the token can fall) and gap risk on closed-regime fills; both are stated plainly and both are paid for through the spread.

## The venue

**Who sets the prices?**
A formula applied to public state: Chainlink mid, tier spread, regime multiplier, inventory skew and an itemised fee. No one at MirageFi can alter an individual quote or fill. See [Pricing and spreads](../protocol/pricing-and-spreads.md).

**What happens if MirageFi the company ceases to exist?**
The contracts go on quoting from on-chain state, withdrawals stay unconditional, and the services are open source, so anyone can run them. See [Corporate structure](../compliance/corporate-structure.md).

**Why should I trust the execution?**
You should not need to: the itemised breakdown of every fill is on-chain, and the venue publishes how far each fill sat from the reference price, with nothing filtered out, at [Execution quality](../transparency/execution-quality.md).

**Can I integrate MirageFi into my own app?**
Yes: there is a public [API and SDK](../architecture/api.md), a permissionless router and no partner tier. Eligibility attaches to your users' wallets, not to your app.
