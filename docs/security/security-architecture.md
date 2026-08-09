# SECURITY ARCHITECTURE
## DOC-140 — нормативная архитектура безопасности проекта сайта актрисы

**Статус:** Canonical / Required  
**Область:** Public Site, Admin, Public Questionnaire Builder, token-scoped resources, media, PDF/QR, AI, providers, background jobs, PostgreSQL, filesystem, OAuth, notifications, analytics.

---

## 1. Назначение

Документ определяет системную модель безопасности проекта и является верхнеуровневым источником требований для всех security-документов DOC-141…DOC-146.

Ключевая доктрина:

> **Public, Admin, token-scoped, provider-facing и background-worker surfaces являются разными trust zones. Данные и полномочия должны сокращаться до минимально необходимого объёма до сериализации, передачи внешнему сервису или выполнения side effect. Ни UI, ни AI, ни cache, ни provider callback не являются источником authorization truth.**

---

## 2. Security objectives

Система обязана обеспечивать:

1. конфиденциальность private/admin данных;
2. целостность professional Source;
3. строгую авторизацию write-actions;
4. безопасную публикацию только явно разрешённых данных;
5. отсутствие hidden data в public payload;
6. защиту токенов, credentials и provider secrets;
7. защиту загрузок и файлового контура;
8. защиту от SSRF и unsafe outbound fetch;
9. CSRF/XSS/CSP и browser-side hardening;
10. rate limiting/anti-abuse публичных write surfaces;
11. auditability high-impact operations;
12. возможность безопасного revoke;
13. recovery без replay опасных side effects;
14. defense-in-depth при компрометации одного слоя.

---

## 3. Trust zones

Canonical zones:

```text
PUBLIC_BROWSER
ADMIN_BROWSER
PUBLIC_BUILDER_SESSION
TOKEN_SCOPED_RECIPIENT
APPLICATION_SERVER
BACKGROUND_WORKER
POSTGRESQL
PRIVATE_MEDIA_STORAGE
DERIVED_PUBLIC_STORAGE
EXTERNAL_AI_PROVIDER
EXTERNAL_NOTIFICATION_PROVIDER
EXTERNAL_SOCIAL_PROVIDER
OAUTH_PROVIDER
PROVIDER_WEBHOOK
OBSERVABILITY
BACKUP_STORAGE
```

Каждый переход между зонами является trust boundary.

### SEC-INV-001
Доступ внутри одной зоны не подразумевает доступ к другой зоне.

### SEC-INV-002
Client-side possession of an identifier does not grant authority over that entity.

---

## 4. Surface classes

### 4.1 Public

Доступ без Admin authentication. Возвращает исключительно public projection.

### 4.2 Admin

Требует authenticated Admin session и server-side authorization.

### 4.3 Builder

Публичная self-service поверхность с отдельной Eligibility Projection. Builder никогда не получает Admin Source payload.

### 4.4 Token-scoped

Opaque token предоставляет строго ограничённый доступ к конкретному resource/scope. Token не является Admin credential.

### 4.5 Provider-facing webhook

Публично достижимый endpoint, но доверие возникает только после signature/state/provider validation.

### 4.6 Worker

Worker действует от имени server-side policy, а не пользователя. Job payload не должен содержать избыточные secrets или unrestricted commands.

---

## 5. Data classification

Минимальные классы:

```text
PUBLIC
PROFESSIONAL_INTERNAL
ADMIN_PRIVATE
PII
SECRET
TOKEN_SCOPED
SECURITY_SENSITIVE
```

Примеры:

- публичная биография → PUBLIC;
- непубликованный Casting → ADMIN_PRIVATE;
- email/phone → PII;
- OAuth/API token → SECRET;
- casting-safe share token → TOKEN_SCOPED;
- audit/security diagnostics → SECURITY_SENSITIVE.

### SEC-INV-003
Класс доступа определяется owning domain/policy, а не местом отображения в UI.

---

## 6. Data minimization before serialization

Правило:

```text
Source
  ↓ authorization + projection
Allowed DTO
  ↓ serialization
Client
```

Запрещено:

```text
Full Source → browser → hide with CSS/JS
```

### SEC-INV-004
Hidden/private fields must be removed server-side before serialization.

Это правило обязательно для:

- Contacts;
- Skills/Languages visibility;
- Achievements;
- Questionnaire Builder;
- Media;
- Feedback;
- Help Tickets;
- Analytics;
- notification endpoints;
- token-scoped resources.

---

## 7. Authentication boundary

Admin authentication рассматривается отдельно в DOC-141.

Архитектурные требования:

- server-validated session;
- secure cookie/session mechanism;
- session rotation after authentication/privilege change;
- logout/revocation support;
- no Admin token in localStorage baseline;
- MFA-ready architecture;
- credential brute-force protection;
- secure recovery flow.

### SEC-INV-005
Authenticated ≠ authorized for every command.

---

## 8. Authorization boundary

Authorization определяется сервером на каждом privileged command/query.

Проверяются как минимум:

```text
principal
permission
profile scope
entity ownership
lifecycle state
expected version
action-specific invariant
```

### SEC-INV-006
UI availability of a button is not authorization evidence.

### SEC-INV-007
UUID secrecy is never an access-control mechanism.

---

## 9. Session and cookie security

Для browser session cookies baseline:

- `HttpOnly`;
- `Secure` в production;
- appropriate `SameSite`;
- narrow path/domain;
- bounded lifetime;
- rotation;
- server-side revocation capability.

Sensitive state-changing requests должны быть защищены от CSRF согласно выбранной session architecture.

---

## 10. CSRF

Если authentication использует cookies, state-changing endpoints требуют CSRF protection through one or combination of:

- SameSite policy;
- origin validation;
- CSRF token;
- framework-supported secure action semantics.

### SEC-INV-008
GET/HEAD endpoints never perform business mutations.

### SEC-INV-009
Provider callbacks are excluded from browser-CSRF flow only because they have independent provider authentication/signature validation.

---

## 11. XSS and output encoding

Untrusted content includes:

- Feedback text;
- Casting source;
- Help Ticket body;
- user captions/descriptions;
- imported HTML;
- external provider text;
- AI output.

Requirements:

- React/default contextual escaping;
- sanitize explicitly supported rich text;
- no arbitrary `dangerouslySetInnerHTML`;
- no raw provider embed code;
- allowlisted embed adapters only;
- safe URL scheme validation.

### SEC-INV-010
AI-generated text is untrusted output until normal application validation/escaping is applied.

---

## 12. Content Security Policy

Production should define CSP appropriate to actual application needs.

Baseline intent:

```text
default-src 'self'
object-src 'none'
base-uri 'self'
frame-ancestors policy
script-src narrowly controlled
style-src controlled
img-src allowlisted/self/data only where justified
media-src controlled
connect-src explicit providers
```

Provider embeds must expand CSP through explicit reviewed configuration, not wildcard domains.

### SEC-INV-011
CSP is defense-in-depth, not replacement for escaping/sanitization.

---

## 13. Security headers

Production response policy should include appropriate:

- HSTS after HTTPS validation;
- `X-Content-Type-Options: nosniff`;
- Referrer-Policy;
- Permissions-Policy;
- frame protection via CSP;
- secure cache headers for sensitive content.

Admin/private responses must never be cached publicly.

---

## 14. URL safety

All application-accepted URLs must be classified.

Unsafe schemes rejected:

```text
javascript:
data:
file:
vbscript:
ftp:
```

unless a narrowly defined internal use explicitly permits a scheme.

Credentials embedded in URL are rejected for public professional links.

Admin/internal/local URLs cannot become public Questionnaire/QR targets.

---

## 15. SSRF

Any server-side URL fetch/check is deny-by-default.

Protected targets include:

- localhost;
- loopback;
- RFC1918/private ranges;
- link-local;
- cloud metadata endpoints;
- internal service DNS;
- Unix/file paths;
- non-http(s) protocols.

Redirects must be revalidated at every hop.

DNS rebinding protections should resolve/check actual destination addresses at connection time where feasible.

### SEC-INV-012
A URL that is safe to display is not automatically safe for server-side fetching.

---

## 16. Upload security

Uploads are untrusted.

Required controls:

1. maximum size;
2. type allowlist;
3. magic-byte/content validation;
4. extension not trusted;
5. safe generated storage name;
6. no user-supplied filesystem path;
7. malware/scanning policy where applicable;
8. quarantine lifecycle;
9. authorization before download;
10. safe `Content-Disposition` and `Content-Type`;
11. no executable serving from upload domain/path;
12. image/document parser isolation where practical.

SVG is special-risk content and must be rejected or processed through explicit safe policy.

---

## 17. Media storage boundary

Canonical:

```text
Immutable Original
    ↓ private storage
Media Processing
    ↓
Derived Artifact
    ↓ access-class-specific resolver
Public / token-scoped delivery
```

Filesystem path is never a public identifier.

### SEC-INV-013
Storage locator and public URL are separate concepts.

---

## 18. PDF and QR security

PDF/QR must never contain:

- Admin URL;
- raw filesystem path;
- raw private storage locator;
- ephemeral internal signed URL unsuitable for artifact lifetime;
- secret/provider token unrelated to intended scoped access.

Tokenized casting-safe links must match intended scope/lifetime.

Historical snapshot semantics do not prevent emergency access revocation.

---

## 19. Public Builder security

Builder receives only `BuilderEligibilityProjection`.

Required:

- no mandatory registration baseline;
- opaque temporary session ID;
- TTL;
- rate limiting;
- anti-replay/idempotency;
- server-side Generate revalidation;
- no direct Source mutation;
- hidden data absent before serialization;
- generated resource uses scoped access if non-public.

### SEC-INV-014
Builder selection cannot elevate access.

---

## 20. Token-scoped access

Scoped token must bind to:

```text
resource/scope
purpose
expiry where applicable
revocation state
```

Tokens must be high-entropy and unguessable.

Store token hashes where plaintext recovery is unnecessary.

Never log raw token.

### SEC-INV-015
Token possession grants only the declared scope, never Admin identity.

---

## 21. Secrets management

Secrets include:

- DB password;
- AI provider key;
- notification provider key;
- OAuth access/refresh tokens;
- webhook signing secret;
- encryption keys.

Rules:

- no repository commit;
- no client bundle;
- no browser rendering;
- no analytics;
- no generic logs;
- environment/secret store separation;
- rotation supported;
- least privilege.

---

## 22. AI security

AI is treated as external/untrusted computational provider.

Required:

- minimized source snapshot;
- no secrets;
- prompt-injection isolation;
- no unrestricted DB/shell/network tools;
- schema validation;
- Human apply for professional/business mutations;
- no automatic publication/send;
- provider failure non-critical to core CRUD.

### SEC-INV-016
Pasted content never gains system/tool authority through prompt injection.

---

## 23. External providers

Notification/Social/AI providers receive minimal data necessary for requested operation.

Each integration must have:

- narrow adapter;
- timeout;
- bounded retries;
- normalized errors;
- credential isolation;
- audit/attempt record where side effect matters;
- unknown-outcome reconciliation where required.

---

## 24. Webhooks

Provider webhook endpoints require:

- signature/state validation where supported;
- timestamp/replay checks;
- bounded body;
- schema validation;
- event-id idempotency;
- state-machine validation;
- no business authority beyond reconciliation contract.

### SEC-INV-017
Webhook payload is provider input, not unrestricted domain command.

---

## 25. Rate limiting and abuse protection

At minimum apply to:

- login/recovery;
- Feedback submission;
- Builder generation;
- analytics ingestion;
- token validation failures;
- public search where abuse risk exists;
- provider callback endpoints where appropriate.

Rate limits should be scope-aware and avoid exposing hidden record existence.

---

## 26. Database security

Requirements:

- application DB role uses least necessary permissions;
- no public DB network exposure;
- parameterized queries/ORM bindings;
- schema constraints reinforce security invariants;
- secret data minimized/encrypted where appropriate;
- backups protected;
- migrations reviewed.

### SEC-INV-018
Application authorization must not rely solely on database row IDs.

---

## 27. Optimistic concurrency and integrity

Security-sensitive/high-impact writes use expected version or equivalent concurrency control where relevant.

Prevents stale Admin/AI/VOP actions from overwriting newer Human state.

---

## 28. Idempotency

Required for side-effecting operations with retry risk:

- Feedback submission;
- Builder Generate;
- publication;
- notification sends;
- Social publishing;
- conversions;
- file/job creation where duplicates matter.

Same idempotency key + different semantic payload must conflict.

---

## 29. Audit

Security-relevant Audit includes:

- login/security events;
- permission/config changes;
- visibility changes;
- publication;
- token/link revoke;
- OAuth connect/disconnect;
- high-impact status changes;
- destructive operations;
- privileged support repairs.

Audit is append-oriented and access-controlled.

Audit must not store raw passwords/secrets.

---

## 30. Logging and observability

Logs are metadata-first.

Prefer:

```text
request_id
correlation_id
principal_id
entity_id
action
status
error_code
latency
```

Avoid:

- full Feedback body;
- full Casting source;
- raw token;
- cookies;
- authorization headers;
- OAuth credentials.

---

## 31. Cache security

Cache keying and storage must include relevant access class/generation.

Forbidden:

```text
public cache entry containing Admin/private projection
```

Revocation-sensitive resources require strong invalidation or access check on every resolution.

### SEC-INV-019
Cache can accelerate authorization-safe projections but cannot replace authorization.

---

## 32. Search security

Public/Admin/Builder search indexes are distinct or strictly projection-separated.

Current hydration revalidates visibility before returning results.

Hidden data must not be inferable from snippets/counts/errors.

---

## 33. Backup security

Backups contain high-value private data and require:

- restricted filesystem/account access;
- encryption where appropriate;
- retention policy;
- restore testing;
- no accidental public serving;
- credential/key handling.

Restore must not replay outbound side effects.

---

## 34. Security incident containment

System should support narrow predefined containment actions such as:

- revoke scoped token;
- disable compromised SocialAccount credential;
- invalidate affected public cache;
- disable an affected public projection/alias where architecture supports it;
- force Admin session revocation.

No generic autonomous `run shell / UPDATE anything` incident tool.

---

## 35. Defense in depth

Example private-media protection:

```text
DB visibility rule
+ application authorization
+ private storage
+ signed/resolver delivery
+ no public cache
+ audit
```

Compromise of one layer should not automatically expose all data.

---

## 36. Security anti-patterns

Forbidden:

1. Admin token in localStorage.
2. Hidden data sent to browser then hidden in UI.
3. Public endpoint accepts arbitrary filesystem path.
4. Anonymous generic QR generator with arbitrary URL.
5. Server-side URL checker can access private network.
6. OAuth tokens stored in Contacts.
7. Raw upload file served as executable content.
8. Provider webhook accepted unsigned where signing is available.
9. GET request performs mutation.
10. CSP wildcard added for convenience.
11. `dangerouslySetInnerHTML` on Feedback/AI/provider text.
12. Raw DB backup served through web root.
13. Public cache reused for token-scoped artifact.
14. Analytics stores raw access token/query secret.
15. UUID treated as secret.
16. AI receives shell/SQL generic tools.
17. VOP can grant itself new business authority.
18. Restore blindly replays Social/Notification sends.
19. Provider timeout treated as definitely failed and blindly retried.
20. Security logs contain credentials.

---

## 37. Security verification matrix

| Boundary | Required controls |
|---|---|
| Public → App | validation, rate limit, allowlisted DTO |
| Admin → App | authentication, authorization, CSRF, concurrency |
| Builder → App | eligibility projection, TTL, anti-abuse, revalidation |
| App → DB | parameterization, least privilege, constraints |
| App → Storage | generated paths, private ACL, content validation |
| App → AI | minimized snapshot, no secrets, schema validation |
| App → Provider | narrow adapter, secrets, timeout, idempotency |
| Provider → Webhook | signature, replay/dedupe, schema validation |
| Worker → Domain | typed job, idempotency, current-state recheck |
| Backup → Restore | restricted access, integrity, non-replay |

---

## 38. Required E2E security cases

At minimum:

- anonymous cannot access `/admin` data;
- authenticated user without permission cannot mutate restricted entity;
- hidden Contact/Skill/Achievement absent from public JSON;
- Builder cannot request hidden item by forged ID;
- revoked token denied;
- raw private media path rejected;
- private-network URL blocked in link checker;
- malicious upload rejected/quarantined;
- XSS payload rendered inert;
- CSRF state mutation rejected;
- stale version cannot overwrite newer state;
- duplicate idempotency request does not duplicate side effect;
- provider webhook invalid signature rejected;
- duplicate webhook idempotent;
- OAuth callback state mismatch rejected;
- token absent from logs/analytics/client payload;
- public cache never serves Admin projection;
- restore does not resend prior notifications/social posts.

---

## 39. Quality gate

Security architecture is accepted only when:

- trust zones documented;
- data classification implemented;
- authentication and authorization server-side;
- public projections data-minimized;
- upload and SSRF controls present;
- OAuth/provider secrets isolated;
- CSRF/XSS/CSP/browser controls configured;
- rate limits exist for public abuse surfaces;
- cache/search respect access classes;
- token scopes/revocation implemented;
- audit/observability secret-safe;
- backup/restore security tested;
- security E2E tests automated.

---

## 40. Acceptance criteria

`AC-SEC-140-001` Public, Admin, Builder, token-scoped, worker and provider surfaces are separate trust zones.  
`AC-SEC-140-002` Hidden/private data is removed server-side before serialization.  
`AC-SEC-140-003` Authentication never substitutes action-level authorization.  
`AC-SEC-140-004` UUID possession grants no access.  
`AC-SEC-140-005` State-changing cookie-authenticated actions are CSRF-protected.  
`AC-SEC-140-006` Untrusted rich text/AI/provider content cannot execute script.  
`AC-SEC-140-007` Server-side URL fetching blocks private/internal targets and revalidates redirects.  
`AC-SEC-140-008` Uploads are content-validated, bounded and private-by-default.  
`AC-SEC-140-009` Filesystem/storage locators never become public resource identifiers.  
`AC-SEC-140-010` QR/PDF never expose Admin/internal/raw-storage URLs.  
`AC-SEC-140-011` OAuth/provider/API tokens never reach browser, analytics or standard logs.  
`AC-SEC-140-012` AI input is purpose-minimized and cannot gain unrestricted tools through prompt injection.  
`AC-SEC-140-013` Provider side effects use bounded retries/idempotency/reconciliation.  
`AC-SEC-140-014` Provider webhooks are authenticated/deduplicated/state-validated.  
`AC-SEC-140-015` Public abuse surfaces are rate-limited.  
`AC-SEC-140-016` Public/Admin/Builder cache and search projections cannot leak across access classes.  
`AC-SEC-140-017` Audit/logging is append-oriented/metadata-first and secret-safe.  
`AC-SEC-140-018` Restore never blindly replays external side effects.  
`AC-SEC-140-019` Narrow revoke/containment actions exist for compromised access.  
`AC-SEC-140-020` Automated security E2E coverage proves all critical trust boundaries.

---

## 41. Финальная доктрина

> **Security является системным свойством всей архитектуры, а не отдельным middleware. Every surface receives only the minimum data and authority it needs; every privileged action is re-authorized server-side; every external side effect is bounded, attributable and recoverable; every public/tokenized artifact is derived from explicit access policy; and no AI, cache, browser identifier, provider callback or worker job can elevate itself into a new source of authority.**
