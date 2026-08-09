# MIGRATION TESTING
## DOC-178 — dry-run, fixture generations, idempotency, reconciliation и rollback rehearsal

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет automated/manual tests для DOC-160…165.

> **Migration tests begin from representative legacy states—not only from an empty database—and prove that reruns, ambiguity, corrupt media, visibility and rollback behave exactly as the migration contracts specify.**

---

## 2. Test source generations

Maintain representative legacy fixtures for:

```text
EMPTY/FRESH
MINIMAL_LEGACY
TYPICAL_LEGACY
DIRTY_LEGACY
CURRENT_PREVIOUS_RELEASE
```

Dirty fixture contains intentional duplicates, missing files, ambiguous values and broken references.

## 3. Schema chain

Test complete migration chain from each supported prior schema/version to latest. Fresh installation also runs latest chain successfully.

## 4. Dry-run

For migration with dry-run, assert:

- no DB/source mutation;
- planned counts/report correct;
- ambiguous rows listed;
- invalid rows classified;
- no side effects/providers called.

## 5. Idempotency

Run migration twice. Second run must not duplicate records/relations/media/revisions and should produce stable mapping/report.

## 6. Resume/checkpoint

Interrupt a batch after known checkpoint, restart, assert completion without duplication/loss.

## 7. Professional ambiguity fixtures

Include:

```text
Skill without level
Language “fluent”
Achievement “festival participant”
Project “Other” ambiguous training
Project recognition vs personal award
unknown contact role
```

Expected result: review/unknown, not invented canonical fact.

## 8. Visibility

Legacy public/private combinations test conservative mapping. Public flag must not auto-enable Builder/QNR/social permissions.

## 9. Current-primary conflicts

Fixture with multiple legacy primaries should produce deterministic review/resolution strategy rather than violate target unique invariant.

## 10. Media checksums

Use known fixture hashes. Post-migration original bytes must match exactly. Include zero-byte/corrupt/missing file.

## 11. Media context

Same binary used in different contexts tests dedupe/provenance preservation. Feedback attachment must not become Portfolio media.

## 12. External URLs

Include valid URL, unsafe scheme, Admin/local URL, redirect, expired signed URL, raw embed HTML. Assert correct classification/review.

## 13. QR legacy

Fixture QR exact match/rejected mismatch/undecodable/Admin target. Decode verification required.

## 14. Questionnaire history

Legacy existing PDF without reconstructable config remains legacy artifact; test migration does not fabricate exact historical Source revision.

## 15. AI provenance

Legacy AI text without source snapshot remains `AI_LEGACY_UNKNOWN/unknown origin`, not fabricated model/provenance.

## 16. Operational pending state

Fixture pending notification/social/job records. After migration test workers/providers remain disabled/non-executing until reconciliation.

## 17. Provider evidence

Local social caption without provider post ID must not become PUBLISHED. Known provider post ID can import historical identity according to policy.

## 18. DB constraints

After migration, run invariant queries/attempt invalid inserts to prove constraints valid.

## 19. Access negative tests

Public/Builder/token probes after migration verify hidden data absent and private files inaccessible.

## 20. Search/cache

Rebuild rather than trust legacy index; test current source projection.

## 21. E2E post-migration

Run critical E2E suite against migrated fixture, not only fresh seeded DB.

## 22. Performance

Large synthetic migration tests establish upper-bound runtime/lock/storage behavior and batch progress; not every CI run must execute huge dataset, but pre-release rehearsal should.

## 23. Rollback rehearsal

For risky migration:

1. create backup/snapshot;
2. migrate;
3. trigger simulated blocker;
4. rollback/restore;
5. verify old app/data works;
6. verify no external side effects replayed.

## 24. Forward-fix test

Where documented, verify safe re-run/repair of partial derived state without touching preserved source.

## 25. Report assertions

Machine-readable migration report counts must match actual target counts/mapping table.

## 26. Security

Migration tests assert reports/logs contain no credential/raw secret and file modes/access class safe.

## 27. Production rehearsal

Before major real cutover, run migration against sanitized clone or protected staging copy representative of production data, with no outbound provider calls.

## 28. Acceptance criteria

`AC-MIGTEST-001` Migration chain is tested from representative prior states.  
`AC-MIGTEST-002` Dirty/ambiguous source fixtures are included.  
`AC-MIGTEST-003` Dry-run causes zero source/target side effects.  
`AC-MIGTEST-004` Re-run/checkpoint restart is idempotent.  
`AC-MIGTEST-005` Professional ambiguity remains review/unknown.  
`AC-MIGTEST-006` Visibility mappings are conservative.  
`AC-MIGTEST-007` Media originals checksum-match and corrupt/missing files are reported.  
`AC-MIGTEST-008` Legacy operational pending state does not execute automatically.  
`AC-MIGTEST-009` Post-migration access/security and critical E2E suites run.  
`AC-MIGTEST-010` Rollback/restore rehearsal proves recoverability.  
`AC-MIGTEST-011` Machine-readable migration report reconciles actual data.  
`AC-MIGTEST-012` Production-like rehearsal disables external provider side effects.

---

## 29. Финальная доктрина

> **Migration tests intentionally include the dirty and ambiguous states that real legacy systems contain. They prove conservative semantics, byte preservation, idempotent restart, access safety and rollback—not merely that clean fixture rows can be inserted into a new schema.**
