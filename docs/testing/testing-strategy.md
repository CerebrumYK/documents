# TESTING STRATEGY
## DOC-170 — test pyramid, risk-based coverage, fixtures, environments и traceability

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт общую testing strategy проекта и распределяет ответственность между unit, integration, E2E, security, performance, accessibility, visual и migration tests.

> **Every critical invariant must be proven at the cheapest reliable layer, while end-to-end tests prove that trust boundaries and professional workflows remain correct when all layers are assembled. Tests validate both successful paths and prohibited actions.**

---

## 2. Test layers

```text
UNIT
INTEGRATION
E2E
SECURITY
PERFORMANCE
ACCESSIBILITY
VISUAL_REGRESSION
MIGRATION
MANUAL_EXPLORATORY
```

## 3. Pyramid

Most deterministic business rules belong unit tests; DB/adapter contracts integration; critical journeys E2E. Avoid putting every edge case into slow browser tests.

## 4. Risk-based priority

Highest testing priority:

1. authentication/authorization;
2. public/private visibility;
3. professional Source integrity;
4. immutable revisions/snapshots;
5. payment-like/external side effects (social/notifications) idempotency;
6. migrations/deletion/restore;
7. PDF/QR correctness;
8. media originals;
9. business workflows;
10. UX refinements.

## 5. Positive and negative coverage

For each command include tests for:

```text
happy path
validation failure
unauthorized
wrong profile
wrong lifecycle
stale version
idempotency retry
concurrency race where relevant
```

## 6. Invariant traceability

Test IDs map to document invariants/acceptance criteria. Final traceability DOC-182/232 records relationship.

Suggested naming:

```text
UT-<DOMAIN>-NNN
IT-<DOMAIN>-NNN
E2E-<DOMAIN>-NNN
SEC-TEST-NNN
```

## 7. Test data doctrine

Fixtures use synthetic professional data, never production PII/secrets. Media fixtures are small legal test assets specifically intended for testing.

## 8. Determinism

Tests avoid dependence on current date/provider/network unless explicitly controlled. Clock, UUID/randomness and external provider clients are injectable/fakeable where needed.

## 9. Database tests

Use isolated test DB/schema with real PostgreSQL behavior for constraints/transactions. Mocking ORM alone is insufficient for DB invariants.

## 10. Provider tests

Use adapter fakes/contracts for normal CI. Separate opt-in sandbox smoke tests may verify real provider integration without making CI dependent on provider availability.

## 11. AI tests

Separate:

- deterministic application contract tests;
- schema/grounding validators;
- model evaluation suites.

Core tests must pass with AI provider disabled/faked.

## 12. Time

Use fake clock for quiet hours, scheduling, deadlines, expiry, retention. Explicit timezone/DST cases required.

## 13. Concurrency

Test double submits and races:

- publish;
- Builder Generate;
- Feedback submit;
- CreateCastingFromInquiry;
- Opportunity transition;
- Social publish;
- background lease/job;
- current-primary changes.

## 14. Idempotency

For each idempotent command:

1. same key + same semantic request → same logical result;
2. same key + different semantic request → conflict;
3. lost response + retry → one side effect.

## 15. Immutable history

Tests prove current Source changes never rewrite:

- QuestionnaireRevision;
- Builder GenerationSnapshot;
- CastingAnalysisRevision;
- BB SourceSnapshot/history;
- Social PublishSnapshot;
- historical QR target.

## 16. Public projection tests

Assert forbidden fields are **absent**, not `null` or hidden CSS. Include raw JSON/RSC response inspection where relevant.

## 17. Media tests

Verify:

- original hash unchanged;
- derivative generation;
- crop config boundaries;
- access class;
- range/download behavior;
- corrupt/unsupported input handling.

## 18. PDF/QR tests

Automate:

- text extraction/selectability;
- hyperlink target;
- QR decode exact equality;
- no Admin/storage URLs;
- required blocks.

## 19. Snapshot tests

Use textual/structural snapshots carefully. Never approve huge opaque snapshots without semantic assertions.

## 20. Flakiness policy

A flaky test is a defect. Quarantine only temporarily with owner/issue/deadline; do not normalize rerun-until-green as acceptance.

## 21. CI stages

Suggested:

```text
lint/typecheck
unit
integration
build
E2E critical
security/static checks
accessibility
performance smoke/budgets
migration validation when schema changes
```

Heavy extended suites may run pre-release/nightly, but security/critical E2E remain blocking.

## 22. Coverage metrics

Line/branch coverage is signal, not goal. Critical domain invariants require explicit tests regardless percentage.

## 23. Manual exploratory testing

Required for:

- nuanced visual/mobile behavior;
- actual PDF print/scan spot check;
- screen-reader paths;
- provider sandbox after integration changes;
- high-risk migration rehearsal.

## 24. Test environment

Environment mirrors production architecture enough to test PostgreSQL, filesystem, worker and Nginx/security assumptions. No Supabase-specific mocks because project does not use Supabase.

## 25. Seed/reset

Test environment can reset deterministically. Production data never imported casually into automated CI.

## 26. Browser matrix

Automated E2E uses representative Chromium plus cross-browser subset according to DOC-153.

## 27. Observability assertions

Where useful, tests assert failed provider/job produces status/error without losing Source and no secret in logs.

## 28. Restore tests

Periodic integration/release exercise proves restored DB/media starts safely without replaying external sends.

## 29. Documentation tests

Links/paths in docs may be linted; requirements IDs/duplicate IDs checked where tooling exists.

## 30. Definition of Done

Feature is done when:

- code tests appropriate layer;
- negative authority cases tested;
- docs/AC updated;
- migration added/tested if needed;
- observability included;
- no known blocking accessibility/security regression.

## 31. Anti-patterns

Forbidden:

1. Only happy-path tests.
2. Every rule tested only through browser.
3. Mock DB for all transaction/constraint tests.
4. CI calls real AI/provider for correctness.
5. Production secrets in fixtures.
6. Public visibility test only checks UI hidden element.
7. Flaky test solved by 5 retries forever.
8. Snapshot replaces semantic assertions.
9. 90% coverage claimed as proof of security.
10. Migration merged without test/rehearsal.
11. Restore never tested.
12. Docs requirements have no test mapping.

## 32. Acceptance criteria

`AC-TEST170-001` Each critical invariant maps to at least one reliable test layer.  
`AC-TEST170-002` Tests cover negative authorization/state paths.  
`AC-TEST170-003` Synthetic fixtures contain no production secrets/PII.  
`AC-TEST170-004` DB constraints/transactions are tested with PostgreSQL.  
`AC-TEST170-005` AI/provider core tests use deterministic adapters/fakes.  
`AC-TEST170-006` Idempotency/concurrency/immutable-history cases are explicit.  
`AC-TEST170-007` Public projection tests assert hidden fields absent from serialized output.  
`AC-TEST170-008` PDF/QR/media have automated semantic tests.  
`AC-TEST170-009` Flaky tests have active remediation rather than permanent retries.  
`AC-TEST170-010` CI has blocking critical suites.  
`AC-TEST170-011` Restore/migration/provider changes receive dedicated validation.  
`AC-TEST170-012` Definition of Done includes docs and test traceability.

---

## 33. Финальная доктрина

> **Testing is a proof system for documented invariants. Cheap deterministic rules are tested close to the domain, real infrastructure contracts are exercised in integration, and E2E/security suites prove that assembled surfaces cannot leak, overwrite, duplicate or autonomously perform forbidden professional actions.**

## Canonical test tooling

The project test implementation is singular per DOC-208: **Vitest** for unit/integration orchestration, real **PostgreSQL** for database semantics, **Playwright** for E2E/cross-browser/visual, **axe-core** for automated accessibility and **Lighthouse CI** plus controlled browser measurements for public performance. Jest/Cypress/SQLite are not parallel baseline substitutes.
