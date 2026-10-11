# Social Boost

A timed visibility campaign for an existing Solana token.

**Live:** https://www.tokenpromotepro.com/#boostSection

## Tiers

| Tier | Price | Window |
|---|---|---|
| Public | 0.2 SOL | 2 hours |
| Private | 0.5 SOL | 6 hours |

## Flow

1. Connect a Solana wallet.
2. Submit the token contract address and social details.
3. Pay — the campaign activates after on-chain verification.

## Payment

Payment follows the platform-wide quote-bound protocol: the server signs the price together with the token contract address (HMAC-SHA256, 10-minute expiry), and the campaign is only registered after the on-chain transfer verifies. Campaign state is stored in the ledger keyed to the paying wallet — no account or email is collected.

Lead notifications to the operator channel pass through Cloudflare Turnstile and server-side input validation.
