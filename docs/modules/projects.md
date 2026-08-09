# PROJECTS, ROLES & PERFORMANCES MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация профессиональных проектов, ролей, role media, performance dates и provenance из Opportunity

**Целевой файл:** `docs/modules/projects.md`  
**Документ:** DOC-115  
**Статус:** ✅ Completed  
**Тип:** Module / Projects / Roles / Filmography / Performances / Professional Experience

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

---

# 1. Назначение модуля

Projects Module является Master Source профессионального опыта актрисы в завершённых, текущих или подтверждённых проектах.

Он отвечает за:

1. Projects;
2. Roles;
3. role descriptions;
4. project descriptions;
5. role-specific photographs;
6. performances / показы / спектакли / даты выступлений;
7. ordering;
8. publication;
9. visibility;
10. selected projects;
11. Homepage role showcase;
12. Questionnaire inclusion;
13. Public Builder inclusion;
14. Search;
15. Casting/Opportunity provenance;
16. Archive/history;
17. multilingual professional copy.

---

# 2. Главная доктрина

> **Project описывает произведение/производство/профессиональную работу; Role описывает участие актрисы в этом Project; MediaAsset остаётся владельцем файла, а RoleMedia описывает использование фотографии в контексте роли.**

Canonical:

```text id="klqpp3"
Project
   ↓
Role
   ├── Role Media
   ├── Performances
   └── Professional Description
```

---

# 3. Fundamental separation

```text id="ocxvl8"
Project
≠
Role
≠
Training
≠
Achievement
≠
Emotional Portfolio
≠
Main Portfolio
≠
Casting
≠
Opportunity
```

---

# 4. Module identifiers

Используются:

```text id="r2m2rf"
PRJ-*
ROLE-*
ROLE-MED-*
PERF-*
PRJ-VIS-*
PRJ-QNR-*
PRJ-BLD-*
PRJ-OPP-*
PRJ-AI-*
PRJ-INV-*
PRJ-AP-*
E2E-PRJ-*
```

---

# 5. Business purpose

Projects module должен позволять casting professional быстро ответить:

1. В каких проектах работала актриса?
2. Какие роли исполняла?
3. Какой тип и масштаб профессионального опыта имеется?
4. Какие подтверждающие материалы доступны?
5. Когда проходили спектакли/показы/выступления?
6. Какие проекты наиболее релевантны текущему кастингу?

---

# 6. Project as professional Source

`Project` является самостоятельной сущностью.

Примеры:

```text id="t56e47"
фильм
сериал
короткометражный фильм
театральная постановка
рекламный проект
музыкальный клип
web/digital production
другой реальный профессиональный production project
```

---

# 7. Project taxonomy

Тип проекта должен быть controlled value.

Baseline MAY include:

```text id="bh5cqq"
FEATURE_FILM
SHORT_FILM
SERIES
THEATRE
COMMERCIAL
MUSIC_VIDEO
WEB_DIGITAL
OTHER
```

---

# 8. `OTHER`

Используется только для **реального проекта**, не попадающего в текущую taxonomy.

---

# 9. PRJ-INV-001 — Training Is Never Project OTHER

Курсы, мастер-классы, школы, обучение и тренинги принадлежат `Training` module.

---

# 10. Migration requirement

Все legacy records, ранее использованные как:

```text id="seapxx"
Project.type = OTHER
```

для курсов/обучения, должны быть перенесены в Training.

---

# 11. PRJ-INV-002 — Emotional Portfolio Is Not Project

Эмоциональная съёмка не моделируется Project.

---

# 12. PRJ-INV-003 — Achievement Is Not Project

Награда/сертификат/достижение хранится отдельно.

---

# 13. Project ownership

Project owns:

```text id="fjb4mv"
title
project_type
production/context metadata
professional description
time period
publication/configuration
ordering/featured state
```

---

# 14. Role ownership

Role owns:

```text id="vgmpkj"
role_name
role_type/category where supported
role_description
credit/status metadata
relation to Project
role-specific visibility/configuration
```

---

# 15. Media ownership

Media Library owns binary.

Role owns contextual `RoleMedia` relationship.

---

# 16. Performance ownership

`Performance` belongs Role/Project performance context.

---

# 17. Suggested physical model

```text id="4fuf7c"
projects
project_localizations

roles
role_localizations

role_media

performances
```

---

# 18. Project relation

Canonical:

```text id="0lzwfk"
Actor Profile
   ↓
Project
   ↓
Role
```

---

# 19. Profile relationship

Каждый Project belongs one Actor Profile in current single-actress product model.

---

# 20. Future multi-cast database

Не требуется моделировать весь актёрский состав.

Система хранит профессиональный experience именно актрисы.

---

# 21. PRJ-INV-004 — Project Is Not General Movie Database

Не требуется создавать IMDb-like database всех участников production.

---

# 22. Multiple roles

Один Project MAY содержать несколько Roles актрисы, если это реально и подтверждено.

---

# 23. ROLE-INV-001 — Role Cannot Exist Without Project

Baseline Role belongs exactly one Project.

---

# 24. Project without Role

Draft MAY exist before Role entered.

Public professional Project SHOULD normally have at least one Role or explicit valid participation type.

---

# 25. Role name

Examples:

```text id="8qw2dh"
Анна
Мать
Врач
Главная героиня
```

Only confirmed professional credit/context.

---

# 26. Role type

If product needs classification, controlled values MAY include:

```text id="jd83cy"
LEAD
SUPPORTING
EPISODIC
FEATURED
ENSEMBLE
OTHER
```

---

# 27. ROLE-INV-002 — Never Infer Role Importance

AI cannot decide that a role was “lead” from photos/text unless Source explicitly confirms it.

---

# 28. Missing role category

Remain:

```text id="31pzsr"
not specified
```

---

# 29. Project title

Human-confirmed Source.

---

# 30. Production year

Project MAY support:

```text id="sy420u"
year
start_date
end_date
```

depending context.

---

# 31. Date semantics

Use:

- `date` for exact date;
- year integer if only year known;
- no fabricated January 1 for year-only data.

---

# 32. PRJ-INV-005 — Precision Must Be Preserved

Known year only:

```text id="awmdxh"
2025
```

must not become:

```text id="fq47eg"
2025-01-01
```

as false exact fact.

---

# 33. Project description

Professional description of production/project.

---

# 34. Role description

Professional description of actress's participation/character.

---

# 35. Description ownership separation

```text id="5vu43f"
Project description
≠
Role description
```

---

# 36. Example

Project:

```text id="t3ebrh"
Короткометражная драма...
```

Role:

```text id="m0s9hh"
Роль врача...
```

---

# 37. Localization

Project title where officially localized MAY use locale-aware content.

Descriptions support RU/EN according to site localization.

---

# 38. Official title vs translation

Do not automatically translate official production title if official localized title unknown.

---

# 39. PRJ-INV-006 — Runtime AI Translation Is Not Official Project Title

---

# 40. BB Assistant

BB Assistant MAY draft:

```text id="xsr9qu"
project description
role description
professional summary
```

from confirmed Source.

---

# 41. PRJ-AI-001 — BB Draft Only

No direct publication.

---

# 42. AI cannot invent

```text id="kp94en"
director
production company
release year
role importance
festival status
awards
character story
```

unless Source/context explicitly provides it.

---

# 43. Project lifecycle

Canonical:

```text id="wv7i2i"
DRAFT
PUBLISHED
ARCHIVED
```

---

# 44. Draft

Can be incomplete.

Not public.

---

# 45. Published

Available to surfaces where corresponding visibility permits.

---

# 46. Archived

Removed from current normal professional exposure.

History retained.

---

# 47. PRJ-INV-007 — Save != Publish

---

# 48. PRJ-INV-008 — Archive != Delete

---

# 49. Role lifecycle

Preferred:

```text id="mhfiml"
DRAFT
PUBLISHED
ARCHIVED
```

or inheritance from Project plus role-level availability.

---

# 50. Role cannot be publicly active if parent Project is archived/unpublished.

---

# 51. ROLE-INV-003 — Child Cannot Exceed Parent Publication

Conceptually:

```text id="nm7dr0"
ROLE_PUBLIC =
  Project published/public
  AND Role published/public
```

---

# 52. Visibility

Projects/Roles should use contextual permissions where they can enter different surfaces.

At minimum:

```text id="fvw0vf"
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 53. PRJ-VIS-001 — Permissions Independent

---

# 54. Project vs Role visibility

If Role has separate visibility, effective visibility is intersection.

---

# 55. Effective rule

```text id="2fp0vz"
ROLE_SURFACE_ELIGIBLE =
  project permits surface
  AND role permits surface
  AND publication/lifecycle allows
```

---

# 56. Simpler implementation

If current product does not need independent role flags initially, Project may govern the surface and Role remains child.

However architecture should not permit Role to exceed Project permission.

---

# 57. Featured / Selected Projects

Homepage should show curated subset rather than all Projects.

---

# 58. `featured`

This is presentation/configuration metadata.

Not professional factual importance.

---

# 59. PRJ-INV-009 — Featured Is Human Presentation Choice

AI/analytics cannot autonomously mark Project featured.

---

# 60. Ordering

Explicit `display_order`.

---

# 61. CreatedAt is not filmography order

---

# 62. Public ordering

Could use:

1. Featured first;
2. explicit professional order;
3. date/year secondary.

Exact UX configurable.

---

# 63. Role Media

Role-specific image relationship:

```text id="0cczyv"
Role
 ↓
RoleMedia
 ↓
MediaAsset
```

---

# 64. Allowed media baseline

Role photo feature specifically uses:

```text id="8ds0b7"
IMAGE
```

---

# 65. Future role video

Could be added separately.

Do not overload photo limit semantics.

---

# 66. Role photo limit

Canonical hard requirement:

> **Maximum 5 role photos per Role.**

---

# 67. ROLE-MED-001 — Maximum 5

```text id="o9rqwu"
active role photos <= 5
```

---

# 68. Scope

Limit applies to current active role-photo relations.

---

# 69. Draft limit

Prefer enforce at relation creation, not only publish.

---

# 70. Race safety

Count must be protected transactionally.

---

# 71. Canonical pattern

```text id="sgiq30"
lock Role
↓
count active RoleMedia
↓
if >= 5 reject
↓
insert
```

---

# 72. ROLE-MED-002 — UI Count Is Not Authority

---

# 73. Error

```text id="06x11z"
ROLE_MEDIA_LIMIT_EXCEEDED
```

---

# 74. Same image duplicate

Recommended one active RoleMedia relation per Role/MediaAsset.

---

# 75. ROLE-MED-003 — No Accidental Duplicate Role Photo

---

# 76. Media original

Immutable MediaAsset.

---

# 77. Role-specific caption

Owned by RoleMedia or Role context.

Not global MediaAsset.

---

# 78. Role photo ordering

Explicit.

---

# 79. Role photo removal

Removes relation, not MediaAsset.

---

# 80. ROLE-MED-004 — Relation Removal Does Not Delete Original

---

# 81. Home “В образе” showcase

A Role may designate up to:

> **2 role photos for Homepage showcase “В образе”.**

---

# 82. ROLE-MED-005 — Maximum 2 Home Showcase Photos

```text id="mbg2dy"
home_showcase=true count <= 2 per Role
```

---

# 83. Home showcase is subset

Every Home Showcase photo MUST also be an active RoleMedia photo for same Role.

---

# 84. ROLE-MED-006 — Home Showcase Cannot Reference Independent File

---

# 85. Mathematical rule

```text id="w9r8ip"
HomeShowcase(Role) ⊆ RoleMedia(Role)
```

---

# 86. Therefore

Maximum:

```text id="23ba47"
RoleMedia = 5
HomeShowcase subset = 2
```

---

# 87. Homepage distinction

These “В образе” images are not Main Portfolio Home carousel.

---

# 88. ROLE-MED-007 — Main Portfolio and Role Showcase Are Separate

---

# 89. Home Showcase selection

Human-curated.

---

# 90. AI may suggest

But cannot set automatically.

---

# 91. ROLE-MED-008 — AI Cannot Set Home Showcase

---

# 92. Removing role photo selected for home

Must also remove/unset its showcase status in same semantic operation or block with clear instruction.

---

# 93. Preferred UX

Warn:

```text id="bx41g7"
Фото используется на главной странице в блоке «В образе».
```

---

# 94. Transaction

Removal + showcase cleanup atomic.

---

# 95. Archive Role

Role showcase disappears from current Homepage.

---

# 96. Media remains.

---

# 97. Project public page

Should show:

- project title;
- project type;
- period/year;
- actress Role;
- role description;
- selected Role photos;
- performances if relevant;
- professional media/links if available through corresponding module.

---

# 98. Not all project types need Performances

`Performance` mainly applies to:

```text id="7cmhdp"
theatre
live performance
stage production
other scheduled performances
```

---

# 99. Performance entity

Represents a real occurrence/date.

---

# 100. Representative fields

```text id="sqcd26"
id
project_id
role_id?
performance_date
start_time?
venue?
city?
notes?
publication_state / visibility if required
version
```

---

# 101. PERF-INV-001 — Performance Date Is Event Fact

Not upload date.

---

# 102. Date-only vs time

If exact start time unknown:

store date only.

Do not invent `00:00`.

---

# 103. Timezone

If exact performance time recorded, use local venue timezone where scheduling semantics matter.

---

# 104. Historical performance

Past date remains professional history.

---

# 105. Upcoming performance

May appear on Public page if product wants current schedule.

---

# 106. Current-date logic

Derived from `performance_date`, not separate stale `is_upcoming` boolean.

---

# 107. PERF-INV-002 — Upcoming Is Derived

---

# 108. Venue

Optional unless known.

---

# 109. City

Optional event-specific city.

Does not modify Profile base city.

---

# 110. PERF-INV-003 — Performance Location ≠ Profile Location

---

# 111. Performance sorting

Default chronological.

Upcoming:

```text id="p273yi"
ASC
```

Past history:

```text id="k9mdqd"
DESC
```

reasonable.

---

# 112. Performance cancellation

If lifecycle requires:

```text id="pd5ypo"
SCHEDULED
CANCELLED
COMPLETED
```

can be modeled separately.

---

# 113. Do not delete cancelled event just to hide it

Historical accuracy may require status.

---

# 114. Current public schedule

Can hide cancelled according to UX while preserving record.

---

# 115. PRJ-INV-010 — Performance Is Not Project Lifecycle

One performance cancellation does not archive Project.

---

# 116. Public Project page

Must prioritize actor-specific value rather than production encyclopedia.

---

# 117. Suggested hierarchy

```text id="ep9u5e"
Project title
Project type / year
Role
Role photos
Description
Performances / dates
Relevant link/media
CTA
```

---

# 118. Homepage Selected Projects

Should show concise proof.

---

# 119. Recommended card

```text id="m7py6h"
Project title
Role
year/type
one safe image where configured
```

---

# 120. Homepage “В образе”

Uses explicit RoleMedia showcase.

---

# 121. Limit rationale

Max 2 keeps Homepage concise and avoids converting it into filmography gallery.

---

# 122. No Dead Ends

Project detail should expose appropriate next actions:

```text id="h46qam"
Portfolio
Questionnaire
Contact
```

where relevant.

---

# 123. Main Portfolio relationship

Role photos are not Main Portfolio by default.

---

# 124. PRJ-INV-011 — Role Image Is Contextual Evidence

If the same binary should also be Main Portfolio:

create explicit PortfolioItem referencing same MediaAsset.

---

# 125. Emotional relationship

Project/Role images do not automatically become Emotional Session sources.

---

# 126. Training relationship

No inheritance.

---

# 127. Achievement relationship

A Project may be related to an Achievement conceptually, but Achievement remains separate Source.

---

# 128. Questionnaire integration

Prepared Questionnaire may include selected Projects/Roles.

---

# 129. PRJ-QNR-001 — Projects Are Optional Evidence

Mandatory Questionnaire baseline remains:

```text id="lxd2ro"
identity
Close-Up
Full Body
official profile URL
```

Projects do not replace mandatory content.

---

# 130. Compact Questionnaire

Should include only highly selected/compact projects if template permits.

---

# 131. Extended Questionnaire

May include broader project history.

---

# 132. Casting Questionnaire

Can include only role-relevant Projects selected for that casting.

---

# 133. Project selection

Explicit item-level selection.

---

# 134. PRJ-QNR-002 — Do Not Dump Entire Filmography Automatically

---

# 135. Role inclusion

Questionnaire should display Role together with Project to preserve meaning.

---

# 136. Example

Good:

```text id="0m1f3h"
Project X — Role: Doctor
```

Bad:

```text id="5ej4rg"
Doctor
```

without project context.

---

# 137. Questionnaire photos

Role photos MAY be selected as Project/Role supporting materials if questionnaire design permits.

---

# 138. Important

They remain Project block media.

They cannot satisfy mandatory Main Portfolio Close-Up/Full Body.

---

# 139. PRJ-QNR-003 — Project Photos Cannot Substitute Portfolio Mandatory Photos

---

# 140. Historical snapshot

Questionnaire Revision freezes:

```text id="cv0l6a"
project identity
project title/description used
role identity/name/description
selected RoleMedia identity
relevant performance data
```

as applicable.

---

# 141. Current Project edit

Does not rewrite historical Questionnaire.

---

# 142. Public Builder

Builder exposes only:

```text id="syr90h"
allow_in_public_questionnaire_builder=true
```

current eligible Project/Role material.

---

# 143. Builder item selection

Can choose:

- Project;
- relevant Role;
- possibly supporting RoleMedia.

---

# 144. Server still constructs display facts from Source.

---

# 145. PRJ-BLD-001 — Builder Cannot Edit Project Credits

---

# 146. Casting labels

Builder Casting template may add temporary:

```text id="2nmf3o"
casting_name
role_name
```

for document context.

These temporary labels do **not** mutate saved Projects.

---

# 147. PRJ-BLD-002 — Temporary Casting Context Is Not Project Source

---

# 148. Builder Generate

Revalidates selected Projects/Roles/media.

---

# 149. If Project becomes ineligible

Generation blocks/removes it.

---

# 150. Search integration

Public Search may index:

```text id="20608e"
Project title
Role name
Project type
approved description
year
```

---

# 151. Public Search excludes

```text id="sqaw61"
draft
archived
private
admin notes
Opportunity provenance internals
```

---

# 152. Search inside profile

Professional visitor can search:

```text id="cksozu"
project title
role
project type
```

---

# 153. Search result routes to Project detail/section.

---

# 154. PRJ-INV-012 — Search Result Does Not Grant Access

---

# 155. SEO

Public Projects may have:

- canonical project route;
- public title;
- role;
- safe description;
- selected public image.

---

# 156. SEO cannot expose private Draft.

---

# 157. Social sharing

Project page may have OpenGraph image from public-safe Project/Role image.

---

# 158. Hidden RoleMedia cannot become OG image.

---

# 159. Casting integration

Casting AI may compare role/casting requirements against Projects/Roles.

---

# 160. Example

Requirement:

```text id="3mcrx9"
опыт в драматических сериалах
```

System may identify confirmed matching Project types.

---

# 161. PRJ-AI-002 — AI Uses Saved Project Facts Only

---

# 162. No inference

A photo cannot prove:

```text id="mac8fs"
lead role
series experience
stunt experience
```

unless Source records support it.

---

# 163. Recommendations

Casting AI may recommend:

```text id="nc499v"
include Project X because project type/role matches explicit requirement
```

with evidence.

---

# 164. Human confirms Questionnaire selection.

---

# 165. Opportunity integration

Opportunity Pipeline is pre-project business workflow.

---

# 166. Canonical pipeline

```text id="d7d8mv"
New
Qualified
Materials Requested
Self-Tape Requested
Audition
Callback
Offer
Booked
Closed—Not Selected
Withdrawn
```

---

# 167. Booked

`Booked` does not automatically create published Project.

---

# 168. PRJ-OPP-001 — Booked Requires Human Decision

---

# 169. Project creation from Booked

Explicit command:

```text id="5h51ii"
CreateProjectDraftFromBookedOpportunity
```

---

# 170. Result

Creates:

```text id="cnc13y"
Project DRAFT
```

with provenance.

---

# 171. PRJ-OPP-002 — Booked→Project Creates Draft Only

---

# 172. Why

Opportunity data may be:

- incomplete;
- provisional;
- not public;
- differently worded than professional filmography.

---

# 173. Provenance

Project created from Opportunity should retain:

```text id="2tzt4m"
source_opportunity_id
created_from_opportunity_at
created_by
```

or equivalent provenance link.

---

# 174. Provenance does not mean live synchronization

---

# 175. PRJ-OPP-003 — Project Is Independent After Creation

Later Opportunity edits do not silently rewrite Project.

---

# 176. Opportunity remains

Do not delete Opportunity after Project creation.

---

# 177. Bidirectional navigation

Admin should be able to navigate:

```text id="kbhi5e"
Opportunity → Project
Project → source Opportunity
```

---

# 178. Idempotency

Booked→Project Draft creation requires durable idempotency.

---

# 179. Same command retry

Returns same Project Draft.

---

# 180. PRJ-OPP-004 — No Duplicate Project From Retry

---

# 181. Domain uniqueness

If one Opportunity may create only one Project Draft, enforce separately.

---

# 182. Distinguish retry vs new intentional Project

Idempotency key/domain rule handles.

---

# 183. Feedback/Casting provenance

Opportunity may originate from Casting, which may originate from Feedback.

---

# 184. Project should not duplicate entire provenance chain.

Store explicit relevant links.

---

# 185. Example

```text id="ew3gh0"
Feedback
  ↓
Casting
  ↓
Opportunity
  ↓
Project
```

---

# 186. PRJ-INV-013 — Provenance Is Reference, Not Copied Business Records

---

# 187. AI cannot auto-create Project on Booked

Human command required.

---

# 188. PRJ-AI-003 — AI Cannot Advance Opportunity or Create Professional Credit Automatically

---

# 189. BB Assistant integration

BB can draft:

```text id="nx5bmf"
Project description
Role description
Questionnaire summary
Social caption
```

---

# 190. Context

Only confirmed Source facts.

---

# 191. Human Apply

Routes through owning Project/Role command.

---

# 192. PRJ-AI-004 — BB Apply Cannot Bypass Project Validation

---

# 193. Theme AI

No project fact mutation.

---

# 194. VOP

May detect:

```text id="8c0n5e"
Project without Role
Role without description
>5 RoleMedia data corruption
>2 Home Showcase
missing EN localization
broken media
Booked Opportunity without Project Draft
Project Draft needing review
```

---

# 195. Safe VOP actions

May:

```text id="cv53v7"
rebuild image derivative
reindex Search
invalidate cache
recompute readiness
```

---

# 196. VOP recommendation-only

For:

```text id="zmc0fx"
publish Project
set Featured
change Role type
choose Home Showcase
archive Project
create Project from Booked
```

---

# 197. Projects must work without AI

Normative.

---

# 198. Admin UX

Recommended top-level:

```text id="afqy8a"
Projects
├── Published
├── Drafts
├── Archived
└── Add Project
```

---

# 199. Project card

Should show:

```text id="rs0ux6"
title
type
year/period
role(s)
publication state
visibility
featured status
media count
```

---

# 200. Project editor

Recommended sections:

1. Project details;
2. localization;
3. Roles;
4. Role Media;
5. Performances;
6. visibility;
7. homepage/featured settings;
8. Questionnaire/Builder eligibility;
9. source Opportunity/provenance;
10. readiness;
11. Public Preview.

---

# 201. Role editor

Show:

```text id="ys02zg"
role name
role type if used
description
publication/visibility
Role Media 0–5
Home Showcase 0–2
```

---

# 202. Role Media counter

Example:

```text id="u6a9s6"
3 / 5 фотографий
```

---

# 203. Home Showcase counter

```text id="3fbsru"
1 / 2 на главной
```

---

# 204. Sixth photo UX

Add control disabled or server rejects.

Server remains authoritative.

---

# 205. Third Home Showcase UX

Same.

---

# 206. Photo picker

Uses Media Library:

```text id="q0tqcu"
same profile
IMAGE
safe
```

---

# 207. Upload inside Role

Flow:

```text id="ill83s"
upload
→ MediaAsset
→ attach RoleMedia
```

---

# 208. No duplicated upload subsystem.

---

# 209. Home Showcase selection

Checkbox/toggle on existing RoleMedia.

---

# 210. ROLE-MED-009 — Home Showcase Toggle Cannot Bypass Limit

---

# 211. Public Preview

Uses real Public Project Projection.

---

# 212. Questionnaire Preview

Shows which Project/Role content can be used.

---

# 213. Builder Preview

Shows Builder-eligible entries only.

---

# 214. Performance editor

Date-centric interface.

Should not require time/venue if unknown.

---

# 215. Empty optional field

Unknown remains blank.

---

# 216. No invented production data

UI should support “Не указано”/blank in Admin, not force fake value.

---

# 217. Project readiness

Context-specific.

---

# 218. Public readiness baseline

At minimum:

```text id="2fdqi3"
Project title
Project type
at least one meaningful Role/participation
publication state
```

plus safe public visibility.

---

# 219. Role photo optional

A Project can be public without RoleMedia if professional data itself is useful.

---

# 220. Homepage showcase readiness

Requires:

```text id="h5spye"
public eligible Project/Role
selected Home Showcase RoleMedia
Media ready
```

---

# 221. Questionnaire readiness

Requires selected Project/Role currently eligible.

Role media selected only if safe/ready.

---

# 222. Builder readiness

Same with Builder permission.

---

# 223. Readiness codes

Suggested:

```text id="jbd2w1"
PROJECT_TITLE_REQUIRED
PROJECT_TYPE_REQUIRED
PROJECT_ROLE_REQUIRED
PROJECT_MEDIA_NOT_READY
ROLE_NAME_REQUIRED
ROLE_MEDIA_LIMIT_EXCEEDED
ROLE_HOME_SHOWCASE_LIMIT_EXCEEDED
PROJECT_PUBLIC_NOT_READY
PROJECT_QUESTIONNAIRE_NOT_ELIGIBLE
PROJECT_BUILDER_NOT_ELIGIBLE
PROJECT_LOCALIZATION_INCOMPLETE
```

---

# 224. Performance codes

```text id="ymczcv"
PERFORMANCE_DATE_REQUIRED
PERFORMANCE_DATE_INVALID
PERFORMANCE_ROLE_MISMATCH
```

where applicable.

---

# 225. Validation — role photo count

Hard invariant:

```text id="l9p0b7"
count(active RoleMedia) <= 5
```

---

# 226. Validation — Home Showcase

Hard invariant:

```text id="zj5b05"
count(active RoleMedia where home_showcase=true) <= 2
```

---

# 227. Validation — subset

Every showcased photo must belong same Role.

---

# 228. Validation — Profile

Project/Role/Media all same Profile.

---

# 229. Validation — dates

No impossible date values.

Future Project dates are not automatically invalid because upcoming work/performance may legitimately exist.

---

# 230. Publication and confidentiality

A booked/upcoming project may not yet be publicly announceable.

---

# 231. PRJ-INV-014 — Booked Does Not Imply Public

---

# 232. Embargo/private project

Must remain Draft/private until Human publication decision.

---

# 233. AI cannot infer embargo expiry.

---

# 234. Public visibility revocation

Must immediately gate current Project access.

---

# 235. Cache/Search cleanup follows.

---

# 236. PRJ-INV-015 — Stale Cache Cannot Keep Project Public

---

# 237. Public DTO

Contains only approved:

```text id="p0q0kp"
title
type
period
Role
description
selected RoleMedia
performances
```

---

# 238. Public DTO excludes

```text id="10l7ad"
source Opportunity details
Admin notes
Casting source
private workflow
visibility config
storage locators
```

---

# 239. Admin DTO

May include provenance and configuration, subject to authorization.

---

# 240. Commands — Projects

Canonical:

```text id="iylprd"
CreateProject
UpdateProject
UpdateProjectLocalization
UpdateProjectVisibility
PublishProject
ArchiveProject
RestoreProject
SetProjectFeatured
ReorderProjects
```

---

# 241. Commands — Roles

```text id="td4p76"
CreateRole
UpdateRole
UpdateRoleLocalization
UpdateRoleVisibility
PublishRole
ArchiveRole
RestoreRole
```

if role-level lifecycle enabled.

---

# 242. Commands — Role Media

```text id="hb68ql"
AttachRoleMedia
RemoveRoleMedia
ReorderRoleMedia
UpdateRoleMediaCaption
SetRoleMediaHomeShowcase
```

---

# 243. Commands — Performances

```text id="8e1rkj"
CreatePerformance
UpdatePerformance
CancelPerformance
ArchivePerformance
RestorePerformance
```

as appropriate.

---

# 244. Opportunity command

```text id="n4m0my"
CreateProjectDraftFromBookedOpportunity
```

---

# 245. Queries

Canonical:

```text id="ko1lqb"
GetPublicProjects
GetPublicProject
GetAdminProjects
GetAdminProject
GetProjectReadiness
ListQuestionnaireEligibleProjects
ListBuilderEligibleProjects
GetFeaturedProjects
GetHomeRoleShowcase
GetUpcomingPerformances
GetPastPerformances
```

---

# 246. AttachRoleMedia input

```text id="e6t4n8"
roleId
mediaAssetId
expectedRoleVersion / collectionVersion
```

---

# 247. Server transaction

```text id="b64gkc"
lock Role
↓
validate current count < 5
↓
validate Media
↓
insert RoleMedia
↓
version/Audit/Outbox as needed
```

---

# 248. Set Home Showcase input

```text id="zczye3"
roleMediaId
enabled
expectedVersion
```

---

# 249. Server transaction

If enabling:

```text id="f0p6qs"
lock Role
↓
validate role media active
↓
count current showcase < 2
↓
set true
```

---

# 250. ROLE-MED-010 — Count Rules Transaction-Safe

---

# 251. ReorderRoleMedia

Supports only active RoleMedia IDs of one Role.

---

# 252. RemoveRoleMedia

If `home_showcase=true`, remove showcase designation atomically.

---

# 253. Project publication

Server reloads Role state.

Does not trust browser-ready flag.

---

# 254. Create from Opportunity input

```text id="nm70wy"
opportunityId
expectedVersion
idempotencyKey
```

---

# 255. Server verifies

```text id="z0x171"
Opportunity stage = BOOKED
Human principal
same profile
no conflicting prior creation
```

---

# 256. Result

```text id="6ll2sw"
Project Draft
source Opportunity reference
```

---

# 257. No automatic publication.

---

# 258. Error taxonomy

At minimum:

```text id="rd5p48"
PROJECT_NOT_FOUND
PROJECT_TITLE_REQUIRED
PROJECT_TYPE_INVALID
PROJECT_ARCHIVED
PROJECT_NOT_READY
ROLE_NOT_FOUND
ROLE_PROJECT_MISMATCH
ROLE_NAME_REQUIRED
ROLE_MEDIA_LIMIT_EXCEEDED
ROLE_HOME_SHOWCASE_LIMIT_EXCEEDED
ROLE_MEDIA_DUPLICATE
ROLE_MEDIA_INVALID
ROLE_MEDIA_WRONG_PROFILE
ROLE_MEDIA_NOT_READY
PERFORMANCE_NOT_FOUND
PERFORMANCE_DATE_INVALID
PROJECT_QUESTIONNAIRE_NOT_ELIGIBLE
PROJECT_BUILDER_NOT_ELIGIBLE
OPPORTUNITY_NOT_BOOKED
OPPORTUNITY_PROJECT_ALREADY_CREATED
```

---

# 259. Concurrency

Projects/Roles use optimistic version.

---

# 260. Collection limits additionally use row locking/transaction.

---

# 261. PRJ-INV-016 — Optimistic Version Alone Does Not Protect Count Race

Role row lock or equivalent needed for 5/2 limits.

---

# 262. Reorder concurrency

Collection stale conflict rather than silent overwrite.

---

# 263. Featured race

If product limits featured count globally, that future limit must be transaction-safe.

No such count is currently imposed.

---

# 264. Primary Project

Not required as domain concept.

Use `featured`/ordering rather than inventing primary Project unless future UX requires it.

---

# 265. Audit

High-impact:

```text id="g7bza2"
publication
archive/restore
visibility
Role credit/name change
Featured selection
Home Showcase selection
Booked→Project creation
```

---

# 266. Role photo attach/remove

May be auditable or operational depending final audit policy.

---

# 267. Performance correction

Significant public date correction SHOULD be audited.

---

# 268. Outbox events

Suggested:

```text id="c009pu"
ProjectCreated
ProjectUpdated
ProjectPublished
ProjectArchived
ProjectVisibilityChanged
ProjectFeaturedChanged
RoleCreated
RoleUpdated
RoleMediaChanged
RoleHomeShowcaseChanged
PerformanceChanged
ProjectCreatedFromOpportunity
```

---

# 269. Consumers

```text id="4b9gbx"
Public projection
Homepage
Search
Questionnaire readiness
Builder eligibility
VOP
Analytics
Cache
```

---

# 270. Cache

Public Project pages can be cached.

---

# 271. Visibility change

Strong current authorization/projection gate first.

Eventual cache/search cleanup after.

---

# 272. Search versioning

Stale reindex cannot overwrite newer Project version.

---

# 273. Analytics

Relevant public events:

```text id="2puqhf"
projects_opened
project_viewed
role_media_opened
performance_viewed
```

---

# 274. Business analytics

May track which Projects lead to Questionnaire/contact engagement.

---

# 275. PRJ-INV-017 — Analytics Cannot Auto-Rewrite Filmography

---

# 276. Performance notification

Future optional capability could notify about upcoming public performance.

Not baseline unless explicitly configured.

---

# 277. Social Publishing

Project/Role Media can feed Social Draft.

---

# 278. Social post is independent Source/configuration.

---

# 279. Social publication does not modify Project.

---

# 280. Security

Draft/embargoed Projects unavailable publicly even by ID/slug guessing.

---

# 281. Sensitive source Opportunity

Never serialized Public.

---

# 282. Media delivery follows Role visibility.

---

# 283. Private Role photo

A public MediaAsset derivative elsewhere does not make private Role relation public automatically.

---

# 284. PRJ-INV-018 — Relation Context Governs Exposure

---

# 285. Archive

Project archive:

- removes Project current public;
- removes child Roles current public;
- removes Home Showcase;
- removes Builder/new Questionnaire eligibility;
- removes Public Search.

---

# 286. Historical Questionnaire

Unchanged.

---

# 287. Role Media originals

Not deleted.

---

# 288. Opportunity provenance

Retained.

---

# 289. Restore

Preferred:

```text id="63uodp"
ARCHIVED → DRAFT
```

---

# 290. PRJ-INV-019 — Restore Does Not Republish Child Roles

---

# 291. Role archive

Does not archive Project.

---

# 292. Project may remain public with other valid Roles.

---

# 293. Hard delete

Exceptional.

Dependency-aware.

---

# 294. Delete Role relation

Must inspect:

```text id="gp444j"
Questionnaire revisions
Builder snapshots
Home projections
Media usage
Audit/provenance
```

---

# 295. PRJ-INV-020 — Historical Credit Cannot Be Silently Erased by Cascade

---

# 296. Privacy/legal redaction

If professional credit must be removed, historical access/redaction follows DOC-094.

---

# 297. Migration

Legacy Projects need classification review.

---

# 298. Training migration

Records recognized as:

```text id="u1ncpa"
course
school
workshop
masterclass
training
```

move to Training.

---

# 299. PRJ-INV-021 — Migration Preserves Provenance

Record migration source IDs/reference in migration log.

---

# 300. Legacy Role photos

Map:

```text id="e02euz"
MediaAsset
+
RoleMedia
```

---

# 301. Legacy max violations

If old Role has >5 images:

do not silently delete.

Migration state:

```text id="6bx5al"
REQUIRES_REVIEW
```

Admin selects maximum 5 active role photos.

---

# 302. Legacy Home Showcase >2

Same.

No silent data loss.

---

# 303. Legacy performance dates

Preserve exact date precision.

---

# 304. Unknown role name

Do not invent.

Mark incomplete.

---

# 305. Anti-patterns

`PRJ-AP-001`  
Use Project `OTHER` for Training.

`PRJ-AP-002`  
Use Project for Emotional Portfolio.

`PRJ-AP-003`  
Store Project and Role as one text string only.

`PRJ-AP-004`  
Duplicate Project title independently in every Role.

`PRJ-AP-005`  
AI invents role importance.

`PRJ-AP-006`  
AI infers professional credit from costume/photo.

`PRJ-AP-007`  
Role photo becomes Main Portfolio automatically.

`PRJ-AP-008`  
Project photo satisfies mandatory Questionnaire Full Body.

`PRJ-AP-009`  
Allow 6+ active Role photos.

`PRJ-AP-010`  
Enforce 5-photo limit only in UI.

`PRJ-AP-011`  
Allow 3 Home Showcase photos.

`PRJ-AP-012`  
Home Showcase references file outside RoleMedia.

`PRJ-AP-013`  
Removing RoleMedia deletes MediaAsset.

`PRJ-AP-014`  
Archive Project deletes Role photos.

`PRJ-AP-015`  
Role “В образе” carousel shares Main Portfolio configuration.

`PRJ-AP-016`  
Upload date becomes project year.

`PRJ-AP-017`  
Year-only data stored as fake January 1.

`PRJ-AP-018`  
Performance location changes Profile city.

`PRJ-AP-019`  
Upcoming stored as manually editable boolean.

`PRJ-AP-020`  
Cancel one performance archives Project.

`PRJ-AP-021`  
Whole filmography dumped into Compact Questionnaire.

`PRJ-AP-022`  
Public Builder edits Project title/Role.

`PRJ-AP-023`  
Temporary Casting role label saved as actual Project Role.

`PRJ-AP-024`  
Booked Opportunity auto-publishes Project.

`PRJ-AP-025`  
AI creates professional credit automatically from Booked.

`PRJ-AP-026`  
Opportunity edits live-sync into published Project.

`PRJ-AP-027`  
Project creation deletes Opportunity.

`PRJ-AP-028`  
Public DTO contains Casting/Opportunity source details.

`PRJ-AP-029`  
One `is_public` controls Site/QNR/Builder.

`PRJ-AP-030`  
Child Role visible when parent Project is private.

`PRJ-AP-031`  
Restore Project automatically republishes.

`PRJ-AP-032`  
Archive Role archives parent Project.

`PRJ-AP-033`  
Current Project edit rewrites historical QuestionnaireRevision.

`PRJ-AP-034`  
Search indexes embargoed Project.

`PRJ-AP-035`  
SEO exposes private Project title before announcement.

`PRJ-AP-036`  
Analytics automatically sets Featured.

`PRJ-AP-037`  
AI/VOP autonomously selects Homepage “В образе”.

`PRJ-AP-038`  
Runtime AI required to display Project descriptions.

`PRJ-AP-039`  
Role Media count based on stale client count.

`PRJ-AP-040`  
Third Home Showcase wins due last-write and violates invariant.

`PRJ-AP-041`  
Cross-profile MediaAsset attached to Role.

`PRJ-AP-042`  
Performance time invented when unknown.

`PRJ-AP-043`  
Official project title machine-translated and treated as official title.

`PRJ-AP-044`  
BB-generated description automatically becomes public.

`PRJ-AP-045`  
Hard delete cascades into historical Questionnaire/Opportunity records.

`PRJ-AP-046`  
Migration silently discards 6th legacy Role photo.

`PRJ-AP-047`  
Migration silently changes Training into Project to avoid schema work.

`PRJ-AP-048`  
Featured means “objectively best project”.

`PRJ-AP-049`  
Role description stored globally on MediaAsset.

`PRJ-AP-050`  
Project page becomes production encyclopedia unrelated to actor journey.

---

# 306. Core invariants

`PRJ-INV-022`  
Project belongs one Actor Profile.

`PRJ-INV-023`  
Role belongs one Project.

`PRJ-INV-024`  
Project and Role remain distinct Source entities.

`PRJ-INV-025`  
Training remains separate.

`PRJ-INV-026`  
Emotional Portfolio remains separate.

`PRJ-INV-027`  
Achievement remains separate.

`PRJ-INV-028`  
Project type is controlled.

`PRJ-INV-029`  
Unknown professional facts remain unknown.

`PRJ-INV-030`  
Date precision is preserved.

`PRJ-INV-031`  
Project description and Role description remain distinct.

`PRJ-INV-032`  
AI cannot invent Project/Role facts.

`PRJ-INV-033`  
Save does not equal Publish.

`PRJ-INV-034`  
Archive does not equal Delete.

`PRJ-INV-035`  
Child Role cannot exceed parent Project public permission.

`PRJ-INV-036`  
Public/Admin Questionnaire/Builder eligibility remain independent.

`PRJ-INV-037`  
Featured is presentation configuration.

`PRJ-INV-038`  
Featured requires Human authority.

`PRJ-INV-039`  
Each active Role has maximum five RoleMedia photos.

`PRJ-INV-040`  
RoleMedia limit is transaction-safe.

`PRJ-INV-041`  
Each Role has maximum two Home Showcase photos.

`PRJ-INV-042`  
Home Showcase is subset of RoleMedia.

`PRJ-INV-043`  
Home Showcase limit is transaction-safe.

`PRJ-INV-044`  
RoleMedia removal does not delete MediaAsset.

`PRJ-INV-045`  
RoleMedia original remains immutable.

`PRJ-INV-046`  
Main Portfolio and RoleMedia remain separate professional contexts.

`PRJ-INV-047`  
Role photo cannot satisfy mandatory Main Portfolio requirements.

`PRJ-INV-048`  
Project media inclusion in Questionnaire is optional evidence.

`PRJ-INV-049`  
Questionnaire project selection is explicit.

`PRJ-INV-050`  
Historical Questionnaire freezes exact Project/Role snapshot.

`PRJ-INV-051`  
Current Project mutation never rewrites history.

`PRJ-INV-052`  
Public Builder cannot edit Project Source.

`PRJ-INV-053`  
Builder Generate revalidates Project/Role eligibility.

`PRJ-INV-054`  
Temporary Casting labels do not create saved Project facts.

`PRJ-INV-055`  
Performance belongs Project/Role professional context.

`PRJ-INV-056`  
Performance date is not upload date.

`PRJ-INV-057`  
Upcoming status is derived.

`PRJ-INV-058`  
Performance city does not modify Profile city.

`PRJ-INV-059`  
One cancelled Performance does not archive Project.

`PRJ-INV-060`  
Search indexes only permitted current public Project facts.

`PRJ-INV-061`  
SEO cannot exceed Public visibility.

`PRJ-INV-062`  
Booked Opportunity does not imply Public Project.

`PRJ-INV-063`  
Booked→Project requires Human command.

`PRJ-INV-064`  
Booked→Project creates Draft.

`PRJ-INV-065`  
Booked→Project creation is idempotent.

`PRJ-INV-066`  
Project preserves source Opportunity provenance.

`PRJ-INV-067`  
Opportunity remains after Project creation.

`PRJ-INV-068`  
Project does not live-sync blindly from Opportunity.

`PRJ-INV-069`  
AI cannot create Project credit automatically.

`PRJ-INV-070`  
BB remains Draft-only.

`PRJ-INV-071`  
VOP cannot publish/select Featured/Home Showcase autonomously.

`PRJ-INV-072`  
Projects remain fully usable without AI.

`PRJ-INV-073`  
Cross-profile RoleMedia injection is rejected.

`PRJ-INV-074`  
Visibility revocation blocks stale current public delivery.

`PRJ-INV-075`  
Archive Project removes child current public exposure.

`PRJ-INV-076`  
Archive does not delete RoleMedia originals.

`PRJ-INV-077`  
Restore does not auto-publish.

`PRJ-INV-078`  
Role archive does not automatically archive Project.

`PRJ-INV-079`  
Hard deletion is dependency-aware.

`PRJ-INV-080`  
Historical professional credit is not removed by uncontrolled cascade.

`PRJ-INV-081`  
Migration does not fabricate Role/title/type facts.

`PRJ-INV-082`  
Legacy role photo excess requires Human review.

`PRJ-INV-083`  
Migration preserves professional provenance.

`PRJ-INV-084`  
Project Public page remains actor-focused.

`PRJ-INV-085`  
Homepage evidence remains curated rather than exhaustive.

`PRJ-INV-086`  
Role photo count and Home Showcase count are independent invariants.

`PRJ-INV-087`  
Project route does not grant access based only on known ID/slug.

`PRJ-INV-088`  
Current Project changes propagate Search/cache readiness without altering Source history.

`PRJ-INV-089`  
Outbox/provider failures cannot undo committed Project Source mutation.

`PRJ-INV-090`  
Project domain never becomes alternate Profile/Training/Emotional data store.

---

# 307. E2E-PRJ-001 — Create Draft Project

Create Project with valid type/title.

Expected Draft, non-public.

---

# 308. E2E-PRJ-002 — Create Role

Role attaches to correct Project.

---

# 309. E2E-PRJ-003 — Role without Project

Rejected.

---

# 310. E2E-PRJ-004 — Cross-profile Project/Role

Rejected.

---

# 311. E2E-PRJ-005 — Project OTHER

Valid real production not covered by taxonomy may use OTHER.

---

# 312. E2E-PRJ-006 — Training as OTHER

Attempt course as Project.

Admin workflow routes/requires Training instead.

---

# 313. E2E-PRJ-007 — Year precision

Store `2025` as year-only.

System does not fabricate exact January 1 date.

---

# 314. E2E-PRJ-008 — Role AI inference

AI suggests `LEAD` without source evidence.

No Source mutation occurs.

---

# 315. E2E-PRJ-009 — Attach first Role photo

Valid same-profile IMAGE succeeds.

---

# 316. E2E-PRJ-010 — Five Role photos

Five active relations allowed.

---

# 317. E2E-PRJ-011 — Sixth Role photo

Expected:

```text id="sj1iv6"
ROLE_MEDIA_LIMIT_EXCEEDED
```

---

# 318. E2E-PRJ-012 — Concurrent fifth/sixth race

Role has four photos.

Two concurrent adds execute.

Expected one succeeds, one fails; final count = 5.

---

# 319. E2E-PRJ-013 — Duplicate RoleMedia

Same asset attached twice to same Role.

Rejected/warned according to uniqueness rule.

---

# 320. E2E-PRJ-014 — Cross-profile RoleMedia

Rejected.

---

# 321. E2E-PRJ-015 — Two Home Showcase

Two RoleMedia selected.

Allowed.

---

# 322. E2E-PRJ-016 — Third Home Showcase

Expected:

```text id="oilr3p"
ROLE_HOME_SHOWCASE_LIMIT_EXCEEDED
```

---

# 323. E2E-PRJ-017 — Concurrent showcase race

One slot remains; two requests race.

Final showcase count never exceeds 2.

---

# 324. E2E-PRJ-018 — Showcase subset

Attempt arbitrary MediaAsset as showcase without RoleMedia.

Rejected.

---

# 325. E2E-PRJ-019 — Remove showcased RoleMedia

Relation removal atomically removes its showcase state.

---

# 326. E2E-PRJ-020 — Original retained

MediaAsset remains in Media Library.

---

# 327. E2E-PRJ-021 — Main Portfolio separation

Role photo does not automatically appear in Main Portfolio.

---

# 328. E2E-PRJ-022 — Questionnaire mandatory Full Body

Role image cannot satisfy mandatory Full Body.

---

# 329. E2E-PRJ-023 — Public Project

Published + public eligible Project appears.

---

# 330. E2E-PRJ-024 — Private Role under Public Project

If role-level visibility enabled and Role hidden, Role is absent from Public DTO.

---

# 331. E2E-PRJ-025 — Public Role under private Project

Not exposed.

---

# 332. E2E-PRJ-026 — Questionnaire-only Project

Public hidden, Admin Questionnaire allowed.

Selectable only in prepared questionnaire context.

---

# 333. E2E-PRJ-027 — Builder-only Project

Public hidden, Builder allowed.

Eligible only Builder projection.

---

# 334. E2E-PRJ-028 — Questionnaire explicit selection

Only selected Projects appear.

No whole-filmography dump.

---

# 335. E2E-PRJ-029 — Historical Questionnaire

Publish with Role description A.

Change current description to B.

Historical Revision remains A.

---

# 336. E2E-PRJ-030 — Builder tamper

Public Builder submits modified Project title.

Server ignores/rejects supplied value and loads Source.

---

# 337. E2E-PRJ-031 — Builder eligibility revoked

Selected Project becomes disallowed before Generate.

Generation removes/blocks selection.

---

# 338. E2E-PRJ-032 — Performance exact date

Add performance date.

Correct date appears chronologically.

---

# 339. E2E-PRJ-033 — Performance time unknown

No fake midnight time displayed/stored as fact.

---

# 340. E2E-PRJ-034 — Performance city

Performance city differs from Profile city.

Profile remains unchanged.

---

# 341. E2E-PRJ-035 — Cancel Performance

Performance state changes; Project remains active.

---

# 342. E2E-PRJ-036 — Upcoming derived

Move test clock past performance date.

Upcoming classification changes without DB boolean update.

---

# 343. E2E-PRJ-037 — Booked opportunity

Opportunity reaches Booked through Human transition.

No Project appears automatically.

---

# 344. E2E-PRJ-038 — Create Project Draft

Human executes command.

One Project Draft with source Opportunity reference created.

---

# 345. E2E-PRJ-039 — Retry Project creation

Same idempotency key returns same Draft.

---

# 346. E2E-PRJ-040 — Booked project private

Generated Draft absent Public until explicitly reviewed/published.

---

# 347. E2E-PRJ-041 — Opportunity later changes

Project Source does not silently change.

---

# 348. E2E-PRJ-042 — Provenance

Admin can navigate Project → originating Opportunity.

---

# 349. E2E-PRJ-043 — AI unavailable

Manual Project/Role CRUD, RoleMedia and publication work normally.

---

# 350. E2E-PRJ-044 — BB description

Generated draft does not update Project until Human Apply.

---

# 351. E2E-PRJ-045 — BB unsupported fact

Draft invents director.

It is not applied automatically.

---

# 352. E2E-PRJ-046 — Search public

Published public Project searchable by title/Role.

---

# 353. E2E-PRJ-047 — Search private

Embargoed Draft absent.

---

# 354. E2E-PRJ-048 — Stale Search

Project visibility revoked before index catches up.

Current hydration does not expose result.

---

# 355. E2E-PRJ-049 — SEO private

Private Project absent from metadata/sitemap.

---

# 356. E2E-PRJ-050 — Home showcase

Selected max-two RoleMedia appear in intended Homepage “В образе” context.

---

# 357. E2E-PRJ-051 — Main Portfolio carousel independence

Changing Main Portfolio carousel does not alter Role showcase.

---

# 358. E2E-PRJ-052 — Archive Project

Project/Role/Home Showcase disappear from current Public/Builder/new QNR.

---

# 359. E2E-PRJ-053 — Historical after archive

Published historical Questionnaire retains exact Project/Role snapshot.

---

# 360. E2E-PRJ-054 — Archive does not purge images

Role MediaAssets remain.

---

# 361. E2E-PRJ-055 — Restore

Project returns Draft/non-public.

---

# 362. E2E-PRJ-056 — Archive Role

One Role archived; Project/other Roles remain.

---

# 363. E2E-PRJ-057 — Visibility cache

Cached Project becomes private.

Next secure Public request does not expose stale Project.

---

# 364. E2E-PRJ-058 — Migration Training

Legacy Project “Acting Masterclass” correctly migrates to Training rather than remaining Project.

---

# 365. E2E-PRJ-059 — Legacy 7 photos

Migration preserves all source MediaAssets, marks RoleMedia selection as requiring review, does not silently delete two.

---

# 366. E2E-PRJ-060 — Legacy 3 Home Showcase

Migration requires Human reduction to max 2.

---

# 367. E2E-PRJ-061 — Public DTO provenance safety

Inspect Public Project DTO.

No Opportunity/Casting/Feedback internal IDs/data.

---

# 368. E2E-PRJ-062 — Concurrent Role edit

Stale version cannot overwrite newer role description.

---

# 369. E2E-PRJ-063 — Featured

Human selects Project Featured.

AI recommendation alone does not alter state.

---

# 370. E2E-PRJ-064 — VOP role media limit

VOP detects imported inconsistency >5 but does not auto-delete images.

---

# 371. E2E-PRJ-065 — Home decision journey

Homepage selected Project card routes to actor-focused Project/Role detail and useful next CTA without dead end.

---

# 372. Domain diagram

```text id="6g5izk"
Actor Profile
    │
    ▼
  Project
    │
    ├───────────────┐
    ▼               ▼
   Role        Performances
    │
    ▼
 RoleMedia
    │
    ▼
 MediaAsset
```

---

# 373. Role Media limits diagram

```text id="ud1skq"
Role
 │
 ├── Photo 1 ──┐
 ├── Photo 2 ──┤
 ├── Photo 3   │  max active RoleMedia = 5
 ├── Photo 4   │
 └── Photo 5 ──┘

Home “В образе”:
subset of those five
max = 2
```

---

# 374. Questionnaire diagram

```text id="73utwk"
Current Project / Role
       ↓
Eligibility
       ↓
Explicit Questionnaire Selection
       ↓
QuestionnaireRevision Snapshot
       ↓
HTML / PDF

Project evidence never substitutes
mandatory Main Portfolio Close-Up / Full Body.
```

---

# 375. Opportunity provenance diagram

```text id="v34vtq"
Feedback
   ↓
Casting
   ↓
Opportunity
   ↓
BOOKED
   ↓
Human Command
   ↓
Project DRAFT
   ↓
Human Review
   ↓
Publish
```

---

# 376. Performance diagram

```text id="137jni"
Project / Role
     ↓
Performance
├── Date
├── Optional Time
├── Optional Venue
├── Optional City
└── Status

Upcoming/Past = derived from date/status
```

---

# 377. Visibility diagram

```text id="lld865"
Project
  │
  ├── Public Site
  ├── Admin Questionnaire
  └── Public Builder
          │
          ▼
        Role
          │
          └── cannot exceed parent permissions
```

---

# 378. Quality gate

Перед implementation должны быть подтверждены:

- [ ] Project entity;
- [ ] Role entity;
- [ ] controlled Project taxonomy;
- [ ] Project vs Training boundary;
- [ ] Project vs Emotional boundary;
- [ ] project/role localization;
- [ ] Project lifecycle;
- [ ] Role lifecycle/effective parent visibility;
- [ ] contextual visibility;
- [ ] explicit display order;
- [ ] Featured project semantics;
- [ ] RoleMedia relation;
- [ ] maximum 5 active Role photos;
- [ ] transactional RoleMedia count guard;
- [ ] maximum 2 Home Showcase photos;
- [ ] Home Showcase subset invariant;
- [ ] transaction-safe showcase count;
- [ ] Main Portfolio separation;
- [ ] Performance model;
- [ ] date precision rules;
- [ ] Questionnaire project selection;
- [ ] Project photos cannot substitute mandatory Portfolio photos;
- [ ] Builder selection-only semantics;
- [ ] Casting AI boundaries;
- [ ] BB draft-only integration;
- [ ] Opportunity→Booked→Project Draft provenance;
- [ ] Human command;
- [ ] idempotency;
- [ ] Search/SEO;
- [ ] Homepage projections;
- [ ] archive/restore;
- [ ] migration from legacy Project Other;
- [ ] E2E concurrency/limits/history tests.

---

# 379. Acceptance criteria

`AC-PRJ-001`  
Project и Role существуют как разные entities.

`AC-PRJ-002`  
Training не хранится как Project.

`AC-PRJ-003`  
Emotional Portfolio не хранится как Project.

`AC-PRJ-004`  
Project types являются controlled values.

`AC-PRJ-005`  
Unknown Project facts не выдумываются.

`AC-PRJ-006`  
Role belongs exactly one Project.

`AC-PRJ-007`  
Project/Role descriptions могут локализоваться.

`AC-PRJ-008`  
AI cannot invent role importance/credits.

`AC-PRJ-009`  
Project Draft не становится Public при Save.

`AC-PRJ-010`  
Role public exposure cannot exceed parent Project.

`AC-PRJ-011`  
Public/Questionnaire/Builder permissions remain context-specific.

`AC-PRJ-012`  
RoleMedia uses immutable MediaAsset.

`AC-PRJ-013`  
Active RoleMedia count never exceeds 5.

`AC-PRJ-014`  
5-photo limit is transaction-safe.

`AC-PRJ-015`  
Home “В образе” count never exceeds 2 per Role.

`AC-PRJ-016`  
Home Showcase is subset of RoleMedia.

`AC-PRJ-017`  
2-photo limit is transaction-safe.

`AC-PRJ-018`  
Removing RoleMedia does not delete MediaAsset.

`AC-PRJ-019`  
RoleMedia does not automatically become Main Portfolio.

`AC-PRJ-020`  
Role photo cannot satisfy mandatory Questionnaire Close-Up/Full Body.

`AC-PRJ-021`  
Questionnaire Projects are explicit selectable evidence.

`AC-PRJ-022`  
Compact questionnaire does not automatically include full filmography.

`AC-PRJ-023`  
Historical Questionnaire freezes exact Project/Role values.

`AC-PRJ-024`  
Current Project changes do not rewrite historical snapshot.

`AC-PRJ-025`  
Public Builder cannot edit Project/Role facts.

`AC-PRJ-026`  
Builder Generate revalidates selected Projects/Roles.

`AC-PRJ-027`  
Temporary Casting labels do not mutate Projects.

`AC-PRJ-028`  
Performance supports exact professional dates without inventing missing time.

`AC-PRJ-029`  
Performance city remains distinct from Profile city.

`AC-PRJ-030`  
Upcoming state is derived.

`AC-PRJ-031`  
Cancelled performance does not archive Project.

`AC-PRJ-032`  
Booked Opportunity does not auto-create/publish Project.

`AC-PRJ-033`  
Human command creates Project Draft from Booked.

`AC-PRJ-034`  
Creation retains Opportunity provenance.

`AC-PRJ-035`  
Retry does not create duplicate Project.

`AC-PRJ-036`  
Opportunity remains after Project creation.

`AC-PRJ-037`  
AI cannot create professional Project credits automatically.

`AC-PRJ-038`  
BB output remains Draft until Human Apply.

`AC-PRJ-039`  
Search/SEO expose only current allowed Project information.

`AC-PRJ-040`  
Featured/Home Showcase selection remains Human-controlled.

`AC-PRJ-041`  
Archive removes Project from current surfaces without deleting history/media.

`AC-PRJ-042`  
Restore does not auto-publish.

`AC-PRJ-043`  
Legacy Training records migrate to Training.

`AC-PRJ-044`  
Legacy Role photo over-limit is preserved for review rather than silently deleted.

`AC-PRJ-045`  
Core Project workflow functions without AI.

`AC-PRJ-046`  
All 5/2 count limits, provenance and historical rules have deterministic E2E coverage.

---

# 380. Финальная доктрина

> **Projects Module является единственным Master Source профессиональной фильмографии и проектного опыта актрисы. Project описывает production, Role — подтверждённое участие актрисы, Performance — конкретный профессиональный показ/выступление, а RoleMedia связывает роль с immutable MediaAssets. Одна роль может иметь максимум пять активных role photos, из которых максимум две могут быть Human-selected для Homepage “В образе”. Эти фотографии остаются отдельным контекстом и не подменяют Main Portfolio Close-Up/Full Body. Training и Emotional Portfolio являются самостоятельными доменами. Booked Opportunity может стать только Project Draft через явную Human-команду с сохранением provenance и idempotency; ни AI, ни Opportunity workflow не имеют права автоматически создавать или публиковать профессиональный credit.**