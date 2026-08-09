# IMPLEMENTATION SEQUENCING
## DOC-206 — dependency-aware delivery order, milestones and Codex execution boundaries

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт рекомендуемую последовательность implementation, чтобы foundational security/data contracts появились до dependent UI/AI/provider features.

> **Implementation order follows dependency and risk, not screen order. Foundational identity, Source ownership, revisions, media, jobs and access projections are built before modules that depend on them, while optional AI/providers are added only after deterministic manual workflows are complete.**

---

## 2. Sequencing principles

1. inspect existing repository before restructuring;
2. preserve working behavior/migrations;
3. build deterministic/manual path first;
4. security/auth before public write/provider surfaces;
5. DB/domain before UI;
6. immutable snapshots before PDF/AI/social use;
7. jobs/outbox before asynchronous providers;
8. tests/migrations with each phase;
9. no giant “implement everything” PR.

## 3. Phase 0 — Repository audit and guardrails

Deliver:

- actual code/file inventory;
- framework/runtime versions;
- current DB schema/migrations;
- current routes/server actions;
- current deployment assumptions;
- test/lint/typecheck baseline;
- secret/config scan;
- mapping legacy paths to DOC-201 target structure.

Do not move/delete broad directories before this phase.

### Exit gate

Existing build/test state documented; no unknown production-critical data path.

## 4. Phase 1 — Shared foundations

Deliver:

- typed IDs/errors/results/pagination/clock;
- configuration schema;
- structured logging/correlation;
- PostgreSQL transaction/repository foundation;
- migration framework;
- authentication session foundation;
- authorization policies;
- Audit + Outbox foundations.

### Exit gate

Authenticated Admin and public projection can coexist without data leakage; transaction/outbox tests pass.

## 5. Phase 2 — Core professional Source

Implement/refactor:

- Profile;
- Media Library;
- Main Portfolio;
- Projects/Roles;
- Training;
- Skills/Languages;
- Contacts;
- Achievements.

Include independent visibility flags and migration from legacy data.

### Exit gate

Core professional Profile works manually with AI/providers disabled.

## 6. Phase 3 — Emotional portfolio/grid

Implement:

- Emotional Portfolio;
- shooting dates/source originals;
- exact Grid presets/counts;
- per-cell crop controls;
- Human confirmation;
- derived composite pipeline.

### Exit gate

Finalized grid reliably renders and originals remain immutable.

## 7. Phase 4 — Professional links/media delivery

Implement:

- ProfessionalLink separation;
- safe URL/canonicalization;
- stable public media resolvers;
- SSRF-safe health checker;
- provider embed allowlist;
- external link editor exact columns.

### Exit gate

Clickable safe links/media are available without filesystem leakage.

## 8. Phase 5 — Questionnaire foundation

Implement:

- QuestionnaireDefinition/Draft;
- readiness rules;
- immutable QuestionnaireRevision;
- primary public semantics;
- DocumentModel;
- HTML renderer;
- PDF generation;
- QR service/verification.

### Exit gate

Compact/Extended/Casting/Custom questionnaires render deterministic PDF with clickable links/exact QR and historical immutability.

## 9. Phase 6 — Public Builder

Implement:

- Builder eligibility projection;
- templates QUICK/STANDARD/EXTENDED/CASTING;
- temporary sessions/TTL;
- preview;
- Generate revalidation;
- immutable GenerationSnapshot;
- scoped artifact access;
- anti-abuse.

### Exit gate

Forged hidden IDs cannot leak; generated PDF matches frozen eligible snapshot.

## 10. Phase 7 — Feedback + Notifications

Implement Feedback first:

- public write-only form;
- private attachments;
- Inbox/workflow/read/assignment/notes/next action;
- transaction+Audit+Outbox.

Then Notifications:

- recipients/endpoints/preferences;
- In-App;
- Email/WhatsApp adapters;
- quiet hours;
- retries/receipts.

### Exit gate

Feedback remains successful/durable with all external notification providers disabled/failing.

## 11. Phase 8 — Castings + Opportunity

Implement deterministic domains:

- Casting Source/requirements/materials/provenance;
- explicit Feedback→Casting;
- Opportunity stages/history/next action/outcome;
- explicit Booked→Project conversion.

### Exit gate

Complete Human workflow exists without AI.

## 12. Phase 9 — AI infrastructure and Casting AI

Implement shared AI gateway/provider adapter then Casting AI:

- immutable SourceSnapshot;
- structured schema;
- evidence;
- revision history;
- Human review/confirmation;
- stale detection.

### Exit gate

AI cannot mutate Source or function when provider disabled; manual workflow remains complete.

## 13. Phase 10 — BB Assistant

Implement task contracts, snapshots, factual validators, Draft history, Human Apply and target integration.

### Exit gate

Generate/Apply/Publish/Send are structurally separate and unsupported claims are detected.

## 14. Phase 11 — Theme system

Implement structured Theme model/manual editor/preview/accessibility validation/publish/activate/rollback. Add Theme AI only after manual theme workflow is complete.

### Exit gate

Theme activation is atomic/accessibility-safe; AI only creates Draft.

## 15. Phase 12 — VOP

Implement deterministic health/readiness checks first, Finding/recommendation lifecycle, then allowlisted safe actions. AI natural-language explanation optional last.

### Exit gate

Hard deny business authority is test-proven.

## 16. Phase 13 — Help Center/Tickets

Implement RU Help Center, tickets, private attachments, public/internal messages, assignment/SLA and notification integration.

### Exit gate

Support remains isolated from professional Feedback/business domains.

## 17. Phase 14 — Social Publishing

Prerequisites: Media eligibility/rights, jobs/outbox, OAuth/secrets, BB Draft, Notifications, provider adapter framework.

Implement:

- social accounts/OAuth;
- Draft/media/caption validation;
- approval fingerprint;
- immutable PublishSnapshot;
- durable schedule;
- PublishAttempt/reconciliation/webhooks.

### Exit gate

Provider unknown outcome cannot duplicate post; AI/VOP cannot publish.

## 18. Phase 15 — Analytics

Implement event registry/ingestion after domain event contracts stabilize. Add attribution/funnels/share tracking and dashboards.

### Exit gate

Analytics is privacy-minimized/read-only and business conversions come from domain commits.

## 19. Phase 16 — Operational hardening

Complete:

- monitoring/alerts;
- backup/restore automation;
- security hardening/CSP/rate limits;
- performance budgets;
- accessibility/cross-browser suites;
- runbooks;
- migration rehearsal;
- release gates.

## 20. Phase 17 — Final audit

Use DOC-230…234:

- repository/document audit;
- gap analysis;
- actual traceability;
- final technical specification;
- documentation index/governance.

## 21. Cross-phase rule: migrations

Every phase changing persisted structure includes DB/data migration in same implementation slice. Do not postpone all migration work until the end.

## 22. Cross-phase rule: tests

Every phase includes unit/integration/critical E2E/security tests relevant to its trust boundary.

## 23. Cross-phase rule: manual fallback

AI/provider phase cannot become prerequisite for deterministic manual professional workflow.

## 24. PR sizing

Recommended PR types:

```text
foundation/schema
single domain vertical slice
migration/backfill
provider adapter
UI integration
hardening/testing
```

Avoid PR simultaneously refactoring entire repo + migrating data + adding AI/provider feature.

## 25. Codex task format

Every Codex implementation task should specify:

```text
Goal
Relevant DOC/FR/AC IDs
Existing code to inspect first
Allowed scope
Forbidden regressions
DB/migration impact
Tests required
Definition of Done
```

## 26. Dependency blockers

Examples:

- QR requires stable canonical/public resolver;
- Builder requires Questionnaire/Portfolio eligibility;
- Social requires durable worker/OAuth/media-rights;
- VOP safe retry requires job idempotency;
- Analytics business funnel requires Opportunity provenance;
- BB/Casting AI require AI architecture/snapshot contracts.

## 27. Parallel work

Safe parallelization only across low-coupling modules after shared contracts merge. Database migrations touching same tables should be coordinated to avoid conflict.

## 28. Legacy compatibility

During phases, compatibility adapters preserve current production until cutover. Every adapter has removal issue/condition.

## 29. Stop conditions

Pause next dependent phase when:

- security/privacy invariant unresolved;
- schema migration unvalidated;
- current Source ownership ambiguous;
- mandatory tests unstable;
- provider unknown side effect unresolved;
- foundational API contract still changing rapidly.

## 30. Definition of Done per phase

```text
code
migrations
positive/negative tests
observability
security/privacy review
docs/traceability
no new high-severity gap
```

## 31. Anti-patterns

Forbidden:

1. Implement Social before durable jobs/idempotency.
2. Build Builder before server eligibility projection.
3. Add AI before manual Casting/BB target workflow exists.
4. Build PDF before immutable Revision semantics.
5. Implement VOP “auto fix” before domain authority defined.
6. Analytics event names before business events stable, then reinterpret historical data silently.
7. One mega-PR implements phases 1–15.
8. Postpone security/accessibility/migrations entirely to final hardening.
9. Delete legacy paths before validated cutover.
10. Parallel migrations conflict on same schema unnoticed.

## 32. Acceptance criteria

`AC-SEQ-001` Repository audit precedes broad refactor.  
`AC-SEQ-002` Auth/config/DB/audit/outbox foundations precede public write/provider modules.  
`AC-SEQ-003` Core professional/manual domains work before AI.  
`AC-SEQ-004` Questionnaire immutable revisions precede Builder/PDF-dependent workflows.  
`AC-SEQ-005` Feedback persistence precedes external notifications.  
`AC-SEQ-006` Human Casting/Opportunity workflow precedes Casting AI enhancements.  
`AC-SEQ-007` Durable jobs/OAuth/media rights precede Social Publishing.  
`AC-SEQ-008` Analytics follows stable domain event/provenance contracts.  
`AC-SEQ-009` Migrations/tests/docs occur within each phase, not at the end.  
`AC-SEQ-010` PRs remain scoped and dependency-aware.  
`AC-SEQ-011` Stop conditions prevent building atop unresolved authority/data gaps.  
`AC-SEQ-012` Final audit validates actual repository against all documented phases.

---

## 33. Финальная доктрина

> **Implementation proceeds from authority and data foundations outward: first secure Source and revisions, then deterministic professional workflows, then durable asynchronous infrastructure, and only then AI/providers/analytics. This sequence ensures optional intelligence and automation enhance a complete manual system instead of becoming hidden dependencies for correctness.**

## Enterprise campaign interpretation

DOC-225 wraps all phases in this document into **one continuous Enterprise implementation campaign**. The existing prohibition on a giant “implement everything” PR remains valid: one campaign means no scope abandonment or repeated user prompts between green phases, while phase gates, atomic commits/checkpoints and dependency-safe migrations remain mandatory.
