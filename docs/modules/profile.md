# ACTOR PROFILE MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация Master Profile, профессиональных фактов, локализации, Biography, Hero и Quick Facts

**Целевой файл:** `docs/modules/profile.md`  
**Документ:** DOC-110  
**Статус:** ✅ Completed  
**Тип:** Module / Profile / Master Data / Professional Facts / Visibility

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

---

# 1. Назначение модуля

Actor Profile — центральный Master Data модуль профессионального профиля актрисы.

Его задача:

> **хранить подтверждённые профильные факты один раз и предоставлять их всем остальным модулям через контекстные projections, visibility policies и immutable snapshots.**

Модуль является владельцем:

- профессионального имени;
- профессионального заголовка;
- города/базовой локации;
- базовой идентификации профиля;
- профильных физических параметров;
- подтверждённых описательных характеристик;
- Biography;
- локализаций Biography;
- field-level visibility профильных scalar facts;
- публичного slug;
- lifecycle Actor Profile.

---

# 2. Главная доктрина

```text
PROFILE FACT
    ↓
ONE AUTHORITATIVE OWNER
    ↓
PROFILE MODULE
    ↓
CONTEXT PROJECTIONS
    ├─ Public Site
    ├─ Questionnaire
    ├─ Public Builder
    ├─ Casting AI
    ├─ BB Assistant
    ├─ Search
    └─ Historical Snapshot
```

> **Профильный факт не копируется как отдельная изменяемая истина в Questionnaire, Casting, Public Builder, Hero или другой модуль.**

---

# 3. Module identifiers

Используются:

```text
PROF-*
PROF-ID-*
PROF-PAR-*
PROF-BIO-*
PROF-VIS-*
PROF-HERO-*
PROF-QF-*
PROF-LOC-*
PROF-AI-*
PROF-INV-*
PROF-AP-*
E2E-PROF-*
```

---

# 4. Business purpose

Профиль должен позволять Casting Director / Producer / Coordinator быстро ответить на вопросы:

1. Кто эта актриса?
2. Где она находится?
3. Какие основные профессиональные параметры доступны?
4. На каких языках говорит?
5. Какие ключевые навыки есть?
6. Где открыть видео?
7. Где получить актёрскую анкету?
8. Как связаться?

При этом Profile Module владеет только своими фактами.

Skills, Languages, Contacts, Media и Portfolio остаются отдельными доменами.

---

# 5. Profile ≠ Homepage

`ActorProfile` — Source Aggregate.

Homepage — Projection.

---

# 6. Profile ≠ Questionnaire

Questionnaire — configured/snapshot representation профиля и других модулей.

---

# 7. Profile ≠ Casting Profile Copy

Casting использует текущие подтверждённые Profile facts или frozen analysis snapshot.

Не создаёт параллельную копию параметров актрисы.

---

# 8. Profile ≠ AI Draft

AI-generated biography является `AIDraft`.

Пока Human Apply не выполнен:

```text
AI Draft != Profile Biography
```

---

# 9. Profile aggregate

Canonical components:

```text
ActorProfile
├── ActorProfileParameters
├── ActorProfileLocalizations
└── ProfileFieldPolicies
```

Связанные, но не принадлежащие Profile module как Source:

```text
Portfolio
Skills
Languages
Contacts
Training
Achievements
Projects
Professional Links
```

---

# 10. Physical data model

Основные таблицы:

```text
actor_profiles
actor_profile_parameters
actor_profile_localizations
profile_field_policies
```

---

# 11. `actor_profiles`

Владеет:

```text
professional_name
professional_title
city
country_code
official_profile_slug
status
version
created_at
updated_at
```

---

# 12. `actor_profile_parameters`

Владеет профильными scalar facts, например:

```text
date_of_birth
height_cm
shoe_size_eu
clothing_size
hair_color
eye_color
voice_type
additional_parameters
```

---

# 13. `actor_profile_localizations`

Владеет:

```text
locale
short_biography
biography
```

---

# 14. `profile_field_policies`

Владеет visibility policy scalar facts.

Для каждого разрешённого `field_key`:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 15. PROF-INV-001 — One Fact, One Owner

`height_cm` хранится только как Profile fact.

Запрещено иметь независимые mutable copies:

```text
questionnaire.height
casting.height
homepage.height
pdf.height
```

---

# 16. Historical exception

QuestionnaireRevision/BuilderGenerationSnapshot/CastingAnalysisRevision MAY contain frozen copy для истории.

Это:

```text
SNAPSHOT
```

а не второй Master.

---

# 17. PROF-INV-002 — Projection Is Not Source

Hero, Quick Facts и Casting Quick View не имеют собственных mutable profile facts.

---

# 18. Actor Profile lifecycle

Baseline:

```text
DRAFT
ACTIVE
ARCHIVED
```

---

# 19. DRAFT

Профиль:

- редактируется;
- не обязан удовлетворять Public readiness;
- не должен становиться публичным автоматически.

---

# 20. ACTIVE

Профиль является текущим рабочим профессиональным профилем.

---

# 21. ARCHIVED

Профиль больше не участвует в обычной текущей выдаче.

История сохраняется.

---

# 22. PROF-INV-003 — Active ≠ Every Field Public

`status=ACTIVE` не означает, что все параметры публичны.

---

# 23. Professional name

`professional_name`:

- обязательный;
- подтверждённый;
- Public baseline;
- используется в Hero;
- используется в Questionnaire;
- используется в PDF filename;
- используется в Search;
- используется в Casting materials.

---

# 24. Name is not AI-inferred

AI не может заменить имя на:

- сценический вариант;
- транслитерацию;
- сокращение;

без explicit Human Apply/configuration.

---

# 25. Professional title

Examples:

```text
Актриса
Actress
```

или другая подтверждённая профессиональная формулировка.

---

# 26. Localization of title

Если title требует локализации, реализация MAY позже выделить localized representation.

До этого поле должно иметь ясно определённую семантику.

---

# 27. Official Profile Slug

`official_profile_slug` — стабильный public route identifier.

Canonical public URL conceptually:

```text
https://<host>/<slug>
```

или проектный route.

---

# 28. PROF-ID-001 — Slug ≠ ID

FK и ownership используют UUID.

Slug используется для public routing.

---

# 29. Slug validation

Slug:

- URL-safe;
- unique;
- normalized;
- не должен содержать secret;
- не должен быть случайно изменён при обычном редактировании имени.

---

# 30. Slug change

Изменение slug — high-impact operation.

Должно учитывать:

```text
external links
QR
search index
SEO
questionnaire links
historical references
```

---

# 31. Redirect policy

Если slug изменён, future module/SEO implementation SHOULD поддержать controlled redirect со старого published route, если это безопасно и предусмотрено.

---

# 32. City

`city` — подтверждённая профессиональная базовая локация.

---

# 33. City semantics

Не смешивать:

```text
current physical GPS location
professional base city
shooting destination
casting location
```

---

# 34. Country code

Структурированный country identifier.

Display label локализуется Presentation Layer.

---

# 35. Date of birth

`date_of_birth` — optional professional/private fact.

---

# 36. PROF-PAR-001 — DOB Visibility Explicit

Дата рождения не становится Public автоматически.

---

# 37. Age

Возраст SHOULD рассчитываться.

Не хранить как независимый mutable Profile fact.

---

# 38. Why

Stored age становится stale.

---

# 39. Age calculation

Если product view требует возраст:

```text
date_of_birth
+
effective/current date
→ age
```

---

# 40. Historical age

Для исторического Questionnaire может потребоваться age-as-of snapshot generation date.

Если age показывается как число, DocumentModel должен заморозить соответствующий derived result или точный DOB/date basis.

---

# 41. Height

Canonical Source:

```text
height_cm
```

---

# 42. Unit

Centimetres.

Presentation may display:

```text
172 см
172 cm
```

according to locale.

---

# 43. PROF-PAR-002 — Height No Duplicate String Source

Не хранить параллельно:

```text
height = "172 см"
```

как независимую истину.

---

# 44. Shoe size

Canonical value:

```text
shoe_size_eu
```

если используется EU convention.

---

# 45. Clothing size

Хранится как подтверждённое профессиональное значение.

Не пытаться автоматически конвертировать между международными системами без отдельной validated conversion policy.

---

# 46. Hair color

Confirmed descriptive fact.

---

# 47. Eye color

Confirmed descriptive fact.

---

# 48. Voice type

Optional professional attribute.

Не заполнять AI-инференсом без Human Apply.

---

# 49. Additional parameters

`additional_parameters` допускается только для редких структурированных extension fields.

---

# 50. PROF-PAR-003 — No EAV Abuse

`additional_parameters` не должен превращаться в универсальное хранилище всех profile fields.

Часто используемый стабильный факт должен получить typed field/model.

---

# 51. JSON schema

`additional_parameters` требует:

```text
schema version
allowlisted keys
validator
migration policy
```

---

# 52. Unknown value

Неизвестный параметр:

```text
NULL / not specified
```

---

# 53. PROF-PAR-004 — Never Invent Defaults

Запрещено:

```text
missing height → 170
missing hair → brown
missing DOB → current date
```

---

# 54. Profile localization

Minimum target locales:

```text
ru
en
```

если фактическая локализация создана.

---

# 55. PROF-LOC-001 — Translation Must Be Explicitly Saved

Нельзя считать существующей английскую Biography только потому, что AI может перевести её runtime.

---

# 56. Biography

Profile Module владеет двумя уровнями narrative:

```text
short_biography
biography
```

---

# 57. Short biography

Назначение:

- compact Hero/About context;
- quick professional introduction;
- compact questionnaire where needed.

---

# 58. Full biography

Назначение:

- About/Profile page;
- Extended questionnaire;
- professional materials where selected.

---

# 59. PROF-BIO-001 — Biography Is Professional Fact Narrative

Biography может включать только подтверждённые профессиональные сведения или редакционный narrative, основанный на них.

---

# 60. AI generation

BB Assistant MAY draft Biography.

---

# 61. BB flow

```text
Profile facts
+
approved professional context
    ↓
BB Assistant
    ↓
AIDraft
    ↓
Human review/edit
    ↓
ApplyBBDraft
    ↓
Profile UpdateBiography
```

---

# 62. PROF-AI-001 — AI Never Writes Biography Directly

---

# 63. PROF-AI-002 — AI Draft Never Auto-Publishes

Apply changes Profile draft/current content only.

Public exposure remains governed by lifecycle/visibility/publication flow.

---

# 64. AI grounding

BB Biography context may include:

- Profile facts;
- approved Projects/Roles;
- Training;
- Skills;
- Languages;
- Achievements;

when relevant.

---

# 65. Excluded AI context

Biography generation does not require:

```text
private Feedback sender data
unrelated Casting source
OAuth credentials
Notification recipients
Audit internals
```

---

# 66. AI source traceability

AIDraft records:

```text
source_snapshot
source_version_vector
prompt_version
provider/model metadata
```

---

# 67. Stale AIDraft

Если Profile/related facts materially changed:

Draft MAY be marked:

```text
STALE
```

---

# 68. Apply stale Draft

Must warn/block according to impact and require current target version.

---

# 69. Human edited text

Store separately:

```text
generated_text
edited_text
```

---

# 70. PROF-AI-003 — Never Overwrite Original AI Generation

---

# 71. Field-level visibility doctrine

Profile scalar fields require independent permissions.

Canonical:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 72. PROF-VIS-001 — Three Independent Dimensions

No implication among them.

---

# 73. Valid example A

```text
height:
public = true
admin questionnaire = true
builder = true
```

---

# 74. Valid example B

```text
date_of_birth:
public = false
admin questionnaire = true
builder = false
```

---

# 75. Valid example C

```text
shoe_size:
public = false
admin questionnaire = false
builder = true
```

if product/business policy explicitly allows it.

---

# 76. Public hiding

Hidden scalar field MUST be absent from Public DTO.

---

# 77. PROF-VIS-002 — No Client-Side Hiding

Запрещено отправлять hidden DOB/height и потом скрывать CSS.

---

# 78. Field policy allowlist

`field_key` must come from code/domain registry.

---

# 79. Example registry

Conceptually:

```text
date_of_birth
height_cm
shoe_size_eu
clothing_size
hair_color
eye_color
voice_type
city
```

Actual list finalizes implementation.

---

# 80. Professional name/title visibility

Core identity MAY be mandatory public fields rather than ordinary optional policy fields.

---

# 81. Why

Без identity публичный Actor Profile теряет бизнес-смысл.

---

# 82. Core identity

Baseline Public Profile requires:

```text
professional_name
professional_title
official_profile_slug
```

plus active/public profile state.

---

# 83. Quick Facts

Quick Facts — Projection.

---

# 84. PROF-QF-001 — No QuickFacts Table as Master

---

# 85. Quick Facts sources

Can aggregate:

```text
Profile parameters
City
Languages
Selected key Skills
```

according to current visibility policy.

---

# 86. Example Quick Facts Projection

```text
Алматы
Рост 172 см
Русский — родной
English — B2
Верховая езда — 4/5
```

только если corresponding facts permitted.

---

# 87. Quick Facts ordering

Deterministic and UX-driven.

Admin MAY configure selected/highlighted fields later.

---

# 88. Missing Quick Fact

Simply omitted.

Не выводить:

```text
Рост: Не указано
```

на public Hero unless UX intentionally calls for it.

---

# 89. Hero

Hero — Projection/composition.

---

# 90. Hero canonical content

Business target:

```text
main photo
professional name
professional title
quick parameters
city
languages
key skills
CTA: video
CTA: questionnaire
CTA: contact
```

---

# 91. PROF-HERO-001 — Hero Photo Owned by Portfolio/Media

Profile Module не хранит отдельный hero image binary.

---

# 92. Hero primary image

Should resolve from configured/current eligible Main Portfolio image.

Exact selection defined Portfolio/Home module interactions.

---

# 93. Hero CTAs

Profile Module itself does not own:

```text
Video
Questionnaire
Contact
```

It composes availability from respective modules.

---

# 94. PROF-HERO-002 — No Dead CTA

CTA appears enabled only when target action/resource is currently available.

---

# 95. Sticky actions

Canonical:

```text
Видео
Анкета
Связаться
```

are Public Projection/navigation concerns.

---

# 96. Hero mobile priority

Must preserve fastest professional action.

---

# 97. Profile public readiness

Profile can be editable before fully ready.

---

# 98. Baseline Profile Public readiness

At minimum:

```text
professional name
professional title
valid public slug
active profile
public-safe main photo for complete professional experience
```

Photo requirement may be enforced at Site readiness rather than base DB status.

---

# 99. Professional completeness

Broader professional readiness SHOULD assess:

```text
Main Portfolio Close-Up
Main Portfolio Full Body
video
contacts
languages
skills
biography
questionnaire
```

but missing optional/supporting modules should not corrupt Profile Source.

---

# 100. PROF-INV-004 — Readiness Is Derived

No mutable:

```text
profile_is_ready = true
```

as sole authority.

---

# 101. Readiness states

Use common:

```text
READY
BLOCKED
WARNING
PROCESSING
STALE
```

where context requires.

---

# 102. Profile Admin UX

Admin Profile should separate at least:

1. Identity;
2. Parameters;
3. Biography/localizations;
4. Visibility;
5. Readiness/usage preview.

---

# 103. Identity editor

Fields:

```text
professional name
professional title
city
country
slug
```

---

# 104. Parameters editor

Typed fields.

No freeform JSON UI for normal parameters.

---

# 105. Biography editor

Per locale:

```text
short biography
full biography
```

with BB Assistant action.

---

# 106. Visibility editor

Each applicable scalar fact should clearly show:

```text
Сайт
Админ-анкета
Публичный конструктор
```

independently.

---

# 107. PROF-VIS-003 — Visibility UX Must Not Use One “Публично” Toggle

One toggle cannot represent three contexts.

---

# 108. Preview

Admin SHOULD provide Public Profile Preview.

---

# 109. Preview authority

Preview uses same Public Projection rules as actual Public Site.

---

# 110. PROF-INV-005 — Preview Must Not Have Special Hidden-Data Exceptions

Otherwise Admin sees false representation.

---

# 111. Questionnaire integration

Prepared Questionnaire loads Profile facts using:

```text
allow_in_admin_questionnaires
```

---

# 112. Snapshot

At publication:

Profile values are frozen into `QuestionnaireRevision.content_snapshot`.

---

# 113. Current Profile mutation

Does not modify existing QuestionnaireRevision.

---

# 114. Questionnaire stale detection

Changing a Profile fact used by current questionnaire Draft/Published current alias MAY mark related Draft/current material stale for rebuild/review.

---

# 115. PROF-INV-006 — Stale Does Not Rewrite History

---

# 116. Public Builder integration

Builder uses only Profile scalar fields where:

```text
allow_in_public_questionnaire_builder = true
```

and additional context rules pass.

---

# 117. Builder cannot edit Profile

User may choose:

```text
include height
exclude hair color
```

but cannot alter height value.

---

# 118. PROF-INV-007 — Builder Selection ≠ Profile Mutation

---

# 119. Builder generation

Server reloads current eligible Profile facts immediately before Generation Snapshot.

---

# 120. Permission revoked

If Builder permission is removed before Generate:

field cannot enter new Generation Snapshot.

---

# 121. Casting integration

Casting AI may compare confirmed Casting Requirements to relevant confirmed Profile facts.

---

# 122. Profile facts for Casting

Only fact values relevant to selected requirements should enter AI/matching context.

---

# 123. PROF-AI-004 — Casting AI Does Not Modify Profile

---

# 124. Example

Casting source says:

```text
Рост 170–175 см
```

Profile:

```text
height_cm = 172
```

Match uses Profile Source.

---

# 125. Missing height

Result:

```text
NO_CONFIRMED_DATA
```

not guessed match/mismatch.

---

# 126. Casting extraction mismatch

If Casting AI thinks Profile is 171 based on image:

ignored as professional fact.

Only stored Profile fact is authoritative.

---

# 127. BB Assistant integration

BB is the only AI capability specifically authorized to draft Profile biography text.

---

# 128. Theme AI integration

Theme AI receives presentation tokens and visual constraints.

It does not modify Profile facts.

---

# 129. VOP integration

VOP may detect:

```text
missing biography EN
missing parameter metadata
questionnaire stale after Profile update
visibility mismatch
broken public Profile link
```

---

# 130. VOP auto-safe operations

Can trigger deterministic:

```text
projection rebuild
search reindex
cache invalidation
```

when policy allows.

---

# 131. VOP cannot

```text
invent height
change city
write biography
publish DOB
```

automatically.

---

# 132. Search integration

Public Search indexes only Profile fields authorized for Public Search/Public Projection.

---

# 133. Admin Search

May index broader Profile facts according to Admin permission.

---

# 134. Builder Search

May include Builder-eligible fields/items where search UX requires.

---

# 135. PROF-INV-008 — Search Never Becomes Profile Owner

---

# 136. Analytics integration

Analytics may track:

```text
profile_viewed
hero_video_clicked
questionnaire_clicked
contact_started
```

---

# 137. Analytics must not duplicate raw private Profile facts

Do not emit:

```text
date_of_birth
height
private contact
```

just because event happened on Profile.

---

# 138. SEO integration

Public Profile may expose:

- canonical URL;
- professional name;
- title;
- safe biography excerpt;
- public image;
- structured metadata.

Exact SEO rules later DOC-154.

---

# 139. PROF-INV-009 — SEO Cannot Exceed Public Visibility

Hidden facts cannot enter:

```text
meta tags
structured data
OpenGraph
JSON-LD
sitemap metadata
```

---

# 140. Public cache

Profile Public Projection can be cached.

---

# 141. Cache key dimensions

At least:

```text
profile
locale
public generation/version
```

---

# 142. Visibility changes

Must invalidate current Public Projection/cache promptly.

---

# 143. Privacy-first behavior

If Profile visibility state is uncertain:

```text
deny/omit/reload
```

over stale exposure.

---

# 144. Profile update flow

Canonical:

```text
Admin Form
   ↓
Server Action
   ↓
UpdateProfile Command
   ↓
Auth
   ↓
expectedVersion
   ↓
Domain Validation
   ↓
BEGIN
 update Source
 Audit
 Outbox
COMMIT
   ↓
invalidate projections/search/cache
```

---

# 145. Profile parameter update flow

Same pattern.

---

# 146. Visibility update flow

```text
UpdateProfileFieldVisibility
   ↓
validate field_key
   ↓
authorize
   ↓
update policy
   ↓
Audit
   ↓
Outbox
   ↓
Public/Builder projection invalidation
```

---

# 147. Why visibility command is explicit

Changing visibility can be more sensitive than changing ordinary descriptive text.

---

# 148. Biography Apply flow

```text
BB AIDraft
   ↓
Human chooses Apply
   ↓
ApplyBBDraft
   ↓
expected target version
   ↓
Profile UpdateBiography
   ↓
Audit
```

---

# 149. Biography publish implications

If current public biography immediately reflects current Profile source, Apply/Save may affect Public after commit according to lifecycle model.

If editorial Draft/Publish separation is introduced, public pointer must be explicit.

Module-specific implementation should preserve global Save != Publish doctrine where formal publishing is required.

---

# 150. Baseline recommendation

For high-impact narrative content, maintain explicit Admin preview and deliberate save/publish semantics rather than uncontrolled autosave-to-public.

---

# 151. Concurrency

High-impact Profile mutable records use optimistic version.

---

# 152. Update input

Includes:

```text
expectedVersion
```

---

# 153. Concurrent Biography edits

Second stale write receives:

```text
STALE_VERSION
```

---

# 154. Field policy concurrency

Policy update must not silently overwrite another Admin change.

---

# 155. Transaction boundary

Profile scalar update + Audit + Outbox commit together where required.

---

# 156. External side effects

No external AI/Search/Notification call inside Profile DB transaction.

---

# 157. Events

Suggested semantic events:

```text
ProfileIdentityUpdated
ProfileParametersUpdated
ProfileBiographyUpdated
ProfileVisibilityUpdated
ProfileActivated
ProfileArchived
ProfileSlugChanged
```

---

# 158. Event payloads

Minimal references:

```text
profile_id
changed field classes
new source version
correlation_id
```

No unnecessary sensitive values.

---

# 159. Consumers

Possible:

```text
Search reindex
Cache invalidation
Questionnaire stale detection
Builder invalidation
VOP readiness recompute
Analytics technical event
```

---

# 160. Profile slug event

May additionally trigger:

```text
canonical route update
QR/link cache invalidation
SEO invalidation
```

---

# 161. Profile archive

Default:

```text
ACTIVE → ARCHIVED
```

---

# 162. Archive effects

- Public Profile unavailable;
- Search removal;
- Builder unavailable;
- current questionnaire hub unavailable as normal public context;
- history remains Admin-accessible;
- historical Revisions remain stored.

---

# 163. Restore

If supported:

```text
ARCHIVED → DRAFT
```

preferred.

---

# 164. PROF-INV-010 — Restore Does Not Auto-Reopen Public Site

---

# 165. Deletion

Hard delete Actor Profile is exceptional and dependency-heavy.

---

# 166. Dependency graph includes

```text
Media
Portfolio
Emotional
Projects/Roles
Training
Skills/Languages
Contacts
Questionnaires
Builder snapshots
Feedback
Castings
Opportunities
AI
Themes
Audit
```

---

# 167. Baseline

Archive rather than delete.

---

# 168. Profile field removal/privacy redaction

If a sensitive Profile fact must be deleted:

copy discovery must include:

```text
Questionnaire snapshots
Builder snapshots
AI snapshots/drafts
PDF artifacts
Search
Cache
Analytics/logs where applicable
```

---

# 169. Commands — Profile

Canonical command catalogue:

```text
CreateActorProfile
UpdateProfileIdentity
UpdateProfileParameters
UpdateProfileLocalization
UpdateProfileBiography
UpdateProfileFieldVisibility
ChangeProfileSlug
ActivateProfile
ArchiveProfile
RestoreProfile
```

---

# 170. Query catalogue — Profile

```text
GetAdminProfile
GetAdminProfileReadiness
GetPublicProfile
GetPublicHero
GetPublicQuickFacts
GetProfileFieldPolicies
PreviewPublicProfile
```

---

# 171. CreateActorProfile

Creates:

```text
actor_profiles
default parameter container where required
default field policies
```

---

# 172. Default public permissions

Conservative.

Except mandatory public identity, optional scalar fields SHOULD default non-public.

---

# 173. Builder permissions default

```text
false
```

unless explicitly documented otherwise.

---

# 174. Questionnaire permissions default

Can be true for normal professional fields where established business policy allows, but privacy-sensitive fields default conservative.

---

# 175. UpdateProfileIdentity input

Conceptually:

```text
profileId
expectedVersion
professionalName
professionalTitle
city?
countryCode?
```

---

# 176. ChangeProfileSlug

Separate command because of external-link impact.

---

# 177. UpdateProfileParameters

Typed explicit fields.

No arbitrary:

```text
Record<string, any>
```

for core parameters.

---

# 178. UpdateProfileBiography

Includes:

```text
locale
shortBiography?
biography?
expectedVersion
```

---

# 179. UpdateProfileFieldVisibility

Includes:

```text
fieldKey
showOnPublicSite
allowInAdminQuestionnaires
allowInPublicQuestionnaireBuilder
expectedVersion
```

---

# 180. Public Profile Projection

Conceptually:

```text
PublicActorProfileDTO
```

contains only eligible values.

---

# 181. Admin Profile Projection

Includes:

- all editable Profile facts;
- field policies;
- versions;
- readiness;
- usage summary;
- warnings;
- localization completeness.

---

# 182. Hero Projection

Consumes Profile plus other safe module projections.

---

# 183. Quick Facts Projection

Consumes only fields selected by product rule and visibility.

---

# 184. Questionnaire Profile Projection

Used when constructing Draft/Revision DocumentModel.

---

# 185. Builder Profile Projection

Contains only Builder-eligible fields.

---

# 186. AI Profile Context Projection

Task-specific.

---

# 187. Profile API contract

Uses DTOs from DOC-101.

---

# 188. Public example

```text
{
  professionalName,
  professionalTitle,
  city?,
  biography?,
  quickFacts
}
```

No policies/internal IDs needed.

---

# 189. Admin example

```text
{
  id,
  version,
  identity,
  parameters,
  localizations,
  fieldPolicies,
  readiness
}
```

---

# 190. Validation rules

Profile module enforces at least:

```text
name required
title required
slug valid/unique
country code valid if present
dates valid
measurements positive/plausible
locales supported
field_key allowlisted
version current
```

---

# 191. PROF-PAR-005 — Plausible Bounds Are Data Safety, Not Appearance Judgment

Validation must avoid body-value commentary.

It only rejects obvious technical corruption.

---

# 192. Biography validation

- bounded length;
- no unsupported unsafe markup if rich text unsupported;
- normalized line endings;
- safe rendering.

---

# 193. Rich text

If Biography later supports rich text:

store safe structured representation/Markdown subset with sanitization.

No arbitrary HTML/JS.

---

# 194. Public rendering

Escaped/sanitized.

---

# 195. Profile readiness blockers examples

```text
PROFILE_NAME_REQUIRED
PROFILE_TITLE_REQUIRED
PROFILE_SLUG_INVALID
PROFILE_MAIN_IMAGE_MISSING
PROFILE_LOCALIZATION_INCOMPLETE
```

depending context.

---

# 196. Profile readiness warnings

Examples:

```text
PROFILE_EN_BIOGRAPHY_MISSING
PROFILE_VIDEO_MISSING
PROFILE_CONTACT_MISSING
PROFILE_QUESTIONNAIRE_MISSING
```

if optional for core profile but professionally recommended.

---

# 197. Readiness must be context-specific

Possible:

```text
PUBLIC_SITE_READINESS
QUESTIONNAIRE_READINESS
BUILDER_READINESS
INTERNATIONAL_PROFILE_READINESS
```

---

# 198. PROF-INV-011 — One Global “100% Complete” Score Is Not Business Authority

May exist for UX, but transitions rely on concrete rules.

---

# 199. International Profile readiness

For EN context may check:

```text
English biography
language data
localized project/training copy where required
```

---

# 200. Fallback localization

If English missing, product MAY show approved fallback according to locale policy.

It MUST NOT silently AI-translate at request time.

---

# 201. Public biography search indexing

Only approved current public Biography.

---

# 202. Draft/AI text excluded

Normative.

---

# 203. Hero Search relation

Hero itself is not indexed as duplicate entity.

Underlying public facts feed SearchProjection.

---

# 204. Profile and Contacts

Profile may display Contact CTA.

But Contact Source belongs Contacts module.

---

# 205. Profile and Skills

Quick Facts may show selected skills.

Skills Source belongs Skills module.

---

# 206. Profile and Languages

Same.

---

# 207. Profile and Main Portfolio

Hero photo and main photo context come from Portfolio/Media.

---

# 208. Profile and Professional Media

Video CTA resolves Professional Media/Links module.

---

# 209. Profile and Questionnaire

Questionnaire CTA resolves current Primary public Questionnaire.

---

# 210. PROF-INV-012 — Profile Must Not Cache Independent Copies of CTA Targets

Use projections/references.

---

# 211. No Dead Ends

Public Profile should always offer next useful action where available:

```text
Portfolio
Video
Questionnaire
Contact
```

---

# 212. Conversion priority

Primary:

```text
professional inquiry / casting / role
```

Secondary:

```text
video
portfolio
questionnaire
PDF
emotional portfolio
```

---

# 213. Source-aware entry

A visitor may enter from:

```text
search
social
direct
questionnaire
casting link
```

Profile content remains same Source facts, with only priority/context differing.

---

# 214. Mobile requirements

Hero must keep:

```text
identity
primary image
critical quick facts
main CTA
```

above excessive detail.

---

# 215. Accessibility

Profile fields rendered semantically.

Do not rely only on icons to identify:

```text
location
languages
height
contact
```

---

# 216. Images

Use meaningful alt text according to safe content policy.

No AI speculative appearance descriptions.

---

# 217. Biography typography

Theme cannot reduce readability below accessibility standards.

---

# 218. Privacy

Profile may contain professional PII.

Public visibility must be deliberate.

---

# 219. DOB privacy

Strongly conservative by default.

---

# 220. Contact privacy

Owned by Contacts module; hidden contacts never enter Profile DTO.

---

# 221. Logs

Profile update logs should contain:

```text
entity ID
field class changed
operation
correlation ID
```

not full sensitive before/after payload by default.

---

# 222. Audit

High-impact changes SHOULD record:

```text
identity changes
visibility changes
slug changes
activation/archive
Biography apply from AI
```

---

# 223. Audit change summary

Can say:

```text
height visibility changed
```

without storing private value unnecessarily.

---

# 224. Analytics

Profile editing activity is not public product analytics unless useful operationally.

---

# 225. Admin Dashboard integration

Dashboard can surface:

```text
Profile incomplete
EN Biography missing
Questionnaire stale after Profile change
Public link issue
```

---

# 226. Notifications

Routine Profile edits do not need notifications.

Operational alerts may arise from:

```text
broken public profile link
failed search/index rebuild
critical readiness issue
```

through VOP/Notifications policy.

---

# 227. Background jobs

Profile updates MAY enqueue:

```text
SEARCH_REINDEX
CACHE_INVALIDATE
QUESTIONNAIRE_READINESS_REBUILD
VOP_PROFILE_CHECK
```

---

# 228. No long job for ordinary scalar save

Save must stay responsive.

---

# 229. Idempotency

Ordinary typed Profile update mainly relies on optimistic concurrency.

Strong idempotency optional.

---

# 230. High-impact commands

Slug change/activation/archive MAY use idempotency where request retry could duplicate significant side effects.

---

# 231. Anti-pattern — duplicated Profile facts

`questionnaires.height_cm` mutable source.

---

# 232. PROF-AP-001

**Store Hero quick facts manually**

---

# 233. PROF-AP-002

**Store age as current integer**

---

# 234. PROF-AP-003

**AI infers height/hair/eye color from photo and saves automatically**

---

# 235. PROF-AP-004

**One `is_public` flag controls Site + Admin Questionnaire + Builder**

---

# 236. PROF-AP-005

**Send hidden Profile parameters to browser and hide CSS**

---

# 237. PROF-AP-006

**Change slug whenever professional name changes**

---

# 238. PROF-AP-007

**Questionnaire modifies Profile fact from PDF editor**

---

# 239. PROF-AP-008

**Public Builder sends corrected height and server saves it to Profile**

---

# 240. PROF-AP-009

**Casting AI overwrites Profile parameter based on role requirement**

---

# 241. PROF-AP-010

**BB generated biography goes live automatically**

---

# 242. PROF-AP-011

**Runtime AI translates Biography on each Public request**

---

# 243. PROF-AP-012

**Theme AI changes professional facts**

---

# 244. PROF-AP-013

**VOP autonomously fills missing professional facts**

---

# 245. PROF-AP-014

**Store same Contact inside ActorProfile**

---

# 246. PROF-AP-015

**Store languages as freeform string inside Profile**

---

# 247. PROF-AP-016

**Store skills inside Profile JSON because Hero needs them**

---

# 248. PROF-AP-017

**Store hero image path directly on Profile instead of Portfolio/Media reference/projection**

---

# 249. PROF-AP-018

**Public Profile DTO includes field policies**

---

# 250. PROF-AP-019

**Public metadata/JSON-LD includes hidden DOB**

---

# 251. PROF-AP-020

**Search indexes Draft Biography**

---

# 252. PROF-AP-021

**Archived Profile stays available because page checks only slug**

---

# 253. PROF-AP-022

**Restore Archive immediately republishes**

---

# 254. PROF-AP-023

**Hard-delete Profile cascades all historical Revisions**

---

# 255. PROF-AP-024

**Use `additional_parameters` as unrestricted EAV database**

---

# 256. PROF-AP-025

**Validation “corrects” unusual measurements automatically**

---

# 257. PROF-AP-026

**One universal completion percentage decides publication**

---

# 258. PROF-AP-027

**UI sends `updated_by` as authoritative actor**

---

# 259. PROF-AP-028

**Profile mutation invokes AI provider within DB transaction**

---

# 260. PROF-AP-029

**Cache contains broader Profile object than Public Projection**

---

# 261. PROF-AP-030

**Current Profile changes mutate existing QuestionnaireRevision**

---

# 262. Core invariants

`PROF-INV-013`  
Every Profile fact has one authoritative owner.

`PROF-INV-014`  
Profile UUID is stable identity.

`PROF-INV-015`  
Public slug is route identity, not FK identity.

`PROF-INV-016`  
Professional name is Human-confirmed.

`PROF-INV-017`  
Profile parameters are not AI-inferred automatically.

`PROF-INV-018`  
Missing fact remains missing.

`PROF-INV-019`  
Age is derived, not independent Source.

`PROF-INV-020`  
Measurement unit is explicit.

`PROF-INV-021`  
Scalar visibility uses independent context flags.

`PROF-INV-022`  
Hidden fields are absent from Public DTO.

`PROF-INV-023`  
Builder permission does not imply public visibility.

`PROF-INV-024`  
Public visibility does not imply Builder permission.

`PROF-INV-025`  
Admin Questionnaire permission remains independent.

`PROF-INV-026`  
Quick Facts is Projection.

`PROF-INV-027`  
Hero is Projection.

`PROF-INV-028`  
Hero photo is not independent binary Source.

`PROF-INV-029`  
Hero CTA destinations belong their owning modules.

`PROF-INV-030`  
Biography localized values are explicitly persisted.

`PROF-INV-031`  
AI-generated Biography is Draft until Human Apply.

`PROF-INV-032`  
Human-edited AI copy remains distinguishable from original generation.

`PROF-INV-033`  
Theme AI cannot mutate Profile facts.

`PROF-INV-034`  
Casting AI cannot mutate Profile facts.

`PROF-INV-035`  
VOP cannot invent Profile facts.

`PROF-INV-036`  
Questionnaire freezes Profile data only as immutable Snapshot.

`PROF-INV-037`  
Builder freezes Profile data only as immutable GenerationSnapshot.

`PROF-INV-038`  
Historical snapshot never becomes current Source.

`PROF-INV-039`  
Profile mutation uses expected version where required.

`PROF-INV-040`  
Profile visibility change invalidates affected Public/Builder projections.

`PROF-INV-041`  
Search index contains only scope-safe Profile fields.

`PROF-INV-042`  
SEO cannot contain hidden Profile fields.

`PROF-INV-043`  
Analytics does not duplicate sensitive Profile facts unnecessarily.

`PROF-INV-044`  
Audit does not store secret/private before/after values unnecessarily.

`PROF-INV-045`  
Archived Profile disappears from current Public surfaces.

`PROF-INV-046`  
Archive does not destroy history.

`PROF-INV-047`  
Restore does not automatically republish.

`PROF-INV-048`  
Hard delete requires dependency-aware workflow.

`PROF-INV-049`  
Profile public Preview uses same rules as Public site.

`PROF-INV-050`  
AI context receives minimum necessary Profile facts.

`PROF-INV-051`  
No Profile fact can be changed through Search/Analytics.

`PROF-INV-052`  
No Profile fact can be changed by PDF renderer.

`PROF-INV-053`  
No Profile fact can be changed by QR subsystem.

`PROF-INV-054`  
No Profile fact can be changed by Notification delivery.

`PROF-INV-055`  
Outbox/job payloads do not become Profile Source.

`PROF-INV-056`  
Runtime fallback locale cannot create translated Source.

`PROF-INV-057`  
Current Profile change does not rewrite historical Questionnaire/Builder artifacts.

`PROF-INV-058`  
Profile readiness is derived.

`PROF-INV-059`  
Profile completeness score cannot bypass concrete readiness blockers.

`PROF-INV-060`  
Profile public data must remain usable when AI provider is unavailable.

---

# 263. E2E-PROF-001 — Create Profile

Create Draft Profile.

Expected:

```text
stable UUID
valid version
non-public optional parameter policies
```

---

# 264. E2E-PROF-002 — Missing name

Activation/publication with blank professional name is blocked.

---

# 265. E2E-PROF-003 — Slug uniqueness

Duplicate current slug rejected with semantic conflict.

---

# 266. E2E-PROF-004 — Slug stability

Change professional name.

Slug remains unchanged unless explicit ChangeProfileSlug command executed.

---

# 267. E2E-PROF-005 — Date-only DOB

DOB remains exact calendar date across timezones.

---

# 268. E2E-PROF-006 — Derived age

Change test date.

Displayed age changes without DB update to stored age.

---

# 269. E2E-PROF-007 — Height unit

`height_cm=172`.

RU display:

```text
172 см
```

EN:

```text
172 cm
```

same Source value.

---

# 270. E2E-PROF-008 — Unknown parameter

Missing shoe size remains absent.

No default created.

---

# 271. E2E-PROF-009 — Public-hidden height

Set:

```text
showOnPublicSite=false
adminQuestionnaire=true
builder=false
```

Expected:

- absent Public;
- available prepared Questionnaire;
- absent Builder.

---

# 272. E2E-PROF-010 — Builder-only parameter

```text
public=false
builder=true
```

Expected:

- absent Public;
- eligible Builder.

---

# 273. E2E-PROF-011 — Three flags independence

Test all eight boolean combinations for supported scalar field.

No implicit coercion.

---

# 274. E2E-PROF-012 — Hidden serialization

Hidden DOB absent from:

```text
HTML
RSC/JSON
SEO metadata
Search
```

---

# 275. E2E-PROF-013 — Draft Biography

Save RU Biography.

Public receives only approved/current public state according to publication policy.

---

# 276. E2E-PROF-014 — EN missing

RU Biography exists; EN missing.

System does not store AI-translated EN automatically.

---

# 277. E2E-PROF-015 — BB Draft

Generate Biography via BB.

Public Profile unchanged.

---

# 278. E2E-PROF-016 — BB Human edit

Generated and edited texts remain distinct.

---

# 279. E2E-PROF-017 — BB Apply

Apply Draft with correct target version.

Profile Biography updates through Profile command.

---

# 280. E2E-PROF-018 — BB stale apply

Profile Biography changes after generation.

Blind stale Apply is blocked/warned per policy.

---

# 281. E2E-PROF-019 — Hero composition

Hero obtains main image from Portfolio/Media.

No duplicate image binary/path stored in Profile.

---

# 282. E2E-PROF-020 — Quick Facts

Hidden scalar field does not appear in Quick Facts.

---

# 283. E2E-PROF-021 — Skill in Quick Facts

Displayed key Skill comes from Skills module Source.

Updating Skill updates derived Quick Facts without Profile duplication.

---

# 284. E2E-PROF-022 — Language in Quick Facts

Same for Language.

---

# 285. E2E-PROF-023 — Questionnaire snapshot

Publish Questionnaire with Profile city=Almaty.

Change city later.

Historical Revision still contains frozen original city.

---

# 286. E2E-PROF-024 — Builder revalidation

Open Builder while height eligible.

Disable Builder permission.

Generate.

Height excluded/blocker; not frozen into new Snapshot.

---

# 287. E2E-PROF-025 — Casting Match

Confirmed Profile height used in matching.

No image-derived inferred height used.

---

# 288. E2E-PROF-026 — Casting missing fact

Height absent.

Match returns `NO_CONFIRMED_DATA`.

---

# 289. E2E-PROF-027 — Theme AI isolation

Theme AI request context contains no Profile private parameters beyond presentation-safe needs.

---

# 290. E2E-PROF-028 — VOP

VOP detects missing EN Biography but does not create one automatically.

---

# 291. E2E-PROF-029 — Search

Draft/private Biography phrase not searchable publicly.

---

# 292. E2E-PROF-030 — SEO

Hidden DOB/parameter absent from JSON-LD/OpenGraph.

---

# 293. E2E-PROF-031 — Concurrency

Admin A and B edit Profile v7.

A commits v8.

B receives stale conflict.

---

# 294. E2E-PROF-032 — Visibility concurrency

Two admins change same field policy.

Stale write cannot silently overwrite.

---

# 295. E2E-PROF-033 — Cache invalidation

Public height initially visible/cached.

Disable public flag.

Next secure Public request cannot expose stale height.

---

# 296. E2E-PROF-034 — Archive

Archive Profile.

Public route and Public Search stop exposing it.

---

# 297. E2E-PROF-035 — Historical after archive

Admin can still inspect prior Questionnaire Revisions and Profile history.

---

# 298. E2E-PROF-036 — Restore

Restore Profile.

It returns Draft/non-public state according to policy; no automatic publication.

---

# 299. E2E-PROF-037 — Hard delete

Attempt hard delete with existing Questionnaires/Projects/Media.

Dependency-aware workflow blocks.

---

# 300. E2E-PROF-038 — Privacy redaction

Remove sensitive Profile fact.

Search/cache/current projections no longer expose it.

Historical access follows redaction policy.

---

# 301. E2E-PROF-039 — Runtime without AI

Disable AI provider.

Manual Profile editing, Public Profile, Questionnaire and Builder continue functioning.

---

# 302. E2E-PROF-040 — Additional parameters

Unknown unregistered JSON key rejected.

---

# 303. E2E-PROF-041 — Unsafe Biography markup

Attempt arbitrary script/unsafe HTML.

Rejected/sanitized according to Biography format policy.

---

# 304. E2E-PROF-042 — Public Preview parity

Admin Public Preview output matches actual Public Projection for same locale/context.

---

# 305. E2E-PROF-043 — Audit

Visibility change creates safe Audit entry without unnecessarily recording hidden value.

---

# 306. E2E-PROF-044 — Outbox

Profile visibility mutation and corresponding Outbox event commit atomically.

---

# 307. E2E-PROF-045 — Search worker stale completion

Older Profile search projection rebuild cannot overwrite newer generation.

---

# 308. E2E-PROF-046 — Questionnaire stale detection

Profile fact used in Questionnaire Draft changes.

Draft readiness/staleness reflects dependency change.

Historical published Revision unchanged.

---

# 309. E2E-PROF-047 — Public CTA

Questionnaire unavailable.

Hero does not render dead enabled Questionnaire CTA.

---

# 310. E2E-PROF-048 — Contact CTA

No eligible Contact.

Public Hero does not leak hidden Contact nor fabricate contact method.

---

# 311. E2E-PROF-049 — Hero mobile

Mobile projection/render preserves identity + primary professional actions before long Biography.

---

# 312. E2E-PROF-050 — AI fact invention

BB output introduces an unsupported role/award.

Human review flags/notices unsupported content; it does not become Profile automatically.

---

# 313. E2E-PROF-051 — Slug event

Explicit slug change triggers invalidation of current public link/search/QR-dependent current representations without mutating historical snapshot URLs.

---

# 314. E2E-PROF-052 — Locale fallback

Request EN where EN Biography missing.

Fallback follows explicit locale policy; no live AI translation occurs.

---

# 315. E2E-PROF-053 — Invalid field policy key

Submit:

```text
fieldKey="oauth_token"
```

Rejected.

---

# 316. E2E-PROF-054 — Builder cannot write fact

Builder submits custom `heightCm=190`.

Server ignores/rejects value; only selected Source fact can enter Snapshot.

---

# 317. E2E-PROF-055 — Questionnaire cannot write fact

Questionnaire editor changes displayed height locally.

No Profile Source mutation occurs without explicit Profile command.

---

# 318. Profile relationship diagram

```text
                     ACTOR PROFILE
                          │
       ┌──────────────────┼──────────────────┐
       ▼                  ▼                  ▼
   PARAMETERS        LOCALIZATIONS      FIELD POLICIES
       │                  │                  │
       └──────────────┬───┴──────────────┬───┘
                      ▼                  ▼
                  PROJECTIONS        SNAPSHOTS
                      │                  │
      ┌───────────────┼─────────────┐    │
      ▼               ▼             ▼    ▼
    Public       Questionnaire    Builder Casting/AI
```

---

# 319. Hero composition diagram

```text
Profile Identity ─────┐
Profile Parameters ───┤
Portfolio Main Photo ─┤
Skills ───────────────┤
Languages ────────────┤
Video ────────────────┤──→ HERO PROJECTION
Questionnaire ────────┤
Contacts ─────────────┘
```

No data duplication.

---

# 320. Biography AI diagram

```text
Confirmed Sources
      ↓
BB Context Projection
      ↓
AI Generation
      ↓
AIDraft
      ↓
Human Review/Edit
      ↓
Apply
      ↓
Profile Domain Validation
      ↓
Biography Source
```

---

# 321. Visibility diagram

```text
height_cm = 172
      │
      ▼
ProfileFieldPolicy
 ┌────┼────┐
 ▼    ▼    ▼
Site  QNR Builder
```

Each branch independent.

---

# 322. Questionnaire diagram

```text
Current Profile Fact
      ↓
Questionnaire Draft Selection
      ↓
Publish Validation
      ↓
QuestionnaireRevision Snapshot
      ↓
HTML/PDF
```

---

# 323. Builder diagram

```text
Current Profile Fact
      ↓
Builder Eligibility
      ↓
Visitor Selection
      ↓
Generate Revalidation
      ↓
BuilderGenerationSnapshot
```

---

# 324. Casting diagram

```text
Casting Requirement
       +
Confirmed Profile Fact
       ↓
Comparison
       ↓
MATCH / PARTIAL / MISMATCH / NO_CONFIRMED_DATA
```

---

# 325. Profile quality gate

Перед implementation считается обязательным:

- [ ] Profile aggregate owner documented;
- [ ] typed identity fields;
- [ ] typed parameters;
- [ ] scalar field policy registry;
- [ ] independent three-way visibility;
- [ ] localization model;
- [ ] Biography model;
- [ ] BB Draft/Apply separation;
- [ ] optimistic concurrency;
- [ ] slug-change semantics;
- [ ] Public Profile Projection;
- [ ] Admin Profile Projection;
- [ ] Quick Facts Projection;
- [ ] Hero Projection;
- [ ] Questionnaire integration;
- [ ] Builder integration;
- [ ] Casting integration;
- [ ] Search projection rules;
- [ ] SEO privacy rules;
- [ ] cache invalidation;
- [ ] audit/outbox;
- [ ] archive/restore semantics;
- [ ] privacy redaction behavior;
- [ ] readiness;
- [ ] E2E coverage.

---

# 326. Acceptance criteria

`AC-PROF-001`  
Professional name stored once as Profile Source.

`AC-PROF-002`  
Optional scalar parameters use typed canonical fields.

`AC-PROF-003`  
Age is derived from DOB, not independently stored.

`AC-PROF-004`  
Each governed scalar parameter can independently allow Public, Admin Questionnaire and Public Builder usage.

`AC-PROF-005`  
Hidden Profile field never enters Public DTO.

`AC-PROF-006`  
Public Builder cannot alter Profile fact values.

`AC-PROF-007`  
Prepared Questionnaire cannot become alternate mutable Profile store.

`AC-PROF-008`  
Historical Questionnaire preserves exact snapshot after Profile change.

`AC-PROF-009`  
Biography supports independent RU/EN saved content where available.

`AC-PROF-010`  
Missing translation is not generated automatically.

`AC-PROF-011`  
BB Assistant produces Draft only.

`AC-PROF-012`  
BB Apply passes target Profile version validation.

`AC-PROF-013`  
AI cannot auto-publish Biography.

`AC-PROF-014`  
Hero is composed from Profile + owning modules.

`AC-PROF-015`  
Quick Facts is derived, not separately stored.

`AC-PROF-016`  
Main photo remains Portfolio/Media-owned.

`AC-PROF-017`  
Skills/Languages remain their own module Sources.

`AC-PROF-018`  
Contacts remain Contacts-owned.

`AC-PROF-019`  
Slug can only change through explicit high-impact command.

`AC-PROF-020`  
Slug change invalidates relevant current projections.

`AC-PROF-021`  
Public Search never indexes Draft/private Profile data.

`AC-PROF-022`  
SEO never exposes hidden Profile data.

`AC-PROF-023`  
Casting AI uses only confirmed Profile facts.

`AC-PROF-024`  
Missing Profile data produces no invented Casting match.

`AC-PROF-025`  
Profile editing remains available without AI.

`AC-PROF-026`  
Profile archive removes current Public access without deleting history.

`AC-PROF-027`  
Restore does not auto-publish.

`AC-PROF-028`  
Hard delete is dependency-aware.

`AC-PROF-029`  
Profile visibility updates are audited where required.

`AC-PROF-030`  
Public Preview and actual Public Profile use same Projection rules.

---

# 327. Final doctrine

> **Actor Profile является единственным Master Source профильных профессиональных фактов актрисы. Identity, параметры и Biography хранятся один раз; Hero, Quick Facts, Questionnaire, Builder, Casting и Search получают только контекстные representations. Public Site, Admin Questionnaire и Public Builder имеют независимые permissions для каждого управляемого scalar fact. AI может только сформировать Draft или recommendation и никогда не становится владельцем профессионального факта. Любое историческое использование Profile данных оформляется immutable Snapshot, поэтому изменение текущего Profile не переписывает прошлые анкеты, PDF или casting materials.**