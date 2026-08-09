# PRODUCT PRINCIPLES

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Непреложные принципы проектирования и развития продукта

**Целевой файл:** `docs/product/product-principles.md`  
**Документ:** DOC-015  
**Статус:** ✅ Completed  
**Тип:** Product Foundation

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`

---

# 1. Назначение документа

Настоящий документ определяет набор фундаментальных продуктовых принципов, которые должны применяться ко всем будущим решениям.

Эти принципы используются при:

- проектировании Customer Journey;
- UX;
- Domain Model;
- Architecture;
- Data Model;
- API;
- Module Specifications;
- Migration;
- Testing;
- GitHub Issues;
- Code Review;
- Acceptance.

Принцип не описывает конкретную функцию.

Он определяет **критерий, по которому функция должна проектироваться**.

---

# 2. Приоритет Product Principles

Если конкретная реализация технически возможна, но противоречит Product Principle, техническая простота сама по себе не является достаточным основанием для реализации.

Приоритет:

```text
Business Intent
      ↓
Product Principles
      ↓
Business Rules
      ↓
Customer Journey
      ↓
Architecture
      ↓
Implementation
```

---

# 3. PP-001 — Single Source of Truth

## Формулировка

Каждый профессиональный факт должен иметь один авторитетный источник.

## Цель

Исключить:

- противоречивые данные;
- ручное обновление нескольких копий;
- устаревшие анкеты;
- несогласованные страницы.

## Пример

Правильно:

```text
Language: English B2
        ↓
Profile
Questionnaire
Public Builder
Casting Analysis
```

Неправильно:

```text
Profile: B2
Questionnaire: B1
PDF: Intermediate
Biography: Fluent
```

---

# 4. PP-002 — One Input, Multiple Uses

## Формулировка

Фактические данные вводятся человеком максимально один раз, после чего повторно используются системой во всех разрешённых представлениях.

## Применение

Дата Emotional Portfolio автоматически доступна:

- Public Emotional Portfolio;
- Emotional Grid;
- Questionnaire;
- PDF.

Администратор не должен повторно вводить её в каждом разделе.

---

# 5. PP-003 — Actor Owns the Data

Профессиональные данные принадлежат Actor Profile.

Public Site, Questionnaire, Builder, Search и AI работают с controlled projections этих данных.

Presentation layer не становится владельцем профессиональных фактов.

---

# 6. PP-004 — Casting Professional Drives Public UX

Хотя данные принадлежат Actor Profile, публичный UX должен проектироваться вокруг задач профессионального потребителя.

Основной вопрос:

> «Что кастинг-специалисту необходимо увидеть или сделать сейчас?»

а не:

> «Как удобно организована административная структура?»

---

# 7. PP-005 — Minimum Actions

Каждый профессиональный Journey должен содержать минимально разумное количество действий.

Цель:

```text
Minimum Friction
+
Maximum Relevant Result
```

Количество действий не должно уменьшаться за счёт:

- безопасности;
- достоверности;
- обязательного подтверждения.

---

# 8. PP-006 — Progressive Disclosure

Пользователь сначала получает наиболее важную информацию.

Подробности раскрываются по необходимости.

Уровни:

```text
Quick Decision
      ↓
Professional Evidence
      ↓
Detailed Information
```

---

# 9. PP-007 — Professional Signal Before Decoration

На публичной странице профессиональная полезность имеет приоритет над декоративным эффектом.

Первый экран должен прежде всего сообщать:

- кто это;
- как выглядит;
- основные профессиональные факты;
- что можно сделать дальше.

Анимация и декоративные элементы не должны задерживать этот процесс.

---

# 10. PP-008 — No Dead Ends

Ключевая профессиональная страница должна иметь логичное продолжение Journey.

Например:

```text
Portfolio
→ Questionnaire

Emotional Portfolio
→ Video / Questionnaire

Project
→ Request Materials

Questionnaire
→ Download / Customize / Contact
```

---

# 11. PP-009 — Admin and Public Are Designed Together

Новая профессиональная функция не должна сначала создаваться только в Admin, а затем позже адаптироваться для Public.

Для каждого capability одновременно определяется:

```text
Admin Management
+
Public Presentation
+
Questionnaire Usage
+
Builder Usage
+
Search Usage
```

где применимо.

---

# 12. PP-010 — Public Is Not a Copy of Admin

Public UI не должен отражать структуру административной панели один к одному.

Admin ориентирован на управление.

Public ориентирован на профессиональное решение.

---

# 13. PP-011 — Source ≠ Configuration ≠ Derived

Всегда разделять:

```text
Source
Configuration
Derived Result
```

Пример:

```text
Original Photo
+
Crop Configuration
=
Emotional Grid Cell
```

Crop не является новым Original Photo.

---

# 14. PP-012 — Originals Are Immutable

Оригинальные media assets не изменяются производными операциями.

Любое:

- thumbnail;
- crop;
- PDF version;
- Web version;

создаётся отдельно.

---

# 15. PP-013 — Reference, Don’t Duplicate

Если один файл используется в нескольких местах, система должна создавать relation, а не новую физическую копию без необходимости.

---

# 16. PP-014 — Domain Separation

Разные профессиональные понятия не должны смешиваться ради удобства реализации.

Обязательно разделять:

```text
Portfolio ≠ Project
Emotional Portfolio ≠ Project
Training ≠ Project
Casting ≠ Project
Feedback ≠ Casting
```

---

# 17. PP-015 — Presentation Is Not a New Domain

Такие представления, как:

- Casting Quick View;
- Homepage Selected Projects;
- Search Result;

не создают новые независимые источники данных.

---

# 18. PP-016 — Admin-Curated + User-Configurable

Для Questionnaire используется двойная модель:

```text
Admin Curated Questionnaire
+
Casting Specialist Custom Selection
```

Администратор предоставляет рекомендуемую профессиональную версию.

Профессиональный пользователь может адаптировать состав.

---

# 19. PP-017 — Start From a Useful Default

Пользователь не должен начинать Public Builder с пустого документа.

Система предлагает профессионально подготовленный template.

Пользователь корректирует только необходимое.

---

# 20. PP-018 — Content Selection, Not Document Design

Кастинг-специалист выбирает:

- какие данные;
- какие проекты;
- какие навыки;
- какие материалы.

Он не должен проектировать:

- typography;
- margins;
- pagination;
- PDF layout.

---

# 21. PP-019 — System Handles Layout

Система сама отвечает за профессиональное представление выбранного content.

Пользователь отвечает за selection.

---

# 22. PP-020 — Prepared Questionnaire Remains Authoritative Recommendation

Public Builder не должен создавать впечатление, что готовая анкета администратора вторична или неправильна.

Primary Prepared Questionnaire является рекомендованным вариантом.

Public Builder предоставляет гибкость.

---

# 23. PP-021 — Privacy by Projection

Private data не должна передаваться клиенту и затем скрываться интерфейсом.

Правильная модель:

```text
Domain Data
      ↓
Permission Filter
      ↓
Safe Projection
      ↓
Client
```

---

# 24. PP-022 — Visibility Is Contextual

Понятие Public не является единственным флагом.

Необходимо различать:

- Public Website;
- Prepared Questionnaire;
- Public Builder;
- Private Casting;
- Admin.

---

# 25. PP-023 — Least Data Necessary

Каждый интерфейс и AI-процесс получает минимально необходимый набор данных.

---

# 26. PP-024 — Human-in-the-loop

ИИ может:

- анализировать;
- структурировать;
- рекомендовать;
- создавать draft.

Человек принимает:

- фактическое решение;
- публикационное решение;
- профессиональное решение.

---

# 27. PP-025 — AI Is an Assistant, Not an Authority

ИИ не является источником профессионального факта.

Основная логика:

```text
Source Fact
≠
AI Interpretation
≠
AI Recommendation
≠
Human Decision
```

---

# 28. PP-026 — Missing Is Better Than Invented

Если информация отсутствует, система должна показать:

```text
Не указано
```

или запросить clarification.

Она не должна придумывать профессиональные факты.

---

# 29. PP-027 — Explainable AI

Пользователь должен иметь возможность понять:

- откуда взялся вывод;
- какие Source использованы;
- насколько вывод уверен;
- что предложено системой.

---

# 30. PP-028 — AI Drafts Never Auto-Publish

Любой AI-generated professional text остаётся Draft до explicit human action.

Это относится к:

- biography;
- project descriptions;
- cover letters;
- casting responses;
- Theme Proposal;
- social copy.

---

# 31. PP-029 — AI Must Not Alter Actor Appearance

ИИ не используется для изменения профессионального образа актрисы.

Запрещены:

- изменение лица;
- тела;
- возраста;
- мимики;
- волос;
- одежды;
- создание несуществующего professional portrait.

---

# 32. PP-030 — Safe Geometric Image Processing

Для профессиональных фотографий разрешаются только согласованные геометрические transformations там, где они необходимы:

- crop;
- scale;
- position;
- orientation.

---

# 33. PP-031 — Published Professional Snapshots Are Immutable

Исторически значимый опубликованный документ должен сохранять то состояние, которое было опубликовано.

Это особенно относится к:

- Questionnaire Revision;
- PDF Snapshot;
- Theme Revision;
- Casting Analysis Revision.

---

# 34. PP-032 — Changes Create New Revisions

Изменение Published content создаёт новую Revision или Draft.

Нельзя незаметно переписывать историю.

---

# 35. PP-033 — Rollback Is a New Event

Rollback не удаляет изменения из истории.

Он создаёт новое состояние на основе прежней Revision.

---

# 36. PP-034 — Draft and Published Are Separate Contexts

Администратор может работать над Draft без изменения текущего Public state.

---

# 37. PP-035 — Readiness Before Publication

Система должна автоматически проверить необходимые условия до публикации.

Принцип:

```text
Edit
→ Validate
→ Ready
→ Publish
```

---

# 38. PP-036 — Blocking Errors Must Be Explicit

Если действие невозможно, пользователь должен понимать:

- почему;
- какой объект;
- как исправить.

---

# 39. PP-037 — Warnings Must Not Masquerade as Errors

Не все потенциальные проблемы должны блокировать работу.

Система различает:

```text
Blocking Error
Warning
Information
```

---

# 40. PP-038 — Automation by Exception

Идеальная административная модель:

```text
Normal State
→ no attention required

Exception
→ user attention
```

Администратор не должен вручную проверять всё.

---

# 41. PP-039 — Automate Derived Work

Система должна максимально автоматизировать операции, которые не требуют профессионального решения:

- thumbnails;
- derivatives;
- readiness checks;
- link checks;
- stale detection;
- PDF rebuild preparation.

---

# 42. PP-040 — Do Not Automate Business Decisions

Автоматизация не принимает решения:

- публиковать ли материал;
- является ли опыт реальным;
- принять ли Casting;
- согласиться ли на Offer.

---

# 43. PP-041 — Virtual Operator Manages Attention

Основная задача Virtual Operator:

> не делать всё вместо пользователя, а показывать, где действительно требуется его внимание.

---

# 44. PP-042 — Virtual Operator Must Be Reversible

Предложения Virtual Operator должны:

- подтверждаться;
- отклоняться;
- редактироваться;

до изменения профессионального факта.

---

# 45. PP-043 — Public Builder Is Temporary by Default

Visitor-generated configuration не должна автоматически становиться постоянной Admin entity.

---

# 46. PP-044 — Public Builder Does Not Change Source Data

Selection внешнего пользователя существует только в рамках конкретного temporary representation.

---

# 47. PP-045 — Server Is the Authority for Public Permissions

Public Builder и другие публичные interfaces не доверяют client-side IDs или visibility.

Все permissions повторно проверяются server-side.

---

# 48. PP-046 — No Mandatory Registration for Basic Casting Journey

Базовый профессиональный Journey должен работать без создания аккаунта.

Регистрация не должна становиться искусственным препятствием между интересом и PDF/Contact.

---

# 49. PP-047 — Professional Documents Must Be Share-Ready

PDF должен оставаться полезным после пересылки третьему лицу.

Он должен позволять определить:

- актрису;
- дату формирования;
- официальный профиль.

---

# 50. PP-048 — QR Complements Hyperlink

QR не заменяет обычную кликабельную ссылку.

Если используется QR:

```text
Clickable URL
+
QR Code
```

---

# 51. PP-049 — Public URLs Must Be Safe

QR и Public PDF не должны содержать:

- Admin URL;
- credentials;
- private token без контролируемой модели;
- PII в URL.

---

# 52. PP-050 — Search Uses Structured Data

Профессиональный поиск должен опираться прежде всего на structured facts.

Не следует полагаться только на полнотекстовый поиск Biography.

---

# 53. PP-051 — Search Must Respect Visibility

Private или Draft information не индексируется в Public Search.

---

# 54. PP-052 — Mobile Is a First-Class Experience

Mobile Journey является полноценным профессиональным сценарием.

Он не должен считаться уменьшенной копией desktop.

---

# 55. PP-053 — Five-Minute Professional Journey

Ключевой кастинговый Journey должен быть рассчитан на профессионального пользователя с ограниченным временем.

Цель:

```text
Landing
→ Evaluate
→ Evidence
→ Questionnaire
→ Action
```

за пять минут или менее.

---

# 56. PP-054 — Quick Decision in Seconds

Первый экран должен позволять в течение нескольких секунд понять основные характеристики профессионального профиля.

---

# 57. PP-055 — Contextual CTA

Следующее действие должно соответствовать намерению посетителя.

Например:

- Invite to Casting;
- Offer Role;
- Request Materials.

---

# 58. PP-056 — One Communication Backend

Разные профессиональные CTA могут использовать единый Feedback workflow.

UI отличается по intent, backend остаётся согласованным.

---

# 59. PP-057 — Save Before Notify

Входящее обращение всегда сохраняется до попытки внешнего уведомления.

---

# 60. PP-058 — External Notification Is Non-Critical

Ошибка WhatsApp или другого внешнего канала не должна означать потерю Professional Inquiry.

---

# 61. PP-059 — Notification Data Minimization

Внешние уведомления должны содержать минимально необходимый объём информации.

---

# 62. PP-060 — Feedback ≠ Opportunity Automatically

Входящее сообщение не становится Casting автоматически.

Human review отделяет:

```text
Inbound Communication
```

от:

```text
Qualified Opportunity
```

---

# 63. PP-061 — Preserve Original Communication

При преобразовании Feedback в Casting оригинальный Feedback сохраняется неизменным.

---

# 64. PP-062 — Opportunity Outcome Is a Business Fact

Analytics Event не должен автоматически менять Casting Outcome.

---

# 65. PP-063 — Analytics Supports Decisions, Not Automatic Changes

Analytics предназначена для:

- понимания;
- рекомендаций;
- оптимизации.

Она не должна самостоятельно менять содержание сайта.

---

# 66. PP-064 — Business Outcome Over Vanity Metrics

Приоритет:

```text
Casting
Audition
Callback
Offer
Booked
```

выше, чем:

```text
Page Views
Session Duration
```

---

# 67. PP-065 — Attribution Must Be Privacy-Conscious

Источник трафика может анализироваться.

Личные данные профессионального посетителя не должны собираться без необходимости.

---

# 68. PP-066 — Theme Changes Presentation Only

Theme отвечает за внешний вид.

Theme не имеет права менять:

- facts;
- contacts;
- Questionnaire composition;
- access rules.

---

# 69. PP-067 — Theme Must Be Structured

Visual configuration должна быть представлена контролируемыми Design Tokens и settings.

Не arbitrary AI-generated executable code.

---

# 70. PP-068 — Theme Requires Preview

Визуальное изменение должно проверяться до публикации.

---

# 71. PP-069 — Accessibility Is a Publication Requirement

Критически нечитаемая тема не должна публиковаться.

---

# 72. PP-070 — Accessibility Is Not Optional Polish

Accessibility входит в базовое качество продукта, а не в пост-релизное улучшение.

---

# 73. PP-071 — Responsive Means Contextual, Not Merely Smaller

Mobile UI может отличаться компоновкой и взаимодействием от desktop, если это улучшает Journey.

---

# 74. PP-072 — Existing Valuable Features Must Not Regress

При реорганизации архитектуры нельзя терять уже полезные возможности без явно определённой replacement model.

---

# 75. PP-073 — Migration Before Removal

Legacy structure удаляется или архивируется только после:

- migration;
- validation;
- acceptance.

---

# 76. PP-074 — Migrations Must Be Idempotent

Повторный запуск migration не создаёт duplicate.

---

# 77. PP-075 — Ambiguous Migration Requires Human Review

Неоднозначную legacy запись нельзя автоматически переклассифицировать без проверки.

---

# 78. PP-076 — Provenance Must Be Preserved

После migration желательно сохранять связь с legacy source.

---

# 79. PP-077 — Archive Before Destructive Delete

Исторически значимые сущности по возможности архивируются.

---

# 80. PP-078 — Dependencies Must Be Visible

Если объект нельзя удалить, система должна показать почему.

---

# 81. PP-079 — Silent Overwrite Is Prohibited

Одновременное редактирование не должно приводить к скрытой потере данных.

---

# 82. PP-080 — Failure Must Be Contained

Сбой optional capability не должен ломать core product.

Примеры:

```text
AI unavailable
→ Public Site works

WhatsApp unavailable
→ Feedback works

Analytics unavailable
→ Questionnaire works
```

---

# 83. PP-081 — Core Must Work Without AI

Основные профессиональные функции должны иметь ручной путь выполнения.

AI повышает эффективность, но не является обязательной единственной точкой выполнения.

---

# 84. PP-082 — Derived Failure Must Preserve Last Valid Result

Неудачная пересборка новой производной версии не должна уничтожать последнюю корректную опубликованную версию.

---

# 85. PP-083 — Performance Serves Professional Flow

Оптимизация производительности должна фокусироваться на:

- first useful content;
- image loading;
- Video start;
- Builder responsiveness;
- PDF workflow.

---

# 86. PP-084 — Originals Are Not Public Thumbnails

Для gallery и lists используются оптимизированные derivatives.

---

# 87. PP-085 — SEO Must Reflect Professional Meaning

SEO metadata должно соответствовать содержанию страницы.

Theme не должна управлять смысловыми SEO metadata.

---

# 88. PP-086 — Deep Links Must Be Useful

Переход непосредственно на:

- Portfolio;
- Project;
- Questionnaire;

должен оставаться самостоятельным понятным Professional Journey.

---

# 89. PP-087 — Stable Public Identity

Public URL важных сущностей SHOULD быть стабильным.

Изменение presentation не должно необязательно менять canonical URL.

---

# 90. PP-088 — Multilingual Means End-to-End

Переключение языка должно распространяться на весь релевантный Journey:

```text
Public UI
→ Questionnaire
→ Builder
→ PDF
→ CTA
```

---

# 91. PP-089 — Translation Does Not Create Facts

Локализация изменяет язык представления, но не бизнес-смысл.

---

# 92. PP-090 — Professional Freshness Is Reviewed, Not Assumed

Возраст материала может быть сигналом для проверки, но не автоматическим основанием для удаления.

---

# 93. PP-091 — Every Capability Needs an Owner

Для каждой domain capability должно быть понятно:

- какая сущность является Source of Truth;
- какой модуль отвечает за lifecycle.

---

# 94. PP-092 — Every Derived Representation Needs Invalidation Rules

Для:

- PDF;
- Search;
- Grid;
- Public Projection;

должно быть понятно, какие Source changes делают его stale.

---

# 95. PP-093 — Every Public Capability Needs a Privacy Boundary

Перед созданием Public UI необходимо определить:

- какие данные разрешены;
- какие запрещены;
- где выполняется фильтрация.

---

# 96. PP-094 — Every AI Capability Needs a Data Boundary

Перед добавлением AI необходимо определить:

- какие данные передаются;
- зачем;
- какие данные запрещены;
- что происходит при failure.

---

# 97. PP-095 — Every Major Capability Needs a Customer Journey

Новый крупный capability не должен реализовываться без определённого use case.

---

# 98. PP-096 — Every Critical Rule Needs a Test

Критические Business Rules и Product Principles должны иметь проверяемое отражение в Test Strategy.

---

# 99. PP-097 — Documentation Is Part of the Product

Функция не считается полностью завершённой, если её целевое поведение не отражено в документации.

---

# 100. PP-098 — GitHub Issues Implement Decisions; They Do Not Invent Product Logic

GitHub Issue должен описывать исполнение уже утверждённого поведения.

Issue не является местом для скрытого принятия нового Product Decision.

---

# 101. PP-099 — Architecture Must Support the Target Product, Not Only the First Milestone

MVP может реализовывать часть функций, но архитектура не должна сознательно закрывать путь к уже утверждённому Target Product.

---

# 102. PP-100 — Simplicity for Users, Explicitness for Engineering

Пользовательский интерфейс должен быть простым.

Внутренняя архитектура и документация — максимально однозначными.

---

# 103. Сводка главных принципов

Все продуктовые решения должны в конечном итоге поддерживать следующую модель:

```text
ONE TRUSTED PROFILE
        ↓
STRUCTURED PROFESSIONAL DATA
        ↓
CONTROLLED PROJECTIONS
        ↓
FAST PROFESSIONAL UX
        ↓
READY OR CUSTOM QUESTIONNAIRE
        ↓
PROFESSIONAL CONTACT
        ↓
CASTING OPPORTUNITY
        ↓
HUMAN-CONTROLLED AI ASSISTANCE
        ↓
PROFESSIONAL OUTCOME
```

---

# 104. Checklist для нового решения

Перед утверждением любого нового capability необходимо проверить:

### Data

- Есть ли существующий Source of Truth?
- Создаётся ли duplicate?
- Кто владеет данными?

### UX

- Какая Persona?
- Какой Desired Outcome?
- Сколько действий?
- Работает ли mobile?

### Public

- Как выглядит Public representation?
- Какой CTA?
- Есть ли dead end?

### Privacy

- Какие данные передаются клиенту?
- Нужна ли отдельная visibility policy?

### Questionnaire

- Может ли это использовать Prepared Questionnaire?
- Public Builder?

### AI

- Нужен ли AI?
- Что AI может делать?
- Где human approval?

### Lifecycle

- Draft?
- Ready?
- Published?
- Archived?
- Revision?

### Reliability

- Что при failure?
- Есть ли fallback?

### Testing

- Как проверить результат?

---

# 105. Критерий соответствия Product Principles

Решение считается соответствующим продукту, если оно:

1. не создаёт второй Source of Truth;
2. минимизирует ручное повторение;
3. сохраняет domain boundaries;
4. не раскрывает private data;
5. поддерживает профессиональный Customer Journey;
6. минимизирует лишние действия;
7. сохраняет human control;
8. не выдаёт AI assumption за факт;
9. поддерживает rollback/revision для значимых состояний;
10. имеет понятный failure behaviour;
11. не разрушает уже утверждённый Target Product;
12. может быть проверено тестами.

---

# 106. Anti-Patterns

Следующие подходы считаются продуктово неправильными.

## AP-001 — Duplicate Facts

```text
Questionnaire stores separate copy of Skill
```

вместо relation/snapshot.

---

## AP-002 — Admin-Only Feature

Создан новый профессиональный раздел в Admin, но его Public use case не продуман.

---

## AP-003 — Public Full Payload

Server отдаёт private data, а UI скрывает их.

---

## AP-004 — AI Auto-Apply

AI recommendation сразу меняет Profile.

---

## AP-005 — Empty Builder

Casting Specialist получает 30 пустых checkbox и должен строить документ с нуля.

---

## AP-006 — Design-heavy Builder

Пользователь должен вручную верстать PDF.

---

## AP-007 — One Giant Questionnaire

Все профессиональные данные принудительно помещаются в один универсальный PDF.

---

## AP-008 — Notification Before Save

Система сначала пытается отправить WhatsApp, а затем сохраняет Feedback.

---

## AP-009 — Technical Status as Business Outcome

`analysis_completed` интерпретируется как `Qualified`.

---

## AP-010 — Theme Owns Content

Изменение Theme меняет Biography или Questionnaire.

---

## AP-011 — Migration by Guess

Все legacy `Other` автоматически объявляются Training.

---

## AP-012 — Analytics Auto-Optimization

Система сама удаляет блок из Questionnaire, потому что он редко используется.

---

# 107. Принцип конечной выгоды

Любой capability должен в конечном итоге улучшать хотя бы одно из четырёх направлений:

```text
1. Quality of Professional Representation
2. Speed of Casting Evaluation
3. Conversion to Professional Opportunity
4. Reduction of Administrative Work
```

Функция, не улучшающая ни одно направление, требует отдельного обоснования.

---

# 108. Product Decision Test

Для спорного решения задаются пять вопросов:

1. Делает ли оно профессиональную информацию достовернее?
2. Уменьшает ли friction?
3. Повышает ли полезность для кастинг-специалиста?
4. Уменьшает ли ручную работу без потери контроля?
5. Повышает ли вероятность профессионального действия?

Если ответы преимущественно отрицательные, решение не должно получать приоритет.

---

# 109. Главный принцип продукта

> **Продукт должен быть максимально простым в использовании, максимально строгим в отношении данных, максимально автоматизированным в рутинных операциях и максимально контролируемым человеком в профессионально значимых решениях.**

---

# 110. Связь с последующей документацией

Product Principles являются обязательным входом для:

- Customer Journey;
- Personas;
- Method 6-3-5;
- UX Principles;
- Domain Model;
- Architecture;
- Modules;
- Security;
- Testing;
- Acceptance Criteria.

Последующие документы могут конкретизировать эти принципы, но не должны менять их смысл без отдельного изменения Product Foundation.