# MEDIA ARCHITECTURE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная архитектура хранения, обработки, публикации и жизненного цикла медиа

**Целевой файл:** `docs/architecture/media.md`  
**Документ:** DOC-074  
**Статус:** ✅ Completed  
**Тип:** Architecture / Media / Storage / Processing / Delivery

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/product-principles.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/forms-and-validation.md`
- `docs/ux/ui-states.md`
- `docs/ux/content-hierarchy.md`
- `docs/ux/responsive-behaviour.md`
- `docs/ux/accessibility.md`
- `docs/domain/domain-model.md`
- `docs/domain/relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/components.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`

---

# 1. Назначение документа

Настоящий документ определяет целевую архитектуру Media subsystem.

Он фиксирует:

1. модель `MediaAsset`;
2. неизменяемость оригиналов;
3. storage abstraction;
4. public/private storage zones;
5. upload pipeline;
6. server-side import;
7. URL/external-media handling;
8. metadata extraction;
9. image derivatives;
10. video/audio processing;
11. document handling;
12. Emotional Grid rendering;
13. stable public media URLs;
14. responsive media delivery;
15. media security;
16. metadata privacy;
17. duplicate detection;
18. usage/dependency graph;
19. archive/delete semantics;
20. background processing;
21. failure recovery;
22. cache behaviour;
23. backup/restore;
24. migration requirements;
25. observability;
26. acceptance criteria.

---

# 2. Главный принцип

> **Оригинальный media asset является неизменяемым источником. Любой crop, resize, оптимизация, thumbnail, poster, PDF-версия или Emotional Grid представляет собой производный артефакт либо контекстную конфигурацию и никогда не перезаписывает оригинал.**

Canonical:

```text
IMMUTABLE ORIGINAL
       │
       ├── technical metadata
       │
       ├── professional relations
       │
       └── DERIVATIVES
             ├── thumbnail
             ├── responsive web
             ├── PDF
             ├── poster
             └── composite
```

---

# 3. Media IDs

Используются идентификаторы:

```text
MED-*
MED-STO-*
MED-UPL-*
MED-DRV-*
MED-DEL-*
MED-SEC-*
MED-EMO-*
MED-BCK-*
```

Architectural invariants:

```text
MED-INV-*
```

---

# 4. Область Media subsystem

Media subsystem управляет:

```text
Image
Video
Audio
Document
```

и их:

```text
source identity
technical metadata
storage
processing
derivatives
delivery
usage
lifecycle
```

---

# 5. External URL не всегда MediaAsset

Внешние профессиональные URL должны различаться по семантике.

## Binary/local media

Например:

```text
JPEG
PNG
WebP source
MP4
MOV
MP3
WAV
PDF document
```

→ `MediaAsset`.

## External professional link

Например:

```text
YouTube URL
Vimeo URL
external audio page
IMDb/profile/reference URL
```

→ преимущественно `ProfessionalLink` / normalized external media reference.

---

# 6. External Media Resolution

Если внешний URL используется как playable video/audio:

```text
ProfessionalLink
      ↓
External Media Resolver
      ↓
normalized playback projection
```

External resolver MUST NOT download/copy third-party media automatically unless отдельный import workflow это явно разрешает.

---

# 7. MED-INV-001 — Immutable Original

После успешной фиксации оригинала его binary content MUST NOT изменяться обычными пользовательскими операциями.

---

# 8. Запрещённые изменения original

Нельзя выполнять in-place:

```text
crop
resize
rotation rewrite
compression rewrite
color correction
retouching
face modification
watermark
metadata stripping
format conversion
```

над исходным бинарным объектом.

---

# 9. Orientation

Если изображение содержит EXIF orientation:

- original остаётся неизменным;
- normalized derivative MAY physically rotate pixels для корректного display;
- transformation MUST быть deterministic;
- source orientation metadata сохраняется внутренне.

---

# 10. MED-INV-002 — No Appearance Mutation

Media processing MUST NOT:

```text
изменять лицо;
форму тела;
кожу;
волосы;
черты лица;
выражение;
одежду;
макияж;
фон посредством генерации;
добавлять отсутствующие детали;
удалять существующие детали через generative fill.
```

---

# 11. Разрешённые deterministic transformations

Допускаются:

```text
crop
scale
format conversion
loss-aware compression
orientation normalization
thumbnail generation
poster extraction
container/transcode where explicitly configured
```

при условии отсутствия изменения внешности/содержания.

---

# 12. MediaAsset

Canonical `MediaAsset` представляет логический источник.

Он владеет:

```text
id
profile_id
media_type
source_kind
original_filename
mime_type
file_size
checksum
storage_locator
technical_metadata
processing_state
lifecycle
created_at
```

Точная схема — DOC-090/091.

---

# 13. Media Type

Canonical:

```text
IMAGE
VIDEO
AUDIO
DOCUMENT
```

---

# 14. Source Kind

Recommended:

```text
UPLOAD
SERVER_IMPORT
MIGRATION
EXTERNAL_IMPORT
```

`EXTERNAL_LINK` SHOULD обычно оставаться ProfessionalLink, а не MediaAsset.

---

# 15. MediaAsset не хранит все профессиональные значения

`MediaAsset` не должен становиться owner для:

```text
Portfolio category
Role meaning
Questionnaire order
Emotional crop
homepage showcase
```

Эти значения принадлежат соответствующим domain relations/configuration.

---

# 16. Media Storage Abstraction

Приложение MUST обращаться к binary storage через abstraction:

```text
MediaStorage
```

---

# 17. MediaStorage Contract

Концептуально:

```text
putOriginal()
readOriginal()
putDerivative()
readDerivative()
exists()
deleteObject()
copyObject()
getMetadata()
```

---

# 18. Storage backend не должен проникать в Domain

Domain не должен знать:

```text
/data/kate-actor/media/...
s3://bucket/...
MinIO endpoint
```

---

# 19. Initial backend

Для текущей self-hosted архитектуры допустим local persistent filesystem.

Recommended logical root:

```text
/data/kate-actor/
```

Конкретный physical layout фиксируется Operations/Deployment docs.

---

# 20. Future backend

Архитектура MUST позволять заменить backend на:

```text
S3-compatible object storage
MinIO
cloud object storage
CDN-backed delivery
```

без изменения professional domain model.

---

# 21. Storage Locator

Database хранит internal `storage_locator`.

Он:

```text
!= public URL
```

---

# 22. MED-INV-003 — No Public Filesystem Paths

Публичный response, QR, PDF или HTML MUST NOT содержать:

```text
/data/...
/mnt/...
file://...
internal bucket filesystem path
```

---

# 23. Storage Zones

Media storage MUST логически разделяться минимум на:

```text
ORIGINAL_PRIVATE
DERIVED_PUBLIC
DERIVED_PRIVATE
OPERATIONAL_PRIVATE
TEMPORARY
```

---

# 24. ORIGINAL_PRIVATE

Original source хранится в trusted storage zone.

Даже если фото публично используется, original storage path не обязан быть публичным.

---

# 25. DERIVED_PUBLIC

Содержит только одобренные public/casting-safe derivatives, доступные через стабильный delivery layer.

---

# 26. DERIVED_PRIVATE

Examples:

```text
Admin preview
private questionnaire assets
protected casting package derivatives
```

---

# 27. OPERATIONAL_PRIVATE

Examples:

```text
Feedback attachments
Casting source files
Support attachments
```

Они не являются частью публичной Media Library.

---

# 28. TEMPORARY

Examples:

```text
upload staging
processing work files
PDF intermediate files
transcoding scratch space
```

Temporary content MUST иметь cleanup policy.

---

# 29. Physical Separation

Storage zones MAY находиться на одном filesystem/backend, но должны иметь:

```text
different namespaces
different access paths
different delivery policy
```

---

# 30. Public Delivery

Browser SHOULD получать media через:

```text
stable public route
CDN/public object URL
media delivery endpoint
```

а не через raw storage locator.

---

# 31. Private Delivery

Private media MUST проходить:

```text
request
→ authentication/token validation
→ authorization
→ protected delivery
```

---

# 32. MED-INV-004 — Static Public Directory Restriction

Feedback/Casting/private attachments MUST NOT находиться в directory, автоматически доступной web server как public static files.

---

# 33. Stable Media URL

Published media used by:

- public site;
- Questionnaire;
- QR;
- shared casting-safe package;

MUST иметь stable canonical URL.

---

# 34. Stable ≠ Permanent Forever

Stable означает:

> URL не зависит от текущего filesystem path, process ID или временного signed upload path.

---

# 35. Public Media URL Design

Концептуально допустимо:

```text
/media/{public-media-id}/{variant}
```

или equivalent storage/CDN path.

Exact routes later.

---

# 36. URL Must Be Domain/Delivery-Based

Нельзя строить URL напрямую из:

```text
filesystem filename
absolute disk path
temporary upload name
```

---

# 37. Public Media Identity

Public route SHOULD использовать:

```text
opaque ID
stable slug-like media identifier
immutable artifact ID
```

---

# 38. Upload Pipeline

Canonical:

```text
SELECT FILE
    ↓
CLIENT PRECHECK
    ↓
SERVER RECEIVE
    ↓
AUTHORIZATION
    ↓
SECURITY VALIDATION
    ↓
STAGING
    ↓
CHECKSUM / MIME / SIZE
    ↓
STORE IMMUTABLE ORIGINAL
    ↓
PERSIST MediaAsset
    ↓
QUEUE PROCESSING
    ↓
RETURN STORED
```

---

# 39. Client Precheck

Client MAY проверять:

```text
extension
declared size
basic media type
```

для UX.

Это не security authority.

---

# 40. Server Validation

Server MUST самостоятельно проверить:

```text
actual content/MIME
allowed type
size limits
malformed content
authorization
profile ownership
```

---

# 41. Extension Is Not Trusted

`photo.jpg.exe` или файл с ложным MIME не должен приниматься только по расширению.

---

# 42. MIME Detection

System SHOULD использовать server-side content-based MIME detection где доступно.

---

# 43. File Size

Limits MUST быть configurable per media type.

Точные лимиты — NFR/module docs.

---

# 44. Image Validation

Image input SHOULD быть реально декодируемым поддерживаемым image codec.

---

# 45. Video Validation

Video SHOULD быть probe-able:

```text
container
codec
duration
dimensions
streams
```

---

# 46. Audio Validation

Audio SHOULD быть probe-able:

```text
codec
duration
channels
sample rate where relevant
```

---

# 47. Document Validation

Document upload must allow only approved formats.

Documents capable of active executable content require explicit security decision.

---

# 48. Filename Handling

Original filename MAY сохраняться как metadata.

Storage object name MUST NOT blindly use user filename.

---

# 49. Safe Storage Name

Use generated safe storage identity.

---

# 50. Checksum

Original SHOULD receive cryptographic checksum.

Use cases:

```text
integrity
duplicate detection
backup verification
idempotency
```

Exact algorithm later.

---

# 51. Duplicate Detection

System SHOULD detect:

```text
exact binary duplicates
```

by checksum.

---

# 52. Duplicate Is Not Automatic Deletion

При обнаружении duplicate:

```text
suggest reuse / show existing
```

но система не должна автоматически удалять существующий asset без явной policy.

---

# 53. Near-Duplicate Analysis

Future/optional AI/computer-vision may detect visually similar images.

Это:

```text
suggestion
```

not authoritative duplicate fact.

---

# 54. Upload Transaction Boundary

Binary storage и database persistence должны быть согласованы.

System must avoid long-lived:

```text
DB record with missing original
```

or:

```text
orphan source file with no record
```

---

# 55. Orphan Cleanup

If crash occurs between stages, recovery/cleanup job SHOULD reconcile staging/orphans.

---

# 56. Upload Idempotency

Repeated upload request MAY use:

```text
upload transaction ID
checksum
```

to prevent accidental duplicate source registration.

---

# 57. Bulk Upload

Bulk upload MUST process assets independently enough to support:

```text
13 succeeded
2 failed
```

without rolling back all valid uploads.

---

# 58. Bulk Result

Admin receives per-file:

```text
success
duplicate candidate
validation error
processing state
```

---

# 59. Server Import

Existing server files MAY be indexed through controlled Server Import workflow.

---

# 60. Server Import Security

Admin MUST NOT be allowed to submit arbitrary filesystem path unrestrictedly.

Import root MUST be allowlisted.

---

# 61. Server Import Flow

```text
Authorized Import Root
      ↓
Enumerate files
      ↓
Select candidates
      ↓
Validate
      ↓
Checksum
      ↓
Copy/register immutable source
      ↓
Persist MediaAsset
```

---

# 62. Register-in-place vs Copy

Preferred architecture SHOULD normalize imported asset into managed storage.

Direct unmanaged references are discouraged because:

```text
source can disappear
permissions can change
backup may omit it
```

---

# 63. Migration Import

Legacy media migration MUST preserve:

```text
source provenance
known relationships
file integrity
```

---

# 64. Metadata Extraction

Technical metadata is derived automatically.

---

# 65. Image Technical Metadata

Recommended:

```text
pixel_width
pixel_height
orientation
format
color profile where relevant
alpha
```

---

# 66. Video Technical Metadata

Recommended:

```text
duration
width
height
aspect ratio
container
video codec
audio codec
frame rate where useful
```

---

# 67. Audio Technical Metadata

Recommended:

```text
duration
format/container
codec
sample rate
channels
bitrate where useful
```

---

# 68. Document Metadata

Recommended:

```text
mime
page count where safe/available
file size
document format
```

---

# 69. Technical Metadata Is Derived

User SHOULD NOT manually edit decoder-derived width/duration.

If source metadata extraction is wrong because file malformed, fix processor/source rather than hand-edit derived truth.

---

# 70. Professional Metadata Separate

Examples:

```text
Portfolio category
caption
Role association
Shooting Date
```

remain Domain data.

---

# 71. EXIF Metadata

Original MAY preserve EXIF internally.

Public derivatives SHOULD remove unnecessary private metadata.

---

# 72. GPS Privacy

GPS/location metadata MUST NOT be exposed publicly by default.

---

# 73. Camera Metadata

Public exposure of EXIF technical camera data is unnecessary unless explicitly required.

Default:

```text
strip from public derivatives
```

---

# 74. Date Taken

EXIF capture date MUST NOT automatically become professional Shooting Date.

It MAY be suggested to Admin.

---

# 75. Image Derivative Architecture

Canonical derivative classes:

```text
THUMBNAIL
WEB_SMALL
WEB_MEDIUM
WEB_LARGE
PDF
POSTER
CUSTOM_CONTEXT
```

Exact names/sizes later.

---

# 76. Responsive Web Images

Public UI SHOULD receive appropriately sized derivative according to viewport/rendering context.

---

# 77. No Huge Original for Thumbnail

Anti-pattern:

```text
download 20 MB original
→ CSS width 200px
```

---

# 78. Responsive Candidate Set

Architecture SHOULD allow:

```text
srcset / sizes
```

or equivalent adaptive media selection.

---

# 79. P0 Media

Hero/Primary Portfolio derivatives SHOULD be optimized and available early.

---

# 80. P1/P2 Media

Secondary gallery media MAY lazy-load.

---

# 81. Aspect Ratio

Derivative generation SHOULD preserve source aspect ratio unless an explicit contextual crop is defined.

---

# 82. Existing Admin Baseline

Existing behavior preserving:

```text
contain
original aspect ratio
full source frame
no implicit crop
```

remains canonical for ordinary source display unless explicit crop relation exists.

---

# 83. Automatic Responsive Crop

MUST NOT randomly crop actress based on viewport.

---

# 84. Contextual Crop

Allowed only when domain/configuration explicitly owns crop.

Examples:

```text
EmotionalGridCell crop
approved thumbnail crop if later introduced
```

---

# 85. Crop Configuration

Must be stored separately from original:

```text
x/y focal region
scale
translation
rotation
```

as appropriate.

---

# 86. Image Format Conversion

Web derivative MAY use efficient supported format.

Original format remains unchanged.

---

# 87. Color Integrity

Image processing SHOULD avoid unintended visible color shifts.

Embedded color profiles should be handled predictably.

---

# 88. Compression

Compression must balance:

```text
professional visual fidelity
page performance
```

Exact quality thresholds later.

---

# 89. No Beautification Filter

No automatic:

```text
skin smoothing
eye enhancement
face sharpening targeted to facial traits
body reshape
AI upscaling that hallucinates details
```

---

# 90. Technical Sharpening

Generic deterministic image resampling/sharpening MAY be used conservatively if it does not alter appearance/content.

---

# 91. Video Architecture

Video source may be:

```text
local MediaAsset
external ProfessionalLink
```

---

# 92. Local Video Source

Original stored immutable.

Derived processing MAY create:

```text
web-compatible playback rendition
poster frame
preview metadata
```

---

# 93. Video Transcoding

Transcoding MAY occur when needed for browser compatibility/performance.

It creates derivative.

---

# 94. Video Source Preservation

Original uploaded video remains unchanged.

---

# 95. Poster

Poster SHOULD be derived from source or explicitly selected professional image.

It must not generate synthetic actor appearance.

---

# 96. Video Playback Projection

Public player receives normalized:

```text
playback URL
poster URL
duration
mime/source type
captions/subtitles if available
```

---

# 97. External Video

Resolver handles provider-specific embedding metadata.

Provider scripts must be isolated and privacy/security reviewed.

---

# 98. Video Failure

External/provider video failure must not break page.

UI provides:

```text
fallback
direct safe link where allowed
```

---

# 99. Audio Architecture

Local audio follows same original/derivative model.

---

# 100. Audio Public Projection

May contain:

```text
title
duration
playback URL
format metadata as needed
```

---

# 101. Audio Waveform

If future UI needs waveform, it is a derived artifact.

Not mandatory.

---

# 102. Document Architecture

Documents may include:

```text
certificates
professional supporting documents
other approved materials
```

---

# 103. Questionnaire PDF Is Not Ordinary Source Media

Generated Questionnaire PDF belongs Document/Artifact subsystem and references `QuestionnaireRevision` or `BuilderGenerationSnapshot`.

---

# 104. Uploaded Document

Uploaded document may be MediaAsset when it is professional/source material.

---

# 105. Uploaded PDFs

Public availability requires explicit permission.

Not all uploaded documents are public.

---

# 106. Document Preview

Preview derivative MAY be generated.

Original remains source.

---

# 107. Emotional Grid Architecture

Emotional Grid is a special derived visual subsystem.

---

# 108. Grid Source Boundary

A Grid can use only:

```text
MediaAssets attached to same EmotionalSession
```

---

# 109. Allowed Dimensions

Exact canonical dimensions:

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

# 110. Exact Cell Count

```text
number_of_cells
=
rows × columns
```

required before finalization.

---

# 111. Emotional Grid Cell

Each cell stores:

```text
source media reference
crop
pan/translation
scale
rotation
confirmation state
```

---

# 112. Face Occupancy Target

Grid editing/readiness must support target:

> **лицо модели занимает не менее 90% полезной площади ячейки**, согласно утверждённому product requirement.

---

# 113. 90% Is Crop Goal, Not AI Appearance Transformation

System may evaluate/suggest crop.

It must not enlarge the face through generative transformation.

---

# 114. Face Detection

AI/computer vision MAY identify:

```text
face bounds
candidate crop
occupancy estimate
```

---

# 115. Face Detection Result

Is:

```text
suggestion / validation aid
```

not final crop approval.

---

# 116. Human Confirmation

Every Grid cell MUST be human-confirmed before finalization.

---

# 117. Grid Rendering Input

Worker MUST receive exact frozen:

```text
GridRevision/config version
cell definitions
source MediaAsset identities/hashes
render specification
```

---

# 118. Grid Worker Cannot Read Mutable Draft

After finalization starts, worker cannot simply query “latest crop”.

---

# 119. Grid Composite Outputs

Required:

```text
MASTER
WEB
PDF
THUMBNAIL
```

---

# 120. Master Composite

High-quality deterministic composite used as parent for selected derivatives or archival render.

---

# 121. Web Composite

Optimized for public site.

---

# 122. PDF Composite

Optimized for professional document renderer.

---

# 123. Thumbnail Composite

Used for lists/previews.

---

# 124. Geometry Preservation

Once composite layout is defined:

```text
4x4 stays 4x4
```

Responsive public display may scale the whole composite, not rearrange cell order.

---

# 125. Grid Revision

All artifacts MUST reference exact finalized Grid version.

---

# 126. Editing Finalized Grid

Material crop/source change creates new draft/revision.

Old artifact remains bound to old revision.

---

# 127. Grid Artifact Immutability

Published/historical artifact MUST NOT be overwritten in place by new Grid revision.

---

# 128. Emotional Questionnaire Use

Questionnaire uses:

```text
approved composite
shooting date
clickable URL to full Emotional Portfolio
```

---

# 129. No Source Photo Dump

Questionnaire MUST NOT substitute individual EmotionalSession photos for canonical composite.

---

# 130. Derivative Specification

Every derivative SHOULD be reproducibly described by:

```text
source_asset_id
source_checksum/version
derivative_type
transformation_spec
processor_version
```

---

# 131. Derivative Cache Key

Conceptually:

```text
source checksum
+
transform spec
+
processor version
```

---

# 132. Regenerability

Derivatives SHOULD be rebuildable from:

```text
original
+
configuration
```

unless they represent historical composite tied to a frozen source version that must be retained.

---

# 133. Processor Version

When processing behaviour can materially alter output, store processor/render version.

---

# 134. Derivative State

Canonical:

```text
QUEUED
PROCESSING
READY
FAILED
STALE
```

---

# 135. MediaAsset Overall State

May be:

```text
STORED
PROCESSING
READY
PROCESSING_PARTIAL
PROCESSING_FAILED
```

per DOC-062.

---

# 136. Partial Ready

A failed optional derivative SHOULD NOT automatically make valid original unusable.

---

# 137. Required Derivative

Context readiness may define particular derivative as required.

Example:

```text
published Hero requires web derivative
```

If unavailable, corresponding publication/readiness may block or degrade according to module policy.

---

# 138. Background Job

Media processing MUST use durable jobs for expensive operations.

---

# 139. Job Payload

Should carry:

```text
asset_id
source checksum/version
derivative specification
job schema version
idempotency key
```

---

# 140. Worker Revalidation

Before writing derivative result, worker SHOULD confirm source/version still matches job.

---

# 141. Duplicate Job

Same derivative request MUST safely deduplicate/reuse result.

---

# 142. Worker Crash

Job remains retryable.

Temporary partial output should not be exposed as READY.

---

# 143. Atomic Derivative Publish

Recommended:

```text
write temporary output
validate
atomically register/move final object
mark derivative READY
```

---

# 144. Corrupt Derivative

Must never be marked READY only because file exists.

---

# 145. Image Validation After Processing

Generated derivative SHOULD be decodable and satisfy expected dimensions/type.

---

# 146. Video Validation After Processing

Generated playback rendition SHOULD be probe-able.

---

# 147. Storage Capacity Failure

If storage is full:

```text
source/derivative operation fails safely
existing assets unchanged
```

Admin/System Status receives explicit alert.

---

# 148. Stable Public URL Resolver

Public URLs SHOULD be resolved by dedicated media delivery component.

---

# 149. Public URL Inputs

Conceptually:

```text
MediaAsset / MediaDerivative
public access class
variant
```

---

# 150. Public URL Eligibility

Before public URL issuance:

```text
asset relation is public-eligible
AND derivative is approved/ready
AND lifecycle allows
```

---

# 151. Direct Public Asset

A MediaAsset SHOULD NOT become public solely by possession of asset ID.

---

# 152. Public Eligibility Is Contextual

Same source can be:

```text
public through Portfolio
private through another relation
```

Domain relationship/published projection determines access.

---

# 153. Canonical Public Media Strategy

For content intentionally published broadly, system MAY assign public artifact status independent of one page while retaining provenance.

---

# 154. Protected Media

Casting-specific/private assets require protected delivery.

---

# 155. Signed URLs

Future object storage MAY use signed URLs for private assets.

Their expiration MUST NOT become the canonical URL encoded in permanent QR/PDF.

---

# 156. MED-INV-005 — No Expiring QR Target

QR in long-lived professional PDF MUST NOT encode temporary signed media URL.

---

# 157. Casting-Safe URL

If a local asset must be opened from PDF/QR:

create stable application-controlled public/token-scoped route with appropriate lifecycle.

---

# 158. HTTP Cache

Public immutable derivatives MAY use long cache lifetime when URL/content identity is immutable.

---

# 159. Current Alias

If an alias points to changing current asset:

must have invalidation/version strategy.

---

# 160. ETag / Content Hash

Public delivery SHOULD support cache validation based on immutable identity/hash where practical.

---

# 161. Browser Security

Public file responses SHOULD set correct:

```text
Content-Type
Content-Disposition where relevant
security headers
```

---

# 162. Content-Disposition

Documents intended download:

```text
attachment
```

may be appropriate.

Images/video intended display/playback:

```text
inline
```

as safe.

---

# 163. Range Requests

Local video/audio delivery SHOULD support HTTP range requests where needed for normal seeking/playback.

---

# 164. Range Security

Protected media must authorize request before serving ranges.

---

# 165. Media Access Logging

Sensitive private asset access MAY be audited/logged at appropriate level.

---

# 166. Public Image Alt Data

Media binary does not inherently own accessible alt meaning.

Context projection should supply suitable:

```text
alt text
caption
professional context
```

---

# 167. AI Alt Text

AI MAY draft descriptive text.

For professional content it remains draft/assistive unless policy explicitly accepts deterministic generated alt after review.

---

# 168. Privacy of Facial Analysis Metadata

If face detection generates:

```text
bounding boxes
confidence
```

these should remain internal processing metadata.

No need to expose public.

---

# 169. Biometric Boundary

System SHOULD avoid storing unnecessary biometric identity templates.

Face detection for crop should operate on geometric face presence/bounds, not identification of the person.

---

# 170. Media Security

Upload/file processing is a trust boundary.

---

# 171. MED-SEC-001 — Allowlist

Only explicitly supported types are accepted.

---

# 172. MED-SEC-002 — Active Content

Potentially executable/active document formats require explicit policy.

Default should reject unnecessary active content.

---

# 173. MED-SEC-003 — Filename Sanitization

User-supplied filenames must not control path traversal.

---

# 174. MED-SEC-004 — Path Traversal

Reject:

```text
../
absolute path injection
encoded traversal
```

in any path-like input.

---

# 175. MED-SEC-005 — Storage Isolation

Uploaded file MUST NOT be executed by application runtime.

---

# 176. MED-SEC-006 — Private Files

Private operational file must never become publicly accessible merely by filename.

---

# 177. MED-SEC-007 — SVG

If SVG upload/display is ever supported:

it MUST be sanitized or delivered in a mode that prevents script execution.

Default image portfolio workflow SHOULD prefer raster formats.

---

# 178. MED-SEC-008 — HTML Documents

Raw user HTML MUST NOT be accepted as media/document without a dedicated sanitization model.

---

# 179. Malware Scanning

Architecture SHOULD permit malware scanning integration for externally supplied attachments/documents.

Especially:

```text
Feedback
Casting source documents
```

---

# 180. Scanner Unavailable

Policy must define whether:

```text
quarantine
reject
defer access
```

for security-sensitive attachments.

Exact security policy later.

---

# 181. Quarantine

Potentially unsafe file SHOULD be isolated from normal media delivery.

---

# 182. Public Media from Admin

Admin-uploaded professional image/video may follow trusted-admin path, but MIME/content validation still applies.

---

# 183. Feedback Attachment Boundary

Feedback attachment is not automatically a MediaAsset usable in Portfolio.

---

# 184. Promote Private Attachment

If Admin wants to reuse external file as professional Media:

must use explicit controlled import/promotion workflow:

```text
private source
→ security validation
→ create managed MediaAsset
→ professional classification
```

---

# 185. Casting Source Boundary

Same rule.

Casting source assets remain private unless explicitly promoted through managed workflow.

---

# 186. Media Usage Graph

System MUST be able to answer:

> **Где используется этот MediaAsset?**

---

# 187. Usage Sources

Potential relations:

```text
PortfolioItem
EmotionalSessionMedia
EmotionalGridCell
RoleMedia
Training evidence
Achievement evidence
SocialPost
current Questionnaire Draft indirectly
historical Revision snapshot/reference
```

---

# 188. Used-In Projection

Admin Media detail SHOULD expose current usage.

---

# 189. Dependency Categories

Use:

```text
ACTIVE_PUBLIC
CURRENT_DRAFT
OPERATIONAL
DERIVED
HISTORICAL
```

---

# 190. Active Public Dependency

Examples:

```text
Primary Close-Up
published RoleMedia
published Emotional Grid
```

High impact.

---

# 191. Current Draft Dependency

Example:

```text
Questionnaire Draft selection
```

---

# 192. Historical Dependency

Example:

```text
Questionnaire Revision 4
```

Does not necessarily block archive, but affects hard deletion/retention.

---

# 193. Archive MediaAsset

Archive removes asset from normal new selections.

---

# 194. Archive Preconditions

System SHOULD warn/block if asset currently drives critical public content.

Example:

```text
Primary Full Body
```

---

# 195. Archived Media Current Public Use

Default rule:

```text
archived source
→ no new active use
```

Existing current published dependency must be explicitly resolved according to module lifecycle.

---

# 196. Delete MediaAsset

Hard deletion is exceptional.

---

# 197. Dependency-Aware Delete

Before delete:

```text
load dependency graph
classify dependencies
apply retention
show exact impact
```

---

# 198. Delete Blockers

Examples:

```text
current Primary Portfolio
published Emotional Grid source without retained revision strategy
current RoleMedia
required historical artifact without retained immutable copy
```

---

# 199. Removing PortfolioItem

Deleting/archiving Portfolio relation MUST NOT automatically delete MediaAsset.

---

# 200. Removing RoleMedia

Same.

---

# 201. Delete Original

If allowed only after all dependencies removed and retention permits.

Derived artifacts MAY then be cleaned.

---

# 202. Historical Artifact Retention

Published Questionnaire PDF may remain valid even if original is later retired, provided the exact historical artifact/media representation is retained according to policy.

---

# 203. Redaction Exception

Privacy/legal redaction may override ordinary media retention.

Must be audited.

---

# 204. Replace Media

“Replace image” MUST be semantically explicit.

Recommended:

```text
create new MediaAsset
update selected professional relation
retain old historical asset
```

not overwrite binary in same MediaAsset ID.

---

# 205. MED-INV-006 — Content Identity Immutability

Different binary content SHOULD normally receive a new MediaAsset identity.

---

# 206. Reason

Otherwise:

```text
old revisions
checksums
QR/download links
cache
provenance
```

become unreliable.

---

# 207. Correct Replacement

```text
MediaAsset A
      ↓
new upload
      ↓
MediaAsset B
      ↓
Portfolio relation switches A → B
```

---

# 208. Historical Relation

Old QuestionnaireRevision remains bound to A/its retained artifact.

---

# 209. Media Versioning

Ordinary original binary is immutable and therefore does not need mutable file versions.

Replacement uses new asset.

---

# 210. Metadata Versioning

Mutable administrative metadata may use standard entity version/Audit.

---

# 211. Derivative Staleness

Derivative becomes STALE when:

```text
processor spec changes
context crop changes
source association changes
```

---

# 212. Original Never Stale

Original can be:

```text
available
corrupt
missing
archived
```

but not stale due to derivative processor version.

---

# 213. Missing Original

This is critical integrity failure.

Must surface:

```text
System Health
VOP
Admin Media status
```

---

# 214. Missing Derivative

Normally recoverable.

---

# 215. Integrity Check

System SHOULD periodically or on-demand verify:

```text
database locator exists
expected checksum
```

for critical originals.

---

# 216. Full Check Frequency

Exact schedule belongs Operations.

---

# 217. Backup Model

A valid product backup requires both:

```text
DATABASE
+
MEDIA STORAGE
```

---

# 218. Database Backup Alone Is Insufficient

Because it only preserves metadata/relationships, not source binaries.

---

# 219. Media Backup Classes

Must include:

```text
immutable originals
non-regenerable/historical artifacts
private operational files subject to retention
```

---

# 220. Regenerable Derivatives

May be excluded from some backup tiers only if:

```text
source retained
configuration retained
processor can rebuild
recovery time acceptable
```

---

# 221. Historical PDF

Published Questionnaire PDF SHOULD be considered important historical artifact even if semantically regenerable.

---

# 222. Emotional Grid Master

Finalized/published Grid artifacts SHOULD be retained or deterministically regenerable from retained frozen configuration and source.

---

# 223. Backup Integrity

Backup process SHOULD verify files rather than rely only on copied directory existence.

---

# 224. Restore Flow

```text
Restore PostgreSQL
      +
Restore Originals / retained artifacts
      ↓
Run media reconciliation
      ↓
Verify locators/checksums
      ↓
Identify missing derivatives
      ↓
Queue rebuild
```

---

# 225. Restore Must Not Regenerate Professional Meaning

Only deterministic derivatives may regenerate automatically.

---

# 226. Backup Point Consistency

Database and media backup SHOULD be coordinated sufficiently to avoid unrecoverable references.

Exact consistency procedure later.

---

# 227. Orphan Reconciliation

System SHOULD be able to report:

```text
DB record missing binary
binary without DB record
derivative with missing source
unknown temporary file
```

---

# 228. Orphan Cleanup Must Be Conservative

Never delete unknown file solely because one query did not find reference without defined grace period/reconciliation policy.

---

# 229. Storage Migration

Migration to new backend:

```text
copy
verify checksum
update locator atomically/batched
verify delivery
retain rollback path
```

---

# 230. Public URL During Storage Migration

Canonical media URL SHOULD remain stable even if backend changes.

This is a key reason for delivery abstraction.

---

# 231. Processor Migration

If image processor changes:

existing published historical artifacts need not automatically change.

New derivatives may use new processor version.

---

# 232. Reprocessing Existing Media

Admin/System MAY schedule derivative regeneration.

Original remains untouched.

---

# 233. Public Visual Stability

Do not unexpectedly replace all published professional visuals merely because processor upgraded.

Changes that materially affect rendered appearance require review/migration plan.

---

# 234. Media Processing Priority

Suggested conceptual priority:

```text
P0 public Hero/Primary
P1 public professional evidence
P2 secondary public
Admin-only
Archive
```

---

# 235. Priority Is Scheduling Only

It does not alter truth or permissions.

---

# 236. Upload UX

Admin should see:

```text
uploading
stored
processing
ready
partial failure
failed
```

according to DOC-047.

---

# 237. No Fake Progress

If processor cannot provide real progress:

use indeterminate state.

---

# 238. Processing Completion

UI may update through:

```text
polling
server refresh
event mechanism
```

Exact transport later.

---

# 239. Media Admin Views

Recommended:

```text
All
Images
Video
Audio
Documents
Unclassified
Recent
Processing Issues
Archived
```

---

# 240. External Links Not Mixed Blindly

External professional media MAY appear alongside Media Library via unified UX filter if useful, but underlying entities/ownership remain distinct.

---

# 241. Media Detail

Recommended Admin sections:

```text
Original
Technical Metadata
Derivatives
Professional Usage
Public/Private Context
Processing
History
```

---

# 242. Public Visibility Label

Media itself should not present one misleading universal:

```text
public=true
```

if its exposure depends on professional relations.

---

# 243. Derived Public Status

A derivative MAY have delivery class, but actual professional inclusion remains relation/projection driven.

---

# 244. Role Media

RoleMedia references MediaAsset.

No file duplication.

---

# 245. Home “В образе”

Home showcase references RoleMedia/MediaAsset.

No extra binary copy.

Maximum currently:

```text
2
```

selected showcase images.

---

# 246. Role Photo Limit

Current role-specific public selection limit:

```text
max 5
```

according to product rules.

This limit belongs relationship/business configuration, not storage.

---

# 247. Questionnaire Media

Questionnaire references professional semantic entities:

```text
PortfolioItem
RoleMedia
EmotionalGrid
ProfessionalLink
```

rather than duplicate binary files.

---

# 248. PDF Embedding

Document renderer gets revision/snapshot-bound media representation.

It MAY embed binary into PDF but does not create a new Source MediaAsset.

---

# 249. QR to Media

Only allowed if target media URL is:

```text
stable
public/casting-safe
authorized for context
```

---

# 250. Private Admin Preview QR

Prohibited for exported professional document.

---

# 251. Public Download Link

Local video/audio may expose clickable stable link in Questionnaire when configured.

---

# 252. Range and Streaming

Large local video delivery SHOULD be designed for efficient streaming/seeking.

---

# 253. Bandwidth Protection

Architecture SHOULD allow:

```text
rate limits
cache
CDN later
```

without changing MediaAsset identity.

---

# 254. Browser Format Support

If original format has poor browser support:

generate browser-compatible derivative.

---

# 255. Original Unsupported Format

Original may be retained even if only derivative is public.

---

# 256. Accessibility

Media architecture must support associated metadata necessary for:

```text
captions
subtitles
transcripts
alt text
descriptions
```

where applicable.

Exact accessibility requirements DOC-050/module specs.

---

# 257. Video Captions

If subtitle/caption file is stored:

model as explicit related media/document/caption asset rather than embedding uncontrolled text into player configuration.

---

# 258. Audio Transcript

If supported later, transcript is content/entity derived/linked to Audio, not binary technical metadata.

---

# 259. Media SEO

Public image projection MAY expose optimized URL and semantic alt/caption.

Filesystem filename is not SEO strategy.

---

# 260. Image Indexing

Whether public images are search-engine indexable follows SEO/publication policy, not storage existence.

---

# 261. Robots/private media

Private/token-scoped assets MUST NOT rely only on `robots.txt` for security.

---

# 262. Hotlinking

Optional protective policies MAY be added later.

Must not break legitimate casting/PDF use.

---

# 263. Signed Origin Delivery

Future CDN/object storage can hide origin behind stable application/CDN URL.

---

# 264. Processing Observability

Each job SHOULD expose:

```text
asset
operation
state
started_at
finished_at
attempts
failure code
processor version
```

---

# 265. Error Codes

Recommended stable classes:

```text
MEDIA_TYPE_UNSUPPORTED
MEDIA_TOO_LARGE
MEDIA_INVALID_CONTENT
MEDIA_STORAGE_FAILED
MEDIA_SOURCE_MISSING
MEDIA_METADATA_FAILED
MEDIA_DERIVATIVE_FAILED
MEDIA_TRANSCODE_FAILED
MEDIA_DEPENDENCY_BLOCK
MEDIA_PRIVATE_ACCESS_DENIED
MEDIA_GRID_RENDER_FAILED
```

Exact API codes later.

---

# 266. Logs

Media logs SHOULD NOT include:

```text
private signed URL token
full secrets
unnecessary private attachment contents
```

---

# 267. VOP Integration

Virtual Operator MAY surface:

```text
missing original
failed derivative
broken public media route
unclassified upload
duplicate candidate
stale Grid derivative
storage capacity issue
```

---

# 268. VOP Safe Actions

AUTO-4 examples:

```text
rebuild thumbnail
retry metadata extraction
retry web derivative
recheck storage existence
```

---

# 269. VOP Human-Confirmed Actions

Examples:

```text
classify Portfolio
replace Primary photo
archive source
promote private attachment
```

---

# 270. Analytics Boundary

Media analytics MAY capture:

```text
video started
video completed
portfolio item opened
```

but MUST NOT mutate Media/Portfolio ordering automatically.

---

# 271. Media Performance

Performance architecture SHOULD minimize:

```text
oversized downloads
unnecessary transcoding during request
layout shift
blocking P0 image processing
```

---

# 272. Pre-generation

Critical public derivatives SHOULD be generated before/at publication readiness rather than first visitor waiting for expensive conversion.

---

# 273. On-Demand Generation

May be used for non-critical variants if:

```text
safe
cached
bounded
not exposing originals
```

---

# 274. Request Path Processing

Expensive image/video transformation SHOULD NOT occur synchronously on every public request.

---

# 275. Media Readiness

Possible deterministic readiness:

```text
Source Available
Required Derivative Ready
Public Route Valid
Professional Relation Published
```

---

# 276. Portfolio Readiness

Requires MediaAsset and appropriate derivative but professional classification remains Portfolio responsibility.

---

# 277. Questionnaire Readiness

Valid source may still be unusable if selected media relation is not eligible/published.

---

# 278. Emotional Grid Readiness

Requires:

```text
all cells
same session
valid sources
confirmed crops
successful required composite outputs
```

---

# 279. Media Retention

Exact duration belongs DOC-094.

Architectural classes:

```text
ACTIVE_SOURCE
ARCHIVED_SOURCE
HISTORICAL_REQUIRED
TEMPORARY
REGENERABLE_DERIVED
PRIVATE_OPERATIONAL
```

---

# 280. Cleanup

Automatic cleanup is allowed for:

```text
expired staging
abandoned temporary render
safe regenerable orphan derivative
```

according to policy.

---

# 281. Cleanup Must Not Touch

Without explicit retention decision:

```text
original professional media
historical revision-required media
Feedback/Casting files under active retention
```

---

# 282. Delete Audit

Hard deletion or privacy redaction SHOULD be audited.

---

# 283. Public URL After Archive

Archived media no longer eligible for new public projection.

Whether previously public URL remains available depends on historical/public retention policy.

---

# 284. Broken Historical URL

Where possible, stable route/redirect strategy should preserve already distributed professional materials.

---

# 285. Stable URL vs Privacy

Privacy/security removal wins over historical convenience.

---

# 286. Media Migration From Current Implementation

Existing media must be audited for:

```text
file location
type
public exposure
current relation
duplicates
missing source
current crop behaviour
```

---

# 287. Legacy Direct Public Files

If current website directly serves raw upload paths:

migration SHOULD introduce managed delivery identities before changing files.

---

# 288. No Breaking URLs Without Mapping

Existing externally shared URLs SHOULD receive redirect/compatibility mapping where practical.

---

# 289. Existing `contain` Behaviour

Must be preserved as safe default during migration.

No migration may suddenly convert full-frame portfolio images to aggressive crops.

---

# 290. Emotional Existing Data

Existing emotional photos/session metadata should migrate into:

```text
EmotionalSession
EmotionalSessionMedia
```

with source binaries preserved.

---

# 291. Existing Grid/Composite

If current composite exists:

record as baseline derived artifact only when provenance/config can be established honestly.

Do not invent cell crop history.

---

# 292. Media Backup Migration

Before destructive media migration:

```text
backup
checksum inventory
restore test
```

required.

---

# 293. MED-AP-001

**Crop overwrites original image**

---

# 294. MED-AP-002

**Every Portfolio use creates duplicate uploaded binary**

---

# 295. MED-AP-003

**Raw filesystem path used as public URL**

---

# 296. MED-AP-004

**Private Feedback attachments inside `/public`**

---

# 297. MED-AP-005

**File extension trusted instead of actual content**

---

# 298. MED-AP-006

**Upload filename used directly as storage path**

---

# 299. MED-AP-007

**EXIF GPS exposed in public derivative**

---

# 300. MED-AP-008

**EXIF date silently becomes Emotional shooting date**

---

# 301. MED-AP-009

**Responsive UI downloads original 20 MB image and shrinks in CSS**

---

# 302. MED-AP-010

**Viewport automatically crops actress differently without explicit crop configuration**

---

# 303. MED-AP-011

**AI enhancement modifies facial traits**

---

# 304. MED-AP-012

**AI face crop is automatically confirmed**

---

# 305. MED-AP-013

**4x4 Emotional Grid reflows into different cell order on mobile**

---

# 306. MED-AP-014

**Grid worker renders from latest mutable Draft instead of frozen revision**

---

# 307. MED-AP-015

**Failed thumbnail marks original failed/deletes it**

---

# 308. MED-AP-016

**External YouTube URL duplicated into local file without explicit import**

---

# 309. MED-AP-017

**Deleting PortfolioItem cascades to source MediaAsset**

---

# 310. MED-AP-018

**Replacing image overwrites binary under same MediaAsset ID**

---

# 311. MED-AP-019

**Temporary signed URL encoded in long-lived QR**

---

# 312. MED-AP-020

**Search/cache becomes sole surviving copy of media metadata**

---

# 313. MED-AP-021

**Server import accepts arbitrary `/etc/...` paths**

---

# 314. MED-AP-022

**Public eligibility determined only by existence of public URL**

---

# 315. MED-AP-023

**Generated questionnaire stores a new duplicate image source instead of revision-bound derivative/reference**

---

# 316. MED-AP-024

**Orphan cleanup immediately deletes unknown file without grace/reconciliation**

---

# 317. MED-AP-025

**Database backed up while media originals are not**

---

# 318. Media Quality Gate

Перед production implementation Media architecture MUST определить:

- [ ] MediaAsset source model;
- [ ] immutable originals;
- [ ] supported types;
- [ ] upload validation;
- [ ] checksum;
- [ ] storage abstraction;
- [ ] public/private zones;
- [ ] staging;
- [ ] server import roots;
- [ ] metadata extraction;
- [ ] derivative classes;
- [ ] processor versioning;
- [ ] image fidelity policy;
- [ ] video/audio processing;
- [ ] stable public URL;
- [ ] protected private delivery;
- [ ] QR-safe media routes;
- [ ] EXIF/privacy handling;
- [ ] duplicate detection;
- [ ] usage/dependency graph;
- [ ] archive/delete behaviour;
- [ ] Grid render pipeline;
- [ ] job retry/idempotency;
- [ ] orphan reconciliation;
- [ ] backup scope;
- [ ] restore verification;
- [ ] observability.

---

# 319. MediaAsset Specification Template

```text
Asset Type:
IMAGE / VIDEO / AUDIO / DOCUMENT

Source Kind:
UPLOAD / SERVER_IMPORT / MIGRATION / ...

Original:
immutable

Storage Zone:
...

Technical Metadata:
...

Required Derivatives:
...

Public Delivery:
...

Private Delivery:
...

Professional Relations:
...

Retention:
...

Processing:
...

Failure Recovery:
...
```

---

# 320. Derivative Specification Template

```text
Derivative:
WEB_MEDIUM

Source:
MediaAsset ID + checksum

Transformation:
resize / format / crop...

Contextual Crop:
yes/no

Processor Version:
...

Public:
yes/no/contextual

Regenerable:
yes/no

Validation:
...

Cache Key:
...
```

---

# 321. Emotional Grid Render Specification Template

```text
Grid Revision:
...

Dimensions:
4x4

Cells:
16 exact

Every Cell Confirmed:
required

Source:
same EmotionalSession only

Transform Per Cell:
crop
pan
scale
rotation

Face Occupancy Target:
>= 90% useful cell area

Generative Modification:
prohibited

Outputs:
MASTER
WEB
PDF
THUMBNAIL

Historical:
immutable after finalization
```

---

# 322. E2E-MED-001 — Immutable Original

Upload image.

Create crop derivative.

Verify original checksum unchanged.

---

# 323. E2E-MED-002 — Replacement

Replace current Primary image.

Expected:

```text
new MediaAsset created
Portfolio relation changed
old historical asset retained
```

---

# 324. E2E-MED-003 — EXIF Privacy

Upload photo containing GPS.

Public derivative/response does not expose GPS metadata.

---

# 325. E2E-MED-004 — False MIME

Upload executable renamed `.jpg`.

Server rejects.

---

# 326. E2E-MED-005 — Path Traversal

Malicious filename cannot escape storage namespace.

---

# 327. E2E-MED-006 — Partial Processing

Original stored.

Thumbnail fails.

Expected:

```text
source preserved
partial status
retry succeeds
```

---

# 328. E2E-MED-007 — Public URL Stability

Move physical backend/path.

Stable public URL remains valid after migration/redirect.

---

# 329. E2E-MED-008 — Private Attachment

Feedback attachment cannot be fetched through public media route.

---

# 330. E2E-MED-009 — Portfolio Reuse

Same MediaAsset is used in Portfolio and Role.

Only one original binary exists.

---

# 331. E2E-MED-010 — Portfolio Delete

Delete PortfolioItem.

MediaAsset remains because source lifecycle is independent.

---

# 332. E2E-MED-011 — Dependency Block

Primary Full Body MediaAsset hard-delete attempt.

System reports blocker and rejects unsafe deletion.

---

# 333. E2E-MED-012 — Emotional Grid Count

4x4 Grid with 15 cells cannot finalize.

---

# 334. E2E-MED-013 — Emotional Confirmation

4x4 with 16 cells but one unconfirmed cannot finalize.

---

# 335. E2E-MED-014 — Grid Revision

Finalize Grid revision 1.

Edit cell crop.

Revision 1 artifact remains unchanged.

---

# 336. E2E-MED-015 — Grid Worker Race

Admin edits next Draft while worker renders revision 1.

Rendered artifact uses frozen revision 1 only.

---

# 337. E2E-MED-016 — Grid Mobile

4x4 public composite scales as 4x4.

Cells never reorder.

---

# 338. E2E-MED-017 — QR Media Target

QR target for local video is stable public/casting-safe URL.

Decode equals canonical URL.

---

# 339. E2E-MED-018 — Signed URL Rejection

Expiring private signed URL cannot be selected as permanent exported QR target.

---

# 340. E2E-MED-019 — Video Range

Local video supports expected seeking/range delivery without exposing storage path.

---

# 341. E2E-MED-020 — External Video Failure

External provider fails.

Public page retains identity/navigation/contact and displays recoverable media state.

---

# 342. E2E-MED-021 — Server Import

Attempt to import outside configured root is rejected.

---

# 343. E2E-MED-022 — Duplicate

Upload exact duplicate.

System detects duplicate candidate without destructive automatic deletion.

---

# 344. E2E-MED-023 — Backup Restore

Restore DB + originals.

Missing regenerable derivatives are detected and rebuilt.

---

# 345. E2E-MED-024 — Missing Original

DB points to missing source.

System marks integrity problem and surfaces critical Media/VOP/System state.

---

# 346. E2E-MED-025 — Public Privacy Payload

Public image DTO exposes derivative URL and professional context, not storage locator/internal checksum.

---

# 347. Media Traceability

Canonical:

```text
SOURCE BINARY
     ↓
MediaAsset
     ↓
Professional Relation
     ↓
Projection
     ↓
Public/Admin Usage
```

---

# 348. Derivative Traceability

```text
MediaAsset + checksum
        ↓
Transformation Spec
        ↓
Processor Version
        ↓
Derivative
        ↓
Delivery URL
```

---

# 349. Emotional Traceability

```text
EmotionalSession
      ↓
Session Media
      ↓
Grid Draft
      ↓
Human Confirmed Cells
      ↓
Frozen Grid Revision
      ↓
Composite Artifact
      ↓
Public / Questionnaire
```

---

# 350. Historical Media Traceability

```text
QuestionnaireRevision
      ↓
exact selected media/grid artifact
      ↓
PDF
```

Current Primary change MUST NOT affect historical chain.

---

# 351. Public Delivery Architecture

```text
                 PRIVATE STORAGE
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
         ORIGINALS          DERIVATIVES
                                 │
                          Eligibility Check
                                 │
                                 ▼
                        Media Delivery Layer
                                 │
                    ┌────────────┴───────────┐
                    ▼                        ▼
               PUBLIC URL              PROTECTED URL
                    │                        │
                    ▼                        ▼
              Public Website        Admin/Casting Scope
```

---

# 352. Media Processing Architecture

```text
UPLOAD
   ↓
STAGING
   ↓
SECURITY VALIDATION
   ↓
IMMUTABLE ORIGINAL
   ↓
MediaAsset STORED
   ↓
DURABLE JOB
   ↓
METADATA EXTRACTION
   ↓
DERIVATIVES
   ↓
VALIDATION
   ↓
READY / PARTIAL / FAILED
```

---

# 353. Emotional Grid Architecture

```text
IMMUTABLE SOURCE PHOTOS
        ↓
EMOTIONAL SESSION
        ↓
GRID CONFIGURATION
        ↓
AI CROP SUGGESTION optional
        ↓
HUMAN CONFIRMATION
        ↓
FROZEN GRID REVISION
        ↓
DETERMINISTIC RENDERER
        ↓
MASTER / WEB / PDF / THUMB
```

---

# 354. Media Compliance Criteria

Реализация соответствует DOC-074, если:

1. originals immutable;
2. changed binary creates new MediaAsset identity;
3. storage paths never become public URLs;
4. MediaStorage abstraction isolates backend;
5. public/private storage/delivery are separated;
6. Feedback/Casting files are private by default;
7. MIME/content server validation exists;
8. filenames cannot perform path traversal;
9. original checksums support integrity;
10. duplicates can be detected without destructive automatic merge;
11. technical metadata is derived;
12. EXIF GPS is excluded from public derivative;
13. shooting date is not inferred as truth from EXIF/upload date;
14. ordinary display preserves source aspect/full frame by default;
15. responsive pages use proper derivatives;
16. no generative appearance modification exists;
17. local video/audio can use browser-safe derivatives;
18. media processing is durable/retryable;
19. derivative jobs are idempotent;
20. failed derivative never destroys original;
21. Emotional Grid only uses same-session sources;
22. allowed Grid dimensions are enforced;
23. exact cell count is enforced;
24. every Grid cell requires Human confirmation;
25. face occupancy target is supported without appearance alteration;
26. Grid output binds to frozen revision;
27. published Grid geometry cannot responsive-reflow;
28. Questionnaire uses composite + date + full Emotional link;
29. stable media URLs support PDF/QR where applicable;
30. expiring/private URLs cannot become permanent public QR targets;
31. Usage/Dependency graph is available;
32. hard deletion is dependency-aware;
33. removing professional relation does not cascade-delete source;
34. backup includes source media;
35. restore can reconcile DB/storage and rebuild derivatives.

---

# 355. Финальная доктрина

> **Media subsystem должен относиться к фотографиям, видео, аудио и документам как к профессиональным исходным материалам, а не как к расходным UI-файлам. Оригинал сохраняется неизменяемым, профессиональный смысл задаётся отдельными domain relations, а все технические представления создаются как воспроизводимые derivatives. Публичная доставка всегда проходит через управляемый access/delivery layer, private media никогда не становится public только из-за расположения файла, а Emotional Grid формируется исключительно из утверждённых оригинальных фотографий через crop-конфигурацию и Human confirmation, без генеративного изменения внешности модели.**