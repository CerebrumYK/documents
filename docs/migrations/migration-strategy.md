# MIGRATION STRATEGY
## DOC-160 — общая стратегия безопасной миграции legacy data/schema/media

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт единый подход к schema/data/media migrations при переходе текущего/legacy приложения к canonical domain model.

> **Migration preserves known facts and provenance, never invents missing professional truth, is restartable/idempotent, produces explicit review queues for ambiguity, and is considered complete only after deterministic validation against source and target invariants.**

---

## 2. Migration classes

```text
SCHEMA
REFERENCE_DATA
DOMAIN_DATA
MEDIA
DERIVED_ARTIFACT
ACCESS_VISIBILITY
HISTORY_REVISION
OPERATIONAL_DATA
```

Each class has separate safety/rollback semantics.

## 3. Principles

1. inventory before transform;
2. backup before destructive step;
3. preserve source until acceptance;
4. map only unambiguous facts automatically;
5. unknown stays unknown;
6. explicit provenance;
7. idempotency;
8. dry-run/report mode;
9. staged verification;
10. no automatic publication;
11. no external side-effect replay.

## 4. Migration phases

```text
DISCOVER
→ CLASSIFY
→ BACKUP
→ DRY_RUN
→ TRANSFORM
→ VALIDATE
→ REVIEW_AMBIGUOUS
→ CUTOVER
→ OBSERVE
→ DECOMMISSION_LEGACY
```

## 5. Source preservation

Legacy source dataset/files remain read-only backup until migration acceptance and retention policy permit removal.

## 6. Migration manifest

Every migration has manifest:

```text
migration_id
version
source_version/commit/schema
target_version
started_at
completed_at
status
input counts
output counts
review counts
error counts
checksum/report refs
```

## 7. Idempotency

Re-running same migration version over same source must not duplicate:

- records;
- relations;
- attachments;
- revisions;
- tickets;
- publication artifacts.

Use stable legacy IDs/mapping table/fingerprint.

## 8. Legacy mapping table

Recommended:

```text
migration_entity_map
migration_id
legacy_type
legacy_id
new_type
new_id
mapping_status
```

This supports reconciliation and reruns.

## 9. Mapping statuses

```text
MAPPED
SKIPPED_INTENTIONAL
REQUIRES_REVIEW
INVALID_SOURCE
FAILED
SUPERSEDED
```

## 10. Unknown data

Never use fabricated defaults for professional facts merely to satisfy target not-null constraint. Change target model/migration staging or mark review where business permits.

Examples:

- unknown Skill level ≠ level 1;
- “fluent” language ≠ guessed C1;
- unknown Achievement year ≠ Jan 1;
- project festival mention ≠ personal Winner.

## 11. Visibility migration

Legacy boolean/public state maps only if semantics are known. If no equivalent exists, default private/review rather than public.

## 12. Publication safety

Migration creates/updates Source but does not automatically publish newly classified records unless legacy public state is unambiguous and migration policy explicitly preserves it.

High-risk new capabilities (Builder/Social/QR) default disabled until configured.

## 13. Revisions/history

Do not fabricate historical revisions from current row when prior versions are unavailable. It is acceptable to import one baseline migration revision labeled source/migration origin.

## 14. Timestamps

Preserve trustworthy original timestamps. Unknown timestamps remain unknown or migration timestamp is stored separately, never presented as historical fact.

## 15. Media

Original bytes should be preserved/checksummed. Derivatives can be regenerated. Storage paths migrate to new managed references; public URLs never become raw filesystem paths.

## 16. External URLs

Validate/canonicalize without changing destination semantics. Unsafe/admin/internal/expired signed URLs become review/invalid states, not silently published replacements.

## 17. AI data

Legacy AI output provenance may be unknown. Do not label Human or AI source falsely. AI outputs never upgrade professional facts during migration.

## 18. Operational data

Old notification/job/log state is especially dangerous. Do not import pending sends/jobs as executable work blindly. Convert to historical/reconciliation states.

## 19. Side effects

Migration itself does not:

- send email/WhatsApp;
- publish social posts;
- trigger external AI analysis;
- contact providers;
- reopen tickets;
- republish Questionnaire.

## 20. Dry run

Dry-run produces:

- source counts;
- planned mappings;
- warnings;
- ambiguous rows;
- invalid references;
- projected target counts;
- no production mutation.

## 21. Batch execution

Large migrations use bounded batches/checkpoints. Each batch transaction is appropriately scoped. Resume from checkpoint safely.

## 22. Concurrency/cutover

During migration either:

- maintenance/read-only window; or
- dual-read/write carefully designed; or
- incremental backfill with final delta catch-up.

For this project baseline, controlled maintenance/read-only cutover is preferred over complex dual-write unless uptime requirement demands otherwise.

## 23. Validation

Validation includes:

- counts;
- referential integrity;
- checksums for media;
- domain invariants;
- visibility/access probes;
- representative page/PDF rendering;
- migration-specific E2E.

## 24. Review queue

Ambiguous professional data produces Human review queue/report with original source preserved.

## 25. Rollback

Rollback approach depends on phase. Before cutover: restore DB/storage backup. After partial migration: rerunnable/reversible scripts plus backup. Never rely solely on reverse SQL if semantics/lossy transform occurred.

## 26. Audit

Record migration actor/version and high-value source changes. Avoid one audit event per low-level row if it overwhelms useful history; use batch + provenance strategy.

## 27. Security

Migration scripts run privileged but narrowly. Secrets not printed. Dumps/reports are protected and not committed if they contain private data.

## 28. Performance

Migrations must avoid unbounded locks and disk exhaustion. Monitor transaction/log/storage size.

## 29. Acceptance report

Every completed migration provides:

```text
what changed
counts before/after
ambiguous/unmapped items
validation results
rollback point
known limitations
Human sign-off
```

## 30. Anti-patterns

Forbidden:

1. Guess missing facts.
2. Default all legacy records public.
3. Run destructive transform without backup.
4. Re-run duplicates records.
5. Fabricate revision history.
6. Treat derived thumbnail as original.
7. Import pending notification job and send it.
8. Mark social post published without provider evidence.
9. Convert every legacy “other project” to Training blindly.
10. Drop source before validation/sign-off.
11. Store migration report with secrets in Git.
12. Rollback plan equals “hope script works”.

## 31. Acceptance criteria

`AC-MIG160-001` Every migration has manifest/version/source/target identity.  
`AC-MIG160-002` Migration is idempotent/restartable.  
`AC-MIG160-003` Unknown/ambiguous professional facts are never guessed.  
`AC-MIG160-004` Legacy source/backups are preserved through validation.  
`AC-MIG160-005` Visibility/publication mappings are conservative.  
`AC-MIG160-006` Historical revisions are not fabricated.  
`AC-MIG160-007` Pending provider/worker side effects are not replayed blindly.  
`AC-MIG160-008` Dry-run and review queue exist for risky migrations.  
`AC-MIG160-009` Cutover/rollback are explicitly planned.  
`AC-MIG160-010` Counts, integrity, access and representative rendering are validated.  
`AC-MIG160-011` Migration reports are secret/privacy-safe.  
`AC-MIG160-012` Human acceptance occurs before legacy decommission.

---

## 32. Финальная доктрина

> **Migration is a controlled translation of known legacy truth—not an opportunity to “complete” missing data. It preserves originals and provenance, maps only what can be justified, quarantines ambiguity for Human review, never replays external side effects blindly and remains idempotent, observable, validated and recoverable until explicit cutover acceptance.**
