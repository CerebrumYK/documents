# BACKEND & WORKER IMPLEMENTATION
## DOC-205 — application services, server actions, durable jobs, outbox, provider adapters and reconciliation

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт implementation contract для server-side application layer и Background Worker.

> **The backend owns authorization, validation, transactions and domain commands. The worker executes durable typed intents after commit; it never invents Human intent, bypasses current-state checks or turns an at-least-once delivery model into duplicate professional/provider side effects.**

---

## 2. Backend layers

Canonical dependency direction:

```text
Next.js Server Action / Route Handler
            ↓
      Application Service
            ↓
       Domain Rules
            ↓
 Repository / Transaction Ports
            ↓
 PostgreSQL / Storage / Job Infrastructure
```

External providers sit behind adapters invoked from application/worker orchestration, not from domain entities.

## 3. Server Actions

Server Action responsibilities:

1. authenticate principal where required;
2. parse/validate request schema;
3. derive server-controlled context;
4. invoke named application command/query;
5. map typed result/error to transport response.

Server Action MUST NOT:

- duplicate domain transition logic;
- build raw SQL;
- expose provider credentials;
- call arbitrary filesystem path;
- call external provider inside authoritative DB transaction.

## 4. Command contracts

Every mutation command declares:

```text
principal/access class
input schema
target/resource scope
expectedVersion when needed
idempotency semantics
transaction boundary
domain invariants
outbox/audit events
result DTO
error codes
```

## 5. Query contracts

Queries are side-effect free and surface-specific.

Examples:

```text
GetPublicProfile
GetAdminFeedbackInbox
GetBuilderEligibility
GetTokenScopedQuestionnaire
```

GET/query code never mutates read/workflow/business state implicitly.

## 6. Authorization

Application service re-checks current resource ownership/state. Client-supplied profile IDs/flags cannot establish permission.

## 7. Transactions

Canonical write flow:

```text
BEGIN
  load current rows
  authorize + validate
  enforce expected version
  mutate authoritative state
  append history/revision as required
  append Audit
  append Outbox
COMMIT
```

Only after COMMIT may asynchronous provider/derived work start.

## 8. Outbox

Outbox row is written in same transaction as domain fact it represents.

Suggested fields:

```text
id
event_type
event_version
aggregate_type/id
profile_id?
payload_minimized
status
available_at
attempts
created_at
```

Payload contains IDs/safe event data, not entire private entity by default.

## 9. Job model

Suggested fields:

```text
id
job_type
job_version
payload
status
available_at
attempt_count
max_attempts
lease_owner?
lease_expires_at?
idempotency_key?
last_error_code?
created_at
updated_at
```

## 10. Job states

Baseline:

```text
QUEUED
RUNNING
SUCCEEDED
FAILED_RETRYABLE
FAILED_FINAL
CANCELLED
```

Provider-specific business outcomes remain in their module records rather than overloading generic job status.

## 11. Claiming jobs

Worker must claim jobs atomically using PostgreSQL locking/lease semantics such as `FOR UPDATE SKIP LOCKED` or equivalent implementation.

Two worker instances must not execute the same active lease concurrently unless handler is explicitly designed for it.

## 12. Lease recovery

If worker crashes, expired lease returns work to retryable queue according to handler semantics.

Lease expiry alone does NOT imply external provider action failed; handler must reconcile unknown outcome when side effect may have occurred.

## 13. Handler registry

Use explicit compile-time/runtime registry:

```text
JOB_TYPE → typed handler
```

Unknown job type/version becomes controlled failure/dead-letter state.

Never use `eval`, arbitrary module/function names or shell commands from job payload.

## 14. Job payloads

Payload contains stable IDs/snapshot IDs/config references.

Forbidden:

- ORM objects;
- raw OAuth/API secrets;
- executable code;
- unrestricted URL to fetch;
- entire mutable Source copy when snapshot/reference exists.

## 15. Current-state revalidation

Before a delayed technical action that depends on current state, worker reloads target.

Examples:

- Social schedule verifies account/rights/revocation;
- Builder/PDF job uses frozen snapshot;
- VOP safe action verifies finding/current generation;
- notification external delivery verifies recipient/channel state.

## 16. Immutable-snapshot jobs

When semantics were intentionally frozen, worker uses immutable snapshot rather than current Source:

```text
QuestionnaireRevision
BuilderGenerationSnapshot
SocialPublishSnapshot
BBSourceSnapshot
CastingAnalysis SourceSnapshot
```

Security/revocation checks may still override ability to externally expose/send.

## 17. Idempotency

Every handler declares one of:

```text
NATURALLY_IDEMPOTENT
IDEMPOTENT_WITH_KEY
REQUIRES_RECONCILIATION
NON_RETRYABLE
```

Automatic retries allowed only where classification permits.

## 18. Idempotency keys

Provider/side-effect key should bind to semantic intent, e.g.:

```text
NotificationDelivery ID
SocialPublishSnapshot ID
Questionnaire artifact revision+renderer profile
```

Same intent retry must not create duplicate provider object/artifact.

## 19. Retry policy

Retry metadata per job/provider class:

- retryable error codes;
- max attempts;
- exponential backoff;
- jitter;
- provider `Retry-After` where relevant;
- next allowed time.

No infinite retry.

## 20. UNKNOWN_OUTCOME

For external operations where request may have succeeded before timeout:

```text
attempt → UNKNOWN_OUTCOME
→ reconciliation job
→ confirmed success / confirmed safe failure / still unknown
```

Worker never maps timeout directly to safe retry by assumption.

## 21. Notification worker

Flow:

```text
Domain Outbox
→ NotificationIntent
→ recipient/materialization
→ In-App notification
→ per-channel Delivery
→ attempts/provider receipts
```

Provider failure never changes originating domain record.

## 22. Social worker

Flow:

```text
approved immutable PublishSnapshot
→ due schedule
→ current account/rights check
→ PublishAttempt
→ provider adapter
→ accepted/processing/published/failure/unknown
→ webhook/poll reconciliation
```

Worker cannot approve/create schedule by itself.

## 23. AI worker

AI job uses immutable task SourceSnapshot + prompt/schema version. Provider output is validated before persistence as AI Output/Draft/Analysis.

AI job has no direct arbitrary domain mutation tool.

## 24. Media worker

Consumes immutable source media reference and derivative profile. Uses resource/time bounds. Writes derivative metadata atomically after successful output validation.

Original never mutated.

## 25. PDF worker

Consumes immutable Revision/Snapshot → DocumentModel → PDF.

Dependencies such as QR must be READY/verified or generated/verified in deterministic pipeline. Requested QR failure cannot be silently omitted.

## 26. QR worker

Consumes server-approved canonical target/reference, generates SVG/PNG, decode-verifies exact target, then marks artifact READY.

No anonymous arbitrary URL payload.

## 27. Search/cache projectors

Consume domain events/generation changes and rebuild derived projections. Visibility is resolved server-side before publication into public index/cache.

## 28. VOP worker

Runs registered checks and registered Safe Actions only. Business-authority deny list is code-level hard constraint, not only UI config.

## 29. Analytics worker

Processes versioned accepted events into deterministic aggregates. Reaggregation/rebuild is idempotent and never mutates business Source.

## 30. Retention/cleanup jobs

Cleanup operates by explicit data class/age/reference checks. It never accepts generic table/path deletion instructions.

Examples safe:

- expired staged uploads;
- unreferenced derived QR;
- expired raw analytics;
- old temporary Builder sessions.

## 31. Provider adapters

Common adapter principles:

```text
normalized request
normalized response/status
normalized error classes
timeout
credential reference
provider request ID
```

Vendor SDK exceptions never leak directly to browser/domain.

## 32. Webhooks

Webhook route validates provider signature/event identity, persists/queues normalized provider event, then reconciliation handler updates only allowed operational state.

Expensive work should not require webhook request to stay open.

## 33. OAuth callbacks

Callback validates state/PKCE, exchanges token server-side, verifies account identity and writes encrypted credential/account state. It never sends token to frontend.

## 34. Error handling

Handlers record stable error code + safe diagnostics. Raw provider payload/secret/path stays internal/redacted.

Unexpected exception produces retry/final according to handler policy; do not `catch { return success; }`.

## 35. Dead-letter/final failures

Final failed jobs remain inspectable/retryable only through policy/Human operation if safe. VOP/operations can surface them.

## 36. Backpressure

Worker concurrency is configured by job class. CPU-heavy video/transcode does not monopolize lightweight notification/outbox dispatch.

## 37. Shutdown

Worker stops leasing new jobs, finishes/abandons leases safely, closes DB/provider clients. It does not mark in-flight external calls failed without reconciliation semantics.

## 38. Observability

Each job/attempt has:

```text
job/attempt id
correlation id
type/version
duration
attempt number
status/error code
provider request id if relevant
```

No raw secrets/private content by default.

## 39. Testing

Integration tests with real PostgreSQL + provider fakes prove:

- transaction/outbox atomicity;
- leasing/concurrency;
- crash/retry;
- idempotency;
- unknown-outcome reconciliation;
- stale/current-state checks;
- webhook dedupe;
- provider failure non-corruption.

## 40. Anti-patterns

Forbidden:

1. Business logic duplicated in Server Action and worker.
2. External API called inside DB transaction.
3. Job payload contains OAuth token.
4. Job payload names arbitrary function to execute.
5. Lease timeout automatically means provider failed.
6. Infinite retries.
7. Worker reads mutable Draft when approved snapshot exists.
8. Notification failure rolls back Feedback.
9. Social worker self-approves content.
10. AI worker writes Profile fact directly.
11. Cleanup job accepts arbitrary filesystem path/table.
12. Search projector indexes hidden data then relies on query filter only.
13. Worker shutdown marks unknown external side effect failed.

## 41. Acceptance criteria

`AC-BE-001` Server Actions are thin validated/authenticated adapters.  
`AC-BE-002` Application services own transaction and domain command boundaries.  
`AC-BE-003` Audit/Outbox are committed atomically with business Source where required.  
`AC-BE-004` Durable jobs use typed/versioned payloads and persistent status.  
`AC-BE-005` Job claiming/lease recovery is concurrency-safe.  
`AC-BE-006` Every handler declares retry/idempotency/reconciliation semantics.  
`AC-BE-007` External unknown outcomes reconcile before retry.  
`AC-BE-008` Immutable revisions/snapshots are used for delayed historical/publishing work.  
`AC-BE-009` Workers reload current rights/revocation where security requires it.  
`AC-BE-010` Provider adapters normalize status/errors and hide credentials/vendor internals.  
`AC-BE-011` AI/VOP/cleanup/projector workers cannot exceed explicit authority.  
`AC-BE-012` Real PostgreSQL/provider-fake integration tests prove durability, idempotency and failure behavior.

---

## 42. Финальная доктрина

> **The backend establishes authority and commits facts; the worker executes durable consequences. Typed jobs, immutable snapshots, current-state security checks, bounded retries and provider reconciliation make at-least-once processing safe without giving asynchronous code a new source of professional or business intent.**
