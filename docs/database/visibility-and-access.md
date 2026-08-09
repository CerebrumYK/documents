# VISIBILITY MODEL

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная модель видимости, exposure, privacy scopes и server-side projection

**Целевой файл:** `docs/database/visibility-and-access.md`  
**Документ:** DOC-092  
**Статус:** ✅ Completed  
**Тип:** Database / Visibility / Privacy / Projection / Exposure Control

**Связанные документы:**
- `docs/TZ.md`
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
- `docs/architecture/media.md`
- `docs/architecture/pdf.md`
- `docs/architecture/background-jobs.md`
- `docs/architecture/cache.md`
- `docs/architecture/search.md`
- `docs/architecture/ai.md`
- `docs/architecture/notifications.md`
- `docs/database/database-architecture.md`
- `docs/database/data-dictionary.md`

---

# 1. Назначение документа

Настоящий документ определяет **кто, где и при каких условиях может видеть конкретные данные продукта**.

Он фиксирует visibility/exposure semantics для:

1. Public Site;
2. Public Search;
3. Public Questionnaire;
4. Public Questionnaire Builder;
5. Builder-generated document;
6. token-scoped Casting material;
7. Admin UI;
8. Admin Search;
9. AI capability contexts;
10. Notifications;
11. Analytics;
12. Logs;
13. Cache;
14. background jobs;
15. historical Revisions/Snapshots;
16. archived entities;
17. deleted/redacted entities;
18. visibility revocation;
19. cache/search invalidation;
20. security-first fallback.

---

# 2. Главный принцип

> **Наличие значения в PostgreSQL не означает права его показывать.**

Canonical:

```text
DATABASE VALUE
     ↓
CONTEXT
     ↓
AUTHORIZATION
     ↓
VISIBILITY POLICY
     ↓
LIFECYCLE / PUBLICATION
     ↓
PROJECTION
     ↓
SAFE DTO
     ↓
OUTPUT
```

---

# 3. VIS identifiers

Используются:

```text
VIS-*
VIS-PUB-*
VIS-ADM-*
VIS-QNR-*
VIS-BLD-*
VIS-TKN-*
VIS-AI-*
VIS-SRCH-*
VIS-ANL-*
VIS-LOG-*
VIS-INV-*
VIS-AP-*
```

---

# 4. VIS-INV-001 — Server Is Authority

Visibility MUST определяться server-side.

Запрещено:

```text
server sends private value
→ browser hides with CSS
```

или:

```text
server sends everything
→ React decides what to display
```

---

# 5. VIS-INV-002 — Hidden Data Is Not Serialized

Если поле не разрешено в текущем context:

оно SHOULD отсутствовать из DTO полностью.

Не:

```json
{
  "phone": "+...",
  "hidden": true
}
```

для Public consumer.

---

# 6. VIS-INV-003 — Contexts Are Independent

Следующие права независимы:

```text
Public Site
Prepared Questionnaire
Public Builder
Token-scoped Casting Package
Admin
AI
Search
Analytics
```

---

# 7. VIS-INV-004 — Public Visibility Does Not Imply Builder Eligibility

Valid:

```text
show_on_public_site = true
allow_in_public_questionnaire_builder = false
```

---

# 8. VIS-INV-005 — Builder Eligibility Does Not Imply Public Site Visibility

Valid:

```text
show_on_public_site = false
allow_in_public_questionnaire_builder = true
```

---

# 9. VIS-INV-006 — Admin Questionnaire Eligibility Is Separate

Valid:

```text
show_on_public_site = false
allow_in_admin_questionnaires = true
```

---

# 10. VIS-INV-007 — AI Access Is Task-Specific

Data visible to Admin is **not automatically available to every AI capability**.

---

# 11. VIS-INV-008 — Search Scope Cannot Exceed Projection Scope

Public Search cannot contain anything that corresponding Public Search Projection is forbidden to expose.

---

# 12. VIS-INV-009 — Analytics Is Minimal

Analytics stores event context required for measurement, not arbitrary source entity payloads.

---

# 13. VIS-INV-010 — Logs Are Not Data Export

Logs MUST NOT become an uncontrolled duplicate store for private Source data.

---

# 14. Canonical visibility contexts

```text
PUBLIC_SITE
PUBLIC_SEARCH
PREPARED_QUESTIONNAIRE
PUBLIC_BUILDER
BUILDER_GENERATED_ARTIFACT
TOKEN_SCOPED_CASTING
ADMIN_UI
ADMIN_SEARCH
AI_CASTING
AI_BB
AI_THEME
AI_VOP
NOTIFICATION_EXTERNAL
NOTIFICATION_IN_APP
ANALYTICS
LOGS
BACKGROUND_JOB
AUDIT
```

---

# 15. Context ≠ User Role

Visibility depends on both:

```text
principal
+
usage context
```

Example:

Admin may see Contact in Admin UI but BB biography task still must not receive that phone number if unnecessary.

---

# 16. Data classes

Canonical data visibility classes:

```text
PUBLIC_ELIGIBLE
PROFESSIONAL_RESTRICTED
PRIVATE_PII
ADMIN_INTERNAL
SECURITY_SECRET
TECHNICAL_INTERNAL
HISTORICAL_SNAPSHOT
DERIVED_PUBLIC
DERIVED_PRIVATE
```

---

# 17. PUBLIC_ELIGIBLE

Can potentially appear publicly if all publication/policy rules pass.

Examples:

```text
professional name
Portfolio
published Project
Skill
Language
approved Contact
```

---

# 18. PROFESSIONAL_RESTRICTED

Professional but not automatically public.

Examples:

```text
Builder-only Project
Admin-questionnaire-only Contact
Casting-specific material
```

---

# 19. PRIVATE_PII

Examples:

```text
Feedback sender phone
Feedback sender email
private Contact
Casting source attachment
```

---

# 20. ADMIN_INTERNAL

Examples:

```text
Feedback notes
Opportunity next action
VOP recommendation
AI Draft
```

---

# 21. SECURITY_SECRET

Examples:

```text
OAuth access token
API key
session secret
provider credential
```

Never ordinary visibility projection.

---

# 22. TECHNICAL_INTERNAL

Examples:

```text
storage_locator
checksum
job lease
provider request ID
```

---

# 23. HISTORICAL_SNAPSHOT

Visibility determined by:

```text
snapshot/revision access class
+
current privacy/redaction policy
```

not current source visibility flags alone.

---

# 24. Visibility decision formula

Conceptually:

```text
ALLOW =
  principal_authorized
  AND
  context_allowed
  AND
  entity_lifecycle_allowed
  AND
  publication_allowed
  AND
  field_or_item_policy_allowed
  AND
  access_scope_valid
  AND
  no_active_redaction_or_revocation
```

---

# 25. Public Site policy

Public Site may receive only:

```text
published/current public-safe Source
AND
show_on_public_site = true
```

where item-level flag exists.

---

# 26. Public Profile identity

Normally Public if Profile:

```text
status = active
```

and site is publicly enabled.

---

# 27. Professional name

Public by product function.

May be included in:

```text
Public Site
Prepared Questionnaire
Builder
PDF
Search
```

---

# 28. Internal Profile IDs

Not needed in Public DTO unless used internally as opaque transport identifier.

Prefer public slug/route identity.

---

# 29. Date of birth

Potentially sensitive professional datum.

Visibility MUST be explicit.

Do not infer:

```text
actor profile active
→ DOB public
```

---

# 30. Age

If displayed, derive from approved DOB/current date according to product policy rather than store inconsistent duplicate age.

Visibility follows DOB/age field policy.

---

# 31. Physical parameters

Examples:

```text
height
hair color
eye color
clothing size
shoe size
```

Each may have independent policy through `profile_field_policies`.

---

# 32. Scalar field policy

A profile scalar fact may be:

```text
public = false
admin questionnaire = true
public builder = true
```

independently.

---

# 33. Public biography

Only approved/current localized biography.

Draft/AIDraft never Public.

---

# 34. Portfolio public rule

Portfolio item appears publicly only when:

```text
publication_state = published
AND
show_on_public_site = true
AND
MediaAsset available/safe
```

---

# 35. Primary photo does not override visibility

A Primary Close-Up must also satisfy publication/public policy before ordinary public rendering.

---

# 36. Questionnaire mandatory photo exception

For Prepared Questionnaire:

required Primary Close-Up/Full Body require:

```text
published/eligible Main Portfolio
AND
allow_in_admin_questionnaires = true
```

according to questionnaire policy.

---

# 37. Builder mandatory photo rule

For Public Builder:

required photos must satisfy:

```text
published/available
AND
allow_in_public_questionnaire_builder = true
```

---

# 38. No fallback from public permission

A Public photo cannot be forced into Builder when Builder flag=false.

---

# 39. Emotional Portfolio public rule

Session/Grid requires:

```text
published
AND
show_on_public_site = true
```

where applicable.

---

# 40. Emotional source photos

Individual source photos MAY be public on full Emotional Portfolio if explicitly designed/allowed.

Questionnaire row uses only approved Grid composite + date + link.

---

# 41. Grid Draft visibility

```text
Admin only
```

Never Public/Builder/Prepared output.

---

# 42. Unconfirmed Grid cells

Never public artifact source.

---

# 43. Project public rule

```text
publication_state = published
AND
show_on_public_site = true
```

---

# 44. Builder-only Project

May be absent ordinary Public Site yet selectable in Builder.

---

# 45. Role public rule

Requires:

```text
Role public policy
AND
parent Project context eligible
```

unless a future explicit standalone Role route is defined.

---

# 46. Role Media

Visibility is contextual.

A MediaAsset being technically available does not make every RoleMedia usage public.

---

# 47. Training public rule

Independent `show_on_public_site`.

---

# 48. Achievement public rule

Independent.

---

# 49. Skill public rule

```text
actor_skill publication/current
AND
show_on_public_site
```

---

# 50. Skill level

May be exposed only when Skill itself allowed.

---

# 51. Language public rule

Same independent visibility model.

---

# 52. Professional Link public rule

Requires:

```text
published
AND
show_on_public_site
AND
URL structurally safe
```

---

# 53. Link health does not expose technical internals

Public may receive:

```text
available/unavailable behavior
```

but not necessarily:

```text
HTTP 503
DNS failure
provider diagnostic
```

---

# 54. Contact public rule

Contact visible publicly only if:

```text
active
AND
show_on_public_site = true
```

---

# 55. Hidden Contact

Must not appear in:

```text
HTML source
JSON
RSC payload
page source
search
client cache
analytics
```

---

# 56. Prepared Questionnaire visibility

Prepared Questionnaire gets data through **questionnaire-specific snapshot generation**.

---

# 57. Prepared Questionnaire eligibility

For selectable reusable items:

```text
allow_in_admin_questionnaires = true
```

plus domain lifecycle/readiness.

---

# 58. Prepared Questionnaire is not Public Profile copy

It can intentionally include data omitted from ordinary site.

---

# 59. Example

Contact:

```text
public=false
admin_questionnaire=true
```

may legitimately appear in Questionnaire PDF.

---

# 60. Questionnaire Revision freeze

Once Revision created:

included values are frozen historically.

---

# 61. Current source permission change

Does not silently rewrite existing Revision.

---

# 62. Historical privacy exception

Privacy/legal redaction policy MAY block serving historical Revision/artifact even though snapshot remains historically recorded.

---

# 63. Revision visibility levels

Possible:

```text
PUBLIC
TOKEN_SCOPED
ADMIN_ONLY
```

according to questionnaire.

---

# 64. Primary public questionnaire

Usually PUBLIC.

---

# 65. Casting-specific questionnaire

May be:

```text
TOKEN_SCOPED
```

or Admin-only before sharing.

---

# 66. Public Builder source boundary

Builder server query starts from:

```text
Builder Eligibility Projection
```

not ordinary Public Projection.

---

# 67. Builder item condition

At minimum:

```text
same profile
AND
current active/published status
AND
allow_in_public_questionnaire_builder = true
AND
context/template allows
```

---

# 68. Builder does not receive hidden item inventory

If 50 entities exist but only 8 eligible:

response contains only 8.

Not:

```text
50 rows + allowed=false
```

---

# 69. Builder session cannot escalate permission

Visitor changing:

```text
entity_id
```

in request does not permit unauthorized item.

---

# 70. Builder Preview

Server revalidates relevant current selections as appropriate.

---

# 71. Builder Generate

MUST perform full authoritative eligibility revalidation regardless of cached Preview.

---

# 72. Permission revoked before Generate

Item is:

```text
removed/blocking
```

according to builder UX policy.

It MUST NOT enter Generation Snapshot.

---

# 73. Permission revoked after Snapshot

Generation Snapshot remains historical immutable record.

Serving resulting artifact then follows:

```text
artifact access class
+
privacy/redaction policy
```

---

# 74. Builder temporary casting labels

`casting_name`, `role_name`, `project_name` belong session.

They do not become Actor/Profile facts.

---

# 75. Builder generated PDF

May contain:

```text
selected eligible content
visitor temporary casting labels
official Profile URL
generated date
```

Nothing outside frozen snapshot.

---

# 76. Token-scoped casting context

Token-scoped resource grants access only to specifically authorized package/resource scope.

---

# 77. VIS-TKN-INV-001 — Token Is Capability, Not Global Account

Token cannot permit:

```text
all unpublished Projects
all Contacts
Admin UI
other Casting packages
```

---

# 78. Token scope must bind

At least:

```text
resource/package
status
expiry
revocation
```

---

# 79. Token storage

Prefer hash/reference rather than recoverable plaintext if possible.

---

# 80. Token in logs

Raw token MUST NOT appear in normal logs.

---

# 81. Token-scoped media

Media referenced by package may be delivered only through package-safe/public delivery route.

Internal storage locator never exposed.

---

# 82. Token revocation

Must take effect independently of cache TTL.

---

# 83. Token expiry

Expired access fails closed.

---

# 84. Admin UI

Admin may read broader data according to authentication/authorization.

---

# 85. Admin does not imply raw secret access

Even Admin UI should not display provider secrets.

---

# 86. Admin technical metadata

May show safe diagnostics:

```text
MIME
file size
processing state
job failure code
```

where useful.

---

# 87. Admin storage locator

Should generally remain hidden from normal UI.

May be available only to system diagnostics if required.

---

# 88. Admin Feedback visibility

Authorized Admin may see:

```text
sender data
message
attachments
internal notes
workflow
```

---

# 89. Admin Casting visibility

Authorized Admin may see private casting source and analysis.

---

# 90. Admin Opportunity

Internal pipeline fields remain Admin-only.

---

# 91. Admin AI Drafts

Visible only within Admin/AI context.

Never automatically Public.

---

# 92. Admin Audit

Separate permission surface.

Not all future Admin roles must necessarily receive full audit access.

---

# 93. Admin Search

May index private/admin data only in separate Admin scope.

---

# 94. Public Search

Must only index public-search-approved content.

---

# 95. Public Search eligibility

Normally:

```text
show_on_public_site = true
AND
published/current
```

plus field allowlist.

---

# 96. Builder Search

Uses Builder eligibility.

---

# 97. Admin Search

Uses Admin authorization.

---

# 98. Search snippet safety

Even if title is safe, snippet must not accidentally include private neighboring text.

---

# 99. Search autocomplete

Same visibility rules as full search results.

---

# 100. Search zero results

Must not reveal:

```text
3 hidden matches
```

---

# 101. Search index stale exposure

Visibility revocation should trigger urgent reindex/removal.

---

# 102. Direct route remains final authority

If stale search result survives briefly:

target route still denies hidden entity.

---

# 103. AI visibility architecture

AI access is **capability-specific projection**.

---

# 104. Casting AI context

May include:

```text
Casting source
relevant confirmed Profile facts
relevant published/internal professional facts
```

only as needed for current analysis.

---

# 105. Casting AI excluded by default

```text
unrelated Feedback
OAuth tokens
Admin sessions
other Castings
Support tickets
Audit
Notification recipients
```

---

# 106. BB Assistant context

Depends on task.

---

# 107. BB biography context

May include:

```text
confirmed profile facts
approved Projects/Roles
Training
Skills
Languages
existing biography
```

---

# 108. BB biography must not receive

```text
private Feedback sender phone
Casting notes
OAuth credentials
Admin audit
```

---

# 109. BB cover letter

May receive specific selected Casting context because task requires it.

---

# 110. BB custom task

Still uses explicit source selection/context policy.

No unrestricted database context.

---

# 111. Theme AI

MAY receive:

```text
Theme tokens
layout/component constraints
accessibility rules
Admin visual prompt
```

---

# 112. Theme AI MUST NOT receive

```text
private Contacts
Feedback
Casting Source
Opportunity
OAuth credentials
```

because unnecessary.

---

# 113. VOP AI

May receive:

```text
specific observation
deterministic evidence
affected domain summary
```

---

# 114. VOP AI does not need broad unrelated content

Normative.

---

# 115. AI provider exposure

External provider receives only Minimum Necessary Context.

---

# 116. AI context ≠ persisted provider history

Application must not assume external provider is confidential archival store.

---

# 117. Notification In-App

May contain more source context than external message because recipient is authenticated Admin.

Still keep summary-oriented.

---

# 118. External WhatsApp/Email

Default = minimal.

---

# 119. External notification may contain

```text
notification type
short source title/context
deadline if required
protected Admin URL
```

---

# 120. External notification excludes by default

```text
full Feedback message
private attachment
full Casting source
internal notes
AI raw analysis
private Contacts
```

---

# 121. Admin URL

Does not grant access by itself.

---

# 122. Analytics visibility

Analytics receives event attributes only.

---

# 123. Allowed analytics examples

```text
public_builder_opened
template_selected
pdf_generated
portfolio_opened
video_started
contact_started
```

---

# 124. Analytics metadata MAY include

```text
surface
locale
source attribution
template type
entity type
```

---

# 125. Analytics MUST NOT include

```text
Feedback message
Casting document text
private phone/email
Contact value
AI prompt content
OAuth token
storage locator
```

---

# 126. Analytics pseudonymous session

If needed:

use privacy-minimized identifier.

---

# 127. Analytics cannot become discovery backdoor

Admin analytics UI should not expose private user content through event payload.

---

# 128. Logging visibility

Logs are infrastructure diagnostics.

---

# 129. Safe logging fields

Examples:

```text
request_id
correlation_id
entity ID
operation
status
error code
latency
job ID
provider request ID
```

---

# 130. Logs MUST NOT routinely contain

```text
password
session token
OAuth token
API key
private attachment bytes
full Feedback message
full Casting source
AI private prompt
```

---

# 131. Error object sanitization

Provider errors may include PII/secrets.

Sanitize before storage/logging.

---

# 132. Background job visibility

Job payload contains only minimal references/config.

---

# 133. Job payload should prefer

```text
revision_id
snapshot_id
delivery_id
media_asset_id
```

over duplicated full private payload.

---

# 134. Background worker access

Worker uses system authorization to load only resources needed for registered handler.

---

# 135. Job inspection Admin UI

Should not display raw private payload if unnecessary.

---

# 136. Cache visibility

Cache contains only context-safe output.

---

# 137. Public cache

Public-safe Projection only.

---

# 138. Builder cache

Builder/session-scoped.

---

# 139. Admin cache

Authorization-sensitive where required.

---

# 140. Protected cache

Access scope is part of key and current authorization is revalidated.

---

# 141. Browser cache

Protected/private responses use restrictive cache policy.

---

# 142. Public immutable artifact cache

May be aggressive if artifact itself Public.

---

# 143. Historical revision visibility

Published public Revision may remain publicly available according to route/product policy.

---

# 144. Superseded does not mean private

A superseded public Revision may still be accessible in history only if product intentionally exposes history.

Baseline public UX does not need to expose all old Revisions.

---

# 145. Admin historical access

Authorized Admin may inspect historical Revisions.

---

# 146. Public current alias

Resolves only current selected published Revision.

---

# 147. Historical Revision direct route

If implemented:

must have explicit access policy.

Do not assume ID knowledge grants access.

---

# 148. Historical Source changes

Do not change snapshot contents.

---

# 149. Privacy redaction over history

If a field must no longer be served for legal/privacy reasons:

historical serving may be blocked or redacted according to DOC-094.

---

# 150. Visibility revocation lifecycle

Canonical:

```text
Admin changes permission
      ↓
DB COMMIT
      ↓
Outbox/Domain Event
      ↓
Public/Builder Projection invalidation
      ↓
Cache invalidation
      ↓
Search removal
      ↓
next request cannot expose value
```

---

# 151. Privacy-first ordering

For sensitive revocation, architecture should favor:

```text
deny/revalidate first
rebuild later
```

over:

```text
continue serving stale
until rebuild finishes
```

---

# 152. Contact revocation

Changing:

```text
show_on_public_site = false
```

requires removal from:

```text
Public profile
public cached DTO
render cache
Public Search if indexed
```

---

# 153. Builder permission revocation

Changing:

```text
allow_in_public_questionnaire_builder = false
```

requires:

```text
Builder query removal
Builder Search removal
session stale marking/revalidation
Generate rejection/removal
```

---

# 154. Admin questionnaire permission revocation

Affects future Draft selection/readiness.

Historical published Revision remains historical.

---

# 155. Professional Link URL change

Current contexts receive new URL according to visibility.

Old historical Revision keeps old exact URL.

---

# 156. Professional Link visibility change

QR current/draft eligibility updates.

Historical QR remains bound to historical Revision subject to privacy policy.

---

# 157. Media visibility revocation

Do not delete original binary automatically.

Stop serving through affected public relationship/route.

---

# 158. Direct Media URL

Preferred design uses managed routes/versioned derivatives.

Knowing storage path must never grant access.

---

# 159. Archived entity

Archive generally removes entity from current public/builder projections.

---

# 160. Admin archive visibility

Admin may still inspect archived entity.

---

# 161. Search archived visibility

Public/Builder:

```text
exclude
```

Admin:

```text
may include with archive filter
```

---

# 162. Deleted entity

Hard deletion semantics deferred to DOC-094.

Visibility default:

```text
not accessible
```

except retained immutable/audit history where explicitly permitted.

---

# 163. Redacted entity

Redaction can preserve structural historical record while removing sensitive payload.

---

# 164. Projection ownership

Canonical projections:

```text
PublicProjection
BuilderProjection
AdminProjection
AIContextProjection
SearchProjection
NotificationProjection
AnalyticsProjection
```

---

# 165. No projection may mutate Source

Normative.

---

# 166. PublicProjection responsibilities

- lifecycle;
- publication;
- field flags;
- locale;
- safe URLs;
- public media delivery;
- remove internal fields.

---

# 167. BuilderProjection responsibilities

- Builder permission;
- same-profile;
- template/context;
- current availability;
- item-level selection payload;
- no hidden inventory.

---

# 168. AdminProjection responsibilities

- authentication;
- authorization;
- Admin-safe fields;
- no secrets unless dedicated security operation.

---

# 169. AIContextProjection responsibilities

- task-specific allowlist;
- privacy minimization;
- exact source versions;
- provenance;
- no unrelated secrets.

---

# 170. SearchProjection responsibilities

- scope;
- searchable allowlist;
- safe title/snippet;
- locale;
- route;
- no private value leakage.

---

# 171. NotificationProjection responsibilities

- minimal message;
- safe template variables;
- protected Admin URL;
- no unnecessary source content.

---

# 172. AnalyticsProjection responsibilities

- minimal event dimensions;
- privacy-safe identifiers;
- no professional/private payload duplication.

---

# 173. Field-level policy table

`profile_field_policies` applies only to approved Profile scalar keys.

---

# 174. Field policy cannot reference arbitrary DB columns

`field_key` MUST be allowlisted.

---

# 175. Item-level policy

Core reusable item tables use explicit visibility columns.

Examples:

```text
portfolio_items
projects
roles
training_items
achievements
actor_skills
actor_languages
professional_links
contacts
emotional_sessions/grids
```

where relevant.

---

# 176. Default visibility policy

Safe default:

```text
show_on_public_site = false
allow_in_public_questionnaire_builder = false
```

---

# 177. Admin questionnaire default

May be true for normal professional source items according to module policy.

But sensitive Contacts SHOULD default conservatively.

---

# 178. New imported item

Must not accidentally become Public because importer omitted permission.

---

# 179. AI-created content default

AI Draft/Proposal default:

```text
Admin-only
```

---

# 180. New uploaded MediaAsset default

Technical/private until a professional relation explicitly exposes it.

---

# 181. VIS-INV-011 — MediaAsset Has No Automatic Publicness

Public visibility belongs to relation/context, not binary existence.

---

# 182. Questionnaire Snapshot access

Snapshot itself should not be directly serialized publicly.

Renderer/projection emits document representation.

---

# 183. Builder Session privacy

Session configuration belongs that temporary visitor context.

---

# 184. Builder Session ID enumeration

Session IDs must be opaque and protected against guessing.

---

# 185. Builder session data in analytics

Do not store full configuration as generic analytics metadata.

---

# 186. Builder session in Feedback

Feedback may reference `questionnaire_session_id`.

Admin can then resolve context.

Public cannot use Feedback reference to inspect session beyond its own allowed flow.

---

# 187. Casting source visibility

Admin/private AI context only.

---

# 188. Casting requirement visibility

Admin only baseline.

A Casting-specific generated questionnaire may reflect approved selected actor facts, not raw Casting analysis unless explicitly intended.

---

# 189. Profile Match visibility

Admin only.

---

# 190. Opportunity visibility

Admin only.

No Public Pipeline.

---

# 191. VOP visibility

Admin only.

---

# 192. BB Draft visibility

Admin only until Human Apply to target Draft and subsequent publication workflow.

---

# 193. Theme Draft visibility

Admin Preview only.

---

# 194. Theme Revision

Public site may use active ThemeRevision presentation, but public does not receive Admin prompt/AI provenance.

---

# 195. Social credential visibility

Never public.

Admin UI may show:

```text
connected account name
status
reauth required
```

but not token.

---

# 196. Help/Ticket visibility

Help articles can be Public/Admin according to status.

Support Tickets are Admin/internal.

---

# 197. Audit visibility

Admin/security context only.

Never Public Search, Analytics payload, AI general context.

---

# 198. Outbox visibility

Infrastructure only.

---

# 199. Background jobs visibility

Admin system diagnostics only.

---

# 200. Search document visibility

Raw `search_documents` table is infrastructure.

Consumers get Search DTO.

---

# 201. Visibility matrix — high level

| Domain | Public Site | Prepared Q | Builder | Token Package | Admin |
|---|---:|---:|---:|---:|---:|
| Profile identity | Yes | Yes | Yes | Yes | Yes |
| Physical parameters | Policy | Policy | Policy | Snapshot | Yes |
| Portfolio | Policy | Policy | Policy | Snapshot | Yes |
| Emotional | Policy | Policy | Policy | Snapshot | Yes |
| Projects/Roles | Policy | Policy | Policy | Snapshot | Yes |
| Training | Policy | Policy | Policy | Snapshot | Yes |
| Skills/Languages | Policy | Policy | Policy | Snapshot | Yes |
| Contacts | Policy | Policy | Policy | Snapshot | Yes |
| Feedback | No | No | No | No | Yes |
| Casting source | No | No | No | No | Yes |
| Opportunity | No | No | No | No | Yes |
| AI Draft | No | No | No | No | Yes |
| VOP | No | No | No | No | Yes |
| Audit | No | No | No | No | Restricted Admin |

---

# 202. Search visibility matrix

| Data | Public Search | Builder Search | Admin Search |
|---|---:|---:|---:|
| Public Skill | Yes | if Builder-enabled | Yes |
| Builder-only Skill | No | Yes | Yes |
| Public Project | Yes | if Builder-enabled | Yes |
| Hidden Project | No | No unless Builder-enabled | Yes |
| Contact value | Generally No | selectable via safe Builder UI if allowed | Yes |
| Feedback | No | No | Optional |
| Casting | No | No | Yes |
| Opportunity | No | No | Yes |
| AI Draft | No | No | Optional |
| VOP Observation | No | No | Optional |
| Secret | Never | Never | Never |

---

# 203. AI visibility matrix

| Data | Casting AI | BB | Theme AI | VOP AI |
|---|---:|---:|---:|---:|
| Profile facts | Relevant only | Relevant only | No | Relevant only |
| Portfolio | Relevant only | Relevant only | No | Relevant only |
| Casting source | Yes | Only task-specific | No | Only if observation needs |
| Feedback | Source-specific only | Task-specific | No | Observation-specific |
| Contact | Rare/task-specific | Rare/task-specific | No | Rare |
| Opportunity | If needed | If task-specific | No | Observation-specific |
| Theme tokens | No | No | Yes | If theme observation |
| OAuth secret | Never | Never | Never | Never |
| Audit | No baseline | No | No | Only narrow evidence if ever required |

---

# 204. External notification visibility matrix

| Field | WhatsApp/Email default |
|---|---:|
| Notification type | Yes |
| Short title | Yes |
| Deadline | If needed |
| Admin URL | Yes |
| Sender phone | No by default |
| Full Feedback text | No |
| Attachment | No |
| Casting source | No |
| AI analysis | No |
| Internal notes | No |
| Opportunity stage | Only if semantically needed |
| Provider technical IDs | No |

---

# 205. Analytics visibility matrix

| Field | Analytics |
|---|---:|
| Event type | Yes |
| Locale | Yes |
| Surface | Yes |
| Attribution | Yes |
| Public entity type | Yes |
| Public entity ID | Possibly opaque/internal |
| Contact value | No |
| Feedback text | No |
| Casting source | No |
| Attachment | No |
| AI prompt | No |
| OAuth token | Never |

---

# 206. Logs visibility matrix

| Data | Logs |
|---|---:|
| Correlation ID | Yes |
| Request ID | Yes |
| Entity ID | Yes |
| Error code | Yes |
| Duration | Yes |
| Full private message | No default |
| Secret | Never |
| Session token | Never |
| Provider credential | Never |
| Storage locator | Avoid unless protected diagnostic |
| AI prompt | No default |

---

# 207. Visibility and locale

Visibility is evaluated before/with locale resolution.

Missing translation MUST NOT expose hidden fallback content from a different visibility context.

---

# 208. Locale fallback

Can use approved localized fallback only if Source item itself is eligible.

---

# 209. Visibility and readiness

Readiness may inspect hidden/current source to determine completeness in Admin.

But Public should not see readiness diagnostics revealing hidden data.

---

# 210. Example

Admin:

```text
Builder cannot generate because Full Body Builder permission=false
```

Public visitor:

```text
required content unavailable
```

without unnecessary internal explanation.

---

# 211. Visibility and errors

Error responses must not reveal existence of unauthorized entity.

---

# 212. Unauthorized resource

Prefer semantically safe:

```text
not found / access denied
```

according to security context.

---

# 213. ID enumeration defense

Knowing UUID/slug does not grant visibility.

---

# 214. Public direct route

Must re-run policy even if result came from Search or cached navigation.

---

# 215. Visibility and canonical URLs

Public links generated only for public/token-scoped delivery routes.

Internal/admin URL never substituted into professional public QR.

---

# 216. Visibility and QR

QR target access class must match document/link intent.

---

# 217. Public QR

Only Public-safe route.

---

# 218. Token-scoped QR

May use intentionally shareable opaque protected route.

---

# 219. Admin QR

Not allowed in professional output.

---

# 220. Visibility and PDF

PDF generation uses exact Revision/Snapshot, but delivery endpoint still enforces artifact access class.

---

# 221. Artifact bytes are not visibility authority

Storage object exists ≠ user may download it.

---

# 222. Public Artifact

Can use stable public delivery URL.

---

# 223. Token Artifact

Requires valid grant each access.

---

# 224. Admin Artifact

Requires authenticated Admin.

---

# 225. Visibility and cache invalidation

Security-sensitive invalidation must be synchronous enough to prevent stale exposure.

---

# 226. Privacy-first cache rule

If cache state uncertain after revocation:

```text
miss/rebuild/deny
```

preferred over stale exposure.

---

# 227. Visibility and Search invalidation

Public/Builder removal is higher priority than non-sensitive content title update.

---

# 228. Visibility and background jobs

Delayed job MUST revalidate visibility if its operation creates a current exposure.

---

# 229. Example Builder

Eligibility checked before Generation Snapshot.

After Snapshot, PDF job uses exact snapshot.

---

# 230. Example Social

Scheduling approval does not imply source private media can suddenly be publicly published unless Social workflow explicitly validates selected media usage.

---

# 231. Example notification

Delivery worker should not rehydrate an entire Feedback and place it in external message simply because it can access it.

---

# 232. Visibility and Migration

Migration MUST preserve or conservatively initialize visibility.

---

# 233. Legacy unknown permission

Safe default:

```text
false for public
false for Builder
```

unless source behavior is known and explicitly mapped.

---

# 234. No permissive migration inference

Do not map:

```text
was visible somewhere
→ public + builder + admin questionnaire = true
```

---

# 235. Existing public pages

Can be mapped to `show_on_public_site=true` if documented current behavior proves exposure.

---

# 236. Existing questionnaire selection

Can map `allow_in_admin_questionnaires=true` when current behavior demonstrates it.

---

# 237. Builder new feature

Legacy data should not automatically all become Builder eligible without review/policy.

---

# 238. Visibility change audit

High-impact visibility mutations SHOULD be audited.

Examples:

```text
Contact public enabled
Builder permission enabled
private Casting package made shareable
token revoked
```

---

# 239. Routine derived invalidation

Does not need business Audit.

---

# 240. Visibility security events

Repeated unauthorized token/resource attempts MAY enter security logs/monitoring.

---

# 241. VIS-AP-001

**Fetch all Contacts and hide private ones in React**

---

# 242. VIS-AP-002

**One `is_public` boolean controls Public, Questionnaire and Builder**

---

# 243. VIS-AP-003

**Admin access automatically grants AI provider all Admin data**

---

# 244. VIS-AP-004

**Builder receives IDs/titles of ineligible items with disabled flag**

---

# 245. VIS-AP-005

**Public Search indexes Builder-only Project**

---

# 246. VIS-AP-006

**Search hit treated as access grant**

---

# 247. VIS-AP-007

**Token validation skipped because PDF exists in cache**

---

# 248. VIS-AP-008

**Private attachment served from static public path**

---

# 249. VIS-AP-009

**storage_locator sent to browser**

---

# 250. VIS-AP-010

**Current Contact revocation waits for one-hour TTL**

---

# 251. VIS-AP-011

**Historical Revision silently rewritten after visibility change**

---

# 252. VIS-AP-012

**Every Admin-only field included in VOP AI context**

---

# 253. VIS-AP-013

**Theme AI receives Casting source**

---

# 254. VIS-AP-014

**Analytics metadata contains Feedback message**

---

# 255. VIS-AP-015

**Logs contain OAuth token**

---

# 256. VIS-AP-016

**WhatsApp sends private attachment automatically**

---

# 257. VIS-AP-017

**Public QR points to `/admin/...`**

---

# 258. VIS-AP-018

**Builder session ID lets visitor read another Builder session**

---

# 259. VIS-AP-019

**Archived Project remains publicly accessible because route only checks slug**

---

# 260. VIS-AP-020

**AI Draft appears publicly before Apply/Publish**

---

# 261. VIS-AP-021

**Token package can browse unrelated unpublished profile data**

---

# 262. VIS-AP-022

**Admin search autocomplete shared with Public autocomplete**

---

# 263. VIS-AP-023

**Visibility flags copied into historical source as current authority**

---

# 264. VIS-AP-024

**Provider webhook returns payload which is then exposed to Public UI**

---

# 265. VIS-AP-025

**Migration enables all legacy content publicly “for compatibility” without evidence**

---

# 266. Visibility quality gate

Перед implementation MUST быть определены:

- [ ] all visibility contexts;
- [ ] all field/item policies;
- [ ] scalar Profile field policy;
- [ ] Public Projection;
- [ ] Builder Projection;
- [ ] Prepared Questionnaire Projection/Snapshot;
- [ ] token-scoped access;
- [ ] Admin authorization;
- [ ] AI task contexts;
- [ ] Public Search allowlist;
- [ ] Builder Search allowlist;
- [ ] Admin Search authorization;
- [ ] Notification minimal projection;
- [ ] Analytics minimization;
- [ ] Logging minimization;
- [ ] cache namespaces;
- [ ] revocation invalidation;
- [ ] archive behavior;
- [ ] historical Revision behavior;
- [ ] redaction override;
- [ ] migration defaults;
- [ ] privacy tests.

---

# 267. Public field policy template

```text
Field:
height_cm

Owner:
Profile

Public:
profile_field_policy.show_on_public_site

Admin Questionnaire:
profile_field_policy.allow_in_admin_questionnaires

Builder:
profile_field_policy.allow_in_public_questionnaire_builder

Admin:
authorized

Search:
only if Public projection explicitly indexes it

AI:
task-specific

Analytics:
never raw value unless separately justified
```

---

# 268. Item policy template

```text
Entity:
Project

Lifecycle:
published

Public:
show_on_public_site

Admin Questionnaire:
allow_in_admin_questionnaires

Builder:
allow_in_public_questionnaire_builder

Admin:
authorized

Public Search:
public + search field allowlist

Builder Search:
builder permission + template eligibility
```

---

# 269. Contact visibility template

```text
Contact:
Parent WhatsApp

Public:
false

Admin Questionnaire:
true

Builder:
false

Admin:
true

Public Search:
false

Builder Search:
false

AI Biography:
false

AI Casting Response:
only if explicitly required by selected task

External Notification:
false by default
```

---

# 270. Builder-only item example

```text
Project:
Example Project

show_on_public_site = false
allow_in_admin_questionnaires = true
allow_in_public_questionnaire_builder = true
```

Results:

```text
Public profile: hidden
Public Search: hidden
Prepared Questionnaire: eligible
Builder: eligible
Builder Search: eligible
Admin: visible
```

---

# 271. Public-only item example

```text
show_on_public_site = true
allow_in_admin_questionnaires = false
allow_in_public_questionnaire_builder = false
```

Results:

```text
Public: visible
Prepared Questionnaire: unavailable
Builder: unavailable
```

Valid configuration.

---

# 272. Fully private item

```text
false
false
false
```

Admin-only.

---

# 273. Historical snapshot example

Prepared Questionnaire R5 contains Contact A.

Later:

```text
Contact A → hidden everywhere
```

R5 bytes/snapshot stay immutable.

Serving R5:

```text
subject to historical privacy/redaction policy
```

not current Contact flag alone.

---

# 274. E2E-VIS-001 — Hidden Contact serialization

Request Public Profile.

Verify hidden Contact value is absent from:

```text
HTML
JSON/RSC payload
API response
page source
```

---

# 275. E2E-VIS-002 — Public-only Project

Project:

```text
public=true
builder=false
```

Expected:

```text
visible Public
visible Public Search if indexed
absent Builder
absent Builder Search
```

---

# 276. E2E-VIS-003 — Builder-only Project

```text
public=false
builder=true
```

Expected:

```text
hidden Public
hidden Public Search
visible Builder
visible Builder Search
```

---

# 277. E2E-VIS-004 — Questionnaire-only Contact

```text
public=false
admin_questionnaire=true
builder=false
```

Can appear in Prepared Questionnaire only when selected.

---

# 278. E2E-VIS-005 — Builder tampered ID

Submit hidden private Project ID manually.

Expected:

```text
server rejects
no DTO/snapshot exposure
```

---

# 279. E2E-VIS-006 — Builder revocation

Open session with selected Project.

Admin sets Builder permission=false.

Generate.

Expected:

```text
Project excluded/blocker
not frozen into new Snapshot
```

---

# 280. E2E-VIS-007 — Public revocation cache

Public Contact cached.

Admin disables Public permission.

Next secured public request cannot expose old Contact.

---

# 281. E2E-VIS-008 — Search stale hit

Archive Project but delay Search update.

Open stale search route.

Expected:

```text
authoritative route denies/unavailable
```

---

# 282. E2E-VIS-009 — Token package

Valid token can open Package A.

Attempt Package B with same token.

Denied.

---

# 283. E2E-VIS-010 — Token revoked

Previously cached package.

Token revoked.

Next request denied.

---

# 284. E2E-VIS-011 — Token expired

Expiry enforced despite valid cached binary.

---

# 285. E2E-VIS-012 — Admin URL

Unauthenticated user follows WhatsApp Admin deep link.

Authentication required before any private content.

---

# 286. E2E-VIS-013 — Theme AI minimization

Inspect Theme AI context.

No Contact/Feedback/Casting/Opportunity data.

---

# 287. E2E-VIS-014 — BB biography minimization

Inspect Biography task context.

No private Feedback sender data.

---

# 288. E2E-VIS-015 — Casting AI scope

Casting A analysis cannot receive Casting B source.

---

# 289. E2E-VIS-016 — Public Search contents

Inspect all Public Search documents.

No Builder-only/private fields.

---

# 290. E2E-VIS-017 — Public autocomplete

Private Project title never appears as autocomplete suggestion.

---

# 291. E2E-VIS-018 — Analytics privacy

Submit Feedback with phone/message.

Inspect analytics event.

Phone/message absent.

---

# 292. E2E-VIS-019 — Logs privacy

Trigger provider failure.

Verify logs contain error code/correlation ID but no provider token/private full payload.

---

# 293. E2E-VIS-020 — Media locator

Inspect Public Portfolio response.

No `storage_locator`.

---

# 294. E2E-VIS-021 — Private attachment

Knowing `feedback_attachments.id` does not enable unauthenticated download.

---

# 295. E2E-VIS-022 — Historical Revision

Change current Profile value.

Historical R1 still shows exact frozen allowed historical value.

---

# 296. E2E-VIS-023 — Historical privacy block

Apply redaction policy to historically sensitive Contact.

Historical document serving follows redaction/block policy without mutating original Revision record.

---

# 297. E2E-VIS-024 — AI Draft

Generate BB biography.

Before Apply/Publish:

Public page remains unchanged.

---

# 298. E2E-VIS-025 — Admin Search

Admin Search may find private Casting.

Public Search same text returns nothing.

---

# 299. E2E-VIS-026 — Notification payload

External WhatsApp contains minimal summary + Admin URL.

Private attachment/full source absent.

---

# 300. E2E-VIS-027 — Builder PDF

Generated Builder PDF contains only Generation Snapshot values.

No later-current private value leaks in renderer.

---

# 301. E2E-VIS-028 — Archive

Archive Training item.

It disappears from current Public/Builder surfaces according to lifecycle, remains Admin-visible/history-preserved.

---

# 302. E2E-VIS-029 — Migration

Legacy item with uncertain exposure migrates with conservative visibility defaults.

No accidental Public/Builder exposure.

---

# 303. E2E-VIS-030 — Cross-profile defense

If future second profile exists, manually submitting entity from profile B into profile A Builder is rejected.

---

# 304. Visibility traceability

Canonical:

```text
FIELD / ENTITY
      ↓
OWNER
      ↓
LIFECYCLE
      ↓
VISIBILITY POLICY
      ↓
CONTEXT
      ↓
PROJECTION
      ↓
SAFE OUTPUT
```

---

# 305. Contact traceability

```text
Contact Source
    ↓
3 independent permissions
    ├─ PublicProjection
    ├─ QuestionnaireSnapshot
    └─ BuilderProjection
```

---

# 306. Builder traceability

```text
Source Entity
     ↓
Builder Permission
     ↓
Template Eligibility
     ↓
Builder DTO
     ↓
Session Selection
     ↓
Generate Revalidation
     ↓
Generation Snapshot
```

---

# 307. AI traceability

```text
Source
  ↓
Capability Context Policy
  ↓
Minimum Necessary Projection
  ↓
Source Snapshot
  ↓
AI Provider
```

---

# 308. Search traceability

```text
Source
 ↓
Visibility Scope
 ↓
Search Projection
 ↓
Search Document
 ↓
Search Result
 ↓
Authoritative Target Revalidation
```

---

# 309. Privacy revocation traceability

```text
Visibility Mutation
      ↓
DB COMMIT
      ↓
Audit / Outbox
      ↓
Projection invalidation
      ↓
Cache invalidation
      ↓
Search removal
      ↓
No future exposure
```

---

# 310. Final visibility architecture

```text
                         POSTGRESQL
                            │
        ┌───────────────────┼───────────────────┐
        ▼                   ▼                   ▼
    CURRENT SOURCE      HISTORICAL         PRIVATE OPS
        │                SNAPSHOTS              │
        │                   │                   │
 ┌──────┼───────┐           │             ┌─────┼─────┐
 ▼      ▼       ▼           ▼             ▼     ▼     ▼
Public Builder Prepared   Access        Admin  AI   Internal
Policy Policy Questionnaire Class       Policy Context Tools
 │      │        │          │
 ▼      ▼        ▼          ▼
Safe   Safe     Frozen     Authorized
DTO    DTO      Document   Artifact
```

---

# 311. Visibility compliance criteria

Реализация соответствует DOC-092, если:

1. visibility enforced server-side;
2. hidden fields are not serialized;
3. Public/Questionnaire/Builder scopes are independent;
4. scalar Profile fields have independent policy;
5. item-level reusable professional data has independent policy;
6. Public Site uses only public-safe Projection;
7. Builder gets only Builder-eligible items;
8. Builder never receives hidden inventory;
9. Generate always revalidates current eligibility;
10. tampered IDs do not bypass Builder policy;
11. Prepared Questionnaire uses Admin Questionnaire permission;
12. historical Revisions are not rewritten by current visibility changes;
13. historical serving can still obey exceptional privacy redaction;
14. token-scoped access is resource-specific;
15. token revocation overrides cache;
16. token expiry fails closed;
17. Admin link requires authentication;
18. storage locators never become Public DTO;
19. private attachments require authorized delivery;
20. Public Search cannot contain Builder/private-only data;
21. stale Search cannot grant access;
22. Admin Search remains separate;
23. AI contexts are capability-specific;
24. AI receives only minimum necessary data;
25. Theme AI receives no unrelated professional/private source;
26. BB receives task-specific context;
27. Casting AI cannot read unrelated Castings;
28. external Notifications use minimized payload;
29. Analytics omits private source content;
30. Logs omit secrets/private content by default;
31. background jobs carry minimal references;
32. cache namespaces preserve visibility contexts;
33. permission revocation causes privacy-first invalidation;
34. archived entities disappear from current public contexts;
35. migration defaults are conservative;
36. professional Contacts remain separate from system recipients;
37. Public Builder session is not an authorization escalation mechanism;
38. AI Drafts remain Admin-only before Apply/Publish;
39. Audit/Outbox/Jobs are never Public;
40. data exposure can be traced from Source through Policy to Projection.

---

# 312. Финальная доктрина

> **Visibility в системе является отдельным серверным policy layer между Source of Truth и любым consumer. Профессиональный факт может существовать один раз, но иметь разные разрешения для Public Site, подготовленной анкеты и Public Builder. Private operational data может быть доступно Admin, но не Search, Analytics или AI автоматически. Historical Revision сохраняет точное прошлое состояние, однако access/redaction policy по-прежнему контролирует её выдачу. Любой Public, Builder, token-scoped, AI или external-channel output должен создаваться только из заранее разрешённой Projection, а не фильтроваться после отправки полного объекта клиенту.**