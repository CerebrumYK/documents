# NOTIFICATIONS MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация Notification Center, routing policy, In-App / WhatsApp / Email deliveries, preferences, quiet hours, retries, idempotency и failure handling

**Целевой файл:** `docs/modules/notifications.md`  
**Документ:** DOC-125  
**Статус:** ✅ Completed  
**Тип:** Module / Notifications / Delivery / Admin Attention / Preferences / Routing

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
- `docs/modules/feedback.md`
- `docs/modules/castings.md`
- `docs/modules/casting-ai-analysis.md`
- `docs/modules/contacts.md`

---

# 1. Назначение модуля

Notifications Module отвечает за **доставку внимания**, а не за хранение бизнес-события.

Он обеспечивает:

- Notification Center в Admin;
- In-App notifications;
- WhatsApp Business notifications;
- Email notifications;
- recipient routing;
- notification preferences;
- quiet hours;
- delivery priority;
- retry/backoff;
- idempotency;
- provider callback/webhook processing;
- read/unread state;
- delivery diagnostics;
- suppression;
- escalation of failed delivery;
- protected Admin deep links.

---

# 2. Главная доктрина

> **Notification является производным operational representation уже совершившегося Business Event. Сначала Business Source фиксируется и COMMIT-ится. Только после этого создаются Notification Intent и channel-specific Delivery. Ошибка доставки никогда не откатывает Business Source.**

Canonical:

```text
Business Domain
     ↓
Source Commit + Audit + Outbox
     ↓
Notification Policy
     ↓
Notification Intent
     ↓
Notification
     ↓
Channel Deliveries
 ┌───────┼────────┐
 ▼       ▼        ▼
In-App WhatsApp  Email
     ↓
Provider Result
```

---

# 3. Fundamental separation

```text
Business Event
≠ Notification Intent
≠ Notification
≠ Notification Delivery
≠ Delivery Attempt
≠ Notification Read State
≠ Contact
≠ Notification Recipient
```

---

# 4. Module identifiers

Используются:

```text
NOT-*
NOT-INT-*
NOT-REC-*
NOT-ROU-*
NOT-PRF-*
NOT-DEL-*
NOT-ATT-*
NOT-READ-*
NOT-WA-*
NOT-EML-*
NOT-IAP-*
NOT-QH-*
NOT-WBH-*
NOT-SEC-*
NOT-INV-*
NOT-AP-*
E2E-NOT-*
```

---

# 5. Business purpose

Модуль должен гарантировать:

1. важное событие видно в Admin;
2. external notification channels повышают шанс реакции;
3. отказ provider не приводит к потере Business Source;
4. duplicate delivery не создаёт duplicate business effect;
5. private content не раскрывается через notification channel;
6. пользователь понимает delivery/read status;
7. notification routing можно изменять без изменения Business domains.

---

# 6. Notification Center

Notification Center является Admin-only projection.

Он должен отображать:

```text
Unread
All
High Priority
Feedback
Castings
Opportunities
System
Social
VOP
Failures
```

---

# 7. NOT-INV-001 — Notification Center Is Projection

Он не является источником:

- Feedback;
- Casting;
- Opportunity;
- Social Post;
- System state.

---

# 8. Notification Intent

`NotificationIntent` описывает:

> **какое внимание нужно привлечь, к кому, с каким приоритетом и по какой причине.**

---

# 9. Suggested entity

```text
notification_intents
```

---

# 10. Representative fields

```text
id
profile_id?
event_type
source_entity_type
source_entity_id
source_event_id?
priority
routing_policy_id?
status
dedupe_key
created_at
```

---

# 11. Intent does not contain full Business Source

Preferred payload:

```text
source reference
event type
minimal routing context
```

---

# 12. NOT-INV-002 — Intent Is Data-Minimized

---

# 13. Notification entity

Represents recipient-facing logical notification.

Suggested:

```text
notifications
```

---

# 14. Representative fields

```text
id
intent_id
recipient_id
title
summary
deep_link?
priority
read_state
created_at
read_at?
dismissed_at?
```

---

# 15. Notification title/summary

Must be concise.

Example:

```text
Новое приглашение на кастинг
```

not complete casting brief.

---

# 16. NOT-SEC-001 — Notification Summary Is Minimal

---

# 17. Recipient model

Notification recipients are operational entities/configurations.

Suggested:

```text
notification_recipients
```

---

# 18. Recipient types

Could include:

```text
ADMIN_USER
EXPLICIT_EMAIL
EXPLICIT_WHATSAPP
SYSTEM_ROLE
```

depending final implementation.

---

# 19. Critical boundary

```text
Public Contact
≠ Notification Recipient
```

---

# 20. NOT-REC-001 — No Automatic Public Contact Routing

Public phone/email must not automatically receive system notifications.

---

# 21. Reverse boundary

Notification recipient must not become Public Contact.

---

# 22. NOT-REC-002 — Operational Recipient Has No Public Exposure Authority

---

# 23. Admin user recipient

Can link to authenticated Admin user.

---

# 24. External recipient endpoint

If configured independently:

```text
email
WhatsApp business destination
```

stored server-side.

---

# 25. NOT-SEC-002 — Recipient Endpoints Are Private

Never serialized to Public UI.

---

# 26. Notification categories

Recommended controlled categories:

```text
FEEDBACK
CASTING
CASTING_AI
OPPORTUNITY
QUESTIONNAIRE
MEDIA
SOCIAL
VOP
SECURITY
SYSTEM
SUPPORT
```

---

# 27. Event types

Examples:

```text
FEEDBACK_SUBMITTED
CASTING_CREATED
CASTING_DEADLINE_APPROACHING
CASTING_AI_READY
OPPORTUNITY_STAGE_CHANGED
QUESTIONNAIRE_PDF_FAILED
SOCIAL_PUBLISH_FAILED
VOP_ACTION_REQUIRED
SYSTEM_BACKUP_FAILED
```

---

# 28. NOT-INV-003 — Event Type Is Stable Machine Contract

Do not drive logic from localized notification title.

---

# 29. Priority

Canonical:

```text
CRITICAL
HIGH
NORMAL
LOW
```

---

# 30. CRITICAL

Reserved for events requiring immediate attention.

Examples might include:

- security issue;
- serious operational failure;
- specifically configured urgent business event.

---

# 31. HIGH

Examples:

- new Casting Invitation;
- submission deadline near;
- important material delivery failure.

---

# 32. NORMAL

Most professional workflow events.

---

# 33. LOW

Informational/non-urgent operational events.

---

# 34. NOT-INV-004 — Priority Does Not Change Business Source

---

# 35. Priority authority

May come from deterministic Notification Policy.

AI does not set final priority baseline.

---

# 36. NOT-INV-005 — AI Recommendation Cannot Set Delivery Priority Automatically

---

# 37. Routing policy

Determines channels based on:

```text
event type
priority
recipient preferences
quiet hours
channel availability
security class
```

---

# 38. Suggested policy output

```text
IN_APP = required
WHATSAPP = yes/no
EMAIL = yes/no
```

---

# 39. In-App baseline

For important Admin events:

```text
IN_APP
```

should usually be canonical persistent channel.

---

# 40. NOT-IAP-001 — In-App Does Not Depend on External Provider

---

# 41. WhatsApp

External attention channel.

Must use:

> **official/approved WhatsApp Business integration only.**

---

# 42. NOT-WA-001 — No Personal WhatsApp Automation

---

# 43. WhatsApp provider credentials

Server secrets.

Never:

- jobs payload;
- browser;
- logs;
- notification entity.

---

# 44. NOT-SEC-003 — Provider Secrets Are Infrastructure Secrets

---

# 45. WhatsApp template rules

If provider requires approved message templates, application must comply.

---

# 46. Minimal WhatsApp content

Preferred:

```text
Новое профессиональное обращение: приглашение на кастинг.
Открыть: <protected Admin link>
```

---

# 47. Do not include by default:

- full casting text;
- private attachments;
- private phone/email;
- AI analysis;
- Admin notes.

---

# 48. NOT-WA-002 — WhatsApp Payload Is Minimal

---

# 49. Email

Can provide more structured content than WhatsApp, but still use data minimization.

---

# 50. Email baseline

Could include:

```text
event title
short summary
received/event time
protected Admin link
```

---

# 51. NOT-EML-001 — Email Is Notification, Not Backup Database

---

# 52. Email delivery failure

Business Source remains.

---

# 53. In-App notification

Stored in DB.

---

# 54. Read state

Canonical:

```text
UNREAD
READ
```

Optional:

```text
DISMISSED
```

as separate UI state.

---

# 55. NOT-READ-001 — Read State ≠ Delivery State

---

# 56. Example

WhatsApp delivered, In-App unread.

Valid.

---

# 57. Provider delivered

Does not imply Admin read.

---

# 58. NOT-READ-002 — Delivered ≠ Read

---

# 59. Notification read action

Must not change source workflow.

Example:

Reading:

```text
New Feedback
```

does not mark Feedback resolved.

---

# 60. NOT-INV-006 — Reading Notification Has No Business Side Effect

---

# 61. Deep links

Notification can contain Admin route.

---

# 62. Deep link example

```text
/admin/feedback/<id>
```

conceptually.

---

# 63. NOT-SEC-004 — Deep Link Is Navigation, Not Authorization

Opening it requires normal authentication and authorization.

---

# 64. Raw secret tokens

Never needed in ordinary Admin notification deep links.

---

# 65. Notification delivery

Each channel delivery is separate.

Suggested:

```text
notification_deliveries
```

---

# 66. Representative fields

```text
id
notification_id
channel
recipient_endpoint_id
status
provider_message_id?
scheduled_for?
accepted_at?
delivered_at?
failed_at?
last_error_code?
attempt_count
created_at
```

---

# 67. Canonical delivery states

```text
PENDING
DEFERRED_QUIET_HOURS
QUEUED
SENDING
ACCEPTED
SENT
DELIVERED
FAILED_RETRYABLE
FAILED_FINAL
SUPPRESSED
CANCELLED
```

---

# 68. NOT-DEL-001 — Delivery Status Is Channel-Specific

One Notification may have:

```text
In-App = available
WhatsApp = failed
Email = delivered
```

---

# 69. Provider accepted vs delivered

Must remain separate.

---

# 70. NOT-DEL-002 — Provider Accepted ≠ Delivered

---

# 71. `SENT`

Use only where semantics are defined.

Do not blur with delivered/read.

---

# 72. Delivery attempt

Every provider attempt stored separately.

Suggested:

```text
notification_delivery_attempts
```

---

# 73. Attempt fields

```text
id
delivery_id
attempt_number
started_at
finished_at?
status
provider_status?
error_class?
correlation_id?
```

---

# 74. Attempt states

```text
STARTED
SUCCEEDED
FAILED
TIMED_OUT
```

---

# 75. NOT-ATT-001 — Attempt History Is Append-Oriented

---

# 76. Retry

Uses new attempt on same logical Delivery.

---

# 77. NOT-DEL-003 — Retry ≠ New Notification

---

# 78. Idempotency

Critical for external channels.

---

# 79. Stable logical key

Each delivery has stable identity:

```text
delivery_id
```

used in provider idempotency where supported.

---

# 80. NOT-DEL-004 — Same Delivery Must Not Send Repeated Message on Retry

---

# 81. Provider lacking idempotency

System uses:

- local durable delivery state;
- reconciliation;
- provider message ID;
- careful timeout handling.

---

# 82. Ambiguous timeout

If request may have reached provider but response lost:

do not immediately blindly resend.

---

# 83. NOT-DEL-005 — Unknown Outcome Requires Reconciliation

---

# 84. Unknown outcome state

May use internal:

```text
UNKNOWN_OUTCOME
```

or equivalent processing metadata.

---

# 85. Retry policy

Finite.

---

# 86. Recommended principles

```text
exponential backoff
jitter
provider Retry-After respect
bounded attempts
```

---

# 87. NOT-DEL-006 — Infinite Retry Forbidden

---

# 88. Retryable failures

Examples:

- timeout;
- temporary provider 5xx;
- rate limiting;
- network failure.

---

# 89. Final failures

Examples:

- permanently invalid recipient;
- rejected template;
- revoked endpoint;
- non-retryable provider validation error.

---

# 90. Provider 429

Respect `Retry-After` when available.

---

# 91. NOT-DEL-007 — Provider Rate Limit Is Not Business Failure

---

# 92. Quiet hours

Notification preferences can define quiet period.

---

# 93. Suggested:

```text
quiet_hours_enabled
quiet_hours_start
quiet_hours_end
timezone
```

---

# 94. Timezone

Use explicit IANA timezone.

---

# 95. NOT-QH-001 — Quiet Hours Are Timezone-Aware

---

# 96. Priority behavior

Example policy:

```text
LOW/NORMAL → defer during quiet hours
HIGH → configurable
CRITICAL → may bypass if explicitly enabled
```

---

# 97. NOT-QH-002 — Bypass Requires Policy, Not Ad-Hoc Code

---

# 98. Deferred state

```text
DEFERRED_QUIET_HOURS
```

with `scheduled_for`.

---

# 99. In-App during quiet hours

May still be immediately persisted/unread, while external delivery deferred.

---

# 100. NOT-QH-003 — Quiet Hours Do Not Hide Event From Notification Center

---

# 101. Preferences

Notification preferences should be server-side.

Suggested:

```text
notification_preferences
```

---

# 102. Preference dimensions

Potential:

```text
recipient
event category
minimum priority
in_app_enabled
whatsapp_enabled
email_enabled
quiet hours
```

---

# 103. Event-specific overrides

MAY support:

```text
Feedback → WhatsApp + In-App
VOP Low → In-App only
System Critical → Email + WhatsApp + In-App
```

---

# 104. NOT-PRF-001 — Preferences Affect Delivery, Not Business Event Creation

---

# 105. Disabled external channel

Source event still creates In-App notification where policy requires.

---

# 106. Recipient endpoint validation

WhatsApp/email endpoints must be syntactically valid/configured.

---

# 107. Verification

Where provider requires/permits verification, store operational verification state.

---

# 108. NOT-REC-003 — Endpoint Syntax ≠ Verified Ownership

---

# 109. Recipient lifecycle

Suggested:

```text
ACTIVE
DISABLED
REVOKED
```

---

# 110. Disabled recipient

No new external deliveries.

---

# 111. Historical delivery records remain.

---

# 112. NOT-REC-004 — Disabling Recipient Does Not Rewrite Delivery History

---

# 113. Feedback events

At minimum:

```text
FEEDBACK_SUBMITTED
```

should produce Admin attention according to policy.

---

# 114. Default suggested routing

```text
In-App = yes
WhatsApp = high-value types / configured
Email = configured
```

---

# 115. Casting events

Possible:

```text
CASTING_CREATED
CASTING_DEADLINE_APPROACHING
CASTING_MATERIALS_INCOMPLETE
CASTING_SUBMITTED
```

---

# 116. Casting AI events

```text
CASTING_AI_COMPLETED
CASTING_AI_FAILED
CASTING_AI_STALE
```

---

# 117. Opportunity events

Examples:

```text
OPPORTUNITY_STAGE_CHANGED
OPPORTUNITY_NEXT_ACTION_DUE
OPPORTUNITY_OFFER_RECEIVED
```

according to later DOC-126.

---

# 118. Questionnaire events

Operational notifications may include:

```text
QUESTIONNAIRE_PDF_FAILED
QUESTIONNAIRE_PDF_READY
```

if configured.

---

# 119. Avoid notification noise

Routine successful technical events do not all need external notification.

---

# 120. NOT-ROU-001 — Routing Policy Prevents Notification Flood

---

# 121. Media events

Typical:

```text
MEDIA_PROCESSING_FAILED
```

Admin notification only when action required.

---

# 122. Social publishing events

Examples:

```text
SOCIAL_PUBLISH_FAILED
SOCIAL_AUTH_EXPIRED
SOCIAL_POST_PUBLISHED
```

according to Social module.

---

# 123. VOP notifications

VOP may create attention request for:

```text
missing data
stale questionnaire
broken link
failed media
overdue next action
```

---

# 124. Critical boundary

VOP notification should say:

```text
Action recommended
```

not imply business action already occurred.

---

# 125. NOT-INV-007 — VOP Notification Does Not Execute Recommendation

---

# 126. System events

Examples:

```text
BACKUP_FAILED
WORKER_FAILURE_THRESHOLD
STORAGE_LOW
MIGRATION_REQUIRED
INTEGRATION_AUTH_EXPIRED
```

---

# 127. Security events

Potential:

```text
SUSPICIOUS_LOGIN
MFA_EVENT
TOKEN_REVOKED
```

when security modules support them.

---

# 128. Security notification content

Must avoid leaking secrets.

---

# 129. NOT-SEC-005 — Security Notification Never Contains Credential/Token Material

---

# 130. Notification policy engine

Recommended central service:

```text
NotificationPolicyService
```

---

# 131. Inputs

```text
event type
source context
priority
recipient preferences
channel status
quiet hours
```

---

# 132. Output

```text
NotificationIntent
Recipient(s)
Delivery plan
```

---

# 133. NOT-INV-008 — Domains Emit Events, Not Channel-Specific Provider Calls

Feedback domain should not call WhatsApp SDK.

---

# 134. Correct:

```text
FeedbackSubmitted
→ Notification Policy
→ WhatsApp Delivery
```

---

# 135. Incorrect:

```text
FeedbackService.sendWhatsApp()
```

inside business transaction.

---

# 136. NOT-INV-009 — Domain Is Provider-Agnostic

---

# 137. Application commands

Possible:

```text
MarkNotificationRead
MarkNotificationUnread
DismissNotification
UpdateNotificationPreferences
EnableNotificationRecipient
DisableNotificationRecipient
RetryNotificationDelivery
CancelPendingNotificationDelivery
```

---

# 138. Admin manual retry

Allowed only for:

```text
FAILED_RETRYABLE
FAILED_FINAL
```

where policy permits.

---

# 139. Manual retry semantics

Creates new Attempt on same Delivery.

---

# 140. NOT-DEL-008 — Manual Retry Does Not Create Duplicate Logical Notification

---

# 141. Manual resend

If user intentionally wants a fresh message even after successful delivery:

that is **Resend**, not Retry.

---

# 142. NOT-DEL-009 — Resend ≠ Retry

---

# 143. Resend

Should create explicit new Delivery/intent or clearly related resend identity.

---

# 144. Why

Audit must distinguish:

```text
provider retry
```

from:

```text
Human intentionally sent again
```

---

# 145. Query contracts

Suggested:

```text
GetNotificationCenter
GetUnreadNotificationCount
GetNotification
GetNotificationDeliveryStatus
GetNotificationPreferences
GetNotificationRecipients
ListFailedNotificationDeliveries
```

---

# 146. Public API

Notifications are Admin-private.

---

# 147. NOT-SEC-006 — Public User Cannot Read Admin Notifications

---

# 148. Browser push

Not baseline unless separately added.

Do not assume push capability.

---

# 149. Real-time UI

Notification Center MAY use:

- polling;
- SSE;
- websocket;
- server refresh;

implementation choice.

---

# 150. NOT-IAP-002 — Real-Time Transport Does Not Change Notification Semantics

---

# 151. Unread count

Derived projection.

---

# 152. Count cache

Can lag briefly, but opening Notification Center must show correct current data.

---

# 153. NOT-READ-003 — Badge Count Is Not Source Authority

---

# 154. Mark all read

May be supported.

---

# 155. `MarkAllNotificationsRead`

must affect notification read state only.

---

# 156. NOT-READ-004 — Mark All Read Never Mutates Business Entities

---

# 157. Dismiss

UI-specific attention state.

---

# 158. Dismissed notification remains audit/delivery history.

---

# 159. NOT-READ-005 — Dismiss ≠ Delete

---

# 160. Delete Notification

Not baseline user action.

Retention policy handles old operational rows.

---

# 161. Notification grouping

UI MAY group multiple related events.

Example:

```text
3 новых обращения
```

---

# 162. But underlying individual Notifications remain.

---

# 163. NOT-IAP-003 — UI Grouping Does Not Merge Business Events

---

# 164. Deduplication

Notification intent may use stable dedupe key for repeated identical technical signals.

---

# 165. Example

A link checker runs hourly and detects same broken URL.

Do not create 24 identical external alerts unnecessarily.

---

# 166. NOT-ROU-002 — Notification Dedupe Is Event-Attention Dedupe, Not Business Record Dedupe

---

# 167. Deduplication policies

Could include:

```text
coalesce identical unresolved alert
cooldown period
latest state update
```

for technical operational events.

---

# 168. Business events

New Feedback items must remain independently represented.

Do not dedupe distinct incoming messages.

---

# 169. NOT-ROU-003 — Distinct Business Events Are Not Coalesced Into One Source Record

---

# 170. Notification aggregation

Daily digest MAY be added later.

---

# 171. Digest does not replace source Notification records unless explicitly designed.

---

# 172. Security of provider webhooks

Delivery providers may send:

- delivered;
- failed;
- bounced;
- rejected;
- read where supported.

---

# 173. Webhook endpoint

Must validate:

- signature;
- timestamp/replay;
- provider event ID;
- expected provider;
- payload schema.

---

# 174. NOT-WBH-001 — Provider Webhook Is Untrusted Input

---

# 175. Webhook dedupe

Provider event ID or deterministic event fingerprint.

---

# 176. NOT-WBH-002 — Webhook Processing Is Idempotent

---

# 177. Unknown provider message ID

Must not mutate arbitrary Delivery.

---

# 178. NOT-WBH-003 — Callback Must Resolve Exact Delivery

---

# 179. Out-of-order callbacks

Possible:

```text
DELIVERED event
then delayed ACCEPTED event
```

---

# 180. State transition logic must prevent regression.

---

# 181. NOT-WBH-004 — Delivery State Cannot Regress From Stronger Confirmed State

---

# 182. Duplicate callbacks

No duplicate side effect.

---

# 183. Webhook logs

Do not log complete sensitive payload unnecessarily.

---

# 184. Email bounce

Can mark delivery failed/final depending provider semantics.

---

# 185. WhatsApp read receipt

If provider supplies it, MAY store as channel receipt.

It still does not mean Business Source handled.

---

# 186. NOT-DEL-010 — Provider Read Receipt ≠ Admin Workflow Completion

---

# 187. Deep-link visibility

Notification renderer must not leak source data into URL query string unnecessarily.

---

# 188. Avoid:

```text
/admin/feedback?id=...&phone=...&message=...
```

---

# 189. NOT-SEC-007 — Deep Links Carry Identifiers, Not Private Payload

---

# 190. Notification rendering

Templates should be controlled/versioned.

---

# 191. Suggested:

```text
notification_templates
```

or code-based versioned templates.

---

# 192. Template identity

```text
event_type
channel
locale
version
```

---

# 193. NOT-INV-010 — Template Is Presentation, Not Routing Authority

---

# 194. Template change

Does not change historical delivery payload automatically.

---

# 195. Historical payload

Delivery metadata may preserve a safe snapshot/hash of what was sent according to retention needs.

---

# 196. Sensitive payload retention

Should be shorter than business Source where possible.

---

# 197. NOT-SEC-008 — Do Not Persist Full Business Payload in Every Delivery

---

# 198. Localization

Notification templates can support:

```text
ru
en
```

according to recipient preference.

---

# 199. Machine event types remain locale-neutral.

---

# 200. NOT-INV-011 — Localized Text Is Never Machine Workflow Input

---

# 201. Time display

Admin Notification Center uses configured/local timezone.

Stored timestamps:

```text
timestamptz
```

---

# 202. Quiet-hours timezone explicit.

---

# 203. Provider delivery timestamp

Preserve original provider timestamp when trustworthy plus local receipt timestamp.

---

# 204. Preferences Admin UX

Recommended:

```text
Notifications
├── Channels
│   ├── In-App
│   ├── WhatsApp
│   └── Email
├── Event Categories
├── Priority Thresholds
├── Quiet Hours
├── Recipients
└── Test Delivery
```

---

# 205. Test delivery

Useful Admin action.

---

# 206. `SendTestNotification`

must create explicit TEST notification/delivery.

---

# 207. NOT-INV-012 — Test Delivery Is Clearly Marked and Not Business Event

---

# 208. Test should not use real Feedback/Casting data.

Use synthetic safe content.

---

# 209. Notification Center detail

Recommended fields:

```text
title
summary
event type
source entity
priority
created at
read state
delivery channels
attempt history
deep link
```

---

# 210. Delivery diagnostics

Admin may see:

```text
WhatsApp — failed final
Email — delivered
In-App — unread
```

---

# 211. Raw provider body

Not necessary in normal UI.

---

# 212. NOT-SEC-009 — Provider Diagnostics Are Sanitized

---

# 213. Failure handling

A FAILED_FINAL delivery should produce:

- visible status;
- optional in-app operational alert;
- manual retry path if meaningful.

---

# 214. Avoid recursive notification storms

Example:

WhatsApp notification fails.

Do not generate WhatsApp notification saying WhatsApp notification failed.

---

# 215. NOT-ROU-004 — Failure Alerts Must Avoid Recursive Channel Loops

---

# 216. Recommended

Provider failure alert:

```text
In-App
Email if alternative channel configured
```

---

# 217. Channel health

System may track:

```text
AVAILABLE
DEGRADED
UNAVAILABLE
AUTH_EXPIRED
```

---

# 218. NOT-ROU-005 — Channel Health Is Operational, Not Recipient Preference

---

# 219. Auth expiration

If WhatsApp/email integration requires authentication and it expires:

- new business events still persist;
- In-App continues;
- external deliveries fail/suppress safely;
- Admin gets channel-health warning.

---

# 220. NOT-INV-013 — Integration Failure Does Not Disable Notification Center

---

# 221. Scheduler

Responsible for:

- quiet-hour release;
- retry due deliveries;
- scheduled reminders;
- cleanup.

---

# 222. Scheduler does not invent notification intents.

It executes policy-created pending work.

---

# 223. NOT-INV-014 — Scheduler Is Not Business Decision Engine

---

# 224. Deadline reminders

Casting/Opportunity domains may emit or request scheduled notification intents.

---

# 225. Example:

```text
CASTING_DEADLINE_APPROACHING
```

---

# 226. Reminder dedupe

One configured reminder per threshold/event scope.

---

# 227. Time changes

If Casting deadline changes:

old scheduled reminder must be cancelled/superseded safely.

---

# 228. NOT-ROU-006 — Scheduled Delivery Must Bind to Current Reminder Generation

---

# 229. Old scheduler job cannot send stale reminder after deadline changed.

---

# 230. Background jobs

Suggested:

```text
NOTIFICATION_ROUTE_INTENT
NOTIFICATION_SEND_WHATSAPP
NOTIFICATION_SEND_EMAIL
NOTIFICATION_RELEASE_QUIET_HOURS
NOTIFICATION_RETRY_DELIVERY
NOTIFICATION_PROCESS_WEBHOOK
NOTIFICATION_CLEANUP
```

---

# 231. In-App creation

May occur transactionally after Intent creation or through worker depending architecture.

But must be durable.

---

# 232. Job payload

Contains:

```text
delivery_id
attempt/generation
```

not provider credentials.

---

# 233. NOT-DEL-011 — Worker Reloads Current Delivery State

---

# 234. Worker checks:

```text
delivery still pending/retryable
not cancelled
recipient still allowed
generation current
```

before send.

---

# 235. Recipient disabled after queue

Delivery should be suppressed/cancelled according to policy before sending.

---

# 236. NOT-REC-005 — Current Recipient Revocation Beats Stale Queue

---

# 237. Preference changed after queue

For unsent notification, current preference may suppress external delivery according to policy.

Already delivered history unaffected.

---

# 238. NOT-PRF-002 — Preference Change Does Not Rewrite Historical Delivery

---

# 239. Race: retry vs disable recipient

Worker must recheck current state.

---

# 240. Race: provider webhook vs local timeout

State machine handles concurrency and provider reconciliation.

---

# 241. NOT-DEL-012 — Delivery State Updates Are Version/Transition Safe

---

# 242. Error taxonomy

At minimum:

```text
NOTIFICATION_NOT_FOUND
NOTIFICATION_DELIVERY_NOT_FOUND
NOTIFICATION_RECIPIENT_NOT_FOUND
NOTIFICATION_RECIPIENT_DISABLED
NOTIFICATION_CHANNEL_DISABLED
NOTIFICATION_CHANNEL_UNAVAILABLE
NOTIFICATION_PREFERENCE_INVALID
NOTIFICATION_QUIET_HOURS_INVALID
NOTIFICATION_DELIVERY_NOT_RETRYABLE
NOTIFICATION_DELIVERY_ALREADY_SUCCEEDED
NOTIFICATION_PROVIDER_RATE_LIMITED
NOTIFICATION_PROVIDER_REJECTED
NOTIFICATION_PROVIDER_TIMEOUT
NOTIFICATION_UNKNOWN_OUTCOME
NOTIFICATION_WEBHOOK_INVALID
NOTIFICATION_WEBHOOK_REPLAY
NOTIFICATION_TEMPLATE_NOT_FOUND
NOTIFICATION_STALE_VERSION
```

---

# 243. Public surfaces

Provider errors stay private.

---

# 244. Admin UI error

Use normalized error classes.

Do not dump provider response/secret.

---

# 245. Audit

Audit-worthy:

```text
recipient created/disabled
preference changed
quiet hours changed
manual retry
manual resend
delivery cancelled
channel credential/config activation
```

---

# 246. Routine automatic attempt history

Operational record, not full business Audit.

---

# 247. NOT-INV-015 — Attempt History and Audit Have Different Roles

---

# 248. Outbox

Business domains create outbox events.

Notification module may create its own outbox/event stream for:

```text
NotificationCreated
DeliverySucceeded
DeliveryFailedFinal
RecipientDisabled
```

where consumers require them.

---

# 249. Do not create circular notification reactions without explicit policy.

---

# 250. Analytics

Useful events:

```text
notification_created
notification_read
delivery_attempted
delivery_delivered
delivery_failed_final
notification_deep_link_opened
```

---

# 251. Analytics excludes:

- full message;
- recipient phone/email;
- provider token;
- casting brief.

---

# 252. NOT-SEC-010 — Analytics Uses IDs/Categories, Not Payload Copies

---

# 253. Public marketing analytics

Must not receive private Notification Center data.

---

# 254. Search

Notifications may support Admin filtering/search by:

```text
title
event category
priority
read state
source type
```

---

# 255. Avoid indexing complete private business payload.

---

# 256. NOT-SEC-011 — Notifications Never Enter Public Search/SEO

---

# 257. Cache

Unread counts may be cached.

Notification detail may use Admin-scoped cache.

---

# 258. Read update invalidates count.

---

# 259. NOT-READ-006 — Cache Loss Does Not Lose Notification State

---

# 260. Notification data must remain DB-authoritative.

---

# 261. Retention

Different retention classes:

```text
Notification
Delivery
Attempt
Provider callback metadata
Sensitive rendered payload
Recipient endpoint
Preferences
```

---

# 262. Suggested principle

Business source typically outlives redundant notification payload.

---

# 263. NOT-SEC-012 — Notification Retention Is Shorter Where Possible

---

# 264. In-App history

Can retain sufficient operational history.

---

# 265. Old attempts

Can be compacted/purged according to retention after terminal state.

---

# 266. Unprocessed/retryable delivery

Never purge merely because old.

---

# 267. NOT-DEL-013 — Pending Work Is Not Age-Purged

---

# 268. Recipient deletion

Prefer disable/revoke first.

Historical delivery records retain tombstoned reference as needed.

---

# 269. Privacy deletion

If recipient PII must be removed:

- redact endpoint;
- preserve minimal delivery provenance;
- do not corrupt business Source.

---

# 270. NOT-SEC-013 — Recipient PII Redaction Preserves Minimal Operational Provenance

---

# 271. Restore

Critical rules after backup restore:

- do not resend historical successful deliveries;
- do not retry terminal failed deliveries automatically unless policy says so;
- do not reactivate disabled recipient;
- do not replay old provider callbacks;
- do not emit duplicate notification for restored business events.

---

# 272. NOT-INV-016 — Restore Must Reconcile Delivery Ledger Before Workers Resume

---

# 273. Successful delivery ledger

Acts as dedupe/reconciliation reference after restore.

---

# 274. Backup point older than provider delivery

Potential risk:

provider may have delivered, DB backup may not know.

Reconciliation strategy must prefer avoiding blind duplicate resend.

---

# 275. NOT-DEL-014 — Restore Uses Conservative External Side-Effect Semantics

---

# 276. Migration

Existing ad-hoc notifications must map to:

```text
Intent
Notification
Delivery
Attempt
Preferences/Recipients
```

where sufficient historical data exists.

---

# 277. Legacy email-only notifications

If no internal Notification existed:

may migrate as historical Delivery record with provenance.

Do not fabricate read status.

---

# 278. NOT-MIG-001 — Missing Historical Read State Remains Unknown/Unset

---

# 279. Legacy public contact used as recipient

Must not remain implicitly linked.

Migration creates explicit NotificationRecipient after review.

---

# 280. NOT-MIG-002 — Migration Breaks Hidden Contact/Recipient Coupling

---

# 281. Existing failed messages

Do not automatically retry on migration.

---

# 282. NOT-MIG-003 — Migration Does Not Replay External Side Effects

---

# 283. Provider message IDs

Preserve if available for reconciliation.

---

# 284. Migration idempotency

Required.

---

# 285. Security matrix

| Data | Public | Admin Notification Center | Provider |
|---|---:|---:|---:|
| Event type | No | Yes | minimal |
| Full Feedback/Casting content | No | via source deep-link | No by default |
| Recipient endpoint | No | restricted | required |
| Provider credential | No | No | infrastructure only |
| Delivery status | No | Yes | provider-origin |
| Internal notes | No | via source only | No |

---

# 286. NOT-SEC-014 — Provider Receives Only Channel-Necessary Data

---

# 287. Accessibility

Notification Center must support:

- semantic list/items;
- keyboard navigation;
- visible unread state;
- non-color priority indication;
- accessible action buttons;
- meaningful timestamps;
- focus management after read/dismiss actions.

---

# 288. Screen reader

Notification title should describe event.

Avoid:

```text
Уведомление 123
```

---

# 289. Delivery state

Text label, not icon/color only.

---

# 290. Mobile Admin

Notification Center should support:

- read;
- open source;
- retry where allowed;
- filter unread/high.

---

# 291. Anti-patterns

`NOT-AP-001`  
Business domain sends WhatsApp before commit.

`NOT-AP-002`  
Provider failure rolls back Feedback/Casting.

`NOT-AP-003`  
Notification is the only record of inquiry.

`NOT-AP-004`  
Public Contact automatically becomes NotificationRecipient.

`NOT-AP-005`  
NotificationRecipient becomes public contact.

`NOT-AP-006`  
Full casting brief included in WhatsApp by default.

`NOT-AP-007`  
Private attachment sent through external notification.

`NOT-AP-008`  
Admin notes included in provider message.

`NOT-AP-009`  
Deep link itself grants authorization.

`NOT-AP-010`  
Raw access token placed in deep link query.

`NOT-AP-011`  
Email used as backup database.

`NOT-AP-012`  
Provider accepted = delivered.

`NOT-AP-013`  
Provider delivered = Admin read.

`NOT-AP-014`  
Notification read = Feedback resolved.

`NOT-AP-015`  
Notification dismissed = Business Source deleted.

`NOT-AP-016`  
Retry creates new Notification.

`NOT-AP-017`  
Provider timeout blindly retried causing duplicate sends.

`NOT-AP-018`  
Infinite retry loop.

`NOT-AP-019`  
429 ignored.

`NOT-AP-020`  
Quiet hours use server timezone instead of recipient/config timezone.

`NOT-AP-021`  
Quiet hours hide In-App event entirely.

`NOT-AP-022`  
CRITICAL bypass hardcoded independently in each module.

`NOT-AP-023`  
AI decides all notification priority.

`NOT-AP-024`  
VOP notification performs recommended action automatically.

`NOT-AP-025`  
Every successful media job sends WhatsApp.

`NOT-AP-026`  
Notification flood with identical broken-link alerts.

`NOT-AP-027`  
Distinct Feedback submissions deduped into one record.

`NOT-AP-028`  
Provider webhook trusted without signature validation.

`NOT-AP-029`  
Duplicate webhook applies side effect twice.

`NOT-AP-030`  
Out-of-order callback regresses DELIVERED → SENT.

`NOT-AP-031`  
Unknown provider message ID updates arbitrary Delivery.

`NOT-AP-032`  
Provider secret stored in job payload.

`NOT-AP-033`  
Provider response body shown raw in Admin UI.

`NOT-AP-034`  
Recipient endpoint serialized to Public client.

`NOT-AP-035`  
Notification search enters public search index.

`NOT-AP-036`  
Shared public cache stores Notification Center.

`NOT-AP-037`  
Unread badge is treated as authoritative count forever.

`NOT-AP-038`  
Mark all read closes business items.

`NOT-AP-039`  
Dismiss deletes Delivery history.

`NOT-AP-040`  
Test notification uses actual private Casting data.

`NOT-AP-041`  
Test notification is indistinguishable from real event.

`NOT-AP-042`  
Template localized title used as machine event ID.

`NOT-AP-043`  
Template change rewrites historical delivery payload.

`NOT-AP-044`  
Recipient disabled but stale queued worker still sends.

`NOT-AP-045`  
Preference disabled but queued delivery ignores fresh state.

`NOT-AP-046`  
Deadline changed but stale reminder still sends.

`NOT-AP-047`  
Scheduler invents business notifications without policy/source event.

`NOT-AP-048`  
Failed WhatsApp triggers another WhatsApp failure notification recursively.

`NOT-AP-049`  
Auth-expired provider disables entire Notification Center.

`NOT-AP-050`  
Pending delivery purged only because it is old.

`NOT-AP-051`  
Backup restore resends all notifications.

`NOT-AP-052`  
Backup restore re-enables disabled recipient.

`NOT-AP-053`  
Migration automatically retries legacy failed messages.

`NOT-AP-054`  
Legacy public contact silently remains provider recipient.

`NOT-AP-055`  
Analytics contains recipient phone/email and full payload.

`NOT-AP-056`  
Notification payload becomes duplicate business data archive.

`NOT-AP-057`  
Channel SDK called directly from Feedback/Casting domain code.

`NOT-AP-058`  
One giant `status` mixes Notification, Delivery, Attempt and Read state.

`NOT-AP-059`  
Manual resend represented as retry.

`NOT-AP-060`  
Delivery retry creates duplicate source event.

---

# 292. Core invariants

`NOT-INV-017`  
Notification Module is operational attention layer, not Business Source.

`NOT-INV-018`  
Business Source commits before Notification processing.

`NOT-INV-019`  
Notification provider failure never rolls back Business Source.

`NOT-INV-020`  
Notification Intent is distinct from Notification.

`NOT-INV-021`  
Notification is distinct from channel Delivery.

`NOT-INV-022`  
Delivery is distinct from Attempt.

`NOT-INV-023`  
Read state is distinct from Delivery state.

`NOT-INV-024`  
Public Contacts and NotificationRecipients remain separate domains.

`NOT-INV-025`  
Recipient endpoints are server-private.

`NOT-INV-026`  
Notification event type is stable machine value.

`NOT-INV-027`  
Localized message text does not drive business logic.

`NOT-INV-028`  
Priority is independent from Business Source state.

`NOT-INV-029`  
In-App remains available independently of external provider.

`NOT-INV-030`  
WhatsApp uses official/approved Business integration only.

`NOT-INV-031`  
Provider secrets never enter browser/domain/job payloads.

`NOT-INV-032`  
External notification payloads are minimized.

`NOT-INV-033`  
Admin deep links require normal authentication.

`NOT-INV-034`  
One Notification may have independent channel outcomes.

`NOT-INV-035`  
Provider accepted/sent/delivered/read semantics remain distinct.

`NOT-INV-036`  
Notification read state cannot mutate Feedback/Casting/Opportunity workflow.

`NOT-INV-037`  
Delivery retry operates on same logical Delivery.

`NOT-INV-038`  
Manual resend is distinct from retry.

`NOT-INV-039`  
Delivery processing is idempotent.

`NOT-INV-040`  
Ambiguous external timeout is reconciled before blind resend.

`NOT-INV-041`  
Retries are finite and backoff-aware.

`NOT-INV-042`  
Provider rate limits are respected.

`NOT-INV-043`  
Quiet hours are timezone-aware.

`NOT-INV-044`  
Quiet hours affect routing timing, not event existence.

`NOT-INV-045`  
Recipient preferences do not alter Business Source.

`NOT-INV-046`  
Disabled recipient does not receive new external deliveries.

`NOT-INV-047`  
Historical deliveries remain unchanged after preference/recipient changes.

`NOT-INV-048`  
Domains emit business/outbox events instead of calling channel SDKs.

`NOT-INV-049`  
Notification grouping does not merge underlying business events.

`NOT-INV-050`  
Technical notification dedupe does not dedupe distinct Feedback/Casting records.

`NOT-INV-051`  
Provider webhooks are signature/replay validated.

`NOT-INV-052`  
Webhook processing is idempotent.

`NOT-INV-053`  
Out-of-order callbacks cannot regress delivery state.

`NOT-INV-054`  
Provider read receipt does not imply Business workflow completion.

`NOT-INV-055`  
Templates are presentation layer.

`NOT-INV-056`  
Template version/localization is separate from event identity.

`NOT-INV-057`  
Notification Center remains Admin-private.

`NOT-INV-058`  
Unread count is a derived projection.

`NOT-INV-059`  
Cache loss cannot lose Notification state.

`NOT-INV-060`  
Notification payload retention is independent from Business Source retention.

`NOT-INV-061`  
Pending/retryable work is not purged merely by age.

`NOT-INV-062`  
Recipient privacy redaction preserves minimal provenance.

`NOT-INV-063`  
Restore reconciles delivery ledger before workers resume.

`NOT-INV-064`  
Restore avoids replaying successful external side effects.

`NOT-INV-065`  
Migration does not resend historical messages.

`NOT-INV-066`  
Migration does not infer Public Contact = NotificationRecipient.

`NOT-INV-067`  
Notification analytics contain metadata, not full private payload.

`NOT-INV-068`  
Notification Search is Admin-only.

`NOT-INV-069`  
VOP can create attention recommendation but not execute business decision.

`NOT-INV-070`  
Scheduler executes due notification work, not business decisions.

`NOT-INV-071`  
Stale queued worker revalidates current recipient/preference/delivery generation.

`NOT-INV-072`  
Scheduled reminder binds to current source/reminder generation.

`NOT-INV-073`  
Integration outage does not disable In-App Notification Center.

`NOT-INV-074`  
Failure notifications avoid recursive same-channel loops.

`NOT-INV-075`  
Test notifications use synthetic safe content.

`NOT-INV-076`  
Notification module functions without AI.

`NOT-INV-077`  
Notification provider selection does not affect Business domain contracts.

`NOT-INV-078`  
All provider-specific details remain behind adapters/infrastructure boundaries.

`NOT-INV-079`  
Admin UI can distinguish Business record state from delivery state.

`NOT-INV-080`  
All critical delivery and privacy rules are enforced server-side.

---

# 293. E2E-NOT-001 — Feedback commit first

Submit Feedback.

Verify DB commit before notification worker/provider invocation.

---

# 294. E2E-NOT-002 — Provider failure

WhatsApp fails.

Feedback remains persisted.

---

# 295. E2E-NOT-003 — In-App

New Feedback creates unread In-App notification according to policy.

---

# 296. E2E-NOT-004 — Public Contact separation

Public WhatsApp number exists.

No system delivery uses it unless separately configured as NotificationRecipient.

---

# 297. E2E-NOT-005 — Reverse separation

Internal NotificationRecipient number does not appear in Public Contacts.

---

# 298. E2E-NOT-006 — Minimal payload

WhatsApp delivery excludes full Feedback message/attachments.

---

# 299. E2E-NOT-007 — Deep-link auth

Unauthenticated deep-link access requires Admin login.

---

# 300. E2E-NOT-008 — Read state

Admin reads notification.

Feedback workflow state remains unchanged.

---

# 301. E2E-NOT-009 — Mark unread

Notification returns UNREAD without changing source entity.

---

# 302. E2E-NOT-010 — Mark all read

Multiple Notifications become READ.

No Feedback/Casting records change.

---

# 303. E2E-NOT-011 — Independent channels

WhatsApp fails while Email delivers and In-App remains unread.

Valid state preserved.

---

# 304. E2E-NOT-012 — Accepted vs delivered

Provider accepts WhatsApp.

Delivery remains ACCEPTED/SENT until delivery receipt arrives.

---

# 305. E2E-NOT-013 — Provider delivered

Callback moves delivery to DELIVERED.

In-App read state unaffected.

---

# 306. E2E-NOT-014 — Duplicate webhook

Same delivery callback repeated.

No duplicate side effect.

---

# 307. E2E-NOT-015 — Out-of-order callback

DELIVERED received before delayed ACCEPTED.

Final state remains DELIVERED.

---

# 308. E2E-NOT-016 — Invalid webhook signature

Rejected.

Delivery unchanged.

---

# 309. E2E-NOT-017 — Webhook replay

Replay protection prevents duplicate processing.

---

# 310. E2E-NOT-018 — Unknown provider ID

Callback cannot mutate unrelated Delivery.

---

# 311. E2E-NOT-019 — Retry

Temporary timeout creates new Attempt on same Delivery.

---

# 312. E2E-NOT-020 — Provider idempotency

Retry does not create duplicate user-visible message when provider idempotency succeeds.

---

# 313. E2E-NOT-021 — Ambiguous timeout

System reconciles unknown outcome before blind resend.

---

# 314. E2E-NOT-022 — Final failure

Invalid recipient becomes FAILED_FINAL after policy-defined handling.

---

# 315. E2E-NOT-023 — Manual retry final failure

Human triggers retry where allowed.

New Attempt created, not new Notification.

---

# 316. E2E-NOT-024 — Resend

Intentional Human resend is represented distinctly from technical retry.

---

# 317. E2E-NOT-025 — Quiet hours normal

NORMAL WhatsApp delivery deferred.

In-App appears immediately.

---

# 318. E2E-NOT-026 — Quiet hours release

Scheduler sends deferred delivery after configured quiet period.

---

# 319. E2E-NOT-027 — Timezone

Quiet-hours evaluation uses configured IANA timezone.

---

# 320. E2E-NOT-028 — Critical bypass

CRITICAL notification bypasses quiet hours only when policy explicitly permits.

---

# 321. E2E-NOT-029 — Preference disable

Disable WhatsApp.

New eligible event produces no new WhatsApp delivery.

---

# 322. E2E-NOT-030 — Preference history

Old delivered WhatsApp record remains unchanged.

---

# 323. E2E-NOT-031 — Recipient disabled while queued

Queued worker rechecks recipient and suppresses/cancels delivery.

---

# 324. E2E-NOT-032 — Channel auth expiry

WhatsApp auth expires.

In-App still works.

Admin receives channel-health warning.

---

# 325. E2E-NOT-033 — No recursive failure loop

WhatsApp failure alert does not trigger another WhatsApp alert recursively.

---

# 326. E2E-NOT-034 — Feedback routing

CASTING_INVITATION produces configured high-priority attention routing.

---

# 327. E2E-NOT-035 — AI completion

Casting AI completion generates “ready for review”, not auto-confirmation.

---

# 328. E2E-NOT-036 — VOP

VOP creates attention notification for stale questionnaire.

No questionnaire rebuild/publication occurs automatically.

---

# 329. E2E-NOT-037 — Notification dedupe

Same unresolved technical broken-link alert repeated hourly does not flood external channels according to dedupe policy.

---

# 330. E2E-NOT-038 — Distinct Feedback

Two new Feedback submissions each produce distinct logical attention records.

---

# 331. E2E-NOT-039 — Test notification

Admin sends test.

Message contains synthetic test content and is clearly marked.

---

# 332. E2E-NOT-040 — Test isolation

No real Feedback/Casting content copied into test notification.

---

# 333. E2E-NOT-041 — Unread count

New notification increments count.

Read action decrements/reconciles.

---

# 334. E2E-NOT-042 — Stale badge

Cached count wrong.

Notification Center still loads authoritative current records.

---

# 335. E2E-NOT-043 — Search privacy

Notification title searchable only by Admin.

No Public Search result.

---

# 336. E2E-NOT-044 — Analytics privacy

Delivery analytics contain event/category/delivery IDs, not recipient phone/message body.

---

# 337. E2E-NOT-045 — Scheduler stale reminder

Casting deadline changes after reminder queued.

Old reminder generation does not send.

---

# 338. E2E-NOT-046 — Rate limited provider

429 causes bounded deferred retry respecting provider timing.

---

# 339. E2E-NOT-047 — Max retries

Retryable failure reaches configured maximum.

Delivery becomes FAILED_FINAL or appropriate terminal status.

---

# 340. E2E-NOT-048 — Pending retention

Old but still pending valid delivery is not deleted by retention cleanup.

---

# 341. E2E-NOT-049 — Recipient privacy redaction

Recipient phone is redacted under privacy request.

Historical delivery retains safe minimal provenance.

---

# 342. E2E-NOT-050 — Restore successful delivery

Restore backup/reconcile.

Previously successful external message is not sent again.

---

# 343. E2E-NOT-051 — Restore disabled recipient

Disabled recipient remains disabled after restore.

---

# 344. E2E-NOT-052 — Restore pending

Pending work is reconciled before worker resumes.

---

# 345. E2E-NOT-053 — Migration legacy email

Legacy email notification imported as historical delivery where provenance exists.

No read state fabricated.

---

# 346. E2E-NOT-054 — Migration no resend

Migration does not send historical notification.

---

# 347. E2E-NOT-055 — Migration recipient review

Legacy Public Contact recipient does not remain implicitly routed without explicit NotificationRecipient configuration.

---

# 348. E2E-NOT-056 — Provider secret safety

Provider credentials absent from DB domain records/jobs/browser payload.

---

# 349. E2E-NOT-057 — Deep-link privacy

URL does not contain message/phone/email payload.

---

# 350. E2E-NOT-058 — Accessibility

Notification list/read/filter actions work by keyboard and expose readable status labels.

---

# 351. E2E-NOT-059 — Mobile

Admin can open Notification Center, mark read and follow source deep link on mobile.

---

# 352. E2E-NOT-060 — AI disabled

Notification routing/delivery works unchanged with AI subsystem unavailable.

---

# 353. Architecture diagram

```text
BUSINESS DOMAIN
 Feedback / Casting / Opportunity / System
                │
                ▼
        Commit + Audit + Outbox
                │
                ▼
        Notification Policy
                │
                ▼
         Notification Intent
                │
        ┌───────┴────────┐
        ▼                ▼
   Notification      Recipients
        │
   ┌────┼──────┐
   ▼    ▼      ▼
In-App WhatsApp Email
        │      │
        ▼      ▼
     Provider Adapters
```

---

# 354. State separation diagram

```text
NOTIFICATION READ
UNREAD ↔ READ

       ≠

DELIVERY
PENDING → QUEUED → SENDING
        → ACCEPTED/SENT
        → DELIVERED
        → FAILED_*

       ≠

ATTEMPT
STARTED → SUCCEEDED / FAILED / TIMED_OUT

       ≠

BUSINESS WORKFLOW
Feedback / Casting / Opportunity states
```

---

# 355. Quiet-hours diagram

```text
Notification Intent
       ↓
Routing Policy
       ↓
Priority + Preferences + Timezone
       ↓
 ┌───────────────┐
 │ Quiet Hours ? │
 └───────┬───────┘
      yes│no
         │
    ┌────┴─────┐
    ▼          ▼
Defer        Queue now
External
Delivery

In-App may still appear immediately.
```

---

# 356. Feedback integration diagram

```text
FeedbackSubmitted
      ↓
Feedback already COMMITTED
      ↓
Notification Intent
      ↓
In-App notification
      ↓
Optional WhatsApp / Email
      ↓
Provider failure?
      │
      └── Feedback remains intact
```

---

# 357. Webhook diagram

```text
Provider Callback
      ↓
Signature Validation
      ↓
Replay/Dedupe Check
      ↓
Resolve exact Delivery
      ↓
Validate state transition
      ↓
Update Delivery
      ↓
Attempt/Receipt history
```

---

# 358. Restore safety diagram

```text
Restore Database
      ↓
Workers remain paused
      ↓
Reconcile:
- successful deliveries
- pending deliveries
- disabled recipients
- revoked access
- provider IDs
      ↓
Resume workers
```

---

# 359. Quality gate

Перед implementation должны быть подтверждены:

- [ ] NotificationIntent;
- [ ] Notification;
- [ ] NotificationRecipient;
- [ ] NotificationDelivery;
- [ ] DeliveryAttempt;
- [ ] read state;
- [ ] event category/type taxonomy;
- [ ] priority taxonomy;
- [ ] centralized routing policy;
- [ ] Public Contact vs Recipient separation;
- [ ] In-App baseline;
- [ ] official WhatsApp Business integration boundary;
- [ ] Email channel boundary;
- [ ] recipient endpoint privacy;
- [ ] preferences;
- [ ] event-specific overrides;
- [ ] quiet hours + IANA timezone;
- [ ] priority bypass policy;
- [ ] deep-link authorization;
- [ ] minimal external payload;
- [ ] provider adapter abstraction;
- [ ] accepted/sent/delivered/read semantics;
- [ ] attempt history;
- [ ] finite retry/backoff/jitter;
- [ ] provider 429 handling;
- [ ] ambiguous timeout reconciliation;
- [ ] idempotent provider processing;
- [ ] manual Retry vs Resend;
- [ ] recipient revocation recheck;
- [ ] preference recheck;
- [ ] reminder generation safety;
- [ ] provider webhooks;
- [ ] signature/replay validation;
- [ ] out-of-order callback protection;
- [ ] channel health;
- [ ] failure diagnostics;
- [ ] recursive-failure prevention;
- [ ] Admin Notification Center;
- [ ] unread counts;
- [ ] test notification;
- [ ] accessibility/mobile;
- [ ] Admin-only search/cache;
- [ ] analytics minimization;
- [ ] retention;
- [ ] privacy redaction;
- [ ] restore reconciliation;
- [ ] migration;
- [ ] deterministic E2E coverage.

---

# 360. Acceptance criteria

`AC-NOT-001`  
Notification subsystem never serves as Business Source.

`AC-NOT-002`  
Business Source commits before notification processing begins.

`AC-NOT-003`  
Notification provider failure cannot roll back Feedback/Casting/Opportunity.

`AC-NOT-004`  
Notification Intent, Notification, Delivery and Attempt are separate concepts.

`AC-NOT-005`  
Notification read state is separate from delivery status and business workflow.

`AC-NOT-006`  
Public Contacts do not automatically become NotificationRecipients.

`AC-NOT-007`  
NotificationRecipients do not become Public Contacts.

`AC-NOT-008`  
Recipient endpoints are server-private.

`AC-NOT-009`  
Stable machine event types drive routing, not localized text.

`AC-NOT-010`  
Priority uses `CRITICAL/HIGH/NORMAL/LOW`.

`AC-NOT-011`  
Notification routing is centralized and policy-driven.

`AC-NOT-012`  
In-App Notification Center works independently of external providers.

`AC-NOT-013`  
WhatsApp notifications use official/approved business channel only.

`AC-NOT-014`  
Provider credentials never enter browser/domain/job payloads.

`AC-NOT-015`  
External notification payloads are minimized.

`AC-NOT-016`  
Protected Admin deep links still require authentication/authorization.

`AC-NOT-017`  
One Notification can have independent In-App/WhatsApp/Email delivery results.

`AC-NOT-018`  
Provider acceptance is not treated as confirmed delivery.

`AC-NOT-019`  
Provider delivery/read receipt does not modify source business workflow.

`AC-NOT-020`  
Read/Unread actions affect Notification only.

`AC-NOT-021`  
Retry uses same logical Delivery and creates new Attempt.

`AC-NOT-022`  
Intentional Resend is distinct from Retry.

`AC-NOT-023`  
External delivery processing is idempotent.

`AC-NOT-024`  
Ambiguous provider timeout does not trigger blind duplicate resend.

`AC-NOT-025`  
Retries are finite, backoff/jitter aware and respect provider rate limits.

`AC-NOT-026`  
Quiet hours are evaluated using explicit timezone.

`AC-NOT-027`  
Quiet hours can defer external channel while In-App event remains visible.

`AC-NOT-028`  
Critical quiet-hour bypass occurs only through explicit routing policy.

`AC-NOT-029`  
Notification preferences affect delivery but never Business Source.

`AC-NOT-030`  
Disabled/revoked recipient is revalidated before queued delivery sends.

`AC-NOT-031`  
Preference changes do not rewrite historical delivery records.

`AC-NOT-032`  
Business modules emit events rather than calling WhatsApp/Email provider SDKs directly.

`AC-NOT-033`  
Notification grouping/deduplication does not merge distinct business events.

`AC-NOT-034`  
Provider webhooks are signature/replay validated.

`AC-NOT-035`  
Provider callbacks are idempotent and resolve exact Delivery.

`AC-NOT-036`  
Out-of-order callbacks cannot regress delivery state.

`AC-NOT-037`  
Notification templates are presentation-only and versionable/localizable.

`AC-NOT-038`  
Notification Center and its search/cache are Admin-only.

`AC-NOT-039`  
Unread count remains a derived projection.

`AC-NOT-040`  
Cache failure cannot lose Notification state.

`AC-NOT-041`  
Notification retention is independently configurable from Business Source retention.

`AC-NOT-042`  
Pending/retryable deliveries are never deleted solely due to age.

`AC-NOT-043`  
Recipient privacy redaction preserves minimal delivery provenance without retaining prohibited PII.

`AC-NOT-044`  
Restore reconciles delivery ledger before workers resume.

`AC-NOT-045`  
Backup restore does not resend already successful external notifications.

`AC-NOT-046`  
Migration does not replay historical provider deliveries.

`AC-NOT-047`  
Migration removes implicit Public Contact→Notification Recipient coupling.

`AC-NOT-048`  
Analytics do not store complete messages or recipient endpoints unnecessarily.

`AC-NOT-049`  
VOP notifications surface recommendations without executing professional actions.

`AC-NOT-050`  
Scheduler sends only previously authorized due notification work.

`AC-NOT-051`  
Changed deadlines/reminder generations invalidate stale scheduled notifications.

`AC-NOT-052`  
External integration outage does not disable In-App Notification Center.

`AC-NOT-053`  
Provider failure alerts do not recursively use the failing channel without explicit safe policy.

`AC-NOT-054`  
Test notifications use synthetic, clearly marked content.

`AC-NOT-055`  
Notification workflows function without AI.

`AC-NOT-056`  
Admin UI clearly distinguishes Source workflow state, Notification state and Delivery state.

`AC-NOT-057`  
All persistence, retry, quiet-hours, webhook, privacy, restore and concurrency invariants have deterministic E2E tests.

---

# 361. Финальная доктрина

> **Notifications Module является централизованным operational attention layer системы. Feedback, Casting, Opportunity или другой Business Source всегда фиксируется первым вместе с Audit/Outbox, после чего Notification Policy создаёт Notification Intent и независимые channel-specific Deliveries. In-App является устойчивым внутренним каналом; WhatsApp работает только через approved Business integration, Email остаётся дополнительным delivery channel. Public Contacts и NotificationRecipients принципиально разделены. `UNREAD/READ`, provider `ACCEPTED/SENT/DELIVERED`, Delivery Attempt и Business Workflow являются разными state machines. Quiet hours, priority, preferences и channel availability управляют только маршрутизацией и временем доставки, но не существованием Business Event. Delivery retries выполняются идемпотентно и конечное число раз; ambiguous provider outcomes требуют reconciliation, а provider callbacks проходят signature/replay validation и не могут регрессировать подтверждённое состояние. External notification payloads содержат только необходимый минимум и защищённый Admin deep link, который сам по себе не даёт authorization. Сбои WhatsApp/Email, смена recipient preferences, backup restore или отключение AI никогда не должны привести к потере, повторному созданию или повторному исполнению исходного профессионального события.**