# MAINTENANCE & RUNBOOKS
## DOC-196 — routine maintenance, safe rebuilds, provider recovery and operational procedures

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт runbook framework and standard maintenance procedures. Exact shell/systemd commands may be added when implementation/server unit names are known; documentation must not invent them.

> **Routine maintenance uses narrow, repeatable and observable procedures. Derived state may be rebuilt automatically; authoritative professional Source and external side effects require stronger explicit controls.**

---

## 2. Runbook template

Every runbook contains:

```text
Purpose
Trigger/symptom
Risk level
Prerequisites
Required backup?
Steps
Validation
Rollback/stop condition
Expected duration class
Escalation
Audit/evidence
```

## 3. Daily/continuous checks

Operational dashboard should reveal:

- app readiness;
- DB health;
- disk free space;
- queue oldest age/failures;
- backup freshness;
- provider reauth/unknown outcomes;
- certificate expiry;
- security alerts.

## 4. Weekly/monthly tasks

As applicable:

- review backup success/restore-test age;
- review failed/dead jobs;
- inspect disk growth/orphans;
- dependency/security updates;
- provider account/token health;
- active Admin/token/share access review;
- log retention;
- review recurring VOP/support findings.

## 5. Safe derived rebuilds

Approved classes:

```text
rebuild search index
invalidate/rebuild cache
regenerate thumbnail/web derivative
regenerate PDF from immutable revision
regenerate verified QR
recompute readiness/projections
```

All use current Source/snapshot and cannot alter professional facts.

## 6. Search rebuild runbook

1. confirm DB healthy;
2. record current index generation;
3. build new index/projection from authoritative Source;
4. validate public/private result sample;
5. atomically switch generation/alias;
6. retain/clean old generation per policy.

Never expose Admin index during rebuild.

## 7. Cache invalidation

Use scoped generation/entity invalidation where possible. Full flush only when justified. Validate token/public partitions after security-sensitive changes.

## 8. Media derivative rebuild

1. verify original exists/checksum where needed;
2. enqueue registered derivative job;
3. observe success;
4. validate output metadata;
5. never replace original.

## 9. PDF rebuild

Use immutable QuestionnaireRevision/BuilderSnapshot. Rebuild does not create new professional revision. Validate text/links/QR.

## 10. QR rebuild

Generate from frozen/current approved canonical target depending context, decode verify exact value, maintain access class/cache partition.

## 11. Stuck job runbook

1. inspect job type/state/lease/attempts/correlation;
2. determine if side-effect-free/idempotent;
3. if safe, release/retry through registered handler;
4. if external unknown outcome, reconcile first;
5. monitor result;
6. create incident/support issue if recurring.

## 12. Notification provider outage

- confirm domain records are committed;
- pause/throttle affected delivery if storm;
- inspect channel health/credential;
- retry only retryable deliveries;
- do not recreate Feedback/Intent;
- use In-App/admin dashboard as authoritative attention source.

## 13. Social reauth

1. identify account `REAUTH_REQUIRED`;
2. pause/block schedules automatically by policy;
3. Admin completes OAuth reconnect;
4. verify scopes/account ID;
5. reconcile overdue schedule according to missed-window policy;
6. do not publish automatically merely because token restored.

## 14. Social UNKNOWN_OUTCOME

1. stop blind retry;
2. query/provider webhook/recent-post evidence;
3. if found → mark PUBLISHED with provider ID;
4. if confirmed not accepted and retry-safe → explicit/system retry policy;
5. if unresolved → Human attention/incident.

## 15. AI provider outage

Mark AI capability degraded, keep manual workflows active. Do not repeatedly retry expensive requests without policy. Existing Source unchanged.

## 16. Disk space low

Preferred cleanup order:

1. identify growth;
2. rotate/compress expired logs;
3. purge unreferenced temp/staged data according retention;
4. purge rebuildable old derived cache according policy;
5. expand storage if needed;
6. never delete immutable originals/private authoritative attachments casually.

## 17. DB connection saturation

- inspect pool/slow queries/worker concurrency;
- reduce non-critical worker load;
- fix leak/query;
- do not simply raise max connections without DB capacity review.

## 18. Slow query

Capture query fingerprint/plan, confirm index/selectivity/N+1, fix/test migration/index. Avoid logging sensitive bind values unnecessarily.

## 19. Backup failure

- treat backup age risk based on RPO;
- inspect destination/free space/credentials;
- rerun after cause fixed;
- verify artifact integrity;
- escalate if RPO at risk.

## 20. Restore runbook reference

Use DOC-193. Never perform “quick restore” directly over production without backup/current-state preservation and side-effect quarantine.

## 21. TLS renewal failure

Monitor expiry well ahead. Fix DNS/challenge/certificate service, validate Nginx config/certificate chain, reload gracefully. Never bypass TLS permanently.

## 22. Nginx/app restart

Use graceful restart/stop and health checks. Do not delete PID/lock/data files blindly. Verify port 3336 local service and public HTTPS separately.

## 23. Database migration runbook

Use DOC-160…165 + release gate. Maintenance window/write pause as needed. One migration actor, backup, validation, rollback checkpoint.

## 24. Provider secret rotation

Use DOC-192/144: create replacement, update protected config, verify, revoke old, ensure logs/jobs do not embed old secret.

## 25. Admin session revoke

Security Admin can revoke one/all sessions through dedicated mechanism. Does not delete Admin account/profile data.

## 26. Token/share revoke

Revoke scoped token and invalidate access-sensitive caches/resolvers. Historical snapshot semantics remain; future application access denied.

## 27. Privacy purge

Use dependency-aware command/process, dry-run report where feasible, backup/retention implications documented. No ad-hoc SQL cascade.

## 28. Orphan cleanup

Only derived/staged/unreferenced records/files with verified reference check and retention age. Produce counts/bytes freed.

## 29. Dependency updates

- review release/security notes;
- update lockfile;
- run build/tests;
- migration/config changes inspected;
- deploy through normal gate.

Avoid unattended major-version production upgrade.

## 30. OS/runtime maintenance

Security updates/reboots scheduled with backup/health checks. Worker/provider side-effect safety remains during restart.

## 31. Emergency containment

Predefined actions only:

- maintenance mode;
- pause workers;
- revoke session/token/provider;
- disable affected public resolver/projection;
- rollback release.

No generic AI/VOP shell tool.

## 32. Change record

For production maintenance record:

```text
date/actor
reason
commands/procedure reference
release/config impact
result
validation
follow-up
```

Secrets redacted.

## 33. Runbook testing

Critical runbooks are rehearsed periodically: backup restore, migration rollback, token revoke, provider reauth, disk low.

## 34. Anti-patterns

Forbidden:

1. Delete originals first when disk low.
2. Retry any stuck job without knowing side-effect class.
3. Restore directly over live production casually.
4. Social token restored then overdue posts auto-send.
5. Search rebuild briefly exposes Admin index.
6. Cache flush used to “fix” auth bug without root cause.
7. Increase DB connections instead of fixing leak blindly.
8. Run migration from two processes.
9. Secret rotation logs old/new secret.
10. Runbook contains guessed commands not matching actual service names.

## 35. Acceptance criteria

`AC-RUN-001` Runbooks follow consistent purpose/prereq/steps/validation/rollback template.  
`AC-RUN-002` Safe derived rebuild procedures never mutate Source.  
`AC-RUN-003` Stuck external-side-effect jobs reconcile before retry.  
`AC-RUN-004` Disk cleanup protects originals/authoritative attachments.  
`AC-RUN-005` Backup/TLS/DB/provider failures have procedures.  
`AC-RUN-006` Social reauth does not auto-publish overdue content.  
`AC-RUN-007` Token/session revocation has narrow operational action.  
`AC-RUN-008` Privacy purge is dependency-aware, not ad-hoc SQL.  
`AC-RUN-009` Production maintenance records validation/results without secrets.  
`AC-RUN-010` Critical runbooks are periodically rehearsed.

---

## 36. Финальная доктрина

> **Runbooks turn operations into controlled, repeatable actions. They distinguish safe derived-state repair from professional Source mutation and ambiguous external side effects, so routine maintenance can rebuild, retry, rotate and recover without improvising destructive commands in production.**
