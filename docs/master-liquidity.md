# Master Liquidity Engine

A 24-hour automated buy-side execution engine that distributes a customer's trading budget across 20 generated child wallets and executes randomized `SOL → token` swaps through Jupiter with Jito bundle submission.

**Live:** https://www.tokenpromotepro.com/#liquidity-engine

## Payment model

One transaction, two transfers:

- **Service fee** → platform wallet (0.6 / 1.1 / 3.0 SOL by tier)
- **Trading budget** → a per-quote generated master wallet (min 2 SOL, max 100 SOL)

Both transfers are verified on-chain against a tamper-proof HMAC-signed quote (10-minute TTL, replay-protected). Client-supplied prices are never trusted.

## Session lifecycle

```
PENDING_PAYMENT  → quote issued (HMAC-signed, 10-min TTL)
PAID_VERIFIED    → dual-transfer confirmed on-chain
RUNNING          → engine ticks execute distributed buys
DEGRADED         → transient failure; auto-recovers on next good tick
ENDED / FAILED   → terminal; refundable via sweep
```

## Wallet-distribution model

On payment verification the engine generates **1 master wallet + 20 child wallets**. The budget is split across the children, which then act as 20 distinct holder addresses — producing real, distributed on-chain buying rather than a single concentrated buyer footprint. Purchased tokens remain in the child wallets and belong to the customer's session; the engine is buy-only by design.

## Execution path

Each engine tick selects active child wallets and issues buy-side swap quotes through the Jupiter Lite API. Swap transactions are submitted as **Jito bundles** for MEV-protected inclusion. Only confirmed landings increment session metrics; unconfirmed submissions are recorded as deferred events while the budget accounts for potentially spent lamports.

Anti-pattern execution: randomized swap amounts (Gaussian distribution), randomized Jito tips, staggered wallet jobs, and jittered tick intervals so buys do not look mechanical.

## Tiers

| Tier | Fee | Execution profile |
|---|---|---|
| IGNITE | 0.6 SOL | Baseline distributed buys, randomized intervals |
| PHANTOM | 1.1 SOL | Higher distribution diversity, reduced on-chain linkage |
| GODMODE | 3.0 SOL | Maximum intensity, priority bundle submission, extended Jito tip allocation |

## Budget accounting

Per executed buy the ledger deducts: swap amount + Jito tip + transaction fee + first-buy associated-token-account rent (~0.002 SOL). This prevents the drift condition where on-chain balances empty before the recorded budget reaches zero.

## Customer controls

- **Live dashboard** — status pill, countdown, volume generated (USD), transaction count, active wallets x/20, budget remaining, engine activity feed, embedded Dexscreener chart.
- **Top-up mid-session** — add SOL to the master wallet; verified on-chain and auto-redistributed to all 20 children (also revives errored wallets).
- **End & Refund** — returns all SPL/Token-2022 tokens from child wallets plus every recoverable lamport of unused SOL. Idempotent, race-safe, works even on FAILED sessions.
- **Restore session** — sign a wallet message (no transaction, free) to rehydrate the dashboard on any device. The wallet signature proves ownership inside a 10-minute timestamp window.

## Refund mechanics (refund-to-last-lamport)

Session termination — manual or at the 24-hour boundary — triggers the sweep engine: it reads live on-chain balances of all 20 children and the master, issues per-wallet transfers back to the user, deducts only required transaction fees, and records the refund in an idempotent ledger entry enforced by a partial unique index. Dust below the sweep-fee threshold (~6,000 lamports) is skipped by design.

## Safety engineering

- Budget pacing across remaining session time
- Per-wallet cooldowns and error states
- Global tick lock and stale-job recovery
- Confirmed-only metrics accounting
- Atomic refund claims via database constraints
- Sessions auto-END at expiry; exhausted budget ends as ENDED, not a fake failure state
- If a wallet-distribution batch fails mid-way, funded children are swept back to the master wallet — no lamports stranded in untracked keypairs
