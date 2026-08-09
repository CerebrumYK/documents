# DOCUMENTATION INDEX
## DOC-234 — final documentation map, status and reading order

**Статус:** Canonical / Final Index

---

## 1. Назначение

Этот индекс описывает canonical documentation map проекта и фактический статус файлов в текущем `CerebrumYK/documents` repository.

Status labels:

```text
PRESENT — file exists in this repository
EXPECTED_NOT_IMPORTED — authored/defined earlier in project, but not physically imported into this repository yet
FINALIZATION — present as part of current final package
```

> **Do not treat EXPECTED_NOT_IMPORTED as missing product semantics that may be invented. Import the earlier canonical project document or formally supersede it through governance.**

---

# 2. Entry / Governance

| DOC | Path | Status | Purpose |
|---|---|---|---|
| DOC-001 | `docs/TZ.md` | EXPECTED_NOT_IMPORTED | Original/primary technical specification authored earlier; final consolidation is DOC-233 |
| DOC-002 | `docs/README.md` | PRESENT after finalization | Documentation entry point |
| DOC-003 | `docs/governance.md` | PRESENT after finalization | Source-of-truth, precedence and change governance |

---

# 3. Product family

| DOC range | Status | Family |
|---|---|---|
| DOC-010…015 | EXPECTED_NOT_IMPORTED | Product overview, terminology, business rules, information architecture, scope, product principles |

Known canonical paths:

```text
docs/product/product-overview.md
docs/product/terminology.md
docs/product/business-rules.md
docs/product/information-architecture.md
docs/product/scope.md
docs/product/product-principles.md
```

---

# 4. Customer Journey / Automation

| DOC range | Status |
|---|---|
| DOC-020…030 | EXPECTED_NOT_IMPORTED |

These documents were authored earlier in the project and should be imported with their original canonical titles/paths rather than reconstructed from memory.

---

# 5. UX

| DOC range | Status |
|---|---|
| DOC-040…050 | EXPECTED_NOT_IMPORTED |

Import original UX family from project source/history.

---

# 6. Domain

| DOC | Path | Status |
|---|---|---|
| DOC-060 | `docs/domain/domain-model.md` | EXPECTED_NOT_IMPORTED |
| DOC-061 | `docs/domain/entity-relationships.md` | EXPECTED_NOT_IMPORTED |
| DOC-062 | `docs/domain/state-machines.md` | EXPECTED_NOT_IMPORTED |
| DOC-063 | `docs/domain/data-ownership.md` | EXPECTED_NOT_IMPORTED |
| DOC-064 | `docs/domain/revisions-and-history.md` | EXPECTED_NOT_IMPORTED |

---

# 7. Architecture

| DOC | Path | Status |
|---|---|---|
| DOC-070 | `docs/architecture/architecture-overview.md` | EXPECTED_NOT_IMPORTED |
| DOC-071 | `docs/architecture/components.md` | EXPECTED_NOT_IMPORTED |
| DOC-072 | `docs/architecture/data-flows.md` | EXPECTED_NOT_IMPORTED |
| DOC-073 | `docs/architecture/projections.md` | EXPECTED_NOT_IMPORTED |
| DOC-074 | `docs/architecture/media-processing.md` | EXPECTED_NOT_IMPORTED |
| DOC-075 | `docs/architecture/pdf-generation.md` | EXPECTED_NOT_IMPORTED |
| DOC-076 | `docs/architecture/background-jobs.md` | EXPECTED_NOT_IMPORTED |
| DOC-077 | `docs/architecture/cache-strategy.md` | EXPECTED_NOT_IMPORTED |
| DOC-078 | `docs/architecture/search.md` | EXPECTED_NOT_IMPORTED |
| DOC-079 | `docs/architecture/ai-architecture.md` | EXPECTED_NOT_IMPORTED |
| DOC-080 | `docs/architecture/notifications.md` | EXPECTED_NOT_IMPORTED |

---

# 8. Database

| DOC | Path | Status |
|---|---|---|
| DOC-090 | `docs/database/database-architecture.md` | EXPECTED_NOT_IMPORTED |
| DOC-091 | `docs/database/data-dictionary.md` | EXPECTED_NOT_IMPORTED |
| DOC-092 | `docs/database/visibility-and-access.md` | EXPECTED_NOT_IMPORTED |
| DOC-093 | `docs/database/validation-and-constraints.md` | EXPECTED_NOT_IMPORTED |
| DOC-094 | `docs/database/deletion-and-retention.md` | EXPECTED_NOT_IMPORTED |

---

# 9. API

| DOC | Path | Status |
|---|---|---|
| DOC-100 | `docs/api/server-actions.md` | EXPECTED_NOT_IMPORTED |
| DOC-101 | `docs/api/contracts.md` | EXPECTED_NOT_IMPORTED |
| DOC-102 | `docs/api/errors.md` | EXPECTED_NOT_IMPORTED |
| DOC-103 | `docs/api/idempotency.md` | EXPECTED_NOT_IMPORTED |

---

# 10. Modules

| DOC | Path | Status |
|---|---|---|
| DOC-110 | `docs/modules/profile.md` | EXPECTED_NOT_IMPORTED |
| DOC-111 | `docs/modules/media-library.md` | EXPECTED_NOT_IMPORTED |
| DOC-112 | `docs/modules/portfolio.md` | EXPECTED_NOT_IMPORTED |
| DOC-113 | `docs/modules/emotional-portfolio.md` | EXPECTED_NOT_IMPORTED |
| DOC-114 | `docs/modules/emotional-grid.md` | EXPECTED_NOT_IMPORTED |
| DOC-115 | `docs/modules/projects.md` | EXPECTED_NOT_IMPORTED |
| DOC-116 | `docs/modules/training.md` | EXPECTED_NOT_IMPORTED |
| DOC-117 | `docs/modules/skills-languages.md` | EXPECTED_NOT_IMPORTED |
| DOC-118 | `docs/modules/professional-media-links.md` | EXPECTED_NOT_IMPORTED |
| DOC-119 | `docs/modules/contacts.md` | EXPECTED_NOT_IMPORTED |
| DOC-120 | `docs/modules/questionnaires.md` | EXPECTED_NOT_IMPORTED |
| DOC-121 | `docs/modules/public-questionnaire-builder.md` | EXPECTED_NOT_IMPORTED |
| DOC-122 | `docs/modules/castings.md` | EXPECTED_NOT_IMPORTED |
| DOC-123 | `docs/modules/casting-ai-analysis.md` | EXPECTED_NOT_IMPORTED |
| DOC-124 | `docs/modules/feedback.md` | EXPECTED_NOT_IMPORTED |
| DOC-125 | `docs/modules/notifications.md` | EXPECTED_NOT_IMPORTED |
| DOC-126 | `docs/modules/opportunity-pipeline.md` | EXPECTED_NOT_IMPORTED |
| DOC-127 | `docs/modules/site-themes.md` | EXPECTED_NOT_IMPORTED |
| DOC-128 | `docs/modules/virtual-operator.md` | EXPECTED_NOT_IMPORTED |
| DOC-129 | `docs/modules/marketing-analytics.md` | EXPECTED_NOT_IMPORTED |
| DOC-130 | `docs/modules/qr-links.md` | EXPECTED_NOT_IMPORTED |
| DOC-131 | `docs/modules/bb-assistant.md` | EXPECTED_NOT_IMPORTED |
| DOC-132 | `docs/modules/help-tickets.md` | EXPECTED_NOT_IMPORTED |
| DOC-133 | `docs/modules/social-publishing.md` | EXPECTED_NOT_IMPORTED |
| DOC-134 | `docs/modules/achievements.md` | EXPECTED_NOT_IMPORTED |

These modules were authored in the project conversation before this repository was initialized.

---

# 11. Security — PRESENT

| DOC | Path |
|---|---|
| DOC-140 | `docs/security/security-architecture.md` |
| DOC-141 | `docs/security/authentication.md` |
| DOC-142 | `docs/security/authorization.md` |
| DOC-143 | `docs/security/public-and-token-access.md` |
| DOC-144 | `docs/security/secrets-and-provider-security.md` |
| DOC-145 | `docs/security/upload-url-and-content-security.md` |
| DOC-146 | `docs/security/privacy-audit-and-abuse-protection.md` |

---

# 12. Non-Functional Requirements — PRESENT

| DOC | Path |
|---|---|
| DOC-150 | `docs/nfr/non-functional-requirements.md` |
| DOC-151 | `docs/nfr/performance.md` |
| DOC-152 | `docs/nfr/accessibility.md` |
| DOC-153 | `docs/nfr/compatibility.md` |
| DOC-154 | `docs/nfr/seo.md` |
| DOC-155 | `docs/nfr/analytics.md` |

---

# 13. Migrations — PRESENT

| DOC | Path |
|---|---|
| DOC-160 | `docs/migrations/migration-strategy.md` |
| DOC-161 | `docs/migrations/legacy-data-inventory.md` |
| DOC-162 | `docs/migrations/database-migrations.md` |
| DOC-163 | `docs/migrations/media-migrations.md` |
| DOC-164 | `docs/migrations/domain-data-migrations.md` |
| DOC-165 | `docs/migrations/migration-validation-and-rollback.md` |

---

# 14. Testing — PRESENT

| DOC | Path |
|---|---|
| DOC-170 | `docs/testing/testing-strategy.md` |
| DOC-171 | `docs/testing/unit-testing.md` |
| DOC-172 | `docs/testing/integration-testing.md` |
| DOC-173 | `docs/testing/e2e-testing.md` |
| DOC-174 | `docs/testing/security-testing.md` |
| DOC-175 | `docs/testing/performance-testing.md` |
| DOC-176 | `docs/testing/accessibility-testing.md` |
| DOC-177 | `docs/testing/visual-regression-testing.md` |
| DOC-178 | `docs/testing/migration-testing.md` |
| DOC-179 | `docs/testing/release-quality-gates.md` |

---

# 15. Master Requirements — PRESENT

| DOC | Path |
|---|---|
| DOC-180 | `docs/requirements/functional-requirements.md` |
| DOC-181 | `docs/requirements/acceptance-criteria.md` |
| DOC-182 | `docs/requirements/requirements-traceability.md` |

---

# 16. Operations — PRESENT

| DOC | Path |
|---|---|
| DOC-190 | `docs/operations/operations-overview.md` |
| DOC-191 | `docs/operations/deployment.md` |
| DOC-192 | `docs/operations/configuration-and-secrets.md` |
| DOC-193 | `docs/operations/backup-and-restore.md` |
| DOC-194 | `docs/operations/monitoring-and-observability.md` |
| DOC-195 | `docs/operations/incident-response.md` |
| DOC-196 | `docs/operations/maintenance-and-runbooks.md` |

---

# 17. Implementation — PRESENT

| DOC | Path |
|---|---|
| DOC-200 | `docs/implementation/implementation-strategy.md` |
| DOC-201 | `docs/implementation/repository-structure.md` |
| DOC-202 | `docs/implementation/coding-standards.md` |
| DOC-203 | `docs/implementation/database-implementation.md` |
| DOC-204 | `docs/implementation/frontend-implementation.md` |
| DOC-205 | `docs/implementation/backend-worker-implementation.md` |
| DOC-206 | `docs/implementation/implementation-sequencing.md` |

---

# 18. GitHub & Codex — PRESENT

| DOC | Path |
|---|---|
| DOC-220 | `docs/github/github-workflow.md` |
| DOC-221 | `docs/github/issue-and-pr-templates.md` |
| DOC-222 | `docs/github/branch-release-strategy.md` |
| DOC-223 | `docs/github/codex-execution-plan.md` |

---

# 19. Finalization — PRESENT

| DOC | Path |
|---|---|
| DOC-230 | `docs/final/documentation-audit.md` |
| DOC-231 | `docs/final/gap-analysis.md` |
| DOC-232 | `docs/final/final-traceability.md` |
| DOC-233 | `docs/final/final-technical-specification.md` |
| DOC-234 | `docs/final/documentation-index.md` |

---

# 20. Recommended reading order for Codex

For **implementation work**, do not read every file linearly first. Use:

1. `docs/governance.md`;
2. `docs/final/final-technical-specification.md`;
3. `docs/github/codex-execution-plan.md`;
4. `docs/implementation/implementation-sequencing.md`;
5. relevant owning module/domain docs;
6. relevant Security/NFR/Migration/Testing docs;
7. `docs/requirements/functional-requirements.md` + AC/traceability.

For Phase 0 audit also read DOC-230/231/232.

---

# 21. Recommended reading order for architecture review

```text
Product Principles
Domain Model / Ownership / Revisions
Architecture Overview / Data Flows / Projections
Database Architecture
Security Architecture
Final Technical Specification
Master Requirements
```

Earlier files in this sequence require import into current repository.

---

# 22. Recommended reading order for operations

```text
DOC-190 Operations Overview
DOC-191 Deployment
DOC-192 Configuration/Secrets
DOC-193 Backup/Restore
DOC-194 Monitoring
DOC-195 Incident Response
DOC-196 Runbooks
DOC-179 Release Gates
```

---

# 23. Recommended reading order for migration

```text
DOC-161 Inventory
DOC-160 Strategy
DOC-162 DB Migration
DOC-163 Media Migration
DOC-164 Domain Migration
DOC-165 Validation/Rollback
DOC-178 Migration Testing
```

---

# 24. Known repository-completeness warning

The current repository was empty before creation of the remaining package.

Therefore:

```text
DOC-140…234 + DOC-002/003 → present after this finalization
DOC-001…134 earlier project families → require separate import
```

Do not remove this warning until an actual repository audit confirms those files exist.

---

# 25. Index maintenance

When a document is added/moved/superseded:

1. update this index;
2. update DOC-002 navigation if needed;
3. update governance/supersession metadata;
4. run link audit;
5. preserve stable DOC IDs.

---

# 26. Acceptance criteria

`AC-IDX234-001` Every present remaining document is listed by path.  
`AC-IDX234-002` Earlier non-imported files are clearly marked.  
`AC-IDX234-003` Known canonical path overrides are represented.  
`AC-IDX234-004` Reading orders exist for Codex, architecture, operations and migration.  
`AC-IDX234-005` Index never claims implementation verification.  
`AC-IDX234-006` DOC IDs remain stable through moves/supersession.  
`AC-IDX234-007` Repository-completeness warning remains until verified resolved.  
`AC-IDX234-008` Index is updated with documentation changes.

---

## 27. Финальная доктрина

> **DOC-234 is the map of the specification, not a substitute for it. It makes the repository navigable, distinguishes files that truly exist from earlier project documents still awaiting import, and directs Codex/reviewers to the smallest authoritative document set needed for each type of work.**
