# PDF AND DOCUMENT ARCHITECTURE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная архитектура HTML/PDF-документов, рендера, QR, ссылок и исторической воспроизводимости

**Целевой файл:** `docs/architecture/pdf.md`  
**Документ:** DOC-075  
**Статус:** ✅ Completed  
**Тип:** Architecture / Documents / PDF / Rendering

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/product-principles.md`
- `docs/ux/public-questionnaire-builder.md`
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
- `docs/architecture/media.md`

---

# 1. Назначение документа

Настоящий документ определяет целевую архитектуру профессиональных документов системы:

```text
HTML Questionnaire
Prepared Questionnaire PDF
Public Builder PDF
Casting-Specific Questionnaire PDF
Admin Preview
Historical Revision Rendering
```

Документ фиксирует:

1. единый semantic Document Model;
2. источники документов;
3. Draft/Revision/Snapshot boundary;
4. HTML/PDF consistency;
5. обязательную структуру анкеты;
6. first-page hierarchy;
7. правила Main Portfolio;
8. Emotional Grid integration;
9. video/audio/external links;
10. hyperlink architecture;
11. QR embedding;
12. stable URL requirements;
13. locale;
14. media embedding;
15. print layout;
16. accessibility;
17. metadata;
18. filenames;
19. background rendering;
20. idempotency;
21. validation;
22. artifact lifecycle;
23. caching;
24. security;
25. private casting packages;
26. historical reproduction;
27. error/retry behaviour;
28. test requirements.

---

# 2. Главный принцип

> **HTML и PDF являются двумя представлениями одного и того же профессионального документа, а не двумя независимыми реализациями анкеты.**

Canonical:

```text
IMMUTABLE DOCUMENT SOURCE
          ↓
SEMANTIC DOCUMENT MODEL
       ┌──┴───┐
       ↓      ↓
      HTML    PDF
```

---

# 3. Document IDs

Используются:

```text
DOCARCH-*
PDF-*
HTMLDOC-*
DOCMODEL-*
DOCVAL-*
PDF-QR-*
PDF-A11Y-*
```

Invariants:

```text
DOC-INV-*
```

---

# 4. Поддерживаемые источники документа

Документ может строиться только из определённого source type.

## Prepared Questionnaire

```text
QuestionnaireRevision
```

## Public Builder

```text
BuilderGenerationSnapshot
```

## Admin Draft Preview

```text
Questionnaire Draft
+
current validated Source projection
```

## Historical Rendering

```text
exact QuestionnaireRevision
```

---

# 5. DOC-INV-001 — PDF Never Reads Arbitrary Current Profile

После создания `QuestionnaireRevision` или `BuilderGenerationSnapshot` PDF renderer MUST NOT подмешивать текущие mutable Profile facts.

---

# 6. Correct Prepared Flow

```text
Questionnaire Draft
      ↓
Readiness
      ↓
Publish
      ↓
QuestionnaireRevision
      ↓
Document Model
      ↓
HTML / PDF
```

---

# 7. Correct Builder Flow

```text
Builder Session
      ↓
Server Revalidation
      ↓
BuilderGenerationSnapshot
      ↓
Document Model
      ↓
PDF
```

---

# 8. Incorrect Flow

```text
PDF Worker
 ↓
read questionnaire
 ↓
read "latest" Profile
 ↓
render
```

Prohibited.

---

# 9. Semantic Document Model

System MUST define renderer-neutral representation:

```text
DocumentModel
```

Document Model expresses **meaning and ordering**, not browser DOM screenshots.

---

# 10. Conceptual Document Model

```text
Document
├─ Metadata
├─ Identity
├─ Sections[]
│  ├─ Facts
│  ├─ Images
│  ├─ Skills
│  ├─ Languages
│  ├─ Emotional
│  ├─ Projects
│  ├─ Training
│  ├─ MediaLinks
│  └─ Contacts
└─ Footer
```

---

# 11. Document Model Responsibilities

It MUST define:

```text
content
semantic section type
ordering
locale
text
approved media
links
QR intent
document metadata
```

---

# 12. Document Model Must Not Define

Renderer-specific implementation such as:

```text
React component state
browser scroll
screen viewport
admin controls
database entity objects
```

---

# 13. DOC-INV-002 — Document Model Is Read-Only

Renderer cannot mutate:

```text
Profile
Questionnaire
Builder Session
Media
Contact
```

---

# 14. Document Model Builder

Canonical component:

```text
DocumentModelBuilder
```

takes exact Source:

```text
QuestionnaireRevision
or
BuilderGenerationSnapshot
```

and produces a validated model.

---

# 15. Prepared vs Builder Models

Both SHOULD converge on the same normalized semantic structure wherever possible.

This ensures:

```text
consistent section meanings
consistent media handling
consistent QR logic
consistent accessibility
consistent PDF layout engine
```

---

# 16. Variant Differences

Differences belong configuration/content selection:

```text
Compact
Extended
Casting
Custom
Builder Quick
Builder Standard
Builder Extended
```

not separate rendering engines.

---

# 17. Canonical Questionnaire Semantic Order

Unless an explicitly configured valid questionnaire variant changes optional ordering, canonical order is:

```text
1 Identification
2 Primary Close-Up
3 Primary Full Body
4 Professional Parameters
5 Languages
6 Skills
7 Video
8 Emotional Range
9 Projects / Roles
10 Training / Achievements
11 Additional Media / Links
12 Contacts
```

---

# 18. Mandatory Minimum

Every generated actor questionnaire MUST contain:

```text
Identification
+
eligible/published Main Portfolio Primary Close-Up
+
eligible/published Main Portfolio Primary Full Body
+
official Profile URL
```

---

# 19. DOC-INV-003 — Required Photos Cannot Be Substituted

Forbidden substitutions:

```text
Project photo → Full Body
Role photo → Close-Up
Emotional photo → Close-Up
Emotional Grid → Full Body
```

Required Close-Up and Full Body come from **Main Portfolio**.

---

# 20. First Page Doctrine

The first PDF page is a professional decision page.

It SHOULD expose as much P0 information as layout safely permits without visual overload.

---

# 21. First Page Priority

Canonical:

```text
Actor identity
Primary Close-Up
Primary Full Body
essential professional facts
languages / key skills where compact
Video/Questionnaire/Profile links where applicable
```

---

# 22. No Decorative Cover Page

A purely decorative cover that delays useful professional information is prohibited by default.

---

# 23. First Page Must Not Become Dense Spreadsheet

Professional hierarchy takes priority over fitting every field.

---

# 24. Page Overflow

If content does not fit:

```text
continue naturally to next page
```

Never shrink important text/photos to unreadable size merely to force one page.

---

# 25. Primary Close-Up Rendering

Must retain the approved professional framing.

No renderer-specific aggressive crop.

---

# 26. Primary Full Body Rendering

Must visibly retain the complete approved full-body framing.

A PDF template MUST NOT accidentally crop it into portrait/headshot format.

---

# 27. Additional Portfolio

Optional Portfolio items may follow according to Questionnaire configuration.

---

# 28. Portfolio Image Source

PDF uses revision/snapshot-bound derivative/reference.

Not current Media relation.

---

# 29. Media Quality

PDF renderer SHOULD request a derivative sufficient for intended printed/display size.

---

# 30. No Original Overuse

Do not embed huge source originals when appropriately sized high-quality PDF derivative exists.

---

# 31. No Upscaling Beyond Useful Fidelity

System SHOULD avoid taking a very small derivative and visually enlarging it beyond its intended resolution.

---

# 32. Emotional Questionnaire Section

Canonical content:

```text
approved Emotional Grid composite
shooting date
clickable link to full Emotional Portfolio
```

---

# 33. Emotional Section Must Not Include

By default:

```text
individual Emotional source photo dump
AI interpretation of expressions
unapproved grid
unconfirmed cells
```

---

# 34. Emotional Grid Geometry

Grid artifact is embedded as finalized composite.

PDF renderer does not rebuild/reorder individual cells independently.

---

# 35. Shooting Date

Must use actual stored `EmotionalSession.shooting_date`.

Not:

```text
upload date
EXIF date guessed as truth
grid creation date
```

---

# 36. Projects Section

May contain selected:

```text
Project
Role
year/date
short approved description
approved role/project image
```

according to Revision/Snapshot.

---

# 37. Project Snapshot

Historical PDF must retain Project values captured for that Revision.

---

# 38. Training

Rendered as standalone professional section.

Training MUST NOT appear disguised as Project `Other`.

---

# 39. Achievements

Can be:

```text
standalone section
or
compact subsection with Training
```

depending template.

Semantic distinction remains.

---

# 40. Skills

Must preserve stored 5-level semantics.

---

# 41. Skill Rendering

Visual bars/icons MAY supplement but MUST NOT be sole representation.

Include textual/programmatic level.

---

# 42. Languages

Must render explicit language + CEFR/native status.

Example:

```text
English — B2
```

---

# 43. Video/Audio/External Links

Documents MUST support the required table/list semantics.

---

# 44. External Link Editor Contract

The source configuration supports exact three-column logical fields:

```text
№
Description
URL
```

Document presentation MAY render these intelligently rather than literal spreadsheet table if semantic information remains equivalent.

---

# 45. Link Rendering

Every included professional URL SHOULD support:

```text
human-readable description
clickable hyperlink
visible destination where appropriate
optional QR
```

---

# 46. DOC-INV-004 — Clickable HTML and PDF Links

Configured links intended for professional use MUST remain clickable in both HTML and PDF where output format supports hyperlinks.

---

# 47. Link Annotation

PDF renderer MUST create actual PDF link annotations, not only blue underlined text.

---

# 48. Visible URL

For professional/printed durability, document SHOULD expose the URL text where layout permits, especially for critical links.

QR alone is insufficient.

---

# 49. Link Text

Use meaningful:

```text
Видеовизитка
Полное эмоциональное портфолио
Профиль актрисы
```

rather than:

```text
Нажмите здесь
Link 1
```

where context allows.

---

# 50. URL Canonicalization

Document Model MUST receive canonical URLs from the proper resolver/source.

Renderer must not invent/modify URL.

---

# 51. Unsafe URL Schemes

Reject for public professional documents:

```text
javascript:
file:
data: where inappropriate
internal schemes
```

Only explicit safe scheme allowlist.

---

# 52. Official Profile Link

Every generated actor questionnaire includes the official Profile URL.

It SHOULD be:

```text
clickable
visible
stable
```

and MAY include QR.

---

# 53. QR Architecture in Documents

QR is generated from the **exact canonical URL** used by corresponding link.

---

# 54. QR Generation Pipeline

```text
Document Link
    ↓
Canonical URL
    ↓
QR Eligibility
    ↓
QR Generator
    ↓
Decode Validation
    ↓
QR Artifact
    ↓
Document Renderer
```

---

# 55. DOC-INV-005 — QR Exactness

Required:

```text
decode(QR) === canonical URL
```

byte-for-character according to canonical serialization.

---

# 56. QR Must Not Encode

```text
/admin URLs
filesystem paths
temporary preview URLs
short-lived signed URLs
private unscoped storage URLs
unsafe redirect URLs
```

---

# 57. Token-Scoped Casting QR

A protected casting-specific package MAY have QR only if target is deliberately shareable in that security scope.

It must use:

```text
stable application route
opaque token
defined expiry/revocation
```

---

# 58. Public vs Private QR

Document access class determines QR eligibility.

---

# 59. Per-Link QR Configuration

Questionnaire supports:

```text
show_link
show_qr
```

independently for eligible links.

---

# 60. QR Cannot Replace Hyperlink

If QR displayed:

corresponding accessible textual link/URL MUST also exist.

---

# 61. QR Print Scannability

Renderer MUST enforce minimum physical size.

Implementation SHOULD choose a product-safe minimum based on QR density and print testing rather than arbitrary scaling.

---

# 62. QR Quiet Zone

Mandatory quiet zone around QR.

Template/theme cannot remove it.

---

# 63. QR Contrast

Use strong foreground/background contrast.

Decorative theme styling MUST NOT reduce scannability.

---

# 64. QR Vector Preference

For PDF:

```text
SVG/vector
```

or sufficiently high-resolution equivalent SHOULD be preferred.

---

# 65. QR Raster Scaling

If raster QR used:

must not be resampled so heavily that modules become blurred.

---

# 66. QR Label

QR SHOULD have meaningful nearby text:

```text
Открыть видеовизитку
```

rather than isolated square.

---

# 67. QR Accessibility

Screen-reader/text equivalent describes destination.

QR image itself is never the only means of accessing content.

---

# 68. QR Failure

If configured required QR cannot be generated or validated:

```text
document readiness/generation = BLOCKED
```

---

# 69. Optional QR Failure

If QR explicitly optional:

policy MAY omit QR with warning, but hyperlink remains.

For published configured output, preferred approach is to fail generation rather than silently change approved document configuration.

---

# 70. HTML/PDF Consistency

Semantic content MUST match.

---

# 71. Consistency Means

Same Revision/Snapshot should produce equivalent:

```text
facts
section inclusion
ordering
photos
links
QR targets
contact values
locale
```

---

# 72. Permitted Layout Differences

HTML and PDF MAY differ in:

```text
pagination
column count
page-break handling
navigation affordances
screen-only vs print-only chrome
```

---

# 73. Prohibited Semantic Differences

Example:

```text
HTML says English C1
PDF says B2
```

from same Revision.

Prohibited.

---

# 74. Shared Model

Preferred:

```text
Revision
 ↓
DocumentModel
 ├─ HTML Renderer
 └─ PDF Renderer
```

---

# 75. Separate Data Fetchers Prohibited

Avoid:

```text
HTML renderer fetches Profile independently
PDF renderer fetches Profile independently
```

---

# 76. Admin Preview

Admin Draft Preview MAY use current Draft model rather than immutable Revision.

It MUST be labelled:

```text
Draft Preview
```

---

# 77. Draft Preview ≠ Published Output

Draft preview can change as Source/Draft changes.

---

# 78. Public Preview

When Admin asks for real public preview of published Questionnaire:

use actual public Revision projection.

---

# 79. Document Locale

Every generated document has explicit locale.

---

# 80. Locale Codes

Use stable locale identifiers, e.g.:

```text
ru
en
```

according to application standards.

---

# 81. Locale Snapshot

Published Revision/Snapshot determines exact localized content used.

---

# 82. Missing Translation

System MUST NOT automatically fabricate professional translation during rendering.

---

# 83. Locale Fallback

If allowed:

```text
requested approved translation
→ explicitly defined approved fallback
```

Readiness may warn/block depending template/publication requirement.

---

# 84. PDF Language Metadata

Document language SHOULD be encoded in PDF metadata/tagging where renderer supports.

---

# 85. Mixed Language

Specific names/roles/titles may retain original language.

Semantic `lang` metadata SHOULD be supported where practical.

---

# 86. Document Metadata

PDF SHOULD contain safe metadata such as:

```text
Title
Author/Subject as appropriate
Language
Creation date
document identifier
revision/generation identifier internally
```

---

# 87. Private Metadata

PDF metadata MUST NOT expose:

```text
internal filesystem path
admin username unless explicitly intended
private database IDs unnecessarily
AI prompts
secrets
```

---

# 88. Generated Date

PDF footer/metadata SHOULD include generation/publication date according to document semantics.

For Builder:

```text
Generated date
```

For prepared Revision, publication date may also be relevant.

---

# 89. Footer

Canonical footer MAY include:

```text
official Profile URL
generated/published date
page number
document identity
```

according to template.

---

# 90. Public Profile Source

Builder PDF SHOULD explicitly identify official Profile as source/reference.

---

# 91. Filename Architecture

Filename must be:

```text
professional
predictable
safe
locale-aware
sanitized
```

---

# 92. Prepared Filename

Conceptually:

```text
{actor}-{questionnaire-type}-{locale}.pdf
```

---

# 93. Casting Filename

Conceptually:

```text
{actor}-{casting}-{role}-{locale}.pdf
```

where relevant.

---

# 94. Filename Sanitization

Must remove/normalize:

```text
path separators
control characters
reserved filesystem characters
excessive length
```

---

# 95. Filename Is Presentation Only

File identity does not depend on filename.

---

# 96. Artifact Identity

Canonical artifact should have internal immutable identity:

```text
artifact_id
```

plus:

```text
revision_id / snapshot_id
locale
renderer version
```

---

# 97. PDF Artifact Model

Conceptually:

```text
QuestionnaireArtifact
```

owns:

```text
artifact type
source revision/snapshot
locale
storage locator
checksum
file size
renderer version
generated_at
validation state
```

---

# 98. Artifact Source

Exactly one:

```text
QuestionnaireRevision
or
BuilderGenerationSnapshot
```

according to document type.

---

# 99. Artifact Immutability

Once an artifact is marked READY and distributed as revision-bound historical output, its binary SHOULD be immutable.

---

# 100. New Rendering Engine

If same Revision is rendered with new engine:

create new Artifact identity/version.

Do not silently overwrite archived historical binary where exact artifact retention matters.

---

# 101. Semantic vs Binary Revision

Document Revision and PDF renderer version are distinct.

---

# 102. Example

```text
Questionnaire Revision 8
PDF Artifact A — Renderer 1
PDF Artifact B — Renderer 2
```

Both may represent same semantic Revision.

---

# 103. Default Current Artifact

System MAY mark one PDF Artifact as current preferred render for Revision/locale.

Historical artifacts remain traceable as policy requires.

---

# 104. PDF Generation Processing State

Canonical:

```text
QUEUED
GENERATING
VALIDATING
READY
FAILED
```

---

# 105. Generation Job Input

Must contain exact:

```text
source_type
source_id
locale
document_model_version
renderer_version
artifact_type
idempotency_key
```

---

# 106. Idempotency Key

Conceptually:

```text
source snapshot/revision
+
locale
+
renderer/config version
+
artifact type
```

---

# 107. Duplicate Job

Must return/reuse existing valid Artifact or safely deduplicate execution.

---

# 108. Worker Race

If Revision 9 publishes while worker renders Revision 8:

Revision 8 worker continues using Revision 8 only.

---

# 109. No “Current Questionnaire” Job Input

Prohibited.

---

# 110. Background Rendering

PDF SHOULD normally render asynchronously when generation may exceed interactive response budget.

---

# 111. Public Builder UX

After Generate:

```text
GenerationSnapshot committed
→ rendering state
→ ready document
```

Session remains intact if worker fails.

---

# 112. Prepared Questionnaire Publication

Architecture may choose:

## Mode A — PDF required before `PUBLISHED`

or

## Mode B — Revision publishes and PDF generates immediately after.

---

# 113. Canonical Product Requirement

Because PDF is a core professional deliverable, publication readiness MUST ensure the system is capable of producing a valid PDF.

The precise transaction coupling is deferred to Questionnaire module architecture.

---

# 114. Previous Revision Safety

A new PDF generation failure MUST NOT destroy the previously valid current published Revision/artifact.

---

# 115. Document Model Validation

Before rendering, validate:

```text
schema
required sections
required photos
valid links
QR configuration
locale
media availability
security/access class
```

---

# 116. Mandatory Questionnaire Validation

Check:

```text
identity exists
Primary Close-Up exists
Primary Full Body exists
official Profile link valid
```

---

# 117. Photo Availability

Not enough that Portfolio record exists.

The required revision-bound media artifact/reference must be resolvable.

---

# 118. Document Link Validation

Structural validation:

```text
safe URL
canonical URL available
QR eligibility if requested
```

---

# 119. External Reachability

Temporary inability to verify remote target is distinct from structurally invalid URL.

Publication policy should avoid equating temporary network error with invalid professional URL without evidence.

---

# 120. QR Validation

Generation-time exact decode validation required.

---

# 121. PDF Binary Validation

Generated artifact SHOULD be checked for:

```text
file exists
non-zero size
valid PDF signature/parser
page count >= 1
expected metadata where applicable
```

---

# 122. Link Validation in Output

Testing SHOULD verify embedded PDF annotations exist and target exact expected URLs.

---

# 123. Text Validation

Critical professional text SHOULD remain extractable/selectable from PDF.

---

# 124. Image-Only PDF Prohibited

Official Questionnaire PDF MUST NOT be implemented as raster screenshots of pages.

---

# 125. Why

Image-only PDF breaks:

```text
accessibility
text selection
search
copying
link semantics
file efficiency
document structure
```

---

# 126. PDF Accessibility Target

Browser/document surfaces target WCAG-aligned accessibility; PDF generation SHOULD produce accessible/tagged output to the extent supported by selected renderer.

---

# 127. Accessible HTML Canonical Alternative

If selected renderer cannot initially provide fully tagged PDF:

the equivalent accessible HTML Questionnaire MUST remain available.

The gap MUST be tracked rather than pretending an image-only PDF is accessible.

---

# 128. PDF Accessibility Requirements

Where technically supported:

```text
document language
logical reading order
headings
paragraph semantics
real text
link annotations
alt text for meaningful images
table semantics
metadata
```

---

# 129. Meaningful Images

Primary actor images require meaningful accessible description.

Example contextual description:

```text
Екатерина Крыгина — крупный план
```

rather than filename.

---

# 130. Full Body Accessibility Label

Clearly identifies:

```text
Фото в полный рост
```

---

# 131. Emotional Grid Description

Should include nearby semantic description such as:

```text
Эмоциональный портфолио-сет, съёмка 12 июля 2026, сетка 4×4.
```

plus approved captions/labels where available.

---

# 132. No AI-Invented Emotional Description

Renderer cannot invent emotions from faces.

Only approved/source metadata may be included.

---

# 133. QR Accessibility

Every QR has corresponding accessible textual target.

---

# 134. Tables

Use real table semantics only for genuinely tabular content.

---

# 135. Link List vs Table

Professional links may be rendered as accessible list when more readable than a rigid table.

The canonical source still retains number/description/URL semantics.

---

# 136. Reading Order

Visual multi-column layout MUST preserve logical reading order.

---

# 137. Page Numbers

Should not disrupt screen-reader/document reading flow where tagging supports artifact semantics.

---

# 138. Contrast

PDF text/background MUST meet product accessibility contrast baseline.

---

# 139. Theme Influence

Theme may affect:

```text
typography
spacing
approved colors
decorative elements
```

but cannot weaken:

```text
contrast
text size/readability
QR scannability
link visibility
document structure
```

---

# 140. Theme Revision Binding

If visual Theme affects generated PDF materially, artifact SHOULD capture:

```text
theme revision / document template version
```

or use a separate document design system version.

---

# 141. Recommended Separation

Website Theme and Professional Document Template SHOULD be related but not blindly identical.

Reason:

```text
screen and print constraints differ
```

---

# 142. Theme Cannot Remove Required Content

No visual theme can hide:

```text
Primary Close-Up
Primary Full Body
official Profile URL
```

from required questionnaire.

---

# 143. Document Template Version

Each renderer layout/template SHOULD have explicit version.

---

# 144. Why Template Version Matters

It supports:

```text
reproducibility
debugging
migration
artifact comparison
```

---

# 145. Page Format

System SHOULD use one canonical professional page format per target market/configuration, e.g. A4 unless future requirement introduces alternatives.

Exact format is template configuration, not browser viewport.

---

# 146. Responsive Behaviour vs PDF

PDF is paginated print/document layout.

It MUST NOT be generated as a screenshot of:

```text
mobile viewport
desktop viewport
```

---

# 147. Public PDF Preview

Browser preview may scale PDF pages responsively.

The PDF binary itself remains fixed page layout.

---

# 148. Page Break Logic

Renderer MUST avoid poor splits where practical.

Examples:

```text
section heading alone at page bottom
QR separated from link description
photo caption on different page
small project card split awkwardly
```

---

# 149. Keep-Together Rules

Appropriate semantic units SHOULD use keep-together controls:

```text
photo + label
QR + link
project title + role
skill group
contact item
```

---

# 150. Oversized Content

When a semantic unit cannot fit whole page:

graceful split permitted according to component-specific layout.

---

# 151. Orphan/Widow Control

Professional text rendering SHOULD avoid obvious single-line page fragments.

---

# 152. Long URLs

Must wrap safely.

Do not:

```text
overflow page
shrink entire document
```

---

# 153. Long Names/Titles

Support RU/EN text without clipping.

---

# 154. Typography

Typography must prioritize professional readability.

No tiny text purely to increase density.

---

# 155. Font Availability

Renderer MUST use deployment-controlled, reliably available fonts or equivalent supported mechanism.

---

# 156. Font Licensing

Only fonts legally deployable/embeddable may be used.

---

# 157. Font Failure

PDF generation MUST NOT silently substitute a font that breaks Cyrillic or layout without validation.

---

# 158. Cyrillic Support

Mandatory.

---

# 159. English Support

Mandatory.

---

# 160. Mixed Cyrillic/Latin

Must render correctly in same document.

---

# 161. Embedded Fonts

Where selected rendering technology supports it, fonts SHOULD be embedded/subset appropriately to preserve appearance.

---

# 162. Media Embedding

PDF renderer fetches media only through trusted internal storage/delivery interface.

---

# 163. SSRF Boundary

Renderer MUST NOT freely fetch arbitrary user-provided URLs during document generation.

---

# 164. External Images

If external image embedding is supported:

must use controlled resolver/allowlist/download pipeline.

Default professional media should use managed MediaAssets.

---

# 165. Remote Video

Video is represented via:

```text
link
QR
poster/thumbnail where approved
```

not embedded as executable playable media inside PDF by default.

---

# 166. Audio

Same:

```text
description
hyperlink
QR optional
```

---

# 167. PDF Embedded Attachments

Not part of baseline product requirement.

Avoid unless explicitly justified.

---

# 168. JavaScript in PDF

Prohibited.

---

# 169. Forms in PDF

Interactive PDF form fields are not required.

Questionnaire is a professional presentation document, not a writable PDF form.

---

# 170. External Tracking in PDF

Document should not depend on invisible tracking resources to render.

---

# 171. Analytics for PDF

Download/open-link events may be tracked at application URL layer.

The PDF itself need not contain invasive tracking.

---

# 172. Link Attribution

Shared/marketing URLs MAY include approved attribution parameters when intentionally generated.

---

# 173. QR Attribution

QR may encode an attributed canonical/shared URL only when that URL is intentionally the actual target.

Decode equality remains exact.

---

# 174. QR Redirect Services

Avoid unnecessary third-party QR shorteners.

Application-controlled stable routes preferred.

---

# 175. Document Security Classification

Every document artifact MUST be classified:

```text
PUBLIC
TOKEN_SCOPED
ADMIN_ONLY
TEMPORARY_SESSION
```

---

# 176. PUBLIC

Example:

```text
Primary published questionnaire
```

---

# 177. TOKEN_SCOPED

Example:

```text
casting-specific private questionnaire
```

---

# 178. ADMIN_ONLY

Examples:

```text
Draft preview
internal unpublished document
```

---

# 179. TEMPORARY_SESSION

Builder-generated artifact may use temporary/session-specific access policy according to product design.

---

# 180. Access Class Is Server Authority

Filename/obscure URL does not define privacy.

---

# 181. PDF Storage

Artifacts MUST use managed storage.

---

# 182. Public Artifact Delivery

Published public PDF may use stable public route.

---

# 183. Private Artifact Delivery

Requires:

```text
session/token/auth verification
```

before binary delivery.

---

# 184. Cache Keys

Must include:

```text
source Revision/Snapshot
locale
renderer/template version
access class where relevant
```

---

# 185. DOC-INV-006 — No Public/Private Cache Collision

A private Casting PDF must never be served from a generic public artifact cache entry.

---

# 186. Current Questionnaire Alias

A stable route can resolve:

```text
logical Questionnaire
→ current published Revision
→ current preferred artifact
```

---

# 187. Revision-Specific Route

If supported:

```text
Revision ID
→ exact Revision artifact
```

immutable except access/redaction policy.

---

# 188. Broken Current Artifact

If current PDF artifact fails/corrupts:

system MAY regenerate from immutable Revision.

---

# 189. Regeneration

Must use same Revision/Snapshot.

Not current live Profile.

---

# 190. Historical Binary

If stored historical PDF exists and is valid, regeneration is not required for ordinary serving.

---

# 191. PDF Checksum

Artifact SHOULD store checksum.

---

# 192. Integrity

On high-value archival verification:

```text
stored checksum
==
actual PDF checksum
```

---

# 193. Backup

Published artifacts SHOULD be included in appropriate backup tier.

---

# 194. Regenerable vs Retained

Even when semantically regenerable, previously distributed PDF may be retained because:

```text
renderer
font
template
```

can evolve.

---

# 195. Builder Artifact Retention

May be shorter.

Exact duration belongs retention policy.

---

# 196. Deleted/Expired Builder Session

Must not accidentally expose old artifact after access authorization expires.

---

# 197. Private Casting Token Revocation

Revocation blocks future delivery immediately even though binary remains stored.

---

# 198. Document Redaction

Privacy/security exceptional redaction can prevent serving or generate a redacted replacement according to policy.

It MUST NOT silently rewrite historical source semantics.

---

# 199. Logging

Renderer logs SHOULD include:

```text
artifact ID
source revision/snapshot
locale
renderer version
job ID
correlation ID
failure category
```

---

# 200. Logging Must Not Include

```text
secret access tokens
private full casting text unnecessarily
signed URL credentials
raw OAuth secrets
```

---

# 201. Observability Metrics

Recommended:

```text
PDF generation latency
queue latency
success/failure rate
page count distribution
artifact size
QR validation failures
media resolution failures
renderer crashes
```

---

# 202. VOP Integration

VOP MAY detect:

```text
stale/missing PDF artifact
failed generation
invalid QR
broken document link
missing required photo
render incompatibility
accessibility validation issue
```

---

# 203. VOP Safe Automation

AUTO-4 examples:

```text
regenerate missing derivative
retry PDF generation
revalidate QR
rebuild preview
```

---

# 204. Human-Controlled Document Changes

Require Human authority:

```text
change questionnaire content
remove required block
change Contact inclusion
change Project selection
publish new Revision
```

---

# 205. Document Renderer Cannot “Fix” Content

If a required field is missing:

renderer reports/blockers.

It MUST NOT invent or substitute content.

---

# 206. Example — Missing Full Body

Correct:

```text
Generation blocked:
Primary Full Body missing
```

Wrong:

```text
Use nearest-looking Project photo automatically
```

---

# 207. Example — Broken Link

Correct:

```text
link validation issue
admin decision/retry
```

Wrong:

```text
silently remove link from approved revision
```

---

# 208. Historical Broken Link

Historical PDF stays historical.

System may:

```text
maintain redirect
warn Admin
publish newer Revision
```

---

# 209. PDF Preview Consistency

Admin preview SHOULD use same semantic model and document template as final PDF as closely as technically practical.

---

# 210. Preview Caveat

If browser preview is HTML simulation rather than rendered PDF:

UI MUST avoid implying pixel-perfect identity unless actual PDF preview is shown.

---

# 211. Recommended Preview Modes

```text
Document Preview
PDF Preview
```

may be distinct if needed.

---

# 212. Actual PDF Preview

For final readiness, Admin SHOULD be able to inspect actual generated PDF.

---

# 213. Print Verification

Critical templates SHOULD be physically/virtually tested at intended page format.

---

# 214. QR Print Test

Tests should include actual or rendered scan at minimum supported physical QR size.

---

# 215. Accessibility Validation

Automated checks alone are insufficient.

Manual reading-order/link/text selection testing required in testing plan.

---

# 216. Document Validation Pipeline

Canonical:

```text
SOURCE
 ↓
MODEL BUILD
 ↓
MODEL VALIDATE
 ↓
MEDIA RESOLVE
 ↓
QR GENERATE/VALIDATE
 ↓
RENDER
 ↓
PDF PARSE/VALIDATE
 ↓
SEMANTIC CHECKS
 ↓
READY
```

---

# 217. Model Validation Failure

No renderer execution required.

---

# 218. Media Resolution Failure

If mandatory media missing:

generation fails with explicit cause.

---

# 219. QR Failure

Failure before final artifact READY.

---

# 220. Renderer Failure

Artifact state:

```text
FAILED
```

Source Revision/Snapshot stays valid.

---

# 221. Post-Render Validation Failure

Generated binary must not be published/served as READY.

---

# 222. Atomic Artifact Publication

Recommended:

```text
render temp binary
↓
validate
↓
write/register final artifact
↓
mark READY
```

---

# 223. No Half-Written PDF

Binary should not be publicly exposed while still being generated.

---

# 224. Error Categories

Recommended:

```text
DOCUMENT_MODEL_INVALID
DOCUMENT_REQUIRED_CONTENT_MISSING
DOCUMENT_MEDIA_UNAVAILABLE
DOCUMENT_LINK_INVALID
DOCUMENT_QR_INVALID
DOCUMENT_RENDER_FAILED
DOCUMENT_FONT_FAILED
DOCUMENT_OUTPUT_INVALID
DOCUMENT_STORAGE_FAILED
DOCUMENT_ACCESS_DENIED
DOCUMENT_SOURCE_EXPIRED
```

---

# 225. Error Copy

Admin sees actionable:

```text
«PDF не создан: отсутствует опубликованное фото в полный рост.»
```

not raw renderer stack trace.

---

# 226. Public Error

Public user receives concise recoverable state.

No internal infrastructure details.

---

# 227. Retry

Retry safe when:

```text
source immutable
same idempotency input
```

---

# 228. Failed Builder Generation

Session remains editable.

---

# 229. Failed Prepared Generation

Previous published artifact remains unaffected.

---

# 230. Renderer Selection

Specific renderer technology is not fixed by DOC-075.

Selection must satisfy:

```text
Cyrillic
links
images
print layout
repeatability
accessibility capabilities
headless production stability
```

---

# 231. Browser Print-to-PDF

May be implementation strategy only if:

- generated from immutable Document Model;
- deterministic;
- server-side;
- actual links preserved;
- accessibility goals supported;
- no dependency on interactive viewport state.

---

# 232. Screenshot PDF Prohibited

Raster screenshot pipeline is not acceptable as official implementation.

---

# 233. HTML Renderer

HTML document may be built from the same model using application UI components.

It MUST preserve accessibility and semantic headings.

---

# 234. HTML Canonical URL

Published Questionnaire HTML has stable canonical application route.

---

# 235. HTML Print

Browser print stylesheet MAY exist, but official downloadable PDF still uses controlled PDF generation pipeline.

---

# 236. Public Builder HTML Preview

Uses temporary session projection before snapshot.

Generated downloadable document uses immutable Snapshot.

---

# 237. HTML Link Behaviour

External links should use safe browser attributes according to security policy.

---

# 238. PDF Link Behaviour

All URLs embedded exactly according to source.

---

# 239. Contact Links

Phone:

```text
tel:
```

WhatsApp:

approved URL

Email:

```text
mailto:
```

may be clickable where configured.

---

# 240. Contact Visibility

A Contact can only enter Document Model if the applicable context permission allows it.

---

# 241. Prepared Questionnaire Contact Rule

Requires:

```text
allow_in_admin_questionnaires = true
```

plus Questionnaire selection.

---

# 242. Builder Contact Rule

Requires:

```text
allow_in_public_questionnaire_builder = true
```

plus Builder selection/template.

---

# 243. Contact Snapshot

Once Revision/Snapshot created:

exact included Contact value is frozen for that historical output, subject to privacy redaction policy.

---

# 244. QR Contact

QR to a phone/WhatsApp/contact action MAY be supported only if QR module policy explicitly allows that safe target.

Not required baseline.

---

# 245. Official Profile QR

Safe recommended document-level QR candidate.

---

# 246. Professional Link QR

Safe if canonical URL passes QR eligibility.

---

# 247. Document Pagination Does Not Change Semantics

Section moved to next page is still same document content.

---

# 248. Content Ordering Is Snapshot Data

Renderer cannot sort Projects/Links independently from approved document configuration.

---

# 249. Context-Specific Order

Questionnaire order wins over public site Project order.

---

# 250. Empty Optional Section

Omit cleanly.

Do not render:

```text
Achievements: null
```

---

# 251. Empty Required Section

Block readiness/generation.

---

# 252. “Не указано”

Used where business semantics explicitly require showing an absent casting requirement.

It should not be indiscriminately used for ordinary optional Profile fields.

---

# 253. Document Version Labels

Admin/history SHOULD identify:

```text
Questionnaire Revision
locale
generated date
```

Public PDF need not expose internal revision ID prominently.

---

# 254. Private Token Must Not Be Printed Unnecessarily

If token is embedded in URL/QR, do not separately expose raw token as debug metadata.

---

# 255. Document IDs

Internal IDs may be embedded in metadata for support only if they pose no privacy/security problem.

---

# 256. Personal/Professional Data Minimization

PDF includes only data selected/required for professional purpose.

---

# 257. Admin Internal Data Exclusion

Never include:

```text
internal notes
VOP recommendations
AI drafts
Casting internal analysis
Audit
processing state
```

unless a distinct internal document is explicitly defined.

---

# 258. Document Theme Safety

No decorative design may:

```text
obscure text
crop required photos improperly
hide URLs
reduce QR quiet zone
make links indistinguishable
reduce contrast
change content order semantically
```

---

# 259. Watermarks

Not baseline requirement.

If introduced later, must not obscure face/content or impair readability/QR.

---

# 260. Page Backgrounds

Must preserve print/readability.

Avoid ink-heavy design unless explicit approved template.

---

# 261. Public Professional Quality

PDF must look intentionally designed as an actor questionnaire, not like a raw database export.

---

# 262. But Professional Design Does Not Mean Decoration-First

Information hierarchy remains primary.

---

# 263. PDF Data Flow — Prepared

```text
QuestionnaireRevision
        ↓
DocumentModelBuilder
        ↓
DocumentModelValidator
        ↓
Media Resolver
        ↓
QR Resolver
        ↓
PDF Renderer
        ↓
Artifact Validator
        ↓
QuestionnaireArtifact READY
```

---

# 264. PDF Data Flow — Builder

```text
BuilderSession
      ↓
Server Revalidation
      ↓
GenerationSnapshot
      ↓
DocumentModelBuilder
      ↓
PDF Renderer
      ↓
Artifact READY
```

---

# 265. HTML Data Flow — Published

```text
QuestionnaireDefinition
      ↓
current_published_revision
      ↓
Revision Projection
      ↓
DocumentModel
      ↓
HTML Renderer
```

---

# 266. Historical Regeneration

```text
Revision 4
 ↓
Document Model using Revision 4 only
 ↓
current Renderer / compatible renderer
 ↓
new Artifact linked to Revision 4
```

---

# 267. Historical Semantics

Even if current renderer changes layout:

professional data and links remain Revision 4 values.

---

# 268. Reproducibility Metadata

Artifact SHOULD allow system to answer:

```text
Which Revision/Snapshot?
Which locale?
Which Document Model version?
Which template?
Which renderer?
Which media?
Which URLs?
Which QR?
When generated?
```

---

# 269. Document Model Versioning

Structured model schema MUST be versionable.

---

# 270. Old Model Compatibility

Historical Revision should remain renderable via:

```text
compatibility adapter
or
normalized historical model transformation
```

---

# 271. Destructive Historical Migration

Avoid rewriting old semantic snapshot just because current model schema changed.

---

# 272. Document Model Upgrade

New generation from current Draft uses current schema.

Historical rendering uses compatibility path.

---

# 273. Artifact Storage Key

Conceptually:

```text
documents/
  questionnaires/
    {questionnaire-id}/
      {revision-id}/
        {locale}/
          {artifact-id}.pdf
```

Exact physical layout is not normative.

---

# 274. Builder Artifact Storage

Separate namespace recommended.

---

# 275. Private/Public Storage Separation

Artifact storage class follows security classification.

---

# 276. Download Endpoint

Responsibilities:

```text
resolve artifact
check access
set content type
set safe filename
serve binary
```

---

# 277. Download Endpoint Must Not Generate Arbitrarily

Serving an existing PDF should not silently rebuild from current data.

---

# 278. Generate Endpoint

Explicitly creates generation task/snapshot.

---

# 279. Content-Disposition

Download endpoint uses sanitized professional filename.

---

# 280. Range Support

Not generally necessary for small PDFs, but standard HTTP serving semantics may support it.

---

# 281. Browser Inline Preview

May use:

```text
Content-Disposition: inline
```

for preview endpoint where safe.

Download action can use attachment disposition.

---

# 282. PDF MIME

Must be:

```text
application/pdf
```

---

# 283. Cache-Control

Public immutable revision artifact may have long immutable caching.

---

# 284. Private PDF Cache

Must be private/no-store or appropriately scoped depending security policy.

Never public shared cache.

---

# 285. Current Alias Cache

Needs invalidation when current Revision changes.

---

# 286. PDF Security

Renderer environment must be treated as sensitive execution environment.

---

# 287. HTML Injection

All text inserted into HTML/PDF template must be escaped/sanitized according to representation.

---

# 288. Renderer Network Access

Should be restricted/minimized.

Do not allow arbitrary URL fetch from document fields.

---

# 289. File Access

Renderer should only access managed approved media files.

---

# 290. Local File URL Injection

Prohibit source content causing:

```text
file:///etc/passwd
```

or similar render-time inclusion.

---

# 291. Template Injection

Templates are system-controlled, not arbitrary user-executable code.

---

# 292. AI and Document Rendering

AI is not invoked implicitly during PDF render.

---

# 293. Why

A Revision/Snapshot must already contain approved content.

Rendering should be deterministic.

---

# 294. BB Assistant Integration

AI-generated text can appear only after:

```text
Generate Draft
→ Human Apply
→ target Draft
→ publish/snapshot
```

---

# 295. Casting AI Integration

Confirmed requirement may influence questionnaire selection before Snapshot.

AI recommendation is not directly rendered as actor fact unless explicitly selected/approved and semantically appropriate.

---

# 296. VOP Integration

VOP may recommend rebuild or report missing/invalid artifact.

It cannot alter historical document content.

---

# 297. Analytics Integration

Document events may include:

```text
questionnaire_viewed
pdf_downloaded
builder_pdf_generated
professional_link_clicked
```

subject to analytics architecture.

---

# 298. PDF Download Is Secondary Conversion

It does not itself equal professional inquiry.

---

# 299. Attribution

A public download may retain safe source attribution context.

---

# 300. Document Lifecycle

Prepared:

```text
Draft
→ Ready
→ Revision Published
→ PDF Queued
→ Ready
→ Historical/Superseded through newer Revision
```

Builder:

```text
Session
→ GenerationSnapshot
→ PDF Queued
→ Ready
→ Expiry/Retention
```

---

# 301. Artifact Failure State

Does not mutate Revision state unless publication policy explicitly couples PDF as mandatory precondition.

---

# 302. Artifact Archive

Published historical artifact generally retained.

---

# 303. Artifact Hard Delete

Subject to retention/privacy policy.

---

# 304. PDF-AP-001

**Generate official PDF from live mutable Profile**

---

# 305. PDF-AP-002

**Separate HTML and PDF business logic**

---

# 306. PDF-AP-003

**Image-only/screenshot PDF**

---

# 307. PDF-AP-004

**Decorative cover before all useful casting information**

---

# 308. PDF-AP-005

**Project image substituted for mandatory Full Body**

---

# 309. PDF-AP-006

**Emotional individual photos dumped instead of approved Grid**

---

# 310. PDF-AP-007

**QR without corresponding hyperlink**

---

# 311. PDF-AP-008

**QR encodes Admin URL**

---

# 312. PDF-AP-009

**QR encodes expiring signed URL**

---

# 313. PDF-AP-010

**QR generated without decode verification**

---

# 314. PDF-AP-011

**Tiny QR scaled to fit**

---

# 315. PDF-AP-012

**Theme removes QR quiet zone**

---

# 316. PDF-AP-013

**PDF worker receives only questionnaire ID and resolves latest data later**

---

# 317. PDF-AP-014

**Historical PDF URL changes when current ProfessionalLink changes**

---

# 318. PDF-AP-015

**Private casting PDF stored under generic public cache URL**

---

# 319. PDF-AP-016

**Renderer fetches arbitrary external URLs supplied by visitor**

---

# 320. PDF-AP-017

**Font substitution breaks Cyrillic**

---

# 321. PDF-AP-018

**Long URL overflows page**

---

# 322. PDF-AP-019

**Required Full Body cropped to portrait**

---

# 323. PDF-AP-020

**Renderer silently removes failed required link**

---

# 324. PDF-AP-021

**AI runs during render and rewrites text**

---

# 325. PDF-AP-022

**Admin Draft Preview is presented as published Revision**

---

# 326. PDF-AP-023

**Current Source changes mutate previously downloaded Builder PDF**

---

# 327. PDF-AP-024

**Save entire Profile as giant JSON inside PDF metadata**

---

# 328. PDF-AP-025

**Layout tables used solely for visual structure with broken accessibility semantics**

---

# 329. Document Quality Gate

До production implementation MUST быть определены:

- [ ] canonical Document Model;
- [ ] model schema version;
- [ ] prepared source = Revision;
- [ ] Builder source = GenerationSnapshot;
- [ ] mandatory questionnaire content;
- [ ] section ordering;
- [ ] first-page hierarchy;
- [ ] Primary Close-Up rendering;
- [ ] Primary Full Body rendering;
- [ ] Emotional Grid representation;
- [ ] link model;
- [ ] canonical URL resolver;
- [ ] QR validation;
- [ ] QR size/quiet zone;
- [ ] locale strategy;
- [ ] PDF metadata;
- [ ] filename rules;
- [ ] document template version;
- [ ] renderer selection;
- [ ] Cyrillic support;
- [ ] accessibility capabilities;
- [ ] media resolver;
- [ ] rendering isolation/security;
- [ ] background job;
- [ ] idempotency;
- [ ] binary validation;
- [ ] artifact storage;
- [ ] public/private access;
- [ ] caching;
- [ ] historical regeneration;
- [ ] backup/retention;
- [ ] observability;
- [ ] E2E scan/link tests.

---

# 330. Document Model Specification Template

```text
Model Version:
...

Source:
QuestionnaireRevision / BuilderGenerationSnapshot

Locale:
...

Sections:
...

Required Content:
...

Media:
...

Links:
...

QR:
...

Contacts:
...

Footer:
...

Template Version:
...
```

---

# 331. PDF Artifact Specification Template

```text
Artifact ID:
...

Source Type:
QuestionnaireRevision

Source ID:
...

Locale:
ru

Document Model Version:
...

Template Version:
...

Renderer Version:
...

Storage Class:
PUBLIC / TOKEN_SCOPED / ADMIN_ONLY

Checksum:
...

Status:
READY

Generated At:
...
```

---

# 332. QR Document Specification Template

```text
Link Description:
...

Canonical URL:
...

Show Hyperlink:
true

Show QR:
true

QR Eligibility:
PASS

Decode Validation:
PASS

Physical Size:
>= configured safe minimum

Quiet Zone:
required

Accessible Text:
...
```

---

# 333. E2E-PDF-001 — Prepared Revision

Publish Revision 1.

Change Profile.

Generate Revision 1 PDF again.

Verify old Revision content unchanged.

---

# 334. E2E-PDF-002 — Mandatory Close-Up

Generation without eligible Main Portfolio Close-Up is blocked.

---

# 335. E2E-PDF-003 — Mandatory Full Body

Generation without eligible Main Portfolio Full Body is blocked.

---

# 336. E2E-PDF-004 — No Substitution

Project Full Body-like image exists but Main Portfolio Full Body missing.

Generation still blocked.

---

# 337. E2E-PDF-005 — Full Body Framing

Generated PDF preserves entire approved Full Body framing.

---

# 338. E2E-PDF-006 — Emotional Section

Generated document contains:

```text
approved Grid
shooting date
full Emotional URL
```

and no individual emotional source-photo dump.

---

# 339. E2E-PDF-007 — Hyperlinks

All configured links are actual clickable PDF annotations with exact target.

---

# 340. E2E-PDF-008 — QR Exactness

Decode every generated QR.

Expected:

```text
decoded URL === canonical URL
```

---

# 341. E2E-PDF-009 — Private QR

Admin/private URL cannot be rendered as professional QR.

---

# 342. E2E-PDF-010 — QR + Link

QR-visible item always has textual/clickable equivalent.

---

# 343. E2E-PDF-011 — Cyrillic

Russian questionnaire renders all characters correctly.

---

# 344. E2E-PDF-012 — English

English localized document uses correct localized snapshot.

---

# 345. E2E-PDF-013 — Localization Independence

Updating EN copy does not alter old EN Revision artifact.

---

# 346. E2E-PDF-014 — Selectable Text

Professional facts can be selected/extracted from generated PDF.

---

# 347. E2E-PDF-015 — Not Raster Screenshot

PDF contains semantic text objects and links rather than page-only images.

---

# 348. E2E-PDF-016 — Builder Snapshot

Generate Builder PDF A.

Modify Session.

Generate PDF B.

A remains unchanged.

---

# 349. E2E-PDF-017 — Builder Revocation

Selected Contact permission revoked before Generate.

Server revalidation prevents Contact entering Snapshot/PDF.

---

# 350. E2E-PDF-018 — Worker Retry

Renderer crashes after job accepted.

Retry generates one valid artifact without duplicate inconsistent records.

---

# 351. E2E-PDF-019 — Parallel Revision

Worker rendering R5 while R6 publishes still produces R5 artifact.

---

# 352. E2E-PDF-020 — Corrupt Output

Renderer writes invalid binary.

Validation rejects it; artifact not READY.

---

# 353. E2E-PDF-021 — Media Missing

Required revision-bound photo missing from storage.

Generation fails explicitly.

It does not use current replacement image.

---

# 354. E2E-PDF-022 — Private Casting Artifact

Unauthorized request cannot download token-scoped PDF.

---

# 355. E2E-PDF-023 — Revoked Token

Previously valid casting token revoked.

Artifact remains stored but future delivery denied.

---

# 356. E2E-PDF-024 — Long URL

Long professional URL wraps without clipping or QR overlap.

---

# 357. E2E-PDF-025 — Accessibility

Validate representative document for:

```text
language
logical reading order
headings where supported
text selection
link labels
image descriptions
```

---

# 358. E2E-PDF-026 — Print QR

QR remains scannable at configured minimum printed size.

---

# 359. E2E-PDF-027 — Theme Safety

Attempt to publish/use document theme with insufficient contrast or broken QR area is blocked.

---

# 360. E2E-PDF-028 — HTML/PDF Consistency

Same Revision HTML and PDF contain same:

```text
professional values
media selections
links
Contacts
section inclusion
```

---

# 361. E2E-PDF-029 — Safe Filename

Casting/role names containing slashes or unsupported characters produce sanitized filename.

---

# 362. E2E-PDF-030 — Backup Restore

Restore Revision and stored PDF.

Verify checksum/access.

If binary missing but regeneration allowed, recreate from exact Revision.

---

# 363. Document Traceability

Canonical:

```text
MASTER DATA
     ↓
Questionnaire Draft
     ↓
Published Revision
     ↓
Document Model
     ↓
HTML / PDF
```

---

# 364. Builder Traceability

```text
CURRENT ELIGIBLE MASTER
       ↓
Builder Session
       ↓
Server Revalidation
       ↓
GenerationSnapshot
       ↓
Document Model
       ↓
PDF
```

---

# 365. Link Traceability

```text
ProfessionalLink / Canonical Route
        ↓
Revision/Snapshot
        ↓
Document Link
        ├─ Hyperlink
        └─ QR
```

---

# 366. QR Traceability

```text
Canonical URL
     ↓
QR Generator
     ↓
Decode Validator
     ↓
QR Artifact
     ↓
Document Artifact
```

---

# 367. Media Traceability

```text
MediaAsset Original
      ↓
Professional Relation
      ↓
Revision/Snapshot
      ↓
PDF Media Derivative
      ↓
Document
```

---

# 368. Final Document Pipeline

```text
              AUTHORITATIVE SOURCE
                     │
          ┌──────────┴───────────┐
          ▼                      ▼
 QuestionnaireRevision    BuilderGenerationSnapshot
          │                      │
          └──────────┬───────────┘
                     ▼
             DOCUMENT MODEL
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
      MEDIA        LINKS         QR
        │            │            │
        └────────────┼────────────┘
                     ▼
              MODEL VALIDATION
                     │
             ┌───────┴────────┐
             ▼                ▼
          HTML              PDF
                               │
                               ▼
                     OUTPUT VALIDATION
                               │
                               ▼
                       READY ARTIFACT
```

---

# 369. Document Compliance Criteria

Реализация соответствует DOC-075, если:

1. HTML и PDF используют единый semantic source/model;
2. prepared PDF строится только из QuestionnaireRevision;
3. Builder PDF строится только из GenerationSnapshot;
4. renderer не читает текущий Profile для historical content;
5. identification обязательна;
6. Main Portfolio Close-Up обязателен;
7. Main Portfolio Full Body обязателен;
8. official Profile link обязателен;
9. Project/Emotional images не могут заменить обязательные Portfolio photos;
10. Full Body не обрезается renderer'ом;
11. Emotional section использует composite + date + full link;
12. professional links clickable;
13. QR optional per link;
14. QR target equals canonical URL exactly;
15. private/admin/temporary unsafe URL is rejected;
16. QR has quiet zone and safe physical size;
17. QR never becomes sole access mechanism;
18. PDF contains real/selectable text;
19. image-only PDF prohibited;
20. Cyrillic and English supported;
21. locale belongs snapshot/document;
22. document metadata is safe;
23. filenames sanitized;
24. artifact binds exact Source/locale/renderer/template;
25. PDF jobs are durable/idempotent;
26. post-render validation precedes READY;
27. private/public artifact access is separated;
28. historical artifact semantics remain reproducible;
29. optional integration failure does not rewrite Revision;
30. accessibility requirements are supported/tested;
31. renderer cannot fetch arbitrary uncontrolled external resources;
32. AI is not invoked during deterministic rendering;
33. themes cannot remove mandatory information or weaken QR/accessibility;
34. previous published artifacts survive failed new generation;
35. exact Revision/Snapshot provenance is reconstructible.

---

# 370. Финальная доктрина

> **Профессиональная актёрская анкета должна существовать сначала как зафиксированный набор утверждённых данных, затем как semantic Document Model и только после этого как HTML или PDF. PDF renderer не имеет права самостоятельно искать “актуальные” факты, выбирать другие фотографии, исправлять ссылки, генерировать тексты или принимать профессиональные решения. Его задача — детерминированно превратить конкретную Revision или Generation Snapshot в читаемый, кликабельный, доступный и печатно пригодный документ, сохранив точные фотографии, ссылки, QR и историческое состояние данных.**