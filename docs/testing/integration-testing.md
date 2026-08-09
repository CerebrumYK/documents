# INTEGRATION TESTING
## DOC-172 — PostgreSQL, filesystem, media, workers, PDF/QR и provider adapter contracts

**Статус:** Canonical / Required

---

## 1. Назначение

Integration tests prove contracts between application components and real infrastructure boundaries that unit tests cannot faithfully simulate.

> **Whenever correctness depends on PostgreSQL transactions/constraints, filesystem bytes, rendering, queue persistence or an adapter protocol, at least one integration layer must exercise that real behavior.**

---

## 2. Required integration boundaries

```text
Application ↔ PostgreSQL
Application ↔ filesystem/media storage
Worker ↔ job/outbox tables
PDF renderer ↔ DocumentModel
QR renderer ↔ decoder
Search projection ↔ source data
Cache ↔ invalidation generation
Provider adapter ↔ fake/sandbox HTTP server
```

## 3. PostgreSQL

Use actual PostgreSQL test instance, not in-memory substitute, for:

- FK;
- unique constraints;
- transactions;
- isolation/concurrency;
- JSONB behavior where used;
- indexes/query semantics;
- migration execution.

## 4. Transaction tests

Examples:

- Feedback + attachment binding + Audit + Outbox atomic commit;
- failure rolls back all authoritative rows;
- Questionnaire publish revision/primary update atomic;
- CreateCastingFromInquiry race-safe;
- Opportunity StageHistory + current stage atomic.

## 5. Constraint tests

Try invalid direct persistence paths:

- duplicate current primary;
- invalid FK;
- wrong enum/check;
- duplicate idempotency key semantics;
- orphan relation.

## 6. Concurrency

Run actual parallel transactions for race-prone commands and assert one logical outcome.

## 7. Filesystem

Use temporary isolated storage root matching production path semantics. Test:

- generated paths;
- permissions;
- staged→bound file flow;
- cleanup;
- checksum;
- missing file behavior;
- no path traversal.

## 8. Media processing

Exercise actual chosen image/video/audio tools on small legal fixtures. Assert output metadata and original hash unchanged.

Do not require giant production-like media in normal CI; extended suite can test larger files.

## 9. PDF renderer

Render real PDF from fixture DocumentModel and inspect:

- text extraction;
- links;
- pages/layout basics;
- embedded QR asset;
- filename/metadata safety.

## 10. QR decoder

Generate SVG/PNG, rasterize if necessary, decode with real decoder, compare exact canonical URL.

## 11. Search

Index fixture data, query surface-specific index/projection, then change visibility/archive and prove rebuild/current hydration removes old hidden result.

## 12. Cache

Exercise current generation/alias invalidation. Test public/token partitions and revocation-sensitive resource behavior.

## 13. Background jobs

Use real job/outbox tables with worker process/handler in test mode:

- lease one job once at a time;
- crash/retry;
- idempotency;
- max retries;
- final failure;
- stale job current-state check.

## 14. Notification provider fake

Run controlled fake HTTP provider to simulate:

```text
success
timeout
5xx
rate limit
permanent rejection
unknown outcome
webhook callback
```

Assert domain business record unaffected.

## 15. Social provider fake

Simulate accepted/processing/published, invalid token, rate limit, timeout unknown outcome, duplicate/out-of-order webhook.

## 16. AI adapter fake

Simulate structured success, invalid schema, timeout, provider unavailable. Assert Draft/Analysis behavior without real model.

## 17. OAuth

Test state/PKCE/callback/token storage with fake OAuth provider. Ensure token never appears in client response/log capture.

## 18. SSRF

Integration test URL checker against local controlled endpoints and redirects; ensure policy blocks localhost/private target even through redirect.

## 19. Uploads

Actual multipart upload tests for MIME mismatch, size limit, safe files, quarantined file and download authorization.

## 20. Email/message rendering

Test template variable escaping/header safety and protected deep links; no raw user newline/header injection.

## 21. Timezones

Integration scheduler test uses controlled clock and DB timestamps. Verify exact due selection and DST cases.

## 22. Migrations

Run migration chain from empty and representative previous schema. Database ends at expected schema/invariants.

## 23. Test isolation

Each test or suite uses transaction rollback/database reset/schema clone to avoid order dependence. Filesystem temp roots cleaned.

## 24. Failure diagnostics

On failure retain enough fixture IDs/log metadata for debugging, not raw secrets.

## 25. CI

Integration suite can be parallelized with isolated DB/storage namespaces. Use deterministic Docker/service versions where repository tooling supports it.

## 26. Anti-patterns

Forbidden:

1. SQLite used to prove PostgreSQL behavior.
2. Filesystem mocked for path/security logic only.
3. PDF test only checks file exists.
4. QR test only checks image exists.
5. Provider timeout untested.
6. Concurrency simulated sequentially.
7. Migration test starts only from current schema.
8. Integration suite shares mutable global test DB without isolation.
9. Production OAuth/provider token used in CI.
10. Failure logs dump secret response headers.

## 27. Acceptance criteria

`AC-INT-001` PostgreSQL constraints/transactions are exercised against real PostgreSQL.  
`AC-INT-002` Race-prone operations have real concurrent integration tests.  
`AC-INT-003` Filesystem/media integration proves immutable originals and safe paths.  
`AC-INT-004` PDF text/links and QR decode are inspected semantically.  
`AC-INT-005` Search/cache visibility invalidation is tested.  
`AC-INT-006` Worker durability/retry/idempotency is tested against persistent queue.  
`AC-INT-007` Provider adapters cover success/retry/final/unknown outcomes.  
`AC-INT-008` OAuth/webhook security contracts are tested with controlled provider.  
`AC-INT-009` SSRF/upload security uses actual I/O test boundaries.  
`AC-INT-010` Migration chain executes in integration environment.  
`AC-INT-011` Tests are isolated/repeatable.  
`AC-INT-012` CI uses no production secrets.

---

## 28. Финальная доктрина

> **Integration testing proves the places where real infrastructure semantics matter: PostgreSQL atomicity, filesystem bytes, rendering/decoding, durable workers and provider protocols. It replaces optimistic mocks with controlled real boundaries while keeping production providers and secrets out of normal CI.**
