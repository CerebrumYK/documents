# UPLOAD, URL & CONTENT SECURITY
## DOC-145 — file uploads, media processing, SSRF, URL validation, embeds, rich text, XSS и content delivery safety

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет безопасность всего untrusted content ingress: uploads, images/video/audio/documents, external URLs, provider embeds, Feedback/Casting/Help text, AI output и rendered rich content.

> **Every uploaded byte, URL and textual payload is untrusted until validated for its exact use. Display safety, fetch safety, storage safety and publication eligibility are separate decisions.**

---

## 2. Threat classes

Основные риски:

```text
malware
polyglot files
content-type spoofing
path traversal
stored XSS
SVG script injection
malicious PDF/document
parser exploits
oversized decompression/resource exhaustion
SSRF
DNS rebinding
unsafe redirect chains
open redirect
prompt injection
private-data publication
```

## 3. Upload lifecycle

Recommended:

```text
UPLOADING
→ STORED_PRIVATE
→ VALIDATING
→ READY
   or QUARANTINED / REJECTED / FAILED
```

A file not in `READY` is not eligible for public use unless a domain-specific secure exception exists.

## 4. File size

Maximum sizes defined by file class and deployment capabilities. Request body limit enforced before expensive parsing where possible.

Never accept “unlimited” upload and depend on filesystem exhaustion handling.

## 5. File names

Original filename is display metadata only.

Storage key/path is generated server-side from stable random/content identity.

Reject/normalize path separators/control chars and sanitize filename for download.

### CONT-INV-001
Original filename never controls filesystem path.

## 6. MIME/content verification

Do not trust browser `Content-Type` or extension alone.

Use:

- extension as hint;
- MIME signature/magic bytes;
- parser/probe output;
- explicit allowlist.

Mismatch is rejected/quarantined.

## 7. Images

Accepted image processing should decode image with trusted library and produce known-safe derivatives.

Controls:

- pixel dimension bounds;
- decompression-bomb protection;
- metadata stripping where privacy policy requires;
- orientation normalization without altering original semantics;
- immutable original retained.

## 8. EXIF/privacy

Public derivatives should normally remove unnecessary EXIF/GPS/device metadata unless explicit professional use requires it.

### CONT-INV-002
Public image publication does not implicitly publish embedded location metadata.

## 9. SVG

SVG is active-content-capable.

Baseline options:

- reject uploaded SVG; or
- sanitize/re-render through dedicated safe pipeline.

Generated internal QR SVG is separate trusted generator output and contains no scripts/external references.

## 10. Video/audio

Use probe/transcode pipeline with:

- allowed codecs/container policy;
- duration/size limits;
- timeout/resource limits;
- isolated worker where practical;
- no command-shell interpolation of filename/user metadata.

Never execute user-provided shell fragments via ffmpeg command construction.

## 11. Documents/PDF

Uploaded documents are private-by-default. Inline browser rendering requires carefully controlled response headers/sandbox behavior.

For arbitrary documents, prefer forced download or sanitized/rendered preview instead of trusting active document content.

Generated Questionnaire PDF is internal trusted renderer output but still must avoid unsafe URLs/scripts/attachments.

## 12. Malware scanning

Where scanner is deployed, file remains unavailable until scan passes.

Scanner timeout/failure ≠ clean result.

## 13. Quarantine

Quarantined file:

- private;
- excluded from Media public projections;
- not downloadable by ordinary public/token clients;
- visible to authorized Admin with safe diagnostics;
- eligible for purge.

## 14. Orphan uploads

Staged uploads not committed to domain record are cleaned after TTL.

Cleanup must not delete a file that became referenced concurrently; reference check/transactional binding required.

## 15. Storage permissions

Upload/original storage resides outside public web root where possible. Application resolver controls access.

## 16. Download headers

Private downloads should set safe:

- Content-Type from validated metadata;
- Content-Disposition;
- X-Content-Type-Options;
- cache policy;
- CSP/sandbox for inline content where relevant.

## 17. URL classification

Separate decisions:

```text
VALID_FOR_DISPLAY
VALID_FOR_LINK_NAVIGATION
VALID_FOR_SERVER_FETCH
VALID_FOR_EMBED
VALID_FOR_PUBLIC_QR
```

A URL may be safe for user navigation but unsafe for server fetch/embed.

## 18. URL parsing

Use standards-based parser. Reject malformed/ambiguous URLs, embedded credentials and disallowed schemes.

Allowed public professional navigation baseline: `https`, with controlled `http` exception only if explicitly accepted; prefer HTTPS.

## 19. Server-side fetch / SSRF

Before connection:

- scheme allowlist `http/https`;
- resolve hostname;
- block localhost/loopback/private/link-local/multicast/reserved/metadata ranges;
- enforce port policy;
- timeout;
- bounded response size;
- no credentials forwarding.

Every redirect target is re-parsed/re-resolved/revalidated.

## 20. DNS rebinding

Where runtime permits, ensure the actual resolved connection address remains outside blocked ranges. Do not validate DNS once and then blindly follow different resolved address.

## 21. Redirect limits

Use small maximum redirect hops. Loop/too-many redirects becomes health-check failure, not reason to loosen SSRF policy.

## 22. Link health checker

Uses HEAD/limited GET where provider behavior permits, bounded bytes/time, and never downloads arbitrary large media synchronously.

Provider/auth failure is health metadata, not automatic Source URL mutation.

## 23. Open redirects

Application redirect endpoints resolve destination from stored validated relation/allowlist. Generic `/redirect?url=` is forbidden for public use.

## 24. Embeds

No arbitrary user/provider raw iframe/script HTML.

Embed flow:

```text
validated provider URL
→ known provider parser
→ safe provider ID
→ application-controlled embed template
```

Unsupported provider falls back to clickable link.

## 25. CSP for embeds

Only explicitly supported provider domains are added to CSP. No `frame-src *` convenience policy.

## 26. Rich text

Prefer plain text or restricted Markdown. If HTML output is supported, sanitize with allowlist.

Forbidden in user content:

- script;
- inline event handlers;
- unsafe style/url constructs;
- iframe except controlled embed;
- object/embed;
- javascript/data execution.

## 27. Stored XSS sources

Particularly treat as untrusted:

- Feedback sender data/message;
- Casting briefs;
- Help Ticket messages;
- Project/import text;
- social/provider data;
- AI output.

Framework escaping remains enabled.

## 28. AI/prompt injection

Text telling AI to ignore rules is content, not instruction authority.

AI receives structured source with explicit trust labels/task contract and no unrestricted tools.

### CONT-INV-003
Prompt injection cannot grant filesystem/network/database/provider authority.

## 29. HTML/PDF generation

Document renderer must escape dynamic text and generate hyperlinks from validated URLs. No raw source HTML injection into PDF template.

## 30. QR target safety

QR generator accepts approved canonical target from domain, not arbitrary browser URL.

## 31. Media URLs

Public URL routes through stable application-controlled resolver. Never expose `/data/kate-actor/...` or other filesystem locator.

## 32. Cache/content delivery

Private attachments/media get private/no-store or scoped cache policy. Public immutable derivatives may use content-addressed long caching.

## 33. Abuse/resource exhaustion

Controls:

- concurrent upload limits;
- per-request size limits;
- worker resource/time limits;
- rate limits;
- queue bounds/backpressure;
- cleanup of failed/staged artifacts.

## 34. Error responses

Return normalized errors without parser stack traces/storage paths.

Examples:

```text
UPLOAD_TOO_LARGE
UPLOAD_TYPE_UNSUPPORTED
UPLOAD_CONTENT_MISMATCH
UPLOAD_QUARANTINED
URL_INVALID
URL_SCHEME_FORBIDDEN
URL_TARGET_FORBIDDEN
EMBED_PROVIDER_UNSUPPORTED
```

## 35. Anti-patterns

Forbidden:

1. Store file under user filename/path.
2. Trust extension only.
3. Trust MIME header only.
4. Publicly serve uploads before validation.
5. Uploaded SVG served raw with script.
6. Quarantined file still available by guessed URL.
7. EXIF GPS published unknowingly.
8. `ffmpeg` command built via shell concatenation.
9. Unlimited image dimensions.
10. Link checker can fetch `127.0.0.1`/`169.254.169.254`/private LAN.
11. Redirect target not revalidated.
12. Arbitrary provider iframe HTML stored/rendered.
13. `dangerouslySetInnerHTML` with Feedback/AI text.
14. Public generic open redirect.
15. Public generic QR `?url=` generator.
16. Full remote media downloaded during every page request.
17. Parser failure stack/path exposed to user.
18. Staged orphan files never cleaned.

## 36. E2E cases

- extension/MIME mismatch rejected;
- oversized upload rejected early;
- decompression bomb rejected;
- unsafe SVG rejected/sanitized;
- quarantined file inaccessible publicly;
- public derivative strips private metadata according to policy;
- path traversal filename cannot escape storage directory;
- private LAN/metadata URL blocked;
- redirect to private LAN blocked;
- open redirect attack rejected;
- raw iframe/script embed rejected;
- XSS payload in Feedback/Help/AI renders inert;
- prompt injection cannot trigger tools;
- QR generator rejects arbitrary unsafe target;
- storage path absent from response/error;
- orphan staged upload cleanup preserves committed references.

## 37. Acceptance criteria

`AC-CONT-001` All uploads are private until validated/committed.  
`AC-CONT-002` Storage paths are server-generated and independent of original filename.  
`AC-CONT-003` MIME/type is verified from content, not client metadata alone.  
`AC-CONT-004` Images/video/audio/documents have bounded parser resources.  
`AC-CONT-005` Uploaded SVG is rejected or processed by explicit safe pipeline.  
`AC-CONT-006` Public derivatives follow metadata/privacy rules.  
`AC-CONT-007` Server-side URL fetching blocks internal/private/metadata targets and redirects.  
`AC-CONT-008` Embed rendering is provider-allowlisted and template-controlled.  
`AC-CONT-009` Rich/user/AI text is escaped/sanitized against stored XSS.  
`AC-CONT-010` Prompt injection cannot elevate content into system authority.  
`AC-CONT-011` QR/PDF/link generators use validated canonical URL contracts.  
`AC-CONT-012` E2E tests cover upload, parser, SSRF, redirects, XSS, embeds and cleanup.

---

## 38. Финальная доктрина

> **Uploaded bytes, URLs and text are untrusted in different ways and therefore pass separate validation gates for storage, parsing, navigation, fetching, embedding and publication. Files remain private until validated; URLs never gain server-fetch authority merely because they are displayable; redirects and DNS destinations are rechecked; and all textual/provider/AI content is rendered through escaping/sanitization rather than trusted execution.**
