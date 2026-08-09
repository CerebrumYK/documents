# DATABASE SCHEMA

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.


## Нормативная целевая PostgreSQL-схема продукта

**Целевой файл:** `docs/database/database-architecture.md`
**Документ:** DOC-090
**Статус:** ✅ Completed
**Тип:** Database / PostgreSQL / Physical Data Model / Constraints

**Связанные документы:**

* `docs/TZ.md`
* `docs/product/terminology.md`
* `docs/product/business-rules.md`
* `docs/product/information-architecture.md`
* `docs/product/product-principles.md`
* `docs/domain/domain-model.md`
* `docs/domain/entity-relationships.md`
* `docs/domain/state-machines.md`
* `docs/domain/data-ownership.md`
* `docs/domain/revisions-and-history.md`
* `docs/architecture/architecture-overview.md`
* `docs/architecture/components.md`
* `docs/architecture/data-flows.md`
* `docs/architecture/projections.md`
* `docs/architecture/media.md`
* `docs/architecture/pdf.md`
* `docs/architecture/background-jobs.md`
* `docs/architecture/cache.md`
* `docs/architecture/search.md`
* `docs/architecture/ai.md`
* `docs/architecture/notifications.md`

---

# 1. Назначение документа

Настоящий документ определяет **целевую физическую структуру PostgreSQL** для TO-BE архитектуры продукта.

Документ фиксирует:

1. database conventions;
2. primary keys;
3. foreign keys;
4. actor/profile ownership;
5. optimistic concurrency;
6. lifecycle columns;
7. ordering;
8. multilingual data;
9. Media;
10. Main Portfolio;
11. Emotional Portfolio;
12. Projects/Roles;
13. Training;
14. Achievements;
15. Skills/Languages;
16. Professional Links;
17. Contacts;
18. Questionnaires;
19. Questionnaire Revisions;
20. Public Builder;
21. QR;
22. Feedback;
23. Castings;
24. Casting AI;
25. Opportunity Pipeline;
26. Notifications;
27. BB Assistant;
28. VOP;
29. Themes;
30. Analytics;
31. Social Publishing;
32. Help/Tickets;
33. Identity/Auth;
34. Audit;
35. Outbox/Background Jobs;
36. Search;
37. constraints;
38. index principles;
39. JSONB boundaries;
40. deletion/archive implications;
41. migration compatibility.

---

# 2. Database role

PostgreSQL является:

> **авторитетным persistent store для structured Master, Configuration, Revision, Snapshot, Operational, AI Output и Audit metadata.**

PostgreSQL **не является binary media store**.

Binary objects хранятся в Media/Artifact Storage и связаны с БД через managed locators.

---

# 3. Главная доктрина

> **Database schema должна физически защищать те бизнес-инварианты, которые могут быть надёжно выражены constraint'ами, а более сложные contextual rules должны проверяться Application/Domain layer внутри контролируемой транзакции.**

Canonical:

```text
DOMAIN RULE
   ↓
APPLICATION VALIDATION
   ↓
DATABASE CONSTRAINT
where structurally possible
```

---

# 4. Schema IDs

Используются:

```text
DB-*
DB-TBL-*
DB-COL-*
DB-FK-*
DB-UQ-*
DB-CHK-*
DB-IDX-*
DB-JSON-*
DB-INV-*
```

---

# 5. DB-INV-001 — Database Is Not the Domain Model

Physical table design отражает Domain Model, но:

```text
table != aggregate
foreign key != permission
row != projection
```

---

# 6. DB-INV-002 — No Generic EAV for Core Professional Data

Core facts MUST NOT храниться как:

```text
entity_id
key
value
```

универсальная EAV-модель.

---

# 7. Почему EAV запрещён

Он разрушает:

```text
typing
constraints
queryability
ownership
migration safety
readability
```

---

# 8. DB-INV-003 — Core Relations Use Real Foreign Keys

Professional relationships SHOULD использовать explicit FK where physical model permits.

---

# 9. DB-INV-004 — JSONB Is Not Escape Hatch

JSONB допускается только там, где структура действительно:

```text
versioned
contextual
snapshot-like
provider-specific
or configuration-oriented
```

---

# 10. DB-INV-005 — Historical Revision Is Immutable

Published Revision/Snapshot rows MUST NOT обновляться обычным edit workflow.

---

# 11. DB-INV-006 — AI Output Separate

AI-generated values MUST NOT храниться в тех же columns как confirmed source values до Human Apply/Confirm.

---

# 12. DB-INV-007 — Filesystem Path Never Used as Business Identity

DB stores managed `storage_locator`, not public business URL or original filename as identity.

---

# 13. DB-INV-008 — Archive Before Delete

Большинство professional entities поддерживают lifecycle/archive вместо immediate hard delete.

---

# 14. PostgreSQL baseline

Canonical:

```text
PostgreSQL
```

Existing project baseline is preserved.

No Supabase dependency is introduced.

---

# 15. Schema namespace

Initial implementation MAY use default:

```text
public
```

PostgreSQL schema.

Larger namespace partitioning MAY be added later.

Not required baseline.

---

# 16. Naming convention

Recommended:

```text
snake_case
plural table names
singular logical entity names in code
```

Examples:

```text
actor_profiles
media_assets
portfolio_items
questionnaire_revisions
```

---

# 17. Primary keys

Preferred:

```text
UUID
```

for principal business/domain entities.

---

# 18. Why UUID

Useful for:

```text
opaque internal identity
distributed-safe creation
migration
non-sequential public exposure risk reduction
```

---

# 19. Generated primary key

Application/database MAY generate UUID.

Exact UUID version is implementation decision.

---

# 20. Sequence IDs

May be used for:

```text
append-only internal event sequence
small dictionaries
revision sequence helpers
```

when beneficial.

---

# 21. Public identifiers

Public route identity SHOULD be separate where appropriate:

```text
slug
public_id
opaque token
```

---

# 22. Public slug

Slugs MUST NOT replace immutable DB primary keys.

---

# 23. Actor/Profile root

Although initial product is single-actress, actor-owned tables SHOULD retain:

```text
profile_id
```

where appropriate.

---

# 24. Why

Avoids hard-coding singleton assumptions throughout schema and simplifies referential integrity.

---

# 25. Single-profile product

This does **not** make product multi-actor SaaS.

It is structural data ownership.

---

# 26. Common columns

Most mutable domain tables SHOULD consider:

```text
id
profile_id where applicable
created_at
updated_at
version
```

plus domain lifecycle.

---

# 27. Timestamp storage

Canonical:

```text
timestamptz
```

for event/creation/update timestamps.

---

# 28. Date-only values

Use:

```text
date
```

when domain concept is calendar date without time.

Example:

```text
EmotionalSession.shooting_date
```

---

# 29. Version

Mutable high-value entities SHOULD have:

```text
version bigint NOT NULL DEFAULT 1
```

or equivalent monotonic integer.

---

# 30. Optimistic concurrency

Update pattern conceptually:

```sql
UPDATE ...
SET ..., version = version + 1
WHERE id = :id
  AND version = :expected_version;
```

---

# 31. No silent last-write-wins

Especially:

```text
Profile
Contacts
Questionnaire Draft
Casting
Opportunity
Theme Draft
```

---

# 32. Lifecycle representation

Do not create one universal lifecycle enum for every table.

Each domain SHOULD have appropriate typed state.

---

# 33. Enum strategy

Canonical baseline is:

```text
text/varchar + CHECK
```

for domain states/enums. This keeps migrations explicit and compatible with the single Drizzle/PostgreSQL stack in DOC-208. Native PostgreSQL ENUM is not the baseline and requires an explicit schema ADR for a specific exceptional type.

---

# 34. Why cautious with PostgreSQL enums

Schema evolution/removal can be harder.

---

# 35. Boolean proliferation

Avoid ambiguous generic:

```text
is_active
is_visible
is_public
```

when domain has multiple independent meanings.

---

# 36. Example Contact

Correct:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 37. Sort order

Contextual ordered relations SHOULD include explicit:

```text
display_order integer
```

or equivalent.

---

# 38. Ordering constraint

Usually:

```text
display_order >= 0
```

---

# 39. Ordering uniqueness

May enforce unique within parent/context where business requires deterministic order.

---

# 40. Actor profile tables

## `actor_profiles`

Canonical root.

Suggested fields:

```text
id UUID PK
professional_name
professional_title
base_location
official_profile_slug
status
version
created_at
updated_at
```

Exact professional fields expanded in Data Dictionary.

---

# 41. Localized Profile Content

## `actor_profile_localizations`

Suggested:

```text
id
profile_id FK
locale
short_biography
biography
other localized narrative fields
version
created_at
updated_at
```

---

# 42. Localization uniqueness

```text
UNIQUE(profile_id, locale)
```

---

# 43. Locale

Must use normalized supported locale code.

Application validates supported locale set.

---

# 44. Biography source

BB Assistant does not write directly.

After Human Apply, target localization row becomes source Draft/current value according to Profile workflow.

---

# 45. Media tables

## `media_assets`

Suggested:

```text
id UUID PK
profile_id UUID FK
media_type
source_kind
original_filename
mime_type
file_size_bytes
checksum
storage_locator
processing_state
lifecycle_state
technical_metadata JSONB
version
created_at
updated_at
archived_at
```

---

# 46. Media checksum

Should be indexed.

Exact uniqueness policy may permit same binary deliberately, therefore checksum need not universally be UNIQUE.

---

# 47. Media storage locator

Should be unique for active managed binary object.

---

# 48. Technical metadata JSONB

Allowed because codec-specific shape varies.

Schema/version SHOULD be controlled by processor.

---

# 49. Technical metadata cannot hold professional facts

No:

```text
portfolio_category
role_name
shooting_date
```

inside technical JSON.

---

# 50. `media_derivatives`

Suggested:

```text
id
media_asset_id FK
derivative_type
transform_spec JSONB
processor_version
storage_locator
mime_type
width
height
file_size_bytes
checksum
state
created_at
updated_at
```

---

# 51. Derivative uniqueness

Conceptually:

```text
UNIQUE(
  media_asset_id,
  derivative_type,
  transformation_identity,
  processor_version
)
```

Physical representation may use `transform_hash`.

---

# 52. `media_processing_jobs`

Not required if generic background job system fully handles processing.

Do not duplicate generic job state unnecessarily.

---

# 53. Portfolio

## `portfolio_items`

Suggested:

```text
id
profile_id FK
media_asset_id FK
category
caption
publication_state
display_order
is_primary
version
created_at
updated_at
archived_at
```

Localized captions may be separate localization table.

---

# 54. Portfolio media relationship

```text
media_asset_id
```

references one immutable source.

No copied filename/file.

---

# 55. Portfolio category values

Canonical concepts include:

```text
close_up
full_body
waist
profile
three_quarter
staged
other
```

Exact dictionary DOC-091.

---

# 56. Primary semantics

`is_primary` MAY exist, but uniqueness MUST be scoped to category.

---

# 57. Primary Close-Up uniqueness

Database SHOULD enforce max one current Primary `close_up` per profile.

---

# 58. Primary Full Body uniqueness

Same for `full_body`.

---

# 59. Partial unique indexes

Recommended conceptual solution:

```sql
UNIQUE(profile_id)
WHERE is_primary = true
  AND category = 'close_up'
  AND publication_state <> 'archived'
```

and same for full_body.

Exact lifecycle condition finalized in implementation.

---

# 60. Invalid Primary

Application must also validate:

```text
is_primary Close-Up → category = close_up
is_primary Full Body → category = full_body
```

---

# 61. Emotional Portfolio

## `emotional_sessions`

Suggested:

```text
id
profile_id FK
title
shooting_date DATE NOT NULL
publication_state
description
version
created_at
updated_at
archived_at
```

---

# 62. Shooting date

`NOT NULL` for valid EmotionalSession according to master requirements.

Draft import/migration exceptions require explicit migration strategy, not silent fake date.

---

# 63. `emotional_session_media`

Suggested relation:

```text
id
emotional_session_id FK
media_asset_id FK
display_order
created_at
```

---

# 64. Same-profile rule

DB FK alone may not guarantee matching `profile_id` if relation table omits profile.

Application MUST validate.

Physical composite FK may be considered later if worth complexity.

---

# 65. Session media uniqueness

```text
UNIQUE(emotional_session_id, media_asset_id)
```

recommended.

---

# 66. Emotional Grids

## `emotional_grids`

Logical Grid parent.

Suggested:

```text
id
profile_id
emotional_session_id
name
publication_state
is_primary
current_draft_version
current_finalized_revision_id nullable
version
created_at
updated_at
archived_at
```

---

# 67. Grid parent vs Revision

Mutable Grid identity separated from finalized immutable versions.

---

# 68. `emotional_grid_drafts`

If draft needs separate persisted model:

```text
id
emotional_grid_id
rows
columns
version
created_at
updated_at
```

Alternative: mutable fields directly on parent before finalization.

Preferred long-term: explicit draft/config + immutable revision.

---

# 69. `emotional_grid_cells`

For current mutable draft:

```text
id
grid_draft_id
position
media_asset_id
crop_x
crop_y
scale
rotation
pan_x
pan_y
face_occupancy_estimate nullable
confirmation_state
confirmed_at
confirmed_by
version
```

---

# 70. Allowed Grid dimensions

Database SHOULD CHECK:

```text
(rows, columns) IN (
  (1,2),(1,3),(1,4),
  (2,2),(2,3),(2,4),
  (3,2),(3,3),(3,4),
  (4,2),(4,3),(4,4)
)
```

---

# 71. Cell position

Must be unique:

```text
UNIQUE(grid_draft_id, position)
```

---

# 72. Exact cell count

Cross-row count cannot be expressed cleanly by simple CHECK.

Application/readiness/finalization transaction MUST enforce:

```text
count(cells) = rows * columns
```

before finalization.

---

# 73. Cell confirmation

Every cell confirmation is explicit state.

---

# 74. `emotional_grid_revisions`

Immutable finalized configuration:

```text
id
emotional_grid_id
revision_number
rows
columns
configuration_snapshot JSONB
source_version_metadata JSONB
render_version
finalized_at
created_by
```

---

# 75. Grid revision uniqueness

```text
UNIQUE(emotional_grid_id, revision_number)
```

---

# 76. Grid revision immutability

No normal UPDATE of semantic snapshot after creation.

---

# 77. `emotional_grid_artifacts`

Suggested:

```text
id
emotional_grid_revision_id
artifact_type
storage_locator
mime_type
width
height
checksum
renderer_version
state
created_at
```

---

# 78. Artifact type uniqueness

```text
UNIQUE(emotional_grid_revision_id, artifact_type, renderer_version)
```

or equivalent.

---

# 79. Primary Grid

At most one current Primary published Grid per applicable scope/profile/session policy.

Current master says multiple grids with primary marker.

Recommended uniqueness:

```text
one primary published grid per emotional session
```

unless module spec later establishes profile-global Primary.

Module DOC-113 will finalize exact scope.

---

# 80. Projects

## `projects`

Suggested:

```text
id
profile_id
project_type
title
year_start
year_end
description
publication_state
is_featured
display_order
version
created_at
updated_at
archived_at
source_opportunity_id nullable
```

Localized narrative/title MAY use localization table.

---

# 81. Project provenance

`source_opportunity_id` optional explicit provenance when created from Booked Opportunity.

---

# 82. Project does not own Training

No project type `other` used as replacement for Training domain.

---

# 83. Roles

## `roles`

Suggested:

```text
id
profile_id
project_id FK
name
description
publication_state
display_order
version
created_at
updated_at
archived_at
source_opportunity_id nullable
```

---

# 84. Project/Profile consistency

Application MUST validate:

```text
Role.profile_id = Project.profile_id
```

---

# 85. Role media

## `role_media`

Suggested:

```text
id
role_id
media_asset_id
media_usage_type
display_order
show_on_home
created_at
updated_at
```

---

# 86. Role photo limit

Application constraint:

```text
max 5 role photos
```

---

# 87. Home showcase

Application/transaction constraint:

```text
max 2 show_on_home
```

for canonical current product scope.

---

# 88. Why not simple DB CHECK

Cross-row count constraints require transaction logic or specialized trigger.

Prefer Application + optional defensive DB trigger later.

---

# 89. Performances

## `performances`

Suggested:

```text
id
project_id
role_id nullable
performance_date
venue
location
description
publication_state
created_at
updated_at
```

---

# 90. Training

## `training_items`

Suggested:

```text
id
profile_id
title
institution
start_date
end_date
completion_date
description
publication_state
display_order
version
created_at
updated_at
archived_at
migration_source_project_id nullable
```

---

# 91. Training migration provenance

Legacy Project `Other` migration may retain:

```text
migration_source_project_id
```

until migration/audit no longer needs it.

---

# 92. Achievements

## `achievements`

Suggested:

```text
id
profile_id
title
issuer
achievement_date
description
publication_state
display_order
project_id nullable
role_id nullable
version
created_at
updated_at
archived_at
```

---

# 93. Skills

## `skill_definitions`

Dictionary:

```text
id
code
default_label
category
active
```

---

# 94. Skill localizations

## `skill_definition_localizations`

```text
skill_definition_id
locale
label
aliases JSONB or normalized alias table
```

---

# 95. Actor Skill

## `actor_skills`

Suggested:

```text
id
profile_id
skill_definition_id
level
description
publication_state
display_order
version
created_at
updated_at
```

---

# 96. Actor Skill uniqueness

```text
UNIQUE(profile_id, skill_definition_id)
```

unless multiple contexts become explicit future requirement.

---

# 97. Skill level

CHECK allowed 5-level scale defined in DOC-091.

---

# 98. Languages

## `language_definitions`

```text
id
code
default_label
active
```

---

# 99. Language localization

Optional dictionary localization table.

---

# 100. Actor language proficiency

## `actor_languages`

Suggested:

```text
id
profile_id
language_definition_id
proficiency_type
cefr_level nullable
is_native boolean
publication_state
display_order
version
created_at
updated_at
```

---

# 101. Actor Language uniqueness

```text
UNIQUE(profile_id, language_definition_id)
```

---

# 102. CEFR constraint

If non-native CEFR:

```text
A1, A2, B1, B2, C1, C2
```

---

# 103. Native semantics

Avoid contradictory:

```text
is_native = true
cefr_level = A2
```

unless product explicitly allows native + CEFR simultaneously.

Preferred CHECK later.

---

# 104. Professional Links

## `professional_links`

Suggested:

```text
id
profile_id
link_type
description
canonical_url
display_order
publication_state
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
version
created_at
updated_at
archived_at
```

---

# 105. Three-column editor

Logical UI:

```text
number
description
URL
```

Number derives from/display_order; it need not be stored as independent professional fact.

---

# 106. Canonical URL

Single authoritative URL column.

QR does not own URL copy.

---

# 107. Link health

Do not overwrite URL with health status.

Use separate table.

---

# 108. `professional_link_health`

Suggested:

```text
professional_link_id PK/FK
status
checked_at
http_status nullable
failure_code nullable
consecutive_failures
updated_at
```

Derived operational state.

---

# 109. Contacts

## `contacts`

Suggested:

```text
id
profile_id
contact_type
label
relationship_type nullable
person_name nullable
value
normalized_value
show_on_public_site boolean
allow_in_admin_questionnaires boolean
allow_in_public_questionnaire_builder boolean
display_order
lifecycle_state
version
created_at
updated_at
archived_at
```

---

# 110. Contact value

Could represent:

```text
phone
email
WhatsApp
social URL
other approved type
```

according to `contact_type`.

---

# 111. Contact validation

Type-specific validation occurs in Application layer and may have DB CHECK where practical.

---

# 112. Contact permissions independent

No constraint should force:

```text
public=true
→ builder=true
```

or any other coupling.

---

# 113. Guardian/parent contact

`relationship_type = parent/guardian` does not auto-enable any visibility flag.

---

# 114. Questionnaires

## `questionnaire_definitions`

Stable logical questionnaire.

Suggested:

```text
id
profile_id
name
questionnaire_type
locale_policy
lifecycle_state
is_primary_public
current_draft_id nullable
current_published_revision_id nullable
casting_id nullable
version
created_at
updated_at
archived_at
```

---

# 115. Questionnaire types

Canonical includes:

```text
compact
extended
casting
custom
```

plus possible default/recommended configuration concepts.

---

# 116. Primary public questionnaire

Database SHOULD enforce:

> at most one active `is_primary_public = true` per profile.

Partial unique index recommended.

---

# 117. Primary does not imply published

Application readiness MUST prevent invalid primary/public combinations as defined module rules.

---

# 118. Questionnaire Draft

## `questionnaire_drafts`

Suggested:

```text
id
questionnaire_definition_id
configuration JSONB
schema_version
source_version_vector JSONB
readiness_state
version
created_at
updated_at
```

---

# 119. Why JSONB allowed

Questionnaire configuration contains:

```text
block inclusion
order
selected entity IDs
per-link QR settings
template-specific configuration
```

and is naturally versioned configuration.

---

# 120. JSONB validation

Application MUST validate against explicit versioned schema.

---

# 121. Core references inside JSONB concern

Entity IDs inside configuration JSONB do not get DB FK protection.

Where relations become important/stable, normalized selection tables SHOULD be preferred.

---

# 122. Recommended normalized questionnaire selections

Long-term preferred tables:

```text
questionnaire_draft_sections
questionnaire_draft_items
questionnaire_draft_links
```

rather than storing every relationship only in JSONB.

---

# 123. Hybrid model

Recommended:

```text
normalized high-value references
+
JSONB for renderer/config options
```

---

# 124. Questionnaire sections

## `questionnaire_draft_sections`

Suggested:

```text
id
questionnaire_draft_id
section_type
enabled
display_order
configuration JSONB
```

---

# 125. Section uniqueness

```text
UNIQUE(questionnaire_draft_id, section_type)
```

where only one section per type.

---

# 126. Questionnaire item selection

## `questionnaire_draft_items`

Suggested:

```text
id
questionnaire_draft_id
section_type
entity_type
entity_id
display_order
configuration JSONB
```

---

# 127. Polymorphic reference caveat

`entity_type/entity_id` cannot use ordinary FK.

This is acceptable only because Questionnaire configuration is supporting/configuration context.

---

# 128. Alternative strict model

Separate tables per item type MAY be used if implementation prefers maximal FK safety.

DOC-090 permits either, but core source entities themselves remain normalized.

---

# 129. Questionnaire Revisions

## `questionnaire_revisions`

Immutable.

Suggested:

```text
id
questionnaire_definition_id
revision_number
locale
snapshot_schema_version
content_snapshot JSONB
source_version_vector JSONB
document_model_version
published_at
published_by
created_at
```

---

# 130. Revision uniqueness

At minimum:

```text
UNIQUE(questionnaire_definition_id, revision_number)
```

If locale-specific revisions are separate, exact uniqueness may include locale; canonical preference is one logical revision with locale-aware snapshot structure or explicit locale artifact relation.

Module DOC-120 will finalize.

---

# 131. Revision allocation

Must be transaction-safe.

---

# 132. Revision snapshot

Contains frozen professional values/references required for historical rendering.

---

# 133. Revision update

Prohibited under ordinary workflows.

---

# 134. Questionnaire artifacts

## `questionnaire_artifacts`

Suggested:

```text
id
questionnaire_revision_id nullable
builder_generation_snapshot_id nullable
artifact_type
locale
document_model_version
template_version
renderer_version
storage_locator
mime_type
file_size_bytes
checksum
access_class
state
generated_at
created_at
```

---

# 135. Source XOR

Exactly one of:

```text
questionnaire_revision_id
builder_generation_snapshot_id
```

MUST be set.

Database CHECK:

```text
(one IS NOT NULL) <> (other IS NOT NULL)
```

---

# 136. Public Builder Templates

## `public_builder_templates`

Suggested:

```text
id
profile_id nullable
code
name
template_type
configuration JSONB
schema_version
is_enabled
display_order
version
created_at
updated_at
```

---

# 137. Canonical types

```text
quick
standard
extended
casting
```

---

# 138. Public Builder Sessions

## `public_builder_sessions`

Temporary authoritative configuration.

Suggested:

```text
id UUID PK
profile_id
template_id nullable
source_questionnaire_definition_id nullable
source_questionnaire_revision_id nullable
locale
casting_name nullable
project_name nullable
role_name nullable
configuration JSONB
schema_version
version
status
expires_at
created_at
updated_at
```

---

# 139. Builder start source

Canonical requirement: start from either:

```text
template
or
recommended/prepared questionnaire
```

Schema SHOULD model mutually compatible source semantics explicitly.

---

# 140. Session ID

Opaque.

Not authorization to unrelated entities.

---

# 141. Builder Session is not cache

It must persist independently from any cache backend.

---

# 142. Builder item selections

Recommended normalized:

## `public_builder_session_items`

```text
id
session_id
section_type
entity_type
entity_id
display_order
selected
configuration JSONB
created_at
updated_at
```

---

# 143. Eligibility

DB cannot prove current Builder permissions for polymorphic source.

Application revalidates.

---

# 144. Generation Snapshot

## `public_builder_generation_snapshots`

Immutable.

Suggested:

```text
id
session_id
profile_id
generation_number
locale
snapshot_schema_version
content_snapshot JSONB
source_version_vector JSONB
generated_at
created_at
```

---

# 145. Generation uniqueness

```text
UNIQUE(session_id, generation_number)
```

---

# 146. One Session → many Snapshots

Required.

---

# 147. QR configuration

QR is derived from canonical URL.

---

# 148. `qr_artifacts`

Suggested:

```text
id
target_url
target_url_hash
settings JSONB
settings_version
storage_locator
mime_type
checksum
validation_state
decoded_url
validated_at
created_at
```

---

# 149. QR cache uniqueness

Conceptually:

```text
UNIQUE(target_url_hash, settings_version, settings_hash)
```

---

# 150. Exact validation

`decoded_url` must equal source target before READY.

---

# 151. QR usage/config

Per questionnaire/link show/hide belongs Questionnaire Draft/Revision configuration, not QR Artifact itself.

---

# 152. Feedback

## `feedback`

Suggested:

```text
id
profile_id
feedback_type
sender_name
sender_organization nullable
sender_email nullable
sender_phone nullable
message
source_context_type nullable
source_context_id nullable
questionnaire_session_id nullable
workflow_state
responsible_admin_user_id nullable
next_action nullable
next_action_at nullable
read_at nullable
version
created_at
updated_at
```

---

# 153. Feedback persistence

Created before notification.

---

# 154. Feedback type

Canonical:

```text
casting_invitation
role
collaboration
questionnaire
materials
question
comment
other
```

---

# 155. Feedback attachment

## `feedback_attachments`

Suggested:

```text
id
feedback_id
storage_locator
original_filename
mime_type
file_size_bytes
checksum
security_state
created_at
```

---

# 156. Feedback attachments are private

No relation to public MediaAsset by default.

---

# 157. Feedback notes

## `feedback_notes`

```text
id
feedback_id
admin_user_id
note
created_at
updated_at
```

Internal-only.

---

# 158. Feedback ↔ Casting

Explicit relation:

## `feedback_casting_links`

```text
feedback_id
casting_id
link_type
created_at
created_by
```

---

# 159. Bidirectional semantic link

One relation table is sufficient physically.

UI resolves both directions.

---

# 160. Castings

## `castings`

Suggested:

```text
id
profile_id
title
project_name nullable
role_name nullable
organization nullable
deadline_at nullable
status
source_feedback_id nullable
version
created_at
updated_at
closed_at nullable
```

---

# 161. Casting Source

## `casting_sources`

Suggested:

```text
id
casting_id
source_type
text_content nullable
source_version
created_at
updated_at
```

If source changes materially, version increments.

---

# 162. Casting Source Assets

## `casting_source_assets`

```text
id
casting_source_id
storage_locator
original_filename
mime_type
file_size_bytes
checksum
security_state
created_at
```

Private.

---

# 163. Casting AI Analysis Revisions

## `casting_analysis_revisions`

Immutable.

Suggested:

```text
id
casting_id
revision_number
source_snapshot JSONB
profile_snapshot JSONB
source_version_vector JSONB
prompt_version
provider
model
model_metadata JSONB
analysis_summary nullable
structured_output JSONB
status
generated_at
created_at
```

---

# 164. Analysis revision uniqueness

```text
UNIQUE(casting_id, revision_number)
```

---

# 165. Extracted Requirements

## `casting_requirements`

Recommended normalized table:

```text
id
casting_analysis_revision_id
category
extracted_value JSONB
source_evidence JSONB
extraction_state
human_confirmed_value JSONB nullable
human_decision_state
confirmed_by nullable
confirmed_at nullable
created_at
updated_at
version
```

---

# 166. Why normalized requirement

Requirements are important business objects requiring:

```text
review
history
match
traceability
```

and should not live only inside analysis JSON.

---

# 167. `NOT_SPECIFIED`

Represent explicitly through state/value semantics, not fake empty string.

---

# 168. Human-confirmed requirement

Separate from extracted value.

---

# 169. Casting Profile Match

## `casting_profile_matches`

Suggested:

```text
id
casting_id
analysis_revision_id
requirement_id
profile_source_type
profile_source_id nullable
match_state
profile_value_snapshot JSONB nullable
reason
algorithm_version
created_at
updated_at
```

---

# 170. Match is derived

Can be recalculated.

Historical analysis context may retain old match rows or snapshot.

---

# 171. Casting Recommendations

## `casting_recommendations`

```text
id
casting_id
analysis_revision_id
recommendation_type
recommendation
rationale
status
created_at
updated_at
```

AI recommendation, not Domain fact.

---

# 172. Opportunity Pipeline

## `opportunities`

Suggested:

```text
id
profile_id
casting_id nullable
title
stage
responsible_admin_user_id nullable
next_action nullable
next_action_at nullable
version
created_at
updated_at
closed_at nullable
```

---

# 173. Opportunity stage canonical values

```text
new
qualified
materials_requested
self_tape_requested
audition
callback
offer
booked
closed_not_selected
withdrawn
```

---

# 174. Opportunity stage is authoritative

Stored directly on Opportunity.

---

# 175. Opportunity stage history

## `opportunity_stage_history`

Append-only:

```text
id
opportunity_id
from_stage nullable
to_stage
reason nullable
changed_by
changed_at
correlation_id nullable
```

---

# 176. Current stage + history

Current stage supports efficient reads.

History supports traceability.

---

# 177. Booked

Only explicit validated Opportunity transition changes stage.

No analytics/AI FK can change it indirectly.

---

# 178. Project from Opportunity

Project/Role may retain:

```text
source_opportunity_id
```

for provenance.

---

# 179. Notifications

## `notifications`

Suggested:

```text
id
notification_type
priority
source_type
source_id
source_event_id nullable
title_key
message_key
template_variables JSONB
admin_route nullable
read_at nullable
archived_at nullable
created_at
```

---

# 180. Notification uniqueness/dedup

May include:

```text
dedup_key
```

with conditional uniqueness for active semantic event types.

Exact policy per notification type.

---

# 181. Notification Deliveries

## `notification_deliveries`

Suggested:

```text
id
notification_id
channel
recipient_identity
template_id
template_version
payload_snapshot JSONB
status
scheduled_at nullable
expires_at nullable
attempt_count
provider_message_id nullable
last_error_code nullable
last_error_at nullable
sent_at nullable
created_at
updated_at
```

---

# 182. Recipient secrets

No provider credentials.

---

# 183. Delivery idempotency

`id` can serve semantic delivery identity.

Provider idempotency key may be derived from it.

---

# 184. Notification delivery attempts

## `notification_delivery_attempts`

Recommended:

```text
id
notification_delivery_id
attempt_number
started_at
finished_at
result_state
error_code nullable
provider_request_id nullable
safe_provider_metadata JSONB nullable
```

---

# 185. Delivery attempt uniqueness

```text
UNIQUE(notification_delivery_id, attempt_number)
```

---

# 186. Notification Preferences

## `notification_preferences`

Suggested:

```text
id
admin_user_id nullable
notification_type
in_app_enabled
whatsapp_enabled
email_enabled
quiet_hours_policy
version
updated_at
```

Exact global-vs-user configuration later.

---

# 187. Notification recipients

System operational destinations SHOULD have own configuration table rather than reusing Contacts.

---

# 188. BB Assistant

## `ai_drafts`

Canonical from master requirements.

Suggested:

```text
id
profile_id
context_entity_type nullable
context_entity_id nullable
task_type
language
tone
length
source_snapshot JSONB
source_version_vector JSONB
generated_text
edited_text nullable
prompt_version
provider
model
model_metadata JSONB
status
target_entity_type nullable
target_entity_id nullable
target_version_at_generation nullable
created_by
created_at
applied_at nullable
discarded_at nullable
```

---

# 189. AI Draft status

Canonical logical:

```text
generated
edited
applied
discarded
failed
stale
```

Generation processing may use supporting request/job state.

---

# 190. Generated text immutable provenance

Do not overwrite `generated_text` with Human edited text.

---

# 191. Edited text

Separate field/version/history.

If rich revision history required, may use `ai_draft_edits` later.

---

# 192. AI Generation Requests

Optional recommended table:

## `ai_generation_requests`

```text
id
capability
task_type
source_snapshot_id/reference
prompt_version
provider
model
status
idempotency_key
requested_by
created_at
completed_at nullable
error_code nullable
```

Useful for retries/cost/observability.

---

# 193. Virtual Operator

## `vop_observations`

Suggested:

```text
id
observation_type
severity
entity_type
entity_id
dedup_key
evidence JSONB
status
first_detected_at
last_detected_at
resolved_at nullable
version
created_at
updated_at
```

---

# 194. Observation states

```text
open
resolved
auto_resolved
dismissed
```

exact module doc later.

---

# 195. VOP Recommendations

## `vop_recommendations`

```text
id
observation_id
recommendation_type
recommendation
rationale
automation_level
source_version_vector JSONB
status
generated_by
created_at
decided_at nullable
decided_by nullable
```

---

# 196. VOP execution history

## `vop_actions`

```text
id
observation_id
recommendation_id nullable
action_type
execution_mode
status
target_entity_type
target_entity_id
correlation_id
created_at
completed_at nullable
```

---

# 197. Themes

## `site_themes`

Stable logical theme identity:

```text
id
profile_id
name
lifecycle_state
current_draft_id nullable
created_at
updated_at
version
```

---

# 198. Theme Drafts

## `theme_drafts`

```text
id
site_theme_id
tokens JSONB
assets_config JSONB
locked_fields JSONB
schema_version
version
created_at
updated_at
```

---

# 199. Theme Proposal

## `theme_ai_proposals`

```text
id
site_theme_id
theme_draft_id
source_draft_version
admin_prompt
proposal JSONB
prompt_version
provider
model
model_metadata JSONB
validation_state
status
created_at
```

---

# 200. Theme Revisions

## `theme_revisions`

Immutable:

```text
id
site_theme_id
revision_number
tokens_snapshot JSONB
assets_snapshot JSONB
schema_version
created_by
published_at
created_at
```

---

# 201. Theme revision uniqueness

```text
UNIQUE(site_theme_id, revision_number)
```

---

# 202. Theme Activations

## `theme_activations`

Suggested:

```text
id
profile_id
theme_revision_id
activation_type
starts_at
ends_at nullable
status
approved_by
created_at
updated_at
```

---

# 203. Current Theme

Effective Theme derived from valid active activation/current policy.

Do not duplicate active tokens into Profile.

---

# 204. Social Publishing

## `social_accounts`

Suggested:

```text
id
profile_id
platform
provider_account_id
display_name
credential_reference
status
connected_at
updated_at
```

---

# 205. Credential reference

References secure secret storage.

No OAuth token plaintext.

---

# 206. Social Posts

## `social_posts`

```text
id
profile_id
content
media_configuration JSONB
status
approved_version
scheduled_at nullable
version
created_by
created_at
updated_at
```

---

# 207. Social targets

## `social_post_targets`

```text
id
social_post_id
social_account_id
approved_post_version
status
scheduled_at nullable
provider_post_id nullable
published_at nullable
last_error_code nullable
```

---

# 208. Social publish attempts

Append technical attempt history if needed similarly to Notification deliveries.

---

# 209. Social partial failure

Each target independent.

---

# 210. Help/Tickets

## `support_tickets`

Suggested:

```text
id
profile_id nullable
created_by_user_id
subject
description
status
priority
created_at
updated_at
closed_at nullable
```

---

# 211. Ticket comments

## `support_ticket_messages`

```text
id
ticket_id
author_user_id
message
created_at
```

---

# 212. Help Center

## `help_articles`

If existing feature persists:

```text
id
slug
locale
title
content
status
version
created_at
updated_at
```

---

# 213. Support domain separate from Feedback

No FK implying one is the other.

---

# 214. Identity/Auth

Exact auth framework may vary.

Canonical logical tables may include:

```text
admin_users
admin_sessions
auth_accounts
```

depending auth implementation.

---

# 215. Admin users

## `admin_users`

Suggested:

```text
id
email
display_name
status
locale
created_at
updated_at
last_login_at nullable
```

---

# 216. Passwords

If local password auth:

store strong password hash only.

Exact security spec DOC-140+.

---

# 217. OAuth/identity provider

Provider identifiers/tokens must remain appropriately protected.

---

# 218. Authorization roles

Current simple system may use one Admin role.

Schema SHOULD allow future explicit role/permission mapping if later security docs require.

Do not prematurely build enterprise RBAC complexity unless needed.

---

# 219. Audit

## `audit_events`

Append-only.

Suggested:

```text
id
occurred_at
actor_type
actor_id nullable
action
entity_type
entity_id
profile_id nullable
correlation_id nullable
request_id nullable
change_summary JSONB
metadata JSONB
```

---

# 220. Audit immutability

No ordinary UPDATE/DELETE.

Retention/redaction exceptional policy later.

---

# 221. Audit secret rule

`change_summary` MUST exclude secrets.

---

# 222. Audit vs Event

Audit describes action/history.

Outbox/domain event drives technical integration.

Separate tables.

---

# 223. Domain Outbox

## `outbox_events`

Suggested:

```text
id UUID PK
event_type
aggregate_type
aggregate_id
profile_id nullable
payload JSONB
payload_version
correlation_id nullable
created_at
available_at
processed_at nullable
attempt_count
last_error_code nullable
```

---

# 224. Outbox append

Written within same transaction as authoritative change when side effect/invalidation must be reliable.

---

# 225. Outbox processing

`processed_at` updates technical processing state.

Event semantic payload itself immutable.

---

# 226. Background Jobs

## `background_jobs`

Suggested:

```text
id UUID PK
job_type
semantic_class
payload JSONB
payload_version
idempotency_key
priority
status
scheduled_for
attempt_count
max_attempts
locked_by nullable
locked_at nullable
lease_until nullable
last_error_code nullable
created_at
updated_at
completed_at nullable
cancelled_at nullable
correlation_id nullable
source_event_id nullable
```

---

# 227. Job idempotency

Database SHOULD support uniqueness appropriate to active semantic job.

Could use:

```text
UNIQUE(job_type, idempotency_key)
```

where repeated completed jobs with same key should reuse semantic result.

Some job types may need more nuanced scope.

---

# 228. Job payload secrets

Prohibited.

---

# 229. Job Attempts

## `background_job_attempts`

Recommended:

```text
id
background_job_id
attempt_number
worker_id
started_at
finished_at
result_state
error_code nullable
diagnostic_metadata JSONB nullable
```

---

# 230. Search Index

For PostgreSQL-first architecture, possible normalized derived table:

## `search_documents`

```text
id
search_scope
profile_id
entity_type
entity_id
locale
title
searchable_text
search_vector
priority_class
public_route nullable
source_version
indexed_at
schema_version
```

---

# 231. Search scope

Canonical:

```text
public
builder
admin
```

---

# 232. Search privacy

A row in `search_documents` must contain only fields authorized for its scope.

---

# 233. Search derived nature

Table may be truncated/rebuilt.

No FK from authoritative Domain entity should depend on Search row.

---

# 234. Search indexes

Likely:

```text
GIN(search_vector)
trigram index on normalized searchable text/title
```

if extension/tooling selected.

Exact physical implementation later.

---

# 235. Analytics

Analytics is derived/observational.

---

# 236. `analytics_events`

Suggested:

```text
id
event_type
profile_id nullable
source_context_type nullable
source_context_id nullable
session_id nullable
attribution_source nullable
metadata JSONB
occurred_at
schema_version
```

---

# 237. Analytics privacy

Do not put:

```text
Feedback full message
private Casting source
Contact value
secrets
```

into generic analytics metadata.

---

# 238. Business events vs analytics

`FeedbackCreated` authoritative source remains Feedback.

`analytics_events` only observes it.

---

# 239. Funnel aggregates

May be computed dynamically/materialized separately.

No need to persist duplicated Opportunity stage as analytics truth.

---

# 240. QR attribution

Analytics source may include:

```text
qr
```

without making QR business authority.

---

# 241. Readiness

Questionnaire/Profile readiness is derived.

---

# 242. Readiness persistence

May be:

```text
computed on demand
cached derived table
```

but MUST NOT be manually editable Source.

---

# 243. If materialized

Use table such as:

```text
entity_readiness
```

only as rebuildable projection.

Not required baseline.

---

# 244. Notification Center projection

Should query Notification tables rather than persist duplicate dashboard copy.

---

# 245. Dashboard projection

No dedicated authoritative `dashboard_items` table required.

If materialized later, must remain derived.

---

# 246. Dependency graph

Can be computed from normalized FKs/relations.

No universal manual dependency table is required baseline.

---

# 247. Generic supporting references

Allowed only in supporting domains:

```text
Audit
VOP
AI context
Notification source
Analytics
```

using:

```text
entity_type
entity_id
```

---

# 248. DB-INV-009 — Core Domain Does Not Use Generic Polymorphism Everywhere

Project→Role uses FK.

Role→Media uses relation FK.

Questionnaire supporting selection may use polymorphism.

---

# 249. Why

Strong core referential integrity outweighs generic schema convenience.

---

# 250. JSONB allowed zones

Canonical allowed categories:

```text
snapshot
configuration
provider metadata
technical metadata
transformation spec
versioned AI structured output
event payload
job payload
audit delta
analytics metadata
```

---

# 251. JSONB forbidden abuse

Do not store entire mutable domain in:

```text
actor_profiles.data JSONB
```

or:

```text
projects.payload JSONB
```

to avoid designing schema.

---

# 252. JSON schema versioning

Every long-lived JSONB structure SHOULD have:

```text
schema_version
```

either column or embedded version.

---

# 253. JSON validation

Application-level schema validation mandatory.

DB-level JSON CHECK MAY supplement for critical simple shape.

---

# 254. Foreign key deletion defaults

Default preference:

```text
ON DELETE RESTRICT
```

for professional/master relations.

---

# 255. Why

Prevents accidental cascaded destruction of professional history.

---

# 256. Cascade allowed

Only for true child technical/config rows whose existence has no independent business/history meaning.

Examples potentially:

```text
draft section rows
temporary session selections
attempt rows
```

---

# 257. Cascade forbidden examples

Do NOT cascade:

```text
delete Project → delete MediaAsset
delete PortfolioItem → delete MediaAsset
delete QuestionnaireDefinition → delete published historical Revision
delete Feedback → delete Casting
```

under ordinary schema.

---

# 258. `ON DELETE SET NULL`

May be suitable where provenance reference may survive source lifecycle and semantics permit.

Use cautiously.

---

# 259. Archive columns

Common pattern:

```text
archived_at timestamptz nullable
```

plus lifecycle state where needed.

---

# 260. Soft-delete flag

Avoid generic:

```text
deleted = true
```

without lifecycle semantics.

---

# 261. Hard delete

Exceptional path.

DOC-094 defines retention/deletion.

---

# 262. Unique constraints with archive

Unique business identity may need partial index over active rows.

---

# 263. Slugs

Public slugs SHOULD have unique constraint in their relevant namespace.

Example:

```text
UNIQUE(profile_id, slug)
```

for Projects if slugs per profile.

---

# 264. Case handling

Case-insensitive uniqueness for email/slug uses a canonical normalized application value plus a PostgreSQL functional unique index on `lower(column)` where case-insensitive identity is required.

`citext` is not a baseline dependency. Normalization rules belong to the owning domain/application contract and are covered by migration/integration tests.

---

# 265. URL length

Use suitable text type.

Do not arbitrarily restrict to legacy 255 chars if canonical URLs may be longer.

---

# 266. Narrative text

Use PostgreSQL `text`.

---

# 267. Money

Not currently a primary product domain.

No floating-point money fields should be introduced if future commercial data appears; use decimal/integer minor units.

---

# 268. Numeric measurements

Appearance/profile physical parameters, if present, should use typed numeric units.

Exact fields Data Dictionary.

---

# 269. Unit storage

Prefer one canonical internal unit.

UI may localize display.

---

# 270. Boolean NULL

Visibility booleans SHOULD be:

```text
NOT NULL DEFAULT false
```

where tri-state is not meaningful.

---

# 271. Status NULL

Lifecycle/status SHOULD generally be `NOT NULL`.

---

# 272. Created/updated timestamps

Mutable table:

```text
created_at NOT NULL
updated_at NOT NULL
```

---

# 273. Immutable revision timestamp

May not need `updated_at` because update is prohibited.

---

# 274. `created_by`

High-value entities/revisions MAY store explicit author/admin id where useful.

---

# 275. Audit remains authoritative history

Do not add `created_by/updated_by` to every table purely as substitute for Audit.

---

# 276. Revision number

Human-readable monotonic integer scoped to parent logical entity.

---

# 277. Revision number allocation

Must happen atomically.

Options:

```text
parent counter under row lock
MAX + 1 under transaction/unique retry
dedicated sequence table
```

Exact implementation later.

---

# 278. DB unique constraint is final guard

Concurrent publish cannot create duplicate revision number.

---

# 279. Current pointer

Parent may store:

```text
current_published_revision_id
```

for efficient current read.

---

# 280. Pointer integrity

Referenced Revision must belong to same parent.

Ordinary FK only ensures Revision exists.

Same-parent invariant may need:

```text
composite FK
trigger
application transaction validation
```

---

# 281. Recommended

Application validation + transaction + targeted DB composite unique/FK where complexity is justified.

---

# 282. Primary marker concurrency

Partial unique indexes provide strong final guard against two Primary rows.

---

# 283. Opportunity stage concurrency

`version` protects transition conflicts.

History insert and current stage update occur in same transaction.

---

# 284. Feedback submit idempotency

Recommended dedicated table or column:

```text
submission_idempotency_key
```

with unique constraint.

---

# 285. Public idempotency keys

Must be opaque and bounded.

---

# 286. Idempotency state is durable

Not cache-only.

---

# 287. Public Builder generation idempotency

Can use:

```text
generation request id
```

plus session generation number.

---

# 288. PDF job idempotency

Backed by artifact/job uniqueness.

---

# 289. Provider credentials

Database stores references/config metadata, not plaintext secrets.

---

# 290. Secret store boundary

Examples:

```text
environment secret
encrypted secret manager
OS-level secret store
```

exact deployment security later.

---

# 291. Sensitive values

Certain DB fields still contain personal/professional PII:

```text
Feedback sender phone/email
private Contacts
Casting source text
```

Access controlled server-side.

---

# 292. Encryption at rest

Database/storage-level encryption policy defined later in security/operations.

---

# 293. Row-level security

PostgreSQL RLS is NOT required baseline.

---

# 294. Why

Current modular application/server-side authorization may be simpler and sufficient.

---

# 295. Future RLS

MAY be evaluated if permission complexity/multi-tenant architecture changes.

Not needed to claim data is secure today.

---

# 296. DB user permissions

Application DB account SHOULD follow least-privilege operational policy later.

Migration account may have broader schema privileges.

---

# 297. Direct database editing

Production professional data SHOULD be mutated through application/migration tooling, not routine manual SQL.

---

# 298. Constraints categories

Canonical:

```text
STRUCTURAL
DOMAIN-SIMPLE
CROSS-ROW
CONTEXTUAL
AUTHORIZATION
```

---

# 299. STRUCTURAL

Database enforces:

```text
NOT NULL
FK
UNIQUE
CHECK
```

---

# 300. DOMAIN-SIMPLE

Database SHOULD enforce simple:

```text
rows between 1 and 4
scale > 0
display_order >= 0
allowed state value
```

---

# 301. CROSS-ROW

May need:

```text
partial unique index
transaction
trigger
```

Examples Primary uniqueness.

---

# 302. CONTEXTUAL

Application layer.

Example:

```text
Builder item belongs to profile
AND currently builder-permitted
AND template permits it
```

---

# 303. AUTHORIZATION

Never a plain DB CHECK.

Application/security layer.

---

# 304. Check constraint examples

Media:

```text
file_size_bytes >= 0
```

---

# 305. Emotional Grid

```text
rows BETWEEN 1 AND 4
columns BETWEEN 2 AND 4
allowed pair set
```

---

# 306. Crop

```text
scale > 0
```

Rotation bounds may be defined by UI/product rules.

---

# 307. Opportunity

Stage belongs exact allowed set.

---

# 308. Notification

`attempt_count >= 0`.

---

# 309. Builder session

```text
expires_at > created_at
```

where required.

---

# 310. Artifact XOR

Exactly one source type.

---

# 311. Date ranges

Training:

```text
end_date >= start_date
```

when both exist.

---

# 312. Project years

```text
year_end >= year_start
```

where applicable.

---

# 313. Index principles

Indexes MUST follow observed query patterns.

Do not index every column automatically.

---

# 314. Mandatory FK indexes

PostgreSQL does not auto-create index on referencing FK column.

Frequently joined FK columns SHOULD be indexed.

---

# 315. Examples

```text
roles(project_id)
portfolio_items(profile_id)
media_derivatives(media_asset_id)
feedback(created_at)
opportunities(stage)
notifications(read_at, created_at)
background_jobs(status, scheduled_for)
```

---

# 316. Composite indexes

Examples:

```text
portfolio_items(profile_id, publication_state, display_order)
projects(profile_id, publication_state, is_featured)
feedback(profile_id, workflow_state, created_at DESC)
opportunities(profile_id, stage, updated_at DESC)
```

---

# 317. Partial indexes

Excellent for:

```text
active
unread
pending jobs
current published
primary
```

---

# 318. Background job claim index

Likely:

```text
(status, scheduled_for, priority)
WHERE status = 'queued'
```

or backend-specific equivalent.

---

# 319. Outbox dispatch index

```text
(processed_at, available_at)
WHERE processed_at IS NULL
```

---

# 320. Notification unread

```text
(created_at DESC)
WHERE read_at IS NULL
```

---

# 321. Search indexes

GIN/trigram as required.

---

# 322. JSONB GIN

Only if actual query requires searching inside JSON.

Do not add giant generic JSONB GIN indexes automatically.

---

# 323. Historical snapshot query

Usually by:

```text
revision_id
parent_id + revision_number
```

not arbitrary JSON contents.

---

# 324. Analytics indexes

Based on:

```text
event_type
occurred_at
attribution_source
```

as reporting evolves.

---

# 325. Table partitioning

Not required baseline.

---

# 326. Candidates later

If very large:

```text
analytics_events
audit_events
job_attempts
```

could be partitioned by time.

Only after measured need.

---

# 327. Database triggers

Use sparingly.

---

# 328. Good trigger candidates

Potential:

```text
updated_at
append-only guard
complex defensive constraint
```

if application tooling benefits.

---

# 329. Avoid business workflows in triggers

Do not implement:

```text
publishing Questionnaire
sending Notification
creating Casting
```

inside DB trigger.

---

# 330. Domain events

Created by Application transaction/outbox, not opaque database trigger side effects by default.

---

# 331. Views

SQL views MAY support stable read models.

---

# 332. Materialized views

Only for measured expensive projections/analytics.

Must remain rebuildable.

---

# 333. Database migrations

All schema changes MUST be version-controlled migrations.

---

# 334. No manual production-only schema drift

Normative.

---

# 335. Migration quality

Each migration SHOULD be:

```text
reviewable
repeatable
backward-aware
data-safe
```

---

# 336. Destructive migrations

Require explicit data migration/backfill/backup plan.

---

# 337. Expand-contract

Preferred for risky production schema evolution:

```text
expand
backfill
dual-read/write if necessary
switch
contract
```

---

# 338. Existing data

Do not add `NOT NULL` with fabricated default if historical data is legitimately unknown.

---

# 339. Example shooting date migration

If legacy EmotionalSession date missing:

do NOT assign:

```text
1970-01-01
today()
upload date
```

as fake professional truth.

Use migration exception/incomplete state requiring Human correction.

---

# 340. Baseline Revision migration

Existing currently published questionnaire may become:

```text
Revision 1
```

at migration time.

History before migration is unknown.

---

# 341. Migration provenance

Should record migration event/baseline source.

---

# 342. Legacy Project Other → Training

Migration must preserve:

```text
title
dates
description
media/evidence where appropriate
provenance
```

without silent loss.

---

# 343. Media migration

Preserve binary checksum/source identity.

Do not create duplicate binary per relation.

---

# 344. Contacts migration

Must explicitly map independent visibility flags.

Do not infer Builder permission from public visibility.

---

# 345. QR migration

Existing links become canonical QR-capable targets only after validation.

Do not invent QR records until required.

---

# 346. Search migration

Search index rebuild after authoritative migration.

Do not migrate old index as truth.

---

# 347. Cache migration

Cache discarded/rebuilt.

---

# 348. Job migration

Pending accepted jobs must remain compatible per DOC-076.

---

# 349. Data integrity diagnostics

System SHOULD be able to detect:

```text
orphan FK-like polymorphic references
missing Media binary
duplicate Primary
invalid current Revision pointer
Builder selection no longer eligible
historical artifact missing
```

---

# 350. Database alone cannot detect all

VOP/readiness/reconciliation complements constraints.

---

# 351. Transaction boundaries

Important multi-row operations MUST be transactional.

---

# 352. Primary switch

One transaction:

```text
unset old
set new
audit/outbox
commit
```

---

# 353. Questionnaire publish

One transaction for:

```text
readiness/version revalidation
Revision insert
current pointer
Audit
Outbox
```

Artifact rendering afterward as designed.

---

# 354. Feedback submit

One transaction:

```text
Feedback
attachment metadata
outbox
idempotency
```

then notification.

---

# 355. Opportunity transition

One transaction:

```text
current stage
history
audit
outbox
```

---

# 356. Casting from Feedback

Create Casting + provenance link transactionally where feasible.

---

# 357. Theme publish

Revision creation + audit/outbox transactional.

Activation may be separate explicit transaction.

---

# 358. Transaction duration

Do not hold DB transaction open across:

```text
AI API
WhatsApp
Email
Social provider
PDF renderer
video transcoding
```

---

# 359. External calls after commit

Per DOC-072/076.

---

# 360. DB-AP-001

**All professional data in one JSONB profile row**

---

# 361. DB-AP-002

**One universal `is_public` column replaces three Contact scopes**

---

# 362. DB-AP-003

**Supabase introduced as hidden dependency despite PostgreSQL baseline**

---

# 363. DB-AP-004

**Media binary stored as duplicate blob in Portfolio/Role tables**

---

# 364. DB-AP-005

**Deleting PortfolioItem cascades to MediaAsset**

---

# 365. DB-AP-006

**Emotional Grid stores modified image as new original**

---

# 366. DB-AP-007

**Questionnaire Revision updated in place**

---

# 367. DB-AP-008

**Revision number has no unique constraint**

---

# 368. DB-AP-009

**Builder Session stored only in Redis/cache**

---

# 369. DB-AP-010

**Feedback stored after WhatsApp delivery**

---

# 370. DB-AP-011

**AI extracted Casting requirement stored directly as confirmed value**

---

# 371. DB-AP-012

**Opportunity current stage exists only in analytics/history**

---

# 372. DB-AP-013

**Current stage updated without append history transaction**

---

# 373. DB-AP-014

**Notification read state reused as Feedback workflow state**

---

# 374. DB-AP-015

**OAuth token stored in social_accounts plaintext ordinary column**

---

# 375. DB-AP-016

**Background jobs contain provider secrets**

---

# 376. DB-AP-017

**Search documents used by authoritative FK**

---

# 377. DB-AP-018

**Audit used as substitute for actual current state**

---

# 378. DB-AP-019

**No optimistic version on high-impact mutable entities**

---

# 379. DB-AP-020

**Generic polymorphic relation replaces Project→Role FK**

---

# 380. DB-AP-021

**Hard delete used as normal content removal**

---

# 381. DB-AP-022

**Every enum implemented as permanent PostgreSQL enum without migration consideration**

---

# 382. DB-AP-023

**Every JSONB column gets GIN index without query need**

---

# 383. DB-AP-024

**External provider call occurs inside long DB transaction**

---

# 384. DB-AP-025

**Migration fabricates unknown professional facts to satisfy NOT NULL**

---

# 385. Canonical table catalogue

## Profile

```text
actor_profiles
actor_profile_localizations
```

## Media

```text
media_assets
media_derivatives
```

## Portfolio

```text
portfolio_items
```

## Emotional

```text
emotional_sessions
emotional_session_media
emotional_grids
emotional_grid_drafts
emotional_grid_cells
emotional_grid_revisions
emotional_grid_artifacts
```

## Experience

```text
projects
roles
role_media
performances
achievements
```

## Training

```text
training_items
```

## Skills/Languages

```text
skill_definitions
skill_definition_localizations
actor_skills
language_definitions
actor_languages
```

## Links/Contacts

```text
professional_links
professional_link_health
contacts
qr_artifacts
```

## Questionnaires

```text
questionnaire_definitions
questionnaire_drafts
questionnaire_draft_sections
questionnaire_draft_items
questionnaire_revisions
questionnaire_artifacts
```

## Public Builder

```text
public_builder_templates
public_builder_sessions
public_builder_session_items
public_builder_generation_snapshots
```

## Feedback

```text
feedback
feedback_attachments
feedback_notes
feedback_casting_links
```

## Castings

```text
castings
casting_sources
casting_source_assets
casting_analysis_revisions
casting_requirements
casting_profile_matches
casting_recommendations
```

## Opportunities

```text
opportunities
opportunity_stage_history
```

## Notifications

```text
notifications
notification_deliveries
notification_delivery_attempts
notification_preferences
```

## AI / BB

```text
ai_generation_requests
ai_drafts
```

## VOP

```text
vop_observations
vop_recommendations
vop_actions
```

## Themes

```text
site_themes
theme_drafts
theme_ai_proposals
theme_revisions
theme_activations
```

## Social

```text
social_accounts
social_posts
social_post_targets
```

## Support

```text
support_tickets
support_ticket_messages
help_articles
```

## Identity

```text
admin_users
[auth/session tables according to selected auth implementation]
```

## Infrastructure

```text
audit_events
outbox_events
background_jobs
background_job_attempts
search_documents
analytics_events
```

---

# 386. Tables intentionally NOT canonical Source

Avoid creating authoritative tables such as:

```text
public_profile
casting_quick_view
dashboard_items
readiness_scores
current_search_results
cached_profile
```

These are projections/derived views.

---

# 387. Physical schema flexibility

Implementation MAY merge or split supporting tables where:

1. semantic ownership remains correct;
2. constraints remain enforceable;
3. traceability remains;
4. no Source duplication is introduced.

---

# 388. Example acceptable simplification

`emotional_grid_drafts` may be represented directly on `emotional_grids` if only one draft exists at a time.

But immutable finalized revision still remains separate.

---

# 389. Example unacceptable simplification

Merge:

```text
QuestionnaireDraft
QuestionnaireRevision
```

into one mutable table with `published=true`.

Prohibited because it breaks immutability/history.

---

# 390. Data class mapping

| Table group                  | Data class              |
| ---------------------------- | ----------------------- |
| actor_profiles               | MASTER                  |
| portfolio_items              | MASTER/CONFIG           |
| emotional_sessions           | MASTER                  |
| questionnaire_drafts         | CONFIG                  |
| questionnaire_revisions      | REVISION/SNAPSHOT       |
| builder_sessions             | TEMPORARY authoritative |
| builder_generation_snapshots | SNAPSHOT                |
| feedback                     | OPERATIONAL             |
| castings                     | OPERATIONAL/DOMAIN      |
| ai_drafts                    | AI_OUTPUT               |
| vop_observations             | OPERATIONAL             |
| media_derivatives            | DERIVED                 |
| qr_artifacts                 | DERIVED                 |
| search_documents             | DERIVED                 |
| analytics_events             | ANALYTICAL              |
| audit_events                 | AUDIT                   |

---

# 391. Ownership mapping

Every table MUST have one clear owning module/domain.

Cross-domain reporting does not change ownership.

---

# 392. Mutation routing

Example:

```text
contacts
```

mutated only by Contacts module Application Service.

---

# 393. Questionnaire selection

May reference Contact but does not update Contact row.

---

# 394. Casting AI

May read Profile.

Does not update ActorSkill/ActorLanguage.

---

# 395. Analytics

May observe Opportunity.

Does not update stage.

---

# 396. Notification

References Feedback.

Does not resolve it.

---

# 397. Database ER overview

```text
ActorProfile
 ├── ProfileLocalization
 ├── MediaAsset
 │    └── MediaDerivative
 ├── PortfolioItem ──────────────► MediaAsset
 ├── EmotionalSession
 │    ├── SessionMedia ──────────► MediaAsset
 │    └── EmotionalGrid
 │         └── GridRevision
 │              └── GridArtifact
 ├── Project
 │    └── Role
 │         └── RoleMedia ────────► MediaAsset
 ├── Training
 ├── Achievement
 ├── ActorSkill ─────────────────► SkillDefinition
 ├── ActorLanguage ──────────────► LanguageDefinition
 ├── ProfessionalLink
 ├── Contact
 ├── QuestionnaireDefinition
 │    ├── Draft
 │    └── QuestionnaireRevision
 │         └── QuestionnaireArtifact
 ├── PublicBuilderSession
 │    └── GenerationSnapshot
 │         └── QuestionnaireArtifact
 ├── Feedback
 │    └── FeedbackCastingLink ───► Casting
 ├── Casting
 │    ├── CastingSource
 │    ├── AnalysisRevision
 │    │    └── Requirement
 │    └── Opportunity
 ├── AIDraft
 ├── SiteTheme
 │    └── ThemeRevision
 └── SocialPost
```

---

# 398. Infrastructure flow overview

```text
Domain Transaction
      │
      ├── Source Tables
      ├── AuditEvent
      └── OutboxEvent
              │
              ▼
        BackgroundJob
              │
      ┌───────┼────────┬─────────┐
      ▼       ▼        ▼         ▼
    Media    PDF    Notification Search
      │       │        │         │
      ▼       ▼        ▼         ▼
 Derived   Artifact  Delivery   Derived
```

---

# 399. High-value database invariants

At minimum implementation MUST preserve:

1. one Actor Profile root per configured site context;
2. actor-owned relations cannot cross profiles;
3. original MediaAsset identity remains immutable;
4. Media derivatives reference exact original;
5. one Primary Close-Up per profile;
6. one Primary Full Body per profile;
7. EmotionalSession has shooting date;
8. Grid dimensions use allowed set;
9. Grid cell position unique;
10. finalized Grid revision immutable;
11. Role belongs one Project;
12. Role media references existing MediaAsset;
13. Training is separate from Project;
14. ActorSkill unique per SkillDefinition;
15. ActorLanguage unique per LanguageDefinition;
16. Contact permissions independent;
17. ProfessionalLink canonical URL has one owner;
18. one Primary public Questionnaire per profile;
19. Questionnaire Revision number unique;
20. published Revision immutable;
21. current Revision pointer cannot intentionally cross parent;
22. Builder Session persists independently of cache;
23. Builder Generation Snapshot immutable;
24. Questionnaire Artifact has exactly one Revision/Snapshot source;
25. Feedback exists independently of Notification;
26. Feedback attachments private;
27. Feedback→Casting relation explicit;
28. Casting Analysis revision number unique;
29. AI extraction separate from Human-confirmed requirement;
30. Opportunity stage authoritative;
31. stage history append-only;
32. notification delivery channel-independent;
33. AI Draft generated text provenance retained;
34. Theme Revision immutable;
35. Search Index derived;
36. Analytics derived;
37. Audit append-only;
38. outbox durable;
39. job payload versioned;
40. secrets absent from ordinary payload tables;
41. high-impact mutable entities versioned;
42. hard cascades cannot destroy professional history accidentally.

---

# 400. Database quality gate

Перед реализацией/миграциями MUST быть проверены:

* [ ] every canonical table has owner;
* [ ] PK strategy consistent;
* [ ] all core relations use FK;
* [ ] all high-impact mutable entities have version;
* [ ] lifecycle/state columns typed;
* [ ] archive semantics explicit;
* [ ] revision entities immutable;
* [ ] revision numbers unique;
* [ ] Primary uniqueness enforced;
* [ ] Contact scopes independent;
* [ ] Builder Session is persistent;
* [ ] generated artifacts bind exact Revision/Snapshot;
* [ ] Media binaries are outside PostgreSQL;
* [ ] Media relations do not duplicate binaries;
* [ ] private attachments separated;
* [ ] AI output separated from Source;
* [ ] Opportunity has authoritative stage + history;
* [ ] Notification ≠ Feedback;
* [ ] outbox is durable;
* [ ] jobs are durable/versioned/idempotent;
* [ ] Search is rebuildable;
* [ ] Audit append-only;
* [ ] JSONB usage justified;
* [ ] JSONB schemas versioned;
* [ ] secrets excluded;
* [ ] FK delete actions reviewed;
* [ ] indexes map to actual reads;
* [ ] migrations preserve unknown-vs-known semantics;
* [ ] no fabricated professional data required by schema.

---

# 401. DB-E2E-001 — Primary Close-Up

Attempt to create two active Primary Close-Up PortfolioItems concurrently.

Expected:

```text
one succeeds
other conflicts/fails constraint
```

---

# 402. DB-E2E-002 — Primary Full Body

Same invariant.

---

# 403. DB-E2E-003 — Questionnaire Revision concurrency

Two concurrent publish operations attempt same next revision number.

Expected:

```text
unique/transactional allocation prevents duplicate revision numbers
```

---

# 404. DB-E2E-004 — Revision immutability

Attempt normal update of published Revision content.

Application rejects; database permission/guard may additionally block.

---

# 405. DB-E2E-005 — Artifact XOR

Attempt Artifact with both:

```text
questionnaire_revision_id
builder_generation_snapshot_id
```

Expected DB constraint failure.

---

# 406. DB-E2E-006 — Builder persistence

Evict all caches.

Builder Session remains in PostgreSQL and can continue until expiry.

---

# 407. DB-E2E-007 — Contact independence

Persist:

```text
public=false
admin_questionnaire=true
builder=false
```

No DB rule coerces flags.

---

# 408. DB-E2E-008 — Feedback notification isolation

Delete/fail Notification Delivery.

Feedback row remains.

---

# 409. DB-E2E-009 — Opportunity transition

Stage transition transaction updates Opportunity and appends exactly one history entry.

---

# 410. DB-E2E-010 — AI separation

Casting extraction exists.

Confirmed value remains NULL/unconfirmed until Human action.

---

# 411. DB-E2E-011 — Media reuse

One MediaAsset referenced by Portfolio and RoleMedia.

One binary identity.

---

# 412. DB-E2E-012 — Delete protection

Attempt delete MediaAsset referenced by Portfolio.

FK/relation policy prevents unsafe cascade.

---

# 413. DB-E2E-013 — Grid dimension

Attempt `1x1`.

Expected constraint/application rejection.

---

# 414. DB-E2E-014 — Grid cell position

Insert two cells for same Grid Draft position.

Expected unique violation.

---

# 415. DB-E2E-015 — Skill duplicate

Same SkillDefinition assigned twice to same profile.

Expected uniqueness violation.

---

# 416. DB-E2E-016 — Language duplicate

Same Language twice for same profile.

Expected uniqueness violation.

---

# 417. DB-E2E-017 — Outbox crash safety

Business transaction and Outbox event commit atomically.

Process crash does not lose event intent.

---

# 418. DB-E2E-018 — Job secret safety

Inspect background job payloads.

No provider credentials.

---

# 419. DB-E2E-019 — Search rebuild

Truncate derived Search documents.

Rebuild fully from authoritative tables.

---

# 420. DB-E2E-020 — Migration unknown data

Legacy Emotional record without Shooting Date is migrated as incomplete/requiring correction, not assigned fabricated professional date.

---

# 421. DB-E2E-021 — Historical questionnaire

Change current Contact.

Old QuestionnaireRevision snapshot remains unchanged.

---

# 422. DB-E2E-022 — Theme history

Publish Theme R1 and R2.

R1 row remains immutable after R2 activation.

---

# 423. DB-E2E-023 — Notification channels

Same Notification has WhatsApp FAILED and In-App SENT without state collision.

---

# 424. DB-E2E-024 — Feedback/Casting provenance

Create Casting from Feedback.

Both entities remain and explicit relation exists.

---

# 425. DB-E2E-025 — Booked provenance

Create Project Draft from Booked Opportunity.

Project references source Opportunity; Opportunity remains.

---

# 426. DB-E2E-026 — Audit append

Significant mutation creates Audit row.

Later source changes do not mutate previous Audit row.

---

# 427. DB-E2E-027 — Provider credential reference

Social account row contains secure credential reference but no plaintext access token.

---

# 428. DB-E2E-028 — Revision pointer

Questionnaire current pointer cannot be intentionally set to Revision belonging to another Questionnaire; Application transaction rejects and defensive DB strategy tested if implemented.

---

# 429. DB-E2E-029 — Archive

Archive Project.

Role/history remains preserved according to lifecycle; no uncontrolled cascade.

---

# 430. DB-E2E-030 — Restore

Restore PostgreSQL plus Media/Artifact Storage.

All relational references reconcile; derived Search/cache can rebuild.

---

# 431. Database traceability

Canonical:

```text
BUSINESS REQUIREMENT
       ↓
DOMAIN ENTITY / RULE
       ↓
TABLE / COLUMN
       ↓
CONSTRAINT
       ↓
APPLICATION COMMAND
       ↓
TEST
```

---

# 432. Example — Contact privacy

```text
BR: independent visibility scopes
       ↓
Contact Domain
       ↓
contacts.show_on_public_site
contacts.allow_in_admin_questionnaires
contacts.allow_in_public_questionnaire_builder
       ↓
Projection policies
       ↓
Privacy tests
```

---

# 433. Example — Questionnaire history

```text
BR: published questionnaire history immutable
       ↓
QuestionnaireRevision
       ↓
questionnaire_revisions
       ↓
UNIQUE(parent, revision_number)
immutable workflow
       ↓
PDF exact revision
       ↓
Historical E2E
```

---

# 434. Example — Casting AI

```text
Source Fact != AI Extraction != Human Decision
       ↓
casting_sources
casting_analysis_revisions
casting_requirements.extracted_value
casting_requirements.human_confirmed_value
       ↓
Human confirmation command
       ↓
Match
```

---

# 435. Example — Notifications

```text
Feedback persists before notification
       ↓
feedback
       ↓
outbox_events
       ↓
notifications
       ↓
notification_deliveries
```

---

# 436. Example — Media

```text
Immutable original
       ↓
media_assets
       ↓
media_derivatives
       ↓
portfolio_items / role_media / emotional_session_media
```

---

# 437. Architecture-to-schema mapping

| Architecture concept | Physical representation             |
| -------------------- | ----------------------------------- |
| Source of Truth      | normalized domain tables            |
| Projection           | query/view/derived table            |
| Revision             | immutable revision table            |
| Snapshot             | immutable JSON/normalized snapshot  |
| Artifact             | artifact metadata + external binary |
| Outbox               | `outbox_events`                     |
| Durable Job          | `background_jobs`                   |
| Audit                | `audit_events`                      |
| Search               | `search_documents`                  |
| Cache                | outside authoritative schema        |
| Media Binary         | external storage                    |
| AI Output            | dedicated AI/capability tables      |

---

# 438. Non-goals DOC-090

Настоящий документ **не фиксирует окончательно**:

```text
every exact varchar length
every final field in ActorProfile
every SQL migration statement
every index name
every API DTO
every retention period
every DB role/grant
```

Это детализируется следующими документами.

---

# 439. Required follow-up

DOC-091 должен превратить текущую схему в точный Data Dictionary:

```text
table
column
type
nullable
default
constraints
meaning
owner
sensitivity
```

---

# 440. Required visibility follow-up

DOC-092 зафиксирует:

```text
Public
Prepared Questionnaire
Builder
Admin
AI
Search
```

видимость данных на уровне fields/entities.

---

# 441. Required validation follow-up

DOC-093 зафиксирует cross-field/cross-entity validation, которую нельзя полностью выразить обычным DB constraint.

---

# 442. Required retention follow-up

DOC-094 зафиксирует:

```text
archive
hard delete
retention
redaction
legal/privacy deletion
orphan cleanup
historical preservation
```

---

# 443. Финальная архитектурная доктрина

> **PostgreSQL-схема платформы должна хранить профессиональную истину один раз, обеспечивать сильную ссылочную целостность для core Domain и физически отделять mutable Source, immutable Revision/Snapshot, AI Output, Derived Artifact, Operational state и Audit. Схема не должна компенсировать архитектурные проблемы универсальным JSONB, generic EAV или каскадным удалением. Database constraints защищают структурные и простые бизнес-инварианты, Application/Domain layer — contextual rules и authorization, а Search, Cache, Analytics и AI никогда не получают права становиться параллельным Source of Truth.**
