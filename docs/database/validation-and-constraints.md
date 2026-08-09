# VALIDATION RULES

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная система field-, cross-field-, cross-entity-, readiness- и transaction-time validation

**Целевой файл:** `docs/database/validation-and-constraints.md`  
**Документ:** DOC-093  
**Статус:** ✅ Completed  
**Тип:** Database / Domain Validation / Data Integrity / Readiness / Transactions

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
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`
- `docs/architecture/media.md`
- `docs/architecture/pdf.md`
- `docs/architecture/background-jobs.md`
- `docs/architecture/cache.md`
- `docs/architecture/search.md`
- `docs/architecture/ai.md`
- `docs/architecture/notifications.md`
- `docs/database/database-architecture.md`
- `docs/database/data-dictionary.md`
- `docs/database/visibility-and-access.md`

---

# 1. Назначение

Настоящий документ определяет обязательные правила проверки данных до их принятия системой.

Validation отвечает на вопрос:

> **Может ли данное изменение быть принято как корректное состояние продукта именно сейчас?**

Он фиксирует:

1. уровни validation;
2. порядок validation;
3. field validation;
4. cross-field validation;
5. cross-entity validation;
6. same-profile invariants;
7. lifecycle validation;
8. publication validation;
9. visibility validation;
10. Questionnaire readiness;
11. Builder validation;
12. Emotional Grid validation;
13. Media validation;
14. Contacts;
15. URLs/QR;
16. Castings;
17. Opportunity transitions;
18. AI/Human boundaries;
19. Notification validation;
20. Theme validation;
21. Social Publishing;
22. concurrency;
23. transaction-time validation;
24. async revalidation;
25. migration exceptions;
26. error contracts;
27. testing.

---

# 2. Главная доктрина

> **Ни UI, ни AI, ни Search, ни Background Worker не имеют права считать данные корректными только потому, что они были корректны раньше или прошли client-side validation. Авторитетная validation выполняется сервером непосредственно перед принимающей Domain operation и повторяется в транзакции там, где состояние могло измениться конкурентно.**

Canonical:

```text
INPUT
  ↓
STRUCTURAL VALIDATION
  ↓
AUTHORIZATION
  ↓
DOMAIN VALIDATION
  ↓
CROSS-ENTITY VALIDATION
  ↓
CONCURRENCY VALIDATION
  ↓
TRANSACTION-TIME REVALIDATION
  ↓
COMMIT
```

---

# 3. Validation identifiers

Используются:

```text
VAL-*
VAL-FLD-*
VAL-XFLD-*
VAL-XENT-*
VAL-MED-*
VAL-PRT-*
VAL-EMO-*
VAL-QNR-*
VAL-BLD-*
VAL-LNK-*
VAL-CST-*
VAL-OPP-*
VAL-AI-*
VAL-THM-*
VAL-NOT-*
VAL-SOC-*
VAL-TXN-*
VAL-INV-*
VAL-AP-*
```

---

# 4. Validation levels

Canonical levels:

```text
L0 — Transport / Structural
L1 — Field
L2 — Cross-Field
L3 — Entity
L4 — Cross-Entity
L5 — Authorization / Visibility Context
L6 — Readiness
L7 — Concurrency
L8 — Transaction-Time
L9 — Async Preconditions
```

---

# 5. L0 — Transport / Structural

Проверяет:

```text
request shape
required payload members
supported content type
size limits
JSON/schema validity
identifier syntax
```

Не проверяет professional meaning.

---

# 6. L1 — Field validation

Проверяет отдельное значение:

```text
type
range
format
length
allowed enumeration
normalized representation
```

---

# 7. L2 — Cross-field

Проверяет взаимосвязанные поля одной entity.

Пример:

```text
end_date >= start_date
```

---

# 8. L3 — Entity

Проверяет entity как бизнес-объект.

Пример:

```text
PortfolioItem category=close_up
AND MediaAsset.type=image
```

---

# 9. L4 — Cross-entity

Проверяет relationships.

Пример:

```text
Role.profile_id = Project.profile_id
```

---

# 10. L5 — Authorization / Visibility

Проверяет:

```text
can this actor perform this command?
can this Source be used in this context?
```

---

# 11. L6 — Readiness

Проверяет полноту перед значимым transition:

```text
Publish
Finalize
Generate
Activate
Send
```

---

# 12. L7 — Concurrency

Проверяет:

```text
expected_version = current_version
```

и current pointer/version assumptions.

---

# 13. L8 — Transaction-Time Revalidation

Повторяет critical predicates после получения необходимых locks/current rows.

---

# 14. L9 — Async Preconditions

Background worker перед side effect проверяет, что execution intent всё ещё действителен.

---

# 15. VAL-INV-001 — Client Validation Is UX Only

Client validation улучшает UX, но не является authority.

Server MUST повторить всё существенное.

---

# 16. VAL-INV-002 — Database Constraint Is Final Guard, Not Only Guard

Пользователь должен получать semantic validation error, а не только raw unique/FK exception.

---

# 17. VAL-INV-003 — Unknown ≠ Invalid

Отсутствие необязательного профессионального факта должно моделироваться как unknown/not specified, а не подменяться фиктивным значением.

---

# 18. VAL-INV-004 — Historical Data Is Not Revalidated Against Current Source

Historical Revision не становится invalid только потому, что current Master изменился.

---

# 19. VAL-INV-005 — Publish/Generate Requires Fresh Validation

Успешный Preview пятиминутной давности не является разрешением Publish/Generate сейчас.

---

# 20. VAL-INV-006 — AI Output Never Bypasses Validation

AI recommendation проходит те же Domain rules, что и ручной input.

---

# 21. VAL-INV-007 — Worker Never Bypasses Validation

Background execution использует application/domain contract или exact immutable input.

---

# 22. VAL-INV-008 — Validation Cannot Mutate Source Silently

Проверка не должна «исправлять» профессиональные факты без отдельной команды.

---

# 23. Validation result model

Recommended:

```text
VALID
INVALID
BLOCKED
STALE
WARNING
```

---

# 24. `INVALID`

Входные данные противоречат правилам.

---

# 25. `BLOCKED`

Данные могут быть корректными сами по себе, но operation сейчас запрещена.

Пример:

```text
Questionnaire cannot publish because mandatory Full Body is unavailable.
```

---

# 26. `STALE`

Используемая версия/source assumption устарела.

---

# 27. `WARNING`

Не блокирует operation, но требует внимания.

Warnings MUST NOT заменять blocking errors.

---

# 28. Validation error structure

Conceptually:

```text
code
field?
entity_type?
entity_id?
severity
message_key
parameters
remediation?
```

---

# 29. Stable codes

UI/tests/API SHOULD rely on error code, not parse localized human text.

---

# 30. Generic validation codes

```text
VALIDATION_REQUIRED
VALIDATION_FORMAT
VALIDATION_RANGE
VALIDATION_CONFLICT
VALIDATION_NOT_FOUND
VALIDATION_WRONG_PROFILE
VALIDATION_NOT_ELIGIBLE
VALIDATION_NOT_READY
VALIDATION_STALE_VERSION
VALIDATION_STATE_TRANSITION
VALIDATION_ACCESS_DENIED
```

---

# 31. Field validation — identifiers

UUID input:

```text
syntactically valid
bounded
must resolve when existence required
```

Known UUID alone never grants access.

---

# 32. Text fields

Must define:

```text
trim policy
empty-string normalization
length ceiling
control-character handling
```

---

# 33. Empty vs NULL

For optional text:

```text
blank input
→ NULL
```

SHOULD be preferred where no semantic difference exists.

---

# 34. Professional names/titles

Must not be blank if required.

No AI or migration may invent placeholder:

```text
N/A
Unknown actress
-
```

as professional fact.

---

# 35. URLs

Canonical validation:

```text
parseable absolute URL
approved scheme
canonical normalization
no admin/internal route when public-safe URL required
```

---

# 36. Public URLs

Allowed baseline:

```text
https
```

HTTP may be accepted only under explicit development/internal policy.

---

# 37. Unsafe schemes

Reject:

```text
javascript:
data:
file:
vbscript:
```

for professional external links.

---

# 38. Phone

Normalize server-side according to chosen canonical strategy.

Original user-facing formatting MAY be retained separately.

---

# 39. Email

Structural normalization/validation.

Do not claim mailbox existence merely because syntax passes.

---

# 40. Dates

Reject impossible dates.

Date-only professional facts stay `date`.

---

# 41. Date ranges

If both present:

```text
end >= start
```

---

# 42. Measurements

Must be positive and within broad technically plausible safety bounds defined by Profile module.

Validation MUST NOT critique appearance or compare against ideals.

---

# 43. Skill level

Exactly:

```text
1..5
```

---

# 44. CEFR

Exactly:

```text
A1 A2 B1 B2 C1 C2
```

for CEFR-specific non-native level.

---

# 45. Language native relation

If:

```text
is_native = true
```

system must avoid contradictory proficiency semantics according to final module rule.

Preferred baseline:

```text
native is its own proficiency semantics
cefr_level = NULL
```

unless explicitly overridden in Language module.

---

# 46. Same-profile invariant

Core rule:

> **Source entities combined in one professional output/context MUST belong to the same Actor Profile unless relation is explicitly global/reference data.**

---

# 47. Same-profile examples

Required for:

```text
PortfolioItem ↔ MediaAsset
EmotionalSession ↔ MediaAsset
EmotionalGrid ↔ Session
Role ↔ Project
RoleMedia ↔ Role/Media
Questionnaire ↔ selected items
BuilderSession ↔ selected items
Casting ↔ Profile Match
Theme ↔ Profile
```

---

# 48. VAL-XENT-001

Any cross-profile professional reference is rejected.

---

# 49. Reference dictionaries exception

Global:

```text
SkillDefinition
LanguageDefinition
```

may be referenced by multiple profiles.

---

# 50. Lifecycle validation

An archived entity generally cannot be:

```text
newly published
newly selected into current Builder
set Primary
scheduled for new public use
```

without explicit Restore transition.

---

# 51. Archived media

Cannot be newly assigned to a public Portfolio item.

Historical Revision may continue referring to its exact historical source.

---

# 52. Deleted/redacted data

Cannot be newly selected.

Historical behavior governed DOC-094.

---

# 53. Publication validation

Publish requires:

```text
valid entity
valid dependencies
valid visibility
valid required derivatives
valid concurrency version
```

---

# 54. Draft saving

Draft save MAY permit incomplete data.

---

# 55. Publish boundary

Publish is stricter than Save.

Canonical:

```text
SAVE DRAFT
≠
READY
≠
PUBLISH
```

---

# 56. Media validation

---

# 57. VAL-MED-001 — Upload Transport

Before persistence:

```text
file present
size within configured limit
stream accepted safely
```

---

# 58. VAL-MED-002 — MIME

Do not trust browser extension or `Content-Type` alone.

Server validates actual media type where technically possible.

---

# 59. Extension mismatch

Example:

```text
.exe renamed to .jpg
```

must not become accepted public MediaAsset.

---

# 60. VAL-MED-003 — Supported Media

Allowed type depends on intended capability.

Example:

Portfolio requires image.

Audio library requires audio.

---

# 61. VAL-MED-004 — Immutable Original

Replacing an original binary in-place is prohibited.

New binary → new MediaAsset.

---

# 62. VAL-MED-005 — Storage Success

MediaAsset must not become normal READY state until durable original write is confirmed.

---

# 63. VAL-MED-006 — Checksum

Checksum computed from persisted source and recorded.

---

# 64. VAL-MED-007 — Derivative

Derivative cannot become READY unless:

```text
binary exists
expected type/dimensions where applicable
checksum/metadata recorded
source still resolvable
```

---

# 65. Media archive validation

Before destructive archive/delete, resolve usage dependencies.

---

# 66. Main Portfolio validation

---

# 67. VAL-PRT-001

Portfolio media MUST be an image for photo categories.

---

# 68. VAL-PRT-002

PortfolioItem and MediaAsset same profile.

---

# 69. VAL-PRT-003

Primary `close_up` requires:

```text
category = close_up
```

---

# 70. VAL-PRT-004

Primary `full_body` requires:

```text
category = full_body
```

---

# 71. VAL-PRT-005

At most one current Primary Close-Up.

---

# 72. VAL-PRT-006

At most one current Primary Full Body.

---

# 73. Primary switch

Must occur atomically:

```text
validate candidate
unset old
set new
audit/outbox
commit
```

---

# 74. Primary candidate readiness

Candidate must be:

```text
active
correct category
valid image
required derivative available where publication requires
```

---

# 75. Primary visibility

Setting Primary MUST NOT automatically change visibility flags.

---

# 76. Questionnaire mandatory photo source

Critical rule:

> **Close-Up and Full Body mandatory questionnaire images may originate only from Main Portfolio.**

---

# 77. Explicit prohibition

Cannot substitute:

```text
Emotional photo
Project role photo
Grid cell
social image
```

for mandatory Close-Up/Full Body.

---

# 78. Role media limits

---

# 79. VAL-PRT-007

Per Role:

```text
role photo count <= 5
```

---

# 80. VAL-PRT-008

Home “В образе”:

```text
show_on_home count <= 2
```

---

# 81. Limits are transaction-time

Concurrent additions cannot exceed limits.

---

# 82. Emotional Session validation

---

# 83. VAL-EMO-001

Normal EmotionalSession requires real:

```text
shooting_date
```

before publish/readiness.

---

# 84. Draft/migration exception

Missing shooting date may exist only as incomplete Admin state.

---

# 85. VAL-EMO-002

Media attached to EmotionalSession:

```text
same profile
image type
active source
```

---

# 86. Emotional Grid dimensions

Allowed exactly:

```text
1x2
1x3
1x4
2x2
2x3
2x4
3x2
3x3
3x4
4x2
4x3
4x4
```

---

# 87. VAL-EMO-003

Any other pair is invalid.

Examples rejected:

```text
1x1
2x1
4x5
5x5
```

---

# 88. VAL-EMO-004 — Exact Photo Count

At finalization:

```text
cell_count = rows × columns
```

exactly.

---

# 89. VAL-EMO-005 — Position completeness

Required positions:

```text
0..(rows*columns-1)
```

must each exist exactly once.

---

# 90. VAL-EMO-006 — Source membership

Every cell MediaAsset must belong to the same EmotionalSession.

---

# 91. VAL-EMO-007 — No cross-session image injection

Even same-profile image from another Emotional Session is invalid unless explicitly added to current session first according to module rules.

---

# 92. VAL-EMO-008 — Crop geometry

Crop/pan/scale/rotation must be within supported renderer bounds.

---

# 93. VAL-EMO-009 — No appearance transformation

Configuration may alter only:

```text
crop
pan
scale
rotation
```

within approved rules.

No:

```text
face reshape
skin edit
body edit
generative fill
background replacement
```

---

# 94. VAL-EMO-010 — Face target

Target:

```text
face >= 90% of useful cell area
```

where feasible under canonical requirement.

---

# 95. Face estimate role

AI/computer vision estimate is assistive.

It cannot finalize cell automatically.

---

# 96. If 90% impossible

System must:

```text
flag limitation
require Human review
```

and MUST NOT generate missing image data.

Final module may define whether such cell is a blocker or explicit approved exception.

Default readiness: blocker unless authorized exception is recorded.

---

# 97. VAL-EMO-011 — Human confirmation

Every cell must be explicitly confirmed before Grid Revision finalization.

---

# 98. Suggested AI crop

State:

```text
SUGGESTED
```

does not satisfy Human confirmation.

---

# 99. VAL-EMO-012 — Frozen revision

Finalization copies exact validated configuration into immutable GridRevision.

---

# 100. Render output validation

Required artifacts per finalized Grid:

```text
master
web
PDF
thumbnail
```

according to renderer/module policy.

---

# 101. Grid publication

Cannot point public current state to failed/incomplete required Grid artifact set.

---

# 102. Questionnaire validation

---

# 103. Questionnaire Draft Save

May be incomplete.

Readiness errors displayed but do not necessarily prevent Save.

---

# 104. Questionnaire Publish mandatory baseline

Before Publish at minimum:

```text
profile identification present
eligible Main Portfolio Primary Close-Up exists
eligible Main Portfolio Primary Full Body exists
official Profile URL resolvable
selected content valid
contacts valid according to selection
links valid structurally
QR requested targets eligible
locale/render configuration valid
```

---

# 105. VAL-QNR-001 — Main Portfolio only

Mandatory Close-Up and Full Body cannot come from another module.

---

# 106. VAL-QNR-002 — Exact candidate state

Photo must be:

```text
current eligible PortfolioItem
correct category
published/available as required
allow_in_admin_questionnaires = true
```

for prepared questionnaire.

---

# 107. VAL-QNR-003 — Primary relation

Prepared default mandatory images SHOULD use the authoritative Primary items, unless module configuration explicitly permits a selected eligible alternative.

Canonical minimum remains Main Portfolio category correctness.

---

# 108. VAL-QNR-004 — Selected item existence

Every selected entity:

```text
exists
same profile
supported type
lifecycle eligible
allow_in_admin_questionnaires=true
```

---

# 109. VAL-QNR-005 — Section ownership

Selected entity type must be valid for section.

Example:

```text
Skill → skills section
Project → projects
Contact → contacts
```

---

# 110. VAL-QNR-006 — No hidden type coercion

A Project cannot be inserted into Training section because both happen to have title/description.

---

# 111. VAL-QNR-007 — Emotional representation

Questionnaire Emotional section includes only:

```text
approved Grid composite
shooting date
clickable link to full Emotional Portfolio
```

No individual Emotional source photos.

---

# 112. VAL-QNR-008 — Emotional Grid state

Selected Grid must reference exact finalized/published eligible revision.

---

# 113. VAL-QNR-009 — Contacts

Selected Contact requires:

```text
allow_in_admin_questionnaires = true
active
value valid for type
```

---

# 114. VAL-QNR-010 — Link

Selected ProfessionalLink requires:

```text
allow_in_admin_questionnaires=true
canonical URL structurally valid
```

Health failure MAY be warning/blocker based on link type/readiness policy.

---

# 115. VAL-QNR-011 — QR

If `show_qr=true`:

```text
canonical target exists
target is allowed
target is not admin/private internal URL
QR generation configuration valid
```

---

# 116. QR artifact readiness

Publication may either:

```text
require QR validated before artifact READY
```

or persist Revision then asynchronously produce QR/PDF.

But final downloadable document state cannot be READY with invalid QR.

---

# 117. VAL-QNR-012 — QR decode equality

Before QR artifact READY:

```text
decoded_url === canonical_target_url
```

exactly according to canonical normalization contract.

---

# 118. VAL-QNR-013 — Revision publish transaction

Inside transaction revalidate:

```text
Draft expected_version
current source versions where freshness required
readiness
next revision allocation
current pointer assumptions
```

---

# 119. Questionnaire publish atomicity

Transaction:

```text
validate
freeze Snapshot
insert Revision
update current pointer
update Primary pointer if command includes it
Audit
Outbox
COMMIT
```

---

# 120. PDF is not inside DB transaction

Rendering occurs after commit from exact Revision.

---

# 121. Historical PDF validation

Worker verifies:

```text
Revision exists
snapshot schema supported
requested locale/template supported
```

It MUST NOT validate against current Master values.

---

# 122. Public Builder validation

---

# 123. VAL-BLD-001 — Session

Session must be:

```text
exists
active
not expired
correct profile
supported schema version
```

---

# 124. VAL-BLD-002 — Template

Selected Builder template must be enabled/currently allowed.

---

# 125. VAL-BLD-003 — Source prepared questionnaire

If Builder starts from recommended questionnaire:

```text
source Revision exists
is eligible as starting source
```

and all imported selections are re-filtered through Builder policy.

---

# 126. Public visibility is irrelevant to Builder eligibility

Do not require:

```text
show_on_public_site=true
```

when `allow_in_public_questionnaire_builder=true`.

---

# 127. VAL-BLD-004 — Selected entity

At preview/generation:

```text
exists
same profile
current active/published state as required
allow_in_public_questionnaire_builder=true
section/template permits it
```

---

# 128. VAL-BLD-005 — Mandatory identification

Generation requires Actor identification.

---

# 129. VAL-BLD-006 — Mandatory Close-Up

Must be eligible Main Portfolio Close-Up.

---

# 130. VAL-BLD-007 — Mandatory Full Body

Must be eligible Main Portfolio Full Body.

---

# 131. VAL-BLD-008 — Official Profile URL

Must be present and public-safe.

---

# 132. VAL-BLD-009 — Temporary labels

Casting/project/role temporary labels:

```text
bounded text
safe
session-only
```

No Source mutation.

---

# 133. VAL-BLD-010 — Contacts separate

Contact eligibility evaluated independently.

---

# 134. VAL-BLD-011 — Generate revalidation

All selected entity IDs MUST be reloaded/revalidated server-side immediately before Snapshot creation.

---

# 135. Cached Preview is insufficient

Canonical:

```text
Preview success
≠
Generation permission
```

---

# 136. VAL-BLD-012 — Generation transaction

Inside transaction:

```text
lock/check session version
verify active/not expired
revalidate selections
allocate generation number
create immutable GenerationSnapshot
```

---

# 137. Snapshot only after successful validation

Invalid/ineligible selected values never enter new immutable Snapshot.

---

# 138. VAL-BLD-013 — Session concurrency

Two tabs editing same session use `version`.

Conflicting write returns stale-version conflict rather than silent overwrite.

---

# 139. VAL-BLD-014 — Expiration

Expiration checked both:

```text
scheduler
AND
request-time
```

Scheduler delay cannot keep expired session usable.

---

# 140. Contact validation

---

# 141. VAL-CNT-001

`contact_type` must be allowlisted.

---

# 142. Type-specific validation

Examples:

```text
email → email syntax
phone → canonical phone parse
WhatsApp → phone-compatible destination
social → approved URL
```

---

# 143. Independent flags

All visibility combinations must remain independently validated.

No cross-field rule:

```text
public → Builder
```

is permitted.

---

# 144. Parent/guardian

`relationship_type=parent/guardian` does not auto-publish.

---

# 145. Notification recipient separation

`contacts` cannot be selected as notification recipient implicitly.

Operational notification configuration uses dedicated domain/table.

---

# 146. Professional Link validation

---

# 147. VAL-LNK-001

Canonical URL must parse.

---

# 148. VAL-LNK-002

Supported safe scheme.

---

# 149. VAL-LNK-003

Public/Questionnaire QR target cannot be:

```text
/admin/...
internal storage path
localhost
private RFC1918 destination
secret-bearing administrative route
```

unless explicit secure token-scoped use case permits a protected public endpoint.

---

# 150. URL canonicalization

Canonicalization must occur once using trusted rules.

QR/cache/search should reuse canonical URL rather than independently rewriting it.

---

# 151. Link health

Network health result is not URL validity.

Example:

```text
valid URL + temporary timeout
```

remains structurally valid but health-degraded.

---

# 152. Casting validation

---

# 153. VAL-CST-001 — Casting source preservation

Analysis cannot run without identifiable source snapshot.

---

# 154. VAL-CST-002 — Source asset

Uploaded Casting source file:

```text
safe MIME
private storage
belongs to Casting
```

---

# 155. VAL-CST-003 — AI output schema

AI extraction must parse and pass exact schema.

---

# 156. VAL-CST-004 — Unknown category/value

Unknown enumerated requirement rejected or explicitly represented as unsupported/free-text according to schema.

Do not silently map.

---

# 157. VAL-CST-005 — Missing requirement

If Source lacks requirement:

```text
NOT_SPECIFIED
```

not inferred value.

---

# 158. VAL-CST-006 — Evidence

Confirmed AI extraction SHOULD retain source evidence where technically available.

---

# 159. VAL-CST-007 — Human confirmation

Requirement becomes confirmed only through Human action.

---

# 160. VAL-CST-008 — Human modified requirement

Must preserve both:

```text
AI extracted
Human confirmed
```

---

# 161. VAL-CST-009 — Rejected requirement

Rejected AI extraction cannot participate as confirmed requirement in Profile Match.

---

# 162. VAL-CST-010 — Stale analysis

If Casting source version changed:

current old Analysis becomes stale.

It may remain historical but cannot silently represent current requirements.

---

# 163. Profile Match validation

---

# 164. VAL-CST-011

Match uses only:

```text
Human-confirmed requirements
+
confirmed Profile facts
```

for authoritative comparison.

---

# 165. VAL-CST-012

If profile fact unavailable:

```text
NO_CONFIRMED_DATA
```

---

# 166. VAL-CST-013

Do not coerce:

```text
NO_CONFIRMED_DATA → MISMATCH
```

---

# 167. VAL-CST-014

`MATCH` requires deterministic or explainable semantic comparison supporting match.

---

# 168. VAL-CST-015

Recommendation must reference valid AnalysisRevision and rationale.

---

# 169. Casting questionnaire Draft

AI recommendation alone cannot create/publish.

Human command:

```text
Create Casting Questionnaire Draft
```

must revalidate source/eligibility.

---

# 170. Feedback → Casting validation

---

# 171. VAL-CST-016

Feedback must exist.

---

# 172. VAL-CST-017

Feedback must belong to same profile.

---

# 173. VAL-CST-018

Duplicate Create Casting command with same semantic idempotency identity MUST return/reuse same resulting Casting rather than create duplicates.

---

# 174. Original Feedback remains

Creation of Casting cannot mutate original Feedback into Casting or delete it.

---

# 175. Opportunity Pipeline validation

---

# 176. Current stages

Exactly:

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

# 177. VAL-OPP-001 — Central transition service

All stage changes must use one authoritative transition validator.

---

# 178. No UI-only Kanban writes

Drag/drop:

```text
UI → TransitionOpportunityStage
```

not direct update to projection table.

---

# 179. Transition graph

Canonical allowed forward flow:

```text
new
↓
qualified
↓
materials_requested
↓
self_tape_requested
↓
audition
↓
callback
↓
offer
↓
booked
```

Terminal alternatives may occur from appropriate active stages:

```text
closed_not_selected
withdrawn
```

---

# 180. Controlled skips

Real casting workflows can skip steps.

Therefore transition validator MAY permit configured valid skips such as:

```text
qualified → audition
audition → offer
```

but only according to explicit transition matrix, not arbitrary stage assignment.

---

# 181. Backward transitions

Require explicit supported workflow/reason.

Do not assume arbitrary backward moves.

---

# 182. VAL-OPP-002 — Terminal stage

A terminal Opportunity cannot transition normally without explicit Reopen command if supported.

---

# 183. VAL-OPP-003 — Booked

`booked` requires explicit Human business decision.

---

# 184. AI cannot Book

Prohibited.

---

# 185. VOP cannot Book

Prohibited.

---

# 186. Analytics cannot Book

Prohibited.

---

# 187. VAL-OPP-004 — Version

Stage transition requires expected Opportunity version.

---

# 188. VAL-OPP-005 — Atomic history

Current stage update + StageHistory + Audit/Outbox commit together.

---

# 189. Project Draft from Booked

Separate command.

---

# 190. VAL-OPP-006

Requires current stage:

```text
booked
```

---

# 191. VAL-OPP-007

Project creation uses idempotency/provenance to prevent duplicate Project Draft.

---

# 192. Booked does not auto-publish Project

Result is Draft.

---

# 193. AI/BB validation

---

# 194. VAL-AI-001 — Context ownership

AI request must declare:

```text
capability
task_type
target
allowed sources
```

---

# 195. VAL-AI-002 — Source snapshot

Persistent AI output requires source provenance/snapshot.

---

# 196. VAL-AI-003 — Prompt version

Persistent output requires known prompt version.

---

# 197. VAL-AI-004 — No secrets

Context validator rejects known secret classes.

---

# 198. VAL-AI-005 — Capability scope

Theme AI request containing private Casting Source is invalid architecture/input.

---

# 199. VAL-AI-006 — BB grounding

BB professional factual statements are expected to derive from saved context.

Automatic factual verifier MAY supplement but Human review remains mandatory.

---

# 200. VAL-AI-007 — Apply target version

BB Apply validates:

```text
target exists
target expected/current version compatible
source not unacceptably stale
```

---

# 201. Stale BB Draft

Must not silently overwrite current target.

---

# 202. VAL-AI-008 — Apply through owner

Example Biography:

```text
AIDraft
→ Profile Application Command
→ Profile validation
```

---

# 203. VAL-AI-009 — Apply is Draft mutation

It does not publish.

---

# 204. Theme validation

---

# 205. VAL-THM-001 — Theme schema

Tokens must match supported schema version.

---

# 206. VAL-THM-002 — Unknown token

Reject or safely ignore according to explicit migration schema.

Never execute arbitrary payload.

---

# 207. VAL-THM-003 — Executable code

Generated:

```text
JavaScript
HTML event handlers
arbitrary CSS injection
```

must not become executable theme authority.

---

# 208. VAL-THM-004 — Locks

AI Apply cannot change locked token.

---

# 209. VAL-THM-005 — Accessibility readiness

Before Theme publish/activation:

```text
contrast
focus visibility
text readability
responsive integrity
```

must meet defined checks.

Exact numerical accessibility standards in DOC-152.

---

# 210. VAL-THM-006 — Required UX

Theme cannot remove/block:

```text
Video CTA
Questionnaire CTA
Contact CTA
mandatory navigation/action functionality
```

where product requires them.

---

# 211. VAL-THM-007 — Actress appearance

Theme cannot modify MediaAsset content to alter actress appearance.

---

# 212. VAL-THM-008 — Publish

Requires exact Theme Draft version → immutable ThemeRevision.

---

# 213. Theme activation

Requires existing published/eligible ThemeRevision.

---

# 214. Temporary activation

If `ends_at`:

```text
ends_at > starts_at
```

---

# 215. Social validation

---

# 216. VAL-SOC-001

Social account must be:

```text
connected
authorized
target platform supported
```

before scheduling/publish.

---

# 217. VAL-SOC-002

Selected media must exist and be permitted for Social usage.

---

# 218. VAL-SOC-003

AI/BB caption stays Draft until Human approval.

---

# 219. VAL-SOC-004

Scheduled publication binds:

```text
approved_post_version
target account
scheduled time
```

---

# 220. Post edit after approval

Must:

```text
invalidate existing approval
or
leave scheduled exact approved version frozen
```

No silent publication of newly edited content.

---

# 221. VAL-SOC-005

Repeated target retry cannot republish already successful platform target.

---

# 222. Notification validation

---

# 223. VAL-NOT-001

Notification source business record/event must exist when source semantics require it.

---

# 224. VAL-NOT-002

External recipient comes from Notification configuration, not arbitrary public form input.

---

# 225. VAL-NOT-003

Channel must be enabled/configured.

---

# 226. VAL-NOT-004

Template must exist and support channel/locale.

---

# 227. VAL-NOT-005

External payload must satisfy minimal field allowlist.

---

# 228. VAL-NOT-006

Quiet hours produce schedule, not validation failure.

---

# 229. VAL-NOT-007

Before delayed time-sensitive notification, relevance may be revalidated.

---

# 230. VAL-NOT-008

Provider credential absence is configuration blocker.

Do not mark source Feedback invalid.

---

# 231. Search validation

---

# 232. VAL-SRCH-001

Search query length bounded.

---

# 233. VAL-SRCH-002

Scope valid:

```text
public
builder
admin
```

---

# 234. VAL-SRCH-003

Search indexer only accepts SearchProjection-safe document.

---

# 235. Public Search document

Validation ensures no private field classes.

---

# 236. Search target

Final route revalidates entity lifecycle/access.

---

# 237. Analytics validation

---

# 238. VAL-ANL-001

Event type must be registered.

---

# 239. VAL-ANL-002

Metadata schema validated per event version.

---

# 240. VAL-ANL-003

Private disallowed payload fields rejected or removed before storage.

---

# 241. Analytics validation failure

Must not roll back successful authoritative business transaction unless analytics itself is explicit primary action.

---

# 242. Audit validation

---

# 243. VAL-AUD-001

Critical operation requiring audit MUST provide:

```text
actor
action
entity
timestamp
safe change summary
```

---

# 244. Audit failure in critical transaction

Where Audit is mandatory:

```text
Audit insert failure
→ transaction failure
```

---

# 245. Audit payload

Must be secret-safe.

---

# 246. Outbox validation

---

# 247. VAL-OUT-001

Event type registered.

---

# 248. VAL-OUT-002

Payload version known.

---

# 249. VAL-OUT-003

No secret fields.

---

# 250. Background Job validation

---

# 251. VAL-JOB-001

Job type registered.

---

# 252. VAL-JOB-002

Payload validates against exact `payload_version`.

---

# 253. VAL-JOB-003

Idempotency key present when required.

---

# 254. VAL-JOB-004

No provider secrets in payload.

---

# 255. VAL-JOB-005 — Immutable source job

Must reference exact:

```text
Revision
Snapshot
MediaAsset/transform identity
```

not ambiguous “current”.

---

# 256. VAL-JOB-006 — Current derived job

May skip itself if source version has been superseded.

---

# 257. VAL-JOB-007 — Delayed external action

Revalidate:

```text
not cancelled
still approved
credential/channel valid
not expired
```

before side effect.

---

# 258. Transaction-time validation doctrine

Critical operations MUST revalidate predicates inside transaction.

---

# 259. Why

Between:

```text
initial read
```

and:

```text
write
```

another Admin/tab/worker may change state.

---

# 260. VAL-TXN-001 — Primary switch

Inside transaction:

```text
lock/validate candidate
validate expected version
enforce category
unset old Primary
set candidate
```

DB partial unique remains final guard.

---

# 261. VAL-TXN-002 — Questionnaire Publish

Inside transaction:

```text
load current Draft
compare expected version
recheck mandatory Sources
recheck selected item permissions
recheck relevant source versions
allocate Revision
freeze Snapshot
set current pointer
```

---

# 262. VAL-TXN-003 — Builder Generate

Inside transaction:

```text
session active
expected session version
not expired
selected items still eligible
mandatory items still eligible
generation number unique
freeze Snapshot
```

---

# 263. VAL-TXN-004 — Opportunity Transition

Inside transaction:

```text
expected version
current stage
allowed transition
Human authority when required
append StageHistory
```

---

# 264. VAL-TXN-005 — Grid Finalization

Inside transaction:

```text
draft expected version
allowed dimensions
exact cell count
unique positions
same Session sources
all confirmed
crop validity
face readiness/approved exception
freeze Revision
```

---

# 265. VAL-TXN-006 — Casting requirement confirmation

Inside transaction:

```text
AnalysisRevision exists
not invalidated beyond allowed use
Requirement current version
Human action
confirmed value schema
```

---

# 266. VAL-TXN-007 — Theme Publish

Inside transaction:

```text
Draft version
schema valid
locks respected
accessibility readiness
immutable Revision creation
```

---

# 267. Optimistic concurrency

Required for mutable high-impact entities.

---

# 268. Stale error

Canonical:

```text
VALIDATION_STALE_VERSION
```

with enough safe metadata for UI to refresh/reconcile.

---

# 269. Conflict UX

UI SHOULD offer:

```text
reload
review changes
retry consciously
```

not silently overwrite.

---

# 270. No automatic merge for professional meaning

Automatic merge only for demonstrably independent technical/config edits.

---

# 271. Validation and projections

Projection may expose readiness summary but cannot be authority for commit.

---

# 272. Example

Dashboard says:

```text
Questionnaire READY
```

Publish still executes current authoritative validation.

---

# 273. Validation and cache

Cached eligibility/readiness can improve UI only.

Commit path reads/revalidates current source as required.

---

# 274. Validation and Search

Search result identity cannot skip final domain/access validation.

---

# 275. Validation and AI

AI schema-valid output is still not Human-confirmed business state.

---

# 276. Validation and Background Workers

Worker must distinguish:

## Immutable input job

Validate input existence/integrity, then use exact snapshot.

## Current-state job

Validate current state/freshness and potentially skip obsolete work.

---

# 277. Migration validation

Migration is not allowed to bypass data meaning.

---

# 278. Legacy invalid/incomplete data

Should be classified:

```text
MIGRATED_VALID
MIGRATED_INCOMPLETE
MIGRATED_REQUIRES_REVIEW
MIGRATION_REJECTED
```

or equivalent.

---

# 279. Migration exceptions

Temporary incomplete rows may exist if:

```text
not public
not ready
clearly marked
```

---

# 280. Migration must not fabricate

Examples prohibited:

```text
missing shooting_date → current date
unknown language → B2
missing Project year → 2026
unknown Contact permission → public=true
```

---

# 281. Import validation

Server file/media import still runs normal security/metadata validation.

Filesystem origin is not trust.

---

# 282. URL import validation

External URL-based media/link imports still parse/sanitize canonical URL.

---

# 283. Readiness model

Readiness is deterministic projection over validation rules.

---

# 284. Possible readiness states

```text
READY
BLOCKED
WARNING
PROCESSING
STALE
```

---

# 285. Readiness blockers

Must list actionable issues.

Example:

```text
QUESTIONNAIRE_MISSING_CLOSE_UP
QUESTIONNAIRE_MISSING_FULL_BODY
QUESTIONNAIRE_INVALID_QR_TARGET
GRID_CELL_UNCONFIRMED
BUILDER_SELECTION_REVOKED
```

---

# 286. Readiness warning

Example:

```text
external video health check temporarily failed
```

depending module policy.

---

# 287. Readiness does not change Source

It derives.

---

# 288. Error severity

Canonical:

```text
ERROR
WARNING
INFO
```

---

# 289. Blocking error

Prevents targeted transition only.

It may not prevent ordinary Draft Save.

---

# 290. Accessibility of errors

Validation errors MUST:

```text
identify field/section
be text-readable
not rely on color only
support focus/summary
```

---

# 291. Public validation errors

Must not reveal hidden/internal data.

---

# 292. Example

Builder invalid hidden item:

Preferred:

```text
Выбранный материал больше недоступен.
```

Not:

```text
Project UUID ... exists but allow_in_public_questionnaire_builder=false.
```

---

# 293. Admin diagnostics

Can provide more specific remediation.

---

# 294. Validation localization

Codes stable; messages localized.

---

# 295. No logic in translation text

Business rules cannot depend on comparing localized error strings.

---

# 296. VAL-AP-001

**Client validation only**

---

# 297. VAL-AP-002

**UI Preview treated as publish authorization**

---

# 298. VAL-AP-003

**Builder trusts submitted entity IDs**

---

# 299. VAL-AP-004

**Archived entity selectable because it still exists in DB**

---

# 300. VAL-AP-005

**Emotional Grid finalizes with 15/16 cells**

---

# 301. VAL-AP-006

**AI crop automatically confirms Grid cell**

---

# 302. VAL-AP-007

**Emotional photo substitutes mandatory Full Body**

---

# 303. VAL-AP-008

**Role photo count checked only in browser**

---

# 304. VAL-AP-009

**Questionnaire publish freezes stale Draft without expected version**

---

# 305. VAL-AP-010

**Current Contact permission is assumed from cached Builder Preview**

---

# 306. VAL-AP-011

**Invalid QR becomes warning but PDF marked READY**

---

# 307. VAL-AP-012

**QR encodes Admin URL**

---

# 308. VAL-AP-013

**AI extraction without evidence silently confirmed**

---

# 309. VAL-AP-014

**No Profile data counted as Casting mismatch**

---

# 310. VAL-AP-015

**Opportunity board updates stage column directly**

---

# 311. VAL-AP-016

**AI recommendation sets Booked**

---

# 312. VAL-AP-017

**BB stale Draft overwrites new biography**

---

# 313. VAL-AP-018

**Theme AI arbitrary token/code accepted without schema validation**

---

# 314. VAL-AP-019

**Scheduled Social publishes latest mutable Draft rather than approved version**

---

# 315. VAL-AP-020

**WhatsApp delivery failure makes Feedback invalid**

---

# 316. VAL-AP-021

**Search index state used as Public authorization**

---

# 317. VAL-AP-022

**Migration invents required values**

---

# 318. VAL-AP-023

**Database unique violation is shown raw to user**

---

# 319. VAL-AP-024

**Validation calls external provider inside long transaction**

---

# 320. VAL-AP-025

**Historical Revision revalidated against current Profile and declared invalid**

---

# 321. Module validation matrix

| Module | Save incomplete | Publish/Finalize strict | Concurrency |
|---|---:|---:|---:|
| Profile | Yes | Public readiness where needed | Yes |
| Media | Initial processing | Ready strict | Processing/idempotency |
| Portfolio | Yes | Public strict | Yes |
| Emotional Session | Yes | Date/deps strict | Yes |
| Emotional Grid | Yes | Very strict | Yes |
| Project/Role | Yes | Public strict | Yes |
| Training | Yes | Public strict | Yes |
| Contacts | Yes with valid type | Exposure strict | Yes |
| Questionnaire | Yes | Very strict | Yes |
| Builder Session | Yes | Generate strict | Yes |
| Casting | Yes | Analysis/review strict | Yes |
| Opportunity | No arbitrary stage | Transition strict | Yes |
| BB | AI Draft | Apply strict | Yes |
| Theme | Yes | Publish strict | Yes |
| Social | Yes | Schedule/publish strict | Yes |

---

# 322. Validation ownership matrix

| Rule | Owner |
|---|---|
| Media MIME | Media |
| Portfolio category | Portfolio |
| Grid dimensions/cells | Emotional Grid |
| Project/Role relation | Projects |
| Skill level | Competency |
| Contact format | Contacts |
| Questionnaire readiness | Questionnaire |
| Builder eligibility | Builder |
| QR safety | QR |
| Casting extraction schema | Casting AI |
| Human requirement confirmation | Casting |
| Match logic | Casting |
| Opportunity transition | Opportunity |
| BB Apply | Target owning Domain |
| Theme readiness | Theme |
| Notification channel | Notifications |
| Social approval | Social |

---

# 323. E2E-VAL-001 — Concurrent Primary

Two clients set different Close-Ups Primary simultaneously.

Expected:

```text
one authoritative Primary
one conflict/retry
```

---

# 324. E2E-VAL-002 — Wrong category

Set `waist` image as Primary Full Body.

Rejected.

---

# 325. E2E-VAL-003 — Wrong mandatory source

Try Project Role photo as Questionnaire Full Body.

Rejected.

---

# 326. E2E-VAL-004 — Grid unsupported size

Create 1×1 Grid.

Rejected.

---

# 327. E2E-VAL-005 — Grid incomplete

4×4 with 15 cells.

Draft save possible; Finalize blocked.

---

# 328. E2E-VAL-006 — Grid duplicate position

Two cells position 4.

DB/Application rejects.

---

# 329. E2E-VAL-007 — Grid unconfirmed AI crop

All geometry valid, one cell still SUGGESTED.

Finalize blocked.

---

# 330. E2E-VAL-008 — Grid foreign session media

Insert same-profile image not belonging to EmotionalSession.

Finalize blocked.

---

# 331. E2E-VAL-009 — Questionnaire missing Close-Up

Publish blocked with stable readiness code.

---

# 332. E2E-VAL-010 — Questionnaire missing Full Body

Publish blocked.

---

# 333. E2E-VAL-011 — Questionnaire Emotional

Attempt individual Emotional photos in questionnaire row.

Rejected by section rule.

---

# 334. E2E-VAL-012 — Invalid QR

QR decode produces different URL.

Artifact cannot reach READY.

---

# 335. E2E-VAL-013 — Admin QR target

Attempt QR for `/admin/castings/...`.

Rejected.

---

# 336. E2E-VAL-014 — Prepared questionnaire permissions

Select Contact with:

```text
allow_in_admin_questionnaires=false
```

Rejected.

---

# 337. E2E-VAL-015 — Builder public-only item

Item:

```text
public=true
builder=false
```

Manual Builder ID submission rejected.

---

# 338. E2E-VAL-016 — Builder builder-only item

```text
public=false
builder=true
```

Eligible if remaining conditions pass.

---

# 339. E2E-VAL-017 — Builder revoke during session

Permission true during Preview, false before Generate.

Generate blocked/reconciled.

---

# 340. E2E-VAL-018 — Builder expiry

Session expires while page open.

Generate rejected request-time.

---

# 341. E2E-VAL-019 — Builder concurrency

Two tabs modify session based on same version.

Second conflicting write receives stale version.

---

# 342. E2E-VAL-020 — Casting missing requirement

No age requirement.

AI result persists `NOT_SPECIFIED`, no invented age.

---

# 343. E2E-VAL-021 — Casting malformed AI output

Schema-invalid AI output cannot create confirmed requirement.

---

# 344. E2E-VAL-022 — Casting Human modification

AI B1 → Human B2.

Both values preserved and match uses B2.

---

# 345. E2E-VAL-023 — Casting no profile data

Requirement exists, Profile value absent.

Result `NO_CONFIRMED_DATA`.

---

# 346. E2E-VAL-024 — Stale Casting analysis

Change source after analysis.

Old analysis shown historical/stale, not current authoritative extraction.

---

# 347. E2E-VAL-025 — Opportunity stale transition

Two Admins transition same Opportunity.

Only one operation based on current version succeeds without reconciliation.

---

# 348. E2E-VAL-026 — Opportunity AI Booked attempt

AI/VOP tries to transition to Booked without Human command.

Rejected.

---

# 349. E2E-VAL-027 — Duplicate Project from Booked

Run Create Project Draft twice with same idempotency intent.

One Project Draft.

---

# 350. E2E-VAL-028 — BB stale apply

Generate at Profile v5.

Profile becomes v6.

Blind Apply blocked.

---

# 351. E2E-VAL-029 — Theme invalid contrast

Theme Draft structurally valid but fails accessibility readiness.

Publish blocked.

---

# 352. E2E-VAL-030 — Theme code injection

Theme AI returns JS token/script.

Schema validator rejects.

---

# 353. E2E-VAL-031 — Social edit after schedule

Approve v3, schedule v3, edit to v4.

Scheduled job cannot silently publish v4.

---

# 354. E2E-VAL-032 — Notification invalid recipient

Notification exists.

WhatsApp delivery blocked/fails configuration; source business entity remains valid.

---

# 355. E2E-VAL-033 — Search stale archived target

Stale Search result clicked.

Final route blocks archived entity.

---

# 356. E2E-VAL-034 — Cross-profile Builder

Profile A session submits Profile B Project.

Rejected.

---

# 357. E2E-VAL-035 — Migration missing shooting date

Migration does not invent date.

Record marked incomplete and cannot publish.

---

# 358. E2E-VAL-036 — Media MIME spoof

Executable renamed `.jpg`.

Rejected/quarantined.

---

# 359. E2E-VAL-037 — Historical PDF

Current source becomes invalid/archived after R1.

R1 renderer uses exact snapshot and does not substitute current data.

---

# 360. E2E-VAL-038 — Audit required transaction

Critical visibility change cannot commit if mandatory Audit persistence fails.

---

# 361. E2E-VAL-039 — Job payload version

Unknown background payload schema.

Job fails safely; handler does not guess.

---

# 362. E2E-VAL-040 — Public hidden diagnostic

Builder invalid selection response contains safe message, not internal permissions/storage details.

---

# 363. Validation traceability

Canonical:

```text
BUSINESS RULE
    ↓
VALIDATION RULE
    ↓
APPLICATION COMMAND
    ↓
DB CONSTRAINT / TRANSACTION
    ↓
ERROR CODE
    ↓
TEST
```

---

# 364. Example — Builder

```text
BR:
Builder can use only eligible data
        ↓
VAL-BLD-004 / VAL-BLD-011
        ↓
Generate Builder command
        ↓
transaction-time entity reload
        ↓
GenerationSnapshot
        ↓
E2E-VAL-015/017
```

---

# 365. Example — Emotional Grid

```text
BR:
Exact supported dimensions
Exact cells
All Human-confirmed
        ↓
VAL-EMO-003/004/011
        ↓
FinalizeGrid
        ↓
GridRevision
        ↓
Renderer
```

---

# 366. Example — Casting

```text
SOURCE
  ↓
AI Extraction
  ↓
Schema Validation
  ↓
Human Confirmation
  ↓
Confirmed Requirement
  ↓
Profile Match
```

---

# 367. Example — Opportunity

```text
Current Stage + Version
      ↓
Transition Rule
      ↓
Human Authority
      ↓
Transaction
 ┌────┼────┐
 ▼    ▼    ▼
Stage History Audit/Outbox
```

---

# 368. Validation compliance criteria

Реализация соответствует DOC-093, если:

1. client validation is never the only validation;
2. field/cross-field/cross-entity rules are separated;
3. same-profile invariants are enforced;
4. Draft can remain incomplete where product allows;
5. Publish/Generate/Finalize run strict readiness;
6. Primary Close-Up and Full Body are category-correct;
7. concurrent Primary conflicts are controlled;
8. role photo limits are transaction-safe;
9. EmotionalSession shooting date is mandatory before publication;
10. only approved Grid dimensions are accepted;
11. Grid exact cell count is validated;
12. every Grid position is unique and complete;
13. Grid cells use same-session media;
14. every Grid cell requires Human confirmation;
15. AI crop suggestion cannot finalize Grid;
16. no generative appearance edits are accepted;
17. mandatory questionnaire Close-Up/Full Body come only from Main Portfolio;
18. Questionnaire selections are permission/lifecycle validated;
19. Emotional questionnaire representation uses composite+date+link only;
20. QR targets are safe/canonical;
21. QR decode must equal encoded URL;
22. Builder session expiry is request-time enforced;
23. Builder selection IDs are revalidated server-side;
24. Builder Generate revalidates all items immediately before Snapshot;
25. Public visibility and Builder permission remain independent;
26. Contact type/value is validated;
27. operational notification recipients remain separate;
28. Casting source is preserved;
29. AI extraction is schema validated;
30. missing Casting requirement remains NOT_SPECIFIED;
31. Human confirmation is separate;
32. NO_CONFIRMED_DATA remains distinct from MISMATCH;
33. stale Casting analysis is detected;
34. Opportunity stage transitions use one authoritative service;
35. Booked requires Human decision;
36. stage/history/audit commit atomically;
37. Project creation from Booked is separate/idempotent;
38. BB Apply checks target/source freshness;
39. AI Apply uses owning Domain validation;
40. Theme AI output is schema constrained;
41. Theme accessibility readiness blocks invalid Publish;
42. Social scheduling binds approved version;
43. notification delivery errors do not invalidate source;
44. Search result never bypasses access validation;
45. migration does not fabricate missing professional facts;
46. high-impact operations repeat critical predicates transactionally;
47. async jobs revalidate delayed side effects;
48. validation errors use stable codes;
49. Public errors do not disclose private internals;
50. validation rules are covered by deterministic E2E tests.

---

# 369. Финальная доктрина

> **Validation в платформе является не формой проверки формы ввода, а механизмом защиты профессиональной истины и корректности переходов. Значимые операции — Finalize Emotional Grid, Publish Questionnaire, Generate Builder Snapshot, Confirm Casting Requirement, Transition Opportunity, Publish Theme или Schedule Social Post — выполняются только после server-side проверки текущего состояния, relationships, visibility, source versions и Human authority. Draft может быть неполным; опубликованное или замороженное состояние — нет. Ни AI output, ни cached Preview, ни Search result, ни Background Job не могут заменить эту проверку.**