# FINAL TRACEABILITY
## DOC-232 — current documentation-to-implementation traceability state

**Статус:** Canonical / Current Traceability  
**Дата актуализации:** 2026-08-09

---

# 1. Назначение

Документ фиксирует текущую traceability между canonical requirements, owning documents и требуемыми implementation/test evidence.

> **All owning specification documents are now physically present. Actual code, migration and passing-test paths remain evidence-gated until DOC-225 is executed against the application repository.**

---

# 2. Status model

```text
PRESENT_DOCUMENTED
PLANNED
IMPLEMENTING
IMPLEMENTED_UNVERIFIED
VERIFIED
BLOCKED
DEFERRED_NON_BASELINE
```

There is no current `EXPECTED_NOT_IMPORTED` canonical status.

---

# 3. Documentation-family status

| Family | DOC range | Documentation | Application evidence |
|---|---:|---|---|
| Technical/Product | DOC-001, 010–015 | PRESENT_DOCUMENTED | NOT_IMPLEMENTED_AS_SPECIFIED / evidence-gated |
| Customer Journey/Automation | DOC-020–030 | PRESENT_DOCUMENTED | evidence-gated |
| UX | DOC-040–050 | PRESENT_DOCUMENTED | evidence-gated |
| Domain | DOC-060–064 | PRESENT_DOCUMENTED | evidence-gated |
| Architecture | DOC-070–080 | PRESENT_DOCUMENTED | evidence-gated |
| Database | DOC-090–094 | PRESENT_DOCUMENTED | evidence-gated |
| API | DOC-100–103 | PRESENT_DOCUMENTED | evidence-gated |
| Modules | DOC-110–134 | PRESENT_DOCUMENTED | NOT_IMPLEMENTED_AS_SPECIFIED baseline |
| Security | DOC-140–146 | PRESENT_DOCUMENTED | NOT_VERIFIED |
| NFR / Interface | DOC-150–156 | PRESENT_DOCUMENTED | NOT_VERIFIED |
| Migrations | DOC-160–165 | PRESENT_DOCUMENTED | NOT_VERIFIED |
| Testing | DOC-170–179 | PRESENT_DOCUMENTED | NOT_VERIFIED |
| Master Requirements | DOC-180–182 | PRESENT_DOCUMENTED | requirement contract |
| Operations | DOC-190–196 | PRESENT_DOCUMENTED | NOT_VERIFIED |
| Implementation | DOC-200–208 | PRESENT_DOCUMENTED | implementation contract |
| GitHub / Codex | DOC-220–225 | PRESENT_DOCUMENTED | process contract |
| Final / Audit | DOC-230–237 | PRESENT_DOCUMENTED | audit/status contract |

---

# 4. Product/domain ownership traceability

| Requirement family | Primary canonical owner |
|---|---|
| FR-PRO | DOC-110 Profile |
| FR-MED | DOC-111 + DOC-074 |
| FR-POR | DOC-112 |
| FR-EMO | DOC-113 |
| FR-EGR | DOC-114 |
| FR-PRJ | DOC-115 |
| FR-TRN | DOC-116 |
| FR-SKL / FR-LNG | DOC-117 |
| FR-LNK | DOC-118 |
| FR-CNT | DOC-119 |
| FR-QNR | DOC-120 + DOC-075 |
| FR-BLD | DOC-121 |
| FR-CST | DOC-122 |
| FR-CAI | DOC-123 + DOC-079 |
| FR-FBK | DOC-124 |
| FR-NOT | DOC-125 + DOC-080 |
| FR-OPP | DOC-126 |
| FR-THM | DOC-127 |
| FR-VOP | DOC-128 |
| FR-ANL | DOC-129 + DOC-155 |
| FR-QR | DOC-130 |
| FR-BB | DOC-131 |
| FR-HLP | DOC-132 |
| FR-SOC | DOC-133 |
| FR-ACH | DOC-134 |
| FR-SEC | DOC-140…146 |
| FR-NFR | DOC-150…156 |
| FR-OPS | DOC-190…196 |
| FR-ENT | DOC-208 + DOC-225 + DOC-237 |

---

# 5. Architecture trace

```text
Product/Domain requirements
→ DOC-070…080 Architecture
→ DOC-090…103 Database/API
→ DOC-200…208 Implementation
→ DOC-170…179 proof layers
→ DOC-190…196 operations
```

DOC-208 is the concrete implementation-technology authority and removes ambiguity between competing production toolchains.

---

# 6. Security trace

Every public/private/Admin/Builder/token/provider flow maps to:

```text
DOC-140 Security Architecture
DOC-141 Authentication
DOC-142 Authorization
DOC-143 Public/Token Access
DOC-144 Secrets/Provider Security
DOC-145 Upload/URL/Content Security
DOC-146 Privacy/Audit/Abuse
DOC-174 Security Testing
DOC-179 Release Gates
```

A release-critical public/provider FR cannot become VERIFIED without negative security evidence.

---

# 7. Unified-stack trace

```text
FR-ENT
→ DOC-208 Unified Technology Stack
→ DOC-225 Enterprise Implementation Pass
→ actual package/runtime/config/container files
→ build/typecheck/test/security/operations evidence
→ DOC-237 consistency audit
```

Implementation must demonstrate one selected stack rather than merely having documentation that names it.

---

# 8. Database/migration trace

Any persisted change maps to:

```text
owning FR/DOC
→ Drizzle/PostgreSQL implementation
→ canonical migration ID
→ migration test
→ data reconciliation evidence
→ rollback/recovery evidence where applicable
```

No migration may be marked complete with ambiguous professional data silently defaulted.

---

# 9. Media/document trace

Critical pipeline:

```text
Media Source
→ immutable original checksum
→ derivative profile
→ Questionnaire/Builder Revision or Snapshot
→ DocumentModel
→ PDF
→ hyperlink assertion
→ QR exact decode
```

Expected evidence includes integration tests plus representative E2E/PDF verification.

---

# 10. Provider-side-effect trace

For Notifications/Social/external integrations:

```text
FR
→ Human/domain intent
→ DB transaction
→ Audit/Outbox
→ Job
→ Attempt
→ provider adapter
→ receipt/webhook/poll
→ final/reconciled state
→ tests
```

Timeout without evidence maps to `UNKNOWN_OUTCOME`, not success/failure by assumption.

---

# 11. AI trace

Each AI capability has independent traceability:

```text
Casting AI  → DOC-123
BB          → DOC-131
Theme AI    → DOC-127
VOP AI      → DOC-128
```

Every implementation must link:

- SourceSnapshot;
- prompt/schema version;
- output validation;
- Human-authority boundary;
- provider-disabled/manual fallback tests.

No generic AI helper can satisfy multiple authority contracts by name alone.

---

# 12. Interface trace

Substantial rendered UI changes map:

```text
owning FR/DOC
→ DOC-156
→ DOC-224
→ GPT-Skill-Enterfaces pinned ref
→ project DESIGN.md / interface profile
→ implementation path
→ runtime browser evidence
→ better-interface review
→ interface-review
→ accessibility/visual/E2E gates
```

---

# 13. Test layer mapping

| Requirement type | Required evidence |
|---|---|
| pure domain/state/validation | Vitest unit |
| DB/transaction/filesystem/job/provider contract | real PostgreSQL integration + fakes |
| user/trust boundary | Playwright E2E |
| auth/visibility/SSRF/XSS/token/provider input | security + E2E |
| accessibility | axe + browser/manual critical evidence |
| performance | Lighthouse/browser/DB measurements |
| layout/theme | visual regression + runtime review |
| migration/data | migration suite/reconciliation |
| backup/recovery | isolated restore rehearsal |

---

# 14. Master acceptance trace

DOC-181 remains the integrated product-level acceptance layer.

Module AC/INV/E2E requirements provide detailed proof underneath it.

A master AC can become VERIFIED only when all applicable underlying owner conditions pass simultaneously.

---

# 15. Machine-readable implementation traceability

DOC-225 requires the implementation repository to maintain:

```text
docs/traceability/requirements.yaml
```

or a formally designated equivalent.

Minimum entry:

```yaml
- id: FR-FBK-003
  owner_docs:
    - docs/modules/feedback.md
    - docs/architecture/notifications.md
  status: VERIFIED
  implementation_paths:
    - <actual path>
  migration_ids:
    - <actual migration or N/A rationale>
  integration_tests:
    - <actual test id/path>
  e2e_security_nfr_tests:
    - <actual test id/path>
  acceptance_ids:
    - AC-MASTER-056
  evidence:
    - <PR/commit/run/artifact>
```

No implementation path may be invented in this documentation repository before actual code audit.

---

# 16. Bidirectional coverage requirements

The implementation campaign must detect:

```text
FR without implementation
FR without tests
public/provider FR without security test
schema FR without migration evidence/rationale
test without FR/AC justification
implementation path without owning requirement
stale documentation path
```

Release-critical unresolved traceability gaps block Enterprise DONE.

---

# 17. Current evidence status

Current documentation repository can assert:

```text
all canonical owners identified: YES
all canonical files present: YES
single target technology stack documented: YES
Enterprise execution contract documented: YES
actual implementation paths: NOT YET AUDITED HERE
actual migrations/tests/results: NOT YET AUDITED HERE
production/runtime verification: NOT YET AUDITED HERE
```

---

# 18. Transition to implementation evidence

DOC-225 Phase 0 performs the legitimate transition:

```text
PRESENT_DOCUMENTED
→ PLANNED / IMPLEMENTING
→ IMPLEMENTED_UNVERIFIED
→ VERIFIED
```

Only real code/migration/test/runtime evidence moves the state.

---

# 19. Final traceability acceptance criteria

`AC-FTRACE-001` Every master requirement family has a present owning canonical document.  
`AC-FTRACE-002` No canonical owner remains `EXPECTED_NOT_IMPORTED`.  
`AC-FTRACE-003` DOC-208 provides a traceable single production stack.  
`AC-FTRACE-004` DOC-225 provides one end-to-end implementation campaign.  
`AC-FTRACE-005` Security/NFR/migration/operations support every affected release-critical FR.  
`AC-FTRACE-006` Implementation paths are added only from actual repository evidence.  
`AC-FTRACE-007` VERIFIED status requires passing relevant tests/evidence.  
`AC-FTRACE-008` Machine-readable traceability is required before Enterprise DONE.  
`AC-FTRACE-009` Bidirectional orphan detection is part of final acceptance.  
`AC-FTRACE-010` Provider/runtime limitations remain explicit instead of being fabricated as verified.

---

# 20. Финальная доктрина

> **Documentation traceability is now complete at the design level: every requirement family has a canonical owner, security/NFR/migration/testing support and one implementation-stack/execution contract. The remaining traceability work is intentionally evidence-driven—Codex must attach actual code, migrations, tests, runtime and release evidence before any requirement becomes VERIFIED.**
