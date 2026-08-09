# EMOTIONAL PORTFOLIO MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация эмоциональных съёмок, shooting date, immutable source photos, публикации и передачи материалов в Emotional Grid

**Целевой файл:** `docs/modules/emotional-portfolio.md`  
**Документ:** DOC-113  
**Статус:** ✅ Completed  
**Тип:** Module / Emotional Portfolio / Shooting Sessions / Professional Photography

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
- `docs/modules/portfolio.md`

---

# 1. Назначение модуля

Emotional Portfolio — самостоятельный профессиональный раздел сайта актрисы, предназначенный для демонстрации эмоционального диапазона в рамках конкретных съёмок.

Он не является:

- разновидностью Main Portfolio;
- подразделом Projects;
- набором Role Photos;
- автоматически создаваемой AI-галереей;
- самим Emotional Grid.

Модуль отвечает за:

1. создание Emotional Shooting Session;
2. обязательную дату съёмки;
3. принадлежность фотографий конкретной съёмке;
4. подписи и порядок кадров;
5. публикацию полной эмоциональной серии;
6. выбор основной/актуальной эмоциональной съёмки;
7. связь с Emotional Grid;
8. eligibility для Questionnaire/Builder;
9. public emotional page;
10. сохранение immutable source photos;
11. lifecycle/archive;
12. readiness;
13. Search/SEO/Analytics integration;
14. AI/VOP assistance без изменения профессиональной истины.

---

# 2. Главная доктрина

> **Emotional Portfolio представляет реальную подтверждённую эмоциональную съёмку как отдельный профессиональный Source Context: дата съёмки и состав серии принадлежат Emotional Session, исходные фотографии принадлежат Media Library, а Emotional Grid является отдельной производной композиционной сущностью.**

Canonical:

```text
MediaAsset originals
        ↓
Emotional Session
        ↓
Session Media + order/caption
        ↓
Published Emotional Portfolio
        ↓
Emotional Grid Draft
        ↓
Human-confirmed Grid Revision
```

---

# 3. Fundamental separation

```text
EmotionalSession
≠
MediaAsset
≠
Main Portfolio
≠
Role Media
≠
EmotionalGrid
≠
Questionnaire Emotional Block
```

---

# 4. Module identifiers

Используются:

```text
EMO-*
EMO-SES-*
EMO-MED-*
EMO-DATE-*
EMO-PUB-*
EMO-VIS-*
EMO-GRID-*
EMO-QNR-*
EMO-BLD-*
EMO-AI-*
EMO-INV-*
EMO-AP-*
E2E-EMO-*
```

---

# 5. Business purpose

Emotional Portfolio должен позволять casting professional быстро проверить:

- естественность мимики;
- диапазон эмоциональных состояний;
- работу лица в крупном плане;
- последовательность кадров одной съёмки;
- актуальность материала по дате;
- наличие готовой компактной Emotional Grid для анкеты.

---

# 6. Business value of shooting date

Дата эмоциональной съёмки является обязательным trust signal.

Она отвечает на вопрос:

> насколько актуально представленное эмоциональное состояние и внешность?

---

# 7. EMO-DATE-001 — Shooting Date Mandatory

Для публикации Emotional Session поле:

```text
shooting_date
```

обязательно.

---

# 8. Draft exception

Draft может временно существовать без даты.

Но:

```text
missing shooting_date
→ publication BLOCKED
```

---

# 9. Shooting date type

Используется:

```text
date
```

а не timestamp.

Wire:

```text
YYYY-MM-DD
```

---

# 10. EMO-DATE-002 — Shooting Date Is Calendar Fact

Она не должна смещаться из-за timezone.

---

# 11. EXIF is not authoritative

Дата EXIF:

- может отсутствовать;
- может быть неправильной;
- может отражать export/edit;
- может иметь timezone ambiguity.

---

# 12. EMO-DATE-003 — EXIF May Suggest, Never Confirm

EXIF может помочь Admin, но Human сохраняет фактическую `shooting_date`.

---

# 13. Future precision

Если известна только приблизительная дата, система не должна выдумывать точный день.

Baseline требует точную дату для published Emotional Session.

Если позднее потребуется month-only/year-only precision — это отдельное расширение модели.

---

# 14. Emotional Session

`EmotionalSession` — aggregate съёмки.

Representative fields:

```text
id
profile_id
title?
description?
shooting_date
publication_state
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
is_primary
display_order
version
created_at
updated_at
archived_at
```

---

# 15. Session media

Связь:

```text
EmotionalSession
    ↓
EmotionalSessionMedia
    ↓
MediaAsset
```

---

# 16. Session Media owns

Контекстные свойства:

```text
caption
display_order
visibility within session if needed
selection state
```

---

# 17. MediaAsset owns

```text
original binary
checksum
MIME
dimensions
technical derivatives
```

---

# 18. EMO-INV-001 — Shooting Date Belongs to Session

Не к каждому MediaAsset.

---

# 19. Почему

Одна эмоциональная съёмка обычно создаётся как серия в одну дату.

Duplicating date per photo creates inconsistency risk.

---

# 20. EMO-INV-002 — Emotional Meaning Does Not Modify MediaAsset

Например:

```text
“радость”
“сомнение”
“гнев”
```

если такие labels используются, они относятся к session/context metadata, а не глобальному MediaAsset.

---

# 21. Emotional labels

Модуль MAY поддерживать optional Human-authored labels/captions для отдельных кадров.

Examples:

```text
Спокойствие
Удивление
Раздражение
Решимость
```

---

# 22. Labels are optional

Эмоциональная серия не должна превращаться в искусственную классификацию каждого выражения, если professional value от этого не повышается.

---

# 23. EMO-INV-003 — AI Emotion Recognition Is Not Professional Truth

AI может предложить визуальный label для Admin review.

Но не должен автоматически утверждать эмоциональное состояние человека как факт.

---

# 24. Why

Выражение лица неоднозначно и контекстно.

---

# 25. Public labels

Только Human-approved labels.

---

# 26. Source photos

Каждая фотография Emotional Session должна быть:

```text
MediaAsset.type = IMAGE
same profile
not quarantined
technically usable
```

---

# 27. EMO-MED-001 — Same Profile

Cross-profile media injection запрещён.

---

# 28. EMO-MED-002 — Image Only

Video/audio/document не являются source photo Emotional Session.

Если позже появится Emotional Video — это отдельное capability.

---

# 29. Immutable originals

Original photographs never modified.

---

# 30. EMO-MED-003 — No Destructive Crop

Любой crop/pan/scale для Emotional Grid существует отдельно.

---

# 31. Public full Emotional Portfolio

Обычная публичная страница Emotional Portfolio должна показывать исходные кадры с сохранением framing.

---

# 32. Default rendering

```text
contain
source aspect ratio
no appearance edit
```

---

# 33. EMO-MED-004 — Grid Crop Does Not Affect Full Emotional Portfolio

Крайне важный invariant.

---

# 34. Photo order

Emotional Session имеет управляемый порядок кадров.

---

# 35. Why order matters

Эмоциональная серия может быть осмысленной последовательностью.

---

# 36. EMO-MED-005 — CreatedAt Is Not Display Order

---

# 37. Reordering

Semantic command:

```text
ReorderEmotionalSessionMedia
```

---

# 38. Reorder validation

- same session;
- no duplicate IDs;
- no missing required IDs;
- same profile;
- expected collection version/current state.

---

# 39. Session title

Optional.

Examples:

```text
Эмоциональная съёмка — июль 2026
Emotional Range — July 2026
```

---

# 40. Title does not replace date

Even if title says “July 2026”:

`shooting_date` remains authoritative.

---

# 41. Session description

Optional professional contextual text.

Could describe:

- purpose of session;
- photographer/context if desired;
- general note.

---

# 42. Description cannot invent projects/roles

It is editorial text, not alternate Project record.

---

# 43. Localization

Title/description/captions MAY have locale-aware content.

At minimum public module should support existing site locale strategy.

---

# 44. EMO-INV-005 — Runtime AI Translation Is Not Saved Localization

Same doctrine as Profile.

---

# 45. Session lifecycle

Canonical:

```text
DRAFT
PUBLISHED
ARCHIVED
```

---

# 46. DRAFT

Allows:

- missing date temporarily;
- incomplete photo set;
- ordering;
- captions;
- Grid work;
- preview.

---

# 47. PUBLISHED

Requires readiness.

Can enter Public Site if:

```text
publication = PUBLISHED
AND show_on_public_site = true
```

---

# 48. ARCHIVED

No longer part of current public/emotional selection.

Historical references remain.

---

# 49. EMO-INV-006 — Published ≠ Visible Everywhere

Same as other modules.

---

# 50. Visibility triplet

Canonical:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 51. EMO-VIS-001 — Independent Dimensions

No implicit relation.

---

# 52. Public site permission

Controls full Emotional Portfolio current visibility.

---

# 53. Admin Questionnaire permission

Controls whether approved Emotional Grid/session representation may be selected into prepared Questionnaire.

---

# 54. Public Builder permission

Controls Builder eligibility of approved Emotional representation.

---

# 55. Important nuance

Questionnaire does **not** select individual Emotional source photos.

Therefore session-level permission alone is not sufficient: selected Grid/Revision must also be eligible.

---

# 56. EMO-QNR-001 — No Individual Emotional Photos in Questionnaire

Canonical Questionnaire Emotional representation:

```text
approved Emotional Grid composite
+
shooting date
+
clickable link to full Emotional Portfolio
```

---

# 57. Explicit prohibition

Questionnaire must not include:

```text
individual Emotional source photos
emotion-by-emotion image rows
raw session gallery
```

as Emotional block.

---

# 58. Why

Questionnaire should remain compact and professional.

Full expressive detail lives on Emotional Portfolio page.

---

# 59. Emotional Grid relationship

`EmotionalGrid` belongs to exactly one Emotional Session.

---

# 60. EMO-GRID-001 — Grid Sources Come From One Session

A Grid cannot arbitrarily mix photographs from different Emotional Sessions.

---

# 61. Why

Grid should represent one real shooting context/date.

---

# 62. Grid creation

Flow:

```text
EmotionalSession
    ↓
eligible session photos
    ↓
Create EmotionalGrid Draft
    ↓
choose exact dimensions
    ↓
assign photos/crops
    ↓
Human confirm each cell
    ↓
Finalize Revision
```

Detailed semantics DOC-114.

---

# 63. Session can have multiple Grids

Supported.

---

# 64. Example

One session may have:

```text
2x4 Grid
4x4 Grid
3x4 Grid
```

for different uses.

---

# 65. Primary Grid

Session/module may designate one current Primary Emotional Grid.

---

# 66. EMO-GRID-002 — Primary Grid Must Belong to Same Session

---

# 67. Primary Grid readiness

Must reference:

```text
finalized valid GridRevision
```

not editable Draft.

---

# 68. Session publication without Grid

Full Emotional Portfolio MAY be publishable without a Grid if business rules allow.

But:

```text
Questionnaire Emotional block
```

requires approved Grid.

---

# 69. Recommended readiness distinction

```text
Emotional Portfolio Public Ready
≠
Emotional Questionnaire Ready
```

---

# 70. Public Emotional readiness

At minimum:

```text
shooting_date present
>= 1 valid session image
PUBLISHED
public permission
```

---

# 71. Professional recommended readiness

Prefer meaningful series, not one accidental photo.

Exact minimum photo count should remain product-configurable unless later formalized.

---

# 72. Questionnaire Emotional readiness

Requires:

```text
eligible Emotional Session
eligible finalized Emotional GridRevision
shooting date
public-safe full Emotional Portfolio URL
```

---

# 73. Builder Emotional readiness

Requires:

```text
Builder-eligible session/grid
current valid GridRevision
shooting date
safe full portfolio link
```

---

# 74. EMO-INV-007 — Readiness Is Contextual

---

# 75. Primary Emotional Session

The site may mark one Emotional Session as primary/current.

---

# 76. Purpose

Used by:

- Homepage emotional section;
- default Questionnaire emotional block;
- Casting Quick View;
- public Emotional landing.

---

# 77. EMO-SES-001 — At Most One Primary Current Emotional Session

If primary semantics are enabled.

---

# 78. Draft period

Can have none.

Readiness may require one for homepage feature.

---

# 79. Primary switch

Explicit Human action.

---

# 80. EMO-SES-002 — AI/VOP Cannot Set Primary Automatically

Can recommend only.

---

# 81. Primary Session and Primary Grid

Different concepts:

```text
Primary Emotional Session
Primary Grid within Session
```

Do not conflate.

---

# 82. Example

Session S2 is primary.

Inside S2, Grid G4x4 may be primary.

---

# 83. Public route

Recommended stable route concept:

```text
/emotional
```

and optionally:

```text
/emotional/{session-slug-or-id}
```

Exact routing implementation deferred.

---

# 84. Public Emotional landing

Should prioritize:

```text
primary/latest appropriate session
shooting date
Grid preview if available
source gallery
navigation between published sessions
```

---

# 85. Public full emotional page

Must clearly show date.

---

# 86. EMO-DATE-004 — Shooting Date Must Be Visible in Public Emotional Context

Where Session is publicly presented.

---

# 87. Reason

It is professionally relevant trust metadata.

---

# 88. Date formatting

Locale-specific display:

```text
27 июля 2026
July 27, 2026
```

Source remains `2026-07-27`.

---

# 89. Sorting sessions

Default:

```text
shooting_date DESC
```

reasonable for recency.

Admin MAY have explicit display ordering/primary override.

---

# 90. EMO-INV-008 — Upload Date Is Not Shooting Recency

---

# 91. Older session

Can remain public for range/history if desired.

---

# 92. Primary/current session

Should not automatically become latest solely due upload/import timestamp.

Human publication/primary policy remains authority.

---

# 93. Homepage integration

Canonical homepage sequence includes:

```text
Portfolio
↓
Video
↓
Emotional
↓
Selected Projects
```

---

# 94. Homepage Emotional presentation

Should be compact:

- primary Grid preview, if available;
- shooting date;
- CTA to full Emotional Portfolio.

---

# 95. Homepage should not dump whole emotional gallery

Keep professional decision journey concise.

---

# 96. Homepage grid click

Opens full Emotional Portfolio.

---

# 97. EMO-INV-009 — Homepage Emotional Preview Is Projection

No duplicate photo ownership.

---

# 98. Casting Quick View integration

May show:

```text
primary emotional grid
shooting date
link to emotional portfolio
```

---

# 99. Search inside profile

Search can support:

```text
emotional
эмоции
```

as navigation to Emotional Portfolio.

---

# 100. Public Search

Could index:

- public session title;
- public description;
- date;
- route.

But not raw AI labels or hidden photos.

---

# 101. EMO-INV-010 — Public Search Respects Session Visibility

---

# 102. Public hidden Session

Absent from:

```text
Public Search
SEO
sitemap
Homepage
ordinary navigation
```

---

# 103. Builder-only Session

May still be available through Builder Projection if allowed.

---

# 104. Prepared Questionnaire-only Session

May be used by Admin Questionnaire even if ordinary public gallery hidden, **but full-portfolio hyperlink requires safe reachable target**.

---

# 105. Critical link issue

Questionnaire emotional block requires:

```text
clickable link to full emotional portfolio
```

If Session is not ordinary public, the link must be:

- explicit token/casting-safe scoped route; or
- Questionnaire readiness should block use.

---

# 106. EMO-QNR-002 — Never Emit Dead or Private Admin Link

---

# 107. Baseline preferred

For ordinary public Questionnaire Emotional block:

selected Session should have public-safe emotional portfolio URL.

---

# 108. Token-scoped future

Casting-specific Questionnaire may use explicit token-scoped Emotional access if access architecture supports it.

---

# 109. Questionnaire projection

Canonical DTO:

```text
EmotionalQuestionnaireBlockDTO {
  gridComposite
  shootingDate
  fullPortfolioUrl
}
```

---

# 110. No source photo list

Normative.

---

# 111. QR

Questionnaire MAY render QR for `fullPortfolioUrl` according to QR settings.

---

# 112. QR target safety

Must be:

```text
public
or
explicit token-safe
```

Never:

```text
/admin/...
storage locator
private asset route
```

---

# 113. EMO-QNR-003 — QR Encodes Exact Canonical Emotional Portfolio URL

---

# 114. Historical Questionnaire

Freezes:

```text
GridRevision identity
composite representation
shooting date
fullPortfolioUrl as published/snapshotted
QR intent
```

---

# 115. Current session later changes

Historical Questionnaire content unchanged semantically.

---

# 116. Access revocation

If privacy/security requires old full-portfolio URL to stop working:

Revision remains historically unchanged, but delivery/access can be revoked according to DOC-092/DOC-094.

---

# 117. Public Builder

Builder Emotional section should expose only eligible approved composites.

---

# 118. Builder does not expose source-cell editing

Public visitor cannot:

- choose arbitrary emotional photos;
- create Grid;
- crop cells;
- change shooting date.

---

# 119. EMO-BLD-001 — Builder Selects Approved Representation

---

# 120. Builder may choose

If multiple allowed Emotional sessions/grids exist:

```text
which approved Grid representation to include
```

according to template.

---

# 121. Builder preview

Shows:

```text
composite
shooting date
link
```

matching eventual PDF semantics.

---

# 122. Builder Generate revalidation

Immediately before snapshot:

```text
Session still eligible
GridRevision still eligible
session belongs profile
date present
link safe
```

---

# 123. Visibility revoked mid-session

Selected item must be removed/block generation.

---

# 124. Profile relationship

Emotional Session belongs profile.

---

# 125. It does not duplicate Profile parameters.

---

# 126. Main Portfolio relationship

A MediaAsset may technically appear both in Main Portfolio and Emotional Session if intentionally configured.

---

# 127. Recommended semantics

Do not automatically reuse emotional source images as Main Portfolio categories.

Explicit separate PortfolioItem required.

---

# 128. EMO-INV-011 — Membership Does Not Cross-Promote Automatically

---

# 129. Project relationship

Emotional Session is not a Project.

Even if shoot occurred during production.

---

# 130. No “Other Project” workaround

Emotional materials stay standalone.

---

# 131. Training relationship

None as Source.

---

# 132. Media replacement

Session media relation may be changed by explicit Admin command.

---

# 133. If GridRevision uses old MediaAsset

Historical Grid remains tied to old exact source.

---

# 134. EMO-MED-006 — Replacing Session Photo Does Not Rewrite Existing GridRevision

---

# 135. Current Grid Draft

If referenced Media is removed/replaced, Draft becomes stale/invalid and needs review.

---

# 136. Session photo removal

Before removal, dependency resolver checks:

```text
Grid Draft
Grid Revision
Questionnaire Draft
Questionnaire Revision
Builder Snapshot
```

---

# 137. Removal from current session

Does not hard-delete MediaAsset.

---

# 138. Historical dependency

May preserve relationship/provenance or block destructive relation deletion.

---

# 139. Archive Session

Effects:

```text
remove from current public Emotional pages
remove from Builder eligibility
remove from new Questionnaire selection
remove from Public Search
invalidate Homepage emotional projection
```

---

# 140. Existing Grid Revisions

Retained.

---

# 141. Existing Questionnaire Revisions

Retained.

---

# 142. MediaAssets

Retained independently.

---

# 143. EMO-INV-012 — Archive Session Does Not Delete Photos

---

# 144. Restore Session

Preferred:

```text
ARCHIVED → DRAFT
```

---

# 145. EMO-INV-013 — Restore Does Not Auto-Publish

---

# 146. Delete

Hard deletion exceptional.

Dependency analysis required.

---

# 147. Concurrency

Session uses optimistic version.

---

# 148. Mutable fields protected

At least:

```text
shooting_date
title/description
visibility
publication state
primary state
```

---

# 149. Reorder concurrency

Collection version or equivalent protects photo ordering.

---

# 150. Primary switch

Transaction-safe if module supports primary Session.

---

# 151. Publish transaction

Canonical:

```text
BEGIN
  load Session
  expectedVersion
  validate shooting_date
  validate media
  validate visibility/readiness
  update publication state
  Audit
  Outbox
COMMIT
```

---

# 152. No renderer inside transaction

Grid rendering/public derivative work after commit.

---

# 153. Events

Suggested:

```text
EmotionalSessionCreated
EmotionalSessionUpdated
EmotionalSessionPublished
EmotionalSessionArchived
EmotionalSessionPrimaryChanged
EmotionalSessionVisibilityChanged
EmotionalSessionMediaChanged
EmotionalSessionOrderChanged
```

---

# 154. Consumers

```text
Public projections
Homepage
Search
Questionnaire readiness
Builder eligibility
Grid readiness
Cache
VOP
Analytics
```

---

# 155. Event payload

Minimal:

```text
profile_id
session_id
version
changed_aspect
```

No image bytes/private payload.

---

# 156. Cache

Public Emotional pages may be cached.

---

# 157. Cache dimensions

```text
profile
locale
session/public generation
```

---

# 158. Visibility revocation

Must block public delivery strongly even before all cache/search cleanup finishes.

---

# 159. EMO-INV-014 — Cache Never Overrides Visibility

---

# 160. Search

Search index derived.

Visibility change triggers reindex/removal.

---

# 161. Stale search result

Final current projection rechecks visibility.

---

# 162. SEO

Public Session may expose:

- title;
- shooting date;
- safe description;
- public Grid preview/photo;
- canonical route.

---

# 163. SEO privacy

Hidden Session/photo must not enter:

```text
JSON-LD
OpenGraph
image metadata
sitemap
preload
```

---

# 164. Analytics

Relevant events:

```text
emotional_portfolio_opened
emotional_session_viewed
emotional_grid_opened
emotional_full_gallery_opened
```

---

# 165. Funnel relevance

Emotional Portfolio is proof/evidence layer.

It is not primary conversion endpoint.

---

# 166. CTA

End of emotional page should provide useful next action:

```text
Видео
Анкета
Связаться
```

according to availability.

---

# 167. No Dead Ends

Normative.

---

# 168. AI role

AI/VOP may assist with:

```text
duplicate detection
face location estimation
crop suggestion for Grid
possible session-media classification
missing metadata detection
```

---

# 169. AI cannot

```text
invent shooting date
auto-publish session
change actress appearance
assign emotional truth automatically
delete photos
finalize Grid
```

---

# 170. EMO-AI-001 — Shooting Date Never AI-Invented

---

# 171. EMO-AI-002 — Emotion Label Is Suggestion Only

---

# 172. EMO-AI-003 — Grid Crop Is Suggestion Only

Human confirms per cell.

---

# 173. EMO-AI-004 — AI Cannot Finalize Emotional Grid

---

# 174. VOP

May detect:

```text
missing shooting date
session without enough useful media
no finalized Grid
Grid stale after media change
broken full portfolio link
failed derivatives
duplicate photo candidates
```

---

# 175. VOP safe automation

Allowed:

```text
rebuild thumbnail
retry derivatives
invalidate cache
reindex search
recompute readiness
```

---

# 176. VOP recommendation only

Requires confirmation:

```text
change Primary Session
archive session
change visibility
select Grid
change photo order
```

---

# 177. Emotional Portfolio without AI

Must remain fully manually operable.

---

# 178. EMO-INV-015 — AI Is Optional

---

# 179. Admin UX

Recommended structure:

```text
Emotional Portfolio
├── Sessions
├── New Session
├── Current/Primary
├── Drafts
└── Archived
```

---

# 180. Session card

Show:

```text
cover/preview
shooting date
photo count
Grid status
publication state
Public / Questionnaire / Builder indicators
Primary marker
```

---

# 181. Session editor

Sections:

1. Basic information;
2. Shooting date;
3. Source photos;
4. Captions/order;
5. Visibility;
6. Emotional Grids;
7. Readiness;
8. Public Preview.

---

# 182. Upload flow

From Session editor:

```text
Upload
↓
MediaAsset
↓
processing
↓
Attach to EmotionalSession
```

---

# 183. Select existing media

Only eligible:

```text
same profile
IMAGE
safe/non-quarantined
```

---

# 184. Duplicate attachment

Same MediaAsset should not be accidentally attached twice to same Session.

---

# 185. EMO-MED-007 — Unique Session Membership Per MediaAsset

Recommended baseline.

---

# 186. Photo captions

Optional.

May support per-locale copy.

---

# 187. Reorder UX

Drag/drop plus keyboard-accessible alternative.

---

# 188. Source frame preview

Admin must be able to inspect full uncropped photo.

---

# 189. Grid status

Per Session show:

```text
No Grid
Draft Grid
Ready Grid
Primary Grid
Stale Grid
```

---

# 190. Readiness panel

Example:

```text
✓ Shooting date
✓ 16 photos
✓ Public link
⚠ No finalized 4×4 Grid
```

---

# 191. Public preview

Must use real Public Projection.

---

# 192. EMO-INV-016 — Preview Cannot Show Hidden Source Photos

---

# 193. Public UX

Recommended:

```text
Emotional Range
Shooting date
Primary Grid
Full photo series
Session navigation
CTA
```

---

# 194. Date prominence

Visible near title/primary Grid.

---

# 195. Grid vs source gallery

Grid provides instant overview.

Gallery provides detailed evidence.

---

# 196. Public performance

Do not preload all full-resolution originals.

Use responsive derivatives/lazy loading.

---

# 197. Source order

Public gallery honors Session order.

---

# 198. Mobile

Support:

- swipe;
- grid preview;
- readable date;
- full-frame images;
- sticky/back navigation where useful.

---

# 199. Accessibility

All controls keyboard accessible.

Grid composite requires meaningful accessible label.

---

# 200. Individual images

Need useful alt semantics without unsupported emotion inference.

---

# 201. Example safe alt

```text
Екатерина Крыгина — эмоциональная съёмка, 27 июля 2026
```

plus approved caption if present.

---

# 202. Avoid AI speculative alt

Do not auto-write:

```text
актриса испытывает глубокую ненависть
```

without approved context.

---

# 203. Image lightbox

Preserves:

```text
original framing
ordered navigation
keyboard controls
close/focus restoration
```

---

# 204. Theme constraints

Theme may change:

- spacing;
- typography;
- background;
- layout.

Theme cannot:

- crop actress unexpectedly;
- alter source photo;
- hide shooting date where required;
- obscure CTA/accessibility.

---

# 205. EMO-INV-017 — Theme Cannot Weaken Professional Evidence

---

# 206. Questionnaire integration detailed

Prepared Questionnaire configuration should select an Emotional representation, not arbitrary source photos.

---

# 207. Preferred default

Use:

```text
Primary Session
+
Primary GridRevision
```

when eligible.

---

# 208. Admin override

Admin MAY select another eligible finalized Grid according to questionnaire variant.

---

# 209. Questionnaire block contents

Exactly:

```text
Grid composite
Shooting date
Clickable full Emotional Portfolio link
Optional QR to same safe URL
```

---

# 210. No individual source photos

Reiterated as hard rule.

---

# 211. PDF layout

Grid should be rendered as a single composite artifact.

---

# 212. EMO-QNR-004 — PDF Must Not Rebuild Grid Independently From Cells

Use exact approved GridRevision/artifact semantics.

---

# 213. Why

Avoid differing crops between website and PDF.

---

# 214. If PDF-specific derivative

It derives from same exact GridRevision.

---

# 215. Link exactness

HTML and PDF use same canonical full-portfolio URL.

---

# 216. QR exactness

QR decodes to same URL.

---

# 217. Builder integration detailed

Builder template decides whether Emotional section is available/default.

---

# 218. Quick template

May omit Emotional section if product template intends maximum compactness.

---

# 219. Standard

Can include primary Emotional Grid.

---

# 220. Extended

Can allow choice among eligible Grids/Sessions.

---

# 221. Casting

Can include the approved Emotional representation relevant to role.

---

# 222. Builder session does not own Grid

Only references selection.

---

# 223. Builder Snapshot

Freezes exact:

```text
session identity
shooting date
GridRevision identity
link
```

---

# 224. Current session changes later

Generated Snapshot remains unchanged.

---

# 225. Casting AI

Can recommend Emotional Grid if casting requirement implies expressive/emotional material.

---

# 226. Example

Casting says:

```text
нужен широкий эмоциональный диапазон
```

AI may recommend:

```text
include primary Emotional Grid
```

with reason.

---

# 227. EMO-AI-005 — Casting Recommendation Does Not Alter Emotional Portfolio

---

# 228. BB Assistant

May draft Session description/caption from approved facts/context.

---

# 229. BB draft-only rule

No auto-publish.

---

# 230. Source facts for BB

Could include:

```text
shooting date
approved captions
session title
explicit Admin instruction
```

---

# 231. No invented shooting context

BB cannot invent:

```text
photographer
studio
purpose
role
```

unless present in Source.

---

# 232. Social Publishing

Published Emotional photo/Grid may be selected for Social Post if allowed by Social workflow.

---

# 233. Social selection does not modify session

---

# 234. Social-specific crop

Must be derived/contextual and not alter actress appearance.

---

# 235. Social post approval

Separate Human approval.

---

# 236. Security

Draft/private Sessions never public by guessed ID.

---

# 237. Original MediaAssets remain protected

Public uses appropriate derivatives.

---

# 238. Admin private session

Not discoverable in Public Search/SEO.

---

# 239. Token scope

If future casting-specific emotional access exists:

token grants only exact allowed Session/artifact scope.

---

# 240. Raw token

Never stored/logged unsafely.

---

# 241. Retention

Session is professional content.

Archive preferred.

---

# 242. Session photo relation retention

Historical Grid revisions may require exact sources.

---

# 243. Hard removal

Requires dependency analysis.

---

# 244. Privacy redaction

If one Emotional photo must be removed:

inspect:

```text
Session
Grid Drafts
Grid Revisions
Grid artifacts
Questionnaire Revisions
Builder Snapshots
Social outputs
Search
Cache
AI snapshots
```

---

# 245. Historical Grid issue

Removing a source photo cannot silently regenerate Grid with another photo.

---

# 246. EMO-INV-018 — Historical Grid Cannot Substitute Source

---

# 247. If legal/privacy removal required

Access/artifact redaction rules apply.

Semantic historical record remains traceable where permitted.

---

# 248. Migration

Existing emotional materials must be migrated into explicit Sessions.

---

# 249. Migration must identify shooting date

If known from trustworthy existing data, map it.

---

# 250. Unknown shooting date

```text
MIGRATED_INCOMPLETE / REQUIRES_REVIEW
```

---

# 251. EMO-DATE-005 — Migration Does Not Invent Shooting Date

---

# 252. Legacy “emotional portfolio” photos without session

Group only where provenance supports same shoot.

Otherwise require Admin review.

---

# 253. Legacy crop

Original Media remains intact.

Only explicit Grid crop configs are migrated as contextual transforms.

---

# 254. Legacy composite image

If an old prebuilt emotional collage exists:

it may be retained as legacy artifact/reference, but should not be treated as equivalent to editable Grid source without known cells/provenance.

---

# 255. Migration goal

Canonical future state:

```text
Session
→ Source photos
→ Grid Draft/Revision
```

---

# 256. Commands

Canonical:

```text
CreateEmotionalSession
UpdateEmotionalSession
UpdateEmotionalSessionLocalization
SetEmotionalShootingDate
AttachEmotionalMedia
RemoveEmotionalMedia
ReorderEmotionalSessionMedia
UpdateEmotionalMediaCaption
UpdateEmotionalSessionVisibility
PublishEmotionalSession
ArchiveEmotionalSession
RestoreEmotionalSession
SetPrimaryEmotionalSession
```

---

# 257. Grid commands

Belong DOC-114 but invoked from Session context:

```text
CreateEmotionalGrid
SetPrimaryEmotionalGrid
```

---

# 258. Queries

```text
GetAdminEmotionalSessions
GetAdminEmotionalSession
GetPublicEmotionalPortfolio
GetPublicEmotionalSession
GetEmotionalReadiness
ListQuestionnaireEligibleEmotionalRepresentations
ListBuilderEligibleEmotionalRepresentations
PreviewPublicEmotionalSession
```

---

# 259. Create input

Conceptually:

```text
CreateEmotionalSessionInput {
  shootingDate?
  title?
  description?
}
```

Draft can omit date.

---

# 260. Publish input

```text
PublishEmotionalSessionInput {
  sessionId
  expectedVersion
  idempotencyKey
}
```

---

# 261. Publish does not accept source truth copy

Server reloads:

```text
date
photos
visibility
Grid/readiness
```

---

# 262. Attach media input

```text
sessionId
mediaAssetId
expectedVersion/collectionVersion
```

---

# 263. Server checks

```text
same profile
IMAGE
safe
not already attached
```

---

# 264. Set Shooting Date

Explicit value:

```text
YYYY-MM-DD
```

---

# 265. Client cannot set

```text
shootingDateSource = AI
confirmedAt
publishedAt
publishedBy
```

as authority.

---

# 266. Error codes

At minimum:

```text
EMOTIONAL_SESSION_NOT_FOUND
EMOTIONAL_SHOOTING_DATE_REQUIRED
EMOTIONAL_SHOOTING_DATE_INVALID
EMOTIONAL_MEDIA_INVALID
EMOTIONAL_MEDIA_WRONG_PROFILE
EMOTIONAL_MEDIA_DUPLICATE
EMOTIONAL_MEDIA_NOT_READY
EMOTIONAL_SESSION_NOT_READY
EMOTIONAL_SESSION_ARCHIVED
EMOTIONAL_SESSION_PRIMARY_CONFLICT
EMOTIONAL_GRID_REQUIRED_FOR_QUESTIONNAIRE
EMOTIONAL_GRID_NOT_READY
EMOTIONAL_PUBLIC_LINK_UNAVAILABLE
EMOTIONAL_REORDER_CONFLICT
```

---

# 267. Readiness codes

```text
EMOTIONAL_PUBLIC_NO_PHOTOS
EMOTIONAL_PUBLIC_DATE_MISSING
EMOTIONAL_QUESTIONNAIRE_NO_GRID
EMOTIONAL_BUILDER_NO_GRID
EMOTIONAL_GRID_STALE
EMOTIONAL_GRID_ARTIFACT_NOT_READY
```

---

# 268. Audit

High-impact actions SHOULD include:

```text
shooting date changes
publication
archive/restore
Primary Session change
visibility change
source photo removal when dependencies exist
```

---

# 269. Captions/order

May have lighter audit requirement unless needed for professional history.

---

# 270. Grid finalization

Audited separately by Grid module.

---

# 271. Public date correction

Changing shooting date after published use is high impact.

---

# 272. EMO-DATE-006 — Historical Questionnaire Date Remains Frozen

---

# 273. Current public session

Reflects corrected current Source after proper update.

---

# 274. Historical artifacts

Unchanged.

---

# 275. Anti-patterns

`EMO-AP-001`  
Store Emotional Portfolio as Project type `OTHER`.

`EMO-AP-002`  
Store Emotional images inside Main Portfolio only.

`EMO-AP-003`  
Treat Emotional Grid as the only source and discard originals.

`EMO-AP-004`  
Store shooting date independently on every photo.

`EMO-AP-005`  
Automatically use EXIF date as professional shooting date.

`EMO-AP-006`  
Invent current date when shooting date missing.

`EMO-AP-007`  
Publish Session without date.

`EMO-AP-008`  
Mix photos from unrelated shooting sessions into one Grid.

`EMO-AP-009`  
Destructively crop source photos to fit Grid.

`EMO-AP-010`  
Grid crop changes full Emotional Portfolio framing.

`EMO-AP-011`  
AI labels emotion and publishes it as fact automatically.

`EMO-AP-012`  
AI judges attractiveness/expression quality.

`EMO-AP-013`  
AI automatically chooses Primary Session.

`EMO-AP-014`  
AI finalizes Grid without Human confirmation.

`EMO-AP-015`  
Use individual Emotional photos in Questionnaire block.

`EMO-AP-016`  
Questionnaire includes every emotional source image.

`EMO-AP-017`  
Questionnaire rebuilds its own crop from Grid cells.

`EMO-AP-018`  
Questionnaire link points to `/admin/...`.

`EMO-AP-019`  
Questionnaire QR points to private storage path.

`EMO-AP-020`  
Builder can create/edit Grid.

`EMO-AP-021`  
Builder can edit shooting date.

`EMO-AP-022`  
Builder accesses raw Emotional Media inventory.

`EMO-AP-023`  
One `is_public` controls all three contexts.

`EMO-AP-024`  
Hidden Session sent to browser then CSS-hidden.

`EMO-AP-025`  
Archived Session remains in Public Search.

`EMO-AP-026`  
Restore auto-publishes.

`EMO-AP-027`  
Archive Session deletes MediaAssets.

`EMO-AP-028`  
Remove current photo rewrites historical GridRevision.

`EMO-AP-029`  
Missing historical source replaced with visually similar photo.

`EMO-AP-030`  
Upload timestamp used as shooting date.

`EMO-AP-031`  
Latest uploaded Session automatically becomes Primary.

`EMO-AP-032`  
Theme hides shooting date.

`EMO-AP-033`  
Theme crops emotional source photos to decorative tiles everywhere.

`EMO-AP-034`  
Public page depends on runtime AI classification.

`EMO-AP-035`  
Search indexes private AI emotion labels.

`EMO-AP-036`  
SEO exposes hidden Session/photo.

`EMO-AP-037`  
BB invents studio/photographer context.

`EMO-AP-038`  
Social publishing mutates source Session crop/order.

`EMO-AP-039`  
VOP automatically archives an older Session.

`EMO-AP-040`  
VOP fills missing shooting date from guessed metadata.

`EMO-AP-041`  
One failed image crashes entire emotional page.

`EMO-AP-042`  
Hard delete Session with Grid/Questionnaire history.

`EMO-AP-043`  
Session title used as only source of date.

`EMO-AP-044`  
Primary Grid and Primary Session represented by same flag.

`EMO-AP-045`  
Public Builder permission implies ordinary Public visibility.

`EMO-AP-046`  
Questionnaire permission automatically implies safe public full-portfolio link.

`EMO-AP-047`  
Media captions stored globally on MediaAsset.

`EMO-AP-048`  
Reorder trusts arbitrary media IDs.

`EMO-AP-049`  
Cross-profile image attached by UUID.

`EMO-AP-050`  
Migration groups photos into a session solely because files were uploaded together.

---

# 276. Core invariants

`EMO-INV-019`  
Emotional Portfolio is standalone from Projects.

`EMO-INV-020`  
Emotional Portfolio is standalone from Main Portfolio.

`EMO-INV-021`  
Emotional Grid is derived/configured from Emotional Session, not vice versa.

`EMO-INV-022`  
Every Session belongs to one Actor Profile.

`EMO-INV-023`  
Every Session photo references same-profile IMAGE MediaAsset.

`EMO-INV-024`  
Original source photo remains immutable.

`EMO-INV-025`  
Session owns shooting date.

`EMO-INV-026`  
Published Session requires shooting date.

`EMO-INV-027`  
EXIF date is non-authoritative.

`EMO-INV-028`  
Missing shooting date is never invented.

`EMO-INV-029`  
Session photo order is explicit.

`EMO-INV-030`  
Session contextual caption does not alter MediaAsset.

`EMO-INV-031`  
One MediaAsset is not accidentally attached twice to same Session.

`EMO-INV-032`  
Public gallery preserves source framing.

`EMO-INV-033`  
Grid crop never mutates source photo.

`EMO-INV-034`  
Grid crop never affects source-gallery projection.

`EMO-INV-035`  
AI cannot modify actress appearance.

`EMO-INV-036`  
AI emotion classification is recommendation only.

`EMO-INV-037`  
AI cannot invent shooting date.

`EMO-INV-038`  
AI cannot set Primary Session.

`EMO-INV-039`  
AI cannot finalize Grid.

`EMO-INV-040`  
VOP cannot change professional facts without confirmation.

`EMO-INV-041`  
Public, Admin Questionnaire and Builder permissions remain independent.

`EMO-INV-042`  
Publication state and visibility remain separate.

`EMO-INV-043`  
Hidden Session is absent from Public DTO.

`EMO-INV-044`  
Public Search only contains Public-safe Sessions.

`EMO-INV-045`  
SEO cannot exceed Public visibility.

`EMO-INV-046`  
Homepage emotional preview is Projection.

`EMO-INV-047`  
Primary Session and Primary Grid are separate concepts.

`EMO-INV-048`  
At most one Primary Session exists if primary semantics enabled.

`EMO-INV-049`  
Questionnaire Emotional block never contains individual source photos.

`EMO-INV-050`  
Questionnaire uses finalized GridRevision.

`EMO-INV-051`  
Questionnaire includes exact shooting date.

`EMO-INV-052`  
Questionnaire includes clickable full Emotional Portfolio link.

`EMO-INV-053`  
QR, if enabled, targets exact safe full-portfolio URL.

`EMO-INV-054`  
Historical Questionnaire freezes exact Grid/date/link semantics.

`EMO-INV-055`  
Current Session changes do not rewrite historical Questionnaire.

`EMO-INV-056`  
Public Builder selects approved representation only.

`EMO-INV-057`  
Builder cannot edit Session Source.

`EMO-INV-058`  
Builder Generate revalidates eligibility.

`EMO-INV-059`  
Grid sources belong same Session.

`EMO-INV-060`  
Replacing/removing a Session image does not rewrite GridRevision.

`EMO-INV-061`  
Current Grid Draft can become stale when sources change.

`EMO-INV-062`  
Archive removes current visibility but preserves history.

`EMO-INV-063`  
Archive does not delete MediaAssets.

`EMO-INV-064`  
Restore does not auto-publish.

`EMO-INV-065`  
Hard delete requires dependency analysis.

`EMO-INV-066`  
Privacy removal discovers Grid/Questionnaire/Builder/AI copies.

`EMO-INV-067`  
Historical source cannot be silently substituted.

`EMO-INV-068`  
Public page remains usable without AI.

`EMO-INV-069`  
Runtime AI is not required for display.

`EMO-INV-070`  
Theme cannot hide required shooting date.

`EMO-INV-071`  
Theme cannot change professional image framing rules.

`EMO-INV-072`  
Social publication is separate workflow.

`EMO-INV-073`  
Social crop never changes Session Source.

`EMO-INV-074`  
BB text remains Draft until Human Apply.

`EMO-INV-075`  
Search and cache are derived only.

`EMO-INV-076`  
Cache cannot override current visibility.

`EMO-INV-077`  
Stale search result cannot leak revoked Session.

`EMO-INV-078`  
Shooting date corrections are versioned/audited.

`EMO-INV-079`  
Historical date remains frozen after correction.

`EMO-INV-080`  
Migration does not fabricate shooting date.

`EMO-INV-081`  
Migration does not merge shoots without provenance.

`EMO-INV-082`  
Legacy composite is not automatically editable Grid Source.

`EMO-INV-083`  
Session and Grid readiness remain separate.

`EMO-INV-084`  
Public readiness and Questionnaire readiness remain separate.

`EMO-INV-085`  
Questionnaire permission alone does not make a private URL safe.

`EMO-INV-086`  
Full Emotional Portfolio route must be safe/resolvable before inclusion in normal public Questionnaire.

`EMO-INV-087`  
One failed optional photo does not crash whole Session.

`EMO-INV-088`  
Required failed Grid creates Questionnaire readiness blocker.

`EMO-INV-089`  
All professional mutations use server-side validation.

`EMO-INV-090`  
Concurrent edits cannot silently overwrite current Session state.

---

# 277. E2E-EMO-001 — Create Draft

Create Emotional Session without date.

Expected:

```text
Draft saved
Public publish blocked
```

---

# 278. E2E-EMO-002 — Publish missing date

Expected:

```text
EMOTIONAL_SHOOTING_DATE_REQUIRED
```

---

# 279. E2E-EMO-003 — Exact date

Set:

```text
2026-07-27
```

Different browser timezones still display correct calendar date.

---

# 280. E2E-EMO-004 — EXIF conflict

EXIF says July 26, Human sets July 27.

Professional Source remains July 27.

---

# 281. E2E-EMO-005 — Image attachment

Valid same-profile IMAGE attaches successfully.

---

# 282. E2E-EMO-006 — Video attachment

Rejected.

---

# 283. E2E-EMO-007 — Cross-profile photo

Rejected.

---

# 284. E2E-EMO-008 — Duplicate attachment

Same MediaAsset twice to same Session rejected/warned.

---

# 285. E2E-EMO-009 — Reorder

Valid source photo order persists.

---

# 286. E2E-EMO-010 — Reorder duplicate IDs

Rejected.

---

# 287. E2E-EMO-011 — Reorder foreign media

Rejected.

---

# 288. E2E-EMO-012 — Public framing

Session photos display uncropped source framing.

---

# 289. E2E-EMO-013 — Grid crop isolation

Change cell crop.

Full Emotional gallery remains visually unchanged.

---

# 290. E2E-EMO-014 — AI emotion label

AI suggests “удивление”.

No public label until Human confirms/applies.

---

# 291. E2E-EMO-015 — AI date attempt

AI suggests date from metadata.

No Source change until Human explicitly sets date.

---

# 292. E2E-EMO-016 — AI unavailable

Create, edit and publish valid Session manually.

---

# 293. E2E-EMO-017 — Public hidden

Session published but `showOnPublicSite=false`.

Absent from Public page/search/SEO.

---

# 294. E2E-EMO-018 — Builder-only

```text
public=false
builder=true
```

Eligible only through Builder if safe representation/link rules pass.

---

# 295. E2E-EMO-019 — Questionnaire-only private link

Session:

```text
public=false
adminQuestionnaire=true
```

with no safe token/public full-portfolio route.

Questionnaire Emotional inclusion blocked rather than emitting Admin/private link.

---

# 296. E2E-EMO-020 — Primary Session

Set one Session Primary.

Only one current Primary remains.

---

# 297. E2E-EMO-021 — Concurrent Primary

Two requests race.

One authoritative Primary remains.

---

# 298. E2E-EMO-022 — Session vs Grid Primary

Changing Primary Grid does not change Primary Session.

---

# 299. E2E-EMO-023 — Multiple Grids

Same Session can retain multiple finalized Grids.

---

# 300. E2E-EMO-024 — Grid wrong Session source

Attempt Grid cell image from another Session.

Rejected.

---

# 301. E2E-EMO-025 — Questionnaire block

Generated block contains only:

```text
Grid composite
shooting date
full portfolio link
```

---

# 302. E2E-EMO-026 — Questionnaire source photos

No individual Session photo list appears.

---

# 303. E2E-EMO-027 — PDF Grid consistency

HTML and PDF use same approved GridRevision.

---

# 304. E2E-EMO-028 — Questionnaire QR

QR decodes exactly to same canonical emotional portfolio URL as hyperlink.

---

# 305. E2E-EMO-029 — Invalid QR target

Admin/private emotional route rejected.

---

# 306. E2E-EMO-030 — Historical date

Publish Questionnaire with Session date July 27.

Correct current Session date later.

Historical Revision preserves original snapshot value.

---

# 307. E2E-EMO-031 — Historical Grid

New Grid becomes Primary later.

Old Questionnaire still uses previously frozen GridRevision.

---

# 308. E2E-EMO-032 — Builder selection

Visitor selects eligible Emotional Grid.

Builder stores selection, not editable source data.

---

# 309. E2E-EMO-033 — Builder revocation

Grid/Session permission revoked before Generate.

Generation blocks/removes selection.

---

# 310. E2E-EMO-034 — Builder date change

Admin corrects current Session date before Builder Generate.

Generation uses current authoritative date after revalidation.

---

# 311. E2E-EMO-035 — Existing Builder Snapshot

After generation, later Session change does not rewrite Snapshot.

---

# 312. E2E-EMO-036 — Remove source used by Grid Draft

Draft becomes invalid/stale and requires review.

---

# 313. E2E-EMO-037 — Remove source used by finalized Grid

Historical GridRevision remains protected; destructive removal blocked/redaction workflow required.

---

# 314. E2E-EMO-038 — Replace Media

Session current relation switches to new asset.

Historical Grid continues referencing old source.

---

# 315. E2E-EMO-039 — Archive Session

Public/Home/Search/Builder current projections stop exposing it.

---

# 316. E2E-EMO-040 — Archive retains history

Historical Questionnaire/Grid remain stored.

---

# 317. E2E-EMO-041 — Archive retains original

MediaAsset not deleted.

---

# 318. E2E-EMO-042 — Restore

Restored Session returns Draft/non-public state.

---

# 319. E2E-EMO-043 — Concurrent Session edit

Admin A saves v6.

Admin B tries v5.

Expected stale conflict.

---

# 320. E2E-EMO-044 — Shooting date audit

Date correction generates expected Audit event.

---

# 321. E2E-EMO-045 — Visibility cache revocation

Public Session cached.

Visibility switched off.

Next public request does not expose stale data.

---

# 322. E2E-EMO-046 — Search stale row

Search index still contains old Session briefly.

Server hydration drops it from Public result.

---

# 323. E2E-EMO-047 — SEO removal

Archived/hidden Session removed from sitemap/metadata projections.

---

# 324. E2E-EMO-048 — Homepage

Primary public Session provides emotional preview + shooting date + CTA.

---

# 325. E2E-EMO-049 — No Grid Homepage fallback

Session public but Grid unavailable.

Homepage follows configured safe fallback or hides Grid section; does not invent composite.

---

# 326. E2E-EMO-050 — Broken primary Grid

Primary Grid artifact fails.

Questionnaire readiness blocks; full source Session may remain public.

---

# 327. E2E-EMO-051 — Social selection

Approved Emotional image/Grid can be selected for Social Draft without changing Session state.

---

# 328. E2E-EMO-052 — Social crop

Platform crop affects social derivative only.

---

# 329. E2E-EMO-053 — BB description

BB drafts Session description.

No publication until Human Apply/save.

---

# 330. E2E-EMO-054 — BB invented photographer

Unsupported photographer detail does not become Source automatically.

---

# 331. E2E-EMO-055 — VOP missing date

VOP reports blocker but does not fill date.

---

# 332. E2E-EMO-056 — VOP failed derivative

Allowed deterministic retry succeeds without changing professional Source.

---

# 333. E2E-EMO-057 — Privacy deletion

Source photo privacy removal triggers discovery of Grid/Questionnaire/Builder/Social/AI copies.

---

# 334. E2E-EMO-058 — Historical substitution prohibited

After source removal, historical Grid cannot silently replace photo with another asset.

---

# 335. E2E-EMO-059 — Migration missing date

Legacy Session imported without reliable date remains incomplete/non-public.

---

# 336. E2E-EMO-060 — Migration grouping

Photos from unrelated known shooting dates are not merged into one Session.

---

# 337. E2E-EMO-061 — Legacy composite

Old collage imported as legacy artifact does not masquerade as editable new Grid with unknown cells.

---

# 338. E2E-EMO-062 — Public responsive loading

Public Session uses web derivatives, not raw originals for every thumbnail.

---

# 339. E2E-EMO-063 — Accessibility

Keyboard user can open/close/navigate source gallery and reach CTA.

---

# 340. E2E-EMO-064 — Theme

Theme changes visual layout but cannot remove required shooting date or destructively crop source gallery.

---

# 341. E2E-EMO-065 — Public safety

Guessing draft Session UUID cannot expose draft photos.

---

# 342. Architecture diagram

```text
                    MEDIA LIBRARY
                         │
                         ▼
                    MediaAsset
                         │
                         ▼
                EmotionalSessionMedia
                         │
                         ▼
                  EmotionalSession
               ┌─────────┼──────────┐
               ▼         ▼          ▼
            Public     Grid       Readiness
            Gallery    Draft
                         │
                         ▼
                   GridRevision
                         │
            ┌────────────┴────────────┐
            ▼                         ▼
       Questionnaire                Builder
```

---

# 343. Source vs Grid diagram

```text
SOURCE PHOTO
    │
    ├── Public Emotional Gallery
    │       → full source framing
    │
    └── Emotional Grid Cell
            → contextual crop/pan/scale
            → Human confirmation

Original binary never changes.
```

---

# 344. Session lifecycle diagram

```text
DRAFT
  │
  │ readiness:
  │ - shooting date
  │ - valid media
  ▼
PUBLISHED
  │
  ▼
ARCHIVED
  │
  └── restore
        ↓
      DRAFT
```

---

# 345. Questionnaire diagram

```text
EmotionalSession
     +
Finalized GridRevision
     ↓
Questionnaire Emotional Projection
     ├── Composite
     ├── Shooting Date
     └── Full Portfolio URL
              ↓
          Hyperlink + optional QR
```

---

# 346. Builder diagram

```text
Current Session/Grid
      ↓
Builder Eligibility Projection
      ↓
Visitor selects approved representation
      ↓
Server revalidates
      ↓
BuilderGenerationSnapshot
```

---

# 347. Historical diagram

```text
Session S1
shooting_date = 2026-07-27
Grid Revision G3
       ↓
Questionnaire Revision Q5
       ↓
Later:
Session date corrected / new Grid G4

Q5 remains:
date 2026-07-27 + G3
```

---

# 348. Visibility diagram

```text
Emotional Session / approved representation
        │
        ├── show_on_public_site
        ├── allow_in_admin_questionnaires
        └── allow_in_public_questionnaire_builder

Independent decisions.
```

---

# 349. AI diagram

```text
Session Photos
     ↓
AI/CV
     ↓
Suggestion
 ┌───────────────┬────────────────┐
 ▼               ▼                ▼
duplicate      crop idea       emotion label
 candidate
     │               │                │
     └──────────── Human Review ───────┘
                       ↓
               Explicit Domain Command
```

---

# 350. Quality gate

Перед implementation должны быть подтверждены:

- [ ] standalone Emotional Portfolio domain;
- [ ] EmotionalSession entity;
- [ ] session/media relation;
- [ ] mandatory shooting date for publish;
- [ ] date-only semantics;
- [ ] EXIF non-authoritative rule;
- [ ] immutable MediaAsset originals;
- [ ] explicit session photo order;
- [ ] captions/localization;
- [ ] publication lifecycle;
- [ ] visibility triplet;
- [ ] optional Primary Session;
- [ ] Primary Session vs Primary Grid separation;
- [ ] full public Emotional page;
- [ ] shooting date visibility;
- [ ] homepage emotional projection;
- [ ] Grid relationship;
- [ ] same-session Grid source rule;
- [ ] multiple Grid support;
- [ ] Questionnaire composite-only rule;
- [ ] full portfolio hyperlink;
- [ ] QR safety;
- [ ] Builder approved-representation rule;
- [ ] historical snapshot behavior;
- [ ] Media replacement semantics;
- [ ] archive/restore;
- [ ] privacy copy discovery;
- [ ] Search/SEO;
- [ ] Cache invalidation;
- [ ] AI/Human boundaries;
- [ ] VOP boundaries;
- [ ] Accessibility;
- [ ] migration policy;
- [ ] E2E coverage.

---

# 351. Acceptance criteria

`AC-EMO-001`  
Emotional Portfolio существует как самостоятельный модуль.

`AC-EMO-002`  
Он не моделируется как Project `OTHER`.

`AC-EMO-003`  
Каждая опубликованная Emotional Session имеет shooting date.

`AC-EMO-004`  
Shooting date хранится как calendar date.

`AC-EMO-005`  
EXIF не является authoritative shooting date.

`AC-EMO-006`  
Отсутствующая дата не выдумывается.

`AC-EMO-007`  
Каждая Session принадлежит Actor Profile.

`AC-EMO-008`  
Каждая source photo является same-profile IMAGE MediaAsset.

`AC-EMO-009`  
Original photo immutable.

`AC-EMO-010`  
Session photo order управляется отдельно.

`AC-EMO-011`  
Grid crop не меняет source gallery framing.

`AC-EMO-012`  
Public gallery сохраняет исходный кадр.

`AC-EMO-013`  
AI emotion recognition не становится фактом без Human confirmation.

`AC-EMO-014`  
AI не может изменить внешность актрисы.

`AC-EMO-015`  
AI не может автоматически установить shooting date.

`AC-EMO-016`  
Public/Questionnaire/Builder permissions независимы.

`AC-EMO-017`  
Hidden Session отсутствует из Public serialization.

`AC-EMO-018`  
Session может иметь несколько Emotional Grids.

`AC-EMO-019`  
Grid использует фото только своей Session.

`AC-EMO-020`  
Primary Session и Primary Grid — разные state dimensions.

`AC-EMO-021`  
Questionnaire Emotional block содержит Grid composite.

`AC-EMO-022`  
Questionnaire Emotional block содержит shooting date.

`AC-EMO-023`  
Questionnaire Emotional block содержит clickable full Emotional Portfolio URL.

`AC-EMO-024`  
Questionnaire Emotional block не содержит individual source photos.

`AC-EMO-025`  
QR, если включён, кодирует exact safe full-portfolio URL.

`AC-EMO-026`  
PDF использует exact approved GridRevision.

`AC-EMO-027`  
Public Builder выбирает только approved representation.

`AC-EMO-028`  
Builder не может создавать/crop/edit Grid.

`AC-EMO-029`  
Builder не может менять shooting date.

`AC-EMO-030`  
Generate revalidates Session/Grid eligibility.

`AC-EMO-031`  
Historical Questionnaire сохраняет exact Grid/date/link snapshot.

`AC-EMO-032`  
Current Session changes не переписывают history.

`AC-EMO-033`  
Source photo replacement не переписывает finalized GridRevision.

`AC-EMO-034`  
Archive removes current public exposure.

`AC-EMO-035`  
Archive does not delete source MediaAssets.

`AC-EMO-036`  
Restore does not auto-publish.

`AC-EMO-037`  
Hard deletion is dependency-aware.

`AC-EMO-038`  
Search/SEO respect Public visibility.

`AC-EMO-039`  
Cache cannot override visibility revocation.

`AC-EMO-040`  
Homepage emotional preview derives from current approved Session/Grid.

`AC-EMO-041`  
Theme cannot hide mandatory date or alter professional framing.

`AC-EMO-042`  
BB/VOP/AI never auto-publish professional data.

`AC-EMO-043`  
Module remains fully functional without AI.

`AC-EMO-044`  
Migration never invents shooting date.

`AC-EMO-045`  
Migration never merges unrelated shoots without evidence.

`AC-EMO-046`  
Privacy redaction discovers historical/derived copies.

`AC-EMO-047`  
Historical source cannot be silently substituted.

`AC-EMO-048`  
Module is covered by deterministic lifecycle/visibility/Grid/Questionnaire E2E tests.

---

# 352. Финальная доктрина

> **Emotional Portfolio является отдельным профессиональным доказательным слоем актёрского профиля. Его основная Source entity — Emotional Session с подтверждённой датой съёмки и упорядоченным набором immutable фотографий. Полная эмоциональная серия показывается на самостоятельной public page с сохранением исходного кадра, тогда как Questionnaire и Public Builder используют только утверждённый Emotional Grid composite, дату съёмки и безопасную ссылку на полное Emotional Portfolio. Grid является производным Human-confirmed представлением одной конкретной съёмки и никогда не изменяет оригиналы. AI может предлагать crop, duplicate candidate или label, но не имеет права придумывать дату, утверждать эмоциональное состояние, менять внешность, публиковать Session или финализировать Grid.**