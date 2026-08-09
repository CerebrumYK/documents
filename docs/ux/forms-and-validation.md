# FORMS AND VALIDATION

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная UX-спецификация форм, ввода данных и валидации

**Целевой файл:** `docs/ux/forms-and-validation.md`  
**Документ:** DOC-046  
**Статус:** ✅ Completed  
**Тип:** UX / Forms / Validation

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/ux-principles.md`
- `docs/ux/public-navigation.md`
- `docs/ux/admin-navigation.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/public-questionnaire-builder.md`

---

# 1. Назначение документа

Настоящий документ определяет единый нормативный UX-контракт для:

- публичных форм;
- административных форм;
- Contact / Feedback;
- Casting forms;
- Questionnaire configuration;
- Public Questionnaire Builder;
- Profile editing;
- Media metadata;
- Projects / Roles;
- Training;
- Skills / Languages;
- Contacts;
- AI-assisted forms;
- Theme configuration;
- search/filter controls;
- file uploads;
- destructive operations.

Документ определяет не только визуальное отображение ошибок, но полный lifecycle пользовательского ввода:

```text
INPUT
 ↓
CLIENT ASSISTANCE
 ↓
SERVER VALIDATION
 ↓
BUSINESS VALIDATION
 ↓
AUTHORIZATION
 ↓
PERSISTENCE
 ↓
SIDE EFFECTS
 ↓
SUCCESS / ERROR FEEDBACK
```

---

# 2. Главный принцип

Форма должна помогать пользователю **правильно завершить профессиональную задачу**, а не просто собирать значения.

Правильная модель:

```text
Understand
→ Enter
→ Validate
→ Correct if needed
→ Save
→ Know the result
```

Неправильная:

```text
Fill everything
→ Submit
→ "Invalid form"
```

---

# 3. Form Requirement IDs

Используется prefix:

```text
FORM-UX-*
```

Validation-specific:

```text
VAL-UX-*
```

---

# 4. Формы делятся по последствиям

## FORM-CLASS-01 — Public Transactional

Примеры:

- Casting Invitation;
- Request Materials;
- Contact;
- Public Builder configuration.

## FORM-CLASS-02 — Admin Master Data

Примеры:

- Profile;
- Skill;
- Project;
- Contact.

## FORM-CLASS-03 — Admin Configuration

Примеры:

- Questionnaire;
- Public Builder Template;
- Theme.

## FORM-CLASS-04 — High-Impact Business Action

Примеры:

- Publish;
- Create Casting;
- Opportunity stage;
- Booked;
- delete/archive.

## FORM-CLASS-05 — AI-Assisted

Примеры:

- Casting analysis review;
- BB Assistant;
- Theme AI.

Разный класс требует разной строгости UX.

---

# 5. FORM-UX-001 — Labels Are Mandatory

Каждое meaningful input поле MUST иметь понятный persistent label.

Placeholder не заменяет label.

Неправильно:

```text
[Введите имя...]
```

без label.

Правильно:

```text
Имя
[________________]
```

---

# 6. FORM-UX-002 — Labels Use Domain Language

Использовать:

```text
Дата съёмки
Уровень языка
Роль
```

а не:

```text
shoot_date
language_level_id
role_value
```

---

# 7. FORM-UX-003 — Required Status Must Be Explicit

Обязательные поля должны быть очевидны до Submit.

Допустимо:

```text
Дата съёмки *
```

при наличии общей legend:

```text
* Обязательное поле
```

или explicit:

```text
Дата съёмки
Обязательно
```

---

# 8. FORM-UX-004 — Optional Status Where Useful

В сложных формах SHOULD явно отмечаться:

```text
Второе имя
Необязательно
```

чтобы пользователь не гадал.

---

# 9. FORM-UX-005 — Do Not Mark Everything Required

Каждое обязательное поле должно иметь business reason.

Public Inquiry должна запрашивать только минимум, необходимый для профессионального ответа.

---

# 10. FORM-UX-006 — Group Related Inputs

Пример Contact:

```text
ОСНОВНЫЕ ДАННЫЕ
Имя
Тип

КОНТАКТ
Телефон
WhatsApp

ВИДИМОСТЬ
Public Site
Prepared Questionnaire
Public Builder
```

---

# 11. FORM-UX-007 — Form Order Follows Mental Model

Project form:

```text
Identity
→ Role/Type
→ Dates
→ Description
→ Media
→ Visibility
```

а не порядок колонок таблицы.

---

# 12. FORM-UX-008 — Progressive Disclosure

Advanced settings SHOULD быть вторичными.

Например:

```text
Основные данные
...
[Дополнительные настройки]
```

---

# 13. FORM-UX-009 — Avoid Giant Undifferentiated Forms

Если форма содержит десятки разнородных полей:

использовать:

- sections;
- tabs;
- steps;
- collapsible advanced areas;

но только когда это реально облегчает задачу.

---

# 14. FORM-UX-010 — Wizard Only When Necessary

Wizard оправдан, когда:

- есть логические этапы;
- последующие поля зависят от предыдущих;
- mobile перегружен;
- проверка каждого этапа полезна.

---

# 15. FORM-UX-011 — One Page When Simpler

Небольшой Contact form не должен превращаться в 4-step wizard.

---

# 16. FORM-UX-012 — Field Width Matches Expected Data

Пример:

`CEFR` не требует full-width input.

Biography — требует широкого editor.

---

# 17. FORM-UX-013 — Use Appropriate Input Type

Examples:

```text
email → email input
phone → tel input
URL → url input
date → date/calendar control
number → numeric input
```

с учётом локализации и accessibility.

---

# 18. FORM-UX-014 — Controlled Vocabulary for Structured Data

Если Domain Model имеет enum:

```text
Close-Up
Full Body
Profile
```

использовать controlled selection.

Не free text.

---

# 19. FORM-UX-015 — Free Text for Narrative

Biography, Project Description и professional message остаются narrative controls.

---

# 20. FORM-UX-016 — Searchable Selector for Long Lists

Для большого списка:

- Skills;
- Media;
- Projects;

SHOULD использовать searchable selection вместо огромного dropdown.

---

# 21. FORM-UX-017 — Native Select Is Fine for Small Lists

Например:

```text
Draft
Published
Archived
```

если editable state selector вообще допустим.

---

# 22. FORM-UX-018 — Checkbox for Independent Boolean

Пример:

```text
☑ Показывать на сайте
☐ Разрешить в публичном конструкторе
```

---

# 23. FORM-UX-019 — Radio for Mutually Exclusive Choice

Например:

```text
Тип анкеты
( ) Compact
( ) Extended
( ) Casting
```

если выбирается ровно один.

---

# 24. FORM-UX-020 — Switch Is Not Default Checkbox Replacement

Switch SHOULD использоваться для immediate on/off settings.

Не для сложного consent или high-impact action.

---

# 25. FORM-UX-021 — Date Inputs Must Represent Correct Semantics

Различать:

- date-only;
- date-time;
- month/year;
- range.

Shooting Date не должна случайно становиться timezone-sensitive timestamp.

---

# 26. FORM-UX-022 — Date Format Localized

Display:

```text
8 August 2026
```

или localized equivalent.

Stored Source semantics не меняются.

---

# 27. FORM-UX-023 — Unknown Date Must Remain Unknown

Не подставлять автоматически:

```text
today
```

для неизвестной professional date.

---

# 28. FORM-UX-024 — Numeric Units Must Be Explicit

Если пользователь вводит рост:

```text
Рост
[170] cm
```

единица не должна быть скрытой assumption.

---

# 29. FORM-UX-025 — Phone Number Does Not Guess Country

Если номер неполный, система не должна незаметно добавлять country code, если это не подтверждённое правило.

---

# 30. FORM-UX-026 — URL Fields Must Show Expected Use

Например:

```text
URL видеовизитки
https://...
```

---

# 31. FORM-UX-027 — Password Fields Are Separate Security Pattern

Если Authentication UI требует password:

- password manager friendly;
- paste allowed;
- accessible reveal action;
- no arbitrary blocking of strong generated passwords.

Detailed auth requirements later.

---

# 32. VAL-UX-001 — Validation Has Layers

Минимум:

```text
Client Validation
Server Validation
Business Validation
Authorization
```

---

# 33. Client Validation Is Assistance

Client-side checks improve speed.

Они MUST NOT быть authoritative security/business gate.

---

# 34. Server Validation Is Authoritative

Любой mutation/input MUST быть валидирован server-side.

---

# 35. Business Validation

Пример:

```text
4×4 Emotional Grid
→ exactly 16 photos
```

это business validation, а не просто field type.

---

# 36. Authorization Validation

Даже валидный Contact ID должен быть rejected, если текущий actor/action не имеет права его использовать.

---

# 37. VAL-UX-002 — Validate as Early as Helpful

Простые очевидные ошибки MAY показываться:

- on blur;
- during input after meaningful threshold;
- at Submit.

Не показывать `Required` до того, как пользователь успел взаимодействовать с полем.

---

# 38. VAL-UX-003 — Avoid Aggressive Validation

Плохой UX:

пользователь вводит:

```text
e
```

и сразу видит:

```text
Invalid email!
```

до завершения ввода.

---

# 39. VAL-UX-004 — Submit Always Revalidates Everything

Даже если client state выглядит valid.

---

# 40. VAL-UX-005 — Inline Error Near Field

Example:

```text
Email
[userexample.com]

Введите корректный email, например name@example.com
```

---

# 41. VAL-UX-006 — Error Summary for Multi-Error Form

Для большой формы после Submit:

```text
Не удалось сохранить форму.

Исправьте 3 поля:
• Дата съёмки
• Основное фото
• URL видеовизитки
```

Ссылки SHOULD перемещать focus к проблемному полю.

---

# 42. VAL-UX-007 — Error Explains Resolution

Неправильно:

```text
Invalid.
```

Правильно:

```text
Выберите ровно 16 фотографий для сетки 4×4.
```

---

# 43. VAL-UX-008 — Error Must Not Blame User

Не использовать:

```text
Вы ввели неправильные данные.
```

Предпочтительно:

```text
URL должен начинаться с https://
```

---

# 44. VAL-UX-009 — Keep Technical Error Separate

User-facing:

```text
Не удалось сохранить изменения.
```

Admin diagnostic detail MAY additionally show internal error code.

---

# 45. VAL-UX-010 — Error State Programmatically Connected

Input SHOULD expose:

- invalid state;
- association to error message;

for assistive technologies.

---

# 46. VAL-UX-011 — Warning Is Not Error

Warning:

```text
Видеоссылка сейчас недоступна.
Анкету можно сохранить, но публикация может быть ограничена.
```

depending rules.

---

# 47. VAL-UX-012 — Information Is Not Warning

Example:

```text
После изменения фото будут обновлены новые анкеты.
Исторические опубликованные версии останутся без изменений.
```

---

# 48. VAL-UX-013 — Severity Must Match Consequence

Не использовать red critical styling для optional missing caption.

---

# 49. VAL-UX-014 — Disabled Button Must Have Explanation

Плохо:

```text
[Publish] disabled
```

без причины.

Хорошо:

```text
Публикация недоступна

• Не выбрано фото в полный рост
• Отсутствует обязательный контакт
```

---

# 50. FORM-UX-028 — Preserve Values After Validation Error

Все корректно введённые поля MUST оставаться.

---

# 51. FORM-UX-029 — Preserve Uploaded Files Where Safely Possible

Если form validation fails после upload, пользователь не должен повторно выбирать уже успешно загруженный файл.

---

# 52. FORM-UX-030 — Do Not Reset Form After Server Error

Сетевой/server error не должен очищать форму.

---

# 53. FORM-UX-031 — Explicit Reset Is User Action

`Reset`/`Start over` должно быть отдельным действием.

---

# 54. FORM-UX-032 — Reset May Need Confirmation

Если форма содержит существенный введённый объём.

---

# 55. FORM-UX-033 — Save Draft

Для длинных Admin forms SHOULD поддерживаться:

```text
Save Draft
```

где domain позволяет.

---

# 56. FORM-UX-034 — Save Draft ≠ Ready

Incomplete Draft может быть сохранён.

Publication readiness проверяется отдельно.

---

# 57. FORM-UX-035 — Autosave

Autosave MAY применяться к:

- long text;
- Builder session;
- selected configuration;

если безопасно.

---

# 58. FORM-UX-036 — Autosave Must Be Visible

States:

```text
Сохраняется…
Сохранено
Не удалось сохранить
```

---

# 59. FORM-UX-037 — Autosave Failure Must Be Obvious

Особенно перед navigation/close.

---

# 60. FORM-UX-038 — Autosave Never Means Publish

Normative.

---

# 61. FORM-UX-039 — Save Button State

After successful save MAY show:

```text
Saved
```

but do not permanently rename primary action inconsistently.

---

# 62. FORM-UX-040 — Unsaved Changes Indicator

Long Admin forms SHOULD show unsaved state where applicable.

---

# 63. FORM-UX-041 — Navigation Guard

If significant unsaved work exists:

```text
Есть несохранённые изменения.
```

before destructive navigation.

---

# 64. FORM-UX-042 — Navigation Guard Must Not Trigger Excessively

If autosaved/no changes, no unnecessary modal.

---

# 65. FORM-UX-043 — Form Submission Prevents Accidental Duplicate

Submit button SHOULD be protected from repeated duplicate clicks while current mutation is in-flight.

---

# 66. FORM-UX-044 — Idempotency Is Server Responsibility Too

UI button disabling is not sufficient.

Especially:

- Feedback;
- Casting creation;
- notification retry;
- publish.

---

# 67. Public Feedback Form

Primary goal:

```text
send a professional inquiry with minimal friction
```

---

# 68. Public Feedback Field Set

Should be type-aware.

Potential common fields:

```text
Name
Organization
Email / Phone
Project / Subject
Message
Attachments
```

Exact requirements depend on Feedback type.

---

# 69. Casting Invitation Form

MAY additionally request:

```text
Project
Role
Dates / Deadline
Location
Message
```

only when useful.

---

# 70. Request Materials Form

MAY include selectable request types:

```text
Additional Photos
Self-Tape
Voice Sample
Specific Scene
Other
```

plus details.

---

# 71. Generic Contact Form

Should not require full Casting fields.

---

# 72. FORM-UX-045 — Context Prefill

When launched from:

```text
Project
Questionnaire
Builder
```

system attaches that context automatically.

---

# 73. FORM-UX-046 — Hidden Context Is Server-Verified

Client hidden input:

```text
project_id=...
```

MUST NOT be blindly trusted.

---

# 74. FORM-UX-047 — Professional Intent May Be Prefilled

`Invite to Casting` CTA can initialize type:

```text
CASTING_INVITATION
```

---

# 75. FORM-UX-048 — User Can Understand Form Type

Heading:

```text
Пригласить на кастинг
```

not generic:

```text
Форма обратной связи
```

---

# 76. FORM-UX-049 — Public Form Success Requires Persistence

Success only after authoritative record is stored.

---

# 77. FORM-UX-050 — Notification Failure Is Separate

User may still see:

```text
Обращение принято.
```

if external notification failed afterward.

---

# 78. FORM-UX-051 — Reference Number

Professional Inquiry SHOULD return a human-readable reference when feasible.

---

# 79. FORM-UX-052 — Public Form Submission State

Sequence:

```text
Ready
→ Submitting
→ Success
```

or:

```text
Ready
→ Submitting
→ Error
→ Retry
```

---

# 80. FORM-UX-053 — Public Success Should Not Promise Reply Time Without Policy

Do not invent:

```text
Ответим в течение 1 часа.
```

unless actual service commitment exists.

---

# 81. File Upload UX

Upload is a dedicated interaction pattern.

---

# 82. FORM-UX-054 — Show Accepted File Types Before Selection

Example:

```text
PDF, JPG, PNG
До 10 MB
```

where rules apply.

---

# 83. FORM-UX-055 — File Input Must Be Keyboard Accessible

Drag-and-drop MAY complement native file chooser.

Cannot be only method.

---

# 84. FORM-UX-056 — Drag-and-Drop State

Clearly show:

```text
Перетащите файлы сюда
или выберите с устройства
```

---

# 85. FORM-UX-057 — Upload Progress

Show per-file state for meaningful upload duration:

```text
Uploading
Uploaded
Failed
```

---

# 86. FORM-UX-058 — Upload Validation Per File

One invalid file should not necessarily fail all valid files.

---

# 87. FORM-UX-059 — File Error Explains Reason

Example:

```text
Файл exceeds 10 MB.
```

or:

```text
Этот формат не поддерживается.
```

---

# 88. FORM-UX-060 — MIME Determined Server-Side Too

Filename extension alone is not trusted.

---

# 89. FORM-UX-061 — Uploaded File Preview

Where useful:

- image thumbnail;
- filename;
- size;
- remove action.

---

# 90. FORM-UX-062 — Original Filename Is Not Safe Display Authority

Escape/sanitize filenames in UI.

---

# 91. FORM-UX-063 — File Removal Before Submit

User can remove mistakenly selected file.

---

# 92. FORM-UX-064 — Private Attachments Stay Private

Casting/Feedback attachments are never exposed as public Media simply because uploaded.

---

# 93. Media Admin Upload

Can support bulk upload.

---

# 94. FORM-UX-065 — Bulk Progress

Use grouped summary:

```text
14 of 16 uploaded
2 failed
```

with per-file errors.

---

# 95. FORM-UX-066 — No Fake Progress

Do not show invented percentages if backend does not report progress.

Indeterminate progress is acceptable.

---

# 96. Search Field UX

Search is a form-like control.

---

# 97. FORM-UX-067 — Search Label

Search input SHOULD have accessible name:

```text
Поиск по профилю
```

---

# 98. FORM-UX-068 — Search May Execute During Typing

Only with debounce and clear results behaviour.

---

# 99. FORM-UX-069 — Search Zero State

```text
Совпадений в опубликованных данных нет.
```

---

# 100. FORM-UX-070 — Search Clear Action

Should be obvious and keyboard accessible.

---

# 101. Filters UX

Filters should:

- show active state;
- be clearable;
- preserve context.

---

# 102. FORM-UX-071 — Filter Count

MAY show:

```text
Фильтры (2)
```

where useful.

---

# 103. FORM-UX-072 — Reset Filters

Should not reset unrelated page state such as locale.

---

# 104. Admin Profile Forms

Master Data changes require precise forms.

---

# 105. FORM-UX-073 — Current Saved Value Visible

Admin should know existing fact before editing.

---

# 106. FORM-UX-074 — Source vs Translation Fields

Localized content SHOULD be visually organized by locale.

Structured Source Fact should not be duplicated in every locale form.

---

# 107. Example

Correct:

```text
Language level:
B2

Localized label:
RU: Английский
EN: English
```

not independent levels per locale.

---

# 108. Contact Form Admin UX

Visibility is critical.

---

# 109. FORM-UX-075 — Visibility Controls Grouped Together

```text
Видимость

☑ Публичный сайт
☑ Подготовленные анкеты
☐ Публичный конструктор
```

---

# 110. FORM-UX-076 — Visibility Defaults Conservative

Sensitive Contact SHOULD default non-public unless explicit product rule says otherwise.

---

# 111. FORM-UX-077 — Visibility Change Impact

Before/after save MAY show dependency effect:

```text
Этот контакт больше не будет доступен в новых Public Builder PDF.
```

---

# 112. FORM-UX-078 — Guardian Relationship Does Not Auto-Enable

Parent/Guardian contact remains separate permission.

---

# 113. Questionnaire Configuration Forms

These are configuration forms, not long text forms.

---

# 114. FORM-UX-079 — Select Existing Entities

Questionnaire photo selector references Portfolio Item.

No duplicate upload control by default.

---

# 115. FORM-UX-080 — Required vs Optional Blocks Visible

Admin understands which configuration violates template/readiness.

---

# 116. FORM-UX-081 — Preview Close to Form

Questionnaire config SHOULD allow easy:

```text
Edit
→ Preview
→ Back
```

---

# 117. FORM-UX-082 — QR Controls Contextual

If QR configurable:

show alongside relevant link/item.

Not one mysterious global `QR enabled` switch.

---

# 118. FORM-UX-083 — QR Unsafe URL Must Block

Known admin/private target produces blocker.

---

# 119. Emotional Grid Forms

High precision.

---

# 120. FORM-UX-084 — Grid Size Selection Uses Allowed Set

Only:

```text
1×2 1×3 1×4
2×2 2×3 2×4
3×2 3×3 3×4
4×2 4×3 4×4
```

---

# 121. FORM-UX-085 — Exact Count Communicated

After choosing `4×4`:

```text
Нужно выбрать 16 фотографий.
Выбрано: 12/16
```

---

# 122. FORM-UX-086 — Finalize Disabled With Explanation

Not merely disabled.

---

# 123. FORM-UX-087 — Cell Confirmation Visible

Each cell state:

```text
Needs review
Confirmed
```

---

# 124. FORM-UX-088 — Crop Controls Must Be Precise

Support:

- pan;
- scale;
- allowed rotation;

without accidental source alteration.

---

# 125. FORM-UX-089 — Reset Crop

Useful:

```text
Сбросить кадрирование
```

to proposal/default.

---

# 126. FORM-UX-090 — AI Crop Proposal Is Labelled

Example:

```text
Предложенное кадрирование
```

not presented as final fact.

---

# 127. Casting Source Forms

Need preserve original input.

---

# 128. FORM-UX-091 — Source Text Saved Separately

Pasted source remains immutable/history-aware according to Casting model.

---

# 129. FORM-UX-092 — AI Extraction Is Separate Editable Review

Source:

```text
Original text
```

AI extraction:

```text
Suggested requirements
```

Human-confirmed values:

```text
Confirmed requirements
```

---

# 130. FORM-UX-093 — Do Not Edit AI Result as if Source

UI separation mandatory.

---

# 131. FORM-UX-094 — Missing Requirement Explicit

Use:

```text
Не указано
```

not blank ambiguous cell where user cannot distinguish missing from loading.

---

# 132. Opportunity Stage Form

High-impact business state.

---

# 133. FORM-UX-095 — Stage Change Shows Destination

Example:

```text
Перевести в этап:
Callback
```

---

# 134. FORM-UX-096 — Invalid Stage Not Offered

UI SHOULD avoid impossible transitions.

Server still validates.

---

# 135. FORM-UX-097 — High-Impact Stage May Require Confirmation

Especially:

```text
Booked
Closed — Not Selected
Withdrawn
```

if consequences significant.

---

# 136. FORM-UX-098 — Reason Field Only When Useful

Do not force unknown rejection reason.

---

# 137. Destructive Forms / Confirmations

Delete/archive patterns.

---

# 138. FORM-UX-099 — Confirmation States Specific Consequence

Bad:

```text
Are you sure?
```

Better:

```text
Удалить этот Media Asset?

Он используется в:
• Portfolio
• Questionnaire
```

---

# 139. FORM-UX-100 — Typed Confirmation Rarely

Typing object name SHOULD be reserved for very high-risk destructive action.

Not every archive.

---

# 140. FORM-UX-101 — Archive Usually Easier Than Delete

Archive can use lightweight confirmation when reversible.

---

# 141. FORM-UX-102 — Delete Button Visually Distinct

But not enormous red primary CTA dominating normal screen.

---

# 142. FORM-UX-103 — Cancel Must Be Clear

Modal destructive confirmation:

```text
[Отмена] [Удалить]
```

---

# 143. FORM-UX-104 — Default Focus Avoids Accidental Destruction

Do not autofocus destructive confirmation button.

---

# 144. AI Forms

Different from ordinary forms.

---

# 145. FORM-UX-105 — AI Context Visible

BB Assistant example:

```text
Задача: Casting Response
Контекст: Casting "..."
Язык: English
Тон: Professional
Длина: Short
```

---

# 146. FORM-UX-106 — AI Prompt Should Be Structured Where Possible

Instead of only blank prompt field, use task-specific controls.

---

# 147. FORM-UX-107 — Custom Instructions Optional

May provide additional instructions field.

---

# 148. FORM-UX-108 — AI Generate Is Not Save

`Generate` creates Draft/Recommendation.

---

# 149. FORM-UX-109 — AI Apply Is Explicit

After review:

```text
[Применить]
```

---

# 150. FORM-UX-110 — AI Apply Is Not Publish

Always preserve this distinction.

---

# 151. FORM-UX-111 — AI Error Keeps Context

If generation fails, user task/context remains selected.

---

# 152. FORM-UX-112 — AI Output Can Be Regenerated

But previous Draft SHOULD not disappear silently if history/revisions matter.

---

# 153. Theme Forms

Structured design configuration.

---

# 154. FORM-UX-113 — Theme Tokens Use Suitable Controls

Examples:

- color control + accessible text value;
- typography dropdown;
- spacing numeric/token selector.

---

# 155. FORM-UX-114 — Accessibility Validation Near Theme Form

Contrast issue SHOULD identify exact token/component combination.

---

# 156. FORM-UX-115 — Theme AI Locks Visible

Locked field:

```text
Locked
```

and excluded from AI regeneration.

---

# 157. FORM-UX-116 — Theme Publish Separate

`Save Theme Draft` and `Publish Theme` distinct.

---

# 158. Async Validation

Examples:

- URL reachability;
- unique slug;
- external account connection.

---

# 159. VAL-UX-015 — Async Validation Shows State

```text
Checking…
Available
Unavailable
Could not verify
```

---

# 160. VAL-UX-016 — Async Failure ≠ Invalid Automatically

Network timeout while validating external URL:

```text
Не удалось проверить ссылку.
```

is not identical to:

```text
Ссылка неверна.
```

---

# 161. VAL-UX-017 — Debounce Expensive Checks

Do not send validation request on every keystroke without control.

---

# 162. VAL-UX-018 — Race Condition Protection

If user changes value while previous async check runs, stale response MUST NOT overwrite validation state for new value.

---

# 163. Validation Timing Matrix

| Validation | Timing |
|---|---|
| Required | blur/submit |
| Format | blur/submit |
| Simple range | input/blur |
| Async uniqueness | debounce/blur |
| Permission | server |
| Business readiness | submit/publish |
| External reachability | async/background |
| Cross-entity dependency | server |

---

# 164. Validation Message Structure

Preferred:

```text
Problem
+
How to fix
```

Example:

```text
Этот URL недоступен для публичного QR.
Укажите HTTPS-ссылку на опубликованный ресурс.
```

---

# 165. Cross-Field Validation

Example:

```text
Start Date
End Date
```

End Date cannot be before Start Date.

Show error near related field/group.

---

# 166. Cross-Entity Validation

Example:

Primary Full Body must reference eligible Portfolio item.

This is server/domain validation.

---

# 167. Readiness vs Validation

Validation:

```text
Is this input allowed?
```

Readiness:

```text
Is the whole entity ready for the target action?
```

Keep separate.

---

# 168. Draft May Be Invalid for Publication

Admin can save:

```text
incomplete Draft
```

where domain permits.

---

# 169. Publish Must Pass Readiness

Normative.

---

# 170. Warning Acknowledgement

A warning MAY require explicit acknowledgement if significant but non-blocking.

Use sparingly.

---

# 171. Error Persistence

After failed submit, error remains until:

- value changed;
- revalidated;
- condition resolved.

---

# 172. Clear Error After Correction

Do not keep stale error after valid change.

---

# 173. Server Error Mapping

Known server validation errors SHOULD map back to corresponding field/group.

---

# 174. Unknown Server Error

Show form-level error, preserve data.

---

# 175. Conflict Error

Concurrency is distinct:

```text
Данные изменились после открытия формы.
```

not generic validation failure.

---

# 176. FORM-UX-117 — Conflict Does Not Erase User Changes

Provide recovery/compare where possible.

---

# 177. FORM-UX-118 — Stale AI Suggestion Error

```text
Предложение основано на более старой версии данных.
```

with:

```text
[Обновить анализ]
```

---

# 178. FORM-UX-119 — Form Success Must Be Explicit

After save:

```text
Изменения сохранены.
```

for important operations.

---

# 179. FORM-UX-120 — Success Should Not Overuse Toasts

Persistent context status may be better for frequent autosave.

---

# 180. Toast Appropriate For

- saved;
- copied;
- item added;
- retry started.

---

# 181. Inline Success Appropriate For

Professional Inquiry submission and larger transaction.

---

# 182. Success and Redirect

After creation:

```text
Create Project
→ Project Detail
```

usually better than returning to empty list.

---

# 183. Success and Context

After update:

remain on entity unless workflow requires next step.

---

# 184. Success Must Not Hide Warning

Example:

```text
Изменения сохранены.
Видеоссылку пока не удалось проверить.
```

can show both.

---

# 185. Form Buttons Hierarchy

Typical:

```text
Primary:
Save / Continue / Generate / Submit

Secondary:
Preview / Cancel / Back

Danger:
Delete
```

---

# 186. FORM-UX-121 — One Primary Action

Avoid:

```text
[Save] [Apply] [Update] [Continue]
```

all visually primary without semantics.

---

# 187. FORM-UX-122 — Button Labels Reflect Outcome

Examples:

```text
Сохранить проект
Создать кастинг
Опубликовать анкету
Сформировать PDF
Отправить запрос
```

---

# 188. FORM-UX-123 — Avoid Generic Submit

`Submit` acceptable internally only if user context clear, but professional copy should be more specific.

---

# 189. FORM-UX-124 — Cancel Does Not Imply Delete

Cancel stops current edit.

Delete is separate.

---

# 190. FORM-UX-125 — Enter Key Behaviour

Simple single-action forms MAY submit on Enter.

Multi-line/text-rich or high-impact forms MUST avoid accidental submission.

---

# 191. FORM-UX-126 — Textarea Enter Inserts Newline

Expected.

---

# 192. FORM-UX-127 — Keyboard Shortcut Optional

Admin MAY support:

```text
Ctrl/Cmd + S
```

for Save Draft.

Must not trigger Publish.

---

# 193. FORM-UX-128 — Shortcut Documented

If supported, visible tooltip/help.

---

# 194. Accessibility

Forms are critical accessibility surface.

---

# 195. FORM-UX-129 — Programmatic Labels

Every input has label association.

---

# 196. FORM-UX-130 — Description Association

Helper text and error text are programmatically related.

---

# 197. FORM-UX-131 — Required State Programmatic

Use proper required semantics where field truly required at this stage.

---

# 198. FORM-UX-132 — Error Summary Focus

After failed large form submit, focus MAY move to error summary.

---

# 199. FORM-UX-133 — First Error Focus

For small forms, moving to first invalid field may be appropriate.

---

# 200. FORM-UX-134 — Do Not Trap Keyboard

Except modal/dialog focus containment according to accessible patterns.

---

# 201. FORM-UX-135 — Selection State Not Color-Only

Checkbox/check icon/text.

---

# 202. FORM-UX-136 — Disabled Controls Accessible

If important to understand why unavailable, associated explanatory text must exist.

---

# 203. FORM-UX-137 — Read-Only vs Disabled

Use read-only when value is viewable/copyable but not editable.

Disabled controls may be skipped/less discoverable.

---

# 204. FORM-UX-138 — Helper Text Persistent for Critical Constraints

Do not hide required constraint only in tooltip.

---

# 205. FORM-UX-139 — Error Icons Need Text

No icon-only validation.

---

# 206. FORM-UX-140 — CAPTCHA Accessibility

If anti-bot challenge added, it must have accessible alternative.

Prefer low-friction server-side anti-abuse when possible.

---

# 207. Mobile Form UX

---

# 208. FORM-UX-141 — Correct Mobile Keyboard

Use appropriate input type/input mode.

---

# 209. FORM-UX-142 — Do Not Cover Current Field

Sticky actions and keyboard MUST not obscure focused input.

---

# 210. FORM-UX-143 — Scroll Invalid Field Into View

After validation.

---

# 211. FORM-UX-144 — Large Touch Targets

Checkbox/card selectors usable by touch.

---

# 212. FORM-UX-145 — Avoid Tiny Inline Controls

Especially visibility matrix / Builder selection.

---

# 213. FORM-UX-146 — Multi-Column Forms Collapse Logically

Desktop 2-column:

```text
First name | Last name
```

can stack mobile in semantic order.

---

# 214. FORM-UX-147 — Long Admin Tables Need Mobile Adaptation

Do not force full dense table for urgent simple edits.

---

# 215. FORM-UX-148 — Mobile Upload Supports Device File Picker

Where platform permits.

---

# 216. FORM-UX-149 — Date Picker Works on Touch

Native or accessible custom implementation.

---

# 217. International Forms

---

# 218. FORM-UX-150 — Labels and Validation Localized

If interface is EN:

```text
Email is required.
```

not Russian validation.

---

# 219. FORM-UX-151 — User Entered Text Is Not Auto-Translated

Professional inquiry remains in original language.

---

# 220. FORM-UX-152 — Locale Switch Preserves Input Where Safe

If user changes UI locale mid-form, entered values SHOULD remain.

---

# 221. FORM-UX-153 — Structured Value Does Not Change by Locale

`B2` stays `B2`.

---

# 222. FORM-UX-154 — Name Spelling

Admin manages approved international spelling explicitly.

No invisible transliteration during form save.

---

# 223. Privacy and Security

---

# 224. FORM-UX-155 — Sensitive Fields Minimized

Only collect what task requires.

---

# 225. FORM-UX-156 — Hidden Input Is Not Security

Normative.

---

# 226. FORM-UX-157 — Server Ignores Unauthorized Fields

Mass-assignment protection required.

---

# 227. FORM-UX-158 — CSRF/Request Protection

Handled according to application security architecture.

UX should degrade with safe re-auth/retry where needed.

---

# 228. FORM-UX-159 — User-Supplied Text Escaped

Applies to:

- Project/Casting labels;
- Feedback;
- file names;
- links descriptions.

---

# 229. FORM-UX-160 — No Raw HTML from Public Forms

Unless later explicitly approved/sanitized capability exists.

---

# 230. FORM-UX-161 — URL Scheme Validation

Reject unsafe schemes.

---

# 231. FORM-UX-162 — Attachment Validation

Server validates:

- type;
- size;
- security policy.

---

# 232. FORM-UX-163 — Privacy Notice Close to Collection

Where legally/policy required, consent/context belongs near relevant form.

---

# 233. FORM-UX-164 — Sensitive Visibility Confirmation

Publishing parent/guardian contact MAY warrant stronger warning than ordinary professional public contact.

---

# 234. FORM-UX-165 — Password/Secret Never Echoed in Logs

UX may show masked state; implementation security later.

---

# 235. FORM-UX-166 — API Error Must Not Leak Secrets

No DB/internal path in public validation output.

---

# 236. Performance

---

# 237. FORM-UX-167 — Initial Form Loads Essential Data First

Do not preload entire Media Library just to render Project form.

---

# 238. FORM-UX-168 — Large Selectors Load Incrementally

Search/pagination.

---

# 239. FORM-UX-169 — Validation Should Feel Immediate

Local deterministic checks respond immediately.

---

# 240. FORM-UX-170 — External Validation Can Be Deferred

URL reachability may happen after save/background if non-blocking.

---

# 241. FORM-UX-171 — Submit Must Show In-Flight State

Prevent uncertainty.

---

# 242. FORM-UX-172 — Do Not Freeze Whole Screen

Only necessary scope enters loading.

---

# 243. FORM-UX-173 — Async Success Updates Relevant Field

Example URL:

```text
Checking…
✓ Link reachable
```

---

# 244. FORM-UX-174 — Background Processing Separate from Save

Media upload can save source even while derivatives process.

---

# 245. Form State Model

Conceptual:

```text
PRISTINE
 ↓
DIRTY
 ↓
VALIDATING
 ↓
VALID
 ↓
SUBMITTING
 ↓
SAVED
```

Error branch:

```text
VALIDATING
→ INVALID
```

or:

```text
SUBMITTING
→ SAVE_FAILED
```

---

# 246. Draft Publication State Is Separate

Form state:

```text
saved
```

Business state:

```text
Draft
```

must not be conflated.

---

# 247. Validation State Matrix

| State | Meaning |
|---|---|
| Untouched | user has not interacted |
| Valid | local/server rule passed |
| Invalid | value violates rule |
| Checking | async validation |
| Warning | usable but concern |
| Conflict | source changed concurrently |
| Unauthorized | action not allowed |

---

# 248. Form Business State Matrix

| Business State | Editability |
|---|---|
| Draft | Editable |
| Ready | Editable unless workflow locks |
| Published current | Depends on domain; often edit creates Draft |
| Historical Revision | Read-only |
| Archived | Usually read-only/reactivation flow |
| Deleted | Recovery/policy-specific |

---

# 249. Historical Revision Forms

Must appear read-only.

---

# 250. FORM-UX-175 — Read-Only Historical View

Inputs SHOULD not masquerade as editable disabled form if a clean read-only representation is clearer.

---

# 251. FORM-UX-176 — Restore Creates New Draft

Never edits historical revision directly.

---

# 252. Form Dependency UX

---

# 253. FORM-UX-177 — Significant Dependency Effect Visible

Example:

```text
Changing this Primary Close-Up will affect:
• Public profile
• New questionnaire previews
• Public Builder
```

---

# 254. FORM-UX-178 — Historical Snapshots Excluded

Explicitly note when previous published documents remain unchanged.

---

# 255. FORM-UX-179 — Dependency Warnings Must Be Accurate

No generic scary warning for every field.

---

# 256. FORM-UX-180 — Relationship Selector Shows Context

Selecting Role:

```text
Project A — Role X
```

not only `Role X` if ambiguous.

---

# 257. Slug Forms

---

# 258. FORM-UX-181 — Slug Auto-Suggestion Allowed

From title.

---

# 259. FORM-UX-182 — Slug Editable Before Publish Where Allowed

---

# 260. FORM-UX-183 — Published Slug Change Warns About Links

May impact shared URLs.

---

# 261. FORM-UX-184 — Redirect Strategy Mentioned

If system supports redirect, UI MAY indicate:

```text
Старая ссылка будет перенаправлена.
```

---

# 262. Translation Forms

---

# 263. FORM-UX-185 — Locale Tabs Are Fine

For limited locales:

```text
RU | EN
```

---

# 264. FORM-UX-186 — Missing Translation Indicator

Example:

```text
EN — missing
```

---

# 265. FORM-UX-187 — Source Language Marked

If translation workflow uses source locale.

---

# 266. FORM-UX-188 — Machine Translation Labelled

BB-generated translation:

```text
AI draft
```

until human applied/reviewed.

---

# 267. Public Builder Forms

DOC-045 remains authoritative for full Builder UX.

This document applies common validation principles:

- server authority;
- state preservation;
- clear required items;
- mobile;
- accessible selection;
- safe session expiry.

---

# 268. FORM-UX-189 — Builder Temporary Fields Sanitized

Casting Name/Role safe in:

- HTML;
- PDF;
- filename;
- analytics.

---

# 269. FORM-UX-190 — Builder Cannot Edit Facts

No editable fields next to Source facts.

---

# 270. FORM-UX-191 — Builder Invalid Item Error Neutral

Do not disclose Admin permission reason.

---

# 271. FORM-UX-192 — Builder Generation Preserves Configuration on Failure

Normative.

---

# 272. Analytics Forms

Analytics configuration, if any, should not allow arbitrary script insertion in normal Admin UI.

---

# 273. FORM-UX-193 — Tracking Parameters Sanitized

Campaign labels etc. treated as untrusted text.

---

# 274. Social Publishing Forms

---

# 275. FORM-UX-194 — Social Caption Draft vs Publish

Clear distinction.

---

# 276. FORM-UX-195 — Account Selection Explicit

User knows which platform/account will receive post.

---

# 277. FORM-UX-196 — Publish Confirmation Reflects External Side Effect

Because external publish is difficult to undo fully.

---

# 278. Help/Ticket Forms

---

# 279. FORM-UX-197 — Ticket Form Preserves Context

If launched from error/module, MAY include safe technical context automatically.

---

# 280. FORM-UX-198 — User Can Review Attached Diagnostic Context Where Relevant

Avoid silently sending sensitive business data.

---

# 281. FORM-UX-199 — Forms Should Avoid Unnecessary Repetition

If name/contact already known to authenticated Admin, do not ask repeatedly.

---

# 282. FORM-UX-200 — Public Visitors Are Different

Anonymous visitor must provide sufficient reply contact because system does not know them.

---

# 283. Validation Anti-Pattern VAL-AP-001

**Generic invalid form**

No field guidance.

---

# 284. VAL-AP-002

**Client-only validation**

Server trusts browser.

---

# 285. VAL-AP-003

**Server error clears form**

---

# 286. VAL-AP-004

**All errors shown only at top**

with no relation to fields.

---

# 287. VAL-AP-005

**Color-only invalid fields**

---

# 288. VAL-AP-006

**Async race**

Old URL validation response marks new URL invalid.

---

# 289. VAL-AP-007

**Warning blocks without business rule**

---

# 290. VAL-AP-008

**Known broken required QR still generates silently**

---

# 291. Form Anti-Pattern FORM-AP-001

**Placeholder as label**

---

# 292. FORM-AP-002

**Every field required**

---

# 293. FORM-AP-003

**One huge ungrouped form**

---

# 294. FORM-AP-004

**Duplicate data entry**

Same photo/contact/details re-entered in Questionnaire.

---

# 295. FORM-AP-005

**Hidden private values in DOM**

---

# 296. FORM-AP-006

**Save silently publishes**

---

# 297. FORM-AP-007

**Disabled Publish with no reason**

---

# 298. FORM-AP-008

**AI output auto-inserted**

---

# 299. FORM-AP-009

**Upload only by drag/drop**

---

# 300. FORM-AP-010

**Delete confirmation says only “Are you sure?”**

---

# 301. FORM-AP-011

**User must re-enter valid fields after error**

---

# 302. FORM-AP-012

**Exact duplicate submit creates multiple inquiries**

---

# 303. FORM-AP-013

**Public Contact form asks for unnecessary personal data**

---

# 304. FORM-AP-014

**Mobile keyboard covers Submit/field**

---

# 305. FORM-AP-015

**Mixed-locale validation messages**

---

# 306. FORM-AP-016

**Historical revision shown as editable form**

---

# 307. FORM-AP-017

**Opportunity moved by drag without transition validation**

---

# 308. FORM-AP-018

**AI confidence disables human review**

---

# 309. FORM-AP-019

**Broken external validation treated as definite invalidity**

---

# 310. FORM-AP-020

**Autosave fails silently**

---

# 311. Form Quality Gate

Для каждой формы до implementation должны быть определены:

- [ ] Form ID / owning flow
- [ ] Purpose
- [ ] Actor
- [ ] Fields
- [ ] Labels
- [ ] Required/optional
- [ ] Defaults
- [ ] Field-level rules
- [ ] Cross-field rules
- [ ] Server validation
- [ ] Authorization
- [ ] Error messages
- [ ] Warning messages
- [ ] Success behaviour
- [ ] Persistence model
- [ ] Autosave/draft behaviour
- [ ] Duplicate-submit protection
- [ ] Async operations
- [ ] File rules
- [ ] Privacy
- [ ] Accessibility
- [ ] Mobile behaviour
- [ ] Analytics where justified
- [ ] Audit for Admin changes
- [ ] E2E test coverage

---

# 312. Validation Rule Specification Template

Каждое важное правило SHOULD иметь:

```text
Rule ID
Field / Entity
Trigger
Constraint
Client handling
Server handling
Error code
User message
Blocking level
Accessibility handling
```

---

# 313. Example

```text
VAL-QST-PRIMARY-FULLBODY-001

Entity:
Questionnaire Draft

Constraint:
A publishable questionnaire must include a valid primary Full Body image.

Blocking:
Publish only

Draft Save:
Allowed

User Message:
"Для публикации анкеты выберите фото в полный рост."
```

---

# 314. Example — Emotional Grid

```text
VAL-EMO-GRID-COUNT-001

Grid:
4×4

Required photos:
16

Selected:
15

Result:
Draft may save.
Finalization blocked.

Message:
"Для сетки 4×4 необходимо выбрать 16 фотографий. Сейчас выбрано 15."
```

---

# 315. Example — Public Inquiry

```text
VAL-FDB-CONTACT-001

Constraint:
At least one valid reply contact method is required.

Message:
"Укажите email или телефон, чтобы можно было ответить на обращение."
```

---

# 316. Example — QR

```text
VAL-QR-PUBLIC-001

Constraint:
QR target must be a valid approved public URL.

Message:
"Эту ссылку нельзя использовать в публичном QR-коде."
```

---

# 317. Example — Contact Visibility

```text
VAL-CON-PQB-001

Constraint:
Contact may appear in Public Builder only when
allow_in_public_questionnaire_builder = true.

Authority:
Server.

Client:
Must not receive ineligible contact.
```

---

# 318. Example — Casting AI

```text
VAL-CAI-REQ-001

Source does not specify age.

AI Result:
"Не указано"

Prohibited:
Inferring age requirement from role description without explicit source evidence.
```

---

# 319. E2E-FORM-001 — Public Inquiry Validation

```text
Submit incomplete inquiry
→ relevant inline errors
→ valid values preserved
→ fix
→ submit
→ one Feedback record
```

---

# 320. E2E-FORM-002 — Duplicate Submit

```text
Double-click Submit
→ one business inquiry
```

---

# 321. E2E-FORM-003 — Notification Failure

```text
Feedback persists
→ WhatsApp fails
→ form success remains
```

---

# 322. E2E-FORM-004 — Admin Draft Save

```text
Incomplete Questionnaire
→ Save Draft
→ succeeds
→ Publish blocked
```

---

# 323. E2E-FORM-005 — Publish Readiness

```text
Fix all blockers
→ Publish enabled/available
→ explicit Publish
```

---

# 324. E2E-FORM-006 — Autosave

```text
Edit Biography
→ autosave
→ saved state visible
→ refresh
→ draft restored
```

if autosave implemented.

---

# 325. E2E-FORM-007 — Autosave Failure

User clearly sees failed save and work is not falsely shown as saved.

---

# 326. E2E-FORM-008 — File Partial Failure

```text
Upload 3 files
→ 2 valid
→ 1 invalid
→ 2 retained
→ clear error for 1
```

---

# 327. E2E-FORM-009 — Grid Count

4×4 cannot finalize with anything other than 16 valid confirmed cells.

---

# 328. E2E-FORM-010 — Contact Privacy

Unauthorized Contact cannot be selected through manipulated request.

---

# 329. E2E-FORM-011 — Concurrent Save

```text
v1 opened
→ v2 saved elsewhere
→ v1 submit
→ conflict
→ no overwrite
```

---

# 330. E2E-FORM-012 — Stale AI Draft

Changed source data triggers stale warning/revalidation according to capability.

---

# 331. E2E-FORM-013 — Keyboard Public Form

Complete Professional Inquiry using keyboard only.

---

# 332. E2E-FORM-014 — Keyboard Builder

Complete essential Builder selection and generation without drag.

---

# 333. E2E-FORM-015 — Mobile Validation

Error field scrolls into usable view and keyboard does not cover required controls.

---

# 334. E2E-FORM-016 — Locale

EN form returns EN validation/error messages.

---

# 335. E2E-FORM-017 — Unsafe URL

Unsafe scheme rejected server-side even if client validation bypassed.

---

# 336. E2E-FORM-018 — Historical Revision

Historical Questionnaire cannot be edited directly.

---

# 337. E2E-FORM-019 — Visibility Update

Changing Contact Builder permission immediately affects future server projections.

---

# 338. E2E-FORM-020 — AI Failure

AI form failure leaves manual target editor/workflow usable.

---

# 339. FORM-UX-201 — Professional Data Integrity Over Convenience

If autocomplete cannot determine a fact reliably, require explicit user choice instead of guessing.

---

# 340. FORM-UX-202 — Human Approval Is an Explicit Form Action

Silence/inactivity never means approval.

---

# 341. FORM-UX-203 — UI Defaults Must Not Create New Facts

Default date, skill level, visibility, stage, etc. must be safe.

---

# 342. FORM-UX-204 — Server Normalization Must Be Predictable

Examples:

- trim harmless whitespace;
- normalize URL where deterministic;

but must not semantically rewrite user input without visibility.

---

# 343. FORM-UX-205 — Preserve Original Where Meaningful

Especially:

- Casting source;
- incoming Feedback message;
- approved official names.

---

# 344. FORM-UX-206 — Destructive Normalization Forbidden

Do not silently truncate meaningful professional text without explicit limit/error.

---

# 345. FORM-UX-207 — Character Limits Visible

For limited fields:

```text
120 / 200
```

where useful.

---

# 346. FORM-UX-208 — Limit Must Have Reason

Do not arbitrarily constrain Biography to tiny size because UI card is small.

Presentation truncation and source length are different.

---

# 347. FORM-UX-209 — Multiline Text Preserves Meaningful Line Breaks

According to content model.

---

# 348. FORM-UX-210 — Server Error Codes Stable

Frontend should not depend on fragile matching of raw human-readable exception text.

---

# 349. FORM-UX-211 — Validation Messages Localized Separately

Error code:

```text
CONTACT_INVALID_PHONE
```

maps to locale-specific user message.

---

# 350. FORM-UX-212 — Business Errors Are Not HTTP Details

Public user should see task-relevant message regardless of transport implementation.

---

# 351. FORM-UX-213 — Retry Only Where Safe

If submit outcome unknown due to network interruption, idempotency/business status should prevent duplicates.

---

# 352. FORM-UX-214 — Success Must Not Be Assumed From Button Click

Normative.

---

# 353. FORM-UX-215 — System-Generated Fields Are Read-Only

Examples:

```text
Created At
Revision Number
Generated Date
```

not editable unless specific business reason.

---

# 354. FORM-UX-216 — Derived Values Identify Their Nature

Example:

```text
Computed readiness
```

not editable text field.

---

# 355. FORM-UX-217 — Slug Suggestions Are Proposals Until Saved

---

# 356. FORM-UX-218 — AI Suggested Fields Must Be Visually Identified

Before confirmation.

---

# 357. FORM-UX-219 — Form State Must Survive Recoverable Optional Failures

Examples:

- AI;
- PDF;
- URL reachability.

---

# 358. FORM-UX-220 — Form UX Must Be Tested as Workflow

Not only field-by-field component tests.

---

# 359. Definition of Forms & Validation Compliance

Форма соответствует настоящему документу, если:

1. каждое meaningful поле имеет label;
2. обязательность понятна заранее;
3. структура формы соответствует профессиональной задаче;
4. client validation помогает, но server остаётся authority;
5. business rules валидируются server-side;
6. ошибки расположены рядом с проблемой;
7. ошибки объясняют исправление;
8. warnings отделены от blockers;
9. введённые данные не теряются после recoverable error;
10. Submit имеет in-flight state;
11. duplicate business mutations защищены;
12. Save и Publish различаются;
13. Draft может сохраняться отдельно от readiness;
14. autosave не скрывает failure;
15. file upload accessible и безопасен;
16. private values не передаются клиенту без права;
17. AI output требует explicit Apply;
18. AI Apply не означает Publish;
19. destructive actions показывают последствия;
20. mobile и keyboard workflows полноценны;
21. locale применяется к labels/errors;
22. concurrency не приводит к silent overwrite;
23. public success основывается на authoritative persistence;
24. optional service failure не уничтожает введённую работу.

---

# 360. Итоговая форма взаимодействия

```text
                       USER INPUT

                           ↓
                  CLEAR FORM STRUCTURE
                           ↓
                    CLIENT ASSISTANCE
                           ↓
                     SERVER REQUEST
                           ↓
          ┌────────────────┼────────────────┐
          │                │                │
    AUTHORIZATION      VALIDATION       CONCURRENCY
          │                │                │
          └────────────────┼────────────────┘
                           ↓
                       VALID?
                      /      \
                    NO        YES
                    │          │
                    ↓          ↓
            EXPLAIN + KEEP   PERSIST
               INPUT           │
                    │           ↓
                    │      BUSINESS STATE
                    │           ↓
                    │      SIDE EFFECTS
                    │           ↓
                    │       USER FEEDBACK
                    │           ↓
                    └─────── CONTINUE
```

---

# 361. Финальный принцип

> **Форма считается качественной не тогда, когда система умеет отклонить неверные данные, а тогда, когда пользователь заранее понимает, что требуется, быстро вводит профессионально точную информацию, легко исправляет ошибки и всегда знает, было ли действие действительно сохранено, опубликовано или отправлено. Валидация должна защищать достоверность, privacy и business rules, не превращая интерфейс в препятствие для нормальной работы.**