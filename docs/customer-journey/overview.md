# CUSTOMER JOURNEY OVERVIEW

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Общая модель пользовательского пути профессионального сайта актрисы

**Целевой файл:** `docs/customer-journey/overview.md`  
**Документ:** DOC-020  
**Статус:** ✅ Completed  
**Тип:** Customer Journey Foundation

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`

---

# 1. Назначение документа

Настоящий документ определяет общую Customer Journey Model целевого продукта.

Документ отвечает на вопросы:

1. Какие пользователи взаимодействуют с системой.
2. Из каких источников они могут приходить.
3. Какие задачи они решают.
4. Как должна развиваться последовательность взаимодействия.
5. Какие данные должны быть доступны на каждом этапе.
6. Какие действия считаются профессионально значимыми.
7. Какие точки могут создавать friction.
8. Какие действия система должна автоматизировать.
9. Где обязательно решение человека.
10. Как внешний пользовательский путь связывается с внутренним административным workflow.
11. Как измеряется успешность Journey.
12. Как Journey должен использоваться в UX, Architecture и Testing.

---

# 2. Роль Customer Journey в проекте

Customer Journey является обязательным слоем между:

```text
Product Definition
        ↓
Customer Journey
        ↓
UX
        ↓
Architecture
        ↓
Implementation
```

Разработка публичного интерфейса MUST NOT строиться исключительно вокруг существующих разделов системы.

Главный вопрос:

> **какую профессиональную задачу пользователь пытается решить и как предоставить ему кратчайший корректный путь к результату.**

---

# 3. Основная цель внешнего Journey

Главный внешний путь:

```text
Discovery
    ↓
Recognition
    ↓
Initial Evaluation
    ↓
Professional Evidence
    ↓
Questionnaire
    ↓
Professional Action
    ↓
Casting Opportunity
    ↓
Professional Outcome
```

Где:

- `Discovery` — пользователь узнаёт о профиле;
- `Recognition` — понимает, кто перед ним;
- `Initial Evaluation` — проверяет первичное соответствие;
- `Professional Evidence` — изучает подтверждающие материалы;
- `Questionnaire` — получает структурированный профессиональный документ;
- `Professional Action` — связывается, запрашивает материал или приглашает;
- `Casting Opportunity` — обращение превращается в рабочий кастинг;
- `Professional Outcome` — Self-Tape, Audition, Callback, Offer или Booked.

---

# 4. Основная цель внутреннего Journey

Внутренний путь:

```text
New Information
      ↓
Classification
      ↓
Validation
      ↓
Human Approval
      ↓
Single Source of Truth
      ↓
Automatic Derivatives
      ↓
Publication
      ↓
Professional Interaction
      ↓
Opportunity Processing
      ↓
Outcome
      ↓
Profile Enrichment
```

Цель:

> **максимально исключить повторную ручную работу, сохраняя человеческий контроль над фактами, публикацией и профессиональными решениями.**

---

# 5. Два взаимосвязанных контура

Система имеет два основных контура.

## 5.1. External Professional Journey

Используется:

- Casting Director;
- Casting Assistant;
- Commercial Producer;
- Director;
- International Casting Specialist;
- другие профессиональные посетители.

## 5.2. Internal Administration Journey

Используется:

- Admin;
- Editor;
- Virtual Portfolio Operator.

Контуры соединяются через:

```text
Public Profile
Questionnaire
Feedback
Casting
Opportunity
```

---

# 6. Основные Persona Classes

В дальнейшем отдельные Persona Documents описывают их детально.

Базовые Persona:

```text
P01 Casting Director
P02 Casting Assistant
P03 Commercial Casting Producer
P04 Director / Producer
P05 International Casting Specialist
P06 Virtual Portfolio Operator
```

Дополнительные системные роли:

```text
Admin
Editor
Actor / Profile Owner
Public Visitor
```

---

# 7. Общий профессиональный контекст

Профессиональный посетитель может:

- одновременно рассматривать десятки кандидатов;
- находиться под ограничением времени;
- работать с desktop или smartphone;
- знать актрису заранее либо видеть впервые;
- иметь конкретные casting requirements;
- не знать внутреннюю структуру сайта;
- не хотеть читать длинный текст;
- хотеть переслать материал другому участнику production team.

Следовательно, интерфейс MUST позволять получить ценность без предварительного изучения навигации.

---

# 8. Entry Points

Пользователь может начать Journey не только с Home Page.

Минимальные Entry Point Classes:

```text
EP-01 Direct Home
EP-02 Search Engine
EP-03 Social Media
EP-04 Shared Profile Link
EP-05 Shared Portfolio Link
EP-06 Shared Questionnaire Link
EP-07 Shared Project Link
EP-08 Casting-Specific Link
EP-09 QR Code
EP-10 Referral / Messenger
```

Каждый важный Public Page SHOULD поддерживать самостоятельный профессиональный контекст.

---

# 9. Source-Aware Journey

Entry Source MAY влиять на presentation priority, но MUST NOT менять профессиональные факты.

Примеры:

## Search Engine

Повышенный приоритет:

- Professional Identity;
- structured facts;
- Portfolio;
- Questionnaire.

## Social Media

Повышенный приоритет:

- visual content;
- Video;
- Emotional Range;
- CTA.

## Direct Questionnaire Link

Повышенный приоритет:

- Questionnaire;
- Download;
- Customize;
- Contact.

## Casting-Specific Link

Повышенный приоритет:

- relevant questionnaire;
- selected materials;
- casting-related CTA.

---

# 10. Customer Journey Stages

Стандартный внешний Journey состоит из восьми стадий.

```text
CJ-STAGE-01 Discovery
CJ-STAGE-02 Recognition
CJ-STAGE-03 Qualification
CJ-STAGE-04 Evidence
CJ-STAGE-05 Questionnaire
CJ-STAGE-06 Action
CJ-STAGE-07 Casting
CJ-STAGE-08 Outcome
```

---

# 11. CJ-STAGE-01 — Discovery

## Цель пользователя

Найти или открыть профиль.

## Возможные источники

- search;
- social;
- recommendation;
- direct URL;
- PDF;
- QR;
- messenger;
- casting database reference.

## Требование продукта

Entry Page MUST:

- быстро загрузиться;
- идентифицировать актрису;
- дать доступ к следующему профессиональному действию.

---

# 12. CJ-STAGE-02 — Recognition

На этом этапе пользователь отвечает на вопрос:

> «Кто это и имеет ли смысл продолжать просмотр?»

Минимальные сигналы:

- имя;
- основное профессиональное фото;
- professional identity;
- location;
- languages;
- key professional facts;
- primary CTA.

Целевое время:

```text
несколько секунд
```

---

# 13. CJ-STAGE-03 — Qualification

На этом этапе пользователь проверяет базовое соответствие своей задаче.

Могут быть нужны:

- Close-Up;
- Full Body;
- appearance;
- languages;
- skills;
- location;
- experience.

Информация SHOULD быть структурированной и быстро сканируемой.

---

# 14. CJ-STAGE-04 — Professional Evidence

Пользователь хочет убедиться, что первичное впечатление подтверждается профессиональными материалами.

Evidence:

- Video Intro;
- Showreel;
- Portfolio;
- Emotional Grid;
- Selected Projects;
- Roles;
- Training;
- Achievements.

---

# 15. CJ-STAGE-05 — Questionnaire

После первичной оценки пользователь переходит к структурированному документу.

У него должно быть два пути:

```text
Prepared Questionnaire
        OR
Public Questionnaire Builder
```

---

# 16. Prepared Questionnaire Journey

Путь:

```text
Open Questionnaire
      ↓
Review
      ↓
Select Version / Language
      ↓
Download PDF
```

Это кратчайший рекомендуемый сценарий.

---

# 17. Public Questionnaire Builder Journey

Если готовый документ:

- слишком длинный;
- слишком короткий;
- не содержит нужный блок;
- содержит нерелевантную информацию;

пользователь получает:

```text
Customize This Questionnaire
        OR
Build Questionnaire
```

Далее:

```text
Select Template
      ↓
Review Defaults
      ↓
Enable / Disable Blocks
      ↓
Select Specific Items
      ↓
Preview
      ↓
Generate PDF
```

---

# 18. Public Builder UX Goal

Типовой Quick/Standard Journey SHOULD завершаться менее чем за три минуты.

Расширенный профессиональный Journey SHOULD завершаться менее чем за пять минут.

---

# 19. CJ-STAGE-06 — Professional Action

После получения достаточной информации посетитель должен иметь понятный следующий шаг.

Основные actions:

```text
Invite to Casting
Offer Role
Request Materials
Collaboration
Ask Question
Download Questionnaire
```

---

# 20. Contextual CTA

CTA SHOULD соответствовать текущему контексту.

Например:

на Questionnaire:

```text
Пригласить на кастинг
```

на Project:

```text
Запросить материалы
```

на Contacts:

```text
Связаться
```

---

# 21. CJ-STAGE-07 — Casting

Professional Inquiry ещё не является Casting Entity.

Путь:

```text
Professional Inquiry
      ↓
Saved Feedback
      ↓
Admin Review
      ↓
Create / Link Casting
      ↓
Casting Workspace
```

Это важная граница между публичным и внутренним Journey.

---

# 22. Casting Processing Journey

После создания Casting:

```text
Casting Source
      ↓
Requirements
      ↓
AI Analysis
      ↓
Profile Match
      ↓
Recommendations
      ↓
Human Review
      ↓
Casting Questionnaire Draft
      ↓
Professional Process
```

---

# 23. CJ-STAGE-08 — Outcome

Opportunity может перейти в:

```text
Materials Requested
Self-Tape
Audition
Callback
Offer
Booked
```

или:

```text
Closed — Not Selected
Withdrawn
```

---

# 24. Замкнутый цикл после Booked

Если Opportunity становится Booked:

```text
Booked
  ↓
Project
  ↓
Role
  ↓
New Photos / Video / Credits
  ↓
Profile Update
  ↓
Stronger Professional Profile
```

Так профессиональный результат возвращается в Content Lifecycle.

---

# 25. Five-Minute Rule

Ключевой принцип:

> **подготовленный профессиональный пользователь должен иметь возможность получить достаточно информации и выполнить целевое действие в пределах пяти минут.**

Это не означает, что всё содержимое должно помещаться в один экран.

Это означает отсутствие unnecessary friction.

---

# 26. UX Time Budget

Рекомендуемый ориентир:

| Этап | Целевое время |
|---|---:|
| Identification | 5–15 сек |
| Initial Qualification | 20–60 сек |
| Portfolio / Evidence | 30–90 сек |
| Video / Emotional Range | 30–120 сек |
| Questionnaire | 30–120 сек |
| Custom Builder | до 3–5 мин общего пути |
| Contact / Casting CTA | ≤30 сек до формы |

---

# 27. Click Budget

Ключевые действия SHOULD быть доступны:

| Задача | Цель |
|---|---:|
| Video Intro | 1 действие |
| Portfolio | 1 |
| Emotional Range | 1 |
| Questionnaire | 1 |
| Primary Contact | ≤1 |
| PDF | ≤2 |
| Casting CTA | ≤2 до формы |
| Language Switch | 1 |
| Search | 1 interaction |
| Public Builder | ≤1 от Questionnaire Page |

---

# 28. Customer Journey Success

Journey считается успешным, если пользователь:

1. понял Professional Identity;
2. нашёл необходимые профессиональные факты;
3. получил Evidence;
4. получил подходящую Questionnaire;
5. выполнил Professional Action.

Не требуется, чтобы пользователь посетил все страницы.

---

# 29. Short Journey Is Valid

Примеры корректных коротких путей:

```text
Search
→ Profile
→ Casting Invitation
```

```text
Direct Link
→ Questionnaire
→ PDF
```

```text
Social
→ Video
→ Contact
```

```text
Recommendation
→ Emotional Portfolio
→ Request Materials
```

---

# 30. Long Journey Is Also Valid

Некоторые пользователи могут подробно изучать:

```text
Home
→ Profile
→ Portfolio
→ Emotional Portfolio
→ Projects
→ Training
→ Questionnaire
→ Contact
```

Система MUST поддерживать подробный Journey, но не заставлять всех пользователей проходить его.

---

# 31. No Mandatory Funnel Sequence

Conversion Funnel используется для аналитики.

Он MUST NOT превращаться в техническое требование пройти все этапы.

Пользователь может пропускать стадии.

---

# 32. No Dead Ends

Каждый ключевой профессиональный Public Page SHOULD завершаться релевантным continuation.

Минимально:

```text
Portfolio
→ Video / Questionnaire / Contact

Emotional Portfolio
→ Questionnaire / Contact

Project
→ Questionnaire / Request Materials

Training
→ Profile / Questionnaire

Questionnaire
→ Download / Customize / Contact
```

---

# 33. Home Journey

Home Page выполняет функцию:

```text
Recognition
+
Qualification
+
Routing
```

Она не должна требовать длинного просмотра для доступа к основным actions.

---

# 34. Profile Journey

Profile Page отвечает:

- кто актриса;
- какие ключевые характеристики;
- какие Skills;
- какие Languages;
- какой основной опыт;
- где Evidence.

---

# 35. Portfolio Journey

Пользователь должен:

1. открыть Portfolio;
2. быстро выбрать интересующую category;
3. увидеть изображения;
4. при необходимости открыть detail/lightbox;
5. перейти к следующему Professional Action.

---

# 36. Emotional Portfolio Journey

Основной flow:

```text
Emotional Range Preview
      ↓
Emotional Grid
      ↓
Full Emotional Portfolio
```

или:

```text
Emotional Grid
→ Questionnaire
```

---

# 37. Project Journey

Основная схема:

```text
Selected Projects
      ↓
Project Detail
      ↓
Role / Media / Evidence
      ↓
Questionnaire / Request Materials
```

---

# 38. Questionnaire Hub Journey

Questionnaire Page должна работать как самостоятельный professional hub:

```text
Primary Questionnaire
      ↓
Download
      ↓
Alternative Prepared Versions
      ↓
Public Builder
      ↓
Professional CTA
```

---

# 39. Public Builder Browse-and-Add Journey

Помимо работы внутри Builder, MAY поддерживаться модель:

```text
Builder Session Started
      ↓
Browse Public Site
      ↓
Add Allowed Project / Photo / Emotional Grid
      ↓
Builder Indicator
      ↓
Continue Questionnaire
```

Если эта модель используется, она не должна перегружать обычного посетителя.

---

# 40. Language Journey

International User должен иметь возможность:

```text
Entry
→ Change Language
→ Continue Same Journey
```

Переключение языка SHOULD сохранять текущий logical context, если локализованный аналог доступен.

---

# 41. Mobile Journey

На Mobile MUST быть доступны:

- primary photo;
- Quick Facts;
- Video;
- Portfolio;
- Emotional Grid;
- Questionnaire;
- Builder;
- Contact.

Не допускается, чтобы значимый CTA был доступен только desktop.

---

# 42. Casting Quick View Journey

Casting Quick View должен позволять пройти сокращённый профессиональный путь:

```text
Quick View
      ↓
Key Facts
      ↓
Photo Evidence
      ↓
Video / Emotional Grid
      ↓
Questionnaire / CTA
```

---

# 43. Search Journey

Public Search:

```text
Enter Query
      ↓
Relevant Structured Results
      ↓
Open Source Section
      ↓
Professional Action
```

Примеры запросов:

- английский;
- вокал;
- театр;
- specific project.

---

# 44. QR Journey

QR Code должен сокращать переход из физического/PDF-контекста в актуальный digital profile.

Пример:

```text
PDF
→ Scan QR
→ Current Public Profile
```

QR MUST NOT вести на internal/admin resources.

---

# 45. Professional Share Journey

Если PDF или Page переслан третьему лицу, новый получатель должен самостоятельно понять:

- чья информация;
- что за документ;
- где официальный профиль;
- как связаться.

---

# 46. Feedback Journey

Flow:

```text
CTA
 ↓
Context-Aware Form
 ↓
Enter Details
 ↓
Consent
 ↓
Submit
 ↓
Reference Number
```

System-side:

```text
Validate
 ↓
Persist
 ↓
Confirm User
 ↓
Notify Admin
 ↓
External Notification
```

---

# 47. Feedback Error Journey

Если validation error:

- введённые данные SHOULD сохраняться;
- ошибка указывает поле;
- пользователь может исправить и повторить.

Если WhatsApp fails:

- пользовательский success не отменяется;
- Admin видит internal failure.

---

# 48. Feedback → Casting Journey

Admin:

```text
Open Feedback
 ↓
Review
 ↓
Create Casting
 ↓
Preview Mapping
 ↓
Confirm
 ↓
Casting Created
```

Feedback остаётся доступным.

---

# 49. Internal Admin Journey

Основной внутренний путь:

```text
Dashboard
 ↓
Exception
 ↓
Entity
 ↓
Resolve
 ↓
Automatic Revalidation
 ↓
Updated State
```

Главная страница Admin не должна требовать ручного обхода всех разделов.

---

# 50. Virtual Operator Journey

Virtual Operator:

```text
Observe Domain Changes
      ↓
Detect Issue / Opportunity
      ↓
Produce Observation
      ↓
Produce Suggestion
      ↓
Admin Review
      ↓
Approve / Modify / Reject
      ↓
Execute Safe Action
```

---

# 51. Virtual Operator Example — New Photos

```text
Upload Photos
 ↓
Media Assets
 ↓
Operator Analysis
 ↓
Possible Classification
 ↓
Admin Confirmation
 ↓
Portfolio / Emotional Portfolio Relations
 ↓
Derivative Generation
```

---

# 52. Virtual Operator Example — Missing Data

```text
Emotional Portfolio Created
 ↓
Shooting Date Missing
 ↓
Operator Detects Blocker
 ↓
Dashboard Alert
 ↓
Admin Adds Date
 ↓
Readiness Recalculated
```

---

# 53. Virtual Operator Example — Broken Link

```text
Scheduled / Triggered Validation
 ↓
External Link Unavailable
 ↓
Observation
 ↓
Admin Alert
 ↓
Fix / Disable
```

Никакого автоматического удаления.

---

# 54. BB Assistant Journey

```text
Open Content Field
 ↓
Request AI Assistance
 ↓
Select Source Context
 ↓
Generate Draft
 ↓
Review
 ↓
Edit
 ↓
Apply Draft
 ↓
Manual Save / Publish Flow
```

Apply Draft не означает Publish.

---

# 55. AI Casting Journey

```text
Casting Source Available
 ↓
Analyze
 ↓
Extracted Requirements
 ↓
Confidence
 ↓
Compare Profile
 ↓
Recommendations
 ↓
Human Review
```

---

# 56. Theme Journey

```text
Current Theme
 ↓
Prompt / Manual Change
 ↓
Theme Proposal
 ↓
Edit / Lock
 ↓
Preview
 ↓
Accessibility Check
 ↓
Publish
```

---

# 57. Failure Journey — AI

Если AI недоступен:

- Casting MAY быть обработан вручную;
- Profile остаётся доступен;
- Public Site работает;
- Admin не теряет Source Data.

---

# 58. Failure Journey — PDF

Если PDF generation fails:

- HTML remains available where permitted;
- error visible;
- last valid Published PDF SHOULD remain where architecture allows;
- Draft generation can be retried.

---

# 59. Failure Journey — Notifications

Если external notification fails:

```text
Feedback = saved
Internal Notification = exists
External Notification = failed
Admin = informed
Retry = possible
```

---

# 60. Failure Journey — Media Derivative

Если новый derivative generation fails:

- Original remains safe;
- last valid published derivative remains available where applicable;
- new state is marked error/stale.

---

# 61. Journey Friction Categories

Friction классифицируется:

```text
FRC-01 Navigation Friction
FRC-02 Information Friction
FRC-03 Form Friction
FRC-04 Document Friction
FRC-05 Performance Friction
FRC-06 Trust Friction
FRC-07 Language Friction
FRC-08 Privacy Friction
FRC-09 Administrative Friction
```

---

# 62. Navigation Friction

Примеры:

- пользователь не знает, где Video;
- Contact спрятан;
- Questionnaire требует несколько меню.

Должно минимизироваться через information hierarchy и CTA.

---

# 63. Information Friction

Примеры:

- необходимые facts находятся только в Biography;
- Projects не структурированы;
- отсутствуют dates.

Снижается за счёт structured data.

---

# 64. Form Friction

Примеры:

- слишком много required fields;
- пользователь вынужден повторять contact info;
- ошибка очищает форму.

---

# 65. Document Friction

Примеры:

- один огромный PDF;
- отсутствует язык;
- невозможно выбрать состав;
- broken URLs.

Public Builder непосредственно решает этот класс friction.

---

# 66. Trust Friction

Возникает при:

- непонятной актуальности материалов;
- broken links;
- несогласованных facts;
- неясном источнике PDF.

Снижается через:

- Shooting Date;
- Generated Date;
- official profile link;
- structured facts.

---

# 67. Administrative Friction

Возникает при:

- повторном вводе;
- ручной пересборке;
- ручной проверке всех страниц;
- ручном поиске stale content.

Снижается Virtual Operator и derived automation.

---

# 68. Customer Journey Metrics

Для каждого Journey могут использоваться четыре класса метрик.

## Reach

- entry sessions;
- source distribution.

## Engagement

- Video;
- Portfolio;
- Emotional Range;
- Questionnaire.

## Intent

- PDF;
- Custom Builder;
- Request Materials;
- Contact.

## Business Outcome

- Casting;
- Audition;
- Callback;
- Offer;
- Booked.

---

# 69. Метрики не определяют качество в одиночку

Высокое время на сайте может означать:

- интерес;

но также:

- сложность поиска.

Поэтому Journey Metrics необходимо интерпретировать в контексте Conversion.

---

# 70. Customer Journey Events

Рекомендуемые logical events:

```text
profile_viewed
quick_view_opened
portfolio_opened
portfolio_filter_used
video_started
emotional_grid_viewed
questionnaire_viewed
questionnaire_downloaded
public_builder_opened
public_builder_template_selected
public_builder_pdf_generated
contact_started
feedback_submitted
casting_created
opportunity_stage_changed
```

---

# 71. Public Builder Analytics Boundary

MAY анализироваться:

- выбранный template;
- включённые block categories;
- PDF generated.

External analytics MUST NOT получать private contact values или содержимое Casting.

---

# 72. Customer Journey и SEO

SEO должен приводить пользователя к релевантной Entry Point.

Например:

- Profile;
- Portfolio;
- Project.

Deep Link MUST поддерживать самостоятельный Professional Journey.

---

# 73. Customer Journey и Content Priority

Информация должна быть визуально приоритизирована:

```text
P0 — identity / video / questionnaire / contact
P1 — appearance / skills / languages / emotional
P2 — projects / training / achievements
P3 — archive / extended materials
```

---

# 74. Customer Journey и Public Navigation

Главная навигация должна поддерживать профессиональные задачи.

Рекомендуемая логика:

```text
Profile
Portfolio
Emotional Range
Projects
Questionnaire
Contacts
```

Дополнительные разделы не должны заслонять основные.

---

# 75. Customer Journey и Admin Navigation

Admin Navigation должна быть ориентирована на domain management.

Dashboard при этом ориентирован на Exceptions.

Это разные UX-задачи.

---

# 76. Customer Journey и Visibility

Если данные недоступны пользователю, Journey не должен:

- показывать пустой placeholder с private content;
- выдавать скрытые IDs;
- предлагать недоступное действие.

---

# 77. Customer Journey и Archived Content

Archived Content не участвует в обычных новых Journeys.

Historical immutable document MAY продолжать ссылаться на snapshot архивного source.

---

# 78. Journey Continuity

При переходе между страницами SHOULD сохраняться полезный context:

- language;
- Public Builder Session;
- source attribution;
- разумный back-navigation state.

---

# 79. Journey Continuity — Builder

Если Public Builder session active, пользователь MAY перейти к разрешённому Public content и затем вернуться к текущей подборке без потери selections.

---

# 80. Session Expiration

Если temporary Builder Session expired:

- пользователь получает ясное сообщение;
- private/hidden data не восстанавливаются из небезопасного client state;
- MAY быть предложено начать новую session.

---

# 81. Customer Journey и Personalization

Продукт MAY адаптировать presentation по source/context.

Однако MUST NOT создавать скрытый профиль конкретного casting specialist без отдельного согласованного scope.

---

# 82. Journey Safety

Сокращение количества действий MUST NOT обходить:

- consent;
- server validation;
- authorization;
- human approval;
- readiness validation.

---

# 83. Journey Accessibility

Любой критический Journey MUST поддерживаться:

- keyboard;
- screen-reader semantics;
- visible focus;
- accessible forms;
- understandable error messages.

---

# 84. Journey Responsiveness

Тестируется минимум:

```text
Desktop
Tablet
Mobile
```

Не только визуальное расположение, но и достижимость целевого результата.

---

# 85. Journey Quality Gate

Public capability не готов к implementation, если не определены:

```text
Persona
Entry Point
Goal
Required Information
Primary CTA
Secondary CTA
Happy Path
Error Path
Mobile Path
Conversion Event
```

---

# 86. Customer Journey ID Model

Все отдельные Journeys должны иметь стабильный ID.

Рекомендуемый формат:

```text
CJM-CD-...
CJM-CA-...
CJM-CP-...
CJM-DIR-...
CJM-INT-...
CJM-VOP-...
```

---

# 87. User Flow IDs

Детальные interaction flows используют:

```text
UF-PUB-...
UF-ADM-...
UF-PQB-...
UF-CST-...
UF-FDB-...
```

Customer Journey выше по уровню, чем User Flow.

---

# 88. Customer Journey ≠ User Flow

Customer Journey описывает:

- мотивацию;
- контекст;
- весь путь;
- touchpoints;
- outcome.

User Flow описывает конкретную последовательность действий внутри capability.

---

# 89. Journey Mapping Template

Для каждого Persona Journey в последующих документах фиксировать:

1. Persona.
2. Scenario.
3. Entry Source.
4. Device.
5. Time Budget.
6. User Goal.
7. Preconditions.
8. Stages.
9. Touchpoints.
10. Questions in user’s mind.
11. Information required.
12. Actions.
13. System responses.
14. Friction.
15. Automation.
16. Trust signals.
17. Primary CTA.
18. Secondary CTA.
19. Conversion.
20. Failure paths.
21. Metrics.
22. Relevant Business Rules.
23. Relevant Product Principles.

---

# 90. Emotional State

Для Customer Journey MAY фиксироваться профессиональное состояние пользователя:

- uncertain;
- scanning;
- interested;
- evaluating;
- ready to act.

Цель — убрать confusion, а не искусственно манипулировать эмоциями.

---

# 91. Trust Signals

В профессиональном Journey доверие поддерживают:

- качественные реальные photos;
- Shooting Date;
- structured experience;
- clear contacts;
- official profile URL;
- generated date;
- consistent language;
- working media;
- clear professional identity.

---

# 92. Public Builder Trust

Пользователь должен понимать:

- он выбирает реальные опубликованные данные;
- исходный профиль не изменяется;
- PDF сформирован официальным сайтом;
- выбор существует только для его документа.

---

# 93. Administrative Trust

Admin должен понимать:

- что сделано AI;
- что подтверждено человеком;
- что опубликовано;
- что Draft;
- что stale;
- что требует действия.

---

# 94. Marketing Journey

Маркетинговое продвижение должно приводить пользователя не просто на Home Page, а при необходимости на наиболее релевантный Entry Point.

Примеры:

```text
Campaign about showreel
→ Video-centric landing/context

Casting-focused sharing
→ Questionnaire

Visual social campaign
→ Portfolio / Emotional Range
```

---

# 95. Marketing Must Not Distort Professional Facts

Campaign presentation MAY менять:

- Entry Page;
- ordering;
- highlighted content.

Не меняет:

- skills;
- experience;
- contacts policy;
- professional facts.

---

# 96. Conversion Funnel

Общая модель:

```text
Reach
 ↓
Relevant Visit
 ↓
Professional Evaluation
 ↓
Evidence Engagement
 ↓
Questionnaire Intent
 ↓
PDF / Custom PDF
 ↓
Professional Inquiry
 ↓
Casting
 ↓
Audition
 ↓
Callback
 ↓
Offer
 ↓
Booked
```

---

# 97. Funnel Is Non-Linear

Допустимы переходы:

```text
Profile → Contact
Questionnaire → Casting
Social → Video → Casting
Project → Request Materials
```

Analytics должна учитывать такие короткие pathways.

---

# 98. External-to-Internal Handoff

Критический момент продукта:

```text
Public Professional Action
      ↓
Persisted Internal Record
```

Например:

```text
Casting Invitation
      ↓
Feedback
```

Данные не должны зависеть от transient notification.

---

# 99. Internal-to-External Handoff

После подготовки данных Admin может:

- опубликовать Questionnaire;
- обновить Public Profile;
- предоставить public link.

Но только через explicit allowed action.

---

# 100. Customer Journey Testing

Каждый Persona Journey должен стать E2E Customer Journey Test.

Минимально:

```text
E2E-CJM-CD
E2E-CJM-CA
E2E-CJM-CP
E2E-CJM-DIR
E2E-CJM-INT
E2E-CJM-VOP
```

---

# 101. Customer Journey Acceptance Principle

Journey считается принятым, если:

1. Persona достигает Desired Outcome;
2. не требуется знание внутренней архитектуры сайта;
3. private data не раскрывается;
4. ключевые действия достижимы;
5. mobile Journey работоспособен, где применимо;
6. failure path понятен;
7. все critical Business Rules соблюдены.

---

# 102. Customer Journey Improvement Loop

После production data:

```text
Journey
 ↓
Analytics
 ↓
Friction Detection
 ↓
Recommendation
 ↓
Human Product Decision
 ↓
UX Change
 ↓
Retest
```

Analytics не применяет изменение автоматически.

---

# 103. Главное различие внешнего и внутреннего Journey

External:

```text
Minimum Information Search
Maximum Professional Relevance
```

Internal:

```text
Minimum Repetitive Administration
Maximum Data Integrity
```

---

# 104. Главный внешний Customer Journey

```text
ENTRY
 ↓
WHO IS THIS?
 ↓
DOES SHE FIT?
 ↓
SHOW ME EVIDENCE
 ↓
GIVE ME THE RIGHT QUESTIONNAIRE
 ↓
LET ME ACT
```

---

# 105. Главный внутренний Customer Journey

```text
WHAT CHANGED?
 ↓
WHAT REQUIRES ATTENTION?
 ↓
WHAT CAN BE AUTOMATED?
 ↓
WHAT REQUIRES MY DECISION?
 ↓
APPLY
 ↓
VERIFY
```

---

# 106. Definition of Journey Consistency

Новый UX считается согласованным с Customer Journey Architecture, если:

1. определена Persona;
2. определён Entry Point;
3. известен Desired Outcome;
4. информация показана в порядке профессиональной ценности;
5. Primary CTA понятен;
6. unnecessary friction минимизирован;
7. no-dead-end принцип соблюдён;
8. Public/Private boundary соблюдена;
9. Journey не зависит необоснованно от AI;
10. результат может быть протестирован.

---

# 107. Anti-Patterns Customer Journey

## CJ-AP-001 — Navigation First

Посетителю предлагается сначала понять структуру меню.

## CJ-AP-002 — Biography Gate

Чтобы узнать базовые факты, нужно прочитать длинную Biography.

## CJ-AP-003 — One PDF Fits All

Профессиональному пользователю предлагается только один неизменяемый огромный PDF.

## CJ-AP-004 — Registration Gate

Для получения обычной анкеты требуется регистрация.

## CJ-AP-005 — Mobile CTA Hidden

Ключевое действие недоступно на smartphone.

## CJ-AP-006 — Dead-End Portfolio

После Portfolio отсутствует дальнейший Professional CTA.

## CJ-AP-007 — Notification Is the Workflow

Входящий Casting существует только в WhatsApp message.

## CJ-AP-008 — Admin Must Check Everything

Нет exception-driven dashboard.

## CJ-AP-009 — AI Decides

AI автоматически меняет professional state.

## CJ-AP-010 — Marketing Landing Without Professional Context

Campaign ведёт на декоративную страницу без быстрого доступа к professional data.

---

# 108. Связь Customer Journey с дальнейшими документами

Настоящий документ является обязательным входом для:

- `personas.md`;
- `method-6-3-5.md`;
- отдельных Persona Journey;
- `conversion-funnel.md`;
- `automation-matrix.md`;
- UX package;
- Functional Requirements;
- E2E Testing.

---

# 109. Итоговая Customer Journey Model

```text
                   EXTERNAL PROFESSIONAL JOURNEY

Discovery
   ↓
Recognition
   ↓
Qualification
   ↓
Evidence
   ↓
Prepared Questionnaire ───────┐
                              ├──→ Professional Action
Public Questionnaire Builder ─┘
                                      ↓
                                    Feedback
                                      ↓
                                    Casting
                                      ↓
                                    Outcome
                                      ↓
                                     Role


                   INTERNAL PROFESSIONAL JOURNEY

New Data / Inquiry
       ↓
Virtual Operator / Admin
       ↓
Validation
       ↓
Human Decision
       ↓
Structured Domain Data
       ↓
Derived Representations
       ↓
Public Professional Experience
       ↓
Professional Opportunity
       ↓
Outcome
       ↓
Profile Enrichment
```

---

# 110. Финальный принцип

> **Customer Journey должен обеспечивать кастинг-специалисту максимально короткий путь к профессионально значимой информации и действию, а администратору — максимально короткий путь от нового материала или обращения до корректного обновления системы без повторной ручной работы.**

Любой последующий UX или технический сценарий, усложняющий один из этих путей без обоснованной причины безопасности, достоверности или контроля, должен рассматриваться как кандидат на переработку.