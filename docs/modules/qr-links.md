# QR LINKS MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация QR Domain: exact canonical URL encoding, HTML/PDF integration, print-safe rendering, decode verification, lifecycle, snapshots, caching и access safety

**Целевой файл:** `docs/modules/qr-links.md`  
**Документ:** DOC-130  
**Статус:** ✅ Completed  
**Тип:** Module / QR / Links / Derived Artifact / PDF / Print / Access Safety

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
- `docs/architecture/search.md`
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
- `docs/modules/professional-media-links.md`
- `docs/modules/contacts.md`
- `docs/modules/questionnaires.md`
- `docs/modules/public-questionnaire-builder.md`
- `docs/modules/marketing-analytics.md`

---

# 1. Назначение модуля

QR Links Module отвечает за безопасное создание, проверку, хранение и использование QR-кодов для профессиональных ссылок, которые должны работать:

- на публичном сайте;
- в подготовленных Questionnaire;
- в Public Questionnaire Builder;
- в HTML-представлении;
- в PDF;
- на печатной копии документа.

Основная задача:

> **QR-код является дополнительным визуальным способом открыть тот же разрешённый canonical target, который доступен пользователю как обычная кликабельная ссылка.**

---

# 2. Главная доктрина

> **QR не является источником URL и не является самостоятельным бизнес-контентом. Он является Derived Artifact, построенным из уже разрешённого canonical target. Текст, hyperlink и QR одного Questionnaire item должны указывать на один и тот же frozen target.**

Canonical:

```text
Source / Snapshot
      │
      ▼
Canonical Allowed URL
      │
      ├──────────────► HTML hyperlink
      │
      ├──────────────► PDF link annotation
      │
      └──────────────► QR encoding
                           │
                           ▼
                     Decode Verify
                           │
                           ▼
                     QRArtifact
```

---

# 3. Fundamental separation

```text
ProfessionalLink
≠ MediaAsset
≠ Public Media URL
≠ QuestionnaireSnapshot
≠ QRArtifact
≠ ShareLink
```

---

# 4. Core formula

```text
QRArtifact = Derived(canonical_url, rendering_settings, renderer_version)
```

---

# 5. Critical invariant

```text
decode(QRArtifact) === canonical_url
```

Строгое строковое равенство после завершённой canonicalization.

---

# 6. Module identifiers

Используются:

```text
QR-*
QR-TGT-*
QR-URL-*
QR-RND-*
QR-VER-*
QR-PRN-*
QR-PDF-*
QR-QNR-*
QR-BLD-*
QR-CACHE-*
QR-SEC-*
QR-LIFE-*
QR-INV-*
QR-AP-*
E2E-QR-*
```

---

# 7. QR target

QR target — URL, уже признанный системой допустимым для конкретного контекста.

---

# 8. Baseline supported target classes

QR MAY be generated for:

```text
PUBLIC_PROFILE_URL
PUBLIC_QUESTIONNAIRE_URL
CASTING_SAFE_QUESTIONNAIRE_URL
PROFESSIONAL_LINK
PUBLIC_MEDIA_URL
CASTING_SAFE_MEDIA_URL
OTHER_APPROVED_PROFESSIONAL_URL
```

---

# 9. Professional media examples

Включают:

- видеовизитку;
- showreel;
- внешний video URL;
- audio sample;
- voice sample;
- другой approved ProfessionalLink.

---

# 10. QR-INV-001 — Target Must Already Be Authorized

QR generation никогда не делает URL разрешённым.

---

# 11. Forbidden target classes

QR MUST NOT encode:

```text
ADMIN_URL
INTERNAL_API_URL
DATABASE_IDENTIFIER_URL
FILESYSTEM_PATH
RAW_STORAGE_PATH
PRIVATE_STORAGE_URL
UNSCOPED_SIGNED_URL
LOCALHOST_URL
PRIVATE_NETWORK_URL
TEMPORARY_UPLOAD_URL
QUARANTINED_ASSET_URL
```

---

# 12. Examples forbidden

```text
/admin/media/...
http://localhost:3336/...
http://192.168.x.x/...
file:///data/kate-actor/...
/data/kate-actor/media/...
s3://bucket/object
```

---

# 13. QR-SEC-001 — Admin URL Is Never Valid QR Target

---

# 14. QR-SEC-002 — Filesystem/Storage Locator Is Never Public QR Target

---

# 15. QR for managed local media

Managed file does not encode its storage locator.

Instead:

```text
MediaAsset
   ↓
Public/Casting-safe application resolver
   ↓
Stable allowed URL
   ↓
QR
```

---

# 16. QR-URL-001 — Managed Media Requires Stable Application-Controlled URL

---

# 17. Ephemeral signed URLs

Baseline prohibits using short-lived signed storage URLs in finalized Questionnaire QR.

---

# 18. Why

A PDF may live for months or years.

A URL expiring in 15 minutes makes the historical document defective.

---

# 19. QR-URL-002 — Final QR Target Must Be Stable for Intended Artifact Lifetime

---

# 20. Casting-safe tokenized URLs

Allowed when deliberate access policy requires scoped access.

---

# 21. Requirements for tokenized target

Token must be:

- scoped;
- purpose-specific;
- non-admin;
- non-privilege-escalating;
- compatible with intended recipient lifetime;
- revocable where policy requires.

---

# 22. QR-SEC-003 — Token Scope Must Match Intended Audience

---

# 23. Canonical URL

QR uses exact canonical URL resolved by domain/application layer.

---

# 24. Canonicalization happens before QR generation

Canonical flow:

```text
Input URL
  ↓
URL validation
  ↓
Canonicalization
  ↓
Access classification
  ↓
Context eligibility
  ↓
QR generation
```

---

# 25. QR-URL-003 — QR Generator Does Not Canonicalize Arbitrarily

It consumes already canonicalized target.

---

# 26. URL canonicalization principles

May include:

- normalized scheme/host case;
- validated percent encoding;
- normalized known application routes;
- removal of meaningless default ports;
- safe normalized representation.

---

# 27. Canonicalization MUST NOT:

- change destination semantics;
- remove required access token;
- add analytics redirect;
- add tracking parameters automatically;
- convert public URL into Admin URL;
- follow redirects and silently replace Source.

---

# 28. QR-URL-004 — Canonicalization Preserves Destination Semantics

---

# 29. Exact equality rule

After canonicalization:

```text
hyperlink.href
=
pdf.annotation.target
=
qr.encoded_value
```

for same frozen Questionnaire item.

---

# 30. QR-INV-002 — Link/QR Target Equality Is Mandatory

---

# 31. Human-readable display text

Displayed URL MAY be shortened visually for layout.

Example:

```text
youtube.com/...
```

But hyperlink and QR still use full canonical URL.

---

# 32. QR-INV-003 — Visual Label Does Not Define Destination

---

# 33. Description

QR may appear with professional description.

Example:

```text
Видеовизитка
[Открыть видео] [QR]
```

---

# 34. Description ownership

Description belongs to ProfessionalLink/ProfessionalMediaItem/QuestionnaireSnapshot, not QRArtifact.

---

# 35. QR-INV-004 — QR Does Not Own Professional Description

---

# 36. Per-link presentation controls

Questionnaire item must support independent:

```text
show_link
show_qr
```

---

# 37. Examples

Valid:

```text
show_link = true
show_qr = true
```

Valid:

```text
show_link = true
show_qr = false
```

Potentially valid in special presentation:

```text
show_link = false
show_qr = true
```

but discouraged for accessibility.

---

# 38. Recommended baseline

For generated professional documents:

```text
show_qr = true
→ show_link SHOULD also be true
```

---

# 39. QR-INV-005 — QR Is Supplementary, Not Sole Access Mechanism

---

# 40. Accessibility

Every QR should have nearby:

- meaningful description;
- clickable text link where digital medium permits.

---

# 41. QR-ACC-001 — QR Cannot Be the Only Route in HTML/PDF

---

# 42. Contacts boundary

Automatic QR for:

- phone;
- email;
- WhatsApp;
- vCard;

is **not baseline functionality** of DOC-130.

---

# 43. Why

Contact data has higher privacy sensitivity and distinct semantics.

---

# 44. QR-INV-006 — Contact QR Requires Separate Explicit Opt-In Product Rule

---

# 45. No automatic vCard

System does not automatically aggregate Contact Directory into QR/vCard.

---

# 46. QR-SEC-004 — Sensitive Contact Aggregation Into QR Is Forbidden by Default

---

# 47. QR Artifact

Logical concept:

```text
QRArtifact
```

---

# 48. Suggested persisted metadata

```text
id
target_fingerprint
canonical_url_hash
rendering_profile
format
renderer_version
verification_state
created_at
storage_reference?
```

---

# 49. Whether QR bytes are persisted

Implementation MAY:

- store generated SVG/PNG;
- generate deterministically on demand;
- use cache-backed artifact storage.

---

# 50. QR-INV-007 — Persisted Bytes Are Derived

Source of truth remains frozen target URL + settings.

---

# 51. Canonical URL storage

For privacy-sensitive tokenized URLs, avoid duplicating raw URL unnecessarily in generic cache metadata.

---

# 52. May store

```text
canonical_url_hash
```

plus protected snapshot reference.

---

# 53. QR-SEC-005 — Cache Metadata Does Not Become Token Leak

---

# 54. Rendering formats

Canonical supported formats:

```text
SVG
PNG
```

---

# 55. SVG

Preferred for:

- PDF;
- print;
- high-resolution rendering;
- scalable output.

---

# 56. PNG

Useful for:

- browser fallback;
- raster-only consumers;
- previews.

---

# 57. QR-RND-001 — Vector Is Preferred for PDF

---

# 58. Raster resolution

PNG must be rendered directly at required output resolution.

---

# 59. Forbidden

Generate tiny QR and upscale with smoothing.

---

# 60. QR-RND-002 — No Interpolated Upscaling

---

# 61. Module geometry

QR renderer must preserve exact square modules.

---

# 62. No antialiased module edges in print raster where it can degrade scanning.

---

# 63. QR-RND-003 — Module Geometry Must Remain Crisp

---

# 64. Quiet zone

Minimum quiet zone:

```text
4 modules
```

on every side.

---

# 65. QR-PRN-001 — Quiet Zone ≥ 4 Modules

---

# 66. Theme/decor boundary

Theme must not:

- place decoration inside quiet zone;
- overlay text;
- place border through QR;
- reduce required clear area.

---

# 67. QR-PRN-002 — Quiet Zone Is Reserved Space

---

# 68. Print module size

Baseline minimum physical module size:

```text
0.4 mm
```

for generated print artifact.

---

# 69. Derived physical QR size

For QR matrix dimension `N` modules:

```text
min_width_mm = (N + 8) × 0.4
```

where `8` represents two 4-module quiet zones.

---

# 70. Example

QR version 5:

```text
N = 37
min_width = (37 + 8) × 0.4
          = 18 mm
```

---

# 71. Recommended minimum overall printed size

Even if formula yields less:

```text
20 mm × 20 mm
```

is baseline minimum.

---

# 72. QR-PRN-003 — Print Size Uses Module Density, Not Fixed Pixel Guess

---

# 73. Recommended professional PDF default

Prefer approximately:

```text
24–30 mm
```

where layout permits.

---

# 74. Dense target

If URL causes higher QR version:

physical size increases automatically to preserve module size.

---

# 75. QR-PRN-004 — Dense QR Must Grow, Not Shrink Modules

---

# 76. Error correction

Baseline:

```text
Error Correction Level M
```

unless renderer/document policy has justified higher level.

---

# 77. Higher level

`Q` may be used for specific print needs, but increases matrix density.

---

# 78. QR-RND-004 — Error Correction Is Controlled Rendering Setting

---

# 79. Logo overlay

Not baseline.

---

# 80. QR-PRN-005 — No Logo Embedded Into QR Baseline

This avoids scan reliability degradation.

---

# 81. Stylized modules

Rounded/dotted/custom artistic QR modules are not baseline for professional PDFs.

---

# 82. QR-PRN-006 — Reliability Over Decorative Styling

---

# 83. Contrast

Baseline:

```text
dark foreground
light background
```

with strong luminance contrast.

---

# 84. Avoid:

- low contrast;
- transparent complex backgrounds;
- gradients through modules;
- photography behind QR.

---

# 85. QR-PRN-007 — QR Contrast Must Be Scan-Safe

---

# 86. Color inversion

Light-on-dark QR may work technically but is not baseline for generated professional PDF.

---

# 87. Default

Use conventional dark-on-light rendering.

---

# 88. Verification

Every newly generated production QR must be decode-verified.

---

# 89. Verification flow

```text
Generate
  ↓
Decode generated artifact
  ↓
Compare exact decoded string
  ↓
PASS / FAIL
```

---

# 90. QR-VER-001 — Decode Verification Is Mandatory

---

# 91. Verification equality

Must satisfy:

```text
decoded_value === expected_canonical_url
```

---

# 92. No fuzzy comparison

Not acceptable:

```text
same domain
similar URL
same destination after redirect
```

---

# 93. QR-VER-002 — Verification Uses Exact String Equality

---

# 94. Redirect equivalence

A QR encoding URL A that redirects to B is not equivalent to encoding B for verification purposes.

---

# 95. QR-VER-003 — Redirect Does Not Satisfy Exact Target Equality

---

# 96. Verification failure

Artifact must not be marked READY.

---

# 97. States

Recommended:

```text
GENERATING
VERIFYING
READY
FAILED_GENERATION
FAILED_VERIFICATION
STALE
REVOKED
```

---

# 98. QR-VER-004 — Failed Verification Blocks Publication Artifact Use

---

# 99. Verification metadata

Store:

```text
verification_state
verified_at
decoder_version?
decoded_hash?
```

as useful.

---

# 100. Raw tokenized decoded value

Should not be copied unnecessarily into logs.

---

# 101. QR-SEC-006 — Verification Logs Are Secret-Safe

---

# 102. Multi-decoder validation

Optional hardening:

production tests MAY use more than one decoder/library.

Not required runtime baseline.

---

# 103. Print validation

Automated verification should test rendered artifact, not only internal QR matrix representation.

---

# 104. QR-VER-005 — Verify Final Rendered Representation

---

# 105. PNG verification

Decode produced PNG.

---

# 106. SVG verification

Can rasterize in controlled test/runtime verifier then decode.

---

# 107. PDF verification

PDF E2E should render page containing QR and decode the actual rendered QR region/artifact.

---

# 108. QR-PDF-001 — PDF QR Must Be Verified in Final Artifact Path

---

# 109. Questionnaire integration

Prepared Questionnaire item snapshots:

```text
description
canonical_url
show_link
show_qr
ordering
QR rendering semantics
```

must be frozen in Revision/GenerationSnapshot.

---

# 110. QR-QNR-001 — Historical Questionnaire Freezes QR Target Semantics

---

# 111. Current Source URL changes later

Do not rewrite old QuestionnaireRevision.

---

# 112. Example

Revision R3 froze:

```text
https://example.com/video-old
```

Current ProfessionalLink later becomes:

```text
https://example.com/video-new
```

R3 remains historically tied to old frozen URL.

---

# 113. QR-QNR-002 — Current URL Change Does Not Rewrite Historical QR

---

# 114. Re-render historical Revision

Must use frozen URL.

---

# 115. QR-QNR-003 — Historical Re-render Uses Snapshot, Not Current Source

---

# 116. New QuestionnaireRevision

Uses current eligible Source URL.

---

# 117. Source update vs Artifact cache

Current QR cache for changed Source must be invalidated/re-keyed.

---

# 118. Historical artifact cache remains addressable by frozen target fingerprint.

---

# 119. QR-CACHE-001 — Historical and Current QR Cache Keys Are Independent

---

# 120. Questionnaire PDF

When:

```text
show_link = true
```

PDF renderer must create actual hyperlink annotation.

---

# 121. When:

```text
show_qr = true
```

PDF renderer includes verified QR.

---

# 122. QR-PDF-002 — PDF Link Must Be Clickable

---

# 123. QR-PDF-003 — QR Does Not Replace PDF Annotation

---

# 124. PDF layout

QR should be placed near its associated:

- description;
- media item;
- URL/link label.

---

# 125. QR-PDF-004 — Semantic Association Must Be Clear

---

# 126. Avoid

A detached page full of unlabeled QR codes unless explicitly designed and clearly numbered.

---

# 127. If multiple QR on page

Each requires distinct adjacent label.

---

# 128. QR-PDF-005 — Multiple QR Codes Must Be Unambiguous

---

# 129. HTML Questionnaire

Can show:

```text
Описание
[Открыть]
[QR image]
```

---

# 130. QR image accessibility

`alt` should explain purpose, not encode raw long URL.

Example:

```text
QR-код для открытия видеовизитки
```

---

# 131. QR-ACC-002 — Meaningful Alternative Description Required

---

# 132. HTML QR click

QR image itself MAY also be wrapped in same link.

If so:

```text
img link target = canonical_url
```

---

# 133. QR-INV-008 — Clickable QR Image Uses Same Target

---

# 134. Public Questionnaire Builder

Builder may expose per-item:

```text
show_link
show_qr
```

only where template/policy permits.

---

# 135. Builder visitor cannot change URL.

---

# 136. QR-BLD-001 — Builder Can Control Representation, Not Source Target

---

# 137. Generation-time validation

At Generate:

server reloads current eligible Source and validates:

- item still belongs profile;
- Builder eligibility still true;
- URL current/allowed;
- target access class valid;
- QR rendering permitted.

---

# 138. QR-BLD-002 — Builder Generate Revalidates QR Target

---

# 139. Frozen GenerationSnapshot

After successful generation:

```text
canonical_url
show_link
show_qr
```

are frozen.

---

# 140. QR-BLD-003 — Builder Historical Generation Is Immutable

---

# 141. Expired/revoked casting-safe target

Generated historical PDF bytes may still exist, but application access can be revoked according to security/deletion policy.

---

# 142. QR-SEC-007 — Snapshot Immutability Does Not Prevent Access Revocation

---

# 143. Important distinction

```text
Historical semantic value
≠ guaranteed perpetual authorization
```

---

# 144. Public Profile QR

Optional use.

Examples:

- official profile URL in PDF;
- public Questionnaire URL.

---

# 145. Official Profile QR

Should encode exact public canonical Profile URL.

---

# 146. QR-TGT-001 — Profile QR Uses Official Canonical Profile URL

---

# 147. ProfessionalLink URL changes

On update:

1. Source new canonical URL saved;
2. current derived QR becomes stale/unreferenced;
3. new QR generated on demand/background;
4. old historical snapshot QR remains valid for old Revision.

---

# 148. QR-LIFE-001 — URL Change Invalidates Current Derived QR

---

# 149. Do not mutate image bytes in place under same immutable artifact identity if target changed.

---

# 150. QR-LIFE-002 — Different Target Means Different Artifact Identity

---

# 151. Artifact fingerprint

Recommended fingerprint inputs:

```text
canonical_url
format
error_correction_level
quiet_zone_modules
module_size_profile
renderer_version
rendering_profile_version
```

---

# 152. QR-CACHE-002 — Cache Key Includes Target and Rendering Semantics

---

# 153. Example conceptual hash

```text
sha256(
  canonical_url
  + format
  + ec_level
  + quiet_zone
  + renderer_version
  + profile_version
)
```

---

# 154. Security-sensitive URL

Hashing raw tokenized URL for cache key is acceptable only if cache implementation does not expose reversible/raw value and security review approves it.

---

# 155. QR-SEC-008 — Cache Key Must Not Leak Sensitive URL

---

# 156. Cache storage

Could be:

```text
/data/kate-actor/derived/qr/...
```

or managed derived storage.

---

# 157. Public serving

QR image itself may be public when associated with public target.

---

# 158. Private/casting-safe QR

Must respect artifact access class.

---

# 159. QR-SEC-009 — QR Artifact Access Cannot Be Broader Than Target Context

---

# 160. Why

Even if QR only contains URL, exposing it may reveal access token.

---

# 161. Public target QR

May use public cache.

---

# 162. Tokenized casting-safe QR

Must not enter shared public cache.

---

# 163. QR-CACHE-003 — Cache Partitioned by Access Class

---

# 164. Browser caching

Public QR can use long immutable cache headers if artifact URL is content-addressed.

---

# 165. Revocable sensitive QR

Requires stricter cache policy.

---

# 166. QR-CACHE-004 — Revocable Target Does Not Use Irrevocable Public Caching

---

# 167. Cache invalidation

Immutable content-addressed artifacts need no in-place invalidation.

Current alias/reference changes instead.

---

# 168. Recommended

```text
new target
→ new QR artifact key
```

---

# 169. QR-CACHE-005 — Prefer Immutable Artifact Addressing

---

# 170. Current aliases

If application uses alias:

```text
/current/qr/{link-id}
```

alias must invalidate/repoint on URL change.

---

# 171. Old job race

QR job for URL v4 completes after Source is already v5.

It must not overwrite current alias.

---

# 172. QR-CACHE-006 — Current QR Alias Is Generation-Safe

---

# 173. Background jobs

Suggested job types:

```text
QR_GENERATE
QR_VERIFY
QR_REBUILD
QR_CLEANUP
```

---

# 174. Job input

Should reference:

```text
source snapshot/revision ID
target fingerprint
rendering profile
```

rather than trusting arbitrary client URL.

---

# 175. QR-SEC-010 — Worker Does Not Accept Unvalidated Arbitrary URL

---

# 176. At-least-once delivery

Generation must be idempotent.

---

# 177. Same fingerprint

Produces/reuses same logical artifact.

---

# 178. QR-INV-009 — QR Generation Is Idempotent

---

# 179. Duplicate worker

Must not create conflicting artifacts/current pointer changes.

---

# 180. Failed QR job

Questionnaire readiness may become blocked if `show_qr=true` and QR is mandatory for requested output.

---

# 181. QR-INV-010 — Requested QR Must Be READY Before Final Artifact Publication

---

# 182. Optional QR

If item has:

```text
show_qr=false
```

QR readiness is irrelevant.

---

# 183. PDF generation dependency

For `show_qr=true`:

```text
PDF job
→ requires READY verified QR
```

or may generate+verify synchronously within bounded renderer pipeline.

---

# 184. QR-PDF-006 — PDF Cannot Embed Unverified QR

---

# 185. Failure behavior

If QR fails:

do not silently remove it while claiming document successfully matches configuration.

---

# 186. Options

- fail PDF generation;
- mark generation blocked;
- require Human change `show_qr=false`.

---

# 187. QR-PDF-007 — No Silent QR Omission

---

# 188. QR scan testing

Test suite should include:

- SVG;
- PNG;
- PDF-rendered;
- screen-sized;
- print-resolution simulation.

---

# 189. Physical print simulation

CI can validate rasterized physical-size equivalent at defined DPI.

---

# 190. QR-VER-006 — Print-Scale Regression Tests Required

---

# 191. Typical print DPI

Testing profiles may include:

```text
150 DPI
300 DPI
```

with 300 DPI preferred for generated professional PDF output.

---

# 192. PDF QR itself should remain vector where renderer supports it.

---

# 193. QR-PRN-008 — Raster Test Does Not Replace Vector Production Preference

---

# 194. Image compression

PDF pipeline must not apply destructive JPEG compression to QR.

---

# 195. QR-PDF-008 — QR Must Not Be JPEG-Compressed

---

# 196. SVG safety

Generated SVG must come from trusted internal renderer.

---

# 197. Do not accept arbitrary uploaded SVG as QR artifact.

---

# 198. QR-SEC-011 — QR SVG Is Internally Generated Only

---

# 199. SVG should contain only necessary vector geometry.

No:

- scripts;
- foreignObject;
- remote resources;
- external references.

---

# 200. QR-SEC-012 — Generated SVG Has Minimal Safe Structure

---

# 201. PNG metadata

No need to include target URL as plaintext metadata.

---

# 202. QR-PRV-001 — Avoid Redundant Sensitive Metadata

---

# 203. QR download

Admin may download QR asset for professional use.

---

# 204. Download naming

Use safe filename.

Example:

```text
videovizitka-qr.svg
```

---

# 205. Do not include token/URL query in filename.

---

# 206. QR-SEC-013 — Filename Is Sanitized

---

# 207. QR artifact reuse

Same exact public target + same rendering profile may reuse artifact.

---

# 208. Context-specific access

Same URL but different access/retention class may require separate artifact metadata even if rendered bytes identical.

---

# 209. QR-INV-011 — Byte Equality Does Not Necessarily Mean Same Access Policy

---

# 210. Analytics boundary

QR is not inherently trackable.

---

# 211. Analytics must not replace exact target with tracking redirect unless the actual business target is explicitly designed as that application-controlled ShareLink.

---

# 212. QR-INV-012 — Tracking Never Overrides Target Contract

---

# 213. If QR target is ShareLink

Then exact canonical target is that ShareLink URL.

This must be explicit at Source/Questionnaire configuration stage.

---

# 214. No invisible QR rewriting during PDF render.

---

# 215. QR-INV-013 — Renderer Cannot Inject Analytics Wrapper

---

# 216. Search

QR artifacts are not Search entities.

---

# 217. Public Search may index the professional destination entity according to its own policy.

---

# 218. QR-INV-014 — QR Does Not Affect Search Eligibility

---

# 219. SEO

No need to index QR image URL as content.

---

# 220. QR image alt text may be meaningful but not keyword-stuffed.

---

# 221. Theme

Theme may style:

- spacing around QR;
- label typography;
- container;
- alignment.

---

# 222. Theme cannot change:

- encoded URL;
- QR geometry;
- quiet zone below minimum;
- verification requirement;
- security/access class.

---

# 223. QR-INV-015 — Theme Has No QR Target Authority

---

# 224. Theme color

Baseline QR foreground/background should remain scan-safe.

Theme may not apply arbitrary palette that violates validator.

---

# 225. QR-PRN-009 — Theme Styling Cannot Reduce Scan Reliability

---

# 226. BB Assistant

May draft link description.

It cannot create or alter URL.

---

# 227. QR-AI-001 — AI Has No QR Target Authority

---

# 228. Casting AI

May recommend including an eligible link.

It cannot invent its URL or QR.

---

# 229. QR-AI-002 — AI Recommendation Does Not Create Target

---

# 230. VOP

May detect:

```text
missing QR artifact
failed verification
stale current QR
QR target revoked
print size violation
PDF QR mismatch
```

---

# 231. Safe VOP action

May:

```text
regenerate QR derived artifact
re-run decode verification
invalidate stale QR cache
```

---

# 232. VOP cannot:

- replace URL;
- enable QR presentation;
- publish Questionnaire;
- expose private target.

---

# 233. QR-INV-016 — VOP May Repair Derived QR Only

---

# 234. QR deletion

QR artifact is rebuildable derived data.

May be purged when unreferenced.

---

# 235. Historical referenced QR

Artifact bytes may be regenerated from frozen Revision Snapshot.

---

# 236. QR-LIFE-003 — QRArtifact Can Be Rebuilt From Frozen Target

---

# 237. Source link archive

Current UI stops offering archived ProfessionalLink for new Questionnaire selection.

---

# 238. Historical Questionnaire remains frozen.

---

# 239. QR-LIFE-004 — Archive Does Not Rewrite Historical QR

---

# 240. Source hard deletion/privacy purge

Must inspect:

- current QR cache;
- historical snapshots;
- tokenized URLs;
- generated PDFs;
- share links;
- cached images.

---

# 241. QR-SEC-014 — Purge Performs QR Copy/Reference Discovery

---

# 242. Revocation priority

For a sensitive URL:

access revocation takes precedence over historical convenience.

---

# 243. Historical PDF cannot necessarily be recalled once externally distributed.

System must not imply otherwise.

---

# 244. QR-SEC-015 — Distributed Artifact Revocation Has Physical Limits

---

# 245. Internal stored PDF

Can be revoked/deleted according to retention/access policy.

---

# 246. Logging

QR logs should use:

```text
artifact_id
target fingerprint/hash
source entity ID
status
renderer version
verification result
```

---

# 247. Avoid raw tokenized URLs.

---

# 248. QR-PRV-002 — Logs Are Target-Minimized

---

# 249. Audit

Audit high-impact QR configuration changes primarily occurs in owning domain:

- Source URL change;
- `show_qr` change;
- Questionnaire publish;
- Builder generation.

---

# 250. Derived QR regeneration itself can use operational execution log rather than high-volume business Audit.

---

# 251. QR-INV-017 — QR Generation History ≠ Business Audit

---

# 252. Metrics

Operational metrics:

```text
qr_generation_success
qr_generation_failure
qr_verification_failure
qr_cache_hit
qr_rebuild
```

---

# 253. No need to store destination PII in metrics.

---

# 254. API commands

Canonical application commands may include:

```text
GenerateQRArtifact
VerifyQRArtifact
RegenerateQRArtifact
PurgeUnreferencedQRArtifacts
```

Mostly internal/Admin operational APIs.

---

# 255. Queries

```text
GetQRArtifact
GetQRArtifactStatus
GetQRPreview
GetQRCodeReadiness
```

---

# 256. Public QR image endpoint

If used, must be content-addressed/access-controlled according to target class.

---

# 257. Public endpoint input

Should use artifact ID/hash, not arbitrary URL.

---

# 258. QR-SEC-016 — Public QR Endpoint Is Not Generic QR Generator

Forbidden endpoint pattern:

```text
/api/qr?url=<anything>
```

for anonymous callers.

---

# 259. Why

It creates:

- phishing surface;
- open redirect-style abuse;
- SSRF confusion;
- unbounded resource consumption;
- brand misuse.

---

# 260. QR-SEC-017 — Anonymous Arbitrary QR Generation Is Forbidden

---

# 261. Admin preview generator

Even Admin generator should resolve target from approved entity/context where possible.

---

# 262. Direct custom URL

If product later supports it:

must pass full ProfessionalLink URL policy before becoming Source.

---

# 263. Error taxonomy

At minimum:

```text
QR_TARGET_REQUIRED
QR_TARGET_INVALID
QR_TARGET_NOT_CANONICAL
QR_TARGET_NOT_ALLOWED
QR_TARGET_PRIVATE
QR_TARGET_ADMIN
QR_TARGET_STORAGE_URL
QR_TARGET_UNSTABLE
QR_TARGET_REVOKED
QR_TARGET_WRONG_PROFILE
QR_RENDERING_PROFILE_INVALID
QR_GENERATION_FAILED
QR_VERIFICATION_FAILED
QR_DECODE_MISMATCH
QR_ARTIFACT_NOT_FOUND
QR_ARTIFACT_STALE
QR_ARTIFACT_REVOKED
QR_PRINT_SIZE_INVALID
QR_QUIET_ZONE_INVALID
QR_ACCESS_FORBIDDEN
QR_STALE_GENERATION
```

---

# 264. Concurrency

QR generation itself is content-addressed/idempotent.

---

# 265. Current entity link update

Uses Source entity version.

---

# 266. Job completion rechecks expected target fingerprint before updating current alias.

---

# 267. QR-INV-018 — Stale Worker Cannot Become Current QR

---

# 268. Questionnaire publish race

Questionnaire publication freezes exact target.

Subsequent Source link update cannot alter publish transaction result.

---

# 269. QR-QNR-004 — Questionnaire Publish Is Snapshot-Consistent

---

# 270. Builder Generate race

Source link changes during Builder generation.

Server must use one validated consistent generation snapshot.

---

# 271. QR-BLD-004 — Builder QR Generation Is Snapshot-Consistent

---

# 272. Migration

Existing QR images may exist without provenance.

---

# 273. Migration process

For each existing QR:

1. locate candidate image;
2. decode;
3. capture decoded target;
4. resolve intended source relation if possible;
5. compare to canonical expected URL;
6. classify;
7. reuse only when valid.

---

# 274. Migration statuses

```text
VERIFIED_REUSABLE
DECODED_MISMATCH
UNDECODABLE
UNSAFE_TARGET
UNKNOWN_PROVENANCE
REGENERATE
```

---

# 275. QR-MIG-001 — Existing QR Is Not Trusted by Appearance

---

# 276. Existing QR reuse

Allowed only if:

```text
decode(existing_qr)
===
expected canonical URL
```

and rendering/access safety acceptable.

---

# 277. QR-MIG-002 — Decode Verification Required Before Reuse

---

# 278. Existing QR encodes Admin URL

Reject and regenerate from approved public/casting-safe target.

---

# 279. Existing QR encodes expired signed URL

Reject.

---

# 280. Existing QR undecodable

Regenerate where valid Source exists.

---

# 281. Legacy PDF

Do not silently rewrite historical external PDF.

For internally regenerated version:

preserve historical frozen target semantics unless performing explicit corrected new Revision.

---

# 282. QR-MIG-003 — Migration Does Not Rewrite Professional Document History

---

# 283. Legacy QR without Source

Can be kept only as legacy artifact if retention requires, but cannot become canonical reusable QR automatically.

---

# 284. Migration idempotency

Required.

---

# 285. Backup

Must preserve:

- Source/snapshot URL;
- QuestionnaireRevision;
- QR rendering settings if needed;
- artifact references where useful.

QR bytes themselves are rebuildable.

---

# 286. QR-INV-019 — Backup Does Not Depend on QR Bytes for Recoverability

---

# 287. Restore

After restore:

- rebuild missing QR artifacts;
- do not change targets;
- do not create new QuestionnaireRevision;
- do not revive revoked token;
- do not expose previously private QR.

---

# 288. QR-LIFE-005 — Restore Regenerates Derived Data Only

---

# 289. Security review after restore

Current target access classification must still be respected.

---

# 290. Print UX

Admin preview should display QR at approximate intended print scale when feasible.

---

# 291. Warning examples

```text
QR слишком мал для надёжной печати.
URL слишком длинный: увеличьте размер QR.
Недостаточная свободная зона вокруг QR.
```

---

# 292. QR-PRN-010 — Print Readiness Is Visible

---

# 293. Questionnaire editor UX

Per supported link:

```text
Описание
URL
[✓] Показывать ссылку
[✓] Показывать QR
QR preview
```

---

# 294. URL editor ownership

URL is edited through owning Source editor, not through QR-specific editor in snapshot contexts.

---

# 295. QR-UX-001 — QR UI Does Not Create Shadow URL Source

---

# 296. Prepared Questionnaire

If item references current ProfessionalLink:

Admin may choose representation.

At Publish:

exact target frozen.

---

# 297. Public Builder

Visitor may choose representation where allowed but cannot edit target.

---

# 298. QR-UX-002 — Representation and Destination Are Separate Controls

---

# 299. QR readiness indicator

Recommended:

```text
READY
GENERATING
INVALID_TARGET
FAILED_VERIFICATION
STALE
NOT_REQUIRED
```

---

# 300. QR-UX-003 — Verification Failure Is Explicitly Visible

---

# 301. Accessibility UX

Admin should see:

```text
QR is supplementary; clickable URL remains included.
```

where appropriate.

---

# 302. No deceptive status

“QR generated” is insufficient if decode verification failed.

---

# 303. QR-UX-004 — Generated ≠ Verified

---

# 304. Anti-patterns

`QR-AP-001`  
QR encodes `/admin/...`.

`QR-AP-002`  
QR encodes filesystem path.

`QR-AP-003`  
QR encodes raw S3/storage locator.

`QR-AP-004`  
QR encodes short-lived signed URL in permanent PDF.

`QR-AP-005`  
QR generation makes private URL public.

`QR-AP-006`  
QR target bypasses Builder eligibility.

`QR-AP-007`  
QR target bypasses Questionnaire access policy.

`QR-AP-008`  
QR target silently replaced by analytics redirect.

`QR-AP-009`  
Renderer adds UTM parameters not present in frozen URL.

`QR-AP-010`  
Hyperlink and QR point to different URLs.

`QR-AP-011`  
PDF annotation differs from HTML link.

`QR-AP-012`  
QR used as sole access mechanism.

`QR-AP-013`  
Contact phone/email automatically converted to QR/vCard.

`QR-AP-014`  
Tiny PNG generated then upscaled.

`QR-AP-015`  
QR JPEG-compressed in PDF.

`QR-AP-016`  
Quiet zone removed to save space.

`QR-AP-017`  
Theme places decoration in quiet zone.

`QR-AP-018`  
Low-contrast branded QR accepted without validation.

`QR-AP-019`  
Logo covers central modules.

`QR-AP-020`  
Stylized artistic QR used without scan validation.

`QR-AP-021`  
Dense QR forced into fixed 10 mm box.

`QR-AP-022`  
Generation success treated as verification success.

`QR-AP-023`  
Internal matrix tested, final rendered artifact not tested.

`QR-AP-024`  
Verification accepts redirect equivalence.

`QR-AP-025`  
Verification accepts same domain rather than exact URL.

`QR-AP-026`  
Failed verification QR embedded anyway.

`QR-AP-027`  
PDF silently omits failed requested QR.

`QR-AP-028`  
Current Source URL update rewrites historical Questionnaire QR.

`QR-AP-029`  
Historical Questionnaire re-render uses current link instead of frozen one.

`QR-AP-030`  
Current URL change mutates existing immutable QR bytes under same identity.

`QR-AP-031`  
Stale job overwrites current QR alias.

`QR-AP-032`  
Public/shared cache contains tokenized casting-safe QR.

`QR-AP-033`  
Revoked QR remains indefinitely publicly cached.

`QR-AP-034`  
Anonymous `/qr?url=` generator exposed publicly.

`QR-AP-035`  
User-supplied arbitrary URL accepted directly by QR worker.

`QR-AP-036`  
QR cache filename contains access token.

`QR-AP-037`  
Raw token URL logged on every verification.

`QR-AP-038`  
Uploaded arbitrary SVG treated as trusted QR.

`QR-AP-039`  
Generated SVG contains scripts/external references.

`QR-AP-040`  
QR generation changes ProfessionalLink Source.

`QR-AP-041`  
VOP replaces broken URL automatically.

`QR-AP-042`  
AI invents QR destination.

`QR-AP-043`  
Theme changes encoded destination.

`QR-AP-044`  
QR changes Search visibility.

`QR-AP-045`  
QR image publicly exposed for private target.

`QR-AP-046`  
Share tracking broadens private target scope.

`QR-AP-047`  
Legacy QR trusted without decoding.

`QR-AP-048`  
Expired legacy signed URL reused.

`QR-AP-049`  
Migration rewrites historical PDF silently.

`QR-AP-050`  
Restore revives revoked target.

`QR-AP-051`  
Restore creates new professional URL.

`QR-AP-052`  
QR bytes treated as irreplaceable source in backup.

`QR-AP-053`  
Row number in link table used as QR entity identity.

`QR-AP-054`  
Description stored only inside QR artifact metadata.

`QR-AP-055`  
Scanning capability assumed from browser without actual decode verification.

`QR-AP-056`  
QR print size specified only in pixels.

`QR-AP-057`  
Module size ignored for long URLs.

`QR-AP-058`  
Different access classes reuse same public artifact endpoint blindly.

`QR-AP-059`  
Builder visitor edits canonical URL directly.

`QR-AP-060`  
QR creation automatically publishes Questionnaire.

---

# 305. Core invariants

`QR-INV-020`  
QR is a Derived Artifact, not URL Source.

`QR-INV-021`  
Every QR originates from an already authorized canonical URL.

`QR-INV-022`  
Admin, filesystem, storage and private-network URLs are forbidden QR targets.

`QR-INV-023`  
Managed local media uses stable application-controlled URL.

`QR-INV-024`  
Ephemeral signed storage URL is not baseline final Questionnaire target.

`QR-INV-025`  
Casting-safe token scope matches intended audience.

`QR-INV-026`  
Canonicalization occurs before QR generation and preserves semantics.

`QR-INV-027`  
Hyperlink, PDF annotation and QR encode the same frozen target.

`QR-INV-028`  
Displayed URL text does not define the actual destination.

`QR-INV-029`  
QR does not own professional descriptions.

`QR-INV-030`  
`show_link` and `show_qr` are independent representation controls.

`QR-INV-031`  
QR is supplementary and does not replace accessible hyperlink baseline.

`QR-INV-032`  
Contact/vCard QR is not automatically generated baseline.

`QR-INV-033`  
QR bytes are rebuildable derived data.

`QR-INV-034`  
Vector SVG is preferred for professional PDF/print.

`QR-INV-035`  
Raster QR is rendered directly at target resolution without interpolated upscale.

`QR-INV-036`  
Quiet zone is at least four modules.

`QR-INV-037`  
Print module size is at least 0.4 mm baseline.

`QR-INV-038`  
Overall print size respects matrix density and is at least 20 mm baseline.

`QR-INV-039`  
Dense target increases QR physical size rather than shrinking modules.

`QR-INV-040`  
QR foreground/background remain scan-safe.

`QR-INV-041`  
Logo/stylized modules are excluded from baseline.

`QR-INV-042`  
Every production QR passes decode verification.

`QR-INV-043`  
Decoded value must equal expected canonical URL exactly.

`QR-INV-044`  
Redirect equivalence does not satisfy verification.

`QR-INV-045`  
Failed verification blocks READY state.

`QR-INV-046`  
Final rendered representation is what verification targets.

`QR-INV-047`  
PDF QR path has final-artifact regression coverage.

`QR-INV-048`  
Questionnaire snapshots freeze QR target and representation semantics.

`QR-INV-049`  
Current Source URL changes never rewrite historical QuestionnaireRevision.

`QR-INV-050`  
Historical re-render uses frozen target.

`QR-INV-051`  
New target creates different QR artifact identity.

`QR-INV-052`  
Cache key contains target fingerprint and rendering semantics.

`QR-INV-053`  
Sensitive target does not leak through cache key/path/log.

`QR-INV-054`  
QR cache/access is partitioned by target access class.

`QR-INV-055`  
Revocable sensitive QR does not receive irrevocable shared-public caching.

`QR-INV-056`  
Current alias/reference is generation-safe.

`QR-INV-057`  
QR generation is idempotent.

`QR-INV-058`  
Stale generation worker cannot overwrite newer current target.

`QR-INV-059`  
Requested QR must be READY before final PDF uses it.

`QR-INV-060`  
Requested but failed QR cannot silently disappear from final document.

`QR-INV-061`  
HTML/PDF clickable link remains distinct from QR image.

`QR-INV-062`  
Multiple QR codes are explicitly labeled.

`QR-INV-063`  
Builder can control representation only where allowed, never target value.

`QR-INV-064`  
Builder Generate revalidates current target eligibility.

`QR-INV-065`  
Builder GenerationSnapshot freezes target.

`QR-INV-066`  
Historical snapshot does not override later access revocation.

`QR-INV-067`  
Theme can style spacing/container only, not target/security geometry invariants.

`QR-INV-068`  
AI cannot create/change QR target.

`QR-INV-069`  
VOP can repair derived QR state only.

`QR-INV-070`  
Analytics tracking never silently rewrites QR target.

`QR-INV-071`  
QR is not Search or SEO authority.

`QR-INV-072`  
Public QR endpoint never acts as arbitrary anonymous QR generator.

`QR-INV-073`  
Internally generated QR SVG contains no executable/external content.

`QR-INV-074`  
Unreferenced QR artifacts can be purged/rebuilt.

`QR-INV-075`  
Source archive does not rewrite historical QR.

`QR-INV-076`  
Privacy purge discovers QR/PDF/cache copies and references.

`QR-INV-077`  
System does not claim recall of already distributed physical/external PDFs.

`QR-INV-078`  
Legacy QR must decode successfully before canonical reuse.

`QR-INV-079`  
Migration never trusts visual similarity or filename alone.

`QR-INV-080`  
Backup recoverability depends on frozen target/config, not QR bytes.

`QR-INV-081`  
Restore rebuilds derived QR only and cannot revive revoked access.

`QR-INV-082`  
All target/access, verification, snapshot, print and cache rules are server-enforced.

---

# 306. E2E-QR-001 — ProfessionalLink

Published eligible ProfessionalLink creates QR from exact canonical URL.

---

# 307. E2E-QR-002 — Exact decode

Generated QR decodes to exact canonical URL.

---

# 308. E2E-QR-003 — Different query

Expected:

```text
https://example.com/video?id=10
```

decoded:

```text
https://example.com/video?id=11
```

Verification fails.

---

# 309. E2E-QR-004 — Redirect difference

QR encodes redirecting short URL while expected target is destination URL.

Exact verification fails.

---

# 310. E2E-QR-005 — Admin URL

Attempt to generate QR for `/admin/...`.

Rejected.

---

# 311. E2E-QR-006 — Localhost

`http://localhost:3336/...`

Rejected.

---

# 312. E2E-QR-007 — Private network

`http://192.168.3.11/...`

Rejected.

---

# 313. E2E-QR-008 — Filesystem

`/data/kate-actor/...`

Rejected.

---

# 314. E2E-QR-009 — Storage locator

Raw storage locator rejected.

---

# 315. E2E-QR-010 — Signed temporary URL

Short-lived signed URL rejected for permanent Questionnaire QR.

---

# 316. E2E-QR-011 — Managed local video

MediaAsset resolves through stable approved application URL.

QR encodes that URL, not storage path.

---

# 317. E2E-QR-012 — Public Questionnaire

QR encodes official canonical Questionnaire public URL.

---

# 318. E2E-QR-013 — Profile

Profile QR encodes exact official public Profile URL.

---

# 319. E2E-QR-014 — show link + QR

Questionnaire item renders clickable link and verified QR.

Both targets equal.

---

# 320. E2E-QR-015 — link only

`show_link=true`, `show_qr=false`.

No QR dependency exists.

---

# 321. E2E-QR-016 — QR requested

`show_qr=true`.

Failed QR verification blocks final PDF rather than silently omitting QR.

---

# 322. E2E-QR-017 — PDF hyperlink

Generated PDF contains actual clickable annotation to same URL.

---

# 323. E2E-QR-018 — PDF QR decode

Render final PDF page.

Decode QR.

Result exactly equals expected frozen URL.

---

# 324. E2E-QR-019 — SVG

Generated SVG decodes successfully.

---

# 325. E2E-QR-020 — PNG

Generated PNG decodes successfully.

---

# 326. E2E-QR-021 — SVG safety

Generated SVG contains no script, remote reference or `foreignObject`.

---

# 327. E2E-QR-022 — Quiet zone

Rendered artifact contains at least four-module quiet zone.

---

# 328. E2E-QR-023 — Print module size

Physical layout calculation maintains ≥0.4 mm module size.

---

# 329. E2E-QR-024 — Dense URL

Long URL produces larger printed QR instead of undersized modules.

---

# 330. E2E-QR-025 — Small layout

Requested layout cannot fit scan-safe QR.

Validation blocks/warns rather than scaling below threshold.

---

# 331. E2E-QR-026 — JPEG

PDF pipeline does not convert QR to destructive JPEG representation.

---

# 332. E2E-QR-027 — Low contrast

Unsafe QR color configuration rejected.

---

# 333. E2E-QR-028 — Theme

Theme cannot reduce quiet zone or alter encoded URL.

---

# 334. E2E-QR-029 — Link updated

ProfessionalLink URL v1 changes to v2.

New current QR encodes v2.

---

# 335. E2E-QR-030 — Historical revision

Questionnaire R1 created with URL v1.

After Source becomes v2, R1 still renders/decodes v1.

---

# 336. E2E-QR-031 — New Questionnaire revision

R2 created after Source update.

R2 encodes v2.

---

# 337. E2E-QR-032 — Stale worker

QR job for v1 finishes after v2 is current.

It cannot become current alias.

---

# 338. E2E-QR-033 — Content-addressed cache

Same public URL/settings reuse same verified artifact logically.

---

# 339. E2E-QR-034 — Access-class cache

Tokenized casting-safe QR does not use shared public artifact endpoint/cache.

---

# 340. E2E-QR-035 — Revocation

Casting-safe target revoked.

Future access denied even though historical snapshot still records URL semantics.

---

# 341. E2E-QR-036 — Builder target tampering

Visitor modifies hidden request to arbitrary URL.

Server rejects/ignores; Source target remains authoritative.

---

# 342. E2E-QR-037 — Builder eligibility changed

Link loses Builder eligibility before Generate.

Generate rejects selection.

---

# 343. E2E-QR-038 — Builder snapshot

Successful GenerationSnapshot freezes exact link/QR target.

---

# 344. E2E-QR-039 — Analytics redirect

Analytics module cannot silently wrap frozen URL in tracker.

---

# 345. E2E-QR-040 — ShareLink explicit target

If Source/Questionnaire intentionally uses canonical ShareLink URL, QR encodes that exact ShareLink.

---

# 346. E2E-QR-041 — AI

AI recommends link inclusion.

QR generated only from existing validated Source URL.

---

# 347. E2E-QR-042 — VOP stale QR

VOP detects stale derived QR and regenerates.

ProfessionalLink URL unchanged.

---

# 348. E2E-QR-043 — Public arbitrary generator

Anonymous request:

```text
/api/qr?url=https://attacker.example
```

does not generate QR.

---

# 349. E2E-QR-044 — Logging

Tokenized target does not appear raw in standard generation logs.

---

# 350. E2E-QR-045 — Filename

Downloaded QR filename excludes token/query secrets.

---

# 351. E2E-QR-046 — Existing legacy QR correct

Legacy QR decodes exactly to expected canonical target.

May be classified reusable after other validations.

---

# 352. E2E-QR-047 — Existing legacy QR mismatch

Decoded target differs.

Not reused.

---

# 353. E2E-QR-048 — Existing Admin QR

Legacy QR points to Admin URL.

Rejected.

---

# 354. E2E-QR-049 — Undecodable legacy QR

Marked for regeneration.

---

# 355. E2E-QR-050 — Migration idempotency

Running migration again does not duplicate artifacts/relations.

---

# 356. E2E-QR-051 — Artifact deleted

QR bytes removed from derived storage.

Frozen Questionnaire still allows identical regeneration.

---

# 357. E2E-QR-052 — Backup restore

Missing QR bytes regenerate from frozen snapshot.

---

# 358. E2E-QR-053 — Restore revoked access

Revoked token is not re-enabled.

---

# 359. E2E-QR-054 — Multiple QR

Two video links in PDF each have unique nearby descriptions and exact targets.

---

# 360. E2E-QR-055 — Accessibility

Digital PDF/HTML contains usable clickable text link in addition to QR.

---

# 361. E2E-QR-056 — Row order

External-link table reorder changes displayed numbering but not target/artifact identity.

---

# 362. E2E-QR-057 — Source description edit

Description changes.

QR target remains same; QR bytes may be reused.

---

# 363. E2E-QR-058 — Rendering profile update

Renderer/print-profile version changes.

New derived QR artifact can be generated without changing Source URL.

---

# 364. E2E-QR-059 — Current/public access

Public QR artifact endpoint does not expose tokenized/private artifact.

---

# 365. E2E-QR-060 — Full flow

ProfessionalLink  
→ Questionnaire Draft  
→ `show_link=true/show_qr=true`  
→ Publish Revision  
→ PDF generation  
→ click hyperlink  
→ decode printed/rendered QR  

Both resolve to the exact same frozen canonical URL.

---

# 366. Architecture diagram

```text
ProfessionalLink / Media Resolver / Profile URL
                    │
                    ▼
          Canonical URL Service
                    │
                    ▼
           Access Eligibility
                    │
                    ▼
              QR Service
           ┌────────┴────────┐
           ▼                 ▼
       Renderer          Fingerprint
           │
           ▼
     SVG / PNG Artifact
           │
           ▼
      Decode Verifier
           │
        PASS only
           │
           ▼
           READY
```

---

# 367. Questionnaire diagram

```text
Questionnaire Draft
        │
        ▼
Eligible Link Selected
        │
        ├── show_link
        └── show_qr
        │
        ▼
Publish
        │
        ▼
Immutable QuestionnaireRevision
        │
        ├── description
        ├── canonical_url
        ├── show_link
        └── show_qr
        │
        ▼
PDF / HTML Render
```

---

# 368. Historical URL diagram

```text
ProfessionalLink v1
URL = A
   │
   ▼
QuestionnaireRevision 1
frozen URL = A

ProfessionalLink changes
URL = B
   │
   ├──────────────► current QR = B
   │
   └──────────────► future Questionnaire = B

QuestionnaireRevision 1
still = A
```

---

# 369. Verification diagram

```text
Expected canonical URL
          │
          ▼
      QR Generate
          │
          ▼
     Final SVG/PNG
          │
          ▼
        Decode
          │
          ▼
decoded === expected ?
      │           │
     yes          no
      │           │
    READY    FAILED_VERIFICATION
```

---

# 370. Print geometry diagram

```text
┌───────────────────────────┐
│     quiet zone ≥ 4        │
│   ┌───────────────────┐   │
│   │                   │   │
│   │    QR modules     │   │
│   │                   │   │
│   └───────────────────┘   │
│     quiet zone ≥ 4        │
└───────────────────────────┘

module size ≥ 0.4 mm
overall baseline ≥ 20 mm
```

---

# 371. Access boundary diagram

```text
Storage file
    │
    ✗ raw storage URL
    │
    ▼
Application Resolver
    │
    ▼
Public / Casting-safe URL
    │
    ▼
QR

Admin/Internal/Filesystem
        ✗
        └── never QR target
```

---

# 372. Cache diagram

```text
canonical URL
 + rendering profile
 + renderer version
 + access class
        │
        ▼
 target fingerprint
        │
        ▼
 immutable QR artifact

URL changes
    ↓
new fingerprint
    ↓
new artifact

Old historical artifact remains separate.
```

---

# 373. Builder security diagram

```text
Builder Visitor
      │
      ▼
Eligible items only
      │
      ├── toggle show_link
      └── toggle show_qr
      │
      ✗ cannot edit URL
      │
      ▼
Generate
      │
      ▼
Server revalidation
      │
      ▼
Frozen GenerationSnapshot
```

---

# 374. Quality gate

Перед implementation должны быть подтверждены:

- [ ] canonical QR target classes;
- [ ] forbidden Admin/internal/storage target classes;
- [ ] stable public media resolver;
- [ ] casting-safe target policy;
- [ ] canonical URL service;
- [ ] exact link/QR equality rule;
- [ ] `show_link` / `show_qr`;
- [ ] QR supplementary-accessibility rule;
- [ ] no automatic contact/vCard QR baseline;
- [ ] QRArtifact model;
- [ ] content-addressed fingerprint;
- [ ] SVG support;
- [ ] PNG support;
- [ ] vector PDF preference;
- [ ] crisp module rendering;
- [ ] quiet zone ≥4 modules;
- [ ] ≥0.4 mm print module size;
- [ ] ≥20 mm baseline physical QR size;
- [ ] dynamic size for dense URLs;
- [ ] scan-safe contrast;
- [ ] controlled error-correction setting;
- [ ] no logo/stylization baseline;
- [ ] runtime decode verification;
- [ ] exact decoded-string comparison;
- [ ] final-render verification;
- [ ] PDF-render E2E decode;
- [ ] QuestionnaireRevision frozen QR semantics;
- [ ] Builder GenerationSnapshot frozen QR semantics;
- [ ] historical/current URL separation;
- [ ] generation-safe current alias;
- [ ] access-class-aware caching;
- [ ] token-safe cache/logging;
- [ ] PDF annotation integration;
- [ ] no silent omission;
- [ ] idempotent generation;
- [ ] background jobs;
- [ ] VOP safe regeneration;
- [ ] no AI target authority;
- [ ] no analytics target rewriting;
- [ ] no public arbitrary QR generator;
- [ ] SVG security;
- [ ] purge/reference discovery;
- [ ] migration decode verification;
- [ ] backup/restore from Source/Snapshot;
- [ ] deterministic E2E coverage.

---

# 375. Acceptance criteria

`AC-QR-001`  
QR является Derived Artifact и не является URL Source.

`AC-QR-002`  
QR создаётся только из уже разрешённого canonical URL.

`AC-QR-003`  
Admin/internal/filesystem/storage/private-network URLs запрещены.

`AC-QR-004`  
Локальный MediaAsset использует stable application-controlled public/casting-safe URL.

`AC-QR-005`  
Короткоживущие signed storage URLs не используются в долговременных Questionnaire QR baseline.

`AC-QR-006`  
Tokenized casting-safe target имеет scope, соответствующий целевой аудитории.

`AC-QR-007`  
Canonicalization завершается до QR generation и не меняет destination semantics.

`AC-QR-008`  
HTML hyperlink, PDF annotation и QR одного frozen item используют идентичный canonical target.

`AC-QR-009`  
Каждый поддерживаемый Questionnaire link имеет независимые `show_link` и `show_qr`.

`AC-QR-010`  
QR не является единственным способом открыть ресурс в HTML/PDF baseline.

`AC-QR-011`  
Contact/vCard QR не создаётся автоматически.

`AC-QR-012`  
SVG является preferred professional PDF/print format.

`AC-QR-013`  
Raster QR не увеличивается интерполяцией из малого исходника.

`AC-QR-014`  
Quiet zone составляет минимум 4 QR modules с каждой стороны.

`AC-QR-015`  
Минимальный print module baseline составляет 0.4 mm.

`AC-QR-016`  
Минимальный overall print size составляет не менее 20 mm и увеличивается при более плотной matrix.

`AC-QR-017`  
QR имеет scan-safe foreground/background contrast.

`AC-QR-018`  
Logo overlays и декоративная стилизация QR не входят в baseline.

`AC-QR-019`  
Каждый production QR проходит decode verification.

`AC-QR-020`  
Decoded value обязан точно совпадать с expected canonical URL.

`AC-QR-021`  
Redirect equivalence не считается успешной verification.

`AC-QR-022`  
Verification failure блокирует READY и использование в финальном PDF.

`AC-QR-023`  
Verification проверяет финальную rendered representation.

`AC-QR-024`  
PDF E2E тест декодирует QR из фактического финального rendering path.

`AC-QR-025`  
QuestionnaireRevision хранит frozen canonical URL + `show_link` + `show_qr`.

`AC-QR-026`  
Изменение текущего ProfessionalLink URL не переписывает историческую QuestionnaireRevision.

`AC-QR-027`  
Historical re-render использует frozen URL.

`AC-QR-028`  
Different target creates different logical QR artifact identity.

`AC-QR-029`  
QR cache key включает target fingerprint и rendering profile/version.

`AC-QR-030`  
Sensitive target не раскрывается через cache key/path/log.

`AC-QR-031`  
Public и casting-safe/private QR caches/access policies разделены.

`AC-QR-032`  
Current QR alias/reference generation-safe и не может быть переписан stale worker.

`AC-QR-033`  
QR generation является idempotent.

`AC-QR-034`  
PDF renderer не встраивает unverified requested QR.

`AC-QR-035`  
Ошибка requested QR не приводит к silent omission в финальном документе.

`AC-QR-036`  
PDF hyperlink остаётся actual clickable annotation независимо от QR.

`AC-QR-037`  
Несколько QR на странице имеют однозначные adjacent labels.

`AC-QR-038`  
Public Builder пользователь может менять только разрешённую representation, но не canonical URL.

`AC-QR-039`  
Builder Generate повторно проверяет current item eligibility и target safety.

`AC-QR-040`  
Builder GenerationSnapshot freezing обеспечивает historical consistency.

`AC-QR-041`  
Historical semantic snapshot не отменяет последующую security revocation.

`AC-QR-042`  
Theme не может изменить URL, quiet-zone minimum, verification или access class.

`AC-QR-043`  
AI не может создавать или менять QR target.

`AC-QR-044`  
VOP может regenerate/verify только derived QR artifact.

`AC-QR-045`  
Analytics не может silently wrap canonical target tracking redirect.

`AC-QR-046`  
QR не изменяет Search/SEO eligibility.

`AC-QR-047`  
Anonymous arbitrary URL→QR generation endpoint отсутствует.

`AC-QR-048`  
Generated SVG не содержит script/external executable references.

`AC-QR-049`  
QR access никогда не шире access class его target context.

`AC-QR-050`  
Source archive не переписывает historical QR.

`AC-QR-051`  
Privacy purge выполняет discovery QR/PDF/cache references.

`AC-QR-052`  
Система не обещает revoke уже физически распространённый PDF/print artifact.

`AC-QR-053`  
Legacy QR переиспользуется только после успешного decode-to-exact-canonical verification.

`AC-QR-054`  
Legacy unsafe/expired/undecodable QR regenerates or is rejected.

`AC-QR-055`  
Backup recoverability основана на Source/Snapshot/configuration, а не на сохранности QR bytes.

`AC-QR-056`  
Restore rebuilds derived QR without changing frozen professional targets or reactivating revoked access.

`AC-QR-057`  
Все URL equality, target access, PDF, print-density, cache, snapshot и verification invariants имеют deterministic E2E coverage.

---

# 376. Финальная доктрина

> **QR Links Module является строго derived, access-aware и verification-first слоем. Система сначала получает уже разрешённый canonical URL из owning Source или immutable Questionnaire/Builder Snapshot и только затем строит QR. Обычный hyperlink, PDF annotation и QR одного элемента всегда должны вести на один и тот же exact frozen target. Admin, filesystem, raw storage, localhost/private-network и нестабильные signed URLs не могут использоваться как professional QR destinations. QR строится преимущественно в vector SVG, сохраняет минимум четыре модуля quiet zone, достаточный физический module size и scan-safe contrast, после чего обязательно декодируется обратно; только exact equality decoded value с expected canonical URL переводит artifact в `READY`. QuestionnaireRevision и Builder GenerationSnapshot замораживают URL/`show_link`/`show_qr`, поэтому последующее изменение текущего Source создаёт новый QR, но никогда не переписывает исторический документ. Cache/addressing являются target- и access-class-aware, stale workers не могут перезаписать current alias, а private/casting-safe QR не попадают в shared public cache. QR остаётся дополнительным способом доступа и не заменяет кликабельную ссылку, не создаёт permission, не меняет Search/SEO, не становится tracking redirect автоматически и не предоставляет AI/VOP/Theme права менять destination.**