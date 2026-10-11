# Token Launchpad

Create a real Solana SPL token with on-chain Metaplex metadata in a single atomic transaction.

**Live:** https://www.tokenpromotepro.com/#launchpadSection

## How it works

1. Connect a Solana wallet (Phantom / Solflare / Backpack).
2. Fill in name, ticker, description, image, supply, decimals, and optional social links.
3. Optionally select authority revocations (mint / freeze / update) at 0.1 SOL each.
4. Sign one transaction. The token is minted directly to your wallet.

## Atomic transaction composition

A launch is a single transaction containing, in order:

```
transfer(fee → recipient)                    // 0.05 SOL service fee
createAccount(mint, rent-exempt, space=82)
initializeMint(decimals, mintAuthority=payer, freezeAuthority=payer)
createAssociatedTokenAccount(owner=payer)
mintTo(mint → payer_ata, supply)
createMetadataAccountV3(name, symbol, uri=IPFS)
setAuthority(mint → null)                    // optional, +0.1 SOL
setAuthority(freeze → null)                  // optional, +0.1 SOL
updateMetadata(updateAuth → burn)            // optional, +0.1 SOL
```

Because everything is atomic, the token either exists fully — minted, supplied, metadata-annotated, authorities revoked — or the whole transaction reverts. There is no partial-launch state.

## Pricing

| Item | Cost |
|---|---|
| Base mint | 0.05 SOL |
| Network rent (mint + ATA, paid to Solana) | ~0.02 SOL |
| Revoke Mint Authority (optional) | +0.1 SOL |
| Revoke Freeze Authority (optional) | +0.1 SOL |
| Revoke Update Authority / immutable metadata (optional) | +0.1 SOL |

## Supply constraint

`supply × 10^decimals < 1.8 × 10^19` (u64 representation bound).

## Clone mode

Paste any existing mint address to autofill the entire form — name, ticker, description, image (re-encoded for re-minting), socials, decimals, supply. Resolution uses a three-source fallback chain:

1. Metaplex on-chain PDA metadata
2. pump.fun metadata endpoint
3. Dexscreener API

Works for standard Metaplex tokens and pump.fun mints.

## Metadata pipeline

Images upload to IPFS via a server-side Pinata gateway; the Metaplex `CreateMetadataAccountV3` instruction references the resulting JSON (name, symbol, image URI, description, external links).

## Post-launch

After confirmation the UI shows the contract address, a Solscan link, an "Add Liquidity on Raydium" shortcut, and a Social Boost cross-sell.
