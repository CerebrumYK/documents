# OPERATIONS OVERVIEW
## DOC-190 — production operating model, responsibilities, environments and service lifecycle

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт эксплуатационную модель проекта после deployment: environments, service ownership, startup/shutdown, health, maintenance, providers, data protection и release operations.

> **Operations preserve the same domain guarantees as application code. A restart, restore, provider outage or maintenance action must never silently republish private data, replay outbound side effects or rewrite professional Source.**

---

## 2. Production components

Baseline:

```text
Nginx / TLS
Next.js application :3336
Background Worker
PostgreSQL
/data/kate-actor media/derived/config roots
provider adapters (AI, notifications, social)
backup jobs
monitoring/logging
```

## 3. Environment classes

```text
DEVELOPMENT
TEST/CI
STAGING (if deployed)
PRODUCTION
```

Production secrets/data never casually reused in lower environments.

## 4. Service ownership

Each operational component has documented:

- startup method;
- config inputs;
- health check;
- logs;
- dependencies;
- restart policy;
- backup relevance;
- upgrade procedure.

## 5. Core vs optional dependencies

Core:

```text
application
PostgreSQL
required local media storage
```

Optional/degradable:

```text
AI provider
Email/WhatsApp provider
Instagram/TikTok provider
external link health targets
analytics collection
```

Provider outage must not take authoritative Source offline.

## 6. Startup sequence

Recommended:

```text
storage mounted/permissions valid
→ PostgreSQL available
→ schema migration/version check
→ application start
→ health check
→ worker start in safe mode/reconcile
→ provider-dependent jobs enabled after reconciliation
→ Nginx traffic enabled/validated
```

## 7. Shutdown

Graceful shutdown:

- stop accepting new work;
- finish/return leased jobs safely;
- close DB connections;
- avoid half-written artifacts;
- never mark unknown provider side effect failed solely because process stops.

## 8. Health model

Separate:

```text
LIVENESS
READINESS
DEPENDENCY_HEALTH
```

Application can be live but not ready when DB unavailable/schema incompatible.

Optional provider outage should usually not fail core readiness; expose degraded dependency status.

## 9. Configuration validation

Startup fails fast for invalid required configuration such as DB/storage/core base URL. Optional integration configuration can mark provider disabled/degraded rather than crash entire site.

## 10. Schema compatibility

App verifies compatible DB migration version. Do not serve requests with unknown schema mismatch.

## 11. Filesystem

Before ready:

- `/data/kate-actor` mounted;
- required directories accessible;
- free-space threshold acceptable;
- private/public permissions correct.

## 12. Worker operations

Monitor queue depth/oldest job/retries/final failures. Worker restart is safe due durable jobs/idempotency.

## 13. Provider operations

Track provider account/credential health separately. Reauth errors visible to Admin; no secret values in dashboard/logs.

## 14. Deployment philosophy

Deployments are repeatable, versioned and rollback-aware. Avoid manual server edits that are not represented in repository/config/runbook.

## 15. Maintenance windows

Used for risky migrations/media moves where controlled write pause is simpler/safer than dual-write complexity.

Maintenance UI should not expose internal diagnostics/secrets.

## 16. Time synchronization

Server time must be synchronized. Scheduling/expiry/audit rely on accurate clock. Business timezone stored explicitly when needed.

## 17. Capacity

Monitor:

- disk space/inodes;
- DB size/connections;
- memory/CPU;
- media growth;
- backup growth;
- queue backlog.

## 18. Data lifecycle jobs

Retention/cleanup jobs are typed/idempotent and cannot delete referenced current Source blindly.

## 19. Search/cache rebuild

Document safe rebuild command/job. Search/cache loss is recoverable from authoritative Source.

## 20. Derived artifact rebuild

Thumbnails/PDF/QR/other derivatives can be rebuilt from Source/immutable snapshots without mutating professional facts.

## 21. Operational access

Server/DB/filesystem access is limited to authorized operators. SSH/root/DB admin credentials are outside application UI and managed securely.

## 22. Logs

Logs have rotation/retention and secret redaction. Correlation IDs connect app/worker/provider issues.

## 23. Change management

Production change identifies:

```text
commit/release
migration set
config changes
provider changes
rollback plan
post-deploy validation
```

## 24. Release workflow

Release quality gate DOC-179 precedes deployment. After deployment run smoke + monitoring observation.

## 25. Backup operations

Backups scheduled/monitored; failure alerts are operationally visible. Periodic restore tests required.

## 26. Incident operations

Incident response DOC-195 covers severity, containment, communication and recovery. Security incident may revoke sessions/tokens/providers before full diagnosis.

## 27. Manual interventions

Any direct DB/filesystem repair is exceptional, backed up, documented and reconciled into code/migration where repeatable. Generic “fix production manually” is not normal workflow.

## 28. Audit

Operational high-impact changes (restore, provider reconnect, emergency maintenance) have audit/change record.

## 29. Runbook index

DOC-196 holds command-level procedures for routine maintenance and common failures.

## 30. Anti-patterns

Forbidden:

1. App starts despite incompatible DB schema.
2. Worker starts replaying old sends before restore reconciliation.
3. Optional AI outage fails public Profile readiness.
4. Media root missing but app reports healthy.
5. Production config edited with no record.
6. Backup job failure unnoticed.
7. Logs grow without rotation until disk full.
8. Direct DB repair without backup/audit.
9. Provider secret printed by health endpoint.
10. Search/cache treated as source of truth.

## 31. Acceptance criteria

`AC-OPS190-001` Production component/dependency inventory is explicit.  
`AC-OPS190-002` Liveness/readiness/dependency health are distinct.  
`AC-OPS190-003` Core startup verifies DB schema and storage.  
`AC-OPS190-004` Optional provider outage degrades independently.  
`AC-OPS190-005` Worker restart is safe through durable jobs/idempotency.  
`AC-OPS190-006` Disk/DB/queue/provider health is observable.  
`AC-OPS190-007` Deployments/config changes are repeatable/versioned.  
`AC-OPS190-008` Backup failures and restore tests are operationalized.  
`AC-OPS190-009` Search/cache/derived artifacts have safe rebuild paths.  
`AC-OPS190-010` Direct production repair is exceptional/audited.  
`AC-OPS190-011` Release gate and post-deploy smoke are mandatory.  
`AC-OPS190-012` Restore/startup never blindly replays external side effects.

---

## 32. Финальная доктрина

> **Operations run the application as a set of recoverable services around authoritative PostgreSQL and immutable media. Startup validates schema/storage, workers are durable and restart-safe, optional providers degrade independently, and every deploy/restore/maintenance action preserves access, history and side-effect guarantees defined by the product domains.**
