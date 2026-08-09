# METHOD 6-3-5

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Применение метода 6-3-5 к проектированию профессионального сайта актрисы

**Целевой файл:** `docs/customer-journey/method-6-3-5.md`  
**Документ:** DOC-022  
**Статус:** ✅ Completed  
**Тип:** Customer Journey / Product Discovery

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

---

# 1. Назначение документа

Настоящий документ формализует использование метода 6-3-5 для проектирования целевого продукта.

Для данного проекта используется адаптированная модель:

```text
6 персон
×
3 продуктовых вывода
×
5 минут реального использования
=
18 ключевых UX/Product Insights
```

Метод используется не как теоретическое brainstorming-упражнение, а как средство проверки того, способен ли продукт реально обслуживать разные профессиональные сценарии без нарушения единой продуктовой логики.

---

# 2. Проектная адаптация 6-3-5

В классическом подходе 6-3-5 шесть участников формируют по три идеи за ограниченный интервал.

В настоящем проекте применяется функциональная адаптация:

```text
6 профессиональных Persona
×
3 независимых наблюдения/идеи
×
5-минутный Customer Journey
```

Каждая Persona рассматривается как отдельная точка зрения профессионального пользователя.

---

# 3. Цель метода

Метод должен помочь ответить на пять вопросов:

1. Может ли каждая Persona получить профессионально значимый результат за пять минут?
2. Какие элементы сайта критичны для разных пользователей?
3. Какие функции одновременно полезны нескольким Persona?
4. Где возникает unnecessary friction?
5. Какие функции необходимо автоматизировать или изменить до реализации конечного UX?

---

# 4. Метод не используется для изменения Product Scope

6-3-5 не является механизмом бесконтрольного добавления функций.

Каждый Product Insight должен проверяться против:

```text
Product Overview
Business Rules
Information Architecture
Product Scope
Product Principles
```

Если идея выходит за утверждённый Scope, она не становится автоматически требованием.

---

# 5. Исходные Persona

Используются шесть утверждённых персон:

```text
P01 — Casting Director
P02 — Casting Assistant
P03 — Commercial Casting Producer
P04 — Director / Producer
P05 — International Casting Specialist
P06 — Virtual Portfolio Operator
```

---

# 6. Унифицированная структура пятиминутного теста

Для каждой Persona моделируется период:

```text
00:00
→
05:00
```

Каждый сценарий включает:

1. Entry Point.
2. Первое впечатление.
3. Проверку ключевых данных.
4. Evidence.
5. Professional Action.
6. Возможные friction points.
7. Три Product Insights.

---

# 7. Временные зоны сценария

Пятиминутный сценарий условно делится:

## T0 — 0:00–0:15

Recognition.

Пользователь должен понять:

- кто перед ним;
- что это профессиональный профиль;
- что делать дальше.

---

## T1 — 0:15–1:00

Initial Qualification.

Проверяются:

- фото;
- location;
- Skills;
- Languages;
- основные параметры.

---

## T2 — 1:00–2:30

Evidence.

Используются:

- Video;
- Portfolio;
- Emotional Grid;
- Projects;
- Roles.

---

## T3 — 2:30–4:00

Structured Professional Material.

Открывается:

- Questionnaire;
- Public Builder;
- Localized PDF.

---

## T4 — 4:00–5:00

Professional Action.

Например:

- Download;
- Casting Invitation;
- Request Materials;
- Contact.

---

# 8. Критерий успешности пятиминутного теста

Persona должна иметь возможность завершить Desired Outcome в пределах пяти минут без:

- изучения внутренней структуры сайта;
- обязательной регистрации;
- ручного поиска по множеству разделов;
- обращения к администратору для получения базовых материалов;
- загрузки private data;
- неочевидных промежуточных действий.

---

# 9. Общая оценочная шкала

Для каждого Journey оцениваются:

| Критерий | Оценка |
|---|---|
| Recognition | 0–2 |
| Information Findability | 0–2 |
| Evidence Availability | 0–2 |
| Questionnaire Efficiency | 0–2 |
| Professional Action | 0–2 |

Максимум:

```text
10
```

---

# 10. Интерпретация результата

```text
9–10
Excellent

7–8
Acceptable with optimization

5–6
High friction

0–4
Journey failure
```

Целевой результат production UX:

```text
>= 9
```

для основных Persona Journeys.

---

# 11. PERSONA 1 — Casting Director

**Persona ID:** `PERSONA-CD`  
**Рабочее имя:** Ирина

---

# 12. Casting Director — Scenario

Ирина получает ссылку на актрису от коллеги.

Контекст:

- находится за notebook;
- одновременно открыто несколько кандидатов;
- ищет исполнительницу для конкретной роли;
- предварительно заинтересована, но актрису лично не знает.

---

# 13. Casting Director — 00:00–00:15

Открывается Profile / Landing.

Ирина должна сразу увидеть:

- имя;
- Primary Close-Up;
- professional identity;
- location;
- Languages;
- Primary CTA.

Она не должна начинать с длинного текста.

---

# 14. Casting Director — 00:15–00:45

Ирина проверяет:

- Full Body;
- Close-Up;
- основные professional parameters;
- Languages;
- key Skills.

При необходимости использует Search.

---

# 15. Casting Director — 00:45–01:30

Открывает Video Intro.

Цель:

- camera presence;
- речь;
- естественность;
- общее впечатление.

Video должен запускаться максимально быстро.

---

# 16. Casting Director — 01:30–02:15

Открывает Emotional Range.

Сначала видит:

```text
Emotional Grid
```

Затем при необходимости:

```text
Full Emotional Portfolio
```

---

# 17. Casting Director — 02:15–03:00

Просматривает Selected Projects.

Её интересуют:

- релевантные роли;
- формат проектов;
- role-specific media.

Полный архив не должен мешать первичной оценке.

---

# 18. Casting Director — 03:00–03:30

Открывает Primary Prepared Questionnaire.

Проверяет:

- достаточно ли информации;
- подходит ли её объём для текущего кастинга.

---

# 19. Casting Director — 03:30–04:20

Если готовая Questionnaire подходит:

```text
Download
```

Если не подходит:

```text
Customize This Questionnaire
```

Например:

- отключает Training;
- добавляет конкретный Project;
- оставляет Emotional Grid;
- выбирает нужные Skills.

---

# 20. Casting Director — 04:20–05:00

Формирует PDF.

Далее:

```text
Invite to Casting
```

или:

```text
Request Materials
```

---

# 21. Casting Director — Friction Risks

- нельзя быстро найти Full Body;
- нет структурированных Skills;
- Video спрятано;
- один огромный PDF;
- Contact находится далеко;
- Emotional Range состоит из десятков отдельных фото;
- неясно, актуальны ли материалы.

---

# 22. Insight CD-01 — Casting Quick View

## Идея

Создать компактное профессиональное представление ключевой информации.

## Причина

Casting Director не должен собирать базовый профиль самостоятельно по нескольким страницам.

## Включает

- identity;
- Close-Up;
- Full Body;
- location;
- Languages;
- Skills;
- Video;
- Emotional Grid;
- Selected Projects.

## Product Impact

Высокий.

---

# 23. Insight CD-02 — Search Inside Profile

## Идея

Добавить поиск по публичным structured professional data.

## Причина

Casting Director часто ищет конкретный критерий.

Например:

```text
английский
танцы
вождение
театр
```

## Product Impact

Высокий.

---

# 24. Insight CD-03 — Casting-Specific CTA

## Идея

Использовать:

```text
Пригласить на кастинг
```

вместо универсального:

```text
Связаться
```

в релевантном контексте.

## Product Impact

Высокий.

---

# 25. PERSONA 2 — Casting Assistant

**Persona ID:** `PERSONA-CA`  
**Рабочее имя:** Даниил

---

# 26. Casting Assistant — Scenario

Даниил получает задачу:

> Найти несколько кандидаток, соответствующих требованиям роли.

Он одновременно сравнивает много профилей.

---

# 27. Casting Assistant — 00:00–00:20

Открывает профиль.

Не читает Biography.

Сразу проверяет:

- location;
- age/appearance data where applicable;
- Languages;
- Skills.

---

# 28. Casting Assistant — 00:20–01:00

Использует Search:

```text
английский
```

или:

```text
вокал
```

Ожидает immediate result.

---

# 29. Casting Assistant — 01:00–01:45

Открывает Portfolio Filters.

Например:

```text
Close-Up
Full Body
Profile
```

---

# 30. Casting Assistant — 01:45–02:30

Проверяет:

- один-два Selected Projects;
- соответствующую Role;
- Video.

Не должен просматривать полный Project Archive.

---

# 31. Casting Assistant — 02:30–03:15

Открывает Compact Questionnaire.

Она должна быть пригодна для быстрой внутренней пересылки.

---

# 32. Casting Assistant — 03:15–04:20

При необходимости:

```text
Customize
```

и оставляет только необходимые сведения.

---

# 33. Casting Assistant — 04:20–05:00

Скачивает Share-Ready PDF и передаёт его Casting Director.

---

# 34. Casting Assistant — Friction Risks

- факты существуют только в prose;
- отсутствует search;
- Portfolio не фильтруется;
- PDF перегружен;
- Skills и Languages смешаны с Biography;
- inconsistent naming.

---

# 35. Insight CA-01 — Structured Quick Facts

## Идея

Все ключевые casting facts должны быть структурированными и быстро сканируемыми.

## Product Impact

Критический.

---

# 36. Insight CA-02 — Professional Portfolio Filters

## Идея

Portfolio должен иметь профессиональные категории.

Не просто:

```text
Все фотографии
```

а:

```text
Full Body
Close-Up
Profile
Three Quarter
...
```

---

# 37. Insight CA-03 — One-Click Compact PDF

## Идея

Compact Questionnaire должна быть доступна практически немедленно.

Цель:

```text
Profile
→ Compact PDF
```

с минимальным количеством действий.

---

# 38. PERSONA 3 — Commercial Casting Producer

**Persona ID:** `PERSONA-CP`  
**Рабочее имя:** Мария

---

# 39. Commercial Producer — Scenario

Мария получает ссылку через social media на смартфоне.

Ищет актрису для рекламного ролика.

---

# 40. Commercial Producer — 00:00–00:10

Открывает страницу.

Важнее всего первое visual impression.

Необходимы:

- Current Close-Up;
- professional identity;
- immediate visual quality.

---

# 41. Commercial Producer — 00:10–00:40

Смотрит:

- Full Body;
- Appearance;
- несколько current photos.

Shooting Date повышает доверие.

---

# 42. Commercial Producer — 00:40–01:20

Открывает Emotional Grid.

Получает быстрое представление диапазона:

- smile;
- concern;
- surprise;
- seriousness;
- other approved emotional categories.

---

# 43. Commercial Producer — 01:20–02:00

Запускает короткий Video Intro.

---

# 44. Commercial Producer — 02:00–03:00

При необходимости проверяет:

- location;
- key Skills;
- Languages.

Projects могут быть вторичными.

---

# 45. Commercial Producer — 03:00–04:00

Открывает Quick/Standard Questionnaire.

Если документ перегружен:

```text
Customize
```

---

# 46. Commercial Producer — 04:00–05:00

Использует:

```text
Offer Role
```

или:

```text
Invite to Casting
```

---

# 47. Commercial Producer — Friction Risks

- desktop-only design;
- heavy hero;
- старые photos без даты;
- длинная Biography;
- emotional materials неудобно просматривать;
- огромный PDF;
- Contact требует нескольких переходов.

---

# 48. Insight CP-01 — Mobile-First Casting Experience

## Идея

Критический casting journey должен полностью работать на smartphone.

Это означает не только responsive CSS, но:

- touch-friendly gallery;
- short CTA path;
- usable Video;
- usable Builder;
- usable Feedback Form.

---

# 49. Insight CP-02 — Shooting Date as Trust Signal

## Идея

Дата профессиональной фотосессии должна использоваться как понятный сигнал актуальности.

Особенно:

- Portfolio;
- Emotional Portfolio.

---

# 50. Insight CP-03 — Contextual Commercial CTA

## Идея

На соответствующих страницах должен быть CTA:

```text
Предложить роль
```

или:

```text
Предложить сотрудничество
```

а не только общий Contact.

---

# 51. PERSONA 4 — Director / Producer

**Persona ID:** `PERSONA-DIR`  
**Рабочее имя:** Алексей

---

# 52. Director — Scenario

Алексей получает рекомендацию от Casting Director.

Уже знает, что кандидат прошёл первичный screening.

Ему нужно понять творческий диапазон.

---

# 53. Director — 00:00–00:20

Открывает профиль.

Первичный fit уже менее важен.

Сразу ищет:

- Video;
- Emotional Range;
- Selected Projects.

---

# 54. Director — 00:20–01:30

Смотрит Video Intro / Showreel.

Основная цель:

- выразительность;
- органичность;
- профессиональное присутствие.

---

# 55. Director — 01:30–02:20

Просматривает Emotional Grid.

При интересе открывает полный Emotional Portfolio.

---

# 56. Director — 02:20–03:30

Открывает Selected Project.

Изучает:

- Role;
- role-specific photos;
- scene/video;
- project description.

---

# 57. Director — 03:30–04:15

Проверяет:

- Training;
- Skills;
- дополнительные материалы.

---

# 58. Director — 04:15–05:00

Выбирает:

```text
Request Materials
```

или:

```text
Invite to Casting
```

---

# 59. Director — Friction Risks

- Profile выглядит как CV;
- невозможно быстро увидеть acting range;
- Projects представлены длинным однообразным списком;
- Role-specific media не связано;
- Video materials не классифицированы.

---

# 60. Insight DIR-01 — Emotional Range as First-Class Content

## Идея

Emotional Range должен быть полноценным верхнеуровневым профессиональным capability.

Не второстепенной галереей.

---

# 61. Insight DIR-02 — Progressive Projects

## Идея

Сначала показывать:

```text
Selected / Featured Projects
```

Далее:

```text
View All
```

Это обеспечивает depth без перегрузки.

---

# 62. Insight DIR-03 — Request Materials CTA

## Идея

Для заинтересованного режиссёра должен быть отдельный action:

```text
Запросить дополнительные материалы
```

Это конверсионно отличается от обычного Contact.

---

# 63. PERSONA 5 — International Casting Specialist

**Persona ID:** `PERSONA-INT`  
**Рабочее имя:** Sophie

---

# 64. International Specialist — Scenario

Sophie получает ссылку на профиль для международного production.

Основной рабочий язык — English.

---

# 65. International Specialist — 00:00–00:15

Открывает страницу.

Сразу требуется:

```text
EN
```

или понятный Language Switch.

---

# 66. International Specialist — 00:15–00:45

Проверяет:

- location;
- base;
- Languages;
- CEFR;
- professional identity.

---

# 67. International Specialist — 00:45–01:30

Смотрит:

- Full Body;
- Close-Up;
- Skills.

---

# 68. International Specialist — 01:30–02:20

Открывает Video.

---

# 69. International Specialist — 02:20–03:10

Проверяет:

- relevant Projects;
- Training;
- international-friendly facts.

---

# 70. International Specialist — 03:10–04:00

Открывает English Questionnaire.

---

# 71. International Specialist — 04:00–05:00

Если требуется другой состав:

```text
Build Custom Questionnaire
→ English
→ PDF
```

После чего:

```text
Contact
```

---

# 72. International Specialist — Friction Risks

- partial translation;
- Russian-only labels;
- language switch теряет context;
- PDF не локализован;
- CEFR отсутствует;
- unclear location;
- mixed transliteration.

---

# 73. Insight INT-01 — Language as Global Journey State

## Идея

Language не должен быть настройкой отдельных страниц.

Он должен быть частью текущего Customer Journey state.

---

# 74. Insight INT-02 — Fast Language Switching

## Идея

Переключение языка:

```text
same page
same context
same Builder state
new localization
```

где это возможно.

---

# 75. Insight INT-03 — Share-Ready Localized PDF

## Идея

English/other supported PDF должен быть полноценным профессиональным документом, готовым для пересылки.

---

# 76. PERSONA 6 — Virtual Portfolio Operator

**Persona ID:** `PERSONA-VOP`

---

# 77. Virtual Operator — Scenario

В систему загружен новый набор фотографий.

Кроме этого:

- одна Questionnaire стала stale;
- один public URL перестал работать;
- через три дня Casting deadline.

Virtual Operator должен обработать ситуацию без лишней нагрузки на Admin.

---

# 78. Virtual Operator — 00:00–00:30

Получает domain events:

```text
MediaUploaded
QuestionnaireDependencyChanged
LinkValidationFailed
CastingDeadlineApproaching
```

---

# 79. Virtual Operator — 00:30–01:15

Анализирует новые фотографии.

Может предложить:

- probable Portfolio category;
- duplicate;
- Emotional Portfolio candidate.

Не применяет автоматически.

---

# 80. Virtual Operator — 01:15–02:00

Проверяет data completeness.

Например:

```text
Emotional Portfolio has no Shooting Date
```

Создаёт Observation.

---

# 81. Virtual Operator — 02:00–02:45

Находит stale Questionnaire.

Создаёт actionable recommendation:

```text
Questionnaire Preview should be regenerated.
```

---

# 82. Virtual Operator — 02:45–03:30

Проверяет broken URL.

Создаёт Alert с точной entity reference.

---

# 83. Virtual Operator — 03:30–04:15

Выявляет approaching Casting deadline.

Повышает приоритет exception.

---

# 84. Virtual Operator — 04:15–05:00

Формирует Admin Dashboard:

```text
🔴 Casting deadline — 3 days
🟠 Broken Showreel link
🟡 Questionnaire stale
🟡 12 new images require classification
```

Admin занимается решениями, а не поиском проблем.

---

# 85. Virtual Operator — Friction Risks

- слишком много уведомлений;
- low-value observations;
- неясно, что сделал AI;
- автоматические изменения без approval;
- невозможно отклонить suggestion;
- нет priority;
- нет entity link.

---

# 86. Insight VOP-01 — Change Once, Propagate Everywhere

## Идея

После изменения Master Data система должна автоматически выявлять все Derived Representations, которые требуют обновления.

---

# 87. Insight VOP-02 — Draft Automation

## Идея

Virtual Operator должен автоматически готовить:

- classification proposal;
- crop proposal;
- stale rebuild proposal;
- content draft;

но не выполнять профессионально значимые изменения без approval.

---

# 88. Insight VOP-03 — Exception-Driven Administration

## Идея

Admin Dashboard должен показывать:

```text
what requires action now
```

а не:

```text
everything that exists
```

---

# 89. Итоговые 18 Product Insights

| ID | Persona | Product Insight |
|---|---|---|
| CD-01 | Casting Director | Casting Quick View |
| CD-02 | Casting Director | Search Inside Profile |
| CD-03 | Casting Director | Casting-Specific CTA |
| CA-01 | Casting Assistant | Structured Quick Facts |
| CA-02 | Casting Assistant | Professional Portfolio Filters |
| CA-03 | Casting Assistant | One-Click Compact PDF |
| CP-01 | Commercial Producer | Mobile-First Casting Experience |
| CP-02 | Commercial Producer | Shooting Date as Trust Signal |
| CP-03 | Commercial Producer | Contextual Commercial CTA |
| DIR-01 | Director | Emotional Range as First-Class Content |
| DIR-02 | Director | Progressive Projects |
| DIR-03 | Director | Request Materials CTA |
| INT-01 | International | Language as Global Journey State |
| INT-02 | International | Fast Language Switching |
| INT-03 | International | Share-Ready Localized PDF |
| VOP-01 | Virtual Operator | Change Once, Propagate Everywhere |
| VOP-02 | Virtual Operator | Draft Automation |
| VOP-03 | Virtual Operator | Exception-Driven Administration |

---

# 90. Cross-Persona Synthesis

Некоторые выводы поддерживают одновременно несколько Persona.

---

# 91. Cluster A — Fast Professional Evaluation

Объединяет:

```text
CD-01
CA-01
CA-02
CP-01
```

Следствие:

Public UX должен позволять проводить initial professional evaluation без чтения длинных narrative sections.

---

# 92. Cluster B — Findability

Объединяет:

```text
CD-02
CA-01
CA-02
INT-01
```

Следствие:

Professional Facts должны быть:

- structured;
- searchable;
- categorized;
- localized.

---

# 93. Cluster C — Professional Evidence

Объединяет:

```text
CP-02
DIR-01
DIR-02
CD-01
```

Следствие:

Evidence не должно быть одним бесструктурным media archive.

Необходимы:

- Portfolio;
- Emotional Range;
- Selected Projects;
- Video.

---

# 94. Cluster D — Questionnaire Flexibility

Объединяет:

```text
CA-03
INT-03
CD-01
```

и требования Product Scope.

Следствие:

```text
Prepared Questionnaire
+
Public Builder
```

является обязательной двойной моделью.

---

# 95. Cluster E — Contextual Conversion

Объединяет:

```text
CD-03
CP-03
DIR-03
```

Следствие:

универсальный CTA:

```text
Связаться
```

недостаточен как единственное профессиональное действие.

---

# 96. Cluster F — International Usability

Объединяет:

```text
INT-01
INT-02
INT-03
```

Следствие:

локализация является end-to-end capability.

---

# 97. Cluster G — Administrative Automation

Объединяет:

```text
VOP-01
VOP-02
VOP-03
```

Следствие:

Virtual Operator должен быть построен вокруг:

```text
Derived Automation
+
Draft Suggestions
+
Exception Prioritization
```

---

# 98. Product Capabilities, подтверждённые методом

Метод подтверждает необходимость следующих capabilities:

```text
Casting Quick View
Structured Professional Facts
Public Search
Portfolio Categories
Mobile Professional UX
Shooting Date
Emotional Grid
Featured Projects
Role-Specific Evidence
Localized Journey
Prepared Questionnaires
Public Questionnaire Builder
Contextual CTA
Virtual Operator
Exception Dashboard
```

---

# 99. Что метод не подтвердил как приоритет

Пятиминутные сценарии не дают оснований ставить в начало:

- длинную Biography;
- полный Project Archive;
- полный Training Archive;
- декоративные эффекты;
- Blog content;
- social feed.

Эти capabilities могут сохраняться, но не должны вытеснять критические профессиональные действия.

---

# 100. Business Impact Ranking

## Critical

- Structured Quick Facts;
- Casting Quick View;
- Prepared Questionnaire;
- Public Builder;
- Mobile UX;
- Contextual CTA.

## High

- Public Search;
- Emotional Grid;
- Portfolio Filters;
- Localized PDF;
- Selected Projects.

## Medium-High

- Shooting Date;
- Request Materials;
- Virtual Operator analytics.

Все входят в утверждённую модель, но ranking используется при UX prioritization.

---

# 101. Five-Minute Rule и глубина контента

Правило пяти минут не означает ограничения объёма сайта.

Система может иметь:

- сотни фотографий;
- множество Projects;
- подробный Training;
- archive.

Но:

```text
Large Data Volume
≠
Large Decision Time
```

Information Architecture должна обеспечивать progressive disclosure.

---

# 102. First 15 Seconds Rule

Все внешние Persona подтвердили необходимость быстро ответить:

```text
Who?
What?
Relevant?
What next?
```

Поэтому Hero/Quick View должен давать:

- Actor Identity;
- visual;
- high-value facts;
- key CTA.

---

# 103. First 60 Seconds Rule

В течение первой минуты пользователь должен иметь возможность проверить:

- appearance;
- location;
- Languages;
- Skills;
- Portfolio.

---

# 104. Two-Minute Evidence Rule

В пределах примерно двух минут должны быть доступны:

- Video;
- Emotional Range;
- selected experience.

---

# 105. Five-Minute Action Rule

Не позднее пятой минуты пользователь должен иметь возможность:

- получить PDF;
- самостоятельно сформировать PDF;
- начать профессиональное обращение.

---

# 106. Minimum Viable Professional Journey

Минимальный корректный Journey:

```text
Entry
→ Quick Facts
→ Evidence
→ Questionnaire
→ Action
```

---

# 107. Advanced Professional Journey

Расширенный:

```text
Entry
→ Quick View
→ Search
→ Portfolio
→ Video
→ Emotional
→ Project
→ Questionnaire
→ Customize
→ PDF
→ Casting CTA
```

---

# 108. Failure Condition 6-3-5-01

Journey считается неуспешным, если пользователь не может определить, где находится Questionnaire.

---

# 109. Failure Condition 6-3-5-02

Journey неуспешен, если для поиска конкретного Skill необходимо вручную читать Biography.

---

# 110. Failure Condition 6-3-5-03

Journey неуспешен, если Professional Contact недоступен или непонятен.

---

# 111. Failure Condition 6-3-5-04

Journey неуспешен для Commercial Producer, если ключевые функции не работают на mobile.

---

# 112. Failure Condition 6-3-5-05

International Journey неуспешен, если Language Switch не обеспечивает полноценную локализованную профессиональную цепочку.

---

# 113. Failure Condition 6-3-5-06

Virtual Operator Journey неуспешен, если Admin по-прежнему вынужден вручную проверять все объекты для обнаружения проблем.

---

# 114. Journey Scoring Requirement

Каждый отдельный Persona Journey Document должен иметь итоговую оценку потенциального UX по 10-балльной шкале после проектирования flows.

До production score является design assessment.

После production может дополняться реальными metrics.

---

# 115. Связь с Business Rules

Примеры:

### CD-02 Search

поддерживает:

```text
BR-CJM-003
BR-CJM-010
BR-PRF-002
```

### Public Questionnaire Builder

поддерживает:

```text
BR-PQB-001–030
```

### VOP-02 Draft Automation

поддерживает:

```text
BR-VOP-002
BR-VOP-003
BR-VOP-004
```

---

# 116. Связь с Product Principles

Ключевые:

```text
PP-004 Casting Professional Drives Public UX
PP-005 Minimum Actions
PP-006 Progressive Disclosure
PP-008 No Dead Ends
PP-016 Admin-Curated + User-Configurable
PP-017 Start From a Useful Default
PP-038 Automation by Exception
PP-053 Five-Minute Professional Journey
```

---

# 117. Связь с UX Requirements

Каждый Insight должен получить отражение в будущем UX package.

Пример:

```text
CD-01
→ UX Quick View

CD-02
→ UX Public Search

CA-02
→ UX Portfolio Filters

CP-01
→ Responsive Behaviour

INT-02
→ Language Journey

VOP-03
→ Admin Dashboard
```

---

# 118. Связь с Functional Requirements

После формирования Functional Requirements каждому Insight присваиваются связанные:

```text
FR-...
```

Пример:

```text
CD-02
→ FR-SEARCH-001
→ FR-SEARCH-002
```

---

# 119. Связь с E2E

Каждая Persona должна иметь E2E Journey Test.

Пример:

```text
E2E-CJM-CD-001

Entry
→ Search Skill
→ Video
→ Questionnaire
→ Custom PDF
→ Casting CTA
```

---

# 120. Запрет на искусственную оптимизацию теста

Нельзя проектировать отдельный специальный путь только для успешного прохождения пятиминутного теста, если обычный пользовательский интерфейс остаётся сложным.

Тестируется реальный production flow.

---

# 121. Automation Insights Matrix

| Insight | Auto | Suggest | Human |
|---|---:|---:|---:|
| Quick View | ✓ render |  |  |
| Search | ✓ |  |  |
| Portfolio Filters | ✓ |  |  |
| Compact PDF | ✓ render |  | selection |
| Emotional Grid | derived | crop suggest | confirm |
| Localization | ✓ |  | translations approve where needed |
| Custom PDF | ✓ render | defaults | content selection |
| Virtual Operator | detect | ✓ | approve |
| Contextual CTA | ✓ route |  | submit |
| Project Prioritization |  | ✓ | approve |

---

# 122. Метод 6-3-5 и Public Questionnaire Builder

Особенно важный вывод:

разные Persona требуют разные составы профессионального документа.

Следовательно:

```text
One Universal PDF
```

не соответствует Customer Journey.

Целевая модель:

```text
Prepared Compact
Prepared Extended
Prepared Casting
+
Public Builder Templates
```

---

# 123. Метод 6-3-5 и Homepage

Home должна обслуживать общий знаменатель пяти внешних Persona:

```text
Who
Appearance
Location
Languages
Skills
Video
Questionnaire
Contact
```

Secondary sections:

```text
Emotional
Projects
Training
Achievements
```

---

# 124. Метод 6-3-5 и Casting Quick View

Quick View является наиболее универсальным cross-persona capability.

Он особенно полезен:

- CD;
- CA;
- CP;
- INT.

Director может использовать его как Entry Context перед переходом к deeper Evidence.

---

# 125. Метод 6-3-5 и Search

Search особенно нужен:

- CD;
- CA;
- INT.

Search должен использовать structured data, а не только narrative text.

---

# 126. Метод 6-3-5 и Emotional Range

Emotional Range особенно критичен:

- CD;
- CP;
- DIR.

Следовательно, он должен иметь высокий information priority.

---

# 127. Метод 6-3-5 и Video

Video является важным Evidence минимум для:

- CD;
- CP;
- DIR;
- INT.

Video Intro нельзя прятать глубоко в Media section.

---

# 128. Метод 6-3-5 и Training

Training важен, но не является P0 для большинства первых пятиминутных Journeys.

Следствие:

Training:

- сохраняется как самостоятельный domain;
- доступен в Profile/Questionnaire;
- используется progressive disclosure.

---

# 129. Метод 6-3-5 и Blog

Blog не является частью критического casting decision path.

Он может выполнять:

- SEO;
- personal/professional context;
- content marketing.

Но не должен вытеснять Professional P0 content.

---

# 130. Метод 6-3-5 и Social Media

Social часто является Entry Source, особенно для Commercial Producer.

Поэтому deep-linked public pages должны сохранять:

- professional identity;
- navigation;
- Questionnaire access;
- CTA.

---

# 131. Метод 6-3-5 и QR

QR особенно полезен как handoff между:

```text
PDF / printed material
→ current digital profile
```

Он должен сокращать Journey, а не создавать новый isolated experience.

---

# 132. Метод 6-3-5 и Marketing

Marketing должен оптимизировать не только:

```text
Click
```

а:

```text
Relevant Entry
→ Professional Evaluation
→ Professional Action
```

---

# 133. Метод 6-3-5 и Virtual Operator

Внутренний автоматизированный Journey показывает, что автоматизация должна быть:

```text
Event-driven where practical
+
Exception-oriented
+
Human-approved for business meaning
```

---

# 134. Product Insights Traceability IDs

Для дальнейшей трассировки фиксируются постоянные IDs:

```text
INS-CD-01 Casting Quick View
INS-CD-02 Search Inside Profile
INS-CD-03 Casting-Specific CTA

INS-CA-01 Structured Quick Facts
INS-CA-02 Portfolio Filters
INS-CA-03 Compact PDF

INS-CP-01 Mobile-First Casting
INS-CP-02 Shooting Date Trust
INS-CP-03 Commercial CTA

INS-DIR-01 Emotional Range
INS-DIR-02 Progressive Projects
INS-DIR-03 Request Materials

INS-INT-01 Global Language State
INS-INT-02 Fast Language Switch
INS-INT-03 Localized PDF

INS-VOP-01 Propagation
INS-VOP-02 Draft Automation
INS-VOP-03 Exception Administration
```

---

# 135. Traceability Model

В дальнейшем:

```text
INS-...
   ↓
CJM-...
   ↓
UF-...
   ↓
FR-...
   ↓
MODULE
   ↓
ISSUE
   ↓
TEST
```

---

# 136. Метод 6-3-5 как Design Gate

Перед завершением публичного UX необходимо повторно провести проверку шести Journeys.

Если хотя бы одна Primary Persona не может выполнить свой Desired Outcome:

```text
<= 5 minutes
```

без обоснованной причины, UX должен быть пересмотрен.

---

# 137. Product Decision Priority

Если возникает спор о placement функции, использовать результаты 6-3-5.

Пример:

### Вопрос

Что должно быть ближе к первому экрану:

```text
Video
```

или:

```text
Training Archive
```

Результаты Persona Journeys однозначно повышают приоритет Video.

---

# 138. Principle of Shared Benefit

Особенно высокий приоритет имеют capabilities, полезные сразу нескольким Persona.

Например:

```text
Quick View
Video
Questionnaire
Public Builder
Structured Facts
```

---

# 139. Principle of Specialized Depth

Capability, важный только некоторым Persona, не удаляется.

Вместо этого используется Progressive Disclosure.

Например:

```text
Role-specific Project Depth
```

особенно важен Director Persona.

---

# 140. Итоговая 6-3-5 модель продукта

```text
CASTING DIRECTOR
→ Quick View
→ Search
→ Casting CTA

CASTING ASSISTANT
→ Structured Facts
→ Filters
→ Compact PDF

COMMERCIAL PRODUCER
→ Mobile
→ Current Visuals
→ Contextual CTA

DIRECTOR
→ Emotional Range
→ Progressive Projects
→ Request Materials

INTERNATIONAL SPECIALIST
→ Global Language
→ Fast Switch
→ Localized PDF

VIRTUAL OPERATOR
→ Propagation
→ Draft Automation
→ Exception Dashboard
```

---

# 141. Definition of 6-3-5 Compliance

Целевой продукт соответствует настоящему документу, если:

1. все шесть Persona имеют определённый Journey;
2. каждый Journey имеет трёх ключевых Product Insights;
3. пять внешних professional Journeys могут привести к значимому действию за пять минут;
4. Virtual Operator сокращает ручную административную работу;
5. Insights отражены в UX;
6. Insights трассируются до Functional Requirements;
7. критические Insights покрыты E2E;
8. ни один Insight не нарушает Product Scope или Business Rules.

---

# 142. Финальный принцип

> **Метод 6-3-5 используется как проверка реальной профессиональной эффективности продукта: каждая из шести ключевых персон должна получить за пять минут именно тот опыт, который соответствует её профессиональной роли, без создания шести разных сайтов и без нарушения единого источника истины.**