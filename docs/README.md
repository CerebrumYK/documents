# Сайт актрисы — Documentation
## DOC-002 — входная точка документации

**Статус:** Canonical Navigation

---

## 1. Назначение

Этот каталог содержит нормативную документацию проекта профессионального сайта/платформы актрисы, а также отдельно изолированный архив исторических source materials.

Документация предназначена для Product/architecture review, Codex implementation, code review, migrations, testing, production operations, security/privacy review и final acceptance.

---

## 2. С чего начать

### Для Codex / implementation

1. [`governance.md`](./governance.md) — source-of-truth и precedence;
2. [`final/final-technical-specification.md`](./final/final-technical-specification.md) — consolidated target architecture;
3. [`github/codex-execution-plan.md`](./github/codex-execution-plan.md) — phased execution plan;
4. [`implementation/implementation-sequencing.md`](./implementation/implementation-sequencing.md) — dependency order;
5. owning Module/Domain document;
6. Security/NFR/Migration/Testing documents;
7. `requirements/*`.

### Для final audit

1. [`final/documentation-audit.md`](./final/documentation-audit.md)
2. [`final/gap-analysis.md`](./final/gap-analysis.md)
3. [`final/final-traceability.md`](./final/final-traceability.md)
4. [`final/documentation-index.md`](./final/documentation-index.md)

### Для historical/source review

Используйте [`archive/source-materials/README.md`](./archive/source-materials/README.md).

Архив хранит прежние аудиты, Codex plans, AGENTS и UX/data-model reference materials. Они сохранены для provenance и **не имеют precedence над current canonical DOC series**.

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

Supabase не является частью current canonical architecture, даже если он встречается в historical source materials.

---

## 5. Основные domain capabilities

Profile; Media Library; Main Portfolio; Emotional Portfolio/Grid; Projects/Roles; Training; Skills/Languages; Professional Media/Links; Contacts; Questionnaires/PDF/QR; Public Questionnaire Builder; Castings/Casting AI; Feedback/Notifications; Opportunity Pipeline; Site Themes; BB Assistant; VOP; Marketing Analytics; Help Center/Tickets; Social Publishing; Achievements.

---

## 6. Current canonical package physically present

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

See [`final/documentation-index.md`](./final/documentation-index.md) for exact paths/status.

---

## 7. Imported historical source materials

The repository now also contains safely archived historical materials recovered from the previous development-document package available in File Library:

```text
docs/archive/source-materials/
├── README.md
├── 2026-07-26/
│   ├── README-source-package.md
│   ├── AGENTS-v1.md
│   └── CODEX_TASKS-v1.md
└── 2026-07-27/
    ├── 01-repository-audit.md
    ├── 03-data-relationships-v2.md
    ├── 04-codex-tasks-v2.md
    ├── 05-agents-v2.md
    └── 06-ux-reference-analysis.md
```

These filenames were normalized to kebab-case/explicit version naming to avoid collisions with current root/canonical files.

### Deliberately not committed

- real source actor questionnaire DOCX;
- production/private imports;
- secrets/exports;
- historical full TZ copies that may include source personal data until redaction/review;
- redundant binary DOCX copies where Markdown or canonical replacement exists.

This is intentional privacy protection, not an omission to be filled by guessing.

---

## 8. Earlier canonical project documents still awaiting direct import

The project conversation had already authored/defined:

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

Historical July source files do **not** substitute for these later canonical DOC documents. Until the original authored DOC-001…134 files are imported, use current final consolidation/traceability plus source archive only as evidence.

---

## 9. Master requirements

- [`requirements/functional-requirements.md`](./requirements/functional-requirements.md) — `FR-*`;
- [`requirements/acceptance-criteria.md`](./requirements/acceptance-criteria.md) — `AC-MASTER-*`;
- [`requirements/requirements-traceability.md`](./requirements/requirements-traceability.md) — traceability.

A requirement is not `VERIFIED` until implementation and passing test evidence exist.

---

## 10. Human authority boundaries

Human-controlled actions include professional Source changes, Skill/Language levels, Casting Requirement confirmation, Questionnaire publication, Theme activation, professional send/reply, Opportunity stage/BOOKED, Project credit creation, Social approval/schedule/publish and ambiguous migration decisions.

---

## 11. Documentation governance

All conflicts, supersession, stable DOC IDs and precedence are governed by [`governance.md`](./governance.md).

> **A newer commit or historical source filename never overrides the owning canonical document merely by being newer/older.**

---

## 12. Documentation status vs implementation status

```text
DOCUMENTED
IMPLEMENTED_UNVERIFIED
VERIFIED
```

This repository proves documentation, not website implementation.

---

## 13. Recommended first Codex request

```text
Perform Phase 0 repository audit.
Do not modify architecture broadly yet.
Map current code, DB schema, routes, media storage, authentication,
provider integrations, tests and deployment against DOC-200/201/206 and DOC-230/231.
Return evidence-backed gaps and a dependency-aware implementation plan.
```

---

## 14. Финальная доктрина

> **Use current canonical DOCs for implementation, historical source materials only for provenance/evidence, and never commit private source documents merely to make the documentation repository appear complete.**
