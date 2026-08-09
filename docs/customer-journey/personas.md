# PERSONAS

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Основные пользовательские персоны профессионального сайта актрисы

**Целевой файл:** `docs/customer-journey/personas.md`  
**Документ:** DOC-021  
**Статус:** ✅ Completed  
**Тип:** Customer Journey

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/overview.md`

---

# 1. Назначение документа

Настоящий документ определяет основные пользовательские персоны продукта.

Persona используется не как художественный образ пользователя, а как **нормативная модель конкретного профессионального поведения**, необходимая для:

- Customer Journey;
- UX;
- Information Priority;
- CTA;
- Navigation;
- Public Questionnaire Builder;
- Admin Dashboard;
- Testing;
- Marketing;
- Conversion Analysis.

Каждая крупная функция Public UX должна быть обоснована потребностью минимум одной Persona.

---

# 2. Основной принцип Persona Model

Persona должна отвечать минимум на следующие вопросы:

1. Кто пользователь?
2. В каком профессиональном контексте он находится?
3. Какой результат ему нужен?
4. Сколько у него времени?
5. Каким устройством он пользуется?
6. Какие сведения для него критичны?
7. Какие сведения вторичны?
8. Какие действия он готов выполнить?
9. Что создаёт friction?
10. Что создаёт доверие?
11. Может ли он принять окончательное решение?
12. Какой CTA для него наиболее релевантен?

---

# 3. Набор основных персон

В продукте фиксируются шесть обязательных основных персон:

```text
PERSONA-CD
Casting Director

PERSONA-CA
Casting Assistant / Casting Researcher

PERSONA-CP
Commercial Casting Producer

PERSONA-DIR
Director / Producer

PERSONA-INT
International Casting Specialist

PERSONA-VOP
Virtual Portfolio Operator
```

Первые пять являются внешними профессиональными потребителями информации.

Шестая является внутренним программным оператором.

---

# 4. Дополнительные системные роли

Следующие роли участвуют в продукте, но не являются основными Persona 6-3-5:

```text
Profile Owner / Actress
Administrator
Editor
Viewer
Generic Public Visitor
```

Они описываются в соответствующих UX, Security и Access Control документах.

---

# 5. Общие характеристики профессиональных внешних персон

Внешний профессиональный пользователь обычно:

- не обязан знать актрису заранее;
- не знает архитектуру сайта;
- сравнивает нескольких кандидатов;
- имеет ограниченное время;
- ожидает структурированные данные;
- может работать в мобильном режиме;
- может открыть не главную страницу, а deep link;
- может пересылать полученные материалы другим участникам команды;
- ожидает корректный, профессиональный и актуальный профиль.

---

# 6. PERSONA-CD — Casting Director

## 6.1. Название

**Casting Director**

Рабочее имя для сценариев:

**Ирина**

---

## 6.2. Профессиональная роль

Отвечает за подбор актёров для проекта или отдельных ролей.

Может:

- самостоятельно принимать решение о shortlist;
- приглашать на пробы;
- запрашивать дополнительные материалы;
- передавать кандидата режиссёру/продюсеру.

---

## 6.3. Контекст

Типичный сценарий:

- сериал;
- фильм;
- рекламный проект;
- театральная постановка;
- digital production.

На одну роль может рассматриваться большое количество кандидатов.

---

## 6.4. Основная задача

> Быстро понять, соответствует ли актриса требованиям роли и стоит ли переходить к следующему профессиональному этапу.

---

## 6.5. Time Budget

Ориентир:

```text
30 секунд — первичная оценка
1–2 минуты — проверка материала
до 5 минут — полный путь до анкеты/контакта
```

---

## 6.6. Тип устройства

Основной:

- desktop;
- notebook.

Дополнительный:

- tablet;
- smartphone.

---

# 7. PERSONA-CD — Ключевые вопросы

При открытии профиля Casting Director мысленно отвечает на вопросы:

1. Кто это?
2. Подходит ли внешне?
3. Где находится?
4. Какие языки?
5. Есть ли нужные навыки?
6. Как выглядит в полный рост?
7. Как выглядит крупным планом?
8. Как ведёт себя в кадре?
9. Какой эмоциональный диапазон?
10. Есть ли релевантный опыт?
11. Где получить анкету?
12. Как пригласить на кастинг?

---

# 8. PERSONA-CD — P0 Information

Критически важная информация:

```text
Name
Primary Close-Up
Primary Full Body
Professional Identity
Location
Languages
Key Skills
Video Intro
Questionnaire
Contact / Casting CTA
```

---

# 9. PERSONA-CD — P1 Information

Следующий уровень:

- Emotional Grid;
- Selected Projects;
- Role experience;
- Showreel;
- professional parameters;
- relevant training.

---

# 10. PERSONA-CD — Низкий приоритет

На первом этапе не должны мешать:

- длинная Biography;
- все курсы подряд;
- полный архив Projects;
- все достижения;
- системная информация;
- декоративные блоки.

---

# 11. PERSONA-CD — Primary CTA

```text
Пригласить на кастинг
```

Secondary:

```text
Открыть анкету
Смотреть видео
Запросить материалы
```

---

# 12. PERSONA-CD — Pain Points

Основные проблемы:

### CD-P01

Не удаётся быстро найти Full Body.

### CD-P02

Навыки спрятаны в Biography.

### CD-P03

Видео требует нескольких переходов.

### CD-P04

PDF слишком длинный.

### CD-P05

PDF не содержит нужной информации.

### CD-P06

Неясно, актуальны ли фотографии.

### CD-P07

Контакт спрятан.

### CD-P08

Сайт перегружен декоративным storytelling.

---

# 13. PERSONA-CD — Trust Signals

Доверие увеличивают:

- актуальная Shooting Date;
- профессиональные реальные фотографии;
- последовательные факты;
- работающие video links;
- официальный PDF;
- понятная дата формирования документа;
- официальный profile URL;
- корректно оформленный Contact.

---

# 14. PERSONA-CD — Critical Product Capabilities

Для Persona обязательны:

- Casting Quick View;
- Portfolio;
- Search;
- Video Intro;
- Emotional Grid;
- Questionnaire;
- Public Builder;
- Contextual CTA.

---

# 15. PERSONA-CD — Conversion

Primary:

```text
Casting Invitation
```

Secondary:

```text
Questionnaire Download
Custom PDF
Request Materials
```

---

# 16. PERSONA-CD — Success Criterion

Persona считается успешно обслуженной, если за пять минут или менее она может:

1. определить первичное соответствие;
2. проверить Evidence;
3. получить подходящую Questionnaire;
4. выполнить профессиональное действие.

---

# 17. PERSONA-CA — Casting Assistant / Researcher

## 17.1. Рабочее имя

**Даниил**

---

## 17.2. Профессиональная роль

Помогает Casting Director:

- искать кандидатов;
- проводить первичный screening;
- собирать shortlist;
- проверять требования;
- комплектовать материалы для внутреннего review.

---

# 18. PERSONA-CA — Основное отличие

Casting Assistant обычно рассматривает **большее количество кандидатов**, чем Casting Director.

Поэтому для него особенно критичны:

```text
Speed
Structure
Consistency
Comparability
```

---

# 19. PERSONA-CA — Основная задача

> Проверить базовые требования, быстро собрать необходимые сведения и передать кандидата дальше в удобном формате.

---

# 20. PERSONA-CA — Time Budget

Типично:

```text
20–40 секунд — первичный screening
1–3 минуты — проверка фактов
до 5 минут — PDF / shortlist action
```

---

# 21. PERSONA-CA — Устройство

Практически всегда:

- desktop;
- notebook;
- несколько вкладок одновременно.

---

# 22. PERSONA-CA — Ключевые вопросы

1. Есть ли необходимый язык?
2. Есть ли конкретный навык?
3. Есть ли релевантный проект?
4. Есть ли Full Body / Close-Up?
5. Есть ли Video?
6. Можно ли быстро скачать краткую анкету?
7. Можно ли передать её Casting Director без дополнительных пояснений?

---

# 23. PERSONA-CA — P0 Information

- structured professional facts;
- Skills;
- Languages;
- Portfolio filters;
- Selected Projects;
- Video;
- Compact Questionnaire.

---

# 24. PERSONA-CA — Основной CTA

```text
Скачать компактную анкету
```

Secondary:

```text
Найти в профиле
Настроить анкету
```

---

# 25. PERSONA-CA — Pain Points

### CA-P01

Нужно читать большой текст ради одного навыка.

### CA-P02

Нет быстрого search.

### CA-P03

Фотографии невозможно фильтровать.

### CA-P04

Проекты не структурированы.

### CA-P05

PDF плохо подходит для shortlist.

### CA-P06

Непонятно, какие данные актуальны.

---

# 26. PERSONA-CA — Critical Capabilities

- Casting Quick View;
- Public Search;
- structured Skills;
- Languages;
- Portfolio Filters;
- Compact Questionnaire;
- Public Builder.

---

# 27. PERSONA-CA — Success Criterion

Casting Assistant должен иметь возможность без чтения полной Biography:

1. найти нужный профессиональный факт;
2. проверить визуальные материалы;
3. получить краткий share-ready PDF.

---

# 28. PERSONA-CP — Commercial Casting Producer

## 28.1. Рабочее имя

**Мария**

---

## 28.2. Профессиональная роль

Занимается подбором исполнителей:

- рекламы;
- digital campaigns;
- брендовых роликов;
- lifestyle video;
- коммерческих съёмок.

---

# 29. PERSONA-CP — Особенности поведения

Для коммерческого кастинга часто особенно важны:

- актуальная внешность;
- естественность;
- camera presence;
- эмоциональность;
- быстрый контакт.

Professional credits могут иметь меньший приоритет, чем для кино.

---

# 30. PERSONA-CP — Основная задача

> Быстро понять визуальный типаж, увидеть эмоциональный диапазон и решить, стоит ли связываться по конкретному коммерческому проекту.

---

# 31. PERSONA-CP — Устройство

Очень часто:

```text
Smartphone
```

Также:

- tablet;
- desktop.

---

# 32. PERSONA-CP — Источник перехода

Частые Entry Points:

- Instagram;
- TikTok;
- recommendation;
- messenger;
- shared portfolio link.

---

# 33. PERSONA-CP — Time Budget

```text
5–15 секунд — visual impression
30–60 секунд — Portfolio
1–2 минуты — Emotional / Video
до 5 минут — Contact
```

---

# 34. PERSONA-CP — P0 Information

- Current Close-Up;
- Full Body;
- Appearance photos;
- Shooting Date;
- Emotional Grid;
- short Video;
- location;
- Contact.

---

# 35. PERSONA-CP — Primary CTA

В зависимости от контекста:

```text
Предложить роль
Пригласить на кастинг
Предложить сотрудничество
```

---

# 36. PERSONA-CP — Pain Points

### CP-P01

Mobile layout неудобен.

### CP-P02

Фотографии слишком старые, но дата неизвестна.

### CP-P03

Эмоции распределены по большому числу отдельных фотографий.

### CP-P04

Видео плохо запускается на smartphone.

### CP-P05

Контакт сложно найти.

### CP-P06

Для короткого коммерческого кастинга предлагается огромный PDF.

---

# 37. PERSONA-CP — Trust Signals

- Shooting Date;
- сохранение естественной внешности;
- качественный responsive Portfolio;
- Emotional Grid;
- короткое Video Intro;
- понятный CTA.

---

# 38. PERSONA-CP — Critical Capabilities

- Mobile-first Public UX;
- Portfolio;
- Shooting Date;
- Emotional Grid;
- Video;
- Contextual CTA;
- Quick/Custom Questionnaire.

---

# 39. PERSONA-CP — Success Criterion

Persona должна выполнить весь профессиональный Journey со смартфона без необходимости переходить на desktop.

---

# 40. PERSONA-DIR — Director / Producer

## 40.1. Рабочее имя

**Алексей**

---

## 40.2. Профессиональная роль

Может быть:

- режиссёром;
- креативным продюсером;
- executive producer;
- project producer.

---

# 41. PERSONA-DIR — Основное отличие

Эта Persona меньше ориентирована на checkbox-проверку параметров и больше — на профессиональный диапазон.

Ключевой вопрос:

> «Что она может дать роли?»

---

# 42. PERSONA-DIR — Основная задача

> Понять актёрскую индивидуальность, эмоциональный диапазон, характер предыдущих ролей и потенциал для конкретного проекта.

---

# 43. PERSONA-DIR — Устройство

Часто:

- notebook;
- tablet.

Иногда:

- smartphone.

---

# 44. PERSONA-DIR — Time Budget

Первичная оценка может быть быстрой, но Persona MAY провести на сайте больше пяти минут.

При этом профессиональное решение о том, стоит ли продолжать, должно быть возможно получить быстро.

---

# 45. PERSONA-DIR — P0 Information

- Video Intro;
- Showreel;
- Emotional Grid;
- Selected Projects;
- Roles;
- key professional photos.

---

# 46. PERSONA-DIR — P1 Information

- Training;
- Achievements;
- detailed Project pages;
- additional video;
- Role-specific photos.

---

# 47. PERSONA-DIR — Primary CTA

```text
Запросить дополнительные материалы
```

или:

```text
Пригласить на кастинг
```

---

# 48. PERSONA-DIR — Pain Points

### DIR-P01

Профиль выглядит как сухое резюме без ощущения актёрского диапазона.

### DIR-P02

Слишком много Projects без приоритета.

### DIR-P03

Невозможно быстро открыть Role-specific evidence.

### DIR-P04

Emotional Range отсутствует или спрятан.

### DIR-P05

Все видео смешаны без понятной структуры.

---

# 49. PERSONA-DIR — Critical Capabilities

- Video Intro;
- Showreel;
- Emotional Portfolio;
- Emotional Grid;
- Featured Projects;
- Role Detail;
- Additional Materials CTA.

---

# 50. PERSONA-DIR — Success Criterion

Persona должна за короткий первый визит:

1. понять профессиональный характер актрисы;
2. увидеть диапазон;
3. открыть релевантный опыт;
4. выполнить следующий профессиональный шаг.

---

# 51. PERSONA-INT — International Casting Specialist

## 51.1. Рабочее имя

**Sophie**

---

## 51.2. Профессиональная роль

Может быть:

- International Casting Coordinator;
- Casting Producer;
- Talent Researcher;
- production coordinator.

Работает в международной production environment.

---

# 52. PERSONA-INT — Основная задача

> Быстро определить, подходит ли кандидат по языкам, location и профессиональному опыту, а затем получить share-ready материал на нужном языке.

---

# 53. PERSONA-INT — Основная проблема

Если профессиональный материал:

- только на русском;
- имеет непонятные сокращения;
- не содержит location;
- не содержит language levels;
- имеет PDF без английской версии;

кандидат может быть сложнее для дальнейшего рассмотрения.

---

# 54. PERSONA-INT — Time Budget

```text
15–30 секунд — language/context recognition
1–3 минуты — profile/evidence
до 5 минут — localized PDF
```

---

# 55. PERSONA-INT — Устройство

Основное:

- desktop;
- notebook.

---

# 56. PERSONA-INT — P0 Information

- English/selected language UI;
- location/base;
- Languages;
- Language Levels;
- Video;
- Full Body;
- Close-Up;
- relevant Skills;
- English Questionnaire.

---

# 57. PERSONA-INT — Primary CTA

```text
Download English Questionnaire
```

Secondary:

```text
Contact
Build Custom Questionnaire
```

---

# 58. PERSONA-INT — Pain Points

### INT-P01

Language switch сбрасывает текущую страницу.

### INT-P02

Часть данных локализована, а часть остаётся непонятной.

### INT-P03

PDF существует только на одном языке.

### INT-P04

Language Skill не имеет уровня.

### INT-P05

Location unclear.

### INT-P06

Professional terminology inconsistent.

---

# 59. PERSONA-INT — Trust Signals

- consistent English terminology;
- clear location;
- structured CEFR;
- localized PDF;
- official profile link;
- Generated Date.

---

# 60. PERSONA-INT — Critical Capabilities

- multilingual state;
- Language Switch;
- CEFR;
- localized Questionnaires;
- Public Builder language;
- international-friendly PDF.

---

# 61. PERSONA-INT — Success Criterion

International Specialist должен получить полноценный professional Journey без необходимости понимать русский язык.

---

# 62. PERSONA-VOP — Virtual Portfolio Operator

## 62.1. Название

**Virtual Portfolio Operator**

Короткое название:

```text
Virtual Operator
```

---

# 63. PERSONA-VOP — Тип

Это не человек и не публичный chatbot.

Это системная внутренняя Persona, используемая для проектирования автоматизации.

---

# 64. PERSONA-VOP — Основная задача

> Минимизировать ручную рутинную работу администратора, сохраняя человека в контуре фактических и публикационных решений.

---

# 65. PERSONA-VOP — Operating Model

Virtual Operator может:

```text
Observe
Analyze
Detect
Suggest
Draft
Validate
Recalculate
Notify
```

Не может самостоятельно:

```text
Invent Facts
Publish
Delete Important Content
Accept Casting
Send Professional Commitments
Alter Appearance
```

---

# 66. PERSONA-VOP — Основные источники данных

Оператор работает с:

- Media Library;
- Profile completeness;
- Portfolio;
- Emotional Portfolio;
- Projects;
- Training;
- Questionnaires;
- Links;
- Feedback;
- Castings;
- Analytics.

Доступ должен ограничиваться задачей.

---

# 67. PERSONA-VOP — Главные сценарии

### VOP-S01 — New Media

Обнаружить новые Media Assets и предложить classification.

### VOP-S02 — Missing Data

Обнаружить обязательные незаполненные данные.

### VOP-S03 — Broken Link

Найти неработающий External Link.

### VOP-S04 — Stale Derived Asset

Определить, что PDF/Grid Preview требует пересборки.

### VOP-S05 — Content Freshness

Предложить проверить материал на актуальность.

### VOP-S06 — Builder Analytics

Показать, какие blocks наиболее востребованы.

### VOP-S07 — Casting Deadline

Выделить приближающийся deadline.

---

# 68. PERSONA-VOP — Desired Outcome

Не:

```text
Я всё сделал вместо пользователя.
```

А:

```text
У пользователя остались только решения,
требующие человеческого внимания.
```

---

# 69. PERSONA-VOP — Primary Interface

Основной интерфейс:

```text
Admin Dashboard
```

Дополнительно:

- contextual suggestions;
- readiness panel;
- entity warnings.

---

# 70. PERSONA-VOP — Pain Points системы, которые он должен устранять

### VOP-P01

Повторный ввод данных.

### VOP-P02

Необходимость вручную проверять все ссылки.

### VOP-P03

Ручной поиск stale PDFs.

### VOP-P04

Ручное определение missing metadata.

### VOP-P05

Ручная классификация очевидных media items.

### VOP-P06

Потерянные deadlines.

### VOP-P07

Отсутствие связи между analytics и actionable recommendations.

---

# 71. PERSONA-VOP — Human Approval Boundary

Human Approval обязателен минимум для:

- новой professional classification, если она меняет domain meaning;
- Profile Fact;
- Emotional Grid confirmation;
- publication;
- AI Draft application where factual meaning changes;
- Casting decisions;
- Theme Publication.

---

# 72. PERSONA-VOP — Success Criterion

Virtual Operator успешен, если:

1. сокращает количество ручных проверок;
2. не создаёт недостоверные данные;
3. показывает actionable exceptions;
4. не мешает ручному workflow;
5. не становится single point of failure;
6. оставляет понятный Audit Trail.

---

# 73. Сравнение шести персон

| Persona | Главная цель | Скорость | Главный материал | Основное действие |
|---|---|---|---|---|
| Casting Director | Решить, приглашать ли | Очень высокая | Quick View + Evidence | Invite to Casting |
| Casting Assistant | Отфильтровать кандидата | Максимальная | Structured Facts | Compact PDF |
| Commercial Producer | Оценить типаж | Очень высокая | Visual + Emotional | Offer / Casting |
| Director / Producer | Понять диапазон | Средняя | Video + Roles | Request Materials |
| International Specialist | Получить usable international profile | Высокая | Language + PDF | Download / Contact |
| Virtual Operator | Снизить ручную работу | Постоянно | Domain State | Suggest / Alert |

---

# 74. Persona Decision Authority

## Casting Director

Может обладать высокой decision authority.

## Casting Assistant

Обычно:

```text
Research / Recommend
```

а не окончательное решение.

## Commercial Producer

Может:

- самостоятельно принимать решение;
- либо формировать shortlist.

## Director / Producer

Часто имеет высокий creative decision influence.

## International Specialist

Может быть coordinator или gatekeeper.

## Virtual Operator

Не имеет authority принимать профессиональные бизнес-решения.

---

# 75. Persona Information Density

Разным Persona требуется разная плотность данных.

```text
Casting Assistant
→ maximum structured density

Casting Director
→ balanced structured evidence

Commercial Producer
→ visual-first

Director
→ depth and context

International
→ structured + localized

Virtual Operator
→ exception-first
```

---

# 76. Persona Navigation Preferences

## CD

Quick View → Evidence → Questionnaire.

## CA

Search → Facts → PDF.

## CP

Visual → Emotional → Video → CTA.

## DIR

Video → Emotional → Project/Role → Materials.

## INT

Language → Facts → Localized Questionnaire.

## VOP

Dashboard → Exception → Resolution.

---

# 77. Persona-Specific CTA Priority

| Persona | Primary CTA | Secondary CTA |
|---|---|---|
| CD | Invite to Casting | Questionnaire |
| CA | Download Compact PDF | Search |
| CP | Offer Role / Casting | Contact |
| DIR | Request Materials | Casting |
| INT | Download Localized PDF | Contact |
| VOP | Resolve Exception | Review Suggestion |

---

# 78. Persona-Specific Trust Signals

## Casting Director

- accuracy;
- current media;
- clear evidence.

## Casting Assistant

- consistency;
- structured facts;
- fast retrieval.

## Commercial Producer

- current visual material;
- natural appearance;
- date.

## Director

- professional depth;
- role evidence;
- emotional authenticity.

## International

- localization;
- clear terminology;
- CEFR;
- professional PDF.

## Virtual Operator

- explainability;
- auditability;
- reversible actions.

---

# 79. Общие Anti-Persona Scenarios

Продукт не должен оптимизироваться преимущественно под следующие типы поведения.

---

# 80. ANTI-PERSONA-01 — Casual Entertainment Visitor

Посетитель, который:

- просто листает фотографии;
- не имеет professional intent.

Такой трафик допустим, но не определяет основной UX.

---

# 81. ANTI-PERSONA-02 — Anonymous Data Harvester

Пользователь, пытающийся массово получать:

- Contacts;
- PDFs;
- private data.

Продукт не обязан обеспечивать удобство такого поведения.

Должны работать Anti-Abuse ограничения.

---

# 82. ANTI-PERSONA-03 — Unauthenticated Admin

Public user, пытающийся получить административную функциональность.

Такой сценарий блокируется Access Control.

---

# 83. ANTI-PERSONA-04 — Autonomous AI

ИИ не рассматривается как пользователь, имеющий право самостоятельно менять business facts.

---

# 84. Secondary Persona — Actress / Profile Owner

Хотя Profile Owner не входит в шесть Persona 6-3-5, её интересы должны учитываться.

Основные цели:

- профессионально представить себя;
- контролировать достоверность;
- контролировать приватность;
- использовать результат для получения ролей.

---

# 85. Secondary Persona — Administrator

Главная задача:

```text
Manage Exceptions
Maintain Accuracy
Publish Safely
Process Opportunities
```

Администратор не должен быть оператором ручного дублирования данных.

---

# 86. Secondary Persona — Editor

Может работать с:

- content;
- drafts;
- metadata;

но доступ к publication определяется Access Control.

---

# 87. Персона и роль — не одно и то же

`Role` в Security Model определяет права.

`Persona` определяет:

- поведение;
- мотивацию;
- информационные потребности.

Например два человека с одинаковым Public access могут быть:

- Casting Director;
- Director.

Но UX expectations будут различаться.

---

# 88. Persona Selection for Feature Design

Каждая новая Public Feature должна указать:

```text
Primary Persona
Secondary Personas
Non-Target Personas
```

---

# 89. Persona Priority

Если UX-требования конфликтуют:

приоритет должен зависеть от профессиональной ценности capability.

Для основной публичной страницы рекомендуется учитывать:

```text
1. Casting Director
2. Casting Assistant
3. Commercial Producer
4. Director / Producer
5. International Specialist
```

Это не означает, что International Persona менее важна.

Language Accessibility должна быть полноценной для неё.

---

# 90. Persona Coverage Matrix

Каждый ключевой capability должен иметь Persona coverage.

| Capability | CD | CA | CP | DIR | INT | VOP |
|---|---:|---:|---:|---:|---:|---:|
| Quick View | ✓ | ✓ | ✓ | ✓ | ✓ |  |
| Portfolio | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Emotional Grid | ✓ |  | ✓ | ✓ | ✓ | ✓ |
| Search | ✓ | ✓ |  | ✓ | ✓ |  |
| Questionnaire | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Public Builder | ✓ | ✓ | ✓ | ✓ | ✓ |  |
| Casting CTA | ✓ |  | ✓ | ✓ | ✓ |  |
| Localization | ✓ | ✓ | ✓ | ✓ | ✓ |  |
| Dashboard |  |  |  |  |  | ✓ |
| AI Assistance |  |  |  |  |  | ✓ |

---

# 91. Persona Coverage Requirement

Критический Public capability SHOULD обслуживать минимум одну основную Persona напрямую.

Если функция не имеет Persona, её необходимость должна быть дополнительно обоснована.

---

# 92. Persona-Based Design Question

Для каждого экрана необходимо задать:

> «Какая из Persona может открыть этот экран напрямую и что она должна понять за первые несколько секунд?»

---

# 93. Persona-Based Information Priority

Информационная архитектура страницы должна учитывать:

- Primary Persona;
- Primary Goal;
- Decision Stage.

Например Project Detail для Director может давать больше depth, чем Quick View для Casting Assistant.

---

# 94. Persona-Based Performance

Performance priority зависит от поведения Persona.

Особенно критичны:

- mobile image loading для Commercial Producer;
- fast structured rendering для Casting Assistant;
- fast Video startup для Director/Casting Director;
- Builder response time для всех professional visitors.

---

# 95. Persona-Based Accessibility

Persona нельзя предполагать полностью зрячей, использующей мышь или desktop.

Критические Journeys должны быть доступны с assistive technology.

---

# 96. Persona-Based Internationalization

International Persona не должна рассматриваться как отдельный «урезанный» сайт.

Она проходит ту же профессиональную модель на другом языке.

---

# 97. Persona-Based Testing

Каждая основная Persona должна иметь отдельный:

```text
CJM
User Flow Set
E2E Customer Journey Test
```

---

# 98. Persona Test IDs

Рекомендуется:

```text
E2E-CJM-CD-001
E2E-CJM-CA-001
E2E-CJM-CP-001
E2E-CJM-DIR-001
E2E-CJM-INT-001
E2E-CJM-VOP-001
```

---

# 99. Persona Success Metrics

## Casting Director

- Casting CTA;
- Questionnaire conversion;
- time to professional action.

## Casting Assistant

- search success;
- Compact PDF generation;
- time to fact.

## Commercial Producer

- mobile engagement;
- Emotional Grid;
- professional CTA.

## Director

- Video engagement;
- Project/Role depth;
- Request Materials.

## International

- language switch;
- localized PDF;
- successful contact.

## Virtual Operator

- reduced unresolved exceptions;
- reduced manual checks;
- suggestion acceptance where appropriate.

---

# 100. Persona Conflict Resolution Example

Если один UX предлагает:

```text
показывать все Projects сразу
```

Director может получить depth, но Casting Assistant — перегрузку.

Правильное решение:

```text
Featured / Relevant Projects
→ View All
```

Таким образом используется Progressive Disclosure вместо выбора одной Persona за счёт другой.

---

# 101. Persona Conflict Resolution Principle

При конфликте предпочтительно искать:

```text
Shared Efficient Default
+
Progressive Detail
```

а не создавать полностью разные сайты для каждой Persona.

---

# 102. Personalization Boundary

Persona Model MAY влиять на:

- layout priorities;
- Entry Page;
- suggested CTA.

Но продукт не должен скрыто классифицировать конкретного человека как определённую Persona и собирать избыточные данные без необходимости.

---

# 103. Persona Model и Public Builder

Public Builder особенно важен потому, что разные Persona имеют разные document needs.

Например:

### CA

Quick.

### CD

Standard/Casting.

### CP

Visual-heavy compact.

### DIR

Projects + Emotional + Video.

### INT

Localized Extended/Casting.

Поэтому один universal PDF противоречит Persona Model.

---

# 104. Persona Model и Marketing

Marketing Campaign должен вести к наиболее релевантному контексту Persona.

Например:

```text
visual social campaign
→ Portfolio / Emotional

professional referral
→ Quick View

casting outreach
→ Questionnaire
```

---

# 105. Persona Model и Virtual Operator

Virtual Operator должен учитывать, какие изменения влияют на ключевые Persona Journeys.

Например broken Video Intro link является более высоким приоритетом, чем отсутствие второстепенного description у архивного Project.

---

# 106. Priority of Exceptions

Оператор SHOULD ранжировать проблемы по:

```text
Impact on Professional Journey
×
Urgency
×
Business Importance
```

---

# 107. Persona Model и Customer Journey 6-3-5

Настоящий документ определяет пользователей.

Следующий документ `method-6-3-5.md` должен взять все шесть Persona и для каждой:

1. смоделировать пять минут реального использования;
2. получить три уникальных продуктовых вывода;
3. определить точки friction;
4. связать выводы с будущими UX requirements.

---

# 108. Definition of Persona Completeness

Persona считается достаточно определённой, если известны:

- professional role;
- context;
- goal;
- device;
- time budget;
- decision authority;
- P0 information;
- pain points;
- trust signals;
- CTA;
- conversion;
- success criterion.

---

# 109. Definition of Persona Coverage

Целевая Persona Model считается полной для текущего Product Scope, если:

1. покрыт первичный Casting Decision;
2. покрыт high-volume shortlist;
3. покрыт Commercial/Mobile path;
4. покрыт Creative Director path;
5. покрыт International path;
6. покрыт Internal Automation path.

Все шесть категорий настоящим документом покрыты.

---

# 110. Итоговая модель

```text
                    PROFESSIONAL PUBLIC USERS

       Casting Director
              │
       Casting Assistant
              │
     Commercial Producer
              │
      Director / Producer
              │
 International Specialist
              │
              ▼
      PUBLIC PROFESSIONAL UX
              │
              ▼
      QUESTIONNAIRE / CTA
              │
              ▼
      PROFESSIONAL OUTCOME


                    INTERNAL AUTOMATION

        Domain Data
            │
            ▼
    Virtual Portfolio Operator
            │
            ▼
     Observation / Suggestion
            │
            ▼
       Human Decision
```

---

# 111. Финальный принцип Persona Model

> **Публичный сайт должен быть достаточно быстрым для Casting Assistant, достаточно информативным для Casting Director, достаточно визуальным для Commercial Producer, достаточно содержательным для Director, достаточно международным для International Casting Specialist и достаточно автоматизированным для Virtual Operator — при этом сохраняя одну общую достоверную информационную модель.**