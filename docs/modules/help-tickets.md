# HELP CENTER & SUPPORT TICKETS MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация Help Center, Support Tickets, ticket lifecycle, categories, assignment, SLA, attachments, replies, knowledge-base integration, notifications, privacy и domain isolation

**Целевой файл:** `docs/modules/help-tickets.md`  
**Документ:** DOC-132  
**Статус:** ✅ Completed  
**Тип:** Module / Admin Support / Help Center / Tickets / Knowledge Base / Operational Support

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
- `docs/architecture/media-processing.md`
- `docs/architecture/background-jobs.md`
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
- `docs/modules/feedback.md`
- `docs/modules/notifications.md`
- `docs/modules/virtual-operator.md`
- `docs/modules/bb-assistant.md`

---

# 1. Назначение модуля

Help Center & Support Tickets Module предназначен для **технической и эксплуатационной поддержки Admin-пользователей сайта актрисы**.

Модуль должен обеспечивать:

- русскоязычный Help Center;
- базу знаний;
- поиск по справочным материалам;
- создание support tickets;
- категории обращений;
- priority;
- assignment;
- ticket lifecycle;
- публичные ответы заявителю;
- internal support notes;
- attachments;
- SLA / due dates;
- notification integration;
- связь с техническими объектами системы;
- incident/problem classification;
- audit;
- privacy;
- retention.

---

# 2. Главная доктрина

> **Support Ticket описывает проблему эксплуатации системы, вопрос по функционалу или запрос технической поддержки. Он никогда не является профессиональным входящим обращением к актрисе и не должен использоваться вместо Feedback, Casting или Opportunity.**

Canonical:

```text id="hlp-can-001"
Admin User
    │
    ├── needs instructions
    │        ↓
    │    Help Center
    │
    └── needs support
             ↓
        Support Ticket
             ↓
     Support Workflow
             ↓
        Resolution
```

---

# 3. Critical domain separation

```text id="hlp-can-002"
HelpTicket
≠ Feedback
≠ Casting
≠ Opportunity
≠ Notification
≠ VOP Finding
≠ Audit Event
```

---

# 4. Examples

Правильный Help Ticket:

```text id="hlp-ex-001"
«Не загружается видео в медиатеку»
```

Правильный Feedback:

```text id="hlp-ex-002"
«Приглашаем Екатерину на кастинг»
```

Правильный VOP Finding:

```text id="hlp-ex-003"
«Media derivative failed»
```

---

# 5. HLP-INV-001 — Support Domain Is Operational

Help Ticket не становится профессиональным Business Source.

---

# 6. Module identifiers

Используются:

```text id="hlp-ids-001"
HLP-*
HLP-KB-*
HLP-TKT-*
HLP-CAT-*
HLP-PRI-*
HLP-STS-*
HLP-ASG-*
HLP-SLA-*
HLP-RPL-*
HLP-NOT-*
HLP-ATT-*
HLP-SEC-*
HLP-INV-*
HLP-AP-*
E2E-HLP-*
```

---

# 7. Help Center

Help Center является read-only справочным разделом для Admin.

---

# 8. Baseline sections

Рекомендуются:

```text id="hlp-kb-001"
Начало работы
Профиль
Медиатека
Портфолио
Эмоциональное портфолио
Проекты и роли
Обучение
Навыки и языки
Ссылки и QR
Анкеты
Public Questionnaire Builder
Кастинги
Feedback
Opportunity Pipeline
Темы сайта
BB Assistant
VOP
Analytics
Social Publishing
Безопасность
Резервное копирование / восстановление
Частые ошибки
```

---

# 9. HLP-KB-001 — Help Content Does Not Define Business Rules Independently

Help Center должен отражать canonical product/domain documentation.

---

# 10. Knowledge Base Article

Suggested entity:

```text id="hlp-kb-entity-001"
HelpArticle
```

---

# 11. Representative fields

```text id="hlp-kb-fields-001"
id
slug
category
title
summary?
body
locale
status
version
display_order
search_keywords?
created_at
updated_at
published_at?
```

---

# 12. Article states

```text id="hlp-kb-state-001"
DRAFT
PUBLISHED
ARCHIVED
```

---

# 13. HLP-KB-002 — Draft Article Is Not Public/Admin-Visible Help Content by Default

---

# 14. Baseline locale

Русский:

```text id="hlp-kb-locale-001"
ru
```

---

# 15. Future localization

Architecture should allow:

```text id="hlp-kb-locale-002"
en
```

and additional locales later.

---

# 16. Article versioning

Published help article changes should preserve revision/history where operationally useful.

---

# 17. HLP-KB-003 — Help Article History Is Separate From Product Source Revisions

---

# 18. Help Search

Admin can search:

```text id="hlp-kb-search-001"
title
summary
body
keywords
category
```

---

# 19. Public Search

Help Center is not part of actor Public Search.

---

# 20. HLP-SEC-001 — Help Search Is Admin Scope

---

# 21. Contextual help

Admin UI MAY link directly to relevant Help Article.

Example:

```text id="hlp-context-001"
Media upload error
→ “Поддерживаемые видеоформаты”
```

---

# 22. HLP-KB-004 — Contextual Help Is Navigation Only

It does not change system state.

---

# 23. Support Ticket entity

Suggested:

```text id="hlp-ticket-entity-001"
support_tickets
```

---

# 24. Representative fields

```text id="hlp-ticket-fields-001"
id
ticket_number
profile_id?
created_by_admin_id
category
subject
description
priority
status
assigned_to?
source_context_type?
source_context_id?
sla_policy_id?
first_response_due_at?
resolution_due_at?
resolved_at?
closed_at?
version
created_at
updated_at
```

---

# 25. Ticket number

Human-readable identifier.

Example:

```text id="hlp-ticket-number-001"
SUP-2026-00124
```

---

# 26. HLP-TKT-001 — Ticket Number ≠ Primary Database Identity

Use UUID internally.

---

# 27. Ticket subject

Short summary.

---

# 28. Ticket description

Detailed problem/request supplied by Admin.

---

# 29. Ticket categories

Canonical baseline:

```text id="hlp-cat-001"
GENERAL
ACCOUNT_ACCESS
PROFILE
MEDIA
PORTFOLIO
QUESTIONNAIRE
CASTING
FEEDBACK
OPPORTUNITY
THEME
AI
SOCIAL
ANALYTICS
NOTIFICATIONS
SECURITY
BACKUP_RESTORE
PERFORMANCE
BUG
FEATURE_REQUEST
DATA_CORRECTION
OTHER
```

---

# 30. HLP-CAT-001 — Category Is Support Routing Metadata

It does not modify underlying domain entity.

---

# 31. BUG

Used when observed behavior contradicts intended product behavior.

---

# 32. FEATURE_REQUEST

Request for capability that does not currently exist.

---

# 33. DATA_CORRECTION

For support-assisted repair where Admin cannot correct data through normal supported UI.

---

# 34. HLP-CAT-002 — Data Correction Ticket Does Not Authorize Direct Unsafe DB Mutation

Any repair must still use supported domain/migration/admin maintenance workflow.

---

# 35. Security category

For:

- suspicious access;
- permission issue;
- data exposure concern;
- credential/integration compromise;
- security control failure.

---

# 36. HLP-SEC-002 — Security Ticket May Require Restricted Visibility

---

# 37. Priority

Canonical:

```text id="hlp-priority-001"
P1_CRITICAL
P2_HIGH
P3_NORMAL
P4_LOW
```

---

# 38. P1_CRITICAL

Examples:

- public exposure of private information;
- site unavailable;
- authentication/security incident;
- destructive data-loss condition.

---

# 39. P2_HIGH

Examples:

- critical professional workflow blocked;
- Questionnaire cannot be generated before active casting deadline;
- media subsystem unavailable.

---

# 40. P3_NORMAL

Normal support issue.

---

# 41. P4_LOW

Minor UX, question, enhancement.

---

# 42. HLP-PRI-001 — Ticket Priority Is Support Priority

It does not become:

- Opportunity priority;
- Casting priority;
- Notification business priority.

---

# 43. Priority setting

Human-controlled baseline.

System may recommend priority based on deterministic category/context.

---

# 44. Security exposure

May automatically force minimum P1/P2 policy where deterministic hard security rule applies.

---

# 45. HLP-PRI-002 — Automatic Escalation Is Policy-Based

Not LLM sentiment.

---

# 46. Ticket lifecycle

Canonical:

```text id="hlp-status-001"
OPEN
TRIAGED
IN_PROGRESS
WAITING_FOR_USER
WAITING_FOR_SYSTEM
RESOLVED
CLOSED
CANCELLED
```

---

# 47. OPEN

Ticket created.

---

# 48. TRIAGED

Category/priority/responsibility reviewed.

---

# 49. IN_PROGRESS

Support actively working.

---

# 50. WAITING_FOR_USER

Further information/action required from requester.

---

# 51. WAITING_FOR_SYSTEM

Waiting for:

- background job;
- provider response;
- deployment;
- maintenance window;
- external dependency.

---

# 52. RESOLVED

Support believes issue has been resolved.

---

# 53. CLOSED

Ticket lifecycle finalized.

---

# 54. CANCELLED

Ticket no longer relevant or was created by mistake.

---

# 55. HLP-STS-001 — Resolved ≠ Closed

Allows verification/reopen window.

---

# 56. Allowed common transitions

```text id="hlp-status-flow-001"
OPEN
→ TRIAGED
→ IN_PROGRESS
→ RESOLVED
→ CLOSED
```

Alternative:

```text id="hlp-status-flow-002"
IN_PROGRESS
→ WAITING_FOR_USER
→ IN_PROGRESS
```

or:

```text id="hlp-status-flow-003"
IN_PROGRESS
→ WAITING_FOR_SYSTEM
→ IN_PROGRESS
```

---

# 57. Reopen

A RESOLVED/CLOSED ticket may be reopened by explicit action when issue recurs or resolution failed.

---

# 58. Suggested command:

```text id="hlp-reopen-001"
ReopenSupportTicket
```

---

# 59. HLP-STS-002 — Reopen Is Explicit and Audited

---

# 60. Ticket status history

Suggested entity:

```text id="hlp-history-001"
support_ticket_status_history
```

---

# 61. Fields

```text id="hlp-history-fields-001"
id
ticket_id
from_status
to_status
changed_by
changed_at
reason?
```

---

# 62. HLP-STS-003 — Ticket Status History Is Append-Only

---

# 63. Assignment

Ticket may be assigned to:

```text id="hlp-assignment-001"
SUPPORT_USER
SUPPORT_TEAM
UNASSIGNED
```

depending deployment.

---

# 64. For single-admin/small installation

Assignment model may remain simple, but schema should not require hardcoding one person.

---

# 65. HLP-ASG-001 — Assignment Is Operational Ownership

Not Public Contact or professional representative.

---

# 66. Assignment history

Should be auditable.

---

# 67. Auto-assignment

May be policy-based by category.

Example:

```text id="hlp-assignment-002"
SECURITY → security support role
MEDIA → technical support
```

---

# 68. HLP-ASG-002 — Auto-Assignment Uses Deterministic Routing Rules

---

# 69. SLA

Support module MAY use SLA policies.

Suggested entity:

```text id="hlp-sla-entity-001"
support_sla_policies
```

---

# 70. SLA fields

```text id="hlp-sla-fields-001"
priority
first_response_target
resolution_target
business_hours_policy?
enabled
```

---

# 71. SLA examples

Conceptually:

```text id="hlp-sla-example-001"
P1 → first response fastest
P4 → longest target
```

Exact contractual hours belong operations/configuration.

---

# 72. HLP-SLA-001 — SLA Policy Is Configurable

Do not hardcode legal/commercial promise unless explicitly approved.

---

# 73. First response

Means first meaningful support response, not automatic ticket-created notification.

---

# 74. HLP-SLA-002 — Automated Acknowledgement ≠ Human First Response

---

# 75. Waiting states

SLA clock MAY pause in:

```text id="hlp-sla-pause-001"
WAITING_FOR_USER
```

if policy explicitly defines it.

---

# 76. System wait

Pause behavior must also be explicit.

---

# 77. HLP-SLA-003 — SLA Pauses Are Policy-Driven

---

# 78. SLA breach

Derived operational state:

```text id="hlp-sla-state-001"
ON_TRACK
AT_RISK
BREACHED
NOT_APPLICABLE
```

---

# 79. HLP-SLA-004 — SLA State Does Not Change Ticket Business Status

---

# 80. Ticket replies

Two distinct types:

```text id="hlp-reply-types-001"
PUBLIC_REPLY
INTERNAL_NOTE
```

---

# 81. PUBLIC_REPLY

Visible to requester/admin audience allowed by ticket access policy.

---

# 82. INTERNAL_NOTE

Visible only to support/internal authorized users.

---

# 83. HLP-RPL-001 — Public Reply and Internal Note Are Separate Data Types

---

# 84. Suggested entity

```text id="hlp-reply-entity-001"
support_ticket_messages
```

---

# 85. Fields

```text id="hlp-reply-fields-001"
id
ticket_id
message_type
body
author_type
author_id
created_at
edited_at?
```

---

# 86. Editing replies

Preferred baseline:

- corrections through new message or bounded edit history;
- important support communication should preserve provenance.

---

# 87. HLP-RPL-002 — Reply Editing Does Not Erase Important History

---

# 88. Internal note privacy

Must never appear in:

- requester/public ticket response;
- email/WhatsApp notification payload;
- public search;
- actor public UI.

---

# 89. HLP-SEC-003 — Internal Notes Are Strictly Private

---

# 90. BB Assistant integration

BB Assistant MAY help draft:

- support response;
- clarification request;
- troubleshooting explanation.

---

# 91. But BB output remains Draft.

---

# 92. HLP-RPL-003 — AI Draft ≠ Support Reply Sent

---

# 93. BB context

Should contain only:

- ticket message/thread needed;
- relevant technical/help context;
- knowledge article excerpts if permitted.

---

# 94. It should not receive unrelated professional/private data.

---

# 95. Attachments

Tickets may support attachments.

Examples:

- screenshot;
- log excerpt;
- exported config;
- small diagnostic file;
- document illustrating issue.

---

# 96. Suggested entity:

```text id="hlp-attachment-entity-001"
support_ticket_attachments
```

---

# 97. Attachment fields

```text id="hlp-attachment-fields-001"
id
ticket_id
message_id?
media_asset_id?
filename
content_type
byte_size
scan_state
access_class
created_by
created_at
```

---

# 98. Reuse Media infrastructure where feasible

Attachment bytes should use managed storage/media mechanisms rather than arbitrary filesystem blobs in DB.

---

# 99. HLP-ATT-001 — Attachment Metadata ≠ Binary Storage

---

# 100. Attachment access

Support attachments are private baseline.

---

# 101. HLP-ATT-002 — Ticket Attachment Is Never Public Media Automatically

---

# 102. No Portfolio reuse

Uploading screenshot to support does not add it to Media Library professional collections automatically.

---

# 103. HLP-ATT-003 — Support Attachment ≠ Professional MediaAsset Usage

---

# 104. File validation

Must enforce:

- size limit;
- MIME/type allowlist;
- extension validation;
- malware/security scanning where available;
- safe download headers.

---

# 105. HLP-SEC-004 — Ticket Upload Is Untrusted Input

---

# 106. Executables

Baseline should reject executable/script attachments unless specific secured support workflow exists.

---

# 107. Images

May show safe previews.

---

# 108. Logs/text

May require redaction before persistence/display.

---

# 109. Secret warning

UI should warn not to upload:

- passwords;
- API keys;
- access tokens;
- private keys.

---

# 110. HLP-SEC-005 — Support Ticket Is Not Secret Vault

---

# 111. Automatic secret detection

May identify common credential patterns and warn/block.

---

# 112. It must not log detected secret value.

---

# 113. HLP-SEC-006 — Secret Scanner Is Redaction-Aware

---

# 114. Source context link

Ticket may reference affected system object.

Examples:

```text id="hlp-source-context-001"
MediaAsset
Questionnaire
Casting
ProfessionalLink
ThemeRevision
BackgroundJob
```

---

# 115. Context link is operational navigation.

---

# 116. HLP-TKT-002 — Context Reference Does Not Transfer Domain Ownership

---

# 117. Example

Ticket:

```text id="hlp-ticket-context-example-001"
“Questionnaire PDF generation fails”
```

may reference `QuestionnaireRevision`.

Ticket status changes do not change Questionnaire lifecycle.

---

# 118. HLP-TKT-003 — Closing Ticket Never Mutates Referenced Business Entity

---

# 119. Conversely

Fixing underlying entity does not automatically close support ticket unless explicit reconciliation/policy does so.

---

# 120. HLP-TKT-004 — Source Resolution ≠ Ticket Closure Automatically

VOP may suggest resolution, but Human/support confirms baseline.

---

# 121. Knowledge Base suggestions

When creating ticket, system may suggest relevant Help Articles.

---

# 122. Example

Subject contains:

```text id="hlp-kb-suggest-001"
“QR не сканируется”
```

suggest:

```text id="hlp-kb-suggest-002"
“Требования к QR в PDF”
```

---

# 123. Suggestions may use:

- category;
- keywords;
- semantic search;
- optional AI.

---

# 124. HLP-KB-005 — Suggested Article Does Not Prevent Ticket Creation

---

# 125. No forced deflection

Admin can still create ticket.

---

# 126. AI Help Assistant

Optional future capability may answer questions from approved Help Center content.

---

# 127. It must be retrieval-grounded.

---

# 128. HLP-AI-001 — Help AI Cannot Invent Product Capabilities

---

# 129. If no reliable KB answer:

recommend creating ticket.

---

# 130. HLP-AI-002 — Unknown Support Answer Remains Unknown

---

# 131. Help AI cannot execute domain actions unless routed through separate authorized tool/action policy.

Baseline: read-only support guidance.

---

# 132. HLP-AI-003 — Help AI Is Read-Only Baseline

---

# 133. Ticket creation sources

Baseline:

```text id="hlp-ticket-source-001"
ADMIN_HELP_CENTER
ADMIN_CONTEXTUAL_HELP
SYSTEM_SUPPORT_UI
```

---

# 134. Public visitor

Does not create technical Help Ticket baseline.

Professional inquiries use Feedback.

---

# 135. HLP-SEC-007 — Public Contact Form Does Not Route Into Help Tickets

---

# 136. VOP integration

VOP Finding MAY offer:

```text id="hlp-vop-001"
Create Support Ticket
```

for an operational issue requiring human technical support.

---

# 137. This is explicit Human action baseline.

---

# 138. HLP-VOP-001 — VOP Finding ≠ Ticket

---

# 139. If ticket created from Finding

Preserve relation:

```text id="hlp-vop-rel-001"
VOPFinding ↔ HelpTicket
```

---

# 140. Ticket resolution does not erase Finding automatically.

VOP re-check determines finding status.

---

# 141. HLP-VOP-002 — Support Resolution and VOP Reconciliation Are Separate

---

# 142. Notifications

Events MAY generate notification intents:

```text id="hlp-not-events-001"
SUPPORT_TICKET_CREATED
SUPPORT_TICKET_ASSIGNED
SUPPORT_REPLY_RECEIVED
SUPPORT_TICKET_AT_RISK
SUPPORT_TICKET_BREACHED
SUPPORT_TICKET_RESOLVED
```

---

# 143. Notifications route through Notifications Module.

---

# 144. HLP-NOT-001 — Support Domain Does Not Call WhatsApp/Email Provider Directly

---

# 145. Notification content

Minimal:

```text id="hlp-not-content-001"
Ticket number
subject
priority/status
protected Admin link
```

---

# 146. Avoid full logs/attachments in external notification.

---

# 147. HLP-SEC-008 — Ticket Notification Is Data-Minimized

---

# 148. Search

Support tickets can be searched by Admin/support users.

---

# 149. Search fields

```text id="hlp-search-fields-001"
ticket number
subject
category
status
priority
assigned user/team
description
message text where policy allows
```

---

# 150. Sensitive security tickets

May have restricted search visibility.

---

# 151. HLP-SEC-009 — Ticket Search Respects Ticket ACL

---

# 152. Public Search/SEO

Never indexes Help Tickets.

---

# 153. HLP-SEC-010 — Support Tickets Are Never Public SEO Content

---

# 154. Help Articles

May be Admin-only in current product.

If Help Center later exposed publicly, publication scope must be explicit and still separated from actor-site SEO.

---

# 155. Ticket access model

At minimum:

```text id="hlp-acl-001"
REQUESTER
SUPPORT_AGENT
SUPPORT_ADMIN
```

---

# 156. Security ticket

Could require:

```text id="hlp-acl-002"
SECURITY_SUPPORT
```

---

# 157. HLP-SEC-011 — Ticket Authorization Is Server-Side

---

# 158. Guessing UUID/ticket number

Does not grant read access.

---

# 159. Ticket number enumeration

Public exposure is absent baseline, but Admin route still must authorize.

---

# 160. HLP-SEC-012 — Human-Readable Ticket Number Is Not Access Credential

---

# 161. Internal note authorization

Only support-internal roles.

Requester cannot fetch by hidden API parameter.

---

# 162. HLP-SEC-013 — Internal Note Filtering Occurs Before Serialization

---

# 163. Source context ACL

Having access to HelpTicket does not necessarily grant access to restricted referenced source entity.

---

# 164. HLP-SEC-014 — Ticket Link Does Not Escalate Referenced Domain Access

---

# 165. Diagnostics

Support may capture structured technical metadata.

Examples:

```text id="hlp-diag-001"
application version
browser family/version
route
job ID
correlation ID
error code
timestamp
environment
```

---

# 166. Prefer structured diagnostics over large log dumps.

---

# 167. HLP-SEC-015 — Diagnostics Are Data-Minimized

---

# 168. Safe automatic diagnostic context

Ticket creation UI MAY include:

- current route;
- browser;
- app version;
- non-secret error code.

---

# 169. Must not automatically include:

- auth cookie;
- JWT;
- full request headers;
- private form data;
- full database payload.

---

# 170. HLP-SEC-016 — Auto-Diagnostics Have Hard Deny Fields

---

# 171. Correlation ID

Useful to connect ticket with observability logs without copying logs into ticket.

---

# 172. HLP-INV-002 — Correlation Reference Preferred Over Diagnostic Duplication

---

# 173. Incident classification

Support Ticket may optionally be linked to an operational Incident/Problem in future.

---

# 174. Current baseline does not require full ITSM.

---

# 175. HLP-INV-003 — Ticket Does Not Implicitly Become Incident Management Platform

---

# 176. Feature Request handling

Feature request may be:

- accepted;
- declined;
- planned;
- converted to GitHub issue/product backlog item.

---

# 177. Conversion must be explicit.

---

# 178. HLP-TKT-005 — Support Ticket ≠ Product Backlog Item

---

# 179. GitHub integration

Future/optional:

```text id="hlp-github-001"
HelpTicket
→ explicit Create GitHub Issue
```

---

# 180. Must preserve relation.

---

# 181. Closing GitHub issue does not automatically close Ticket unless explicit workflow configured.

---

# 182. HLP-TKT-006 — External Issue Tracker Remains Separate Domain

---

# 183. SLA reminders

Background jobs may schedule:

- first response approaching;
- resolution approaching;
- breached SLA.

---

# 184. HLP-SLA-005 — Reminder Does Not Change Ticket Priority/Status Automatically

---

# 185. Escalation

A deterministic SLA policy may escalate support notification/routing.

---

# 186. It should not change professional domains.

---

# 187. Background jobs

Suggested:

```text id="hlp-jobs-001"
SUPPORT_SLA_RECALCULATE
SUPPORT_SLA_NOTIFY
SUPPORT_ATTACHMENT_SCAN
SUPPORT_SEARCH_INDEX
SUPPORT_RETENTION_CLEANUP
```

---

# 188. Job retry

Must be idempotent.

---

# 189. Attachment scan failure

Attachment stays unavailable/quarantined until validated.

---

# 190. HLP-ATT-004 — Unscanned Attachment Is Not Downloadable Baseline

---

# 191. Quarantine states

```text id="hlp-attachment-states-001"
UPLOADING
SCANNING
READY
REJECTED
QUARANTINED
FAILED
```

---

# 192. HLP-ATT-005 — Attachment State Is Independent From Ticket State

---

# 193. Ticket can continue without unsafe attachment.

---

# 194. Data correction workflow

If ticket requires correction to business Source:

support must use approved command/domain flow.

---

# 195. Example:

Incorrect Profile field cannot be “fixed” with:

```text id="hlp-bad-sql-001"
UPDATE profile ...
```

through generic ticket tool.

---

# 196. HLP-SEC-017 — Support Module Has No Generic Business DB Mutation Tool

---

# 197. Privileged maintenance

If unavoidable, use dedicated audited maintenance procedure documented separately.

---

# 198. Ticket resolution note

Should explain:

- what was found;
- what was changed;
- any remaining action;
- relevant Help Article if applicable.

---

# 199. HLP-RPL-004 — Resolution Is Human-Readable

---

# 200. Resolution code

Recommended controlled values:

```text id="hlp-resolution-001"
FIXED
CONFIGURED
USER_GUIDANCE
DUPLICATE
NOT_REPRODUCIBLE
BY_DESIGN
FEATURE_REQUEST
EXTERNAL_DEPENDENCY
NO_ACTION_REQUIRED
OTHER
```

---

# 201. HLP-STS-004 — Resolution Code ≠ Ticket Status

A ticket can be `RESOLVED` with resolution code `USER_GUIDANCE`.

---

# 202. Duplicate tickets

Can link:

```text id="hlp-duplicate-001"
duplicate_of_ticket_id
```

---

# 203. Duplicate ticket should preserve its original messages/attachments.

---

# 204. HLP-TKT-007 — Duplicate Linking Does Not Delete Ticket History

---

# 205. Merge tickets

Not baseline.

If implemented, must preserve complete provenance.

---

# 206. Ticket comments order

Use authoritative server timestamps and stable ordering.

---

# 207. Concurrency

Ticket uses:

```text id="hlp-version-001"
version
```

---

# 208. High-impact mutations require `expectedVersion`.

Examples:

- status;
- priority;
- assignment;
- resolution.

---

# 209. HLP-INV-004 — Ticket Workflow Uses Optimistic Concurrency

---

# 210. Simultaneous status change

Only one current state transition commits against same version.

---

# 211. Reply posting

Can be independently append-only/idempotent.

---

# 212. HLP-RPL-005 — Duplicate Network Retry Must Not Duplicate Reply

---

# 213. Reply idempotency

Use `idempotencyKey`.

---

# 214. Attachment retry

Same upload intent must not create duplicate attachment associations inadvertently.

---

# 215. Ticket creation

Idempotent when retrying same submit after network loss.

---

# 216. HLP-TKT-008 — Ticket Creation Is Idempotent

---

# 217. API commands

Canonical:

```text id="hlp-commands-001"
CreateSupportTicket
UpdateSupportTicketCategory
UpdateSupportTicketPriority
AssignSupportTicket
UnassignSupportTicket
TransitionSupportTicketStatus
ReopenSupportTicket
AddSupportTicketPublicReply
AddSupportTicketInternalNote
AttachSupportTicketFile
RemoveSupportTicketAttachment
ResolveSupportTicket
CloseSupportTicket
CancelSupportTicket
LinkDuplicateSupportTicket
CreateSupportTicketFromVOPFinding
```

---

# 218. Knowledge Base commands

```text id="hlp-commands-002"
CreateHelpArticle
UpdateHelpArticle
PublishHelpArticle
ArchiveHelpArticle
ReorderHelpArticles
```

---

# 219. Queries

```text id="hlp-queries-001"
GetHelpCenter
SearchHelpArticles
GetHelpArticle
ListSupportTickets
GetSupportTicket
GetSupportTicketTimeline
GetSupportTicketMessages
GetSupportTicketSLA
GetSupportTicketAttachments
GetSupportDashboard
```

---

# 220. Ticket creation input

Conceptually:

```text id="hlp-ticket-input-001"
category
subject
description
priority?
sourceContext?
attachments?
idempotencyKey
```

---

# 221. Client cannot set

```text id="hlp-ticket-deny-001"
resolved_at
closed_at
assigned support identity arbitrarily
internal note
SLA result
security ACL
```

unless authorized role/command explicitly allows.

---

# 222. HLP-SEC-018 — Server Derives Privileged Ticket Fields

---

# 223. Public reply input

```text id="hlp-reply-input-001"
ticketId
body
expectedTicketVersion?
idempotencyKey
```

---

# 224. Internal note

Requires separate command and authorization.

---

# 225. HLP-RPL-006 — Client Cannot Convert Internal Note To Public Reply by Field Tampering

---

# 226. Error taxonomy

At minimum:

```text id="hlp-errors-001"
HELP_ARTICLE_NOT_FOUND
HELP_ARTICLE_NOT_PUBLISHED
SUPPORT_TICKET_NOT_FOUND
SUPPORT_TICKET_ACCESS_DENIED
SUPPORT_TICKET_CATEGORY_INVALID
SUPPORT_TICKET_PRIORITY_INVALID
SUPPORT_TICKET_STATUS_INVALID
SUPPORT_TICKET_TRANSITION_INVALID
SUPPORT_TICKET_STALE_VERSION
SUPPORT_TICKET_ALREADY_CLOSED
SUPPORT_TICKET_REOPEN_NOT_ALLOWED
SUPPORT_ASSIGNMENT_INVALID
SUPPORT_REPLY_EMPTY
SUPPORT_INTERNAL_NOTE_FORBIDDEN
SUPPORT_ATTACHMENT_INVALID
SUPPORT_ATTACHMENT_TOO_LARGE
SUPPORT_ATTACHMENT_TYPE_FORBIDDEN
SUPPORT_ATTACHMENT_NOT_READY
SUPPORT_ATTACHMENT_QUARANTINED
SUPPORT_SLA_POLICY_INVALID
SUPPORT_DUPLICATE_LINK_INVALID
SUPPORT_IDEMPOTENCY_CONFLICT
```

---

# 227. Ticket timeline

Should combine:

- creation;
- status transitions;
- assignment changes;
- replies;
- internal notes where authorized;
- attachment events;
- SLA events;
- resolution.

---

# 228. HLP-INV-005 — Timeline Is Projection, Underlying Records Remain Typed

Do not collapse everything into one generic mutable JSON log.

---

# 229. Help Dashboard

Recommended:

```text id="hlp-dashboard-001"
Open
Unassigned
P1 / P2
Waiting for User
Waiting for System
At Risk
SLA Breached
Resolved Recently
Security
Feature Requests
```

---

# 230. Dashboard counts are derived.

---

# 231. HLP-INV-006 — Dashboard Count Is Not Ticket Source

---

# 232. Accessibility

Help Center/Ticket UI must support:

- semantic headings;
- keyboard navigation;
- screen-reader labels;
- status text;
- priority text, not color only;
- accessible attachment controls;
- readable timeline.

---

# 233. Markdown/rich text

If support messages/articles use Markdown:

must sanitize output.

---

# 234. HLP-SEC-019 — Help Content Cannot Inject Script/Unsafe HTML

---

# 235. Links in support message

Must use safe URL protocols.

---

# 236. External troubleshooting URL

No automatic fetch.

---

# 237. HLP-SEC-020 — Ticket URL Is Untrusted Content

---

# 238. Privacy

Tickets may contain:

- screenshots;
- names;
- email addresses;
- internal operational information.

---

# 239. Data minimization

UI should request only information needed to solve issue.

---

# 240. HLP-SEC-021 — Support Form Does Not Ask for Unnecessary Professional/Personal Data

---

# 241. Analytics

Can measure:

```text id="hlp-analytics-001"
ticket_created
ticket_first_response
ticket_resolved
ticket_reopened
article_view
article_search
article_helpful
```

---

# 242. Must not copy:

- ticket description;
- internal notes;
- attachment content.

---

# 243. HLP-SEC-022 — Support Analytics Is Metadata-Only

---

# 244. Metrics

Useful:

- ticket volume;
- category distribution;
- first-response time;
- resolution time;
- reopen rate;
- SLA breach rate;
- helpful article rate;
- repeated issue classes.

---

# 245. No professional actor scoring.

---

# 246. VOP may use support trends

Example:

```text id="hlp-vop-trends-001"
frequent QR failures
frequent media upload errors
```

for operational recommendations.

---

# 247. It must not inspect private ticket content beyond configured purpose/permissions.

---

# 248. HLP-VOP-003 — Support Trend Analysis Is Privacy-Scoped

---

# 249. Audit

Must include:

```text id="hlp-audit-001"
priority change
assignment change
status transition
internal note creation
security ACL change
attachment deletion
ticket reopen
ticket resolution
ticket close
```

---

# 250. Public reply itself is stored message history; Audit can reference message ID.

---

# 251. HLP-INV-007 — Ticket History and Audit Are Different Records

---

# 252. Outbox events

Suggested:

```text id="hlp-events-001"
SupportTicketCreated
SupportTicketAssigned
SupportTicketPriorityChanged
SupportTicketStatusChanged
SupportTicketReplyAdded
SupportTicketResolved
SupportTicketClosed
SupportTicketReopened
SupportSLABreached
```

---

# 253. Consumers

```text id="hlp-consumers-001"
Notifications
Admin Dashboard
Analytics
VOP
Search
```

---

# 254. Retention

Separate retention classes:

```text id="hlp-retention-001"
Help Articles
Tickets
Messages
Internal Notes
Attachments
Diagnostics
SLA History
Audit
```

---

# 255. Closed tickets

May retain sufficiently long for support history and recurring-problem analysis.

---

# 256. Attachments

Can have shorter retention than ticket metadata if no longer required.

---

# 257. HLP-RET-001 — Ticket and Attachment Retention Are Independent

---

# 258. Security ticket data

May require stricter access and retention.

---

# 259. Privacy deletion

Should support redaction/purge of unnecessary PII while preserving safe operational record where required.

---

# 260. HLP-SEC-023 — Ticket History Does Not Justify Permanent Unnecessary PII

---

# 261. Hard delete

Exceptional.

Prefer:

```text id="hlp-delete-001"
CLOSED
→ retention
→ redact/purge according to policy
```

---

# 262. Backup

Must preserve:

- ticket records;
- replies/notes;
- assignment/status history;
- required attachment references;
- Help Articles.

---

# 263. Restore

Must not:

- resend historical support notifications;
- reopen closed tickets;
- recreate attachments;
- replay SLA alerts;
- repeat external provider sends.

---

# 264. HLP-INV-008 — Restore Does Not Replay Support Side Effects

---

# 265. SLA after restore

Recalculate from authoritative ticket timestamps/current state.

---

# 266. Do not generate duplicate historic breach events blindly.

---

# 267. HLP-SLA-006 — Restore Reconciles SLA State

---

# 268. Migration

Existing Help Center content should map to HelpArticle.

---

# 269. Legacy tickets

Preserve:

- original timestamps;
- status where reliably known;
- requester;
- messages;
- attachments;
- external IDs.

---

# 270. Unknown historical state

Do not invent lifecycle transitions.

---

# 271. HLP-MIG-001 — Migration Does Not Fabricate Ticket History

---

# 272. Legacy logs

A log error is not automatically imported as Support Ticket.

---

# 273. HLP-MIG-002 — Operational Log ≠ Ticket

---

# 274. Existing Feedback incorrectly used as technical support

Migration must be reviewed.

Do not silently reinterpret professional Feedback records.

---

# 275. HLP-MIG-003 — Cross-Domain Reclassification Requires Explicit Migration Rule

---

# 276. Migration idempotency

Required.

---

# 277. Security incident boundary

Help Ticket may be entry point for security concern.

Actual security event/incident handling belongs Security/Operations documentation.

---

# 278. HLP-SEC-024 — Ticket Is Not Sole Security Evidence Store

---

# 279. Observability links

Ticket may reference:

```text id="hlp-obs-link-001"
correlation ID
job ID
deployment ID
error code
```

---

# 280. Detailed logs remain in observability system.

---

# 281. HLP-INV-009 — Support Ticket Does Not Replace Observability

---

# 282. Anti-patterns

`HLP-AP-001`  
Casting invitation stored as Help Ticket.

`HLP-AP-002`  
Technical bug stored as professional Feedback.

`HLP-AP-003`  
Help Ticket creates Opportunity.

`HLP-AP-004`  
Closing Help Ticket closes Casting.

`HLP-AP-005`  
Closing Help Ticket changes Questionnaire state.

`HLP-AP-006`  
Ticket category changes business domain state.

`HLP-AP-007`  
Ticket priority changes Opportunity priority.

`HLP-AP-008`  
Automatic acknowledgement counts as Human first response.

`HLP-AP-009`  
SLA breach changes business Source.

`HLP-AP-010`  
Internal note serialized to requester.

`HLP-AP-011`  
Internal note included in email/WhatsApp notification.

`HLP-AP-012`  
Support screenshot becomes Public Portfolio media.

`HLP-AP-013`  
Attachment upload bypasses scan/type validation.

`HLP-AP-014`  
Executable attachment publicly downloadable.

`HLP-AP-015`  
Support form requests passwords/API keys.

`HLP-AP-016`  
Detected token copied into logs.

`HLP-AP-017`  
Ticket carries authentication cookie automatically.

`HLP-AP-018`  
Support has generic SQL update endpoint.

`HLP-AP-019`  
Support uses ticket context to bypass referenced entity ACL.

`HLP-AP-020`  
Ticket number acts as secret access token.

`HLP-AP-021`  
Public visitor can enumerate tickets.

`HLP-AP-022`  
Support ticket indexed by public SEO/search.

`HLP-AP-023`  
Help AI invents unsupported product capability.

`HLP-AP-024`  
Help AI changes configuration directly.

`HLP-AP-025`  
BB-generated reply auto-sends.

`HLP-AP-026`  
VOP finding auto-creates ticket without explicit policy/Human action baseline.

`HLP-AP-027`  
Ticket resolution automatically resolves VOP Finding.

`HLP-AP-028`  
VOP resolution auto-closes ticket.

`HLP-AP-029`  
Feature Request ticket automatically becomes production change.

`HLP-AP-030`  
GitHub issue closure silently closes ticket.

`HLP-AP-031`  
Duplicate ticket is deleted with its history.

`HLP-AP-032`  
Reply edit erases previous important support communication.

`HLP-AP-033`  
Ticket search exposes restricted security notes.

`HLP-AP-034`  
Analytics stores ticket body.

`HLP-AP-035`  
Analytics stores internal note content.

`HLP-AP-036`  
Support ticket substitutes for Audit.

`HLP-AP-037`  
Support ticket substitutes for Observability.

`HLP-AP-038`  
Restore reopens all unresolved historical tickets incorrectly.

`HLP-AP-039`  
Restore resends old replies/notifications.

`HLP-AP-040`  
Migration fabricates historical status transitions.

`HLP-AP-041`  
Log error automatically imported as ticket.

`HLP-AP-042`  
Source entity fix automatically closes ticket without support review.

`HLP-AP-043`  
Ticket close deletes referenced media/project/questionnaire.

`HLP-AP-044`  
Support assignment becomes public Contact.

`HLP-AP-045`  
Ticket attachment placed in shared public cache.

`HLP-AP-046`  
Unscanned attachment can be opened.

`HLP-AP-047`  
Public reply and internal note use same visibility flag prone to client filtering.

`HLP-AP-048`  
Client decides security ACL.

`HLP-AP-049`  
Client sets SLA status manually.

`HLP-AP-050`  
Missing SLA notification blocks ticket transition.

`HLP-AP-051`  
Ticket creation failure creates orphan attachment.

`HLP-AP-052`  
Retry creates duplicate ticket.

`HLP-AP-053`  
Retry creates duplicate reply.

`HLP-AP-054`  
Cross-profile ticket context link accepted without validation.

`HLP-AP-055`  
Archived Help Article remains suggested as current solution.

`HLP-AP-056`  
Help Center text becomes independent source of conflicting business rule.

`HLP-AP-057`  
Ticket stores provider secret for convenience.

`HLP-AP-058`  
Security attachment accessible to ordinary support role.

`HLP-AP-059`  
Closed ticket is hard-deleted immediately regardless retention/audit needs.

`HLP-AP-060`  
Support Module becomes generic admin superuser for all domains.

---

# 283. Core invariants

`HLP-INV-010`  
Help Ticket is technical/support operational domain, not professional inquiry domain.

`HLP-INV-011`  
Feedback, Casting and Opportunity remain separate from Support Tickets.

`HLP-INV-012`  
Help Center reflects canonical product documentation and does not independently redefine business rules.

`HLP-INV-013`  
Help Articles have explicit publication lifecycle.

`HLP-INV-014`  
Help Search remains Admin-scoped baseline.

`HLP-INV-015`  
Ticket has stable UUID plus human-readable ticket number.

`HLP-INV-016`  
Ticket categories affect support routing only.

`HLP-INV-017`  
Ticket priority affects support workflow only.

`HLP-INV-018`  
Ticket status transitions are validated server-side.

`HLP-INV-019`  
Ticket status history is append-only.

`HLP-INV-020`  
RESOLVED and CLOSED are distinct.

`HLP-INV-021`  
Reopen is explicit and audited.

`HLP-INV-022`  
Assignment is internal operational ownership.

`HLP-INV-023`  
SLA targets are configurable, not implicit contractual promises.

`HLP-INV-024`  
Automatic acknowledgement does not count as meaningful first response.

`HLP-INV-025`  
SLA state remains derived and does not change ticket status itself.

`HLP-INV-026`  
Public Replies and Internal Notes are separate typed records.

`HLP-INV-027`  
Internal Notes are removed before requester serialization.

`HLP-INV-028`  
BB Assistant may draft support text but cannot send automatically.

`HLP-INV-029`  
Ticket attachments are private and do not become professional public media.

`HLP-INV-030`  
Attachments are validated/scanned before download/use.

`HLP-INV-031`  
Support is not a secret-storage mechanism.

`HLP-INV-032`  
Source context link does not transfer ownership or access authority.

`HLP-INV-033`  
Closing ticket does not mutate linked business Source.

`HLP-INV-034`  
Fixing Source does not automatically close ticket baseline.

`HLP-INV-035`  
Help Article suggestion cannot prevent ticket creation.

`HLP-INV-036`  
Help AI is retrieval-grounded/read-only baseline.

`HLP-INV-037`  
Public professional contact forms do not route into Help Tickets.

`HLP-INV-038`  
VOP Finding and Help Ticket remain separate.

`HLP-INV-039`  
Ticket notifications route through Notifications Module.

`HLP-INV-040`  
Ticket notifications are data-minimized.

`HLP-INV-041`  
Ticket Search respects ACL/security restrictions.

`HLP-INV-042`  
Ticket number is not an access credential.

`HLP-INV-043`  
Internal note filtering is server-side.

`HLP-INV-044`  
Ticket access does not escalate access to linked domain entity.

`HLP-INV-045`  
Diagnostic context is purpose-minimized and secret-safe.

`HLP-INV-046`  
Correlation references are preferred over copying full observability logs.

`HLP-INV-047`  
Support Ticket does not implicitly become full incident/problem management system.

`HLP-INV-048`  
Feature Request Ticket is not automatically product backlog item.

`HLP-INV-049`  
External GitHub issue remains separate entity/workflow.

`HLP-INV-050`  
SLA reminders do not mutate professional/business domains.

`HLP-INV-051`  
Attachment state is independent from Ticket state.

`HLP-INV-052`  
Support Module has no generic arbitrary business DB mutation capability.

`HLP-INV-053`  
Resolution code and status remain separate.

`HLP-INV-054`  
Duplicate Ticket linking preserves original history.

`HLP-INV-055`  
Ticket workflow uses optimistic concurrency.

`HLP-INV-056`  
Ticket creation and message posting are idempotent.

`HLP-INV-057`  
Privileged ticket fields are server-derived/authorized.

`HLP-INV-058`  
Ticket Timeline is a projection over typed underlying records.

`HLP-INV-059`  
Dashboard metrics are derived and non-authoritative.

`HLP-INV-060`  
Help/support rich text is sanitized.

`HLP-INV-061`  
Ticket URLs/content are untrusted inputs.

`HLP-INV-062`  
Support Analytics is metadata-only.

`HLP-INV-063`  
Support trends cannot become professional scoring.

`HLP-INV-064`  
Support history and Audit remain separate.

`HLP-INV-065`  
Ticket and attachment retention are independently configurable.

`HLP-INV-066`  
Unnecessary PII is not retained merely because it appeared in support context.

`HLP-INV-067`  
Restore does not replay notifications/replies/SLA side effects.

`HLP-INV-068`  
SLA state is recalculated/reconciled after restore.

`HLP-INV-069`  
Migration does not fabricate ticket lifecycle history.

`HLP-INV-070`  
Operational log entries are not automatically tickets.

`HLP-INV-071`  
Cross-domain migration requires explicit reviewed mapping.

`HLP-INV-072`  
Ticket is not sole security evidence repository.

`HLP-INV-073`  
Support does not replace observability.

`HLP-INV-074`  
All ticket access, internal-note, attachment and workflow rules are server-enforced.

---

# 284. E2E-HLP-001 — Help Article

Admin opens published Russian Help Article.

Content accessible.

---

# 285. E2E-HLP-002 — Draft article

Unpublished Draft article absent from normal Help Center search.

---

# 286. E2E-HLP-003 — Archived article

Archived article not suggested as current solution.

---

# 287. E2E-HLP-004 — Search

Admin searches “QR PDF”.

Relevant Help Articles returned.

No actor Public Search result created.

---

# 288. E2E-HLP-005 — Create ticket

Admin submits technical issue.

One Ticket created with human-readable number.

---

# 289. E2E-HLP-006 — Creation retry

Network response lost.

Same idempotency key returns existing Ticket, not duplicate.

---

# 290. E2E-HLP-007 — Feedback isolation

Casting invitation arriving through Feedback does not appear in Support Ticket queue.

---

# 291. E2E-HLP-008 — Ticket isolation

Media bug Ticket does not create Feedback/Casting/Opportunity.

---

# 292. E2E-HLP-009 — Category

Changing MEDIA → BUG changes support metadata only.

Media Source remains unchanged.

---

# 293. E2E-HLP-010 — Priority

Ticket becomes P1.

Opportunity/Casting priorities unchanged.

---

# 294. E2E-HLP-011 — Status history

OPEN → TRIAGED → IN_PROGRESS.

Append-only history contains both transitions.

---

# 295. E2E-HLP-012 — Resolved

Ticket becomes RESOLVED.

Not CLOSED yet.

---

# 296. E2E-HLP-013 — Close

Resolved Ticket explicitly closed.

---

# 297. E2E-HLP-014 — Reopen

Issue returns.

Ticket reopened with reason/history.

---

# 298. E2E-HLP-015 — Assignment

Ticket assigned to support role.

No Public Contact changes.

---

# 299. E2E-HLP-016 — SLA acknowledgement

Automatic “ticket received” notification does not satisfy first-response timer.

---

# 300. E2E-HLP-017 — Waiting user

Ticket enters WAITING_FOR_USER.

SLA pause follows configured policy only.

---

# 301. E2E-HLP-018 — SLA breach

Derived SLA state becomes BREACHED.

Ticket business status remains unchanged.

---

# 302. E2E-HLP-019 — Public reply

Support adds Public Reply.

Authorized requester can see it.

---

# 303. E2E-HLP-020 — Internal note

Support adds Internal Note.

Requester API/UI does not contain it.

---

# 304. E2E-HLP-021 — Client note tampering

Requester tries to request/include internal notes via query parameter.

Server still excludes them.

---

# 305. E2E-HLP-022 — Reply retry

Same idempotency key does not duplicate Public Reply.

---

# 306. E2E-HLP-023 — BB draft

BB Assistant drafts support response.

No reply is posted until explicit Human action.

---

# 307. E2E-HLP-024 — Attachment upload

Screenshot uploaded.

Attachment remains SCANNING until validation completes.

---

# 308. E2E-HLP-025 — Attachment ready

Safe image becomes READY and is downloadable only by authorized ticket users.

---

# 309. E2E-HLP-026 — Executable

Forbidden executable attachment rejected/quarantined.

---

# 310. E2E-HLP-027 — Attachment public leak

Support attachment absent from actor public Media endpoints.

---

# 311. E2E-HLP-028 — Secret warning

Attachment/text containing probable credential triggers safe warning/redaction flow.

Secret value absent from generic logs.

---

# 312. E2E-HLP-029 — Context link

Ticket references QuestionnaireRevision.

Closing Ticket does not alter Questionnaire.

---

# 313. E2E-HLP-030 — Context ACL

Support user with Ticket access but without restricted source access cannot bypass domain authorization via context link.

---

# 314. E2E-HLP-031 — Source fixed

Underlying media issue fixed.

Ticket remains open until support resolves it.

---

# 315. E2E-HLP-032 — VOP Finding

Human explicitly creates Support Ticket from VOP Finding.

Bidirectional relation preserved.

---

# 316. E2E-HLP-033 — VOP resolution

Ticket resolved.

VOP Finding only resolves after actual VOP re-check confirms condition gone.

---

# 317. E2E-HLP-034 — Notification

Ticket assignment creates Notification Intent.

Support domain does not call provider directly.

---

# 318. E2E-HLP-035 — Notification privacy

External notification contains ticket number/subject/link, not attachment/internal note.

---

# 319. E2E-HLP-036 — Public access

Anonymous actor-site visitor cannot enumerate/read Support Tickets.

---

# 320. E2E-HLP-037 — Ticket number

Knowing `SUP-2026-00124` without authorization grants no access.

---

# 321. E2E-HLP-038 — Security ACL

Restricted Security Ticket hidden from ordinary support user without permission.

---

# 322. E2E-HLP-039 — Help AI

Question answered from approved Help Article.

No product capability invented.

---

# 323. E2E-HLP-040 — Help AI unknown

KB has no answer.

Assistant recommends ticket rather than fabricating instructions.

---

# 324. E2E-HLP-041 — Data correction

Support cannot directly arbitrary-update Profile DB through Help Ticket tool.

---

# 325. E2E-HLP-042 — Duplicate

Ticket B marked duplicate of Ticket A.

B history/messages remain available.

---

# 326. E2E-HLP-043 — Feature request

Ticket category FEATURE_REQUEST created.

No product feature/config automatically enabled.

---

# 327. E2E-HLP-044 — GitHub relation

If converted to external issue, support relation is preserved.

Issue close does not silently close Ticket baseline.

---

# 328. E2E-HLP-045 — Optimistic concurrency

Two agents change Ticket status from same version.

One succeeds; stale one rejected.

---

# 329. E2E-HLP-046 — Rich text sanitation

Ticket body contains script HTML.

Rendered output sanitized.

---

# 330. E2E-HLP-047 — Analytics

Ticket resolved event contributes metadata metric without ticket body.

---

# 331. E2E-HLP-048 — Search privacy

Internal note text does not appear to unauthorized search caller.

---

# 332. E2E-HLP-049 — Attachment retention

Old attachment purged according to policy while retained Ticket history remains usable.

---

# 333. E2E-HLP-050 — Privacy redaction

Unnecessary PII removed/redacted while safe support history is preserved.

---

# 334. E2E-HLP-051 — Backup restore

Closed Ticket remains closed.

No old reply notification resent.

---

# 335. E2E-HLP-052 — SLA restore

SLA state recalculated without duplicate historical breach event.

---

# 336. E2E-HLP-053 — Migration

Legacy Ticket with only final status is imported without fabricated intermediate history.

---

# 337. E2E-HLP-054 — Log migration

Application error log is not automatically transformed into Ticket.

---

# 338. E2E-HLP-055 — Cross-domain migration

Legacy technical inquiry incorrectly stored in Feedback is flagged for explicit reviewed migration, not silently moved.

---

# 339. E2E-HLP-056 — Accessibility

Status/priority/timeline/attachments usable with keyboard and screen reader.

---

# 340. E2E-HLP-057 — Mobile

Admin can create/read/reply/update Ticket from supported mobile Admin UI.

---

# 341. E2E-HLP-058 — AI disabled

Help Articles and full Support Ticket workflow operate without AI.

---

# 342. E2E-HLP-059 — Notification provider down

Ticket creation/status/replies remain functional.

In-App support history persists.

---

# 343. E2E-HLP-060 — Full flow

Admin encounters Questionnaire PDF failure  
→ opens contextual Help Article  
→ creates Support Ticket  
→ attachment scanned  
→ ticket triaged/assigned  
→ Public Reply added  
→ underlying issue fixed  
→ support marks RESOLVED  
→ requester verifies  
→ Ticket CLOSED.

Questionnaire, Feedback, Casting and Opportunity states remain governed by their own domains throughout.

---

# 344. Architecture diagram

```text id="hlp-diagram-001"
                 ADMIN USER
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
      Help Center          Support Ticket
          │                     │
   Help Articles                ├── Status History
          │                     ├── Public Replies
          │                     ├── Internal Notes
          │                     ├── Attachments
          │                     ├── Assignment
          │                     └── SLA
          │
          └────── contextual navigation ──────┘
```

---

# 345. Domain separation diagram

```text id="hlp-diagram-002"
Professional Communication
    Feedback
      ↓
    Casting
      ↓
  Opportunity

           ≠

Technical Support
    HelpTicket

           ≠

Operational Detection
   VOPFinding
```

---

# 346. Ticket lifecycle diagram

```text id="hlp-diagram-003"
OPEN
  ↓
TRIAGED
  ↓
IN_PROGRESS
  ├────────────→ WAITING_FOR_USER ────┐
  ├────────────→ WAITING_FOR_SYSTEM ──┤
  │                                   │
  └───────────────────────────────────┘
  ↓
RESOLVED
  ↓
CLOSED

Explicit:
RESOLVED/CLOSED → REOPEN workflow
```

---

# 347. Reply visibility diagram

```text id="hlp-diagram-004"
Ticket Thread
   │
   ├── PUBLIC_REPLY
   │      └── requester + support
   │
   └── INTERNAL_NOTE
          └── authorized support only

Filtering occurs server-side.
```

---

# 348. Attachment security diagram

```text id="hlp-diagram-005"
Upload
  ↓
Validate size/type
  ↓
Scan
 ┌┴────────────┐
 ▼             ▼
READY       QUARANTINED/REJECTED
 │
 ▼
Private authorized download only
```

---

# 349. VOP integration diagram

```text id="hlp-diagram-006"
VOP Finding
    ↓
Human chooses:
Create Support Ticket
    ↓
HelpTicket

Support resolves issue
    ↓
Domain/system state changes through proper workflow
    ↓
VOP re-check
    ↓
Finding resolved if condition gone
```

---

# 350. SLA diagram

```text id="hlp-diagram-007"
Ticket Priority
      ↓
SLA Policy
      ↓
First Response Due
Resolution Due
      ↓
ON_TRACK / AT_RISK / BREACHED

SLA state does not mutate ticket/business domains.
```

---

# 351. Quality gate

Перед implementation должны быть подтверждены:

- [ ] HelpArticle model;
- [ ] Russian Help Center baseline;
- [ ] Help Article publication lifecycle;
- [ ] Help Search;
- [ ] SupportTicket model;
- [ ] human-readable ticket number;
- [ ] category taxonomy;
- [ ] support priority taxonomy;
- [ ] ticket status machine;
- [ ] append-only status history;
- [ ] reopen workflow;
- [ ] assignment model;
- [ ] SLA policies;
- [ ] first-response semantics;
- [ ] waiting-state SLA policy;
- [ ] derived SLA state;
- [ ] Public Reply;
- [ ] Internal Note;
- [ ] server-side visibility filtering;
- [ ] attachment model;
- [ ] upload validation/scanning;
- [ ] private attachment access;
- [ ] secret-safe diagnostics;
- [ ] source context references;
- [ ] no source ownership transfer;
- [ ] VOP Finding relation;
- [ ] Notifications integration;
- [ ] Help Article suggestions;
- [ ] optional read-only Help AI;
- [ ] Admin/support ACL;
- [ ] restricted Security Tickets;
- [ ] optimistic concurrency;
- [ ] idempotent create/reply;
- [ ] duplicate linking;
- [ ] resolution code;
- [ ] support dashboard;
- [ ] metadata-only analytics;
- [ ] retention/privacy;
- [ ] backup/restore non-replay;
- [ ] migration without fabricated history;
- [ ] strict Feedback/Casting/Opportunity isolation;
- [ ] deterministic E2E coverage.

---

# 352. Acceptance criteria

`AC-HLP-001`  
Help Ticket представляет техническую/эксплуатационную поддержку и не является professional Feedback.

`AC-HLP-002`  
Help Ticket не создаёт Casting/Opportunity автоматически.

`AC-HLP-003`  
Help Center отражает canonical product rules и не создаёт независимую competing business logic.

`AC-HLP-004`  
Help Articles поддерживают Draft/Published/Archived lifecycle.

`AC-HLP-005`  
Help Search остаётся Admin-scoped baseline.

`AC-HLP-006`  
Support Ticket имеет UUID и отдельный human-readable ticket number.

`AC-HLP-007`  
Ticket category используется только для support classification/routing.

`AC-HLP-008`  
Ticket priority не меняет Casting/Opportunity/business priority.

`AC-HLP-009`  
Status transitions валидируются server-side и сохраняются в append-only history.

`AC-HLP-010`  
RESOLVED и CLOSED являются разными состояниями.

`AC-HLP-011`  
Reopen является explicit audited transition.

`AC-HLP-012`  
Assignment остаётся internal support ownership и никогда не становится Public Contact.

`AC-HLP-013`  
SLA targets configurable и не считаются implicit external commitments.

`AC-HLP-014`  
Automatic acknowledgement не считается Human first response.

`AC-HLP-015`  
SLA state derived и не изменяет Ticket status самостоятельно.

`AC-HLP-016`  
Public Reply и Internal Note реализованы как разные typed records.

`AC-HLP-017`  
Internal Note никогда не сериализуется requester/public client.

`AC-HLP-018`  
BB Assistant может draft support response, но не отправляет его автоматически.

`AC-HLP-019`  
Ticket attachments являются private support artifacts и не становятся professional public media.

`AC-HLP-020`  
Attachment проходит validation/security scanning до использования.

`AC-HLP-021`  
Support UI предупреждает/защищает от загрузки секретов.

`AC-HLP-022`  
Ticket может ссылаться на affected domain entity, но не получает его ownership/authorization.

`AC-HLP-023`  
Closing/Resolving Ticket никогда автоматически не изменяет linked Questionnaire/Casting/Opportunity/Media state.

`AC-HLP-024`  
Исправление linked Source не закрывает Ticket автоматически baseline.

`AC-HLP-025`  
Help Article suggestion не препятствует созданию Ticket.

`AC-HLP-026`  
Help AI, если реализован, retrieval-grounded и read-only baseline.

`AC-HLP-027`  
Public actor Contact/Feedback forms не создают Help Tickets.

`AC-HLP-028`  
VOP Finding и Help Ticket остаются отдельными сущностями.

`AC-HLP-029`  
Support notifications проходят через Notifications Module.

`AC-HLP-030`  
External support notifications не содержат Internal Notes/attachments/full diagnostic payload.

`AC-HLP-031`  
Ticket Search и reads применяют server-side ACL.

`AC-HLP-032`  
Ticket number не является access credential.

`AC-HLP-033`  
Ticket access не повышает права на linked source entity.

`AC-HLP-034`  
Automatic diagnostics исключают cookies/tokens/private form payloads.

`AC-HLP-035`  
Correlation ID используется вместо ненужного копирования полного observability log.

`AC-HLP-036`  
Feature Request Ticket не становится product backlog/change автоматически.

`AC-HLP-037`  
External GitHub issue remains separate workflow if integration is added.

`AC-HLP-038`  
SLA reminders/escalations do not mutate professional domains.

`AC-HLP-039`  
Attachment lifecycle remains independent from Ticket status.

`AC-HLP-040`  
Support module не предоставляет generic arbitrary business DB mutation.

`AC-HLP-041`  
Resolution code и Ticket status разделены.

`AC-HLP-042`  
Duplicate Ticket linking сохраняет исходную историю.

`AC-HLP-043`  
Ticket workflow защищён optimistic concurrency.

`AC-HLP-044`  
Ticket creation/reply posting idempotent.

`AC-HLP-045`  
Privileged support fields derived/validated server-side.

`AC-HLP-046`  
Ticket Timeline является derived projection typed records, а не generic mutable blob.

`AC-HLP-047`  
Help content/messages sanitize rich text/URLs.

`AC-HLP-048`  
Support Analytics хранит metadata, а не Ticket bodies/Internal Notes.

`AC-HLP-049`  
Support trends не используются для professional/person scoring.

`AC-HLP-050`  
Audit и Ticket history остаются разными records.

`AC-HLP-051`  
Ticket/Attachment retention policies независимы.

`AC-HLP-052`  
Unnecessary PII может быть redacted/purged without corrupting minimal support history.

`AC-HLP-053`  
Backup restore не replays replies, notifications, ticket reopen or old SLA side effects.

`AC-HLP-054`  
SLA state recalculated/reconciled after restore.

`AC-HLP-055`  
Migration does not fabricate Ticket lifecycle history.

`AC-HLP-056`  
Logs/errors не автоматически становятся Tickets.

`AC-HLP-057`  
Legacy cross-domain reclassification requires explicit migration review.

`AC-HLP-058`  
Help Ticket не является единственным security evidence/observability store.

`AC-HLP-059`  
Module remains fully operational with AI disabled.

`AC-HLP-060`  
Все ACL, visibility, attachment, concurrency, SLA и domain-isolation invariants имеют deterministic E2E coverage.

---

# 353. Финальная доктрина

> **Help Center & Support Tickets Module является изолированным operational support domain. Help Center предоставляет русскоязычную справочную базу, основанную на canonical product documentation, а Support Ticket фиксирует техническую проблему, запрос поддержки, bug или feature request. Ticket никогда не используется вместо professional Feedback, Casting или Opportunity и не получает права менять linked business Source. Ticket lifecycle, assignment, priority и SLA принадлежат только support workflow; Public Replies и Internal Notes являются разными типами данных с server-side visibility filtering. Attachments private-by-default, проходят validation/scanning и не становятся professional Media. VOP может предложить создать Ticket, BB Assistant может помочь написать Draft ответа, Notifications могут доставить внимание, но ни один из этих слоёв не нарушает Support/domain boundaries. Closing Ticket не закрывает Casting/Opportunity, исправление Source не закрывает Ticket автоматически, а Support Module не предоставляет generic superuser/SQL mutation path в бизнес-домены.**