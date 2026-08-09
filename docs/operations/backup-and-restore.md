# BACKUP & RESTORE
## DOC-193 — PostgreSQL/media/config backup, RPO/RTO policy, restore validation and side-effect-safe recovery

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет backup/restore strategy для authoritative DB, media originals, required configuration and recovery procedures.

> **A backup is valuable only if it can restore authoritative professional data and media into a safe application state. Restore is not complete until schema/access/revocation and pending external side effects are reconciled before workers/public exposure resume.**

---

## 2. Backup scope

Required:

```text
PostgreSQL
immutable media originals
private required attachments according to retention
critical generated artifacts only if not rebuildable or operationally useful
configuration manifest (without unsafe secret exposure)
application release/commit reference
```

Source code itself remains in Git and does not require data backup beyond repository availability.

## 3. Rebuildable data

Usually not required as authoritative backup copy:

- thumbnails;
- search indexes;
- caches;
- QR bytes;
- rebuildable PDF artifacts when immutable snapshot exists.

May still be backed up for faster recovery but never as sole source.

## 4. Backup types

Possible:

```text
DB logical backup
filesystem snapshot/archive
incremental media backup
full periodic backup
```

Exact tools depend deployment.

## 5. RPO/RTO

Operations configuration must define target:

```text
RPO = maximum acceptable authoritative data loss window
RTO = target service recovery duration
```

Exact values are operational commitments and must be set based on actual backup frequency/infrastructure, not invented in documentation.

## 6. DB consistency

Use PostgreSQL-supported consistent backup method. Do not copy live DB data directory arbitrarily unless using valid snapshot/PITR procedure.

## 7. Media consistency

Media backup preserves original bytes/paths/metadata relation. Use checksums/manifests where practical.

## 8. Cross-component consistency

DB may reference media written near backup boundary. Backup strategy should reduce inconsistent snapshots through ordering/snapshots/manifests and post-restore reconciliation.

## 9. Backup schedule

Define automated periodic schedule appropriate to RPO. Monitor last success and age.

## 10. Retention

Maintain multiple restore points. Retention policy balances:

- recent operational recovery;
- rollback/migration points;
- storage cost;
- privacy deletion lifecycle.

## 11. Pre-migration backup

Mandatory before destructive/high-risk migrations/media moves. Assign unique backup ID and record in migration/deploy evidence.

## 12. Off-host/offsite principle

At least one recovery copy should not depend on the same single storage failure domain as production server where infrastructure permits.

A backup only on the same disk/RAID is not sufficient protection from server/storage failure.

## 13. Access control

Backup contains private/admin data; only authorized operator/service can access. Backup files never under public Nginx directory.

## 14. Encryption

Encrypt backup at rest/in transit according to storage/environment risk. Encryption keys handled separately enough that backup theft does not trivially disclose data, while recovery remains possible.

## 15. Secret handling

Do not accidentally dump plaintext OAuth/API secrets into documentation backup package. If encrypted DB contains credentials, protect key material appropriately.

## 16. Integrity

Backup job records:

```text
backup_id
started/completed
DB artifact size/checksum
media manifest/count/size
release/schema version
result
```

## 17. Restore testing

Periodic restore into isolated environment is mandatory. A backup that has never been restored is unverified.

## 18. Restore environment

Restore test must not have active production provider credentials/routes by default. External sends/social publishing disabled/faked until reconciliation.

## 19. Restore sequence

```text
provision isolated/target host
→ restore PostgreSQL
→ restore media originals/private files
→ deploy compatible application release
→ validate schema/config/storage
→ keep outbound workers paused
→ rebuild cache/search/derivatives as needed
→ reconcile tokens/revocations/jobs/provider attempts
→ run security/functional smoke
→ enable public/admin
→ enable workers by class
```

## 20. Schema version

Restore uses application release compatible with backup schema, then controlled migrations may move forward. Do not start latest app against unsupported restored schema.

## 21. Revocation reconciliation

If restoring an older backup, it may contain tokens/accounts/data later revoked/deleted. Operations must apply durable revocation/deletion records available outside snapshot or perform manual reconciliation based on incident context before exposure.

This is particularly important after privacy/security recovery.

## 22. Pending jobs

After restore, inspect job/outbox states. Do not assume `QUEUED` means safe to execute; determine whether side effect already happened after backup snapshot.

## 23. Notifications

Historical pending/attempted sends are reconciled. Do not resend old Feedback notification blindly.

## 24. Social publishing

In-flight/unknown attempts reconcile provider first. Overdue schedules apply missed-window policy; never publish all restored overdue content.

## 25. AI

Old queued analyses/drafts need not rerun automatically. Provider-cost/privacy work is explicit/reconciled.

## 26. VOP

Historical safe actions/findings re-scan current restored state rather than replay action history.

## 27. Search/cache

Rebuild from restored authoritative Source. No need to trust stale backup cache/index.

## 28. Derived media

Rebuild missing derivatives from immutable originals. Check original checksum before blaming derivative.

## 29. Validation after restore

At minimum:

- DB row/schema integrity;
- media original access/checksum sample;
- Admin login;
- public Profile;
- hidden-data negative checks;
- Questionnaire/PDF/QR representative;
- Feedback write test in isolated mode;
- queue/provider state;
- search/cache rebuild.

## 30. Disaster scenarios

Runbooks should cover:

```text
DB corruption/loss
media disk loss
application host loss
bad migration
accidental deletion
security compromise
ransomware-like host compromise
```

## 31. Point-in-time recovery

Optional if PostgreSQL WAL/PITR configured. If not, RPO honestly reflects periodic backup cadence.

## 32. Privacy deletion and backups

Deleted data can persist in protected backups until backup expiration. Restoring older backup requires re-applying deletion/revocation reconciliation before normal operation.

## 33. Backup monitoring

Alert on:

- failed backup;
- backup too old;
- abnormal size drop/growth;
- destination unavailable;
- integrity/checksum failure.

## 34. Restore evidence

Record periodic restore test date, backup ID, result, duration, discovered defects and fixes.

## 35. Anti-patterns

Forbidden:

1. “RAID is backup”.
2. Only one backup copy on same disk.
3. DB files copied live with invalid method.
4. Backup never restore-tested.
5. Backup stored under web root.
6. Restore uses production social credentials immediately.
7. Restored queue sends every pending notification/social post.
8. Latest app runs unknown old schema.
9. Privacy-deleted token/data revived silently.
10. Search/cache backup treated authoritative.
11. Encryption key lost/no recovery procedure.
12. Backup job success not monitored.

## 36. Acceptance criteria

`AC-BACKUP-001` Backup scope includes PostgreSQL and immutable required media.  
`AC-BACKUP-002` RPO/RTO are explicit configurable operational targets.  
`AC-BACKUP-003` Backup method is DB/filesystem-consistent and integrity-recorded.  
`AC-BACKUP-004` At least one recovery copy should survive primary host/storage failure where feasible.  
`AC-BACKUP-005` Backup access/encryption protects private data.  
`AC-BACKUP-006` Pre-migration backup is mandatory for risky migration.  
`AC-BACKUP-007` Periodic isolated restore tests are performed.  
`AC-BACKUP-008` Restore holds outbound workers until reconciliation.  
`AC-BACKUP-009` Notifications/social/AI/VOP side effects are never blindly replayed.  
`AC-BACKUP-010` Search/cache/derived artifacts can rebuild from authoritative restored data.  
`AC-BACKUP-011` Old backup restore reconciles later revoke/privacy-deletion state.  
`AC-BACKUP-012` Backup age/failure/integrity is monitored.

---

## 37. Финальная доктрина

> **Backup protects authoritative PostgreSQL and immutable media, not merely application files. Recovery is proven through periodic isolated restores, and restored state remains quarantined from outbound workers until schema, access revocations, privacy deletions and ambiguous provider side effects are reconciled.**
