# ACHIEVEMENTS & AWARDS MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация professional achievements, awards, nominations, selections, evidence, provenance, visibility, Questionnaire/Builder integration и AI wording constraints

**Целевой файл:** `docs/modules/achievements.md`  
**Документ:** DOC-134  
**Статус:** ✅ Completed  
**Тип:** Module / Professional Source / Achievements / Awards / Recognition / Provenance

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/product-principles.md`
- `docs/domain/domain-model.md`
- `docs/domain/entity-relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions-and-history.md`
- `docs/architecture/projections.md`
- `docs/architecture/media-processing.md`
- `docs/database/database-architecture.md`
- `docs/database/data-dictionary.md`
- `docs/database/visibility-and-access.md`
- `docs/database/validation-and-constraints.md`
- `docs/database/deletion-and-retention.md`
- `docs/api/server-actions.md`
- `docs/api/contracts.md`
- `docs/api/errors.md`
- `docs/api/idempotency.md`
- `docs/modules/profile.md`
- `docs/modules/media-library.md`
- `docs/modules/projects.md`
- `docs/modules/training.md`
- `docs/modules/questionnaires.md`
- `docs/modules/public-questionnaire-builder.md`
- `docs/modules/bb-assistant.md`
- `docs/modules/virtual-operator.md`
- `docs/modules/marketing-analytics.md`

---

# 1. Назначение

Achievements & Awards Module является отдельным professional Source domain для подтверждённых профессиональных признаний актрисы.

Модуль предназначен для хранения и публикации:

- наград;
- побед;
- номинаций;
- официальных отборов;
- finalist/shortlist statuses;
- специальных упоминаний;
- профессиональных признаний;
- подтверждённых достижений другого типа.

---

# 2. Главная доктрина

> **Achievement хранит точный подтверждённый статус признания. `NOMINATED`, `OFFICIAL_SELECTION`, `FINALIST`, `WINNER` и другие статусы имеют разный профессиональный смысл и никогда не могут автоматически повышаться друг до друга. AI, BB Assistant, Analytics или VOP не имеют права превращать номинацию или selection в победу либо использовать wording `award-winning` без фактического подтверждения соответствующей награды.**

Canonical:

```text
Recognition Evidence
        ↓
Human-confirmed Achievement
        ↓
Professional Source
        ↓
Public / Questionnaire / Builder projections
```

---

# 3. Fundamental separation

```text
Achievement
≠ Project
≠ Role
≠ Training
≠ Skill
≠ Festival Event
≠ MediaAsset
≠ AI Claim
```

---

# 4. Achievement entity

Suggested entity:

```text
Achievement
```

Representative fields:

```text
id
profile_id
achievement_type
recognition_status
title
category?
work_title?
issuing_organization?
event_name?
event_edition?
year?
date?
location?
description?
evidence_status
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
is_highlighted
display_order
lifecycle_state
version
created_at
updated_at
archived_at?
```

---

# 5. Recognition status

Canonical baseline:

```text
WINNER
NOMINATED
FINALIST
SHORTLISTED
OFFICIAL_SELECTION
SPECIAL_MENTION
HONORABLE_MENTION
RECIPIENT
RECOGNIZED
OTHER_CONFIRMED
```

---

# 6. ACH-INV-001 — Recognition Status Is Explicit

Нельзя хранить размытый generic:

```text
AWARD
```

если известен более точный professional status.

---

# 7. Winner

`WINNER` означает подтверждённую победу в соответствующей категории/награде.

---

# 8. Nomination

`NOMINATED` означает номинацию.

Это не победа.

---

# 9. Official Selection

`OFFICIAL_SELECTION` означает официальный отбор работы/участника в программу/selection соответствующего события.

Он не означает:

- номинацию;
- finalist;
- winner.

---

# 10. Finalist

`FINALIST` означает подтверждённый выход в финал.

---

# 11. Shortlisted

`SHORTLISTED` используется только если организатор официально применяет такую стадию либо её эквивалент однозначно подтверждён.

---

# 12. Special Mention

Не равно winner, если организатор явно не классифицирует это как победную награду.

---

# 13. ACH-INV-002 — No Semantic Inflation

Forbidden transitions by wording:

```text
OFFICIAL_SELECTION → WINNER
NOMINATED → WINNER
FINALIST → WINNER
SPECIAL_MENTION → WINNER
```

без нового Human-confirmed evidence.

---

# 14. Achievement type

Отдельно от status.

Suggested controlled types:

```text
AWARD
FESTIVAL_RECOGNITION
INDUSTRY_RECOGNITION
PROFESSIONAL_COMPETITION
HONOR
GRANT
SCHOLARSHIP
OTHER
```

---

# 15. ACH-INV-003 — Type ≠ Status

Example:

```text
type = FESTIVAL_RECOGNITION
status = OFFICIAL_SELECTION
```

---

# 16. Title

`title` — название признания/категории/достижения.

Examples:

```text
Best Actress
Official Selection
Young Performers Competition
Special Jury Mention
```

---

# 17. Category

Если награда относится к категории:

```text
Best Actress
Best Supporting Actress
Best Ensemble
```

category should be stored explicitly where known.

---

# 18. Work title

Если recognition относится к конкретному:

- фильму;
- спектаклю;
- роли;
- проекту;

можно сохранить relation/reference.

---

# 19. Project relation

Preferred:

```text
Achievement
   └── optional Project/Role reference
```

rather than duplicating Project facts.

---

# 20. ACH-INV-004 — Project Facts Are Referenced, Not Duplicated

---

# 21. Work attribution

Achievement may concern:

```text
ACTRESS
PROJECT
ROLE
ENSEMBLE
OTHER
```

Suggested field:

```text
recognition_subject
```

---

# 22. Critical distinction

`PROJECT OFFICIAL_SELECTION` does not automatically mean **actress personally received an award**.

---

# 23. ACH-INV-005 — Project Recognition ≠ Personal Award

---

# 24. Example

If film containing actress is officially selected:

Correct:

```text
«Фильм X — официальный отбор Festival Y»
```

Potentially misleading:

```text
«Екатерина — лауреат Festival Y»
```

unless personal award evidence exists.

---

# 25. Issuing organization

Should identify official entity granting recognition where known.

Examples:

- festival organization;
- academy;
- theatre union;
- competition committee;
- school/industry body.

---

# 26. Event

Separate fields MAY include:

```text
event_name
event_edition
year
location
```

---

# 27. ACH-INV-006 — Unknown Event Detail Remains Null

Do not invent edition/year/location.

---

# 28. Date precision

Achievement may have:

```text
year only
full date
```

Store precision honestly.

---

# 29. ACH-INV-007 — Year-Only Evidence Is Not Converted to Arbitrary Date

---

# 30. Evidence

Achievement should support provenance/evidence.

Suggested related entity:

```text
AchievementEvidence
```

---

# 31. Evidence fields

```text
id
achievement_id
evidence_type
media_asset_id?
source_url?
description?
verification_state
created_at
verified_by?
verified_at?
```

---

# 32. Evidence types

Baseline:

```text
CERTIFICATE
DIPLOMA
OFFICIAL_PAGE
OFFICIAL_PROGRAM
SCREENSHOT
PUBLICATION
PHOTO
DOCUMENT
OTHER
```

---

# 33. ACH-INV-008 — Evidence Type Does Not Define Recognition Status

A certificate image does not itself prove WINNER unless its content does.

---

# 34. Evidence state

```text
UNVERIFIED
HUMAN_CONFIRMED
REJECTED
STALE_LINK
```

---

# 35. Professional Source authority

Achievement should become fully publishable only after Human confirmation according to product policy.

---

# 36. ACH-INV-009 — AI Evidence Parsing Cannot Confirm Achievement

---

# 37. External source URL

Must follow safe ProfessionalLink-like URL rules.

No:

- `javascript:`;
- local/private URLs;
- Admin routes;
- storage locators.

---

# 38. ACH-SEC-001 — Evidence URL Is Untrusted Input

---

# 39. External source checking

VOP may check availability.

It must not rewrite Achievement based on redirect/result automatically.

---

# 40. Lifecycle

Recommended:

```text
DRAFT
ACTIVE
ARCHIVED
```

---

# 41. ACTIVE

Means valid current professional Source record.

Does not automatically mean publicly visible.

---

# 42. ACH-INV-010 — Active ≠ Public

---

# 43. Visibility

Exact independent flags:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 44. ACH-INV-011 — Visibility Triplet Is Independent

Public:

```text
true
```

does not imply Builder:

```text
true
```

---

# 45. Server filtering

Hidden Achievement must be removed before public/Builder serialization.

---

# 46. ACH-SEC-002 — Visibility Is Server-Enforced

---

# 47. Highlight

```text
is_highlighted
```

Human-controlled.

---

# 48. Highlight does not imply visibility.

---

# 49. ACH-INV-012 — Highlight ≠ Public Permission

---

# 50. Display order

Explicit:

```text
display_order
```

---

# 51. Default sort fallback

May use:

```text
year/date descending
```

only when explicit display order absent/configured.

---

# 52. ACH-INV-013 — Analytics Cannot Change Achievement Ordering

---

# 53. Public site

Public projection may show:

- status;
- title/category;
- organization/event;
- year;
- linked Project/Role;
- selected evidence/media where appropriate.

---

# 54. Wording templates

Recommended status labels RU:

```text
WINNER → Победитель / Лауреат
NOMINATED → Номинация / Номинант
FINALIST → Финалист
SHORTLISTED → Шорт-лист
OFFICIAL_SELECTION → Официальный отбор
SPECIAL_MENTION → Специальное упоминание
HONORABLE_MENTION → Почётное упоминание
```

Exact UI wording can be localized separately.

---

# 55. ACH-INV-014 — Localized Label Must Preserve Canonical Status

---

# 56. “Лауреат”

Use only under explicit localization policy where underlying recognition semantics justify it.

Do not use as generic label for every nominee/selection.

---

# 57. Questionnaire integration

Achievement block is optional professional block.

---

# 58. Questionnaire can select only:

```text
same profile
ACTIVE
allow_in_admin_questionnaires = true
```

---

# 59. At publish

QuestionnaireRevision freezes:

```text
recognition status
title
category
organization/event
year/date representation
project/role display reference
description
ordering
```

---

# 60. ACH-QNR-001 — Questionnaire Freezes Exact Achievement Semantics

---

# 61. Source edit later

Does not rewrite old QuestionnaireRevision.

---

# 62. ACH-QNR-002 — Historical Questionnaire Does Not Upgrade Recognition

---

# 63. Builder integration

Builder receives only:

```text
allow_in_public_questionnaire_builder = true
```

current eligible achievements.

---

# 64. Visitor can include/exclude.

Cannot edit recognition status/title/year.

---

# 65. ACH-BLD-001 — Builder Cannot Modify Achievement Facts

---

# 66. Builder Generate

Revalidates:

- same profile;
- lifecycle;
- current Builder eligibility;
- access/media safety.

---

# 67. Snapshot

GenerationSnapshot freezes selected Achievement representation.

---

# 68. ACH-BLD-002 — Builder Snapshot Is Historical

---

# 69. Media

Achievement may reference supporting MediaAsset:

- certificate;
- event photograph;
- official badge/logo if rights permit.

---

# 70. Achievement media is context relation.

Do not overwrite global MediaAsset metadata solely for achievement use.

---

# 71. ACH-MED-001 — Achievement Media Context Is Separate

---

# 72. Evidence documents

Usually private/admin-only unless explicitly selected/approved for public display.

---

# 73. ACH-SEC-003 — Evidence Is Not Public by Default

---

# 74. Official logos

Use only where rights/licensing permit.

Existence online does not imply redistribution rights.

---

# 75. ACH-MED-002 — Logo Availability ≠ Usage Permission

---

# 76. BB Assistant

BB may use Human-confirmed Achievement as Source.

---

# 77. Allowed:

```text
«номинирована на...»
«фильм вошёл в официальный отбор...»
«стала финалисткой...»
«получила награду...»
```

only when exact status supports wording.

---

# 78. ACH-BB-001 — BB Wording Must Preserve Recognition Status

---

# 79. `award-winning`

Allowed only when confirmed achievement semantics support an actual award/winner status appropriate to the statement.

---

# 80. ACH-BB-002 — `award-winning` Requires Winner/Award Evidence

---

# 81. Forbidden example

Source:

```text
OFFICIAL_SELECTION
```

BB:

```text
award-winning actress
```

→ validation failure.

---

# 82. Personal vs Project wording

If recognition subject is PROJECT:

BB must not imply actress personally won unless separate personal recognition exists.

---

# 83. ACH-BB-003 — Recognition Subject Must Be Preserved in Copy

---

# 84. Casting AI

Casting AI may use confirmed Achievement only where relevant to requirement comparison.

---

# 85. It cannot:

- infer winner from nomination;
- infer acting quality score;
- create Achievement Source.

---

# 86. ACH-AI-001 — AI Cannot Promote Recognition Status

---

# 87. VOP

May detect:

```text
missing year
broken official evidence URL
duplicate candidate
public Achievement without Human confirmation
questionnaire references archived achievement
ambiguous migrated status
```

---

# 88. Safe actions

May:

- recheck link;
- rebuild derived projection;
- invalidate cache.

---

# 89. VOP cannot:

- change status;
- mark WINNER;
- merge duplicates;
- publish Achievement.

---

# 90. ACH-INV-015 — VOP Cannot Change Recognition Truth

---

# 91. Analytics

May measure:

```text
achievement_section_view
achievement_open
```

---

# 92. It cannot infer:

```text
this achievement is more important
```

as Source fact or auto-order content.

---

# 93. ACH-ANL-001 — Engagement ≠ Professional Significance

---

# 94. Search

Public Search may include public Achievement title/event depending product Search policy.

---

# 95. Hidden Achievement must be absent.

---

# 96. Admin Search can search all authorized current/history records.

---

# 97. ACH-SEC-004 — Search Respects Surface Visibility

---

# 98. SEO

Public Achievement may contribute structured page content.

But JSON-LD or metadata must preserve exact recognition status.

---

# 99. ACH-SEO-001 — SEO Cannot Upgrade Recognition Semantics

---

# 100. Duplicate detection

Possible duplicate factors:

```text
event
year
category
status
work
```

---

# 101. Duplicate detection produces candidate only.

---

# 102. ACH-INV-016 — Duplicate Candidate Is Not Auto-Merge

---

# 103. Corrections

If original status entered incorrectly:

Human can edit current Achievement.

---

# 104. Historical Questionnaire remains unchanged.

---

# 105. Significant status correction

Audit required.

---

# 106. ACH-INV-017 — Recognition Status Change Is High-Value Audit Event

---

# 107. Example

```text
NOMINATED → WINNER
```

requires explicit Human change and ideally evidence update.

---

# 108. Current revision/history

Achievement mutable current Source + revision/audit history according to DOC-064.

---

# 109. ACH-HST-001 — Current Correction Does Not Rewrite Historical Publication

---

# 110. Evidence deletion

Removing evidence does not necessarily delete Achievement.

Could move evidence state to insufficient/review depending policy.

---

# 111. ACH-INV-018 — Evidence Lifecycle and Achievement Lifecycle Are Related but Distinct

---

# 112. Privacy

Achievement generally professional/public-capable data.

But evidence may contain:

- certificate numbers;
- personal signatures;
- private documents;
- addresses/contact data.

---

# 113. Public projection must expose only approved fields.

---

# 114. ACH-SEC-005 — Evidence Document Privacy Is Field/Asset-Aware

---

# 115. Redaction

Public derivative may be redacted while private original evidence stays protected.

---

# 116. ACH-MED-003 — Public Evidence Derivative May Differ for Privacy, Not Recognition Meaning

---

# 117. Retention

Classes:

```text
Achievement Source
Achievement revision/history
Evidence metadata
Evidence media/documents
Derived public evidence
Questionnaire snapshots
```

---

# 118. Archive

Removes current public/new selection eligibility according to visibility/lifecycle.

Historical documents remain.

---

# 119. ACH-LIFE-001 — Archive Does Not Rewrite History

---

# 120. Restore

Restoring archived Achievement does not automatically restore old visibility/highlight settings if product policy requires review.

---

# 121. ACH-LIFE-002 — Restore Does Not Auto-Publish

---

# 122. Hard delete

Exceptional.

Must inspect references:

- QuestionnaireRevision;
- Builder snapshots;
- Project links;
- evidence;
- analytics IDs.

---

# 123. ACH-LIFE-003 — Purge Is Dependency-Aware

---

# 124. Migration

Legacy achievement text may be freeform.

Examples:

```text
«участник фестиваля»
«лауреат»
«номинация»
«official selection»
```

---

# 125. Migration must not guess stronger semantic status.

---

# 126. Canonical mapping only when unambiguous.

Examples:

```text
"Official Selection" → OFFICIAL_SELECTION
"Finalist" → FINALIST
"Nominee" → NOMINATED
"Winner" → WINNER
```

---

# 127. Ambiguous:

```text
"Festival participant"
"Awarded"
"Laureate"
"Recognition"
```

requires review depending source language/context.

---

# 128. ACH-MIG-001 — Ambiguous Legacy Recognition Requires Human Review

---

# 129. Legacy Project recognition

Must determine recognition subject.

Do not automatically assign to actress personally.

---

# 130. ACH-MIG-002 — Legacy Project Award ≠ Actor Award Automatically

---

# 131. Missing year

Remain null.

---

# 132. Missing category

Remain null.

---

# 133. Missing evidence

Do not fabricate evidence.

---

# 134. Migration state may use:

```text
MAPPED
REQUIRES_REVIEW
UNVERIFIED
```

---

# 135. ACH-MIG-003 — Migration Never Invents Evidence

---

# 136. Migration idempotency

Required.

---

# 137. Commands

Canonical:

```text
CreateAchievement
UpdateAchievement
UpdateAchievementRecognitionStatus
SetAchievementVisibility
SetAchievementHighlight
ReorderAchievements
AddAchievementEvidence
ConfirmAchievementEvidence
RejectAchievementEvidence
ArchiveAchievement
RestoreAchievement
```

---

# 138. Queries

```text
ListAchievements
GetAchievement
GetPublicAchievements
GetQuestionnaireEligibleAchievements
GetBuilderEligibleAchievements
GetAchievementEvidence
GetAchievementHistory
```

---

# 139. Create input

Conceptually:

```text
achievementType
recognitionStatus
title
category?
work/project reference?
organization?
event?
year/date?
description?
visibility
```

---

# 140. Client cannot set

```text
verified_by
verified_at
AI_confirmed
published_revision semantics
```

without authorized command flow.

---

# 141. ACH-SEC-006 — Confirmation Authority Is Server-Controlled

---

# 142. Update concurrency

Use:

```text
expectedVersion
```

---

# 143. ACH-INV-019 — Achievement Updates Use Optimistic Concurrency

---

# 144. Error taxonomy

At minimum:

```text
ACHIEVEMENT_NOT_FOUND
ACHIEVEMENT_WRONG_PROFILE
ACHIEVEMENT_TYPE_INVALID
ACHIEVEMENT_STATUS_INVALID
ACHIEVEMENT_DATE_INVALID
ACHIEVEMENT_PROJECT_NOT_FOUND
ACHIEVEMENT_PROJECT_WRONG_PROFILE
ACHIEVEMENT_EVIDENCE_NOT_FOUND
ACHIEVEMENT_EVIDENCE_INVALID
ACHIEVEMENT_EVIDENCE_UNSAFE
ACHIEVEMENT_VISIBILITY_INVALID
ACHIEVEMENT_STALE_VERSION
ACHIEVEMENT_ALREADY_ARCHIVED
ACHIEVEMENT_RESTORE_CONFLICT
ACHIEVEMENT_DUPLICATE_CANDIDATE
```

---

# 145. Audit

Audit at minimum:

```text
Achievement created
Recognition status changed
Evidence confirmed/rejected
Visibility changed
Highlight changed
Achievement archived/restored
```

---

# 146. Outbox events

Suggested:

```text
AchievementCreated
AchievementUpdated
AchievementRecognitionChanged
AchievementVisibilityChanged
AchievementArchived
AchievementRestored
```

---

# 147. Consumers

```text
Public Projection
Questionnaires
Builder
Search
VOP
Analytics
Cache
```

---

# 148. Anti-patterns

`ACH-AP-001`  
Official Selection displayed as Winner.

`ACH-AP-002`  
Nomination displayed as Award Winner.

`ACH-AP-003`  
Finalist displayed as Winner.

`ACH-AP-004`  
Special Mention always treated as Winner.

`ACH-AP-005`  
Project award treated as actress personal award automatically.

`ACH-AP-006`  
Film festival selection produces `award-winning actress`.

`ACH-AP-007`  
AI creates Achievement Source from article.

`ACH-AP-008`  
AI confidence confirms award.

`ACH-AP-009`  
BB invents award to improve Biography.

`ACH-AP-010`  
BB changes “nominated” to “winner”.

`ACH-AP-011`  
BB changes project recognition into personal recognition.

`ACH-AP-012`  
VOP changes ambiguous legacy achievement to WINNER.

`ACH-AP-013`  
Analytics auto-highlights most-clicked award.

`ACH-AP-014`  
Public visibility automatically enables Builder.

`ACH-AP-015`  
Highlight automatically enables public visibility.

`ACH-AP-016`  
Builder edits recognition status.

`ACH-AP-017`  
Questionnaire renderer reads current Achievement instead of frozen snapshot.

`ACH-AP-018`  
Current correction rewrites historical Questionnaire.

`ACH-AP-019`  
Evidence certificate becomes public automatically.

`ACH-AP-020`  
Certificate number exposed publicly unnecessarily.

`ACH-AP-021`  
Official logo used without rights review.

`ACH-AP-022`  
Broken evidence URL deletes Achievement automatically.

`ACH-AP-023`  
Evidence redirect rewrites Source automatically.

`ACH-AP-024`  
Missing year invented from event edition.

`ACH-AP-025`  
Year-only value converted to January 1.

`ACH-AP-026`  
Duplicate candidate auto-merged.

`ACH-AP-027`  
Archive deletes historical references.

`ACH-AP-028`  
Restore automatically republishes Achievement.

`ACH-AP-029`  
Hard delete cascades into immutable Questionnaire snapshot.

`ACH-AP-030`  
Legacy “participant” mapped to OFFICIAL_SELECTION without evidence.

`ACH-AP-031`  
Legacy “laureate” mapped to WINNER regardless source semantics.

`ACH-AP-032`  
Project title duplicated as editable Achievement string when relation exists.

`ACH-AP-033`  
Achievement used to infer Skill level.

`ACH-AP-034`  
Achievement used to infer Language level.

`ACH-AP-035`  
Achievement count becomes actress quality score.

`ACH-AP-036`  
Search exposes hidden Achievement.

`ACH-AP-037`  
SEO metadata says `award-winning` without winner evidence.

`ACH-AP-038`  
Client declares evidence verified.

`ACH-AP-039`  
Cross-profile Project attached to Achievement.

`ACH-AP-040`  
Current Achievement has no version/concurrency protection.

`ACH-AP-041`  
Evidence original altered to change wording.

`ACH-AP-042`  
AI-generated certificate/evidence accepted.

`ACH-AP-043`  
Training certificate automatically creates Award.

`ACH-AP-044`  
Competition participation automatically creates finalist status.

`ACH-AP-045`  
Recognition subject omitted, causing project award to look personal.

`ACH-AP-046`  
External media article becomes authoritative status without Human review.

`ACH-AP-047`  
Current source and evidence document disagree but UI silently chooses stronger wording.

`ACH-AP-048`  
Archived achievement selectable in new Questionnaire.

`ACH-AP-049`  
Builder receives Admin-only evidence URL.

`ACH-AP-050`  
Evidence URL accepts private/local network destination.

---

# 149. Core invariants

`ACH-INV-020`  
Achievement is independent professional Source domain.

`ACH-INV-021`  
Recognition status is explicit and semantically exact.

`ACH-INV-022`  
Achievement type and recognition status remain separate.

`ACH-INV-023`  
Nomination/Selection/Finalist/Special Mention never automatically become Winner.

`ACH-INV-024`  
Project recognition does not automatically become personal actress award.

`ACH-INV-025`  
Recognition subject is explicit where ambiguity is possible.

`ACH-INV-026`  
Unknown category/year/date/organization remains unknown.

`ACH-INV-027`  
Date precision is preserved honestly.

`ACH-INV-028`  
Evidence supports Source but does not independently define stronger semantics.

`ACH-INV-029`  
AI cannot Human-confirm evidence.

`ACH-INV-030`  
Evidence URLs use safe URL policies.

`ACH-INV-031`  
Active status does not imply public visibility.

`ACH-INV-032`  
Public/QNR/Builder visibility flags remain independent.

`ACH-INV-033`  
Hidden data is removed server-side.

`ACH-INV-034`  
Highlight is Human-controlled and independent from visibility.

`ACH-INV-035`  
Analytics never automatically reorders/highlights Achievements.

`ACH-INV-036`  
Public labels preserve canonical recognition semantics.

`ACH-INV-037`  
Questionnaire freezes exact Achievement semantics.

`ACH-INV-038`  
Current Achievement edits do not rewrite historical QuestionnaireRevision.

`ACH-INV-039`  
Builder can select but never edit Achievement facts.

`ACH-INV-040`  
Builder Generate revalidates current eligibility.

`ACH-INV-041`  
Evidence/media is private unless separately approved for exposure.

`ACH-INV-042`  
Official logos/media require usage rights where applicable.

`ACH-INV-043`  
BB wording must remain evidence-grounded.

`ACH-INV-044`  
`award-winning` wording requires genuine award/winner evidence.

`ACH-INV-045`  
BB preserves whether recognition belongs to actress, role, project or ensemble.

`ACH-INV-046`  
Casting AI cannot promote recognition status.

`ACH-INV-047`  
VOP cannot change recognition truth.

`ACH-INV-048`  
Engagement analytics is not professional significance.

`ACH-INV-049`  
Search/SEO preserve visibility and semantic accuracy.

`ACH-INV-050`  
Duplicate detection is advisory only.

`ACH-INV-051`  
Recognition status change is audited.

`ACH-INV-052`  
Current correction never rewrites frozen historical publication.

`ACH-INV-053`  
Evidence lifecycle and Achievement lifecycle remain distinct.

`ACH-INV-054`  
Privacy-sensitive evidence fields/assets are protected independently.

`ACH-INV-055`  
Archive does not rewrite historical publications.

`ACH-INV-056`  
Restore does not auto-publish.

`ACH-INV-057`  
Hard purge is dependency-aware.

`ACH-INV-058`  
Migration never guesses stronger recognition semantics.

`ACH-INV-059`  
Legacy Project recognition is not automatically mapped as personal award.

`ACH-INV-060`  
Migration does not invent missing evidence or dates.

`ACH-INV-061`  
Achievement mutation is version/concurrency protected.

`ACH-INV-062`  
All Source, visibility, evidence and semantic-upgrade controls are server-enforced.

---

# 150. E2E-ACH-001 — Winner

Admin creates confirmed WINNER achievement.

Public wording accurately represents Winner.

---

# 151. E2E-ACH-002 — Nomination

NOMINATED displays nomination wording.

No winner wording appears.

---

# 152. E2E-ACH-003 — Official Selection

Project recognition is OFFICIAL_SELECTION.

UI does not label actress award winner.

---

# 153. E2E-ACH-004 — Finalist

FINALIST remains finalist in Profile, Questionnaire and BB copy.

---

# 154. E2E-ACH-005 — BB semantic guard

BB attempts:

```text
award-winning actress
```

with only NOMINATED Source.

Fact validation blocks/warns.

---

# 155. E2E-ACH-006 — Project award

Achievement subject = PROJECT.

Biography may say film received recognition, not actress personally.

---

# 156. E2E-ACH-007 — Personal award

Achievement subject = ACTRESS, status = WINNER.

Personal award wording allowed.

---

# 157. E2E-ACH-008 — Unknown year

Year absent.

System displays no invented year.

---

# 158. E2E-ACH-009 — Year precision

Source says 2025 only.

No artificial `2025-01-01`.

---

# 159. E2E-ACH-010 — Evidence

Certificate uploaded as private evidence.

Not public automatically.

---

# 160. E2E-ACH-011 — Evidence confirmation

Human confirms evidence.

Achievement confirmation changes through authorized workflow.

---

# 161. E2E-ACH-012 — Client fake verification

Browser sends `verified=true`.

Ignored/rejected.

---

# 162. E2E-ACH-013 — External evidence URL

Safe HTTPS official page accepted.

Private-network/local URL rejected.

---

# 163. E2E-ACH-014 — Visibility

Public=false, Questionnaire=true.

Achievement absent from public site but selectable in Admin Questionnaire.

---

# 164. E2E-ACH-015 — Builder permission

Builder=false.

Public Builder never receives record.

---

# 165. E2E-ACH-016 — Highlight

Highlighted but Public=false.

Still absent from public page.

---

# 166. E2E-ACH-017 — Questionnaire snapshot

Questionnaire R4 freezes NOMINATED.

---

# 167. E2E-ACH-018 — Current correction

Current Achievement later corrected to WINNER.

R4 remains NOMINATED.

---

# 168. E2E-ACH-019 — New Questionnaire

New R5 uses current confirmed WINNER status.

---

# 169. E2E-ACH-020 — Builder

Visitor includes Builder-eligible Achievement.

Cannot alter status/title/year.

---

# 170. E2E-ACH-021 — Builder revocation

Eligibility removed before Generate.

Generate rejects selected achievement.

---

# 171. E2E-ACH-022 — VOP broken evidence

Official source URL broken.

VOP flags issue but does not alter status.

---

# 172. E2E-ACH-023 — Analytics

Achievement viewed frequently.

No automatic highlight/order change.

---

# 173. E2E-ACH-024 — Duplicate candidate

Two similar event/year records detected.

No automatic merge.

---

# 174. E2E-ACH-025 — Cross-profile relation

Project from another profile cannot attach to Achievement.

---

# 175. E2E-ACH-026 — Concurrent edit

Two admins edit same version.

Stale update rejected.

---

# 176. E2E-ACH-027 — Archive

Archived Achievement disappears from new public/QNR/Builder selection according to policy.

Historical QNR unaffected.

---

# 177. E2E-ACH-028 — Restore

Restored Achievement does not become public merely because it once was.

---

# 178. E2E-ACH-029 — Privacy evidence

Certificate contains private identifier.

Public projection uses approved redacted derivative or omits evidence.

---

# 179. E2E-ACH-030 — Migration official selection

Exact legacy `Official Selection` maps safely.

---

# 180. E2E-ACH-031 — Migration ambiguous

Legacy `Festival participant` is marked review-required.

Not mapped to selection/winner.

---

# 181. E2E-ACH-032 — Migration laureate ambiguity

Ambiguous localized “лауреат” is not blindly mapped to WINNER without source semantics.

---

# 182. E2E-ACH-033 — Project recognition migration

Legacy film award stays project recognition unless personal award evidence exists.

---

# 183. E2E-ACH-034 — AI disabled

Full Achievement CRUD/visibility/QNR functionality operates without AI.

---

# 184. E2E-ACH-035 — Search

Hidden Achievement absent from Public Search.

---

# 185. E2E-ACH-036 — SEO

SEO does not produce `award-winning` from nomination-only Source.

---

# 186. E2E-ACH-037 — Evidence deletion

Evidence removed.

Achievement remains according to lifecycle/review policy; historical Questionnaire unchanged.

---

# 187. E2E-ACH-038 — VOP duplicate

VOP recommends review.

No merge/status update.

---

# 188. E2E-ACH-039 — BB personal/project distinction

Project official selection appears in cover letter as project recognition, not personal award.

---

# 189. E2E-ACH-040 — Full flow

Human enters festival recognition  
→ selects `OFFICIAL_SELECTION`  
→ links Project  
→ adds official evidence  
→ confirms record  
→ enables Public/QNR  
→ public projection shows exact wording  
→ Questionnaire freezes exact status  
→ BB Biography uses project-selection wording without `award-winning`.

---

# 190. Architecture diagram

```text
Evidence / Human Input
        │
        ▼
   Achievement
        │
 ┌──────┼───────────────┐
 ▼      ▼               ▼
Public  Questionnaire   Builder
        │
        ▼
 immutable snapshot

BB / AI may read confirmed facts.
They cannot promote status.
```

---

# 191. Semantic hierarchy diagram

```text
OFFICIAL_SELECTION
NOMINATED
SHORTLISTED
FINALIST
SPECIAL_MENTION
WINNER

These are distinct states.

There is NO automatic upward conversion.
```

---

# 192. Personal vs project recognition

```text
Project X
   │
   └── Official Selection
          │
          ▼
Project Recognition

Actress
   │
   └── Best Actress Winner
          │
          ▼
Personal Recognition

Do not conflate them.
```

---

# 193. Questionnaire history diagram

```text
Achievement v2
status = NOMINATED
     ↓
QuestionnaireRevision 5
frozen = NOMINATED

Achievement v3
status corrected = WINNER

Revision 5 remains NOMINATED.
New Revision may use WINNER.
```

---

# 194. AI authority diagram

```text
Confirmed Achievement
        ↓
BB / Casting AI
        ↓
Read-only professional context
        ↓
Draft / recommendation

AI cannot:
- change status
- verify evidence
- create Winner fact
- publish Achievement
```

---

# 195. Quality gate

Перед implementation должны быть подтверждены:

- [ ] Achievement Source entity;
- [ ] separate achievement type/status;
- [ ] controlled recognition statuses;
- [ ] recognition subject;
- [ ] Project/Role references;
- [ ] organization/event/year/date fields;
- [ ] date precision handling;
- [ ] evidence model;
- [ ] Human evidence confirmation;
- [ ] safe evidence URL policy;
- [ ] lifecycle;
- [ ] visibility triplet;
- [ ] highlight/order;
- [ ] server-side public/QNR/Builder projections;
- [ ] Questionnaire frozen semantics;
- [ ] Builder immutable snapshot semantics;
- [ ] public/private evidence handling;
- [ ] BB semantic-grounding rules;
- [ ] `award-winning` restriction;
- [ ] project-vs-personal recognition guard;
- [ ] Casting AI read-only boundary;
- [ ] VOP recommendation-only boundary;
- [ ] Analytics read-only boundary;
- [ ] Search/SEO semantic accuracy;
- [ ] optimistic concurrency;
- [ ] audit;
- [ ] archive/restore;
- [ ] dependency-aware purge;
- [ ] migration ambiguity handling;
- [ ] deterministic E2E coverage.

---

# 196. Acceptance criteria

`AC-ACH-001`  
Achievement is an independent professional Source domain.

`AC-ACH-002`  
Recognition status uses a controlled canonical taxonomy.

`AC-ACH-003`  
Achievement type and recognition status remain distinct.

`AC-ACH-004`  
Nomination, Official Selection, Finalist, Shortlisted, Special Mention and Winner are never automatically interchangeable.

`AC-ACH-005`  
Project recognition does not automatically become a personal actress award.

`AC-ACH-006`  
Recognition subject is explicit where needed.

`AC-ACH-007`  
Missing year/date/category/organization data remains unknown rather than inferred.

`AC-ACH-008`  
Date precision is preserved honestly.

`AC-ACH-009`  
Achievement evidence and Source facts have explicit provenance.

`AC-ACH-010`  
AI cannot Human-confirm evidence or create authoritative achievement status.

`AC-ACH-011`  
Evidence URLs follow safe URL policy.

`AC-ACH-012`  
Achievement lifecycle and visibility are independent.

`AC-ACH-013`  
Public/QNR/Builder permissions are separate server-enforced flags.

`AC-ACH-014`  
Highlight/order do not grant visibility.

`AC-ACH-015`  
Public localization preserves canonical recognition semantics.

`AC-ACH-016`  
QuestionnaireRevision freezes exact Achievement representation/status.

`AC-ACH-017`  
Current Achievement edits never rewrite historical Questionnaire revisions.

`AC-ACH-018`  
Public Builder can select eligible Achievement but cannot edit facts.

`AC-ACH-019`  
Builder Generate revalidates current eligibility.

`AC-ACH-020`  
Evidence documents/media remain private unless explicitly approved for public presentation.

`AC-ACH-021`  
BB Assistant preserves exact recognition semantics.

`AC-ACH-022`  
`award-winning` wording requires confirmed Winner/award evidence.

`AC-ACH-023`  
BB distinguishes personal recognition from Project/Role recognition.

`AC-ACH-024`  
Casting AI cannot elevate or create Achievement status.

`AC-ACH-025`  
VOP can detect issues but cannot modify recognition truth.

`AC-ACH-026`  
Analytics cannot auto-highlight/reorder Achievements.

`AC-ACH-027`  
Public Search and SEO respect visibility and exact status.

`AC-ACH-028`  
Duplicate detection remains recommendation-only.

`AC-ACH-029`  
Recognition-status changes are auditable and concurrency-protected.

`AC-ACH-030`  
Evidence lifecycle does not silently overwrite Achievement lifecycle.

`AC-ACH-031`  
Archive removes current exposure/new eligibility without rewriting history.

`AC-ACH-032`  
Restore does not auto-publish or restore visibility implicitly.

`AC-ACH-033`  
Hard purge discovers dependent snapshots/media/references.

`AC-ACH-034`  
Migration does not guess stronger recognition semantics from ambiguous legacy text.

`AC-ACH-035`  
Legacy Project recognition is not automatically attributed personally to the actress.

`AC-ACH-036`  
Migration never invents missing evidence, dates or categories.

`AC-ACH-037`  
All visibility, evidence, semantic-status, concurrency and historical-snapshot boundaries have deterministic E2E coverage.

---

# 197. Финальная доктрина

> **Achievements & Awards Module хранит профессиональное признание как отдельный Human-confirmed Source с точным статусом, субъектом признания и provenance. `OFFICIAL_SELECTION`, `NOMINATED`, `FINALIST`, `SPECIAL_MENTION` и `WINNER` имеют разные профессиональные значения и никогда не повышаются автоматически. Признание проекта не превращается в личную награду актрисы, а wording `award-winning` допустим только при подтверждённом award/winner evidence. Public Site, Admin Questionnaire и Public Builder используют независимые visibility permissions; Questionnaire/Builder snapshots замораживают точную историческую семантику. Evidence остаётся private-by-default и не получает public access автоматически. BB Assistant, Casting AI, VOP и Analytics могут читать подтверждённые Achievement facts в пределах своей задачи, но не могут менять статус, подтверждать evidence, повышать recognition semantics, публиковать Achievement или использовать engagement как меру профессиональной значимости.**