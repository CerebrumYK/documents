# ACCESSIBILITY

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация доступности публичной и административной частей продукта

**Целевой файл:** `docs/ux/accessibility.md`  
**Документ:** DOC-050  
**Статус:** ✅ Completed  
**Тип:** UX / Accessibility

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
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/ux-principles.md`
- `docs/ux/public-navigation.md`
- `docs/ux/admin-navigation.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/public-questionnaire-builder.md`
- `docs/ux/forms-and-validation.md`
- `docs/ux/ui-states.md`
- `docs/ux/content-hierarchy.md`
- `docs/ux/responsive-behaviour.md`

---

# 1. Назначение документа

Настоящий документ определяет обязательные требования доступности интерфейса и профессиональных материалов продукта.

Accessibility является частью базового качества продукта и MUST учитываться при:

- UX design;
- visual design;
- component design;
- frontend development;
- form design;
- responsive behaviour;
- media publishing;
- PDF generation;
- Public Questionnaire Builder;
- Theme creation;
- AI-assisted workflows;
- Admin implementation;
- automated testing;
- manual QA.

Accessibility MUST NOT рассматриваться как завершающий cosmetic pass после реализации.

---

# 2. Accessibility Goal

Цель продукта:

> **ключевые профессиональные действия должны быть выполнимы независимо от того, использует ли человек мышь, сенсорный экран, клавиатуру, увеличенный текст, screen reader или иные assistive technologies.**

---

# 3. Baseline Target

В качестве базового продуктового уровня принимается:

```text
WCAG 2.2
Level AA
```

как минимальная целевая планка для Web UI.

Это является **внутренним продуктовым требованием**, а не заявлением о юридической сертификации.

Если в будущем применимый рынок, заказчик или законодательство потребуют более высокого уровня, соответствующая нормативная документация MUST быть обновлена.

---

# 4. Accessibility IDs

Используется prefix:

```text
A11Y-*
```

Подгруппы:

```text
A11Y-SEM    Semantics
A11Y-KBD    Keyboard
A11Y-FOC    Focus
A11Y-VIS    Visual
A11Y-FORM   Forms
A11Y-MEDIA  Media
A11Y-NAV    Navigation
A11Y-STATE  UI States
A11Y-PQB    Public Builder
A11Y-PDF    Questionnaire/PDF
A11Y-ADM    Admin
A11Y-AI     AI interfaces
A11Y-RESP   Responsive/Reflow
A11Y-TEST   Testing
```

---

# 5. Core Accessibility Principles

Product MUST follow these principles:

```text
Perceivable
Operable
Understandable
Robust
```

Within the project this means:

```text
Can I perceive it?
Can I operate it?
Can I understand it?
Can assistive technology interpret it?
```

---

# 6. A11Y-001 — No Critical Mouse Dependency

Все критические flows MUST быть полностью доступны без мыши.

Минимально:

```text
Public Profile
Portfolio
Video
Questionnaire
Public Builder
Professional Inquiry

Admin Dashboard
Profile Editing
Questionnaires
Inbox
Castings
Opportunity actions
Virtual Operator review
```

---

# 7. A11Y-002 — No Critical Touch Dependency

Gesture-only interaction запрещён для critical actions.

Swipe, drag, pinch MAY дополнять интерфейс.

Но должен существовать alternative control.

---

# 8. A11Y-003 — No Hover Dependency

Hover MAY предоставлять enhancement.

Но critical information/action MUST иметь:

- visible alternative;
- focus state;
- touch alternative.

---

# 9. A11Y-SEM-001 — Semantic HTML First

Нативная семантика SHOULD использоваться там, где она соответствует задаче.

Предпочтительно:

```html
<button>
<a>
<input>
<select>
<textarea>
<nav>
main
header
footer
```

а не custom interactive div.

---

# 10. Native Before ARIA

Принцип:

> **No ARIA is better than incorrect ARIA.**

Если существует подходящий native semantic element, SHOULD использоваться он.

---

# 11. A11Y-SEM-002 — Page Landmarks

Основные страницы SHOULD иметь корректные landmarks:

```text
Header
Primary Navigation
Main
Complementary content where applicable
Footer
```

---

# 12. One Primary Main Region

На странице SHOULD быть один основной `<main>` context.

---

# 13. A11Y-SEM-003 — Page Heading

Каждая major route MUST иметь понятный page title/heading.

---

# 14. Heading Hierarchy

Heading levels MUST отражать смысловую структуру.

Не выбирать `h1/h2/h3` исключительно по визуальному размеру.

---

# 15. Example

Correct:

```text
H1 Projects
  H2 Project A
  H2 Project B
```

Project detail:

```text
H1 Project A
  H2 Role
  H2 Media
```

---

# 16. Heading Anti-Pattern

```text
H1 Actor
H4 Portfolio
H2 Video
H6 Contact
```

только ради styling.

---

# 17. A11Y-SEM-004 — Lists Are Lists

Navigation, structured option lists, Skills/Projects collections SHOULD use appropriate list semantics where useful.

---

# 18. A11Y-SEM-005 — Tables Only for Tabular Data

Не использовать HTML table для visual layout.

Admin tables are valid when data genuinely tabular.

---

# 19. Table Headers

Data tables MUST associate cells with relevant headers.

---

# 20. Table Caption / Context

Complex tables SHOULD have programmatically understandable context/title.

---

# 21. A11Y-NAV-001 — Skip Navigation

Public and Admin layouts SHOULD provide:

```text
Skip to main content
```

or equivalent keyboard-accessible bypass mechanism.

---

# 22. Repeated Navigation

Keyboard/screen-reader users must not traverse full repeated header navigation before every page content without bypass capability.

---

# 23. A11Y-KBD-001 — Keyboard Completeness

Every critical interactive control MUST support keyboard operation.

---

# 24. Standard Keyboard Behaviour

Native expected behaviour SHOULD remain intact.

Examples:

```text
Tab        move focus
Shift+Tab  reverse focus
Enter      activate link/button where appropriate
Space      activate button/checkbox
Escape     close modal/menu where pattern supports
Arrow keys component-specific where standard pattern requires
```

---

# 25. A11Y-KBD-002 — Logical Tab Order

Keyboard order MUST follow logical reading/task order.

---

# 26. Positive Tabindex

Avoid:

```text
tabindex="1"
tabindex="2"
```

and manual global focus sequencing.

Prefer natural document order.

---

# 27. A11Y-KBD-003 — No Keyboard Trap

User MUST be able to enter and leave all components.

Exception:

accessible modal dialog temporarily constrains focus while open.

---

# 28. Media Player Keyboard

Custom player controls MUST be keyboard accessible.

If native controls are adequate, prefer native/accessible proven controls.

---

# 29. Portfolio Viewer Keyboard

Must support:

```text
Close
Previous
Next
```

without pointer.

---

# 30. Builder Reordering Keyboard

If ordering enabled:

dragging cannot be only method.

Provide:

```text
Move up
Move down
```

or equivalent accessible pattern.

---

# 31. Pipeline Keyboard

Opportunity stage transition cannot require Kanban drag.

---

# 32. Emotional Grid Editor Keyboard

Where crop editor supports keyboard use, controls SHOULD provide:

- move;
- zoom;
- rotation where permitted;
- reset;
- confirm.

If precision editor cannot be completely accessible through visual crop canvas alone, equivalent numeric/button controls SHOULD be considered.

---

# 33. A11Y-FOC-001 — Visible Focus

All keyboard-focusable interactive elements MUST have clearly visible focus.

---

# 34. Focus Must Not Be Removed

CSS such as:

```css
outline: none;
```

without accessible replacement is prohibited.

---

# 35. Focus Contrast

Focus indicator must remain visible across all approved Themes/backgrounds.

---

# 36. A11Y-FOC-002 — Focus Must Not Be Obscured

Focused control must not be hidden behind:

- sticky header;
- sticky bottom CTA;
- cookie/consent bar;
- modal edge;
- virtual keyboard-related fixed UI.

---

# 37. A11Y-FOC-003 — Modal Focus

On open:

```text
focus → meaningful first element / dialog heading
```

On close:

```text
focus → control that opened dialog
```

---

# 38. Menu Focus

Opening mobile menu SHOULD move focus into menu according to component pattern.

Closing returns focus to trigger.

---

# 39. A11Y-FOC-004 — Route Change Focus

SPA/client navigation SHOULD establish meaningful focus on new page.

Usually:

- page heading;
- main content;
- equivalent route target.

---

# 40. Route Change Must Be Understandable

Screen-reader user must know that page content changed.

---

# 41. Validation Focus

For failed large form:

focus SHOULD move to:

```text
Error Summary
```

with links to invalid inputs.

For short forms MAY move to first invalid field.

---

# 42. A11Y-VIS-001 — Color Is Not Sole Signal

Never represent:

- Error;
- Warning;
- Selected;
- Published;
- Required;
- Primary;
- Active;

through color alone.

---

# 43. Use Multiple Cues

Example:

```text
⚠ Warning
```

plus text.

For selected item:

```text
✓ Selected
```

plus programmatic state.

---

# 44. A11Y-VIS-002 — Contrast

Text, controls, borders required for interpretation, focus indicators and state markers MUST meet project accessibility contrast requirements corresponding to the baseline target.

Theme publishing MUST validate critical contrast automatically where technically feasible.

---

# 45. Contrast Is Theme Guardrail

Theme AI/manual Theme Editor MUST NOT be able to publish a visual configuration that makes critical:

- text;
- CTA;
- navigation;
- focus;
- validation states;

inaccessible.

---

# 46. Decorative Content

Decorative visual elements SHOULD not require contrast as content if correctly excluded from accessibility tree.

---

# 47. A11Y-VIS-003 — Text Resize

Core flows MUST remain usable with substantially increased text size/browser zoom.

UI must:

- reflow;
- wrap;
- avoid clipping;
- preserve actions.

---

# 48. No Tiny Text Workaround

Do not make font smaller merely to fit long content on Mobile.

---

# 49. A11Y-RESP-001 — Reflow

At narrow viewport/large zoom, ordinary content MUST reflow without requiring whole-page horizontal scrolling.

---

# 50. Permitted Local Exceptions

Certain components MAY need local horizontal scrolling:

- complex Admin table;
- large timeline;
- specialized editing canvas.

The rest of the page remains reflowed.

---

# 51. A11Y-RESP-002 — Reading Order

Responsive visual reordering MUST NOT create contradictory screen-reader/keyboard order.

---

# 52. A11Y-RESP-003 — Orientation

Public critical flows must work without requiring one particular orientation.

---

# 53. A11Y-RESP-004 — Touch Targets

Critical interactive targets MUST provide sufficient physical size and separation for reliable touch operation.

Exact implementation values SHOULD be set in design tokens/component library consistent with the project's accessibility baseline.

---

# 54. Adjacent Destructive Controls

Mobile UI MUST avoid placing:

```text
Delete
Publish
Cancel
```

as tiny adjacent targets likely to cause accidental activation.

---

# 55. A11Y-VIS-004 — Text Spacing

Interface SHOULD tolerate user-applied increased:

- line height;
- letter spacing;
- word spacing;
- paragraph spacing;

without loss of function/content.

---

# 56. Fixed Height Text Containers

Avoid fixed heights that clip localized/enlarged text.

---

# 57. A11Y-VIS-005 — Meaning Does Not Depend on Shape/Position Alone

Avoid instructions like:

```text
Нажмите зелёную кнопку справа.
```

Use label:

```text
Нажмите «Сформировать PDF».
```

---

# 58. A11Y-VIS-006 — Flashing Content

Product MUST NOT use flashing/strobing visual effects that create accessibility/safety issues.

---

# 59. Animated Decorations

Theme/hero effects SHOULD be subtle, non-essential and independently reducible.

---

# 60. A11Y-VIS-007 — Reduced Motion

Product MUST respect reduced-motion preference for non-essential animation.

---

# 61. Reduced Motion Applies To

Examples:

- page transitions;
- parallax;
- decorative hero movement;
- animated card entrances;
- theme effects.

---

# 62. Reduced Motion Does Not Remove Meaning

If animation communicates progress/state, provide static/text equivalent.

---

# 63. A11Y-VIS-008 — No Mandatory Motion Interaction

Do not require:

```text
shake device
drag rapidly
swipe gesture
```

for critical action.

---

# 64. A11Y-MEDIA-001 — Professional Images Need Purposeful Alternatives

Not all images require identical alt text.

Alt strategy depends on image purpose.

---

# 65. Decorative Images

If truly decorative:

```text
alt=""
```

or semantic equivalent.

---

# 66. Professional Portraits

Professional Portfolio image alt SHOULD identify meaningful context without subjective body commentary.

Example:

```text
Екатерина, портрет крупным планом
```

or localized equivalent.

---

# 67. Full Body Image Alt

Example:

```text
Екатерина, фотография в полный рост
```

---

# 68. Role Image Alt

Example:

```text
Екатерина в роли [Role] в проекте [Project]
```

when accurate.

---

# 69. Avoid Appearance Judgement in Alt

Do not generate alt such as:

```text
beautiful slim actress...
```

Alt text describes professional context, not subjective attractiveness.

---

# 70. AI Alt Text

AI MAY propose alt text.

It MUST be treated as draft/recommendation when professional interpretation is involved.

---

# 71. Original File Name Is Not Alt

```text
IMG_9231.jpg
```

is not useful alt.

---

# 72. A11Y-MEDIA-002 — Emotional Grid Alternative

Composite Emotional Grid SHOULD have meaningful alternative context.

At minimum:

```text
Эмоциональный ряд, съёмка [date]
```

Detailed cell-level description MAY be available where useful and appropriate.

---

# 73. Emotional Grid Is Professional Visual Evidence

Accessibility representation MUST NOT invent emotions not explicitly labelled/confirmed.

If cells have confirmed emotion labels, they MAY be included.

---

# 74. A11Y-MEDIA-003 — Video Controls

Video player MUST provide accessible controls for:

- play/pause;
- seek where supported;
- volume/mute;
- fullscreen where supported.

---

# 75. No Unexpected Audio

Video/audio MUST NOT unexpectedly start audible playback.

---

# 76. A11Y-MEDIA-004 — Captions

Where speech/dialogue in Video is professionally relevant, captions SHOULD be supported.

Critical published video SHOULD aim to provide captions/subtitles when feasible.

---

# 77. Caption Provenance

AI-generated captions that have not been reviewed SHOULD not be represented as professionally verified transcript.

---

# 78. Caption Language

Caption track language should be identifiable.

---

# 79. A11Y-MEDIA-005 — Transcript

For important spoken professional media, transcript SHOULD be considered where useful/available.

---

# 80. Audio-Only Material

Audio content SHOULD have transcript/text equivalent where practical, especially when speech is central to professional evaluation.

---

# 81. A11Y-MEDIA-006 — Media Failure

Media unavailable state MUST still be accessible and textually understandable.

---

# 82. A11Y-FORM-001 — Programmatic Labels

Every meaningful form control MUST have programmatically associated label.

---

# 83. Placeholder Is Not Label

Normative.

---

# 84. A11Y-FORM-002 — Instructions Before Error

Critical input constraints SHOULD be communicated before Submit where possible.

Example:

```text
До 10 MB · PDF/JPG/PNG
```

---

# 85. A11Y-FORM-003 — Required Fields

Required state MUST be available:

- visually;
- programmatically.

---

# 86. A11Y-FORM-004 — Error Association

Validation error MUST be programmatically associated with corresponding input.

---

# 87. A11Y-FORM-005 — Error Summary

Large forms SHOULD provide error summary after failed submission.

---

# 88. Error Summary Example

```text
Не удалось сохранить форму.
Исправьте 3 поля:

• Дата съёмки
• Фото в полный рост
• URL видеовизитки
```

Items can navigate/focus corresponding field.

---

# 89. A11Y-FORM-006 — Error Not Color-Only

Input red border alone is insufficient.

---

# 90. A11Y-FORM-007 — Helper Text Associations

Instructions/helper text SHOULD be connected to relevant form control.

---

# 91. A11Y-FORM-008 — Disabled Control Explanation

If a relevant action is disabled, user needs understandable reason.

---

# 92. Read-Only vs Disabled

Use semantic distinction correctly.

Read-only values should remain perceivable/copyable.

---

# 93. A11Y-FORM-009 — Autocomplete

Common personal/contact fields SHOULD support appropriate browser autocomplete semantics where privacy/business rules allow.

---

# 94. Public Inquiry Example

Fields like:

- name;
- email;
- phone;

should use appropriate autocomplete tokens where technically applicable.

---

# 95. A11Y-FORM-010 — Input Purpose

Inputs SHOULD expose correct purpose/type to browsers/assistive technologies.

---

# 96. A11Y-FORM-011 — File Upload

File upload MUST be operable without drag-and-drop.

---

# 97. Upload Status

Per-file status MUST be announced/visible:

```text
Uploading
Uploaded
Failed
```

---

# 98. A11Y-FORM-012 — Async Validation

States such as:

```text
Checking…
Valid
Could not verify
```

should be communicated without excessive screen-reader noise.

---

# 99. A11Y-FORM-013 — Long Form Navigation

Form sections SHOULD have clear headings.

---

# 100. A11Y-FORM-014 — Autosave State

Autosave status must be perceivable:

```text
Saving…
Saved
Save failed
```

---

# 101. Do Not Announce Every Keystroke Save

Live regions must not become noisy.

Announcements should reflect meaningful state transitions.

---

# 102. A11Y-STATE-001 — State Must Be Textually Understandable

Every important state:

- loading;
- warning;
- error;
- blocked;
- stale;
- success;

must have meaningful textual representation.

---

# 103. A11Y-STATE-002 — Live Regions

Use polite/assertive announcements appropriately for:

- form submission;
- PDF completion;
- selected item addition/removal;
- critical error.

---

# 104. Do Not Overuse Live Regions

Background automatic operations should not create constant announcements.

---

# 105. A11Y-STATE-003 — Loading

Loading should not repeatedly move focus.

---

# 106. Skeletons

Skeleton placeholder content should generally be excluded from accessibility semantics if it would be read as meaningless content.

---

# 107. A11Y-STATE-004 — Processing

Example:

```text
Формируем PDF…
```

state is announced once.

On completion:

```text
PDF готов.
```

---

# 108. A11Y-STATE-005 — Blocking State

User must be able to discover:

- blocker;
- cause;
- resolution action.

---

# 109. A11Y-STATE-006 — Stale

State SHOULD be explicit text:

```text
Требует обновления
```

rather than icon/color only.

---

# 110. A11Y-STATE-007 — Historical Revision

Read-only/historical nature MUST be clear programmatically and visually.

---

# 111. A11Y-STATE-008 — Conflict

Concurrency conflict needs heading/message and actionable choices.

---

# 112. A11Y-NAV-002 — Current Navigation State

Current page/section SHOULD be available programmatically.

---

# 113. Mobile Menu Button

Must expose:

```text
expanded=true/false
```

or equivalent semantic state.

---

# 114. A11Y-NAV-003 — Language Selector

Language selector must use language names/codes, not flags alone.

---

# 115. A11Y-NAV-004 — Breadcrumbs

Breadcrumbs use appropriate navigation semantics and current-page indication.

---

# 116. A11Y-NAV-005 — Deep Links

Direct Project/Questionnaire/Emotional pages MUST preserve accessible navigation context.

---

# 117. A11Y-NAV-006 — Link Purpose

Link purpose should be understandable from link text/context.

Prefer:

```text
Скачать Compact PDF
```

rather than repeated:

```text
Подробнее
Подробнее
Подробнее
```

---

# 118. Ambiguous Icons

Icon-only controls require accessible names.

Example:

```text
aria-label="Закрыть"
```

or appropriate semantic implementation.

---

# 119. Social Icons

Must identify platform/action to assistive technologies.

---

# 120. External Links

Where opening a new window/tab is significant and project design chooses to do so, the user SHOULD be informed consistently.

---

# 121. A11Y-PQB-001 — Public Builder Keyboard Complete

Entire Public Builder core flow MUST be executable with keyboard:

```text
Template
→ Section selection
→ Item selection
→ Preview
→ Generate PDF
```

---

# 122. A11Y-PQB-002 — Selection State

Cards/checklists MUST expose programmatic selected state.

---

# 123. A11Y-PQB-003 — Required Sections

Required content needs text/semantic state:

```text
Обязательно
```

---

# 124. A11Y-PQB-004 — Reordering

Drag-only prohibited.

---

# 125. A11Y-PQB-005 — Builder Steps

Step-based Mobile flow SHOULD expose:

- current step;
- total steps where used;
- step heading.

---

# 126. Builder Example

```text
Шаг 2 из 4
Содержание
```

---

# 127. A11Y-PQB-006 — Builder Update Announcement

Adding/removing an item MAY announce concise result:

```text
Проект добавлен в анкету.
```

Do not announce full document on every selection.

---

# 128. A11Y-PQB-007 — Validation

Invalid configuration must identify exact missing/invalid section.

---

# 129. A11Y-PQB-008 — Expired Session

Expired session view needs:

- explicit heading;
- explanation;
- actionable new-session control.

---

# 130. A11Y-PQB-009 — Photo Selection

Photo cards MUST not require image recognition alone.

They SHOULD include:

- professional category;
- relevant label/date where useful.

---

# 131. A11Y-PQB-010 — Search in Builder

Search control has clear accessible label and result count/state where helpful.

---

# 132. A11Y-PQB-011 — Preview

HTML preview SHOULD preserve semantic content hierarchy.

---

# 133. A11Y-PQB-012 — PDF Ready

Completion must be announced and Download control reachable.

---

# 134. A11Y-PDF-001 — PDF Accessibility

Generated PDFs SHOULD be designed to support accessible document structure where technically feasible.

Target requirements include:

- meaningful reading order;
- document title;
- tagged logical structure where supported;
- headings;
- meaningful link text;
- image alternatives where technically supported;
- selectable text;
- sufficient contrast.

---

# 135. PDF Must Not Be Image-Only

Questionnaire PDF SHOULD NOT be produced as a single rasterized image document.

Text must remain actual text where feasible.

---

# 136. A11Y-PDF-002 — Hyperlinks

Links must remain actual clickable links.

---

# 137. A11Y-PDF-003 — QR Is Supplemental

QR MUST NOT be the only way to access a linked resource.

Human-readable/clickable link remains.

---

# 138. A11Y-PDF-004 — QR Context

QR must have a text label.

Example:

```text
Видеовизитка
```

---

# 139. A11Y-PDF-005 — PDF Reading Order

Multi-column layouts MUST preserve logical reading order.

---

# 140. A11Y-PDF-006 — Image Context

Primary photos SHOULD have meaningful accessible descriptions where PDF technology/rendering pipeline supports it.

---

# 141. A11Y-PDF-007 — Document Language

PDF SHOULD identify document language where technically supported.

---

# 142. A11Y-PDF-008 — Document Title

Generated Questionnaire SHOULD have meaningful document metadata/title where supported.

---

# 143. A11Y-PDF-009 — Do Not Encode Facts Only Visually

Example:

Language level should exist as text:

```text
English — B2
```

not only star rating graphic.

---

# 144. A11Y-PDF-010 — Print Contrast

Printable Questionnaire must preserve readability in normal print conditions.

---

# 145. A11Y-MEDIA-007 — QR Does Not Replace Media Label

Same rule applies in HTML/public pages.

---

# 146. A11Y-ADM-001 — Dashboard Actionability

Dashboard alerts MUST be keyboard reachable and understandable without relying on card color/severity color.

---

# 147. Severity Example

```text
High
Showreel unavailable
```

not red border alone.

---

# 148. A11Y-ADM-002 — Admin Sidebar

Sidebar must:

- use navigation semantics;
- expose groups appropriately;
- maintain visible current location.

---

# 149. Collapsed Sidebar

Icon-only collapsed navigation requires accessible labels/tooltips.

---

# 150. A11Y-ADM-003 — Admin Tables

Tables need:

- headers;
- logical row action labels;
- keyboard accessible controls.

---

# 151. Row Action Naming

Screen reader should hear:

```text
Открыть Project A
```

not 15 identical:

```text
Открыть
```

where feasible.

---

# 152. A11Y-ADM-004 — Status Badges

Draft/Published/Blocked states require text, not color alone.

---

# 153. A11Y-ADM-005 — Tabs

True tab interfaces MUST implement accessible tab behaviour.

If interaction behaves like ordinary page navigation, links may be more appropriate.

---

# 154. A11Y-ADM-006 — Kanban

Opportunity pipeline MUST have accessible non-drag alternative.

---

# 155. Pipeline Alternative

At minimum:

```text
Open Opportunity
→ Change Stage
→ choose valid stage
```

---

# 156. A11Y-ADM-007 — Bulk Operations

Selection count and bulk action MUST be keyboard/screen-reader accessible.

---

# 157. Bulk Status

Example:

```text
Выбрано 12 элементов.
```

---

# 158. A11Y-ADM-008 — Media Library

Media cards MUST expose:

- type;
- useful label;
- state;
- selection.

Not require visual thumbnail recognition only.

---

# 159. A11Y-ADM-009 — Emotional Grid Editor

Each cell SHOULD be individually identifiable.

Example:

```text
Ячейка 3 из 16
Статус: подтверждена
```

---

# 160. Cell Order

Cell navigation should follow stable logical grid order.

Example:

```text
row 1 col 1
row 1 col 2
...
```

---

# 161. A11Y-ADM-010 — Visibility Controls

Independent scopes must have full textual labels:

```text
Показывать на публичном сайте
Разрешать в подготовленных анкетах
Разрешать в публичном конструкторе
```

not three unlabeled switches.

---

# 162. A11Y-ADM-011 — Readiness

Readiness blockers must be navigable as a list with exact corrective actions.

---

# 163. A11Y-AI-001 — AI Output Identified

AI-generated:

- Draft;
- Extraction;
- Recommendation;

MUST be explicitly labelled.

---

# 164. AI Cannot Rely on Visual Border

Text label required:

```text
AI Draft
AI Suggestion
```

---

# 165. A11Y-AI-002 — Source and AI Output Distinguishable

Casting AI MUST preserve semantic sections:

```text
Original Source
AI Extraction
Confirmed Requirement
```

---

# 166. A11Y-AI-003 — AI Actions Keyboard Accessible

Generate, Apply, Reject, Regenerate, Edit must all support keyboard.

---

# 167. A11Y-AI-004 — AI Processing Announcements

Example:

```text
Анализ кастинга начат.
```

then:

```text
Анализ готов.
```

without continuous verbose announcements.

---

# 168. A11Y-AI-005 — AI Failure

Manual workflow remains visible and accessible.

---

# 169. A11Y-AI-006 — Stale AI

Stale warning must be textually clear.

---

# 170. A11Y-AI-007 — Confidence

If confidence is shown, it must not exist only as colored gauge.

---

# 171. A11Y-AI-008 — No Accessibility Decision by AI Alone

AI MUST NOT autonomously decide that inaccessible content is acceptable.

Deterministic accessibility checks remain authoritative where available.

---

# 172. A11Y-THM-001 — Theme Guardrail

Every Theme candidate MUST be tested against core accessibility requirements before Public Publish.

---

# 173. Theme Validation Targets

At minimum:

- text contrast;
- CTA contrast;
- link differentiation;
- focus visibility;
- error/warning states;
- selected state;
- navigation;
- form controls.

---

# 174. A11Y-THM-002 — Theme Cannot Remove Focus

Normative.

---

# 175. A11Y-THM-003 — Theme Cannot Make Links Indistinguishable

Links within body copy need sufficient visual indication beyond inaccessible color-only differences.

---

# 176. A11Y-THM-004 — Theme Cannot Encode Meaning Only by Color

Normative.

---

# 177. A11Y-THM-005 — Theme AI Accessibility Failures Block Publish

If deterministic Theme accessibility validation identifies a hard violation in critical UI:

Theme Publish MUST be blocked until corrected.

---

# 178. Theme Preview

Admin SHOULD preview accessibility across:

```text
Desktop
Tablet
Mobile
```

and representative critical screens.

---

# 179. Representative Theme Screens

At minimum:

```text
Home/Profile
Portfolio
Questionnaire
Public Builder
Contact Form
```

---

# 180. A11Y-NAV-007 — Sticky UI

Sticky navigation/action bars MUST not hide keyboard-focused content.

---

# 181. A11Y-NAV-008 — Current Section

Current navigation state should have programmatic equivalent such as `aria-current` where applicable.

---

# 182. A11Y-NAV-009 — Dropdowns

Custom dropdown/menu patterns must implement expected accessible interaction.

Avoid overengineering custom widgets where native alternatives are sufficient.

---

# 183. A11Y-NAV-010 — Mobile Drawer

Must:

- expose open/closed state;
- manage focus;
- close with accessible action;
- not leave background interactive.

---

# 184. A11Y-NAV-011 — 404/Error Pages

Must have accessible heading and recovery navigation.

---

# 185. Language Accessibility

---

# 186. A11Y-I18N-001 — Page Language

Active page language SHOULD be declared programmatically.

---

# 187. Mixed-Language Content

Substantial content in different language SHOULD be marked appropriately where implementation allows.

---

# 188. Proper Names

Do not over-mark every foreign proper name if it creates excessive noise; use semantic judgment.

---

# 189. A11Y-I18N-002 — Locale Switch

Control must announce/select actual language.

---

# 190. A11Y-I18N-003 — Validation Language

Validation/error messages follow current UI locale.

---

# 191. A11Y-I18N-004 — PDF Language

Generated localized PDF SHOULD have corresponding document language metadata where supported.

---

# 192. Cognitive Accessibility

The product SHOULD reduce unnecessary cognitive load.

---

# 193. A11Y-COG-001 — Consistent Terminology

Use canonical terms consistently:

```text
Портфолио
Эмоциональный диапазон
Анкета
Кастинг
```

---

# 194. A11Y-COG-002 — Clear Actions

Prefer:

```text
Опубликовать анкету
```

over:

```text
Proceed
```

---

# 195. A11Y-COG-003 — Consistent Component Behaviour

Same UI pattern SHOULD behave the same across modules.

---

# 196. A11Y-COG-004 — Avoid Unexpected Context Change

Changing a radio/checkbox SHOULD NOT unexpectedly navigate away unless clearly designed and communicated.

---

# 197. A11Y-COG-005 — Prevent Errors

High-impact actions require clear consequence and confirmation according to UX rules.

---

# 198. A11Y-COG-006 — Recovery

Errors SHOULD state how to recover.

---

# 199. A11Y-COG-007 — Preserve User Work

Validation/session errors SHOULD not erase correct input.

---

# 200. A11Y-COG-008 — Avoid Time Pressure

Public core flows SHOULD NOT impose short timeouts.

---

# 201. Session Expiration

If session expiry exists, it should be sufficiently generous and communicated where practical.

---

# 202. A11Y-COG-009 — No Forced Animation

Normative.

---

# 203. A11Y-COG-010 — Avoid Excessive Notifications

Virtual Operator/Admin should group repetitive alerts.

---

# 204. Status Noise

Routine successful background operations SHOULD NOT generate constant intrusive banners.

---

# 205. Authentication Accessibility

---

# 206. A11Y-AUTH-001 — Login Keyboard Complete

Admin authentication must be accessible via keyboard and assistive technologies.

---

# 207. A11Y-AUTH-002 — Password Manager Compatibility

Authentication form SHOULD not unnecessarily prevent password manager usage.

---

# 208. A11Y-AUTH-003 — Error Clarity

Authentication failure must identify actionable issue without leaking sensitive security information.

---

# 209. CAPTCHA

If eventually required, an accessible alternative MUST exist.

Prefer risk-based/low-friction protection where possible.

---

# 210. Public Anti-Abuse

Anti-bot controls MUST NOT create unnecessary barrier for casting professionals using assistive technologies.

---

# 211. Toast Accessibility

---

# 212. A11Y-STATE-009 — Toasts

Important toast messages SHOULD be announced appropriately.

But toast MUST NOT be the only representation of:

- serious error;
- destructive failure;
- transaction result requiring further action.

---

# 213. Toast Duration

Important messages should not disappear before they can reasonably be perceived.

Persistent error/context state is preferable for critical information.

---

# 214. Tooltips

---

# 215. A11Y-SEM-006 — Tooltip Is Supplemental

Critical explanation MUST NOT exist only in tooltip.

---

# 216. Tooltip Trigger

If tooltip used, it must support:

- keyboard focus;
- pointer hover where relevant;
- non-blocking dismissal.

---

# 217. Popovers / Sheets

Must expose appropriate dialog/popover semantics according to interaction type.

---

# 218. Carousel Accessibility

---

# 219. A11Y-MEDIA-008 — Carousel Manual Control

Public/Home “В образе” carousel MUST allow manual navigation.

---

# 220. Auto-Rotation

If carousel auto-rotates, user MUST be able to pause/stop it.

Preferred approach for professional content is generally no aggressive auto-rotation.

---

# 221. Carousel Position

Where helpful, announce:

```text
Slide 2 of 5
```

or semantic equivalent.

---

# 222. Carousel Controls

Previous/Next controls need accessible names.

---

# 223. Portfolio Grid

Each image/action should have distinct accessible context.

---

# 224. Modal Lightbox

Opening image detail should announce/establish dialog or page context appropriately.

---

# 225. Charts and Analytics

---

# 226. A11Y-ADM-012 — Charts Need Non-Visual Equivalent

Important analytics charts SHOULD provide:

- summary;
- data table;
- textual key findings;

so meaning does not depend solely on visual graph.

---

# 227. Chart Color

Multiple series must not be distinguished by color alone.

---

# 228. Dashboard Trend

Arrow/color alone insufficient:

```text
↑ 12%
```

needs understandable label/context.

---

# 229. Public Map

If maps are later introduced, core contact/location information must also exist textually.

---

# 230. QR Accessibility

QR is inherently visual.

Therefore every QR MUST be paired with textual link context.

---

# 231. A11Y-PDF-011 — QR Print Size

QR must remain reliably scannable at intended output size.

Exact QR technical dimensions belong DOC-130.

---

# 232. Public Search Accessibility

---

# 233. A11Y-NAV-012 — Search Label

Search input must have explicit accessible name.

---

# 234. Dynamic Search Results

Result updates SHOULD be announced carefully, e.g.:

```text
5 результатов
```

without announcing every keystroke excessively.

---

# 235. Search Result Context

Screen-reader user should hear:

```text
English — B2, Language
```

not isolated `B2`.

---

# 236. Zero Results

Explicit text:

```text
Совпадений в опубликованных данных нет.
```

---

# 237. Accessibility and Privacy

Accessibility MUST NOT require exposing private data.

---

# 238. A11Y-PRIV-001 — Hidden Means Absent

Private data MUST remain absent from:

- DOM;
- accessibility tree;
- client payload.

---

# 239. Do Not Use aria-hidden as Privacy Control

```text
aria-hidden=true
```

does not make sensitive data secure.

---

# 240. Disabled Private Item

Public Builder MUST NOT expose private item simply as disabled card for screen-reader users.

---

# 241. Accessibility and AI

AI-generated description/alt/translation MUST follow same privacy restrictions as visible content.

---

# 242. Accessibility and Performance

Assistive functionality MUST NOT rely on expensive optional client scripts where simpler semantic HTML works.

---

# 243. Progressive Enhancement

Critical links/forms/navigation SHOULD remain as robust as practical if optional JS enhancement fails.

---

# 244. Loading Performance

Screen readers should receive useful page structure without long blank waits where architecture permits.

---

# 245. Focus Stability During Refresh

Background refresh MUST NOT unexpectedly steal focus.

---

# 246. Live Data Refresh

Admin Dashboard auto-refresh must not reset keyboard position or repeatedly announce all cards.

---

# 247. Virtual Operator Updates

New observations appearing should be surfaced without disrupting current task.

---

# 248. Accessibility and Notifications

Notification center should have:

- unread textual state;
- meaningful link/action label.

---

# 249. Notification Badge

Number/color alone insufficient if critical meaning depends on it.

---

# 250. Accessibility State Acceptance Matrix

| State | Requirement |
|---|---|
| Loading | announced only when useful, no fake content |
| Empty | explicit text |
| Error | text + recovery |
| Warning | text, not color-only |
| Blocked | exact reason + action |
| Saving | state available |
| Saved | confirmation |
| Processing | task identified |
| Success | authoritative and perceivable |
| Stale | explicit label |
| Conflict | explicit explanation |
| Expired | next action |
| Unauthorized | safe accessible message |

---

# 251. Accessibility and Public Content Hierarchy

Semantic order SHOULD follow:

```text
Identity
Quick Facts
Evidence
Questionnaire
Action
```

consistent with DOC-048.

---

# 252. Screen Reader Must Not Start With Decorative Noise

Avoid exposing:

- decorative SVG names;
- repeated background descriptions;
- visual particle effects.

---

# 253. Primary Actress Photo

Alt/title strategy should remain concise enough not to overwhelm repeated professional image galleries.

---

# 254. Gallery Duplicate Alt

If several photos have identical category, contextual position/caption MAY differentiate where useful.

Avoid unnecessary verbose descriptions of every visual detail.

---

# 255. Accessibility and Emotional Photos

Descriptions should use confirmed professional labels/context.

Do not infer sensitive emotional/psychological claims from facial appearance.

---

# 256. Accessibility and Admin Source Integrity

Assistive user must have same distinction:

```text
Source
AI suggestion
Human-confirmed value
```

as visual user.

---

# 257. Accessibility and Confirmation

Confirmation dialog needs:

- clear heading;
- consequence;
- cancel;
- exact action label.

---

# 258. Example

```text
Опубликовать анкету?

После публикации будет создана новая публичная версия.

[Отмена]
[Опубликовать]
```

---

# 259. Delete Confirmation

Must identify affected entity.

---

# 260. Accessibility and Readiness

Blocker list should be semantic list.

Each corrective link has meaningful text.

---

# 261. Accessibility and Help

Contextual help links SHOULD be descriptive:

```text
Как работает Emotional Grid
```

not:

```text
Подробнее
```

---

# 262. Accessibility and Social Publishing

Platform/account selector needs full accessible labels.

Post status:

```text
Instagram — Published
TikTok — Failed
```

not icons alone.

---

# 263. Accessibility and System Status

Service state:

```text
PDF Service — Degraded
```

with textual description of impact.

---

# 264. Accessibility and Audit

Audit table/list should permit keyboard navigation and meaningful reading order.

---

# 265. Accessibility and Theme Preview

Automated contrast checks are necessary but not sufficient.

Human/manual accessibility review SHOULD complement them for major Theme changes.

---

# 266. Automated Checks Cannot Prove Full Accessibility

Automation can detect selected classes of issues.

It cannot alone verify:

- meaningful alt;
- cognitive clarity;
- logical flow;
- correct focus;
- professional context.

Therefore accessibility acceptance requires both automated and manual tests.

---

# 267. Accessibility Testing Layers

Required strategy:

```text
Static/Lint
 ↓
Automated Browser Checks
 ↓
Component Tests
 ↓
Keyboard Manual Tests
 ↓
Screen Reader Tests
 ↓
Responsive/Zoom Tests
 ↓
PDF Tests
 ↓
Critical E2E Flows
```

---

# 268. A11Y-TEST-001 — Automated Testing

CI SHOULD include accessibility automation for representative pages/components.

Specific tooling determined in Testing/Implementation docs.

---

# 269. Automated Test Coverage

At minimum representative:

```text
Home/Profile
Portfolio
Questionnaire
Public Builder
Contact Form
Admin Dashboard
Admin Form
Casting Detail
```

---

# 270. A11Y-TEST-002 — Keyboard Testing

Manual/automated keyboard walkthrough MUST verify:

- no traps;
- focus visible;
- order logical;
- all actions reachable.

---

# 271. A11Y-TEST-003 — Screen Reader

Representative manual testing SHOULD include at least one major desktop screen-reader environment and representative mobile assistive environment where feasible.

Exact supported matrix belongs compatibility/testing documents.

---

# 272. A11Y-TEST-004 — Zoom/Reflow

Test increased zoom/text scaling for:

- Public Profile;
- Questionnaire;
- Builder;
- forms;
- Admin urgent workflows.

---

# 273. A11Y-TEST-005 — Reduced Motion

Test with reduced-motion preference enabled.

---

# 274. A11Y-TEST-006 — High Contrast / User Styles

Where supported by target browsers/OS, verify critical controls remain distinguishable.

---

# 275. A11Y-TEST-007 — Color Blindness Review

Critical state distinctions should remain interpretable without relying on hue.

---

# 276. A11Y-TEST-008 — PDF

Generated Questionnaire PDF SHOULD be inspected for:

- text selection;
- reading order;
- links;
- language metadata where supported;
- document title;
- visual contrast;
- QR + link equivalence.

---

# 277. A11Y-TEST-009 — Builder

Complete Public Builder flow keyboard-only.

---

# 278. A11Y-TEST-010 — Inquiry

Complete Professional Inquiry flow keyboard-only with validation error and retry.

---

# 279. A11Y-TEST-011 — Mobile Menu

Verify focus/open/close/current section.

---

# 280. A11Y-TEST-012 — Portfolio

Open/next/previous/close without pointer.

---

# 281. A11Y-TEST-013 — Video

Play/pause/mute/seek without pointer.

---

# 282. A11Y-TEST-014 — Emotional Grid

Grid has meaningful accessible context and enlargement action is accessible.

---

# 283. A11Y-TEST-015 — Admin Dashboard

Open critical observation and reach affected entity via keyboard.

---

# 284. A11Y-TEST-016 — Opportunity Pipeline

Move stage without drag.

---

# 285. A11Y-TEST-017 — AI

Generate → review → Apply/Reject using keyboard and distinguish AI/source states.

---

# 286. A11Y-TEST-018 — Theme

Attempt to publish inaccessible contrast configuration.

Expected:

```text
blocked
+
specific issue
```

where deterministic validation detects the issue.

---

# 287. A11Y-TEST-019 — Locale

Switch RU/EN via keyboard and verify current page context retained.

---

# 288. A11Y-TEST-020 — Error Recovery

Force:

- save error;
- PDF failure;
- expired session;

and verify accessible recovery.

---

# 289. Accessibility Issue Severity

Recommended classification:

```text
A11Y-BLOCKER
A11Y-HIGH
A11Y-MEDIUM
A11Y-LOW
```

---

# 290. A11Y-BLOCKER

Examples:

- critical flow impossible with keyboard;
- private content exposed to accessibility tree;
- form impossible for screen reader;
- inaccessible critical modal;
- Theme makes primary CTA unreadable.

Release MUST block.

---

# 291. A11Y-HIGH

Examples:

- major navigation inaccessible;
- severe focus-order issue;
- critical image/context has no accessible identification;
- Builder critical step inaccessible.

Normally release-blocking for affected capability.

---

# 292. A11Y-MEDIUM

Examples:

- non-critical helper text association missing;
- secondary archive control difficult to use;
- minor heading hierarchy defect.

Should be resolved before full acceptance where practical.

---

# 293. A11Y-LOW

Examples:

- redundant wording;
- non-critical semantic enhancement.

Does not override P0/P1 accessibility defects.

---

# 294. Accessibility Release Gate

Public release MUST NOT be marked professionally ready when A11Y-BLOCKER exists in:

```text
Profile
Portfolio
Video
Questionnaire
Builder
Contact
```

---

# 295. Admin Release Gate

Admin release MUST NOT have A11Y-BLOCKER in:

```text
Dashboard
Core content editing
Questionnaires
Inbox
Castings
Critical opportunity workflow
```

---

# 296. Theme Release Gate

A Theme MUST NOT be published if known accessibility blockers affect core Public UI.

---

# 297. Accessibility Regression

Any change to:

- global navigation;
- component library;
- form controls;
- typography;
- Theme;
- modal system;

SHOULD trigger accessibility regression coverage.

---

# 298. Accessibility Component Contract

Reusable interactive component MUST document:

```text
Semantic role
Keyboard interaction
Focus behavior
States
Labels
Screen-reader behavior
Touch behavior
Reduced-motion behavior
```

---

# 299. Component Examples Requiring Contract

- Button;
- Link;
- Modal;
- Drawer;
- Dropdown;
- Tabs;
- Tooltip;
- Carousel;
- Gallery;
- File Upload;
- Search Select;
- Toast;
- Alert;
- Data Table;
- Builder Selection Card.

---

# 300. Accessible Name Stability

Accessible names SHOULD remain stable across loading states.

Avoid:

```text
Generate PDF
→ icon only
→ Download file
```

with confusing context unless state transition is intentional and announced.

---

# 301. Loading Button Example

```text
Сформировать PDF
→ Формируем PDF…
→ Скачать PDF
```

is acceptable when semantic state is clear.

---

# 302. Icon Buttons

All icon-only controls need accessible names.

Examples:

```text
Закрыть
Удалить фото
Открыть меню
Предыдущее фото
Следующее фото
```

---

# 303. Duplicate Controls

If same action appears multiple times, context SHOULD differentiate where ambiguity exists.

---

# 304. Accessibility and Public SEO

Semantic headings, link text and page titles improve both accessibility and content discoverability, but SEO MUST NOT introduce unnatural heading/order that harms accessibility.

---

# 305. Accessibility and Source Data

Accessibility metadata such as alt text SHOULD be treated as content metadata associated with source/projection, not hardcoded ad hoc into pages where repeated reuse is expected.

---

# 306. Alt Data Model

Detailed model later MAY support:

```text
localized_alt_text
professional_caption
accessibility_description
```

where business value justifies.

---

# 307. Do Not Duplicate Automatically

The same generic AI-generated alt should not be blindly copied to different images.

---

# 308. Accessibility and Localization

Alt text/captions SHOULD be localizable for supported production locales where professionally important.

---

# 309. Accessibility and Public Builder Permission

Accessibility metadata MUST follow same data permission rules as underlying content.

It cannot leak private project/contact name.

---

# 310. Accessibility and Error Logging

Assistive user-facing errors remain clean.

Internal technical error details belong diagnostics/logging.

---

# 311. Accessibility and Privacy Consent

Consent labels MUST clearly explain the action.

Avoid ambiguous:

```text
I agree
```

without associated statement.

---

# 312. Accessibility and Time Limits

Avoid time-limited public interaction unless required.

If timeout/session expiry exists, user should not lose a large amount of work without reasonable handling.

---

# 313. Public Builder Expiry

Session may expire by business policy.

The expiry model SHOULD be generous enough for professional completion and recovery should be clear.

Exact duration later.

---

# 314. Accessibility and Auto-Refresh

Automatically changing content should not repeatedly disturb focus/reading.

---

# 315. Admin Dashboard Live Changes

New alert may be indicated unobtrusively.

Do not automatically move focus.

---

# 316. Accessibility and Sorting

Sortable tables must expose:

- sortable column;
- current sort direction.

---

# 317. Accessibility and Filters

Active filter state must be programmatically detectable.

---

# 318. Accessibility and Pagination

Controls need explicit labels:

```text
Следующая страница
Предыдущая страница
Страница 3
```

where pagination used.

---

# 319. Accessibility and Infinite Scroll

If used, user needs predictable access to additional content and footer/navigation.

Prefer explicit `Load more` where infinite scroll harms navigation/accessibility.

---

# 320. Accessibility and Copy Buttons

Copy action should announce success:

```text
Ссылка скопирована.
```

---

# 321. Accessibility and External Messaging Links

WhatsApp/phone links should identify purpose:

```text
Связаться через WhatsApp
Позвонить
```

---

# 322. Accessibility and Phone Formatting

Displayed phone number should remain readable and actionable without overly fragmented screen-reader output.

---

# 323. Accessibility and Skill Levels

Do not encode proficiency solely through:

- stars;
- bars;
- color.

Textual level remains.

---

# 324. Accessibility and Language Levels

Always provide textual:

```text
English — B2
```

---

# 325. Accessibility and Opportunity Stage

Stage must be textual.

Kanban column position alone is insufficient.

---

# 326. Accessibility and Virtual Operator Severity

Severity textual:

```text
High
```

not only red/orange.

---

# 327. Accessibility and Readiness

State textual:

```text
Blocked
2 blockers
```

---

# 328. Accessibility and Social Account Health

```text
Instagram — Connected
TikTok — Reconnect required
```

---

# 329. Accessibility and QR Decode Validation

QR test itself is technical.

User-facing PDF still needs URL textual equivalent.

---

# 330. Accessibility Anti-Pattern A11Y-AP-001

**Clickable div without semantics**

---

# 331. A11Y-AP-002

**Outline removed**

---

# 332. A11Y-AP-003

**Color-only error**

---

# 333. A11Y-AP-004

**Placeholder as label**

---

# 334. A11Y-AP-005

**Drag-only Builder**

---

# 335. A11Y-AP-006

**Drag-only Opportunity Pipeline**

---

# 336. A11Y-AP-007

**Swipe-only Portfolio**

---

# 337. A11Y-AP-008

**Flag-only language selector**

---

# 338. A11Y-AP-009

**Hover-only help**

---

# 339. A11Y-AP-010

**Screen-reader user receives private hidden field**

---

# 340. A11Y-AP-011

**Auto-playing audio**

---

# 341. A11Y-AP-012

**Animation required to understand content**

---

# 342. A11Y-AP-013

**No captions for critical spoken material where captions are required by product acceptance**

---

# 343. A11Y-AP-014

**PDF rendered entirely as images**

---

# 344. A11Y-AP-015

**QR as only media access method**

---

# 345. A11Y-AP-016

**AI Draft indistinguishable from confirmed source**

---

# 346. A11Y-AP-017

**Theme removes focus indicator**

---

# 347. A11Y-AP-018

**Tiny Mobile targets**

---

# 348. A11Y-AP-019

**Fixed-height text clipping at zoom**

---

# 349. A11Y-AP-020

**Visual reordering contradicts keyboard order**

---

# 350. A11Y-AP-021

**Modal opens without moving/managing focus**

---

# 351. A11Y-AP-022

**Modal closes and focus disappears to page top**

---

# 352. A11Y-AP-023

**Screen reader announces skeleton placeholders as real content**

---

# 353. A11Y-AP-024

**Every background change announced in live region**

---

# 354. A11Y-AP-025

**Validation message in wrong locale**

---

# 355. A11Y-AP-026

**Chart only, no textual interpretation/data alternative**

---

# 356. A11Y-AP-027

**Published/Draft distinguished only by badge color**

---

# 357. A11Y-AP-028

**Primary photo category identifiable only visually**

---

# 358. A11Y-AP-029

**Builder photo selection uses thumbnails without labels/context**

---

# 359. A11Y-AP-030

**Accessibility “fixed” through ARIA while incorrect interaction model remains**

---

# 360. Accessibility Quality Gate — Public

Before release verify:

- [ ] semantic page structure;
- [ ] skip-to-content;
- [ ] global navigation keyboard complete;
- [ ] visible focus;
- [ ] locale selector accessible;
- [ ] primary photos have appropriate alternatives;
- [ ] Portfolio viewer keyboard complete;
- [ ] Video controls accessible;
- [ ] Emotional Grid has accessible context;
- [ ] Questionnaire hierarchy semantic;
- [ ] PDF links clickable;
- [ ] QR supplemented with link text;
- [ ] Public Builder keyboard complete;
- [ ] Builder required/selected states accessible;
- [ ] Contact form labelled;
- [ ] validation errors associated;
- [ ] success/error states announced;
- [ ] mobile touch targets usable;
- [ ] text zoom/reflow tested;
- [ ] reduced motion supported;
- [ ] Theme contrast validated;
- [ ] no private content in accessibility tree.

---

# 361. Accessibility Quality Gate — Admin

- [ ] Dashboard actions keyboard accessible;
- [ ] sidebar/drawer accessible;
- [ ] current module state visible/programmatic;
- [ ] tables have headers;
- [ ] row actions understandable;
- [ ] lifecycle states textual;
- [ ] forms labelled;
- [ ] readiness blockers accessible;
- [ ] Media Library cards identified;
- [ ] Emotional Grid cells identifiable;
- [ ] Questionnaire config keyboard accessible;
- [ ] Opportunity stage change available without drag;
- [ ] Casting AI source/recommendation distinction semantic;
- [ ] BB Assistant Draft clearly labelled;
- [ ] Virtual Operator severity/action understandable;
- [ ] Theme Editor supports keyboard;
- [ ] Theme accessibility blockers prevent publish where detected;
- [ ] notification/status states textual;
- [ ] session/conflict errors recoverable.

---

# 362. Accessibility Quality Gate — PDF

- [ ] actual selectable text;
- [ ] logical reading order;
- [ ] document title where supported;
- [ ] document language where supported;
- [ ] headings/structure where renderer supports tagging;
- [ ] meaningful hyperlink text;
- [ ] links are actual links;
- [ ] QR has text equivalent;
- [ ] visual contrast acceptable;
- [ ] no critical information represented only by graphics;
- [ ] correct professional content order.

---

# 363. Accessibility Quality Gate — Themes

- [ ] body text readable;
- [ ] navigation readable;
- [ ] links distinguishable;
- [ ] CTA contrast valid;
- [ ] focus indicator visible;
- [ ] form fields perceivable;
- [ ] error/warning/success states distinguishable;
- [ ] selected state not color-only;
- [ ] reduced-motion variant works;
- [ ] Desktop/Tablet/Mobile checked.

---

# 364. E2E-A11Y-001 — Public Keyboard Journey

```text
Open Profile
→ Portfolio
→ Video
→ Questionnaire
→ Contact
```

without pointer.

---

# 365. E2E-A11Y-002 — Public Builder Keyboard

```text
Open Builder
→ select Template
→ select Sections/Items
→ Preview
→ Generate PDF
```

without drag or pointer.

---

# 366. E2E-A11Y-003 — Public Inquiry Validation

Keyboard-only:

```text
submit invalid
→ errors identified
→ correct
→ submit successfully
```

---

# 367. E2E-A11Y-004 — Mobile Navigation

Screen-reader/touch:

```text
open menu
→ navigate
→ close
→ focus/context correct
```

---

# 368. E2E-A11Y-005 — Portfolio Viewer

Keyboard:

```text
open
→ next
→ previous
→ close
```

---

# 369. E2E-A11Y-006 — Video Player

Keyboard operation of critical controls.

---

# 370. E2E-A11Y-007 — Emotional Grid

Assistive user can determine:

- this is Emotional Grid;
- shooting date;
- full portfolio link.

---

# 371. E2E-A11Y-008 — Admin Dashboard

```text
Dashboard
→ critical exception
→ affected entity
→ resolution
```

keyboard-only.

---

# 372. E2E-A11Y-009 — Questionnaire Admin

```text
edit
→ readiness
→ preview
→ publish
```

with understandable states.

---

# 373. E2E-A11Y-010 — Opportunity Stage

Change stage without drag.

---

# 374. E2E-A11Y-011 — BB Assistant

```text
generate
→ identify as AI Draft
→ edit
→ Apply
```

without confusion with Published state.

---

# 375. E2E-A11Y-012 — Casting AI

Screen reader can distinguish:

```text
Source
Extraction
Confirmed Requirement
Recommendation
```

---

# 376. E2E-A11Y-013 — Theme Contrast

Known inaccessible Theme configuration cannot be Published when detected by deterministic validation.

---

# 377. E2E-A11Y-014 — Zoom/Reflow

At project target zoom/text enlargement:

- Public Profile;
- Builder;
- Contact;
- Admin Inbox;

remain usable.

---

# 378. E2E-A11Y-015 — Reduced Motion

No decorative critical animation remains mandatory.

---

# 379. E2E-A11Y-016 — Error State

PDF generation failure:

- message announced;
- Builder selections preserved;
- Retry keyboard reachable.

---

# 380. E2E-A11Y-017 — Conflict

Admin concurrency conflict is understandable without relying on visual modal styling.

---

# 381. E2E-A11Y-018 — Private Data

Private Contact is absent from:

- visual DOM;
- screen-reader tree;
- public API response.

---

# 382. E2E-A11Y-019 — Localized Validation

EN flow returns EN accessible errors and page language.

---

# 383. E2E-A11Y-020 — PDF

Generated PDF retains:

- readable text;
- clickable URLs;
- logical professional order;
- QR + textual link.

---

# 384. Accessibility Traceability

Required chain:

```text
A11Y-*
 ↓
UX Screen / Component
 ↓
FR-*
 ↓
AC-*
 ↓
TEST-A11Y-*
 ↓
Issue / Regression
```

---

# 385. Component Definition of Done

Reusable component is NOT complete until documented/tested:

```text
Default
Hover where relevant
Focus
Active
Disabled
Error
Keyboard
Touch
Screen Reader
Responsive
Reduced Motion where relevant
```

---

# 386. Screen Definition of Done

Screen is NOT complete until:

1. semantic hierarchy correct;
2. keyboard flow complete;
3. focus behaviour defined;
4. state messages accessible;
5. responsive reflow tested;
6. private data excluded;
7. form validation accessible;
8. Theme does not break accessibility.

---

# 387. Accessibility Bugs Are Functional Bugs

A control that cannot be operated by keyboard is not “visually imperfect”.

It is functionally unavailable to part of the user population.

---

# 388. Accessibility and Product Prioritization

A11Y defects in P0/P1 journeys have higher priority than decorative refinements.

---

# 389. Accessibility and Automation

Safe automation MAY:

- run contrast checks;
- run static accessibility tests;
- identify missing label associations;
- detect duplicate IDs;
- detect invalid ARIA;
- detect missing document metadata where deterministic.

---

# 390. Automation May Create Observation

Virtual Operator MAY surface accessibility defects as Content/System Health exceptions if architecture integrates such checks.

---

# 391. Automation Must Not Auto-Approve Accessibility

Passing automated checks does NOT mean complete accessibility acceptance.

---

# 392. Accessibility and Virtual Operator

Future VOP observations MAY include:

```text
Theme contrast blocker
Missing required alt text
Broken keyboard control
Unlabelled form field
PDF accessibility generation failure
```

---

# 393. Accessibility Severity in VOP

Critical public P0 accessibility defect SHOULD receive high operational priority.

---

# 394. Accessibility and BB Assistant

BB Assistant MAY help draft:

- alt text;
- captions;
- plain-language copy;
- localized accessible descriptions.

Output remains Draft when interpretation/professional facts are involved.

---

# 395. Accessibility and Casting AI

Casting AI accessibility is mainly about:

- source/recommendation separation;
- keyboard review;
- accessible requirement status.

It MUST NOT change professional match semantics based on disability-related assumptions.

---

# 396. Accessibility and Theme AI

Theme AI can propose visual style only inside accessibility guardrails.

---

# 397. Theme AI Prohibited Behaviour

It MUST NOT intentionally:

- reduce text visibility;
- hide focus;
- replace labels with unexplained icons;
- introduce flashing;
- create inaccessible interaction solely for visual effect.

---

# 398. Accessibility Governance

Accessibility requirements are cross-cutting and MUST be included in:

- module docs;
- acceptance criteria;
- test plans;
- Definition of Done;
- design review.

---

# 399. Accessibility Exception Process

If a requirement cannot technically be satisfied immediately:

1. document exact limitation;
2. classify severity;
3. provide accessible alternative where possible;
4. create tracked remediation issue;
5. do not silently mark capability fully accessible.

---

# 400. Accessibility Debt

Accessibility limitations MUST NOT remain undocumented “known quirks”.

---

# 401. Third-Party Components

Before adoption of:

- media player;
- date picker;
- rich editor;
- carousel;
- chart library;
- PDF renderer;

accessibility capabilities MUST be evaluated.

---

# 402. Third-Party Component Rule

If third-party component creates a blocker in critical flow and cannot be corrected reasonably, it SHOULD be replaced.

---

# 403. External Embedded Media

When using YouTube/Vimeo/other external embeds, project SHOULD use accessible embedding configuration where available and ensure fallback link/context exists.

---

# 404. External Provider Limitations

If accessibility issue is controlled by third-party provider:

- document limitation;
- provide alternative access where practical;
- do not misrepresent it as locally fixed.

---

# 405. Accessibility and Browser Compatibility

Accessibility behaviour is part of compatibility testing.

Supported browser matrix later MUST include assistive interaction expectations, not only visual rendering.

---

# 406. Accessibility and Print

Printed Questionnaire should preserve:

- readable type size;
- contrast;
- meaningful URLs;
- QR labels;
- logical information grouping.

---

# 407. Printed URL

Where link usefulness matters offline, a readable URL or concise link representation SHOULD be available according to PDF design.

---

# 408. Accessibility and Document Length

Compact Questionnaire should not increase font density to an unreadable level merely to force one-page output.

Professional readability has priority over arbitrary page-count target.

---

# 409. Accessibility and Responsive Content Priority

At increased zoom/narrow width:

```text
P0 remains P0
```

Do not hide critical facts to reduce page height.

---

# 410. Accessibility and Sticky CTA

At high zoom:

sticky CTA MAY need to:

- stack;
- collapse to accessible menu;
- stop being fixed;

rather than obscure large part of viewport.

---

# 411. Accessibility and Status Copy

Avoid unexplained abbreviations where target audience may not understand them.

Professional abbreviations like CEFR may be accompanied by understandable context where appropriate.

---

# 412. CEFR Example

Public:

```text
English — B2
```

is acceptable because value itself is professionally meaningful.

Detailed explanation can be available if needed.

---

# 413. Accessibility and Date Formatting

Dates should be human-readable and programmatically sensible.

Avoid ambiguous:

```text
08/09/26
```

when locale ambiguity can matter.

---

# 414. Accessibility and Link URLs

Very long raw URLs should not destroy visual layout.

Clickable descriptive label can be primary, while full URL remains accessible where needed.

---

# 415. Accessibility and Public Contact

Phone/WhatsApp/social links need descriptive accessible names.

---

# 416. Accessibility and Contact Privacy

Accessibility metadata must never reveal hidden parent/guardian contact.

---

# 417. Accessibility and Notifications Failure

Public user receives clear successful inquiry state even if internal WhatsApp alert failed.

This avoids false contradictory feedback to assistive users as well.

---

# 418. Accessibility and Error Prevention

For irreversible/external actions:

- clear label;
- confirmation where justified;
- no keyboard focus defaulting to destructive action.

---

# 419. Accessibility and Social Publish

Scheduled/published external side effect must be understandable before commit.

---

# 420. Accessibility and Opportunity Outcome

`Booked`, `Closed — Not Selected`, `Withdrawn` must be textual business states.

---

# 421. Accessibility and Primary Markers

`Primary Close-Up`, `Primary Full Body`, `Primary Questionnaire`, `Primary Emotional Grid` must have text/programmatic markers in Admin.

---

# 422. Accessibility and Public Primary Presentation

Public need not expose the word `Primary`; it simply receives prioritized content.

---

# 423. Accessibility and Archives

Archive controls must be reachable but should not burden default keyboard path unnecessarily.

---

# 424. Accessibility and Progressive Disclosure

Collapsed content control must expose:

- expanded/collapsed state;
- relationship to controlled content where pattern requires.

---

# 425. Accessibility and Accordion

Accordion button should be a real button with expanded state.

---

# 426. Accessibility and Tabs vs Accordion

Responsive transformation from Tabs → Accordion MAY occur if semantic interaction is correctly changed, not merely styled differently.

---

# 427. Accessibility and Shared Components

Public/Admin can share primitive components, but accessibility requirements remain same even if visual theme differs.

---

# 428. Accessibility and SSR/Hydration

Hydration MUST NOT temporarily produce:

- unlabeled controls;
- duplicate content;
- privacy leakage;
- broken focusable duplicates.

---

# 429. Accessibility and Loading Boundaries

Server/client loading boundaries should retain meaningful page title/context whenever possible.

---

# 430. Accessibility and Error Boundaries

React/Next-like error boundaries SHOULD render accessible error headings and recovery actions.

Exact implementation later.

---

# 431. Accessibility Acceptance Criteria

Target product accessibility is accepted when:

1. critical public journeys are keyboard operable;
2. urgent Admin workflows are keyboard operable;
3. all interactive controls have semantic accessible names;
4. focus is visible and predictable;
5. mobile menu/dialog focus is handled;
6. structured headings/landmarks are valid;
7. forms have labels and accessible validation;
8. errors are not color-only;
9. state transitions are perceivable;
10. Portfolio and media have appropriate alternatives;
11. Video is controllable without pointer;
12. Public Builder is keyboard-complete;
13. Builder reorder has non-drag path;
14. Pipeline has non-drag stage transition;
15. QR never replaces clickable/text link;
16. PDFs are not image-only;
17. PDFs preserve logical professional reading order;
18. Theme contrast/accessibility guardrails block known critical violations;
19. AI output is distinguishable from Source and confirmed fact;
20. responsive reflow preserves critical functionality;
21. private data remain absent from accessibility tree/client;
22. reduced-motion preference is respected;
23. accessibility regression testing exists;
24. no unresolved A11Y-BLOCKER remains in P0 public journey.

---

# 432. Final Public Accessibility Model

```text
                    PUBLIC PROFESSIONAL JOURNEY

                              ENTRY
                                ↓
                           IDENTIFY
                                ↓
                       QUICK PROFESSIONAL FACTS
                                ↓
                    PORTFOLIO / VIDEO / EMOTIONAL
                                ↓
                         QUESTIONNAIRE
                                ↓
                       PUBLIC BUILDER / PDF
                                ↓
                        PROFESSIONAL ACTION

Every stage must remain:

KEYBOARD OPERABLE
SCREEN-READER UNDERSTANDABLE
TOUCH USABLE
ZOOM/REFLOW SAFE
PRIVACY SAFE
```

---

# 433. Final Admin Accessibility Model

```text
                           ADMIN

                        DASHBOARD
                            ↓
                     ACTION REQUIRED
                            ↓
                      SOURCE CONTEXT
                            ↓
              EDIT / REVIEW / AI ASSISTANCE
                            ↓
                       VALIDATION
                            ↓
                    HUMAN DECISION
                            ↓
                   SAVE / PUBLISH / RESOLVE
                            ↓
                         VERIFY

Every stage must preserve:

SEMANTICS
FOCUS
KEYBOARD
STATE CLARITY
SOURCE vs AI DISTINCTION
ERROR RECOVERY
```

---

# 434. Финальный принцип

> **Accessibility в данном продукте — это не альтернативный интерфейс для отдельной группы пользователей, а требование к корректности основного интерфейса. Профессиональный посетитель должен иметь возможность изучить профиль, портфолио, видео, эмоциональный диапазон, анкету, сформировать собственный PDF и отправить профессиональное обращение без зависимости от конкретного способа ввода или визуального восприятия. Администратор должен иметь такой же доступ к управлению контентом, кастингами и решениями. Визуальная тема, responsive-layout, AI и автоматизация могут улучшать интерфейс, но никогда не могут отменять его семантическую понятность, управляемость, читаемость и доступность.**