# UNIT TESTING
## DOC-171 — deterministic domain rules, validators, state machines и pure transformations

**Статус:** Canonical / Required

---

## 1. Назначение

Unit tests prove deterministic business rules without network, real provider or full application stack.

> **A domain rule that can be decided from explicit inputs should be unit-tested directly rather than relying on slow UI tests to discover it indirectly.**

---

## 2. Unit scope

Primary targets:

- value objects/normalizers;
- validators;
- controlled enum semantics;
- state transition functions;
- readiness calculations;
- visibility policy functions;
- URL canonicalization classification;
- QR size/fingerprint logic;
- notification quiet-hours policy;
- attribution/funnel math;
- migration mapping functions;
- AI output schema/claim validators;
- permission policy pure functions.

## 3. What is not unit scope

Real PostgreSQL transactions/FKs, filesystem codecs, browser, provider API and actual PDF render belong other layers.

## 4. Table-driven tests

Controlled mappings should use exhaustive tables.

Examples:

```text
Skill levels 1..5 valid; 0/6/decimal invalid
Languages Native/A1…C2 valid; arbitrary text invalid
Achievement status labels preserve semantics
Emotional Grid supported sizes exact
```

## 5. State machine tests

For each state machine test allowed and forbidden transitions. Generate matrix where practical.

Examples:

- Feedback workflow;
- Opportunity stages;
- Help Ticket lifecycle;
- Social Draft/attempt;
- Notification delivery;
- QR lifecycle.

## 6. Visibility tests

Given independent flags, assert surface eligibility independently. Public=true never implies Builder=true.

## 7. Source ownership

Test functions that classify entity ownership/reference/provenance and forbid same-profile violations before persistence where deterministic.

## 8. URL tests

Canonicalization cases:

- HTTPS normalization;
- unsafe schemes;
- credentials in URL;
- Admin/internal path classification;
- redirect not automatically canonical target;
- stable URL comparison.

SSRF network resolution itself belongs security/integration, but IP-range classifier can be unit-tested exhaustively.

## 9. QR tests

Unit-test:

- fingerprint inputs;
- minimum physical size from module count;
- quiet-zone setting;
- target/access classification;
- current-vs-historical key semantics.

Actual decode is integration/E2E.

## 10. Document model tests

Pure transformation from immutable QuestionnaireRevision/BuilderSnapshot to normalized DocumentModel should be deterministic and preserve links/order/show_qr flags.

## 11. AI validation

Use fixed structured AI outputs to test:

- unsupported claims;
- source reference validation;
- `Не указано` missing requirement handling;
- match status canonicalization;
- BB semantic inflation blocks.

No live LLM required.

## 12. Notification policy

Unit-test:

- category/channel preference;
- quiet hours in IANA timezone;
- DST boundary;
- severity/override rule;
- retry classification;
- recipient policy.

## 13. Analytics math

Unit-test conversion rate numerator/denominator, attribution precedence, unique-session dedupe definitions, sampled metric logic where used.

## 14. Migration mapping

Every automatic legacy mapping has unit test proving exact supported inputs and ambiguity fallback. This is especially required for Languages/Achievements/Training classification.

## 15. Deterministic time

Inject/freeze clock. Never use real `now()` unpredictably in tests.

## 16. Random IDs/tokens

Test shape/uniqueness properties through injectable generator; do not snapshot random values without control.

## 17. Property-based tests

Useful for:

- URL parser invariants;
- ordering/reorder algorithms;
- pagination cursor encoding;
- token/fingerprint functions;
- crop bounds.

## 18. Assertions

Prefer behavior assertions over implementation internals. Refactor should not break tests if semantics unchanged.

## 19. Mocking

Mock only boundary collaborators. Do not mock the function under test through an elaborate chain that proves nothing.

## 20. Speed

Unit suite should remain fast enough to run on every local/CI change. Slow I/O tests belong integration.

## 21. Coverage expectations

High branch coverage expected for validators/state machines/security classifiers. Coverage threshold is secondary to explicit invariant list.

## 22. Naming

Tests reference invariant/AC where useful:

```text
UT-QNR-PRIMARY-001
UT-AUTHZ-VIS-003
UT-ACH-STATUS-004
```

## 23. Anti-patterns

Forbidden:

1. Unit test calls live OpenAI/social provider.
2. Real sleep for quiet-hours test.
3. Only happy transitions tested.
4. Huge snapshots with no semantic assertion.
5. Mocking validator return value instead of testing validator.
6. Current time/randomness makes flaky tests.
7. Legacy mapping ambiguity untested.
8. Unit test used as proof of DB unique constraint actually working.

## 24. Acceptance criteria

`AC-UNIT-001` Core validators/state machines have exhaustive positive/negative unit coverage.  
`AC-UNIT-002` Visibility flags are tested independently.  
`AC-UNIT-003` URL/access/SSRF classifiers have edge-case coverage.  
`AC-UNIT-004` AI factual/semantic validators use deterministic fixtures.  
`AC-UNIT-005` Notification time/DST policy is deterministic.  
`AC-UNIT-006` Migration mappings prove exact supported and ambiguous inputs.  
`AC-UNIT-007` Clock/randomness is controllable.  
`AC-UNIT-008` Unit suite performs no real external I/O.  
`AC-UNIT-009` Critical invariants are named/traceable.  
`AC-UNIT-010` Unit suite remains fast and blocking in CI.

---

## 25. Финальная доктрина

> **Unit tests are the exhaustive executable specification of deterministic domain decisions: what values are valid, which transitions exist, which visibility applies and when automation is forbidden. They run quickly, without external I/O, and make ambiguity explicit before it reaches database or browser workflows.**
