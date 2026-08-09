# SECRETS & PROVIDER SECURITY
## DOC-144 — secrets management, OAuth credentials, external providers, webhooks, retries и reconciliation

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет security contract для всех секретов и внешних интеграций: AI providers, Email/WhatsApp, Instagram/TikTok OAuth, provider webhooks, database credentials и encryption keys.

> **A provider credential is a narrowly scoped secret, never business data and never client state. External providers are untrusted dependencies: every request is minimized and every callback is authenticated, normalized and constrained to its integration contract.**

---

## 2. Secret classes

At minimum:

```text
DATABASE_CREDENTIAL
AI_API_KEY
EMAIL_PROVIDER_SECRET
WHATSAPP_PROVIDER_SECRET
SOCIAL_OAUTH_ACCESS_TOKEN
SOCIAL_OAUTH_REFRESH_TOKEN
OAUTH_CLIENT_SECRET
WEBHOOK_SIGNING_SECRET
SESSION_SIGNING/ENCRYPTION_KEY
DATA_ENCRYPTION_KEY
BACKUP_ENCRYPTION_KEY
```

## 3. Secret lifecycle

Every secret SHOULD have defined:

```text
owner/purpose
creation source
storage location
scope
rotation procedure
revocation procedure
expiry if applicable
consumers
```

## 4. Storage rules

Secrets MUST NOT be committed to Git.

Use environment/secret management appropriate to deployment. OAuth tokens stored in DB must be encrypted/protected at rest and readable only by narrow provider service.

No plaintext `.env` backup in public/shared artifact.

## 5. Client boundary

Secrets never appear in:

- React props/browser JSON;
- localStorage/sessionStorage;
- HTML;
- public source map;
- analytics;
- public error;
- QR/PDF;
- generated filenames.

## 6. Logging

Logging middleware MUST redact:

```text
Authorization
Cookie
Set-Cookie
api_key
access_token
refresh_token
client_secret
password
signature secret
```

Do not rely on developers manually avoiding every field; use centralized redaction where practical.

## 7. Git/security scanning

Repository workflow should include secret scanning/pre-commit or CI checks where available. If secret is committed, deleting latest line is insufficient: rotate/revoke secret and address history according to incident procedure.

## 8. Least privilege

Provider credential receives only required capabilities/scopes.

Examples:

- Social Publishing requests publishing scopes, not inbox access when unused.
- DB application role does not need superuser.
- backup account does not need application write privileges.

## 9. Secret rotation

Rotation must support overlap or atomic switch as provider allows.

Process:

```text
create/receive replacement
→ deploy/store replacement securely
→ verify consumers
→ revoke old
→ confirm no remaining references
```

## 10. OAuth flow

OAuth connect requires:

- exact configured redirect URI;
- state validation;
- PKCE where supported;
- code exchange server-side;
- provider account identity verification;
- encrypted token storage;
- scope recording;
- expiry recording.

Authorization code/access token never written to long-lived analytics/logs.

## 11. Token refresh

Refresh is technical credential maintenance.

Refresh job:

- uses narrow Social provider adapter;
- handles token rotation atomically;
- stores expiry;
- marks `REAUTH_REQUIRED` on permanent failure;
- never triggers social publication by itself.

## 12. OAuth revocation

Disconnect:

- revokes provider token if supported;
- immediately marks local credential unusable;
- blocks future schedules;
- preserves publication history;
- audits Human action.

## 13. AI provider security

AI requests contain only purpose-minimized snapshots. Never send:

- provider/API secrets;
- auth cookies;
- unrelated Contacts/Feedback;
- arbitrary full database exports.

AI provider cannot call application DB/shell/network unless a separately defined narrow tool exists; baseline modules use no unrestricted tool execution.

## 14. Notification providers

Email/WhatsApp adapters receive minimal recipient endpoint and rendered message snapshot.

Provider delivery status only updates `NotificationDelivery`; it cannot mutate Feedback/Casting/Opportunity.

WhatsApp outbound must use official/approved Business integration rather than browser automation/unofficial session scraping.

## 15. Social providers

Provider call uses immutable approved SocialPublishSnapshot. Token service supplies credential internally; worker/job payload must not contain raw access token.

Unknown external outcome requires reconciliation before retry.

## 16. Webhook endpoint security

Webhook handlers MUST implement as provider supports:

- signature verification;
- timestamp tolerance;
- replay protection;
- provider event ID dedupe;
- body size limit;
- schema validation;
- account/operation binding;
- state transition validation.

A valid signature proves provider authenticity, not permission to perform arbitrary domain operation.

## 17. Provider event replay

Same event ID is processed idempotently. Out-of-order events do not regress a final confirmed state unless provider semantics explicitly allow it.

## 18. Provider response minimization

Persist normalized fields rather than full response dump:

```text
provider_request_id
provider_message/post_id
normalized_status
normalized_error_code
received_at
```

Raw provider payload retention, if needed for troubleshooting, has short protected retention.

## 19. Unknown outcome

External side effect may be ambiguous after timeout/network break.

Canonical state:

```text
UNKNOWN_OUTCOME
```

Do not map to `FAILED` automatically.

Reconciliation uses provider-supported status endpoint/webhook/idempotency evidence.

## 20. Retry policy

Classify:

```text
RETRYABLE_KNOWN_SAFE
PERMANENT
UNKNOWN_OUTCOME
```

Only first category is automatically retryable. Retry is bounded with backoff/jitter and provider rate-limit semantics.

## 21. Circuit breaking

Repeated provider outage should reduce request pressure and surface channel health rather than create retry storm.

Core business record remains committed/available even when provider unavailable.

## 22. Provider URL allowlist

Adapters use configured provider base URLs/endpoints. Provider endpoints are not user-input arbitrary URLs.

## 23. TLS

Outbound provider communication requires TLS and normal certificate verification. Disabling TLS verification in production is forbidden.

## 24. Environment separation

Production credentials never used by local/test unless explicitly isolated. Staging/test providers/accounts should be distinct where possible.

## 25. Test secrets

Tests use fake/local credentials or dedicated test secrets. Never put real production secrets in fixture snapshots.

## 26. Backup

Encrypted credentials in DB backup remain protected. Encryption key management must not accidentally store key beside unrestricted backup in equivalent trust location.

Recovery runbook documents how provider connections behave after restore.

## 27. Restore

Restore must not:

- mark unknown credentials valid without check;
- replay previous provider sends;
- retry UNKNOWN_OUTCOME blindly;
- reconnect revoked accounts;
- resend old notifications.

Provider state is reconciled first.

## 28. Incident response

If credential exposure suspected:

1. revoke/rotate;
2. disable affected integration if necessary;
3. invalidate sessions/tokens when relevant;
4. review audit/provider activity;
5. remove secret from unsafe logs/history/artifacts;
6. restore integration with replacement secret;
7. document cause/remediation.

## 29. Secret references in jobs

Job payload stores `credential_reference`/account ID, never token value. Worker resolves current valid credential at execution time.

This ensures rotation/revocation affects queued work.

## 30. Secret references in caches

Never cache raw credentials in public/general cache. If in-memory provider client caches credential, lifetime must be bounded and invalidated on rotation/revoke.

## 31. Permission to view secrets

Admin UI should normally show connection state/scopes/expiry, not secret itself. “Copy access token” is not baseline product capability.

## 32. Anti-patterns

Forbidden:

1. API key committed to Git.
2. OAuth token in localStorage.
3. Token included in job JSON.
4. Token in analytics dimension.
5. Full Authorization header in logs.
6. Production secrets in test fixture.
7. TLS verification disabled.
8. OAuth callback without state validation.
9. Client-defined redirect URI.
10. Excessive provider scopes.
11. WhatsApp browser/session automation.
12. Timeout automatically retried as failure after unknown side effect.
13. Unsigned webhook trusted.
14. Webhook allowed to alter unrelated business state.
15. Restore resumes all sends without reconciliation.
16. Revoked social token remains usable from cache.
17. DB app connects as superuser unnecessarily.
18. Old secret remains active indefinitely after rotation.

## 33. E2E cases

- tokens absent from browser responses/logs/analytics;
- OAuth state mismatch rejected;
- requested scope recorded and least-privilege policy enforced;
- token refresh rotates atomically;
- disconnect blocks scheduled social publication;
- invalid webhook signature rejected;
- duplicate webhook idempotent;
- out-of-order webhook cannot regress final state;
- notification provider failure leaves Feedback intact;
- timeout creates UNKNOWN_OUTCOME where appropriate;
- unknown outcome reconciled before retry;
- revoked credential cannot be resolved by worker;
- restore does not replay provider side effects;
- production secret scanner prevents accidental repository exposure.

## 34. Acceptance criteria

`AC-SEC144-001` Secrets are classified, owned, scoped, rotatable and revocable.  
`AC-SEC144-002` No raw secret reaches browser/analytics/standard logs.  
`AC-SEC144-003` Provider credentials are least-privilege.  
`AC-SEC144-004` OAuth uses state validation and secure server-side token exchange.  
`AC-SEC144-005` Job payloads reference credentials rather than embed them.  
`AC-SEC144-006` Provider callbacks are authenticated, deduplicated and state-constrained.  
`AC-SEC144-007` Unknown external outcomes reconcile before retry.  
`AC-SEC144-008` Retry is bounded/provider-aware.  
`AC-SEC144-009` Provider outages never roll back committed business Source.  
`AC-SEC144-010` Restore does not replay provider side effects.  
`AC-SEC144-011` Credential compromise has documented revoke/rotate response.  
`AC-SEC144-012` Security tests prove token secrecy, OAuth, webhook and reconciliation boundaries.

---

## 35. Финальная доктрина

> **Secrets remain server-side capabilities with least privilege and explicit lifecycle. External providers are isolated behind narrow adapters; requests are minimized, callbacks are authenticated and idempotent, and uncertain external effects are reconciled rather than guessed. Provider availability or credentials can fail without corrupting professional Source or silently replaying side effects.**
