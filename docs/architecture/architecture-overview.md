# ARCHITECTURE OVERVIEW

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная целевая архитектура платформы профессионального сайта актрисы

**Целевой файл:** `docs/architecture/architecture-overview.md`  
**Документ:** DOC-070  
**Статус:** ✅ Completed  
**Тип:** Architecture / System Overview / Target Architecture

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/overview.md`
- `docs/customer-journey/conversion-funnel.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/ux-principles.md`
- `docs/ux/public-navigation.md`
- `docs/ux/admin-navigation.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/public-questionnaire-builder.md`
- `docs/ux/forms-and-validation.md`
- `docs/ux/ui-states.md`
- `docs/ux/content-hierarchy.md`
- `docs/ux/responsive-behaviour.md`
- `docs/ux/accessibility.md`
- `docs/domain/domain-model.md`
- `docs/domain/relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions.md`

---

# 1. Назначение документа

Настоящий документ определяет **целевую системную архитектуру TO-BE** продукта.

Он фиксирует:

1. общий architecture style;
2. основные runtime-компоненты;
3. границы Public и Admin;
4. backend/application layering;
5. Domain boundaries;
6. PostgreSQL role;
7. media/file storage;
8. Public Data Aggregation Layer;
9. background processing;
10. PDF generation;
11. QR subsystem;
12. Search;
13. caching;
14. Notifications;
15. AI integrations;
16. Virtual Operator;
17. social integrations;
18. external communication channels;
19. audit/analytics boundaries;
20. security boundaries;
21. deployment topology;
22. failure isolation;
23. scaling approach;
24. architectural invariants;
25. дальнейшую декомпозицию документации.

Документ **не определяет** окончательную физическую схему БД, API contracts или deployment scripts. Они будут определены далее.

---

# 2. Архитектурная цель

Система должна обеспечивать одновременно:

```text
PUBLIC PROFESSIONAL EXPERIENCE
+
ADMIN PROFESSIONAL OPERATIONS
+
DOCUMENT GENERATION
+
CASTING WORKFLOW
+
CONTROLLED AI ASSISTANCE
+
RELIABLE DATA HISTORY
```

при сохранении:

```text
ONE SOURCE OF TRUTH
PRIVACY BY PROJECTION
HUMAN AUTHORITY
REVISION HISTORY
FAILURE ISOLATION
```

---

# 3. Базовый architecture style

Для текущего масштаба продукта канонической архитектурой является:

> **Modular Monolith + Background Worker Architecture**

Основной application runtime:

```text
Next.js
TypeScript
PostgreSQL
Media/File Storage
Background Jobs
```

---

# 4. Почему Modular Monolith

Продукт:

- обслуживает один основной Actor Profile;
- имеет тесно связанные профессиональные domains;
- требует сильной transactional consistency;
- не имеет масштаба marketplace;
- не требует независимого масштабирования десятков bounded services;
- должен оставаться поддерживаемым одним небольшим development/operations контуром.

Поэтому микросервисная декомпозиция на текущем этапе создаёт больше operational complexity, чем business value.

---

# 5. ARCH-INV-001 — No Premature Microservices

Нельзя разделять систему на отдельные сетевые сервисы только потому, что существуют отдельные Domain contexts.

Domain modularity MUST существовать внутри codebase независимо от deployment topology.

---

# 6. Возможность дальнейшего выделения

Архитектура SHOULD позволять в будущем независимо вынести:

```text
Media Processing
PDF Rendering
AI Processing
Notifications
Search
Analytics
```

если фактическая нагрузка/надежность это потребует.

Но это эволюционный путь, не начальная обязательная topology.

---

# 7. Technology Baseline

Текущий и целевой baseline:

```text
Frontend / Web Runtime:
Next.js

Language:
TypeScript

Primary Database:
PostgreSQL

Media:
local/server-managed file storage with abstraction

Reverse Proxy / TLS:
Nginx or equivalent

Deployment:
self-hosted server/container-compatible runtime
```

---

# 8. Existing Deployment Compatibility

Архитектура MUST оставаться совместимой с существующей operational моделью:

```text
application root:
 /data/kate-actor

public application port:
 3336

reverse proxy:
 Nginx / HTTPS
```

Конкретные параметры deployment могут изменяться через Operations docs, но переход не должен требовать неоправданной инфраструктурной перестройки.

---

# 9. Supabase

Supabase НЕ является обязательным компонентом архитектуры.

Текущая архитектура использует PostgreSQL напрямую.

Добавление Supabase без отдельного архитектурного решения запрещено как скрытая platform dependency.

---

# 10. High-Level Architecture

```text
                    INTERNET / PROFESSIONAL USER
                              │
                              ▼
                    HTTPS / REVERSE PROXY
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      NEXT.JS APPLICATION                     │
│                                                             │
│   ┌─────────────────┐              ┌────────────────────┐   │
│   │   PUBLIC WEB    │              │    ADMIN WEB       │   │
│   │                 │              │                    │   │
│   │ Profile         │              │ Dashboard          │   │
│   │ Portfolio       │              │ Content            │   │
│   │ Emotional       │              │ Questionnaires     │   │
│   │ Projects        │              │ Castings           │   │
│   │ Questionnaire   │              │ Opportunities      │   │
│   │ Builder         │              │ AI Assistance      │   │
│   │ Contact         │              │ Themes             │   │
│   └────────┬────────┘              └──────────┬─────────┘   │
│            │                                  │              │
│            └──────────────┬───────────────────┘              │
│                           ▼                                  │
│               APPLICATION / DOMAIN LAYER                    │
│                           │                                  │
│    ┌──────────────────────┼──────────────────────────────┐   │
│    ▼                      ▼                              ▼   │
│ Profile/Content      Casting/Opportunity        Documents/AI │
│ Domains              Domains                    Services     │
│    │                      │                              │   │
└────┼──────────────────────┼──────────────────────────────┼───┘
     │                      │                              │
     ▼                      ▼                              ▼
 PostgreSQL            Background Jobs            External Providers
     │                      │                      AI / WhatsApp /
     │                      │                      Social Platforms
     ▼                      ▼
 Media Metadata       Worker Processing
     │                      │
     └──────────┬───────────┘
                ▼
          MEDIA STORAGE
```

---

# 11. Architecture Layers

Канонические logical layers:

```text
Presentation
Application
Domain
Infrastructure
```

---

# 12. Presentation Layer

Содержит:

```text
Public Pages
Admin Pages
UI Components
Forms
Public Builder UI
Admin Workspaces
```

Presentation Layer MUST NOT владеть business truth.

---

# 13. Application Layer

Оркестрирует use cases.

Examples:

```text
Publish Questionnaire
Create Casting From Feedback
Generate Builder PDF
Set Primary Portfolio Item
Apply AI Draft
Book Opportunity
Publish Theme
```

Application Layer:

- вызывает Domain rules;
- управляет transaction boundary;
- вызывает infrastructure adapters;
- организует side effects.

---

# 14. Domain Layer

Содержит:

```text
entities
value objects
domain rules
state transitions
ownership constraints
readiness logic
revision rules
```

Domain Layer не должен зависеть от HTTP/UI implementation.

---

# 15. Infrastructure Layer

Содержит adapters к:

```text
PostgreSQL
Filesystem/Object Storage
PDF Renderer
Image Processing
QR Generator
AI Provider
WhatsApp
Instagram
TikTok
Email
Search
Logging
Background Jobs
```

---

# 16. Dependency Direction

Canonical:

```text
Presentation
    ↓
Application
    ↓
Domain

Infrastructure
    ↑
Application interfaces
```

Domain MUST NOT импортировать:

```text
React
Next.js page components
HTTP request objects
provider-specific SDK concepts
```

---

# 17. Domain Module Structure

Codebase SHOULD отражать bounded contexts из DOC-060.

Conceptually:

```text
modules/
  profile/
  media/
  portfolio/
  emotional/
  projects/
  training/
  skills-languages/
  professional-links/
  contacts/
  questionnaires/
  public-builder/
  qr/
  feedback/
  castings/
  opportunities/
  notifications/
  bb-assistant/
  virtual-operator/
  themes/
  analytics/
  social/
  help/
  audit/
```

Physical directory naming will be finalized during implementation planning.

---

# 18. Module Encapsulation

Каждый module SHOULD иметь собственные:

```text
domain
application
repository/data access
validation
public/admin projections
tests
```

где это оправдано.

---

# 19. Cross-Module Access Rule

Один module MUST NOT напрямую изменять чужие tables/entities в обход owning Domain service.

Например:

```text
Questionnaire module
MUST NOT
UPDATE contacts
```

---

# 20. Cross-Module Read

Read-side aggregation MAY получать данные из нескольких domains через:

- repositories;
- read services;
- projections;
- aggregation layer.

Это допустимо.

---

# 21. Cross-Module Mutation

Mutation SHOULD идти через owning application/domain operation.

Example:

```text
Create Casting From Feedback
```

может быть application orchestration, но:

```text
Feedback ownership
Casting ownership
```

остаются раздельными.

---

# 22. Public Runtime Boundary

Public application surface включает:

```text
Profile
Portfolio
Emotional Portfolio
Projects
Questionnaires
Public Builder
Professional Contact / Feedback
Public Media
Public Search
localized routes
```

Public runtime MUST работать только с Public Projections.

---

# 23. Admin Runtime Boundary

Admin surface включает privileged operations:

```text
content CRUD
publication
visibility
casting analysis
pipeline
AI assistance
themes
system health
audit
```

Admin endpoints MUST требовать server-side authentication/authorization.

---

# 24. `/admin` Boundary

All Admin routes live conceptually under:

```text
/admin
```

но безопасность определяется не URL prefix, а server-side authorization.

---

# 25. Public/Admin Code Sharing

Public и Admin MAY использовать общие:

```text
design primitives
formatters
domain types
read utilities
```

Но private Admin data MUST NOT попадать в Public bundles/payloads из-за shared implementation.

---

# 26. Public Data Aggregation Layer

Ключевой architectural component:

```text
PUBLIC DATA AGGREGATION LAYER
```

Он отвечает за:

```text
authorization/visibility filtering
lifecycle filtering
locale resolution
projection composition
safe DTO generation
```

---

# 27. Public Projection Flow

```text
MASTER DATA
     ↓
Lifecycle Rules
     ↓
Visibility Rules
     ↓
Locale Resolution
     ↓
Public Data Aggregation Layer
     ↓
SAFE PUBLIC DTO
     ↓
Public Page
```

---

# 28. ARCH-INV-002 — Privacy Before Client

Hidden/private data MUST be removed **до передачи клиенту**.

Prohibited:

```text
SELECT all contacts
→ send browser
→ CSS hide private ones
```

---

# 29. Public Builder Projection

Public Builder MUST иметь отдельную server-authoritative projection.

Flow:

```text
MASTER DATA
     ↓
Builder Eligibility Rules
     ↓
Template Constraints
     ↓
Public Builder Projection
     ↓
Browser
```

---

# 30. Builder Projection ≠ Public Site Projection

Critical rule:

```text
show_on_public_site
!=
allow_in_public_questionnaire_builder
```

Поэтому нельзя использовать обычный Public Profile DTO как единственный Builder data source.

---

# 31. Prepared Questionnaire Projection

Prepared Questionnaire использует:

```text
QuestionnaireDefinition
+
current Draft source refs
```

для Admin preview,

или:

```text
QuestionnaireRevision
```

для published historical output.

---

# 32. Casting Quick View Projection

Casting Quick View является read model.

Inputs:

```text
ActorProfile
Primary Portfolio
Languages
Skills
Video
Questionnaire availability
```

No writable duplicates.

---

# 33. Search Projection

Public Search index MUST строиться только из public-eligible/search-eligible projection.

Private Admin data MUST NOT индексироваться в public search.

---

# 34. PostgreSQL Role

PostgreSQL является canonical persistent store для:

```text
Master Data
Configurations
Operational entities
Revisions
Snapshots metadata
Audit
Job metadata where applicable
Analytics metadata where retained
```

---

# 35. Binary Files Outside PostgreSQL

Large media binaries SHOULD NOT храниться как DB BLOBs по умолчанию.

PostgreSQL хранит:

```text
logical identity
metadata
storage location
checksum
relationships
processing status
```

Binary хранится в media storage.

---

# 36. Media Storage Abstraction

Architecture MUST использовать abstraction:

```text
MediaStorage
```

а не hardcode local filesystem assumptions по всему application code.

---

# 37. Initial Storage Backend

Текущий self-hosted deployment MAY использовать локальное persistent storage:

```text
/data/kate-actor/...
```

или выделенный media directory.

---

# 38. Future Storage Backends

Storage abstraction SHOULD позволять:

```text
local filesystem
S3-compatible storage
object storage
CDN-backed storage
```

без изменения Domain Model.

---

# 39. Storage Locator ≠ Public URL

Critical:

```text
internal storage path
!=
public media URL
```

---

# 40. Media Delivery Layer

Public media MUST получать безопасный stable URL через application/media delivery layer.

---

# 41. Stable Public Media URL

Published local media used in:

- website;
- Questionnaire;
- QR;

MUST иметь stable public/casting-safe route.

---

# 42. No QR File Duplication

Создание QR не должно создавать новую копию media binary.

QR targets stable URL.

---

# 43. Original vs Derivatives

Media architecture:

```text
ORIGINAL
  │
  ├─ thumbnail
  ├─ responsive web
  ├─ PDF
  ├─ poster
  └─ other approved derivative
```

---

# 44. Original Immutability

Original MUST NOT overwrite during crop/resize.

---

# 45. Derivative Identity

Derivative SHOULD быть identifiable by:

```text
source identity
transformation type
transformation parameters/version
```

---

# 46. Media Processing Architecture

Media processing SHOULD execute asynchronously when operation may be expensive.

Examples:

```text
image derivatives
video metadata
poster generation
large batch processing
```

---

# 47. Emotional Grid Rendering

Emotional Grid uses:

```text
immutable source MediaAsset
+
Grid configuration
+
cell crop config
```

to produce derived composite.

---

# 48. Emotional Grid Generation Pipeline

```text
Grid Draft
   ↓
Validate exact dimensions/cells
   ↓
Human cell confirmation
   ↓
Freeze Grid Revision
   ↓
Background/processing job
   ↓
Master Composite
   ├─ Web
   ├─ PDF
   └─ Thumbnail
```

---

# 49. No Generative Appearance Processing

Image processing pipeline MUST NOT include generative alteration of actress appearance.

---

# 50. Background Processing Requirement

Long-running or retryable work MUST NOT блокировать normal HTTP request unnecessarily.

---

# 51. Background Job Categories

Minimum architecture supports jobs for:

```text
media processing
PDF generation
QR generation/validation
link health checks
search reindex
notification delivery
social publishing
AI generation/analysis
theme preview/build if expensive
cache rebuild
cleanup/retention
```

---

# 52. Background Job Architecture

Logical:

```text
Application Command
      ↓
Persist authoritative business state
      ↓
Create Job / Outbox Event
      ↓
Worker
      ↓
Execute
      ↓
Persist result
      ↓
Invalidate/update projection
```

---

# 53. Job Technology

Specific queue implementation is NOT fixed in DOC-070.

Possible implementation:

```text
PostgreSQL-backed job queue
Redis-backed queue
dedicated worker queue
```

must be selected later based on operational simplicity.

---

# 54. Canonical Initial Preference

Для текущего deployment scale SHOULD prefer minimal operational dependencies.

A PostgreSQL-backed durable job mechanism is architecturally preferable to introducing Redis solely for queueing unless measured need justifies Redis.

---

# 55. ARCH-INV-003 — Durable Jobs

Critical background work MUST NOT exist only in process memory.

Application restart must not silently lose:

```text
notification
PDF generation
social publish
important media processing
```

that has already been accepted as durable work.

---

# 56. Job Idempotency

Every retryable side-effecting job MUST have idempotency strategy.

---

# 57. Job Status

Canonical processing model:

```text
QUEUED
RUNNING
SUCCEEDED
FAILED
RETRY_SCHEDULED
CANCELLED where appropriate
```

---

# 58. Job Ownership

Job is technical execution.

Business state remains in owning domain.

---

# 59. Outbox Pattern

For important domain event → async side effect consistency, architecture SHOULD use transactional outbox or equivalent reliable mechanism.

Example:

```text
Feedback saved
+
NotificationRequested event saved
```

within same transaction.

Worker later delivers.

---

# 60. Why Outbox

Prevents failure:

```text
Feedback committed
process crashes
notification task never created
```

---

# 61. Inbox/Idempotency Pattern

External callbacks/provider events SHOULD be processed idempotently where applicable.

---

# 62. PDF Architecture

PDF subsystem is a dedicated application/infrastructure capability.

Inputs MUST be immutable:

```text
QuestionnaireRevision
or
BuilderGenerationSnapshot
```

---

# 63. PDF Generator MUST NOT Read Mutable Live Profile

Background PDF worker receives exact snapshot/revision identity.

---

# 64. Prepared Questionnaire PDF

```text
QuestionnaireRevision
      ↓
PDF Render Model
      ↓
Renderer
      ↓
PDF Artifact
```

---

# 65. Public Builder PDF

```text
Builder Session
      ↓
Server Revalidation
      ↓
Generation Snapshot
      ↓
PDF Render Model
      ↓
Renderer
```

---

# 66. PDF Render Model

Renderer SHOULD consume a normalized document model independent from page/admin DB structure.

Conceptually:

```text
Document
Sections
Text
Images
Links
QR
Metadata
```

---

# 67. HTML/PDF Consistency

HTML and PDF SHOULD derive from same semantic document snapshot/model.

This reduces divergence.

---

# 68. PDF Accessibility

Renderer architecture MUST permit:

- selectable text;
- clickable links;
- meaningful structure where supported;
- document language;
- non-raster-only output.

---

# 69. QR Architecture

QR subsystem:

```text
Canonical URL Resolver
       ↓
QR Safety Validator
       ↓
QR Generator
       ↓
Decode Validator
       ↓
QR Artifact
```

---

# 70. Exact QR Equality

Required:

```text
decode(generated_qr)
===
canonical_url
```

---

# 71. QR Safety

QR subsystem MUST reject:

```text
/admin URLs
private media URLs
auth preview URLs
filesystem paths
unsafe schemes
unstable internal hostnames
```

---

# 72. QR Cache

QR MAY be cached by:

```text
canonical_url
+
settings/version
```

---

# 73. QR Invalidation

Canonical URL change:

```text
dependent QR → stale
→ regenerate
```

Historical QuestionnaireRevision artifacts remain unchanged.

---

# 74. Search Architecture

Search has two distinct use cases:

```text
Public Professional Search
Admin Global Search
```

---

# 75. Public Search

Indexes only:

```text
published/eligible profile facts
skills
languages
projects
roles
training
other approved public professional data
```

---

# 76. Admin Search

May include privileged entities:

```text
Media
Contacts
Questionnaires
Castings
Feedback
Opportunities
```

according to authorization.

---

# 77. Search Implementation

Initial scale MAY use PostgreSQL search capabilities.

Dedicated engine MUST NOT be introduced without need.

---

# 78. Search Index as Derived Data

Search indexes are rebuildable.

---

# 79. Search Failure Isolation

Public Profile/Portfolio/Questionnaire MUST continue working if search is unavailable.

---

# 80. Caching Architecture

Cache is optional acceleration, never authority.

---

# 81. Cacheable Data

Potential:

```text
public profile projection
project lists
portfolio lists
published questionnaire HTML
QR artifacts
media derivatives
search results
```

---

# 82. Cache Invalidation

Must be domain-event/source-aware.

Examples:

```text
ProjectPublished
ContactVisibilityChanged
PrimaryPortfolioChanged
QuestionnairePublished
ThemeActivated
```

---

# 83. Security Before Cache

Cache key/scope MUST encode relevant:

```text
public/private context
locale
revision
access token context where applicable
```

---

# 84. ARCH-INV-004 — No Shared Private/Public Cache Leak

Private casting Questionnaire response MUST NOT be served from generic public cache key.

---

# 85. Published Revision Caching

Immutable revision-specific resources are excellent long-lived cache candidates.

---

# 86. Current Alias Caching

Routes pointing to “current” content need explicit invalidation when current pointer changes.

---

# 87. AI Architecture Overview

AI is implemented as **four separate application capabilities**:

```text
Casting AI
BB Assistant
Theme AI
Virtual Operator AI assistance
```

---

# 88. No Generic AI God Service

Infrastructure provider adapter MAY be shared.

Business logic MUST remain capability-specific.

---

# 89. AI Provider Abstraction

Application SHOULD use provider-neutral interface where practical:

```text
AIProvider
```

to avoid business domain dependency on one model/provider.

---

# 90. AI Request Pipeline

Canonical:

```text
Authorized User Action / Allowed Trigger
      ↓
Context Builder
      ↓
Privacy Filter
      ↓
Source Snapshot
      ↓
Prompt/Task
      ↓
AI Provider
      ↓
Structured Result Validation
      ↓
Persist AI Output
      ↓
Human Review
```

---

# 91. AI Context Minimization

Each AI capability receives only context required for task.

---

# 92. Casting AI Context

May include:

```text
CastingSource
confirmed Profile facts
selected professional evidence
```

not unrelated private data.

---

# 93. BB Assistant Context

Only facts needed for requested writing task.

---

# 94. Theme AI Context

Receives:

```text
theme/current design structure
allowed visual tokens
prompt
accessibility constraints
```

not private Castings/Contacts.

---

# 95. Virtual Operator Context

Uses structured system observations, not unrestricted all-data prompt by default.

---

# 96. AI Output Validation

Structured AI output MUST be schema-validated before persistence/use.

---

# 97. AI Failure Isolation

If AI provider fails:

```text
Profile still editable
Casting still usable
Theme current version stays active
BB manual editor works
```

---

# 98. ARCH-INV-005 — AI Is Optional for Core Operation

No core professional data CRUD must depend on AI availability.

---

# 99. AI Async Execution

Potentially slow AI requests SHOULD use job/background execution where UX benefits.

Short interactive drafts MAY be synchronous/streaming if timeout/failure handling is robust.

---

# 100. AI Revision Storage

Meaningful persistent outputs use revision/provenance semantics defined DOC-064.

---

# 101. Notification Architecture

Notification system comprises:

```text
Notification
NotificationDelivery
Channel Adapter
Retry Scheduler
Quiet Hours Policy
```

---

# 102. Channels

Initial/target channels MAY include:

```text
In-App
WhatsApp
Email
```

plus future approved channels.

---

# 103. WhatsApp Architecture

WhatsApp integration MUST use official/approved business channel mechanism.

Architecture MUST NOT depend on unsupported browser automation or personal-account scraping.

---

# 104. Minimal WhatsApp Notification

Default notification SHOULD contain minimal necessary professional alert.

Sensitive details remain inside authenticated Admin.

---

# 105. Protected Admin Link

WhatsApp notification MAY include protected Admin route.

It MUST NOT encode private data into URL.

---

# 106. Quiet Hours

Quiet hours are evaluated before external delivery.

Business record is persisted immediately regardless.

---

# 107. Notification Retry

Transient delivery failures use retry policy + idempotency.

---

# 108. Notification Failure Isolation

Canonical:

```text
Feedback persisted
Notification created
WhatsApp failed
```

Result:

```text
Feedback remains valid
Admin UI can show notification failure
```

---

# 109. Professional Feedback Architecture

Public form flow:

```text
Public Form
   ↓
Server Validation
   ↓
Anti-Abuse
   ↓
Attachment Security
   ↓
Feedback Transaction
   ↓
Commit
   ↓
Notification Event
   ↓
Success Response
```

---

# 110. Feedback Success Boundary

Visitor success means:

```text
Feedback committed
```

not:

```text
WhatsApp delivered
```

---

# 111. Attachment Architecture

Feedback attachments remain private.

They MUST NOT use public Media routes by default.

---

# 112. Attachment Safety

Pipeline supports:

```text
MIME validation
extension/content verification
size limits
safe filename
private storage
security scanning where implemented
```

---

# 113. Castings Architecture

Casting workspace orchestrates:

```text
Casting
CastingSource
CastingAnalysis
Questionnaire Draft
Opportunity relation
```

but preserves Domain boundaries.

---

# 114. Casting Creation From Feedback

Application use case:

```text
Read Feedback
     ↓
Create Casting
     ↓
Create provenance link
     ↓
Copy permitted source context
     ↓
Commit
     ↓
Audit
```

Original Feedback remains intact.

---

# 115. Casting AI Architecture

```text
Casting Source
      ↓
Snapshot
      ↓
AI Extraction
      ↓
Schema Validation
      ↓
Analysis Revision
      ↓
Human Review
      ↓
Confirmed Requirements
      ↓
Profile Match
      ↓
Recommendations
```

---

# 116. Profile Match

Profile Match SHOULD be deterministic wherever possible after human-confirmed requirement normalization.

AI MAY assist interpretation, but matching confirmed structured values should not rely unnecessarily on generative output.

---

# 117. Opportunity Architecture

Opportunity transition occurs through one authoritative application service.

Kanban/list/dashboard all call the same transition rules.

---

# 118. ARCH-INV-006 — One Opportunity Transition Path

No UI-specific state mutation path.

---

# 119. Revision Architecture

Revision services must enforce:

```text
immutable historical data
atomic revision numbers
current pointer
snapshot construction
artifact binding
rollback semantics
```

---

# 120. Transaction Boundaries

Critical operations SHOULD use database transaction.

Examples:

```text
Set Primary Questionnaire
Set Primary Portfolio Item
Publish Questionnaire
Create Casting From Feedback
Transition Opportunity
Change Contact visibility + audit/outbox
```

---

# 121. External Side Effects Outside Core Transaction

External calls generally SHOULD NOT occur inside DB transaction if they can block unpredictably.

Instead:

```text
DB commit
→ durable job/outbox
→ external call
```

---

# 122. Exception

If external system is mandatory to establish authoritative transaction, architecture must explicitly document distributed consistency handling.

Current product should avoid such dependency where possible.

---

# 123. Domain Events

Internal domain/application events SHOULD represent committed meaningful changes.

Examples:

```text
FeedbackCreated
QuestionnairePublished
ContactVisibilityChanged
OpportunityStageChanged
ThemePublished
```

---

# 124. Event Consumer Examples

```text
QuestionnairePublished
→ PDF job
→ cache invalidation
→ analytics
```

---

# 125. Event Delivery

Critical internal events need durable semantics where missing them would leave inconsistent system state.

---

# 126. Eventual Consistency

Acceptable for:

```text
search reindex
analytics
notification delivery
cache refresh
secondary derivative
```

---

# 127. Strong Consistency

Required for:

```text
business entity transition
visibility permission
primary uniqueness
revision publication pointer
Profile facts
Questionnaire snapshot
```

---

# 128. Security Architecture Overview

Security follows:

```text
AUTHENTICATE
→ AUTHORIZE
→ VALIDATE
→ MUTATE/READ
```

---

# 129. Public Access

Public routes require no user account.

But Public Builder/Feedback endpoints remain protected by:

```text
server validation
rate limiting
anti-abuse
data eligibility
opaque session identifiers
```

---

# 130. Admin Access

Admin data/actions require authenticated authorized session.

---

# 131. Session Security

Session identity MUST be server-verifiable.

Specific authentication implementation documented later.

---

# 132. Builder Session Security

Builder session token/id:

```text
!= authorization to arbitrary profile data
```

---

# 133. Casting-Specific Links

Unlisted/private casting package links MUST use:

```text
opaque token
server-side scope
expiry/revocation where required
```

---

# 134. No Security by Obscurity

Sequential DB ID, hidden menu, unknown URL do not constitute access control.

---

# 135. Input Validation Boundary

All external data is untrusted:

```text
public form
builder configuration
admin form
URL
files
external provider webhook
AI output
```

---

# 136. AI Output Is Untrusted Input

AI-generated structured output MUST undergo validation exactly like other external inputs.

---

# 137. Output Encoding

User/AI supplied text must be safely rendered.

No arbitrary HTML execution unless explicitly supported through sanitized content model.

---

# 138. URL Security

URL service should centralize:

```text
scheme allowlist
canonicalization
public/private route classification
QR eligibility
```

---

# 139. SSR / Server Components

Next.js server-side capabilities SHOULD be used where they improve:

```text
privacy
SEO
initial performance
server-authoritative projection
```

---

# 140. Client Components

Used for interactive functionality:

```text
Builder
media viewer
Admin forms
Grid editor
dynamic filters
```

---

# 141. Server vs Client Principle

Do not move authoritative validation or privacy logic client-side merely for interactive convenience.

---

# 142. Public SEO Architecture

Public professional pages SHOULD be server-renderable/indexable where appropriate.

Examples:

```text
Profile
Portfolio
Emotional
Projects
Questionnaire landing
```

---

# 143. Private/Temporary SEO

Must not be indexed:

```text
Admin
private casting links
Builder session URLs
auth previews
private media
```

---

# 144. Localization Architecture

Locale is explicit request/projection dimension.

Conceptually:

```text
route/request
→ locale resolution
→ localized projection
```

---

# 145. Locale Persistence

Must preserve across:

```text
navigation
Questionnaire
Builder
PDF generation
```

---

# 146. Translation Source

Approved localized content comes from Domain data.

Runtime SHOULD NOT silently invoke AI translation for missing public content.

---

# 147. Theme Architecture

Theme system is presentation configuration on top of stable core components.

```text
ThemeRevision
      ↓
Validated Design Tokens / Approved Overrides
      ↓
Public UI
```

---

# 148. Theme Boundary

Theme MUST NOT alter:

```text
data visibility
business rules
navigation availability of critical features
professional facts
authorization
```

---

# 149. Theme Validation

Before activation:

```text
schema validation
accessibility checks
responsive preview
critical component compatibility
```

---

# 150. Theme Activation

Activation references exact published ThemeRevision.

---

# 151. Virtual Operator Architecture

Virtual Operator is internal operational intelligence layer.

Inputs:

```text
domain events
job failures
readiness checks
link checks
system health
analytics summaries
```

Outputs:

```text
Observation
Recommendation
Safe AutomationExecution
```

---

# 152. VOP Is Not Public Chatbot

No Public runtime dependency on VOP.

---

# 153. VOP Action Model

```text
OBSERVE
→ EVALUATE
→ RECOMMEND
→ AUTO-4 SAFE ACTION
   or
→ HUMAN DECISION
```

---

# 154. VOP Automation Engine

Should invoke existing Domain/Application commands rather than writing DB directly.

---

# 155. ARCH-INV-007 — No Automation Backdoor

Virtual Operator/background jobs cannot bypass domain validations/authorization class simply because they run server-side.

---

# 156. BB Assistant Architecture

BB Assistant operates as contextual drafting service.

```text
Task
+
Allowed Source Context
      ↓
Source Snapshot
      ↓
AI Generation
      ↓
AIDraft
      ↓
Human Edit
      ↓
Apply
```

---

# 157. Apply Boundary

Apply writes into target **Draft**, not Published target.

---

# 158. Social Publishing Architecture

```text
SocialPost Draft
      ↓
Human Approval / Schedule
      ↓
Durable Publish Job
      ↓
Platform Adapter
      ↓
PublishAttempt
```

---

# 159. OAuth Connections

Platform credentials must be isolated from public application data and stored securely.

---

# 160. Multi-Platform Failure

Each platform publish attempt independent.

One failure must not incorrectly mark other successful publication failed.

---

# 161. Analytics Architecture

Analytics comprises:

```text
Client/Public Events
Server Business Events
Attribution
Aggregations
Admin Reporting
```

---

# 162. Server Business Events Preferred for Conversions

Critical funnel points derive from authoritative server events.

Examples:

```text
FeedbackCreated
CastingCreated
OpportunityBooked
```

---

# 163. Client Analytics

Useful for:

```text
page view
video start
Builder interaction
section navigation
```

Non-authoritative.

---

# 164. Analytics Failure Isolation

Site business flow continues without analytics.

---

# 165. Analytics Privacy

Must not send:

```text
Feedback message
private Contact
Casting Source
private attachment content
```

as event payload.

---

# 166. Audit Architecture

Audit is durable append-oriented data for significant actions.

---

# 167. Audit Event Generation

Audit SHOULD occur inside or directly coupled with authoritative mutation transaction where practical.

---

# 168. Audit Consumers

Admin Audit UI reads Audit.

No domain mutation should depend on reconstructing current value from Audit.

---

# 169. Help/Tickets Architecture

Help Center is standard content.

Support Tickets are private operational workflow separate from public professional Feedback.

---

# 170. Observability Architecture

System SHOULD provide:

```text
structured application logs
job status
error correlation IDs
health endpoints
system status
audit
```

---

# 171. Logging

Logs SHOULD include:

```text
timestamp
severity
component/module
correlation/request ID
safe entity identifiers
error class
```

---

# 172. Sensitive Logging

Logs MUST NOT unnecessarily contain:

```text
passwords
OAuth tokens
private messages
full contact details
AI provider secrets
session tokens
```

---

# 173. Correlation IDs

Critical multi-step operation SHOULD carry correlation identity across:

```text
HTTP request
domain command
outbox event
worker job
provider call
audit/error log
```

---

# 174. Health Architecture

System SHOULD expose checks for:

```text
application
database
media storage
job worker
optional integrations
```

---

# 175. Health State Categories

```text
HEALTHY
DEGRADED
UNAVAILABLE
UNKNOWN
```

---

# 176. Optional Provider Failure

AI/Social/WhatsApp failure should create:

```text
DEGRADED
```

not necessarily entire application `UNAVAILABLE`.

---

# 177. Critical Infrastructure

Application cannot function normally without:

```text
PostgreSQL
required storage
core web runtime
```

Their outage may produce unavailable state.

---

# 178. Deployment Topology — Minimum

Canonical simple topology:

```text
Internet
   │
   ▼
Nginx / TLS
   │
   ▼
Next.js Web Runtime
   │
   ├──────── PostgreSQL
   │
   ├──────── Media Storage
   │
   └──────── Durable Job Queue
                  │
                  ▼
             Worker Runtime
                  │
          ┌───────┼────────┐
          ▼       ▼        ▼
         AI      PDF   Notifications
```

---

# 179. Web and Worker Separation

Web and worker MAY initially run:

- same host;
- same repository;
- same deployment bundle;

but SHOULD be logically separate processes.

---

# 180. Why Separate Worker

Prevents expensive:

```text
PDF
AI
image processing
social publish
```

from blocking request-serving process.

---

# 181. Single-Process Development Mode

Development MAY run worker inline/local for convenience.

Production architecture should maintain durable semantics.

---

# 182. Containers

Application MAY be containerized.

Architecture does not mandate Docker for logical design, but existing deployment compatibility makes container-friendly packaging desirable.

---

# 183. Database Backups

PostgreSQL must be backed up independently from media binaries.

---

# 184. Media Backups

Media storage backup required.

A database backup without original media is incomplete.

---

# 185. Consistent Recovery

Recovery process needs reconcile:

```text
DB
media
revision artifacts
```

Exact operations later.

---

# 186. No External SaaS as Sole Source of Truth

Instagram/TikTok/WhatsApp/AI providers cannot be sole storage for core product data.

---

# 187. Failure Domain Classification

Architecture distinguishes:

```text
CORE
DERIVED
OPTIONAL_EXTERNAL
```

---

# 188. CORE

Examples:

```text
PostgreSQL
web runtime
source media storage
```

Failure affects core service.

---

# 189. DERIVED

Examples:

```text
search index
thumbnail
QR cache
PDF cache
```

Can generally rebuild.

---

# 190. OPTIONAL_EXTERNAL

Examples:

```text
AI provider
WhatsApp
Instagram
TikTok
analytics provider if external
```

Failure should degrade only associated capability.

---

# 191. Graceful Degradation Matrix

| Failure | Required Result |
|---|---|
| AI unavailable | manual workflows remain |
| WhatsApp unavailable | Feedback still persists |
| Search unavailable | direct navigation works |
| Analytics unavailable | site/business flow works |
| QR cache lost | regenerate |
| Thumbnail lost | regenerate |
| Social OAuth expired | website unaffected |
| PDF worker temporary failure | configuration retained/retry |
| Public media derivative fails | original/source remains safe |
| Theme AI unavailable | current Theme remains active |

---

# 192. Performance Architecture Principles

Performance details later, but baseline:

```text
server-render P0 content
use media derivatives
lazy-load P2/P3 media
cache published projections
nonblocking analytics
background expensive work
```

---

# 193. P0 Critical Path

Public first useful render SHOULD NOT depend on:

```text
AI
analytics
social integrations
WhatsApp
background health checker
```

---

# 194. Public Critical Dependencies

Minimal:

```text
web runtime
database/read projection
critical media derivative/storage
```

---

# 195. Media CDN

Not mandatory initially.

Can be introduced later behind MediaStorage/Delivery abstraction.

---

# 196. Database Scaling

Current scale does not justify sharding.

Use:

```text
indexes
query optimization
connection management
read projections
```

before distributed DB complexity.

---

# 197. Horizontal Web Scaling

Architecture SHOULD remain stateless enough for multiple web instances in future.

Therefore critical session/business state should not exist only in process memory.

---

# 198. Public Builder Session

Must use durable/server-controlled storage, not solely one server's RAM.

---

# 199. Admin Session

Auth session architecture must support production topology without relying on one process memory unless deployment deliberately remains single-instance with documented limitation.

---

# 200. Worker Scaling

Job handlers SHOULD be safe for multiple workers through:

```text
claim/lock semantics
idempotency
atomic status transition
```

---

# 201. Concurrency

Database is authority for concurrent business mutations.

---

# 202. Optimistic Locking

Mutable professional entities SHOULD use version/ETag semantics from DOC-064.

---

# 203. Database Constraints

Application validation SHOULD be complemented by DB constraints for:

```text
foreign keys
unique primary marker scopes
required relationships
revision uniqueness
```

where practical.

---

# 204. No Business Invariant Only in UI

Critical constraints cannot exist solely in React form validation.

---

# 205. Validation Layers

Architecture follows:

```text
UI affordance
↓
client validation
↓
server/application validation
↓
domain rules
↓
database integrity
```

---

# 206. External Webhooks

If provider webhooks are introduced:

```text
authenticate/verify
normalize
idempotency
persist event
process asynchronously
```

---

# 207. Provider-Specific Isolation

Provider payloads should be normalized at adapter boundary.

Domain should not be polluted with provider-specific response structure.

---

# 208. API Surface Philosophy

The product may use:

```text
Next.js Server Actions
Route Handlers / API endpoints
```

depending use case.

Architecture requirement is semantic:

> all writes go through validated application commands.

---

# 209. Server Actions

Suitable for authenticated first-party Admin mutations where architecture chooses.

---

# 210. API Routes

Suitable for:

```text
public forms
Builder endpoints
external integrations/webhooks
download endpoints
public read APIs where necessary
```

---

# 211. API Contracts

Exact choice/function contracts belong DOC-100–103.

---

# 212. No Direct DB from Client

Browser MUST NOT connect directly to PostgreSQL or receive DB credentials.

---

# 213. Repository/Data Access Boundary

Database queries SHOULD be encapsulated behind module repositories/data-access functions.

---

# 214. Cross-Module Raw SQL

Avoid one module directly manipulating another module's table unless through dedicated read model or controlled migration/reporting layer.

---

# 215. Read Models

Complex Admin dashboard/public projections MAY use optimized read queries spanning modules.

These remain read-only.

---

# 216. Command/Query Separation

Architecture SHOULD conceptually distinguish:

```text
COMMAND
changes state

QUERY
reads projection
```

Full CQRS infrastructure is NOT required.

---

# 217. ARCH-INV-008 — No CQRS Overengineering

Use command/query discipline without separate databases/message buses unless scale justifies it.

---

# 218. Current/Public Read Optimization

Published immutable data can use aggressive caching/read models.

Mutable Admin data prioritizes correctness.

---

# 219. Public Route Architecture

Recommended stable route groups:

```text
/
 /portfolio
 /emotional
 /emotional/{slug}
 /projects
 /projects/{slug}
 /questionnaire
 /questionnaire/{slug}
 /questionnaire/build
 /questionnaire/casting/{token-or-slug}
 /contact
```

Exact routing docs later.

---

# 220. Admin Route Architecture

Recommended:

```text
/admin
/admin/profile
/admin/media
/admin/portfolio
/admin/emotional
/admin/projects
/admin/training
/admin/skills-languages
/admin/contacts
/admin/questionnaires
/admin/castings
/admin/inbox
/admin/opportunities
/admin/virtual-operator
/admin/bb-assistant
/admin/themes
/admin/analytics
/admin/social
/admin/help
/admin/audit
/admin/settings
```

---

# 221. Route Does Not Define Domain Ownership

`/admin/questionnaires/{id}/project-selection` does not give Questionnaire ownership of Project.

---

# 222. Architecture and Existing Features

The architecture MUST preserve:

```text
role-specific media
home "В образе"
typed media
local/URL/server media sources
video/audio playback
questionnaire variants
skills 5 levels
CEFR languages
help/tickets
Instagram/TikTok publishing
multilingual support
```

---

# 223. Architecture and New Mandatory Capabilities

Must add first-class support for:

```text
Emotional Grid
QR
Public Builder
Contacts visibility model
Training separation
Feedback Inbox
Casting AI
Opportunity Pipeline
Notifications
BB Assistant
Virtual Operator
Theme system
revision architecture
```

---

# 224. Migration Architecture

Legacy data must transition through explicit migrations.

Application startup MUST NOT contain permanent hidden auto-fixes for known schema/domain changes.

---

# 225. Migration Execution

Schema/data migrations SHOULD be:

```text
versioned
repeat-safe where practical
observable
backed up
tested
```

---

# 226. Legacy Compatibility Layer

Temporary compatibility adapters MAY exist during migration.

They MUST have:

```text
documented purpose
removal condition
target version
```

---

# 227. No Permanent Dual Truth

Migration MUST eventually eliminate situations like:

```text
Training current table
+
Project type Other also writable as Training
```

---

# 228. Architecture Decision Records

Significant future changes SHOULD receive ADR.

Examples:

```text
job queue technology
media storage backend
PDF renderer
auth provider
AI provider abstraction
search engine
```

---

# 229. ADR Does Not Replace Normative Docs

ADR records why a technical choice was made.

Module/architecture docs define required behaviour.

---

# 230. Architectural Quality Attributes

Core priorities:

```text
Correctness
Privacy
Professional Data Integrity
Reliability
Maintainability
Reproducibility
Accessibility
Performance
Security
Observability
Extensibility
```

---

# 231. Priority Order in Architectural Conflict

Use:

```text
Security / Privacy
→ Data Integrity
→ Business Correctness
→ Historical Reproducibility
→ Availability
→ Accessibility
→ Performance
→ Developer Convenience
```

---

# 232. Maintainability Principle

Prefer explicit boring architecture to clever implicit coupling.

---

# 233. Dependency Principle

New external dependency must justify:

```text
business value
operational cost
backup/recovery impact
security impact
exit strategy
```

---

# 234. No Vendor Lock-In by Accident

Provider-specific code belongs adapters.

---

# 235. AI Provider Exit Strategy

Persistent product value remains:

```text
AIDraft
CastingAnalysisRevision
ThemeProposal history
```

in internal schema, not only provider conversation IDs.

---

# 236. Social Provider Exit Strategy

Core SocialPost history remains internal even if connection revoked.

---

# 237. WhatsApp Exit Strategy

Feedback/Notification records remain even if WhatsApp provider changes.

---

# 238. Media Exit Strategy

Storage locator abstraction permits migration from local filesystem to object storage.

---

# 239. Architecture Security Zones

Conceptually:

```text
PUBLIC ZONE
ADMIN ZONE
PRIVATE OPERATIONAL DATA
EXTERNAL PROVIDER ZONE
FILE STORAGE ZONE
DATABASE ZONE
```

---

# 240. Public Zone

May access only public/Builder-safe projections and public media.

---

# 241. Admin Zone

Authenticated access to professional/operational data according to permission.

---

# 242. Private Operational Data

Examples:

```text
Feedback body
Casting Source
internal notes
private attachments
Audit
AI source snapshots containing private context
```

must never be publicly routable by default.

---

# 243. File Storage Zones

Storage SHOULD logically distinguish:

```text
public-derived media
private source media
private operational attachments
temporary artifacts
```

even if same physical disk backend initially.

---

# 244. Media Access Authorization

Private files SHOULD be served through protected route/signed mechanism, not guessed static public path.

---

# 245. Public Originals

Even if source image is public, public pages SHOULD normally serve optimized derivative, not expose raw storage path.

---

# 246. Download Authorization

PDF/document downloads must resolve access context:

```text
public prepared
public builder-safe
private casting token
admin-only
```

---

# 247. Anti-Abuse Architecture

Public writable endpoints need:

```text
rate limiting
payload limits
file limits
duplicate protection
server validation
optional bot mitigation
```

without mandatory user registration.

---

# 248. Rate Limiting Storage

Must work safely across production instances if horizontal scaling added.

Exact technology later.

---

# 249. Error Architecture

Infrastructure errors translate into stable application/domain errors.

Public must not see:

```text
SQL
stack trace
filesystem path
provider secret
```

---

# 250. Error Correlation

Unexpected failure SHOULD return safe correlation/reference ID for diagnostics where useful.

---

# 251. Architecture of Readiness

Readiness is application/domain validation service.

Examples:

```text
QuestionnaireReadiness
EmotionalGridReadiness
ThemeReadiness
PublicBuilderGenerationReadiness
```

---

# 252. Readiness Output

Structured:

```text
status
blockers[]
warnings[]
source versions
validation version
```

---

# 253. Readiness Is Not AI

Hard blockers SHOULD be deterministic.

AI MAY explain/recommend resolution.

---

# 254. Architecture of Primary Markers

Primary uniqueness enforced:

```text
application transaction
+
database constraint where practical
```

---

# 255. Architecture of Visibility

Visibility rules evaluated:

```text
server-side
per context
```

through shared policy/services.

---

# 256. Avoid Scattered Visibility Checks

Prohibited architecture:

```text
page A checks contact.public
page B forgets
page C uses another condition
```

---

# 257. Centralized Policy Concepts

Examples:

```text
canExposeOnPublicSite()
canUseInPreparedQuestionnaire()
canUseInPublicBuilder()
```

Exact API later.

---

# 258. Architecture of Current vs Historical Data

Current page:

```text
live public projection
```

Historical Questionnaire:

```text
Revision snapshot
```

They MUST use distinct read paths.

---

# 259. No Historical Live Joins

A published historical document must not accidentally join mutable Profile fields at render time.

---

# 260. Architecture of Rollback

Rollback service uses Revision system.

It does not perform arbitrary DB restore of whole application.

---

# 261. Architecture of Audit

All significant mutation commands emit/store Audit information in standard form.

---

# 262. Audit Actor Context

Must distinguish:

```text
AdminUser
System
Virtual Operator
Casting AI
BB Assistant
Theme AI
Migration
```

---

# 263. Architecture of Provenance

Important entities SHOULD retain explicit origin relation rather than free-text only.

Examples:

```text
Casting.source_feedback_id
Project.source_opportunity_id
Questionnaire.context_casting_id
```

where final schema permits.

---

# 264. Observed Current Technology vs Target

Current implementation already provides:

```text
Next.js
TypeScript
PostgreSQL
typed media
public/admin surfaces
questionnaires
social OAuth
```

Target architecture extends this foundation rather than replacing stack wholesale.

---

# 265. Architectural Refactoring Strategy

Preferred:

```text
DOCUMENT
→ MODEL
→ MIGRATE
→ INTRODUCE MODULE BOUNDARIES
→ ADD NEW CAPABILITIES
→ REMOVE LEGACY PATHS
```

---

# 266. No Big-Bang Rewrite Requirement

Architecture does not require rewriting all working features from scratch.

Use staged migration toward canonical boundaries.

---

# 267. Strangler-Like Internal Refactoring

Existing feature can be adapted behind new domain/application interface, then legacy direct access removed.

---

# 268. Architecture Anti-Pattern ARCH-AP-001

**Microservice per domain from day one**

---

# 269. ARCH-AP-002

**One giant `admin.ts` handling all domains**

---

# 270. ARCH-AP-003

**React components directly querying/updating arbitrary tables**

---

# 271. ARCH-AP-004

**Public client receives private data then hides it**

---

# 272. ARCH-AP-005

**Questionnaire PDF generated from mutable live Profile after publish**

---

# 273. ARCH-AP-006

**Background jobs stored only in memory**

---

# 274. ARCH-AP-007

**External WhatsApp call before Feedback commit**

---

# 275. ARCH-AP-008

**AI provider response used without schema validation**

---

# 276. ARCH-AP-009

**One generic AI service with unrestricted DB context**

---

# 277. ARCH-AP-010

**Theme AI modifies content/permissions**

---

# 278. ARCH-AP-011

**QR encodes internal filesystem path**

---

# 279. ARCH-AP-012

**Separate uploaded file copy for every Questionnaire**

---

# 280. ARCH-AP-013

**Search index treated as Source of Truth**

---

# 281. ARCH-AP-014

**Redis introduced solely because “modern architecture needs Redis”**

---

# 282. ARCH-AP-015

**Public Builder stores permanent copies of Profile facts**

---

# 283. ARCH-AP-016

**Opportunity stage updated independently by Kanban and table**

---

# 284. ARCH-AP-017

**Analytics event creates/updates business outcome**

---

# 285. ARCH-AP-018

**Private Casting attachments served from public static directory**

---

# 286. ARCH-AP-019

**Admin authentication protected only by `/admin` route naming**

---

# 287. ARCH-AP-020

**Historical Revision joins current Contact**

---

# 288. ARCH-AP-021

**Media crop overwrites original file**

---

# 289. ARCH-AP-022

**Application requires AI provider to boot/render Profile**

---

# 290. ARCH-AP-023

**One universal polymorphic JSON table for all domains**

---

# 291. ARCH-AP-024

**Business rules implemented differently in every page**

---

# 292. ARCH-AP-025

**Cache invalidation based only on TTL for sensitive visibility changes**

---

# 293. Architecture Quality Gate

Перед implementation architecture считается достаточно определённой, когда подтверждены:

- [ ] modular monolith boundary;
- [ ] Public/Admin separation;
- [ ] domain module boundaries;
- [ ] application command model;
- [ ] PostgreSQL as primary authority;
- [ ] media storage abstraction;
- [ ] immutable originals;
- [ ] Public Projection layer;
- [ ] Builder Projection layer;
- [ ] durable background jobs;
- [ ] idempotency;
- [ ] transaction/outbox strategy;
- [ ] PDF snapshot rendering;
- [ ] QR safety/decode validation;
- [ ] search as derived subsystem;
- [ ] cache invalidation strategy;
- [ ] AI provider isolation;
- [ ] four AI capability boundaries;
- [ ] notification adapters;
- [ ] Feedback persistence-before-notification;
- [ ] revision binding;
- [ ] opportunity transition authority;
- [ ] audit;
- [ ] observability;
- [ ] graceful degradation;
- [ ] backup boundaries;
- [ ] security zones;
- [ ] migration path.

---

# 294. Architecture Requirement AR-001

Application MUST use one canonical PostgreSQL-backed persistence layer for authoritative business data.

---

# 295. AR-002

Public interfaces MUST consume server-authorized projections.

---

# 296. AR-003

Admin mutations MUST pass through owning application/domain command.

---

# 297. AR-004

Media source and professional classification MUST remain separate.

---

# 298. AR-005

Original media MUST be immutable.

---

# 299. AR-006

Long-running processing MUST support durable asynchronous execution.

---

# 300. AR-007

Important async commands MUST be retry-safe/idempotent.

---

# 301. AR-008

Feedback persistence MUST precede optional external notifications.

---

# 302. AR-009

PDF generation MUST bind to exact immutable Revision/Snapshot.

---

# 303. AR-010

Public Builder generation MUST perform server-side revalidation before Snapshot creation.

---

# 304. AR-011

QR generation MUST use canonical safe URL and decode verification.

---

# 305. AR-012

Public QR MUST NOT reference private/admin resources.

---

# 306. AR-013

Current public and historical revision rendering MUST use distinct data paths.

---

# 307. AR-014

Search indexes/caches MUST remain derived and rebuildable.

---

# 308. AR-015

Visibility changes MUST invalidate affected public/Builder projections promptly.

---

# 309. AR-016

AI provider failure MUST NOT disable manual core workflows.

---

# 310. AR-017

AI outputs MUST be persisted separately from confirmed professional facts.

---

# 311. AR-018

Opportunity stage transitions MUST have one authoritative server-side implementation.

---

# 312. AR-019

Theme activation MUST target exact validated ThemeRevision.

---

# 313. AR-020

Virtual Operator must use existing domain/application operations rather than direct unrestricted database mutation.

---

# 314. AR-021

Optional provider failures MUST be contained to their capability.

---

# 315. AR-022

Private operational files MUST be protected independently from public media.

---

# 316. AR-023

Business-critical state MUST NOT live only in browser or process memory.

---

# 317. AR-024

Cross-module read optimization MUST NOT create cross-module write ownership.

---

# 318. AR-025

Schema/data migrations MUST be versioned and explicit.

---

# 319. Architecture Acceptance Scenarios

## E2E-ARCH-001 — Public Profile

```text
Request
→ Public Aggregation
→ approved Profile/Portfolio projection
→ SSR/public response
```

Private Contacts absent.

---

# 320. E2E-ARCH-002 — Builder

```text
Open Builder
→ Builder projection
→ Session
→ selection
→ server revalidate
→ GenerationSnapshot
→ PDF
```

No Profile mutation.

---

# 321. E2E-ARCH-003 — Feedback + WhatsApp Failure

```text
Feedback submit
→ DB commit
→ Notification job
→ WhatsApp fails
```

Feedback remains accessible in Admin.

---

# 322. E2E-ARCH-004 — Questionnaire Publication

```text
Draft
→ validate
→ freeze snapshot
→ Revision
→ current pointer
→ artifact job
```

No mixed-state public output.

---

# 323. E2E-ARCH-005 — Historical PDF

R5 published.

Profile changes.

Worker regenerates R5 PDF.

Result uses R5 snapshot.

---

# 324. E2E-ARCH-006 — QR

```text
canonical URL
→ QR generate
→ decode
→ equality check
→ PDF
```

---

# 325. E2E-ARCH-007 — QR URL Change

Current ProfessionalLink changes.

Current QR invalidated/regenerated.

Historical Revision QR unchanged.

---

# 326. E2E-ARCH-008 — Media

Upload original.

Generate Web/PDF derivatives.

Delete derivative cache.

System regenerates without original modification.

---

# 327. E2E-ARCH-009 — Emotional Grid

Grid cells confirmed.

Freeze revision.

Generate composite derivatives asynchronously.

---

# 328. E2E-ARCH-010 — Casting AI Failure

AI provider fails.

Casting Source and manual review remain available.

---

# 329. E2E-ARCH-011 — BB Assistant

AI Draft generated.

Human applies to Profile Draft.

AI outage afterwards does not affect saved Profile Draft.

---

# 330. E2E-ARCH-012 — Opportunity

Kanban and detail screen both use same transition service.

Concurrent stale update rejected.

---

# 331. E2E-ARCH-013 — Theme

Theme AI proposes invalid contrast.

Validation blocks publish.

Current Theme unaffected.

---

# 332. E2E-ARCH-014 — Search Loss

Search index removed.

Public Profile and direct navigation still operate.

Reindex restores search.

---

# 333. E2E-ARCH-015 — Cache Leak Prevention

Private casting link and public Questionnaire must never share unauthorized cached payload.

---

# 334. E2E-ARCH-016 — Worker Restart

Queued notification/PDF job survives worker restart.

---

# 335. E2E-ARCH-017 — Duplicate Worker Delivery

Two worker attempts do not produce duplicate external notification/publication.

---

# 336. E2E-ARCH-018 — Database Restore

Database + media recover.

Derived QR/search/thumbs may rebuild.

---

# 337. E2E-ARCH-019 — Public Media Privacy

Private Feedback attachment cannot be retrieved from public media URL namespace.

---

# 338. E2E-ARCH-020 — AI Context Privacy

Theme AI request contains no Feedback/Casting private text.

---

# 339. Architecture Traceability

Required chain:

```text
BR / Product Rule
       ↓
Domain Rule
       ↓
Architecture Requirement
       ↓
Component
       ↓
Data Flow
       ↓
API / DB
       ↓
Issue
       ↓
Test
```

---

# 340. Subsequent Architecture Decomposition

DOC-070 является обзорным документом.

Детализация должна происходить в следующих Architecture documents.

---

# 341. DOC-071 — Components

Зафиксирует:

```text
Web Runtime
Public Application
Admin Application
Domain Modules
Application Services
Repositories
Worker
Storage
PDF
QR
Search
AI
Notifications
Integrations
```

и dependency contracts.

---

# 342. DOC-072 — Data Flows

Опишет end-to-end:

```text
public reads
publication
uploads
Builder
Feedback
Casting
Opportunity
AI
Theme
notifications
```

---

# 343. DOC-073 — Projections

Определит:

```text
Public Profile Projection
Builder Projection
Quick View
Dashboard Read Models
Search Projection
historical projections
```

---

# 344. DOC-074 — Media Architecture

Определит:

```text
storage layout
originals
derivatives
processing
public/private media
upload pipeline
```

---

# 345. DOC-075 — PDF / Document Architecture

Определит:

```text
render model
HTML/PDF parity
revision binding
asset embedding
links
QR
accessibility
```

---

# 346. DOC-076 — Background Jobs

Определит:

```text
queue
worker
outbox
retries
idempotency
scheduling
cleanup
```

---

# 347. DOC-077 — Cache Architecture

Определит:

```text
cache classes
keys
invalidation
security
revision caching
```

---

# 348. DOC-078 — Search Architecture

Определит Public/Admin search.

---

# 349. DOC-079 — AI Architecture

Определит четыре AI capabilities, context boundaries, provider abstraction и provenance.

---

# 350. DOC-080 — Notifications Architecture

Определит:

```text
In-App
WhatsApp
Email
quiet hours
retry
delivery
failure isolation
```

---

# 351. Final Architecture Diagram

```text
┌──────────────────────────────────────────────────────────────┐
│                        INTERNET / USERS                      │
└──────────────────────────────┬───────────────────────────────┘
                               │ HTTPS
                               ▼
                      ┌──────────────────┐
                      │ Reverse Proxy/TLS│
                      └────────┬─────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│                    NEXT.JS MODULAR MONOLITH                  │
│                                                              │
│  PUBLIC UI                     ADMIN UI                       │
│     │                             │                           │
│     └────────────┬────────────────┘                           │
│                  ▼                                            │
│            APPLICATION LAYER                                 │
│                  │                                            │
│  ┌───────────────┼─────────────────────────────────────────┐  │
│  │               DOMAIN MODULES                            │  │
│  │ Profile  Media  Portfolio  Emotional  Projects         │  │
│  │ Training Skills Contacts Questionnaires Builder        │  │
│  │ Feedback Castings Opportunities Themes AI etc.         │  │
│  └───────────────┼─────────────────────────────────────────┘  │
│                  │                                            │
│        ┌─────────┼───────────┐                                │
│        ▼         ▼           ▼                                │
│    Repositories Public    Application                         │
│                 Projection Services                           │
└────────┬──────────┬───────────┬───────────────────────────────┘
         │          │           │
         ▼          ▼           ▼
 PostgreSQL    Media Storage   Durable Jobs
                                  │
                                  ▼
                          Background Worker
                                  │
         ┌────────────────────────┼────────────────────────┐
         ▼                        ▼                        ▼
     PDF / QR                  AI Provider             Notifications
     Media Jobs                 Adapters               Social/External
```

---

# 352. Финальная архитектурная доктрина

> **Платформа должна оставаться единым, понятным и транзакционно надёжным приложением, но внутренне быть разделена на строгие доменные модули. PostgreSQL хранит авторитетные бизнес-данные, media storage — бинарные источники и производные файлы, Public интерфейс получает только серверно разрешённые проекции, а дорогая и внешняя работа выполняется через надёжные background jobs. PDF, QR, Search, AI, Notifications и Analytics являются специализированными capabilities вокруг основного профессионального ядра и не могут становиться альтернативными источниками истины. Архитектура должна позволять постепенно масштабировать эти capabilities независимо, но не ценой преждевременной распределённости, потери транзакционной целостности или усложнения эксплуатации.**