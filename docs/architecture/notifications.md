# NOTIFICATION ARCHITECTURE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная архитектура In-App, WhatsApp, Email, delivery lifecycle, quiet hours и privacy-safe уведомлений

**Целевой файл:** `docs/architecture/notifications.md`  
**Документ:** DOC-080  
**Статус:** ✅ Completed  
**Тип:** Architecture / Notifications / Messaging / Delivery / External Channels

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/conversion-funnel.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/admin-navigation.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/forms-and-validation.md`
- `docs/ux/ui-states.md`
- `docs/domain/domain-model.md`
- `docs/domain/entity-relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions-and-history.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/components.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`
- `docs/architecture/background-jobs.md`
- `docs/architecture/cache.md`
- `docs/architecture/ai.md`

---

# 1. Назначение документа

Настоящий документ определяет архитектуру Notification subsystem продукта.

Он фиксирует:

1. что является Notification;
2. чем Notification отличается от Feedback/Inbox;
3. чем Notification отличается от Delivery;
4. In-App channel;
5. WhatsApp channel;
6. Email channel;
7. notification intent;
8. source business events;
9. persistence-before-notification;
10. delivery lifecycle;
11. quiet hours;
12. retries;
13. idempotency;
14. provider reconciliation;
15. privacy-minimized payload;
16. protected Admin links;
17. recipient resolution;
18. templates;
19. localization;
20. priority;
21. deduplication;
22. batching/digests;
23. failures;
24. Admin Notification Center;
25. read/unread state;
26. provider credentials;
27. observability;
28. audit;
29. security;
30. retention;
31. testing.

---

# 2. Главная доктрина

> **Notification сообщает о уже существующем факте или требующем внимания состоянии, но никогда не создаёт и не подтверждает этот факт.**

Canonical:

```text
BUSINESS EVENT
    ↓
AUTHORITATIVE COMMIT
    ↓
NOTIFICATION INTENT
    ↓
NOTIFICATION
    ↓
DELIVERY RECORD(S)
    ↓
CHANNEL PROVIDER
```

Never:

```text
CHANNEL MESSAGE SENT
    ↓
therefore Feedback exists
```

---

# 3. Notification IDs

Используются:

```text
NOT-*
NOT-INT-*
NOT-DEL-*
NOT-CH-*
NOT-POL-*
NOT-TPL-*
NOT-SEC-*
NOT-E2E-*
```

Invariants:

```text
NOT-INV-*
```

---

# 4. Core entities

Notification architecture использует концептуально:

```text
Notification
NotificationDelivery
NotificationPreference
NotificationTemplate
```

Точная physical schema определяется DOC-090/091.

---

# 5. Notification

`Notification` — внутренний durable record:

> **что произошло и почему оператору может потребоваться внимание.**

---

# 6. NotificationDelivery

`NotificationDelivery` — отдельная попытка/жизненный цикл доставки Notification по конкретному каналу.

---

# 7. Пример

```text
Feedback #123 создан
    ↓
Notification #456
    ├─ In-App Delivery
    ├─ WhatsApp Delivery
    └─ Email Delivery
```

---

# 8. NOT-INV-001 — Notification ≠ Business Entity

Notification не является:

```text
Feedback
Casting
Opportunity
Questionnaire
SocialPost
SupportTicket
```

---

# 9. NOT-INV-002 — Notification ≠ Inbox Item

Professional Feedback остаётся в **Feedback Inbox**.

Notification Center только сообщает:

```text
Новое профессиональное обращение
```

---

# 10. Read State Independence

```text
Notification read
!=
Feedback read
!=
Feedback resolved
```

---

# 11. NOT-INV-003 — Source Persists First

Для business-critical входящих событий:

```text
persist business record
COMMIT
then notification processing
```

обязательно.

---

# 12. Feedback Example

Correct:

```text
Public form
  ↓
Feedback persisted
  ↓
COMMIT
  ↓
Notification
  ↓
WhatsApp
```

---

# 13. Incorrect Feedback Flow

```text
Public form
  ↓
WhatsApp send
  ↓
database insert
```

Prohibited.

---

# 14. Consequence

Если WhatsApp недоступен:

```text
Feedback remains safely stored
```

---

# 15. NOT-INV-004 — Delivery Failure Does Not Rewrite Source

Examples:

```text
WhatsApp FAILED
```

does not mean:

```text
Feedback FAILED
Casting CLOSED
Opportunity changed
```

---

# 16. Notification source

Notification MUST have identifiable source context.

Conceptually:

```text
source_type
source_id
source_event
```

---

# 17. Source event examples

```text
FeedbackCreated
CastingCreated
CastingDeadlineApproaching
OpportunityStageChanged
QuestionnaireGenerationFailed
NotificationDeliveryFailed
SocialPublishFailed
VOPActionRequired
```

---

# 18. Business Event vs Technical Event

Notification subsystem may notify about:

## Business

```text
New Casting Inquiry
Role Offer
Opportunity Callback
```

## Operational

```text
PDF generation failed
WhatsApp provider configuration invalid
Critical media missing
```

---

# 19. Notification importance

Canonical priority classes:

```text
CRITICAL
HIGH
NORMAL
LOW
```

---

# 20. CRITICAL

Reserved for conditions requiring immediate attention.

Examples may include:

```text
security-related access issue
critical professional deadline
system condition risking loss of active inquiry
```

Must remain rare.

---

# 21. HIGH

Examples:

```text
new casting invitation
role offer
materials request with deadline
callback/audition update
```

---

# 22. NORMAL

Examples:

```text
professional inquiry
questionnaire request
social publish failure
AI analysis completed where attention requested
```

---

# 23. LOW

Examples:

```text
routine content health suggestion
successful background completion if notification is useful
```

Many routine successes SHOULD not create notifications.

---

# 24. Notification Creation Policy

Not every Domain Event becomes Notification.

Canonical:

```text
Domain Event
   ↓
Notification Policy
   ↓
Notify?
   ├─ No
   └─ Yes → create Notification
```

---

# 25. Avoid Notification Noise

Examples generally not needing notification:

```text
thumbnail generated
cache rebuilt
search reindexed successfully
routine AUTO-4 completed
```

These belong job/system history.

---

# 26. Notification Center Goal

Admin Notification Center answers:

> **Что произошло или требует моего внимания, независимо от того, в каком domain workspace находится источник?**

---

# 27. Dashboard vs Notification Center

Dashboard:

```text
What needs action today?
```

Notification Center:

```text
What notifications/events have arrived?
```

They overlap but are not identical.

---

# 28. VOP vs Notifications

VOP Observation:

```text
structured operational condition
```

Notification:

```text
delivery/attention signal
```

A VOP Observation MAY cause Notification if priority warrants.

---

# 29. Notification lifecycle

Recommended logical lifecycle:

```text
CREATED
ACTIVE
READ
ARCHIVED
```

Read may be modeled orthogonally.

---

# 30. Delivery lifecycle

Canonical from DOC-062:

```text
PENDING
   ↓
SCHEDULED
   ↓
SENDING
   ↓
SENT
```

Failure path:

```text
SENDING
   ↓
FAILED
   ↓
RETRY_SCHEDULED
   ↓
SENDING
```

Terminal alternative:

```text
CANCELLED
```

---

# 31. Delivery aggregate status

Notification MAY derive summary:

```text
DELIVERED
PARTIALLY_DELIVERED
DELIVERY_FAILED
PENDING
```

but source Notification remains independent.

---

# 32. Multi-channel independence

Example:

```text
In-App = SENT
WhatsApp = FAILED
Email = SENT
```

valid.

---

# 33. NOT-INV-005 — Channel Independence

Failure in one channel MUST NOT automatically mark successful other channels failed.

---

# 34. Notification channels

Baseline architecture supports:

```text
IN_APP
WHATSAPP
EMAIL
```

Email may be optional/configurable.

---

# 35. In-App channel

In-App SHOULD be the most reliable notification surface because it does not depend on third-party messaging delivery.

---

# 36. In-App persistence

For important Notification, In-App record SHOULD exist even if WhatsApp delivery is requested.

---

# 37. WhatsApp

WhatsApp integration MUST use:

> **official or otherwise approved business messaging channel**

according to chosen provider and applicable rules.

---

# 38. Prohibited WhatsApp strategy

Do not base production architecture on:

```text
unofficial browser automation
personal account scraping
fragile reverse-engineered protocol
```

---

# 39. WhatsApp purpose

External alert channel, not primary database.

---

# 40. Email

Email MAY provide:

```text
secondary alert channel
fallback channel
documented professional notification
```

depending configuration.

---

# 41. Email is not business authority

Same rules as WhatsApp.

---

# 42. Delivery Adapter Interface

Each external channel implements conceptually:

```text
NotificationChannelAdapter
```

---

# 43. Adapter responsibilities

```text
provider request formatting
authentication
send
provider error translation
provider result parsing
external message ID
```

---

# 44. Adapter does not decide

```text
whether Notification should exist
business priority
whether Feedback is valid
whether Opportunity should move stage
```

---

# 45. Notification Policy Engine

Canonical component:

```text
NotificationPolicyEngine
```

---

# 46. Policy evaluates

```text
notification type
priority
configured channels
recipient
quiet hours
channel availability
delivery preference
```

---

# 47. Policy output

Conceptually:

```text
channels[]
delivery_time
minimal_payload_profile
```

---

# 48. Notification intent

Application/source event expresses semantic intent:

```text
NEW_PROFESSIONAL_INQUIRY
CASTING_INVITATION_RECEIVED
ROLE_OFFER_RECEIVED
DEADLINE_APPROACHING
DELIVERY_FAILED
SYSTEM_ACTION_REQUIRED
```

---

# 49. Intent vs rendered text

Intent is stable machine/business identifier.

Rendered message is localized presentation.

---

# 50. Templates

Messages SHOULD use version-controlled templates.

---

# 51. Template ID

Examples:

```text
new-feedback:v2
casting-invitation:v1
opportunity-callback:v1
job-final-failure:v1
```

---

# 52. Template owner

Notification application/configuration layer owns template.

Provider adapter does not compose arbitrary business copy.

---

# 53. Provider template requirement

Some official messaging providers may require approved pre-registered templates.

Architecture SHOULD support mapping:

```text
internal notification template
→ provider template
```

---

# 54. Template version

Delivery record SHOULD retain relevant template/version for diagnostics where practical.

---

# 55. Minimal Notification Doctrine

External messages SHOULD default to **minimum necessary content**.

---

# 56. Example — new professional inquiry

Preferred WhatsApp:

```text
Новое профессиональное обращение: приглашение на кастинг.
Открыть в админ-панели: [protected link]
```

---

# 57. Avoid by default

Sending full:

```text
visitor message
phone
attachments
private role terms
casting document
```

through WhatsApp when unnecessary.

---

# 58. Why

Reduces:

```text
privacy exposure
provider data exposure
message length
notification clutter
```

---

# 59. Source details stay in Admin

External message links back to protected Admin detail.

---

# 60. Protected Admin Link

Canonical:

```text
https://canonical-host/admin/...
```

---

# 61. Admin link security

Link itself does NOT bypass authentication.

---

# 62. NOT-SEC-001 — Admin Deep Link Is Not Access Token

Opening link still requires valid Admin authentication/authorization.

---

# 63. Link destination

Should route directly to affected context.

Examples:

```text
/admin/inbox/{feedback}
```

or:

```text
/admin/castings/{casting}
```

---

# 64. No generic dashboard link when precise context exists

Better:

```text
Open this inquiry
```

than:

```text
Open admin homepage
```

---

# 65. Canonical host

Notification links MUST use trusted configured canonical base URL.

Never arbitrary request Host header.

---

# 66. Link leakage

Do not place secret query tokens in ordinary Admin notification links.

---

# 67. Token-scoped public link

Only when the notification explicitly relates to such public package and sharing it is intended.

Internal Admin alerts normally use authenticated Admin routes.

---

# 68. Recipient

Notification Delivery has explicit recipient target.

---

# 69. Recipient resolution

Could come from:

```text
AdminUser
system notification configuration
approved operational destination
```

---

# 70. NOT-INV-006 — Actor Contacts ≠ Admin Notification Recipient

Professional public Contact records MUST NOT automatically become system notification recipients.

---

# 71. Example

A parent/guardian Contact shown in questionnaire does not imply:

```text
send all system notifications there
```

---

# 72. Recipient settings separate

Notification recipient configuration belongs system/admin settings.

---

# 73. Phone normalization

WhatsApp recipient SHOULD use canonical normalized phone representation.

---

# 74. Email normalization

Email recipient MUST pass standard structural validation.

---

# 75. Recipient invalid

Permanent delivery failure or configuration blocker.

Do not endlessly retry.

---

# 76. Multiple Admin recipients

Future MAY support several recipients.

Architecture must avoid duplicate semantic delivery where a single destination is configured twice.

---

# 77. Notification preferences

May define per notification category:

```text
In-App on/off
WhatsApp on/off
Email on/off
quiet-hours behavior
priority override rules
```

---

# 78. In-App critical notifications

System MAY require certain categories to always remain visible In-App even if external channels disabled.

---

# 79. User preferences cannot disable required security/system safety notification if product policy marks mandatory

Exact policy later.

---

# 80. Quiet Hours

Notification external delivery SHOULD support quiet hours.

---

# 81. Canonical behavior

```text
Notification created
      ↓
Policy
      ↓
within quiet hours?
   ├─ No → PENDING/SENDING
   └─ Yes → SCHEDULED
```

---

# 82. NOT-INV-007 — Quiet Hours ≠ Failure

Status:

```text
SCHEDULED
```

not:

```text
FAILED
```

---

# 83. Quiet hours apply to channel delivery

In-App Notification may still exist immediately while WhatsApp waits.

---

# 84. Timezone

Quiet hours MUST be evaluated in configured operational timezone.

---

# 85. DST/timezone handling

Scheduling architecture must use unambiguous stored timezone/time values according to later operations/config specs.

---

# 86. Critical override

Selected CRITICAL category MAY bypass quiet hours only if explicitly configured by product/admin policy.

---

# 87. No implicit bypass

`HIGH` does not automatically mean wake-up notification unless policy says so.

---

# 88. Quiet-hour release

At next allowed time:

```text
SCHEDULED
→ SENDING
```

via durable scheduler/job.

---

# 89. Server restart

Scheduled delivery survives restart.

---

# 90. Retry architecture

External sends use durable jobs from DOC-076.

---

# 91. Retryable errors

Examples:

```text
network timeout
HTTP 429
HTTP 502/503
temporary DNS/provider outage
```

---

# 92. Permanent errors

Examples:

```text
invalid recipient
recipient explicitly unreachable where provider says permanent
invalid approved template configuration
provider account disabled
invalid credentials pending operator action
```

---

# 93. Provider authentication failure

Should usually move to operational failure requiring Admin action rather than endless retries.

---

# 94. Retry budget

Finite, channel-specific.

---

# 95. Backoff

External channels use bounded exponential backoff/jitter where appropriate.

---

# 96. Retry history

Each attempt SHOULD preserve:

```text
attempt number
started_at
finished_at
safe result/error
provider response ID if useful
```

---

# 97. Idempotency

Every NotificationDelivery MUST have durable idempotency identity.

---

# 98. Canonical key

Conceptually:

```text
notification_delivery_id
```

plus provider idempotency key when supported.

---

# 99. NOT-INV-008 — Duplicate Job Cannot Produce Duplicate Message

Repeated worker claim/retry should result in one semantic delivery where provider capabilities allow.

---

# 100. Unknown provider outcome

Special case:

```text
provider may have accepted message
response lost
```

---

# 101. Correct handling

Use:

```text
provider idempotency
provider message lookup
reconciliation
```

where supported.

---

# 102. Incorrect handling

Blind immediate resend.

---

# 103. Provider external ID

Persist when available:

```text
provider_message_id
```

---

# 104. Delivery success definition

`SENT` means provider accepted/confirmed according to adapter semantics.

It does not necessarily mean human read the message.

---

# 105. Delivered/read receipts

If provider supports them, MAY be modeled separately:

```text
DELIVERED
READ
```

but they are not required baseline.

---

# 106. External read receipt

Does not change Notification Center read state automatically unless explicit future rule says so.

---

# 107. In-App read state

Explicit user action or view semantics according to UX implementation.

---

# 108. Read ≠ resolved

A notification can be read and still require business action.

---

# 109. Notification archive

Allows decluttering Notification Center.

Archiving does not archive source Feedback/Casting.

---

# 110. Notification deletion

Routine hard delete follows retention policy.

Not used to mutate source history.

---

# 111. Deduplication

Repeated source signals can create notification spam.

Architecture SHOULD support semantic deduplication.

---

# 112. Example — failed provider

Twenty retries SHOULD NOT create twenty separate Admin Notifications saying:

```text
WhatsApp failed
```

---

# 113. Preferred

One active operational Notification/VOP Observation updated with current failure state where semantics are the same.

---

# 114. Business notifications

Distinct Feedback submissions MUST remain distinct notifications where useful.

Do not merge unrelated professional inquiries.

---

# 115. Dedup key

Conceptually:

```text
notification_type
+
source identity
+
semantic event identity/window
```

---

# 116. Dedup must not lose business events

Example:

Two different role offers are distinct even from same sender.

---

# 117. Grouping

Notification Center MAY group related routine notifications visually.

Underlying records remain traceable.

---

# 118. Digest

Future MAY support scheduled digests for LOW/NORMAL categories.

---

# 119. Digest must not delay urgent professional inquiry unless explicitly configured

---

# 120. Example digest

```text
5 content-health items require review
```

could replace five routine external WhatsApp messages.

---

# 121. External Channel Noise Policy

WhatsApp SHOULD focus on:

```text
high-value professional inbound
deadlines
important operational failures
```

not every internal event.

---

# 122. Notification types — Feedback

Recommended:

```text
NEW_CASTING_INVITATION
NEW_ROLE_OFFER
NEW_COLLABORATION_INQUIRY
NEW_MATERIALS_REQUEST
NEW_QUESTIONNAIRE_REQUEST
NEW_PROFESSIONAL_QUESTION
NEW_COMMENT
NEW_OTHER_INQUIRY
```

---

# 123. Feedback type mapping

Based on stored authoritative Feedback type.

---

# 124. Casting notifications

Potential:

```text
CASTING_CREATED
CASTING_DEADLINE_APPROACHING
CASTING_ANALYSIS_READY
CASTING_REVIEW_REQUIRED
```

---

# 125. Opportunity notifications

Potential:

```text
OPPORTUNITY_ACTION_DUE
SELF_TAPE_REQUESTED
AUDITION_DUE
CALLBACK_RECEIVED
OFFER_RECEIVED
```

---

# 126. Booked notification

MAY exist as important business event, but Opportunity itself is authority.

---

# 127. Questionnaire notifications

Potential:

```text
QUESTIONNAIRE_PDF_FAILED
QUESTIONNAIRE_READINESS_BLOCKED
CASTING_PACKAGE_READY
```

---

# 128. Media notifications

Usually only for actionable failure:

```text
CRITICAL_MEDIA_MISSING
MEDIA_PROCESSING_FINAL_FAILURE
```

Routine success should remain silent.

---

# 129. Link/QR notifications

Potential:

```text
PROFESSIONAL_LINK_INVALID
QR_VALIDATION_FAILED
```

especially if affects current questionnaire.

---

# 130. Social notifications

Potential:

```text
SOCIAL_PUBLISH_FAILED
SOCIAL_PARTIAL_FAILURE
SOCIAL_ACCOUNT_REAUTH_REQUIRED
```

---

# 131. AI notifications

AI completion SHOULD generally be local UI state rather than external notification unless long-running flow warrants it.

---

# 132. AI failure external alert

Only if operationally important.

---

# 133. VOP notification

High-priority VOP Observation can create Notification.

---

# 134. System notifications

Potential:

```text
WORKER_UNAVAILABLE
STORAGE_CAPACITY_CRITICAL
DATABASE_BACKUP_FAILED
PROVIDER_AUTH_REQUIRED
```

Operations details refined later.

---

# 135. Notification payload

Internal Notification may contain:

```text
type
priority
source reference
title key
message key/variables
admin route
created_at
```

---

# 136. External delivery payload

Should contain subset only.

---

# 137. NOT-SEC-002 — External Channel Minimization

Do not automatically send:

```text
private Feedback attachment URL
Casting confidential text
Admin notes
AI raw analysis
full Contact record
```

---

# 138. Attachment policy

Baseline WhatsApp/Email Notification SHOULD link to Admin rather than attaching sensitive source files.

---

# 139. Future email attachments

Only if separately specified with privacy/security policy.

---

# 140. WhatsApp attachment baseline

Not required.

---

# 141. Notification locale

System SHOULD support localized notification templates.

---

# 142. Admin recipient locale

Template selection may use configured Admin locale.

---

# 143. Source content language

Should not force external notification template locale unexpectedly.

---

# 144. Names

Professional names/project titles MAY remain source-language values inside localized template.

---

# 145. No AI translation at delivery

Notification renderer should not invoke generative AI just before sending.

---

# 146. Template fallback

If locale template unavailable:

use defined approved fallback.

---

# 147. Notification rendering

Canonical:

```text
Notification Intent
    ↓
Template
    ↓
Variables
    ↓
Channel-specific renderer
    ↓
Provider payload
```

---

# 148. Channel-specific rendering

WhatsApp text can differ visually from Email while semantic meaning remains same.

---

# 149. Email subject

Email has subject + body.

WhatsApp may have concise message.

In-App may have title + short body.

---

# 150. Template variables

MUST be allowlisted and safely escaped.

---

# 151. HTML email

If supported, untrusted source text must be escaped/sanitized.

---

# 152. No raw source HTML

Feedback message cannot be injected as trusted HTML.

---

# 153. Email external links

Use canonical application URLs.

---

# 154. Tracking

Notification links MAY carry safe internal attribution/correlation parameters if deliberately designed.

---

# 155. Tracking parameters must not contain private source data

---

# 156. Provider webhooks

External providers MAY send:

```text
delivery status
failure
read receipt
```

---

# 157. Webhook security

Inbound webhook MUST validate:

```text
signature/authentication
provider identity
payload structure
replay/idempotency
```

---

# 158. Webhook cannot mutate source business entity directly

It may update:

```text
NotificationDelivery
```

only according to channel semantics.

---

# 159. NOT-SEC-003 — Provider Webhook Has Narrow Authority

WhatsApp webhook cannot:

```text
resolve Feedback
book Opportunity
publish Questionnaire
```

---

# 160. Webhook replay

Same event received twice MUST be idempotent.

---

# 161. Webhook unknown delivery

If no matching `NotificationDelivery`:

log/reconcile safely.

Do not create arbitrary business entity.

---

# 162. Notification Center Projection

Canonical fields:

```text
notification ID
type
priority
title
short message
source type
source identity
source route
read state
created_at
delivery summary where relevant
```

---

# 163. Notification Center filters

Recommended:

```text
Unread
All
Professional
Deadlines
System
Delivery Issues
Archived
```

Exact UX later.

---

# 164. Notification item action

Primary action should open source.

---

# 165. Example

```text
Новое приглашение на кастинг
[Открыть обращение]
```

---

# 166. Notification item must not duplicate whole Feedback form content

Summary + direct context route preferred.

---

# 167. Delivery warning

Notification Center MAY show:

```text
WhatsApp не доставлен
```

without turning entire Notification into failure.

---

# 168. Retry from Notification Center

Admin MAY retry failed delivery if safe.

Action routes to Notification Application Service.

---

# 169. Retry button

Should identify channel:

```text
Повторить WhatsApp
```

not ambiguous:

```text
Повторить
```

---

# 170. Bulk mark read

Allowed.

Does not alter source business states.

---

# 171. Mark all read

Allowed if UX desires.

---

# 172. Read state concurrency

Low-risk optimistic UI may be acceptable.

Server remains authority.

---

# 173. Delivery mutation concurrency

Provider/job status updates should use version/state validation where needed.

---

# 174. Notification preferences concurrency

Standard expected-version/config update protections.

---

# 175. Notification creation transaction

Preferred source business flow:

```text
BEGIN
  source mutation
  outbox event
COMMIT
```

Then Notification created asynchronously.

---

# 176. Alternative critical In-App creation

Notification record MAY be created in same transaction as source when this materially simplifies guaranteed local notification.

But source commit remains authoritative.

---

# 177. Preferred separation

Use domain event/outbox to keep notification policy decoupled from domain writes.

---

# 178. Notification creation failure

If outbox exists:

retry later.

Source business record remains.

---

# 179. Outbox idempotency

Same source event processed twice should not duplicate same Notification unintentionally.

---

# 180. Notification key

Stable source-event identity assists deduplication.

---

# 181. Delivery creation

After Notification policy:

```text
Notification
  ↓
Delivery records per channel
```

---

# 182. Channel disabled

No Delivery created, or record may show NOT_CONFIGURED depending physical model.

Prefer not to misrepresent disabled as FAILED.

---

# 183. Provider unavailable

Delivery created and fails/retries according to policy.

---

# 184. Missing credentials

Configuration blocker, not endless transient retry.

---

# 185. Template unapproved/provider mismatch

Configuration failure requiring Admin/System action.

---

# 186. Recipient missing

If channel configured but no valid recipient:

explicit configuration warning.

---

# 187. In-App as fallback

Important Notification remains available In-App.

---

# 188. Notification channel health

System Status MAY expose:

```text
In-App: Healthy
WhatsApp: Degraded
Email: Healthy
```

---

# 189. Channel outage isolation

WhatsApp outage MUST NOT stop:

```text
Email
In-App
PDF
Media
Search
AI
```

---

# 190. Provider circuit protection

Repeated provider outage MAY temporarily throttle/pause attempts.

Durable deliveries retained.

---

# 191. Resume

When provider recovers:

eligible deliveries retry according to policy.

---

# 192. Late notifications

Some notifications become stale.

---

# 193. Example

Casting deadline passed while WhatsApp offline.

System SHOULD not blindly send:

```text
Deadline in 2 hours
```

three days later.

---

# 194. Delivery relevance revalidation

Before very delayed delivery, policy MAY revalidate whether message is still meaningful.

---

# 195. Stale message handling

Possible:

```text
cancel obsolete delivery
replace with updated summary notification
```

according to type.

---

# 196. NOT-INV-009 — Retry Does Not Guarantee Same Message Remains Relevant Forever

Channel policy must support expiry/relevance.

---

# 197. Notification expiry

Certain Delivery intents may have:

```text
expires_at
```

---

# 198. Example

```text
audition reminder
deadline reminder
```

---

# 199. Source business notification

New Feedback itself usually remains relevant even if delivery delayed; exact wording may become:

```text
You have an unread professional inquiry
```

rather than time-sensitive claim.

---

# 200. Quiet hours + expiry

If delivery would expire before quiet hours end, policy determines:

```text
bypass if critical
cancel
deliver In-App only
```

explicitly.

---

# 201. Notification grouping windows

Optional future.

Example:

10 technical retry failures over 5 minutes → one grouped alert.

---

# 202. Business event grouping restriction

Never combine distinct Casting invitations into one record that obscures provenance.

---

# 203. Provider credentials

Stored in Secret Provider/config.

---

# 204. NOT-SEC-004 — No Credentials in DB Message Payload

Do not put:

```text
WhatsApp token
SMTP password
OAuth secret
```

inside Notification/Delivery/job payload.

---

# 205. Credentials rotation

Adapter retrieves current valid secret/config.

Rotation should not require rewriting pending Notification records.

---

# 206. Email SMTP/API provider

Implementation may use SMTP or approved Email API provider.

Architecture remains adapter-based.

---

# 207. Sender identity

Configured in system settings/environment.

Not derived from arbitrary public Contact field.

---

# 208. WhatsApp sender

Configured approved business sender.

---

# 209. Delivery privacy logs

Operational logs SHOULD contain:

```text
delivery ID
channel
status
provider request/result ID
error category
```

not full sensitive message where avoidable.

---

# 210. Message body logging

Avoid production logging by default.

---

# 211. Provider raw response

Store only safe fields needed for diagnostics.

---

# 212. PII in provider errors

Sanitize before displaying/logging.

---

# 213. Audit

Business/user actions worth Audit:

```text
notification preferences changed
manual retry requested
recipient configuration changed
channel enabled/disabled
```

---

# 214. Routine send attempts

Usually belong Delivery history, not AuditEvent.

---

# 215. Notification read

May be ordinary operational state without heavy Audit.

---

# 216. Provider credential change

Security/config audit recommended.

---

# 217. Security boundary

External providers are untrusted network dependencies.

---

# 218. Outbound request

Must use:

```text
TLS
bounded timeout
validated endpoint/config
```

---

# 219. SSRF

Provider endpoint should come from trusted configuration.

Not arbitrary Notification data.

---

# 220. Message injection

Variables escaped/encoded according to channel.

---

# 221. URL safety

Only trusted canonical/protected application links.

---

# 222. Public Feedback message

Do not reflect untrusted message into external channel in ways that could masquerade as system instructions.

---

# 223. Recommended external copy

Structured fixed template + minimal source label.

---

# 224. In-App can expose more context

Because Admin is authenticated, but still should use source link rather than duplicate entire sensitive entity unnecessarily.

---

# 225. Notification rate limiting

Outbound channel needs provider-aware throttling.

---

# 226. Professional inbound burst

If many inquiries arrive:

all records are persisted.

Notification layer may batch external alerts if configured without losing In-App records.

---

# 227. Notification DoS

Public user cannot directly choose arbitrary WhatsApp recipient/message.

They create allowed Feedback; server policy decides Notification.

---

# 228. NOT-SEC-005 — Public Form Cannot Become Messaging Relay

Prohibited flow:

```text
visitor supplies phone + arbitrary text
→ system sends that text via WhatsApp to arbitrary number
```

---

# 229. Notification sender/recipient are system-controlled

Normative.

---

# 230. Attachment malware

Notification subsystem should not auto-forward unscanned inbound attachments.

---

# 231. Protected source access

Admin link opens attachment only after authorization/private media flow.

---

# 232. Notification retention

Must distinguish:

```text
Notification
Delivery attempts
provider metadata
read state
```

---

# 233. Retention durations

Defined later in DOC-094.

---

# 234. Feedback deletion/redaction

Notification summary/provenance may require corresponding privacy redaction.

---

# 235. Historical delivery log

Must not preserve prohibited sensitive payload solely for diagnostics.

---

# 236. Provider message IDs

May remain for operational traceability subject to policy.

---

# 237. Notification templates retention

Version-control history provides template provenance.

---

# 238. Backup

Notification/Delivery records are persistent operational data and belong DB backup.

---

# 239. External provider is not backup

Provider history does not replace application records.

---

# 240. Restore

After database restore:

Delivery records must not all be blindly resent.

---

# 241. NOT-INV-010 — Restore Does Not Re-Deliver Historical Messages Automatically

Pending/ambiguous deliveries require reconciliation policy.

---

# 242. Restore reconciliation

Classify:

```text
SENT
FAILED_FINAL
PENDING
UNKNOWN
```

before resuming.

---

# 243. Notification migration

Existing ad hoc messaging code must migrate to central Notification subsystem.

---

# 244. No module-specific direct provider calls

Prohibited:

```text
FeedbackService → WhatsApp SDK directly
CastingService → SMTP directly
```

---

# 245. Correct

```text
FeedbackService
→ event/outbox
→ Notification subsystem
→ channel adapter
```

---

# 246. Provider SDK location

Infrastructure adapter only.

---

# 247. Notification APIs

Exact API contracts deferred to DOC-100–103.

---

# 248. Internal command examples

```text
CreateNotificationFromEvent
MarkNotificationRead
ArchiveNotification
RetryDelivery
UpdateNotificationPreferences
```

---

# 249. Delivery worker

Uses durable jobs:

```text
NOTIFICATION_DELIVER
```

---

# 250. Worker payload

Prefer:

```text
delivery_id
```

---

# 251. Worker loads

```text
Delivery
Notification
template
recipient
channel config
```

under current approved policy.

---

# 252. Message freeze semantics

For high-value notifications, exact message/template variables may need to be frozen at delivery creation.

---

# 253. Why

Avoid a retry hours later silently changing meaning because source entity changed.

---

# 254. Two valid strategies

## Event Snapshot

Delivery stores safe semantic snapshot/template variables.

## Current Summary

Retry deliberately regenerates current summary.

---

# 255. Strategy must be type-specific

Example:

New Feedback alert SHOULD retain identity of original Feedback.

Deadline reminder MAY re-evaluate current deadline relevance.

---

# 256. No silent semantic drift

Normative.

---

# 257. Notification template variables

Conceptually:

```text
actor_name
notification_type
source_title
deadline
admin_url
```

Only approved variables.

---

# 258. Do not expose internal IDs as professional copy

IDs may exist in links/system metadata but not dominate message.

---

# 259. Phone/WhatsApp public Contact

Do not send public Contact record values externally unless directly required.

Admin recipient config is separate.

---

# 260. Opportunity Notification

If stage changes:

```text
OpportunityStageChanged
```

could create In-App notification.

External notification only according to configured importance.

---

# 261. No duplicate stage notification from analytics

Analytics event does not trigger business Notification independently if authoritative domain event already exists.

---

# 262. Event authority

Use source domain event.

---

# 263. Notification and AI

AI MAY help draft notification templates offline/admin later, but production delivery copy SHOULD be deterministic approved template.

---

# 264. NOT-INV-011 — No Per-Delivery Generative AI Required

Notification delivery must work if AI unavailable.

---

# 265. AI-generated summaries

If ever introduced:

must be clearly optional, validated and not required for business delivery.

---

# 266. Notification and Search

Notification Center may have local filter/search later.

It must not be mixed into Public Search.

---

# 267. Notification and Cache

Notification Center dynamic read model should use minimal caching.

---

# 268. External message cache

Not needed.

---

# 269. Notification and Analytics

May capture:

```text
notification created
delivery sent
delivery failed
notification opened
source opened
```

---

# 270. Delivery Analytics Is Not Business Conversion

WhatsApp SENT does not equal inquiry handled.

---

# 271. Notification opened

Does not equal Feedback resolved.

---

# 272. Metrics

Recommended operational:

```text
notifications created
deliveries by channel
send success rate
retry rate
final failures
queue age
delivery latency
quiet-hour scheduled count
provider rate limits
```

---

# 273. Product metrics

Could include:

```text
time from Feedback creation to Admin open
time to professional next action
```

but source events remain business authority.

---

# 274. SLA

Notification performance targets defined later in NFR/operations.

---

# 275. Monitoring

Alert on:

```text
critical channel unavailable
high failure rate
queue backlog
provider auth failure
unusually old HIGH deliveries
```

---

# 276. System Status

Shows independent status:

```text
In-App
WhatsApp
Email
```

---

# 277. VOP integration

VOP may observe:

```text
delivery failed repeatedly
provider authentication invalid
quiet-hour backlog abnormal
recipient missing
notification queue stuck
```

---

# 278. VOP safe automation

May:

```text
retry transient failed delivery
recheck provider
clear resolved technical observation
```

under AUTO-4 policy.

---

# 279. VOP cannot

```text
change recipient
enable external channel
send confidential content
```

without Human/config authority.

---

# 280. Notification UI accessibility

Notification Center MUST support:

```text
semantic list
keyboard navigation
read/unread text state
priority not color-only
accessible timestamps
meaningful action link
```

---

# 281. Live notifications

If real-time UI updates later:

screen-reader live-region announcements should be concise and non-spammy.

---

# 282. Notification badge

Unread count MAY appear in Admin shell.

---

# 283. Badge semantics

Must have accessible label:

```text
5 непрочитанных уведомлений
```

---

# 284. Badge count ≠ tasks count

Dashboard Action Required count remains separate.

---

# 285. Notification timestamp

Display timezone-aware formatted time.

---

# 286. External message accessibility

Text messages should use clear concise language and descriptive link context.

---

# 287. Email accessibility

If HTML email used:

```text
semantic structure
meaningful links
sufficient contrast
no image-only essential message
```

---

# 288. Failure UX

Admin should see:

```text
Notification created
WhatsApp delivery failed
In-App available
[Retry WhatsApp]
```

---

# 289. Avoid generic

```text
Something went wrong
```

when channel/error class known.

---

# 290. Notification readiness

System configuration readiness may include:

```text
recipient valid
provider configured
template available
channel enabled
```

---

# 291. Channel misconfiguration

Should be surfaced before relying on it for critical alerts.

---

# 292. Test notification

Admin settings MAY support:

```text
Send Test Notification
```

---

# 293. Test Notification semantics

Must be explicitly labeled test and not linked to fake business event.

---

# 294. Test delivery

Uses same adapter path where possible.

---

# 295. Test failure

Does not create professional Feedback/Casting.

---

# 296. Test recipient

Uses configured recipient or explicitly permitted settings recipient.

---

# 297. Provider sandbox

May be supported during development.

Production settings must be explicit.

---

# 298. Environment isolation

Dev/staging MUST NOT accidentally send production professional notifications.

---

# 299. NOT-SEC-006 — Environment Delivery Safety

Recommended:

```text
environment-specific provider config
recipient allowlist in non-production
visible test prefix
```

---

# 300. Staging message

Example:

```text
[STAGING] Новое тестовое обращение
```

---

# 301. No production recipient by accident

Non-prod protections required in deployment plan.

---

# 302. Notification deployment

Web and Worker may deploy independently.

---

# 303. Template compatibility

Queued Delivery created under template version N must remain renderable/safe after deploy N+1.

---

# 304. Recommended

Freeze:

```text
template_version
semantic variables
```

at delivery creation where important.

---

# 305. Provider adapter upgrade

Must not cause old pending Delivery to become unreadable.

---

# 306. Provider migration

Changing WhatsApp provider:

pending deliveries require explicit migration/retry/reconciliation plan.

---

# 307. No duplicate migration sends

Use existing delivery semantic ID/idempotency.

---

# 308. Notification state machine ownership

Notification Domain owns notification/delivery lifecycle.

---

# 309. Provider response cannot invent new Notification

It only updates relevant Delivery.

---

# 310. Source deletion

If source entity becomes inaccessible:

Notification may remain with:

```text
Source no longer available
```

according to retention/privacy policy.

---

# 311. Source route

Must gracefully handle archived/deleted/redacted source.

---

# 312. Notification history should not become backdoor to deleted private data

Normative.

---

# 313. Notification Data Matrix

| Data | Owner | External by default |
|---|---|---:|
| Notification type | Notification | Yes |
| Priority | Notification | Maybe |
| Source ID | Internal | No raw |
| Source title | Source/snapshot | Minimal |
| Full Feedback body | Feedback | No |
| Admin URL | Application | Yes |
| Private attachment | Feedback/Casting | No |
| Delivery status | Notification | No |
| Provider message ID | Delivery | No |
| Contact data | Contact | No by default |

---

# 314. Channel Matrix

| Capability | In-App | WhatsApp | Email |
|---|---:|---:|---:|
| New Casting | Yes | Recommended | Optional |
| Role Offer | Yes | Recommended | Optional |
| Generic Comment | Yes | Configurable | Optional |
| System Failure | Yes | Configurable | Optional |
| Routine Success | Usually No | No | No |
| Deadline | Yes | Configurable | Configurable |

---

# 315. Quiet Hours Matrix

| Notification | Quiet Hours Default |
|---|---|
| Casting invitation | Respect unless explicit critical override |
| Role offer | Respect unless configured |
| Generic question | Respect |
| Content health | Respect / digest |
| Critical system safety | Policy-specific |
| In-App | Immediate record |

---

# 316. Failure Matrix

| Failure | Behaviour |
|---|---|
| WhatsApp timeout | Retry |
| WhatsApp 429 | Retry/backoff |
| Invalid recipient | Final/config issue |
| Provider auth invalid | Final/config alert |
| Email temporary 5xx | Retry |
| Template missing | Configuration failure |
| DB unavailable | Do not uncontrolled-send |
| Outbox delayed | Source remains safe |
| Worker unavailable | Delivery remains queued |
| Quiet hours | Schedule, not fail |

---

# 317. Notification Anti-Pattern NOT-AP-001

**Notify before saving Feedback**

---

# 318. NOT-AP-002

**WhatsApp failure deletes inbound inquiry**

---

# 319. NOT-AP-003

**Feedback Inbox is implemented only as notification list**

---

# 320. NOT-AP-004

**Reading Notification resolves Feedback**

---

# 321. NOT-AP-005

**One status shared for all channels**

---

# 322. NOT-AP-006

**Quiet hours stored as FAILED**

---

# 323. NOT-AP-007

**Infinite notification retries**

---

# 324. NOT-AP-008

**Blind resend after unknown provider outcome**

---

# 325. NOT-AP-009

**Full Casting source pasted into WhatsApp by default**

---

# 326. NOT-AP-010

**Private Feedback attachments auto-forwarded to external provider**

---

# 327. NOT-AP-011

**Admin deep link itself bypasses login**

---

# 328. NOT-AP-012

**Public Contact automatically becomes Admin alert recipient**

---

# 329. NOT-AP-013

**Public visitor can choose WhatsApp recipient**

---

# 330. NOT-AP-014

**Provider SDK called directly from FeedbackService**

---

# 331. NOT-AP-015

**Provider API key stored in Delivery payload**

---

# 332. NOT-AP-016

**Production sends every thumbnail completion via WhatsApp**

---

# 333. NOT-AP-017

**Old deadline reminder sent days late without relevance check**

---

# 334. NOT-AP-018

**External delivery success marks Opportunity handled**

---

# 335. NOT-AP-019

**Analytics event creates duplicate business notification independently of Domain event**

---

# 336. NOT-AP-020

**Provider webhook can mutate Opportunity**

---

# 337. NOT-AP-021

**Provider webhook replay creates duplicate state transitions**

---

# 338. NOT-AP-022

**Generic cache publicly stores token-scoped Notification detail**

---

# 339. NOT-AP-023

**Non-production environment sends unrestricted production notifications**

---

# 340. NOT-AP-024

**Restore resends all historical notifications**

---

# 341. NOT-AP-025

**AI generates delivery wording at send time and changes approved meaning**

---

# 342. Notification Quality Gate

Перед production implementation MUST быть определены:

- [ ] Notification entity;
- [ ] NotificationDelivery entity;
- [ ] source-event mapping;
- [ ] channel list;
- [ ] In-App baseline;
- [ ] WhatsApp official/approved integration;
- [ ] Email optional integration;
- [ ] Notification Policy Engine;
- [ ] priority classes;
- [ ] recipient configuration;
- [ ] channel preferences;
- [ ] quiet hours;
- [ ] timezone;
- [ ] critical override policy;
- [ ] template IDs/versions;
- [ ] minimal external payload;
- [ ] protected Admin link builder;
- [ ] channel adapter contracts;
- [ ] durable delivery jobs;
- [ ] retry classes;
- [ ] idempotency;
- [ ] unknown-outcome reconciliation;
- [ ] provider webhook validation;
- [ ] delivery expiration/relevance;
- [ ] notification deduplication;
- [ ] Admin Notification Center;
- [ ] privacy/logging controls;
- [ ] non-production safeguards;
- [ ] metrics/health;
- [ ] retention/recovery;
- [ ] E2E tests.

---

# 343. Notification Specification Template

```text
Notification Type:
NEW_CASTING_INVITATION

Source Event:
FeedbackCreated

Source Condition:
feedback.type = casting_invitation

Priority:
HIGH

In-App:
Yes

WhatsApp:
Yes, configurable

Email:
Optional

External Payload:
minimal

Admin Route:
/admin/inbox/{feedback-id}

Quiet Hours:
respect by default

Expiry:
none / type-specific

Dedup:
source Feedback ID

Business Authority:
Feedback

Delivery Failure Effect:
none on Feedback
```

---

# 344. Delivery Specification Template

```text
Channel:
WHATSAPP

Notification ID:
...

Recipient:
resolved system recipient

Template:
new-feedback:v2

Scheduled At:
...

Idempotency Key:
delivery_id

Max Attempts:
...

Provider:
...

External Message ID:
...

Status:
PENDING / SCHEDULED / SENDING / SENT / FAILED

Source Mutation:
prohibited
```

---

# 345. WhatsApp Adapter Specification Template

```text
Channel:
WHATSAPP

Provider Type:
official/approved business provider

Credentials:
Secret Provider

Input:
rendered minimal message
recipient
idempotency identity

Timeout:
bounded

Retry:
429 / transient 5xx / timeout

Permanent:
invalid recipient
invalid template/config

Result:
provider_message_id
accepted status

Webhook:
signature-validated
```

---

# 346. E2E-NOT-001 — Feedback Persistence

Disable all external providers.

Submit Casting Feedback.

Expected:

```text
Feedback persisted
visible in Inbox
In-App Notification available
external delivery failure does not lose inquiry
```

---

# 347. E2E-NOT-002 — WhatsApp Success

Create qualifying Feedback.

One WhatsApp Delivery reaches `SENT`.

Feedback remains independent.

---

# 348. E2E-NOT-003 — Retry

Provider returns 503 twice, succeeds third time.

Expected:

```text
one semantic message
three attempt records
Delivery = SENT
```

---

# 349. E2E-NOT-004 — Invalid Recipient

Invalid configured number.

Delivery reaches terminal/config failure.

No infinite retry.

---

# 350. E2E-NOT-005 — Quiet Hours

Create HIGH non-critical Notification during quiet hours.

Expected:

```text
In-App immediate
WhatsApp SCHEDULED
send after allowed window
```

---

# 351. E2E-NOT-006 — Restart During Quiet Hours

Restart server before scheduled release.

Delivery still occurs from durable schedule.

---

# 352. E2E-NOT-007 — Channel Independence

WhatsApp fails, Email succeeds.

Notification summary = partial/degraded delivery.

---

# 353. E2E-NOT-008 — Notification Read

Mark Notification read.

Feedback remains in existing workflow state.

---

# 354. E2E-NOT-009 — Feedback Resolved

Resolve Feedback.

Notification read state does not have to change automatically unless explicit UI policy says so.

---

# 355. E2E-NOT-010 — Protected Admin Link

Open WhatsApp Admin URL while unauthenticated.

Expected:

```text
authentication required
no Feedback content exposed
```

---

# 356. E2E-NOT-011 — Minimal Payload

Inspect outbound WhatsApp request.

Full Feedback attachment/message/private notes absent unless explicitly required.

---

# 357. E2E-NOT-012 — Public Relay Attack

Visitor submits arbitrary phone/message.

System cannot send visitor-controlled WhatsApp to arbitrary recipient.

---

# 358. E2E-NOT-013 — Duplicate Worker Execution

Process same Delivery job twice.

No duplicate semantic message where provider/idempotency support allows prevention.

---

# 359. E2E-NOT-014 — Unknown Outcome

Provider accepts send; worker loses response.

Retry uses idempotency/reconciliation before second send.

---

# 360. E2E-NOT-015 — Provider Webhook Replay

Receive same `delivered` webhook twice.

Delivery state remains correct without duplicate records/actions.

---

# 361. E2E-NOT-016 — Provider Webhook Authority

Malicious/valid provider webhook cannot modify Feedback/Casting/Opportunity business state.

---

# 362. E2E-NOT-017 — Obsolete Deadline

Deadline reminder queued.

System outage lasts beyond deadline.

On recovery obsolete reminder is cancelled/updated according to policy, not blindly sent.

---

# 363. E2E-NOT-018 — Template Injection

Feedback contains HTML/script-like content.

Notification rendering escapes it safely.

---

# 364. E2E-NOT-019 — Secret Inspection

Inspect Notification/Delivery/job records.

No provider API secrets.

---

# 365. E2E-NOT-020 — Non-Production Safety

Staging environment cannot silently send ordinary alerts to unrestricted production recipient list.

---

# 366. E2E-NOT-021 — Provider Outage Isolation

WhatsApp adapter unavailable.

Email/In-App and unrelated Worker jobs continue.

---

# 367. E2E-NOT-022 — Notification Dedup

Repeated technical event for same unresolved WhatsApp configuration failure does not produce notification flood.

---

# 368. E2E-NOT-023 — Separate Business Events

Two distinct Casting Feedback records generate two distinct professional Notifications.

---

# 369. E2E-NOT-024 — Restore

Restore DB containing historical SENT deliveries.

System does not resend them.

---

# 370. E2E-NOT-025 — Accessibility

Keyboard/screen-reader user can:

```text
identify unread state
read notification title
understand priority
open source
mark read
retry failed channel
```

---

# 371. E2E-NOT-026 — Locale

Configured RU recipient receives RU approved template.

Switch to EN configuration; future notifications use EN template without rewriting history.

---

# 372. E2E-NOT-027 — Admin Recipient vs Public Contact

Change public parent Contact.

Admin notification recipient remains unchanged unless Notification settings are explicitly edited.

---

# 373. E2E-NOT-028 — Retry Exact Source

Retry failed Notification for Feedback A.

It cannot accidentally resolve/use Feedback B due to current Inbox ordering.

---

# 374. E2E-NOT-029 — Social Failure Notification

TikTok publish fails.

Notification links to exact SocialPost/attempt context.

Does not republish automatically merely because Notification is opened.

---

# 375. E2E-NOT-030 — VOP Integration

Repeated delivery failure creates one meaningful VOP/operational action item with correct source/channel.

---

# 376. Notification Traceability

Canonical:

```text
SOURCE DOMAIN
     ↓
DOMAIN EVENT
     ↓
OUTBOX
     ↓
NOTIFICATION
     ↓
DELIVERY
     ↓
PROVIDER
     ↓
DELIVERY RESULT
```

---

# 377. Feedback Traceability

```text
Public Form
   ↓
Feedback
   ↓
COMMIT
   ↓
FeedbackCreated
   ↓
Notification
   ├─ In-App
   ├─ WhatsApp
   └─ Email
```

---

# 378. Deadline Traceability

```text
Casting / Opportunity deadline
       ↓
Scheduler / VOP detector
       ↓
Notification intent
       ↓
Policy / quiet hours
       ↓
Delivery
```

---

# 379. Failure Traceability

```text
NotificationDelivery
      ↓
Attempt 1
      ↓
Attempt 2
      ↓
FAILED_FINAL
      ↓
Notification Center / VOP
```

---

# 380. Admin Link Traceability

```text
Notification Source
      ↓
Canonical Admin Route Builder
      ↓
External Minimal Message
      ↓
Authenticated Admin Route
      ↓
Authoritative Source Workspace
```

---

# 381. Notification Architecture Diagram

```text
                  AUTHORITATIVE DOMAIN
                          │
                          ▼
                     DB COMMIT
                          │
                          ▼
                        OUTBOX
                          │
                          ▼
                 NOTIFICATION POLICY
                          │
                          ▼
                    NOTIFICATION
                          │
           ┌──────────────┼──────────────┐
           ▼              ▼              ▼
        IN-APP         WHATSAPP         EMAIL
           │              │              │
           ▼              ▼              ▼
         SENT       DELIVERY JOB      DELIVERY JOB
                          │              │
                          ▼              ▼
                      PROVIDER        PROVIDER
                          │              │
                          ▼              ▼
                   SENT / FAILED   SENT / FAILED
```

---

# 382. Quiet Hours Architecture

```text
Notification
    ↓
Delivery Policy
    ↓
Current local time
    ↓
Quiet Hours?
 ┌──┴───┐
 No     Yes
 │       │
 ▼       ▼
PENDING SCHEDULED
 │       │
 ▼       ▼
SEND   Scheduler
         │
         ▼
       SEND
```

---

# 383. Privacy Architecture

```text
FULL SOURCE DATA
      │
      ▼
NOTIFICATION INTENT
      │
      ▼
MINIMAL MESSAGE PROJECTION
      │
      ├────────► In-App summary
      │
      ├────────► WhatsApp
      │
      └────────► Email
                       │
                       ▼
               PROTECTED ADMIN LINK
                       │
                       ▼
             AUTHENTICATED FULL SOURCE
```

---

# 384. Notification Compliance Criteria

Реализация соответствует DOC-080, если:

1. Notification and source business entities are separate;
2. NotificationDelivery is separate from Notification;
3. business record persists before optional delivery;
4. Feedback remains safe during provider failure;
5. In-App notification works independently of external providers;
6. WhatsApp uses approved/official business integration;
7. provider calls live behind adapters;
8. source Domains do not call providers directly;
9. external payload is minimized;
10. protected Admin links require authentication;
11. Admin link does not contain secret bypass token;
12. public Contacts do not automatically become Admin notification recipients;
13. quiet hours create SCHEDULED state rather than failure;
14. scheduler is durable;
15. retries are finite and classified;
16. delivery jobs are idempotent;
17. unknown provider outcome has reconciliation strategy;
18. delivery states are channel-independent;
19. successful one channel is preserved when another fails;
20. provider webhook has narrow authority and replay protection;
21. reading Notification does not resolve Feedback;
22. opening Notification does not mutate business stage;
23. routine technical successes do not create alert noise;
24. stale/obsolete delayed notifications can be cancelled or updated safely;
25. provider secrets are isolated;
26. notification templates are controlled/versioned;
27. AI is not required at delivery time;
28. non-production delivery safeguards exist;
29. system exposes provider/channel health;
30. delivery failures are visible in Admin/VOP;
31. notification restoration does not resend historical SENT messages;
32. Notification Center is accessible;
33. localization is supported deterministically;
34. provider logs do not unnecessarily retain private payloads;
35. source→notification→delivery→provider traceability is reconstructible.

---

# 385. Финальная архитектурная доктрина

> **Notification subsystem не должен быть механизмом хранения обращений или принятия бизнес-решений. Сначала система надёжно фиксирует Feedback, Casting, Opportunity или другое авторитетное состояние, затем создаёт Notification и независимо доставляет его через In-App, WhatsApp или Email. Внешние каналы получают только минимальную информацию и защищённую ссылку в Admin; quiet hours, retries и provider outages влияют только на доставку, но никогда не способны уничтожить, отменить или изменить исходный профессиональный факт.**