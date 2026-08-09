# MONITORING & OBSERVABILITY
## DOC-194 — logs, metrics, traces, health, alerts and operational dashboards

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет observability model для application, PostgreSQL, worker queues, media, providers, security and backups.

> **Observability should make failures diagnosable without copying sensitive professional content or credentials into telemetry. Every critical async/provider workflow exposes state, latency and failure classification; authoritative Source remains in domain storage, not logs.**

---

## 2. Three pillars

```text
LOGS
METRICS
TRACES/CORRELATION
```

Distributed tracing may be lightweight for modular monolith, but request/job/provider correlation IDs are mandatory enough to follow a workflow.

## 3. Correlation IDs

Generate/propagate across:

```text
HTTP request
server command
domain transaction/outbox
background job
provider request
webhook/reconciliation
```

Do not place secret/token in correlation ID.

## 4. Structured logs

Prefer JSON/structured fields:

```text
timestamp
level
service
request_id/correlation_id
principal_id?
profile_id?
entity_type/id?
action
status
error_code
duration_ms
```

## 5. Redaction

Central redaction for:

- passwords;
- Authorization/Cookie;
- OAuth/API tokens;
- private share tokens;
- full Feedback/Casting content;
- unnecessary phone/email;
- signed storage URL queries.

## 6. Log levels

```text
DEBUG (non-production or bounded)
INFO
WARN
ERROR
FATAL
```

Expected user validation failure is not ERROR flood. Security denied attempts may have dedicated metric/log sampling.

## 7. Application metrics

At minimum:

- request rate;
- 4xx/5xx by route class;
- latency p50/p95/p99;
- active/failed auth;
- public page health;
- server action failures.

## 8. Database metrics

Monitor:

- availability;
- connections/pool saturation;
- query latency/slow queries;
- locks/deadlocks;
- DB size/storage;
- replication/PITR health if used;
- migration version.

## 9. Worker metrics

```text
queue depth
oldest queued age
leased/running
success rate
retry rate
final failures
job duration by type
stuck lease count
```

## 10. Outbox

Monitor undispatched age/count. Persistent outbox backlog indicates delivery/projector issue even when DB writes succeed.

## 11. Media

Metrics:

- upload success/rejection;
- derivative jobs;
- transcode failures;
- missing file/404;
- storage bytes/free space;
- processing duration;
- quarantined count.

## 12. PDF/QR

Track:

- PDF generation success/failure/duration;
- QR generation/verification failure;
- artifact size;
- stale/missing derived artifact findings.

No raw target token in metric label.

## 13. Notifications

Per channel:

```text
queued/deferred/sending
provider accepted
delivered if confirmed
retry/final failure
provider latency
recipient suppression counts
```

Avoid high-cardinality endpoint/email/phone labels.

## 14. Social

Track:

- account connected/reauth-required count;
- schedules due/missed/blocked;
- attempts accepted/published/failed/unknown;
- reconciliation age;
- provider latency/rate limits.

## 15. AI

Track by capability/provider/model:

- request count;
- success/schema failure;
- latency;
- provider error;
- token/cost units if available/appropriate;
- validation failure.

Never log raw prompt by default.

## 16. Feedback/Opportunity product health

Operational metrics may count records/states/overdue next actions without copying message body/private notes.

## 17. Analytics pipeline

Monitor analytics ingestion availability/schema reject/aggregate freshness. Analytics itself does not monitor infrastructure alone.

## 18. Security metrics

Examples:

```text
failed login/rate-limit
invalid token attempts
SSRF blocked target classes
invalid webhook signatures
upload security rejection
permission-denied anomalies
```

Bound cardinality and avoid raw hostile payload.

## 19. Backup metrics

- last successful backup timestamp;
- age;
- duration;
- artifact size;
- integrity status;
- last restore test date/result.

## 20. Health endpoints

### Liveness
Process alive/event loop responding.

### Readiness
Core app can safely serve requests: DB/schema/storage okay.

### Dependency
Optional provider states returned in Admin/monitoring, not necessarily public detailed endpoint.

## 21. Alerts

Alert only actionable conditions with owner/runbook:

- public/core unavailable;
- DB unavailable/near capacity;
- disk critically low;
- queue oldest age excessive;
- backup too old/failed;
- repeated media/PDF failure;
- notification/social provider systemic failure;
- social UNKNOWN_OUTCOME old;
- security incident signal;
- TLS certificate expiry risk.

## 22. Alert severity

Suggested:

```text
P1 immediate availability/security/data-loss risk
P2 major workflow degradation
P3 operational degradation
P4 informational/maintenance
```

Not identical to Feedback/Opportunity business priority.

## 23. Alert dedupe

One persistent incident should not page repeatedly every minute. Group/dedupe/recovery notification as platform permits.

## 24. Dashboards

Recommended:

```text
Service overview
PostgreSQL
Workers/Outbox
Media/PDF/QR
Providers
Security
Backups
```

## 25. SLO indicators

Track separately:

- public availability/latency;
- Admin core health;
- background processing health;
- provider delivery success;
- backup freshness.

Optional provider outage not necessarily public-site outage.

## 26. High cardinality

Do not use full URL, user email, phone, token, message ID unboundedly as metric labels. IDs may be logs/traces, not metric dimensions.

## 27. Log retention

Rotate/bound logs. Security/Audit retention independent. Disk-full due logs is operational defect.

## 28. Privacy

Access to logs/monitoring is privileged. Export/sharing of logs must consider private metadata.

## 29. VOP relationship

VOP consumes selected operational health/findings but does not replace observability. Observability detects/records; VOP can surface actionable findings/safe remediation.

## 30. Help Tickets

Support tickets may reference correlation ID rather than copying full logs. Monitoring links do not grant unsupported access.

## 31. Deployment markers

Record release/commit deployment events on dashboards/traces to correlate regressions.

## 32. Migration markers

Record migration/cutover time and version for post-migration monitoring.

## 33. Synthetic checks

External/internal scheduled checks may verify public homepage/health/critical link without creating fake business records or inflating analytics (tag/exclude synthetic traffic).

## 34. Alert testing

Periodically test alerts/runbook routing. Alert configuration existing is insufficient if nobody receives it.

## 35. Anti-patterns

Forbidden:

1. `console.log` raw Feedback/Casting payload.
2. Token/email as Prometheus label.
3. Health endpoint exposes credentials/stack.
4. Queue only monitored by total count, not oldest age.
5. Provider accepted counted as delivered universally.
6. No alert for backup failure/disk low.
7. Every 404 triggers P1 alert.
8. VOP used as only logs/monitoring system.
9. Synthetic traffic inflates marketing analytics.
10. Logs never rotate.

## 36. Acceptance criteria

`AC-OBS-001` Request/job/provider workflows have correlation IDs.  
`AC-OBS-002` Logs are structured and centrally secret/PII-redacted.  
`AC-OBS-003` App/DB/queue/outbox/media/provider/backup health has metrics.  
`AC-OBS-004` PDF/QR and AI schema/provider failures are observable.  
`AC-OBS-005` Security events have bounded privacy-safe telemetry.  
`AC-OBS-006` Liveness/readiness/optional dependency health are distinct.  
`AC-OBS-007` Alerts are actionable, severity-classified and deduplicated.  
`AC-OBS-008` High-cardinality PII/tokens are excluded from metrics.  
`AC-OBS-009` Logs have retention/rotation.  
`AC-OBS-010` Deployment/migration markers enable regression correlation.  
`AC-OBS-011` Synthetic monitoring is excluded from human analytics.  
`AC-OBS-012` Alert paths are periodically tested.

---

## 37. Финальная доктрина

> **Observability follows a request from browser through transaction, outbox, job and provider without copying the professional payload into telemetry. Metrics reveal capacity/freshness/failure, logs explain normalized events, and actionable alerts link directly to runbooks while preserving secret and privacy boundaries.**
