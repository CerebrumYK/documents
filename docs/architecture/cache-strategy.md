# CACHE ARCHITECTURE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная архитектура кэширования, invalidation, изоляции доступа и защиты от устаревших данных

**Целевой файл:** `docs/architecture/cache.md`  
**Документ:** DOC-077  
**Статус:** ✅ Completed  
**Тип:** Architecture / Cache / Invalidation / Performance / Privacy

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/business-rules.md`
- `docs/product/product-principles.md`
- `docs/ux/ui-states.md`
- `docs/ux/content-hierarchy.md`
- `docs/ux/responsive-behaviour.md`
- `docs/domain/domain-model.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/components.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`
- `docs/architecture/media.md`
- `docs/architecture/pdf.md`
- `docs/architecture/background-jobs.md`

---

# 1. Назначение документа

Настоящий документ определяет целевую архитектуру cache subsystem.

Он фиксирует:

1. что может кэшироваться;
2. что запрещено считать cache authority;
3. классы кэшей;
4. cache keys;
5. current vs immutable content;
6. public/private/token-scoped isolation;
7. locale-aware caching;
8. revision-aware caching;
9. Builder session caching;
10. Admin caching;
11. invalidation events;
12. visibility revocation;
13. stale data policy;
14. TTL;
15. stale-while-revalidate;
16. cache stampede protection;
17. request-local caching;
18. media/browser/CDN caching;
19. PDF/QR cache;
20. search interaction;
21. worker/background invalidation;
22. deployment behaviour;
23. cache loss/rebuild;
24. privacy/security;
25. observability;
26. testing.

---

# 2. Главный принцип

> **Cache ускоряет получение уже авторитетных данных, но никогда не определяет их смысл, доступность, видимость или жизненный цикл.**

Canonical:

```text
SOURCE OF TRUTH
      ↓
AUTHORIZED PROJECTION
      ↓
CACHE
      ↓
CONSUMER
```

Never:

```text
CACHE
  ↓
BUSINESS TRUTH
```

---

# 3. Cache IDs

Используются:

```text
CACHE-*
CACHE-KEY-*
CACHE-INV-*
CACHE-SEC-*
CACHE-E2E-*
```

---

# 4. CACHE-INV-001 — Cache Is Derived

Любой cache entry MUST быть:

```text
rebuildable
replaceable
non-authoritative
```

---

# 5. CACHE-INV-002 — Cache Loss Must Be Safe

Полная потеря cache не должна означать:

```text
loss of Profile
loss of Questionnaire Revision
loss of Feedback
loss of Opportunity
loss of Media original
```

---

# 6. CACHE-INV-003 — Privacy Before Cache

В cache может попадать только уже разрешённая для данного access context Projection.

Нельзя:

```text
raw Contact row
→ cache
→ later decide who can see it
```

для public cache.

---

# 7. CACHE-INV-004 — Access Scope Is Part of Identity

Если два consumer имеют разные visibility scopes, они MUST NOT использовать один и тот же cache entry.

---

# 8. CACHE-INV-005 — Security Revocation Beats TTL

Изменение:

```text
public → private
builder allowed → denied
token valid → revoked
```

не должно ждать обычного долгого TTL.

---

# 9. Cache Classes

Canonical classes:

```text
REQUEST_LOCAL
CURRENT_PUBLIC
IMMUTABLE_REVISION
BUILDER
TOKEN_SCOPED
ADMIN_READ
MEDIA
DOCUMENT
QR
SEARCH_DERIVED
EXTERNAL_METADATA
```

---

# 10. REQUEST_LOCAL Cache

Живёт только внутри одного request/execution context.

Используется для:

```text
deduplicating repeated DB reads
projection assembly
permission lookup reuse
```

---

# 11. Request-Local Authority

Request-local cache допустим после корректного context resolution.

Он исчезает после запроса.

---

# 12. CURRENT_PUBLIC Cache

Хранит текущие public-safe projections.

Examples:

```text
Public Home
Portfolio listing
Project listing
Current Questionnaire Hub
Public Contacts
```

---

# 13. CURRENT_PUBLIC Characteristics

```text
mutable source
event-driven invalidation
locale-aware
public access only
```

---

# 14. IMMUTABLE_REVISION Cache

Хранит данные/артефакты, чья source identity immutable.

Examples:

```text
QuestionnaireRevision projection
Historical Questionnaire HTML
Published revision-specific PDF metadata
ThemeRevision preview
```

---

# 15. Immutable Cache Advantage

Revision-specific content может иметь:

```text
long TTL
immutable cache headers
content-addressed identity
```

если privacy classification позволяет.

---

# 16. Current Alias vs Revision Cache

Различать:

```text
/questionnaire/main
```

и:

```text
/questionnaire/revisions/{revision}
```

Первый зависит от current pointer.

Второй — от immutable Revision.

---

# 17. Current Alias

Cache entry MUST инвалидироваться при смене current published Revision.

---

# 18. Historical Revision

Не требует invalidation из-за публикации новой Revision.

---

# 19. BUILDER Cache

Builder cache является session/context-sensitive.

---

# 20. Builder Cache Key

MUST учитывать как минимум:

```text
profile
session
locale
builder policy/config version
```

а где необходимо:

```text
session version
eligibility version
```

---

# 21. Builder Cache Security

Builder cache MUST NOT быть shared generic public cache.

---

# 22. Builder Eligibility Cache

Если eligibility кэшируется:

permission revocation MUST invalidate/revalidate affected entries.

---

# 23. Builder Generate

Даже если Preview был получен из cache:

```text
Generate
```

MUST выполнить server-authoritative revalidation.

---

# 24. TOKEN_SCOPED Cache

Для:

```text
private casting questionnaire
unlisted professional package
token-scoped document
```

---

# 25. Token Must Be Part of Security Context

Но raw secret token SHOULD NOT обязательно использоваться как plaintext cache key/loggable string.

Use:

```text
token hash
access-scope identifier
resolved grant ID
```

where practical.

---

# 26. CACHE-SEC-001 — No Token/Public Collision

Следующие ответы MUST иметь разные cache namespaces:

```text
public questionnaire
private casting questionnaire
admin preview
```

даже если underlying content похож.

---

# 27. Revoked Token

После revocation:

```text
cached payload alone
```

не может продолжить доступ.

Protected request MUST perform current token/access validation before serving protected content where required.

---

# 28. ADMIN_READ Cache

Admin operational data SHOULD кэшироваться осторожно.

---

# 29. Admin Freshness Priority

Для:

```text
Feedback
Casting
Opportunity
Notifications
VOP
```

freshness обычно важнее aggressive caching.

---

# 30. Admin Cache Permitted

Examples:

```text
stable reference catalogs
Help content
expensive read-only aggregates
short-lived Dashboard projection
```

---

# 31. Admin Cache Authorization

Cache identity MUST учитывать relevant permission/security context where results differ.

---

# 32. MEDIA Cache

Media cache может существовать на нескольких уровнях:

```text
application metadata cache
reverse proxy cache
browser cache
CDN/object cache
```

---

# 33. Immutable Media Derivative

Если URL однозначно идентифицирует immutable derivative/content hash:

можно использовать long-lived immutable cache.

---

# 34. Current Media Alias

Если URL может указывать на изменяющийся current image:

требуется:

```text
versioned URL
or
explicit invalidation
```

---

# 35. Recommended Media Strategy

Prefer:

```text
immutable/versioned derivative URLs
```

и обновление reference при смене asset.

---

# 36. Hero Image

Primary change SHOULD приводить к новому media identity/reference, а не overwrite binary under same URL.

---

# 37. Browser Cache

Public immutable media SHOULD использовать эффективные browser cache headers.

---

# 38. Private Media

Private/token-scoped media MUST NOT использовать public shared-cache semantics без строгого scope control.

---

# 39. DOCUMENT Cache

Prepared PDF/HTML documents divide into:

```text
current alias
revision-specific artifact
private/token-scoped artifact
```

---

# 40. Revision-Specific Public PDF

Excellent immutable-cache candidate.

---

# 41. Current PDF Alias

Must resolve current Revision and invalidate when current pointer changes.

---

# 42. Private PDF

Must use:

```text
private cache policy
no-store
or
strict token/access-scoped cache
```

depending implementation.

Never globally public.

---

# 43. Builder PDF

Access/retention depends on Builder session/artifact policy.

It MUST NOT be placed in generic public cache solely because it is a PDF.

---

# 44. QR Cache

QR derived artifact key:

```text
canonical URL
+
QR settings version
```

---

# 45. QR Cache Validity

READY entry valid only if QR decode validation succeeded.

---

# 46. QR URL Change

New URL:

```text
new cache identity
```

or invalidate current dependency.

Historical old QR remains valid for old Revision.

---

# 47. QR Public/Private Classification

QR image itself may expose its encoded URL.

Therefore access classification of QR artifact follows target/document context.

---

# 48. SEARCH_DERIVED Cache

Search query result cache MAY be used.

But Search Index itself is already derived.

---

# 49. Search Cache Freshness

Security-sensitive result still requires final eligibility protection.

---

# 50. External Metadata Cache

Examples:

```text
external video metadata
link health result
provider embed info
```

may be cached with bounded TTL.

---

# 51. External Reachability Is Not Permanent Truth

A link checked as healthy 10 minutes ago can fail now.

Cache semantics should reflect observation timestamp.

---

# 52. Cache Key Doctrine

A correct cache key must distinguish every dimension capable of changing returned meaning or authorization.

---

# 53. Generic Cache Key Formula

Conceptually:

```text
namespace
+
resource identity
+
projection/version
+
locale
+
access scope
+
representation
```

---

# 54. Public Profile Key

Example:

```text
public-profile:{profile}:{locale}:{projection-version}
```

---

# 55. Public Project Key

```text
public-project:{project}:{locale}:{source-version}
```

or equivalent event-invalidated key.

---

# 56. Questionnaire Revision Key

```text
questionnaire-revision:{revision}:{locale}:{projection-version}
```

---

# 57. Current Questionnaire Alias Key

```text
questionnaire-current:{definition}:{locale}
```

invalidated on pointer change.

---

# 58. Builder Key

```text
builder:{session}:{session-version}:{locale}:{builder-policy-version}
```

---

# 59. Protected Package Key

Conceptually:

```text
protected-document:{grant-id}:{artifact-id}
```

not generic public artifact key.

---

# 60. Theme Key

Current public presentation cache may include:

```text
theme_revision_id
```

if rendered HTML depends materially on Theme.

---

# 61. Theme Activation

Theme change may invalidate presentation cache while content source cache remains reusable if architecture separates them.

---

# 62. Projection Cache vs Render Cache

Useful distinction:

```text
DATA PROJECTION CACHE
RENDERED RESPONSE CACHE
```

---

# 63. Data Projection Cache

Stores structured safe DTO/read model.

---

# 64. Rendered Response Cache

Stores:

```text
HTML
RSC response
serialized page output
```

depending Next.js architecture.

---

# 65. Rendering Cache Risk

Rendered cache MUST inherit all:

```text
locale
access
theme
current revision
visibility
```

dimensions that affect output.

---

# 66. Prefer Layered Cache

Where useful:

```text
Source
↓
Projection Cache
↓
Rendered Response Cache
```

but avoid unnecessary complexity at initial scale.

---

# 67. Initial Simplicity

Current project scale does not require dedicated distributed cache service by default.

---

# 68. CACHE-INV-006 — No Redis-by-Default Requirement

Redis MUST NOT be introduced solely because cache exists.

Possible initial mechanisms:

```text
Next.js/server cache
process/request cache
HTTP/browser cache
PostgreSQL-derived read optimization
filesystem/object immutable artifacts
```

---

# 69. Distributed Cache Trigger

Dedicated shared cache becomes justified if:

```text
multiple web instances
cache coherence measurable problem
high repeated read cost
latency requirement
```

demands it.

---

# 70. Process Memory Cache

Safe only for:

```text
optional acceleration
short-lived data
data with server revalidation
```

---

# 71. Process Cache Limitation

Multiple processes do not share it.

Therefore it must not be used to enforce:

```text
authorization revocation
primary uniqueness
business state
idempotency
```

---

# 72. Invalidation Architecture

Preferred strategy:

> **event-driven invalidation + bounded TTL fallback**

---

# 73. Why Not TTL Only

TTL-only creates privacy/business staleness.

Example:

```text
Contact public=true cached 1 hour
Admin changes false
```

Waiting one hour is unacceptable.

---

# 74. Domain Event Invalidation

Examples:

```text
ProfileChanged
PortfolioChanged
PrimaryPortfolioChanged
ContactVisibilityChanged
ProfessionalLinkChanged
EmotionalGridPublished
ProjectPublished
ProjectArchived
QuestionnairePublished
QuestionnairePrimaryChanged
ThemeActivated
```

---

# 75. Invalidation Consumer

Canonical:

```text
Domain Event
   ↓
Cache Invalidation Coordinator
   ↓
targeted cache keys/tags
```

---

# 76. Transaction Ordering

Invalidation event is produced from committed state.

---

# 77. Race Avoidance

Do not invalidate before transaction commits and then leave old state restored after rollback.

---

# 78. Outbox Integration

Important invalidation MAY use transactional outbox so a crash cannot permanently miss invalidation.

---

# 79. Visibility Invalidation Priority

Critical:

```text
ContactVisibilityChanged
BuilderEligibilityChanged
ProtectedTokenRevoked
PublicEntityArchived
```

---

# 80. Security Revalidation

For high-risk access:

cache invalidation alone SHOULD NOT be only protection.

Example private token request:

```text
validate token current state
→ then use cached artifact
```

---

# 81. Invalidation Granularity

Target specific affected projections.

Example:

```text
ContactVisibilityChanged
```

may invalidate:

```text
Public Contact
Public Home if contact rendered
Builder eligibility
Questionnaire Draft readiness
```

---

# 82. Historical Cache Not Invalidated

Current Contact change does not invalidate:

```text
QuestionnaireRevision 4
```

except controlled privacy redaction/access policy.

---

# 83. ProfessionalLink URL Change

Invalidate:

```text
current public link projection
link health current cache
current dependent QR cache
draft previews
```

Do not mutate historical Revision cache.

---

# 84. Media Primary Change

Invalidate:

```text
Public Home
Casting Quick View
Portfolio current
Questionnaire readiness
```

Historical Questionnaire Revision cache unchanged.

---

# 85. Emotional Grid Primary Change

Invalidate current Emotional/Profile projections.

Historical Questionnaire revision unaffected.

---

# 86. Questionnaire Publish

Invalidate:

```text
Questionnaire Hub
current questionnaire alias
public home CTA availability if affected
download chooser
```

Revision-specific old caches stay immutable.

---

# 87. Primary Questionnaire Switch

Must invalidate all surfaces resolving “recommended/current primary”.

---

# 88. Theme Activation

Invalidate rendered public presentation affected by active Theme.

Content Projection MAY remain reusable if Theme-independent.

---

# 89. Cache Tags

Implementation MAY use tag-based invalidation:

```text
profile:{id}
contact:{id}
questionnaire:{id}
theme:current
```

---

# 90. Tag Rule

Tags are implementation mechanism, not semantic authority.

---

# 91. Versioned Keys

Alternative/complement:

```text
profile_projection_version
```

as part of cache identity.

---

# 92. Event + Version Strategy

Recommended where practical:

```text
event invalidation
+
version-aware key
```

to reduce stale collision risk.

---

# 93. TTL

TTL is fallback freshness limit, not primary privacy control.

---

# 94. TTL Categories

Conceptually:

```text
VERY_SHORT
SHORT
MEDIUM
LONG
IMMUTABLE
```

Exact seconds later/per implementation.

---

# 95. VERY_SHORT

For highly dynamic Admin/read operational data if cached at all.

---

# 96. SHORT

Current public projections where event invalidation exists but bounded fallback desired.

---

# 97. MEDIUM

Stable public catalogs/content.

---

# 98. LONG

Immutable media and Revision-specific assets.

---

# 99. IMMUTABLE

Content-addressed/revision-specific artifacts whose bytes/semantics cannot change.

---

# 100. No One Global TTL

Different cache classes need different policy.

---

# 101. Stale-While-Revalidate

May be used for non-sensitive public performance.

---

# 102. Safe SWR Examples

Potential:

```text
public Project list
Training list
non-sensitive Portfolio listing
```

for short bounded periods.

---

# 103. Unsafe SWR Examples

Do not serve stale after revocation for:

```text
private token
Contact visibility removal
Builder eligibility
protected media access
```

---

# 104. CACHE-SEC-002 — No SWR for Access Revocation

Security/visibility state must be current enough to enforce revocation.

---

# 105. Stale-if-error

May serve stale public non-sensitive content during temporary DB/backend failure where product chooses.

---

# 106. Stale-if-error Limit

Never serve stale data if current state may have become less public/private-sensitive.

---

# 107. Example

Serving yesterday's public Project during short DB outage may be acceptable.

Serving cached Contact after unknown visibility state is more sensitive and may require fail-closed policy.

---

# 108. Fail-Open vs Fail-Closed

Per projection define explicitly.

---

# 109. Public Identity

Could fail-open to recent public cache if safe policy permits.

---

# 110. Protected Resource

Must fail-closed if authorization cannot be verified.

---

# 111. Builder Generate

Fail-closed if eligibility cannot be verified.

---

# 112. Cache Stampede

Popular cache expiry may cause many simultaneous expensive rebuilds.

---

# 113. Stampede Protection

Possible strategies:

```text
single-flight
mutex/lease
stale-while-revalidate
background refresh
randomized TTL jitter
```

---

# 114. Single-Flight

Only one worker/request rebuilds key.

Others:

```text
wait
or
serve allowed stale
```

according to policy.

---

# 115. Distributed Single-Flight

Needed only if multiple web instances and rebuild cost warrants.

---

# 116. No Global Locking for Everything

Avoid making cache locks a system bottleneck.

---

# 117. Negative Caching

May cache:

```text
not found
```

for short duration to reduce repeated misses.

---

# 118. Negative Cache Security Risk

A newly published resource could remain unavailable until negative TTL ends.

Therefore short TTL/event invalidation required.

---

# 119. Authorization Denial Caching

Should be used cautiously.

Permission changes can make cached denial obsolete.

---

# 120. Never Cache Secret Material in Shared Public Cache

Examples:

```text
OAuth tokens
session cookies
provider credentials
private attachments
```

---

# 121. Cache Serialization

Structured cache data SHOULD use explicit DTO schema.

---

# 122. ORM Entity Caching

Avoid caching raw ORM entities directly for Public output.

---

# 123. Schema Version

Complex serialized cache entries SHOULD include:

```text
projection schema version
```

or use key versioning.

---

# 124. Deployment Compatibility

When Projection schema changes:

old cache must be:

```text
invalidated
version-isolated
or
backward compatible
```

---

# 125. Recommended Key Version

Example:

```text
public-home:v3:{profile}:{locale}
```

---

# 126. Deployment Cache Flush

Global cache flush MAY be acceptable for small system deployment.

But architecture should not require full flush for every ordinary content mutation.

---

# 127. Cache Warm-Up

Optional after deployment/publication.

---

# 128. Warm-Up Candidates

```text
Public Home
Primary Questionnaire
Hero media
Primary Portfolio
```

---

# 129. Warm-Up Is Optimization

System remains correct without it.

---

# 130. Publication Warm-Up

After Questionnaire publish:

system MAY prebuild:

```text
public revision projection
PDF artifact
current alias cache
```

---

# 131. Hero Warm-Up

After Primary photo switch, new derivative should ideally already be ready before public switch where readiness requires.

---

# 132. Cache Rebuild Jobs

Expensive rebuild MAY run as background job.

---

# 133. Current-Derived Semantic Class

Cache rebuild jobs are normally:

```text
CURRENT_STATE_DERIVED
```

and may skip superseded work.

---

# 134. Cache Job Idempotency

Same source/version rebuild produces same semantic result.

---

# 135. Cache Failure

If cache write fails:

source business mutation remains committed.

---

# 136. Cache Failure UI

Usually transparent fallback to Source.

If source fallback impossible/too expensive:

feature can be degraded.

---

# 137. CACHE-INV-007 — Cache Write Is Not Commit Criterion

Ordinary Source mutation MUST NOT fail simply because optional cache storage failed after authoritative DB commit.

---

# 138. Exception

If architecture requires cache as delivery store for generated immutable artifact, that store is actually artifact/storage authority, not merely cache and must be modeled accordingly.

---

# 139. Distinguish Cache vs Artifact Store

Critical.

```text
PDF stored binary
```

may be a persistent Artifact, not cache.

A copy of that PDF in CDN/browser is cache.

---

# 140. Distinguish Media Derivative vs Cache

A registered derivative may be persistent derived artifact.

Browser/CDN copy is cache.

---

# 141. Distinguish Search Index vs Cache

Search Index is derived read model, not ordinary cache, although rebuildable.

---

# 142. Request Memoization

Within one server request:

```text
load Profile once
```

even if multiple components need it.

---

# 143. Request Context Isolation

Never leak data across requests/users from unsafe mutable global objects.

---

# 144. Server Component Caching

Next.js framework caching features MAY be used only when their invalidation/access semantics are understood and tested.

---

# 145. Framework Cache Is Not Special Authority

Same rules apply.

---

# 146. Static Generation

Public stable pages MAY use static generation/revalidation where compatible with:

```text
visibility
localization
content updates
```

---

# 147. Admin Pages

Generally should not be statically cached with shared public semantics.

---

# 148. Public Builder Pages

Builder-specific session data SHOULD be dynamic/private.

---

# 149. CDN

Optional future component.

---

# 150. CDN Excellent Use Cases

```text
immutable public images
revision-specific public PDF
static JS/CSS
public thumbnails
```

---

# 151. CDN Sensitive Use Cases

Protected/private media requires signed/private CDN mechanisms and server authorization model.

---

# 152. CDN Purge

Current alias or revoked public resource may require:

```text
purge
versioned URL
origin authorization
```

---

# 153. Prefer Versioned Immutable URLs

Avoid frequent CDN purge where immutable URL strategy solves problem safely.

---

# 154. Browser Cache for HTML

Current HTML public content should use conservative policy aligned with server invalidation/revalidation.

---

# 155. Browser Back Button

Client may display previously rendered page from browser history.

Security-sensitive protected pages SHOULD use appropriate cache headers to avoid unintended persistence.

---

# 156. Admin Browser Cache

Sensitive Admin pages SHOULD use restrictive cache semantics as defined by security architecture.

---

# 157. Private Attachment Response

Generally:

```text
Cache-Control: private/no-store
```

or equivalent strict policy.

Exact headers later.

---

# 158. Token-Scoped PDF

Likely private cache semantics unless explicit long-lived shared package design says otherwise.

---

# 159. Public Questionnaire Revision PDF

Can be:

```text
public, immutable
```

if route itself contains immutable public identity.

---

# 160. Cache and Localization

Locale MUST be in cache identity wherever output differs.

---

# 161. CACHE-AP-001

**Russian HTML served from English cache key**

Prohibited.

---

# 162. Cache and Theme

If rendered result changes by active Theme:

Theme Revision participates in render cache identity/invalidation.

---

# 163. Data Projection Cache May Be Theme-Neutral

Useful separation:

```text
PublicProfileDTO
```

can remain same across Themes.

Rendered HTML cache must change.

---

# 164. Cache and Accessibility

User-specific accessibility settings SHOULD not be cached globally into page output unless part of cache identity.

Prefer CSS/system preference handling where possible.

---

# 165. Reduced Motion

Should be handled presentation-side rather than creating duplicate data projection caches.

---

# 166. Cache and Authentication

Authenticated response caches must not accidentally cross users.

---

# 167. Session-Specific Cache

Key must include safe identity/access scope.

---

# 168. Admin Shared Read Cache

Only for data genuinely identical across authorized Admin users.

---

# 169. Permission-Specific Output

Requires permission scope identity or avoid caching.

---

# 170. Cache and CSRF/Auth Tokens

Never cache user-specific security tokens in shared rendered HTML cache.

---

# 171. Cache and Public Form

Feedback form page may be cached.

Submission action is never cached.

---

# 172. Cache and Idempotency

Idempotency store is NOT cache.

It is durable transactional state.

---

# 173. Cache and Rate Limit

Rate-limit counters may use cache-like technology but are security operational state and require separate semantics.

---

# 174. Cache and Session

Public Builder session storage is NOT cache.

It is TEMPORARY authoritative configuration.

---

# 175. CACHE-INV-008 — Temporary Does Not Mean Cache

If loss changes user/business state, it is not merely cache.

---

# 176. Cache and AI

AI generated outputs are persistent AIDraft/Analysis/Proposal.

Not cache.

---

# 177. AI Provider Response Memoization

May be used only where exact deterministic/request reuse is intentionally safe.

Do not reuse one AI answer across semantically different contexts solely to save cost.

---

# 178. AI Context Cache

Sensitive and task-specific.

Prefer request-local or carefully scoped cache.

---

# 179. Cache and VOP

VOPObservation is operational data, not cache.

Computed detector intermediate results MAY be cached.

---

# 180. Cache and Analytics

Analytics aggregate may be cached for dashboard presentation.

Raw authoritative business entities still determine business states.

---

# 181. Cache and Link Health

Link health result is timestamped derived observation.

Cache should retain:

```text
checked_at
status
```

---

# 182. Link Health Display

Admin must know freshness.

Do not show old cached `HEALTHY` as timeless truth.

---

# 183. Cache and QR

QR cache validity requires exact URL/settings.

---

# 184. QR Change

If styling/settings version changes:

new cache key.

---

# 185. Historical QR

Old Revision can continue using old cached/stored QR.

---

# 186. Cache and PDF Render Models

Document Model for immutable Revision MAY be cached by:

```text
revision
locale
document-model-version
```

---

# 187. PDF Artifact Is Separate

Cached Document Model can be lost and rebuilt.

Stored PDF Artifact may be retained history.

---

# 188. Cache and Media Metadata

Technical metadata may be cached.

Authoritative persistent extracted metadata remains DB.

---

# 189. Media Binary Cache

Browser/CDN copy can disappear without affecting MediaAsset.

---

# 190. Cache Poisoning

Inputs contributing to key/response must be validated.

---

# 191. Host Header

Canonical URL generation MUST NOT trust arbitrary Host header in a way that poisons cached public canonical links/QR.

---

# 192. Canonical Base URL

Use trusted application configuration/domain resolver.

---

# 193. Cache Key Injection

Do not concatenate untrusted arbitrary values into filesystem/Redis namespaces without safe encoding.

---

# 194. Cache Deserialization

Treat cached serialized data as internal but validate schema/version where corruption/incompatible deployments are possible.

---

# 195. Cache Encryption

Sensitive cache data MAY require encryption at rest depending backend/security policy.

Best approach remains:

```text
do not cache unnecessary sensitive data
```

---

# 196. Cache Namespace Separation

Recommended logical namespaces:

```text
public:
builder:
protected:
admin:
media:
document:
qr:
external:
```

---

# 197. Namespace Is Not Authorization Alone

Server still enforces access.

---

# 198. Cache Observability

Minimum metrics:

```text
hit rate
miss rate
build latency
invalidations
cache errors
entry age
stale serves
evictions
memory/storage usage
```

---

# 199. Security Metrics

Track meaningful:

```text
protected cache access denial
revocation invalidation failure
unexpected namespace collision
```

without logging secrets.

---

# 200. Cache Logging

Do not log raw:

```text
private token
session secret
full private DTO
```

---

# 201. Correlation

Cache build/invalidation MAY include correlation ID from source mutation/job.

---

# 202. Invalidation Audit

Routine cache invalidation does not need business AuditEvent.

Operational logs/metrics are sufficient.

---

# 203. VOP Integration

VOP may surface:

```text
repeated cache rebuild failures
stale current projection
security invalidation failure
cache storage unavailable
unusually high miss/backlog
```

---

# 204. VOP Safe Action

AUTO-4:

```text
clear affected cache
rebuild projection
retry cache warming
```

when deterministic.

---

# 205. Cache Storage Failure

VOP/system health may mark:

```text
DEGRADED
```

if source fallback works.

---

# 206. Full Cache Flush

Safe from data-integrity perspective.

May affect performance.

---

# 207. Rebuild Order

If full cache lost:

prioritize:

```text
P0 public
current Questionnaire
critical media aliases
then lower-priority content
```

if proactive warm-up used.

---

# 208. Backup

Cache generally does NOT require backup.

---

# 209. Exception

If something “cached” requires backup to avoid data loss, it is likely misclassified and should be modeled as persistent artifact/state.

---

# 210. Restore

After DB/media restore:

cache SHOULD be considered disposable and rebuilt from restored authoritative data.

---

# 211. Cache Migration

Deployment may invalidate all incompatible cache versions.

No need to migrate most ephemeral cache records.

---

# 212. Cache Schema Evolution

Use:

```text
key version
projection version
namespace version
```

rather than complex migration where possible.

---

# 213. Multi-Instance Web

If system later runs multiple Web instances:

local process caches may diverge.

---

# 214. Multi-Instance Strategy

Options:

```text
shared distributed cache
event invalidation to all instances
versioned key strategy
minimal process-local caching
```

---

# 215. Current Single-Host Deployment

Can remain simpler while designing invalidation contracts so future shared cache can be added.

---

# 216. Cache Warmed from Database

Cache builder reads via projection/application read layer.

It should not bypass visibility rules with raw SQL then serialize all fields.

---

# 217. Public Cache Builder

Must produce same safe DTO as uncached public path.

---

# 218. Cache/No-Cache Equivalence

Given same Source state and access context:

```text
cached response
==
uncached response
```

semantically.

---

# 219. CACHE-E2E-001 — Public Contact Revocation

1. Contact public=true.
2. Public page cached.
3. Admin sets public=false.
4. Request again.

Expected:

```text
Contact absent immediately according to defined secure invalidation path.
```

---

# 220. CACHE-E2E-002 — Builder Revocation

Builder-eligible Contact selected/cached.

Admin revokes permission.

Generate revalidates and cannot include Contact.

---

# 221. CACHE-E2E-003 — Private Token Revocation

Private PDF previously cached.

Token revoked.

Unauthorized request cannot retrieve artifact from cache.

---

# 222. CACHE-E2E-004 — Locale Isolation

Cache RU and EN versions.

Verify no cross-locale content.

---

# 223. CACHE-E2E-005 — Questionnaire New Revision

R5 cached.

R6 published.

Current alias serves R6.

Revision-specific R5 remains R5.

---

# 224. CACHE-E2E-006 — Historical Stability

Profile changes.

Historical Revision cache does not update.

---

# 225. CACHE-E2E-007 — Theme Activation

Content Projection same.

Rendered public output switches to active Theme Revision.

No stale old Theme after invalidation beyond defined safe transition.

---

# 226. CACHE-E2E-008 — Cache Loss

Delete all cache entries.

Public site still works from authoritative Source.

---

# 227. CACHE-E2E-009 — Cache Storage Down

Disable cache backend.

Core public read falls back appropriately.

No Source write failure solely because cache unavailable.

---

# 228. CACHE-E2E-010 — PDF Artifact

Delete PDF response cache/CDN copy.

Persistent QuestionnaireArtifact remains or regenerates from exact Revision.

---

# 229. CACHE-E2E-011 — Media Derivative Browser Cache

Physical source backend changes while stable delivery identity remains.

Correct derivative continues through new origin/URL mapping.

---

# 230. CACHE-E2E-012 — QR Key

Change canonical URL.

New QR does not reuse old cached QR artifact.

---

# 231. CACHE-E2E-013 — Public/Private Collision

Public Questionnaire and token-scoped Questionnaire have similar IDs/content.

Verify cache namespaces cannot cross-serve.

---

# 232. CACHE-E2E-014 — Stampede

Invalidate popular Home projection.

Send concurrent requests.

Expected:

```text
controlled rebuild
no uncontrolled duplicate expensive computation
```

where protection configured.

---

# 233. CACHE-E2E-015 — SWR Safety

Non-sensitive public Project list may serve bounded stale content.

Private access revocation never uses same SWR behaviour.

---

# 234. CACHE-E2E-016 — Search Staleness

Search result cached for now-private Project.

Direct final route authorization blocks access.

---

# 235. CACHE-E2E-017 — Next.js Cache

Framework cache invalidation produces same semantics as direct projection read after Contact visibility change.

---

# 236. CACHE-E2E-018 — Deployment Schema Version

Old projection cache v2 exists.

Deploy v3.

Application never deserializes v2 as v3 incorrectly.

---

# 237. CACHE-E2E-019 — Admin Cross-User Safety

Permission-sensitive Admin read cannot leak more privileged cached result to less privileged Admin.

---

# 238. CACHE-E2E-020 — Protected Browser Cache

Sign out/lose authorization.

Protected private document cannot be casually retrieved through shared public caching route.

---

# 239. CACHE-E2E-021 — Full Cache Flush

Clear current public cache during production.

No professional data lost.

---

# 240. CACHE-E2E-022 — Link Health Freshness

Cached health status displays `checked_at`; system never implies it is live certainty.

---

# 241. Cache Anti-Pattern CACHE-AP-001

**Cache is only copy of business data**

---

# 242. CACHE-AP-002

**Raw Profile row cached and sent publicly**

---

# 243. CACHE-AP-003

**Private Contact hidden client-side after being cached publicly**

---

# 244. CACHE-AP-004

**Public and Admin responses share key**

---

# 245. CACHE-AP-005

**Builder uses generic Public Profile cache despite independent permissions**

---

# 246. CACHE-AP-006

**Visibility change relies only on one-hour TTL**

---

# 247. CACHE-AP-007

**Token revocation does not invalidate/revalidate cached protected response**

---

# 248. CACHE-AP-008

**Historical Revision cache invalidated/rebuilt from current Profile**

---

# 249. CACHE-AP-009

**Current alias treated as immutable**

---

# 250. CACHE-AP-010

**Immutable Revision cache unnecessarily purged on every source update**

---

# 251. CACHE-AP-011

**Redis added without operational need**

---

# 252. CACHE-AP-012

**Redis/cache used as durable job authority without appropriate persistence model**

---

# 253. CACHE-AP-013

**Public Builder Session stored only in cache and lost on eviction**

---

# 254. CACHE-AP-014

**AIDraft stored only in cache**

---

# 255. CACHE-AP-015

**Feedback idempotency key stored only in volatile cache**

---

# 256. CACHE-AP-016

**Public PDF cache key excludes locale**

---

# 257. CACHE-AP-017

**Rendered page cache ignores active Theme**

---

# 258. CACHE-AP-018

**Cache key contains plaintext private access token and is logged**

---

# 259. CACHE-AP-019

**Private attachment response cached by public CDN**

---

# 260. CACHE-AP-020

**Stale-while-revalidate used for authorization decisions**

---

# 261. CACHE-AP-021

**Cache builder bypasses Public Projection and reads all DB fields**

---

# 262. CACHE-AP-022

**Cache miss changes business outcome**

---

# 263. CACHE-AP-023

**Search cache used to decide whether entity is public**

---

# 264. CACHE-AP-024

**Broken cache prevents Profile edit from committing**

---

# 265. CACHE-AP-025

**Browser cache treated as revocation-safe access control**

---

# 266. Cache Quality Gate

Перед implementation MUST быть определены:

- [ ] cache classes;
- [ ] authoritative source for every cache;
- [ ] key schema;
- [ ] locale dimension;
- [ ] access scope;
- [ ] public/private namespace separation;
- [ ] immutable vs current semantics;
- [ ] TTL class;
- [ ] invalidation event;
- [ ] security revocation behaviour;
- [ ] fail-open/fail-closed policy;
- [ ] stale-while-revalidate eligibility;
- [ ] stampede protection;
- [ ] projection schema version;
- [ ] deployment invalidation;
- [ ] multi-instance implications;
- [ ] browser/CDN policy;
- [ ] cache fallback;
- [ ] observability;
- [ ] privacy tests.

---

# 267. Cache Specification Template

```text
Cache Name:
Public Home

Class:
CURRENT_PUBLIC

Source:
Public Home Projection

Key:
profile + locale + projection version

Access:
PUBLIC

TTL:
SHORT

Invalidated By:
ProfileChanged
PrimaryPortfolioChanged
EmotionalGridPublished
QuestionnairePrimaryChanged
ContactVisibilityChanged
Theme impact if rendered cache

SWR:
allowed only within defined non-sensitive window

Fallback:
rebuild from authoritative Projection

Backup:
No
```

---

# 268. Immutable Revision Cache Template

```text
Cache Name:
Questionnaire Revision Projection

Class:
IMMUTABLE_REVISION

Source:
QuestionnaireRevision

Key:
revision_id + locale + schema version

Invalidation:
normally none

Access:
PUBLIC / TOKEN_SCOPED according to Revision

TTL:
LONG / IMMUTABLE

Source Mutation Impact:
none
```

---

# 269. Builder Cache Template

```text
Cache Name:
Builder Session Projection

Class:
BUILDER

Key:
session_id + session_version + locale + builder_policy_version

Access:
session-scoped

Source:
PublicBuilderSession + current eligible projection

Security:
Generate always server-revalidates

Invalidated By:
Session update
Builder permission change
Template/config change
Session expiry

Backup:
No — authoritative Session itself lives outside cache
```

---

# 270. Protected Artifact Cache Template

```text
Class:
TOKEN_SCOPED

Source:
Protected Artifact

Key:
grant/access scope + artifact identity

Request Requirement:
current token/grant validation

Shared Public Cache:
Forbidden

Revocation:
effective immediately through access validation/invalidation
```

---

# 271. Cache Invalidation Matrix

| Source Event | Primary Cache Effects |
|---|---|
| ProfileChanged | Public Profile/Home |
| PrimaryPortfolioChanged | Home/Quick View/Portfolio |
| ContactVisibilityChanged | Public Contact/Home/Builder |
| ProfessionalLinkChanged | Link projection/QR/current docs |
| EmotionalGridPublished | Emotional/Home |
| ProjectPublished | Project/Home/Search |
| ProjectArchived | Project/Search |
| QuestionnairePublished | Hub/current alias |
| QuestionnairePrimaryChanged | Hub/Home CTA |
| BuilderPolicyChanged | Builder eligibility/session projections |
| ThemeActivated | Rendered public responses |
| TokenRevoked | Protected access cache |
| MediaDerivativeChanged | relevant media aliases |

---

# 272. Cache Freshness Matrix

| Data | Freshness Requirement |
|---|---|
| Contact visibility | Immediate/high |
| Protected token | Immediate/high |
| Builder eligibility | At Generate authoritative |
| Opportunity state | Prefer uncached/current |
| Public Home copy | Short bounded |
| Project list | Short/medium |
| Historical Revision | Immutable |
| Media derivative | Immutable/versioned |
| QR for fixed URL | Immutable |
| Analytics aggregate | Eventual |
| Link health | Timestamped eventual |

---

# 273. Cache Access Matrix

| Cache | Public | Session | Token | Admin |
|---|---:|---:|---:|---:|
| Public Home | ✓ |  |  |  |
| Public Portfolio | ✓ |  |  |  |
| Revision Public | ✓ |  | maybe | admin can read source |
| Builder Session |  | ✓ |  | authorized Admin optionally |
| Casting Package |  |  | ✓ | ✓ |
| Admin Dashboard |  |  |  | ✓ |
| Private Media |  |  | maybe | ✓ |

---

# 274. Architecture Diagram

```text
                    AUTHORITATIVE DATA
                           │
                           ▼
                    PROJECTION LAYER
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
           PUBLIC       BUILDER       ADMIN
              │            │            │
              ▼            ▼            ▼
        Public Cache   Scoped Cache   Admin Cache
              │            │            │
              ▼            ▼            ▼
           Browser       Session       Admin UI
```

---

# 275. Invalidation Diagram

```text
DOMAIN MUTATION
      │
      ▼
 DB COMMIT
      │
      ▼
DOMAIN EVENT / OUTBOX
      │
      ▼
CACHE INVALIDATION COORDINATOR
      │
 ┌────┼─────────────┐
 ▼    ▼             ▼
Public Builder   Rendered
Cache  Cache      Cache
```

---

# 276. Revision Cache Diagram

```text
Questionnaire Revision 5
       │
       ├── HTML Projection Cache
       ├── PDF Artifact / CDN Cache
       └── QR Cache

Questionnaire Revision 6
       │
       ├── own independent cache
       └── current alias now points here

Revision 5 remains unchanged.
```

---

# 277. Security Doctrine

Canonical access sequence for protected cache:

```text
REQUEST
  ↓
AUTH/TOKEN VALIDATION
  ↓
ACCESS SCOPE
  ↓
CACHE LOOKUP WITH SCOPE
  ↓
RESPONSE
```

Never:

```text
REQUEST
  ↓
GLOBAL CACHE HIT
  ↓
maybe check access
```

---

# 278. Cache Rebuild Doctrine

```text
CACHE MISS
   ↓
AUTHORITATIVE PROJECTION
   ↓
SAFE DTO
   ↓
CACHE WRITE
   ↓
RESPONSE
```

If cache write fails:

```text
response may still use safe DTO
```

---

# 279. Cache Compliance Criteria

Реализация соответствует DOC-077, если:

1. cache never becomes Source of Truth;
2. complete cache loss does not lose business data;
3. public cache contains only public-safe projections;
4. Builder cache is separate from Public cache;
5. private/token-scoped cache is isolated;
6. protected requests validate access independently of cached payload;
7. locale is included when relevant;
8. immutable Revision and current alias are separate;
9. historical cache does not update from current Source;
10. visibility revocation does not rely only on TTL;
11. invalidation is event/source-aware;
12. current security-sensitive outputs are revalidated;
13. SWR is prohibited for access revocation decisions;
14. cache keys are explicit/versioned;
15. cache schema upgrades cannot deserialize incompatible records silently;
16. process-local cache is optional acceleration only;
17. Builder Session itself is not stored only as cache;
18. AI outputs are not cache-only;
19. idempotency records are not treated as volatile cache;
20. media/public document immutable URLs can use long caching;
21. private artifacts never enter generic public CDN cache;
22. Theme changes invalidate rendered output appropriately;
23. cache failures do not invalidate successful source commits;
24. stampede protection exists where expensive rebuilds justify it;
25. observability can detect misses, stale entries and invalidation failures;
26. cache storage does not require backup for business recovery;
27. full cache rebuild from authoritative state is possible;
28. multi-instance evolution is supported without changing business semantics;
29. protected/browser cache headers align with security classification;
30. cached and uncached responses are semantically equivalent.

---

# 280. Финальная доктрина

> **Cache в этой системе существует исключительно после Source of Truth, domain rules и privacy projection. Он может сделать Public Profile, Portfolio, Questionnaire, media и read models быстрее, но не может решить, что является опубликованным, кто имеет доступ, какой Contact разрешён, какая Questionnaire Revision текущая или какой Builder item допустим. Чем ближе данные к security, privacy или текущему business workflow, тем меньше система должна доверять stale cache; чем более данные immutable и revision-specific, тем агрессивнее их можно кэшировать.**