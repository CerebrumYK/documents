# REQUIREMENTS TRACEABILITY
## DOC-182 — mapping of master requirements to design documents, implementation and tests

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет traceability model между `FR-*`, module/domain documents, implementation units, migrations and tests.

> **Every release-critical requirement must be traceable in both directions: from requirement to implementation/test, and from implementation/test back to the requirement that justifies it.**

---

## 2. Traceability dimensions

Each requirement may map to:

```text
Requirement ID
Owning document
Supporting document(s)
Domain/module
Implementation package/path
Database migration(s)
Unit test(s)
Integration test(s)
E2E/security/NFR test(s)
Acceptance criterion
Status
Evidence/issue/PR
```

## 3. Status taxonomy

```text
DOCUMENTED
PLANNED
IMPLEMENTING
IMPLEMENTED_UNVERIFIED
VERIFIED
BLOCKED
DEFERRED_NON_BASELINE
```

`VERIFIED` requires passing relevant acceptance tests, not only merged code.

## 4. Ownership map

| Requirement family | Primary design source |
|---|---|
| FR-PRO | Profile module/domain |
| FR-MED | Media Library + media-processing |
| FR-POR | Portfolio |
| FR-EMO / FR-EGR | Emotional Portfolio/Grid |
| FR-PRJ | Projects |
| FR-TRN | Training |
| FR-SKL / FR-LNG | Skills & Languages |
| FR-LNK | Professional Media & Links |
| FR-CNT | Contacts |
| FR-QNR | Questionnaires/PDF |
| FR-BLD | Public Questionnaire Builder |
| FR-QR | QR Links |
| FR-CST / FR-CAI | Castings / Casting AI |
| FR-FBK | Feedback |
| FR-NOT | Notifications |
| FR-OPP | Opportunity Pipeline |
| FR-THM | Site Themes |
| FR-BB | BB Assistant |
| FR-VOP | Virtual Operator |
| FR-ANL | Marketing Analytics |
| FR-HLP | Help Tickets |
| FR-SOC | Social Publishing |
| FR-ACH | Achievements |
| FR-SEC | Security DOC-140…146 |
| FR-NFR | NFR DOC-150…155 |
| FR-OPS | Operations DOC-190…196 |
| FR-ENT | DOC-208 Unified Stack + DOC-225 Enterprise Pass + DOC-237 Audit |

## 5. Test layer map

| Requirement nature | Minimum test layer |
|---|---|
| pure validation/state | Unit |
| transaction/FK/filesystem | Integration |
| user journey/public exposure | E2E |
| auth/visibility/SSRF/XSS/token | Security + E2E |
| latency/load | Performance |
| keyboard/contrast/reader | Accessibility |
| layout/theme | Visual regression |
| legacy transform | Migration |

## 6. Security traceability

Every public/write/provider feature must reference at least one security requirement/test family. “Feature-only” traceability is incomplete when it crosses trust boundary.

## 7. Migration traceability

Any schema/data ownership change maps to migration ID + migration test. If no migration required, trace entry may state `N/A — new table/feature on fresh install` with rationale.

## 8. Acceptance traceability

Master `AC-MASTER-*` references one or more `FR-*` and module-specific AC/E2E IDs. Final DOC-232 will instantiate current repository status.

## 9. Suggested machine-readable file

Implementation SHOULD maintain a generated/hand-maintained file such as:

```text
docs/traceability/requirements.yaml
```

Example:

```yaml
- id: FR-QNR-007
  owner: docs/modules/questionnaires.md
  status: VERIFIED
  implementation:
    - src/modules/questionnaires/pdf/...
  tests:
    - IT-QNR-PDF-001
    - E2E-QNR-PDF-001
  acceptance:
    - AC-MASTER-039
```

Actual code paths are filled only after repository implementation is inspected; documentation must not invent paths.

## 10. GitHub linkage

Issues/PRs should reference relevant `FR-*`/`AC-*`. This allows code review to ask which requirement changes.

## 11. Requirement change

When requirement semantics change:

1. update owning canonical doc;
2. update DOC-180 if master catalog affected;
3. update tests;
4. update migration/operations if impacted;
5. update traceability;
6. record breaking/compatibility impact.

## 12. Requirement deletion

Do not silently remove ID. Mark superseded/deprecated with successor/reason so historical PR/test references remain understandable.

## 13. ID stability

Requirement IDs are stable once referenced externally. Editing wording is allowed only while preserving semantics; semantic replacement uses new ID or explicit version/supersession.

## 14. Coverage reports

Useful aggregate views:

```text
% FR documented
% FR implemented
% FR verified
unverified security requirements
requirements without tests
tests without requirement link
open migration-dependent requirements
```

Percentages are management aids, not proof of product correctness.

## 15. Gap classification

Traceability gap types:

```text
NO_DESIGN
NO_IMPLEMENTATION
NO_TEST
NO_SECURITY_TEST
NO_MIGRATION
NO_OPERATIONS
STALE_DOCUMENTATION
CONFLICTING_REQUIREMENTS
```

## 16. Conflict resolution

If two docs conflict, use governance/source-of-truth precedence (DOC-003) and create explicit documentation correction; tests must not arbitrarily choose one interpretation.

## 17. Example traces

### Questionnaire QR

```text
FR-QR-002
→ QR Links / PDF Generation / Questionnaires
→ QR renderer + PDF renderer implementation
→ unit target equality
→ integration decode
→ E2E PDF scan
→ AC-MASTER-040/041/042
```

### Feedback durability

```text
FR-FBK-002/003
→ Feedback + Notifications architecture
→ DB transaction/outbox
→ integration provider-failure test
→ E2E Feedback success with provider down
→ AC-MASTER-055/056
```

### Social publish

```text
FR-SOC-005…009
→ Social Publishing + Security
→ approval snapshot/attempt worker
→ integration provider fake
→ E2E unknown-outcome reconcile
→ AC-MASTER-085…087
```

## 18. Review checklist

For every PR changing product behavior:

- Which `FR-*` changed/implemented?
- Which owning doc applies?
- Which AC proves it?
- Which tests changed?
- Security/privacy impact?
- Migration impact?
- Operations impact?
- Traceability updated?

## 19. Anti-patterns

Forbidden:

1. Requirement marked VERIFIED because issue closed.
2. Code path invented in docs before repository exists.
3. Test coverage percentage used instead of requirement mapping.
4. Security requirement omitted from public feature trace.
5. Requirement ID reused for different semantics.
6. Deleted requirement ID disappears without successor.
7. Conflicting docs silently resolved by implementation preference.
8. Migration needed but trace says N/A without rationale.

## 20. Acceptance criteria

`AC-TRACE-001` Every master requirement has owning design document.  
`AC-TRACE-002` Implemented release-critical requirements map to implementation evidence.  
`AC-TRACE-003` Verified requirements map to passing test IDs.  
`AC-TRACE-004` Trust-boundary requirements include security test evidence.  
`AC-TRACE-005` Schema/data changes include migration evidence or explicit N/A rationale.  
`AC-TRACE-006` FR/AC IDs remain stable and supersession is documented.  
`AC-TRACE-007` GitHub issues/PRs can reference requirement IDs.  
`AC-TRACE-008` Requirement changes update docs/tests/traceability together.  
`AC-TRACE-009` Gaps are classified explicitly.  
`AC-TRACE-010` Final DOC-232 reports actual verification state without inventing code evidence.

---

## 21. Финальная доктрина

> **Traceability connects intent to evidence. Every important requirement has an owner, implementation path, migration/operations impact when relevant and a test that proves it. “Implemented” and “verified” remain different states, preventing documentation or issue closure from masquerading as working, tested product behavior.**
