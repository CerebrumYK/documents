# INCIDENT RESPONSE
## DOC-195 — severity, containment, diagnosis, recovery and post-incident process

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт единый incident response process для availability, security, data integrity, provider and operational failures.

> **Incident response prioritizes containment of privacy/data-loss/external-side-effect risk, preservation of evidence and restoration of authoritative Source. Fast recovery must not guess provider outcomes, replay sends or hide integrity defects.**

---

## 2. Incident classes

```text
AVAILABILITY
SECURITY
DATA_INTEGRITY
DATABASE
MEDIA_STORAGE
MIGRATION/DEPLOYMENT
PROVIDER
BACKGROUND_JOBS
PERFORMANCE/CAPACITY
BACKUP/RECOVERY
```

## 3. Severity

### P1 Critical

- private/secret data exposure;
- authentication/authorization compromise;
- authoritative data loss/corruption;
- production unavailable broadly;
- uncontrolled duplicate external publication;
- backup/recovery failure during disaster.

### P2 High

- major professional workflow unavailable;
- primary Questionnaire/media broadly broken;
- severe provider outage with time-critical scheduled workflow;
- significant queue/storage capacity risk.

### P3 Normal

Partial degradation/workaround exists.

### P4 Low

Minor operational defect/no immediate business impact.

## 4. Incident roles

For small team, one person may hold multiple roles, but responsibilities remain:

```text
Incident Lead
Technical Investigator
Communications/Stakeholder owner
Recorder
```

## 5. First response

1. confirm symptom/scope;
2. assign severity;
3. preserve correlation/release context;
4. contain active harm;
5. disable unsafe automation/provider if needed;
6. establish timeline;
7. choose rollback/forward-fix path.

## 6. Containment priority

Security/private-data issue may require immediately:

- revoke sessions/tokens;
- disconnect provider credential;
- disable affected public projection/route;
- invalidate public cache;
- pause workers;
- place site/module into safe maintenance mode.

Containment actions must be narrow/predefined where possible.

## 7. Evidence preservation

Preserve relevant:

- release SHA;
- Audit events;
- logs/correlation IDs;
- DB snapshot/backup if integrity case;
- provider request IDs;
- migration version;
- timestamps.

Do not copy raw secrets into incident notes.

## 8. Security credential exposure

Treat exposed secret as compromised even if no misuse seen:

```text
revoke/rotate
→ inspect activity
→ remove unsafe copies
→ restore integration
```

## 9. Data integrity incident

Stop writes if continuing could worsen corruption. Snapshot current state before repair. Prefer restore/validated migration over ad-hoc destructive edits.

## 10. Migration incident

If validation/privacy/data counts fail during cutover, halt traffic/workers and use DOC-165 rollback criteria. Do not “finish migration manually” without source reconciliation.

## 11. Provider incident

Distinguish:

```text
provider unavailable
credential invalid
rate limited
unknown outcome
provider data inconsistency
```

Unknown outcome is reconciled before retry.

## 12. Social publishing incident

If duplicate/unknown publication risk:

- pause social worker;
- reconcile provider IDs/recent post status;
- do not retry unknown sends;
- preserve PublishSnapshot/Attempt evidence.

## 13. Notification incident

Feedback/business records remain authoritative. Pause/retry delivery separately; do not recreate incoming inquiry.

## 14. Queue incident

If backlog grows:

- identify job class/oldest age;
- check provider/resource bottleneck;
- pause harmful retry storm;
- scale/restart safely;
- preserve leases/idempotency.

## 15. Media/storage incident

If disk full/missing:

- stop writes/processing that can corrupt source;
- verify mount/RAID/storage state;
- preserve originals;
- cleanup only known derived/temp data via runbook;
- restore missing originals from backup if required.

Never delete originals to free space without explicit disaster decision.

## 16. Database incident

- stop unsafe writes;
- inspect connections/locks/storage;
- verify backup;
- failover/restore according to DB runbook;
- validate schema and application before traffic.

## 17. Performance incident

Identify whether CPU, memory, DB query, media, provider or queue. Rate-limit/backpressure non-critical heavy work rather than bypass security/quality.

## 18. Rollback vs forward fix

Rollback preferred when:

- new release caused privacy/security leak;
- schema compatibility broken;
- critical widespread regression;
- integrity uncertain.

Forward fix may be appropriate when data state intact and correction is isolated/quick/testable.

## 19. Communication

For user-facing outage, status communication should be factual, concise and not expose exploit/security details prematurely. Internal notes separate from public communication.

## 20. Recovery validation

Before declaring resolved:

- original incident signal gone;
- security/access checks pass;
- critical smoke tests pass;
- queues/provider state stable;
- no hidden replay pending;
- data integrity reconciled.

## 21. RESOLVED vs CLOSED

Incident may be operationally resolved but remain open for postmortem/remediation follow-up.

## 22. Post-incident review

For P1/P2 and meaningful repeats:

```text
what happened
impact
timeline
root/major contributing causes
detection quality
containment/recovery
what went well/poorly
action items
owner/deadline
```

Blameless analysis focuses on system/process improvements.

## 23. Action item classes

- code fix;
- test coverage;
- monitoring alert;
- runbook;
- security control;
- migration validation;
- provider fallback;
- capacity;
- documentation.

## 24. Recurrence

Repeated same class should update systemic Finding/backlog, not normalize recurring manual workaround.

## 25. Incident records

Private operational records with appropriate retention/access. They are not public Help Tickets or professional Feedback.

## 26. VOP

VOP may surface signals/safe containment actions from allowlist, but cannot independently perform destructive/business decisions outside policy.

## 27. Help Tickets

A Help Ticket may report an incident but is not the incident evidence/control plane. Link them explicitly if needed.

## 28. Security notifications

Use notification channels configured for operators, but provider notification failure must not lose incident record.

## 29. Drills

Periodically rehearse:

- restore;
- revoked token/session;
- provider credential expiry;
- disk-full warning;
- migration rollback.

## 30. Anti-patterns

Forbidden:

1. Retry unknown social send during incident.
2. Delete logs/evidence to “clean up”.
3. Publish full secret in incident chat/ticket.
4. Continue writes on known corruption blindly.
5. Delete media originals to recover disk space first.
6. Declare resolved before smoke/access validation.
7. No postmortem/action owner for serious repeat incident.
8. Public status message contains private security detail unnecessarily.
9. VOP gets emergency generic shell/SQL authority.
10. Incident workaround becomes permanent undocumented architecture.

## 31. Acceptance criteria

`AC-INC-001` Incident classification/severity is explicit.  
`AC-INC-002` P1 containment prioritizes security/privacy/data integrity.  
`AC-INC-003` Release/correlation/provider evidence is preserved secret-safely.  
`AC-INC-004` Credential exposure triggers revoke/rotate.  
`AC-INC-005` Unknown provider outcomes reconcile before retry.  
`AC-INC-006` DB/media integrity incidents stop unsafe writes and protect originals.  
`AC-INC-007` Rollback criteria exist and are used for unsafe release/migration.  
`AC-INC-008` Recovery requires security/functional/queue validation.  
`AC-INC-009` P1/P2 incidents produce follow-up actions with owners.  
`AC-INC-010` Incident records remain private and separate from Feedback/Help domains.  
`AC-INC-011` Regular drills cover restore/token/provider/migration scenarios.  
`AC-INC-012` Emergency action never grants unrestricted AI/VOP authority.

---

## 32. Финальная доктрина

> **Incidents are handled by containing active harm, preserving trustworthy evidence, restoring authoritative Source and reconciling every ambiguous side effect before normal automation resumes. Recovery is complete only after the same security and professional invariants that define normal operation are proven again.**
