# IMPLEMENTATION STRATEGY
## DOC-200 — engineering strategy for translating canonical documentation into production code

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет, как Codex/разработчик должен реализовывать спецификацию без разрушения domain boundaries.

> **Implementation follows domain ownership and invariants first, UI second. The codebase should make forbidden states/actions difficult to express, with Source authority concentrated in typed domain services and all public/provider/AI surfaces constrained adapters around them.**

---

## 2. Architectural baseline

```text
Modular Monolith
+ Background Worker
+ PostgreSQL
+ filesystem/media storage
+ Nginx/HTTPS
```

No microservice split unless later justified by measured operational need.

## 3. Implementation order principle

For each capability:

```text
Domain types/invariants
→ DB schema/constraints
→ repository/query layer
→ domain commands/services
→ projections/DTOs
→ server actions/API
→ UI
→ jobs/providers
→ tests/observability
```

Do not begin with UI tables and retrofit semantics later.

## 4. Module boundary

Each major domain has explicit package/module. Cross-module interaction via IDs/contracts/events/domain service, not direct mutation of another module’s tables from arbitrary UI handler.

## 5. Source ownership

One module owns write path to each Source fact. Other modules may reference/project/snapshot, but do not update the owner table directly.

## 6. Domain services

High-impact commands are named operations:

```text
PublishQuestionnaire
CreateCastingFromInquiry
TransitionOpportunity
ApplyBBDraft
ActivateTheme
ScheduleSocialPost
```

Prefer these over generic CRUD endpoints.

## 7. Server actions/API boundary

Server Action validates/authenticates/parses input then invokes application/domain service. It does not contain duplicate business logic or raw provider calls.

## 8. DTOs

Never serialize ORM model directly to browser. Define surface-specific DTO/projections:

```text
PublicProfileDTO
AdminProfileDTO
BuilderEligibilityDTO
TokenScopedArtifactDTO
```

## 9. Validation layers

```text
client UX validation
→ API schema validation
→ domain validation
→ DB constraints
```

Client does not replace server/domain validation.

## 10. Transactions

Domain application service owns transaction boundary. External providers are called only after commit via durable outbox/job unless operation semantics explicitly require another safe pattern.

## 11. Idempotency

High-risk/public/provider commands accept/use idempotency keys. Store semantic request fingerprint/result to detect changed payload reuse.

## 12. Concurrency

Mutable aggregates use `version`/expectedVersion where stale overwrite matters. Database constraints protect structural race conditions.

## 13. Revisions/snapshots

Immutable revision created from transactionally validated current Source. Renderer/worker always consumes revision/snapshot ID, not mutable current rows for historical artifact.

## 14. Background jobs

PostgreSQL-backed durable queue/outbox baseline:

- at-least-once;
- lease/heartbeat as needed;
- bounded retry;
- idempotent handler;
- final failure state;
- current-state recheck.

## 15. External providers

Provider adapters hide vendor-specific API. Domain uses normalized contracts/status/errors.

No provider SDK objects leak into domain entities.

## 16. AI

Shared AI gateway/adapters, separate capability contracts:

```text
Casting AI
Theme AI
BB Assistant
VOP optional explanation
```

No universal AI agent with generic write tools.

## 17. Media

Original storage immutable/private; derived processing async; contextual crop/config stored separately; public resolver never exposes path.

## 18. PDF/QR

Normalized DocumentModel decouples business snapshot from renderer. QR service accepts validated canonical target and verifies decode.

## 19. Search/cache

Treat as projections. Build/rebuild handlers subscribe to events/generations. Authorization/visibility is applied before indexing/cache publication.

## 20. Security by construction

- allowlist projection;
- typed permission checks;
- URL validators;
- safe provider adapters;
- no raw HTML;
- secrets only server-side;
- upload quarantine.

## 21. Feature flags

May control module availability/provider use, not security invariants or business authority. Disabled feature leaves Source safe.

## 22. Error model

Domain/application errors use stable codes from canonical docs. UI translates codes to user messages; raw stack/provider detail stays internal.

## 23. Logging

Structured metadata/correlation; no raw secrets/private content. Domain command emits useful audit/outbox metadata separately.

## 24. Tests during implementation

Implement tests alongside each layer:

- unit invariants;
- integration DB/renderer/adapter;
- E2E critical journey;
- negative security test.

Do not leave traceability/testing for final sprint.

## 25. Migrations

Every schema change includes migration and, where non-trivial, backfill/validation. No production manual schema drift.

## 26. Compatibility

Preserve existing public routes/data where specified or supply redirects/migration. Breaking contract documented.

## 27. Incremental delivery

Modules can be delivered vertically, but foundational shared contracts are built first:

1. auth/security/config;
2. DB/domain foundations;
3. media/projections;
4. questionnaire/PDF;
5. workflow/AI/provider modules;
6. analytics/operations.

Detailed sequence DOC-206.

## 28. Technical debt rule

If temporary compatibility adapter is needed, isolate and document removal condition. Do not contaminate canonical domain model with legacy naming indefinitely.

## 29. Code review focus

Review asks:

- owning domain correct?
- does this duplicate fact/rule?
- can client/AI/provider bypass authority?
- transaction/idempotency correct?
- private data serialized?
- historical snapshot stable?
- tests/migration/docs included?

## 30. Implementation anti-patterns

Forbidden:

1. Generic `updateAnyEntity` endpoint.
2. UI writes DB directly.
3. ORM entity sent to client.
4. Provider call inside DB transaction.
5. One universal AI service can mutate domains.
6. Business rules duplicated in React/VOP/job.
7. Mutable “history” row.
8. Media path used as public URL.
9. Search/cache used as Source.
10. Feature flag disables authorization.
11. Manual prod schema change absent migration.
12. Implementation merged with no traceable tests/docs.

## 31. Acceptance criteria

`AC-IMPL200-001` Domain/invariant/schema layers precede UI implementation.  
`AC-IMPL200-002` Source ownership is enforced through module write paths.  
`AC-IMPL200-003` High-impact operations are named commands, not generic CRUD.  
`AC-IMPL200-004` DTO/projections prevent ORM/private leakage.  
`AC-IMPL200-005` Transaction/outbox/provider boundaries preserve committed Source.  
`AC-IMPL200-006` Idempotency/concurrency/revisions are first-class.  
`AC-IMPL200-007` Jobs/providers/AI use typed narrow contracts.  
`AC-IMPL200-008` Media/PDF/QR/search/cache preserve source/derived separation.  
`AC-IMPL200-009` Migrations/tests/observability are implemented with features.  
`AC-IMPL200-010` Security/authority cannot be disabled by convenience flag.  
`AC-IMPL200-011` Temporary legacy adapters are isolated/documented.  
`AC-IMPL200-012` PR review and traceability validate owning requirement/AC/test.

---

## 32. Финальная доктрина

> **Implementation translates the documentation into enforceable code structure: domain modules own facts, named commands own mutations, immutable snapshots own history, adapters own external providers, and projections own each public/admin surface. This architecture keeps client, AI, VOP, caches and providers outside the authority boundary by design rather than by convention.**
