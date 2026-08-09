# DOCUMENTATION INDEX
## DOC-234 — canonical map, source availability and implementation-status reading order

**Status:** Canonical index  
**Updated:** 2026-08-09

---

## 1. Status dimensions

Do not collapse these dimensions:

### File/source status

```text
PRESENT
PRESENT
ARCHIVED_SOURCE
NOT_AVAILABLE
```

### Documentation status

```text
PLANNED
COMPLETE
SUPERSEDED
```

### Implementation status

```text
NOT_IMPLEMENTED_AS_SPECIFIED
IMPLEMENTED_UNVERIFIED
VERIFIED
DEFERRED
```

See DOC-207.

---

## 2. Entry and governance

| DOC | Path | Repository status |
|---|---|---|
| DOC-001 | `docs/TZ.md` | PRESENT (`TZ/Master-TZ.txt`) |
| DOC-002 | `docs/README.md` | PRESENT |
| DOC-003 | `docs/governance.md` | PRESENT |

---

## 3. Earlier source specification package

The supplied `kate-site.zip` provides mapped sources for:

| DOC range | Family | Source status | Implementation baseline |
|---|---|---|---|
| DOC-010…015 | Product | PRESENT | NOT_IMPLEMENTED_AS_SPECIFIED |
| DOC-020…030 | Customer Journey / Automation | PRESENT | NOT_IMPLEMENTED_AS_SPECIFIED |
| DOC-040…050 | UX | PRESENT | NOT_IMPLEMENTED_AS_SPECIFIED |
| DOC-060…064 | Domain | PRESENT | NOT_IMPLEMENTED_AS_SPECIFIED |
| DOC-070…080 | Architecture | PRESENT | NOT_IMPLEMENTED_AS_SPECIFIED |
| DOC-090…094 | Database | PRESENT | NOT_IMPLEMENTED_AS_SPECIFIED |
| DOC-100…103 | API | PRESENT | NOT_IMPLEMENTED_AS_SPECIFIED |
| DOC-110…134 | Modules | PRESENT | NOT_IMPLEMENTED_AS_SPECIFIED |

Exact source filenames, hashes and corrected canonical target paths:

[`../source-materials/kate-site-package-manifest.md`](../source-materials/kate-site-package-manifest.md)

All listed DOC-001…134 canonical target files are now committed and PRESENT. `PRESENT` still does not mean implemented.

---

## 4. Security — PRESENT

```text
DOC-140 docs/security/security-architecture.md
DOC-141 docs/security/authentication.md
DOC-142 docs/security/authorization.md
DOC-143 docs/security/public-and-token-access.md
DOC-144 docs/security/secrets-and-provider-security.md
DOC-145 docs/security/upload-url-and-content-security.md
DOC-146 docs/security/privacy-audit-and-abuse-protection.md
```

Implementation: NOT_VERIFIED against actual application.

---

## 5. NFR / interface quality — PRESENT

```text
DOC-150 docs/nfr/non-functional-requirements.md
DOC-151 docs/nfr/performance.md
DOC-152 docs/nfr/accessibility.md
DOC-153 docs/nfr/compatibility.md
DOC-154 docs/nfr/seo.md
DOC-155 docs/nfr/analytics.md
DOC-156 docs/nfr/interface-design-and-quality-system.md
```

DOC-156 integrates the pinned universal interface system.

---

## 6. Migrations — PRESENT

```text
DOC-160 docs/migrations/migration-strategy.md
DOC-161 docs/migrations/legacy-data-inventory.md
DOC-162 docs/migrations/database-migrations.md
DOC-163 docs/migrations/media-migrations.md
DOC-164 docs/migrations/domain-data-migrations.md
DOC-165 docs/migrations/migration-validation-and-rollback.md
```

---

## 7. Testing — PRESENT

```text
DOC-170 docs/testing/testing-strategy.md
DOC-171 docs/testing/unit-testing.md
DOC-172 docs/testing/integration-testing.md
DOC-173 docs/testing/e2e-testing.md
DOC-174 docs/testing/security-testing.md
DOC-175 docs/testing/performance-testing.md
DOC-176 docs/testing/accessibility-testing.md
DOC-177 docs/testing/visual-regression-testing.md
DOC-178 docs/testing/migration-testing.md
DOC-179 docs/testing/release-quality-gates.md
```

---

## 8. Master requirements — PRESENT

```text
DOC-180 docs/requirements/functional-requirements.md
DOC-181 docs/requirements/acceptance-criteria.md
DOC-182 docs/requirements/requirements-traceability.md
```

These are requirement contracts, not proof of implementation.

---

## 9. Operations — PRESENT

```text
DOC-190 docs/operations/operations-overview.md
DOC-191 docs/operations/deployment.md
DOC-192 docs/operations/configuration-and-secrets.md
DOC-193 docs/operations/backup-and-restore.md
DOC-194 docs/operations/monitoring-and-observability.md
DOC-195 docs/operations/incident-response.md
DOC-196 docs/operations/maintenance-and-runbooks.md
```

---

## 10. Implementation — PRESENT

```text
DOC-200 docs/implementation/implementation-strategy.md
DOC-201 docs/implementation/repository-structure.md
DOC-202 docs/implementation/coding-standards.md
DOC-203 docs/implementation/database-implementation.md
DOC-204 docs/implementation/frontend-implementation.md
DOC-205 docs/implementation/backend-worker-implementation.md
DOC-206 docs/implementation/implementation-sequencing.md
DOC-207 docs/implementation/implementation-status.md
```

DOC-207 is the authority for distinguishing spec completion from application completion.

---

## 11. GitHub / Codex — PRESENT

```text
DOC-220 docs/github/github-workflow.md
DOC-221 docs/github/issue-and-pr-templates.md
DOC-222 docs/github/branch-release-strategy.md
DOC-223 docs/github/codex-execution-plan.md
DOC-224 docs/github/interface-system-integration.md
```

DOC-224 pins `GPT-Skill-Enterfaces@598677eb16196398f0b563ed039bff341a5cf197` for future project integration.

---

## 12. Finalization / audit — PRESENT

```text
DOC-230 docs/final/documentation-audit.md
DOC-231 docs/final/gap-analysis.md
DOC-232 docs/final/final-traceability.md
DOC-233 docs/final/final-technical-specification.md
DOC-234 docs/final/documentation-index.md
DOC-235 docs/final/source-package-and-implementation-audit.md
DOC-236 docs/final/canonical-import-verification.md
```

DOC-236 is the current repository-presence authority after canonical import. DOC-235 remains the source/implementation audit; neither supersedes owning product/domain requirements.

---

## 13. Source and historical materials

```text
docs/source-materials/kate-site-package-manifest.md
docs/archive/source-materials/README.md
docs/archive/source-materials/2026-07-26/*
docs/archive/source-materials/2026-07-27/*
```

Historical material has provenance value only.

---

## 14. Corrected canonical paths for source import

Use governance path overrides, including:

```text
DOC-061 docs/domain/entity-relationships.md
DOC-064 docs/domain/revisions-and-history.md
DOC-074 docs/architecture/media-processing.md
DOC-075 docs/architecture/pdf-generation.md
DOC-077 docs/architecture/cache-strategy.md
DOC-078 docs/architecture/search.md
DOC-079 docs/architecture/ai-architecture.md
DOC-080 docs/architecture/notifications.md
DOC-090 docs/database/database-architecture.md
DOC-091 docs/database/data-dictionary.md
DOC-092 docs/database/visibility-and-access.md
DOC-093 docs/database/validation-and-constraints.md
DOC-094 docs/database/deletion-and-retention.md
DOC-100 docs/api/server-actions.md
DOC-101 docs/api/contracts.md
DOC-102 docs/api/errors.md
DOC-103 docs/api/idempotency.md
```

---

## 15. Codex reading order

### General implementation

```text
DOC-003 Governance
→ DOC-207 Implementation Status
→ DOC-233 Final Technical Specification
→ DOC-235 Current Audit
→ DOC-223 Codex Execution Plan
→ DOC-206 Implementation Sequencing
→ owning specification docs
→ Security/NFR/Testing/Migration
→ FR/AC/traceability
```

### Interface work

```text
above
→ DOC-156 Interface Design & Quality System
→ DOC-224 Interface System Integration
→ GPT-Skill-Enterfaces@598677.../docs/CODEX_MASTER.md
→ target AGENTS.md / DESIGN.md / project profile / executable UI evidence
```

### Status review

```text
DOC-207
→ DOC-235
→ actual application repository evidence
```

---

## 16. Current gaps

1. Actual actor-site application repository has not been proven by `kate-site.zip`; implementation status remains evidence-gated.
2. Universal interface source is verified, but consuming-project integration is not yet verified.
3. Runtime/browser/provider/production evidence must be collected during implementation and release.

---

## 17. Acceptance criteria

`AC-IDX234-001` Source availability and physical repository presence are distinct.  
`AC-IDX234-002` Documentation and implementation statuses are distinct.  
`AC-IDX234-003` DOC-001…134 source package is mapped without claiming implementation.  
`AC-IDX234-004` DOC-156/207/224/235 are indexed.  
`AC-IDX234-005` Universal interface source pin is explicit.  
`AC-IDX234-006` Corrected canonical path overrides remain explicit.  
`AC-IDX234-007` Codex read order starts with governance/status before implementation claims.  
`AC-IDX234-008` No index status may be upgraded without repository/runtime evidence.

---

## 18. Финальная доктрина

> **The documentation index now answers three separate questions: do we have the specification source, is the canonical document physically present, and is the software actually implemented/verified. These questions must never again be collapsed into one “Completed” status.**
