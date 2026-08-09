# MEDIA LIBRARY MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация immutable originals, media ingest, metadata, derivatives, usage graph и delivery

**Целевой файл:** `docs/modules/media-library.md`  
**Документ:** DOC-111  
**Статус:** ✅ Completed  
**Тип:** Module / Media / Storage / Processing / Derivatives / Delivery

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

---

# 1. Назначение модуля

Media Library — единый технический реестр файлов и медиаисточников, которыми управляет платформа.

Модуль отвечает за:

- загрузку файлов;
- server-side import;
- безопасный ingest;
- определение типа;
- сохранение immutable original;
- checksum;
- technical metadata;
- processing state;
- derivatives;
- thumbnails;
- web variants;
- PDF variants;
- video/audio processing;
- usage graph;
- archive;
- dependency-aware purge;
- private/public delivery;
- stable managed media URLs;
- integrity;
- background processing;
- recovery/rebuild.

---

# 2. Главная доктрина

> **Media Library владеет файлом как техническим объектом, но не профессиональным смыслом его использования.**

Canonical:

```text
IMMUTABLE ORIGINAL
       ↓
MEDIA ASSET
       ↓
DERIVATIVES
       ↓
DOMAIN RELATIONS
 ┌─────┼─────┬─────┬─────┐
 ▼     ▼     ▼     ▼     ▼
Portfolio Emotional Role QNR Social
```

---

# 3. Fundamental separation

```text
Binary file
≠
MediaAsset
≠
PortfolioItem
≠
RoleMedia
≠
Emotional photo
≠
Questionnaire item
```

Один `MediaAsset` может использоваться в нескольких разрешённых контекстах без копирования исходного файла.

---

# 4. Module identifiers

Используются:

```text
MED-*
MED-ING-*
MED-TYPE-*
MED-ORG-*
MED-META-*
MED-DER-*
MED-USG-*
MED-DEL-*
MED-ARC-*
MED-PURGE-*
MED-SEC-*
MED-INV-*
MED-AP-*
E2E-MED-*
```

---

# 5. Ownership

Media Library является владельцем:

```text
original binary identity
verified MIME
checksum
file size
technical metadata
processing lifecycle
storage locator
derivatives
technical archive state
```

---

# 6. Media Library не владеет

```text
portfolio category
“крупный план”
“в полный рост”
роль
проект
дата эмоциональной съёмки
Grid crop confirmation
questionnaire visibility
professional link description
social caption
```

Это данные consuming domains.

---

# 7. Physical model

Основные сущности:

```text
MediaAsset
MediaDerivative
```

Основные таблицы:

```text
media_assets
media_derivatives
```

Дополнительные relation tables принадлежат consuming domains.

---

# 8. Canonical MediaAsset types

```text
IMAGE
VIDEO
AUDIO
DOCUMENT
```

---

# 9. IMAGE

Типичные применения:

- Main Portfolio;
- Emotional Portfolio;
- Projects/Roles;
- Achievements/Training attachment where supported;
- Questionnaire;
- Social Publishing;
- Theme decorative asset where allowed.

---

# 10. VIDEO

Типичные применения:

- видеовизитка;
- showreel;
- project video;
- professional video materials;
- social publishing.

---

# 11. AUDIO

Типичные применения:

- voice sample;
- audio portfolio;
- pronunciation;
- professional audio material.

---

# 12. DOCUMENT

Типичные применения:

- source/supporting document;
- Casting input;
- certificates where explicitly supported;
- internal materials.

Public generated Questionnaire PDF itself является `Derived Artifact`, а не обычным manually uploaded MediaAsset.

---

# 13. Source kinds

Recommended logical source kinds:

```text
UPLOAD
SERVER_IMPORT
MIGRATION
MANAGED_REMOTE_IMPORT
SYSTEM_GENERATED_SOURCE
```

`SYSTEM_GENERATED_SOURCE` используется только там, где artefact действительно становится новым controlled source по отдельной documented workflow.

---

# 14. External professional URL distinction

Ключевое правило:

> **Ссылка на YouTube/Vimeo/аудиосервис/внешний документ сама по себе обычно является `ProfessionalLink`, а не `MediaAsset`.**

---

# 15. MED-INV-001 — Remote URL ≠ Immutable Original

Remote resource может:

- измениться;
- исчезнуть;
- сменить содержимое;
- требовать authentication;
- иметь неизвестную лицензию/ownership.

Поэтому простой URL нельзя считать локальным immutable original.

---

# 16. Managed remote import

Если пользователь явно импортирует внешний файл в Media Library:

```text
remote URL
   ↓
server-side validation
   ↓
safe controlled download
   ↓
checksum
   ↓
immutable local original
```

После успешного import системой управляется собственная immutable копия.

---

# 17. MED-INV-002 — No Hybrid Ambiguity

Каждый URL-based input должен однозначно закончиться как:

```text
ProfessionalLink
```

или:

```text
managed imported MediaAsset
```

Не допускается неопределённый объект «MediaAsset, который на самом деле просто внешняя ссылка».

---

# 18. Existing URL workflows

UI MAY offer:

```text
Добавить ссылку
Импортировать файл по URL
```

как две разные операции.

---

# 19. Upload lifecycle

Canonical:

```text
RECEIVED
↓
STORED
↓
QUEUED
↓
PROCESSING
↓
READY
```

Failure branches:

```text
REJECTED
QUARANTINED
FAILED
PARTIAL
```

---

# 20. Upload request

Client may provide only hints:

```text
filename
declared MIME
purpose/context
```

---

# 21. MED-INV-003 — Client MIME Is Untrusted

Verified MIME определяется server-side.

---

# 22. Filename

`original_filename` сохраняется для Admin convenience/provenance.

Он не используется как storage path authority.

---

# 23. MED-SEC-001 — Filename Never Controls Storage Path

Например:

```text
../../secret.jpg
```

не может выйти за managed storage namespace.

---

# 24. File size

`file_size_bytes` определяется фактическим persisted stream.

Не доверять только HTTP metadata.

---

# 25. Size limits

Limits должны быть:

- configurable;
- media-type-specific;
- enforced before/while ingest;
- documented operationally.

---

# 26. Upload staging

Файл сначала может помещаться в:

```text
TEMPORARY / STAGING
```

до завершения проверок.

---

# 27. MED-INV-004 — Unvalidated File Is Not Active MediaAsset

Он не может:

- появляться Public;
- выбираться в Portfolio;
- отправляться в Questionnaire;
- публиковаться Social.

---

# 28. MIME validation

Проверка SHOULD учитывать:

```text
file signature / magic
container structure
declared MIME
extension consistency
```

где технически возможно.

---

# 29. MIME mismatch

Mismatch должен:

```text
reject
or
quarantine
```

согласно security policy.

---

# 30. Malformed media

Файл с формально правильным extension, но повреждённой структурой, не должен считаться READY.

---

# 31. Image validation

Проверяется как минимум:

```text
decodable image
dimensions
format
orientation metadata where present
```

---

# 32. Video validation

Проверяется:

```text
container
codec discoverability
duration
resolution
audio/video stream metadata
```

где доступно.

---

# 33. Audio validation

Проверяется:

```text
container
codec
duration
channels
sample metadata
```

---

# 34. Document validation

Проверяется:

```text
actual document type
size
security policy
safe handling
```

---

# 35. SVG

SVG должен рассматриваться как потенциально executable/active content.

Он требует отдельной sanitizer/security policy или должен быть запрещён для user-uploaded public media.

---

# 36. Original immutability

После принятия `MediaAsset` его original binary immutable.

---

# 37. MED-ORG-001

Запрещено изменять original:

```text
crop
resize
rotate
compress
retouch
watermark
metadata strip
format conversion
```

in place.

---

# 38. Replacement

Если пользователь хочет заменить файл:

```text
create new MediaAsset
↓
change domain relation
↓
retain old MediaAsset if dependencies exist
```

---

# 39. MED-INV-005 — Replace ≠ Overwrite

Это критично для historical QuestionnaireRevision и GridRevision.

---

# 40. Checksum

Для original вычисляется cryptographic checksum.

---

# 41. Checksum purposes

- integrity;
- duplicate detection;
- storage verification;
- migration verification;
- derivative identity support.

---

# 42. Checksum ≠ user intent

Два одинаковых файла могут быть намеренно загружены в разные моменты.

---

# 43. MED-INV-006 — Checksum Dedup != Request Idempotency

Checksum может помочь предложить reuse, но не должен автоматически схлопывать distinct business intent без policy.

---

# 44. Duplicate detection

Media Library SHOULD detect:

```text
exact binary duplicate
possible visual near-duplicate
```

как разные классы.

---

# 45. Exact duplicate

Определяется checksum.

---

# 46. Exact duplicate UX

Система может предложить:

```text
Этот файл уже есть в медиатеке.
Использовать существующий?
```

---

# 47. Automatic dedup

Допускается только если lifecycle/provenance rules явно поддерживают это.

---

# 48. Near-duplicate

Computer vision/perceptual hash MAY использоваться как assistive feature.

---

# 49. MED-INV-007 — Near-Duplicate Suggestion Is Not Deletion Authority

AI/CV не может автоматически удалять профессиональные фото как «дубликат».

---

# 50. Technical metadata

Media Library может хранить:

### Image
```text
width
height
orientation
color profile
format
```

### Video
```text
width
height
duration
codec
frame rate
audio streams
```

### Audio
```text
duration
codec
sample rate
channels
```

### Document
```text
page count where safe
format
```

---

# 51. Technical metadata ≠ professional metadata

EXIF camera data не является автоматически:

```text
shooting_date
project date
portfolio category
```

---

# 52. MED-META-001 — EXIF Date Is Not Emotional Shooting Date

Admin может использовать EXIF как подсказку.

Human подтверждает business date.

---

# 53. GPS metadata

GPS should not be exposed publicly by default.

---

# 54. Public derivatives

Unneeded private metadata SHOULD be stripped.

Especially:

```text
GPS
device identifiers
unnecessary EXIF
```

---

# 55. Original metadata

Original can retain source metadata according to storage/privacy policy because original immutable.

---

# 56. Rotation/orientation

Renderer must honor orientation correctly.

---

# 57. Normalized derivative

Derived web/PDF versions MAY normalize orientation without changing original.

---

# 58. Derivatives doctrine

Derivative:

> **детерминированно воспроизводимое представление immutable original.**

---

# 59. Canonical derivative categories

```text
THUMBNAIL
WEB_SMALL
WEB_MEDIUM
WEB_LARGE
PDF
POSTER
AUDIO_PREVIEW
VIDEO_PREVIEW
CUSTOM_CONTEXT
```

---

# 60. CUSTOM_CONTEXT

Используется только с explicit transform spec.

Например:

```text
Emotional Grid source render
social platform-specific derivative
```

---

# 61. Derivative identity

Canonical:

```text
MediaAsset checksum/id
+
derivative type
+
transform spec
+
processor version
```

---

# 62. `transform_hash`

Deterministic hash normalized transform specification.

---

# 63. MED-DER-001 — Derivative Is Rebuildable

Удаление derivative не должно уничтожить Source.

---

# 64. MED-DER-002 — Derivative Never Becomes Master Automatically

---

# 65. Processor version

Обязателен для reproducibility/migrations.

---

# 66. Example

```text
media = M123
type = WEB_MEDIUM
transform = contain 1600x1600
processor = v4
```

однозначно идентифицирует output intent.

---

# 67. Image derivative default

Для Main Portfolio baseline:

```text
preserve source aspect ratio
contain
no crop
```

если consuming context не задаёт explicit crop.

---

# 68. MED-INV-008 — Default Portfolio Rendering Must Not Crop Actress

Согласуется с existing Admin requirement:

> оригинальное соотношение сторон / полный исходный кадр / без crop.

---

# 69. Contextual crop

Разрешён только когда owning module имеет explicit crop configuration.

Основной пример:

```text
Emotional Grid cell
```

---

# 70. Contextual crop data ownership

Crop configuration принадлежит consuming module.

Media Library только выполняет deterministic transform.

---

# 71. MED-INV-009 — Media Library Does Not Invent Crop

---

# 72. Appearance protection

Разрешены:

```text
crop
pan
scale
orientation normalization
format conversion
compression
transcoding
```

---

# 73. Appearance mutation запрещена

Нельзя использовать Media Library processing для:

```text
face reshape
body reshape
skin editing
beautification
eye modification
generative enhancement
appearance-changing background replacement
```

---

# 74. Quality improvement

Техническая оптимизация разрешена, если не меняет внешность:

```text
codec conversion
safe compression
resolution adaptation
orientation fix
```

---

# 75. AI enhancement

Generative/photo-beautification processing не входит в baseline.

---

# 76. Image source aspect ratio

Original dimensions сохраняются.

---

# 77. Public responsive variants

May include multiple widths for responsive browser loading.

---

# 78. Browser `object-fit`

Default professional portfolio usage:

```text
contain
```

unless contextual composition explicitly requests crop.

---

# 79. PDF derivative

Must preserve intended framing.

Particularly:

```text
Full Body
```

не должен быть случайно cropped.

---

# 80. Thumbnail exception

Admin grid thumbnail MAY crop only when clearly a navigation preview and not professional output, но предпочтительнее сохранить understandable framing.

Professional content display and selection preview must not misrepresent source.

---

# 81. Video transcoding

Media module MAY generate standardized playback derivatives.

---

# 82. Original video

Immutable.

---

# 83. Playback variant

May optimize:

```text
codec
container
bitrate
resolution
streamability
```

---

# 84. MED-DER-003 — Transcoding Does Not Change Editorial Content

No automatic:

```text
trim
reorder
add music
AI edit
```

without explicit video-editing module, which не входит baseline.

---

# 85. Poster frame

Generated derivative.

---

# 86. Poster frame selection

Can be:

```text
deterministic technical default
or Human selected timestamp
```

according to future module UX.

---

# 87. Audio processing

May generate browser-compatible playback variant/waveform preview.

---

# 88. Waveform

Derived technical artifact.

Not Source.

---

# 89. Document preview

May generate safe thumbnail/preview when supported.

---

# 90. Generated Questionnaire PDF

Belongs Document Artifact subsystem, not MediaAsset original collection.

---

# 91. Processing states

Recommended derivative states:

```text
QUEUED
PROCESSING
READY
FAILED
STALE
```

---

# 92. Media source processing

Recommended:

```text
STORED
QUEUED
PROCESSING
READY
PARTIAL
FAILED
QUARANTINED
```

---

# 93. PARTIAL

Original safely stored, but one or more optional derivatives failed.

---

# 94. READY semantics

Required technical processing for normal intended use completed.

---

# 95. MED-INV-010 — READY Must Be Context-Aware

A MediaAsset may be READY generally, while a specific derivative is still processing.

Consuming module must require its exact needed derivative.

---

# 96. Background jobs

Typical:

```text
MEDIA_METADATA_EXTRACT
MEDIA_IMAGE_DERIVE
MEDIA_VIDEO_TRANSCODE
MEDIA_AUDIO_TRANSCODE
MEDIA_POSTER_GENERATE
MEDIA_DOCUMENT_PREVIEW
MEDIA_INTEGRITY_CHECK
MEDIA_PURGE
```

---

# 97. Job idempotency

Derivative jobs use deterministic derivative identity.

---

# 98. Duplicate worker delivery

Must not create duplicate outputs/records.

---

# 99. Stale worker completion

Worker must confirm attempt/processor/source identity before marking current derivative state.

---

# 100. Processor upgrade

New processor version may produce new derivative.

Old derivative can remain until replaced/cleanup.

---

# 101. Usage graph

Media Library MUST support answering:

> **Где используется этот файл?**

---

# 102. Usage graph sources

At least:

```text
Main Portfolio
Emotional Session
Emotional Grid Draft
Emotional Grid Revision
Project/Role
Questionnaire Draft
Questionnaire Revision
Builder Session/Snapshot
Social Post
Theme
Feedback/Casting where relevant
```

---

# 103. Usage classification

```text
CURRENT
DRAFT
HISTORICAL
PRIVATE_OPERATIONAL
DERIVED
```

---

# 104. MED-USG-001 — Usage Graph Is Referential, Not Duplication

---

# 105. Admin Media detail

Should show:

```text
technical metadata
processing status
usage count
usage contexts
archive state
available derivatives
integrity
```

---

# 106. Usage link

Admin should navigate to owning entity where authorized.

---

# 107. Historical usage

Must be visually distinguishable from current usage.

---

# 108. Why

User must understand why hard delete is blocked even when photo disappeared from current portfolio.

---

# 109. Media Library Admin UX

Recommended sections:

1. Library grid/list;
2. filters;
3. upload/import;
4. asset detail;
5. usage;
6. processing;
7. archive;
8. dependency-aware purge.

---

# 110. Library filters

At minimum:

```text
media type
processing state
archive state
usage state
date uploaded/imported
```

Optional:

```text
dimensions
duration
source kind
duplicate status
```

---

# 111. Search

Admin Media Search MAY index:

```text
filename
safe admin label
technical metadata
related usage labels
```

according to Admin Search policy.

---

# 112. Public Search

Raw MediaAsset is generally not independent Public Search result.

Owning public entities are indexed.

---

# 113. MED-INV-011 — File Existence Does Not Create Public Page

---

# 114. Media labeling

Technical library may support Admin-only label/title if useful.

But professional presentation captions belong owning modules.

---

# 115. Why

Same photo may need different contextual captions.

---

# 116. Upload target context

Admin may upload from inside:

```text
Portfolio
Emotional Session
Project/Role
Social
```

---

# 117. Underlying flow

Still:

```text
create MediaAsset
↓
process
↓
create owning-domain relation
```

---

# 118. MED-INV-012 — Context Upload Uses Same Media Library

Не создавать отдельные upload systems per module.

---

# 119. Direct Media Library upload

May create unattached MediaAsset.

---

# 120. Unused media

Valid state.

Should appear as:

```text
unused
```

not automatically deleted.

---

# 121. Orphan terminology

An unattached but valid MediaAsset is **not** an orphan.

---

# 122. True orphan

Binary exists with no DB owner/metadata, or broken referential state.

---

# 123. Server import

Product supports selecting/indexing files already on managed server.

---

# 124. Server import doctrine

> Server filesystem is an untrusted ingest source, not an automatic public library.

---

# 125. Import flow

```text
Admin selects/requests managed source
↓
server validates allowed root
↓
safe path resolution
↓
file validation
↓
copy/move into canonical immutable storage
↓
checksum
↓
MediaAsset
```

---

# 126. MED-SEC-002 — No Arbitrary Filesystem Browsing

Admin UI cannot read arbitrary host filesystem.

Only configured import roots.

---

# 127. Path traversal

Rejected.

---

# 128. Symlink handling

Must not allow escape from configured import root.

---

# 129. Import copy vs move

Baseline recommendation:

```text
copy into canonical managed immutable storage
```

Then source import area remains outside MediaAsset authority.

---

# 130. Why copy

Avoid dependence on external directory lifecycle.

---

# 131. Migration

Legacy media migration:

```text
validate
checksum
preserve original bytes
map provenance
create MediaAsset
```

---

# 132. MED-INV-013 — Migration Does Not Recompress Originals

Unless source is unreadable/corrupt and explicit remediation is documented.

---

# 133. URL import

Managed remote download requires:

```text
approved scheme
SSRF protection
redirect policy
size bound
MIME validation
timeout
checksum
```

---

# 134. MED-SEC-003 — Remote Import Is SSRF-Sensitive

Must block:

```text
localhost
private/internal network
metadata service
file://
admin internal URLs
```

unless explicitly trusted infrastructure path exists outside public feature.

---

# 135. Redirects

Each redirect target must be revalidated.

---

# 136. Content length

Do not rely solely on remote `Content-Length`.

Stream must enforce actual byte limit.

---

# 137. Remote authentication

Baseline public remote import should not accept arbitrary credentials embedded in URL.

---

# 138. External embeds

YouTube/Vimeo/etc. belong Professional Links/Media Links module unless explicitly downloaded under permitted workflow.

---

# 139. Storage zones

Logical:

```text
ORIGINAL_PRIVATE
DERIVED_PUBLIC
DERIVED_PRIVATE
OPERATIONAL_PRIVATE
TEMPORARY
```

---

# 140. Original storage

Originals private by default.

---

# 141. MED-DEL-001 — Original Is Not Served by Direct Filesystem URL

---

# 142. Public derivative

Can be served through:

```text
managed route
reverse proxy
object-storage abstraction
CDN abstraction
```

as long as access semantics are preserved.

---

# 143. Stable public URL

Needed where local published media is referenced externally or through QR.

---

# 144. Stable URL doctrine

URL should identify application-managed public media/resource, not underlying disk path.

---

# 145. Public URL example conceptually

```text
https://kate.supremevision.org/media/<opaque-public-id>/<variant>
```

Exact route deferred implementation.

---

# 146. Historical URL

Historical documents should use stable resource route or frozen public/casting-safe link semantics.

---

# 147. Private delivery

Feedback/Casting/private document uses authenticated route.

---

# 148. Private media flow

```text
request
↓
authenticate
↓
authorize owning entity
↓
resolve Media/storage internally
↓
stream
```

---

# 149. MED-DEL-002 — Asset ID Alone Does Not Grant Download

---

# 150. Public derivative authorization

If derivative is current-public:

server/public routing may allow.

If visibility revoked:

delivery must cease despite binary cache existence.

---

# 151. Cache

Media derivatives are immutable by identity.

Aggressive immutable caching is safe when URL identity is versioned/content-addressed appropriately.

---

# 152. Current aliases

May resolve to current eligible derivative.

Must invalidate on visibility/current relation changes.

---

# 153. MED-INV-014 — Immutable Derivative URL ≠ Current Visibility Grant

A raw immutable artifact route must still be designed so private/withdrawn content does not remain unintentionally accessible.

---

# 154. Privacy-sensitive media

Prefer protected delivery or non-guessable app-controlled resource with current policy enforcement where revocation matters.

---

# 155. Portfolio integration

Main Portfolio stores relation:

```text
PortfolioItem
→ MediaAsset
```

---

# 156. Portfolio category

Owned by Portfolio.

---

# 157. Primary status

Owned by Portfolio.

---

# 158. Public portfolio visibility

Owned by Portfolio relation/policy.

---

# 159. Media Library responsibility

Ensure appropriate image derivative is available.

---

# 160. Emotional Portfolio integration

Session owns membership.

```text
EmotionalSession
→ EmotionalSessionMedia
→ MediaAsset
```

---

# 161. Shooting date

Not in MediaAsset.

---

# 162. Emotional Grid integration

Grid cell references MediaAsset + contextual crop.

---

# 163. Crop data

Not stored as default crop on MediaAsset.

---

# 164. MED-INV-015 — Grid Crop Never Mutates Original

---

# 165. Emotional final derivative

Composite Grid output is Derived Artifact from exact GridRevision + Media originals.

It does not replace source photos.

---

# 166. Project/Role integration

```text
RoleMedia
→ MediaAsset
```

---

# 167. Role photo limits

Owned by Projects module.

Media Library does not enforce `max 5` globally.

---

# 168. Home “В образе”

Owned by RoleMedia configuration.

---

# 169. Questionnaire integration

Questionnaire references eligible owning-domain items/media.

---

# 170. Questionnaire Revision

Freezes exact selected media identity/representation.

---

# 171. PDF rendering

Uses exact approved derivative/source metadata from Revision.

---

# 172. MED-INV-016 — PDF Renderer Cannot Swap to New Current Photo

Historical render uses exact frozen selection.

---

# 173. Public Builder

Builder chooses eligible Portfolio/etc. items, not unrestricted MediaAsset inventory.

---

# 174. MED-INV-017 — Public Builder Does Not Browse Raw Media Library

This prevents bypassing domain eligibility.

---

# 175. Social Publishing

SocialPost may reference existing MediaAssets.

---

# 176. Social-specific derivative

May be generated from immutable original according to platform requirements.

---

# 177. Social derivative must not alter actress appearance

Only technical/platform formatting.

---

# 178. Social approval

Exact MediaAsset/version/configuration bound to approved post version.

---

# 179. Theme integration

Theme decorative assets MAY use Media Library.

---

# 180. Professional actress photos

Theme AI cannot generate altered appearance version from media.

---

# 181. Feedback attachments

Private attachments may use related storage subsystem patterns but must not automatically enter reusable professional Media Library.

---

# 182. Important distinction

A casting/feedback attachment may be:

```text
private operational file
```

not professional reusable MediaAsset.

---

# 183. Promotion to Media Library

If later needed, must be explicit Human action with validation.

---

# 184. MED-INV-018 — Private Attachment ≠ Public MediaAsset

---

# 185. Casting source

Same.

---

# 186. AI integration

AI may consume selected MediaAsset only when specific capability requires it.

---

# 187. Current AI uses

Potential:

```text
image classification suggestion
duplicate suggestion
Emotional Grid crop suggestion
Casting source image analysis
```

---

# 188. AI classification

Can suggest:

```text
close_up
full_body
profile
```

but Portfolio category remains Human-controlled unless module explicitly supports confirm workflow.

---

# 189. MED-INV-019 — AI Classification Is Recommendation

---

# 190. AI cannot

```text
retouch
beautify
reshape
replace actress appearance
publish automatically
delete “bad” photo
```

---

# 191. Face analysis

For Emotional Grid:

```text
face detection
occupancy estimate
crop suggestion
```

allowed as assistive analysis.

Human confirms final cell.

---

# 192. Media Library AI privacy

Only selected media and required technical context go to AI provider.

---

# 193. No bulk provider upload

Do not automatically send entire Media Library to AI.

---

# 194. Provider retention

External AI provider handling of image data governed security/AI operations policy.

---

# 195. VOP integration

VOP may identify:

```text
processing failure
missing derivative
duplicate candidate
unused media
broken storage reference
orphan binary
historical dependency
```

---

# 196. VOP safe actions

Can:

```text
retry derivative
rebuild thumbnail
re-run metadata extraction
verify checksum
clear stale derived artifact
```

according to allowlist.

---

# 197. VOP cannot

```text
hard-delete professional original
replace photo
publish photo
change Portfolio category
```

automatically.

---

# 198. Integrity checks

System SHOULD support periodic integrity verification.

---

# 199. Integrity comparison

```text
stored checksum
vs
actual binary checksum
```

---

# 200. Integrity mismatch

Critical operational issue.

Do not silently recalculate and accept new checksum.

---

# 201. MED-INV-020 — Checksum Mismatch Does Not Redefine Original

It indicates corruption/change.

---

# 202. Missing original

Critical.

---

# 203. Response

- mark affected asset problematic;
- preserve metadata/history;
- notify Admin/VOP;
- attempt restore from backup;
- never substitute another photo automatically.

---

# 204. Missing derivative

Non-critical if source exists.

Regenerate.

---

# 205. Backup

Originals MUST be part of backup strategy.

---

# 206. Derivatives

May be excluded from backup if confidently rebuildable, except historical artefacts requiring byte preservation.

---

# 207. Restore

Must restore:

```text
MediaAsset IDs
original bytes
checksums
storage mapping
metadata
relations
```

---

# 208. Processor rebuild

After restore derivatives can be regenerated.

---

# 209. Migration integrity

File count alone insufficient.

Verify:

```text
asset records
binary presence
checksum
usage references
```

---

# 210. Archive

MediaAsset archive prevents new usage but does not erase binary.

---

# 211. Archive is not same as domain content archive

Archiving a PortfolioItem does not necessarily archive shared MediaAsset.

---

# 212. MED-ARC-001 — Media Archive Requires Usage Awareness

If MediaAsset still used elsewhere, archive semantics need explicit handling.

---

# 213. Recommended

MediaAsset archive means:

> no new professional relations can be created.

Existing current relationships SHOULD either block archive or require explicit handling.

---

# 214. Safer baseline

If current active usages exist:

```text
Archive MediaAsset
→ blocked
```

until current relations are removed/archived.

---

# 215. Historical usages

Do not necessarily block technical archive.

They block destructive purge where required.

---

# 216. Purge

Hard physical removal is exceptional.

---

# 217. Purge requirements

All required:

```text
no current usage
no draft usage
historical dependencies resolved
operational dependencies resolved
retention satisfied
no hold
authorized destructive command
```

---

# 218. Purge workflow

```text
REQUEST
↓
DEPENDENCY ANALYSIS
↓
APPROVED
↓
PURGE_PENDING
↓
binary deletion
↓
verification
↓
metadata/tombstone finalization
```

---

# 219. MED-PURGE-001 — Never DB Delete First

---

# 220. Storage deletion failure

State remains:

```text
FAILED / PURGE_PENDING
```

not `PURGED`.

---

# 221. Purge idempotency

Repeated same purge intent returns existing workflow/state.

---

# 222. Tombstone

May preserve:

```text
asset ID
checksum
deleted_at
reason
```

if required for provenance.

---

# 223. Derived cleanup

When original purged:

all derivatives should be removed/reconciled.

---

# 224. Public cache cleanup

Any public delivery aliases invalidated before/at destructive purge.

---

# 225. Search cleanup

Owning entities handle search removal; raw media not generally public indexed.

---

# 226. Retention

Media original normally `R3 PROFESSIONAL_HISTORY`.

---

# 227. Unused originals

May have retention cleanup policy, but should not be auto-purged simply for being unused unless clearly intentional/staged.

---

# 228. Upload abandoned before finalization

Staging file uses short R0 retention.

---

# 229. Temporary derivatives

R6 rebuildable.

---

# 230. Processing failures

Failed derivative attempts use operational retention.

---

# 231. Security

Original/public/private stores must be logically separated.

---

# 232. Provider credentials

Never stored in MediaAsset.

---

# 233. Remote import credential

If future authenticated remote import exists, credential stored via secret infrastructure, not URL/Media table.

---

# 234. File serving headers

Must use safe:

```text
Content-Type
Content-Disposition
cache policy
nosniff where appropriate
```

---

# 235. Executable document risk

Download behavior should avoid accidental inline execution of unsafe types.

---

# 236. Public images/videos/audio

Only allowlisted safe media formats.

---

# 237. Download filenames

Sanitized.

---

# 238. Access logs

May record:

```text
asset ID
variant
status
request/correlation ID
```

without private payload.

---

# 239. Media URLs in logs

Token/private query parameters must be redacted.

---

# 240. Public metadata

Do not expose:

```text
original filesystem path
raw EXIF GPS
checksum unless needed
internal processor version
```

---

# 241. Admin metadata

Can expose checksum/processor diagnostics where useful, but storage locator remains restricted.

---

# 242. Commands

Canonical:

```text
CreateMediaUpload
FinalizeMediaUpload
ImportMediaFromServer
ImportMediaFromRemoteUrl
RetryMediaProcessing
RequestMediaDerivative
ArchiveMediaAsset
RestoreMediaAsset
RequestMediaPurge
CancelMediaPurge
```

where applicable.

---

# 243. Queries

```text
GetMediaLibrary
GetMediaAsset
GetMediaUsage
GetMediaProcessingStatus
GetMediaDuplicates
GetMediaDerivatives
GetMediaPurgeReadiness
```

---

# 244. CreateMediaUpload

Returns upload/asset intent according to transport design.

---

# 245. FinalizeMediaUpload

Server verifies actual staged content.

Client cannot set:

```text
checksum
verified MIME
READY
storage locator
```

---

# 246. ImportMediaFromServer

Requires configured import root.

---

# 247. ImportMediaFromRemoteUrl

Requires SSRF-safe fetch policy.

---

# 248. RequestMediaDerivative

Caller specifies allowed semantic derivative intent, not arbitrary shell/ffmpeg arguments.

---

# 249. MED-SEC-004 — No Arbitrary Processor Arguments From Client

---

# 250. ArchiveMediaAsset

Checks active usage.

---

# 251. RequestMediaPurge

Returns dependency analysis/purge workflow.

---

# 252. Query contract — Library item

Admin conceptually:

```text
MediaLibraryItemDTO {
  id
  mediaType
  originalFilename
  mimeType
  fileSizeBytes
  dimensions?
  duration?
  processingState
  lifecycleState
  preview
  usageSummary
  createdAt
}
```

---

# 253. Asset detail DTO

Adds:

```text
sourceKind
technicalMetadata
checksum
derivatives
usage
integrityState
```

where authorized.

---

# 254. Still excluded

```text
raw storage credentials
filesystem path
provider secrets
```

---

# 255. Public Media DTO

As DOC-101:

```text
url
width
height
alt
variants?
```

Only context-safe information.

---

# 256. Processing result

Async operations return MediaAsset/Derivative status, not raw BackgroundJob internals.

---

# 257. Error codes

At least:

```text
FILE_TOO_LARGE
FILE_TYPE_UNSUPPORTED
FILE_CONTENT_INVALID
FILE_SECURITY_REJECTED
FILE_QUARANTINED
MEDIA_NOT_READY
MEDIA_PROCESSING_FAILED
MEDIA_SOURCE_MISSING
MEDIA_DERIVATIVE_NOT_READY
MEDIA_DERIVATIVE_FAILED
MEDIA_USAGE_CONFLICT
MEDIA_DELETE_BLOCKED_BY_DEPENDENCIES
MEDIA_INTEGRITY_FAILED
MEDIA_REMOTE_IMPORT_BLOCKED
MEDIA_REMOTE_IMPORT_FAILED
MEDIA_SERVER_IMPORT_PATH_INVALID
```

---

# 258. Retryability

Examples:

```text
MEDIA_PROCESSING_FAILED due transient worker
→ potentially retryable
```

```text
FILE_TYPE_UNSUPPORTED
→ not retryable without different file
```

---

# 259. Public failure

Owning module should degrade gracefully where possible.

Example one gallery image processing fails:

do not crash entire public Profile.

---

# 260. Video unavailable

Video module can show unavailable/no CTA while other Profile content works.

---

# 261. Optional processor outage

Does not prevent editing unrelated Profile facts.

---

# 262. Admin readiness

Media Library should clearly identify:

```text
ready
processing
failed
archived
blocked for purge
```

---

# 263. Media usage readiness

Owning modules determine semantic eligibility.

Media Library reports technical readiness only.

---

# 264. MED-INV-021 — Technical READY ≠ Professional Eligible

An IMAGE may be perfectly READY but not qualify as Main Portfolio Close-Up.

---

# 265. Publication

Media Library does not have universal `published=true` Source authority.

Publication generally belongs to relation/context.

---

# 266. Why

One photo can be:

```text
public in Portfolio
private in another relation
historical in Questionnaire
```

---

# 267. MED-INV-022 — Publicness Is Contextual

---

# 268. Same asset multi-context example

```text
MediaAsset M1
├─ PortfolioItem — public
├─ SocialPost Draft — admin
└─ QuestionnaireRevision R2 — historical
```

No contradiction.

---

# 269. Deleting relation

Deleting/archiving PortfolioItem leaves M1 for other usages.

---

# 270. Derived cache identity

Immutable derivative should preferably have immutable cache key/URL.

---

# 271. Browser cache busting

Processor/source identity change yields new derivative URL/key.

---

# 272. No mutable binary behind permanent immutable URL

Avoid replacing bytes at same immutable-addressed path.

---

# 273. CDN

Not baseline requirement.

Architecture must permit adding it without changing MediaAsset identity.

---

# 274. Storage backend abstraction

Filesystem initial implementation acceptable.

Domain must not depend on literal:

```text
/data/kate-actor/...
```

---

# 275. Self-hosted deployment

Current target storage can reside under managed `/data/kate-actor` hierarchy.

---

# 276. Storage backend interface

Conceptually:

```text
putOriginal
openOriginal
deleteOriginal
putDerivative
openDerivative
deleteDerivative
exists
```

---

# 277. Domain never calls filesystem directly

Infrastructure adapter only.

---

# 278. Atomic storage ingest

Temporary write → fsync/verification → canonical finalization where supported.

---

# 279. Interrupted upload

Must not create phantom READY file.

---

# 280. Disk full

Returns storage failure.

No incomplete asset marked READY.

---

# 281. Reprocessing

Admin may request reprocessing.

---

# 282. Reprocess original

Original untouched.

New metadata/derivatives generated according to current processor.

---

# 283. Historical derivative preservation

If old published document depends on exact existing binary artefact, document artifact remains independent.

Media derivative can usually regenerate from frozen source identity.

---

# 284. Processor migration

Do not bulk rewrite originals.

---

# 285. Metrics

Recommended:

```text
media_assets_total
media_bytes_total
media_processing_queue
media_processing_failed
media_derivatives_total
media_unused_total
media_integrity_failures
media_purge_backlog
media_duplicate_candidates
```

---

# 286. Operational alerts

Examples:

```text
original missing
checksum mismatch
storage near capacity
processing final failure
purge repeatedly failing
orphan count increase
```

---

# 287. VOP “what needs action today”

May surface:

```text
3 files failed processing
2 media assets have broken references
5 exact duplicate uploads need review
```

---

# 288. No aesthetic scoring

Media Library/VOP must not score:

```text
beauty
attractiveness
body quality
“good/bad face”
```

---

# 289. Professional technical checks allowed

Examples:

```text
resolution low
image corrupt
face occupancy insufficient for requested Grid cell
required Full Body classification unconfirmed
```

---

# 290. Accessibility

Media UI must provide text labels for:

- processing state;
- file type;
- actions;
- errors.

---

# 291. Keyboard navigation

Library selection/usage should be keyboard-accessible.

---

# 292. Preview

Do not force crop that prevents Admin from verifying source framing.

---

# 293. Original view

Admin should have ability to inspect full source frame.

---

# 294. MED-INV-023 — Admin Preview Must Not Misrepresent Original

---

# 295. Localization

Technical status localized in UI.

Technical enum remains language-neutral.

---

# 296. Original filenames

May be any supported Unicode after safe normalization.

---

# 297. Public filenames

Should use safe generated naming, not expose sensitive original names by default.

---

# 298. Download filename

Can be human-friendly and sanitized.

---

# 299. Security scanning

Architecture SHOULD permit malware/content scanning adapter for documents/uploads.

Exact engine optional.

---

# 300. Quarantine

Quarantined file:

```text
not selectable
not public
not downloadable through normal path
```

except restricted diagnostic workflow.

---

# 301. MED-INV-024 — Quarantine Cannot Be Bypassed by Known Asset ID

---

# 302. Server import quarantine

Same security rules as upload.

---

# 303. Migration quarantine

Legacy source is not trusted merely because it existed previously.

---

# 304. Remote import quarantine

Same.

---

# 305. Usage graph consistency

Relations use FK/application validation.

Reconciliation detects missing targets.

---

# 306. Same-profile

Professional MediaAsset relation must belong same Actor Profile unless global/admin asset context explicitly says otherwise.

---

# 307. MED-INV-025 — Cross-Profile Media Injection Rejected

---

# 308. Future multi-profile readiness

MediaAsset ownership remains explicit `profile_id`.

---

# 309. Sharing across profiles

Not baseline.

Would require explicit shared asset architecture, not removing ownership checks casually.

---

# 310. Audit

Significant operations:

```text
archive
restore
purge request
purge complete
security quarantine override
server/remote import
```

SHOULD be audited.

---

# 311. Routine derivative creation

Does not require business Audit.

Operational logs sufficient.

---

# 312. Upload Audit

Ordinary upload may be traceable through created_by/technical events without full business Audit depending final security policy.

---

# 313. Binary content in Audit

Never.

---

# 314. Search indexing

Media technical admin index may be rebuildable.

---

# 315. Cache

Thumbnail/derivative cache entirely rebuildable.

---

# 316. Analytics

May record:

```text
media_uploaded
media_selected
video_started
```

where useful.

Do not include private original filename or storage path unnecessarily.

---

# 317. Notifications

Could notify Admin about:

```text
processing final failure
storage/integrity problem
```

according to policy.

---

# 318. No notification per successful thumbnail

Avoid noise.

---

# 319. Anti-patterns

---

# 320. MED-AP-001

**Overwrite original file when user crops image.**

---

# 321. MED-AP-002

**Save Portfolio category inside MediaAsset.**

---

# 322. MED-AP-003

**Save Emotional shooting date from EXIF automatically.**

---

# 323. MED-AP-004

**Every upload gets public URL immediately.**

---

# 324. MED-AP-005

**Storage path is returned to browser.**

---

# 325. MED-AP-006

**Original file is served directly from `/data/...`.**

---

# 326. MED-AP-007

**Filename controls destination path.**

---

# 327. MED-AP-008

**Trust browser MIME.**

---

# 328. MED-AP-009

**Trust file extension.**

---

# 329. MED-AP-010

**Remote URL is stored as immutable MediaAsset without importing bytes.**

---

# 330. MED-AP-011

**Arbitrary remote URL fetch with no SSRF protection.**

---

# 331. MED-AP-012

**Arbitrary server filesystem browser/import.**

---

# 332. MED-AP-013

**Near-duplicate AI automatically deletes image.**

---

# 333. MED-AP-014

**Exact checksum duplicate automatically means same user intent.**

---

# 334. MED-AP-015

**Portfolio relation removal deletes MediaAsset.**

---

# 335. MED-AP-016

**Archive Project cascades binary deletion.**

---

# 336. MED-AP-017

**Grid crop stored as MediaAsset global crop.**

---

# 337. MED-AP-018

**Thumbnail crop becomes professional source framing.**

---

# 338. MED-AP-019

**Full Body PDF image uses arbitrary cover crop.**

---

# 339. MED-AP-020

**Generative beauty enhancement as ordinary derivative.**

---

# 340. MED-AP-021

**Transcoder edits/trims video automatically.**

---

# 341. MED-AP-022

**Background worker creates duplicate derivative records per retry.**

---

# 342. MED-AP-023

**Checksum mismatch is “fixed” by replacing stored checksum.**

---

# 343. MED-AP-024

**Missing original silently replaced with another file.**

---

# 344. MED-AP-025

**Public Builder can browse raw Media Library.**

---

# 345. MED-AP-026

**Feedback attachment automatically appears in reusable professional library.**

---

# 346. MED-AP-027

**Social derivative alters actress appearance to match platform.**

---

# 347. MED-AP-028

**Theme AI modifies actress photo.**

---

# 348. MED-AP-029

**Media technical READY means automatically eligible for Questionnaire.**

---

# 349. MED-AP-030

**Single `is_public` field on MediaAsset becomes universal visibility authority.**

---

# 350. MED-AP-031

**Purge DB row first, binary later.**

---

# 351. MED-AP-032

**Hard-delete asset with historical Questionnaire dependency.**

---

# 352. MED-AP-033

**Unknown orphan binary automatically deleted.**

---

# 353. MED-AP-034

**Quarantined file selectable by direct ID.**

---

# 354. MED-AP-035

**Provider/API credentials embedded into remote URL field.**

---

# 355. MED-AP-036

**Unbounded video processing inside Server Action.**

---

# 356. MED-AP-037

**Public Search indexes original filenames as public content.**

---

# 357. MED-AP-038

**GPS EXIF leaked through public derivative.**

---

# 358. MED-AP-039

**Processor version omitted from derivative identity.**

---

# 359. MED-AP-040

**Mutable bytes served forever under same immutable cache URL.**

---

# 360. Core invariants

`MED-INV-026`  
Every managed MediaAsset has stable identity.

`MED-INV-027`  
Every accepted original has checksum.

`MED-INV-028`  
Original bytes are immutable.

`MED-INV-029`  
Replacement creates new MediaAsset.

`MED-INV-030`  
Client filename is non-authoritative.

`MED-INV-031`  
Client MIME is non-authoritative.

`MED-INV-032`  
MediaAsset type is server-validated.

`MED-INV-033`  
Unvalidated media cannot be consumed professionally.

`MED-INV-034`  
Quarantined media cannot be consumed normally.

`MED-INV-035`  
Derivatives are rebuildable.

`MED-INV-036`  
Derivative identity includes processor version.

`MED-INV-037`  
Derivatives never mutate original.

`MED-INV-038`  
Default portfolio processing preserves framing.

`MED-INV-039`  
Contextual crop requires owning-domain configuration.

`MED-INV-040`  
Media Library cannot alter actress appearance.

`MED-INV-041`  
EXIF metadata does not become business fact automatically.

`MED-INV-042`  
Public derivatives remove unnecessary sensitive metadata.

`MED-INV-043`  
External ProfessionalLink and managed MediaAsset remain distinct.

`MED-INV-044`  
Remote import must create controlled immutable copy.

`MED-INV-045`  
Remote import is SSRF-protected.

`MED-INV-046`  
Server import is restricted to approved roots.

`MED-INV-047`  
Media publicness is contextual.

`MED-INV-048`  
Raw MediaAsset existence does not imply Public availability.

`MED-INV-049`  
Private media requires authorization before delivery.

`MED-INV-050`  
Storage locator never grants client access.

`MED-INV-051`  
Stable public URLs are application-controlled.

`MED-INV-052`  
MediaAsset does not own Portfolio category.

`MED-INV-053`  
MediaAsset does not own Emotional shooting date.

`MED-INV-054`  
MediaAsset does not own Role context.

`MED-INV-055`  
Public Builder does not bypass owning-domain eligibility.

`MED-INV-056`  
Questionnaire historical render uses frozen media identity.

`MED-INV-057`  
Social usage binds selected MediaAsset explicitly.

`MED-INV-058`  
AI media analysis is recommendation/assistive unless deterministic technical metadata.

`MED-INV-059`  
AI cannot autonomously delete media.

`MED-INV-060`  
AI cannot autonomously retouch actress appearance.

`MED-INV-061`  
Usage graph distinguishes current and historical references.

`MED-INV-062`  
Removing one relation does not delete shared MediaAsset.

`MED-INV-063`  
Archive differs from Purge.

`MED-INV-064`  
Active usage blocks MediaAsset archive/purge according to policy.

`MED-INV-065`  
Historical dependency can block purge.

`MED-INV-066`  
Purge is dependency-aware.

`MED-INV-067`  
Purge is idempotent.

`MED-INV-068`  
Storage delete failure cannot produce false PURGED state.

`MED-INV-069`  
Original backup/restore preserves checksum and identity.

`MED-INV-070`  
Missing derivative can be rebuilt.

`MED-INV-071`  
Missing original cannot be silently substituted.

`MED-INV-072`  
Checksum mismatch is integrity incident.

`MED-INV-073`  
Processor retry is idempotent.

`MED-INV-074`  
Stale worker completion cannot overwrite newer processor state.

`MED-INV-075`  
Large processing is asynchronous.

`MED-INV-076`  
Cache loss causes no Source loss.

`MED-INV-077`  
Search loss causes no Source loss.

`MED-INV-078`  
Public DTO omits private technical metadata.

`MED-INV-079`  
Admin normal DTO omits raw storage credentials/path.

`MED-INV-080`  
Same-profile relation validation is mandatory.

`MED-INV-081`  
Future multi-profile sharing requires explicit architecture.

`MED-INV-082`  
Unattached valid asset is not automatically an orphan.

`MED-INV-083`  
Unknown orphan requires cautious reconciliation.

`MED-INV-084`  
Technical metadata extraction does not need Human professional approval.

`MED-INV-085`  
Professional classification does require owning-domain confirmation where semantic.

`MED-INV-086`  
Media processing remains functional without AI.

`MED-INV-087`  
No AI provider is required to display valid existing media.

`MED-INV-088`  
Document/PDF artefacts remain distinct from uploaded originals.

`MED-INV-089`  
Private operational attachment does not automatically become reusable MediaAsset.

`MED-INV-090`  
Media module remains storage-backend independent.

---

# 361. E2E-MED-001 — Image upload

Upload valid JPEG.

Expected:

```text
MediaAsset created
checksum stored
verified MIME image/jpeg
original immutable
processing begins
```

---

# 362. E2E-MED-002 — MIME spoof

Upload executable renamed `.jpg`.

Rejected/quarantined.

---

# 363. E2E-MED-003 — Filename traversal

Filename:

```text
../../secret.jpg
```

does not influence storage path.

---

# 364. E2E-MED-004 — Oversized upload

Payload exceeds configured limit.

Upload fails safely without partial active MediaAsset.

---

# 365. E2E-MED-005 — Interrupted upload

Connection drops halfway.

No phantom READY asset.

Staging residue cleaned later.

---

# 366. E2E-MED-006 — Original immutable

Create derivative/crop.

Checksum/original bytes unchanged.

---

# 367. E2E-MED-007 — Replace

Replace photo through UI.

Expected new MediaAsset ID.

Historical reference to old ID remains valid.

---

# 368. E2E-MED-008 — Exact duplicate

Upload same bytes twice.

System detects checksum match and can offer reuse.

No automatic destructive merge.

---

# 369. E2E-MED-009 — Similar image

Near-duplicate detector flags candidate.

Neither asset deleted automatically.

---

# 370. E2E-MED-010 — EXIF date

Image EXIF says July 1.

Emotional shooting date remains unset until Human confirms domain value.

---

# 371. E2E-MED-011 — GPS stripping

Original contains GPS.

Public derivative/DTO does not expose GPS.

---

# 372. E2E-MED-012 — Orientation

Portrait EXIF-orientation image renders correctly while original remains unchanged.

---

# 373. E2E-MED-013 — Portfolio contain

Portrait Portfolio image rendered in Admin/Public without automatic crop of source framing.

---

# 374. E2E-MED-014 — Full Body PDF

Full Body source remains visually full-frame in PDF derivative.

---

# 375. E2E-MED-015 — Grid crop

Grid creates contextual crop.

Opening original elsewhere shows uncropped original.

---

# 376. E2E-MED-016 — Grid rotation

Allowed contextual rotation changes Grid output only.

Original unchanged.

---

# 377. E2E-MED-017 — Appearance mutation attempt

Client requests face reshaping transform.

Rejected as unsupported transform.

---

# 378. E2E-MED-018 — Video upload

Valid video stored immutable and playback derivative generated asynchronously.

---

# 379. E2E-MED-019 — Video processor outage

Original remains stored.

Processing reaches failed/partial state.

Other site functions remain operational.

---

# 380. E2E-MED-020 — Video retry

Retry processing creates no duplicate semantic derivative.

---

# 381. E2E-MED-021 — Audio

Valid audio receives metadata/duration and playback-safe derivative where required.

---

# 382. E2E-MED-022 — Server import valid root

Import file from approved source root.

Creates canonical managed immutable copy.

---

# 383. E2E-MED-023 — Server import escape

Attempt `../` or symlink escape.

Rejected.

---

# 384. E2E-MED-024 — Remote import public file

Valid external HTTPS file passes SSRF/size/MIME checks and becomes local immutable MediaAsset.

---

# 385. E2E-MED-025 — Remote localhost

Attempt import:

```text
http://127.0.0.1/...
```

Blocked.

---

# 386. E2E-MED-026 — Remote private IP redirect

Initial public URL redirects to private address.

Blocked after redirect revalidation.

---

# 387. E2E-MED-027 — Professional URL

Add YouTube video as professional link.

It is not falsely represented as locally immutable MediaAsset.

---

# 388. E2E-MED-028 — Usage graph

One image used by Portfolio + QuestionnaireRevision.

Admin usage view shows current Portfolio + historical Questionnaire.

---

# 389. E2E-MED-029 — Remove Portfolio relation

Remove/archive PortfolioItem.

MediaAsset remains because historical usage exists.

---

# 390. E2E-MED-030 — Public Builder raw access

Attempt to submit arbitrary MediaAsset ID directly into Builder.

Rejected because Builder requires owning-domain eligible item.

---

# 391. E2E-MED-031 — Social reuse

Existing Portfolio MediaAsset selected for Social post.

No duplicate original created.

---

# 392. E2E-MED-032 — Social derivative

Platform-specific technical variant generated without appearance mutation.

---

# 393. E2E-MED-033 — Feedback attachment isolation

Upload Feedback attachment.

It does not appear in reusable Media Library/Portfolio automatically.

---

# 394. E2E-MED-034 — Casting attachment isolation

Same behavior.

---

# 395. E2E-MED-035 — Private delivery

Unauthenticated request for private attachment/media route denied.

---

# 396. E2E-MED-036 — Public DTO

Inspect Public image DTO.

Absent:

```text
storageLocator
GPS
checksum
internal processor metadata
```

---

# 397. E2E-MED-037 — Archived owning item

PortfolioItem archived.

Current Public route stops serving it as Portfolio content even though derivative binary still exists.

---

# 398. E2E-MED-038 — Asset archive active usage

Attempt archive MediaAsset with current active Portfolio usage.

Blocked or requires explicit dependency handling according to policy.

---

# 399. E2E-MED-039 — Purge current usage

Hard purge blocked.

---

# 400. E2E-MED-040 — Purge historical usage

Only historical Questionnaire dependency remains.

Default hard purge blocked pending historical policy.

---

# 401. E2E-MED-041 — Purge no usage

No current/draft/historical required references and retention satisfied.

Purge workflow can proceed.

---

# 402. E2E-MED-042 — Storage delete fails

Media not marked PURGED.

Admin sees failure/retry state.

---

# 403. E2E-MED-043 — Purge replay

Retry same purge command.

No duplicate purge workflow.

---

# 404. E2E-MED-044 — Thumbnail deletion

Delete thumbnail derivative.

Original remains.

Thumbnail regenerates.

---

# 405. E2E-MED-045 — Processor version

Generate same derivative under processor v2 after v1.

Distinct derivative identity; original same.

---

# 406. E2E-MED-046 — Worker replay

Same derivative job executed twice.

One semantic derivative result.

---

# 407. E2E-MED-047 — Stale worker

Old attempt completes after newer attempt.

Old completion cannot regress newer READY state.

---

# 408. E2E-MED-048 — Missing derivative

Public owning page requests needed derivative missing.

System can trigger/retry regeneration and degrade gracefully.

---

# 409. E2E-MED-049 — Missing original

Delete/corrupt original externally.

Integrity scanner reports critical issue.

No automatic replacement with another image.

---

# 410. E2E-MED-050 — Checksum corruption

Binary differs from checksum.

System reports integrity failure rather than updating checksum.

---

# 411. E2E-MED-051 — Restore

Restore DB + originals from backup.

Asset IDs/checksums/relations remain consistent.

---

# 412. E2E-MED-052 — Derivative rebuild after restore

Derivatives absent after restore.

System rebuilds from original without changing professional relations.

---

# 413. E2E-MED-053 — Cross-profile injection

Try relating MediaAsset from profile B into profile A Portfolio.

Rejected.

---

# 414. E2E-MED-054 — Quarantine direct ID

Known quarantined asset UUID cannot bypass selection/download rules.

---

# 415. E2E-MED-055 — AI crop suggestion

AI proposes Grid crop.

MediaAsset remains unchanged; Grid cell remains unconfirmed until Human action.

---

# 416. E2E-MED-056 — AI duplicate suggestion

AI flags duplicate.

No archive/purge occurs automatically.

---

# 417. E2E-MED-057 — AI unavailable

Disable AI service.

Upload, processing, Media Library, Portfolio viewing continue working.

---

# 418. E2E-MED-058 — VOP retry

VOP executes allowlisted retry derivative action.

No professional Source mutation occurs.

---

# 419. E2E-MED-059 — Public cache

Public immutable derivative cached.

Current Portfolio visibility revoked.

Public content route no longer grants usage/access despite cached binary architecture.

---

# 420. E2E-MED-060 — Original filename privacy

Original filename contains private information.

Public URL/download metadata does not expose it by default.

---

# 421. E2E-MED-061 — Public link stability

Published locally managed media URL remains stable across filesystem relocation.

---

# 422. E2E-MED-062 — Storage backend relocation

Move underlying storage/backend through supported migration.

Domain MediaAsset IDs and professional relations unchanged.

---

# 423. E2E-MED-063 — Duplicate relationship

Same MediaAsset cannot accidentally create duplicate identical RoleMedia relation when relation uniqueness prohibits it.

---

# 424. E2E-MED-064 — Technical READY vs eligible

Valid image READY but not categorized in Main Portfolio.

Questionnaire cannot use it as mandatory Close-Up.

---

# 425. E2E-MED-065 — Public Search

Private/unattached original filename cannot appear in Public Search.

---

# 426. Media lifecycle diagram

```text
          UPLOAD / IMPORT
                ↓
             STAGING
                ↓
        SECURITY VALIDATION
        ┌───────┴────────┐
        ▼                ▼
   REJECT/QUARANTINE    ACCEPT
                         ↓
                 IMMUTABLE ORIGINAL
                         ↓
                     MediaAsset
                         ↓
                   PROCESSING
                 ┌──────┴──────┐
                 ▼             ▼
               READY        PARTIAL/FAILED
```

---

# 427. Derivative diagram

```text
MediaAsset
   +
TransformSpec
   +
ProcessorVersion
   ↓
Deterministic Identity
   ↓
MediaDerivative
```

---

# 428. Domain usage diagram

```text
                       MediaAsset
                           │
       ┌───────────┬───────┼───────┬───────────┐
       ▼           ▼       ▼       ▼           ▼
PortfolioItem Emotional  RoleMedia SocialPost ThemeAsset
       │           │       │
       └───────────┼───────┘
                   ▼
           Questionnaire Snapshot
```

---

# 429. Server import diagram

```text
Approved Import Root
       ↓
Safe Path Resolver
       ↓
MIME / Security
       ↓
Checksum
       ↓
Copy to Managed Storage
       ↓
MediaAsset
```

---

# 430. Remote import diagram

```text
HTTPS URL
   ↓
URL Validator
   ↓
SSRF Guard
   ↓
Bounded Fetch
   ↓
Redirect Revalidation
   ↓
MIME / Security
   ↓
Immutable Local Original
```

---

# 431. Usage/purge diagram

```text
MediaAsset
   ↓
Usage Resolver
 ┌─┼────────┬───────────┬────────────┐
 ▼ ▼        ▼           ▼            ▼
Current Draft Historical Operational Derived
   │
   ↓
PURGE ELIGIBLE?
 ┌───────┴───────┐
 ▼               ▼
NO              YES
│                ↓
Archive/Block  Purge Workflow
```

---

# 432. Historical protection diagram

```text
Media M1
  ↓
QuestionnaireRevision R4
  ↓
PDF Artifact R4

Current Portfolio later replaces M1 with M2

R4 still means:
M1
```

---

# 433. Admin UX diagram

```text
MEDIA LIBRARY
├── All
├── Images
├── Video
├── Audio
├── Documents
├── Processing
├── Failed
├── Unused
└── Archived

Asset Detail
├── Preview
├── Technical Metadata
├── Usage
├── Derivatives
├── Integrity
└── Archive/Purge
```

---

# 434. Media quality gate

Перед implementation должны быть определены:

- [ ] canonical media types;
- [ ] source kinds;
- [ ] external link vs imported media separation;
- [ ] upload staging;
- [ ] MIME validation;
- [ ] size limits;
- [ ] immutable original policy;
- [ ] checksum;
- [ ] storage abstraction;
- [ ] storage zones;
- [ ] metadata extraction;
- [ ] GPS/privacy stripping;
- [ ] derivatives;
- [ ] transform spec schema;
- [ ] processor versioning;
- [ ] image contain/no-crop default;
- [ ] video/audio processing;
- [ ] server import roots;
- [ ] SSRF-safe remote import;
- [ ] Admin Media Library;
- [ ] usage graph;
- [ ] duplicate detection;
- [ ] Public/private delivery;
- [ ] stable media URLs;
- [ ] same-profile enforcement;
- [ ] Portfolio integration;
- [ ] Emotional integration;
- [ ] Grid crop isolation;
- [ ] Questionnaire integration;
- [ ] Builder restrictions;
- [ ] Social integration;
- [ ] AI boundaries;
- [ ] integrity scanner;
- [ ] archive;
- [ ] purge;
- [ ] backup/restore;
- [ ] VOP actions;
- [ ] observability;
- [ ] E2E tests.

---

# 435. Acceptance criteria

`AC-MED-001`  
Каждый managed original имеет стабильный MediaAsset ID.

`AC-MED-002`  
Original immutable после acceptance.

`AC-MED-003`  
Replacement создаёт новый MediaAsset.

`AC-MED-004`  
MIME проверяется server-side.

`AC-MED-005`  
Filename не управляет storage path.

`AC-MED-006`  
Каждый accepted original имеет checksum.

`AC-MED-007`  
Unvalidated/quarantined файл не может использоваться профессиональными модулями.

`AC-MED-008`  
Image/video/audio/document имеют distinct type semantics.

`AC-MED-009`  
Внешняя профессиональная ссылка не маскируется под immutable MediaAsset.

`AC-MED-010`  
Remote imported file становится controlled local original только после безопасного fetch.

`AC-MED-011`  
Server import ограничен разрешёнными roots.

`AC-MED-012`  
Remote import защищён от SSRF.

`AC-MED-013`  
Derivatives не меняют original.

`AC-MED-014`  
Derivative identity включает transform и processor version.

`AC-MED-015`  
Portfolio default сохраняет исходный кадр/соотношение сторон.

`AC-MED-016`  
Contextual Grid crop существует только в Grid config.

`AC-MED-017`  
Media processing не изменяет внешность актрисы.

`AC-MED-018`  
EXIF shooting date не становится Emotional shooting date автоматически.

`AC-MED-019`  
Public derivatives не публикуют GPS metadata.

`AC-MED-020`  
Один MediaAsset может переиспользоваться несколькими modules.

`AC-MED-021`  
Удаление relation не удаляет shared original.

`AC-MED-022`  
MediaAsset не владеет Portfolio category.

`AC-MED-023`  
Public Builder не имеет прямого доступа к Media Library inventory.

`AC-MED-024`  
Questionnaire historical media selection остаётся привязанной к exact frozen source.

`AC-MED-025`  
Feedback/Casting attachments не становятся professional MediaAsset автоматически.

`AC-MED-026`  
Public URL не раскрывает filesystem path.

`AC-MED-027`  
Private media requires authorization.

`AC-MED-028`  
Technical READY не означает professional eligibility.

`AC-MED-029`  
Usage graph показывает current и historical dependencies.

`AC-MED-030`  
Hard purge блокируется при запрещающих dependencies.

`AC-MED-031`  
Storage delete failure не создаёт ложный PURGED state.

`AC-MED-032`  
Derivative можно удалить и восстановить.

`AC-MED-033`  
Missing original является integrity incident.

`AC-MED-034`  
Checksum mismatch не приводит к автоматическому изменению checksum.

`AC-MED-035`  
AI recommendations не могут автоматически удалить/ретушировать media.

`AC-MED-036`  
Media Library работает без AI provider.

`AC-MED-037`  
Background retries не создают duplicate derivative.

`AC-MED-038`  
Storage backend можно заменить без изменения Domain identity.

`AC-MED-039`  
Originals входят в backup/restore strategy.

`AC-MED-040`  
Media module покрыт security/integrity/usage/purge E2E tests.

---

# 436. Финальная доктрина

> **Media Library — это единый технический слой владения immutable originals и их воспроизводимыми derivatives. Он никогда не определяет профессиональный смысл файла, его роль в Portfolio, Emotional Portfolio, Project, Questionnaire или Social publication и не превращает наличие файла в разрешение на публичный доступ. Каждый original сохраняется неизменным, каждая contextual transformation существует отдельно, каждое использование связано через owning-domain relation, а hard purge возможен только после dependency analysis. Внешние ссылки остаются Professional Links, если файл не был явно и безопасно импортирован в controlled storage. AI может помогать с классификацией, duplicate detection и crop suggestions, но не может автоматически менять внешность, удалять профессиональные материалы или становиться владельцем media semantics.**