# PROFESSIONAL MEDIA & LINKS MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация Video, Audio, External Links, canonical URLs, HTML/PDF hyperlinks, QR и link-health monitoring

**Целевой файл:** `docs/modules/professional-media-links.md`  
**Документ:** DOC-118  
**Статус:** ✅ Completed  
**Тип:** Module / Professional Media / Links / Video / Audio / QR / Link Health

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
- `docs/modules/projects.md`
- `docs/modules/qr-links.md` *(будущий DOC-130; subsystem rules подробно фиксируются там)*

---

# 1. Назначение модуля

Professional Media & Links Module хранит и публикует профессиональные media destinations актрисы.

Он отвечает за:

- видеовизитку;
- showreel;
- отдельные video samples;
- audio samples;
- voice samples;
- внешние профессиональные ссылки;
- локально размещённые опубликованные media URLs;
- ordering;
- description;
- visibility;
- Questionnaire inclusion;
- Public Builder inclusion;
- HTML/PDF hyperlinks;
- QR eligibility;
- canonical URL resolution;
- link health;
- current availability.

---

# 2. Главная доктрина

> **ProfessionalLink является профессиональной ссылкой на ресурс. MediaAsset является управляемым техническим файлом. Они могут представлять один пользовательский сценарий, но являются разными Source types и не должны смешиваться.**

Canonical:

```text id="w35n2p"
External Resource
      ↓
ProfessionalLink
      ↓
Canonical URL
      ↓
Public / Questionnaire / Builder / QR

Managed Local Media
      ↓
MediaAsset
      ↓
Published App-Controlled URL
      ↓
Professional Media Projection
```

---

# 3. Fundamental separation

```text id="1uvrm2"
ProfessionalLink
≠ MediaAsset
≠ MediaDerivative
≠ QR Artifact
≠ Questionnaire Snapshot
≠ Social Post
```

---

# 4. Module identifiers

Используются:

```text id="0zs2w6"
PML-*
PML-TYPE-*
PML-URL-*
PML-VID-*
PML-AUD-*
PML-EXT-*
PML-VIS-*
PML-QNR-*
PML-BLD-*
PML-QR-*
PML-HLT-*
PML-AI-*
PML-INV-*
PML-AP-*
E2E-PML-*
```

---

# 5. Business purpose

Casting professional должен иметь возможность за **одно действие**:

- открыть видеовизитку;
- открыть showreel;
- прослушать voice/audio sample;
- перейти по профессиональной ссылке.

---

# 6. Action budget

Canonical:

```text id="w0jr1z"
Video = 1 action
Professional external link = 1 action
Audio = 1 action
```

---

# 7. Core professional media types

Recommended controlled types:

```text id="u42t0b"
VIDEO_INTRO
SHOWREEL
VIDEO_SAMPLE
AUDIO_SAMPLE
VOICE_SAMPLE
EXTERNAL_PROFESSIONAL
OTHER
```

---

# 8. VIDEO_INTRO

Видеовизитка актрисы.

---

# 9. SHOWREEL

Компактная подборка профессиональной видеоработы.

---

# 10. VIDEO_SAMPLE

Отдельный video sample / scene / casting clip.

---

# 11. AUDIO_SAMPLE

Профессиональный аудиоматериал.

---

# 12. VOICE_SAMPLE

Voice / speech / voice acting sample.

---

# 13. EXTERNAL_PROFESSIONAL

Внешняя профессиональная ссылка, не являющаяся локальным файлом.

---

# 14. OTHER

Только для действительного профессионального media/link use-case вне текущей taxonomy.

---

# 15. Source strategies

Professional Media может быть представлен двумя способами.

### A. Managed media

```text id="1a24na"
MediaAsset
```

### B. External link

```text id="pt1srw"
ProfessionalLink
```

---

# 16. PML-INV-001 — External URL Is Not MediaAsset

Ссылка на:

```text id="v1i6m8"
YouTube
Vimeo
SoundCloud
Google Drive public resource
casting platform
agency/profile page
```

не должна автоматически становиться MediaAsset.

---

# 17. Managed local file

Если файл загружен в Media Library:

```text id="xzozm8"
MediaAsset
↓
published safe resource
↓
app-controlled URL
```

---

# 18. PML-INV-002 — Managed Local Media Must Use Stable App-Controlled URL

Нельзя использовать в Questionnaire:

```text id="w8hntj"
/data/kate-actor/video/file.mp4
```

---

# 19. ProfessionalLink entity

Representative fields:

```text id="4xlvqw"
id
profile_id
link_type
description
url
canonical_url
display_order
publication_state
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
show_link_by_default
show_qr_by_default
version
created_at
updated_at
archived_at
```

---

# 20. Media-backed professional entry

Implementation may use explicit entity such as:

```text id="1slj4z"
ProfessionalMediaItem
```

or projection/configuration over MediaAsset.

Required semantics remain:

```text id="0p2m91"
media_asset_id
professional_type
description
visibility
order
stable published URL
```

---

# 21. PML-INV-003 — Professional Classification Does Not Belong to MediaAsset

`SHOWREEL` is contextual professional meaning, not global binary property.

---

# 22. Exact external link table requirement

Questionnaire link editor MUST support exactly the semantic columns:

```text id="2i5gbt"
№
Описание
URL
```

---

# 23. Column 1 — №

Sequential presentation number.

---

# 24. Column 2 — Описание

Human-readable professional description.

Example:

```text id="jpf13q"
Видеовизитка
Showreel 2026
Сцена из короткометражного фильма
Voice sample
```

---

# 25. Column 3 — URL

Exact clickable canonical URL.

---

# 26. PML-EXT-001 — Three-Column Semantics Are Canonical

HTML/PDF may visually adapt layout responsively, but semantics remain:

```text id="w6kxl2"
number
description
URL
```

---

# 27. Sequence number ownership

`№` is derived from selected item order.

Do not store it as permanent business identity.

---

# 28. PML-INV-004 — Row Number ≠ Entity ID

---

# 29. URL source

User enters or system derives URL.

Server performs normalization/validation.

---

# 30. Canonical URL

Every current link intended for publication must resolve to one canonical normalized URL.

---

# 31. Canonical URL doctrine

Canonicalization may include safe transformations such as:

- scheme normalization;
- host normalization where appropriate;
- removing accidental whitespace;
- standard URL parsing;
- optional recognized tracking-parameter cleanup according to policy.

---

# 32. PML-URL-001 — Canonicalization Cannot Change Destination Semantics

---

# 33. Example

Input:

```text id="usfc6s"
 https://example.com/video 
```

Canonical:

```text id="4zv29z"
https://example.com/video
```

---

# 34. URL scheme allowlist

For ordinary professional web links:

```text id="zhm2wu"
https
```

preferred.

---

# 35. HTTP

May be accepted only where explicitly required, with readiness/security warning.

Preferred public output:

```text id="lvwc6q"
HTTPS only
```

---

# 36. Forbidden schemes

Reject:

```text id="iohxta"
javascript:
data:
file:
ftp:
vbscript:
```

unless future explicit specialized feature defines otherwise.

---

# 37. PML-URL-002 — Unsafe Scheme Is Never Published

---

# 38. Credentials in URL

Reject or sanitize:

```text id="ofoz6f"
https://user:password@example.com/...
```

for public professional links.

---

# 39. Admin URLs

Never valid professional public targets:

```text id="v0wf89"
/admin/...
```

---

# 40. Storage URLs

Raw filesystem/internal object-store locator prohibited.

---

# 41. Internal network URLs

Prohibited from public professional publication.

Examples:

```text id="ktrtg0"
http://localhost
http://192.168.x.x
http://10.x.x.x
```

---

# 42. PML-URL-003 — Public Link Must Be Externally Resolvable in Intended Context

---

# 43. Local published media URL

Managed local video/audio needs public/casting-safe route.

Example conceptually:

```text id="taakac"
https://kate.supremevision.org/media/<public-id>/video
```

---

# 44. Stable media route

Must remain stable across:

- filesystem relocation;
- derivative regeneration;
- deployment changes.

---

# 45. PML-URL-004 — Public URL Never Encodes Storage Locator

---

# 46. Video ownership

A video item may reference:

1. managed MediaAsset;
2. external ProfessionalLink.

---

# 47. PML-VID-001 — Same UX, Explicit Backend Kind

UI can present both as “Video”, while data model distinguishes source kind.

---

# 48. Managed video validation

Requires:

```text id="uxkjlx"
MediaAsset.type = VIDEO
same profile
technically ready
eligible delivery route
```

---

# 49. External video validation

Requires:

```text id="w0h1g5"
safe canonical URL
supported/allowed destination semantics
```

---

# 50. Video player

Managed media can use native/application player.

---

# 51. External video

May:

- open external service;
- embed only if provider policy/security explicitly supports it.

---

# 52. PML-VID-002 — External Embed Is Not Assumed

Do not assume every URL is embeddable.

---

# 53. Fallback

Always provide clickable link when embedding unavailable.

---

# 54. Autoplay

Not baseline.

Professional media should not unexpectedly autoplay with sound.

---

# 55. Accessibility

Video player must expose:

- play/pause;
- keyboard controls;
- accessible labeling.

Captions/subtitles support SHOULD be possible where assets exist.

---

# 56. Video intro primary

Product may designate one:

```text id="v9rta6"
Primary Video Intro
```

for Hero/Sticky CTA.

---

# 57. PML-VID-003 — At Most One Current Primary Video Intro

If primary semantics used.

---

# 58. Showreel primary

Likewise MAY designate one current primary showreel.

---

# 59. Primary selection

Human-controlled.

---

# 60. AI cannot select automatically

---

# 61. PML-AI-001 — AI Cannot Change Primary Media

---

# 62. Hero CTA

`Видеовизитка` resolves current eligible primary VIDEO_INTRO.

---

# 63. PML-INV-005 — Hero CTA Is Projection

No duplicated URL field in Profile.

---

# 64. Dead CTA

If primary video missing/unavailable:

- CTA hidden;
- disabled with clear reason in Admin preview;
- never points to broken/private route.

---

# 65. Audio ownership

Managed audio:

```text id="6y9fjw"
MediaAsset.type = AUDIO
```

or external ProfessionalLink.

---

# 66. Audio playback

Must support common browser-safe playback derivative for managed audio.

---

# 67. Audio metadata

Can include:

```text id="c9vg4e"
duration
format
description
```

---

# 68. Voice sample

May be separately typed from generic Audio Sample for professional filtering.

---

# 69. PML-AUD-001 — Audio Description Is Contextual

Not global MediaAsset caption.

---

# 70. External link description

Required for Questionnaire/PDF presentation unless system has a reliable controlled default based on professional type.

---

# 71. Preferred

Human-readable description required before publication.

---

# 72. PML-EXT-002 — Raw URL Alone Is Poor Professional UX

A published Questionnaire row should normally have a meaningful description.

---

# 73. Link ordering

Explicit:

```text id="4pfniq"
display_order
```

---

# 74. PML-INV-006 — CreatedAt Is Not Link Order

---

# 75. Visibility triplet

Canonical:

```text id="ik06rg"
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 76. PML-VIS-001 — Independent Dimensions

---

# 77. Example A

Public video, QNR, Builder:

```text id="6g11hj"
true / true / true
```

---

# 78. Example B

Casting-specific link:

```text id="wvjd72"
public=false
adminQ=true
builder=false
```

only if URL itself is safe for the generated questionnaire context.

---

# 79. Example C

Builder-only resource:

```text id="ogf87f"
public=false
adminQ=false
builder=true
```

requires a Builder-safe/token-safe destination.

---

# 80. PML-VIS-002 — Visibility Permission Does Not Make Unsafe URL Safe

---

# 81. Publication lifecycle

Canonical:

```text id="651lav"
DRAFT
PUBLISHED
ARCHIVED
```

---

# 82. DRAFT

Editable, not current-public.

---

# 83. PUBLISHED

May appear according to visibility.

---

# 84. ARCHIVED

Removed from current selections.

Historical Questionnaire snapshots remain.

---

# 85. PML-INV-007 — Archive Does Not Delete MediaAsset

---

# 86. URL changes

Changing URL is a significant mutation.

---

# 87. PML-URL-005 — URL Change Invalidates Current QR

Any current QR for previous canonical URL becomes stale.

---

# 88. Historical QR

Historical QuestionnaireRevision remains tied to old exact URL/QR semantics.

---

# 89. PML-URL-006 — URL Change Never Rewrites Historical QNR

---

# 90. Questionnaire integration

Questionnaire supports:

- videos;
- audio;
- professional external links.

---

# 91. Link rendering

Every included external/resource link is actually clickable.

---

# 92. PML-QNR-001 — Visible URL Must Have Real Hyperlink Annotation

Not just colored text.

---

# 93. HTML

Use semantic anchor:

```text id="6zwqhm"
<a href="...">
```

with security-safe attributes as appropriate.

---

# 94. PDF

Renderer must create actual clickable annotation.

---

# 95. PML-QNR-002 — PDF Link Must Be Interactive

---

# 96. Printed fallback

Visible exact URL remains readable where reasonable.

---

# 97. Exact 3-column representation

For link table:

```text id="cm5yik"
№ | Описание | URL
```

---

# 98. Responsive HTML

Mobile MAY stack each row visually:

```text id="3jzdqb"
1.
Видеовизитка
https://...
```

but semantic fields remain unchanged.

---

# 99. PDF layout

Must remain readable without truncating destination to useless text.

---

# 100. Long URL handling

May wrap safely.

Do not alter hyperlink destination.

---

# 101. PML-QNR-003 — Display Formatting Never Changes Actual Href

---

# 102. Additional media blocks

Video/audio MAY have dedicated richer blocks in HTML.

PDF still needs:

```text id="5oksvh"
description
clickable URL
optional QR
```

because embedded playback is not portable.

---

# 103. PML-QNR-004 — PDF Does Not Depend on Embedded Video Playback

---

# 104. Per-link QR controls

Every supported link must allow:

```text id="1je0x0"
show_link
show_qr
```

per Questionnaire/configuration context.

---

# 105. Default

Recommended:

```text id="qazudx"
show_link = true
show_qr = configurable
```

---

# 106. PML-QR-001 — QR Never Replaces Hyperlink by Default

---

# 107. Four valid presentation combinations

Where policy allows:

```text id="989y3k"
link=true, qr=false
link=true, qr=true
link=false, qr=true
link=false, qr=false
```

---

# 108. Professional baseline

For ordinary Questionnaire links, prefer:

```text id="9mo8yb"
link=true
```

even when QR shown.

---

# 109. QR input

Exactly:

```text id="etllss"
canonical_url
```

---

# 110. PML-QR-002 — QR Target Equals Hyperlink Target Exactly

After normalization.

---

# 111. Validation

QR generation flow:

```text id="oi65f6"
canonical URL
↓
eligibility
↓
QR generation
↓
decode validation
↓
decoded URL == canonical URL
↓
READY
```

---

# 112. QR forbidden targets

Never:

```text id="wrow8x"
/admin
localhost
private storage
raw filesystem
expired temp URL
unsafe scheme
```

---

# 113. Token-scoped QR

Allowed only if token route is intentionally distributable for that Questionnaire/casting context.

---

# 114. PML-QR-003 — Bearer Scope Must Match Intended Audience

---

# 115. Printed QR longevity

Short-lived signed URLs are poor QR targets.

---

# 116. Preferred

Stable application-controlled resource route that performs access policy at open time.

---

# 117. PML-QR-004 — QR Should Not Encode Ephemeral Storage Signature

---

# 118. QR artifact cache

Derived from:

```text id="mabh4f"
canonical URL
+
QR settings
+
generator version
```

---

# 119. URL mutation

Creates new QR identity.

---

# 120. Historical QR unaffected semantically.

---

# 121. QR minimum print quality

Detailed dimensions in DOC-130.

Module-level requirement:

- sufficient physical size;
- quiet zone;
- high contrast;
- vector/high-resolution PDF output;
- decode-tested.

---

# 122. Public Builder

Builder can include only Builder-eligible links/media.

---

# 123. Builder selection

Visitor may:

- include/exclude item;
- where allowed, toggle QR/link presentation.

---

# 124. Builder cannot

```text id="xed0ax"
edit canonical URL
change description Source
replace media
create new external link
```

---

# 125. PML-BLD-001 — Builder Configures Representation, Not Source

---

# 126. Builder Generate

Server revalidates:

```text id="1q0n20"
item current
published/eligible
same profile
URL still safe
link health policy acceptable
managed media publicly/casting-safe resolvable
```

---

# 127. Broken link

Need distinction:

```text id="qia6cc"
temporarily unreachable
permanently invalid
unknown
```

---

# 128. Link health subsystem

ProfessionalLink health is derived operational state.

---

# 129. Suggested states

```text id="sl709f"
UNKNOWN
HEALTHY
DEGRADED
BROKEN
AUTH_REQUIRED
RATE_LIMITED
CHECK_FAILED
```

---

# 130. PML-HLT-001 — Link Health Is Not Source Truth

---

# 131. Link health record

Separate:

```text id="4hcuc0"
professional_link_health
```

or equivalent.

---

# 132. Suggested fields

```text id="rm5k7x"
link_id
checked_at
status
http_status?
resolved_url?
latency_ms?
failure_class?
attempt_count?
```

---

# 133. Redirect

Health checker may follow bounded redirects.

---

# 134. Canonical destination vs runtime resolved redirect

Do not silently replace Source URL simply because external provider redirected.

---

# 135. PML-HLT-002 — Health Check Cannot Mutate Canonical URL Automatically

---

# 136. Redirect recommendation

VOP/Admin may suggest updating URL.

Human confirms.

---

# 137. Health check SSRF

Link checker itself is SSRF-sensitive.

---

# 138. Must block

```text id="7hd6k0"
private IP
localhost
metadata endpoint
file scheme
internal admin service
```

---

# 139. Redirect targets

Revalidated at every hop.

---

# 140. Request method

HEAD preferred where reliable; bounded GET fallback may be needed.

Provider-specific behavior may differ.

---

# 141. PML-HLT-003 — Link Checker Must Not Download Arbitrary Large Media

---

# 142. Response body

Bounded/not needed for ordinary status check.

---

# 143. Timeouts

Finite.

---

# 144. Rate limiting

External domains must not be aggressively polled.

---

# 145. Scheduler

Background job checks link health periodically.

---

# 146. Frequency

Operationally configurable.

---

# 147. No real-time check on every public request

---

# 148. PML-HLT-004 — Public Rendering Must Not Depend on Synchronous External Health Check

---

# 149. Why

External provider outage must not block page response.

---

# 150. Current health use

Admin can see warning:

```text id="5ja9s6"
Ссылка недоступна при последней проверке.
```

---

# 151. Public behavior

A transient `DEGRADED` state should not necessarily hide a professionally valid link automatically.

---

# 152. Broken policy

Confirmed persistent unsafe/dead link MAY block readiness/publication according to configured policy.

---

# 153. PML-HLT-005 — Operational Failure Does Not Silently Delete ProfessionalLink

---

# 154. Readiness

Current link readiness considers:

```text id="4hse62"
valid canonical URL
safe scheme
surface access suitability
published state
optional health policy
```

---

# 155. Managed media readiness

Additionally:

```text id="n2jsic"
MediaAsset READY
required derivative READY
stable resource URL available
```

---

# 156. Readiness codes

Suggested:

```text id="6t7rdm"
PRO_LINK_URL_REQUIRED
PRO_LINK_URL_INVALID
PRO_LINK_URL_UNSAFE
PRO_LINK_DESCRIPTION_REQUIRED
PRO_LINK_BROKEN
PRO_LINK_HEALTH_UNKNOWN
PRO_MEDIA_NOT_READY
PRO_MEDIA_PUBLIC_URL_UNAVAILABLE
PRO_MEDIA_PRIMARY_VIDEO_MISSING
PRO_QR_TARGET_INVALID
PRO_QR_NOT_READY
```

---

# 157. Hero video readiness

For Video CTA:

```text id="j9aa3n"
primary VIDEO_INTRO
published/current
public allowed
destination safe
```

---

# 158. Primary missing

Profile Hero remains valid but Video CTA absent/warning.

---

# 159. Search

Public Search may index:

- description;
- link/media type;
- perhaps video/showreel labels.

---

# 160. Public Search should not expose raw private URL if item hidden.

---

# 161. Search result

Usually routes to profile media section rather than raw external target.

---

# 162. PML-INV-008 — Search Does Not Bypass Profile Journey

---

# 163. SEO

Public media metadata may expose:

- VideoObject-like safe metadata later;
- public descriptions;
- stable public page route.

---

# 164. Do not put private/token URLs into public structured data.

---

# 165. Public Profile integration

Hero:

```text id="1ghwgu"
▶ Видеовизитка
```

---

# 166. Professional Media section

Can include:

```text id="02tlg6"
Video Intro
Showreel
Video Samples
Audio / Voice
Professional Links
```

---

# 167. UX hierarchy

Primary Video Intro/Showreel first.

Secondary links below.

---

# 168. No link dump

Large raw URL list without descriptions is poor UX.

---

# 169. PML-INV-009 — Description-Oriented Presentation

---

# 170. Homepage

May surface primary video.

Avoid excessive external-link clutter.

---

# 171. Casting Quick View

May include:

```text id="dlk9ph"
primary video
showreel
selected relevant links
```

---

# 172. Projects relationship

Project-specific video/link may belong Project relation, not global professional media list.

---

# 173. PML-INV-010 — Contextual Project Link Remains Project-Owned Where Appropriate

Global ProfessionalLink is for reusable profile-level professional destination.

---

# 174. Same destination reuse

If same URL intentionally used in multiple contexts:

prefer reference/reuse where model supports it.

Do not copy mutable URL unnecessarily.

---

# 175. Contacts relationship

Phone/WhatsApp/social contacts are Contacts domain, not ProfessionalLink, unless specifically modeled as professional media/social profile link by policy.

---

# 176. Social platform profile

Could be either:

- Contact/social identity;
- ProfessionalLink.

Ownership must be explicit.

---

# 177. Recommended

Contactable social identities belong Contacts.

Pure media/profile destination may belong ProfessionalLinks.

---

# 178. PML-INV-011 — Avoid Duplicate Social Source Across Modules

---

# 179. Casting AI

Casting AI can recommend:

```text id="6gkf0b"
include showreel
include voice sample
include specific relevant external material
```

based on explicit casting needs.

---

# 180. It cannot invent URL.

---

# 181. PML-AI-002 — AI Cannot Create External Destination

unless user explicitly supplied it and Human confirms save.

---

# 182. BB Assistant

Can draft descriptions:

```text id="nmh6f3"
Showreel — драматические и коммерческие сцены
```

---

# 183. Draft-only.

---

# 184. AI cannot claim content inside link unless source/context supports it.

---

# 185. Theme AI

May style player/link cards.

Cannot:

- modify href;
- conceal URL while changing destination;
- remove QR quiet zone;
- make inaccessible controls.

---

# 186. VOP

May detect:

```text id="lbpb02"
broken link
redirected destination
missing description
duplicate URL
unsafe URL
QR stale after URL change
primary video unavailable
```

---

# 187. VOP safe operations

May:

```text id="jttuzp"
trigger link check
rebuild QR
retry media derivative
invalidate cache
```

---

# 188. VOP requires confirmation for

```text id="eokxio"
replace URL
archive link
change visibility
set primary
publish
```

---

# 189. Duplicate URLs

Same canonical URL may be duplicate candidate.

---

# 190. PML-INV-012 — Duplicate URL Detection Does Not Auto-Merge

Different descriptions/context may intentionally point same destination.

---

# 191. Managed Media and external link duplication

If local MediaAsset and YouTube upload contain same video:

they are distinct technical destinations.

Admin may choose preferred presentation.

---

# 192. No automatic content equivalence assumption.

---

# 193. Primary destination priority

For reliability/privacy, app-controlled managed URL may be preferred where local media is intentionally published.

But external official platform may still be chosen.

Human decides.

---

# 194. Lifecycle and archive

Archive ProfessionalLink:

- removes current public;
- removes new Questionnaire/Builder selection;
- removes Search;
- invalidates current QR aliases/config.

Historical QNR preserved.

---

# 195. Archive media-backed professional item

Does not delete MediaAsset.

---

# 196. PML-INV-013 — Archive Link/Media Presentation ≠ Delete Underlying Resource

---

# 197. Restore

Preferred:

```text id="h9rnqg"
ARCHIVED → DRAFT
```

---

# 198. No auto-publish.

---

# 199. Hard delete

Dependency-aware.

---

# 200. Dependencies

Potential:

```text id="a9fn4g"
Questionnaire Draft
Questionnaire Revision
Builder Snapshot
QR Artifact
Casting material
Search
Cache
AI source snapshot
```

---

# 201. Historical URL

Should remain frozen in Revision even if current Source archived.

---

# 202. Access can later be revoked

according to security/privacy policy.

---

# 203. PML-INV-014 — Historical Meaning and Current Access Are Separate

---

# 204. Link privacy

A token-scoped URL may be professionally valid but sensitive.

---

# 205. Logging

Never log raw bearer token unnecessarily.

---

# 206. QR artifact

Must not expose raw token in logs/filenames.

---

# 207. Short links

System-controlled short URL MAY be introduced later.

---

# 208. If used

Short-link redirect itself becomes controlled Source/Derived routing with:

- stable identity;
- access rules;
- analytics;
- revocation.

---

# 209. PML-URL-007 — Do Not Depend on Uncontrolled Public URL Shorteners Baseline

---

# 210. Link analytics

May track:

```text id="olpuuy"
professional_link_clicked
video_started
showreel_opened
audio_started
qr_destination_opened
```

where measurable.

---

# 211. Privacy

Do not embed private target query data into analytics payload.

---

# 212. Analytics does not change primary selection automatically.

---

# 213. PML-INV-015 — Popularity Does Not Become Professional Authority

---

# 214. Commands — Professional Links

Canonical:

```text id="4ltsf4"
CreateProfessionalLink
UpdateProfessionalLink
UpdateProfessionalLinkVisibility
ReorderProfessionalLinks
PublishProfessionalLink
ArchiveProfessionalLink
RestoreProfessionalLink
SetPrimaryVideoIntro
SetPrimaryShowreel
RequestProfessionalLinkHealthCheck
```

---

# 215. Commands — Managed professional media

Depending implementation:

```text id="trhcr6"
CreateProfessionalMediaItem
UpdateProfessionalMediaItem
UpdateProfessionalMediaVisibility
PublishProfessionalMediaItem
ArchiveProfessionalMediaItem
RestoreProfessionalMediaItem
```

---

# 216. Queries

```text id="cu5u6u"
GetPublicProfessionalMedia
GetAdminProfessionalMedia
GetProfessionalLink
GetProfessionalMediaReadiness
GetPrimaryVideoIntro
GetPrimaryShowreel
ListQuestionnaireEligibleProfessionalLinks
ListBuilderEligibleProfessionalLinks
GetProfessionalLinkHealth
PreviewPublicProfessionalMedia
```

---

# 217. Create ProfessionalLink input

Conceptually:

```text id="2645qj"
linkType
description
url
visibility
```

---

# 218. Server computes

```text id="2zdy4r"
canonical_url
```

---

# 219. Client cannot set

```text id="97hps4"
health=HEALTHY
checkedAt
qrReady
canonicalUrlOverride
publishedAt
```

as authority.

---

# 220. Managed media input

```text id="i4978y"
mediaAssetId
professionalType
description
visibility
```

---

# 221. Server validates

```text id="h5brp0"
same profile
correct MediaAsset type
ready/safe
stable publish route available
```

---

# 222. Reorder

Ordered IDs with collection version.

---

# 223. Primary video

Must be correct professional type and currently eligible.

---

# 224. PML-INV-016 — Primary Cannot Point to Archived/Invalid Media

---

# 225. Error taxonomy

At minimum:

```text id="dwn7zw"
PRO_LINK_NOT_FOUND
PRO_LINK_TYPE_INVALID
PRO_LINK_DESCRIPTION_REQUIRED
PRO_LINK_URL_REQUIRED
PRO_LINK_URL_INVALID
PRO_LINK_URL_UNSAFE
PRO_LINK_DUPLICATE_CANDIDATE
PRO_LINK_ARCHIVED
PRO_LINK_NOT_READY
PRO_LINK_HEALTH_FAILED
PRO_MEDIA_NOT_FOUND
PRO_MEDIA_WRONG_TYPE
PRO_MEDIA_WRONG_PROFILE
PRO_MEDIA_NOT_READY
PRO_MEDIA_PUBLIC_URL_UNAVAILABLE
PRO_PRIMARY_VIDEO_INVALID
PRO_PRIMARY_SHOWREEL_INVALID
PRO_LINK_QUESTIONNAIRE_NOT_ELIGIBLE
PRO_LINK_BUILDER_NOT_ELIGIBLE
PRO_QR_TARGET_INVALID
```

---

# 226. Concurrency

ProfessionalLink uses `version`.

---

# 227. URL edit

Requires expectedVersion.

---

# 228. Primary switch

Atomic/transaction-safe.

---

# 229. Reorder

Collection version or equivalent.

---

# 230. Visibility update

Transaction:

```text id="2chpsi"
BEGIN
 update Source
 Audit
 Outbox
COMMIT
```

then derived invalidation.

---

# 231. External health check

Never inside mutation transaction.

---

# 232. QR generation

Never inside mutation transaction.

---

# 233. Video transcoding

Media subsystem/background job only.

---

# 234. Outbox events

Suggested:

```text id="43x2kr"
ProfessionalLinkCreated
ProfessionalLinkUpdated
ProfessionalLinkUrlChanged
ProfessionalLinkPublished
ProfessionalLinkArchived
ProfessionalLinkVisibilityChanged
ProfessionalMediaChanged
PrimaryProfessionalMediaChanged
```

---

# 235. Consumers

```text id="no4u06"
Public projections
Hero
Questionnaire readiness
Builder eligibility
QR invalidation/generation
Search
Cache
VOP
Analytics
```

---

# 236. URL change event

Should include:

```text id="lw88re"
link_id
version
changed_url=true
```

not raw secret URL if token-sensitive.

---

# 237. QR invalidation

Current derived QR for old canonical URL marked stale/superseded.

---

# 238. Search

Reindex description/type.

---

# 239. Link-health record

Recheck new destination.

---

# 240. Audit

High-impact:

```text id="5rp0tk"
URL change
publication
archive/restore
visibility
Primary Video change
token/public access-class change
```

---

# 241. Routine health result

Operational log, not business Audit.

---

# 242. URL old/new values

For ordinary public URLs Audit MAY retain safe values.

For bearer/token-sensitive URLs, avoid raw token retention.

---

# 243. Cache

Public Professional Media projection cache.

---

# 244. Current visibility revocation

Must prevent stale public exposure.

---

# 245. PML-INV-017 — Cache Cannot Keep Revoked Link Public

---

# 246. Link health cache

Operational and separate from professional content cache.

---

# 247. Questionnaire snapshot

At publish, freeze exact:

```text id="bs4dpg"
description
canonical URL
ordering
show_link
show_qr
QR artifact identity/settings
media type
```

as applicable.

---

# 248. Current URL later changes

Old Questionnaire remains original.

---

# 249. Re-render historical PDF

Uses frozen Revision URL.

---

# 250. PML-QNR-005 — Historical Rerender Never Uses Current URL

---

# 251. If old destination revoked/broken

Historical PDF still contains historical destination semantics unless redaction/security policy requires otherwise.

---

# 252. Current readiness

May warn:

```text id="pu97mm"
historical link no longer reachable
```

without rewriting PDF.

---

# 253. Public Builder snapshot

Freezes exact selected URLs/config at generation.

---

# 254. Current link change afterward

Generated Builder PDF remains unchanged.

---

# 255. PML-BLD-002 — Generated Builder Snapshot Is Immutable

---

# 256. Local media URLs in Builder

Must be stable beyond immediate render if expected recipient will open them later.

---

# 257. PML-URL-008 — Do Not Put Temporary Upload URLs Into Final PDF

---

# 258. Link-health effect on Builder

A known permanently broken link should block selection/generation or produce explicit readiness blocker.

A transient check failure may be warning.

---

# 259. Health policy should distinguish

```text id="6mhrba"
INVALID
BROKEN_CONFIRMED
TRANSIENT_FAILURE
UNKNOWN
```

---

# 260. Public failure handling

If external video provider unavailable:

Public Profile remains functional.

Show graceful fallback/link/error.

---

# 261. PML-INV-018 — External Provider Outage Cannot Break Core Profile

---

# 262. Managed media outage

If local media subsystem fails, affected media CTA degrades but unrelated Site remains available.

---

# 263. AI outage

No impact on link/media playback itself.

---

# 264. Accessibility

External link text should be meaningful.

Avoid many identical:

```text id="3qvz54"
Открыть
Открыть
Открыть
```

without context.

---

# 265. Better

```text id="atm9g6"
Открыть видеовизитку
Открыть showreel
Прослушать voice sample
```

---

# 266. External target indication

UI MAY indicate external destination where useful.

---

# 267. New tab

If external links open new tab, provide accessible/security-safe behavior.

---

# 268. Player controls

No inaccessible custom-only UI.

---

# 269. PDF accessibility

URL should remain selectable/clickable.

QR is supplementary.

---

# 270. PML-INV-019 — QR Is Never Sole Accessibility Path

---

# 271. Migration

Legacy media/link records must be classified into:

```text id="n31pyv"
Managed Media
ProfessionalLink
```

---

# 272. Migration rule

If record only contains URL:

default candidate = ProfessionalLink.

---

# 273. If file exists under managed storage

candidate = MediaAsset-backed media item.

---

# 274. PML-MIG-001 — Migration Must Not Pretend URL Is Local File

---

# 275. Existing video links

Preserve exact URL.

Canonicalize only safe normalization.

---

# 276. Legacy descriptions

Preserve.

Missing description requires review.

---

# 277. Legacy duplicate URLs

Preserve + flag.

No silent merge.

---

# 278. Legacy QR

If existing QR image cannot be proven to decode exact canonical URL:

regenerate.

---

# 279. PML-MIG-002 — QR Must Be Decode-Verified

---

# 280. Historical documents

Not rewritten.

---

# 281. Migration idempotency

Same source record must not create duplicate ProfessionalLink.

---

# 282. Anti-patterns

`PML-AP-001`  
Store YouTube URL as fake MediaAsset.

`PML-AP-002`  
Store local filesystem path in Questionnaire.

`PML-AP-003`  
Use temporary signed storage URL as printed QR target.

`PML-AP-004`  
Use `/admin/...` in PDF.

`PML-AP-005`  
Allow `javascript:` links.

`PML-AP-006`  
Trust client canonical URL.

`PML-AP-007`  
URL redirect automatically mutates Source.

`PML-AP-008`  
No description, only raw URLs everywhere.

`PML-AP-009`  
Hardcode Link № as permanent entity field.

`PML-AP-010`  
PDF URL styled like link but not clickable.

`PML-AP-011`  
QR replaces visible hyperlink.

`PML-AP-012`  
QR destination differs from hyperlink.

`PML-AP-013`  
QR generated without decode validation.

`PML-AP-014`  
Public QR points to private asset.

`PML-AP-015`  
Builder edits link URL.

`PML-AP-016`  
Builder edits professional description Source.

`PML-AP-017`  
Current URL change rewrites historical QNR.

`PML-AP-018`  
Historical PDF re-render resolves current URL.

`PML-AP-019`  
External health check happens synchronously on every page load.

`PML-AP-020`  
Health checker follows redirects to internal network.

`PML-AP-021`  
Health checker downloads entire large video.

`PML-AP-022`  
Temporary outage auto-deletes ProfessionalLink.

`PML-AP-023`  
Broken link auto-archives without Human review.

`PML-AP-024`  
One `is_public` controls all surfaces.

`PML-AP-025`  
Public-hidden URL sent to browser then hidden.

`PML-AP-026`  
Primary video stored again in Profile as duplicate URL.

`PML-AP-027`  
AI invents missing video URL.

`PML-AP-028`  
AI sets Primary video automatically.

`PML-AP-029`  
Analytics chooses Primary by clicks.

`PML-AP-030`  
Theme changes href destination.

`PML-AP-031`  
Theme makes QR too small/unscannable.

`PML-AP-032`  
Archive media item deletes shared MediaAsset.

`PML-AP-033`  
Social profile duplicated independently in Contacts and ProfessionalLinks without ownership rule.

`PML-AP-034`  
Public Search links straight to secret token target.

`PML-AP-035`  
Raw token URL stored in ordinary analytics payload.

`PML-AP-036`  
Token copied into QR filename.

`PML-AP-037`  
Local media public URL changes when disk path changes.

`PML-AP-038`  
External provider outage crashes Profile.

`PML-AP-039`  
Video autoplay with sound by default.

`PML-AP-040`  
PDF embeds video as sole way to access it.

`PML-AP-041`  
Managed file reclassified by modifying MediaAsset type.

`PML-AP-042`  
Current broken health status rewrites historical QNR.

`PML-AP-043`  
Duplicate URLs automatically merged.

`PML-AP-044`  
URL shortened using uncontrolled third-party shortener by default.

`PML-AP-045`  
Old QR cache reused after URL change.

`PML-AP-046`  
External embed assumed possible for every domain.

`PML-AP-047`  
Health state treated as business Source.

`PML-AP-048`  
Private certificate/document link placed in ProfessionalLinks without access review.

`PML-AP-049`  
Link ordering derived from CreatedAt.

`PML-AP-050`  
Runtime AI required to show/play professional media.

---

# 283. Core invariants

`PML-INV-020`  
ProfessionalLink and MediaAsset remain different Source types.

`PML-INV-021`  
Managed local media uses app-controlled stable URL.

`PML-INV-022`  
Filesystem/storage locator never enters Public/PDF URL.

`PML-INV-023`  
Professional media type is contextual metadata.

`PML-INV-024`  
External links use canonical URL.

`PML-INV-025`  
Canonicalization preserves destination semantics.

`PML-INV-026`  
Unsafe schemes are rejected.

`PML-INV-027`  
Internal/admin/private URLs are not ordinary public professional targets.

`PML-INV-028`  
Link description is Human-readable professional metadata.

`PML-INV-029`  
Questionnaire rows preserve number/description/URL semantics.

`PML-INV-030`  
Row number derives from display order.

`PML-INV-031`  
Display order is explicit.

`PML-INV-032`  
Public/QNR/Builder visibility dimensions are independent.

`PML-INV-033`  
Published does not imply exposure on every surface.

`PML-INV-034`  
Hero Video CTA derives from current eligible professional media.

`PML-INV-035`  
Primary media is Human-selected.

`PML-INV-036`  
AI cannot set Primary media.

`PML-INV-037`  
PDF hyperlink is actual clickable annotation.

`PML-INV-038`  
HTML hyperlink uses exact current/frozen canonical URL.

`PML-INV-039`  
QR is supplementary and independently configurable.

`PML-INV-040`  
QR target exactly equals canonical intended URL.

`PML-INV-041`  
QR is decode-validated before READY.

`PML-INV-042`  
QR never points to Admin/private/raw storage route.

`PML-INV-043`  
URL mutation invalidates current QR.

`PML-INV-044`  
Historical QR semantics remain frozen.

`PML-INV-045`  
Builder selects representation but cannot edit Source URL.

`PML-INV-046`  
Builder Generate revalidates URL/access/eligibility.

`PML-INV-047`  
Final PDFs never contain temporary upload links.

`PML-INV-048`  
Link health is derived operational state.

`PML-INV-049`  
Health check never mutates Source URL automatically.

`PML-INV-050`  
Link checker is SSRF-protected.

`PML-INV-051`  
Public rendering does not synchronously depend on health checker.

`PML-INV-052`  
Temporary provider outage does not delete Source.

`PML-INV-053`  
External provider outage does not break core Profile.

`PML-INV-054`  
Archived presentation does not delete underlying MediaAsset.

`PML-INV-055`  
Historical Questionnaire freezes exact description/URL/config.

`PML-INV-056`  
Current URL change never rewrites historical Revision.

`PML-INV-057`  
Historical re-render uses frozen URL.

`PML-INV-058`  
Search cannot leak hidden/private destinations.

`PML-INV-059`  
SEO cannot expose token/private URLs.

`PML-INV-060`  
Analytics cannot expose private target data unnecessarily.

`PML-INV-061`  
AI cannot invent destination URL.

`PML-INV-062`  
BB descriptions remain Draft until Human Apply.

`PML-INV-063`  
Theme cannot change destination semantics.

`PML-INV-064`  
Theme cannot weaken QR readability.

`PML-INV-065`  
QR is never sole accessibility mechanism.

`PML-INV-066`  
External embed support is provider-specific.

`PML-INV-067`  
Duplicate URL detection does not auto-merge.

`PML-INV-068`  
Social/contact ownership boundaries must be explicit.

`PML-INV-069`  
Core professional media workflows operate without AI.

`PML-INV-070`  
All high-impact mutations remain server-validated/version-aware.

---

# 284. E2E-PML-001 — External video

Create YouTube ProfessionalLink.

Expected external-link Source, not MediaAsset.

---

# 285. E2E-PML-002 — Managed video

Select VIDEO MediaAsset.

System creates professional media relation and safe stable app URL.

---

# 286. E2E-PML-003 — Filesystem path

Attempt:

```text id="wjr2gf"
/data/kate-actor/video.mp4
```

as public URL.

Rejected.

---

# 287. E2E-PML-004 — JavaScript URL

Rejected.

---

# 288. E2E-PML-005 — Localhost URL

Rejected for Public ProfessionalLink.

---

# 289. E2E-PML-006 — URL whitespace

Safe normalization produces exact canonical URL.

---

# 290. E2E-PML-007 — URL credentials

Embedded username/password rejected.

---

# 291. E2E-PML-008 — Description

Published ordinary professional link requires meaningful description according to readiness policy.

---

# 292. E2E-PML-009 — Reorder

External link numbering changes with order.

Entity IDs unchanged.

---

# 293. E2E-PML-010 — Public-only

Link visible on Site only.

---

# 294. E2E-PML-011 — Questionnaire-only

Hidden Public, eligible prepared Questionnaire if destination safe.

---

# 295. E2E-PML-012 — Builder-only

Visible only in Builder when destination is appropriate.

---

# 296. E2E-PML-013 — Hidden serialization

Private link absent from Public browser/Search/SEO.

---

# 297. E2E-PML-014 — Primary video

Eligible VIDEO_INTRO set as primary.

Hero CTA resolves it.

---

# 298. E2E-PML-015 — Invalid primary

Archived/private invalid item cannot become active public Primary Video.

---

# 299. E2E-PML-016 — Primary AI suggestion

AI recommendation alone does not change Primary.

---

# 300. E2E-PML-017 — PDF table

Generated PDF contains:

```text id="9ga0hi"
№ | Описание | URL
```

with actual clickable links.

---

# 301. E2E-PML-018 — Long URL

PDF wraps display safely while actual href remains exact.

---

# 302. E2E-PML-019 — HTML link

Accessible anchor points to exact canonical URL.

---

# 303. E2E-PML-020 — QR enabled

Hyperlink and QR both target exact same URL.

---

# 304. E2E-PML-021 — QR decode

Generated QR is decoded in test and equals canonical URL byte-for-byte after canonical serialization rule.

---

# 305. E2E-PML-022 — QR private route

Admin/private route rejected.

---

# 306. E2E-PML-023 — QR URL change

Change current URL.

Old current QR marked stale; new QR generated.

---

# 307. E2E-PML-024 — Historical QR

Old Questionnaire Revision still points to frozen old URL/QR semantics.

---

# 308. E2E-PML-025 — Builder toggles QR

Visitor may enable/disable allowed QR representation without changing Source.

---

# 309. E2E-PML-026 — Builder URL tamper

Visitor submits alternate URL.

Server loads saved Source URL instead.

---

# 310. E2E-PML-027 — Builder permission revoked

Generate blocks/removes link.

---

# 311. E2E-PML-028 — Temporary signed URL

Attempt use short-lived storage URL in final PDF.

Readiness rejects.

---

# 312. E2E-PML-029 — Health healthy

Checker records HEALTHY without changing Source.

---

# 313. E2E-PML-030 — Redirect

External link redirects.

Health checker records resolved destination but does not rewrite Source automatically.

---

# 314. E2E-PML-031 — Redirect to private network

Health checker blocks redirect for SSRF safety.

---

# 315. E2E-PML-032 — Large remote video

Health checker does not download full body.

---

# 316. E2E-PML-033 — Temporary outage

Provider timeout marks operational failure/degraded state.

ProfessionalLink remains.

---

# 317. E2E-PML-034 — Public outage

External provider unavailable.

Profile and unrelated sections still render.

---

# 318. E2E-PML-035 — Managed media unavailable

Affected CTA degrades safely; core Profile remains.

---

# 319. E2E-PML-036 — Historical QNR

Current URL changes from A to B.

Historical Revision PDF rerender still uses A.

---

# 320. E2E-PML-037 — Current QNR

New revision after URL change uses B.

---

# 321. E2E-PML-038 — Archive

Link disappears from current Public/QNR/Builder selections.

---

# 322. E2E-PML-039 — Archive MediaAsset safety

Archiving professional media presentation does not delete shared underlying MediaAsset.

---

# 323. E2E-PML-040 — Restore

Returns Draft/non-public state.

---

# 324. E2E-PML-041 — Duplicate URL

Two entries same URL with different professional contexts trigger candidate warning, no auto-merge.

---

# 325. E2E-PML-042 — Search

Public search can find showreel description, but not hidden URL.

---

# 326. E2E-PML-043 — SEO token safety

Token-scoped Builder/Casting URL absent from public metadata.

---

# 327. E2E-PML-044 — BB description

BB drafts link description.

No automatic Source mutation/publication.

---

# 328. E2E-PML-045 — AI invented URL

AI-suggested nonexistent destination cannot become Source automatically.

---

# 329. E2E-PML-046 — Theme

Theme restyles card/player but actual href remains unchanged.

---

# 330. E2E-PML-047 — QR size

PDF theme cannot reduce QR below subsystem minimum.

---

# 331. E2E-PML-048 — Accessibility

Screen reader receives meaningful link names.

---

# 332. E2E-PML-049 — Video keyboard

Player operable using keyboard.

---

# 333. E2E-PML-050 — No AI

All links/media/QR/playback core workflows function with AI provider disabled.

---

# 334. E2E-PML-051 — Migration external URL

Legacy URL maps to ProfessionalLink.

---

# 335. E2E-PML-052 — Migration local media

Legacy managed file maps to MediaAsset-backed professional media.

---

# 336. E2E-PML-053 — Legacy QR mismatch

Existing QR decodes to wrong destination.

It is not reused.

---

# 337. E2E-PML-054 — Link health stale

Old HEALTHY state does not bypass current URL validation after URL mutation.

---

# 338. E2E-PML-055 — Primary video archived

Hero Video CTA immediately stops resolving archived primary.

---

# 339. Architecture diagram

```text id="a901xw"
                Professional Media
                  /           \
                 /             \
        Managed Media        External
             │                  │
        MediaAsset        ProfessionalLink
             │                  │
             └───────┬──────────┘
                     ▼
              Professional
               Projection
             /      |      \
            ▼       ▼       ▼
         Public    QNR    Builder
                     │
                     ▼
               Link + QR
```

---

# 340. Questionnaire link flow

```text id="ashqg4"
ProfessionalLink
      ↓
Canonical URL
      ↓
Questionnaire Selection
      ↓
Snapshot
      ↓
№ | Description | URL
                  │
        ┌─────────┴────────┐
        ▼                  ▼
    Hyperlink              QR
                           │
                           ▼
                   Decode Validation
```

---

# 341. Managed media flow

```text id="nysszz"
Upload
  ↓
MediaAsset
  ↓
Processing
  ↓
Stable Public/Casting-Safe URL
  ↓
Professional Media Item
  ↓
Video / Audio / QNR / QR
```

---

# 342. URL change flow

```text id="2li6xc"
Current URL A
   ↓
QR A READY
   ↓
URL changed to B
   ↓
Current QR A = STALE
   ↓
QR B generated
   ↓

Historical QNR Revision:
still URL A / QR A semantics
```

---

# 343. Health flow

```text id="dw8mxg"
ProfessionalLink
      ↓
Scheduled Health Check
      ↓
SSRF-Safe Resolver
      ↓
Status
 ┌────┼─────┬─────────┐
 ▼    ▼     ▼         ▼
Healthy Degraded Broken Unknown
      │
      ▼
Admin/VOP recommendation
```

---

# 344. Visibility diagram

```text id="xvhqt8"
Professional Media / Link
       │
       ├── Public Site
       ├── Admin Questionnaire
       └── Public Builder

URL safety/access class is checked separately.
```

---

# 345. Quality gate

Перед implementation должны быть подтверждены:

- [ ] ProfessionalLink entity;
- [ ] managed-media professional relation;
- [ ] controlled professional media types;
- [ ] MediaAsset vs URL separation;
- [ ] canonical URL normalization;
- [ ] URL scheme allowlist;
- [ ] internal/private route rejection;
- [ ] stable local media route;
- [ ] Primary Video Intro semantics;
- [ ] Primary Showreel semantics if enabled;
- [ ] exact `№ / описание / URL` representation;
- [ ] actual HTML hyperlinks;
- [ ] actual PDF hyperlink annotations;
- [ ] per-item `show_link`;
- [ ] per-item `show_qr`;
- [ ] QR exact canonical URL rule;
- [ ] QR decode validation;
- [ ] QR invalidation on URL change;
- [ ] stable/token-safe QR destinations;
- [ ] visibility triplet;
- [ ] Builder selection-only rules;
- [ ] historical QNR URL freeze;
- [ ] link-health states;
- [ ] SSRF-safe health checker;
- [ ] no synchronous health check on Public render;
- [ ] Hero integration;
- [ ] Search/SEO;
- [ ] BB/AI boundaries;
- [ ] VOP actions;
- [ ] archive/restore;
- [ ] migration classification;
- [ ] accessibility;
- [ ] E2E coverage.

---

# 346. Acceptance criteria

`AC-PML-001`  
External URL и MediaAsset существуют как разные Source types.

`AC-PML-002`  
Локально размещённый media resource получает stable application-controlled URL.

`AC-PML-003`  
Raw filesystem/storage locator никогда не попадает в Public/PDF.

`AC-PML-004`  
Professional media type является controlled context metadata.

`AC-PML-005`  
External links имеют canonical URL.

`AC-PML-006`  
Unsafe/internal/Admin URLs не публикуются.

`AC-PML-007`  
Questionnaire сохраняет семантику `№ / описание / URL`.

`AC-PML-008`  
HTML URL является кликабельной ссылкой.

`AC-PML-009`  
PDF URL является реальной hyperlink annotation.

`AC-PML-010`  
Link number derives from selected display order.

`AC-PML-011`  
Public/QNR/Builder permissions независимы.

`AC-PML-012`  
Hero Video CTA resolves current eligible Primary Video Intro.

`AC-PML-013`  
Primary professional media определяется Human action.

`AC-PML-014`  
QR можно включать/выключать per item/per Questionnaire.

`AC-PML-015`  
QR не заменяет hyperlink по умолчанию.

`AC-PML-016`  
QR кодирует exact canonical intended URL.

`AC-PML-017`  
QR decode validation выполняется до READY.

`AC-PML-018`  
QR не кодирует private/Admin/raw-storage URL.

`AC-PML-019`  
URL mutation invalidates current QR.

`AC-PML-020`  
Historical Questionnaire сохраняет frozen URL/QR semantics.

`AC-PML-021`  
Historical PDF rerender использует frozen, а не current URL.

`AC-PML-022`  
Builder не может редактировать Source URL/description.

`AC-PML-023`  
Builder Generate revalidates current eligibility/access.

`AC-PML-024`  
Final PDF не использует ephemeral upload/storage URLs.

`AC-PML-025`  
Link health является derived operational state.

`AC-PML-026`  
Health checker защищён от SSRF.

`AC-PML-027`  
Health checker не изменяет Source URL автоматически.

`AC-PML-028`  
Public page не выполняет synchronous external health check.

`AC-PML-029`  
Temporary provider failure не удаляет ProfessionalLink.

`AC-PML-030`  
External provider outage не ломает основной Profile.

`AC-PML-031`  
Search/SEO не раскрывают hidden/token destinations.

`AC-PML-032`  
AI не может автоматически создавать URL или выбирать Primary.

`AC-PML-033`  
Theme не может менять href/QR semantics.

`AC-PML-034`  
QR остаётся supplementary, а не sole-access mechanism.

`AC-PML-035`  
Archive presentation не удаляет underlying MediaAsset.

`AC-PML-036`  
Core professional media workflow работает без AI.

`AC-PML-037`  
Legacy URL/file migration сохраняет правильный Source type.

`AC-PML-038`  
Legacy QR используется только после decode verification.

`AC-PML-039`  
Concurrency, visibility, historical URL и QR behavior имеют deterministic E2E tests.

---

# 347. Финальная доктрина

> **Professional Media & Links Module является единым профессиональным слоем доступа к видеовизитке, showreel, video/audio samples и внешним ресурсам, но строго различает управляемые MediaAssets и ProfessionalLinks. Каждый публикуемый destination имеет безопасный canonical URL, а локальные media resources используют стабильный application-controlled URL вместо filesystem/storage paths. Questionnaire и PDF сохраняют точную семантику `№ / описание / URL`, обеспечивают реальные кликабельные hyperlinks и, при необходимости, отдельный decode-validated QR для exact того же canonical destination. Public Site, prepared Questionnaire и Public Builder имеют независимые permissions; URL health остаётся derived operational state и никогда автоматически не переписывает Professional Source. Historical Questionnaires freeze exact URL/QR semantics, поэтому последующее изменение current link не изменяет ранее опубликованные профессиональные материалы.**