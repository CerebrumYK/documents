# MAIN PORTFOLIO MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация профессионального фотопортфолио, ракурсов, Primary Close-Up / Full Body, publication и contextual eligibility

**Целевой файл:** `docs/modules/portfolio.md`  
**Документ:** DOC-112  
**Статус:** ✅ Completed  
**Тип:** Module / Portfolio / Professional Photography / Casting / Visibility

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

---

# 1. Назначение модуля

Main Portfolio — основной профессиональный фотопортфолио-модуль актрисы.

Он предназначен для того, чтобы Casting Director, Casting Assistant, Producer или другой профессиональный пользователь мог быстро получить:

1. актуальный крупный план;
2. фотографию в полный рост;
3. дополнительные профессиональные ракурсы;
4. профиль;
5. поясные/средние планы;
6. 3/4;
7. постановочные профессиональные кадры;
8. иные подтверждённые portfolio categories;
9. предсказуемую структуру для Questionnaire;
10. фильтруемую фотоподборку для casting workflow.

---

# 2. Главная доктрина

> **Main Portfolio определяет профессиональный смысл использования фотографии, но не владеет её binary original.**

Canonical:

```text
MediaAsset
   ↓
PortfolioItem
   ↓
Professional Classification
   ↓
Visibility / Eligibility / Ordering
   ↓
Public / Questionnaire / Builder / Hero
```

---

# 3. Fundamental separation

```text
MediaAsset
≠
PortfolioItem
≠
Emotional Photo
≠
Role Photo
≠
Questionnaire Snapshot
```

Один `MediaAsset` может участвовать в Portfolio, но Portfolio определяет:

- category;
- primary status;
- professional caption;
- public visibility;
- Questionnaire eligibility;
- Builder eligibility;
- display order.

---

# 4. Module identifiers

Используются:

```text
PORT-*
PORT-CAT-*
PORT-PRI-*
PORT-VIS-*
PORT-ORD-*
PORT-QNR-*
PORT-BLD-*
PORT-HERO-*
PORT-AI-*
PORT-INV-*
PORT-AP-*
E2E-PORT-*
```

---

# 5. Business purpose

Main Portfolio должен позволять профессиональному посетителю за минимальное число действий:

```text
открыть портфолио
→ увидеть Close-Up
→ увидеть Full Body
→ при необходимости отфильтровать остальные типы
→ принять решение о дальнейшем просмотре/анкете/контакте
```

---

# 6. Customer Journey role

Для Casting Assistant canonical journey:

```text
Quick Facts
   ↓
Portfolio
   ↓
Close-Up / Full Body / Profile / Staged filters
   ↓
Skills
   ↓
Projects
   ↓
Questionnaire/PDF
```

---

# 7. Main Portfolio ≠ Emotional Portfolio

Main Portfolio:

- профессиональные фотографии;
- классификация по framing/назначению;
- обязательный источник Close-Up;
- обязательный источник Full Body.

Emotional Portfolio:

- отдельная эмоциональная съёмка;
- shooting date;
- эмоциональный диапазон;
- отдельные grids.

---

# 8. PORT-INV-001 — Emotional Photo Cannot Substitute Main Portfolio Mandatory Photo

Ни отдельная Emotional photo, ни Emotional Grid не может заменить:

```text
Primary Close-Up
Primary Full Body
```

для mandatory Questionnaire baseline.

---

# 9. Main Portfolio ≠ Project/Role Media

Role photo демонстрирует образ/роль в проекте.

Main Portfolio демонстрирует актрису как профессиональный casting profile asset.

---

# 10. PORT-INV-002 — Role Photo Cannot Substitute Mandatory Full Body / Close-Up

Даже если визуально подходит.

Если изображение требуется в Main Portfolio, оно должно иметь самостоятельный `PortfolioItem`.

---

# 11. Source ownership

Portfolio Module владеет:

```text
portfolio category
caption
ordering
primary status
publication lifecycle
visibility/eligibility
portfolio-specific configuration
```

---

# 12. Media Library владеет

```text
original binary
checksum
MIME
dimensions
technical processing
derivatives
storage
```

---

# 13. Physical model

Canonical:

```text
PortfolioItem
→ MediaAsset
```

Representative table:

```text
portfolio_items
```

---

# 14. Representative fields

Conceptually:

```text
id
profile_id
media_asset_id
category
caption
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

Exact dictionary remains DOC-091.

---

# 15. Category taxonomy doctrine

Portfolio categories должны быть:

- finite;
- typed;
- extensible through controlled migration/configuration;
- understandable casting professionals;
- not arbitrary free-form tags as sole classification.

---

# 16. Canonical baseline categories

At minimum:

```text
CLOSE_UP
FULL_BODY
WAIST
PROFILE
THREE_QUARTER
STAGED
OTHER
```

---

# 17. Extended professional categories

Module MAY support additional controlled categories such as:

```text
HEADSHOT
MEDIUM_SHOT
SIDE_PROFILE
BACK_VIEW
ACTION
CHARACTER
EDITORIAL
COMMERCIAL
```

only when terminology is documented and UX needs justify it.

---

# 18. Category vocabulary

Displayed labels localized.

Example RU:

```text
Крупный план
В полный рост
Поясной
Профиль
3/4
Постановочный
Другое
```

---

# 19. Category stored language-neutral

Database:

```text
close_up
full_body
waist
profile
three_quarter
staged
other
```

or equivalent canonical enum/check value.

---

# 20. PORT-CAT-INV-001 — Category Is Professional Metadata

Category is not derived solely from image dimensions.

---

# 21. Landscape image ≠ Staged automatically

Orientation does not establish professional category.

---

# 22. Face detector ≠ Close-Up authority

AI/CV may suggest.

Human confirms.

---

# 23. Full Body semantics

`FULL_BODY` means professional image intended to show actor full-length sufficiently for casting evaluation.

---

# 24. PORT-CAT-001 — Full Body Requires Full Figure Visibility

Image must not be categorized mandatory Full Body if critical parts of figure are outside source frame such that professional requirement is not met.

---

# 25. Full Body rendering

Must preserve original framing.

---

# 26. PORT-INV-003 — No Cover Crop for Full Body

Public, Admin selection and PDF presentation must not use arbitrary `cover` crop that cuts off actor.

---

# 27. Close-Up semantics

`CLOSE_UP` is professional head/face close-up intended for casting identification and evaluation.

---

# 28. Close-Up framing

May naturally contain head/shoulders according to source.

Portfolio does not reshape or regenerate face.

---

# 29. Profile category

Profile view is distinct from generic close-up.

May be side-profile or clearly identified profile-facing composition.

---

# 30. Three-quarter

Represents a professional 3/4 framing/angle category according to adopted casting vocabulary.

---

# 31. Waist

Professional waist/medium framing.

---

# 32. Staged

Professional staged/posed/production image suitable as portfolio material but not a substitute for required neutral Close-Up/Full Body.

---

# 33. OTHER

Fallback only for legitimate professional portfolio image not fitting current taxonomy.

---

# 34. PORT-CAT-INV-002 — OTHER Must Not Become Default Dumping Ground

If a recurring professional category accumulates, taxonomy should be extended deliberately.

---

# 35. Multiple classifications

Baseline recommendation:

> one primary professional category per `PortfolioItem`.

---

# 36. Why

Keeps:

- filtering deterministic;
- mandatory photo validation predictable;
- questionnaire logic simple.

---

# 37. Future secondary tags

May be added separately:

```text
tags
attributes
```

without replacing primary category.

---

# 38. Same media in multiple portfolio contexts

If one MediaAsset legitimately needs materially distinct contextual classification, system MAY allow multiple PortfolioItems referencing same asset.

But this should be explicit, not accidental duplication.

---

# 39. Recommended baseline

Prefer one active PortfolioItem per MediaAsset unless specific business need requires otherwise.

---

# 40. Primary model

Main Portfolio has two critical primary roles:

```text
Primary Close-Up
Primary Full Body
```

---

# 41. PORT-PRI-001 — Exactly Zero or One Primary Close-Up

Database enforces **at most one** current active primary Close-Up per profile.

Readiness may require exactly one.

---

# 42. PORT-PRI-002 — Exactly Zero or One Primary Full Body

Same.

---

# 43. Why “at most one” DB + readiness

Draft/incomplete Profile can temporarily have none.

Application publication/readiness requires one where business flow needs it.

---

# 44. Primary candidate validation

Primary Close-Up:

```text
category = CLOSE_UP
active
correct profile
MediaAsset = IMAGE
MediaAsset technically usable
not archived
```

---

# 45. Primary Full Body

Same except:

```text
category = FULL_BODY
```

---

# 46. PORT-PRI-003 — Primary Does Not Change Category Automatically

Setting a `WAIST` item as Primary Close-Up is invalid.

System must not silently convert category.

---

# 47. Primary switch

Explicit semantic command.

```text
SetPrimaryCloseUp
SetPrimaryFullBody
```

---

# 48. Primary atomic flow

```text
candidate
   ↓
validate
   ↓
transaction
   ├─ unset existing primary
   └─ set candidate
   ↓
Audit / Outbox
```

---

# 49. Concurrent primary race

Database partial unique + transaction/concurrency protect final invariant.

---

# 50. PORT-PRI-004 — UI Toggle Alone Is Not Authority

---

# 51. Primary and visibility

Setting Primary does not automatically set:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 52. Why

Primary and contextual permission are separate concepts.

---

# 53. Public readiness conflict

If Primary Close-Up exists but:

```text
show_on_public_site = false
```

Public Profile may need another public hero strategy or readiness blocker depending exact Hero policy.

---

# 54. Questionnaire readiness conflict

If Primary Full Body exists but:

```text
allow_in_admin_questionnaires = false
```

Prepared Questionnaire cannot automatically use it.

---

# 55. Builder readiness conflict

If primary Full Body is:

```text
allow_in_public_questionnaire_builder = false
```

Public Builder mandatory baseline may be blocked unless another eligible Main Portfolio Full Body is allowed by final Builder selection policy.

---

# 56. Mandatory questionnaire source rule

Canonical:

> Every generated actor Questionnaire requires both an eligible Main Portfolio Close-Up and an eligible Main Portfolio Full Body.

---

# 57. PORT-QNR-001

No substitute from:

```text
Emotional Portfolio
Projects
Role Media
Social
AI-generated image
```

---

# 58. Prepared Questionnaire eligibility

An item is eligible when:

```text
same profile
active/published as required
correct category
MediaAsset available
allow_in_admin_questionnaires = true
```

---

# 59. Public Builder eligibility

An item is eligible when:

```text
same profile
current
active/published as required
MediaAsset available
allow_in_public_questionnaire_builder = true
```

---

# 60. Public visibility

Independent:

```text
show_on_public_site = true
```

---

# 61. PORT-VIS-001 — Three Independent Contexts

```text
Public Site
Admin Questionnaire
Public Builder
```

must remain independent.

---

# 62. Valid example

```text
Close-Up A:
public=true
questionnaire=true
builder=false
```

---

# 63. Valid example

```text
Close-Up B:
public=false
questionnaire=true
builder=true
```

---

# 64. Public hidden / Builder enabled

Legitimate where professional material is intentionally available for casting document builder but not ordinary site gallery.

---

# 65. PORT-VIS-002 — No Universal `is_public`

---

# 66. Publication lifecycle

Suggested:

```text
DRAFT
PUBLISHED
ARCHIVED
```

---

# 67. Draft

Can be categorized/reordered/configured.

Not ordinary public content.

---

# 68. Published

Eligible for contexts whose visibility flags also permit it.

---

# 69. Archived

Unavailable for current new selection.

Historical snapshots preserve references.

---

# 70. PORT-INV-004 — Published ≠ Visible Everywhere

Publication state and context eligibility are orthogonal.

---

# 71. Save vs Publish

Save Draft does not publish image.

---

# 72. Archive vs Delete

Archive removes from current portfolio.

Does not delete MediaAsset original.

---

# 73. Restore

Preferred:

```text
ARCHIVED → DRAFT
```

then explicit publication.

---

# 74. PORT-INV-005 — Restore Does Not Auto-Publish

---

# 75. Ordering

Portfolio has explicit `display_order`.

---

# 76. PORT-ORD-001 — Created At Is Not Display Order

---

# 77. Reorder operation

Semantic command:

```text
ReorderPortfolio
```

---

# 78. Reorder input

Ordered item identities, bounded and validated.

---

# 79. Reorder transaction

Must ensure:

- IDs belong same profile;
- no duplicates;
- items belong target collection;
- no unauthorized hidden item injection.

---

# 80. Category-specific ordering

System MAY maintain global order plus filter result order.

Preferred baseline:

single portfolio display order.

Filtering preserves relative global order.

---

# 81. Why

Avoid competing order models.

---

# 82. Public Portfolio layout

Main Portfolio SHOULD support casting-efficient browsing rather than purely decorative gallery.

---

# 83. Filter UX

At minimum:

```text
Все
Крупный план
В полный рост
Поясной
Профиль
3/4
Постановочный
```

when category contains visible items.

---

# 84. Empty category filter

Do not show useless empty filter unless UX needs fixed taxonomy.

Preferred Public behavior: hide empty filters.

---

# 85. Casting Quick workflow

At top, user should reach primary:

```text
Close-Up
Full Body
```

quickly.

---

# 86. Public ordering recommendation

Decision-oriented:

```text
Primary Close-Up
Primary Full Body
selected supporting professional images
```

while preserving Admin-configured order after required highlights.

---

# 87. Alternative

If Admin global order is fully authoritative, Primary markers can visually highlight rather than reorder.

Exact UX defined DOC-04x/module implementation.

---

# 88. PORT-INV-006 — Primary Must Be Easy to Discover

Regardless of layout implementation.

---

# 89. Image rendering doctrine

Main Portfolio uses full source framing by default.

---

# 90. Existing canonical requirement

```text
object-fit: contain
original aspect ratio
full source frame
no automatic crop
```

---

# 91. PORT-INV-007 — Portfolio Does Not Modify Original Framing

---

# 92. Thumbnail grid

Admin/Public thumbnail can use layout adaptation only if:

- source is not misrepresented;
- Full Body remains recognizably complete;
- opening full view gives original framing.

Preferred professional display still `contain`.

---

# 93. No portrait standardization by destructive crop

Do not force every image into identical ratio by modifying source.

---

# 94. Background/padding

Responsive UI MAY add neutral layout space around contained image.

Theme handles surrounding presentation.

---

# 95. Theme restrictions

Theme cannot introduce crop that violates Portfolio framing policy.

---

# 96. PORT-INV-008 — Theme Cannot Override Professional Framing Rules

---

# 97. Full-screen/lightbox

Public Portfolio SHOULD allow full professional review.

---

# 98. Lightbox must preserve

```text
source aspect ratio
keyboard navigation
close action
image alt/context
```

---

# 99. Mobile

Swipe/touch navigation MAY be provided.

---

# 100. Accessibility

Portfolio must support:

- keyboard;
- accessible controls;
- alt text;
- focus management;
- no information conveyed only by hover.

---

# 101. Alt text

Should describe image context safely.

Avoid speculative physical judgments.

---

# 102. Caption

PortfolioItem may have contextual caption.

---

# 103. Caption ownership

Caption belongs PortfolioItem, not MediaAsset.

---

# 104. Why

Same MediaAsset may be reused elsewhere with different context.

---

# 105. Localization

Portfolio caption MAY be localized if business need exists.

If implemented, use proper translation structure.

---

# 106. AI captions

BB Assistant may potentially assist later for professional descriptions, but Portfolio caption cannot be auto-published.

---

# 107. Classification assistance

Virtual Operator / AI may suggest:

```text
Close-Up
Full Body
Profile
Waist
Staged
```

---

# 108. PORT-AI-001 — Classification Is Suggestion

Human confirms category.

---

# 109. AI evidence

Suggestion MAY use technical visual analysis.

---

# 110. AI must not

```text
judge attractiveness
rank actress by beauty
alter appearance
reject photo as “unattractive”
```

---

# 111. Technical quality analysis

Allowed:

```text
resolution too low
image corrupt
face not sufficiently visible for intended Close-Up
full figure not in source frame for Full Body
duplicate candidate
```

---

# 112. PORT-AI-002 — Technical Suitability ≠ Aesthetic Scoring

---

# 113. Primary suggestion

AI/VOP MAY recommend Primary Close-Up/Full Body.

Human must confirm.

---

# 114. PORT-AI-003 — AI Cannot Set Primary Automatically

---

# 115. Hero integration

Hero uses Portfolio source for primary professional image.

---

# 116. Default Hero source

Preferred:

```text
Primary Close-Up
```

unless Admin explicitly configures another eligible Main Portfolio presentation image.

---

# 117. Hero image source must remain Main Portfolio

Avoid independent duplicated hero upload.

---

# 118. PORT-HERO-001 — Hero Image Is Projection/Reference

---

# 119. Hero eligibility

Hero candidate must be:

```text
published/current
public-visible
valid MediaAsset
```

---

# 120. Primary hidden publicly

If Primary Close-Up is not public-visible, Hero cannot leak it.

---

# 121. Fallback

System may:

1. choose explicit alternative public Main Portfolio image; or
2. report Hero readiness blocker.

It must not expose hidden image.

---

# 122. Home swipe carousel

Existing roadmap requires home swipe carousel.

---

# 123. Home Portfolio carousel

Should consume selected public Main Portfolio items according to homepage projection/configuration.

---

# 124. Carousel does not create duplicated media/source records

---

# 125. Project “В образе” distinction

Role-specific home showcase is separate:

```text
max 2 home role photos
```

and belongs Projects/Role Media module.

---

# 126. PORT-INV-009 — Main Portfolio Home Carousel ≠ Role “В образе” Carousel

They may appear in same Homepage architecture but use distinct sources/configuration.

---

# 127. Questionnaire integration

Prepared Questionnaire default selects mandatory Main Portfolio photos.

---

# 128. Mandatory baseline

```text
1 Close-Up
1 Full Body
```

at minimum.

---

# 129. Additional photos

Questionnaire configuration may allow selectable extra Main Portfolio photos.

---

# 130. PORT-QNR-002 — Extra Portfolio Photos Are Explicitly Selected

Do not dump entire portfolio into PDF automatically.

---

# 131. Why

PDF must remain decision-efficient.

---

# 132. Questionnaire Snapshot

Publication freezes:

```text
PortfolioItem identity
MediaAsset identity
category
caption used
exact derivative/representation intent
```

as appropriate.

---

# 133. Historical behavior

If PortfolioItem changes/archives later:

historical Revision remains unchanged.

---

# 134. PORT-INV-010 — Current Portfolio Change Never Rewrites Published Questionnaire

---

# 135. Current questionnaire staleness

Changing Primary or relevant selected item MAY mark current Questionnaire Draft/material stale.

---

# 136. Public Builder

Builder offers only eligible Main Portfolio items.

---

# 137. Builder Quick template

Minimum:

```text
Close-Up
Full Body
```

plus other baseline data.

---

# 138. Builder Standard/Extended

May allow extra Portfolio items.

---

# 139. Builder Casting template

Can let casting specialist select exactly the photos needed.

---

# 140. PORT-BLD-001 — Public Builder Cannot Upload/Change Profile Portfolio

It configures temporary document only.

---

# 141. Builder filter

Eligible Main Portfolio items can be grouped by professional category.

---

# 142. Builder Generate

Reloads every selected PortfolioItem.

---

# 143. Generate checks

```text
same profile
published/current
Media ready
Builder eligible
not archived
```

---

# 144. Mandatory Builder Close-Up

At least one eligible Main Portfolio Close-Up.

---

# 145. Mandatory Builder Full Body

At least one eligible Main Portfolio Full Body.

---

# 146. Primary vs alternative in Builder

Builder MAY use another eligible Close-Up/Full Body if product policy intentionally allows public casting specialist choice.

---

# 147. Recommended rule

Default Quick/Standard use current primary eligible items.

Casting/Custom MAY offer other eligible category-correct Main Portfolio items.

---

# 148. Mandatory semantic requirement remains

Whatever user selects:

```text
category = CLOSE_UP
category = FULL_BODY
```

from Main Portfolio.

---

# 149. Profile Search integration

Portfolio filters are public navigation/read-model features.

---

# 150. Search inside profile

May support:

```text
close-up
full body
profile
staged
```

through category labels.

---

# 151. Public Search engine

Individual PortfolioItem need not become global indexed page.

Search can route to portfolio section/filter.

---

# 152. Search scope

Only:

```text
published
show_on_public_site=true
```

items.

---

# 153. Admin Search

Can find draft/archived Portfolio items according to permission.

---

# 154. PORT-INV-011 — Search Cannot Grant Portfolio Access

Route rechecks current policy.

---

# 155. Casting AI integration

Casting analysis may identify requirement such as:

```text
нужны свежие фото крупным планом и в полный рост
```

---

# 156. Recommendation

System can recommend existing eligible Main Portfolio items.

---

# 157. Casting AI cannot reclassify automatically

If image currently category=WAIST:

AI cannot silently treat it as mandatory Full Body.

---

# 158. Human confirmation

Required to change category.

---

# 159. Shooting date / recency

Main Portfolio may later support photo shooting date metadata if professionally useful.

Not currently mandatory source requirement except Emotional Portfolio.

---

# 160. PORT-INV-012 — Do Not Copy Emotional Shooting-Date Requirement to Main Portfolio Automatically

---

# 161. If Main Portfolio photo date added later

Must be:

- explicit;
- Human confirmed;
- separate from EXIF;
- documented in Data Dictionary/module extension.

---

# 162. Media replacement

PortfolioItem may switch `media_asset_id` only through explicit command.

---

# 163. Preferred semantics

Replacing binary behind existing professional item should be treated carefully.

---

# 164. Safer baseline

For meaningful source replacement:

```text
new MediaAsset
+
update PortfolioItem relation
+
version increment
+
staleness propagation
```

---

# 165. Historical questionnaire

Already frozen Revision continues referencing old MediaAsset.

---

# 166. PORT-INV-013 — Replacement Cannot Rewrite Historical Media Reference

---

# 167. PortfolioItem archive

Archive removes item from:

```text
Public Portfolio
Builder eligibility
new Questionnaire selections
Hero candidate set
Public Search
```

---

# 168. Existing historical snapshots

Remain.

---

# 169. Archive Primary

Cannot leave system silently pretending primary exists.

---

# 170. PORT-PRI-005 — Archiving Primary Triggers Readiness Change

Admin must:

- select replacement; or
- accept blocked/incomplete readiness.

---

# 171. Optional UX

Archive Primary action SHOULD warn:

```text
Эта фотография используется как основной крупный план.
После архивации потребуется выбрать новую.
```

---

# 172. Archive not binary delete

MediaAsset remains according to Media dependencies.

---

# 173. Restore

Restored PortfolioItem returns Draft/non-public preferred state.

---

# 174. Hard delete

Rare.

Normally remove/archive `PortfolioItem`, not MediaAsset.

---

# 175. Deleting PortfolioItem

If no history/business reason requires its relation record, hard relation delete may eventually be permitted under retention policy.

But MediaAsset remains independent.

---

# 176. Portfolio dependency graph

Potential dependants:

```text
Hero
Questionnaire Draft
Questionnaire Revision
Builder Session
Builder Snapshot
Public Search
Cache
Analytics references
```

---

# 177. Current relation deletion

Must invalidate projections.

---

# 178. Historical references

Must remain resolvable or self-contained according to revision architecture.

---

# 179. Concurrency

PortfolioItem mutable configuration uses `version`.

---

# 180. Commands with version

At least:

```text
UpdatePortfolioItem
ArchivePortfolioItem
UpdatePortfolioVisibility
```

---

# 181. Collection operations

Primary switch/reorder need appropriate aggregate/container concurrency or transaction locking.

---

# 182. PORT-INV-014 — Concurrent Reorder Must Not Lose Items

---

# 183. Reorder conflict behavior

If collection changed since page load:

return stale/conflict rather than silently overwrite new item ordering.

---

# 184. Primary race

Exactly one current primary after concurrent requests.

---

# 185. Transactions

Primary switch + Audit + Outbox atomic.

---

# 186. Visibility mutation

Portfolio visibility update:

```text
BEGIN
 update item
 Audit
 Outbox
COMMIT
```

then:

```text
Public projection invalidation
Builder invalidation
Search reindex/removal
cache invalidation
questionnaire readiness refresh
```

---

# 187. External processing

No image processor call inside business transaction.

---

# 188. Missing derivative

Commit Portfolio metadata can exist.

Publication/readiness blocks if required derivative unavailable.

---

# 189. Async derivative

Media Library owns its generation.

---

# 190. Portfolio readiness

Context-specific readiness.

---

# 191. Public Portfolio readiness

At minimum:

```text
one current public Close-Up
one current public Full Body
Media available
```

for full professional baseline.

---

# 192. Questionnaire readiness

```text
eligible Admin-Questionnaire Close-Up
eligible Admin-Questionnaire Full Body
```

---

# 193. Builder readiness

```text
eligible Builder Close-Up
eligible Builder Full Body
```

---

# 194. PORT-INV-015 — Readiness Must Be Context-Specific

One item can make Site ready but Builder blocked.

---

# 195. Example

```text
Primary Close-Up:
public=true
adminQ=true
builder=false
```

Then:

```text
Public Portfolio = potentially READY
Prepared Questionnaire = potentially READY
Builder = may be BLOCKED
```

---

# 196. Readiness issue codes

Suggested:

```text
PORTFOLIO_CLOSE_UP_MISSING
PORTFOLIO_FULL_BODY_MISSING
PORTFOLIO_PUBLIC_CLOSE_UP_MISSING
PORTFOLIO_PUBLIC_FULL_BODY_MISSING
PORTFOLIO_QUESTIONNAIRE_CLOSE_UP_MISSING
PORTFOLIO_QUESTIONNAIRE_FULL_BODY_MISSING
PORTFOLIO_BUILDER_CLOSE_UP_MISSING
PORTFOLIO_BUILDER_FULL_BODY_MISSING
PORTFOLIO_PRIMARY_MEDIA_NOT_READY
PORTFOLIO_PRIMARY_ARCHIVED
```

---

# 197. Warning examples

```text
PORTFOLIO_LOW_RESOLUTION
PORTFOLIO_PRIMARY_NOT_PUBLIC
PORTFOLIO_DUPLICATE_CANDIDATE
```

depending exact policy.

---

# 198. Low resolution

Should normally be warning unless output requirement cannot be met.

---

# 199. Professional image quality

Technical criteria may include:

```text
minimum pixel dimensions
decodability
correct framing for category
```

---

# 200. No aesthetic readiness

Do not block based on beauty/attractiveness scoring.

---

# 201. Admin UX structure

Recommended:

1. Main Portfolio grid;
2. category filters;
3. Primary indicators;
4. visibility indicators;
5. upload/select Media;
6. reorder;
7. readiness;
8. Public preview;
9. Questionnaire/Builder eligibility preview;
10. archived items.

---

# 202. Portfolio card

Admin should show:

```text
image preview
category
Primary badge if any
Public
Questionnaire
Builder
processing/readiness
usage
```

---

# 203. No ambiguous icons only

Visibility context labels/tooltips required.

---

# 204. Primary indicators

Explicit:

```text
Основной крупный план
Основное фото в полный рост
```

---

# 205. Set Primary action

Available only on correct category.

---

# 206. Category change on Primary

If changing category makes Primary invalid:

operation must:

- block; or
- explicitly unset Primary in same semantic transaction after confirmation.

---

# 207. Preferred

Block with clear action:

```text
Сначала выберите другой основной крупный план.
```

for high clarity.

---

# 208. PORT-PRI-006 — No Silent Loss of Primary Through Generic Edit

---

# 209. Delete/archive UX

When used by current Questionnaire/Hero:

Admin sees usage implications.

---

# 210. Public preview

Must use same Public Portfolio Projection as real page.

---

# 211. Builder preview

Admin SHOULD be able to preview eligible Builder inventory.

---

# 212. Questionnaire preview

Admin SHOULD identify which photo will satisfy mandatory Close-Up/Full Body.

---

# 213. Media selector

Portfolio can select existing MediaAsset or upload new one.

---

# 214. Existing MediaAsset selector

Only:

```text
profile-owned
IMAGE
non-quarantined
usable
```

assets.

---

# 215. Selecting image creates PortfolioItem

It does not mutate MediaAsset professional metadata globally.

---

# 216. Upload from Portfolio

Flow:

```text
Upload
↓
MediaAsset
↓
processing
↓
PortfolioItem
↓
Human category
```

---

# 217. AI-assisted category

After upload:

```text
AI suggests category
↓
Admin confirms/changes
```

---

# 218. PORT-AI-004 — AI Category Must Be Visibly Marked as Suggested Until Confirmed

---

# 219. Batch upload

May support multiple images.

---

# 220. Batch workflow

Recommended:

```text
upload
↓
technical processing
↓
suggested classification
↓
Human review
↓
publish/visibility
```

---

# 221. Batch publish

Must not publish unreviewed categories automatically.

---

# 222. Duplicate Media usage

If same image already Portfolio item:

UI should warn/prevent accidental duplicate active PortfolioItem unless intentional.

---

# 223. Selection filters

Admin Media selector can filter:

```text
unused
already in portfolio
orientation
technical readiness
```

---

# 224. Home integration

Public Homepage portfolio section uses selected/ordered public Portfolio Projection.

---

# 225. Primary Close-Up may also serve Hero.

---

# 226. Full Body placement

Homepage may expose quickly without requiring full portfolio scroll.

---

# 227. Business objective

Professional decision-maker should access Full Body within one action from profile/home.

---

# 228. Action budget

Canonical:

```text
Portfolio = 1 action
```

from main page.

---

# 229. Filter action budget

Category search/filter:

```text
1 action
```

within Portfolio.

---

# 230. Deep links

Portfolio filters MAY support stable deep links:

```text
/portfolio?type=full-body
```

or route fragments.

Exact route implementation later.

---

# 231. Marketing/casting deep links

Could link directly to:

```text
Close-Up
Full Body
Profile
```

without duplicating content.

---

# 232. URL state

Filter URL must not grant access to hidden items.

---

# 233. Portfolio Projection catalogue

At least:

```text
PublicPortfolioProjection
AdminPortfolioProjection
QuestionnairePortfolioProjection
BuilderPortfolioProjection
HeroPortfolioProjection
PortfolioReadinessProjection
```

---

# 234. Public Portfolio DTO

Conceptually:

```text
{
  primaryCloseUp?,
  primaryFullBody?,
  categories,
  items[]
}
```

---

# 235. Admin DTO

Includes:

```text
id
version
media
category
caption
publicationState
visibility
displayOrder
primaryRole?
readiness
usage
```

---

# 236. Builder DTO

Minimal:

```text
id
category
preview
selected
```

only eligible items.

---

# 237. Questionnaire projection

Provides category-correct selectable items.

---

# 238. Hero projection

Provides only current public-safe candidate.

---

# 239. Media DTO

Uses safe derivative URL.

No storage locator.

---

# 240. Commands

Canonical:

```text
CreatePortfolioItem
UpdatePortfolioItem
UpdatePortfolioCategory
UpdatePortfolioVisibility
ReorderPortfolio
SetPrimaryCloseUp
SetPrimaryFullBody
PublishPortfolioItem
ArchivePortfolioItem
RestorePortfolioItem
```

---

# 241. Optional

```text
BatchCreatePortfolioItems
BatchUpdatePortfolioVisibility
```

only if validation remains explicit.

---

# 242. Queries

```text
GetPublicPortfolio
GetAdminPortfolio
GetPortfolioItem
GetPortfolioReadiness
ListQuestionnaireEligiblePortfolio
ListBuilderEligiblePortfolio
GetHeroPortfolioCandidate
PreviewPublicPortfolio
```

---

# 243. CreatePortfolioItem input

Conceptually:

```text
mediaAssetId
category
caption?
visibility
```

---

# 244. Server validations

```text
MediaAsset exists
same profile
IMAGE
not quarantined
category valid
```

---

# 245. Client cannot set

```text
createdAt
checksum
storage path
technical readiness
confirmedBy AI
```

---

# 246. SetPrimaryCloseUp input

```text
portfolioItemId
expectedVersion / aggregate version
```

---

# 247. SetPrimaryFullBody

Same.

---

# 248. Reorder input

```text
orderedIds[]
expectedCollectionVersion
```

or equivalent.

---

# 249. Visibility input

Exactly:

```text
showOnPublicSite
allowInAdminQuestionnaires
allowInPublicQuestionnaireBuilder
```

---

# 250. Error codes

At minimum:

```text
PORTFOLIO_ITEM_NOT_FOUND
PORTFOLIO_MEDIA_INVALID
PORTFOLIO_MEDIA_WRONG_PROFILE
PORTFOLIO_CATEGORY_INVALID
PORTFOLIO_PRIMARY_WRONG_CATEGORY
PORTFOLIO_PRIMARY_CONFLICT
PORTFOLIO_ITEM_ARCHIVED
PORTFOLIO_ITEM_NOT_READY
PORTFOLIO_REORDER_CONFLICT
PORTFOLIO_DUPLICATE_ITEM
PORTFOLIO_PUBLIC_CLOSE_UP_MISSING
PORTFOLIO_PUBLIC_FULL_BODY_MISSING
PORTFOLIO_BUILDER_CLOSE_UP_MISSING
PORTFOLIO_BUILDER_FULL_BODY_MISSING
```

---

# 251. Audit

High-impact operations SHOULD audit:

```text
category change
Primary change
public visibility change
Questionnaire/Builder eligibility change
publish
archive/restore
```

---

# 252. Routine reorder

May be audited if professional presentation history is important, otherwise operational change record may suffice.

---

# 253. Audit must not duplicate binary metadata.

---

# 254. Outbox events

Suggested:

```text
PortfolioItemCreated
PortfolioItemUpdated
PortfolioItemPublished
PortfolioItemArchived
PortfolioPrimaryChanged
PortfolioVisibilityChanged
PortfolioOrderChanged
```

---

# 255. Consumers

```text
Public cache
Search
Profile/Hero
Questionnaire readiness
Builder eligibility
VOP
Analytics
```

---

# 256. Event payload

Minimal:

```text
profile_id
portfolio_item_id
new version
changed aspect
```

---

# 257. No image bytes in Outbox

Normative.

---

# 258. Cache

Public Portfolio can be cached per:

```text
profile
locale
projection generation/version
```

---

# 259. Visibility revocation

High-priority cache invalidation.

---

# 260. Primary change

Invalidates:

```text
Public Portfolio
Hero
Quick casting projection
Questionnaire readiness
possibly current PDF alias/readiness
```

---

# 261. Builder visibility change

Invalidates Builder eligibility projection/session freshness.

---

# 262. Search

Public search/filter projection rebuild.

---

# 263. Historical Revision unaffected

Normative.

---

# 264. Analytics

Relevant events:

```text
portfolio_opened
portfolio_filter_selected
portfolio_image_opened
```

---

# 265. Avoid vanity ranking

Analytics cannot automatically decide which actress photo is “best” and change Primary.

---

# 266. VOP recommendation

Could say:

```text
Primary Full Body is not Builder eligible.
Public Builder is blocked.
```

---

# 267. VOP may detect

```text
missing Primary Close-Up
missing Primary Full Body
Primary media failed
duplicate candidates
category missing
visibility mismatch
```

---

# 268. VOP safe automation

May:

```text
rebuild derivative
reindex search
invalidate cache
recompute readiness
```

---

# 269. VOP requires confirmation for

```text
change category
set Primary
change visibility
archive item
```

---

# 270. Portfolio and AI outage

Portfolio must remain fully manually manageable without AI.

---

# 271. PORT-INV-016 — AI Is Optional

---

# 272. Portfolio and Media outage

Existing successfully processed public derivatives should remain usable where infrastructure permits.

New processing may be temporarily blocked.

---

# 273. Graceful failure

One failed photo does not crash entire Portfolio.

---

# 274. Public failed media

Omit/degrade affected item safely.

If failed item is Primary required content, readiness/admin warning becomes critical.

---

# 275. No broken mandatory PDF image

Questionnaire generation should block/fail before final READY PDF if required image unavailable.

---

# 276. SEO

Portfolio public images may participate in safe image metadata.

---

# 277. Hidden Portfolio images

Must not appear in:

```text
OpenGraph
JSON-LD
sitemap
preload
public image feeds
```

---

# 278. Security

Public routes never expose unpublished item by guessed UUID.

---

# 279. Signed/protected original

Admin full-resolution original view requires authorized delivery if original is private.

---

# 280. Public high-res

Use controlled derivative rather than raw original by default.

---

# 281. Why

- metadata privacy;
- bandwidth;
- original preservation;
- policy control.

---

# 282. Download original

If Admin feature exists, authenticated only.

---

# 283. Screenshot/download prevention

Not considered reliable security.

Publicly displayed image can be captured.

Architecture controls server access, not impossible client copying.

---

# 284. No watermark baseline

Watermarking is not mandatory and should not damage professional casting materials unless explicitly required later.

---

# 285. Retention

PortfolioItem is professional Source/configuration.

Archive preferred.

---

# 286. MediaAsset retention

Independent.

---

# 287. Historical Portfolio references

QuestionnaireRevision may preserve item/media values beyond current Portfolio lifecycle.

---

# 288. Privacy deletion

If a Portfolio image must be fully removed for privacy/legal reasons:

must inspect:

```text
current Portfolio
historical Questionnaires
Builder snapshots
PDF artifacts
Search
Cache
Social copies
AI snapshots
```

according to DOC-094.

---

# 289. External copies

Already sent/downloaded/socially published copies cannot be guaranteed deleted solely by Portfolio archive.

---

# 290. Migration

Existing photos should map to:

```text
MediaAsset
+
PortfolioItem
+
category
```

---

# 291. Unknown legacy category

Do not invent.

Use:

```text
OTHER
```

or `requires_review` migration state according to migration plan.

---

# 292. Legacy crop

If old UI displayed images using crop but original is intact:

new Main Portfolio should default to original contain framing.

---

# 293. PORT-INV-017 — Migration Preserves Original Framing

---

# 294. Legacy Primary

If existing system clearly defines main Close-Up/Full Body, map deliberately.

Otherwise require Admin confirmation.

---

# 295. No AI bulk autopublish

AI may classify legacy photos but Human confirms before professional categories/Primary become authoritative.

---

# 296. Anti-patterns

`PORT-AP-001`  
Store original image bytes in Portfolio table.

`PORT-AP-002`  
Store portfolio category globally on MediaAsset.

`PORT-AP-003`  
Use Role image as mandatory Full Body without PortfolioItem.

`PORT-AP-004`  
Use Emotional photo as mandatory Close-Up.

`PORT-AP-005`  
Allow zero-category published items without review.

`PORT-AP-006`  
Use `OTHER` for every unclassified image indefinitely.

`PORT-AP-007`  
AI auto-classifies and publishes without confirmation.

`PORT-AP-008`  
AI chooses Primary automatically.

`PORT-AP-009`  
Judge/score attractiveness to select Primary.

`PORT-AP-010`  
Crop Full Body using `cover`.

`PORT-AP-011`  
Destructively crop original.

`PORT-AP-012`  
Theme overrides contain rule and crops actress.

`PORT-AP-013`  
One `is_public` controls Site/QNR/Builder.

`PORT-AP-014`  
Setting Primary automatically makes image public everywhere.

`PORT-AP-015`  
Hiding Public item automatically removes it from Builder.

`PORT-AP-016`  
Created timestamp used as display order.

`PORT-AP-017`  
Reorder trusts arbitrary IDs from browser.

`PORT-AP-018`  
Public Builder browses raw MediaAssets.

`PORT-AP-019`  
Questionnaire uses current Portfolio during historical PDF rerender instead of Revision snapshot.

`PORT-AP-020`  
Archive PortfolioItem deletes original MediaAsset.

`PORT-AP-021`  
Restored item auto-publishes.

`PORT-AP-022`  
Changing category silently drops Primary state.

`PORT-AP-023`  
Changing image overwrites original binary behind same MediaAsset.

`PORT-AP-024`  
Public DTO includes hidden item with `visible=false`.

`PORT-AP-025`  
Hidden image included in preload/SEO despite not rendered.

`PORT-AP-026`  
Global Search exposes Draft item title/caption.

`PORT-AP-027`  
Questionnaire PDF automatically includes every portfolio image.

`PORT-AP-028`  
Low-resolution warning treated as attractiveness criticism.

`PORT-AP-029`  
Casting AI rewrites category to fit casting requirement.

`PORT-AP-030`  
VOP archives “unused” professional photo automatically.

`PORT-AP-031`  
Hero stores duplicate upload separate from Portfolio.

`PORT-AP-032`  
Homepage role carousel and Main Portfolio carousel use same configuration source.

`PORT-AP-033`  
Current Primary change rewrites historical QuestionnaireRevision.

`PORT-AP-034`  
Public photo exists because MediaAsset has public derivative even if PortfolioItem is private.

`PORT-AP-035`  
Primary uniqueness enforced only in UI.

`PORT-AP-036`  
No expected version on category/visibility edits.

`PORT-AP-037`  
Browser supplies `publishedAt/publishedBy`.

`PORT-AP-038`  
Media processor called inside Portfolio DB transaction.

`PORT-AP-039`  
Runtime AI needed to render portfolio.

`PORT-AP-040`  
Public Portfolio becomes decorative masonry that makes required Full Body hard to find.

---

# 297. Core invariants

`PORT-INV-018`  
Every PortfolioItem belongs to exactly one Actor Profile.

`PORT-INV-019`  
Every PortfolioItem references an IMAGE MediaAsset.

`PORT-INV-020`  
Critical cross-profile references are rejected.

`PORT-INV-021`  
Media original remains immutable.

`PORT-INV-022`  
PortfolioItem owns professional category.

`PORT-INV-023`  
PortfolioItem owns portfolio caption.

`PORT-INV-024`  
PortfolioItem owns context visibility.

`PORT-INV-025`  
MediaAsset does not imply portfolio publication.

`PORT-INV-026`  
Primary Close-Up must be category CLOSE_UP.

`PORT-INV-027`  
Primary Full Body must be category FULL_BODY.

`PORT-INV-028`  
At most one active Primary Close-Up per profile.

`PORT-INV-029`  
At most one active Primary Full Body per profile.

`PORT-INV-030`  
Readiness may require exactly one.

`PORT-INV-031`  
Primary and visibility are independent.

`PORT-INV-032`  
Primary switch is atomic.

`PORT-INV-033`  
Primary change uses Human/domain command.

`PORT-INV-034`  
AI cannot autonomously set Primary.

`PORT-INV-035`  
Close-Up/Full Body requirements cannot be satisfied by Emotional media.

`PORT-INV-036`  
Close-Up/Full Body requirements cannot be satisfied by Project/Role media.

`PORT-INV-037`  
Main Portfolio keeps source framing by default.

`PORT-INV-038`  
Full Body cannot be arbitrarily cropped in professional outputs.

`PORT-INV-039`  
Theme cannot weaken framing requirements.

`PORT-INV-040`  
Display order is explicit.

`PORT-INV-041`  
Reordering does not change category/visibility.

`PORT-INV-042`  
Public/Questionnaire/Builder permissions remain independent.

`PORT-INV-043`  
Hidden Public item is absent from Public DTO.

`PORT-INV-044`  
Builder only receives Builder-eligible PortfolioItems.

`PORT-INV-045`  
Prepared Questionnaire only receives Questionnaire-eligible PortfolioItems.

`PORT-INV-046`  
Questionnaire mandatory photos originate only Main Portfolio.

`PORT-INV-047`  
Questionnaire extra photos are explicit selections.

`PORT-INV-048`  
Historical Questionnaire snapshot freezes exact portfolio media semantics.

`PORT-INV-049`  
Current Portfolio mutation does not rewrite history.

`PORT-INV-050`  
Builder Generate revalidates current eligibility.

`PORT-INV-051`  
Builder cannot mutate Portfolio Source.

`PORT-INV-052`  
Hero uses public-safe Main Portfolio projection/reference.

`PORT-INV-053`  
Hero image is not a separate binary Source.

`PORT-INV-054`  
Home role showcase and Main Portfolio remain different concerns.

`PORT-INV-055`  
Technical media readiness and portfolio eligibility are different.

`PORT-INV-056`  
AI classification is non-authoritative until Human confirmation.

`PORT-INV-057`  
Technical quality analysis must not become attractiveness scoring.

`PORT-INV-058`  
Archive removes current exposure but preserves history.

`PORT-INV-059`  
Archive does not delete MediaAsset.

`PORT-INV-060`  
Restore does not auto-publish.

`PORT-INV-061`  
Archiving Primary changes readiness.

`PORT-INV-062`  
Historical dependencies remain valid after archive.

`PORT-INV-063`  
Public Search cannot expose Draft/private PortfolioItem.

`PORT-INV-064`  
Search hit never grants access.

`PORT-INV-065`  
SEO cannot exceed Public Portfolio visibility.

`PORT-INV-066`  
Public Preview uses actual Public Projection.

`PORT-INV-067`  
Portfolio works without AI.

`PORT-INV-068`  
AI provider failure cannot block manual categorization.

`PORT-INV-069`  
One failed optional photo does not crash entire Portfolio.

`PORT-INV-070`  
Mandatory failed photo creates readiness blocker.

`PORT-INV-071`  
Visibility revocation invalidates public/builder/search caches.

`PORT-INV-072`  
Portfolio changes emit only safe event metadata.

`PORT-INV-073`  
External processing is post-commit/asynchronous.

`PORT-INV-074`  
Optimistic concurrency protects mutable item configuration.

`PORT-INV-075`  
DB uniqueness protects Primary races.

`PORT-INV-076`  
Migration does not invent professional classification without review where uncertain.

`PORT-INV-077`  
Migration preserves original source framing.

`PORT-INV-078`  
Portfolio classification cannot alter actress appearance.

`PORT-INV-079`  
Portfolio has no dependency on runtime AI to serve public assets.

`PORT-INV-080`  
Professional navigation prioritizes rapid access to Close-Up and Full Body.

---

# 298. E2E-PORТ-001 — Create Close-Up

Select valid profile-owned IMAGE MediaAsset and create:

```text
category=CLOSE_UP
```

PortfolioItem created successfully.

---

# 299. E2E-PORT-002 — Non-image asset

Attempt VIDEO MediaAsset as photo PortfolioItem.

Rejected.

---

# 300. E2E-PORT-003 — Cross-profile media

Profile A attempts MediaAsset owned by Profile B.

Rejected.

---

# 301. E2E-PORT-004 — Set Primary Close-Up

Correct category succeeds.

Exactly one Primary Close-Up remains.

---

# 302. E2E-PORT-005 — Wrong Primary category

Attempt WAIST item as Primary Close-Up.

Expected:

```text
PORTFOLIO_PRIMARY_WRONG_CATEGORY
```

---

# 303. E2E-PORT-006 — Primary Full Body

Correct Full Body can become Primary.

---

# 304. E2E-PORT-007 — Concurrent Primary Close-Up

Two Admin requests race.

Expected:

```text
one winner
one semantic conflict/retry
one final Primary
```

---

# 305. E2E-PORT-008 — Primary visibility independence

Set Primary.

Visibility flags remain unchanged.

---

# 306. E2E-PORT-009 — Public-only item

```text
public=true
adminQ=false
builder=false
```

Visible site only.

---

# 307. E2E-PORT-010 — Questionnaire-only item

```text
public=false
adminQ=true
builder=false
```

Hidden Public, eligible prepared Questionnaire.

---

# 308. E2E-PORT-011 — Builder-only item

```text
public=false
adminQ=false
builder=true
```

Hidden ordinary site, eligible Builder.

---

# 309. E2E-PORT-012 — All visibility combinations

All eight combinations preserve independent semantics.

---

# 310. E2E-PORT-013 — Public hidden serialization

Hidden item absent from Public:

```text
HTML
RSC/JSON
preload
SEO
Search
```

---

# 311. E2E-PORT-014 — Questionnaire mandatory Close-Up

Prepared Questionnaire finds eligible Main Portfolio Close-Up.

---

# 312. E2E-PORT-015 — Emotional substitution

Try Emotional image as mandatory Close-Up.

Rejected.

---

# 313. E2E-PORT-016 — Role substitution

Try RoleMedia as mandatory Full Body.

Rejected.

---

# 314. E2E-PORT-017 — Builder missing Close-Up

No Builder-eligible Close-Up.

Generate blocked with specific readiness code.

---

# 315. E2E-PORT-018 — Builder missing Full Body

Same.

---

# 316. E2E-PORT-019 — Builder alternative category-correct photo

Casting/Custom Builder selects another eligible Main Portfolio Full Body where policy permits.

Generation succeeds.

---

# 317. E2E-PORT-020 — Builder raw Media ID

Visitor submits arbitrary IMAGE MediaAsset not wrapped as eligible PortfolioItem.

Rejected.

---

# 318. E2E-PORT-021 — Reorder

Reorder valid item set.

Public ordering updates.

---

# 319. E2E-PORT-022 — Reorder duplicate IDs

Rejected.

---

# 320. E2E-PORT-023 — Reorder foreign item

Rejected.

---

# 321. E2E-PORT-024 — Concurrent reorder

Collection changes before stale reorder submit.

Stale operation cannot lose newly added item.

---

# 322. E2E-PORT-025 — Source aspect

Portrait source renders contained with complete frame.

---

# 323. E2E-PORT-026 — Full Body framing

Full Body remains complete in Public and PDF.

---

# 324. E2E-PORT-027 — Theme crop attack

Theme tries `object-fit: cover` for mandatory Full Body professional output.

Professional component constraints preserve required framing.

---

# 325. E2E-PORT-028 — AI classification

AI suggests CLOSE_UP.

Before Human confirmation category does not become authoritative.

---

# 326. E2E-PORT-029 — AI unavailable

Admin manually categorizes photo and publishes Portfolio normally.

---

# 327. E2E-PORT-030 — AI Primary recommendation

AI suggests Primary.

No change until Human executes Primary command.

---

# 328. E2E-PORT-031 — Media replacement

PortfolioItem references M1.

Admin replaces with new M2.

Historical Questionnaire R1 still references M1.

---

# 329. E2E-PORT-032 — Current rerender

Current Portfolio displays M2 after valid mutation.

---

# 330. E2E-PORT-033 — Historical Questionnaire

R1 remains visually/semantically based on M1.

---

# 331. E2E-PORT-034 — Archive non-primary

Item disappears from current Public/Builder/new Questionnaire selection.

MediaAsset remains.

---

# 332. E2E-PORT-035 — Archive Primary

Readiness reports missing Primary after archive unless replacement chosen.

---

# 333. E2E-PORT-036 — Restore

Restored item does not become public automatically.

---

# 334. E2E-PORT-037 — Search

Public category search only returns public current items.

---

# 335. E2E-PORT-038 — Stale Search

Archive item while Search is stale.

Direct route/projection does not expose archived item.

---

# 336. E2E-PORT-039 — Hero

Public Primary Close-Up becomes Hero candidate.

---

# 337. E2E-PORT-040 — Hidden Hero candidate

Primary Close-Up becomes Public-hidden.

Hero does not leak image.

---

# 338. E2E-PORT-041 — Questionnaire Draft staleness

Selected Portfolio item changes/replaced.

Questionnaire Draft readiness becomes stale according to dependency policy.

Published historical Revision unchanged.

---

# 339. E2E-PORT-042 — Builder session revocation

Selected Portfolio item loses Builder permission before Generate.

Server removes/blocks it.

---

# 340. E2E-PORT-043 — Failed derivative

Optional Staged image derivative fails.

Other Portfolio items continue to render.

---

# 341. E2E-PORT-044 — Failed Primary derivative

Primary Full Body unavailable.

Relevant readiness is blocked.

---

# 342. E2E-PORT-045 — Audit Primary

Primary switch records safe Audit event.

---

# 343. E2E-PORT-046 — Outbox

Primary/visibility mutation commits Outbox with Source transaction.

---

# 344. E2E-PORT-047 — Cache revocation

Public item cached.

Public flag disabled.

Next Public request does not expose stale item.

---

# 345. E2E-PORT-048 — SEO revocation

Hidden image absent from OpenGraph/structured metadata.

---

# 346. E2E-PORT-049 — Archive does not purge Media

Asset usage graph still shows historical relationship where relevant.

---

# 347. E2E-PORT-050 — Duplicate active Portfolio item

Attempt accidental second identical PortfolioItem for same asset/context.

System warns/rejects according to uniqueness policy.

---

# 348. E2E-PORT-051 — Caption context

Same MediaAsset reused in another domain.

Changing Portfolio caption does not change other domain caption.

---

# 349. E2E-PORT-052 — Public filter

Select `FULL_BODY`.

Only public Full Body items returned/displayed.

---

# 350. E2E-PORT-053 — Empty filter

No public Profile category images.

Public UX does not expose broken/empty category control by default.

---

# 351. E2E-PORT-054 — Deep link hidden item

Direct category/filter URL cannot expose hidden PortfolioItem.

---

# 352. E2E-PORT-055 — Runtime without AI

Disable all AI capabilities.

Public Portfolio and all core Admin CRUD remain available.

---

# 353. E2E-PORT-056 — Migration unknown category

Legacy photo with no reliable type is not invented as Full Body/Close-Up.

Marked `OTHER`/review-required according to migration mapping.

---

# 354. E2E-PORT-057 — Migration framing

Legacy image with original aspect ratio remains uncropped after migration.

---

# 355. E2E-PORT-058 — Privacy removal

Privacy revocation removes current Portfolio and derived public exposures while historical redaction policy is applied independently.

---

# 356. E2E-PORT-059 — Main vs Role carousel

Changing Role “В образе” selection does not modify Main Portfolio carousel/source configuration.

---

# 357. E2E-PORT-060 — Quick professional journey

From Public Profile, user reaches Main Portfolio within one action and can reach Full Body within one additional filter/action at most.

---

# 358. Architecture diagram

```text
              MEDIA LIBRARY
                   │
                   ▼
              MediaAsset
                   │
                   ▼
              PortfolioItem
                   │
    ┌──────────────┼──────────────┐
    ▼              ▼              ▼
 Category       Visibility       Order
    │              │              │
    └──────────────┼──────────────┘
                   ▼
              Projections
    ┌──────────────┼───────────────┬─────────────┐
    ▼              ▼               ▼             ▼
 Public      Questionnaire      Builder         Hero
```

---

# 359. Primary architecture

```text
PortfolioItems
     │
     ├── category=CLOSE_UP ───→ Primary Close-Up
     │
     └── category=FULL_BODY ──→ Primary Full Body

DB:
at most one active primary of each role

Readiness:
required context may demand exactly one eligible item
```

---

# 360. Questionnaire flow

```text
Main Portfolio
    ↓
eligible Close-Up + Full Body
    ↓
Questionnaire Draft
    ↓
Publish Validation
    ↓
QuestionnaireRevision
    ↓
HTML / PDF
```

---

# 361. Builder flow

```text
Main Portfolio
    ↓
Builder Eligibility Projection
    ↓
Close-Up / Full Body / Extra Photos
    ↓
Visitor Selection
    ↓
Generate Revalidation
    ↓
BuilderGenerationSnapshot
```

---

# 362. Hero flow

```text
Primary Close-Up
     ↓
Public visibility
     ↓
Media readiness
     ↓
HeroPortfolioProjection
     ↓
Hero
```

---

# 363. AI classification flow

```text
MediaAsset
    ↓
AI/CV Analysis
    ↓
Category Suggestion
    ↓
Human Review
    ↓
UpdatePortfolioCategory
    ↓
Authoritative PortfolioItem
```

---

# 364. Archive flow

```text
PortfolioItem
    ↓
Archive
    ↓
Current Projections Removed
    ↓
MediaAsset retained
    ↓
Historical snapshots unchanged
```

---

# 365. Quality gate

Перед implementation должны быть подтверждены:

- [ ] canonical category taxonomy;
- [ ] localized category labels;
- [ ] Close-Up semantics;
- [ ] Full Body semantics;
- [ ] Primary Close-Up;
- [ ] Primary Full Body;
- [ ] DB at-most-one constraints;
- [ ] readiness exact-one requirements;
- [ ] MediaAsset reference model;
- [ ] no-crop/contain rendering;
- [ ] visibility triplet;
- [ ] publication lifecycle;
- [ ] ordering;
- [ ] public filters;
- [ ] Hero integration;
- [ ] Home Portfolio integration;
- [ ] Role home-showcase separation;
- [ ] Questionnaire mandatory rules;
- [ ] extra Questionnaire photo selection;
- [ ] Public Builder eligibility;
- [ ] Builder alternative-photo policy;
- [ ] Search integration;
- [ ] AI suggestion confirmation;
- [ ] VOP boundaries;
- [ ] archive/restore;
- [ ] historical media protection;
- [ ] cache/search invalidation;
- [ ] audit/outbox;
- [ ] migration mapping;
- [ ] accessibility;
- [ ] E2E suite.

---

# 366. Acceptance criteria

`AC-PORT-001`  
Main Portfolio is a separate domain from Media Library.

`AC-PORT-002`  
Every PortfolioItem references immutable IMAGE MediaAsset.

`AC-PORT-003`  
Every active PortfolioItem has controlled professional category.

`AC-PORT-004`  
System supports Close-Up.

`AC-PORT-005`  
System supports Full Body.

`AC-PORT-006`  
System supports Waist.

`AC-PORT-007`  
System supports Profile.

`AC-PORT-008`  
System supports 3/4.

`AC-PORT-009`  
System supports Staged.

`AC-PORT-010`  
At most one active Primary Close-Up exists per profile.

`AC-PORT-011`  
At most one active Primary Full Body exists per profile.

`AC-PORT-012`  
Primary candidate must have matching category.

`AC-PORT-013`  
Primary switch is atomic.

`AC-PORT-014`  
AI cannot autonomously select Primary.

`AC-PORT-015`  
Original image is never destructively cropped.

`AC-PORT-016`  
Full Body preserves complete professional framing.

`AC-PORT-017`  
Theme cannot override mandatory framing rules.

`AC-PORT-018`  
Public Site, Admin Questionnaire and Public Builder permissions are independent.

`AC-PORT-019`  
Hidden Public Portfolio item does not reach browser payload.

`AC-PORT-020`  
Prepared Questionnaire mandatory Close-Up comes only from Main Portfolio.

`AC-PORT-021`  
Prepared Questionnaire mandatory Full Body comes only from Main Portfolio.

`AC-PORT-022`  
Emotional images cannot substitute those mandatory types.

`AC-PORT-023`  
Role images cannot substitute them.

`AC-PORT-024`  
Extra Questionnaire images are explicitly selectable.

`AC-PORT-025`  
Public Builder uses only Builder-eligible PortfolioItems.

`AC-PORT-026`  
Builder Generate revalidates selected PortfolioItems.

`AC-PORT-027`  
Builder cannot edit professional image classification/source facts.

`AC-PORT-028`  
Historical QuestionnaireRevision preserves exact selected image identity.

`AC-PORT-029`  
Changing current Portfolio cannot rewrite historical Revision.

`AC-PORT-030`  
Hero resolves its image through Main Portfolio Projection.

`AC-PORT-031`  
Hero does not create a second image Source.

`AC-PORT-032`  
Public filters support casting-oriented category navigation.

`AC-PORT-033`  
Portfolio order is explicit.

`AC-PORT-034`  
Concurrent reorder cannot silently destroy newer collection state.

`AC-PORT-035`  
Archive removes item from current projections.

`AC-PORT-036`  
Archive does not delete MediaAsset.

`AC-PORT-037`  
Restore does not auto-publish.

`AC-PORT-038`  
Archiving Primary changes readiness.

`AC-PORT-039`  
Search contains only current public Portfolio information.

`AC-PORT-040`  
SEO does not expose hidden images.

`AC-PORT-041`  
AI classification remains recommendation until Human confirmation.

`AC-PORT-042`  
No attractiveness/body-quality ranking controls Portfolio state.

`AC-PORT-043`  
Portfolio remains fully functional without AI.

`AC-PORT-044`  
Primary/visibility changes propagate through Audit/Outbox/invalidation.

`AC-PORT-045`  
Main Portfolio and Role “В образе” home configuration remain separate.

`AC-PORT-046`  
Migration preserves original image framing.

`AC-PORT-047`  
Unknown legacy category is not invented as mandatory Close-Up/Full Body.

`AC-PORT-048`  
Portfolio supports rapid professional access to Close-Up and Full Body.

---

# 367. Финальная доктрина

> **Main Portfolio является профессиональной классификационной оболочкой над immutable MediaAssets. Он определяет, что конкретная фотография означает в casting context — Close-Up, Full Body, Profile, Waist, 3/4, Staged или другой утверждённый тип — и отдельно управляет Primary, порядком, publication и eligibility. Обязательные Close-Up и Full Body для актёрской анкеты могут происходить только из Main Portfolio; Emotional и Role photos не являются их заменой. Public Site, Prepared Questionnaire и Public Builder используют независимые permission dimensions. Изображения показываются с сохранением исходного кадра и aspect ratio, а AI может только предлагать classification/Primary, но никогда не менять professional state без Human confirmation.**