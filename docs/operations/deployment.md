# PRODUCTION DEPLOYMENT
## DOC-191 — deployment procedure for Next.js/PostgreSQL/Worker/Nginx on `/data/kate-actor`, port 3336

**Статус:** Canonical / Required

---

## 1. Назначение

Документ описывает repeatable production deployment baseline проекта.

Current baseline:

```text
application data root: /data/kate-actor
application port: 3336
reverse proxy: Nginx
transport: HTTPS
DB: PostgreSQL
runtime: Next.js/Node.js + Background Worker
```

> **Deployment is a controlled state transition from one known release/schema/configuration to another, with preflight, migration, health verification and rollback evidence.**

---

## 2. Deployment inputs

Every deployment identifies:

```text
release/commit SHA
build artifact/source revision
required Node/runtime version
DB migrations
required config schema
media/storage migrations if any
provider config changes
rollback release
```

## 3. Directory baseline

Conceptual target:

```text
/data/kate-actor/
  app/ or deployment checkout
  media/
    originals/
    derived/
    private/
  generated/
  backups/ (if policy keeps local staging copies)
  logs/ only if local logging configured
```

Actual directory names may evolve but canonical storage classes/access remain.

## 4. Separation

Application code deployment must not delete media/data directories. Runtime/release directory and persistent data directory are separate concerns.

## 5. User/permissions

Application/worker run as non-root service identity baseline. Service account receives only needed read/write access to application data directories.

Nginx should not have direct unrestricted read of private originals/attachments unless architecture explicitly uses safe internal redirect with authorization.

## 6. PostgreSQL

DB is reachable only through intended network/socket boundary. Runtime DB credential is least-privilege; migration privilege may be separated if practical.

## 7. Nginx

Nginx responsibilities:

- TLS termination;
- HTTP→HTTPS redirect;
- reverse proxy to `127.0.0.1:3336` or documented local binding;
- request size/timeouts appropriate to upload routes;
- security headers/caching where appropriate;
- no public exposure of `/data/kate-actor` directory.

## 8. App binding

Application should bind local/private interface where reverse proxy is sole public ingress.

Direct public port 3336 exposure is not baseline.

## 9. TLS

Use valid certificate and automated renewal. Verify renewal monitoring. HSTS only after HTTPS is stable and intended domains are covered.

## 10. Preflight checklist

Before deploy:

- release gate DOC-179 passed;
- exact commit known;
- config validated;
- secrets available;
- DB connectivity verified;
- storage mounted/free space;
- backup current for risky migration;
- migrations reviewed/tested;
- maintenance/read-only plan if needed;
- rollback release available.

## 11. Build

Build with production configuration while keeping server-only secrets out of client bundle. Typecheck/build must fail on missing compile-time requirements.

## 12. Dependency install

Use lockfile/frozen dependency resolution. Do not perform unbounded package upgrades during production deploy.

## 13. Migration order

Depending compatibility:

```text
backup
→ maintenance/read-only if required
→ DB migration
→ deploy compatible app
→ rebuild derived data/indexes
```

For expand/contract rollout follow DOC-162 sequence.

## 14. Application start

Service should fail readiness if:

- DB unavailable/incompatible;
- required storage inaccessible;
- required core config invalid.

Optional provider missing/expired may mark degraded instead.

## 15. Worker start

Worker starts only after schema compatible. After restore/migration, provider/outbound job classes may remain paused until reconciliation.

## 16. Health checks

At minimum:

```text
/health/live
/health/ready
```

or equivalent internal endpoints.

Readiness verifies core dependencies without exposing secrets/details publicly.

## 17. Post-deploy smoke

Verify:

1. HTTPS/public domain;
2. Profile/Home;
3. static/media derivative;
4. Admin login;
5. DB read/write safe smoke;
6. Questionnaire/PDF critical path when changed;
7. Feedback submit in controlled manner when changed;
8. worker queue health;
9. provider integration states;
10. logs for startup errors.

## 18. Cache/index

If release changes projection/search/theme/public content:

- invalidate generation;
- rebuild index;
- verify public/private separation.

Do not flush unrelated security-sensitive caches without understanding effects.

## 19. Rollback

Rollback triggers include:

- failed readiness;
- privacy/security leak;
- broken DB compatibility;
- critical public/Admin failure;
- migration integrity defect.

If DB migration is backward-compatible, deploy prior app. If lossy/incompatible, follow documented DB restore/forward-fix plan.

## 20. Zero/low-downtime

Not a requirement at expense of safety. For this small professional site, brief controlled maintenance is acceptable for risky migrations if documented and safer.

## 21. Maintenance page

Should return appropriate status, minimal public message, no stack traces/internal paths.

## 22. Uploads during deploy

Avoid deployments that replace/remove persistent upload root. If write pause needed, reject/queue safely rather than accept partially migrated files.

## 23. Environment variables

Validate against config schema at startup. Sensitive values not printed. Public build variables explicitly separated from server secrets.

## 24. Process management

Use systemd/container/process manager with:

- restart on failure;
- graceful stop timeout;
- environment file permissions;
- logs integration;
- correct working directory/user.

Exact mechanism documented in server runbook.

## 25. Database migration locking

Only one migration process executes at once. App instances must not race migration automatically unless migration tool has explicit safe lock semantics.

## 26. Deployment audit

Record:

```text
deployed commit
actor
start/end
migration versions
result
rollback if any
```

## 27. Secrets change during deployment

Rotate with defined overlap/atomic switch. Never echo secret in shell logs/output captured by CI.

## 28. Provider credentials

A missing optional provider credential disables/degrades that module and surfaces diagnostic; it must not prevent Profile/Media core startup.

## 29. Domain/base URL

Canonical public base URL is explicit config and used for Profile/PDF/QR links. Deployment must test it is HTTPS/public-safe and not localhost/internal hostname.

## 30. Backups

Migration release does not proceed until backup status meets policy. Backup location must not be under Nginx-served directory.

## 31. CI/CD

Automated deployment is preferred when mature, but automation must implement same gates. `main` merge alone is not proof production deployment succeeded.

## 32. Emergency deployment

May shorten irrelevant checks but always records release/backup/targeted tests/post-smoke. Security fixes should not introduce unreviewed migration/data changes casually.

## 33. Deployment anti-patterns

Forbidden:

1. `git pull` plus random package update directly in production with no release identity.
2. App code and persistent media in one replaceable directory.
3. Nginx exposes `/data/kate-actor` publicly.
4. Port 3336 openly published unnecessarily.
5. Migration runs without backup/risk check.
6. App serves traffic on incompatible schema.
7. Worker resumes outbound jobs before restore reconciliation.
8. Secrets printed by config validation.
9. Health endpoint exposes DB credentials/internal stack.
10. No rollback release known.
11. HSTS enabled before HTTPS/domain verified.
12. Production dependency resolution ignores lockfile.

## 34. Acceptance criteria

`AC-DEP-001` Every deployment identifies exact release/commit and migration set.  
`AC-DEP-002` Persistent `/data/kate-actor` data is separated from replaceable app code.  
`AC-DEP-003` App runs behind Nginx/HTTPS; port 3336 is not baseline public ingress.  
`AC-DEP-004` Runtime/service identities use least privilege.  
`AC-DEP-005` Preflight validates config, DB, storage, backup and migration readiness.  
`AC-DEP-006` Schema compatibility is checked before serving traffic.  
`AC-DEP-007` Worker start/reconciliation is controlled after restore/migration.  
`AC-DEP-008` Public/Admin/queue/provider smoke checks follow deploy.  
`AC-DEP-009` Rollback path is known for each release.  
`AC-DEP-010` Canonical base URL produces safe public PDF/QR/Profile links.  
`AC-DEP-011` Secrets/private storage are never exposed by Nginx/logging.  
`AC-DEP-012` Deployment result is recorded/auditable.

---

## 35. Финальная доктрина

> **Production deployment promotes an exact tested release into a known PostgreSQL/storage/config state behind Nginx/HTTPS. Persistent data remains independent from app code, schema compatibility gates traffic, workers resume deliberately, and every deployment has preflight, smoke verification and an explicit rollback path.**
