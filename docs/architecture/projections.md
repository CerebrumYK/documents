# ARCHITECTURE PROJECTIONS

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная архитектура read models, public projections и безопасных представлений данных

**Целевой файл:** `docs/architecture/projections.md`  
**Документ:** DOC-073  
**Статус:** ✅ Completed  
**Тип:** Architecture / Projections / Read Models / Privacy Boundaries

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/personas.md`
- `docs/customer-journey/conversion-funnel.md`
- `docs/ux/public-navigation.md`
- `docs/ux/admin-navigation.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/public-questionnaire-builder.md`
- `docs/ux/content-hierarchy.md`
- `docs/domain/domain-model.md`
- `docs/domain/entity-relationships.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions-and-history.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/components.md`
- `docs/architecture/data-flows.md`

---

# 1. Назначение документа

Настоящий документ определяет архитектуру всех значимых **read-side projections** продукта.

Он фиксирует:

1. что такое Projection;
2. чем Projection отличается от Source Entity;
3. Public Data Aggregation Layer;
4. Public Profile Projection;
5. Casting Quick View;
6. Portfolio Projection;
7. Emotional Projection;
8. Project/Role Projection;
9. Questionnaire projections;
10. Public Builder Projection;
11. Admin Dashboard read model;
12. Inbox/Casting/Opportunity projections;
13. Search Projection;
14. Historical Revision Projection;
15. PDF/Document Projection;
16. Analytics projections;
17. VOP/System Health projections;
18. permission-aware DTO construction;
19. projection caching/invalidation;
20. consistency requirements;
21. projection schema/versioning;
22. запреты на writable projections.

---

# 2. Главный принцип

> **Projection — это контекстно разрешённое представление авторитетных данных для конкретной пользовательской задачи. Projection может объединять, фильтровать, сортировать и форматировать Source Data, но не имеет собственной профессиональной истины.**

---

# 3. Projection IDs

Используется:

```text
PRJCT-*
```

Категории:

```text
PRJCT-PUB-*   Public
PRJCT-PQB-*   Public Builder
PRJCT-ADM-*   Admin
PRJCT-QST-*   Questionnaire
PRJCT-CST-*   Casting
PRJCT-SRCH-*  Search
PRJCT-HIST-*  Historical
PRJCT-ANL-*   Analytics
PRJCT-VOP-*   Operator/System
```

---

# 4. Что является Projection

Examples:

```text
Public Profile
Casting Quick View
Public Portfolio
Questionnaire HTML
Builder Eligible Content
Admin Dashboard Card
Search Result
Opportunity Pipeline Board
```

---

# 5. Что Projection НЕ является

Projection MUST NOT быть:

```text
independent writable business entity
duplicate professional profile
security bypass
permanent copy of mutable Master Data
client-side privacy filter
```

---

# 6. Projection ≠ Snapshot

Projection:

```text
normally reflects current authoritative state
```

Snapshot:

```text
freezes state at a point in time
```

---

# 7. Projection ≠ Revision

A Revision is immutable historical business/document version.

Projection may render:

```text
current Master Data
or
exact Revision
```

depending context.

---

# 8. Projection ≠ Cache

Projection defines semantics.

Cache merely stores a reusable computed projection representation.

---

# 9. Projection ≠ DTO

DTO is transport representation.

Projection defines the conceptual read model from which DTO may be serialized.

---

# 10. Canonical Projection Pipeline

```text
AUTHORITATIVE SOURCES
        ↓
Lifecycle Eligibility
        ↓
Authorization / Visibility
        ↓
Context Policy
        ↓
Locale Resolution
        ↓
Projection Composition
        ↓
Content Hierarchy / Ordering
        ↓
SAFE DTO
        ↓
SSR / Client / Renderer / Search
```

---

# 11. PRJCT-INV-001 — Authorization Before Projection Output

Private data MUST be excluded before final DTO leaves trusted server boundary.

---

# 12. PRJCT-INV-002 — No Client Privacy Filtering

Prohibited:

```text
server returns all contacts
browser hides private contact
```

---

# 13. PRJCT-INV-003 — Projection Is Read-Only

A Projection MAY expose actions/links.

Any write must route to owning Application Service.

---

# 14. PRJCT-INV-004 — No Duplicate Truth

Projection MUST not contain independently editable copies of:

```text
Profile facts
Skill levels
Contact values
Project titles
Opportunity stages
```

---

# 15. PRJCT-INV-005 — Same Fact Can Have Different Projection Eligibility

Example:

```text
Contact X

Public Site:
false

Prepared Questionnaire:
true

Public Builder:
false

Admin:
true
```

This is valid.

---

# 16. Public Data Aggregation Layer

Canonical component:

```text
Public Data Aggregation Layer
```

Responsibilities:

```text
collect current eligible entities
apply public lifecycle rules
apply public visibility
resolve locale
construct safe public model
exclude private/admin fields
```

---

# 17. Public Aggregation Layer Does Not Write

No mutations.

---

# 18. Public Aggregation Inputs

Potential inputs:

```text
ActorProfile
Portfolio
Emotional
Projects
Roles
Training
Achievements
Skills
Languages
Professional Links
Contacts
Published Questionnaires
Theme
```

---

# 19. Public Aggregation Output

Only fields required by public professional experience.

Technical/admin metadata SHOULD be omitted.

---

# 20. Example Excluded Fields

Public Projection MUST NOT expose ordinary internal:

```text
database IDs unless public-safe
internal storage paths
admin notes
processing retry details
AI source snapshots
audit metadata
private visibility flags
OAuth identifiers/secrets
```

---

# 21. Public Identifiers

Public DTO SHOULD use:

```text
slug
opaque public ID
public URL
```

where possible.

Internal UUID MAY be exposed only if intentionally safe and useful.

---

# 22. PRJCT-PUB-001 — Public Actor Identity Projection

Canonical minimal identity:

```text
professional name
professional role/title
primary professional image
location/base
core published parameters
languages summary
key skills summary
locale
canonical profile URL
```

---

# 23. Identity Projection Availability

Actor identity SHOULD remain available across deep public pages:

```text
Project
Emotional
Questionnaire
Portfolio
```

without duplicating Source Data.

---

# 24. PRJCT-PUB-002 — Public Home Projection

Home Projection follows DOC-048 semantic order:

```text
1 Hero / Identity
2 Quick Facts
3 Primary Portfolio
4 Video Intro
5 Emotional Range
6 Selected Projects
7 Skills / Languages
8 Training / Achievements
9 Questionnaire
10 Contact / Casting CTA
```

---

# 25. Home Projection Is Task-Oriented

It does NOT serialize all Profile entities.

---

# 26. Hero Projection

MUST prioritize:

```text
name
professional identity
main visual
quick facts
Video CTA
Questionnaire CTA
Contact CTA
```

---

# 27. Hero Must Not Contain

By default:

```text
full biography
all skills
all achievements
all projects
private contact information
```

---

# 28. Quick Facts Projection

Reads confirmed structured Master Data.

Never derives professional facts from Biography at runtime.

---

# 29. Selected Projects Projection

Uses curated:

```text
featured
display order
published state
```

not:

```text
latest uploaded automatically
analytics popularity automatically
AI score
```

---

# 30. PRJCT-PUB-003 — Casting Quick View

Casting Quick View is a **professional decision projection**.

Purpose:

> allow casting professional to assess basic suitability without navigating all pages.

---

# 31. Casting Quick View Inputs

Canonical:

```text
Actor identity
Primary Close-Up
Primary Full Body
essential appearance/professional parameters
location
languages
key skills
Video Intro
Primary Questionnaire access
professional CTA
```

---

# 32. Casting Quick View Does Not Own Data

It contains no manual “Quick View profile” editor.

---

# 33. Quick View Missing Data

If Source data is absent:

```text
do not invent
do not infer from photos
```

Either omit or show appropriate unavailable state.

---

# 34. Casting Quick View Performance

Should be a compact projection suitable for rapid SSR/mobile retrieval.

---

# 35. PRJCT-PUB-004 — Public Portfolio Projection

Inputs:

```text
published PortfolioItems
eligible Media derivatives
professional categories
Primary markers
display order
localized captions
```

---

# 36. Portfolio Projection Groups

Canonical category groupings MAY include:

```text
Close-Up
Full Body
Waist
Profile
Three-Quarter
Staged
Other approved
```

---

# 37. Primary Close-Up and Full Body

Projection MUST preserve these semantic roles distinctly.

---

# 38. Project Images Do Not Enter Main Portfolio Automatically

A RoleMedia item appears in Portfolio only if an actual PortfolioItem relation/classification exists.

---

# 39. Portfolio Media DTO

Public media item should expose only necessary:

```text
public derivative URL
dimensions/aspect info
alt/caption
category
primary marker
stable lightbox identity
```

---

# 40. Original Storage Details Excluded

No:

```text
/data/... path
checksum
internal processing IDs
```

unless specific public need exists.

---

# 41. PRJCT-PUB-005 — Emotional Range Projection

Inputs:

```text
published EmotionalSession
shooting_date
published finalized EmotionalGrid revision
primary grid marker
public full emotional route
```

---

# 42. Emotional Overview Ordering

Canonical:

```text
shooting date
Primary Grid
other approved grids
full emotional portfolio continuation
```

---

# 43. Emotional Composite Integrity

Projection MUST treat approved composite geometry as fixed semantic artifact.

Responsive UI scales it; does not reorder cells.

---

# 44. Individual Emotional Sources

May appear in full Emotional Portfolio if business rules permit.

They MUST NOT replace composite in Questionnaire projection.

---

# 45. PRJCT-PUB-006 — Project Listing Projection

Each card SHOULD contain only decision-relevant:

```text
title
role
type/year/context
approved key image
short description where useful
```

---

# 46. Project Detail Projection

May include:

```text
Project facts
Role
RoleMedia
Performance dates
Achievements relation
professional CTA
```

---

# 47. Archive Exclusion

Archived Project is absent from active default Public Projection unless historical/archive surface explicitly exists.

---

# 48. Role Parent Context

A Role projection MUST preserve parent Project identity.

---

# 49. PRJCT-PUB-007 — Skills & Languages Projection

Uses confirmed:

```text
ActorSkill
ActorLanguageProficiency
```

---

# 50. Skills Projection

May group by category and importance.

Must preserve actual stored 5-level semantics.

---

# 51. Language Projection

Must preserve explicit:

```text
language
CEFR/native status where applicable
```

---

# 52. No Visual Guessing

Projection cannot derive language proficiency or skill level from AI/media.

---

# 53. PRJCT-PUB-008 — Training & Achievements Projection

Separate from Projects.

---

# 54. Training Projection

May contain:

```text
course/program
institution
date
compact description
approved evidence
```

---

# 55. Achievements Projection

May contain:

```text
title
issuer/context
date
project relation where applicable
```

---

# 56. PRJCT-PUB-009 — Professional Media & Links Projection

Normalizes:

```text
Video Intro
Showreel
Video
Audio
External professional links
```

---

# 57. Link DTO

Potential fields:

```text
description
canonical public URL
media/link type
playback model
QR-capable flag where relevant
```

---

# 58. Link Health Public Behaviour

Temporary reachability failure MAY produce degraded player/link state.

Internal diagnostic details remain Admin-only.

---

# 59. PRJCT-PUB-010 — Contact Projection

Only Contacts with:

```text
show_on_public_site = true
```

and otherwise eligible current lifecycle.

---

# 60. Contact Projection May Expose

```text
label
role/relationship
phone
email
WhatsApp link
approved social contact link
```

only as configured.

---

# 61. Hidden Contact

Must not exist:

```text
in HTML
hydration payload
client JSON
preloaded API data
```

---

# 62. Guardian Contact

Guardian/parent relationship does not imply public inclusion.

---

# 63. PRJCT-PUB-011 — Questionnaire Hub Projection

Canonical hierarchy:

```text
Primary Recommended Questionnaire
Download
Alternatives
Customize This Version
Build for Casting
Professional CTA
```

---

# 64. Questionnaire Hub Sources

Uses:

```text
QuestionnaireDefinition
current published Revision
primary marker
public availability
locale/artifact availability
Builder capability state
```

---

# 65. Draft Questionnaire Exclusion

Admin Drafts MUST NOT appear.

---

# 66. PRJCT-QST-001 — Current Prepared Questionnaire Projection

For public current route:

```text
logical Questionnaire
→ current_published_revision_id
→ exact immutable Revision Projection
```

---

# 67. No Live Source Backfill

If historical/current published Revision lacks a field, renderer MUST NOT silently fill it from current Profile.

---

# 68. PRJCT-QST-002 — Questionnaire Revision Projection

Canonical output sections:

```text
identification
Primary Close-Up
Primary Full Body
parameters
languages
skills
video
emotional composite
projects/roles
training/achievements
additional media/links
contacts
official profile link
```

according to exact Revision config.

---

# 69. Required Questionnaire Content

Every generated actor questionnaire MUST have:

```text
identification
eligible/published Main Portfolio Close-Up
eligible/published Main Portfolio Full Body
official Profile link
```

as defined by master TЗ.

---

# 70. Historical Revision Projection

Reads only immutable snapshot/version-bound assets.

---

# 71. PRJCT-QST-003 — Questionnaire Admin Draft Projection

Admin projection combines:

```text
current Draft
current source references
current readiness
published Revision comparison
```

---

# 72. Admin Draft Projection Must Distinguish

```text
Draft values
Current Source values
Current Published Revision values
```

where they differ.

---

# 73. Example

```text
Current Profile:
English C1

Current published questionnaire:
English B2

Draft:
English C1
```

UI MUST not blur these three states.

---

# 74. PRJCT-QST-004 — Questionnaire Readiness Projection

Structured:

```text
status:
 READY | ATTENTION | BLOCKED

blockers[]
warnings[]
validated_at
validation_version
```

---

# 75. Readiness Projection Is Derived

Never manually edited.

---

# 76. Readiness Blocker Example

```text
Primary Full Body missing
```

must identify exact action/context rather than numeric score.

---

# 77. PRJCT-QST-005 — Questionnaire PDF Render Projection

Document Model is a specialized projection over immutable Revision/Snapshot.

---

# 78. PDF Projection Must Be Semantic

It must not simply screenshot browser DOM.

---

# 79. PRJCT-PQB-001 — Public Builder Eligibility Projection

This is a separate critical projection.

Purpose:

> expose only data that the visitor may include in a temporary custom questionnaire.

---

# 80. Builder Eligibility Formula

Conceptually:

```text
belongs_to_profile
AND
active/available
AND
allow_in_public_questionnaire_builder = true
AND
template/context rules
AND
server-side policy
```

---

# 81. Builder Projection MAY Include Builder-Only Data

An item MAY have:

```text
show_on_public_site = false
allow_in_public_questionnaire_builder = true
```

if explicitly approved.

Therefore Builder Projection cannot be derived only from Public Site Projection.

---

# 82. Builder Projection Must Never Include

```text
Admin-only data
private Contacts
Draft content
archived items
Casting internal data
AI internal outputs
```

unless explicitly permitted by Builder policy.

---

# 83. Builder Projection Structure

Potential:

```text
profile identification
required Primary Close-Up
required Primary Full Body
appearance parameters
Portfolio options
skills
languages
Emotional Grid options
projects
roles
training
achievements
video/audio/links
contacts
```

all filtered.

---

# 84. Builder Item DTO

Should contain only information required to choose item.

Example Project:

```text
public-safe ID
title
role
thumbnail
compact metadata
selected eligibility
```

---

# 85. Builder Does Not Need Permission Flags

Do not expose raw:

```text
allow_in_public_questionnaire_builder
show_on_public_site
```

unless UX requires explanatory state.

Prefer sending only eligible data.

---

# 86. PRJCT-PQB-002 — Builder Session Projection

Contains:

```text
session identity
template
locale
current valid selection
temporarily invalid/revoked selection indicators
casting/project/role labels
expiration
generation status
```

---

# 87. Builder Session Projection Does Not Contain Master Copies

Selections reference eligible identities/config.

---

# 88. PRJCT-PQB-003 — Builder Preview Projection

Built from:

```text
current Session
+
current server-authorized Builder source
```

before immutable generation snapshot.

---

# 89. Preview Is Non-Authoritative

Generation MUST revalidate again.

---

# 90. PRJCT-PQB-004 — Builder Generation Projection

Once GenerationSnapshot exists:

PDF/finished document renders from exact Snapshot.

No live Builder source joins.

---

# 91. PRJCT-ADM-001 — Admin Dashboard Projection

Dashboard answers:

> **Что требует внимания сейчас?**

Canonical ordering:

```text
1 Action Required
2 Deadlines
3 New Professional Inquiries
4 Pending Review
5 Content Health
6 System Health
7 Recently Resolved
```

---

# 92. Dashboard Inputs

Potential:

```text
Feedback
Casting
Opportunity
VOPObservation
failed jobs
stale content
notification failure
readiness issues
```

---

# 93. Dashboard Does Not Own Status

Card:

```text
Opportunity — Callback
```

reads Opportunity current stage.

It does not maintain `dashboard_stage`.

---

# 94. Dashboard Card Model

Should contain:

```text
entity identity
category
priority
current state
why attention needed
deadline/age
primary action
route
```

---

# 95. Dashboard Priority

Should be deterministic/business-driven.

Not AI ranking alone.

---

# 96. Dashboard Vanity Metrics

Analytics charts MUST NOT displace urgent operational items at top.

---

# 97. PRJCT-ADM-002 — Media Usage Projection

For Media detail:

```text
Used In:
Portfolio
Emotional Session
Role
Questionnaire Draft
Social Post
```

---

# 98. Media Usage Projection

Computed from real relations.

Not a manually maintained list.

---

# 99. PRJCT-ADM-003 — Dependency Impact Projection

Used before:

```text
archive
delete
visibility change
URL change
```

---

# 100. Dependency Impact Categories

Canonical:

```text
ACTIVE_PUBLIC
CURRENT_DRAFT
DERIVED
HISTORICAL
OPERATIONAL
```

---

# 101. Historical Dependency

Must be identified separately because it usually does not block ordinary archive but affects retention.

---

# 102. PRJCT-ADM-004 — Questionnaire List Projection

Each Questionnaire entry may expose:

```text
name
type
Primary marker
current published revision
Draft status
readiness
locale availability
last published date
Casting context if applicable
```

---

# 103. Primary vs Published

Projection MUST distinguish:

```text
Primary
Published
Draft
Archived
```

They are not one status.

---

# 104. PRJCT-ADM-005 — Inbox Projection

Feedback list/cards:

```text
type
sender
organization
time
status
responsible
next action
source context
linked Casting
```

---

# 105. Inbox Must Not Be Notification Projection

Notification status is separate.

---

# 106. PRJCT-ADM-006 — Casting Workspace Projection

Composite read model:

```text
Casting summary
Source
Source assets
Analysis history
current Analysis
Confirmed Requirements
Profile Match
Recommendations
Questionnaire context
Opportunity
Feedback provenance
History
```

---

# 107. Source Before AI

Projection ordering MUST preserve:

```text
Original Source
→ AI Extraction
→ Human Confirmation
→ Profile Match
→ Recommendation
```

especially on mobile.

---

# 108. AI Output Visual Separation

Projection model should expose explicit source types/statuses so UI can label:

```text
Source
AI Extracted
Human Confirmed
```

---

# 109. PRJCT-CST-001 — Profile Match Projection

Derived from:

```text
confirmed Casting Requirement
+
selected confirmed Profile facts
```

---

# 110. Match Result DTO

Should contain:

```text
requirement
match_state
supporting profile fact/evidence
reason
missing data state
```

---

# 111. No Confirmed Data

Must remain distinct:

```text
NO_CONFIRMED_DATA
```

not visually converted to failure.

---

# 112. PRJCT-CST-002 — Casting Questionnaire Recommendation Projection

May propose:

```text
which Profile evidence is relevant
which questionnaire template
missing material
```

Read-only until Human action.

---

# 113. PRJCT-ADM-007 — Opportunity Pipeline Projection

Reads authoritative Opportunity.

May display:

```text
Kanban
stage-grouped list
timeline
```

---

# 114. Pipeline Projection Must Not Encode Stage Locally

Drag operation produces a command.

UI does not become authority.

---

# 115. Compact Projection

For mobile, same Opportunity may render as grouped list instead of horizontal Kanban.

State remains identical.

---

# 116. PRJCT-ADM-008 — Notification Center Projection

Contains:

```text
notification type
source
message
read/unread
delivery warning where relevant
timestamp
```

---

# 117. Notification Center ≠ Inbox

A Notification may point to Feedback.

Feedback has independent workflow state.

---

# 118. PRJCT-VOP-001 — Virtual Operator Action Projection

Canonical card:

```text
What happened
Why it matters
Affected entity
Journey/business impact
Recommended action
Automation level
Primary action
```

---

# 119. VOP Projection Should Hide Noise

Routine successful AUTO-4 work SHOULD be available in history, not dominate Action Required.

---

# 120. PRJCT-VOP-002 — Content Health Projection

Aggregates deterministic health:

```text
missing required metadata
broken links
stale questionnaires
missing derivatives
QR validation issues
unclassified media
```

---

# 121. Content Health Is Not Actor Score

No:

```text
Professional score = 82%
```

unless a clearly scoped readiness metric is separately justified.

---

# 122. PRJCT-ADM-009 — Theme Workspace Projection

Must distinguish:

```text
Current Active Theme
Current Draft
AI Proposal
Preview
Historical Revisions
Temporary Activation
```

---

# 123. Theme Projection Cannot Hide Current Active State

Admin must always know what is currently public.

---

# 124. PRJCT-ADM-010 — BB Assistant Projection

Must expose:

```text
task type
source context
generated text
human-edited text
status
staleness
target context
```

---

# 125. BB Projection Must Preserve Origin

Human should be able to tell AI generated vs subsequently edited/applied.

---

# 126. PRJCT-ADM-011 — Audit Projection

Read-only.

Contains normalized:

```text
actor
action
entity
timestamp
safe delta
correlation context
```

---

# 127. Secret Redaction in Audit Projection

Even authorized Audit UI SHOULD not reveal secrets accidentally captured in lower-level logs.

---

# 128. PRJCT-ADM-012 — System Status Projection

Inputs:

```text
web health
database
storage
worker
job backlog
provider health
```

---

# 129. System Status Classification

Canonical:

```text
HEALTHY
DEGRADED
UNAVAILABLE
UNKNOWN
```

---

# 130. Optional Provider State

AI provider unavailable:

```text
system overall = DEGRADED
```

not necessarily unavailable.

---

# 131. PRJCT-SRCH-001 — Public Search Projection

Indexable/searchable fields are explicitly allowlisted.

---

# 132. Public Search Sources

Potential:

```text
Actor identity
public Skills
public Languages
published Projects/Roles
Training
Portfolio categories
```

---

# 133. Search Private Exclusion

Must never index public-unauthorized:

```text
Feedback
Contacts hidden from public
Casting data
Admin notes
Builder-only private information unless search context specifically allows it
```

---

# 134. Search Builder Scope

If Builder supports search-inside-profile while active:

use **Builder Search Projection**, not generic Public Search.

---

# 135. PRJCT-SRCH-002 — Builder Search Projection

Searches only:

```text
currently Builder-eligible items
```

---

# 136. Builder Search Permission Revocation

New query reflects permission immediately/within secure invalidation model.

---

# 137. PRJCT-SRCH-003 — Admin Global Search Projection

May include:

```text
Projects
Media
Contacts
Questionnaires
Castings
Feedback
Opportunities
```

according to Admin permissions.

---

# 138. Admin Search Result

Should include:

```text
entity type
matched value
context
status
route
```

---

# 139. Search Result Does Not Duplicate Entity

It carries only enough info to identify/navigate.

---

# 140. Search Rank

Public search SHOULD prioritize professional relevance/exactness.

Not click popularity by default.

---

# 141. PRJCT-HIST-001 — Historical Questionnaire Projection

Read source:

```text
QuestionnaireRevision
```

not current Questionnaire Draft.

---

# 142. Historical Projection Semantics

Must show:

```text
revision number
published date
historical/read-only marker in Admin context
exact content
```

---

# 143. Historical Projection Current Labels

System UI chrome may use current translation.

Professional snapshot content remains historical.

---

# 144. PRJCT-HIST-002 — Historical Casting Analysis Projection

Reads exact:

```text
CastingAnalysisRevision
source snapshot
Profile snapshot
AI extraction
human decisions
match state at that analysis
```

---

# 145. Historical Analysis Is Not Current Advice

UI must indicate when superseded/stale relative to current Casting/Profile.

---

# 146. PRJCT-HIST-003 — Theme Revision Projection

Allows preview/comparison of immutable ThemeRevision.

---

# 147. Theme Historical Preview

Must not activate revision merely by viewing it.

---

# 148. PRJCT-HIST-004 — Opportunity Timeline Projection

Derived from:

```text
OpportunityStageHistory
Audit/context
```

---

# 149. Timeline Is Read-Only

Current stage remains Opportunity authority.

---

# 150. PRJCT-ANL-001 — Conversion Funnel Projection

Derived from:

```text
Analytics events
Feedback
Casting
Opportunity
```

---

# 151. Business Funnel Authority

For stages like:

```text
Inquiry
Casting
Offer
Booked
```

server business entities take precedence over client analytics.

---

# 152. Funnel Projection Does Not Move Opportunities

Normative.

---

# 153. PRJCT-ANL-002 — Source Attribution Projection

Aggregates:

```text
direct
search
social
campaign
shared_profile
shared_questionnaire
shared_project
casting_specific_link
qr
other
```

---

# 154. Attribution Is Analytical

It does not change content prominence automatically.

---

# 155. PRJCT-ANL-003 — Builder Usage Projection

May aggregate:

```text
builder opens
template choices
section inclusion
PDF generations
professional CTA starts
```

---

# 156. Analytics Privacy

Aggregates SHOULD avoid exposing individual sensitive visitor details unless required and permitted.

---

# 157. Analytics Recommendations

May suggest:

```text
template changes
content discoverability improvements
```

but remain recommendation projection.

---

# 158. Projection Localization

Locale is a projection dimension.

---

# 159. Locale Resolution

Canonical:

```text
request locale
→ supported locale check
→ entity localized content
→ defined fallback
```

---

# 160. Locale Fallback

Must not silently invent translation.

Possible policy:

```text
requested approved translation
→ approved fallback locale
```

with UI semantics as defined later.

---

# 161. Same Entity Across Locales

Project ID remains same.

RU/EN projections are localized views, not different Projects.

---

# 162. Projection Ordering

Projection owns presentation ordering only where order is context-specific.

---

# 163. Global vs Context Order

Example:

```text
Project global display order
```

may come from Project domain.

Questionnaire project order:

```text
comes from Questionnaire configuration/snapshot
```

---

# 164. Projection Must Respect Context Order

Do not sort questionnaire sections by current Project public order.

---

# 165. Primary/Featured Semantics

Projection may use:

```text
Primary
Featured
display order
```

as authoritative curation signals.

---

# 166. No Automatic Popularity Reordering

Analytics cannot silently reorder Public Profile/Portfolio/Projects.

---

# 167. AI Ordering

AI recommendations MAY suggest content changes.

No AI-generated runtime ordering of professional evidence without explicit product rule/human authority.

---

# 168. Projection Content Hierarchy

Projection composition must respect DOC-048 P0–P3 priorities.

---

# 169. P0 Projection

Load first where practical:

```text
identity
primary visuals
quick facts
Video access
Questionnaire
Contact CTA
```

---

# 170. P2/P3 Projection

May be lazy/deferred:

```text
full project archive
extended achievements
secondary media
```

---

# 171. Performance Does Not Change Authorization

Lazy loading MUST still use server-authorized projection.

---

# 172. Responsive Projection Principle

Viewport can change presentation layout.

It MUST NOT change professional data truth or privacy eligibility.

---

# 173. Mobile Projection

May provide condensed DTO only if it preserves all needed P0/P1 semantics.

Avoid separate mobile fact source.

---

# 174. Projection DTO Optimization

Allowed:

```text
summary DTO
detail DTO
```

for same Source entity.

---

# 175. Example

Project list DTO:

```text
id
slug
title
role
thumbnail
year
```

Project detail DTO:

```text
full approved content
role media
performances
```

---

# 176. Summary Projection Is Not Different Truth

Normative.

---

# 177. Projection and SSR

Public P0 projections SHOULD be suitable for server rendering.

---

# 178. Projection and Hydration

Private fields MUST not appear during SSR/hydration transition.

No privacy flash.

---

# 179. Projection and Client Fetch

Interactive components may fetch additional safe projections.

They must not invoke unrestricted Master Data APIs.

---

# 180. Projection Cache Classes

Canonical categories:

```text
CURRENT_PUBLIC
IMMUTABLE_REVISION
BUILDER_SESSION
ADMIN_READ_MODEL
SEARCH
```

---

# 181. Current Public Cache

Needs source-event invalidation.

---

# 182. Immutable Revision Cache

Can be long-lived, keyed by revision.

---

# 183. Builder Cache

Must be scoped to:

```text
session
profile
locale
permission/config version
```

if caching used.

---

# 184. Admin Cache

Use cautiously due freshness/authorization.

Operational workflows generally prioritize current state.

---

# 185. Projection Version Key

For complex projections MAY use:

```text
source updated/version vector
projection schema version
locale
```

---

# 186. Projection Invalidation

Examples:

```text
ProfileChanged
→ Public Profile

PrimaryPortfolioChanged
→ Home + Quick View + Questionnaire readiness

ContactVisibilityChanged
→ Contact + Builder + Questionnaire readiness

QuestionnairePublished
→ Questionnaire Hub

ThemeActivated
→ presentation caches
```

---

# 187. Invalidation Must Be Targeted

Avoid clearing every cache for every mutation where practical.

---

# 188. Security Invalidation

Visibility/token revocation takes priority over cache efficiency.

---

# 189. Projection Eventual Consistency

Acceptable for ordinary non-sensitive:

```text
featured Project list refresh
analytics aggregates
search
```

---

# 190. Projection Strong Freshness

Required for:

```text
private/public visibility
Builder generation eligibility
current Opportunity state before transition
Questionnaire publish readiness
token access
```

---

# 191. Final Revalidation Before Sensitive Output

Even if a projection is cached:

```text
Builder Generate
private Document Download
protected media access
```

MUST perform authoritative security/eligibility checks as required.

---

# 192. Projection Schema

Projection DTOs SHOULD be explicit typed schemas.

---

# 193. Avoid Passing ORM Records Directly

Bad:

```text
return database Contact row
```

Good:

```text
ContactPublicDTO
```

---

# 194. Why Explicit DTO

Prevents accidental leakage when database later gains:

```text
internal_note
private_phone
token
provider_metadata
```

---

# 195. DTO Allowlist Principle

Public DTO defined by allowed fields.

Not by “all fields minus private list”.

---

# 196. Projection Schema Version

For internal/public APIs that may persist or be consumed long-term, schema versioning MAY be required.

---

# 197. Background Job Input Is Not Projection

Job payload may contain references to exact Revision/Entity.

Do not confuse it with UI read model.

---

# 198. Projection Error Behaviour

If optional section fails:

page MAY render remaining valid projection with degraded section.

---

# 199. Critical Projection Failure

If core identity cannot resolve safely:

show page-level unavailable state.

Do not serve partial data of uncertain ownership.

---

# 200. Missing Optional Data

Projection should usually omit section rather than render technical nulls.

---

# 201. Missing Required Questionnaire Data

Readiness blocks generation/publication.

Do not simply omit required Close-Up/Full Body.

---

# 202. Projection Observability

Complex projection query SHOULD support correlation and performance monitoring.

---

# 203. Projection Logging

Must not log full sensitive DTO by default.

---

# 204. Projection Metrics

Useful:

```text
build latency
cache hit
DB query latency
projection errors
invalidations
```

---

# 205. Projection Security Testing

Tests MUST inspect actual response payload, not only rendered visual result.

---

# 206. Example Test

Hidden Contact:

```text
assert not in HTML
assert not in JSON
assert not in hydration payload
assert not in Builder API
```

according to context.

---

# 207. Projection Accessibility

Projection must provide enough semantic metadata for accessible rendering.

Examples:

```text
meaningful labels
alt/caption data
language
link descriptions
heading hierarchy inputs
```

---

# 208. Projection SEO

Public projection MAY provide:

```text
title
description
canonical URL
structured metadata inputs
```

---

# 209. SEO Must Reuse Source

No separate manually divergent professional identity if same fact already exists.

---

# 210. Projection and Theme

Theme consumes projection.

Theme does not determine eligibility.

---

# 211. Projection Before Theme

Canonical order:

```text
Authorize data
→ Build Projection
→ Render using Theme
```

Never:

```text
Theme hides private item
```

---

# 212. Projection and AI

AI MAY consume dedicated internal context projection.

---

# 213. AI Context Projection

Should be capability-specific:

```text
CastingAIContext
BBWritingContext
ThemeAIContext
VOPAnalysisContext
```

---

# 214. AI Context Is Not Public Projection

May contain privileged information when task requires it, subject to privacy policy.

---

# 215. AI Context Minimization

Only fields necessary for task.

---

# 216. PRJCT-CST-003 — Casting AI Context Projection

Potential:

```text
Casting Source snapshot
confirmed relevant Profile facts
professional evidence
```

No unrelated Contacts/private domains.

---

# 217. PRJCT-BBA-001 — BB Writing Context Projection

Task-specific.

Example Cover Letter:

```text
confirmed profile identity
selected experience
skills/languages
casting context
```

---

# 218. PRJCT-THM-001 — Theme AI Context Projection

Contains:

```text
design token schema
current theme
component constraints
accessibility requirements
```

No professional operational secrets.

---

# 219. PRJCT-VOP-003 — VOP Analysis Projection

Contains structured operational facts necessary to explain Observation.

---

# 220. VOP Does Not Need Whole Database Dump

Normative.

---

# 221. Projection Mutation Rule

If user edits through a projection:

```text
Projection UI
→ explicit command
→ owning Domain
→ authoritative state changes
→ projection rebuilt
```

---

# 222. Optimistic UI

May be used only where failure semantics are safe.

Not default for:

```text
publish
Booked
Contact visibility
Primary marker
delete
```

---

# 223. Projection After Mutation

Client SHOULD use returned authoritative command result or refetch projection.

Avoid assuming local mutation succeeded.

---

# 224. Projection and Revision Diff

Revision comparison is a derived projection:

```text
Revision A
+
Revision B
→ Domain-aware Diff Projection
```

---

# 225. Diff Does Not Alter Revisions

Normative.

---

# 226. Projection and Archived Data

Admin projections MAY include archived entities under explicit filter.

Public default projections exclude them.

---

# 227. Projection and Historical Data

Historical records must be marked/contextualized so users do not mistake them for current state.

---

# 228. Projection and Deleted Data

Hard-deleted values must not remain in rebuilt active projection unless retained historical snapshot permits it.

---

# 229. Projection and Redacted Historical Data

Controlled redaction layer may override historical presentation without rewriting normal revision semantics.

---

# 230. Public Data Exposure Matrix

| Data | Public Site | Prepared QST | Builder | Admin |
|---|---:|---:|---:|---:|
| Published Profile facts | per visibility | eligible | per Builder eligibility | Yes |
| Main Portfolio | public eligible | selected | eligible | Yes |
| Emotional Grid | public eligible | selected | eligible | Yes |
| Project | public eligible | selected | eligible | Yes |
| Training | public eligible | selected | eligible | Yes |
| Skills | public eligible | selected | eligible | Yes |
| Contact | `show_on_public_site` | `allow_in_admin_questionnaires` | `allow_in_public_questionnaire_builder` | Yes |
| Feedback | No | No | No | Authorized only |
| Casting Source | No | No | No | Authorized only |
| Opportunity | No | No | No | Authorized only |
| AI Draft | No | No | No | Authorized only |
| Audit | No | No | No | Authorized only |

---

# 231. Questionnaire Projection Matrix

| Mode | Source |
|---|---|
| Admin current Draft | Draft + current Master |
| Admin historical | immutable Revision |
| Public current prepared | current Published Revision |
| Public Builder preview | Session + current eligible Source |
| Builder generated PDF | immutable GenerationSnapshot |
| Casting-specific prepared | exact permitted Questionnaire Revision |
| PDF regeneration | exact Revision/Snapshot |

---

# 232. Public Page Projection Matrix

| Page | Main Projection |
|---|---|
| Home | Public Home |
| Portfolio | Portfolio |
| Emotional | Emotional Range |
| Project list | Project Listing |
| Project detail | Project Detail |
| Questionnaire | Questionnaire Hub |
| Prepared questionnaire | Revision Projection |
| Builder | Builder Eligibility/Session |
| Contact | Public Contact + Feedback form context |

---

# 233. Admin Projection Matrix

| Workspace | Projection |
|---|---|
| Dashboard | Action Dashboard |
| Media | Media + Usage |
| Portfolio | Portfolio Admin |
| Emotional | Session/Grid Admin |
| Projects | Project/Role Admin |
| Questionnaires | Draft/Revision/Readiness |
| Inbox | Feedback |
| Castings | Casting Composite |
| Opportunities | Pipeline |
| VOP | Observations |
| BB | AI Draft Context |
| Themes | Theme State |
| Audit | Audit History |
| System | Health |

---

# 234. Projection Anti-Pattern PRJCT-AP-001

**Public projection is serialized database entity**

---

# 235. PRJCT-AP-002

**Separate editable Quick View profile**

---

# 236. PRJCT-AP-003

**Builder reuses Public Profile DTO and therefore cannot support independent eligibility**

---

# 237. PRJCT-AP-004

**Private Contact sent to browser with `hidden: true`**

---

# 238. PRJCT-AP-005

**Questionnaire historical projection joins current Language level**

---

# 239. PRJCT-AP-006

**Dashboard copies Opportunity stage into own persistent table**

---

# 240. PRJCT-AP-007

**Search result contains full private entity payload**

---

# 241. PRJCT-AP-008

**Analytics rank silently determines Project order**

---

# 242. PRJCT-AP-009

**Theme decides which Contacts are visible**

---

# 243. PRJCT-AP-010

**Casting AI recommendation shown above original Casting Source**

---

# 244. PRJCT-AP-011

**Current and historical Questionnaire states visually indistinguishable**

---

# 245. PRJCT-AP-012

**Builder Preview treated as security-authoritative for PDF generation**

---

# 246. PRJCT-AP-013

**Mobile projection omits Primary Full Body while desktop includes it**

---

# 247. PRJCT-AP-014

**Projection duplicates professional facts in writable JSON cache**

---

# 248. PRJCT-AP-015

**Public page renders raw storage path**

---

# 249. PRJCT-AP-016

**Projection cache ignores Contact visibility invalidation**

---

# 250. PRJCT-AP-017

**VOP receives unrestricted private data for every observation**

---

# 251. PRJCT-AP-018

**AI context shares one generic “full profile dump”**

---

# 252. PRJCT-AP-019

**Search popularity becomes professional suitability score**

---

# 253. PRJCT-AP-020

**Readiness percentage replaces exact blockers**

---

# 254. Projection Quality Gate

Каждая значимая Projection MUST определить:

- [ ] Projection ID;
- [ ] purpose;
- [ ] intended consumer;
- [ ] Source entities;
- [ ] current vs historical source mode;
- [ ] authorization;
- [ ] visibility policy;
- [ ] locale;
- [ ] field allowlist;
- [ ] ordering;
- [ ] content priority;
- [ ] public identifiers;
- [ ] cacheability;
- [ ] invalidation events;
- [ ] freshness requirement;
- [ ] fallback/error behaviour;
- [ ] accessibility metadata;
- [ ] privacy tests;
- [ ] mutation route, if actions exist.

---

# 255. Projection Specification Template

```text
Projection ID:
PRJCT-...

Name:
...

Consumer:
Public / Builder / Admin / AI / Search / Renderer

Purpose:
...

Source:
...

Authorization:
...

Visibility:
...

Locale:
...

Fields:
...

Ordering:
...

Historical or Current:
...

Cache:
...

Invalidated By:
...

Freshness:
...

Writable:
No

Actions:
route to ...

Tests:
...
```

---

# 256. Example — Public Contact Projection

```text
Projection:
PRJCT-PUB-CONTACT

Source:
Contact

Eligibility:
lifecycle active
AND show_on_public_site = true

Output:
label
relationship
approved value/link

Excluded:
internal notes
builder/admin permission flags
archived values

Cache:
yes

Invalidate:
ContactChanged
ContactVisibilityChanged

Writable:
No
```

---

# 257. Example — Builder Contact Projection

```text
Projection:
PRJCT-PQB-CONTACT

Eligibility:
lifecycle active
AND allow_in_public_questionnaire_builder = true

Does NOT require:
show_on_public_site = true

Output:
only fields required to select/render contact

Security:
server authoritative
```

---

# 258. Example — Casting Quick View

```text
Source:
ActorProfile
Primary Close-Up
Primary Full Body
Skills
Languages
Video
Primary Questionnaire

Output:
compact professional decision model

Writable:
No

Missing data:
omit or explicitly unavailable
never AI-invent
```

---

# 259. Example — Historical Questionnaire

```text
Source:
QuestionnaireRevision

Current Profile joins:
Prohibited for document content

Output:
exact historical snapshot

Cache:
long-lived revision-specific

Mutation:
none
```

---

# 260. Example — Opportunity Card

```text
Source:
Opportunity
Casting summary
next action

Output:
stage
title
deadline
next action

Change Stage:
command to OpportunityApplicationService

No local writable stage.
```

---

# 261. Projection Security Tests

Minimum:

```text
cross-profile data exclusion
archived data exclusion
private Contact exclusion
Builder permission enforcement
historical/current separation
token-scoped access
AI context minimization
```

---

# 262. E2E-PRJCT-001 — Public Contact

Contact public=false.

Verify absent from:

```text
SSR HTML
page JSON
client hydration
public API
```

---

# 263. E2E-PRJCT-002 — Builder-Only Contact

```text
public=false
builder=true
```

Expected:

```text
absent public Contact page
present eligible Builder projection
```

---

# 264. E2E-PRJCT-003 — Admin-Questionnaire-Only Contact

```text
public=false
admin_questionnaire=true
builder=false
```

Expected:

```text
available Admin Questionnaire
absent Public Profile
absent Builder
```

---

# 265. E2E-PRJCT-004 — Casting Quick View

Primary Close-Up and Full Body render from Portfolio authority.

No duplicate Quick View values.

---

# 266. E2E-PRJCT-005 — Historical Questionnaire

Current Profile changes.

Historical Projection unchanged.

---

# 267. E2E-PRJCT-006 — Builder Tampering

Private ID submitted manually.

Builder projection/generation rejects it.

---

# 268. E2E-PRJCT-007 — Search Privacy

Hidden Project does not appear in rebuilt Public Search.

---

# 269. E2E-PRJCT-008 — Stale Search Hit

Project becomes private before search reindex.

Direct access final authorization blocks it.

---

# 270. E2E-PRJCT-009 — Dashboard Authority

Dashboard reflects Opportunity transition from detail page after refetch/event.

No parallel status.

---

# 271. E2E-PRJCT-010 — Notification vs Feedback

Reading Notification does not make Feedback RESOLVED/READ unless explicit Feedback action occurs.

---

# 272. E2E-PRJCT-011 — Source Before AI

Compact Casting UI maintains sequence:

```text
Source
AI Extraction
Human Confirmation
Match
Recommendation
```

---

# 273. E2E-PRJCT-012 — Mobile

Primary P0/P1 data remains available at 320px.

Layout changes only.

---

# 274. E2E-PRJCT-013 — Public Cache

Contact visibility OFF invalidates relevant current public cache.

---

# 275. E2E-PRJCT-014 — Revision Cache

Publishing Revision 6 does not modify cached Revision 5.

---

# 276. E2E-PRJCT-015 — Theme

Switch Theme.

Public content facts/visibility remain identical.

---

# 277. E2E-PRJCT-016 — AI Context

BB Biography request does not receive unrelated Casting Source/private Feedback data.

---

# 278. E2E-PRJCT-017 — Media Usage

Same MediaAsset used in Portfolio and Role.

Admin Usage Projection shows both.

---

# 279. E2E-PRJCT-018 — Questionnaire Required Photos

Any current/generated questionnaire projection contains both eligible Primary Close-Up and Full Body.

---

# 280. E2E-PRJCT-019 — Emotional Questionnaire

Questionnaire Projection contains:

```text
composite
shooting date
full emotional link
```

not individual source-photo dump.

---

# 281. E2E-PRJCT-020 — Readiness

Missing Full Body produces explicit blocker.

No meaningless general score replaces it.

---

# 282. Projection Traceability

Required chain:

```text
Source Entity
    ↓
Visibility / Lifecycle Rule
    ↓
Projection Definition
    ↓
DTO / Renderer
    ↓
User Flow
    ↓
Test
```

---

# 283. Public Projection Traceability

Example:

```text
Contact.show_on_public_site
      ↓
Public Contact Policy
      ↓
PRJCT-PUB-CONTACT
      ↓
Contact Page / CTA
      ↓
Privacy E2E Test
```

---

# 284. Builder Projection Traceability

```text
allow_in_public_questionnaire_builder
      ↓
Builder Eligibility Policy
      ↓
PRJCT-PQB-*
      ↓
Builder UI / Generation
      ↓
Tampering Test
```

---

# 285. Historical Projection Traceability

```text
QuestionnaireRevision
      ↓
Historical Projection
      ↓
HTML/PDF
      ↓
Current Source Changed
      ↓
Historical Stability Test
```

---

# 286. Projection Ownership Rule

Projection definition belongs to application/read architecture.

Source ownership remains Domain.

---

# 287. Projection Change Governance

A material change to Public Projection P0/P1 composition must be aligned with:

```text
DOC-048 Content Hierarchy
DOC-041 Public Navigation
DOC-043 Public User Flows
```

It must not happen as isolated frontend refactor.

---

# 288. Projection and Database Schema

Physical implementation MAY use:

```text
direct joins
SQL views
materialized views
query builders
dedicated projection tables
cache
```

depending need.

Semantics in this document remain independent of physical mechanism.

---

# 289. Materialized Projection

If materialized:

```text
must be rebuildable
must not become writable authority
must have invalidation/rebuild strategy
```

---

# 290. Dedicated Projection Table

Permitted for:

```text
search
analytics
complex public read acceleration
```

only as derived data.

---

# 291. Projection Rebuild

System SHOULD be capable of rebuilding derived/materialized projections from authoritative Source.

---

# 292. Rebuild Failure

Does not modify Source.

Affected feature becomes stale/degraded.

---

# 293. Current Projection vs Revision Projection

Canonical distinction:

```text
Current Professional Site:
Master Data → current eligible Projection

Published Questionnaire:
Revision → Revision Projection
```

---

# 294. Builder Projection vs Revision Projection

Canonical:

```text
Builder Preview:
Session + current eligible Master

Builder Generated PDF:
GenerationSnapshot
```

---

# 295. AI Context Projection vs Public Projection

Canonical:

```text
AI Context:
minimum authorized internal task-specific data

Public Projection:
only externally publishable data
```

They are not interchangeable.

---

# 296. Projection Architecture Diagram

```text
                         MASTER DATA
                             │
             ┌───────────────┼─────────────────┐
             │               │                 │
             ▼               ▼                 ▼
       PUBLIC POLICY    BUILDER POLICY     ADMIN POLICY
             │               │                 │
             ▼               ▼                 ▼
        PUBLIC PRJCT      BUILDER PRJCT      ADMIN PRJCT
             │               │                 │
      ┌──────┼──────┐        │        ┌───────┼────────┐
      ▼      ▼      ▼        ▼        ▼       ▼        ▼
    Home Portfolio Projects Builder Dashboard Casting Media

                         REVISIONS
                             │
                             ▼
                     HISTORICAL PRJCT
                       │           │
                       ▼           ▼
                     HTML         PDF

                         MASTER
                           │
                           ▼
                     SEARCH POLICY
                           │
                           ▼
                     SEARCH PRJCT
```

---

# 297. Final Projection Catalogue

```text
PUBLIC
  Actor Identity
  Home
  Casting Quick View
  Portfolio
  Emotional Range
  Project Listing
  Project Detail
  Skills/Languages
  Training/Achievements
  Professional Media/Links
  Contacts
  Questionnaire Hub

QUESTIONNAIRE
  Admin Draft
  Readiness
  Published Revision
  Document/PDF model

BUILDER
  Eligibility
  Session
  Preview
  Generation Snapshot

ADMIN
  Dashboard
  Media Usage
  Dependency Impact
  Questionnaire List
  Inbox
  Casting Composite
  Opportunity Pipeline
  Notifications
  Themes
  BB
  Audit
  System Status

SEARCH
  Public
  Builder
  Admin

AI CONTEXT
  Casting AI
  BB
  Theme AI
  VOP

HISTORICAL
  Questionnaire Revision
  Casting Analysis Revision
  Theme Revision
  Opportunity Timeline

ANALYTICS
  Funnel
  Attribution
  Builder Usage
```

---

# 298. Projection Compliance Criteria

Реализация соответствует DOC-073, если:

1. Public interfaces receive safe server-built projections;
2. hidden data is absent from browser payload;
3. Public Site and Public Builder eligibility are independent;
4. Casting Quick View is read-only projection, not duplicate profile;
5. Main Portfolio Primary roles derive from Portfolio authority;
6. Emotional Projection preserves finalized composite;
7. Questionnaire current public rendering uses Published Revision;
8. historical Questionnaire never reads current mutable facts;
9. Admin Draft clearly distinguishes current Source and Published Revision;
10. Builder Preview remains current/non-authoritative;
11. Builder generated output uses immutable GenerationSnapshot;
12. Dashboard does not own business state;
13. Inbox and Notification Center remain distinct;
14. Casting projection preserves Source → AI → Human decision order;
15. Opportunity Pipeline reads one authoritative stage;
16. Public Search excludes private data;
17. Builder Search uses Builder eligibility;
18. Admin Search remains authorization-aware;
19. caches never become Source of Truth;
20. visibility revocation triggers appropriate invalidation/revalidation;
21. DTOs use explicit field allowlists;
22. AI contexts are capability-specific and minimized;
23. responsive variants do not change facts/permissions;
24. Projection materialization is rebuildable;
25. projection actions route to owning Application Service.

---

# 299. Финальная доктрина

> **Read architecture продукта должна формировать ровно то представление данных, которое требуется конкретной профессиональной задаче. Public Profile, Casting Quick View, Questionnaire, Public Builder, Dashboard, Search и AI Context могут использовать одни и те же Source entities, но каждый из них имеет собственные eligibility, privacy, ordering и historical semantics. Это позволяет переиспользовать единую профессиональную правду без копирования её в отдельные writable модели и одновременно гарантировать, что Public пользователь, casting specialist, Admin, AI и PDF renderer получают только тот объём данных, который им действительно разрешён и необходим.**