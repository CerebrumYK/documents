# ADMIN NAVIGATION

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная модель административной навигации

**Целевой файл:** `docs/ux/admin-navigation.md`  
**Документ:** DOC-042  
**Статус:** ✅ Completed  
**Тип:** UX / Admin Navigation

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/virtual-operator.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/ux-principles.md`
- `docs/ux/public-navigation.md`

---

# 1. Назначение документа

Настоящий документ определяет целевую модель навигации административной части продукта.

Документ фиксирует:

1. глобальную структуру `/admin`;
2. назначение Dashboard;
3. domain navigation;
4. Content Management navigation;
5. Casting Workspace;
6. Feedback / Inbox;
7. Opportunity Pipeline;
8. Questionnaire management;
9. AI capabilities;
10. Virtual Operator;
11. Theme management;
12. Marketing / Analytics;
13. Help / Tickets;
14. Social Publishing;
15. System / Operations;
16. contextual navigation;
17. breadcrumbs;
18. object relations;
19. Draft/Published/Archive navigation;
20. responsive behaviour;
21. mobile admin priorities;
22. permission-aware navigation;
23. accessibility;
24. empty/error states;
25. navigation acceptance criteria.

---

# 2. Главная задача Admin Navigation

Admin Navigation должна отвечать на два разных вопроса:

```text
Что требует моего внимания сейчас?
```

и:

```text
Где я управляю конкретным типом профессиональных данных?
```

Поэтому целевая модель состоит из:

```text
ACTION-ORIENTED DASHBOARD
+
DOMAIN-ORIENTED WORKSPACES
```

---

# 3. Admin Navigation не должна быть только списком сущностей

Неправильная модель:

```text
Profile
Media
Portfolio
Projects
Skills
Contacts
Questionnaires
...
```

без операционного слоя.

Правильная модель:

```text
Dashboard
   ↓
Что требует действия?

+

Domain Workspaces
   ↓
Где управляется конкретная информация?
```

---

# 4. Главный mental model администратора

```text
SEE
 ↓
PRIORITIZE
 ↓
OPEN CONTEXT
 ↓
EDIT / REVIEW
 ↓
VALIDATE
 ↓
PUBLISH / RESOLVE
 ↓
VERIFY
```

---

# 5. Navigation IDs

Требования Admin Navigation используют prefix:

```text
NAV-ADM-*
```

---

# 6. Корневая административная структура

Целевая верхнеуровневая модель:

```text
ADMIN
│
├── Dashboard
│
├── Profile & Content
│
├── Media
│
├── Questionnaires
│
├── Castings
│
├── Inbox / Feedback
│
├── Opportunities
│
├── AI Workspace
│
├── Appearance / Themes
│
├── Marketing & Analytics
│
├── Publishing
│
├── Help / Support
└── System
```

Конкретные labels могут уточняться, но domain boundaries MUST сохраняться.

---

# 7. Recommended primary admin navigation

Предпочтительная модель sidebar:

```text
Dashboard

CONTENT
Profile
Portfolio
Emotional Portfolio
Projects
Training
Skills & Languages
Media & Links
Contacts
Achievements

QUESTIONNAIRES
Prepared Questionnaires
Public Builder

CASTING
Castings
Inbox
Opportunities

ASSISTANCE
Virtual Operator
BB Assistant
AI Casting Analysis

PRESENTATION
Themes

GROWTH
Analytics
Social Publishing

SYSTEM
Help / Tickets
Settings
System Status
Audit
```

Точный grouping определяется screen design, но такая логическая сегментация является целевой.

---

# 8. Dashboard — первый административный экран

Root route:

```text
/admin
```

SHOULD вести на Dashboard, а не на Profile form.

---

# 9. Dashboard Purpose

Dashboard отвечает:

```text
Что произошло?
Что критично?
Что требует решения?
Что приближается по сроку?
Что система сделала сама?
```

---

# 10. Dashboard не является отчётом inventory

Плохой Dashboard:

```text
Photos: 186
Projects: 24
Skills: 31
```

как основной content.

Хороший Dashboard:

```text
🔴 Casting deadline tomorrow
🔴 New role inquiry
🟠 Showreel unavailable
🟡 12 photos require classification
🟢 4 previews regenerated automatically
```

---

# 11. Dashboard Groups

Минимальные logical groups:

```text
Action Required
Deadlines
New Professional Inquiries
Pending Review
Content Health
System Health
Recently Resolved
```

---

# 12. Dashboard Cards Must Be Navigational

Каждая actionable card должна вести непосредственно к affected entity/action.

Пример:

```text
Showreel unavailable
→ Open media item
```

не:

```text
→ Media homepage
→ search manually
```

---

# 13. Dashboard → Domain Context

Navigation должна сохранять source context.

Пример:

```text
Dashboard Alert
→ Showreel
```

может отображать:

```text
Opened from:
Virtual Operator alert
```

где это полезно.

---

# 14. Dashboard Filters

MAY поддерживаться:

```text
All
Critical
Deadlines
Content
Casting
Technical
```

Но default — actionable priority.

---

# 15. Dashboard sorting

Recommended default:

```text
Priority
→ Deadline
→ Recency
```

а не только `created_at DESC`.

---

# 16. Main Content Group

`Profile & Content` объединяет professional source data.

В него входят:

```text
Profile
Portfolio
Emotional Portfolio
Projects
Training
Skills
Languages
Achievements
Professional Media / Links
Contacts
```

---

# 17. Profile

Route conceptual:

```text
/admin/profile
```

Profile является root workspace для:

- identity;
- biography;
- professional parameters;
- location;
- primary facts;
- multilingual text.

---

# 18. Profile must not absorb unrelated domains

Profile workspace не должен становиться огромной страницей, куда помещены:

- Projects;
- Castings;
- Questionnaires;
- Media Library;
- Contacts management.

Он агрегирует summary, но отдельные domains имеют собственные workspaces.

---

# 19. Profile Contextual Links

Profile MAY содержать summary + direct links:

```text
Primary Close-Up → Portfolio
Languages → Skills & Languages
Primary Questionnaire → Questionnaires
Public Profile Preview → Preview
```

---

# 20. Public Preview from Admin

Admin header/content workspace SHOULD предоставлять очевидный action:

```text
Preview public profile
```

---

# 21. Preview must open public representation

Preview не должен просто рендерить admin form.

Он должен использовать реальные public projection rules.

---

# 22. Portfolio workspace

Conceptual route:

```text
/admin/portfolio
```

Sub-navigation MAY include:

```text
All
Primary Photos
By Category
Draft
Published
Archived
```

---

# 23. Portfolio should not duplicate Media Library

Media Library manages assets.

Portfolio manages professional use/classification.

Navigation must make this distinction clear.

---

# 24. Portfolio → Media Asset relation

From Portfolio Item Admin SHOULD be able to open source Media Asset.

Example:

```text
Portfolio Item
→ Source Asset
```

---

# 25. Media Asset → Portfolio relation

Likewise Media workspace SHOULD show:

```text
Used in:
Portfolio
Questionnaire
Project
Role
```

with direct navigation.

---

# 26. Emotional Portfolio workspace

Conceptual:

```text
/admin/emotional
```

Subareas:

```text
Sessions
Emotional Grids
Grid Drafts
Published
```

---

# 27. Emotional Session as parent context

Navigation:

```text
Emotional Portfolio
→ Shooting Session
→ Source Photos
→ Emotional Grids
```

---

# 28. Emotional Grid editor

Grid editing deserves dedicated workspace/route, not modal-only flow.

Conceptually:

```text
/admin/emotional/{sessionId}/grids/{gridId}
```

---

# 29. Grid Editor context

Header SHOULD show:

```text
Emotional Portfolio
› Session — July 2026
› Grid 4×4
```

---

# 30. Grid Editor actions

Contextual actions:

```text
Preview
Validate
Save Draft
Finalize
Publish
```

according to actual state/business rules.

---

# 31. Projects workspace

Conceptual:

```text
/admin/projects
```

Top-level:

```text
Featured
All
Draft
Published
Archived
```

---

# 32. Project Detail

Navigation model:

```text
Projects
› Project
   ├── Overview
   ├── Roles
   ├── Media
   ├── Performances
   ├── Achievements
   └── Public Preview
```

---

# 33. Project tab structure

Tabs MAY be used where they reduce context switching.

They are preferable to nested navigation menus for closely related Project subareas.

---

# 34. Role Detail

Role should preserve parent context:

```text
Projects
› Project A
› Role: Character X
```

---

# 35. Role Media

Role-specific images/video SHOULD be reachable from Role Detail without going through global Media Library search.

---

# 36. Training workspace

Conceptual:

```text
/admin/training
```

Training MUST remain separate from Projects.

---

# 37. Training views

MAY include:

```text
All
Current
Completed
Archived
```

if corresponding domain fields exist.

---

# 38. Skills & Languages

Conceptual grouping:

```text
/admin/skills-languages
```

MAY have tabs:

```text
Skills
Languages
```

because both are structured professional competencies but remain separate entity types.

---

# 39. Skills navigation

Possible local filters:

```text
Category
Level
Published
Incomplete
```

---

# 40. Languages navigation

Should expose:

- Language;
- CEFR/Level;
- visibility;
- localization-relevant state.

---

# 41. Achievements

Can be independent workspace or secondary subsection under professional content.

Conceptual:

```text
/admin/achievements
```

---

# 42. Media Library

Conceptual:

```text
/admin/media
```

Media Library is asset-centric.

---

# 43. Media Library local navigation

Recommended:

```text
All
Images
Video
Audio
Documents
External
Unclassified
Recently Added
```

---

# 44. Media filters

MAY include:

- type;
- source;
- usage;
- date;
- processing state;
- duplicate status.

---

# 45. Media Details

Media Detail SHOULD show:

```text
Original
Derivatives
Metadata
Usage
Public/Private state
Processing status
```

---

# 46. Media usage navigation

Example:

```text
Used in:
• Portfolio — Primary Close-Up
• Questionnaire — Compact
• Project — Film X
```

Each relation should be clickable.

---

# 47. Professional Media / Links

External video/audio/link records may exist in a separate logical area or Media Library tab.

Must preserve typed distinction:

```text
Video
Audio
External Link
Document
```

---

# 48. Contacts workspace

Conceptual:

```text
/admin/contacts
```

---

# 49. Contacts navigation structure

Recommended:

```text
All Contacts
Professional Contacts
Parent / Guardian Contacts
Social
Visibility Review
```

depending on actual domain data.

---

# 50. Contact visibility must be prominent

Contact detail MUST clearly expose independent controls:

```text
Public Site
Prepared Questionnaires
Public Builder
```

---

# 51. Contacts must not be hidden inside Profile

Because privacy and reuse rules are complex, Contacts deserve independent workspace.

---

# 52. Questionnaires top-level group

Questionnaires are major product capability.

Recommended main navigation group:

```text
Questionnaires
├── Prepared Questionnaires
├── Templates
├── Public Builder Settings
└── PDF / Readiness
```

Some items may be merged depending UX complexity.

---

# 53. Prepared Questionnaires

Conceptual route:

```text
/admin/questionnaires
```

---

# 54. Questionnaire list views

Recommended filters:

```text
Primary
Published
Draft
Casting
Compact
Extended
Custom
Archived
```

---

# 55. Primary Questionnaire

Primary status should be visible in list, not discoverable only in edit form.

---

# 56. Questionnaire Detail

Suggested workspace:

```text
Questionnaire
› Overview
› Content
› Preview
› Revisions
› Distribution / Links
```

---

# 57. Questionnaire Content

Content editing should use existing domain objects by selection, not re-entry.

---

# 58. Questionnaire Preview

Admin SHOULD access:

```text
HTML Preview
PDF Preview
```

where supported.

---

# 59. Questionnaire Revisions

Historical revisions belong to current Questionnaire context.

They SHOULD NOT be mixed in list as independent current documents without clear label.

---

# 60. Revision detail

Breadcrumb:

```text
Questionnaires
› Casting Extended
› Revision 12
```

Historical state must be visibly read-only.

---

# 61. Public Builder Settings

Conceptual:

```text
/admin/questionnaires/public-builder
```

---

# 62. Builder settings areas

MAY include:

```text
General
Templates
Allowed Content
Limits
Preview
Analytics
```

---

# 63. Builder settings must not expose visitor sessions as main questionnaire list

Temporary Public Builder Sessions are operational/analytics data, not Prepared Questionnaires.

---

# 64. Builder templates

Template editing SHOULD be under Builder/Questionnaires, not Themes.

Because they define document content defaults, not visual website appearance.

---

# 65. Castings top-level workspace

Conceptual:

```text
/admin/castings
```

Castings MUST remain separate from Projects.

---

# 66. Casting list

Recommended local views:

```text
New
Active
Needs Review
Deadline Soon
Closed
```

---

# 67. Casting detail

Suggested structure:

```text
Casting
› Overview
› Source
› Requirements
› Profile Match
› AI Analysis
› Materials
› Questionnaire
› Opportunity
› History
```

---

# 68. Casting Source

Source tab/section preserves:

- original text;
- uploaded images/files;
- source metadata.

---

# 69. AI Analysis inside Casting

Casting AI MAY be contextual tab:

```text
AI Analysis
```

inside Casting Detail.

There may also be global AI workspace for cross-casting access.

---

# 70. Source → Extraction → Decision navigation

UI SHOULD preserve conceptual order:

```text
Source
→ Extraction
→ Profile Match
→ Recommendation
→ Human Decision
```

---

# 71. Casting Questionnaire

From Casting Detail SHOULD be possible to:

```text
Create Questionnaire Draft
Open existing related Questionnaire
```

without manual global search.

---

# 72. Casting Deadline

Deadline SHOULD be visible:

- list;
- detail;
- Dashboard.

---

# 73. Inbox / Feedback

Conceptual:

```text
/admin/inbox
```

or:

```text
/admin/feedback
```

UI label can be `Входящие`.

---

# 74. Inbox role

Inbox is professional communication intake.

It is not generic support-only inbox.

---

# 75. Inbox filters

Recommended:

```text
Unread
Casting Invitations
Role Offers
Material Requests
Collaboration
Questions
Other
Resolved
```

---

# 76. Feedback detail

Should show:

```text
Sender
Type
Message
Attachments
Source Page
Questionnaire Context
Attribution
Status
Responsible
Internal Notes
Next Action
```

where permitted.

---

# 77. Feedback → Casting

High-value contextual action:

```text
Create Casting
```

or:

```text
Link to Existing Casting
```

---

# 78. Feedback/Casting bidirectional navigation

After conversion:

Feedback SHOULD show:

```text
Linked Casting: ...
```

Casting SHOULD show:

```text
Source Inquiry: ...
```

---

# 79. Feedback must not disappear after Casting creation

Original communication remains part of history.

---

# 80. Opportunities workspace

Conceptual:

```text
/admin/opportunities
```

---

# 81. Opportunity views

Recommended:

```text
Pipeline
List
Deadlines / Next Actions
Closed
```

---

# 82. Pipeline visualization

Pipeline MAY use Kanban-like representation:

```text
New
Qualified
Materials Requested
Self-Tape Requested
Audition
Callback
Offer
Booked
```

with terminal states handled appropriately.

---

# 83. Pipeline navigation is business workflow

Opportunity movement MUST not be treated as purely visual drag operation without business confirmation/rules.

---

# 84. Drag-and-drop

MAY be used as interaction for stage change.

But must invoke authoritative stage transition logic.

---

# 85. Opportunity Detail

Should include:

```text
Current Stage
Casting
Source Inquiry
Next Action
Timeline
Materials
Notes
Outcome
```

---

# 86. Closed Opportunities

`Closed — Not Selected` and `Withdrawn` SHOULD remain accessible in Closed view.

---

# 87. AI Workspace

AI MUST NOT be represented as one undifferentiated top-level chatbot.

Recommended:

```text
AI Workspace
├── BB Assistant
├── Casting AI
├── Virtual Operator
└── Theme AI
```

Theme AI may primarily live inside Themes but remain conceptually distinct.

---

# 88. Virtual Operator

Conceptual route:

```text
/admin/operator
```

or accessible primarily via Dashboard.

---

# 89. Virtual Operator local views

Recommended:

```text
Action Required
Observations
Suggestions
Resolved
Automation History
```

---

# 90. Virtual Operator should not duplicate Dashboard entirely

Dashboard shows highest-priority actionable subset.

Operator workspace provides full operational detail/history.

---

# 91. Operator Observation detail

Should show:

```text
Observed Fact
Affected Entity
Reason
Affected Journey
Severity
Recommendation
History
```

---

# 92. Operator direct resolution

`Open affected entity` SHOULD be primary action.

---

# 93. BB Assistant

Conceptual route:

```text
/admin/bb-assistant
```

plus contextual invocation inside editors.

---

# 94. BB Assistant top-level workspace

Global workspace MAY support:

```text
New Draft
Recent Drafts
Applied
Discarded
```

---

# 95. BB Assistant contextual navigation

Examples:

```text
Profile Biography
→ Improve with BB Assistant

Project Description
→ Draft with BB Assistant

Casting
→ Draft Response
```

---

# 96. BB Assistant context must be visible

User should know:

```text
Context:
Project "..."
Task:
Project Description
Language:
EN
```

---

# 97. AI Draft detail

Navigation SHOULD show:

- source context;
- generated draft;
- revisions;
- status;
- Apply/Discard.

---

# 98. Casting AI global workspace

Global route MAY list analyses/castings requiring review.

But detailed analysis should remain tied to Casting context.

---

# 99. Theme management

Conceptual:

```text
/admin/themes
```

---

# 100. Theme workspace

Suggested sections:

```text
Current Theme
Draft Themes
Temporary Theme
Theme History
Accessibility
```

---

# 101. Theme Editor

Dedicated workspace preferable to modal.

Suggested flow:

```text
Theme
› Tokens
› Components
› Hero
› Preview
› Accessibility
› Publish
```

---

# 102. Theme AI

Inside Theme Editor:

```text
Generate Proposal
```

rather than generic AI homepage.

---

# 103. Theme Preview navigation

Preview MUST support:

```text
Desktop
Tablet
Mobile
```

---

# 104. Theme Publish

Publish action must be separated from Save Draft.

---

# 105. Analytics workspace

Conceptual:

```text
/admin/analytics
```

---

# 106. Analytics top-level views

Recommended:

```text
Overview
Conversion Funnel
Content
Questionnaires
Public Builder
Sources / Attribution
International
```

---

# 107. Analytics must not replace operational Dashboard

`3 conversions this week` ≠ `Showreel is broken`.

Analytics and operational health remain separate.

---

# 108. Analytics contextual navigation

From analytics insight MAY be possible:

```text
Open related Questionnaire Template
Open related Project
Open Builder Settings
```

without auto-changing anything.

---

# 109. Marketing / Attribution

If separate workspace justified:

```text
/admin/analytics/acquisition
```

or integrated Analytics tab.

Avoid additional top-level item unless needed.

---

# 110. Social Publishing

Existing capability must be preserved.

Conceptual:

```text
/admin/social
```

---

# 111. Social Publishing views

MAY include:

```text
Accounts
Draft Posts
Published
Failed
```

Scheduling if future scope/implemented later should be separated appropriately.

---

# 112. OAuth connection state

Account health SHOULD be visible:

```text
Instagram — Connected
TikTok — Token expired
```

---

# 113. Social failure → Operator

Expired/failed account MAY generate Virtual Operator exception.

---

# 114. Help / Tickets

Existing Help Center/Ticket functionality SHOULD remain accessible.

Conceptual:

```text
/admin/help
```

or:

```text
/admin/support
```

---

# 115. Help navigation

Suggested:

```text
Help Center
My Tickets
Create Ticket
```

depending existing functionality.

---

# 116. Contextual Help

Complex admin workspaces MAY provide direct help link without losing current state.

---

# 117. System group

Recommended system-level navigation:

```text
Settings
System Status
Audit
```

---

# 118. Settings

Conceptual:

```text
/admin/settings
```

---

# 119. Settings should not become miscellaneous dumping ground

Each option belongs in Settings only if:

- global;
- cross-domain;
- account/system configuration.

Domain-specific settings remain in their module.

---

# 120. Example — correct settings placement

```text
Default locale
System notification preferences
Global publishing rules
```

MAY belong Settings.

---

# 121. Example — wrong settings placement

```text
Portfolio Primary Full Body
```

does not belong global Settings.

---

# 122. System Status

Conceptual:

```text
/admin/system
```

MAY show:

```text
Database
Storage
PDF
AI
Notifications
Social integrations
Background processing
```

in user-actionable form.

---

# 123. System Status is not infrastructure console

Default view should avoid overwhelming raw technical metrics.

Detailed diagnostics can be secondary.

---

# 124. Audit

Conceptual:

```text
/admin/audit
```

---

# 125. Audit views

Recommended filters:

```text
Entity
Actor
Action
Date
AI Capability
Publication
Security-sensitive change
```

---

# 126. Audit is read-focused

Audit workspace should not look like ordinary editable list.

---

# 127. Global Admin Sidebar

Desktop preferred pattern:

```text
Persistent Sidebar
+
Top Context Header
+
Main Workspace
```

---

# 128. Why Sidebar

Admin has many domains.

Sidebar provides:

- stable orientation;
- predictable module access;
- enough space for grouping.

---

# 129. Sidebar groups

Visual grouping SHOULD distinguish:

```text
Content
Questionnaires
Casting
Assistance
Presentation
Growth
System
```

---

# 130. Sidebar should remain compact

Avoid every sub-route at first level.

For example:

```text
Projects
```

not:

```text
Projects
Featured Projects
All Projects
Draft Projects
Archived Projects
Roles
Performances
```

all in primary sidebar.

---

# 131. Local sub-navigation

Detailed module states belong:

- tabs;
- filters;
- secondary sidebar;
- page-level controls.

---

# 132. Collapsible groups

Sidebar groups MAY collapse, but current location must remain clear.

---

# 133. Collapsed sidebar

On medium viewport sidebar MAY collapse to icons.

Icons MUST have:

- tooltip;
- accessible label.

---

# 134. Admin Header

Top context header SHOULD include:

- current workspace/entity;
- breadcrumbs where needed;
- state;
- relevant primary actions.

---

# 135. Context header example

```text
Projects › Film X

Published
Last updated: ...

[Preview] [Save Draft] [Publish]
```

depending current state.

---

# 136. Context actions must not all be global

`Publish` belongs current entity context, not top-right of every page.

---

# 137. Global actions

MAY include:

```text
Search
Notifications
Account
```

---

# 138. Global Admin Search

Admin MAY support search across:

- Projects;
- Media;
- Contacts;
- Questionnaires;
- Castings.

---

# 139. Global search respects permissions

Search cannot reveal objects the Admin role cannot access.

---

# 140. Global Search result should show entity type

Example:

```text
Film X
Project

Film X — Main Role
Role

Film X Casting
Casting
```

to avoid ambiguity.

---

# 141. Command Palette

MAY be supported later for advanced users.

Not required for baseline navigation.

---

# 142. Breadcrumbs

Deep Admin contexts SHOULD use breadcrumbs.

---

# 143. Breadcrumb examples

```text
Projects
› Project A
› Role B
```

```text
Questionnaires
› Extended
› Revision 12
```

```text
Castings
› Brand X
› AI Analysis
```

---

# 144. Breadcrumb rules

Breadcrumbs:

- show hierarchy;
- remain clickable where destination valid;
- do not include every UI tab unnecessarily.

---

# 145. Back action

Prefer semantic parent navigation over generic browser-history `Back`.

---

# 146. Cross-domain relations

Admin often moves not only parent→child, but relation→relation.

Example:

```text
Feedback
→ Casting
→ Questionnaire
```

---

# 147. Relationship links

Whenever entities are explicitly related, navigation SHOULD expose relation.

---

# 148. Relationship example — Media

```text
Media Asset
Used in:
Portfolio
Project
Questionnaire
```

---

# 149. Relationship example — Casting

```text
Casting
Source Inquiry
Related Questionnaire
Opportunity
```

---

# 150. Relationship example — Opportunity

```text
Opportunity
Casting
Feedback
Project/Role when Booked
```

---

# 151. Do not rely only on breadcrumbs for cross-domain relations

Breadcrumbs represent hierarchy.

Relation links represent graph connections.

Both are needed.

---

# 152. Unsaved Changes Navigation Guard

If Admin has unsaved meaningful changes and navigates away:

system SHOULD either:

- autosave Draft safely;
- or warn.

---

# 153. Autosave

If enabled:

```text
Autosave Draft
≠
Publish
```

---

# 154. Navigation with autosave failure

If autosave fails, UI MUST clearly indicate risk before navigation that would lose work.

---

# 155. Draft state in lists

Lists SHOULD clearly indicate:

```text
Draft
Published
Archived
```

---

# 156. Publication state filters

Admin SHOULD be able to filter by lifecycle state in content-heavy domains.

---

# 157. Historical revision navigation

Historical revision views should show:

```text
Read-only
Published on ...
```

and obvious link:

```text
Back to current questionnaire
```

---

# 158. Archive navigation

Archived items SHOULD be accessible from explicit Archive view.

They should not pollute default active lists.

---

# 159. Delete navigation

Deleted/soft-deleted items, if recoverable, belong in dedicated recovery/trash model only if domain requires it.

Do not add a generic Trash top-level item without business need.

---

# 160. Admin Navigation by Task

The same system supports three main task modes:

```text
OPERATE
EDIT
ANALYZE
```

---

# 161. OPERATE mode

Main destinations:

```text
Dashboard
Inbox
Castings
Opportunities
Virtual Operator
```

---

# 162. EDIT mode

Main destinations:

```text
Profile
Portfolio
Emotional
Projects
Training
Skills/Languages
Contacts
Questionnaires
Themes
```

---

# 163. ANALYZE mode

Main destinations:

```text
Analytics
Audit
System Status
```

---

# 164. Navigation should support switching modes without losing context

Example:

```text
Questionnaire
→ Analytics for this questionnaire
→ Back to Questionnaire
```

where contextual link is available.

---

# 165. Admin Notifications

A global notification center MAY be accessible from top bar.

It is separate from Inbox.

---

# 166. Notification Center vs Inbox

## Notification Center

System/admin alerts.

## Inbox

External professional communications.

Do not merge blindly.

---

# 167. Notification examples

```text
PDF generation failed
Showreel link invalid
New casting inquiry
```

The first two are system/operator notifications.

The third may also lead to Inbox item.

---

# 168. Notification click

Should navigate directly to affected entity.

---

# 169. Mobile Admin Navigation

Admin mobile is secondary but important for urgent actions.

---

# 170. Mobile priorities

Critical mobile-accessible areas:

```text
Dashboard
Inbox
Castings
Opportunities
Virtual Operator alerts
Quick status
Contacts
```

---

# 171. Advanced mobile editing

Complex tasks such as:

- 4×4 Emotional Grid crop editing;
- dense Theme editing;
- large questionnaire structure management;

MAY be optimized primarily for Tablet/Desktop.

But mobile should:

- inspect;
- review;
- defer;
- perform essential urgent actions where feasible.

---

# 172. Mobile Admin global navigation

Recommended:

```text
Compact Header
+
Drawer
+
Context Actions
```

---

# 173. Mobile Sidebar replacement

Desktop sidebar becomes drawer/sheet.

Groups remain same logical grouping.

---

# 174. Mobile current context

Header MUST show current entity/workspace sufficiently.

---

# 175. Mobile action overflow

Secondary actions MAY move to `More`.

Primary action remains visible.

---

# 176. Mobile Publish

High-impact action must not be hidden so deeply that user cannot find it, but also not placed where accidental tap is likely.

---

# 177. Tablet Admin

Tablet should support most workflows except where fine crop/complex tables require adaptive presentation.

---

# 178. Responsive navigation does not remove capabilities

It changes presentation and interaction.

---

# 179. Permission-aware navigation

Navigation MUST reflect authorization.

A user without access to:

```text
Audit
```

should not receive sensitive audit data in hidden client payload.

---

# 180. Hidden vs Disabled

For unavailable permission:

- usually hide inaccessible module;
- or show disabled only if product intentionally needs to explain unavailable capability.

Never leak private data.

---

# 181. Future roles

Even if current Admin is single-user/owner-oriented, navigation SHOULD not assume all future roles have identical permissions.

---

# 182. Permission change during session

If access revoked:

next request/navigation must enforce new authorization.

Client navigation state is not authority.

---

# 183. Admin authentication

Unauthenticated access to `/admin/*` should lead to auth flow according to security architecture.

---

# 184. Auth redirect return

After successful authentication, user SHOULD return to intended safe admin destination where appropriate.

---

# 185. Session expiry

If session expires during editing:

- sensitive action blocked;
- user informed;
- unsaved Draft recovery attempted where safely possible.

---

# 186. Admin navigation accessibility

Sidebar/header/menu MUST use semantic landmarks and controls.

---

# 187. Keyboard navigation

All modules and page-level navigation must be keyboard accessible.

---

# 188. Current location

Current module/state must not be indicated by color alone.

---

# 189. Focus management

Drawer/modal navigation must handle focus correctly.

---

# 190. Tables

Large Admin lists may use tables, but row actions must remain keyboard accessible.

---

# 191. Table row navigation

Clicking arbitrary row may navigate, but explicit semantic links/actions should also exist.

---

# 192. Tabs

Tabs MUST follow accessible tab semantics when implemented as true tabbed panels.

Do not style links as tabs without coherent behaviour.

---

# 193. Pagination

Large lists SHOULD use predictable pagination/infinite loading pattern.

Admin navigation should preserve filters when moving list→detail→back.

---

# 194. Filter state preservation

Example:

```text
Projects
Filter: Draft
→ Project A
→ Back
```

should return to Draft filtered list where feasible.

---

# 195. Search state preservation

Same for global/local search.

---

# 196. Sorting state preservation

List sorting should not reset unnecessarily after editing one object.

---

# 197. Empty Admin State

Empty state should explain:

- what this module stores;
- what next action is.

Example:

```text
Пока нет подготовленных анкет.

[Создать первую анкету]
```

---

# 198. Empty State must not overuse AI

Do not make:

```text
Ask AI to create everything
```

the only action.

Manual creation remains available.

---

# 199. Error State

Module-level error should preserve sidebar/global navigation.

---

# 200. 404 Admin

Should provide:

- safe message;
- Dashboard;
- relevant parent module.

---

# 201. Authorization Error

Should distinguish:

```text
No access
```

from:

```text
Not found
```

only where security policy permits disclosure.

---

# 202. Background Job State

If user opens entity undergoing processing, context header MAY show:

```text
Processing derivatives…
```

with non-blocking navigation.

---

# 203. AI processing state

Example:

```text
Casting analysis in progress
```

should not freeze unrelated Casting tabs.

---

# 204. Navigation and concurrency

If another change supersedes current version, UI should surface conflict before save/apply.

---

# 205. Stale AI suggestion

If user navigates back to old suggestion after entity changed:

show:

```text
This suggestion is based on an older version.
Re-analyze before applying.
```

---

# 206. Navigation and Audit

Relevant entity detail MAY expose:

```text
View history
```

which opens Audit filtered to that entity.

---

# 207. Navigation and System Health

Failure message MAY link:

```text
View system status
```

for Admin, not public visitor.

---

# 208. Navigation and Help

Complex module SHOULD have contextual help link.

Example:

```text
Emotional Grid
→ How grid cropping works
```

---

# 209. Navigation and existing `/admin`

Existing `/admin` route MUST be preserved as administrative entry point unless migration strategy explicitly changes it.

---

# 210. Existing functionality preservation

Navigation redesign MUST preserve access to:

- multilingual editing;
- Projects/Roles;
- role-specific photos;
- typed Media;
- Skills;
- Languages;
- Help/Tickets;
- Instagram/TikTok publishing;
- authentication;
- existing content.

---

# 211. Legacy navigation migration

Old links/routes SHOULD be redirected/mapped where practical if route changes.

---

# 212. No orphaned legacy features

Feature preserved in Scope but missing from new navigation is considered migration defect.

---

# 213. Suggested complete admin tree

```text
/admin
│
├── dashboard
│
├── profile
│
├── portfolio
│
├── emotional
│   ├── sessions
│   └── grids
│
├── projects
│   └── {project}
│       ├── roles
│       ├── media
│       └── performances
│
├── training
├── skills-languages
├── achievements
├── media
├── contacts
│
├── questionnaires
│   ├── prepared
│   ├── templates
│   └── public-builder
│
├── castings
│   └── {casting}
│       ├── source
│       ├── requirements
│       ├── ai-analysis
│       ├── materials
│       └── questionnaire
│
├── inbox
├── opportunities
│
├── operator
├── bb-assistant
│
├── themes
├── analytics
├── social
│
├── help
├── settings
├── system
└── audit
```

Это conceptual route tree, не окончательный framework routing contract.

---

# 214. Primary Sidebar Example

```text
Dashboard

CONTENT
  Profile
  Portfolio
  Emotional
  Projects
  Training
  Skills & Languages
  Media
  Contacts
  Achievements

QUESTIONNAIRES
  Questionnaires
  Public Builder

CASTING
  Castings
  Inbox
  Opportunities

ASSISTANCE
  Virtual Operator
  BB Assistant

PRESENTATION
  Themes

GROWTH
  Analytics
  Social Publishing

SYSTEM
  Help
  Settings
  System Status
  Audit
```

---

# 215. Casting AI placement

Casting AI SHOULD NOT necessarily occupy permanent primary sidebar item.

Preferred:

```text
Castings
→ Casting
→ AI Analysis
```

plus optionally a review queue in AI Workspace.

---

# 216. Theme AI placement

Preferred:

```text
Themes
→ Generate / Edit with AI
```

not standalone primary navigation.

---

# 217. AI grouping principle

AI is attached to professional context.

The navigation should not imply that AI is a separate source of truth.

---

# 218. Content Creation action

Global `Create` button MAY exist.

Potential options:

```text
Project
Training
Questionnaire
Casting
```

but only if it materially speeds work.

---

# 219. Global Create must not become huge menu

Avoid listing every possible entity.

Contextual creation is usually clearer.

---

# 220. Example contextual create

In Projects:

```text
[New Project]
```

In Training:

```text
[Add Training]
```

In Questionnaires:

```text
[Create Questionnaire]
```

---

# 221. Search vs Navigation

Global search supplements navigation.

It does not replace clear module structure.

---

# 222. Recently Viewed

MAY be offered for power users.

Not required.

---

# 223. Favorites/Pinned Modules

MAY be future convenience.

Not part of core target.

---

# 224. Admin Navigation analytics

MAY measure aggregate usability:

```text
dashboard_action_opened
admin_search_used
module_opened
```

but should not create unnecessary behavioral surveillance.

---

# 225. Operational analytics

More important than sidebar click count:

- unresolved exceptions;
- time to resolution;
- time to inquiry review;
- time to casting action.

---

# 226. NAV-ADM-001

`/admin` MUST lead to operational Dashboard.

---

# 227. NAV-ADM-002

Dashboard MUST expose actionable exceptions and professional deadlines.

---

# 228. NAV-ADM-003

Domain workspaces MUST remain directly reachable independent of Dashboard.

---

# 229. NAV-ADM-004

Projects and Castings MUST remain separate navigation domains.

---

# 230. NAV-ADM-005

Portfolio and Media Library MUST remain distinct workspaces.

---

# 231. NAV-ADM-006

Emotional Portfolio MUST remain distinct from Projects and Main Portfolio.

---

# 232. NAV-ADM-007

Training MUST remain separate from Projects.

---

# 233. NAV-ADM-008

Prepared Questionnaires and Public Builder configuration MUST remain within Questionnaire domain.

---

# 234. NAV-ADM-009

Inbox/Feedback MUST be separately accessible from Castings.

---

# 235. NAV-ADM-010

Feedback linked to Casting MUST retain bidirectional navigation.

---

# 236. NAV-ADM-011

Opportunity Pipeline MUST remain distinct from raw Feedback.

---

# 237. NAV-ADM-012

Virtual Operator MUST be accessible as an action/exception workspace.

---

# 238. NAV-ADM-013

BB Assistant MUST be distinct from Casting AI, Theme AI and Virtual Operator.

---

# 239. NAV-ADM-014

AI capabilities MUST preserve source/entity context.

---

# 240. NAV-ADM-015

Themes MUST separate Draft, Preview and Published states.

---

# 241. NAV-ADM-016

Analytics MUST remain separate from operational System Health.

---

# 242. NAV-ADM-017

Social Publishing and Help/Tickets existing capabilities MUST remain navigable.

---

# 243. NAV-ADM-018

System Status and Audit MUST be accessible without exposing them publicly.

---

# 244. NAV-ADM-019

Current entity/state MUST remain visible during editing.

---

# 245. NAV-ADM-020

Deep admin relations SHOULD provide direct navigation between related entities.

---

# 246. NAV-ADM-021

Navigation MUST preserve filters/context where practical.

---

# 247. NAV-ADM-022

Navigation MUST warn or safely autosave before abandoning unsaved significant changes.

---

# 248. NAV-ADM-023

Admin navigation MUST be permission-aware server-side.

---

# 249. NAV-ADM-024

Complex admin workflows MAY be desktop-first, but urgent operational workflows SHOULD remain usable on mobile.

---

# 250. NAV-ADM-025

Optional AI/analytics failure MUST NOT make core Admin navigation unusable.

---

# 251. Navigation anti-pattern ADM-AP-001

**Entity Dump**

Все DB entities находятся на первом уровне sidebar.

---

# 252. ADM-AP-002

**Dashboard as Statistics Wall**

Нет actionable items.

---

# 253. ADM-AP-003

**One Generic AI Menu**

Все AI задачи смешаны в одном prompt box.

---

# 254. ADM-AP-004

**Projects = Castings**

Разные business entities смешаны в одном workspace.

---

# 255. ADM-AP-005

**Media = Portfolio**

Asset management и professional classification смешаны.

---

# 256. ADM-AP-006

**Questionnaire Revision as Independent Questionnaire**

Historical revisions выглядят как текущие editable documents.

---

# 257. ADM-AP-007

**Feedback disappears after Casting**

Теряется source communication.

---

# 258. ADM-AP-008

**Pipeline stage as cosmetic column**

Drag card changes stage without business validation.

---

# 259. ADM-AP-009

**Admin settings junk drawer**

Domain-specific options спрятаны в Settings.

---

# 260. ADM-AP-010

**Context loss**

User clicks related Project from Media and cannot return/understand source relationship.

---

# 261. ADM-AP-011

**Modal maze**

Project → Role → Media → Edit all nested modals.

---

# 262. ADM-AP-012

**Hidden unsaved loss**

Navigation silently discards edits.

---

# 263. ADM-AP-013

**Sidebar as permissions security**

Item hidden client-side, but route/API still accessible.

---

# 264. ADM-AP-014

**Analytics mixed with operational alerts**

Traffic chart and broken Showreel shown as same concept.

---

# 265. ADM-AP-015

**Mobile removes critical operations**

Urgent Casting cannot even be reviewed from smartphone.

---

# 266. ADM-AP-016

**Legacy feature orphaning**

Existing Help/Social/Role Photos functionality survives in code but disappears from navigation.

---

# 267. Admin Navigation Quality Gate

Перед реализацией каждого Admin module должны быть определены:

- [ ] Primary navigation group
- [ ] Route
- [ ] List view
- [ ] Detail view
- [ ] Local filters/tabs
- [ ] Primary action
- [ ] Lifecycle states
- [ ] Related entities
- [ ] Breadcrumb/context model
- [ ] Empty state
- [ ] Error state
- [ ] Unsaved-change behaviour
- [ ] Mobile operational behaviour
- [ ] Permissions
- [ ] Audit/history access
- [ ] Public Preview where relevant
- [ ] Help/context support

---

# 268. Cross-module navigation test

Минимально должны работать цепочки:

```text
Dashboard
→ Broken Video
→ Media
```

```text
Inbox
→ Casting
→ AI Analysis
→ Questionnaire
```

```text
Opportunity
→ Casting
→ Source Inquiry
```

```text
Media
→ Portfolio Item
→ Questionnaire Usage
```

```text
Project
→ Role
→ Role Media
```

```text
Virtual Operator
→ Affected Entity
→ Resolve
```

---

# 269. Admin task completion target

Routine domain destination SHOULD обычно быть доступна:

```text
≤ 1 sidebar action
```

из любого основного Admin workspace.

Deep entity:

```text
module
→ entity
```

without excessive hierarchy.

---

# 270. Dashboard action target

Critical exception SHOULD be resolvable by:

```text
Dashboard
→ affected context
→ action
```

без ручного поиска сущности.

---

# 271. Public Preview target

Для publishable content:

```text
Edit
→ Preview
→ Publish
```

должно быть естественным navigation flow.

---

# 272. AI assistance target

Для contextual AI:

```text
Entity
→ AI Assistance
→ Review Draft/Recommendation
→ Apply
→ return to Entity
```

не теряя context.

---

# 273. Navigation and business meaning

Расположение menu item не определяет ownership данных.

Например:

`Casting AI` внутри Casting Detail не означает, что AI владеет Casting.

---

# 274. Admin terminology

UI labels SHOULD использовать понятные domain terms:

```text
Кастинги
Входящие
Анкеты
Портфолио
Обучение
Контакты
```

а не internal technical naming.

---

# 275. Admin state terminology

Use canonical:

```text
Draft
Published
Archived
Stale
Ready
Blocked
```

с локализованными UI labels.

---

# 276. Navigation and readiness

Readiness SHOULD быть доступна:

- list badge;
- entity header;
- publish workflow.

---

# 277. Readiness click

Clicking a blocked state SHOULD open blocker details, not generic help page.

---

# 278. Navigation and dependencies

From destructive/change-sensitive actions user SHOULD be able to inspect dependencies.

Example:

```text
This contact is used by:
• Primary Questionnaire
• Public Builder template
```

---

# 279. Navigation and revisions

Revision history should always link back to current entity.

---

# 280. Navigation and audit actor

Audit details MAY link to:

- entity;
- affected user;
- AI capability context.

---

# 281. Navigation and automation history

Virtual Operator automatic actions MAY have history link:

```text
View automation history
```

without cluttering normal content screens.

---

# 282. Navigation and service degradation

If AI/PDF/social integration degraded, a compact global Admin indicator MAY be used.

Example:

```text
1 service degraded
```

Click → System Status.

---

# 283. Do not over-alert

Global red banner reserved for material cross-product issue.

Minor service failure belongs notification/system status.

---

# 284. Public/Private navigation separation

Admin navigation and public navigation MUST remain visually and semantically distinct.

Admin should always know they are in management environment.

---

# 285. Public Preview separation

Public Preview may open new tab/window or explicit preview mode.

It should not make Admin mistake Preview for live site state.

---

# 286. Preview banner

Admin preview MAY include:

```text
PREVIEW — not published
```

when previewing Draft.

---

# 287. Preview permissions

Preview should not accidentally make private draft accessible by public URL.

---

# 288. Definition of Admin Navigation Compliance

Admin Navigation соответствует целевой модели, если:

1. `/admin` открывает exception-driven Dashboard;
2. все основные domains доступны напрямую;
3. Projects, Castings, Feedback и Opportunities разделены;
4. Media и Portfolio разделены;
5. Emotional Portfolio является отдельным workspace;
6. Questionnaires/Public Builder имеют собственный navigation domain;
7. Virtual Operator направляет к конкретным actionable entities;
8. BB Assistant, Casting AI, Theme AI и Virtual Operator различимы;
9. related entities связаны прямыми navigation links;
10. Draft/Published/Archived/Revision context всегда понятен;
11. unsaved changes не теряются молча;
12. mobile поддерживает urgent operational workflows;
13. navigation соблюдает permissions;
14. existing Help/Social/multilingual capabilities не теряются;
15. Public Preview доступен для publishable content;
16. optional AI/analytics failure не ломает core Admin experience.

---

# 289. Итоговая модель

```text
                           ADMIN

                         DASHBOARD
                             │
             What requires attention now?
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
     CONTENT              CASTING             SYSTEM
        │                    │                    │
 Profile                Castings             Status
 Portfolio              Inbox                Audit
 Emotional              Opportunities        Settings
 Projects
 Training
 Skills/Languages
 Media
 Contacts
        │                    │
        ├──── Questionnaires ┤
        │                    │
        └──── ASSISTANCE ────┘
                 │
       Virtual Operator
       BB Assistant
       Casting AI
       Theme AI
                 │
             PRESENTATION
                 │
               Themes
                 │
               GROWTH
                 │
        Analytics / Social
```

---

# 290. Финальный принцип

> **Административная навигация должна сочетать два подхода: Dashboard сообщает, что требует внимания сейчас, а domain workspaces дают полный контроль над источниками профессиональных данных. Администратор не должен обходить модули в поисках проблем и не должен терять контекст при переходе между связанными сущностями. AI, автоматизация, аналитика и системные сервисы помогают принять решение, но навигационная структура всегда остаётся организованной вокруг профессиональных данных и реальных бизнес-процессов.**