# DATABASE IMPLEMENTATION
## DOC-203 — PostgreSQL schema implementation, keys, constraints, revisions, outbox and indexes

**Статус:** Canonical / Required

---

## 1. Назначение

Документ переводит database architecture в implementation-level PostgreSQL rules.

> **PostgreSQL is the authoritative transactional store for structured Source, configuration, immutable revisions, operational state, Audit and durable jobs/outbox. The schema reinforces structural invariants while application services own semantic transitions.**

---

## 2. General conventions

- UUID primary keys for domain entities;
- `created_at`/`updated_at` as timestamptz for instants;
- `version` integer/bigint for optimistic concurrency where required;
- explicit FK constraints;
- indexes aligned with query patterns;
- JSONB only for bounded flexible metadata/provider payloads.

## 3. Naming

Use consistent snake_case table/column naming. Join/relation tables express semantic relation, not generic `entity_links` unless relation truly generic and controlled.

## 4. Profile ownership

Profile-owned tables include `profile_id NOT NULL` FK where appropriate. Cross-profile relations should be prevented by application and, where practical, composite FK/constraint design.

## 5. Soft archive

Use explicit lifecycle/archive fields rather than overloaded nullable `deleted_at` for every semantic. Hard deletion policy is domain-specific.

## 6. Current Source vs revisions

Current mutable tables and immutable revision tables are distinct. Revision rows include source/version snapshot payload normalized enough for deterministic rendering/history.

No UPDATE path for immutable revisions except narrowly justified metadata that does not alter frozen semantic content.

## 7. Snapshot payloads

For complex immutable document snapshots, JSONB may store versioned normalized snapshot if schema evolution strategy is documented. Do not store raw ORM object dump or secrets.

## 8. Visibility

Independent boolean/access fields stored explicitly where domain requires:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

No derived “public everywhere” shortcut.

## 9. Ordering

Use explicit `display_order` integer/decimal strategy per collection. Reorder operation updates in transaction and enforces deterministic unique/sequence policy as needed.

## 10. Primary markers

Enforce “at most one current primary” through partial unique index where PostgreSQL permits.

Example conceptual:

```sql
CREATE UNIQUE INDEX ...
ON questionnaires(profile_id)
WHERE is_primary_public = true AND archived_at IS NULL;
```

Exact schema names depend owning module.

## 11. Skills

`skill_definitions` stable dictionary; `actor_skills` unique current `(profile_id, skill_definition_id)`. CHECK level between 1 and 5.

## 12. Languages

Stable language dictionary; actor language unique current `(profile_id, language_id)` and CHECK/enum allowed proficiency.

## 13. Professional links

Store canonical URL/text description/visibility/order/version. URL validation application-level; DB stores normalized string. No raw embed executable HTML.

## 14. Contacts

`contact_entries` and `contact_methods` separate. Contact method normalized value/private flags. Phone normalized form can coexist with display form; no guessed country code in migration.

## 15. Questionnaire

Tables conceptually:

```text
questionnaire_definitions
questionnaire_drafts/config
questionnaire_revisions
questionnaire_artifacts
```

Revision number unique per definition/profile as designed. Published current pointers reference immutable revision.

## 16. Builder

Temporary `builder_sessions` with TTL/version; immutable `builder_generation_snapshots`; generated artifact access metadata separate from snapshot semantics.

## 17. Feedback

`feedback`, `feedback_attachments`, `feedback_notes`, `feedback_casting_links`. Public submit transaction inserts Feedback + Audit/outbox/binds staged attachments atomically.

Read state/workflow state separate columns.

## 18. Castings

Separate `castings`, source refs/snapshots, `casting_requirements`, materials, analysis revisions. Confirmed requirement state explicit; AI proposals not mixed into same authoritative row without status/provenance.

## 19. Opportunities

`opportunities` current state + `opportunity_stage_history` append-only. Stage transition transaction updates current + inserts history + outbox/audit.

## 20. Notifications

Suggested:

```text
notification_intents
notifications (recipient-specific in-app)
notification_recipients/endpoints/preferences
notification_deliveries
notification_attempts/provider_receipts
```

Endpoint PII protected/masked; provider message IDs not public.

## 21. Jobs/outbox

Durable tables include:

```text
jobs
outbox_events
```

Fields: type, payload version, status, available_at, attempts, lease owner/expiry, idempotency/dedupe key, error code, timestamps.

Worker claims rows using safe PostgreSQL locking pattern (e.g. `FOR UPDATE SKIP LOCKED`) according to implementation.

## 22. Idempotency

Central or per-command table stores:

```text
scope/key
request_fingerprint
result reference/status
created_at/expires_at if applicable
```

Unique index prevents races.

## 23. Audit

Append-only `audit_events`; ordinary app role can INSERT/read authorized but not generic UPDATE/DELETE depending operational needs.

## 24. AI drafts/snapshots

Separate tables per capability or shared infrastructure with capability discriminator only if semantics remain typed. Never combine authoritative Source and AI output columns ambiguously.

## 25. Social

Social accounts, encrypted credential reference, mutable draft, immutable publish snapshot, attempt/history tables. Raw token storage isolated/encrypted.

## 26. Help

Ticket current row + status/assignment history + messages typed PUBLIC_REPLY/INTERNAL_NOTE + private attachments/SLA state.

## 27. Achievements

Achievement status/type/subject explicit; evidence relation separate; visibility triplet and version.

## 28. Index strategy

Index common:

- FK ownership;
- status/lifecycle;
- visibility + order;
- created/updated date for inbox lists;
- `available_at/status` jobs;
- outbox undelivered;
- token hash lookup;
- idempotency unique keys;
- search-specific indexes.

Avoid indexing every boolean independently without query evidence.

## 29. Pagination

Admin growing lists use keyset/cursor where useful; stable sort `(timestamp,id)`.

## 30. Transactions

Repository methods accept transaction context. Domain service owns BEGIN/COMMIT. Provider call never inside.

## 31. Isolation/races

Use row locking/unique constraints/compare version according to race. Do not globally use serializable isolation without measured need.

## 32. Migration safety

All schema changes via versioned migrations. New constraints introduced after backfill/validation per DOC-162.

## 33. Secrets/encryption

If app-layer encryption used, ciphertext/version/key-id fields explicit. Keys external to DB. Avoid searching/indexing encrypted secret values.

## 34. Backups

Schema design must support consistent DB backup and reconstruction of references to media. Derived cache/search need not be in DB backup.

## 35. Testing

Integration tests prove:

- constraints;
- transaction rollback;
- concurrent primary/idempotency/transition races;
- immutable tables not modified through repository;
- query performance/index hot paths.

## 36. Anti-patterns

Forbidden:

1. Binary media blobs in normal DB rows baseline.
2. Every structured fact inside JSONB.
3. No FK because “app handles it”.
4. No unique constraint for race-sensitive primary/idempotency.
5. AI output written into Source table directly.
6. Revision row updated in place.
7. One `status` column meaning read/workflow/delivery simultaneously.
8. Notification endpoint same table/field as public Contact by shortcut.
9. Job payload contains OAuth token.
10. Direct provider response dumped unbounded into JSONB forever.

## 37. Acceptance criteria

`AC-DB203-001` PostgreSQL schema reflects Source/config/revision/derived-operational separation.  
`AC-DB203-002` UUID/FK/version/timestamp conventions are consistent.  
`AC-DB203-003` Structural invariants have DB constraints/unique indexes where practical.  
`AC-DB203-004` Skills/Languages/visibility/status semantics are constrained correctly.  
`AC-DB203-005` Questionnaire/Builder/Social revisions/snapshots are immutable by design.  
`AC-DB203-006` Feedback/Opportunity transitions are transactional with history/outbox.  
`AC-DB203-007` Notification/public Contacts remain separate schemas.  
`AC-DB203-008` Durable jobs/outbox/idempotency are concurrency-safe.  
`AC-DB203-009` Audit is append-oriented and secret-safe.  
`AC-DB203-010` Index/pagination strategy supports growing operational lists.  
`AC-DB203-011` Schema evolves only through tested migrations.  
`AC-DB203-012` Real PostgreSQL integration tests prove constraints/transactions/races.

---

## 38. Финальная доктрина

> **The PostgreSQL schema is an executable structural guardrail around the domain: explicit owners and states, immutable history, independent visibility, foreign keys, uniqueness, optimistic versions, outbox/jobs and indexes. Application services decide meaning; the database makes race-induced and structurally impossible states difficult or impossible to commit.**

## DOC-208 unified-stack binding

For this project, the concrete implementation of the repository/transaction boundary is **Drizzle ORM + node-postgres (`pg`) + one drizzle-kit/reviewed-SQL migration chain**. Raw PostgreSQL SQL remains allowed only inside infrastructure/repository code for explicit features such as partial indexes, FTS/trigram and `SKIP LOCKED`. Prisma/Knex/TypeORM/parallel migration runners are not baseline alternatives. See DOC-208.
