# PUBLIC QUESTIONNAIRE BUILDER MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.


## Нормативная спецификация публичного конструктора актёрской анкеты для casting specialist, temporary sessions, templates, live preview и immutable generation snapshots

**Целевой файл:** `docs/modules/public-questionnaire-builder.md`
**Документ:** DOC-121
**Статус:** ✅ Completed
**Тип:** Module / Public Builder / Questionnaire / Casting Self-Service / Temporary Session / PDF Generation

**Связанные документы:**

* `docs/TZ.md`
* `docs/product/product-overview.md`
* `docs/product/terminology.md`
* `docs/product/business-rules.md`
* `docs/product/information-architecture.md`
* `docs/product/scope.md`
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
* `docs/architecture/pdf-generation.md`
* `docs/architecture/background-jobs.md`
* `docs/architecture/cache-strategy.md`
* `docs/architecture/search.md`
* `docs/architecture/ai-architecture.md`
* `docs/database/database-architecture.md`
* `docs/database/data-dictionary.md`
* `docs/database/visibility-and-access.md`
* `docs/database/validation-and-constraints.md`
* `docs/database/deletion-and-retention.md`
* `docs/api/server-actions.md`
* `docs/api/contracts.md`
* `docs/api/errors.md`
* `docs/api/idempotency.md`
* `docs/modules/profile.md`
* `docs/modules/portfolio.md`
* `docs/modules/emotional-portfolio.md`
* `docs/modules/emotional-grid.md`
* `docs/modules/projects.md`
* `docs/modules/training.md`
* `docs/modules/skills-languages.md`
* `docs/modules/professional-media-links.md`
* `docs/modules/contacts.md`
* `docs/modules/questionnaires.md`

---

# 1. Назначение модуля

Public Questionnaire Builder позволяет casting specialist самостоятельно собрать профессиональную актёрскую анкету из **заранее разрешённых владельцем профиля данных**, не меняя Source и не получая доступ к Admin.

Основные задачи:

1. выбрать шаблон;
2. выбрать разрешённые блоки;
3. выбрать отдельные элементы;
4. изменить только порядок/состав presentation;
5. увидеть live preview;
6. сформировать временную персонализированную анкету;
7. получить HTML/PDF;
8. при необходимости указать контекст кастинга/роли;
9. перейти к Casting Invitation / Feedback flow.

---

# 2. Главная доктрина

> **Public Builder не редактирует профиль и не является публичным Admin. Он работает только с отдельной server-side Builder Eligibility Projection, хранит временную configuration session и при Generate повторно загружает разрешённые Source Facts, после чего замораживает immutable BuilderGenerationSnapshot.**

Canonical:

```text
Master Sources
      ↓
Builder Eligibility Policies
      ↓
Server-side Builder Projection
      ↓
Builder Session
      ↓
Visitor Selection / Ordering
      ↓
Live Preview
      ↓
GENERATE
      ↓
Server-side Revalidation
      ↓
Immutable BuilderGenerationSnapshot
      ↓
HTML / PDF
```

---

# 3. Fundamental separation

```text
BuilderSession
≠
QuestionnaireDefinition
≠
QuestionnaireRevision
≠
BuilderGenerationSnapshot
≠
Profile Source
≠
Admin Questionnaire
```

---

# 4. Module identifiers

Используются:

```text
BLD-*
BLD-TPL-*
BLD-SES-*
BLD-ELG-*
BLD-BLK-*
BLD-ITM-*
BLD-PRV-*
BLD-GEN-*
BLD-SNP-*
BLD-PDF-*
BLD-SEC-*
BLD-ANL-*
BLD-INV-*
BLD-AP-*
E2E-BLD-*
```

---

# 5. Business purpose

Public Builder сокращает путь casting specialist от:

```text
«Мне нужна информация только под этот кастинг»
```

до:

```text
«Я собрал подходящую структурированную анкету и могу действовать дальше»
```

без обращения к администратору для каждого варианта PDF.

---

# 6. User persona

Основной пользователь:

```text
Casting Director
Casting Assistant
Producer
Casting Coordinator
International Coordinator
```

---

# 7. No mandatory registration

Canonical requirement:

> **Public Builder не должен требовать обязательной регистрации для базового сценария сборки анкеты.**

---

# 8. BLD-INV-001 — Anonymous Use Supported

Builder Session может работать:

```text
anonymous
+
temporary session identifier
```

с anti-abuse controls.

---

# 9. Registration future option

Account-based persistence может быть добавлен позже.

Но это не baseline dependency.

---

# 10. Entry points

Public Builder должен быть доступен:

1. из Questionnaire Hub;
2. из prepared Questionnaire;
3. из Profile CTA;
4. из Casting-specific link;
5. из Search inside profile where relevant.

---

# 11. Primary public entry

Recommended copy:

```text
Собрать анкету под свой кастинг
```

---

# 12. From prepared questionnaire

Recommended:

```text
Настроить эту версию под себя
```

---

# 13. BLD-INV-002 — Starting Point Does Not Change Security Model

Даже если Builder стартовал из Admin-prepared Questionnaire, Builder должен повторно применить:

```text
allow_in_public_questionnaire_builder
```

для каждого Source item.

---

# 14. Critical permission separation

Canonical:

```text
show_on_public_site
≠
allow_in_admin_questionnaires
≠
allow_in_public_questionnaire_builder
```

---

# 15. BLD-ELG-001 — Builder Permission Independent

Элемент может быть:

```text
public=false
adminQuestionnaire=false
builder=true
```

и быть допустимым только в Builder context.

---

# 16. Opposite case

```text
public=true
builder=false
```

означает:

виден на сайте, но недоступен для пользовательского PDF Builder.

---

# 17. Why

Владельцу профиля необходим независимый контроль:

```text
что видно на сайте
что можно включить в подготовленные анкеты
что может самостоятельно включать casting specialist
```

---

# 18. Builder Eligibility Projection

Public Builder получает **отдельную Projection**.

---

# 19. BLD-ELG-002 — Never Reuse Public Profile DTO Blindly

Public Site visibility не определяет Builder eligibility.

---

# 20. BLD-ELG-003 — Never Reuse Admin DTO

Admin DTO может содержать:

* private fields;
* internal IDs;
* audit metadata;
* Drafts;
* hidden contacts;
* AI data.

---

# 21. Eligibility rule

Conceptually:

```text
BUILDER_ELIGIBLE =
  belongs target profile
  AND current lifecycle allows
  AND allow_in_public_questionnaire_builder = true
  AND access class allows Builder context
  AND surface-specific validation passes
```

---

# 22. Source values remain server-owned

Browser получает только разрешённую representation.

---

# 23. BLD-INV-003 — Browser Selection Uses IDs, Not Authoritative Values

Good:

```text
skillId
selected=true
```

Bad:

```text
skillName="Horse Riding"
level=5
```

как authority.

---

# 24. Templates

Canonical Builder templates:

```text
QUICK
STANDARD
EXTENDED
CASTING
```

---

# 25. QUICK

Business goal:

> минимальное число действий и короткий casting-ready document.

---

# 26. Quick template baseline

Includes:

```text
Basic Identity
Close-Up
Full Body
Core Parameters
Languages
Key Skills
Primary Video
Contact
Official Profile URL
```

---

# 27. STANDARD

Adds:

```text
Emotional Grid
Selected Projects
Training compact
additional professional media where appropriate
```

---

# 28. EXTENDED

Makes available all Builder-eligible professional blocks.

---

# 29. CASTING

Allows:

```text
casting_name
project_name?
role_name
```

as temporary document context.

---

# 30. BLD-TPL-001 — Casting Labels Are Session Context

They do not create:

* Project;
* Role;
* Casting Source;
* Profile data.

---

# 31. Template = default configuration

Template determines:

* initially enabled blocks;
* recommended ordering;
* default selection policy;
* optional limits.

---

# 32. BLD-TPL-002 — Template Does Not Bypass Eligibility

---

# 33. Template selection first

Progressive configuration:

```text
Step 1 — Choose Template
Step 2 — Adjust Content
Step 3 — Preview
Step 4 — Generate
```

---

# 34. BLD-UX-001 — Progressive Disclosure

Visitor should not begin with dozens of checkboxes before understanding template purpose.

---

# 35. Template cards

Recommended:

```text
Quick
Для быстрой оценки

Standard
Сбалансированная профессиональная анкета

Extended
Максимально полный набор доступных данных

Casting
Версия под конкретный кастинг/роль
```

---

# 36. Builder blocks

Canonical:

```text
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

# 37. Mandatory blocks

Every generated actor questionnaire MUST contain:

```text
BASIC / identification
CLOSE_UP
FULL_BODY
official Profile URL
```

---

# 38. BLD-GEN-001 — Mandatory Minimum Cannot Be Disabled

---

# 39. Mandatory Close-Up source

Exactly:

```text
eligible/published Main Portfolio CLOSE_UP
```

---

# 40. Mandatory Full Body source

Exactly:

```text
eligible/published Main Portfolio FULL_BODY
```

---

# 41. Invalid substitutes

Never:

```text
RoleMedia
Emotional photo
Emotional Grid
Project image
Training image
AI-generated image
```

---

# 42. BLD-GEN-002 — Source Category Is Hard Requirement

---

# 43. Mandatory official Profile URL

Generated document includes safe canonical official Profile URL.

---

# 44. No private profile route

Must be public/casting-safe official destination.

---

# 45. Template mandatory behavior

Even if user unchecks:

```text
Close-Up
```

server retains/re-enables mandatory block or rejects invalid session state.

---

# 46. Better UX

Mandatory items rendered as:

```text
Обязательно
```

rather than interactive unchecked checkbox.

---

# 47. Item-level selection

Optional blocks support item-level selection where useful.

---

# 48. Examples

### Portfolio

Visitor can select eligible extra photos.

### Skills

Select specific skills.

### Projects

Select particular projects.

### Training

Select selected courses.

### Links

Select specific professional links.

### Contacts

Select allowed contact methods.

---

# 49. BLD-ITM-001 — Item Selection Never Changes Source

---

# 50. Reordering

Optional drag reorder can be supported for:

* optional blocks;
* selected items.

---

# 51. Mandatory structural restrictions

System can keep certain sections fixed or constrained.

---

# 52. BLD-BLK-001 — Ordering Is Temporary Presentation Config

No mutation of:

```text
Profile display_order
Portfolio order
Projects order
Skills order
```

---

# 53. Drag/drop accessibility

If drag reorder exists, provide keyboard-accessible controls:

```text
Move up
Move down
```

---

# 54. No visual design controls

Canonical requirement:

> Builder does not let casting specialist design the document.

---

# 55. User cannot edit

```text
font
colors
logo
page size
grid dimensions
CSS
background
theme
hero image crop
```

---

# 56. BLD-INV-004 — Content Configuration, Not Design Editor

System performs layout automatically.

---

# 57. Why

Ensures:

* brand consistency;
* PDF readability;
* professional quality;
* accessibility;
* predictable renderer.

---

# 58. Theme

Published Site/Questionnaire template governs appearance within allowed rules.

Visitor cannot override it.

---

# 59. BuilderSession

Temporary mutable configuration.

Representative model:

```text
id
profile_id
template_type
language
configuration
casting_name?
project_name?
role_name?
version
expires_at
created_at
updated_at
```

---

# 60. Recommended exact conceptual structure

```text
builder_sessions
builder_session_items
```

or configuration JSON + normalized item references according to DB design.

---

# 61. BLD-SES-001 — Session Is Temporary

It is not long-term professional Source.

---

# 62. Session identity

Use opaque non-sequential identifier.

---

# 63. Public session key

Must not expose database semantics or authorization beyond intended session scope.

---

# 64. Session lifetime

Must have:

```text
expires_at
```

---

# 65. BLD-SES-002 — Every Anonymous Session Expires

---

# 66. Exact TTL

Operationally configurable.

Should be long enough for realistic editing but not permanent.

---

# 67. Example category

Hours/days rather than indefinite.

Exact value belongs operations/configuration.

---

# 68. Expired session

Cannot mutate or Generate.

---

# 69. Error

```text
BUILDER_SESSION_EXPIRED
```

---

# 70. Session renewal

System MAY allow safe renewal by creating/revalidating new session.

Do not silently make expired session permanent.

---

# 71. Session version

Mutable configuration uses optimistic concurrency:

```text
version
```

---

# 72. BLD-SES-003 — Stale Session Mutation Rejected

---

# 73. Multi-tab

Two tabs editing same session must not silently overwrite each other.

---

# 74. Mutation input

Uses:

```text
sessionId
expectedVersion
```

---

# 75. Session data minimization

Store only:

* selected template;
* selected IDs;
* ordering;
* locale;
* temporary casting labels;
* presentation toggles.

---

# 76. BLD-PRV-001 — Do Not Copy Entire Profile Into Mutable Session

---

# 77. Why

Professional facts remain current Source until Generate.

---

# 78. Preview semantics

Preview combines:

```text
current Builder-eligible Source
+
current BuilderSession configuration
```

---

# 79. BLD-PRV-002 — Preview Is Not Historical Snapshot

---

# 80. Source change during session

Example:

```text
English B2 → C1
```

Before Generate, preview may reflect current Source after refresh/reload.

---

# 81. Visibility change during session

If selected item becomes Builder-ineligible:

preview must no longer treat it as valid.

---

# 82. Session stale indicators

System MAY show:

```text
Некоторые выбранные данные были обновлены.
```

---

# 83. Generate still performs authoritative revalidation.

---

# 84. Live Preview

Required:

```text
Desktop
Mobile
```

at minimum.

---

# 85. BLD-UX-002 — Live Preview Uses Same Semantic Document Model

Preview should approximate eventual generated document semantics.

---

# 86. Desktop preview

Shows structured actor questionnaire.

---

# 87. Mobile preview

Ensures:

* photos readable;
* contacts tappable;
* links visible;
* no overflow.

---

# 88. PDF preview

Optional before Generate.

Could use draft-like rendering.

---

# 89. Preview watermark

Not necessary for normal in-browser preview, but if downloadable preview PDF exists, label as non-final.

---

# 90. Preview cannot grant access

It may show only Builder-eligible data.

---

# 91. BLD-PRV-003 — Preview Path Is Same Authorization Boundary

---

# 92. Basic block

Uses approved Profile Builder Projection.

---

# 93. Profile fields

Each Profile field may independently support:

```text
allow_in_public_questionnaire_builder
```

---

# 94. Example

```text
height → Builder allowed
date of birth → Builder disabled
```

---

# 95. BLD-PRV-004 — Hidden Profile Fields Never Sent to Builder

---

# 96. Portfolio

Builder can receive:

* eligible Close-Up;
* eligible Full Body;
* eligible optional Portfolio items.

---

# 97. Mandatory selection behavior

If multiple eligible Close-Up/Full Body items exist:

template may choose configured default/primary.

Visitor MAY choose alternative eligible item if product enables it.

---

# 98. BLD-ITM-002 — Alternative Must Remain Correct Category

---

# 99. Extra Portfolio

Explicitly selectable.

---

# 100. Portfolio crop

Visitor cannot crop/reposition source image.

---

# 101. BLD-INV-005 — Builder Has No Media Editing Authority

---

# 102. Full Body

Rendered:

```text
contain
source aspect ratio
```

---

# 103. BLD-PDF-001 — Full Body Framing Preserved

---

# 104. Emotional block

Builder receives only **approved Emotional representation**.

---

# 105. Canonical content

```text
Finalized Emotional Grid composite
Shooting Date
Full Emotional Portfolio URL
optional QR
```

---

# 106. BLD-INV-006 — No Individual Emotional Source Photos

---

# 107. Builder cannot

* select cell photos;
* crop cells;
* create Grid;
* change session shooting date.

---

# 108. If multiple eligible Grids

Visitor may choose among approved representations where template allows.

---

# 109. Generate binds exact GridRevision.

---

# 110. Projects/Roles

Builder can select eligible professional projects.

---

# 111. It cannot edit

```text
project title
role
year
description
credits
```

---

# 112. Project Role photos

May be selectable as optional evidence where product/template permits.

They cannot replace mandatory Main Portfolio images.

---

# 113. Training

Builder gets standalone Training items.

---

# 114. BLD-INV-007 — No Project/Other Legacy Representation

---

# 115. Skills

Builder gets eligible ActorSkills with current level.

---

# 116. Visitor can include/exclude.

Cannot change:

```text
level
note
canonical skill identity
```

---

# 117. Languages

Same:

cannot change:

```text
Native/CEFR
```

---

# 118. BLD-INV-008 — Visitor Cannot Increase Capability

---

# 119. Achievements

Builder can select eligible achievements where available.

---

# 120. Professional Video/Audio/Links

Builder can select eligible resources.

---

# 121. Per-link display options

May allow:

```text
show_link
show_qr
```

according to policy.

---

# 122. BLD-INV-009 — Builder May Configure Representation, Not Target URL

---

# 123. QR

If enabled:

```text
QR exact target = selected frozen canonical URL
```

---

# 124. QR never points to:

```text
/admin
raw storage
localhost
private unscoped URL
```

---

# 125. Contacts

Builder gets only Builder-eligible ContactMethods.

---

# 126. Visitor may include/exclude approved methods.

---

# 127. Cannot edit phone/email/social.

---

# 128. BLD-PRV-005 — Builder Does Not Expose Notification Recipients

---

# 129. Contact privacy

Builder-only method may be exposed inside Builder context even if hidden ordinary Public Site.

That is intentional and must be explicitly configured.

---

# 130. BLD-PRV-006 — Builder Context Is Public but Scoped

“Public Builder” does not mean “all public-site data”.

It is a distinct authorized public surface.

---

# 131. Casting template context fields

Allowed temporary fields:

```text
casting_name
project_name
role_name
```

---

# 132. These are user-supplied presentation data.

---

# 133. Input validation

* length bounded;
* sanitized;
* plain text;
* no HTML/JS.

---

# 134. BLD-SEC-001 — Casting Labels Are Untrusted Input

---

# 135. They cannot influence system prompt/AI privileges.

---

# 136. No automatic Casting entity creation

Simply typing:

```text
Role: Doctor
```

does not create saved Casting or Role.

---

# 137. BLD-TPL-003 — Temporary Casting Context Remains Temporary

---

# 138. Generate

Primary high-value action.

Canonical command:

```text
GeneratePublicQuestionnaire
```

---

# 139. Generate flow

```text
BuilderSession
     ↓
load current session
     ↓
verify active/not expired
     ↓
load all selected Source entities server-side
     ↓
same-profile validation
     ↓
current Builder eligibility validation
     ↓
mandatory minimum validation
     ↓
media readiness
     ↓
link/QR safety
     ↓
freeze normalized BuilderGenerationSnapshot
     ↓
commit
     ↓
render artifacts
```

---

# 140. BLD-GEN-003 — Generate Never Trusts Preview

Preview success is not authority.

---

# 141. BLD-GEN-004 — Every Selected Entity Reloaded

Canonical validation:

```text
belongs profile
AND current
AND Builder eligible
AND technically usable
```

---

# 142. Browser-supplied value tampering

If visitor submits:

```text
height = 190
```

but current Source says:

```text
height = 170
```

server uses Source 170.

---

# 143. BLD-GEN-005 — Browser Cannot Override Professional Fact

---

# 144. Mandatory minimum revalidation

Immediately before snapshot:

```text
identification
Close-Up
Full Body
Official Profile URL
```

---

# 145. If mandatory photo was revoked

Generate blocks.

---

# 146. No fallback to Project/Emotional image.

---

# 147. Generate idempotency

Required.

---

# 148. Input

Conceptually:

```text
sessionId
expectedVersion
idempotencyKey
```

---

# 149. Same session/version/config + same key

Returns same snapshot/artifact result.

---

# 150. Changed session + old key

Must produce:

```text
IDEMPOTENCY_CONFLICT
```

---

# 151. BLD-GEN-006 — Generation Intent Bound to Session State

---

# 152. New generation after edits

New key.

Creates new snapshot.

---

# 153. BuilderGenerationSnapshot

Immutable.

---

# 154. BLD-SNP-001 — Snapshot Is Historical Generated Document Source

---

# 155. Snapshot freezes exact selected content

At minimum:

```text
template
locale
casting labels
identity
profile parameters
Close-Up
Full Body
extra portfolio
Skills
Languages
Emotional GridRevision
shooting date
Projects/Roles
Training
Achievements
Video/Audio
Professional links
Contacts
Official Profile URL
QR/link settings
block/item order
generation timestamp
```

where selected.

---

# 156. Snapshot must be data-minimized

Do not freeze entire Source rows.

---

# 157. BLD-SNP-002 — Only Selected/Required Data Frozen

---

# 158. Example

If Visitor selected public agent email only:

do not snapshot:

* private parent phone;
* admin notes;
* notification recipients.

---

# 159. Snapshot mutation

Forbidden.

---

# 160. Later Source changes

Do not rewrite generated document.

---

# 161. Example

```text
English B2
↓ generate
Snapshot = B2
↓ Profile later C1
Snapshot remains B2
```

---

# 162. BLD-SNP-003 — Generated Document Is Stable

---

# 163. Snapshot lifetime

May outlive BuilderSession.

---

# 164. BLD-SES-004 — Session Expiry Does Not Automatically Delete Existing Snapshot

Retention policy controls generated snapshots separately.

---

# 165. Generated artifacts

At minimum:

```text
HTML
PDF
```

---

# 166. PDF

Generated from exact BuilderGenerationSnapshot.

---

# 167. BLD-PDF-002 — PDF Never Reads Current Source After Snapshot

---

# 168. HTML

Same semantic DocumentModel.

---

# 169. BLD-PDF-003 — HTML/PDF Semantic Parity

---

# 170. PDF filename

Recommended:

```text
actor-name-quick-ru.pdf
```

Casting:

```text
actor-name-casting-name-role-name-en.pdf
```

sanitized.

---

# 171. Filename must not contain session secret/token.

---

# 172. Footer

Recommended:

```text
official profile URL
generated date
source attribution
```

---

# 173. Generated date

Represents generation time, not source fact date.

---

# 174. BLD-PDF-004 — Generated Document Identifies Official Source

---

# 175. PDF hyperlinks

All selected ProfessionalLinks/contacts that support hyperlink remain clickable.

---

# 176. QR

Vector/high-resolution in PDF.

Decode-validated.

---

# 177. Full Body framing

Never decorative crop.

---

# 178. Emotional Grid

Uses exact approved composite.

No cell recreation.

---

# 179. Renderers

Consume normalized DocumentModel derived from snapshot.

---

# 180. BLD-SNP-004 — Renderer Is Source-Neutral

No current DB truth lookup beyond exact artifact/media references captured by snapshot.

---

# 181. Artifact lifecycle

```text
QUEUED
RENDERING
VALIDATING
READY
FAILED
```

---

# 182. Generation success semantics

Possible:

```text
Snapshot created
PDF processing
```

without treating renderer delay as Source failure.

---

# 183. BLD-GEN-007 — Artifact Failure Does Not Delete Snapshot

---

# 184. Retry

Uses exact Snapshot.

---

# 185. Artifact identity

Conceptually:

```text
snapshot_id
+
artifact_type
+
locale
+
renderer_version
+
template_version
```

---

# 186. BLD-GEN-008 — Artifacts Are Idempotent by Snapshot Identity

---

# 187. Public artifact access

Generated Builder documents need explicit access model.

---

# 188. Recommended classes

```text
TEMPORARY_SESSION
TOKEN_SCOPED
PUBLIC_SHARE if explicitly enabled
```

---

# 189. Baseline

Generated document should not become globally indexable by default.

---

# 190. BLD-SEC-002 — Generated Builder Document Is Not SEO Public by Default

---

# 191. Access URL

Use stable application-controlled opaque route.

---

# 192. Never:

```text
raw storage URL
filesystem path
guessable sequential ID
```

---

# 193. Sharing

User may receive share/download URL.

If bearer-based:

* random opaque token;
* scoped to exact snapshot/artifact;
* revocable/expiring according to policy.

---

# 194. BLD-SEC-003 — Token Grants Exact Scope Only

---

# 195. Token storage

Prefer hash server-side.

---

# 196. Raw token

Never logs/analytics/filenames.

---

# 197. Public artifact caching

Must be token/access-safe.

No shared cross-token cache key.

---

# 198. BLD-SEC-004 — Cache Must Include Access Context

---

# 199. Session security

Session mutation endpoints must verify session authority/cookie/token as designed.

---

# 200. Session ID alone should not become unrestricted database access token.

---

# 201. Anti-abuse

Because no registration required, module needs:

* rate limiting;
* generation quotas;
* session quotas;
* input length limits;
* request size limits;
* bot/automation controls where appropriate.

---

# 202. BLD-SEC-005 — Anonymous Does Not Mean Unlimited

---

# 203. Rate limit dimensions

Could include:

```text
IP/risk context
session
generation action
time window
```

without creating invasive profiling.

---

# 204. Do not reveal exact anti-abuse thresholds publicly if harmful.

---

# 205. Session creation abuse

Limit mass session creation.

---

# 206. PDF generation abuse

More expensive action → stronger limits.

---

# 207. No CAPTCHA baseline requirement

Can be added if abuse observed.

---

# 208. BLD-SEC-006 — Anti-Abuse Must Not Become Core UX Friction Without Need

---

# 209. CSRF/session integrity

Same-origin browser mutations require appropriate framework protections.

---

# 210. Input sanitization

Casting labels plain text only.

---

# 211. No arbitrary URLs from Builder visitor

Visitor can select only saved approved ProfessionalLinks.

---

# 212. BLD-SEC-007 — Builder Is Not Open URL-to-PDF Service

---

# 213. No arbitrary file upload baseline

Public Builder should not accept arbitrary user files unless future Casting workflow explicitly requires it.

---

# 214. BLD-SEC-008 — Builder Does Not Become Public File Processor

---

# 215. Admin configuration

Admin needs Builder settings.

---

# 216. Admin controls

At minimum:

```text
templates enabled
default template
default blocks
required blocks
block limits
item limits
reorder permission
Casting fields availability
default contact policy
default QR behavior
session expiration policy
```

---

# 217. Source-specific permissions remain in owning modules

Admin Builder config cannot override a Source item:

```text
allow_in_public_questionnaire_builder=false
```

---

# 218. BLD-INV-010 — Template Defaults Cannot Escalate Source Permission

---

# 219. Builder templates as configuration

Could be modeled:

```text
builder_templates
```

with:

```text
type
label
default_config
required_blocks
enabled
version
```

---

# 220. Template versions

Generated Snapshot should record template version.

---

# 221. BLD-TPL-004 — Template Mutation Does Not Change Existing Snapshot

---

# 222. Quick template minimum

Recommended:

```text
Basic
Close-Up
Full Body
Appearance
Languages
Skills
Video
Contact
```

---

# 223. Standard template

Recommended adds:

```text
Emotional
Selected Projects
Training compact
```

---

# 224. Extended template

All eligible blocks optional beyond mandatory baseline.

---

# 225. Casting template

Default focus:

```text
Casting context
Basic
mandatory photos
matching Skills/Languages
relevant Projects
Video
Emotional
Contact
```

---

# 226. User can refine

Within enabled/eligible constraints.

---

# 227. No mandatory Registration CTA before Generate

Normative.

---

# 228. Post-generation CTA

Recommended:

```text
Пригласить на кастинг
Связаться
Открыть полный профиль
```

---

# 229. Casting Invitation integration

After Generate:

```text
BuilderGenerationSnapshot
       ↓
CTA: Invite to Casting
       ↓
Feedback / Casting flow
```

---

# 230. Inquiry provenance

Feedback may store:

```text
questionnaire_session_id?
builder_generation_snapshot_id?
```

or equivalent link.

---

# 231. BLD-INV-011 — Inquiry Link Is Provenance, Not Source Duplication

---

# 232. If session expired

Snapshot ID may still provide provenance where retained.

---

# 233. Analytics

Canonical events:

```text
public_builder_opened
template_selected
section_enabled
section_disabled
pdf_generated
casting_contact_started
```

---

# 234. Additional useful events

May include:

```text
builder_preview_opened
builder_generation_failed
builder_item_selected
```

but avoid excessive event noise.

---

# 235. BLD-ANL-001 — Analytics Cannot Include Private Professional Values Unnecessarily

---

# 236. Example

Good:

```text
section="projects"
```

Bad:

```text
privatePhone="+7..."
```

---

# 237. Attribution

Builder session may retain source:

```text
direct
profile
prepared_questionnaire
casting_link
search
social
campaign
```

---

# 238. Attribution is operational analytics.

Not professional Source.

---

# 239. VOP

Virtual Portfolio Operator may analyze aggregate Builder usage.

---

# 240. It may recommend:

```text
Standard template frequently excludes block X
Quick users often add languages
PDF generation failures increased
```

---

# 241. BLD-ANL-002 — VOP Cannot Auto-Change Templates From Analytics

---

# 242. Human decides template/default changes.

---

# 243. AI in Builder

Baseline Builder itself does not need AI.

---

# 244. BLD-INV-012 — Builder Core Must Work Without AI

---

# 245. Casting AI optional integration

If Builder launched from confirmed Casting analysis:

system may preselect relevant eligible items.

---

# 246. Still:

* recommendation only;
* visitor/admin can adjust;
* Builder eligibility applies;
* no Source mutation.

---

# 247. BLD-AI-001 — AI Cannot Add Ineligible Item

---

# 248. BB Assistant

Not required for public visitor.

Public Builder should not expose unrestricted AI writing interface baseline.

---

# 249. Temporary casting labels are user-authored.

---

# 250. Search inside Builder

Useful for Extended template.

Could search eligible:

```text
skills
projects
training
portfolio items
```

---

# 251. BLD-ELG-004 — Builder Search Index Is Separate Context

---

# 252. It must not return ordinary Public-only items if Builder permission false.

---

# 253. Search result still revalidated on selection/Generate.

---

# 254. Session invalidation on permission change

When Admin flips:

```text
allow_in_public_questionnaire_builder = false
```

current active sessions may still contain selected ID.

---

# 255. Required behavior

Current Generate must reject/remove it.

---

# 256. Preview should refresh when practical.

---

# 257. BLD-INV-013 — Permission Revocation Beats Session Cache

---

# 258. Cache strategy

Cache Builder eligibility projection carefully.

---

# 259. Cache key must include

```text
profile
Builder policy/version
locale
projection generation
```

---

# 260. Session preview cache additionally includes:

```text
session version/config
```

---

# 261. Strong revocation

Fresh access/eligibility check before serving sensitive Builder-only values where required.

---

# 262. BLD-SEC-009 — Stale Cache Cannot Leak Revoked Builder Item

---

# 263. Search stale candidate

Rehydrate/revalidate current Builder permission.

---

# 264. Admin readiness

Admin should be able to preview Builder exactly as visitor.

---

# 265. Required Admin tool

```text
Preview Public Builder
```

---

# 266. Preview must use actual Builder Projection.

---

# 267. BLD-INV-014 — Admin Preview Cannot Use Admin DTO

---

# 268. Admin readiness dashboard

Should show:

```text
Builder enabled
Quick ready
Standard ready
Extended ready
Casting ready
mandatory Close-Up available
mandatory Full Body available
official URL available
eligible Contacts
eligible Video
eligible Emotional Grid
```

---

# 269. Template readiness contextual

Quick may be READY while Standard warns due missing Emotional Grid.

---

# 270. BLD-RDY-001 — Readiness Is Per Template

---

# 271. Hard baseline Builder blockers

```text
no Actor identification
no Builder-eligible Main Portfolio Close-Up
no Builder-eligible Main Portfolio Full Body
no official Profile URL
```

---

# 272. Template-specific blockers

Example Standard:

```text
required configured Emotional block unavailable
```

only if Admin configured Emotional as required.

---

# 273. Optional missing content

Warning, not blocker.

---

# 274. Readiness codes

Suggested:

```text
BUILDER_PROFILE_NOT_READY
BUILDER_CLOSE_UP_MISSING
BUILDER_FULL_BODY_MISSING
BUILDER_PROFILE_URL_MISSING
BUILDER_TEMPLATE_DISABLED
BUILDER_TEMPLATE_NOT_READY
BUILDER_CONTACT_MISSING
BUILDER_SELECTED_ITEM_REVOKED
BUILDER_SELECTED_ITEM_UNAVAILABLE
BUILDER_GRID_NOT_READY
BUILDER_LINK_INVALID
BUILDER_QR_TARGET_INVALID
BUILDER_SESSION_EXPIRED
BUILDER_SESSION_STALE
```

---

# 275. Builder session UI

Recommended layout:

```text
Header
├── Actor name
├── Template
└── Progress

Configuration Panel
├── Sections
├── Items
└── Casting context

Preview
├── Desktop
└── Mobile

Footer
├── Generate PDF
└── Reset / Start Over
```

---

# 276. Floating session indicator

Canonical requirement:

> Builder should provide a clear persistent indication that a temporary custom questionnaire is being configured.

---

# 277. Example

```text
Ваша версия анкеты · Standard
```

---

# 278. Reset

Explicitly clears/reinitializes temporary configuration.

---

# 279. Start Over

May create new session rather than mutating old generated snapshot.

---

# 280. Session recovery

Browser refresh should restore active session where cookie/storage/session identity remains valid.

---

# 281. BLD-SES-005 — Browser Refresh Should Not Randomly Lose Valid Session

---

# 282. Do not store professional data Source copies in localStorage.

Store only opaque session reference/minimal UI state where practical.

---

# 283. BLD-SEC-010 — Browser Storage Is Not Professional Data Store

---

# 284. Session sharing

Baseline session itself should not be casually shareable/editable through URL unless explicitly designed.

Generated snapshot can have share semantics.

---

# 285. BLD-SES-006 — Editing Session and Generated Share Are Separate Capabilities

---

# 286. Reset vs Generate

Generate freezes current state.

Later Reset does not mutate generated Snapshot.

---

# 287. Multiple generations

One session may intentionally produce multiple snapshots if configuration changes.

---

# 288. Each intentional generation uses new idempotency key.

---

# 289. Snapshot lineage

May store:

```text
builder_session_id
generation_number?
```

for provenance.

---

# 290. But generation number is not required business identity.

---

# 291. Expiration model

Session:

```text
short-lived
```

Snapshot/artifact:

```text
separate retention policy
```

---

# 292. BLD-SES-007 — Do Not Couple Session TTL and Artifact TTL Blindly

---

# 293. Retention

Anonymous unused sessions can be purged.

---

# 294. Generated snapshots

Retain according to configured professional sharing/privacy policy.

---

# 295. Artifact purge

Derived PDF may be purged independently if Snapshot retained and regeneration allowed.

---

# 296. However

If generated snapshot includes a contact later privacy-revoked, access/redaction policy can override.

---

# 297. BLD-SNP-005 — Snapshot History Does Not Guarantee Permanent Public Availability

---

# 298. Privacy deletion

Copy discovery includes:

```text
BuilderGenerationSnapshot
Builder PDF
QR artifacts
shared links
analytics references
Feedback provenance
```

---

# 299. BLD-SEC-011 — Privacy Revocation Applies to Generated Builder Artifacts

---

# 300. No silent substitution

If old snapshot contains Phone A and it is privacy-redacted:

do not replace with current Phone B.

---

# 301. BLD-SNP-006 — Historical Contact Is Never Rewritten to Current Contact

---

# 302. Generate from prepared questionnaire

Flow:

```text
Prepared Questionnaire
      ↓
Copy configuration proposal
      ↓
Builder Eligibility Filter
      ↓
Remove/disable ineligible items
      ↓
Builder Session
```

---

# 303. BLD-INV-015 — Prepared Snapshot Is Not Builder Authority

---

# 304. Example

Prepared Questionnaire includes:

```text
Project A
```

with:

```text
adminQ=true
builder=false
```

Builder started from it must omit Project A.

---

# 305. UX

Explain:

```text
Некоторые материалы этой версии недоступны для самостоятельной настройки.
```

---

# 306. No security details exposed.

---

# 307. Public Builder + Casting AI

Future enhanced path:

```text
Casting brief
↓
AI analysis
↓
Human-confirmed requirements
↓
recommended Builder template/items
```

---

# 308. But public anonymous Builder should not need Casting AI to function.

---

# 309. Internationalization

Builder UI supports site locales.

---

# 310. Generated questionnaire locale is explicit:

```text
ru
en
```

---

# 311. BLD-LNG-001 — Builder Locale Is Snapshot Dimension

---

# 312. Visitor can choose language where available.

---

# 313. Language switch

Should not create fake translation.

---

# 314. Missing localization

Use documented fallback/warning.

No live AI translation baseline.

---

# 315. BLD-LNG-002 — No Runtime AI Translation During Generate

---

# 316. Casting labels

User-entered labels may be rendered in the chosen language exactly as entered unless user edits them.

System must not invent translation.

---

# 317. Official titles

Preserve approved localized/official values.

---

# 318. Accessibility

Builder itself must support:

* keyboard operation;
* semantic checkboxes;
* labelled controls;
* visible focus;
* non-color status;
* accessible reorder alternative;
* responsive preview.

---

# 319. Generated document follows Questionnaire PDF accessibility rules.

---

# 320. BLD-INV-016 — Accessibility Is Not Optional Because Builder Is Temporary

---

# 321. Mobile Builder

Must allow full base flow:

```text
template
selection
preview
generate
```

without desktop requirement.

---

# 322. Complex drag/drop

Can degrade to Move Up/Down controls.

---

# 323. Performance

Builder should not fetch all full-resolution media.

Use appropriate preview derivatives.

---

# 324. BLD-INV-017 — Builder Preview Uses Web Derivatives

---

# 325. PDF generation

Server/background worker handles high-quality assets.

---

# 326. Progressive loading

Sections/items may load on demand.

---

# 327. No N+1 public leakage

Eligibility Projection should be efficient/structured.

---

# 328. Search/filtering

For large datasets:

```text
filter by Portfolio type
Project
Skill
Training
```

where useful.

---

# 329. Default template selection

Admin-configurable.

---

# 330. Analytics cannot silently alter default.

---

# 331. Template recommendation

VOP may recommend based on aggregate usage.

Human Apply required.

---

# 332. Reset to template

Visitor can reset custom changes to template defaults.

---

# 333. BLD-TPL-005 — Reset Changes Session Only

---

# 334. Public route

Recommended concept:

```text
/questionnaire/builder
```

---

# 335. Casting-specific route

Potential:

```text
/questionnaire/casting/{public-token-or-slug}
```

where it starts a Builder/Casting context.

---

# 336. BLD-SEC-012 — Route Token Scope Is Explicit

Token cannot grant Admin or unrelated profile data.

---

# 337. Multi-profile future

All Builder entities scoped by:

```text
profile_id
```

---

# 338. Same-profile invariant

Every selected Source entity must match BuilderSession profile.

---

# 339. BLD-INV-018 — Cross-Profile Injection Rejected

---

# 340. Public API boundary types

Relevant:

```text
PUBLIC_BUILDER_ACTION
TOKEN_SCOPED_ENDPOINT
ARTIFACT_DELIVERY_ENDPOINT
```

---

# 341. Server Actions

Same-origin session mutations may use Server Actions.

---

# 342. Route Handlers

Preferred for:

* PDF download;
* token-scoped generated artifact;
* external share route.

---

# 343. No direct DB from browser.

---

# 344. BLD-INV-019 — Builder UI Never Calls Database Directly

---

# 345. Commands

Canonical:

```text
CreateBuilderSession
SelectBuilderTemplate
SetBuilderLanguage
EnableBuilderSection
DisableBuilderSection
SelectBuilderItem
UnselectBuilderItem
ReorderBuilderSections
ReorderBuilderItems
UpdateBuilderCastingContext
UpdateBuilderLinkOptions
GeneratePublicQuestionnaire
ResetBuilderSession
```

---

# 346. Queries

```text
GetBuilderTemplates
GetBuilderEligibilityProjection
GetBuilderSession
GetBuilderPreview
GetBuilderReadiness
GetBuilderGeneratedSnapshot
GetBuilderArtifact
```

---

# 347. Create session input

Conceptually:

```text
profileSlug/profileContext
sourceQuestionnaireId?
templateType?
language?
```

---

# 348. Server derives

```text
sessionId
version
expiresAt
eligibility context
```

---

# 349. Session mutation input

Typical:

```text
sessionId
expectedVersion
itemId
selected
```

---

# 350. Browser does not send item professional value.

---

# 351. Generate input

Exactly conceptual:

```text
sessionId
expectedVersion
idempotencyKey
```

---

# 352. BLD-INV-020 — Generate Payload Is Intentionally Small

---

# 353. Why

Server reconstructs authoritative content.

---

# 354. Error taxonomy

At minimum:

```text
BUILDER_NOT_AVAILABLE
BUILDER_TEMPLATE_NOT_FOUND
BUILDER_TEMPLATE_DISABLED
BUILDER_SESSION_NOT_FOUND
BUILDER_SESSION_EXPIRED
BUILDER_SESSION_STALE
BUILDER_ITEM_NOT_FOUND
BUILDER_ITEM_NOT_ELIGIBLE
BUILDER_ITEM_WRONG_PROFILE
BUILDER_SECTION_REQUIRED
BUILDER_CLOSE_UP_MISSING
BUILDER_FULL_BODY_MISSING
BUILDER_PROFILE_URL_MISSING
BUILDER_GRID_NOT_READY
BUILDER_MEDIA_NOT_READY
BUILDER_LINK_INVALID
BUILDER_CONTACT_NOT_AVAILABLE
BUILDER_QR_TARGET_INVALID
BUILDER_GENERATION_CONFLICT
BUILDER_GENERATION_RATE_LIMITED
BUILDER_ARTIFACT_NOT_READY
BUILDER_ARTIFACT_FAILED
BUILDER_ACCESS_REVOKED
```

---

# 355. Public error safety

Do not reveal:

* hidden item names;
* Admin states;
* private contacts;
* source UUID inventory.

---

# 356. Example

Safe:

```text
Один из выбранных материалов больше недоступен.
```

---

# 357. Admin readiness detail can be richer.

---

# 358. Idempotency

Required for Generate.

---

# 359. Session mutation idempotency

Normal versioned checkbox/reorder operations do not necessarily need durable idempotency records.

---

# 360. Snapshot generation

Does.

---

# 361. BLD-INV-021 — Generate Retry ≠ Generate New Version

---

# 362. Regenerate after changes

New intent.

New key.

---

# 363. Audit

Anonymous selection changes are not business Audit.

---

# 364. Audit-worthy events

Admin-side:

```text
Builder template config changed
Builder permission changed
Generated artifact access revoked
```

---

# 365. Public generated snapshot creation

Can be operationally recorded with privacy-safe provenance.

---

# 366. Do not audit every checkbox as professional business change.

---

# 367. Outbox events

Suggested:

```text
BuilderSessionCreated
BuilderGenerationSnapshotCreated
BuilderArtifactRequested
BuilderArtifactReady
BuilderArtifactAccessRevoked
```

---

# 368. Session checkbox mutation

Usually no Outbox required unless cache/render architecture needs it.

---

# 369. Worker jobs

Suggested:

```text
BUILDER_RENDER_HTML
BUILDER_RENDER_PDF
BUILDER_RENDER_QR_SET
BUILDER_CLEAN_EXPIRED_SESSIONS
BUILDER_CLEAN_EXPIRED_ARTIFACTS
```

---

# 370. Worker input

Exact Snapshot ID.

Never current Session for final render.

---

# 371. BLD-INV-022 — Worker Renders Snapshot, Not Mutable Session

---

# 372. Session cleanup

Cleanup job only deletes expired temporary sessions that policy allows.

---

# 373. It must not delete generated Snapshot just because parent Session expired.

---

# 374. BLD-INV-023 — Cleanup Respects Separate Retention Classes

---

# 375. Restore

After backup restore:

* expired sessions remain expired;
* revoked artifacts stay revoked;
* generated snapshot must not trigger automatic re-notification;
* old session must not become active because clock/state restored incorrectly.

---

# 376. BLD-SEC-013 — Restore Must Not Reactivate Expired Public Access

---

# 377. Migration

Since Public Builder is new, migration primarily concerns eligibility policy.

---

# 378. Existing Source data

Every relevant module needs explicit:

```text
allow_in_public_questionnaire_builder
```

migration/default.

---

# 379. BLD-MIG-001 — Never Infer Builder Permission From Public Visibility Automatically

---

# 380. Conservative default

Recommended for new field:

```text
false
```

until reviewed,

except where product migration specification intentionally enables known-safe fields.

---

# 381. Why

Builder is a new disclosure surface.

---

# 382. Mandatory items

Migration/config must explicitly ensure at least:

* eligible Close-Up;
* eligible Full Body;
* identity;
* official Profile URL.

---

# 383. Otherwise Builder remains not-ready.

---

# 384. Prepared Questionnaire migration

No need to clone all prepared Questionnaires into Builder templates.

Templates are independent configuration.

---

# 385. BLD-MIG-002 — Admin Questionnaire Structure Is Not Builder Template Authority

---

# 386. Initial templates

Create canonical:

```text
Quick
Standard
Extended
Casting
```

through seed/config migration.

---

# 387. Migration validation report

Should include:

```text
missing Close-Up permission
missing Full Body permission
no eligible contacts
no eligible video
no Emotional Grid
public data accidentally Builder-enabled
```

---

# 388. No auto-fix of sensitive permission issues.

---

# 389. Anti-patterns

`BLD-AP-001`
Reuse Admin Questionnaire editor publicly.

`BLD-AP-002`
Reuse Admin DTO in Builder.

`BLD-AP-003`
Assume public Site visibility equals Builder eligibility.

`BLD-AP-004`
Assume Admin Questionnaire eligibility equals Builder eligibility.

`BLD-AP-005`
Send hidden profile fields to browser and disable checkbox.

`BLD-AP-006`
Store entire Profile copy inside BuilderSession.

`BLD-AP-007`
Browser submits modified skill level and server trusts it.

`BLD-AP-008`
Browser submits modified phone and server trusts it.

`BLD-AP-009`
Visitor edits Project title.

`BLD-AP-010`
Visitor changes CEFR.

`BLD-AP-011`
Visitor changes shooting date.

`BLD-AP-012`
Visitor crops Emotional Grid.

`BLD-AP-013`
Visitor selects individual Emotional source photos.

`BLD-AP-014`
Role photo substitutes Full Body.

`BLD-AP-015`
Emotional photo substitutes Close-Up.

`BLD-AP-016`
Custom template removes mandatory Profile URL.

`BLD-AP-017`
Visitor gets CSS/theme controls.

`BLD-AP-018`
Builder becomes arbitrary HTML editor.

`BLD-AP-019`
Builder becomes arbitrary URL-to-PDF service.

`BLD-AP-020`
Builder allows arbitrary file uploads baseline.

`BLD-AP-021`
No expiry for anonymous sessions.

`BLD-AP-022`
Session ID is sequential.

`BLD-AP-023`
Session ID alone permits querying arbitrary profile Source.

`BLD-AP-024`
No anti-abuse on PDF generation.

`BLD-AP-025`
Every checkbox write creates heavy PDF immediately.

`BLD-AP-026`
Preview considered immutable snapshot.

`BLD-AP-027`
Generate trusts preview cache.

`BLD-AP-028`
Generate does not reload Source.

`BLD-AP-029`
Generate uses stale permission snapshot without current check.

`BLD-AP-030`
Permission revoked but cached Builder still exposes data.

`BLD-AP-031`
Old Browser professional values accepted as Source.

`BLD-AP-032`
Generated PDF reads current Profile after Snapshot.

`BLD-AP-033`
Session expiry deletes historical generated Snapshot automatically.

`BLD-AP-034`
Reset mutates previously generated PDF.

`BLD-AP-035`
Generated artifact becomes publicly indexable automatically.

`BLD-AP-036`
Raw storage URL returned as PDF link.

`BLD-AP-037`
Token stored in PDF filename.

`BLD-AP-038`
Token-scoped artifact cached globally.

`BLD-AP-039`
Public Builder exposes NotificationRecipients.

`BLD-AP-040`
Builder exposes admin-only contact notes.

`BLD-AP-041`
Casting label creates saved Role automatically.

`BLD-AP-042`
Casting label creates Project automatically.

`BLD-AP-043`
AI auto-adds hidden/ineligible content.

`BLD-AP-044`
AI automatically changes template defaults based on analytics.

`BLD-AP-045`
Runtime AI translates final PDF.

`BLD-AP-046`
Builder requires AI to function.

`BLD-AP-047`
Builder requires registration before basic generation.

`BLD-AP-048`
Desktop-only drag/drop blocks mobile/keyboard users.

`BLD-AP-049`
Full-resolution originals loaded into selection grid.

`BLD-AP-050`
Prepared Questionnaire copied without filtering Builder-ineligible items.

`BLD-AP-051`
Migration turns all public fields into Builder-enabled fields.

`BLD-AP-052`
Migration turns all AdminQ fields into Builder-enabled fields.

`BLD-AP-053`
Generated Snapshot stores entire Contacts table.

`BLD-AP-054`
Privacy revocation substitutes old phone with current phone.

`BLD-AP-055`
Expired session becomes active again after restore.

`BLD-AP-056`
Analytics records raw phone/email/token.

`BLD-AP-057`
Search in Builder uses Public Search index without Builder filter.

`BLD-AP-058`
Artifact retry renders mutable Session instead of Snapshot.

`BLD-AP-059`
Same idempotency key used after session mutation without conflict.

`BLD-AP-060`
Generation creates duplicate snapshot after response retry.

---

# 390. Core invariants

`BLD-INV-024`
Public Builder is separate from prepared Questionnaires.

`BLD-INV-025`
Public Builder supports anonymous baseline use.

`BLD-INV-026`
Builder does not grant Admin access.

`BLD-INV-027`
Builder receives only Builder Eligibility Projection.

`BLD-INV-028`
Builder eligibility is independent from Public visibility.

`BLD-INV-029`
Builder eligibility is independent from Admin Questionnaire eligibility.

`BLD-INV-030`
Browser never receives Builder-ineligible Source merely for UI hiding.

`BLD-INV-031`
Templates are Quick/Standard/Extended/Casting.

`BLD-INV-032`
Template defines defaults, not Source permission.

`BLD-INV-033`
Every generated actor questionnaire includes identification.

`BLD-INV-034`
Every generated actor questionnaire includes eligible Main Portfolio Close-Up.

`BLD-INV-035`
Every generated actor questionnaire includes eligible Main Portfolio Full Body.

`BLD-INV-036`
Every generated actor questionnaire includes official Profile URL.

`BLD-INV-037`
Role/Emotional/Project media cannot substitute mandatory photos.

`BLD-INV-038`
Builder may select optional items only from eligible projection.

`BLD-INV-039`
Builder cannot edit professional Source values.

`BLD-INV-040`
Builder cannot edit media crops.

`BLD-INV-041`
Builder cannot change Skill level.

`BLD-INV-042`
Builder cannot change CEFR/Native.

`BLD-INV-043`
Builder cannot change contact values.

`BLD-INV-044`
Builder cannot change ProfessionalLink target.

`BLD-INV-045`
Builder cannot change shooting date.

`BLD-INV-046`
Builder does not expose individual Emotional source photos.

`BLD-INV-047`
Builder uses approved GridRevision.

`BLD-INV-048`
Builder Session is mutable and temporary.

`BLD-INV-049`
Anonymous session always expires.

`BLD-INV-050`
Session config stores references/configuration rather than Profile copy.

`BLD-INV-051`
Session uses optimistic concurrency.

`BLD-INV-052`
Preview uses current eligible Source plus session configuration.

`BLD-INV-053`
Preview is not historical Snapshot.

`BLD-INV-054`
Generate revalidates session active/expiry.

`BLD-INV-055`
Generate reloads every selected Source entity server-side.

`BLD-INV-056`
Generate validates same-profile ownership.

`BLD-INV-057`
Generate validates current Builder permission.

`BLD-INV-058`
Generate validates mandatory Close-Up/Full Body/Profile URL.

`BLD-INV-059`
Generate is idempotent.

`BLD-INV-060`
Same key + changed session state creates idempotency conflict.

`BLD-INV-061`
BuilderGenerationSnapshot is immutable.

`BLD-INV-062`
Snapshot freezes exact selected professional values.

`BLD-INV-063`
Snapshot is data-minimized.

`BLD-INV-064`
Snapshot may outlive Session.

`BLD-INV-065`
Current Source changes never rewrite Snapshot.

`BLD-INV-066`
PDF/HTML render from Snapshot, not current Source.

`BLD-INV-067`
HTML/PDF share semantic DocumentModel.

`BLD-INV-068`
Full Body framing remains preserved.

`BLD-INV-069`
QR target equals frozen canonical URL.

`BLD-INV-070`
Generated artifacts are not SEO-public by default.

`BLD-INV-071`
Artifact access is opaque/scoped.

`BLD-INV-072`
Raw tokens are absent from logs/filenames/analytics.

`BLD-INV-073`
Anonymous use has anti-abuse controls.

`BLD-INV-074`
Builder is not arbitrary file/URL processing service.

`BLD-INV-075`
Builder has no visual design/CSS authority.

`BLD-INV-076`
Casting labels are temporary untrusted presentation text.

`BLD-INV-077`
Casting labels do not create Project/Role/Casting entities.

`BLD-INV-078`
Builder Contact projection excludes NotificationRecipients.

`BLD-INV-079`
Prepared Questionnaire seed is re-filtered by Builder permissions.

`BLD-INV-080`
Permission revocation beats active session selection/cache.

`BLD-INV-081`
Builder Search is Builder-context search.

`BLD-INV-082`
VOP analytics recommendations cannot auto-change Builder templates.

`BLD-INV-083`
Builder core works without AI.

`BLD-INV-084`
Runtime AI is not used to translate generated documents.

`BLD-INV-085`
Builder supports mobile/keyboard core workflows.

`BLD-INV-086`
Browser storage does not become professional Source store.

`BLD-INV-087`
Editing Session and generated Snapshot share are separate capabilities.

`BLD-INV-088`
Reset/expiry never mutate existing Snapshot.

`BLD-INV-089`
Session retention and Snapshot retention are separate.

`BLD-INV-090`
Privacy/access revocation can restrict generated Snapshot delivery.

`BLD-INV-091`
Historical Snapshot values are never silently substituted with current Source.

`BLD-INV-092`
Cross-profile item injection is rejected.

`BLD-INV-093`
Builder UI never accesses DB directly.

`BLD-INV-094`
Worker renders exact immutable Snapshot.

`BLD-INV-095`
Artifact failure does not delete Snapshot.

`BLD-INV-096`
Session cleanup does not blindly delete retained generated artifacts.

`BLD-INV-097`
Restore does not reactivate expired/revoked access.

`BLD-INV-098`
Migration requires explicit Builder-permission policy.

`BLD-INV-099`
Builder permission is not inferred blindly from Public/AdminQ flags.

`BLD-INV-100`
All privacy/generation/eligibility rules are enforced server-side.

---

# 391. E2E-BLD-001 — Anonymous open

Anonymous visitor opens Builder.

No registration required.

---

# 392. E2E-BLD-002 — Session creation

Opaque temporary session created with expiry/version.

---

# 393. E2E-BLD-003 — Quick template

Quick defaults load required content.

---

# 394. E2E-BLD-004 — Standard template

Standard includes configured Emotional/Projects/Training defaults where eligible.

---

# 395. E2E-BLD-005 — Extended

All current eligible block categories available.

---

# 396. E2E-BLD-006 — Casting

Casting/Role temporary labels accepted as plain bounded text.

---

# 397. E2E-BLD-007 — Casting script injection

HTML/script-like input rendered safely as text.

---

# 398. E2E-BLD-008 — Public-hidden Builder-eligible

Item:

```text
public=false
builder=true
```

appears in Builder, not normal Public Profile.

---

# 399. E2E-BLD-009 — Public-visible Builder-disabled

Item:

```text
public=true
builder=false
```

does not appear in Builder.

---

# 400. E2E-BLD-010 — AdminQ-only

```text
adminQ=true
builder=false
```

does not appear in Builder.

---

# 401. E2E-BLD-011 — Hidden payload

Inspect Builder response.

Ineligible private item is absent, not disabled client-side.

---

# 402. E2E-BLD-012 — Mandatory Close-Up

Visitor cannot disable required Close-Up.

---

# 403. E2E-BLD-013 — Mandatory Full Body

Cannot disable.

---

# 404. E2E-BLD-014 — Role image substitute

Client attempts to submit RoleMedia ID as Full Body.

Rejected.

---

# 405. E2E-BLD-015 — Emotional substitute

Emotional image cannot satisfy Close-Up.

---

# 406. E2E-BLD-016 — Alternative Close-Up

Visitor selects another eligible CLOSE_UP.

Accepted.

---

# 407. E2E-BLD-017 — Wrong category

Waist image submitted as Close-Up.

Rejected.

---

# 408. E2E-BLD-018 — Portfolio tamper

Visitor sends title/category change.

Server ignores/rejects.

---

# 409. E2E-BLD-019 — Skill selection

Eligible skill can be included/excluded.

---

# 410. E2E-BLD-020 — Skill level tamper

Saved 3/5, browser submits 5/5.

Preview/Generate uses 3/5.

---

# 411. E2E-BLD-021 — Language tamper

Saved B2, browser submits C2.

Generate uses B2.

---

# 412. E2E-BLD-022 — Emotional Grid

Only approved finalized eligible Grid appears.

---

# 413. E2E-BLD-023 — Grid editing attempt

Visitor sends custom crop/cells.

Rejected/ignored.

---

# 414. E2E-BLD-024 — Emotional shooting date

Cannot be changed by visitor.

---

# 415. E2E-BLD-025 — Project selection

Eligible Project selected.

Saved title/Role remain authoritative.

---

# 416. E2E-BLD-026 — Training

Appears as standalone Training.

---

# 417. E2E-BLD-027 — Link selection

Eligible ProfessionalLink can be selected.

---

# 418. E2E-BLD-028 — URL tamper

Browser submits different URL.

Generate uses saved canonical URL.

---

# 419. E2E-BLD-029 — QR option

Visitor enables allowed QR.

Generated QR decodes to exact frozen link.

---

# 420. E2E-BLD-030 — Contact

Builder receives only Builder-eligible ContactMethods.

---

# 421. E2E-BLD-031 — Contact tamper

Phone changed client-side.

Generate reloads Source.

---

# 422. E2E-BLD-032 — Notification privacy

NotificationRecipient never appears as Builder Contact option.

---

# 423. E2E-BLD-033 — Live desktop preview

Selection change reflected.

---

# 424. E2E-BLD-034 — Mobile preview

Same semantic content in mobile representation.

---

# 425. E2E-BLD-035 — Reorder

User reorders optional sections.

Profile Source order remains unchanged.

---

# 426. E2E-BLD-036 — Keyboard reorder

Move up/down works without drag interaction.

---

# 427. E2E-BLD-037 — Refresh

Valid active session is restored after browser refresh.

---

# 428. E2E-BLD-038 — Expired session

Mutation returns:

```text
BUILDER_SESSION_EXPIRED
```

---

# 429. E2E-BLD-039 — Expired Generate

Blocked.

---

# 430. E2E-BLD-040 — Concurrent tabs

Stale mutation rejected rather than silently overwriting newer session version.

---

# 431. E2E-BLD-041 — Permission revoke during session

Selected Project becomes Builder-disabled.

Generate rejects/removes it.

---

# 432. E2E-BLD-042 — Mandatory permission revoke

Selected mandatory Full Body becomes disabled.

Generate blocks entirely.

---

# 433. E2E-BLD-043 — Generate server reload

Current profile height changed after preview.

Generate uses current Source value.

---

# 434. E2E-BLD-044 — Generate snapshot

Immutable Snapshot created with exact selection.

---

# 435. E2E-BLD-045 — Double Generate

Same session state + same idempotency key creates one Snapshot.

---

# 436. E2E-BLD-046 — Response lost

Retry same key returns same Snapshot.

---

# 437. E2E-BLD-047 — Edit then old key

Session changes after generation attempt.

Using old key returns idempotency conflict.

---

# 438. E2E-BLD-048 — New generation

Changed session + new key creates new Snapshot.

---

# 439. E2E-BLD-049 — Snapshot history

Profile value changes after Generate.

Old Snapshot remains unchanged.

---

# 440. E2E-BLD-050 — PDF render

PDF uses exact Snapshot.

---

# 441. E2E-BLD-051 — Current Source later archived

Historical Snapshot does not switch to alternative current entity.

---

# 442. E2E-BLD-052 — Full Body PDF

Complete figure preserved.

---

# 443. E2E-BLD-053 — Emotional PDF

Contains Grid composite + shooting date + full portfolio link.

---

# 444. E2E-BLD-054 — Link PDF

Actual clickable link preserved.

---

# 445. E2E-BLD-055 — QR PDF

Vector/high-resolution QR decodes correctly.

---

# 446. E2E-BLD-056 — Artifact failure

Snapshot remains valid if renderer fails.

---

# 447. E2E-BLD-057 — Artifact retry

Retry uses exact Snapshot.

---

# 448. E2E-BLD-058 — Session expiry after generation

Generated Snapshot remains available according to its own access/retention policy.

---

# 449. E2E-BLD-059 — Reset

Reset session does not modify previous Snapshot.

---

# 450. E2E-BLD-060 — No indexing

Generated anonymous PDF/share is absent from sitemap/Public Search by default.

---

# 451. E2E-BLD-061 — Token scope

Generated token opens exact artifact only.

---

# 452. E2E-BLD-062 — Token enumeration

Another artifact cannot be accessed by modifying ID.

---

# 453. E2E-BLD-063 — Raw token logging

Logs/analytics do not contain raw access token.

---

# 454. E2E-BLD-064 — Rate limit

Abusive generation rate returns typed rate-limit response without corrupting session.

---

# 455. E2E-BLD-065 — Arbitrary URL attempt

Visitor cannot inject external URL into PDF.

---

# 456. E2E-BLD-066 — Arbitrary upload

No unsupported public upload endpoint exists through Builder baseline.

---

# 457. E2E-BLD-067 — Start from prepared QNR

Prepared questionnaire configuration is imported as proposal.

---

# 458. E2E-BLD-068 — Prepared item Builder-disabled

Item is removed/disabled after Builder filtering.

---

# 459. E2E-BLD-069 — Post-generation Casting CTA

Feedback/Casting flow receives Snapshot provenance.

---

# 460. E2E-BLD-070 — Feedback provenance

Inquiry links to Builder Snapshot without copying professional facts again.

---

# 461. E2E-BLD-071 — Analytics

Required events emitted without raw contact/private values.

---

# 462. E2E-BLD-072 — VOP analytics

VOP creates recommendation only; template remains unchanged.

---

# 463. E2E-BLD-073 — AI disabled

Full Builder workflow remains functional.

---

# 464. E2E-BLD-074 — Locale

RU/EN generation preserves explicit locale.

---

# 465. E2E-BLD-075 — Missing translation

Document follows defined fallback/readiness policy, not runtime AI generation.

---

# 466. E2E-BLD-076 — Admin preview

Admin “Preview Builder” sees same eligible dataset as visitor.

---

# 467. E2E-BLD-077 — Admin DTO leak

Admin-only field absent from preview payload.

---

# 468. E2E-BLD-078 — Search Builder

Builder Search returns Builder-eligible Skill.

---

# 469. E2E-BLD-079 — Search Public-only

Public-visible but Builder-disabled item absent.

---

# 470. E2E-BLD-080 — Search stale

Revoked Builder item filtered during current revalidation.

---

# 471. E2E-BLD-081 — Cache revocation

Cached Builder projection does not expose newly revoked item.

---

# 472. E2E-BLD-082 — Privacy revoke generated contact

Old generated artifact can be access-revoked/redacted per policy.

It is not rewritten with current phone.

---

# 473. E2E-BLD-083 — Restore expired session

Backup restore does not make expired session active again.

---

# 474. E2E-BLD-084 — Cleanup

Expired session is deleted by retention job while retained generated Snapshot remains.

---

# 475. E2E-BLD-085 — Migration permissions

Legacy Public item does not automatically become Builder-enabled.

---

# 476. E2E-BLD-086 — Builder readiness

Without Builder-enabled Close-Up, Admin readiness reports hard blocker.

---

# 477. Architecture diagram

```text
                MASTER SOURCES
 Profile / Portfolio / Skills / Languages
 Emotional / Projects / Training / Links / Contacts
                         │
                         ▼
              BUILDER ELIGIBILITY
                   PROJECTION
                         │
                         ▼
                  BuilderSession
                         │
               User Configuration
                         │
                         ▼
                   Live Preview
                         │
                         ▼
                    GENERATE
                         │
                         ▼
               Current Revalidation
                         │
                         ▼
          BuilderGenerationSnapshot
                         │
                  ┌──────┴──────┐
                  ▼             ▼
                HTML            PDF
```

---

# 478. Permission diagram

```text
Source Item
   │
   ├── Public Site = false
   ├── Admin Questionnaire = true
   └── Public Builder = true
                │
                ▼
      Available in Builder
      but not ordinary Site

All three permissions are independent.
```

---

# 479. Template flow

```text
Open Builder
     ↓
Choose Template
 ┌────┼──────┬────────┐
 ▼    ▼      ▼        ▼
Quick Standard Extended Casting
     ↓
Adjust Eligible Content
     ↓
Preview
     ↓
Generate
```

---

# 480. Mandatory-content diagram

```text
Identification ──────────────┐
Main Portfolio Close-Up ─────┤
Main Portfolio Full Body ────┤
Official Profile URL ────────┤
                             ▼
                    Valid Generation

Role / Project / Emotional images
cannot replace mandatory Portfolio media.
```

---

# 481. Session vs Snapshot diagram

```text
BuilderSession
mutable
temporary
expires
     │
     ▼
GENERATE
     │
     ▼
BuilderGenerationSnapshot
immutable
exact professional representation
separate retention
```

---

# 482. Prepared Questionnaire integration

```text
Prepared Questionnaire
       ↓
«Настроить под себя»
       ↓
Copy Configuration Intent
       ↓
Apply Builder Eligibility
       ↓
BuilderSession
```

---

# 483. Contact/Casting continuation

```text
Generated Questionnaire
         ↓
   Casting CTA
         ↓
 Feedback / Casting Inquiry
         ↓
 Provenance:
 Snapshot / Session reference
```

---

# 484. Privacy gate

```text
Builder Request
      ↓
Session Context
      ↓
Builder Permission
      ↓
Current Lifecycle
      ↓
Same Profile
      ↓
Safe Projection
      ↓
Browser
```

---

# 485. Quality gate

Перед implementation должны быть подтверждены:

* [ ] anonymous baseline Builder;
* [ ] Builder-specific Eligibility Projection;
* [ ] independent Builder permission on all relevant Source types;
* [ ] Quick template;
* [ ] Standard template;
* [ ] Extended template;
* [ ] Casting template;
* [ ] progressive template-first configuration;
* [ ] mandatory identification;
* [ ] mandatory Main Portfolio Close-Up;
* [ ] mandatory Main Portfolio Full Body;
* [ ] mandatory official Profile URL;
* [ ] item-level selection;
* [ ] optional ordering;
* [ ] no visual design controls;
* [ ] temporary BuilderSession;
* [ ] session version;
* [ ] expiration;
* [ ] safe anonymous session identity;
* [ ] browser refresh recovery;
* [ ] desktop preview;
* [ ] mobile preview;
* [ ] Profile field Builder policy;
* [ ] Portfolio Builder policy;
* [ ] Emotional approved-Grid-only rule;
* [ ] Project/Training/Skill/Language selection;
* [ ] Link/QR representation controls;
* [ ] Contacts Builder privacy;
* [ ] Generate server-side full revalidation;
* [ ] Generate idempotency;
* [ ] immutable BuilderGenerationSnapshot;
* [ ] data-minimized snapshot;
* [ ] HTML/PDF semantic parity;
* [ ] Full Body framing;
* [ ] exact GridRevision;
* [ ] stable profile/media links;
* [ ] scoped artifact access;
* [ ] anti-abuse/rate limits;
* [ ] no arbitrary URL/file processing;
* [ ] Admin Builder templates/config;
* [ ] readiness per template;
* [ ] start from prepared Questionnaire;
* [ ] Casting/Feedback provenance;
* [ ] analytics events;
* [ ] VOP recommendation-only behavior;
* [ ] Search context separation;
* [ ] cache/revocation behavior;
* [ ] retention separation Session/Snapshot;
* [ ] privacy redaction;
* [ ] migration permission review;
* [ ] accessibility;
* [ ] complete E2E coverage.

---

# 486. Acceptance criteria

`AC-BLD-001`
Public Builder работает без обязательной регистрации.

`AC-BLD-002`
Builder является отдельным domain от prepared Questionnaires.

`AC-BLD-003`
Builder использует отдельную server-side Eligibility Projection.

`AC-BLD-004`
`show_on_public_site`, `allow_in_admin_questionnaires` и `allow_in_public_questionnaire_builder` независимы.

`AC-BLD-005`
Builder-ineligible data отсутствуют из browser payload.

`AC-BLD-006`
Поддерживаются Quick, Standard, Extended и Casting templates.

`AC-BLD-007`
Configuration начинается с выбора template.

`AC-BLD-008`
Builder позволяет выбирать отдельные разрешённые items.

`AC-BLD-009`
Builder не позволяет редактировать professional Source values.

`AC-BLD-010`
Builder не предоставляет visual/CSS document editor.

`AC-BLD-011`
Каждая generated Questionnaire содержит Actor identification.

`AC-BLD-012`
Каждая generated Questionnaire содержит Main Portfolio Close-Up.

`AC-BLD-013`
Каждая generated Questionnaire содержит Main Portfolio Full Body.

`AC-BLD-014`
Каждая generated Questionnaire содержит official Profile URL.

`AC-BLD-015`
Project/Role/Emotional media cannot substitute mandatory Portfolio photos.

`AC-BLD-016`
Builder uses temporary versioned session.

`AC-BLD-017`
Anonymous session expires.

`AC-BLD-018`
Session stores configuration/references rather than full Profile copy.

`AC-BLD-019`
Live Preview supports Desktop and Mobile.

`AC-BLD-020`
Preview does not become immutable professional history.

`AC-BLD-021`
Builder cannot modify Skill level or Language proficiency.

`AC-BLD-022`
Builder cannot modify Emotional Grid composition or shooting date.

`AC-BLD-023`
Builder Emotional block uses approved GridRevision only.

`AC-BLD-024`
Builder cannot modify Project/Training professional facts.

`AC-BLD-025`
Builder cannot change ProfessionalLink URL.

`AC-BLD-026`
Builder cannot change Contact values.

`AC-BLD-027`
Builder does not expose Admin NotificationRecipients.

`AC-BLD-028`
Generate reloads selected Source data server-side.

`AC-BLD-029`
Generate revalidates same-profile ownership.

`AC-BLD-030`
Generate revalidates current Builder permission for every selected entity.

`AC-BLD-031`
Generate revalidates mandatory Close-Up, Full Body and Profile URL.

`AC-BLD-032`
Browser-submitted professional values cannot override Source.

`AC-BLD-033`
Generate is idempotent.

`AC-BLD-034`
Same idempotency key with changed Session state returns conflict.

`AC-BLD-035`
Generate creates immutable BuilderGenerationSnapshot.

`AC-BLD-036`
Snapshot freezes exact selected professional representation.

`AC-BLD-037`
Snapshot is data-minimized.

`AC-BLD-038`
Current Source changes do not rewrite generated Snapshot.

`AC-BLD-039`
Generated HTML/PDF render from Snapshot.

`AC-BLD-040`
Generated Full Body framing is preserved.

`AC-BLD-041`
Generated Emotional block uses exact composite/date/link.

`AC-BLD-042`
QR targets exact frozen canonical URL.

`AC-BLD-043`
Generated documents are not globally SEO-public by default.

`AC-BLD-044`
Artifact access is opaque/scoped and does not expose raw storage locators.

`AC-BLD-045`
Raw bearer tokens are excluded from logs, analytics and filenames.

`AC-BLD-046`
Anonymous Builder has anti-abuse and rate limiting.

`AC-BLD-047`
Builder is not arbitrary URL-to-PDF or file-processing service.

`AC-BLD-048`
Starting from prepared Questionnaire re-applies Builder eligibility to every item.

`AC-BLD-049`
Builder permissions revoked during session are enforced before Generate.

`AC-BLD-050`
Builder Search respects Builder eligibility rather than ordinary Public visibility.

`AC-BLD-051`
Admin can preview Builder using real Builder Projection.

`AC-BLD-052`
Admin Builder readiness identifies missing mandatory Builder-eligible photos.

`AC-BLD-053`
Generated document can lead to Casting/Feedback workflow with Snapshot provenance.

`AC-BLD-054`
Required Builder analytics events are emitted without unnecessary PII.

`AC-BLD-055`
VOP may recommend template/default improvements but cannot apply them autonomously.

`AC-BLD-056`
Builder core works without AI.

`AC-BLD-057`
No runtime AI translation is required for generation.

`AC-BLD-058`
Builder is usable on mobile and keyboard-only workflows.

`AC-BLD-059`
Session expiry/reset never mutates generated Snapshot.

`AC-BLD-060`
Session retention and Snapshot retention remain independent.

`AC-BLD-061`
Privacy/access revocation can restrict historical generated artifact delivery without substituting current data.

`AC-BLD-062`
Worker renders exact Snapshot, not mutable Session.

`AC-BLD-063`
Artifact failure does not delete Snapshot.

`AC-BLD-064`
Backup restore does not reactivate expired/revoked public access.

`AC-BLD-065`
Migration does not infer Builder permission blindly from Public/AdminQ flags.

`AC-BLD-066`
All permission, tampering, session, idempotency, privacy and artifact rules have deterministic E2E tests.

---

# 487. Финальная доктрина

> **Public Questionnaire Builder является самостоятельным public self-service capability для casting specialist, но не публичным Admin и не альтернативным Master Source. Пользователь без обязательной регистрации выбирает один из шаблонов Quick / Standard / Extended / Casting, после чего настраивает только состав и порядок разрешённых материалов. Все данные поступают исключительно из отдельной server-side Builder Eligibility Projection, основанной на независимом `allow_in_public_questionnaire_builder`; Public Site и Admin Questionnaire permissions не дают Builder-доступ автоматически. Каждая generated actor questionnaire неизменно содержит identification, eligible Main Portfolio Close-Up, eligible Main Portfolio Full Body и official Profile URL. На Generate система повторно загружает и валидирует все Source entities, игнорирует любые подменённые browser values и атомарно создаёт immutable BuilderGenerationSnapshot. HTML/PDF/QR затем генерируются только из этого Snapshot. Builder не может изменять Skills, CEFR, Contacts, Project credits, shooting dates, Grid crops, URLs или иные профессиональные факты; он конфигурирует presentation, а не профессиональную истину.**
