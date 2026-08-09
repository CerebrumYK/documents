# CASTING ASSISTANT JOURNEY

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Подробный Customer Journey ассистента по кастингу / Casting Researcher

**Целевой файл:** `docs/customer-journey/casting-assistant.md`  
**Документ:** DOC-024  
**Статус:** ✅ Completed  
**Тип:** Customer Journey

**Persona:** `PERSONA-CA — Casting Assistant / Casting Researcher`  
**Рабочее имя Persona:** Даниил

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

---

# 1. Назначение документа

Настоящий документ описывает полный Customer Journey Casting Assistant / Casting Researcher.

Основная задача Persona отличается от Casting Director:

> **не принимать окончательное творческое решение, а максимально быстро проверить большое количество кандидатов по формальным и профессиональным критериям, собрать shortlist и передать качественно структурированные материалы следующему лицу принятия решения.**

Документ является нормативным источником для:

- Casting Quick View;
- Public Search;
- Structured Professional Facts;
- Portfolio Filters;
- Project filtering;
- Compact Questionnaire;
- Public Questionnaire Builder;
- PDF generation;
- localization;
- analytics;
- Customer Journey Tests.

---

# 2. Journey ID

Основной Journey:

```text id="ca-main"
CJM-CA-001
```

Название:

```text id="ca-title"
Casting Assistant — High-Volume Screening to Shortlist Package
```

---

# 3. Persona Summary

## Persona

Casting Assistant / Casting Researcher.

## Основная роль

Поддерживает Casting Director и production team в:

- поиске кандидатов;
- первичной квалификации;
- проверке критериев;
- подготовке shortlist;
- сборе профессиональных материалов.

---

# 4. Decision Authority

Casting Assistant обычно имеет:

```text
Research Authority
+
Screening Authority
+
Recommendation Authority
```

но не обязательно:

```text
Final Casting Authority
```

Поэтому продукт должен помогать не только оценить кандидата, но и **передать результат оценки дальше**.

---

# 5. Основная профессиональная задача

> Проверить, соответствует ли актриса заданным требованиям, и сформировать короткий, структурированный и пересылаемый профессиональный пакет без необходимости вручную собирать информацию из нескольких источников.

---

# 6. Типичный рабочий контекст

Casting Assistant может:

- одновременно работать с 10–100+ кандидатами;
- иметь таблицу требований;
- переключаться между большим количеством вкладок;
- копировать ссылки во внутреннюю систему;
- сравнивать профили;
- создавать shortlist;
- пересылать PDF Casting Director.

Для этой Persona критична не только полнота данных, но и **предсказуемость структуры**.

---

# 7. Основная проблема Persona

Типичная неэффективная модель:

```text
Открыть профиль
→ читать Biography
→ искать возраст/язык
→ искать фотографии
→ искать Showreel
→ искать PDF
→ вручную выписать данные
→ сохранить ссылку
→ передать Casting Director
```

Целевая модель:

```text
Open
→ Search / Quick Facts
→ Verify
→ Compact PDF
→ Shortlist
```

---

# 8. Desired Outcome

Ключевой результат:

```text
Candidate Screened
+
Relevant Facts Verified
+
Share-Ready Package Obtained
```

После этого кандидат может быть:

```text
Recommend for Shortlist
Reject from Current Casting
Needs Further Review
```

Эти решения не обязательно фиксируются публичным сайтом, но продукт должен дать достаточно данных для их принятия.

---

# 9. Business Outcome

Основные профессиональные результаты:

```text
Compact Questionnaire Download
Custom Shortlist PDF
Profile Shared
Candidate Advanced to Casting Director
```

В перспективе может учитываться downstream conversion:

```text
Casting Invitation
```

но она может быть выполнена уже другой Persona.

---

# 10. Time Budget

Casting Assistant имеет наиболее жёсткий time budget среди внешних Persona.

Целевой ориентир:

```text
00:00–00:10
Recognition

00:10–00:40
Formal Qualification

00:40–01:30
Visual Verification

01:30–02:30
Experience / Video

02:30–03:30
Questionnaire

03:30–05:00
Customize / Export / Share
```

Первичное решение должно быть возможно значительно быстрее пяти минут.

---

# 11. Primary Device

Основной сценарий:

```text
Desktop / Notebook
```

Типичны:

- большой экран;
- много вкладок;
- keyboard;
- copy/paste;
- параллельная таблица или casting system.

Mobile остаётся поддерживаемым, но не является первичным рабочим устройством Persona.

---

# 12. Typical Entry Sources

```text
CA-ENTRY-01 Search Engine
CA-ENTRY-02 Casting Director Link
CA-ENTRY-03 Internal Shortlist
CA-ENTRY-04 Messenger
CA-ENTRY-05 Spreadsheet / CRM Link
CA-ENTRY-06 Shared Questionnaire
CA-ENTRY-07 Project Link
CA-ENTRY-08 Social Profile
```

---

# 13. Primary Mental Model

Casting Assistant думает не:

> «Расскажите мне историю актрисы».

А:

```text
Does she meet requirement A?
Does she meet requirement B?
Can I prove it?
Can I package it?
Can I send it?
```

---

# 14. Основной Journey

```text
Entry
 ↓
Recognition
 ↓
Structured Qualification
 ↓
Search Specific Requirement
 ↓
Visual Verification
 ↓
Experience Verification
 ↓
Compact Questionnaire
 ↓
Customize if Required
 ↓
Export / Share
```

---

# 15. Stage CA-01 — Entry

**Время:** `00:00`

Пользователь открывает профиль, часто одновременно с другими кандидатами.

Система не должна требовать:

- intro animation;
- modal;
- registration;
- предварительного просмотра storytelling content.

---

# 16. Stage CA-02 — Recognition

**Время:** `00:00–00:10`

Должны быть сразу видны:

- Actress Name;
- Primary Close-Up;
- Professional Identity;
- Location/Base;
- key Languages;
- quick access to Questionnaire.

---

# 17. Recognition Success Condition

Пользователь должен за несколько секунд подтвердить:

```text
Correct Candidate
+
Correct Professional Profile
```

и перейти непосредственно к проверке требований.

---

# 18. Stage CA-03 — Structured Qualification

**Время:** `00:10–00:40`

Casting Assistant проверяет набор критериев из casting brief.

Например:

```text
English B2+
Driving
Swimming
Theatre Experience
Location: Almaty
```

---

# 19. Required Structured Data

Особенно важны:

- Location;
- Languages;
- Language Level;
- Skills;
- Skill Level;
- professional parameters;
- relevant Project Types;
- Training where explicitly required.

---

# 20. Structured Data Principle

Данные должны быть:

```text
Scannable
Searchable
Categorized
Consistently Named
```

Narrative Biography не может быть единственным источником.

---

# 21. Quick Facts Block

Рекомендуемый logical presentation:

```text
Location
Languages
Key Skills
Professional Parameters
Selected Experience
```

Quick Facts является Projection, а не новой копией Master Data.

---

# 22. Stage CA-04 — Search Specific Requirement

Один из центральных сценариев Persona.

Пользователь может искать:

```text
English
Horse riding
Singing
Driving
Dance
Theatre
```

---

# 23. Public Search Requirement

Search должен:

1. работать по структурированным публичным данным;
2. быстро возвращать результат;
3. показывать category;
4. указывать значение;
5. позволять перейти к source section.

---

# 24. Example Search Result

```text
Запрос: English

Языки
English — B2

Связанный материал:
Video Intro — EN
```

Если соответствующие данные публично разрешены.

---

# 25. Search Empty State

Если ничего не найдено:

```text
По опубликованным данным совпадений нет.
```

Нельзя автоматически интерпретировать это как:

```text
Навыка точно нет.
```

Отсутствие опубликованной информации и отсутствие профессионального навыка — не одно и то же.

---

# 26. Search Privacy

Search MUST NOT показывать:

- unpublished Skill;
- private Project;
- hidden Contact;
- Admin Note;
- Casting data;
- AI assumptions.

---

# 27. Search Precision

Нужно отличать:

```text
structured exact match
```

от:

```text
textual mention
```

Например упоминание автомобиля в Project Description не означает наличие `Driving Skill`.

---

# 28. Stage CA-05 — Visual Verification

**Время:** примерно `00:40–01:30`

После formal match пользователь проверяет визуальное соответствие.

Основные фотографии:

- Close-Up;
- Full Body;
- Profile;
- Three Quarter;
- additional relevant views.

---

# 29. Portfolio Filtering

Portfolio Filters являются критической функцией этой Persona.

Нужно обеспечить:

```text
All
Full Body
Close-Up
Waist
Profile
Three Quarter
Staged
Appearance
Additional
```

---

# 30. Filter Behaviour

Filter SHOULD:

- применять selection сразу;
- сохранять текущий context;
- не выполнять full page navigation без необходимости;
- быть keyboard accessible;
- работать на mobile.

---

# 31. Primary Photos

`Primary Full Body` и `Primary Close-Up` должны быть доступны без фильтрации.

Filters нужны для deeper verification.

---

# 32. Photography Date

Если дата известна, она должна быть доступна без необходимости открывать Admin metadata.

Дата помогает определить актуальность внешнего вида.

---

# 33. No Artificial Appearance

Для Screening важно, чтобы изображения не вводили пользователя в заблуждение генеративным изменением внешности.

---

# 34. Stage CA-06 — Experience Verification

**Время:** примерно `01:30–02:15`

Assistant проверяет только релевантный опыт.

Нежелательная модель:

```text
45 Projects
→ inspect each manually
```

Целевая:

```text
Featured
+
Search/Filter
+
Structured Roles
```

---

# 35. Relevant Project Information

Для быстрого screening нужны:

- Project Title;
- Type;
- Role;
- date/year where relevant;
- relevant media;
- short description.

---

# 36. Project Search

Если Casting требует:

```text
theatre experience
```

пользователь должен быстро найти соответствующие Projects/Roles.

---

# 37. Role Evidence

Role-specific photos и media должны быть доступны непосредственно из Project/Role context.

---

# 38. Stage CA-07 — Video Verification

Video может использоваться:

- для подтверждения camera presence;
- речи;
- языка;
- professional presence.

Assistant обычно не должен просматривать длинный Showreel полностью для каждого кандидата.

Поэтому Primary Video Intro особенно важна.

---

# 39. Video Priority

Для этой Persona:

```text
Video Intro
>
Long Showreel
```

на этапе первичного screening.

Showreel остаётся доступным для дополнительной проверки.

---

# 40. Stage CA-08 — Questionnaire

**Время:** примерно `02:15–03:00`

После screening пользователь хочет получить структурированный документ для передачи дальше.

Основной вариант:

```text
Compact Questionnaire
```

---

# 41. Compact Questionnaire Purpose

Compact Questionnaire должна отвечать на задачу:

> «Могу ли я переслать этот документ Casting Director и дать ему достаточно информации для следующего решения?»

---

# 42. Compact Questionnaire Suggested Content

Минимально:

```text
Actress Identity
Close-Up
Full Body
Location
Professional Parameters
Languages
Key Skills
Video Link / QR
Selected Experience
Primary Contact where allowed
Official Profile Link
```

Конкретный состав определяется Questionnaire Specification.

---

# 43. Compact Means Relevant, Not Tiny

Compact Questionnaire не должна сокращаться за счёт критических фактов.

Цель:

```text
High Information Density
+
Low Noise
```

---

# 44. One-Click Compact PDF

Если Primary Questionnaire соответствует Compact use case, путь SHOULD приближаться к:

```text
Profile
→ Download Compact PDF
```

---

# 45. Download Picker Behaviour

Если доступно несколько вариантов:

```text
Compact
Extended
Casting
```

Compact должна быть ясно обозначена как вариант для быстрого просмотра.

---

# 46. Stage CA-09 — Public Builder Decision

Если Compact Questionnaire не подходит конкретному brief, Casting Assistant должен иметь возможность адаптировать содержание.

---

# 47. Typical Builder Use Cases

## CA-BLD-01

Добавить конкретный Skill.

## CA-BLD-02

Добавить один релевантный Project.

## CA-BLD-03

Убрать Training.

## CA-BLD-04

Добавить Language details.

## CA-BLD-05

Оставить только две фотографии.

## CA-BLD-06

Добавить Video / QR.

---

# 48. Preferred Builder Entry

Для Persona оптимален сценарий:

```text
Compact Questionnaire
→ Customize
```

а не:

```text
Empty Builder
→ Build Everything
```

---

# 49. Builder Template Priority

Для Casting Assistant наиболее релевантен:

```text
Quick Template
```

или:

```text
Compact Prepared Questionnaire as Starting Point
```

---

# 50. Builder Selection Density

Desktop UI MAY использовать более плотное представление, чем Public Mobile flow.

Например:

```text
☑ Languages
  ☑ English B2
  ☐ Russian

☑ Skills
  ☑ Driving
  ☑ Swimming
```

Точный UX определяется позднее.

---

# 51. Builder Item Limits

Template SHOULD иметь разумные limits, чтобы пользователь случайно не превратил Compact PDF в Extended.

Например:

- selected Projects;
- Additional Photos;
- Training.

Конкретные limits задаются Template Configuration.

---

# 52. Builder Revalidation

Перед generation система обязана повторно проверить каждый item.

Client-side selection не является authorization.

---

# 53. Builder Session

Public Questionnaire Session является temporary configuration.

Она не должна загрязнять список Prepared Questionnaires в Admin.

---

# 54. Stage CA-10 — Preview

Перед экспортом пользователь должен быстро увидеть:

- выбранные блоки;
- порядок;
- количество страниц ориентировочно;
- основные изображения.

---

# 55. Preview Purpose

Preview нужен не для pixel-perfect design editing.

Он отвечает на вопрос:

> «В документе именно те материалы, которые мне нужны?»

---

# 56. Stage CA-11 — Export

Формируется:

```text
Share-Ready PDF
```

---

# 57. Share-Ready Requirement

PDF должен сохранять смысл после:

- скачивания;
- отправки в messenger;
- загрузки во внутреннюю систему;
- пересылки email;
- распечатки.

---

# 58. Required PDF Identity

Минимально:

- Actress Name;
- Professional Identity;
- Generated Date;
- Official Profile URL.

---

# 59. QR Value for Casting Assistant

QR особенно полезен, когда PDF:

- распечатан;
- открыт на другом устройстве;
- пересылается production team.

QR должен вести на разрешённый stable public URL.

---

# 60. Stage CA-12 — Internal Share

Сам сайт не обязан управлять внутренним shortlist Casting Director.

Но PDF и URL должны быть пригодны для передачи.

---

# 61. Share Actions

Public UI MAY поддерживать:

```text
Copy Link
Download PDF
```

Share API устройства MAY использоваться там, где подходит.

---

# 62. No Mandatory Account

Casting Assistant не должен создавать аккаунт, чтобы:

- искать публичный Skill;
- фильтровать Portfolio;
- открыть Questionnaire;
- сформировать разрешённый Custom PDF.

---

# 63. Main Happy Path CJM-CA-001

```text
Casting Director sends candidate link
 ↓
Profile opens
 ↓
Quick Facts
 ↓
Search "English"
 ↓
English B2 confirmed
 ↓
Filter Full Body
 ↓
Filter Profile
 ↓
Open relevant Project
 ↓
Start Video Intro
 ↓
Open Compact Questionnaire
 ↓
Download PDF
 ↓
Send to Casting Director
```

---

# 64. Custom Shortlist Path CJM-CA-002

```text
Profile
 ↓
Search required Skills
 ↓
Visual verification
 ↓
Compact Questionnaire
 ↓
Customize
 ↓
Add Project
 ↓
Remove irrelevant blocks
 ↓
Generate PDF
 ↓
Share
```

---

# 65. Search-First Path CJM-CA-003

```text
Search Engine
 ↓
Relevant Project / Profile
 ↓
Search Professional Facts
 ↓
Quick View
 ↓
Compact PDF
```

---

# 66. Questionnaire-First Path CJM-CA-004

```text
Shared Questionnaire
 ↓
Review Summary
 ↓
Open Current Profile
 ↓
Verify Facts
 ↓
Download
```

---

# 67. Mobile Fallback Journey CJM-CA-M01

Although desktop is primary:

```text
Mobile Link
 ↓
Quick Facts
 ↓
Search
 ↓
Primary Photos
 ↓
Compact Questionnaire
 ↓
Download / Share
```

must remain usable.

---

# 68. Multi-Tab Behaviour

Desktop screening often involves multiple candidate tabs.

Therefore:

- Page Title SHOULD clearly identify actress;
- favicon/site identity SHOULD remain stable;
- primary facts SHOULD not depend on transient UI state;
- direct URLs SHOULD remain stable.

---

# 69. Back/Forward Navigation

Filters and search SHOULD behave predictably with browser navigation where practical.

User should not lose useful context unnecessarily.

---

# 70. Deep Link to Search Result

Architecture MAY allow linking directly to a relevant section, but a Search Result itself does not become a new domain entity.

---

# 71. Friction CA-FRC-01 — Narrative Facts

### Problem

Skill exists only in Biography.

### Impact

Manual reading per candidate.

### Resolution

Structured Skill.

---

# 72. Friction CA-FRC-02 — Unfilterable Portfolio

### Problem

All photos are one large gallery.

### Impact

High screening time.

### Resolution

Professional Portfolio Categories.

---

# 73. Friction CA-FRC-03 — Inconsistent Naming

Например:

```text
английский средний
English Intermediate
B2
```

в разных views.

### Resolution

SSOT + canonical terminology.

---

# 74. Friction CA-FRC-04 — Overloaded Questionnaire

### Problem

15-page document where only first 2 pages matter.

### Resolution

Compact Questionnaire + Builder.

---

# 75. Friction CA-FRC-05 — Missing Current Date

### Problem

Unknown photo freshness.

### Resolution

Shooting Date where known.

---

# 76. Friction CA-FRC-06 — Video Buried in Media

### Resolution

Primary Video direct action.

---

# 77. Friction CA-FRC-07 — Repeated Verification

Если PDF и Profile показывают разные current facts, Assistant должен перепроверять.

Resolution:

```text
SSOT
+
Snapshot Date
```

---

# 78. Friction CA-FRC-08 — Builder Too Complex

Если Builder выглядит как document editor, пользователь теряет время.

Resolution:

```text
Template
→ Select Content
→ Generate
```

---

# 79. Friction CA-FRC-09 — Hidden Download

Download action MUST быть явным.

---

# 80. Friction CA-FRC-10 — Registration

Mandatory account creation является неприемлемым friction для базового workflow.

---

# 81. Trust Signals

Для Persona важны:

```text
Consistent structured data
Clear categories
Shooting Date
Official Questionnaire
Generated Date
Stable Profile URL
Working Video
Professional PDF
```

---

# 82. Trust Through Consistency

Casting Assistant сравнивает кандидатов.

Поэтому consistent presentation особенно важно.

Например Language SHOULD всегда отображаться в одинаковом формате.

---

# 83. Trust Through Provenance

Если generated PDF является custom selection:

MAY быть указано:

```text
Сформировано на официальном сайте актрисы
```

с official URL.

---

# 84. Information Priority — P0

```text
Name
Primary Close-Up
Primary Full Body
Location
Languages
Skills
Professional Parameters
Compact Questionnaire
```

---

# 85. Information Priority — P1

```text
Video Intro
Relevant Projects
Roles
Portfolio Filters
```

---

# 86. Information Priority — P2

```text
Emotional Grid
Training
Achievements
Additional Media
```

Значимость P2 может повышаться в конкретном Casting.

---

# 87. Information Priority — P3

```text
Full Biography
Blog
Full Archive
Extended Narrative
```

---

# 88. Search and Public Builder Relationship

Search отвечает:

> «Есть ли в опубликованном профиле нужная информация?»

Builder отвечает:

> «Добавить ли эту информацию в документ?»

Это отдельные действия.

---

# 89. Search Result to Builder

Если Public Builder Session active, MAY поддерживаться:

```text
Search Skill
→ Add to Questionnaire
```

только если Skill разрешён Public Builder.

---

# 90. Portfolio to Builder

Аналогично MAY поддерживаться:

```text
Portfolio Item
→ Add to current questionnaire
```

с соблюдением permissions.

---

# 91. Project to Builder

```text
Relevant Project
→ Add
```

не должен изменять Project или Prepared Questionnaire.

---

# 92. Floating Builder Indicator

Если пользователь вышел из Builder для просмотра профиля, MAY отображаться компактный indicator:

```text
Анкета: 8 выбранных элементов
```

с action:

```text
Продолжить
```

---

# 93. Builder Context Persistence

При навигации по публичным разрешённым страницам текущая Public Builder Session SHOULD сохраняться до TTL.

---

# 94. Empty State — No Relevant Project

Если поиском Project не найден:

```text
Подходящих опубликованных проектов не найдено.
```

Нельзя создавать AI-suggested imaginary project.

---

# 95. Missing Skill Semantics

Если Skill не найден:

правильно:

```text
Навык не указан в опубликованном профиле.
```

неправильно:

```text
Актриса не владеет навыком.
```

---

# 96. Localized Screening

Casting Assistant может работать в international production.

Поэтому structured fields должны быть локализуемыми без изменения фактического значения.

---

# 97. CEFR Advantage

Для Language screening:

```text
English — B2
```

значительно полезнее неоднозначного:

```text
English — Good
```

---

# 98. Analytics Events

Рекомендуемые logical events:

```text
casting_assistant_profile_viewed
professional_search_started
professional_search_result_opened
portfolio_filter_used
project_opened
video_started
compact_questionnaire_viewed
compact_questionnaire_downloaded
public_builder_opened
public_builder_item_selected
public_builder_pdf_generated
profile_link_copied
```

---

# 99. Analytics Privacy

Search analytics MAY хранить aggregate query categories.

Не требуется создавать постоянный behavioural profile конкретного Casting Assistant.

---

# 100. KPI — Time to Fact

Один из важнейших KPI Persona.

Определение:

```text
Entry / Search Start
→ Required Structured Fact Located
```

---

# 101. KPI — Screening Completion Time

Ориентировочное время от Profile View до профессионально достаточного результата.

---

# 102. KPI — Compact Questionnaire Usage

Показывает пригодность Prepared Compact Questionnaire.

---

# 103. KPI — Customization Rate

Если практически каждый Assistant изменяет Compact Questionnaire, defaults могут быть недостаточно удачными.

Это сигнал для human product review.

---

# 104. KPI — Search Success Rate

Доля поисков, приводящих к полезному structured result.

---

# 105. KPI — Zero Result Query Categories

Агрегированные zero-result категории могут выявить:

- missing structured data;
- terminology mismatch;
- реальные отсутствующие данные.

Virtual Operator MAY использовать это как recommendation input.

---

# 106. Virtual Operator Support

Для Casting Assistant Journey особенно важны проверки:

- missing Primary Full Body;
- missing Primary Close-Up;
- incomplete Skill metadata;
- broken Video;
- stale Questionnaire;
- invalid Language Level;
- missing Portfolio category;
- broken official link.

---

# 107. Virtual Operator Recommendation Example

Если analytics показывает много Search queries:

```text
horse riding
```

и profile содержит этот навык только в narrative Project Description, оператор MAY предложить:

```text
Проверить, следует ли добавить структурированный Skill.
```

Но не добавляет его автоматически.

---

# 108. Relevant 6-3-5 Insights

Основные:

```text
INS-CA-01 Structured Quick Facts
INS-CA-02 Portfolio Filters
INS-CA-03 Compact PDF
```

Дополнительные:

```text
INS-CD-02 Search Inside Profile
INS-CP-01 Mobile-First
INS-INT-01 Global Language State
INS-INT-03 Localized PDF
INS-VOP-01 Propagation
```

---

# 109. Relevant Business Rules

Критичные:

```text
BR-PRF-002
BR-POR-004
BR-POR-005
BR-POR-006
BR-POR-012

BR-QST-001–020
BR-PQB-001–030

BR-CJM-001
BR-CJM-002
BR-CJM-003
BR-CJM-005
BR-CJM-006
BR-CJM-009
BR-CJM-010
```

---

# 110. Relevant Product Principles

```text
PP-001 Single Source of Truth
PP-005 Minimum Actions
PP-006 Progressive Disclosure
PP-010 Public Is Not a Copy of Admin
PP-017 Start From a Useful Default
PP-018 Content Selection, Not Document Design
PP-050 Search Uses Structured Data
PP-051 Search Respects Visibility
PP-053 Five-Minute Professional Journey
PP-054 Quick Decision in Seconds
PP-064 Business Outcome Over Vanity Metrics
```

---

# 111. Future User Flows

Из этого документа должны быть выведены минимум:

```text
UF-PUB-CA-001 Open Candidate Profile
UF-PUB-CA-002 Review Quick Facts
UF-PUB-CA-003 Search Skill
UF-PUB-CA-004 Search Language
UF-PUB-CA-005 Filter Portfolio
UF-PUB-CA-006 Review Relevant Project
UF-PUB-CA-007 Start Video
UF-QST-CA-001 Open Compact Questionnaire
UF-QST-CA-002 Download Compact PDF
UF-PQB-CA-001 Customize Compact Questionnaire
UF-PQB-CA-002 Add Specific Item
UF-PQB-CA-003 Generate Shortlist PDF
UF-PUB-CA-008 Copy Profile Link
```

---

# 112. Functional Requirement Areas

```text
FR-PROFILE
FR-SEARCH
FR-PORTFOLIO
FR-SKILLS
FR-LANGUAGES
FR-PROJECTS
FR-VIDEO
FR-QUESTIONNAIRE
FR-PQB
FR-PDF
FR-ANALYTICS
```

---

# 113. E2E-CJM-CA-001 — Standard Screening

```text
Given
a published actor profile with structured data

When
Casting Assistant opens it

Then
they can:
- confirm identity
- locate Language
- locate Skill
- filter Full Body
- filter Profile photo
- open relevant Project
- start Video Intro
- open Compact Questionnaire
- download share-ready PDF
```

---

# 114. E2E-CJM-CA-002 — Search

```text
Given
English B2 is publicly available

When
user searches "English"

Then
result shows Language = English, Level = B2

And
does not expose any private data
```

---

# 115. E2E-CJM-CA-003 — Missing Skill

```text
Given
a Skill is not published

When
user searches for it

Then
system reports no published matching data

And
does not claim the actress lacks the skill
```

---

# 116. E2E-CJM-CA-004 — Portfolio Filters

Test:

```text
Close-Up filter
→ only matching public Portfolio Items

Full Body filter
→ only matching public Portfolio Items
```

---

# 117. E2E-CJM-CA-005 — Compact PDF

Проверить:

- correct identity;
- required images;
- structured facts;
- working links;
- official profile URL;
- no unauthorized Contacts.

---

# 118. E2E-CJM-CA-006 — Builder Tampering

Client attempts to include unauthorized Training/Project.

Expected:

```text
Server rejects or excludes according to policy
```

No private content appears in PDF.

---

# 119. E2E-CJM-CA-007 — Language Consistency

Structured Language value must match between:

- Public Profile;
- Compact Questionnaire;
- newly generated Public Builder PDF;

except historical immutable snapshot contexts.

---

# 120. E2E-CJM-CA-008 — Broken Video

Broken Video must not prevent:

- Search;
- Portfolio;
- Questionnaire download.

---

# 121. Journey Score Target

| Criterion | Target |
|---|---:|
| Recognition | 2 |
| Findability | 2 |
| Evidence | 2 |
| Questionnaire Efficiency | 2 |
| Export / Share | 2 |

Target:

```text
10/10
```

Minimum acceptable:

```text
9/10
```

---

# 122. Hard Failure Conditions

Journey должен быть переработан, если:

1. Skill нельзя найти иначе чем через Biography.
2. Full Body нельзя быстро открыть.
3. Compact Questionnaire отсутствует без адекватной альтернативы.
4. Public Builder начинается с пустой страницы и требует ручного построения структуры.
5. PDF нельзя безопасно переслать.
6. Private fields присутствуют в client payload.
7. Search делает вывод об отсутствии навыка по отсутствию данных.
8. Language terminology inconsistent.
9. пользователь обязан регистрироваться.
10. основной screening реалистично требует десятков ручных действий.

---

# 123. High-Volume Screening Principle

UI должен учитывать, что пользователь не посвящает весь рабочий день одному профилю.

Поэтому приоритет:

```text
Fast Recognition
Fast Fact Verification
Fast Visual Verification
Fast Export
```

---

# 124. Comparability Principle

Хотя сайт не является marketplace и не сравнивает актрису с другими кандидатами, структура информации должна быть достаточно профессионально стандартной, чтобы Casting Assistant мог самостоятельно проводить сравнение.

---

# 125. No Peer Comparison Feature

Сам продукт MUST NOT вводить:

- рейтинг актрис;
- сравнение внешности;
- ranking против других кандидатов.

Comparability достигается структурой данных, а не встроенным соревнованием.

---

# 126. Information Compression Principle

Компактность достигается:

- структурой;
- filters;
- selection;
- concise labels;

а не уменьшением font до нечитаемого размера или удалением critical context.

---

# 127. Search Before Browse Principle

Для high-volume user возможность найти конкретный критерий зачастую эффективнее полного просмотра раздела.

Search должен дополнять Navigation, а не заменять её полностью.

---

# 128. Default Before Custom Principle

Если стандартный Compact PDF подходит, пользователь должен получить его быстрее, чем custom PDF.

Public Builder — дополнительный capability, а не обязательная ступень.

---

# 129. Customization Without Ownership

Casting Assistant может настроить собственный document representation, но:

- не редактирует Profile;
- не исправляет Master Data;
- не меняет Prepared Questionnaire;
- не меняет visibility.

---

# 130. Feedback on Incorrect Data

Если в будущем продукт предоставляет механизм сообщения о возможной ошибке, он должен создавать Feedback/Review request, а не разрешать Public User редактировать факт.

Не является обязательным отдельным capability текущего Journey.

---

# 131. Shortlist Handoff Principle

После завершения Journey следующий пользователь — часто Casting Director.

Поэтому output Persona должен быть понятен **без участия Casting Assistant**.

---

# 132. Share-Ready Content Principle

Ключевые handoff assets:

```text
Stable Public Profile URL
Prepared Questionnaire URL
PDF
```

должны сохранять professional identity и context.

---

# 133. Final Mental Model

```text
FIND
 ↓
VERIFY
 ↓
PACKAGE
 ↓
HAND OFF
```

---

# 134. Product Response to Mental Model

```text
FIND
→ Search + Quick Facts

VERIFY
→ Portfolio + Projects + Video

PACKAGE
→ Compact Questionnaire + Builder

HAND OFF
→ Share-Ready PDF + Stable Link
```

---

# 135. Definition of Journey Compliance

Public UX соответствует `CJM-CA-001`, если Casting Assistant может:

1. идентифицировать актрису за секунды;
2. быстро найти structured Language;
3. быстро найти structured Skill;
4. открыть нужную Portfolio category;
5. проверить релевантный Project/Role;
6. быстро открыть Video Intro;
7. получить Compact Questionnaire;
8. при необходимости адаптировать её из разрешённых данных;
9. сформировать share-ready PDF;
10. не видеть private data;
11. не регистрироваться;
12. завершить типовой screening в пределах нескольких минут.

---

# 136. Итоговая схема

```text
CASTING ASSISTANT

Casting Brief / Candidate Link
            ↓
      QUICK RECOGNITION
            ↓
      STRUCTURED FACTS
            ↓
           SEARCH
     ┌──────┴──────┐
     │             │
  Skills        Languages
     │             │
     └──────┬──────┘
            ↓
    PORTFOLIO FILTERS
            ↓
      RELEVANT PROJECT
            ↓
        VIDEO INTRO
            ↓
  COMPACT QUESTIONNAIRE
            ↓
       Is sufficient?
        /          \
      YES           NO
       │             │
   Download      Customize
       │             │
       └──────┬──────┘
              ↓
       SHARE-READY PDF
              ↓
       CASTING DIRECTOR
```

---

# 137. Финальный принцип

> **Casting Assistant Journey должен превращать профессиональный профиль в инструмент быстрого screening: нужный факт находится за секунды, нужный визуальный материал — без пролистывания всего архива, а результат проверки можно передать следующему участнику кастинга в компактном и достоверном формате без ручной сборки информации.**