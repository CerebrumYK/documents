# Сайт актрисы — Documentation
## DOC-002 — входная точка документации

**Статус:** Canonical Navigation

---

## 1. Назначение

Этот каталог содержит нормативную документацию проекта профессионального сайта/платформы актрисы.

Документация предназначена для:

- Product/architecture review;
- реализации в Codex;
- разработки и code review;
- миграций;
- тестирования;
- production deployment/operations;
- security/privacy review;
- acceptance и final audit.

---

## 2. С чего начать

### Для Codex / implementation

Читайте в таком порядке:

1. [`governance.md`](./governance.md) — правила source-of-truth и precedence;
2. [`final/final-technical-specification.md`](./final/final-technical-specification.md) — consolidated target architecture;
3. [`github/codex-execution-plan.md`](./github/codex-execution-plan.md) — phased execution plan;
4. [`implementation/implementation-sequencing.md`](./implementation/implementation-sequencing.md) — dependency order;
5. соответствующий owning Module/Domain document;
6. соответствующие Security/NFR/Migration/Testing documents;
7. [`requirements/functional-requirements.md`](./requirements/functional-requirements.md), [`requirements/acceptance-criteria.md`](./requirements/acceptance-criteria.md), [`requirements/requirements-traceability.md`](./requirements/requirements-traceability.md).

### Для final audit

1. [`final/documentation-audit.md`](./final/documentation-audit.md)
2. [`final/gap-analysis.md`](./final/gap-analysis.md)
3. [`final/final-traceability.md`](./final/final-traceability.md)
4. [`final/documentation-index.md`](./final/documentation-index.md)

### Для production operations

Начните с [`operations/operations-overview.md`](./operations/operations-overview.md), затем Deployment → Config/Secrets → Backup/Restore → Monitoring → Incident Response → Runbooks.

---

## 3. Главная архитектурная доктрина

```text
Professional Source
    ↓
Configuration / Human intent
    ↓
Revision / Snapshot
    ↓
Derived Artifact / Projection
    ↓
Public/Admin/Builder/Provider surface
```

Ключевые правила:

- один владелец каждого профессионального факта;
- PostgreSQL — authoritative structured store;
- Media originals immutable;
- `Save != Publish`;
- `Generate != Apply`;
- `Apply != Publish/Send`;
- historical Revision/Snapshot immutable;
- hidden data absent before serialization;
- AI/VOP/Analytics не получают autonomous professional authority;
- external provider work выполняется после commit через durable jobs/outbox;
- retries idempotent, ambiguous provider outcome reconciled;
- Search/Cache/Derived Artifacts rebuildable;
- migrations never invent missing professional facts.

---

## 4. Технологический baseline

```text
Next.js
TypeScript
PostgreSQL
Modular Monolith
Background Worker
Nginx / HTTPS
/data/kate-actor persistent data root
application port 3336
```

Supabase не является частью canonical architecture.

---

## 5. Основные domain capabilities

Платформа включает:

- Profile;
- Media Library;
- Main Portfolio;
- Emotional Portfolio/Grid;
- Projects/Roles;
- Training;
- Skills/Languages;
- Professional Media/Links;
- Contacts;
- Questionnaires/PDF/QR;
- Public Questionnaire Builder;
- Castings/Casting AI;
- Feedback/Notifications;
- Opportunity Pipeline;
- Site Themes;
- BB Assistant;
- VOP;
- Marketing Analytics;
- Help Center/Tickets;
- Social Publishing;
- Achievements.

---

## 6. Security/NFR/Operations package present in this repository

Current repository contains complete remaining package:

```text
Security       DOC-140…146
NFR            DOC-150…155
Migrations     DOC-160…165
Testing        DOC-170…179
Requirements   DOC-180…182
Operations     DOC-190…196
Implementation DOC-200…206
GitHub/Codex   DOC-220…223
Finalization   DOC-230…234
Governance     DOC-002/003
```

See [`final/documentation-index.md`](./final/documentation-index.md) for exact paths.

---

## 7. Important repository completeness note

This GitHub repository was empty before creation of the **remaining documentation package**.

The project conversation had already authored/defined earlier documents:

```text
DOC-001
DOC-010…015
DOC-020…030
DOC-040…050
DOC-060…064
DOC-070…080
DOC-090…094
DOC-100…103
DOC-110…134
```

Those files are **not physically present in this repository yet** unless separately imported.

Do not interpret their absence as permission to invent replacement semantics.

For details see:

- [`final/documentation-audit.md`](./final/documentation-audit.md)
- [`final/gap-analysis.md`](./final/gap-analysis.md)
- [`final/documentation-index.md`](./final/documentation-index.md)

---

## 8. Master requirements

The consolidated requirement catalog is:

- [`requirements/functional-requirements.md`](./requirements/functional-requirements.md) — `FR-*`;
- [`requirements/acceptance-criteria.md`](./requirements/acceptance-criteria.md) — `AC-MASTER-*`;
- [`requirements/requirements-traceability.md`](./requirements/requirements-traceability.md) — traceability model.

A requirement is not `VERIFIED` until actual implementation and passing test evidence exists.

---

## 9. Human authority boundaries

The following classes of action remain explicitly Human-controlled:

- professional Source fact changes;
- Skill/Language level;
- Casting Requirement confirmation;
- Questionnaire publication;
- Theme activation;
- professional reply/send;
- Opportunity stage/BOOKED;
- Project credit creation;
- Social publishing approval/schedule/publish;
- ambiguous legacy migration decisions.

AI/VOP may assist or recommend but cannot silently cross these boundaries.

---

## 10. Documentation governance

All conflicts, supersession, stable DOC IDs, requirement changes and precedence are governed by [`governance.md`](./governance.md).

Important principle:

> **A higher document number or newer commit does not automatically override a more specific canonical owning document.**

Security/privacy hard constraints also cannot be weakened by convenience wording in another document.

---

## 11. Documentation status vs implementation status

Keep separate:

```text
DOCUMENTED
IMPLEMENTED_UNVERIFIED
VERIFIED
```

This repository currently proves documentation, not implementation of the website.

Actual code repository must be audited through Phase 0 of [`github/codex-execution-plan.md`](./github/codex-execution-plan.md).

---

## 12. Recommended first Codex request

Use a scoped request based on DOC-223 rather than asking Codex to implement the whole platform in one pass.

First task should be:

```text
Perform Phase 0 repository audit.
Do not modify architecture broadly yet.
Map current code, DB schema, routes, media storage, authentication,
provider integrations, tests and deployment against DOC-200/201/206 and DOC-230/231.
Return evidence-backed gaps and a dependency-aware implementation plan.
```

After audit, create GitHub issues/PR-sized phases using DOC-221 templates.

---

## 13. Финальная доктрина

> **The documentation is an implementation contract, not a substitute for repository evidence. Start from governance and the final technical specification, implement through small dependency-aware phases, preserve Human professional authority and update traceability only when real code, migrations and tests prove the requirement.**
