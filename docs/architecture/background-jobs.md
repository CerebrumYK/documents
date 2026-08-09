# BACKGROUND JOBS ARCHITECTURE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная архитектура очередей, workers, outbox, retries, scheduling и восстановления фоновых операций

**Целевой файл:** `docs/architecture/background-jobs.md`  
**Документ:** DOC-076  
**Статус:** ✅ Completed  
**Тип:** Architecture / Background Jobs / Workers / Outbox / Scheduling

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/business-rules.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/forms-and-validation.md`
- `docs/ux/ui-states.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/components.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`
- `docs/architecture/media.md`
- `docs/architecture/pdf.md`

---

# 1. Назначение документа

Настоящий документ определяет архитектуру всех фоновых и отложенных операций продукта.

Он фиксирует:

1. какие операции должны выполняться asynchronously;
2. что такое durable job;
3. как создаётся job;
4. как работает transactional outbox;
5. как Web Runtime и Worker разделяют ответственность;
6. lifecycle job;
7. leasing/claiming;
8. retries;
9. backoff;
10. idempotency;
11. duplicate suppression;
12. scheduling;
13. quiet hours;
14. job payload versioning;
15. concurrency;
16. worker crash recovery;
17. failure classification;
18. dead-letter/final failure semantics;
19. cancellation;
20. priority;
21. dependency chains;
22. observability;
23. security;
24. privacy;
25. deployment;
26. schema/upgrade compatibility;
27. retention/cleanup;
28. testing.

---

# 2. Главный принцип

> **Если система приняла обязательство выполнить фоновую работу, это обязательство не должно исчезнуть из-за рестарта процесса, временного сбоя провайдера или повторной доставки одного и того же события.**

Canonical:

```text
AUTHORITATIVE COMMIT
        ↓
DURABLE INTENT
        ↓
QUEUE / JOB
        ↓
WORKER
        ↓
IDEMPOTENT EXECUTION
        ↓
RESULT / RETRY / FINAL FAILURE
```

---

# 3. Идентификаторы

Используются:

```text
JOB-*
WORKER-*
OUTBOX-*
SCHED-*
RETRY-*
IDEMP-*
```

Invariants:

```text
JOB-INV-*
```

---

# 4. Что считается Background Job

Background Job — durable unit of technical work, выполнение которой:

- может занять заметное время;
- может временно завершиться ошибкой;
- может безопасно повторяться;
- не должна блокировать HTTP request;
- должна переживать process restart;
- либо должна выполняться в заданный момент времени.

---

# 5. Основные категории jobs

Минимальный каталог:

```text
MEDIA_PROCESS
MEDIA_DERIVATIVE
EMOTIONAL_GRID_RENDER
PDF_GENERATE
QR_GENERATE
QR_VALIDATE
LINK_HEALTH_CHECK
SEARCH_REINDEX
SEARCH_FULL_REBUILD
CACHE_REBUILD
NOTIFICATION_DELIVER
SOCIAL_PUBLISH
AI_CASTING_ANALYSIS
AI_BB_DRAFT
AI_THEME_PROPOSAL
VOP_CHECK
THEME_ACTIVATION
THEME_EXPIRATION
BUILDER_SESSION_EXPIRATION
RETENTION_CLEANUP
STORAGE_RECONCILIATION
```

---

# 6. JOB-INV-001 — Durable Acceptance

После того как пользователь/система получил подтверждение, что background action принят, job intent MUST быть сохранён durably.

Нельзя:

```text
HTTP success
→ setTimeout(...)
→ process crashes
→ work lost
```

---

# 7. JOB-INV-002 — No In-Memory-Only Critical Queue

Критические фоновые операции MUST NOT существовать только:

```text
in-memory array
Node.js timer
process-local event emitter
```

в production.

---

# 8. JOB-INV-003 — Business Truth Before Async Side Effect

Job обычно создаётся **после или вместе с commit authoritative business state**.

Example:

```text
Feedback committed
→ Notification job
```

не наоборот.

---

# 9. JOB-INV-004 — Job Is Not Business Authority

Job status:

```text
SUCCEEDED
```

не означает автоматически:

```text
Opportunity = BOOKED
Questionnaire = PUBLISHED
```

Business state принадлежит owning Domain.

---

# 10. JOB-INV-005 — Semantic Decisions Are Not Background Technical Work

Worker не имеет права самостоятельно принимать high-impact professional decisions.

Запрещено:

```text
AI worker → set Primary Close-Up
Scheduler → Opportunity BOOKED
VOP worker → publish Questionnaire
Night job → enable public Contact
```

если Human authority обязательна.

---

# 11. Допустимые automatic jobs

AUTO-4 examples:

```text
thumbnail regeneration
PDF rendering from approved Revision
QR generation
QR decode validation
search reindex
cache rebuild
link health check
notification retry
approved scheduled social publish
temporary Theme expiry
Builder session expiry
```

---

# 12. Human-Authorized Deferred Jobs

Некоторые действия могут выполняться позже автоматически, если Human заранее утвердил точное действие.

Example:

```text
Admin schedules SocialPost for 18:00
```

Human decision происходит при scheduling.

Worker выполняет утверждённый delivery.

---

# 13. Architecture Baseline

Canonical initial architecture:

```text
PostgreSQL
+
Durable Job Store
+
Transactional Outbox
+
Worker Runtime
+
Scheduler
```

---

# 14. Queue Technology

DOC-076 не фиксирует конкретную библиотеку.

Допустимо:

```text
PostgreSQL-backed queue
Redis-backed queue
dedicated message broker
```

при выполнении всех invariants.

---

# 15. Preferred Initial Strategy

Для текущего масштаба SHOULD предпочесть:

> PostgreSQL-backed durable queue/outbox

если она удовлетворяет требованиям.

Причины:

```text
fewer infrastructure dependencies
shared transactional boundary
simpler backup
simpler self-hosted operations
```

---

# 16. Когда Redis/Broker оправдан

Только при подтверждённой необходимости:

```text
high throughput
large worker fleet
advanced delayed delivery
complex queue isolation
measured DB queue bottleneck
```

---

# 17. Transactional Outbox

Для domain commit → async work используется:

```text
Transactional Outbox
```

или эквивалентная reliable delivery pattern.

---

# 18. Outbox Purpose

Решает проблему:

```text
DB transaction committed
→ process crashes before queue publish
→ side effect permanently lost
```

---

# 19. Canonical Outbox Flow

```text
BEGIN TRANSACTION

1. mutate authoritative entity
2. append Audit if required
3. append Outbox record

COMMIT

↓
Outbox Dispatcher
↓
Job creation/execution
```

---

# 20. Outbox Record

Conceptually:

```text
id
event_type
aggregate_type
aggregate_id
payload
payload_version
created_at
available_at
processed_at
attempts
correlation_id
```

Exact DB schema later.

---

# 21. Outbox Payload

SHOULD contain only data required to route/execute event.

Avoid storing huge sensitive snapshots if exact source reference is sufficient.

---

# 22. Outbox Immutability

Once committed, business event meaning SHOULD NOT be mutated.

Processing metadata may change.

---

# 23. Outbox Processing

Dispatcher:

```text
find unprocessed
claim
dispatch/create job
mark processed
```

must tolerate restart and duplicate attempt.

---

# 24. Exactly-Once Myth

Distributed systems generally cannot rely on true universal exactly-once execution.

Canonical target:

> **at-least-once delivery + idempotent consumers**

---

# 25. JOB-INV-006 — Duplicate Delivery Safe

Любой critical consumer MUST быть способен безопасно получить одно и то же событие несколько раз.

---

# 26. Job Lifecycle

Canonical:

```text
QUEUED
  ↓
CLAIMED
  ↓
RUNNING
  ↓
SUCCEEDED
```

Failure branch:

```text
RUNNING
  ↓
FAILED_RETRYABLE
  ↓
RETRY_SCHEDULED
  ↓
QUEUED
```

Terminal:

```text
FAILED_FINAL
CANCELLED
```

---

# 27. Simplified Persisted Status

Physical DB MAY use:

```text
QUEUED
RUNNING
SUCCEEDED
FAILED
CANCELLED
```

с отдельными:

```text
attempt_count
next_attempt_at
terminal
```

если semantics остаются ясными.

---

# 28. QUEUED

Job durably exists and ждёт eligible worker.

---

# 29. CLAIMED / RUNNING

Worker получил exclusive execution lease.

---

# 30. SUCCEEDED

Technical operation completed and expected result persisted.

---

# 31. FAILED_RETRYABLE

Transient problem.

Examples:

```text
provider timeout
temporary DNS error
temporary storage busy
remote 503
```

---

# 32. FAILED_FINAL

System determined:

```text
retry budget exhausted
or
failure permanent
```

---

# 33. CANCELLED

Job deliberately prevented from future execution.

Cancellation semantics must be explicit.

---

# 34. Claiming

Worker MUST atomically claim eligible job.

Two workers MUST NOT считать один job exclusively owned одновременно.

---

# 35. Claim Mechanism

Can use:

```text
SELECT ... FOR UPDATE SKIP LOCKED
atomic status update
queue-native lease
```

depending backend.

---

# 36. Job Lease

RUNNING job SHOULD иметь:

```text
locked_by
locked_at
lease_until / heartbeat
```

or equivalent.

---

# 37. Why Lease

Если worker dies:

```text
RUNNING
```

не должен остаться навсегда.

---

# 38. Worker Heartbeat

Long jobs SHOULD renew lease/heartbeat where needed.

---

# 39. Expired Lease

Another worker MAY reclaim after safe timeout.

Handler must remain idempotent because original worker outcome may be uncertain.

---

# 40. Short Job

Для very short operations DB lock/claim может быть достаточен без frequent heartbeat.

---

# 41. Retry Classification

Failures MUST классифицироваться.

---

# 42. Retryable Error

Examples:

```text
network timeout
HTTP 429
HTTP 503
temporary provider unavailable
worker crash
temporary storage I/O issue
```

---

# 43. Permanent Error

Examples:

```text
invalid canonical URL
unsupported media format
missing required source that cannot be resolved
revoked access
malformed immutable job payload
invalid provider credentials until operator action
```

Некоторые могут стать retryable только после external correction.

---

# 44. Unknown Outcome

Особый класс:

```text
provider call may have succeeded
but response lost
```

Requires reconciliation/idempotency rather than blind resend.

Especially:

```text
WhatsApp
Email
Social publish
```

---

# 45. Retry Policy

Each job type defines:

```text
max_attempts
initial_delay
backoff strategy
max_delay
retryable errors
non-retryable errors
```

---

# 46. Exponential Backoff

Recommended default for external transient failures:

```text
delay_n = min(max_delay, base × 2^(n-1))
```

plus jitter where useful.

Exact values configurable.

---

# 47. Jitter

SHOULD be used for many jobs hitting same external provider to avoid synchronized retries.

---

# 48. Immediate Retry

Allowed only for narrow technical failures likely to resolve instantly.

Do not loop aggressively.

---

# 49. Retry Budget

No infinite retries by default.

---

# 50. Retry Exhaustion

After max attempts:

```text
FAILED_FINAL
```

and generate:

```text
Admin diagnostic
VOP Observation where meaningful
System Status degradation if relevant
```

---

# 51. Manual Retry

Admin MAY trigger retry for failed jobs where safe.

Manual retry MUST preserve same semantic idempotency protections.

---

# 52. Reset Attempt Count

Manual retry MAY create:

```text
new execution attempt record
```

while preserving prior failure history.

Do not erase evidence.

---

# 53. Idempotency

Every side-effecting job MUST define idempotency key.

---

# 54. Generic Idempotency Key

Conceptually:

```text
job_type
+
business/source identity
+
operation version
```

---

# 55. PDF Idempotency

```text
QuestionnaireRevision
+
locale
+
template version
+
renderer version
```

---

# 56. QR Idempotency

```text
canonical URL
+
QR settings version
```

---

# 57. Media Derivative Idempotency

```text
MediaAsset checksum
+
transform specification
+
processor version
```

---

# 58. Notification Idempotency

```text
notification_delivery_id
```

plus provider idempotency key where supported.

---

# 59. Social Publish Idempotency

```text
SocialPost
+
target account/platform
+
approved publication intent/version
```

---

# 60. Search Reindex Idempotency

Reindexing same source state produces equivalent index representation.

---

# 61. Theme Activation Idempotency

Activating already-active exact ThemeRevision must not create harmful duplicates.

---

# 62. Project Draft From Booked Idempotency

Repeated command/job must not create multiple Project Drafts for same approved source action.

---

# 63. Idempotency Persistence

Do not store idempotency only in worker RAM.

It must survive restart.

---

# 64. Job Identity vs Idempotency Identity

Different jobs MAY share same semantic idempotency key if retries/requeues represent same operation.

---

# 65. Job Payload

Each job MUST be explicit typed/versioned data.

---

# 66. Recommended Job Envelope

```text
{
  job_type,
  payload_version,
  payload,
  idempotency_key,
  correlation_id,
  priority,
  scheduled_for
}
```

---

# 67. Payload References Preferred

Prefer:

```text
revision_id
asset_id
snapshot_id
notification_delivery_id
```

over copying entire entity state.

---

# 68. Exception — Immutable Snapshot Payload

Where worker must execute from exact frozen input, reference exact immutable Snapshot/Revision.

---

# 69. No `"current"` Ambiguity

Bad:

```text
{ questionnaire_id: 42 }
```

if worker later resolves whichever Revision happens to be current.

Good:

```text
{ questionnaire_revision_id: "..." }
```

---

# 70. Payload Schema Version

Required for durable jobs that can survive deployment.

Example:

```text
payload_version = 2
```

---

# 71. Worker Backward Compatibility

New deployment MUST either:

1. still understand queued older payload versions;
2. migrate pending jobs;
3. safely invalidate/recreate them.

---

# 72. JOB-INV-007 — Deployments Cannot Strand Jobs

Application upgrade MUST NOT leave durable accepted jobs permanently unreadable.

---

# 73. Job Handler Registry

Canonical concept:

```text
job_type
→ handler
```

Unknown type:

```text
FAILED_FINAL / operator intervention
```

not arbitrary execution.

---

# 74. Handler Contract

Every handler defines:

```text
input schema
required dependencies
idempotency semantics
timeout
retry classification
result persistence
observability
```

---

# 75. Job Timeout

Every handler SHOULD have bounded maximum execution time or lease policy.

---

# 76. AI Timeout

AI provider request cannot run forever.

---

# 77. PDF Timeout

Renderer process should have bounded timeout.

---

# 78. Media Timeout

Large video processing MAY require longer configurable timeout.

---

# 79. Timeout Is Not Always Permanent Failure

Usually retryable subject to attempts.

---

# 80. Scheduler

Scheduler handles future-time operations.

---

# 81. Scheduler Responsibilities

Examples:

```text
SocialPost publish time
Notification quiet-hour release
Theme temporary activation
Theme expiration
Builder session expiration
retention cleanup
periodic link check
storage integrity check
```

---

# 82. Scheduler Architecture

Scheduling intent MUST be persisted.

Do not rely solely on:

```text
setTimeout
cron callback in one process without durable state
```

for business-relevant scheduled actions.

---

# 83. `scheduled_for`

Job may exist immediately with:

```text
scheduled_for > now
```

and becomes claimable later.

---

# 84. Recurring Jobs

Recurring tasks SHOULD be represented by durable schedule definition or deterministic scheduler logic.

---

# 85. Scheduler Duplicate Safety

Multiple Scheduler instances must not create duplicate semantic jobs for same schedule tick.

Use:

```text
unique schedule occurrence key
distributed lock
idempotency key
```

---

# 86. Quiet Hours

Notification flow:

```text
Notification ready
 ↓
Policy detects quiet hours
 ↓
Delivery scheduled_for next allowed window
```

It is not a failure.

---

# 87. Quiet Hours and Priority

Critical notifications MAY have explicit override policy.

Default notification remains quiet-hours aware.

---

# 88. Theme Scheduled Activation

If Admin has explicitly approved exact Revision + time:

```text
ThemeRevision
→ scheduled activation job
```

Worker may execute AUTO-4 at approved time.

---

# 89. Theme Expiration

Temporary Theme expiry is deterministic AUTO-4.

---

# 90. Social Scheduled Publishing

Human decision locks:

```text
post content/version
targets
scheduled time
```

Worker publishes exact approved version.

---

# 91. Social Draft Change After Scheduling

If content changes after scheduling:

system MUST define one of:

```text
scheduled version remains frozen
or
schedule invalidated and requires re-approval
```

Preferred:

> scheduled publish binds to an approved immutable/publishable post version.

---

# 92. No Silent New Content Publishing

Editing SocialPost after approval must not cause scheduled worker to publish unreviewed newer content.

---

# 93. Builder Session Expiration

Scheduler/request-level check marks session EXPIRED after `expires_at`.

Idempotent.

---

# 94. Retention Cleanup

Cleanup jobs operate only according to retention policy.

---

# 95. Cleanup Cannot Guess

Worker MUST NOT decide professional retention from filesystem age alone.

---

# 96. Priority

Jobs MAY have priority classes.

Recommended:

```text
CRITICAL
HIGH
NORMAL
LOW
MAINTENANCE
```

---

# 97. Critical Examples

```text
security revocation propagation
critical public artifact recovery
urgent professional notification
```

---

# 98. High Examples

```text
Feedback notification
current Questionnaire PDF generation
P0 media derivative
```

---

# 99. Normal Examples

```text
standard media derivative
AI draft
search reindex
```

---

# 100. Low/Maintenance

```text
historical derivative rebuild
cleanup
full integrity scan
analytics aggregation
```

---

# 101. Priority Does Not Override Security

A HIGH job without permission cannot execute.

---

# 102. Queue Starvation

Worker scheduling SHOULD prevent permanent starvation of low-priority maintenance jobs.

---

# 103. Concurrency Limits

Job types MAY define max concurrency.

---

# 104. AI Concurrency

Protect provider quotas/cost.

---

# 105. PDF Concurrency

Protect CPU/RAM.

---

# 106. Video Processing Concurrency

Potentially low on current self-hosted server due CPU/memory constraints.

---

# 107. Notification Concurrency

Respect provider rate limits.

---

# 108. Per-Entity Serialization

Some jobs SHOULD serialize by entity.

Example:

```text
two renders for same GridRevision
```

can deduplicate.

---

# 109. Latest-Wins Jobs

Certain derived updates may collapse superseded jobs.

Example:

```text
Search reindex for Profile v10
Search reindex for Profile v11
```

worker MAY skip v10 if v11 safely supersedes it.

---

# 110. Latest-Wins Restriction

Never apply to:

```text
historical PDF
Notification delivery
Social publish
Audit
```

where each intent has distinct meaning.

---

# 111. Coalescing

Allowed for safely derivable operations:

```text
cache rebuild
search reindex
read model refresh
```

---

# 112. Dependency Chains

Jobs MAY depend on prerequisites.

Example:

```text
Media Original Stored
→ PDF derivative generation
→ Questionnaire PDF generation
```

---

# 113. Preferred Coordination

Do not create fragile deeply nested arbitrary job chains.

Use:

```text
authoritative state/readiness
domain events
explicit prerequisites
```

---

# 114. Example Emotional Grid

```text
Grid frozen
 ↓
Render master
 ↓
Generate WEB/PDF/THUMB
 ↓
validate required artifacts
 ↓
mark Grid finalized
```

May be one orchestrated job or controlled sub-jobs.

---

# 115. Partial Dependency Failure

If `THUMB` fails but MASTER/WEB/PDF succeed:

policy may mark:

```text
FINALIZED + partial derivative issue
```

only if thumbnail is non-critical.

---

# 116. Required Dependency

If PDF composite required and fails:

Grid cannot reach fully ready state.

---

# 117. Job Orchestrator

A specific application-level orchestrator MAY coordinate job tree.

It must not duplicate Domain state machine.

---

# 118. Saga Complexity

Full distributed Saga framework is not required for current modular-monolith architecture.

---

# 119. Compensation

Needed only where external side effect cannot be rolled back transactionally.

Example:

```text
Social published externally
internal persistence fails
```

requires reconciliation, not DB rollback illusion.

---

# 120. External Side Effect Pattern

Canonical:

```text
persist intent
→ execute provider
→ persist provider result
```

---

# 121. Provider Call Before Result Commit

If provider says success but DB result persistence fails:

retry must reconcile to avoid duplicate external side effect.

---

# 122. Provider Idempotency

Use provider-supported idempotency key whenever available.

---

# 123. Provider External ID

Persist:

```text
provider_message_id
provider_post_id
```

when returned.

Useful for reconciliation.

---

# 124. Unknown Outcome Reconciliation

For unknown external outcome:

```text
query provider status
or
use idempotency key
```

before blind resend where provider supports it.

---

# 125. Notification Retry

Failure never mutates Feedback/Casting/Opportunity truth.

---

# 126. Social Retry

Retry only failed target.

Do not republish already successful target.

---

# 127. AI Retry

AI retry SHOULD generally create/continue same generation request carefully.

Repeated stochastic output may differ.

---

# 128. AI Retry Semantics

If request failed before valid output:

retry same job intent.

If valid output persisted:

do not regenerate automatically just because response to UI was lost.

---

# 129. AI Idempotency Boundary

Persist generation request identity before provider call where useful.

---

# 130. AI Cost Protection

Duplicate concurrent AI jobs for same explicit request SHOULD be deduplicated.

---

# 131. AI Regenerate Is New Intent

User clicking:

```text
Regenerate
```

creates new generation/AIDraft intent, not retry of old successful job.

---

# 132. PDF Regenerate

May be either:

```text
retry same artifact intent
```

or explicit:

```text
new renderer version artifact
```

depending requested operation.

---

# 133. Cancellation

Some jobs MAY be cancellable before execution.

---

# 134. Safe Cancellation Examples

```text
queued low-priority reindex
scheduled social post before publishing
future Theme activation
```

subject to business state.

---

# 135. Unsafe Cancellation

Cannot simply cancel:

```text
provider action already sent
```

and pretend nothing happened.

---

# 136. Cancellation Race

Worker must revalidate cancellation/desired state before side effect where applicable.

---

# 137. Scheduled Social Cancellation

Canonical:

```text
SCHEDULED
→ Admin Cancel
→ schedule state CANCELLED
→ queued execution sees cancellation and exits safely
```

---

# 138. Job Staleness

Before execution, handler SHOULD check if intent remains valid.

---

# 139. Stale Job Example — Search

Profile v8 reindex job sees Profile already v10.

May skip/rebuild current v10 depending semantics.

---

# 140. Stale Job Example — AI

Casting source version changed before analysis begins.

Prefer:

```text
cancel/stale old requested analysis
or
execute exact old snapshot as historical analysis
```

depending command semantics.

For explicit Casting Analysis Revision, exact Snapshot makes old analysis historically valid but UI must mark stale relative to current.

---

# 141. Stale Job Example — Theme

Scheduled activation of ThemeRevision that has been revoked/archived must revalidate before activation.

---

# 142. Security Revalidation Before Side Effect

Every delayed high-impact job must revalidate:

```text
resource still exists
authorization intent still valid where applicable
access not revoked
scheduled version is still approved
```

---

# 143. Human Authorization Expiry

Long-delayed operation may need reauthorization if product/security policy requires.

Exact security docs later.

---

# 144. Job Result

Handler result SHOULD be persisted in owning technical/business record.

Examples:

```text
QuestionnaireArtifact READY
MediaDerivative READY
NotificationDelivery SENT
SocialPublishAttempt PUBLISHED
AIDraft GENERATED
```

---

# 145. Job Output Is Not Arbitrary JSON Dump

Persist structured domain/infrastructure result.

---

# 146. Failure Details

Store stable:

```text
error_code
safe_message
attempt
timestamp
```

Optionally internal diagnostic context.

---

# 147. Stack Trace

Belongs logs/error reporting, not ordinary end-user domain record.

---

# 148. Failure Code Taxonomy

Recommended generic:

```text
JOB_TIMEOUT
JOB_DEPENDENCY_UNAVAILABLE
JOB_PAYLOAD_INVALID
JOB_SOURCE_MISSING
JOB_SOURCE_STALE
JOB_PROVIDER_RATE_LIMIT
JOB_PROVIDER_AUTH_FAILED
JOB_PROVIDER_TEMPORARY
JOB_PROVIDER_PERMANENT
JOB_STORAGE_FAILED
JOB_RENDER_FAILED
JOB_CANCELLED
```

Capability-specific codes may refine.

---

# 149. Dead-Letter Concept

`FAILED_FINAL` jobs form logical dead-letter/failure queue.

Implementation may use a separate table/queue or status.

---

# 150. Dead-Letter Requirements

Admin must be able to inspect:

```text
job type
target
attempts
last error
first/last failure
retry eligibility
correlation ID
```

---

# 151. No Automatic Silent Deletion of Failed Jobs

Failure history retained according to operational retention.

---

# 152. Recovery Workflow

Admin/VOP may:

```text
Retry
Resolve underlying issue
Cancel
Acknowledge
```

depending job.

---

# 153. VOP Integration

VOP SHOULD create Observation for meaningful:

```text
repeated critical failures
stuck queue
failed PDF
failed notification
media processing issue
provider outage
```

---

# 154. VOP Noise Control

One failed low-priority thumbnail retry should not necessarily generate critical dashboard alert immediately.

Use severity/attempt threshold.

---

# 155. VOP AUTO-4

May retry deterministic safe job according to policy.

---

# 156. VOP Cannot Retry Indefinitely

Same retry budget/operational policy applies.

---

# 157. Worker Process Model

Worker runtime can host multiple handlers.

Conceptually:

```text
worker
├─ media
├─ documents
├─ notifications
├─ AI
├─ search
└─ maintenance
```

---

# 158. Separate Worker Pools

Future MAY split by resource profile:

```text
cpu-heavy
network-heavy
AI
notification
```

without changing job contracts.

---

# 159. Current Deployment

At initial scale:

```text
one or few worker processes
same host
shared PostgreSQL
shared Media Storage
```

acceptable.

---

# 160. Resource Isolation

CPU-heavy jobs SHOULD not starve:

```text
Web Runtime
database
critical notification
```

---

# 161. Video Processing

May need explicit low concurrency on current hardware.

---

# 162. PDF Rendering

May spawn headless/browser process.

Must have:

```text
memory limit
timeout
cleanup
```

---

# 163. Worker Temp Files

Use dedicated TEMPORARY zone.

Clean after success/failure.

---

# 164. Orphan Temp Files

Maintenance job handles leftovers after crash.

---

# 165. Worker Permissions

Worker gets only required system credentials/filesystem access.

---

# 166. External Credentials

Provided through Secret Provider/configuration, not job payload.

---

# 167. JOB-INV-008 — No Secrets in Job Payload

Do not persist:

```text
API key
OAuth access token
database password
session cookie
```

inside durable payload.

---

# 168. Private Data in Jobs

May contain private entity references.

Full content should be minimized.

---

# 169. AI Job Privacy

Prefer payload:

```text
analysis_request_id
```

then worker loads exact internal Snapshot.

Not full Casting source duplicated into generic queue payload if avoidable.

---

# 170. Notification Payload Privacy

External channel payload prepared according to minimal-notification policy.

---

# 171. Logging Privacy

Worker logs MUST NOT contain unnecessary:

```text
Feedback body
Casting documents
Contact secrets
OAuth tokens
AI key
```

---

# 172. Correlation

Every important job SHOULD carry:

```text
correlation_id
```

linking:

```text
request
business command
outbox
job
provider call
result
audit/error
```

---

# 173. Job Parentage

Optional:

```text
parent_job_id
source_event_id
```

for diagnostics.

---

# 174. Job Observability

Minimum metrics:

```text
queued count
running count
oldest queued age
success rate
failure rate
retry count
execution latency
queue latency
final failures
```

---

# 175. Per-Type Metrics

Especially:

```text
PDF
Media
Notification
AI
Social
```

---

# 176. Queue Age

Oldest pending HIGH job exceeding threshold should degrade system status.

---

# 177. Worker Health

Health check distinguishes:

```text
worker process alive
worker actively consuming
queue backlog healthy
```

---

# 178. False Healthy Worker

Process existing but unable to claim jobs is not fully healthy.

---

# 179. Provider Health

Separate from worker health.

Example:

```text
Worker healthy
WhatsApp provider degraded
```

---

# 180. Job History

Admin should be able to see recent jobs tied to relevant domain entity where useful.

Example Questionnaire:

```text
PDF generation history
```

---

# 181. Operational Dashboard

Should prioritize:

```text
failed critical
repeated retry
stuck jobs
queue backlog
provider auth failure
```

---

# 182. Retention of Job Records

Categories:

```text
active
recent success
failed final
historically important execution
maintenance
```

Exact retention durations DOC-094/operations.

---

# 183. Successful Job Cleanup

Old routine success records MAY be compacted/deleted after retention.

---

# 184. Failed Job Retention

FAILED_FINAL SHOULD be retained longer for diagnostics.

---

# 185. Audit vs Job History

Job history:

```text
technical execution
```

Audit:

```text
meaningful business/admin action
```

They are separate.

---

# 186. Scheduled Business Intent

Scheduling an externally visible action SHOULD be audited.

Examples:

```text
SocialPost scheduled
Temporary Theme scheduled
```

---

# 187. Worker Execution Usually Not Business Audit

Routine:

```text
thumbnail regenerated
```

may be operational log/job history only.

---

# 188. Notification Sent

May remain NotificationDelivery history rather than AuditEvent unless required.

---

# 189. Schema Migrations

Queue/job tables must use explicit DB migrations.

---

# 190. Deploy Order

Deployment SHOULD consider queued jobs:

```text
DB migration
→ backward-compatible application/worker
→ deploy web/worker
→ remove old compatibility later
```

---

# 191. Rolling Upgrade

If Web vN+1 queues jobs before Worker vN+1 deployed:

old Worker must either understand new payload or jobs remain safely queued until compatible Worker exists.

---

# 192. Feature Rollout

New job type SHOULD not be emitted until compatible handler is deployed.

---

# 193. Handler Removal

Before deleting handler:

```text
no pending jobs
no schedule producing type
no outbox records awaiting dispatch
```

---

# 194. Poison Job

Malformed job repeatedly failing should become FAILED_FINAL quickly.

Do not burn endless retries.

---

# 195. Backpressure

Queue system SHOULD support controlled backpressure.

---

# 196. High Backlog

When resource-intensive jobs backlog:

- keep accepting critical business transactions if safe;
- expose delayed processing state;
- throttle optional generation if needed.

---

# 197. AI Backlog

Does not block Profile/Casting manual editing.

---

# 198. PDF Backlog

Questionnaire Revision remains persisted; UI shows processing if PDF generation pending.

---

# 199. Notification Backlog

Feedback remains persisted; Admin sees delivery delay.

---

# 200. Media Backlog

Uploads remain STORED/PROCESSING, not falsely READY.

---

# 201. Rate Limits

Provider-specific rate limiting belongs adapter/job policy.

---

# 202. HTTP 429

Typically retryable using:

```text
Retry-After
```

where provided.

---

# 203. Global Provider Throttling

System MAY pause related queue if provider clearly unavailable/rate-limited.

---

# 204. Other Job Types Continue

WhatsApp outage must not stop PDF/media workers.

---

# 205. Queue Isolation

Logical queues/types SHOULD allow failure/load isolation.

---

# 206. Initial Physical Queue

May still be one table if type/priority/query indexes provide adequate isolation.

---

# 207. Dependency on PostgreSQL

If PostgreSQL unavailable:

Web core cannot persist;
Worker cannot safely progress authoritative job results.

Workers SHOULD fail/stop safely rather than continue side effects without persistence ability.

---

# 208. JOB-INV-009 — No External Side Effect Without Result Persistence Strategy

Worker should avoid making irreversible external calls when it cannot record outcome.

---

# 209. Provider Call Transaction

Do not keep PostgreSQL transaction open across slow external API call by default.

---

# 210. Canonical External Execution

```text
claim job
↓
load state
↓
mark/start attempt
↓
commit
↓
external provider call
↓
persist result in new short transaction
```

---

# 211. Crash Window

Crash after provider success but before result commit is handled through idempotency/reconciliation.

---

# 212. Internal Deterministic Job

For pure internal file generation, safer atomic temp-output strategy can reduce ambiguous outcomes.

---

# 213. PDF Example

```text
Job claimed
→ temp PDF
→ validate
→ store final artifact
→ DB READY
```

If DB commit fails after final binary:

reconciliation detects orphan/reuses checksum/idempotency identity.

---

# 214. Media Derivative Example

Same principle.

---

# 215. Notification Example

```text
Delivery PENDING
→ job
→ provider
→ SENT/FAILED
```

Source Feedback unaffected.

---

# 216. AI Example

```text
AnalysisRequest
→ job
→ provider
→ validate
→ AnalysisRevision
```

If validation fails:

```text
job fails / AI output rejected
```

No human-confirmed requirement created.

---

# 217. VOP Periodic Checks

Scheduled VOP/system checks SHOULD generate Observations from deterministic evidence.

---

# 218. Periodic Link Health

Link health check:

```text
ProfessionalLink
→ network check
→ health state
```

Failure does not archive Link.

---

# 219. Link Health Scheduling

May use adaptive frequency later.

Current architecture only requires durable scheduling capability.

---

# 220. Search Reindex

Triggered by domain event.

May coalesce multiple updates.

---

# 221. Cache Rebuild

May be skipped if newer source version already has rebuilt cache.

---

# 222. Dependency Revalidation

Worker must distinguish:

```text
exact historical operation
```

versus:

```text
current-derived operation
```

---

# 223. Historical Operation

Example:

```text
Generate PDF for QuestionnaireRevision R5
```

Always uses R5 even if R6 exists.

---

# 224. Current-Derived Operation

Example:

```text
Reindex Public Profile
```

may use newest committed state and skip superseded job.

---

# 225. Job Semantic Class

Recommended conceptual metadata:

```text
IMMUTABLE_SOURCE
CURRENT_STATE_DERIVED
EXTERNAL_SIDE_EFFECT
SCHEDULED_PREAUTHORIZED
MAINTENANCE
```

---

# 226. IMMUTABLE_SOURCE

Examples:

```text
PDF from Revision
Grid render from GridRevision
```

---

# 227. CURRENT_STATE_DERIVED

Examples:

```text
search reindex
cache rebuild
```

---

# 228. EXTERNAL_SIDE_EFFECT

Examples:

```text
WhatsApp
Email
Social publish
```

highest idempotency/reconciliation importance.

---

# 229. SCHEDULED_PREAUTHORIZED

Examples:

```text
Theme activation
Social publish
```

---

# 230. MAINTENANCE

Examples:

```text
retention cleanup
storage reconciliation
integrity scan
```

---

# 231. User-Visible Job States

UI should not expose raw queue jargon unnecessarily.

Map to:

```text
В очереди
Обработка
Готово
Ошибка
Повторная попытка
```

according to context.

---

# 232. Unknown State

If execution outcome temporarily unknown:

show:

```text
Проверяем результат
```

where appropriate rather than false failure/success.

---

# 233. Job Progress

Only show percentage when real measurable progress exists.

---

# 234. Media Batch Progress

Can be actual:

```text
8 / 12 assets processed
```

---

# 235. AI Generation Progress

Usually indeterminate, unless provider supplies meaningful phase/progress.

---

# 236. PDF Progress

Often:

```text
queued
generating
validating
```

better than fake 67%.

---

# 237. User Cancellation Feedback

If cancellation succeeds:

display authoritative cancelled state.

---

# 238. Cancellation Pending

If worker already executing irreversible operation:

do not claim immediate cancellation until known.

---

# 239. Scheduler Time Semantics

Persist schedule using unambiguous timezone strategy.

---

# 240. User-Facing Scheduling

Admin-selected local time must be converted/retained so future DST/timezone interpretation remains correct.

Exact timezone implementation later.

---

# 241. Casting Deadline Jobs

VOP/reminder jobs may observe deadline.

They do not automatically change Casting/Opportunity outcome.

---

# 242. Opportunity Inactivity

Scheduler may create Observation:

```text
No action for X days
```

but MUST NOT auto-transition stage.

---

# 243. Job Security Authorization

Background handler acts under:

```text
system execution authority
+
previous authorized intent
```

where applicable.

---

# 244. System Actor

Audit may identify:

```text
System
Virtual Operator
Migration
```

as actor for safe automation.

---

# 245. Scheduled Human Intent

Job SHOULD retain:

```text
requested_by
approved_at
```

for high-impact scheduled actions.

---

# 246. Permission Revocation Before Execution

System policy must determine if prior authorization remains valid.

For sensitive delayed action, revalidation SHOULD occur.

---

# 247. Example Theme

If Admin account disabled before scheduled Theme activation, exact previously approved activation may still or may not execute depending security policy; this must be explicit later.

---

# 248. Example Social

If OAuth connection revoked:

job fails authorization/integration check and does not publish.

---

# 249. No Credential Copy in Schedule

Scheduled job references SocialAccountConnection, retrieves current secure credential.

---

# 250. Job Payload Validation

Every claim/execution starts with schema validation.

---

# 251. Invalid Payload

```text
FAILED_FINAL
```

with diagnostic.

Do not attempt best-effort guessing.

---

# 252. Missing Entity

If exact target no longer exists:

classification depends on semantics.

Example search reindex:

```text
remove from index
```

may be correct.

Example PDF Revision missing:

```text
FAILED_FINAL / integrity issue
```

---

# 253. Archived Entity

Current-derived jobs may skip or adapt.

Historical immutable jobs may still execute if retained source exists.

---

# 254. Job Dependencies on Privacy

If privacy revocation makes output illegal:

job MUST revalidate and stop.

Example:

Builder generation queued before Contact Builder permission revoked.

If Snapshot not yet created:

revalidate and exclude/block.

If immutable Snapshot already created, serving/access policy follows privacy policy.

---

# 255. Builder Generation Boundary

Preferred:

```text
revalidate
→ create GenerationSnapshot
→ queue PDF job
```

Thus PDF job itself renders immutable approved Snapshot.

---

# 256. Notification Privacy Boundary

Notification job uses persisted minimized delivery payload or rebuilds under current policy, depending design.

Sensitive information should not be unnecessarily serialized into queue.

---

# 257. Scheduler Restart

Persisted future jobs remain intact.

---

# 258. Clock Drift

Worker/scheduler environment should use reliable system time.

Operational monitoring should detect significant clock issues if they affect scheduling.

---

# 259. Grace Period

Some scheduled actions MAY support configurable grace window.

Example:

if worker was down at 18:00 and returns at 18:05:

```text
publish social post?
skip?
```

Policy must be job-specific.

---

# 260. Social Missed Schedule

Recommended configurable behavior:

```text
execute if within acceptable grace window
otherwise require Admin review
```

rather than publish stale campaign hours/days late blindly.

---

# 261. Notification Missed Quiet-Hour Window

Usually execute when next allowed and service returns.

---

# 262. Theme Activation Missed

May activate if still within approved temporary period; otherwise skip and record missed activation.

---

# 263. Maintenance Missed

Execute next available cycle.

---

# 264. Job Locks

Locks are technical.

They must not replace domain version/concurrency validation.

---

# 265. Example

Job has exclusive lock but target Opportunity version changed.

If job's semantic action depends on version, handler still revalidates.

---

# 266. Business Command Jobs

If future architecture queues a business command itself, that handler MUST run same Domain rules as synchronous command.

No “trusted worker shortcut”.

---

# 267. Current Recommended Scope

Core professional decisions should remain synchronous/explicit Human commands.

Background handles technical consequences.

---

# 268. Testing Strategy

Background architecture requires:

```text
unit
integration
crash/recovery
concurrency
provider failure
idempotency
deployment compatibility
```

tests.

---

# 269. Unit Tests

Per handler:

```text
payload validation
retry classification
idempotency key
state mapping
```

---

# 270. Integration Tests

With real/test database:

```text
claim
lock
retry
lease expiry
outbox dispatch
```

---

# 271. Crash Tests

Kill worker:

```text
after claim
during processing
after external side effect
before result commit
```

Verify recovery strategy.

---

# 272. Concurrency Test

Two workers claim same queue concurrently.

Only safe execution semantics result.

---

# 273. Retry Test

Transient failure:

```text
attempt 1 fail
attempt 2 fail
attempt 3 success
```

One final semantic result.

---

# 274. Final Failure Test

Permanent failure does not retry indefinitely.

---

# 275. Outbox Crash Test

Business transaction commits, process stops before dispatcher.

After restart, event still dispatches.

---

# 276. Duplicate Outbox Test

Same outbox event processed twice.

Consumer produces one semantic side effect.

---

# 277. Job Payload Upgrade Test

Queue v1 job.

Deploy handler v2.

Old job remains processable/migrated safely.

---

# 278. Scheduler Duplicate Test

Two scheduler instances run same tick.

Only one semantic scheduled job created.

---

# 279. Missed Schedule Test

Worker offline through scheduled time.

Recovery follows job-specific grace policy.

---

# 280. Security Test

Job payload cannot inject arbitrary handler/module.

Only registered job types.

---

# 281. Secret Leakage Test

No job/outbox record contains provider secrets.

---

# 282. Privacy Test

Private Casting/Feedback contents are not unnecessarily serialized into generic job list/logs.

---

# 283. JOB-AP-001

**Critical work queued with `setTimeout()` only**

---

# 284. JOB-AP-002

**HTTP request returns queued before durable job exists**

---

# 285. JOB-AP-003

**DB commit succeeds but task intent exists only in process memory**

---

# 286. JOB-AP-004

**Infinite retries**

---

# 287. JOB-AP-005

**Fixed one-second retry storm against unavailable provider**

---

# 288. JOB-AP-006

**Every error is considered retryable**

---

# 289. JOB-AP-007

**Every error is considered permanent**

---

# 290. JOB-AP-008

**No provider idempotency for external side effects**

---

# 291. JOB-AP-009

**Worker publishes Opportunity stage directly**

---

# 292. JOB-AP-010

**Queue payload contains API key**

---

# 293. JOB-AP-011

**PDF job references “current questionnaire”**

---

# 294. JOB-AP-012

**Media job overwrites source original**

---

# 295. JOB-AP-013

**Worker crash leaves job RUNNING forever**

---

# 296. JOB-AP-014

**Scheduler uses local process timer as sole state**

---

# 297. JOB-AP-015

**Multiple scheduler instances create duplicate Social publishes**

---

# 298. JOB-AP-016

**Retry all social platforms when only TikTok failed**

---

# 299. JOB-AP-017

**AI successful result regenerated on HTTP response retry**

---

# 300. JOB-AP-018

**Search reindex job treated as immutable historical work and executes every obsolete version**

---

# 301. JOB-AP-019

**Historical PDF job collapsed because newer Revision exists**

---

# 302. JOB-AP-020

**Queue tables cleaned without preserving FAILED_FINAL diagnostics**

---

# 303. JOB-AP-021

**Worker logs entire private Casting payload**

---

# 304. JOB-AP-022

**External API call executed inside long DB transaction**

---

# 305. JOB-AP-023

**Provider says success, DB write fails, retry blindly duplicates action**

---

# 306. JOB-AP-024

**Job payload schema changed without migration/versioning**

---

# 307. JOB-AP-025

**Background worker bypasses same domain validation used by Admin**

---

# 308. Background Jobs Quality Gate

Перед implementation MUST быть определены:

- [ ] durable job store;
- [ ] transactional outbox;
- [ ] worker runtime;
- [ ] scheduler;
- [ ] claiming;
- [ ] lease/recovery;
- [ ] payload schema;
- [ ] payload versioning;
- [ ] registered job types;
- [ ] per-type timeout;
- [ ] retry classification;
- [ ] retry limits;
- [ ] backoff/jitter;
- [ ] idempotency key;
- [ ] provider idempotency/reconciliation;
- [ ] priority;
- [ ] concurrency limits;
- [ ] cancellation rules;
- [ ] stale job behaviour;
- [ ] security revalidation;
- [ ] secret handling;
- [ ] privacy minimization;
- [ ] observability;
- [ ] dead-letter/final failure flow;
- [ ] Admin diagnostics;
- [ ] VOP integration;
- [ ] retention/cleanup;
- [ ] deployment compatibility;
- [ ] crash-recovery tests.

---

# 309. Job Specification Template

```text
Job Type:
PDF_GENERATE

Semantic Class:
IMMUTABLE_SOURCE

Source:
QuestionnaireRevision

Payload:
revision_id
locale
renderer_version
template_version

Payload Version:
1

Idempotency Key:
revision + locale + renderer + template

Priority:
HIGH

Timeout:
...

Retryable Errors:
renderer crash
temporary storage error

Permanent Errors:
source revision missing
invalid immutable document model

Max Attempts:
...

Concurrency:
...

Result:
QuestionnaireArtifact

Audit:
not for each technical retry

VOP:
observe final/repeated failure
```

---

# 310. Notification Job Example

```text
Job Type:
NOTIFICATION_DELIVER

Semantic Class:
EXTERNAL_SIDE_EFFECT

Source:
NotificationDelivery

Payload:
delivery_id

Idempotency:
delivery ID + provider idempotency

Quiet Hours:
yes

Retry:
transient provider/network failures

Permanent:
invalid recipient / revoked configuration

Result:
SENT / FAILED_FINAL

Business Source:
never mutated because of delivery failure
```

---

# 311. Social Job Example

```text
Job Type:
SOCIAL_PUBLISH

Source:
approved SocialPost version + target account

Human Approval:
required before scheduling

Scheduled:
optional

Idempotency:
post version + target platform/account

Retry:
target-specific only

Unknown Outcome:
provider reconciliation required

Result:
SocialPublishAttempt
```

---

# 312. AI Casting Job Example

```text
Job Type:
AI_CASTING_ANALYSIS

Source:
immutable Casting analysis request/source snapshot

AI Output:
schema validated

Success:
new CastingAnalysisRevision

Failure:
manual Casting workflow remains available

Human Confirmation:
still required for extracted requirements
```

---

# 313. Emotional Grid Job Example

```text
Job Type:
EMOTIONAL_GRID_RENDER

Source:
exact finalized Grid revision/config

Inputs:
same-session MediaAssets
confirmed cells

Outputs:
MASTER
WEB
PDF
THUMBNAIL

Appearance Mutation:
prohibited

Idempotency:
Grid revision + renderer version

Failure:
configuration preserved
```

---

# 314. Search Job Example

```text
Job Type:
SEARCH_REINDEX

Semantic Class:
CURRENT_STATE_DERIVED

Source:
current public projection

Superseded Work:
may coalesce/skip

Failure:
direct site navigation remains operational

Idempotent:
yes
```

---

# 315. Theme Expiration Job Example

```text
Job Type:
THEME_EXPIRATION

Semantic Class:
SCHEDULED_PREAUTHORIZED

Source:
ThemeActivation

Trigger:
expires_at

Action:
resolve next effective ThemeRevision

Human Decision:
already encoded in temporary activation

Idempotent:
yes
```

---

# 316. E2E-JOB-001 — Outbox Durability

Create Feedback.

Commit succeeds.

Kill Web Runtime before dispatch.

Restart.

Expected:

```text
Notification intent still dispatched.
```

---

# 317. E2E-JOB-002 — Worker Restart

Queue PDF job.

Kill Worker while queued.

Restart.

Job runs.

---

# 318. E2E-JOB-003 — Crash After Claim

Worker claims Media derivative and crashes.

Lease expires.

Another Worker retries.

One final valid derivative.

---

# 319. E2E-JOB-004 — Duplicate Claim Competition

Two Workers compete.

No harmful duplicate semantic result.

---

# 320. E2E-JOB-005 — Notification Retry

WhatsApp returns temporary 503 twice, then success.

Expected:

```text
one delivered notification
attempt history preserved
```

---

# 321. E2E-JOB-006 — Notification Final Failure

Permanent invalid recipient.

No endless retry.

Feedback unchanged.

---

# 322. E2E-JOB-007 — PDF Historical Input

Queue PDF for Revision 5.

Publish Revision 6.

Worker later renders Revision 5.

Output is Revision 5.

---

# 323. E2E-JOB-008 — Search Supersession

Profile v10 and v11 reindex queued.

System may collapse obsolete v10 safely.

Final index represents v11.

---

# 324. E2E-JOB-009 — Scheduled Social

Schedule approved post.

Restart server before schedule.

At scheduled time Worker publishes exact approved version.

---

# 325. E2E-JOB-010 — Edit Scheduled Social

Change post after scheduling.

Unreviewed new content is not silently published.

---

# 326. E2E-JOB-011 — Social Partial Failure

Instagram succeeds.

TikTok fails.

Retry runs TikTok only.

---

# 327. E2E-JOB-012 — AI Retry

Provider timeout before valid result.

Retry creates one accepted AnalysisRevision.

---

# 328. E2E-JOB-013 — AI Regenerate

User explicitly clicks Regenerate after successful Draft.

Creates new AIDraft generation, not retry of existing successful job.

---

# 329. E2E-JOB-014 — Grid Retry

Grid renderer crashes.

Frozen configuration remains.

Retry uses identical Grid revision.

---

# 330. E2E-JOB-015 — Queue Payload Upgrade

Old payload v1 remains processable after application deployment v2.

---

# 331. E2E-JOB-016 — Scheduler Duplication

Run two Scheduler instances.

One Theme activation occurs.

---

# 332. E2E-JOB-017 — Builder Expiry

Server down at exact expiry.

After recovery, expired session cannot generate new document.

---

# 333. E2E-JOB-018 — Provider Unknown Outcome

Provider accepts external send but network response lost.

Retry/reconciliation does not create duplicate send where provider capabilities allow prevention.

---

# 334. E2E-JOB-019 — Secrets

Inspect durable job/outbox storage.

No API/OAuth secrets persisted.

---

# 335. E2E-JOB-020 — Final Failure → VOP

Repeated current Questionnaire PDF failure reaches terminal state.

VOP/Dashboard surfaces actionable Observation.

---

# 336. E2E-JOB-021 — Cleanup

Expired staging/temp files cleaned.

Professional originals/historical required artifacts untouched.

---

# 337. E2E-JOB-022 — Priority

High-priority Feedback notification is not permanently blocked by large maintenance backlog.

---

# 338. E2E-JOB-023 — External Provider Outage Isolation

WhatsApp queue backs up/fails.

PDF, Media and Search jobs continue.

---

# 339. E2E-JOB-024 — DB Unavailable

Worker cannot persist result.

It stops/fails safely rather than performing uncontrolled repeated external side effects.

---

# 340. E2E-JOB-025 — Stale Scheduled Theme

Scheduled Theme revision revoked before activation.

Worker revalidates and does not activate it.

---

# 341. Job Traceability

Canonical:

```text
BUSINESS COMMAND
      ↓
AUTHORITATIVE COMMIT
      ↓
OUTBOX EVENT
      ↓
JOB
      ↓
ATTEMPT
      ↓
RESULT
```

---

# 342. External Side Effect Traceability

```text
Notification
     ↓
NotificationDelivery
     ↓
Job
     ↓
Provider Request
     ↓
Provider ID / Result
```

---

# 343. Document Traceability

```text
QuestionnaireRevision
      ↓
PDF Job
      ↓
Artifact
```

---

# 344. AI Traceability

```text
AI Request / Source Snapshot
      ↓
AI Job
      ↓
Provider
      ↓
Validated Output
      ↓
AIDraft / AnalysisRevision / ThemeProposal
```

---

# 345. Scheduled Action Traceability

```text
Human Approval
      ↓
Schedule
      ↓
Scheduled Job
      ↓
Revalidation
      ↓
Execution
      ↓
Result
```

---

# 346. Worker Architecture Diagram

```text
                   WEB / APPLICATION
                         │
                  DB TRANSACTION
                         │
                ┌────────┴─────────┐
                ▼                  ▼
          AUTHORITATIVE DATA     OUTBOX
                                   │
                                   ▼
                           OUTBOX DISPATCHER
                                   │
                                   ▼
                              JOB STORE
                                   │
                    ┌──────────────┼──────────────┐
                    ▼              ▼              ▼
                Worker A        Worker B       Scheduler
                    │              │              │
        ┌───────────┼───────────┐  │              │
        ▼           ▼           ▼  ▼              ▼
      Media        PDF          AI Notifications Scheduled
        │           │           │       │
        ▼           ▼           ▼       ▼
      Storage     Artifact    AI Out   Provider
```

---

# 347. Failure Architecture Diagram

```text
JOB
 ↓
RUNNING
 ↓
ERROR
 ├─ RETRYABLE
 │      ↓
 │   BACKOFF
 │      ↓
 │    RETRY
 │
 ├─ UNKNOWN EXTERNAL OUTCOME
 │      ↓
 │   RECONCILE
 │
 └─ PERMANENT / EXHAUSTED
        ↓
    FAILED_FINAL
        ↓
  ADMIN / VOP OBSERVATION
```

---

# 348. Background Jobs Compliance Criteria

Реализация соответствует DOC-076, если:

1. critical accepted jobs are durable;
2. no critical queue relies solely on process memory;
3. transactional outbox protects commit→side-effect gap;
4. consumers tolerate at-least-once delivery;
5. handlers are idempotent;
6. external side effects have duplicate prevention/reconciliation strategy;
7. jobs use typed versioned payloads;
8. immutable jobs reference exact Revision/Snapshot;
9. current-derived jobs may coalesce safely;
10. worker claiming is concurrency-safe;
11. crashed RUNNING jobs can be reclaimed;
12. retries are finite and classified;
13. backoff exists;
14. final failures remain observable;
15. Scheduler state is durable;
16. multiple schedulers cannot create duplicate semantic actions;
17. human-authorized scheduled actions bind to approved content/version;
18. delayed jobs revalidate relevant state/security;
19. business truth does not depend on optional provider success;
20. notification failure never loses Feedback;
21. AI failure leaves manual workflows available;
22. background worker cannot bypass Domain rules;
23. secrets are absent from durable payloads;
24. private data is minimized;
25. Web and Worker failures are operationally isolated where possible;
26. provider-specific outages do not stop unrelated queues;
27. deployments preserve queued payload compatibility;
28. queue metrics/backlog/failures are observable;
29. VOP can surface meaningful terminal/repeated failures;
30. cleanup jobs cannot delete professional sources outside retention policy.

---

# 349. Финальная доктрина

> **Background processing в продукте должно быть не “набором асинхронных функций”, а отдельной надёжной execution architecture. Веб-приложение фиксирует бизнес-истину и durable intent, worker выполняет техническое действие, а retries, idempotency, scheduling и reconciliation гарантируют, что временные сбои не превращаются в потерю обращений, документов, уведомлений или публикаций. При этом background worker никогда не получает права самостоятельно менять профессиональный смысл: он может сгенерировать PDF, QR, derivative, выполнить утверждённую публикацию или уведомление, но решение о том, что является фактом, опубликованным материалом, подтверждённым требованием или Booked opportunity, остаётся внутри соответствующего Domain и Human authority.**