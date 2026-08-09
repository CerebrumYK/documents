# TRAINING MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация Courses, Acting Education, Workshops, Training History и миграции legacy Project → Other

**Целевой файл:** `docs/modules/training.md`  
**Документ:** DOC-116  
**Статус:** ✅ Completed  
**Тип:** Module / Training / Education / Courses / Professional Development

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
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
- `docs/architecture/media-processing.md`
- `docs/architecture/pdf-generation.md`
- `docs/architecture/background-jobs.md`
- `docs/architecture/cache-strategy.md`
- `docs/architecture/search.md`
- `docs/architecture/ai-architecture.md`
- `docs/database/database-architecture.md`
- `docs/database/data-dictionary.md`
- `docs/database/visibility-and-access.md`
- `docs/database/validation-and-constraints.md`
- `docs/database/deletion-and-retention.md`
- `docs/api/server-actions.md`
- `docs/api/contracts.md`
- `docs/api/errors.md`
- `docs/api/idempotency.md`
- `docs/modules/profile.md`
- `docs/modules/media-library.md`
- `docs/modules/projects.md`

---

# 1. Назначение модуля

Training Module является единственным Master Source для профессионального обучения актрисы.

Модуль хранит:

- актёрское образование;
- курсы;
- мастер-классы;
- workshops;
- интенсивы;
- профессиональные программы;
- acting schools;
- voice/speech training;
- movement/stage training;
- camera acting training;
- иное подтверждённое профессиональное обучение.

---

# 2. Главная доктрина

> **Training описывает факт обучения. Skill описывает текущую подтверждённую компетенцию. Achievement описывает отдельное достижение или признание. Project описывает профессиональную работу. Эти сущности не взаимозаменяемы.**

Canonical:

```text
Training
   ↓
что и где изучала актриса

Skill
   ↓
что актриса умеет

Achievement
   ↓
какое достижение/признание получено

Project
   ↓
где актриса профессионально работала
```

---

# 3. Fundamental separation

```text
Training
≠ Project
≠ Skill
≠ Achievement
≠ Certificate File
≠ Biography
```

---

# 4. Module identifiers

Используются:

```text
TRN-*
TRN-TYPE-*
TRN-DATE-*
TRN-ORG-*
TRN-INST-*
TRN-MED-*
TRN-VIS-*
TRN-QNR-*
TRN-BLD-*
TRN-AI-*
TRN-MIG-*
TRN-INV-*
TRN-AP-*
E2E-TRN-*
```

---

# 5. Business purpose

Training Module должен позволять casting professional быстро понять:

1. какое профессиональное образование есть у актрисы;
2. какие профильные программы она проходила;
3. где проходило обучение;
4. в какой период;
5. кто преподавал, если это подтверждённый и профессионально значимый факт;
6. какие направления изучались;
7. какие документы/сертификаты подтверждают обучение, если они доступны.

---

# 6. Training entity

Canonical Source:

```text
TrainingItem
```

Representative table:

```text
training_items
```

---

# 7. Representative fields

Conceptually:

```text
id
profile_id
training_type
program_name
organization_name
location?
start_date?
end_date?
start_year?
end_year?
completion_date?
instructor_text?
description?
credential_name?
publication_state
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
display_order
version
created_at
updated_at
archived_at
migration_provenance?
```

Exact physical fields remain governed by DOC-091.

---

# 8. Training type

Controlled taxonomy.

Recommended baseline:

```text
FORMAL_EDUCATION
ACTING_SCHOOL
COURSE
WORKSHOP
MASTERCLASS
INTENSIVE
PRIVATE_TRAINING
OTHER
```

---

# 9. FORMAL_EDUCATION

Used for structured education such as:

- university;
- academy;
- conservatory;
- professional college;
- formal long-term acting education.

---

# 10. ACTING_SCHOOL

Professional acting school/program not necessarily formal degree education.

---

# 11. COURSE

Structured course with defined curriculum.

---

# 12. WORKSHOP

Shorter practical professional workshop.

---

# 13. MASTERCLASS

Professional masterclass with identified instructor/context.

---

# 14. INTENSIVE

Condensed intensive educational program.

---

# 15. PRIVATE_TRAINING

Individual professional coaching/training where relevant and confirmable.

---

# 16. OTHER

Only for real professional training not fitting current taxonomy.

---

# 17. TRN-TYPE-INV-001 — OTHER Is Not Escape Hatch

Если recurring category появляется регулярно, taxonomy должна быть расширена.

---

# 18. Training vs Project

Hard rule:

```text
course/masterclass/workshop/school
→ Training

film/series/theatre/commercial production
→ Project
```

---

# 19. TRN-INV-001 — Training Never Stored as Project OTHER

Это нормативное исправление существующей legacy-модели.

---

# 20. Training vs Skills

Пример:

```text
Training:
"Stage Combat Workshop, 2025"

Skill:
"Stage Combat — level 3/5"
```

Первое — история обучения.

Второе — текущая подтверждённая компетенция.

---

# 21. TRN-INV-002 — Completing Training Does Not Automatically Create Skill

---

# 22. Why

Прохождение курса само по себе не определяет:

- текущий уровень;
- устойчивость навыка;
- профессиональную readiness;
- skill level 1–5.

---

# 23. Skill recommendation

После Training система MAY предложить Admin:

```text
Добавить/обновить связанный навык?
```

но только как Human-confirmed action.

---

# 24. TRN-INV-003 — Training Cannot Auto-Increase Skill Level

---

# 25. Training vs Achievement

Пример:

```text
Training:
"Acting for Camera — completed 2026"

Achievement:
"Best Actress Award"
```

Это разные business entities.

---

# 26. Certificate nuance

Сертификат о прохождении курса может быть:

1. атрибутом/attachment Training;
2. отдельным Achievement, **только если продуктовая семантика Achievement это оправдывает**.

---

# 27. Baseline recommendation

Certificate of completion remains associated with Training.

Award/honour/competitive recognition belongs Achievement.

---

# 28. TRN-INV-004 — Certificate File Is Not Automatically Achievement

---

# 29. Training vs Biography

Biography может упоминать образование.

Но Biography — narrative projection/editorial Source.

Training remains structured professional Source.

---

# 30. TRN-INV-005 — Biography Is Not Training Database

---

# 31. Program name

`program_name` — подтверждённое название курса/программы.

---

# 32. Organization name

`organization_name` — подтверждённое название:

- школы;
- университета;
- студии;
- training provider;
- theatre school;
- education organization.

---

# 33. TRN-ORG-001 — Organization Is Human-Confirmed

AI не должен исправлять/расширять название организации по догадке.

---

# 34. Organization normalization

Система MAY нормализовать whitespace/casing for internal consistency.

Но не должна автоматически заменять название на другое юридическое/брендовое имя.

---

# 35. Location

Optional.

May contain:

```text
city
country
```

where known.

---

# 36. TRN-INV-006 — Training Location Does Not Change Profile Location

---

# 37. Instructor

Instructor data is optional.

---

# 38. Instructor semantics

Only include:

- confirmed name;
- relevant professional attribution.

---

# 39. TRN-INST-001 — No Instructor Invention

Если instructor unknown:

```text
NULL / Не указано
```

---

# 40. Multiple instructors

Baseline may use structured array/relation later.

If existing implementation needs simplicity:

```text
instructor_text
```

may temporarily hold a Human-authored list.

---

# 41. Preferred future normalized model

```text
training_instructors
```

if reuse/search/filter requirements justify it.

---

# 42. No external person profile requirement

Training Module does not need full CRM/contact entity for instructor.

---

# 43. Date precision doctrine

Training data may be known with different precision.

Examples:

```text
exact dates
month/year
year only
ongoing
```

---

# 44. TRN-DATE-001 — Preserve Known Precision

If only year is known:

```text
2024
```

do not fabricate:

```text
2024-01-01
```

---

# 45. Exact dates

Use `date`.

---

# 46. Year-only

Use explicit year representation.

---

# 47. Period

Can be:

```text
start_date/end_date
```

or:

```text
start_year/end_year
```

depending known precision.

---

# 48. TRN-DATE-002 — Do Not Mix Fake Precision

---

# 49. Ongoing training

May be explicitly represented:

```text
ongoing = true
```

or lifecycle/period semantics.

---

# 50. If ongoing

`end_date` should remain absent.

---

# 51. Completion date

May be distinct from end date if certificate issued later, but should only be modeled if actual product data requires distinction.

---

# 52. Duration

Should generally be derived from dates, not independently stored, unless source course duration such as:

```text
40 hours
```

is a professional fact.

---

# 53. Course hours

May be stored as structured optional professional value if confirmed.

---

# 54. TRN-DATE-003 — Upload Date Is Not Training Date

---

# 55. Description

Training description may include:

- subject areas;
- program focus;
- techniques studied;
- professional context.

---

# 56. Description must not turn into unverified Skill claims

Example:

Bad:

```text
"После курса профессионально владеет каскадёрскими трюками"
```

unless Skill Source confirms.

---

# 57. Good:

```text
"Программа включала основы сценического боя."
```

if confirmed by course context.

---

# 58. TRN-INV-007 — Curriculum ≠ Current Competence

---

# 59. Localization

Training supports localized professional representation.

At minimum:

```text
ru
en
```

where saved.

---

# 60. Localization fields

Potential:

```text
localized program display name
description
credential display label
```

---

# 61. Official program name

If program has an official English name, preserve it.

---

# 62. TRN-INV-008 — Machine Translation Is Not Official Program Name

---

# 63. BB Assistant

Can draft:

- concise Training description;
- English/Russian professional summary;
- Questionnaire compact description.

---

# 64. TRN-AI-001 — BB Draft Only

---

# 65. BB source context

May contain:

```text
program name
organization
dates
instructor
confirmed curriculum notes
```

---

# 66. BB cannot invent

```text
qualification
degree
certificate
instructor
hours
skills obtained
ranking
accreditation
```

---

# 67. Apply

Human-reviewed text flows through:

```text
UpdateTraining
```

or localization command.

---

# 68. TRN-AI-002 — BB Apply Cannot Modify Structured Facts Indirectly

Generated prose cannot silently parse back into organization/date/instructor fields.

---

# 69. Training lifecycle

Canonical:

```text
DRAFT
PUBLISHED
ARCHIVED
```

---

# 70. Draft

May be incomplete.

---

# 71. Published

Can appear on surfaces where contextual visibility allows.

---

# 72. Archived

Removed from current professional presentation/new document selections.

Historical snapshots remain.

---

# 73. TRN-INV-009 — Save != Publish

---

# 74. TRN-INV-010 — Archive != Delete

---

# 75. Visibility triplet

Canonical:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 76. TRN-VIS-001 — Three Independent Dimensions

---

# 77. Example A

```text
public=true
adminQ=true
builder=true
```

Normal public education record.

---

# 78. Example B

```text
public=false
adminQ=true
builder=false
```

Training may be included in a prepared professional questionnaire without showing in ordinary public Training page.

---

# 79. Example C

```text
public=false
adminQ=false
builder=true
```

Possible only if product intentionally allows this.

---

# 80. Effective visibility

Still requires:

```text
published/current
AND surface permission
AND projection allowlist
```

---

# 81. TRN-VIS-002 — Published Does Not Mean Public Everywhere

---

# 82. Public Training section

Should display structured professional education, not internal certificate repository.

---

# 83. Suggested public hierarchy

```text
Training / Education
├── Program
├── Organization
├── Period
├── Instructor if relevant
└── Concise description
```

---

# 84. Public document attachments

Certificates should not necessarily be downloadable publicly.

---

# 85. Default certificate policy

Private/Admin unless explicitly enabled.

---

# 86. TRN-MED-001 — Certificate Visibility Separate From Training Visibility

A public Training record does not automatically make certificate file public.

---

# 87. Media/attachments

Training may reference supporting MediaAssets/Documents.

---

# 88. Examples

```text
certificate PDF
certificate image
program confirmation
```

---

# 89. Relationship

Conceptually:

```text
TrainingItem
  ↓
TrainingAttachment
  ↓
MediaAsset / private document
```

if implemented.

---

# 90. Attachment type

Must be explicit.

Examples:

```text
CERTIFICATE
DIPLOMA
PROGRAM_DOCUMENT
OTHER
```

---

# 91. TRN-MED-002 — Attachment Does Not Become Public By Association

---

# 92. Document security

Certificate/document may contain:

- date of birth;
- registration number;
- signatures;
- personal identifiers.

---

# 93. Therefore

Public delivery requires separate visibility/sanitization decision.

---

# 94. Training photo

If educational photo is used as visual evidence, it still remains contextual attachment, not Main Portfolio photo.

---

# 95. TRN-MED-003 — Training Media Cannot Satisfy Portfolio Requirements

---

# 96. Questionnaire integration

Training is optional evidence.

---

# 97. Compact Questionnaire

Training should be concise or omitted depending template.

---

# 98. Standard

Can show compact list.

---

# 99. Extended

Can show richer Training history.

---

# 100. Casting

Can select only relevant Training.

---

# 101. TRN-QNR-001 — Questionnaire Training Is Explicitly Selectable

---

# 102. No full automatic dump

Especially if history becomes long.

---

# 103. Questionnaire content

Suggested:

```text
Program
Organization
Period/year
Instructor? 
Short description?
```

---

# 104. Certificate

Not included by default in actor Questionnaire PDF.

---

# 105. TRN-QNR-002 — Questionnaire Does Not Embed Private Certificate Automatically

---

# 106. Historical snapshot

Questionnaire Revision freezes exact selected Training values.

---

# 107. Current Training edit

Does not rewrite historical Questionnaire.

---

# 108. TRN-INV-011 — Historical Training Snapshot Immutable

---

# 109. Public Builder

Builder exposes only current eligible Training records.

---

# 110. Builder template behavior

### Quick
May omit Training.

### Standard
Compact selected Training.

### Extended
Broader selection.

### Casting
Role-relevant Training selection.

---

# 111. TRN-BLD-001 — Builder Selects, Does Not Edit Training

---

# 112. Visitor cannot change

```text
organization
program
dates
instructor
description
```

---

# 113. Builder temporary labels

Not required for Training.

No mutation of Source.

---

# 114. Generate validation

Server rechecks:

```text
same profile
published/current
builder allowed
not archived
```

---

# 115. Revocation mid-session

Removed/block generation.

---

# 116. Search

Public Search may index:

```text
program name
organization
public description
instructor name where public
training type
year
```

---

# 117. Public Search excludes

```text
certificate number
private attachment metadata
Draft
archived/private records
migration notes
Admin notes
```

---

# 118. Search inside profile

Could support:

```text
training
school
course
teacher
camera acting
```

where indexed fields permit.

---

# 119. TRN-INV-012 — Search Does Not Grant Access

---

# 120. SEO

Public Training section can contribute to actor profile structured content.

Standalone indexable pages are optional.

---

# 121. Hidden/private Training

Must not appear in:

```text
JSON-LD
OpenGraph
sitemap
public Search
```

---

# 122. Skills integration

Training may reference a set of related Skill definitions as non-authoritative association.

---

# 123. Example

```text
Training: Stage Combat Intensive
related skill: Stage Combat
```

---

# 124. Association semantics

Means:

> course topic relates to this Skill.

Not:

> actor automatically has skill at level N.

---

# 125. TRN-INV-013 — Training-Skill Link Does Not Set Skill Level

---

# 126. Skill Module remains authority

It owns:

```text
skill
level 1..5
description
visibility
```

---

# 127. Training completion workflow

May offer Admin CTA:

```text
Связать с существующим навыком
```

or:

```text
Добавить навык
```

---

# 128. Still separate confirmation.

---

# 129. Language training

Course like:

```text
English Acting Workshop
```

does not automatically set CEFR Language level.

---

# 130. TRN-INV-014 — Training Cannot Set CEFR

---

# 131. Achievement integration

Training may reference Achievement if:

- distinct award/recognition resulted;
- award exists separately.

---

# 132. Example

```text
Training:
Acting Academy Program

Achievement:
Graduation Award / distinction
```

if actually confirmed.

---

# 133. Certificate alone

Prefer Training attachment.

---

# 134. Projects integration

Project must not duplicate education record.

---

# 135. Casting AI integration

Casting requirement may state:

```text
профессиональное актёрское образование
```

---

# 136. Casting comparison

Can compare against confirmed Training records.

---

# 137. Example

Requirement:

```text
обучение Meisner technique
```

Training Source:

```text
Meisner Technique Workshop
```

May be relevant evidence.

---

# 138. TRN-AI-003 — Matching Must Cite Training Source

---

# 139. Missing Training evidence

Result:

```text
NO_CONFIRMED_DATA
```

not automatic mismatch if requirement interpretation is uncertain.

---

# 140. AI cannot convert curriculum inference into Skill Source.

---

# 141. Casting questionnaire recommendation

AI may recommend relevant Training records for inclusion.

Human confirms.

---

# 142. TRN-AI-004 — Recommendation Never Changes Training Visibility

---

# 143. BB Assistant integration

Can produce casting-specific concise wording based on selected Training.

But generated text stays Draft.

---

# 144. VOP

May detect:

```text
Training stored in legacy Project
missing organization
missing date/period
missing EN description
duplicate Training
private certificate accidentally public
questionnaire using archived Training
```

---

# 145. VOP safe automation

May:

```text
reindex Search
invalidate cache
recompute readiness
retry attachment derivative
```

---

# 146. VOP requires Human confirmation for

```text
migrate ambiguous Project to Training
archive Training
change dates
change organization
create Skill from Training
publish
change visibility
```

---

# 147. TRN-AI-005 — VOP Cannot Infer Qualification

---

# 148. Homepage integration

Training appears later in hierarchy than:

```text
Portfolio
Video
Emotional
Projects
Skills/Languages
```

because it is supporting evidence.

---

# 149. Recommended homepage treatment

Compact:

```text
selected/recent/high-value Training entries
→ CTA to full Training/Education section
```

---

# 150. Selection

Human-curated presentation.

No AI autonomous ranking.

---

# 151. TRN-INV-015 — Training Importance Is Human Presentation Configuration

---

# 152. Public Training page/section

Can be chronological or curated.

---

# 153. Recommended default ordering

Most professionally relevant/recent first through explicit order.

---

# 154. Display order

Use explicit `display_order`.

---

# 155. TRN-INV-016 — CreatedAt Is Not Professional Order

---

# 156. Date sorting fallback

Only if explicit order absent.

---

# 157. Admin UX

Recommended:

```text
Training
├── Published
├── Drafts
├── Archived
└── Add Training
```

---

# 158. Training card

Shows:

```text
program
organization
type
period
publication state
Site/QNR/Builder
attachments
```

---

# 159. Editor sections

1. Type;
2. Program;
3. Organization;
4. Period;
5. Location;
6. Instructor;
7. Description/localization;
8. Related Skills;
9. Attachments;
10. Visibility;
11. Questionnaire/Builder;
12. Readiness;
13. Public Preview;
14. Migration provenance where applicable.

---

# 160. Date UX

Must allow:

```text
exact dates
year-only
ongoing
```

without forcing fake precision.

---

# 161. Instructor UX

Optional field.

Should not force value.

---

# 162. Attachments UX

Clearly display:

```text
Private
Public if explicitly approved
```

rather than inheriting parent Training visibility silently.

---

# 163. Public Preview

Uses same Public Projection.

---

# 164. Questionnaire Preview

Shows compact representation, not private attachment.

---

# 165. Builder Preview

Shows only eligible Training.

---

# 166. Readiness

Training record can exist as incomplete Draft.

---

# 167. Public readiness

Recommended minimum:

```text
program_name
organization_name or justified provider context
training_type
valid period/year if known/required
published
public permitted
```

---

# 168. Organization requirement nuance

Some valid private coaching may not have organization.

Therefore:

```text
organization may be optional for PRIVATE_TRAINING
```

but not blindly optional for every type.

---

# 169. TRN-INV-017 — Validation Depends on Training Type

---

# 170. Example

FORMAL_EDUCATION:

```text
organization required
```

PRIVATE_TRAINING:

```text
organization optional
instructor may provide context
```

---

# 171. Readiness issue codes

Suggested:

```text
TRAINING_PROGRAM_REQUIRED
TRAINING_TYPE_REQUIRED
TRAINING_ORGANIZATION_REQUIRED
TRAINING_PERIOD_INVALID
TRAINING_INSTRUCTOR_UNCONFIRMED
TRAINING_PUBLIC_NOT_READY
TRAINING_ATTACHMENT_NOT_READY
TRAINING_LOCALIZATION_INCOMPLETE
TRAINING_MIGRATION_REVIEW_REQUIRED
```

---

# 172. Warnings

Examples:

```text
TRAINING_DATE_MISSING
TRAINING_EN_DESCRIPTION_MISSING
TRAINING_DUPLICATE_CANDIDATE
TRAINING_CERTIFICATE_PRIVATE
```

---

# 173. Missing date

May be warning rather than blocker if historic Source genuinely lacks exact year and business accepts it.

Do not invent.

---

# 174. Publication rules

Human publishes.

---

# 175. TRN-INV-018 — AI Cannot Publish Training

---

# 176. Concurrency

Training mutable Source uses `version`.

---

# 177. Updates

Require `expectedVersion` for significant edits.

---

# 178. Visibility concurrency

Stale Admin cannot silently overwrite another visibility change.

---

# 179. Reorder concurrency

Collection changes should be guarded.

---

# 180. Transactions

Source mutation:

```text
BEGIN
  update Training
  Audit
  Outbox
COMMIT
```

---

# 181. Attachments processing

Outside business transaction.

---

# 182. AI calls

Outside transaction.

---

# 183. Search/cache updates

Post-commit via Outbox/jobs.

---

# 184. Commands

Canonical:

```text
CreateTraining
UpdateTraining
UpdateTrainingLocalization
UpdateTrainingVisibility
PublishTraining
ArchiveTraining
RestoreTraining
ReorderTraining
AttachTrainingDocument
RemoveTrainingDocument
LinkTrainingSkill
UnlinkTrainingSkill
```

---

# 185. Migration-specific command

Potential:

```text
MigrateLegacyProjectToTraining
```

but production migration should preferably be scripted/versioned rather than routine UI command.

---

# 186. Queries

```text
GetPublicTraining
GetAdminTraining
GetTrainingItem
GetTrainingReadiness
ListQuestionnaireEligibleTraining
ListBuilderEligibleTraining
PreviewPublicTraining
GetTrainingMigrationProvenance
```

---

# 187. Create input

Conceptually:

```text
trainingType
programName
organizationName?
datePrecision/data
location?
instructor?
description?
```

---

# 188. Client cannot set

```text
publishedAt
publishedBy
migrationVerified
AIConfirmed
```

---

# 189. Visibility input

Exactly:

```text
showOnPublicSite
allowInAdminQuestionnaires
allowInPublicQuestionnaireBuilder
```

---

# 190. Attach document input

References already validated MediaAsset/private attachment identity.

---

# 191. Server validates

```text
same profile
allowed document type
safe
not quarantined
```

---

# 192. Skill link input

Only relation identity.

Does not accept/change Skill level.

---

# 193. Error taxonomy

At minimum:

```text
TRAINING_NOT_FOUND
TRAINING_TYPE_INVALID
TRAINING_PROGRAM_REQUIRED
TRAINING_ORGANIZATION_REQUIRED
TRAINING_DATE_INVALID
TRAINING_DATE_PRECISION_INVALID
TRAINING_ARCHIVED
TRAINING_NOT_READY
TRAINING_ATTACHMENT_INVALID
TRAINING_ATTACHMENT_WRONG_PROFILE
TRAINING_ATTACHMENT_NOT_READY
TRAINING_SKILL_WRONG_PROFILE
TRAINING_QUESTIONNAIRE_NOT_ELIGIBLE
TRAINING_BUILDER_NOT_ELIGIBLE
TRAINING_MIGRATION_REVIEW_REQUIRED
```

---

# 194. Search/cache events

Suggested Outbox:

```text
TrainingCreated
TrainingUpdated
TrainingPublished
TrainingArchived
TrainingVisibilityChanged
TrainingOrderChanged
TrainingAttachmentChanged
TrainingSkillLinkChanged
```

---

# 195. Consumers

```text
Public Projection
Search
Questionnaire readiness
Builder eligibility
Cache
VOP
Analytics
```

---

# 196. Audit

High-impact operations SHOULD record:

```text
publication
archive/restore
visibility
program/organization/date correction
legacy Project migration
certificate public exposure change
```

---

# 197. Routine description edit

Can also be audited according to global editorial policy.

---

# 198. Attachment private/public change

High privacy impact.

Must be auditable.

---

# 199. Search

Search projection is derived.

---

# 200. Cache

Public Training projection cache derived.

---

# 201. Visibility revocation

Strong current gate.

Then:

```text
cache invalidate
search remove
Builder eligibility update
Questionnaire readiness update
```

---

# 202. TRN-INV-019 — Cache Cannot Override Current Visibility

---

# 203. Analytics

Useful events:

```text
training_section_viewed
training_item_opened
```

---

# 204. Analytics should not collect private certificate identifiers.

---

# 205. Analytics cannot change display ordering autonomously.

---

# 206. Social Publishing

Training entry may be source context for a social draft.

Example:

```text
completed professional course
```

---

# 207. Human approval still required.

---

# 208. TRN-INV-020 — Social Post Does Not Modify Training Source

---

# 209. Certificate image in Social

Requires explicit selection/approval.

No automatic publication of private certificate.

---

# 210. Security

Training data can contain:

- educational history;
- names;
- certificate IDs;
- signatures;
- personal data.

---

# 211. Public Training facts and private evidence must remain separable.

---

# 212. TRN-SEC-001 — Public Record Does Not Grant Private Attachment Access

---

# 213. Private certificate delivery

Requires Admin/protected authorization.

---

# 214. Public certificate

Only through explicit approved safe derivative/resource.

---

# 215. Storage locator never exposed.

---

# 216. Archive

Archive Training:

- removes Public;
- removes from new QNR/Builder selection;
- removes Public Search;
- preserves historical Revisions.

---

# 217. Attachment remains according to dependencies/retention.

---

# 218. TRN-INV-021 — Archive Does Not Delete Certificate

---

# 219. Restore

Preferred:

```text
ARCHIVED → DRAFT
```

---

# 220. TRN-INV-022 — Restore Does Not Auto-Publish

---

# 221. Hard delete

Exceptional.

Dependency-aware.

---

# 222. Dependency graph

May include:

```text
Questionnaire Draft
Questionnaire Revision
Builder Snapshot
Biography source references
Casting analysis snapshots
AI drafts
Search
Cache
Attachments
```

---

# 223. Historical Questionnaire

Ordinary current archive/edit never rewrites historical Training.

---

# 224. Privacy removal

If Training fact or certificate must be erased:

copy discovery applies through DOC-094.

---

# 225. TRN-INV-023 — Private Certificate Cannot Survive Legal Redaction Through Derived Copy Accidentally

---

# 226. Migration from legacy Projects

Critical normative migration.

---

# 227. Migration goal

Legacy:

```text
Project(type=OTHER)
```

used for:

```text
course
training
masterclass
school
education
```

becomes:

```text
TrainingItem
```

---

# 228. TRN-MIG-001 — Classification Must Be Evidence-Based

A record migrates to Training when existing source data clearly indicates educational/training semantics.

---

# 229. Evidence examples

```text
"курс"
"обучение"
"мастер-класс"
"школа актёрского мастерства"
"workshop"
"acting course"
"intensive"
```

plus surrounding structured/manual context.

---

# 230. Ambiguous record

Do not silently migrate.

State:

```text
REQUIRES_REVIEW
```

---

# 231. TRN-MIG-002 — AI May Suggest Migration Classification, Not Decide Ambiguous Records

---

# 232. Migration field mapping

Example:

```text
Legacy Project.title
→ Training.program_name

Legacy Project.description
→ Training.description

Legacy Project.year/dates
→ Training period

Legacy organization/provider
→ Training.organization_name
```

only where semantics match.

---

# 233. Legacy Project ID

Store migration provenance:

```text
source_entity_type = PROJECT
source_entity_id
migration_id
migrated_at
```

or migration-log equivalent.

---

# 234. TRN-MIG-003 — Migration Provenance Must Survive

---

# 235. Project relation removal

After successful verified migration:

legacy Project can be archived/removed according to migration strategy.

---

# 236. No duplicate public entry

Once migrated and accepted:

the same course should not remain simultaneously Public as Project and Training.

---

# 237. TRN-MIG-004 — No Double Representation After Cutover

---

# 238. Historical Questionnaire complication

Old QuestionnaireRevision that legitimately froze legacy Project remains unchanged.

---

# 239. TRN-MIG-005 — Migration Does Not Rewrite Historical Revisions

---

# 240. Current Questionnaire Draft

Should resolve/migrate to Training configuration if appropriate.

---

# 241. Search cutover

Remove current legacy Project search document.

Add Training document.

---

# 242. URL migration

If legacy public Project route existed for a course:

support safe redirect to Training destination where product routing allows.

---

# 243. Do not break historical artifact semantics.

---

# 244. Role data on legacy Training Project

If legacy training-as-project accidentally has Role fields, do not fabricate Training meaning.

Flag for review.

---

# 245. Role photos on training record

Do not silently migrate to Main Portfolio/Training attachment.

Human review required.

---

# 246. Legacy certificate files

Map to Training attachments when provenance is clear.

---

# 247. Unknown file meaning

Retain private and flag.

---

# 248. Migration idempotency

Migration must be safely re-runnable.

---

# 249. Same legacy record

Must not produce duplicate TrainingItem.

---

# 250. Suggested uniqueness/provenance guard

```text
source_type + source_id + migration_kind
```

---

# 251. TRN-MIG-006 — Migration Is Idempotent

---

# 252. Rollback

Before destructive legacy cleanup:

migration must support verification/report.

---

# 253. Migration report

Should include:

```text
migrated
ambiguous
failed
duplicate candidate
attachment review required
```

---

# 254. No data loss

Normative.

---

# 255. Training duplicate detection

Potential duplicates:

```text
same program
same organization
same period
```

---

# 256. Detection only

Can suggest merge/review.

---

# 257. TRN-INV-024 — Similar Training Records Are Not Auto-Merged

---

# 258. Merge operation

If future supported, must preserve provenance/history.

Not baseline requirement.

---

# 259. Admin readiness for migration

VOP/dashboard may show:

```text
3 legacy Project→Other records require Training migration review
```

---

# 260. Customer journey

Training belongs Proof/Detail layer.

---

# 261. It should not displace:

```text
Quick Facts
Portfolio
Video
Questionnaire
Contact
```

from high-priority journey.

---

# 262. International Coordinator journey

Training may be more important for international reviewer.

Expected path:

```text
EN Profile
→ Languages
→ Skills
→ Training
→ Reel
→ Extended Questionnaire
```

---

# 263. Therefore

Localized Training quality matters.

---

# 264. Public Training compact design

Avoid oversized course cards.

Professional scanning speed matters.

---

# 265. Example compact representation

```text
Acting for Camera
XYZ Acting School · 2025
Instructor: ...
```

---

# 266. Description expandable if long.

---

# 267. Accessibility

Training lists use semantic structure.

Dates should not depend on icons only.

---

# 268. Attachments have meaningful labels.

---

# 269. Download certificate link states content/type safely.

---

# 270. Theme

May style Training cards/list.

Cannot:

- hide essential program/provider;
- expose private attachment;
- change fact wording automatically.

---

# 271. TRN-INV-025 — Theme Has No Data Authority

---

# 272. API DTO — Public

Conceptually:

```text
PublicTrainingDTO {
  id
  trainingType
  programName
  organizationName?
  period?
  location?
  instructor?
  description?
}
```

---

# 273. Public DTO excludes

```text
migration provenance
private certificate
storage metadata
admin notes
visibility configuration
AI metadata
```

---

# 274. Admin DTO

May include:

```text
id
version
type
facts
localizations
attachments
relatedSkills
visibility
publication
readiness
migrationProvenance
```

---

# 275. Builder DTO

Minimal:

```text
id
programName
organizationName?
period?
selected
```

only eligible entries.

---

# 276. Questionnaire DTO

Document-oriented approved projection.

Not raw Training DB row.

---

# 277. Anti-patterns

`TRN-AP-001`  
Continue storing courses as Project OTHER.

`TRN-AP-002`  
Training completion automatically creates Skill.

`TRN-AP-003`  
Training completion automatically sets Skill level 5.

`TRN-AP-004`  
Language course automatically sets CEFR.

`TRN-AP-005`  
Certificate automatically becomes Achievement.

`TRN-AP-006`  
Biography becomes only Training source.

`TRN-AP-007`  
AI invents instructor.

`TRN-AP-008`  
AI invents organization.

`TRN-AP-009`  
AI invents accreditation.

`TRN-AP-010`  
AI promotes curriculum topic to confirmed Skill.

`TRN-AP-011`  
Year-only course represented by fake January 1.

`TRN-AP-012`  
Upload date used as course date.

`TRN-AP-013`  
One `is_public` controls Site/QNR/Builder.

`TRN-AP-014`  
Public Training automatically exposes certificate file.

`TRN-AP-015`  
Private certificate URL sent to browser then hidden.

`TRN-AP-016`  
Certificate storage path shown publicly.

`TRN-AP-017`  
Training attachment becomes Main Portfolio.

`TRN-AP-018`  
Entire Training history automatically inserted into Compact PDF.

`TRN-AP-019`  
Builder edits Training name/provider.

`TRN-AP-020`  
Casting AI modifies Training to satisfy requirement.

`TRN-AP-021`  
BB generated description auto-publishes.

`TRN-AP-022`  
Runtime AI translation treated as stored official program name.

`TRN-AP-023`  
Search indexes private certificate number.

`TRN-AP-024`  
SEO includes hidden Training.

`TRN-AP-025`  
Analytics changes Training order automatically.

`TRN-AP-026`  
VOP migrates ambiguous Project without review.

`TRN-AP-027`  
Migration deletes ambiguous legacy record.

`TRN-AP-028`  
Migration rewrites old QuestionnaireRevision.

`TRN-AP-029`  
Migration leaves same course public in both Project and Training.

`TRN-AP-030`  
Migration invents missing dates/provider.

`TRN-AP-031`  
Similar titles automatically merged.

`TRN-AP-032`  
Archive Training deletes certificate immediately.

`TRN-AP-033`  
Restore auto-publishes.

`TRN-AP-034`  
Hard delete cascades through historical Questionnaire.

`TRN-AP-035`  
Training location changes Profile city.

`TRN-AP-036`  
Training instructor becomes ordinary Contact automatically.

`TRN-AP-037`  
Course hours inferred from website without source approval.

`TRN-AP-038`  
Public Builder receives raw attachment inventory.

`TRN-AP-039`  
Theme hides organization/provider to improve design.

`TRN-AP-040`  
Training is used as catch-all for achievements/projects.

---

# 278. Core invariants

`TRN-INV-026`  
Every TrainingItem belongs to one Actor Profile.

`TRN-INV-027`  
Training is independent from Project.

`TRN-INV-028`  
Training is independent from Skill.

`TRN-INV-029`  
Training is independent from Achievement.

`TRN-INV-030`  
Program name is Human-confirmed Source.

`TRN-INV-031`  
Organization is Human-confirmed Source.

`TRN-INV-032`  
Instructor is optional and Human-confirmed.

`TRN-INV-033`  
Missing facts remain missing.

`TRN-INV-034`  
Date precision is preserved.

`TRN-INV-035`  
Upload date never becomes Training date.

`TRN-INV-036`  
Curriculum does not automatically become current Skill.

`TRN-INV-037`  
Training cannot change CEFR automatically.

`TRN-INV-038`  
Certificate file does not automatically become Achievement.

`TRN-INV-039`  
Certificate visibility is independent from parent Training visibility.

`TRN-INV-040`  
Private certificate never leaks through Public Projection.

`TRN-INV-041`  
Training attachment remains contextual.

`TRN-INV-042`  
Training media cannot satisfy Main Portfolio requirements.

`TRN-INV-043`  
Save does not publish.

`TRN-INV-044`  
Archive does not delete.

`TRN-INV-045`  
Restore does not auto-publish.

`TRN-INV-046`  
Public/QNR/Builder permissions are independent.

`TRN-INV-047`  
Questionnaire Training inclusion is explicit.

`TRN-INV-048`  
Builder selects rather than edits Training.

`TRN-INV-049`  
Historical Questionnaire freezes Training snapshot.

`TRN-INV-050`  
Current Training edit does not rewrite history.

`TRN-INV-051`  
Search only indexes permitted current data.

`TRN-INV-052`  
SEO cannot exceed Public visibility.

`TRN-INV-053`  
Casting AI matching relies on confirmed Training Source.

`TRN-INV-054`  
Casting AI cannot mutate Training.

`TRN-INV-055`  
BB drafts do not become Source without Human Apply.

`TRN-INV-056`  
VOP cannot fabricate qualifications.

`TRN-INV-057`  
Core Training workflow works without AI.

`TRN-INV-058`  
Explicit display order governs presentation.

`TRN-INV-059`  
CreatedAt is not professional ranking.

`TRN-INV-060`  
Theme has no authority over facts/visibility.

`TRN-INV-061`  
Visibility revocation invalidates derived public surfaces.

`TRN-INV-062`  
Cache cannot keep revoked Training public.

`TRN-INV-063`  
Private evidence stays private unless explicitly approved.

`TRN-INV-064`  
Hard deletion is dependency-aware.

`TRN-INV-065`  
Privacy removal discovers attachment/snapshot/AI copies.

`TRN-INV-066`  
Legacy Training stored as Project must migrate.

`TRN-INV-067`  
Migration classification uses evidence.

`TRN-INV-068`  
Ambiguous migration requires Human review.

`TRN-INV-069`  
Migration does not invent facts.

`TRN-INV-070`  
Migration preserves provenance.

`TRN-INV-071`  
Migration is idempotent.

`TRN-INV-072`  
Migration does not rewrite historical Revisions.

`TRN-INV-073`  
Migration does not silently discard legacy attachments.

`TRN-INV-074`  
Migration cutover avoids duplicate current public representation.

`TRN-INV-075`  
Duplicate suggestions never auto-merge Training.

`TRN-INV-076`  
Skill relationship is contextual, not competence authority.

`TRN-INV-077`  
Training location is not Profile location.

`TRN-INV-078`  
Ongoing Training does not require fake end date.

`TRN-INV-079`  
Public DTO omits migration/private/internal fields.

`TRN-INV-080`  
All authoritative mutations are server-validated and version-aware.

---

# 279. E2E-TRN-001 — Create Course

Create valid Course Draft.

Expected non-public TrainingItem.

---

# 280. E2E-TRN-002 — Formal education organization

FORMAL_EDUCATION without required organization.

Readiness/validation blocked.

---

# 281. E2E-TRN-003 — Private coaching

PRIVATE_TRAINING with instructor but no organization can be valid according to type-specific policy.

---

# 282. E2E-TRN-004 — Year-only

Store:

```text
2024
```

No fake exact date generated.

---

# 283. E2E-TRN-005 — Ongoing

Start period present.

End absent.

`ongoing=true`.

No fake end date.

---

# 284. E2E-TRN-006 — Upload timestamp

File uploaded in 2026 for 2022 course.

Training period remains 2022.

---

# 285. E2E-TRN-007 — Instructor missing

Optional instructor remains absent.

No AI value generated.

---

# 286. E2E-TRN-008 — Public Training

Published + public allowed item appears in Public Projection.

---

# 287. E2E-TRN-009 — Questionnaire-only

```text
public=false
adminQ=true
```

Hidden Site, selectable prepared Questionnaire.

---

# 288. E2E-TRN-010 — Builder-only

Current eligible item appears only in Builder context.

---

# 289. E2E-TRN-011 — Hidden serialization

Private Training absent from Public HTML/RSC/Search/SEO.

---

# 290. E2E-TRN-012 — Certificate private

Training public.

Certificate private.

Anonymous user cannot download certificate.

---

# 291. E2E-TRN-013 — Certificate explicit public

If explicitly approved and policy permits, safe public derivative/resource becomes accessible.

---

# 292. E2E-TRN-014 — Skill link

Link Training to Stage Combat Skill.

Skill level remains unchanged.

---

# 293. E2E-TRN-015 — New Training does not create Skill

Complete workshop.

No Skill appears automatically.

---

# 294. E2E-TRN-016 — Language course

English course does not change CEFR level.

---

# 295. E2E-TRN-017 — Questionnaire compact

Only explicitly selected compact Training appears.

---

# 296. E2E-TRN-018 — Historical Questionnaire

Training organization changed later.

Old Revision retains previous frozen value.

---

# 297. E2E-TRN-019 — Builder tampering

Visitor submits modified organization name.

Server ignores/rejects value and loads Source.

---

# 298. E2E-TRN-020 — Builder revoke

Permission removed before Generate.

Training removed/blocking selection.

---

# 299. E2E-TRN-021 — Casting AI

Requirement asks for camera acting training.

Matching cites existing confirmed Training item.

---

# 300. E2E-TRN-022 — Missing training evidence

No relevant Training record.

AI does not invent one.

---

# 301. E2E-TRN-023 — BB draft

Generate professional course description.

Public remains unchanged.

---

# 302. E2E-TRN-024 — BB invented accreditation

Unsupported accreditation does not become Source automatically.

---

# 303. E2E-TRN-025 — AI disabled

Manual Training CRUD/Questionnaire/Builder remain fully functional.

---

# 304. E2E-TRN-026 — Search

Public Training searchable by approved program/provider.

---

# 305. E2E-TRN-027 — Private Search

Draft/private certificate data absent.

---

# 306. E2E-TRN-028 — Visibility revocation

Cached public Training becomes private.

Next request does not expose stale item.

---

# 307. E2E-TRN-029 — Archive

Training disappears from current Public/QNR/Builder.

---

# 308. E2E-TRN-030 — Historical archive

Historical Questionnaire remains unchanged.

---

# 309. E2E-TRN-031 — Archive certificate

Underlying attachment not destructively deleted solely due Training archive.

---

# 310. E2E-TRN-032 — Restore

Returns Draft/non-public state.

---

# 311. E2E-TRN-033 — Concurrent edits

v4 changed by Admin A.

Admin B submits v4 after A committed v5.

Expected `STALE_VERSION`.

---

# 312. E2E-TRN-034 — Legacy Project migration

Legacy:

```text
Project OTHER = "Acting for Camera Workshop"
```

migrates to Training with provenance.

---

# 313. E2E-TRN-035 — Migration retry

Running migration again does not create duplicate TrainingItem.

---

# 314. E2E-TRN-036 — Ambiguous Project

Legacy `Project OTHER = "Studio 2024"` with insufficient semantics.

Marked review-required, not silently migrated.

---

# 315. E2E-TRN-037 — Migration no fabrication

Legacy record lacks organization/date.

Migrated/review state keeps values absent.

---

# 316. E2E-TRN-038 — Historical Revision migration safety

Old Questionnaire referencing legacy Project remains immutable.

---

# 317. E2E-TRN-039 — Current duplicate representation

After accepted migration, course does not appear simultaneously as current Project and current Training.

---

# 318. E2E-TRN-040 — Legacy attachment

Known certificate maps to Training attachment.

---

# 319. E2E-TRN-041 — Unknown legacy attachment

Remains private/review-required.

---

# 320. E2E-TRN-042 — Duplicate candidate

Two similar courses detected.

No automatic merge.

---

# 321. E2E-TRN-043 — Profile city isolation

Training in London does not change Profile city.

---

# 322. E2E-TRN-044 — Homepage selection

Selected Training appears as compact supporting evidence without displacing primary casting actions.

---

# 323. E2E-TRN-045 — SEO

Private course/certificate absent from structured metadata.

---

# 324. E2E-TRN-046 — Attachment cross-profile

Profile A cannot attach document owned by Profile B.

---

# 325. E2E-TRN-047 — Quarantined certificate

Cannot be downloaded/used through Training.

---

# 326. E2E-TRN-048 — Theme

Theme can restyle Training list but cannot hide organization or expose private evidence.

---

# 327. Domain diagram

```text
Actor Profile
     │
     ▼
 TrainingItem
  ┌──┼──────────────┐
  ▼  ▼              ▼
Facts Localizations Attachments
  │
  └─────────┐
            ▼
    Related Skills
    (non-authoritative link)
```

---

# 328. Separation diagram

```text
          Training
         /    |    \
        /     |     \
  proves   relates   may produce
 learning    to      document
     ↓       ↓          ↓
 Training   Skill    Certificate

Training does NOT automatically create
Skill level or Achievement.
```

---

# 329. Questionnaire flow

```text
Current Training
      ↓
Surface Eligibility
      ↓
Explicit Selection
      ↓
Questionnaire Draft
      ↓
Publish
      ↓
Immutable QuestionnaireRevision
```

---

# 330. Casting flow

```text
Casting Requirement
       +
Confirmed Training Source
       ↓
Evidence-based comparison
       ↓
Recommendation
       ↓
Human selects relevant Training
```

---

# 331. Legacy migration flow

```text
Legacy Project OTHER
       ↓
Classification
   ┌───┴──────────┐
   ▼              ▼
Clearly Training  Ambiguous
   │              │
   ▼              ▼
Map fields      Requires Review
   │
   ▼
Create Training
   │
   ▼
Preserve provenance
   │
   ▼
Validate
   │
   ▼
Cut over current projections
```

---

# 332. Visibility diagram

```text
TrainingItem
   │
   ├── Public Site
   ├── Admin Questionnaire
   └── Public Builder

Attachment visibility:
separate decision
```

---

# 333. Quality gate

Перед implementation должны быть подтверждены:

- [ ] standalone Training domain;
- [ ] controlled Training types;
- [ ] Project boundary;
- [ ] Skill boundary;
- [ ] Achievement boundary;
- [ ] program/provider/instructor semantics;
- [ ] date precision model;
- [ ] ongoing semantics;
- [ ] localization;
- [ ] lifecycle;
- [ ] three-way visibility;
- [ ] explicit display order;
- [ ] Training attachment model;
- [ ] certificate privacy;
- [ ] related Skill links without level mutation;
- [ ] Questionnaire selection;
- [ ] Public Builder eligibility;
- [ ] Casting AI evidence rules;
- [ ] BB draft-only rules;
- [ ] Search/SEO;
- [ ] archive/restore;
- [ ] dependency-aware deletion;
- [ ] legacy Project→Training classifier;
- [ ] ambiguous migration review;
- [ ] migration provenance;
- [ ] migration idempotency;
- [ ] historical Revision preservation;
- [ ] public cutover/no duplication;
- [ ] VOP checks;
- [ ] concurrency;
- [ ] E2E coverage.

---

# 334. Acceptance criteria

`AC-TRN-001`  
Training существует как отдельный Master Data module.

`AC-TRN-002`  
Courses/Masterclasses/Workshops больше не моделируются как Project OTHER.

`AC-TRN-003`  
Training Type является controlled value.

`AC-TRN-004`  
Program name хранится как confirmed Source.

`AC-TRN-005`  
Organization хранится отдельно от Program.

`AC-TRN-006`  
Instructor является optional confirmed fact.

`AC-TRN-007`  
Unknown Training facts не выдумываются.

`AC-TRN-008`  
Date precision сохраняется без fake precision.

`AC-TRN-009`  
Ongoing Training не получает искусственный end date.

`AC-TRN-010`  
Training completion не создаёт Skill автоматически.

`AC-TRN-011`  
Training completion не изменяет Skill level автоматически.

`AC-TRN-012`  
Language Training не изменяет CEFR автоматически.

`AC-TRN-013`  
Certificate не становится Achievement автоматически.

`AC-TRN-014`  
Certificate/public evidence visibility управляется отдельно.

`AC-TRN-015`  
Private Training attachment не попадает в Public payload.

`AC-TRN-016`  
Training media не заменяет Main Portfolio photos.

`AC-TRN-017`  
Public/Admin Questionnaire/Public Builder permissions независимы.

`AC-TRN-018`  
Questionnaire использует explicit selected Training.

`AC-TRN-019`  
Historical Questionnaire freezes exact Training representation.

`AC-TRN-020`  
Public Builder не может редактировать Training facts.

`AC-TRN-021`  
Builder Generate revalidates eligibility.

`AC-TRN-022`  
Casting AI использует только confirmed Training evidence.

`AC-TRN-023`  
Casting AI не изменяет Training Source.

`AC-TRN-024`  
BB output remains Draft until Human Apply.

`AC-TRN-025`  
Search/SEO respect current Public visibility.

`AC-TRN-026`  
Archive removes current exposure without destroying history.

`AC-TRN-027`  
Restore does not auto-publish.

`AC-TRN-028`  
Legacy Project→Training migration preserves provenance.

`AC-TRN-029`  
Ambiguous migration requires Human review.

`AC-TRN-030`  
Migration does not invent program/provider/date facts.

`AC-TRN-031`  
Migration is idempotent.

`AC-TRN-032`  
Migration does not rewrite historical QuestionnaireRevision.

`AC-TRN-033`  
Migrated course is not duplicated as current Project and Training.

`AC-TRN-034`  
Legacy attachments are preserved/reviewed without silent loss.

`AC-TRN-035`  
Core Training workflow operates without AI.

`AC-TRN-036`  
All privacy, migration, concurrency and historical rules have deterministic tests.

---

# 335. Финальная доктрина

> **Training Module является единственным Master Source истории профессионального обучения актрисы. Он хранит подтверждённые программы, школы, курсы, мастер-классы, instructors, периоды и supporting evidence, сохраняя исходную точность данных и не превращая обучение автоматически в Skills, CEFR levels или Achievements. Public Site, prepared Questionnaire и Public Builder используют независимые eligibility permissions, а сертификаты и другие доказательные документы имеют отдельную privacy policy. Legacy образовательные записи из `Project → Other` должны быть безопасно, idempotent и с сохранением provenance перенесены в Training без переписывания исторических Questionnaire Revisions и без изобретения отсутствующих фактов. AI/BB/VOP могут помогать с текстом, сопоставлением и миграционными рекомендациями, но профессиональная образовательная история остаётся исключительно Human-confirmed Source.**