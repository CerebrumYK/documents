# REPOSITORY STRUCTURE
## DOC-201 — canonical code/document organization for Modular Monolith + Worker

**Статус:** Canonical / Required

---

## 1. Назначение

Документ предлагает target repository layout для Codex implementation. Exact existing paths must be reconciled with repository audit before destructive moves; structure expresses ownership rules, not permission to delete working code blindly.

> **Directory structure should make domain ownership and runtime boundaries obvious: UI, domain, infrastructure, providers and background jobs are separated without fragmenting the project into unnecessary services.**

---

## 2. Suggested top-level layout

```text
/
  app/                    # Next.js routes/layouts/actions where framework requires
  src/
    modules/
    shared/
    infrastructure/
    workers/
    providers/
  public/                 # static public build assets only
  db/
    migrations/
    seeds/
  scripts/
  tests/
  docs/
  config/
  package.json
  tsconfig.json
```

If current Next.js convention already uses `src/app`, adapt without duplicating app roots.

## 3. Module template

```text
src/modules/<module>/
  domain/
    entities.ts
    value-objects.ts
    states.ts
    invariants.ts
    errors.ts
  application/
    commands/
    queries/
    services/
  infrastructure/
    repository.ts
    mappers.ts
  projections/
  api/
  ui/                     # optional module-specific components
  tests/
```

Not every small module needs every folder; avoid empty ceremony.

## 4. Canonical modules

Suggested:

```text
profile
media
portfolio
emotional-portfolio
emotional-grid
projects
training
skills-languages
professional-links
contacts
questionnaires
public-builder
qr
castings
casting-ai
feedback
notifications
opportunities
themes
bb-assistant
vop
analytics
help
social-publishing
achievements
```

## 5. Shared code

`src/shared` only for genuinely cross-domain primitives:

```text
IDs
Result/Error base
clock
pagination
localization primitives
validation helpers without business ownership
```

Do not move business rules into generic `utils` merely because two modules call them.

## 6. Infrastructure

Cross-cutting technical implementations:

```text
src/infrastructure/db
src/infrastructure/storage
src/infrastructure/jobs
src/infrastructure/cache
src/infrastructure/search
src/infrastructure/logging
src/infrastructure/security
```

## 7. Providers

External vendor adapters isolated:

```text
src/providers/ai
src/providers/email
src/providers/whatsapp
src/providers/instagram
src/providers/tiktok
```

Domain imports provider interfaces/contracts, not raw SDK across codebase.

## 8. Workers

```text
src/workers/handlers/
src/workers/registry.ts
src/workers/runner.ts
```

Each handler is typed by job kind and invokes application service/provider adapter. No dynamic arbitrary function execution.

## 9. Next.js routes

Public routes separated from Admin route group and provider callback routes. Example conceptual:

```text
app/(public)/...
app/admin/...
app/api/webhooks/...
app/api/oauth/...
```

Actual syntax follows framework version.

## 10. Server Actions

Keep thin adapters near route/module; shared application command lives module application layer. Avoid business logic in page components.

## 11. UI components

```text
src/shared/ui/           # design-system primitives
src/modules/*/ui/       # domain-specific widgets/editors
```

Do not couple low-level shared button to Profile/Casting semantics.

## 12. Database migrations

All schema migrations under one ordered canonical directory. No per-feature alternative migration runners.

## 13. SQL/repositories

Repository implementation belongs module/infrastructure and returns domain/application records/DTO, not raw SQL rows to UI.

## 14. Tests

Suggested:

```text
tests/e2e/
tests/security/
tests/performance/
tests/accessibility/
tests/migrations/
```

Unit/integration tests may co-locate with module where productive.

## 15. Fixtures

```text
tests/fixtures/media/
tests/fixtures/legacy/
tests/factories/
```

No production data/secrets.

## 16. Documentation

Canonical docs live under `docs/` using families:

```text
product/
domain/
architecture/
database/
api/
modules/
security/
nfr/
migrations/
testing/
requirements/
operations/
implementation/
github/
final/
```

## 17. Scripts

`scripts/` contains bounded operational/development utilities. Production maintenance scripts must follow runbook/safety conventions and not become undocumented generic DB mutators.

## 18. Config

Schema/default definitions can live code/config; real secrets outside repository. Example config files contain placeholders only.

## 19. Generated files

Do not commit runtime-generated media/PDF/cache/search data. `.gitignore` covers local generated directories/secrets.

## 20. Persistent production data

`/data/kate-actor` is outside repository checkout/release lifecycle.

## 21. Import boundaries

Preferred dependency direction:

```text
UI/API
 ↓
Application
 ↓
Domain

Infrastructure implements interfaces used by Application/Domain.
Providers are infrastructure adapters.
```

Domain does not import React/Next.js/provider SDK.

## 22. Cross-module dependencies

Use application/query contracts or events. Avoid module A directly importing module B internal repository and updating its table.

## 23. Naming

Use domain vocabulary from `terminology.md`; avoid duplicate synonyms (`award`/`achievement` tables) unless semantics distinct.

## 24. Legacy directories

During refactor, move incrementally. Compatibility facade may preserve old imports temporarily with issue/removal milestone.

## 25. Circular dependency prevention

Shared primitives cannot import domain modules. Module interaction through contracts/events rather than bidirectional entity imports where possible.

## 26. Public vs server-only code

Provider secrets/DB/storage code must live server-only modules and never be imported by client components. Use framework directives/build checks as appropriate.

## 27. Documentation adjacency

Code comments reference requirement IDs sparingly for non-obvious invariants; canonical prose stays docs, not duplicated giant comments.

## 28. Anti-patterns

Forbidden:

1. `utils.ts` contains business rules for everything.
2. React component imports Prisma/DB client.
3. Domain imports Next.js/provider SDK.
4. Worker uses eval/dynamic function name.
5. One `services.ts` with all domains.
6. Two migration systems.
7. Runtime generated media committed to Git.
8. Production `/data` inside release directory.
9. Real `.env` committed.
10. Cross-module table writes through private repository implementation.
11. Empty architecture folders created for ceremony only.

## 29. Acceptance criteria

`AC-STRUCT-001` Repository layout exposes module/domain ownership.  
`AC-STRUCT-002` Domain/application code is framework/provider-independent where practical.  
`AC-STRUCT-003` UI/server actions do not directly access DB/provider secrets.  
`AC-STRUCT-004` External providers are isolated adapters.  
`AC-STRUCT-005` Worker handlers are typed/registered.  
`AC-STRUCT-006` One canonical migration chain exists.  
`AC-STRUCT-007` Public/Admin/provider route groups are clearly separated.  
`AC-STRUCT-008` Cross-module dependencies use contracts/events rather than arbitrary table mutation.  
`AC-STRUCT-009` Persistent/generated/secret data is excluded from repository.  
`AC-STRUCT-010` Existing code is migrated incrementally without blind destructive restructuring.

---

## 30. Финальная доктрина

> **The repository is organized around domain ownership inside one modular application, with infrastructure/provider/worker concerns pushed to explicit edges. This gives Codex a physical code map that mirrors the documentation and makes accidental DB/provider/client boundary violations visible during review.**
