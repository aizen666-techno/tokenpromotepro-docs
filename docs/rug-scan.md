# Rug-Pull Checker & SWR Deep Scan

Paste any Solana token contract address for an on-chain safety report.

**Live:** https://www.tokenpromotepro.com/scan

## Free scan

The base scan is free and evaluates:

- Mint authority and freeze authority status
- LP burn percentage
- Top-holder concentration
- Token-2022 transfer fees (hidden taxes)
- Deployer history
- Transaction forensics

## SWR Deep Scan (Shadow Whale Radar)

The paid tier reconstructs funding links between wallets: clusters of wallets funded by a common source inside a short window, holding a material share of supply in aggregate, are flagged as bundled distribution — the primary signature of insider-controlled supply.

| Token risk profile | Price |
|---|---|
| Low bundled share | 0.1 SOL |
| Material bundled share | 0.2 SOL |

Payment is verified on-chain via the same quote-signed flow used by every paid service.

## SOL rent reclaim

The scanner also surfaces reclaimable rent: empty or dead SPL token accounts owned by the connected wallet can be closed to recover their SOL rent deposits (~0.002 SOL each). The service fee is 10% of the reclaimed amount. Also available as a Solana Blink/Action.
