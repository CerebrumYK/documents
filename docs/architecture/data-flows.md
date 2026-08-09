# ARCHITECTURE DATA FLOWS

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативные сквозные потоки данных и границы согласованности системы

**Целевой файл:** `docs/architecture/data-flows.md`  
**Документ:** DOC-072  
**Статус:** ✅ Completed  
**Тип:** Architecture / Data Flows / Transactions / Async Boundaries

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/overview.md`
- `docs/customer-journey/conversion-funnel.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/public-questionnaire-builder.md`
- `docs/ux/forms-and-validation.md`
- `docs/ux/ui-states.md`
- `docs/domain/domain-model.md`
- `docs/domain/relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/components.md`

---

# 1. Назначение документа

Настоящий документ определяет канонические **end-to-end data flows** продукта.

Документ фиксирует:

1. откуда поступают данные;
2. в какой точке выполняется validation;
3. когда формируется authoritative state;
4. где проходит transaction boundary;
5. какие действия выполняются синхронно;
6. какие действия переводятся в background jobs;
7. какие данные становятся snapshots;
8. как создаются revisions;
9. как работает projection layer;
10. когда инвалидируются cache/search/derived artifacts;
11. как работают external side effects;
12. где применяются retries/idempotency;
13. как flow восстанавливается при ошибке;
14. как исключается утечка private data;
15. как сохраняется provenance.

---

# 2. Data Flow IDs

Используется:

```text
DF-*
```

Категории:

```text
DF-PUB-*   Public
DF-ADM-*   Admin
DF-MED-*   Media
DF-EMO-*   Emotional
DF-QST-*   Questionnaire
DF-PQB-*   Public Builder
DF-FDB-*   Feedback
DF-CST-*   Casting
DF-OPP-*   Opportunity
DF-BBA-*   BB Assistant
DF-VOP-*   Virtual Operator
DF-THM-*   Themes
DF-NOT-*   Notifications
DF-SOC-*   Social
DF-SRCH-*  Search
DF-AUD-*   Audit
DF-ERR-*   Error/Recovery
```

---

# 3. Главный принцип

> **Authoritative data flow должен всегда иметь определённую точку фиксации истины. Всё, что происходит до этой точки, является input/draft/validation; всё, что происходит после неё, — projection, side effect, derived processing или integration.**

---

# 4. Canonical Write Flow

Все существенные writes SHOULD следовать:

```text
Client / Internal Trigger
        ↓
Authentication / Context
        ↓
Authorization
        ↓
Input Validation
        ↓
Application Command
        ↓
Domain Validation
        ↓
Concurrency Check
        ↓
DATABASE TRANSACTION
        ↓
Authoritative State Commit
        ↓
Audit / Outbox
        ↓
Transaction Commit
        ↓
Async Side Effects / Cache Invalidation / Notifications
```

---

# 5. Canonical Read Flow

```text
Request
   ↓
Context / Locale / Access Scope
   ↓
Projection Query
   ↓
Authorization / Visibility
   ↓
Read Model
   ↓
Optional Cache
   ↓
Response
```

---

# 6. DF-INV-001 — No Business Truth Before Commit

UI MUST NOT сообщать authoritative success до завершения authoritative persistence.

Пример:

```text
Feedback form submitted
→ notification sent
```

не означает успех, если Feedback не сохранён.

---

# 7. DF-INV-002 — External Side Effects After Commit

По умолчанию:

```text
DB commit
→ external side effect
```

а не:

```text
external side effect
→ maybe DB commit
```

---

# 8. DF-INV-003 — Client Never Defines Authorization

Client может предлагать:

```text
contact_id
portfolio_item_id
questionnaire selection
```

Server всегда перепроверяет eligibility.

---

# 9. DF-INV-004 — AI Output Is Input

AI response после получения проходит:

```text
schema validation
business validation
persistence as AI output
human review where required
```

---

# 10. DF-INV-005 — Async Job Uses Exact Input Identity

Background job MUST использовать точный:

```text
entity version
revision_id
snapshot_id
artifact specification
```

а не неопределённое `"current"` состояние, если оно может измениться до выполнения.

---

# 11. DF-INV-006 — Historical Flow Is Immutable

Historical Revision read flow MUST NOT join mutable current Master Data для содержимого документа.

---

# 12. Flow Consistency Classes

Используются два основных класса.

## Strongly Consistent

Должно быть атомарно в одной authoritative transaction boundary.

Примеры:

```text
Profile fact update
Primary photo switch
Contact visibility
Questionnaire publish
Opportunity transition
Feedback creation
```

## Eventually Consistent

Может завершиться позже после authoritative commit.

Примеры:

```text
search reindex
cache rebuild
notification delivery
analytics event
thumbnail generation
non-blocking PDF regeneration
```

---

# 13. DF-PUB-001 — Public Profile Read

## Goal

Показать актуальное опубликованное профессиональное представление.

## Flow

```text
Browser
  ↓
GET Public Profile
  ↓
Locale Resolution
  ↓
Public Projection Service
  ↓
Read published/eligible Profile domains
  ↓
Visibility filtering
  ↓
Public DTO
  ↓
SSR/Response
```

---

# 14. Public Profile Inputs

Могут включать:

```text
ActorProfile
Primary Portfolio
Selected Portfolio
Video
Emotional Grid
Featured Projects
Skills
Languages
Training
Achievements
Primary Questionnaire availability
Public Contacts
```

---

# 15. Public Profile Privacy Boundary

Private Contact MUST быть исключён:

```text
before DTO construction
```

а не после попадания в браузер.

---

# 16. Public Profile Cache Flow

Если cache используется:

```text
Public Projection
→ cache by profile + locale + projection version
```

При source event:

```text
ContactVisibilityChanged
ProfileChanged
PrimaryPortfolioChanged
QuestionnairePublished
```

cache invalidируется.

---

# 17. Public Profile Failure

Optional subcomponent failure:

```text
analytics
search
VOP
AI
```

не должен блокировать Profile render.

---

# 18. DF-PUB-002 — Direct Project Deep Link

```text
GET /projects/{slug}
      ↓
Resolve Project
      ↓
Check Published + Public Eligibility
      ↓
Resolve Actor identity/context
      ↓
Build Project Public Projection
      ↓
Render
```

---

# 19. Deep Link Context

Даже при прямом входе пользователь получает:

```text
Actor identity
Questionnaire access
Professional CTA
Navigation
```

---

# 20. DF-PUB-003 — Public Search

```text
Query
 ↓
Public Search Service
 ↓
Search Index / PostgreSQL search
 ↓
Only public-indexed entities
 ↓
Result ranking
 ↓
Safe Search DTO
```

---

# 21. Search Never Escalates Access

Если Search Index содержит устаревшую ссылку на уже hidden entity:

final result/read SHOULD revalidate eligibility where needed.

---

# 22. DF-MED-001 — Media Upload

## Flow

```text
Admin UI
  ↓
Select File
  ↓
Client pre-check
  ↓
Upload Endpoint / Server Action
  ↓
Authorization
  ↓
File Validation
  ↓
Create MediaAsset upload record
  ↓
Store Original
  ↓
Verify storage/checksum
  ↓
Commit MediaAsset = STORED
  ↓
Queue processing job
  ↓
Return uploaded state
```

---

# 23. Media Upload Authority

Authoritative upload success occurs only when:

```text
file stored
+
MediaAsset persisted
```

---

# 24. Processing Is Separate

После source commit:

```text
MediaAsset STORED
→ job
→ metadata/derivatives
→ READY or PARTIAL
```

---

# 25. Upload Failure

Если binary storage failed before persistence:

```text
MediaAsset must not appear as READY
```

Partial staging cleanup выполняется отдельно.

---

# 26. DF-MED-002 — Media Processing

```text
Worker claims MediaProcessingJob
      ↓
Load exact MediaAsset
      ↓
Read immutable original
      ↓
Extract metadata
      ↓
Generate derivatives
      ↓
Persist derivative records/status
      ↓
Media processing state update
      ↓
Emit MediaProcessed
```

---

# 27. Derivative Failure

```text
original remains authoritative
```

Possible:

```text
web derivative READY
thumbnail FAILED
```

System uses partial processing state.

---

# 28. DF-MED-003 — Media Classification

```text
Admin Portfolio UI
      ↓
Select MediaAsset
      ↓
Portfolio command
      ↓
Validate same profile / image / availability
      ↓
Create PortfolioItem
      ↓
Commit
      ↓
Projection/cache invalidation
```

MediaAsset itself не меняет professional classification.

---

# 29. AI Classification Suggestion

```text
MediaAsset
  ↓
AI/vision analysis
  ↓
Suggested category
  ↓
persist suggestion
  ↓
Human confirmation
  ↓
Portfolio mutation
```

---

# 30. DF-MED-004 — Set Primary Close-Up / Full Body

```text
Admin action
  ↓
Portfolio Application Service
  ↓
Validate category + published eligibility
  ↓
Begin transaction
  ↓
Unset previous primary in scope
  ↓
Set new primary
  ↓
Audit
  ↓
Commit
  ↓
Invalidate Public Profile / Questionnaire readiness
```

---

# 31. Primary Flow Must Be Atomic

Public never observes:

```text
two primary close-ups
```

или accidental no-primary промежуточное состояние при успешной операции.

---

# 32. DF-EMO-001 — Create Emotional Session

```text
Admin
 ↓
Create Session
 ↓
Set mandatory shooting date
 ↓
Attach MediaAssets
 ↓
Persist Session + relations
```

---

# 33. DF-EMO-002 — Create Emotional Grid

```text
Admin
 ↓
Select EmotionalSession
 ↓
Choose allowed dimensions
 ↓
Create Grid Draft
 ↓
Assign exact source photos/cells
 ↓
Configure crop/pan/scale/rotation
 ↓
Confirm each cell
```

---

# 34. Cell Configuration Persistence

Каждый significant edit может сохраняться как Draft version.

Но Grid остается Draft до finalization.

---

# 35. DF-EMO-003 — AI Crop Suggestion

```text
Grid Cell
 ↓
Allowed source photo
 ↓
Face/crop analysis
 ↓
Suggested crop
 ↓
Store proposal
 ↓
Human review
 ↓
Confirm / Modify / Reject
```

---

# 36. AI Crop Does Not Finalize

```text
AI suggestion
!=
Cell confirmation
```

---

# 37. DF-EMO-004 — Finalize Emotional Grid

```text
Admin clicks Finalize
      ↓
Grid Readiness
      ↓
Validate:
 allowed dimension
 exact cell count
 all confirmed
 same session
 valid sources
      ↓
Freeze Grid Revision
      ↓
Create Render Job
      ↓
Grid = FINALIZING
      ↓
Worker renders composites
      ↓
Validate artifacts
      ↓
Grid Revision = FINALIZED
```

---

# 38. Emotional Render Failure

```text
Grid configuration preserved
Grid returns actionable finalization state
```

No user crop work lost.

---

# 39. DF-EMO-005 — Publish Emotional Grid

```text
Finalized Grid Revision
      ↓
Human Publish
      ↓
Set publication state
      ↓
optional Primary switch transaction
      ↓
Commit
      ↓
Invalidate public Emotional/Profile/Questionnaire projections
```

---

# 40. Questionnaire Emotional Data Flow

Questionnaire receives:

```text
approved composite
shooting date
full emotional portfolio URL
```

Not individual emotional source photos.

---

# 41. DF-QST-001 — Create Questionnaire

```text
Admin
 ↓
Create QuestionnaireDefinition
 ↓
Choose type/template
 ↓
Initial configuration
 ↓
Save Draft
```

---

# 42. Questionnaire Draft Flow

Draft references live Master entities where configured.

Example:

```text
ActorLanguageProficiency ID
Project IDs
PortfolioItem IDs
Contact IDs
```

---

# 43. DF-QST-002 — Edit Questionnaire Draft

```text
Admin UI
 ↓
Load Draft + current source references
 ↓
Edit blocks/order/selection
 ↓
Server validation
 ↓
Expected version check
 ↓
Persist Draft
 ↓
Return saved version
```

---

# 44. Source Edit During Questionnaire Editing

If underlying Profile data changes:

```text
Draft may become STALE / needs refresh
```

Historical Published Revision remains unaffected.

---

# 45. DF-QST-003 — Questionnaire Readiness

```text
Questionnaire Draft
    +
Current eligible Master Data
    +
Link/QR state
    ↓
Readiness Engine
    ↓
READY / ATTENTION / BLOCKED
    ↓
structured blockers/warnings
```

---

# 46. Mandatory Readiness

For every generated actor questionnaire:

```text
Identification
+
eligible/published Main Portfolio Close-Up
+
eligible/published Main Portfolio Full Body
+
official profile link
```

MUST be available.

Emotional/Project photos cannot substitute.

---

# 47. Additional Readiness Inputs

Potential blockers:

```text
invalid selected entity
revoked Contact permission
unpublished Portfolio item
invalid required URL
required QR decode failure
missing locale content
```

---

# 48. DF-QST-004 — Publish Questionnaire

Canonical strong-consistency flow:

```text
Admin Publish
      ↓
Authentication / Authorization
      ↓
Expected Draft Version
      ↓
Readiness Validation
      ↓
Resolve exact source values
      ↓
Create immutable publication snapshot
      ↓
Allocate revision number
      ↓
Persist QuestionnaireRevision
      ↓
Update current published pointer
      ↓
Audit + Outbox
      ↓
COMMIT
```

---

# 49. After Questionnaire Commit

Async:

```text
Outbox
 ├─ PDF generation
 ├─ QR artifact generation if not already materialized
 ├─ public cache invalidation
 └─ analytics/system events
```

---

# 50. Atomic Public Visibility

Before transaction:

```text
Revision N public
```

После успешного commit:

```text
Revision N+1 public
```

Never partial mixture.

---

# 51. Publish Failure Before Commit

```text
previous published revision remains current
```

---

# 52. DF-QST-005 — Historical Questionnaire Read

```text
Request exact Revision
      ↓
Load QuestionnaireRevision snapshot
      ↓
Load revision-bound immutable assets/artifacts
      ↓
Render
```

Must not query current:

```text
Contact.value
Language.level
Project.title
Primary photo
```

for document content.

---

# 53. DF-QST-006 — Restore Revision to Draft

```text
Admin selects Revision N
      ↓
Load immutable snapshot
      ↓
Create new current Draft based on N
      ↓
Validate against current rules/permissions
      ↓
Human review
      ↓
Optional Publish as next Revision
```

No old Revision mutation.

---

# 54. DF-QST-007 — Prepared PDF Generation

```text
PDF Job
 ↓
revision_id + locale + render version
 ↓
Load immutable Revision
 ↓
Build Document Model
 ↓
Resolve exact revision-bound assets
 ↓
Generate links/QR
 ↓
Render PDF
 ↓
Validate artifact
 ↓
Persist artifact
```

---

# 55. PDF Worker Cannot Use Current Master

Normative.

---

# 56. DF-PQB-001 — Open Public Builder

```text
Visitor
 ↓
GET /questionnaire/build
 ↓
Resolve Profile + Locale
 ↓
Builder Projection Service
 ↓
Apply Builder eligibility
 ↓
Return templates + eligible content
```

---

# 57. Public Builder Projection May Differ From Public Profile

Because:

```text
show_on_public_site
!=
allow_in_public_questionnaire_builder
```

---

# 58. DF-PQB-002 — Create Builder Session

```text
Select Template / Customize prepared Questionnaire
 ↓
POST Create Session
 ↓
Validate Template enabled
 ↓
Create durable temporary Session
 ↓
Return opaque session ID
```

---

# 59. Session ID Boundary

Opaque session ID grants access only to that Session workflow.

It never authorizes arbitrary entity fetch.

---

# 60. DF-PQB-003 — Modify Builder Selection

```text
Visitor chooses item
 ↓
Client sends item ID
 ↓
Builder Service
 ↓
Resolve source entity
 ↓
Validate:
 same profile
 available
 builder-permitted
 template allows
 limits allow
 ↓
Persist Session config
```

---

# 61. Tampered ID

If client submits private/foreign ID:

```text
reject
```

It must never be returned into generated output.

---

# 62. DF-PQB-004 — Start From Prepared Questionnaire

```text
Prepared Questionnaire
 ↓
Visitor "Настроить эту версию под себя"
 ↓
Resolve current allowed prepared content/config
 ↓
Filter through Builder eligibility
 ↓
Create Builder Session
```

Private/ineligible prepared items are not automatically carried into Builder.

---

# 63. DF-PQB-005 — Builder Preview

```text
Session
 ↓
Current server revalidation
 ↓
Build temporary preview model
 ↓
Render HTML preview
```

No immutable Snapshot required for every keystroke.

---

# 64. DF-PQB-006 — Builder PDF Generation

```text
Visitor Generate
      ↓
Load Session
      ↓
Check expiry
      ↓
Server revalidate every selected entity
      ↓
Validate mandatory questionnaire requirements
      ↓
Create immutable BuilderGenerationSnapshot
      ↓
Commit Snapshot
      ↓
Queue/execute PDF render
      ↓
PDF artifact bound to Snapshot
```

---

# 65. Builder Generation Failure

Session and selections remain intact.

Retry is possible.

---

# 66. Builder Source Change Between Generations

Generation A:

```text
snapshot A
```

Later Profile changes.

Generation B:

```text
snapshot B
```

A remains unchanged.

---

# 67. DF-PQB-007 — Builder Session Expiry

```text
Scheduler / request check
 ↓
expires_at passed
 ↓
Session ACTIVE → EXPIRED
 ↓
future mutation/generation denied
```

Historical generated artifacts follow retention/access policy.

---

# 68. DF-FDB-001 — Public Professional Inquiry

Canonical flow:

```text
Visitor opens contextual CTA
      ↓
Form prefilled with safe source context
      ↓
User enters message/contact
      ↓
Submit
      ↓
Server validation
      ↓
Anti-abuse
      ↓
Attachment validation
      ↓
Idempotency check
      ↓
BEGIN TRANSACTION
      ↓
Create Feedback
      ↓
Create attachment metadata/context
      ↓
Create Outbox Notification Intent
      ↓
Audit/system record if required
      ↓
COMMIT
      ↓
Return success
```

---

# 69. Feedback Success Definition

Success to visitor occurs when:

```text
Feedback is persisted
```

---

# 70. DF-FDB-002 — Notification After Feedback

```text
Outbox Dispatcher
 ↓
Notification Creator
 ↓
Notification persisted
 ↓
Delivery jobs
 ├─ In-App
 ├─ WhatsApp
 └─ Email if configured
```

---

# 71. WhatsApp Failure

```text
NotificationDelivery = FAILED
Feedback = unchanged
```

---

# 72. DF-FDB-003 — Feedback Read / Workflow

```text
Admin opens Inbox
 ↓
Feedback Query
 ↓
Mark read optionally
 ↓
Admin assigns / notes / next action
 ↓
Feedback Application Service
 ↓
Persist operational state
```

---

# 73. Notification Read != Feedback Read

Different flows.

---

# 74. DF-FDB-004 — Create Casting From Feedback

```text
Admin chooses Create Casting
      ↓
Feedback Service reads authoritative Feedback
      ↓
Casting Orchestrator
      ↓
BEGIN TRANSACTION
      ↓
Create Casting Draft
      ↓
Create source/provenance link
      ↓
Copy permitted source context into CastingSource
      ↓
Audit
      ↓
COMMIT
```

---

# 75. Feedback Is Preserved

No destructive conversion.

---

# 76. Duplicate Create Casting Request

Idempotency/relationship check prevents duplicate accidental Casting.

---

# 77. DF-CST-001 — Manual Casting Creation

```text
Admin
 ↓
Create Casting Draft
 ↓
Add title/context/deadline
 ↓
Add Source text/files
 ↓
Persist
```

---

# 78. Casting Source Attachments

Stored in private operational storage.

Not Media Library public pool.

---

# 79. DF-CST-002 — Request Casting AI Analysis

```text
Admin requests analysis
      ↓
Authorize Casting
      ↓
Resolve CastingSource
      ↓
Create immutable source snapshot
      ↓
Resolve minimum necessary Profile snapshot
      ↓
Create Analysis Job
      ↓
Return queued state
```

---

# 80. DF-CST-003 — Execute Casting AI Analysis

```text
Worker
 ↓
Load exact Analysis source snapshot
 ↓
Build AI context
 ↓
AI Provider
 ↓
Receive output
 ↓
Schema Validation
 ↓
Business normalization
 ↓
Persist CastingAnalysisRevision
 ↓
Analysis state = GENERATED
```

---

# 81. Analysis Generation Does Not Mutate ActorProfile

Normative.

---

# 82. DF-CST-004 — Human Review Requirements

```text
Admin opens Analysis Revision
 ↓
View Source
 ↓
View AI Extraction
 ↓
Confirm / Modify / Reject each requirement
 ↓
Casting Application Service
 ↓
Persist human-confirmed interpretation
 ↓
Audit
```

---

# 83. Missing Requirement

If absent in source:

```text
NOT_SPECIFIED / "Не указано"
```

No AI invention.

---

# 84. DF-CST-005 — Profile Match

```text
Confirmed Casting Requirements
      +
Confirmed Profile Facts
      ↓
Profile Match Engine
      ↓
MATCH / PARTIAL / MISMATCH /
NO_CONFIRMED_DATA / NOT_APPLICABLE
      ↓
Persist/current derived Match Result
```

---

# 85. Profile Match Prefer Deterministic Logic

Structured requirement comparison should be deterministic where possible.

---

# 86. DF-CST-006 — Casting Recommendation

```text
Requirements
+
Match Results
+
Available professional evidence
 ↓
Recommendation generation
 ↓
Persist recommendation
 ↓
Human review
```

---

# 87. Recommendation Does Not Mutate Questionnaire Automatically

Admin may choose:

```text
Generate Casting Questionnaire Draft
```

---

# 88. DF-CST-007 — Generate Casting Questionnaire Draft

```text
Admin explicit action
 ↓
Casting + confirmed requirements
 ↓
Questionnaire suggestion/config builder
 ↓
Validate eligible Profile data
 ↓
Create QuestionnaireDefinition/Draft
 ↓
Link context_casting
 ↓
Human review
```

---

# 89. Casting Questionnaire Remains Draft

No auto-publication.

---

# 90. DF-CST-008 — Source Change After Analysis

```text
Casting Source changes
 ↓
Emit SourceChanged
 ↓
Existing current Analysis marked STALE
 ↓
VOP/UI indicates re-analysis/review required
```

Old AnalysisRevision preserved.

---

# 91. DF-OPP-001 — Create Opportunity

```text
Admin in Casting
 ↓
Create Opportunity
 ↓
Validate no conflicting active opportunity according to rules
 ↓
BEGIN TRANSACTION
 ↓
Create Opportunity = NEW
 ↓
Append initial stage history
 ↓
Link Casting
 ↓
Audit
 ↓
COMMIT
```

---

# 92. DF-OPP-002 — Transition Opportunity Stage

All interfaces:

```text
Kanban
List
Detail
Dashboard
```

converge to:

```text
OpportunityApplicationService.transition()
```

---

# 93. Opportunity Transition Flow

```text
Command
 ↓
Authorization
 ↓
Expected version/current stage
 ↓
Validate allowed transition
 ↓
Validate business evidence/confirmation
 ↓
BEGIN TRANSACTION
 ↓
Update current stage
 ↓
Append StageHistory
 ↓
Audit
 ↓
Outbox business event
 ↓
COMMIT
```

---

# 94. Concurrent Transition

If expected version/stage stale:

```text
reject conflict
```

No silent last-write-wins.

---

# 95. DF-OPP-003 — Booked

```text
Human selects Booked
 ↓
Confirm actual booking
 ↓
Opportunity transition
 ↓
BOOKED persisted
 ↓
StageHistory
 ↓
Audit
 ↓
Optional action offered:
 Create Project Draft
```

---

# 96. DF-OPP-004 — Create Project From Booked

```text
Admin explicit action
 ↓
Read BOOKED Opportunity
 ↓
Resolve known Casting context
 ↓
Create Project Draft
 ↓
Create Role Draft if appropriate
 ↓
Set provenance source_opportunity
 ↓
Commit
```

---

# 97. No Auto-Publication

Project/Role undergo normal Project workflow.

---

# 98. DF-BBA-001 — Generate BB Draft

```text
Admin selects task
 ↓
Choose context
 ↓
BB Context Builder
 ↓
Fetch permitted Source Facts
 ↓
Create Source Snapshot
 ↓
AI Provider
 ↓
Schema/text validation
 ↓
Persist AIDraft = GENERATED
```

---

# 99. BB Source Scope

Only data needed for:

```text
biography
cover letter
casting response
social caption
etc.
```

---

# 100. DF-BBA-002 — Edit BB Draft

```text
Admin edits generated text
 ↓
Persist edited_text / Draft state
```

Original generated text remains provenance.

---

# 101. DF-BBA-003 — Apply BB Draft

```text
Admin clicks Apply
 ↓
Check target exists
 ↓
Check target version/staleness
 ↓
Human confirmation if needed
 ↓
Owning Domain command
 ↓
Update target Draft
 ↓
Audit/provenance
 ↓
AIDraft = APPLIED
```

---

# 102. Apply Does Not Publish

Target remains governed by own workflow.

---

# 103. DF-BBA-004 — Stale BB Draft

```text
Source fact changes
 ↓
Draft detected stale
 ↓
Apply action warns/revalidates
```

Never silently overwrites newer target.

---

# 104. DF-VOP-001 — Observation Generation

Inputs may include:

```text
domain events
scheduled checks
job failures
link health
readiness
system health
analytics summaries
```

Flow:

```text
Signal
 ↓
Detector
 ↓
Deterministic condition evaluation
 ↓
Create/update VOPObservation
 ↓
Dashboard/Operator projection
```

---

# 105. VOP Duplicate Suppression

Same unresolved deterministic condition SHOULD update/reuse one Observation rather than create alert spam.

---

# 106. DF-VOP-002 — Recommendation

```text
Observation
 ↓
Rule/AI assistance
 ↓
Recommendation + rationale
 ↓
Persist
 ↓
Human review
```

---

# 107. DF-VOP-003 — Safe AUTO-4 Execution

Example thumbnail rebuild:

```text
Observation
 ↓
Safe deterministic action selected
 ↓
Application command/job
 ↓
Execute
 ↓
Revalidate condition
 ↓
AUTO_RESOLVED only if actually fixed
```

---

# 108. VOP Must Revalidate

Successful job execution is not enough if underlying problem remains.

---

# 109. DF-VOP-004 — Human-Approved Action

```text
Recommendation
 ↓
Admin Approve
 ↓
Revalidate source version
 ↓
Owning Domain command
 ↓
Persist
 ↓
Audit
 ↓
Resolve Observation when condition clears
```

---

# 110. DF-THM-001 — Create Theme Draft

```text
Admin
 ↓
Create/clone Theme Draft
 ↓
Persist editable tokens/config
```

---

# 111. DF-THM-002 — AI Theme Proposal

```text
Admin prompt
 ↓
Theme Context Builder
 ↓
Current Theme + allowed token schema + accessibility constraints
 ↓
AI Provider
 ↓
Structured ThemeProposal
 ↓
Schema validation
 ↓
Persist proposal
```

---

# 112. Theme AI Never Activates Directly

Proposal requires Apply.

---

# 113. DF-THM-003 — Apply Theme Proposal

```text
Admin reviews proposal
 ↓
Apply selected fields
 ↓
Theme Draft mutation
 ↓
Preserve locks/manual overrides
```

---

# 114. DF-THM-004 — Theme Validation

```text
Theme Draft
 ↓
Schema validation
 ↓
Contrast/accessibility validation
 ↓
Responsive preview validation
 ↓
Critical component checks
 ↓
READY or BLOCKED
```

---

# 115. DF-THM-005 — Publish Theme

```text
Admin explicit Publish
 ↓
Expected Draft Version
 ↓
Validation still valid
 ↓
Create ThemeRevision
 ↓
Audit
 ↓
COMMIT
```

---

# 116. DF-THM-006 — Activate Theme

```text
Published ThemeRevision
 ↓
Create/change ThemeActivation
 ↓
Atomic effective-theme switch
 ↓
Invalidate Public presentation cache
```

---

# 117. Temporary Theme Flow

```text
Admin schedules/activates temporary revision
 ↓
ThemeActivation with expires_at
 ↓
Scheduler detects expiry
 ↓
Deactivate expired activation
 ↓
Resolve next valid effective Theme
```

No professional content mutation.

---

# 118. DF-NOT-001 — Notification Creation

Trigger:

```text
committed domain event
```

Flow:

```text
Outbox
 ↓
Notification Creator
 ↓
Policy Engine
 ↓
Persist Notification
 ↓
Create Delivery records/jobs
```

---

# 119. DF-NOT-002 — Notification Delivery

```text
Delivery Job
 ↓
Quiet Hours Check
 ↓
If delayed → schedule
Else:
 ↓
Provider Adapter
 ↓
Send
 ↓
Persist SENT / FAILED
```

---

# 120. Minimal Payload Flow

External provider receives only minimum needed data.

Detailed private context remains behind Admin auth.

---

# 121. DF-NOT-003 — Retry

```text
FAILED transient
 ↓
Retry Policy
 ↓
RETRY_SCHEDULED
 ↓
Delivery Job
```

Same idempotency identity.

---

# 122. Permanent Failure

After configured attempts:

```text
FAILED terminal
```

Admin/VOP alerted.

Source business record unchanged.

---

# 123. DF-SOC-001 — Connect Social Account

```text
Admin starts OAuth
 ↓
Provider Authorization
 ↓
Callback
 ↓
Verify state/security
 ↓
Exchange credentials
 ↓
Store secure credential reference
 ↓
SocialAccountConnection = CONNECTED
```

---

# 124. OAuth Token Storage

Tokens go to secure credential infrastructure, not ordinary public/domain text.

---

# 125. DF-SOC-002 — Create Social Post

```text
Admin
 ↓
Create Draft
 ↓
Select Media
 ↓
Write caption / Apply BB Draft
 ↓
Choose platforms
 ↓
Save
```

---

# 126. DF-SOC-003 — Immediate Publish

```text
Admin explicit Publish
 ↓
Create platform publish jobs
 ↓
Commit internal publish intent
 ↓
Workers call providers
 ↓
Store per-platform PublishAttempt
 ↓
Aggregate SocialPost status
```

---

# 127. DF-SOC-004 — Scheduled Publish

```text
Admin sets date/time
 ↓
Persist SCHEDULED
 ↓
Scheduler
 ↓
At approved time create/claim publish job
 ↓
Provider delivery
```

---

# 128. Partial Social Failure

Example:

```text
Instagram = PUBLISHED
TikTok = FAILED
```

SocialPost:

```text
PARTIALLY_PUBLISHED
```

Retry TikTok only.

---

# 129. DF-SRCH-001 — Search Reindex on Change

```text
Committed domain event
 ↓
Outbox/job
 ↓
Projection Builder
 ↓
Apply current publication/visibility
 ↓
Write search index
```

---

# 130. Search Index Lag

Temporary eventual consistency is acceptable.

Security-sensitive hide/revocation SHOULD also be protected by final eligibility check where stale index could expose discoverability.

---

# 131. DF-SRCH-002 — Full Search Rebuild

```text
Admin/System command
 ↓
Read authoritative current eligible data
 ↓
Rebuild index
 ↓
Atomically switch/use rebuilt state where needed
```

---

# 132. Search Rebuild Cannot Modify Source

Normative.

---

# 133. DF-CACHE-001 — Public Cache Invalidation

```text
Source mutation committed
 ↓
Domain Event
 ↓
Cache Invalidator
 ↓
Invalidate affected keys
```

---

# 134. Visibility Changes Are High-Priority Invalidation

Examples:

```text
Contact public OFF
Builder eligibility OFF
Private casting token revoked
```

MUST NOT rely solely on long TTL.

---

# 135. DF-CACHE-002 — Immutable Revision Cache

Revision-specific cache can be long-lived:

```text
revision_id + locale
```

because content is immutable.

---

# 136. DF-CACHE-003 — Current Alias Cache

Current questionnaire/profile/theme alias must be invalidated when pointer/config changes.

---

# 137. DF-AUD-001 — Audit Write

Significant mutation flow:

```text
Application Command
 ↓
Business mutation
 ↓
AuditWriter within same transaction where feasible
 ↓
Commit
```

---

# 138. Audit Failure

For critical audited transitions, inability to persist required Audit SHOULD normally fail transaction rather than commit unaudited mutation.

Exact policy may vary by event criticality.

---

# 139. DF-ANL-001 — Public Client Analytics

```text
Public interaction
 ↓
Nonblocking analytics event
 ↓
Validation/minimization
 ↓
Persist/send
```

Failure ignored from user-business perspective.

---

# 140. DF-ANL-002 — Server Business Analytics

```text
FeedbackCreated
OpportunityBooked
etc.
 ↓
Analytics consumer
 ↓
Store aggregate event
```

Business source remains authoritative.

---

# 141. Analytics Event Timing

Critical conversion event SHOULD be emitted from committed server state.

---

# 142. DF-ADM-001 — Admin Standard Edit

Generic authoritative edit:

```text
Load entity version N
 ↓
Edit
 ↓
Submit expected_version=N
 ↓
Server authorization
 ↓
Validation
 ↓
Domain mutation
 ↓
version N+1
 ↓
Audit/event
 ↓
Commit
```

---

# 143. Admin Conflict

If entity already version N+1:

```text
reject
```

UI offers compare/reload/copy changes.

---

# 144. DF-ADM-002 — Archive Entity

```text
Admin Archive
 ↓
Dependency Impact Service
 ↓
Show impacts
 ↓
Human confirms
 ↓
Owning Domain archive transition
 ↓
Commit
 ↓
Invalidate projections/search/cache
```

---

# 145. Archive ≠ Hard Delete

Relations/history remain according to domain rules.

---

# 146. DF-ADM-003 — Delete Source Entity

High-impact flow:

```text
Admin Delete
 ↓
Dependency Impact
 ↓
Retention/privacy policy
 ↓
Block if unresolved dependencies
 ↓
Explicit confirmation
 ↓
Owning Domain deletion procedure
 ↓
Derived cleanup jobs
 ↓
Audit
```

Exact deletion semantics DOC-094.

---

# 147. DF-ERR-001 — HTTP Request Failure Before Commit

```text
validation/business error
```

Result:

```text
no authoritative mutation
user input preserved
specific error returned
```

---

# 148. DF-ERR-002 — Unknown Network Outcome After Commit

Client sends mutation, connection drops.

Client MUST NOT blindly retry without idempotency where duplication matters.

---

# 149. Idempotency Recovery

```text
retry same idempotency key
 ↓
server returns original committed result
```

---

# 150. DF-ERR-003 — Worker Crash Mid-Job

```text
Worker claims job
 ↓
crashes
```

Durable job system eventually:

```text
lease expires / retry scheduled
 ↓
another worker retries
```

Handler must be idempotent.

---

# 151. DF-ERR-004 — External Provider Timeout

```text
send attempted
 ↓
timeout / unknown provider outcome
```

System records unknown/retry-safe state.

Must avoid duplicate side effect through provider/idempotency reconciliation where supported.

---

# 152. DF-ERR-005 — Database Failure

No authoritative commit:

```text
no success UI
no external side effect should be considered authoritative
```

---

# 153. DF-ERR-006 — Storage Failure

Media upload:

```text
do not create READY source
```

PDF/derived artifact:

```text
source Revision remains valid
artifact generation fails/retries
```

---

# 154. DF-ERR-007 — AI Failure

```text
AI request fails
 ↓
AI job/draft state FAILED
 ↓
manual workflow remains
```

No Source mutation.

---

# 155. DF-ERR-008 — Cache Failure

Fallback to authoritative query where operationally acceptable.

Cache rebuild later.

---

# 156. DF-ERR-009 — Search Failure

Public/Admin direct route queries remain.

Search UI shows degraded state.

---

# 157. DF-ERR-010 — QR Validation Failure

If QR optional:

```text
warning / omit QR according to config
```

If QR required for publication:

```text
readiness blocker
```

Never use unverified QR.

---

# 158. Data Sensitivity During Flows

Каждый flow должен классифицировать data as:

```text
PUBLIC
ADMIN
PRIVATE_OPERATIONAL
SECRET
TOKEN_SCOPED
```

---

# 159. PUBLIC

Examples:

```text
published Profile
public Portfolio
public Questionnaire
```

---

# 160. ADMIN

Examples:

```text
Draft Questionnaire
VOP observations
```

---

# 161. PRIVATE_OPERATIONAL

Examples:

```text
Feedback message
Casting Source
internal notes
private attachments
AI snapshots containing Casting details
```

---

# 162. SECRET

Examples:

```text
OAuth tokens
AI provider API key
session signing secret
```

---

# 163. TOKEN_SCOPED

Examples:

```text
private casting questionnaire
unlisted package
```

---

# 164. Flow Security Rule

Sensitivity MAY become stricter downstream.

It MUST NOT become less restricted without explicit authorized projection/publication step.

---

# 165. Example

```text
Private Casting Source
→ AI Analysis
```

remains private.

It MUST NOT flow into:

```text
Public Profile
```

because AI recommended it.

---

# 166. Data Minimization Rule

Cross-component flow carries only required fields.

---

# 167. Example — Notification

Instead of sending full Feedback body via WhatsApp:

```text
New casting inquiry
[Open Admin]
```

preferred by default.

---

# 168. Example — AI

Theme AI does not receive Contacts or Casting Source.

---

# 169. Transaction Boundary Matrix

| Flow | Transaction |
|---|---|
| Profile update | one DB transaction |
| Set Primary Portfolio | one DB transaction |
| Questionnaire publish | one DB transaction for Revision/pointer/outbox |
| Builder session update | one DB transaction |
| Builder Snapshot creation | one DB transaction |
| Feedback submit | one DB transaction |
| Casting from Feedback | one DB transaction where feasible |
| Opportunity transition | one DB transaction |
| Theme publish | one DB transaction |
| Notification provider call | outside source business transaction |
| AI provider call | outside target Master mutation transaction |
| PDF rendering | async/outside publication transaction unless mandatory precondition |

---

# 170. Eventual Consistency Matrix

| Derived capability | Lag allowed? |
|---|---:|
| Search index | Yes |
| Public cache | Minimal |
| Analytics | Yes |
| Notification delivery | Yes |
| Thumbnail | Yes |
| QR cache | Yes, but required output waits for valid QR |
| PDF secondary regeneration | Yes |
| Public visibility revocation | Must take effect promptly |
| Opportunity state | No |
| Questionnaire current pointer | No |

---

# 171. Flow Idempotency Matrix

| Flow | Key/Strategy |
|---|---|
| Feedback submit | client/server idempotency key |
| Questionnaire publish | publish command ID |
| Builder generation | generation request ID/config hash |
| QR generation | URL + settings |
| PDF generation | Revision/Snapshot + locale + renderer version |
| Notification send | notification delivery key |
| Social publish | platform publish intent key |
| Opportunity transition | command ID + expected state/version |
| Project Draft from Booked | source Opportunity uniqueness |
| Media derivative | source + transformation hash |

---

# 172. Flow Provenance Matrix

| Output | Must reference |
|---|---|
| PortfolioItem | MediaAsset |
| Emotional Grid Artifact | exact GridRevision/config |
| QuestionnaireRevision | logical Questionnaire + source snapshot |
| Questionnaire PDF | exact Revision |
| Builder PDF | exact GenerationSnapshot |
| QR | canonical URL |
| Casting Analysis | exact CastingSource/Profile snapshot |
| AIDraft | exact source snapshot |
| Project Draft from Booked | Opportunity |
| Notification | source entity/event |
| Social post result | SocialPost + platform attempt |

---

# 173. Domain Event Flow

Canonical event lifecycle:

```text
Domain Command
 ↓
Business mutation
 ↓
Event/Outbox record
 ↓
Commit
 ↓
Dispatcher
 ↓
One or more consumers
```

---

# 174. Consumers Must Be Independent

Example:

```text
QuestionnairePublished
```

may trigger:

```text
PDF
Cache
Analytics
```

PDF failure must not prevent Analytics/cache consumers from operating where independent.

---

# 175. Consumer Idempotency

Each consumer tracks its own processing/idempotency state.

---

# 176. No Event-Chaining Business Logic Without Control

Avoid fragile:

```text
Event A
→ event B
→ event C
→ eventually core business state
```

for strongly consistent professional transitions.

Core transition belongs synchronous application/domain flow.

---

# 177. Public Questionnaire End-to-End Flow

```text
MASTER DATA
     ↓
Questionnaire Draft
     ↓
Readiness
     ↓
Human Publish
     ↓
Immutable Revision
     ↓
PDF/QR artifacts
     ↓
Public Questionnaire Hub
     ↓
Visitor
     ↓
Professional CTA
     ↓
Feedback
```

---

# 178. Full Casting End-to-End Flow

```text
Professional Inquiry
      ↓
Feedback
      ↓
Admin Review
      ↓
Casting
      ↓
Casting Source
      ↓
AI Analysis Revision
      ↓
Human-Confirmed Requirements
      ↓
Profile Match
      ↓
Recommendation
      ↓
Casting Questionnaire Draft
      ↓
Opportunity
      ↓
Audition / Callback / Offer
      ↓
Booked
      ↓
Project / Role Draft
```

---

# 179. Full Media-to-Questionnaire Flow

```text
Upload Media
 ↓
Immutable MediaAsset
 ↓
Derivative Processing
 ↓
Portfolio Classification
 ↓
Primary Close-Up / Full Body
 ↓
Questionnaire Draft references
 ↓
Publication Snapshot
 ↓
PDF
```

---

# 180. Full Emotional Flow

```text
MediaAssets
 ↓
EmotionalSession + date
 ↓
Grid Draft
 ↓
Cell confirmations
 ↓
Grid Revision
 ↓
Composite
 ↓
Publish
 ↓
Public Emotional Range
 ↓
Questionnaire Composite + date + link
```

---

# 181. Full BB Flow

```text
Source Facts
 ↓
Source Snapshot
 ↓
AI Draft
 ↓
Human Edit
 ↓
Apply
 ↓
Target Draft
 ↓
Separate Save/Publish/Send
```

---

# 182. Full Theme Flow

```text
Current Theme
 ↓
Draft
 ↓
AI Proposal optional
 ↓
Human Edit
 ↓
Accessibility Validation
 ↓
Published ThemeRevision
 ↓
Activation
 ↓
Public Presentation
```

---

# 183. Full Notification Flow

```text
Committed Domain Event
 ↓
Outbox
 ↓
Notification
 ↓
Policy
 ↓
Delivery
 ↓
Provider
 ↓
Delivery Result
```

No reverse authority over source entity.

---

# 184. Data Flow Anti-Pattern DF-AP-001

**Public UI queries raw Contacts and filters them client-side**

---

# 185. DF-AP-002

**PDF worker reads current Profile instead of Revision**

---

# 186. DF-AP-003

**Feedback success shown after WhatsApp send but before DB commit**

---

# 187. DF-AP-004

**AI output directly writes Skill/Project facts**

---

# 188. DF-AP-005

**Public Builder trusts submitted entity IDs**

---

# 189. DF-AP-006

**Emotional Grid worker reads mutable current crop while rendering finalized artifact**

---

# 190. DF-AP-007

**Questionnaire publication updates current pointer before Revision fully persists**

---

# 191. DF-AP-008

**Search result reveals archived/private item because final eligibility is never checked**

---

# 192. DF-AP-009

**Notification retry creates duplicate external messages**

---

# 193. DF-AP-010

**Worker crash loses accepted job**

---

# 194. DF-AP-011

**Social publish retries all platforms after only one failed**

---

# 195. DF-AP-012

**Opportunity stage mutation is optimistic UI-only and later reconciled**

---

# 196. DF-AP-013

**Theme preview writes directly to active Theme**

---

# 197. DF-AP-014

**Current source update mutates old Builder PDF**

---

# 198. DF-AP-015

**Public request carries internal storage path**

---

# 199. DF-AP-016

**Analytics event is emitted before business commit and treated as authoritative conversion**

---

# 200. DF-AP-017

**AIDraft Apply bypasses target version/concurrency check**

---

# 201. DF-AP-018

**Archive operation ignores dependencies**

---

# 202. DF-AP-019

**Private Casting attachment flows into public Media Library automatically**

---

# 203. DF-AP-020

**One failed async consumer rolls back already committed source business data**

---

# 204. Data Flow Quality Gate

Каждый новый flow MUST определить:

- [ ] source actor/system;
- [ ] input data classification;
- [ ] authentication;
- [ ] authorization;
- [ ] input validation;
- [ ] application command/query;
- [ ] owning Domain;
- [ ] concurrency requirement;
- [ ] transaction boundary;
- [ ] authoritative commit point;
- [ ] audit;
- [ ] outbox/domain events;
- [ ] async processing;
- [ ] idempotency;
- [ ] external integrations;
- [ ] cache invalidation;
- [ ] projection impact;
- [ ] privacy boundary;
- [ ] failure recovery;
- [ ] resulting UI state.

---

# 205. Data Flow Specification Template

```text
Flow ID:
DF-...

Trigger:
...

Actor:
...

Inputs:
...

Sensitivity:
PUBLIC / ADMIN / PRIVATE / SECRET / TOKEN_SCOPED

Synchronous Steps:
1.
2.
3.

Transaction Boundary:
...

Authoritative Commit:
...

Async Steps:
...

Domain Events:
...

Idempotency:
...

Concurrency:
...

Failure Handling:
...

Audit:
...

Projections Invalidated:
...

Success Definition:
...
```

---

# 206. E2E-DF-001 — Public Profile Privacy

Private Contact exists.

Public request:

```text
Projection excludes Contact before response.
```

---

# 207. E2E-DF-002 — Upload

Upload valid image.

Original persists.

Worker fails thumbnail.

Expected:

```text
source retained
partial processing state
retry possible
```

---

# 208. E2E-DF-003 — Primary Photo

Concurrent Set Primary requests.

Expected:

```text
one authoritative result
no duplicate Primary
```

---

# 209. E2E-DF-004 — Grid Finalization

Worker renders from frozen Grid version even if Admin starts new Draft edit simultaneously.

---

# 210. E2E-DF-005 — Questionnaire Publish

Profile changes during publication.

Expected:

```text
either publication uses one accepted frozen snapshot
or transaction aborts/revalidates
never mixed state
```

---

# 211. E2E-DF-006 — Historical PDF

Revision 3 generated after Revision 4 exists.

Worker still renders Revision 3 content.

---

# 212. E2E-DF-007 — Builder Tampering

Visitor submits private Contact ID.

Expected:

```text
generation rejected/removes item
private value never reaches client artifact
```

---

# 213. E2E-DF-008 — Builder Permission Revocation

Contact selected in active Session.

Admin disables Builder permission.

Next Preview/Generate revalidates and excludes/blocks Contact.

---

# 214. E2E-DF-009 — Feedback Duplicate Retry

Client times out and retries same submission.

One Feedback only.

---

# 215. E2E-DF-010 — Notification Failure

Feedback persisted.

WhatsApp provider unavailable.

Feedback remains visible in Inbox.

---

# 216. E2E-DF-011 — Casting AI

AI returns malformed structured output.

Schema validation rejects it.

No confirmed requirement created.

---

# 217. E2E-DF-012 — Casting Source Update

Old Analysis marked stale.

Historical revision remains accessible.

---

# 218. E2E-DF-013 — Opportunity Conflict

Two Admin tabs transition same Opportunity.

Second stale command rejected.

---

# 219. E2E-DF-014 — Booked to Project

Retry Create Project Draft.

Expected:

```text
one resulting Project Draft per idempotent source action
```

---

# 220. E2E-DF-015 — BB Apply

Target changed after AI generation.

Apply detects version mismatch/staleness.

No silent overwrite.

---

# 221. E2E-DF-016 — Theme Publish

Accessibility check fails.

No ThemeRevision activation.

Current Theme unchanged.

---

# 222. E2E-DF-017 — Social Partial Failure

Instagram succeeds, TikTok fails.

Retry TikTok only.

---

# 223. E2E-DF-018 — Worker Restart

Queued PDF/notification job remains durable.

---

# 224. E2E-DF-019 — Search Lag

Project archived.

Stale search hit attempted.

Final Project public eligibility rejects access.

Search reindex later removes result.

---

# 225. E2E-DF-020 — Public Cache Privacy

Contact visibility toggled OFF.

No stale cached public payload continues serving Contact beyond defined immediate invalidation/revalidation strategy.

---

# 226. E2E-DF-021 — QR Exactness

QR generated from canonical URL.

Decode result exactly equals input URL.

---

# 227. E2E-DF-022 — QR Historical Stability

ProfessionalLink URL updated.

Historical PDF still contains old Revision QR.

Current Questionnaire uses new QR.

---

# 228. E2E-DF-023 — Media Delete Dependency

MediaAsset used as Primary Full Body.

Delete flow reports dependency and blocks unsafe removal.

---

# 229. E2E-DF-024 — Feedback Attachment Privacy

Private attachment cannot be accessed through public static media path.

---

# 230. E2E-DF-025 — Analytics Failure

Analytics collector unavailable.

Feedback submission still completes normally.

---

# 231. Architectural Flow Traceability

Required chain:

```text
User Flow / Business Rule
       ↓
Data Flow
       ↓
Application Command / Query
       ↓
Domain Rule
       ↓
Transaction / Job
       ↓
Projection / Artifact
       ↓
Acceptance Test
```

---

# 232. Flow → State Mapping

Каждый flow должен явно понимать target Domain State.

Examples:

```text
Publish Questionnaire
→ QuestionnaireRevision.PUBLISHED

Generate AI Draft
→ AIDraft.GENERATED

Book Opportunity
→ Opportunity.BOOKED

Builder expiration
→ PublicBuilderSession.EXPIRED
```

---

# 233. Flow → Ownership Mapping

Каждый write flow обязан иметь одного owning Domain.

Если flow затрагивает несколько Domains:

```text
Application Orchestrator
```

координирует их, но ownership не сливается.

---

# 234. Flow → Revision Mapping

Если action создаёт исторически значимый output:

должен быть определён:

```text
Revision
Snapshot
or Audit-only
```

---

# 235. Flow → Privacy Mapping

До реализации flow must answer:

```text
What is the most sensitive datum in this flow?
Which components receive it?
Where is it stored?
Can it reach Public client?
Can it reach AI/external provider?
```

---

# 236. Flow → Failure Mapping

Для каждого external/async step MUST быть ответ:

```text
What happens if it fails after source commit?
```

Default:

```text
source remains authoritative
dependent capability becomes failed/degraded/retryable
```

---

# 237. Canonical System Flow Map

```text
                          PUBLIC USER
                              │
             ┌────────────────┼─────────────────┐
             ↓                ↓                 ↓
         Public Read       Builder          Feedback
             │                │                 │
             ↓                ↓                 ↓
        Projections      Session/Snapshot   DB Commit
                                                 │
                                                 ↓
                                            Notification

                              ADMIN
                                │
            ┌───────────────────┼─────────────────────┐
            ↓                   ↓                     ↓
        Master Data         Castings             Questionnaires
            │                   │                     │
            ↓                   ↓                     ↓
        Domain Write       AI Analysis              Revision
            │                   │                     │
            ↓                   ↓                     ↓
         Events           Human Review              PDF/QR
            │                   │
            ↓                   ↓
      Cache/Search/VOP     Opportunity
                                │
                                ↓
                              Booked
                                │
                                ↓
                         Project / Role Draft
```

---

# 238. Strong Consistency Zone

```text
PostgreSQL Transaction
────────────────────────────────────────
Master Data
Visibility
Primary markers
Feedback persistence
Casting creation
Opportunity stage
Questionnaire Revision
Theme Revision
Audit/Outbox where required
────────────────────────────────────────
```

---

# 239. Eventual Consistency Zone

```text
After Commit
────────────────────────────────────────
Notifications
PDF rendering
QR cache
Search
Analytics
Media derivatives
VOP observations
External social publishing
────────────────────────────────────────
```

---

# 240. Финальный принцип

> **Каждый сквозной поток продукта должен чётко отделять принятие решения и фиксацию бизнес-истины от её доставки, рендера, анализа и распространения. Профиль, обращение, кастинг, Opportunity, Questionnaire Revision и Theme Revision становятся авторитетными только после валидированной транзакции. PDF, QR, уведомления, поисковый индекс, аналитика и AI выполняются вокруг уже зафиксированной истины и могут повторяться, перестраиваться или временно выходить из строя без потери основного профессионального состояния.**