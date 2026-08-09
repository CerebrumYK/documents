# NON-FUNCTIONAL REQUIREMENTS
## DOC-150 — системные NFR, SLO/quality attributes и общие measurable gates

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт общую модель non-functional requirements проекта: performance, availability, reliability, scalability, accessibility, compatibility, SEO, security, privacy, maintainability, observability и recoverability.

> **Functional correctness is necessary but insufficient. Every production feature must satisfy measurable quality attributes appropriate to its surface, risk and workload.**

---

## 2. Quality attributes

Canonical set:

```text
SECURITY
PRIVACY
PERFORMANCE
RELIABILITY
AVAILABILITY
RECOVERABILITY
ACCESSIBILITY
COMPATIBILITY
SEO
MAINTAINABILITY
OBSERVABILITY
SCALABILITY
DATA_INTEGRITY
USABILITY
```

## 3. Priority order

When requirements conflict, default priority:

1. safety/security/privacy/data integrity;
2. professional correctness/history;
3. availability/recoverability;
4. accessibility;
5. performance;
6. convenience/visual enhancement.

Example: system must not expose private media merely to achieve lower latency.

## 4. Environment baseline

Production architecture:

```text
Next.js / TypeScript
PostgreSQL
filesystem/media storage under /data/kate-actor
background worker
Nginx/TLS
application port 3336
```

NFR targets assume this architecture unless revised by deployment documentation.

## 5. Availability classes

### Core public surface
Profile/public portfolio/public Questionnaire should degrade gracefully when optional providers fail.

### Admin core
CRUD/source management should remain usable when AI/social/notification providers unavailable.

### Optional integrations
AI, Social Publishing and external delivery may be degraded independently.

### NFR-INV-001
Optional provider failure must not make authoritative Source unavailable.

## 6. Reliability

Required architectural mechanisms:

- transactions for authoritative writes;
- outbox for post-commit side effects;
- durable background jobs;
- idempotency;
- optimistic concurrency;
- immutable revisions/snapshots;
- retry classification;
- unknown-outcome reconciliation.

## 7. Data integrity

No silent partial success for operations requiring atomic domain state.

Examples:

```text
Feedback + attachment binding + audit + outbox
Questionnaire publish + immutable revision
Booked conversion relation
```

must use transaction boundaries appropriate to domain.

## 8. Performance classification

User-visible interactions are grouped:

```text
INTERACTIVE_READ
INTERACTIVE_WRITE
HEAVY_GENERATION
BACKGROUND_PROCESSING
EXTERNAL_PROVIDER_ACTION
```

Each class has separate expectations. Long video transcoding is not held to button-click latency; it must instead be queued with observable status.

## 9. Public rendering

Public pages should prioritize:

- server-renderable meaningful content;
- optimized image/media derivatives;
- minimal client JS for primary content;
- cacheable public projections;
- no blocking analytics/provider calls.

## 10. Admin responsiveness

Ordinary list/detail/edit operations should feel interactive under expected dataset size. Heavy generation is async where needed.

## 11. Scalability

Project is single-actress/small professional site baseline, therefore avoid unnecessary distributed complexity. However code/data model must not create obvious O(N²) scans or unbounded client payloads.

### NFR-INV-002
Optimize for predictable bounded workload before horizontal-distribution complexity.

## 12. Database performance

- indexes for foreign keys and common filters;
- pagination for unbounded admin lists;
- avoid N+1 queries;
- projections rather than loading full blobs;
- query plans reviewed for high-use paths;
- no binary media in relational rows baseline.

## 13. Background jobs

Requirements:

- durable;
- restart-safe;
- observable;
- bounded retries;
- idempotent;
- dead-letter/failed state;
- no unbounded queue growth silently.

## 14. Accessibility

WCAG-oriented detailed requirements are DOC-152. Global rule: primary professional content and Admin workflows must be keyboard/screen-reader operable and not rely solely on color, hover or QR.

## 15. Browser/device compatibility

DOC-153 defines support matrix. Responsive public/Admin UI must work across current desktop/mobile browser classes without device-specific business logic.

## 16. SEO

Public content should be indexable/canonical where intended; Admin/token-scoped/private surfaces must not be indexed. DOC-154 defines details.

## 17. Security/privacy

DOC-140…146 are mandatory NFRs, not optional hardening backlog.

Security cannot be traded off for convenience or performance without explicit documented decision.

## 18. Observability

Every critical workflow should expose enough metadata to diagnose:

- request/command failure;
- background job failure;
- provider failure;
- publication/render failure;
- queue/backlog;
- data-quality gap.

Observability excludes raw secrets/private payloads by default.

## 19. Error handling

Errors are:

- typed/normalized;
- user-actionable where possible;
- correlated with internal request/job ID;
- free of stack/path/secret leakage;
- distinguished between validation, state conflict, provider, retryable and internal failures.

## 20. Graceful degradation

Examples:

- AI unavailable → manual writing/edit remains;
- WhatsApp unavailable → Feedback still stored/In-App remains;
- analytics unavailable → public navigation still works;
- social provider unavailable → Draft/schedule history remains;
- external link checker unavailable → link remains Source with UNKNOWN health.

## 21. Recoverability

Backup/restore must cover PostgreSQL and required media/config artifacts. Restore must not replay outbound notifications/social publication or revive revoked access automatically.

Detailed RPO/RTO policy belongs DOC-193.

## 22. Maintainability

- strict TypeScript;
- explicit domain modules;
- DTO != ORM model;
- reusable validation/business rules;
- named state machines;
- controlled enums;
- migrations versioned;
- tests co-located/traceable;
- no duplicate business rules in UI/VOP/AI.

## 23. Documentation

Every production domain should have:

- purpose/doctrine;
- entities;
- invariants;
- commands/queries;
- errors;
- lifecycle;
- E2E cases;
- acceptance criteria.

Documentation changes accompany behavior changes.

## 24. Localization quality

Russian is primary admin/help language baseline; public content supports configured locales. Missing translation must not silently substitute misleading professional content.

## 25. Timezones/time

Store authoritative timestamps in unambiguous format/UTC as appropriate, while preserving explicit business timezone where required (social schedule, display). Do not infer timezone from phone/IP.

## 26. File/media resilience

Originals immutable; derived artifacts rebuildable. Missing derivative is recoverable without modifying Source.

## 27. Search/caches

Search/cache are derived. They must be rebuildable and cannot be sole copy of professional Source.

## 28. PDF/QR quality

Generated PDF must remain selectable/searchable text where appropriate, have clickable links and verified QR. No screenshot-only Questionnaire baseline.

## 29. Quality budgets

Each specialized NFR doc defines measurable budgets. CI/release may have blocking and warning thresholds.

Baseline categories:

```text
BLOCKER
RELEASE_WARNING
OBSERVATIONAL
```

## 30. Production readiness

A module is not production-ready merely because unit tests pass. Gate includes:

- functional acceptance;
- security;
- migration safety;
- accessibility;
- performance for relevant path;
- observability;
- backup/recovery implications;
- documentation.

## 31. Change risk classification

Changes touching:

- authentication/authorization;
- visibility;
- public projections;
- migrations;
- publication;
- tokens/secrets;
- deletion/retention;

are high-risk and require stronger review/testing.

## 32. External dependency principle

No external provider is treated as perfectly reliable. Every integration has timeout, explicit failure state and data-preserving behavior.

## 33. Client payload bounds

Public/Admin APIs should paginate/bound collections and avoid embedding full binaries/base64. Browser does not receive hidden alternatives “just in case”.

## 34. Quality anti-patterns

Forbidden:

1. Optional AI outage breaks Profile CRUD.
2. Huge original image sent for thumbnails.
3. Whole DB collection serialized to client.
4. No pagination on unbounded history.
5. Background task run synchronously until HTTP timeout.
6. Cache is only copy of generated result needed for history.
7. Accessibility deferred as cosmetic.
8. Private/token pages indexed for SEO.
9. Provider call without timeout.
10. Restore untested.
11. Production behavior changed without docs/tests.
12. Silent failed jobs.
13. NFR claims without measurable verification path.

## 35. Global quality gate

Before production release:

- security tests pass;
- migrations validated;
- critical E2E pass;
- performance budgets acceptable;
- accessibility blockers zero;
- supported browser smoke tests pass;
- public SEO validation passes;
- backup/restore last-known test within policy;
- monitoring/alerts operational;
- no known P1/P2 blocking defects;
- documentation/traceability updated.

## 36. Acceptance criteria

`AC-NFR-001` Optional providers fail independently from authoritative core.  
`AC-NFR-002` Core writes use transactional/idempotent/revision-safe patterns.  
`AC-NFR-003` Heavy operations use durable background processing where appropriate.  
`AC-NFR-004` Public pages avoid unnecessary client payload/JS and large originals.  
`AC-NFR-005` Admin lists are bounded/paginated and DB queries indexed.  
`AC-NFR-006` Derived artifacts/search/cache are rebuildable.  
`AC-NFR-007` Accessibility/security/privacy are release requirements.  
`AC-NFR-008` External providers have timeout/failure/retry/reconciliation semantics.  
`AC-NFR-009` Backup/restore preserves Source without replaying side effects.  
`AC-NFR-010` Quality gates are measurable and automated where feasible.  
`AC-NFR-011` High-risk changes receive stronger testing/review.  
`AC-NFR-012` Documentation and requirements traceability are part of Definition of Done.

---

## 37. Финальная доктрина

> **Non-functional quality is part of the product contract. The system remains secure, private, accessible, recoverable and diagnosable under normal failures; heavy work is durable and bounded; optional providers degrade independently; and release decisions are based on measurable quality gates rather than subjective “works on my machine” acceptance.**
