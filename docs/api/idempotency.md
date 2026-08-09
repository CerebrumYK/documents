# IDEMPOTENCY

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная архитектура durable idempotency, deduplication, replay и external-side-effect reconciliation

**Целевой файл:** `docs/api/idempotency.md`  
**Документ:** DOC-103  
**Статус:** ✅ Completed  
**Тип:** API / Reliability / Idempotency / Concurrency / External Side Effects

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/business-rules.md`
- `docs/product/product-principles.md`
- `docs/domain/domain-model.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions-and-history.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/media-processing.md`
- `docs/architecture/pdf-generation.md`
- `docs/architecture/background-jobs.md`
- `docs/architecture/cache-strategy.md`
- `docs/architecture/search.md`
- `docs/architecture/ai-architecture.md`
- `docs/architecture/notifications.md`
- `docs/database/database-architecture.md`
- `docs/database/data-dictionary.md`
- `docs/database/validation-and-constraints.md`
- `docs/database/deletion-and-retention.md`
- `docs/api/server-actions.md`
- `docs/api/contracts.md`
- `docs/api/errors.md`

---

# 1. Назначение

Настоящий документ определяет, как система должна безопасно обрабатывать повтор одного и того же логического действия.

Idempotency требуется для защиты от:

- двойного клика;
- повторной отправки формы;
- browser retry;
- network retry;
- mobile reconnect;
- reverse proxy retry;
- worker retry;
- provider webhook replay;
- scheduler retry;
- crash/restart;
- ambiguous external-provider outcome;
- concurrent execution одного semantic intent.

---

# 2. Главная доктрина

> **Повтор одного и того же логического намерения не должен создавать повторный бизнес-результат или повторный внешний side effect.**

Canonical:

```text
ONE SEMANTIC INTENT
       ↓
IDEMPOTENCY IDENTITY
       ↓
AT MOST ONE BUSINESS RESULT
       ↓
REPLAY RETURNS SAME RESULT
```

---

# 3. Idempotency != exactly-once transport

Система не предполагает exactly-once delivery.

Baseline:

```text
delivery:
AT LEAST ONCE

business effect:
IDEMPOTENT
```

---

# 4. Почему

HTTP, queues, process crashes и внешние providers не дают универсальной гарантии exactly-once.

Поэтому архитектура должна предполагать duplicate delivery.

---

# 5. Identifiers

Используются:

```text
IDEM-*
IDEM-KEY-*
IDEM-FP-*
IDEM-EXT-*
IDEM-JOB-*
IDEM-WEBHOOK-*
IDEM-INV-*
IDEM-AP-*
```

---

# 6. IDEM-INV-001 — Idempotency Is Durable

Критичные idempotency records MUST храниться durable.

Запрещено полагаться только на:

```text
browser disabled button
React state
in-memory Set
process memory
cache-only lock
```

---

# 7. IDEM-INV-002 — Idempotency Scope Is Explicit

Один key не существует «глобально».

Identity включает semantic scope.

Conceptually:

```text
scope
+
idempotency_key
```

---

# 8. Example scopes

```text
feedback.submit
builder.generate
questionnaire.publish
casting.create_from_feedback
opportunity.create_project
notification.delivery
social.publish
provider.webhook
```

---

# 9. IDEM-INV-003 — Same Key + Same Intent = Same Result

Повтор должен:

```text
return existing result
```

или сообщить, что первоначальная операция ещё выполняется.

---

# 10. IDEM-INV-004 — Same Key + Different Intent = Conflict

Нельзя молча использовать результат первого запроса для другого payload.

Canonical error:

```text
IDEMPOTENCY_CONFLICT
```

---

# 11. IDEM-INV-005 — Idempotency Does Not Bypass Validation

Первый запрос должен пройти обычные:

```text
authentication
authorization
validation
concurrency
domain rules
```

---

# 12. Replay validation

Повтор уже **завершённой** операции не должен повторять destructive/domain execution только потому, что current source изменился.

Он возвращает сохранённый semantic result исходного intent.

---

# 13. Новый intent после Source changes

Требует нового idempotency key.

---

# 14. Canonical idempotency record

DOC-091 зафиксировал:

```text
idempotency_records
```

Recommended fields:

```text
id
scope
idempotency_key_hash
request_fingerprint
result_entity_type
result_entity_id
status
expires_at
created_at
updated_at
```

---

# 15. Recommended extension

Implementation SHOULD support:

```text
result_snapshot
error_code
completed_at
owner_principal_hash/context
```

where operation requires replay without reconstructing ambiguous current result.

---

# 16. Statuses

Canonical:

```text
PROCESSING
COMPLETED
FAILED_RETRYABLE
FAILED_FINAL
UNKNOWN_OUTCOME
EXPIRED
```

---

# 17. PROCESSING

Semantic intent already claimed and is executing.

---

# 18. COMPLETED

Business result committed.

Replay returns same semantic result.

---

# 19. FAILED_RETRYABLE

Original execution did not create business effect and may be retried safely.

---

# 20. FAILED_FINAL

Semantic request failed permanently.

Whether same key can be retried depends on operation contract.

Preferred baseline:

same key replays same final result.

Corrected request uses new key.

---

# 21. UNKNOWN_OUTCOME

External effect may have happened, but local system cannot yet prove outcome.

---

# 22. Critical rule

`UNKNOWN_OUTCOME` MUST NOT be blindly converted to new external send.

---

# 23. Expired

Record no longer provides duplicate protection after policy window.

---

# 24. Key source

Idempotency key may originate from:

```text
client
server
domain entity
provider event ID
scheduled intent
```

depending operation.

---

# 25. Client-generated key

Suitable for:

```text
Feedback submission
Builder Generate
Admin high-impact form command
```

---

# 26. Server-generated semantic key

Suitable when identity already exists in authoritative source.

Example:

```text
questionnaire:{revisionId}:pdf:{locale}:{rendererVersion}
```

---

# 27. Provider event ID

Preferred for webhook replay protection when provider supplies stable event identity.

---

# 28. Scheduled intent

Can derive from:

```text
delivery_id
social_post_target_id
artifact_id
```

---

# 29. Key opacity

Client-generated keys SHOULD be opaque random values.

UUID acceptable.

---

# 30. No business secrets in key

Avoid:

```text
phone-email-message
```

inside raw idempotency key.

---

# 31. Key storage

Prefer storing:

```text
hash(raw_key)
```

rather than raw user/provider secret-like key where raw value is not needed.

---

# 32. Request fingerprint

Fingerprint proves whether same key represents same semantic request.

---

# 33. Fingerprint construction

Conceptually:

```text
canonicalize(request_semantics)
→ deterministic serialization
→ cryptographic hash
```

---

# 34. Fingerprint includes

Only semantic fields that define intent.

Example Feedback:

```text
feedback_type
normalized sender fields
message
source context
attachment identities
```

---

# 35. Fingerprint excludes

Fields that do not change semantic request:

```text
request timestamp
correlation ID
trace ID
HTTP header order
browser metadata
```

---

# 36. Canonical serialization

Must be deterministic.

For example:

- stable property ordering;
- normalized URLs;
- normalized phone/email where relevant;
- normalized optional/null semantics;
- ordered arrays preserved where order matters.

---

# 37. IDEM-FP-INV-001

Semantically equal payloads SHOULD fingerprint equally.

---

# 38. IDEM-FP-INV-002

Semantically different payloads MUST not intentionally collapse into same fingerprint.

---

# 39. Cryptographic hash

Use standard secure hash such as SHA-256 or equivalent project-approved algorithm.

Exact implementation later.

---

# 40. Key uniqueness

Database SHOULD guarantee:

```text
UNIQUE(scope, idempotency_key_hash)
```

---

# 41. Atomic claim

Critical race:

```text
Request A
Request B
```

same key concurrently.

Only one may claim execution.

---

# 42. Claim pattern

Conceptually:

```text
INSERT idempotency record PROCESSING
ON CONFLICT → read existing
```

inside controlled transaction.

---

# 43. Alternative

Row locking / transaction-safe upsert acceptable.

---

# 44. IDEM-INV-006 — Check-Then-Insert Without Unique Guard Is Invalid

Bad:

```text
SELECT no row
↓
two requests
↓
both INSERT business object
```

---

# 45. Business transaction integration

Where possible:

```text
idempotency claim
+
business Source mutation
+
Audit
+
Outbox
```

should be coordinated transactionally.

---

# 46. Completed result

Only mark:

```text
COMPLETED
```

after authoritative transaction has committed successfully.

---

# 47. Crash before commit

Result must remain retryable/PROCESSING lease-recoverable depending implementation.

No phantom `COMPLETED`.

---

# 48. Crash after business commit before response

This is a primary idempotency scenario.

Replay must discover completed business result rather than create second entity.

---

# 49. Therefore

Result linkage must commit reliably enough to recover semantic result.

---

# 50. Preferred transactional pattern

```text
BEGIN
  claim/read idempotency record
  validate
  create business entity
  Audit
  Outbox
  set idempotency result reference
  mark COMPLETED
COMMIT
```

---

# 51. If operation schedules async work

Initial idempotent business result may be:

```text
Artifact ID
AI request ID
Notification Delivery ID
```

rather than final external outcome.

---

# 52. Example

Builder Generate:

```text
one request
→ one GenerationSnapshot
→ one Artifact identity
```

PDF may still be queued.

Replay returns same Snapshot/Artifact.

---

# 53. IDEM-INV-007 — Idempotency Protects Semantic Creation, Not Only Final Completion

---

# 54. Feedback submission

Required durable idempotency.

---

# 55. Feedback scope

```text
feedback.submit
```

---

# 56. First request

```text
Create Feedback
→ Feedback F123
```

---

# 57. Same retry

Returns:

```text
F123
```

---

# 58. Must not

```text
F123
F124
F125
```

because browser retried.

---

# 59. Attachment concern

Feedback idempotency fingerprint must include attachment semantic identity where attachments belong same submit intent.

---

# 60. Staged attachment

If upload occurs before Feedback submit:

use uploaded attachment IDs/checksums in fingerprint.

---

# 61. Attachment retry

Must not duplicate identical relation in same semantic Feedback submit.

---

# 62. Feedback notification

Separate idempotency scope.

Creating Feedback once does not by itself guarantee provider send once unless Notification delivery also idempotent.

---

# 63. Builder Generate

Required durable idempotency.

Scope:

```text
builder.generate
```

---

# 64. Semantic intent

Includes:

```text
session ID
expected/current session version
generation configuration fingerprint
```

---

# 65. Replay

Returns same:

```text
generationSnapshotId
artifactId
```

---

# 66. Session changes

If visitor edits Builder Session after generation:

new Generate operation MUST use new key.

---

# 67. Same key after session changed

Returns `IDEMPOTENCY_CONFLICT`, not old result pretending it represents new configuration.

---

# 68. Questionnaire Publish

Requires idempotent publication command.

---

# 69. Scope

```text
questionnaire.publish
```

---

# 70. Semantic identity

At least:

```text
questionnaire ID
Draft expected version/content identity
locale/publication configuration
```

---

# 71. Double-click Publish

Must not create:

```text
Revision 5
Revision 6
```

from same intent.

---

# 72. Correct

One:

```text
Revision 5
```

and replay returns it.

---

# 73. New edited Draft

New publication intent → new key → new Revision.

---

# 74. Grid Finalization

Should be idempotent.

---

# 75. Scope

```text
emotional_grid.finalize
```

---

# 76. Same draft version + same key

One GridRevision.

---

# 77. Changed draft

New key required.

---

# 78. Create Casting from Feedback

Strong idempotency required.

---

# 79. Why

Admin double-click or retry must not create two Castings for same explicit creation command.

---

# 80. Scope

```text
casting.create_from_feedback
```

---

# 81. Semantic identity

```text
feedback ID
creation mode/configuration
```

---

# 82. Result

One:

```text
Casting
+
FeedbackCastingLink
```

---

# 83. Replay

Returns same Casting.

---

# 84. It does not imply only one Casting can ever relate to Feedback

Business rule may allow future additional explicitly separate Castings.

Each separate intent receives new key.

---

# 85. Booked Opportunity → Project Draft

Strong idempotency required.

---

# 86. Scope

```text
opportunity.create_project
```

---

# 87. Same Booked opportunity command retry

One Project Draft.

---

# 88. Suggested additional business guard

Where product semantics allow only one generated Project from an Opportunity, database/domain uniqueness SHOULD reinforce idempotency.

---

# 89. Idempotency is not replacement for domain uniqueness

Both may apply.

---

# 90. AI generation

Generation requests SHOULD have idempotency.

---

# 91. Scope examples

```text
ai.bb.generate
ai.casting.analyze
ai.theme.generate
ai.vop.analyze
```

---

# 92. Semantic identity

Includes:

```text
capability
source snapshot hash
prompt version
model config version
task-specific settings
```

---

# 93. Same exact request retry

May reuse same generation request/output according to capability policy.

---

# 94. Explicit “Regenerate”

Is a **new semantic intent**.

It MUST be able to generate a new AI output even with same Source.

Therefore it gets new request identity/key.

---

# 95. IDEM-AI-INV-001 — Regenerate != Retry

Critical.

---

# 96. AI provider call

If provider generation is pure/non-side-effectful, duplicate call is less dangerous but still costs money and creates history noise.

Idempotency prevents unnecessary duplicate generations.

---

# 97. Theme Generate

Same retry returns same ThemeProposal generation.

User clicking “Generate another” creates new proposal.

---

# 98. Notifications

Notification architecture has two idempotency levels.

---

# 99. Level 1 — Notification intent

Same business event should not create uncontrolled duplicate Notification records.

---

# 100. Level 2 — Delivery

Each:

```text
Notification × Channel × Recipient
```

has stable Delivery identity.

---

# 101. Delivery idempotency key

Prefer:

```text
notification_delivery.id
```

or deterministic derived key.

---

# 102. External provider

If provider supports native idempotency key:

send stable Delivery ID/key.

---

# 103. If provider does not

Local delivery state + reconciliation must protect as far as possible.

---

# 104. External side effects are hardest case

Canonical states:

```text
NOT_SENT
SENDING
SENT
FAILED
OUTCOME_UNKNOWN
```

---

# 105. Example ambiguous outcome

```text
POST WhatsApp
provider accepted
network disconnect before response
```

Local application cannot know whether message was sent.

---

# 106. Bad behavior

Immediately retry send.

Could create duplicate message.

---

# 107. Correct behavior

```text
OUTCOME_UNKNOWN
↓
provider lookup/reconciliation if available
↓
or controlled retry according to provider idempotency guarantees
```

---

# 108. IDEM-EXT-001 — Unknown Outcome Is First-Class State

---

# 109. Email

Email protocols/providers may offer limited idempotency semantics.

System must use semantic delivery state and avoid casual blind retries after ambiguous acceptance.

---

# 110. Social Publishing

Strong external idempotency required.

---

# 111. Level

Per:

```text
social_post_target
```

---

# 112. Same retry

Must not create two Instagram/TikTok posts.

---

# 113. Stable identity

Use:

```text
target ID
approved post version
```

as semantic basis.

---

# 114. If target already PUBLISHED

Retry command returns existing published result.

---

# 115. If outcome unknown

Reconcile provider state before reposting where possible.

---

# 116. Approved version change

New version is a new semantic publish intent.

Existing published target must not be silently overwritten/reposted.

---

# 117. Provider webhooks

Webhook replay protection mandatory.

---

# 118. Preferred key

Provider stable event ID.

---

# 119. Scope

```text
provider:{provider}:webhook:{event_type}
```

or equivalent.

---

# 120. Webhook pipeline

```text
verify signature
↓
parse event
↓
determine event ID/fingerprint
↓
claim idempotency
↓
process Application handler
↓
mark completed
```

---

# 121. Replay already completed

Return provider-compatible success acknowledgment.

---

# 122. Why

Returning error causes provider to retry endlessly.

---

# 123. Webhook same ID/different payload

Security anomaly.

Do not process blindly.

---

# 124. Response

Reject/log:

```text
WEBHOOK_EVENT_CONFLICT
```

or security-specific internal code.

---

# 125. Provider without stable event ID

Use deterministic event fingerprint based on signed stable fields.

Document provider-specific strategy.

---

# 126. OAuth callbacks

OAuth callback `state` validation serves security, not general idempotency.

But callback processing SHOULD still avoid duplicate social-account connection side effects when provider/browser repeats callback.

---

# 127. Background Jobs

DOC-076 establishes at-least-once execution.

Therefore every job handler MUST declare idempotency semantics.

---

# 128. Job classes

```text
IMMUTABLE_ARTIFACT
CURRENT_DERIVED
EXTERNAL_SIDE_EFFECT
MAINTENANCE
```

---

# 129. Immutable artifact job

Example:

```text
QuestionnaireRevision R5
→ PDF locale ru renderer v3
```

Semantic key:

```text
revision + locale + renderer/template version
```

---

# 130. Duplicate job

Should reuse same Artifact identity/result.

---

# 131. Current derived job

Example:

```text
Search reindex Project v11
```

Can use latest-wins/coalescing.

---

# 132. v10 after v11

v10 skips.

Not a duplicate external effect problem.

---

# 133. External-side-effect job

Examples:

```text
WhatsApp
Email
Social
```

requires persistent Delivery/Target identity.

---

# 134. Maintenance job

Example:

```text
cleanup expired temp
```

should itself be naturally idempotent:

deleting already absent safe temp file should not corrupt state.

---

# 135. Job idempotency key

DOC-076 suggested:

```text
job_type + idempotency_key
```

database uniqueness.

---

# 136. Job key lifetime

Must cover full duplicate-delivery risk window.

---

# 137. Job payload changes

Same key + materially different payload is invalid.

---

# 138. Scheduler

Recurring scheduler instances require deterministic occurrence identity where duplicate scheduling could create duplicate side effect.

---

# 139. Example

Scheduled theme activation occurrence:

```text
activation:{activationId}:start
```

---

# 140. Duplicate scheduler run

One semantic transition.

---

# 141. Search/cache rebuild

Prefer deterministic generation/version guard rather than generic IdempotencyRecord for every rebuild.

---

# 142. Important distinction

```text
idempotency
!=
coalescing
!=
latest-wins
```

---

# 143. Idempotency

Same intent → same result.

---

# 144. Coalescing

Many similar pending requests collapse into one required operation.

---

# 145. Latest-wins

Older derived work is skipped because newer state supersedes it.

---

# 146. Example Search

v10 and v11 reindex:

not same intent.

v10 is obsolete; v11 wins.

---

# 147. Cache invalidation

Repeated invalidation naturally idempotent:

```text
invalidate key
invalidate key again
```

no business duplication.

---

# 148. Deletion/Purge

Hard purge requests SHOULD be idempotent.

---

# 149. Scope

```text
media.purge
entity.redact
privacy.delete
```

---

# 150. Repeated purge command

Must not create multiple conflicting purge workflows.

---

# 151. Already purged

Return semantic:

```text
PURGED
```

not fail because binary is gone.

---

# 152. Purge unknown outcome

Example storage delete timed out.

Storage deletion is generally safely repeatable if object-addressed delete is idempotent, but DB finalization still checks actual state.

---

# 153. Archive/Restore

Typically naturally idempotent:

```text
Archive archived entity
→ already archived
```

May return current state.

---

# 154. But stale expectedVersion

Explicit Admin edit concurrency may still return `STALE_VERSION`.

Idempotency and optimistic concurrency solve different problems.

---

# 155. IDEM-INV-008 — Idempotency != Optimistic Concurrency

Idempotency protects duplicate **same intent**.

Version protects conflicting **different edits**.

---

# 156. Example

Admin A:

```text
Update title → A
```

Admin B:

```text
Update title → B
```

Different intents.

Idempotency must not collapse them.

Concurrency resolves conflict.

---

# 157. Idempotency + authorization

Idempotency record must not let one principal retrieve another user's sensitive result.

---

# 158. Public anonymous scope

Builder/Feedback idempotency replay requires proper session/request context.

---

# 159. IDEM-SEC-001

Knowing another user's raw idempotency key MUST NOT grant result access.

---

# 160. Therefore

Result replay still checks appropriate principal/session/access scope.

---

# 161. Principal binding

For sensitive scopes, record MAY bind to:

```text
authenticated principal
Builder session
anonymous capability context
```

---

# 162. Do not fingerprint secret principal data directly

Use safe IDs/hashes.

---

# 163. Idempotency result storage

Two strategies allowed.

---

# 164. Strategy A — Result reference

Store:

```text
result_entity_type
result_entity_id
```

Then rebuild current semantic response carefully.

---

# 165. Strategy B — Result snapshot

Store safe response snapshot when replay must return exact original result semantics.

---

# 166. Preferred

Use reference where resource identity is sufficient.

Use snapshot for operations where reconstructing from current state would materially change replay response.

---

# 167. Example Feedback

Reference sufficient:

```text
Feedback ID
receivedAt
```

---

# 168. Example transition

Could store result snapshot:

```text
fromStage
toStage
changedAt
newVersion
```

because current Opportunity may have progressed further by replay time.

---

# 169. IDEM-INV-009 — Replay Must Not Lie Using Current State

Example:

original request:

```text
audition → callback
```

Opportunity later reaches `offer`.

Replay of original intent must not claim original transition resulted in `offer`.

---

# 170. Therefore

Store enough original semantic result to replay accurately.

---

# 171. Error replay

If original request definitively failed before business mutation:

same key may replay same error.

---

# 172. Retryable infrastructure failure

Two possible policies:

### A. same key may retry

Good when no semantic effect occurred and operation is known safe.

### B. caller receives failure and must submit new key

Simpler for some UI operations.

Each operation MUST define policy.

---

# 173. Preferred baseline

For transient pre-commit failures:

same key MAY retry.

For definitive validation/business failure:

same key replays same failure.

---

# 174. Request corrected after validation failure

Requires new key.

---

# 175. Why

Same key identifies same semantic request.

---

# 176. PROCESSING timeout

An idempotency record cannot remain permanently PROCESSING after process crash.

---

# 177. Need recovery model

Record SHOULD include:

```text
processing_started_at
lease_until / recoverable_after
```

or equivalent.

---

# 178. Recovery

After safe timeout:

system inspects whether business result committed.

---

# 179. If result exists

mark COMPLETED.

---

# 180. If no effect occurred

reclaim/retry.

---

# 181. If external outcome ambiguous

mark `UNKNOWN_OUTCOME`.

---

# 182. IDEM-INV-010 — Expired PROCESSING Must Be Reconciled, Not Blindly Duplicated

---

# 183. Transactional operations are easier

For DB-only semantic creation, idempotency record and business result can commit atomically.

---

# 184. External operations are two-phase

Canonical:

```text
persist intent
↓
commit
↓
external provider
↓
persist observed result
```

---

# 185. Never

```text
call provider
↓
then maybe persist intent
```

for high-impact external side effect.

---

# 186. Why

Crash after provider success but before local persistence creates impossible duplicate risk.

---

# 187. Notification delivery flow

```text
Delivery record
status=PENDING
      ↓
claim
status=SENDING
      ↓
provider send using stable provider idempotency key if available
      ↓
┌────────────┬─────────────┬──────────────┐
▼            ▼             ▼
SENT       FAILED       OUTCOME_UNKNOWN
```

---

# 188. Social equivalent

Same structure per `social_post_target`.

---

# 189. Reconciliation

Provider Adapter SHOULD expose lookup/reconcile capability when provider supports it.

Examples:

```text
lookup provider message ID
query post state
fetch event status
```

---

# 190. If provider has no reconciliation

Use conservative policy defined per integration.

Avoid guaranteed claims system cannot make.

---

# 191. Provider-native idempotency

When provider supports it:

ALWAYS prefer stable application-side semantic key.

---

# 192. Do not generate new provider key per retry

That defeats provider idempotency.

---

# 193. Retention of idempotency records

Records need not be permanent.

---

# 194. Retention class

Generally:

```text
R1 — SHORT_OPERATIONAL
```

unless business result requires longer replay guarantee.

---

# 195. Retention duration

Must cover realistic retry/replay window.

---

# 196. Candidates with longer retention

```text
external provider events
social publication
high-impact destructive operation
```

where late replay is plausible.

---

# 197. After expiry

Same raw key may technically be reusable only if policy permits.

Safer recommendation:

client generates new random keys per new action.

---

# 198. Provider event dedup retention

Must consider provider webhook retry horizon.

---

# 199. Cleanup

Retention worker may purge expired idempotency records only when:

```text
not PROCESSING
not UNKNOWN_OUTCOME requiring reconciliation
no hold
```

---

# 200. Never purge unresolved UNKNOWN_OUTCOME by age alone

Needs escalation/reconciliation policy.

---

# 201. Monitoring

Recommended metrics:

```text
idempotency_claims_total
idempotency_replays_total
idempotency_conflicts_total
idempotency_in_progress_total
unknown_outcomes_total
reconciliation_success_total
reconciliation_failure_total
```

---

# 202. Useful ratio

```text
replays / total requests
```

helps detect client/network retry behavior.

---

# 203. High conflict rate

May indicate:

```text
client reusing keys incorrectly
bug in fingerprint normalization
malicious replay
```

---

# 204. Observability logs

Safe fields:

```text
scope
key_hash_prefix
status
result entity ID
correlation ID
```

---

# 205. Never log raw sensitive key unnecessarily

---

# 206. Audit

Normal technical replay does not require Audit event.

---

# 207. Initial business operation

Audit occurs normally.

---

# 208. Replay

Must not create duplicate Audit entry pretending second business mutation occurred.

---

# 209. Important

Technical replay log may exist separately.

---

# 210. Analytics

Repeated transport replay SHOULD normally correspond to one business conversion event.

---

# 211. Example Feedback

One semantic Feedback → one:

```text
feedback_created
```

analytics event.

Not one per retry.

---

# 212. Builder generation

One semantic Snapshot → one:

```text
pdf_generated
```

when completed.

---

# 213. IDEM-INV-011 — Analytics Should Follow Business Effect, Not Delivery Attempts

---

# 214. Idempotency and notifications from events

Outbox event may be delivered repeatedly.

Notification consumer must deduplicate using stable:

```text
source_event_id
+
notification type
```

or semantic equivalent.

---

# 215. Example

`FeedbackCreated` processed twice.

Should create one Notification intent per configured semantic rule.

---

# 216. Event consumer identity

Recommended:

```text
consumer_name + event_id
```

where generic event-consumption dedup is useful.

---

# 217. Event version

Same event ID with changed payload is invalid.

Event payload immutable.

---

# 218. Revision artifact idempotency

Questionnaire PDF identity:

```text
revision ID
locale
document model version
template version
renderer version
```

---

# 219. Same generation request

One Artifact identity.

---

# 220. New renderer version

New semantic artifact identity.

Not duplicate.

---

# 221. QR identity

Canonical:

```text
target URL hash
settings hash
settings version
```

---

# 222. Same QR

Reuse validated artifact.

---

# 223. URL changes

New key/identity.

Historical old QR remains historical.

---

# 224. Media derivative identity

Canonical:

```text
MediaAsset
derivative type
transform hash
processor version
```

---

# 225. Duplicate derivative jobs

Reuse same derivative record/output.

---

# 226. User-visible “Retry”

Two meanings must be distinguished.

---

# 227. Retry failed same operation

Uses same semantic resource/intention where safe.

Example:

```text
Retry PDF artifact generation
```

same Artifact identity or new attempt for same Artifact.

---

# 228. Repeat business action

Creates new semantic intent.

Example:

```text
Send another professional message
Generate another AI draft
Publish new Questionnaire revision
```

new idempotency key.

---

# 229. UI naming

Avoid ambiguous button “Retry” when action actually means “Create another”.

---

# 230. Idempotency key generation UX

Client should generate key:

```text
when semantic action begins
```

and retain it across network retries of that action.

---

# 231. Client must not regenerate key on each HTTP retry

---

# 232. Client must generate new key for new user action

---

# 233. Server Action consideration

Framework may retry/re-submit under certain UX circumstances.

Business idempotency must not depend on framework behavior.

---

# 234. Multi-tab

Two tabs performing same explicitly duplicated business action with different keys are considered separate intents unless domain uniqueness prevents duplication.

---

# 235. Idempotency cannot infer user intent from similarity

Do not silently deduplicate:

```text
two Feedback messages happen to have same text
```

without same idempotency key/domain identity.

---

# 236. IDEM-INV-012 — Similar Payload != Same Intent

---

# 237. Anti-spam dedup

Separate concern.

Heuristic duplicate detection may flag, but does not replace idempotency contract.

---

# 238. Rate limit

Separate concern.

---

# 239. Concurrency matrix

| Situation | Mechanism |
|---|---|
| Same request repeated | Idempotency |
| Two different edits same entity | Optimistic concurrency |
| Only one Primary allowed | DB unique + transaction |
| Old derived work | Version/generation guard |
| Spam flood | Rate limiting |
| Provider repeated webhook | Webhook idempotency |
| Duplicate external send | Delivery idempotency + provider key/reconciliation |

---

# 240. Idempotency matrix — core product

| Operation | Required |
|---|---:|
| Save ordinary Profile draft | Usually optional |
| Publish Questionnaire | Yes |
| Finalize Emotional Grid | Yes |
| Builder Generate | Yes |
| Submit Feedback | Yes |
| Create Casting from Feedback | Yes |
| Run AI Analysis | Yes/recommended |
| Explicit AI Regenerate | New intent |
| Opportunity stage transition | Recommended/high-value |
| Create Project from Booked | Yes |
| Mark Notification Read | Naturally idempotent |
| Notification Delivery | Yes |
| Social Publish Target | Yes |
| Webhook processing | Yes |
| Media derivative | Yes |
| PDF rendering | Yes |
| QR generation | Yes |
| Cache invalidation | Naturally idempotent |
| Search reindex | Version/latest-wins |
| Purge | Yes |

---

# 241. Naturally idempotent update

Example:

```text
MarkNotificationRead(notificationId)
```

Running twice yields same state.

---

# 242. Still must authorize

Natural idempotency does not remove security validation.

---

# 243. Opportunity transition

Could be naturally conflict-protected by version, but idempotency is valuable because client might lose response after successful transition.

---

# 244. Example

Request:

```text
callback → offer
```

commits.

Response lost.

Retry same key:

must return original transition result rather than fail because current stage already `offer`.

---

# 245. Therefore

High-impact transition SHOULD be idempotency-aware.

---

# 246. Primary switch

Same principle may be useful.

But repeating:

```text
SetPrimary(item A)
```

is naturally convergent.

If response lost, current state already proves effect.

Dedicated IdempotencyRecord is optional unless Audit/side-effects make exact replay important.

---

# 247. File upload

Raw upload idempotency depends upload protocol.

---

# 248. Finalizing MediaAsset

Should avoid creating duplicate MediaAsset for retry of same staged upload.

---

# 249. Checksum dedup != idempotency

Two intentionally separate uploads may contain identical binary.

Do not assume same checksum = same user intent.

---

# 250. IDEM-INV-013 — Content Deduplication != Request Idempotency

---

# 251. Deletion/redaction

Privacy delete request should have semantic request identity.

---

# 252. Repeated privacy deletion

Returns current deletion workflow/state.

Does not start duplicate jobs.

---

# 253. Security

Idempotency endpoints are still abuse targets.

---

# 254. Key length

Bounded.

---

# 255. Key format

Strictly validated.

---

# 256. Key guessing

Should not reveal whether another principal used same key.

---

# 257. Raw key echo

Do not include raw key in user-facing diagnostics unless needed.

---

# 258. Fingerprint collision

Secure cryptographic hash makes practical collision risk negligible, but implementation must compare scope/key semantics properly.

---

# 259. Database isolation

Unique constraint is final concurrency guard.

---

# 260. Deadlocks

Retrying internal transaction can keep same semantic idempotency identity.

---

# 261. Transaction retry

Must not regenerate revision/result identity incorrectly outside transaction.

---

# 262. Revision number allocation

Idempotency + database uniqueness prevents duplicate revisions but revision allocation still needs its own transaction safety.

---

# 263. Failed publish retry

If no Revision committed:

same intent/key may retry.

---

# 264. Publish committed but PDF failed

Replay Publish returns existing Revision.

It does **not** publish a new Revision.

PDF retry happens at Artifact level.

---

# 265. Critical separation

```text
Publish Questionnaire idempotency
!=
PDF generation idempotency
```

---

# 266. Same for Emotional Grid

```text
Finalize Grid
!=
Render Grid Artifacts
```

---

# 267. Same for Feedback

```text
Create Feedback
!=
Send Notification
```

---

# 268. Same for Social content

```text
Approve/Schedule Post
!=
Publish Target
```

---

# 269. Idempotency layers prevent cascade duplication

Example:

```text
FeedbackCreated delivered twice
     ↓
Notification intent dedup
     ↓
WhatsApp Delivery retry
     ↓
provider idempotency
```

---

# 270. Error mapping

Canonical errors:

```text
IDEMPOTENCY_CONFLICT
IDEMPOTENCY_IN_PROGRESS
IDEMPOTENCY_RESULT_UNAVAILABLE
IDEMPOTENCY_RECONCILIATION_REQUIRED
```

---

# 271. `IDEMPOTENCY_IN_PROGRESS`

Retryable after delay.

---

# 272. `IDEMPOTENCY_CONFLICT`

Not retryable with same key.

New corrected request → new key.

---

# 273. `IDEMPOTENCY_RECONCILIATION_REQUIRED`

Admin/provider-specific operation awaiting resolution of ambiguous external outcome.

---

# 274. Public UX

Do not expose internal technical terminology unnecessarily.

Example:

```text
Ваш запрос уже обрабатывается.
```

---

# 275. Admin UX

May show:

```text
Операция уже выполняется
Повторный запрос распознан
Ожидается сверка с внешним сервисом
```

---

# 276. IDEM-AP-001

**Disable button in React = idempotency**

---

# 277. IDEM-AP-002

**In-memory Map protects Feedback duplicates**

---

# 278. IDEM-AP-003

**Check SELECT then INSERT with no unique constraint**

---

# 279. IDEM-AP-004

**New random idempotency key on every retry**

---

# 280. IDEM-AP-005

**Same key with different payload returns old result**

---

# 281. IDEM-AP-006

**Fingerprint includes volatile timestamp so every retry differs**

---

# 282. IDEM-AP-007

**Fingerprint uses raw non-normalized URL/phone inconsistently**

---

# 283. IDEM-AP-008

**Publish retry creates another QuestionnaireRevision**

---

# 284. IDEM-AP-009

**Builder Generate retry creates another Snapshot**

---

# 285. IDEM-AP-010

**Create Casting from Feedback retry creates second Casting**

---

# 286. IDEM-AP-011

**Booked→Project retry creates duplicate Project**

---

# 287. IDEM-AP-012

**AI provider timeout causes uncontrolled duplicate generations**

---

# 288. IDEM-AP-013

**AI Regenerate incorrectly returns prior output because request is “same”**

---

# 289. IDEM-AP-014

**WhatsApp timeout immediately triggers blind resend**

---

# 290. IDEM-AP-015

**Social publish timeout immediately creates second post**

---

# 291. IDEM-AP-016

**Provider retry uses a new provider idempotency key**

---

# 292. IDEM-AP-017

**Webhook replay creates duplicate domain mutation**

---

# 293. IDEM-AP-018

**Already processed webhook returns 500**

---

# 294. IDEM-AP-019

**Current state used to fabricate original replay result**

---

# 295. IDEM-AP-020

**Idempotency used instead of expectedVersion for conflicting edits**

---

# 296. IDEM-AP-021

**Same checksum means same upload request**

---

# 297. IDEM-AP-022

**Similar Feedback text heuristically deduplicated as same intent**

---

# 298. IDEM-AP-023

**Expired PROCESSING record immediately reclaimed without reconciliation**

---

# 299. IDEM-AP-024

**UNKNOWN_OUTCOME automatically treated as FAILED**

---

# 300. IDEM-AP-025

**Unresolved external outcome idempotency record purged by retention age**

---

# 301. IDEM-AP-026

**Replay creates duplicate Audit event for same business mutation**

---

# 302. IDEM-AP-027

**Replay creates duplicate analytics conversion**

---

# 303. IDEM-AP-028

**Old Search reindex treated as same idempotency use case rather than latest-wins**

---

# 304. IDEM-AP-029

**Client raw idempotency key acts as authorization token**

---

# 305. IDEM-AP-030

**Provider event ID trusted before webhook signature verification**

---

# 306. Idempotency quality gate

Перед implementation MUST быть определены:

- [ ] idempotent operation catalogue;
- [ ] scope for every protected operation;
- [ ] key source;
- [ ] key validation;
- [ ] key hashing;
- [ ] request fingerprint;
- [ ] canonical serialization;
- [ ] database uniqueness;
- [ ] atomic claim;
- [ ] PROCESSING recovery;
- [ ] completed result representation;
- [ ] same-key/same-request replay;
- [ ] same-key/different-request conflict;
- [ ] transient failure retry semantics;
- [ ] UNKNOWN_OUTCOME state;
- [ ] provider reconciliation;
- [ ] provider-native idempotency integration;
- [ ] webhook replay handling;
- [ ] job idempotency;
- [ ] revision/artifact identity;
- [ ] retention period;
- [ ] unresolved-record protection;
- [ ] authorization on result replay;
- [ ] metrics/logging;
- [ ] deterministic tests.

---

# 307. E2E-IDEM-001 — Double Feedback submit

Send identical request twice concurrently with same key.

Expected:

```text
one Feedback
one business FeedbackCreated event
same result returned
```

---

# 308. E2E-IDEM-002 — Feedback changed payload

Same key, changed message.

Expected:

```text
IDEMPOTENCY_CONFLICT
```

---

# 309. E2E-IDEM-003 — Feedback crash after commit

Commit Feedback, simulate connection failure before HTTP response.

Retry same key.

Expected same Feedback.

---

# 310. E2E-IDEM-004 — Builder double Generate

Two concurrent Generate requests same session/version/key.

Expected:

```text
one GenerationSnapshot
one Artifact identity
```

---

# 311. E2E-IDEM-005 — Builder changed after generation

Edit session, reuse old key.

Expected conflict.

---

# 312. E2E-IDEM-006 — Builder new generation

Edit session + new key.

Expected new GenerationSnapshot.

---

# 313. E2E-IDEM-007 — Publish double-click

Two concurrent Questionnaire Publish requests same key.

Expected one Revision.

---

# 314. E2E-IDEM-008 — Publish response lost

Revision commits; response lost.

Retry returns exact original revision number/ID.

---

# 315. E2E-IDEM-009 — Publish then edit

Publish R3.

Edit Draft.

New key.

Publish R4.

Valid.

---

# 316. E2E-IDEM-010 — Finalize Grid double-click

Same key produces one GridRevision.

---

# 317. E2E-IDEM-011 — Casting from Feedback retry

Same key produces one Casting and one provenance link.

---

# 318. E2E-IDEM-012 — Separate Casting intent

Same Feedback, new deliberate command/new key.

Domain rules decide whether second Casting is permitted; idempotency does not automatically collapse it.

---

# 319. E2E-IDEM-013 — Booked Project retry

Same key creates one Project Draft.

---

# 320. E2E-IDEM-014 — Opportunity transition response lost

Transition to Callback commits.

Response lost.

Retry same key returns original Callback transition result even if Opportunity later advances.

---

# 321. E2E-IDEM-015 — AI generation retry

Provider call/request retry same semantic key.

One AIDraft/Analysis request identity.

---

# 322. E2E-IDEM-016 — AI Regenerate

Explicit Regenerate receives new intent/key and creates new output.

---

# 323. E2E-IDEM-017 — Notification event replay

Same `FeedbackCreated` event delivered twice.

One Notification semantic intent.

---

# 324. E2E-IDEM-018 — Notification provider stable key

Retry Delivery uses same provider idempotency identity.

---

# 325. E2E-IDEM-019 — WhatsApp unknown outcome

Provider receives message; response lost.

System enters UNKNOWN_OUTCOME/reconciliation path, not immediate duplicate send.

---

# 326. E2E-IDEM-020 — Social retry after success

Instagram Target already published.

Retry command returns existing provider post state; no second post.

---

# 327. E2E-IDEM-021 — Social unknown outcome

Timeout after submission.

System reconciles before reposting.

---

# 328. E2E-IDEM-022 — Webhook replay

Valid same provider event received five times.

One domain effect.

All replay calls receive provider-compatible acknowledgement.

---

# 329. E2E-IDEM-023 — Webhook same ID changed payload

Rejected/logged as conflict/security anomaly.

---

# 330. E2E-IDEM-024 — Job duplicate

Same PDF job enqueued twice.

One Artifact semantic output.

---

# 331. E2E-IDEM-025 — Renderer version change

Same QuestionnaireRevision with renderer v4 after prior v3.

New Artifact identity is allowed.

---

# 332. E2E-IDEM-026 — QR duplicate

Same canonical URL/settings.

Existing validated QR reused.

---

# 333. E2E-IDEM-027 — QR URL change

Changed canonical URL generates different identity.

---

# 334. E2E-IDEM-028 — Media derivative duplicate

Two identical derivative jobs.

One derivative semantic output.

---

# 335. E2E-IDEM-029 — Search latest-wins

v10 and v11 indexing jobs.

v10 does not overwrite v11.

This is generation/freshness behavior rather than duplicate business creation.

---

# 336. E2E-IDEM-030 — Archive repeated

Archive already archived Project.

No duplicate side effects/history transition pretending second archive occurred.

---

# 337. E2E-IDEM-031 — Purge repeated

Same purge request while purge in progress.

Returns existing purge workflow.

---

# 338. E2E-IDEM-032 — Purge completed replay

Same key after completed purge returns completed state.

---

# 339. E2E-IDEM-033 — Key unauthorized replay

Another anonymous/public context obtains idempotency key but lacks original access context.

Cannot retrieve sensitive result.

---

# 340. E2E-IDEM-034 — PROCESSING crash recovery

Claim operation then terminate process before business commit.

After recovery window, system verifies no result exists and safely retries.

---

# 341. E2E-IDEM-035 — Commit-before-crash recovery

Commit business result then terminate process before response.

Recovery finds existing result and marks/replays completed.

---

# 342. E2E-IDEM-036 — Unknown external recovery

Crash at ambiguous external boundary.

Record is not automatically reclaimed as unsent.

---

# 343. E2E-IDEM-037 — Retention cleanup

Completed expired idempotency record purged according to policy.

No business source removed.

---

# 344. E2E-IDEM-038 — Unknown-outcome retention

Expired-by-age but unresolved UNKNOWN_OUTCOME is not deleted by generic cleanup.

---

# 345. E2E-IDEM-039 — Audit duplicate

Replay completed command creates no second business Audit entry.

---

# 346. E2E-IDEM-040 — Analytics duplicate

Transport replay creates one semantic conversion/business event only.

---

# 347. Idempotency traceability

Canonical:

```text
USER INTENT
   ↓
IDEMPOTENCY KEY
   ↓
REQUEST FINGERPRINT
   ↓
ATOMIC CLAIM
   ↓
APPLICATION COMMAND
   ↓
BUSINESS RESULT
   ↓
PERSIST RESULT LINK/SNAPSHOT
   ↓
REPLAY
```

---

# 348. External side-effect traceability

```text
SEMANTIC DELIVERY
      ↓
DURABLE DELIVERY RECORD
      ↓
STABLE PROVIDER IDEMPOTENCY KEY
      ↓
EXTERNAL CALL
      ↓
┌─────────┬────────────┬────────────────┐
▼         ▼            ▼
SENT    FAILED     OUTCOME_UNKNOWN
                       ↓
                  RECONCILIATION
```

---

# 349. Webhook traceability

```text
WEBHOOK
  ↓
VERIFY SIGNATURE
  ↓
EVENT ID
  ↓
IDEMPOTENCY CLAIM
  ↓
APPLICATION HANDLER
  ↓
COMPLETED
  ↓
REPLAY = ACK
```

---

# 350. Questionnaire publication layers

```text
Publish Intent
    ↓
QuestionnaireRevision          ← idempotency layer 1
    ↓
PDF Artifact                   ← idempotency layer 2
    ↓
QR Artifact                    ← deterministic identity
```

---

# 351. Feedback layers

```text
Feedback Submit                ← idempotency layer 1
    ↓
FeedbackCreated Outbox
    ↓
Notification Intent            ← idempotency layer 2
    ↓
Channel Delivery               ← idempotency layer 3
    ↓
Provider Native Idempotency    ← layer 4 where supported
```

---

# 352. Social layers

```text
Approved Post Version
    ↓
Social Target
    ↓
Publish Intent
    ↓
Provider
    ↓
Published / Unknown Outcome
```

---

# 353. AI layers

```text
Human Generate Intent
    ↓
AI Request Identity
    ↓
Source Snapshot Hash
    ↓
Provider
    ↓
AI Output
```

Explicit Regenerate:

```text
NEW HUMAN INTENT
→ NEW REQUEST ID
```

---

# 354. Idempotency compliance criteria

Реализация соответствует DOC-103, если:

1. duplicate delivery is assumed possible;
2. business effects are idempotent where duplication matters;
3. critical idempotency state is durable;
4. each protected operation has explicit scope;
5. keys are opaque/bounded;
6. raw sensitive keys are not unnecessarily stored/logged;
7. same key + same intent returns same semantic result;
8. same key + changed intent returns conflict;
9. request fingerprinting is deterministic;
10. volatile transport metadata is excluded from fingerprint;
11. database uniqueness protects concurrent claims;
12. check-then-insert race cannot create duplicate effects;
13. completed state is written only after authoritative commit;
14. crash after commit is recoverable without duplicate creation;
15. PROCESSING records have recovery/reconciliation path;
16. UNKNOWN_OUTCOME is first-class;
17. ambiguous external side effects are not blindly retried;
18. provider-native idempotency is used when available;
19. provider retries reuse same provider key;
20. Feedback submit is idempotent;
21. Builder Generate is idempotent;
22. Questionnaire Publish is idempotent;
23. Emotional Grid Finalize is idempotent;
24. Casting-from-Feedback is idempotent;
25. Booked→Project creation is idempotent;
26. Opportunity high-value transition retry can replay original result;
27. AI retry is distinguished from explicit Regenerate;
28. Notification intent and Delivery have separate protection layers;
29. Social Target publishing is idempotent;
30. provider webhook processing is replay-safe;
31. duplicate webhook receives acknowledgment rather than duplicate mutation;
32. Background Jobs declare idempotency semantics;
33. artifact generation uses deterministic semantic identity;
34. QR generation uses canonical URL/settings identity;
35. Media derivatives use source/transform/processor identity;
36. current derived work uses freshness/latest-wins where appropriate rather than misuse of idempotency;
37. idempotency and optimistic concurrency remain separate mechanisms;
38. result replay does not fabricate current state as original outcome;
39. result replay still respects access control;
40. idempotency record retention covers realistic replay window;
41. unresolved ambiguous outcomes are not age-purged blindly;
42. duplicate transport delivery does not duplicate business Audit;
43. duplicate transport delivery does not duplicate business Analytics;
44. idempotency behavior is covered by concurrency/crash/replay E2E tests.

---

# 355. Финальная доктрина

> **Платформа должна исходить из того, что любой HTTP-запрос, Server Action, Outbox event, Background Job или provider webhook может быть доставлен повторно. Idempotency обеспечивает не exactly-once transport, а exactly-one semantic business effect там, где повтор опасен. Для локальных транзакций это достигается durable key/fingerprint, уникальным atomic claim и сохранением результата; для внешних side effects — отдельным durable Delivery/Target intent, стабильным provider idempotency key и обязательным `UNKNOWN_OUTCOME`/reconciliation path. Idempotency не заменяет optimistic concurrency, domain uniqueness, rate limiting или latest-wins freshness — каждый механизм решает отдельный класс конфликтов.**