# PUBLIC USER FLOWS

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативные пользовательские сценарии публичной части продукта

**Целевой файл:** `docs/ux/public-user-flows.md`  
**Документ:** DOC-043  
**Статус:** ✅ Completed  
**Тип:** UX / Public User Flows

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
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/ux-principles.md`
- `docs/ux/public-navigation.md`

---

# 1. Назначение документа

Настоящий документ определяет полный нормативный набор Public User Flows.

Он переводит:

```text
Persona
+
Customer Journey
+
Public Navigation
+
Product Principles
```

в конкретные пользовательские последовательности:

```text
Entry
→ User Action
→ System Response
→ Decision
→ Next Action
→ Success / Error
```

Документ является основанием для:

- UX screen specifications;
- route design;
- frontend behaviour;
- Functional Requirements;
- Acceptance Criteria;
- Analytics events;
- E2E tests;
- accessibility testing.

---

# 2. Что такое User Flow

`User Flow` — это конкретная последовательность действий пользователя и ответов системы для достижения одной определённой цели.

Пример:

```text
UF-PUB-QST-001
Open Questionnaire
→ Select Prepared Version
→ Preview
→ Download PDF
```

User Flow отличается от Customer Journey:

```text
Customer Journey
=
широкий профессиональный сценарий Persona

User Flow
=
конкретная интерфейсная задача
```

---

# 3. Public User Flow ID Convention

Используется формат:

```text
UF-PUB-{DOMAIN}-{NNN}
```

Например:

```text
UF-PUB-PRO-001
UF-PUB-POR-001
UF-PUB-QST-001
UF-PUB-PQB-001
UF-PUB-FDB-001
```

---

# 4. Доменные префиксы

```text
PRO — Profile
NAV — Navigation
SRC — Search
POR — Portfolio
EMO — Emotional Portfolio
VID — Video
PRJ — Projects / Roles
TRN — Training
SKL — Skills / Languages
QST — Prepared Questionnaires
PQB — Public Questionnaire Builder
PDF — PDF
QR  — QR navigation
CON — Contacts
FDB — Feedback / Professional Inquiry
I18N — Localization
MOB — Mobile
ERR — Error / Recovery
```

---

# 5. Общий Public Flow Contract

Каждый Public Flow должен определить:

1. Flow ID.
2. Persona relevance.
3. Entry Point.
4. Preconditions.
5. User Goal.
6. Main Path.
7. Alternative Path.
8. Empty State.
9. Error State.
10. Privacy boundary.
11. Mobile behaviour.
12. Accessibility requirements.
13. Analytics.
14. Success condition.

---

# 6. Public Flow Principles

Все flows MUST соблюдать:

```text
No Mandatory Registration
No Dead Ends
Privacy by Projection
Server-Side Authorization
Progressive Disclosure
Context Preservation
Mobile Critical Path
Accessibility
Graceful Failure
```

---

# 7. UF-PUB-PRO-001 — Open Public Profile

## Goal

Понять, кто актриса, и перейти к профессиональной оценке.

## Entry

- `/`;
- direct profile URL;
- search engine;
- recommendation;
- social link.

## Main Flow

```text
Open Profile
 ↓
Primary professional content loads
 ↓
User sees:
- Actor Name
- Primary Photo
- Professional Identity
- Key Facts
- Video CTA
- Questionnaire CTA
- Professional Contact CTA
 ↓
User chooses next action
```

## Success

Пользователь понимает:

```text
Who?
What?
What next?
```

---

# 8. UF-PUB-PRO-002 — Review Quick Facts

## Goal

Быстро проверить structured professional facts.

## Flow

```text
Profile
 ↓
Quick Facts
 ↓
Location
Languages
Key Skills
Professional Parameters
 ↓
Optional detailed section
```

## Rule

Biography MUST NOT быть единственным способом узнать эти данные.

---

# 9. UF-PUB-PRO-003 — Open Full Professional Profile Details

## Goal

Получить более подробную информацию после quick qualification.

## Flow

```text
Quick Facts
 ↓
View More / Profile Details
 ↓
Biography
Skills
Languages
Training
Achievements
```

---

# 10. UF-PUB-PRO-004 — Open Public Profile From Deep Link

## Entry

Например:

```text
/projects/{slug}
```

## System response

Страница должна показать:

- Actor Identity;
- current section;
- global navigation;
- Questionnaire;
- relevant CTA.

## Success

Пользователь может продолжить Journey без возврата на Home.

---

# 11. UF-PUB-NAV-001 — Use Global Navigation

## Flow

```text
Any public page
 ↓
Open primary navigation
 ↓
Select destination
 ↓
Navigate
```

Destinations:

- Profile;
- Portfolio;
- Emotional Range;
- Projects;
- Questionnaire;
- Contacts.

---

# 12. UF-PUB-NAV-002 — Mobile Navigation

## Flow

```text
Tap menu trigger
 ↓
Drawer opens
 ↓
Focus moves into menu
 ↓
Select destination
 ↓
Drawer closes
 ↓
Destination loads
```

## Accessibility

- semantic button;
- expanded state;
- Escape support where appropriate;
- focus return.

---

# 13. UF-PUB-NAV-003 — Return to Parent Context

Example:

```text
Project Detail
 ↓
← All Projects
```

This SHOULD be semantic parent navigation, not only `history.back()`.

---

# 14. UF-PUB-NAV-004 — Preserve Filter State

Example:

```text
Portfolio
Filter: Full Body
 ↓
Open image
 ↓
Close / Back
 ↓
Portfolio still filtered: Full Body
```

---

# 15. UF-PUB-NAV-005 — Browser Back/Forward

System SHOULD preserve expected page/filter context where technically reasonable.

---

# 16. UF-PUB-SRC-001 — Open Professional Search

## Goal

Найти конкретный профессиональный факт.

## Entry

Profile/Search control.

## Flow

```text
Open Search
 ↓
Enter query
 ↓
Search published structured data
 ↓
Show matching results
```

---

# 17. UF-PUB-SRC-002 — Search Skill

Example:

```text
Query: "driving"
 ↓
Result:
Driving
Skill
 ↓
Open Skills section
```

---

# 18. UF-PUB-SRC-003 — Search Language

```text
Query: "English"
 ↓
English — B2
Languages
```

---

# 19. UF-PUB-SRC-004 — Search Project / Role

```text
Query
 ↓
Matching Project / Role
 ↓
Open semantic source page
```

---

# 20. UF-PUB-SRC-005 — Zero Results

Correct output:

```text
По опубликованным данным совпадений нет.
```

Not:

```text
Актриса не владеет этим навыком.
```

---

# 21. UF-PUB-SRC-006 — Search Privacy

Search MUST NOT return:

- Draft content;
- Private Contact;
- Admin notes;
- Casting;
- Feedback;
- AI Drafts.

---

# 22. UF-PUB-POR-001 — Open Portfolio

## Flow

```text
Profile / Navigation
 ↓
Portfolio
 ↓
Primary Close-Up
Primary Full Body
Additional Portfolio
```

---

# 23. UF-PUB-POR-002 — Filter Portfolio by Category

## Flow

```text
Portfolio
 ↓
Select category
 ↓
Results update
```

Categories may include:

```text
Full Body
Close-Up
Waist
Profile
Three-Quarter
Staged
Other approved types
```

---

# 24. UF-PUB-POR-003 — Open Primary Full Body

This must require no deep filter navigation.

```text
Profile / Portfolio
→ Primary Full Body
```

---

# 25. UF-PUB-POR-004 — Open Primary Close-Up

Same requirement.

---

# 26. UF-PUB-POR-005 — Open Portfolio Image

## Flow

```text
Portfolio grid
 ↓
Tap/click image
 ↓
Detail/lightbox
 ↓
Previous / Next / Close
```

---

# 27. UF-PUB-POR-006 — Return From Image Detail

System preserves:

- category;
- scroll;
- current page where feasible.

---

# 28. UF-PUB-POR-007 — View Shooting Date

If available:

```text
Portfolio shooting
→ Shooting Date
```

displayed as professional freshness context.

---

# 29. UF-PUB-POR-008 — Portfolio to Questionnaire

```text
Portfolio
 ↓
Questionnaire CTA
 ↓
Questionnaire Hub
```

---

# 30. UF-PUB-POR-009 — Portfolio to Casting CTA

```text
Portfolio
 ↓
Invite to Casting
 ↓
Professional Inquiry
```

---

# 31. UF-PUB-EMO-001 — Open Emotional Range

## Flow

```text
Navigation / Profile
 ↓
Emotional Range
 ↓
Primary Emotional Grid
```

---

# 32. UF-PUB-EMO-002 — Open Emotional Grid

User sees:

- composite;
- shooting date;
- contextual label;
- full Emotional Portfolio action.

---

# 33. UF-PUB-EMO-003 — Open Full Emotional Portfolio

```text
Emotional Grid
 ↓
View Full Portfolio
 ↓
Individual emotional photos
```

---

# 34. UF-PUB-EMO-004 — Switch Emotional Session

If multiple sessions are published:

```text
Current Session
 ↓
Other Session
```

Current/Primary SHOULD be presented first.

---

# 35. UF-PUB-EMO-005 — Emotional to Video

```text
Emotional Range
 ↓
Video Intro
```

---

# 36. UF-PUB-EMO-006 — Emotional to Questionnaire

```text
Emotional Range
 ↓
Questionnaire
```

---

# 37. UF-PUB-EMO-007 — Emotional Direct Deep Link

Direct session URL MUST retain:

- Actor Identity;
- navigation;
- CTA;
- Questionnaire.

---

# 38. UF-PUB-VID-001 — Start Video Intro

## Flow

```text
Profile / CTA
 ↓
Video Intro
 ↓
User presses Play
 ↓
Playback starts
```

---

# 39. UF-PUB-VID-002 — Start Showreel

```text
Video section / Project
 ↓
Showreel
 ↓
Play
```

Video Intro and Showreel MUST be clearly differentiated.

---

# 40. UF-PUB-VID-003 — Open Project / Role Video

```text
Project
 ↓
Role
 ↓
Scene Video
```

Context remains visible.

---

# 41. UF-PUB-VID-004 — External Video

If external URL:

```text
Video Action
 ↓
Embed or approved external navigation
```

with predictable behaviour.

---

# 42. UF-PUB-VID-005 — Video Failure

If Video fails:

```text
Show clear unavailable state
 ↓
Offer retry where appropriate
 ↓
Keep:
Portfolio
Questionnaire
Contact
```

---

# 43. UF-PUB-VID-006 — Video to Questionnaire

```text
Video
 ↓
Questionnaire CTA
```

---

# 44. UF-PUB-VID-007 — Video to Request Materials

For Director-like context:

```text
Video
 ↓
Request Materials
```

---

# 45. UF-PUB-PRJ-001 — Open Projects

## Flow

```text
Navigation
 ↓
Projects
 ↓
Featured Projects
 ↓
View All
```

---

# 46. UF-PUB-PRJ-002 — Open Featured Project

```text
Featured Project
 ↓
Project Detail
```

---

# 47. UF-PUB-PRJ-003 — View All Projects

```text
Projects
 ↓
View All
 ↓
Complete published project list
```

---

# 48. UF-PUB-PRJ-004 — Open Project Detail

User sees:

- title;
- type;
- role;
- media;
- description;
- dates where relevant.

---

# 49. UF-PUB-PRJ-005 — Open Role

```text
Project
 ↓
Role
 ↓
Role Detail / Context
```

---

# 50. UF-PUB-PRJ-006 — Open Role-Specific Photos

```text
Role
 ↓
Role Media
 ↓
Photo
```

---

# 51. UF-PUB-PRJ-007 — Open Role-Specific Video

Same contextual relation.

---

# 52. UF-PUB-PRJ-008 — Project to Request Materials

```text
Project
 ↓
Request Additional Materials
 ↓
Professional Inquiry
```

---

# 53. UF-PUB-PRJ-009 — Project to Questionnaire

```text
Project
 ↓
Questionnaire
```

---

# 54. UF-PUB-PRJ-010 — Project Deep-Link Entry

Direct Project URL MUST preserve full professional continuation.

---

# 55. UF-PUB-TRN-001 — Open Training

## Flow

```text
Profile / Secondary navigation
 ↓
Training
 ↓
Published training records
```

---

# 56. UF-PUB-TRN-002 — Open Training Detail

Optional where detailed page exists.

---

# 57. UF-PUB-SKL-001 — Review Skills

```text
Profile
 ↓
Skills
 ↓
Category / level
```

---

# 58. UF-PUB-SKL-002 — Review Languages

```text
Profile
 ↓
Languages
 ↓
Language + Level / CEFR
```

---

# 59. UF-PUB-SKL-003 — Structured Level

User sees consistent format, e.g.:

```text
English — B2
```

not contradictory free-text variants.

---

# 60. UF-PUB-QST-001 — Open Questionnaire Hub

## Entry

- global nav;
- Profile CTA;
- Portfolio CTA;
- direct URL.

## Flow

```text
Open Questionnaire
 ↓
Recommended Prepared Questionnaire
 ↓
Alternative versions
 ↓
Customize option
```

---

# 61. UF-PUB-QST-002 — Open Primary Prepared Questionnaire

```text
Questionnaire Hub
 ↓
Recommended Version
 ↓
HTML Questionnaire
```

---

# 62. UF-PUB-QST-003 — Open Alternative Prepared Version

Examples:

```text
Compact
Extended
Casting
Custom
```

only Published/available versions are shown.

---

# 63. UF-PUB-QST-004 — Preview Questionnaire HTML

User can inspect professional content before download.

---

# 64. UF-PUB-QST-005 — Select PDF Language

If multiple locales:

```text
Questionnaire
 ↓
Download
 ↓
Select language
```

if language not already established by active locale.

---

# 65. UF-PUB-QST-006 — Download Prepared PDF

```text
Prepared Questionnaire
 ↓
Download PDF
 ↓
Server validates published revision
 ↓
PDF returned
```

---

# 66. UF-PUB-QST-007 — Open Download Picker

If multiple versions are downloadable:

```text
Download
 ↓
Compact
Extended
Casting
```

with short explanation.

---

# 67. UF-PUB-QST-008 — Questionnaire to Public Builder

```text
Prepared Questionnaire
 ↓
Customize This Version
 ↓
Public Builder
```

Preferred path.

---

# 68. UF-PUB-QST-009 — Questionnaire Direct Deep Link

Direct Questionnaire URL MUST expose:

- actress identity;
- official profile;
- download;
- customize;
- professional CTA.

---

# 69. UF-PUB-QST-010 — Questionnaire to Professional Inquiry

```text
Questionnaire
 ↓
Invite to Casting / Contact
```

---

# 70. UF-PUB-QST-011 — Historical Questionnaire Context

If public historical snapshot exists:

UI SHOULD show Generated/Published Date and link to current Profile where appropriate.

---

# 71. UF-PUB-PQB-001 — Open Public Builder

## Preconditions

Builder enabled.

## Flow

```text
Questionnaire Hub / Customize
 ↓
Public Builder
```

---

# 72. UF-PUB-PQB-002 — Choose Template

Available examples:

```text
Quick
Standard
Extended
Casting
```

System returns only enabled templates.

---

# 73. UF-PUB-PQB-003 — Start From Prepared Questionnaire

```text
Prepared Questionnaire
 ↓
Customize
 ↓
Builder prefilled from permitted current configuration
```

---

# 74. UF-PUB-PQB-004 — Start From Template

```text
Builder
 ↓
Choose Template
 ↓
Useful default configuration loads
```

No empty document by default.

---

# 75. UF-PUB-PQB-005 — Review Included Blocks

User sees:

```text
Included
Excluded
Required
Optional
```

with understandable labels.

---

# 76. UF-PUB-PQB-006 — Enable Block

Example:

```text
Training OFF
→ user enables
→ allowed Training items become available
```

---

# 77. UF-PUB-PQB-007 — Disable Block

Selection updates without changing Source Profile.

---

# 78. UF-PUB-PQB-008 — Select Portfolio Item

Only items allowed by Public Builder projection are returned.

---

# 79. UF-PUB-PQB-009 — Select Project

Same server-side permission requirement.

---

# 80. UF-PUB-PQB-010 — Select Skill

```text
Skills
 ↓
select approved skill
```

---

# 81. UF-PUB-PQB-011 — Select Language

```text
Languages
 ↓
select allowed language record
```

---

# 82. UF-PUB-PQB-012 — Select Emotional Grid

Only approved/public-builder-enabled Grid.

---

# 83. UF-PUB-PQB-013 — Select Video / Audio / Link

User can include approved professional media references.

---

# 84. UF-PUB-PQB-014 — Select Contacts

Only Contacts explicitly enabled for Public Builder.

---

# 85. UF-PUB-PQB-015 — Enter Temporary Casting Context

Casting Template MAY request:

- Casting Name;
- Project Name;
- Role Name.

These values are session/document context only.

---

# 86. UF-PUB-PQB-016 — Reorder Blocks

If reordering is enabled:

```text
select / drag / move
```

must remain accessible without requiring drag-only interaction.

---

# 87. UF-PUB-PQB-017 — Return to Profile During Builder Session

```text
Builder
 ↓
Open public profile/material
 ↓
Builder session remains active
```

---

# 88. UF-PUB-PQB-018 — Add Item From Public Content

When active Builder Session exists and item is eligible:

```text
Project / Portfolio / Skill
 ↓
Add to Questionnaire
 ↓
Session updates
```

---

# 89. UF-PUB-PQB-019 — Persistent Builder Indicator

UI MAY display:

```text
Моя анкета · 8 элементов
[Продолжить]
```

---

# 90. UF-PUB-PQB-020 — Preview Builder Output

```text
Configuration
 ↓
Preview
 ↓
Server revalidates selection
 ↓
Preview renders
```

---

# 91. UF-PUB-PQB-021 — Generate Custom PDF

```text
Preview
 ↓
Generate PDF
 ↓
Server revalidates all items
 ↓
Generate share-ready PDF
```

---

# 92. UF-PUB-PQB-022 — Builder Permission Changed

Scenario:

```text
Item selected
 ↓
Admin revokes permission
 ↓
User generates PDF
 ↓
Server detects invalid selection
```

System MUST not include unauthorized item.

---

# 93. UF-PUB-PQB-023 — Builder Session Expired

```text
Open expired session
 ↓
Session expired message
 ↓
Start new Builder
```

No unauthorized restoration from client payload.

---

# 94. UF-PUB-PQB-024 — Builder Mobile Flow

Recommended mobile sequence:

```text
Template
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

# 95. UF-PUB-PQB-025 — Builder Without Registration

All standard flow steps accessible without account creation.

---

# 96. UF-PUB-PDF-001 — Generate Prepared PDF

Input:

Published Questionnaire Revision.

System:

- validates;
- renders;
- returns correct locale.

---

# 97. UF-PUB-PDF-002 — Generate Custom PDF

Input:

validated Public Builder Session snapshot.

---

# 98. UF-PUB-PDF-003 — Open Clickable Link in PDF

Professional link opens exact configured URL.

---

# 99. UF-PUB-PDF-004 — Use PDF Offline

Document remains understandable with:

- Actor Name;
- Generated Date;
- official Profile URL;
- human-readable media labels.

---

# 100. UF-PUB-PDF-005 — PDF Generation Failure

```text
Generate
 ↓
Failure
 ↓
Show retry
```

Where possible HTML Questionnaire remains usable.

---

# 101. UF-PUB-QR-001 — Scan Generic Profile QR

```text
Printed/PDF QR
 ↓
Scan
 ↓
Official public Profile
```

---

# 102. UF-PUB-QR-002 — Scan Video QR

```text
QR label: Video Intro
 ↓
Scan
 ↓
Exact approved video/public media URL
```

---

# 103. UF-PUB-QR-003 — QR Exact Target Validation

At generation time system must verify:

```text
Encoded URL
=
Configured canonical URL
```

---

# 104. UF-PUB-QR-004 — QR Private URL Protection

Admin/private URL MUST never be emitted into public Questionnaire/PDF.

---

# 105. UF-PUB-QR-005 — QR + Hyperlink Consistency

Where both are shown for the same item:

```text
Clickable link target
=
QR target
```

---

# 106. UF-PUB-I18N-001 — Switch Language on Profile

```text
Profile RU
 ↓
EN
 ↓
Same logical Profile EN
```

---

# 107. UF-PUB-I18N-002 — Switch Language on Project

```text
Project RU
 ↓
EN
 ↓
Same Project EN
```

---

# 108. UF-PUB-I18N-003 — Switch Language on Questionnaire

Same logical questionnaire context preserved.

---

# 109. UF-PUB-I18N-004 — Switch Language During Builder

Builder selection remains while display locale changes, where supported.

---

# 110. UF-PUB-I18N-005 — Generate PDF in Selected Locale

```text
Builder/Questionnaire EN
 ↓
Generate PDF
 ↓
PDF EN
```

---

# 111. UF-PUB-I18N-006 — Missing Translation

System uses documented fallback.

MUST NOT show raw translation keys.

---

# 112. UF-PUB-I18N-007 — Visibility Does Not Change by Locale

Private Contact remains private across languages.

---

# 113. UF-PUB-I18N-008 — Language Level Consistency

Example:

```text
RU: Английский — B2
EN: English — B2
```

same Source Fact.

---

# 114. UF-PUB-CON-001 — Open Contacts

```text
Global Navigation / CTA
 ↓
Contacts
```

---

# 115. UF-PUB-CON-002 — Use Direct Contact

If publicly allowed:

```text
Phone
WhatsApp
Telegram
Other approved channel
```

becomes actionable.

---

# 116. UF-PUB-CON-003 — Hidden Contact

Private Contact MUST be completely absent from Public payload/UI.

---

# 117. UF-PUB-CON-004 — Parent / Guardian Contact

Displayed only when explicitly permitted in current public context.

---

# 118. UF-PUB-FDB-001 — Start Casting Invitation

## Entry

Contextual CTA:

```text
Пригласить на кастинг
```

## Flow

```text
CTA
 ↓
Casting Invitation Form
```

---

# 119. UF-PUB-FDB-002 — Start Role Offer

```text
Предложить роль
 ↓
Role Offer form
```

---

# 120. UF-PUB-FDB-003 — Start Request Materials

```text
Запросить материалы
 ↓
Material Request form
```

---

# 121. UF-PUB-FDB-004 — Start Collaboration Inquiry

```text
Предложить сотрудничество
 ↓
Collaboration form
```

---

# 122. UF-PUB-FDB-005 — Generic Professional Question

```text
Задать вопрос
 ↓
Professional Question form
```

---

# 123. UF-PUB-FDB-006 — Form Context Prefill

System MAY automatically attach safe context:

- source page;
- Project ID;
- Questionnaire ID;
- Builder Session ID;
- attribution.

User should not manually re-enter these.

---

# 124. UF-PUB-FDB-007 — Enter Inquiry Details

Minimum relevant fields may include:

- Name;
- Organization;
- Contact;
- Project/Subject;
- Message.

Specific fields depend on Feedback type.

---

# 125. UF-PUB-FDB-008 — Add Attachment

If allowed:

```text
Select attachment
 ↓
Client preliminary validation
 ↓
Server validation
 ↓
Private safe storage
```

---

# 126. UF-PUB-FDB-009 — Validation Error

```text
Submit
 ↓
Validation error
 ↓
Invalid field highlighted
 ↓
All valid entered data preserved
```

---

# 127. UF-PUB-FDB-010 — Submit Professional Inquiry

Authoritative sequence:

```text
Validate
 ↓
Persist Feedback
 ↓
Return success
 ↓
Trigger notifications
```

---

# 128. UF-PUB-FDB-011 — Successful Submission

User sees:

```text
Обращение принято.
№...
```

and possible neutral next-step information.

---

# 129. UF-PUB-FDB-012 — Notification Failure

If Feedback persisted but WhatsApp notification fails:

public success remains valid.

No false claim that inquiry was lost.

---

# 130. UF-PUB-FDB-013 — Duplicate Submit Protection

Repeated tap/retry MUST not create duplicate inquiry where idempotency controls apply.

---

# 131. UF-PUB-FDB-014 — Return After Submission

User can return to:

- Profile;
- Questionnaire;
- source content.

---

# 132. UF-PUB-FDB-015 — No Automatic Casting Creation

Successful public inquiry creates Feedback.

Not automatically qualified Casting.

---

# 133. UF-PUB-MOB-001 — Mobile Home/Profile

Flow:

```text
Mobile Entry
 ↓
Primary Visual
 ↓
Quick Facts
 ↓
Video / Questionnaire / Contact
```

---

# 134. UF-PUB-MOB-002 — Mobile Portfolio

Must support:

- swipe/tap;
- filters;
- large visual;
- no hover dependence.

---

# 135. UF-PUB-MOB-003 — Mobile Emotional Grid

Grid must remain interpretable without horizontal overflow.

---

# 136. UF-PUB-MOB-004 — Mobile Video

Video usable inline/fullscreen with touch controls.

---

# 137. UF-PUB-MOB-005 — Mobile Questionnaire

Prepared Questionnaire and download actions remain accessible.

---

# 138. UF-PUB-MOB-006 — Mobile Builder

No desktop-only split-screen requirement.

---

# 139. UF-PUB-MOB-007 — Mobile Professional Form

Must support:

- touch;
- correct input types;
- keyboard;
- error recovery;
- attachments where allowed.

---

# 140. UF-PUB-MOB-008 — Mobile Sticky Actions

Where enabled:

```text
Видео
Анкета
Связаться
```

must not obscure content/forms.

---

# 141. UF-PUB-MOB-009 — Mobile Language Switch

Locale control remains accessible without opening multiple nested menus where avoidable.

---

# 142. UF-PUB-MOB-010 — Mobile Deep Link

Direct Project/Questionnaire/Emotional URLs remain fully usable.

---

# 143. UF-PUB-ERR-001 — Public 404

## Flow

```text
Unknown/unavailable URL
 ↓
Safe 404
 ↓
Profile
Portfolio
Questionnaire
Contact
```

No dead end.

---

# 144. UF-PUB-ERR-002 — Hidden Resource

Private/non-public resource MUST not leak internal details.

Behaviour follows Security policy.

---

# 145. UF-PUB-ERR-003 — Network Failure

Recoverable action SHOULD offer retry where meaningful.

---

# 146. UF-PUB-ERR-004 — Image Failure

Broken secondary image does not prevent page navigation.

---

# 147. UF-PUB-ERR-005 — Video Failure

Local failure only.

---

# 148. UF-PUB-ERR-006 — PDF Service Failure

HTML content/other actions remain accessible where technically possible.

---

# 149. UF-PUB-ERR-007 — Analytics Failure

Completely non-blocking.

---

# 150. UF-PUB-ERR-008 — AI Failure

Public core flow should generally not depend on runtime AI.

---

# 151. UF-PUB-ERR-009 — Builder Item Becomes Invalid

User receives actionable message:

```text
Один из выбранных материалов больше недоступен.
Проверьте состав анкеты.
```

No private details leaked.

---

# 152. UF-PUB-ERR-010 — Session Expiry

Provide clear restart path.

---

# 153. Persona Flow — Casting Director

Core composition:

```text
UF-PUB-PRO-001
→ UF-PUB-PRO-002
→ UF-PUB-POR-003/004
→ UF-PUB-VID-001
→ UF-PUB-EMO-002
→ UF-PUB-PRJ-002
→ UF-PUB-QST-002
→ UF-PUB-PQB-003
→ UF-PUB-PDF-002
→ UF-PUB-FDB-001
```

---

# 154. Persona Flow — Casting Assistant

```text
UF-PUB-PRO-001
→ UF-PUB-SRC-002/003
→ UF-PUB-POR-002
→ UF-PUB-PRJ-004
→ UF-PUB-VID-001
→ UF-PUB-QST-003 Compact
→ UF-PUB-PDF-001
```

---

# 155. Persona Flow — Commercial Producer

```text
UF-PUB-MOB-001
→ UF-PUB-POR-003/004
→ UF-PUB-EMO-002
→ UF-PUB-VID-001
→ UF-PUB-FDB-001/002
```

---

# 156. Persona Flow — Director / Producer

```text
UF-PUB-VID-001
→ UF-PUB-VID-002
→ UF-PUB-EMO-003
→ UF-PUB-PRJ-004
→ UF-PUB-PRJ-005
→ UF-PUB-PRJ-006/007
→ UF-PUB-FDB-003
```

---

# 157. Persona Flow — International Casting

```text
UF-PUB-I18N-001
→ UF-PUB-SKL-002
→ UF-PUB-POR-003/004
→ UF-PUB-VID-001
→ UF-PUB-QST-002
→ UF-PUB-I18N-005
→ UF-PUB-FDB-001
```

---

# 158. Flow — Quick Professional Decision

Target:

```text
Profile
→ Quick Facts
→ Primary Photos
→ Video
→ CTA
```

Should be possible without entering archive content.

---

# 159. Flow — Evidence Deep Dive

```text
Profile
→ Video
→ Emotional
→ Project
→ Role
→ Media
```

---

# 160. Flow — Ready Questionnaire

```text
Any major page
→ Questionnaire
→ Recommended Version
→ Download
```

---

# 161. Flow — Custom Questionnaire

```text
Questionnaire
→ Customize
→ Template/Current Prepared Configuration
→ Select Content
→ Preview
→ Generate PDF
```

---

# 162. Flow — Offline to Online

```text
PDF / Printed Material
→ QR
→ Current Public Content
→ Professional Action
```

---

# 163. Flow — Social to Casting

```text
Social Link
→ Mobile Profile/Portfolio
→ Emotional
→ Video
→ Casting CTA
```

---

# 164. Flow — Shared Project to Professional Action

```text
Project Deep Link
→ Role
→ Role Media
→ Questionnaire / Request Materials
```

---

# 165. Flow — Shared Questionnaire to Current Profile

```text
Questionnaire URL / PDF
→ Official Profile Link / QR
→ Current Profile
→ Current Evidence
```

---

# 166. Flow — Search to Custom PDF

```text
Search Skill
→ Open Skill/Profile
→ Active Builder
→ Add Skill
→ Continue Builder
→ PDF
```

where active Builder Session exists and item eligible.

---

# 167. Public Builder Permission Flow

System source pipeline:

```text
Master Data
 ↓
Visibility / Builder Permission
 ↓
Public Builder Projection
 ↓
Session Selection
 ↓
Server Revalidation
 ↓
Output
```

---

# 168. Privacy Critical Flow

Private data MUST be removed before public delivery.

Not:

```text
Send all data
→ hide private with CSS
```

---

# 169. Public Flow Server Authority

Client may propose:

```text
selected_project_id
selected_contact_id
```

Server decides whether selection is valid.

---

# 170. Flow State Ownership

Public browser may own temporary UI state:

- current filter;
- Builder selection;
- active locale.

It does not own professional source data.

---

# 171. URL State

State SHOULD be placed in URL when:

- shareable;
- important for Back/Forward;
- semantic.

Examples MAY include:

- Portfolio category;
- Project slug;
- Questionnaire slug;
- locale.

---

# 172. Ephemeral State

Need not be shareable:

- open dropdown;
- hovered image;
- transient loading state.

---

# 173. Public Flow Accessibility Contract

Every Public Flow MUST support:

- keyboard;
- visible focus;
- semantic controls;
- screen reader labels;
- accessible validation;
- no color-only state;
- non-hover critical interaction.

---

# 174. Public Flow Mobile Contract

Critical flows MUST remain possible on mobile:

```text
Profile
Portfolio
Emotional
Video
Questionnaire
Builder
Professional Inquiry
```

---

# 175. Public Flow Performance Contract

Critical user flow MUST NOT require loading complete large archives before useful content.

---

# 176. Public Flow Failure Contract

Optional capability failure SHOULD fail locally.

---

# 177. Public Flow Analytics Contract

Analytics MAY record meaningful transitions.

Must not:

- block action;
- define business truth;
- expose private data.

---

# 178. Main Analytics Mapping

| Flow | Suggested event |
|---|---|
| Profile opened | `profile_viewed` |
| Search used | `professional_search_started` |
| Portfolio filter | `portfolio_filter_used` |
| Video started | `video_started` |
| Emotional Grid | `emotional_grid_viewed` |
| Project opened | `project_opened` |
| Questionnaire opened | `questionnaire_viewed` |
| PDF downloaded | `questionnaire_downloaded` |
| Builder opened | `public_builder_opened` |
| Template selected | `public_builder_template_selected` |
| Custom PDF generated | `public_builder_pdf_generated` |
| Inquiry started | `professional_inquiry_started` |
| Inquiry persisted | business-backed submission event |

Final naming defined in Analytics specification.

---

# 179. Flow Success vs Analytics Event

UI success MUST be based on business response.

Example:

```text
Feedback created
→ success
```

not:

```text
analytics event sent
→ success
```

---

# 180. Public User Flow State Matrix

| State | Expected UX |
|---|---|
| Loading | context preserved, useful skeleton where appropriate |
| Empty | clear professional-safe message |
| Ready | primary task available |
| Warning | action may continue if policy permits |
| Error | explain + recovery |
| Unauthorized | safe unavailable/access message |
| Expired | restart/recovery |
| Processing | status + non-blocking where possible |

---

# 181. Flow Blocking Principles

Blocking is justified only for:

- missing required input;
- authorization failure;
- security/privacy risk;
- impossible requested output;
- business-rule blocker.

---

# 182. Non-Blocking Examples

Should generally not block whole flow:

- analytics failure;
- optional social link failure;
- one unavailable Project image;
- WhatsApp notification failure after Feedback persistence.

---

# 183. User Input Preservation

Any form/configuration flow MUST preserve valid input after recoverable validation error.

---

# 184. Public Builder Recovery

If PDF generation fails:

Builder selection SHOULD remain available within active session.

---

# 185. Questionnaire Recovery

If one PDF format fails, HTML Questionnaire should remain accessible where possible.

---

# 186. Video Recovery

Retry MAY be offered, but user can continue to other professional evidence.

---

# 187. Search Recovery

Search service failure SHOULD not make Profile content inaccessible.

---

# 188. Deep-Link Recovery

If detail item unavailable, safe page SHOULD offer:

```text
Profile
Relevant parent section
Questionnaire
```

where appropriate.

---

# 189. Conversion-Ready Flow Requirements

Each major professional evidence page SHOULD expose at least one logical next conversion step:

- Questionnaire;
- Contact;
- Casting CTA;
- Request Materials.

---

# 190. No Forced Questionnaire Rule

Questionnaire is important but not required before all professional inquiries.

---

# 191. No Forced Contact Page Rule

Contextual CTA can open form directly.

---

# 192. No Forced Home Rule

Deep-linked visitor does not need to visit Home first.

---

# 193. No Forced Registration Rule

Public professional evaluation and basic Builder/PDF flow do not require user account.

---

# 194. No Forced AI Rule

No public professional flow requires AI interaction.

---

# 195. Professional Trust Flow

Public user should be able to determine:

```text
This is official profile
These are current/dated materials
This PDF has source/date
These links are actionable
```

---

# 196. Current vs Snapshot Flow

```text
PDF Snapshot
 ↓
Official Profile Link
 ↓
Current Data
```

This is intentional and SHOULD be supported.

---

# 197. QR Trust Flow

```text
Human-readable label
+
QR
+
Clickable URL
```

provides redundant professional access paths.

---

# 198. Public User Flow Anti-Pattern UF-AP-001

**Home Gate**

Every deep link redirects to Home before content.

---

# 199. UF-AP-002

**Portfolio Archive First**

User must inspect dozens of photos before primary Full Body.

---

# 200. UF-AP-003

**Biography Gate**

Structured facts only discoverable in narrative.

---

# 201. UF-AP-004

**Video Media Maze**

Video Intro hidden among all media.

---

# 202. UF-AP-005

**Builder Blank Canvas**

Public user must manually construct entire document structure.

---

# 203. UF-AP-006

**Builder Leaks Private Data**

Hidden items loaded and disabled client-side.

---

# 204. UF-AP-007

**PDF Without Current Source**

No official profile link/date.

---

# 205. UF-AP-008

**QR Only**

QR replaces clickable hyperlink.

---

# 206. UF-AP-009

**Contact Lost on Notification Failure**

Business inquiry not persisted independently.

---

# 207. UF-AP-010

**Locale Reset**

Switching language loses page/Builder state.

---

# 208. UF-AP-011

**Desktop-Only Builder**

Mobile cannot complete configuration.

---

# 209. UF-AP-012

**Generic CTA Everywhere**

Only `Contact` regardless of professional intent.

---

# 210. UF-AP-013

**Error Dead End**

Failure leaves no retry/alternative action.

---

# 211. UF-AP-014

**Search Hallucination**

Zero result presented as confirmed lack of skill.

---

# 212. UF-AP-015

**Historical Rewrite**

Old PDF silently changes when Profile changes.

---

# 213. Public Flow Quality Gate

Перед implementation каждого flow должны быть определены:

- [ ] Flow ID
- [ ] Persona
- [ ] Goal
- [ ] Entry
- [ ] Preconditions
- [ ] Main path
- [ ] Alternate path
- [ ] Success state
- [ ] Empty state
- [ ] Error state
- [ ] Mobile
- [ ] Accessibility
- [ ] Security/privacy
- [ ] Analytics
- [ ] Related route/screens
- [ ] E2E coverage

---

# 214. E2E-PUB-001 — Profile to Casting

```text
Open Profile
→ inspect Quick Facts
→ open Video
→ open Questionnaire
→ submit Casting Invitation
```

---

# 215. E2E-PUB-002 — Portfolio Screening

```text
Open Portfolio
→ Full Body filter
→ Close-Up filter
→ open image
→ return with state preserved
```

---

# 216. E2E-PUB-003 — Search

```text
Search public Skill
→ correct structured result
→ no private data
```

---

# 217. E2E-PUB-004 — Emotional Journey

```text
Open Emotional Grid
→ Full Emotional Portfolio
→ Questionnaire
```

---

# 218. E2E-PUB-005 — Project Deep Link

```text
Open direct Project URL
→ identify actress
→ open Role
→ open Role Media
→ Request Materials
```

---

# 219. E2E-PUB-006 — Prepared Questionnaire

```text
Open Questionnaire
→ select Prepared Version
→ download PDF
→ verify current official link
```

---

# 220. E2E-PUB-007 — Public Builder

```text
Open Builder
→ choose template
→ select items
→ preview
→ generate PDF
```

---

# 221. E2E-PUB-008 — Builder Privacy

Manipulated client request attempts unauthorized item.

Expected:

```text
server rejects/excludes
no data leak
```

---

# 222. E2E-PUB-009 — QR

```text
Generate PDF
→ decode QR
→ verify exact canonical URL
```

---

# 223. E2E-PUB-010 — Locale

```text
Profile RU
→ switch EN
→ Project
→ Questionnaire
→ Builder
→ PDF
```

Locale remains EN.

---

# 224. E2E-PUB-011 — Feedback Persistence

```text
Submit inquiry
→ Feedback saved
→ notification fails
→ user still receives success/reference
```

---

# 225. E2E-PUB-012 — Mobile Commercial Flow

```text
Mobile Profile
→ Portfolio
→ Emotional Grid
→ Video
→ Casting CTA
```

---

# 226. E2E-PUB-013 — No Registration

Verify core Public Builder/Questionnaire flow does not request account creation.

---

# 227. E2E-PUB-014 — Broken Video

Other critical professional capabilities remain available.

---

# 228. E2E-PUB-015 — Deep-Link Navigation

Direct:

- Project;
- Emotional;
- Questionnaire;

all retain professional navigation and CTA.

---

# 229. Traceability Model

```text
PERSONA
 ↓
CJM
 ↓
UF-PUB-*
 ↓
UX Screen
 ↓
FR-*
 ↓
AC-*
 ↓
E2E-*
```

---

# 230. Flow Priority Levels

## P0

```text
Profile
Primary Photos
Video Intro
Questionnaire
PDF
Contact / Professional Inquiry
```

## P1

```text
Search
Emotional Grid
Projects
Public Builder
Localization
```

## P2

```text
Training
Achievements
Extended Portfolio
Additional Media
```

---

# 231. P0 Flow Release Requirement

Public release is not considered professionally ready if any critical P0 flow is broken.

---

# 232. Public Builder Release Requirement

Because Public Builder is Core Target capability, target-state acceptance requires its complete flow, even if migration/deployment can phase rollout.

---

# 233. Flow Completion Metrics

Possible metrics:

```text
Time to Quick Facts
Time to Video
Time to Questionnaire
PDF Completion
Builder Completion
Professional Inquiry Completion
```

---

# 234. Flow Metrics Must Not Distort UX

Do not add unnecessary steps solely to increase measurable events.

---

# 235. Flow Context Preservation Matrix

| Context | Preserve |
|---|---|
| Locale | Yes |
| Builder Session | Yes until expiry |
| Portfolio Filter | Preferably |
| Search query | Preferably |
| Attribution | Internally |
| Private permissions | Server-derived, never client-owned |

---

# 236. Flow Security Matrix

| User action | Authority |
|---|---|
| Navigate public page | Public projection |
| Select Builder item | Server |
| Generate PDF | Server |
| Access Contact | Visibility rules |
| Submit Feedback | Server validation |
| Access private Casting | Never through normal public flow |
| Change Profile data | Not allowed |

---

# 237. Flow Data Ownership

Public visitor may create only temporary or inbound data:

```text
Builder Session Configuration
Feedback / Inquiry
Temporary Casting Labels in Builder
```

They do not modify:

```text
Profile
Portfolio
Projects
Skills
Languages
Contacts
Prepared Questionnaires
```

---

# 238. Public Session Boundaries

Builder Session SHOULD contain only data necessary to reproduce temporary questionnaire configuration.

No unnecessary browsing profile.

---

# 239. Flow Retention

Public Builder Session expires according to policy.

Feedback follows business retention policy.

Analytics follows privacy/analytics retention.

---

# 240. Final Public Flow Map

```text
                         PUBLIC ENTRY
                              │
      ┌───────────────┬───────┼────────┬─────────────┐
      │               │       │        │             │
    Home           Search   Social    QR        Deep Link
      │               │       │        │             │
      └───────────────┴───────┼────────┴─────────────┘
                              ↓
                       ACTOR CONTEXT
                              ↓
                   QUICK PROFESSIONAL FACTS
                              ↓
       ┌────────────┬─────────┼─────────┬────────────┐
       │            │         │         │            │
   Portfolio      Video    Emotional  Projects     Search
       │            │         │         │            │
       └────────────┴─────────┼─────────┴────────────┘
                              ↓
                      PROFESSIONAL EVIDENCE
                              ↓
                       QUESTIONNAIRE HUB
                              ↓
             ┌────────────────┴────────────────┐
             │                                 │
      PREPARED QUESTIONNAIRE              PUBLIC BUILDER
             │                                 │
       Open / Download                  Select / Preview
             │                                 │
             └────────────────┬────────────────┘
                              ↓
                     SHARE-READY PACKAGE
                              ↓
                PROFESSIONAL ACTION / CTA
                              ↓
        ┌────────────┬────────┼────────┬────────────┐
        │            │        │        │            │
     Casting      Role     Materials  Collab      Question
        │            │        │        │            │
        └────────────┴────────┼────────┴────────────┘
                              ↓
                           FEEDBACK
                              ↓
                     INTERNAL WORKFLOW
```

---

# 241. Definition of Public User Flow Compliance

Публичная часть соответствует настоящему документу, если:

1. основные профессиональные цели представлены отдельными понятными flows;
2. пользователь может начать с любой meaningful deep link;
3. Home не является обязательным gate;
4. structured facts доступны без чтения Biography;
5. Primary Full Body и Close-Up легко доступны;
6. Video Intro находится быстро;
7. Emotional Grid служит быстрым первым слоем Emotional Range;
8. Project/Role media сохраняют context;
9. Prepared Questionnaire остаётся preferred default;
10. Public Builder начинается с полезной configuration;
11. Public Builder не изменяет Source Profile;
12. все Builder selections revalidate server-side;
13. PDF содержит профессиональный source context;
14. QR и hyperlinks согласованы;
15. locale сохраняется по Journey;
16. mobile поддерживает все critical flows;
17. Professional Inquiry сохраняется до notifications;
18. failure одной optional capability не создаёт total dead end;
19. private data не попадают в client;
20. flows доступны без обязательной регистрации.

---

# 242. Финальный принцип

> **Публичные User Flows должны позволять профессиональному пользователю начать работу с любой релевантной точки — профиля, проекта, портфолио, анкеты, QR или социальной ссылки — и без потери контекста пройти к нужному доказательству, получить готовую или самостоятельно настроенную профессиональную анкету и выполнить следующее профессиональное действие. Система должна сокращать путь, но никогда не сокращать достоверность, privacy или человеческий контроль над профессиональными данными.**