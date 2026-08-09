# PUBLIC QUESTIONNAIRE BUILDER UX

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная UX-спецификация публичного конструктора актёрской анкеты

**Целевой файл:** `docs/ux/public-questionnaire-builder.md`  
**Документ:** DOC-045  
**Статус:** ✅ Completed  
**Тип:** UX / Public Questionnaire Builder

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/overview.md`
- `docs/customer-journey/casting-director.md`
- `docs/customer-journey/casting-assistant.md`
- `docs/customer-journey/commercial-producer.md`
- `docs/customer-journey/international-casting.md`
- `docs/customer-journey/conversion-funnel.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/ux-principles.md`
- `docs/ux/public-navigation.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/admin-user-flows.md`

---

# 1. Назначение документа

Настоящий документ определяет полный UX-контракт публичного конструктора актёрской анкеты.

Public Questionnaire Builder предназначен для профессионального пользователя — Casting Director, Casting Assistant, Producer, Director или International Casting Specialist — которому готовая анкета актрисы подходит не полностью и требуется собственная **временная подборка из уже разрешённых профессиональных данных**.

Главный принцип:

> **Пользователь настраивает представление анкеты, а не редактирует профиль актрисы.**

---

# 2. Product Positioning

Public Builder является вторым уровнем после Prepared Questionnaire.

Целевая модель:

```text
Prepared Questionnaire
        ↓
    Подходит?
    /      \
  YES       NO
   │         │
Download   Customize
             │
             ↓
       Public Builder
```

Public Builder MUST NOT становиться обязательным этапом стандартного professional journey.

---

# 3. UX Goal

Профессиональный пользователь должен иметь возможность:

```text
Choose useful starting point
        ↓
Select relevant content
        ↓
Verify result
        ↓
Generate professional PDF
```

без:

- регистрации;
- знания структуры CMS;
- ручной верстки документа;
- повторного ввода данных актрисы;
- доступа к private data.

---

# 4. UX Success Target

Типовой пользователь SHOULD иметь возможность получить кастингово-релевантный документ примерно за:

```text
30–90 seconds
```

если базовая Prepared Questionnaire уже близка к его задаче.

Сложный Extended/Casting сценарий MAY занимать больше времени.

---

# 5. Builder Is Not

Public Builder MUST NOT восприниматься как:

- Word Processor;
- Canva-like design editor;
- actor profile editor;
- document management system;
- user account workspace;
- permanent casting CRM;
- AI-generated CV service.

---

# 6. Builder Terminology

Canonical technical term:

```text
Public Questionnaire Builder
```

Допустимые Russian UI labels:

```text
Собрать анкету
Настроить анкету
Собрать анкету под кастинг
```

Preferred contextual CTA:

```text
Настроить эту версию под себя
```

если Builder запускается из Prepared Questionnaire.

---

# 7. UX IDs

Требования документа используют prefix:

```text
PQB-UX-*
```

---

# 8. Primary Personas

### Primary

```text
PERSONA-CD
PERSONA-CA
PERSONA-INT
```

### Secondary

```text
PERSONA-CP
PERSONA-DIR
```

---

# 9. Primary Use Cases

```text
PQB-UC-01
Сократить стандартную анкету.

PQB-UC-02
Добавить релевантный Project.

PQB-UC-03
Добавить конкретный Skill.

PQB-UC-04
Сделать Casting-specific package.

PQB-UC-05
Создать English/localized PDF.

PQB-UC-06
Выбрать конкретные дополнительные фотографии.

PQB-UC-07
Добавить Emotional Grid.

PQB-UC-08
Исключить нерелевантное Training/Achievements.

PQB-UC-09
Выбрать разрешённый Contact.

PQB-UC-10
Подготовить компактный документ для передачи следующему decision maker.
```

---

# 10. Entry Points

Builder MUST быть доступен минимум из:

```text
Questionnaire Hub
Prepared Questionnaire
```

MAY также запускаться из:

- Casting-specific public link;
- Profile CTA;
- active Builder indicator;
- eligible public content context.

---

# 11. Preferred Entry

Наиболее эффективный UX:

```text
Prepared Questionnaire
        ↓
[Настроить эту версию под себя]
```

Пользователь получает уже полезную configuration и только меняет нужное.

---

# 12. Secondary Entry

```text
Questionnaire Hub
 ↓
[Собрать анкету под свой кастинг]
```

Затем пользователь выбирает Template.

---

# 13. No Registration Gate

До:

```text
Template selection
Content configuration
Preview
PDF generation
```

не должно требоваться создание публичного аккаунта.

---

# 14. Builder Ownership Message

На первом экране SHOULD быть кратко объяснено:

```text
Вы создаёте собственную временную версию анкеты
из разрешённых данных официального профиля.
Исходный профиль актрисы не изменяется.
```

Не требуется длинный legal-style текст.

---

# 15. Builder Core Stages

Logical flow:

```text
1. Starting Point
2. Content
3. Items
4. Preview
5. Generate
```

На Desktop некоторые этапы MAY быть объединены визуально.

---

# 16. Desktop and Mobile May Differ

Desktop MAY использовать:

```text
Configuration panel
+
Live preview
```

Mobile MAY использовать:

```text
Step-by-step flow
```

Logical data model остаётся одинаковым.

---

# 17. Stage PQB-01 — Starting Point

Пользователь выбирает:

```text
Recommended Current Version
Quick
Standard
Extended
Casting
```

только из доступных вариантов.

---

# 18. Prepared Questionnaire Starting Point

Если Builder открыт из Prepared Questionnaire:

выбор стартового шаблона MAY быть пропущен.

Flow:

```text
Prepared Questionnaire
 ↓
Customize
 ↓
Builder loaded with equivalent allowed configuration
```

---

# 19. Template Cards

Каждый Template SHOULD иметь:

- Name;
- one-sentence purpose;
- approximate content scope;
- recommended use.

Например:

```text
Quick
Для быстрого первичного отбора.
Основные фото, параметры, языки, навыки, видео и контакт.
```

---

# 20. Template Names Must Explain Use

Не использовать только:

```text
Template A
Template B
Template C
```

---

# 21. Template — Quick

Baseline purpose:

> быстрый screening / shortlist.

Suggested content:

```text
Basic Information
Primary Close-Up
Primary Full Body
Professional Parameters
Languages
Key Skills
Video
Allowed Contact
```

---

# 22. Template — Standard

Quick +:

```text
Emotional Grid
Selected Projects
Training summary
```

according to allowed/default configuration.

---

# 23. Template — Extended

Broader set of allowed professional data.

Does not mean:

```text
everything in the database
```

Only Public Builder-allowed content.

---

# 24. Template — Casting

Designed for a specific casting context.

MAY additionally support temporary fields:

```text
Casting Name
Project Name
Role Name
```

---

# 25. Temporary Casting Context

These values MUST be clearly scoped as:

```text
Document Context
```

They MUST NOT create:

- Casting entity;
- Project;
- Role;
- Profile Fact.

---

# 26. Example UI

```text
Анкета для кастинга

Название кастинга
[_____________________]

Проект
[_____________________]

Роль
[_____________________]
```

with explanatory helper text:

```text
Эти данные используются только в вашей версии документа.
```

---

# 27. Stage PQB-02 — Section Selection

After starting point, user sees logical blocks.

---

# 28. Supported Blocks

Conceptually:

```text
Basic Information
Primary Close-Up
Primary Full Body
Additional Portfolio
Appearance / Parameters
Skills
Languages
Emotional Range
Projects
Roles
Training
Achievements
Video
Audio
Professional Links
Contacts
```

---

# 29. Block-Level Control

Each optional block MAY have:

```text
Included / Excluded
```

state.

Example:

```text
☑ Языки
☑ Навыки
☑ Эмоциональный диапазон
☐ Обучение
```

---

# 30. Required Blocks

Template MAY define blocks as required.

Required blocks MUST NOT look like ordinary removable selections.

Example:

```text
Основная информация
Обязательно
```

---

# 31. Required Does Not Mean Hardcoded Globally

Requirements may differ by Template.

Example:

`Full Body` MAY be required in all professional templates.

`Training` should generally remain optional.

Exact template rules are Admin-configurable within Business Rules.

---

# 32. Minimal Document Requirements

Generated document MUST contain at least:

```text
Actor Identification
At least one valid primary professional photo
Official Profile Link
```

plus any globally mandatory information defined later in Questionnaire module.

---

# 33. Stage PQB-03 — Item-Level Selection

Enabling a block MAY expose individual items.

Example:

```text
Projects
  ☑ Project A
  ☐ Project B
  ☑ Project C
```

---

# 34. Item Selection Is Core Requirement

Builder MUST support selection of individual eligible items, not only entire sections.

Especially for:

```text
Portfolio
Skills
Languages
Projects
Training
Achievements
Video
Audio
Links
Contacts
```

where applicable.

---

# 35. Item Eligibility

The browser MUST receive only eligible records.

Eligibility conceptually requires:

```text
belongs_to_profile = true
AND
available = true
AND
allow_in_public_questionnaire_builder = true
AND
other applicable publication/security rules = true
```

---

# 36. Public Site Visibility Is Independent

An item can conceptually be:

```text
show_on_public_site = false
allow_in_public_questionnaire_builder = true
```

if Business Rules explicitly permit this controlled projection.

Therefore Builder MUST use its own server projection.

---

# 37. Prepared Questionnaire Permission Is Also Independent

Three independent contexts MUST remain conceptually separate:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 38. Client Must Not Receive Ineligible Items

Prohibited model:

```text
API returns all contacts
→ private contacts disabled/hidden in UI
```

Correct model:

```text
API returns only Builder-eligible contacts
```

---

# 39. Stage — Primary Photos

Primary Close-Up and Full Body SHOULD be included automatically when required by Template.

---

# 40. Primary Photo Replacement

Public Builder SHOULD NOT allow visitor to redefine which photo is `Primary`.

If Template permits additional photo selection:

user chooses from eligible additional Portfolio Items.

---

# 41. Additional Portfolio Selection

UX SHOULD show:

- thumbnail;
- category;
- shooting date where useful;
- selected state.

---

# 42. Professional Categories

Portfolio selection MAY be grouped by:

```text
Close-Up
Full Body
Profile
Three-Quarter
Staged
Other
```

---

# 43. Limits

Admin Template MAY specify:

```text
Max additional photos: N
```

When limit reached:

UI explains:

```text
Выбрано максимальное количество: N
```

rather than silently ignoring selection.

---

# 44. Limits Are Template Rules

User MUST NOT manipulate client request to exceed limits.

Server revalidates.

---

# 45. Skills UX

Skills MAY be grouped by category.

Example:

```text
Навыки

Вождение
☑ Вождение автомобиля — уровень ...

Спорт
☑ Плавание
☐ ...
```

Exact level labels follow domain model.

---

# 46. Search Within Selection

For long lists such as Skills/Projects, Builder MAY provide local search/filter.

This search operates only on eligible Builder projection.

---

# 47. Languages UX

Each language SHOULD show:

```text
Language
Level / CEFR
```

Example:

```text
☑ English — B2
☑ Russian — Native
```

---

# 48. Language Value Is Not Editable

Public user may include/exclude the Language record.

Cannot change:

```text
B2 → C1
```

---

# 49. Emotional Range UX

Builder SHOULD primarily offer:

```text
Approved Emotional Grid
```

rather than forcing selection of individual Emotional Portfolio images.

This aligns with canonical questionnaire model.

---

# 50. Emotional Grid Information

Selection MAY show:

- Grid preview;
- shooting date;
- primary marker.

---

# 51. Questionnaire Emotional Rule

When Emotional content is included in Questionnaire:

preferred content:

```text
Composite Grid
+
Shooting Date
+
Clickable link to Full Emotional Portfolio
```

Individual emotional photographs MUST NOT automatically flood the Questionnaire.

---

# 52. Projects UX

Project item SHOULD show concise professional information:

```text
Project Title
Type
Role
Year/date where relevant
```

---

# 53. Project Selection

User MAY select only specific relevant Projects.

This is especially important for Casting/Director use cases.

---

# 54. Roles UX

If Role can be selected independently according to future module rules:

its parent Project MUST remain visible in selection context.

Never show orphan:

```text
Anna
```

without Project context.

---

# 55. Training UX

Training is optional by default for many Templates.

Selection item SHOULD show:

- Institution;
- Course;
- dates.

---

# 56. Achievements UX

Same principle:

- title;
- issuer/context;
- date if relevant.

---

# 57. Video UX

Eligible Video selection SHOULD clearly differentiate:

```text
Video Intro
Showreel
Project Video
Other Professional Video
```

---

# 58. Video Selection Output

The generated Questionnaire MAY include:

```text
Human-readable label
Clickable hyperlink
QR where enabled
```

not embedded full media file unless separate module explicitly defines otherwise.

---

# 59. Audio UX

Same pattern:

```text
Description
Clickable Link
QR if configured
```

---

# 60. Professional Links UX

External link editor source structure remains:

```text
Number
Description
URL
```

Builder displays eligible links by description.

---

# 61. Contact UX

Eligible Contacts MUST display enough context for user to select appropriately.

Example:

```text
☑ Professional WhatsApp
☐ Manager Phone
```

only if both are permitted for Builder.

---

# 62. Guardian / Parent Contacts

These MUST NOT appear unless explicitly:

```text
allow_in_public_questionnaire_builder = true
```

and all applicable privacy rules allow it.

---

# 63. Contacts Default

Template SHOULD use a safe professional contact default rather than exposing every available Contact.

---

# 64. Selection States

Each selectable item should support:

```text
Selected
Not selected
Unavailable / removed only where needed after session change
Required
```

---

# 65. Selection Feedback

User action should have immediate visible response.

Example:

```text
Project added
```

or selected card state.

---

# 66. Undo

Selection must be easily reversible before generation.

---

# 67. Stage PQB-04 — Order

Content order is primarily defined by Template.

User reordering MAY be enabled by Admin configuration.

---

# 68. Reordering Is Optional

Builder can be production-ready without arbitrary reordering if Template ordering solves the professional use case.

---

# 69. If Reordering Is Enabled

MUST support non-drag alternative:

```text
Move up
Move down
```

or accessible equivalent.

Drag-and-drop cannot be the only method.

---

# 70. Protected Ordering

Some structural constraints MAY prevent moving certain blocks.

Example:

```text
Actor Identity
```

always first.

If so, UI MUST communicate this.

---

# 71. No Visual Design Controls

Public user MUST NOT control:

- fonts;
- brand colors;
- logo position;
- exact column width;
- margins;
- line-height;
- QR visual style beyond system-approved configuration;
- arbitrary CSS.

---

# 72. Why No Design Controls

The document must remain:

```text
Professional
Consistent
Accessible
Share-ready
Brand-controlled
```

---

# 73. Stage PQB-05 — Locale

Builder MUST inherit current public locale.

---

# 74. Locale Selector

If supported languages > 1, user MAY switch output locale.

Changing locale modifies:

```text
UI labels
approved localized text
preview
PDF
```

not Source Facts.

---

# 75. Locale Persistence

Selected locale persists throughout:

```text
Builder
→ Profile browsing
→ Return
→ Preview
→ PDF
```

until user changes it.

---

# 76. Missing Translation Behaviour

If an optional item lacks required localization:

system follows documented fallback policy.

It MUST NOT silently produce invented AI translation.

---

# 77. International Template UX

For international use, Location and Languages SHOULD remain prominent.

---

# 78. Stage PQB-06 — Live Summary

Builder SHOULD continuously communicate current result.

Example Desktop summary:

```text
Ваша анкета

12 элементов
2 фото
2 языка
4 навыка
2 проекта

Язык: English
```

---

# 79. Live Page Count

Approximate page count MAY be shown if reliable.

It MUST be clearly approximate if exact final PDF pagination is not yet known.

---

# 80. Avoid False Precision

Do not show:

```text
Exactly 4 pages
```

unless renderer can guarantee it.

---

# 81. Builder Indicator Outside Builder

If active session exists:

```text
Моя анкета · 12 элементов
[Продолжить]
```

MAY appear on allowed public pages.

---

# 82. Indicator Behaviour

It should:

- be unobtrusive;
- disappear after expiry;
- not display private details;
- link back to Builder.

---

# 83. Add From Public Profile

When active Builder exists, eligible content MAY offer:

```text
Добавить в анкету
```

---

# 84. Example

```text
Project Detail
[Добавить проект в мою анкету]
```

only if Project eligible.

---

# 85. Add Action Confirmation

After add:

```text
Добавлено в анкету
[Продолжить настройку]
```

---

# 86. Remove From Public Context

If item already selected, action MAY become:

```text
Убрать из анкеты
```

---

# 87. Stage PQB-07 — Preview

Preview is mandatory before final generation or at minimum easily accessible.

---

# 88. Preview Goal

Preview answers:

```text
Что войдёт в документ?
В правильном ли порядке?
На правильном ли языке?
Все ли ссылки/фото ожидаемы?
```

---

# 89. Preview Is Not Layout Editor

User can return and change content selections.

Cannot directly drag PDF text blocks inside Preview unless future scope explicitly changes.

---

# 90. Preview Uses Server-Valid Data

Preview SHOULD be generated from server-authoritative revalidated configuration.

---

# 91. Preview Revalidation

Before preview, server checks:

```text
ownership
permission
availability
template limits
required blocks
```

---

# 92. Removed Item Between Selection and Preview

If Admin has revoked access:

system SHOULD show neutral message:

```text
Один из выбранных материалов больше недоступен
и был исключён из анкеты.
```

or require user review depending impact.

Do not reveal privacy reason.

---

# 93. Blocking Change

If removed item was mandatory:

Preview/Generate must block until valid required content exists.

---

# 94. Preview Device Modes

Builder MAY offer:

```text
Desktop preview
Mobile preview
```

for HTML representation.

For PDF, page-oriented preview is more appropriate.

---

# 95. PDF Preview

Where technically feasible, user SHOULD be able to preview generated-document representation before final download.

---

# 96. Stage PQB-08 — Generate

Primary CTA:

```text
Сформировать PDF
```

or:

```text
Скачать PDF
```

depending implementation.

---

# 97. Generate Is User-Triggered

No automatic PDF generation after every checkbox change.

---

# 98. Server Authoritative Generation

Generation flow:

```text
Client configuration
 ↓
Server loads Builder Session
 ↓
Server revalidates permissions
 ↓
Server creates generation snapshot
 ↓
PDF render
 ↓
Output
```

---

# 99. Generation Snapshot

The generated document SHOULD be based on a stable snapshot of the selected valid data at generation time.

This prevents internal inconsistency during render.

---

# 100. Generated Date

PDF MUST include Generated Date.

---

# 101. Official Source

PDF MUST include stable official Profile URL.

---

# 102. File Name

Conceptual format:

```text
actor-name_template_language.pdf
```

or for Casting:

```text
actor-name_casting-role_language.pdf
```

where context exists.

---

# 103. Filename Safety

Filename is sanitized server-side.

Temporary user-provided Casting/Role text MUST NOT be inserted unsafely.

---

# 104. PDF Content Consistency

Selected facts must match Source snapshot.

No separate client-edited values.

---

# 105. Hyperlinks

Supported professional URLs MUST be clickable in PDF.

---

# 106. QR Support

For supported links Builder/PDF MAY render QR according to approved configuration.

---

# 107. Per-Link QR Visibility

Where enabled by product configuration, QR visibility MAY be independently controlled per link/questionnaire context.

---

# 108. Public Visitor QR Control

Default UX SHOULD NOT expose low-level QR styling.

At most the Builder MAY allow permitted inclusion/exclusion of a QR-capable link, while QR presentation follows template rules.

If product later allows `show QR` customization, it must remain bounded to approved links.

---

# 109. QR Label

Every QR SHOULD have a human-readable context:

```text
Official Profile
Video Intro
Showreel
```

---

# 110. QR Exactness

Before emitting QR:

```text
encode canonical URL
 ↓
decode
 ↓
compare exact URL
```

must pass.

---

# 111. QR Safety

QR MUST NOT target:

- Admin;
- private asset;
- expired internal URL;
- authenticated preview;
- unapproved local-only endpoint.

---

# 112. Local Media Public URL

If local media is selected for a public questionnaire:

system MUST use stable approved public/casting-safe URL.

Do not duplicate file just to create QR.

---

# 113. Stage PQB-09 — Success

After successful generation, user SHOULD receive:

```text
PDF ready
[Download]
[Back to Profile]
[Invite to Casting]
```

depending context.

---

# 114. Professional CTA After Generation

This is a valuable conversion moment.

Relevant actions MAY include:

```text
Invite to Casting
Request Materials
Contact
```

---

# 115. Link to Current Profile

Success view SHOULD retain access to current official Profile.

---

# 116. Session After Generation

Builder Session MAY remain available until expiry so user can:

```text
Edit selection
→ generate another version
```

---

# 117. Multiple Generated PDFs

Generating a second document from same session does not create a new Profile or Prepared Questionnaire.

---

# 118. Session Nature

Public Builder Session is:

```text
temporary
non-authoritative
configuration-only
```

---

# 119. Session Data Model — UX-Relevant Concept

Conceptually contains:

```text
id
profile_id
template_id/type
language
configuration
casting_name
role_name
expires_at
created_at
```

Exact schema belongs later Data Model docs.

---

# 120. Session ID Is Not Authorization

Possessing session identifier MUST NOT permit access to data beyond valid Public Builder projection.

---

# 121. Session Expiry

Upon expiry:

```text
This questionnaire session has expired.
[Start a new questionnaire]
```

---

# 122. Expiry UX

If safe and permitted, system MAY offer:

```text
Start again with same template
```

but MUST re-request all eligible data from current server projection.

---

# 123. No Blind Restoration

Expired client configuration MUST NOT reintroduce items no longer allowed.

---

# 124. Session Persistence

Within active lifetime, session SHOULD survive:

- page refresh;
- internal public navigation;
- locale-preserving navigation.

---

# 125. Browser Storage

Browser storage MAY supplement UX, but server session remains authoritative for sensitive selection/permissions.

---

# 126. Multi-Tab Behaviour

If Builder opened in multiple tabs:

system SHOULD avoid silent destructive last-write confusion where practical.

Exact concurrency strategy is later technical architecture.

---

# 127. Back/Forward

Steps SHOULD behave predictably with browser Back/Forward.

Do not reset whole session from a simple browser Back.

---

# 128. Cancel / Start Over

User MAY explicitly:

```text
Начать заново
```

This should confirm only if it discards meaningful current selection.

---

# 129. Reset to Template Defaults

Useful action:

```text
Сбросить к шаблону
```

MAY be provided separately from full new session.

---

# 130. Desktop Layout

Recommended conceptual structure:

```text
┌────────────────────────────────────────────────────────────┐
│ Header / Actor Context / Builder                           │
├──────────────────────┬─────────────────────────────────────┤
│ Configuration        │ Preview / Selection Summary         │
│                      │                                     │
│ Template             │                                     │
│ Sections             │                                     │
│ Items                │                                     │
│ Locale               │                                     │
│                      │                                     │
├──────────────────────┴─────────────────────────────────────┤
│ Back                          Preview      Generate PDF     │
└────────────────────────────────────────────────────────────┘
```

---

# 131. Desktop Sticky Summary

Summary/Preview MAY remain visible as user scrolls configuration.

Must not create inaccessible independent scroll traps.

---

# 132. Mobile Layout

Recommended:

```text
Header
 ↓
Progress / Step label
 ↓
Current step content
 ↓
Sticky or bottom action:
Back | Continue
```

Final:

```text
Preview
→ Generate PDF
```

---

# 133. Mobile Step Indicator

Example:

```text
Шаг 2 из 4 · Содержание
```

MAY be used.

Do not over-segment into excessive steps.

---

# 134. Mobile Section Cards

Large touch-friendly cards preferred over dense checkbox tables.

---

# 135. Mobile Photo Selection

Each photo card:

- large enough thumbnail;
- category;
- selected state;
- accessible control.

---

# 136. Mobile Preview

Must fit viewport without horizontal page overflow.

PDF preview MAY use controlled page scaling.

---

# 137. Mobile Fixed Actions

`Continue`/`Generate` MAY be sticky.

Must not cover form controls or browser keyboard.

---

# 138. Tablet

Can use split-pane if usable width allows.

Breakpoint decided by actual space, not device label alone.

---

# 139. Accessibility — Core

Builder MUST support complete keyboard operation.

---

# 140. Checkbox Semantics

Boolean selection uses actual checkbox/switch semantics appropriate to interaction.

---

# 141. Card Selection

Clickable card must expose selected state programmatically.

---

# 142. Required Item Semantics

Required items should be labelled textually:

```text
Обязательно
```

not only lock icon/color.

---

# 143. Reordering Accessibility

If reorder enabled:

- keyboard method;
- screen-reader accessible position changes;
- not drag-only.

---

# 144. Focus After Step Change

Focus SHOULD move to meaningful heading/start of new step.

---

# 145. Error Summary

On generation validation failure with several problems:

UI SHOULD provide top-level summary + links/focus to affected sections.

---

# 146. Inline Error

Individual issue shown near relevant selection.

---

# 147. Screen Reader Announcements

Async state:

```text
PDF generation started
PDF ready
Generation failed
```

should be communicated accessibly.

---

# 148. Color

Selected/required/error states MUST NOT depend only on color.

---

# 149. Preview Accessibility

HTML preview SHOULD remain keyboard/screen-reader usable where it represents user-visible output.

---

# 150. Reduced Motion

Transitions/step animation non-essential.

Respect reduced-motion preferences.

---

# 151. Form Validation

Temporary Casting fields SHOULD validate:

- length;
- unsafe characters where relevant;
- required status according to Template.

---

# 152. User-Supplied Labels Are Untrusted

Casting Name/Role Name must be escaped/sanitized in:

- HTML;
- PDF;
- filename;
- analytics.

---

# 153. No User HTML

Public user cannot inject HTML/Markdown into generated Questionnaire unless a future specifically secured feature allows it.

---

# 154. Content Editing Boundary

Builder visitor can:

```text
Include
Exclude
Order if allowed
Add temporary casting context
Choose locale
```

Cannot edit actress facts.

---

# 155. Example — Invalid Desired Change

User wants:

```text
Change English B2 to C1
```

Builder MUST NOT offer inline edit.

They may simply exclude the Language if irrelevant.

---

# 156. Example — Project Title

Public user cannot rename Profile Project.

Casting-specific temporary project label is separate document context.

---

# 157. Example — Contact

Public user cannot type a different phone number to replace actress Contact.

---

# 158. Privacy Principle

Builder is a controlled projection, not a privileged portal.

---

# 159. Server Permission Validation

At least at:

```text
session creation
item selection/update
preview
generation
```

server-side permission must remain enforceable according to architecture.

Critical check immediately before output is mandatory.

---

# 160. TOCTOU Protection

Permission may change after selection.

Therefore:

```text
Selected once
≠
authorized forever
```

---

# 161. Privacy Error Messaging

If item becomes unavailable:

correct:

```text
Материал больше недоступен.
```

Avoid:

```text
Администратор изменил поле allow_in_public_questionnaire_builder=false.
```

---

# 162. Rate Limiting / Abuse

Generation MAY be rate-limited.

UX SHOULD distinguish rate limit from generic failure.

Example:

```text
Слишком много запросов подряд.
Попробуйте повторить позже.
```

Exact anti-abuse rules later.

---

# 163. Bot Protection

If required, anti-abuse measures SHOULD minimize professional-user friction.

No mandatory intrusive challenge by default unless risk warrants it.

---

# 164. PDF Generation Loading State

Expected UI:

```text
Формируем PDF…
```

with progress only if actual progress is trustworthy.

Do not fake precise percentage.

---

# 165. PDF Failure

```text
Не удалось сформировать PDF.
Ваш выбор сохранён.
[Повторить]
```

where session remains valid.

---

# 166. PDF Service Outage

Builder should still retain configuration and, where possible, HTML Preview.

---

# 167. Partial Media Failure

If an optional selected media derivative cannot render:

policy later determines whether:

- block;
- exclude with warning;
- fallback.

UX MUST not silently produce misleading incomplete output.

---

# 168. Broken External Link

Before generation, readiness MAY warn/block according to link criticality.

Known broken QR must not be emitted silently.

---

# 169. Template Unavailable Mid-Session

If Admin disables Template:

current session MUST be revalidated.

System may allow current valid snapshot only if explicitly permitted by policy; default should favor current authorization/configuration.

---

# 170. Public Builder Admin Changes

Changes to Template defaults SHOULD affect:

```text
new sessions
```

Existing sessions require defined version/revalidation behaviour.

They should not silently restructure themselves unpredictably.

---

# 171. Session Versioning

Builder session SHOULD conceptually retain template/version reference sufficient for predictable behaviour.

Exact model later.

---

# 172. Prepared Questionnaire Starting Point and Changes

If source Prepared Questionnaire changes after Builder session starts:

Builder should not silently overwrite user's current selection.

Revalidation is separate from replacing configuration.

---

# 173. User Selection Priority

Within valid permissions:

user's current selections remain until:

- user changes them;
- item becomes invalid;
- session expires;
- explicit reset.

---

# 174. Public Builder vs Prepared Questionnaire Authority

Prepared Questionnaire is:

```text
official curated recommendation
```

Custom Builder output is:

```text
user-configured professional package
generated from official allowed data
```

The UI SHOULD communicate this distinction.

---

# 175. Suggested Labels

Prepared:

```text
Рекомендуемая анкета
```

Builder output:

```text
Анкета, собранная под ваш запрос
```

---

# 176. Custom PDF Provenance

PDF MAY include text conceptually equivalent to:

```text
Сформировано на официальном сайте актрисы
на основе выбранных разрешённых данных профиля.
```

Exact copy later.

---

# 177. No False Official Endorsement

Custom Casting labels entered by visitor MUST NOT appear as if actress/admin officially confirmed involvement in that casting.

---

# 178. Example

If visitor writes:

```text
Netflix Series X
Role: Lead
```

document context MUST NOT imply:

```text
Confirmed participation / application
```

unless separately established.

---

# 179. Public Builder and Feedback

After PDF generation, Professional Inquiry form MAY carry:

```text
questionnaire_session_id
```

or generated package reference.

---

# 180. Privacy of Session Reference

Session linkage passed to Feedback should identify configuration internally without exposing more visitor data than necessary.

---

# 181. Public Builder and Casting Invitation

Flow:

```text
Generate PDF
 ↓
Invite to Casting
 ↓
Feedback form
 ↓
Session/package context attached
```

This reduces repetition.

---

# 182. Public Builder and Request Materials

Same contextual handoff MAY occur.

---

# 183. Analytics Events

Recommended events:

```text
public_builder_opened
public_builder_starting_point_selected
public_builder_template_selected
public_builder_section_enabled
public_builder_section_disabled
public_builder_item_selected
public_builder_item_removed
public_builder_preview_opened
public_builder_pdf_generation_started
public_builder_pdf_generated
public_builder_pdf_failed
public_builder_casting_contact_started
```

Final event schema later.

---

# 184. Analytics Must Not Include Private Content

Event payload SHOULD use safe identifiers/categories.

Do not log:

- contact value;
- pasted Casting message;
- private Profile facts.

---

# 185. Analytics Session Boundary

Builder analytics may use anonymous/pseudonymous Builder Session ID according to Privacy architecture.

---

# 186. Builder KPI — Start Rate

```text
Questionnaire viewers
→ Builder opened
```

Not inherently “higher is better”.

A low Builder rate may mean Prepared Questionnaire is effective.

---

# 187. KPI — Completion Rate

```text
Builder opened
→ PDF generated
```

---

# 188. KPI — Time to Package

Time:

```text
Builder opened
→ successful PDF
```

Useful efficiency metric.

---

# 189. KPI — Template Usage

Track relative usage:

```text
Quick
Standard
Extended
Casting
```

---

# 190. KPI — Customization Pattern

Aggregate:

- sections commonly added;
- sections commonly removed.

Feeds human product review only.

---

# 191. KPI — Generation Error Rate

High priority operational metric.

---

# 192. KPI — Contact After Builder

```text
PDF generated
→ professional inquiry
```

valuable conversion signal.

---

# 193. Analytics Recommendation Boundary

If 70% users add Emotional Grid:

system MAY recommend Admin review.

It MUST NOT automatically mutate Template.

---

# 194. Virtual Operator Integration

Virtual Operator SHOULD detect:

```text
high generation error rate
invalid QR dependencies
broken template defaults
frequent missing required data
permission inconsistencies
```

---

# 195. Virtual Operator Does Not Modify Builder Automatically

Template/content changes require Admin/Product human decision.

---

# 196. Search Integration

Builder MAY provide search inside long item lists.

---

# 197. Search Query Scope

Only current eligible Builder data.

No access to general Admin/private search.

---

# 198. Public Profile Search Integration

Active Builder user may use normal Public Search and add eligible item from result/source.

---

# 199. Search Zero Result

Must retain semantics:

```text
В доступных данных совпадений нет.
```

Not claim absence of professional capability.

---

# 200. Empty States — No Eligible Projects

```text
Для публичного конструктора сейчас нет доступных проектов.
```

Do not expose names of excluded Projects.

---

# 201. Empty States — No Emotional Grid

If block optional:

disable/omit section with understandable state.

Do not generate synthetic grid.

---

# 202. Empty State — Contacts

If no Contact allowed:

Professional Feedback CTA may remain available separately if product rules allow.

---

# 203. Empty State — Template

Builder cannot open if no enabled valid Template.

Questionnaire Hub SHOULD still expose Prepared Questionnaire where available.

---

# 204. Error State — Session Not Found

Safe message:

```text
Эта версия анкеты недоступна или срок её действия завершён.
[Создать новую]
```

---

# 205. Error State — Authorization Revalidation

Do not show technical 403 for ordinary removed item if a clearer safe Builder message is possible.

---

# 206. Error State — Generation Validation

Example:

```text
Невозможно сформировать анкету:

• требуется фото в полный рост;
• выбранный видеоматериал больше недоступен.

[Исправить]
```

---

# 207. Error Navigation

Error summary items SHOULD take user to relevant configuration section.

---

# 208. Warning State

Example:

```text
Один внешний видеолинк временно недоступен.
```

Whether generation continues depends on template/module policy.

---

# 209. No Silent Removal of Required Content

If required item becomes invalid, generation must not quietly omit it.

---

# 210. No Silent Replacement

System MUST NOT replace invalid selected Project with another Project automatically.

---

# 211. No AI Auto-Completion

If selected package lacks desired content:

AI MUST NOT invent text/photos to “complete” questionnaire.

---

# 212. Performance Goal

Builder first useful configuration SHOULD load without downloading full-size media assets.

---

# 213. Thumbnail Strategy

Selection cards use appropriate derivatives.

Originals load only where specifically necessary.

---

# 214. Lazy Loading

Long project/photo lists MAY lazy-load.

Selection state must remain stable.

---

# 215. Preview Performance

Preview generation SHOULD reuse stable derived representations where possible.

---

# 216. PDF Work Asynchronous

Generation MAY be asynchronous internally.

User-facing flow must provide clear current status.

---

# 217. Analytics Non-Blocking

Tracking failure does not block Builder.

---

# 218. AI Non-Dependency

Builder core requires no AI.

---

# 219. Public Builder and Theme

Public website Theme MAY style Builder interface.

Generated PDF uses approved Questionnaire/PDF presentation rules, not arbitrary visitor-selected Theme.

---

# 220. Theme Accessibility

Theme MUST NOT make:

- checkbox state;
- required label;
- errors;
- CTA;

inaccessible.

---

# 221. Security Boundary Summary

Public Builder user MAY:

```text
read eligible projection
select eligible items
create temporary configuration
generate derived document
submit professional inquiry
```

MAY NOT:

```text
modify profile
change visibility
access private data
publish prepared questionnaire
create casting entity
change professional facts
```

---

# 222. Privacy Boundary Summary

Public Builder API MUST return the minimum data needed for Builder UX.

---

# 223. File/Media Security

Builder MUST NOT expose original filesystem paths such as:

```text
/data/kate-actor/...
```

or internal storage metadata.

---

# 224. Admin IDs

Internal sequential IDs SHOULD not become security mechanism.

Opaque/public identifiers may be used according to later API architecture.

---

# 225. URL Safety

All rendered URLs must pass public safety rules.

---

# 226. External Link Security

Links generated in HTML/PDF SHOULD follow safe protocol policy.

Examples allowed likely:

```text
https:
tel:
mailto:
```

and approved messaging schemes as defined later.

No arbitrary unsafe scheme.

---

# 227. Builder Data Integrity

Server MUST ensure selected item belongs to the correct Actor Profile.

Even though current product is single-profile, this invariant should exist.

---

# 228. Template Data Integrity

Template referenced by session must belong to current Builder configuration/system scope.

---

# 229. Session Integrity

Client cannot rewrite:

```text
profile_id
template privileged configuration
permission flags
```

and expect server to trust them.

---

# 230. Generation Integrity

Output is built from server-side normalized snapshot, not raw client object.

---

# 231. QR Integrity

QR target is taken from canonical validated server URL, not arbitrary visitor-submitted URL.

Temporary visitor Casting Name/Role cannot create arbitrary QR destination.

---

# 232. Accessibility Acceptance

Builder cannot be accepted if user cannot complete:

```text
Template
→ Select
→ Preview
→ Generate
```

using keyboard only.

---

# 233. Mobile Acceptance

Builder cannot be accepted if smartphone user must:

- horizontally scroll full page;
- use hover;
- perform precision drag;
- use desktop-width modal.

---

# 234. International Acceptance

EN Builder cannot be accepted if:

```text
Template EN
→ Preview RU
```

or PDF contains avoidable mixed critical UI labels.

---

# 235. Privacy Acceptance

Builder cannot be accepted if private entity names appear in client response even if visually hidden.

---

# 236. Template Acceptance

Each enabled Template MUST satisfy:

```text
useful default
+
valid minimum structure
+
defined required blocks
+
defined limits
+
supported locale behavior
```

---

# 237. Public Builder Quality Gate

Before implementation/release, verify:

- [ ] Builder can start from Prepared Questionnaire.
- [ ] Builder can start from Template.
- [ ] Templates have clear professional purpose.
- [ ] Useful defaults exist.
- [ ] Required blocks identifiable.
- [ ] Optional blocks configurable.
- [ ] Item-level selection works.
- [ ] Only eligible server-projected data returned.
- [ ] Primary photos handled correctly.
- [ ] Emotional Grid supported.
- [ ] Projects selectable.
- [ ] Skills/Languages selectable.
- [ ] Video/audio/links selectable.
- [ ] Contacts obey independent permission.
- [ ] Temporary Casting fields do not modify Profile.
- [ ] Locale persists.
- [ ] Preview exists.
- [ ] Server revalidates before Preview/Generate.
- [ ] PDF includes generated date and official Profile URL.
- [ ] Hyperlinks clickable.
- [ ] QR exact targets validated.
- [ ] Mobile complete.
- [ ] Keyboard complete.
- [ ] Session expiry handled.
- [ ] PDF failure preserves configuration.
- [ ] Professional CTA available after generation.
- [ ] No registration required.

---

# 238. E2E-PQB-001 — Start From Recommended Questionnaire

```text
Open Recommended Questionnaire
→ Customize
→ Builder opens with current allowed equivalent selections
```

---

# 239. E2E-PQB-002 — Quick Template

```text
Open Builder
→ Quick
→ default required content present
→ Preview
→ Generate
```

---

# 240. E2E-PQB-003 — Item Selection

```text
Enable Projects
→ select Project A
→ Preview
→ Project A appears
→ Project B does not
```

---

# 241. E2E-PQB-004 — Permission Security

```text
Client requests unauthorized Contact ID
→ server rejects/excludes
→ response/PDF contains no private contact
```

---

# 242. E2E-PQB-005 — Permission Revocation

```text
Select Contact
→ Admin disables permission
→ Generate
→ server revalidates
→ Contact absent/block according to requirement
```

---

# 243. E2E-PQB-006 — Casting Context Isolation

```text
Visitor enters Project "X" / Role "Y"
→ PDF contains temporary context
→ Actor Profile Projects/Roles remain unchanged
```

---

# 244. E2E-PQB-007 — Emotional Grid

```text
Select approved Emotional Grid
→ generated PDF contains composite
→ shooting date
→ full Emotional Portfolio link
```

No automatic individual emotional-photo dump.

---

# 245. E2E-PQB-008 — Locale

```text
Builder EN
→ select items
→ Preview
→ Generate
→ PDF EN
```

Source data values remain same.

---

# 246. E2E-PQB-009 — QR Exactness

For each generated QR:

```text
decode
→ compare exact canonical target
→ PASS
```

---

# 247. E2E-PQB-010 — QR Privacy

Attempt to configure private/admin URL.

Expected:

```text
generation blocked / QR denied
```

according to policy.

---

# 248. E2E-PQB-011 — Session Persistence

```text
Builder
→ select items
→ open Profile
→ return
→ selections preserved
```

---

# 249. E2E-PQB-012 — Session Expiry

Expired session cannot be used to restore revoked/ineligible content.

---

# 250. E2E-PQB-013 — PDF Failure

```text
configure
→ generate
→ PDF service fails
→ selection remains
→ retry available
```

---

# 251. E2E-PQB-014 — Mobile

Complete:

```text
Template
→ Content
→ Preview
→ Generate
```

on representative mobile viewport.

---

# 252. E2E-PQB-015 — Keyboard

Complete same flow without mouse/touch.

---

# 253. E2E-PQB-016 — Template Limit

Attempt to select `N+1` photos where limit `N`.

UI explains limit; server also rejects excess.

---

# 254. E2E-PQB-017 — Required Block

User cannot generate valid document after trying to remove mandatory identification/primary requirement.

---

# 255. E2E-PQB-018 — No Registration

Entire supported Builder flow works anonymously.

---

# 256. E2E-PQB-019 — No Profile Mutation

After any Public Builder action, Profile data remains unchanged.

---

# 257. E2E-PQB-020 — Feedback Handoff

```text
Generate PDF
→ Invite to Casting
→ Feedback form
→ questionnaire session/package context associated
```

without converting automatically to Casting entity.

---

# 258. Traceability

Expected chain:

```text
PERSONA
 ↓
CJM
 ↓
UF-PUB-PQB-*
 ↓
PQB-UX-*
 ↓
FR-PQB-*
 ↓
API / Server Action
 ↓
AC-PQB-*
 ↓
E2E-PQB-*
```

---

# 259. PQB-UX-001

Public Builder MUST be positioned as customization of approved professional data, not editing of the Actor Profile.

---

# 260. PQB-UX-002

Public Builder MUST be accessible without mandatory account registration.

---

# 261. PQB-UX-003

Builder SHOULD offer a Prepared Questionnaire as a preferred starting point where available.

---

# 262. PQB-UX-004

Builder MUST support enabled templates with useful default configurations.

---

# 263. PQB-UX-005

Builder MUST support block-level inclusion/exclusion subject to Template rules.

---

# 264. PQB-UX-006

Builder MUST support item-level selection for applicable domains.

---

# 265. PQB-UX-007

Builder MUST receive only server-approved eligible source data.

---

# 266. PQB-UX-008

Builder MUST NOT allow modification of Actor Profile facts.

---

# 267. PQB-UX-009

Temporary Casting context MUST remain document/session-scoped.

---

# 268. PQB-UX-010

Primary actor identification and mandatory professional content MUST be protected from invalid removal.

---

# 269. PQB-UX-011

Public Builder permissions MUST be independent from ordinary Public Site visibility.

---

# 270. PQB-UX-012

Contact permissions MUST be revalidated independently.

---

# 271. PQB-UX-013

Builder MUST preserve active locale throughout configuration and generation unless explicitly changed.

---

# 272. PQB-UX-014

Builder SHOULD preserve active valid session across normal public navigation.

---

# 273. PQB-UX-015

Preview MUST reflect server-revalidated configuration.

---

# 274. PQB-UX-016

PDF generation MUST perform final server-side permission and validity revalidation.

---

# 275. PQB-UX-017

Generated PDF MUST include Generated Date and official Profile URL.

---

# 276. PQB-UX-018

Supported URLs MUST be clickable in generated documents.

---

# 277. PQB-UX-019

QR MUST complement rather than replace hyperlinks.

---

# 278. PQB-UX-020

QR MUST encode exact approved public target and be decode-validated.

---

# 279. PQB-UX-021

QR MUST NOT point to private/admin resources.

---

# 280. PQB-UX-022

Builder MUST remain fully usable on mobile.

---

# 281. PQB-UX-023

Builder critical flow MUST be keyboard accessible.

---

# 282. PQB-UX-024

Drag-and-drop MUST NOT be required as the only reordering mechanism.

---

# 283. PQB-UX-025

PDF generation errors MUST preserve user configuration where the session remains valid.

---

# 284. PQB-UX-026

Expired sessions MUST fail safely and MUST NOT restore unauthorized stale content.

---

# 285. PQB-UX-027

Admin permission changes MUST take effect at output revalidation even for already-open Builder sessions.

---

# 286. PQB-UX-028

Builder analytics MUST NOT automatically change Templates.

---

# 287. PQB-UX-029

Public Builder MUST NOT depend on AI for core functionality.

---

# 288. PQB-UX-030

After successful generation, the user SHOULD have a direct path to relevant professional CTA.

---

# 289. UX Anti-Pattern PQB-AP-001

**Blank Document**

User starts with nothing.

---

# 290. PQB-AP-002

**Public Word Processor**

Fonts/margins/page layout exposed to casting visitor.

---

# 291. PQB-AP-003

**Private Items Disabled in UI**

Private entities loaded but visually disabled.

---

# 292. PQB-AP-004

**Profile Editing**

Visitor can modify facts.

---

# 293. PQB-AP-005

**Client-Authoritative Permissions**

Server trusts selected IDs blindly.

---

# 294. PQB-AP-006

**Builder Registration Wall**

Account required before basic usage.

---

# 295. PQB-AP-007

**Desktop Split View on Mobile**

Whole desktop interface shrunk horizontally.

---

# 296. PQB-AP-008

**Drag-Only Reordering**

Keyboard user cannot change order.

---

# 297. PQB-AP-009

**Silent Permission Removal**

Critical selected item removed without informing user.

---

# 298. PQB-AP-010

**QR to Admin Preview**

Generated public document contains internal/private link.

---

# 299. PQB-AP-011

**QR Replaces Link**

PDF contains only image QR, no clickable URL where supported.

---

# 300. PQB-AP-012

**Template Auto-Optimization**

Analytics modifies live Template without human review.

---

# 301. PQB-AP-013

**Temporary Casting Context Becomes Profile Data**

Visitor-supplied Role appears in actress Project history.

---

# 302. PQB-AP-014

**AI Fills Missing Facts**

System invents missing Skill/Project description to complete PDF.

---

# 303. PQB-AP-015

**Session as Authorization**

Knowing session ID reveals otherwise private data.

---

# 304. PQB-AP-016

**Generation Destroys Session**

Single failed render forces full configuration restart.

---

# 305. PQB-AP-017

**Mixed-Language Package**

EN Builder generates avoidable RU field labels.

---

# 306. PQB-AP-018

**Unbounded Selection**

User creates enormous document because Template has no useful rules/limits.

---

# 307. PQB-AP-019

**Prepared Questionnaire De-emphasized**

User is forced into Builder despite already suitable official version.

---

# 308. PQB-AP-020

**No Provenance**

Generated PDF does not identify actor/current official source/date.

---

# 309. Main Desktop Flow

```text
QUESTIONNAIRE HUB
       ↓
Recommended Questionnaire
       ↓
[Customize]
       ↓
┌──────────────────────────────────────────────┐
│ Public Questionnaire Builder                 │
├────────────────────┬─────────────────────────┤
│ Template/Sections  │ Live Summary / Preview  │
│                    │                         │
│ ☑ Basic Info       │ Actor Name              │
│ ☑ Close-Up         │ Photos                  │
│ ☑ Full Body        │ Languages               │
│ ☑ Languages        │ Skills                  │
│ ☑ Skills           │ ...                     │
│ ☐ Training         │                         │
│ ☑ Projects         │                         │
└────────────────────┴─────────────────────────┘
                 ↓
              PREVIEW
                 ↓
        SERVER REVALIDATION
                 ↓
          GENERATE PDF
                 ↓
       DOWNLOAD / CASTING CTA
```

---

# 310. Main Mobile Flow

```text
QUESTIONNAIRE
      ↓
  Customize
      ↓
STEP 1
Starting Point
      ↓
STEP 2
Sections
      ↓
STEP 3
Select Items
      ↓
STEP 4
Preview
      ↓
Generate PDF
      ↓
Download
      ↓
Professional CTA
```

---

# 311. Builder Data Flow

```text
                    MASTER DATA
                         │
                         ↓
             VISIBILITY / BUILDER RULES
                         │
                         ↓
              SERVER BUILDER PROJECTION
                         │
                         ↓
                   USER SESSION
                         │
              ┌──────────┼──────────┐
              │          │          │
           Select     Exclude     Order
              │          │          │
              └──────────┼──────────┘
                         ↓
                  SERVER REVALIDATION
                         ↓
                  GENERATION SNAPSHOT
                         ↓
               ┌─────────┴─────────┐
               ↓                   ↓
           HTML PREVIEW           PDF
                                   │
                              Links + QR
```

---

# 312. Security Flow

```text
Client requests item
        ↓
Server checks:
- belongs to profile
- allowed in Builder
- current availability
- template limits
- visibility/security
        ↓
      Allowed?
      /      \
    YES       NO
     │         │
 Session     Reject /
 Update      Exclude
```

---

# 313. Definition of Public Builder UX Compliance

Public Questionnaire Builder соответствует целевому продукту, если:

1. начинается с полезного Prepared Questionnaire или Template;
2. не требует обязательной регистрации;
3. позволяет выбирать состав анкеты, а не проектировать layout;
4. поддерживает block-level и item-level selection;
5. использует только server-approved eligible data;
6. не позволяет менять professional facts;
7. поддерживает temporary Casting context без изменения Profile;
8. поддерживает Primary Photos, Portfolio, Emotional Grid, Skills, Languages, Projects, Training, Achievements, Video, Audio, Links и Contacts согласно permissions;
9. сохраняет locale;
10. имеет понятный Preview;
11. повторно валидирует данные перед output;
12. создаёт share-ready PDF;
13. PDF содержит official Profile URL и Generated Date;
14. hyperlinks кликабельны;
15. QR безопасны и exact-match validated;
16. mobile flow полноценен;
17. keyboard flow полноценен;
18. session expiry безопасен;
19. permission revocation применяется к уже открытым sessions при generation;
20. PDF failure не уничтожает текущую конфигурацию;
21. Builder не зависит от AI;
22. после generation существует professional next action.

---

# 314. Финальный принцип

> **Public Questionnaire Builder должен позволять кастинг-специалисту за минимальное число действий собрать собственную профессионально релевантную версию анкеты из достоверных и специально разрешённых данных официального профиля. Пользователь контролирует только состав и допустимый контекст документа; факты, права доступа, профессиональная идентичность, ссылки, визуальный стандарт и безопасность остаются под контролем системы и администратора.**