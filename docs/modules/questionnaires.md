# QUESTIONNAIRES MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация prepared actor questionnaires, variants, Draft → Revision → HTML/PDF, readiness, publication и historical consistency

**Целевой файл:** `docs/modules/questionnaires.md`  
**Документ:** DOC-120  
**Статус:** ✅ Completed  
**Тип:** Module / Actor Questionnaire / Casting Materials / Revisions / PDF / Publication

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
- `docs/architecture/notifications.md`
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
- `docs/modules/portfolio.md`
- `docs/modules/emotional-portfolio.md`
- `docs/modules/emotional-grid.md`
- `docs/modules/projects.md`
- `docs/modules/training.md`
- `docs/modules/skills-languages.md`
- `docs/modules/professional-media-links.md`
- `docs/modules/contacts.md`

---

# 1. Назначение модуля

Questionnaires Module управляет **подготовленными администратором профессиональными актёрскими анкетами**.

Он отвечает за:

- создание вариантов анкеты;
- структуру блоков;
- item-level selection;
- порядок блоков;
- выбор профессиональных материалов;
- локализацию;
- readiness;
- Draft;
- immutable published Revision;
- HTML representation;
- PDF artifacts;
- QR/hyperlinks;
- public questionnaire routes;
- exactly one primary public questionnaire;
- historical consistency;
- regeneration;
- archive/supersede;
- Casting-specific variants.

---

# 2. Что такое Questionnaire

Questionnaire — это **структурированное профессиональное представление уже существующих Source Facts**, подготовленное для удобного принятия casting decision.

Questionnaire не является отдельным профилем актрисы.

---

# 3. Главная доктрина

> **Questionnaire Draft содержит конфигурацию выбора и presentation intent. При публикации система server-side повторно загружает и валидирует Source Facts, замораживает immutable QuestionnaireRevision, а HTML/PDF/QR являются derived artifacts этой exact Revision.**

Canonical:

```text id="haq5oy"
Master Sources
    ↓
Questionnaire Definition
    ↓
Questionnaire Draft / Configuration
    ↓
Readiness Validation
    ↓
Publish
    ↓
Immutable QuestionnaireRevision
    ↓
Document Model
  ┌────┼─────┐
  ▼    ▼     ▼
HTML  PDF   QR
```

---

# 4. Fundamental separation

```text id="oa588w"
QuestionnaireDefinition
≠ QuestionnaireDraft
≠ QuestionnaireRevision
≠ PDF Artifact
≠ Public Builder Session
≠ BuilderGenerationSnapshot
≠ Profile Source
```

---

# 5. Module identifiers

Используются:

```text id="xwb26c"
QNR-*
QNR-TYPE-*
QNR-DRF-*
QNR-REV-*
QNR-BLK-*
QNR-ITM-*
QNR-PUB-*
QNR-PRI-*
QNR-RDY-*
QNR-PDF-*
QNR-HTML-*
QNR-QR-*
QNR-LNG-*
QNR-AI-*
QNR-INV-*
QNR-AP-*
E2E-QNR-*
```

---

# 6. Business purpose

Questionnaire должна позволять casting professional:

1. быстро получить структурированные основные данные;
2. увидеть обязательные Close-Up и Full Body;
3. проверить параметры;
4. увидеть Skills/Languages;
5. открыть video/showreel;
6. посмотреть Emotional Grid;
7. ознакомиться с релевантными Projects/Training;
8. получить контакт;
9. скачать/открыть PDF;
10. перейти к полному Profile.

---

# 7. Questionnaire ≠ Static PDF

Questionnaire является domain object.

PDF — лишь один из outputs.

---

# 8. QNR-INV-001 — PDF Is Derived Artifact

---

# 9. Questionnaire variants

Canonical types:

```text id="1snyrh"
COMPACT
EXTENDED
CASTING
CUSTOM
```

---

# 10. COMPACT

Назначение:

> максимально короткая профессиональная анкета для быстрого решения.

---

# 11. Recommended Compact content

At minimum:

```text id="2cy214"
Identification
Primary Close-Up
Primary Full Body
Core Profile Parameters
Languages
Key Skills
Primary Video
Official Profile URL
Contact
```

plus other selected compact content according to configuration.

---

# 12. EXTENDED

Назначение:

> максимально полный, но структурированный professional actor profile document.

---

# 13. Extended may include

```text id="lnd2s7"
Basic Profile
Portfolio Extras
Appearance/Profile Parameters
Skills
Languages
Emotional Grid
Projects/Roles
Training
Achievements
Video
Audio
External Links
Contacts
```

subject to eligibility.

---

# 14. CASTING

Prepared questionnaire configured for a specific:

```text id="8j55ar"
casting
project
role
```

---

# 15. Casting variant

May contain temporary contextual labels:

```text id="1z0674"
casting_name
project_name
role_name
```

without changing Master Data.

---

# 16. QNR-TYPE-001 — Casting Labels Are Contextual

---

# 17. CUSTOM

Human-configured composition not limited to predefined variant defaults.

---

# 18. QNR-TYPE-002 — Custom Does Not Bypass Mandatory Minimum

Even Custom must satisfy core professional requirements when published as actor questionnaire.

---

# 19. Mandatory professional minimum

Every generated/published actor Questionnaire MUST contain:

```text id="enjj7h"
Actor identification
Eligible/published Main Portfolio Close-Up
Eligible/published Main Portfolio Full Body
Official Profile URL
```

---

# 20. QNR-RDY-001 — Close-Up Mandatory

Source:

```text id="e6psj8"
Main Portfolio
category = CLOSE_UP
```

---

# 21. QNR-RDY-002 — Full Body Mandatory

Source:

```text id="fom0i0"
Main Portfolio
category = FULL_BODY
```

---

# 22. QNR-RDY-003 — Official Profile URL Mandatory

Must be safe canonical application-controlled profile URL.

---

# 23. Invalid substitutes

The mandatory Close-Up/Full Body cannot be replaced by:

```text id="o12wr5"
Emotional photo
Emotional Grid
Project Role photo
Training photo
Social image
AI-generated image
```

---

# 24. QNR-INV-002 — Emotional/Role Media Never Substitute Mandatory Portfolio Types

---

# 25. Eligible mandatory image

Must satisfy:

```text id="ihzu0z"
same profile
current
Main Portfolio
correct category
allow_in_admin_questionnaires = true
MediaAsset usable/ready
```

and relevant publication/readiness policy.

---

# 26. Primary vs alternative mandatory item

Prepared Questionnaire default SHOULD use eligible current Primary Close-Up/Full Body.

---

# 27. Admin alternative

Admin MAY select another category-correct questionnaire-eligible Main Portfolio item where UX permits.

---

# 28. QNR-INV-003 — Category Correctness Remains Mandatory

---

# 29. Profile URL

Questionnaire must include official actor profile link.

---

# 30. Profile link representation

At minimum:

```text id="8x7a07"
visible URL
+
clickable hyperlink
```

Optional QR according to configuration.

---

# 31. QuestionnaireDefinition

Stable identity of prepared questionnaire.

Representative fields:

```text id="8gmxvn"
id
profile_id
type
name
slug/public identifier?
language
is_primary_public
lifecycle_state
current_revision_id?
version
created_at
updated_at
archived_at
```

---

# 32. Questionnaire Draft

Editable configuration owned by Definition or separate draft identity.

---

# 33. Draft includes

```text id="fhqyq3"
selected blocks
selected items
block ordering
item ordering
presentation options
language
casting labels
QR/link display settings
```

---

# 34. Draft does not own copied professional facts

---

# 35. QNR-DRF-001 — Draft Stores References, Not Duplicated Source Truth

Example:

Good:

```text id="1rb9dt"
portfolioItemId = ...
```

Bad:

```text id="nl8ej1"
heightCm = copied manual value
```

when height belongs Profile Source.

---

# 36. Why

Current Draft preview should generally reflect current Source until publication freezes Revision.

---

# 37. Draft preview

Reads:

```text id="9ya4pv"
current Source
+
Draft selection/configuration
```

---

# 38. Revision

Published immutable professional snapshot.

---

# 39. QNR-REV-001 — Revision Freezes Semantic Content

Revision must not depend on current Master values for historical meaning.

---

# 40. Revision fields conceptually

```text id="4d89c7"
id
questionnaire_definition_id
revision_number
language
questionnaire_type
source_snapshot
document_configuration
published_by
published_at
render_schema_version
checksum
```

plus normalized snapshot structures.

---

# 41. Exact snapshot

May be normalized tables/JSON snapshot depending database model.

But must be immutable and fully sufficient for exact historical regeneration.

---

# 42. What Revision freezes

At minimum, all included professional content:

```text id="92w4p4"
actor identity
profile parameters
selected Main Portfolio
selected portfolio extras
Skills
Languages
Emotional GridRevision
Emotional shooting date
Projects/Roles
Training
Achievements
Professional links
Video/audio
Contacts
Official Profile URL
QR targets/settings
locale-specific rendered content
casting contextual labels
block/item order
```

where selected.

---

# 43. QNR-REV-002 — Revision Uses Exact GridRevision

Not current EmotionalGrid pointer.

---

# 44. QNR-REV-003 — Revision Uses Exact Media Identity

Historical image relation remains exact.

---

# 45. QNR-REV-004 — Revision Freezes Contact Values

Current contact changes later do not modify old Revision.

---

# 46. QNR-REV-005 — Revision Freezes Link URLs

Current ProfessionalLink changes later do not alter old Revision.

---

# 47. Revision numbering

Monotonically increasing within QuestionnaireDefinition:

```text id="5t2fc7"
1
2
3
...
```

---

# 48. Revision number ≠ entity version

---

# 49. QNR-INV-004 — Entity Version and Revision Number Are Independent

---

# 50. Publish lifecycle

Canonical:

```text id="uo0wil"
Draft
  ↓
Readiness
  ↓
Publish
  ↓
Revision N
  ↓
Artifacts
  ↓
Current Published
```

---

# 51. Editing published Questionnaire

Does not mutate Revision.

---

# 52. Instead

```text id="51gi0e"
Revision N
↓
new Draft/configuration
↓
publish
↓
Revision N+1
```

---

# 53. QNR-REV-006 — Published Revision Immutable

---

# 54. Draft lifecycle

Recommended:

```text id="dfglml"
EDITING
READY
STALE
```

---

# 55. `EDITING`

Incomplete or currently changing.

---

# 56. `READY`

All configured hard checks pass.

---

# 57. `STALE`

Selected Source changed in a manner requiring review/revalidation.

---

# 58. Important nuance

Because Draft generally resolves current Source dynamically, not every Source change makes Draft semantically stale.

Staleness applies particularly where:

- selected entity archived;
- permission revoked;
- required media failed;
- selected Grid replaced/invalid;
- referenced item becomes unavailable.

---

# 59. QNR-DRF-002 — Staleness Is Dependency State, Not Historical Rewrite

---

# 60. Publish command

Canonical:

```text id="9zsx30"
PublishQuestionnaire
```

---

# 61. Publish input

Conceptually:

```text id="6djhzo"
questionnaireId
expectedVersion
idempotencyKey
```

---

# 62. Browser does not submit frozen Source facts

---

# 63. Server publish flow

```text id="51zc6k"
load Definition + Draft
↓
expectedVersion
↓
load current Source records
↓
revalidate ownership
↓
revalidate permissions
↓
revalidate mandatory minimum
↓
revalidate media readiness
↓
resolve canonical URLs
↓
resolve exact GridRevision
↓
build normalized Document/Snapshot
↓
BEGIN
  create immutable Revision
  update current revision pointer
  update publication state
  Audit
  Outbox
COMMIT
↓
async HTML/PDF/QR artifacts
```

---

# 64. QNR-INV-005 — Publish Revalidates Source Server-Side

---

# 65. Publish idempotency

Mandatory.

---

# 66. Double click

Same semantic publish intent must not create duplicate Revisions.

---

# 67. Response loss

Retry with same idempotency key returns same Revision result.

---

# 68. New changed Draft

Requires new semantic operation/idempotency identity.

---

# 69. QNR-INV-006 — Publish Is Idempotent

---

# 70. Concurrency

Draft/Definition has version.

---

# 71. Stale publish

If Draft/config changed since Admin opened page:

return:

```text id="y5e4yn"
STALE_VERSION
```

or typed publish conflict.

---

# 72. QNR-INV-007 — No Last-Write-Wins Publication

---

# 73. Questionnaire block model

Canonical sections may include:

```text id="wqznth"
BASIC
CLOSE_UP
FULL_BODY
EXTRA_PORTFOLIO
APPEARANCE
SKILLS
LANGUAGES
EMOTIONAL
PROJECTS
ROLES
TRAINING
ACHIEVEMENTS
VIDEO
AUDIO
LINKS
CONTACTS
```

---

# 74. BASIC

Actor identity and essential professional facts.

---

# 75. CLOSE_UP

Mandatory Main Portfolio Close-Up.

---

# 76. FULL_BODY

Mandatory Main Portfolio Full Body.

---

# 77. EXTRA_PORTFOLIO

Additional explicitly selected Main Portfolio images.

---

# 78. APPEARANCE

Structured Profile parameters where allowed.

---

# 79. SKILLS

Selected/current eligible ActorSkills.

---

# 80. LANGUAGES

Selected/current eligible ActorLanguages.

---

# 81. EMOTIONAL

Exactly approved Emotional representation.

---

# 82. PROJECTS / ROLES

Selected professional experience.

---

# 83. TRAINING

Selected educational history.

---

# 84. ACHIEVEMENTS

Selected professional achievements.

---

# 85. VIDEO

Video intro/showreel/video samples.

---

# 86. AUDIO

Audio/voice samples.

---

# 87. LINKS

Professional external links.

---

# 88. CONTACTS

Selected contact methods.

---

# 89. QNR-BLK-001 — Block Does Not Own Master Data

---

# 90. Block ordering

Explicit and configurable according to Questionnaire type.

---

# 91. Mandatory blocks

Identification + Close-Up + Full Body + official Profile URL cannot be removed from publishable Questionnaire.

---

# 92. QNR-BLK-002 — Custom Cannot Disable Mandatory Professional Minimum

---

# 93. Block order guidance

Decision-oriented default:

```text id="95e4g4"
Basic
Close-Up / Full Body
Parameters
Skills / Languages
Video
Emotional
Projects
Training
Links
Contacts
```

Variant may differ.

---

# 94. First page principle

PDF should prioritize casting decision.

No decorative cover-only page.

---

# 95. QNR-PDF-001 — No Decorative Cover That Delays Professional Facts

---

# 96. Compact block ordering

Recommended:

```text id="2yvvoq"
Basic
Mandatory Photos
Parameters
Languages/Skills
Video
Contact
Profile URL
```

---

# 97. Extended ordering

May include all evidence after high-priority facts.

---

# 98. Casting ordering

May prioritize:

```text id="pl3u3e"
Casting label
Quick facts
matching skills/languages
mandatory photos
relevant Projects
Video
Emotional
Contacts
```

while preserving mandatory content.

---

# 99. Block item selection

For optional blocks, Admin explicitly selects items.

---

# 100. QNR-ITM-001 — Optional Evidence Selection Is Explicit

No:

```text id="rsuvgs"
include everything automatically
```

for compact/casting variants.

---

# 101. Extra Portfolio

Explicitly selected.

---

# 102. Projects

Explicitly selected.

---

# 103. Training

Explicitly selected.

---

# 104. Links

Explicitly selected.

---

# 105. Contacts

Explicitly selected.

---

# 106. Skills/Languages

Can use:

- selected subset;
- all eligible;
- highlighted defaults;

according to variant config.

---

# 107. Exact policy stored in Draft config

---

# 108. Item ordering

Explicit within blocks where user value exists.

---

# 109. QNR-INV-008 — Item Ordering Is Presentation Config, Not Source Ordering Mutation

---

# 110. Profile facts

Questionnaire never edits Profile data.

---

# 111. QNR-INV-009 — Edit Source at Owner, Not in Questionnaire

If Admin sees wrong height:

CTA/action should open Profile editor, not duplicate override in Questionnaire.

---

# 112. Casting contextual labels exception

Temporary questionnaire-specific labels such as:

```text id="e2c82q"
Casting: ...
Role: ...
```

are valid Questionnaire configuration, because they are document context, not Actor Profile Source.

---

# 113. Language

Each Questionnaire Definition/Revision has explicit locale.

Baseline:

```text id="35r2v5"
ru
en
```

---

# 114. QNR-LNG-001 — Locale Is Revision Dimension

---

# 115. RU Revision ≠ EN Revision

Even if same Definition conceptually supports both.

---

# 116. Recommended model

Either:

1. separate Definition per locale; or
2. one Definition with locale-specific published revisions/config.

Preferred product simplicity:

> language should be explicit in Draft and exact in Revision/artifact identity.

---

# 117. No runtime AI translation during PDF render

---

# 118. QNR-LNG-002 — Published Locale Content Must Already Be Resolved

---

# 119. Missing translation

Readiness may:

- fallback to approved Source language according to documented policy; or
- warn/block.

---

# 120. Never silently AI-translate during publication.

---

# 121. Official project/program titles

Retain official title where no confirmed localized version exists.

---

# 122. Primary Public Questionnaire

System requires:

> **Exactly one current primary public questionnaire at most; public readiness may require exactly one.**

---

# 123. QNR-PRI-001 — At Most One Primary Public Questionnaire

---

# 124. Database protection

Use transaction/unique guard.

---

# 125. Primary candidate

Must have:

```text id="34brq3"
published Revision
publicly deliverable
not archived
valid locale/public route
```

---

# 126. Primary switch

Explicit Human command:

```text id="ytektj"
SetPrimaryPublicQuestionnaire
```

---

# 127. QNR-PRI-002 — Primary Switch Is Atomic

---

# 128. Primary does not modify Revision

It changes current Definition/presentation pointer.

---

# 129. QNR-PRI-003 — Primary Is Configuration, Not Revision Mutation

---

# 130. Archiving primary

Must:

- require replacement; or
- leave system with explicit no-primary readiness issue.

---

# 131. No silent fallback to random Questionnaire.

---

# 132. QNR-PRI-004 — No Automatic Arbitrary Primary

---

# 133. Public Questionnaire Hub

Public profile may provide:

```text id="ifg5kx"
Актёрская анкета
```

CTA.

---

# 134. CTA default

Routes to primary public Questionnaire.

---

# 135. PDF chooser

Public Questionnaire page MUST allow user to choose/download available professional PDF variant(s) according to policy.

---

# 136. QNR-PDF-002 — Public PDF Chooser

At minimum, user should be able to identify:

- Questionnaire name/type;
- language;
- download/open PDF.

---

# 137. If multiple public variants

Example:

```text id="pg5puf"
Compact RU
Extended RU
Compact EN
Casting-specific token variant
```

only intended public variants appear.

---

# 138. Token-specific casting Questionnaire

Must not be listed in normal public chooser unless intentionally public.

---

# 139. QNR-PUB-001 — Public Listing Is Explicit

---

# 140. Public Questionnaire route

Should resolve exact current published Revision.

---

# 141. Historical revision routes

Admin/history access may support exact Revision.

Public may expose prior revision only if explicitly required.

---

# 142. Current alias

Example conceptually:

```text id="llon1s"
/questionnaire/compact
```

resolves current revision.

---

# 143. Revision-specific artifact

May use opaque/revision-addressed resource internally.

---

# 144. Cache

Current alias and immutable Revision artifact must be treated differently.

---

# 145. QNR-INV-010 — Current Alias ≠ Immutable Artifact Identity

---

# 146. Questionnaire public visibility

Definition may have public availability config.

---

# 147. Prepared Questionnaire visibility

Different from individual Source eligibility.

Effective exposure requires:

```text id="j5vzei"
Questionnaire public
AND Revision published
AND artifact access allowed
```

---

# 148. QNR-PUB-002 — Questionnaire Cannot Exceed Snapshot Access Policy

---

# 149. Historical privacy issue

Revision may contain contact/media later revoked.

Current public delivery can be revoked/redacted according to access policy without mutating semantic snapshot.

---

# 150. QNR-INV-011 — Immutable Semantic History Does Not Mean Immutable Public Access

---

# 151. HTML representation

HTML must be based on the same normalized `DocumentModel` as PDF.

---

# 152. QNR-HTML-001 — HTML/PDF Semantic Parity

---

# 153. Same content semantics

Must align:

```text id="37etk9"
block presence
item selection
ordering
URLs
Grid
shooting date
contacts
professional facts
```

---

# 154. Responsive differences allowed

HTML may:

- stack;
- collapse optional details;
- use interactive playback;
- use responsive layout.

But semantic content remains equivalent.

---

# 155. PDF representation

Derived from exact Revision.

---

# 156. PDF must be selectable-text document

No screenshot-only PDF.

---

# 157. QNR-PDF-003 — No Screenshot PDF

---

# 158. PDF mandatory behavior

- clickable hyperlinks;
- readable typography;
- preserved Full Body framing;
- QR where selected;
- sensible page breaks;
- exact language;
- professional filename.

---

# 159. PDF source

Exact QuestionnaireRevision.

Not current Profile.

---

# 160. QNR-PDF-004 — Historical PDF Generation Never Reads Current Profile as Fact Source

---

# 161. Artifact model

Conceptually:

```text id="4ojv2w"
QuestionnaireArtifact
```

Fields:

```text id="woyjpr"
revision_id
artifact_type
locale
render_version
template_version
state
checksum
storage reference
created_at
```

---

# 162. Artifact types

At minimum:

```text id="u1cu1h"
HTML
PDF
```

QR artifacts may be separate QR subsystem entities.

---

# 163. Artifact lifecycle

```text id="79ur5l"
QUEUED
RENDERING
VALIDATING
READY
FAILED
```

---

# 164. QNR-INV-012 — Revision Published ≠ PDF READY

---

# 165. Publish response

Can report:

```text id="2ie620"
Revision published
PDF processing
```

rather than block DB transaction on renderer.

---

# 166. Artifact failure

Does not roll back published Revision.

---

# 167. QNR-INV-013 — Renderer Failure Cannot Destroy Revision

---

# 168. Admin sees

```text id="xvi9np"
Published
PDF failed — Retry
```

---

# 169. Public behavior

If PDF unavailable:

HTML may remain accessible if ready/allowed.

PDF CTA clearly unavailable/retry later rather than broken link.

---

# 170. Retry

Reuses exact Revision.

---

# 171. PDF regeneration

New renderer/template version can produce new artifact identity while preserving Revision semantics.

---

# 172. QNR-PDF-005 — Render Version ≠ Content Revision

---

# 173. Filename

Professional and sanitized.

Recommended:

```text id="zrbcmz"
actor-name-questionnaire-compact-ru.pdf
```

Casting:

```text id="5sukly"
actor-name-casting-project-role-ru.pdf
```

---

# 174. Filename must not contain

- raw token;
- internal UUID unless needed;
- unsafe characters;
- private values.

---

# 175. Footer

Recommended/required for generated PDF:

```text id="8m3r02"
official profile source
generated/published date
current profile URL
```

depending exact template policy.

---

# 176. QNR-PDF-006 — Official Profile Link Remains Discoverable

---

# 177. Full Body rendering

Critical:

```text id="u31fdp"
contain
source aspect ratio
no arbitrary cover crop
```

---

# 178. QNR-PDF-007 — Full Body Framing Preserved

---

# 179. Close-Up rendering

Uses approved Main Portfolio source.

No appearance manipulation.

---

# 180. Extra Portfolio

Preserve professional framing according to Portfolio module.

---

# 181. Emotional block

Canonical contents:

```text id="ld83rf"
approved Emotional Grid composite
shooting date
clickable full Emotional Portfolio URL
optional QR
```

---

# 182. QNR-BLK-003 — Emotional Block Never Includes Individual Source Photos

---

# 183. Grid exactness

Uses exact frozen GridRevision.

---

# 184. QNR-BLK-004 — Questionnaire Renderer Does Not Rebuild Emotional Grid

---

# 185. Projects block

Contains selected Project/Role facts.

---

# 186. Role photos

Optional selected Project evidence.

Never mandatory Portfolio substitute.

---

# 187. Training block

Standalone.

Must not appear under “Projects — Other”.

---

# 188. QNR-BLK-005 — Training Is Separate Block

---

# 189. Skills

Displays frozen level 1–5.

---

# 190. Languages

Displays frozen Native/CEFR.

---

# 191. QNR-BLK-006 — Structured Capability Values Must Not Be Creatively Reworded at Render Time

---

# 192. Links

Canonical three-column semantics:

```text id="9rs943"
№ | Описание | URL
```

---

# 193. QNR-BLK-007 — URLs Are Actual Hyperlinks

---

# 194. QR

Per-link/per-questionnaire configuration.

---

# 195. QNR-QR-001 — QR Target Equals Frozen Canonical URL

---

# 196. QR generation

Decode-validated before artifact READY.

---

# 197. No private/Admin URLs.

---

# 198. QR never sole access mechanism.

---

# 199. Contacts

Only selected eligible contacts.

---

# 200. QNR-BLK-008 — Contact Block Does Not Include Notification Recipients

---

# 201. Appearance/Profile facts

Pulled from Profile Source at publish.

---

# 202. Unknown Profile fact

Rendered as:

- omitted; or
- “Не указано”;

according to field-specific document policy.

Never invented.

---

# 203. QNR-INV-014 — Unknown ≠ False/Zero/Empty Fabrication

---

# 204. Readiness architecture

Readiness must explain exactly why Draft cannot publish.

---

# 205. Readiness classes

```text id="qo81zy"
BLOCKER
WARNING
INFO
```

---

# 206. Blocker

Prevents publish.

---

# 207. Warning

Allows publish after Human decision according to policy.

---

# 208. Info

Non-blocking context.

---

# 209. Mandatory blockers

At minimum:

```text id="n5w4xs"
identification missing
Close-Up missing
Full Body missing
official Profile URL missing
required selected media unavailable
selected item no longer eligible
invalid unsafe URL
invalid QR target
invalid Emotional Grid
```

---

# 210. Readiness issue codes

Canonical examples:

```text id="5vto2b"
QNR_IDENTIFICATION_MISSING
QNR_CLOSE_UP_MISSING
QNR_FULL_BODY_MISSING
QNR_PROFILE_URL_MISSING
QNR_SELECTED_ITEM_UNAVAILABLE
QNR_SELECTED_ITEM_NOT_ELIGIBLE
QNR_MEDIA_NOT_READY
QNR_EMOTIONAL_GRID_NOT_READY
QNR_EMOTIONAL_LINK_INVALID
QNR_PROFESSIONAL_LINK_INVALID
QNR_CONTACT_INVALID
QNR_QR_TARGET_INVALID
QNR_LANGUAGE_CONTENT_INCOMPLETE
QNR_DRAFT_STALE
```

---

# 211. Warning examples

```text id="dlhesa"
QNR_OPTIONAL_VIDEO_MISSING
QNR_OPTIONAL_CONTACT_LIMITED
QNR_LINK_HEALTH_DEGRADED
QNR_TRANSLATION_FALLBACK_USED
```

only where policy allows.

---

# 212. Readiness is derived

Never business Source.

---

# 213. QNR-RDY-004 — Readiness Cache Is Not Publish Authority

Publish reruns hard validation.

---

# 214. Eligibility

Each selected Source item must satisfy:

```text id="v0wcrb"
same profile
current
allow_in_admin_questionnaires = true
valid lifecycle
not revoked
```

plus module-specific requirements.

---

# 215. QNR-RDY-005 — Same-Profile Invariant

No cross-profile content.

---

# 216. Public-readiness vs publish-readiness

A Questionnaire may be publishable internally but not public-ready if:

- no safe public route;
- access class restricted;
- artifact incomplete.

---

# 217. QNR-INV-015 — Publishability and Public Deliverability Are Separate

---

# 218. Primary public readiness

Requires:

```text id="mord2z"
primary Definition exists
current published Revision
public access allowed
HTML/PDF state according to CTA policy
```

---

# 219. Primary pointer with failed PDF

Could remain primary if HTML usable and PDF clearly unavailable, but product readiness should warn.

---

# 220. Recommended

Public primary Questionnaire should normally have READY PDF.

---

# 221. Draft Preview

Admin preview should display same semantic DocumentModel intended for publish, but using current Source.

---

# 222. QNR-DRF-003 — Preview Is Not Revision

---

# 223. PDF Preview

Can be rendered from Draft for review, but must visibly mark:

```text id="lflo7a"
DRAFT
```

---

# 224. QNR-PDF-008 — Draft PDF Must Be Clearly Labeled

---

# 225. Draft artifacts

Do not use stable public URL intended for published Questionnaire.

---

# 226. Draft URLs

Admin-only/private.

---

# 227. Casting Questionnaire

Can link to a Casting entity.

---

# 228. Suggested relation

```text id="pcg2a2"
questionnaire_definition.casting_id?
```

or contextual configuration.

---

# 229. QNR-TYPE-003 — Casting Questionnaire Does Not Become Casting Source

Casting owns casting facts.

Questionnaire only references contextual labels/material selection.

---

# 230. Casting AI recommendation

Casting AI may recommend:

```text id="6qgvml"
include Skill X
include Project Y
include Emotional Grid
include Video
```

---

# 231. Human applies recommendation to Draft.

---

# 232. QNR-AI-001 — AI Cannot Publish Questionnaire

---

# 233. QNR-AI-002 — AI Cannot Add Unsupported Professional Fact

---

# 234. AI can create configuration proposal

Allowed:

```text id="snt9rs"
recommended blocks
recommended eligible item IDs
reason
```

---

# 235. AI proposal status

Non-authoritative until Human Apply.

---

# 236. BB Assistant

Can draft:

- brief summary;
- cover/casting-specific text;
- optional descriptive text blocks.

---

# 237. BB text remains AI Draft until Human Apply.

---

# 238. QNR-AI-003 — BB Cannot Modify Snapshot During Render

No AI calls in renderer.

---

# 239. Virtual Operator

May detect:

```text id="a44k6w"
questionnaire stale
PDF failed
QR stale
selected link broken
primary questionnaire missing
Close-Up eligibility revoked
Full Body eligibility revoked
historical artifact mismatch
```

---

# 240. VOP safe auto-actions

Can:

```text id="ty10go"
retry PDF render
retry QR render
invalidate cache
recompute readiness
rebuild preview
```

---

# 241. Requires Human for

```text id="4wz7qc"
change selected facts
publish
change primary
replace photo
change contact
archive questionnaire
```

---

# 242. QNR-AI-004 — VOP Cannot Decide Professional Content

---

# 243. Questionnaire state machine

Definition lifecycle:

```text id="5ymfcb"
DRAFT
PUBLISHED
ARCHIVED
```

---

# 244. Revision lifecycle

```text id="td3qca"
IMMUTABLE_PUBLISHED
```

with artifact processing separately.

---

# 245. Supersession

Old Revision remains.

Current pointer moves to newer Revision.

---

# 246. QNR-REV-007 — Superseded Revision Is Not Deleted

---

# 247. Archive Definition

Removes it from new current/public selection.

Revisions retained.

---

# 248. Restore

Preferred:

```text id="unx0ve"
ARCHIVED → DRAFT
```

---

# 249. QNR-INV-016 — Restore Does Not Auto-Publish or Auto-Primary

---

# 250. Delete Draft

Abandoned Draft may be removable if no dependencies.

---

# 251. Delete Revision

Exceptional.

---

# 252. Hard deletion

Requires retention/privacy/dependency policy.

---

# 253. Public access classes

Possible:

```text id="ddx2s2"
PUBLIC
TOKEN_SCOPED
ADMIN_ONLY
```

for Questionnaire delivery.

---

# 254. Normal primary Questionnaire

`PUBLIC`.

---

# 255. Casting-specific private Questionnaire

May be `TOKEN_SCOPED`.

---

# 256. Token

Cryptographically strong opaque bearer value.

Store hash, not raw token where architecture allows.

---

# 257. QNR-PUB-003 — Token Is Not Revision ID

---

# 258. Invalid/revoked token

Does not reveal hidden Questionnaire existence.

---

# 259. Token-scoped PDF/HTML

Must validate scope on every delivery.

---

# 260. QNR-PUB-004 — Artifact URL Alone Does Not Grant Access Outside Policy

---

# 261. Raw storage URL

Never returned as public contract.

---

# 262. Download route

Application-controlled.

---

# 263. Search

Public Search may index public Questionnaire hub/title as navigation.

---

# 264. It should not duplicate all Questionnaire content into broad search if Profile source already provides it.

---

# 265. Token/private Questionnaire

Never public Search/SEO.

---

# 266. QNR-INV-017 — Search Cannot Expose Casting-Specific Private Questionnaire

---

# 267. SEO

Primary public Questionnaire MAY be indexable if desired.

---

# 268. Duplicate content concern

Canonical Profile should remain primary SEO source where appropriate.

Questionnaire may use canonical/noindex strategy according to SEO design.

---

# 269. Raw PDF URLs

Do not unnecessarily expose obsolete revisions in sitemap.

---

# 270. Analytics

Useful events:

```text id="6alrpz"
questionnaire_opened
questionnaire_pdf_opened
questionnaire_pdf_downloaded
questionnaire_link_clicked
questionnaire_qr_target_opened
```

where measurable.

---

# 271. Public Builder analytics belongs Builder module.

---

# 272. Analytics dimensions

May include:

```text id="f30jx7"
questionnaire_definition_id
revision_id
type
language
```

---

# 273. Avoid raw contact/link token data.

---

# 274. QNR-INV-018 — Analytics Does Not Alter Questionnaire Content

---

# 275. Attribution

Questionnaire may participate in:

```text id="bpcksp"
shared questionnaire
campaign
casting-specific link
```

source attribution.

---

# 276. Attribution metadata is operational/analytics.

Not professional Profile Source.

---

# 277. Public sharing

Stable current URL may resolve latest Revision.

---

# 278. Exact revision sharing

For professional consistency, system MAY support immutable share links to exact Revision if access policy permits.

---

# 279. Current alias behavior

Recipients opening current alias later may see updated Questionnaire.

---

# 280. Immutable share behavior

Recipients opening exact Revision see frozen version.

---

# 281. UX should make distinction clear where both are offered.

---

# 282. QNR-INV-019 — Current and Frozen Share Semantics Must Not Be Ambiguous

---

# 283. Notification integration

Publication may optionally trigger internal notification/activity.

No public professional content should depend on successful notification.

---

# 284. QNR-INV-020 — Notification Failure Does Not Roll Back Publication

---

# 285. Social publishing

Questionnaire URL can be shared through Social workflow only after explicit Human action.

---

# 286. Social post does not alter Questionnaire.

---

# 287. Contact CTA

Questionnaire can provide:

```text id="htbgn5"
Связаться
Пригласить на кастинг
Запросить материалы
```

where appropriate.

---

# 288. “Пригласить на кастинг”

Should create/open Feedback/Casting flow.

Not mutate Questionnaire.

---

# 289. No Dead Ends

Questionnaire ending should provide useful next action.

---

# 290. Public mobile UX

Must remain usable:

- responsive sections;
- full photos readable;
- links clickable;
- contact buttons tappable;
- PDF available.

---

# 291. Full Body on mobile

Still preserve full body framing.

---

# 292. Accessibility

Questionnaire HTML:

- semantic headings;
- alt text;
- keyboard navigation;
- link names;
- sufficient contrast.

---

# 293. PDF accessibility

Target:

- selectable text;
- logical reading order where renderer supports;
- tagged/semantic PDF where practical;
- link annotations;
- meaningful image descriptions/document labels.

---

# 294. QNR-INV-021 — Accessibility Cannot Depend on QR

---

# 295. Theme integration

Theme may style Questionnaire UI.

---

# 296. Theme cannot

```text id="dsu5zd"
remove mandatory block
change Source fact
change URL
change Grid crop
change visibility
make QR unusable
crop Full Body
```

---

# 297. QNR-INV-022 — Theme Is Presentation Only

---

# 298. Questionnaire templates

Template is presentation/configuration rule set.

---

# 299. Template version

Should participate in artifact identity.

---

# 300. Template change

Must not mutate existing artifact/revision semantics.

---

# 301. Re-render old Revision with new template

Possible only if explicit behavior is desired.

Should create new artifact identity and preserve content snapshot.

---

# 302. QNR-PDF-009 — Template Version Is Recorded

---

# 303. Default templates

Could map:

```text id="nfcgje"
COMPACT → compact template
EXTENDED → extended template
CASTING → casting template
CUSTOM → selected compatible template
```

---

# 304. Template does not determine professional eligibility by itself.

---

# 305. DocumentModel

QuestionnaireRevision converts to a normalized renderer-neutral DocumentModel.

---

# 306. QNR-INV-023 — Renderers Consume DocumentModel, Not ORM Rows

---

# 307. Benefits

- HTML/PDF parity;
- testing;
- deterministic layout;
- provider independence;
- historical regeneration.

---

# 308. DocumentModel sections

Conceptually:

```text id="b6n5fb"
Document
  metadata
  identity
  sections[]
    blocks[]
    items[]
  footer
```

---

# 309. No live Source queries inside final renderer

Renderer receives exact frozen data.

---

# 310. QNR-INV-024 — Renderer Is Pure With Respect to Professional Truth

---

# 311. Media resolver

Renderer loads only allowlisted exact MediaAsset/Derivative references from Revision.

---

# 312. No arbitrary remote URL fetching

SSRF protected.

---

# 313. External links are rendered as links, not fetched for content.

---

# 314. QNR-INV-025 — External Link Rendering Does Not Require Fetching Target

---

# 315. PDF image resolution

Need sufficient output for professional print/view.

---

# 316. No generative enhancement.

---

# 317. QR artifact

Vector/high-resolution preferred.

---

# 318. Link health

Broken/degraded current external link may create readiness warning/block according to policy.

---

# 319. Historical Revision remains unchanged.

---

# 320. QNR-INV-026 — Link Health Is Operational, Not Historical Content Mutation

---

# 321. Primary Questionnaire consistency

Primary switch should invalidate:

```text id="m53ves"
Hero/CTA
Questionnaire hub
SEO/current alias
cache
```

---

# 322. Does not regenerate unrelated Revision.

---

# 323. Multiple questionnaires

Supported simultaneously.

Examples:

```text id="m4sktr"
Compact RU
Extended RU
Extended EN
Casting A RU
Casting B EN
Custom Producer Version
```

---

# 324. At most one normal primary public CTA destination.

---

# 325. Public chooser can expose multiple non-primary public variants.

---

# 326. QNR-PRI-005 — Primary Is Default, Not Only Available Public Questionnaire

---

# 327. Duplicate names

Definition name need not be globally unique if IDs differ, but Admin UX should avoid ambiguity.

---

# 328. Public slug

Must be unique within relevant scope.

---

# 329. Rename

Changing display name/slug should not mutate historical Revision data.

---

# 330. Public slug redirect

If changed, optional redirect strategy can preserve existing links.

---

# 331. Slug is not entity identity.

---

# 332. QNR-INV-027 — Slug ≠ Primary Key

---

# 333. Archiving selected Source

If selected Project/Training/etc. becomes archived:

Draft readiness changes.

Existing published Revision remains unchanged.

---

# 334. Revoking QNR eligibility

Same.

New publish cannot include it.

---

# 335. QNR-INV-028 — Current Eligibility Applies at Publish, Not Retroactively to Frozen Semantic History

Public access to history can still be revoked separately for privacy/security.

---

# 336. Profile fact correction

If Profile height corrected before next publish:

Draft preview/current publish uses corrected value.

Old Revision preserves old snapshot.

---

# 337. QNR-INV-029 — Fact Correction Produces New Professional Revision, Not Historical Rewrite

---

# 338. Revisions list

Admin should display:

```text id="i4dkof"
Revision #
published date
published by
language
artifact status
current marker
```

---

# 339. Revision diff

Highly recommended.

Show meaningful differences:

```text id="tylehb"
Profile fact changes
selected photos
Skill levels
Language levels
Projects
Training
Links
Contacts
block order
```

---

# 340. QNR-REV-008 — Revision Diff Is Derived

No mutation.

---

# 341. Rollback

Rollback does not delete new Revisions.

---

# 342. Preferred semantics

To “restore” previous content:

```text id="773qct"
create current Draft based on old Revision/config
→ validate against current Source/access
→ publish new Revision
```

---

# 343. QNR-REV-009 — Rollback Creates New Current State

---

# 344. Why

Old snapshot may contain now-invalid/private data.

Cannot blindly reactivate without current validation.

---

# 345. Copy Questionnaire

Admin may duplicate Definition/config.

---

# 346. Copy behavior

Copies:

- block config;
- selected references where still valid;
- presentation settings.

Does not clone immutable Revision identities as new history.

---

# 347. QNR-INV-030 — Clone Creates New Draft, Not Fake Published History

---

# 348. Readiness dashboard

Admin questionnaire list should show:

```text id="3bir0c"
Ready
Warnings
Blocked
PDF Ready
Primary
Public
```

---

# 349. Questionnaire card

Recommended:

```text id="p6g34q"
Name
Type
Language
Current Revision
Draft changes?
Readiness
PDF status
Public
Primary
```

---

# 350. Editor UX

Recommended sections:

1. General;
2. Type/language;
3. Blocks;
4. Basic/Profile;
5. Portfolio;
6. Emotional;
7. Projects;
8. Training;
9. Skills/Languages;
10. Media/Links;
11. Contacts;
12. QR settings;
13. Ordering;
14. Preview;
15. Readiness;
16. Publish;
17. History.

---

# 351. Progressive editing

Admin should not edit raw JSON.

---

# 352. QNR-INV-031 — Questionnaire Configuration Is Domain UI, Not Arbitrary JSON Editor

---

# 353. Drag reorder

Allowed for optional block/item ordering where UX useful.

---

# 354. Mandatory block protection

UI prevents removal and server enforces.

---

# 355. Source edit affordance

When data incorrect, show:

```text id="h6x119"
Редактировать в профиле
```

or relevant source module.

---

# 356. QNR-UX-001 — Source Ownership Must Be Visible to Admin

---

# 357. Preview modes

Recommended:

```text id="60sgqa"
Desktop HTML
Mobile HTML
PDF Preview
```

---

# 358. Draft preview warning

Clearly:

```text id="xegbr7"
Черновик — не опубликовано
```

---

# 359. Publish confirmation

Should summarize:

- blockers none;
- warnings;
- Revision number about to create;
- artifact generation.

---

# 360. Do not require confirmation for every Save Draft.

Publish is high-impact.

---

# 361. Primary switch separately explicit.

---

# 362. Error taxonomy

At minimum:

```text id="0vqfbg"
QUESTIONNAIRE_NOT_FOUND
QUESTIONNAIRE_TYPE_INVALID
QUESTIONNAIRE_LANGUAGE_INVALID
QUESTIONNAIRE_DRAFT_NOT_FOUND
QUESTIONNAIRE_NOT_READY
QUESTIONNAIRE_CLOSE_UP_MISSING
QUESTIONNAIRE_FULL_BODY_MISSING
QUESTIONNAIRE_PROFILE_URL_MISSING
QUESTIONNAIRE_ITEM_NOT_ELIGIBLE
QUESTIONNAIRE_ITEM_WRONG_PROFILE
QUESTIONNAIRE_MEDIA_NOT_READY
QUESTIONNAIRE_EMOTIONAL_GRID_NOT_READY
QUESTIONNAIRE_LINK_INVALID
QUESTIONNAIRE_CONTACT_INVALID
QUESTIONNAIRE_QR_TARGET_INVALID
QUESTIONNAIRE_PUBLISH_CONFLICT
QUESTIONNAIRE_PRIMARY_CONFLICT
QUESTIONNAIRE_REVISION_NOT_FOUND
QUESTIONNAIRE_ARTIFACT_NOT_READY
QUESTIONNAIRE_ARTIFACT_FAILED
QUESTIONNAIRE_ACCESS_REVOKED
```

---

# 363. Admin readiness errors

May contain safe structured blockers.

---

# 364. Public errors

Must not expose hidden Source inventory.

---

# 365. Example

Admin:

```text id="ydzj09"
Selected Full Body is no longer eligible.
```

Public:

```text id="ux3qph"
Questionnaire is temporarily unavailable.
```

as appropriate.

---

# 366. QNR-INV-032 — Error Detail Is Surface-Safe

---

# 367. Commands

Canonical:

```text id="59vglu"
CreateQuestionnaire
UpdateQuestionnaireMetadata
UpdateQuestionnaireLanguage
UpdateQuestionnaireBlocks
AddQuestionnaireItem
RemoveQuestionnaireItem
ReorderQuestionnaireBlocks
ReorderQuestionnaireItems
UpdateQuestionnairePresentationOptions
UpdateQuestionnaireQrOptions
PublishQuestionnaire
SetPrimaryPublicQuestionnaire
ArchiveQuestionnaire
RestoreQuestionnaire
CreateQuestionnaireDraftFromRevision
RetryQuestionnaireArtifact
```

---

# 368. Optional Casting commands

```text id="fbhp4u"
CreateCastingQuestionnaireDraft
ApplyCastingQuestionnaireRecommendations
```

Human-authorized.

---

# 369. Queries

```text id="a8gtfy"
GetQuestionnairesAdminList
GetQuestionnaireDraft
GetQuestionnaireReadiness
PreviewQuestionnaireHtml
PreviewQuestionnairePdf
GetQuestionnaireRevision
ListQuestionnaireRevisions
GetCurrentPublicQuestionnaire
ListPublicQuestionnaires
GetQuestionnaireArtifact
GetQuestionnaireRevisionDiff
```

---

# 370. Update block input

References block IDs/types/config.

Does not include copied profile facts.

---

# 371. Add item input

```text id="zk0yxc"
questionnaireId
blockType
sourceEntityId
expectedVersion
```

---

# 372. Server checks ownership/eligibility.

---

# 373. Publish uses no client “ready=true”.

---

# 374. QNR-INV-033 — Client Readiness Flag Is Never Authority

---

# 375. Primary switch transaction

```text id="9ztdym"
BEGIN
 lock primary scope
 validate candidate
 unset old primary
 set new primary
 Audit
 Outbox
COMMIT
```

---

# 376. DB uniqueness

Protects one primary public Questionnaire per profile/scope.

---

# 377. Artifact jobs

Suggested:

```text id="07uh7s"
QUESTIONNAIRE_RENDER_HTML
QUESTIONNAIRE_RENDER_PDF
QUESTIONNAIRE_RENDER_QR_SET
QUESTIONNAIRE_VALIDATE_ARTIFACT
```

Could combine HTML synchronous/SSR depending architecture, but semantic input remains Revision.

---

# 378. Job payload

Contains:

```text id="7g5d72"
revision_id
artifact_type
render_version
schema_version
```

not full Source dump.

---

# 379. Worker loads exact Revision.

---

# 380. QNR-INV-034 — Worker Never Uses Current Profile for Historical Render

---

# 381. At-least-once

Jobs must be idempotent.

---

# 382. Artifact dedupe key

Conceptually:

```text id="w5xhly"
revision_id
+
artifact_type
+
locale
+
render_version
+
template_version
```

---

# 383. Worker stale attempt

Older attempt cannot overwrite artifact pointer/status for newer generation incorrectly.

---

# 384. QNR-INV-035 — Artifact State Is Generation-Safe

---

# 385. Outbox events

Suggested:

```text id="570luj"
QuestionnaireCreated
QuestionnaireDraftUpdated
QuestionnairePublished
QuestionnairePrimaryChanged
QuestionnaireArchived
QuestionnaireArtifactRequested
QuestionnaireAccessRevoked
```

---

# 386. DraftUpdated event

May be coalesced to avoid noise.

---

# 387. Consumers

```text id="f070m8"
Public Questionnaire projection
PDF renderer
QR generator
Cache
Search
VOP
Analytics
Homepage/Profile CTA
```

---

# 388. Audit

Must include:

```text id="ri6f9u"
publish
primary switch
archive/restore
public/access-class change
high-impact Draft config changes where useful
```

---

# 389. Revision itself records published by/time.

---

# 390. Do not rely solely on log for business history.

---

# 391. Cache

Two classes:

### Immutable Revision cache

Key:

```text id="78jcmp"
revision_id + locale + artifact/render version
```

### Current alias cache

Key:

```text id="5hsv2d"
questionnaire_id/current pointer
```

---

# 392. QNR-INV-036 — Revision Cache Can Be Long-Lived

Subject to access revocation gate.

---

# 393. Current alias invalidation

On:

- publish;
- primary switch;
- access change;
- archive.

---

# 394. Access before cache

Protected/token/public authorization always evaluated safely.

---

# 395. QNR-INV-037 — Cache Cannot Bypass Access Revocation

---

# 396. Search

Public Questionnaire search only for intentionally public Definitions.

---

# 397. Search result revalidates current exposure.

---

# 398. Public Builder boundary

Prepared Questionnaires Module is distinct from Public Questionnaire Builder.

---

# 399. Prepared Questionnaire

Created/configured by Admin.

---

# 400. Public Builder

Created temporarily by casting specialist using allowed Source subset.

---

# 401. QNR-INV-038 — Prepared Questionnaire ≠ Builder Session

---

# 402. Integration

Public user may start Builder from:

```text id="qxs9kg"
Настроить эту версию под себя
```

---

# 403. In that flow

Builder copies **configuration intent/selections where eligible**, not immutable authority.

---

# 404. Builder then revalidates current Builder permissions independently.

---

# 405. QNR-INV-039 — Admin Questionnaire Eligibility ≠ Builder Eligibility

Critical:

```text id="q5wcgv"
allow_in_admin_questionnaires
≠
allow_in_public_questionnaire_builder
```

---

# 406. Therefore

Starting Builder from prepared Questionnaire may remove items not Builder-eligible.

---

# 407. UI should explain this safely.

---

# 408. Builder implementation detailed in DOC-121.

---

# 409. Public CTA

Questionnaire hub may expose two paths:

```text id="spvdd4"
Готовая актёрская анкета
Собрать анкету под свой кастинг
```

---

# 410. Prepared path uses DOC-120.

Builder path uses DOC-121.

---

# 411. Security — serialized Source

Revision snapshot must not contain non-selected/private Source fields “just in case”.

---

# 412. QNR-PRV-001 — Snapshot Is Data-Minimized

---

# 413. Why

Immutable snapshots can live long.

Do not freeze entire DB rows.

---

# 414. Example

Selecting public agent email does not justify copying:

```text id="aa9xb4"
agent private phone
admin notes
notification preferences
```

into Revision.

---

# 415. QNR-PRV-002 — Snapshot Contains Only Needed Professional Representation

---

# 416. Raw tokens

Never written into filenames/logs/analytics.

---

# 417. Token-sensitive link snapshot

If intentional token-safe professional link is frozen:

handle it under scoped secret/access policy.

---

# 418. Public Questionnaire Revision should prefer stable public destinations.

---

# 419. Security — source ownership

All selected entities checked same profile.

---

# 420. QNR-PRV-003 — UUID Possession Is Not Permission

---

# 421. Security — media resolver

PDF renderer cannot fetch arbitrary browser-supplied URL.

---

# 422. Source Media resolved through internal allowlisted references.

---

# 423. Security — HTML

All editorial text escaped/sanitized according to renderer.

No arbitrary HTML from Description fields baseline.

---

# 424. QNR-PRV-004 — Questionnaire Text Is Not Trusted HTML

---

# 425. Custom styling

No arbitrary CSS/JS per Questionnaire baseline.

Theme/template system handles presentation.

---

# 426. QNR-INV-040 — Custom Questionnaire Means Content Composition, Not Arbitrary Executable Template

---

# 427. Retention

QuestionnaireDefinition is professional configuration.

QuestionnaireRevision is historical immutable business record.

Artifacts are derived.

---

# 428. Retention priority

```text id="1pt6mq"
Revision > Artifact
```

Artifact can be regenerated from Revision where sources/access permit.

---

# 429. But historical media dependency

If exact old media bytes are legally removed, regeneration may become impossible.

Record appropriate redaction/unrenderable state.

---

# 430. QNR-INV-041 — Do Not Substitute Current Media During Historical Regeneration

---

# 431. Archive Definition

Keeps Revision history.

---

# 432. Purge derived PDF

Permitted under retention if Revision preserved and access semantics allow regeneration.

---

# 433. Token expiry

Token-scoped access may expire while Revision remains stored.

---

# 434. QNR-INV-042 — Expired Access Does Not Delete Revision

---

# 435. Restore after backup

Must not:

- republish archived Questionnaire accidentally;
- reactivate revoked token;
- resend notifications;
- overwrite current pointer with older state.

---

# 436. QNR-INV-043 — Restore Reconciles Current Access/Deletion State

---

# 437. Migration

Existing Questionnaire variants/HTML/PDF need migration into:

```text id="79xynr"
QuestionnaireDefinition
Draft/config
Published Revision
Artifacts
```

---

# 438. Existing HTML/PDF only

If exact Source snapshot cannot be reconstructed:

retain as legacy artifact.

Do not claim native Revision equivalence without provenance.

---

# 439. QNR-MIG-001 — Flat PDF Alone Is Not Native Questionnaire Revision

---

# 440. Existing structured questionnaire

Map selected items to current Source identities where evidence is reliable.

---

# 441. Historical legacy values

If exact source entity no longer exists but PDF historically used value:

legacy snapshot may preserve value as historical imported snapshot with provenance.

---

# 442. Do not attach it falsely to current Source.

---

# 443. QNR-MIG-002 — Migration Preserves Historical Truth, Not False Current References

---

# 444. Primary migration

If existing product has one obvious public Questionnaire:

can map as primary after readiness/access review.

---

# 445. Otherwise Human review.

---

# 446. Legacy mandatory photo issue

If old Questionnaire used Project/Emotional photo as “Full Body”:

do not grandfather it as compliant current native Revision.

Historical artifact may remain historical.

New publication must satisfy Main Portfolio rules.

---

# 447. QNR-MIG-003 — Legacy Noncompliance Does Not Weaken Current Rules

---

# 448. Legacy links

Verify canonical URL/hyperlinks/QR.

---

# 449. Legacy QR

Regenerate if exact decode provenance unavailable.

---

# 450. Migration idempotency

Required.

---

# 451. Migration report

Should identify:

```text id="y12kpk"
native migrated
legacy artifact only
missing source mapping
invalid mandatory photos
broken links
QR regeneration required
primary review required
```

---

# 452. Anti-patterns

`QNR-AP-001`  
Treat PDF file as Questionnaire Source.

`QNR-AP-002`  
Copy Profile data into mutable Draft fields.

`QNR-AP-003`  
Allow editing actress height only inside Questionnaire.

`QNR-AP-004`  
Publish without Main Portfolio Close-Up.

`QNR-AP-005`  
Publish without Main Portfolio Full Body.

`QNR-AP-006`  
Use Emotional photo as mandatory Close-Up.

`QNR-AP-007`  
Use Role photo as mandatory Full Body.

`QNR-AP-008`  
Publish without official Profile URL.

`QNR-AP-009`  
Allow Custom variant to bypass mandatory minimum.

`QNR-AP-010`  
Include whole Portfolio automatically in Compact.

`QNR-AP-011`  
Include entire Projects history automatically in Casting.

`QNR-AP-012`  
Use individual Emotional photos in Questionnaire.

`QNR-AP-013`  
Rebuild Emotional Grid from cells in PDF renderer.

`QNR-AP-014`  
Keep Training under Projects/Other.

`QNR-AP-015`  
Serialize private Contacts into Revision accidentally.

`QNR-AP-016`  
Include Admin notification recipient in Contacts block.

`QNR-AP-017`  
Render link-looking text without actual PDF hyperlink.

`QNR-AP-018`  
QR destination differs from hyperlink.

`QNR-AP-019`  
Use Admin/private/storage URL in QR.

`QNR-AP-020`  
Use temporary signed upload URL in PDF.

`QNR-AP-021`  
Generate PDF synchronously inside Source DB transaction.

`QNR-AP-022`  
Renderer queries current Profile while rerendering old Revision.

`QNR-AP-023`  
Current phone update rewrites old Revision.

`QNR-AP-024`  
Current Skill level update rewrites historical PDF.

`QNR-AP-025`  
New Grid primary silently updates old Questionnaire.

`QNR-AP-026`  
Published Revision mutable.

`QNR-AP-027`  
Delete superseded Revision automatically.

`QNR-AP-028`  
Rollback by deleting newer Revision.

`QNR-AP-029`  
Rollback blindly republishes old private values.

`QNR-AP-030`  
Primary Questionnaire chosen randomly when old primary archived.

`QNR-AP-031`  
Two primary public Questionnaires after race.

`QNR-AP-032`  
Primary status stored inside immutable Revision.

`QNR-AP-033`  
Runtime AI translates published PDF.

`QNR-AP-034`  
Runtime AI writes missing Profile data during render.

`QNR-AP-035`  
AI publishes Casting Questionnaire automatically.

`QNR-AP-036`  
AI recommendation changes Source visibility.

`QNR-AP-037`  
Draft preview is treated as historical snapshot.

`QNR-AP-038`  
Client submits `ready=true` and server trusts it.

`QNR-AP-039`  
Browser sends immutable Revision payload and DB accepts it.

`QNR-AP-040`  
Readiness cache is trusted instead of transaction validation.

`QNR-AP-041`  
All errors return generic 500.

`QNR-AP-042`  
PDF is screenshot of HTML page.

`QNR-AP-043`  
Full Body uses `cover` and cuts figure.

`QNR-AP-044`  
Theme removes shooting date.

`QNR-AP-045`  
Theme changes href.

`QNR-AP-046`  
Custom Questionnaire allows arbitrary HTML/JS.

`QNR-AP-047`  
External links fetched by PDF renderer unnecessarily.

`QNR-AP-048`  
Public Builder reuses Admin Questionnaire permission as Builder permission.

`QNR-AP-049`  
Prepared Questionnaire and Builder Session stored as same entity.

`QNR-AP-050`  
Token-scoped Casting Questionnaire appears in sitemap.

`QNR-AP-051`  
Raw token stored in PDF filename.

`QNR-AP-052`  
Raw storage locator returned to Public.

`QNR-AP-053`  
Artifact failure deletes Revision.

`QNR-AP-054`  
Publish rolled back because notification failed.

`QNR-AP-055`  
Old worker overwrites artifact status for newer renderer generation.

`QNR-AP-056`  
Current alias cache bypasses access revocation.

`QNR-AP-057`  
Revision snapshot stores complete ORM rows.

`QNR-AP-058`  
Current Source archive mutates frozen Revision.

`QNR-AP-059`  
Historical missing media silently substituted with current primary.

`QNR-AP-060`  
Migration calls legacy flat PDF a native Revision without provenance.

---

# 453. Core invariants

`QNR-INV-044`  
Prepared Questionnaire is Admin-configured professional document domain.

`QNR-INV-045`  
QuestionnaireDefinition, Draft, Revision and Artifact remain distinct.

`QNR-INV-046`  
Draft stores configuration/references rather than copied mutable truth.

`QNR-INV-047`  
Publish reloads Source server-side.

`QNR-INV-048`  
Publish validates same-profile ownership.

`QNR-INV-049`  
Every published Questionnaire includes actor identification.

`QNR-INV-050`  
Every published Questionnaire includes eligible Main Portfolio Close-Up.

`QNR-INV-051`  
Every published Questionnaire includes eligible Main Portfolio Full Body.

`QNR-INV-052`  
Every published Questionnaire includes official Profile URL.

`QNR-INV-053`  
No Emotional/Project photo substitutes mandatory Portfolio photos.

`QNR-INV-054`  
Compact/Extended/Casting/Custom all obey mandatory minimum.

`QNR-INV-055`  
Optional evidence selection is explicit/configured.

`QNR-INV-056`  
Questionnaire cannot mutate Profile Source.

`QNR-INV-057`  
Questionnaire cannot mutate Skills/Languages Source.

`QNR-INV-058`  
Questionnaire cannot mutate Projects/Training Source.

`QNR-INV-059`  
Questionnaire cannot mutate Contacts/Links Source.

`QNR-INV-060`  
Revision is immutable after publication.

`QNR-INV-061`  
Revision number and entity version are distinct.

`QNR-INV-062`  
Revision freezes exact professional values used.

`QNR-INV-063`  
Revision freezes exact selected Media identity.

`QNR-INV-064`  
Revision freezes exact Emotional GridRevision.

`QNR-INV-065`  
Revision freezes exact shooting date used.

`QNR-INV-066`  
Revision freezes exact Skill levels.

`QNR-INV-067`  
Revision freezes exact Language proficiency.

`QNR-INV-068`  
Revision freezes exact selected Project/Role representation.

`QNR-INV-069`  
Revision freezes exact Training representation.

`QNR-INV-070`  
Revision freezes exact link URLs.

`QNR-INV-071`  
Revision freezes exact contact representation.

`QNR-INV-072`  
Revision freezes exact block/item ordering.

`QNR-INV-073`  
Current Source mutations never rewrite historical Revision.

`QNR-INV-074`  
Publish is idempotent.

`QNR-INV-075`  
Concurrent stale publish is rejected.

`QNR-INV-076`  
At most one Primary Public Questionnaire exists per profile/scope.

`QNR-INV-077`  
Primary switch is atomic.

`QNR-INV-078`  
Primary status remains configuration, not Revision content.

`QNR-INV-079`  
Archiving primary does not silently choose arbitrary replacement.

`QNR-INV-080`  
Public chooser only lists explicitly public/current variants.

`QNR-INV-081`  
Token-scoped questionnaires stay out of ordinary public listing/Search/SEO.

`QNR-INV-082`  
HTML and PDF share semantic DocumentModel.

`QNR-INV-083`  
PDF is selectable text, not screenshot baseline.

`QNR-INV-084`  
PDF preserves Full Body framing.

`QNR-INV-085`  
Emotional block contains composite + date + full portfolio link.

`QNR-INV-086`  
Questionnaire never lays out individual Emotional source photos.

`QNR-INV-087`  
Links are actual hyperlinks.

`QNR-INV-088`  
QR is optional representation, not sole access mechanism.

`QNR-INV-089`  
QR target exactly matches frozen canonical destination.

`QNR-INV-090`  
QR does not target Admin/private/storage resource.

`QNR-INV-091`  
Contacts block excludes NotificationRecipients.

`QNR-INV-092`  
Renderer consumes frozen Revision, not current ORM rows.

`QNR-INV-093`  
Renderer does not invoke AI.

`QNR-INV-094`  
Artifact rendering happens after Revision commit.

`QNR-INV-095`  
Artifact failure does not invalidate/delete Revision.

`QNR-INV-096`  
Artifact jobs are idempotent.

`QNR-INV-097`  
Artifact identity includes Revision/render/template dimensions.

`QNR-INV-098`  
Old worker attempts cannot overwrite newer artifact generation state.

`QNR-INV-099`  
Draft Preview is not immutable professional history.

`QNR-INV-100`  
Draft PDF is clearly labeled Draft.

`QNR-INV-101`  
Prepared Questionnaire and Public Builder remain distinct.

`QNR-INV-102`  
Admin Questionnaire permission and Public Builder permission remain independent.

`QNR-INV-103`  
Starting Builder from prepared Questionnaire revalidates Builder eligibility.

`QNR-INV-104`  
AI may recommend configuration but cannot publish.

`QNR-INV-105`  
BB text is Draft-only until Human Apply.

`QNR-INV-106`  
VOP can retry technical processing but cannot choose professional content.

`QNR-INV-107`  
Search cannot reveal private/token questionnaires.

`QNR-INV-108`  
SEO cannot exceed public Questionnaire access.

`QNR-INV-109`  
Analytics does not modify content.

`QNR-INV-110`  
Notification failure never rolls back publication.

`QNR-INV-111`  
Theme cannot change Source facts, URLs, Grid or mandatory content.

`QNR-INV-112`  
Custom variant does not permit arbitrary executable HTML/JS.

`QNR-INV-113`  
Revision snapshot is data-minimized.

`QNR-INV-114`  
UUID/slugs do not grant access.

`QNR-INV-115`  
Current alias cache cannot bypass access revocation.

`QNR-INV-116`  
Historical access can be revoked without semantic substitution.

`QNR-INV-117`  
Historical media cannot be replaced by current primary during regeneration.

`QNR-INV-118`  
Migration preserves historical truth and provenance.

`QNR-INV-119`  
Legacy noncompliance does not weaken new publish rules.

`QNR-INV-120`  
Core Questionnaire workflows operate fully without AI.

---

# 454. E2E-QNR-001 — Create Compact Draft

Create COMPACT Questionnaire.

Expected editable non-public Draft.

---

# 455. E2E-QNR-002 — Missing Close-Up

No eligible Main Portfolio Close-Up.

Publish returns blocker.

---

# 456. E2E-QNR-003 — Emotional substitute attempt

Use Emotional image as mandatory Close-Up.

Rejected.

---

# 457. E2E-QNR-004 — Missing Full Body

Publish blocked.

---

# 458. E2E-QNR-005 — Role photo substitute

RoleMedia submitted as mandatory Full Body.

Rejected.

---

# 459. E2E-QNR-006 — Missing official Profile URL

Publish blocked.

---

# 460. E2E-QNR-007 — Mandatory baseline

Identification + valid Close-Up + Full Body + official URL all pass.

---

# 461. E2E-QNR-008 — Custom removes mandatory block

Client tries removing Full Body from Custom.

Server still blocks publish.

---

# 462. E2E-QNR-009 — Cross-profile item

Admin submits PortfolioItem from another profile.

Rejected.

---

# 463. E2E-QNR-010 — Revoked eligibility

Selected Training/Project becomes `allowInAdminQuestionnaires=false`.

Publish blocks/removes according to config policy.

---

# 464. E2E-QNR-011 — Draft current facts

Change Profile fact before publish.

Draft Preview reflects current Source.

---

# 465. E2E-QNR-012 — Publish freezes

Publish Revision R1.

Snapshot stores exact current facts.

---

# 466. E2E-QNR-013 — Current Profile changes

After R1, height/other fact changes.

R1 unchanged.

---

# 467. E2E-QNR-014 — New Revision

Publish changed Draft.

R2 contains current corrected Source.

---

# 468. E2E-QNR-015 — Revision immutable

Attempt direct R1 mutation.

Rejected/impossible.

---

# 469. E2E-QNR-016 — Double publish

Same idempotency key double click.

One Revision only.

---

# 470. E2E-QNR-017 — Lost response

Publish committed, network response lost.

Retry same key returns same Revision.

---

# 471. E2E-QNR-018 — Concurrent Draft edit

Another Admin changes Draft after page load.

Stale publish returns conflict.

---

# 472. E2E-QNR-019 — Primary

Set published public Questionnaire as Primary.

Only one Primary remains.

---

# 473. E2E-QNR-020 — Concurrent primary switch

Two requests race.

Final invariant one Primary.

---

# 474. E2E-QNR-021 — Archive Primary

System does not silently assign random alternative.

Readiness warns until Human selects replacement.

---

# 475. E2E-QNR-022 — Public chooser

Only intended public variants displayed.

---

# 476. E2E-QNR-023 — Token casting questionnaire

Token-scoped Casting Questionnaire absent from ordinary public chooser.

---

# 477. E2E-QNR-024 — Search private

Token/private Questionnaire absent from Public Search/SEO.

---

# 478. E2E-QNR-025 — HTML/PDF parity

Same Revision shows same selected blocks/items/URLs in HTML and PDF.

---

# 479. E2E-QNR-026 — PDF selectable text

Extracted text available.

Not a single-page screenshot image.

---

# 480. E2E-QNR-027 — Full Body

PDF preserves complete Full Body framing.

---

# 481. E2E-QNR-028 — Emotional block

Contains exact Grid composite, date and full portfolio URL only.

---

# 482. E2E-QNR-029 — Emotional individual photos

No raw Emotional session photo list appears.

---

# 483. E2E-QNR-030 — Emotional Grid history

R1 uses GridRevision G3.

Current Grid later G4.

R1 remains G3.

---

# 484. E2E-QNR-031 — Links table

PDF/HTML preserve:

```text id="c21ak0"
№ | Описание | URL
```

semantics.

---

# 485. E2E-QNR-032 — Hyperlink

PDF link annotation opens exact frozen canonical URL.

---

# 486. E2E-QNR-033 — QR

QR decodes to exact same URL.

---

# 487. E2E-QNR-034 — Invalid QR target

Admin/private URL blocks readiness.

---

# 488. E2E-QNR-035 — URL history

ProfessionalLink changes A→B.

R1 remains A.

R2 uses B.

---

# 489. E2E-QNR-036 — Contact history

Phone A frozen in R1.

Current contact becomes B.

R1 stays A.

---

# 490. E2E-QNR-037 — Privacy revoke historical

Historical artifact access can be revoked without replacing A with B.

---

# 491. E2E-QNR-038 — Skills history

R1 Horse Riding 3/5.

Current 4/5.

R1 stays 3/5.

---

# 492. E2E-QNR-039 — Languages history

R1 English B2.

Current C1.

R1 stays B2.

---

# 493. E2E-QNR-040 — Project history

Role description changes.

Historical R1 unchanged.

---

# 494. E2E-QNR-041 — Training separate

Training renders in Training block, not Projects/Other.

---

# 495. E2E-QNR-042 — PDF async

Publish commits Revision while PDF is queued.

Revision remains published.

---

# 496. E2E-QNR-043 — PDF failure

Renderer fails.

Revision remains valid; artifact state FAILED.

---

# 497. E2E-QNR-044 — Retry PDF

Retry uses exact Revision.

---

# 498. E2E-QNR-045 — Renderer v2

New render version creates distinct artifact without changing content Revision.

---

# 499. E2E-QNR-046 — Old worker

Stale worker cannot mark newer generation incorrectly.

---

# 500. E2E-QNR-047 — Draft PDF

Draft artifact visibly marked DRAFT and is not public.

---

# 501. E2E-QNR-048 — AI recommendation

Casting AI recommends selected Projects/Skills.

No Draft change until Human Apply.

---

# 502. E2E-QNR-049 — AI publish attempt

No AI/system automation can invoke professional publish authority without Human path.

---

# 503. E2E-QNR-050 — AI disabled

Questionnaire can be created, configured, published and rendered fully manually.

---

# 504. E2E-QNR-051 — VOP artifact retry

VOP retries failed PDF.

Revision content unchanged.

---

# 505. E2E-QNR-052 — VOP stale warning

Close-Up eligibility revoked.

VOP flags Draft/current readiness but does not select replacement.

---

# 506. E2E-QNR-053 — Prepared vs Builder eligibility

Item allowed AdminQ but not Builder.

Prepared Questionnaire includes it.

Builder started from it removes/disallows item.

---

# 507. E2E-QNR-054 — Builder permissions reverse

Item Builder=true, AdminQ=false.

Prepared Questionnaire cannot select it.

Builder may.

---

# 508. E2E-QNR-055 — Current alias

Publish R2.

Current URL resolves R2.

Immutable R1 resource still remains separate where access allowed.

---

# 509. E2E-QNR-056 — Alias access revoke

Current cache contains Questionnaire.

Access revoked.

Next request denied despite cache.

---

# 510. E2E-QNR-057 — Historical rerender missing current Source

Source entity archived.

Historical renderer still uses frozen snapshot/exact asset references, not current projection.

---

# 511. E2E-QNR-058 — Historical media legal purge

Exact media no longer legally available.

System marks artifact unavailable/redacted according to policy; does not substitute new primary photo.

---

# 512. E2E-QNR-059 — Clone

Copy Extended configuration to new Custom Draft.

No fake copied Revision history.

---

# 513. E2E-QNR-060 — Rollback

Create Draft from older Revision.

Current eligibility/privacy revalidated before publishing new Revision.

---

# 514. E2E-QNR-061 — Public PDF chooser language

RU/EN variants labeled correctly.

---

# 515. E2E-QNR-062 — Locale artifact

RU Revision render never silently live-translates from EN via AI.

---

# 516. E2E-QNR-063 — Official title

Project official title not machine-invented during localization.

---

# 517. E2E-QNR-064 — Snapshot privacy

Revision containing public agent email does not include private phone/admin notification preferences.

---

# 518. E2E-QNR-065 — Public UUID enumeration

Knowing hidden Questionnaire UUID does not grant access.

---

# 519. E2E-QNR-066 — Renderer SSRF

External link in Revision is rendered as hyperlink.

Renderer does not fetch arbitrary destination content.

---

# 520. E2E-QNR-067 — Custom HTML injection

Description containing script-like text does not execute in HTML output.

---

# 521. E2E-QNR-068 — Theme

Theme can restyle Questionnaire but cannot crop Full Body/change link/Grid/mandatory block.

---

# 522. E2E-QNR-069 — Accessibility

HTML is keyboard/navigable and links have meaningful labels.

PDF provides clickable links/selectable text.

---

# 523. E2E-QNR-070 — Notification failure

Publish commits successfully.

Optional internal notification fails.

Published Revision remains.

---

# 524. Domain diagram

```text id="9g86cl"
                Master Sources
 Profile / Portfolio / Skills / Languages
 Emotional / Projects / Training / Links / Contacts
                        │
                        ▼
            QuestionnaireDefinition
                        │
                        ▼
              QuestionnaireDraft
                        │
                        ▼
                  READINESS
                        │
                        ▼
                     PUBLISH
                        │
                        ▼
             QuestionnaireRevision
                        │
                 ┌──────┼──────┐
                 ▼      ▼      ▼
                HTML    PDF    QR
```

---

# 525. Revision lifecycle diagram

```text id="qjw0nb"
Draft v17
   ↓
Publish
   ↓
Revision 1 ───────── immutable
   ↓
Further edits
   ↓
Draft v23
   ↓
Publish
   ↓
Revision 2 ───────── immutable

Revision 1 remains unchanged.
```

---

# 526. Mandatory baseline diagram

```text id="tabqi8"
Identification ─────────────┐
Main Portfolio Close-Up ────┤
Main Portfolio Full Body ───┤
Official Profile URL ───────┤
                            ▼
                     Publishable QNR

Emotional / Role photos
cannot substitute mandatory photos.
```

---

# 527. Emotional block diagram

```text id="b0q135"
EmotionalSession
      ↓
GridRevision
      ↓
QuestionnaireRevision
      │
      ├── Grid Composite
      ├── Shooting Date
      └── Full Portfolio URL
```

---

# 528. Prepared vs Builder diagram

```text id="inrwci"
            Actor Source
             /      \
            /        \
 allow_in_admin_QNR  allow_in_public_builder
          │                  │
          ▼                  ▼
Prepared Questionnaire   Public Builder
     DOC-120              DOC-121

Permissions are independent.
```

---

# 529. HTML/PDF parity diagram

```text id="yi317k"
QuestionnaireRevision
        ↓
   DocumentModel
     /       \
    ▼         ▼
  HTML       PDF

Same semantic content.
Different rendering surface.
```

---

# 530. Primary public diagram

```text id="9pnrh5"
Published QNR A ──┐
Published QNR B ──┤
Published QNR C ──┤
                  ▼
           Human selects
                  ↓
        ONE Primary Public QNR
                  ↓
        Profile CTA / Hub
```

---

# 531. Artifact lifecycle diagram

```text id="ejw3xt"
Revision Committed
       ↓
Artifact Job
       ↓
QUEUED
       ↓
RENDERING
       ↓
VALIDATING
   ┌───┴────┐
   ▼        ▼
 READY    FAILED
            │
            └── Retry same Revision
```

---

# 532. Quality gate

Перед implementation должны быть подтверждены:

- [ ] QuestionnaireDefinition;
- [ ] QuestionnaireDraft;
- [ ] QuestionnaireRevision;
- [ ] QuestionnaireArtifact;
- [ ] Compact variant;
- [ ] Extended variant;
- [ ] Casting variant;
- [ ] Custom variant;
- [ ] mandatory identification;
- [ ] mandatory Main Portfolio Close-Up;
- [ ] mandatory Main Portfolio Full Body;
- [ ] mandatory official Profile URL;
- [ ] no substitute media;
- [ ] controlled block taxonomy;
- [ ] item-level selection;
- [ ] block/item ordering;
- [ ] source ownership/edit routing;
- [ ] locale semantics;
- [ ] Draft readiness/staleness;
- [ ] publish server-side revalidation;
- [ ] publish idempotency;
- [ ] optimistic concurrency;
- [ ] immutable Revision;
- [ ] current revision pointer;
- [ ] exactly one primary public Questionnaire maximum;
- [ ] atomic primary switch;
- [ ] public Questionnaire hub;
- [ ] PDF chooser;
- [ ] token-scoped Casting Questionnaire;
- [ ] HTML/PDF semantic parity;
- [ ] DocumentModel;
- [ ] no screenshot PDF;
- [ ] Full Body framing;
- [ ] Emotional Grid exact revision;
- [ ] Training separate block;
- [ ] frozen Skills/Languages;
- [ ] frozen links/contacts;
- [ ] clickable PDF hyperlinks;
- [ ] QR exact-target rules;
- [ ] data-minimized Revision snapshot;
- [ ] async artifact lifecycle;
- [ ] renderer version/template version;
- [ ] generation-safe worker behavior;
- [ ] retries;
- [ ] public/access revocation;
- [ ] current vs immutable aliases;
- [ ] Prepared vs Builder permission separation;
- [ ] Casting AI recommendation-only;
- [ ] BB draft-only;
- [ ] VOP technical-only automation;
- [ ] Search/SEO;
- [ ] Analytics;
- [ ] archive/restore/history;
- [ ] rollback-as-new-revision;
- [ ] migration;
- [ ] accessibility;
- [ ] deterministic E2E suite.

---

# 533. Acceptance criteria

`AC-QNR-001`  
Prepared Questionnaires являются отдельным domain от Profile и Public Builder.

`AC-QNR-002`  
Поддерживаются Compact, Extended, Casting и Custom variants.

`AC-QNR-003`  
Каждая публикуемая анкета содержит actor identification.

`AC-QNR-004`  
Каждая публикуемая анкета содержит eligible Main Portfolio Close-Up.

`AC-QNR-005`  
Каждая публикуемая анкета содержит eligible Main Portfolio Full Body.

`AC-QNR-006`  
Каждая публикуемая анкета содержит official Profile URL.

`AC-QNR-007`  
Emotional/Role media cannot substitute mandatory Portfolio photos.

`AC-QNR-008`  
Custom variant cannot disable mandatory professional minimum.

`AC-QNR-009`  
Draft stores selection/configuration rather than duplicated mutable Profile truth.

`AC-QNR-010`  
Publish reloads and revalidates Source server-side.

`AC-QNR-011`  
Publish verifies same-profile ownership and current Questionnaire eligibility.

`AC-QNR-012`  
Published QuestionnaireRevision is immutable.

`AC-QNR-013`  
Revision freezes exact selected professional values and media identities.

`AC-QNR-014`  
Revision freezes exact Emotional GridRevision and shooting date.

`AC-QNR-015`  
Revision freezes exact Skill levels and Language proficiency.

`AC-QNR-016`  
Revision freezes exact URLs and contact representation.

`AC-QNR-017`  
Current Source changes never rewrite historical QuestionnaireRevision.

`AC-QNR-018`  
Publish is idempotent.

`AC-QNR-019`  
Stale concurrent publication cannot silently overwrite newer Draft.

`AC-QNR-020`  
At most one Primary Public Questionnaire exists per profile/scope.

`AC-QNR-021`  
Primary switch is atomic and Human-controlled.

`AC-QNR-022`  
Archiving Primary does not choose random replacement.

`AC-QNR-023`  
Public hub/chooser exposes only intended public variants.

`AC-QNR-024`  
Token-scoped Casting Questionnaires do not appear in ordinary public listing/Search/SEO.

`AC-QNR-025`  
HTML and PDF derive from the same semantic DocumentModel.

`AC-QNR-026`  
PDF is not screenshot-only.

`AC-QNR-027`  
Full Body framing is preserved in PDF.

`AC-QNR-028`  
Emotional block contains only approved Grid composite, shooting date and full portfolio link.

`AC-QNR-029`  
Training is represented as standalone Training content.

`AC-QNR-030`  
External URLs remain actual HTML/PDF hyperlinks.

`AC-QNR-031`  
QR is configurable per link and targets exact frozen canonical URL.

`AC-QNR-032`  
QR never targets Admin/private/raw-storage destination.

`AC-QNR-033`  
Contacts block never includes Admin NotificationRecipients automatically.

`AC-QNR-034`  
Artifact rendering happens after Revision commit.

`AC-QNR-035`  
PDF/HTML artifact failure does not destroy published Revision.

`AC-QNR-036`  
Artifact generation is idempotent and revision-bound.

`AC-QNR-037`  
Old worker attempts cannot overwrite newer artifact generation state.

`AC-QNR-038`  
Historical rerender never reads current Profile to replace frozen facts.

`AC-QNR-039`  
Draft Preview remains distinct from published Revision.

`AC-QNR-040`  
Draft PDF is visibly identified as Draft.

`AC-QNR-041`  
Admin Questionnaire and Public Builder permissions remain independent.

`AC-QNR-042`  
Starting Builder from prepared Questionnaire revalidates every item under Builder policy.

`AC-QNR-043`  
AI may recommend Questionnaire content but cannot publish or invent Source facts.

`AC-QNR-044`  
BB output remains Draft until Human Apply.

`AC-QNR-045`  
VOP may retry/rebuild technical artifacts but cannot choose professional content.

`AC-QNR-046`  
Current cache/search cannot bypass access revocation.

`AC-QNR-047`  
Revision snapshots are data-minimized and exclude unrelated private Source fields.

`AC-QNR-048`  
Theme cannot modify mandatory content, Source facts, URLs, Grid composition or Full Body framing.

`AC-QNR-049`  
Rollback is implemented as a new validated current Draft/Revision, not historical mutation.

`AC-QNR-050`  
Archive preserves Revision history.

`AC-QNR-051`  
Legacy PDFs without reconstructable native provenance remain legacy artifacts rather than fabricated native Revisions.

`AC-QNR-052`  
Legacy noncompliant materials do not weaken current publication rules.

`AC-QNR-053`  
Questionnaire workflows remain fully operational without AI.

`AC-QNR-054`  
All publication, concurrency, history, privacy, URL, QR and artifact-generation invariants have deterministic E2E tests.

---

# 534. Финальная доктрина

> **Questionnaires Module является профессиональным publication layer над подтверждёнными Master Sources актрисы. Compact, Extended, Casting и Custom questionnaires сохраняют только configuration intent в Draft и не создают альтернативную копию Profile. Каждая публикуемая анкета обязана содержать identification, Main Portfolio Close-Up, Main Portfolio Full Body и official Profile URL. Publish повторно валидирует текущие Source records server-side и атомарно создаёт immutable QuestionnaireRevision; HTML, PDF и QR являются derived artifacts exact этой Revision. Emotional content представлен только утверждённым Grid composite, shooting date и ссылкой на full Emotional Portfolio; links остаются реальными hyperlinks, Contacts не смешиваются с Notification Recipients, а Training не хранится как Project. Exactly one prepared Questionnaire может быть Primary Public CTA, при этом другие public variants остаются доступны через chooser. Current Source changes, AI recommendations, new Grid versions, phone changes или link updates никогда не переписывают исторические Revisions: любое новое профессиональное состояние публикуется как новая Revision.**