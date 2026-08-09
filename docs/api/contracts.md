# API CONTRACTS

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативные DTO, Command/Query contracts, pagination, versions, references и contract evolution

**Целевой файл:** `docs/api/contracts.md`  
**Документ:** DOC-101  
**Статус:** ✅ Completed  
**Тип:** API / Contracts / DTO / Commands / Queries / Compatibility

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/domain/domain-model.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions-and-history.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`
- `docs/architecture/media-processing.md`
- `docs/architecture/pdf-generation.md`
- `docs/architecture/background-jobs.md`
- `docs/architecture/cache-strategy.md`
- `docs/architecture/search.md`
- `docs/architecture/ai.md`
- `docs/architecture/notifications.md`
- `docs/database/database-architecture.md`
- `docs/database/data-dictionary.md`
- `docs/database/visibility-and-access.md`
- `docs/database/validation-and-constraints.md`
- `docs/database/deletion-and-retention.md`
- `docs/api/server-actions.md`

---

# 1. Назначение

Настоящий документ определяет **единый контракт данных между Presentation Layer и Application Layer**, а также внешний HTTP/API contract там, где он нужен.

Он фиксирует:

1. Command DTO;
2. Query DTO;
3. Projection DTO;
4. identifiers;
5. expected versions;
6. timestamps;
7. calendar dates;
8. locales;
9. URLs;
10. measurements;
11. pagination;
12. filtering;
13. sorting;
14. entity references;
15. asynchronous-operation contracts;
16. upload/download references;
17. artifact references;
18. Builder contracts;
19. Questionnaire contracts;
20. AI contracts;
21. Notification contracts;
22. Opportunity contracts;
23. serialization;
24. compatibility;
25. contract versioning;
26. deprecation;
27. testing.

---

# 2. Главная доктрина

> **API contract описывает намерение и безопасное представление данных, а не физическую структуру PostgreSQL.**

Canonical:

```text id="up7vk2"
DATABASE ROW
    ≠
DOMAIN ENTITY
    ≠
COMMAND DTO
    ≠
QUERY DTO
    ≠
PROJECTION DTO
```

---

# 3. Contract identifiers

Используются:

```text id="a3xp2u"
CTR-*
CTR-CMD-*
CTR-QRY-*
CTR-DTO-*
CTR-PAG-*
CTR-REF-*
CTR-ASYNC-*
CTR-FILE-*
CTR-VER-*
CTR-INV-*
CTR-AP-*
```

---

# 4. CTR-INV-001 — ORM Shape Is Never API Contract

Запрещено считать стабильным API:

```text id="w2we9a"
PrismaProject
DrizzleProjectRow
SELECT *
```

---

# 5. CTR-INV-002 — Input and Output DTOs Are Separate

Input для mutation и output после mutation не обязаны иметь одинаковую форму.

---

# 6. Example

Input:

```text id="q75wpb"
UpdateProjectInput {
  projectId
  expectedVersion
  title
  description
}
```

Output:

```text id="kr9hdv"
ProjectMutationResult {
  id
  version
  updatedAt
}
```

---

# 7. CTR-INV-003 — Context-Specific DTOs

Нельзя иметь один:

```text id="1nlz6q"
ProjectDTO
```

для:

```text id="82j3y0"
Public
Admin
Builder
Search
AI
```

если эти contexts имеют разные visibility semantics.

---

# 8. Canonical DTO categories

```text id="0gk8ht"
CommandInput
CommandResult
QueryInput
ProjectionDTO
AsyncOperationDTO
ArtifactDTO
ReferenceDTO
ErrorDTO
```

---

# 9. Command input

Command input содержит только данные, которые caller имеет право предложить.

---

# 10. Command input MUST NOT contain authoritative system fields

Examples:

```text id="d5np95"
createdAt
updatedAt
publishedAt
publishedBy
revisionNumber
processingState
Audit actor
job status
providerMessageId
```

---

# 11. Command input may contain

```text id="p3e0vo"
entity ID
expectedVersion
human-entered values
explicit visibility flags
selection/order
semantic operation parameters
idempotency key where required
```

---

# 12. Command result

Command result SHOULD be minimal.

---

# 13. Recommended mutation result

```text id="rl9nqu"
{
  id,
  version,
  state?,
  updatedAt?
}
```

---

# 14. Why minimal

UI should refetch/refresh canonical Projection when broad state changed instead of reconstructing aggregate from mutation response.

---

# 15. Rich command result

Allowed when immediate UX materially benefits.

Example:

```text id="sw8zuc"
FinalizeEmotionalGridResult {
  gridId
  revisionId
  revisionNumber
  artifactState
}
```

---

# 16. Query input

Contains:

```text id="brmnng"
identifier
filter
sort
pagination
locale
context-specific options
```

No business mutation fields.

---

# 17. Projection DTO

Projection DTO is **already authorized and visibility-filtered**.

---

# 18. CTR-INV-004 — DTO Cannot Require Client Privacy Filtering

Public DTO never contains:

```text id="g92ou7"
hidden contacts
admin notes
storage locators
AI private metadata
```

just to be filtered in UI.

---

# 19. Identifier contract

Canonical business identifier:

```text id="phssgf"
UUID serialized as canonical lowercase string
```

unless public resource intentionally uses slug/token.

---

# 20. Internal ID

Example:

```text id="v1wjif"
"550e8400-e29b-41d4-a716-446655440000"
```

---

# 21. Slug

Used for human/public routing where appropriate.

---

# 22. Slug rules

Should be:

```text id="6au029"
normalized
URL-safe
stable enough for external links
```

---

# 23. Slug ≠ database identity

Canonical Source relation uses UUID.

---

# 24. Opaque token

Used only for scoped access.

---

# 25. CTR-INV-005 — Token Is Never Returned as Ordinary Entity ID

Token contracts are explicit.

---

# 26. Entity reference

Canonical lightweight reference:

```text id="x34v10"
EntityRef {
  id
  type
}
```

---

# 27. When title is needed

Use:

```text id="1pef9h"
EntitySummaryRef {
  id
  type
  label
}
```

only after appropriate projection/visibility filtering.

---

# 28. No generic polymorphic reference from arbitrary caller

`type` MUST come from allowlist.

---

# 29. Version contract

Mutable high-value DTOs expose:

```text id="9ap883"
version: integer
```

or JS-safe serialized bigint representation.

---

# 30. Recommended transport

Although DB uses `bigint`, API SHOULD expose monotonically increasing version in a representation safe for JavaScript.

---

# 31. Baseline

If practical range is bounded:

```text id="0u7gcu"
number
```

may be used.

Otherwise:

```text id="gc8qhs"
decimal string
```

---

# 32. One project-wide choice

Do not mix:

```text id="cdzpmr"
version: 7
```

and:

```text id="bzvbi4"
version: "7"
```

arbitrarily across DTOs.

---

# 33. Recommended current contract

Use:

```text id="sdan7a"
version: number
```

while enforcing safe integer bounds.

DB `bigint` remains persistence detail.

---

# 34. expectedVersion

Mutation inputs for protected concurrent resources:

```text id="6tyhtu"
expectedVersion: number
```

required.

---

# 35. CTR-INV-006 — Version Is Opaque Concurrency Value

Client must not infer business meaning from:

```text id="rv0rk9"
version=12
```

except equality/change.

---

# 36. Revision number

Different from entity version.

---

# 37. Example

```text id="1eqwym"
version = 14
revisionNumber = 3
```

valid.

---

# 38. Timestamp contract

All timestamps serialized as:

```text id="6vvd0i"
RFC 3339 / ISO 8601 with explicit timezone/UTC
```

Recommended wire form:

```text id="5aaxwn"
2026-08-08T15:10:20.123Z
```

---

# 39. Client locale formatting

Wire timestamp is not localized display string.

---

# 40. Calendar date

Domain `date` fields serialized:

```text id="v8p62k"
YYYY-MM-DD
```

Example:

```text id="j82h8c"
2026-07-27
```

---

# 41. CTR-INV-007 — Date-Only Must Not Become UTC Timestamp

`shootingDate = 2026-07-27`

must not become:

```text id="nm611l"
2026-07-26T19:00:00Z
```

through timezone conversion.

---

# 42. Timezone-bearing schedule

Use timestamp + explicit operational timezone where semantics require local schedule.

---

# 43. Example Theme schedule

```text id="ypioyg"
startsAt
endsAt
timezone
```

if user-facing local scheduling needs preserved timezone.

---

# 44. Locale contract

Use normalized supported locale identifiers.

Recommended baseline:

```text id="62s90p"
ru
en
```

or explicit project-supported BCP 47 list.

---

# 45. Locale normalization

Input such as:

```text id="aq46j2"
RU
ru-RU
```

must be mapped only if approved policy defines equivalent.

Do not invent locale availability.

---

# 46. Projection locale

DTO SHOULD indicate effective locale when fallback can occur.

Example:

```text id="nrl2bx"
requestedLocale: "en"
effectiveLocale: "ru"
```

only where fallback is intentionally allowed.

---

# 47. URL contract

URLs transported as absolute canonical URL where public/clickable output requires it.

---

# 48. Internal route reference

Admin DTO MAY use application route:

```text id="scahpr"
/admin/castings/...
```

as route, not canonical external URL.

---

# 49. Public professional URL

Should be absolute in contexts such as:

```text id="etajbu"
PDF
QR
external notification
```

---

# 50. CTR-INV-008 — Storage Locator Is Not URL DTO

Never expose:

```text id="t9y9yx"
/data/kate-actor/media/original/...
```

as media URL.

---

# 51. Measurement contract

Physical parameters use typed values with explicit unit semantics.

---

# 52. Height

Preferred:

```text id="az8ql1"
heightCm: number
```

not:

```text id="836fo5"
height: "172 см"
```

as source DTO.

---

# 53. Display formatting

Presentation converts:

```text id="px9qmx"
172
→
172 см
```

---

# 54. Shoe/clothing

If source representation isn't safely numeric/universal, use structured/string value defined by module.

Do not over-normalize ambiguous domain values.

---

# 55. Numeric decimal serialization

Avoid floating-point precision dependence for values requiring exact decimals.

For current physical parameters minor decimal precision is acceptable if validated.

---

# 56. Boolean contract

Always explicit `true/false`.

Avoid:

```text id="4txbyt"
0
1
"yes"
"true"
```

---

# 57. Nullable contract

Use `null` only where domain says “known absence/not specified”.

---

# 58. Missing property vs null

Recommended:

- mutation partial input: omitted = no change;
- explicit nullable field: `null` = clear value;
- projection: field may be absent entirely when visibility forbids it;
- projection field may be `null` when visible concept exists but value is not specified.

---

# 59. CTR-INV-009 — Hidden ≠ Null

Public hidden Contact:

```text id="acw30u"
property absent
```

not:

```text id="sbd59x"
phone: null
```

if even field existence is not part of contract.

---

# 60. Array order

If order is meaningful, response order is authoritative and SHOULD already be sorted.

---

# 61. Do not require UI to sort by hidden `displayOrder` if not needed

Public DTO can simply return ordered array.

---

# 62. Admin DTO

May expose:

```text id="m8g4ca"
displayOrder
```

for drag/reorder UX.

---

# 63. Reorder input

Recommended semantic contract:

```text id="gvtvlc"
ReorderItemsInput {
  expectedContainerVersion?
  orderedIds: UUID[]
}
```

---

# 64. Reorder validation

Requires:

```text id="jlkfwr"
same exact eligible set or defined subset
no duplicate IDs
bounded count
same owner/context
```

---

# 65. Pagination

All potentially unbounded list Queries use pagination.

---

# 66. Canonical pagination types

Two supported families:

```text id="e17ubz"
CursorPagination
OffsetPagination
```

---

# 67. Cursor pagination preferred

For:

```text id="3mugcm"
Feedback Inbox
Notifications
Audit
Analytics event streams
```

where stable chronological navigation matters.

---

# 68. Offset pagination acceptable

For small Admin catalog tables where simplicity is valuable.

---

# 69. One endpoint/query uses one documented pagination model

No ambiguous combination.

---

# 70. Cursor request

Conceptually:

```text id="7u3kye"
{
  after?: string,
  limit: number
}
```

---

# 71. Cursor response

```text id="ptns1w"
{
  items: [...],
  pageInfo: {
    hasNextPage,
    endCursor?
  }
}
```

---

# 72. Cursor is opaque

Client must not parse/construct it.

---

# 73. Cursor may encode

```text id="9ek70d"
sort value
ID tie-breaker
query version
```

but internal structure is not contract.

---

# 74. Offset request

```text id="5zmbzg"
{
  page: number,
  pageSize: number
}
```

---

# 75. Offset response

```text id="y1ox54"
{
  items,
  page,
  pageSize,
  total?
}
```

---

# 76. Total count

Should only be calculated where cost is acceptable/UX needs it.

---

# 77. Pagination bounds

Server enforces maximum `limit/pageSize`.

---

# 78. Default limit

Each query defines explicit reasonable default.

---

# 79. CTR-PAG-INV-001 — No `limit=100000`

Unbounded requests rejected/clamped according to contract.

---

# 80. Filtering contract

Filters are typed, allowlisted fields.

---

# 81. Example Feedback filter

```text id="we0nv8"
FeedbackFilter {
  type?
  workflowState?
  responsibleAdminId?
  unreadOnly?
  createdFrom?
  createdTo?
}
```

---

# 82. No raw SQL filter expression

Prohibited:

```text id="ocxwiu"
filter: "workflow_state='new' OR 1=1"
```

---

# 83. Sorting contract

Use stable enum:

```text id="5f5enm"
sort: "created_desc"
```

or:

```text id="3yhico"
{
  field: "createdAt",
  direction: "desc"
}
```

with strict allowlist.

---

# 84. Stable tie-breaker

Pagination sorting SHOULD include immutable unique tie-breaker:

```text id="jha5q7"
created_at DESC, id DESC
```

---

# 85. Search contract

Search input:

```text id="yrkuhf"
query
locale
scope implied/server-controlled
filters
pagination
```

---

# 86. Public Search caller cannot choose `scope=admin`

Scope resolved from endpoint/query context.

---

# 87. Search result DTO

Recommended:

```text id="61gkj3"
SearchResultDTO {
  type
  id
  title
  subtitle?
  snippet?
  route
  relevance?
}
```

---

# 88. Relevance

If exposed, it is presentation/derived metric, not professional score.

---

# 89. Search result must not expose private explanation

No:

```text id="k7n5y7"
hidden because...
```

---

# 90. Projection DTO version

For large/stable external contracts, MAY expose explicit:

```text id="b2m3g3"
schemaVersion
```

---

# 91. Internal same-app Server Action DTOs

Do not require explicit schema version on every object if code is deployed atomically.

---

# 92. Persisted contracts do require version

Examples:

```text id="65ga8g"
Outbox payload
Background Job payload
Revision snapshot
Builder snapshot
Analytics event
AI structured output
```

---

# 93. Public Profile DTO

Conceptually:

```text id="b071t2"
PublicActorProfileDTO {
  slug
  professionalName
  professionalTitle
  locale
  quickFacts
  hero
  portfolio
  video
  emotional
  selectedProjects
  skills
  languages
  training
  achievements
  questionnaires
  contacts
}
```

Only permitted sections included.

---

# 94. Public Quick Facts

Example:

```text id="p8jn17"
{
  city?,
  heightCm?,
  languages?: [...],
  selectedSkills?: [...]
}
```

based on field/item visibility.

---

# 95. Public Portfolio DTO

```text id="c8nybl"
PortfolioPhotoDTO {
  id
  category
  image
  caption?
}
```

---

# 96. Public media reference

Recommended:

```text id="9r3eyz"
PublicImageDTO {
  url
  width
  height
  alt
  srcSet? / variants?
}
```

---

# 97. Public media DTO MUST NOT include

```text id="wfmd5n"
storageLocator
checksum
EXIF GPS
processing internals
original local filename
```

unless explicit safe use case.

---

# 98. Admin Media DTO

May include:

```text id="4oe9ao"
id
originalFilename
mimeType
fileSizeBytes
processingState
technical dimensions
usage summary
createdAt
```

Still no secret storage path by default.

---

# 99. Media Usage DTO

Recommended:

```text id="gkn2y4"
MediaUsageDTO {
  usageType
  entityRef
  label
  currentOrHistorical
}
```

---

# 100. Portfolio Admin DTO

Includes:

```text id="o8987s"
id
version
media
category
caption
publicationState
visibility
displayOrder
isPrimary
usageWarnings
```

---

# 101. Visibility DTO

Canonical reusable:

```text id="dfchj5"
VisibilityPolicyDTO {
  showOnPublicSite
  allowInAdminQuestionnaires
  allowInPublicQuestionnaireBuilder
}
```

---

# 102. CTR-INV-010 — Visibility Flags Are Explicit

No combined:

```text id="e7b58l"
visibility: "public"
```

because three dimensions are independent.

---

# 103. Emotional Session Public DTO

```text id="x7u14n"
EmotionalSessionDTO {
  id
  title?
  shootingDate
  description?
  primaryGrid?
  sourcePhotos? // only full emotional portfolio policy
}
```

---

# 104. Questionnaire Emotional block DTO

Different contract:

```text id="mblc33"
EmotionalQuestionnaireBlockDTO {
  gridComposite
  shootingDate
  fullPortfolioUrl
}
```

No individual emotion photos.

---

# 105. Emotional Grid Draft Admin DTO

```text id="4w0rxb"
EmotionalGridDraftDTO {
  id
  version
  rows
  columns
  cells[]
  readiness
}
```

---

# 106. Grid cell DTO

```text id="c1v66e"
EmotionalGridCellDTO {
  id
  position
  media
  crop
  faceOccupancyEstimate?
  confirmationState
}
```

---

# 107. Crop DTO

```text id="1qf4us"
CropTransformDTO {
  cropX
  cropY
  scale
  rotationDeg
  panX
  panY
}
```

---

# 108. Coordinates contract

Grid module MUST define normalized coordinate range in module spec.

Do not silently switch between pixels and normalized ratios.

---

# 109. Recommended

Use normalized transform values independent of source output resolution where feasible.

---

# 110. Project Public DTO

```text id="grvdk3"
ProjectDTO {
  id
  slug?
  title
  projectType
  period?
  description?
  roles[]
}
```

Only allowed public content.

---

# 111. Admin Project DTO

Additionally:

```text id="zj92mv"
version
publicationState
visibility
isFeatured
displayOrder
sourceOpportunity?
```

---

# 112. Skill DTO

Public:

```text id="ouwjuh"
{
  code
  label
  level
  description?
}
```

---

# 113. Language DTO

```text id="xvyhzv"
{
  code
  label
  proficiency: {
    type: "native" | "cefr",
    cefr?: "A1" | "A2" | "B1" | "B2" | "C1" | "C2"
  }
}
```

---

# 114. CTR-INV-011 — Native Is Not CEFR C2

Do not encode native speaker by falsely assigning C2.

---

# 115. Professional Link DTO

Canonical professional link row semantics:

```text id="y7giz0"
ProfessionalLinkDTO {
  id
  description
  url
  linkType
}
```

---

# 116. Questionnaire link DTO

```text id="zghvyi"
QuestionnaireLinkDTO {
  number
  description
  url
  showQr
  qr?
}
```

---

# 117. `number`

Document ordering number.

Not durable entity identity.

---

# 118. QR DTO

Public/document renderer side:

```text id="v1chnf"
QrDTO {
  targetUrl
  artifactUrl
  format
}
```

Only after validated eligibility.

---

# 119. Admin QR diagnostics DTO

May additionally expose:

```text id="wria0m"
validationState
decodedUrl
settingsVersion
```

---

# 120. Contacts DTO — Public

```text id="7ox2je"
PublicContactDTO {
  type
  label?
  personName?
  relationshipType?
  displayValue
  href
}
```

---

# 121. Contact href

Derived server-side:

```text id="v8pkhv"
tel:
mailto:
approved WhatsApp URL
social URL
```

---

# 122. Public Contact DTO does not expose visibility flags

They are Admin configuration, not public content.

---

# 123. Admin Contact DTO

```text id="xjbyh2"
AdminContactDTO {
  id
  version
  type
  label?
  relationshipType?
  personName?
  value
  normalizedValue?
  visibility
  displayOrder
  lifecycleState
}
```

---

# 124. Questionnaire Draft DTO

```text id="8q5bxc"
QuestionnaireDraftDTO {
  id
  questionnaireId
  version
  type
  name
  locale
  sections[]
  readiness
}
```

---

# 125. Section DTO

```text id="qtex8w"
QuestionnaireSectionDTO {
  type
  enabled
  displayOrder
  items[]
  settings
}
```

---

# 126. Settings

Must be typed per section where practical.

Avoid giant untyped client JSON.

---

# 127. Readiness DTO

Canonical:

```text id="byf8ei"
ReadinessDTO {
  state
  blockers[]
  warnings[]
}
```

---

# 128. Readiness issue

```text id="mdcm0s"
ReadinessIssueDTO {
  code
  entityRef?
  field?
  parameters?
  remediationAction?
}
```

---

# 129. Human-facing message localization

Prefer frontend/message catalog using stable code + parameters.

---

# 130. Questionnaire Revision DTO

Admin/history:

```text id="b5thwh"
QuestionnaireRevisionSummaryDTO {
  id
  revisionNumber
  publishedAt
  publishedBy
  locale
  artifactStatus
}
```

---

# 131. Full immutable Revision snapshot

Should not generally be sent raw to browser.

Use historical document/projection DTO.

---

# 132. Questionnaire public current DTO

Contains document model-derived safe sections, not DB snapshot internals.

---

# 133. Builder template DTO

```text id="60ta00"
BuilderTemplateDTO {
  id
  type
  name
  description?
  defaultSections
}
```

---

# 134. Builder session DTO

```text id="5a7pcq"
BuilderSessionDTO {
  id
  version
  locale
  template
  castingContext?
  sections
  readiness
  expiresAt
}
```

---

# 135. Builder session MUST NOT expose

```text id="6gpslf"
all hidden profile entity IDs
admin questionnaire permissions
internal source versions unrelated to UX
```

---

# 136. Builder eligible item DTO

```text id="bu915a"
BuilderEligibleItemDTO {
  id
  type
  label
  preview?
  selected
}
```

---

# 137. Builder selection input

```text id="p2nnfl"
SelectBuilderItemInput {
  sessionId
  expectedVersion
  sectionType
  entityType
  entityId
}
```

---

# 138. Browser does not submit item payload

Only identity/intention.

Server reloads Source.

---

# 139. Builder Generation result

Asynchronous:

```text id="v3dv1y"
GenerateBuilderResult {
  generationSnapshotId
  generationNumber
  artifact
}
```

---

# 140. Artifact processing reference

```text id="iv08ei"
ArtifactProcessDTO {
  artifactId
  state
  downloadUrl?
}
```

---

# 141. `downloadUrl`

Present only when state/access permits.

---

# 142. Artifact states

Use domain-specific stable values such as:

```text id="f22vth"
queued
generating
validating
ready
failed
```

---

# 143. CTR-INV-012 — No Raw Background Job DTO as User Artifact Status

User observes Artifact state.

---

# 144. Feedback input contract

Public:

```text id="nuo9ln"
CreateFeedbackInput {
  feedbackType
  senderName?
  senderOrganization?
  senderEmail?
  senderPhone?
  message
  sourceContext?
  questionnaireSessionId?
  idempotencyKey
}
```

---

# 145. Public input excludes

```text id="ri8uw2"
workflowState
adminNote
responsibleAdmin
castingId
notification settings
```

---

# 146. Feedback create result

```text id="sylrfh"
CreateFeedbackResult {
  id
  receivedAt
}
```

Keep public response minimal.

---

# 147. Feedback Admin DTO

```text id="wccl2t"
FeedbackAdminDTO {
  id
  version
  type
  sender
  message
  attachments
  sourceContext?
  workflow
  responsibleAdmin?
  nextAction?
  readAt?
  createdAt
  linkedCasting?
}
```

---

# 148. Sender DTO

Admin-only:

```text id="h55x5g"
{
  name?
  organization?
  email?
  phone?
}
```

---

# 149. Casting DTO

Admin:

```text id="1lr9oz"
CastingAdminDTO {
  id
  version
  title
  projectName?
  roleName?
  organization?
  deadlineAt?
  status
  source
  currentAnalysis?
  opportunity?
}
```

---

# 150. Casting source DTO

Must distinguish:

```text id="np80bf"
originalSource
derivedTextExtraction
```

where applicable.

---

# 151. Casting requirement DTO

```text id="yvz6xy"
CastingRequirementDTO {
  id
  version
  category
  extractionState
  extractedValue?
  sourceEvidence?
  humanDecisionState
  confirmedValue?
  match?
}
```

---

# 152. CTR-INV-013 — Extraction and Confirmation Are Separate Fields

Never:

```text id="t50umo"
value: B2
```

with no information whether AI or Human.

---

# 153. Match DTO

```text id="yfckng"
CastingMatchDTO {
  state
  profileValue?
  reason
  algorithmVersion?
}
```

---

# 154. Match state

Exact:

```text id="5hu82c"
match
partial
mismatch
no_confirmed_data
not_applicable
```

---

# 155. Casting recommendation DTO

```text id="dys3gy"
{
  id
  type
  recommendation
  rationale
  status
}
```

---

# 156. Opportunity DTO

```text id="2h6pfk"
OpportunityDTO {
  id
  version
  title
  stage
  casting?
  responsibleAdmin?
  nextAction?
  nextActionAt?
  updatedAt
}
```

---

# 157. Transition input

```text id="db7dye"
TransitionOpportunityInput {
  opportunityId
  expectedVersion
  toStage
  reason?
  idempotencyKey?
}
```

---

# 158. Transition result

```text id="7pp5k3"
TransitionOpportunityResult {
  id
  previousStage
  stage
  version
  changedAt
}
```

---

# 159. Board DTO

Kanban/pipeline query can group:

```text id="9x8j71"
{
  stages: [
    {
      stage,
      opportunities
    }
  ]
}
```

but stage list comes from canonical domain ordering.

---

# 160. AI generation input

Admin client should provide task intent, not raw provider prompt.

---

# 161. Bad

```text id="nh54nk"
{
  systemPrompt,
  model,
  temperature,
  fullDatabaseDump
}
```

---

# 162. Good BB input

```text id="b2kngq"
GenerateBBDraftInput {
  taskType
  contextEntity?
  language
  tone?
  length?
  additionalInstruction?
}
```

---

# 163. Context built server-side

Normative.

---

# 164. BB Draft DTO

```text id="qq6ocv"
AIDraftDTO {
  id
  taskType
  language
  generatedText
  editedText?
  status
  sourceSummary
  createdAt
  stale
}
```

---

# 165. Public/client UI should not require provider metadata

Admin diagnostics may expose:

```text id="sf0lbh"
model
promptVersion
```

if useful.

---

# 166. Apply BB input

```text id="f6vrvv"
ApplyBBDraftInput {
  draftId
  targetEntityId
  expectedTargetVersion
}
```

---

# 167. Theme Proposal DTO

```text id="dv6174"
ThemeProposalDTO {
  id
  sourceDraftVersion
  proposedChanges
  validation
  status
}
```

---

# 168. Theme Public DTO

Public browser receives resolved design tokens/assets needed for presentation.

It MUST NOT receive:

```text id="qqk0cr"
AI prompt
proposal history
Admin locks
private validation diagnostics
```

---

# 169. VOP Observation DTO

```text id="y2isq7"
VOPObservationDTO {
  id
  type
  severity
  status
  title
  evidenceSummary
  affectedEntity
  recommendation?
  detectedAt
}
```

---

# 170. VOP action input

Must be explicit semantic action ID/type approved by server.

No arbitrary command string.

---

# 171. Notification Center DTO

```text id="gi0lqk"
NotificationDTO {
  id
  type
  priority
  title
  message
  source?
  adminRoute?
  read
  createdAt
  deliverySummary?
}
```

---

# 172. Delivery summary

```text id="gztu1h"
NotificationDeliverySummaryDTO {
  channels: [
    {
      channel
      state
      retryable?
    }
  ]
}
```

---

# 173. External notification contract

Not same as Notification Center DTO.

It uses channel-rendered minimal template variables.

---

# 174. Social Post DTO

Admin:

```text id="rfhv30"
SocialPostDTO {
  id
  version
  content
  media
  status
  approvedVersion?
  scheduledAt?
  targets[]
}
```

---

# 175. Target DTO

```text id="w8s45r"
SocialTargetDTO {
  id
  platform
  account
  approvedPostVersion
  status
  publishedAt?
  error?
}
```

---

# 176. Async operation contract

Operations that continue after HTTP/action response MUST return accepted/persisted processing state.

---

# 177. Canonical async result

```text id="va11hi"
AsyncOperationDTO {
  operationId
  resourceType
  resourceId
  state
  statusUrl? / queryKey?
}
```

---

# 178. Prefer domain resource identity

Example PDF:

```text id="dwm5gr"
artifactId
```

rather than exposing generic job ID.

---

# 179. Job ID

May be included only in Admin system diagnostics.

---

# 180. Accepted async states

Typical initial:

```text id="ew65fo"
queued
processing
```

---

# 181. Completion observed through Query

No background promise callback to browser is required.

---

# 182. Polling contract

Should support bounded polling.

---

# 183. Future realtime

WebSocket/SSE may deliver state changes later.

It must preserve same domain DTO/state contract.

---

# 184. File input contract

Metadata input separate from binary stream.

---

# 185. Upload request

Can include:

```text id="hkas9v"
purpose
context entity
declared filename
declared MIME
```

but declared MIME is untrusted hint.

---

# 186. Upload result

```text id="urqe3e"
MediaUploadResult {
  mediaAssetId
  processingState
}
```

---

# 187. Never return storage locator

Normative.

---

# 188. Private attachment reference

Admin:

```text id="b4mbro"
PrivateAttachmentDTO {
  id
  filename
  mimeType
  fileSizeBytes
  securityState
  downloadUrl?
}
```

---

# 189. Public attachment

Only if explicit professional/public content route exists.

---

# 190. Artifact reference

Canonical:

```text id="gj97ck"
ArtifactRefDTO {
  id
  type
  state
  url?
  mimeType?
  fileSizeBytes?
}
```

---

# 191. Historical artifact

May additionally expose:

```text id="1r6yjk"
sourceRevisionNumber
generatedAt
```

---

# 192. `url` is conditional

No URL before:

```text id="x2kva5"
READY
AND
access allowed
```

---

# 193. Error envelope

Detailed DOC-102.

DOC-101 defines baseline:

```text id="qri2s7"
ApiErrorDTO {
  code
  message?
  fieldErrors?
  retryable?
  correlationId?
}
```

---

# 194. Error message

Can be localized server-side or mapped client-side.

Stable `code` is authoritative for behavior.

---

# 195. Field errors

Recommended:

```text id="edmxvl"
{
  field: "title",
  code: "VALIDATION_REQUIRED",
  parameters?: {}
}
```

---

# 196. Error path

Nested input MAY use:

```text id="6dplml"
sections[2].items[1]
```

or structured path array.

One project-wide convention required.

---

# 197. Command success envelope

For Route Handler/API:

```text id="bpzb1b"
{
  data: ...
}
```

is sufficient.

---

# 198. Server Action result

May use:

```text id="ih0fmb"
{
  ok: true,
  data
}
```

because Actions do not rely solely on HTTP status.

---

# 199. CTR-INV-014 — Do Not Double-Nest Arbitrarily

Avoid:

```text id="0o48hz"
{
  success: true,
  result: {
    data: {
      payload: ...
    }
  }
}
```

---

# 200. HTTP Query success

Prefer direct documented resource envelope.

---

# 201. List envelope

Recommended:

```text id="4uooyn"
{
  items,
  pageInfo
}
```

rather than generic global envelope with unrelated metadata.

---

# 202. Contract serialization

All DTOs must be safely serializable through selected Next.js/HTTP boundary.

---

# 203. Avoid transport types such as

```text id="js18dn"
BigInt
Date object
Map
Set
class instance
database Decimal object
```

unless explicitly serialized first.

---

# 204. `Date`

Wire DTO uses string.

---

# 205. `BigInt`

Wire DTO uses selected number/string strategy.

---

# 206. Binary

Never JSON base64 for ordinary large media upload/download.

Use binary/stream/multipart where appropriate.

---

# 207. Contract casing

Recommended TypeScript/JSON:

```text id="4vxpru"
camelCase
```

Database remains `snake_case`.

---

# 208. One boundary owns mapping

Repository/domain rows are mapped to application/projection DTO deliberately.

---

# 209. No automatic DB serializer

Prohibited:

```text id="mc260q"
JSON.stringify(dbRow)
```

for Public/API response.

---

# 210. Contract versioning doctrine

Not every internal DTO needs `v1`, `v2`.

---

# 211. Version when persisted or independently deployed

Required/strongly recommended for:

```text id="t33ris"
background job payload
outbox event
webhook internal persisted normalization
analytics event
snapshot schema
AI structured schema
```

---

# 212. External/public HTTP API

If a stable external consumer API is later exposed:

use explicit URL/header/schema versioning policy.

---

# 213. Current application

Same Web/Admin frontend and server can evolve in lockstep.

Therefore over-versioning every internal action is unnecessary.

---

# 214. Contract evolution

Safe additive changes:

```text id="3dqw22"
optional output field added
new enum only when old client handles unknown safely
new optional input
```

---

# 215. Breaking changes

Examples:

```text id="jqs93i"
field removal
meaning change
required field added
enum semantic change
type change
cursor format contract if client parses it
```

---

# 216. Internal atomic deployment

Breaking internal DTO change may be deployed together if no durable queued/saved payload depends on old shape.

---

# 217. Durable contract

Must remain backwards readable while old payloads can still exist.

---

# 218. Example Job payload

Worker version N+1 must still understand queued payload N or provide migration/terminal handling.

---

# 219. Deprecated output

Deprecation period only needed for independently versioned/external consumers.

---

# 220. No compatibility with bugs as hidden requirement

If old contract leaked private data, security fix may intentionally break it immediately.

---

# 221. Enumeration evolution

Consumers MUST handle unknown value safely where forward compatibility is desired.

---

# 222. UI pattern

Unknown operational status:

```text id="fbdh4r"
Unknown status
```

rather than crash.

---

# 223. But business workflow transitions

Server never accepts unknown stage from client.

---

# 224. Contract source definitions

Recommended project structure conceptually:

```text id="4v8xtl"
src/
  application/
    contracts/
  projections/
  validation/
```

Exact filesystem finalized implementation docs.

---

# 225. Shared TypeScript types

May be shared between server/client **only if they are explicitly client-safe DTOs**.

---

# 226. CTR-INV-015 — Do Not Share Domain Entity Type Just Because TypeScript Allows It

---

# 227. Sensitive contract separation

Example:

```text id="kug797"
PublicContactDTO
AdminContactDTO
```

must remain separate types.

---

# 228. Type generation

Schema validators may generate/infer TypeScript types.

Good if:

```text id="wxiqrm"
schema = contract
```

and does not originate from DB schema blindly.

---

# 229. OpenAPI

May be generated later for Route Handler external contracts.

Not required for internal Server Actions baseline.

---

# 230. Documentation

Every externally consumed endpoint SHOULD document:

```text id="tbjyqr"
method
path
auth
input
result
errors
idempotency
rate limits
```

---

# 231. Server Action documentation

High-impact action SHOULD document:

```text id="5iqrcu"
input
principal
expectedVersion
domain command
success
errors
side effects
```

---

# 232. Example — Publish Questionnaire

```text id="ntnmju"
PublishQuestionnaireInput {
  questionnaireId: UUID
  expectedDraftVersion: number
  idempotencyKey: string
}
```

Result:

```text id="dkru2o"
PublishQuestionnaireResult {
  questionnaireId
  revisionId
  revisionNumber
  artifact: {
    state
  }
}
```

---

# 233. Why no selected entities in Publish input

Selections already belong authoritative Draft.

Publish reloads Draft server-side.

---

# 234. Example — Set Primary Close-Up

Input:

```text id="y06b1d"
{
  portfolioItemId,
  expectedProfilePortfolioVersion
}
```

or appropriate aggregate version.

---

# 235. Result

```text id="k4kfri"
{
  primaryItemId,
  version
}
```

---

# 236. Example — Confirm Grid Cell

```text id="k2y38e"
ConfirmGridCellInput {
  gridDraftId
  cellId
  expectedCellVersion
}
```

No:

```text id="rl0ndd"
confirmedBy
confirmedAt
```

from client.

---

# 237. Example — Finalize Grid

```text id="y1um3l"
FinalizeEmotionalGridInput {
  gridId
  draftId
  expectedDraftVersion
  idempotencyKey
}
```

Result:

```text id="b8sb39"
{
  gridRevisionId,
  revisionNumber,
  renderState
}
```

---

# 238. Example — Update Contact Visibility

```text id="f8352k"
UpdateContactVisibilityInput {
  contactId
  expectedVersion
  showOnPublicSite
  allowInAdminQuestionnaires
  allowInPublicQuestionnaireBuilder
}
```

---

# 239. No combined visibility enum

Normative.

---

# 240. Example — Generate Builder

```text id="d2psnr"
GenerateBuilderInput {
  sessionId
  expectedVersion
  idempotencyKey
}
```

---

# 241. Result

```text id="i6es7l"
{
  snapshotId,
  generationNumber,
  artifact: {
    id,
    state
  }
}
```

---

# 242. Example — Run Casting Analysis

```text id="jsohtl"
RunCastingAnalysisInput {
  castingId
  expectedCastingVersion
  idempotencyKey
}
```

No prompt/model supplied by normal UI.

---

# 243. Result

```text id="mf9x51"
{
  analysisRequestId,
  state: "queued" | "processing"
}
```

---

# 244. Analysis Query result

Later:

```text id="9n3dqa"
{
  currentAnalysisRevision,
  requirements,
  recommendations
}
```

---

# 245. Example — Confirm Casting Requirement

```text id="00zo1e"
ConfirmCastingRequirementInput {
  requirementId
  expectedVersion
  confirmedValue
}
```

---

# 246. Source extraction is not resubmitted

Server loads it.

---

# 247. Example — Create Project from Booked

```text id="q8xc73"
CreateProjectDraftFromBookedOpportunityInput {
  opportunityId
  expectedVersion
  idempotencyKey
}
```

---

# 248. Result

```text id="w0cdvz"
{
  projectId,
  projectVersion,
  sourceOpportunityId
}
```

---

# 249. Example — Generate BB Draft

```text id="xxv0ts"
GenerateBBDraftInput {
  taskType
  contextEntityRef?
  language
  tone?
  length?
  instruction?
  idempotencyKey?
}
```

---

# 250. `instruction`

Bounded free-form human guidance.

It cannot override application policies.

---

# 251. Example — Apply BB Draft

```text id="hfaxlk"
ApplyBBDraftInput {
  draftId
  expectedDraftStatus
  targetEntityRef
  expectedTargetVersion
}
```

---

# 252. Example — Publish Theme

```text id="jcq329"
PublishThemeInput {
  themeId
  draftId
  expectedDraftVersion
  idempotencyKey
}
```

---

# 253. Result

```text id="u6c94j"
{
  themeRevisionId,
  revisionNumber
}
```

---

# 254. Example — Schedule Social Post

```text id="n0rtit"
ScheduleSocialPostInput {
  postId
  expectedVersion
  targetAccountIds[]
  scheduledAt
  timezone
}
```

---

# 255. Schedule result

```text id="lmmf0i"
{
  postId
  approvedVersion
  targets[]
}
```

---

# 256. Contract validation responsibilities

Transport validator verifies:

```text id="qcdvw2"
types
required
format
bounded arrays
known enums
```

Application/Domain verifies:

```text id="l0ghtq"
existence
ownership
state
eligibility
visibility
transition
concurrency
```

---

# 257. CTR-INV-016 — Client DTO Cannot Prove Eligibility

Example:

```text id="wru4hk"
isEligible: true
```

submitted by browser has no authority.

---

# 258. Output determinism

Projection should provide stable semantics for same authoritative state/context.

---

# 259. Field ordering in JSON

Must not be relied upon.

---

# 260. Collection ordering

Explicitly contractually meaningful.

---

# 261. HTML/PDF consistency

DocumentModel DTO is renderer-neutral internal contract.

---

# 262. Document model contract

Should include:

```text id="s053el"
metadata
sections
ordered items
approved media references
links
QR intent
locale
footer
```

---

# 263. It MUST NOT include

```text id="6g747h"
React component
DOM
CSS selectors
database row
Admin object
```

---

# 264. Questionnaire HTML and PDF use same semantic document contract

Normative.

---

# 265. Artifact contract

Artifact identity is independent from delivery URL.

---

# 266. Why

URL can change infrastructure/CDN routing while artifact remains exact historical binary.

---

# 267. Access-scoped download URL

May be:

```text id="39l6d8"
stable public
authenticated Admin
temporary/token route
```

depending access class.

---

# 268. URLs may expire

If temporary authenticated delivery is used, DTO must not imply artifact itself expires.

---

# 269. QR exception

QR requires stable target URL.

Do not encode short-lived artifact download URL.

---

# 270. Nullability quality rule

Every nullable output must have documented reason.

---

# 271. Bad nullable design

```text id="invl3r"
roleName?: null for unknown reason
```

---

# 272. Good

Document:

```text id="86jws3"
roleName is optional because Casting may be project-level.
```

---

# 273. Optional arrays

Prefer:

```text id="8ywi64"
skills: []
```

rather than missing `skills` when section exists but contains none.

---

# 274. Hidden section

May be absent entirely.

---

# 275. Empty vs hidden

Important distinction:

```text id="2o3rlg"
[]
```

means context exposes section but no entries.

Absent means section not part of Projection contract/current visibility.

---

# 276. Contract accessibility

Public-facing DTO should provide semantic text for images/links needed by accessible rendering.

---

# 277. Image alt

May be approved/descriptive product text.

Must not be AI-invented sensitive visual interpretation.

---

# 278. Link label

Should be meaningful enough for accessible rendering.

---

# 279. Contract security

No DTO may contain credentials.

---

# 280. CTR-SEC-001

Forbidden in ordinary API DTO:

```text id="rmovuc"
password hash
OAuth access token
refresh token
provider API key
session secret
storage credential
```

---

# 281. CTR-SEC-002

Admin DTO does not mean secrets are safe to expose.

---

# 282. CTR-SEC-003

Raw token-scoped access token should appear only at issuance where required and then be handled carefully.

---

# 283. Secret token issuance

If system must present a newly generated secret once:

contract should explicitly mark:

```text id="xktd0h"
oneTimeSecret
```

and never expose it again through normal Query.

---

# 284. No token in URLs returned to logs unnecessarily

Tokenized public URL may necessarily contain token; logging middleware must redact.

---

# 285. Contract testing

Required categories:

```text id="lgtewt"
schema
serialization
privacy
compatibility
pagination
enum handling
idempotency
historical exactness
```

---

# 286. Contract snapshot tests

Useful for stable Public/Admin DTO shapes.

But snapshot tests cannot replace semantic privacy assertions.

---

# 287. Explicit privacy test

Example:

```text id="qpv0b7"
expect(publicDTO).not.toHaveProperty("storageLocator")
```

---

# 288. Consumer-driven tests

If future external consumer/app appears, add consumer contract testing.

Not required baseline.

---

# 289. CTR-AP-001

**Reuse database row as DTO**

---

# 290. CTR-AP-002

**One ProjectDTO for Public/Admin/Builder**

---

# 291. CTR-AP-003

**Hidden fields sent as values + `visible=false`**

---

# 292. CTR-AP-004

**Server sends BigInt directly in JSON**

---

# 293. CTR-AP-005

**Calendar shooting date serialized as UTC instant**

---

# 294. CTR-AP-006

**Height source represented only as formatted `"172 см"`**

---

# 295. CTR-AP-007

**Native language encoded as CEFR C2**

---

# 296. CTR-AP-008

**Public Builder submits full source object instead of ID/selection**

---

# 297. CTR-AP-009

**Browser supplies `publishedBy`**

---

# 298. CTR-AP-010

**Mutation response always returns entire aggregate**

---

# 299. CTR-AP-011

**One giant global API response envelope**

---

# 300. CTR-AP-012

**Cursor documented so client parses internal DB fields**

---

# 301. CTR-AP-013

**Public Search caller chooses Admin scope**

---

# 302. CTR-AP-014

**Public media DTO exposes local filename/storage locator**

---

# 303. CTR-AP-015

**Raw Revision snapshot sent to Public renderer without Projection**

---

# 304. CTR-AP-016

**Grid coordinates mix percentages/pixels without contract**

---

# 305. CTR-AP-017

**Casting `value` overwrites distinction between AI extraction and Human confirmation**

---

# 306. CTR-AP-018

**Async PDF endpoint returns raw job lock state**

---

# 307. CTR-AP-019

**AI UI sends provider model/system prompt**

---

# 308. CTR-AP-020

**Theme Public DTO exposes AI prompt/history**

---

# 309. CTR-AP-021

**WhatsApp delivery DTO exposes provider credentials**

---

# 310. CTR-AP-022

**Unknown enum causes client crash**

---

# 311. CTR-AP-023

**Breaking persisted job contract with no payload version support**

---

# 312. CTR-AP-024

**Optional field omitted and null used interchangeably without semantics**

---

# 313. CTR-AP-025

**Base64 large video inside JSON API**

---

# 314. Contract quality gate

Перед implementation MUST быть определены:

- [ ] DTO naming convention;
- [ ] camelCase wire convention;
- [ ] UUID serialization;
- [ ] version serialization;
- [ ] timestamp format;
- [ ] date-only format;
- [ ] locale format;
- [ ] URL semantics;
- [ ] measurement units;
- [ ] null vs absent semantics;
- [ ] ordered collection semantics;
- [ ] pagination models;
- [ ] filter allowlists;
- [ ] sorting allowlists;
- [ ] Public/Admin/Builder DTO separation;
- [ ] media reference DTO;
- [ ] artifact DTO;
- [ ] readiness DTO;
- [ ] Feedback DTO;
- [ ] Casting AI/Human distinction;
- [ ] Opportunity transition contract;
- [ ] async operation contract;
- [ ] upload/download contract;
- [ ] error baseline;
- [ ] persisted schema versioning;
- [ ] backward compatibility rules;
- [ ] privacy contract tests.

---

# 315. E2E-CTR-001 — BigInt safety

Entity with high DB `version bigint` serializes according to approved JS-safe contract without runtime serialization failure.

---

# 316. E2E-CTR-002 — Date-only

Emotional shooting date remains:

```text id="45wz5l"
2026-07-27
```

for users in different timezones.

---

# 317. E2E-CTR-003 — Hidden Contact

Public DTO contains no hidden Contact property/value.

---

# 318. E2E-CTR-004 — Builder-only Project

Public Profile DTO absent.

Builder eligible DTO present.

---

# 319. E2E-CTR-005 — Storage locator

No Public/Admin ordinary media DTO exposes storage locator.

---

# 320. E2E-CTR-006 — Native language

Native language transport semantics remain `native`, not artificial `C2`.

---

# 321. E2E-CTR-007 — Pagination bound

Request `limit=100000`.

Server rejects/clamps according to documented policy.

---

# 322. E2E-CTR-008 — Cursor opacity

Client can roundtrip server cursor without understanding contents.

---

# 323. E2E-CTR-009 — Stable tie ordering

Items with identical timestamp paginate without duplicate/missing rows through ID tie-breaker.

---

# 324. E2E-CTR-010 — Reorder duplicates

Reorder payload with duplicate ID rejected.

---

# 325. E2E-CTR-011 — Questionnaire emotional DTO

Contains Grid composite + shooting date + portfolio link.

No individual source photo list.

---

# 326. E2E-CTR-012 — Builder tampering

Builder submits visible label/value instead of source identity to alter professional fact.

Server ignores client value and reloads Source.

---

# 327. E2E-CTR-013 — Casting distinction

DTO simultaneously preserves:

```text id="i3t9be"
extractedValue = B1
confirmedValue = B2
humanDecisionState = modified
```

---

# 328. E2E-CTR-014 — Async PDF

Generate returns Artifact ID/state, not raw BackgroundJob payload.

---

# 329. E2E-CTR-015 — Artifact URL state

Failed/queued artifact DTO has no downloadable URL.

---

# 330. E2E-CTR-016 — Public Feedback result

Response exposes generated Feedback ID/time but no Admin workflow/internal notification details.

---

# 331. E2E-CTR-017 — BB context

Normal GenerateBBDraft contract has no provider model/system prompt field.

---

# 332. E2E-CTR-018 — Theme Public DTO

No AI provider/model/prompt included.

---

# 333. E2E-CTR-019 — Social approved version

Schedule result includes/fixes exact approved post version.

---

# 334. E2E-CTR-020 — Error field mapping

Invalid title maps to stable field path/code without raw DB exception.

---

# 335. E2E-CTR-021 — Unknown status

New safe operational enum returned by server does not expose private data or catastrophically crash fallback UI.

---

# 336. E2E-CTR-022 — Historical Questionnaire

Historical DTO references exact Revision, not current Profile version.

---

# 337. E2E-CTR-023 — Token artifact

Token-scoped artifact DTO never exposes Admin route/storage path.

---

# 338. E2E-CTR-024 — Serialization

Every public projection can be serialized/deserialized without framework-specific DB/class objects.

---

# 339. E2E-CTR-025 — DTO contract separation

Type/static/integration test proves Admin Contact fields cannot accidentally be assigned to `PublicContactDTO` serializer without explicit mapping.

---

# 340. Contract traceability

Canonical:

```text id="ok7zke"
DOMAIN INTENT
     ↓
APPLICATION COMMAND / QUERY
     ↓
INPUT CONTRACT
     ↓
VALIDATION
     ↓
DOMAIN
     ↓
PROJECTION
     ↓
OUTPUT CONTRACT
```

---

# 341. Public read traceability

```text id="hu8ecb"
Source
 ↓
Visibility Policy
 ↓
Public Projection
 ↓
Public DTO
 ↓
Server Component / HTTP
```

---

# 342. Builder traceability

```text id="j2utgh"
Eligible Source
 ↓
BuilderEligibleItemDTO
 ↓
User selects ID
 ↓
Command
 ↓
Server reload/revalidate
 ↓
GenerationSnapshot
 ↓
Document DTO
```

---

# 343. Mutation traceability

```text id="lvwo4b"
Form
 ↓
CommandInput
 ↓
Transport Validation
 ↓
Application Command
 ↓
Domain Validation
 ↓
Commit
 ↓
CommandResult
```

---

# 344. Async traceability

```text id="71eq42"
Command
 ↓
Persist exact Source/Snapshot
 ↓
Create Artifact/Generation record
 ↓
Accepted DTO
 ↓
Worker
 ↓
Artifact status
 ↓
Query DTO
```

---

# 345. Contract compliance criteria

Реализация соответствует DOC-101, если:

1. DB/ORM rows are not API contracts;
2. Command input and Projection output are separate types;
3. Public/Admin/Builder DTOs are context-specific;
4. hidden data is absent rather than client-filtered;
5. identifiers use canonical representation;
6. slugs do not replace DB identity;
7. opaque tokens are not ordinary IDs;
8. version uses one consistent JS-safe representation;
9. entity version and revision number remain distinct;
10. timestamps use timezone-explicit ISO representation;
11. date-only values remain `YYYY-MM-DD`;
12. locales use normalized approved codes;
13. storage locators never become URLs;
14. measurements carry defined units;
15. hidden vs null vs omitted semantics are documented;
16. meaningful array ordering is explicit;
17. pagination is bounded;
18. cursors are opaque;
19. filters/sorts are allowlisted;
20. Search scope is server-controlled;
21. Public media DTOs exclude private technical metadata;
22. visibility policies remain three independent booleans;
23. Emotional questionnaire DTO cannot expose individual emotional source photos;
24. Builder sends identities/selections rather than professional source values;
25. Builder Session DTO excludes hidden inventory;
26. Builder Generation returns exact Snapshot/Artifact identity;
27. Feedback Public input cannot control workflow/admin fields;
28. Casting DTO separates AI extraction from Human confirmation;
29. `NO_CONFIRMED_DATA` remains explicit;
30. Opportunity transition has dedicated typed contract;
31. AI generation contract expresses task intent, not provider internals;
32. BB Apply includes target concurrency information;
33. Theme Public DTO excludes AI/Admin metadata;
34. Notification external contract is separate from Admin Notification DTO;
35. Social scheduling binds exact approved version;
36. async operations expose domain/artifact status rather than job internals;
37. file transport avoids large JSON/base64;
38. output contains no secret credentials;
39. persisted payload contracts are versioned;
40. durable old payload versions remain processable or explicitly migrated;
41. internal same-deployment contracts are not over-versioned unnecessarily;
42. breaking external/persisted changes follow compatibility policy;
43. DTO serialization uses transport-safe primitives;
44. contract privacy is covered by explicit tests;
45. contract evolution cannot reintroduce private-field leakage.

---

# 346. Финальная доктрина

> **Контракты платформы должны отражать бизнес-намерение, security context и projection semantics, а не форму таблиц PostgreSQL. Commands принимают только разрешённые пользовательские намерения; Queries возвращают только заранее авторизованные Projection DTO; Public, Admin и Builder никогда не делят небезопасный универсальный DTO. Версии, даты, локали, ссылки, единицы измерения, pagination и async processing имеют единый сериализуемый контракт, а persisted integration payloads дополнительно получают schema version. Это позволяет менять ORM, базу, renderer или provider без нарушения бизнес-смысла и без утечки внутренних/private полей через транспортный слой.**