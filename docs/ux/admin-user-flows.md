# ADMIN USER FLOWS

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативные пользовательские сценарии административной части продукта

**Целевой файл:** `docs/ux/admin-user-flows.md`  
**Документ:** DOC-044  
**Статус:** ✅ Completed  
**Тип:** UX / Admin User Flows

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/virtual-operator.md`
- `docs/customer-journey/conversion-funnel.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/ux-principles.md`
- `docs/ux/public-navigation.md`
- `docs/ux/admin-navigation.md`
- `docs/ux/public-user-flows.md`

---

# 1. Назначение документа

Настоящий документ определяет полный нормативный набор административных User Flows целевого продукта.

Документ переводит:

```text
Business Rules
+
Domain Boundaries
+
Admin Navigation
+
Automation Matrix
+
Virtual Operator Journey
```

в конкретные рабочие последовательности:

```text
Trigger
→ Context
→ User Action
→ System Validation
→ Human Decision
→ Persistence
→ Publication / Resolution
→ Verification
```

Документ является основанием для:

- Admin screen specifications;
- server actions;
- permissions;
- workflow state machines;
- background processing;
- Functional Requirements;
- Acceptance Criteria;
- Audit requirements;
- integration tests;
- E2E Admin tests.

---

# 2. Admin User Flow Definition

`Admin User Flow` — последовательность действий администратора и системы, приводящая к одному понятному административному результату.

Например:

```text
UF-ADM-QST-006
Open Questionnaire Draft
→ Validate
→ Preview
→ Publish Revision
→ Verify Public Version
```

---

# 3. ID Convention

Используется:

```text
UF-ADM-{DOMAIN}-{NNN}
```

Доменные prefixes:

```text
DSH — Dashboard
PRO — Profile
MED — Media
POR — Portfolio
EMO — Emotional Portfolio / Grid
PRJ — Projects / Roles
TRN — Training
SKL — Skills / Languages
ACH — Achievements
LNK — Professional Links
CON — Contacts
QST — Prepared Questionnaires
PQB — Public Builder Administration
CST — Castings
CAI — Casting AI
FDB — Feedback / Inbox
OPP — Opportunity Pipeline
NOT — Notifications
VOP — Virtual Operator
BBA — BB Assistant
THM — Themes / Theme AI
I18N — Localization
SOC — Social Publishing
HLP — Help / Tickets
ANL — Analytics
SYS — System / Operations
AUD — Audit
SEC — Security / Authentication
```

---

# 4. Общий Admin Flow Contract

Каждый flow SHOULD определить:

1. Flow ID.
2. Actor/Role.
3. Trigger.
4. Preconditions.
5. Goal.
6. Main Path.
7. System Validation.
8. Human Decision point.
9. Persistence behaviour.
10. Audit behaviour.
11. Failure path.
12. Concurrency behaviour.
13. Mobile behaviour where relevant.
14. Success condition.

---

# 5. Главный административный принцип

Администратор не должен начинать каждую рабочую сессию с ручного обхода всех разделов.

Целевая модель:

```text
Dashboard
 ↓
Action Required
 ↓
Affected Context
 ↓
Decision / Edit
 ↓
Resolve
```

а для плановой работы:

```text
Domain Workspace
 ↓
Create / Edit
 ↓
Validate
 ↓
Preview
 ↓
Publish
```

---

# 6. Основные классы Admin Flow

Все administrative flows относятся к одному из пяти классов:

```text
AF-01 OPERATE
AF-02 EDIT
AF-03 REVIEW
AF-04 PUBLISH
AF-05 ANALYZE
```

---

# 7. AF-01 — Operate

Краткосрочные business/operational задачи:

- новое Casting Inquiry;
- deadline;
- broken Video;
- notification failure;
- urgent opportunity action.

---

# 8. AF-02 — Edit

Работа с Source/Master Data:

- Profile;
- Project;
- Skill;
- Contact;
- Training.

---

# 9. AF-03 — Review

Human-in-the-loop:

- AI recommendation;
- photo classification;
- Casting requirements;
- Emotional Grid crop;
- migration ambiguity.

---

# 10. AF-04 — Publish

Explicit professional state transition:

```text
Draft
→ Ready
→ Preview
→ Publish
```

---

# 11. AF-05 — Analyze

Работа с:

- Analytics;
- Audit;
- System Health;
- operational history.

---

# 12. UF-ADM-DSH-001 — Open Admin Dashboard

## Trigger

Admin opens `/admin`.

## Flow

```text
Authenticate
 ↓
Dashboard
 ↓
Load:
- Action Required
- Deadlines
- New Inquiries
- Pending Review
- Content Health
- System Health
```

## Success

Admin immediately understands:

```text
What requires attention now?
```

---

# 13. UF-ADM-DSH-002 — Open Critical Exception

```text
Dashboard
 ↓
Critical alert
 ↓
Open affected entity
 ↓
Problem context shown
```

The admin MUST NOT need to manually locate the entity afterward.

---

# 14. UF-ADM-DSH-003 — Resolve Dashboard Exception

Example:

```text
Showreel unavailable
 ↓
Open Video record
 ↓
Replace URL
 ↓
Validate
 ↓
Save
 ↓
Virtual Operator rechecks
 ↓
Exception resolved
```

---

# 15. UF-ADM-DSH-004 — Review Deadline

```text
Dashboard
 ↓
Casting deadline tomorrow
 ↓
Casting Detail
 ↓
Review Next Action
 ↓
Update workflow
```

---

# 16. UF-ADM-DSH-005 — Review Recently Automated Work

```text
Dashboard
 ↓
Automatically Resolved
 ↓
4 thumbnails regenerated
 ↓
Optional detail/history
```

No confirmation required for safe routine work.

---

# 17. UF-ADM-PRO-001 — Edit Profile Fact

## Example

Location.

## Flow

```text
Profile
 ↓
Edit Location
 ↓
Validate
 ↓
Save Draft/current source
 ↓
Dependencies recalculated
 ↓
Affected projections invalidated/rebuilt
```

---

# 18. UF-ADM-PRO-002 — Edit Biography

```text
Profile
 ↓
Biography editor
 ↓
Edit manually
 ↓
Save Draft
 ↓
Preview
 ↓
Publish according to profile workflow
```

---

# 19. UF-ADM-PRO-003 — Use BB Assistant for Biography

```text
Biography editor
 ↓
Open BB Assistant
 ↓
Current approved profile facts assembled
 ↓
Generate Draft
 ↓
Review
 ↓
Apply
 ↓
Editable Biography updated
 ↓
Save
 ↓
Human Publish
```

AI Draft MUST NOT auto-publish.

---

# 20. UF-ADM-PRO-004 — Change Professional Parameter

System MUST:

- validate type/range;
- preserve Source of Truth;
- invalidate relevant projections;
- not rewrite historical snapshots.

---

# 21. UF-ADM-PRO-005 — Preview Public Profile

```text
Profile
 ↓
Preview Public
 ↓
Render actual Public Projection
 ↓
Return to Admin context
```

---

# 22. UF-ADM-MED-001 — Upload Media

```text
Media Library
 ↓
Upload
 ↓
Select file(s)
 ↓
Server validates
 ↓
Original stored
 ↓
Metadata extracted
 ↓
Derivatives queued/generated
 ↓
Media records available
```

---

# 23. UF-ADM-MED-002 — Bulk Upload

```text
Upload 16 files
 ↓
Grouped processing
 ↓
Progress
 ↓
Successful / Failed items
```

Partial failure MUST be explicit.

---

# 24. UF-ADM-MED-003 — Review Media Processing

States MAY include:

```text
Queued
Processing
Ready
Failed
```

Admin can continue unrelated work.

---

# 25. UF-ADM-MED-004 — Review Duplicate Suggestion

```text
New Media
 ↓
Possible duplicate
 ↓
Compare
 ↓
Admin decides:
- reuse existing
- keep both
- archive/delete according to policy
```

---

# 26. UF-ADM-MED-005 — Open Media Usage

```text
Media Asset
 ↓
Used In
 ↓
Portfolio
Project
Questionnaire
Role
```

Each relation is navigable.

---

# 27. UF-ADM-MED-006 — Replace External Video URL

```text
Media / Video
 ↓
Edit URL
 ↓
Syntax validation
 ↓
Reachability validation
 ↓
Save
 ↓
QR / questionnaire / public dependencies recalculated
```

---

# 28. UF-ADM-MED-007 — Delete Media With Dependencies

```text
Media
 ↓
Delete
 ↓
Dependency check
 ↓
Dependencies displayed
 ↓
Block or require explicit resolution
```

No silent cascading loss.

---

# 29. UF-ADM-POR-001 — Create Portfolio Item From Media

```text
Media Asset
 ↓
Add to Portfolio
 ↓
Select Category
 ↓
Optional metadata
 ↓
Save
```

The underlying file is referenced, not duplicated.

---

# 30. UF-ADM-POR-002 — Review AI Photo Classification

```text
Virtual Operator / Portfolio
 ↓
Suggestion: Close-Up
 ↓
Open photo
 ↓
Approve / Modify / Reject
```

---

# 31. UF-ADM-POR-003 — Set Primary Close-Up

```text
Portfolio
 ↓
Select eligible Close-Up
 ↓
Set as Primary
 ↓
Confirm
 ↓
Previous Primary demoted
 ↓
Dependencies invalidated
```

---

# 32. UF-ADM-POR-004 — Set Primary Full Body

Equivalent flow.

---

# 33. UF-ADM-POR-005 — Publish Portfolio Item

```text
Portfolio Item Draft
 ↓
Validate
 ↓
Preview
 ↓
Publish
```

---

# 34. UF-ADM-POR-006 — Archive Portfolio Item

```text
Published Item
 ↓
Archive
 ↓
Show affected current projections
 ↓
Confirm
 ↓
Archive
```

Historical snapshot use remains according to revision policy.

---

# 35. UF-ADM-EMO-001 — Create Emotional Portfolio Session

```text
Emotional Portfolio
 ↓
New Session
 ↓
Set Shooting Date
 ↓
Attach source media
 ↓
Save
```

Shooting Date is mandatory according to canonical requirements.

---

# 36. UF-ADM-EMO-002 — Add Photos to Emotional Session

Only existing/referenced Media Assets SHOULD be selected.

---

# 37. UF-ADM-EMO-003 — Create Emotional Grid

```text
Session
 ↓
New Grid
 ↓
Select allowed dimensions
 ↓
System determines exact cell count
 ↓
Select exact number of source photos
```

Allowed sizes:

```text
1x2, 1x3, 1x4
2x2, 2x3, 2x4
3x2, 3x3, 3x4
4x2, 4x3, 4x4
```

---

# 38. UF-ADM-EMO-004 — Review Suggested Grid Photos

Virtual Operator MAY propose candidates.

Admin:

```text
Review
→ Accept/Replace each
```

---

# 39. UF-ADM-EMO-005 — Edit Grid Cell

For each cell:

```text
Select source image
 ↓
Adjust crop/pan/scale/rotation
 ↓
Review face prominence
 ↓
Confirm cell
```

---

# 40. UF-ADM-EMO-006 — Confirm Entire Grid

All cells MUST be confirmed before finalization.

```text
All cells confirmed?
 ├── No → block finalization
 └── Yes → generate derived output
```

---

# 41. UF-ADM-EMO-007 — Generate Grid Derivatives

After confirmation:

```text
Master composite
Web derivative
PDF derivative
Thumbnail
```

generated automatically.

---

# 42. UF-ADM-EMO-008 — Publish Emotional Grid

```text
Finalized Grid
 ↓
Public Preview
 ↓
Publish
```

No automatic publication.

---

# 43. UF-ADM-EMO-009 — Set Primary Emotional Grid

If multiple Grids exist:

```text
Grid
→ Set Primary
→ Confirm
```

---

# 44. UF-ADM-PRJ-001 — Create Project

```text
Projects
 ↓
New Project
 ↓
Enter structured facts
 ↓
Save Draft
```

---

# 45. UF-ADM-PRJ-002 — Add Role to Project

```text
Project
 ↓
Roles
 ↓
Add Role
 ↓
Role data
 ↓
Save
```

---

# 46. UF-ADM-PRJ-003 — Add Role Photos

```text
Role
 ↓
Select Media Assets
 ↓
Up to applicable configured limits
 ↓
Save relations
```

Existing role-specific photo capability MUST be preserved.

---

# 47. UF-ADM-PRJ-004 — Select Home “В образе” Photos

Within established product rule:

```text
Role photos
 ↓
Select up to allowed showcase items
 ↓
Preview Home representation
 ↓
Save
```

---

# 48. UF-ADM-PRJ-005 — Add Performance Date

```text
Project / Role
 ↓
Performances
 ↓
Add Date
 ↓
Validate
 ↓
Save
```

---

# 49. UF-ADM-PRJ-006 — Feature Project

```text
Project
 ↓
Set Featured
 ↓
Optional ordering
 ↓
Save
```

Human-controlled presentation decision.

---

# 50. UF-ADM-PRJ-007 — Publish Project

```text
Draft Project
 ↓
Readiness
 ↓
Preview
 ↓
Publish
```

---

# 51. UF-ADM-PRJ-008 — Archive Project

Must preserve relations/history according to lifecycle policy.

---

# 52. UF-ADM-TRN-001 — Create Training Record

```text
Training
 ↓
Add
 ↓
Institution / Course / Dates / Description
 ↓
Save
```

---

# 53. UF-ADM-TRN-002 — Migrate Legacy “Other Project” to Training

```text
Migration Candidate
 ↓
Review source
 ↓
Confirm Training classification
 ↓
Create/transform Training
 ↓
Preserve provenance
```

Ambiguous content requires human review.

---

# 54. UF-ADM-SKL-001 — Add Skill

```text
Skills
 ↓
Add Skill
 ↓
Choose canonical skill/category
 ↓
Set level
 ↓
Visibility
 ↓
Save
```

---

# 55. UF-ADM-SKL-002 — Edit Skill Level

Changing Skill Level updates current Profile projections but not immutable historical snapshots.

---

# 56. UF-ADM-SKL-003 — Add Language

```text
Languages
 ↓
Add Language
 ↓
Language
 ↓
Level / CEFR
 ↓
Save
```

---

# 57. UF-ADM-SKL-004 — Update CEFR

System must not derive an unsupported higher fluency label.

---

# 58. UF-ADM-ACH-001 — Add Achievement

```text
Achievements
 ↓
Add
 ↓
Title / Organization / Date / Evidence
 ↓
Save
```

---

# 59. UF-ADM-LNK-001 — Add Professional External Link

Exact required editor structure:

```text
Number
Description
URL
```

---

# 60. UF-ADM-LNK-002 — Validate Professional Link

```text
Enter URL
 ↓
Validate syntax
 ↓
Optional reachability check
 ↓
Save
```

---

# 61. UF-ADM-LNK-003 — Configure Questionnaire QR

```text
Link
 ↓
Allow in Questionnaire
 ↓
Enable QR for selected questionnaire/context
 ↓
Preview
```

QR visibility is independently configurable where specified.

---

# 62. UF-ADM-LNK-004 — QR Regeneration After URL Change

```text
Canonical URL changes
 ↓
QR derivative marked stale
 ↓
Regenerate
 ↓
Decode
 ↓
Verify exact equality
```

---

# 63. UF-ADM-CON-001 — Create Contact

```text
Contacts
 ↓
Add Contact
 ↓
Type / Label / Value
 ↓
Visibility
 ↓
Save
```

---

# 64. UF-ADM-CON-002 — Configure Independent Visibility

Admin sets separately:

```text
Show on Public Site
Allow in Prepared Questionnaires
Allow in Public Questionnaire Builder
```

---

# 65. UF-ADM-CON-003 — Configure Parent / Guardian Contact

The relation itself MUST NOT imply public visibility.

Admin explicitly selects visibility scopes.

---

# 66. UF-ADM-CON-004 — Disable Contact From Public Builder

```text
Contact
 ↓
Disable Public Builder permission
 ↓
Save
 ↓
Existing active sessions remain subject to server revalidation
```

---

# 67. UF-ADM-QST-001 — Create Prepared Questionnaire

```text
Questionnaires
 ↓
Create
 ↓
Choose type:
Compact / Extended / Casting / Custom
 ↓
Name / Locale / Configuration
 ↓
Draft
```

---

# 68. UF-ADM-QST-002 — Configure Questionnaire Blocks

Admin selects:

- Profile facts;
- Portfolio Items;
- Emotional Grid;
- Projects;
- Roles;
- Training;
- Achievements;
- Video;
- Audio;
- Links;
- Contacts.

Selections reference Source entities.

---

# 69. UF-ADM-QST-003 — Configure Questionnaire Order

Admin can arrange logical blocks according to allowed structure.

---

# 70. UF-ADM-QST-004 — Configure Link and QR Rendering

For each supported link:

```text
Show hyperlink?
Show QR?
```

according to module rules.

---

# 71. UF-ADM-QST-005 — Validate Questionnaire Readiness

System checks:

```text
Required photos
Required identification
Valid links
Allowed contacts
QR targets
Locale completeness
Other mandatory rules
```

---

# 72. UF-ADM-QST-006 — Preview Questionnaire

```text
Draft
 ↓
HTML Preview
 ↓
PDF Preview
```

Preview uses actual visibility/projection rules.

---

# 73. UF-ADM-QST-007 — Publish Questionnaire Revision

```text
Draft
 ↓
Ready?
 ↓
Human Publish
 ↓
New immutable Revision
 ↓
Current Published pointer updated
```

---

# 74. UF-ADM-QST-008 — Set Primary Questionnaire

```text
Published Questionnaire
 ↓
Set Primary
 ↓
Confirm
 ↓
Previous Primary removed
```

Exactly one active primary public questionnaire where configured.

---

# 75. UF-ADM-QST-009 — Open Historical Revision

```text
Questionnaire
 ↓
Revisions
 ↓
Revision
 ↓
Read-only view
```

---

# 76. UF-ADM-QST-010 — Restore From Historical Revision

If restoration capability is implemented:

```text
Historical Revision
 ↓
Create new Draft from revision
```

Historical revision itself remains unchanged.

---

# 77. UF-ADM-QST-011 — Archive Questionnaire

Published history remains preserved according to lifecycle policy.

---

# 78. UF-ADM-PQB-001 — Enable Public Builder

```text
Questionnaires
 ↓
Public Builder Settings
 ↓
Enable
 ↓
Configure allowed templates
 ↓
Save
```

---

# 79. UF-ADM-PQB-002 — Configure Builder Template

```text
Template
 ↓
Default blocks
Required blocks
Optional blocks
Limits
Ordering rules
 ↓
Preview
 ↓
Save
```

---

# 80. UF-ADM-PQB-003 — Configure Allowed Content

Admin defines item-level/domain-level availability where relevant.

---

# 81. UF-ADM-PQB-004 — Configure Template Limits

Examples:

- max Project selections;
- max additional photos;
- reordering enabled/disabled.

---

# 82. UF-ADM-PQB-005 — Preview Public Builder

Admin SHOULD see actual public flow under configured rules.

---

# 83. UF-ADM-PQB-006 — Review Builder Analytics Recommendation

```text
Virtual Operator / Analytics
 ↓
"Users frequently add Emotional Grid"
 ↓
Open Template
 ↓
Human reviews
 ↓
Optional manual change
```

---

# 84. UF-ADM-PQB-007 — Disable Item Permission

System MUST ensure future/public generation revalidates existing temporary sessions.

---

# 85. UF-ADM-FDB-001 — Open Inbox

```text
Admin
 ↓
Inbox
 ↓
Unread / Priority items
```

---

# 86. UF-ADM-FDB-002 — Open Professional Inquiry

Admin sees:

- sender;
- type;
- message;
- source;
- attachments;
- questionnaire context;
- attribution;
- status.

---

# 87. UF-ADM-FDB-003 — Mark Inquiry Read

Simple operational state.

Does not qualify it.

---

# 88. UF-ADM-FDB-004 — Assign Responsible Person

If multi-user workflow exists/targeted:

```text
Inquiry
→ Assign responsible
```

---

# 89. UF-ADM-FDB-005 — Add Internal Note

Internal note MUST remain private.

---

# 90. UF-ADM-FDB-006 — Set Next Action

Example:

```text
Review casting brief
Call producer
Prepare materials
```

according to data model.

---

# 91. UF-ADM-FDB-007 — Create Casting From Inquiry

```text
Inquiry
 ↓
Create Casting
 ↓
System pre-fills safe source context
 ↓
Admin reviews
 ↓
Confirm
 ↓
Casting created
 ↓
Bidirectional link stored
```

---

# 92. UF-ADM-FDB-008 — Link Inquiry to Existing Casting

No duplicate Casting required.

---

# 93. UF-ADM-FDB-009 — Draft Reply With BB Assistant

```text
Inquiry
 ↓
Draft Reply
 ↓
BB Assistant
 ↓
Source facts/context
 ↓
Draft
 ↓
Human edit
 ↓
Human send through supported channel
```

---

# 94. UF-ADM-FDB-010 — Handle Notification Failure

```text
Inquiry exists
 ↓
WhatsApp notification failed
 ↓
Operator alert
 ↓
Admin reviews
```

Inquiry remains intact.

---

# 95. UF-ADM-CST-001 — Create Casting Manually

```text
Castings
 ↓
New Casting
 ↓
Source / Project / Role / Deadline
 ↓
Save
```

---

# 96. UF-ADM-CST-002 — Import Casting Source Text

```text
Casting
 ↓
Add Source
 ↓
Paste text
 ↓
Save original source
```

---

# 97. UF-ADM-CST-003 — Add Casting Source Files

```text
Casting
 ↓
Attach images/files
 ↓
Validate
 ↓
Store privately
```

---

# 98. UF-ADM-CAI-001 — Run Casting AI Analysis

```text
Casting
 ↓
AI Analysis
 ↓
Run
 ↓
Analyze stored source
 ↓
Extract Requirements
 ↓
Compare to confirmed Profile
 ↓
Generate Recommendations
```

---

# 99. UF-ADM-CAI-002 — Review Extracted Requirement

UI MUST separate:

```text
Source Text
AI Extraction
```

Admin can:

```text
Confirm
Edit
Reject
```

---

# 100. UF-ADM-CAI-003 — Missing Casting Requirement

If source does not specify value:

```text
Не указано
```

No inference to Master Data.

---

# 101. UF-ADM-CAI-004 — Review Profile Match

Example:

```text
Requirement:
English B2

Profile:
English B2

Result:
Match
```

or:

```text
Requirement:
Horse riding

Profile:
No published/confirmed matching fact

Result:
Not specified in profile
```

Not automatic rejection.

---

# 102. UF-ADM-CAI-005 — Review Recommendation

AI may propose:

- highlight matching evidence;
- request missing material;
- include relevant project.

Human decides.

---

# 103. UF-ADM-CAI-006 — Save Casting Analysis Revision

Each substantive analysis SHOULD be revisioned/versioned according to AI/Casting architecture.

---

# 104. UF-ADM-CST-004 — Generate Casting Questionnaire Draft

```text
Casting
 ↓
Create Questionnaire Draft
 ↓
Use confirmed Casting Requirements
+
confirmed Profile facts
 ↓
Draft
 ↓
Human review
```

---

# 105. UF-ADM-CST-005 — Publish Casting Questionnaire

Normal Questionnaire publication rules still apply.

---

# 106. UF-ADM-CST-006 — Review Casting Deadline

Deadline appears:

- Casting detail;
- Dashboard;
- Operator.

---

# 107. UF-ADM-CST-007 — Close Casting

Human selects appropriate terminal state/outcome.

No automatic `Not Selected` based solely on inactivity.

---

# 108. UF-ADM-OPP-001 — Create Opportunity

Opportunity MAY be created as result of qualified Casting/business workflow.

Human/authoritative business action required.

---

# 109. UF-ADM-OPP-002 — Move Opportunity to Qualified

```text
Opportunity
 ↓
Stage Change
 ↓
Validate allowed transition
 ↓
Confirm
 ↓
Persist
 ↓
Audit
```

---

# 110. UF-ADM-OPP-003 — Materials Requested

Stage updated on authoritative business evidence.

---

# 111. UF-ADM-OPP-004 — Self-Tape Requested

Same principle.

---

# 112. UF-ADM-OPP-005 — Audition

Admin records external professional event.

---

# 113. UF-ADM-OPP-006 — Callback

```text
Audition
→ Callback
```

through valid transition.

---

# 114. UF-ADM-OPP-007 — Offer

Offer is recorded as explicit business event.

---

# 115. UF-ADM-OPP-008 — Booked

```text
Offer / applicable stage
 ↓
Booked
 ↓
Confirm
 ↓
Business outcome persisted
```

---

# 116. UF-ADM-OPP-009 — Closed — Not Selected

Must remain distinct from Withdrawn.

---

# 117. UF-ADM-OPP-010 — Withdraw

Human-controlled outcome.

---

# 118. UF-ADM-OPP-011 — Create Project/Role From Booked Opportunity

Where implemented:

```text
Booked
 ↓
Create Project / Role Draft
 ↓
Pre-fill confirmed known data
 ↓
Admin review
 ↓
Save
```

No automatic professional history publication.

---

# 119. UF-ADM-NOT-001 — Review Notification Center

```text
Admin Header
 ↓
Notifications
 ↓
Operational/system notifications
```

Distinct from external Inbox.

---

# 120. UF-ADM-NOT-002 — Open Notification Target

Every actionable notification SHOULD deep-link to context.

---

# 121. UF-ADM-NOT-003 — Retry Failed Notification

Where applicable:

```text
Failed notification
 ↓
Retry
 ↓
Idempotency check
 ↓
Send
```

---

# 122. UF-ADM-VOP-001 — Open Virtual Operator

```text
Dashboard / Sidebar
 ↓
Virtual Operator
 ↓
Action Required
Observations
Suggestions
Resolved
```

---

# 123. UF-ADM-VOP-002 — Review Observation

```text
Observation
 ↓
Observed Fact
Reason
Severity
Affected Entity
Affected Journey
```

---

# 124. UF-ADM-VOP-003 — Approve Suggestion

```text
Suggestion
 ↓
Review source/context
 ↓
Approve
 ↓
System revalidates entity version
 ↓
Apply
 ↓
Verify
 ↓
Audit
```

---

# 125. UF-ADM-VOP-004 — Modify Suggestion

```text
Suggestion
 ↓
Edit proposed value/configuration
 ↓
Apply modified result
```

---

# 126. UF-ADM-VOP-005 — Reject Suggestion

System records rejection and avoids immediate repetitive resurfacing without changed conditions.

---

# 127. UF-ADM-VOP-006 — Defer Suggestion

Admin may postpone according to supported rules.

---

# 128. UF-ADM-VOP-007 — Resolve Broken Link

Operator → Link entity → Fix → Revalidate → Auto-resolve exception.

---

# 129. UF-ADM-VOP-008 — Review New Media Classification

Grouped task preferred:

```text
12 photos need review
 ↓
Bulk review workspace
 ↓
Approve selected classifications
```

---

# 130. UF-ADM-VOP-009 — Review Stale Questionnaire

```text
Stale Draft/Preview
 ↓
Open Questionnaire
 ↓
Review dependency changes
 ↓
Rebuild
```

Historical Published Revision remains unchanged.

---

# 131. UF-ADM-VOP-010 — Handle QR Validation Error

```text
QR issue
 ↓
Open source Link / Questionnaire
 ↓
Correct target/configuration
 ↓
Regenerate
 ↓
Decode verify
 ↓
Resolve
```

---

# 132. UF-ADM-BBA-001 — Create BB Draft

```text
BB Assistant
 ↓
Choose task
 ↓
Select context
 ↓
Language / Tone / Length
 ↓
Generate
```

---

# 133. UF-ADM-BBA-002 — BB Draft Types

Supported conceptual task types:

```text
Biography
Portfolio Description
Project Description
Course Description
Cover Letter
Casting Response
Role Response
Questionnaire Text
Social Caption
Custom
```

---

# 134. UF-ADM-BBA-003 — Review Source Snapshot

Admin SHOULD be able to understand which profile/context facts were available when Draft was generated.

---

# 135. UF-ADM-BBA-004 — Edit AI Draft

Generated text becomes editable draft.

---

# 136. UF-ADM-BBA-005 — Apply Draft

```text
Draft
 ↓
Apply
 ↓
Insert into target editor
```

Does not publish.

---

# 137. UF-ADM-BBA-006 — Discard Draft

Draft remains historically traceable according to retention policy.

---

# 138. UF-ADM-BBA-007 — Stale Draft Warning

If relevant source data changed:

```text
Draft may be outdated
→ review / regenerate
```

before Apply where supported.

---

# 139. UF-ADM-THM-001 — Create Theme Draft

```text
Themes
 ↓
New Theme
 ↓
Manual or AI-assisted start
```

---

# 140. UF-ADM-THM-002 — Generate Theme With AI

```text
Theme
 ↓
Describe desired visual direction
 ↓
Theme AI creates structured proposal
 ↓
Draft Tokens / Hero / Decoration
```

---

# 141. UF-ADM-THM-003 — Edit Theme Tokens

Admin can manually modify structured theme configuration.

---

# 142. UF-ADM-THM-004 — Lock Theme Element

Locked elements MUST be excluded from AI changes where specified.

---

# 143. UF-ADM-THM-005 — Preview Theme

```text
Theme Draft
 ↓
Desktop
Tablet
Mobile
```

---

# 144. UF-ADM-THM-006 — Accessibility Validation

System automatically evaluates required contrast/accessibility rules.

---

# 145. UF-ADM-THM-007 — Publish Theme

```text
Draft
 ↓
Preview
 ↓
Accessibility Ready
 ↓
Human Publish
 ↓
Atomic activation
```

---

# 146. UF-ADM-THM-008 — Roll Back Theme

```text
Theme History
 ↓
Select previous revision
 ↓
Preview
 ↓
Activate/Roll back
```

according to revision model.

---

# 147. UF-ADM-THM-009 — Activate Temporary Theme

Admin specifies supported duration/context.

Temporary state remains visible in Admin.

---

# 148. UF-ADM-I18N-001 — Edit Localized Profile Content

```text
Profile
 ↓
Locale
 ↓
Edit localized text
 ↓
Save
```

Structured facts remain shared.

---

# 149. UF-ADM-I18N-002 — Create Translation Draft With BB Assistant

```text
Source text
 ↓
Create translation draft
 ↓
Review
 ↓
Apply
 ↓
Save localized field
```

---

# 150. UF-ADM-I18N-003 — Review Missing Translation Observation

Virtual Operator identifies critical localization gap.

Admin opens affected entity directly.

---

# 151. UF-ADM-I18N-004 — Verify International Name

Approved professional Latin spelling is edited explicitly, not auto-invented.

---

# 152. UF-ADM-I18N-005 — Preview Localized Public Journey

Admin MAY preview public page in selected locale.

---

# 153. UF-ADM-SOC-001 — Connect Social Account

Existing Instagram/TikTok OAuth workflow remains available.

---

# 154. UF-ADM-SOC-002 — Review Social Connection Health

```text
Social Publishing
 ↓
Accounts
 ↓
Connected / Expired / Error
```

---

# 155. UF-ADM-SOC-003 — Create Social Draft

Manual or BB-assisted caption.

---

# 156. UF-ADM-SOC-004 — Publish Social Post

Human-triggered action.

No automatic post from BB Assistant draft.

---

# 157. UF-ADM-SOC-005 — Handle Social Publish Failure

Failure shown in Publishing workspace and MAY appear as Operator exception.

---

# 158. UF-ADM-HLP-001 — Open Contextual Help

```text
Complex Admin screen
 ↓
Help
 ↓
Relevant article
```

Current work state should not be lost.

---

# 159. UF-ADM-HLP-002 — Create Support Ticket

Existing Help/Ticket capability preserved.

---

# 160. UF-ADM-ANL-001 — Open Analytics Overview

```text
Analytics
 ↓
Professional Visits
Questionnaires
Inquiries
Castings
Offers
Booked
```

where data available.

---

# 161. UF-ADM-ANL-002 — Review Conversion Funnel

```text
Relevant Visit
→ Questionnaire
→ Inquiry
→ Casting
→ Offer
→ Booked
```

---

# 162. UF-ADM-ANL-003 — Review Public Builder Usage

Admin sees aggregate:

- template selection;
- completion;
- frequently added/removed blocks;
- PDF generation.

---

# 163. UF-ADM-ANL-004 — Open Related Configuration

Example:

```text
Builder analytics insight
 ↓
Open Casting Template settings
```

Analytics must not change configuration directly.

---

# 164. UF-ADM-ANL-005 — Review Attribution

Admin sees meaningful source labels without unnecessary visitor PII.

---

# 165. UF-ADM-SYS-001 — Open System Status

```text
System
 ↓
Status
 ↓
Storage
PDF
AI
Notifications
Social
Background Processing
```

---

# 166. UF-ADM-SYS-002 — Open Service Degradation

```text
Degraded service
 ↓
Human-readable impact
 ↓
Diagnostic details
```

---

# 167. UF-ADM-SYS-003 — Retry Failed Background Operation

Where safe/idempotent:

```text
Failed job
 ↓
Retry
```

---

# 168. UF-ADM-SYS-004 — Preserve Core Without Optional Service

Example:

```text
AI unavailable
→ manual workflow available
```

---

# 169. UF-ADM-AUD-001 — View Entity History

```text
Entity
 ↓
History
 ↓
Audit filtered to entity
```

---

# 170. UF-ADM-AUD-002 — Review AI Action History

Admin can identify:

- AI capability;
- source context;
- output;
- human decision;
- applied state.

---

# 171. UF-ADM-AUD-003 — Review Publication Event

Audit shows who published, what revision, when.

---

# 172. UF-ADM-SEC-001 — Authenticate to Admin

```text
/admin
 ↓
Not authenticated
 ↓
Authentication
 ↓
Return to intended safe route
```

---

# 173. UF-ADM-SEC-002 — Session Expiration

```text
Editing
 ↓
Session expires
 ↓
Sensitive save blocked
 ↓
Re-authenticate
 ↓
Recover unsaved Draft where safely possible
```

---

# 174. UF-ADM-SEC-003 — Permission Denied

User cannot access unauthorized domain/action.

Client state is not authority.

---

# 175. UF-ADM-SEC-004 — Permission Changed During Session

Next protected action MUST revalidate current authorization.

---

# 176. Admin Save Flow

General source editing model:

```text
Open Entity
 ↓
Edit
 ↓
Validate
 ↓
Save
 ↓
Success
 ↓
Dependencies update
```

Save does not imply Publish unless explicitly defined otherwise.

---

# 177. Admin Publish Flow

General model:

```text
Draft
 ↓
Readiness
 ↓
Fix blockers
 ↓
Preview
 ↓
Explicit Publish
 ↓
Immutable/current revision created where applicable
 ↓
Public verification
```

---

# 178. Admin Archive Flow

```text
Active Entity
 ↓
Archive
 ↓
Dependency impact
 ↓
Confirm
 ↓
Archive
```

---

# 179. Admin Delete Flow

```text
Entity
 ↓
Delete
 ↓
Dependency check
 ↓
Show consequences
 ↓
Confirm if allowed
 ↓
Delete / soft delete
 ↓
Audit
```

---

# 180. Admin AI Review Flow

Universal semantic pattern:

```text
SOURCE
 ↓
AI OUTPUT
 ↓
EXPLANATION
 ↓
HUMAN REVIEW
 ↓
APPROVE / MODIFY / REJECT
 ↓
APPLY
```

---

# 181. AI Output MUST NOT bypass editing context

Example:

BB Assistant result should return into Biography editor, not create hidden independent public text.

---

# 182. Admin Concurrency Flow

```text
Admin opens entity version 5
 ↓
Entity changes to version 6 elsewhere
 ↓
Admin saves version 5
 ↓
Conflict detected
 ↓
No silent overwrite
```

---

# 183. Conflict Resolution

UI SHOULD offer:

- reload current;
- compare changes;
- copy unsaved content;
- manually reapply.

---

# 184. Stale Suggestion Flow

```text
AI suggestion based on v5
 ↓
Entity now v6
 ↓
Apply clicked
 ↓
System blocks/revalidates
```

---

# 185. Bulk Review Flow

For repetitive review:

```text
Grouped Task
 ↓
Open batch
 ↓
Review items
 ↓
Approve selected
 ↓
Handle failures individually
```

---

# 186. Bulk Edit Rule

Bulk action MUST show scope before commit.

---

# 187. Partial Bulk Failure

Example:

```text
12 selected
10 updated
2 failed
```

UI displays exact failures and retry route.

---

# 188. Admin Mobile Flow — Urgent Inquiry

```text
Mobile Notification
 ↓
Inbox
 ↓
Open Casting Invitation
 ↓
Review
 ↓
Set next action / create Casting
```

---

# 189. Admin Mobile Flow — Casting Deadline

```text
Dashboard
 ↓
Deadline
 ↓
Casting
 ↓
Update action/status
```

---

# 190. Admin Mobile Flow — Virtual Operator

Admin can:

- review;
- dismiss;
- defer;
- open entity.

Complex visual editing MAY remain desktop-oriented.

---

# 191. Admin Mobile Limitation

Fine-grained Emotional Grid crop editing MAY show:

```text
For precise editing, use tablet/desktop
```

only if mobile cannot reasonably provide safe precision.

Basic viewing/review remains possible.

---

# 192. Admin Error State — Validation

Field-level issue:

```text
Save
 ↓
Validation fails
 ↓
Show error near field
 ↓
Preserve other changes
```

---

# 193. Admin Error State — Server Save Failure

```text
Save
 ↓
Server failure
 ↓
Unsaved state remains visible
 ↓
Retry
```

Admin MUST know data were not saved.

---

# 194. Admin Error State — PDF Failure

Questionnaire Draft remains intact.

Retry does not require rebuilding content.

---

# 195. Admin Error State — AI Failure

Manual editor/workflow remains available.

---

# 196. Admin Error State — Notification Failure

Business source record remains saved.

---

# 197. Admin Error State — Derivative Failure

Original remains intact.

Current valid derivative remains where policy allows.

---

# 198. Admin Error State — Broken External Link

Link is not deleted automatically.

Admin chooses:

- fix;
- replace;
- disable;
- leave temporarily.

---

# 199. Admin Empty State — Portfolio

```text
Портфолио пока пусто.
[Добавить из медиатеки]
```

---

# 200. Admin Empty State — Questionnaire

```text
Нет подготовленных анкет.
[Создать анкету]
```

---

# 201. Admin Empty State — Castings

```text
Активных кастингов нет.
[Создать кастинг]
```

No unnecessary AI-only CTA.

---

# 202. Admin Empty State — Virtual Operator

A good empty state:

```text
Нет действий, требующих внимания.
```

not artificial tasks to fill screen.

---

# 203. Admin Unsaved Changes Flow

If autosave disabled:

```text
Edit
 ↓
Navigate away
 ↓
Unsaved changes warning
```

---

# 204. Autosave Flow

If enabled:

```text
Edit
 ↓
Autosave Draft
 ↓
Saved indicator
```

Never auto-Publish.

---

# 205. Public Preview Flow

Any publishable content SHOULD support:

```text
Admin Edit
 ↓
Preview
 ↓
Real projection
 ↓
Return
```

---

# 206. Draft Preview Security

Draft preview must not create unrestricted public URL unless explicitly protected.

---

# 207. Dependency Review Flow

Example Contact:

```text
Contact
 ↓
Change visibility
 ↓
System shows:
Affected new Questionnaires / Builder
 ↓
Save
```

Historical immutable revisions remain unaffected.

---

# 208. Dependency Invalidation Flow

```text
Source changes
 ↓
System computes dependent dynamic representations
 ↓
Invalidate / rebuild
 ↓
Admin sees status only if action needed
```

---

# 209. Readiness Flow

```text
Entity
 ↓
Readiness
 ↓
Errors / Warnings / Info
 ↓
Open blocker
 ↓
Fix
 ↓
Recalculate
```

---

# 210. Readiness MUST NOT equal approval

`Ready` means technical/business validation passed.

Human Publish still required where specified.

---

# 211. Admin Flow — New Photos to Published Portfolio

End-to-end:

```text
Upload Photos
 ↓
Metadata + Derivatives
 ↓
Operator Suggestions
 ↓
Review Classification
 ↓
Create Portfolio Items
 ↓
Choose Primary Close-Up / Full Body
 ↓
Preview
 ↓
Publish
 ↓
Public Profile verified
```

---

# 212. Admin Flow — Emotional Shoot to Published Grid

```text
Upload Photos
 ↓
Create Emotional Session
 ↓
Set Shooting Date
 ↓
Create Grid
 ↓
Choose exact dimensions
 ↓
Select exact photo count
 ↓
Crop each cell
 ↓
Confirm all cells
 ↓
Generate derivatives
 ↓
Preview
 ↓
Publish
```

---

# 213. Admin Flow — New Professional Inquiry to Casting

```text
Public Feedback
 ↓
Inbox
 ↓
Review
 ↓
Qualified professional context
 ↓
Create Casting
 ↓
Preserve Source Inquiry link
 ↓
Analyze Casting
 ↓
Prepare materials/questionnaire
```

---

# 214. Admin Flow — Casting to Booked Role

```text
Casting
 ↓
Qualified
 ↓
Materials / Self-Tape
 ↓
Audition
 ↓
Callback
 ↓
Offer
 ↓
Booked
 ↓
Create Project / Role Draft if appropriate
```

---

# 215. Admin Flow — BB-assisted Casting Response

```text
Casting / Inquiry
 ↓
BB Assistant
 ↓
Use confirmed facts
 ↓
Draft response
 ↓
Human edit
 ↓
Human Send
```

---

# 216. Admin Flow — Questionnaire Update After Profile Change

```text
Profile fact changed
 ↓
Dynamic Questionnaire Draft marked stale/recalculated
 ↓
Historical Published Revision unchanged
 ↓
Admin opens current Questionnaire
 ↓
Reviews change
 ↓
Publishes new Revision if desired
```

---

# 217. Admin Flow — Broken Video Impact

```text
Link validator detects failure
 ↓
Operator creates exception
 ↓
Admin opens Video
 ↓
Fixes URL
 ↓
Revalidation
 ↓
Future Questionnaire/QR readiness restored
```

---

# 218. Admin Flow — Public Builder Permission Revocation

```text
Admin disables item permission
 ↓
Save
 ↓
Public Builder projection invalidated
 ↓
New sessions cannot receive item
 ↓
Existing session generation revalidates
```

---

# 219. Admin Flow — Theme Change

```text
Theme Draft
 ↓
AI Proposal or Manual Edit
 ↓
Preview 3 viewports
 ↓
Accessibility
 ↓
Publish
 ↓
Verify site
```

---

# 220. Admin Flow — Localization Completion

```text
Operator detects missing EN text
 ↓
Open entity
 ↓
Manual or BB Translation Draft
 ↓
Human Review
 ↓
Save EN field
 ↓
Preview EN
```

---

# 221. Admin Flow — Social Publish

```text
Select media/content
 ↓
Write caption manually / BB Draft
 ↓
Review
 ↓
Select connected platform
 ↓
Publish
 ↓
Result stored
```

---

# 222. Admin Flow — System Degradation

```text
Service fails
 ↓
System Status updated
 ↓
Operator/notification if relevant
 ↓
Core workflows degrade gracefully
 ↓
Admin can inspect/retry
```

---

# 223. Admin Flow Security Principles

Every server mutation MUST revalidate:

- authentication;
- authorization;
- current entity;
- input;
- applicable business state.

---

# 224. Admin Flow Audit Principles

Significant actions SHOULD capture:

```text
Actor
Action
Entity
Timestamp
Relevant Before/After
Source Context
```

---

# 225. Mandatory Audit Examples

- Publish Questionnaire;
- change Contact visibility;
- set Primary photo;
- create Casting;
- update Opportunity stage;
- apply AI suggestion;
- publish Theme;
- delete/archive important content.

---

# 226. Admin Flow Performance Principles

Admin does not need instant loading of entire Media Library before dashboard/actions become available.

---

# 227. Async Work

Appropriate for:

- media derivatives;
- PDF generation;
- AI analysis;
- bulk processing;
- link checks;
- search indexing.

---

# 228. Async Status

User should see:

```text
Queued
Processing
Completed
Failed
```

where task duration makes status meaningful.

---

# 229. Navigation During Async Task

User MAY leave and continue work.

On return, status remains available.

---

# 230. Idempotency-Critical Admin Actions

Particularly:

- retry notification;
- PDF generation;
- Casting creation from Feedback;
- migration;
- background job retry;
- publish where repeated client submission possible.

---

# 231. Admin User Flow Anti-Pattern ADM-UF-AP-001

**Save = Publish**

---

# 232. ADM-UF-AP-002

**AI Suggestion = Fact**

---

# 233. ADM-UF-AP-003

**Feedback = Casting automatically**

---

# 234. ADM-UF-AP-004

**Notification sent before inquiry is stored**

---

# 235. ADM-UF-AP-005

**Delete source asset without dependency review**

---

# 236. ADM-UF-AP-006

**Historical Questionnaire mutates with Profile**

---

# 237. ADM-UF-AP-007

**Analytics automatically changes Template**

---

# 238. ADM-UF-AP-008

**AI publishes Theme**

---

# 239. ADM-UF-AP-009

**Photo AI silently sets Primary**

---

# 240. ADM-UF-AP-010

**Grid auto-generates altered face**

---

# 241. ADM-UF-AP-011

**Broken link is deleted automatically**

---

# 242. ADM-UF-AP-012

**Session expires and Admin loses long text with no recovery attempt**

---

# 243. ADM-UF-AP-013

**Concurrent edit silently overwrites newer state**

---

# 244. ADM-UF-AP-014

**Admin must copy same fact into Profile, Questionnaire and PDF separately**

---

# 245. ADM-UF-AP-015

**AI tools share one unrestricted generic context**

---

# 246. Flow Quality Gate

Каждый Admin Flow перед implementation должен иметь:

- [ ] Flow ID
- [ ] Trigger
- [ ] Actor
- [ ] Preconditions
- [ ] Required permission
- [ ] Main path
- [ ] Validation
- [ ] Human decision point
- [ ] Persistence behaviour
- [ ] Publication behaviour
- [ ] Async behaviour
- [ ] Failure behaviour
- [ ] Concurrency behaviour
- [ ] Audit
- [ ] Mobile requirements
- [ ] Related entities
- [ ] E2E test

---

# 247. E2E-ADM-001 — Daily Dashboard

```text
Open /admin
→ see critical exception
→ open exact entity
→ resolve
→ alert disappears/resolves
```

---

# 248. E2E-ADM-002 — Media to Portfolio

```text
Upload image
→ derivative generated
→ classify
→ create Portfolio Item
→ set Primary
→ publish
→ verify public
```

---

# 249. E2E-ADM-003 — Emotional Grid

```text
Create session
→ set date
→ create 4×4
→ select exactly 16 photos
→ confirm all cells
→ generate
→ publish
```

Finalization MUST fail with fewer/unconfirmed cells.

---

# 250. E2E-ADM-004 — Questionnaire Revision

```text
Edit Questionnaire
→ preview
→ publish
→ Revision N created
→ edit again
→ Revision N remains unchanged
```

---

# 251. E2E-ADM-005 — QR Update

```text
Change canonical URL
→ QR stale
→ regenerate
→ decode
→ target exact match
```

---

# 252. E2E-ADM-006 — Feedback to Casting

```text
Open Feedback
→ create Casting
→ verify bidirectional relation
→ Feedback preserved
```

---

# 253. E2E-ADM-007 — Casting AI

```text
Add source text
→ run AI
→ inspect extracted requirement
→ edit/confirm
→ no invented missing requirement
```

---

# 254. E2E-ADM-008 — BB Assistant

```text
Generate Biography Draft
→ edit
→ Apply
→ verify not published
→ Publish manually
```

---

# 255. E2E-ADM-009 — Contact Privacy

```text
Disable Public Builder visibility
→ attempt existing public session generation
→ contact excluded/rejected
```

---

# 256. E2E-ADM-010 — Notification Failure

```text
Feedback saved
→ WhatsApp fails
→ Inbox still contains inquiry
→ Operator alert exists
```

---

# 257. E2E-ADM-011 — Opportunity Pipeline

```text
Casting
→ Qualified
→ Audition
→ Callback
→ Offer
→ Booked
```

Only valid transitions accepted.

---

# 258. E2E-ADM-012 — Concurrency

```text
Open version 1
→ external update version 2
→ save version 1
→ conflict
→ no overwrite
```

---

# 259. E2E-ADM-013 — Theme Publish

```text
AI Theme Proposal
→ edit
→ preview all target viewports
→ accessibility
→ publish
```

AI cannot skip Publish confirmation.

---

# 260. E2E-ADM-014 — AI Outage

With AI unavailable:

- Profile editing works;
- Questionnaire editing works;
- Casting manual review works;
- Theme manual editing works.

---

# 261. E2E-ADM-015 — Legacy Feature Preservation

Verify new navigation/workflows retain:

- role-specific photos;
- performances;
- achievements;
- Help/Tickets;
- social publishing;
- multilingual editing.

---

# 262. Admin Flow Traceability

```text
BR
 ↓
CJM / VOP
 ↓
UF-ADM-*
 ↓
Screen
 ↓
Server Action
 ↓
FR
 ↓
AC
 ↓
E2E
```

---

# 263. Admin Flow Priority

## P0

```text
Dashboard
Profile
Media
Portfolio
Questionnaires
Contacts
Feedback
Castings
```

## P1

```text
Emotional
Public Builder Administration
Opportunity Pipeline
Virtual Operator
Skills/Languages
Projects
```

## P2

```text
BB Assistant
Theme AI
Analytics
Training
Achievements
Social
```

Priority here describes implementation/operational criticality, not permanent business importance.

---

# 264. Core Release Blocking Flows

Target-state product MUST NOT be considered administratively ready if broken:

- save Profile;
- manage Primary Portfolio;
- manage Contacts visibility;
- publish Questionnaire;
- process Feedback;
- create/manage Casting;
- protect privacy;
- recover from failed optional notification.

---

# 265. Automation vs Human Flow Matrix

| Task | System | Human |
|---|---|---|
| Thumbnail | Generate | None |
| Photo category | Suggest | Confirm |
| Primary photo | Candidate info | Select |
| Emotional crop | Suggest | Confirm |
| PDF | Render | Trigger/configure |
| QR | Generate/validate | Enable approved target |
| Casting requirements | Extract | Confirm |
| BB text | Draft | Edit/apply |
| Theme | Propose/validate | Publish |
| Opportunity stage | Validate | Business decision |
| Notification | Send/retry | Configure/review failure |

---

# 266. Admin Flow Context Rule

Every related flow SHOULD preserve enough context that user never has to ask:

```text
Why am I on this screen?
Which object caused this?
```

---

# 267. Admin Flow Source-of-Truth Rule

Editing a Projection SHOULD redirect to or clearly identify its Source when the projection itself is not editable.

---

# 268. Example

Public Quick View displays Language.

Admin clicking `Edit` from Preview should edit Language Source record, not a separate Quick View copy.

---

# 269. Admin Flow Reuse Rule

Existing data is linked/selected wherever possible.

Do not create duplicate copy workflows for:

- photos;
- contacts;
- skills;
- projects;
- video links.

---

# 270. Admin Flow Human-Control Rule

The following MUST always have explicit human-controlled decision:

```text
Professional fact confirmation
Public visibility
Primary media selection
Publication
Casting qualification
Opportunity business outcome
Professional reply/send
Theme publication
Destructive professional content action
```

---

# 271. Admin Flow Failure Containment Rule

Optional feature failure MUST remain bounded to its function.

Examples:

```text
AI failure
≠
Profile failure

WhatsApp failure
≠
Feedback loss

Analytics failure
≠
Admin navigation failure
```

---

# 272. Admin Flow Mobile Contract

Mobile MUST support at least:

```text
Dashboard review
Inbox review
Casting review
Opportunity review/update
Operator alerts
Basic Profile status
```

Complex precision editing MAY adapt or recommend larger viewport.

---

# 273. Admin Flow Accessibility Contract

All critical flows require:

- keyboard support;
- focus visibility;
- accessible validation;
- semantic controls;
- no drag-only mandatory action;
- non-color state labels.

---

# 274. Final Admin User Flow Map

```text
                         ADMIN ENTRY
                              ↓
                          DASHBOARD
                              ↓
           ┌──────────────────┼──────────────────┐
           │                  │                  │
       OPERATE              EDIT              ANALYZE
           │                  │                  │
   Inbox / Casting      Profile / Media      Analytics
   Opportunities        Portfolio            Audit
   Operator             Emotional            System
           │            Projects
           │            Questionnaires
           │            Contacts
           │                  │
           └──────────────┬───┘
                          ↓
                      VALIDATION
                          ↓
             ┌────────────┼────────────┐
             │            │            │
          ERROR         READY       AI REVIEW
             │            │            │
          FIX           PREVIEW      HUMAN
             │            │         DECISION
             └──────┬─────┴─────┬─────┘
                    ↓           ↓
                  SAVE       PUBLISH /
                    │         RESOLVE
                    └──────┬────┘
                           ↓
                     DEPENDENCIES
                           ↓
                   DERIVED PROCESSING
                           ↓
                        VERIFY
                           ↓
                         AUDIT
```

---

# 275. Definition of Admin User Flow Compliance

Административная часть соответствует настоящему документу, если:

1. Dashboard служит первым operational entry;
2. Admin может перейти из exception непосредственно к affected entity;
3. Source data вводятся один раз и переиспользуются;
4. Save и Publish различаются;
5. historical revisions не мутируют;
6. Media и Portfolio разделены;
7. Emotional Grid требует human confirmation;
8. Project, Casting, Feedback и Opportunity не смешиваются;
9. Public Builder имеет отдельное admin configuration flow;
10. Contact visibility управляется по независимым scopes;
11. Feedback сохраняется до notification;
12. Casting AI outputs требуют human review;
13. BB Assistant outputs остаются Draft;
14. Theme AI не публикует автоматически;
15. Virtual Operator управляет exceptions, а не профессиональными решениями;
16. concurrency не приводит к silent overwrite;
17. significant actions имеют Audit Trail;
18. optional services fail independently;
19. mobile поддерживает urgent operations;
20. legacy valuable capabilities сохраняются.

---

# 276. Финальный принцип

> **Admin User Flows должны превращать сложную внутреннюю систему в последовательность управляемых профессиональных решений: система сама выполняет проверяемую техническую работу, показывает зависимости, предупреждает о проблемах и готовит предложения; администратор управляет источниками данных, подтверждает профессиональный смысл, публикует материалы и фиксирует реальные бизнес-события. Ни один AI, background job или convenience-механизм не должен незаметно обходить эту границу.**