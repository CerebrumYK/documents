# DATA DICTIONARY

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативный словарь данных PostgreSQL

**Целевой файл:** `docs/database/data-dictionary.md`  
**Документ:** DOC-091  
**Статус:** ✅ Completed  
**Тип:** Database / Data Dictionary / Fields / Constraints / Sensitivity  
**Основание:** DOC-090 `docs/database/database-architecture.md`

---

# 1. Назначение

Настоящий документ является нормативным словарём данных целевой PostgreSQL-модели.

Для каждой таблицы и каждого значимого поля он определяет:

- физический тип;
- nullable;
- default;
- semantic meaning;
- data owner;
- mutability;
- sensitivity;
- FK;
- UNIQUE;
- CHECK;
- индексируемость;
- исторические/derived свойства.

Документ является мостом:

```text
Domain Model
    ↓
Database Schema
    ↓
Data Dictionary
    ↓
Validation Rules
    ↓
API Contracts
    ↓
Implementation
```

---

# 2. Нормативные обозначения

## 2.1 Mutability

| Код | Значение |
|---|---|
| `M` | Mutable current Source/Configuration |
| `I` | Immutable после создания |
| `A` | Append-only |
| `D` | Derived/rebuildable |
| `T` | Temporary authoritative state |

---

# 3. Sensitivity classes

| Код | Значение |
|---|---|
| `PUB` | Может быть публичным при выполнении publication/visibility rules |
| `PRO` | Professional data, не обязательно публичные |
| `PRI` | Private/PII |
| `INT` | Internal Admin/operational |
| `TECH` | Technical/infrastructure |
| `SEC` | Secret или security-sensitive |
| `AUD` | Audit/history |

`PUB` **не означает автоматически public**.

Public exposure всегда требует:

```text
publication
AND
visibility permission
AND
projection policy
```

---

# 4. Canonical PostgreSQL types

| Logical type | PostgreSQL |
|---|---|
| Identifier | `uuid` |
| Text | `text` |
| Boolean | `boolean` |
| Integer | `integer` |
| Version | `bigint` |
| Timestamp | `timestamptz` |
| Calendar date | `date` |
| Structured variable data | `jsonb` |
| Decimal measurement | `numeric(p,s)` |
| Search vector | `tsvector` |

---

# 5. Common mutable entity fields — `COMMON-M`

Если таблица далее обозначена `+ COMMON-M`, она содержит:

| Field | Type | Null | Default | Mut. | Sens. | Meaning |
|---|---|---:|---|---|---|---|
| `id` | uuid | No | generated | I | TECH | Stable entity identity |
| `version` | bigint | No | `1` | M | TECH | Optimistic concurrency version |
| `created_at` | timestamptz | No | now | I | TECH | Creation timestamp |
| `updated_at` | timestamptz | No | now | M | TECH | Last persisted mutation |

`version >= 1`.

---

# 6. Common profile-owned entity fields — `COMMON-P`

`COMMON-P = COMMON-M +`

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `profile_id` | uuid FK | No | Owning Actor Profile |

FK:

```text
profile_id → actor_profiles.id
ON DELETE RESTRICT
```

---

# 7. Common publishable item permissions — `COMMON-VIS`

Reusable professional source items SHOULD expose:

| Field | Type | Null | Default | Meaning |
|---|---|---:|---:|---|
| `show_on_public_site` | boolean | No | false | Eligible for ordinary public projection |
| `allow_in_admin_questionnaires` | boolean | No | true | May be selected into Admin questionnaire |
| `allow_in_public_questionnaire_builder` | boolean | No | false | May be exposed to Public Builder |

These flags are **independent**.

No implication is permitted between them.

---

# 8. Common ordering

Where used:

```text
display_order integer NOT NULL DEFAULT 0
CHECK display_order >= 0
```

---

# 9. Common lifecycle

No universal status enum is forced.

Publishable content generally distinguishes:

```text
draft
published
archived
```

where applicable.

`archived_at` is nullable.

---

# 10. Actor Profile domain

---

# 11. `actor_profiles`

**Owner:** Profile Domain  
**Class:** MASTER  
**Mutability:** M

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `id` | uuid | No | TECH | Actor Profile identity |
| `professional_name` | text | No | PUB | Main professional display name |
| `professional_title` | text | No | PUB | e.g. «Актриса» |
| `city` | text | Yes | PUB | Professional base city |
| `country_code` | text | Yes | PUB | ISO-like country identifier |
| `official_profile_slug` | text | No | PUB | Stable public profile slug |
| `status` | text | No | INT | Profile lifecycle |
| `version` | bigint | No | TECH | Optimistic version |
| `created_at` | timestamptz | No | TECH | Created |
| `updated_at` | timestamptz | No | TECH | Updated |

Constraints:

```text
UNIQUE(official_profile_slug)
CHECK trim(professional_name) <> ''
```

Supported status baseline:

```text
draft
active
archived
```

---

# 12. `actor_profile_parameters`

**Owner:** Profile Domain  
**Class:** MASTER  
**Cardinality:** normally 1:1 with Actor Profile

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `profile_id` | uuid PK/FK | No | TECH | Actor |
| `date_of_birth` | date | Yes | PRO | Confirmed birth date when used |
| `height_cm` | numeric(5,2) | Yes | PUB/PRO | Height in canonical centimetres |
| `shoe_size_eu` | numeric(4,1) | Yes | PUB/PRO | EU shoe size |
| `clothing_size` | text | Yes | PUB/PRO | Professional clothing size |
| `hair_color` | text | Yes | PUB | Confirmed description |
| `eye_color` | text | Yes | PUB | Confirmed description |
| `voice_type` | text | Yes | PRO | Professional voice description where used |
| `additional_parameters` | jsonb | No | PRO | Versioned uncommon typed extensions |
| `version` | bigint | No | TECH | Concurrency |
| `created_at` | timestamptz | No | TECH | Created |
| `updated_at` | timestamptz | No | TECH | Updated |

Constraints should prevent clearly invalid measurements.

Example:

```text
height_cm > 0
shoe_size_eu > 0
```

No guessed values.

---

# 13. `profile_field_policies`

**Owner:** Profile Domain / Visibility Configuration  
**Class:** CONFIG  
**Purpose:** independent visibility policy for scalar Profile fields.

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Policy identity |
| `profile_id` | uuid FK | No | Actor |
| `field_key` | text | No | Stable approved field identifier |
| `show_on_public_site` | boolean | No | Public permission |
| `allow_in_admin_questionnaires` | boolean | No | Admin questionnaire permission |
| `allow_in_public_questionnaire_builder` | boolean | No | Builder permission |
| `version` | bigint | No | Configuration version |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

Constraint:

```text
UNIQUE(profile_id, field_key)
```

`field_key` MUST come from application allowlist.

This table stores **policies, not facts**, therefore it does not violate the no-EAV doctrine.

---

# 14. `actor_profile_localizations`

**Owner:** Profile Domain  
**Class:** MASTER localized narrative

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `id` | uuid | No | TECH | Localization identity |
| `profile_id` | uuid FK | No | TECH | Actor |
| `locale` | text | No | TECH | Locale |
| `short_biography` | text | Yes | PUB | Compact professional bio |
| `biography` | text | Yes | PUB | Full approved biography |
| `version` | bigint | No | TECH | Concurrency |
| `created_at` | timestamptz | No | TECH | Created |
| `updated_at` | timestamptz | No | TECH | Updated |

Constraint:

```text
UNIQUE(profile_id, locale)
```

No runtime AI translation is persisted here without Human Apply.

---

# 15. Media domain

---

# 16. `media_assets`

**Owner:** Media Domain  
**Class:** MASTER technical source  
**Binary:** external storage

| Field | Type | Null | Mut. | Sens. | Meaning |
|---|---|---:|---|---|---|
| `id` | uuid | No | I | TECH | Media identity |
| `profile_id` | uuid FK | No | I | TECH | Owner |
| `media_type` | text | No | I | TECH | image/video/audio/document |
| `source_kind` | text | No | I | TECH | upload/server_import/migration/etc. |
| `original_filename` | text | No | I | PRI/TECH | Original filename |
| `mime_type` | text | No | I | TECH | Verified MIME |
| `file_size_bytes` | bigint | No | I | TECH | Original size |
| `checksum` | text | No | I | TECH | Cryptographic checksum |
| `storage_locator` | text | No | I | SEC/TECH | Internal managed locator |
| `processing_state` | text | No | M | TECH | Processing lifecycle |
| `lifecycle_state` | text | No | M | INT | active/archived/etc. |
| `technical_metadata` | jsonb | No | M/D | TECH | Extracted metadata |
| `created_at` | timestamptz | No | I | TECH | Created |
| `updated_at` | timestamptz | No | M | TECH | Processing/lifecycle update |
| `archived_at` | timestamptz | Yes | M | TECH | Archive timestamp |

Checks:

```text
file_size_bytes >= 0
```

Indexes:

```text
(profile_id, media_type)
checksum
processing_state
```

`storage_locator` MUST never appear in Public DTO.

---

# 17. `media_derivatives`

**Owner:** Media Domain  
**Class:** DERIVED

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Derivative identity |
| `media_asset_id` | uuid FK | No | Exact source |
| `derivative_type` | text | No | thumbnail/web/pdf/poster/etc. |
| `transform_spec` | jsonb | No | Deterministic transform |
| `transform_hash` | text | No | Canonical transform identity |
| `processor_version` | text | No | Rendering processor version |
| `storage_locator` | text | Yes | Ready output locator |
| `mime_type` | text | Yes | Output MIME |
| `width` | integer | Yes | Pixel width |
| `height` | integer | Yes | Pixel height |
| `file_size_bytes` | bigint | Yes | Binary size |
| `checksum` | text | Yes | Output integrity |
| `state` | text | No | queued/processing/ready/failed/stale |
| `created_at` | timestamptz | No | Creation |
| `updated_at` | timestamptz | No | Processing update |

Recommended uniqueness:

```text
UNIQUE(media_asset_id, derivative_type, transform_hash, processor_version)
```

---

# 18. Main Portfolio

---

# 19. `portfolio_items`

**Owner:** Portfolio Domain  
**Class:** MASTER/CONFIG  
**Pattern:** COMMON-P + COMMON-VIS

Additional fields:

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `media_asset_id` | uuid FK | No | Referenced immutable source |
| `category` | text | No | Portfolio category |
| `caption` | text | Yes | Approved caption |
| `publication_state` | text | No | draft/published/archived |
| `display_order` | integer | No | Order |
| `is_primary` | boolean | No | Primary within eligible category |
| `archived_at` | timestamptz | Yes | Archive |

Categories baseline:

```text
close_up
full_body
waist
profile
three_quarter
staged
other
```

Critical partial uniqueness:

```text
max 1 current Primary Close-Up/profile
max 1 current Primary Full Body/profile
```

---

# 20. Emotional Portfolio

---

# 21. `emotional_sessions`

**Owner:** Emotional Portfolio Domain  
**Class:** MASTER  
**Pattern:** COMMON-P + COMMON-VIS

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `title` | text | Yes | Session label |
| `shooting_date` | date | No* | Real shooting date |
| `description` | text | Yes | Approved description |
| `publication_state` | text | No | Lifecycle |
| `display_order` | integer | No | Order |
| `archived_at` | timestamptz | Yes | Archive |

`shooting_date` is mandatory for valid normal content.

Migration exceptions must be marked incomplete rather than fabricated.

---

# 22. `emotional_session_media`

**Owner:** Emotional Portfolio Domain  
**Class:** RELATION

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Relation |
| `emotional_session_id` | uuid FK | No | Session |
| `media_asset_id` | uuid FK | No | Original image |
| `display_order` | integer | No | Ordering |
| `created_at` | timestamptz | No | Attached |

Constraint:

```text
UNIQUE(emotional_session_id, media_asset_id)
```

---

# 23. `emotional_grids`

**Owner:** Emotional Grid Domain  
**Class:** Stable aggregate/config identity  
**Pattern:** COMMON-P + COMMON-VIS

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `emotional_session_id` | uuid FK | No | Source session |
| `name` | text | Yes | Admin name |
| `publication_state` | text | No | Lifecycle |
| `is_primary` | boolean | No | Primary grid marker |
| `current_draft_id` | uuid | Yes | Mutable current draft |
| `current_finalized_revision_id` | uuid | Yes | Current finalized revision |
| `display_order` | integer | No | Order |
| `archived_at` | timestamptz | Yes | Archive |

---

# 24. `emotional_grid_drafts`

**Owner:** Emotional Grid Domain  
**Class:** CONFIG / mutable

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Draft |
| `emotional_grid_id` | uuid FK | No | Parent |
| `rows` | integer | No | Grid rows |
| `columns` | integer | No | Grid columns |
| `version` | bigint | No | Concurrency |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

Allowed pairs exactly:

```text
1x2 1x3 1x4
2x2 2x3 2x4
3x2 3x3 3x4
4x2 4x3 4x4
```

---

# 25. `emotional_grid_cells`

**Owner:** Emotional Grid Domain  
**Class:** CONFIG

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Cell |
| `grid_draft_id` | uuid FK | No | Draft |
| `position` | integer | No | Stable row-major position |
| `media_asset_id` | uuid FK | No | Session source image |
| `crop_x` | numeric | No | Crop coordinate |
| `crop_y` | numeric | No | Crop coordinate |
| `scale` | numeric | No | Scale |
| `rotation_deg` | numeric | No | Rotation |
| `pan_x` | numeric | No | Pan |
| `pan_y` | numeric | No | Pan |
| `face_occupancy_estimate` | numeric | Yes | Assistive estimate only |
| `confirmation_state` | text | No | suggested/confirmed/etc. |
| `confirmed_at` | timestamptz | Yes | Human confirmation |
| `confirmed_by` | uuid FK | Yes | Admin |
| `version` | bigint | No | Concurrency |

Constraints:

```text
UNIQUE(grid_draft_id, position)
scale > 0
position >= 0
```

`face_occupancy_estimate` is not professional Source Fact.

---

# 26. `emotional_grid_revisions`

**Owner:** Emotional Grid Domain  
**Class:** REVISION  
**Mutability:** I

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Revision |
| `emotional_grid_id` | uuid FK | No | Parent |
| `revision_number` | integer | No | Parent-scoped number |
| `rows` | integer | No | Frozen rows |
| `columns` | integer | No | Frozen columns |
| `configuration_snapshot` | jsonb | No | Exact cells/transforms |
| `source_version_metadata` | jsonb | No | Source checksums/versions |
| `render_version` | text | No | Rendering contract |
| `finalized_at` | timestamptz | No | Finalized |
| `created_by` | uuid FK | No | Human actor |
| `created_at` | timestamptz | No | Created |

Constraint:

```text
UNIQUE(emotional_grid_id, revision_number)
```

No ordinary UPDATE.

---

# 27. `emotional_grid_artifacts`

**Owner:** Emotional Grid / Media Processing  
**Class:** DERIVED historical artifact

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Artifact |
| `emotional_grid_revision_id` | uuid FK | No | Exact revision |
| `artifact_type` | text | No | master/web/pdf/thumbnail |
| `renderer_version` | text | No | Renderer |
| `storage_locator` | text | Yes | Output |
| `mime_type` | text | Yes | MIME |
| `width` | integer | Yes | Width |
| `height` | integer | Yes | Height |
| `checksum` | text | Yes | Integrity |
| `state` | text | No | Generation state |
| `created_at` | timestamptz | No | Created |

---

# 28. Projects and professional experience

---

# 29. `projects`

**Owner:** Projects Domain  
**Class:** MASTER  
**Pattern:** COMMON-P + COMMON-VIS

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `project_type` | text | No | Film/theatre/commercial/etc. |
| `title` | text | No | Project title |
| `slug` | text | Yes | Public deep-link slug |
| `year_start` | integer | Yes | Start year |
| `year_end` | integer | Yes | End year |
| `description` | text | Yes | Approved narrative |
| `publication_state` | text | No | Lifecycle |
| `is_featured` | boolean | No | Selected public emphasis |
| `display_order` | integer | No | Order |
| `source_opportunity_id` | uuid FK | Yes | Provenance from Booked |
| `archived_at` | timestamptz | Yes | Archive |

Checks:

```text
year_end >= year_start
```

when both present.

---

# 30. `roles`

**Owner:** Projects Domain  
**Class:** MASTER  
**Pattern:** COMMON-P + COMMON-VIS

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `project_id` | uuid FK | No | Parent Project |
| `name` | text | No | Role name |
| `description` | text | Yes | Approved description |
| `publication_state` | text | No | Lifecycle |
| `display_order` | integer | No | Order |
| `source_opportunity_id` | uuid FK | Yes | Provenance |
| `archived_at` | timestamptz | Yes | Archive |

Invariant:

```text
Role.profile_id = Project.profile_id
```

---

# 31. `role_media`

**Owner:** Projects Domain  
**Class:** RELATION/CONFIG

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Relation |
| `role_id` | uuid FK | No | Role |
| `media_asset_id` | uuid FK | No | Reused MediaAsset |
| `media_usage_type` | text | No | Role photo/etc. |
| `display_order` | integer | No | Order |
| `show_on_home` | boolean | No | “В образе” home showcase |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

Business limits:

```text
max role photos = 5
max show_on_home = 2
```

enforced transactionally.

---

# 32. `performances`

**Owner:** Projects Domain  
**Class:** MASTER

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Performance |
| `project_id` | uuid FK | No | Project |
| `role_id` | uuid FK | Yes | Role |
| `performance_date` | date/timestamptz | Yes | Event date/time according to use |
| `venue` | text | Yes | Venue |
| `location` | text | Yes | Location |
| `description` | text | Yes | Description |
| `publication_state` | text | No | Lifecycle |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

---

# 33. Training and achievements

---

# 34. `training_items`

**Owner:** Training Domain  
**Class:** MASTER  
**Pattern:** COMMON-P + COMMON-VIS

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `title` | text | No | Course/program |
| `institution` | text | Yes | Institution |
| `start_date` | date | Yes | Start |
| `end_date` | date | Yes | End |
| `completion_date` | date | Yes | Completion |
| `description` | text | Yes | Approved description |
| `publication_state` | text | No | Lifecycle |
| `display_order` | integer | No | Order |
| `migration_source_project_id` | uuid | Yes | Legacy provenance |
| `archived_at` | timestamptz | Yes | Archive |

Check:

```text
end_date >= start_date
```

---

# 35. `achievements`

**Owner:** Achievements Domain  
**Class:** MASTER  
**Pattern:** COMMON-P + COMMON-VIS

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `title` | text | No | Achievement |
| `issuer` | text | Yes | Issuer/organization |
| `achievement_date` | date | Yes | Date |
| `description` | text | Yes | Description |
| `project_id` | uuid FK | Yes | Optional provenance/context |
| `role_id` | uuid FK | Yes | Optional context |
| `publication_state` | text | No | Lifecycle |
| `display_order` | integer | No | Order |
| `archived_at` | timestamptz | Yes | Archive |

---

# 36. Skills

---

# 37. `skill_definitions`

**Owner:** Competency Domain  
**Class:** REFERENCE

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Skill definition |
| `code` | text | No | Stable code |
| `default_label` | text | No | Default label |
| `category` | text | Yes | Group |
| `active` | boolean | No | Selectable |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

Constraint:

```text
UNIQUE(code)
```

---

# 38. `skill_definition_localizations`

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `skill_definition_id` | uuid FK | No | Skill |
| `locale` | text | No | Locale |
| `label` | text | No | Localized name |
| `aliases` | jsonb | No | Controlled search aliases |

PK/unique:

```text
UNIQUE(skill_definition_id, locale)
```

---

# 39. `actor_skills`

**Owner:** Competency Domain  
**Class:** MASTER  
**Pattern:** COMMON-P + COMMON-VIS

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `skill_definition_id` | uuid FK | No | Skill |
| `level` | smallint | No | 1–5 |
| `description` | text | Yes | Professional clarification |
| `publication_state` | text | No | Lifecycle |
| `display_order` | integer | No | Order |

Constraints:

```text
UNIQUE(profile_id, skill_definition_id)
CHECK level BETWEEN 1 AND 5
```

---

# 40. Languages

---

# 41. `language_definitions`

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Language |
| `code` | text | No | Stable language code |
| `default_label` | text | No | Name |
| `active` | boolean | No | Selectable |

`UNIQUE(code)`.

---

# 42. `language_definition_localizations`

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `language_definition_id` | uuid FK | No | Language |
| `locale` | text | No | Display locale |
| `label` | text | No | Localized name |

Constraint:

```text
UNIQUE(language_definition_id, locale)
```

---

# 43. `actor_languages`

**Owner:** Competency Domain  
**Class:** MASTER  
**Pattern:** COMMON-P + COMMON-VIS

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `language_definition_id` | uuid FK | No | Language |
| `cefr_level` | text | Yes | A1…C2 |
| `is_native` | boolean | No | Native language |
| `description` | text | Yes | Clarification |
| `publication_state` | text | No | Lifecycle |
| `display_order` | integer | No | Order |

Constraint:

```text
UNIQUE(profile_id, language_definition_id)
```

Valid CEFR:

```text
A1 A2 B1 B2 C1 C2
```

`is_native=true` MUST follow explicit validation rule and cannot contradict stored proficiency semantics.

---

# 44. Professional Links

---

# 45. `professional_links`

**Owner:** Professional Media/Links Domain  
**Class:** MASTER  
**Pattern:** COMMON-P + COMMON-VIS

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `link_type` | text | No | video/audio/external/etc. |
| `description` | text | No | Human-readable description |
| `canonical_url` | text | No | Exact authoritative URL |
| `publication_state` | text | No | Lifecycle |
| `display_order` | integer | No | Editor row/order |
| `archived_at` | timestamptz | Yes | Archive |

URL must use approved safe scheme.

`display_order + 1` MAY become visible row number.

---

# 46. `professional_link_health`

**Owner:** Link Health Processor  
**Class:** DERIVED OPERATIONAL

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `professional_link_id` | uuid PK/FK | No | Link |
| `status` | text | No | healthy/degraded/failed/unknown |
| `checked_at` | timestamptz | Yes | Last check |
| `http_status` | integer | Yes | HTTP result |
| `failure_code` | text | Yes | Stable error |
| `consecutive_failures` | integer | No | Failure count |
| `updated_at` | timestamptz | No | Updated |

This table does not control publication automatically.

---

# 47. Contacts

---

# 48. `contacts`

**Owner:** Contacts Domain  
**Class:** MASTER/CONFIG  
**Pattern:** COMMON-P + COMMON-VIS

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `contact_type` | text | No | PRO | phone/email/WhatsApp/social/etc. |
| `label` | text | Yes | PUB/PRO | Display label |
| `relationship_type` | text | Yes | PRO | self/parent/guardian/agent/etc. |
| `person_name` | text | Yes | PRI/PRO | Contact person |
| `value` | text | No | PRI/PRO | Original value |
| `normalized_value` | text | Yes | PRI | Canonical searchable/contact form |
| `display_order` | integer | No | TECH | Order |
| `lifecycle_state` | text | No | INT | active/archived |
| `archived_at` | timestamptz | Yes | TECH | Archive |

Public exposure is server-side permission controlled.

---

# 49. QR subsystem

---

# 50. `qr_artifacts`

**Owner:** QR Infrastructure  
**Class:** DERIVED

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `id` | uuid | No | TECH | QR artifact |
| `target_url` | text | No | PUB/PRO | Exact encoded canonical URL |
| `target_url_hash` | text | No | TECH | Cache identity |
| `settings` | jsonb | No | TECH | QR generation settings |
| `settings_version` | text | No | TECH | Settings schema |
| `settings_hash` | text | No | TECH | Identity |
| `storage_locator` | text | Yes | SEC/TECH | Artifact |
| `mime_type` | text | Yes | TECH | SVG/PNG/etc. |
| `checksum` | text | Yes | TECH | Artifact integrity |
| `validation_state` | text | No | TECH | pending/valid/invalid |
| `decoded_url` | text | Yes | TECH | Decoder validation result |
| `validated_at` | timestamptz | Yes | TECH | Validation time |
| `created_at` | timestamptz | No | TECH | Created |

READY requirement:

```text
decoded_url = target_url
```

Admin/private/unsafe URLs MUST fail eligibility before artifact use.

---

# 51. Questionnaires

---

# 52. `questionnaire_definitions`

**Owner:** Questionnaire Domain  
**Class:** Stable aggregate identity  
**Pattern:** COMMON-P

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `name` | text | No | Admin/public version name |
| `questionnaire_type` | text | No | compact/extended/casting/custom |
| `locale_policy` | jsonb | No | Supported/default locales |
| `lifecycle_state` | text | No | Lifecycle |
| `is_primary_public` | boolean | No | Recommended public questionnaire |
| `current_draft_id` | uuid | Yes | Current draft |
| `current_published_revision_id` | uuid | Yes | Current published Revision |
| `casting_id` | uuid FK | Yes | Casting-specific context |
| `archived_at` | timestamptz | Yes | Archive |

Partial unique constraint:

```text
max 1 current primary public Questionnaire/profile
```

---

# 53. `questionnaire_drafts`

**Owner:** Questionnaire Domain  
**Class:** CONFIG

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Draft |
| `questionnaire_definition_id` | uuid FK | No | Parent |
| `configuration` | jsonb | No | Non-relational renderer/options configuration |
| `schema_version` | text | No | Draft config schema |
| `source_version_vector` | jsonb | No | Versions used for readiness |
| `readiness_state` | text | No | Derived current readiness |
| `version` | bigint | No | Concurrency |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

---

# 54. `questionnaire_draft_sections`

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Section config |
| `questionnaire_draft_id` | uuid FK | No | Draft |
| `section_type` | text | No | Semantic block |
| `enabled` | boolean | No | Included |
| `display_order` | integer | No | Order |
| `configuration` | jsonb | No | Section-specific options |

Constraint:

```text
UNIQUE(questionnaire_draft_id, section_type)
```

---

# 55. `questionnaire_draft_items`

**Class:** CONFIG relation

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Selection |
| `questionnaire_draft_id` | uuid FK | No | Draft |
| `section_type` | text | No | Parent semantic section |
| `entity_type` | text | No | Allowlisted source type |
| `entity_id` | uuid | No | Selected source entity |
| `display_order` | integer | No | Item order |
| `configuration` | jsonb | No | e.g. show QR |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

Polymorphic target requires Application revalidation.

---

# 56. `questionnaire_revisions`

**Owner:** Questionnaire Domain  
**Class:** REVISION/SNAPSHOT  
**Mutability:** I

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `id` | uuid | No | TECH | Revision |
| `questionnaire_definition_id` | uuid FK | No | TECH | Parent |
| `revision_number` | integer | No | PUB/TECH | Sequential version |
| `snapshot_schema_version` | text | No | TECH | Snapshot contract |
| `content_snapshot` | jsonb | No | PRO | Frozen document semantics |
| `source_version_vector` | jsonb | No | TECH | Exact source versions |
| `document_model_version` | text | No | TECH | Document model |
| `published_at` | timestamptz | No | PUB | Publication |
| `published_by` | uuid FK | No | INT | Human publisher |
| `created_at` | timestamptz | No | TECH | Created |

Constraint:

```text
UNIQUE(questionnaire_definition_id, revision_number)
```

No ordinary semantic UPDATE.

---

# 57. `questionnaire_artifacts`

**Owner:** Document subsystem  
**Class:** DERIVED/HISTORICAL ARTIFACT

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Artifact |
| `questionnaire_revision_id` | uuid FK | Yes | Prepared source |
| `builder_generation_snapshot_id` | uuid FK | Yes | Builder source |
| `artifact_type` | text | No | pdf/html/etc. |
| `locale` | text | No | Locale |
| `document_model_version` | text | No | Semantic model |
| `template_version` | text | No | Layout |
| `renderer_version` | text | No | Renderer |
| `storage_locator` | text | Yes | Binary |
| `mime_type` | text | Yes | MIME |
| `file_size_bytes` | bigint | Yes | Size |
| `checksum` | text | Yes | Integrity |
| `access_class` | text | No | public/token/admin/etc. |
| `state` | text | No | queued/generating/ready/failed |
| `generated_at` | timestamptz | Yes | Completed |
| `created_at` | timestamptz | No | Created |

Critical CHECK:

```text
exactly one of
questionnaire_revision_id
builder_generation_snapshot_id
is non-null
```

---

# 58. Public Questionnaire Builder

---

# 59. `public_builder_templates`

**Owner:** Builder Domain  
**Class:** CONFIG

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Template |
| `profile_id` | uuid FK | Yes | Profile-specific override |
| `code` | text | No | Stable template code |
| `name` | text | No | Display name |
| `template_type` | text | No | quick/standard/extended/casting |
| `configuration` | jsonb | No | Defaults/rules |
| `schema_version` | text | No | Config schema |
| `is_enabled` | boolean | No | Public availability |
| `display_order` | integer | No | Order |
| `version` | bigint | No | Concurrency |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

---

# 60. `public_builder_sessions`

**Owner:** Builder Domain  
**Class:** TEMPORARY authoritative  
**Mutability:** T

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `id` | uuid | No | PRI/TECH | Opaque session |
| `profile_id` | uuid FK | No | TECH | Actor |
| `template_id` | uuid FK | Yes | TECH | Template origin |
| `source_questionnaire_definition_id` | uuid FK | Yes | TECH | Prepared questionnaire origin |
| `source_questionnaire_revision_id` | uuid FK | Yes | TECH | Starting Revision |
| `locale` | text | No | PRO | Locale |
| `casting_name` | text | Yes | PRI/PRO | Visitor temporary label |
| `project_name` | text | Yes | PRI/PRO | Temporary label |
| `role_name` | text | Yes | PRI/PRO | Temporary label |
| `configuration` | jsonb | No | PRI/PRO | Temporary choices |
| `schema_version` | text | No | TECH | Schema |
| `version` | bigint | No | TECH | Concurrency |
| `status` | text | No | TECH | active/expired/etc. |
| `expires_at` | timestamptz | No | TECH | Expiration |
| `created_at` | timestamptz | No | TECH | Created |
| `updated_at` | timestamptz | No | TECH | Updated |

It MUST NOT mutate Actor Profile.

---

# 61. `public_builder_session_items`

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Selection |
| `session_id` | uuid FK | No | Builder Session |
| `section_type` | text | No | Section |
| `entity_type` | text | No | Source type |
| `entity_id` | uuid | No | Source |
| `display_order` | integer | No | Order |
| `selected` | boolean | No | Current selection |
| `configuration` | jsonb | No | Item config |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

Current eligibility is revalidated server-side.

---

# 62. `public_builder_generation_snapshots`

**Owner:** Builder Domain  
**Class:** SNAPSHOT  
**Mutability:** I

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Snapshot |
| `session_id` | uuid FK | No | Source session |
| `profile_id` | uuid FK | No | Actor |
| `generation_number` | integer | No | Per-session generation |
| `locale` | text | No | Locale |
| `snapshot_schema_version` | text | No | Schema |
| `content_snapshot` | jsonb | No | Exact frozen selected content |
| `source_version_vector` | jsonb | No | Source versions |
| `generated_at` | timestamptz | No | Frozen |
| `created_at` | timestamptz | No | Created |

Constraint:

```text
UNIQUE(session_id, generation_number)
```

---

# 63. Feedback

---

# 64. `feedback`

**Owner:** Feedback Domain  
**Class:** OPERATIONAL BUSINESS DATA  
**Pattern:** COMMON-P

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `feedback_type` | text | No | PRO | casting/role/collaboration/etc. |
| `sender_name` | text | Yes | PRI | Sender |
| `sender_organization` | text | Yes | PRI/PRO | Organization |
| `sender_email` | text | Yes | PRI | Email |
| `sender_phone` | text | Yes | PRI | Phone |
| `message` | text | No | PRI | Original inbound content |
| `source_context_type` | text | Yes | INT | Origin context |
| `source_context_id` | uuid | Yes | INT | Origin entity |
| `questionnaire_session_id` | uuid FK | Yes | INT | Builder provenance |
| `workflow_state` | text | No | INT | Inbox workflow |
| `responsible_admin_user_id` | uuid FK | Yes | INT | Owner |
| `next_action` | text | Yes | INT | Planned action |
| `next_action_at` | timestamptz | Yes | INT | Due |
| `read_at` | timestamptz | Yes | INT | Inbox read state |
| `submission_idempotency_key` | text | Yes | SEC/TECH | Duplicate protection |

`submission_idempotency_key` SHOULD be uniquely indexed where present.

---

# 65. `feedback_attachments`

**Owner:** Feedback Domain  
**Class:** PRIVATE

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Attachment |
| `feedback_id` | uuid FK | No | Feedback |
| `storage_locator` | text | No | Private storage |
| `original_filename` | text | No | Filename |
| `mime_type` | text | No | Verified MIME |
| `file_size_bytes` | bigint | No | Size |
| `checksum` | text | No | Integrity |
| `security_state` | text | No | pending/safe/quarantined/rejected |
| `created_at` | timestamptz | No | Created |

Never publicly static by default.

---

# 66. `feedback_notes`

**Owner:** Feedback Domain  
**Class:** INTERNAL

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Note |
| `feedback_id` | uuid FK | No | Feedback |
| `admin_user_id` | uuid FK | No | Author |
| `note` | text | No | Internal note |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

Never Public/Builder/AI unless an explicit task requires scoped access.

---

# 67. `feedback_casting_links`

**Owner:** Feedback/Casting orchestration  
**Class:** PROVENANCE

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `feedback_id` | uuid FK | No | Source inquiry |
| `casting_id` | uuid FK | No | Created/linked Casting |
| `link_type` | text | No | created_from/related/etc. |
| `created_by` | uuid FK | No | Admin/system actor |
| `created_at` | timestamptz | No | Linked |

Recommended composite PK/UNIQUE:

```text
(feedback_id, casting_id, link_type)
```

---

# 68. Castings

---

# 69. `castings`

**Owner:** Casting Domain  
**Class:** OPERATIONAL/DOMAIN  
**Pattern:** COMMON-P

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `title` | text | No | PRI/PRO | Casting title |
| `project_name` | text | Yes | PRI/PRO | Project |
| `role_name` | text | Yes | PRI/PRO | Role |
| `organization` | text | Yes | PRI/PRO | Client/company |
| `deadline_at` | timestamptz | Yes | PRI/PRO | Deadline |
| `status` | text | No | INT | Casting workflow |
| `source_feedback_id` | uuid FK | Yes | INT | Provenance shortcut |
| `closed_at` | timestamptz | Yes | INT | Closure |

Casting data is Admin-private by default.

---

# 70. `casting_sources`

**Owner:** Casting Domain  
**Class:** SOURCE

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `id` | uuid | No | TECH | Source |
| `casting_id` | uuid FK | No | TECH | Casting |
| `source_type` | text | No | INT | pasted_text/file/image/etc. |
| `text_content` | text | Yes | PRI | Original pasted/extracted textual source |
| `source_version` | bigint | No | TECH | Source semantic version |
| `created_at` | timestamptz | No | TECH | Created |
| `updated_at` | timestamptz | No | TECH | Updated |

`source_version` changes on material source modification.

---

# 71. `casting_source_assets`

**Owner:** Casting Domain  
**Class:** PRIVATE SOURCE

Fields analogous to private Feedback attachment:

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

No automatic public Media promotion.

---

# 72. `casting_analysis_revisions`

**Owner:** Casting AI  
**Class:** AI OUTPUT REVISION  
**Mutability:** I

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Analysis revision |
| `casting_id` | uuid FK | No | Casting |
| `revision_number` | integer | No | Sequential |
| `source_snapshot` | jsonb | No | Exact Casting source snapshot |
| `profile_snapshot` | jsonb | No | Relevant confirmed profile context |
| `source_version_vector` | jsonb | No | Versions |
| `prompt_version` | text | No | Prompt |
| `provider` | text | No | Provider metadata |
| `model` | text | No | Model |
| `model_metadata` | jsonb | No | Generation metadata |
| `analysis_summary` | text | Yes | AI summary |
| `structured_output` | jsonb | No | Validated output |
| `status` | text | No | valid/failed/stale/etc. |
| `generated_at` | timestamptz | No | Generated |
| `created_at` | timestamptz | No | Created |

Constraint:

```text
UNIQUE(casting_id, revision_number)
```

---

# 73. `casting_requirements`

**Owner:** Casting Domain + Casting AI review boundary

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Requirement |
| `casting_analysis_revision_id` | uuid FK | No | AI analysis |
| `category` | text | No | Requirement category |
| `extracted_value` | jsonb | Yes | AI extracted value |
| `source_evidence` | jsonb | Yes | Evidence/source location |
| `extraction_state` | text | No | extracted/not_specified/etc. |
| `human_confirmed_value` | jsonb | Yes | Human decision |
| `human_decision_state` | text | No | pending/confirmed/modified/rejected |
| `confirmed_by` | uuid FK | Yes | Admin |
| `confirmed_at` | timestamptz | Yes | Decision time |
| `version` | bigint | No | Concurrency |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

Critical doctrine:

```text
extracted_value != human_confirmed_value
```

by ownership, even when values happen to be equal.

---

# 74. `casting_profile_matches`

**Owner:** Casting Domain  
**Class:** DERIVED

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Match |
| `casting_id` | uuid FK | No | Casting |
| `analysis_revision_id` | uuid FK | No | Analysis |
| `requirement_id` | uuid FK | No | Requirement |
| `profile_source_type` | text | No | Compared fact type |
| `profile_source_id` | uuid | Yes | Exact source |
| `match_state` | text | No | match/partial/mismatch/no_data/not_applicable |
| `profile_value_snapshot` | jsonb | Yes | Compared profile value |
| `reason` | text | No | Explainable rationale |
| `algorithm_version` | text | No | Comparison engine |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Recomputed |

`NO_CONFIRMED_DATA` MUST remain distinct from `MISMATCH`.

---

# 75. `casting_recommendations`

**Owner:** Casting AI/Application  
**Class:** AI OUTPUT

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Recommendation |
| `casting_id` | uuid FK | No | Casting |
| `analysis_revision_id` | uuid FK | No | Analysis |
| `recommendation_type` | text | No | Type |
| `recommendation` | text | No | Suggested action |
| `rationale` | text | No | Reason |
| `status` | text | No | pending/accepted/rejected/etc. |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

Does not mutate business state.

---

# 76. Opportunity Pipeline

---

# 77. `opportunities`

**Owner:** Opportunity Domain  
**Class:** BUSINESS OPERATIONAL  
**Pattern:** COMMON-P

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `casting_id` | uuid FK | Yes | Related Casting |
| `title` | text | No | Opportunity title |
| `stage` | text | No | Authoritative pipeline stage |
| `responsible_admin_user_id` | uuid FK | Yes | Owner |
| `next_action` | text | Yes | Planned next action |
| `next_action_at` | timestamptz | Yes | Due |
| `closed_at` | timestamptz | Yes | Terminal timestamp |

Exact stages:

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

# 78. `opportunity_stage_history`

**Owner:** Opportunity Domain  
**Class:** APPEND-ONLY HISTORY

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Transition |
| `opportunity_id` | uuid FK | No | Opportunity |
| `from_stage` | text | Yes | Previous |
| `to_stage` | text | No | New |
| `reason` | text | Yes | Human/system rationale |
| `changed_by` | uuid FK | Yes | Human/system actor |
| `changed_at` | timestamptz | No | Transition time |
| `correlation_id` | uuid | Yes | Traceability |

Current stage + history MUST commit atomically.

---

# 79. Notifications

---

# 80. `notifications`

**Owner:** Notification Domain  
**Class:** OPERATIONAL

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Notification |
| `notification_type` | text | No | Semantic type |
| `priority` | text | No | critical/high/normal/low |
| `source_type` | text | No | Source domain type |
| `source_id` | uuid | No | Source identity |
| `source_event_id` | uuid | Yes | Event provenance |
| `dedup_key` | text | Yes | Semantic dedup |
| `title_key` | text | No | Localization template key |
| `message_key` | text | No | Localization key |
| `template_variables` | jsonb | No | Safe minimal values |
| `admin_route` | text | Yes | Protected Admin deep link |
| `read_at` | timestamptz | Yes | Notification read |
| `archived_at` | timestamptz | Yes | Notification archive |
| `created_at` | timestamptz | No | Created |

No source business payload duplication unless needed.

---

# 81. `notification_deliveries`

**Owner:** Notification Domain  
**Class:** DELIVERY

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `id` | uuid | No | TECH | Delivery identity/idempotency |
| `notification_id` | uuid FK | No | TECH | Notification |
| `channel` | text | No | TECH | in_app/whatsapp/email |
| `recipient_identity` | text | Yes | PRI | Destination |
| `template_id` | text | No | TECH | Template |
| `template_version` | text | No | TECH | Version |
| `payload_snapshot` | jsonb | No | PRI/INT | Exact minimal delivery semantics |
| `status` | text | No | TECH | lifecycle |
| `scheduled_at` | timestamptz | Yes | TECH | Send time |
| `expires_at` | timestamptz | Yes | TECH | Relevance expiry |
| `attempt_count` | integer | No | TECH | Attempts |
| `provider_message_id` | text | Yes | INT | Provider trace |
| `last_error_code` | text | Yes | TECH | Error |
| `last_error_at` | timestamptz | Yes | TECH | Error time |
| `sent_at` | timestamptz | Yes | TECH | Accepted/sent |
| `created_at` | timestamptz | No | TECH | Created |
| `updated_at` | timestamptz | No | TECH | Updated |

No provider credential.

---

# 82. `notification_delivery_attempts`

**Class:** APPEND-ORIENTED TECHNICAL HISTORY

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Attempt |
| `notification_delivery_id` | uuid FK | No | Delivery |
| `attempt_number` | integer | No | Sequence |
| `started_at` | timestamptz | No | Start |
| `finished_at` | timestamptz | Yes | End |
| `result_state` | text | No | success/failure/unknown |
| `error_code` | text | Yes | Stable error |
| `provider_request_id` | text | Yes | Provider trace |
| `safe_provider_metadata` | jsonb | Yes | Sanitized metadata |

Constraint:

```text
UNIQUE(notification_delivery_id, attempt_number)
```

---

# 83. `notification_preferences`

**Owner:** Notification Configuration  
**Class:** CONFIG

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Preference |
| `admin_user_id` | uuid FK | Yes | User-specific or global |
| `notification_type` | text | No | Category |
| `in_app_enabled` | boolean | No | In-App |
| `whatsapp_enabled` | boolean | No | WhatsApp |
| `email_enabled` | boolean | No | Email |
| `quiet_hours_policy` | jsonb | No | Scheduling rule |
| `version` | bigint | No | Concurrency |
| `updated_at` | timestamptz | No | Updated |

---

# 84. `notification_recipients`

**Owner:** Notification Configuration  
**Class:** CONFIG/PRIVATE

This table is normative to prevent reuse of professional Contacts as operational recipients.

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Recipient config |
| `admin_user_id` | uuid FK | Yes | Optional owner |
| `channel` | text | No | whatsapp/email |
| `recipient_value` | text | No | Phone/email |
| `normalized_value` | text | No | Canonical destination |
| `is_enabled` | boolean | No | Enabled |
| `is_primary` | boolean | No | Default target |
| `version` | bigint | No | Concurrency |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

---

# 85. BB Assistant / shared AI generation

---

# 86. `ai_generation_requests`

**Owner:** AI Infrastructure/Application  
**Class:** OPERATIONAL AI

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Generation intent |
| `profile_id` | uuid FK | Yes | Actor context |
| `capability` | text | No | casting/bb/theme/vop |
| `task_type` | text | No | Task |
| `source_snapshot` | jsonb | No | Minimal exact context |
| `source_version_vector` | jsonb | No | Versions |
| `prompt_version` | text | No | Prompt |
| `provider` | text | No | Provider |
| `model` | text | No | Model |
| `status` | text | No | requested/generating/completed/failed |
| `idempotency_key` | text | No | Duplicate protection |
| `requested_by` | uuid FK | Yes | Admin |
| `created_at` | timestamptz | No | Created |
| `completed_at` | timestamptz | Yes | Finished |
| `error_code` | text | Yes | Stable failure |

No API keys.

---

# 87. `ai_drafts`

**Owner:** BB Assistant  
**Class:** AI OUTPUT  
**Pattern:** canonical master model

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Draft |
| `profile_id` | uuid FK | No | Actor |
| `context_entity_type` | text | Yes | Source context |
| `context_entity_id` | uuid | Yes | Context entity |
| `task_type` | text | No | biography/etc. |
| `language` | text | No | Output locale |
| `tone` | text | Yes | Requested tone |
| `length` | text | Yes | Length preset |
| `source_snapshot` | jsonb | No | Facts used |
| `source_version_vector` | jsonb | No | Versions |
| `generated_text` | text | No | Original AI output |
| `edited_text` | text | Yes | Human-edited version |
| `provider` | text | No | Provider |
| `model` | text | No | Model |
| `model_metadata` | jsonb | No | Metadata |
| `prompt_version` | text | No | Prompt |
| `status` | text | No | generated/edited/applied/discarded/stale |
| `target_entity_type` | text | Yes | Apply destination |
| `target_entity_id` | uuid | Yes | Destination |
| `target_version_at_generation` | bigint | Yes | Stale detection |
| `created_by` | uuid FK | No | Human requester |
| `created_at` | timestamptz | No | Created |
| `applied_at` | timestamptz | Yes | Applied |
| `discarded_at` | timestamptz | Yes | Discarded |

`generated_text` MUST NOT be overwritten by Human edits.

---

# 88. Virtual Operator

---

# 89. `vop_observations`

**Owner:** VOP  
**Class:** OPERATIONAL

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Observation |
| `observation_type` | text | No | Detector type |
| `severity` | text | No | Severity |
| `entity_type` | text | No | Affected entity |
| `entity_id` | uuid | No | Entity |
| `dedup_key` | text | No | Repeated-condition identity |
| `evidence` | jsonb | No | Deterministic evidence |
| `status` | text | No | open/resolved/dismissed/etc. |
| `first_detected_at` | timestamptz | No | First seen |
| `last_detected_at` | timestamptz | No | Last seen |
| `resolved_at` | timestamptz | Yes | Resolved |
| `version` | bigint | No | Concurrency |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

---

# 90. `vop_recommendations`

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Recommendation |
| `observation_id` | uuid FK | No | Observation |
| `recommendation_type` | text | No | Suggested action |
| `recommendation` | text | No | Description |
| `rationale` | text | No | Reason |
| `automation_level` | text | No | AUTO-1…AUTO-4 |
| `source_version_vector` | jsonb | No | Stale detection |
| `status` | text | No | pending/approved/rejected/etc. |
| `generated_by` | text | No | deterministic/ai |
| `created_at` | timestamptz | No | Created |
| `decided_at` | timestamptz | Yes | Human decision |
| `decided_by` | uuid FK | Yes | Admin |

---

# 91. `vop_actions`

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Action |
| `observation_id` | uuid FK | No | Observation |
| `recommendation_id` | uuid FK | Yes | Recommendation |
| `action_type` | text | No | Application command |
| `execution_mode` | text | No | human/auto4 |
| `status` | text | No | execution state |
| `target_entity_type` | text | No | Target |
| `target_entity_id` | uuid | No | Target |
| `correlation_id` | uuid | No | Traceability |
| `created_at` | timestamptz | No | Created |
| `completed_at` | timestamptz | Yes | Finished |

VOP action does not substitute Audit/domain state.

---

# 92. Themes

---

# 93. `site_themes`

**Owner:** Theme Domain  
**Class:** Stable configuration aggregate

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Theme |
| `profile_id` | uuid FK | No | Actor/site |
| `name` | text | No | Theme name |
| `lifecycle_state` | text | No | Lifecycle |
| `current_draft_id` | uuid | Yes | Draft |
| `version` | bigint | No | Concurrency |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

---

# 94. `theme_drafts`

**Owner:** Theme Domain  
**Class:** CONFIG

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Draft |
| `site_theme_id` | uuid FK | No | Parent |
| `tokens` | jsonb | No | Typed design tokens |
| `assets_config` | jsonb | No | Approved decorative config |
| `locked_fields` | jsonb | No | Human locks |
| `schema_version` | text | No | Token schema |
| `version` | bigint | No | Concurrency |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

No arbitrary executable JS.

---

# 95. `theme_ai_proposals`

**Owner:** Theme AI  
**Class:** AI OUTPUT

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Proposal |
| `site_theme_id` | uuid FK | No | Theme |
| `theme_draft_id` | uuid FK | No | Source Draft |
| `source_draft_version` | bigint | No | Conflict detection |
| `admin_prompt` | text | No | User visual instruction |
| `proposal` | jsonb | No | Structured tokens |
| `prompt_version` | text | No | AI prompt |
| `provider` | text | No | Provider |
| `model` | text | No | Model |
| `model_metadata` | jsonb | No | Metadata |
| `validation_state` | text | No | Schema/accessibility validation |
| `status` | text | No | generated/applied/rejected/stale |
| `created_at` | timestamptz | No | Created |

---

# 96. `theme_revisions`

**Owner:** Theme Domain  
**Class:** REVISION  
**Mutability:** I

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Revision |
| `site_theme_id` | uuid FK | No | Parent |
| `revision_number` | integer | No | Sequential |
| `tokens_snapshot` | jsonb | No | Frozen design |
| `assets_snapshot` | jsonb | No | Frozen asset config |
| `schema_version` | text | No | Schema |
| `created_by` | uuid FK | No | Human |
| `published_at` | timestamptz | No | Published |
| `created_at` | timestamptz | No | Created |

Constraint:

```text
UNIQUE(site_theme_id, revision_number)
```

---

# 97. `theme_activations`

**Owner:** Theme Domain  
**Class:** CONFIG/OPERATIONAL

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Activation |
| `profile_id` | uuid FK | No | Site |
| `theme_revision_id` | uuid FK | No | Exact Theme Revision |
| `activation_type` | text | No | permanent/temporary/scheduled |
| `starts_at` | timestamptz | No | Start |
| `ends_at` | timestamptz | Yes | End |
| `status` | text | No | scheduled/active/expired/cancelled |
| `approved_by` | uuid FK | No | Human |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

Check:

```text
ends_at > starts_at
```

when `ends_at` exists.

---

# 98. Social Publishing

---

# 99. `social_accounts`

**Owner:** Social Publishing Domain  
**Class:** CONFIG/INTEGRATION

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `id` | uuid | No | TECH | Connection |
| `profile_id` | uuid FK | No | TECH | Actor |
| `platform` | text | No | TECH | instagram/tiktok/etc. |
| `provider_account_id` | text | No | INT | Remote identity |
| `display_name` | text | Yes | INT | Account display |
| `credential_reference` | text | No | SEC | Secret-store reference only |
| `status` | text | No | INT | connected/reauth/etc. |
| `connected_at` | timestamptz | Yes | TECH | Connected |
| `updated_at` | timestamptz | No | TECH | Updated |

No access/refresh token plaintext.

---

# 100. `social_posts`

**Owner:** Social Publishing Domain  
**Class:** CONFIG/CONTENT DRAFT  
**Pattern:** COMMON-P

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `content` | text | No | Approved/draft caption/content |
| `media_configuration` | jsonb | No | Selected existing media refs/config |
| `status` | text | No | draft/approved/scheduled/etc. |
| `approved_version` | bigint | Yes | Exact approved version |
| `scheduled_at` | timestamptz | Yes | Desired schedule |
| `created_by` | uuid FK | No | Author |

Scheduled delivery MUST bind approved version.

---

# 101. `social_post_targets`

**Owner:** Social Publishing Domain  
**Class:** DELIVERY CONFIG

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Target |
| `social_post_id` | uuid FK | No | Post |
| `social_account_id` | uuid FK | No | Destination |
| `approved_post_version` | bigint | No | Frozen approved version |
| `status` | text | No | pending/published/failed |
| `scheduled_at` | timestamptz | Yes | Schedule |
| `provider_post_id` | text | Yes | Remote post |
| `published_at` | timestamptz | Yes | Published |
| `last_error_code` | text | Yes | Failure |

Constraint:

```text
UNIQUE(social_post_id, social_account_id, approved_post_version)
```

---

# 102. `social_publish_attempts`

**Owner:** Social Publishing  
**Class:** TECHNICAL HISTORY

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Attempt |
| `social_post_target_id` | uuid FK | No | Target |
| `attempt_number` | integer | No | Attempt |
| `started_at` | timestamptz | No | Start |
| `finished_at` | timestamptz | Yes | End |
| `result_state` | text | No | Result |
| `error_code` | text | Yes | Failure |
| `provider_request_id` | text | Yes | Trace |
| `safe_provider_metadata` | jsonb | Yes | Sanitized |

---

# 103. Help / Support

---

# 104. `support_tickets`

**Owner:** Support Domain  
**Class:** OPERATIONAL

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `id` | uuid | No | TECH | Ticket |
| `profile_id` | uuid FK | Yes | TECH | Context |
| `created_by_user_id` | uuid FK | No | INT | Author |
| `subject` | text | No | INT | Subject |
| `description` | text | No | PRI/INT | Request |
| `status` | text | No | INT | Workflow |
| `priority` | text | No | INT | Priority |
| `created_at` | timestamptz | No | TECH | Created |
| `updated_at` | timestamptz | No | TECH | Updated |
| `closed_at` | timestamptz | Yes | TECH | Closed |

---

# 105. `support_ticket_messages`

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Message |
| `ticket_id` | uuid FK | No | Ticket |
| `author_user_id` | uuid FK | No | Author |
| `message` | text | No | Message |
| `created_at` | timestamptz | No | Created |

---

# 106. `help_articles`

**Owner:** Support/Help  
**Class:** MANAGED CONTENT

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Article |
| `slug` | text | No | Stable route |
| `locale` | text | No | Locale |
| `title` | text | No | Title |
| `content` | text | No | Help content |
| `status` | text | No | draft/published/archived |
| `version` | bigint | No | Concurrency |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

Constraint:

```text
UNIQUE(slug, locale)
```

---

# 107. Identity / Admin

---

# 108. `admin_users`

**Owner:** Identity Domain  
**Class:** SECURITY/MASTER

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `id` | uuid | No | SEC | User |
| `email` | text | No | PRI/SEC | Login/contact identity |
| `display_name` | text | Yes | INT | Admin UI name |
| `status` | text | No | SEC | active/disabled/etc. |
| `locale` | text | No | INT | Admin locale |
| `created_at` | timestamptz | No | TECH | Created |
| `updated_at` | timestamptz | No | TECH | Updated |
| `last_login_at` | timestamptz | Yes | SEC/TECH | Last successful login |

Email uniqueness is case-insensitive.

Auth-provider/session/password tables depend on selected auth technology and are refined in security docs.

---

# 109. Audit

---

# 110. `audit_events`

**Owner:** Audit Domain  
**Class:** AUDIT  
**Mutability:** A

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `id` | uuid | No | AUD | Audit event |
| `occurred_at` | timestamptz | No | AUD | Time |
| `actor_type` | text | No | AUD | human/system/vop/migration |
| `actor_id` | uuid | Yes | AUD | Actor |
| `action` | text | No | AUD | Semantic action |
| `entity_type` | text | No | AUD | Target type |
| `entity_id` | uuid | No | AUD | Target |
| `profile_id` | uuid FK | Yes | AUD | Profile context |
| `correlation_id` | uuid | Yes | AUD | Trace |
| `request_id` | text | Yes | TECH | Request trace |
| `change_summary` | jsonb | No | AUD | Safe semantic change summary |
| `metadata` | jsonb | No | AUD/TECH | Additional safe context |

No secrets.

Ordinary update/delete prohibited.

---

# 111. Transactional Outbox

---

# 112. `outbox_events`

**Owner:** Infrastructure  
**Class:** DURABLE INTEGRATION EVENT

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Event |
| `event_type` | text | No | Semantic event |
| `aggregate_type` | text | No | Source type |
| `aggregate_id` | uuid | No | Source |
| `profile_id` | uuid FK | Yes | Context |
| `payload` | jsonb | No | Versioned event payload |
| `payload_version` | integer | No | Schema version |
| `correlation_id` | uuid | Yes | Trace |
| `created_at` | timestamptz | No | Commit time |
| `available_at` | timestamptz | No | Earliest dispatch |
| `processed_at` | timestamptz | Yes | Dispatcher complete |
| `attempt_count` | integer | No | Attempts |
| `last_error_code` | text | Yes | Error |

`payload` excludes secrets.

---

# 113. Background Jobs

---

# 114. `background_jobs`

**Owner:** Job Infrastructure  
**Class:** DURABLE TECHNICAL STATE

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Job |
| `job_type` | text | No | Registered handler |
| `semantic_class` | text | No | immutable/current/external/etc. |
| `payload` | jsonb | No | Versioned minimal references |
| `payload_version` | integer | No | Schema |
| `idempotency_key` | text | No | Semantic idempotency |
| `priority` | text | No | Priority |
| `status` | text | No | queue lifecycle |
| `scheduled_for` | timestamptz | No | Earliest execution |
| `attempt_count` | integer | No | Attempts |
| `max_attempts` | integer | No | Retry budget |
| `locked_by` | text | Yes | Worker |
| `locked_at` | timestamptz | Yes | Claim |
| `lease_until` | timestamptz | Yes | Recovery |
| `last_error_code` | text | Yes | Failure |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |
| `completed_at` | timestamptz | Yes | Terminal success |
| `cancelled_at` | timestamptz | Yes | Cancel |
| `correlation_id` | uuid | Yes | Trace |
| `source_event_id` | uuid FK | Yes | Outbox provenance |

No provider secrets.

---

# 115. `background_job_attempts`

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Attempt |
| `background_job_id` | uuid FK | No | Job |
| `attempt_number` | integer | No | Sequence |
| `worker_id` | text | No | Worker |
| `started_at` | timestamptz | No | Start |
| `finished_at` | timestamptz | Yes | End |
| `result_state` | text | No | success/failure/unknown |
| `error_code` | text | Yes | Stable error |
| `diagnostic_metadata` | jsonb | Yes | Sanitized diagnostics |

Constraint:

```text
UNIQUE(background_job_id, attempt_number)
```

---

# 116. Search

---

# 117. `search_documents`

**Owner:** Search Infrastructure  
**Class:** DERIVED / REBUILDABLE

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Search document |
| `search_scope` | text | No | public/builder/admin |
| `profile_id` | uuid FK | No | Actor |
| `entity_type` | text | No | Source type |
| `entity_id` | uuid | No | Source |
| `locale` | text | No | Locale |
| `title` | text | No | Safe title |
| `searchable_text` | text | No | Scope-safe text |
| `search_vector` | tsvector | Yes | PostgreSQL FTS |
| `priority_class` | text | No | Deterministic rank class |
| `public_route` | text | Yes | Public-safe target where relevant |
| `source_version` | bigint | Yes | Freshness |
| `indexed_at` | timestamptz | No | Indexed |
| `schema_version` | integer | No | Search document schema |

Expected uniqueness:

```text
(search_scope, entity_type, entity_id, locale)
```

No private fields in Public scope.

---

# 118. Analytics

---

# 119. `analytics_events`

**Owner:** Analytics  
**Class:** ANALYTICAL / APPEND-ORIENTED

| Field | Type | Null | Sens. | Meaning |
|---|---|---:|---|---|
| `id` | uuid | No | TECH | Event |
| `event_type` | text | No | TECH | Event |
| `profile_id` | uuid FK | Yes | TECH | Profile |
| `source_context_type` | text | Yes | TECH | Surface/entity |
| `source_context_id` | uuid | Yes | TECH | Context |
| `session_id` | text | Yes | PRI/TECH | Privacy-safe session identity |
| `attribution_source` | text | Yes | TECH | direct/search/social/campaign/shared/QR/etc. |
| `metadata` | jsonb | No | TECH | Privacy-minimized attributes |
| `occurred_at` | timestamptz | No | TECH | Time |
| `schema_version` | integer | No | TECH | Event schema |

Must not contain full private Feedback/Casting/Contact content.

---

# 120. Additional durable idempotency state

---

# 121. `idempotency_records`

**Owner:** Application Infrastructure  
**Class:** DURABLE TECHNICAL

Required for workflows where idempotency cannot be represented naturally on the target row.

| Field | Type | Null | Meaning |
|---|---|---:|---|
| `id` | uuid | No | Record |
| `scope` | text | No | feedback_submit/builder_generate/etc. |
| `idempotency_key_hash` | text | No | Safe key identity |
| `request_fingerprint` | text | Yes | Request semantic fingerprint |
| `result_entity_type` | text | Yes | Created resource |
| `result_entity_id` | uuid | Yes | Created resource |
| `status` | text | No | processing/completed/failed |
| `expires_at` | timestamptz | Yes | Technical retention |
| `created_at` | timestamptz | No | Created |
| `updated_at` | timestamptz | No | Updated |

Constraint:

```text
UNIQUE(scope, idempotency_key_hash)
```

Raw secret/idempotency token need not be stored.

---

# 122. Canonical state dictionaries

The following values are normative logical values. Physical implementation uses validated text or controlled enums.

---

# 123. Publication lifecycle

```text
draft
published
archived
```

---

# 124. Media type

```text
image
video
audio
document
```

---

# 125. Media processing

```text
stored
queued
processing
ready
partial
failed
```

---

# 126. Derivative processing

```text
queued
processing
ready
failed
stale
```

---

# 127. Questionnaire type

```text
compact
extended
casting
custom
```

---

# 128. Builder template

```text
quick
standard
extended
casting
```

---

# 129. Feedback type

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

# 130. Opportunity stage

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

# 131. Casting match state

```text
match
partial
mismatch
no_confirmed_data
not_applicable
```

---

# 132. AI Human decision state

```text
pending
confirmed
modified
rejected
```

---

# 133. Notification priority

```text
critical
high
normal
low
```

---

# 134. Notification delivery

```text
pending
scheduled
sending
sent
failed
retry_scheduled
cancelled
```

---

# 135. Access classes

```text
public
token_scoped
admin_only
temporary_session
```

---

# 136. Background job semantic classes

```text
immutable_source
current_state_derived
external_side_effect
scheduled_preauthorized
maintenance
```

---

# 137. Data visibility doctrine

Every field falls into one of three mechanisms.

## A. intrinsically internal

Examples:

```text
storage_locator
checksum
Audit
job locks
AI provider metadata
```

Never public.

## B. source data with explicit visibility policy

Examples:

```text
Portfolio
Skills
Languages
Projects
Training
Contacts
```

## C. immutable historical snapshot

Visibility determined by the Revision/Snapshot access class and privacy policy.

---

# 138. Public Builder doctrine

Builder receives only Source entities satisfying:

```text
belongs to same Profile
AND
active/published as required
AND
allow_in_public_questionnaire_builder = true
AND
template/context permits
```

A submitted `entity_id` never bypasses these rules.

---

# 139. Mandatory Builder data

Any generated actor questionnaire requires:

```text
Actor identification
Main Portfolio Primary Close-Up
Main Portfolio Primary Full Body
Official Profile URL
```

Database existence alone is insufficient.

Current eligibility is validated before Generation Snapshot.

---

# 140. Sensitive fields never Public DTO

At minimum:

```text
storage_locator
private attachment path
checksum
provider credentials
credential_reference
Audit internals
job payload
Outbox payload
AI provider request metadata
private Feedback notes
Casting source
Opportunity internal notes
Admin user security data
```

---

# 141. JSONB governance

Each long-lived JSONB contract MUST have a schema owner.

| JSONB | Owner |
|---|---|
| `technical_metadata` | Media Processor |
| `transform_spec` | Media Processor |
| Questionnaire config | Questionnaire Domain |
| Revision snapshot | Questionnaire Domain |
| Builder configuration | Builder Domain |
| Builder snapshot | Builder Domain |
| Casting AI structured output | Casting AI |
| Casting evidence | Casting Domain |
| Theme tokens | Theme Domain |
| Job payload | Job Infrastructure |
| Outbox payload | Event contract owner |
| Audit summary | Audit Domain |
| Analytics metadata | Analytics |
| QR settings | QR Domain |

---

# 142. JSONB rule

No consumer may depend on undocumented arbitrary keys.

Every persistent structure requires:

```text
schema/version
validator
migration/compatibility policy
```

---

# 143. Indexing priorities

## High-value operational indexes

```text
feedback(workflow_state, created_at DESC)
castings(status, deadline_at)
opportunities(stage, next_action_at)
notifications(read_at, created_at DESC)
notification_deliveries(status, scheduled_at)
background_jobs(status, scheduled_for, priority)
outbox_events(processed_at, available_at)
```

---

# 144. Professional public query indexes

```text
portfolio_items(profile_id, publication_state, display_order)
projects(profile_id, publication_state, display_order)
roles(project_id, publication_state, display_order)
training_items(profile_id, publication_state, display_order)
actor_skills(profile_id, display_order)
actor_languages(profile_id, display_order)
```

---

# 145. Revision indexes

```text
questionnaire_revisions(questionnaire_definition_id, revision_number DESC)
emotional_grid_revisions(emotional_grid_id, revision_number DESC)
casting_analysis_revisions(casting_id, revision_number DESC)
theme_revisions(site_theme_id, revision_number DESC)
```

---

# 146. Foreign-key deletion doctrine

Default:

```text
ON DELETE RESTRICT
```

for professional/master/historical data.

---

# 147. Appropriate CASCADE examples

Potential:

```text
temporary Builder Session → session item children
job → job attempt children after approved retention deletion
notification → delivery attempts only when parent itself validly deleted
```

Retention rules still apply.

---

# 148. CASCADE forbidden examples

```text
PortfolioItem → MediaAsset
RoleMedia → MediaAsset
QuestionnaireDefinition → QuestionnaireRevision
Feedback → Casting
Opportunity → Project
CastingAnalysisRevision → Casting source
```

---

# 149. Historical immutability matrix

| Entity | Mutable after finalization? |
|---|---:|
| QuestionnaireRevision | No |
| BuilderGenerationSnapshot | No |
| EmotionalGridRevision | No |
| ThemeRevision | No |
| AuditEvent | No |
| OpportunityStageHistory | No |
| Original AIDraft generated_text | No semantic overwrite |
| CastingAnalysisRevision | No |

---

# 150. Derived/rebuildable matrix

| Data | Rebuildable |
|---|---:|
| Media thumbnails | Yes |
| Search documents | Yes |
| Current cache | Yes |
| QR artifact | Yes from exact URL/settings |
| Profile match | Yes |
| Link health | Yes |
| Analytics aggregates | Yes |
| Questionnaire PDF | Semantically yes from exact Revision, but historical binary may be retained |
| Emotional Grid derivative | Yes from exact frozen Grid Revision and originals |

---

# 151. Required transaction guards

The following operations MUST use transaction + version/constraint protection:

```text
Set Primary Close-Up
Set Primary Full Body
Finalize Emotional Grid
Publish Questionnaire
Switch Primary Questionnaire
Create Builder Generation Snapshot
Create Casting from Feedback
Confirm Casting requirement
Transition Opportunity
Publish Theme
Activate Theme
Approve/schedule SocialPost
```

---

# 152. Database constraints vs Domain validation

## Database MUST enforce where practical

```text
PK
FK
NOT NULL
simple enum/check
unique Primary constraint
unique revision number
XOR artifact source
valid positive numeric values
unique dictionary assignment
```

## Domain MUST enforce

```text
same-profile contextual relations
Builder eligibility
exact Grid cell count
90% face target/readiness
all cells Human-confirmed
max 5 Role photos
max 2 Home role photos
questionnaire mandatory content
Contact/context permission
QR URL eligibility
Opportunity transition legality
AI source/version staleness
```

---

# 153. Data Dictionary security invariants

`DD-SEC-001`  
No plaintext provider credential in business tables.

`DD-SEC-002`  
No public Projection may expose `storage_locator`.

`DD-SEC-003`  
No Feedback/Casting private attachment may become public because its ID is known.

`DD-SEC-004`  
No Search Public row may contain more data than Public Projection.

`DD-SEC-005`  
No AI request record may contain system secrets.

`DD-SEC-006`  
No raw private token should be used as routine loggable cache/index identity.

`DD-SEC-007`  
Notification operational recipient is separate from professional Contact.

---

# 154. Data Dictionary source-of-truth invariants

`DD-INV-001`

```text
Profile fact is stored once.
```

`DD-INV-002`

Questionnaire stores Snapshot/history, not parallel mutable professional fact.

`DD-INV-003`

AI extraction never becomes source through direct write.

`DD-INV-004`

Search is not source.

`DD-INV-005`

Analytics is not source.

`DD-INV-006`

Notification is not Feedback.

`DD-INV-007`

Media relation does not duplicate binary.

`DD-INV-008`

Current pointers never redefine historical Revision.

---

# 155. Data ownership summary

| Data | Owner |
|---|---|
| Profile facts | Profile |
| Physical parameters | Profile |
| Media originals | Media |
| Portfolio meaning | Portfolio |
| Emotional shooting date | Emotional Portfolio |
| Grid crop config | Emotional Grid |
| Project/Role | Projects |
| Training | Training |
| Skills/Languages | Competency |
| Professional URL | Professional Links |
| Contact value | Contacts |
| Questionnaire config | Questionnaire |
| Published questionnaire snapshot | Questionnaire Revision |
| Public builder choices | Builder Session |
| Builder generated history | Builder Snapshot |
| Feedback | Feedback |
| Casting source | Casting |
| AI extraction | Casting AI |
| Confirmed Casting requirement | Casting |
| Opportunity stage | Opportunity |
| Notification | Notifications |
| BB generated text | BB/AIDraft |
| Applied biography | Profile |
| Theme Proposal | Theme AI |
| Published Theme | Theme Revision |
| Search Document | Search |
| Analytics Event | Analytics |
| Audit Event | Audit |

---

# 156. Data Dictionary quality gates

Before database implementation is accepted:

- [ ] every table has explicit owner;
- [ ] every source field has physical type;
- [ ] nullable semantics are defined;
- [ ] defaults cannot fabricate professional facts;
- [ ] every high-impact mutable entity has `version`;
- [ ] every Revision has immutable identity;
- [ ] all core FKs exist;
- [ ] delete actions are reviewed;
- [ ] all Primary uniqueness constraints exist;
- [ ] all item-level visibility scopes are independent;
- [ ] scalar Profile fields have policy mechanism;
- [ ] Builder sessions are durable;
- [ ] Builder generation snapshots are immutable;
- [ ] Feedback exists independently from notifications;
- [ ] Castings retain original source;
- [ ] AI extraction and Human decision are physically separable;
- [ ] Opportunity stage/history transaction is supported;
- [ ] operational notification recipients are separate from Contacts;
- [ ] Social credentials are references only;
- [ ] JSONB structures are schema-versioned;
- [ ] public data cannot expose internal storage paths;
- [ ] Search can be dropped and rebuilt;
- [ ] Audit is append-oriented;
- [ ] Outbox supports durable post-commit processing;
- [ ] Job payloads are versioned;
- [ ] job/provider secrets are absent;
- [ ] migration can represent “unknown” without fake defaults.

---

# 157. DD-E2E-001 — Scalar visibility

Set:

```text
height_cm = confirmed
show_on_public_site = false
allow_in_admin_questionnaires = true
allow_in_public_questionnaire_builder = false
```

Expected:

- absent public page;
- selectable Admin Questionnaire;
- absent Public Builder.

---

# 158. DD-E2E-002 — Builder-only Project

Set:

```text
show_on_public_site = false
allow_in_public_questionnaire_builder = true
```

Expected:

- no ordinary Public Project page/list exposure;
- can appear in Builder according to template rules.

---

# 159. DD-E2E-003 — Primary conflict

Concurrent second Primary Close-Up is rejected.

---

# 160. DD-E2E-004 — Immutable Media

Replacing source image creates new `media_assets.id`.

Existing historical Revision continues referencing old historical source/artifact.

---

# 161. DD-E2E-005 — Grid

4×4 draft with 15 cells cannot finalize despite valid database rows.

Domain validation blocks Revision creation.

---

# 162. DD-E2E-006 — Grid AI crop

`face_occupancy_estimate=94` does not set `confirmation_state=confirmed`.

Human confirmation remains mandatory.

---

# 163. DD-E2E-007 — Contact scopes

All eight combinations of three visibility booleans can be represented unless higher-level business rule explicitly blocks one.

No DB coercion.

---

# 164. DD-E2E-008 — Questionnaire history

Publish R1.

Modify current Skill.

R1 `content_snapshot` remains byte/semantic unchanged.

---

# 165. DD-E2E-009 — Builder mutation isolation

Change `public_builder_sessions.configuration`.

No Actor Profile, Portfolio, Contact, Skill or Project row changes.

---

# 166. DD-E2E-010 — Feedback before notification

Feedback transaction commits.

Notification provider is offline.

Feedback remains queryable.

---

# 167. DD-E2E-011 — Casting AI

AI stores:

```text
extracted_value = B1
```

Human changes requirement to B2.

Expected:

```text
extracted_value = B1
human_confirmed_value = B2
human_decision_state = modified
```

---

# 168. DD-E2E-012 — Missing Casting requirement

No height in source.

Stored as explicit extraction state `not_specified`.

No default numeric height.

---

# 169. DD-E2E-013 — No profile data

Casting requires English B2.

Actor Language unavailable/unconfirmed.

Match:

```text
no_confirmed_data
```

not mismatch.

---

# 170. DD-E2E-014 — Opportunity history

Transition:

```text
audition → callback
```

updates `opportunities.stage` and appends stage history atomically.

---

# 171. DD-E2E-015 — Booked provenance

`booked` Opportunity can generate Project Draft through separate orchestrated action.

Project retains `source_opportunity_id`.

Opportunity is not deleted/transformed.

---

# 172. DD-E2E-016 — Operational recipient

Change public parent/guardian Contact.

`notification_recipients` remains unchanged.

---

# 173. DD-E2E-017 — Theme AI

Theme proposal exists.

`theme_revisions` remains unchanged until Human Publish.

---

# 174. DD-E2E-018 — BB stale apply

AIDraft generated against target version 4.

Target becomes version 5.

Apply detects stale conflict using `target_version_at_generation`.

---

# 175. DD-E2E-019 — Search destruction

Drop/truncate `search_documents`.

No professional/master data is lost.

Full rebuild succeeds.

---

# 176. DD-E2E-020 — Job secret inspection

`background_jobs.payload`, `outbox_events.payload`, `notification_deliveries.payload_snapshot` contain no provider authentication secrets.

---

# 177. DD-E2E-021 — QR validation

`qr_artifacts.validation_state` cannot reach valid application state unless decoded canonical URL equals `target_url`.

---

# 178. DD-E2E-022 — Social partial failure

Instagram target succeeds.

TikTok target fails.

Two `social_post_targets.status` values differ independently.

---

# 179. DD-E2E-023 — Public Search safety

Inspect all:

```text
search_scope = public
```

rows.

No private Contact, Feedback, Casting, Admin, VOP or AI fields exist.

---

# 180. DD-E2E-024 — Migration unknown value

Legacy session lacks shooting date.

Migration records incomplete/migration exception.

It does not populate date with `now()`.

---

# 181. DD-E2E-025 — Audit separation

Changing Contact creates AuditEvent.

Audit row does not become Contact source and cannot be used as current public value.

---

# 182. Physical model overview

```text
ACTOR PROFILE
│
├── PARAMETERS ── FIELD POLICIES
├── LOCALIZATIONS
├── MEDIA
│   └── DERIVATIVES
├── PORTFOLIO
├── EMOTIONAL SESSIONS
│   └── GRIDS
│       ├── DRAFT/CELLS
│       └── IMMUTABLE REVISIONS ── ARTIFACTS
├── PROJECTS
│   └── ROLES ── ROLE MEDIA
├── TRAINING
├── ACHIEVEMENTS
├── SKILLS
├── LANGUAGES
├── LINKS ── LINK HEALTH
├── CONTACTS
├── QUESTIONNAIRES
│   ├── DRAFT/SECTIONS/ITEMS
│   └── REVISIONS ── ARTIFACTS
├── BUILDER
│   ├── SESSIONS
│   └── GENERATION SNAPSHOTS ── ARTIFACTS
├── FEEDBACK ── ATTACHMENTS/NOTES
├── CASTINGS
│   ├── SOURCES
│   ├── AI ANALYSIS REVISIONS
│   ├── REQUIREMENTS
│   └── MATCHES/RECOMMENDATIONS
├── OPPORTUNITIES ── STAGE HISTORY
├── AI DRAFTS
├── THEMES ── REVISIONS/ACTIVATIONS
└── SOCIAL
```

Infrastructure:

```text
AUDIT
OUTBOX
BACKGROUND JOBS
NOTIFICATIONS
SEARCH
ANALYTICS
IDEMPOTENCY
```

---

# 183. Important implementation rule

DOC-091 specifies the **logical physical contract**, not necessarily ORM naming syntax.

Implementation MAY:

- use Prisma/Drizzle/native SQL;
- choose UUID generation function;
- choose text+CHECK vs DB ENUM;
- choose generated search vector;
- add supporting indexes.

Implementation MUST NOT alter:

- ownership;
- meaning;
- visibility boundaries;
- history immutability;
- source/AI separation;
- source/derived distinction;
- referential semantics.

---

# 184. Required follow-up — DOC-092

The next document MUST map each entity/field class across:

```text
Admin
Public Site
Prepared Questionnaire
Public Builder
Casting/token scope
AI contexts
Search
Analytics
Logs
```

and answer:

> **Who can see which data, under which condition, and at which stage?**

---

# 185. Финальная доктрина

> **Data Dictionary является контрактом между бизнес-моделью и физической PostgreSQL-схемой. Каждый профессиональный факт имеет одного владельца и типизированное место хранения; каждая конфигурация отделена от факта; каждая Revision/Snapshot неизменяема; AI Output физически отделён от Human-confirmed data; private operational information не попадает в Public projections; Search, Analytics, Cache и derived artifacts остаются восстановимыми производными. Ни nullable, ни default, ни JSONB не могут использоваться для сокрытия неопределённости или создания вымышленных профессиональных фактов.**