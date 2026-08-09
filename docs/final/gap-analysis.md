# GAP ANALYSIS

> **Repository-presence status note (2026-08-09):** statements in this historical audit about DOC-001…134 being unavailable/not imported are superseded by DOC-236 `docs/final/canonical-import-verification.md`. Canonical import is complete; implementation remains evidence-gated.

## DOC-231 — repository, specification, implementation and operational gap classification

**Статус:** Canonical / Final Gap Register

---

## 1. Назначение

Документ фиксирует известные gaps после автоматической генерации remaining documentation package и определяет, какие gaps являются repository-content gaps, какие требуют actual website-code audit, а какие являются будущей implementation работой.

> **A gap is not silently filled with assumptions. It is classified, assigned a resolution path and kept distinct from a defect in already implemented behavior.**

---

## 2. Gap classes

```text
DOC_MISSING
DOC_STALE_REFERENCE
DOC_CONFLICT
IMPLEMENTATION_UNKNOWN
IMPLEMENTATION_MISSING
TEST_MISSING
MIGRATION_UNKNOWN
OPERATIONS_UNKNOWN
SECURITY_UNKNOWN
DATA_AMBIGUITY
PROVIDER_DEPENDENCY
NON_BASELINE_FUTURE
```

## 3. Current highest-priority repository gap

### GAP-DOC-001 — Earlier canonical documents are not physically present

**Class:** `DOC_MISSING`  
**Severity:** High for standalone documentation-repository completeness; not a contradiction in remaining docs.

Missing physical files include project documents authored before this repository was initialized:

```text
DOC-001
DOC-010…015
DOC-020…030
DOC-040…050
DOC-060…064
DOC-070…080
DOC-090…094
DOC-100…103
DOC-110…134
```

### Resolution

Import those existing authored project documents into their canonical paths, or formally supersede them through an approved consolidated source-of-truth decision.

### Guardrail

Do not regenerate approximate versions and present them as byte-identical originals without explicit decision; preserve project-authored semantics.

---

## 4. GAP-DOC-002 — Known stale related-document paths in earlier docs

**Class:** `DOC_STALE_REFERENCE`  
**Severity:** Medium.

Known aliases likely needing correction after import:

```text
database-schema.md
visibility-model.md
validation-rules.md
deletion-retention.md
```

Canonical targets:

```text
database-architecture.md
visibility-and-access.md
validation-and-constraints.md
deletion-and-retention.md
```

### Resolution

Run repository-wide Markdown link/path audit after DOC-001…134 import.

---

## 5. GAP-IMP-001 — Website implementation repository not audited by this documentation operation

**Class:** `IMPLEMENTATION_UNKNOWN`  
**Severity:** Critical for claiming product completion, zero impact on documentation-generation completion.

The current repository is documentation-only. No evidence in this operation proves the actual actress website code implements DOC-140+ or earlier module specifications.

### Resolution

Run DOC-223 Phase 0 against the actual implementation repository, then populate DOC-232 with code/migration/test paths.

---

## 6. GAP-IMP-002 — Existing database/schema compatibility unknown

**Class:** `IMPLEMENTATION_UNKNOWN / MIGRATION_UNKNOWN`.

Need actual audit of:

- PostgreSQL schema;
- current migrations;
- legacy tables;
- existing IDs/visibility flags;
- revisions/history;
- outbox/jobs;
- stored media references.

### Resolution

DOC-161 inventory + DOC-162 migration mapping against actual implementation.

---

## 7. GAP-DATA-001 — Legacy professional-data ambiguity cannot be resolved from documentation alone

**Class:** `DATA_AMBIGUITY`.

Potential ambiguity families:

- free-text Skill level;
- qualitative Language proficiency;
- legacy Project `Other` vs Training;
- recognition/Achievement wording;
- public/Builder/QNR permissions;
- notification-recipient/public-contact coupling;
- AI-generated vs Human-confirmed Casting requirements.

### Resolution

Inventory actual records and generate Human review queue. Never infer values only to satisfy target schema.

---

## 8. GAP-SEC-001 — Current authentication implementation unknown

**Class:** `SECURITY_UNKNOWN`  
**Severity:** Critical for production.

Need verify:

- session storage/cookies;
- password/recovery;
- authorization checks;
- CSRF;
- Admin route protection;
- MFA readiness.

### Resolution

DOC-223 Phase 1 + DOC-174 security tests.

---

## 9. GAP-SEC-002 — Current public serialization/visibility leakage unknown

Need inspect raw RSC/API/public JSON for hidden Contacts/Skills/Languages/media/admin state.

### Resolution

Public projection refactor + raw-response negative security tests.

---

## 10. GAP-SEC-003 — Upload/SSRF/provider-secret implementation unknown

Need audit:

- upload MIME/magic validation;
- SVG policy;
- storage paths;
- server-side URL fetch;
- provider keys/tokens;
- OAuth state/scopes;
- webhook signatures.

### Resolution

DOC-145/144 implementation and security testing.

---

## 11. GAP-MED-001 — Actual media-original/derivative classification unknown

Need verify current files under deployment storage and whether existing thumbnails/processed files are being treated as originals.

### Resolution

DOC-161 + DOC-163 checksum inventory.

---

## 12. GAP-QNR-001 — Current Questionnaire revision immutability unknown

Need verify current system stores immutable historical revisions rather than rendering directly from mutable current Profile.

If not, this is a high-priority architecture migration because historical PDFs/QR must remain frozen.

---

## 13. GAP-QR-001 — QR not present in original current project status

Earlier project notes indicated QR was absent at runtime. DOC-130 now specifies it fully.

**Class:** likely `IMPLEMENTATION_MISSING`, pending actual code audit.

Prerequisites:

- stable application-controlled public URLs;
- PDF renderer;
- canonical link service;
- exact decode verification.

---

## 14. GAP-NOT-001 — Outbound notifications runtime status uncertain

Earlier project status indicated outbound notifications outside current runtime. Actual implementation must confirm:

- durable outbox;
- NotificationRecipient separation;
- In-App center;
- Email/official WhatsApp Business configuration;
- quiet hours;
- retries.

---

## 15. GAP-SOC-001 — Social scheduling/runtime uncertain

Earlier project roadmap indicated scheduled social publishing outside runtime while OAuth publishing was planned/partially specified.

Actual code audit must classify:

```text
ABSENT
PARTIAL_DRAFT_ONLY
CONNECTED_NO_SCHEDULER
FULLY_IMPLEMENTED
```

Do not resume legacy schedules blindly during migration.

---

## 16. GAP-ANL-001 — Analytics/share tracking runtime uncertain

Earlier project status indicated analytics/share tracking roadmap. DOC-129/155 now define target architecture; implementation state must be verified.

---

## 17. GAP-AI-001 — AI role separation in code unknown

Documentation explicitly separates:

```text
Casting AI
Theme AI
BB Assistant
VOP
```

Need audit actual code for one generic AI helper that bypasses capability-specific contracts/provenance.

---

## 18. GAP-VOP-001 — Safe Action Registry implementation unknown

Target requires hard allowlist and hard business-authority deny list. If current VOP does not exist, implement after deterministic domains/jobs. If generic AI operator exists, restrict before expanding.

---

## 19. GAP-HLP-001 — Help Center/Tickets current implementation unknown

Earlier project requirements mention RU help center/tickets. Need verify actual separation of professional Feedback vs technical HelpTicket, private internal notes and attachment security.

---

## 20. GAP-ACH-001 — Achievement semantic status normalization unknown

Need inspect legacy records for `winner/nominee/official selection/laureate` and confirm subject personal vs project. No automatic upgrade.

---

## 21. GAP-NFR-001 — Performance baseline not measured

**Class:** `TEST_MISSING / IMPLEMENTATION_UNKNOWN`.

Need measure current public pages/API/DB/media using DOC-151/175 targets before claiming compliance.

---

## 22. GAP-A11Y-001 — Accessibility baseline not measured

Need automated + keyboard/screen-reader/contrast review. Current design appearance alone is no evidence of WCAG compliance.

---

## 23. GAP-COMP-001 — Browser support baseline not measured

Need actual browser matrix tests against implementation.

---

## 24. GAP-SEO-001 — Current metadata/indexability not audited

Need verify canonical/robots/sitemap/structured data and ensure Admin/token resources do not leak.

---

## 25. GAP-OPS-001 — Actual production service names/process manager unknown

Documentation deliberately does not invent exact systemd/container commands. Deployment/runbook must be specialized after inspecting actual production units.

### Resolution

Record actual:

- service names;
- working directories;
- environment file location;
- Nginx site config;
- worker command;
- PostgreSQL backup command/tool;
- monitoring stack.

---

## 26. GAP-OPS-002 — RPO/RTO values not formally set

DOC-193 defines semantics but intentionally does not invent operational commitment.

### Resolution

Operator chooses values based on actual backup frequency/storage/recovery capability.

---

## 27. GAP-OPS-003 — Restore rehearsal evidence absent in documentation repository

Need actual periodic restore evidence after implementation/ops integration.

---

## 28. GAP-GH-001 — `.github` templates/workflows not created by this documentation-only operation

DOC-221 specifies templates, but this operation creates canonical documentation, not implementation `.github` files unless separately requested/implemented.

### Resolution

Codex Phase 0/1 can materialize `.github` templates/workflows in the actual implementation repo.

---

## 29. GAP-TRACE-001 — Actual code/test paths absent from traceability

DOC-182 deliberately contains model/example, not invented code paths.

### Resolution

After actual code audit, create/update machine-readable traceability and DOC-232.

---

## 30. Gap priorities

### P0 before implementation claim

```text
GAP-DOC-001 if repository must be standalone complete
GAP-IMP-001
GAP-SEC-001/002/003
GAP-DB actual schema inventory
```

### P1 before production cutover of new architecture

```text
media inventory/checksums
questionnaire revision verification
migration ambiguity review
backup/restore evidence
provider/outbox/idempotency verification
```

### P2 hardening

```text
performance/accessibility/browser/SEO baselines
analytics/social/help/VOP optional module completion according to roadmap
```

---

## 31. Gap resolution workflow

For every gap:

```text
confirm actual state
→ attach evidence
→ classify owner
→ link FR/AC/DOC
→ create scoped issue
→ implement/migrate/test
→ update traceability
→ close only after VERIFIED
```

---

## 32. No-gap invention rule

If actual repository contains an implementation not anticipated here, add/update gap/audit based on evidence. Do not force code into a prewritten gap category inaccurately.

---

## 33. Acceptance criteria

`AC-GAP231-001` Missing earlier documentation is explicitly classified rather than hidden.  
`AC-GAP231-002` Documentation completeness and website implementation completeness are separated.  
`AC-GAP231-003` Security/database/media/migration implementation state remains UNKNOWN until actual audit.  
`AC-GAP231-004` Ambiguous professional data has Human-review resolution path.  
`AC-GAP231-005` Optional roadmap modules are not falsely marked implemented.  
`AC-GAP231-006` Performance/accessibility/SEO/restore compliance requires measured evidence.  
`AC-GAP231-007` Operational values/service commands are not invented.  
`AC-GAP231-008` Every gap can be converted into traceable GitHub implementation task.  
`AC-GAP231-009` Closing a gap requires implementation/test evidence.  
`AC-GAP231-010` Future audits may add/remove gaps based on actual repository evidence without rewriting historical audit dishonestly.

---

## 34. Финальная доктрина

> **The principal current gap is evidence, not specification: the remaining documentation is now defined, but earlier project documents must still be imported for standalone repository completeness and the actual website repository must be audited before any implementation requirement can be marked verified. Every other gap is resolved through evidence, scoped implementation, migration and tests—not assumption.**
