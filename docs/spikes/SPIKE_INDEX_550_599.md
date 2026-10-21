# SPIKE INDEX 550–599

This index captures the second batch of proposed integration spikes for PHASE.

Status: proposed, not yet implemented.

## Batch summary

The following spikes are intentionally scoped as migration-bearing, cross-boundary, and flag-gated. Each should be implemented only behind a feature flag and documented in PROJECT_ARCHITECTURE.md and docs/TECHNICAL.md.

## Proposed spikes

### 550 — Protocol treasury segregation and passive treasury yield
- Combine protocol treasury reads with passive address accounting and yield monitoring.
- Enforce segregation between distributor operating funds and treasury funds.
- Add read paths for treasury balances and APY/position data.
- Gate any on-chain treasury movement behind admin auth and timelock-safe flows.
- Risk: double-spend class / treasury-drain class if distributor and treasury are combined.

### 551 — Referral settlement and attribution
- Add referral linkage to settle flows without weakening existing write invariants.
- Introduce stable off-chain cache or ledger-backed referral state that is resilient to replay.
- Prevent self-referral / sybil amplification and cap referral claims on-chain or with enforceable off-chain checks.
- Ensure contributor and royalty split math remains deterministic when referral attribution is present.

### 552 — Quest snapshot and claim-receipt integrity
- Move claim state from a single monolithic faucet flow to a deterministic claim-receipt pattern.
- Snapshot quest eligibility at ledger time, maintain claimed bitmap state, and guard against replay or stale claims.
- Keep rate limits and deny-list checks consistent across wallet, deploy, and claim flows.
- Preserve idempotency under serverless timeout and retry windows.

### 553 — Webhook fulfillment and payment provider idempotency
- Reuse existing HMAC verification patterns but require strict raw-body verification before fulfillment.
- Add event-id deduplication before minting or transfer operations are executed.
- Prevent duplicate fulfillment after provider retries or duplicate webhook deliveries.
- Require a kill switch for payment-provider outage and dead-letter handling.

## Cross-cutting requirements

All spikes in this batch must satisfy the same guardrails:

- Default flag off.
- No hardcoded testnet IDs in runtime code.
- Typed API payloads and deterministic status codes.
- Replay/idempotency handling before any side-effecting operation.
- i18n keys through lib/phase-copy.ts and normalized error mapping.
- Observability labels and counters that avoid wallet or PII leakage.
- Documentation updates to PROJECT_ARCHITECTURE.md and docs/TECHNICAL.md.

## Acceptance gate

This batch remains proposed until the following are proven:

1. End-to-end testnet flow with fund → act → verify → index → UI.
2. 409/429/412 taxonomy and retry semantics for overlapping writers.
3. Contract drift / issuer rotation / gateway failover guard checks.
4. Migration + rollback story with feature flag off + data inert on disk.
5. No regression when the new flags are disabled.

## Notes

This batch intentionally does not duplicate the earlier 500–549 spike set; it extends the same security posture while covering treasury, claim, referral, and event-driven fulfillment behavior.
