# DATABASE MIGRATIONS
## DOC-162 — PostgreSQL schema evolution, constraints, backfills, indexes и transactional cutover

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт правила изменения PostgreSQL schema/data structures без потери professional truth, integrity и recoverability.

> **Schema migration is versioned application code. It must be deterministic, reviewable, compatible with the deployment sequence and safe against partial execution; constraints are introduced only after existing data is validated/backfilled honestly.**

---

## 2. Migration tooling

Use one canonical migration mechanism in repository. Every schema change is versioned and applied in deterministic order.

Manual production DDL outside migration history is emergency-only and must be reconciled back into canonical migration chain.

## 3. Migration identity

Each migration includes stable ID/name and purpose. Applied migrations are recorded in DB migration metadata.

## 4. Forward strategy

Prefer forward migrations over routinely relying on destructive down migrations. For lossy transformations, backup/restore is authoritative rollback.

## 5. Expand/contract pattern

For incompatible live changes where zero-downtime matters:

```text
EXPAND: add compatible column/table/index
→ deploy code that can use both
→ backfill
→ validate
→ switch reads/writes
→ CONTRACT: remove legacy structure later
```

For controlled maintenance deployment, simpler atomic cutover may be preferred but still must have backup/validation.

## 6. Nullability

Do not add `NOT NULL` with fabricated professional default.

Safe flow:

```text
add nullable
→ backfill only known values
→ identify unknowns
→ redesign/review if unknown legitimate
→ set NOT NULL only when invariant truly holds
```

## 7. Defaults

Database default is valid only when it expresses true domain default, not migration convenience.

Example forbidden: missing Skill level → `1` merely to satisfy schema.

## 8. UUIDs

Canonical entity IDs use UUID where defined. Legacy IDs may be retained in migration mapping/provenance fields, not exposed as new authority.

## 9. Foreign keys

Introduce FKs after orphan inventory/repair strategy. Do not silently delete orphan professional records to make FK pass.

## 10. Unique constraints

Use DB uniqueness for structural invariants such as idempotency keys/current-primary combinations where schema design supports them.

Before adding, detect duplicates and resolve deterministically/Human review.

## 11. Checks/enums

Controlled states may use DB checks/enums or validated text according to migration flexibility. Application/domain remains source of semantic transitions.

## 12. Indexes

Create indexes for common filters/FKs/statuses before high-load cutover. For large production tables use online/concurrent index creation where supported and operationally necessary.

## 13. Lock analysis

Review potentially blocking DDL:

- column type rewrite;
- table rewrite;
- constraint validation;
- index build;
- large update.

Schedule maintenance or staged approach accordingly.

## 14. Backfills

Backfill scripts are:

- versioned;
- bounded batches for large sets;
- idempotent;
- observable;
- restartable;
- source/version aware.

## 15. Backfill provenance

When new normalized entity is created from legacy row, store mapping through migration map. Do not infer author/timestamp unavailable in source.

## 16. JSONB normalization

Do not migrate structured core professional facts into opaque JSONB for convenience. JSONB reserved for flexible provider/config metadata where canonical schema docs permit.

## 17. History tables

Current Source and revision/history tables are distinct. Migration may create baseline revision for imported current state labeled `MIGRATION_BASELINE`, but cannot fabricate prior revisions.

## 18. Visibility backfill

If legacy visibility semantics known, map exactly. Otherwise default private/review state and report.

## 19. Soft-delete/archive

Map legacy deleted flags carefully:

- active;
- archived;
- deleted;
- unknown.

Do not restore a legacy-deleted record merely because target default is active.

## 20. Time types

Use timezone-aware timestamps for instants. Preserve local business timezone separately where semantically required. Do not reinterpret naive historical date without source context.

## 21. Dates vs timestamps

Shooting/training/project dates that are calendar dates should not become timestamp if timezone has no meaning.

## 22. Numeric/level types

Skill level migration accepts only valid 1..5 confirmed source values. Invalid/qualitative values go review queue.

## 23. Language proficiency

Only exact/approved mapping to `NATIVE/A1…C2`; ambiguous qualitative values remain unresolved.

## 24. Achievement statuses

Only exact semantic mapping. No status rank upgrade.

## 25. Operational tables

Jobs/outbox/deliveries use explicit state mapping. Legacy pending rows are not automatically executed after migration; may become historical/reconciliation queue.

## 26. Idempotency tables

Migration preserves semantic uniqueness keys if operation history matters. New constraints prevent duplicate effects.

## 27. Secrets

If token/secret columns migrate, use encrypted target storage and avoid migration logs containing values.

## 28. Transaction boundaries

Small coherent schema/data mutation may run in transaction. Large backfill may commit per batch with checkpoint; partial state is represented and safely resumable.

## 29. Pre-migration checks

- expected schema version;
- free disk space;
- backup complete;
- no unexpected invalid rows;
- maintenance/cutover readiness;
- application version compatibility.

## 30. Post-migration checks

- migration table updated;
- constraints valid;
- row counts/mappings;
- orphan checks;
- unique/check violations zero;
- critical queries work;
- public/Admin access smoke tests;
- job queues safe.

## 31. Rollback

If migration is reversible and no new writes depend on it, scripted down may be used. For destructive/lossy schema transform, restore snapshot is primary rollback.

## 32. Deployment compatibility

Application should fail fast or refuse unsafe startup when DB schema version is unsupported, rather than run with unknown mismatch silently.

## 33. Migration observability

Report batch progress, rows scanned/mapped/review/failed, duration and last checkpoint without dumping row contents/secrets.

## 34. Security

Migration DB role is privileged only for migration window. Application runtime continues least-privilege.

## 35. Anti-patterns

Forbidden:

1. Manual production schema drift not committed.
2. `NOT NULL DEFAULT` invents professional value.
3. Delete duplicate/orphan rows to make constraint pass without review.
4. One giant transaction updates millions of rows without plan.
5. Column type rewrite during traffic without lock analysis.
6. Legacy deleted row becomes active by default.
7. Naive date timezone invented.
8. Secrets printed by migration.
9. Pending outbox jobs resume blindly.
10. Down migration assumed safe after lossy transform.
11. Code deployed against unsupported schema silently.

## 36. Acceptance criteria

`AC-DBMIG-001` All schema changes are versioned in one migration chain.  
`AC-DBMIG-002` Professional defaults are never fabricated for schema convenience.  
`AC-DBMIG-003` Backfills are idempotent/restartable and preserve provenance.  
`AC-DBMIG-004` FK/unique/check constraints are added after data validation.  
`AC-DBMIG-005` Lock/rewrite risk is reviewed before production DDL.  
`AC-DBMIG-006` History migration never fabricates prior revisions.  
`AC-DBMIG-007` Visibility/archive/status semantics map conservatively.  
`AC-DBMIG-008` Operational pending side effects are reconciled, not blindly resumed.  
`AC-DBMIG-009` Pre/post migration automated checks exist.  
`AC-DBMIG-010` Lossy rollback relies on verified backup.  
`AC-DBMIG-011` App/DB schema compatibility is enforced.  
`AC-DBMIG-012` Migration logs remain secret/privacy-safe.

---

## 37. Финальная доктрина

> **PostgreSQL migration evolves constraints around validated reality. It never manufactures data to satisfy a new schema, never silently drops conflicting legacy evidence, and is deployed as versioned, observable, restartable application code with explicit compatibility and rollback boundaries.**
