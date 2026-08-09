# SERVER ACTIONS & APPLICATION BOUNDARY

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная архитектура Server Actions, Route Handlers, Commands, Queries и транзакционных границ

**Целевой файл:** `docs/api/server-actions.md`  
**Документ:** DOC-100  
**Статус:** ✅ Completed  
**Тип:** API / Application Boundary / Next.js / Server Actions / Commands / Queries

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/business-rules.md`
- `docs/product/product-principles.md`
- `docs/domain/domain-model.md`
- `docs/domain/entity-relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions-and-history.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/components.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`
- `docs/architecture/background-jobs.md`
- `docs/architecture/cache.md`
- `docs/architecture/search.md`
- `docs/architecture/ai.md`
- `docs/architecture/notifications.md`
- `docs/database/database-architecture.md`
- `docs/database/data-dictionary.md`
- `docs/database/visibility-and-access.md`
- `docs/database/validation-and-constraints.md`
- `docs/database/deletion-and-retention.md`

---

# 1. Назначение документа

Настоящий документ определяет application/API boundary платформы.

Он фиксирует:

1. назначение Server Actions;
2. назначение Route Handlers;
3. Commands;
4. Queries;
5. Application Services;
6. Domain Services;
7. Query Services;
8. Repository boundary;
9. ORM boundary;
10. authentication;
11. authorization;
12. visibility context;
13. input validation;
14. transaction management;
15. concurrency;
16. idempotency;
17. Audit;
18. Outbox;
19. Background Jobs;
20. file/media operations;
21. AI operations;
22. public Builder;
23. Feedback;
24. Castings;
25. Opportunity;
26. Notifications;
27. Social Publishing;
28. error handling;
29. redirect/revalidation;
30. security;
31. observability;
32. testing.

---

# 2. Главная доктрина

> **Server Action или HTTP endpoint является адаптером доставки запроса в Application Layer, а не местом хранения бизнес-логики.**

Canonical:

```text
CLIENT
  ↓
SERVER ACTION / ROUTE HANDLER
  ↓
AUTHENTICATION
  ↓
AUTHORIZATION / CONTEXT
  ↓
INPUT VALIDATION
  ↓
APPLICATION COMMAND / QUERY
  ↓
DOMAIN / QUERY SERVICE
  ↓
REPOSITORY / INFRASTRUCTURE
```

---

# 3. API identifiers

Используются:

```text
API-*
SA-*
RH-*
CMD-*
QRY-*
APP-*
API-INV-*
API-SEC-*
API-TXN-*
API-AP-*
E2E-API-*
```

---

# 4. API-INV-001 — UI Never Uses ORM Directly

Запрещено:

```text
React Client Component
→ Prisma/Drizzle
→ PostgreSQL
```

---

# 5. API-INV-002 — UI Never Owns Business Transaction

Клиент может инициировать operation.

Он не определяет:

- transaction scope;
- authoritative state;
- revision number;
- permission;
- publication validity.

---

# 6. API-INV-003 — Server Action ≠ Domain Service

Server Action не должен содержать десятки строк:

```text
if project...
if contact...
if published...
update table...
create audit...
enqueue...
```

---

# 7. Правильная граница

```text
Server Action
   ↓
Application Service
   ↓
Domain
```

---

# 8. API-INV-004 — ORM Is Infrastructure

ORM не является Application API.

---

# 9. ORM access

Допускается только через:

```text
Repository
Query Repository
Infrastructure Adapter
```

или иной контролируемый persistence abstraction.

---

# 10. Application model

Canonical:

```text
Presentation
     ↓
Application
     ↓
Domain
     ↓
Infrastructure
```

---

# 11. Presentation Layer

Включает:

```text
Server Components
Client Components
Server Actions
Route Handlers
Forms
HTTP adapters
```

---

# 12. Application Layer

Отвечает за:

- orchestration;
- authorization context;
- transaction boundary;
- repository coordination;
- Domain invocation;
- Audit;
- Outbox;
- idempotency;
- result mapping.

---

# 13. Domain Layer

Отвечает за:

- business invariants;
- state transitions;
- ownership;
- domain validation;
- lifecycle.

---

# 14. Infrastructure Layer

Включает:

```text
PostgreSQL
ORM
Media Storage
AI provider
WhatsApp
Email
Social APIs
PDF renderer
```

---

# 15. Commands vs Queries

Система различает:

```text
COMMAND
QUERY
```

---

# 16. Command

Изменяет authoritative or operational state.

Examples:

```text
UpdateProfile
SetPrimaryPortfolioItem
PublishQuestionnaire
GenerateBuilderSnapshot
CreateFeedback
TransitionOpportunity
ApplyAIDraft
PublishTheme
ScheduleSocialPost
```

---

# 17. Query

Читает данные без business mutation.

Examples:

```text
GetPublicProfile
GetAdminCasting
SearchPublicProfile
GetQuestionnaireReadiness
GetNotificationCenter
```

---

# 18. API-INV-005 — Query Does Not Mutate Domain

Query MAY:

- populate cache;
- update technical metrics;
- emit non-authoritative analytics where safe.

Query MUST NOT silently:

```text
publish
archive
mark opportunity handled
change professional fact
```

---

# 19. Command contract

Conceptually:

```text
Command<Input>
→ Application Service
→ Result<Output, Error>
```

---

# 20. Query contract

```text
Query<Criteria>
→ Query Service
→ Projection DTO
```

---

# 21. Server Actions

Server Actions SHOULD be preferred for:

```text
authenticated Admin form mutation
simple same-app mutations
progressive-enhancement forms
```

---

# 22. Suitable examples

```text
saveProfileAction
setPrimaryPhotoAction
archiveProjectAction
publishQuestionnaireAction
markNotificationReadAction
```

---

# 23. Route Handlers

Route Handlers SHOULD be preferred when operation requires explicit HTTP semantics.

---

# 24. Typical Route Handler use cases

```text
public Feedback submission
public Builder session endpoints where external/client API useful
media upload/download
PDF download
QR delivery
token-scoped access
provider OAuth callbacks
provider webhooks
social callbacks
streaming
health checks
```

---

# 25. API-INV-006 — One Mechanism Is Not Forced Everywhere

Architecture deliberately permits:

```text
Server Action
AND
Route Handler
```

according to boundary needs.

---

# 26. Server Components

Server Components MAY call Query Services directly on server.

They SHOULD NOT mutate Domain during render.

---

# 27. Client Components

Client Components call:

- Server Action;
- safe Route Handler;
- passed callbacks.

Never repository/DB.

---

# 28. Application Service naming

Recommended verbs:

```text
Create*
Update*
Publish*
Archive*
Restore*
Finalize*
Generate*
Apply*
Confirm*
Transition*
Schedule*
Retry*
```

---

# 29. Query Service naming

Recommended:

```text
Get*
List*
Search*
Resolve*
Preview*
Check*
```

---

# 30. Command input

Command accepts semantic application input.

Not raw ORM row.

---

# 31. Example

Bad:

```text
updateProject(data: Prisma.ProjectUpdateInput)
```

---

# 32. Good

```text
UpdateProjectCommand {
  projectId
  expectedVersion
  title
  projectType
  description
  visibility
}
```

---

# 33. API-INV-007 — ORM Types Do Not Escape Infrastructure

Presentation must not depend on:

```text
Prisma.Project
Drizzle inferred DB row
raw SQL result
```

as public API contract.

---

# 34. Why

Prevents coupling:

```text
DB schema
→ UI
```

and accidental private field serialization.

---

# 35. Command IDs

High-impact command SHOULD have semantic trace identity where required.

Possible:

```text
command_id
request_id
idempotency_key
correlation_id
```

---

# 36. Correlation ID

One business operation SHOULD retain same correlation context through:

```text
HTTP
→ Application
→ transaction
→ Audit
→ Outbox
→ Background Job
```

---

# 37. Authentication

Every protected operation establishes server-side authenticated principal.

---

# 38. Admin Server Action

Canonical:

```text
Server Action
  ↓
requireAdminSession()
  ↓
Application Command
```

---

# 39. No trusted client user ID

Prohibited:

```text
{ adminUserId: "..." }
```

from browser as authority for current actor.

---

# 40. Actor identity

Resolved from authenticated server session.

---

# 41. Public operation

Examples:

```text
Feedback
Builder
public Search
public profile
```

do not require Admin session.

They receive explicitly restricted Public principal/context.

---

# 42. Principal model

Recommended:

```text
AnonymousPublic
TokenScopedVisitor
AuthenticatedAdmin
SystemWorker
MigrationActor
```

---

# 43. AI is not authenticated principal

AI output does not become an authorization actor capable of arbitrary application commands.

---

# 44. VOP

VOP safe technical actions execute under explicitly limited System/VOP principal.

---

# 45. Authorization

Authentication answers:

> Who?

Authorization answers:

> May this principal perform this operation on this resource?

---

# 46. API-SEC-001 — Authorization Per Command

Do not authorize once merely because user can load `/admin`.

---

# 47. Resource authorization

Must be checked for affected resource.

---

# 48. Future role support

Even if initial site has one main Admin, command contracts MUST not assume:

```text
authenticated = unrestricted
```

forever.

---

# 49. Visibility vs authorization

Read query requires both:

```text
principal authorization
+
context visibility
```

---

# 50. Example

Admin can read private Contact.

Public Profile query cannot, regardless of same underlying Repository.

---

# 51. Input validation boundary

Server Action/Route Handler validates structural input before Application command.

---

# 52. Schema validation

Recommended:

```text
Zod or equivalent schema validation
```

at delivery/application boundary.

Library selection is implementation detail.

---

# 53. Validation stages

```text
raw request
↓
transport/schema validation
↓
normalized input
↓
Application command
↓
Domain validation
```

---

# 54. API-INV-008 — Transport Schema Is Not Domain Validation

A string being valid text does not mean Project is publishable.

---

# 55. Unknown form fields

Should not automatically be passed to persistence.

---

# 56. Mass assignment

Prohibited:

```text
db.project.update({
  data: formData
})
```

---

# 57. Field allowlist

Command explicitly maps accepted properties.

---

# 58. Normalization

Examples:

```text
trim
empty → null
URL canonicalization
phone normalization
locale normalization
```

must happen in controlled layer.

---

# 59. Server-generated fields

Client cannot authoritatively supply:

```text
created_at
published_at
published_by
revision_number
version increment
Audit actor
job lease
processing_state
```

---

# 60. Expected version

Client MAY submit:

```text
expectedVersion
```

for optimistic concurrency.

Server verifies it.

---

# 61. Transaction boundary

Application Service owns DB transaction.

---

# 62. Canonical mutation transaction

```text
BEGIN
  reload current state
  authorization/resource validation
  expected-version validation
  domain validation
  source mutation
  Audit
  Outbox
COMMIT
```

---

# 63. External side effects

Outside transaction:

```text
AI request
WhatsApp
Email
Social API
PDF render
video transcode
```

---

# 64. API-TXN-001 — No External Network Call Inside Long DB Transaction

Normative.

---

# 65. Why

Avoid:

- lock duration;
- provider timeout holding transaction;
- DB contention;
- ambiguous rollback after external success.

---

# 66. Post-commit work

Use:

```text
Outbox
or
same-transaction BackgroundJob intent
```

as defined DOC-076.

---

# 67. Same transaction job creation

Allowed when exact one durable internal job is known.

---

# 68. Outbox

Preferred for decoupled fan-out.

---

# 69. Example Publish Questionnaire

```text
publishQuestionnaireAction
   ↓
PublishQuestionnaireCommand
   ↓
BEGIN
  validate Draft/current sources
  create QuestionnaireRevision
  update current published pointer
  Audit
  Outbox QuestionnairePublished
COMMIT
   ↓
PDF/QR jobs
```

---

# 70. PDF must not render before commit

Normative.

---

# 71. Example Feedback

```text
POST /feedback
   ↓
CreateFeedbackCommand
   ↓
BEGIN
  validate public form
  idempotency
  create Feedback
  attach metadata
  Outbox FeedbackCreated
COMMIT
   ↓
Notification subsystem
```

---

# 72. API-INV-009 — Notification Failure Cannot Roll Back Feedback

Post-commit.

---

# 73. Example Emotional Grid

```text
finalizeGridAction
   ↓
FinalizeEmotionalGridCommand
   ↓
transaction-time validation
   ↓
GridRevision
   ↓
Outbox
   ↓
EMOTIONAL_GRID_RENDER
```

---

# 74. Example Opportunity

```text
transitionOpportunityAction
   ↓
TransitionOpportunityStage
   ↓
BEGIN
 current version
 transition legality
 stage update
 history
 Audit
 Outbox
COMMIT
```

---

# 75. Idempotency

Idempotency is Application concern for commands that can be repeated due to:

```text
double click
network retry
browser retry
worker retry
provider callback replay
```

---

# 76. Idempotency key sources

Could be:

```text
client-generated request key
server-generated intent ID
resource + semantic operation
provider event ID
```

---

# 77. API-INV-010 — Idempotency Is Durable

Critical public/business operations cannot rely only on in-memory Map/cache.

---

# 78. Strong idempotency examples

Required for:

```text
Feedback submit
Builder Generate
Create Casting from Feedback
Project Draft from Booked
Notification delivery
Social publish
provider webhook
```

---

# 79. Idempotent response

Duplicate same request SHOULD return semantic existing result.

Example:

```text
CreateFeedback
→ Feedback #123
```

retry:

```text
→ same Feedback #123
```

---

# 80. Same key/different request

Must reject as idempotency conflict.

---

# 81. Idempotency contract detailed in DOC-103

DOC-100 establishes architectural requirement.

---

# 82. Server Action result

Server Action SHOULD return stable application result.

---

# 83. Recommended shape

Conceptually:

```text
{
  ok: true,
  data: ...
}
```

or:

```text
{
  ok: false,
  error: {
    code,
    fields?,
    messageKey?,
    correlationId?
  }
}
```

Exact standard defined DOC-101/102.

---

# 84. Do not return raw Error

Prohibited:

```text
return error.stack
```

---

# 85. Do not return raw ORM error

Example:

```text
Unique constraint failed on index portfolio_items_profile_id...
```

must be translated.

---

# 86. Redirects

Server Action MAY return/perform navigation behavior after successful operation according to Next.js conventions.

But command success must exist independently of browser redirect.

---

# 87. Revalidation

`revalidatePath`, tag invalidation or equivalent are Presentation/cache concerns.

---

# 88. API-INV-011 — Cache Revalidation Is Not Business Commit

Correct:

```text
Command commits
→ then cache invalidation/revalidation
```

---

# 89. Cache invalidation failure

Cannot undo professional Source commit.

System may retry/rebuild.

---

# 90. Queries

Queries return Projections, not raw aggregate tables.

---

# 91. Public query

Example:

```text
GetPublicProfileQuery
```

returns:

```text
PublicProfileProjection
```

---

# 92. Admin query

Example:

```text
GetAdminProjectQuery
```

may include broader fields.

---

# 93. Builder query

Uses:

```text
BuilderEligibilityProjection
```

---

# 94. Historical query

Uses exact:

```text
QuestionnaireRevision
BuilderGenerationSnapshot
GridRevision
```

not current source.

---

# 95. API-INV-012 — One Repository Query Is Not Reused Blindly Across Visibility Contexts

Avoid:

```text
getProject()
→ Public
→ Admin
→ Builder
```

with client-side filtering.

---

# 96. Query repositories

May optimize read operations separately from Domain repositories.

---

# 97. CQRS

Formal distributed CQRS is not required.

Logical command/query separation is sufficient.

---

# 98. Public Profile queries

Should be cacheable where safe.

---

# 99. Admin queries

Generally dynamic/private.

---

# 100. Query pagination

List/search APIs MUST bound result count.

---

# 101. Cursor vs offset

Implementation chooses based on dataset/query.

No unbounded “return everything” for operational lists.

---

# 102. Filtering

Filter values validated.

---

# 103. Sorting

Only allowlisted sort fields/directions.

No raw SQL column submitted by client.

---

# 104. Search query

Public Search endpoint delegates to SearchQueryService.

It does not build arbitrary SQL from query text.

---

# 105. File upload boundary

Media upload requires dedicated request path capable of handling binary safely.

---

# 106. Server Action binary

Small/simple file upload MAY use form Server Action if runtime constraints allow.

---

# 107. Dedicated Route Handler preferred when

```text
large file
streaming
progress
chunked upload
special headers
```

---

# 108. Upload workflow

```text
request
↓
authentication/context
↓
transport size validation
↓
staging
↓
MIME/security validation
↓
Media Application Service
```

---

# 109. API-SEC-002 — Client Filename Is Untrusted

Never derive storage path directly.

---

# 110. Download route

Protected media/artifact delivery SHOULD use application-controlled Route Handler or safe storage abstraction.

---

# 111. Public media

May use stable public-safe route/CDN abstraction.

---

# 112. Private attachments

Route Handler:

```text
authenticate
authorize source
resolve storage locator server-side
stream
```

---

# 113. API-SEC-003 — Storage Locator Is Never Client Authority

Request never says:

```text
/download?path=/data/...
```

---

# 114. Public Builder actions

Public Builder needs a constrained API/application surface.

---

# 115. Canonical Builder commands

```text
CreateBuilderSession
SelectBuilderTemplate
UpdateBuilderSection
SelectBuilderItem
RemoveBuilderItem
UpdateBuilderTemporaryContext
GenerateBuilderSnapshot
```

---

# 116. Builder query commands separation

Queries:

```text
GetBuilderSession
ListBuilderEligibleItems
PreviewBuilder
```

---

# 117. Builder Session ID

Opaque identifier.

---

# 118. Builder ownership/access

Anonymous session access must use session-specific secure capability/context.

Knowledge of UUID alone is insufficient if additional session protection is used.

Exact security in DOC-140+.

---

# 119. API-INV-013 — Builder Client Cannot Submit Arbitrary Data Snapshot

Client submits selections/config.

Server builds snapshot from current authorized Source.

---

# 120. Generate Builder

Must reload all selected entities server-side.

---

# 121. Feedback API

Public endpoint accepts only declared Feedback fields.

---

# 122. Public Feedback cannot set

```text
workflow_state
responsible_admin
admin_notes
casting_id
Opportunity stage
notification recipient
```

---

# 123. Public feedback type

Must be allowed public input value.

---

# 124. Attachment upload

Attachment linked only after security/type validation.

---

# 125. Spam/abuse controls

May occur before/around Application command:

```text
rate limit
bot protection
payload size
duplicate detection
```

Detailed security later.

---

# 126. Castings Admin actions

Recommended commands:

```text
CreateCasting
CreateCastingFromFeedback
UpdateCasting
ArchiveCasting
RunCastingAnalysis
ConfirmCastingRequirement
ModifyCastingRequirement
RejectCastingRequirement
CreateCastingQuestionnaireDraft
```

---

# 127. AI request command

`RunCastingAnalysis` creates durable AI generation intent/snapshot.

It should not synchronously perform a long provider call inside DB transaction.

---

# 128. Casting analysis result persistence

Worker/Application completion path writes:

```text
CastingAnalysisRevision
```

through capability service.

---

# 129. Confirm Requirement

Explicit Human Admin command.

---

# 130. Opportunity commands

Canonical:

```text
CreateOpportunity
TransitionOpportunityStage
UpdateOpportunityNextAction
AssignOpportunity
CreateProjectDraftFromBookedOpportunity
```

---

# 131. API-INV-014 — Generic `updateOpportunity(stage=...)` Is Prohibited

Stage uses transition-specific command.

---

# 132. Why

Preserves:

```text
transition rules
Human authority
history
Audit
Outbox
```

---

# 133. Contacts commands

Canonical:

```text
CreateContact
UpdateContact
UpdateContactVisibility
ArchiveContact
RestoreContact
```

---

# 134. Visibility changes

May deserve dedicated command rather than generic update because:

```text
cache
search
Builder eligibility
Audit
```

are affected.

---

# 135. Portfolio commands

```text
CreatePortfolioItem
UpdatePortfolioItem
SetPrimaryCloseUp
SetPrimaryFullBody
ArchivePortfolioItem
RestorePortfolioItem
ReorderPortfolio
```

---

# 136. Primary command is explicit

Avoid:

```text
UpdatePortfolioItem { isPrimary: true }
```

as generic mutation.

---

# 137. Emotional Grid commands

```text
CreateEmotionalGrid
UpdateGridDimensions
AssignGridCell
UpdateGridCellCrop
ConfirmGridCell
FinalizeEmotionalGrid
SetPrimaryEmotionalGrid
```

---

# 138. Confirm cell

Separate semantic command records Human authority.

---

# 139. Questionnaire commands

```text
CreateQuestionnaire
UpdateQuestionnaireDraft
ConfigureQuestionnaireSection
SelectQuestionnaireItem
RemoveQuestionnaireItem
SetPrimaryPublicQuestionnaire
PublishQuestionnaire
ArchiveQuestionnaire
```

---

# 140. Publish explicit

No generic:

```text
update(status='published')
```

---

# 141. BB Assistant commands

```text
GenerateBBDraft
EditBBDraft
DiscardBBDraft
ApplyBBDraft
```

---

# 142. Apply target

Uses target owning Application Service.

---

# 143. API-INV-015 — `ApplyBBDraft` Cannot Update Target Table Directly

Example:

```text
ApplyBBDraft
→ ProfileService.UpdateBiography
```

---

# 144. Theme commands

```text
CreateThemeDraft
UpdateThemeDraft
GenerateThemeProposal
ApplyThemeProposal
PublishTheme
ActivateTheme
ScheduleTemporaryTheme
RollbackTheme
```

---

# 145. Theme proposal

AI output is separate from Theme Draft.

---

# 146. Notifications commands

```text
MarkNotificationRead
MarkAllNotificationsRead
ArchiveNotification
RetryNotificationDelivery
UpdateNotificationPreferences
UpdateNotificationRecipient
SendTestNotification
```

---

# 147. Retry Delivery

Loads existing immutable semantic Delivery.

Client cannot provide arbitrary message text.

---

# 148. Social commands

```text
CreateSocialPost
UpdateSocialPost
ApproveSocialPost
ScheduleSocialPost
PublishSocialPostNow
CancelScheduledSocialPost
RetrySocialTarget
```

---

# 149. Provider callback/webhook

Must map to narrow Application command.

Example:

```text
HandleSocialProviderCallback
HandleNotificationProviderWebhook
```

---

# 150. Webhooks

Route Handler only.

---

# 151. Webhook pipeline

```text
HTTP request
↓
provider identification
↓
signature/auth validation
↓
payload schema
↓
replay/idempotency check
↓
Application event handler
```

---

# 152. API-SEC-004 — Webhook Payload Is Untrusted Until Verified

---

# 153. Webhook replay

Provider event ID or stable fingerprint stored durably.

---

# 154. Webhook scope

Provider callback may mutate only integration-specific state explicitly allowed.

---

# 155. OAuth callback

External OAuth authorization code handled server-side.

---

# 156. API-SEC-005 — OAuth Tokens Never Returned to Client Application State

Persist via secure credential mechanism.

---

# 157. Background Worker boundary

Worker is another Application adapter.

---

# 158. Worker flow

```text
claim BackgroundJob
↓
validate payload/version
↓
load exact source
↓
invoke registered handler/application service
↓
persist result
↓
mark job
```

---

# 159. API-INV-016 — Worker Does Not Execute Arbitrary Function Name from Payload

Handler registry is code allowlist.

---

# 160. Worker Domain mutations

Must use Domain/Application commands where mutation has business meaning.

---

# 161. Technical artifact handler

May write infrastructure-owned artifact state directly through appropriate service.

---

# 162. Scheduler boundary

Scheduler creates durable command/job intent.

Does not perform heavy business work itself.

---

# 163. System/internal commands

Should still have semantic contract.

Example:

```text
ExpireBuilderSession
RebuildSearchDocument
RecheckProfessionalLink
```

---

# 164. Audit integration

High-impact command decides/declares Audit requirement.

---

# 165. Audit actor

Resolved from execution context:

```text
AuthenticatedAdmin
System
Migration
VOP
```

---

# 166. API-INV-017 — Client Cannot Choose Audit Actor

---

# 167. Audit + mutation

Where required:

same transaction.

---

# 168. Outbox + mutation

Where reliable async work is required:

same transaction.

---

# 169. Analytics

Business command may produce:

```text
BusinessEvent
```

which later leads to Analytics.

Do not block main transaction on external analytics provider.

---

# 170. Cache invalidation

Application result/event describes changed resources.

Presentation/cache adapter invalidates relevant projections.

---

# 171. Recommended pattern

```text
CommandResult {
  entityId
  newVersion
  invalidationTags?
}
```

Implementation-specific cache tags remain outside Domain.

---

# 172. Domain must not import Next.js

Prohibited:

```text
domain/project.ts
import { revalidatePath } from "next/cache"
```

---

# 173. API-INV-018 — Domain Is Framework-Agnostic

Domain should not depend on:

```text
Next.js
React
HTTP Request
cookies()
revalidatePath()
```

---

# 174. Application may depend on abstract transaction/repository interfaces

Infrastructure supplies implementation.

---

# 175. Redirect security

Redirect target must be trusted/internal or explicitly validated.

---

# 176. Open redirect

Never accept arbitrary:

```text
returnUrl=https://attacker...
```

without strict policy.

---

# 177. Form action security

Server resolves target entity server-side and authorizes operation.

---

# 178. CSRF

Mutating HTTP endpoints require appropriate same-origin/CSRF protections according to selected Next.js/auth implementation.

Detailed controls DOC-140+.

---

# 179. Method semantics

Route Handlers SHOULD follow HTTP semantics:

```text
GET    read
POST   create/action
PATCH  partial semantic update where appropriate
DELETE destructive request where explicitly designed
```

---

# 180. But Domain command names remain semantic

A `POST` may represent:

```text
/publish
/finalize
/retry
```

when state transition semantics are clearer than generic PATCH.

---

# 181. API-INV-019 — REST Purity Does Not Override Domain Clarity

Explicit action endpoint is acceptable for business transition.

---

# 182. DELETE semantics

Do not map every UI “remove” to HTTP hard DELETE.

---

# 183. Archive

Should invoke Archive command.

---

# 184. Purge

Separate privileged destructive command.

---

# 185. Public response DTO

Field allowlist only.

---

# 186. Admin response DTO

Still must avoid:

```text
secrets
raw storage paths
unnecessary provider credentials
```

---

# 187. Error taxonomy

Detailed DOC-102.

High-level classes:

```text
VALIDATION
AUTHENTICATION
AUTHORIZATION
NOT_FOUND
CONFLICT
STALE_VERSION
RATE_LIMIT
DEPENDENCY
PROVIDER
INTERNAL
```

---

# 188. HTTP mapping

Route Handlers map Application errors to HTTP status.

Server Actions map same errors to structured action results.

---

# 189. API-INV-020 — HTTP Status Is Not Domain Error Model

Domain/Application uses stable error codes.

HTTP is transport mapping.

---

# 190. Not Found vs Forbidden

Security-sensitive endpoints MAY deliberately avoid revealing resource existence.

Policy defined security docs.

---

# 191. Exception handling

Unexpected exceptions:

```text
correlation ID
safe generic response
structured internal log
```

---

# 192. No stack traces to Public

Normative.

---

# 193. Database constraint errors

Expected constraint conflicts SHOULD be translated.

Unexpected constraints logged as internal errors.

---

# 194. Provider errors

External provider codes translated behind integration adapter.

---

# 195. Client UX

UI receives:

```text
stable error code
safe message
field mappings where applicable
```

---

# 196. Retryability

Error contract SHOULD indicate retryability where useful.

Example:

```text
AI_PROVIDER_UNAVAILABLE → retryable
VALIDATION_NOT_ELIGIBLE → not retryable until data changes
```

---

# 197. Rate limiting

Public/high-cost endpoints require rate controls.

Candidates:

```text
Feedback submit
Builder create/generate
Public Search
file upload
AI generation
login/auth
token access
```

---

# 198. Rate-limit key

Must not rely blindly on a user-controlled header.

Use trusted combination according to security design.

---

# 199. Abuse response

Rate limit does not reveal internal thresholds unnecessarily.

---

# 200. Request size

Bound:

```text
JSON body
text
file
number of selected IDs
query length
```

---

# 201. Bulk operations

Must set explicit max item count.

---

# 202. No giant form trust

Example Public Builder cannot submit 50,000 entity IDs.

---

# 203. Timeouts

Route/Server Action work should remain bounded.

---

# 204. Heavy work

Move to Worker.

---

# 205. Interactive vs asynchronous

Recommended:

## synchronous

```text
save Draft
update Contact
mark read
select Builder item
```

## asynchronous

```text
video transcode
PDF
Grid render
AI analysis
social publish
external notification
```

---

# 206. Generate PDF UX

Command should produce:

```text
generation accepted
snapshot/revision ID
artifact processing state
```

rather than hold one HTTP connection through full render when architecture uses async Worker.

---

# 207. Polling/status

Client may query artifact/job-derived status via domain-safe projection.

---

# 208. Job table not exposed directly

UI should query:

```text
QuestionnaireArtifact status
```

not raw BackgroundJob internals unless Admin System diagnostics.

---

# 209. Long-running AI

Same principle.

UI observes:

```text
AI generation status
```

through capability projection.

---

# 210. Observability

Every application entry SHOULD generate/propagate:

```text
request_id
correlation_id
principal type
operation name
duration
result class
```

---

# 211. Logging

Log:

```text
operation
entity ID
safe state
error code
duration
```

not full payload by default.

---

# 212. Metrics

Recommended:

```text
command latency
query latency
validation failures
conflicts
authorization failures
idempotency replays
provider failures
job dispatch lag
```

---

# 213. Sensitive operations

Security logging may record:

```text
login
visibility change
token revoke
purge
credential change
```

---

# 214. Application service testability

Application Services SHOULD be testable without browser/React.

---

# 215. Domain testability

Domain rules testable without Next.js/database where practical.

---

# 216. Server Action tests

Test:

```text
input mapping
auth requirement
Application command invocation
safe result mapping
```

---

# 217. Route Handler tests

Additionally:

```text
HTTP method/status
headers
body parsing
webhook signature
file/download behavior
```

---

# 218. Repository integration tests

Use real PostgreSQL/test DB for:

```text
transaction
unique conflicts
FK
version update
revision allocation
```

---

# 219. E2E test

Exercises browser/public/Admin through actual action boundary.

---

# 220. API-AP-001

**Client Component imports ORM**

---

# 221. API-AP-002

**Server Action contains full Domain workflow**

---

# 222. API-AP-003

**Route Handler directly updates table for Opportunity stage**

---

# 223. API-AP-004

**`update(data=formData)` mass assignment**

---

# 224. API-AP-005

**Client sets `published_by`**

---

# 225. API-AP-006

**Client sets Revision number**

---

# 226. API-AP-007

**External AI call inside DB transaction**

---

# 227. API-AP-008

**Send WhatsApp before Feedback transaction commits**

---

# 228. API-AP-009

**Background work launched with fire-and-forget Promise after response**

---

# 229. API-AP-010

**Cache revalidation treated as successful business persistence**

---

# 230. API-AP-011

**Public Builder sends complete document snapshot authored by browser**

---

# 231. API-AP-012

**UI submits hidden Project ID and Server trusts it**

---

# 232. API-AP-013

**Generic `updateOpportunity({stage})`**

---

# 233. API-AP-014

**Generic `updatePortfolio({isPrimary:true})`**

---

# 234. API-AP-015

**`ApplyBBDraft` directly updates Profile DB table**

---

# 235. API-AP-016

**Webhook accepted before signature verification**

---

# 236. API-AP-017

**Webhook can invoke arbitrary command from payload string**

---

# 237. API-AP-018

**OAuth token returned to Client Component**

---

# 238. API-AP-019

**Private attachment URL is direct filesystem path**

---

# 239. API-AP-020

**Raw ORM entity returned from Public endpoint**

---

# 240. API-AP-021

**Raw database error returned to user**

---

# 241. API-AP-022

**Raw provider error returned to Public**

---

# 242. API-AP-023

**Client chooses Admin audit actor**

---

# 243. API-AP-024

**Public form chooses Notification recipient**

---

# 244. API-AP-025

**Search GET request archives content as side effect**

---

# 245. API-AP-026

**Server Component publishes data while rendering**

---

# 246. API-AP-027

**Background Worker mutates business table bypassing Domain command**

---

# 247. API-AP-028

**Heavy video processing performed synchronously inside Server Action**

---

# 248. API-AP-029

**Idempotency exists only in browser disabled-button state**

---

# 249. API-AP-030

**Route Handler accepts arbitrary storage path**

---

# 250. Command catalogue — Profile

```text
UpdateProfileIdentity
UpdateProfileParameters
UpdateProfileFieldVisibility
UpdateProfileLocalization
PublishProfileContent
```

---

# 251. Command catalogue — Media

```text
CreateMediaUpload
FinalizeMediaUpload
ArchiveMediaAsset
RequestMediaDerivative
RequestMediaPurge
```

---

# 252. Command catalogue — Portfolio

```text
CreatePortfolioItem
UpdatePortfolioItem
SetPrimaryCloseUp
SetPrimaryFullBody
ReorderPortfolio
ArchivePortfolioItem
RestorePortfolioItem
```

---

# 253. Command catalogue — Emotional

```text
CreateEmotionalSession
UpdateEmotionalSession
AttachEmotionalMedia
CreateEmotionalGrid
UpdateGridCell
ConfirmGridCell
FinalizeEmotionalGrid
SetPrimaryEmotionalGrid
ArchiveEmotionalGrid
```

---

# 254. Command catalogue — Projects

```text
CreateProject
UpdateProject
ArchiveProject
CreateRole
UpdateRole
AttachRoleMedia
ReorderRoleMedia
SetRoleHomeShowcase
```

---

# 255. Command catalogue — Training/Achievements

```text
CreateTrainingItem
UpdateTrainingItem
ArchiveTrainingItem
CreateAchievement
UpdateAchievement
ArchiveAchievement
```

---

# 256. Command catalogue — Competencies

```text
SetSkill
UpdateSkill
RemoveSkill
SetLanguage
UpdateLanguage
RemoveLanguage
```

---

# 257. Command catalogue — Links/Contacts

```text
CreateProfessionalLink
UpdateProfessionalLink
ArchiveProfessionalLink
CreateContact
UpdateContact
UpdateContactVisibility
ArchiveContact
RestoreContact
```

---

# 258. Command catalogue — Questionnaire

```text
CreateQuestionnaire
UpdateQuestionnaireDraft
UpdateQuestionnaireSection
SelectQuestionnaireItem
RemoveQuestionnaireItem
PublishQuestionnaire
SetPrimaryPublicQuestionnaire
ArchiveQuestionnaire
```

---

# 259. Command catalogue — Builder

```text
CreateBuilderSession
UpdateBuilderSession
SelectBuilderTemplate
SelectBuilderItem
RemoveBuilderItem
GenerateBuilderSnapshot
ExpireBuilderSession
```

---

# 260. Command catalogue — Feedback/Casting

```text
CreateFeedback
UpdateFeedbackWorkflow
AssignFeedback
AddFeedbackNote
CreateCasting
CreateCastingFromFeedback
UpdateCasting
RunCastingAnalysis
ConfirmCastingRequirement
ModifyCastingRequirement
RejectCastingRequirement
CreateCastingQuestionnaireDraft
```

---

# 261. Command catalogue — Opportunity

```text
CreateOpportunity
TransitionOpportunityStage
UpdateOpportunityNextAction
AssignOpportunity
CreateProjectDraftFromBookedOpportunity
```

---

# 262. Command catalogue — AI/BB

```text
GenerateBBDraft
EditBBDraft
DiscardBBDraft
ApplyBBDraft
```

---

# 263. Command catalogue — Themes

```text
CreateThemeDraft
UpdateThemeDraft
GenerateThemeProposal
ApplyThemeProposal
PublishTheme
ActivateTheme
ScheduleTheme
RollbackTheme
```

---

# 264. Command catalogue — Notifications

```text
MarkNotificationRead
MarkAllNotificationsRead
ArchiveNotification
RetryNotificationDelivery
UpdateNotificationPreferences
UpdateNotificationRecipient
SendTestNotification
```

---

# 265. Command catalogue — Social

```text
ConnectSocialAccount
DisconnectSocialAccount
CreateSocialPost
UpdateSocialPost
ApproveSocialPost
ScheduleSocialPost
PublishSocialPost
CancelScheduledSocialPost
RetrySocialPublishTarget
```

---

# 266. Query catalogue — Public

```text
GetPublicHome
GetPublicProfile
GetPublicPortfolio
GetPublicEmotionalPortfolio
GetPublicProject
GetPublicQuestionnaireHub
GetPublicQuestionnaire
SearchPublicProfile
```

---

# 267. Query catalogue — Builder

```text
GetBuilderTemplates
GetBuilderSession
ListBuilderEligibleItems
SearchBuilderItems
PreviewBuilderQuestionnaire
GetBuilderArtifactStatus
```

---

# 268. Query catalogue — Admin

```text
GetAdminDashboard
GetAdminProfile
GetMediaLibrary
GetMediaUsage
GetQuestionnaireReadiness
GetFeedbackInbox
GetCastingWorkspace
GetOpportunityPipeline
GetNotificationCenter
GetVOPWorkspace
GetThemeWorkspace
GetBBDraft
GetSystemStatus
SearchAdmin
```

---

# 269. Query catalogue — Historical

```text
GetQuestionnaireRevision
GetBuilderGenerationSnapshot
GetEmotionalGridRevision
GetThemeRevision
GetAuditTrail
```

---

# 270. Server Action template

```text
"use server"

async function action(input) {
  const principal = requireAdminPrincipal()
  const parsed = validateTransportInput(input)

  const result = await applicationService.execute({
    principal,
    input: parsed,
    requestContext
  })

  return mapActionResult(result)
}
```

This is conceptual, not mandatory syntax.

---

# 271. Route Handler template

```text
HTTP request
   ↓
parse request
   ↓
request context
   ↓
authenticate/authorize
   ↓
validate transport input
   ↓
application command/query
   ↓
map result to HTTP response
```

---

# 272. Application command template

```text
execute(command, context):
  authorize
  normalize/validate
  begin transaction
    load current aggregate
    validate expectedVersion
    invoke domain operation
    persist changes
    audit
    outbox/job intent
  commit
  return semantic result
```

---

# 273. Query template

```text
query(criteria, context):
  validate criteria
  determine visibility scope
  resolve projection
  load read model
  apply authorization
  return DTO
```

---

# 274. Background command template

```text
claim job
 ↓
validate payload version
 ↓
load exact input
 ↓
check idempotency/current preconditions
 ↓
execute handler
 ↓
persist result
 ↓
mark success
```

---

# 275. API quality gate

Перед implementation MUST быть определены:

- [ ] Server Action conventions;
- [ ] Route Handler conventions;
- [ ] Command/Query separation;
- [ ] Application Service layer;
- [ ] repository boundary;
- [ ] ORM isolation;
- [ ] authentication context;
- [ ] authorization policy integration;
- [ ] input schema validation;
- [ ] normalization;
- [ ] optimistic concurrency;
- [ ] transaction ownership;
- [ ] Audit integration;
- [ ] Outbox integration;
- [ ] durable jobs;
- [ ] idempotency;
- [ ] stable results;
- [ ] stable error codes;
- [ ] public DTO allowlists;
- [ ] Builder command boundary;
- [ ] private media delivery;
- [ ] webhook validation;
- [ ] OAuth callback handling;
- [ ] provider adapter isolation;
- [ ] cache invalidation boundary;
- [ ] observability/correlation;
- [ ] rate limits;
- [ ] tests.

---

# 276. E2E-API-001 — Client ORM isolation

Build/static analysis confirms Client Components cannot import persistence module.

---

# 277. E2E-API-002 — Unauthorized Admin action

Call protected Server Action without Admin session.

No mutation occurs.

---

# 278. E2E-API-003 — Mass assignment

Submit:

```text
published_at
published_by
version=999
```

inside Project form.

Server ignores/rejects unauthorized fields.

---

# 279. E2E-API-004 — Stale version

Admin A loads Project v4.

Admin B updates to v5.

Admin A submits expectedVersion=4.

Result:

```text
STALE_VERSION
```

No overwrite.

---

# 280. E2E-API-005 — Publish Questionnaire

Server Action creates exact Revision in transaction.

PDF job starts only after commit.

---

# 281. E2E-API-006 — Publish rollback

Force transaction failure before commit.

No QuestionnaireRevision and no durable PDF job/outbox side effect survives.

---

# 282. E2E-API-007 — Feedback provider outage

Create Feedback while WhatsApp unavailable.

Feedback commits.

Notification delivery handles failure asynchronously.

---

# 283. E2E-API-008 — Duplicate Feedback

Same idempotency key submitted twice.

One Feedback entity.

---

# 284. E2E-API-009 — Same idempotency key, changed request

Second materially different payload with same key.

Rejected as idempotency conflict.

---

# 285. E2E-API-010 — Builder tampering

Public client submits hidden Project UUID.

Generate command reloads source and rejects it.

---

# 286. E2E-API-011 — Builder snapshot ownership

Browser cannot submit arbitrary frozen profile values.

Snapshot built server-side.

---

# 287. E2E-API-012 — Primary semantic command

Set new Primary Close-Up.

Application performs atomic switch.

No generic uncontrolled `is_primary` writes.

---

# 288. E2E-API-013 — Opportunity generic update

Attempt API call:

```text
PATCH stage=booked
```

outside transition command.

Rejected/not exposed.

---

# 289. E2E-API-014 — Opportunity concurrent transition

Only validated expected-version transition succeeds.

---

# 290. E2E-API-015 — BB Apply

Apply AIDraft.

Target Domain command invoked.

No direct repository shortcut.

---

# 291. E2E-API-016 — Theme AI

AI completion persists ThemeProposal only.

No active Theme changes.

---

# 292. E2E-API-017 — Private attachment

Unauthenticated request for Feedback attachment ID.

Denied without storage path disclosure.

---

# 293. E2E-API-018 — Webhook signature

Unsigned provider webhook.

Rejected before Application state update.

---

# 294. E2E-API-019 — Webhook replay

Same valid provider event twice.

One semantic state update.

---

# 295. E2E-API-020 — OAuth callback

Successful OAuth stores credentials through Secret boundary.

Browser response contains no access token.

---

# 296. E2E-API-021 — Heavy processing

Video upload request returns persisted processing state.

Transcode occurs in Worker, not request transaction.

---

# 297. E2E-API-022 — PDF artifact status

UI requests questionnaire generation status.

Receives artifact/domain status, not raw job lock/payload.

---

# 298. E2E-API-023 — Raw ORM field leakage

Public Profile DTO inspection contains no private/internal columns.

---

# 299. E2E-API-024 — Raw DB error

Trigger unique Primary conflict.

User receives stable conflict code, not DB constraint name.

---

# 300. E2E-API-025 — Provider error

AI provider returns internal provider stack/code.

UI receives normalized safe AI error.

---

# 301. E2E-API-026 — Search mutation

Public Search query produces no domain mutation.

---

# 302. E2E-API-027 — Archive semantics

Archive Project action performs Archive command.

No hard DELETE issued for professional Project.

---

# 303. E2E-API-028 — Purge authorization

Ordinary edit action cannot invoke hard Media purge.

---

# 304. E2E-API-029 — Cache failure

Command commits successfully.

Forced cache invalidation failure does not roll back professional mutation.

---

# 305. E2E-API-030 — Correlation

One publish operation has traceable:

```text
request
command
Audit
Outbox
PDF job
```

through correlation ID.

---

# 306. E2E-API-031 — Public rate limit

Abusive Builder/Feedback request rate is bounded without impacting existing persisted business records.

---

# 307. E2E-API-032 — Upload path attack

Filename:

```text
../../secret
```

cannot control storage locator.

---

# 308. E2E-API-033 — Worker handler allowlist

Craft job payload with arbitrary function/class name.

Worker refuses unsupported `job_type`.

---

# 309. E2E-API-034 — Worker business mutation

Business-significant worker path invokes owning Application Service and preserves validation/Audit.

---

# 310. E2E-API-035 — Server Component purity

Rendering Admin page cannot accidentally trigger Publish/Archive command.

---

# 311. E2E-API-036 — External redirect

Malicious untrusted `returnUrl`.

System refuses external open redirect.

---

# 312. Application flow overview

```text
                     CLIENT
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
    SERVER ACTION             ROUTE HANDLER
          │                         │
          └────────────┬────────────┘
                       ▼
                REQUEST CONTEXT
                       │
                       ▼
              AUTH / AUTHORIZATION
                       │
                       ▼
                INPUT VALIDATION
                       │
           ┌───────────┴───────────┐
           ▼                       ▼
        COMMAND                  QUERY
           │                       │
           ▼                       ▼
   APPLICATION SERVICE       QUERY SERVICE
           │                       │
           ▼                       ▼
         DOMAIN                PROJECTION
           │                       │
           ▼                       ▼
      REPOSITORIES              SAFE DTO
           │
           ▼
       POSTGRESQL
```

---

# 313. Mutation flow overview

```text
COMMAND
  ↓
Application Service
  ↓
BEGIN
  load
  authorize
  validate
  concurrency
  domain transition
  persist
  audit
  outbox/job intent
COMMIT
  ↓
async side effects
  ↓
projection/cache refresh
```

---

# 314. Public Builder boundary

```text
PUBLIC CLIENT
     ↓
Builder Route/Action
     ↓
Anonymous Session Context
     ↓
Builder Application Service
     ↓
Builder Eligibility Projection
     ↓
Persistent Session
     ↓
Generate Command
     ↓
AUTHORITATIVE REVALIDATION
     ↓
GenerationSnapshot
```

---

# 315. Feedback boundary

```text
PUBLIC FORM
   ↓
CreateFeedback
   ↓
Validation / Abuse Protection
   ↓
TRANSACTION
 Feedback + Outbox
   ↓
COMMIT
   ↓
Notification
```

---

# 316. AI boundary

```text
ADMIN ACTION
   ↓
AI Capability Application Service
   ↓
Context Builder
   ↓
Source Snapshot
   ↓
Generation Request / Job
   ↓
Provider
   ↓
Validated AI Output
   ↓
Human Apply Command
   ↓
Owning Domain
```

---

# 317. API compliance criteria

Реализация соответствует DOC-100, если:

1. Client UI never accesses ORM/DB directly;
2. Server Actions remain thin delivery adapters;
3. Route Handlers remain thin HTTP adapters;
4. business rules live in Application/Domain layers;
5. Commands and Queries are logically separated;
6. Queries do not mutate professional state;
7. ORM types do not define UI/API contracts;
8. all protected mutations resolve authenticated principal server-side;
9. client cannot choose authoritative actor identity;
10. authorization occurs per operation/resource;
11. structural validation occurs server-side;
12. Domain validation remains separate from transport validation;
13. mass assignment is impossible;
14. server-generated fields cannot be client-controlled;
15. optimistic concurrency is used where required;
16. Application Services own transaction boundaries;
17. external network calls occur outside long DB transactions;
18. Audit and Outbox commit with source mutation where required;
19. durable background work is not implemented as fire-and-forget Promise;
20. Feedback persists before Notification;
21. Questionnaire Revision persists before PDF generation;
22. Grid Revision persists before rendering;
23. Builder Snapshot is built server-side from revalidated Source;
24. Builder cannot submit arbitrary professional fact values;
25. primary photo changes use explicit semantic command;
26. Opportunity stage uses explicit transition command;
27. AI Apply routes through owning Domain;
28. provider webhooks are authenticated and idempotent;
29. OAuth secrets never become Client state;
30. private storage locators are never accepted/exposed as client authority;
31. public/admin/builder Queries use appropriate projections;
32. Public DTOs are allowlisted;
33. idempotency is durable where business duplication matters;
34. cache invalidation is post-commit/supporting behavior;
35. errors are normalized and safe;
36. provider/DB stack traces are not returned to user;
37. heavy operations move to Worker;
38. Worker job types are allowlisted/versioned;
39. workers do not bypass Domain for semantic mutations;
40. correlation IDs connect request → command → Audit → Outbox → Job;
41. rate/size controls exist for exposed costly/public actions;
42. Domain code remains framework-independent;
43. destructive Purge is separate from ordinary Archive;
44. API behavior is covered by integration/E2E tests.

---

# 318. Финальная доктрина

> **API/Application boundary платформы строится вокруг семантических Commands и контекстных Queries, а не вокруг прямого CRUD над PostgreSQL. Next.js Server Actions и Route Handlers отвечают за транспорт, principal, validation и преобразование результата, но не становятся владельцами бизнес-логики. Application Service формирует transaction boundary и соединяет Domain, Repository, Audit и Outbox; внешние и тяжёлые side effects выполняются только после commit через durable architecture. UI, AI, webhook, Background Worker и Public Builder проходят через те же owning-domain rules и никогда не получают прямого права модифицировать ORM/таблицы.**