# UX PRINCIPLES

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативные принципы пользовательского опыта

**Целевой файл:** `docs/ux/ux-principles.md`  
**Документ:** DOC-040  
**Статус:** ✅ Completed  
**Тип:** UX Foundation

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
- `docs/customer-journey/director.md`
- `docs/customer-journey/international-casting.md`
- `docs/customer-journey/virtual-operator.md`
- `docs/customer-journey/conversion-funnel.md`
- `docs/customer-journey/automation-matrix.md`

---

# 1. Назначение документа

Настоящий документ определяет обязательные UX-принципы целевого продукта.

Он является нормативным основанием для:

- Public Navigation;
- Admin Navigation;
- Public User Flows;
- Admin User Flows;
- Public Questionnaire Builder;
- Forms;
- Validation;
- UI States;
- Content Hierarchy;
- Responsive Behaviour;
- Accessibility;
- Frontend implementation;
- UX Acceptance Testing.

Документ описывает **как продукт должен ощущаться и вести себя для пользователя**, не определяя конкретную визуальную тему, CSS framework или component library.

---

# 2. UX Definition

В рамках продукта UX означает не только визуальное оформление.

UX включает:

```text
Information Findability
+
Interaction Cost
+
Decision Clarity
+
System Feedback
+
Professional Trust
+
Accessibility
+
Performance Perception
+
Error Recovery
```

---

# 3. UX Goal

Главная задача публичного UX:

> **дать профессиональному пользователю максимально короткий и понятный путь от первого открытия страницы до профессионально значимой информации и действия.**

Главная задача Admin UX:

> **позволить управлять сложной профессиональной системой через понятные рабочие состояния, исключения и безопасные действия без повторного ввода данных.**

---

# 4. UX Principle IDs

Принципы имеют идентификаторы:

```text
UXP-001
UXP-002
...
```

Они должны использоваться в:

- UX Specifications;
- Functional Requirements;
- Acceptance Criteria;
- Issues;
- Tests;
- Design Review.

---

# 5. UXP-001 — Professional Task First

Каждый экран MUST проектироваться вокруг профессиональной задачи пользователя.

Не вокруг:

- структуры базы данных;
- существующих backend modules;
- количества доступных entities;
- желания показать весь контент сразу.

Основной вопрос:

> «Что пользователь должен понять или сделать на этом экране?»

---

# 6. UXP-002 — Professional Signal Before Decoration

Профессиональная информация имеет приоритет над декоративным storytelling.

На критическом первом экране раньше декоративных элементов должны быть доступны:

- Actor Identity;
- primary visual;
- key facts;
- Video;
- Questionnaire;
- Professional CTA.

---

# 7. UXP-003 — Seconds to Recognition

Public Entry Point должен позволять понять за несколько секунд:

```text
WHO?
WHAT?
RELEVANT?
WHAT NEXT?
```

---

# 8. UXP-004 — Five-Minute Professional Journey

Типовой профессиональный пользователь SHOULD иметь возможность:

```text
Recognize
→ Qualify
→ Review Evidence
→ Get Questionnaire
→ Act
```

в пределах пяти минут.

---

# 9. UXP-005 — Do Not Force Full Exploration

Пользователь не обязан открывать:

- Biography;
- Training;
- all Projects;
- Blog;
- all Achievements;

для выполнения основного профессионального действия.

---

# 10. UXP-006 — Progressive Disclosure

Информация организуется слоями:

```text
Immediate Decision Data
        ↓
Professional Evidence
        ↓
Detailed Context
        ↓
Archive
```

---

# 11. UXP-007 — Scannable Before Readable

Критические professional facts должны сначала хорошо сканироваться взглядом.

Затем пользователь может перейти к подробному тексту.

Использовать:

- labels;
- concise values;
- groups;
- hierarchy;
- whitespace.

Не использовать длинные prose blocks как единственный способ передачи structured facts.

---

# 12. UXP-008 — One Concept, One Visual Meaning

Одинаковые типы сущностей и состояний должны иметь последовательное визуальное поведение.

Например:

- Draft;
- Published;
- Archived;
- Warning;
- Error;
- Private.

Не должны выглядеть случайным образом по-разному в разных модулях.

---

# 13. UXP-009 — Domain Terminology Must Be Consistent

UX MUST использовать canonical terminology из `terminology.md`.

Не использовать взаимозаменяемо:

```text
Project
Casting
Portfolio
Emotional Portfolio
Questionnaire
```

если это разные понятия.

---

# 14. UXP-010 — User Language, Not Internal Schema

Интерфейс не должен показывать пользователю внутренние технические термины, если существует профессионально понятный label.

Неправильно:

```text
portfolio_item_type = fullbody
```

Правильно:

```text
Фото в полный рост
```

---

# 15. UXP-011 — Navigation Follows User Intent

Public Navigation строится по профессиональным задачам и содержанию.

Admin Navigation — по domain management.

Эти структуры не обязаны совпадать.

---

# 16. UXP-012 — No Dead Ends

Любая ключевая Public Page SHOULD предлагать логичное продолжение.

Например:

```text
Portfolio
→ Video / Questionnaire / Contact

Project
→ Questionnaire / Request Materials

Questionnaire
→ Download / Customize / Contact
```

---

# 17. UXP-013 — Deep Link Is a First-Class Entry

Любая индексируемая или shareable professional page должна быть понятна при прямом открытии.

Она SHOULD содержать:

- Actor Identity;
- current context;
- navigation;
- key CTA.

---

# 18. UXP-014 — Context Should Survive Navigation

Где уместно, система SHOULD сохранять:

- selected locale;
- Public Builder Session;
- attribution context;
- selected filters;
- return context.

---

# 19. UXP-015 — Browser Behaviour Must Remain Predictable

Back/Forward navigation SHOULD вести себя ожидаемо.

Filters, search и modal states не должны делать браузерную навигацию хаотичной.

---

# 20. UXP-016 — Stable URLs for Important Public Content

Professional pages SHOULD иметь stable, shareable URLs.

UI state не должен зависеть исключительно от ephemeral client state, если страницу предполагается пересылать.

---

# 21. UXP-017 — Primary CTA Must Be Obvious

Критический экран SHOULD иметь один визуально приоритетный Primary CTA.

Дополнительные действия должны быть вторичными.

---

# 22. UXP-018 — CTA Must Describe Intent

Предпочтительно:

```text
Пригласить на кастинг
Запросить материалы
Скачать анкету
```

вместо:

```text
Продолжить
Подробнее
Отправить
```

там, где действие можно назвать точно.

---

# 23. UXP-019 — CTA Contextuality

CTA SHOULD соответствовать странице и намерению.

Пример:

- Project → Request Materials;
- Questionnaire → Invite to Casting;
- Contacts → Contact;
- Commercial visual flow → Offer Role / Casting.

---

# 24. UXP-020 — CTA Does Not Imply Acceptance

Кнопка:

```text
Предложить роль
```

означает отправку professional inquiry.

Она MUST NOT создавать впечатление, что роль автоматически принята.

---

# 25. UXP-021 — Sticky Actions May Reduce Friction

На Mobile MAY использоваться компактный sticky action layer:

```text
Видео
Анкета
Связаться
```

если он:

- не закрывает content;
- не мешает forms;
- не нарушает accessibility.

---

# 26. UXP-022 — One Screen, One Primary Decision

Экран не должен визуально требовать от пользователя принять пять одинаково приоритетных решений одновременно.

---

# 27. UXP-023 — Quick Facts Must Be Structured

Ключевые:

- Location;
- Languages;
- Skills;
- Parameters;

отображаются структурировано.

---

# 28. UXP-024 — Missing Data Must Be Honest

Если fact отсутствует:

```text
Не указано
```

предпочтительнее:

- догадки;
- AI-generated значения;
- скрытая подмена другим полем.

---

# 29. UXP-025 — Absence of Data ≠ Negative Fact

Если Skill не опубликован:

правильно:

```text
Навык не указан в опубликованном профиле.
```

неправильно:

```text
Навыком не владеет.
```

---

# 30. UXP-026 — Public Search Is Professional Search

Search должен помогать найти:

- Languages;
- Skills;
- Projects;
- Roles;
- relevant professional content.

Не требуется превращать сайт в универсальную full-text search system.

---

# 31. UXP-027 — Search Result Must Explain Match

Search result SHOULD показывать:

- category;
- matched value;
- source section.

---

# 32. UXP-028 — Search Must Respect Privacy

Private/draft content не должен даже появляться как «скрытый result count».

---

# 33. UXP-029 — Filters Must Reflect Professional Categories

Portfolio Filters должны использовать профессиональные категории:

```text
Full Body
Close-Up
Profile
Three-Quarter
...
```

---

# 34. UXP-030 — Primary Photos Must Not Require Filtering

Primary Full Body и Primary Close-Up должны быть легко доступны даже без использования filters.

---

# 35. UXP-031 — Image Presentation Must Preserve Professional Meaning

UI cropping MAY использовать derived representation.

Original Media остаётся неизменным.

---

# 36. UXP-032 — No Misleading Appearance Transformation

UX не должен показывать synthetic appearance как реальный professional material.

---

# 37. UXP-033 — Date Can Be a Trust Signal

Shooting Date SHOULD отображаться там, где она профессионально полезна.

Особенно:

- Portfolio shooting;
- Emotional Portfolio.

---

# 38. UXP-034 — Emotional Grid Before Emotional Overload

Для быстрого просмотра Emotional Range SHOULD сначала использоваться Emotional Grid.

Полный Emotional Portfolio — следующий слой.

---

# 39. UXP-035 — Video Intro Is High Priority

Video Intro должен быть доступен без глубокого перехода через общий Media Library.

---

# 40. UXP-036 — Video Types Must Be Distinguishable

Минимально различать:

```text
Video Intro
Showreel
Project/Role Video
```

---

# 41. UXP-037 — Do Not Autoplay Unexpected Audio

Public pages SHOULD NOT неожиданно запускать звук.

---

# 42. UXP-038 — Media Failure Must Be Local

Broken Video не должен превращать весь экран в failure state.

---

# 43. UXP-039 — Featured Before Archive

Для Projects:

```text
Featured / Selected
→ View All
```

предпочтительнее полного списка по умолчанию.

---

# 44. UXP-040 — Role Context Must Remain Visible

Role-specific media должно сохранять контекст:

```text
Project
→ Role
→ Media
```

---

# 45. UXP-041 — Prepared Questionnaire Is the Default Professional Path

Пользователь сначала получает готовую рекомендуемую версию.

Public Builder не должен быть обязательным.

---

# 46. UXP-042 — Builder Is Customization, Not Document Creation From Zero

Preferred path:

```text
Prepared Questionnaire
→ Customize
```

или:

```text
Template
→ Adjust
```

---

# 47. UXP-043 — Builder Starts Useful

Template defaults должны давать usable document ещё до изменения пользователем.

---

# 48. UXP-044 — Content Selection, Not Layout Editing

Публичный Builder позволяет выбрать:

- blocks;
- items;
- content.

Не:

- fonts;
- margins;
- exact page breaks;
- arbitrary colors;
- document grids.

---

# 49. UXP-045 — Builder Must Explain Current Selection

Пользователь должен понимать:

- что включено;
- что исключено;
- какой Template активен;
- какой язык;
- какой output будет создан.

---

# 50. UXP-046 — Builder Selection Must Be Reversible

Enable/disable действия должны легко отменяться до generation.

---

# 51. UXP-047 — Builder State Must Be Persistent Within Session

Переход к разрешённому public content SHOULD не уничтожать selection.

---

# 52. UXP-048 — Builder Session Must Be Visible

Если пользователь временно вышел из Builder, MAY показываться ненавязчивый indicator:

```text
Анкета: 8 элементов
[Продолжить]
```

---

# 53. UXP-049 — Builder Preview Is Content Verification

Preview отвечает на вопрос:

> «Выбраны ли правильные данные?»

а не предоставляет полноценный graphic design editor.

---

# 54. UXP-050 — Builder Does Not Expose Unauthorized Choices

Недоступные items не должны:

- показываться disabled с private names;
- загружаться в client;
- быть selectable через modified request.

---

# 55. UXP-051 — PDF Action Must Be Obvious

Пользователь должен быстро понимать:

- где открыть;
- где скачать;
- какой язык;
- какой вариант.

---

# 56. UXP-052 — Download Picker Must Clarify Differences

Если вариантов несколько:

```text
Compact
Extended
Casting
```

UI SHOULD кратко объяснять отличие.

---

# 57. UXP-053 — Share-Ready Output

Generated PDF должен восприниматься как законченный professional document.

---

# 58. UXP-054 — Hyperlink and QR Complement Each Other

В PDF там, где QR включён:

- URL остаётся кликабельным;
- QR предоставляет альтернативный способ доступа.

---

# 59. UXP-055 — QR Must Have Human-Understandable Context

Рядом с QR SHOULD быть понятно, что он открывает.

Например:

```text
Видеовизитка
```

---

# 60. UXP-056 — Public Builder Must Work Without Registration

No registration wall before basic Builder/PDF workflow.

---

# 61. UXP-057 — Forms Must Ask Only Necessary Information

Professional Contact Form не должна превращаться в CRM questionnaire.

Первый контакт должен быть коротким.

---

# 62. UXP-058 — Preserve User Input on Validation Error

Если form validation fails, пользователь не должен повторно вводить корректные поля.

---

# 63. UXP-059 — Validation Must Be Near the Problem

Ошибка отображается рядом с соответствующим field или ясно связывается с ним.

---

# 64. UXP-060 — Validation Must Explain Fix

Неправильно:

```text
Invalid value.
```

Лучше:

```text
Введите номер телефона в международном формате.
```

если это фактическое требование field.

---

# 65. UXP-061 — Errors, Warnings and Information Must Differ

UI MUST различать:

```text
Error
Warning
Information
```

как по semantics, так и визуально.

---

# 66. UXP-062 — Blocking Error Must Explain Why

Если Publish недоступен:

```text
Невозможно опубликовать: не выбрано основное фото в полный рост.
```

лучше, чем disabled button без explanation.

---

# 67. UXP-063 — Success Must Confirm Business Result

После Feedback:

```text
Обращение принято. №123
```

значительно полезнее generic:

```text
Success
```

---

# 68. UXP-064 — Transaction Success Must Be Based on Persistence

Если Feedback сохранён, UI может показать success даже при отдельной проблеме WhatsApp notification.

---

# 69. UXP-065 — Loading State Must Preserve Context

Loading не должен приводить к исчезновению всего уже загруженного экрана без необходимости.

---

# 70. UXP-066 — Skeletons/Placeholders Must Not Mislead

Loading representation не должна выглядеть как реальные data values.

---

# 71. UXP-067 — Empty State Must Be Informative

Например:

```text
Опубликованные материалы этого типа пока отсутствуют.
```

вместо пустой белой области.

---

# 72. UXP-068 — Empty State Must Not Invent Content

AI не используется для визуального заполнения отсутствующего professional content без явной separate proposal flow.

---

# 73. UXP-069 — Retry Must Be Available for Recoverable Failures

Например:

- PDF generation;
- external media;
- upload.

---

# 74. UXP-070 — Retry Must Be Safe

Повтор submit не должен создавать duplicate business records.

---

# 75. UXP-071 — Destructive Action Must Be Explicit

Удаление должно ясно показывать:

- что удаляется;
- последствия;
- dependencies where relevant.

---

# 76. UXP-072 — Archive Preferred Over Delete

Если domain поддерживает Archive, UX SHOULD предлагать его как основной способ убрать исторически значимый content из активного использования.

---

# 77. UXP-073 — Destructive Confirmation Must Match Impact

Не нужно использовать сложные modal confirmation для неопасных действий.

Но важные irreversible/destructive operations требуют ясного подтверждения.

---

# 78. UXP-074 — Undo Where Reasonable

Для reversible UI/content operations SHOULD предоставляться easy undo или безопасный revision path.

---

# 79. UXP-075 — Draft and Published Must Be Visually Distinct

Admin должен всегда понимать, редактирует ли он:

- Draft;
- current Published;
- Archived;
- historical Revision.

---

# 80. UXP-076 — Publish Is Explicit

Нельзя создавать UX, где Save автоматически означает Publish без явного business rule.

---

# 81. UXP-077 — Save and Publish Are Different Concepts

Recommended mental model:

```text
Save Draft
Review
Publish
```

---

# 82. UXP-078 — Readiness Is Visible Before Publish

Admin должен видеть:

- blocking errors;
- warnings;
- ready status.

---

# 83. UXP-079 — Revision Context Must Be Visible

При открытии historical Questionnaire/Theme revision UI должен явно показывать, что пользователь смотрит прошлую версию.

---

# 84. UXP-080 — Historical Content Must Not Look Editable

Immutable historical revision не должна визуально выглядеть как обычная editable current form.

---

# 85. UXP-081 — AI Output Must Be Visually Distinguishable

Admin должен понимать, что текст или recommendation создан AI.

---

# 86. UXP-082 — AI Must Show Source Context Where Relevant

Для Casting AI, BB Assistant и Virtual Operator SHOULD быть понятно, на каких данных основан результат.

---

# 87. UXP-083 — AI Recommendation Must Not Look Like Confirmed Fact

Пример:

```text
AI suggestion: вероятно, фото в полный рост
```

не должно выглядеть как уже сохранённая Portfolio category.

---

# 88. UXP-084 — AI Apply Requires Explicit Action

Использовать:

```text
Применить
```

а не auto-insert.

---

# 89. UXP-085 — Apply ≠ Publish

После Apply AI Draft становится editable content.

Publish остаётся отдельным действием.

---

# 90. UXP-086 — AI Must Degrade Gracefully

Если AI unavailable:

- form/editor остаётся;
- manual workflow остаётся;
- AI area показывает понятное состояние.

---

# 91. UXP-087 — Virtual Operator Is Exception-First

Admin Dashboard должен показывать:

```text
What requires attention
```

а не просто inventory.

---

# 92. UXP-088 — Operational Priority Must Be Visible

Admin должен различать:

- critical;
- high;
- normal;
- low.

Но severity не должна использоваться декоративно без business meaning.

---

# 93. UXP-089 — Operator Alert Must Be Actionable

Каждый alert SHOULD содержать:

- issue;
- entity;
- impact;
- recommended action;
- direct path to resolve.

---

# 94. UXP-090 — Alerts Should Be Grouped

Например:

```text
12 новых фото требуют классификации
```

лучше 12 отдельных alerts.

---

# 95. UXP-091 — System Actions Should Be Transparent

Admin SHOULD иметь возможность увидеть:

```text
4 производных изображения обновлены автоматически.
```

без необходимости подтверждать безопасную рутину.

---

# 96. UXP-092 — Admin Should Not Need to Visit Every Module

Dashboard + contextual navigation должны обеспечивать exception-driven workflow.

---

# 97. UXP-093 — Admin Forms Should Reuse Existing Data

Если entity уже известна системе, UI не должен просить повторный ввод того же факта без причины.

---

# 98. UXP-094 — Relationships Should Be Selected, Not Duplicated

Например в Questionnaire:

пользователь выбирает существующий Portfolio Item.

Он не должен повторно загружать то же фото.

---

# 99. UXP-095 — Current Context Must Be Clear

Admin должен понимать, какую entity он редактирует, особенно в nested contexts:

```text
Project
→ Role
→ Media
```

---

# 100. UXP-096 — Breadcrumbs Where Hierarchy Is Deep

Admin/deep public structures MAY использовать breadcrumbs, если они реально помогают orientation.

---

# 101. UXP-097 — Modal Is Not Default Navigation

Крупные editing workflows не следует помещать в modal только ради compact UI.

Modal подходит для:

- confirmation;
- lightweight selection;
- quick preview.

---

# 102. UXP-098 — Long Tasks Need Stepwise Structure

Например Public Builder на mobile:

```text
Template
→ Content
→ Preview
→ Generate
```

лучше одного огромного form.

---

# 103. UXP-099 — Step Count Must Be Justified

Wizard не должен существовать только ради wizard.

Если задача выполняется проще на одном экране — использовать один экран.

---

# 104. UXP-100 — Default Values Must Be Safe and Useful

Defaults SHOULD:

- ускорять workflow;
- не раскрывать private data;
- не создавать false professional claims.

---

# 105. UXP-101 — Default Visibility Must Be Conservative

Sensitive Contact или private content не должны становиться public из-за default UI state.

---

# 106. UXP-102 — Checkbox Semantics Must Be Explicit

Особенно для независимых visibility contexts.

Не один generic:

```text
Показывать
```

а отдельно:

```text
Показывать на сайте
Разрешать в подготовленных анкетах
Разрешать в публичном конструкторе
```

---

# 107. UXP-103 — Accessibility Is Baseline UX

Все critical flows проектируются доступными с самого начала.

Не как отдельный polish phase.

---

# 108. UXP-104 — Keyboard Complete

Все критические действия MUST быть выполнимы без мыши.

---

# 109. UXP-105 — Focus Must Be Visible

Keyboard focus не должен скрываться theme styling.

---

# 110. UXP-106 — Semantic Controls

Clickable div не должен заменять button/link без правильной semantics.

---

# 111. UXP-107 — Form Labels Must Be Programmatically Associated

Placeholder не является заменой label.

---

# 112. UXP-108 — Error Identification Must Be Accessible

Ошибки должны быть доступны assistive technologies и визуально понятны.

---

# 113. UXP-109 — Color Alone Is Insufficient

Error/Warning/Status не определяются только цветом.

Использовать:

- icon;
- text;
- label;
- semantics.

---

# 114. UXP-110 — Motion Must Be Non-Essential

Критический professional information path не должен зависеть от animation.

---

# 115. UXP-111 — Reduced Motion Must Be Respected

При соответствующей user preference decorative motion SHOULD сокращаться/отключаться.

---

# 116. UXP-112 — Contrast Is a Publication Gate for Themes

Theme не должна становиться Published, если critical text/controls не соответствуют установленным accessibility rules.

---

# 117. UXP-113 — Responsive Is Contextual

Responsive UX — не просто уменьшение desktop layout.

Каждый breakpoint учитывает:

- content priority;
- touch;
- input method;
- available viewport.

---

# 118. UXP-114 — Mobile Is First-Class for Public Critical Paths

Mobile MUST поддерживать:

- Portfolio;
- Video;
- Emotional Grid;
- Questionnaire;
- Builder;
- Contact.

---

# 119. UXP-115 — Admin Mobile Support Is Task-Based

Admin MAY иметь более desktop-oriented advanced editing.

Но критические действия:

- review notification;
- open inquiry;
- quick status;
- urgent casting action;

SHOULD быть usable на mobile where feasible.

---

# 120. UXP-116 — Touch Targets Must Be Comfortable

Особенно:

- filters;
- Builder selections;
- gallery controls;
- CTA;
- form controls.

Точные размеры определяются Accessibility Specification.

---

# 121. UXP-117 — Hover Must Never Be Required

Вся critical functionality должна иметь non-hover interaction.

---

# 122. UXP-118 — Orientation Independence

Portrait mode должен поддерживать основной mobile Journey.

Landscape может улучшать Video/Grid, но не быть обязательным.

---

# 123. UXP-119 — Performance Is Part of UX

Пользователь оценивает не Lighthouse score, а:

```text
How quickly can I act?
```

---

# 124. UXP-120 — First Useful Content Has Priority

Сначала загружается content, необходимый для текущего decision:

- primary photo;
- identity;
- key CTA.

Не весь media archive.

---

# 125. UXP-121 — Use Appropriate Derivatives

Gallery card не должна загружать multi-megabyte Original без необходимости.

---

# 126. UXP-122 — Lazy Load Secondary Media

Secondary images/videos SHOULD загружаться по мере необходимости.

---

# 127. UXP-123 — Loading Must Not Shift Critical Layout Excessively

Большие layout jumps снижают usability и trust.

---

# 128. UXP-124 — Analytics Must Be Invisible to Core UX

Tracking не должно:

- задерживать CTA;
- блокировать form;
- ломать navigation.

---

# 129. UXP-125 — AI Must Not Block Core UX

Public page не ждёт AI для отображения confirmed data.

---

# 130. UXP-126 — Reliability Is UX

Профессионально значимые failures должны иметь predictable behaviour.

---

# 131. UXP-127 — Preserve Last Valid Result

При failed regeneration UI SHOULD использовать last valid representation where allowed.

---

# 132. UXP-128 — Failure Message Must Match User Context

Не показывать public visitor:

```text
S3 PUT failed: ECONNRESET
```

Показывать понятный professional-safe message.

---

# 133. UXP-129 — Technical Details May Be Available to Admin

Admin diagnostic layer MAY содержать error code/details для troubleshooting.

---

# 134. UXP-130 — International UX Is End-to-End

Locale влияет на:

- navigation;
- labels;
- content;
- forms;
- validation;
- Builder;
- PDF.

---

# 135. UXP-131 — Language Switch Must Preserve Logical Page

Переключение locale не должно необоснованно возвращать пользователя на Home.

---

# 136. UXP-132 — No Mixed Locale in Critical Flow

Critical Professional Journey SHOULD избегать смешения локалей при наличии перевода.

---

# 137. UXP-133 — Official Names Are Not Casual Translations

Project/Institution/Award titles должны сохранять утверждённую форму.

---

# 138. UXP-134 — Dates and Units Must Be Localized Carefully

Presentation может меняться.

Source value — нет.

---

# 139. UXP-135 — Public Privacy Is UX

Пользователь должен видеть только разрешённые данные.

Privacy нельзя считать исключительно backend concern.

---

# 140. UXP-136 — Hidden Means Absent

Private item не должен:

- занимать пустое место;
- появляться как disabled card;
- быть виден в HTML;
- быть виден в initial payload.

---

# 141. UXP-137 — Consent Must Be Understandable

Если form требует consent, пользователь должен понимать:

- с чем соглашается;
- зачем это нужно.

---

# 142. UXP-138 — Avoid Dark Patterns

Запрещены:

- deceptive buttons;
- hidden opt-outs;
- false urgency;
- forced registration for basic access;
- misleading acceptance states.

---

# 143. UXP-139 — Professional Trust Over Engagement Tricks

Нельзя жертвовать:

- accuracy;
- clarity;
- privacy;

ради:

- more clicks;
- more time-on-site;
- more scroll.

---

# 144. UXP-140 — Analytics Does Not Personalize Invisibly

Без отдельного scope продукт не должен скрыто менять professional facts/content per identified visitor.

---

# 145. UXP-141 — Public UI Must Not Expose Admin Concepts Unnecessarily

Например visitor не должен видеть:

```text
Revision #14
visibility_scope_id
media relation
```

если это не является полезным professional information.

---

# 146. UXP-142 — Admin UI Must Expose Operational Truth

Напротив, Admin должен видеть:

- state;
- revision;
- readiness;
- source;
- visibility;
- dependencies;

когда это необходимо для безопасного управления.

---

# 147. UXP-143 — Information Hierarchy Must Be Persona-Aware

Один и тот же content может иметь разный priority в разных contexts.

Например:

- Commercial path → visuals first;
- Director path → video/projects;
- International path → language/location.

Но facts остаются одинаковыми.

---

# 148. UXP-144 — Contextual Presentation ≠ Separate Data

UX может менять ordering/presentation.

Не создавать duplicate content source.

---

# 149. UXP-145 — Home Is a Router, Not an Encyclopedia

Home должна:

- идентифицировать;
- квалифицировать;
- направлять;

а не показывать полный объём каждого domain.

---

# 150. UXP-146 — Public Profile Is a Professional Hub

Profile SHOULD позволять перейти к:

- Portfolio;
- Video;
- Emotional;
- Projects;
- Questionnaire;
- Contact.

---

# 151. UXP-147 — Questionnaire Page Is a Professional Hub

Она должна объединять:

```text
Recommended Questionnaire
Alternative Versions
Download
Customize
Professional CTA
```

---

# 152. UXP-148 — Admin Dashboard Is an Action Hub

Dashboard не заменяет domain navigation.

Он показывает:

- exceptions;
- deadlines;
- new inquiries;
- health;
- pending review.

---

# 153. UXP-149 — Public Builder Must Not Pollute Admin

Temporary user configurations не должны появляться как обычные Admin Questionnaires.

---

# 154. UXP-150 — User Should Understand Temporary Nature Where Relevant

Builder MAY сообщать, что:

```text
вы создаёте собственную временную версию анкеты;
исходный профиль не изменяется.
```

---

# 155. UXP-151 — Trust Through Provenance

Public documents SHOULD показывать:

- official source;
- generated date;
- profile URL.

---

# 156. UXP-152 — Historical Snapshot Must Be Understandable

Если старый PDF отличается от current profile, Generated Date помогает объяснить разницу.

---

# 157. UXP-153 — No False Freshness

Recent upload date не должна визуально называться Shooting Date.

---

# 158. UXP-154 — No False Completeness

UI не должен создавать впечатление, что empty field означает «не применимо», если fact просто отсутствует.

---

# 159. UXP-155 — No False AI Certainty

AI confidence не превращает recommendation в fact.

---

# 160. UXP-156 — Confirmation Must Match Consequence

Low-impact:

```text
Remove filter
```

может быть immediate.

High-impact:

```text
Publish
Delete
Send
Create Casting
```

требует explicit confirmation/action.

---

# 161. UXP-157 — Confirmation Fatigue Must Be Avoided

Не использовать confirmation modal для каждого save или filter.

Иначе пользователь перестаёт воспринимать серьёзные confirmations.

---

# 162. UXP-158 — Bulk Actions Must Show Scope

Перед bulk operation Admin должен понимать:

```text
Выбрано 12 элементов
```

и что произойдёт.

---

# 163. UXP-159 — Partial Bulk Failure Must Be Explainable

Если 10 из 12 действий успешны, UI должен показать:

- successful count;
- failed items;
- reasons;
- retry path.

---

# 164. UXP-160 — Async Operations Need Status

Для долгих операций:

- upload processing;
- derivative generation;
- PDF;
- AI Draft;

должны существовать состояния:

```text
Queued / Processing / Ready / Failed
```

где применимо.

---

# 165. UXP-161 — Do Not Freeze Whole UI for Background Work

Пользователь SHOULD иметь возможность продолжить безопасную работу, если операция может выполняться независимо.

---

# 166. UXP-162 — Optimistic UI Must Not Lie

UI MAY оптимистично обновляться только если failure recovery ясна.

Для high-impact business states предпочтительна authoritative confirmation.

---

# 167. UXP-163 — Concurrency Conflict Must Be Visible

Если entity изменена другим процессом/пользователем:

```text
Данные изменились после открытия формы.
Проверьте новую версию.
```

Вместо silent overwrite.

---

# 168. UXP-164 — Conflict Resolution Must Preserve Work

Где возможно, UI SHOULD помочь:

- compare;
- copy;
- reopen;
- reapply manually.

---

# 169. UXP-165 — Audit Should Be Discoverable, Not Intrusive

Admin может открыть history.

Но Audit Trail не должен перегружать основной editing interface.

---

# 170. UXP-166 — Help Must Be Contextual

Сложные поля MAY иметь:

- inline help;
- tooltip;
- Help Center link.

Не требовать искать базовое объяснение в отдельной документации.

---

# 171. UXP-167 — Tooltips Are Supplemental

Critical instructions не должны существовать только внутри hover tooltip.

---

# 172. UXP-168 — Examples Should Be Domain-Relevant

При необходимости placeholder/example:

```text
Например: English — B2
```

лучше абстрактного:

```text
Введите значение
```

---

# 173. UXP-169 — Forms Should Group Related Fields

Например Contact:

```text
Identity
Contact Methods
Visibility
```

вместо случайного длинного списка.

---

# 174. UXP-170 — Advanced Settings Should Be Secondary

Редко используемые технические настройки не должны конкурировать с core editing flow.

---

# 175. UXP-171 — Sensible Defaults, Explicit Exceptions

Admin should need to configure unusual cases, not every trivial standard behaviour.

---

# 176. UXP-172 — System Should Explain Dependency Effects

При изменении important source:

```text
Изменение основного фото повлияет на:
• публичный профиль
• новые анкеты
• конструктор
```

где это полезно.

---

# 177. UXP-173 — Historical Outputs Must Be Excluded From Misleading Dependency Warnings

Если Published historical revision immutable, UI не должен говорить:

```text
Все старые PDF будут обновлены.
```

---

# 178. UXP-174 — AI Roles Must Have Distinct UI Identity

Admin должен различать:

```text
Casting AI
BB Assistant
Theme AI
Virtual Operator
```

---

# 179. UXP-175 — Do Not Create One Generic AI Box

Один универсальный prompt field для всех AI задач ухудшает:

- context;
- trust;
- permissions;
- traceability.

---

# 180. UXP-176 — BB Assistant Is Contextual

BB Assistant SHOULD запускаться рядом с editable professional text или через понятный dedicated workspace.

---

# 181. UXP-177 — Casting AI Is Source-Oriented

Casting analysis UI должен явно показывать:

```text
Source
Extraction
Profile Match
Recommendation
Human Decision
```

---

# 182. UXP-178 — Theme AI Is Preview-Oriented

Theme workflow:

```text
Prompt
→ Proposal
→ Edit
→ Preview
→ Accessibility
→ Publish
```

---

# 183. UXP-179 — Virtual Operator Is Action-Oriented

Operator UI:

```text
Observation
→ Reason
→ Suggested Action
→ Resolve
```

---

# 184. UXP-180 — AI Should Not Dominate the Product

Если AI unavailable, interface не должен выглядеть пустым или нефункциональным.

---

# 185. UXP-181 — Public UI Must Feel Professional, Not Experimental

AI mechanics, technical statuses и internal automation не должны быть видимы public visitor без reason.

---

# 186. UXP-182 — Public Content Should Be Calm and Focused

Профессиональный сайт не должен использовать агрессивные:

- popups;
- flashing banners;
- gamification;
- scarcity cues.

---

# 187. UXP-183 — Visual Theme Must Not Obscure Content

Decorative layers не должны:

- ухудшать photo perception;
- перекрывать CTA;
- снижать contrast;
- ухудшать Video controls.

---

# 188. UXP-184 — Actor Photography Has Visual Priority

Professional photos не должны чрезмерно обрезаться декоративным UI.

Если thumbnail использует crop, full original context должен быть доступен там, где это профессионально необходимо.

---

# 189. UXP-185 — Aspect Ratio Must Be Intentional

Разные contexts могут использовать разные derived aspect ratios.

Но source/derivative relation должен сохраняться.

---

# 190. UXP-186 — Grid Density Must Match Device

Desktop может показывать больше items одновременно.

Mobile — меньше, но крупнее.

---

# 191. UXP-187 — Text Width Must Remain Readable

Long-form Biography/Project text не должен растягиваться на чрезмерную ширину large desktop viewport.

---

# 192. UXP-188 — Content Priority Beats Symmetry

Если профессионально важному block нужно больше визуального пространства, не следует жертвовать им ради идеальной декоративной симметрии.

---

# 193. UXP-189 — Consistent Interaction Patterns

Одинаковые actions:

- edit;
- archive;
- preview;
- publish;
- delete;

SHOULD использовать одинаковые patterns.

---

# 194. UXP-190 — Icon-Only Actions Need Labels Where Ambiguous

Trash icon может быть понятен.

Но abstract icons без accessible name/tooltip не допускаются.

---

# 195. UXP-191 — Public Links Must Look Interactive

Clickable professional links не должны выглядеть как plain text.

---

# 196. UXP-192 — Phone/WhatsApp/Social Should Be Actionable

Где visibility разрешает, UI SHOULD позволять выполнить соответствующее действие непосредственно.

---

# 197. UXP-193 — PDF Link Must Remain Understandable Offline

В самом PDF рядом с QR/link желательно иметь readable label, а не только bare encoded destination.

---

# 198. UXP-194 — Localization Switch Must Be Easy to Reverse

Пользователь должен одним действием вернуться к другой поддерживаемой locale.

---

# 199. UXP-195 — Unsupported Locale Must Not Pretend to Be Complete

Если locale не поддерживается полноценно, UI не должен выдавать её за production-complete language.

---

# 200. UXP-196 — Data Visibility Should Be Explicit in Admin

Admin content editors SHOULD видеть effective visibility context.

---

# 201. UXP-197 — Privacy-Sensitive Fields Need Stronger Visual Treatment

Например Guardian/Parent Contacts SHOULD иметь ясные privacy indicators.

---

# 202. UXP-198 — Public Preview Is Required for Significant Publication

Admin SHOULD иметь возможность увидеть, как content будет выглядеть публично до publish.

---

# 203. UXP-199 — Preview Must Use Real Publication Rules

Preview не должен показывать private fields, которые реально не попадут в Public Projection.

---

# 204. UXP-200 — Preview Is Not Authorization

Previewing content не делает его Published.

---

# 205. UXP-201 — Multiple Preview Contexts Where Needed

Для relevant content MAY поддерживаться:

```text
Public Site Preview
Questionnaire Preview
Public Builder Preview
```

поскольку visibility может различаться.

---

# 206. UXP-202 — Source and Derived Asset Must Be Understandable in Admin

Для Emotional Grid/Admin media пользователь должен понимать:

```text
Original
→ Crop configuration
→ Derived output
```

---

# 207. UXP-203 — Do Not Overexpose Technical Metadata

EXIF/checksum/file ID могут быть доступны в advanced details, но не должны перегружать основной media workflow.

---

# 208. UXP-204 — Readiness Should Be Summary + Detail

Пример:

```text
Готово к публикации: 8/10
```

с возможностью увидеть 2 blockers.

---

# 209. UXP-205 — Readiness Score Must Not Hide Hard Blockers

Высокий процент completion не отменяет critical error.

---

# 210. UXP-206 — Dashboard Uses Exceptions, Modules Use Detail

Dashboard:

```text
что требует внимания
```

Domain module:

```text
полное управление объектами
```

---

# 211. UXP-207 — Editing Should Stay Close to Context

Если Admin видит конкретный Project, он SHOULD иметь быстрый доступ к редактированию Project/Role без потери context.

---

# 212. UXP-208 — Cross-Module Navigation Must Preserve Object Identity

Например:

```text
Feedback
→ Create Casting
→ Casting
```

должно сохранять обратную связь к исходному Feedback.

---

# 213. UXP-209 — Relationship Visibility

Admin SHOULD понимать, что объект уже используется.

Например:

```text
Это фото используется:
• Main Portfolio
• Questionnaire "Compact"
```

---

# 214. UXP-210 — Broken Dependency Must Be Discoverable

Если удалить/скрыть object нельзя, UI показывает dependencies.

---

# 215. UXP-211 — Public Builder Customization Must Not Look Like Editing Actress Profile

UI SHOULD явно отделять:

```text
Ваша версия анкеты
```

от:

```text
Официальный профиль
```

---

# 216. UXP-212 — Prepared Questionnaire Should Retain Authority

Phrase/order SHOULD communicate:

```text
Recommended version
```

before:

```text
Build your own
```

---

# 217. UXP-213 — User-Created Temporary Casting Labels Must Be Scoped

Если visitor вводит:

```text
Project: Brand X
Role: Mother
```

UI должен объяснять, что это context их generated document, не изменение public actor profile.

---

# 218. UXP-214 — Analytics Should Inform, Not Judge

Admin Dashboard SHOULD показывать factual metrics/recommendations без категоричных:

```text
Эта фотография плохая.
```

если это не объективная technical validation.

---

# 219. UXP-215 — Business Outcome Labels Must Be Neutral and Exact

Opportunity stages:

```text
Callback
Offer
Booked
Closed — Not Selected
Withdrawn
```

не должны заменяться эмоционально окрашенными labels.

---

# 220. UXP-216 — Archived Does Not Mean Deleted

Admin UI должен ясно различать эти состояния.

---

# 221. UXP-217 — Empty Archives Should Not Pollute Public Navigation

Public navigation не показывает пустой section без professional value.

---

# 222. UXP-218 — Feature Visibility Should Follow Available Content

Если Showreel отсутствует, UI не должен показывать broken `Showreel` tab.

---

# 223. UXP-219 — Dynamic Navigation Must Remain Predictable

Скрытие пустых sections допустимо, но базовая навигационная структура не должна становиться хаотичной.

---

# 224. UXP-220 — Help Center Must Not Interrupt Core Work

Help доступен из контекста, но не блокирует standard flow.

---

# 225. UXP-221 — Social Publishing Is Admin Capability, Not Public Distraction

Public site MAY иметь approved social links.

Admin social publishing workflow остаётся отдельным management area.

---

# 226. UXP-222 — Existing Valuable Behaviour Must Be Preserved

При UX redesign нельзя потерять без migration/replacement:

- role-specific photos;
- “В образе” carousel;
- typed media;
- Help/Tickets;
- social publishing;
- multilingual content.

---

# 227. UXP-223 — UX Migration Must Be Non-Destructive

Новый UI не должен требовать удаления legacy content до его корректной migration.

---

# 228. UXP-224 — Responsive Reordering Is Allowed

Content MAY менять визуальный порядок между Desktop и Mobile, если:

- смысл сохраняется;
- accessibility order остаётся корректным;
- critical actions не теряются.

---

# 229. UXP-225 — Visual Order and DOM Order Must Be Considered Together

CSS-only rearrangement не должно создавать screen-reader/keyboard order, противоречащий визуальному flow.

---

# 230. UXP-226 — Public Theme Must Not Alter Core UX Contracts

Theme может менять presentation.

Не может менять наличие:

- Primary CTA;
- Questionnaire path;
- accessibility;
- professional navigation.

---

# 231. UXP-227 — Theme Locks Must Be Understandable

Если Admin lock’ает component/token от AI Theme change, UI должен ясно показывать locked state.

---

# 232. UXP-228 — Theme Preview Across Viewports

Перед Publish необходимо проверить минимум:

```text
Desktop
Tablet
Mobile
```

---

# 233. UXP-229 — Temporary Theme Must Show Operational State

Admin должен видеть:

- Temporary;
- activation period;
- current permanent Theme.

---

# 234. UXP-230 — Publish Should Be Atomic From User Perspective

Пользователь не должен видеть «полутему», где часть страниц уже новая, а часть старая, если architecture поддерживает atomic theme activation.

---

# 235. UXP-231 — Public Professional Actions Must Remain Available During Optional Service Failure

Например Analytics outage не скрывает Contact.

---

# 236. UXP-232 — Service State May Be Shown to Admin

Admin MAY видеть:

```text
AI unavailable
WhatsApp degraded
PDF service healthy
```

если это помогает работе.

---

# 237. UXP-233 — Public Visitor Should Not See Internal Service Topology

Public error messages не раскрывают unnecessary infrastructure details.

---

# 238. UXP-234 — Security Errors Should Be Safe

Например unavailable private resource:

не должен подтверждать существование hidden object лишними detail.

---

# 239. UXP-235 — Authorization Failure Is Not a Validation Error

Admin UI SHOULD различать:

- you cannot access;
- entered data invalid.

---

# 240. UXP-236 — Session Expiration Must Be Clear

Для Public Builder:

```text
Сессия конструктора истекла.
Начните новую анкету.
```

с safe recovery options where possible.

---

# 241. UXP-237 — Authentication Expiration Must Preserve Unsaved Work Where Safely Possible

Admin session expiry SHOULD минимизировать потерю введённого текста, если architecture позволяет безопасное recovery.

---

# 242. UXP-238 — Long Text Editing Needs Draft Safety

Biography/description editor SHOULD снижать риск потери текста при navigation/network issues.

---

# 243. UXP-239 — Autosave Is Not Auto-Publish

Если используется autosave:

```text
autosave draft
```

никогда не означает:

```text
publish
```

---

# 244. UXP-240 — Autosave State Should Be Visible

Например:

```text
Сохранено
Сохраняется…
Ошибка сохранения
```

---

# 245. UXP-241 — User Must Know When Data Has Not Been Saved

Silent save failure неприемлем для significant Admin editing.

---

# 246. UXP-242 — Data Entry Must Support Professional Precision

Не заменять precise fields чрезмерно «красивыми» controls, ухудшающими точность ввода.

---

# 247. UXP-243 — Controlled Vocabularies Where Domain Requires

Например Portfolio type SHOULD использовать approved values, а не free-text spelling variants.

---

# 248. UXP-244 — Free Text Where Professional Nuance Requires

Biography/Project description остаются text areas, а не набором rigid enums.

---

# 249. UXP-245 — Structured + Narrative Complement Each Other

Необходимо поддерживать оба слоя:

```text
structured professional facts
+
professional narrative
```

Один не заменяет другой.

---

# 250. UXP-246 — Public Data Hierarchy

Рекомендуемый общий public priority:

```text
P0
Identity
Primary visuals
Video
Questionnaire
Contact

P1
Appearance
Languages
Skills
Emotional Range

P2
Projects
Roles
Training
Achievements

P3
Archive
Blog
Extended context
```

Конкретная страница может адаптировать порядок по Persona.

---

# 251. UXP-247 — Homepage Content Order

Базовая целевая логика:

```text
Hero
↓
Quick Facts
↓
Portfolio
↓
Video
↓
Emotional Range
↓
Selected Projects
↓
Skills / Languages
↓
Training / Achievements
↓
Questionnaire
↓
Professional Contact
```

Точный final layout определяется отдельной UX specification.

---

# 252. UXP-248 — Repetition Can Be Intentional for CTA

Questionnaire/Contact CTA MAY повторяться в нескольких релевантных местах.

Это не считается data duplication, поскольку CTA — presentation.

---

# 253. UXP-249 — Repetition of Facts Must Be Consistent

Если Location показывается в Hero и Questionnaire, обе projections используют один Source.

---

# 254. UXP-250 — UX Must Support “One Profile — Many Presentations”

Один Profile может проявляться как:

- Home;
- Quick View;
- Prepared Questionnaire;
- Custom Builder PDF;
- Project page;
- International version.

UX должен делать эти representations согласованными, но не идентичными.

---

# 255. UX Conflict Resolution Priority

Если UX-принципы конфликтуют, используется следующий приоритет:

```text
1. Security / Privacy
2. Factual Integrity
3. Accessibility
4. Business Rules
5. Professional Task Completion
6. Error Prevention
7. Efficiency
8. Consistency
9. Visual Elegance
10. Decorative Enhancement
```

---

# 256. Пример конфликта

Красивый full-screen Hero video:

- замедляет first useful content;
- скрывает CTA;
- неудобен mobile.

Решение:

профессиональная доступность имеет приоритет над cinematic effect.

---

# 257. Пример конфликта Builder

One-page builder:

- быстрее на Desktop;
- перегружен на Mobile.

Допустимо использовать:

```text
Desktop: compact multi-panel
Mobile: progressive steps
```

если logical model одинакова.

---

# 258. Пример конфликта Privacy

Product wants to show:

```text
Contact available
```

но Contact hidden.

UX не должен показывать даже partially masked private value без отдельного rule.

---

# 259. Пример конфликта AI

AI classification has 98% confidence.

Business Rule требует human confirmation.

Human confirmation остаётся обязательным.

---

# 260. UX Quality Gate

Capability не готов к implementation, если не определены:

```text
Primary Persona
Primary Goal
Entry Point
Information Priority
Primary CTA
Secondary CTA
Happy Path
Empty State
Loading State
Error State
Mobile Behaviour
Accessibility Behaviour
Privacy Boundary
```

---

# 261. Screen Specification Minimum

Будущая Screen Specification SHOULD включать:

1. Screen ID.
2. Route.
3. Persona.
4. Purpose.
5. Entry Points.
6. Primary Content.
7. Actions.
8. States.
9. Permissions.
10. Responsive rules.
11. Accessibility.
12. Analytics.
13. Related User Flows.

---

# 262. UX Requirement Traceability

Рекомендуемая цепочка:

```text
Persona
 ↓
CJM
 ↓
UXP
 ↓
UF
 ↓
FR
 ↓
Component / Screen
 ↓
AC
 ↓
Test
```

---

# 263. UX Acceptance Test Principle

UX не считается принятым только по screenshot comparison.

Нужно проверять:

- task completion;
- navigation;
- state transitions;
- keyboard;
- mobile;
- errors;
- privacy;
- content correctness.

---

# 264. UX Anti-Pattern — AP-UX-001

**Database Navigation**

Public menu отражает таблицы backend.

---

# 265. AP-UX-002

**Everything on Home**

Все данные помещены на одну бесконечную страницу без priority.

---

# 266. AP-UX-003

**Mystery Buttons**

Основные actions названы:

```text
Go
More
Next
```

без intent.

---

# 267. AP-UX-004

**Hidden Professional CTA**

Contact доступен только в footer.

---

# 268. AP-UX-005

**Desktop Shrink**

Desktop UI просто масштабирован до mobile width.

---

# 269. AP-UX-006

**Narrative-as-Database**

Skills/Languages существуют только внутри Biography.

---

# 270. AP-UX-007

**AI-as-Fact**

AI recommendation визуально не отличается от saved data.

---

# 271. AP-UX-008

**Auto-Publish by Save**

Любое редактирование сразу публично.

---

# 272. AP-UX-009

**Private-but-Loaded**

Private field загружен в browser, но hidden CSS.

---

# 273. AP-UX-010

**One Giant PDF**

Пользователь не имеет Compact/custom alternative.

---

# 274. AP-UX-011

**Builder as Word Processor**

Публичный пользователь вручную верстает документ.

---

# 275. AP-UX-012

**Empty Builder**

Нет useful defaults.

---

# 276. AP-UX-013

**Modal Everywhere**

Сложные workflows открываются в nested modals.

---

# 277. AP-UX-014

**Confirmation Everywhere**

Каждый harmless click требует modal.

---

# 278. AP-UX-015

**No Confirmation for High Impact**

Publish/Delete/Send выполняются случайным click без ясного consequence.

---

# 279. AP-UX-016

**Generic Error**

```text
Something went wrong.
```

без context/recovery для исправимой ошибки.

---

# 280. AP-UX-017

**Raw Technical Error to Public**

Stack traces/DB errors показываются public visitor.

---

# 281. AP-UX-018

**Broken Deep Link**

Project/Questionnaire page не даёт понять, чей это profile или куда идти дальше.

---

# 282. AP-UX-019

**Mixed Locale Critical Flow**

Builder на EN создаёт RU Preview.

---

# 283. AP-UX-020

**Visual Theme Overrules Accessibility**

Theme делает CTA/labels нечитаемыми.

---

# 284. Definition of UX Compliance

Экран или flow соответствует настоящему документу, если:

1. имеет понятную professional purpose;
2. поддерживает relevant Persona;
3. critical information легко находится;
4. Primary CTA очевиден;
5. нет необоснованных dead ends;
6. navigation предсказуема;
7. mobile flow полноценен;
8. keyboard/accessibility поддержаны;
9. privacy enforced before client;
10. AI не маскируется под confirmed fact;
11. errors понятны и recoverable;
12. Draft/Published semantics ясны;
13. performance поддерживает professional task;
14. optional service failure не уничтожает core flow;
15. visual presentation не ухудшает professional decision-making.

---

# 285. Главная UX-модель Public Product

```text
ENTRY
  ↓
UNDERSTAND
  ↓
QUALIFY
  ↓
VERIFY
  ↓
GET THE RIGHT MATERIAL
  ↓
ACT
```

---

# 286. Главная UX-модель Admin Product

```text
SEE WHAT REQUIRES ATTENTION
  ↓
UNDERSTAND WHY
  ↓
EDIT / REVIEW
  ↓
VALIDATE
  ↓
CONFIRM
  ↓
PUBLISH / RESOLVE
  ↓
VERIFY
```

---

# 287. Финальный принцип

> **UX продукта должен скрывать техническую сложность, но не скрывать профессиональный смысл, состояние данных или последствия действий. Публичный пользователь должен быстро понять актрису, проверить релевантные доказательства, получить подходящий профессиональный пакет и выполнить действие. Администратор должен видеть точное состояние системы, работать преимущественно с исключениями и принимать только те решения, которые действительно требуют человеческого профессионального контроля.**