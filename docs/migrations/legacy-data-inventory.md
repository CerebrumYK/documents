# LEGACY DATA INVENTORY
## DOC-161 — инвентаризация legacy schema/files/routes/features перед миграцией

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет обязательный inventory-процесс перед любой migration. Inventory фиксирует, **что реально существует**, какие семантики известны, какие данные дублируются, какие связи потеряны и какие элементы требуют Human review.

> **No migration design begins from assumptions about legacy data. It begins from evidence: schema, files, counts, routes, code paths, runtime configuration and representative records.**

---

## 2. Inventory outputs

Для каждой legacy подсистемы подготовить:

```text
source location
schema/format
record/file count
primary identifiers
relationships
lifecycle/publication semantics
visibility semantics
known defaults
null/missing patterns
duplicates
broken references
media paths
external URLs
AI/provenance state
operational side effects
migration disposition
```

## 3. Sources to inventory

At minimum:

- PostgreSQL schema/tables/views;
- application source models/types;
- filesystem under legacy media paths;
- `/data/kate-actor` current deployment structure;
- environment/config files (without copying secrets into report);
- Next.js routes/server actions/API;
- cron/background scripts;
- generated PDF/assets;
- public/admin URLs;
- documentation (`project-status`, `admin-guide`, `future-roadmap`, `deployment`);
- OAuth/provider integrations;
- help content;
- analytics/log-related storage.

## 4. Table inventory

For every table capture:

```text
name
purpose inferred/confirmed
PK
FKs
unique constraints
indexes
column types/nullability/defaults
row count
size
created/updated timestamps
soft-delete/archive mechanism
```

## 5. Semantic confidence

Each mapping assumption receives:

```text
CONFIRMED
HIGH_CONFIDENCE
AMBIGUOUS
UNKNOWN
```

Only confirmed/unambiguous semantics can drive automatic professional mapping.

## 6. Profile data

Inventory:

- identity/name;
- biography locales;
- appearance/profile fields;
- public visibility;
- hero/current fields;
- duplicated values elsewhere.

Identify any field stored in multiple tables/components.

## 7. Portfolio

Classify legacy photos:

- fullbody;
- closeup;
- waist;
- profile;
- 3/4;
- staged;
- role/project photos;
- emotional portfolio;
- unknown/custom.

Do not infer category solely from filename if no authoritative evidence.

## 8. Emotional portfolio/grid

Inventory source shoots, shooting dates, originals, derived composites, crop configs, primary markers and any photos incorrectly mixed with Projects.

Check whether existing grids are true derived artifacts or flattened images without reconstructable config.

## 9. Projects/Roles

Inventory:

- title;
- year/dates;
- role;
- director/production;
- media;
- links;
- `Other` educational entries that may be Training;
- duplicate project/role records.

Educational migration candidates are reviewable, not automatic if semantics unclear.

## 10. Training

Locate any dedicated Training data plus educational records stored in Project/Other/free text. Record provider/course/teacher/date evidence.

## 11. Skills

Inventory free-text skills, categories, any level values. Identify legacy qualitative labels or missing levels. Never assign canonical 1–5 during inventory.

## 12. Languages

Inventory language names/codes and proficiency labels. Mark mappings like `fluent`, `basic`, `native speaker` with semantic confidence. Do not guess CEFR where not explicit.

## 13. Professional links/media

Identify:

- external URL rows;
- local managed file rows;
- raw embed HTML;
- video/audio links;
- descriptions/order;
- public flags;
- duplicates;
- broken/redirecting links.

Classify `ProfessionalLink` vs `MediaAsset`; do not merge merely because both are playable.

## 14. Contacts

Inventory person/role vs contact method. Note if parent/agent information exists only as free text. Identify phone normalization state and current public exposure.

Notification recipients stored in Contacts must be flagged for separation rather than preserved as automatic coupling.

## 15. Questionnaires

Inventory:

- variants;
- primary public state;
- selected blocks/items;
- PDFs;
- HTML representations;
- links/QR;
- historical versions if any;
- whether generated PDF is reproducible from source.

Do not fabricate historical revisions from old PDFs without evidence.

## 16. Builder

If legacy builder does not exist, record `ABSENT`; do not invent migration data. If temporary configs exist, classify whether they are historical artifacts or reusable templates.

## 17. Castings

Inventory casting source fields, uploaded images/docs, extracted AI text if any, status, requirements and provenance. Determine which “requirements” are Human-confirmed vs AI-generated.

## 18. Feedback

Inventory incoming messages, sender fields, statuses, attachments, notes, any conversions to Casting, notification-only copies and source contexts.

Email notification copy is not automatically superior to DB message.

## 19. Opportunities

If absent, record ABSENT. Do not infer historical Opportunity stages from Projects unless explicit provenance exists.

## 20. Themes

Inventory current hardcoded/site theme config, tokens, CSS overrides, temporary experiments. Distinguish published theme state from code defaults.

## 21. AI

Inventory:

- AI-generated fields;
- prompts if retained;
- provider metadata;
- source snapshots;
- Human apply history;
- unknown-origin content.

Unknown origin remains unknown.

## 22. Achievements

Inventory recognition text, status, event, year, project/person subject and evidence. Ambiguous `лауреат/participant/award` receives review state.

## 23. Social publishing

Inventory OAuth/account data, scheduled jobs, drafts and provider post IDs. Token existence does not prove validity; local caption does not prove provider publication.

## 24. Help/Tickets

Inventory Help Center articles and technical support records. Ensure professional Feedback is not misclassified as Help Ticket.

## 25. Filesystem inventory

For every media root:

```text
path
file count/bytes
extensions/MIME observed
orphan files
duplicate hashes
zero-byte/corrupt candidates
symlinks
permissions
```

Never put sensitive full paths in public report, but operational migration report may contain controlled filesystem references.

## 26. Checksums

Compute stable hash for media originals before migration to verify byte preservation.

## 27. Derived artifacts

Classify thumbnails/optimized videos/PDFs/QR/cache/search indexes as rebuildable where true. Do not spend migration complexity preserving derived bytes if canonical source/config can regenerate them.

## 28. Routes inventory

Map legacy URLs to:

```text
KEEP
REDIRECT
REMOVE
ADMIN_ONLY
TOKEN_SCOPED
```

Check public backlinks/canonical SEO implications.

## 29. Configuration inventory

Document variable names and purpose, not secret values:

```text
DATABASE_URL
AI provider config
notification provider config
social OAuth IDs
storage paths
base/public URLs
```

## 30. Background/cron inventory

Find every scheduler/cron/script. Classify:

```text
SAFE_REBUILD
EXTERNAL_SIDE_EFFECT
OBSOLETE
REQUIRES_REVIEW
```

No legacy cron resumes after cutover until explicitly mapped.

## 31. Operational data inventory

Jobs/outbox/delivery attempts can cause side effects. Record pending/retry/unknown states separately and define migration disposition.

## 32. Duplicate inventory

Detect candidates by stable keys/hash/name/date but report as candidates. Automatic merge only under explicit deterministic rule.

## 33. Broken references

List missing FK-like links/file paths/URLs. Do not silently discard records; determine whether repair, archive, tombstone or review.

## 34. Inventory report structure

```text
Executive summary
Source versions
Counts/sizes
Per-domain findings
Ambiguities
Data quality defects
Security/privacy findings
Migration mapping table
Review queue
Blocking issues
Recommended order
```

## 35. Security

Inventory export is private. Redact:

- passwords;
- access tokens;
- recovery/session secrets;
- raw provider credentials.

## 36. Freeze/version

Inventory must record source commit/schema/data snapshot timestamp. Otherwise later changes make counts/mappings unverifiable.

## 37. Acceptance criteria

`AC-INV161-001` All DB tables/files/routes/config/jobs are inventoried before migration.  
`AC-INV161-002` Inventory records source version/timestamp.  
`AC-INV161-003` Professional mappings have semantic confidence.  
`AC-INV161-004` Unknown/ambiguous values remain explicit.  
`AC-INV161-005` Media originals have checksum/count/size inventory.  
`AC-INV161-006` Operational side-effect jobs/providers are identified.  
`AC-INV161-007` Contacts/notifications, media/links, Feedback/help and Project/Training boundaries are reviewed.  
`AC-INV161-008` Legacy AI provenance is not fabricated.  
`AC-INV161-009` Duplicate/broken-reference candidates are reported, not silently repaired.  
`AC-INV161-010` Inventory report contains no raw secrets.  
`AC-INV161-011` Each source item/domain has migration disposition.  
`AC-INV161-012` Blocking ambiguities are converted to Human review queue.

---

## 38. Финальная доктрина

> **Inventory is evidence, not migration code. It records every relevant schema, file, relationship, route, background side effect and semantic uncertainty against a frozen source version, enabling the migration to preserve what is known and explicitly isolate what is not.**
