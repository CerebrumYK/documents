# CASTING DIRECTOR JOURNEY

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Подробный Customer Journey кастинг-директора

**Целевой файл:** `docs/customer-journey/casting-director.md`  
**Документ:** DOC-023  
**Статус:** ✅ Completed  
**Тип:** Customer Journey

**Persona:** `PERSONA-CD — Casting Director`  
**Рабочее имя Persona:** Ирина

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/overview.md`
- `docs/customer-journey/personas.md`
- `docs/customer-journey/method-6-3-5.md`

---

# 1. Назначение документа

Настоящий документ описывает полный Customer Journey кастинг-директора.

Он должен стать нормативным источником для дальнейшего проектирования:

- Home;
- Profile;
- Casting Quick View;
- Public Search;
- Portfolio;
- Emotional Portfolio;
- Video;
- Projects;
- Questionnaire;
- Public Questionnaire Builder;
- Feedback;
- Casting CTA;
- Marketing Analytics;
- E2E Tests.

Основной вопрос документа:

> **Как позволить кастинг-директору за несколько минут определить профессиональное соответствие актрисы, получить необходимые доказательства, сформировать подходящий документ и перейти к следующему кастинговому действию?**

---

# 2. Journey ID

Основной Journey:

```text
CJM-CD-001
```

Название:

```text
Casting Director — Initial Evaluation to Casting Action
```

---

# 3. Persona Summary

## Persona

Casting Director.

## Основная профессиональная задача

Подобрать кандидатов для конкретной роли или проекта.

## Decision Authority

Высокая.

Persona может:

- добавить актрису в shortlist;
- запросить дополнительные материалы;
- пригласить на кастинг;
- передать кандидатуру режиссёру;
- исключить кандидатуру из дальнейшего рассмотрения.

---

# 4. Профессиональный контекст

Типичный Casting Director:

- одновременно рассматривает несколько или десятки кандидатов;
- не обязан быть знаком с актрисой заранее;
- имеет конкретные требования роли;
- работает в условиях ограниченного времени;
- может возвращаться к профилю несколько раз;
- может пересылать материалы коллегам;
- ожидает структурированную, достоверную и актуальную профессиональную информацию.

---

# 5. Основная проблема Persona

Кастинг-директор не должен самостоятельно собирать профессиональный профиль из:

- Biography;
- Instagram;
- отдельных фотографий;
- облачных папок;
- старых PDF;
- сообщений;
- различных видеоссылок.

Сайт должен объединять эти данные в управляемую профессиональную последовательность.

---

# 6. Desired Outcome

Основной результат:

```text
Qualified Professional Decision
```

Далее один из вариантов:

```text
Invite to Casting
Request Materials
Download Questionnaire
Build Custom Questionnaire
Shortlist Candidate
```

---

# 7. Business Outcome

Предпочтительная конверсия:

```text
Relevant Profile Visit
→ Qualified Interest
→ Casting Invitation
```

Дополнительные конверсии:

```text
Questionnaire Download
Custom PDF
Request Materials
Video Engagement
```

---

# 8. Time Budget

Основной ориентир:

```text
0–15 секунд
Recognition

15–60 секунд
Initial Qualification

1–2 минуты
Professional Evidence

2–4 минуты
Questionnaire

4–5 минут
Professional Action
```

Целевой Journey:

```text
≤ 5 минут
```

---

# 9. Device Context

Основной:

- Desktop;
- Notebook.

Дополнительный:

- Tablet;
- Smartphone.

Desktop является наиболее вероятным профессиональным рабочим сценарием, но Journey MUST быть полностью работоспособен на Mobile.

---

# 10. Entry Sources

Casting Director может попасть на сайт через:

```text
CD-ENTRY-01 Direct URL
CD-ENTRY-02 Search Engine
CD-ENTRY-03 Recommendation
CD-ENTRY-04 Messenger Link
CD-ENTRY-05 Social Media
CD-ENTRY-06 Shared Questionnaire
CD-ENTRY-07 Shared Project
CD-ENTRY-08 QR Code
CD-ENTRY-09 Casting-Specific Link
```

---

# 11. Entry Point Principle

Необходимо исходить из того, что пользователь может открыть не Home Page.

Поэтому:

- Profile;
- Portfolio;
- Project;
- Emotional Portfolio;
- Questionnaire;

должны сохранять:

- Actress Identity;
- main navigation;
- key CTA;
- access to Questionnaire;
- access to Contact.

---

# 12. Основной Journey

```text
Entry
 ↓
Recognition
 ↓
Initial Qualification
 ↓
Professional Evidence
 ↓
Questionnaire
 ↓
Ready PDF
      OR
Public Builder
 ↓
Professional Action
 ↓
Casting Invitation / Request Materials
```

---

# 13. Journey Stage CD-01 — Entry

**Time:** `00:00`

Пользователь открывает ссылку.

Система должна максимально быстро показать полезный профессиональный контент.

---

# 14. User Question — CD-01

Первый внутренний вопрос:

> «Кто это?»

---

# 15. Required Information — CD-01

Первые элементы:

- Primary Close-Up;
- Actress Name;
- Professional Identity;
- Location;
- 2–5 key facts;
- Languages;
- Primary CTA.

---

# 16. System Behaviour — CD-01

При загрузке страницы система SHOULD:

1. отдать critical content без ненужной задержки;
2. использовать оптимизированный Primary Image;
3. не блокировать страницу тяжёлым animation;
4. сразу показать основные actions.

---

# 17. Preferred Hero

Концептуальная структура:

```text
[ Primary Close-Up ]

Екатерина ...
Актриса

Алматы / ...
Русский · English B2
Ключевой навык · Ключевой навык

[ Смотреть видео ]
[ Актёрская анкета ]
[ Пригласить на кастинг ]
```

Точный UI определяется позже.

---

# 18. Primary CTA Hierarchy

Для Casting Director:

### Primary

```text
Актёрская анкета
```

или в casting-context:

```text
Пригласить на кастинг
```

### High-priority secondary

```text
Смотреть видео
```

---

# 19. Journey Stage CD-02 — Recognition

**Time:** `00:00–00:15`

Пользователь должен понять:

- имя;
- профессиональную принадлежность;
- визуальный типаж;
- географический контекст;
- ключевые профессиональные параметры.

---

# 20. Recognition Success Condition

Через 15 секунд Casting Director должен быть способен сказать:

> «Я понимаю, чей это профиль и имеет ли смысл продолжить первичную проверку.»

---

# 21. Recognition Failure

Journey считается неудовлетворительным, если пользователь:

- не видит основное фото;
- не понимает профессию;
- вынужден скроллить до имени;
- вынужден открыть Biography для Location;
- не видит никакого следующего действия.

---

# 22. Journey Stage CD-03 — Initial Qualification

**Time:** `00:15–01:00`

Цель:

быстро проверить формальное или первичное профессиональное соответствие.

---

# 23. User Questions — CD-03

Типичные вопросы:

```text
Как выглядит крупным планом?
Как выглядит в полный рост?
Где находится?
Какие языки?
Какие навыки?
Есть ли релевантный опыт?
```

---

# 24. Required Information — Qualification

Минимально:

- Primary Close-Up;
- Primary Full Body;
- Appearance data;
- Location;
- Languages;
- Language Levels;
- Key Skills;
- availability of Video;
- link to Projects.

---

# 25. Structured Facts Requirement

Ключевые факты MUST NOT требовать чтения Biography.

Пример:

```text
Languages
Русский — native
English — B2
```

вместо:

```text
В свободное время изучает английский...
```

---

# 26. Casting Quick View

На этом этапе особенно полезен:

```text
Casting Quick View
```

Он должен агрегировать:

- Close-Up;
- Full Body;
- location;
- languages;
- skills;
- key parameters;
- Video;
- Emotional Grid;
- selected experience.

---

# 27. Quick View Principle

Casting Quick View не должен становиться новым data store.

Он является:

```text
Projection
```

над существующими Master Data.

---

# 28. Public Search

Если Casting Director ищет конкретный критерий:

```text
английский
танцы
вокал
вождение
```

он SHOULD иметь возможность получить ответ через Public Search.

---

# 29. Search Result Behaviour

Результат должен:

1. показать найденный факт;
2. указать его category;
3. дать переход к исходному Public section.

Например:

```text
English — B2
Languages
```

---

# 30. Search Privacy

Public Search MUST NOT индексировать:

- private Contacts;
- Castings;
- Feedback;
- internal Notes;
- unpublished content;
- AI drafts.

---

# 31. Journey Stage CD-04 — Visual Evidence

**Time:** примерно `00:30–01:15`

Пользователь проверяет реальные профессиональные фотографии.

---

# 32. Portfolio Requirements

Portfolio должно позволять быстро открыть:

```text
Close-Up
Full Body
Profile
Three Quarter
Appearance
Staged
```

---

# 33. Portfolio Filter Principle

Кастинг-директор не должен вручную пролистывать всю галерею, чтобы найти Full Body.

---

# 34. Shooting Date

Если известна дата фотосессии, она SHOULD быть доступна.

Она служит:

```text
Professional Freshness Signal
```

но не должна автоматически определять пригодность материала.

---

# 35. Image Authenticity

Кастинг-директор должен быть уверен, что профессиональные Portfolio Images не содержат продуктово разрешённых синтетических изменений внешности.

Допускаются только утверждённые представления и derivatives.

---

# 36. Journey Stage CD-05 — Video Evidence

**Time:** примерно `00:45–01:30`

Пользователь открывает Video Intro.

---

# 37. Video Purpose

Video Intro используется для оценки:

- поведения в кадре;
- речи;
- общего профессионального присутствия;
- визуальной динамики;
- естественности представления.

---

# 38. Video UX Requirements

Video Intro SHOULD:

- открываться за одно действие;
- быть обозначено понятным label;
- не требовать поиска в общем Media Archive;
- работать на Desktop и Mobile;
- иметь graceful fallback при недоступности.

---

# 39. Showreel

Если пользователь хочет глубже проверить опыт:

```text
Video Intro
→ Showreel
```

Showreel не должен заменять Video Intro как единственный основной video asset.

---

# 40. Video Failure State

Если Video недоступно:

- UI не должен зависать;
- должна быть понятная ошибка;
- остальные части Journey продолжают работать;
- Virtual Operator/Admin SHOULD получать сигнал о broken public link.

---

# 41. Journey Stage CD-06 — Emotional Range

**Time:** примерно `01:15–02:00`

Пользователь оценивает эмоциональный диапазон.

---

# 42. First Emotional Touchpoint

Сначала рекомендуется показывать:

```text
Emotional Grid
```

поскольку оно позволяет провести первичную оценку быстрее полного альбома.

---

# 43. Emotional Grid Information

Пользователь должен видеть:

- Composite;
- Shooting Date;
- при необходимости краткое описание;
- переход к Full Emotional Portfolio.

---

# 44. Emotional Portfolio Deep Dive

Если Casting Director заинтересован:

```text
Emotional Grid
→ Full Emotional Portfolio
```

Он получает более подробный набор фотографий.

---

# 45. Emotional Authenticity

Система не должна изменять:

- facial expression;
- face;
- age;
- appearance;

при создании Emotional Grid.

---

# 46. Emotional Grid Trust

Grid представляет реальные фотографии, а не AI-generated representation.

Это важный professional trust principle.

---

# 47. Journey Stage CD-07 — Experience Evidence

**Time:** примерно `01:45–02:45`

Пользователь проверяет профессиональный опыт.

---

# 48. Projects Information Strategy

Первым слоем показываются:

```text
Featured / Selected Projects
```

а не весь архив.

---

# 49. Project Preview

Для каждого Selected Project желательно быстро показать:

- title;
- type;
- Role;
- year/date where relevant;
- image/video if relevant.

---

# 50. Project Detail

При переходе в Project:

```text
Project
 ├── Role
 ├── Role Photos
 ├── Video
 ├── Description
 └── relevant Achievement
```

---

# 51. Role-Specific Evidence

Если Project имеет Role-specific photos или media, Casting Director SHOULD иметь быстрый доступ к ним.

---

# 52. Progressive Disclosure

Основной Journey не должен быть перегружен всеми Projects.

Используется модель:

```text
Selected Projects
→ View All
```

---

# 53. Journey Stage CD-08 — Questionnaire Decision

**Time:** примерно `02:30–03:15`

Casting Director переходит к структурированной профессиональной анкете.

---

# 54. Primary Questionnaire

Первым должен быть предложен:

```text
Primary Prepared Questionnaire
```

Это рекомендуемая администратором профессиональная версия.

---

# 55. Questionnaire Page

Минимальная структура:

```text
Актёрская анкета

Рекомендуемая версия

[ Открыть ]
[ Скачать PDF ]

Нужен другой состав данных?

[ Собрать свою анкету ]
```

---

# 56. Prepared Questionnaire Path

Если содержание подходит:

```text
Questionnaire
→ Download Picker
→ Language
→ PDF
```

---

# 57. Download Picker

Если доступно несколько подготовленных вариантов:

- Compact;
- Extended;
- Casting-specific;

пользователь выбирает нужный.

---

# 58. Questionnaire Trust Signals

Prepared Questionnaire SHOULD показывать:

- Actress Identity;
- generation/publication context;
- актуальный official profile link;
- clickable professional links;
- QR where configured.

---

# 59. Journey Stage CD-09 — Questionnaire Customization Decision

Casting Director может решить:

```text
Prepared Questionnaire is sufficient
```

или:

```text
I need another data set
```

---

# 60. Когда нужен Public Builder

Типовые причины:

### Scenario A

Prepared Questionnaire слишком подробная.

### Scenario B

Не нужен Training.

### Scenario C

Нужен конкретный Project.

### Scenario D

Нужен конкретный Skill.

### Scenario E

Нужен Emotional Grid.

### Scenario F

Нужен краткий PDF для передачи режиссёру.

---

# 61. Public Builder Entry

Рекомендуемый CTA:

```text
Настроить эту версию под свой кастинг
```

или:

```text
Собрать свою анкету
```

---

# 62. Public Builder Starting Point

Не пустой документ.

Система SHOULD предложить:

```text
Quick
Standard
Extended
Casting
```

или использовать текущую Prepared Questionnaire как starting configuration.

---

# 63. Ideal Casting Director Builder Path

```text
Primary Questionnaire
 ↓
Customize
 ↓
Current Structure Loaded
 ↓
Disable Training
 ↓
Add Project X
 ↓
Keep Languages
 ↓
Keep Emotional Grid
 ↓
Preview
 ↓
Generate PDF
```

---

# 64. Builder Content Selection

Casting Director MAY выбирать разрешённые:

- Projects;
- Skills;
- Languages;
- Training;
- Portfolio Photos;
- Emotional Grid;
- Video;
- Audio;
- Links;
- Contacts.

---

# 65. Builder Data Boundary

Casting Director MUST NOT видеть:

- private Contact;
- draft Project;
- private Casting;
- admin Notes;
- unpublished Media;
- hidden Training.

---

# 66. Server-Side Authority

Даже если пользователь вручную изменяет request:

```text
selectedProjectId=private-id
```

сервер MUST отклонить такой item.

---

# 67. Builder UX Principle

Casting Director выбирает:

```text
CONTENT
```

не:

```text
DOCUMENT DESIGN
```

Он не должен настраивать:

- font;
- margin;
- columns;
- page break;
- typography.

---

# 68. Builder Preview

Перед PDF должен быть Preview, чтобы пользователь мог проверить:

- состав;
- порядок;
- выбранные элементы.

---

# 69. Builder Time Target

Для Casting Director типовой customization SHOULD занимать:

```text
30–90 секунд
```

после открытия template.

---

# 70. Custom PDF

PDF должен быть:

- professional;
- share-ready;
- readable;
- current at generation time;
- based on revalidated data.

---

# 71. Custom PDF Metadata

Должен включать минимум:

- Actress Identity;
- Generated Date;
- Official Profile Link.

MAY включать:

- Casting Name;
- Role;
- QR.

---

# 72. Journey Stage CD-10 — Professional Action

**Time:** примерно `04:00–05:00`

После оценки и получения документа пользователь готов выполнить следующее действие.

---

# 73. Primary Professional Actions

```text
Invite to Casting
Request Materials
Offer Role
Contact
```

Для этой Persona основной:

```text
Invite to Casting
```

---

# 74. Casting CTA Context

При открытии формы система MAY передать безопасный context:

- source page;
- Questionnaire type;
- Public Builder session reference where appropriate;
- campaign attribution.

---

# 75. Casting Invitation Form

Форма SHOULD быть профессионально краткой.

Минимально:

- Name;
- Organization;
- Subject/Project;
- Message;
- Contact;
- consent.

Optional:

- Role;
- Deadline;
- Attachments.

---

# 76. Form Friction Principle

Нельзя требовать сведения, которые не нужны для первого профессионального контакта.

---

# 77. Successful Submission

После submit:

```text
Feedback persisted
→ reference number
→ success state
```

Внешнее notification отправляется после сохранения.

---

# 78. Reference Number

Casting Director SHOULD получить:

```text
Обращение №...
```

Это повышает доверие и позволяет ссылаться на сообщение.

---

# 79. Internal Handoff

После отправки:

```text
Casting Invitation
→ Feedback
```

Затем внутри Admin:

```text
Feedback
→ Human Review
→ Casting
```

---

# 80. No Automatic Casting Creation

Отправка формы MUST NOT автоматически создавать qualified Casting.

Необходимо решение Admin.

---

# 81. No Automatic Professional Response

AI MUST NOT автоматически отвечать Casting Director.

BB Assistant MAY подготовить Draft Reply для Admin.

---

# 82. Journey Completion Conditions

Основной Journey считается завершённым, если пользователь:

### Option A

Отправил Casting Invitation.

### Option B

Отправил Request Materials.

### Option C

Получил PDF для дальнейшего внутреннего рассмотрения.

---

# 83. Happy Path CJM-CD-001

```text
Recommendation Link
 ↓
Profile
 ↓
Quick Facts
 ↓
Full Body / Close-Up
 ↓
Video Intro
 ↓
Emotional Grid
 ↓
Selected Project
 ↓
Primary Questionnaire
 ↓
Customize
 ↓
Custom PDF
 ↓
Invite to Casting
 ↓
Reference Number
```

---

# 84. Short Happy Path CJM-CD-002

```text
Direct Profile
 ↓
Quick View
 ↓
Video
 ↓
Invite to Casting
```

Это полностью корректный Journey.

---

# 85. Questionnaire-First Path CJM-CD-003

```text
Shared Questionnaire Link
 ↓
Questionnaire
 ↓
Verify Profile
 ↓
Download PDF
 ↓
Casting CTA
```

---

# 86. Search-Led Path CJM-CD-004

```text
Search Engine
 ↓
Project / Profile
 ↓
Search Skill
 ↓
Quick View
 ↓
Questionnaire
 ↓
Contact
```

---

# 87. QR Path CJM-CD-005

```text
Printed / Shared PDF
 ↓
Scan QR
 ↓
Current Profile
 ↓
Verify Updated Information
 ↓
Casting CTA
```

---

# 88. Mobile Path CJM-CD-M01

На Smartphone:

```text
Entry
 ↓
Compact Hero
 ↓
Quick Facts
 ↓
Swipe / Filter Portfolio
 ↓
Video
 ↓
Emotional Grid
 ↓
Questionnaire
 ↓
Contact
```

---

# 89. Mobile Sticky Actions

На mobile MAY использоваться компактная sticky action bar:

```text
Видео
Анкета
Связаться
```

Она не должна закрывать существенный контент.

---

# 90. Builder Mobile Behaviour

На mobile Public Builder SHOULD использовать последовательную структуру:

```text
Template
 ↓
Blocks
 ↓
Item Selection
 ↓
Preview
 ↓
Generate
```

а не desktop split-screen.

---

# 91. Failure Path CD-F01 — Video unavailable

Система:

- показывает понятную ошибку;
- сохраняет остальные действия;
- не блокирует Questionnaire;
- SHOULD уведомить Admin/Virtual Operator.

---

# 92. Failure Path CD-F02 — No Emotional Grid

Если Emotional Portfolio существует, но Grid не опубликован:

- не показывать broken placeholder;
- MAY показать full Emotional Portfolio;
- Journey продолжается.

---

# 93. Failure Path CD-F03 — PDF generation error

Если Prepared PDF generation fails:

- HTML Questionnaire может оставаться доступной;
- показывается retry;
- другие Prepared Questionnaire не блокируются;
- Public Builder MAY оставаться доступным, если его renderer независим.

---

# 94. Failure Path CD-F04 — Public Builder item revoked

Пользователь выбрал Project, но перед generation permission изменилась.

System MUST:

1. revalidate;
2. исключить optional item или остановить generation согласно policy;
3. показать сообщение.

Пример:

```text
Один из выбранных материалов больше недоступен.
Проверьте состав анкеты.
```

---

# 95. Failure Path CD-F05 — Builder Session Expired

Пользователь получает:

```text
Сессия конструктора истекла.
Начните новую анкету.
```

Скрытые данные не восстанавливаются из неподтверждённого client payload.

---

# 96. Failure Path CD-F06 — Feedback validation

Если обязательное поле не заполнено:

- значения сохраняются;
- error связан с field;
- можно исправить без повторного ввода всей формы.

---

# 97. Failure Path CD-F07 — WhatsApp unavailable

Пользователь всё равно получает successful Feedback submission, если запись сохранена.

Admin получает Internal Notification.

---

# 98. Failure Path CD-F08 — Contact unavailable

Если один публичный канал временно недоступен, система SHOULD показывать другие разрешённые способы связи.

---

# 99. Friction Map

Основные точки риска:

```text
FRC-CD-01 Slow recognition
FRC-CD-02 Hidden professional facts
FRC-CD-03 Unstructured portfolio
FRC-CD-04 Deep video navigation
FRC-CD-05 Emotional overload
FRC-CD-06 Project overload
FRC-CD-07 One-size-fits-all PDF
FRC-CD-08 Hidden contact
FRC-CD-09 Mobile degradation
FRC-CD-10 Broken media
```

---

# 100. Friction Reduction Strategy

| Friction | Решение |
|---|---|
| Slow recognition | Hero + Quick Facts |
| Hidden facts | Structured Data |
| Portfolio overload | Professional Filters |
| Video navigation | Primary Video CTA |
| Emotional overload | Emotional Grid |
| Project overload | Featured Projects |
| Bad PDF fit | Public Builder |
| Hidden contact | Contextual CTA |
| Mobile degradation | Mobile-first critical flow |
| Broken media | Validation + Virtual Operator |

---

# 101. Trust Map

Основные Trust Signals:

```text
TRUST-CD-01 Real professional photos
TRUST-CD-02 Shooting Date
TRUST-CD-03 Consistent structured facts
TRUST-CD-04 Working Video
TRUST-CD-05 Official Questionnaire
TRUST-CD-06 Generated Date
TRUST-CD-07 Official Profile URL
TRUST-CD-08 Clear Professional Contacts
TRUST-CD-09 Human-reviewed information
```

---

# 102. Trust Risk — Inconsistent Facts

Если Profile и PDF показывают разные текущие facts без snapshot explanation, доверие снижается.

Поэтому:

- current views используют SSOT;
- historical immutable PDF имеет Generated Date/Snapshot context.

---

# 103. Trust Risk — AI Appearance

Профессиональный visual representation MUST NOT вводить пользователя в заблуждение генеративно изменённой внешностью.

---

# 104. Content Priority for Casting Director

## P0

```text
Name
Close-Up
Full Body
Professional Identity
Location
Languages
Key Skills
Video
Questionnaire
Casting CTA
```

## P1

```text
Appearance
Emotional Grid
Selected Projects
Showreel
```

## P2

```text
Training
Achievements
Role Details
Additional Media
```

## P3

```text
Archive
Blog
Extended content
```

---

# 105. Information Density

Casting Director интерфейс должен быть:

```text
Structured
Scannable
Evidence-rich
Not cluttered
```

---

# 106. Biography Role

Biography MAY создавать профессиональный narrative context.

Но MUST NOT быть обязательным для:

- Languages;
- Skills;
- Location;
- Project discovery;
- Contact.

---

# 107. Marketing Attribution

Journey MAY фиксировать:

```text
source
campaign
landing page
referrer
UTM
```

где применимо.

---

# 108. Attribution Privacy

Необходимо избегать хранения ненужной PII внешнего пользователя.

---

# 109. Conversion Events

Рекомендуемые:

```text
casting_director_profile_viewed
quick_view_opened
portfolio_opened
portfolio_filter_used
video_started
emotional_grid_opened
project_opened
questionnaire_viewed
questionnaire_downloaded
public_builder_opened
public_builder_pdf_generated
casting_invitation_started
casting_invitation_submitted
request_materials_submitted
```

Конкретная naming convention определяется Analytics Specification.

---

# 110. Primary Conversion

```text
Casting Invitation Submitted
```

---

# 111. Secondary Conversions

```text
Questionnaire Downloaded
Custom PDF Generated
Request Materials Submitted
```

---

# 112. Journey KPI — Time to Qualification

Измеряет время от entry до получения основных professional facts.

---

# 113. Journey KPI — Time to Questionnaire

Измеряет путь до открытия профессиональной анкеты.

---

# 114. Journey KPI — Questionnaire to Action

Показывает долю пользователей, которые после Questionnaire:

- Contact;
- Invite;
- Request Materials.

---

# 115. Journey KPI — Builder Usage

Показывает:

- сколько Casting Directors открывают Builder;
- какие Templates;
- какие Blocks часто добавляют/удаляют.

---

# 116. Builder Analytics Interpretation

Если пользователи часто:

```text
Prepared Questionnaire
→ remove Training
```

это MAY стать рекомендацией пересмотреть Default Template.

Но система не меняет template автоматически.

---

# 117. Virtual Operator Interaction

Virtual Operator должен обнаруживать проблемы, особенно критичные для Journey:

- broken Video;
- missing Primary Full Body;
- missing Primary Close-Up;
- stale Questionnaire;
- broken PDF link;
- missing Shooting Date;
- invalid Contact.

---

# 118. Exception Priority

Проблемы, блокирующие Casting Director Journey, SHOULD иметь высокий административный priority.

Например:

```text
Video Intro unavailable
```

важнее, чем:

```text
missing optional caption in archived Training
```

---

# 119. Relevant Product Insights

Из 6-3-5:

```text
INS-CD-01 Casting Quick View
INS-CD-02 Search Inside Profile
INS-CD-03 Casting-Specific CTA
```

Дополнительно этот Journey подтверждает:

```text
INS-CA-01 Structured Quick Facts
INS-CA-02 Portfolio Filters
INS-CA-03 Compact PDF
INS-CP-01 Mobile-First
INS-DIR-01 Emotional Range
INS-DIR-02 Progressive Projects
INS-INT-03 Localized PDF
```

---

# 120. Relevant Business Rules

Минимально:

```text
BR-CJM-001
BR-CJM-002
BR-CJM-003
BR-CJM-004
BR-CJM-005
BR-CJM-006
BR-CJM-009
BR-CJM-010

BR-POR-005
BR-POR-006
BR-POR-012

BR-QST-001–020

BR-PQB-001–030

BR-FDB-001–015

BR-CON-004–011
```

---

# 121. Relevant Product Principles

Ключевые:

```text
PP-004 Casting Professional Drives Public UX
PP-005 Minimum Actions
PP-006 Progressive Disclosure
PP-008 No Dead Ends
PP-016 Admin-Curated + User-Configurable
PP-017 Start From a Useful Default
PP-018 Content Selection, Not Document Design
PP-020 Prepared Questionnaire Remains Recommendation
PP-021 Privacy by Projection
PP-045 Server Authority
PP-046 No Mandatory Registration
PP-047 Share-Ready Documents
PP-053 Five-Minute Journey
PP-054 Quick Decision in Seconds
PP-055 Contextual CTA
```

---

# 122. Future UX Flows Required

Из этого Journey должны быть созданы минимум:

```text
UF-PUB-CD-001 Open Professional Profile
UF-PUB-CD-002 Inspect Quick View
UF-PUB-CD-003 Search Professional Fact
UF-PUB-CD-004 Filter Portfolio
UF-PUB-CD-005 Play Video Intro
UF-PUB-CD-006 Open Emotional Range
UF-PUB-CD-007 Review Selected Project
UF-QST-CD-001 Open Prepared Questionnaire
UF-QST-CD-002 Download Questionnaire
UF-PQB-CD-001 Customize Questionnaire
UF-PQB-CD-002 Generate Custom PDF
UF-FDB-CD-001 Invite to Casting
UF-FDB-CD-002 Request Materials
```

---

# 123. Functional Requirement Areas

Journey требует будущих FR в областях:

```text
FR-PROFILE
FR-QUICKVIEW
FR-SEARCH
FR-PORTFOLIO
FR-VIDEO
FR-EMOTIONAL
FR-PROJECT
FR-QUESTIONNAIRE
FR-PQB
FR-CONTACT
FR-FEEDBACK
FR-ANALYTICS
```

---

# 124. E2E Test — E2E-CJM-CD-001

Основной тест:

```text
Given
a public professional profile is fully ready

When
Casting Director opens the profile

Then
they can:
- identify actress
- inspect Full Body and Close-Up
- find Language
- start Video
- open Emotional Grid
- review Selected Project
- open Primary Questionnaire
- customize it
- generate PDF
- submit Casting Invitation
```

---

# 125. E2E Timing Expectation

Автоматический тест не обязан эмулировать реальные пять минут ожидания.

Но UX Acceptance Test SHOULD подтверждать, что путь не содержит unnecessary steps, делающих пятиминутный сценарий нереалистичным.

---

# 126. E2E Privacy Test

Попытка выбрать запрещённый Project ID в Public Builder MUST завершиться отказом.

---

# 127. E2E Contact Privacy Test

Private Contact не должен присутствовать:

- в DOM;
- network payload;
- generated PDF;
- search result.

---

# 128. E2E Failure Test — WhatsApp

```text
Given
external WhatsApp delivery fails

When
Casting Director submits Invitation

Then
Feedback remains stored
and user sees successful submission
and Admin receives internal alert
```

---

# 129. E2E Deep Link Test

Opening:

```text
/projects/{slug}
```

directly MUST still expose enough identity/navigation/CTA to continue the professional Journey.

---

# 130. Journey Score Target

Целевой design score:

| Критерий | Target |
|---|---:|
| Recognition | 2 |
| Findability | 2 |
| Evidence | 2 |
| Questionnaire | 2 |
| Action | 2 |

Итого:

```text
10/10
```

Minimum acceptable before production acceptance:

```text
9/10
```

---

# 131. Journey Failure Conditions

Journey MUST считаться требующим redesign, если:

1. Primary Questionnaire невозможно найти быстро.
2. Full Body требует поиска по общему Media Archive.
3. Language находится только в Biography.
4. Video скрыто глубоко.
5. Public Builder начинает с пустого документа без причины.
6. Contact requires account registration.
7. Mobile Journey теряет ключевую функциональность.
8. Private Data попадает в Public Builder.
9. Casting Invitation может потеряться при notification failure.
10. Direct deep link становится тупиковой страницей.

---

# 132. Design Decision Principle

Если необходимо выбирать между:

```text
больше контента на первом экране
```

и:

```text
быстрее профессиональное решение
```

предпочтение отдаётся второму, при условии доступности дополнительного content через Progressive Disclosure.

---

# 133. Journey Optimization Principle

Оптимизация должна сокращать:

```text
Time to Understand
Time to Evidence
Time to Questionnaire
Time to Action
```

а не искусственно увеличивать session duration.

---

# 134. Journey Marketing Principle

Если рекламная или referral-ссылка уже содержит профессиональный context, Entry Point MAY быть более специализированным.

Например:

```text
Casting campaign
→ Questionnaire / Quick View
```

вместо обязательного:

```text
→ Home
```

---

# 135. Journey Completion Does Not Require Full Site Exploration

Successful Casting Director MAY никогда не открыть:

- Blog;
- all Training;
- all Achievements;
- full Project archive.

Это не является UX failure.

---

# 136. Long-Form Exploration Remains Supported

Если Casting Director хочет изучить профиль глубоко, все подробные материалы остаются доступны через secondary layers.

---

# 137. Main Professional Loop

```text
WHO?
 ↓
FIT?
 ↓
PROVE IT
 ↓
GIVE ME THE RIGHT DOCUMENT
 ↓
LET ME ACT
```

Это основной mental model Persona.

---

# 138. Internal Product Response

Система должна отвечать на этот mental model:

```text
WHO?
→ Professional Identity

FIT?
→ Structured Facts + Portfolio

PROVE IT
→ Video + Emotional + Projects

DOCUMENT
→ Prepared Questionnaire + Builder

ACT
→ Contextual CTA
```

---

# 139. Definition of Journey Compliance

Public UX соответствует `CJM-CD-001`, если Casting Director:

1. узнаёт Actress Profile за секунды;
2. видит основные structured facts;
3. легко находит Full Body/Close-Up;
4. запускает Video без глубокого поиска;
5. получает быстрый Emotional Range;
6. видит релевантный опыт;
7. быстро находит Prepared Questionnaire;
8. может адаптировать документ;
9. не получает Private Data;
10. может выполнить профессиональный CTA;
11. не обязан регистрироваться;
12. весь типовой путь реалистично укладывается в пять минут.

---

# 140. Итоговая схема Journey

```text
CASTING DIRECTOR

External Link / Search / Referral / QR
                ↓
        PROFESSIONAL IDENTITY
                ↓
          CASTING QUICK VIEW
                ↓
    ┌───────────┼────────────┐
    │           │            │
Portfolio     Video     Emotional Grid
    │           │            │
    └───────────┼────────────┘
                ↓
        SELECTED EXPERIENCE
                ↓
      PRIMARY QUESTIONNAIRE
                ↓
        Is it sufficient?
           /          \
         YES           NO
          │             │
      Download      Public Builder
          │             │
          └──────┬──────┘
                 ↓
        PROFESSIONAL ACTION
                 ↓
    Invite to Casting / Request Materials
                 ↓
              FEEDBACK
                 ↓
            ADMIN REVIEW
                 ↓
              CASTING
```

---

# 141. Финальный принцип

> **Путь кастинг-директора должен позволять перейти от первого впечатления к профессионально обоснованному действию без необходимости самостоятельно собирать профиль актрисы из разрозненных материалов. Система должна последовательно отвечать на пять вопросов: кто актриса, соответствует ли она задаче, какие есть доказательства, какой документ нужен и как выполнить следующий кастинговый шаг.**