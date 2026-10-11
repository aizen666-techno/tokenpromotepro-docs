# Jito Bundles and MEV-Protected Execution on Solana

*An engineering note on how buy-side transaction engines route swaps through Jito, and why bundle submission matters for distributed liquidity execution.*

## The problem: public mempool exposure

On Solana, a swap submitted through a plain RPC broadcast enters the public transaction stream. Specialized searchers observe these transactions and can attempt sandwich-style extraction: front-running the buy and selling into it, capturing the price impact the buyer paid for.

For a single retail swap the loss is small. For an automated engine executing hundreds of buys across 20 wallets over 24 hours, systematic MEV extraction compounds into a meaningful drag on budget efficiency.

## What a Jito bundle is

The Jito Block Engine accepts **bundles**: ordered groups of transactions that are executed atomically within a block or not at all. A bundle either lands completely or fails completely — there is no partial execution where a searcher inserts a transaction between yours.

Key properties:

- **Atomicity** — all transactions in a bundle execute in sequence in one slot, or none do.
- **Ordering guarantee** — the bundle's internal ordering is respected; external transactions cannot interleave inside the bundle.
- **Tip-based priority** — bundles carry a tip to validators; higher tips compete for faster inclusion, trading cost for landing probability.

## How this applies to a distributed liquidity engine

TokenPromotePro's Master Liquidity engine executes `SOL → token` swaps across 20 child wallets. Each tick:

1. Requests a swap quote from the Jupiter Lite API (`SOL_TO_TOKEN` direction only — the engine is buy-side by design).
2. Builds the swap transaction for a randomly selected active child wallet.
3. Submits the transaction as a Jito bundle with a randomized tip.
4. Confirms landing before crediting session metrics — unconfirmed submissions are logged as deferred events, and the budget conservatively reserves the potentially spent lamports.

The result: buys land through MEV-protected inclusion rather than mempool exposure, and metrics only count verified execution.

## Cost accounting reality

Every executed buy consumes:

```
swap amount + Jito tip + priority/tx fee + ATA rent on first buy (~0.002 SOL)
```

An engine that ignores rent and tips in accounting drifts — the on-chain wallets empty before the recorded budget does. Deducting all four components per trade keeps the ledger and the chain in agreement, which is what makes the refund sweep correct to the last lamport.

## Randomization as hygiene

Because 20 wallets funded from one master is a detectable funding pattern, execution hygiene matters at the distribution layer too:

- Gaussian-distributed swap amounts (no round numbers, no fixed sizes)
- Randomized Jito tip amounts
- Staggered per-wallet job scheduling and jittered tick intervals
- Per-wallet cooldowns so no single address produces a metronome pattern

The goal is not to hide the activity — all 20 wallets are publicly traceable back to the funding wallet on-chain — but to avoid producing a mechanically obvious execution signature that degrades execution quality against other searchers.

## What this does not do

Honest limits worth stating:

- Bundles protect against *intra-bundle* sandwiching of the submitted transaction set; they do not change market price impact of the buys themselves.
- Randomization reduces mechanical obviousness; it does not make wallets anonymous. Funding topology remains publicly inspectable.
- Jito submission improves inclusion characteristics; it cannot guarantee landing in a congested block.

## Further reading

- Jito Block Engine documentation: https://docs.jito.wtf
- Jupiter swap API: https://station.jup.ag/docs
- TokenPromotePro Master Liquidity mechanics: https://www.tokenpromotepro.com/technical
