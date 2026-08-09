# RESPONSIVE BEHAVIOUR

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация адаптивного поведения публичной и административной частей продукта

**Целевой файл:** `docs/ux/responsive-behaviour.md`  
**Документ:** DOC-049  
**Статус:** ✅ Completed  
**Тип:** UX / Responsive Behaviour

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
- `docs/customer-journey/commercial-producer.md`
- `docs/customer-journey/international-casting.md`
- `docs/customer-journey/conversion-funnel.md`
- `docs/ux/ux-principles.md`
- `docs/ux/public-navigation.md`
- `docs/ux/admin-navigation.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/public-questionnaire-builder.md`
- `docs/ux/forms-and-validation.md`
- `docs/ux/ui-states.md`
- `docs/ux/content-hierarchy.md`

---

# 1. Назначение документа

Настоящий документ определяет обязательное responsive behaviour целевого продукта.

Документ отвечает на вопросы:

1. как меняется интерфейс при изменении viewport;
2. какие функции должны сохраняться на Mobile;
3. какие операции MAY быть оптимизированы преимущественно для Desktop/Tablet;
4. как перестраивается content hierarchy;
5. как меняется navigation;
6. как адаптируются Portfolio и media;
7. как отображается Emotional Grid;
8. как работает Public Questionnaire Builder;
9. как ведут себя forms;
10. как адаптируется Admin;
11. какие touch-интеракции обязательны;
12. как предотвращается horizontal overflow;
13. как сохраняются accessibility и semantic order;
14. как адаптация влияет на performance;
15. как тестируется responsive behaviour.

---

# 2. Главный принцип

Responsive design в данном продукте означает:

> **перестроить интерфейс под доступное пространство и способ взаимодействия, сохранив профессиональную задачу, содержание и функциональные возможности.**

Responsive НЕ означает:

```text id="y0wux6"
Desktop UI
↓
уменьшить всё до ширины телефона
```

---

# 3. Каноническая модель

```text id="ktqeas"
SAME PROFESSIONAL TASK
        │
        ↓
SAME SOURCE DATA
        │
        ↓
SAME BUSINESS RULES
        │
        ↓
ADAPTIVE PRESENTATION
        │
 ┌──────┼──────┐
 ↓      ↓      ↓
Desktop Tablet Mobile
```

---

# 4. Responsive Requirement IDs

Используется prefix:

```text id="laz8vo"
RESP-UX-*
```

---

# 5. Breakpoints Are Behavioural

Точные CSS breakpoints определяются implementation/design system.

Документ фиксирует не конкретные числа, а **режимы поведения**.

---

# 6. Canonical Responsive Modes

Минимально проект должен поддерживать:

```text id="0qpk7g"
RSP-L — Large / Desktop
RSP-M — Medium / Tablet / Small Desktop
RSP-S — Small / Mobile
```

Дополнительно MAY поддерживаться:

```text id="91jym8"
RSP-XL — Large Desktop
RSP-XS — Narrow Mobile
```

---

# 7. Breakpoint Selection Rule

Breakpoint определяется моментом, когда текущая layout-модель перестаёт:

- быть читаемой;
- сохранять touch target;
- сохранять content hierarchy;
- помещать navigation;
- сохранять usable forms.

Не по названию устройства.

---

# 8. Device Independence

Нельзя предполагать:

```text id="na4cp0"
tablet = touch only
desktop = mouse only
```

Каждый режим должен учитывать:

- mouse;
- keyboard;
- touch;
- assistive technologies.

---

# 9. RESP-UX-001 — Critical Functionality Is Device-Independent

Следующие public capabilities MUST работать на всех основных viewport modes:

```text id="a75a8v"
Profile
Portfolio
Video Intro
Emotional Range
Projects
Questionnaire
Public Builder
PDF generation
Professional Contact / Feedback
Locale switch
```

---

# 10. RESP-UX-002 — Mobile Is First-Class Public Surface

Mobile не является урезанной версией сайта.

Особенно для:

- social acquisition;
- commercial producer journey;
- QR landing;
- shared links.

---

# 11. RESP-UX-003 — Responsive Reordering Is Allowed

Content MAY менять визуальный порядок между viewport modes.

Но:

- professional priority must remain correct;
- logical semantics must remain correct;
- DOM/accessibility order must remain coherent.

---

# 12. RESP-UX-004 — Responsive Hiding Is Restricted

Можно скрывать/сворачивать:

- decorative content;
- duplicate labels;
- low-priority P3;
- secondary metadata.

Нельзя скрывать только из-за маленького viewport:

- Primary CTA;
- critical P0 fact;
- mandatory validation error;
- essential navigation.

---

# 13. Content Priority by Viewport

## Desktop

Can present parallel content.

## Tablet

Moderate density.

## Mobile

Sequential hierarchy.

Но приоритет:

```text id="w12yja"
P0
→ P1
→ P2
→ P3
```

должен сохраняться.

---

# 14. First Useful View — Desktop

Recommended:

```text id="5g557g"
Primary Visual
+
Name
+
Quick Facts
+
Primary Actions
```

в одном first viewport where possible.

---

# 15. First Useful View — Mobile

Recommended:

```text id="8ax12o"
Name
Primary Visual
Core facts
Video / Questionnaire / Contact
```

без необходимости длительного scroll.

---

# 16. Mobile P0 Rule

Нельзя допускать:

```text id="ip3tk6"
Hero decorative image
↓
long Biography
↓
social links
↓
after 3 screens: Questionnaire
```

---

# 17. Public Header — Large

RSP-L MAY use:

```text id="kd9apc"
Logo/Name
Horizontal Navigation
Locale
Professional CTA
```

---

# 18. Public Header — Medium

RSP-M MAY:

- reduce spacing;
- move secondary items into More;
- keep important navigation visible.

---

# 19. Public Header — Small

RSP-S SHOULD use:

```text id="5u1rnr"
Actor Identity
Locale or locale access
Menu trigger
```

with compact form.

---

# 20. Mobile Navigation

Primary destinations:

```text id="mpnw07"
Profile
Portfolio
Emotional Range
Projects
Questionnaire
Contacts
```

remain available.

---

# 21. Sticky Public Actions

On Mobile MAY use:

```text id="r6ccge"
Video | Questionnaire | Contact
```

or context-sensitive equivalent.

---

# 22. Sticky Action Constraints

Sticky bar MUST NOT:

- cover focused form field;
- cover validation message;
- overlap browser safe area;
- consume excessive viewport;
- trap keyboard focus.

---

# 23. Safe Area

Mobile fixed UI SHOULD account for device safe areas/notches/home indicators through platform-appropriate CSS.

---

# 24. Header Height

Sticky header SHOULD become more compact on small screens.

It should not consume a large percentage of vertical viewport.

---

# 25. Scroll Behaviour

Header MAY hide/reappear during scroll.

But critical navigation/action access must remain predictable.

---

# 26. Mobile Drawer

Menu drawer/sheet MUST:

- be keyboard accessible;
- support touch;
- maintain focus;
- prevent unwanted background interaction;
- close predictably.

---

# 27. Drawer Width

Should use comfortable width.

Not tiny desktop sidebar scaled down.

---

# 28. Locale Control — Desktop

May appear directly in header:

```text id="vn4o63"
RU | EN
```

---

# 29. Locale Control — Mobile

MAY be:

- header control;
- top area of menu.

Should not be hidden several nested levels deep.

---

# 30. Responsive Hero — Desktop

Hero MAY use:

```text id="33x80m"
Text/Quick Facts | Primary Photo
```

or equivalent two-column composition.

---

# 31. Responsive Hero — Mobile

Usually stacks:

```text id="5h4vxh"
Identity
 ↓
Primary Photo
 ↓
Quick Facts
 ↓
Primary Actions
```

or photo first if professional visual design supports clear identity.

---

# 32. Hero Cropping Rule

Responsive crop MUST not distort professional representation.

---

# 33. Face Preservation

Derived responsive image SHOULD preserve meaningful face/body framing.

---

# 34. Full Body Preservation

Primary Full Body MUST have a view where entire professional framing is accessible.

Mobile must not crop it into portrait headshot accidentally.

---

# 35. Close-Up Preservation

Close-Up may use stronger crop only according to approved derivative.

---

# 36. Portfolio Grid — Large

Recommended:

```text id="49dj4q"
3–5 columns
```

depending card width and image aspect.

Exact count not normative.

---

# 37. Portfolio Grid — Medium

Typically:

```text id="loyaai"
2–3 columns
```

---

# 38. Portfolio Grid — Small

Typically:

```text id="wpbpvq"
1–2 columns
```

depending professional image readability.

---

# 39. Grid Count Rule

Column count should be based on minimum useful image size.

Not forced fixed number.

---

# 40. Portfolio Filter — Desktop

May use horizontal chips/tabs.

---

# 41. Portfolio Filter — Mobile

May use:

- horizontally scrollable chips;
- filter sheet;
- compact selector.

Must remain discoverable.

---

# 42. Horizontal Filter Scrolling

If used:

- first/last controls fully reachable;
- no hidden-only hover affordance;
- active filter visible.

---

# 43. Portfolio Detail — Desktop

Lightbox/modal MAY be centered with previous/next controls.

---

# 44. Portfolio Detail — Mobile

Prefer:

- nearly full-screen viewer;
- swipe optional;
- visible close;
- previous/next controls accessible without swipe.

---

# 45. Swipe Is Supplemental

Critical gallery navigation cannot be swipe-only.

---

# 46. Image Zoom

MAY support pinch zoom where professionally useful.

But default should remain understandable without zoom.

---

# 47. Emotional Grid — Core Principle

Composite MUST remain identifiable as a grid in all viewport modes.

---

# 48. Emotional 4×4 on Desktop

May render full matrix at comfortable size.

---

# 49. Emotional 4×4 on Mobile

Should remain a single logical composite if it is the generated Emotional Grid artifact.

Do NOT silently transform:

```text id="90btpb"
4×4
→ 2×8 list
```

if this changes the intended composite.

---

# 50. Emotional Grid Scaling

Preferred:

```text id="x2rg7q"
scale entire composite responsively
```

while preserving cell relationships.

---

# 51. Emotional Grid Detail

Mobile MAY allow tap to enlarge composite.

---

# 52. Emotional Grid Face Legibility

If scaled inline view becomes too small:

provide enlargement/fullscreen action.

---

# 53. Full Emotional Portfolio

Responsive gallery follows general Portfolio principles.

---

# 54. Video — Desktop

Player may occupy substantial content width without exceeding comfortable viewport.

---

# 55. Video — Mobile

Video should use native responsive aspect ratio.

No horizontal overflow.

---

# 56. Video Fullscreen

Native/device fullscreen support SHOULD work.

---

# 57. Video Controls

Must remain touch accessible.

---

# 58. Autoplay

Responsive behaviour does not change policy:

unexpected audio autoplay remains prohibited.

---

# 59. Projects Grid — Desktop

May use multi-column cards.

---

# 60. Projects — Mobile

Use one-column or large-card layout.

Cards should prioritize:

```text id="dvy76r"
Visual
Title
Role
Project type/date
```

---

# 61. Project Detail — Desktop

May use:

```text id="uk1dzg"
Primary media
+
Project/Role facts
```

in parallel.

---

# 62. Project Detail — Mobile

Recommended:

```text id="vdxyft"
Project Identity
Role
Primary Media
Key Facts
Description
Additional Media
CTA
```

---

# 63. Role Media — Mobile

No tiny thumbnail strip that requires precision tapping.

---

# 64. Skills — Desktop

Can use multi-column grouped lists.

---

# 65. Skills — Mobile

Stack categories vertically.

---

# 66. Languages — Mobile

Compact list remains easy to scan:

```text id="u2revd"
English — B2
Russian — Native
```

---

# 67. Training/Achievements — Responsive

Cards/rows MAY move:

```text id="sxdjrm"
multi-column
→ single-column
```

without hiding facts.

---

# 68. Questionnaire Hub — Desktop

May use side-by-side cards for:

```text id="0l5nkr"
Recommended
Compact
Extended
Casting
```

---

# 69. Questionnaire Hub — Mobile

Stack versions vertically.

Recommended Questionnaire must remain visually first.

---

# 70. Download Picker — Desktop

May use modal/popover if compact.

---

# 71. Download Picker — Mobile

Prefer sheet/full-width selector if modal would feel cramped.

---

# 72. PDF Preview — Desktop

May show document page centered with surrounding controls.

---

# 73. PDF Preview — Mobile

Must:

- fit page to viewport;
- support zoom;
- not require horizontal page scrolling as default;
- keep Download action accessible.

---

# 74. Public Builder — Desktop

Recommended pattern:

```text id="uejkz5"
Configuration Panel
+
Live Preview / Summary
```

---

# 75. Public Builder — Medium

May use:

```text id="iybdh2"
Configuration
+
collapsible preview
```

or switchable panes.

---

# 76. Public Builder — Mobile

Recommended sequential flow:

```text id="9t8jye"
Starting Point
↓
Sections
↓
Items
↓
Preview
↓
Generate
```

---

# 77. Builder Feature Parity

Mobile Builder MUST still support:

- template selection;
- block inclusion/exclusion;
- item selection;
- temporary Casting context;
- locale;
- preview;
- PDF generation.

---

# 78. Builder Desktop-Only Features

Visual conveniences MAY differ.

For example side-by-side live preview is not required on mobile.

---

# 79. Builder Selection Cards — Mobile

Touch targets large enough.

Selected state explicit.

---

# 80. Builder Reorder — Mobile

If enabled, accessible Move Up / Move Down action is preferable to precision drag.

---

# 81. Builder Sticky Actions

On Mobile:

```text id="cpsn2j"
Back | Continue
```

or:

```text id="y81rdi"
Preview | Generate PDF
```

may be fixed to bottom.

---

# 82. Builder Sticky Safety

Must account for:

- virtual keyboard;
- safe-area inset;
- long error message;
- browser controls.

---

# 83. Builder Progress

Mobile may display:

```text id="m9e0eg"
Step 3 of 4
```

Desktop need not.

---

# 84. Builder Active Session Indicator

Responsive variant:

Desktop:
```text id="dxhsae"
Моя анкета · 8 элементов
```

Mobile:
compact pill/action.

---

# 85. Public Forms — Desktop

May use two-column grouping for related short fields.

---

# 86. Public Forms — Mobile

Stack all inputs logically.

---

# 87. Form Column Reordering

DOM order MUST match logical reading order.

Avoid CSS grid rearrangement that creates:

```text id="66dthc"
visual Name → Email
keyboard Name → Organization → Phone → Email
```

---

# 88. Input Width — Mobile

Inputs usually fill available width.

---

# 89. Mobile Keyboard

Appropriate input types should trigger:

- email keyboard;
- telephone keyboard;
- numeric input.

---

# 90. Form CTA — Mobile

Submit action should remain easy to reach.

But MUST NOT be covered by on-screen keyboard.

---

# 91. File Upload — Desktop

Drag/drop zone MAY be prominent.

---

# 92. File Upload — Mobile

Native file picker/camera integration where appropriate.

Drag/drop is not required.

---

# 93. Validation — Mobile

Invalid field MUST:

- be brought into view;
- show full message;
- not be obscured by sticky UI.

---

# 94. Error Summary — Mobile

Error summary should remain concise and actionable.

---

# 95. Long Form — Mobile

Use grouped sections/steps if necessary.

---

# 96. Admin Layout — Large

Recommended:

```text id="vmmxy7"
Sidebar
+
Context Header
+
Main Workspace
+
optional auxiliary panel
```

---

# 97. Admin Layout — Medium

Sidebar MAY collapse.

Main workspace retains full task.

---

# 98. Admin Layout — Small

Desktop sidebar becomes:

```text id="e91tzj"
Drawer / Sheet
```

---

# 99. Admin Mobile Scope

Mobile MUST support urgent operational flows:

```text id="m26841"
Dashboard
Inbox
Casting review
Opportunity review
Virtual Operator alerts
Notifications
Basic status/action
```

---

# 100. Advanced Admin Editing

The following MAY be optimized primarily for larger viewports:

- precise Emotional Grid crop;
- complex Theme token editing;
- large Questionnaire structure editing;
- dense analytics tables.

---

# 101. Larger Viewport Preference ≠ Feature Removal

On Mobile advanced capability SHOULD provide at least one:

- read/review view;
- defer action;
- limited safe edit;
- explicit recommendation to use larger display.

---

# 102. Bad Mobile Admin Pattern

```text id="qu48e3"
"This page is unavailable on mobile."
```

for entire Casting/Inquiry module.

---

# 103. Acceptable Mobile Precision Limitation

For complex crop:

```text id="thndd8"
Для точной настройки кадрирования рекомендуется планшет или компьютер.
```

while preview/review remains possible.

---

# 104. Admin Dashboard — Desktop

Can use multi-column operational grid.

---

# 105. Admin Dashboard — Mobile

Priority becomes sequential:

```text id="l8ii0v"
Critical
Deadlines
New Inquiries
Pending Review
Warnings
Resolved
```

---

# 106. Dashboard Metrics on Mobile

Vanity/secondary metrics move below operational content.

---

# 107. Admin Tables — Desktop

Can use full columns.

---

# 108. Admin Tables — Medium

Hide or condense low-priority columns.

---

# 109. Admin Tables — Mobile

Do NOT simply create horizontally scrolling 12-column table for critical workflows.

Use:

- responsive card/list;
- row detail;
- priority fields.

---

# 110. Horizontal Table Scroll

MAY remain for advanced analytical/technical tables where loss of columns would be worse.

Must be clearly scrollable and accessible.

---

# 111. Table Priority

Columns must have documented importance.

Example Opportunities:

```text id="p5wlbb"
P0:
Casting
Stage
Next Action
Deadline

P1:
Source
Owner

P2:
Created At
Internal IDs
```

---

# 112. Admin List → Detail

Mobile SHOULD prefer:

```text id="g46yl6"
List summary
→ Detail
```

rather than dense inline editing.

---

# 113. Admin Context Header — Desktop

May show:

- breadcrumbs;
- state;
- readiness;
- actions;
- metadata.

---

# 114. Admin Context Header — Mobile

Prioritize:

```text id="5ke2mw"
Entity Name
State
Primary Action
```

Secondary actions move to overflow.

---

# 115. Breadcrumbs — Mobile

May compress:

```text id="0z7m7w"
Projects
› Film X
```

or show semantic parent back link instead of full deep chain.

---

# 116. Breadcrumb Information Must Not Be Lost

Current context remains understandable.

---

# 117. Admin Forms — Desktop

Can use:

- section sidebar;
- two-column form;
- inline preview.

---

# 118. Admin Forms — Mobile

Stack fields.

Advanced sections collapsed when safe.

---

# 119. Visibility Matrix — Desktop

May render columns:

```text id="kyuyqf"
Public Site | Prepared Q | Public Builder
```

---

# 120. Visibility Matrix — Mobile

Prefer per-contact grouped controls:

```text id="5h4dno"
Visibility

☑ Public Site
☑ Prepared Questionnaires
☐ Public Builder
```

instead of squeezed table.

---

# 121. Questionnaire Admin — Desktop

Can combine structure editor + preview.

---

# 122. Questionnaire Admin — Mobile

May use separate:

```text id="lf29pc"
Content
Preview
Readiness
```

tabs/steps.

---

# 123. Questionnaire Publish on Mobile

Must be possible if safe.

But high-impact confirmation should protect against accidental touch.

---

# 124. Emotional Grid Admin — Desktop

Full editor:

```text id="hqzi1a"
Source Photos
+
Grid Canvas
+
Cell Controls
+
Preview
```

---

# 125. Emotional Grid Admin — Tablet

SHOULD support full editing if touch precision sufficient.

---

# 126. Emotional Grid Admin — Mobile

Can use one-cell-at-a-time editing model if implemented safely:

```text id="cuex21"
Cell 1/16
→ crop
→ confirm
→ next
```

This MAY eventually allow full mobile support.

---

# 127. If One-Cell Mobile Editing Not Implemented

Mobile remains review-focused, with explicit larger-screen recommendation.

---

# 128. Theme Editor — Desktop

Can use:

```text id="bh6mkj"
Controls
+
Live Preview
```

---

# 129. Theme Editor — Tablet

May use switchable control/preview panes.

---

# 130. Theme Editor — Mobile

Minimum:

- inspect current theme;
- review proposal;
- inspect accessibility issues;
- avoid accidental Publish.

Full token editing MAY be simplified.

---

# 131. Casting Workspace — Desktop

Could use multi-panel:

```text id="vd554x"
Source
Requirements
Profile Match
Recommendation
```

---

# 132. Casting Workspace — Mobile

Stack logically:

```text id="kt9l8t"
Casting Summary
 ↓
Source
 ↓
Requirements
 ↓
Profile Match
 ↓
Recommendation
 ↓
Actions
```

---

# 133. Source Text — Mobile

Long casting source should use readable text width and collapsible long attachments.

---

# 134. AI Recommendation Cards — Mobile

Observation/source must remain above recommendation.

---

# 135. Opportunity Pipeline — Desktop

Kanban MAY be appropriate.

---

# 136. Opportunity Pipeline — Tablet

Kanban MAY remain with horizontal lane scrolling if usable.

---

# 137. Opportunity Pipeline — Mobile

Prefer:

```text id="uhhzi9"
List by stage
```

or stage filter.

Full horizontal Kanban is not required.

---

# 138. Pipeline Drag

Mobile drag is never only way to change stage.

---

# 139. Stage Change — Mobile

Use explicit action:

```text id="gt0y3z"
Change Stage
→ Select valid destination
→ Confirm
```

---

# 140. Inbox — Desktop

May use:

```text id="0o5gbg"
List
+
Message Detail
```

two-pane pattern.

---

# 141. Inbox — Mobile

Use:

```text id="18rn6g"
Inbox list
→ Inquiry detail
```

---

# 142. Inquiry Actions — Mobile

High-value:

- Create Casting;
- Set Next Action;
- Draft Reply;

must remain reachable.

---

# 143. Analytics — Desktop

Charts/tables may use wide canvas.

---

# 144. Analytics — Mobile

Prioritize:

- KPI;
- trend summary;
- concise chart;
- drill-down.

Avoid tiny unreadable multi-series charts.

---

# 145. Analytics Horizontal Scroll

May be used selectively for complex tables, not main KPI presentation.

---

# 146. Social Publishing — Responsive

Caption/editor/media selection should remain usable on Mobile if publishing is supported there.

---

# 147. Rich Text Editors

Toolbar MUST adapt.

Mobile MAY reduce visible controls into overflow.

Core text editing remains available.

---

# 148. BB Assistant — Desktop

Can show:

```text id="nnmwa2"
Source Context
+
Draft
```

in parallel.

---

# 149. BB Assistant — Mobile

Stack:

```text id="g8zapu"
Task/Context
 ↓
Generate
 ↓
Draft
 ↓
Edit
 ↓
Apply
```

---

# 150. Virtual Operator — Desktop

Cards/table MAY expose many metadata fields.

---

# 151. Virtual Operator — Mobile

Priority card:

```text id="q6s1pf"
Issue
Severity
Why it matters
Primary action
```

Secondary details expandable.

---

# 152. Help / Tickets — Mobile

Must remain usable for support from affected device.

---

# 153. Responsive Modals

Desktop modal MAY become bottom sheet/full-screen dialog on small viewport.

---

# 154. Large Modal Rule

If modal contains:

- long form;
- complex editing;
- many steps;

on Mobile it SHOULD become dedicated/full-screen view.

---

# 155. Nested Modal Prohibition

Responsive conversion must not create:

```text id="hvgy8l"
drawer
→ modal
→ nested modal
```

maze.

---

# 156. Dialog Keyboard Behaviour

Consistent across viewport modes.

---

# 157. Tooltips

Hover tooltip is unsuitable as sole mobile information source.

Critical helper text must be accessible by:

- inline copy;
- tap-triggered help;
- accessible description.

---

# 158. Hover-Dependent UI

Prohibited for critical actions.

---

# 159. Responsive Hover Enhancement

Desktop MAY add hover previews.

Mobile equivalent need not mimic hover, provided core task remains.

---

# 160. Touch Targets

Exact minimum size defined in DOC-050 Accessibility.

Responsive design MUST allocate sufficient size.

---

# 161. Touch Spacing

Adjacent destructive and positive actions should not be too close on small screens.

---

# 162. Gesture Policy

Gestures MAY supplement:

- swipe gallery;
- pinch zoom;
- drag reorder.

They MUST NOT be sole path for critical functionality.

---

# 163. Pointer Precision

Do not require tiny crop handles without zoom/alternate controls on touch devices.

---

# 164. Orientation

Public critical flows MUST work in Portrait.

---

# 165. Landscape

Can improve:

- Video;
- Emotional Grid;
- media;
- complex Admin editing.

But MUST NOT be mandatory for basic public journey.

---

# 166. Orientation Change

State SHOULD survive device orientation change.

---

# 167. Public Builder Orientation

Current selections/step remain.

---

# 168. Media Orientation

Fullscreen Video may naturally adapt.

---

# 169. Very Narrow Viewports

At narrow widths:

- no horizontal page overflow;
- controls may stack;
- text may wrap;
- tables become cards or scroll selectively.

---

# 170. Long Words / URLs

Must not break page width.

Use:

- wrapping;
- ellipsis with accessible full value;
- break strategies.

---

# 171. Long Actor/Project Names

Layout must support longer localized names without overlap.

---

# 172. Russian vs English Length

Responsive components cannot assume EN label length.

---

# 173. Dynamic Type / Zoom

Layout SHOULD tolerate text enlargement without breaking critical flows.

Detailed requirements DOC-050.

---

# 174. Content Width — Desktop

Long text SHOULD use readable max-width.

Do not stretch Biography across ultra-wide monitor.

---

# 175. Ultra-Wide Screens

More viewport width SHOULD NOT automatically create:

- eight-column Portfolio;
- extremely long text lines.

Use constrained content containers.

---

# 176. Large Image Displays

Professional photography MAY expand within intentional max widths.

---

# 177. Responsive Image Sources

Use appropriate derivative/srcset/sizes strategy according to architecture.

---

# 178. Image Selection

Browser should not download desktop-sized asset unnecessarily on Mobile.

---

# 179. Performance Priority — Mobile

Mobile may have:

- slower network;
- lower memory;
- less CPU.

P0 content must be optimized accordingly.

---

# 180. P0 Asset Loading

Prioritize:

- identity;
- primary image;
- quick facts;
- core CSS;
- critical actions.

---

# 181. Secondary Media

Lazy-load:

- additional Portfolio;
- project archive;
- old emotional sessions.

---

# 182. Video

Do not preload entire high-bitrate video before user intent unless performance strategy supports it.

---

# 183. Public Builder

Do not fetch all full-resolution images for selection grid.

Use thumbnails.

---

# 184. Admin Media Library

Incremental/paginated loading.

---

# 185. Layout Shift

Reserve dimensions for major media to reduce cumulative layout shift.

---

# 186. Sticky Elements and Layout Shift

Fixed UI should not appear late and cover content unexpectedly.

---

# 187. Responsive State Preservation

Changing viewport must not reset:

- current locale;
- Builder selection;
- form edits;
- current Project;
- active Admin entity.

---

# 188. Breakpoint Crossing

When browser is resized desktop→mobile:

logical state remains.

---

# 189. Example Builder

Desktop selected:

```text id="kmyauv"
3 Projects
4 Skills
EN
```

Resize mobile:

same selection persists.

---

# 190. Example Filters

Portfolio:

```text id="w48t4m"
Close-Up selected
```

remains selected after viewport change.

---

# 191. Responsive DOM Strategy

Prefer one semantic source tree where feasible.

Avoid duplicating complete content:

```text id="a0ca15"
.desktop-only
.mobile-only
```

with both rendered if this creates accessibility/privacy inconsistencies.

---

# 192. Duplicate Markup

MAY be justified for certain complex components, but MUST avoid:

- duplicate IDs;
- duplicate focus targets;
- duplicate screen-reader reading;
- inconsistent content.

---

# 193. CSS Ordering

Visual `order` SHOULD not create semantic mismatch.

---

# 194. Responsive Accessibility

Tab order should follow logical task order.

---

# 195. Keyboard on Desktop

Responsive implementation must not favor touch at expense of keyboard.

---

# 196. Keyboard on Mobile Browser

External keyboard users must still work.

---

# 197. Focus After Drawer

Same contract across responsive modes.

---

# 198. Responsive Error Placement

Errors stay adjacent to field/component after stack transition.

---

# 199. Responsive Status Badges

Badges should wrap safely.

Avoid clipping state names.

---

# 200. Responsive Tables — Priority Model

Before implementation every important table SHOULD classify columns:

```text id="e9mmpr"
essential
secondary
diagnostic
```

---

# 201. Essential Columns

Must remain immediately visible on narrow view.

---

# 202. Secondary Columns

Can move to row detail.

---

# 203. Diagnostic Columns

Can be hidden behind `Details`.

---

# 204. Responsive Charts

Charts must remain legible.

May simplify:

- label count;
- visible series;
- annotations.

Underlying data semantics remain.

---

# 205. Chart Alternative

Critical data SHOULD have textual/table summary for accessibility and narrow view.

---

# 206. Responsive QR Display

QR on public HTML/PDF preview must not be scaled below reliably scannable dimensions.

Exact print constraints later QR module.

---

# 207. QR Mobile Viewing

If user displays QR on their own phone, it is less useful for same-device navigation.

Clickable link remains available.

---

# 208. QR Desktop

May be presented adjacent to hyperlink.

---

# 209. Responsive PDF Itself

PDF uses print/page layout, not ordinary website responsive reflow.

However HTML preview around PDF is responsive.

---

# 210. Print Styles

Public HTML Questionnaire MAY have print styling if supported.

Print-specific requirements belong PDF/print architecture.

---

# 211. Responsive Date/Metadata

Metadata MAY move under title on mobile.

Do not truncate critical date semantics.

---

# 212. Responsive CTA Labels

Do not shorten professional intent into ambiguous icon if space is low.

Instead stack or use full-width button.

---

# 213. Example

Desktop:

```text id="a0zfag"
[Пригласить на кастинг]
```

Mobile should remain understandable.

Not merely:

```text id="i7dfl7"
[+]
```

---

# 214. Icon + Short Label

Allowed if meaning remains explicit.

---

# 215. Responsive Cards

Card layout MAY change:

```text id="2qsh6i"
image left + text right
→
image top + text below
```

---

# 216. Card Tap Area

Whole card MAY be clickable, but nested actions must remain distinguishable.

---

# 217. Responsive Project Media

Role-specific images/video should avoid nested tiny carousels on Mobile.

---

# 218. Responsive Carousels

Carousels MAY be used carefully.

Must support:

- visible navigation;
- swipe optional;
- keyboard where applicable;
- no auto-advance critical content.

---

# 219. Home “В образе” Carousel

Existing functionality MUST remain responsive.

---

# 220. “В образе” Mobile

Selected role showcase images may become swipeable horizontal carousel or stacked cards.

No loss of role context.

---

# 221. Responsive Blog

Blog is P3.

May use standard single-column article reading on Mobile.

Must not affect critical actor-site performance.

---

# 222. Social Publishing Admin

Media picker may use responsive gallery.

Platform selection remains clear.

---

# 223. Responsive Help Content

Long help article uses readable width.

---

# 224. Responsive Audit

Mobile audit list may show summary:

```text id="ve68dd"
Time
Action
Entity
Actor
```

with details drill-down.

---

# 225. Responsive System Status

Use status cards rather than wide tables on Mobile.

---

# 226. System Status Priority

Only services affecting current professional work require top prominence.

---

# 227. Responsive Notification Center

Mobile list naturally one-column.

Unread/action state visible.

---

# 228. Responsive Empty States

Should remain compact.

Avoid full viewport decorative illustrations that push CTA off-screen.

---

# 229. Responsive Loading

Skeletons should match current viewport layout.

Do not render desktop skeleton then dramatically reflow.

---

# 230. Responsive Error Pages

404/500 must provide usable navigation on all viewport modes.

---

# 231. Responsive Authentication

Admin login must work on narrow viewport.

No fixed-width desktop panel overflow.

---

# 232. Responsive Session Expiry

Reauthentication dialog/page usable with mobile keyboard.

---

# 233. Responsive Consent

Public consent text and checkbox should wrap cleanly.

---

# 234. Responsive Attachment List

Long filenames wrap/truncate safely without hiding remove/status action.

---

# 235. Responsive Character Counter

Keep associated with its input.

---

# 236. Responsive Inline Help

Help text may stack below control.

---

# 237. Responsive Popovers

Desktop popover MAY become bottom sheet on Mobile.

---

# 238. Popover Clipping

Must not render off-screen.

---

# 239. Menus

Dropdown menu must reposition to remain inside viewport.

---

# 240. Responsive Date Picker

Custom calendar must fit narrow viewport or use native date controls.

---

# 241. Responsive Rich Select

Searchable large selector MAY become full-screen selection sheet on Mobile.

---

# 242. Media Picker — Mobile

Could use:

```text id="ixqvdk"
Search
Filter
Selectable cards
Confirm selection
```

instead of tiny multi-select dialog.

---

# 243. Responsive Bulk Operations

Desktop supports checkboxes/table.

Mobile MAY use:

```text id="06qwg8"
Select
→ select cards/items
→ sticky bulk action
```

---

# 244. Bulk Action Count

Always visible:

```text id="v9pp2d"
Выбрано: 12
```

---

# 245. Responsive Destructive Confirmation

On Mobile button order/spacing must reduce accidental destructive taps.

---

# 246. Responsive Theme Preview

Admin should be able to explicitly preview:

```text id="57ormp"
Desktop
Tablet
Mobile
```

regardless of current device, if preview tooling supports it.

---

# 247. Current Device Preview

Also useful to open actual live preview at device viewport.

---

# 248. Theme Responsive Constraints

Theme cannot change breakpoint behaviour arbitrarily if that would violate UX contract.

---

# 249. Theme Tokens

Responsive spacing/type scale MAY be tokenized.

Core interaction behaviour remains controlled by product.

---

# 250. Responsive Typography

Exact scales belong design system.

Rules:

- headings wrap;
- no clipped text;
- body stays readable;
- button labels fit/wrap safely;
- hierarchy remains.

---

# 251. Avoid Excessively Small Fonts

Do not compensate for narrow screen by shrinking text below accessibility requirements.

Stack instead.

---

# 252. Avoid Fixed Height for Text Cards

Localized/wrapped text must not be clipped.

---

# 253. Responsive Line Length

Long body text should remain around comfortable reading widths on large displays.

---

# 254. Responsive Spacing

Small screens MAY use tighter spacing, but touch separation remains sufficient.

---

# 255. Density Modes

Admin MAY eventually support compact density on Desktop.

Mobile should prioritize touch density.

---

# 256. Responsive Professional Integrity

Layout reflow must not make:

```text id="zbcf7m"
Project title
```

appear visually attached to wrong Role/media.

---

# 257. Relationship Grouping

When columns stack, parent-child relationship labels must remain explicit.

---

# 258. Example Desktop

```text id="gqjufa"
Project A | Role X | Media
```

Mobile:

```text id="6pb1ok"
Project A
Role: X
[Media]
```

---

# 259. Responsive Localization

UI must tolerate longer language translations.

---

# 260. Example

`Questionnaire` vs `Актёрская анкета` lengths differ.

Nav/layout must adapt without hidden clipping.

---

# 261. Bidirectional Text

If future locale introduces RTL, architecture should not assume left/right meaning for logical controls.

Not current mandatory locale requirement, but avoid preventable hardcoding.

---

# 262. Responsive SEO

Server-rendered content should remain semantically same across viewport.

Do not serve entirely different factual page to mobile user.

---

# 263. Responsive Analytics

Device/viewport category MAY be recorded as analytics context.

---

# 264. Analytics Should Not Define Layout Automatically

No dynamic layout mutation based on user conversion statistics without product review.

---

# 265. Responsive Testing Modes

At minimum test:

```text id="srs46j"
narrow mobile portrait
large mobile portrait
mobile landscape
tablet portrait
tablet landscape
desktop
large desktop
```

Exact viewport suite later Testing docs.

---

# 266. Testing Real Devices

At least representative real-device testing SHOULD complement browser emulation for:

- touch;
- keyboard;
- safe areas;
- video;
- PDF;
- file upload.

---

# 267. Responsive Test — Public Hero

Verify:

- name visible;
- photo usable;
- Quick Facts;
- CTA accessible;
- no overlap.

---

# 268. Responsive Test — Navigation

Verify:

- menu usable;
- locale visible;
- focus correct;
- no off-screen item.

---

# 269. Responsive Test — Portfolio

Verify:

- Primary Close-Up/Full Body remain recognizable;
- filters usable;
- lightbox mobile-safe.

---

# 270. Responsive Test — Emotional Grid

Verify 4×4 composite:

- fits viewport;
- remains recognizable;
- can be enlarged.

---

# 271. Responsive Test — Video

Verify player:

- correct aspect ratio;
- controls touch-safe;
- fullscreen.

---

# 272. Responsive Test — Project Deep Link

Verify direct mobile entry retains:

- Actor Identity;
- Project;
- Role;
- CTA.

---

# 273. Responsive Test — Questionnaire

Verify recommended version and Download visible.

---

# 274. Responsive Test — Builder

Complete full flow on Mobile.

---

# 275. Responsive Test — Public Form

Submit professional inquiry with virtual keyboard open.

---

# 276. Responsive Test — Locale

Long translated labels do not overflow.

---

# 277. Responsive Test — Admin Dashboard

Critical action appears before secondary analytics.

---

# 278. Responsive Test — Inbox

Open inquiry and create Casting on Mobile.

---

# 279. Responsive Test — Opportunity

Change valid stage without drag on Mobile.

---

# 280. Responsive Test — Contact Visibility

Admin Mobile can understand independent visibility settings.

---

# 281. Responsive Test — Questionnaire Admin

Preview/readiness/publish accessible.

---

# 282. Responsive Test — Emotional Admin

Mobile behaviour matches documented capability level:

- full editor;
- cell editor;
- or review + larger-screen guidance.

---

# 283. Responsive Test — Errors

Long validation message does not overflow/cut off CTA.

---

# 284. Responsive Test — Loading

No massive layout shift.

---

# 285. Responsive Test — Accessibility Zoom

Layout remains usable at increased zoom/text sizing according to DOC-050 targets.

---

# 286. RESP-UX-005 — No Horizontal Page Overflow

Ordinary public/admin page MUST NOT require horizontal scrolling.

Exceptions MAY include specifically documented:

- advanced data table;
- timeline;
- large editor canvas.

---

# 287. RESP-UX-006 — Documented Horizontal Scroll

Where horizontal scrolling is intentional, it must be local to component and discoverable.

---

# 288. RESP-UX-007 — Critical Public Content Stays in Normal Flow

Do not place P0 content in horizontally scrolling carousels as the only access path.

---

# 289. RESP-UX-008 — Mobile Menu Has Full Critical Navigation

Normative.

---

# 290. RESP-UX-009 — Sticky Mobile Actions Must Not Obscure Forms

Normative.

---

# 291. RESP-UX-010 — Primary Photos Preserve Professional Framing

Normative.

---

# 292. RESP-UX-011 — Portfolio Grid Is Adaptive, Not Fixed

Column count based on usable card width.

---

# 293. RESP-UX-012 — Emotional Composite Preserves Grid Structure

Normative.

---

# 294. RESP-UX-013 — Video Maintains Aspect Ratio

Normative.

---

# 295. RESP-UX-014 — Project and Role Context Must Survive Stacking

Normative.

---

# 296. RESP-UX-015 — Prepared Questionnaire Remains First on Mobile

Normative.

---

# 297. RESP-UX-016 — Public Builder Has Functional Mobile Flow

Normative.

---

# 298. RESP-UX-017 — Builder Mobile Does Not Require Drag

Normative.

---

# 299. RESP-UX-018 — Forms Stack Semantically

Normative.

---

# 300. RESP-UX-019 — Validation Messages Remain Visible on Mobile

Normative.

---

# 301. RESP-UX-020 — Public Core Features Do Not Become Desktop-Only

Normative.

---

# 302. RESP-UX-021 — Admin Urgent Operations Remain Mobile-Usable

Normative.

---

# 303. RESP-UX-022 — Complex Admin Precision Work May Prefer Larger Screen

Only where explicitly documented.

---

# 304. RESP-UX-023 — Mobile Admin Still Allows Review and Safe Action

Even when precise editor is larger-screen optimized.

---

# 305. RESP-UX-024 — Data Tables Have Column Priority Strategy

Normative for complex Admin tables.

---

# 306. RESP-UX-025 — Pipeline Stage Change Has Non-Drag Mobile Alternative

Normative.

---

# 307. RESP-UX-026 — Responsive Reflow Does Not Change Business Meaning

Normative.

---

# 308. RESP-UX-027 — Responsive Reflow Does Not Change Visibility

Mobile must not expose more/fewer sensitive facts solely due to layout.

---

# 309. RESP-UX-028 — Locale Persists Across Responsive Modes

Normative.

---

# 310. RESP-UX-029 — Builder State Persists Across Responsive Modes

Normative.

---

# 311. RESP-UX-030 — Unsaved Admin State Persists Through Resize/Orientation

Where session remains active.

---

# 312. RESP-UX-031 — No Hover-Only Critical Action

Normative.

---

# 313. RESP-UX-032 — Touch and Keyboard Both Supported

Normative.

---

# 314. RESP-UX-033 — Responsive Images Use Suitable Derivatives

Normative.

---

# 315. RESP-UX-034 — Secondary Media Must Not Block P0 Mobile Rendering

Normative.

---

# 316. RESP-UX-035 — Long Localized Labels Must Reflow

Normative.

---

# 317. RESP-UX-036 — Fixed Heights Must Not Clip Dynamic Text

Normative.

---

# 318. RESP-UX-037 — Modals Adapt to Available Space

Large mobile workflows become full-screen/dedicated when necessary.

---

# 319. RESP-UX-038 — No Nested Responsive Modal Maze

Normative.

---

# 320. RESP-UX-039 — Safe Area Insets Respected

For fixed mobile actions where relevant.

---

# 321. RESP-UX-040 — Portrait Supports Full Public Conversion Flow

Normative.

---

# 322. Responsive Anti-Pattern RESP-AP-001

**Desktop Shrink**

Full desktop layout scaled into phone width.

---

# 323. RESP-AP-002

**Mobile Feature Removal**

Builder/contact/video inaccessible.

---

# 324. RESP-AP-003

**Horizontal Whole-Page Scroll**

---

# 325. RESP-AP-004

**Tiny Four-Column Mobile Portfolio**

---

# 326. RESP-AP-005

**Full Body Cropped Into Headshot**

---

# 327. RESP-AP-006

**Emotional 4×4 Reordered Into Different Composition**

---

# 328. RESP-AP-007

**Desktop Header Wrapped Into Two Chaotic Rows**

instead of switching pattern.

---

# 329. RESP-AP-008

**Hover-Only Menu**

---

# 330. RESP-AP-009

**Mobile Questionnaire Builder Is Desktop Split Pane Scaled Down**

---

# 331. RESP-AP-010

**Drag-Only Mobile Builder**

---

# 332. RESP-AP-011

**Virtual Keyboard Covers Submit Button**

---

# 333. RESP-AP-012

**Sticky Contact Bar Covers Validation**

---

# 334. RESP-AP-013

**Admin Mobile Disabled Entirely**

---

# 335. RESP-AP-014

**Kanban Horizontal Maze on Phone**

---

# 336. RESP-AP-015

**Wide Admin Table as Only Mobile View**

---

# 337. RESP-AP-016

**CSS Visual Reorder Breaks Keyboard Reading Order**

---

# 338. RESP-AP-017

**Duplicate Desktop/Mobile Markup Exposes Private Data**

---

# 339. RESP-AP-018

**Desktop Image Loaded at Full Resolution on Every Mobile Card**

---

# 340. RESP-AP-019

**Tiny Text to Fit Content**

---

# 341. RESP-AP-020

**Fixed-Height Localized Cards**

Text clips in Russian/English.

---

# 342. RESP-AP-021

**Country Flag as Mobile-Only Locale Control**

without language label.

---

# 343. RESP-AP-022

**Landscape Required for Public Flow**

---

# 344. RESP-AP-023

**Orientation Change Resets Builder**

---

# 345. RESP-AP-024

**Breakpoint Change Resets Form**

---

# 346. RESP-AP-025

**Theme Overrides Responsive UX Contract**

---

# 347. Responsive Page Specification Template

Для каждого major screen SHOULD быть определено:

```text id="3c0g0o"
Desktop layout
Medium layout
Mobile layout
Content order
Navigation behaviour
Primary action
Secondary actions
Hidden/collapsed content
Touch behaviour
Keyboard behaviour
Media behaviour
Error states
Overflow policy
```

---

# 348. Responsive Component Specification

For complex component:

```text id="cqau7k"
Component
Minimum useful width
Large behaviour
Medium behaviour
Small behaviour
Touch alternative
Keyboard alternative
Overflow strategy
Performance strategy
```

---

# 349. Example — Portfolio

```text id="t19fyp"
Component:
Portfolio Grid

Large:
4-column adaptive

Medium:
2–3-column adaptive

Small:
1–2 columns

Filter:
chips → scrollable chips/sheet

Image detail:
lightbox → full-screen viewer

No:
horizontal page overflow
```

Exact column numbers MAY change after visual design testing.

---

# 350. Example — Emotional Grid

```text id="2yefeb"
Component:
Emotional Grid

Large:
full composite

Medium:
scaled composite

Small:
scaled composite + enlarge

Structure:
must remain unchanged
```

---

# 351. Example — Public Builder

```text id="to34d2"
Large:
configuration + preview

Medium:
configuration + collapsible preview

Small:
step flow

Feature parity:
required
```

---

# 352. Example — Admin Inbox

```text id="l0k71i"
Large:
list + detail panes

Medium:
list + optional detail pane

Small:
list → detail
```

---

# 353. Example — Opportunity Pipeline

```text id="jswmvu"
Large:
Kanban + list alternative

Medium:
Kanban/list

Small:
stage-filtered list

Stage update:
explicit action available everywhere
```

---

# 354. Example — Visibility Settings

```text id="1xs6vk"
Large:
matrix/table possible

Small:
grouped per entity
```

---

# 355. Responsive Performance Budget Principle

Small viewport should generally receive less unnecessary media payload than large viewport.

Not because small users deserve lower quality, but because appropriate derivatives should match display need.

---

# 356. High-Density Displays

Image delivery must consider device pixel ratio without always sending Original.

---

# 357. Network-Aware Optional Optimization

Architecture MAY later consider network conditions.

Must never reduce professional factual content.

---

# 358. Reduced Data Mode

Optional future support MAY reduce autoplay/preloads/secondary media.

Core content stays accessible.

---

# 359. Responsive Rendering and SSR

If SSR/server components are used, content should not depend on client viewport to decide security projection.

---

# 360. CSS-Based Responsive Presentation

Preferred where semantic content same.

---

# 361. Client-Specific Behaviour

JS viewport logic MAY be used for interaction pattern where necessary, but must not create hydration/privacy inconsistency.

---

# 362. Responsive Privacy

The same authorization rules apply across all modes.

---

# 363. Responsive Business Rules

The same:

- Questionnaire requirements;
- Builder permissions;
- Casting state transitions;
- Contact visibility;

apply on Mobile/Desktop.

---

# 364. Responsive Validation

Mobile cannot bypass validation because fewer fields displayed.

If field is required but hidden due to responsive bug, release is invalid.

---

# 365. Responsive Publication

Admin mobile Publish invokes same readiness/security rules.

---

# 366. Responsive AI

AI output/context separation remains on all viewports.

---

# 367. Responsive Virtual Operator

Severity and action remain explicit even if secondary metadata collapses.

---

# 368. Responsive Audit

Audit detail accessible, although perhaps through drill-down rather than wide table.

---

# 369. Responsive Theme

Themes may define responsive visual tokens but cannot modify business layout contracts without explicit product support.

---

# 370. Content Hierarchy Cross-Check

Each breakpoint should be reviewed against DOC-048:

```text id="rpz1y7"
Does P0 remain P0?
Does P1 remain discoverable?
Did P2 accidentally move above P0?
Did P3 block action?
```

---

# 371. Navigation Cross-Check

Against DOC-041/DOC-042:

- all major destinations present;
- current context clear;
- no lost admin workspace.

---

# 372. Forms Cross-Check

Against DOC-046:

- labels visible;
- errors visible;
- required fields available;
- save state clear.

---

# 373. UI State Cross-Check

Against DOC-047:

- loading/error/empty responsive;
- status not clipped;
- retry accessible.

---

# 374. Public Builder Cross-Check

Against DOC-045:

mobile maintains full functional path.

---

# 375. Accessibility Cross-Check

DOC-050 will define exact requirements.

Responsive design MUST anticipate:

- text resize;
- zoom;
- focus;
- touch targets;
- semantic order.

---

# 376. Responsive Quality Gate — Public

Before release verify:

- [ ] no full-page horizontal overflow;
- [ ] global navigation works;
- [ ] locale switch works;
- [ ] Hero P0 visible;
- [ ] Primary Full Body not miscropped;
- [ ] Portfolio filters touch-safe;
- [ ] Emotional Grid readable/enlargeable;
- [ ] Video controls usable;
- [ ] Project deep links retain context;
- [ ] Questionnaire usable;
- [ ] PDF controls usable;
- [ ] Builder complete on Mobile;
- [ ] Contact/Feedback complete on Mobile;
- [ ] validation not hidden;
- [ ] 404/error navigation works;
- [ ] long localization text reflows;
- [ ] orientation change preserves state.

---

# 377. Responsive Quality Gate — Admin

- [ ] Dashboard critical items first;
- [ ] navigation drawer works;
- [ ] Inbox usable;
- [ ] Casting readable/actionable;
- [ ] Opportunity stage can be changed without drag;
- [ ] Virtual Operator alerts actionable;
- [ ] Contacts visibility understandable;
- [ ] Questionnaire readiness visible;
- [ ] Publish actions protected;
- [ ] long forms stack correctly;
- [ ] errors visible;
- [ ] system status readable;
- [ ] optional precision limitations explicitly documented.

---

# 378. E2E-RESP-001 — Mobile Public Conversion

```text id="gl9ide"
Open Home
→ Quick Facts
→ Portfolio
→ Video
→ Questionnaire
→ Professional Inquiry
```

complete on narrow portrait viewport.

---

# 379. E2E-RESP-002 — Mobile Social Entry

```text id="syvxsu"
Social deep link
→ Portfolio
→ Emotional
→ Casting CTA
```

---

# 380. E2E-RESP-003 — Mobile Project Deep Link

```text id="9fq8xe"
Direct Project URL
→ Actor Identity
→ Role
→ Media
→ Request Materials
```

---

# 381. E2E-RESP-004 — Mobile Builder

Complete:

```text id="umzop9"
Template
→ Blocks
→ Items
→ Preview
→ PDF
```

---

# 382. E2E-RESP-005 — Mobile Builder + Navigation

```text id="u4beo9"
Builder selection
→ Profile
→ return
→ selection preserved
```

---

# 383. E2E-RESP-006 — Mobile Locale

Switch RU→EN during Builder and generate EN PDF.

---

# 384. E2E-RESP-007 — Emotional 4×4 Mobile

4×4 composition remains structurally identical and can be enlarged.

---

# 385. E2E-RESP-008 — Full Body Mobile

Primary Full Body view exposes full source framing.

---

# 386. E2E-RESP-009 — Mobile Form Keyboard

Virtual keyboard does not cover current field/errors/submit path.

---

# 387. E2E-RESP-010 — Tablet Public

All critical flows work without forced desktop or phone-specific layout.

---

# 388. E2E-RESP-011 — Desktop Resize

Resize Desktop→Mobile while Builder active.

State preserved.

---

# 389. E2E-RESP-012 — Orientation Change

Portrait→Landscape→Portrait preserves current page/session/inputs.

---

# 390. E2E-RESP-013 — Admin Mobile Inquiry

```text id="54igaa"
Dashboard
→ New Inquiry
→ Create Casting
```

---

# 391. E2E-RESP-014 — Admin Mobile Opportunity

Change valid pipeline stage without drag-and-drop.

---

# 392. E2E-RESP-015 — Admin Mobile VOP

Open high-priority issue → affected entity → resolve/defer.

---

# 393. E2E-RESP-016 — Long Translation

Long RU/EN labels do not overlap navigation/buttons.

---

# 394. E2E-RESP-017 — Zoom/Text Scale

Critical flow remains usable under required accessibility zoom/text scale.

---

# 395. E2E-RESP-018 — Slow Mobile Network

P0 identity/primary actions usable before full archive/media loads.

---

# 396. E2E-RESP-019 — Tablet Emotional Editor

Validate intended target support according to chosen editing model.

---

# 397. E2E-RESP-020 — Theme

Published Theme remains usable across all target responsive modes.

---

# 398. Definition of Responsive Compliance

Экран или flow соответствует настоящему документу, если:

1. сохраняет ту же professional task на всех основных viewport modes;
2. не теряет critical functionality на Mobile;
3. сохраняет P0/P1 hierarchy;
4. не создаёт обычного full-page horizontal overflow;
5. имеет navigation suitable for touch/keyboard;
6. сохраняет professional image framing;
7. сохраняет Emotional Grid composition;
8. адаптирует media without distortion;
9. обеспечивает полноценный Mobile Questionnaire/Builder;
10. forms и validation остаются usable;
11. urgent Admin workflows доступны на Mobile;
12. complex Admin precision tasks имеют documented mobile behaviour;
13. drag/hover gestures не являются единственным critical interaction;
14. state сохраняется при resize/orientation;
15. localization не ломает layout;
16. responsive images используют подходящие derivatives;
17. optional/secondary media не блокируют P0;
18. semantic/keyboard order остаётся правильным;
19. security/visibility не зависят от viewport;
20. Theme не может нарушить responsive contracts.

---

# 399. Итоговая адаптивная модель Public

```text id="xm7baq"
                     SAME PUBLIC JOURNEY

                            │
        ┌───────────────────┼───────────────────┐
        ↓                   ↓                   ↓
     DESKTOP              TABLET              MOBILE
        │                   │                   │
 Parallel evidence      Balanced layout    Sequential P0
 Wide navigation       Hybrid navigation   Compact header
 Multi-column media    Reduced columns     Touch-first media
 Builder split view    Adaptive panes      Builder steps
        │                   │                   │
        └───────────────────┼───────────────────┘
                            ↓
                 SAME PROFESSIONAL ACTION
```

---

# 400. Итоговая адаптивная модель Admin

```text id="s2bn19"
                       SAME BUSINESS RULES

                              │
             ┌────────────────┼────────────────┐
             ↓                ↓                ↓
          DESKTOP           TABLET           MOBILE
             │                │                │
         Sidebar         Compact sidebar      Drawer
      Dense workspace     Adaptive workspace   Focused task
     Split views/tables   Hybrid views        List → Detail
     Precision editing    Most editing        Urgent operations
             │                │                │
             └────────────────┼────────────────┘
                              ↓
                 SAME AUTHORITATIVE ACTIONS
```

---

# 401. Финальный принцип

> **Responsive behaviour должно адаптировать форму взаимодействия, но не профессиональную сущность продукта. Пользователь на телефоне должен иметь тот же путь к портфолио, видеовизитке, эмоциональному диапазону, анкете, PDF и профессиональному обращению, что и пользователь на большом экране. Администратор на мобильном устройстве должен иметь возможность выполнить срочные операционные действия, а сложные precision-workflows могут использовать более подходящий интерфейс планшета или компьютера — но только как осознанная UX-оптимизация, а не как следствие неадаптированного desktop-интерфейса.**