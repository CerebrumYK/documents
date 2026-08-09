# CODING STANDARDS
## DOC-202 — TypeScript, naming, errors, validation, transactions, security and code-review standards

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт engineering conventions для реализации спецификации.

> **Code style serves semantic clarity: types, names and module boundaries should expose domain invariants and authority. Clever generic abstractions that hide ownership, errors or side effects are discouraged.**

---

## 2. TypeScript

Use strict TypeScript. Avoid `any`; if boundary data is unknown, use `unknown` then validate/narrow.

No non-null assertion to suppress a real domain uncertainty without proof.

## 3. Naming

Use canonical domain vocabulary:

```text
QuestionnaireRevision
BuilderGenerationSnapshot
CastingRequirement
NotificationDelivery
SocialPublishAttempt
```

Avoid vague names such as `item`, `data`, `status2`, `manager` when domain meaning known.

## 4. IDs

Strongly distinguish IDs by type where practical (branded types/value objects) to reduce cross-entity mistakes.

## 5. DTOs

Input DTOs validated. Output DTOs explicit. Do not spread ORM row blindly into API response.

## 6. Runtime validation

All external/client/provider/DB-untrusted boundary payloads validated with canonical schema library/pattern. Types alone are compile-time, not runtime trust.

## 7. Enums/statuses

Use centralized controlled values from owning module. Avoid magic strings repeated across UI/job/DB.

## 8. State transitions

Implement through named transition function/service, not arbitrary assignment:

```ts
transitionOpportunity(...)
```

rather than direct `stage = request.stage`.

## 9. Error codes

Use stable typed application/domain error:

```text
code
safe message/localization key
metadata safe
cause internal
```

Provider/raw DB exception is normalized before client.

## 10. Exceptions

Unexpected programmer/infrastructure exception logs correlation and returns generic internal error. Do not catch-and-ignore.

## 11. Result pattern

Use project-consistent error approach (exceptions/result types) but do not mix unpredictably. Expected validation/state conflicts must remain distinguishable from system failures.

## 12. Transactions

Transaction boundary in application service. No external provider/network call inside transaction.

## 13. Repository methods

Repository names express intent (`findEligibleForBuilder`, `saveRevision`) and return bounded data. Avoid `findAll()` unbounded default.

## 14. SQL

Always parameterized. Dynamic sort/filter uses allowlisted column mapping, never raw user SQL fragments.

## 15. Concurrency

When version is required, method signature includes expected version and update uses atomic compare/check.

## 16. Idempotency

Shared utility can store/check key + semantic fingerprint + result, but owning command defines semantic identity.

## 17. Immutability

Revision/Snapshot types are readonly by design. No generic update repository for immutable tables.

## 18. Date/time

Use explicit clock abstraction in domain/application for testability. Store instants/timezones correctly. Avoid scattered `new Date()` business decisions.

## 19. Money/commercial values

If later introduced, avoid floating point and inventing currency. Not core current requirement.

## 20. URLs

Use central URL parser/policies, not regex fragments repeated in components. Server-fetch URL policy distinct from navigation URL policy.

## 21. Filesystem

Use storage abstraction with generated keys and root containment check. Never concatenate user filename into absolute target path.

## 22. Logging

Use structured logger, never ad-hoc sensitive `console.log`. Log IDs/codes, not full private payload.

## 23. Secrets

Server-only config accessor prevents accidental client import. Never commit/fallback to hardcoded production secret.

## 24. React components

Keep rendering/presentation separate from domain mutation. Client components call typed server/API boundary, not repository/provider.

## 25. Forms

Form schema shares/derives from application input contract where practical. Client errors improve UX, server revalidates.

## 26. Accessibility

Native semantic HTML first. Interactive custom components must implement keyboard/focus/ARIA patterns.

## 27. CSS/theme

Use design tokens/Theme variables rather than hardcoded inconsistent values in module components unless token intentionally absent. Security/QR/accessibility invariants cannot be overridden by theme CSS.

## 28. AI prompts

Prompts/contracts versioned and located with owning AI capability. Do not scatter giant prompt strings through UI handlers.

## 29. Provider SDKs

Imported only inside provider adapters. Normalize SDK responses/errors.

## 30. Background jobs

Job payload is versioned/narrow and serializable. No ORM object/credential/functions in payload.

## 31. Comments

Explain why/invariant/tradeoff, not restate obvious code. Reference `FR-*`/`INV-*` for security/business non-obvious constraints.

## 32. Formatting/lint

Use repository-standard formatter/linter automatically. No style-only debate in PR when tool can enforce.

## 33. Dead code

Remove obsolete code or mark explicit compatibility adapter with removal issue. Avoid commented-out large blocks.

## 34. Dependencies

Add dependency only for justified need. Prefer maintained libraries; review license/security/bundle impact.

## 35. Tests

Test names describe behavior. Arrange/Act/Assert or similarly clear structure. Avoid tests depending on execution order.

## 36. Code review checklist

- canonical names/owner?
- runtime validation?
- authorization?
- transaction/provider boundary?
- version/idempotency?
- public DTO minimization?
- immutable history safe?
- no secrets/log leakage?
- accessibility?
- tests/docs/migration?

## 37. Anti-patterns

Forbidden:

1. `as any` to silence domain mismatch.
2. `catch {}` ignored failures.
3. generic mutable repository for revisions.
4. raw ORM JSON response.
5. user sort string interpolated into SQL.
6. provider SDK used in React component.
7. `process.env` read throughout client code.
8. raw user filename path concat.
9. direct status assignment bypass transition service.
10. current date hardcoded in business test.
11. duplicated URL/visibility/business rule in multiple layers.
12. giant generic helper abstraction hiding side effects.

## 38. Acceptance criteria

`AC-CODE-001` Strict TypeScript and runtime validation are enabled.  
`AC-CODE-002` Canonical domain names/states are centralized.  
`AC-CODE-003` DTOs are explicit and ORM rows are not serialized directly.  
`AC-CODE-004` State changes use named domain/application commands.  
`AC-CODE-005` Errors are normalized/stable/secret-safe.  
`AC-CODE-006` External calls do not occur inside DB transactions.  
`AC-CODE-007` Concurrency/idempotency/immutability are visible in APIs/types.  
`AC-CODE-008` URL/filesystem/provider boundaries use centralized safe abstractions.  
`AC-CODE-009` UI does not own business mutation/provider logic.  
`AC-CODE-010` Logs/config handling prevent secret/private leakage.  
`AC-CODE-011` Formatter/lint/tests are automated.  
`AC-CODE-012` Review checks docs/migrations/tests alongside code.

---

## 39. Финальная доктрина

> **Coding standards make the architecture executable: strict types expose uncertainty, runtime schemas validate trust boundaries, named commands expose side effects, immutable types protect history and provider/filesystem/database concerns remain behind narrow server-only adapters.**

## Canonical toolchain binding

DOC-208 resolves generic implementation choices: Zod is the runtime schema layer; Pino is the structured logger; OpenTelemetry is the telemetry API/SDK; pnpm is the package manager; ESLint + Prettier are the lint/format baseline; Vitest and Playwright are the canonical automated test frameworks. Equivalent examples in older documents do not authorize a parallel production toolchain.
