# CONFIGURATION & SECRETS OPERATIONS
## DOC-192 — environment schema, production configuration, secret rotation and startup validation

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет operational management application configuration/secrets.

> **Configuration is versioned by schema and validated at startup; secret values are managed outside Git and never become diagnostic output. Optional integration configuration may degrade its module, while missing core configuration prevents unsafe startup.**

---

## 2. Configuration classes

```text
CORE_RUNTIME
PUBLIC_CONFIGURATION
FEATURE_CONFIGURATION
PROVIDER_CONFIGURATION
SECRET
OPERATIONAL_POLICY
```

## 3. Core examples

```text
DATABASE_URL/reference
APP_BASE_URL
APP_PORT=3336
MEDIA_ROOT=/data/kate-actor/...
NODE_ENV
```

## 4. Optional provider examples

```text
AI provider key/model config
Email provider
WhatsApp Business provider
Instagram OAuth client config
TikTok OAuth client config
```

## 5. Config schema

Application owns typed config schema with:

- required/optional;
- type/format;
- safe default only when semantically valid;
- secret flag;
- environment restrictions.

Invalid required config fails startup with redacted message.

## 6. Public vs server variables

Variables intentionally exposed to browser must use explicit public namespace/build mechanism. Server secrets can never be exposed by naming mistake.

CI/build should inspect bundles/config where possible.

## 7. `.env`

Example/template file may list variable names/placeholders, never real secret values. Real production env file permissions restricted and excluded from Git.

## 8. Base URL

`APP_BASE_URL` must be canonical HTTPS production domain and is security-critical for generated Profile/PDF/QR links. Reject localhost/private host in production configuration.

## 9. Port

Application baseline `3336`; reverse proxy handles 443/80. Port change is config/documented deployment change.

## 10. Storage paths

Resolve absolute allowed root and ensure media subpaths cannot escape it. Startup validates existence/permissions/free space threshold as configured.

## 11. Feature flags

Feature flags can enable optional module availability, but cannot disable hard security invariants or grant VOP/AI forbidden business authority.

## 12. Provider disabled state

If optional provider config missing:

```text
module provider state = DISABLED/NOT_CONFIGURED
```

UI clearly indicates unavailable integration while manual/core functionality remains.

## 13. Secret sources

Preferred:

- OS/service protected environment;
- secret manager where deployed;
- encrypted application credential store for OAuth tokens.

No secret in source code/database migration literal.

## 14. Rotation

Runbook records:

```text
secret purpose
provider/location
rotation procedure
consumer restart/reload requirement
revocation confirmation
```

## 15. Database credential

Runtime role least privilege. Rotation updates application secret and verifies connections before old credential revoked where possible.

## 16. OAuth client secrets

Stored server-side; callback/redirect config exact. OAuth user tokens managed by Social domain encrypted store.

## 17. Webhook secrets

Rotated with provider-supported overlap/version when available. Handler can temporarily accept old/new during controlled rotation only.

## 18. Encryption keys

Encryption key rotation is higher-risk: requires data re-encryption/versioned key strategy. Do not overwrite key without recovery plan for existing encrypted tokens.

## 19. Config change workflow

1. document/request change;
2. validate staging/non-production if relevant;
3. backup if data/security risk;
4. update secret/config;
5. restart/reload controlled service;
6. health/smoke;
7. revoke old secret if rotation;
8. record result.

## 20. Startup diagnostics

May report:

```text
DATABASE configured=true
AI provider configured=false
Social Instagram configured=true
MEDIA_ROOT accessible=true
```

Never secret values.

## 21. Logging

Central redaction handles config object serialization. Avoid `console.log(process.env)`.

## 22. Backups

Backups may need config manifest but never plain secrets in documentation bundle. Recovery separately ensures credentials/keys are available through protected channel.

## 23. Restore

After restore validate secret/config version and external provider credential state. Do not assume old OAuth tokens remain valid.

## 24. Config drift

Operational checks compare expected schema/required variable presence. Manual hidden server-only overrides should be minimized and documented.

## 25. Environment separation

Production and dev/test keys/accounts distinct when possible. Never point test social publishing at production actor account by default.

## 26. Secret exposure incident

If secret appears in repo/log/chat/artifact:

- treat as compromised;
- rotate/revoke;
- remove unsafe copy/history as appropriate;
- review access/activity;
- fix prevention control.

## 27. Configuration documentation

Maintain table (without values):

```text
name
class
required environments
purpose
secret yes/no
default
restart required
owner
```

## 28. Anti-patterns

Forbidden:

1. Real `.env` committed.
2. `NEXT_PUBLIC_`/browser variable contains secret.
3. Startup prints token.
4. Missing AI key crashes Profile page.
5. Feature flag disables authorization.
6. Production base URL localhost.
7. Encryption key rotated without old-data plan.
8. Test uses production Social account.
9. Backup archive contains plaintext environment secret unnecessarily.
10. `console.log(process.env)` diagnostics.

## 29. Acceptance criteria

`AC-CFG-001` Configuration has typed startup schema.  
`AC-CFG-002` Missing invalid core configuration fails readiness/startup.  
`AC-CFG-003` Optional provider config degrades only provider feature.  
`AC-CFG-004` Real secrets are excluded from Git/browser/logs/analytics.  
`AC-CFG-005` Public base URL is validated for production-safe generated links.  
`AC-CFG-006` Storage root/path permissions are validated.  
`AC-CFG-007` Feature flags cannot override hard security/authority rules.  
`AC-CFG-008` Secret rotation/revocation procedures are documented.  
`AC-CFG-009` Encryption key changes preserve decryptability/recovery.  
`AC-CFG-010` Production/test provider credentials are isolated.  
`AC-CFG-011` Configuration health exposes state, not values.  
`AC-CFG-012` Secret exposure triggers rotation, not merely deletion from file.

---

## 30. Финальная доктрина

> **Configuration is an explicit validated operational contract, while secrets remain protected capabilities outside source control and client state. Core misconfiguration prevents unsafe startup; optional integrations degrade independently; and every credential has a lifecycle for storage, rotation, revocation and recovery without ever being printed as application diagnostics.**
