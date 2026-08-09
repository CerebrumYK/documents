# FEEDBACK & INQUIRIES MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация входящих профессиональных обращений, Inbox workflow, private attachments, persist-before-notify и преобразования Inquiry → Casting

**Целевой файл:** `docs/modules/feedback.md`  
**Документ:** DOC-124  
**Статус:** ✅ Completed  
**Тип:** Module / Feedback / Inquiry / Professional Inbox / Attachments / Casting Conversion

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
- `docs/modules/contacts.md`
- `docs/modules/questionnaires.md`
- `docs/modules/public-questionnaire-builder.md`
- `docs/modules/castings.md`
- `docs/modules/casting-ai-analysis.md`

---

# 1. Назначение модуля

Feedback & Inquiries Module является единым входящим professional inbox сайта.

Модуль принимает и хранит:

- приглашения на кастинг;
- предложения роли;
- предложения сотрудничества;
- запросы анкеты;
- запросы дополнительных материалов;
- профессиональные вопросы;
- комментарии;
- иные обращения.

Основная задача:

> **ни одно профессионально значимое обращение не должно потеряться из-за ошибки email, WhatsApp, AI, background worker или внешнего provider.**

---

# 2. Главная доктрина

> **Сначала обращение валидируется и надёжно сохраняется в PostgreSQL вместе с Audit/Outbox. Только после COMMIT создаются и отправляются уведомления. Доставка уведомления является побочным operational effect и никогда не является условием существования Feedback.**

Canonical:

```text
Public Visitor
     ↓
Feedback Submission
     ↓
Transport Validation
     ↓
Anti-Abuse
     ↓
Domain Validation
     ↓
DB TRANSACTION
 ┌──────────────────────────────┐
 │ Feedback                    │
 │ Attachment references       │
 │ Audit                       │
 │ Outbox                      │
 └──────────────────────────────┘
     ↓
   COMMIT
     ↓
Success response
     ↓
Async Notification Intent
     ↓
In-App / WhatsApp / Email
```

---

# 3. Fundamental separation

```text
Feedback
≠ Contact
≠ Notification
≠ Casting
≠ Opportunity
≠ Support Ticket
≠ Social Message
≠ AI Draft
```

---

# 4. Module identifiers

Используются:

```text
FBK-*
FBK-TYPE-*
FBK-SRC-*
FBK-ATT-*
FBK-READ-*
FBK-WF-*
FBK-RESP-*
FBK-NOTE-*
FBK-NXT-*
FBK-CST-*
FBK-NOT-*
FBK-SEC-*
FBK-ABUSE-*
FBK-AI-*
FBK-INV-*
FBK-AP-*
E2E-FBK-*
```

---

# 5. Feedback types

Canonical controlled values:

```text
CASTING_INVITATION
ROLE
COLLABORATION
QUESTIONNAIRE
MATERIALS
QUESTION
COMMENT
OTHER
```

---

# 6. CASTING_INVITATION

Incoming invitation or casting brief that may later be converted into Casting.

---

# 7. ROLE

Concrete role/job proposition.

---

# 8. COLLABORATION

Professional collaboration proposal not necessarily tied to a casting.

---

# 9. QUESTIONNAIRE

Request for:

- actor questionnaire;
- specific questionnaire variant;
- PDF;
- profile information.

---

# 10. MATERIALS

Request for:

- additional photographs;
- showreel;
- video;
- self-tape;
- audio;
- portfolio;
- other professional materials.

---

# 11. QUESTION

Professional question requiring response.

---

# 12. COMMENT

Comment or message that does not imply a specific requested action.

---

# 13. OTHER

Only for legitimate professional inquiry outside the controlled taxonomy.

---

# 14. FBK-TYPE-001 — Type Is Structured

Do not persist only:

```text
subject = "Casting"
```

without controlled `feedback_type`.

---

# 15. Unknown intent

If visitor selects/uses generic form and exact intent cannot be safely classified:

```text
OTHER
```

or explicit unclassified state.

AI must not silently decide a high-impact type such as CASTING_INVITATION.

---

# 16. FBK-TYPE-002 — AI Classification Is Suggestion Only

---

# 17. Feedback entity

Representative:

```text
feedback
```

Conceptual fields:

```text
id
profile_id
feedback_type
sender_name?
sender_company?
sender_email?
sender_phone?
sender_contact_preference?
subject?
message
source_context
workflow_state
read_state
priority?
responsible_admin_id?
next_action?
next_action_at?
version
created_at
updated_at
closed_at?
archived_at?
```

---

# 18. Input principle

Only information genuinely supplied by visitor or safely derived from request context is stored as incoming Source.

---

# 19. FBK-INV-001 — Incoming Message Is Source

Internal summary, AI classification and Admin interpretation are separate.

---

# 20. Visitor identity

Sender MAY provide:

```text
name
company/agency
email
phone
```

according to form requirements.

---

# 21. Required sender data

Must be minimal.

The system should not require unnecessary personal information merely to submit an inquiry.

---

# 22. Recommended baseline

Require at least one usable reply/contact mechanism only where the selected inquiry type requires a response.

---

# 23. Anonymous comment

Product MAY permit no-reply Comment depending anti-abuse policy.

---

# 24. FBK-SEC-001 — Do Not Over-Collect Contact Data

---

# 25. Contact preference

Optional visitor preference:

```text
EMAIL
PHONE
WHATSAPP
OTHER
```

This is request context.

It does not create Contacts Directory configuration.

---

# 26. FBK-INV-002 — Sender Contact ≠ Profile Contact

---

# 27. Sender normalization

Email/phone can be normalized for operational use.

Original/display value should remain semantically reconstructable where needed.

---

# 28. No automatic Contacts promotion

Incoming sender information never automatically creates:

```text
ContactEntry
ContactMethod
```

---

# 29. FBK-INV-003 — Incoming Sender Is Not Public Contact Source

---

# 30. Source context

Feedback may record where submission originated.

Examples:

```text
PUBLIC_CONTACT_FORM
PROFILE_CTA
QUESTIONNAIRE
PUBLIC_BUILDER
CASTING_SPECIFIC_LINK
MATERIAL_REQUEST
OTHER
```

---

# 31. Attribution

May also include:

```text
direct
search
social
campaign
shared_questionnaire
casting_link
```

as analytics/provenance metadata.

---

# 32. FBK-SRC-001 — Source Context Is Operational Provenance

Not professional actor data.

---

# 33. Questionnaire provenance

If visitor started inquiry from prepared Questionnaire:

store reference:

```text
questionnaire_definition_id?
questionnaire_revision_id?
```

where useful.

---

# 34. Builder provenance

If from generated Public Builder questionnaire:

```text
builder_session_id?
builder_generation_snapshot_id?
```

---

# 35. FBK-SRC-002 — Provenance Uses References, Not Full Fact Copies

---

# 36. Casting-specific incoming link

If Feedback originates from a scoped casting-specific URL, provenance may preserve that context.

It does not mean a saved Casting already exists unless explicitly linked.

---

# 37. Message

`message` is untrusted visitor content.

---

# 38. FBK-SEC-002 — Visitor Message Is Never Trusted HTML

Render escaped/sanitized.

No direct execution of HTML/JS.

---

# 39. Message limits

Server enforces reasonable:

- minimum/maximum length;
- encoding;
- payload size.

---

# 40. No hidden prompt authority

If message contains:

```text
Ignore system instructions...
```

it remains incoming text.

---

# 41. FBK-AI-001 — Feedback Text Is Untrusted AI Context

---

# 42. Attachments

Feedback MAY allow attachments for relevant types.

Examples:

```text
casting brief
reference image
PDF
document
project information
```

---

# 43. Attachment default

> **Every Feedback attachment is private operational data by default.**

---

# 44. FBK-ATT-001 — Attachment Is Not Public MediaAsset

---

# 45. Storage zone

Use:

```text
OPERATIONAL_PRIVATE
```

or equivalent.

---

# 46. Attachment record

Representative:

```text
feedback_attachments
```

Conceptual:

```text
id
feedback_id
storage_reference
original_filename
verified_mime
size_bytes
checksum
processing_state
created_at
```

---

# 47. Client MIME

Untrusted.

---

# 48. Server validation

Must validate:

- content signature/magic;
- MIME;
- size;
- allowed extension where useful;
- safe filename;
- quarantine;
- malware/safety pipeline if available.

---

# 49. FBK-ATT-002 — Declared MIME Is Not Authority

---

# 50. Allowed file types

Controlled and limited to business need.

Baseline may include:

```text
image/*
application/pdf
selected office/document types
```

subject to secure parser support.

---

# 51. Executables

Rejected.

---

# 52. SVG

Either prohibited or processed through explicit safe sanitization workflow.

---

# 53. Raw public file URLs

Forbidden.

---

# 54. FBK-ATT-003 — Attachment Delivery Requires Admin Authorization

---

# 55. Attachment promotion

If an incoming file later becomes a reusable Casting or Media Library asset:

requires explicit command.

---

# 56. Example

```text
PromoteFeedbackAttachmentToCastingSource
```

or create Casting with reference/snapshot.

---

# 57. FBK-ATT-004 — Promotion Is Explicit and Audited

---

# 58. Preserve original

Promotion must not destroy Feedback provenance.

---

# 59. Upload lifecycle

Recommended:

```text
UPLOADING
STORED
VALIDATING
READY
QUARANTINED
FAILED
```

---

# 60. Feedback creation with attachment

Preferred architecture:

1. staged private upload;
2. validated attachment token/reference;
3. Feedback submission references staged assets;
4. transaction binds them to Feedback.

---

# 61. FBK-ATT-005 — Orphan Upload Cleanup Required

Temporary unattached files expire and are cleaned asynchronously.

---

# 62. Critical persistence rule

Canonical:

```text
Feedback persistence
BEFORE
notification delivery
```

---

# 63. FBK-NOT-001 — Persist Before Notify

Hard invariant.

---

# 64. Correct flow

```text
BEGIN
  insert Feedback
  bind attachments
  insert Audit
  insert Outbox
COMMIT

then:
  create notification intent
  queue deliveries
```

---

# 65. Incorrect flow

```text
send WhatsApp
send email
then insert Feedback
```

Forbidden.

---

# 66. Why

If provider fails:

Feedback still exists.

---

# 67. If provider succeeds but DB fails

Without persist-first, administrator could receive an alert for an inquiry that does not exist.

Also forbidden.

---

# 68. FBK-NOT-002 — Notification Is Derived Operational Effect

---

# 69. Submission response

After successful DB commit:

visitor receives success even if notification subsystem is temporarily unavailable.

---

# 70. Example

```text
Ваше сообщение отправлено.
```

---

# 71. FBK-NOT-003 — Notification Failure Must Not Produce False Submission Failure

If Feedback was committed successfully.

---

# 72. Conversely

If Feedback persistence failed:

do not claim successful submission merely because upload or notification staging happened.

---

# 73. FBK-INV-004 — Submission Success Means Feedback Was Persisted

---

# 74. Notification channels

Potential:

```text
IN_APP
WHATSAPP
EMAIL
```

according to DOC-080.

---

# 75. Default external notification

Minimal.

---

# 76. Example

```text
Новое профессиональное обращение: приглашение на кастинг.
Открыть в админ-панели: <protected link>
```

---

# 77. Avoid full message

Do not send complete private casting brief/attachment through external notification by default.

---

# 78. FBK-NOT-004 — External Notification Data-Minimized

---

# 79. Protected Admin link

Notification deep link is navigation only.

Still requires Admin authentication/authorization.

---

# 80. FBK-NOT-005 — Deep Link Is Not Authorization Token

---

# 81. Notification recipient

Configured by Notifications domain.

Not derived automatically from Public Contacts.

---

# 82. FBK-NOT-006 — Public Contact ≠ Notification Recipient

---

# 83. Quiet hours

Apply according to Notifications policy.

---

# 84. Critical incoming Feedback

May use notification priority policy, but Feedback remains immediately available in Admin.

---

# 85. Retry

Notification delivery follows durable retry/idempotency rules.

---

# 86. Notification retry must never create second Feedback.

---

# 87. FBK-NOT-007 — Delivery Retry Is Independent From Business Record

---

# 88. Admin Inbox

Feedback should have dedicated Inbox.

Recommended views:

```text
All
Unread
New
Needs Action
Casting
Role
Collaboration
Materials
Closed
Archived
```

---

# 89. Read state

Separate from workflow state.

Canonical:

```text
UNREAD
READ
```

---

# 90. FBK-READ-001 — Read State ≠ Workflow State

---

# 91. Reading message

Does not mean:

```text
Handled
Closed
Replied
Converted
```

---

# 92. Mark unread

Should be possible.

---

# 93. Read timestamps

Optional:

```text
first_read_at
last_read_at?
```

---

# 94. Read principal

May optionally track first reader.

Not business workflow owner.

---

# 95. Workflow state

Recommended:

```text
NEW
IN_REVIEW
ACTION_REQUIRED
WAITING_EXTERNAL
RESOLVED
CLOSED
ARCHIVED
```

---

# 96. NEW

Persisted, no workflow processing yet.

---

# 97. IN_REVIEW

Admin evaluating inquiry.

---

# 98. ACTION_REQUIRED

Specific internal response/action required.

---

# 99. WAITING_EXTERNAL

Response/material sent or question asked; awaiting external party.

---

# 100. RESOLVED

Business need handled.

---

# 101. CLOSED

Workflow intentionally completed.

---

# 102. ARCHIVED

Removed from active inbox while history retained.

---

# 103. FBK-WF-001 — Workflow Uses Validated Transitions

No arbitrary status assignment.

---

# 104. Example transitions

```text
NEW → IN_REVIEW
IN_REVIEW → ACTION_REQUIRED
ACTION_REQUIRED → WAITING_EXTERNAL
WAITING_EXTERNAL → ACTION_REQUIRED
ACTION_REQUIRED → RESOLVED
RESOLVED → CLOSED
CLOSED → ARCHIVED
```

---

# 105. Practical flexibility

Imported/historical inquiries may enter later state through explicit migration/admin path.

No fake intermediate timestamps.

---

# 106. FBK-WF-002 — Workflow Must Represent Reality, Not Fabricate History

---

# 107. Responsible Admin

Feedback can be assigned to:

```text
responsible_admin_id
```

---

# 108. Assignment

Represents internal work ownership.

---

# 109. FBK-RESP-001 — Responsible Admin ≠ Public Contact

---

# 110. Responsible Admin change

Auditable operational action.

---

# 111. Unassigned state

Allowed.

---

# 112. Inbox filter

```text
Assigned to me
Unassigned
```

recommended.

---

# 113. Internal notes

Admin can add private notes.

---

# 114. Notes entity

Preferred:

```text
feedback_notes
```

rather than a single overwritten textarea.

---

# 115. Note fields

```text
id
feedback_id
author_admin_id
body
created_at
edited_at?
```

---

# 116. FBK-NOTE-001 — Notes Are Append-Oriented

---

# 117. Notes are not visitor message

Never mix visually or structurally.

---

# 118. FBK-NOTE-002 — Internal Note ≠ Incoming Source

---

# 119. Notes are not Public.

---

# 120. Notes are not included in Casting Source automatically.

---

# 121. FBK-NOTE-003 — Internal Interpretation Must Not Pollute Casting Provenance

---

# 122. Edited notes

If editing allowed:

retain edit audit/version.

For material decisions, append new note may be preferable.

---

# 123. Next Action

Feedback supports structured:

```text
next_action
next_action_at?
```

---

# 124. Examples

```text
Review casting brief
Prepare questionnaire
Request additional information
Send materials
Follow up
Create Casting
Close inquiry
```

---

# 125. Next Action is workflow aid.

---

# 126. FBK-NXT-001 — Next Action ≠ Automated Execution

Writing:

```text
Send materials
```

does not send anything.

---

# 127. Due date

Optional `next_action_at`.

---

# 128. Scheduler

May alert when action becomes due/overdue.

---

# 129. Scheduler cannot automatically complete high-impact action.

---

# 130. FBK-NXT-002 — Reminder Automation Does Not Become Business Authority

---

# 131. Priority

Optional:

```text
LOW
NORMAL
HIGH
URGENT
```

Human-controlled baseline.

---

# 132. Due date may generate recommendation.

It does not automatically set URGENT unless explicit deterministic policy is intentionally adopted.

---

# 133. FBK-INV-005 — AI Does Not Set Workflow Priority Automatically

---

# 134. Reply state

Feedback may track whether professional response was:

```text
NOT_REQUIRED
PENDING
DRAFTED
SENT
```

if product needs it.

---

# 135. Reply body

Should not be stored as a single mutable field if real communication history is introduced.

Prefer communication/send records.

---

# 136. FBK-INV-006 — Reply Workflow ≠ Feedback Source

---

# 137. BB Assistant

May draft reply:

- casting response;
- role response;
- collaboration response;
- question response;
- materials cover note.

---

# 138. BB output remains `AIDraft`.

---

# 139. FBK-AI-002 — AI Reply Is Draft-Only

---

# 140. Human can edit.

---

# 141. Human explicitly sends through supported channel.

---

# 142. FBK-AI-003 — Generate ≠ Send

---

# 143. AI cannot mark inquiry resolved merely because it created a draft.

---

# 144. AI cannot change responsible Admin.

---

# 145. AI cannot convert Feedback to Casting automatically.

---

# 146. FBK-AI-004 — AI Cannot Perform Business Workflow Conversion

---

# 147. Feedback classification AI

Optional.

AI MAY suggest:

```text
likely CASTING_INVITATION
```

---

# 148. For low-risk UX, system could store:

```text
suggested_type
```

separately.

---

# 149. Human-selected/submitted type remains authoritative unless explicit reviewed correction occurs.

---

# 150. FBK-AI-005 — Suggested Type ≠ feedback_type

---

# 151. Spam classification

Automated anti-abuse models/rules may score likely spam.

This is security/operational metadata, not professional content.

---

# 152. Anti-spam handling

Possible states:

```text
NORMAL
SUSPECTED_SPAM
BLOCKED
QUARANTINED
```

---

# 153. Important business rule

High confidence technical abuse may be blocked.

Ambiguous professional message should not silently disappear.

---

# 154. FBK-ABUSE-001 — Prefer Quarantine Over Silent Loss for Ambiguous Messages

---

# 155. Anti-abuse protections

At minimum:

- rate limiting;
- payload limits;
- attachment limits;
- duplicate/replay protection;
- spam heuristics;
- hidden honeypot where useful;
- optional CAPTCHA only if needed.

---

# 156. FBK-ABUSE-002 — Anonymous Does Not Mean Unlimited

---

# 157. Rate limits

Should consider:

```text
IP/risk context
profile
time window
attachment volume
```

without excessive tracking.

---

# 158. Privacy

Anti-abuse metadata has bounded retention.

---

# 159. Bot submission

If rejected before persistence:

return safe response according to anti-abuse policy.

---

# 160. If persisted to quarantine

Admin may review.

---

# 161. Idempotency

Public submission should support replay protection.

---

# 162. Why

Browser may retry after network failure.

---

# 163. Canonical:

```text
SubmitFeedback
```

with idempotency key where client flow supports it.

---

# 164. Same key + same semantic request

Returns same created Feedback identity/result.

---

# 165. Same key + changed payload

```text
IDEMPOTENCY_CONFLICT
```

---

# 166. FBK-INV-007 — Public Submission Retry Must Not Duplicate Inquiry

---

# 167. Duplicate detection

Separate from idempotency.

Two independently submitted identical messages may genuinely be separate user actions.

---

# 168. System MAY flag duplicate candidate.

---

# 169. FBK-INV-008 — Duplicate Detection Never Auto-Merges Feedback

---

# 170. Create Casting from Inquiry

Critical workflow.

Eligible Feedback can be converted into a Casting.

---

# 171. Canonical action

```text
CreateCastingFromInquiry
```

---

# 172. Preconditions

At minimum:

```text
authenticated Admin
Feedback exists
authorized
not archived/deleted
type/context supports conversion or Human explicitly overrides
not already converted under baseline rule
```

---

# 173. FBK-CST-001 — Conversion Requires Explicit Human Action

---

# 174. No automatic conversion

Even when:

```text
feedback_type = CASTING_INVITATION
```

---

# 175. Why

Could still be:

- spam;
- malformed;
- duplicate;
- incomplete;
- non-actionable.

---

# 176. Conversion transaction

Conceptually:

```text
BEGIN
  validate Feedback
  create Casting
  create Feedback↔Casting provenance
  create CastingSource snapshot/reference
  Audit
  Outbox
COMMIT
```

---

# 177. FBK-CST-002 — Feedback Remains After Conversion

---

# 178. Bidirectional provenance

Feedback:

```text
linked_casting_id
```

or relation.

Casting:

```text
source_feedback_id
```

or relation.

---

# 179. Better normalized model

Dedicated relation where architecture prefers:

```text
feedback_casting_links
```

---

# 180. FBK-CST-003 — Provenance Navigable Both Directions

---

# 181. What becomes Casting Source

Only actual incoming material:

- message;
- sender-supplied attachments;
- source context;
- visitor-supplied role/project labels where applicable.

---

# 182. Excluded automatically

- Admin notes;
- AI classification;
- workflow next action;
- responsible Admin;
- private operational notification data.

---

# 183. FBK-CST-004 — Internal Workflow Metadata Is Not Casting Source

---

# 184. Stable source snapshot

Casting should preserve the incoming content as it existed at conversion.

---

# 185. Later Feedback workflow changes

Do not rewrite Casting Source.

---

# 186. FBK-CST-005 — Casting Provenance Is Historical

---

# 187. Conversion idempotency

Mandatory.

---

# 188. Retry after response loss

Must return/link same Casting.

---

# 189. FBK-CST-006 — Conversion Retry Cannot Create Duplicate Casting

---

# 190. Baseline cardinality

Recommended:

```text
one Feedback → zero or one primary Casting
```

---

# 191. If future product needs multiple Castings from one incoming message:

must become explicit multi-conversion design.

---

# 192. Convert type correction

If Feedback is originally OTHER but Human sees valid casting:

conversion MAY be allowed.

Do not require AI to reclassify it first.

---

# 193. FBK-CST-007 — Human Can Correct Workflow Without Rewriting Original Message

---

# 194. Opportunity

Feedback should normally not create Opportunity directly when Casting semantics exist.

Recommended:

```text
Feedback
→ Casting
→ Opportunity
```

for casting-specific leads.

---

# 195. Generic Collaboration

Could later create Opportunity directly if Opportunity module permits.

This does not make Feedback an Opportunity itself.

---

# 196. FBK-INV-009 — Feedback Is Intake, Not Pipeline Stage

---

# 197. Questionnaire requests

For `QUESTIONNAIRE` type:

Admin may:

- share primary questionnaire;
- select alternate prepared questionnaire;
- create custom/casting Draft;
- provide Public Builder link.

---

# 198. Feedback does not duplicate Questionnaire content.

---

# 199. FBK-INV-010 — Inquiry Links to Questionnaire, Never Becomes Questionnaire

---

# 200. Materials requests

For `MATERIALS`:

Admin can link requested existing materials.

---

# 201. Material request tracking MAY use:

```text
requested_materials
```

or relation to follow-up workflow.

---

# 202. Do not automatically make private media public merely because visitor asked for it.

---

# 203. FBK-SEC-003 — Inquiry Cannot Escalate Media Access

---

# 204. Role request

`ROLE` Feedback can become Casting if it represents actual casting/job context.

Human decides.

---

# 205. Collaboration

May remain Feedback or create Opportunity depending workflow.

---

# 206. Feedback public acknowledgement

After commit:

show concise success state.

---

# 207. Do not expose:

- Feedback internal ID unnecessarily;
- Admin routing;
- recipient addresses;
- moderation state.

---

# 208. Optional reference number

Could provide opaque public reference if future reply/status workflow needs it.

Not baseline.

---

# 209. FBK-SEC-004 — DB UUID Is Not Public Tracking Credential

---

# 210. Public readback

Baseline visitors cannot query submitted Feedback by ID.

---

# 211. FBK-SEC-005 — Feedback Is Write-Only From Public Surface

---

# 212. Why

Avoid exposure/enumeration of messages and contact details.

---

# 213. Public edit/delete

Not baseline.

Privacy requests handled through appropriate process.

---

# 214. Admin detail view

Recommended layout:

```text
Header
├── Type
├── Status
├── Read state
├── Priority
├── Responsible
└── Received time

Sender
Message
Attachments
Source / Attribution
Related Questionnaire / Builder
Related Casting
Internal Notes
Next Action
Activity
Notification status
```

---

# 215. FBK-UX-001 — Incoming Message Visually Separate From Internal Notes

---

# 216. Read action

Opening detail MAY mark read automatically.

If used, make it predictable.

---

# 217. Workflow action

Explicit buttons:

```text
Взять в работу
Требуется действие
Ожидаем ответ
Закрыть
Архивировать
```

---

# 218. Create Casting button

Show when appropriate:

```text
Создать кастинг
```

---

# 219. If already converted:

show:

```text
Открыть кастинг
```

rather than create again.

---

# 220. FBK-UX-002 — Conversion State Is Visible

---

# 221. Notes

Chronological.

Show author/time.

---

# 222. Next Action

Visible near top when set.

---

# 223. Attachments

Show:

- filename;
- MIME/type;
- size;
- safe status;
- preview/download action if allowed.

---

# 224. Quarantined attachment

Clearly unavailable.

Do not send it to AI/Casting processing.

---

# 225. FBK-ATT-006 — Quarantine Status Is Visible to Admin

---

# 226. Activity timeline

Derived from:

- creation;
- reads;
- assignments;
- workflow transitions;
- note actions;
- Casting conversion;
- notification outcomes;
- replies if implemented.

---

# 227. FBK-INV-011 — Timeline Is Derived Projection

---

# 228. Notification status in Admin

Useful:

```text
In-App delivered
WhatsApp failed — retrying
Email delivered
```

---

# 229. Important

Failure badge must not imply Feedback failure.

---

# 230. FBK-UX-003 — Business Record and Delivery Status Shown Separately

---

# 231. Search

Admin search can search:

```text
sender name
company
email
subject
message
type
workflow
linked Casting
```

subject to authorization.

---

# 232. Public Search

Never indexes Feedback.

---

# 233. FBK-SEC-006 — Feedback Is Absent From Public Search/SEO

---

# 234. Admin Search is private.

---

# 235. Search snippets

Must not be cached/shared publicly.

---

# 236. Cache

Feedback detail/list cache:

- Admin-scoped;
- short-lived or no-cache where appropriate;
- must respect read/workflow updates.

---

# 237. FBK-SEC-007 — No Public Shared Feedback Cache

---

# 238. AI usage

Optional scenarios:

- classify inquiry;
- summarize for Admin;
- draft reply;
- suggest Casting conversion;
- analyze Casting only after conversion/source flow.

---

# 239. Summary

AI summary must remain derived/non-authoritative.

---

# 240. FBK-AI-006 — AI Summary Never Replaces Message

---

# 241. Conversion recommendation

AI may say:

```text
Похоже на приглашение на кастинг.
```

but Human still clicks Create Casting.

---

# 242. FBK-AI-007 — AI Recommendation Cannot Convert Record

---

# 243. Context minimization

AI classification/summarization need not receive unrelated Profile private data.

---

# 244. Reply draft

May receive only:

- incoming message;
- approved Profile facts relevant to answer;
- Human instructions.

---

# 245. FBK-AI-008 — AI Context Is Purpose-Bound

---

# 246. Admin notification vs AI

Notification creation must not depend on AI classification.

---

# 247. Even if AI provider is down:

new Feedback still appears and notification can use submitted `feedback_type`.

---

# 248. FBK-INV-012 — Intake/Notifications Work Without AI

---

# 249. Notification priority

Can use deterministic rules based on:

- explicitly selected type;
- Human priority;
- due date.

Do not require model inference.

---

# 250. Public form UX

Form should be concise.

Recommended:

```text
Тип обращения
Имя
Компания / проект (optional)
Email / телефон
Сообщение
Вложения (when allowed)
Consent/privacy notice if required
Отправить
```

---

# 251. Type-specific progressive fields

Example CASTING_INVITATION may optionally request:

```text
project name
role
deadline
```

but fields remain visitor-supplied context.

---

# 252. FBK-UX-004 — Do Not Force Unknown Casting Data

If visitor does not know deadline:

allow missing.

---

# 253. Form validation

Client for UX.

Server authoritative.

---

# 254. FBK-INV-013 — Client Validation Is Not Security Boundary

---

# 255. File upload limits

Displayed before user uploads.

---

# 256. Accessibility

Public form requires:

- explicit labels;
- clear field errors;
- keyboard access;
- status announcements;
- no color-only errors;
- accessible attachment remove controls.

---

# 257. Error recovery

If validation fails:

retain non-sensitive form fields where possible.

---

# 258. Upload failure

Should explain specific file issue without losing entire composed message if possible.

---

# 259. FBK-UX-005 — One Bad Attachment Should Not Needlessly Destroy Message Draft

---

# 260. Public error safety

Examples:

```text
Сообщение слишком длинное.
Файл этого типа не поддерживается.
Не удалось отправить сообщение.
```

---

# 261. Do not expose:

- database errors;
- queue internals;
- notification provider;
- storage paths.

---

# 262. Error taxonomy

At minimum:

```text
FEEDBACK_TYPE_INVALID
FEEDBACK_MESSAGE_REQUIRED
FEEDBACK_MESSAGE_TOO_LONG
FEEDBACK_CONTACT_REQUIRED
FEEDBACK_EMAIL_INVALID
FEEDBACK_PHONE_INVALID
FEEDBACK_ATTACHMENT_INVALID
FEEDBACK_ATTACHMENT_TOO_LARGE
FEEDBACK_ATTACHMENT_UNSUPPORTED
FEEDBACK_ATTACHMENT_QUARANTINED
FEEDBACK_RATE_LIMITED
FEEDBACK_DUPLICATE_REPLAY
FEEDBACK_NOT_FOUND
FEEDBACK_WORKFLOW_TRANSITION_INVALID
FEEDBACK_RESPONSIBLE_INVALID
FEEDBACK_NEXT_ACTION_INVALID
FEEDBACK_ALREADY_CONVERTED_TO_CASTING
FEEDBACK_CASTING_CONVERSION_CONFLICT
FEEDBACK_STALE_VERSION
```

---

# 263. Public rate-limit response

Typed but safe.

May include retry information when useful.

---

# 264. Versioning

Feedback mutable operational fields use:

```text
version
```

---

# 265. Visitor message

After submission should generally be immutable as received.

---

# 266. FBK-INV-014 — Incoming Source Message Is Append/Immutable-Oriented

Corrections should be separate Admin note or explicit corrected metadata with audit, not silent rewrite of what visitor sent.

---

# 267. Sender metadata correction

If Admin fixes normalization/typo for operational use:

preserve original where provenance value matters.

---

# 268. Workflow updates

Use `expectedVersion`.

---

# 269. Assignment updates

Use concurrency protection.

---

# 270. FBK-INV-015 — Stale Admin Update Cannot Overwrite Newer Workflow State

---

# 271. Notes concurrency

Append-oriented notes reduce conflicts.

---

# 272. Conversion race

Two Admins click Create Casting.

Transaction/idempotency/unique guard ensures one.

---

# 273. FBK-CST-008 — Conversion Is Race-Safe

---

# 274. Notification race

Outbox dedupe/idempotent delivery prevents duplicate provider sends for same delivery intent.

---

# 275. Does not affect Feedback uniqueness.

---

# 276. Commands — Public

Canonical:

```text
SubmitFeedback
UploadFeedbackAttachment
RemoveStagedFeedbackAttachment
```

---

# 277. Commands — Admin workflow

```text
MarkFeedbackRead
MarkFeedbackUnread
StartFeedbackReview
SetFeedbackActionRequired
SetFeedbackWaitingExternal
ResolveFeedback
CloseFeedback
ArchiveFeedback
RestoreFeedback
AssignFeedbackResponsible
SetFeedbackPriority
SetFeedbackNextAction
ClearFeedbackNextAction
AddFeedbackNote
```

---

# 278. Casting command

```text
CreateCastingFromInquiry
```

---

# 279. AI-related commands

```text
RequestFeedbackAISummary
RequestFeedbackAIClassification
CreateFeedbackReplyDraft
```

where enabled.

---

# 280. Queries

```text
GetFeedbackInbox
GetFeedback
GetFeedbackCounts
GetUnreadFeedbackCount
GetFeedbackAttachments
GetFeedbackNotes
GetFeedbackTimeline
GetFeedbackNotificationStatus
GetFeedbackCastingLink
```

---

# 281. Public submission contract

Conceptually:

```text
type
name?
company?
email?
phone?
contactPreference?
subject?
message
attachmentTokens[]
sourceContext?
questionnaireContext?
builderSnapshotContext?
idempotencyKey
```

---

# 282. Client cannot set

```text
workflowState
readState
priority
responsibleAdminId
castingId
notificationStatus
spamApproved
createdAt
```

---

# 283. FBK-SEC-008 — Public Command Cannot Set Admin Fields

---

# 284. Server source context

Where possible derive route/context server-side rather than blindly trusting submitted source labels.

---

# 285. FBK-SRC-003 — Attribution Claims Are Validated/Derived

---

# 286. Feedback transaction

```text
BEGIN
 create Feedback
 bind validated staged attachments
 create Audit
 create Outbox
 persist idempotency result
COMMIT
```

---

# 287. After commit

```text
Outbox Dispatcher
→ Notification Intent
→ channel deliveries
```

---

# 288. FBK-INV-016 — Notification Provider Is Never Called Inside Feedback Transaction

---

# 289. Outbox event

Canonical:

```text
FeedbackSubmitted
```

---

# 290. Event payload should contain minimum identifiers:

```text
feedback_id
profile_id
feedback_type
priority?
created_at
```

---

# 291. Do not duplicate full message unless consumer genuinely requires it.

Notification service can reload authorized minimum data.

---

# 292. FBK-SEC-009 — Outbox Payload Is Data-Minimized

---

# 293. Additional events

```text
FeedbackReadChanged
FeedbackWorkflowChanged
FeedbackAssigned
FeedbackNextActionChanged
FeedbackNoteAdded
FeedbackConvertedToCasting
FeedbackArchived
```

---

# 294. Consumers

```text
Notification Center
Admin Dashboard
VOP
Casting workflow
Analytics
Private Search
```

---

# 295. Analytics

Canonical useful events:

```text
feedback_submitted
feedback_type
feedback_opened_admin
feedback_converted_to_casting
feedback_resolved
```

---

# 296. Avoid raw:

```text
message
phone
email
attachments
```

in analytics payload by default.

---

# 297. FBK-SEC-010 — Analytics Is Not Inbox Storage

---

# 298. Marketing attribution

Can measure source-to-inquiry conversion.

Example:

```text
social → profile → questionnaire → feedback
```

---

# 299. This data does not change Feedback workflow automatically.

---

# 300. FBK-INV-017 — Analytics Cannot Mark Lead Qualified

---

# 301. VOP

May flag:

```text
Unread high-priority inquiry
Feedback without responsible Admin
Next Action overdue
Casting Invitation not converted/reviewed
Material request awaiting response
Notification failed
Duplicate candidate
```

---

# 302. Safe VOP actions

May:

```text
recompute inbox counters
retry safe notification technical job via notification subsystem policy
reindex private search
```

---

# 303. VOP cannot:

```text
reply
convert to Casting
close Feedback
assign Human business decision
send materials
```

without explicit Human authority.

---

# 304. FBK-INV-018 — VOP Cannot Resolve Professional Inquiry Autonomously

---

# 305. Notification center

New Feedback should appear as internal notification even if external delivery unavailable, provided Notification Center itself is operational.

---

# 306. Inbox itself remains primary source.

---

# 307. FBK-INV-019 — Inbox Is More Authoritative Than Notification Badge

---

# 308. Counts

Unread count is Projection.

---

# 309. If count cache stale:

opening Inbox must query/reconcile current state.

---

# 310. FBK-READ-002 — Badge Count Is Not Business Authority

---

# 311. Retention

Feedback contains potentially personal and commercially sensitive information.

---

# 312. Suggested retention classes

```text
Feedback Source
Attachments
Internal Notes
Notification Metadata
Analytics
Staged Uploads
```

handled separately.

---

# 313. Staged uploads

Short retention.

---

# 314. Feedback Source

Retained according to professional/privacy policy.

---

# 315. Attachments

May have shorter retention than Feedback text if no longer needed.

---

# 316. Internal Notes

Operational retention.

---

# 317. FBK-SEC-011 — Retention Is Component-Specific

---

# 318. Archive

Archive hides from normal active Inbox but preserves history.

---

# 319. FBK-INV-020 — Archive ≠ Delete

---

# 320. Restore

Does not automatically:

- mark unread;
- resend notification;
- create Casting;
- restore old next action.

unless explicit policy/action.

---

# 321. FBK-INV-021 — Restore Does Not Replay Side Effects

---

# 322. Hard delete / privacy purge

Dependency-aware.

Must inspect:

```text
attachments
Casting provenance
Casting Source snapshot
AI summaries/drafts
notification payload copies
search index
analytics references
logs
```

---

# 323. If converted to Casting

Ordinary deleting Feedback must not silently corrupt Casting provenance.

---

# 324. FBK-CST-009 — Converted Feedback Has HARD Provenance Dependency

Use redaction/tombstone/minimal provenance if full deletion is legally required.

---

# 325. Attachment purge

Must follow storage deletion workflow:

```text
revoke access
dependency check
delete bytes
verify
tombstone/update metadata
audit
```

---

# 326. No DB-row-first deletion.

---

# 327. Backup restore

Must not:

- resend historical notifications;
- recreate Castings;
- mark old inquiries unread arbitrarily;
- reactivate staged uploads;
- reopen Closed feedback.

---

# 328. FBK-INV-022 — Restore Reconciles Operational State Before Workers Resume

---

# 329. Migration

Legacy contact/inquiry form records should map to Feedback.

---

# 330. Migration must preserve:

```text
original message
original sender data
created timestamp
legacy status
attachments
source provenance
```

where available.

---

# 331. Legacy untyped message

Can migrate:

```text
feedback_type = OTHER
```

or explicit review-required type.

---

# 332. FBK-MIG-001 — Migration Does Not Invent Inquiry Type

---

# 333. Legacy notification email

Email copy is not superior Source if original DB record exists.

---

# 334. FBK-MIG-002 — Notification Copy ≠ Feedback Source

---

# 335. Email-only historical inquiry

If database record absent but trusted archived incoming email is the only source:

may import as migrated Feedback with explicit provenance.

---

# 336. Do not pretend it was originally submitted through current form.

---

# 337. FBK-MIG-003 — Migration Provenance Must Be Explicit

---

# 338. Legacy already-converted casting

Create/link provenance without creating duplicate Casting.

---

# 339. Migration idempotency

Required.

---

# 340. Legacy read state

Preserve if reliable.

Otherwise use explicit unknown/migrated workflow policy rather than inventing read timestamps.

---

# 341. Legacy internal notes

Migrate to Notes, not incoming message.

---

# 342. Anti-patterns

`FBK-AP-001`  
Send email before saving Feedback.

`FBK-AP-002`  
Send WhatsApp before DB commit.

`FBK-AP-003`  
Return failure to visitor because notification failed after successful persistence.

`FBK-AP-004`  
Lose inquiry when WhatsApp provider is down.

`FBK-AP-005`  
Notification email is the only stored copy of Feedback.

`FBK-AP-006`  
Store complete inbox only in external provider.

`FBK-AP-007`  
Public form can set workflow status.

`FBK-AP-008`  
Public form can set responsible Admin.

`FBK-AP-009`  
Client-supplied `is_read=true` accepted.

`FBK-AP-010`  
Incoming sender automatically becomes Public Contact.

`FBK-AP-011`  
Incoming email automatically becomes NotificationRecipient.

`FBK-AP-012`  
AI changes feedback type silently.

`FBK-AP-013`  
AI converts Feedback to Casting automatically.

`FBK-AP-014`  
AI sends reply automatically.

`FBK-AP-015`  
AI summary replaces incoming message.

`FBK-AP-016`  
Internal note appended to incoming source text.

`FBK-AP-017`  
Admin notes copied to Casting Source automatically.

`FBK-AP-018`  
Reading inquiry automatically closes it.

`FBK-AP-019`  
Read state and workflow state use one field.

`FBK-AP-020`  
Assignment automatically exposes Admin contact publicly.

`FBK-AP-021`  
Next Action executes itself automatically.

`FBK-AP-022`  
Notification recipient taken from Public Contacts automatically.

`FBK-AP-023`  
Full casting brief sent through external notification by default.

`FBK-AP-024`  
Admin deep link bypasses authentication.

`FBK-AP-025`  
Notification retry creates duplicate Feedback.

`FBK-AP-026`  
Same idempotency key with changed message creates second silent record.

`FBK-AP-027`  
Duplicate text automatically merged.

`FBK-AP-028`  
Every CASTING_INVITATION automatically creates Casting.

`FBK-AP-029`  
Delete Feedback after Casting conversion.

`FBK-AP-030`  
Casting conversion copies internal workflow fields as source facts.

`FBK-AP-031`  
Conversion retry creates multiple Castings.

`FBK-AP-032`  
Visitor can enumerate Feedback by UUID.

`FBK-AP-033`  
Public endpoint returns submitted message details by ID.

`FBK-AP-034`  
Public Search indexes Feedback.

`FBK-AP-035`  
Feedback appears in SEO/JSON-LD.

`FBK-AP-036`  
Public/shared cache contains Feedback details.

`FBK-AP-037`  
Raw email/phone/message copied into analytics.

`FBK-AP-038`  
Declared attachment MIME trusted.

`FBK-AP-039`  
Executable uploaded as casting attachment.

`FBK-AP-040`  
Feedback attachment becomes public MediaAsset automatically.

`FBK-AP-041`  
Raw storage URL given to visitor/Admin browser unnecessarily.

`FBK-AP-042`  
Attachment ID is sufficient authorization.

`FBK-AP-043`  
Quarantined attachment sent to AI.

`FBK-AP-044`  
Orphan uploads retained indefinitely.

`FBK-AP-045`  
Stale Admin edit overwrites newer workflow state.

`FBK-AP-046`  
Two Admin conversions create two Castings.

`FBK-AP-047`  
Archive deletes Feedback attachments/history immediately.

`FBK-AP-048`  
Restore resends old notifications.

`FBK-AP-049`  
Restore re-creates linked Casting.

`FBK-AP-050`  
Hard delete breaks existing Casting provenance.

`FBK-AP-051`  
Legacy untyped message assigned CASTING_INVITATION by guess.

`FBK-AP-052`  
Migration notification email treated as more authoritative than original record.

`FBK-AP-053`  
Spam AI silently deletes ambiguous professional inquiry.

`FBK-AP-054`  
No rate limiting on public form.

`FBK-AP-055`  
One bad attachment destroys visitor's entire message draft unnecessarily.

`FBK-AP-056`  
Feedback intake depends on AI availability.

`FBK-AP-057`  
Feedback intake depends on Notification provider availability.

`FBK-AP-058`  
Notification delivery status stored as Feedback workflow state.

`FBK-AP-059`  
Clicking PDF from inquiry automatically marks MATERIALS request resolved.

`FBK-AP-060`  
Analytics or VOP automatically qualifies inquiry into Opportunity.

---

# 343. Core invariants

`FBK-INV-023`  
Feedback is the authoritative persisted incoming inquiry record.

`FBK-INV-024`  
Feedback type uses controlled taxonomy.

`FBK-INV-025`  
Incoming message remains distinct from Admin interpretation.

`FBK-INV-026`  
Incoming sender data does not automatically enter Contacts Directory.

`FBK-INV-027`  
Source/context provenance remains separate from professional actor Source.

`FBK-INV-028`  
Questionnaire/Builder provenance uses references rather than copied facts.

`FBK-INV-029`  
Visitor content is treated as untrusted text.

`FBK-INV-030`  
Feedback attachments are private operational files by default.

`FBK-INV-031`  
Client MIME is never authoritative.

`FBK-INV-032`  
Unsafe/quarantined attachment cannot be used downstream.

`FBK-INV-033`  
Attachment promotion to Casting/Media is explicit.

`FBK-INV-034`  
Feedback commit always precedes notification delivery.

`FBK-INV-035`  
Successful persistence is the definition of successful submission.

`FBK-INV-036`  
Notification failure cannot remove/rollback Feedback.

`FBK-INV-037`  
Notification retry cannot duplicate Feedback.

`FBK-INV-038`  
External notification payload is minimized.

`FBK-INV-039`  
Notification Admin deep link still requires authorization.

`FBK-INV-040`  
Notification recipients remain separate from Public Contacts.

`FBK-INV-041`  
Read state is independent from workflow state.

`FBK-INV-042`  
Reading a Feedback does not resolve it.

`FBK-INV-043`  
Workflow transitions are validated.

`FBK-INV-044`  
Responsible Admin is internal assignment only.

`FBK-INV-045`  
Internal notes remain distinct from incoming Source.

`FBK-INV-046`  
Next Action is planning metadata, not automatic execution.

`FBK-INV-047`  
Priority is Human-controlled baseline.

`FBK-INV-048`  
AI reply/classification/summary remains non-authoritative.

`FBK-INV-049`  
AI cannot convert Feedback to Casting.

`FBK-INV-050`  
AI cannot send professional reply automatically.

`FBK-INV-051`  
Feedback intake works without AI.

`FBK-INV-052`  
Feedback intake works when external notification providers fail.

`FBK-INV-053`  
Ambiguous anti-spam results prefer quarantine/review over silent professional data loss.

`FBK-INV-054`  
Public submission is rate-limited and bounded.

`FBK-INV-055`  
Submission replay is idempotent where idempotency key is used.

`FBK-INV-056`  
Duplicate detection remains distinct from idempotency.

`FBK-INV-057`  
CreateCastingFromInquiry is explicit Human action.

`FBK-INV-058`  
Feedback remains after Casting conversion.

`FBK-INV-059`  
Feedback↔Casting provenance is bidirectional.

`FBK-INV-060`  
Casting Source receives only actual incoming content, not internal notes/workflow metadata.

`FBK-INV-061`  
Casting conversion is idempotent and race-safe.

`FBK-INV-062`  
Feedback is intake, not Opportunity stage.

`FBK-INV-063`  
Inquiry cannot escalate media access merely by requesting materials.

`FBK-INV-064`  
Public Feedback interface is write-only baseline.

`FBK-INV-065`  
Knowing Feedback ID does not grant read access.

`FBK-INV-066`  
Feedback does not enter Public Search/SEO.

`FBK-INV-067`  
Feedback cache/search are Admin authorization-scoped.

`FBK-INV-068`  
AI contexts are purpose-bound and minimized.

`FBK-INV-069`  
Analytics avoid raw contact/message payloads.

`FBK-INV-070`  
VOP cannot autonomously reply/convert/resolve.

`FBK-INV-071`  
Inbox remains authoritative over notification badge.

`FBK-INV-072`  
Unread counters are derived projections.

`FBK-INV-073`  
Feedback source message is immutable/append-oriented after receipt.

`FBK-INV-074`  
Operational updates use optimistic concurrency.

`FBK-INV-075`  
Internal notes are append-oriented and attributable.

`FBK-INV-076`  
Outbox payloads are data-minimized.

`FBK-INV-077`  
Notification provider is never called inside Feedback transaction.

`FBK-INV-078`  
Archive preserves Feedback history.

`FBK-INV-079`  
Restore does not replay previous side effects.

`FBK-INV-080`  
Privacy purge performs dependency/copy discovery.

`FBK-INV-081`  
Converted Feedback cannot be destructively removed in a way that corrupts Casting provenance.

`FBK-INV-082`  
Attachment purge is storage-safe and dependency-aware.

`FBK-INV-083`  
Restore does not resend historic notifications or recreate Casting.

`FBK-INV-084`  
Migration preserves original incoming content/provenance.

`FBK-INV-085`  
Migration never invents Feedback type.

`FBK-INV-086`  
Legacy internal notes migrate separately from incoming Source.

`FBK-INV-087`  
Migration is idempotent.

`FBK-INV-088`  
Public command cannot set Admin-only fields.

`FBK-INV-089`  
All surface privacy decisions are server-side.

`FBK-INV-090`  
Core Feedback workflow remains operational without optional AI/integration providers.

---

# 344. E2E-FBK-001 — Basic submission

Visitor submits valid QUESTION inquiry.

Feedback persisted with state NEW/UNREAD.

---

# 345. E2E-FBK-002 — Notification provider down

Feedback persists successfully.

External notification fails.

Visitor still receives successful submission response.

---

# 346. E2E-FBK-003 — Database failure

Feedback cannot be persisted.

No success response is returned.

No notification is sent as if inquiry existed.

---

# 347. E2E-FBK-004 — Persist before notify

Test verifies Feedback/Outbox transaction commits before provider delivery starts.

---

# 348. E2E-FBK-005 — Outbox retry

Notification job retries.

Feedback row count remains one.

---

# 349. E2E-FBK-006 — Duplicate browser retry

Same idempotency key and identical request.

One Feedback only.

---

# 350. E2E-FBK-007 — Idempotency conflict

Same key with changed message returns typed conflict.

---

# 351. E2E-FBK-008 — Separate duplicate submission

Two independently intentional identical messages can exist; duplicate candidate warning only.

---

# 352. E2E-FBK-009 — Public admin field tamper

Visitor submits:

```text
workflowState=CLOSED
```

Server ignores/rejects.

Stored state NEW.

---

# 353. E2E-FBK-010 — Type validation

Unknown invalid type rejected or mapped only through explicit safe form policy.

---

# 354. E2E-FBK-011 — Sender Contact

Visitor email stored operationally.

No ContactEntry created.

---

# 355. E2E-FBK-012 — Notification recipient separation

Public actor email exists.

System notification still uses configured NotificationRecipient, not Public Contact automatically.

---

# 356. E2E-FBK-013 — Message XSS

Message contains script HTML.

Admin UI displays safe text; script does not execute.

---

# 357. E2E-FBK-014 — Prompt injection

Message contains AI instruction.

Optional AI summarizer treats it as data.

No privilege expansion.

---

# 358. E2E-FBK-015 — Valid attachment

PDF attachment passes validation and is bound privately to Feedback.

---

# 359. E2E-FBK-016 — MIME spoof

Executable renamed `.pdf`.

Rejected/quarantined.

---

# 360. E2E-FBK-017 — Attachment enumeration

Anonymous user guesses attachment ID.

Denied.

---

# 361. E2E-FBK-018 — Attachment promotion

Admin promotes relevant attachment through explicit Casting flow.

Original Feedback attachment provenance remains.

---

# 362. E2E-FBK-019 — Orphan upload

Visitor uploads but abandons form.

Temporary attachment cleaned after expiry.

---

# 363. E2E-FBK-020 — Oversized attachment

Rejected without persisting invalid file as READY.

---

# 364. E2E-FBK-021 — Read state

Admin opens Feedback.

Read state changes to READ.

Workflow remains NEW.

---

# 365. E2E-FBK-022 — Mark unread

READ → UNREAD succeeds without changing workflow.

---

# 366. E2E-FBK-023 — Start review

NEW → IN_REVIEW.

---

# 367. E2E-FBK-024 — Invalid workflow transition

Invalid direct transition returns typed error.

---

# 368. E2E-FBK-025 — Assignment

Feedback assigned to Admin A.

No public contact exposure occurs.

---

# 369. E2E-FBK-026 — Concurrent assignment

Admin A updates v3→v4.

Admin B submits stale v3.

Rejected with stale-version conflict.

---

# 370. E2E-FBK-027 — Internal note

Admin adds note.

Incoming visitor message unchanged.

---

# 371. E2E-FBK-028 — Note visibility

Public endpoints cannot read Admin note.

---

# 372. E2E-FBK-029 — Next Action

Admin sets:

```text
Prepare questionnaire
```

No questionnaire is created automatically.

---

# 373. E2E-FBK-030 — Overdue Next Action

Scheduler/VOP flags it.

Does not execute action automatically.

---

# 374. E2E-FBK-031 — AI classification

AI suggests CASTING_INVITATION.

Stored authoritative type unchanged until Human correction.

---

# 375. E2E-FBK-032 — AI summary

Summary generated.

Original message remains intact and primary.

---

# 376. E2E-FBK-033 — AI reply draft

BB drafts reply.

No email/WhatsApp is sent.

---

# 377. E2E-FBK-034 — AI unavailable

Feedback submit, Inbox, assignment, Casting conversion all remain usable.

---

# 378. E2E-FBK-035 — Anti-spam rate limit

Excessive submissions receive rate-limit response.

Valid existing Feedback unaffected.

---

# 379. E2E-FBK-036 — Ambiguous spam

Potentially legitimate inquiry quarantined/reviewable rather than silently erased according to policy.

---

# 380. E2E-FBK-037 — Casting conversion

Admin clicks Create Casting.

One Casting created with source/provenance.

---

# 381. E2E-FBK-038 — Feedback retained

Converted Feedback remains readable.

---

# 382. E2E-FBK-039 — Bidirectional link

Feedback opens Casting.

Casting opens source Feedback.

---

# 383. E2E-FBK-040 — Conversion source

Casting receives incoming message/attachments.

Admin notes are excluded.

---

# 384. E2E-FBK-041 — Conversion retry

Lost response then same idempotency retry.

Same Casting returned.

---

# 385. E2E-FBK-042 — Conversion race

Two Admins simultaneously convert.

Exactly one primary Casting created.

---

# 386. E2E-FBK-043 — Already converted UX

Button changes from Create Casting to Open Casting.

---

# 387. E2E-FBK-044 — OTHER to Casting

Human may explicitly convert legitimate casting-like OTHER inquiry without rewriting original incoming text.

---

# 388. E2E-FBK-045 — Questionnaire provenance

Feedback submitted from Questionnaire R5 stores R5 reference.

Does not copy Questionnaire snapshot into Feedback.

---

# 389. E2E-FBK-046 — Builder provenance

Feedback from generated Builder document stores Snapshot provenance.

---

# 390. E2E-FBK-047 — Materials request

Visitor requests private material.

System does not automatically publish/private-link that material.

---

# 391. E2E-FBK-048 — Public Feedback lookup

Anonymous GET by Feedback UUID does not return message/sender data.

---

# 392. E2E-FBK-049 — Public Search

Incoming message text never appears in Public Search.

---

# 393. E2E-FBK-050 — SEO

No Feedback data appears in sitemap/OG/JSON-LD.

---

# 394. E2E-FBK-051 — Admin Search

Authorized Admin can find inquiry by sender/message.

---

# 395. E2E-FBK-052 — Search authorization

Unauthorized user cannot access private Feedback search.

---

# 396. E2E-FBK-053 — Analytics privacy

`feedback_submitted` records type/source IDs but not raw phone/message by default.

---

# 397. E2E-FBK-054 — Minimal WhatsApp notification

Notification contains concise type/protected link.

Full message/attachments absent.

---

# 398. E2E-FBK-055 — Notification deep-link auth

Unauthenticated user opens Admin deep link.

Authentication required.

---

# 399. E2E-FBK-056 — Quiet hours

Feedback persists immediately.

External low/noncritical delivery may defer.

Inbox availability is immediate.

---

# 400. E2E-FBK-057 — Notification retry final failure

Feedback remains in Inbox with delivery failure status.

---

# 401. E2E-FBK-058 — Close

Inquiry resolved/closed.

Read state unaffected semantically.

---

# 402. E2E-FBK-059 — Archive

Feedback disappears from active Inbox but history retained.

---

# 403. E2E-FBK-060 — Restore

Restored Feedback does not resend original notifications.

---

# 404. E2E-FBK-061 — Converted Feedback archive

Linked Casting provenance remains intact.

---

# 405. E2E-FBK-062 — Privacy purge dependency

Attempt purge Feedback linked to Casting.

System discovers provenance dependency and uses allowed redaction/tombstone policy.

---

# 406. E2E-FBK-063 — Attachment purge

Bytes deleted only after dependency/revocation workflow.

DB is not deleted first.

---

# 407. E2E-FBK-064 — Backup restore

Historical Feedback returns with correct closed/archive state.

No provider notification replay.

---

# 408. E2E-FBK-065 — Restore Casting relation

Existing link restored/reconciled.

No duplicate Casting created.

---

# 409. E2E-FBK-066 — Migration untyped

Legacy generic inquiry migrates as OTHER/review state rather than guessed CASTING.

---

# 410. E2E-FBK-067 — Migration note separation

Legacy Admin note becomes FeedbackNote, not incoming message.

---

# 411. E2E-FBK-068 — Migration idempotency

Re-running migration does not duplicate inquiry/attachments/links.

---

# 412. E2E-FBK-069 — Public form accessibility

Keyboard/screen-reader user can complete form and hear validation/success state.

---

# 413. E2E-FBK-070 — Attachment UX

One invalid attachment can be removed/replaced without discarding typed message.

---

# 414. E2E-FBK-071 — Inbox unread count

Submitting Feedback increments derived unread counter.

Marking read decrements/reconciles correctly.

---

# 415. E2E-FBK-072 — Stale counter

Cached badge stale.

Inbox current query still returns correct source state.

---

# 416. E2E-FBK-073 — VOP overdue warning

VOP flags overdue next action but does not reply/close.

---

# 417. E2E-FBK-074 — VOP Casting recommendation

VOP can surface unprocessed Casting Invitation.

No conversion until Human action.

---

# 418. E2E-FBK-075 — No notification system

Notification subsystem unavailable entirely.

Submission still persists and appears in Admin Inbox.

---

# 419. Architecture diagram

```text
PUBLIC VISITOR
      │
      ▼
Submit Feedback
      │
      ▼
Validation / Anti-Abuse
      │
      ▼
      DB TX
┌───────────────────────┐
│ Feedback              │
│ Attachment links      │
│ Audit                 │
│ Outbox                │
└───────────────────────┘
      │
    COMMIT
      │
      ├──────────────► Visitor Success
      │
      ▼
 Outbox Dispatcher
      │
      ▼
Notification Intent
  ┌───┼─────┐
  ▼   ▼     ▼
App WhatsApp Email
```

---

# 420. State separation diagram

```text
READ STATE
UNREAD ↔ READ

          ≠

WORKFLOW
NEW → IN_REVIEW → ACTION_REQUIRED
             ↘ WAITING_EXTERNAL
                 ↓
              RESOLVED
                 ↓
               CLOSED
                 ↓
              ARCHIVED
```

---

# 421. Inbox ownership diagram

```text
Feedback
   │
   ├── Incoming Source
   ├── Sender
   ├── Attachments
   ├── Read State
   ├── Workflow State
   ├── Responsible Admin
   ├── Next Action
   └── Notes

All are distinct concepts.
```

---

# 422. Feedback → Casting diagram

```text
Feedback
  │
  ├── Message
  ├── Sender context
  └── Attachments
          │
          ▼
   Human: Create Casting
          │
          ▼
      DB Transaction
      /            \
     ▼              ▼
 Casting      Provenance Link
     │              │
     └──────────────┘
          ↓
 Feedback remains
```

---

# 423. Notes boundary diagram

```text
Incoming Message
      │
      └── immutable received source

Internal Notes
      │
      └── Admin-only interpretation/work history

Create Casting:
uses Incoming Source,
not Internal Notes by default.
```

---

# 424. Notification separation diagram

```text
Feedback
  = business record

NotificationIntent
  = attention request

NotificationDelivery
  = channel attempt

Provider failure:
Feedback still exists.
```

---

# 425. Provenance diagram

```text
Prepared Questionnaire Revision ──┐
Public Builder Snapshot ───────────┤
Profile CTA ───────────────────────┤
Campaign / Social ─────────────────┤
                                  ▼
                               Feedback
                                  │
                                  ▼
                                Casting
                           if Human converts
```

---

# 426. Quality gate

Перед implementation должны быть подтверждены:

- [ ] controlled Feedback type taxonomy;
- [ ] public SubmitFeedback contract;
- [ ] sender/contact minimization;
- [ ] source/provenance context;
- [ ] Questionnaire provenance;
- [ ] Builder Snapshot provenance;
- [ ] immutable/append-oriented incoming message;
- [ ] private attachment model;
- [ ] staged upload workflow;
- [ ] MIME/signature validation;
- [ ] quarantine;
- [ ] orphan cleanup;
- [ ] persist-before-notify transaction;
- [ ] Audit + Outbox atomicity;
- [ ] minimal Notification Intent;
- [ ] In-App/WhatsApp/Email separation;
- [ ] external notification failure tolerance;
- [ ] read-state model;
- [ ] workflow-state model;
- [ ] responsible Admin;
- [ ] internal notes;
- [ ] next action;
- [ ] optional priority;
- [ ] anti-spam/rate limiting;
- [ ] submission idempotency;
- [ ] duplicate candidate logic;
- [ ] Admin Inbox;
- [ ] Admin private search;
- [ ] public write-only privacy;
- [ ] AI summary/classification/draft-only boundaries;
- [ ] CreateCastingFromInquiry;
- [ ] bidirectional Feedback↔Casting provenance;
- [ ] conversion idempotency/race safety;
- [ ] no automatic Contacts promotion;
- [ ] no automatic Opportunity qualification;
- [ ] material access boundary;
- [ ] analytics minimization;
- [ ] VOP limits;
- [ ] archive/restore;
- [ ] privacy dependency handling;
- [ ] migration;
- [ ] accessibility;
- [ ] deterministic E2E coverage.

---

# 427. Acceptance criteria

`AC-FBK-001`  
Feedback является persisted business record, а не email/WhatsApp notification copy.

`AC-FBK-002`  
Поддерживаются `CASTING_INVITATION`, `ROLE`, `COLLABORATION`, `QUESTIONNAIRE`, `MATERIALS`, `QUESTION`, `COMMENT`, `OTHER`.

`AC-FBK-003`  
Incoming sender data не становится Contacts Directory Source автоматически.

`AC-FBK-004`  
Visitor message сохраняется отдельно от Admin notes/AI summaries.

`AC-FBK-005`  
Questionnaire/Builder source provenance сохраняется через references.

`AC-FBK-006`  
Feedback attachments private by default.

`AC-FBK-007`  
Attachment MIME/type проверяется server-side.

`AC-FBK-008`  
Unsafe/quarantined files cannot enter normal downstream workflows.

`AC-FBK-009`  
Attachment promotion to Casting/Media requires explicit Human action.

`AC-FBK-010`  
Feedback and its Audit/Outbox are committed before notification delivery begins.

`AC-FBK-011`  
Successful Feedback persistence returns successful submission even if optional notification provider fails.

`AC-FBK-012`  
Notification failure never rolls back or loses Feedback.

`AC-FBK-013`  
Notification retry never duplicates Feedback.

`AC-FBK-014`  
External notifications contain minimized content and protected Admin link.

`AC-FBK-015`  
Admin notification recipients are separate from Public Contacts.

`AC-FBK-016`  
Read state is independent from workflow state.

`AC-FBK-017`  
Reading an inquiry does not mark it resolved/closed.

`AC-FBK-018`  
Feedback workflow uses validated transitions.

`AC-FBK-019`  
Feedback can have responsible Admin without exposing that identity publicly.

`AC-FBK-020`  
Internal notes are private, append-oriented and attributable.

`AC-FBK-021`  
Next Action records work intent but does not execute itself automatically.

`AC-FBK-022`  
AI may classify/summarize/draft but cannot change Feedback business state autonomously.

`AC-FBK-023`  
AI cannot send professional reply automatically.

`AC-FBK-024`  
Feedback intake functions without AI.

`AC-FBK-025`  
Feedback intake functions while external notification channels are unavailable.

`AC-FBK-026`  
Public form has rate limits/payload limits/anti-abuse controls.

`AC-FBK-027`  
Ambiguous anti-spam cases do not silently discard legitimate professional messages.

`AC-FBK-028`  
Public submission retry is idempotent.

`AC-FBK-029`  
Duplicate detection remains separate from idempotency and does not auto-merge records.

`AC-FBK-030`  
CreateCastingFromInquiry requires explicit Human action.

`AC-FBK-031`  
CASTING_INVITATION does not automatically create Casting.

`AC-FBK-032`  
Feedback remains persisted after Casting conversion.

`AC-FBK-033`  
Feedback↔Casting relation is navigable bidirectionally.

`AC-FBK-034`  
Casting Source created from Feedback excludes Admin notes/workflow metadata.

`AC-FBK-035`  
Casting conversion is idempotent and race-safe.

`AC-FBK-036`  
Feedback is not an Opportunity stage and cannot auto-qualify business pipeline.

`AC-FBK-037`  
Material request does not grant access to private media automatically.

`AC-FBK-038`  
Public surface cannot read/enumerate submitted Feedback by ID.

`AC-FBK-039`  
Feedback data does not appear in Public Search or SEO.

`AC-FBK-040`  
Admin Search/cache are authorization-scoped.

`AC-FBK-041`  
Analytics avoid raw message/email/phone/attachment content by default.

`AC-FBK-042`  
VOP may surface overdue/unprocessed inquiries but cannot reply, resolve or convert them autonomously.

`AC-FBK-043`  
Inbox remains authoritative even when notification badges/deliveries fail.

`AC-FBK-044`  
Incoming message cannot be silently overwritten after receipt.

`AC-FBK-045`  
Operational Feedback updates are version-aware.

`AC-FBK-046`  
Public submission cannot set read/workflow/priority/responsible/Casting fields.

`AC-FBK-047`  
Notification provider calls never execute inside Feedback database transaction.

`AC-FBK-048`  
Archive preserves Feedback history.

`AC-FBK-049`  
Restore does not resend notifications or recreate Casting.

`AC-FBK-050`  
Privacy purge performs dependency discovery across attachments, Castings, AI, notification copies and search/index data.

`AC-FBK-051`  
Converted Feedback cannot be deleted in a way that destroys Casting provenance.

`AC-FBK-052`  
Attachment purge follows storage-safe dependency-aware workflow.

`AC-FBK-053`  
Migration does not invent Feedback type or sender semantics.

`AC-FBK-054`  
Legacy internal notes remain distinct from migrated incoming Source.

`AC-FBK-055`  
Migration is idempotent.

`AC-FBK-056`  
Public form and Admin Inbox satisfy keyboard/screen-reader accessibility requirements.

`AC-FBK-057`  
All persistence, notification-failure, privacy, attachment, conversion and concurrency invariants have deterministic E2E tests.

---

# 428. Финальная доктрина

> **Feedback & Inquiries Module является надёжным professional intake layer сайта. Любое допустимое обращение сначала сохраняется как authoritative Feedback вместе с attachment references, Audit и Outbox в одной транзакции; только после COMMIT создаются In-App, WhatsApp или Email notifications. Поэтому сбой внешнего provider никогда не означает потерю обращения и не должен превращать уже сохранённое сообщение в ошибку отправки для посетителя. Incoming message, read state, workflow state, responsible Admin, internal notes, next action и notification delivery являются различными понятиями. Вложения являются private operational files и не становятся MediaAssets автоматически. Feedback остаётся private/write-only для public surface и не попадает в Search/SEO. `CreateCastingFromInquiry` выполняется только явным Human action, идемпотентно создаёт Casting и двусторонний provenance, при этом оригинальный Feedback остаётся неизменным источником входящего обращения. AI/VOP могут классифицировать, суммировать, предлагать действия и готовить Draft reply, но не имеют права самостоятельно отвечать, закрывать обращение, создавать Casting или менять pipeline outcome.**