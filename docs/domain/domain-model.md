# DOMAIN MODEL

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Каноническая доменная модель профессиональной платформы актрисы

**Целевой файл:** `docs/domain/domain-model.md`  
**Документ:** DOC-060  
**Статус:** ✅ Completed  
**Тип:** Domain / Canonical Model

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

---

# 1. Назначение документа

Настоящий документ определяет **каноническую доменную модель TO-BE продукта**.

Он фиксирует:

1. основные Domain Areas;
2. канонические сущности;
3. смысл каждой сущности;
4. границы ответственности;
5. что является Source of Truth;
6. что является Configuration;
7. что является Revision/Snapshot;
8. что является Derived Artifact;
9. что является Projection;
10. что является Operational Entity;
11. что является AI Output;
12. какие данные не должны моделироваться отдельными сущностями;
13. основные invariants.

Документ намеренно **не является физической схемой PostgreSQL**.

Физическая схема определяется позднее в:

```text
DOC-090 database-architecture.md
DOC-091 data-dictionary.md
```

---

# 2. Место Domain Model в архитектуре

Цепочка:

```text
Business Requirements
        ↓
Business Rules
        ↓
Information Architecture
        ↓
UX / User Flows
        ↓
DOMAIN MODEL
        ↓
Relationships
        ↓
State Machines
        ↓
Data Ownership
        ↓
Revisions
        ↓
Architecture / Database / API
```

---

# 3. Главный принцип Domain Model

> **Профессиональный факт должен существовать в системе один раз как авторитетный источник и переиспользоваться во всех представлениях, документах и workflows через отношения, конфигурации, snapshots и projections.**

---

# 4. Domain Model IDs

Канонические Domain identifiers используют:

```text
DM-*
```

Entities:

```text
DM-ENT-*
```

Domain invariants:

```text
DM-INV-*
```

Bounded contexts:

```text
DM-CTX-*
```

---

# 5. Основные типы доменных объектов

В модели MUST различаться следующие классы.

## 5.1 Source / Master Data

Авторитетные профессиональные данные.

Например:

```text
ActorProfile
PortfolioItem
Project
Role
Skill
LanguageProficiency
Training
Contact
```

---

## 5.2 Configuration

Определяет, **как Source Data используется**, но не создаёт новый профессиональный факт.

Например:

```text
QuestionnaireDefinition
BuilderTemplate
GridCellCropConfiguration
VisibilityPolicy
```

---

## 5.3 Revision / Snapshot

Фиксированное состояние данных на конкретный момент.

Например:

```text
QuestionnaireRevision
ThemeRevision
CastingAnalysisRevision
```

---

## 5.4 Derived Artifact

Вычисляемый/генерируемый результат.

Например:

```text
MediaDerivative
EmotionalGridComposite
QuestionnairePDF
QRArtifact
Thumbnail
```

---

## 5.5 Projection

Контекстное представление нескольких Source entities.

Например:

```text
Casting Quick View
Public Profile
Search Result
Questionnaire HTML View
```

Projection MAY быть materialized/cacheable технически, но **не должна становиться отдельным независимым Source of Truth**.

---

## 5.6 Operational Entity

Отражает процесс или реальное профессиональное событие.

Например:

```text
Feedback
Casting
Opportunity
Notification
SupportTicket
```

---

## 5.7 AI Output

Машинный результат, который не является подтверждённым Source Fact.

Например:

```text
CastingRequirementExtraction
CastingRecommendation
AIDraft
ThemeProposal
VOPRecommendation
```

---

## 5.8 Audit / History

Не редактируемая бизнес-история значимых действий.

Например:

```text
AuditEvent
OpportunityStageHistory
NotificationAttempt
```

---

# 6. Source Fact != Projection

Пример:

```text
ActorLanguageProficiency:
English — B2
```

является Source Fact.

Его отображение в:

```text
Profile
Quick View
Prepared Questionnaire
Public Builder
PDF
Search
```

не создаёт пять независимых значений.

---

# 7. Source Fact != Snapshot

Текущий Source Fact:

```text
English — C1
```

не должен переписывать историческую Questionnaire Revision, сформированную, когда значение было:

```text
English — B2
```

---

# 8. Source Fact != AI Extraction

Если Casting AI извлёк:

```text
English required: B2
```

это Casting Requirement Extraction.

Это не изменение языка актрисы.

---

# 9. Source Fact != Human Decision

Human Decision может подтвердить AI Extraction, но сама по себе не является характеристикой Actor Profile.

---

# 10. Product Root

Корневой professional aggregate:

```text
ActorProfile
```

В текущем продукте предполагается одна основная актриса.

Однако actor-owned entities SHOULD иметь логическую связь с:

```text
profile_id
```

чтобы domain integrity не зависела от assumption `единственный profile навсегда`.

---

# 11. Canonical Domain Areas

Модель разделяется на следующие contexts:

```text
DM-CTX-01 Actor Profile
DM-CTX-02 Media
DM-CTX-03 Main Portfolio
DM-CTX-04 Emotional Portfolio
DM-CTX-05 Professional Experience
DM-CTX-06 Training & Achievements
DM-CTX-07 Skills & Languages
DM-CTX-08 Professional Media & Links
DM-CTX-09 Contacts
DM-CTX-10 Questionnaires
DM-CTX-11 Public Questionnaire Builder
DM-CTX-12 QR & Public Links
DM-CTX-13 Feedback / Professional Inbox
DM-CTX-14 Castings
DM-CTX-15 Opportunity Pipeline
DM-CTX-16 Notifications
DM-CTX-17 BB Assistant
DM-CTX-18 Virtual Operator
DM-CTX-19 Themes
DM-CTX-20 Marketing & Analytics
DM-CTX-21 Social Publishing
DM-CTX-22 Help & Tickets
DM-CTX-23 Identity / Authorization
DM-CTX-24 Audit
```

---

# 12. High-Level Domain Map

```text
                            ACTOR PROFILE
                                  │
       ┌──────────────┬───────────┼───────────┬──────────────┐
       ↓              ↓           ↓           ↓              ↓
     MEDIA         PORTFOLIO   PROJECTS    SKILLS        CONTACTS
       │              │           │        LANGUAGES
       │              │           │
       └──────┐       │           │
              ↓       ↓           ↓
         EMOTIONAL   QUESTIONNAIRES
              │           │
              │      ┌────┴──────────┐
              │      ↓               ↓
              │ PREPARED       PUBLIC BUILDER
              │      │               │
              └──────┼───────────────┘
                     ↓
               PROFESSIONAL PACKAGE
                     │
                     ↓
                  FEEDBACK
                     │
                     ↓
                   CASTING
                     │
                     ↓
                 OPPORTUNITY
                     │
                     ↓
                   BOOKED
                     │
                     ↓
                PROJECT / ROLE
```

Cross-cutting:

```text
QR
Notifications
AI
Virtual Operator
Analytics
Audit
Themes
Social Publishing
```

---

# 13. DM-ENT-001 — ActorProfile

## Purpose

Главная профессиональная identity сущность.

## Represents

```text
кто является актрисой
```

а не полный набор всех дочерних данных.

## Conceptual attributes

```text
id
professional_name
legal/display naming fields where applicable
professional_title
location/base
biography source/localized references
primary_locale
supported_locales
public_slug/root identity
lifecycle status
created_at
updated_at
```

---

# 14. ActorProfile Must Not Become God Object

Не следует хранить непосредственно внутри одной структуры:

- все фотографии;
- Projects;
- Castings;
- Contacts;
- Skills;
- Questionnaires.

Они являются самостоятельными entities/domains.

---

# 15. Profile Localized Content

Narrative локализуемые поля MAY моделироваться отдельной entity/value structure:

```text
ProfileLocalizedContent
```

Conceptually:

```text
profile_id
locale
biography
professional_summary
other localized narrative
```

Точная физическая нормализация будет определена позднее.

---

# 16. Actor Profile Structured Facts

Структурированные факты вроде:

- location;
- physical/professional parameters;
- dates;

SHOULD быть typed data, а не извлекаться каждый раз из Biography.

---

# 17. DM-ENT-010 — MediaAsset

## Purpose

Хранит логическую запись о media source.

MediaAsset представляет **оригинальный медиаматериал**, а не его профессиональную классификацию.

## Types

Минимально:

```text
IMAGE
VIDEO
AUDIO
DOCUMENT
```

External-only resources MAY иметь отдельную ProfessionalLink entity вместо MediaAsset, в зависимости от ownership.

---

# 18. MediaAsset conceptual fields

```text
id
profile_id
type
storage_source
original_filename
mime_type
size
technical_dimensions/duration
checksum
capture/shooting metadata where known
storage locator
processing status
created_at
```

Internal storage locator MUST NOT быть Public URL.

---

# 19. Immutable Original Principle

Исходный uploaded MediaAsset SHOULD рассматриваться как immutable source.

Изменения:

```text
crop
resize
format conversion
compression
thumbnail
grid composite
```

создают Derived Artifact.

---

# 20. DM-ENT-011 — MediaDerivative

Derived representation of MediaAsset.

Examples:

```text
thumbnail
web optimized
PDF image
poster
responsive image
```

Conceptual:

```text
id
media_asset_id
derivative_type
parameters/hash
storage_locator
status
generated_at
source_version
```

---

# 21. Derivative Is Regenerable

MediaDerivative SHOULD быть воспроизводим из source + configuration.

Следовательно:

```text
Derivative != Source of Truth
```

---

# 22. DM-ENT-012 — MediaUsage

Conceptually relation/projection identifying where MediaAsset is used.

May ultimately be represented through explicit domain foreign keys/join tables rather than generic polymorphic table.

Domain requirement:

Admin MUST be able to determine:

```text
where this asset is used
```

---

# 23. DM-ENT-020 — PortfolioItem

## Purpose

Профессиональное использование MediaAsset в основном актёрском Portfolio.

PortfolioItem:

```text
references MediaAsset
```

и добавляет профессиональную семантику.

---

# 24. PortfolioItem conceptual fields

```text
id
profile_id
media_asset_id
portfolio_type
shooting_date/session relation if available
caption/localized metadata
display_order
is_primary
publication/lifecycle state
visibility
created_at
```

---

# 25. Portfolio Categories

Canonical taxonomy supports types such as:

```text
FULL_BODY
CLOSE_UP
WAIST
PROFILE
THREE_QUARTER
STAGED
OTHER_APPROVED
```

Taxonomy final form belongs corresponding Portfolio module.

---

# 26. Primary Portfolio Roles

At minimum domain MUST support semantic roles:

```text
Primary Close-Up
Primary Full Body
```

These are curated presentation markers.

---

# 27. PortfolioItem != MediaAsset

One MediaAsset MAY:

- exist in Media Library;
- become PortfolioItem;
- also relate to Project/Role;

without copying physical file.

---

# 28. DM-ENT-030 — EmotionalSession

## Purpose

Представляет одну профессиональную Emotional Portfolio shooting session.

Mandatory professional attribute:

```text
shooting_date
```

---

# 29. EmotionalSession conceptual fields

```text
id
profile_id
shooting_date
title/context optional
localized description
lifecycle status
visibility
created_at
```

---

# 30. DM-ENT-031 — EmotionalSessionMedia

Relation between:

```text
EmotionalSession
MediaAsset
```

It identifies eligible original photos for that emotional shoot.

---

# 31. EmotionalSessionMedia Does Not Alter Source

Any crop/configuration for Grid belongs Grid Cell, not source MediaAsset.

---

# 32. DM-ENT-032 — EmotionalGrid

## Purpose

Конфигурация профессионального composite Emotional Row/Grid.

Represents:

```text
which session
which dimensions
which cells
which order
which crop settings
which version
which one is primary
```

---

# 33. Allowed EmotionalGrid dimensions

Exact canonical set:

```text
1×2
1×3
1×4
2×2
2×3
2×4
3×2
3×3
3×4
4×2
4×3
4×4
```

No arbitrary dimensions.

---

# 34. EmotionalGrid Exact Count Invariant

For:

```text
rows × columns
```

Grid MUST contain exactly:

```text
rows * columns
```

valid cells before finalization.

---

# 35. DM-ENT-033 — EmotionalGridCell

Each cell represents:

```text
grid_id
position
source emotional media
crop/pan/scale/rotation configuration
confirmation state
```

Conceptually:

```text
id
grid_id
position_index
emotional_session_media_id
crop_x
crop_y
scale
rotation
confirmed_at/by
```

Exact crop representation later.

---

# 36. Face Prominence

The domain requirement:

```text
target ≥90% useful cell area represented by face close-up
```

is a **professional crop constraint**.

Automated face geometry MAY help validate/suggest, but human confirmation remains authoritative for cell finalization.

---

# 37. Emotional Grid Appearance Invariant

Grid configuration MUST NOT include:

- facial regeneration;
- body regeneration;
- skin retouch transformation;
- synthetic details;
- identity alteration.

Permitted transformations:

```text
crop
pan
scale
limited rotation
approved technical derivative processing
```

---

# 38. DM-ENT-034 — EmotionalGridArtifact

Derived outputs:

```text
MASTER_COMPOSITE
WEB
PDF
THUMBNAIL
```

MAY be modeled as specialized MediaDerivative or separate artifact relation.

Domain rule:

```text
Artifact is derived from finalized EmotionalGrid configuration.
```

---

# 39. Multiple Emotional Grids

One EmotionalSession MAY have multiple Grids.

Exactly one MAY be marked:

```text
primary
```

for a specific applicable context according to module rules.

---

# 40. DM-ENT-040 — Project

## Purpose

Represents confirmed professional work/production.

Project MUST NOT represent Casting.

---

# 41. Project conceptual fields

```text
id
profile_id
title
localized titles/descriptions
project_type
production/company/context
start/end/year fields
featured
display_order
lifecycle
visibility
created_at
```

---

# 42. Project != Casting

Canonical distinction:

```text
Casting = potential professional opportunity
Project = professional work / production record
```

A successful Booked Opportunity MAY eventually create a Project Draft.

---

# 43. DM-ENT-041 — Role

Represents actor's role within a Project.

Conceptual:

```text
id
project_id
role_name
localized description
role_type/context
featured/display metadata
lifecycle/visibility
```

---

# 44. Role Requires Project Context

A Role MUST belong to a Project.

No orphan public roles.

---

# 45. DM-ENT-042 — RoleMedia

Relation:

```text
Role
→ MediaAsset
```

with role-specific metadata/display ordering.

Must preserve existing capability:

```text
max 5 role photos
```

according to current/business configuration.

---

# 46. Home “В образе”

Domain MUST support selecting up to:

```text
2
```

role/showcase images for existing home `В образе` presentation, preserving current product capability.

This is presentation configuration over RoleMedia, not independent Media source.

---

# 47. DM-ENT-043 — Performance

Represents professional performance/showing date associated with Project/Role where applicable.

Conceptual:

```text
id
project_id
role_id optional according to semantics
date/time
venue/location
localized note
status
```

---

# 48. Performance ≠ Project

One Project MAY have many Performances.

---

# 49. DM-ENT-044 — Achievement

Represents award, recognition, certification or other professional achievement where appropriate.

Achievement is separate from Project but MAY reference Project/Role when context exists.

Conceptually:

```text
id
profile_id
title
issuer/context
date
description
evidence media/link
visibility
```

---

# 50. DM-ENT-050 — Training

Represents courses, education, workshops and professional training.

---

# 51. Training conceptual fields

```text
id
profile_id
institution
course/program
start/end/date
description
credential/evidence relation
visibility
lifecycle
```

---

# 52. Training != Project

Legacy items previously stored as Project `"Other"` MUST migrate into Training where semantically appropriate.

Ambiguous migration requires human decision.

---

# 53. DM-ENT-060 — SkillDefinition

Represents canonical skill identity/category.

Example:

```text
Horse Riding
Driving
Dance
Swimming
```

---

# 54. DM-ENT-061 — ActorSkill

Represents confirmed relation:

```text
ActorProfile
→ SkillDefinition
```

Conceptually:

```text
id
profile_id
skill_id
level
professional notes
visibility scopes
display_order
```

---

# 55. Skill Level

Current product requires support of:

```text
5 levels
```

Exact semantics/names defined in Skills module.

---

# 56. Skill Inference Rule

AI MAY suggest skill extraction from source material.

It MUST NOT create confirmed ActorSkill silently.

---

# 57. DM-ENT-062 — LanguageDefinition

Canonical language identity.

Example:

```text
Russian
English
Kazakh
```

---

# 58. DM-ENT-063 — ActorLanguageProficiency

Confirmed language capability.

Conceptually:

```text
id
profile_id
language_id
level
cefr_level where applicable
native flag where applicable
notes
visibility scopes
display_order
```

---

# 59. CEFR Invariant

CEFR value MUST be explicit confirmed professional fact.

AI MUST NOT infer:

```text
"speaks English well"
→ C1
```

without human confirmation.

---

# 60. DM-ENT-070 — ProfessionalLink

Represents external professional URL reference.

Supports existing exact administrative editing model:

```text
number
description
URL
```

---

# 61. ProfessionalLink conceptual fields

```text
id
profile_id
link_type
number/order
description/localized description
canonical_url
visibility scopes
link health status
created_at
```

---

# 62. Link Types

Examples:

```text
VIDEO
AUDIO
EXTERNAL_PROFILE
DOCUMENT
SHOWREEL
VIDEO_INTRO
OTHER_PROFESSIONAL
```

Exact taxonomy later.

---

# 63. ProfessionalLink vs MediaAsset

Use conceptual distinction:

```text
MediaAsset
= product manages original/source media

ProfessionalLink
= product references external/stable linked resource
```

A video MAY have both local MediaAsset and public link projection depending implementation.

---

# 64. Link Health Is Derived Operational State

Reachability result:

```text
reachable
temporarily_unreachable
invalid
unknown
```

is not the professional URL fact itself.

Do not auto-delete link on failed check.

---

# 65. DM-ENT-080 — Contact

Represents a professional communication destination/person.

---

# 66. Contact conceptual fields

```text
id
profile_id
contact_type
person_name/label
relationship_role
value
normalized_value
notes
visibility scopes
display_order
lifecycle
```

---

# 67. Contact Types

Potential:

```text
PHONE
EMAIL
WHATSAPP
TELEGRAM
MANAGER
PARENT_GUARDIAN
SOCIAL
OTHER
```

Final vocabulary later.

---

# 68. Contact Visibility Scopes

Canonical independent permissions:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

These MUST remain independently controllable.

---

# 69. Parent / Guardian Contacts

Relationship type:

```text
PARENT_GUARDIAN
```

does not imply public visibility.

Visibility always explicit.

---

# 70. Contact Privacy Invariant

A Contact unavailable in a public context MUST be absent from server public projection, not merely hidden by client CSS.

---

# 71. DM-ENT-090 — QuestionnaireDefinition

Represents a curated questionnaire identity/configuration.

Examples:

```text
Compact
Extended
Casting
Custom
```

---

# 72. QuestionnaireDefinition conceptual fields

```text
id
profile_id
name
type
default_locale
configuration
primary marker
lifecycle
visibility/public availability
created_at
```

---

# 73. QuestionnaireDefinition != Revision

QuestionnaireDefinition represents logical document.

QuestionnaireRevision represents immutable published/saved snapshot according to revision rules.

---

# 74. DM-ENT-091 — QuestionnaireRevision

Canonical immutable questionnaire snapshot.

Conceptually contains/reference-snapshots:

```text
id
questionnaire_id
revision_number
locale
configuration_snapshot
content_snapshot
publication metadata
created_by
created_at
published_at
```

Exact snapshot strategy is DOC-064.

---

# 75. Historical Revision Invariant

Published historical QuestionnaireRevision MUST NOT mutate when Source Profile changes.

---

# 76. Exactly One Primary Public Questionnaire

System MUST support:

```text
one active primary public prepared questionnaire
```

at a time for applicable Profile/context.

---

# 77. Questionnaire Content References

Draft/configuration SHOULD reference Source entities where possible.

Examples:

```text
PortfolioItem
EmotionalGrid
Project
Role
Skill
LanguageProficiency
Training
Achievement
ProfessionalLink
Contact
```

---

# 78. Questionnaire Block Model

At conceptual level questionnaire consists of ordered Blocks.

Examples:

```text
BASIC_INFO
PRIMARY_CLOSEUP
PRIMARY_FULLBODY
PARAMETERS
SKILLS
LANGUAGES
EMOTIONAL
PROJECTS
TRAINING
VIDEO
LINKS
CONTACTS
```

Physical representation may be JSON configuration or normalized entities later.

---

# 79. Questionnaire Item Selection

Within blocks, configuration MUST support item-level selections where business rules require.

---

# 80. Questionnaire QR Configuration

Questionnaire configuration MUST support per-link/context QR visibility.

Conceptually:

```text
show_hyperlink
show_qr
```

for QR-capable links.

---

# 81. DM-ENT-092 — QuestionnaireArtifact

Derived outputs:

```text
HTML projection
PDF
preview artifact
```

PDF MAY be persisted/cached, but its authority derives from QuestionnaireRevision or valid generation snapshot.

---

# 82. Questionnaire PDF Provenance

Generated PDF MUST be traceable to:

```text
Questionnaire/Revision or Builder Generation Snapshot
locale
generation time
source profile
```

---

# 83. DM-ENT-100 — PublicBuilderTemplate

Represents Admin-controlled allowed starting configuration for Public Questionnaire Builder.

Types:

```text
Quick
Standard
Extended
Casting
```

---

# 84. PublicBuilderTemplate conceptual fields

```text
id
profile_id/system scope
name
type
locale support
default block configuration
required blocks
limits
ordering policy
enabled
version
```

---

# 85. Builder Template != Theme

Template defines document content/configuration defaults.

Theme defines visual website presentation.

---

# 86. DM-ENT-101 — PublicBuilderSession

Temporary visitor-owned configuration.

Conceptual:

```text
id
profile_id
template_id/version
locale
configuration
casting_name
project_name
role_name
expires_at
created_at
updated_at
```

---

# 87. PublicBuilderSession Is Not Master Data

It MUST NOT modify:

- ActorProfile;
- Projects;
- Roles;
- Skills;
- Contacts;
- Prepared Questionnaires.

---

# 88. Builder Temporary Casting Context

Fields:

```text
casting_name
project_name
role_name
```

are document/session context only.

They MUST NOT imply professional participation.

---

# 89. Builder Session Authorization Invariant

Session identifier:

```text
!= authorization
```

Every selected entity must remain server-authorized at use/generation time.

---

# 90. DM-ENT-102 — BuilderGenerationSnapshot

At PDF generation time, system SHOULD create a stable normalized snapshot of valid selection.

Purpose:

```text
consistent output
traceability
protection from mid-render source changes
```

---

# 91. Builder Snapshot Is Not Prepared Questionnaire

It is temporary generated package state.

---

# 92. DM-ENT-110 — QRConfiguration

Conceptual configuration describing whether/how an approved URL is represented as QR in a specific document/context.

May physically live inside Questionnaire configuration.

Domain concept MUST exist even if not separate DB table.

---

# 93. DM-ENT-111 — QRArtifact

Derived QR representation keyed by conceptually:

```text
canonical_url
+
QR settings
```

---

# 94. QRArtifact Invariant

QR generation MUST include decode verification:

```text
decoded URL
===
canonical configured URL
```

---

# 95. QR Is Derived

QR image MUST NOT become source URL authority.

Changing canonical URL invalidates/regenerates QRArtifact.

---

# 96. QR Safe Target Invariant

Public QR MUST NOT encode:

- `/admin`;
- private media URL;
- authenticated preview;
- temporary internal storage URL;
- unsafe scheme;
- inaccessible resource.

---

# 97. DM-ENT-120 — Feedback

Represents persisted external professional inquiry.

Feedback is the authoritative record of an inbound professional communication.

---

# 98. Feedback Types

Canonical conceptual types:

```text
CASTING_INVITATION
ROLE_OFFER
COLLABORATION
QUESTIONNAIRE_REQUEST
MATERIALS_REQUEST
QUESTION
COMMENT
OTHER
```

---

# 99. Feedback conceptual fields

```text
id
profile_id
type
sender_name
organization
reply contact
subject/project context
message
source_page/context
questionnaire context
builder_session/snapshot reference where allowed
attribution
status
responsible
next_action
created_at
```

---

# 100. Feedback Persistence Invariant

Canonical order:

```text
Validate
→ Persist Feedback
→ Commit
→ Trigger Notifications
```

Never:

```text
Send WhatsApp
→ if successful then save Feedback
```

---

# 101. Feedback != Casting

Professional inquiry does NOT automatically become Casting.

---

# 102. DM-ENT-121 — FeedbackAttachment

Private attachment linked to Feedback.

Conceptually:

```text
id
feedback_id
secure media/storage reference
filename
mime
size
security scan/status
```

---

# 103. FeedbackAttachment Privacy

Must not automatically become public MediaAsset.

It MAY reuse underlying secure media storage infrastructure while remaining private operational content.

---

# 104. DM-ENT-122 — FeedbackNote

Internal administrative note.

Always private.

---

# 105. DM-ENT-123 — FeedbackCastingLink

Conceptual bidirectional relation between Feedback and Casting.

May physically be FK or relation entity depending cardinality.

Required behaviour:

```text
Feedback → Casting
Casting → Source Feedback
```

---

# 106. DM-ENT-130 — Casting

Represents professional opportunity under evaluation.

---

# 107. Casting conceptual fields

```text
id
profile_id
title
project/context
role context
source type
deadline
status
source_feedback_id optional
next_action
created_at
closed_at
```

---

# 108. Casting Does Not Mean Qualified

A Casting can exist before complete review.

Qualification belongs business workflow/human decision.

---

# 109. DM-ENT-131 — CastingSource

Preserves original incoming casting information.

Sources MAY include:

```text
PASTED_TEXT
DOCUMENT
IMAGE
EMAIL/INQUIRY
MANUAL
OTHER
```

---

# 110. CastingSource conceptual fields

```text
id
casting_id
source_type
original_text
source metadata
created_at
```

Original source SHOULD be preserved separately from AI extraction.

---

# 111. DM-ENT-132 — CastingSourceAsset

Private files/images associated with CastingSource.

Not Public Portfolio media.

---

# 112. DM-ENT-133 — CastingAnalysis

Logical AI-assisted analysis identity/current process.

A Casting MAY have multiple analysis revisions.

---

# 113. DM-ENT-134 — CastingAnalysisRevision

Immutable/versioned result of one analysis pass.

Contains/reference:

```text
source_snapshot
model/prompt metadata
extracted requirements
profile comparison snapshot
recommendations
created_at
```

---

# 114. DM-ENT-135 — CastingRequirement

Represents a requirement extracted or manually recorded for Casting.

Conceptual:

```text
id
analysis_revision/casting
requirement_type
source_evidence
extracted_value
confirmation_state
confirmed_value
human note
```

---

# 115. Missing Casting Requirement

If source does not provide a requirement:

canonical value/state:

```text
Не указано
```

No invention.

---

# 116. DM-ENT-136 — ProfileMatchResult

Derived comparison:

```text
CastingRequirement
vs
confirmed ActorProfile data
```

It is not a professional fact.

---

# 117. Match States

Conceptually:

```text
MATCH
PARTIAL
NO_CONFIRMED_DATA
MISMATCH
NOT_APPLICABLE
```

Exact semantics later.

---

# 118. No Confirmed Data != Mismatch

Critical invariant.

If Profile does not contain a skill:

```text
NO_CONFIRMED_DATA
```

not automatically:

```text
MISMATCH
```

---

# 119. DM-ENT-137 — CastingRecommendation

AI/system recommendation regarding:

- relevant evidence;
- missing material;
- questionnaire composition;
- follow-up.

Recommendation requires human review.

---

# 120. DM-ENT-138 — CastingHumanDecision

Conceptual record of explicit human confirmation where decision traceability is needed.

Examples:

```text
requirement confirmed
recommendation accepted/rejected
qualification decision
```

May physically appear as state + audit events rather than generic table.

Domain concept must remain distinguishable from AI recommendation.

---

# 121. Casting AI Boundary

Canonical pipeline:

```text
Casting Source
      ↓
AI Extraction
      ↓
Human Confirmation
      ↓
Confirmed Casting Requirements
      ↓
Profile Match
      ↓
AI/System Recommendation
      ↓
Human Decision
```

---

# 122. DM-ENT-140 — Opportunity

Represents an actual professional opportunity tracked through business outcome.

---

# 123. Opportunity conceptual fields

```text
id
profile_id
casting_id
source_feedback_id optional
current_stage
owner/responsible
next_action
next_action_at
outcome metadata
created_at
closed_at
```

---

# 124. Canonical Opportunity Stages

```text
New
Qualified
Materials Requested
Self-Tape Requested
Audition
Callback
Offer
Booked
Closed — Not Selected
Withdrawn
```

Exact state machine belongs DOC-062.

---

# 125. Offer != Booked

Canonical invariant.

---

# 126. Negative Outcomes Are Valid Data

```text
Closed — Not Selected
Withdrawn
```

are meaningful business outcomes, not technical errors.

---

# 127. DM-ENT-141 — OpportunityStageHistory

Immutable record of stage transitions.

Conceptually:

```text
opportunity_id
from_stage
to_stage
reason/context
actor
changed_at
```

---

# 128. DM-ENT-142 — OpportunityNote / Action

Operational notes/next-action records MAY be represented separately where workflow needs history.

---

# 129. Booked → Professional History

A Booked Opportunity MAY create:

```text
Project Draft
Role Draft
```

using confirmed known data.

It MUST NOT automatically publish acting history.

---

# 130. DM-ENT-150 — Notification

Represents a system-generated notification intent/message.

Examples:

```text
new Feedback
deadline
system issue
processing result
```

---

# 131. Notification != Feedback

Feedback is professional inbound business record.

Notification is delivery/attention mechanism.

---

# 132. DM-ENT-151 — NotificationDelivery

Represents delivery attempt through a channel.

Channels MAY include:

```text
IN_APP
WHATSAPP
EMAIL
OTHER_APPROVED
```

---

# 133. NotificationDelivery conceptual fields

```text
id
notification_id
channel
status
attempt_count
scheduled_at
sent_at
last_error
idempotency key/reference
```

---

# 134. Notification Status Does Not Control Business Truth

If NotificationDelivery fails:

```text
Feedback still exists
Casting still exists
Opportunity still exists
```

---

# 135. Quiet Hours

Quiet hours belong Notification delivery configuration, not Feedback lifecycle.

---

# 136. DM-ENT-160 — AIDraft

Canonical entity for **BB Assistant** generated professional text.

---

# 137. AIDraft conceptual fields

Canonical proposed model:

```text
id
profile_id
context_entity_type
context_entity_id
task_type
language
tone
length
source_snapshot
generated_text
edited_text/current draft
model_metadata
prompt_version
status
created_by
created_at
applied_at
```

---

# 138. BB Assistant task types

Canonical:

```text
BIOGRAPHY
PORTFOLIO_DESCRIPTION
PROJECT_DESCRIPTION
COURSE_DESCRIPTION
COVER_LETTER
CASTING_RESPONSE
ROLE_RESPONSE
QUESTIONNAIRE_TEXT
SOCIAL_CAPTION
CUSTOM
```

---

# 139. AIDraft statuses

Canonical:

```text
GENERATED
EDITED
APPLIED
DISCARDED
```

Additional technical:

```text
FAILED
STALE
```

MAY be represented as processing/current states.

---

# 140. Apply != Publish

Critical invariant:

```text
AIDraft Applied
≠
Target Published
≠
Message Sent
```

---

# 141. BB Source Constraint

AIDraft MUST be based on permitted saved Profile/context facts.

No invented:

- roles;
- skills;
- awards;
- training;
- experience.

---

# 142. DM-ENT-170 — VOPObservation

Virtual Operator observation of a system/content condition.

---

# 143. Observation conceptual fields

```text
id
entity_type
entity_id
issue_type
severity
reason/evidence
detected_at
affected_journeys
status
```

---

# 144. Observation != Recommendation

Observation:

```text
Showreel URL failed validation
```

Recommendation:

```text
Replace or disable link
```

These are different concepts.

---

# 145. DM-ENT-171 — VOPRecommendation

System suggestion associated with Observation or optimization.

Conceptual:

```text
id
observation_id
recommended_action
reason
source_version
status
created_at
```

---

# 146. Recommendation statuses

Conceptually:

```text
OPEN
APPROVED
MODIFIED
REJECTED
DEFERRED
SUPERSEDED
```

---

# 147. DM-ENT-172 — AutomationExecution

Records safe automatic operation.

Examples:

```text
thumbnail regeneration
retry
search reindex
preview rebuild
QR regeneration
```

Conceptual:

```text
id
action_type
target
trigger
idempotency reference
status
started_at
finished_at
error
```

---

# 148. Virtual Operator Is Not Source of Truth

It observes, recommends and executes deterministic safe operations.

It does not own professional facts.

---

# 149. DM-ENT-180 — SiteTheme

Logical visual theme identity.

---

# 150. SiteTheme conceptual fields

```text
id
name
current lifecycle
created_by
created_at
```

Theme content belongs revisions/configurations.

---

# 151. DM-ENT-181 — ThemeRevision

Versioned structured visual configuration.

May include:

```text
design tokens
typography
background
hero treatment
decoration
component overrides within guardrails
accessibility validation result
```

---

# 152. ThemeRevision Must Not Contain Actor Professional Facts

Theme changes presentation only.

---

# 153. DM-ENT-182 — ThemeProposal

AI-generated proposed configuration.

It is not a published ThemeRevision until human adoption/publish workflow.

---

# 154. DM-ENT-183 — ThemeActivation

Represents active revision/context/time range.

Useful to model:

```text
permanent active theme
temporary activation
scheduled/expiry where allowed
```

---

# 155. Theme Publication Boundary

Theme AI MAY propose.

Only human-authorized action activates public Theme.

---

# 156. DM-ENT-190 — AnalyticsEvent

Represents privacy-approved behavioural/product event.

Examples:

```text
profile_viewed
video_started
questionnaire_viewed
public_builder_opened
public_builder_pdf_generated
professional_inquiry_started
```

---

# 157. AnalyticsEvent Is Not Business Authority

Examples:

```text
casting_cta_started
```

does not mean Casting exists.

```text
professional_inquiry_started
```

does not mean Feedback persisted.

---

# 158. Server Business Events Override Client Analytics

For critical conversion:

```text
Feedback persisted
Casting created
Opportunity stage changed
Booked
```

authoritative business storage is source of truth.

Analytics may mirror it.

---

# 159. DM-ENT-191 — AttributionContext

Conceptually captures acquisition/source classification.

Canonical sources include:

```text
direct
search
social
referral
campaign
shared_profile
shared_questionnaire
shared_project
casting_specific_link
qr
other
```

---

# 160. Source != Persona

Attribution:

```text
Instagram
```

does not prove visitor is Commercial Producer.

No hidden persona classification is required.

---

# 161. DM-ENT-192 — ConversionEvent / BusinessAnalyticsProjection

MAY be derived from authoritative entities.

Examples:

```text
Qualified Inquiry
Casting Created
Offer
Booked
```

Prefer deriving from Feedback/Casting/Opportunity records rather than creating competing truth.

---

# 162. DM-ENT-200 — SocialAccountConnection

Represents connected external social publishing account.

Conceptually:

```text
id
platform
account identifier
connection status
credential reference
connected_at
expires_at
```

Secrets themselves belong secure credential storage, not ordinary domain payload.

---

# 163. DM-ENT-201 — SocialPost

Represents prepared social publication.

Conceptually:

```text
id
profile_id
text
media relations
target platforms
status
created_at
```

---

# 164. DM-ENT-202 — SocialPublishAttempt

Tracks external publication attempt/result.

---

# 165. BB Social Caption

BB Assistant may create AIDraft with:

```text
task_type = SOCIAL_CAPTION
```

Then human applies it to SocialPost.

---

# 166. DM-ENT-210 — HelpArticle

Existing Help Center content.

Conceptually:

```text
id
locale
title
body/content
category
status
```

---

# 167. DM-ENT-211 — SupportTicket

Existing Admin support workflow.

Conceptually:

```text
id
creator
subject
category
status
priority
context
created_at
```

---

# 168. DM-ENT-212 — SupportTicketMessage

Conversation/history inside SupportTicket.

---

# 169. SupportTicket != Professional Feedback

Important distinction:

```text
Feedback
= external professional business communication

SupportTicket
= system/product support communication
```

---

# 170. DM-ENT-220 — AdminUser

Represents authenticated administrative identity.

Detailed security/role model later.

Conceptually:

```text
id
identity provider/login relation
display name
status
```

---

# 171. DM-ENT-221 — AdminRole / Permission

Conceptual authorization model.

Current product MAY have one primary owner/admin, but domain architecture MUST NOT make permissions impossible.

---

# 172. Persona != AdminRole

Casting Director is public Persona, not authenticated security role.

Virtual Operator is system actor, not human AdminRole.

---

# 173. DM-ENT-230 — AuditEvent

Immutable record of significant system/business change.

Conceptual:

```text
id
actor_type
actor_id
action
entity_type
entity_id
before/after or structured delta
source context
timestamp
correlation id
```

---

# 174. Audit Actor Types

Canonical conceptual values:

```text
human
system
virtual-operator
casting-ai
bb-assistant
theme-ai
migration
```

---

# 175. Audit != Analytics

Audit:

```text
who changed what and when
```

Analytics:

```text
how product/professional funnel is used/performs
```

Never conflate them.

---

# 176. Audit Immutability

AuditEvent SHOULD be append-only.

---

# 177. What Is NOT a Standalone Source Entity

The following SHOULD generally remain projections/computed views unless later architecture establishes compelling reason:

```text
Casting Quick View
Public Home Page
Public Search Result
Readiness Score
Dashboard Card
Portfolio Filter Result
Questionnaire Preview
Builder Preview
Conversion Funnel Stage Visualization
```

---

# 178. Casting Quick View

It is a projection of:

```text
ActorProfile
Portfolio
Skills
Languages
Video
Questionnaire
```

not a manually maintained duplicate record.

---

# 179. Search Index

Search index is Derived/Projection infrastructure.

It is NOT Master Data.

---

# 180. Dashboard Alert Card

UI representation of:

```text
VOPObservation
Feedback
Casting deadline
System condition
```

not its own business entity.

---

# 181. Readiness

Readiness is preferably computed from deterministic business rules.

A cached readiness projection MAY exist technically.

It MUST be reproducible from authoritative data.

---

# 182. Public Profile

Public Profile is projection:

```text
ActorProfile
+
allowed related entities
+
locale
+
visibility rules
```

---

# 183. Public Projection Boundary

Canonical:

```text
Master Data
        ↓
Visibility / Authorization
        ↓
Public Data Aggregation Layer
        ↓
Public Projection
```

---

# 184. Public Projection Is Server-Authoritative

Client MUST NOT receive all data and determine privacy locally.

---

# 185. Visibility Is Contextual

An entity can have different eligibility for:

```text
Public Site
Prepared Questionnaire
Public Builder
Admin
Casting
```

without duplicating entity.

---

# 186. Domain Visibility Model

Exact implementation later, but entity/configuration model MUST support:

```text
context-specific visibility/eligibility
```

instead of a single overloaded boolean `public`.

---

# 187. Published != Public

Canonical invariant:

```text
Published
```

may mean a revision is formally committed.

Public availability additionally depends on:

- visibility;
- context;
- access policy.

---

# 188. Archived != Deleted

Archived content remains part of history/domain.

Deleted/removal semantics defined later.

---

# 189. Draft != Historical Revision

Draft is current editable working state.

Historical Revision is immutable intentional past state.

---

# 190. Stale Is Not Lifecycle Entity State

`Stale` often describes a derived representation relative to changed source.

It should not be blindly applied as universal lifecycle state to every source entity.

---

# 191. Canonical Source Ownership Groups

Actor-owned professional data:

```text
ActorProfile
PortfolioItem
EmotionalSession/Grid
Project/Role
Training
Achievement
ActorSkill
ActorLanguageProficiency
ProfessionalLink
Contact
QuestionnaireDefinition
```

Operational business data:

```text
Feedback
Casting
Opportunity
Notification
```

System/assistive:

```text
AIDraft
VOPObservation
ThemeProposal
AnalyticsEvent
AuditEvent
```

---

# 192. Domain Relationship Principle

Relationships SHOULD prefer stable identifiers and explicit semantics.

Avoid hidden relationship encoded only inside human text.

Example bad:

```text
Role description:
"Role in Project A"
```

Correct:

```text
role.project_id = Project A
```

---

# 193. Media Relationship Principle

Professional content references MediaAsset.

Do not duplicate file per:

- Portfolio;
- Project;
- Role;
- Questionnaire.

---

# 194. Questionnaire Relationship Principle

Questionnaire Draft references professional entities/configuration.

Published Revision freezes necessary snapshot.

---

# 195. Builder Relationship Principle

Builder Session references only eligible entities temporarily.

Generation performs current server revalidation.

---

# 196. Feedback Relationship Principle

Feedback may reference:

- source public page;
- Questionnaire;
- Builder Session/Generation;
- attribution context.

These relations reduce repeated user input.

---

# 197. Casting Relationship Principle

Casting may originate from:

```text
Feedback
Manual creation
Other source
```

but source provenance should remain traceable.

---

# 198. Opportunity Relationship Principle

Opportunity SHOULD link back to:

```text
Casting
```

and, where applicable:

```text
Feedback
```

for full business history.

---

# 199. Booked Feedback Loop

Canonical professional lifecycle:

```text
Feedback
→ Casting
→ Opportunity
→ Booked
→ Project Draft
→ Role Draft
→ Published Experience
```

Every transition preserves provenance.

---

# 200. AI Provenance Principle

Every persistent meaningful AI output SHOULD be traceable to:

```text
AI capability
source context/snapshot
prompt/version
model metadata where policy permits
created_at
human decision
```

---

# 201. AI Separation

Four distinct AI capabilities:

```text
1. Virtual Operator
2. Casting AI
3. Theme AI
4. BB Assistant
```

They MUST NOT share ambiguous domain ownership.

---

# 202. Virtual Operator

Owns:

```text
observations
recommendations
safe automation execution records
```

Does not own professional content.

---

# 203. Casting AI

Owns:

```text
analysis
extractions
match projections
recommendations
```

Does not own confirmed Profile facts.

---

# 204. Theme AI

Owns:

```text
visual proposal
```

until human adopts/publishes.

---

# 205. BB Assistant

Owns:

```text
professional text drafts
```

until explicitly applied to target Draft.

---

# 206. DM-INV-001 — Single Source Fact

A confirmed professional fact MUST NOT have independent writable copies across modules.

---

# 207. DM-INV-002 — Immutable Original Media

Original uploaded MediaAsset is preserved.

---

# 208. DM-INV-003 — Derived Artifacts Regenerable

Derived media/document artifacts should be reconstructible from source/configuration where practical.

---

# 209. DM-INV-004 — Portfolio Does Not Own Binary

PortfolioItem references MediaAsset.

---

# 210. DM-INV-005 — Emotional Session Has Shooting Date

Mandatory.

---

# 211. DM-INV-006 — Emotional Grid Uses Allowed Dimensions Only

Normative.

---

# 212. DM-INV-007 — Emotional Grid Cell Count Exact

Normative.

---

# 213. DM-INV-008 — Emotional Grid Never Alters Appearance

Normative.

---

# 214. DM-INV-009 — Project != Casting

Normative.

---

# 215. DM-INV-010 — Training != Project

Normative.

---

# 216. DM-INV-011 — Role Belongs to Project

Normative.

---

# 217. DM-INV-012 — CEFR Is Explicit

No unconfirmed AI inference.

---

# 218. DM-INV-013 — Contact Visibility Independent by Context

Normative.

---

# 219. DM-INV-014 — Hidden Public Data Is Server-Excluded

Normative.

---

# 220. DM-INV-015 — QuestionnaireDefinition != Revision

Normative.

---

# 221. DM-INV-016 — Published Questionnaire Revision Immutable

Normative.

---

# 222. DM-INV-017 — Exactly One Primary Public Questionnaire

For applicable active public context.

---

# 223. DM-INV-018 — Public Builder Does Not Modify Profile

Normative.

---

# 224. DM-INV-019 — Builder Permission Revalidated

Immediately before protected output generation.

---

# 225. DM-INV-020 — Builder Session ID Is Not Authorization

Normative.

---

# 226. DM-INV-021 — QR Encodes Canonical Public Target

Normative.

---

# 227. DM-INV-022 — QR Exact Decode Equality

Normative.

---

# 228. DM-INV-023 — QR Never Points to Private/Admin URL

Normative.

---

# 229. DM-INV-024 — Feedback Persists Before Notifications

Normative.

---

# 230. DM-INV-025 — Feedback != Casting

Normative.

---

# 231. DM-INV-026 — AI Extraction != Confirmed Requirement

Normative.

---

# 232. DM-INV-027 — Missing Casting Requirement = Not Specified

No invention.

---

# 233. DM-INV-028 — No Profile Data != Mismatch

Normative for Casting Profile Match.

---

# 234. DM-INV-029 — Offer != Booked

Normative.

---

# 235. DM-INV-030 — Pipeline Stage Is Business State

Not cosmetic UI state.

---

# 236. DM-INV-031 — Notification Failure Does Not Roll Back Business Record

Normative.

---

# 237. DM-INV-032 — BB Draft Never Auto-Publishes

Normative.

---

# 238. DM-INV-033 — BB Draft Never Auto-Sends

Normative.

---

# 239. DM-INV-034 — Theme Does Not Own Content

Normative.

---

# 240. DM-INV-035 — Theme Publish Requires Human Authority

Normative.

---

# 241. DM-INV-036 — Analytics Does Not Define Business Truth

Normative.

---

# 242. DM-INV-037 — Analytics Cannot Auto-Reorder Professional Content

Normative.

---

# 243. DM-INV-038 — Audit Is Distinct From Analytics

Normative.

---

# 244. DM-INV-039 — AI Actor Is Auditable

Persistent significant AI actions are attributable to the correct AI capability.

---

# 245. DM-INV-040 — Public Persona != Security Role

Normative.

---

# 246. Domain Model and Deletion

Detailed deletion/retention belongs DOC-094.

At model level distinguish:

```text
archive
soft removal
hard deletion
derived artifact cleanup
temporary session expiry
```

---

# 247. Source Deletion Is High Impact

Deleting Source entities with dependencies requires explicit dependency handling.

---

# 248. Derived Cleanup Is Lower Risk

Example:

```text
thumbnail
QR cache
preview
```

can generally be regenerated and may follow automated retention.

---

# 249. Temporary Builder Data

PublicBuilderSession SHOULD expire and be cleaned according to retention policy.

---

# 250. Historical Questionnaire Data

Published Revision MUST obey retention/history policy and not be cleaned merely because source changed.

---

# 251. Casting Source Retention

Original Casting Source should remain traceable for analysis history according to retention policy.

---

# 252. Feedback Retention

Feedback is business communication and follows business/privacy retention, not temporary form/session policy.

---

# 253. Domain Model and Localization

Localized narrative MUST be separated conceptually from language-neutral structured facts.

---

# 254. Language-Neutral Facts

Examples:

```text
date
CEFR=B2
height numeric value
Project relation
```

---

# 255. Localized Fields

Examples:

```text
Biography
Project Description
Role Description
Training Description
ProfessionalLink Description
```

---

# 256. Translation Does Not Create New Professional Fact

RU and EN representation of same Project title/description belong same Project identity.

---

# 257. AI Translation

AI-generated translation remains Draft until human-approved/applied where professional semantics matter.

---

# 258. Domain Model and Search

Search SHOULD index only eligible projection.

Search does not create entities.

---

# 259. Domain Model and Public Quick View

Quick View is projection composed from:

```text
ActorProfile
Primary Portfolio
Skills
Languages
Professional Links/Video
Questionnaire availability
```

---

# 260. Domain Model and PDF

PDF is derived document, not a data-editing source.

A PDF upload should not silently update Profile facts unless explicit import workflow with review exists.

---

# 261. Domain Model and Import

Future/manual import follows:

```text
Source Document
→ Extracted Candidate Data
→ Human Review
→ Master Data
```

Never direct uncontrolled overwrite.

---

# 262. Domain Model and Migration

Migration records MAY be introduced technically to preserve provenance.

Example:

```text
Legacy Project "Other"
→ Training
```

must be traceable where meaningful.

---

# 263. Domain Model and Version IDs

Entities requiring concurrency control SHOULD support logical version/revision indicator.

Exact mechanism later:

```text
version integer
updated_at
etag/hash
```

---

# 264. Domain Model and Idempotency

Actions likely requiring idempotency:

```text
Feedback submission
Casting creation from Feedback
PDF generation job
QR generation
Notification delivery
Migration
Background job execution
Analytics server event creation where critical
```

Idempotency token is infrastructure/application concept, not necessarily entity.

---

# 265. Domain Model and Security

Authorization is evaluated against:

```text
actor identity
entity
operation
context
```

not client visibility state.

---

# 266. Public Identifiers

Public URLs SHOULD use stable safe identifiers/slugs/tokens.

Database sequential ID MUST NOT itself be treated as access control.

---

# 267. Domain Model and Public Slugs

Slugs are routing identifiers.

Changing slug does not create new Project/Questionnaire entity.

Redirect/history policy later.

---

# 268. Domain Model and Readiness

Readiness should be represented conceptually as:

```text
target action
+
deterministic validation result
+
blocking/warning reasons
```

not opaque actor quality score.

---

# 269. No Actor Ranking Entity

Product MUST NOT introduce:

```text
Actor attractiveness score
Casting desirability score
Overall actor ranking
```

as part of this single-profile professional platform.

---

# 270. No Public User Account Required

PublicBuilderSession and Feedback do not depend on PublicUser entity.

Current target explicitly avoids mandatory public registration.

---

# 271. Future Public Account Compatibility

If future scope introduces accounts, it must extend, not redefine, core anonymous professional journey.

Not current Scope.

---

# 272. No Casting Marketplace Entity

Product manages the actress's own casting workflow.

It is not a multi-actor/multi-agency casting marketplace.

---

# 273. No Contract/Payment Domain

Payments, contracts, signatures and financial settlements are outside current Scope.

---

# 274. No Autonomous Agent Domain

AI capabilities are constrained assistive systems.

No entity should imply independent autonomous professional agent with authority to accept role/casting.

---

# 275. Entity Criticality

## Tier A — Core Master Data

```text
ActorProfile
MediaAsset
PortfolioItem
EmotionalSession/Grid
Project/Role
Training
Skill/Language
Contact
QuestionnaireDefinition/Revision
```

## Tier B — Core Business Operations

```text
Feedback
Casting
Opportunity
```

## Tier C — Supporting Operations

```text
Notification
AIDraft
VOPObservation
Theme
SocialPost
SupportTicket
```

## Tier D — Derived / Analytics

```text
MediaDerivative
QRArtifact
PDF
AnalyticsEvent
Search Index
Preview
```

---

# 276. Persistence Criticality

Tier A/B loss is severe.

Tier D SHOULD generally be regenerable/reconstructible where technically practical.

---

# 277. Aggregate Boundaries — Preliminary

Detailed relationships in DOC-061, but canonical aggregate ownership is:

```text
ActorProfile
  owns professional identity context

EmotionalSession
  owns EmotionalGrid context

Project
  owns Role/Performance context

QuestionnaireDefinition
  owns QuestionnaireRevision context

PublicBuilderSession
  owns temporary custom package config

Feedback
  owns inbound message/attachment/note context

Casting
  owns CastingSource/Analysis context

Opportunity
  owns stage history

Notification
  owns delivery attempts

SiteTheme
  owns revisions/activation context

SupportTicket
  owns ticket messages
```

---

# 278. Aggregate Does Not Mean Cascade Delete

Aggregate ownership does not automatically define destructive database cascades.

Deletion policy belongs DOC-094.

---

# 279. Domain Events — Conceptual

Important source/business changes SHOULD conceptually emit domain events.

Examples:

```text
ProfileFactChanged
MediaUploaded
PortfolioPrimaryChanged
EmotionalGridFinalized
ProjectPublished
QuestionnairePublished
ContactVisibilityChanged
FeedbackCreated
CastingCreated
CastingAnalysisCompleted
OpportunityStageChanged
OpportunityBooked
ThemePublished
```

---

# 280. Domain Events Are Not Analytics

Domain event drives internal consistency/workflow.

Analytics event observes product/business behaviour.

Some may be mirrored, but purposes differ.

---

# 281. Domain Events and Dependencies

Example:

```text
ContactVisibilityChanged
        ↓
invalidate Builder Projection
        ↓
recalculate Questionnaire readiness
        ↓
invalidate dynamic preview
```

---

# 282. Domain Events and Historical Revisions

Source event MUST NOT rewrite immutable historical Revision.

---

# 283. Domain Events and VOP

Virtual Operator MAY consume domain/system events to create observations.

---

# 284. Domain Events and Notifications

FeedbackCreated MAY trigger Notification creation after persistence.

---

# 285. Domain Events and Search

Source publication/change MAY trigger search reindex.

---

# 286. Domain Events and QR

ProfessionalLink URL change MAY invalidate dependent QRArtifact.

---

# 287. Domain Events and PDF

Questionnaire Draft source changes MAY mark preview/current draft-derived artifact stale.

Published historic PDF/revision remains intact.

---

# 288. Canonical Domain Flow — Professional Content

```text
MediaAsset
   ↓
Professional Classification
   ↓
Portfolio / Project / Emotional
   ↓
Questionnaire Configuration
   ↓
Revision / Builder Snapshot
   ↓
PDF / QR / Public Projection
```

---

# 289. Canonical Domain Flow — Casting

```text
Feedback / Manual Source
       ↓
     Casting
       ↓
 CastingSource
       ↓
AI Analysis Revision
       ↓
Extracted Requirement
       ↓
Human Confirmation
       ↓
Profile Match
       ↓
Recommendation
       ↓
Human Business Decision
       ↓
Opportunity
```

---

# 290. Canonical Domain Flow — Booked

```text
Opportunity
   ↓
Offer
   ↓
Booked
   ↓
Project Draft
   ↓
Role Draft
   ↓
Human Review
   ↓
Published Professional Experience
```

---

# 291. Canonical Domain Flow — BB Assistant

```text
Target Entity
    +
Allowed Source Facts
       ↓
Source Snapshot
       ↓
AIDraft
       ↓
Human Edit
       ↓
Apply
       ↓
Target Draft
       ↓
Human Save / Publish / Send
```

---

# 292. Canonical Domain Flow — Theme AI

```text
Current Theme / Prompt
       ↓
ThemeProposal
       ↓
Human Review/Edit
       ↓
ThemeRevision Draft
       ↓
Accessibility Validation
       ↓
Human Publish
       ↓
ThemeActivation
```

---

# 293. Canonical Domain Flow — VOP

```text
Domain/System Event
       ↓
Observation
       ↓
Severity / Journey Impact
       ↓
Recommendation
       ↓
AUTO-4 Safe Execution
          OR
Human Decision
       ↓
Resolution
```

---

# 294. Data Normalization Principle

Normalize concepts that have independent:

- identity;
- lifecycle;
- reuse;
- permissions;
- history.

Avoid over-normalizing purely presentational details.

---

# 295. JSON Configuration Principle

Structured JSON MAY be appropriate for:

- Questionnaire layout/config;
- Builder session configuration;
- Theme tokens;
- crop parameters;

provided:

- schema/version exists;
- server validates;
- referential integrity requirements are handled;
- critical entities are not hidden as uncontrolled blobs.

---

# 296. Polymorphic Relations

Use cautiously.

Generic:

```text
entity_type + entity_id
```

may be appropriate for:

- Audit;
- VOP Observation;
- AI context;

but core professional domain SHOULD prefer explicit typed relationships when practical.

---

# 297. Enumeration Governance

Enums such as:

- Feedback Type;
- Questionnaire Type;
- Opportunity Stage;
- Media Type;

MUST have centrally documented canonical values.

Do not replicate inconsistent string variants.

---

# 298. Time Semantics

Domain model MUST distinguish:

```text
created_at
updated_at
shooting_date
event/performance_date
generated_at
published_at
deadline
expires_at
```

They are not interchangeable.

---

# 299. Shooting Date != Upload Date

Critical professional distinction.

---

# 300. Generated Date != Published Date

Questionnaire/PDF may be generated independently of content publication date.

---

# 301. Deadline != Next Action Date

Casting deadline and internal follow-up time are distinct.

---

# 302. Identity Semantics

Every first-class entity SHOULD have stable internal identifier.

Public-facing slug/token is additional projection/routing identity.

---

# 303. Display Name != Identifier

Titles/names can change without changing entity identity.

---

# 304. Domain Model Accessibility Metadata

Accessibility-relevant content MAY belong to media/content entities:

```text
localized alt text
caption
transcript relation
caption track
```

Exact data model later.

---

# 305. Accessibility Metadata Is Content, Not Decoration

It SHOULD follow:

- localization;
- visibility;
- revision rules;

where applicable.

---

# 306. Domain Model SEO Metadata

SEO fields MAY be modeled as projection/configuration attributes on publishable entities.

They MUST NOT duplicate professional facts unnecessarily.

---

# 307. Domain Model Blog

If existing Blog capability is confirmed/preserved, it SHOULD be treated as secondary Content context with conceptual entities:

```text
BlogPost
BlogPostLocalizedContent
```

Blog is not core Casting domain.

Detailed module may be added if implementation audit requires dedicated documentation.

---

# 308. Existing Multilingual Capability

All current professional publishable domains MUST remain capable of localized presentation according to product language rules.

---

# 309. Existing HACS-like Irrelevant Concepts

No unrelated infrastructure concepts should enter this product's domain model merely because underlying stack supports them.

Domain remains product-specific.

---

# 310. Domain Model Completeness Check

The model MUST support all core target requirements:

- [x] Actor Profile
- [x] Main Portfolio
- [x] Media Library
- [x] Emotional Portfolio
- [x] Emotional Grid
- [x] Projects
- [x] Roles
- [x] Role-specific media
- [x] Performances
- [x] Training
- [x] Achievements
- [x] Skills
- [x] CEFR Languages
- [x] Video/Audio/Links
- [x] Contacts
- [x] Prepared Questionnaires
- [x] Questionnaire Revisions
- [x] Public Builder Templates
- [x] Public Builder Sessions
- [x] PDF artifacts
- [x] QR subsystem
- [x] Feedback/Inbox
- [x] Feedback Attachments
- [x] Castings
- [x] Casting Source
- [x] Casting AI
- [x] Requirements
- [x] Profile Match
- [x] Recommendations
- [x] Opportunity Pipeline
- [x] Notifications
- [x] BB Assistant
- [x] Virtual Operator
- [x] Theme AI / Themes
- [x] Analytics / Attribution
- [x] Social Publishing
- [x] Help/Tickets
- [x] Admin Identity
- [x] Audit

---

# 311. Domain Model Anti-Pattern DM-AP-001

**Questionnaire owns duplicate Profile facts**

---

# 312. DM-AP-002

**Portfolio stores copied image files**

---

# 313. DM-AP-003

**Emotional Grid overwrites original photos**

---

# 314. DM-AP-004

**Castings stored as Projects**

---

# 315. DM-AP-005

**Training stored as Project type Other indefinitely**

---

# 316. DM-AP-006

**Contact has one generic public boolean**

despite independent Questionnaire/Builder requirements.

---

# 317. DM-AP-007

**Historical Questionnaire queries live Profile at render time**

and silently changes old revision.

---

# 318. DM-AP-008

**Public Builder Session creates permanent Profile data**

---

# 319. DM-AP-009

**QR image is treated as URL authority**

---

# 320. DM-AP-010

**Failed WhatsApp delivery deletes/rolls back Feedback**

---

# 321. DM-AP-011

**Feedback automatically creates Casting**

---

# 322. DM-AP-012

**Casting AI output directly mutates Actor Profile**

---

# 323. DM-AP-013

**Missing Profile fact is recorded as mismatch**

---

# 324. DM-AP-014

**Offer automatically means Booked**

---

# 325. DM-AP-015

**AI Draft shares same field/state as Published professional copy**

---

# 326. DM-AP-016

**Theme stores copies of Profile content**

---

# 327. DM-AP-017

**Analytics event used as business transaction**

---

# 328. DM-AP-018

**Dashboard card becomes authoritative entity**

---

# 329. DM-AP-019

**Readiness percentage is stored as actor quality fact**

---

# 330. DM-AP-020

**Client visibility state treated as security**

---

# 331. DM-AP-021

**All AI functions use one generic `ai_result` blob**

without task/context/provenance.

---

# 332. DM-AP-022

**All entities have arbitrary generic JSON metadata instead of domain fields**

---

# 333. DM-AP-023

**Every relationship encoded polymorphically**

when explicit typed relation is clearer.

---

# 334. DM-AP-024

**Upload date used as Shooting Date**

---

# 335. DM-AP-025

**Public persona modeled as authenticated role**

---

# 336. Domain Model Quality Gate

Перед переходом сущности к DB/API design должны быть определены:

- [ ] canonical name;
- [ ] domain context;
- [ ] purpose;
- [ ] source/config/derived/operational classification;
- [ ] owner aggregate;
- [ ] key identity;
- [ ] principal attributes;
- [ ] lifecycle concept;
- [ ] visibility context;
- [ ] revision behaviour;
- [ ] relationships;
- [ ] deletion implications;
- [ ] audit requirements;
- [ ] AI authority boundary;
- [ ] public exposure rules.

---

# 337. Entity Naming Rule

Names SHOULD describe business concept.

Prefer:

```text
QuestionnaireRevision
PublicBuilderSession
CastingRequirement
```

over:

```text
DataRecord2
ConfigItem
AIObject
```

---

# 338. Domain Terminology Consistency

Canonical:

```text
Project
Casting
Feedback
Opportunity
Portfolio
Emotional Portfolio
Emotional Grid
Questionnaire
Revision
```

must retain meanings from `terminology.md`.

---

# 339. Future Extension Rule

A new entity SHOULD be created only if concept has sufficiently independent:

```text
identity
lifecycle
rules
permissions
history
relationships
```

Otherwise prefer value object/configuration/projection.

---

# 340. Domain Model vs Database Table Count

One conceptual entity may map to:

- one table;
- multiple tables;
- JSON + table;
- view;

depending technical design.

This document does not force 1:1 entity/table mapping.

---

# 341. Domain Model vs Frontend Component

One entity may have many components.

One component may display several entities.

Neither defines domain ownership.

---

# 342. Domain Model vs Route

Route does not define entity boundary.

Example:

```text
/admin/castings/{id}/ai-analysis
```

is UI route into Casting context.

It does not imply AI Analysis is independent root aggregate.

---

# 343. Domain Model vs AI Prompt

Prompt structure is implementation detail.

Persistent AI source/output concepts remain domain concepts.

---

# 344. Domain Model vs Background Job

Job executes domain operation.

It is not automatically business entity.

`AutomationExecution`/job history may be stored for observability where needed.

---

# 345. Domain Model vs Cache

Cache is replaceable derived infrastructure.

No business truth should exist only in cache.

---

# 346. Domain Model vs Search Index

Search index is reconstructible derived structure.

---

# 347. Domain Model vs File Storage

Database holds logical identity/metadata.

Object/file storage holds binary.

Filesystem path is not domain identity.

---

# 348. Domain Model vs External Provider

Instagram/TikTok/WhatsApp etc. are integrations.

Internal domain entity owns:

```text
connection
intent
attempt
result
```

External provider response does not redefine core business object.

---

# 349. Failure Containment

Optional external failure SHOULD affect only derived/integration entities.

Examples:

```text
WhatsApp failure → NotificationDelivery.failed
not Feedback.deleted

AI outage → CastingAnalysis unavailable
not Casting unavailable

Analytics outage → AnalyticsEvent missing/delayed
not Profile unavailable
```

---

# 350. Domain Model Security Principle

Sensitive operational entities:

```text
Feedback
CastingSource
CastingSourceAsset
Internal Notes
SupportTicket private content
Audit
```

are never part of ordinary Public Profile projection.

---

# 351. Public Package Principle

A professional public package consists only of explicitly allowed projection/snapshot data.

Not a serialization of entire Profile aggregate.

---

# 352. Public Builder Principle

Builder API should conceptually expose:

```text
BuilderProjection
```

not raw database entities with permission flags.

---

# 353. Casting AI Principle

AI receives minimum required Casting + Profile context.

Not all private Admin data by default.

---

# 354. BB Assistant Principle

BB source snapshot should contain only facts necessary for selected writing task.

---

# 355. Virtual Operator Principle

VOP observes system/domain conditions.

It should not require unrestricted exposure of unrelated sensitive payloads.

---

# 356. Audit Principle

Audit captures what is necessary for accountability while respecting privacy/retention rules.

---

# 357. Final Canonical Entity Catalogue

```text
ACTOR
  ActorProfile
  ProfileLocalizedContent

MEDIA
  MediaAsset
  MediaDerivative

PORTFOLIO
  PortfolioItem

EMOTIONAL
  EmotionalSession
  EmotionalSessionMedia
  EmotionalGrid
  EmotionalGridCell
  EmotionalGridArtifact

EXPERIENCE
  Project
  Role
  RoleMedia
  Performance
  Achievement

TRAINING
  Training

COMPETENCIES
  SkillDefinition
  ActorSkill
  LanguageDefinition
  ActorLanguageProficiency

PROFESSIONAL LINKS
  ProfessionalLink

CONTACTS
  Contact

QUESTIONNAIRES
  QuestionnaireDefinition
  QuestionnaireRevision
  QuestionnaireArtifact

PUBLIC BUILDER
  PublicBuilderTemplate
  PublicBuilderSession
  BuilderGenerationSnapshot

QR
  QRConfiguration
  QRArtifact

INBOX
  Feedback
  FeedbackAttachment
  FeedbackNote
  FeedbackCastingLink

CASTINGS
  Casting
  CastingSource
  CastingSourceAsset
  CastingAnalysis
  CastingAnalysisRevision
  CastingRequirement
  ProfileMatchResult
  CastingRecommendation
  CastingHumanDecision

OPPORTUNITIES
  Opportunity
  OpportunityStageHistory
  OpportunityNote/Action

NOTIFICATIONS
  Notification
  NotificationDelivery

BB ASSISTANT
  AIDraft

VIRTUAL OPERATOR
  VOPObservation
  VOPRecommendation
  AutomationExecution

THEMES
  SiteTheme
  ThemeRevision
  ThemeProposal
  ThemeActivation

ANALYTICS
  AnalyticsEvent
  AttributionContext
  Conversion projections

SOCIAL
  SocialAccountConnection
  SocialPost
  SocialPublishAttempt

HELP
  HelpArticle
  SupportTicket
  SupportTicketMessage

IDENTITY
  AdminUser
  AdminRole/Permission

AUDIT
  AuditEvent
```

---

# 358. Canonical Authority Matrix

| Concept | Authority |
|---|---|
| Actress identity | ActorProfile |
| Original media | MediaAsset |
| Portfolio classification | PortfolioItem |
| Emotional shoot | EmotionalSession |
| Grid composition | EmotionalGrid + Cells |
| Professional work | Project / Role |
| Training | Training |
| Skill | ActorSkill |
| Language level | ActorLanguageProficiency |
| Public contact | Contact + visibility rules |
| Prepared questionnaire identity | QuestionnaireDefinition |
| Historical questionnaire | QuestionnaireRevision |
| Public custom selection | PublicBuilderSession |
| Generated custom package | BuilderGenerationSnapshot |
| URL | ProfessionalLink / canonical route |
| QR image | Derived QRArtifact |
| Professional inquiry | Feedback |
| Casting opportunity source | Casting + CastingSource |
| AI-extracted casting requirement | CastingRequirement extraction state |
| Confirmed casting interpretation | Human-confirmed requirement |
| Business opportunity | Opportunity |
| Business outcome | Opportunity state/history |
| Notification result | NotificationDelivery |
| BB text | AIDraft |
| VOP issue | VOPObservation |
| Theme | SiteTheme/ThemeRevision |
| Usage analytics | AnalyticsEvent |
| Change accountability | AuditEvent |

---

# 359. Canonical Source vs Derived Matrix

| Source / Config | Derived / Projection |
|---|---|
| MediaAsset | Thumbnail/Web/PDF derivative |
| PortfolioItem | Public Portfolio card |
| EmotionalGrid | Composite artifact |
| ActorLanguageProficiency | Quick View language |
| Project/Role | Project card |
| QuestionnaireDefinition + source refs | HTML preview |
| QuestionnaireRevision | Published PDF |
| PublicBuilderSession | Builder preview |
| BuilderGenerationSnapshot | Custom PDF |
| ProfessionalLink | QRArtifact |
| CastingSource | AI Extraction |
| Confirmed CastingRequirement + Profile | ProfileMatchResult |
| Profile facts | BB Draft |
| ThemeRevision | Rendered public theme |
| Feedback/Casting/Opportunity | Conversion Funnel projection |

---

# 360. Definition of Domain Model Compliance

Реализация соответствует DOC-060, если:

1. ActorProfile остаётся professional root, но не превращён в god object;
2. MediaAsset отделён от Portfolio classification;
3. Original media сохраняется неизменным;
4. Emotional Portfolio и Main Portfolio разделены;
5. Emotional Grid является derived/configured composite;
6. Project и Casting являются разными domains;
7. Training отделён от Projects;
8. Skills/Languages являются structured professional facts;
9. Contacts имеют независимые visibility scopes;
10. Prepared Questionnaire отделена от Revision;
11. Public Builder Session не изменяет Master Data;
12. Builder output server-revalidated;
13. QR является derived representation canonical URL;
14. Feedback сохраняется как отдельный business record;
15. Feedback не превращается автоматически в Casting;
16. Casting Source отделён от AI Extraction;
17. AI Extraction отделена от Human Confirmation;
18. Profile Match не становится Actor fact;
19. Opportunity содержит реальные pipeline outcomes;
20. Offer и Booked различаются;
21. Notification delivery не управляет business persistence;
22. BB Assistant использует отдельные AIDraft entities;
23. VOP Observation и Recommendation различаются;
24. Theme не хранит professional content;
25. Analytics не является Source of Truth;
26. Audit отделён от Analytics;
27. current Source Data и immutable historical Snapshots различаются;
28. private data исключаются до Public projection;
29. Derived artifacts могут быть invalidated/regenerated;
30. существующие Help/Social/multilingual/role-media capabilities не теряются.

---

# 361. Финальная доменная модель

```text
                              ACTOR PROFILE
                                    │
      ┌──────────┬──────────┬───────┼───────┬──────────┬───────────┐
      ↓          ↓          ↓       ↓       ↓          ↓
    MEDIA     PORTFOLIO  EMOTIONAL PROJECTS SKILLS   CONTACTS
      │          │          │       ROLES   LANGUAGES     │
      │          │          │          │                │
      └──────────┴──────────┴──────────┼────────────────┘
                                       ↓
                               QUESTIONNAIRE
                              /             \
                             ↓               ↓
                      PREPARED REVISION   PUBLIC BUILDER
                             │               │
                             └───────┬───────┘
                                     ↓
                             PROFESSIONAL PACKAGE
                             PDF · LINKS · QR
                                     │
                                     ↓
                                  FEEDBACK
                                     │
                                     ↓
                                  CASTING
                                     │
                Source → AI Analysis → Human Confirmation
                                     │
                                     ↓
                                 OPPORTUNITY
                                     │
               Materials → Audition → Callback → Offer
                                     │
                                     ↓
                                   BOOKED
                                     │
                                     ↓
                              PROJECT / ROLE DRAFT

CROSS-CUTTING:

BB Assistant
Virtual Operator
Notifications
Themes
Analytics
Social Publishing
Help
Audit
Security
```

---

# 362. Финальный принцип

> **Domain Model должен гарантировать, что профессиональная информация существует как управляемые авторитетные сущности, а все остальные формы её использования — публичная страница, Quick View, анкета, PDF, QR, кастинговый анализ, AI-текст, аналитика и автоматизация — являются контролируемыми relationships, configurations, snapshots, projections или derived artifacts. Система не должна создавать новые независимые копии профессиональной правды ради удобства отдельного интерфейса.**