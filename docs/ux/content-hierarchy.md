# CONTENT HIERARCHY

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная иерархия контента публичной и административной частей продукта

**Целевой файл:** `docs/ux/content-hierarchy.md`  
**Документ:** DOC-048  
**Статус:** ✅ Completed  
**Тип:** UX / Information Priority / Content Hierarchy

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
- `docs/customer-journey/conversion-funnel.md`
- `docs/ux/ux-principles.md`
- `docs/ux/public-navigation.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/public-questionnaire-builder.md`
- `docs/ux/ui-states.md`

---

# 1. Назначение документа

Настоящий документ определяет нормативную иерархию профессионального контента во всех основных представлениях продукта.

Документ отвечает на вопросы:

1. Что пользователь должен увидеть первым.
2. Что должно быть доступно без дополнительного раскрытия.
3. Какие данные являются доказательством.
4. Какие данные относятся к углублённому профессиональному контексту.
5. Что допустимо скрывать за progressive disclosure.
6. Что не должно конкурировать с основными professional actions.
7. Как hierarchy меняется в зависимости от Persona.
8. Как hierarchy адаптируется на Mobile.
9. Как hierarchy работает в Questionnaire/PDF.
10. Как избежать информационной перегрузки.
11. Как сохранять единый Source of Truth при разных представлениях.
12. Как отделять профессиональную ценность от decorative content.

---

# 2. Главный принцип

Контент должен быть организован не по объёму и не по дате добавления, а по скорости профессионального решения.

Целевая модель:

```text
WHO
 ↓
FIT
 ↓
PROOF
 ↓
DETAIL
 ↓
ACTION
```

---

# 3. Content Hierarchy ≠ Data Model

Domain Model определяет:

```text
что существует
```

Content Hierarchy определяет:

```text
что показывать первым
что вторым
что скрывать глубже
```

Одна сущность может появляться на разных уровнях hierarchy в разных contexts.

---

# 4. Content Priority IDs

Используется prefix:

```text
CH-*
```

Уровни:

```text
CH-P0
CH-P1
CH-P2
CH-P3
```

---

# 5. CH-P0 — Immediate Professional Decision

Данные, необходимые для первичной профессиональной оценки.

Канонический набор:

- Actor Name;
- professional identity;
- current primary photo;
- Primary Close-Up;
- Primary Full Body;
- Location/Base;
- Languages;
- key professional parameters;
- key Skills where relevant;
- Video Intro;
- Questionnaire;
- Professional Contact / Casting CTA.

---

# 6. P0 Rule

Пользователь SHOULD получать P0 без:

- раскрытия accordion;
- поиска по сайту;
- чтения полной Biography;
- скачивания PDF;
- открытия archive;
- регистрации.

---

# 7. CH-P1 — Professional Evidence

Контент, подтверждающий пригодность после первичной qualification.

Канонически:

- additional Main Portfolio;
- Emotional Grid;
- Showreel;
- selected Projects;
- Roles;
- role-specific media;
- Skills;
- Languages;
- current visual evidence.

---

# 8. CH-P2 — Professional Context

Контекст, повышающий доверие и полноту оценки:

- full Projects list;
- Training;
- Achievements;
- extended Skills;
- additional Video/Audio;
- detailed Biography;
- performances;
- supporting professional links.

---

# 9. CH-P3 — Archive / Extended Context

Низкоприоритетный для первого решения контент:

- historical materials;
- archive projects;
- older sessions;
- Blog;
- extended background;
- non-critical supplementary materials.

---

# 10. Priority Is Not Importance

`P3` не означает «ненужный».

Это означает:

```text
не должен конкурировать
с более важным контентом
в первом профессиональном journey
```

---

# 11. Professional Priority Matrix

| Уровень | Задача |
|---|---|
| P0 | Кто актриса и подходит ли базово |
| P1 | Есть ли доказательства |
| P2 | Каков профессиональный контекст |
| P3 | Дополнительная глубина / история |

---

# 12. Information Funnel

```text
IDENTITY
  ↓
QUICK FACTS
  ↓
PRIMARY VISUAL EVIDENCE
  ↓
PERFORMANCE EVIDENCE
  ↓
EXPERIENCE
  ↓
EXTENDED CONTEXT
  ↓
PROFESSIONAL ACTION
```

---

# 13. CH-001 — Identity Always First

Любой major public Entry Point MUST позволять быстро идентифицировать актрису.

Минимум:

```text
Name
Professional Identity
Photo / Visual Context
```

---

# 14. CH-002 — Current Appearance Has High Priority

Для актёрского профиля current visual representation имеет больший первичный вес, чем длинный текст Biography.

---

# 15. CH-003 — Primary Photos Before Gallery

Перед расширенным Portfolio пользователь должен быстро получить:

```text
Primary Close-Up
Primary Full Body
```

---

# 16. CH-004 — Structured Facts Before Narrative

Пример:

```text
Location: Almaty
English: B2
```

должно быть доступно отдельно от prose Biography.

---

# 17. CH-005 — Video Before Deep Biography

Video Intro является более сильным профессиональным evidence для большинства кастинговых сценариев, чем длинная Biography.

---

# 18. CH-006 — Emotional Grid Before Individual Emotional Photos

Быстрый evaluation:

```text
Emotional Grid
```

Детальный:

```text
Full Emotional Portfolio
```

---

# 19. CH-007 — Featured Projects Before Complete Archive

Default:

```text
Selected / Featured Projects
 ↓
View All
```

---

# 20. CH-008 — Role Must Stay Attached to Project

Не показывать роль как изолированный факт без parent Project context.

---

# 21. CH-009 — Questionnaire Is High-Priority Professional Utility

Questionnaire не является footer utility.

Она относится к core professional hierarchy.

---

# 22. CH-010 — Contact Is an Outcome Layer

Contact/CTA должно быть доступно рано, но не должно визуально затмевать professional evidence.

---

# 23. Home Content Hierarchy

Базовый порядок:

```text
1. Hero / Identity
2. Quick Facts
3. Primary Portfolio
4. Video Intro
5. Emotional Range
6. Selected Projects
7. Skills / Languages
8. Training / Achievements
9. Questionnaire
10. Contact / Casting CTA
```

---

# 24. Home Is Not Linear Requirement

Порядок является baseline hierarchy.

UI MAY:

- комбинировать;
- использовать cards;
- вставлять contextual CTA;

но priority сохраняется.

---

# 25. Hero Content

Hero SHOULD содержать:

```text
Actor Name
Professional Identity
Primary Visual
Core Quick Facts
Primary Actions
```

---

# 26. Hero Must Not Become Biography

Long Biography does not belong inside primary Hero.

---

# 27. Hero CTA Hierarchy

Recommended:

```text
[▶ Video Intro]
[Actor Questionnaire]
[Contact]
```

или contextual equivalent.

---

# 28. Hero Quick Facts

Suitable examples:

- city/base;
- languages;
- selected key skill(s);
- key professional parameter(s).

Не весь Profile.

---

# 29. Hero Anti-Overload

Не помещать одновременно:

- all Skills;
- all social icons;
- full Biography;
- 8 CTAs;
- awards;
- archive navigation.

---

# 30. Portfolio Hierarchy

Recommended:

```text
Primary Close-Up
Primary Full Body
 ↓
Professional Category Filters
 ↓
Additional Portfolio
 ↓
Questionnaire / Video / CTA
```

---

# 31. Portfolio Default

Основные professional images должны быть видимы первыми.

Не сортировать default только:

```text
newest upload
```

если это разрушает professional hierarchy.

---

# 32. Portfolio Categories

Local grouping can include:

```text
Close-Up
Full Body
Waist
Profile
Three-Quarter
Staged
Other approved categories
```

---

# 33. Portfolio Metadata Priority

Primary:

- image;
- professional category.

Secondary:

- Shooting Date where available/useful;
- session/context.

Tertiary:

- technical metadata.

---

# 34. Technical Media Metadata

EXIF, checksum, original dimensions belong Admin advanced layer.

Не Public P0/P1.

---

# 35. Emotional Portfolio Hierarchy

```text
Shooting Date
 ↓
Primary Emotional Grid
 ↓
Additional Grid(s)
 ↓
Full Emotional Portfolio
 ↓
Questionnaire / CTA
```

---

# 36. Emotional Date Placement

Shooting Date SHOULD быть рядом с Emotional representation, а не спрятана в technical details.

---

# 37. Emotional Individual Photos

Не должны автоматически занимать основную Questionnaire hierarchy.

Composite first.

---

# 38. Video Hierarchy

Recommended:

```text
Video Intro
 ↓
Showreel
 ↓
Role / Project Videos
 ↓
Other Professional Video
```

---

# 39. Video Intro

P0/P1 depending page.

Должно быть легко различимо.

---

# 40. Showreel

P1 professional evidence.

Не смешивать с casual/social Video.

---

# 41. Role Video

P1/P2 depending Project context.

Always attached to:

```text
Project
Role
```

---

# 42. Projects Hierarchy

```text
Featured / Selected
 ↓
Project Detail
 ↓
Role
 ↓
Role Media / Performances
 ↓
View All Projects
 ↓
Archive
```

---

# 43. Project Card Priority

Recommended:

1. strong image where available;
2. title;
3. project type;
4. role;
5. concise relevant date;
6. short description if useful.

---

# 44. Project Card Must Not Become Full CV

Long descriptions belong detail page.

---

# 45. Project Detail Hierarchy

```text
Project Identity
 ↓
Role
 ↓
Strong Media
 ↓
Professional Description
 ↓
Performances / Additional facts
 ↓
Related CTA
```

---

# 46. Role Detail Hierarchy

```text
Role Name
Parent Project
 ↓
Role-specific visual evidence
 ↓
Role description/context
 ↓
Related video/photos
```

---

# 47. Skills Hierarchy

Public display SHOULD prioritize:

```text
Most relevant / Key Skills
 ↓
Grouped additional Skills
```

where professional curation exists.

---

# 48. Skill Level

If level exists, it must remain visible and consistent.

No visual embellishment should overstate proficiency.

---

# 49. Languages Hierarchy

Languages often P0/P1.

Display:

```text
Language
Level / CEFR
```

without requiring separate detail page.

---

# 50. International Journey Priority

For international visitor:

```text
Name
Location
Languages
Professional Photos
Video
Skills
Questionnaire
```

receive elevated priority.

---

# 51. Language Level Before Biography Translation

A visitor should not need to read full Biography just to determine English level.

---

# 52. Training Hierarchy

Training is normally P2.

Display concise list first:

```text
Institution
Course
Dates
```

Optional description deeper.

---

# 53. Achievements Hierarchy

P2 by default.

Key professional achievement MAY be surfaced higher if genuinely material.

---

# 54. Achievements Must Not Overwhelm Acting Evidence

Do not make certificate grid dominate:

- Portfolio;
- Video;
- Projects.

---

# 55. Biography Hierarchy

Biography SHOULD be:

```text
short professional summary
 ↓
full version / expanded context
```

rather than giant first-page prose.

---

# 56. Biography Is Narrative Complement

Biography does not replace structured:

- Languages;
- Skills;
- Location;
- Projects.

---

# 57. Contacts Hierarchy

Preferred:

```text
Professional Inquiry CTA
 ↓
Approved primary Contact
 ↓
Other approved Contact methods
 ↓
Social links
```

depending visibility.

---

# 58. Direct Contact vs Feedback

If direct phone/WhatsApp public:

can coexist with structured Feedback.

Neither should expose hidden Contact.

---

# 59. Social Links Priority

Social links are secondary to professional Contact and Questionnaire.

---

# 60. Questionnaire Hub Hierarchy

Recommended:

```text
Recommended Questionnaire
 ↓
Download
 ↓
Alternative Prepared Versions
 ↓
Customize This Version
 ↓
Build Under Casting
 ↓
Professional CTA
```

---

# 61. Prepared Questionnaire First

Official curated version receives visual priority over Builder.

---

# 62. Alternative Versions

Should explain professional use:

```text
Compact — quick screening
Extended — full details
```

---

# 63. Builder CTA

Important but secondary:

```text
Настроить эту версию под себя
```

---

# 64. Questionnaire Content Hierarchy

A professional Questionnaire SHOULD generally follow:

```text
1. Identification
2. Primary Close-Up
3. Primary Full Body
4. Professional Parameters
5. Languages
6. Skills
7. Video
8. Emotional Grid
9. Selected Projects / Roles
10. Training / Achievements
11. Additional Media / Links
12. Contacts
```

Exact Template-specific hierarchy may vary.

---

# 65. Questionnaire Compact

Prioritize:

```text
Identity
Primary Photos
Parameters
Languages
Skills
Video
Contact
```

---

# 66. Questionnaire Standard

Add:

```text
Emotional Grid
Selected Projects
Training summary
```

---

# 67. Questionnaire Extended

May include broader eligible:

- projects;
- roles;
- achievements;
- audio;
- links.

Still structured and curated.

---

# 68. Questionnaire Casting

May elevate:

```text
Casting Context
Relevant Skills
Relevant Projects
Relevant Media
```

without altering professional facts.

---

# 69. PDF First Page

First page SHOULD contain enough for initial identification/evaluation.

Recommended:

```text
Name
Primary Photos
Key Facts
Core Professional Context
```

not cover art alone.

---

# 70. PDF Page Hierarchy

Avoid:

```text
page 1 decorative cover
page 2 biography
page 3 actual photos
```

if it slows professional decision.

---

# 71. QR Hierarchy

QR belongs adjacent to its meaningful link.

Do not create one detached page:

```text
QR Codes
```

unless specifically justified.

---

# 72. QR Visual Priority

QR should be scannable but not visually dominate professional content.

---

# 73. Hyperlink Priority

Human-readable label and clickable URL remain available.

QR = complementary channel.

---

# 74. Public Builder Hierarchy

Builder UI:

```text
Starting Point
 ↓
Required Content
 ↓
Optional Sections
 ↓
Individual Items
 ↓
Preview
 ↓
Generate
```

---

# 75. Builder Required First

Required sections SHOULD appear before optional details.

---

# 76. Builder Long Lists

Use:

- categories;
- search;
- selected-first grouping where helpful.

Avoid giant undifferentiated checklists.

---

# 77. Builder Selection Summary

Summary should prioritize:

```text
what is included
what remains required
current locale
document type
```

not technical session metadata.

---

# 78. Search Results Hierarchy

Search result SHOULD show:

```text
Matched value
Entity type
Relevant context
```

Example:

```text
English — B2
Language
```

---

# 79. Search Relevance

Structured exact professional match generally outranks unrelated Biography text occurrence.

Final search ranking defined later.

---

# 80. Search Privacy

Hidden/private results do not influence visible result count or content.

---

# 81. Mobile Content Hierarchy

Mobile must reduce simultaneous density, not remove professional priority.

---

# 82. Mobile Above-the-Fold Priority

Recommended:

```text
Identity
Primary Visual
Core Facts
Primary CTA
```

---

# 83. Mobile Does Not Mean Biography First

Vertical layout should not place long text before Video/Questionnaire merely because it is easier technically.

---

# 84. Mobile Sticky Actions

Useful for preserving access to:

```text
Video
Questionnaire
Contact
```

while scrolling deeper content.

---

# 85. Mobile Gallery

Fewer items per row.

Larger touch targets.

Primary items remain first.

---

# 86. Mobile Project Cards

Keep concise.

Avoid multi-paragraph descriptions in list.

---

# 87. Mobile Questionnaire

Priority blocks remain same.

Layout may become sequential.

---

# 88. Desktop Content Hierarchy

Desktop can expose parallel information:

```text
photo + quick facts + CTA
```

without forcing vertical sequence.

---

# 89. Desktop Must Not Become Dashboard Grid

Professional public page still needs clear visual narrative.

---

# 90. Tablet

May combine:

- visual;
- quick facts;
- structured evidence.

Responsive rules later define exact layout.

---

# 91. Persona-Specific Hierarchy

Same Source Data can be prioritized differently by context.

---

# 92. Casting Director

Priority:

```text
Identity
Quick Facts
Primary Photos
Languages / Skills
Video
Emotional Grid
Questionnaire
Casting CTA
```

---

# 93. Casting Assistant

Priority:

```text
Structured Facts
Search
Primary Photos
Portfolio Categories
Languages / Skills
Compact Questionnaire
PDF
```

---

# 94. Commercial Producer

Priority:

```text
Current Visual
Close-Up / Full Body
Shooting Date
Emotional Grid
Video
Commercial/Casting CTA
```

---

# 95. Director / Producer

Priority:

```text
Video
Emotional Range
Selected Projects
Roles
Role Media
Request Materials
```

---

# 96. International Casting

Priority:

```text
Locale
Name
Location
Languages
Primary Photos
Skills
Video
Localized Questionnaire/PDF
Contact
```

---

# 97. Persona Priority Does Not Change Facts

Only presentation/order changes.

---

# 98. Context-Specific Ordering

Example:

Project deep link:

```text
Project Identity
Role
Media
Actor Context
Questionnaire / CTA
```

may reasonably prioritize Project over generic Profile details.

---

# 99. Deep Link Context

Every deep link must provide enough actor identity without reproducing entire homepage.

---

# 100. No Dead End Content Hierarchy

Every major evidence block SHOULD offer an appropriate continuation.

---

# 101. Evidence → Action Pairing

Examples:

```text
Video → Questionnaire / Request Materials
Portfolio → Video / Questionnaire
Project → Request Materials
Emotional → Questionnaire
```

---

# 102. CTA Placement

CTA may appear:

- after P0 qualification;
- after key evidence;
- at page end.

This repetition is intentional presentation, not data duplication.

---

# 103. Avoid CTA Saturation

Every section does not need multiple large buttons.

---

# 104. Content Density

Density should reflect user task.

Public:

```text
low-to-medium density
```

Admin:

```text
medium-to-high density
```

where operationally useful.

---

# 105. Public Content Density

Prefer:

- strong hierarchy;
- whitespace;
- scannability.

---

# 106. Admin Content Density

Can expose more:

- state;
- metadata;
- actions;
- relationships;

but should still prioritize task.

---

# 107. Admin Dashboard Hierarchy

Highest priority:

```text
Critical Action
Deadline
New Professional Inquiry
```

Then:

```text
Pending Review
Content Health
Automated Resolutions
```

---

# 108. Dashboard Does Not Lead With Vanity Analytics

Page views and social metrics should not occupy top operational hierarchy above urgent Casting.

---

# 109. Admin Entity Header Hierarchy

Recommended:

```text
Entity Identity
State / Readiness
Primary Actions
Key Context
```

---

# 110. Admin Detail Secondary Layer

Then:

- tabs;
- relationships;
- metadata;
- audit/help.

---

# 111. Form Hierarchy

Within form:

```text
Required professional data
 ↓
Important optional data
 ↓
Visibility / publication
 ↓
Advanced technical options
```

---

# 112. Visibility May Be Elevated

For sensitive Contacts, visibility belongs high in form because consequence is significant.

Priority is consequence-aware.

---

# 113. AI Hierarchy

AI assistance SHOULD be secondary to authoritative Source.

---

# 114. Casting AI Screen

Hierarchy:

```text
Original Source
 ↓
Extracted Requirements
 ↓
Profile Match
 ↓
Recommendation
 ↓
Human Decision
```

---

# 115. BB Assistant Screen

Hierarchy:

```text
Target Task
Source Context
 ↓
AI Draft
 ↓
Human Edit
 ↓
Apply
```

---

# 116. Virtual Operator Card

Hierarchy:

```text
What happened
 ↓
Why it matters
 ↓
Affected entity
 ↓
Suggested action
 ↓
Resolve
```

---

# 117. Theme AI

Hierarchy:

```text
Current visual context
 ↓
Proposal
 ↓
Preview
 ↓
Accessibility
 ↓
Publish
```

---

# 118. AI Confidence

Confidence MAY appear as supporting metadata.

It MUST NOT outrank source evidence or human review.

---

# 119. Validation Hierarchy

On form:

```text
Blocking Errors
 ↓
Warnings
 ↓
Informational Notes
```

---

# 120. Error Hierarchy

Critical local blocker appears close to relevant action.

Do not bury under generic toast.

---

# 121. Readiness Hierarchy

Display:

```text
Blocked / Ready
 ↓
Exact reasons
 ↓
Resolution links
```

not just percentage.

---

# 122. Content Freshness

Freshness signals should appear where professionally useful.

Examples:

- Shooting Date;
- Generated Date;
- last reviewed where Admin.

---

# 123. Do Not Flood Public With Timestamps

Created/updated dates for every field are unnecessary.

---

# 124. Shooting Date Has Different Meaning

It is a professional visual freshness signal.

Should receive appropriate priority.

---

# 125. Generated Date

Important in PDF/Questionnaire snapshot context.

---

# 126. Updated At

Primarily Admin/operations.

---

# 127. Current vs Historical

Current active content appears before archive.

Historical content requires explicit exploration.

---

# 128. Historical Material

Must not compete visually with current Primary Portfolio.

---

# 129. Archive Entry Point

Use:

```text
View archive
Previous sessions
All projects
```

rather than mixing everything.

---

# 130. Content Relevance Over Chronology

Newest item is not always most professionally relevant.

Curated/primary status can outrank chronology.

---

# 131. Content Relevance Over Popularity

Most-viewed content is not automatically most important.

Analytics cannot silently reorder hierarchy.

---

# 132. Content Relevance Over AI Score

AI cannot autonomously promote/demote professional content based on inferred attractiveness/fit.

---

# 133. Manual Curation

Human can define:

- Primary photos;
- Featured Projects;
- Primary Questionnaire;
- Primary Emotional Grid.

These markers influence hierarchy.

---

# 134. Automatic Hierarchy

Safe automatic ordering MAY use explicit deterministic fields such as:

```text
display_order
primary
featured
published_at
```

according to module rules.

---

# 135. Fallback Ordering

When manual ordering absent:

deterministic fallback MUST be defined.

Avoid database-natural/random order.

---

# 136. Content Duplication

Same Source Fact may be shown multiple places.

Example:

```text
English B2
```

on:

- Profile;
- Quick View;
- Questionnaire;
- PDF.

This is projection reuse, not data duplication.

---

# 137. Copy Consistency

All projections use same canonical fact unless explicitly snapshot-based.

---

# 138. Narrative Duplication

Avoid copying same long Biography into:

- Home;
- About;
- Questionnaire;
- Project.

Use shorter projections/contextual summaries where needed.

---

# 139. Content Truncation

List/card MAY truncate description visually.

Full Source remains accessible.

---

# 140. Truncation Must Not Alter Meaning

Do not truncate structured:

```text
English B2
```

to ambiguous:

```text
English...
```

---

# 141. “Read More”

Appropriate for long narrative.

Not needed for every short field.

---

# 142. Collapsible Sections

Useful for:

- long P2/P3 detail;
- archive;
- technical Admin metadata.

Avoid hiding P0 content.

---

# 143. Accordion Anti-Pattern

Do not put:

```text
Video
Primary Photos
Contact
```

all inside closed accordions by default.

---

# 144. Tabs

Useful when related views share one entity context.

Should not hide critical data unnecessarily.

---

# 145. Public Tabs

Use cautiously because hidden content may reduce scannability.

Portfolio filters are more appropriate than unrelated tab overload.

---

# 146. Admin Tabs

Appropriate for:

```text
Project
Roles
Media
Performances
```

because entity context remains clear.

---

# 147. Cards

Cards useful for:

- Projects;
- questionnaire versions;
- Builder Templates;
- Admin alerts.

---

# 148. Card Content Hierarchy

Each card SHOULD include only information needed to choose/open it.

---

# 149. Table

Admin operational data may use tables.

Public core professional presentation should generally not look like database tables unless structured facts benefit.

---

# 150. Quick Facts Table/List

Compact structured grid may be appropriate.

---

# 151. Iconography

Icons supplement hierarchy.

Do not replace critical professional labels with ambiguous icon-only representations.

---

# 152. Typography Hierarchy

Exact design later, but semantic levels MUST exist:

```text
Page Title
Section Heading
Card Title
Metadata
Body
Caption
```

---

# 153. Heading Structure

Must be semantic, not only visual.

One logical page title.

Nested sections use correct hierarchy.

---

# 154. SEO Heading and UX Heading

Can align but SEO must not distort natural user hierarchy.

---

# 155. Visual Emphasis

Should align to professional importance.

A decorative quote should not visually outweigh:

```text
Actor Name
```

or:

```text
Questionnaire CTA
```

---

# 156. Photography Priority

Actor imagery is primary professional evidence.

UI decoration SHOULD frame, not compete with, photography.

---

# 157. Backgrounds

Themes may alter background treatment but cannot reduce visual legibility of actor photography/context.

---

# 158. Decorative Elements

P3 visual hierarchy.

They MUST NOT:

- cover faces;
- confuse CTA;
- compete with professional facts.

---

# 159. Content Hierarchy and Accessibility

Visual hierarchy must map to semantic hierarchy.

---

# 160. Screen Reader Order

DOM order SHOULD reflect meaningful information priority.

---

# 161. Visual Reordering

Responsive CSS MUST NOT make screen-reader order nonsensical.

---

# 162. Skip Links

Public/Admin SHOULD support skip to main content according to accessibility design.

---

# 163. Landmark Hierarchy

Use semantic:

- header;
- nav;
- main;
- complementary;
- footer.

---

# 164. Heading Labels

Avoid generic repeated headings:

```text
Details
Details
Details
```

when professional-specific labels are available.

---

# 165. Mobile Accessibility Hierarchy

Sticky actions should come after or alongside semantic main structure without focus confusion.

---

# 166. Content Hierarchy and Localization

Localized labels may differ in length.

Layout must accommodate this without changing priority.

---

# 167. Localized Content Length

English/Russian copies may differ significantly.

Hierarchy should rely on structure, not fixed line count.

---

# 168. Missing Localization

Do not promote untranslated content merely to fill space.

---

# 169. Proper Names

Official names may remain original-language according to localization policy.

This does not lower their hierarchy.

---

# 170. Content Hierarchy and Search Engines

SEO should make major professional sections independently discoverable.

---

# 171. Search Landing Hierarchy

Deep SEO landing SHOULD first provide:

```text
Page-specific content
+
Actor Identity
+
professional continuation
```

not force generic Home intro first.

---

# 172. Project SEO Landing

Project content first, while retaining actor context.

---

# 173. Questionnaire Shared Link

Questionnaire identity and useful document controls first.

Not generic homepage carousel.

---

# 174. QR Landing

Exact destination content should be immediately available.

Do not redirect every QR to Home unless QR intentionally targets Profile.

---

# 175. Content Hierarchy and Conversion

Hierarchy should progressively increase confidence:

```text
Recognition
→ Qualification
→ Evidence
→ Package
→ Action
```

---

# 176. CTA Before Evidence

A CTA may be available early.

But page should not look like lead-generation form before user can evaluate actress.

---

# 177. CTA After Evidence

Important conversion reinforcement.

---

# 178. Questionnaire as Conversion Bridge

Questionnaire bridges:

```text
Evidence
→ Professional Package
→ Action
```

---

# 179. Public Builder as Secondary Bridge

Used when generic package insufficient.

---

# 180. Content Hierarchy and No Dead Ends

Every P1/P2 detail SHOULD have navigation to:

- parent;
- related evidence;
- professional action.

---

# 181. Admin Hierarchy and No Dead Ends

Every exception/recommendation SHOULD link to affected entity.

---

# 182. Privacy Hierarchy

Private content is not lower priority public content.

It is absent.

---

# 183. Hidden ≠ Collapsed

A private Contact must not exist behind:

```text
Show more
```

for unauthorized visitor.

---

# 184. Draft Content

Draft is absent from ordinary public hierarchy.

---

# 185. Published but Restricted

Only appears in authorized projection/context.

---

# 186. Builder-Only Content

May appear only inside eligible Public Builder projection if independently allowed.

---

# 187. Content Hierarchy and Performance

P0 assets load first.

---

# 188. P0 Loading Priority

Examples:

```text
Primary image
Name
Quick Facts
Primary CTA
```

---

# 189. P1 Lazy Loading

Additional Portfolio/Projects MAY lazy load.

---

# 190. P3 Lazy Loading

Archive/content supplements SHOULD not block First Useful Content.

---

# 191. Video Loading

Video player shell/thumbnail can load before heavy media stream.

---

# 192. Original Images

Do not load full originals for thumbnails.

---

# 193. Content Hierarchy and Failure

If P1 item fails:

P0 remains.

---

# 194. Failed Project Image

Project title/role/CTA may remain.

---

# 195. Failed Video

Portfolio/Questionnaire remain.

---

# 196. Missing Optional Content

Section may be omitted instead of rendering empty visual gap.

---

# 197. Missing Required Content

Admin readiness should catch it before professional publication where possible.

---

# 198. Empty Public Profile Section

Do not render decorative heading with no content.

---

# 199. Content Hierarchy and Analytics

Analytics may identify friction.

It MUST NOT autonomously reorder professional hierarchy.

---

# 200. Allowed Analytics Use

Example:

```text
Users frequently open Languages before Projects.
```

This MAY create product recommendation.

---

# 201. Human Review

Any significant hierarchy change based on analytics requires deliberate UX/product decision.

---

# 202. Virtual Operator

Can detect hierarchy-affecting blockers:

- Primary photo missing;
- Video broken;
- Primary Questionnaire unavailable;
- localization gap.

---

# 203. Operator Does Not Reorder Public Page Autonomously

Normative.

---

# 204. Content Hierarchy and Themes

Theme can change:

- typography;
- spacing;
- decoration;
- visual treatment.

Cannot hide/reorder essential P0 content beyond allowed layout configuration.

---

# 205. Theme Guardrails

Must preserve:

```text
Identity
Primary navigation
Primary CTAs
Accessibility
```

---

# 206. Temporary Theme

Same hierarchy rules.

---

# 207. Home Hierarchy Reference Model

```text
┌────────────────────────────────────────────┐
│ HERO                                       │
│ Name                                       │
│ Actress                                    │
│ Primary photo                              │
│ Location · Languages · Key Facts           │
│ [Video] [Questionnaire] [Contact]          │
└────────────────────────────────────────────┘
                    ↓
             QUICK FACTS
                    ↓
               PORTFOLIO
        Close-Up + Full Body
                    ↓
                  VIDEO
                    ↓
            EMOTIONAL RANGE
                    ↓
          SELECTED PROJECTS
                    ↓
          SKILLS / LANGUAGES
                    ↓
       TRAINING / ACHIEVEMENTS
                    ↓
             QUESTIONNAIRE
                    ↓
           PROFESSIONAL CTA
```

---

# 208. Casting Director Reference

```text
IDENTITY
 ↓
QUICK VIEW
 ↓
STRUCTURED FIT
 ↓
VIDEO / EMOTIONAL
 ↓
QUESTIONNAIRE
 ↓
CASTING ACTION
```

---

# 209. Casting Assistant Reference

```text
IDENTITY
 ↓
STRUCTURED FACTS
 ↓
SEARCH / FILTERS
 ↓
PRIMARY PORTFOLIO
 ↓
COMPACT PACKAGE
```

---

# 210. Director Reference

```text
VIDEO
 ↓
EMOTIONAL RANGE
 ↓
PROJECTS
 ↓
ROLES
 ↓
ROLE MEDIA
 ↓
REQUEST MATERIALS
```

---

# 211. International Reference

```text
LOCALE
 ↓
IDENTITY
 ↓
LOCATION
 ↓
LANGUAGES
 ↓
PRIMARY VISUALS
 ↓
SKILLS
 ↓
LOCALIZED PACKAGE
```

---

# 212. Public Content Priority Table

| Content | Default Priority |
|---|---:|
| Name | P0 |
| Professional Identity | P0 |
| Primary Close-Up | P0 |
| Primary Full Body | P0 |
| Location | P0 |
| Languages | P0/P1 |
| Video Intro | P0/P1 |
| Questionnaire | P0 |
| Contact CTA | P0 |
| Emotional Grid | P1 |
| Additional Portfolio | P1 |
| Showreel | P1 |
| Selected Projects | P1 |
| Roles | P1 |
| Skills | P1 |
| Training | P2 |
| Achievements | P2 |
| Full Project archive | P2/P3 |
| Blog | P3 |
| Technical metadata | Admin-only |

---

# 213. Admin Content Priority Table

| Content | Default Priority |
|---|---:|
| Critical exception | P0 |
| Casting deadline | P0 |
| New professional inquiry | P0 |
| Readiness blocker | P0 |
| Pending human review | P1 |
| Content health warning | P1 |
| Domain inventory | P2 |
| Analytics trend | P2 |
| Audit details | P2/P3 |
| Raw technical metadata | P3 / diagnostic |

---

# 214. CH-011 — P0 Must Be Professionally Current

Old/archived primary material must not remain P0 merely because technically available.

---

# 215. CH-012 — P0 Must Be Small Enough to Scan

P0 cannot contain 30 fields.

It is a curated minimal professional set.

---

# 216. CH-013 — P1 Must Provide Evidence

P1 content should answer:

```text
Почему этим данным можно доверять?
Как актриса выглядит/работает в кадре?
```

---

# 217. CH-014 — P2 Provides Context, Not Noise

Training/Achievements/extended material increase confidence but should not dilute evidence.

---

# 218. CH-015 — P3 Must Be Discoverable but Non-Blocking

Archive is available to users who need depth.

---

# 219. CH-016 — Persona Context May Promote Priority

Example:

Training may move P2 → P1 for a specific relevant international context.

Source data remains unchanged.

---

# 220. CH-017 — Primary Markers Override Generic Sort

Primary Close-Up should remain primary even if newer additional photo exists.

---

# 221. CH-018 — Featured Markers Are Presentation Metadata

`Featured Project` does not create different Project Source.

---

# 222. CH-019 — Current Content Before Archive

Normative across:

- Portfolio;
- Emotional sessions;
- Projects;
- Questionnaires.

---

# 223. CH-020 — Prepared Before Custom

Prepared Questionnaire before Public Builder.

---

# 224. CH-021 — Fact Before AI Recommendation

Admin screens always prioritize authoritative/current fact over AI suggestion.

---

# 225. CH-022 — Source Before Extraction

Casting UI:

```text
Source
→ Extraction
```

not reverse.

---

# 226. CH-023 — Observation Before Recommendation

Virtual Operator:

```text
What happened
→ What system recommends
```

---

# 227. CH-024 — Business Outcome Before Vanity Metrics

Dashboard/analytics:

```text
Professional Inquiry
Casting
Offer
Booked
```

higher than page views.

---

# 228. CH-025 — Error Before Optimization

Critical broken Video outranks recommendation to change Project ordering.

---

# 229. CH-026 — Deadline Before Low-Severity Content Task

Casting deadline outranks missing optional caption.

---

# 230. CH-027 — Privacy Overrides Hierarchy

No priority rule can make private data public.

---

# 231. CH-028 — Accessibility Overrides Decorative Hierarchy

Visual design cannot destroy semantic reading order.

---

# 232. CH-029 — Mobile Priority Must Be Explicit

Do not rely on CSS shrink alone.

---

# 233. CH-030 — Contextual CTA Must Follow Evidence Naturally

Avoid forcing aggressive CTA before sufficient professional information.

---

# 234. Content Hierarchy Anti-Pattern CH-AP-001

**Biography First**

Long prose before identity/photos/actions.

---

# 235. CH-AP-002

**Archive Wall**

All projects/photos shown equally.

---

# 236. CH-AP-003

**Certificate Dominance**

Training/Achievements visually outweigh Portfolio/Video.

---

# 237. CH-AP-004

**Social First**

Instagram/TikTok links dominate professional CTA.

---

# 238. CH-AP-005

**Every Fact in Hero**

Information overload.

---

# 239. CH-AP-006

**Latest Upload = Primary**

Chronology overrides curation.

---

# 240. CH-AP-007

**Most Viewed = Featured Automatically**

Analytics controls professional presentation.

---

# 241. CH-AP-008

**AI-Ranked Portfolio**

AI autonomously orders actor photos by inferred attractiveness.

---

# 242. CH-AP-009

**Individual Emotional Photos in Questionnaire**

Composite hierarchy lost.

---

# 243. CH-AP-010

**Video Hidden in Media Library**

---

# 244. CH-AP-011

**Questionnaire in Footer Only**

---

# 245. CH-AP-012

**Builder Before Prepared Version**

User forced to configure unnecessarily.

---

# 246. CH-AP-013

**Decorative Cover Before Professional PDF Content**

---

# 247. CH-AP-014

**Private Content as Locked Card**

Unauthorized user sees existence/name.

---

# 248. CH-AP-015

**Technical Metadata in Public P0**

---

# 249. CH-AP-016

**Same Weight Everywhere**

Name, Primary Photo, Blog Post and optional certificate have identical visual emphasis.

---

# 250. CH-AP-017

**Mobile Reversal**

P0 content moved below long P2 sections due to simplistic responsive layout.

---

# 251. CH-AP-018

**Mixed Source Copies**

Different values shown across Profile and Questionnaire because hierarchy used duplicated data.

---

# 252. CH-AP-019

**CTA Flood**

Every section has three primary buttons.

---

# 253. CH-AP-020

**Readiness Percentage Above Actual Blockers**

---

# 254. Content Hierarchy Quality Gate

Перед реализацией любой major Public Page должны быть определены:

- [ ] Page purpose
- [ ] Primary Persona
- [ ] P0 content
- [ ] P1 evidence
- [ ] P2 context
- [ ] P3/archive
- [ ] Primary CTA
- [ ] Secondary CTA
- [ ] mobile priority
- [ ] direct deep-link hierarchy
- [ ] localization impact
- [ ] empty-state behaviour
- [ ] loading priority
- [ ] privacy projection
- [ ] accessibility reading order
- [ ] related evidence paths
- [ ] continuation/no-dead-end path

---

# 255. Admin Hierarchy Quality Gate

For Admin screen:

- [ ] critical state/action first
- [ ] entity identity visible
- [ ] lifecycle state visible
- [ ] readiness visible
- [ ] source facts before AI
- [ ] primary actions identifiable
- [ ] secondary metadata separated
- [ ] relationships accessible
- [ ] audit/technical detail secondary
- [ ] mobile urgent hierarchy defined

---

# 256. Screen Priority Annotation

Будущие UX specs MAY annotate content blocks:

```text
priority: P0
priority: P1
priority: P2
priority: P3
```

as documentation metadata.

This does not need to become runtime property unless architecture requires it.

---

# 257. Component-Level Priority

Within card:

```text
Project Title — primary
Role — primary/secondary
Year — secondary
Description — tertiary
```

should be explicit in design specification.

---

# 258. Content Hierarchy E2E — CH-E2E-001

Open Home on mobile.

Verify without deep navigation:

- Name;
- primary visual;
- core facts;
- Video;
- Questionnaire;
- Contact path.

---

# 259. CH-E2E-002 — Portfolio

Verify Primary Close-Up and Primary Full Body available before archive exploration.

---

# 260. CH-E2E-003 — Emotional

Primary Grid appears before full emotional photo collection.

---

# 261. CH-E2E-004 — Projects

Featured Projects before full list.

---

# 262. CH-E2E-005 — Questionnaire

Recommended Questionnaire before Builder.

---

# 263. CH-E2E-006 — PDF

First page includes professional identification/primary content.

---

# 264. CH-E2E-007 — International

EN journey surfaces Location/Languages without Biography dependency.

---

# 265. CH-E2E-008 — Director

Project/Role deep flow preserves Video/Emotional/evidence hierarchy.

---

# 266. CH-E2E-009 — Mobile

P0 remains above P2 after responsive transformation.

---

# 267. CH-E2E-010 — Privacy

Private content absent rather than merely moved down hierarchy.

---

# 268. CH-E2E-011 — AI

AI recommendation never visually replaces authoritative value.

---

# 269. CH-E2E-012 — Admin Dashboard

Critical Casting/Inquiry outranks low-priority content-health suggestion.

---

# 270. CH-E2E-013 — Broken P1

Broken Video does not hide P0 identity/Questionnaire/Contact.

---

# 271. CH-E2E-014 — Archive

Archived Project absent from default Featured/Active hierarchy but still accessible according to archive policy.

---

# 272. CH-E2E-015 — Data Reuse

Same Language Level appears consistently in Profile, Questionnaire and PDF.

---

# 273. Traceability

```text
PERSONA
 ↓
CJM
 ↓
CONTENT PRIORITY
 ↓
UX SCREEN
 ↓
UF
 ↓
FR
 ↓
AC
 ↓
E2E
```

---

# 274. Home P0 Acceptance

Home is unacceptable if initial professional view prominently contains:

- decorative animation;
- Blog;
- social promotions;

while Video/Questionnaire/Quick Facts are difficult to find.

---

# 275. Portfolio P0 Acceptance

Portfolio is unacceptable if user must manually identify which images are:

- Full Body;
- Close-Up;

from an unclassified gallery.

---

# 276. Emotional Acceptance

Emotional representation is unacceptable if Questionnaire shows many individual emotional photos instead of configured composite hierarchy.

---

# 277. Questionnaire Acceptance

Questionnaire Hub is unacceptable if Public Builder appears as only/default path when Prepared Questionnaire exists.

---

# 278. Admin Acceptance

Admin Dashboard is unacceptable if a critical professional inquiry can be visually buried under analytics/cards without action priority.

---

# 279. Content Priority Change Governance

Significant changes in canonical P0/P1 hierarchy SHOULD require:

```text
UX/Product decision
+
document update
+
acceptance regression review
```

not isolated frontend redesign.

---

# 280. Content Hierarchy and Documentation

Module docs MUST not contradict this hierarchy without explicitly documenting context-specific reason.

---

# 281. Content Hierarchy and Domain Ownership

Hierarchy never changes entity ownership.

Example:

Skills shown in Quick View are still Skills domain data.

---

# 282. Content Hierarchy and Projections

Preferred architecture:

```text
Master Data
 ↓
Contextual Projection
 ↓
Presentation Hierarchy
```

---

# 283. Content Hierarchy and Snapshots

Historical Questionnaire uses hierarchy fixed in its Revision/configuration.

It does not reorder because current website hierarchy changes.

---

# 284. Content Hierarchy and Public Builder

Visitor's valid selection can customize document composition within allowed structural/template constraints.

It does not modify global Public hierarchy.

---

# 285. Content Hierarchy and Conversion Funnel

Mapping:

```text
P0 → Recognition / Qualification
P1 → Evidence
P2 → Deep Evaluation
Questionnaire → Package
CTA → Professional Action
```

---

# 286. Content Hierarchy and Five-Minute Journey

Recommended time allocation:

```text
0:00–0:20
Identity / Recognition

0:20–1:00
Qualification

1:00–3:00
Evidence

3:00–4:00
Questionnaire / Package

4:00–5:00
Professional Action
```

Not a hard timer, but UX target.

---

# 287. Definition of Content Hierarchy Compliance

Публичное или административное представление соответствует настоящему документу, если:

1. профессионально важный контент визуально и логически предшествует второстепенному;
2. Identity всегда понятна;
3. structured facts не спрятаны в prose;
4. Primary Close-Up и Full Body имеют высокий приоритет;
5. Video Intro легко доступно;
6. Emotional Grid предшествует полному emotional archive;
7. Featured Projects предшествуют полному Project archive;
8. Questionnaire является core professional utility;
9. Prepared Questionnaire имеет приоритет над Builder;
10. CTA доступны, но не заменяют evidence;
11. mobile сохраняет P0/P1;
12. International hierarchy поднимает Location/Languages;
13. Admin prioritizes exceptions/deadlines over inventory;
14. AI recommendation не визуально выше Source Fact;
15. historical/archive content не конкурирует с current;
16. privacy исключает данные вместо «понижения» их hierarchy;
17. analytics не меняет hierarchy автоматически;
18. themes не могут скрыть P0;
19. accessibility reading order соответствует meaningful hierarchy;
20. разные projections используют единый Source of Truth.

---

# 288. Итоговая модель

```text
                         CONTENT HIERARCHY

                              P0
                    IMMEDIATE DECISION
                              │
         Identity · Primary Photos · Quick Facts
         Video · Questionnaire · Professional CTA
                              │
                              ↓
                              P1
                     PROFESSIONAL EVIDENCE
                              │
      Portfolio · Emotional Grid · Showreel · Projects
               Roles · Skills · Languages
                              │
                              ↓
                              P2
                    PROFESSIONAL CONTEXT
                              │
       Training · Achievements · Extended Projects
           Detailed Biography · Additional Media
                              │
                              ↓
                              P3
                    EXTENDED / HISTORICAL
                              │
             Archive · Blog · Old Sessions
                              │
                              ↓
                    PROFESSIONAL ACTION
```

---

# 289. Финальный принцип

> **Иерархия контента должна отражать реальный порядок профессионального решения: сначала понять, кто актриса и подходит ли она базовым критериям; затем увидеть доказательства её внешности, эмоционального диапазона и работы в кадре; после этого при необходимости изучить опыт и профессиональный контекст; и только затем углубляться в архив. Ни объём данных, ни дата загрузки, ни популярность, ни AI-рекомендации не должны автоматически менять этот порядок.**