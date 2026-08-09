# ARCHITECTURE COMPONENTS

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативный каталог компонентов и разрешённых зависимостей системы

**Целевой файл:** `docs/architecture/components.md`  
**Документ:** DOC-071  
**Статус:** ✅ Completed  
**Тип:** Architecture / Components / Boundaries / Dependencies

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/public-navigation.md`
- `docs/ux/admin-navigation.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/admin-user-flows.md`
- `docs/domain/domain-model.md`
- `docs/domain/relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions.md`
- `docs/architecture/architecture-overview.md`

---

# 1. Назначение документа

Настоящий документ определяет канонические архитектурные компоненты продукта.

Он фиксирует:

1. runtime components;
2. presentation components;
3. application services;
4. domain modules;
5. infrastructure adapters;
6. repositories;
7. public projection components;
8. background worker components;
9. document generation components;
10. media processing;
11. AI capabilities;
12. notification components;
13. search;
14. caching;
15. analytics;
16. audit;
17. security/auth;
18. external integrations;
19. allowed dependencies;
20. forbidden dependencies;
21. component ownership;
22. lifecycle and deployment responsibilities;
23. component-level failure isolation.

---

# 2. Главный принцип

> **Каждый компонент должен иметь одну ясную ответственность, явный контракт и ограниченное направление зависимостей. Компонент не должен получать право изменять чужую доменную модель только потому, что технически находится в одном приложении.**

---

# 3. Component IDs

Используется:

```text
CMP-*
```

Категории:

```text
CMP-RUN-*    Runtime
CMP-PUB-*    Public
CMP-ADM-*    Admin
CMP-APP-*    Application
CMP-DOM-*    Domain
CMP-DATA-*   Persistence
CMP-MED-*    Media
CMP-DOC-*    Documents/PDF
CMP-QR-*     QR
CMP-JOB-*    Background Jobs
CMP-SRCH-*   Search
CMP-CACHE-*  Cache
CMP-AI-*     AI
CMP-NOT-*    Notifications
CMP-ANL-*    Analytics
CMP-AUD-*    Audit
CMP-SEC-*    Security
CMP-INT-*    Integrations
CMP-OBS-*    Observability
```

---

# 4. Component Classification

Компоненты разделяются на:

```text
Runtime Component
Presentation Component
Application Component
Domain Component
Infrastructure Component
Projection Component
Worker Component
External Adapter
```

---

# 5. Общая карта компонентов

```text
                            USERS
                              │
                    ┌─────────┴─────────┐
                    ▼                   ▼
               PUBLIC WEB           ADMIN WEB
                    │                   │
                    └─────────┬─────────┘
                              ▼
                     APPLICATION SERVICES
                              │
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
     DOMAIN MODULES      READ PROJECTIONS     ORCHESTRATORS
          │                   │                   │
          └─────────────┬─────┴─────────────┬─────┘
                        ▼                   ▼
                   REPOSITORIES         JOB DISPATCH
                        │                   │
                        ▼                   ▼
                    POSTGRESQL          JOB WORKER
                                            │
                  ┌─────────────────────────┼────────────────────┐
                  ▼                         ▼                    ▼
             MEDIA / PDF                 AI ADAPTER        NOTIFICATIONS
                  │                                              │
                  ▼                                              ▼
            MEDIA STORAGE                                 EXTERNAL CHANNELS
```

---

# 6. CMP-RUN-001 — Reverse Proxy / TLS Gateway

## Responsibility

Принимает внешние HTTPS requests и маршрутизирует их в Web Runtime.

## Responsibilities

- TLS termination;
- canonical host routing;
- HTTP→HTTPS redirect;
- proxy headers;
- request size safeguards where appropriate;
- public/admin route forwarding.

## Does Not Own

- authentication truth;
- business authorization;
- application routing semantics;
- professional data.

---

# 7. Reverse Proxy Security Rule

Reverse proxy MAY provide additional restrictions.

Но:

```text
Nginx rule
!=
application authorization
```

---

# 8. CMP-RUN-002 — Next.js Web Runtime

Основной HTTP/rendering runtime.

Отвечает за:

- public routes;
- admin routes;
- server rendering;
- route handlers;
- server actions where adopted;
- request/session context;
- invoking application services.

---

# 9. Web Runtime Must Remain Thin

Web Runtime MUST NOT превращаться в место хранения domain logic.

Bad:

```text
page.tsx
→ SQL
→ update opportunity stage
```

Correct:

```text
page/action
→ OpportunityApplicationService
→ Domain Transition
→ Repository
```

---

# 10. CMP-RUN-003 — Background Worker Runtime

Отдельный logical runtime для:

```text
media processing
PDF rendering
QR generation
link health
search indexing
notifications
social publishing
AI tasks
cleanup
```

---

# 11. Worker May Share Codebase

Worker MAY быть:

```text
same repository
same TypeScript domain/application packages
same host
```

но должен иметь отдельную execution responsibility.

---

# 12. CMP-RUN-004 — Scheduler

Ответственность:

```text
scheduled jobs
quiet-hour delivery
retry scheduling
temporary Theme expiry
retention cleanup
periodic health/link validation
```

---

# 13. Scheduler Does Not Make Semantic Decisions

Scheduler MAY trigger:

```text
check casting deadline
expire builder session
retry delivery
```

It MUST NOT independently:

```text
book opportunity
publish questionnaire
change contact visibility
```

---

# 14. CMP-PUB-001 — Public Shell

Public layout component.

Owns:

- public header;
- navigation;
- locale entry point;
- footer;
- responsive navigation;
- global public status handling.

Does not own Profile content.

---

# 15. CMP-PUB-002 — Public Profile Page

Projection consumer for:

```text
Actor identity
Quick Facts
Primary professional evidence
Primary CTA
```

Read-only.

---

# 16. CMP-PUB-003 — Public Portfolio

Owns presentation of:

- Portfolio filters;
- portfolio gallery;
- lightbox/viewer;
- categories;
- Primary Full Body / Close-Up presentation.

Reads Portfolio Projection.

---

# 17. CMP-PUB-004 — Emotional Range

Owns presentation of:

```text
Primary Emotional Grid
Shooting Date
Full Emotional Portfolio link
Sessions where published
```

---

# 18. CMP-PUB-005 — Projects

Owns:

```text
project listing
project detail
role context
performances
role media
```

from Project public projection.

---

# 19. CMP-PUB-006 — Professional Media Player

Reusable public media component for:

```text
Video
Audio
Showreel
Video Intro
```

Responsibilities:

- accessible playback;
- error fallback;
- responsive sizing;
- external/local source abstraction.

---

# 20. Media Player Does Not Own Media

It consumes normalized playback model.

---

# 21. CMP-PUB-007 — Questionnaire Hub

Public entry surface for:

```text
Primary prepared questionnaire
alternative questionnaires
PDF downloads
Public Builder entry
```

---

# 22. CMP-PUB-008 — Published Questionnaire Renderer

Reads immutable QuestionnaireRevision projection.

It MUST NOT query mutable Profile facts to fill missing values dynamically.

---

# 23. CMP-PUB-009 — Public Questionnaire Builder UI

Owns interactive temporary selection experience.

Consumes:

```text
Builder Projection
Builder Session state
Generation status
```

No direct Master Data mutation.

---

# 24. CMP-PUB-010 — Contact / Professional Action UI

Handles public professional intents:

```text
Casting invitation
Role offer
Collaboration
Materials request
Questionnaire request
Question
Comment
```

Submits to Feedback application layer.

---

# 25. CMP-PUB-011 — Public Search

Consumes Search Projection.

Must not search Admin/private data.

---

# 26. CMP-PUB-012 — Public Locale Resolver

Determines supported public locale and maintains logical route continuity.

---

# 27. CMP-PUB-013 — Public Error/Recovery Surface

Handles:

```text
404
temporary unavailable
media failure
questionnaire unavailable
session expired
```

without exposing infrastructure detail.

---

# 28. CMP-ADM-001 — Admin Shell

Owns:

```text
sidebar
context header
breadcrumbs
notification entry
global search
authenticated layout
```

---

# 29. Admin Shell Does Not Own Data

It presents navigation/action context only.

---

# 30. CMP-ADM-002 — Dashboard

Read-model consumer combining:

```text
Feedback
Casting deadlines
Opportunity actions
VOP observations
System health
Pending review
```

No independent business state.

---

# 31. Dashboard Command Routing

Any mutation launched from Dashboard MUST route to owning domain service.

---

# 32. CMP-ADM-003 — Profile Workspace

Owns UX for ActorProfile facts and localized content.

Uses Profile application commands.

---

# 33. CMP-ADM-004 — Media Library Workspace

Owns:

```text
upload
asset listing
technical metadata
derivative status
usage lookup
source management
```

Does NOT own Portfolio classification.

---

# 34. CMP-ADM-005 — Portfolio Workspace

Owns:

```text
professional classification
primary close-up/full-body
ordering
publication
archive
```

References MediaAsset.

---

# 35. CMP-ADM-006 — Emotional Workspace

Owns:

```text
Emotional Sessions
shooting dates
source media selection
published session state
```

---

# 36. CMP-ADM-007 — Emotional Grid Editor

Specialized editor for:

```text
dimensions
exact cells
crop
pan
scale
rotation
confirmation
finalization
```

---

# 37. Grid Editor Boundary

Grid Editor cannot modify original MediaAsset binary.

---

# 38. CMP-ADM-008 — Projects Workspace

Owns Admin UX for:

```text
Projects
Roles
RoleMedia
Performances
featured state
public preview
```

---

# 39. CMP-ADM-009 — Training Workspace

Dedicated Training domain UI.

Must not use Project `Other` as long-term editing path.

---

# 40. CMP-ADM-010 — Skills & Languages Workspace

Owns confirmed:

```text
skills
5-level proficiency
languages
CEFR
```

AI suggestions may appear but must remain distinguished.

---

# 41. CMP-ADM-011 — Achievements Workspace

Owns Achievements administration.

---

# 42. CMP-ADM-012 — Professional Links Workspace

Owns:

```text
number
description
canonical URL
link type
link health presentation
QR usage visibility
```

---

# 43. CMP-ADM-013 — Contacts Workspace

Owns:

```text
Contact master data
public visibility
prepared questionnaire visibility
builder visibility
guardian/parent relation
```

---

# 44. CMP-ADM-014 — Questionnaires Workspace

Owns:

```text
Questionnaire Definitions
Drafts
blocks
selection
readiness
publication
primary state
revisions
artifacts
```

---

# 45. CMP-ADM-015 — Public Builder Settings Workspace

Owns Admin configuration for:

```text
templates
defaults
required sections
limits
eligibility preview
public preview
```

It does not own visitor sessions.

---

# 46. CMP-ADM-016 — Feedback Inbox

UX workspace over Feedback domain.

Owns:

- filtering;
- assignment;
- notes;
- next action;
- Create/Link Casting interaction.

---

# 47. CMP-ADM-017 — Casting Workspace

Owns UI for:

```text
Casting
Source
Requirements
Profile Match
Recommendations
Materials
Questionnaire
Opportunity relation
History
```

---

# 48. CMP-ADM-018 — Opportunity Workspace

Owns presentation of authoritative Opportunity workflow.

Supports:

```text
list
stage view
Kanban where used
next actions
history
```

---

# 49. CMP-ADM-019 — Notification Center

Owns Admin consumption of durable Notifications.

Notification read-state only.

---

# 50. CMP-ADM-020 — Virtual Operator Workspace

Owns:

```text
observations
recommendations
safe automation history
resolve/defer/dismiss
```

---

# 51. CMP-ADM-021 — BB Assistant Workspace

Owns:

```text
task selection
source context view
generate
edit
apply
discard
history
```

---

# 52. CMP-ADM-022 — Theme Workspace

Owns:

```text
current theme
draft revisions
AI proposal
desktop/tablet/mobile preview
accessibility checks
publish
rollback
temporary activation
```

---

# 53. CMP-ADM-023 — Analytics Workspace

Consumes Analytics and business projections.

Cannot modify public content automatically.

---

# 54. CMP-ADM-024 — Social Publishing Workspace

Owns:

```text
connections
SocialPost Draft
platform targets
schedule
publish status
```

---

# 55. CMP-ADM-025 — Help / Tickets Workspace

Owns Help Center and private support tickets.

---

# 56. CMP-ADM-026 — Audit Workspace

Read-only administrative access to AuditEvents.

---

# 57. CMP-ADM-027 — System Status Workspace

Shows:

```text
database
storage
worker
integrations
job failures
```

It is operational presentation, not infrastructure authority.

---

# 58. Application Layer Doctrine

Application components implement use cases.

They SHOULD be named around actions/business tasks rather than CRUD tables.

---

# 59. CMP-APP-001 — Profile Application Service

Commands:

```text
UpdateProfileFacts
UpdateBiography
UpdateLocalizedProfileContent
Publish/Visibility actions where applicable
```

---

# 60. CMP-APP-002 — Media Application Service

Commands:

```text
RegisterUpload
FinalizeUpload
RequestDerivative
ArchiveMedia
DeleteMediaWithDependencyCheck
```

---

# 61. CMP-APP-003 — Portfolio Application Service

Commands:

```text
CreatePortfolioItem
ClassifyPortfolioItem
PublishPortfolioItem
ArchivePortfolioItem
SetPrimaryCloseUp
SetPrimaryFullBody
ReorderPortfolio
```

---

# 62. CMP-APP-004 — Emotional Application Service

Commands:

```text
CreateEmotionalSession
SetShootingDate
AttachSessionMedia
CreateGrid
UpdateCellCrop
ConfirmCell
FinalizeGrid
PublishGrid
SetPrimaryGrid
```

---

# 63. CMP-APP-005 — Project Application Service

Commands:

```text
CreateProject
UpdateProject
CreateRole
AttachRoleMedia
SetRoleShowcase
AddPerformance
PublishProject
ArchiveProject
```

---

# 64. CMP-APP-006 — Training Application Service

Owns Training mutations and legacy migration target operations.

---

# 65. CMP-APP-007 — Competency Application Service

Owns Skill/Language confirmed mutations.

---

# 66. CMP-APP-008 — Professional Link Application Service

Commands:

```text
CreateLink
UpdateCanonicalUrl
ArchiveLink
RequestLinkCheck
```

URL update emits dependent invalidations.

---

# 67. CMP-APP-009 — Contact Application Service

Commands:

```text
CreateContact
UpdateContact
ChangeVisibility
ArchiveContact
```

---

# 68. CMP-APP-010 — Questionnaire Application Service

Major commands:

```text
CreateQuestionnaire
SaveDraft
ChangeBlocks
ChangeSelections
ValidateReadiness
PublishRevision
SetPrimaryQuestionnaire
ArchiveQuestionnaire
RestoreRevisionToDraft
```

---

# 69. CMP-APP-011 — Public Builder Application Service

Owns:

```text
CreateSession
UpdateConfiguration
RevalidateSession
CreateGenerationSnapshot
GenerateDocument
ExpireSession
```

---

# 70. Public Builder Command Constraint

No command in Builder service may write Actor/Profile-owned Master Data.

---

# 71. CMP-APP-012 — Feedback Application Service

Commands:

```text
SubmitFeedback
MarkRead
Assign
AddInternalNote
SetNextAction
ResolveFeedback
```

---

# 72. SubmitFeedback Transaction

Must persist:

```text
Feedback
Attachments metadata where committed
Outbox/Notification request
```

according to transaction architecture.

---

# 73. CMP-APP-013 — Casting Application Service

Commands:

```text
CreateCasting
CreateCastingFromFeedback
AddSource
CloseCasting
LinkQuestionnaire
CreateOpportunity
```

---

# 74. CMP-APP-014 — Casting Analysis Application Service

Commands:

```text
RequestAnalysis
ReviewRequirement
ConfirmRequirement
ModifyRequirement
RejectRequirement
RecalculateProfileMatch
```

---

# 75. CMP-APP-015 — Opportunity Application Service

Single authority for:

```text
CreateOpportunity
TransitionStage
SetNextAction
Book
CloseNotSelected
Withdraw
CreateProjectDraftFromBooked
```

---

# 76. Opportunity Transition Contract

All UIs MUST invoke this service.

No independent mutation in Kanban.

---

# 77. CMP-APP-016 — Notification Application Service

Owns notification intent and delivery orchestration.

---

# 78. CMP-APP-017 — BB Assistant Application Service

Commands:

```text
GenerateDraft
RegenerateDraft
ApplyDraft
DiscardDraft
```

---

# 79. CMP-APP-018 — VOP Application Service

Commands:

```text
Create/UpsertObservation
Acknowledge
Defer
Dismiss
Resolve
ApproveRecommendation
ExecuteSafeAction
```

---

# 80. CMP-APP-019 — Theme Application Service

Commands:

```text
CreateThemeDraft
GenerateThemeProposal
ApplyProposal
ValidateTheme
PublishThemeRevision
ActivateTheme
RollbackTheme
ScheduleTemporaryTheme
```

---

# 81. CMP-APP-020 — Social Application Service

Commands:

```text
CreateSocialPost
UpdatePost
SchedulePost
PublishPost
RetryPlatform
```

---

# 82. CMP-APP-021 — Help Application Service

Owns Help/Ticket use cases.

---

# 83. CMP-APP-022 — Audit Query Service

Read-only querying/filtering of audit history.

Audit writes usually occur via shared AuditWriter component.

---

# 84. CMP-APP-023 — Public Projection Service

Builds public-safe read models.

---

# 85. CMP-APP-024 — Builder Projection Service

Builds Builder-safe eligible read model.

Distinct from generic Public Projection.

---

# 86. CMP-APP-025 — Admin Projection Service

Provides composite Admin read models where multiple domains are required.

Must remain read-only.

---

# 87. CMP-APP-026 — Readiness Service

Central pattern for deterministic readiness calculation.

Domain-specific implementations:

```text
QuestionnaireReadiness
EmotionalGridReadiness
ThemeReadiness
BuilderReadiness
```

---

# 88. Readiness Service Is Not Generic Scoring AI

It returns deterministic:

```text
blockers
warnings
status
```

---

# 89. CMP-APP-027 — Dependency Impact Service

Determines `Used In` / dependency impact before:

```text
delete
archive
visibility change
URL change
```

---

# 90. Dependency Service Inputs

May read across modules.

It must not perform hidden cross-domain mutations.

---

# 91. CMP-DOM-001 — Profile Domain

Owns ActorProfile rules.

---

# 92. CMP-DOM-002 — Media Domain

Owns MediaAsset identity and source invariants.

---

# 93. CMP-DOM-003 — Portfolio Domain

Owns professional photo classification and Primary markers.

---

# 94. CMP-DOM-004 — Emotional Domain

Owns EmotionalSession/Grid invariants.

---

# 95. CMP-DOM-005 — Project Domain

Owns Project, Role, Performance and role-context rules.

---

# 96. CMP-DOM-006 — Training Domain

Owns Training.

---

# 97. CMP-DOM-007 — Competency Domain

Owns:

```text
ActorSkill
ActorLanguageProficiency
```

---

# 98. CMP-DOM-008 — Link Domain

Owns ProfessionalLink canonical URL and lifecycle.

---

# 99. CMP-DOM-009 — Contact Domain

Owns Contact and visibility permissions.

---

# 100. CMP-DOM-010 — Questionnaire Domain

Owns:

```text
QuestionnaireDefinition
QuestionnaireRevision
publication rules
primary uniqueness
```

---

# 101. CMP-DOM-011 — Public Builder Domain

Owns temporary Builder Session/Template/Generation Snapshot semantics.

---

# 102. CMP-DOM-012 — Feedback Domain

Owns external inbound professional inquiry.

---

# 103. CMP-DOM-013 — Casting Domain

Owns:

```text
Casting
CastingSource
confirmed requirements
casting lifecycle
```

---

# 104. CMP-DOM-014 — Opportunity Domain

Owns pipeline states and stage transitions.

---

# 105. CMP-DOM-015 — Notification Domain

Owns notification intent and channel delivery records.

---

# 106. CMP-DOM-016 — BB Assistant Domain

Owns AIDraft lifecycle/provenance.

---

# 107. CMP-DOM-017 — VOP Domain

Owns Observation/Recommendation/Execution records.

---

# 108. CMP-DOM-018 — Theme Domain

Owns ThemeRevision and Activation semantics.

---

# 109. CMP-DOM-019 — Social Domain

Owns internal SocialPost and publish attempts.

---

# 110. CMP-DOM-020 — Support Domain

Owns SupportTicket lifecycle.

---

# 111. CMP-DOM-021 — Audit Domain

Owns immutable AuditEvent semantics.

---

# 112. Domain Components Must Be Framework-Independent

They SHOULD NOT depend on:

```text
NextRequest
React components
HTML
Nginx
provider SDK request types
```

---

# 113. CMP-DATA-001 — PostgreSQL Database

Primary persistent authority.

---

# 114. CMP-DATA-002 — Profile Repository

Reads/writes Profile-owned persistence.

---

# 115. CMP-DATA-003 — Media Repository

Stores media metadata, not source binary.

---

# 116. CMP-DATA-004 — Portfolio Repository

Owns Portfolio persistence queries.

---

# 117. Repository Rule

Repositories SHOULD correspond to aggregate/module responsibilities.

Avoid:

```text
GenericRepository<any>
```

for critical domain writes.

---

# 118. CMP-DATA-005 — Emotional Repository

Responsible for:

```text
sessions
grids
cells
finalized version metadata
```

---

# 119. CMP-DATA-006 — Project Repository

---

# 120. CMP-DATA-007 — Questionnaire Repository

Must support:

```text
draft state
revision history
current published pointer
primary uniqueness
```

---

# 121. CMP-DATA-008 — Builder Repository

Stores durable temporary sessions/snapshots.

---

# 122. CMP-DATA-009 — Feedback Repository

---

# 123. CMP-DATA-010 — Casting Repository

---

# 124. CMP-DATA-011 — Opportunity Repository

Must support atomic stage/version update and history append.

---

# 125. CMP-DATA-012 — Notification Repository

---

# 126. CMP-DATA-013 — AI Draft Repository

---

# 127. CMP-DATA-014 — Theme Repository

---

# 128. CMP-DATA-015 — Audit Repository

Append-oriented.

---

# 129. Repository Transaction Context

Application services SHOULD be able to coordinate several repositories inside one DB transaction where business invariant requires.

---

# 130. Repository Escape Hatch

Raw SQL MAY be used internally for:

- complex reads;
- migration;
- performance-sensitive queries;

but must remain encapsulated and tested.

---

# 131. CMP-MED-001 — Media Storage Adapter

Interface:

```text
store original
read original
write derivative
read derivative
delete permitted object
check existence
```

---

# 132. Storage Adapter Must Know Storage, Not Business Meaning

It should not decide:

```text
this is Primary Close-Up
```

---

# 133. CMP-MED-002 — Local Filesystem Storage Adapter

Initial/self-hosted implementation.

---

# 134. CMP-MED-003 — Private File Delivery Component

Serves:

```text
Feedback attachments
Casting source assets
private documents
```

after server authorization.

---

# 135. CMP-MED-004 — Public Media Delivery Component

Maps approved media/derivatives to stable public URLs.

---

# 136. CMP-MED-005 — Image Derivative Processor

Generates:

```text
thumbnail
web variants
PDF variants
posters where applicable
```

---

# 137. CMP-MED-006 — Media Metadata Extractor

Extracts deterministic technical:

```text
width
height
duration
mime
size
orientation
```

---

# 138. CMP-MED-007 — Emotional Grid Renderer

Consumes exact finalized Grid config and source media.

Outputs composite.

---

# 139. Emotional Renderer Restrictions

No generative face/body transformation.

---

# 140. CMP-MED-008 — Media Usage Resolver

Read-only component answering:

```text
Where is this MediaAsset used?
```

---

# 141. CMP-DOC-001 — Document Model Builder

Transforms immutable Questionnaire Revision/Snapshot into normalized semantic document model.

---

# 142. Document Model Responsibilities

Produces sections such as:

```text
Actor Identity
Photos
Parameters
Skills
Languages
Emotional Composite
Projects
Training
Links
Contacts
Metadata
```

---

# 143. CMP-DOC-002 — HTML Questionnaire Renderer

Consumes Document Model.

---

# 144. CMP-DOC-003 — PDF Renderer

Consumes same or semantically equivalent Document Model.

---

# 145. CMP-DOC-004 — PDF Artifact Store

Stores generated artifact metadata/binary reference.

---

# 146. CMP-DOC-005 — Document Filename Builder

Produces safe normalized filename based on:

```text
actor
questionnaire/template
casting/role where applicable
locale
```

---

# 147. CMP-DOC-006 — Document Accessibility Adapter

Responsible for renderer-supported:

```text
document language
metadata
tagging/reading order hooks
link semantics
alt metadata
```

---

# 148. CMP-QR-001 — Canonical URL Resolver

Takes domain link/route reference and returns exact safe canonical URL.

---

# 149. CMP-QR-002 — QR Eligibility Validator

Rejects:

```text
private URLs
admin URLs
unsafe schemes
unstable internal routes
```

---

# 150. CMP-QR-003 — QR Generator

Generates vector/high-resolution representation.

---

# 151. CMP-QR-004 — QR Decode Validator

Decodes generated QR and verifies exact URL equality.

---

# 152. CMP-QR-005 — QR Artifact Repository/Cache

Stores derived QR artifacts.

---

# 153. QR Generator Must Not Resolve Business Visibility Alone

Visibility decision comes from owning/publication context + QR Eligibility policy.

---

# 154. CMP-JOB-001 — Durable Job Store

Persistent queue/job metadata.

---

# 155. CMP-JOB-002 — Job Dispatcher

Registers durable work after application command/outbox.

---

# 156. CMP-JOB-003 — Job Worker

Claims and executes jobs.

---

# 157. CMP-JOB-004 — Retry Policy

Centralized rules for:

```text
transient failure
max attempts
backoff
terminal failure
```

---

# 158. Retry Policy Must Be Capability-Aware

Example:

```text
link check
```

can retry differently from:

```text
social publish
```

---

# 159. CMP-JOB-005 — Transactional Outbox

Stores committed event/side-effect intent inside DB transaction.

---

# 160. CMP-JOB-006 — Outbox Dispatcher

Converts outbox records into worker execution.

---

# 161. CMP-JOB-007 — Scheduled Job Registry

Handles timed/preauthorized jobs.

---

# 162. CMP-JOB-008 — Job Deduplication / Idempotency Guard

Prevents duplicate processing.

---

# 163. CMP-JOB-009 — Job Failure Monitor

Feeds:

```text
System Status
VOP observations
Admin diagnostics
```

---

# 164. CMP-SRCH-001 — Public Search Indexer

Consumes public projection events.

---

# 165. CMP-SRCH-002 — Public Search Query Service

Read-only public search.

---

# 166. CMP-SRCH-003 — Admin Search Query Service

Authorized cross-domain Admin search.

---

# 167. CMP-SRCH-004 — Search Rebuild Service

Can reconstruct indexes from authoritative Source.

---

# 168. Initial Search Backend

PostgreSQL-based search is preferred unless later performance requirements justify dedicated engine.

---

# 169. CMP-CACHE-001 — Public Projection Cache

Caches approved public read models.

---

# 170. CMP-CACHE-002 — Revision Artifact Cache

Optimizes immutable revision responses.

---

# 171. CMP-CACHE-003 — Cache Invalidation Coordinator

Consumes domain events.

---

# 172. Cache Invalidator Must Be Context-Aware

Examples:

```text
ContactVisibilityChanged
→ invalidate Public Contact projection
→ invalidate Builder eligibility
```

---

# 173. CMP-CACHE-004 — Request-local Cache

May deduplicate repeated reads inside one request.

No business authority.

---

# 174. CMP-AI-001 — AI Provider Adapter

Provider-neutral infrastructure interface.

Responsibilities:

```text
send request
receive output
timeout
provider errors
usage metadata where required
```

---

# 175. AI Provider Adapter Does Not Own Prompts

Capability-specific prompt/task design belongs application AI components.

---

# 176. CMP-AI-002 — AI Context Builder

Builds minimum permitted Source context for a specific capability.

---

# 177. Context Builder Must Be Capability-Specific

Avoid:

```text
buildEverythingForAI()
```

---

# 178. CMP-AI-003 — AI Output Schema Validator

Treats model output as untrusted.

---

# 179. CMP-AI-004 — Casting AI Engine

Owns AI-specific:

```text
requirement extraction
source structuring
recommendation generation
```

It does not confirm requirements.

---

# 180. CMP-AI-005 — Casting Profile Match Engine

Prefer deterministic comparison of structured requirements vs confirmed Profile.

May use AI only for cases that inherently require semantic interpretation.

---

# 181. CMP-AI-006 — BB Generation Engine

Supports task types defined in domain docs.

---

# 182. CMP-AI-007 — Theme Proposal Engine

Produces structured visual proposal only.

---

# 183. CMP-AI-008 — VOP Analysis Assistant

May assist:

```text
grouping
explanation
recommendation drafting
```

while deterministic observations remain system-owned where possible.

---

# 184. CMP-AI-009 — AI Provenance Recorder

Stores:

```text
capability
source snapshot
prompt version
model metadata
output identity
```

---

# 185. AI Components Must Not Share Authority

A shared provider adapter is allowed.

A generic AI output must still be persisted under correct domain capability.

---

# 186. CMP-NOT-001 — Notification Creator

Transforms committed business/system event into Notification intent.

---

# 187. CMP-NOT-002 — Notification Policy Engine

Evaluates:

```text
channel
priority
quiet hours
recipient configuration
minimal payload
```

---

# 188. CMP-NOT-003 — In-App Notification Adapter

Creates durable Admin notification display state.

---

# 189. CMP-NOT-004 — WhatsApp Adapter

Official/approved provider adapter.

---

# 190. CMP-NOT-005 — Email Adapter

If enabled.

---

# 191. CMP-NOT-006 — Delivery Worker

Handles channel send/retry.

---

# 192. CMP-NOT-007 — Delivery Idempotency Guard

Prevents duplicate delivery.

---

# 193. CMP-NOT-008 — Notification Failure Reporter

Feeds Admin/VOP without changing source business entity.

---

# 194. CMP-ANL-001 — Public Analytics Collector

Collects allowed nonblocking interaction events.

---

# 195. CMP-ANL-002 — Server Business Event Collector

Consumes authoritative events:

```text
FeedbackCreated
CastingCreated
OpportunityBooked
```

---

# 196. CMP-ANL-003 — Attribution Resolver

Resolves:

```text
direct
search
social
campaign
QR
shared questionnaire
casting-specific link
```

---

# 197. CMP-ANL-004 — Analytics Aggregator

Builds metrics/read models.

---

# 198. CMP-ANL-005 — Recommendation Layer

May produce Analytics-derived recommendations.

Never auto-mutates professional content.

---

# 199. CMP-AUD-001 — Audit Writer

Standard application capability for append-only significant action records.

---

# 200. Audit Writer Inputs

```text
actor
action
entity
before/after or structured change
correlation id
timestamp
```

---

# 201. CMP-AUD-002 — Audit Query

Read/filter/search AuditEvents.

---

# 202. CMP-SEC-001 — Authentication Component

Validates Admin identity/session.

---

# 203. CMP-SEC-002 — Authorization Component

Answers:

```text
Can actor perform operation X on resource Y?
```

---

# 204. CMP-SEC-003 — Public Token Validator

Validates:

```text
private casting tokens
unlisted questionnaire tokens
other scoped public tokens
```

---

# 205. CMP-SEC-004 — Anti-Abuse Component

Public endpoints:

```text
rate limit
submission throttling
payload controls
bot protection hooks
```

---

# 206. CMP-SEC-005 — Input Security Validator

Cross-cutting rules for:

```text
URLs
files
MIME
unsafe content
schemes
```

---

# 207. CMP-SEC-006 — Secret Provider

Secure access to:

```text
database credentials
OAuth secrets
AI keys
WhatsApp credentials
```

---

# 208. Secrets Must Not Live in Domain Entities

Normative.

---

# 209. CMP-INT-001 — Instagram Adapter

External OAuth/publishing boundary.

---

# 210. CMP-INT-002 — TikTok Adapter

---

# 211. CMP-INT-003 — WhatsApp Provider Adapter

May coincide with notification adapter implementation.

---

# 212. CMP-INT-004 — AI Provider Adapter

Provider-specific implementation behind CMP-AI-001.

---

# 213. CMP-INT-005 — External Media Resolver

For supported external media URLs.

May normalize:

```text
provider
embed/playback metadata
canonical URL
```

---

# 214. External Media Resolver Must Not Circumvent Link Safety

---

# 215. CMP-OBS-001 — Structured Logger

---

# 216. CMP-OBS-002 — Error Reporter

Optional internal/external error reporting integration.

---

# 217. CMP-OBS-003 — Health Check Service

Checks:

```text
Web Runtime
PostgreSQL
Media Storage
Worker
optional adapters
```

---

# 218. CMP-OBS-004 — Correlation Context

Carries request/command/job correlation identity.

---

# 219. CMP-OBS-005 — System Metrics

May expose:

```text
request health
job backlog
job failure counts
storage status
```

---

# 220. Component Dependency Layers

Canonical dependency direction:

```text
UI
 ↓
Application
 ↓
Domain

Application
 ↓
Repository Interfaces
 ↓
Infrastructure Implementations
```

---

# 221. Allowed Dependency A

Public/Admin Presentation MAY depend on:

```text
Application Commands
Projection Queries
Shared UI primitives
```

---

# 222. Forbidden Dependency A

Presentation MUST NOT directly depend on:

```text
raw database connection
filesystem implementation
AI provider SDK
```

---

# 223. Allowed Dependency B

Application MAY depend on:

```text
Domain
Repository interfaces
Job interfaces
Audit interface
Integration interfaces
```

---

# 224. Forbidden Dependency B

Application use case SHOULD NOT depend on React/Next.js page implementation.

---

# 225. Allowed Dependency C

Domain MAY depend on:

```text
pure domain types
value objects
domain policies
```

---

# 226. Forbidden Dependency C

Domain MUST NOT depend on:

```text
Prisma-specific records
Next.js
filesystem
PDF renderer
OpenAI/provider SDK
```

where avoidable.

---

# 227. Infrastructure MAY Depend on Domain/Application Contracts

Adapters implement interfaces defined inward.

---

# 228. Cross-Domain Query Dependency

Read model MAY read several repositories.

Example Dashboard:

```text
Feedback
Casting
Opportunity
VOP
```

---

# 229. Cross-Domain Command Dependency

Should be orchestrated in Application layer.

Example:

```text
CreateProjectFromBookedOpportunity
```

---

# 230. No Domain-to-Domain Repository Mutation

Prohibited:

```text
Opportunity Domain
→ directly INSERT project table
```

Correct:

```text
Application Orchestrator
→ validate Opportunity
→ ProjectApplicationService/CreateDraft
→ provenance
```

---

# 231. Shared Kernel

A small shared domain kernel MAY contain:

```text
EntityId types
Locale
Timestamp abstractions
Result/Error types
Version
ActorContext
```

---

# 232. Shared Kernel Must Stay Small

Do not move all business logic into:

```text
shared/
utils/
common/
```

---

# 233. Utility Anti-Pattern

If a utility knows:

```text
Portfolio Primary
Contact Builder permission
Opportunity Booked
```

it likely belongs Domain/Application module.

---

# 234. Component Ownership Matrix

| Component | Owns Writes? | Primary Data |
|---|---:|---|
| Public UI | No | projections |
| Admin UI | No direct | commands/projections |
| Profile Service | Yes | ActorProfile |
| Media Service | Yes | Media metadata |
| Portfolio Service | Yes | Portfolio |
| Questionnaire Service | Yes | Questionnaire |
| Builder Service | Yes temporary | Builder session |
| Feedback Service | Yes | Feedback |
| Casting Service | Yes | Casting |
| Opportunity Service | Yes | Opportunity |
| AI Engines | AI output only | analysis/drafts |
| PDF Renderer | Artifact only | derived |
| QR Generator | Artifact only | derived |
| Search | No source writes | derived index |
| Analytics | events/aggregates | analytics |
| Theme Service | Yes | Theme |
| VOP | observations/execution | VOP data |

---

# 235. Data Read Matrix

## Public UI

Can read:

```text
Public Projections
Builder Projection
public artifact metadata
```

---

# 236. Admin UI

Can read authorized:

```text
Master
Operational
Revision
Audit
System status
```

through application/query interfaces.

---

# 237. Worker

Can read only context required by claimed job.

---

# 238. AI Provider

Receives only explicit context payload built for task.

No DB access.

---

# 239. External Notification Provider

Receives only delivery payload.

No application database access.

---

# 240. Component Security Boundary

Adapters to external systems are considered trust boundary.

Validate both outgoing scope and incoming response.

---

# 241. External Provider Response Rule

Provider success response does not automatically mean domain success if internal persistence failed.

Ordering must follow use-case semantics.

---

# 242. Component Error Translation

Each layer translates errors upward appropriately.

Example:

```text
filesystem ENOSPC
↓
MediaStorageUnavailable
↓
Application Processing Failure
↓
Admin actionable state
```

---

# 243. Do Not Leak Infrastructure Error

Public never receives:

```text
ENOENT /data/kate-actor/media/...
```

---

# 244. Component Timeouts

External adapter must use bounded timeout.

No request waits forever for AI/WhatsApp/provider.

---

# 245. Circuit/Degradation Strategy

Optional external integrations MAY use failure throttling/circuit-like behaviour if repeated failures occur.

Exact implementation later.

---

# 246. Component Idempotency Matrix

| Component | Requires idempotency |
|---|---:|
| Feedback Submit | Yes |
| Questionnaire Publish | Yes |
| Builder PDF Generation | Yes/reusable |
| QR Generation | Yes/reusable |
| Notification Delivery | Yes |
| Social Publish | Yes |
| Opportunity Transition | Yes for retries |
| Project Draft from Booked | Yes |
| Theme Activation | Yes |
| Media Derivative | Yes |

---

# 247. Component Concurrency Matrix

Strong concurrency controls required for:

```text
Primary Portfolio
Primary Questionnaire
Questionnaire Draft/Publish
Contact visibility
Emotional Grid finalization
Opportunity stage
Theme publish/activation
```

---

# 248. Component Event Producers

Examples:

```text
ProfileService
→ ProfileChanged

PortfolioService
→ PrimaryPortfolioChanged

QuestionnaireService
→ QuestionnairePublished

FeedbackService
→ FeedbackCreated

OpportunityService
→ OpportunityStageChanged
```

---

# 249. Component Event Consumers

Example:

```text
QuestionnairePublished
 ├─ Document Job
 ├─ Public Cache Invalidator
 ├─ Search/Projection update if relevant
 ├─ Analytics
 └─ Audit already coupled to command
```

---

# 250. Events Must Be Business-Semantic

Prefer:

```text
ContactVisibilityChanged
```

over:

```text
RowUpdated(table=contacts)
```

---

# 251. Component Public/Private Classification

Every component dealing with files/data MUST classify output as:

```text
PUBLIC
PRIVATE
ADMIN_ONLY
TOKEN_SCOPED
DERIVED_PUBLIC
```

where applicable.

---

# 252. Public Media Delivery

May expose only PUBLIC/DERIVED_PUBLIC.

---

# 253. Private File Delivery

Handles PRIVATE/ADMIN_ONLY/TOKEN_SCOPED after authorization.

---

# 254. Document Access Classification

Examples:

```text
Primary public questionnaire PDF → PUBLIC
Builder-generated anonymous PDF → session/token-scoped as designed
Casting-specific questionnaire → TOKEN_SCOPED
Admin preview → ADMIN_ONLY
```

---

# 255. Component Testing Responsibility

Every component class requires matching test category.

---

# 256. Domain Component Tests

Must test:

```text
invariants
state transitions
ownership
invalid transitions
```

---

# 257. Application Component Tests

Must test:

```text
use-case orchestration
transactions
authorization
idempotency
side effects
```

---

# 258. Infrastructure Component Tests

Must test:

```text
storage
provider integration
serialization
failure translation
```

---

# 259. Projection Component Tests

Must test:

```text
visibility
locale
private-data exclusion
historical/current distinction
```

---

# 260. Worker Component Tests

Must test:

```text
claiming
retry
deduplication
crash recovery
```

---

# 261. Public UI Component Tests

Must test:

```text
critical flow
accessibility
responsive behaviour
failure states
```

---

# 262. Admin UI Tests

Must test:

```text
correct domain command routing
dirty/conflict states
permission behaviour
```

---

# 263. Component Deployment Units

Initial recommended deployment units:

```text
1. Web Runtime
2. Worker Runtime
3. PostgreSQL
4. Media Storage
5. Reverse Proxy
```

Optional external providers remain external.

---

# 264. Scheduler Deployment

Scheduler MAY:

```text
run inside worker runtime
```

if single-instance/locking semantics prevent duplicate schedules.

---

# 265. Multi-Worker Scheduler Safety

If multiple workers:

scheduled claims must be distributed/locked.

---

# 266. Component Scalability

Web and Worker SHOULD scale independently in future.

---

# 267. Database Remains Shared Transactional Core

No independent per-domain databases at current scale.

---

# 268. Why Shared PostgreSQL Is Acceptable

Modular boundaries are code/domain boundaries.

Distributed DB isolation is not required for a modular monolith.

---

# 269. But Shared DB Does Not Permit Arbitrary Writes

Normative.

---

# 270. Component Configuration

Runtime configuration SHOULD be centralized/type-safe.

Examples:

```text
database URL
storage root
public base URL
provider endpoints
job settings
feature flags
```

---

# 271. Environment Configuration ≠ Business Configuration

Environment:

```text
MEDIA_ROOT=/data/...
```

Business:

```text
Public Builder enabled
```

must not be conflated.

---

# 272. Business Configuration

Store in application/domain configuration where auditable/manageable.

---

# 273. Feature Flags

MAY support staged rollout.

Feature flag must not permanently replace actual lifecycle/domain state.

---

# 274. Feature Flag Security

Disabled UI flag does not substitute authorization.

---

# 275. Component Naming Principle

Names should describe responsibility.

Good:

```text
QuestionnaireRevisionRenderer
BuilderEligibilityProjection
OpportunityTransitionService
```

Bad:

```text
Manager
Helper
Utils2
DataHandler
```

---

# 276. Component Interface Principle

Interfaces SHOULD expose business intent.

Good:

```text
publishQuestionnaire()
```

Bad:

```text
save(data)
```

when semantics matter.

---

# 277. Component Boundary Example — Questionnaire

```text
Admin Questionnaire UI
      ↓
QuestionnaireApplicationService
      ↓
Questionnaire Domain
      ↓
QuestionnaireRepository
      ↓
PostgreSQL

Publish:
      ↓
Revision created
      ↓
Outbox
      ↓
PDF Worker
      ↓
Document Model
      ↓
PDF Renderer
```

---

# 278. Component Boundary Example — Builder

```text
Public Builder UI
      ↓
BuilderApplicationService
      ↓
BuilderEligibilityProjection
      ↓
BuilderSessionRepository

Generate:
      ↓
Server Revalidation
      ↓
Generation Snapshot
      ↓
Document Job
```

---

# 279. Component Boundary Example — Casting

```text
Casting Admin UI
      ↓
CastingApplicationService
      ↓
Casting Domain
      ↓
CastingRepository

AI:
CastingAnalysisService
      ↓
Context Builder
      ↓
AI Provider
      ↓
Schema Validation
      ↓
Analysis Revision
      ↓
Human Review
```

---

# 280. Component Boundary Example — Feedback

```text
Public Contact UI
      ↓
FeedbackApplicationService
      ↓
Validation / Anti-Abuse
      ↓
Feedback Domain
      ↓
FeedbackRepository
      ↓
COMMIT
      ↓
Outbox
      ↓
Notification Service
```

---

# 281. Component Boundary Example — Opportunity

```text
Dashboard/Kanban/Detail
          ↓
OpportunityApplicationService
          ↓
Opportunity Domain Transition
          ↓
Repository + StageHistory
          ↓
Audit/Event
```

---

# 282. Component Boundary Example — QR

```text
Questionnaire Document Model
      ↓
Canonical URL Resolver
      ↓
QR Eligibility
      ↓
QR Generator
      ↓
Decode Validator
      ↓
Artifact
```

---

# 283. Component Boundary Example — VOP

```text
Domain Event / Health Result
      ↓
Observation Detector
      ↓
VOPObservation
      ↓
Recommendation
      ↓
Human Approval
OR
AUTO-4 Safe Execution
      ↓
Existing Application Command
```

---

# 284. VOP Detector Components

VOP MAY have specialized detectors:

```text
BrokenLinkDetector
StaleQuestionnaireDetector
MissingMetadataDetector
FailedJobDetector
AccessibilityIssueDetector
```

---

# 285. Detector Rule

Detector creates Observation.

It MUST NOT silently fix semantic professional content.

---

# 286. Component Dependency Graph

```text
Presentation
   │
   ▼
Application Services
   │
   ├──────────────► Projection Services
   │
   ▼
Domain
   │
   ▼
Repository Interfaces
   │
   ▼
Infrastructure

Application
   ├────────► Job Interface
   ├────────► Audit Interface
   ├────────► AI Interface
   ├────────► Notification Interface
   └────────► Storage Interface
```

---

# 287. Forbidden Dependency Graph

```text
Public UI ─X→ PostgreSQL
Admin UI  ─X→ filesystem
Domain    ─X→ OpenAI/provider SDK
PDF       ─X→ mutable live Profile
QR        ─X→ private URL guessing
VOP       ─X→ arbitrary SQL update
Analytics ─X→ Opportunity transition
Theme     ─X→ Contact visibility
```

---

# 288. Component Anti-Pattern CMP-AP-001

**Generic service with all database access**

---

# 289. CMP-AP-002

**Every Admin page imports ORM client**

---

# 290. CMP-AP-003

**Worker reimplements business rules independently**

---

# 291. CMP-AP-004

**AI service directly updates Profile**

---

# 292. CMP-AP-005

**Public Builder calls Contact repository directly**

---

# 293. CMP-AP-006

**PDF renderer queries live database itself**

---

# 294. CMP-AP-007

**QR generator decides whether Contact is public**

---

# 295. CMP-AP-008

**Notification adapter updates Feedback state**

---

# 296. CMP-AP-009

**Dashboard maintains separate pipeline status**

---

# 297. CMP-AP-010

**Search query service contains writable CRUD**

---

# 298. CMP-AP-011

**Media processor changes Portfolio category**

---

# 299. CMP-AP-012

**Theme renderer controls authorization**

---

# 300. CMP-AP-013

**Social adapter stores OAuth token in SocialPost**

---

# 301. CMP-AP-014

**Job worker reads unspecified current entity instead of exact job input/revision**

---

# 302. CMP-AP-015

**Shared `utils` becomes hidden domain layer**

---

# 303. CMP-AP-016

**Application service returns ORM entities directly to Public client**

---

# 304. CMP-AP-017

**Private attachments share public static directory**

---

# 305. CMP-AP-018

**Same cache namespace for public and private projections**

---

# 306. CMP-AP-019

**Analytics collector includes private Feedback body**

---

# 307. CMP-AP-020

**Support Ticket and Feedback share same business entity**

---

# 308. Component Quality Gate

Каждый component MUST иметь:

- [ ] Component ID;
- [ ] clear responsibility;
- [ ] input contract;
- [ ] output contract;
- [ ] owner domain;
- [ ] allowed dependencies;
- [ ] forbidden dependencies;
- [ ] persistence authority;
- [ ] security classification;
- [ ] failure behaviour;
- [ ] retry/idempotency requirement;
- [ ] concurrency implications;
- [ ] audit implications;
- [ ] observability;
- [ ] test responsibility.

---

# 309. Component Specification Template

```text
Component:
CMP-...

Name:
...

Layer:
Presentation / Application / Domain / Infrastructure / Worker

Responsibility:
...

Inputs:
...

Outputs:
...

Reads:
...

Writes:
...

Allowed Dependencies:
...

Forbidden Dependencies:
...

Security:
...

Failure Behaviour:
...

Idempotency:
...

Audit:
...

Tests:
...
```

---

# 310. Runtime Component Matrix

| Component | Deployment | Criticality |
|---|---|---|
| Reverse Proxy | host/container | Critical |
| Next.js Web | process/container | Critical |
| PostgreSQL | service | Critical |
| Media Storage | persistent storage | Critical |
| Worker | process/container | High |
| Scheduler | worker/function | High |
| AI Provider | external | Optional |
| WhatsApp | external | Optional |
| Social Platforms | external | Optional |

---

# 311. Component Failure Matrix

| Failure | Behaviour |
|---|---|
| Public Projection failure | affected public page error/degraded |
| Worker down | queued durable jobs remain |
| PDF renderer down | generation fails/retries |
| QR generator down | required QR may block new output |
| AI provider down | manual workflow |
| Notification channel down | source business record retained |
| Search down | direct pages still work |
| Analytics down | no business interruption |
| Media derivative processor down | source preserved |
| Theme AI down | active Theme remains |
| Admin search down | direct navigation remains |

---

# 312. Component Data Authority Matrix

| Component | Master | Config | Operational | Derived |
|---|---:|---:|---:|---:|
| Profile Service | ✓ |  |  |  |
| Portfolio Service | ✓ | ✓ |  |  |
| Questionnaire Service |  | ✓ |  | revision |
| Builder Service |  | temporary |  | snapshot |
| Feedback Service |  |  | ✓ |  |
| Casting Service |  |  | ✓ |  |
| Opportunity Service |  |  | ✓ |  |
| PDF Renderer |  |  |  | ✓ |
| QR Generator |  |  |  | ✓ |
| Search |  |  |  | ✓ |
| AI | AI output only |  |  | recommendations |
| VOP |  |  | observations | analyses |
| Analytics |  |  | analytics events | aggregates |

---

# 313. Component Events Matrix

| Producer | Event | Consumer examples |
|---|---|---|
| Profile | ProfileChanged | projections, readiness, search |
| Portfolio | PrimaryPortfolioChanged | public, questionnaire readiness |
| Contact | ContactVisibilityChanged | public, builder, readiness |
| Link | ProfessionalLinkChanged | QR, health, previews |
| Questionnaire | QuestionnairePublished | PDF, cache |
| Feedback | FeedbackCreated | notification, dashboard |
| Casting | CastingCreated | dashboard |
| Opportunity | OpportunityStageChanged | analytics, dashboard |
| Theme | ThemeActivated | public cache |
| Worker | JobFailed | VOP/system status |

---

# 314. Public Component Security Rule

No Public component may receive raw Admin entity merely to choose client-side what to render.

---

# 315. Admin Component Security Rule

Admin component receives only authorized fields required for its operation.

---

# 316. Worker Security Rule

Job payload SHOULD carry references, not unnecessary full sensitive records.

Worker fetches authorized internal context according to job type.

---

# 317. AI Component Security Rule

No AI component receives database credentials or unrestricted repository interface.

---

# 318. Provider Adapter Security Rule

External provider adapters receive secrets through Secret Provider/configuration, not domain arguments from browser.

---

# 319. Component Versioning

External adapter contracts and persisted job payload schemas SHOULD be versionable where long-lived async jobs may span deployments.

---

# 320. Job Payload Version

A durable job SHOULD contain:

```text
job_type
payload_schema_version
target identifiers
idempotency key
```

---

# 321. Deployment Compatibility

A deployment MUST avoid leaving queued jobs unreadable after application upgrade.

Backward compatibility or migration required.

---

# 322. Component Removal

Removing component requires dependency audit.

Example removing old Project `Other` path requires:

```text
Training migration complete
no UI references
no API writes
no queued jobs
```

---

# 323. Component Introduction

New component must not duplicate existing authority.

Example:

Adding `CastingQuickViewService` is valid read model.

Adding `CastingQuickViewProfileTable` as writable Profile clone is not.

---

# 324. Component Granularity Principle

A component should be introduced when it has:

```text
distinct responsibility
distinct contract
distinct failure behaviour
or clear domain boundary
```

Not every function becomes component.

---

# 325. No Class-per-Entity Requirement

Architecture does not mandate Java-style service/repository class explosion.

Implementation may use TypeScript modules/functions.

The **boundary and responsibility** are normative, not class syntax.

---

# 326. Suggested TypeScript Module Shape

Conceptual only:

```text
modules/questionnaires/
  domain/
  application/
  data/
  projections/
  validation/
  tests/
```

---

# 327. Infrastructure Shape

```text
infrastructure/
  db/
  storage/
  jobs/
  pdf/
  qr/
  ai/
  notifications/
  social/
  observability/
```

---

# 328. Public Shape

```text
app/
  public routes
  admin routes
```

Exact Next.js structure deferred to implementation docs.

---

# 329. Component Acceptance Criteria

System complies with DOC-071 when:

1. Web Runtime does not own business logic;
2. Worker is logically separated from request handling;
3. Public and Admin consume application/projection interfaces;
4. every Master mutation routes through owning application service;
5. Builder has a separate eligibility projection;
6. Dashboard remains read model/action surface;
7. Media Library and Portfolio are separate components;
8. Emotional Grid uses dedicated editor/renderer;
9. Questionnaire renderer is revision-bound;
10. PDF renderer consumes immutable document model;
11. QR generator receives canonical validated URL;
12. Search remains derived;
13. background jobs are durable;
14. outbox/reliable dispatch exists for critical side effects;
15. AI capabilities remain separated;
16. AI provider adapter has no direct domain authority;
17. Notification delivery cannot mutate source business truth;
18. Opportunity transitions have one authoritative service;
19. VOP uses application commands instead of arbitrary DB updates;
20. Theme subsystem cannot affect authorization/data ownership;
21. private/public media delivery paths are distinct;
22. repositories reflect domain boundaries;
23. public/private cache scopes cannot collide;
24. external provider failures remain isolated;
25. component dependencies follow inward architecture direction.

---

# 330. E2E-CMP-001 — Public Profile

```text
Public Profile Page
→ Public Projection Service
→ Profile/Portfolio read services
→ safe DTO
```

No raw private Contact.

---

# 331. E2E-CMP-002 — Admin Primary Photo

```text
Portfolio Workspace
→ PortfolioApplicationService
→ Domain uniqueness
→ Repository transaction
→ event/cache invalidation
```

---

# 332. E2E-CMP-003 — Builder Contact Attack

Client submits hidden Contact ID.

BuilderApplicationService:

```text
→ Builder Eligibility Projection
→ server denial
```

No Contact returned/generated.

---

# 333. E2E-CMP-004 — Feedback

```text
Contact UI
→ Feedback Service
→ Repository commit/outbox
→ Notification Worker
```

Provider failure isolated.

---

# 334. E2E-CMP-005 — PDF

Worker receives exact revision ID.

PDF component does not query current Profile.

---

# 335. E2E-CMP-006 — AI

Casting AI receives Source Snapshot.

No direct repository method:

```text
updateActorSkill()
```

exists inside AI provider adapter.

---

# 336. E2E-CMP-007 — VOP

Broken link detector creates Observation.

It does not delete Link.

---

# 337. E2E-CMP-008 — Opportunity

Kanban and detail page call identical transition component.

---

# 338. E2E-CMP-009 — Theme

Theme AI generates proposal.

ThemeApplicationService validates and requires explicit Publish.

---

# 339. E2E-CMP-010 — Worker Restart

Durable job remains claimable after process restart.

---

# 340. Final Component Topology

```text
┌──────────────────────────────────────────────────────────────┐
│                     PRESENTATION LAYER                       │
│                                                              │
│ Public UI                                 Admin UI            │
└───────────────────────┬──────────────────────┬───────────────┘
                        │                      │
                        └──────────┬───────────┘
                                   ▼
┌──────────────────────────────────────────────────────────────┐
│                     APPLICATION LAYER                        │
│                                                              │
│ Profile / Media / Portfolio / Emotional / Projects           │
│ Contacts / Questionnaire / Builder / Feedback / Casting      │
│ Opportunity / AI / Themes / Notifications / VOP              │
│ Projections / Readiness / Dependency Impact                  │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│                        DOMAIN LAYER                           │
│                                                              │
│ Entities · State Machines · Ownership · Revision Rules       │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│                    INFRASTRUCTURE LAYER                       │
│                                                              │
│ PostgreSQL · Repositories · Media Storage · Jobs · PDF        │
│ QR · Search · Cache · AI Provider · Notifications · Social    │
│ Logging · Health · Secrets                                   │
└──────────────────────────────────────────────────────────────┘
```

---

# 341. Финальный принцип

> **Компонентная архитектура должна позволять разработчику однозначно ответить на три вопроса: кто владеет данными, кто имеет право их изменить и через какой контракт выполняется действие. Public и Admin интерфейсы инициируют use cases, Application Layer оркестрирует их, Domain Layer определяет смысл и ограничения, а Infrastructure Layer предоставляет PostgreSQL, storage, workers, PDF, QR, AI и внешние каналы. Ни один инфраструктурный или UI-компонент не получает самостоятельного права обходить эти границы.**