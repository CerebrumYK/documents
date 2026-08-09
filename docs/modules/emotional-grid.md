# EMOTIONAL GRID MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация Grid Draft, cell composition, Human confirmation, immutable GridRevision и MASTER / WEB / PDF / THUMBNAIL artifacts

**Целевой файл:** `docs/modules/emotional-grid.md`  
**Документ:** DOC-114  
**Статус:** ✅ Completed  
**Тип:** Module / Emotional Grid / Composition / Crop / Human Review / Derived Artifacts

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
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
- `docs/modules/media-library.md`
- `docs/modules/emotional-portfolio.md`

---

# 1. Назначение модуля

Emotional Grid — специализированный модуль создания компактного профессионального composite из фотографий одной Emotional Session.

Grid предназначен для:

- мгновенной оценки эмоционального диапазона;
- Homepage emotional preview;
- Public Emotional Portfolio;
- prepared Questionnaire;
- Public Questionnaire Builder;
- PDF;
- Casting Quick View;
- professional sharing.

Grid **не является новым источником фотографий**.

Он является:

> **Human-confirmed композиционной конфигурацией и immutable Revision, производной от immutable source photographs Emotional Session.**

---

# 2. Главная доктрина

Canonical:

```text
Emotional Session
      ↓
Immutable Source Photos
      ↓
Grid Draft
      ↓
Cell Assignment
      ↓
Crop / Pan / Scale / Rotation
      ↓
Face Occupancy Validation
      ↓
HUMAN CONFIRMATION — EVERY CELL
      ↓
Finalize
      ↓
Immutable GridRevision
      ↓
MASTER / WEB / PDF / THUMBNAIL
```

---

# 3. Fundamental separation

```text
MediaAsset
≠
EmotionalSession
≠
EmotionalGrid
≠
EmotionalGridDraft
≠
EmotionalGridCell
≠
EmotionalGridRevision
≠
EmotionalGridArtifact
```

---

# 4. Module identifiers

Используются:

```text
GRID-*
GRID-SIZE-*
GRID-CELL-*
GRID-XFM-*
GRID-FACE-*
GRID-CONF-*
GRID-REV-*
GRID-ART-*
GRID-AI-*
GRID-QNR-*
GRID-BLD-*
GRID-INV-*
GRID-AP-*
E2E-GRID-*
```

---

# 5. Business purpose

Grid должен позволять профессиональному пользователю увидеть эмоциональный диапазон **без необходимости открывать десятки отдельных фотографий**.

При этом компактность не должна достигаться за счёт:

- изменения внешности;
- автоматической ретуши;
- генерации новых изображений;
- смешивания разных съёмок;
- случайного cropping;
- неподтверждённых AI-композиций.

---

# 6. Grid source

Каждый Grid принадлежит:

```text
one Actor Profile
+
one Emotional Session
```

---

# 7. GRID-INV-001 — One Session Only

Все source images Grid должны принадлежать одной и той же Emotional Session.

---

# 8. Почему

Grid должен иметь единый:

- shooting context;
- shooting date;
- visual provenance;
- professional meaning.

---

# 9. Prohibited mix

Нельзя собрать один Grid из:

```text
Session July 2026
+
Session January 2025
+
Main Portfolio Close-Up
+
Role photo
```

---

# 10. Source eligibility

Grid cell source должен быть:

```text
same profile
same Emotional Session
MediaAsset.type = IMAGE
not quarantined
technically usable
```

---

# 11. Allowed Grid dimensions

Разрешены **только** следующие размеры:

```text
1×2
1×3
1×4

2×2
2×3
2×4

3×2
3×3
3×4

4×2
4×3
4×4
```

---

# 12. GRID-SIZE-001 — Closed Allowlist

Никакие другие размеры baseline не допускаются.

---

# 13. Explicitly unsupported

Например:

```text
1×1
2×5
3×5
4×5
5×5
6×6
```

недопустимы.

---

# 14. Why 1×1 excluded

Grid должен представлять множество эмоциональных состояний.

Одиночное изображение не является Emotional Grid.

---

# 15. Exact cell count

Formula:

```text
cell_count = rows × columns
```

---

# 16. Canonical counts

| Grid | Cells |
|---|---:|
| 1×2 | 2 |
| 1×3 | 3 |
| 1×4 | 4 |
| 2×2 | 4 |
| 2×3 | 6 |
| 2×4 | 8 |
| 3×2 | 6 |
| 3×3 | 9 |
| 3×4 | 12 |
| 4×2 | 8 |
| 4×3 | 12 |
| 4×4 | 16 |

---

# 17. GRID-SIZE-002 — Exact Cell Count Mandatory

Finalize запрещён, если количество заполненных ячеек не равно:

```text
rows × columns
```

---

# 18. No empty finalized cells

Finalized Grid не может содержать empty placeholders.

---

# 19. Grid orientation

`rows` и `columns` имеют точную семантику.

```text
2×4 != 4×2
```

---

# 20. GRID-SIZE-003 — Orientation Is Part of Revision Identity

---

# 21. Grid entity model

Canonical hierarchy:

```text
EmotionalGrid
  ↓
EmotionalGridDraft
  ↓
EmotionalGridCells
  ↓
EmotionalGridRevision
  ↓
EmotionalGridArtifacts
```

---

# 22. EmotionalGrid

Stable conceptual identity композиции.

Может иметь:

- current Draft;
- multiple immutable Revisions;
- primary marker within Session;
- lifecycle.

---

# 23. Grid Draft

Editable working configuration.

---

# 24. Grid Revision

Frozen, immutable, professionally approved representation.

---

# 25. Grid Artifact

Rendered binary derived from exact GridRevision.

---

# 26. Draft fields

Conceptually:

```text
grid_id
session_id
rows
columns
version
state
created_at
updated_at
```

---

# 27. Cell fields

Conceptually:

```text
id
grid_draft_id
row_index
column_index
media_asset_id
crop_x
crop_y
scale
rotation_deg
pan_x
pan_y
face_occupancy_estimate
confirmation_state
confirmed_by
confirmed_at
version
```

Exact DB design remains DOC-091.

---

# 28. Position addressing

Recommended:

```text
row_index: 0..rows-1
column_index: 0..columns-1
```

---

# 29. Unique position

Database/Application must ensure:

```text
UNIQUE(grid_draft_id, row_index, column_index)
```

or semantic equivalent.

---

# 30. GRID-CELL-001 — One Cell Per Position

---

# 31. Source reuse

Question:

может ли одно и то же source photo использоваться более одного раза в одном Grid?

---

# 32. Baseline rule

> **No.**

Один MediaAsset не должен повторяться в одном finalized Grid.

---

# 33. GRID-CELL-002 — Unique Source Per Finalized Grid

Причина:

повтор одного кадра искусственно создаёт впечатление большего эмоционального диапазона.

---

# 34. Draft duplicate

Draft MAY temporarily contain duplicate during editing only if UX implementation requires intermediate state.

Finalize must block.

---

# 35. Better UX

Prefer preventing duplicate assignment immediately.

---

# 36. Cell ordering

Reading order:

```text
top-left
→ left-to-right
→ row-by-row
```

---

# 37. Grid semantic order

Admin может выбирать порядок кадров.

System не должен автоматически сортировать эмоции по AI labels.

---

# 38. GRID-CELL-003 — Cell Order Is Human-Controlled

---

# 39. Source image full frame

Original remains unchanged and inspectable.

---

# 40. Cell transformation

Each cell may independently define:

```text
crop
pan
scale
rotation
```

---

# 41. Transform doctrine

Transformation относится **только к cell composition**.

---

# 42. GRID-XFM-001 — Transform Never Mutates MediaAsset

---

# 43. Crop

Crop defines visible source region used inside cell.

---

# 44. Pan

Allows repositioning source inside crop viewport.

---

# 45. Scale

Allows enlarging/reducing source within allowed composition limits.

---

# 46. Rotation

Allows limited rotation where required to visually align source.

---

# 47. Rotation constraints

Rotation should be bounded.

Baseline recommendation:

```text
-15° ... +15°
```

unless future UX demonstrates legitimate broader need.

---

# 48. Why bounded

Grid is professional photo presentation, not creative collage editor.

---

# 49. GRID-XFM-002 — No Arbitrary Artistic Transform

Not supported:

```text
perspective warp
face deformation
non-uniform stretch
mirror unless explicitly approved later
liquify
```

---

# 50. Scale constraints

Scale should prevent:

- zero/negative values;
- source leaving cell empty;
- extreme unusable enlargement.

Exact numeric range implementation-specific.

---

# 51. Transform coordinate contract

Transform parameters must use one normalized coordinate system.

---

# 52. Recommended

Use normalized values independent of output pixel size.

Example conceptually:

```text
cropX ∈ [0,1]
cropY ∈ [0,1]
panX / panY normalized
scale > 0
```

Exact transform contract implementation docs must freeze.

---

# 53. GRID-XFM-003 — No Pixel/Ratio Ambiguity

Never mix:

```text
cropX = 0.2
```

in one API and:

```text
cropX = 200 px
```

elsewhere without explicit type.

---

# 54. Transform reproducibility

Same:

```text
source MediaAsset
+
transform config
+
renderer version
```

must produce semantically identical composition.

---

# 55. Cell aspect ratio

Grid cell geometry derives from final Grid canvas layout.

Baseline cells should be equal-sized.

---

# 56. GRID-CELL-004 — Equal Cell Geometry

No arbitrary mosaic spanning cells baseline.

---

# 57. Unsupported baseline

```text
one cell spans 2 columns
one hero cell 2× larger
asymmetric masonry
```

---

# 58. Why

Casting document should remain systematic and quickly scannable.

---

# 59. Gaps

Grid may have small consistent inter-cell gap or no gap according to visual system.

---

# 60. Theme control

Theme can style:

```text
gap
outer margin
background
border radius within safe limits
```

---

# 61. Theme cannot change

```text
source transform
cell source
rows/columns
confirmed crop
face occupancy semantics
```

---

# 62. GRID-INV-004 — Theme Is Presentation Only

---

# 63. Face occupancy target

Canonical product requirement:

> **Face should occupy at least 90% of the useful cell area.**

---

# 64. GRID-FACE-001 — Target ≥90%

Conceptual:

```text
face_occupancy >= 0.90
```

for each finalized cell.

---

# 65. “Useful cell area”

Means visible content area allocated to the image after excluding structural padding/border.

---

# 66. Important interpretation

The 90% target is a **composition target for a close emotional face presentation**, not a judgment of appearance.

---

# 67. Face occupancy estimation

May be calculated by deterministic CV/AI-assisted face detector.

---

# 68. GRID-FACE-002 — Estimate Is Assistive

The numerical detector estimate is not professional truth.

---

# 69. Human confirmation remains mandatory

Even if:

```text
face_occupancy_estimate = 0.96
```

cell is not finalized until Human confirms.

---

# 70. Detection unavailable

If automated face detector unavailable:

manual composition is still possible.

---

# 71. GRID-FACE-003 — AI/CV Is Optional

---

# 72. Manual validation

Human may visually confirm compliance.

---

# 73. Detector below 90%

Recommended:

```text
block confirmation/finalization
```

unless explicit privileged override workflow is introduced.

---

# 74. Override policy

Baseline:

> no ordinary override below 90%.

If technical detector is clearly wrong, Admin should re-run/manual-review via explicit diagnostic process rather than casually override requirement.

---

# 75. Detector confidence

System MAY store:

```text
face_detection_confidence
```

technical metadata.

---

# 76. Multiple faces

Emotional Grid source should normally contain the actress as clear subject.

If detector finds multiple faces:

```text
requires human review
```

---

# 77. GRID-FACE-004 — Multiple Faces Cannot Be Resolved by Guess Alone

System should not arbitrarily choose another person's face.

---

# 78. Subject identification

Baseline does not require biometric identity recognition.

Prefer:

- source selection curated by Admin;
- Human visual confirmation.

---

# 79. No biometric profile

Do not create persistent facial biometric identity database solely for Grid.

---

# 80. GRID-FACE-005 — Face Detection ≠ Identity Recognition

---

# 81. Face target visual result

The intended outcome is a strongly face-focused crop.

---

# 82. Yet no appearance edits

To reach target, allowed:

```text
crop
pan
scale
limited rotation
```

Not allowed:

```text
face enlargement independent from whole image
generative face fill
skin retouch
eye manipulation
```

---

# 83. Cell confirmation state

Canonical:

```text
UNCONFIRMED
CONFIRMED
```

Optionally:

```text
NEEDS_REVIEW
```

for technical workflow.

---

# 84. GRID-CONF-001 — Every Cell Must Be Confirmed

Finalize requires:

```text
ALL cells confirmation_state = CONFIRMED
```

---

# 85. Confirmation identity

Store:

```text
confirmed_by
confirmed_at
```

for professional traceability.

---

# 86. GRID-CONF-002 — Confirmation Is Human Authority

`confirmed_by` must resolve authenticated Human Admin.

---

# 87. AI cannot be confirmer

Prohibited:

```text
confirmed_by = AI
```

---

# 88. System cannot silently confirm

Even if automated checks pass.

---

# 89. Transform after confirmation

Changing any meaningful cell transform must:

```text
invalidate confirmation
```

---

# 90. GRID-CONF-003 — Edit Invalidates Confirmation

Applies to:

```text
source photo
crop
pan
scale
rotation
position if semantic review changes
```

---

# 91. Why

Human approved exact visual composition, not abstract photo identity.

---

# 92. Face detector result changes

Re-running detector alone does not necessarily invalidate confirmation if transform/source unchanged.

But if new validation reports requirement violation, Draft readiness becomes blocked.

---

# 93. Swap cells

If same exact per-cell visual configs move positions:

confirmation SHOULD be invalidated for moved cells because sequence/composition changed.

---

# 94. Safer baseline

Any structural Grid edit invalidates affected/all confirmations.

---

# 95. Dimension change

Changing:

```text
2×4 → 4×4
```

creates major Draft restructuring.

---

# 96. GRID-SIZE-004 — Dimension Change Invalidates Finalization Readiness

Affected confirmations must be reset.

---

# 97. Dimension change implementation

May:

- preserve compatible assignments as unconfirmed;
- clear incompatible cells.

Must not pretend old approvals remain valid.

---

# 98. Grid Draft state

Recommended:

```text
EDITING
READY_FOR_REVIEW
READY_TO_FINALIZE
STALE
```

---

# 99. Ready for Review

All cells assigned, but some unconfirmed.

---

# 100. Ready to Finalize

All hard validations pass and every cell confirmed.

---

# 101. Stale

A dependency changed, for example:

```text
source media archived
source media replaced in Session
processor incompatibility
```

---

# 102. Finalization

Finalize is explicit high-impact command.

---

# 103. Canonical Finalize workflow

```text
FinalizeEmotionalGrid
       ↓
load Grid Draft
       ↓
expectedVersion
       ↓
validate Session
       ↓
validate allowed dimensions
       ↓
validate exact cell count
       ↓
validate unique positions
       ↓
validate unique sources
       ↓
validate same Session
       ↓
validate all Media
       ↓
validate transforms
       ↓
validate face target
       ↓
validate all confirmations
       ↓
BEGIN
  freeze GridRevision
  update current revision pointer
  Audit
  Outbox
COMMIT
       ↓
render artifacts async
```

---

# 104. GRID-INV-005 — Rendering Happens After Revision Commit

---

# 105. Why

Rendered output must always have exact immutable semantic source.

---

# 106. Idempotency

Finalize requires durable idempotency.

---

# 107. Same Draft + same key

Returns same GridRevision.

---

# 108. Double-click

Must not create Revision 5 and Revision 6 from same finalization intent.

---

# 109. Changed Draft

New semantic intent → new key.

---

# 110. Revision

`EmotionalGridRevision` is immutable.

---

# 111. Revision freezes

At minimum:

```text
grid identity
session identity
shooting date reference/snapshot where needed
rows
columns
cell ordering
source MediaAsset IDs
source checksums/version identity
all cell transforms
confirmation evidence
render schema version
created/finalized by
finalized at
```

---

# 112. GRID-REV-001 — Revision Never Reads Current Draft for Historical Meaning

---

# 113. Current Draft edits after finalization

Do not mutate existing Revision.

---

# 114. New change

```text
Revision N
→ editable Draft
→ Revision N+1
```

---

# 115. Revision numbering

Monotonically increasing within Grid.

---

# 116. Revision number vs entity version

Independent.

---

# 117. Example

```text
Grid.version = 23
GridRevision.revisionNumber = 4
```

valid.

---

# 118. Grid current revision pointer

May point to latest accepted/finalized revision.

---

# 119. Primary Grid

Session may designate a Grid as primary.

Prefer primary reference to:

```text
Grid + current approved Revision
```

or exact revision semantics defined by module state.

---

# 120. Important

Questionnaire must bind exact GridRevision, not merely mutable Grid ID.

---

# 121. GRID-QNR-001 — Questionnaire References Exact Revision

---

# 122. Artifacts

Required derivative outputs:

```text
MASTER
WEB
PDF
THUMBNAIL
```

---

# 123. GRID-ART-001 — Four Canonical Artifact Classes

---

# 124. MASTER

Highest-quality canonical composite derivative for a GridRevision.

---

# 125. MASTER purpose

- archival reusable rendered representation;
- basis for other deterministic output where appropriate;
- professional high-quality export.

---

# 126. MASTER is not Source

Source remains original MediaAssets + GridRevision config.

---

# 127. WEB

Optimized for site/browser.

---

# 128. PDF

Optimized for document rendering/print.

---

# 129. THUMBNAIL

Admin/Public compact preview.

---

# 130. Artifact identity

Conceptually:

```text
GridRevision
+
artifact type
+
render version
+
output specification
```

---

# 131. GRID-ART-002 — Artifacts Are Immutable By Identity

---

# 132. New renderer version

Can generate new artifact identity without changing GridRevision semantics.

---

# 133. Artifact processing states

```text
QUEUED
RENDERING
VALIDATING
READY
FAILED
```

---

# 134. Validation before READY

At minimum:

- expected dimensions;
- all cells present;
- no blank output;
- decodable output;
- correct source revision;
- expected checksum/metadata;
- visual transform application success.

---

# 135. Optional visual regression validation

System MAY compare generated cell geometry against expected transform spec.

---

# 136. GRID-ART-003 — Failed Artifact Does Not Invalidate Source Revision

Revision can be valid while rendering failed.

---

# 137. Readiness distinction

```text
Revision finalized
≠
Artifact READY
```

---

# 138. Public readiness

Requires correct WEB artifact READY.

---

# 139. Questionnaire readiness

Requires correct PDF/appropriate composite artifact READY or renderer able to deterministically produce from exact Revision.

Preferred: explicit PDF artifact READY before final document READY.

---

# 140. Thumbnail failure

Should not invalidate professional Revision, but Admin preview may degrade.

---

# 141. Artifact regeneration

Always uses exact Revision.

Never current Draft.

---

# 142. GRID-ART-004 — Historical Artifact Regeneration Is Revision-Bound

---

# 143. Rendering layout

Equal cell rectangles.

---

# 144. Output consistency

MASTER/WEB/PDF/THUMBNAIL should preserve:

- same source photo per cell;
- same cell order;
- same crop/pan/scale/rotation;
- same semantic composition.

---

# 145. GRID-ART-005 — Different Formats, Same Composition

---

# 146. Minor format differences allowed

Examples:

```text
compression
pixel dimensions
file format
DPI metadata
```

---

# 147. Not allowed

PDF variant cannot choose different crop to “fit better”.

---

# 148. Color management

Processor may normalize safe output color profile.

Must not intentionally alter appearance.

---

# 149. Sharpening/compression

Only technical bounded processing.

No beauty retouch.

---

# 150. Public Emotional Portfolio

May display Primary Grid plus full source gallery.

---

# 151. Homepage

Can display WEB/THUMBNAIL variant of Primary Grid.

---

# 152. Questionnaire

Uses exact GridRevision composite.

---

# 153. Public Builder

Chooses approved eligible GridRevision.

---

# 154. Public Builder cannot

```text
change layout
change cells
reorder
crop
zoom
rotate
replace images
```

---

# 155. GRID-BLD-001 — Builder Selects, Never Edits

---

# 156. Builder selection model

Conceptually:

```text
session
gridRevision
include = true/false
```

---

# 157. If multiple eligible Grids

Builder may select among them according to template.

---

# 158. Builder preview

Must use exact approved Grid representation.

---

# 159. Builder Generate

Revalidates:

```text
GridRevision exists
belongs target profile
eligible for Builder
source Session eligible
artifact safe/ready
not revoked
```

---

# 160. Questionnaire use

Emotional block contains:

```text
Grid composite
shooting date
full Emotional Portfolio URL
optional QR
```

---

# 161. GRID-QNR-002 — No Cell-Level Data in Questionnaire

PDF/HTML consumers receive composite, not separate photos for layout recreation.

---

# 162. Why

Guarantees:

```text
website Grid == questionnaire Grid
```

semantically.

---

# 163. Full portfolio hyperlink

Owned/derived from Emotional Session public/token-safe route.

---

# 164. QR

QR may encode full Emotional Portfolio URL.

Not Grid storage URL unless specific separate business requirement arises.

---

# 165. Visibility

Grid eligibility must consider:

```text
Session policy
Grid policy/current state
Revision state
artifact access
surface policy
```

---

# 166. Grid-level visibility

Recommended if multiple Grids have different usages:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

or equivalent Grid configuration.

---

# 167. GRID-INV-006 — Session Permission Alone Is Not Always Enough

Example:

Session allows Builder, but only one approved Grid is permitted.

---

# 168. Intersection rule

Conceptually:

```text
GRID_SURFACE_ELIGIBLE =
  session_surface_allowed
  AND grid_surface_allowed
  AND revision finalized
  AND artifact ready/safe
```

---

# 169. Public Primary Grid

May display only if Public eligible.

---

# 170. Primary Grid hidden publicly

Homepage must not leak it.

Can use another configured public Grid or hide emotional Grid preview.

---

# 171. Grid Draft never Public

Normative.

---

# 172. GRID-INV-007 — Draft Is Admin-Only

---

# 173. Revision Admin view

Admin can inspect:

- source photo IDs;
- transforms;
- confirmations;
- artifacts;
- provenance;
- usage.

---

# 174. Public view

Gets only safe rendered composite + approved metadata.

---

# 175. No crop metadata Public

Public DTO should not expose:

```text
cropX
cropY
face occupancy estimate
confirmedBy
processor internals
```

---

# 176. GRID-INV-008 — Internal Composition Metadata Is Admin/Renderer Data

---

# 177. AI/CV assistance

AI/CV MAY assist with:

1. face detection;
2. crop proposal;
3. face occupancy estimate;
4. duplicate detection;
5. source shortlist;
6. rough auto-fill Draft.

---

# 178. Auto-fill Draft

Permitted only as **proposal**.

---

# 179. GRID-AI-001 — AI May Populate Draft Proposal, Not Final State

---

# 180. Proposed cell

Must be visually marked:

```text
Suggested / Unconfirmed
```

---

# 181. Human review

Each cell still requires exact confirmation.

---

# 182. GRID-AI-002 — No Bulk “AI Approved” Finalize

---

# 183. AI source shortlist

Can rank technical suitability for face occupancy.

It cannot rank attractiveness.

---

# 184. Allowed ranking dimensions

```text
face detected
usable resolution
estimated face size
blur/technical quality
duplicate likelihood
```

---

# 185. Prohibited ranking

```text
beauty
age desirability
body desirability
“best looking”
```

---

# 186. Crop suggestion

AI suggests transform only.

---

# 187. Human may adjust

```text
zoom
pan
rotation
source selection
position
```

---

# 188. After adjustment

AI suggestion loses authority; final config is Human-approved exact transform.

---

# 189. AI outage

Manual Grid editor remains fully operational.

---

# 190. GRID-AI-003 — Core Workflow Must Work Without AI

---

# 191. VOP

May detect:

```text
Grid Draft incomplete
cell unconfirmed
face occupancy below target
source media failed
artifact failed
Questionnaire using stale/non-primary Grid
```

---

# 192. VOP safe auto-actions

May:

```text
retry artifact render
rebuild thumbnail
re-run deterministic face estimate
invalidate cache
```

---

# 193. VOP cannot

```text
confirm cell
finalize Grid
replace source
change crop
change primary
publish Grid
```

without Human approval.

---

# 194. Grid editor UX

Recommended structure:

```text
Header
├── Session
├── Shooting Date
├── Grid Size
├── Revision status
└── Readiness

Canvas
├── Grid cells
├── Drag source photo
├── Reorder
├── Crop/Pan/Scale/Rotate
└── Confirmation state

Side Panel
├── Session photos
├── Face estimate
├── Technical warnings
├── Usage
└── Preview
```

---

# 195. Grid size selector

Only twelve allowed sizes.

No free numeric fields.

---

# 196. Why

Prevents invalid combinations and simplifies UX.

---

# 197. Source tray

Shows only photos from selected Emotional Session.

---

# 198. GRID-UX-001 — No Global Media Picker by Default

Grid editor should not expose all Media Library as direct sources.

---

# 199. Why

Prevents accidental cross-session contamination.

---

# 200. Photo assignment

Drag/drop or click assignment.

Must have keyboard-accessible alternative.

---

# 201. Cell editor

Should offer:

```text
zoom
pan
rotate
reset
confirm
```

---

# 202. Reset

Returns to deterministic default fitting proposal.

---

# 203. Default transform

May center detected face if CV available.

Otherwise deterministic center crop proposal.

Still unconfirmed.

---

# 204. GRID-CONF-004 — Default Placement Is Not Confirmation

---

# 205. Visual safe guides

Editor should show:

- cell bounds;
- source crop;
- face occupancy indicator;
- target ≥90%;
- clipping warnings.

---

# 206. Face occupancy indicator

Example:

```text
92% ✓
84% — needs adjustment
```

Technical UI only.

---

# 207. No body criticism

UI wording must remain composition-oriented.

---

# 208. Confirmation button

Enabled only when hard validation passes.

---

# 209. Confirmed state

Clearly visible on each cell.

---

# 210. Global readiness

Example:

```text
16 / 16 cells assigned
16 / 16 confirmed
16 / 16 pass composition validation
Ready to finalize
```

---

# 211. Finalize button

Disabled until all hard requirements pass.

---

# 212. No “finalize anyway” ordinary flow

---

# 213. Preview modes

Admin SHOULD support:

```text
Master-like
Web
PDF
Thumbnail
```

or representative responsive previews.

---

# 214. Preview ≠ Artifact authority

Preview can be client-rendered approximation.

Final artifact always server-rendered from frozen Revision.

---

# 215. GRID-INV-009 — Client Preview Cannot Become Master Artifact

---

# 216. Server-side render

Required for canonical artifacts.

---

# 217. Why

Ensures:

- determinism;
- PDF/web consistency;
- no browser-specific raster differences as Source;
- background regeneration.

---

# 218. Renderer inputs

Only:

```text
GridRevision
approved source MediaAssets
renderer config/version
```

---

# 219. No live AI during render

Normative.

---

# 220. GRID-ART-006 — Rendering Must Be Deterministic

---

# 221. Rendering failure

Revision remains.

Artifact can retry.

---

# 222. Retry

Uses same artifact semantic identity where renderer version/output spec unchanged.

---

# 223. Cache

Rendered WEB/THUMBNAIL immutable artifacts can be cached.

---

# 224. Current Primary alias

May resolve to current public-safe primary revision.

---

# 225. Visibility revocation

Must prevent delivery of revoked current alias/resource even if artifact bytes remain cached.

---

# 226. Search

Grid itself typically appears as part of Emotional Portfolio projection, not independent global search entity.

---

# 227. Search metadata

May index:

```text
Emotional Portfolio
shooting date
session title
```

not crop settings.

---

# 228. SEO

Public Primary Grid can be image representation.

Internal transform metadata not exposed.

---

# 229. Accessibility

Composite image should have meaningful alt.

Example:

```text
Эмоциональная фотосерия Екатерины Крыгиной, съёмка 27 июля 2026 года
```

---

# 230. Individual emotion inference

Do not auto-generate alt describing each emotional state unless labels were Human-approved.

---

# 231. Questionnaire accessibility

PDF artifact should include surrounding semantic text:

```text
Эмоциональный диапазон
Дата съёмки
Ссылка на полное портфолио
```

---

# 232. Mobile Grid

Public composite remains single image/object.

May allow tap to full emotional page.

---

# 233. Grid proportions

Output aspect ratio derives from:

```text
columns / rows
```

assuming equal square cells.

---

# 234. Example

```text
4×4 → square
2×4 → wide
4×2 → tall
```

if notation is rows×columns.

---

# 235. GRID-SIZE-005 — Dimension Semantics Must Be Consistent Everywhere

Admin UI, renderer, PDF, filenames and API all use:

```text
rows × columns
```

---

# 236. Filenames

Artifact filename MAY include:

```text
actor-emotional-2026-07-27-4x4-web
```

sanitized.

---

# 237. Revision filename

If relevant:

```text
...-r3-...
```

---

# 238. MASTER resolution

Exact pixels operational/render specification.

Should be sufficient to derive professional outputs without source loss.

---

# 239. WEB

Responsive use.

---

# 240. PDF

High enough resolution for document rendering/print while respecting file-size target.

---

# 241. THUMBNAIL

Admin/dashboard compact.

---

# 242. GRID-ART-007 — Artifact Type Does Not Change Grid Layout

---

# 243. Color/background

Grid background should be neutral/theme-aware.

No decorative overlay over faces unless explicitly safe and unobtrusive.

---

# 244. No text over source cells baseline

Keep emotional faces unobstructed.

---

# 245. GRID-INV-010 — Professional Composite Prioritizes Faces Over Decoration

---

# 246. Watermark

Not baseline.

If introduced later, cannot obscure facial content.

---

# 247. Session shooting date

Grid itself inherits context from Session.

---

# 248. Should GridRevision snapshot date?

Recommended yes in professional revision/document snapshot metadata, so historical provenance remains exact even if current Session date is later corrected.

---

# 249. Important distinction

Current Emotional Portfolio can show corrected Session date.

Historical GridRevision retains its finalized provenance.

---

# 250. GRID-REV-002 — Finalization Freezes Relevant Session Provenance

---

# 251. Session media removal after finalized Grid

Current source relationship may change, but Revision remains exact.

---

# 252. Hard media deletion

Blocked by GridRevision dependency unless privacy/legal redaction workflow resolves it.

---

# 253. GRID-REV-003 — Finalized Revision Is Historical Dependency

---

# 254. Archive Grid

Grid may be archived from current use.

---

# 255. Archive effect

Remove from:

```text
new Questionnaire selections
Builder eligibility
current public primary candidates
```

Historical usage remains.

---

# 256. Archive Revision?

Individual immutable Revision is generally superseded, not archived/deleted simply because newer Revision exists.

---

# 257. Current Grid archive

May archive overall Grid/configuration.

---

# 258. Restore

Preferred to Draft/current review state.

No auto-publication.

---

# 259. Delete Grid Draft

Abandoned Draft can be removed after retention if no business dependency.

---

# 260. Delete GridRevision

Exceptional.

Long-lived professional history.

---

# 261. Privacy deletion

If source image must be removed:

inspect:

```text
GridRevision
MASTER
WEB
PDF
THUMBNAIL
Questionnaire Revision
Builder Snapshot
Search/cache
Social copies
AI snapshots
```

---

# 262. GRID-INV-011 — Deleting One Source Requires Composite Copy Discovery

---

# 263. Historical artifact redaction

May block/remove delivery according to retention/access rules.

Do not silently generate replacement Grid with another source.

---

# 264. Grid revision usage graph

Admin should be able to see:

```text
Public Emotional Portfolio
Homepage
Questionnaire Revision
Builder Snapshot
Social Draft/Post
```

where applicable.

---

# 265. Usage before archive/delete

Show impact to Admin.

---

# 266. Commands

Canonical:

```text
CreateEmotionalGrid
CreateEmotionalGridDraft
ChangeEmotionalGridDimensions
AssignEmotionalGridCell
RemoveEmotionalGridCell
MoveEmotionalGridCell
UpdateEmotionalGridCellTransform
ConfirmEmotionalGridCell
UnconfirmEmotionalGridCell
AutoSuggestEmotionalGrid
FinalizeEmotionalGrid
SetPrimaryEmotionalGrid
ArchiveEmotionalGrid
RestoreEmotionalGrid
RetryEmotionalGridArtifact
```

---

# 267. Queries

```text
GetAdminEmotionalGrid
GetEmotionalGridDraft
GetEmotionalGridReadiness
GetEmotionalGridRevision
ListEmotionalGridRevisions
GetPublicEmotionalGrid
ListQuestionnaireEligibleEmotionalGrids
ListBuilderEligibleEmotionalGrids
GetEmotionalGridUsage
```

---

# 268. Create input

```text
sessionId
rows
columns
```

---

# 269. Server validates size allowlist

Client cannot create arbitrary rows/columns.

---

# 270. Assign cell input

Conceptually:

```text
gridDraftId
rowIndex
columnIndex
mediaAssetId
expectedVersion
```

---

# 271. Server validates

```text
Grid/session/profile ownership
Media belongs Session
IMAGE
safe
position valid
duplicate source policy
```

---

# 272. Transform input

```text
cellId
expectedVersion
crop/pan/scale/rotation
```

---

# 273. Transform server validation

Never trust client geometry blindly.

---

# 274. Confirm input

```text
cellId
expectedVersion
```

No client:

```text
confirmedBy
confirmedAt
```

---

# 275. Server confirmation checks

```text
source valid
transform valid
face target passes
cell belongs editable Draft
```

---

# 276. Finalize input

```text
gridId
draftId
expectedDraftVersion
idempotencyKey
```

---

# 277. Client does not submit revision snapshot

Server constructs it.

---

# 278. GRID-INV-012 — Browser Cannot Author Immutable Revision Payload

---

# 279. Set Primary input

Exact finalized Grid/Revision identity according to application model.

---

# 280. Primary selection

Human action.

---

# 281. Error taxonomy

At minimum:

```text
GRID_NOT_FOUND
GRID_DRAFT_NOT_FOUND
GRID_DIMENSIONS_UNSUPPORTED
GRID_CELL_COUNT_MISMATCH
GRID_POSITION_INVALID
GRID_POSITION_DUPLICATE
GRID_CELL_EMPTY
GRID_MEDIA_INVALID
GRID_MEDIA_WRONG_SESSION
GRID_MEDIA_DUPLICATE
GRID_TRANSFORM_INVALID
GRID_FACE_NOT_DETECTED
GRID_MULTIPLE_FACES_REVIEW_REQUIRED
GRID_FACE_TARGET_NOT_MET
GRID_CELL_UNCONFIRMED
GRID_DRAFT_STALE
GRID_FINALIZE_CONFLICT
GRID_REVISION_NOT_FOUND
GRID_ARTIFACT_NOT_READY
GRID_ARTIFACT_FAILED
GRID_PRIMARY_CONFLICT
```

---

# 282. Face not detected

May mean:

- detector unavailable/inconclusive;
- source unsuitable;
- manual review needed.

Do not interpret as statement about the person.

---

# 283. Grid face target wording

Safe UI:

```text
Лицо занимает недостаточную часть ячейки. Увеличьте масштаб или измените кадрирование.
```

---

# 284. Avoid

```text
Лицо слишком маленькое/неподходящее
```

in judgmental sense.

---

# 285. Readiness blockers

At least:

```text
GRID_DIMENSIONS_INVALID
GRID_EMPTY_CELLS
GRID_DUPLICATE_SOURCE
GRID_SOURCE_NOT_READY
GRID_FACE_TARGET_NOT_MET
GRID_UNCONFIRMED_CELLS
GRID_ARTIFACT_REQUIRED
```

---

# 286. Readiness warnings

Possible:

```text
GRID_LOW_SOURCE_RESOLUTION
GRID_MULTIPLE_FACE_SOURCE
GRID_SOURCE_NEAR_DUPLICATE
```

---

# 287. Low source resolution

Could become blocker if final MASTER/PDF required resolution cannot be met.

---

# 288. Quality calculation

Output cell pixel dimensions can determine whether source crop has sufficient effective resolution.

---

# 289. GRID-ART-008 — Upscaling Cannot Invent Professional Detail

Technical interpolation may be limited, but system should warn/block instead of relying on generative enhancement.

---

# 290. No generative super-resolution baseline

If future added, appearance-preservation and explicit policy required.

---

# 291. Audit

Must audit:

```text
Finalize GridRevision
Set Primary Grid
Archive/Restore Grid
manual exceptional security/privacy actions
```

---

# 292. Cell confirmation audit

Can be represented inside Revision confirmation metadata plus operational audit according to implementation.

---

# 293. Avoid one giant Audit event per mouse pan

Draft transform changes need not produce business Audit spam.

---

# 294. Finalized exact state matters

Revision snapshot provides professional history.

---

# 295. Outbox events

Suggested:

```text
EmotionalGridDraftUpdated
EmotionalGridFinalized
EmotionalGridPrimaryChanged
EmotionalGridArchived
EmotionalGridArtifactRequested
```

---

# 296. `DraftUpdated` event

Should be used carefully; may coalesce to avoid job/event noise.

---

# 297. Consumers

```text
artifact renderer
public cache
Questionnaire readiness
Builder eligibility
Homepage projection
VOP
```

---

# 298. Render jobs

Canonical:

```text
EMOTIONAL_GRID_RENDER_MASTER
EMOTIONAL_GRID_RENDER_WEB
EMOTIONAL_GRID_RENDER_PDF
EMOTIONAL_GRID_RENDER_THUMBNAIL
```

Could be one multi-output job or separate jobs.

---

# 299. Preferred idempotency identity

```text
GridRevision ID
+
artifact type
+
renderer version
```

---

# 300. Worker security

Job payload uses IDs/references, not source binary/base64.

---

# 301. Worker loads exact Revision

Not current Grid Draft.

---

# 302. GRID-INV-013 — Worker Cannot “Improve” Composition

Renderer executes specification exactly.

---

# 303. Cache

Artifact URLs may be revision/version-addressed.

---

# 304. Public alias

Current Primary Grid route may redirect/reference exact current approved revision.

---

# 305. Current change

New Primary/current revision invalidates alias.

Old immutable revision asset can remain stored but access governed.

---

# 306. Social Publishing

Grid Artifact may be used as source for approved Social Post.

---

# 307. Social crop

Platform-specific derivative may transform outer composite canvas.

Must not independently recrop individual cells contrary to GridRevision.

---

# 308. GRID-INV-014 — Social Does Not Recompose Cells

---

# 309. BB Assistant

No core role in Grid creation.

May draft accompanying social/session text separately.

---

# 310. Theme AI

Cannot modify GridRevision transforms.

May style surrounding page only.

---

# 311. Analytics

Relevant events:

```text
emotional_grid_viewed
emotional_grid_full_portfolio_clicked
```

---

# 312. Admin analytics

Could track which Grid sizes are commonly used.

---

# 313. Analytics cannot auto-change Primary

---

# 314. Migration

Legacy composite/grid data may exist.

---

# 315. Migration case A — source photos + crop metadata known

Can migrate into:

```text
Grid
Draft/Revision
cells
transforms
```

after validation/Human review.

---

# 316. Migration case B — composite image only

Cannot reconstruct cells reliably.

---

# 317. Baseline handling

Store as:

```text
legacy derived artifact
```

and require creating new native Grid from source photos for full functionality.

---

# 318. GRID-INV-015 — Raster Composite Alone Is Not Native Editable Grid

---

# 319. Legacy dimensions unknown

Do not infer arbitrary cell source mapping.

Visual detection may suggest but requires Human review.

---

# 320. Legacy crop quality

Do not mark Human-confirmed automatically merely because old composite existed.

---

# 321. Native Grid migration readiness

Must satisfy current exact rules before becoming finalized current Revision.

---

# 322. Anti-patterns

`GRID-AP-001`  
Allow arbitrary dimensions.

`GRID-AP-002`  
Allow empty finalized cells.

`GRID-AP-003`  
Allow duplicate photo multiple times in finalized Grid.

`GRID-AP-004`  
Mix photographs from different Emotional Sessions.

`GRID-AP-005`  
Use Main Portfolio photo directly without Session membership.

`GRID-AP-006`  
Use Role photo directly.

`GRID-AP-007`  
Modify source MediaAsset to create crop.

`GRID-AP-008`  
Store one global crop on MediaAsset.

`GRID-AP-009`  
Use destructive rotation.

`GRID-AP-010`  
Allow arbitrary perspective/warp.

`GRID-AP-011`  
Use generative face enlargement.

`GRID-AP-012`  
Retouch face to satisfy Grid.

`GRID-AP-013`  
AI auto-confirms cells.

`GRID-AP-014`  
System auto-confirms because detector reports 95%.

`GRID-AP-015`  
Finalize with one unconfirmed cell.

`GRID-AP-016`  
Change transform but retain confirmation.

`GRID-AP-017`  
Change source but retain confirmation.

`GRID-AP-018`  
Change Grid dimensions but preserve all old confirmations.

`GRID-AP-019`  
Treat AI-detected emotion as cell professional fact.

`GRID-AP-020`  
Rank source photos by attractiveness.

`GRID-AP-021`  
Use biometric identity database solely to choose actress face.

`GRID-AP-022`  
Questionnaire rebuilds individual Grid cells independently.

`GRID-AP-023`  
PDF chooses different crop than WEB.

`GRID-AP-024`  
Thumbnail changes cell order.

`GRID-AP-025`  
Theme changes cell crop.

`GRID-AP-026`  
Theme hides or overlays faces with decorative content.

`GRID-AP-027`  
Public Builder edits cells.

`GRID-AP-028`  
Public Builder creates arbitrary Grid size.

`GRID-AP-029`  
Browser submits entire frozen Revision as authoritative.

`GRID-AP-030`  
Render Grid before immutable Revision is committed.

`GRID-AP-031`  
Render job reads current Draft.

`GRID-AP-032`  
New render overwrites artifact bytes under immutable old artifact ID.

`GRID-AP-033`  
Artifact failure deletes GridRevision.

`GRID-AP-034`  
New GridRevision mutates old Questionnaire.

`GRID-AP-035`  
Deleting source automatically substitutes nearest similar photo.

`GRID-AP-036`  
Archive Grid deletes source MediaAssets.

`GRID-AP-037`  
Restore Grid immediately republishes.

`GRID-AP-038`  
Public DTO includes crop coordinates and Admin confirmation metadata.

`GRID-AP-039`  
Grid requires live AI provider to render.

`GRID-AP-040`  
AI outage blocks manual Grid workflow.

`GRID-AP-041`  
Face target failure described as appearance deficiency.

`GRID-AP-042`  
Scale/crop unit differs between web and worker renderer.

`GRID-AP-043`  
Rows×columns interpreted differently in Admin and renderer.

`GRID-AP-044`  
Legacy raster collage imported as fully editable native Grid.

`GRID-AP-045`  
Auto-finalize after batch AI proposal.

`GRID-AP-046`  
One global `is_public` controls Site/QNR/Builder eligibility.

`GRID-AP-047`  
Session public permission automatically exposes every Grid.

`GRID-AP-048`  
Current Primary alias bypasses current access policy.

`GRID-AP-049`  
Social platform independently recrops individual cells.

`GRID-AP-050`  
Generative upscaling used to compensate insufficient source quality without policy.

---

# 323. Core invariants

`GRID-INV-016`  
Every Grid belongs to one Emotional Session.

`GRID-INV-017`  
Every source cell photo belongs to that Session.

`GRID-INV-018`  
Every source is a valid IMAGE MediaAsset.

`GRID-INV-019`  
Grid dimensions come only from twelve allowed sizes.

`GRID-INV-020`  
Rows×columns semantics are consistent system-wide.

`GRID-INV-021`  
Finalized cell count is exact.

`GRID-INV-022`  
Finalized Grid has no empty cells.

`GRID-INV-023`  
Each cell position is unique.

`GRID-INV-024`  
Finalized Grid uses unique source photo per cell.

`GRID-INV-025`  
Cell order is explicit and Human-controlled.

`GRID-INV-026`  
Cell transforms are contextual.

`GRID-INV-027`  
Transforms never mutate originals.

`GRID-INV-028`  
Transforms use one canonical coordinate contract.

`GRID-INV-029`  
Cells have equal baseline geometry.

`GRID-INV-030`  
Face occupancy target is ≥90%.

`GRID-INV-031`  
Face occupancy is composition validation, not appearance judgment.

`GRID-INV-032`  
Face detection is optional assistance.

`GRID-INV-033`  
Face detection does not perform identity recognition baseline.

`GRID-INV-034`  
Human confirmation is mandatory for every cell.

`GRID-INV-035`  
AI cannot confirm a cell.

`GRID-INV-036`  
System cannot silently confirm a cell.

`GRID-INV-037`  
Changing source/transform invalidates confirmation.

`GRID-INV-038`  
Structural Grid changes invalidate affected confirmation.

`GRID-INV-039`  
Finalize is explicit Human command.

`GRID-INV-040`  
Finalize is idempotent.

`GRID-INV-041`  
Finalize constructs Revision server-side.

`GRID-INV-042`  
GridRevision is immutable.

`GRID-INV-043`  
GridRevision freezes exact source identities.

`GRID-INV-044`  
GridRevision freezes exact transforms.

`GRID-INV-045`  
GridRevision freezes exact cell order/layout.

`GRID-INV-046`  
GridRevision preserves confirmation provenance.

`GRID-INV-047`  
GridRevision preserves relevant Session provenance.

`GRID-INV-048`  
Artifact rendering begins after Revision commit.

`GRID-INV-049`  
MASTER is derived, not Source.

`GRID-INV-050`  
WEB is derived from exact Revision.

`GRID-INV-051`  
PDF is derived from exact Revision.

`GRID-INV-052`  
THUMBNAIL is derived from exact Revision.

`GRID-INV-053`  
All artifact classes preserve identical semantic composition.

`GRID-INV-054`  
Renderer version participates in artifact identity.

`GRID-INV-055`  
Artifact failure does not mutate Revision.

`GRID-INV-056`  
Artifact regeneration reads Revision, not current Draft.

`GRID-INV-057`  
Questionnaire references exact GridRevision.

`GRID-INV-058`  
Questionnaire receives composite rather than cell source list.

`GRID-INV-059`  
Builder selects approved Grid, not cells.

`GRID-INV-060`  
Builder cannot change transformations.

`GRID-INV-061`  
Builder Generate revalidates access/eligibility.

`GRID-INV-062`  
Draft is never Public.

`GRID-INV-063`  
Internal crop/face metadata is not Public.

`GRID-INV-064`  
Session and Grid visibility may both gate exposure.

`GRID-INV-065`  
Primary Grid is Human-selected.

`GRID-INV-066`  
AI may propose, never finalize.

`GRID-INV-067`  
VOP may retry technical processing, never approve visual content.

`GRID-INV-068`  
Core Grid editing works without AI.

`GRID-INV-069`  
Theme cannot change composition.

`GRID-INV-070`  
Social cannot independently recompose cells.

`GRID-INV-071`  
Archive removes current use but preserves Revision history.

`GRID-INV-072`  
Archive does not delete source MediaAssets.

`GRID-INV-073`  
Restore does not auto-publish.

`GRID-INV-074`  
Historical source cannot be silently substituted.

`GRID-INV-075`  
Privacy removal discovers all composite artifacts/snapshots.

`GRID-INV-076`  
Source resolution inadequacy is solved by review/replacement, not appearance-generative repair.

`GRID-INV-077`  
Legacy composite alone does not establish editable native cell provenance.

`GRID-INV-078`  
Cache never becomes Grid authority.

`GRID-INV-079`  
Search never becomes Grid authority.

`GRID-INV-080`  
Public alias does not bypass current access policy.

`GRID-INV-081`  
Same-profile ownership is enforced server-side.

`GRID-INV-082`  
Concurrent Draft edits use version checks.

`GRID-INV-083`  
Concurrent Finalize cannot create duplicate semantic revisions.

`GRID-INV-084`  
No client-supplied confirmation identity is authoritative.

`GRID-INV-085`  
No client-supplied face score is authoritative.

`GRID-INV-086`  
No client-supplied artifact READY state is authoritative.

`GRID-INV-087`  
No AI-generated source photo is introduced baseline.

`GRID-INV-088`  
No appearance manipulation is permitted.

`GRID-INV-089`  
Professional output prioritizes legibility of expressions over decorative layout.

`GRID-INV-090`  
Every Grid remains traceable to exact Emotional Session and source photographs.

---

# 324. E2E-GRID-001 — Allowed 1×2

Create `1×2`.

Expected 2 cells.

---

# 325. E2E-GRID-002 — Allowed 4×4

Create `4×4`.

Expected 16 cells.

---

# 326. E2E-GRID-003 — Unsupported 5×5

Rejected:

```text
GRID_DIMENSIONS_UNSUPPORTED
```

---

# 327. E2E-GRID-004 — Unsupported 1×1

Rejected.

---

# 328. E2E-GRID-005 — Orientation

`2×4` generates 2 rows/4 columns.

`4×2` remains different layout.

---

# 329. E2E-GRID-006 — Incomplete cells

4×4 Draft with 15 assigned cells.

Finalize:

```text
GRID_CELL_COUNT_MISMATCH
```

---

# 330. E2E-GRID-007 — Empty position

16 cell records but one with no source.

Finalize blocked.

---

# 331. E2E-GRID-008 — Duplicate position

Two cells claim `(1,2)`.

Rejected.

---

# 332. E2E-GRID-009 — Duplicate photo

Same MediaAsset assigned twice in finalized Grid.

Rejected.

---

# 333. E2E-GRID-010 — Wrong Session

Cell source comes from another Emotional Session.

Rejected:

```text
GRID_MEDIA_WRONG_SESSION
```

---

# 334. E2E-GRID-011 — Main Portfolio direct source

Image exists in Portfolio but not target Emotional Session.

Rejected.

---

# 335. E2E-GRID-012 — Source original immutable

Pan/zoom/crop cell.

MediaAsset checksum unchanged.

---

# 336. E2E-GRID-013 — Full Emotional gallery unaffected

Cell crop heavily zoomed.

Full Emotional Portfolio continues to display source framing.

---

# 337. E2E-GRID-014 — Transform invalid

Negative scale / out-of-contract geometry.

Rejected.

---

# 338. E2E-GRID-015 — Rotation bound

Out-of-range rotation rejected.

---

# 339. E2E-GRID-016 — Face occupancy 92%

Cell passes technical threshold.

Still remains `UNCONFIRMED` until Human confirms.

---

# 340. E2E-GRID-017 — Face occupancy 84%

Confirmation/finalization blocked.

---

# 341. E2E-GRID-018 — AI unavailable

Human manually crops to acceptable composition and confirms.

---

# 342. E2E-GRID-019 — Client spoofs face score

Browser sends `faceOccupancy=1.0`.

Server ignores/recalculates/uses authoritative validation.

---

# 343. E2E-GRID-020 — Multiple faces

Detector finds multiple faces.

Cell requires Human review and cannot arbitrarily select one automatically.

---

# 344. E2E-GRID-021 — Confirm cell

Valid Human Admin confirmation persists identity/time.

---

# 345. E2E-GRID-022 — AI confirm attempt

No AI/system principal can use Human Confirm command.

---

# 346. E2E-GRID-023 — Edit confirmed crop

Change pan after confirmation.

Cell returns `UNCONFIRMED`.

---

# 347. E2E-GRID-024 — Replace confirmed source

Confirmation invalidated.

---

# 348. E2E-GRID-025 — Move confirmed cell

Affected confirmation invalidated under baseline safe policy.

---

# 349. E2E-GRID-026 — Dimension change

2×4 → 4×4.

Old readiness/confirmations do not incorrectly persist.

---

# 350. E2E-GRID-027 — All cells confirmed

All hard checks pass.

Draft reaches `READY_TO_FINALIZE`.

---

# 351. E2E-GRID-028 — One unconfirmed

Finalize returns:

```text
GRID_CELL_UNCONFIRMED
```

---

# 352. E2E-GRID-029 — Double Finalize

Two concurrent same-key requests.

One GridRevision.

---

# 353. E2E-GRID-030 — Finalize response lost

Revision commits, response lost.

Retry returns same Revision.

---

# 354. E2E-GRID-031 — Edit after Finalize

Revision remains unchanged.

New edits occur in Draft/new revision flow.

---

# 355. E2E-GRID-032 — Revision exact source

Revision source IDs/checksums match approved cells.

---

# 356. E2E-GRID-033 — MASTER render

Worker generates MASTER from exact Revision.

---

# 357. E2E-GRID-034 — WEB render

WEB preserves same cell composition as MASTER.

---

# 358. E2E-GRID-035 — PDF render

PDF preserves same exact crop/order.

---

# 359. E2E-GRID-036 — THUMBNAIL

Thumbnail differs only in output resolution/technical format.

---

# 360. E2E-GRID-037 — Artifact failure

WEB fails rendering.

GridRevision remains valid.

---

# 361. E2E-GRID-038 — Artifact retry

Same revision/type/renderer identity does not create uncontrolled duplicate artifact.

---

# 362. E2E-GRID-039 — Renderer upgrade

Renderer v2 produces distinct artifact identity without altering Revision.

---

# 363. E2E-GRID-040 — Historical regeneration

Old Revision R2 re-rendered after Draft now R5.

Renderer uses R2 exact cells.

---

# 364. E2E-GRID-041 — Questionnaire

Questionnaire selects R3.

PDF uses R3 composite.

---

# 365. E2E-GRID-042 — New Primary later

Grid R4 becomes current Primary.

Historical Questionnaire with R3 remains R3.

---

# 366. E2E-GRID-043 — Questionnaire no raw cells

Document DTO contains composite reference, not list of individual emotional photos.

---

# 367. E2E-GRID-044 — Builder selection

Builder sees approved eligible Grid only.

---

# 368. E2E-GRID-045 — Builder manipulation

Visitor attempts to submit custom crop values.

Ignored/rejected.

---

# 369. E2E-GRID-046 — Builder revocation

Grid permission revoked before Generate.

Generation blocked.

---

# 370. E2E-GRID-047 — Hidden Primary Grid

Primary Grid not Public eligible.

Homepage/Public does not expose it.

---

# 371. E2E-GRID-048 — Draft route guessed

Anonymous request for Draft ID denied.

---

# 372. E2E-GRID-049 — Public DTO privacy

Public response contains no:

```text
cropX
faceOccupancy
confirmedBy
internal source locator
```

---

# 373. E2E-GRID-050 — Theme safety

Theme change cannot alter cell crops/order.

---

# 374. E2E-GRID-051 — Social safety

Social derivative preserves internal cell composition.

---

# 375. E2E-GRID-052 — AI proposal

AI auto-fills 4×4 Draft.

All 16 cells remain unconfirmed.

---

# 376. E2E-GRID-053 — AI ranking

Technical suitability sorting works without attractiveness score.

---

# 377. E2E-GRID-054 — VOP retry artifact

VOP retries failed WEB render.

No GridRevision mutation.

---

# 378. E2E-GRID-055 — Archive

Archive current Grid.

Removed from new Public/QNR/Builder selections.

---

# 379. E2E-GRID-056 — Archive source preservation

Source MediaAssets remain.

---

# 380. E2E-GRID-057 — Historical usage

Archived GridRevision still resolves in historical Questionnaire where access allowed.

---

# 381. E2E-GRID-058 — Privacy source removal

One source photo becomes legally/privacy blocked.

System discovers all Grid artifacts and dependent Questionnaires.

No silent substitution.

---

# 382. E2E-GRID-059 — Low resolution source

Crop would require insufficient effective resolution.

Readiness warns/blocks according to output requirements.

No generative repair baseline.

---

# 383. E2E-GRID-060 — Legacy composite

Import only raster 4×4 image with no cell provenance.

System does not treat it as native editable GridRevision.

---

# 384. E2E-GRID-061 — Accessibility

Public composite has meaningful alt/context and link to full Emotional Portfolio.

---

# 385. E2E-GRID-062 — Keyboard Grid editor

Admin can assign/navigate/confirm cells without mouse-only dependency.

---

# 386. E2E-GRID-063 — Preview vs server artifact

Client preview differs slightly raster-wise.

Canonical saved artifact remains server-rendered exact Revision output.

---

# 387. E2E-GRID-064 — Cache

Old primary alias cached.

New Primary selected.

Current route resolves new approved revision after invalidation/current authorization.

---

# 388. E2E-GRID-065 — AI fully disabled

Complete 4×4 Grid can be manually created, confirmed, finalized and rendered.

---

# 389. Architecture diagram

```text
                EmotionalSession
                       │
                       ▼
                 Source Photos
                       │
                       ▼
                EmotionalGrid
                       │
                       ▼
                Grid Draft
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
     Source         Transform      Confirmation
        │              │              │
        └──────────────┴──────────────┘
                       ▼
                 FINALIZE
                       ▼
                GridRevision
                       │
        ┌──────────────┼──────────────┬──────────────┐
        ▼              ▼              ▼              ▼
      MASTER           WEB            PDF         THUMBNAIL
```

---

# 390. Cell lifecycle diagram

```text
EMPTY
  ↓
SOURCE ASSIGNED
  ↓
TRANSFORMED
  ↓
VALIDATED
  ↓
HUMAN CONFIRMED
  │
  ├── edit source/transform
  │       ↓
  └──── UNCONFIRMED
```

---

# 391. Finalization gate diagram

```text
Allowed Size? ───────────────┐
Exact Cell Count? ───────────┤
Unique Positions? ───────────┤
Unique Sources? ─────────────┤
Same Session? ───────────────┤
Valid Media? ────────────────┤
Valid Transforms? ───────────┤
Face Target ≥90%? ───────────┤
All Human Confirmed? ────────┤
                              ▼
                         FINALIZE
                              ↓
                        GridRevision
```

---

# 392. AI assistance diagram

```text
Source Photos
     ↓
AI/CV Analysis
     ↓
Suggested:
- source selection
- face crop
- occupancy
- duplicate warning
     ↓
UNCONFIRMED DRAFT
     ↓
Human Adjusts
     ↓
Human Confirms Each Cell
```

---

# 393. Artifact diagram

```text
GridRevision R4
      │
      ├── MASTER / renderer v2
      ├── WEB / renderer v2
      ├── PDF / renderer v2
      └── THUMBNAIL / renderer v2

All share identical semantic cell composition.
```

---

# 394. Questionnaire diagram

```text
GridRevision
    ↓
PDF/WEB Composite
    ↓
Emotional Questionnaire Block
    ├── Composite
    ├── Shooting Date
    └── Full Emotional Portfolio URL
```

---

# 395. Quality gate

Перед implementation должны быть подтверждены:

- [ ] exact 12-size allowlist;
- [ ] rows×columns convention;
- [ ] exact cell count;
- [ ] unique cell position;
- [ ] unique source per finalized Grid;
- [ ] same-session source restriction;
- [ ] immutable source policy;
- [ ] transform coordinate model;
- [ ] crop/pan/scale/rotation validation;
- [ ] rotation limits;
- [ ] equal-cell layout;
- [ ] face occupancy target ≥90%;
- [ ] face detector abstraction;
- [ ] Human confirmation state;
- [ ] edit-invalidates-confirmation rules;
- [ ] Draft lifecycle;
- [ ] Finalize transaction;
- [ ] durable Finalize idempotency;
- [ ] immutable GridRevision;
- [ ] source/checksum provenance;
- [ ] MASTER artifact;
- [ ] WEB artifact;
- [ ] PDF artifact;
- [ ] THUMBNAIL artifact;
- [ ] render-version identity;
- [ ] artifact validation;
- [ ] Public eligibility;
- [ ] Questionnaire exact-revision binding;
- [ ] Builder selection-only behavior;
- [ ] Primary Grid semantics;
- [ ] AI suggestion-only workflow;
- [ ] VOP safe-action limits;
- [ ] access/privacy rules;
- [ ] archive/restore;
- [ ] privacy source removal handling;
- [ ] migration handling;
- [ ] accessibility;
- [ ] full E2E suite.

---

# 396. Acceptance criteria

`AC-GRID-001`  
Поддерживаются только размеры `1×2, 1×3, 1×4, 2×2, 2×3, 2×4, 3×2, 3×3, 3×4, 4×2, 4×3, 4×4`.

`AC-GRID-002`  
Finalize требует exact `rows×columns` заполненных cells.

`AC-GRID-003`  
Finalized Grid не содержит пустых cells.

`AC-GRID-004`  
Все Grid photos принадлежат одной Emotional Session.

`AC-GRID-005`  
Все source photos являются immutable IMAGE MediaAssets.

`AC-GRID-006`  
Один source image не повторяется в finalized Grid.

`AC-GRID-007`  
Каждая cell имеет уникальную position.

`AC-GRID-008`  
Каждая cell поддерживает contextual crop.

`AC-GRID-009`  
Каждая cell поддерживает pan.

`AC-GRID-010`  
Каждая cell поддерживает scale.

`AC-GRID-011`  
Каждая cell поддерживает bounded rotation.

`AC-GRID-012`  
Cell transforms никогда не меняют source original.

`AC-GRID-013`  
Transform contract одинаков для Admin и renderer.

`AC-GRID-014`  
Face occupancy target составляет не менее 90% useful cell area.

`AC-GRID-015`  
Automated face estimate не заменяет Human confirmation.

`AC-GRID-016`  
Каждая cell должна быть подтверждена Human Admin.

`AC-GRID-017`  
AI не может подтвердить cell.

`AC-GRID-018`  
Изменение source/transform сбрасывает confirmation.

`AC-GRID-019`  
Finalize невозможен при хотя бы одной unconfirmed cell.

`AC-GRID-020`  
Finalize является explicit Human command.

`AC-GRID-021`  
Finalize idempotent.

`AC-GRID-022`  
GridRevision создаётся server-side.

`AC-GRID-023`  
GridRevision immutable.

`AC-GRID-024`  
GridRevision сохраняет exact sources/transforms/order.

`AC-GRID-025`  
GridRevision сохраняет confirmation provenance.

`AC-GRID-026`  
Rendering выполняется после Revision commit.

`AC-GRID-027`  
Система создаёт MASTER artifact.

`AC-GRID-028`  
Система создаёт WEB artifact.

`AC-GRID-029`  
Система создаёт PDF artifact.

`AC-GRID-030`  
Система создаёт THUMBNAIL artifact.

`AC-GRID-031`  
Все artifacts сохраняют одну и ту же semantic composition.

`AC-GRID-032`  
Artifact identity включает renderer version.

`AC-GRID-033`  
Artifact failure не уничтожает Revision.

`AC-GRID-034`  
Historical artifact regeneration использует exact Revision.

`AC-GRID-035`  
Questionnaire использует exact approved GridRevision.

`AC-GRID-036`  
Questionnaire не пересобирает Grid из source photos самостоятельно.

`AC-GRID-037`  
Builder выбирает approved Grid, но не редактирует его.

`AC-GRID-038`  
Builder revalidates eligibility before Generate.

`AC-GRID-039`  
Grid Draft никогда не является Public.

`AC-GRID-040`  
Public DTO не раскрывает crop/face/Admin confirmation internals.

`AC-GRID-041`  
AI может только предлагать source/crop/occupancy.

`AC-GRID-042`  
AI auto-fill остаётся unconfirmed Draft.

`AC-GRID-043`  
Core workflow работает полностью без AI.

`AC-GRID-044`  
Theme не может менять cell composition.

`AC-GRID-045`  
Social publishing не пересобирает individual cells.

`AC-GRID-046`  
Archive не удаляет source images.

`AC-GRID-047`  
Historical GridRevision не переписывается.

`AC-GRID-048`  
Privacy source removal не приводит к silent substitution.

`AC-GRID-049`  
Legacy flat composite не считается native editable Grid без cell provenance.

`AC-GRID-050`  
Все критические правила покрыты E2E concurrency/history/privacy/render tests.

---

# 397. Финальная доктрина

> **Emotional Grid является Human-confirmed immutable композиционным представлением одной конкретной Emotional Session. Он поддерживает только двенадцать строго разрешённых размеров, требует точного количества ячеек, уникальных source photos, корректной contextual crop/pan/scale/rotation configuration и целевого заполнения лицом не менее 90% полезной площади каждой ячейки. Ни автоматический detector, ни AI proposal не могут заменить подтверждение каждой ячейки человеком. Finalize создаёт immutable GridRevision, после чего MASTER, WEB, PDF и THUMBNAIL генерируются детерминированно из этой exact Revision. Ни Theme, ни Questionnaire, ни Public Builder, ни Social Publishing не имеют права пересобирать или менять утверждённую композицию.**