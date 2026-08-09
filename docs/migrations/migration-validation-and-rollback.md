# MIGRATION VALIDATION & ROLLBACK
## DOC-165 — reconciliation, acceptance evidence, cutover, rollback и post-migration observation

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет, как доказать корректность migration и безопасно выполнить cutover/rollback.

> **Migration is not successful because the script exited with code 0. It is successful only when source-to-target reconciliation, domain invariants, access boundaries, media checksums and representative user journeys pass, and a tested rollback point remains available until acceptance.**

---

## 2. Validation layers

```text
L1 structural/schema
L2 counts/mapping
L3 referential integrity
L4 semantic/domain invariants
L5 access/security
L6 media/checksum
L7 rendering/user journeys
L8 operational/side-effect reconciliation
```

All relevant layers must pass or have explicit accepted exception.

## 3. Pre-cutover gate

Required:

- migration scripts committed/reviewed;
- source inventory frozen;
- backup verified;
- dry run complete;
- ambiguous queue understood;
- enough disk/time capacity;
- application target build tested;
- rollback procedure rehearsed/credible.

## 4. Source/target counts

Per domain report:

```text
source total
automatically mapped
review-required
intentionally skipped
invalid/corrupt
failed
target created/updated
```

Equation must reconcile; unexplained loss is blocker.

## 5. Mapping reconciliation

Every migrated legacy ID maps to stable new ID or explicit disposition. Duplicate mappings checked for unintended many-to-one collapse.

## 6. Referential integrity

Check:

- orphan FKs;
- same-profile relations;
- owner relations;
- media references;
- Project/Role links;
- Feedback/Casting/Opportunity provenance;
- Questionnaire snapshot refs.

## 7. Domain invariants

Examples:

- Skill level only 1..5;
- Language only Native/A1–C2;
- exactly allowed Emotional Grid sizes/counts;
- at most one current primary public Questionnaire;
- no unsupported Achievement status;
- no Builder-hidden item in eligibility projection;
- immutable revisions not mutable.

## 8. Visibility/access validation

Run negative tests:

- hidden contacts absent from public response;
- private media inaccessible anonymously;
- Builder cannot fetch ineligible item;
- token-scoped resource requires valid scope;
- Admin data absent from public Search/cache;
- Support internal notes hidden.

Migration that “works” but changes privacy is failed migration.

## 9. Media validation

For originals:

```text
source SHA-256 == target SHA-256
source bytes == target bytes count/size
```

Check missing/corrupt list reconciles inventory.

Derived media can differ/regenerate but must render successfully.

## 10. URL validation

Check public canonical links, redirects, no raw filesystem paths, no Admin URLs in QR/PDF, legacy URL redirects where promised.

## 11. PDF/QR validation

Generate representative Questionnaire:

- selectable text;
- actual hyperlinks;
- QR exact decode;
- no private/admin URLs;
- layout/readability.

## 12. Search/cache validation

Rebuild from target Source, then confirm public/Admin/Builder separation. Search/cache data itself need not be migrated if rebuildable.

## 13. Operational reconciliation

Before workers/providers resume inspect:

- outbox;
- notification deliveries;
- social schedules/attempts;
- AI requests;
- pending jobs;
- VOP actions;
- retention cleanup.

No ambiguous external side effect is automatically replayed.

## 14. Provider reconciliation

Legacy/restored pending social/notification operations classified:

```text
COMPLETED_HISTORICAL
SAFE_TO_RETRY
UNKNOWN_OUTCOME_RECONCILE
CANCELLED
DO_NOT_EXECUTE
```

## 15. Authentication/secrets validation

Verify production can authenticate with intended account/session system; secrets available but not logged. Migrated OAuth credentials checked without exposing raw values.

## 16. Representative E2E journeys

At minimum:

1. public Profile/Portfolio;
2. public media/link;
3. public Questionnaire + PDF/QR;
4. Builder generate;
5. Admin login/Profile edit;
6. Media upload/derivative;
7. Feedback submit/In-App notification;
8. Casting/AI review boundary;
9. Opportunity transition;
10. Theme preview/publish boundary;
11. Help Ticket;
12. Social Draft/schedule dry integration path where safe.

## 17. Historical comparison

Sample important legacy public records and compare target representation. Differences documented as intended correction vs defect.

## 18. Dry-run vs production

Production migration produces report comparable to dry-run. Material unexpected count changes halt cutover pending investigation.

## 19. Cutover plan

Typical controlled baseline:

```text
announce maintenance/read-only
→ stop legacy writes/workers
→ final backup
→ migrate final delta/full dataset
→ validate critical gates
→ deploy target app
→ rebuild derived indexes/cache
→ smoke test
→ enable public/admin
→ enable workers/providers selectively
```

## 20. Worker enable sequence

Enable low-risk derived jobs before outbound providers. Reconcile notifications/social/unknown attempts before their workers resume.

## 21. Rollback triggers

Examples:

- data count mismatch;
- privacy/access leak;
- media checksum loss;
- authentication failure;
- migration script partial corruption;
- critical public/Admin journey broken;
- unexpected provider side effects.

## 22. Rollback modes

### Pre-publication rollback
Restore DB/storage backup and old application.

### Post-cutover short window
Requires write-freeze or documented delta handling. Avoid split-brain writes.

### Forward-fix
Appropriate only when data integrity intact and issue is small/reversible; must not be used to rationalize privacy/data-loss defects.

## 23. Backup validation

A backup exists only if restorable. Before risky migration, verify latest backup metadata/integrity and periodic restore test policy.

## 24. Rollback data loss

If new system accepted writes after cutover, rollback to old backup can lose them. Therefore define rollback window/write handling explicitly before cutover.

## 25. Post-migration observation

Monitor elevated for defined period:

- 5xx/error rates;
- auth failures;
- media 404;
- PDF failures;
- queue depth;
- provider failures;
- security denied-access anomalies;
- Feedback submissions;
- DB performance.

## 26. Human review queue

Migration may cut over with non-blocking review items only if they are private/non-critical and explicitly accepted. No unresolved item may accidentally publish.

## 27. Acceptance sign-off

Migration acceptance record includes:

```text
migration version
source backup ID
validation report
known review items
known accepted limitations
cutover timestamp
sign-off actor
rollback checkpoint disposition
```

## 28. Legacy decommission

Only after acceptance/observation:

- stop old jobs/services;
- revoke unused credentials;
- archive source backup according to retention;
- remove old public routes or preserve redirects;
- document final state.

## 29. Automated validation

Scripts/tests should produce machine-readable report with fail/pass/warn, not only console prose.

## 30. Error budget for migration

Professional fact loss/privacy leak/checksum mismatch = zero-tolerance blocker. Cosmetic differences may be warnings if reviewed.

## 31. Anti-patterns

Forbidden:

1. Success = script exit 0.
2. Counts not reconciled.
3. Only positive tests; no hidden-data probes.
4. No checksum validation for originals.
5. Workers enabled before provider reconciliation.
6. Rollback backup never tested.
7. Rollback after new writes without delta plan.
8. Unknown-outcome social send retried during startup.
9. Unresolved ambiguous records published.
10. Legacy system deleted immediately after cutover.
11. Migration acceptance undocumented.

## 32. Acceptance criteria

`AC-MVAL-001` Validation covers structural, count, referential, semantic, security, media and E2E layers.  
`AC-MVAL-002` Source/target counts reconcile with explicit dispositions.  
`AC-MVAL-003` Media originals checksum-match.  
`AC-MVAL-004` Negative access tests prove no visibility regression.  
`AC-MVAL-005` PDF/QR/public links are validated.  
`AC-MVAL-006` Search/cache rebuild from authoritative target data.  
`AC-MVAL-007` Pending side effects reconcile before workers resume.  
`AC-MVAL-008` Cutover has explicit order/maintenance behavior.  
`AC-MVAL-009` Rollback triggers and write-loss handling are documented.  
`AC-MVAL-010` Post-cutover monitoring is elevated.  
`AC-MVAL-011` Human acceptance record exists before legacy decommission.  
`AC-MVAL-012` Professional fact loss/privacy leak/media checksum mismatch are blockers.

---

## 33. Финальная доктрина

> **A migration is accepted only when it can be reconciled from frozen source evidence to target records, passes domain and access invariants, preserves original media bytes and demonstrates real user journeys. Cutover is staged, outbound side effects resume only after reconciliation, and a verified rollback point remains until Human acceptance and post-cutover observation complete.**
