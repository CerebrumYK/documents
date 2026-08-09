# MASTER DOCUMENTATION PLAN

## Полный обязательный пакет проектной документации

**Проект:** профессиональный сайт актрисы
**Назначение:** зафиксировать полный комплект документации до декомпозиции на GitHub Epic / Issues
**Принцип:** Product → Customer Journey → Domain → Architecture → Modules → Data → Security → Migration → Testing → Operations → Implementation → GitHub

---

# 0. Правило дальнейшей работы

Документы подготавливаются **строго в указанной ниже последовательности**.

После каждого завершённого документа его статус меняется:

```text
⬜ Planned
🟨 In Progress
✅ Completed
```

При сообщении пользователя:

```text
Следующий
Дальше
Продолжай
Следующий документ
```

необходимо автоматически:

1. найти первый документ со статусом `⬜ Planned`;
2. использовать все ранее утверждённые документы как входные требования;
3. не менять уже зафиксированную продуктовую логику;
4. устранить возможные противоречия на уровне нового документа;
5. создать полный нормативный документ;
6. указать его целевой путь в репозитории;
7. после завершения считать его `✅ Completed`;
8. перейти к следующему только после следующего сообщения пользователя.

---

# 1. MASTER / GOVERNANCE

Эти документы определяют, как читать и применять весь комплект.

---

## DOC-001 — Master Technical Specification

**Файл:**

```text
docs/TZ.md
```

**Статус:** ✅ Completed концептуально

Содержит:

* цель;
* scope;
* ключевые функции;
* основной business flow;
* общие ограничения;
* ссылки на детальные документы;
* итоговые acceptance principles.

После формирования всего пакета документ необходимо будет **финально актуализировать**, но продуктовую логику не менять.

---

## DOC-002 — Documentation Index

**Файл:**

```text
docs/README.md
```

**Статус:** ⬜ Planned

Назначение:

единая карта всей документации.

Содержит:

* список документов;
* статус;
* назначение;
* зависимости;
* порядок чтения;
* source-of-truth hierarchy.

Этот файл логично сформировать **в конце основного пакета**, несмотря на его верхнеуровневое расположение.

---

## DOC-003 — Documentation Governance

**Файл:**

```text
docs/documentation-governance.md
```

**Статус:** ⬜ Planned

Определяет:

* приоритет документов;
* правила изменения требований;
* versioning;
* deprecated requirements;
* постоянные IDs;
* traceability;
* правило «документация раньше реализации».

---

# 2. PRODUCT FOUNDATION

Это нормативная продуктовая база.
Без неё нельзя переходить к архитектуре.

---

## DOC-010 — Product Overview

**Файл:**

```text
docs/product/product-overview.md
```

**Статус:** ⬜ Planned

Должен описывать:

* что за продукт создаётся;
* для кого;
* какую проблему решает;
* бизнес-результаты;
* границы продукта;
* основные capability;
* внутренний и внешний цикл;
* принципы минимизации действий;
* роль ИИ;
* роль Virtual Operator.

---

## DOC-011 — Terminology

**Файл:**

```text
docs/product/terminology.md
```

**Статус:** ⬜ Planned

Крайне необходим.

Фиксирует однозначные определения:

* Profile;
* Portfolio;
* Media Asset;
* Emotional Portfolio;
* Emotional Grid;
* Project;
* Role;
* Training;
* Questionnaire;
* Questionnaire Revision;
* Public Questionnaire Builder;
* Public Template;
* Casting;
* Feedback;
* Opportunity;
* Theme;
* Revision;
* Snapshot;
* Draft;
* Published;
* Archived;
* Source;
* Derived;
* Visibility;
* Public;
* Private;
* и т. д.

После утверждения терминологии разные документы не должны использовать разные названия одной сущности.

---

## DOC-012 — Business Rules

**Файл:**

```text
docs/product/business-rules.md
```

**Статус:** ✅ Completed

Уже сформирован.

Является нормативным каталогом:

```text
BR-GEN-...
BR-POR-...
BR-EMO-...
BR-GRD-...
BR-QST-...
BR-PQB-...
BR-CST-...
...
```

---

## DOC-013 — Information Architecture

**Файл:**

```text
docs/product/information-architecture.md
```

**Статус:** ✅ Completed

Уже сформирован.

Определяет:

* классы информации;
* ownership;
* источники истины;
* controlled projections;
* admin/public/builder/AI/PDF/search contexts;
* основные информационные потоки.

---

## DOC-014 — Product Scope

**Файл:**

```text
docs/product/scope.md
```

**Статус:** ⬜ Planned

Нужен для предотвращения расползания разработки.

Содержит:

### In Scope

Что точно реализуется.

### Out of Scope

Что сознательно не входит.

### Future Scope

Что допускается позже.

### Explicit Non-Goals

Например:

* автоматическая отправка кастинговой заявки;
* автоматическое подтверждение роли;
* изменение внешности актрисы;
* публикация AI без подтверждения.

---

## DOC-015 — Product Principles

**Файл:**

```text
docs/product/product-principles.md
```

**Статус:** ⬜ Planned

Фиксирует непреложные принципы:

* Single Source of Truth;
* Human-in-the-loop;
* No hidden private payload;
* Admin → Public;
* Progressive Disclosure;
* Minimum Actions;
* No Dead Ends;
* Immutable Published Revisions;
* Exception-driven administration.

---

# 3. CUSTOMER JOURNEY / PRODUCT EXPERIENCE

Этот слой необходимо завершить **до детального UX и архитектуры экранов**.

---

## DOC-020 — Customer Journey Overview

**Файл:**

```text
docs/customer-journey/overview.md
```

**Статус:** ⬜ Planned

Содержит общую модель:

```text
Discovery
→ Evaluation
→ Evidence
→ Questionnaire
→ Contact
→ Casting
→ Audition
→ Role
```

и внутренний цикл:

```text
Content
→ Operator
→ Approval
→ Publication
→ Opportunity
→ Result
→ New Content
```

---

## DOC-021 — Personas

**Файл:**

```text
docs/customer-journey/personas.md
```

**Статус:** ⬜ Planned

Фиксирует шесть основных персон:

1. Casting Director.
2. Casting Assistant.
3. Commercial Casting Producer.
4. Director / Producer.
5. International Casting Specialist.
6. Virtual Portfolio Operator.

Для каждой:

* цель;
* контекст;
* устройство;
* ограничения;
* ожидания;
* pain points;
* desired outcome.

---

## DOC-022 — Method 6-3-5

**Файл:**

```text
docs/customer-journey/method-6-3-5.md
```

**Статус:** ⬜ Planned

Формализует:

```text
6 personas
×
3 product insights
×
5-minute journey
```

и все 18 продуктовых выводов.

---

## DOC-023 — Casting Director Journey

```text
docs/customer-journey/casting-director.md
```

**Статус:** ⬜ Planned

---

## DOC-024 — Casting Assistant Journey

```text
docs/customer-journey/casting-assistant.md
```

**Статус:** ⬜ Planned

---

## DOC-025 — Commercial Producer Journey

```text
docs/customer-journey/commercial-producer.md
```

**Статус:** ⬜ Planned

---

## DOC-026 — Director / Producer Journey

```text
docs/customer-journey/director.md
```

**Статус:** ⬜ Planned

---

## DOC-027 — International Casting Journey

```text
docs/customer-journey/international-casting.md
```

**Статус:** ⬜ Planned

---

## DOC-028 — Virtual Operator Journey

```text
docs/customer-journey/virtual-operator.md
```

**Статус:** ⬜ Planned

---

## DOC-029 — Conversion Funnel

```text
docs/customer-journey/conversion-funnel.md
```

**Статус:** ⬜ Planned

Определяет:

* professional visitor;
* qualified interest;
* questionnaire;
* download;
* custom PDF;
* inquiry;
* casting;
* audition;
* callback;
* offer;
* booked.

---

## DOC-030 — Automation Matrix

```text
docs/customer-journey/automation-matrix.md
```

**Статус:** ⬜ Planned

Для каждого действия определяет:

| Действие | Auto | Human approval | AI allowed |
| -------- | ---: | -------------: | ---------: |

---

# 4. UX / INTERACTION ARCHITECTURE

После Customer Journey.

---

## DOC-040 — UX Principles

```text
docs/ux/ux-principles.md
```

**Статус:** ⬜ Planned

---

## DOC-041 — Public Navigation

```text
docs/ux/public-navigation.md
```

**Статус:** ⬜ Planned

Определяет:

* menu;
* primary journeys;
* CTA hierarchy;
* mobile navigation;
* deep links;
* no-dead-end rules.

---

## DOC-042 — Admin Navigation

```text
docs/ux/admin-navigation.md
```

**Статус:** ⬜ Planned

---

## DOC-043 — Public User Flows

```text
docs/ux/public-user-flows.md
```

**Статус:** ⬜ Planned

---

## DOC-044 — Admin User Flows

```text
docs/ux/admin-user-flows.md
```

**Статус:** ⬜ Planned

---

## DOC-045 — Public Questionnaire Builder UX

```text
docs/ux/public-questionnaire-builder.md
```

**Статус:** ⬜ Planned

Крайне важный отдельный документ.

---

## DOC-046 — Forms & Validation UX

```text
docs/ux/forms-and-validation.md
```

**Статус:** ⬜ Planned

---

## DOC-047 — UI States

```text
docs/ux/ui-states.md
```

**Статус:** ⬜ Planned

Должны быть описаны:

* empty;
* loading;
* saving;
* saved;
* stale;
* error;
* conflict;
* unavailable;
* generating;
* ready.

---

## DOC-048 — Content Hierarchy

```text
docs/ux/content-hierarchy.md
```

**Статус:** ⬜ Planned

Фиксирует P0/P1/P2/P3 приоритеты информации.

---

## DOC-049 — Responsive Behaviour

```text
docs/ux/responsive-behaviour.md
```

**Статус:** ⬜ Planned

Desktop / Tablet / Mobile.

---

## DOC-050 — Accessibility UX

```text
docs/ux/accessibility.md
```

**Статус:** ⬜ Planned

---

# 5. DOMAIN MODEL

Это переход от продуктовой модели к технической архитектуре.

---

## DOC-060 — Domain Model

**Файл:**

```text
docs/architecture/domain-model.md
```

**Статус:** ⬜ Planned

**Следующий рекомендуемый документ после текущего плана.**

Он должен формализовать:

* aggregates;
* aggregate roots;
* entities;
* value objects;
* ownership;
* invariants;
* domain boundaries;
* relations;
* lifecycle.

---

## DOC-061 — Domain Relationships

```text
docs/architecture/domain-relationships.md
```

**Статус:** ⬜ Planned

Подробная карта:

```text
Profile → Portfolio
Profile → Emotional Portfolio
Questionnaire → Revision
Feedback ↔ Casting
...
```

---

## DOC-062 — State Machines

```text
docs/architecture/state-machines.md
```

**Статус:** ⬜ Planned

Отдельные автоматы состояний:

* Portfolio publication;
* Emotional Portfolio;
* Grid;
* Questionnaire;
* Casting;
* Feedback;
* Notification;
* Theme.

---

## DOC-063 — Data Ownership & Lifecycle

```text
docs/architecture/data-ownership-lifecycle.md
```

**Статус:** ⬜ Planned

Кто владеет сущностью и что происходит:

* create;
* edit;
* publish;
* archive;
* delete;
* migrate.

---

## DOC-064 — Revision & Snapshot Model

```text
docs/architecture/revisions-and-snapshots.md
```

**Статус:** ⬜ Planned

Крайне необходим для:

* Questionnaire;
* Theme;
* Casting Analysis;
* generated PDF.

---

# 6. SYSTEM ARCHITECTURE

---

## DOC-070 — Architecture Overview

```text
docs/architecture/overview.md
```

**Статус:** ⬜ Planned

Описывает систему целиком.

---

## DOC-071 — Component Architecture

```text
docs/architecture/components.md
```

**Статус:** ⬜ Planned

Компоненты:

* Public Web;
* Admin;
* Data Layer;
* Media;
* PDF;
* AI;
* Notifications;
* Worker Processes;
* Search;
* Analytics.

---

## DOC-072 — Data Flow Architecture

```text
docs/architecture/data-flows.md
```

**Статус:** ⬜ Planned

---

## DOC-073 — Projection Architecture

```text
docs/architecture/projections.md
```

**Статус:** ⬜ Planned

Крайне важный документ:

* Admin Projection;
* Public Projection;
* Quick View Projection;
* Public Builder Projection;
* Search Projection;
* AI Projection;
* PDF Snapshot Projection.

---

## DOC-074 — Media Lifecycle

```text
docs/architecture/media-lifecycle.md
```

**Статус:** ⬜ Planned

---

## DOC-075 — PDF Architecture

```text
docs/architecture/pdf-generation.md
```

**Статус:** ⬜ Planned

---

## DOC-076 — Background Processing

```text
docs/architecture/background-processing.md
```

**Статус:** ⬜ Planned

Для:

* PDF;
* grids;
* AI;
* notifications.

---

## DOC-077 — Cache & Invalidation

```text
docs/architecture/cache-invalidation.md
```

**Статус:** ⬜ Planned

---

## DOC-078 — Search Architecture

```text
docs/architecture/search.md
```

**Статус:** ⬜ Planned

---

## DOC-079 — AI Architecture & Boundaries

```text
docs/architecture/ai-boundaries.md
```

**Статус:** ⬜ Planned

---

## DOC-080 — Notification Architecture

```text
docs/architecture/notifications.md
```

**Статус:** ⬜ Planned

---

# 7. DATA MODEL

---

## DOC-090 — Database Schema

```text
docs/architecture/database-schema.md
```

**Статус:** ⬜ Planned

Здесь уже:

* таблицы;
* columns;
* types;
* keys;
* indexes;
* unique;
* constraints.

---

## DOC-091 — Data Dictionary

```text
docs/architecture/data-dictionary.md
```

**Статус:** ⬜ Planned

Определяет смысл каждого поля.

---

## DOC-092 — Visibility Model

```text
docs/architecture/visibility-model.md
```

**Статус:** ⬜ Planned

Крайне важен.

---

## DOC-093 — Data Validation Model

```text
docs/architecture/data-validation.md
```

**Статус:** ⬜ Planned

---

## DOC-094 — Deletion & Retention Model

```text
docs/architecture/deletion-retention.md
```

**Статус:** ⬜ Planned

---

# 8. API / APPLICATION CONTRACTS

---

## DOC-100 — Server Actions / API Catalog

```text
docs/api/server-actions.md
```

**Статус:** ⬜ Planned

---

## DOC-101 — Input / Output Contracts

```text
docs/api/contracts.md
```

**Статус:** ⬜ Planned

---

## DOC-102 — Error Catalogue

```text
docs/api/errors.md
```

**Статус:** ⬜ Planned

---

## DOC-103 — Idempotency & Concurrency

```text
docs/api/idempotency-concurrency.md
```

**Статус:** ⬜ Planned

---

# 9. MODULE SPECIFICATIONS

Каждый крупный модуль получает самостоятельное ТЗ.

---

## DOC-110 — Profile Module

```text
docs/modules/profile.md
```

---

## DOC-111 — Media Library

```text
docs/modules/media-library.md
```

---

## DOC-112 — Portfolio

```text
docs/modules/portfolio.md
```

---

## DOC-113 — Emotional Portfolio

```text
docs/modules/emotional-portfolio.md
```

---

## DOC-114 — Emotional Grid

```text
docs/modules/emotional-grid.md
```

---

## DOC-115 — Projects & Roles

```text
docs/modules/projects.md
```

---

## DOC-116 — Training

```text
docs/modules/training.md
```

---

## DOC-117 — Skills & Languages

```text
docs/modules/skills-languages.md
```

---

## DOC-118 — Professional Media & Links

```text
docs/modules/professional-media-links.md
```

---

## DOC-119 — Contacts

```text
docs/modules/contacts.md
```

---

## DOC-120 — Prepared Questionnaires

```text
docs/modules/questionnaires.md
```

---

## DOC-121 — Public Questionnaire Builder

```text
docs/modules/public-questionnaire-builder.md
```

**Критически важный.**

---

## DOC-122 — Castings

```text
docs/modules/castings.md
```

---

## DOC-123 — AI Casting Analysis

```text
docs/modules/casting-ai-analysis.md
```

---

## DOC-124 — Feedback

```text
docs/modules/feedback.md
```

---

## DOC-125 — Notifications / WhatsApp

```text
docs/modules/notifications.md
```

---

## DOC-126 — Opportunity Pipeline

```text
docs/modules/opportunity-pipeline.md
```

---

## DOC-127 — Site Themes

```text
docs/modules/site-themes.md
```

---

## DOC-128 — Virtual Operator

```text
docs/modules/virtual-operator.md
```

---

## DOC-129 — Marketing & Analytics

```text
docs/modules/marketing-analytics.md
```

---

# 10. SECURITY / PRIVACY

Не стоит откладывать это до реализации.

---

## DOC-140 — Security Model

```text
docs/security/security-model.md
```

**Статус:** ⬜ Planned

---

## DOC-141 — Access Control

```text
docs/security/access-control.md
```

---

## DOC-142 — Public / Private Data Policy

```text
docs/security/data-visibility.md
```

---

## DOC-143 — Personal Data & Privacy

```text
docs/security/personal-data.md
```

---

## DOC-144 — File Upload Security

```text
docs/security/uploads.md
```

---

## DOC-145 — AI Data Security

```text
docs/security/ai-data-boundaries.md
```

---

## DOC-146 — Audit Policy

```text
docs/security/audit.md
```

---

# 11. NON-FUNCTIONAL REQUIREMENTS

Крайне важный блок, которого часто не хватает в ТЗ.

---

## DOC-150 — Non-Functional Requirements

```text
docs/requirements/non-functional-requirements.md
```

Содержит:

* performance;
* scalability;
* reliability;
* availability;
* security;
* privacy;
* accessibility;
* maintainability;
* compatibility.

---

## DOC-151 — Performance Requirements

```text
docs/requirements/performance.md
```

---

## DOC-152 — Accessibility Requirements

```text
docs/requirements/accessibility.md
```

---

## DOC-153 — Browser / Device Compatibility

```text
docs/requirements/compatibility.md
```

---

## DOC-154 — SEO Requirements

```text
docs/requirements/seo.md
```

---

## DOC-155 — Analytics Requirements

```text
docs/requirements/analytics.md
```

---

# 12. MIGRATIONS

---

## DOC-160 — Migration Strategy

```text
docs/migrations/strategy.md
```

---

## DOC-161 — Emotional Portfolio Migration

```text
docs/migrations/emotional-portfolio.md
```

---

## DOC-162 — Training Migration

```text
docs/migrations/training.md
```

---

## DOC-163 — Portfolio Photo Migration

```text
docs/migrations/portfolio-photos.md
```

---

## DOC-164 — Questionnaire Migration

```text
docs/migrations/questionnaires.md
```

Если потребуется преобразование текущих конфигураций.

---

## DOC-165 — Migration Validation / Rollback

```text
docs/migrations/validation-and-rollback.md
```

---

# 13. TESTING / QUALITY

---

## DOC-170 — Test Strategy

```text
docs/testing/test-strategy.md
```

---

## DOC-171 — Unit Test Matrix

```text
docs/testing/unit-tests.md
```

---

## DOC-172 — Integration Test Matrix

```text
docs/testing/integration-tests.md
```

---

## DOC-173 — E2E Test Matrix

```text
docs/testing/e2e-tests.md
```

---

## DOC-174 — Customer Journey Tests

```text
docs/testing/customer-journey-tests.md
```

Проверяет все шесть персон 6-3-5.

---

## DOC-175 — Security Tests

```text
docs/testing/security-tests.md
```

---

## DOC-176 — Accessibility Tests

```text
docs/testing/accessibility-tests.md
```

---

## DOC-177 — Migration Tests

```text
docs/testing/migration-tests.md
```

---

## DOC-178 — Regression Test Plan

```text
docs/testing/regression.md
```

Особенно важен, поскольку проект уже существует.

---

## DOC-179 — Acceptance Test Plan

```text
docs/testing/acceptance-tests.md
```

---

# 14. REQUIREMENTS TRACEABILITY

---

## DOC-180 — Functional Requirements Register

```text
docs/requirements/functional-requirements.md
```

Каждое требование:

```text
FR-...
```

---

## DOC-181 — Acceptance Criteria Register

```text
docs/requirements/acceptance-criteria.md
```

---

## DOC-182 — Traceability Matrix

```text
docs/requirements/traceability.md
```

Ключевой документ перед GitHub.

Связывает:

```text
Business Rule
→ Functional Requirement
→ Architecture
→ Module
→ Issue
→ Test
```

---

# 15. OPERATIONS / DEPLOYMENT

Нужны до production implementation.

---

## DOC-190 — Deployment Architecture

```text
docs/operations/deployment.md
```

---

## DOC-191 — Configuration & Environment

```text
docs/operations/configuration.md
```

---

## DOC-192 — Storage & Backup

```text
docs/operations/storage-backup.md
```

---

## DOC-193 — Observability

```text
docs/operations/observability.md
```

---

## DOC-194 — Health Checks

```text
docs/operations/health-checks.md
```

---

## DOC-195 — Failure & Recovery

```text
docs/operations/failure-recovery.md
```

---

## DOC-196 — Production Runbook

```text
docs/operations/runbook.md
```

---

# 16. IMPLEMENTATION PLANNING

Только после завершения предыдущих слоёв.

---

## DOC-200 — Implementation Roadmap

```text
docs/implementation/roadmap.md
```

---

## DOC-201 — Dependency Graph

```text
docs/implementation/dependencies.md
```

---

## DOC-202 — Work Breakdown Structure

```text
docs/implementation/work-breakdown.md
```

Разбивает продукт на:

```text
Epic
→ Feature
→ Task
→ Subtask
```

---

## DOC-203 — Definition of Ready

```text
docs/implementation/definition-of-ready.md
```

---

## DOC-204 — Definition of Done

```text
docs/implementation/definition-of-done.md
```

---

## DOC-205 — Issue Template

```text
docs/implementation/issue-template.md
```

---

## DOC-206 — ADR Register

```text
docs/architecture/adr/README.md
```

ADR = Architecture Decision Record.

Использовать только для решений, которые реально требуют выбора:

```text
ADR-001
Почему выбран такой snapshot model

ADR-002
Почему Public Builder отделён от Questionnaire

ADR-003
Как реализуется background processing
```

---

# 17. GITHUB PLANNING

Только после Traceability + Roadmap + Dependencies.

---

## DOC-220 — GitHub Epic Map

```text
docs/implementation/github-epics.md
```

---

## DOC-221 — GitHub Issue Plan

```text
docs/implementation/github-issues.md
```

---

## DOC-222 — Release / Milestone Plan

```text
docs/implementation/milestones.md
```

---

## DOC-223 — Implementation Sequence

```text
docs/implementation/implementation-sequence.md
```

Точная последовательность Issue с dependencies.

---

# 18. ФИНАЛЬНАЯ КОНСОЛИДАЦИЯ

После подготовки всех документов.

---

## DOC-230 — Documentation Consistency Audit

```text
docs/review/documentation-audit.md
```

Проверить:

* противоречия;
* пропуски;
* терминологию;
* broken references;
* duplicate rules;
* orphan requirements;
* requirements without tests;
* tests without requirements.

---

## DOC-231 — Gap Analysis

```text
docs/review/gap-analysis.md
```

Сравнить:

```text
Current Repository AS-IS
vs
Target Documentation TO-BE
```

---

## DOC-232 — Final Traceability Audit

```text
docs/review/final-traceability.md
```

Каждое критическое требование должно иметь путь:

```text
BR
→ FR
→ IA/Domain
→ Module
→ Issue
→ Test
```

---

## DOC-233 — Final TZ Consolidation

Финально актуализировать:

```text
docs/TZ.md
```

Чтобы он стал компактным master index всех утверждённых требований.

---

## DOC-234 — Documentation Index Finalization

Финально сформировать:

```text
docs/README.md
```

со всеми ссылками и статусами.

---

# 19. Порядок, которому будем следовать

Учитывая уже выполненные документы, последовательность от текущей точки фиксируется так:

```text
✅ DOC-001 Master TZ
✅ DOC-012 Business Rules
✅ DOC-013 Information Architecture

⬜ DOC-010 Product Overview
⬜ DOC-011 Terminology
⬜ DOC-014 Product Scope
⬜ DOC-015 Product Principles

⬜ DOC-020 Customer Journey Overview
⬜ DOC-021 Personas
⬜ DOC-022 Method 6-3-5
⬜ DOC-023 Casting Director Journey
⬜ DOC-024 Casting Assistant Journey
⬜ DOC-025 Commercial Producer Journey
⬜ DOC-026 Director Journey
⬜ DOC-027 International Casting Journey
⬜ DOC-028 Virtual Operator Journey
⬜ DOC-029 Conversion Funnel
⬜ DOC-030 Automation Matrix

⬜ DOC-040–050 UX package

⬜ DOC-060 Domain Model
⬜ DOC-061 Domain Relationships
⬜ DOC-062 State Machines
⬜ DOC-063 Data Ownership & Lifecycle
⬜ DOC-064 Revisions & Snapshots

⬜ DOC-070–080 Architecture package

⬜ DOC-090–094 Data Model package

⬜ DOC-100–103 API package

⬜ DOC-110–129 Module specifications

⬜ DOC-140–146 Security package

⬜ DOC-150–155 Non-Functional Requirements

⬜ DOC-160–165 Migration package

⬜ DOC-170–179 Testing package

⬜ DOC-180–182 Requirements & Traceability

⬜ DOC-190–196 Operations package

⬜ DOC-200–206 Implementation package

⬜ DOC-220–223 GitHub planning

⬜ DOC-230–234 Final audit and consolidation
```

---

# 20. Несколько документов сознательно готовятся позже

Несмотря на номера, следующие документы формируются после остальных:

```text
DOC-002 Documentation Index
DOC-003 Documentation Governance
DOC-233 Final TZ Consolidation
DOC-234 Documentation Index Finalization
```

Потому что сначала необходимо получить фактический полный пакет.

---

# 21. Следующий документ

Согласно утверждённой последовательности первым незавершённым документом является:

```text
DOC-010
docs/product/product-overview.md
```

После него:

```text
DOC-011
docs/product/terminology.md
```

Далее продолжаем без дополнительных вопросов строго по настоящему Master Documentation Plan.

---

# 22. Работа после получения ZIP

После завершения документационного пакета пользователь передаёт ZIP с итоговой структурой.

Тогда необходимо выполнить отдельный **Documentation Consistency Audit**:

1. распаковать пакет;
2. построить фактическое дерево документации;
3. проверить наличие всех документов;
4. проверить ссылки;
5. проверить IDs;
6. найти дубли;
7. найти противоречия;
8. найти потерянные требования;
9. проверить Business Rules против IA;
10. проверить Domain Model против IA;
11. проверить Modules против Business Rules;
12. проверить Migration против Data Model;
13. проверить Tests против Acceptance Criteria;
14. проверить Traceability;
15. сравнить с Master Documentation Plan;
16. сформировать Gap Analysis;
17. после исправлений признать пакет готовым к GitHub decomposition.

---

# 23. Критерий готовности документационного пакета

До создания GitHub Issues должны существовать и быть согласованы как минимум:

```text
Product Foundation
Customer Journey
UX
Domain Model
Architecture
Data Model
API Contracts
Module Specifications
Security
Non-Functional Requirements
Migrations
Testing
Functional Requirements
Acceptance Criteria
Traceability
Implementation Roadmap
Dependencies
Definition of Ready
Definition of Done
```

Только после этого начинается формальная декомпозиция:

```text
Product Documentation
        ↓
GitHub Epics
        ↓
GitHub Issues
        ↓
Subtasks
        ↓
Implementation
```

Это позволит использовать GitHub Issues как **исполняемое отражение утверждённой архитектуры**, а не как место, где продуктовые решения принимаются непосредственно во время написания кода.
