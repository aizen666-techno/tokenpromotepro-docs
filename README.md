# TokenPromotePro — Technical Documentation

**TokenPromotePro** is a non-custodial, pay-per-action toolkit on Solana mainnet for token creators and traders.

- **Site:** https://www.tokenpromotepro.com
- **Live docs:** https://www.tokenpromotepro.com/docs
- **Technical reference:** https://www.tokenpromotepro.com/technical

Identity is wallet-based (Phantom / Solflare / Backpack). No accounts, no deposits, no custody of user keys — every action is a signed on-chain transaction bound to a server-issued HMAC quote. The interface is available in 12 languages.

## Services

| Service | Function | Price |
|---|---|---|
| [Token Launchpad](docs/launchpad.md) | Atomic SPL mint + Metaplex metadata in one transaction | 0.05 SOL + ~0.02 SOL rent |
| [Social Boost](docs/social-boost.md) | Timed visibility campaign for an existing token | 0.2 SOL (2h) / 0.5 SOL (6h) |
| [Master Liquidity](docs/master-liquidity.md) | 24-hour distributed buy-side engine across 20 child wallets via Jupiter + Jito | 0.6 / 1.1 / 3.0 SOL fee + budget (min 2 SOL) |
| [Rug-Scan / SWR](docs/rug-scan.md) | Token safety analysis + bundled-wallet detection + SOL rent reclaim | Free / 0.1–0.2 SOL / 10% of reclaimed rent |
| [Degen Terminal](docs/degen-terminal.md) | Real-time memecoin discovery feeds, wallet tracking, quick-buy | Free / 0.5 SOL Alpha Vault unlock |

## Architecture

- [Payment verification protocol](docs/payments.md) — HMAC-signed quotes, on-chain verification, replay protection, refund mechanics
- [Security model](docs/security.md) — non-custodial design, AES-256-GCM session keys, Ed25519 session proofs, edge protection

## Articles

- [Jito Bundles and MEV-Protected Execution on Solana](articles/jito-bundles-mev.md) — how bundle submission works and why buy-side engines route through Jito

## Design principles

1. **On-chain truth** — every payment, session balance, and refund is verifiable on Solscan.
2. **Non-custodial end to end** — the platform never holds user keys; generated session wallets are encrypted at rest and swept back to the user.
3. **Pay per action** — no subscriptions; each service is a single verifiable transaction.
