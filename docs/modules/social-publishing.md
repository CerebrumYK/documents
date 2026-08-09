# SOCIAL PUBLISHING MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация Instagram/TikTok publishing domain, OAuth accounts, SocialPostDraft, media eligibility, scheduling, PublishAttempt, provider reconciliation, token lifecycle, webhooks и Human-controlled publication

**Целевой файл:** `docs/modules/social-publishing.md`  
**Документ:** DOC-133  
**Статус:** ✅ Completed  
**Тип:** Module / Social Publishing / OAuth / Scheduling / External Provider / Human-Controlled Publication

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
- `docs/architecture/background-jobs.md`
- `docs/architecture/cache-strategy.md`
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
- `docs/modules/media-library.md`
- `docs/modules/portfolio.md`
- `docs/modules/projects.md`
- `docs/modules/professional-media-links.md`
- `docs/modules/contacts.md`
- `docs/modules/bb-assistant.md`
- `docs/modules/notifications.md`
- `docs/modules/virtual-operator.md`
- `docs/modules/marketing-analytics.md`

---

# 1. Назначение модуля

Social Publishing Module отвечает за управляемую публикацию профессионального контента актрисы во внешние социальные платформы.

Baseline platforms:

```text
INSTAGRAM
TIKTOK
```

Архитектура должна допускать добавление других provider adapters позже без изменения core domain semantics.

Модуль отвечает за:

- подключение social accounts через OAuth;
- безопасное хранение provider credentials/tokens;
- SocialPostDraft;
- выбор допустимых media;
- platform-specific validation;
- caption/text;
- связь с BB Assistant;
- explicit scheduling;
- explicit publish;
- provider publishing attempts;
- reconciliation;
- token refresh;
- token revocation;
- provider webhooks/status callbacks;
- retry/idempotency;
- публикационную историю;
- audit;
- notifications;
- analytics metadata.

---

# 2. Главная доктрина

> **Social Publishing является отдельным outbound publication domain. Draft, AI-generated caption, Human Apply и Schedule сами по себе не публикуют контент. Публикация во внешний provider допускается только после explicit Human approval либо исполнения заранее явно подтверждённого расписания конкретной immutable publish configuration. AI, VOP, Analytics, Theme и background worker не имеют права самостоятельно придумывать или расширять публикационное намерение.**

Canonical:

```text
Professional Source
      +
Eligible Media
      +
Human-authored / BB-assisted Caption
      ↓
SocialPostDraft
      ↓
Validation
      ↓
Human Approval
      ↓
┌─────────────────────┐
│ Publish Now         │
│ or Schedule         │
└─────────┬───────────┘
          ↓
Immutable PublishSnapshot
          ↓
PublishAttempt
          ↓
Provider API
          ↓
Reconciliation
          ↓
PUBLISHED / FAILED / UNKNOWN
```

---

# 3. Fundamental separation

```text
SocialAccount
≠ OAuthCredential
≠ SocialPostDraft
≠ PublishSnapshot
≠ PublishAttempt
≠ ProviderPost
≠ BB Draft
≠ Analytics Event
```

---

# 4. Critical action separation

```text
Generate Caption
≠ Apply Caption
≠ Approve Post
≠ Schedule Post
≠ Publish Post
```

---

# 5. Module identifiers

Используются:

```text
SOC-*
SOC-ACC-*
SOC-OAUTH-*
SOC-DRF-*
SOC-MED-*
SOC-CAP-*
SOC-VAL-*
SOC-SCH-*
SOC-PUB-*
SOC-ATT-*
SOC-WHK-*
SOC-REC-*
SOC-TOK-*
SOC-SEC-*
SOC-INV-*
SOC-AP-*
E2E-SOC-*
```

---

# 6. Supported providers

Canonical provider identifiers:

```text
INSTAGRAM
TIKTOK
```

Не использовать business logic, завязанную на:

```text
if instagram ...
else if tiktok ...
```

по всему приложению.

---

# 7. Provider Adapter

Каждая платформа реализуется через provider-specific adapter поверх общего Social Publishing contract.

---

# 8. SOC-INV-001 — Provider Differences Stay Behind Adapter

---

# 9. Social Account

Основная сущность:

```text
SocialAccount
```

Suggested table:

```text
social_accounts
```

---

# 10. Representative fields

```text
id
profile_id
provider
provider_account_id
display_name?
username?
account_type?
status
credential_reference
capabilities
connected_by
connected_at
last_verified_at?
token_expires_at?
revoked_at?
version
created_at
updated_at
```

---

# 11. Account statuses

Canonical:

```text
CONNECTED
REAUTH_REQUIRED
REVOKED
DISABLED
ERROR
```

---

# 12. SOC-ACC-001 — Connected Account Is Not ContactEntry

Instagram/TikTok account used for publishing credentials remains separate from public professional social ContactMethod.

---

# 13. Public social link boundary

A public Instagram profile link may belong to Contacts or ProfessionalLinks.

OAuth authorization belongs Social Publishing.

---

# 14. SOC-INV-002 — Public Identity ≠ OAuth Credential

---

# 15. One provider account identity

Store stable provider account ID when available.

Username/display name may change.

---

# 16. SOC-ACC-002 — Provider Account ID Is Preferred Identity

---

# 17. OAuth connection flow

Canonical:

```text
Admin
  ↓
Connect Instagram/TikTok
  ↓
Server creates OAuth state + PKCE where supported
  ↓
Provider Authorization
  ↓
Callback
  ↓
State verification
  ↓
Token exchange
  ↓
Account identity validation
  ↓
Encrypted credential storage
  ↓
SocialAccount CONNECTED
```

---

# 18. SOC-OAUTH-001 — OAuth State Validation Is Mandatory

---

# 19. PKCE

Use when provider flow supports/requires it.

---

# 20. Redirect URI

Must be fixed application-controlled allowlisted URI.

---

# 21. SOC-OAUTH-002 — Client Cannot Supply Arbitrary OAuth Redirect URI

---

# 22. Credentials

Access/refresh tokens are secrets.

---

# 23. They MUST NOT appear in:

- browser storage;
- analytics;
- public API;
- URLs;
- logs;
- filenames;
- notifications;
- screenshots generated by system.

---

# 24. SOC-SEC-001 — OAuth Tokens Are Secret Material

---

# 25. Credential storage

Use encrypted secret storage/database encryption appropriate to deployment.

At rest, provider token must not be plain application-readable dump outside controlled service boundary.

---

# 26. SOC-OAUTH-003 — Credential Access Is Narrowly Scoped

---

# 27. Token lifecycle

Supported states:

```text
VALID
EXPIRING
EXPIRED
REFRESH_FAILED
REVOKED
UNKNOWN
```

---

# 28. Refresh

Provider adapter may refresh token when supported.

---

# 29. SOC-TOK-001 — Token Refresh Is Technical, Not New Publishing Consent

Refreshing an already authorized connection does not authorize new content publication beyond existing workflow.

---

# 30. Refresh jobs

Can be scheduled before expiry.

---

# 31. Failed refresh

Set account to:

```text
REAUTH_REQUIRED
```

when appropriate.

---

# 32. SOC-TOK-002 — Failed Refresh Cannot Be Ignored Silently

---

# 33. Revocation

User can disconnect account.

Command:

```text
DisconnectSocialAccount
```

---

# 34. Disconnect flow

```text
revoke provider token where supported
↓
mark credential unusable
↓
SocialAccount REVOKED
↓
cancel/block future scheduled unpublished posts
```

---

# 35. SOC-TOK-003 — Account Revocation Blocks Future Publication

---

# 36. Existing externally published posts

Remain external provider records.

Disconnecting local OAuth does not delete provider posts automatically.

---

# 37. SOC-INV-003 — Disconnect ≠ Delete Published Social Content

---

# 38. SocialPostDraft

Primary mutable publication planning object.

Suggested table:

```text
social_post_drafts
```

---

# 39. Representative fields

```text
id
profile_id
provider
social_account_id
post_type
caption
locale?
selected_media
provider_options
status
version
created_by
created_at
updated_at
```

---

# 40. Draft status

Canonical:

```text
DRAFT
READY
APPROVED
SCHEDULED
PUBLISHING
PUBLISHED
FAILED
CANCELLED
```

---

# 41. Important nuance

`PUBLISHED` on Draft is a convenience lifecycle/projection.

Authoritative provider publication outcome is represented through PublishSnapshot + PublishAttempt + ProviderPost identity.

---

# 42. SOC-DRF-001 — Draft Is Mutable Until Publish Snapshot Freezes It

---

# 43. Post types

Normalized domain MAY include:

```text
IMAGE_POST
CAROUSEL
VIDEO_POST
SHORT_VIDEO
```

Provider adapter maps to supported platform types.

---

# 44. Platform-specific names

Examples like Reel/TikTok Video may be adapter/UI labels.

---

# 45. SOC-INV-004 — Provider Marketing Names Do Not Define Core Domain

---

# 46. Media selection

SocialPostDraft references existing MediaAsset or approved derivative.

---

# 47. It does not duplicate binary bytes.

---

# 48. SOC-MED-001 — Social Draft References Media, Does Not Own Original

---

# 49. Eligible media source classes

Potentially:

```text
MediaAsset
Project-related approved media
Portfolio media
explicit social-only managed media
```

subject to configured publication eligibility.

---

# 50. Social publication eligibility

Must be independent from:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

because social publishing is a separate outbound surface.

---

# 51. Recommended explicit permission

Where per-media restriction is needed:

```text
allow_social_publishing
```

or equivalent policy projection.

---

# 52. SOC-MED-002 — Public Site Visibility Does Not Automatically Grant Social Publishing Rights

---

# 53. Why

A photo may be allowed on the website but not licensed/approved for third-party social distribution.

---

# 54. Rights metadata

Where media rights restrictions exist, Social validation must consume them.

Examples:

```text
distribution restrictions
embargo date
project publicity restriction
expiration
platform restriction
```

---

# 55. SOC-MED-003 — Publishing Must Respect Media Rights Policy

---

# 56. Unknown rights

If publication authorization is unclear:

do not assume permission.

---

# 57. SOC-MED-004 — Unknown Rights ≠ Allowed

---

# 58. Media original

Do not send oversized/raw original blindly if provider-specific derivative can preserve intended content with lower risk/cost.

---

# 59. Provider derivative

Media pipeline MAY produce:

```text
SOCIAL_INSTAGRAM_IMAGE
SOCIAL_INSTAGRAM_VIDEO
SOCIAL_TIKTOK_VIDEO
```

or generic profiles.

---

# 60. SOC-MED-005 — Provider Derivatives Are Derived Artifacts

---

# 61. Source image appearance

Social processing MAY:

- resize;
- transcode;
- normalize codec;
- letterbox/pad;
- generate thumbnail/cover;

but MUST NOT:

- retouch actor appearance;
- reshape;
- beauty-filter;
- generatively alter face/body;
- crop semantically protected image without Human-approved framing.

---

# 62. SOC-MED-006 — Social Processing Cannot Alter Actress Appearance

---

# 63. Crop handling

Provider-specific aspect ratio may require crop.

If crop can affect meaningful composition:

Human review/approved crop configuration required.

---

# 64. SOC-MED-007 — Destructive Social Crop Requires Human Control

---

# 65. Immutable original remains unchanged.

---

# 66. Caption

Caption is Draft content.

Source may be:

```text
Human-written
BB Assistant AIDraft applied
Imported existing approved copy
```

---

# 67. Caption provenance

Recommended fields:

```text
caption_origin
bb_draft_id?
bb_draft_revision?
```

---

# 68. SOC-CAP-001 — BB Caption Apply ≠ Social Publication

---

# 69. BB boundary

BB Assistant may draft:

- social caption;
- hashtags;
- announcement copy.

It cannot:

- choose account without Human context;
- schedule;
- publish;
- change provider options;
- submit media.

---

# 70. SOC-CAP-002 — Writing Authority Is Separate From Publishing Authority

---

# 71. Hashtags

Stored as part of caption or structured provider-safe field.

---

# 72. Mention/tagging

If supported:

must distinguish plain text mention from provider entity tagging requiring API-specific identifiers.

---

# 73. SOC-CAP-003 — Text `@name` ≠ Verified Provider Tag

---

# 74. External account tagging

Never infer person/account identity automatically from arbitrary text.

---

# 75. SOC-SEC-002 — Provider Tag Identity Requires Explicit Validated Selection

---

# 76. Location tagging

Not baseline unless explicitly implemented.

Precise location requires intentional Human selection.

---

# 77. SOC-SEC-003 — No Automatic Precise Location Publishing

---

# 78. Caption validation

Checks may include:

```text
length
encoding
forbidden control characters
provider restrictions
link semantics
hashtag limits
mention format
```

---

# 79. SOC-VAL-001 — Validation Uses Current Provider Capability Contract

---

# 80. Provider limits

Must be adapter/config driven, not scattered constants.

---

# 81. SocialDraft media validation

Checks:

- media exists;
- same profile/context;
- media active;
- allow_social_publishing;
- rights;
- derivative readiness;
- provider type/size/duration constraints;
- supported aspect ratio;
- safe content type.

---

# 82. SOC-VAL-002 — Publish Reloads/Revalidates Media Server-Side

---

# 83. Browser cannot declare:

```text
media_ready=true
rights_ok=true
```

as authority.

---

# 84. Draft READY

Means current validation passed.

---

# 85. READY may become stale

If:

- Media archived;
- rights revoked;
- token expired;
- provider capability changed;
- account disconnected.

---

# 86. SOC-DRF-002 — READY Is Revalidated Before Approval/Publish

---

# 87. Approval

Explicit Human step:

```text
ApproveSocialPostDraft
```

---

# 88. Preconditions

At minimum:

```text
authorized Admin
current Draft version
connected account
valid media
valid caption/options
current rights
```

---

# 89. SOC-PUB-001 — Approval Is Human-Controlled

---

# 90. Approval does not publish.

---

# 91. SOC-PUB-002 — APPROVED ≠ PUBLISHED

---

# 92. Approval snapshot

To avoid approval drift, approved content SHOULD bind to a content fingerprint.

Example:

```text
approved_content_fingerprint
```

including:

```text
caption
media refs/versions
provider options
account
post type
```

---

# 93. Editing approved Draft

Invalidates approval.

---

# 94. SOC-PUB-003 — Material Draft Edit Revokes Prior Approval

---

# 95. Publish Now

Canonical command:

```text
PublishSocialPostNow
```

---

# 96. Publish Now requires current approved configuration or explicit approval within same high-impact command flow.

---

# 97. No hidden “auto approval”.

---

# 98. SOC-PUB-004 — Publish Intent Is Explicit

---

# 99. Schedule

Canonical command:

```text
ScheduleSocialPost
```

---

# 100. Scheduling requires:

- explicit exact future local datetime;
- timezone;
- approved content;
- selected account;
- provider;
- current validation.

---

# 101. SOC-SCH-001 — Schedule Is Explicit Human Publishing Intent

---

# 102. Scheduled time

Persist as unambiguous instant plus intended timezone context where useful:

```text
scheduled_at
scheduled_timezone
```

---

# 103. SOC-SCH-002 — No Ambiguous Server-Time Scheduling

---

# 104. Daylight saving

Schedule resolver must handle timezone transitions deterministically.

---

# 105. Scheduled post

Must freeze exactly what was approved.

---

# 106. PublishSnapshot

Critical immutable entity:

```text
SocialPublishSnapshot
```

Suggested table:

```text
social_publish_snapshots
```

---

# 107. Fields conceptually:

```text
id
social_post_draft_id
profile_id
provider
social_account_id
post_type
caption
media_snapshot
provider_options
scheduled_at?
approval_actor
approved_at
schema_version
content_fingerprint
created_at
```

---

# 108. SOC-SCH-003 — Scheduled Worker Publishes Snapshot, Not Mutable Draft

---

# 109. Why

If Admin edits Draft after scheduling:

previous scheduled publish must not silently use new unapproved text/media.

---

# 110. Editing scheduled Draft

Should require one of:

```text
Cancel existing schedule
Create updated approval/snapshot
Reschedule
```

---

# 111. SOC-SCH-004 — Mutable Draft Cannot Modify Existing Scheduled Snapshot

---

# 112. Cancel schedule

Command:

```text
CancelScheduledSocialPost
```

---

# 113. Must be idempotent.

---

# 114. Cancellation race

If provider publication already started:

system must reconcile actual outcome.

Cannot guarantee cancel after external provider accepted request.

---

# 115. SOC-SCH-005 — Cancel Is Best-Effort Once Publish Starts

---

# 116. PublishAttempt

Every outbound publication try is represented explicitly:

```text
SocialPublishAttempt
```

---

# 117. Suggested table:

```text
social_publish_attempts
```

---

# 118. Representative fields

```text
id
publish_snapshot_id
attempt_number
provider
provider_account_id
idempotency_key
status
started_at
completed_at?
provider_request_id?
provider_post_id?
provider_status?
error_class?
error_code?
reconciliation_state
```

---

# 119. Attempt statuses

Canonical:

```text
REQUESTED
SENDING
ACCEPTED
PUBLISHED
FAILED_RETRYABLE
FAILED_FINAL
UNKNOWN_OUTCOME
CANCELLED_BEFORE_SEND
```

---

# 120. SOC-ATT-001 — Provider Attempt Is Immutable Historical Record

---

# 121. ProviderPost

If provider returns stable external post identity:

```text
provider_post_id
provider_permalink?
published_at
```

---

# 122. Permalink

May be stored as provider response data if safe/stable.

---

# 123. SOC-PUB-005 — ProviderPost Identity Is Not Inferred From Username/Time

---

# 124. Idempotency

Critical because external POST may have uncertain outcome.

---

# 125. Use provider-native idempotency mechanism when available.

Also use internal semantic idempotency.

---

# 126. Internal key binds:

```text
publish_snapshot_id
publishing intent
```

---

# 127. SOC-PUB-006 — Same Snapshot Is Not Published Twice Accidentally

---

# 128. Unknown outcome

Example:

```text
request sent
connection timed out
provider may have accepted it
```

System state:

```text
UNKNOWN_OUTCOME
```

---

# 129. Do NOT immediately retry blindly.

---

# 130. SOC-REC-001 — Unknown Outcome Requires Reconciliation Before Retry

---

# 131. Reconciliation

Methods may include:

- provider request status endpoint;
- provider media container status;
- webhook;
- provider account recent posts lookup using stable operation identifiers where supported.

---

# 132. SOC-REC-002 — Reconciliation Uses Provider-Supported Evidence

---

# 133. Never decide:

```text
timeout = failed
```

without provider semantics.

---

# 134. Retryable failure

Examples:

- temporary provider 5xx;
- rate limit with known not-accepted request;
- temporary network failure before send;
- explicitly retryable processing state.

---

# 135. Final failure

Examples:

- revoked permission;
- invalid media;
- unsupported format;
- account blocked;
- content violates provider validation;
- expired non-refreshable token.

---

# 136. SOC-REC-003 — Retry Classification Is Provider-Aware

---

# 137. Retry limits

Bounded.

No infinite retry loop.

---

# 138. Scheduled worker

For due snapshot:

```text
load snapshot
↓
verify schedule still active
↓
verify account credential state
↓
revalidate external-safe prerequisites
↓
create attempt
↓
provider call
↓
reconcile
```

---

# 139. Important

Do not re-read mutable caption/media selection from Draft.

---

# 140. SOC-SCH-006 — Snapshot Content Is Stable at Send Time

---

# 141. Rights revocation after scheduling

If Media distribution rights are revoked before send:

scheduled publication MUST block.

---

# 142. SOC-MED-008 — Current Security/Rights Revocation Beats Historical Approval

---

# 143. Distinction

```text
snapshot semantic immutability
≠ unconditional right to publish forever
```

---

# 144. Account revocation after scheduling

Blocks send.

---

# 145. Provider capability change

May make snapshot no longer publishable.

Attempt should fail/block with clear reason, not silently alter media/caption.

---

# 146. SOC-VAL-003 — Provider Changes Do Not Authorize Silent Content Mutation

---

# 147. Media reprocessing

Technical derivative can be regenerated if semantics/framing remain approved.

---

# 148. If provider now requires materially different crop/edit:

Human reapproval required.

---

# 149. SOC-MED-009 — Material Presentation Change Requires Reapproval

---

# 150. Provider asynchronous processing

Some platforms may return accepted/processing rather than immediately published.

---

# 151. System must support:

```text
ACCEPTED
→ provider processing
→ PUBLISHED / FAILED
```

---

# 152. SOC-PUB-007 — API Acceptance ≠ Published

---

# 153. Webhooks

Provider webhook/callback MAY update publication status.

---

# 154. Webhook requirements:

- signature verification where supported;
- event dedupe;
- provider account matching;
- event freshness;
- replay protection where possible;
- schema validation.

---

# 155. SOC-WHK-001 — Unsigned/Invalid Webhook Cannot Mutate Publication State

---

# 156. Webhook event identity

Store provider event ID where available.

---

# 157. Duplicate webhook

Must be idempotent.

---

# 158. SOC-WHK-002 — Webhook Processing Is Idempotent

---

# 159. Out-of-order webhook

Must not regress:

```text
PUBLISHED
```

to stale:

```text
PROCESSING
```

without valid provider semantics.

---

# 160. SOC-WHK-003 — Provider Events Are Monotonic/State-Validated

---

# 161. Webhook does not create publishing intent.

It only reconciles already known account/post/provider state.

---

# 162. SOC-WHK-004 — Webhook Cannot Create New Draft/Publication Intent

---

# 163. Provider polling

If webhook unavailable, background reconciliation may poll bounded pending attempts.

---

# 164. Polling stops after final state or bounded timeout/escalation.

---

# 165. SOC-REC-004 — Pending Reconciliation Is Bounded

---

# 166. Scheduling implementation

Use durable Postgres-backed background job/outbox infrastructure.

---

# 167. Do not rely on:

- browser tab timer;
- in-memory `setTimeout`;
- client device remaining online.

---

# 168. SOC-SCH-007 — Scheduling Is Server-Durable

---

# 169. Scheduler at-least-once

Publication worker must therefore be idempotent.

---

# 170. SOC-SCH-008 — Duplicate Job Delivery Cannot Duplicate Social Post

---

# 171. Schedule state

Possible entity:

```text
SocialPublishSchedule
```

or schedule fields on PublishSnapshot.

---

# 172. Recommended lifecycle:

```text
ACTIVE
CANCELLED
TRIGGERED
COMPLETED
BLOCKED
```

---

# 173. Past-due scheduler after downtime

On startup:

reconcile overdue scheduled posts.

---

# 174. Policy

Do not blindly publish hours/days late without configured tolerance.

---

# 175. Suggested:

```text
grace_period
```

After grace exceeded:

```text
BLOCKED_MISSED_WINDOW
```

and Human review.

---

# 176. SOC-SCH-009 — Missed Schedule Has Explicit Policy

---

# 177. Example

A premiere post scheduled 19:00 must not automatically publish next morning simply because server was offline.

---

# 178. Human reschedule/publish-now can resolve.

---

# 179. Provider timezone

Application schedule uses user's configured timezone, provider call at absolute resolved instant.

---

# 180. Draft duplication

Admin may clone a previous post into new Draft.

---

# 181. Clone copies:

- intended caption;
- media references where still eligible;
- options.

---

# 182. It does not copy:

- publish state;
- provider post ID;
- previous schedule;
- previous attempt history.

---

# 183. SOC-DRF-003 — Clone Does Not Clone Publication History

---

# 184. Republishing same content

Allowed only as a new explicit Draft/approval/publish intent.

Not as retry of old successful PublishSnapshot.

---

# 185. SOC-PUB-008 — Repost ≠ Retry

---

# 186. Draft from Project/Media

UI may offer:

```text
Create Social Post
```

from a Project/Media context.

---

# 187. This creates SocialPostDraft referencing Source.

---

# 188. It does not change Project/Media public visibility.

---

# 189. SOC-DRF-004 — Social Draft Creation Has No Source Publication Side Effect

---

# 190. Project embargo

If Project has embargo/publication date:

Social validation must respect it.

---

# 191. SOC-MED-010 — Embargo Overrides Schedule

A schedule before embargo is blocked/rejected.

---

# 192. Copyright/rights

The platform must not assume rights solely because file exists in Media Library.

---

# 193. Rights validation may be:

- explicit media flag;
- Project publicity policy;
- manual Human attestation.

---

# 194. SOC-INV-005 — Possession of Media ≠ Distribution Rights

---

# 195. Alt/accessibility text

If provider supports alt text/accessibility description:

SocialPostDraft MAY include:

```text
alt_text
```

---

# 196. BB Assistant can draft alt text from confirmed visual/context information but Human review remains required.

---

# 197. SOC-CAP-004 — Accessibility Text Is Part of Draft

---

# 198. Video cover

If provider supports cover/thumbnail selection:

store explicit approved frame/derivative reference.

---

# 199. AI cannot choose actor appearance-defining cover automatically for final publication baseline.

May suggest only.

---

# 200. SOC-MED-011 — Final Cover Selection Is Human-Controlled

---

# 201. Music/audio selection

Provider-native music library selection is not baseline unless supported by provider API and rights model.

---

# 202. Do not automatically add copyrighted audio.

---

# 203. SOC-MED-012 — No Autonomous Music Addition

---

# 204. Comments/interactions

Inbound social comments/messages are out of baseline Social Publishing scope.

---

# 205. Module baseline is outbound publication, not social CRM.

---

# 206. SOC-INV-006 — Social Publishing ≠ Social Inbox

---

# 207. Deleting external post

Not baseline unless explicitly implemented.

---

# 208. If added:

must be explicit destructive Human action with provider reconciliation.

---

# 209. SOC-PUB-009 — Local Archive Does Not Delete Provider Post

---

# 210. Local archive

Published record may be archived locally for UI.

External post remains.

---

# 211. Notifications

Possible intents:

```text
SOCIAL_POST_SCHEDULED
SOCIAL_POST_PUBLISHED
SOCIAL_POST_FAILED
SOCIAL_ACCOUNT_REAUTH_REQUIRED
SOCIAL_SCHEDULE_MISSED
```

---

# 212. Notification uses minimal payload.

---

# 213. Do not include OAuth tokens/full provider response.

---

# 214. SOC-SEC-004 — Social Notifications Are Secret-Free

---

# 215. Provider failure never loses Draft/Snapshot.

---

# 216. SOC-INV-007 — External Provider Failure Does Not Destroy Publication Intent History

---

# 217. VOP

May detect:

```text
scheduled post blocked
account needs reauth
publish attempt unknown outcome
repeated provider failure
social derivative missing
missed schedule
```

---

# 218. VOP may safely:

- retry derivative generation;
- refresh safe technical projection;
- request reconciliation;
- retry known-safe failed technical job.

---

# 219. VOP cannot:

- approve post;
- change caption;
- change media;
- schedule;
- publish;
- repost.

---

# 220. SOC-INV-008 — VOP Has No Social Publishing Intent Authority

---

# 221. BB Assistant

May generate/edit Draft text.

No provider calls.

---

# 222. Analytics

May report:

```text
draft_created
schedule_created
publish_attempted
publish_succeeded
publish_failed
```

and inbound referral attribution if public links later generate website visits.

---

# 223. Analytics cannot trigger publication.

---

# 224. SOC-INV-009 — Analytics Cannot Auto-Publish High-Performing Content

---

# 225. Theme

Has no authority over external social content.

---

# 226. SOC-INV-010 — Site Theme Does Not Style/Mutate Provider Post Payload

---

# 227. AI general boundary

No AI role can:

```text
select account autonomously
approve post
schedule exact time autonomously
publish
delete provider post
change commercial/rights metadata
```

---

# 228. Recommendations

AI may recommend:

```text
suggested caption
suggested post timing
suggested media
```

as advisory only.

---

# 229. SOC-INV-011 — AI Recommendation ≠ Publishing Consent

---

# 230. Suggested posting time

Analytics/AI may recommend based on data.

Human must choose/approve actual schedule.

---

# 231. SOC-SCH-010 — Recommended Time ≠ Scheduled Time

---

# 232. Security scopes

OAuth request only minimum scopes needed for configured publishing capability.

---

# 233. SOC-OAUTH-004 — Least-Privilege Provider Scopes

---

# 234. Do not request inbox/comment/account-management scope if not needed.

---

# 235. Scope upgrade

If new feature requires additional scope:

explicit reconnect/reconsent flow where provider requires it.

---

# 236. SOC-OAUTH-005 — New Scope Is Not Silently Assumed

---

# 237. Account verification

Periodic technical check can verify:

- token valid;
- provider account accessible;
- required publishing scope present.

---

# 238. Does not publish.

---

# 239. SOC-ACC-003 — Health Check Is Read-Only

---

# 240. Provider rate limiting

Adapter should classify rate limit separately.

---

# 241. Retry after provider-specified interval if outcome known safe.

---

# 242. SOC-REC-005 — Provider Rate Limit Is Not Blind Immediate Retry

---

# 243. Provider API version

Record:

```text
provider_api_version
adapter_version
```

in attempt provenance where useful.

---

# 244. SOC-ATT-002 — Attempt Captures Integration Provenance

---

# 245. Provider response storage

Store minimum needed:

- external ID;
- processing status;
- normalized error code;
- request/correlation ID.

---

# 246. Avoid dumping raw provider response containing tokens/PII.

---

# 247. SOC-SEC-005 — Provider Payload Storage Is Minimized

---

# 248. Audit

Must include:

```text
Social account connected
Social account disconnected
Draft approved
Schedule created
Schedule changed/cancelled
Publish-now requested
Republish explicitly requested
Provider post delete if later supported
```

---

# 249. Provider status webhook/poll records belong operational history, not Human Audit event itself.

---

# 250. SOC-INV-012 — Provider Attempt History ≠ Human Audit

---

# 251. Outbox/domain events

Suggested:

```text
SocialAccountConnected
SocialAccountReauthRequired
SocialPostDraftCreated
SocialPostApproved
SocialPostScheduled
SocialPostScheduleCancelled
SocialPublishRequested
SocialPublishAccepted
SocialPostPublished
SocialPublishFailed
SocialPublishUnknownOutcome
```

---

# 252. Consumers

```text
Notifications
Analytics
VOP
Admin Dashboard
Observability
```

---

# 253. Search

Social drafts/history are Admin-only.

---

# 254. Public Search/SEO

Do not index:

- Draft;
- schedule;
- provider attempts;
- token/account details.

---

# 255. SOC-SEC-006 — Social Operational Data Is Admin-Private

---

# 256. Provider permalink

A published provider permalink MAY later be surfaced publicly only through explicit ProfessionalLink/Project relation.

---

# 257. It does not automatically become ProfessionalLink.

---

# 258. SOC-PUB-010 — Published Social Post ≠ Public Site Link Automatically

---

# 259. Why

Social posts can be temporary, contextual or unsuitable for permanent professional profile.

---

# 260. Create ProfessionalLink from published provider post

If desired:

explicit Human command.

---

# 261. SOC-PUB-011 — Cross-Domain Promotion Is Explicit

---

# 262. Draft privacy

Unpublished captions/media selections are private Admin data.

---

# 263. Draft preview

May use local provider-like preview.

Does not call provider unless necessary technical validation endpoint exists.

---

# 264. SOC-SEC-007 — Preview ≠ Provider Publication

---

# 265. Token revocation response

If provider signals revoked authorization:

- mark credential revoked/reauth required;
- block schedules;
- notify Admin;
- preserve Draft/Snapshot history.

---

# 266. SOC-TOK-004 — Credential Loss Does Not Delete Content Drafts

---

# 267. Credential rotation

If refresh rotates token:

atomic secret update.

Never keep old active token references unintentionally.

---

# 268. SOC-TOK-005 — Token Rotation Is Atomic

---

# 269. Multi-account future

Architecture should permit multiple accounts per provider/profile.

---

# 270. Draft explicitly selects one target SocialAccount.

---

# 271. SOC-ACC-004 — Account Selection Is Explicit

---

# 272. Cross-posting

Publishing same intended content to Instagram + TikTok should produce separate provider-specific snapshots/attempts.

---

# 273. Reason

Provider constraints/caption/media transformations differ.

---

# 274. SOC-PUB-012 — Cross-Platform Publish Is Not One Shared External Attempt

---

# 275. UI may offer:

```text
Prepare for Instagram
Prepare for TikTok
```

from same source seed.

---

# 276. Each gets separate validation/approval.

---

# 277. SOC-PUB-013 — Provider-Specific Approval Required

---

# 278. Batch scheduling

Can schedule multiple posts.

Each post remains independently approved/snapshotted/idempotent.

---

# 279. SOC-SCH-011 — Batch Operation Does Not Weaken Per-Post Validation

---

# 280. Scheduling conflicts

Optional warning if multiple posts scheduled too close.

Warning only unless explicit policy blocks.

---

# 281. Provider duplicate-content policy

May warn if same content recently published.

Does not assume provider rejection unless known.

---

# 282. SOC-VAL-004 — Duplicate Detection Is Advisory Unless Provider Contract Says Otherwise

---

# 283. Provider deletion/unavailability

If published post later removed externally:

reconciliation may mark:

```text
PROVIDER_POST_UNAVAILABLE
```

without rewriting historical “was published” fact.

---

# 284. SOC-REC-006 — Current Provider Availability ≠ Historical Publication Outcome

---

# 285. Publication history

Need distinguish:

```text
Published successfully at T
Later unavailable/deleted
```

---

# 286. SOC-PUB-014 — Historical Publish Success Is Immutable

---

# 287. Local lifecycle of ProviderPost

Possible:

```text
PUBLISHED
UNAVAILABLE
DELETED_EXTERNALLY
DELETED_BY_REQUEST
UNKNOWN
```

---

# 288. Retention

Separate classes:

```text
SocialAccount metadata
OAuth credentials
SocialPostDraft
PublishSnapshot
PublishAttempt
ProviderPost identity
Webhook events
provider response diagnostics
```

---

# 289. Credentials

Retain only while connection/operational need exists.

---

# 290. Disconnect should make token unusable immediately locally.

---

# 291. SOC-RET-001 — Credential Retention Is Shortest Necessary

---

# 292. Drafts

Can be retained for editorial history.

---

# 293. PublishSnapshots/Attempts

Longer retention useful for audit/reconciliation.

---

# 294. Raw webhook/provider payload

Shorter/minimized retention.

---

# 295. SOC-RET-002 — Raw Provider Payload Retention Is Bounded

---

# 296. Privacy deletion

Must locate:

- caption text;
- media references;
- account identifiers;
- provider IDs;
- webhook metadata;
- credentials.

---

# 297. Published external content

Local deletion cannot guarantee removal from provider or third-party copies.

---

# 298. SOC-SEC-008 — External Distribution Has Irreversible Limits

---

# 299. Delete credential vs delete history

Disconnect/revoke credential does not require deleting publication history.

---

# 300. Backup

Need preserve:

- Drafts;
- PublishSnapshots;
- Attempt history;
- provider post identity;
- account metadata.

---

# 301. Credential backup

If encrypted credentials are backed up, encryption key management must be handled securely.

---

# 302. SOC-SEC-009 — Backup Must Not Convert Encrypted OAuth Secret to Plaintext

---

# 303. Restore

Critical rules:

Do not automatically:

- publish overdue schedules;
- replay pending attempts;
- retry UNKNOWN_OUTCOME;
- reconnect revoked account;
- refresh all tokens before reconciliation;
- resend notifications.

---

# 304. SOC-INV-013 — Restore Does Not Replay External Publishing Side Effects

---

# 305. Restore reconciliation

For scheduled posts:

- inspect schedule time;
- apply missed-window policy;
- verify account/token;
- require Human decision if too late.

---

# 306. For in-flight attempt:

reconcile provider before any retry.

---

# 307. SOC-REC-007 — Restore Treats In-Flight Attempt as Potential Unknown Outcome

---

# 308. Migration

Existing Instagram/TikTok OAuth data must be classified.

---

# 309. Migration should identify:

```text
provider
account identity
token state
scopes
expiry
draft posts
scheduled posts
published provider IDs
```

where data exists.

---

# 310. Unknown token validity

Do not mark CONNECTED merely because token string exists.

---

# 311. SOC-MIG-001 — Migrated Credential Is Untrusted Until Verified

---

# 312. Existing scheduled jobs

Must not blindly resume until:

- snapshot existence;
- approval;
- target account;
- schedule time;
- idempotency state;

are verified.

---

# 313. SOC-MIG-002 — Legacy Schedule Requires Reconciliation

---

# 314. Existing published posts

If provider post ID known, import as historical provider identity.

---

# 315. If only caption/image exists but no provider evidence:

do not mark PUBLISHED.

---

# 316. SOC-MIG-003 — Local Draft Evidence ≠ Provider Publication Evidence

---

# 317. Legacy AI captions

Can be imported as Draft content with origin unknown/legacy.

Do not fabricate BB provenance.

---

# 318. SOC-MIG-004 — Legacy Caption Provenance Is Preserved Honestly

---

# 319. Migration idempotency

Required.

---

# 320. Admin UI structure

Recommended:

```text
Social Publishing
├── Accounts
├── Drafts
├── Scheduled
├── Publishing
├── Published
├── Failed / Needs Attention
└── History
```

---

# 321. Accounts page

Show:

```text
provider
account display name
connection state
scope/capability summary
token expiry/reauth state
last verified
```

Never display token.

---

# 322. SOC-UX-001 — Secret Is Never Rendered Back to Admin

---

# 323. Draft editor

Recommended:

```text
Account
Platform
Post type
Media
Crop/cover preview
Caption
Hashtags
Accessibility text
Provider options
Validation
Preview
Approve
Publish now / Schedule
```

---

# 324. Approval indicator

Clearly show:

```text
Draft changed since approval
```

when content fingerprint differs.

---

# 325. SOC-UX-002 — Approval Freshness Is Visible

---

# 326. Scheduled page

Show:

```text
scheduled local time
timezone
provider
account
snapshot summary
status
cancel/reschedule
```

---

# 327. Publish history

Show:

```text
published_at
provider post ID/link
snapshot
attempt count
final status
```

---

# 328. Unknown outcome

Must be visually distinct from failed.

---

# 329. SOC-UX-003 — UNKNOWN_OUTCOME ≠ FAILED

---

# 330. Reschedule

Should create updated approved snapshot rather than mutating already-triggered snapshot history.

---

# 331. SOC-SCH-012 — Reschedule Preserves Prior Schedule History

---

# 332. Error taxonomy

At minimum:

```text
SOCIAL_ACCOUNT_NOT_FOUND
SOCIAL_ACCOUNT_WRONG_PROFILE
SOCIAL_ACCOUNT_NOT_CONNECTED
SOCIAL_ACCOUNT_REAUTH_REQUIRED
SOCIAL_ACCOUNT_REVOKED
SOCIAL_PROVIDER_UNSUPPORTED
SOCIAL_SCOPE_MISSING
SOCIAL_DRAFT_NOT_FOUND
SOCIAL_DRAFT_STALE_VERSION
SOCIAL_DRAFT_NOT_READY
SOCIAL_DRAFT_NOT_APPROVED
SOCIAL_APPROVAL_STALE
SOCIAL_MEDIA_NOT_FOUND
SOCIAL_MEDIA_NOT_ELIGIBLE
SOCIAL_MEDIA_RIGHTS_BLOCKED
SOCIAL_MEDIA_NOT_READY
SOCIAL_MEDIA_FORMAT_UNSUPPORTED
SOCIAL_MEDIA_DURATION_INVALID
SOCIAL_MEDIA_ASPECT_INVALID
SOCIAL_CAPTION_INVALID
SOCIAL_PROVIDER_OPTION_INVALID
SOCIAL_SCHEDULE_IN_PAST
SOCIAL_SCHEDULE_INVALID_TIMEZONE
SOCIAL_SCHEDULE_MISSED_WINDOW
SOCIAL_SCHEDULE_CANCEL_CONFLICT
SOCIAL_PUBLISH_ALREADY_COMPLETED
SOCIAL_PUBLISH_IN_PROGRESS
SOCIAL_PUBLISH_UNKNOWN_OUTCOME
SOCIAL_PROVIDER_RATE_LIMITED
SOCIAL_PROVIDER_REJECTED
SOCIAL_PROVIDER_PROCESSING_FAILED
SOCIAL_WEBHOOK_INVALID
SOCIAL_IDEMPOTENCY_CONFLICT
```

---

# 333. API commands

Canonical:

```text
BeginSocialOAuthConnect
CompleteSocialOAuthConnect
VerifySocialAccount
DisconnectSocialAccount
CreateSocialPostDraft
CloneSocialPostDraft
UpdateSocialPostCaption
SelectSocialPostMedia
UpdateSocialProviderOptions
SetSocialAccessibilityText
ValidateSocialPostDraft
ApproveSocialPostDraft
RevokeSocialPostApproval
PublishSocialPostNow
ScheduleSocialPost
CancelScheduledSocialPost
RescheduleSocialPost
ReconcileSocialPublishAttempt
RetrySocialPublishAttempt
ArchiveSocialPostDraft
```

---

# 334. Internal/provider commands

```text
RefreshSocialToken
HandleSocialProviderWebhook
RunDueSocialSchedule
PollPendingSocialPublication
```

---

# 335. Queries

```text
ListSocialAccounts
GetSocialAccount
ListSocialPostDrafts
GetSocialPostDraft
GetSocialPostValidation
ListScheduledSocialPosts
GetSocialPublishSnapshot
GetSocialPublishAttempt
ListSocialPublishAttempts
ListPublishedSocialPosts
GetSocialPublishingHistory
```

---

# 336. Create Draft input

Conceptually:

```text
provider
socialAccountId
postType
sourceContext?
mediaIds?
caption?
locale?
```

---

# 337. Client cannot set

```text
published=true
provider_post_id
oauth token
rights_valid=true
approved_by
provider accepted=true
```

---

# 338. SOC-SEC-010 — Provider/Approval Authority Fields Are Server-Controlled

---

# 339. Approve input

```text
draftId
expectedVersion
idempotencyKey
```

Server computes fingerprint.

---

# 340. Schedule input

```text
draftId
expectedVersion
scheduledLocalDateTime
timezone
idempotencyKey
```

Server:

- validates;
- freezes snapshot;
- resolves instant;
- creates durable schedule.

---

# 341. Publish-now input

```text
draftId
expectedVersion
idempotencyKey
```

Server:

- validates approval;
- freezes/loads publish snapshot;
- creates attempt.

---

# 342. SOC-PUB-015 — Browser Never Calls Provider Directly

---

# 343. Webhook endpoint

Provider-facing public endpoint.

Must:

- authenticate/signature validate;
- rate limit;
- parse bounded body;
- avoid returning internal data.

---

# 344. SOC-SEC-011 — Webhook Route Is Write-Only/Reconciliation-Oriented

---

# 345. Search/analytics privacy

Provider account username may be operationally displayed in Admin.

OAuth credential/token never used as analytics/search field.

---

# 346. Metrics

Useful:

```text
scheduled count
publish success rate
provider processing time
reauth incidents
unknown-outcome count
retry count
missed schedule count
```

---

# 347. These measure publishing system reliability, not actress popularity.

---

# 348. SOC-INV-014 — Social Publishing Metrics Do Not Score Person

---

# 349. Anti-patterns

`SOC-AP-001`  
BB caption generation immediately posts to Instagram.

`SOC-AP-002`  
Applying BB Draft schedules automatically.

`SOC-AP-003`  
Scheduling does not require Human approval.

`SOC-AP-004`  
VOP publishes failed Draft automatically.

`SOC-AP-005`  
Analytics publishes “high-performing” media automatically.

`SOC-AP-006`  
Theme changes social caption.

`SOC-AP-007`  
OAuth token stored in localStorage.

`SOC-AP-008`  
OAuth token appears in URL query after callback.

`SOC-AP-009`  
OAuth state not verified.

`SOC-AP-010`  
Client supplies arbitrary OAuth redirect URI.

`SOC-AP-011`  
Requested provider scopes exceed required functionality.

`SOC-AP-012`  
Public Instagram URL treated as publishing credential.

`SOC-AP-013`  
ContactEntry stores OAuth token.

`SOC-AP-014`  
Provider username used as immutable account identity.

`SOC-AP-015`  
Website-public media automatically allowed for social publishing.

`SOC-AP-016`  
Unknown media rights treated as allowed.

`SOC-AP-017`  
Media original modified to fit TikTok/Instagram.

`SOC-AP-018`  
Beauty filter/AI retouch applied automatically.

`SOC-AP-019`  
Destructive crop chosen without Human review.

`SOC-AP-020`  
AI selects final social cover automatically.

`SOC-AP-021`  
Automatic copyrighted music added.

`SOC-AP-022`  
Provider constraints trusted from stale client data.

`SOC-AP-023`  
Draft marked READY once and never revalidated.

`SOC-AP-024`  
Editing approved caption leaves approval valid.

`SOC-AP-025`  
Draft edit silently changes scheduled post.

`SOC-AP-026`  
Scheduler publishes mutable Draft rather than immutable Snapshot.

`SOC-AP-027`  
Browser timer used for scheduled publication.

`SOC-AP-028`  
Server publishes missed post next day without missed-window policy.

`SOC-AP-029`  
Account revoked but schedule still executes.

`SOC-AP-030`  
Rights revoked after scheduling but post still publishes.

`SOC-AP-031`  
Provider API accepted request treated as immediately published.

`SOC-AP-032`  
Timeout treated as definitely failed.

`SOC-AP-033`  
Unknown-outcome request immediately retried.

`SOC-AP-034`  
Duplicate job delivery creates duplicate provider post.

`SOC-AP-035`  
Successful published post retried as same operation to repost.

`SOC-AP-036`  
Cross-platform publication shares one attempt record.

`SOC-AP-037`  
Instagram approval silently reused for TikTok transformed payload.

`SOC-AP-038`  
Provider raw response stored with access token.

`SOC-AP-039`  
Webhook signature ignored.

`SOC-AP-040`  
Duplicate webhook creates duplicate transitions.

`SOC-AP-041`  
Old webhook regresses PUBLISHED to PROCESSING.

`SOC-AP-042`  
Webhook creates a new publication request.

`SOC-AP-043`  
Infinite polling of unresolved provider status.

`SOC-AP-044`  
Disconnect deletes historical publish records.

`SOC-AP-045`  
Disconnect automatically deletes external posts.

`SOC-AP-046`  
Local archive deletes external provider post.

`SOC-AP-047`  
Published provider permalink automatically becomes Profile link.

`SOC-AP-048`  
Provider account credentials appear in Analytics.

`SOC-AP-049`  
Social drafts indexed publicly.

`SOC-AP-050`  
Cross-profile SocialAccount used for publishing.

`SOC-AP-051`  
Browser directly calls provider publish API.

`SOC-AP-052`  
Client sets `approved=true`.

`SOC-AP-053`  
Client sets `rights_ok=true`.

`SOC-AP-054`  
Retry uses new semantic payload under same idempotency key.

`SOC-AP-055`  
Restore publishes all overdue schedules immediately.

`SOC-AP-056`  
Restore retries UNKNOWN_OUTCOME attempts blindly.

`SOC-AP-057`  
Migrated token marked CONNECTED without verification.

`SOC-AP-058`  
Legacy local Draft imported as PUBLISHED without provider evidence.

`SOC-AP-059`  
AI recommended posting time becomes schedule automatically.

`SOC-AP-060`  
Social Publishing becomes autonomous marketing agent.

---

# 350. Core invariants

`SOC-INV-015`  
Social Publishing is an explicit outbound publication domain.

`SOC-INV-016`  
Instagram and TikTok use provider adapters behind a shared domain contract.

`SOC-INV-017`  
SocialAccount identity and OAuth credentials are distinct.

`SOC-INV-018`  
Public social Contact/ProfessionalLink does not grant publishing authorization.

`SOC-INV-019`  
OAuth state/redirect/scopes are server-validated and least-privilege.

`SOC-INV-020`  
OAuth tokens are encrypted/secret and never exposed to browser/logs/analytics/notifications.

`SOC-INV-021`  
Token refresh is technical credential maintenance, not publishing consent.

`SOC-INV-022`  
Disconnect/revocation blocks future publication but preserves history.

`SOC-INV-023`  
SocialPostDraft references Source media rather than duplicating originals.

`SOC-INV-024`  
Website visibility does not automatically grant social distribution permission.

`SOC-INV-025`  
Unknown distribution rights do not imply permission.

`SOC-INV-026`  
Social derivatives never mutate immutable media originals.

`SOC-INV-027`  
Automatic appearance retouch/generative modification is prohibited.

`SOC-INV-028`  
Destructive crop or material framing change requires Human control.

`SOC-INV-029`  
BB Assistant may draft caption but cannot publish/schedule.

`SOC-INV-030`  
Caption/hashtags/alt text remain Draft content until approval.

`SOC-INV-031`  
Provider tag identity requires explicit validated selection.

`SOC-INV-032`  
Precise location is never published automatically.

`SOC-INV-033`  
Provider limits/capabilities are validated server-side through adapters.

`SOC-INV-034`  
READY state is revalidated before approval/publish.

`SOC-INV-035`  
Approval is explicit Human action.

`SOC-INV-036`  
Approval does not publish.

`SOC-INV-037`  
Material Draft changes invalidate previous approval.

`SOC-INV-038`  
Publish Now is explicit Human publishing intent.

`SOC-INV-039`  
Schedule is explicit Human publishing intent with exact time/timezone.

`SOC-INV-040`  
Scheduled content is frozen into immutable PublishSnapshot.

`SOC-INV-041`  
Scheduled worker publishes Snapshot, never mutable Draft.

`SOC-INV-042`  
Editing Draft cannot silently modify scheduled Snapshot.

`SOC-INV-043`  
Schedule cancellation is idempotent and best-effort after send starts.

`SOC-INV-044`  
Every outbound provider call has immutable PublishAttempt history.

`SOC-INV-045`  
Provider acceptance is distinct from successful publication.

`SOC-INV-046`  
Provider post identity comes from provider evidence.

`SOC-INV-047`  
Publish operations are idempotent/deduplicated.

`SOC-INV-048`  
UNKNOWN_OUTCOME is distinct from FAILED.

`SOC-INV-049`  
Unknown external outcome requires reconciliation before retry.

`SOC-INV-050`  
Retry classification is provider-aware and bounded.

`SOC-INV-051`  
Provider asynchronous processing is modeled explicitly.

`SOC-INV-052`  
Webhook signatures/events are validated and deduplicated.

`SOC-INV-053`  
Out-of-order webhook cannot arbitrarily regress publication state.

`SOC-INV-054`  
Webhook does not create publication intent.

`SOC-INV-055`  
Scheduling runs on durable server-side jobs, not browser timers.

`SOC-INV-056`  
At-least-once scheduler delivery cannot duplicate provider posts.

`SOC-INV-057`  
Missed publishing windows use explicit grace/review policy.

`SOC-INV-058`  
Current rights/security/account revocation can block previously approved scheduled publication.

`SOC-INV-059`  
Provider capability change cannot silently mutate approved payload.

`SOC-INV-060`  
Repost is a new explicit publication intent, not retry.

`SOC-INV-061`  
Creating Social Draft from Project/Media does not change original domain visibility.

`SOC-INV-062`  
Embargo/publicity restrictions override schedule.

`SOC-INV-063`  
Possession of media is not proof of social distribution rights.

`SOC-INV-064`  
Final social cover/crop is Human-controlled where semantically material.

`SOC-INV-065`  
Social Publishing baseline is outbound-only, not Social Inbox/CRM.

`SOC-INV-066`  
Local archive/disconnect does not delete provider content.

`SOC-INV-067`  
Notifications/VOP/Analytics have no publishing intent authority.

`SOC-INV-068`  
AI recommendations do not become schedules/publications automatically.

`SOC-INV-069`  
Recommended posting time remains advisory until Human approval.

`SOC-INV-070`  
OAuth scopes remain least-privilege and new scope requires explicit consent flow.

`SOC-INV-071`  
Provider response storage is secret/data minimized.

`SOC-INV-072`  
Human Audit and provider attempt history remain distinct.

`SOC-INV-073`  
Social operational data is Admin-private.

`SOC-INV-074`  
Published provider permalink does not automatically become Public Profile Source.

`SOC-INV-075`  
Account token loss does not destroy Draft/Snapshot history.

`SOC-INV-076`  
Multiple provider accounts can be supported with explicit account selection.

`SOC-INV-077`  
Cross-platform publishing creates separate provider-specific validation/snapshots/attempts.

`SOC-INV-078`  
Batch scheduling retains per-post approval/validation/idempotency.

`SOC-INV-079`  
Historical successful publication remains historical fact even if provider content later disappears.

`SOC-INV-080`  
Credential retention is minimal and disconnected credentials become unusable.

`SOC-INV-081`  
Restore does not replay external publication side effects.

`SOC-INV-082`  
In-flight restored attempts are reconciled before retry.

`SOC-INV-083`  
Migrated credentials/schedules/publication states are verified rather than trusted blindly.

`SOC-INV-084`  
Legacy local content is never marked provider-published without provider evidence.

`SOC-INV-085`  
Client cannot set provider/approval/rights authority fields.

`SOC-INV-086`  
Browser never receives direct provider publishing authority.

`SOC-INV-087`  
All approval, scheduling, credentials, rights and external-side-effect rules are server-enforced.

---

# 351. E2E-SOC-001 — Connect Instagram

Admin starts OAuth.

Valid state/callback connects intended account.

Token remains server-side.

---

# 352. E2E-SOC-002 — OAuth state attack

Callback with invalid state.

Connection rejected.

---

# 353. E2E-SOC-003 — Redirect tampering

Client attempts attacker-controlled redirect URI.

Rejected.

---

# 354. E2E-SOC-004 — Token privacy

Connected account UI never returns access token.

---

# 355. E2E-SOC-005 — TikTok connect

TikTok provider adapter connects account under same domain contract.

---

# 356. E2E-SOC-006 — Refresh

Expiring refreshable token rotates successfully.

No publication occurs.

---

# 357. E2E-SOC-007 — Refresh failure

Account becomes REAUTH_REQUIRED where appropriate.

Future schedules blocked.

---

# 358. E2E-SOC-008 — Disconnect

Human disconnects account.

Future schedules cannot publish.

Existing publish history remains.

---

# 359. E2E-SOC-009 — Public link separation

Public Instagram ContactMethod remains visible even if publishing OAuth revoked, according to Contacts policy.

---

# 360. E2E-SOC-010 — Create Draft

Admin selects connected Instagram account and creates Draft.

Nothing published.

---

# 361. E2E-SOC-011 — BB caption

BB generates caption.

Social Draft unchanged until Human Apply.

---

# 362. E2E-SOC-012 — BB apply

Caption applied to SocialPostDraft.

No scheduling/provider request.

---

# 363. E2E-SOC-013 — Media reference

Draft references MediaAsset.

No duplicate binary copy becomes Source.

---

# 364. E2E-SOC-014 — Website-visible but social-blocked media

Media public on website but `allow_social_publishing=false`.

Social validation rejects it.

---

# 365. E2E-SOC-015 — Unknown rights

Rights unclear.

Publish blocked pending Human resolution.

---

# 366. E2E-SOC-016 — Social derivative

Provider derivative generated.

Original remains byte-identical.

---

# 367. E2E-SOC-017 — Beauty edit

Automatic appearance filter request is unavailable/rejected.

---

# 368. E2E-SOC-018 — Crop

Required destructive crop triggers Human review rather than automatic send.

---

# 369. E2E-SOC-019 — Provider validation

Unsupported video duration rejected before publish attempt.

---

# 370. E2E-SOC-020 — READY stale

Media archived after Draft became READY.

Approval/publish revalidation blocks.

---

# 371. E2E-SOC-021 — Approve

Human approves exact Draft content.

Fingerprint recorded.

No external publish yet.

---

# 372. E2E-SOC-022 — Edit after approval

Caption changes.

Prior approval invalidated.

---

# 373. E2E-SOC-023 — Publish now

Approved Draft published through explicit command.

PublishSnapshot/Attempt created.

---

# 374. E2E-SOC-024 — Client fake approval

Browser sends `approved=true`.

Ignored/rejected.

---

# 375. E2E-SOC-025 — Schedule

Human schedules exact future Warsaw/Almaty/local configured time with timezone.

Immutable Snapshot created.

---

# 376. E2E-SOC-026 — Draft edited after schedule

Scheduled Snapshot remains unchanged.

UI requires reschedule/new approval for new content.

---

# 377. E2E-SOC-027 — Durable scheduling

Browser closes.

Server still publishes at scheduled time.

---

# 378. E2E-SOC-028 — Duplicate scheduler delivery

Same due job runs twice.

One provider post only.

---

# 379. E2E-SOC-029 — Revoked account before schedule

Scheduled publish blocks and alerts Admin.

---

# 380. E2E-SOC-030 — Rights revoked before schedule

Scheduled publish blocks.

Snapshot remains historical.

---

# 381. E2E-SOC-031 — Embargo

Schedule earlier than confirmed embargo rejected/blocked.

---

# 382. E2E-SOC-032 — Server downtime short

Server resumes within configured grace.

Policy determines allowed delayed execution.

---

# 383. E2E-SOC-033 — Missed long window

Server resumes far after intended time.

Post not blindly published; state requires review.

---

# 384. E2E-SOC-034 — Provider accepted

Provider says processing.

State is ACCEPTED/processing, not PUBLISHED.

---

# 385. E2E-SOC-035 — Provider later published

Webhook/poll confirms.

Attempt becomes PUBLISHED with provider post ID.

---

# 386. E2E-SOC-036 — Timeout unknown

Network timeout after send.

State becomes UNKNOWN_OUTCOME.

No blind immediate retry.

---

# 387. E2E-SOC-037 — Reconciliation success

Provider evidence finds post.

Attempt becomes PUBLISHED without duplicate retry.

---

# 388. E2E-SOC-038 — Reconciliation confirms failure

Known-safe retry may proceed under policy.

---

# 389. E2E-SOC-039 — Rate limit

Provider rate limit classified.

Retry respects provider semantics/backoff.

---

# 390. E2E-SOC-040 — Webhook signature

Invalid webhook cannot alter attempt state.

---

# 391. E2E-SOC-041 — Duplicate webhook

Same provider event processed once logically.

---

# 392. E2E-SOC-042 — Out-of-order webhook

Old processing event after PUBLISHED does not regress state.

---

# 393. E2E-SOC-043 — Repost

Human wants same post again next month.

System creates new explicit Draft/Snapshot, not retry old successful attempt.

---

# 394. E2E-SOC-044 — Cross-platform

Same media prepared for Instagram and TikTok.

Each gets independent validation/approval/snapshot.

---

# 395. E2E-SOC-045 — TikTok failure

TikTok attempt fails.

Instagram success/history unaffected.

---

# 396. E2E-SOC-046 — Published permalink

External permalink stored.

Not automatically exposed on actor site.

---

# 397. E2E-SOC-047 — Promote permalink

Human explicitly creates approved ProfessionalLink if desired.

---

# 398. E2E-SOC-048 — VOP

VOP detects UNKNOWN_OUTCOME.

It requests reconciliation but cannot publish/repost.

---

# 399. E2E-SOC-049 — Analytics

Analytics shows post success/failure metadata.

Does not schedule similar content.

---

# 400. E2E-SOC-050 — AI time recommendation

AI recommends 19:30.

Nothing scheduled until Human chooses time.

---

# 401. E2E-SOC-051 — Secret logging

Provider access token absent from application logs/error notification.

---

# 402. E2E-SOC-052 — Provider raw payload

Stored normalized response excludes unnecessary secret fields.

---

# 403. E2E-SOC-053 — Restore scheduled

Backup restored after scheduled time.

Missed-window policy applies; no blind publish.

---

# 404. E2E-SOC-054 — Restore unknown attempt

In-flight external attempt reconciled before retry.

---

# 405. E2E-SOC-055 — Restore revoked account

Account remains revoked/reauth-required.

---

# 406. E2E-SOC-056 — Migration token

Legacy token exists.

Account stays unverified until provider verification succeeds.

---

# 407. E2E-SOC-057 — Migration published state

Legacy caption+image without provider ID/evidence is not marked PUBLISHED.

---

# 408. E2E-SOC-058 — Accessibility

Draft can store provider-supported alt text, and it is frozen in PublishSnapshot.

---

# 409. E2E-SOC-059 — Provider account mismatch

Draft/Profile A cannot publish through SocialAccount owned by Profile B.

Rejected.

---

# 410. E2E-SOC-060 — Full flow

Media selected  
→ rights/social eligibility validated  
→ BB caption Draft applied  
→ Human adjusts caption  
→ Draft validated  
→ Human approves  
→ Human schedules  
→ immutable PublishSnapshot  
→ durable worker executes  
→ provider accepts/processes  
→ verified webhook confirms provider post  
→ publication recorded and Analytics/Notification emitted.

At no stage do BB, AI, VOP, Analytics or browser obtain autonomous provider publishing authority.

---

# 411. Architecture diagram

```text
Professional Source
      │
      ├── MediaAsset
      └── Project context
             │
             ▼
      SocialPostDraft
        │        │
        │        └── BB caption provenance
        ▼
    Validation
        ▼
  Human Approval
        ▼
SocialPublishSnapshot
   immutable
        ▼
Schedule / Publish Now
        ▼
 SocialPublishAttempt
        ▼
  Provider Adapter
        ▼
Instagram / TikTok
        ▼
Webhook / Poll / Reconcile
```

---

# 412. OAuth security diagram

```text
Admin
  ↓
OAuth Start
  ↓
state + PKCE
  ↓
Provider
  ↓
Callback
  ↓
state verify
  ↓
token exchange
  ↓
encrypted credential store
  ↓
SocialAccount

Token never travels back to browser UI.
```

---

# 413. Approval/schedule diagram

```text
Draft v7
  ↓
Human Approve
  ↓
fingerprint F7

Draft changed to v8
  ↓
F7 invalid

Schedule requires current approval
  ↓
PublishSnapshot S8
  ↓
Worker publishes S8

Future Draft edits cannot alter S8.
```

---

# 414. Unknown outcome diagram

```text
Provider request sent
       ↓
connection timeout
       ↓
UNKNOWN_OUTCOME
       ↓
Reconcile
 ┌─────┼──────────┐
 ▼     ▼          ▼
Found Failed   Still Unknown
 │     │          │
 ▼     ▼          ▼
PUBLISHED  Safe retry?  Human/VOP attention
```

---

# 415. Scheduling diagram

```text
Human selects:
2026-08-15 19:00 Europe/Warsaw
          ↓
Timezone resolution
          ↓
Absolute instant
          ↓
Durable Postgres schedule
          ↓
At due time:
reload Snapshot
validate current rights/account
          ↓
PublishAttempt
```

---

# 416. Authority diagram

```text
BB Assistant
    └── caption Draft

Analytics
    └── performance insight

VOP
    └── operational finding

AI
    └── advisory suggestion

Human
    ├── approve
    ├── schedule
    └── publish

Only Human intent creates outbound publication authorization.
```

---

# 417. Provider lifecycle diagram

```text
REQUESTED
   ↓
SENDING
   ↓
 ┌───────────┬───────────────┐
 ▼           ▼               ▼
ACCEPTED   FAILED_*     UNKNOWN_OUTCOME
   ↓                           ↓
Provider processing          Reconcile
   ↓
PUBLISHED
```

---

# 418. Quality gate

Перед implementation должны быть подтверждены:

- [ ] provider adapter abstraction;
- [ ] Instagram adapter;
- [ ] TikTok adapter;
- [ ] SocialAccount model;
- [ ] account/profile ownership;
- [ ] OAuth state validation;
- [ ] PKCE where supported;
- [ ] fixed callback allowlist;
- [ ] least-privilege scopes;
- [ ] encrypted token storage;
- [ ] token refresh/rotation;
- [ ] disconnect/revocation;
- [ ] reauth-required lifecycle;
- [ ] SocialPostDraft model;
- [ ] provider-specific post-type validation;
- [ ] explicit media social eligibility;
- [ ] rights/embargo checks;
- [ ] provider derivatives;
- [ ] immutable original protection;
- [ ] Human crop/cover controls;
- [ ] caption provenance;
- [ ] BB Assistant integration;
- [ ] alt/accessibility text;
- [ ] Draft validation;
- [ ] approval fingerprint;
- [ ] approval invalidation on edit;
- [ ] explicit Publish Now;
- [ ] explicit Schedule;
- [ ] timezone-safe scheduling;
- [ ] immutable PublishSnapshot;
- [ ] durable scheduling jobs;
- [ ] missed-window policy;
- [ ] schedule cancellation;
- [ ] PublishAttempt model;
- [ ] provider post identity;
- [ ] provider-aware idempotency;
- [ ] UNKNOWN_OUTCOME state;
- [ ] reconciliation flow;
- [ ] bounded retry/backoff;
- [ ] asynchronous provider processing;
- [ ] signed/idempotent webhook handling;
- [ ] out-of-order webhook protection;
- [ ] polling fallback;
- [ ] account/rights revocation before send;
- [ ] no browser direct provider calls;
- [ ] Notifications integration;
- [ ] VOP operational-only boundary;
- [ ] Analytics read-only boundary;
- [ ] no AI autonomous scheduling/publishing;
- [ ] Admin-only privacy;
- [ ] retention/backup/restore;
- [ ] migration reconciliation;
- [ ] deterministic E2E coverage.

---

# 419. Acceptance criteria

`AC-SOC-001`  
Instagram/TikTok publishing реализуется через provider adapters поверх общего Social Publishing domain.

`AC-SOC-002`  
SocialAccount, public social link и OAuth credential являются разными сущностями/concerns.

`AC-SOC-003`  
OAuth flow использует state validation, fixed callback policy и least-privilege scopes.

`AC-SOC-004`  
OAuth tokens не доступны browser/UI/Analytics/Notifications и хранятся как секреты.

`AC-SOC-005`  
Token refresh/rotation является технической credential operation и не создаёт publishing intent.

`AC-SOC-006`  
Disconnect/revocation немедленно блокирует будущие публикации, но не удаляет историю/внешние posts.

`AC-SOC-007`  
SocialPostDraft является mutable Admin Draft и сам по себе ничего не публикует.

`AC-SOC-008`  
Social Draft references existing managed Media rather than duplicating Source binaries.

`AC-SOC-009`  
Website visibility не означает permission for social publishing.

`AC-SOC-010`  
Unknown media rights блокируют publication baseline.

`AC-SOC-011`  
Embargo/distribution restrictions валидируются до approval/send.

`AC-SOC-012`  
Social media processing никогда не меняет immutable original или внешность актрисы.

`AC-SOC-013`  
Destructive crop/material framing requires Human-controlled approval.

`AC-SOC-014`  
BB Assistant может создать Caption Draft, но не имеет schedule/publish authority.

`AC-SOC-015`  
Draft caption/media/provider options проходят server-side validation.

`AC-SOC-016`  
READY state revalidated перед approval и publication.

`AC-SOC-017`  
Approval является explicit Human action.

`AC-SOC-018`  
Approval не означает Publish.

`AC-SOC-019`  
Изменение approved content инвалидирует approval.

`AC-SOC-020`  
Publish Now является explicit Human publishing command.

`AC-SOC-021`  
Schedule является explicit Human command с точным datetime/timezone.

`AC-SOC-022`  
Scheduled content замораживается в immutable SocialPublishSnapshot.

`AC-SOC-023`  
Scheduled worker публикует Snapshot, а не mutable Draft.

`AC-SOC-024`  
Редактирование Draft после Schedule не изменяет scheduled payload.

`AC-SOC-025`  
Scheduling server-durable и не зависит от browser session/device uptime.

`AC-SOC-026`  
At-least-once schedule execution защищён idempotency от duplicate provider post.

`AC-SOC-027`  
Missed schedule использует explicit grace/review policy, а не blind delayed publish.

`AC-SOC-028`  
Revoked account/media rights before send block scheduled publication.

`AC-SOC-029`  
Provider API acceptance не считается final publication.

`AC-SOC-030`  
Каждый external call сохраняется как immutable PublishAttempt.

`AC-SOC-031`  
Provider post identity подтверждается provider evidence.

`AC-SOC-032`  
UNKNOWN_OUTCOME отличается от FAILED и запрещает blind retry.

`AC-SOC-033`  
Retry выполняется только после provider-aware classification/reconciliation и имеет bounded attempts.

`AC-SOC-034`  
Provider asynchronous processing поддерживается явно.

`AC-SOC-035`  
Provider webhook проходит signature/schema/dedupe validation.

`AC-SOC-036`  
Out-of-order webhook не может произвольно регрессировать confirmed state.

`AC-SOC-037`  
Webhook никогда не создаёт нового publishing intent.

`AC-SOC-038`  
Polling/reconciliation pending attempts ограничены по времени/частоте.

`AC-SOC-039`  
Repost является новым Human publication intent, а не retry успешной публикации.

`AC-SOC-040`  
Instagram и TikTok получают отдельные provider-specific snapshots/approvals/attempts.

`AC-SOC-041`  
Batch scheduling не ослабляет per-post validation/approval/idempotency.

`AC-SOC-042`  
VOP может только обнаруживать/reconcile technical problems и не публикует.

`AC-SOC-043`  
Analytics/AI могут рекомендовать media/time/copy, но никогда не schedule/publish автоматически.

`AC-SOC-044`  
Site Theme не имеет authority над external provider payload.

`AC-SOC-045`  
Browser никогда не получает direct provider publish credential/API authority.

`AC-SOC-046`  
Social operational drafts/schedules/attempts являются Admin-private.

`AC-SOC-047`  
Published provider permalink не становится ProfessionalLink автоматически.

`AC-SOC-048`  
Notifications содержат только secret-safe publication metadata.

`AC-SOC-049`  
Provider response/log storage минимизирован и не содержит OAuth secrets.

`AC-SOC-050`  
Historical publish success сохраняется даже если provider post позже недоступен.

`AC-SOC-051`  
Credential retention минимальна, а raw provider/webhook payload retention ограничена.

`AC-SOC-052`  
Backup/restore не replay external publication attempts/schedules blindly.

`AC-SOC-053`  
Restored in-flight/UNKNOWN_OUTCOME attempts reconcile provider state before retry.

`AC-SOC-054`  
Migrated credentials проверяются у provider до CONNECTED state.

`AC-SOC-055`  
Legacy Draft/local media не помечаются PUBLISHED без provider evidence.

`AC-SOC-056`  
Legacy caption не получает fabricated BB provenance.

`AC-SOC-057`  
Client не может установить approval, rights validity, provider result или publish state.

`AC-SOC-058`  
Все OAuth, rights, approval, scheduling, provider-side-effect, idempotency и reconciliation invariants имеют deterministic E2E coverage.

---

# 420. Финальная доктрина

> **Social Publishing Module является отдельным outbound publication domain для Instagram/TikTok и последующих provider adapters. OAuth account connection, professional public social identity и provider credentials строго разделены; токены являются секретами, используются server-side и обслуживаются через least-privilege OAuth/refresh/revocation workflows. Контент сначала существует как mutable `SocialPostDraft`, может получить BB-assisted caption, пройти Human crop/cover/media review и provider-specific validation, после чего только Human approval фиксирует publishing intent. Publish Now или Schedule создают immutable `SocialPublishSnapshot`; scheduled worker никогда не читает изменившийся Draft и не может публиковать контент, не входивший в approval. Каждая provider interaction фиксируется как `PublishAttempt`, `ACCEPTED` не равно `PUBLISHED`, а сетевой timeout может приводить к `UNKNOWN_OUTCOME`, который обязательно требует reconciliation до retry. Provider webhooks проверяются, deduplicate и не могут создавать новое publishing intent. Отзыв OAuth, rights/embargo или security access может остановить даже ранее approved schedule. BB, AI, VOP, Analytics и Theme могут только помогать с Draft/insight/technical health и никогда самостоятельно не schedule/publish/repost контент.**