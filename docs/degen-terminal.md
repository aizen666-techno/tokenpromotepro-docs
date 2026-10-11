# Degen Terminal

A real-time memecoin discovery terminal built on public Solana data streams.

**Live:** https://www.tokenpromotepro.com/degen-terminal

## Data pipeline

- **New-token feed** — a direct WebSocket subscription to the pump.fun launch stream renders new mints as they are created on-chain.
- **Golden Dev Hunter** — deployer addresses are matched against the history of previously launched tokens; repeat deployers with graduated tokens are flagged inline.
- **Shadow Whale tracking** — monitors large SOL transfers from exchange-linked wallets to fresh addresses, an early-accumulation signal.
- **Migration & sniper context** — tracks pump.fun → Raydium migrations and sniper activity around new listings.
- **Market data** — charts and token metadata served through public aggregator APIs (Dexscreener, pump.fun).

## Alpha Vault

The terminal is free to read. The optional Alpha Vault unlock (0.5 SOL, quote-bound payment like every other service) exposes the top-5 ranked signals per feed for the session window. Unlock state is verified on-chain and can be consumed session-wise.

## Quick-buy

Token rows carry quick-buy execution against public DEX routes — the same Jupiter liquidity path used elsewhere on the platform.
