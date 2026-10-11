# Payment Verification Protocol

Every paid action on TokenPromotePro follows the same verification pipeline.

## Quote binding

Every paid action begins with a server-issued quote signed with HMAC-SHA256, pinning:

- service identifier
- price in lamports
- token contract address
- recipient wallet
- 10-minute expiry

Client-supplied prices are never trusted — the client renders what the quote says.

## On-chain verification

A payment must satisfy all of the following:

1. Valid quote signature (tamper-evident).
2. On-chain transaction match — correct recipients and lamport amounts.
3. Durable signature replay protection — every transaction signature is stored and rejected on reuse, across all payment flows (double-spend impossible).
4. Freshness — the transaction must land within a 24-hour window of verification.
5. For liquidity sessions, the token CA is taken from the quote — not the client payload — so a session cannot be re-targeted after quoting.

## Session recovery

Liquidity sessions are bound to the paying wallet. The Restore flow proves ownership via an Ed25519 wallet signature inside a 10-minute timestamp window and reissues a status token — no password, no email.

## Refunds

Liquidity budgets refund to the last lamport: the sweep engine reads live balances of all 21 session wallets (1 master + 20 children), subtracts required transaction fees, and returns the remainder. Refund claims are atomic and idempotent — enforced by a partial unique index, once per session. SPL/Token-2022 tokens held by child wallets are transferred back to the customer in the same flow.
