# CODEX EXECUTION PLAN
## DOC-223 — master phased instructions for implementing the actress website from canonical documentation

**Статус:** Canonical / Required

---

## 1. Назначение

Этот документ предназначен для прямого использования как master execution contract в Codex. Он не заменяет профильные DOC/FR/AC, а задаёт способ их поэтапной реализации в существующем репозитории.

> **Codex must inspect the actual code before editing, implement one dependency-bounded phase at a time, preserve existing working behavior and never fill documentary gaps by inventing professional facts, access rules or provider authority.**

---

# 2. Master instruction for Codex

Use the following operational instruction:

```text
You are implementing the actress website according to the canonical documentation.

Before every implementation phase:
1. Inspect the current repository, package/runtime versions, DB schema/migrations, routes, server actions, tests and deployment configuration.
2. Identify what already exists, what conflicts with the target documentation and what can be reused safely.
3. Read the mandatory DOC/FR/AC references for the current phase.
4. Do not assume a path/entity/API exists merely because the documentation proposes one.
5. Preserve existing working production behavior outside the approved scope.

Hard constraints:
- PostgreSQL remains authoritative for structured Source.
- Media originals remain immutable.
- Hidden/private data is removed before serialization.
- Save != Publish; Generate != Apply; Apply != Publish/Send.
- AI/VOP/Analytics never receive autonomous professional/business authority.
- External provider calls never execute inside authoritative DB transactions.
- Durable side effects use outbox/jobs/idempotency/reconciliation.
- Historical Revision/Snapshot content is immutable.
- Unknown legacy/professional data must remain unknown/review-required; never invent defaults.
- No production secret may enter source, tests, logs, browser bundles or GitHub output.

For every phase:
- implement schema/migration first where required;
- implement domain/application contracts;
- implement server projections/actions;
- implement UI;
- implement worker/provider integration only after durable intent exists;
- add unit/integration/E2E/security tests;
- update documentation/traceability;
- run all affected quality gates;
- report exact files changed, migrations, tests, unresolved gaps and FR/AC IDs.

Do not proceed to a dependent phase while a blocking authority, privacy, migration or data-integrity gap remains unresolved.
```

---

# 3. Phase 0 — Actual repository audit

Mandatory docs:

```text
DOC-200 implementation strategy
DOC-201 repository structure
DOC-202 coding standards
DOC-206 sequencing
DOC-230 audit model when available
```

Tasks:

1. inventory current source tree;
2. identify Next.js/runtime/package versions;
3. locate PostgreSQL schema/migrations/ORM/query layer;
4. identify media storage paths;
5. enumerate public/Admin/API routes;
6. locate auth/session implementation;
7. identify existing AI/provider integrations;
8. run build/typecheck/tests;
9. identify current documentation/config/deployment paths;
10. produce actual gap report before refactor.

No broad file moves during audit.

---

# 4. Phase 1 — Security/application foundations

Mandatory:

```text
DOC-140…146
DOC-150
DOC-162
DOC-170…174
DOC-203
DOC-205
```

Implement/refactor:

- config schema/server-only secret accessor;
- structured errors/logging/correlation;
- Admin authentication/session lifecycle;
- server-side authorization/profile scope;
- Audit;
- Outbox;
- idempotency infrastructure;
- optimistic version patterns;
- durable job tables/worker registry;
- secure upload/URL primitives.

Gate:

- anonymous/Admin boundary tests;
- transaction/outbox integration;
- secret scan;
- worker idempotency.

---

# 5. Phase 2 — Core professional domains

Mandatory module documents:

```text
Profile
Media Library
Portfolio
Projects
Training
Skills/Languages
Contacts
Achievements
```

Use `FR-PRO`, `FR-MED`, `FR-POR`, `FR-PRJ`, `FR-TRN`, `FR-SKL`, `FR-LNG`, `FR-CNT`, `FR-ACH`.

Tasks:

- normalize ownership;
- create migrations/backfills;
- immutable media original storage;
- independent visibility triplets;
- controlled dictionaries/statuses;
- Admin CRUD/projections;
- public projections;
- migration review queues for ambiguity.

Gate: manual Profile/Portfolio works with AI/providers completely disabled.

---

# 6. Phase 3 — Emotional Portfolio/Grid

Implement exact grid presets, shooting date, immutable originals, crop configuration, Human confirmations and derived outputs.

Must not:

- mix emotional data into Projects;
- generatively alter appearance;
- fabricate cell config from flattened legacy collage.

Gate: exact count/confirmation tests + public/QNR projection semantics.

---

# 7. Phase 4 — Professional links and stable media delivery

Implement:

- ProfessionalLink vs MediaAsset separation;
- canonical safe URLs;
- application-controlled media routes;
- SSRF-safe link health;
- provider embed adapters;
- exact `№ / Описание / URL` editor.

Gate: no raw storage path/public unsafe URL.

---

# 8. Phase 5 — Questionnaires, PDF and QR

Mandatory:

```text
Questionnaires
PDF architecture
QR Links
FR-QNR / FR-QR
```

Implement:

- Draft/configuration;
- readiness;
- immutable Revision;
- exactly one primary public;
- normalized DocumentModel;
- HTML/PDF renderer;
- clickable annotations;
- exact verified QR;
- artifact cache keyed by revision/renderer settings.

Gate: historical revision remains byte/semantic stable after Source edits; PDF/QR E2E passes.

---

# 9. Phase 6 — Public Questionnaire Builder

Implement eligibility projection/server filtering before public UI.

Requirements:

- QUICK/STANDARD/EXTENDED/CASTING;
- no mandatory registration baseline;
- temporary session/TTL;
- mandatory identity + Main Portfolio Close-Up/Full Body/Profile URL;
- Generate revalidation;
- immutable GenerationSnapshot;
- scoped artifact access;
- rate limit/anti-abuse.

Gate: forged hidden IDs reveal nothing and cannot generate.

---

# 10. Phase 7 — Feedback and Notifications

Implement Feedback authoritative transaction first.

Transaction:

```text
Feedback
+ attachment binding
+ Audit
+ Outbox
COMMIT
```

Only then notifications.

Add:

- read/workflow separation;
- notes/assignee/next action;
- private attachments;
- In-App center;
- explicit recipients/endpoints/preferences;
- Email/official WhatsApp Business adapters;
- quiet hours/retry/idempotency.

Gate: with every external provider deliberately failing, public Feedback still succeeds and Admin Inbox contains record.

---

# 11. Phase 8 — Castings and Opportunity Pipeline

First implement deterministic Human workflow:

- Casting Source/provenance;
- Human requirements/materials;
- explicit Feedback→Casting;
- Opportunity current stage + append-only history;
- validated Human transitions;
- next action/priority/owner;
- explicit BOOKED→Project conversion.

No AI yet required.

Gate: complete professional workflow works manually.

---

# 12. Phase 9 — Casting AI

Implement AI gateway first if absent, then explicit casting analysis:

- immutable minimized SourceSnapshot;
- prompt/schema version;
- evidence-backed extraction;
- missing=`Не указано`;
- canonical match statuses;
- immutable AnalysisRevision;
- Human confirmation only;
- stale handling;
- Questionnaire Draft recommendation only.

Gate: malicious prompt/source cannot gain tool authority; provider disabled leaves manual workflow fully functional.

---

# 13. Phase 10 — BB Assistant

Implement:

- explicit task types;
- BBSourceSnapshot;
- factual claim validation;
- Draft revisions/provenance;
- Human edits;
- explicit idempotent Apply;
- target optimistic concurrency.

Prove:

```text
Generate != Apply != Publish != Send
```

Gate: invented award/Skill/language/availability/commercial term is blocked/warned according to target policy.

---

# 14. Phase 11 — Site Themes

Implement manual structured theme workflow first:

- tokens/config;
- responsive preview;
- locks;
- contrast/accessibility validation;
- immutable published revision;
- explicit activate/rollback/temporary theme.

Then add Theme AI as Draft generator only.

Gate: Theme cannot change professional content/access; accessibility blocker prevents activation.

---

# 15. Phase 12 — VOP

Implement deterministic checks/readiness integration and Findings before any AI explanation.

Safe registry may include:

- reindex;
- cache invalidation;
- derivative/PDF/QR rebuild;
- retry declared idempotent technical job.

Hard deny list includes publication, Source fact edits, Casting confirmation, Opportunity stage, Social publish, professional replies.

Gate: direct API/config attempt to execute forbidden VOP action fails server-side.

---

# 16. Phase 13 — Help Center & Tickets

Implement RU Help Center and operational tickets:

- category/priority/lifecycle;
- assignment/SLA;
- Public Reply vs Internal Note;
- private scanned attachments;
- VOP relation;
- notifications.

Gate: technical Ticket never becomes professional Feedback/Opportunity and Internal Note never reaches requester payload.

---

# 17. Phase 14 — Social Publishing

Prerequisites must already exist: security/secrets, media eligibility/rights, BB, durable jobs, notifications.

Implement:

- Instagram/TikTok adapters;
- OAuth state/PKCE/scopes/encrypted credentials;
- SocialPostDraft;
- media rights/embargo validation;
- Human approval fingerprint;
- immutable PublishSnapshot;
- durable exact-time schedule;
- PublishAttempt;
- provider webhook/reconciliation;
- UNKNOWN_OUTCOME behavior;
- token refresh/revoke.

Gate: edit after approval invalidates approval; duplicate worker does not duplicate post; timeout reconciles before retry.

---

# 18. Phase 15 — Marketing Analytics

Only after domain event/provenance contracts are stable.

Implement:

- versioned event registry;
- client/server event authority;
- first-party sessions;
- bot/Admin/non-prod filtering;
- UTM/referrer minimization;
- ShareLinks;
- Feedback/Casting/Opportunity funnels by domain provenance;
- aggregates/data-quality dashboard;
- bounded retention.

Gate: client cannot forge Booked/Feedback business event; analytics outage blocks no professional workflow.

---

# 19. Phase 16 — Production hardening

Apply DOC-150…155, DOC-170…179 and DOC-190…196:

- performance budgets;
- WCAG testing;
- browser matrix;
- SEO;
- CSP/headers;
- security tests/scans;
- backup automation + restore rehearsal;
- monitoring/alerts;
- runbooks;
- deployment process.

---

# 20. Phase 17 — Migration/cutover

When legacy/current production data requires migration:

1. complete DOC-161 inventory;
2. dry-run DOC-160…165 migration;
3. preserve/checksum originals;
4. review ambiguous data;
5. backup;
6. controlled cutover;
7. validate negative privacy tests;
8. resume workers in safe order;
9. post-cutover observation.

Never infer missing professional data to satisfy schema.

---

# 21. Phase 18 — Final audit

Use DOC-230…234:

- compare actual repository against all canonical requirements;
- mark `VERIFIED / IMPLEMENTED_UNVERIFIED / PLANNED / BLOCKED`;
- do not claim absent code exists;
- create residual issues for remaining gaps;
- finalize Technical Specification/index.

---

# 22. Codex per-task completion response

Codex must return:

```text
1. Summary
2. Requirements implemented (FR/AC)
3. Files changed
4. DB migrations/backfills
5. Tests added and exact results
6. Security/privacy impact
7. Provider/side-effect behavior
8. Documentation/traceability updates
9. Remaining gaps/blockers
```

Never return “done” without stating tests/results.

# 23. Rules for uncertainties

If existing repository conflicts with documentation:

- preserve evidence;
- identify exact conflict;
- follow canonical governance precedence;
- implement compatibility/migration if safe;
- do not delete data to force target model;
- record unresolved conflict if specification itself ambiguous.

# 24. Rules for external research

Implementation may consult official primary documentation for current framework/provider APIs because these change over time. Security/provider assumptions must be verified against current official docs during implementation rather than relying on stale memory.

# 25. Rules for generated documentation

Codex may update actual traceability/code path references after implementation, but must not rewrite product doctrine merely to match easier code.

# 26. Stop conditions

Stop the current implementation slice and report blocker if:

- requested operation would expose private data;
- migration mapping requires invented professional fact;
- provider behavior makes side effect outcome unknown with no safe reconciliation path;
- existing production data would be destructively lost;
- canonical docs materially conflict and governance does not resolve them;
- required secret/credential is missing for actual integration test (use fake/manual path rather than fabricate).

# 27. Anti-patterns

Codex MUST NOT:

1. Replace architecture with Supabase because convenient.
2. Turn project into microservices without requirement.
3. Auto-publish AI output.
4. Use public Contacts as notification recipients automatically.
5. Put raw storage path in URL/PDF/QR.
6. Guess CEFR/Skill/Achievement status during migration.
7. Let Builder receive full Admin Source.
8. Call Email/WhatsApp/Social inside DB transaction.
9. Retry UNKNOWN_OUTCOME blindly.
10. Rewrite immutable historical revisions after current Source changes.
11. Commit secrets/test with production data.
12. Mark requirement VERIFIED without test evidence.

# 28. Acceptance criteria

`AC-CODEX-001` Codex inspects actual repository before every major phase.  
`AC-CODEX-002` Work is divided by dependency-aware phases/small PR-sized tasks.  
`AC-CODEX-003` Each task references canonical DOC/FR/AC.  
`AC-CODEX-004` Manual deterministic workflow precedes AI/provider automation.  
`AC-CODEX-005` Schema/migration/domain precede dependent UI.  
`AC-CODEX-006` Tests/security/docs/observability are delivered with feature, not deferred.  
`AC-CODEX-007` Unknown legacy/professional data is never invented.  
`AC-CODEX-008` External side effects use durable explicit intent/idempotency/reconciliation.  
`AC-CODEX-009` Codex reports exact changed files/tests/gaps rather than unsupported completion claims.  
`AC-CODEX-010` Final verification is based on actual repository evidence.  
`AC-CODEX-011` Current provider/framework behavior is verified against primary documentation when implementation depends on it.  
`AC-CODEX-012` Blocking authority/privacy/data-loss uncertainty stops the affected slice rather than being papered over.

---

## 29. Финальная доктрина

> **Codex should treat this documentation as an executable architecture contract, but treat the repository as evidence of current state. It audits first, implements foundations before dependents, keeps manual workflows independent from AI/providers, couples every change to migrations/tests/traceability and refuses to invent missing data or authority simply to make a feature appear complete.**
