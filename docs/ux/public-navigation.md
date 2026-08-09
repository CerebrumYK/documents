# PUBLIC NAVIGATION

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная модель публичной навигации профессионального сайта актрисы

**Целевой файл:** `docs/ux/public-navigation.md`  
**Документ:** DOC-041  
**Статус:** ✅ Completed  
**Тип:** UX / Public Navigation

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
- `docs/customer-journey/conversion-funnel.md`
- `docs/ux/ux-principles.md`

---

# 1. Назначение документа

Настоящий документ определяет целевую публичную навигационную модель продукта.

Документ фиксирует:

1. глобальную навигацию;
2. основные публичные разделы;
3. иерархию разделов;
4. deep-link behaviour;
5. mobile navigation;
6. contextual navigation;
7. language switching;
8. Public Questionnaire navigation;
9. Public Builder navigation;
10. Search integration;
11. CTA placement;
12. Back/Forward behaviour;
13. navigation states;
14. empty-section behaviour;
15. accessibility requirements;
16. SEO/shareability requirements;
17. правила предотвращения dead ends.

---

# 2. Главная задача публичной навигации

Публичная навигация должна позволять профессиональному пользователю быстро ответить:

```text
Где я?
Кто это?
Что здесь можно проверить?
Куда идти дальше?
Как получить анкету?
Как связаться?
```

Навигация не должна требовать знания внутренней структуры CMS.

---

# 3. Основной принцип

Public Navigation строится вокруг **профессиональных задач**, а не таблиц Domain Model.

Целевой mental model:

```text
Профиль
Портфолио
Эмоциональный диапазон
Проекты
Анкета
Контакты
```

а не:

```text
Media Assets
Portfolio Items
Role Relations
Questionnaire Revisions
```

---

# 4. Navigation IDs

Навигационные требования используют prefix:

```text
NAV-PUB-*
```

Пример:

```text
NAV-PUB-001
Global navigation must expose Questionnaire.
```

---

# 5. Основные публичные разделы

Целевая primary navigation SHOULD включать:

```text
Profile
Portfolio
Emotional Range
Projects
Questionnaire
Contacts
```

Русские UI-labels могут быть:

```text
Обо мне / Профиль
Портфолио
Эмоции
Проекты
Анкета
Контакты
```

Точный copy определяется UX/content layer.

---

# 6. Home не обязана быть отдельным пунктом меню

Logo/Actor Name SHOULD вести на Public Home/Profile root.

Отдельный пункт:

```text
Главная
```

MAY использоваться, но не обязателен, если брендовый link выполняет ту же функцию понятно и доступно.

---

# 7. Navigation Priority

Порядок primary navigation должен отражать профессиональную ценность.

Recommended baseline:

```text
Profile
Portfolio
Emotional Range
Projects
Questionnaire
Contacts
```

Но responsive/context-specific presentation MAY менять визуальную форму без изменения доступности.

---

# 8. Questionnaire — обязательный верхнеуровневый путь

`Questionnaire` MUST быть доступна из глобальной публичной навигации или эквивалентного постоянно заметного action.

Она не должна быть спрятана:

- в footer;
- внутри Contacts;
- внутри Project;
- только на Home.

---

# 9. Contact — обязательный верхнеуровневый путь

Professional Contact MUST быть легко достижим.

Минимум один из вариантов:

```text
global navigation
persistent CTA
sticky mobile action
page contextual CTA
```

должен быть доступен на критических страницах.

---

# 10. Video navigation

Video Intro является P0 content, но не обязательно отдельным global navigation section.

Предпочтительная модель:

```text
prominent CTA on Home/Profile
+
contextual access throughout site
```

Например:

```text
[Смотреть видеовизитку]
```

---

# 11. Why Video Is Not Necessarily a Menu Item

Создание глобального menu item:

```text
Видео
```

может быть оправдано, если professional media library имеет достаточную самостоятельную ценность.

Но наличие отдельного Video menu item не должно быть обязательным условием доступа к Video Intro.

---

# 12. Training in Global Navigation

Training является самостоятельным Domain capability, но не обязательно primary navigation item.

Предпочтительно:

```text
Profile / Professional Information
→ Training
```

или отдельный secondary navigation link.

Причина:

Training является P2 content для большинства первых профессиональных Journeys.

---

# 13. Achievements

Achievements также являются secondary professional content.

Они MAY быть:

- section на Profile/Home;
- отдельной page;
- secondary navigation item.

Но не должны вытеснять Questionnaire/Portfolio/Contact.

---

# 14. Blog

Blog MAY существовать как отдельный navigation item, если он активно используется.

Однако он не является частью primary casting decision path.

Допустимые варианты:

```text
More → Blog
```

или secondary/footer navigation.

---

# 15. Main Navigation Architecture

Базовая модель:

```text
ACTOR PUBLIC SITE
│
├── Profile / Home
│   ├── Quick Facts
│   ├── Video Intro
│   ├── Skills
│   ├── Languages
│   ├── Training
│   └── Achievements
│
├── Portfolio
│   ├── All
│   ├── Full Body
│   ├── Close-Up
│   ├── Profile
│   ├── Three-Quarter
│   └── Other approved categories
│
├── Emotional Range
│   ├── Emotional Grid
│   └── Emotional Portfolio
│
├── Projects
│   ├── Featured Projects
│   ├── All Projects
│   └── Project Detail
│       └── Role
│
├── Questionnaire
│   ├── Primary Prepared Questionnaire
│   ├── Alternative Prepared Questionnaires
│   ├── Download Picker
│   └── Public Questionnaire Builder
│
└── Contacts
    └── Professional CTA
```

---

# 16. Flat Primary Navigation

На верхнем уровне SHOULD избегаться чрезмерная глубина.

Пользователь не должен проходить:

```text
Portfolio
→ Photos
→ Professional
→ Main
→ Full Body
```

для получения основного Full Body.

---

# 17. Secondary Navigation

Secondary navigation MAY использоваться внутри sections.

Например Portfolio:

```text
Все
В полный рост
Крупный план
Профиль
3/4
```

Это filters/tabs, а не отдельная глобальная hierarchy.

---

# 18. Public Navigation ≠ Sitemap

Sitemap может содержать значительно больше URL, чем global navigation.

Например:

```text
/projects/project-a
/projects/project-b
/emotional-portfolio/session-a
/questionnaire/casting-x
```

не должны все отображаться в global menu.

---

# 19. Direct Entry Principle

Каждая shareable public page MUST предполагать direct entry.

То есть пользователь может попасть сразу на:

- Portfolio;
- Project;
- Emotional Portfolio;
- Questionnaire;
- Builder-related public page.

---

# 20. Direct Entry Requirements

При direct entry страница SHOULD предоставлять:

```text
Actor Identity
Current Section
Global Navigation
Questionnaire access
Professional CTA
```

---

# 21. No Deep-Link Dead Ends

Недопустимо:

```text
Shared Project URL
→ Project page
→ no actor identity
→ no navigation
→ no contact
```

---

# 22. Project Detail Continuation

Project Detail SHOULD иметь contextual continuation:

```text
Другие проекты
Актёрская анкета
Запросить материалы
Пригласить на кастинг
```

в зависимости от UX context.

---

# 23. Emotional Portfolio Continuation

Emotional Portfolio SHOULD предлагать:

```text
Video
Questionnaire
Portfolio
Professional CTA
```

---

# 24. Portfolio Continuation

Portfolio SHOULD предлагать:

```text
Video Intro
Emotional Range
Questionnaire
Contact / Casting CTA
```

---

# 25. Questionnaire Continuation

Questionnaire Page SHOULD предлагать:

```text
Download
Customize
Official Profile
Professional CTA
```

---

# 26. Navigation and Conversion Funnel

Primary navigation должна поддерживать этапы:

```text
Evaluation
→ Evidence
→ Questionnaire
→ Action
```

Не требуется отображать Funnel буквально.

---

# 27. Home as Navigation Router

Home выполняет три роли:

```text
Recognition
Qualification
Routing
```

Она должна позволять перейти к главным профессиональным направлениям без полного scroll.

---

# 28. Recommended Home Anchors

Если Home является multi-section page, MAY использоваться contextual anchors:

```text
Portfolio
Video
Emotional
Projects
Skills
Questionnaire
Contacts
```

Но anchors не должны заменять stable section URLs, если соответствующий capability имеет отдельную страницу.

---

# 29. Anchor URLs

Если section имеет собственный share/use case, предпочтительна отдельная route.

Например:

```text
/portfolio
/questionnaire
/projects
```

лучше исключительно:

```text
/#portfolio
/#questionnaire
```

для критических профессиональных destinations.

---

# 30. Canonical Routing Principle

Public routing SHOULD обеспечивать стабильный semantic URL.

Conceptual examples:

```text
/
/portfolio
/emotional
/projects
/projects/{slug}
/questionnaire
/contact
```

Конкретная technical route naming определяется Architecture.

---

# 31. Emotional Portfolio Route

Поскольку может существовать несколько Emotional Portfolio sessions, модель MAY быть:

```text
/emotional
/emotional/{slug}
```

где:

`/emotional` — overview/current primary;

`/emotional/{slug}` — конкретная shooting session.

---

# 32. Questionnaire Routes

Модель MAY включать:

```text
/questionnaire
/questionnaire/{slug}
```

или иной agreed route structure.

Главная `/questionnaire` SHOULD выступать hub.

---

# 33. Casting-Specific Questionnaire Route

Target model MAY использовать:

```text
/questionnaire/casting/{public-token-or-slug}
```

для специальной профессиональной версии.

Route MUST соблюдать:

- Public access policy;
- expiry/access if configured;
- no private/admin identifiers in unsafe form.

---

# 34. Public Builder Route

Conceptually:

```text
/questionnaire/build
```

или equivalent.

Builder SHOULD также запускаться из Prepared Questionnaire:

```text
/questionnaire/{slug}
→ Customize
```

---

# 35. Builder Is Part of Questionnaire Navigation

Public Builder не должен выглядеть отдельным unrelated application.

User mental model:

```text
Questionnaire
→ Customize / Build
```

---

# 36. Builder Exit

Builder SHOULD предоставлять понятные варианты:

```text
Back to Questionnaire
Back to Profile
Continue Editing
Generate PDF
```

---

# 37. Builder Exit Must Preserve Session

Обычная навигация к публичным разрешённым страницам SHOULD не уничтожать Builder Session до expiry.

---

# 38. Builder Active State

При активной session MAY быть показан persistent indicator:

```text
Моя анкета · 8 элементов
```

с action:

```text
Продолжить
```

---

# 39. Builder Indicator Must Not Dominate

Indicator не должен мешать обычному public browsing пользователю.

Он отображается только при активной session.

---

# 40. Global Header

Public Header SHOULD содержать минимум:

- Actor identity/logo/name;
- navigation trigger/items;
- locale control;
- key action where layout permits.

---

# 41. Desktop Header

Desktop MAY использовать horizontal navigation:

```text
Name/Logo | Profile | Portfolio | Emotional | Projects | Questionnaire | Contacts | EN
```

и отдельный prominent CTA.

---

# 42. Desktop CTA

Один prominent CTA MAY быть вынесен отдельно:

```text
[Актёрская анкета]
```

или contextual:

```text
[Пригласить на кастинг]
```

Конкретная hierarchy определяется screen context.

---

# 43. Header Density

Не следует помещать в desktop header:

- все secondary sections;
- social networks;
- Training;
- Achievements;
- Blog;
- Help;
- every CTA;

если это создаёт visual noise.

---

# 44. Secondary Menu / More

Менее приоритетные sections MAY находиться:

```text
More / Ещё
```

например:

- Training;
- Achievements;
- Blog.

Но professional P0 items не следует прятать туда.

---

# 45. Mobile Navigation

На Mobile рекомендуется комбинация:

```text
Compact Header
+
Menu Drawer / Sheet
+
Persistent High-Value Actions
```

---

# 46. Mobile Header

Минимум:

- Actor identity;
- menu trigger;
- locale control или доступ к нему.

---

# 47. Mobile Menu

Menu SHOULD открывать major destinations:

```text
Профиль
Портфолио
Эмоции
Проекты
Анкета
Контакты
```

Secondary:

```text
Обучение
Достижения
Блог
```

где доступны.

---

# 48. Mobile Sticky Actions

На professional content pages MAY использоваться:

```text
Видео | Анкета | Связаться
```

или contextual equivalent.

---

# 49. Sticky Navigation vs Sticky Actions

Не смешивать:

- section navigation;
- professional action bar.

Они выполняют разные задачи.

---

# 50. Mobile Bottom Navigation

Полноценный app-style bottom nav MAY использоваться только если он реально улучшает critical mobile journey.

Он не является обязательным.

---

# 51. Mobile Drawer Closing Behaviour

После выбора navigation destination drawer SHOULD закрываться.

Focus должен переходить предсказуемо.

---

# 52. Scroll Lock

Открытый mobile navigation overlay SHOULD корректно управлять scroll/focus без потери position после закрытия.

---

# 53. Desktop Current Section

Активный navigation item SHOULD быть визуально различим.

Например пользователь на:

```text
/portfolio
```

видит `Portfolio` как current.

---

# 54. Nested Current State

На:

```text
/projects/{slug}
```

верхнеуровневый `Projects` остаётся активным context.

---

# 55. Questionnaire Builder Current State

На Builder:

```text
Questionnaire
```

остаётся parent navigation context.

---

# 56. Breadcrumbs

Breadcrumbs MAY использоваться на deep pages.

Например:

```text
Projects
› Project Name
› Role
```

---

# 57. Breadcrumbs Not Required Everywhere

На shallow pages:

```text
Portfolio
Questionnaire
Contacts
```

breadcrumb может быть избыточным.

---

# 58. Breadcrumbs Must Not Replace Global Navigation

Они являются orientation aid.

---

# 59. Project Breadcrumb

Recommended conceptual structure:

```text
Projects
› Project Title
```

Если Role имеет самостоятельный public detail:

```text
Projects
› Project Title
› Role
```

---

# 60. Emotional Breadcrumb

```text
Emotional Range
› Shooting Session
```

при наличии нескольких sessions.

---

# 61. Questionnaire Breadcrumb

```text
Questionnaire
› Casting Version
```

для specific questionnaire.

Builder:

```text
Questionnaire
› Build Your Version
```

---

# 62. Back Links

Contextual:

```text
← Все проекты
← Эмоциональный диапазон
```

MAY дополнять breadcrumbs.

---

# 63. Avoid JavaScript-only “Back”

Кнопка:

```text
Назад
```

не должна исключительно вызывать browser history, если пользователь мог открыть страницу напрямую.

Лучше semantic parent link:

```text
← Все проекты
```

где context ясен.

---

# 64. Browser Back Still Works

Custom navigation не должна ломать нативную browser history.

---

# 65. Language Navigation

Locale control является глобальным navigation control.

---

# 66. Locale Switch Principle

Switch:

```text
RU ↔ EN
```

SHOULD сохранять текущую logical destination.

---

# 67. Locale Switch Example

Правильно:

```text
RU Project A
→ EN
→ EN Project A
```

Неправильно:

```text
RU Project A
→ EN
→ EN Home
```

без объективной причины.

---

# 68. Missing Localized Route

Если точная localization unavailable, system follows defined I18N fallback.

User должен получать понятное поведение, не 404 из-за language switch.

---

# 69. Locale State Persistence

Selected locale SHOULD сохраняться при дальнейшей navigation.

---

# 70. Locale and Builder

Active Public Builder Session сохраняет locale согласно Builder rules.

---

# 71. Locale and Security

Navigation switch не меняет permissions.

Если page unavailable because of access, другой locale не должен обходить это.

---

# 72. Search Navigation

Public Search SHOULD быть доступен там, где он реально помогает professional screening.

Варианты:

```text
Header search
Quick View search
Profile search control
```

Точное размещение определяется UX screens.

---

# 73. Search Is Not Required in Global Header at All Sizes

Mobile header может использовать search action/icon, а не full input.

---

# 74. Search Results

Result link должен вести к semantic source page/section.

Например:

```text
English — B2
→ Profile / Languages
```

---

# 75. Search Result Must Not Create Synthetic Pages Necessarily

Не требуется route:

```text
/search/result/123
```

для каждого результата.

---

# 76. Search State

Search query MAY сохраняться при возврате к results.

---

# 77. Search Deep Link

Если search URL shareable:

```text
/search?q=english
```

он не должен раскрывать private data и должен иметь reasonable SEO policy.

Точная реализация определяется Search Architecture.

---

# 78. Portfolio Navigation

Portfolio top-level view SHOULD позволять:

```text
All
Full Body
Close-Up
Profile
Three-Quarter
...
```

через category filters.

---

# 79. Filters Are Not Global Navigation

Portfolio categories остаются локальной navigation model.

---

# 80. Filter URL State

Если category view полезен для sharing/back navigation, filter MAY отражаться в URL:

```text
/portfolio?type=full-body
```

или semantic subroute.

Точное решение определяется UX/Architecture.

---

# 81. Portfolio Lightbox Navigation

При открытии image detail/lightbox пользователь SHOULD иметь:

- previous;
- next;
- close;
- current category context.

---

# 82. Lightbox Close

После закрытия пользователь возвращается к прежнему scroll/filter state.

---

# 83. Deep-Linkable Media

Отдельные media deep links MAY поддерживаться, но не являются обязательными для каждого Photo.

Если реализуются, они MUST сохранять professional context.

---

# 84. Emotional Navigation

Основной emotional journey:

```text
Emotional Overview
→ Primary Grid
→ Full Emotional Portfolio
```

---

# 85. Multiple Emotional Grids

Если для одной session существует несколько Grids:

- Primary Grid показывается первым;
- дополнительные доступны secondary.

---

# 86. Emotional Session Navigation

При нескольких sessions MAY быть:

```text
Current / Primary
Previous Sessions
```

Но UX не должен превращать их в confusing archive wall.

---

# 87. Projects Navigation

Projects top-level:

```text
Featured
All
```

MAY дополняться category filters, если domain/data justified.

---

# 88. Project Detail Navigation

Project Detail SHOULD иметь links:

```text
Role
Media
Related/Other Projects
Questionnaire
Professional CTA
```

---

# 89. Performance Dates

Если Project/Role содержит Performance Dates, они относятся к detail content и не требуют отдельного global nav.

---

# 90. Training Navigation

Если Training имеет отдельную public page:

```text
/training
```

она SHOULD быть доступна из:

- Profile;
- More/secondary nav;
- Questionnaire relations where appropriate.

---

# 91. Achievements Navigation

Аналогично Training.

---

# 92. Contacts Navigation

Contacts page должна быть простой final destination.

Не создавать hierarchy:

```text
Contacts
→ Ways to contact
→ Professional contacts
→ Contact form
```

без необходимости.

---

# 93. Contact Page Content

MAY содержать:

- permitted direct contacts;
- social/professional links;
- contextual Feedback options.

---

# 94. Contextual CTA Can Bypass Contacts Page

`Invite to Casting` MAY открывать form напрямую из Project/Questionnaire/Profile.

Пользователь не обязан сначала идти на `/contact`.

---

# 95. Contact Form URL

Если form имеет отдельную shareable route, она SHOULD сохранять professional identity.

Но contextual modal/sheet также допустим.

---

# 96. Feedback Type From Navigation Context

CTA MAY заранее задавать Feedback Type:

```text
Invite to Casting
→ type=CASTING_INVITATION
```

но user-visible intent должен быть понятен.

---

# 97. Contextual Source

System MAY передать:

```text
source_page
project_id
questionnaire_id
builder_session_id
```

где allowed.

Public user не должен заполнять это вручную.

---

# 98. Navigation and Attribution

Navigation preserves acquisition attribution invisibly where required.

Не показывать пользователю marketing tracking IDs.

---

# 99. Footer Navigation

Footer SHOULD дополнять Header.

MAY включать:

- main navigation;
- Contact;
- Questionnaire;
- social links;
- privacy/legal;
- Blog;
- Help/other public resources where applicable.

---

# 100. Footer Must Not Be Only Path

Critical destinations не должны существовать только в Footer.

---

# 101. Social Links

Approved public social links MAY быть:

- Footer;
- Contacts;
- Profile.

Не должны перегружать primary navigation.

---

# 102. Help Center

Если Help Center имеет публичную audience relevance, он MAY быть в Footer/secondary navigation.

Не является P0 casting navigation.

---

# 103. Empty Section Behaviour

Если public section не имеет Published content, navigation SHOULD избегать ведения пользователя на бессмысленную пустую page.

---

# 104. Empty Section Navigation Policy

Conceptually:

```text
No published Showreel
→ do not show Showreel menu item
```

Но parent section может оставаться, если в нём есть другой content.

---

# 105. Dynamic Navigation Constraint

Dynamic hiding не должно делать main navigation unpredictable.

Core destinations:

```text
Profile
Portfolio
Questionnaire
Contact
```

SHOULD быть стабильными, если соответствующий core capability функционален.

---

# 106. Emotional Section Empty

Если Emotional Portfolio пока не Published:

- section MAY быть скрыт;
- не показывать broken placeholder в main nav.

---

# 107. Projects Empty

Если Projects отсутствуют, `Projects` MAY быть скрыт до появления public content.

---

# 108. Questionnaire Empty Is Different

Questionnaire — Core Target capability.

Если Prepared Questionnaire временно unavailable, page SHOULD скорее показывать appropriate state/alternative Builder, чем исчезать полностью из navigation, если product remains configured to support questionnaires.

---

# 109. Contact Empty Is Not Acceptable for Ready Public Profile

Профессиональный Profile должен иметь хотя бы один разрешённый professional contact path:

- Feedback Form;
- direct Contact;
- equivalent approved CTA.

Readiness should catch this.

---

# 110. Navigation Loading States

Header/global navigation SHOULD быть доступна независимо от loading secondary page content.

---

# 111. Navigation Failure Isolation

Broken page content не должно ломать entire global navigation.

---

# 112. 404 Page

Public 404 SHOULD содержать:

- Actor Identity;
- global navigation;
- link to Profile;
- Questionnaire;
- Contact where appropriate.

---

# 113. 404 Must Not Expose Private Existence

Request to private/unavailable resource MAY produce safe unavailable/not-found behaviour according to Security rules.

---

# 114. 500/Error Page

Should provide:

- understandable message;
- retry/home path;
- global identity if safe.

No raw infrastructure details.

---

# 115. Offline/Network Failure

Where app architecture supports client-side state, safe retry can be offered.

Public navigation should not imply successful navigation if route content failed completely.

---

# 116. Authentication Is Not Part of Main Public Navigation

Admin login MAY be available separately.

Public navigation SHOULD not foreground administrative access.

---

# 117. Admin Link

If public site includes an Admin link, it SHOULD be subtle/secondary.

Security never relies on hiding the link.

---

# 118. Navigation and Public Builder Privacy

Builder session identifier MUST NOT be used as authorization for private Profile data.

---

# 119. Navigation and Temporary Casting Links

Private or semi-private casting-specific links, if implemented later, MUST have explicit access model.

They are not automatically listed in global navigation.

---

# 120. Private Link Discoverability

Private/casting-specific pages:

- SHOULD NOT appear in public search/index/navigation unless designed as public;
- SHOULD follow SEO noindex/access rules as appropriate.

---

# 121. Navigation and SEO

Stable public routes SHOULD support:

- descriptive page titles;
- canonical URLs;
- localized alternate relationships where applicable.

---

# 122. Navigation Labels and SEO Titles

Menu label may be concise:

```text
Проекты
```

while page SEO title can be richer.

They are not required to be identical.

---

# 123. Slugs

Public slug SHOULD be:

- stable;
- human-readable where appropriate;
- URL-safe;
- independent of database numeric ID where practical.

---

# 124. Slug Change

If a published slug changes, redirect strategy SHOULD be considered to preserve shared links.

Exact policy defined in SEO/Architecture.

---

# 125. Actor Name in URL

Product MAY use single-profile root URLs without actor slug because system currently targets one Actor Profile.

Architecture SHOULD not unnecessarily duplicate actor identity in every path unless future multi-profile strategy demands it.

---

# 126. Public Navigation Accessibility

Global navigation MUST use semantic navigation landmark.

Conceptually:

```html
<nav aria-label="Основная навигация">
```

Exact markup implementation follows frontend stack.

---

# 127. Navigation Keyboard Support

All items MUST be keyboard reachable.

---

# 128. Menu Trigger

Mobile menu trigger MUST:

- be semantic button;
- expose expanded/collapsed state;
- have accessible name.

---

# 129. Focus Management

Opening menu:

- focus enters menu;
- background interaction controlled appropriately.

Closing:

- focus returns to trigger.

---

# 130. Escape

Overlay/drawer navigation SHOULD close on Escape where pattern supports it.

---

# 131. Screen Reader Current Page

Active navigation item SHOULD expose current state semantically where appropriate.

---

# 132. Language Selector Accessibility

Locale options MUST have understandable labels.

Avoid icon-only flag controls as sole representation, because country ≠ language.

---

# 133. Flags

Flags MAY complement labels but SHOULD NOT be the only language indication.

Prefer:

```text
RU
EN
```

or full language labels.

---

# 134. Touch Targets

Mobile navigation controls MUST meet accessibility touch-size requirements defined later.

---

# 135. Menu Scroll

If secondary menu exceeds viewport, menu itself must remain scrollable without trapping user.

---

# 136. Motion

Menu animation SHOULD respect reduced-motion preference.

---

# 137. Navigation Contrast

Theme cannot reduce navigation/control contrast below accessibility thresholds.

---

# 138. Current Section Visibility

Current/active state must not be indicated by color alone.

---

# 139. Public Navigation Responsive Model

## Large Desktop

```text
Horizontal primary navigation
+ CTA
+ Locale
```

## Tablet

```text
Compact horizontal or hybrid menu
+ CTA
```

## Mobile

```text
Compact header
+ menu trigger
+ optional sticky professional actions
```

---

# 140. Breakpoint Principle

Navigation behaviour changes because of space/user interaction, not arbitrary device names.

Exact breakpoints are defined in Responsive Behaviour document.

---

# 141. Tablet

Tablet MAY retain horizontal navigation if items remain usable.

Do not force mobile drawer solely because viewport is technically tablet.

---

# 142. Mobile Menu Information Hierarchy

Primary group:

```text
Профиль
Портфолио
Эмоции
Проекты
Анкета
Контакты
```

Secondary group:

```text
Обучение
Достижения
Блог
Социальные ссылки
```

where available.

---

# 143. Public Search in Mobile Menu

Search MAY be exposed near top of menu.

But Questionnaire/Contact should not be pushed below excessively long secondary items.

---

# 144. CTA Duplication

It is acceptable to have:

```text
Questionnaire menu item
+
Questionnaire CTA button
```

because one is navigation and one is conversion emphasis.

---

# 145. Avoid Excessive CTA Duplication

Do not show:

```text
Questionnaire
Questionnaire button
Questionnaire sticky bar
Questionnaire banner
Questionnaire popup
```

all simultaneously without reason.

---

# 146. Navigation Scroll Behaviour

Sticky Header MAY reduce navigation friction.

If used, it should:

- remain compact;
- not consume excessive vertical space;
- behave predictably on mobile.

---

# 147. Auto-Hiding Header

Scroll-based hide/show MAY be used, but must not make professional actions difficult to find.

---

# 148. Header on Video

Fullscreen video MAY temporarily hide site navigation according to native behaviour.

Upon exit, context should return.

---

# 149. External Link Navigation

External professional links SHOULD indicate external destination where useful.

---

# 150. Target Blank

Opening external links in new tab MAY be appropriate for professional media, but should follow consistent policy.

Do not indiscriminately force every internal link into new tabs.

---

# 151. PDF Navigation

Clicking Download should initiate/download/open according to platform conventions.

Clickable HTML Questionnaire remains separate from PDF.

---

# 152. QR Navigation

QR target is part of offline-to-online navigation.

It should resolve to a meaningful semantic destination.

---

# 153. QR to Current Profile

For generic questionnaire/package, default useful QR target MAY be official current Profile.

---

# 154. QR to Media

Video/audio link QR MAY lead directly to that approved resource when configured.

---

# 155. QR Must Not Depend on Admin Session

Obvious but normative:

public QR target works without authenticated Admin session unless intentionally private casting access model applies.

---

# 156. Navigation and Current vs Historical

Historical Questionnaire PDF MAY link to current profile.

UI should make clear:

```text
Document generated on X
Current profile available here
```

---

# 157. Navigation and Archive

Archived public entities SHOULD generally no longer appear in standard navigation/listing.

Existing historical links follow archive/redirect policy.

---

# 158. Archived Project Link

Possible behaviours depending later lifecycle policy:

- read-only archive page;
- redirect;
- not found.

Must be deterministic and documented.

---

# 159. Navigation and Draft

Draft content never appears in Public navigation.

---

# 160. Navigation and Published but Non-Public

A Published entity with non-public visibility does not appear in general Public navigation.

---

# 161. Navigation and Public Builder Permissions

An item visible on Public Site is not automatically selectable in Public Builder.

Navigation/UI must respect independent flags.

---

# 162. Add-to-Builder Actions

When Builder session is active, eligible public items MAY expose:

```text
Добавить в анкету
```

---

# 163. Add-to-Builder Eligibility

Action appears only when item is:

```text
publicly visible as applicable
AND
allowed_in_public_questionnaire_builder
AND
available
```

---

# 164. Add-to-Builder Feedback

After adding:

```text
Добавлено в анкету
[Продолжить]
```

with reversible remove action where appropriate.

---

# 165. Ineligible Builder Item

Do not display:

```text
Нельзя добавить, потому что admin запретил
```

if that reveals unnecessary policy details.

Simply omit add action.

---

# 166. Builder Navigation Safety

Navigating away SHOULD not accidentally submit/generate.

---

# 167. Unsaved Builder Changes

Because selection is session-based, system SHOULD persist selection as user makes choices or otherwise warn before destructive exit.

Exact persistence model later.

---

# 168. Navigation and Feedback Form

Contextual CTA MAY open:

- inline section;
- dialog/sheet;
- dedicated route.

Choice depends on form complexity and device.

---

# 169. Form Route Preferred for Long/Attachment Workflow

If request supports multiple attachments/detail, dedicated page may be more reliable than small modal.

---

# 170. Modal Feedback Form

For short inquiry modal/sheet MAY reduce friction.

Must support:

- deep accessibility;
- mobile keyboard;
- error recovery.

---

# 171. Success Navigation After Feedback

After submit, user SHOULD remain in meaningful context.

Possible:

```text
success confirmation
+
reference number
+
back to profile/questionnaire
```

Do not redirect randomly to Home without reason.

---

# 172. Multiple Professional CTAs

Relevant types:

```text
Invite to Casting
Offer Role
Request Materials
Collaboration
Question
```

Navigation should not expose all simultaneously everywhere.

---

# 173. CTA Selection Strategy

Context picks 1–2 relevant actions.

Additional options can exist under:

```text
Other professional inquiry
```

if needed.

---

# 174. Navigation and Opportunity Pipeline

Opportunity Pipeline is private Admin functionality.

Public navigation does not expose internal opportunity stages.

---

# 175. Navigation and Casting AI

Casting AI is Admin-only.

No public menu item:

```text
AI Casting Analysis
```

---

# 176. Navigation and BB Assistant

BB Assistant is Admin-only.

---

# 177. Navigation and Theme AI

Admin-only.

---

# 178. Navigation and Virtual Operator

Admin-only.

---

# 179. Public Site Should Not Feel Like CMS

Public navigation and URL/content presentation must remain clean professional experience regardless of backend complexity.

---

# 180. Navigation Performance

Header/menu interaction MUST remain responsive even if page media is still loading.

---

# 181. Navigation JavaScript Failure

Where technical architecture allows server-rendered links, critical navigation SHOULD remain usable without relying on heavy client initialization.

---

# 182. Prefetching

Internal route prefetch MAY improve responsiveness but must not:

- fetch private data;
- overload network unnecessarily;
- prefetch huge media.

---

# 183. Navigation Analytics

Useful events MAY include:

```text
public_nav_clicked
questionnaire_nav_clicked
portfolio_nav_clicked
contact_nav_clicked
language_switched
mobile_menu_opened
```

But analytics should focus on meaningful behaviour, not every trivial hover.

---

# 184. Navigation Event Context

MAY include:

- source page;
- destination;
- device class;
- locale.

No unnecessary PII.

---

# 185. Navigation Analytics Failure

Must never block navigation.

---

# 186. NAV-PUB-001

Global Public Navigation MUST expose a direct path to Profile/Home.

---

# 187. NAV-PUB-002

Global Public Navigation MUST expose Portfolio when public Portfolio content exists.

---

# 188. NAV-PUB-003

Global Public Navigation SHOULD expose Emotional Range when public Emotional content exists.

---

# 189. NAV-PUB-004

Global Public Navigation SHOULD expose Projects when public Projects exist.

---

# 190. NAV-PUB-005

Questionnaire MUST remain a high-priority public destination.

---

# 191. NAV-PUB-006

Professional Contact MUST remain reachable from all critical public Journeys.

---

# 192. NAV-PUB-007

Directly opened public detail pages MUST preserve Actor Identity and continuation actions.

---

# 193. NAV-PUB-008

Language switching MUST preserve logical destination where supported.

---

# 194. NAV-PUB-009

Navigation MUST NOT reveal hidden/private content.

---

# 195. NAV-PUB-010

Public Builder MUST exist within Questionnaire navigation context.

---

# 196. NAV-PUB-011

Active Builder Session SHOULD survive normal permitted public navigation until expiry.

---

# 197. NAV-PUB-012

Primary public navigation MUST remain usable on mobile.

---

# 198. NAV-PUB-013

Critical functionality MUST NOT require hover.

---

# 199. NAV-PUB-014

Browser Back/Forward SHOULD behave predictably.

---

# 200. NAV-PUB-015

Navigation MUST remain functional when optional analytics/AI services fail.

---

# 201. NAV-PUB-016

Empty/unpublished secondary sections SHOULD NOT create misleading navigation entries.

---

# 202. NAV-PUB-017

Deep links MUST NOT become dead ends.

---

# 203. NAV-PUB-018

Public navigation labels MUST use canonical professional terminology.

---

# 204. NAV-PUB-019

Primary CTA and navigation links MUST remain distinguishable concepts.

---

# 205. NAV-PUB-020

Private/admin routes MUST NOT appear in public navigation based merely on their existence in route tree.

---

# 206. Persona Navigation Mapping

| Persona | Primary navigation preference |
|---|---|
| Casting Director | Profile → Video/Emotional → Questionnaire |
| Casting Assistant | Profile/Search → Portfolio → Questionnaire |
| Commercial Producer | Portfolio → Emotional → Video → CTA |
| Director / Producer | Video → Emotional → Projects → Request Materials |
| International Casting | Locale → Profile → Questionnaire |
| Generic Professional Visitor | Profile → relevant evidence → CTA |

---

# 207. Navigation by Entry Source

## Search

Likely path:

```text
Deep Result
→ Context
→ Questionnaire / Contact
```

## Social

```text
Visual Content
→ Profile / Video
→ CTA
```

## Shared Questionnaire

```text
Questionnaire
→ Current Profile
→ Contact
```

## QR

```text
Specific Target
→ Current Digital Journey
```

---

# 208. Navigation by Device

## Desktop

Fast random access among sections.

## Mobile

Sequential professional flow + quick actions.

This does not mean mobile has fewer capabilities.

---

# 209. Navigation Hierarchy Test

A primary destination should usually be reachable in:

```text
≤ 1 navigation action
```

from global navigation.

---

# 210. Professional Action Test

Questionnaire/Contact/Video should generally be reachable:

```text
0–1 actions
```

from major public pages through global/contextual controls.

---

# 211. Navigation Depth Guideline

Avoid public hierarchy deeper than necessary.

Typical:

```text
Section
→ Detail
```

is preferred.

Depth:

```text
Section
→ Subsection
→ Category
→ Detail
→ Media
```

requires strong justification.

---

# 212. Navigation Consistency

Same label must lead to same conceptual destination.

`Questionnaire` cannot mean:

- PDF download on one page;
- builder on another;
- admin form elsewhere.

---

# 213. Questionnaire Terminology

Recommended conceptual hierarchy:

```text
Актёрская анкета
├── Рекомендуемая версия
├── Другие версии
└── Собрать свою
```

---

# 214. Emotional Terminology

Public label should be understandable to casting professionals.

Possible:

```text
Эмоциональный диапазон
```

rather than internal:

```text
Emotional Grid Management
```

---

# 215. Portfolio Terminology

Avoid vague:

```text
Gallery
```

as sole professional label if `Portfolio` better communicates purpose.

---

# 216. Projects Terminology

Use `Projects` for confirmed professional work.

Do not mix Castings into same public section.

---

# 217. Training Terminology

Use `Training / Обучение`, not legacy `Other Projects`.

---

# 218. Public Navigation Anti-Pattern NAV-AP-001

**Everything in menu**

15–20 primary items.

---

# 219. NAV-AP-002

**Critical actions in footer only**

---

# 220. NAV-AP-003

**Biography as navigation gate**

User must read/scroll before accessing Portfolio.

---

# 221. NAV-AP-004

**Deep-link orphan**

Project page without global context.

---

# 222. NAV-AP-005

**Locale reset**

Language switch sends user to Home.

---

# 223. NAV-AP-006

**Private disabled menu item**

Public user sees name of private content with lock.

---

# 224. NAV-AP-007

**Mobile horizontal overflow menu**

Desktop nav squeezed beyond viewport.

---

# 225. NAV-AP-008

**Hover-only dropdown**

Touch/keyboard users cannot access children.

---

# 226. NAV-AP-009

**Every portfolio filter is a header menu item**

Overloads global hierarchy.

---

# 227. NAV-AP-010

**Builder as separate product**

User leaves actress site context and cannot return easily.

---

# 228. NAV-AP-011

**Infinite nested menu**

Projects → Years → Types → Roles → Media as header hierarchy.

---

# 229. NAV-AP-012

**Unstable primary nav**

Items reorder unpredictably based on analytics/AI.

---

# 230. NAV-AP-013

**Search as sole navigation**

User must know what to type to access main content.

---

# 231. NAV-AP-014

**Home-only CTA**

Direct Project visitor cannot contact/invite.

---

# 232. NAV-AP-015

**Flag-only language selector**

Country flag used as sole language label.

---

# 233. NAV-AP-016

**Admin concepts in Public menu**

`Revisions`, `Media Library`, `AI Drafts`.

---

# 234. NAV-AP-017

**External social links dominate header**

Social platforms should not displace core professional destinations.

---

# 235. NAV-AP-018

**404 dead end**

No route back to profile.

---

# 236. Navigation Readiness Checklist

Before releasing public navigation verify:

- [ ] Actor identity links to root/profile.
- [ ] Portfolio reachable.
- [ ] Emotional Range reachable if published.
- [ ] Projects reachable if published.
- [ ] Questionnaire prominent.
- [ ] Professional Contact reachable.
- [ ] Video accessible without deep navigation.
- [ ] direct Project links retain context.
- [ ] direct Questionnaire links retain context.
- [ ] locale switching preserves page.
- [ ] mobile menu keyboard/touch accessible.
- [ ] no private items in payload/menu.
- [ ] active section visible.
- [ ] 404 has recovery.
- [ ] empty sections handled.
- [ ] Builder can return to Questionnaire/Profile.
- [ ] Builder session preserved where required.
- [ ] Header remains usable while content loads.
- [ ] analytics failure does not block links.

---

# 237. Navigation Acceptance Criteria

Public Navigation считается корректной, если:

1. пользователь понимает текущий section;
2. Questionnaire доступна без поиска в footer;
3. Contact доступен из любого критического Journey;
4. Portfolio и Emotional Range легко находятся;
5. Project detail не является dead end;
6. direct deep links работают как самостоятельные Entry Points;
7. selected locale сохраняется;
8. mobile имеет полный набор critical destinations;
9. active Builder session не теряется при допустимой навигации;
10. hidden data не раскрываются;
11. current section имеет accessible indication;
12. browser navigation остаётся predictable;
13. optional services не блокируют navigation;
14. global menu не перегружен secondary content;
15. professional CTA остаётся контекстным и понятным.

---

# 238. Рекомендуемая desktop navigation model

```text
┌──────────────────────────────────────────────────────────────────────────┐
│ Екатерина ...   Профиль  Портфолио  Эмоции  Проекты  Анкета  Контакты │
│                                                          RU / EN        │
└──────────────────────────────────────────────────────────────────────────┘
```

Дополнительно MAY использоваться отдельный CTA:

```text
[Пригласить на кастинг]
```

в соответствующем context.

---

# 239. Рекомендуемая mobile navigation model

```text
┌─────────────────────────────┐
│ Екатерина ...      EN   ☰   │
└─────────────────────────────┘

MENU
───────────────────────────────
Профиль
Портфолио
Эмоциональный диапазон
Проекты
Актёрская анкета
Контакты

Дополнительно
Обучение
Достижения
Блог
───────────────────────────────
```

При необходимости:

```text
┌─────────────────────────────┐
│ Видео │ Анкета │ Связаться  │
└─────────────────────────────┘
```

как отдельный sticky professional action layer.

---

# 240. Итоговая публичная информационно-навигационная модель

```text
                         PUBLIC ENTRY
                              │
               ┌──────────────┼──────────────┐
               │              │              │
             Home         Deep Link         QR
               │              │              │
               └──────────────┼──────────────┘
                              ↓
                         ACTOR CONTEXT
                              ↓
        ┌──────────┬──────────┼──────────┬──────────┐
        │          │          │          │          │
    Portfolio   Emotional   Projects   Video   Quick Facts
        │          │          │          │          │
        └──────────┴──────────┼──────────┴──────────┘
                              ↓
                       QUESTIONNAIRE HUB
                         /            \
               Prepared Version     Builder
                         \            /
                              ↓
                    PROFESSIONAL PACKAGE
                              ↓
                   PROFESSIONAL ACTION
                              ↓
                 Contact / Casting / Request
```

---

# 241. Definition of Public Navigation Compliance

Public navigation соответствует целевому продукту, если она:

1. организована вокруг профессиональных задач;
2. не отражает внутреннюю CMS hierarchy;
3. обеспечивает быстрый доступ к P0 content;
4. поддерживает direct deep links;
5. не создаёт dead ends;
6. поддерживает Questionnaire as core destination;
7. поддерживает contextual CTA;
8. полноценно работает на Mobile;
9. сохраняет locale/context;
10. соблюдает Public/Private boundary;
11. доступна keyboard/screen-reader users;
12. остаётся стабильной и предсказуемой;
13. не требует обхода Home для продолжения профессионального Journey.

---

# 242. Финальный принцип

> **Публичная навигация должна вести пользователя не по структуре сайта, а по логике профессионального решения: понять актрису, проверить визуальные и профессиональные доказательства, получить подходящую анкету и выполнить следующее профессиональное действие. Любая публичная страница должна оставаться полноценной точкой входа, сохранять контекст актрисы и давать понятный путь вперёд.**