# CODEX ENTERPRISE IMPLEMENTATION PASS
## DOC-225 — единая Enterprise-задача для реализации полного документального стека

**Статус документации:** Canonical / Required Execution Contract  
**Статус реализации:** NOT_STARTED until executed against the actual application repository  
**Target:** actual actress-website application repository, not `CerebrumYK/documents`

---

# 1. Назначение

Этот документ является **готовой отдельной master-задачей для Codex**.

Её цель — реализовать **весь canonical documentation stack проекта единым управляемым Enterprise-проходом**, не оставляя модули, интеграции, security, migrations, tests или operations как несвязанные последующие работы.

> **“One Enterprise pass” means one uninterrupted end-to-end implementation campaign with one target architecture, one canonical technology stack, one traceability model and one final integrated acceptance. It does NOT mean one unsafe mega-commit or bypassing dependency gates. Codex executes the campaign phase-by-phase, creates atomic checkpoints, runs blocking validation continuously and continues automatically to the next phase whenever the current gate passes.**

Codex must not require the user to re-request every documented module separately.

---

# 2. Ready-to-use Codex task

Use the following instruction as the master request to Codex:

```text
Implement the entire canonical actress-website documentation stack as one coordinated Enterprise implementation campaign.

AUTHORITATIVE DOCUMENTATION REPOSITORY
CerebrumYK/documents

UNIVERSAL INTERFACE SOURCE
CerebrumYK/GPT-Skill-Enterfaces
pinned ref: 598677eb16196398f0b563ed039bff341a5cf197

PRIMARY OBJECTIVE
Bring the actual application repository from its verified current state to full conformance with the canonical documentation, using exactly one coherent production technology stack and producing complete code, migrations, tests, runtime verification, security evidence, operational artifacts and traceability.

THIS IS ONE ENTERPRISE PASS
Do not stop after implementing only one screen, module or phase.
Do not ask the user to submit a new task after every successful phase.
Continue through all dependency phases automatically while their exit gates pass.
Stop only for a true blocker that cannot be safely resolved from repository evidence, canonical governance, official primary technical documentation or available test/sandbox infrastructure.

ONE PASS DOES NOT MEAN ONE MEGA-COMMIT
Use dependency-aware phases, atomic commits/checkpoints and reviewable internal slices.
Do not collapse schema migration, all modules, providers and deployment into an unreviewable change with no intermediate verification.
The campaign is singular; the engineering checkpoints remain safe and auditable.
```

---

# 3. Mandatory documentation read order

Before implementation, Codex MUST read in this order:

```text
DOC-003  docs/governance.md
DOC-207  docs/implementation/implementation-status.md
DOC-237  docs/final/enterprise-consistency-audit.md
DOC-233  docs/final/final-technical-specification.md
DOC-208  docs/implementation/unified-technology-stack.md
DOC-225  docs/github/codex-enterprise-implementation-pass.md
DOC-223  docs/github/codex-execution-plan.md
DOC-206  docs/implementation/implementation-sequencing.md
DOC-180  docs/requirements/functional-requirements.md
DOC-181  docs/requirements/acceptance-criteria.md
DOC-182  docs/requirements/requirements-traceability.md
```

Then read all owning documents applicable to every phase:

```text
DOC-001 / Product / Journey / UX
DOC-060…134 Domain / Architecture / Database / API / Modules
DOC-140…146 Security
DOC-150…156 NFR and Interface Quality
DOC-160…165 Migrations
DOC-170…179 Testing and Release Gates
DOC-190…196 Operations
DOC-200…208 Implementation
DOC-220…225 GitHub/Codex
DOC-230…237 Final/Audit/Traceability
```

For rendered UI work additionally read and apply the pinned `GPT-Skill-Enterfaces` integration contract from DOC-156/DOC-224.

---

# 4. Precedence

When documentation appears to conflict:

```text
DOC-003 governance
→ Security/privacy hard constraints
→ owning Domain/Module semantics
→ cross-cutting Domain/Architecture/DB/API
→ DOC-208 concrete implementation technology
→ master FR/AC
→ DOC-233 consolidation
→ implementation/operations/process docs
→ audit/index
```

Do not resolve conflict by choosing the easiest implementation.

If ambiguity remains after applying precedence, document the exact conflict and block only the affected slice. Continue independent work when safe.

---

# 5. Canonical technology stack is mandatory

Implement against DOC-208.

The final system MUST converge to one baseline:

```text
Next.js / React / strict TypeScript
Node.js LTS pinned
pnpm pinned + one lockfile
PostgreSQL
Drizzle ORM + node-postgres
one drizzle-kit/reviewed-SQL migration chain
Zod
PostgreSQL opaque Admin sessions + Argon2id
PostgreSQL Outbox + durable Jobs / SKIP LOCKED
PostgreSQL FTS + pg_trgm
Next.js/application cache; no Redis baseline
filesystem media under /data/kate-actor
Sharp
FFmpeg / ffprobe
Playwright-managed Chromium PDF rendering
qrcode + independent ZXing verification
CSS Modules + semantic CSS Custom Property tokens
React Hook Form + Zod
Vitest
Playwright
axe-core
Lighthouse CI
Pino
OpenTelemetry
Docker Compose
host Nginx / HTTPS
GitHub Actions
```

Do not introduce parallel Prisma/Knex/TypeORM, Redis/BullMQ, external search engine, S3/MinIO, second styling framework, Jest/Cypress or another schema framework as an alternative production path.

If the repository already uses a conflicting technology, Phase 0 must classify it and migrate it, or isolate it behind a temporary compatibility adapter with a concrete removal condition. Enterprise DONE requires the parallel stack to be removed unless an explicit approved ADR changes DOC-208.

---

# 6. Phase 0 — Enterprise discovery and baseline lock

Before broad edits:

1. inventory the actual repository;
2. identify current branch/release/deployment state;
3. identify Node/Next/React/TypeScript/package manager versions;
4. identify DB schema, migrations, ORM/query libraries and production database;
5. identify all runtime/public/Admin/API routes;
6. identify auth/session implementation;
7. inventory media originals/derivatives/storage paths;
8. identify jobs/queues/cache/search;
9. identify AI/provider/OAuth/webhook code;
10. identify existing UI/design system/styling;
11. identify tests/CI/security scans;
12. identify production process manager/container topology/Nginx;
13. identify backup/monitoring/restore/runbooks;
14. run the existing build/typecheck/lint/tests;
15. create an evidence-backed gap matrix against every FR family.

Output statuses:

```text
ABSENT
PARTIAL
LEGACY_CONFLICT
IMPLEMENTED_UNVERIFIED
VERIFIED
DEFERRED_NON_BASELINE
```

Never mark VERIFIED from filenames or screenshots alone.

### Phase 0 mandatory decisions

Before proceeding, pin exact supported versions for DOC-208 runtime/tooling using current official primary documentation and repository compatibility evidence.

Record them in:

- package manager metadata;
- lockfile;
- Node/runtime config;
- Docker/container manifests;
- PostgreSQL image/major;
- interface skill pin.

Do not use floating `latest`.

---

# 7. Phase 0.5 — Repository and interface foundations

Reconcile repository layout toward DOC-201 without destructive cosmetic restructuring.

Materialize/verify:

```text
AGENTS.md
DESIGN.md
docs/interface-project-profile.md
.interface-skills-version.json
.agents/skills/<nine pinned skills>
interface skill sync/verify/drift tooling
interface change detector
interface quality orchestration
```

Use pinned `GPT-Skill-Enterfaces@598677...` exactly.

Project-specific business/design rules remain in the application repository; never modify vendored generic skill bodies to encode actress-specific semantics.

---

# 8. Phase 1 — Security and shared authority foundations

Implement completely:

- typed IDs/results/errors;
- environment/config validation with Zod;
- server-only secret accessor;
- Pino logging/correlation;
- OpenTelemetry instrumentation;
- PostgreSQL/Drizzle transaction layer;
- migrations;
- first-party Admin accounts/sessions;
- Argon2id credentials;
- session expiry/rotation/revocation;
- authorization/profile scope;
- CSRF/origin protection;
- CSP/security headers;
- Audit;
- Outbox;
- idempotency store;
- optimistic concurrency/version pattern;
- PostgreSQL durable jobs/worker registry;
- rate-limit primitives;
- secure upload quarantine;
- URL/SSRF policies.

### Blocking gate

Do not proceed if anonymous/Admin separation, session security, public/private projection, transaction/outbox or secret-safety tests fail.

---

# 9. Phase 2 — Core professional Source

Implement all owning domains, schema, migrations, Admin UX and projections:

```text
Profile
Media Library
Main Portfolio
Projects / Roles / Performances
Training
Skills / Languages
Contacts
Achievements
Professional Media / Links
```

Required outcomes include:

- one owner per fact;
- independent visibility flags;
- exact Skill 1..5;
- exact Language NATIVE/A1…C2;
- exact Achievement recognition semantics;
- immutable media originals;
- stable application-controlled public media URLs;
- no invented migrated values;
- migration review queues for ambiguity.

### Gate

Core Profile/Portfolio/Contacts/Projects work with every AI/provider integration disabled.

---

# 10. Phase 3 — Emotional Portfolio / Grid

Implement the complete Emotional domain:

- mandatory shooting date;
- immutable source photos;
- public Emotional Portfolio;
- exact supported grid presets;
- exact cell count;
- per-cell crop/pan/scale/rotation;
- >=90% useful face-area target workflow;
- Human confirmation per cell;
- master/web/PDF/thumb derivatives;
- multiple grids and primary marker.

No beauty/generative appearance modifications.

---

# 11. Phase 4 — Questionnaire / Document / QR foundation

Implement fully:

```text
Questionnaire COMPACT / EXTENDED / CASTING / CUSTOM
QuestionnaireDraft
readiness
immutable QuestionnaireRevision
exactly one primary public questionnaire
DocumentModel
HTML renderer
Playwright/Chromium PDF renderer
clickable PDF links
qrcode generation
independent ZXing exact-decode verification
artifact cache/invalidation
```

Mandatory Full Body + Close-Up come only from eligible Main Portfolio.

### Gate

Edit current Profile after publication and prove historical HTML/PDF/QR remains frozen.

---

# 12. Phase 5 — Public Questionnaire Builder

Implement complete no-mandatory-registration baseline:

- QUICK/STANDARD/EXTENDED/CASTING templates;
- Builder Eligibility Projection;
- temporary opaque session/TTL;
- preview;
- current eligibility revalidation;
- immutable BuilderGenerationSnapshot;
- protected/scoped artifact access;
- rate limit/anti-abuse;
- mandatory identity/Close-Up/Full Body/Profile URL.

Forged hidden IDs must reveal no candidate data and cannot generate.

---

# 13. Phase 6 — Feedback / Notifications

Implement Feedback transaction first:

```text
Feedback
+ staged/private attachment binding
+ Audit
+ Outbox
COMMIT
```

Then implement:

- Inbox;
- read/workflow separation;
- assignment/notes/next action;
- NotificationIntent;
- In-App;
- Email;
- official WhatsApp Business;
- recipient/endpoints/preferences;
- quiet hours;
- attempts/receipts;
- retry/reconciliation.

### Gate

All external notification providers deliberately fail and Feedback still commits successfully and remains usable in Admin.

---

# 14. Phase 7 — Castings / Opportunity

Implement Human-controlled deterministic workflow before AI:

- private Casting Source/provenance;
- source materials;
- CastingRequirement Human lifecycle;
- explicit Feedback→Casting;
- Opportunity stages/history;
- allowed transitions/skips;
- next action/owner/priority;
- terminal/reopen/correction semantics;
- explicit BOOKED;
- explicit BOOKED→Project/Role Draft conversion.

No AI may control stage/outcome.

---

# 15. Phase 8 — AI architecture / Casting AI

Implement one shared provider gateway with separate capability contracts.

Casting AI:

- explicit invocation;
- minimized immutable SourceSnapshot;
- prompt/schema version;
- evidence-backed extraction;
- missing=`Не указано`;
- canonical match statuses;
- immutable AnalysisRevision;
- Human confirmation;
- stale detection;
- Questionnaire Draft recommendation only.

Prompt injection cannot grant tool/business authority.

---

# 16. Phase 9 — BB Assistant

Implement every documented writing task using:

```text
Confirmed Source + Human context
→ immutable BBSourceSnapshot
→ AIDraft
→ factual/schema validation
→ Human review/edit
→ explicit Apply
```

Prove:

```text
Generate != Apply != Publish != Send
```

Draft cannot invent awards, skills, languages, availability, fees, representation, project credits or URLs.

---

# 17. Phase 10 — Themes + interface-system completion

Implement manual structured Theme system first:

- typed semantic tokens;
- CSS custom-property output;
- component presets;
- Hero/decor/motion/responsive settings;
- locks;
- contrast/accessibility validation;
- immutable published ThemeRevision;
- separate Activate/Rollback/temporary activation.

Then Theme AI may create structured Draft proposals only.

For substantial UI changes execute the full DOC-156/DOC-224 lifecycle:

```text
interface-build
→ Design Preflight
→ implementation
→ runtime/browser evidence
→ structured critique
→ better-interface full
→ repair HIGH/MEDIUM
→ interface-review
→ repair Introduced/Regression HIGH/MEDIUM
→ deterministic gates
```

No Tailwind/MUI/shadcn second design system may be added as shortcut around DOC-208.

---

# 18. Phase 11 — VOP

Implement deterministic checks first:

- Finding/evidence/fingerprint/lifecycle;
- readiness checks;
- SafeAction registry;
- hard business-authority deny list;
- technical reindex/cache/derivative/PDF/QR/job retry actions only where idempotency is proven.

AI explanation is optional/advisory only.

Direct API/config attempts to enable forbidden business mutation must fail server-side.

---

# 19. Phase 12 — Help Center / Support Tickets

Implement:

- RU Help Center;
- article lifecycle/search;
- Ticket category/priority/lifecycle;
- assignment/SLA;
- Public Reply vs Internal Note;
- private scanned attachments;
- VOP relation;
- support notifications;
- strict separation from Feedback/Casting/Opportunity.

Internal Notes must be absent before requester serialization.

---

# 20. Phase 13 — Social Publishing

Implement complete Instagram/TikTok workflow:

- least-privilege OAuth state/PKCE;
- encrypted token reference/lifecycle;
- Draft;
- media social-rights/embargo eligibility;
- BB caption provenance;
- Human approval fingerprint;
- immutable PublishSnapshot;
- durable exact-time schedule;
- PublishAttempt;
- async processing states;
- signed/deduplicated webhooks;
- polling/reconciliation;
- UNKNOWN_OUTCOME;
- token refresh/revoke;
- missed-window policy.

Duplicate worker execution cannot duplicate provider publication.

Missing live credentials do not justify fake provider success: complete adapter/fake/sandbox verification and keep live-provider status NOT_VERIFIED until evidence exists.

---

# 21. Phase 14 — Marketing Analytics

Implement after business events stabilize:

- versioned Event Registry;
- client interaction vs server committed business events;
- first-party bounded session;
- bot/Admin/non-prod classification;
- URL/referrer minimization;
- UTM campaign model;
- ShareLinks;
- first/last touch;
- Feedback→Casting→Opportunity actual-history funnel;
- Builder funnel;
- aggregates;
- data-quality metrics;
- bounded raw retention;
- advisory-only AI insights.

Browser cannot emit authoritative BOOKED/Feedback/Casting conversion events.

---

# 22. Phase 15 — Search / cache / performance consolidation

Complete the single-stack implementations:

```text
PostgreSQL FTS + pg_trgm
public/admin/builder search projections
Next.js/application generation cache
bounded in-process technical cache only
no Redis
no external search engine
```

Run query-plan/index tests, cache revocation/privacy tests and public performance budgets.

---

# 23. Phase 16 — Full migration and legacy cutover

Run DOC-160…165 against actual data:

1. complete inventory;
2. classify legacy schemas/files;
3. checksum media originals;
4. generate review queues for ambiguous facts;
5. perform rehearsal on copy;
6. validate row counts/references/visibility;
7. backup;
8. controlled migration;
9. negative privacy tests;
10. provider/job reconciliation;
11. rollback rehearsal where applicable.

Never manufacture Skill, CEFR, Achievement status, visibility or historical revision to make migration green.

---

# 24. Phase 17 — Enterprise operations

Materialize actual production operations, not generic placeholders:

- Docker Compose manifests for web/worker/PostgreSQL;
- pinned images/runtime;
- host Nginx HTTPS config;
- `/data/kate-actor` persistent mounts/permissions;
- startup/readiness/liveness;
- migration command/lock;
- backup automation;
- restore automation/rehearsal;
- monitoring/alerts;
- Pino/OpenTelemetry pipeline;
- incident/runbooks;
- maintenance procedures;
- log retention;
- secret rotation;
- provider degraded-state handling.

### RPO/RTO gate

DOC-193 intentionally does not invent RPO/RTO. During this phase determine realistic values from actual backup/recovery infrastructure, record them in operations configuration/runbooks and prove them with a timed restore rehearsal before Enterprise DONE.

---

# 25. Phase 18 — Complete quality campaign

Run the entire test matrix, not only changed-module tests.

## Unit

Vitest for:

- domain invariants;
- state machines;
- validation;
- matching/readiness;
- QR/link policies;
- factual validators;
- idempotency fingerprints.

## Integration

Real PostgreSQL + filesystem/provider fakes for:

- migrations/constraints;
- transactions/outbox;
- concurrent races;
- SKIP LOCKED worker leases;
- idempotency;
- immutable revisions;
- media derivatives;
- PDF/QR;
- webhook dedupe;
- provider unknown outcomes.

No SQLite substitute.

## E2E

Playwright against assembled application for all critical public/Admin/Builder/token/provider-simulated journeys.

## Security

Include negative tests for:

- auth bypass;
- CSRF;
- IDOR/cross-profile;
- hidden-data serialization;
- token scope/expiry/revocation;
- SSRF;
- malicious upload;
- XSS/HTML injection;
- OAuth state;
- webhook signature/replay;
- secrets in client/logs;
- provider-side-effect duplication.

## Accessibility

- axe-core automated scans;
- keyboard-only flows;
- focus management;
- zoom/reflow;
- screen-reader critical paths;
- reduced motion;
- contrast;
- PDF/link alternatives to QR.

## Compatibility

Playwright Chromium/Firefox/WebKit plus documented mobile/reflow checks.

## Visual

Representative public/Admin/Builder/Theme states and long RU/EN content.

## Performance

- Lighthouse CI public paths;
- controlled Playwright measurements;
- DB query plans/hot queries;
- upload/media generation resource bounds;
- worker backlog/load behavior.

## Migration

- empty/fresh DB;
- legacy fixture;
- repeated idempotent migration where applicable;
- rollback/recovery path;
- media checksums;
- ambiguity queue.

## Restore

Perform an isolated restore rehearsal with provider sending disabled, reconcile pending jobs, rebuild derived state and verify public/Admin/security smoke before enabling workers.

---

# 26. Phase 19 — Static and supply-chain gates

Run and record:

```text
pnpm install --frozen-lockfile
format check
ESLint
tsc/typecheck
Next.js production build
Vitest full suite
Playwright full suite
accessibility suite
Lighthouse/performance gates
migration verification
secret scan
CodeQL/static security
production dependency audit
container image scan
license review where required
```

Exact commands must be taken from the actual implementation repository after they are materialized and recorded in the final report.

---

# 27. Phase 20 — Full traceability closure

Create/populate machine-readable traceability in the implementation repository:

```text
docs/traceability/requirements.yaml
```

or an explicitly canonical equivalent.

For **every FR** record:

```yaml
id:
owner_docs:
status:
implementation_paths:
migration_ids:
unit_tests:
integration_tests:
e2e_security_nfr_tests:
acceptance_ids:
issue_pr_evidence:
notes:
```

No release-critical FR may remain without implementation and test evidence at Enterprise acceptance.

Also detect:

- tests with no requirement link;
- FR with no tests;
- public/provider FR with no security evidence;
- schema FR with no migration evidence/rationale.

---

# 28. Phase 21 — Final integrated acceptance

Compare actual implementation against:

```text
all FR-* in DOC-180
all AC-MASTER-* in DOC-181
all owning module AC/INV/E2E requirements
DOC-140…146 Security
DOC-150…156 NFR/UI
DOC-160…165 Migration
DOC-170…179 Testing/Gates
DOC-190…196 Operations
DOC-208 Unified Stack
```

Enterprise completion is not allowed merely because individual modules report green.

All system-wide invariants must hold simultaneously.

---

# 29. Enterprise DONE definition

The campaign may declare `ENTERPRISE_IMPLEMENTATION_COMPLETE` only if all applicable conditions are true:

```text
one canonical production technology stack
all baseline functional modules implemented
all required DB schema/migrations applied/rehearsed
no duplicate professional Source ownership
manual workflows operate with AI/providers disabled
security/privacy trust boundaries verified
AI remains Draft/Analysis only
provider side effects durable/idempotent/reconciled
immutable historical snapshots proven
media originals checksum/immutability proven
PDF hyperlinks/selectable text/QR exact decode proven
Public Builder hidden-data negative tests pass
Social UNKNOWN_OUTCOME duplicate prevention proven
full unit/integration/E2E/security suites green
accessibility blocking findings = 0
performance blocking regressions = 0
supported-browser critical smoke green
migration/recovery/restore rehearsal green
backup RPO/RTO documented and demonstrated
monitoring/alerts/runbooks operational
production deployment smoke green
machine-readable FR/AC traceability complete
P0/P1 unresolved implementation gaps = 0
no known blocking security/privacy/data-loss defects
no floating production dependencies
no unauthorized parallel implementation stack
```

A missing live third-party credential may leave only that live provider integration `IMPLEMENTED_UNVERIFIED`; it must be explicitly reported and cannot be silently counted as VERIFIED. If that provider is required for the declared production baseline, final production acceptance remains blocked until safe live/sandbox evidence is supplied according to its provider contract.

---

# 30. Stop conditions

Stop only the affected dependent work and report a blocker when:

- canonical docs remain genuinely contradictory after DOC-003 precedence;
- migration would require inventing professional facts;
- existing data would be irreversibly destroyed with no validated migration/backup path;
- security/privacy invariant cannot be met;
- provider side effect is unknown and no reconciliation method exists;
- required production secret is unavailable for a mandatory live verification;
- actual infrastructure makes the documented RPO/RTO impossible and operator decision is required.

Do NOT stop merely because:

- implementation is large;
- the next documented module has not been separately requested;
- one phase finished successfully;
- a manual workflow can be improved later;
- optional AI/provider is temporarily unavailable while deterministic implementation work remains.

---

# 31. Required progress discipline

Within the Codex execution thread/campaign:

- maintain a phase checklist;
- update real status after every gate;
- preserve exact command/test output;
- create atomic commits/checkpoints;
- do not claim later phases completed from intention;
- automatically continue to the next dependency phase when green;
- re-run invalidated upstream tests after shared-contract changes;
- keep the branch deployable or explicitly migration-gated at each milestone.

---

# 32. Git/PR interpretation

DOC-206 forbids an unsafe giant “implement everything” PR.

DOC-225 therefore defines **one Enterprise campaign**, not necessarily one enormous review unit.

Preferred execution:

```text
one Enterprise campaign
→ one coherent integration branch or controlled stacked phase branches
→ atomic phase commits/checkpoints
→ phase validation
→ final integration/rebase
→ full regression
→ final reviewable Enterprise PR/release candidate
```

If repository policy requires separate phase PRs, Codex may use them without waiting for a new user prompt between phases, provided the same campaign/evidence matrix remains continuous.

Never merge known-red intermediate work into production merely to satisfy “one pass”.

---

# 33. Final Codex report

At completion return an evidence report with:

1. exact starting SHA and final SHA;
2. unified stack and pinned versions;
3. modules implemented;
4. legacy technology removed/compatibility adapters remaining;
5. schema and migration list;
6. data migration/review results;
7. test commands and exact pass/fail counts;
8. security scan results;
9. accessibility results;
10. performance results/budgets;
11. browser matrix results;
12. PDF/QR verification results;
13. provider sandbox/live results;
14. restore/RPO/RTO evidence;
15. deployment smoke results;
16. observability/alerts/runbook status;
17. requirement traceability coverage;
18. unresolved gaps by severity;
19. explicit list of any requirement not VERIFIED and why;
20. final release state under DOC-179.

Never summarize as simply “done” without evidence.

---

# 34. Mandatory final review

Before final acceptance:

1. run holistic architecture/security/data ownership review;
2. run complete `better-interface full` on rendered UI scope;
3. repair all confirmed HIGH/MEDIUM findings;
4. run `interface-review` over final change scope;
5. repair Introduced/Regression HIGH/MEDIUM;
6. rerun invalidated runtime/a11y/visual tests;
7. run all DOC-179 blocking gates;
8. update DOC-207 implementation evidence and actual traceability in the implementation repository;
9. produce final residual-gap report.

No unresolved blocking finding is converted to warning solely to complete the campaign.

---

# 35. Acceptance criteria for this Codex task

`AC-ENT225-001` Codex executes one continuous Enterprise campaign covering every baseline documented domain.  
`AC-ENT225-002` Enterprise pass remains dependency-phased and does not become an unsafe mega-commit.  
`AC-ENT225-003` Actual repository evidence is audited before broad refactor.  
`AC-ENT225-004` Final code converges to DOC-208 single technology stack.  
`AC-ENT225-005` Non-canonical legacy technology is removed or explicitly bounded with a removal condition.  
`AC-ENT225-006` Security/DB/domain foundations precede dependent UI/provider work.  
`AC-ENT225-007` Manual deterministic workflows are complete before AI automation becomes required.  
`AC-ENT225-008` Every schema/data change includes tested migration/backfill/reconciliation.  
`AC-ENT225-009` Every provider side effect uses durable intent/idempotency/reconciliation.  
`AC-ENT225-010` All nine interface skills and project design/profile contracts are integrated from the pinned source.  
`AC-ENT225-011` Full unit/integration/E2E/security/accessibility/performance/visual/migration/restore tests run before final acceptance.  
`AC-ENT225-012` No SQLite substitutes PostgreSQL integration behavior.  
`AC-ENT225-013` PDF/QR semantic correctness is automatically and manually verified where required.  
`AC-ENT225-014` Production operations include deployment, monitoring, backup/restore, RPO/RTO and runbooks.  
`AC-ENT225-015` Machine-readable requirement traceability links every release-critical FR to implementation/tests.  
`AC-ENT225-016` Final status of every requirement is evidence-based; no documentation-only item is called implemented.  
`AC-ENT225-017` All DOC-179 blocking gates pass before production-ready status.  
`AC-ENT225-018` P0/P1 residual gaps are zero at Enterprise DONE.  
`AC-ENT225-019` No blocking security/privacy/data-loss/authority defect is waived for completion pressure.  
`AC-ENT225-020` Final report exposes exact unresolved live-provider/environment limitations rather than fabricating success.

---

# 36. Финальная доктрина

> **Codex must treat the complete documentation repository as one Enterprise delivery contract. It audits the existing application once, locks the unified stack once, then carries the platform through secure foundations, every professional domain, documents/QR, workflows, AI, providers, analytics, migration, operations and full-system verification without scope abandonment. The execution is one campaign but remains engineering-safe through dependency phases, atomic checkpoints and blocking gates. Completion means evidence-backed integrated conformance, not a collection of individually implemented screens.**
