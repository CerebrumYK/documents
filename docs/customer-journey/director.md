# DIRECTOR / PRODUCER JOURNEY

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Подробный Customer Journey режиссёра / продюсера

**Целевой файл:** `docs/customer-journey/director.md`  
**Документ:** DOC-026  
**Статус:** ✅ Completed  
**Тип:** Customer Journey

**Persona:** `PERSONA-DIR — Director / Producer`  
**Рабочее имя Persona:** Алексей

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
- `docs/customer-journey/casting-director.md`
- `docs/customer-journey/casting-assistant.md`
- `docs/customer-journey/commercial-producer.md`

---

# 1. Назначение документа

Настоящий документ описывает Customer Journey режиссёра / продюсера, который уже проявил интерес к кандидату и хочет понять не только формальное соответствие, но и:

- актёрский диапазон;
- эмоциональную выразительность;
- качество присутствия в кадре;
- характер предыдущих ролей;
- профессиональный опыт;
- пригодность для конкретного художественного контекста.

Документ является нормативным источником для проектирования:

- Video Intro;
- Showreel;
- Emotional Portfolio;
- Emotional Grid;
- Featured Projects;
- Role Detail;
- Project Media;
- Request Materials CTA;
- Questionnaire;
- Public Builder;
- Deep Links;
- E2E Journey Tests.

---

# 2. Journey ID

Основной Journey:

```text
CJM-DIR-001
```

Название:

```text
Director / Producer — Creative Evaluation to Materials Request / Casting Action
```

---

# 3. Persona Summary

## Persona

Director / Producer.

## Основная профессиональная задача

> Понять, обладает ли актриса необходимым творческим диапазоном и способна ли органично существовать в контексте конкретного проекта или роли.

---

# 4. Decision Authority

Persona может иметь высокий уровень влияния на конечный результат.

Она может:

- рекомендовать кандидата;
- запросить дополнительные пробы;
- пригласить на очный/онлайн casting;
- запросить self-tape;
- определить, какие материалы нужны дополнительно;
- участвовать в финальном выборе.

---

# 5. Основное отличие Persona

В отличие от Casting Assistant и Casting Director, которые чаще начинают с проверки критериев, режиссёр может уже получить кандидата после первичного отбора.

Поэтому Journey начинается не с:

```text
Does she meet the formal criteria?
```

а чаще с:

```text
What can she bring to the role?
```

---

# 6. Профессиональный контекст

Типичная ситуация:

- кандидат уже прошёл первичный screening;
- ссылка пришла от Casting Director;
- режиссёр хочет понять актёрскую природу;
- формальные данные уже частично известны;
- пользователь готов потратить больше времени на материалы, если быстро увидит профессиональную ценность.

---

# 7. Primary Device

Наиболее вероятны:

- Notebook;
- Desktop;
- Tablet.

Mobile должен оставаться полноценным fallback, но это не основной рабочий сценарий.

---

# 8. Entry Sources

```text
DIR-ENTRY-01 Casting Director Recommendation
DIR-ENTRY-02 Shared Project Link
DIR-ENTRY-03 Shared Showreel
DIR-ENTRY-04 Shared Questionnaire
DIR-ENTRY-05 Messenger
DIR-ENTRY-06 Direct Profile
DIR-ENTRY-07 Emotional Portfolio Link
DIR-ENTRY-08 QR
```

---

# 9. Primary Mental Model

Режиссёр проходит примерно такую последовательность:

```text
Как она существует в кадре?
 ↓
Какой у неё эмоциональный диапазон?
 ↓
Что она уже играла?
 ↓
Как выглядит в конкретных ролях?
 ↓
Есть ли дополнительные материалы?
 ↓
Стоит ли приглашать дальше?
```

---

# 10. Desired Outcome

Основной результат:

```text
Creative Interest Confirmed
```

и затем одно из действий:

```text
Request Materials
Invite to Casting
Request Self-Tape
Open Casting Questionnaire
```

---

# 11. Business Outcome

Primary:

```text
Qualified Creative Interest
```

Secondary:

```text
Request Materials
Casting Invitation
Questionnaire Download
Project Deep Dive
Showreel Engagement
```

---

# 12. Time Budget

Первичная оценка должна оставаться быстрой:

```text
00:00–00:20
Professional Recognition

00:20–01:30
Video / Showreel

01:30–02:30
Emotional Range

02:30–03:45
Projects / Roles

03:45–04:30
Additional Evidence

04:30–05:00
Professional Action
```

Пользователь MAY продолжить просмотр после пяти минут.

Пятиминутное правило означает, что до этого момента он уже должен иметь достаточно данных для следующего решения.

---

# 13. Stage DIR-01 — Entry

Режиссёр часто приходит по deep link.

Поэтому страница должна сразу сохранять:

- Actress Identity;
- professional navigation;
- Video access;
- Questionnaire;
- Professional CTA.

---

# 14. Deep Link Principle

Если вход:

```text
/projects/{slug}
```

пользователь не должен сначала идти:

```text
Project
→ Home
→ Profile
→ find video
```

Project Detail должен дать естественное продолжение Journey.

---

# 15. Stage DIR-02 — Professional Recognition

На первом экране достаточно:

- Name;
- Primary Photo;
- Professional Identity;
- brief relevant facts;
- prominent Video / Showreel access.

---

# 16. Recognition Priority

Для Persona важнее:

```text
Who + What kind of performer?
```

чем полный structured résumé на первом экране.

---

# 17. Stage DIR-03 — Video Intro

Video Intro должен быть быстро доступен.

Его назначение:

- увидеть естественную речь;
- оценить поведение перед камерой;
- получить базовое ощущение личности и presence.

---

# 18. Stage DIR-04 — Showreel

Если интерес сохраняется:

```text
Video Intro
→ Showreel
```

Showreel должен быть ясно отделён от Video Intro.

---

# 19. Showreel Purpose

Showreel помогает оценить:

- работу внутри сцены;
- реакцию;
- ритм;
- взаимодействие;
- драматический диапазон;
- профессиональный опыт.

---

# 20. Video Taxonomy

Режиссёр не должен видеть одну неструктурированную ленту видео.

Минимально полезно различать:

```text
Video Intro
Showreel
Project Scene
Role Scene
Other Professional Video
```

---

# 21. Video Context

Project/Role video SHOULD быть связан с соответствующей сущностью.

Например:

```text
Project
 └── Role
      └── Scene Video
```

а не существовать только в общей Media Library.

---

# 22. Stage DIR-05 — Emotional Range

После видео пользователь оценивает диапазон выражений и состояний.

Первый слой:

```text
Emotional Grid
```

---

# 23. Emotional Grid Purpose

Grid даёт быстрый ответ:

> «Есть ли визуально выраженный эмоциональный диапазон, который стоит изучить глубже?»

---

# 24. Full Emotional Portfolio

При интересе:

```text
Emotional Grid
→ Emotional Portfolio Detail
```

---

# 25. Emotional Portfolio Structure

Режиссёр MAY видеть:

- Shooting Date;
- selected emotion labels;
- individual photos;
- one or more approved Emotional Grids.

---

# 26. Authenticity Requirement

Для creative evaluation критично, чтобы:

- эмоции были реальными;
- лицо не модифицировалось;
- AI не дорисовывал expression;
- crop не искажал содержание.

---

# 27. Stage DIR-06 — Featured Projects

Пользователь переходит к Selected / Featured Projects.

Необходимо сначала показать ограниченный набор наиболее профессионально значимых работ.

---

# 28. Why Featured First

Длинный Project Archive создаёт noise.

Режиссёр обычно хочет увидеть:

```text
best evidence
```

до:

```text
complete history
```

---

# 29. Project Card Information

На preview достаточно:

- Project Title;
- Type;
- Role;
- key media;
- date/year where relevant.

---

# 30. Stage DIR-07 — Project Detail

Project Detail должен позволять быстро понять:

- что это за Project;
- какую Role исполняла актриса;
- что именно можно посмотреть;
- есть ли Role-specific media.

---

# 31. Role as First-Class Evidence

Role не должна быть просто строкой внутри Project description.

Она должна быть структурированной сущностью, если Project имеет актёрскую роль.

---

# 32. Role Detail Information

Может включать:

- Character Name;
- Role Type;
- description;
- role photos;
- scene video;
- performance context.

---

# 33. Role-Specific Photos

Existing role-specific photo capability является особенно важным для этой Persona.

Пользователь может хотеть увидеть:

```text
как актриса выглядит именно в образе
```

---

# 34. Role Photo Hierarchy

Project Detail MAY показывать:

```text
2 featured role images
→ View role gallery
```

вместо полного набора сразу.

---

# 35. Home “В образе”

Публичная carousel-функция role imagery может быть полезна для Director Journey как быстрый preview разнообразия образов.

Она остаётся presentation layer.

---

# 36. Stage DIR-08 — Experience Context

После визуального материала Persona MAY проверить:

- другие Roles;
- Training;
- Achievements;
- relevant Skills;
- Languages.

---

# 37. Training Priority

Training не должен появляться раньше evidence, если не является непосредственно релевантным роли.

Пример:

```text
Specific acting method training
```

может быть полезен после просмотра опыта.

---

# 38. Achievements

Achievements MAY усиливать professional credibility.

Но:

```text
Award
```

не заменяет actual acting evidence.

---

# 39. Skills

Если роль требует конкретной способности:

- вокал;
- танец;
- вождение;
- спорт;

Structured Skill становится high priority.

---

# 40. Search in Director Journey

Public Search полезен для ответа на конкретный creative requirement.

Например:

```text
theatre
singing
dance
English
```

---

# 41. Stage DIR-09 — Request More Evidence

После изучения опубликованных данных режиссёр может решить:

> «Мне нужно увидеть ещё что-то конкретное.»

Это важный самостоятельный conversion point.

---

# 42. Request Materials CTA

Primary CTA для этого этапа:

```text
Запросить дополнительные материалы
```

---

# 43. Why Request Materials Is Separate

Запрос дополнительных материалов отличается от:

```text
Связаться
```

и от:

```text
Пригласить на кастинг
```

Он означает конкретное профессиональное намерение:

```text
интерес есть,
но нужно больше evidence
```

---

# 44. Material Request Types

Форма MAY позволять указать:

- Additional Photo;
- Scene;
- Self-Tape;
- Voice Sample;
- Specific Role Material;
- Other.

Точная taxonomy определяется Feedback module.

---

# 45. Contextual Request

Если CTA вызывается из Project/Role, system MAY безопасно передать context:

```text
source_project
source_role
```

чтобы пользователь не повторял очевидную информацию.

---

# 46. Stage DIR-10 — Questionnaire

Если production process требует structured profile:

```text
Prepared Questionnaire
```

доступна без возврата на Home.

---

# 47. Questionnaire Type

Для Director чаще могут быть полезны:

```text
Standard
Extended
Casting
```

Compact остаётся доступным, но может быть недостаточно подробным.

---

# 48. Public Builder Use Case

Режиссёр может собрать document, где акцент на:

- selected Projects;
- Roles;
- Emotional Grid;
- Video;
- relevant Skills;
- Training.

---

# 49. Director Builder Example

```text
Standard Questionnaire
 ↓
Customize
 ↓
Keep Full Body / Close-Up
 ↓
Add Emotional Grid
 ↓
Add Project A
 ↓
Add Project B
 ↓
Add Acting Training
 ↓
Remove unrelated Skills
 ↓
Generate PDF
```

---

# 50. Builder Principle

Даже для creative Persona Builder остаётся content selector, а не layout editor.

---

# 51. Stage DIR-11 — Casting Action

Если решение достаточно уверенное:

```text
Invite to Casting
```

или:

```text
Request Self-Tape
```

---

# 52. Request Self-Tape

На продуктовом уровне Self-Tape является Opportunity Stage / professional request.

Форма MAY быть частью Feedback/Casting workflow.

Точная implementation определяется Castings/Feedback modules.

---

# 53. No Automatic Professional Commitment

Ни Request Self-Tape, ни Offer, ни Casting CTA не означает автоматическое согласие актрисы.

---

# 54. Main Happy Path CJM-DIR-001

```text
Casting Director Recommendation
 ↓
Profile / Project
 ↓
Video Intro
 ↓
Showreel
 ↓
Emotional Grid
 ↓
Featured Project
 ↓
Role Media
 ↓
Request Materials
```

---

# 55. Casting Path CJM-DIR-002

```text
Recommendation
 ↓
Video
 ↓
Emotional Range
 ↓
Selected Projects
 ↓
Casting Questionnaire
 ↓
Invite to Casting
```

---

# 56. Project-First Path CJM-DIR-003

```text
Shared Project Link
 ↓
Role
 ↓
Role Photos
 ↓
Scene Video
 ↓
Other Selected Projects
 ↓
Questionnaire
 ↓
Request Materials
```

---

# 57. Emotional-First Path CJM-DIR-004

```text
Shared Emotional Portfolio
 ↓
Emotional Grid
 ↓
Full Emotional Portfolio
 ↓
Video
 ↓
Projects
 ↓
Casting CTA
```

---

# 58. Showreel-First Path CJM-DIR-005

```text
Shared Showreel
 ↓
Video
 ↓
Profile Context
 ↓
Relevant Role
 ↓
Questionnaire
 ↓
Request Self-Tape
```

---

# 59. Mobile/Tablet Path CJM-DIR-M01

Tablet/mobile MUST support:

```text
Video
→ Emotional Grid
→ Project
→ Role Media
→ Request Materials
```

without desktop-only controls.

---

# 60. Friction DIR-FRC-01 — CV-First Profile

Проблема:

пользователь получает длинный résumé до creative evidence.

Resolution:

```text
Video / Emotional / Featured Projects first
```

в relevant Journey.

---

# 61. Friction DIR-FRC-02 — Flat Project List

Проблема:

все Projects имеют одинаковый priority.

Resolution:

```text
Featured Projects
→ View All
```

---

# 62. Friction DIR-FRC-03 — Role Hidden in Project Text

Resolution:

Structured Role.

---

# 63. Friction DIR-FRC-04 — Media Without Context

Пользователь открывает gallery, но не понимает, из какой Role материал.

Resolution:

explicit Project/Role relations.

---

# 64. Friction DIR-FRC-05 — Video Chaos

Все Video находятся одной лентой.

Resolution:

Video taxonomy + primary Video Intro + Showreel + project context.

---

# 65. Friction DIR-FRC-06 — Emotional Portfolio Buried

Emotional Range должен быть first-class content.

---

# 66. Friction DIR-FRC-07 — Contact Too Generic

Пользователь заинтересован, но видит только:

```text
Написать сообщение
```

Resolution:

```text
Request Materials
Invite to Casting
```

---

# 67. Friction DIR-FRC-08 — Full Archive Before Relevant Work

Использовать progressive disclosure.

---

# 68. Friction DIR-FRC-09 — Broken Scene Video

Role Detail остаётся usable, но missing evidence должно быть понятно.

Virtual Operator SHOULD detect broken resource.

---

# 69. Friction DIR-FRC-10 — Artificial Image Editing

Generative modification может подорвать доверие к creative assessment.

Запрещено.

---

# 70. Trust Signals

Для Persona особенно важны:

```text
Real Scene Evidence
Role Context
Emotional Authenticity
Video Quality
Project Structure
Consistent Credits
Real Photography
Human-Reviewed Facts
```

---

# 71. Trust Signal — Context

Фото из роли значительно полезнее, если понятно:

- Project;
- Role;
- context.

---

# 72. Trust Signal — Chronology

Если date/year известны, они помогают понимать progression профессионального опыта.

---

# 73. Trust Signal — Source Integrity

Project credits не должны создаваться AI на основании догадок.

---

# 74. Information Priority — P0

```text
Video Intro
Showreel
Emotional Grid
Selected Projects
Roles
Primary Portfolio
```

---

# 75. Information Priority — P1

```text
Role-specific Media
Skills
Languages
Questionnaire
Request Materials CTA
```

---

# 76. Information Priority — P2

```text
Training
Achievements
Additional Video
Full Emotional Portfolio
```

---

# 77. Information Priority — P3

```text
Archive
Blog
Secondary Training
General narrative
```

---

# 78. Creative Depth Principle

Depth должна быть доступна, но не навязываться.

Модель:

```text
Preview
→ Relevant Detail
→ Archive
```

---

# 79. Project Ordering

Project ordering MAY учитывать curated editorial priority.

Он не должен автоматически определяться только:

- количеством views;
- AI score;
- recency;

без human-controlled presentation logic.

---

# 80. Featured Project Selection

Featured status является presentation configuration.

Он не изменяет business meaning Project.

---

# 81. Director and Public Builder

Public Builder для Persona полезен не только для сокращения документа, но и для создания **тематически релевантной подборки**.

Например:

```text
Drama Casting
→ dramatic role examples
→ emotional grid
→ relevant training
```

---

# 82. Builder Must Not Invent Context

Если User вводит:

```text
Role: Anna
```

в Casting Template, это относится только к generated session/document.

Это не создаёт новую Role в Profile.

---

# 83. Request Materials and Public Builder

Два разных механизма:

### Builder

Собирает уже опубликованные разрешённые материалы.

### Request Materials

Просит то, чего нет среди доступных public materials.

---

# 84. Request Materials Handoff

```text
Public Request
 ↓
Feedback
 ↓
Admin Review
 ↓
Possible Casting
 ↓
Response prepared separately
```

---

# 85. BB Assistant Support

После Request Materials или Casting Inquiry BB Assistant MAY помочь Admin подготовить:

- response;
- cover note;
- material description.

Draft-only.

---

# 86. Casting AI Relationship

Если Professional Inquiry превращается в Casting:

AI MAY анализировать requirements.

Это происходит уже после external Journey.

---

# 87. Attribution

Source MAY быть:

```text
casting_director_referral
shared_project
showreel
emotional_portfolio
direct
```

для aggregate analytics.

---

# 88. Analytics Events

Рекомендуемые logical events:

```text
director_profile_viewed
video_intro_started
showreel_started
emotional_grid_viewed
emotional_portfolio_opened
featured_project_opened
role_opened
role_media_viewed
questionnaire_opened
public_builder_opened
request_materials_started
request_materials_submitted
casting_invitation_started
```

---

# 89. KPI — Evidence Depth

Можно анализировать последовательность:

```text
Video
→ Emotional
→ Project
→ Role
```

как индикатор deeper professional interest.

---

# 90. KPI — Request Materials Rate

Высокий показатель может означать:

- высокий интерес;
- либо недостаток опубликованных материалов.

Необходимо интерпретировать вместе с request category.

---

# 91. KPI — Showreel to Project Conversion

Показывает, переходит ли пользователь от видео к конкретному experience evidence.

---

# 92. KPI — Role Media Engagement

Особенно полезно для оценки ценности role-specific content.

---

# 93. Analytics Must Not Select Featured Projects Automatically

Analytics MAY предложить:

```text
Project X often precedes professional inquiries
```

но не менять Featured status без human decision.

---

# 94. Virtual Operator Support

Для Director Journey высокий приоритет имеют:

- broken Showreel;
- broken Project Scene;
- missing Role relation;
- missing role photo metadata;
- stale project media derivative;
- missing Emotional Grid;
- broken Request Materials flow.

---

# 95. Virtual Operator Suggestion Example

Если Project имеет несколько media assets, но ни один не связан с Role:

Virtual Operator MAY предложить:

```text
Проверить привязку материалов к роли.
```

---

# 96. Relevant 6-3-5 Insights

Основные:

```text
INS-DIR-01 Emotional Range as First-Class Content
INS-DIR-02 Progressive Projects
INS-DIR-03 Request Materials CTA
```

Дополнительные:

```text
INS-CD-01 Casting Quick View
INS-CD-03 Casting-Specific CTA
INS-CA-01 Structured Quick Facts
INS-CP-01 Mobile-First
```

---

# 97. Relevant Business Rules

Ключевые:

```text
BR-PRJ-001
BR-PRJ-004
BR-PRJ-005
BR-PRJ-006

BR-EMO-001
BR-EMO-005
BR-GRD-005
BR-GRD-006
BR-GRD-008

BR-CJM-001
BR-CJM-004
BR-CJM-005
BR-CJM-006

BR-CTA-001
BR-CTA-002
BR-CTA-003

BR-PQB-001–030
BR-FDB-001–015
```

---

# 98. Relevant Product Principles

```text
PP-004 Casting Professional Drives Public UX
PP-005 Minimum Actions
PP-006 Progressive Disclosure
PP-008 No Dead Ends
PP-014 Domain Separation
PP-015 Presentation Is Not a New Domain
PP-029 AI Must Not Alter Actor Appearance
PP-055 Contextual CTA
PP-060 Feedback ≠ Opportunity Automatically
PP-063 Analytics Supports Decisions
PP-086 Deep Links Must Be Useful
```

---

# 99. Future User Flows

Минимально:

```text
UF-PUB-DIR-001 Open Recommended Profile
UF-PUB-DIR-002 Play Video Intro
UF-PUB-DIR-003 Play Showreel
UF-PUB-DIR-004 Open Emotional Grid
UF-PUB-DIR-005 Open Emotional Portfolio
UF-PUB-DIR-006 Open Featured Project
UF-PUB-DIR-007 Open Role
UF-PUB-DIR-008 Review Role Media
UF-QST-DIR-001 Open Questionnaire
UF-PQB-DIR-001 Build Creative Casting Questionnaire
UF-FDB-DIR-001 Request Materials
UF-FDB-DIR-002 Invite to Casting
UF-FDB-DIR-003 Request Self-Tape
```

---

# 100. Functional Requirement Areas

```text
FR-VIDEO
FR-EMOTIONAL
FR-PROJECT
FR-ROLE
FR-MEDIA
FR-QUESTIONNAIRE
FR-PQB
FR-FEEDBACK
FR-CASTING
FR-ANALYTICS
```

---

# 101. E2E-CJM-DIR-001 — Main Creative Journey

```text
Given
the profile has published professional evidence

When
Director opens a recommended link

Then
they can:
- identify actress
- start Video Intro
- start Showreel
- open Emotional Grid
- open Featured Project
- inspect Role
- inspect Role-specific media
- submit Request Materials
```

---

# 102. E2E-CJM-DIR-002 — Deep Project Entry

```text
Given
a public Project deep link

When
user opens it directly

Then
they can:
- identify actress
- understand Project and Role
- open role media
- navigate to Video / Questionnaire
- perform professional CTA
```

---

# 103. E2E-CJM-DIR-003 — Emotional Entry

Test direct Emotional Portfolio deep link preserves complete professional continuation.

---

# 104. E2E-CJM-DIR-004 — Request Materials

Submission:

- persists Feedback;
- retains relevant source context;
- returns Reference Number;
- does not auto-create Casting.

---

# 105. E2E-CJM-DIR-005 — Role Media

Role-specific media must be related to correct Profile/Project/Role and must not leak private media.

---

# 106. E2E-CJM-DIR-006 — Builder

Director can build a document with:

- selected Project;
- Role;
- Emotional Grid;
- relevant Skills;

only where items are allowed Public Builder data.

---

# 107. E2E-CJM-DIR-007 — AI Safety

No displayed professional credit or Role is created merely from AI recommendation without confirmed Master Data.

---

# 108. E2E-CJM-DIR-008 — Broken Showreel

If Showreel fails:

- Video Intro MAY remain usable;
- Projects remain accessible;
- CTA remains accessible;
- failure does not break page.

---

# 109. Journey Score Target

| Критерий | Target |
|---|---:|
| Recognition | 2 |
| Video Evidence | 2 |
| Emotional Evidence | 2 |
| Project / Role Depth | 2 |
| Professional Action | 2 |

Target:

```text
10/10
```

Minimum acceptable:

```text
9/10
```

---

# 110. Hard Failure Conditions

Journey требует redesign, если:

1. Video невозможно быстро найти.
2. Showreel и Video Intro не различимы.
3. Emotional Range отсутствует как понятный capability.
4. Featured Projects неотличимы от полного архива.
5. Role существует только внутри prose.
6. Role-specific media теряет контекст.
7. Deep Project link является dead end.
8. Request Materials отсутствует или сводится к generic Contact.
9. Private media доступно публично.
10. AI-generated credits отображаются как подтверждённый опыт.
11. core creative evidence невозможно удобно просмотреть на tablet/mobile.
12. отказ одного media provider блокирует весь Journey.

---

# 111. Director Journey ≠ Long Session Requirement

Поскольку режиссёр MAY проводить больше времени на сайте, это не означает, что UX должен намеренно увеличивать session duration.

Правильная модель:

```text
Fast Initial Value
+
Optional Deep Exploration
```

---

# 112. Evidence Before Narrative

Сначала:

```text
Video
Emotional
Role
Project
```

потом при необходимости:

```text
Biography
Long-form context
```

---

# 113. Creative Evaluation Does Not Replace Structured Data

Даже visual/creative Persona должна иметь доступ к:

- Languages;
- Skills;
- Location;
- Questionnaire.

Но они не обязаны занимать первый визуальный приоритет.

---

# 114. Project Archive Principle

Полный профессиональный архив важен как:

```text
depth layer
```

но не как first decision layer.

---

# 115. Role Context Principle

Профессиональный опыт должен быть максимально привязан к:

```text
Project
→ Role
→ Evidence
```

---

# 116. Media Reuse Principle

Один Media Asset MAY использоваться:

- в Project;
- Role;
- Portfolio;

только через явные relations.

Сам файл не должен бесконтрольно копироваться.

---

# 117. Creative Request Principle

Request Materials является признаком:

```text
qualified interest
```

и должен учитываться в conversion analytics как значимое действие.

---

# 118. Self-Tape Principle

Self-Tape request относится к casting workflow и не должен автоматически создавать подтверждённый Self-Tape asset без последующего действия Admin/Profile Owner.

---

# 119. Public Builder Principle for Director

Builder должен позволять не просто «короткий PDF», а **содержательно релевантный PDF**.

То есть пользователь может выбрать те professional evidence, которые соответствуют его проекту.

---

# 120. Share-Ready Principle

Режиссёр может переслать:

- Project URL;
- Questionnaire;
- PDF;

другому producer/decision maker.

Любой такой asset должен сохранять identity и официальный context.

---

# 121. QR Principle

QR в PDF MAY использоваться для:

- Official Profile;
- Video;
- Emotional Portfolio;
- other approved public links.

QR не должен вести на private Role Media.

---

# 122. Mental Model

```text
SHOW ME THE ACTOR
 ↓
SHOW ME THE RANGE
 ↓
SHOW ME THE WORK
 ↓
SHOW ME MORE IF NEEDED
 ↓
LET ME MOVE FORWARD
```

---

# 123. Product Response

```text
SHOW ME THE ACTOR
→ Video Intro

SHOW ME THE RANGE
→ Emotional Grid / Portfolio

SHOW ME THE WORK
→ Featured Projects / Roles

SHOW ME MORE
→ Request Materials / Public Builder

MOVE FORWARD
→ Casting CTA / Self-Tape Request
```

---

# 124. Definition of Journey Compliance

Public UX соответствует `CJM-DIR-001`, если Director / Producer:

1. быстро получает creative evidence;
2. может различить Video Intro и Showreel;
3. быстро видит Emotional Range;
4. получает Featured Projects до полного архива;
5. может открыть конкретный Project и Role;
6. получает Role-specific evidence с правильным контекстом;
7. может запросить дополнительные материалы;
8. может открыть или собрать релевантную Questionnaire;
9. deep links не создают dead ends;
10. AI не подменяет подтверждённые профессиональные факты;
11. mobile/tablet сохраняют критический Journey;
12. профессионально значимое действие достижимо в пределах первых пяти минут.

---

# 125. Итоговая схема Journey

```text
DIRECTOR / PRODUCER

Casting Director / Recommendation
              ↓
       VIDEO INTRO
              ↓
          SHOWREEL
              ↓
       EMOTIONAL RANGE
              ↓
     FEATURED PROJECTS
              ↓
            ROLE
              ↓
     ROLE-SPECIFIC MEDIA
              ↓
     Need more evidence?
          /        \
        NO          YES
        │            │
        │      Request Materials
        │            │
        └──────┬─────┘
               ↓
         QUESTIONNAIRE
               ↓
          CASTING ACTION
               ↓
      Casting / Self-Tape / Offer
```

---

# 126. Финальный принцип

> **Director / Producer Journey должен позволять быстро перейти от общего профессионального впечатления к пониманию актёрского диапазона и конкретных доказательств работы: видео, эмоциям, проектам, ролям и материалам в образе. Глубина должна быть доступна по запросу, но не создавать информационную перегрузку до момента, когда у пользователя возник профессиональный интерес.**