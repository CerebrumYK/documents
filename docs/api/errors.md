# API ERRORS

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная taxonomy ошибок, transport mapping, retryability и безопасная диагностика

**Целевой файл:** `docs/api/errors.md`  
**Документ:** DOC-102  
**Статус:** ✅ Completed  
**Тип:** API / Errors / Validation / Transport / Diagnostics / Reliability

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/business-rules.md`
- `docs/product/product-principles.md`
- `docs/domain/domain-model.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/background-jobs.md`
- `docs/architecture/notifications.md`
- `docs/database/validation-and-constraints.md`
- `docs/database/visibility-and-access.md`
- `docs/api/server-actions.md`
- `docs/api/contracts.md`

---

# 1. Назначение

Настоящий документ определяет единую модель ошибок платформы.

Он фиксирует:

1. error taxonomy;
2. stable error codes;
3. field errors;
4. validation errors;
5. authentication;
6. authorization;
7. not-found semantics;
8. conflicts;
9. optimistic concurrency;
10. idempotency;
11. dependency failures;
12. provider failures;
13. async processing failures;
14. rate limiting;
15. retryability;
16. Server Action mapping;
17. HTTP mapping;
18. Worker failures;
19. user-facing messages;
20. Admin diagnostics;
21. privacy;
22. correlation IDs;
23. observability;
24. unknown errors;
25. testing.

---

# 2. Главная доктрина

> **Ошибка должна быть стабильным семантическим результатом операции, а не утечкой исключения конкретной библиотеки, ORM, базы данных или внешнего provider.**

Canonical:

```text
LOW-LEVEL FAILURE
      ↓
ERROR CLASSIFICATION
      ↓
APPLICATION ERROR
      ↓
SAFE CONTRACT
      ↓
TRANSPORT MAPPING
      ↓
USER / CLIENT
```

---

# 3. Error identifiers

Используются:

```text
ERR-*
ERR-VAL-*
ERR-AUTH-*
ERR-CONFLICT-*
ERR-IDEMP-*
ERR-DEP-*
ERR-PROV-*
ERR-PROC-*
ERR-RATE-*
ERR-INT-*
ERR-INV-*
ERR-AP-*
```

---

# 4. ERR-INV-001 — Error Code Is Stable

UI, API clients и tests SHOULD depend on:

```text
error.code
```

а не на текст сообщения.

---

# 5. ERR-INV-002 — Human Message Is Not Business Logic

Localized message may change.

Code semantics MUST remain stable.

---

# 6. ERR-INV-003 — Low-Level Exception Does Not Escape

Запрещено возвращать:

```text
SQLSTATE
constraint_name
filesystem path
stack trace
provider raw response
internal class name
```

обычному пользователю.

---

# 7. ERR-INV-004 — Errors Are Context-Safe

Public response may intentionally reveal less diagnostic information than Admin response.

---

# 8. ERR-INV-005 — Error Does Not Reveal Hidden Resource

A request for unauthorized/private entity MUST NOT unintentionally prove its existence.

---

# 9. ERR-INV-006 — Error Class != HTTP Status

Application error remains semantic.

HTTP status is transport mapping only.

---

# 10. ERR-INV-007 — Provider Error != Domain Error

Example:

```text
WhatsApp 503
```

becomes:

```text
NOTIFICATION_PROVIDER_UNAVAILABLE
```

not a raw provider error leaking upstream.

---

# 11. ERR-INV-008 — Failure of Derived Side Effect Does Not Rewrite Source Success

Example:

```text
Feedback commit = success
Notification delivery = failed
```

must remain two distinct outcomes.

---

# 12. Canonical top-level categories

```text
VALIDATION
AUTHENTICATION
AUTHORIZATION
NOT_FOUND
CONFLICT
STALE_VERSION
IDEMPOTENCY
RATE_LIMIT
DEPENDENCY
PROCESSING
PROVIDER
UNAVAILABLE
INTERNAL
```

---

# 13. Standard error contract

Canonical:

```text
ApiErrorDTO {
  code: string
  category: ErrorCategory
  message?: string
  fieldErrors?: FieldErrorDTO[]
  retryable: boolean
  correlationId?: string
  details?: SafeErrorDetails
}
```

---

# 14. Minimal Public error

May be:

```json
{
  "code": "VALIDATION_REQUIRED",
  "category": "VALIDATION",
  "retryable": false
}
```

---

# 15. Admin error

May include safe remediation data:

```json
{
  "code": "QUESTIONNAIRE_MISSING_FULL_BODY",
  "category": "VALIDATION",
  "retryable": false,
  "details": {
    "section": "portfolio",
    "requiredCategory": "full_body"
  },
  "correlationId": "..."
}
```

---

# 16. `message`

Optional localized human-readable text.

Should not be the only usable error identifier.

---

# 17. `details`

Must use explicit safe schema per error family.

Never arbitrary raw exception object.

---

# 18. Field error contract

Canonical:

```text
FieldErrorDTO {
  path: string
  code: string
  message?: string
  parameters?: object
}
```

---

# 19. Field path

One canonical project-wide format:

```text
field
nested.field
sections[2].items[0]
```

---

# 20. ERR-VAL-001 — Required

```text
VALIDATION_REQUIRED
```

---

# 21. ERR-VAL-002 — Format

```text
VALIDATION_FORMAT
```

Examples:

```text
email
phone
URL
UUID
locale
date
```

---

# 22. ERR-VAL-003 — Range

```text
VALIDATION_RANGE
```

---

# 23. ERR-VAL-004 — Unsupported value

```text
VALIDATION_UNSUPPORTED_VALUE
```

---

# 24. ERR-VAL-005 — Invalid relation

```text
VALIDATION_INVALID_RELATION
```

---

# 25. ERR-VAL-006 — Wrong profile

Internal/Admin diagnostic:

```text
VALIDATION_WRONG_PROFILE
```

Public surfaces SHOULD usually map to safer context-appropriate error.

---

# 26. ERR-VAL-007 — Not eligible

```text
VALIDATION_NOT_ELIGIBLE
```

---

# 27. ERR-VAL-008 — Not ready

```text
VALIDATION_NOT_READY
```

---

# 28. Readiness-specific codes

Prefer granular stable codes where remediation matters.

Examples:

```text
QUESTIONNAIRE_MISSING_CLOSE_UP
QUESTIONNAIRE_MISSING_FULL_BODY
QUESTIONNAIRE_INVALID_QR_TARGET
QUESTIONNAIRE_INVALID_CONTACT
GRID_CELL_UNCONFIRMED
GRID_CELL_COUNT_MISMATCH
BUILDER_SELECTION_REVOKED
THEME_ACCESSIBILITY_BLOCKED
```

---

# 29. ERR-VAL-009 — State transition

```text
VALIDATION_STATE_TRANSITION
```

---

# 30. Domain-specific transition codes

Examples:

```text
OPPORTUNITY_TRANSITION_NOT_ALLOWED
QUESTIONNAIRE_ALREADY_ARCHIVED
CASTING_ALREADY_CLOSED
THEME_REVISION_NOT_ACTIVATABLE
```

---

# 31. Authentication errors

Canonical category:

```text
AUTHENTICATION
```

---

# 32. Missing authentication

```text
AUTHENTICATION_REQUIRED
```

---

# 33. Invalid session

```text
AUTHENTICATION_INVALID_SESSION
```

---

# 34. Expired session

```text
AUTHENTICATION_SESSION_EXPIRED
```

---

# 35. Reauthentication

Provider/social OAuth connection issue is not necessarily Admin login auth error.

Keep separate provider/integration code.

---

# 36. Authorization errors

Canonical:

```text
AUTHORIZATION_DENIED
```

---

# 37. Resource-specific access

May use:

```text
AUTHORIZATION_RESOURCE_DENIED
```

internally.

---

# 38. Public resource privacy

When existence must not be revealed:

map to safe:

```text
RESOURCE_NOT_FOUND
```

or equivalent policy response.

---

# 39. Token access errors

Examples:

```text
ACCESS_TOKEN_INVALID
ACCESS_TOKEN_EXPIRED
ACCESS_TOKEN_REVOKED
ACCESS_SCOPE_DENIED
```

---

# 40. Public safety

Do not reveal token internals or hash state.

---

# 41. Not Found category

Canonical:

```text
RESOURCE_NOT_FOUND
```

---

# 42. Entity-specific variants

Useful internally/UI:

```text
PROJECT_NOT_FOUND
MEDIA_ASSET_NOT_FOUND
QUESTIONNAIRE_NOT_FOUND
CASTING_NOT_FOUND
```

only where exposing resource type is safe.

---

# 43. Historical Revision

```text
QUESTIONNAIRE_REVISION_NOT_FOUND
```

Admin/history context.

---

# 44. Conflict category

Used when operation cannot proceed because of current state, uniqueness or competing intent.

---

# 45. Generic conflict

```text
RESOURCE_CONFLICT
```

---

# 46. Primary conflict

```text
PORTFOLIO_PRIMARY_CONFLICT
```

---

# 47. Duplicate relation

```text
RELATION_ALREADY_EXISTS
```

---

# 48. Duplicate business creation

Can become idempotency success when same semantic request.

Do not always report conflict.

---

# 49. Stale version

Canonical:

```text
STALE_VERSION
```

---

# 50. Stale details

Admin-safe response MAY include:

```text
expectedVersion
currentVersion
```

if this does not expose sensitive state.

---

# 51. Public Builder stale session

Use:

```text
BUILDER_SESSION_STALE
```

with refresh/reload guidance.

---

# 52. AI stale target

Examples:

```text
AI_DRAFT_TARGET_STALE
CASTING_ANALYSIS_STALE
THEME_PROPOSAL_STALE
```

---

# 53. ERR-INV-009 — Stale Is Not Generic Internal Failure

Must be recoverable UX state.

---

# 54. Idempotency category

Canonical:

```text
IDEMPOTENCY_CONFLICT
```

when same idempotency key is reused with materially different request.

---

# 55. Same request replay

Should generally return original semantic success/result.

No error.

---

# 56. Idempotency record in progress

Possible:

```text
IDEMPOTENCY_IN_PROGRESS
```

---

# 57. Retry behavior

May tell caller to retry after short interval if same request is processing.

---

# 58. Idempotency expired

Usually new request can proceed according to operation policy.

Not necessarily an error.

---

# 59. Dependency category

Used when target operation is blocked by another resource/state.

---

# 60. Example Media deletion

```text
MEDIA_DELETE_BLOCKED_BY_DEPENDENCIES
```

---

# 61. Questionnaire publish dependency

```text
QUESTIONNAIRE_DEPENDENCY_NOT_READY
```

---

# 62. Missing derivative

```text
MEDIA_DERIVATIVE_NOT_READY
```

---

# 63. Token artifact unavailable

```text
ARTIFACT_NOT_READY
```

or access-specific error.

---

# 64. Dependency details

Admin may receive safe list:

```text
dependencyType
entityId
label
blockingReason
```

---

# 65. Public dependency error

Should be abstracted.

Example Builder:

```text
Выбранный материал больше недоступен.
```

without private dependency graph.

---

# 66. Rate Limit category

Canonical:

```text
RATE_LIMITED
```

---

# 67. Specialized

Examples:

```text
FEEDBACK_RATE_LIMITED
BUILDER_RATE_LIMITED
AI_RATE_LIMITED
LOGIN_RATE_LIMITED
```

---

# 68. Retry metadata

HTTP may include:

```text
Retry-After
```

where meaningful.

---

# 69. Error DTO

May include:

```text
retryAfterSeconds
```

if contract chooses.

---

# 70. Public threshold secrecy

Do not need to reveal exact anti-abuse algorithm.

---

# 71. Processing errors

Used for internal derived generation.

Examples:

```text
PDF_GENERATION_FAILED
QR_GENERATION_FAILED
QR_VALIDATION_FAILED
MEDIA_PROCESSING_FAILED
GRID_RENDER_FAILED
SEARCH_INDEX_FAILED
```

---

# 72. Processing retryability

Depends on failure cause.

Example:

```text
PDF_RENDERER_TEMPORARILY_UNAVAILABLE
```

retryable.

```text
PDF_DOCUMENT_MODEL_INVALID
```

not retryable until data/code changes.

---

# 73. Provider category

Examples:

```text
AI_PROVIDER_UNAVAILABLE
AI_PROVIDER_TIMEOUT
AI_PROVIDER_RATE_LIMITED
NOTIFICATION_PROVIDER_UNAVAILABLE
EMAIL_PROVIDER_UNAVAILABLE
SOCIAL_PROVIDER_UNAVAILABLE
SOCIAL_ACCOUNT_REAUTH_REQUIRED
```

---

# 74. Provider auth/config

Examples:

```text
PROVIDER_CREDENTIAL_INVALID
PROVIDER_CONFIGURATION_INVALID
PROVIDER_TEMPLATE_INVALID
```

Usually not retryable automatically until configuration changes.

---

# 75. Provider unknown outcome

Special category/code:

```text
PROVIDER_OUTCOME_UNKNOWN
```

---

# 76. Why separate

Blind retry may duplicate external side effect.

---

# 77. Correct handling

Requires:

```text
reconciliation
provider lookup
idempotency
```

before resend.

---

# 78. Unavailable category

Internal service dependency unavailable:

```text
SERVICE_UNAVAILABLE
DATABASE_UNAVAILABLE
STORAGE_UNAVAILABLE
```

---

# 79. Database unavailable

Writes cannot proceed.

Do not convert to validation error.

---

# 80. Cache unavailable

Normally **not** user-facing error if fallback works.

---

# 81. Search unavailable

Public/Admin search may return:

```text
SEARCH_UNAVAILABLE
```

while main site remains functional.

---

# 82. AI unavailable

Manual product functions remain available.

---

# 83. Notification provider unavailable

Feedback submission still succeeds.

---

# 84. Internal errors

Canonical:

```text
INTERNAL_ERROR
```

---

# 85. Unexpected exception

Public/ordinary UI receives safe generic error:

```text
INTERNAL_ERROR
correlationId
```

---

# 86. Internal log retains

```text
exception class
stack
request/correlation
safe metadata
```

subject to privacy policy.

---

# 87. ERR-INV-010 — Correlation ID Is Safe to Expose

Correlation ID may be shown to user for support.

It MUST NOT itself encode secret or private data.

---

# 88. Correlation ID generation

Server-generated or trusted propagated value.

Do not blindly trust arbitrary user header as canonical correlation ID.

---

# 89. Error severity

Operational classification MAY use:

```text
INFO
WARNING
ERROR
CRITICAL
```

This is observability severity, not API category.

---

# 90. Retryability contract

Each application error SHOULD classify:

```text
retryable = true | false
```

where meaningful.

---

# 91. Retryable examples

```text
SERVICE_UNAVAILABLE
AI_PROVIDER_TIMEOUT
NOTIFICATION_PROVIDER_UNAVAILABLE
DATABASE_TRANSIENT_FAILURE
RATE_LIMITED
```

---

# 92. Non-retryable examples

```text
VALIDATION_REQUIRED
AUTHORIZATION_DENIED
QUESTIONNAIRE_MISSING_FULL_BODY
IDEMPOTENCY_CONFLICT
INVALID_QR_TARGET
```

---

# 93. Conditional retry

Some errors require state change first.

Example:

```text
STALE_VERSION
```

Not blind retryable.

User/client should reload then retry consciously.

---

# 94. Retry strategy types

Recommended distinction:

```text
NONE
IMMEDIATE_SAFE
BACKOFF
RELOAD_THEN_RETRY
REAUTHENTICATE
RECONCILE
FIX_CONFIGURATION
```

Could be internal metadata rather than external contract.

---

# 95. Server Action error mapping

Server Actions SHOULD return structured result.

Example:

```text
{
  ok: false,
  error: {
    code: "STALE_VERSION",
    category: "STALE_VERSION",
    retryable: false
  }
}
```

---

# 96. Server Action should not throw expected errors

Expected business errors should be returned/mapped.

---

# 97. Unexpected exception

May throw internally but outer adapter maps to safe result.

---

# 98. HTTP mapping

Recommended baseline:

| Category | HTTP |
|---|---:|
| VALIDATION | 400 / 422 |
| AUTHENTICATION | 401 |
| AUTHORIZATION | 403 |
| NOT_FOUND | 404 |
| CONFLICT | 409 |
| STALE_VERSION | 409 |
| IDEMPOTENCY | 409 |
| RATE_LIMIT | 429 |
| DEPENDENCY | 409 / 424 where justified |
| PROCESSING | 422 / 500 / 503 depending cause |
| PROVIDER | 502 / 503 |
| UNAVAILABLE | 503 |
| INTERNAL | 500 |

---

# 99. Validation status choice

Recommended:

```text
422
```

for structurally valid HTTP request with semantic validation failure.

`400` remains acceptable for malformed transport input.

---

# 100. Malformed JSON

```text
400 Bad Request
```

---

# 101. Missing required semantic field

Could be:

```text
422
```

after successful request parsing.

---

# 102. Unauthorized hidden entity

Policy may deliberately map:

```text
403 → 404
```

for privacy.

---

# 103. ERR-INV-011 — HTTP Mapping Must Be Consistent Per Endpoint Family

Do not arbitrarily return:

```text
200 { success:false }
```

for ordinary Route Handler errors.

Server Actions may use structured `ok` because HTTP status semantics are less directly consumed by the caller.

---

# 104. Accepted async operation

If durable async work is accepted:

```text
202 Accepted
```

is appropriate for Route Handler.

---

# 105. Example

```text
POST /api/questionnaires/{id}/generate
→ 202
```

with Artifact/operation reference.

---

# 106. Already completed idempotent async request

May return:

```text
200
```

with existing resource.

---

# 107. File upload errors

Examples:

```text
FILE_TOO_LARGE
FILE_TYPE_UNSUPPORTED
FILE_CONTENT_INVALID
FILE_SECURITY_REJECTED
UPLOAD_STORAGE_FAILED
```

---

# 108. Security quarantine

Use:

```text
FILE_QUARANTINED
```

Admin-safe context.

Public may get generic rejection.

---

# 109. Media errors

Examples:

```text
MEDIA_NOT_READY
MEDIA_PROCESSING_FAILED
MEDIA_SOURCE_MISSING
MEDIA_DELETE_BLOCKED_BY_DEPENDENCIES
MEDIA_USAGE_CONFLICT
```

---

# 110. Portfolio errors

Examples:

```text
PORTFOLIO_CATEGORY_INVALID
PORTFOLIO_PRIMARY_CONFLICT
PORTFOLIO_PRIMARY_WRONG_CATEGORY
ROLE_MEDIA_LIMIT_EXCEEDED
ROLE_HOME_SHOWCASE_LIMIT_EXCEEDED
```

---

# 111. Emotional errors

Examples:

```text
EMOTIONAL_SHOOTING_DATE_REQUIRED
GRID_DIMENSIONS_UNSUPPORTED
GRID_CELL_COUNT_MISMATCH
GRID_POSITION_DUPLICATE
GRID_MEDIA_WRONG_SESSION
GRID_CELL_UNCONFIRMED
GRID_FACE_TARGET_NOT_MET
GRID_RENDER_FAILED
```

---

# 112. Face target diagnostic

Must remain technical/readiness-oriented.

No appearance judgment language.

---

# 113. Questionnaire errors

Examples:

```text
QUESTIONNAIRE_MISSING_IDENTIFICATION
QUESTIONNAIRE_MISSING_CLOSE_UP
QUESTIONNAIRE_MISSING_FULL_BODY
QUESTIONNAIRE_ITEM_NOT_ELIGIBLE
QUESTIONNAIRE_CONTACT_NOT_ELIGIBLE
QUESTIONNAIRE_LINK_INVALID
QUESTIONNAIRE_QR_INVALID
QUESTIONNAIRE_NOT_READY
QUESTIONNAIRE_PUBLISH_CONFLICT
```

---

# 114. PDF errors

Examples:

```text
PDF_GENERATION_FAILED
PDF_VALIDATION_FAILED
PDF_RENDERER_UNAVAILABLE
PDF_SOURCE_REVISION_UNSUPPORTED
```

---

# 115. Builder errors

Examples:

```text
BUILDER_SESSION_NOT_FOUND
BUILDER_SESSION_EXPIRED
BUILDER_SESSION_STALE
BUILDER_TEMPLATE_DISABLED
BUILDER_ITEM_NOT_ELIGIBLE
BUILDER_SELECTION_REVOKED
BUILDER_MISSING_CLOSE_UP
BUILDER_MISSING_FULL_BODY
BUILDER_GENERATION_FAILED
```

---

# 116. Public Builder privacy

Do not return:

```text
Project exists but allow_in_public_questionnaire_builder=false
```

Use safe:

```text
BUILDER_ITEM_NOT_ELIGIBLE
```

---

# 117. QR errors

Examples:

```text
QR_TARGET_INVALID
QR_TARGET_NOT_PUBLIC_SAFE
QR_TARGET_PRIVATE
QR_GENERATION_FAILED
QR_DECODE_FAILED
QR_DECODE_MISMATCH
```

---

# 118. Link errors

```text
LINK_URL_INVALID
LINK_SCHEME_UNSUPPORTED
LINK_UNRESOLVABLE
LINK_HEALTH_CHECK_FAILED
```

Structural invalid vs network health failure must remain distinct.

---

# 119. Feedback errors

Examples:

```text
FEEDBACK_TYPE_INVALID
FEEDBACK_MESSAGE_REQUIRED
FEEDBACK_ATTACHMENT_REJECTED
FEEDBACK_RATE_LIMITED
FEEDBACK_DUPLICATE_CONFLICT
```

---

# 120. Notification failure after Feedback

Not returned as Feedback failure if Feedback already committed.

---

# 121. Casting errors

Examples:

```text
CASTING_SOURCE_REQUIRED
CASTING_SOURCE_INVALID
CASTING_ANALYSIS_SCHEMA_INVALID
CASTING_ANALYSIS_STALE
CASTING_REQUIREMENT_NOT_FOUND
CASTING_REQUIREMENT_ALREADY_DECIDED
CASTING_REQUIREMENT_INVALID_VALUE
CASTING_MATCH_NO_CONFIRMED_DATA
```

---

# 122. `CASTING_MATCH_NO_CONFIRMED_DATA`

Usually a match state rather than exceptional error.

Do not throw if valid business result.

---

# 123. ERR-INV-012 — Valid Negative Business Outcome Is Not Error

Examples:

```text
NO_CONFIRMED_DATA
MISMATCH
NOT_SPECIFIED
```

are normal result states.

---

# 124. Opportunity errors

Examples:

```text
OPPORTUNITY_TRANSITION_NOT_ALLOWED
OPPORTUNITY_TERMINAL
OPPORTUNITY_REOPEN_REQUIRED
OPPORTUNITY_BOOKED_REQUIRES_HUMAN
OPPORTUNITY_PROJECT_ALREADY_CREATED
```

---

# 125. BB Assistant errors

Examples:

```text
BB_CONTEXT_INVALID
BB_SOURCE_UNAVAILABLE
BB_DRAFT_STALE
BB_TARGET_STALE
BB_APPLY_NOT_ALLOWED
BB_PROVIDER_UNAVAILABLE
```

---

# 126. Theme errors

Examples:

```text
THEME_SCHEMA_INVALID
THEME_TOKEN_UNSUPPORTED
THEME_LOCK_VIOLATION
THEME_ACCESSIBILITY_BLOCKED
THEME_REVISION_STALE
THEME_ACTIVATION_CONFLICT
THEME_AI_OUTPUT_INVALID
```

---

# 127. Social errors

Examples:

```text
SOCIAL_ACCOUNT_NOT_CONNECTED
SOCIAL_ACCOUNT_REAUTH_REQUIRED
SOCIAL_POST_NOT_APPROVED
SOCIAL_APPROVED_VERSION_STALE
SOCIAL_TARGET_ALREADY_PUBLISHED
SOCIAL_PROVIDER_UNAVAILABLE
SOCIAL_PUBLISH_OUTCOME_UNKNOWN
```

---

# 128. Notification errors

Examples:

```text
NOTIFICATION_RECIPIENT_INVALID
NOTIFICATION_CHANNEL_DISABLED
NOTIFICATION_TEMPLATE_MISSING
NOTIFICATION_PROVIDER_UNAVAILABLE
NOTIFICATION_PROVIDER_AUTH_FAILED
NOTIFICATION_DELIVERY_EXPIRED
NOTIFICATION_OUTCOME_UNKNOWN
```

---

# 129. Search errors

Examples:

```text
SEARCH_QUERY_INVALID
SEARCH_QUERY_TOO_LONG
SEARCH_SCOPE_INVALID
SEARCH_UNAVAILABLE
```

Public caller must never receive `SEARCH_SCOPE_INVALID` due to attempted Admin scope if endpoint does not even accept scope.

---

# 130. Retention/deletion errors

Examples:

```text
DELETE_NOT_ALLOWED
DELETE_DEPENDENCY_BLOCKED
RETENTION_HOLD_ACTIVE
PURGE_ALREADY_IN_PROGRESS
PURGE_FAILED
REDACTION_SCOPE_INVALID
```

---

# 131. Archive is not delete failure

If user requests ordinary removal and Archive is correct operation, API should expose Archive command rather than return “delete blocked” routinely.

---

# 132. Database error translation

Examples:

```text
unique violation
foreign key violation
serialization/deadlock
connection failure
```

must be classified.

---

# 133. Unique violation

Map known constraint semantic to:

```text
PORTFOLIO_PRIMARY_CONFLICT
REVISION_NUMBER_CONFLICT
RELATION_ALREADY_EXISTS
```

---

# 134. Unknown unique violation

```text
RESOURCE_CONFLICT
```

and log internal constraint name.

---

# 135. FK violation

Known semantic:

```text
RESOURCE_DEPENDENCY_CONFLICT
```

or specific code.

---

# 136. Deadlock / serialization failure

May be retryable internal transient error.

---

# 137. Do not show SQLSTATE

Normative.

---

# 138. Storage error translation

Examples:

```text
STORAGE_UNAVAILABLE
STORAGE_WRITE_FAILED
STORAGE_READ_FAILED
STORAGE_DELETE_FAILED
```

---

# 139. Source missing

Separate:

```text
MEDIA_SOURCE_MISSING
```

from infrastructure unavailable.

---

# 140. AI provider error translation

Provider-specific:

```text
429
5xx
timeout
schema mismatch
content policy refusal
```

maps to normalized capability errors.

---

# 141. AI refused/unsupported request

Use capability-safe error:

```text
AI_REQUEST_NOT_SUPPORTED
```

where appropriate.

Do not expose provider policy internals unnecessarily.

---

# 142. AI invalid structured output

```text
AI_OUTPUT_SCHEMA_INVALID
```

and retry according to capability policy.

---

# 143. Provider timeout

```text
AI_PROVIDER_TIMEOUT
```

retryable.

---

# 144. Provider auth

```text
AI_PROVIDER_CONFIGURATION_INVALID
```

not blind retryable.

---

# 145. External notification provider

Same adapter translation doctrine.

---

# 146. Webhook errors

Incoming provider request errors:

```text
WEBHOOK_SIGNATURE_INVALID
WEBHOOK_PAYLOAD_INVALID
WEBHOOK_EVENT_UNSUPPORTED
WEBHOOK_REPLAY
```

---

# 147. Replay

If same valid event already processed, preferred response may be transport success/idempotent acknowledgment rather than error.

---

# 148. ERR-INV-013 — Idempotent Replay Is Often Success

Avoid making provider retry endlessly because duplicate event was already handled.

---

# 149. OAuth callback errors

Examples:

```text
OAUTH_STATE_INVALID
OAUTH_CODE_MISSING
OAUTH_EXCHANGE_FAILED
OAUTH_ACCOUNT_MISMATCH
```

---

# 150. OAuth error page

Safe user-facing message.

No raw code/token.

---

# 151. Public vs Admin diagnostic layers

Canonical:

```text
Internal Exception
     ↓
Application Error
     ↓
┌───────────────┬───────────────┐
▼               ▼
Public-safe     Admin-safe
message         diagnostics
```

---

# 152. Public-safe diagnostics

May contain:

```text
code
generic message
retryable
correlationId
field errors
```

---

# 153. Admin-safe diagnostics

May additionally contain:

```text
affected entity
dependency type
processing stage
provider class
retry recommendation
```

---

# 154. Still forbidden in Admin error

```text
API token
password
session secret
full DB connection string
raw private external payload unless dedicated secured diagnostic flow
```

---

# 155. Logs

Structured log event SHOULD capture:

```text
error_code
category
operation
correlation_id
request_id
entity_type?
entity_id?
retryable
duration
```

---

# 156. Internal exception

May also capture stack trace.

---

# 157. PII minimization

Do not log:

```text
Feedback full message
Contact value
Casting full source
AIDraft content
```

by default.

---

# 158. Error fingerprinting

Monitoring MAY group by:

```text
error code
exception class
safe stack fingerprint
handler
```

not user payload.

---

# 159. Observability counters

Recommended:

```text
errors_total{code}
validation_failures_total{code}
conflicts_total
stale_version_total
provider_failures_total{provider_class}
rate_limit_total{surface}
internal_errors_total
```

---

# 160. Alerting

Do not alert on every validation failure.

---

# 161. Good alert candidates

```text
high INTERNAL_ERROR rate
database unavailable
storage failures
provider auth failures
queue final failures
PDF failure spike
redaction/purge failure
```

---

# 162. Expected errors

Examples:

```text
VALIDATION_REQUIRED
STALE_VERSION
AUTHENTICATION_REQUIRED
```

should not automatically be treated as system incidents.

---

# 163. Retry UX

Error DTO may drive UI actions.

---

# 164. Examples

```text
STALE_VERSION
→ Reload current data
```

```text
SOCIAL_ACCOUNT_REAUTH_REQUIRED
→ Reconnect account
```

```text
AI_PROVIDER_UNAVAILABLE
→ Retry later / continue manually
```

---

# 165. No automatic retry on all errors

Normative.

---

# 166. Blind retry forbidden for

```text
IDEMPOTENCY_CONFLICT
PROVIDER_OUTCOME_UNKNOWN
AUTHORIZATION_DENIED
VALIDATION_NOT_ELIGIBLE
```

---

# 167. Retry budgets

Worker retry policies are defined per job/provider, not by client error alone.

---

# 168. Async failure state

For asynchronous operation, failure becomes persistent resource state.

Example:

```text
QuestionnaireArtifact.state = failed
```

with safe error code.

---

# 169. Querying failed async resource

Returns:

```text
state
errorCode
retryable?
```

appropriate to context.

---

# 170. Do not disappear failed async resource

Otherwise Admin cannot diagnose/retry.

---

# 171. Previous successful artifact preservation

If regeneration fails:

```text
new attempt = failed
old published artifact = still valid
```

unless source/access policy changed.

---

# 172. Error code namespace

Recommended prefixes by bounded context.

Examples:

```text
PROFILE_*
MEDIA_*
PORTFOLIO_*
GRID_*
QUESTIONNAIRE_*
BUILDER_*
CASTING_*
OPPORTUNITY_*
BB_*
THEME_*
NOTIFICATION_*
SOCIAL_*
SEARCH_*
```

---

# 173. Generic codes

Retained for cross-cutting errors:

```text
VALIDATION_REQUIRED
AUTHENTICATION_REQUIRED
AUTHORIZATION_DENIED
RESOURCE_NOT_FOUND
STALE_VERSION
RATE_LIMITED
INTERNAL_ERROR
```

---

# 174. Error code naming rules

Use:

```text
UPPER_SNAKE_CASE
```

---

# 175. Error code semantic stability

Do not rename code because UI wording changed.

---

# 176. Removing a code

Requires compatibility review if persisted/consumed externally.

---

# 177. Persisted error codes

Background Jobs, artifacts, delivery attempts MAY store stable error codes.

---

# 178. Provider raw error

Store separately/sanitized if required for diagnostics.

Application-facing code remains normalized.

---

# 179. Error contract versioning

Normal additive evolution:

```text
new optional details
new error code
```

is safe if clients handle unknown values.

---

# 180. Client fallback

Unknown code:

```text
generic safe message
correlationId
```

without crash.

---

# 181. Field error unknown path

UI can display form summary fallback.

---

# 182. Internationalization

Error codes are language-neutral.

---

# 183. Message localization

Recommended lookup:

```text
error.code
+
parameters
```

---

# 184. Server vs client localization

Either may be used consistently.

Preferred for same-app UI:

client/server shared message catalog.

---

# 185. External API

May return English/default message while code remains authoritative.

---

# 186. ERR-INV-014 — Do Not Store Localized Message as Only Persistent Failure State

Store:

```text
error_code
```

and safe parameters.

---

# 187. Validation aggregation

Forms MAY return multiple field validation errors in one response.

---

# 188. Transaction/domain conflict

Usually one principal semantic error is sufficient.

---

# 189. Questionnaire readiness

Can return multiple blockers:

```text
QUESTIONNAIRE_MISSING_CLOSE_UP
QUESTIONNAIRE_MISSING_FULL_BODY
QUESTIONNAIRE_INVALID_QR_TARGET
```

in readiness DTO.

---

# 190. Publish command failure

May return readiness error collection if operation is blocked by several conditions.

---

# 191. Security validation

Do not distinguish too precisely for attacker-controlled Public requests.

---

# 192. Example login

Avoid confirming:

```text
email exists but password wrong
```

if auth design uses generic credential error.

---

# 193. Token access

Do not expose:

```text
token hash matched but resource ID wrong
```

---

# 194. Upload rejection

Public/Admin message may state unsupported file type without exposing malware-engine internals.

---

# 195. Dependency error example

Admin Media purge:

```json
{
  "code": "MEDIA_DELETE_BLOCKED_BY_DEPENDENCIES",
  "category": "DEPENDENCY",
  "retryable": false,
  "details": {
    "dependencies": [
      {
        "type": "questionnaire_revision",
        "id": "...",
        "label": "Анкета — Revision 4"
      }
    ]
  }
}
```

---

# 196. Public equivalent

Would not expose internal dependency list.

---

# 197. Stale version example

```json
{
  "code": "STALE_VERSION",
  "category": "STALE_VERSION",
  "retryable": false,
  "details": {
    "expectedVersion": 7,
    "currentVersion": 8
  }
}
```

Admin context only.

---

# 198. Idempotency conflict example

```json
{
  "code": "IDEMPOTENCY_CONFLICT",
  "category": "IDEMPOTENCY",
  "retryable": false
}
```

---

# 199. Provider timeout example

```json
{
  "code": "AI_PROVIDER_TIMEOUT",
  "category": "PROVIDER",
  "retryable": true,
  "correlationId": "..."
}
```

---

# 200. Internal error example

```json
{
  "code": "INTERNAL_ERROR",
  "category": "INTERNAL",
  "retryable": true,
  "correlationId": "..."
}
```

No stack.

---

# 201. Server Action pattern

Conceptually:

```text
try:
  result = execute command
  return { ok: true, data: result }

catch ApplicationError:
  return { ok: false, error: mapSafe(error) }

catch unknown:
  correlation = recordInternalFailure()
  return {
    ok: false,
    error: {
      code: INTERNAL_ERROR,
      category: INTERNAL,
      retryable: true,
      correlationId
    }
  }
```

---

# 202. Route Handler pattern

```text
Application Error
  ↓
HTTP mapper
  ↓
status + ApiErrorDTO
```

---

# 203. Worker error pattern

```text
handler exception
  ↓
classification
  ├─ retryable
  ├─ permanent
  └─ unknown external outcome
  ↓
persist attempt
  ↓
retry / final failure / reconcile
```

---

# 204. Worker retry classification

Should store normalized failure code.

---

# 205. Poison job

Invalid payload/schema:

```text
JOB_PAYLOAD_INVALID
JOB_PAYLOAD_VERSION_UNSUPPORTED
JOB_TYPE_UNSUPPORTED
```

terminal/operator intervention.

---

# 206. Job source stale

```text
JOB_SOURCE_SUPERSEDED
```

may be normal skip/success outcome for latest-wins derived jobs rather than error.

---

# 207. ERR-INV-015 — Obsolete Derived Work May Be SKIPPED, Not FAILED

Example:

```text
Search rebuild v10
current = v11
```

→ skip obsolete work.

---

# 208. Historical job missing Revision

Real error:

```text
JOB_SOURCE_NOT_FOUND
```

because exact immutable source expected.

---

# 209. Provider unknown outcome in Worker

Persist:

```text
PROVIDER_OUTCOME_UNKNOWN
```

and enter reconciliation path.

---

# 210. User-cancelled action

Can be normal result:

```text
CANCELLED
```

not necessarily error.

---

# 211. Async job cancelled before execution

Resource status:

```text
cancelled
```

rather than error if user/system intentionally cancelled.

---

# 212. Cancellation race

If external side effect may already have occurred:

do not report simple cancelled.

Use reconciliation/partial state.

---

# 213. Partial success

Multi-target operations may have mixed outcomes.

---

# 214. Example Social

```text
Instagram = published
TikTok = failed
```

overall result:

```text
PARTIAL_SUCCESS
```

or domain status.

Not generic error hiding successful target.

---

# 215. Notification

Same:

```text
In-App = sent
WhatsApp = failed
Email = sent
```

Notification remains valid.

---

# 216. ERR-INV-016 — Multi-Target Error Must Preserve Successful Subresults

---

# 217. Batch operations

If bulk operation exists:

return per-item results where semantics permit.

---

# 218. Do not rollback unrelated independent items just because one failed

unless operation declared atomic.

---

# 219. Atomic bulk operation

Must be explicitly documented.

---

# 220. Error and Audit

Expected validation errors generally do not need Audit.

---

# 221. Authorization denial

May enter security log, not normal AuditEvent every time.

---

# 222. Destructive failure

Failed purge/redaction attempt SHOULD have operational/audit trace where significant.

---

# 223. Error and Analytics

Product analytics MAY track safe aggregate error event types.

Do not send full error payload/private details.

---

# 224. Example

```text
builder_generation_failed
reason_category = validation
```

safe.

---

# 225. Error and VOP

VOP may surface repeated:

```text
provider auth failure
artifact final failure
storage reconciliation failure
```

---

# 226. VOP must deduplicate error noise

Not one Observation per retry attempt.

---

# 227. Error retention

Persistent error codes in:

```text
jobs
artifacts
deliveries
social targets
```

follow owning retention policy.

---

# 228. Stack traces

Only operational logging/monitoring.

Bounded retention.

---

# 229. Error details redaction

Before logging provider error:

remove:

```text
Authorization header
token
cookies
phone/email where unnecessary
message body
```

---

# 230. HTTP response headers

For 429:

```text
Retry-After
```

where known.

---

# 231. For 401

Auth implementation may include safe auth semantics.

No secret detail.

---

# 232. For file download denied

No internal file existence metadata.

---

# 233. Error body content type

HTTP JSON API:

```text
application/json
```

or project-standard problem format if later adopted.

---

# 234. RFC 9457 Problem Details

MAY be adopted for external HTTP APIs later.

Not required baseline.

If adopted, stable application `code` remains mandatory extension.

---

# 235. No framework lock-in

Error semantics must not depend on Next.js internal exception classes.

---

# 236. Domain errors

Framework-agnostic.

---

# 237. Adapter errors

Map framework/provider-specific errors inward.

---

# 238. Testing taxonomy

Required tests:

```text
classification
transport mapping
privacy
retryability
field mapping
unknown errors
provider translation
constraint translation
multi-target partial success
```

---

# 239. ERR-AP-001

**Return raw `error.message` from catch**

---

# 240. ERR-AP-002

**Expose Prisma constraint name**

---

# 241. ERR-AP-003

**Expose SQLSTATE**

---

# 242. ERR-AP-004

**Return stack trace to Public**

---

# 243. ERR-AP-005

**Return provider API key in error details**

---

# 244. ERR-AP-006

**Use localized message string as client logic condition**

---

# 245. ERR-AP-007

**Everything maps to `INTERNAL_ERROR`**

---

# 246. ERR-AP-008

**Every failure marked retryable**

---

# 247. ERR-AP-009

**STALE_VERSION blindly retried automatically**

---

# 248. ERR-AP-010

**Unknown provider outcome blindly retried**

---

# 249. ERR-AP-011

**Hidden entity returns detailed permission reason to Public**

---

# 250. ERR-AP-012

**Feedback submission returns error because WhatsApp failed after commit**

---

# 251. ERR-AP-013

**Search outage makes entire website unavailable**

---

# 252. ERR-AP-014

**AI provider outage blocks manual profile editing**

---

# 253. ERR-AP-015

**MISMATCH represented as exception**

---

# 254. ERR-AP-016

**NOT_SPECIFIED represented as exception**

---

# 255. ERR-AP-017

**Partial Social success reported only as total failure**

---

# 256. ERR-AP-018

**Worker invalid payload retried forever**

---

# 257. ERR-AP-019

**Obsolete cache/search job counted as system failure**

---

# 258. ERR-AP-020

**Error logs full Feedback body**

---

# 259. ERR-AP-021

**Error contract contains arbitrary raw `details` object from provider**

---

# 260. ERR-AP-022

**Public file error reveals filesystem path**

---

# 261. ERR-AP-023

**Provider webhook replay returns failure causing endless provider retry**

---

# 262. ERR-AP-024

**Client crashes on unknown future error code**

---

# 263. ERR-AP-025

**HTTP 200 for every Route Handler error**

---

# 264. Error quality gate

Перед implementation MUST быть определены:

- [ ] canonical categories;
- [ ] stable error code convention;
- [ ] field error contract;
- [ ] public/admin diagnostic separation;
- [ ] retryability classification;
- [ ] stale-version behavior;
- [ ] idempotency conflict behavior;
- [ ] provider unknown-outcome handling;
- [ ] DB constraint translation;
- [ ] storage translation;
- [ ] AI provider translation;
- [ ] notification/social provider translation;
- [ ] HTTP status mapping;
- [ ] Server Action mapping;
- [ ] Worker failure mapping;
- [ ] correlation IDs;
- [ ] safe logging;
- [ ] private-data redaction;
- [ ] async resource failure states;
- [ ] partial-success semantics;
- [ ] monitoring/metrics;
- [ ] localized messages;
- [ ] unknown-code fallback;
- [ ] contract tests.

---

# 265. E2E-ERR-001 — Field validation

Submit missing required Project title.

Expected:

```text
VALIDATION_REQUIRED
path = title
```

No DB error.

---

# 266. E2E-ERR-002 — Invalid URL

ProfessionalLink `javascript:` URL.

Expected:

```text
LINK_SCHEME_UNSUPPORTED
```

---

# 267. E2E-ERR-003 — Stale update

Submit expectedVersion=4 against current 5.

Expected:

```text
STALE_VERSION
```

with no overwrite.

---

# 268. E2E-ERR-004 — Primary unique race

Two concurrent Primary mutations.

One succeeds.

One receives semantic:

```text
PORTFOLIO_PRIMARY_CONFLICT
```

not raw unique constraint.

---

# 269. E2E-ERR-005 — Hidden resource

Anonymous user requests private Casting ID.

Response does not reveal Casting existence or title.

---

# 270. E2E-ERR-006 — Builder hidden entity

Tampered hidden Project ID.

Expected:

```text
BUILDER_ITEM_NOT_ELIGIBLE
```

with safe generic message.

---

# 271. E2E-ERR-007 — Builder expired

Expected:

```text
BUILDER_SESSION_EXPIRED
```

not generic 500.

---

# 272. E2E-ERR-008 — Missing Full Body

Publish Questionnaire.

Expected blocker:

```text
QUESTIONNAIRE_MISSING_FULL_BODY
```

---

# 273. E2E-ERR-009 — Grid incomplete

Finalize 4×4 with 15 cells.

Expected:

```text
GRID_CELL_COUNT_MISMATCH
```

---

# 274. E2E-ERR-010 — Grid AI unconfirmed

Expected:

```text
GRID_CELL_UNCONFIRMED
```

---

# 275. E2E-ERR-011 — QR mismatch

Decoded URL differs.

Expected:

```text
QR_DECODE_MISMATCH
```

artifact not READY.

---

# 276. E2E-ERR-012 — Feedback + WhatsApp outage

Public receives successful Feedback result.

Admin later sees Notification provider failure.

---

# 277. E2E-ERR-013 — AI outage

BB generation returns:

```text
BB_PROVIDER_UNAVAILABLE
```

or normalized provider code.

Profile editing remains functional.

---

# 278. E2E-ERR-014 — AI malformed structured output

Casting analysis produces invalid schema.

Expected:

```text
AI_OUTPUT_SCHEMA_INVALID
```

No confirmed requirement.

---

# 279. E2E-ERR-015 — Opportunity invalid transition

Expected:

```text
OPPORTUNITY_TRANSITION_NOT_ALLOWED
```

No stage mutation/history row.

---

# 280. E2E-ERR-016 — AI tries Booked

Expected:

```text
OPPORTUNITY_BOOKED_REQUIRES_HUMAN
```

---

# 281. E2E-ERR-017 — Idempotent replay

Same Feedback key + same payload.

Second request returns existing success, not duplicate error.

---

# 282. E2E-ERR-018 — Idempotency conflict

Same key + different payload.

Expected:

```text
IDEMPOTENCY_CONFLICT
```

---

# 283. E2E-ERR-019 — Provider unknown outcome

External send accepted but response lost.

System persists:

```text
PROVIDER_OUTCOME_UNKNOWN
```

and does not blindly duplicate send.

---

# 284. E2E-ERR-020 — Storage outage

Upload durable storage unavailable.

Expected normalized storage/service error.

No phantom READY MediaAsset.

---

# 285. E2E-ERR-021 — Search outage

Public profile opens normally.

Search endpoint returns:

```text
SEARCH_UNAVAILABLE
```

safe response.

---

# 286. E2E-ERR-022 — Cache outage

No user-facing error if DB fallback succeeds.

---

# 287. E2E-ERR-023 — Internal exception

Forced unexpected exception.

User gets:

```text
INTERNAL_ERROR
correlationId
```

No stack.

---

# 288. E2E-ERR-024 — Correlation trace

Correlation ID from user error maps to internal structured log/trace.

---

# 289. E2E-ERR-025 — Private log safety

Feedback error path does not log full sender message/phone unless explicitly secured diagnostic policy requires it.

---

# 290. E2E-ERR-026 — Webhook invalid signature

Expected:

```text
WEBHOOK_SIGNATURE_INVALID
```

No state mutation.

---

# 291. E2E-ERR-027 — Webhook replay

Previously processed provider event is acknowledged idempotently without duplicate state change.

---

# 292. E2E-ERR-028 — Social partial success

Instagram success + TikTok failure.

Admin sees both target states, not total failure that hides Instagram success.

---

# 293. E2E-ERR-029 — Old successful PDF

New regeneration fails.

Old published artifact remains available.

New artifact exposes failure code separately.

---

# 294. E2E-ERR-030 — Worker poison payload

Unsupported payload version.

Job reaches final failure/intervention state without infinite retries.

---

# 295. E2E-ERR-031 — Obsolete Search job

v10 indexing job runs after v11.

Result treated as skipped/superseded, not incident failure.

---

# 296. E2E-ERR-032 — Media purge dependencies

Attempt purge referenced Media.

Expected:

```text
MEDIA_DELETE_BLOCKED_BY_DEPENDENCIES
```

with Admin-safe dependency data.

---

# 297. E2E-ERR-033 — Auth

Admin action without session.

Expected Authentication error and no mutation.

---

# 298. E2E-ERR-034 — Authorization

Authenticated low-privilege future role tries Purge.

Expected safe Authorization error.

---

# 299. E2E-ERR-035 — Unknown error code client fallback

Client receives new future code.

UI displays generic safe message and correlation ID without crashing.

---

# 300. Error traceability

Canonical:

```text
FAILURE
  ↓
CLASSIFIER
  ↓
APPLICATION ERROR CODE
  ↓
SAFE ERROR DTO
  ↓
TRANSPORT
  ↓
CLIENT UX
```

---

# 301. Database failure traceability

```text
PostgreSQL constraint
      ↓
Infrastructure Adapter
      ↓
Known semantic mapping
      ↓
Application Error
      ↓
Safe UI
```

---

# 302. Provider failure traceability

```text
External Provider
     ↓
Adapter
     ↓
Normalized Provider Error
     ↓
Retry/Reconcile Policy
     ↓
Application/Job State
```

---

# 303. Async failure traceability

```text
Job Attempt
   ↓
Error Classification
   ↓
Retry / Final / Reconcile
   ↓
Artifact/Delivery State
   ↓
Admin Projection
```

---

# 304. Error compliance criteria

Реализация соответствует DOC-102, если:

1. all expected errors have stable semantic codes;
2. UI behavior does not depend on localized text;
3. ORM/database/provider exceptions do not escape directly;
4. Public and Admin diagnostic detail differ appropriately;
5. hidden resource existence is not leaked;
6. Application category remains independent of HTTP status;
7. validation errors support field paths;
8. readiness blockers use granular actionable codes;
9. authentication/authorization are distinct;
10. token invalid/expired/revoked states are safely handled;
11. stale version is first-class recoverable state;
12. idempotent replay can return original success;
13. same idempotency key with changed request yields conflict;
14. dependency blockers are distinct from validation syntax;
15. rate limiting exposes safe retry semantics;
16. processing failures distinguish permanent/transient causes;
17. provider unknown outcome has separate reconciliation state;
18. external provider errors are normalized;
19. cache outage does not become business failure where fallback exists;
20. Search/AI/provider outage degrades only affected optional capability;
21. unexpected errors return generic safe result + correlation ID;
22. stack traces remain internal;
23. logs exclude secrets/private payload by default;
24. stable error codes may be persisted in jobs/artifacts/deliveries;
25. HTTP status mapping is consistent;
26. Server Actions return structured expected errors;
27. Route Handlers do not return HTTP 200 for ordinary errors;
28. async failures persist on owning technical resource;
29. prior successful artifact is not destroyed by regeneration failure;
30. valid negative business states are not treated as exceptions;
31. multi-target operations preserve partial successes;
32. webhook replay is handled idempotently;
33. poison jobs do not retry forever;
34. obsolete derived jobs may be skipped rather than failed;
35. clients have unknown-code fallback;
36. error taxonomy is covered by deterministic tests.

---

# 305. Финальная доктрина

> **Error architecture платформы должна превращать низкоуровневые исключения в стабильные бизнес- и application-level результаты. Ошибка сообщает клиенту только то, что необходимо для безопасного восстановления или исправления действия, а техническая диагностика остаётся в защищённой observability-среде. Validation, Authorization, Stale Version, Idempotency, Dependency, Provider, Processing и Internal failures имеют разные semantics и разные retry strategies. Ошибка внешнего сервиса никогда не должна подменять состояние авторитетного Domain, а успешная бизнес-транзакция не становится неуспешной только потому, что последующий Notification, PDF, Search или AI side effect завершился ошибкой.**