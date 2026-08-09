# PRIVACY, AUDIT & ABUSE PROTECTION
## DOC-146 — PII minimization, retention, audit integrity, anti-abuse, privacy purge и security observability

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет privacy-by-design, PII handling, audit semantics, anti-abuse и security telemetry across the project.

> **The system collects and retains only data required for a defined professional or operational purpose. Audit proves high-impact actions without becoming a secret dump; abuse protection limits hostile behavior without silently losing accepted professional records.**

---

## 2. Privacy principles

1. purpose limitation;
2. data minimization;
3. access-class separation;
4. bounded retention;
5. server-side filtering;
6. revocation/purge capability;
7. no unnecessary cross-domain copying;
8. protected backups/logs;
9. explicit provenance;
10. privacy-safe analytics.

## 3. PII examples

```text
name
email
phone
WhatsApp number
social contact identifier
sender metadata
support ticket personal data
IP address where retained
```

Not every professional public field is private, but its use outside intended surface still requires purpose.

## 4. Sensitive data examples

```text
Admin notes
Casting source/materials
private Feedback attachments
security tickets
OAuth/provider identifiers
token-scoped URLs
access logs
```

## 5. Secrets vs PII

Secrets are never treated merely as PII. They have stricter handling and are excluded from logs/analytics/backoffice display.

## 6. Data ownership

Each fact has one owning domain. Analytics, AI, Notifications, Search and VOP reference/snapshot only required subset.

### PRIV-INV-001
Cross-domain convenience copying requires explicit purpose and retention.

## 7. Public Contacts

Only `show_on_public_site=true` methods enter public projection. Hidden contact values are absent from serialized payload, not merely visually hidden.

Notification recipients remain separate operational configuration.

## 8. Feedback

Feedback may contain sender PII and message text. Persist accepted professional inquiry before asynchronous notification, but external notification payload should contain minimal summary/protected link rather than full message/attachment.

## 9. Casting/Opportunity

Private professional workflow data stays Admin-only. Analytics joins via IDs/provenance rather than copying full Casting source or notes.

## 10. Help Tickets

Internal notes/security ticket content have stricter ACL. Public/requester serialization excludes internal notes before response creation.

## 11. AI privacy

AI snapshot is task-specific. No entire database/inbox/profile export for a narrow writing/analysis task.

Raw prompts/provider outputs use separate retention from authoritative Source.

## 12. Analytics privacy

Baseline:

- first-party session identity;
- no browser fingerprinting;
- raw IP not long-term marketing dimension;
- no exact location baseline;
- no raw phone/email/message;
- URL/referrer/query redaction;
- private tokens excluded;
- bounded raw event retention.

## 13. Logs

Application logs are metadata-first:

```text
correlation_id
principal_id/entity_id
operation
status/error_code
latency
```

Central redaction removes cookies, Authorization headers, tokens, passwords, sensitive query strings.

## 14. Audit purpose

Audit answers:

```text
who/what actor
did what
to which entity
when
from which prior state/version where needed
with which result
```

Audit is not a full copy of business record.

## 15. Audit event model

Suggested:

```text
id
actor_type
actor_id?
action
entity_type
entity_id
profile_id?
correlation_id
metadata_minimized
created_at
```

## 16. Audit actor types

```text
HUMAN_ADMIN
SYSTEM_WORKER
VOP_SAFE_ACTION
MIGRATION
PROVIDER_RECONCILIATION
```

AI model itself is not the Human actor for an applied change; provenance may reference AI Draft separately.

## 17. Audit events required

At minimum:

- authentication/security events;
- visibility/publication changes;
- Questionnaire/Theme/Social publish/activation;
- Casting/Opportunity high-impact transitions;
- Contact changes;
- Achievement recognition status changes;
- token/share revoke/create where sensitive;
- OAuth connect/disconnect;
- destructive archive/purge/restore;
- permission/security configuration changes;
- privileged support/maintenance actions.

## 18. Audit immutability

Audit is append-oriented. Ordinary Admin cannot edit/delete individual audit events through generic UI.

Corrections use new event/annotation rather than rewriting history.

## 19. Audit security

Audit read requires privileged Admin access. It may contain metadata about private entities and is never public Search/SEO content.

## 20. Audit content minimization

Do not store:

- password;
- OAuth token;
- full Feedback/Casting text;
- whole uploaded document;
- raw private URL token;
- cookie/header dump.

Use entity IDs and normalized changed-field metadata where sufficient.

## 21. Retention registry

Every data family must declare retention class. At minimum:

```text
AUTHORITATIVE_SOURCE
HISTORICAL_REVISION
PRIVATE_ATTACHMENT
AI_SNAPSHOT_OUTPUT
ANALYTICS_RAW
ANALYTICS_AGGREGATE
NOTIFICATION_DELIVERY
PROVIDER_DIAGNOSTIC
AUDIT
LOG
BACKUP
STAGED_UPLOAD
```

## 22. Retention behavior

Retention periods are configuration/policy values, not scattered magic constants in code.

Expired derived/operational data can be purged without rewriting authoritative history incorrectly.

## 23. Archive vs delete vs redact vs purge

```text
ARCHIVE = remove from active use
REVOKE = deny future access/capability
REDACT = remove sensitive value while preserving safe record
PURGE = physically remove data/copies according to dependency policy
```

These operations are not interchangeable.

## 24. Privacy purge discovery

Purge must consider copies/references in:

- Source;
- attachments;
- historical snapshots where policy allows/mandates redaction;
- AI snapshots/drafts;
- Search indexes;
- caches;
- analytics raw records;
- notifications;
- provider diagnostics;
- logs;
- backups according to backup retention process.

## 25. Immutable history vs privacy

Immutability does not mean prohibited personal data must be retained forever. When legal/privacy policy requires deletion, use redacted/tombstone historical representation preserving minimum integrity.

## 26. Backups

Data may persist in protected backup until normal backup expiry. Operational documentation must distinguish live purge from backup lifecycle and prevent restored deleted data from being silently reintroduced.

## 27. Restore reconciliation

After restore, run retention/revocation reconciliation before exposing public/worker operations.

## 28. Anti-abuse principles

Public write surfaces use:

- payload limits;
- rate limits;
- idempotency/replay controls;
- validation;
- optional honeypot/CAPTCHA based on measured abuse;
- attachment limits/scanning;
- anomaly telemetry.

## 29. Feedback anti-abuse

High-confidence technical abuse may be rejected before persistence. Ambiguous suspected spam SHOULD prefer quarantine/review over silent deletion of legitimate professional inquiry where feasible.

Accepted persisted Feedback is never deleted because later notification failed.

## 30. Builder anti-abuse

Rate-limit session creation/generation, bound templates/items/PDF generation cost, enforce TTL and server revalidation.

## 31. Login abuse

Rate-limit credential attempts/recovery without exposing account enumeration.

## 32. Analytics abuse

Bound event batch size, schema and rate. Invalid client events cannot become arbitrary DB/log injection.

## 33. Webhook abuse

Signature validation plus size/rate/replay controls. Invalid repeated webhooks do not create unbounded log/DB growth.

## 34. File abuse

Upload limits, scanner/quarantine and worker resource caps protect storage/CPU.

## 35. Denial-of-service balance

Protection should avoid allowing attacker to trivially lock legitimate account forever or exhaust provider/API cost through public actions.

## 36. Privacy-safe security telemetry

Useful:

```text
failed login count
rate-limit activation
invalid token count
webhook signature failures
upload rejection classes
SSRF blocked target class
```

Do not store unnecessary raw submitted secret/PII to explain the event.

## 37. Alerts

Security alerts route through Notifications/operations and contain minimal protected-link metadata. Alert channel failure does not erase security event.

## 38. Access review

Periodic review SHOULD cover:

- active Admin accounts;
- connected OAuth accounts/scopes;
- active scoped tokens/share links;
- public Contacts;
- public/private media exposure;
- retained secrets/config.

## 39. Privacy defaults

When a new field/source lacks explicit visibility policy, default should be non-public until classified.

### PRIV-INV-002
Unknown visibility never defaults to public exposure.

## 40. Bot/abuse classification

Heuristics are not infallible. Bot/spam classification is operational metadata and should be revisable. Do not treat heuristic classification as business fact.

## 41. Audit vs Analytics vs Logs

```text
Audit = high-impact action accountability
Analytics = product/business measurement
Logs = engineering diagnostics
```

Never use one as uncontrolled substitute for another.

## 42. Data export

Admin export of private analytics/support/source data is privileged, logged where appropriate and filtered to requested scope. Secrets are excluded.

## 43. Incident evidence

Security investigation may require protected evidence retention. Such retention must be explicit, access-controlled and not cause public leakage.

## 44. Anti-patterns

Forbidden:

1. Public-by-default new field.
2. Hidden Contact sent to browser.
3. Notification includes full Feedback attachment.
4. Analytics stores Feedback body/phone.
5. Logs store Authorization header.
6. Audit duplicates complete DB rows.
7. Audit editable through generic CRUD.
8. Raw IP retained forever for marketing.
9. Browser fingerprinting without explicit requirement.
10. Suspicious Feedback silently dropped after accepted commit.
11. CAPTCHA added as replacement for server validation/rate limits.
12. Restore republishes previously revoked data.
13. “Immutable” used to refuse required privacy redaction.
14. Purge deletes referential history without tombstone strategy.
15. Backup exposed as web-download file.
16. Security alert contains raw secret/token.
17. Heuristic bot/spam result treated as professional fact.

## 45. E2E cases

- hidden contact absent from public serialization;
- full Feedback not present in external notification;
- analytics/logs/audit exclude secret and unnecessary PII;
- archive/revoke/redact/purge have distinct effects;
- privacy purge clears eligible AI/cache/search copies;
- backup restore reconciliation keeps revoked resource inaccessible;
- repeated public abuse triggers rate policy;
- same idempotency request not duplicated;
- ambiguous spam remains reviewable according to policy;
- invalid webhook/upload event cannot grow resources unboundedly;
- audit event cannot be edited by normal Admin;
- new unclassified field is not public.

## 46. Acceptance criteria

`AC-PRIV-001` Every collected field/event has defined purpose/owner/access class.  
`AC-PRIV-002` Hidden/private data is removed before public serialization.  
`AC-PRIV-003` AI/Analytics/Notifications copy only minimized data.  
`AC-PRIV-004` Logs and Audit are centrally secret-redacted.  
`AC-PRIV-005` Audit is append-oriented, privileged and metadata-focused.  
`AC-PRIV-006` Retention is class-specific/configurable.  
`AC-PRIV-007` Archive, revoke, redact and purge are distinct operations.  
`AC-PRIV-008` Privacy purge discovers derived/AI/cache/search/attachment copies.  
`AC-PRIV-009` Restore reconciles deletion/revocation before normal operation.  
`AC-PRIV-010` Public write surfaces enforce bounded anti-abuse controls.  
`AC-PRIV-011` Accepted professional records are not lost due downstream provider failure/spam heuristics.  
`AC-PRIV-012` E2E tests prove privacy minimization, retention, purge, audit and abuse controls.

---

## 47. Финальная доктрина

> **Privacy and abuse protection are enforced through purpose limitation, server-side minimization, bounded retention and explicit lifecycle semantics. Audit records accountability without becoming a secret or content warehouse, while anti-abuse controls reject technical attacks without silently corrupting or losing accepted professional records.**
