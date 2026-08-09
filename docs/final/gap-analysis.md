# GAP ANALYSIS
## DOC-231 — current Enterprise implementation and evidence gap register

**Статус:** Canonical / Current Gap Register  
**Дата актуализации:** 2026-08-09

---

# 1. Назначение

Документ фиксирует **текущие** gaps после canonical import и Enterprise consistency reconciliation.

> **The documentation-import gap is closed. Current gaps are implementation, migration, runtime, provider and operational evidence gaps unless explicitly stated otherwise.**

---

# 2. Gap classes

```text
IMPLEMENTATION_UNKNOWN
IMPLEMENTATION_MISSING
LEGACY_CONFLICT
MIGRATION_UNKNOWN
DATA_AMBIGUITY
TEST_MISSING
SECURITY_UNKNOWN
OPERATIONS_UNKNOWN
PROVIDER_EVIDENCE_MISSING
INTERFACE_INTEGRATION_UNKNOWN
TRACEABILITY_MISSING
ENVIRONMENT_DECISION_REQUIRED
NON_BASELINE_FUTURE
```

`DOC_MISSING` is not a current baseline gap for canonical project families.

---

# 3. Resolved documentation gaps

Resolved:

```text
DOC-001 / DOC-010…134 canonical import
canonical path aliases
source provenance
DOC identity metadata
single implementation technology selection
Enterprise Codex master task
```

Authority:

- DOC-236 — canonical import verification;
- DOC-208 — unified production technology stack;
- DOC-225 — Enterprise execution task;
- DOC-237 — current consistency audit.

---

# 4. GAP-IMP-001 — Actual application repository must be audited

**Class:** `IMPLEMENTATION_UNKNOWN`  
**Severity:** P0.

The documentation repository does not prove the actual site implementation.

Required evidence:

- repository tree;
- runtime/package versions;
- routes/actions;
- DB schema/migrations;
- storage;
- auth;
- providers;
- jobs;
- tests/CI;
- deployment.

### Resolution

DOC-225 Phase 0.

---

# 5. GAP-STACK-001 — Existing application may use non-canonical technology

**Class:** `LEGACY_CONFLICT / IMPLEMENTATION_UNKNOWN`  
**Severity:** P0 when discovered.

Target is DOC-208.

For every existing conflicting technology classify:

```text
KEEP_AS_CANONICAL
MIGRATE
TEMPORARY_COMPATIBILITY_ADAPTER
REMOVE
```

Enterprise completion does not allow undocumented competing ORM, migrations, queue, search, storage, styling, schema-validation or testing stacks.

---

# 6. GAP-DB-001 — Actual schema/migration compatibility unknown

**Class:** `MIGRATION_UNKNOWN`  
**Severity:** P0/P1.

Need inspect:

- current PostgreSQL version/schema;
- IDs/FKs;
- legacy tables;
- current ORM/query layer;
- visibility fields;
- revisions/snapshots;
- Audit/Outbox/jobs/idempotency;
- data volumes/indexes.

### Resolution

DOC-161/162 + DOC-208 + DOC-225.

---

# 7. GAP-DATA-001 — Legacy professional data ambiguity

**Class:** `DATA_AMBIGUITY`  
**Severity:** P1.

Potential ambiguous data:

- Skill level;
- CEFR;
- legacy educational Projects vs Training;
- Achievement wording/status/subject;
- visibility permissions;
- notification recipient vs public Contact;
- AI proposed vs Human-confirmed Casting requirements.

### Resolution

Human review queue. Never infer values to make target schema complete.

---

# 8. GAP-MEDIA-001 — Actual original/derivative inventory unknown

**Class:** `MIGRATION_UNKNOWN`  
**Severity:** P1.

Need checksum inventory of files and metadata under actual storage.

Determine:

- immutable originals;
- derivatives;
- orphan files;
- public/private access;
- legacy crop semantics;
- invalid direct filesystem URLs.

---

# 9. GAP-SEC-001 — Actual Admin authentication/authorization unknown

**Class:** `SECURITY_UNKNOWN`  
**Severity:** P0.

Verify/reconcile to DOC-141/142/208:

- PostgreSQL opaque sessions;
- Argon2id;
- cookie flags;
- expiry/rotation/revocation;
- recovery;
- CSRF/origin;
- authorization/profile scope;
- MFA readiness.

---

# 10. GAP-SEC-002 — Public serialization/access leakage unknown

**Class:** `SECURITY_UNKNOWN`  
**Severity:** P0.

Inspect raw public/RSC/API/Builder/token payloads.

Hidden fields must be absent before serialization, not merely hidden in UI.

---

# 11. GAP-SEC-003 — Upload/URL/provider security unknown

**Class:** `SECURITY_UNKNOWN`  
**Severity:** P0/P1.

Verify:

- MIME/magic/type validation;
- quarantine;
- path containment;
- SVG/content policy;
- SSRF DNS/IP redirect controls;
- OAuth state/PKCE/scopes;
- webhook signatures/replay;
- provider secret handling.

---

# 12. GAP-QNR-001 — Historical Questionnaire implementation unknown

**Class:** `IMPLEMENTATION_UNKNOWN`  
**Severity:** P1.

Must prove immutable `QuestionnaireRevision` and DocumentModel/PDF/QR generation from frozen semantics.

Current Profile edits must not mutate historical output.

---

# 13. GAP-BLD-001 — Builder eligibility isolation unknown

Need prove browser receives only Builder-eligible Source and Generate revalidates forged IDs/current eligibility.

No mandatory account signup baseline.

---

# 14. GAP-JOB-001 — Durable Outbox/job implementation unknown

**Class:** `IMPLEMENTATION_UNKNOWN`  
**Severity:** P0/P1.

Target:

```text
PostgreSQL Outbox
PostgreSQL jobs
SKIP LOCKED leases
idempotency
bounded retry
UNKNOWN_OUTCOME reconciliation
```

No Redis/BullMQ second authority baseline.

---

# 15. GAP-NOT-001 — Notification provider evidence unknown

Need prove Feedback commits independently from external provider delivery and In-App/Email/official WhatsApp states are separate.

Live Email/WhatsApp provider status may remain `IMPLEMENTED_UNVERIFIED` if credentials/sandbox evidence are unavailable.

---

# 16. GAP-AI-001 — AI capability separation unknown

Actual code must preserve separate contracts for:

```text
Casting AI
BB Assistant
Theme AI
VOP explanation
```

No generic AI write tool may mutate arbitrary domains.

---

# 17. GAP-VOP-001 — Safe Action Registry evidence missing

Need server-side hard allowlist and hard business-authority deny list.

UI/config restriction alone is insufficient.

---

# 18. GAP-SOC-001 — Social OAuth/publish/reconciliation evidence unknown

Need prove:

- OAuth state/PKCE/token security;
- media rights/embargo;
- Human approval fingerprint;
- immutable PublishSnapshot;
- durable schedule;
- provider attempt;
- signed webhook;
- UNKNOWN_OUTCOME reconciliation;
- duplicate prevention.

---

# 19. GAP-ANL-001 — Analytics implementation/evidence unknown

Need verify versioned event registry, privacy minimization, Admin/bot/non-prod filtering and server authority for business conversions.

Analytics remains read-only.

---

# 20. GAP-UI-001 — Consuming-project interface system integration unknown

**Class:** `INTERFACE_INTEGRATION_UNKNOWN`.

Generic source is verified at:

```text
CerebrumYK/GPT-Skill-Enterfaces
598677eb16196398f0b563ed039bff341a5cf197
```

Need actual application evidence for:

```text
AGENTS.md
DESIGN.md
interface project profile
pinned vendored skills
sync/drift tooling
automatic interface-build activation
runtime verification
better-interface
interface-review
```

---

# 21. GAP-TEST-001 — Full test evidence not yet linked

**Class:** `TEST_MISSING / IMPLEMENTATION_UNKNOWN`.

Need real evidence for:

```text
Vitest unit/integration
real PostgreSQL integration
Playwright E2E
security
axe accessibility
visual regression
Lighthouse/performance
migration
restore
provider fake/sandbox
```

A documented E2E case is not a passing test.

---

# 22. GAP-TRACE-001 — Actual machine-readable code/test traceability absent

DOC-182 defines the model, but actual implementation paths/tests must be populated after Phase 0.

DOC-225 requires:

```text
docs/traceability/requirements.yaml
```

or approved canonical equivalent.

Enterprise acceptance requires every release-critical FR to map to implementation/test evidence.

---

# 23. GAP-OPS-001 — Actual production topology evidence required

DOC-208 selects target:

```text
Docker Compose: web + worker + PostgreSQL
host Nginx HTTPS
/data/kate-actor persistence
```

Need actual manifests/config/service evidence and safe migration from current deployment if different.

---

# 24. GAP-OPS-002 — RPO/RTO not yet concretized

**Class:** `ENVIRONMENT_DECISION_REQUIRED`  
**Severity:** P1 before Enterprise DONE.

DOC-193 intentionally did not invent values.

DOC-225 requires the operator-realistic values to be selected from actual backup/recovery capability and proven with a timed restore rehearsal.

---

# 25. GAP-OPS-003 — Backup/restore rehearsal evidence missing

Need real isolated restore:

- PostgreSQL;
- media originals;
- compatible release;
- workers initially paused;
- revocation/provider/job reconciliation;
- derived rebuild;
- security/functional smoke;
- measured recovery duration.

---

# 26. GAP-NFR-001 — Performance baseline/evidence missing

Need real measurements against DOC-151/175 and target production-like environment.

No performance compliance claim from architecture alone.

---

# 27. GAP-A11Y-001 — Accessibility runtime evidence missing

Need automated and manual critical evidence:

- axe;
- keyboard;
- focus;
- zoom/reflow;
- contrast;
- screen-reader critical paths;
- reduced motion.

---

# 28. GAP-COMP-001 — Browser matrix evidence missing

Need Playwright-supported Chromium/Firefox/WebKit critical journey evidence plus required mobile/reflow checks.

---

# 29. GAP-SEO-001 — Production SEO/indexability evidence missing

Verify:

- canonical metadata;
- robots/sitemap;
- structured data;
- private/Admin/token noindex;
- no secret/storage URL leakage.

---

# 30. GAP-PROVIDER-001 — Live provider verification may require credentials

**Class:** `PROVIDER_EVIDENCE_MISSING`.

A provider integration can be code-complete and fake/sandbox-tested while live credentials are unavailable.

Do not fabricate live success.

If a provider is mandatory for declared production acceptance, this remains a release blocker until safe provider evidence exists.

---

# 31. Priority summary

## P0 — before broad implementation / production claim

```text
actual repository audit
stack/legacy technology reconciliation
auth/authz/public projection security
DB/migration state
secret/upload/SSRF boundaries
```

## P1 — before Enterprise DONE

```text
core domain migration
media checksums
immutable questionnaires/Builder
jobs/outbox/reconciliation
AI authority boundaries
social/provider safety
interface integration
complete tests/traceability
production deployment
backup/restore + RPO/RTO
```

## P2 — release hardening but still blocking where DOC-179 says so

```text
performance
a11y
browser compatibility
SEO
operational tuning
```

---

# 32. Resolution workflow

Every gap follows:

```text
inspect actual evidence
→ classify
→ map DOC/FR/AC
→ implement/migrate
→ test
→ attach evidence
→ update traceability
→ close only when VERIFIED or explicitly DEFERRED_NON_BASELINE
```

---

# 33. No assumption rule

Codex must not turn an unknown into a completed implementation merely to close this list.

If actual evidence reveals an unanticipated conflict, add/update the gap explicitly.

---

# 34. Acceptance criteria

`AC-GAP231-001` No current canonical documentation-import gap remains.  
`AC-GAP231-002` Remaining gaps are evidence/classification based.  
`AC-GAP231-003` Actual application audit is P0 before completion claims.  
`AC-GAP231-004` Legacy non-canonical technology has an explicit convergence path to DOC-208.  
`AC-GAP231-005` Security/public-serialization unknowns remain blockers until tested.  
`AC-GAP231-006` Ambiguous professional data always has Human-review resolution.  
`AC-GAP231-007` Provider live status is never fabricated.  
`AC-GAP231-008` RPO/RTO and restore evidence are required before Enterprise DONE.  
`AC-GAP231-009` Machine-readable implementation/test traceability is required.  
`AC-GAP231-010` Every closed implementation gap links concrete evidence.

---

# 35. Финальная доктрина

> **The specification gap is closed. The remaining work is to convert a complete, coherent Enterprise contract into verified application evidence: one stack, migrated real data, secure runtime boundaries, complete deterministic/manual workflows, controlled AI/providers, production operations and full traceability. No gap is closed by assumption.**
