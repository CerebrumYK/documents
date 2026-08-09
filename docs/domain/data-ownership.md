# DATA OWNERSHIP

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная модель владения данными, Source of Truth и authority boundaries

**Целевой файл:** `docs/domain/data-ownership.md`  
**Документ:** DOC-063  
**Статус:** ✅ Completed  
**Тип:** Domain / Data Ownership / Source of Truth / Authority

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/admin-navigation.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/forms-and-validation.md`
- `docs/ux/ui-states.md`
- `docs/domain/domain-model.md`
- `docs/domain/entity-relationships.md`
- `docs/domain/state-machines.md`

---

# 1. Назначение документа

Настоящий документ определяет каноническую модель владения данными в продукте.

Он фиксирует:

1. какой Domain является Source of Truth;
2. какие сущности writable;
3. какие сущности read-only;
4. какие representations являются projections;
5. какие данные являются snapshots;
6. какие данные derived;
7. какие данные принадлежат AI;
8. какие данные принадлежат System;
9. какие действия требуют Human authority;
10. какие данные Public Visitor может создавать;
11. какие данные Public Visitor никогда не может изменять;
12. как избежать дублирования между Profile, Questionnaire, Builder, Casting, AI и Analytics;
13. как работает ownership при импорте;
14. как работает ownership при миграции;
15. как сохраняется provenance;
16. как определяются mutation boundaries.

---

# 2. Главный принцип

> **Каждый профессиональный факт должен иметь одного канонического владельца. Все остальные модули могут ссылаться на этот факт, включать его в конфигурацию, создавать его snapshot, анализировать его или отображать его — но не должны становиться альтернативным writable Source of Truth.**

---

# 3. Ownership IDs

Используется prefix:

```text
OWN-*
```

Примеры:

```text
OWN-PROFILE-001
OWN-MEDIA-001
OWN-QST-001
OWN-AI-001
```

---

# 4. Основные категории владения

Каждый persistent datum MUST относиться к одной из категорий:

```text
MASTER
CONFIGURATION
SNAPSHOT
DERIVED
OPERATIONAL
AI_OUTPUT
AUDIT
TEMPORARY
```

---

# 5. MASTER

Авторитетный профессиональный факт.

Examples:

```text
ActorProfile.location
PortfolioItem.category
ActorLanguageProficiency.level
Project.title
Role.name
Training.course
Contact.value
ProfessionalLink.canonical_url
```

MASTER изменяется только через owning Domain.

---

# 6. CONFIGURATION

Определяет использование MASTER data.

Examples:

```text
Questionnaire selected blocks
Questionnaire selected Projects
show_qr
Builder Template limits
display order
Primary markers
visibility permissions
```

Configuration не является новым профессиональным фактом.

---

# 7. SNAPSHOT

Зафиксированное историческое состояние данных.

Examples:

```text
QuestionnaireRevision
BuilderGenerationSnapshot
CastingAnalysisRevision source snapshot
AIDraft source_snapshot
```

Snapshot исторически фиксирован и не редактирует MASTER.

---

# 8. DERIVED

Результат вычисления или рендера.

Examples:

```text
thumbnail
PDF
QR
Emotional Grid composite
search index
readiness
ProfileMatchResult
```

Derived data MUST NOT использоваться как единственный источник исходной истины.

---

# 9. OPERATIONAL

Данные реального workflow.

Examples:

```text
Feedback
Casting
Opportunity
Notification
SupportTicket
```

Они являются авторитетными в собственном operational Domain.

---

# 10. AI_OUTPUT

Результат AI, который может быть:

- extraction;
- recommendation;
- draft;
- proposal.

AI_OUTPUT по определению не является подтверждённым MASTER fact.

---

# 11. AUDIT

Append-only history существенных действий.

Audit описывает изменение, но не является текущим Source of Truth.

---

# 12. TEMPORARY

Временная session/configuration data.

Examples:

```text
PublicBuilderSession
unsaved UI draft
temporary generation context
```

Temporary data не должна становиться MASTER автоматически.

---

# 13. Source of Truth Model

Каноническая схема:

```text
                 MASTER DOMAIN DATA
                        │
        ┌───────────────┼────────────────┐
        ↓               ↓                ↓
  CONFIGURATION      PROJECTION        SNAPSHOT
        │               │                │
        ↓               ↓                ↓
    DERIVED UI       PUBLIC VIEW       HISTORICAL
        │
        ↓
     ARTIFACT

AI:
MASTER/SOURCE
    ↓
AI OUTPUT
    ↓
HUMAN DECISION
    ↓
OPTIONAL MASTER MUTATION
```

---

# 14. OWN-INV-001 — One Writable Owner

У одного профессионального факта MUST быть ровно один writable owning Domain.

---

# 15. Example — Language Level

Canonical owner:

```text
ActorLanguageProficiency
```

Read-only users:

```text
Public Profile
Casting Quick View
Questionnaire
Public Builder
PDF
Casting AI
Search
```

No module besides Skills & Languages ownership workflow may directly write:

```text
English B2 → C1
```

---

# 16. Example — Project Title

Owner:

```text
Project
```

Questionnaire MAY include it.

BB Assistant MAY draft a Project Description.

Casting AI MAY reference it.

Public Search MAY index it.

None of those modules owns Project title.

---

# 17. Example — Contact Number

Owner:

```text
Contact
```

Questionnaire only references/snapshots it.

Builder only receives it when permitted.

Feedback sender details do not overwrite it.

---

# 18. Example — Video URL

Owner:

```text
ProfessionalLink
```

or approved Media/Public URL domain depending resource type.

Questionnaire `show_qr=true` does not own the URL.

QRArtifact certainly does not own it.

---

# 19. ActorProfile Ownership

`ActorProfile` owns core identity-level facts.

Canonical examples:

```text
professional_name
professional_title
location/base
profile-level professional parameters
primary locale
public slug identity
biography source context
```

---

# 20. ActorProfile Does Not Own Everything

The following MUST remain separate:

```text
Portfolio
Media
Projects
Roles
Training
Skills
Languages
Contacts
Questionnaires
Castings
```

---

# 21. Biography Ownership

Canonical Biography belongs to Profile content domain.

Questionnaire MAY snapshot/reuse Biography or shorter selected form.

BB Assistant may generate draft for Biography.

AI draft is not Biography until Human Apply.

---

# 22. Portfolio Ownership

`PortfolioItem` owns:

```text
professional category
portfolio-specific caption
display order
primary marker
portfolio lifecycle
```

---

# 23. Media Ownership

`MediaAsset` owns:

```text
source binary identity
mime
original file metadata
checksum
storage reference
```

Portfolio MUST NOT alter these source facts.

---

# 24. Portfolio Crop Ownership

If Main Portfolio needs presentation crop:

crop configuration belongs to Portfolio presentation relation/configuration or derivative settings.

It MUST NOT alter immutable MediaAsset original.

---

# 25. Emotional Session Ownership

`EmotionalSession` owns:

```text
shooting date
session identity
session description
session lifecycle
```

---

# 26. Emotional Grid Ownership

`EmotionalGrid` owns:

```text
dimensions
selected cells
cell ordering
crop settings
primary marker
finalization state
```

---

# 27. Grid Artifact Ownership

Composite image belongs Derived Artifact layer.

It MUST NOT become independently editable source.

---

# 28. Project Ownership

`Project` owns:

```text
project identity
title
project type
production context
project dates
project description
featured marker
lifecycle
```

---

# 29. Role Ownership

`Role` owns:

```text
role name
role description
role-specific professional context
```

Role MUST reference Project.

---

# 30. Casting Cannot Write Project History

A Casting may contain:

```text
potential project name
potential role name
```

but these belong Casting context.

They MUST NOT mutate Project/Role professional history until actual Booked workflow + Human creation.

---

# 31. Public Builder Cannot Write Project History

Visitor-entered:

```text
Project: Film X
Role: Anna
```

is Builder document context.

Never Project MASTER.

---

# 32. Training Ownership

`Training` owns training history.

Legacy Project `Other` records migrate into Training after semantic validation.

---

# 33. Achievement Ownership

`Achievement` owns award/certification/recognition facts.

Projects may reference them, but do not own them.

---

# 34. Skills Ownership

`ActorSkill` owns confirmed skill proficiency.

---

# 35. AI Skill Suggestion Boundary

AI MAY say:

```text
Possible skill: Horse Riding
```

That is a suggestion.

Until Human confirms:

```text
no ActorSkill mutation
```

---

# 36. Language Ownership

`ActorLanguageProficiency` owns:

```text
language
level
CEFR
native status
```

---

# 37. AI Language Boundary

AI MUST NOT derive confirmed CEFR from CV prose, Casting source or video without explicit human confirmation.

---

# 38. Professional Link Ownership

`ProfessionalLink` owns canonical external URL + professional description.

---

# 39. QR Ownership

`QRArtifact` owns only generated QR representation metadata.

It does not own target URL.

---

# 40. Contact Ownership

`Contact` owns:

```text
contact value
contact type
relationship
visibility permissions
```

---

# 41. Contact Visibility Ownership

Visibility scopes belong Contact/configuration domain:

```text
public site
prepared questionnaires
public builder
```

Questionnaire cannot override Contact into a context where Contact does not permit use.

---

# 42. Questionnaire Ownership

`QuestionnaireDefinition` owns:

```text
questionnaire identity
type
configuration
block selection
item selection
display order
primary marker
```

---

# 43. Questionnaire Does Not Own Profile Facts

Questionnaire does NOT own:

- name;
- height;
- language level;
- Project title;
- Skill level;
- Contact value.

It references them in Draft and snapshots them in Revision.

---

# 44. Questionnaire Revision Ownership

`QuestionnaireRevision` owns historical snapshot truth of that document.

This is not current Profile truth.

---

# 45. Two Valid Truths May Coexist

Example:

```text
Current Profile:
English — C1

Questionnaire Revision #4:
English — B2
```

Both are correct:

- C1 = current MASTER fact;
- B2 = historical snapshot fact.

---

# 46. Historical Snapshot Is Authoritative for History

When rendering Revision #4, system MUST use revision snapshot.

Not live C1.

---

# 47. Public Builder Ownership

`PublicBuilderSession` owns only:

```text
temporary selection
output locale
temporary casting labels
temporary ordering/configuration
```

---

# 48. Builder Never Owns Profile Content

Even if visitor selects only 2 Projects, it does not modify global Project visibility or Profile content.

---

# 49. Builder Generation Snapshot Ownership

Snapshot owns:

```text
exact valid package data at generation time
```

for that generated document.

It does not own current Profile.

---

# 50. Feedback Ownership

`Feedback` owns inbound professional communication.

Canonical examples:

```text
sender
message
reply contact
feedback type
submission timestamp
source context
workflow status
```

---

# 51. Feedback Sender Contact ≠ Actor Contact

Critical invariant.

Incoming:

```text
Producer phone
```

belongs Feedback/business counterparty context.

It MUST NOT enter actress's Contact directory automatically.

---

# 52. Feedback Message Immutability

Original submitted message SHOULD be preserved.

Admin internal notes are separate.

---

# 53. Feedback Status Ownership

Inbox/Feedback domain owns:

```text
NEW
READ
IN_REVIEW
ACTION_REQUIRED
RESOLVED
```

Casting does not own Feedback status.

---

# 54. Casting Ownership

`Casting` owns:

```text
casting identity
deadline
operational casting context
source relations
casting workspace state
```

---

# 55. Casting Source Ownership

`CastingSource` owns original received/pasted casting material.

AI output never replaces original Source.

---

# 56. Casting Requirement Ownership Layers

Must distinguish:

```text
Raw source statement
AI-extracted candidate requirement
Human-confirmed casting requirement
```

---

# 57. AI Extraction Owner

`CastingAnalysisRevision` / Casting AI domain owns extracted candidate.

---

# 58. Human-Confirmed Requirement Owner

Casting domain owns confirmed interpretation after human decision.

It remains a Casting fact, not Actor Profile fact.

---

# 59. Profile Match Ownership

`ProfileMatchResult` is derived from:

```text
confirmed Casting Requirement
+
current/snapshotted Profile facts
```

It is Derived state.

---

# 60. Profile Match Does Not Own Skill

If match says:

```text
Driving — Match
```

that does not create or modify ActorSkill.

---

# 61. Casting Recommendation Ownership

Casting AI owns recommendation output.

Human approval may produce a target configuration mutation.

---

# 62. Opportunity Ownership

`Opportunity` owns business progression after Casting becomes tracked professional opportunity.

Canonical owner of:

```text
stage
next action
deadline/action context
outcome
```

---

# 63. Casting Does Not Own Opportunity Stage

Casting may display it as projection.

Writable owner:

```text
Opportunity
```

---

# 64. Dashboard Does Not Own Opportunity Stage

Dragging a Dashboard/Kanban card invokes Opportunity transition.

It does not modify presentation state only.

---

# 65. Project Created From Opportunity

Once Project Draft is created:

```text
Project domain owns Project
```

Opportunity retains provenance relation but does not own Project content.

---

# 66. Notification Ownership

`Notification` owns notification intent.

`NotificationDelivery` owns per-channel delivery status.

---

# 67. Notification Does Not Own Source Workflow

Example:

```text
Feedback = NEW
WhatsApp = FAILED
```

Feedback domain remains authority for Feedback.

---

# 68. In-App Read State

Notification center may own:

```text
read/unread
```

for Notification.

This is separate from Feedback `READ`.

---

# 69. Feedback READ vs Notification READ

Critical distinction:

```text
Notification read
!=
Feedback reviewed
```

---

# 70. BB Assistant Ownership

`AIDraft` owns generated/editable draft history.

---

# 71. BB Assistant Target Ownership

After Apply:

target domain owns resulting content.

Example:

```text
AIDraft biography
→ Apply
→ Profile Draft biography
```

AIDraft remains provenance/history.

---

# 72. AIDraft APPLIED Does Not Retain Write Authority

After Apply, later target edits happen in target domain.

BB Assistant does not overwrite them automatically.

---

# 73. Virtual Operator Ownership

VOP owns:

```text
Observation
Recommendation
AutomationExecution record
```

---

# 74. VOP Does Not Own Professional Fact

Example:

```text
VOP detects old Video link
```

ProfessionalLink remains owner.

---

# 75. VOP Safe Automation

For deterministic derivative actions VOP/System may own execution:

```text
regenerate thumbnail
retry link check
rebuild preview
reindex search
```

---

# 76. VOP Semantic Mutation Boundary

VOP may not directly set:

```text
Primary photo
Skill level
Contact visibility
Booked state
Published Questionnaire
```

without required Human decision.

---

# 77. Theme Ownership

`ThemeRevision` owns visual configuration.

---

# 78. Theme Cannot Own Public Content Order Outside Guardrails

Theme may control approved presentation layout.

It cannot redefine professional domain facts or remove mandatory P0 functionality.

---

# 79. Theme AI Ownership

`ThemeProposal` owns AI-proposed design configuration only.

---

# 80. Analytics Ownership

`AnalyticsEvent` owns observation of interaction/event.

It does not own:

- Feedback;
- Casting;
- Opportunity;
- Questionnaire;
- Profile.

---

# 81. Business Event Truth

For conversion:

```text
Feedback persisted
```

is authoritative.

Analytics:

```text
professional_inquiry_submitted
```

is secondary observation.

---

# 82. Analytics Loss Must Not Lose Business Truth

If analytics service fails:

```text
Feedback/Casting/Opportunity remain intact
```

---

# 83. Audit Ownership

`AuditEvent` owns historical record of mutation.

It does not own current value.

---

# 84. Audit Cannot Be Used as Live State Store

Current value should not require replaying arbitrary audit log unless architecture deliberately implements event sourcing, which is NOT current product requirement.

---

# 85. Support Ticket Ownership

SupportTicket owns system/product support dialogue.

It must remain independent of Professional Feedback.

---

# 86. Social Publishing Ownership

`SocialPost` owns internal prepared external-post content.

`SocialPublishAttempt` owns provider attempt/result.

---

# 87. Social Post Caption from BB

Flow:

```text
AIDraft
→ Human Apply
→ SocialPost.caption
```

SocialPost becomes writable owner of final selected caption.

---

# 88. Public User Authority Model

Public visitor MAY create:

```text
PublicBuilderSession
Feedback
FeedbackAttachment
AnalyticsEvent indirectly
temporary casting-context labels
```

---

# 89. Public User Cannot Mutate

Public visitor MUST NOT mutate:

```text
ActorProfile
PortfolioItem
MediaAsset
EmotionalSession
EmotionalGrid
Project
Role
Training
Achievement
ActorSkill
LanguageProficiency
Contact
ProfessionalLink
Prepared Questionnaire
Casting
Opportunity
Theme
```

---

# 90. Public Builder Is Selection Authority Only

Public visitor has authority to decide:

```text
what allowed items to include in their temporary package
```

not whether item itself is true or public.

---

# 91. Admin Human Authority

Authorized Admin is principal authority for professional MASTER data.

Examples:

- profile facts;
- classifications;
- visibility;
- publication;
- casting interpretation;
- Opportunity outcomes.

---

# 92. System Authority

System may authoritatively write deterministic technical data:

```text
timestamps
hashes
processing state
derived artifact metadata
retry count
link validation result
session expiry
```

---

# 93. AI Authority

AI is authoritative only for recording:

```text
what AI generated/extracted/recommended
```

It is NOT authoritative for truth of the professional subject.

---

# 94. Example

AI can authoritatively store:

```text
"The model extracted age requirement: 25–30"
```

as AI output.

It cannot authoritatively declare:

```text
"The casting definitely requires age 25–30"
```

until source evidence/human confirmation supports it.

---

# 95. AI Four-Layer Model

Canonical:

```text
SOURCE FACT
   ↓
AI EXTRACTION / GENERATION
   ↓
AI RECOMMENDATION
   ↓
HUMAN DECISION
```

No layer may silently replace previous semantics.

---

# 96. OWN-AI-001 — AI Output Requires Provenance

Persistent AI output SHOULD include:

```text
source snapshot/reference
model metadata
prompt/version
generated_at
capability/task
```

---

# 97. OWN-AI-002 — Human Modification Preserved

If human edits AI output:

system SHOULD distinguish:

```text
generated_text
edited/current_text
```

where history matters.

---

# 98. OWN-AI-003 — Apply Is Auditable

Applying AI suggestion to MASTER/configuration MUST create provenance/audit.

---

# 99. OWN-AI-004 — Rejected AI Has No Mutation Rights

Rejected output does not modify target.

---

# 100. OWN-AI-005 — Stale AI Cannot Silently Apply

Source version mismatch requires:

- warning;
- revalidation;
- or regeneration;

depending action.

---

# 101. Source Authority Matrix

| Data | Writable Owner |
|---|---|
| Actor identity | ActorProfile |
| Biography | ActorProfile content |
| Original media | MediaAsset |
| Portfolio category | PortfolioItem |
| Primary photo marker | Portfolio |
| Emotional shooting date | EmotionalSession |
| Emotional crop/grid | EmotionalGrid |
| Project fact | Project |
| Role fact | Role |
| Training | Training |
| Achievement | Achievement |
| Skill level | ActorSkill |
| Language/CEFR | ActorLanguageProficiency |
| Professional URL | ProfessionalLink |
| Contact value | Contact |
| Contact visibility | Contact |
| Questionnaire config | QuestionnaireDefinition/Draft |
| Historical questionnaire | QuestionnaireRevision |
| Builder selection | PublicBuilderSession |
| Inquiry | Feedback |
| Casting source/context | Casting/CastingSource |
| Confirmed casting requirement | Casting domain |
| Opportunity stage | Opportunity |
| Notification delivery | NotificationDelivery |
| BB text draft | AIDraft |
| VOP issue | VOPObservation |
| Theme configuration | ThemeRevision |
| Social post draft | SocialPost |
| Support case | SupportTicket |
| Audit history | AuditEvent |

---

# 102. Read-Only Projection Matrix

| Projection | Source |
|---|---|
| Public Home | Profile + related public data |
| Casting Quick View | Profile/Portfolio/Skills/Languages |
| Search Result | Search projection of published data |
| Portfolio Filter Result | Portfolio |
| Project Card | Project/Role |
| Questionnaire HTML | Definition/Revision |
| PDF | Revision/Snapshot |
| QR | Canonical URL |
| Builder Preview | Builder Session + current eligible source |
| Profile Match | Requirement + Profile |
| Dashboard Card | underlying operational entity |
| Funnel | Feedback/Casting/Opportunity events |

---

# 103. Projection Mutation Rule

A projection MUST NOT offer direct mutation unless interaction is routed to owning Domain.

Example:

Dashboard:

```text
[Change Opportunity Stage]
```

must invoke Opportunity transition service.

Dashboard does not write its own card state.

---

# 104. Quick View Mutation Rule

Public Quick View is always read-only.

Admin Quick View MAY link to owning entity editor but does not store duplicate facts.

---

# 105. Search Ownership

Search owns only index/query representation.

---

# 106. Search Result Click

Opens underlying Source entity/projection.

No editing inside index.

---

# 107. Search Reindex

Changing Source invalidates/rebuilds index.

Changing index never changes Source.

---

# 108. Readiness Ownership

Readiness is owned by deterministic validation/rules engine for target capability.

---

# 109. Readiness Cannot Be Manually Edited

No field:

```text
readiness = 100
```

editable by Admin.

---

# 110. Readiness Inputs

Derived from:

```text
source facts
configuration
visibility
link/QR validity
processing state
required content
```

---

# 111. Readiness Override

If product later permits an override, it MUST be explicit exceptional business object with:

- reason;
- actor;
- expiry/scope;
- audit.

Not silent manual score editing.

---

# 112. Visibility Ownership Model

Visibility should remain with source/configuration closest to protected datum.

Examples:

```text
Contact visibility → Contact
Project publication visibility → Project
Builder eligibility → source entity/visibility policy
```

---

# 113. Questionnaire Cannot Escalate Visibility

Canonical rule:

```text
Questionnaire requests Contact
+
Contact permission denies context
=
Questionnaire cannot use Contact
```

---

# 114. Public Builder Cannot Escalate Visibility

Client selection cannot override:

```text
allow_in_public_questionnaire_builder = false
```

---

# 115. AI Cannot Escalate Visibility

AI recommendation may identify private evidence internally if policy allows, but cannot make it public or insert it into public output without appropriate human/configuration authorization.

---

# 116. Theme Cannot Escalate Visibility

Theme rendering never affects data authorization.

---

# 117. Analytics Cannot Escalate Visibility

Most-clicked private asset does not become public.

---

# 118. Snapshot Ownership Model

Snapshots own their historical values after creation.

But snapshot creation authority derives from owning workflow.

---

# 119. Questionnaire Snapshot

Created by Questionnaire publication.

---

# 120. Builder Snapshot

Created by Public Builder generation.

---

# 121. Casting Analysis Snapshot

Created by analysis execution.

---

# 122. AI Source Snapshot

Created by AI draft generation.

---

# 123. Snapshot Mutation Rule

Existing snapshot MUST NOT be modified to reflect later Source changes.

---

# 124. Snapshot Redaction Exception

Privacy/legal/security policy MAY require controlled historical redaction.

Such action MUST:

- be exceptional;
- be auditable;
- preserve redaction metadata;
- not masquerade as ordinary source update.

---

# 125. Derived Artifact Ownership

Derived artifact's owner is generation subsystem + source relation.

But semantic authority remains Source/Snapshot.

---

# 126. Example PDF

PDF bytes are authoritative for:

```text
what was generated in that artifact
```

but Profile/Questionnaire source remains authority for domain facts outside that historical artifact.

---

# 127. Example QR

QR bytes are authoritative only as representation.

Canonical URL remains ProfessionalLink/route authority.

---

# 128. Example Emotional Composite

Grid artifact reflects finalized Grid.

Individual source photos remain Media authority.

---

# 129. Generated Artifact Manual Editing

Derived artifacts MUST NOT be manually edited outside source/config workflow and then treated as canonical.

Example prohibited:

```text
open PDF in editor
change language level
reupload as new canonical questionnaire
```

without controlled import/reconciliation.

---

# 130. Import Ownership Model

Any imported source follows:

```text
IMPORT SOURCE
 ↓
CANDIDATE DATA
 ↓
HUMAN REVIEW
 ↓
MASTER MUTATION
```

---

# 131. Imported Document Is Not Truth by Itself

PDF/CV/casting document MAY contain stale or incorrect information.

Import cannot blindly overwrite Profile.

---

# 132. Candidate Data Ownership

Import subsystem owns extracted candidate.

Target Domain owns confirmed value after Apply.

---

# 133. Migration Ownership

Migration process is temporary transformation authority.

It must not invent professional meaning.

---

# 134. Migration Example — Training

Legacy:

```text
Project.type = Other
```

candidate:

```text
Training
```

If semantics ambiguous:

```text
Human review required
```

---

# 135. Migration Audit

Important semantic migration SHOULD record:

```text
source record
target record
mapping decision
migration version
timestamp
```

---

# 136. Data Copying Rule

Data MAY be copied only when semantics require snapshot/history or external delivery.

Ordinary reuse SHOULD use references/projections.

---

# 137. Legitimate Copy Examples

```text
QuestionnaireRevision snapshot
BuilderGenerationSnapshot
notification delivery payload
Audit delta
external SocialPost payload
```

---

# 138. Illegitimate Copy Examples

```text
Questionnaire.project_title writable copy
Builder.contact_phone permanent copy
Casting.actor_skill copied and editable
Dashboard.opportunity_stage duplicate field
```

---

# 139. Denormalized Read Models

Architecture MAY use denormalized read models for performance.

But they MUST be:

```text
derived
rebuildable
non-authoritative
```

---

# 140. Read Model Drift

If read model conflicts with MASTER:

MASTER wins.

System should rebuild/repair projection.

---

# 141. Cache Ownership

Cache owns no business truth.

---

# 142. Cache Failure

Cache loss must not cause business data loss.

---

# 143. Public Data Aggregation Layer

Owns assembly logic, not professional facts.

---

# 144. Public Aggregation Inputs

Only already-authorized eligible Source/Projection data.

---

# 145. Public Aggregation Output

Read-only DTO/projection.

Never trusted back as write payload for source mutation.

---

# 146. API Ownership Rule

Write API/action MUST be organized around owning Domain.

Prefer:

```text
updateContactVisibility()
updateActorLanguage()
publishQuestionnaire()
transitionOpportunity()
```

over generic:

```text
updateEntity(type, id, payload)
```

for critical business mutations.

---

# 147. Generic Admin Mutation Anti-Pattern

A generic endpoint that can mutate arbitrary fields across domains bypasses ownership boundaries.

Avoid.

---

# 148. Form Ownership

Admin form should submit to owning Domain service.

Example:

```text
Questionnaire UI displaying Project
```

does not mean Project fields become editable there.

---

# 149. Contextual Editing

If cross-domain edit is needed, UI SHOULD:

```text
open Project editor
```

or invoke explicit Project Domain action.

---

# 150. Nested Editing Risk

Avoid modal inside Questionnaire that silently edits Contact MASTER without communicating broader impact.

If supported:

- clearly identify source edit;
- show impact;
- use Contact mutation service.

---

# 151. Human Decision Ownership

Human decision belongs Domain where decision has meaning.

Examples:

```text
Grid Cell confirmation → Emotional domain
Casting Requirement confirmation → Casting domain
Booked → Opportunity domain
Publish → target publishable domain
```

---

# 152. Human Decision Record

Significant Human Decision SHOULD be retained through:

- state transition;
- decision record;
- audit;

depending domain.

---

# 153. Public Visitor Submission Ownership

Once Feedback successfully persists:

Feedback domain owns message.

Visitor cannot subsequently edit the professional inquiry unless a future explicit edit workflow exists.

---

# 154. Feedback Original vs Admin Notes

Original inbound message:

```text
visitor-owned content / immutable business input
```

Admin notes:

```text
internal operational data
```

Must remain separated.

---

# 155. Casting Source Original vs Normalized Data

Original source must remain available even after normalization/extraction.

---

# 156. AI Normalization Boundary

AI can normalize:

```text
"tomorrow at 15:00"
```

to structured candidate deadline only when context/date semantics are known.

Human confirmation may still be required when ambiguous.

Original source retained.

---

# 157. Opportunity External Facts

External outcomes such as callback/offer are entered/confirmed by Admin from real communication.

They are not inferred from website analytics.

---

# 158. Analytics Authority Boundary

Analytics may indicate:

```text
Casting CTA clicked
```

but cannot infer:

```text
Casting invitation received
```

until Feedback/operational data supports it.

---

# 159. Source-Aware Funnel

Canonical mapping:

```text
Page View
→ Analytics

Professional Inquiry
→ Feedback

Casting
→ Casting

Opportunity stage
→ Opportunity

Booked
→ Opportunity terminal state
```

---

# 160. Dashboard Ownership

Dashboard owns no independent operational facts.

It reads:

```text
Feedback
Casting
Opportunity
VOP
Notifications
System state
```

---

# 161. Dashboard Action Routing

Action:

```text
Resolve Inquiry
```

must call Feedback workflow.

Action:

```text
Book Opportunity
```

must call Opportunity transition.

---

# 162. Notification Center Ownership

Notification Center owns display/read state of Notifications.

It does not replace source entities.

---

# 163. Inbox Ownership

Inbox is UX workspace over Feedback.

Feedback is domain authority.

---

# 164. Castings Workspace Ownership

Workspace is UX surface over Casting domain.

---

# 165. Opportunities Workspace Ownership

Pipeline/List are projections over Opportunity domain.

---

# 166. Media Library Ownership

Media Library UI manages MediaAsset source and derivatives.

Portfolio UI manages Portfolio classification.

---

# 167. Media Library Must Not Own Portfolio Semantics

A MediaAsset can display its Portfolio usage, but classification remains PortfolioItem.

---

# 168. Portfolio Must Not Own File Processing

If derivative fails, processing belongs Media domain.

---

# 169. Emotional Grid Editor Ownership

Owns Grid configuration only.

It must not perform destructive original photo edits.

---

# 170. Questionnaire PDF Generator Ownership

Owns render/generation operation and artifact.

It does not own Questionnaire content.

---

# 171. QR Generator Ownership

Owns QR generation/validation.

It does not choose arbitrary URL.

---

# 172. Link Health Checker Ownership

Owns health observation.

It does not archive link automatically.

---

# 173. Search Indexer Ownership

Owns index.

It does not determine public visibility independently.

Eligibility is supplied from authoritative projection rules.

---

# 174. SEO Ownership

SEO projection/config may own metadata such as:

```text
meta description
canonical route
structured presentation metadata
```

but should reuse Source facts.

---

# 175. Localization Ownership

Canonical Source entity owns identity.

Localized content records own approved translation/localized narrative.

---

# 176. Localized Copy vs Source Fact

Example:

```text
Project ID 42
RU title
EN title
```

all belong same Project semantic identity.

---

# 177. Translation AI

AI-generated translation is AIDraft/candidate.

Localized approved value becomes target Domain content only after Human Apply.

---

# 178. Public Builder Locale

Session owns selected output locale.

It does not alter `ActorProfile.primary_locale`.

---

# 179. Social Connection Ownership

`SocialAccountConnection` owns integration state.

ProfessionalLink owns public profile URL if displayed separately.

---

# 180. OAuth Token Ownership

Credentials belong secure credential store/integration infrastructure.

They MUST NOT live as ordinary public/domain text fields.

---

# 181. Social Publish Result

Provider result belongs SocialPublishAttempt.

It does not modify Project/Portfolio facts.

---

# 182. Help Article Ownership

Help domain owns instructional product content.

It does not own professional actress content.

---

# 183. Support Diagnostic Ownership

SupportTicket MAY include references to source entities/errors.

It must not own those target entities.

---

# 184. Audit Actor Ownership

AuditEvent identifies actor but does not become identity provider authority.

---

# 185. AdminUser Ownership

Identity/auth system owns authentication identity.

Domain modules consume authorized actor context.

---

# 186. Security Role Ownership

Authorization domain owns permissions.

Feature modules MUST NOT determine security from hidden menu items/client state.

---

# 187. UI State Ownership

UI owns ephemeral interaction state:

```text
modal open
selected tab
hover
loading indicator
temporary unsaved control state
```

These are not automatically domain data.

---

# 188. Unsaved Form Draft

Before persistence, client/form state may own temporary user changes.

Once saved, target Domain becomes authoritative.

---

# 189. Autosave Ownership

Successful autosave writes target Draft domain.

UI must wait for server confirmation before treating state as authoritative.

---

# 190. Conflict Ownership

Server authoritative current version wins when client draft is stale.

User draft is preserved for recovery but cannot silently overwrite.

---

# 191. Current Version Ownership

Owning Domain controls version/concurrency field.

---

# 192. Mutation Preconditions

Before any source mutation:

```text
1. identity
2. authorization
3. target ownership
4. current version
5. validation
6. business rule
7. persistence
8. audit/side effects
```

---

# 193. Ownership Through Delete

Deleting entity does not transfer ownership to referencing module.

Example:

Questionnaire using Project does not become owner of Project snapshot except historical Revision context.

---

# 194. Archive Ownership

Owning Domain performs archive.

Referencing domains react through invalidation/eligibility.

---

# 195. Example — Project Archived

Owner:

```text
Project domain
```

Consequences:

```text
Public Projection removes current Project
Builder eligibility updates
Questionnaire Draft readiness updates
Search reindexes
Historical Revision stays unchanged
```

---

# 196. Example — Contact Visibility Revoked

Owner:

```text
Contact domain
```

Consequences:

```text
Public Profile projection updates
Builder sessions revalidate
Questionnaire Draft may block
Historical snapshot follows revision/privacy policy
```

---

# 197. Example — Media Processing Failure

Owner:

```text
Media processing domain
```

Portfolio remains owner of classification.

Questionnaire may see readiness impact.

---

# 198. Example — Opportunity Booked

Owner:

```text
Opportunity domain
```

Creates optional action:

```text
Create Project Draft
```

After creation, Project domain becomes owner of new professional record.

---

# 199. Data Ownership and Deletion Requests

Detailed retention later.

Canonical principle:

privacy deletion/redaction requests must be routed to owning Domain and propagate to dependent projections/derived artifacts.

---

# 200. Derived Cleanup Propagation

When source removed legitimately:

system SHOULD clean/invalidate:

```text
cache
search index
QR
preview
temporary Builder projection
```

according to rules.

---

# 201. Historical Data Exception

Historical business records may have lawful/product retention requirements.

Deletion does not automatically mean erase every historical trace.

DOC-094 defines exact policy.

---

# 202. Ownership and Referential Integrity

Database FK can enforce structure.

But FK alone does not define authority.

---

# 203. Example

Questionnaire FK to Project means:

```text
relation valid
```

It does NOT mean:

```text
Questionnaire can update Project
```

---

# 204. Ownership and Service Boundaries

Architecture SHOULD expose domain services matching ownership boundaries.

Conceptually:

```text
ProfileService
MediaService
PortfolioService
EmotionalService
ProjectService
ContactService
QuestionnaireService
FeedbackService
CastingService
OpportunityService
```

Names illustrative.

---

# 205. Cross-Domain Orchestration

Complex use case MAY use application/orchestration service.

Example:

```text
Create Casting From Feedback
```

Orchestrator:

```text
read Feedback
→ create Casting
→ link provenance
→ audit
```

It does not transfer Feedback ownership into Casting service.

---

# 206. Transaction Boundary

Cross-domain operation should preserve invariants transactionally where possible.

---

# 207. Example — Set Primary Questionnaire

Must atomically:

```text
old primary = false
new primary = true
```

within Questionnaire ownership.

---

# 208. Example — Create Casting From Feedback

Must avoid state:

```text
Casting created
but source link lost
```

where transactionality permits.

---

# 209. Ownership and Domain Events

Owning Domain publishes events after committed change.

---

# 210. Example

```text
ContactVisibilityChanged
```

emitted by Contact domain.

Consumers:

```text
Public Projection
Builder
Questionnaire Readiness
Search
VOP
```

---

# 211. Consumers Cannot Rewrite Source Event

Consumers react.

They do not change meaning of original Contact mutation.

---

# 212. Ownership and Event Ordering

Side effects must not make dependent representation look committed before source transaction commits.

---

# 213. Ownership and Background Jobs

Background jobs MAY operate derived data.

For MASTER mutations, background jobs require explicit previously authorized command and proper concurrency validation.

---

# 214. Example Allowed

```text
User confirms Theme publish for 19:00
→ scheduled authorized activation job
```

---

# 215. Example Not Allowed

```text
Nightly AI decides to replace Primary Close-Up
```

---

# 216. Ownership and Idempotency

Idempotency ownership belongs mutation operation.

Examples:

```text
FeedbackService → Feedback submission key
QuestionnaireService → publish request key
NotificationService → delivery key
```

---

# 217. Ownership of Canonical URL

Every QR/linkable public route MUST have a canonical URL owner.

Examples:

```text
ProfessionalLink → external URL
Router/public entity → canonical internal route
```

QR generation accepts URL from owner.

---

# 218. No QR-Specific URL Copy

Prohibited:

```text
qr_url field manually diverging from link URL
```

---

# 219. Ownership of Shooting Date

For Emotional Portfolio:

```text
EmotionalSession.shooting_date
```

For Portfolio Session if separate future model exists, that session owns its own date.

Upload timestamp is never fallback owner of Shooting Date.

---

# 220. Ownership of Media Caption

General media technical label MAY belong MediaAsset.

Professional Portfolio caption belongs PortfolioItem.

Role caption/context may belong RoleMedia relation.

Do not force one universal caption field for all contexts.

---

# 221. Context-Specific Metadata Ownership

If metadata meaning depends on relationship, it belongs relationship/configuration.

Example:

```text
Questionnaire display order
```

belongs Questionnaire item configuration.

---

# 222. Global Metadata Ownership

If value is universally true:

```text
Project title
```

belongs Project.

---

# 223. Ownership Test

For any field ask:

> **If this value changes, should every context see the same new value?**

If YES:

likely Source entity.

If NO because context-specific:

likely relation/configuration.

If NO because historical:

snapshot.

If computed:

derived.

---

# 224. Example — Project Display Order

Public Projects page order may be Project presentation config.

Questionnaire order differs.

Therefore Questionnaire order is relation-specific.

---

# 225. Example — Link Description

Canonical professional description may belong ProfessionalLink.

Questionnaire-specific abbreviated label MAY belong Questionnaire configuration if explicitly supported.

---

# 226. Override Policy

Contextual override SHOULD be used sparingly.

It must not create misleading alternative professional facts.

---

# 227. Allowed Override Example

```text
Questionnaire label:
"Video Intro"
```

for ProfessionalLink described more fully elsewhere.

---

# 228. Prohibited Override Example

Source:

```text
English B2
```

Questionnaire override:

```text
English C1
```

---

# 229. AI Draft Ownership Test

AI-generated field starts as AI_OUTPUT.

Only after Human Apply does a target-domain Draft own the new selected content.

---

# 230. Casting AI Ownership Test

Extracted fact remains AI_OUTPUT until confirmed.

---

# 231. Theme AI Ownership Test

Proposal remains AI_OUTPUT until converted to ThemeRevision Draft.

---

# 232. Virtual Operator Ownership Test

Recommendation remains VOP data until approved action mutates target Domain.

---

# 233. Ownership Anti-Pattern OWN-AP-001

**Questionnaire has independent editable copy of Actor height**

---

# 234. OWN-AP-002

**Builder can edit Skill levels**

---

# 235. OWN-AP-003

**Casting Source edits Actor Profile**

---

# 236. OWN-AP-004

**AI extraction stored directly as confirmed Casting requirement**

---

# 237. OWN-AP-005

**Profile Match result stored as Actor skill truth**

---

# 238. OWN-AP-006

**Dashboard card owns Opportunity stage**

---

# 239. OWN-AP-007

**Notification read means Feedback reviewed**

---

# 240. OWN-AP-008

**QR owns independent target URL**

---

# 241. OWN-AP-009

**PDF re-import silently overwrites Profile**

---

# 242. OWN-AP-010

**Media Library classification writes Portfolio category directly into source MediaAsset**

if classification is contextual.

---

# 243. OWN-AP-011

**Theme controls data visibility**

---

# 244. OWN-AP-012

**Analytics automatically selects Primary Project**

---

# 245. OWN-AP-013

**VOP observation changes semantic source automatically**

---

# 246. OWN-AP-014

**AI draft stays linked as live generated text replacing human edits**

---

# 247. OWN-AP-015

**External sender email is saved as actress Contact**

---

# 248. OWN-AP-016

**Public Builder Casting Name creates Casting entity**

---

# 249. OWN-AP-017

**Opportunity stage stored separately in Kanban card and Opportunity table**

---

# 250. OWN-AP-018

**Search index becomes fallback source after Profile loss**

---

# 251. OWN-AP-019

**Cache contains only copy of latest published content**

---

# 252. OWN-AP-020

**Historical Revision queries mutable current fields**

---

# 253. Ownership Quality Gate

Для каждого persistent field/entity before DB/API implementation MUST быть определено:

- [ ] ownership category;
- [ ] writable owner;
- [ ] who can read;
- [ ] who can mutate;
- [ ] Human/System/AI authority;
- [ ] projection consumers;
- [ ] snapshot behaviour;
- [ ] revision behaviour;
- [ ] audit requirement;
- [ ] visibility;
- [ ] deletion implications;
- [ ] concurrency owner;
- [ ] import/migration behaviour.

---

# 254. Field-Level Ownership Template

```text
Field:
ActorLanguageProficiency.level

Category:
MASTER

Owner:
Skills & Languages Domain

Writable by:
Authorized Admin

Readable by:
Profile
Questionnaires
Builder if allowed
Casting AI
Search

AI:
May suggest, cannot confirm

Snapshots:
Questionnaire Revision
Casting analysis source snapshot
AI draft source snapshot

Audit:
Yes for change

Public:
According to visibility
```

---

# 255. Example — Contact Value

```text
Field:
Contact.value

Category:
MASTER / sensitive

Owner:
Contacts Domain

Writable:
Authorized Admin

Questionnaire:
Reference/snapshot only

Builder:
Read only when explicitly eligible

Public visitor:
No mutation

AI:
No mutation

Audit:
Yes

Privacy:
Server-side projection
```

---

# 256. Example — Questionnaire Selected Project

```text
Field:
Questionnaire.ProjectSelection

Category:
CONFIGURATION

Owner:
Questionnaire Domain

Target:
Project reference

Project Domain:
Does not change

Published Revision:
Snapshots selection/order/content as required
```

---

# 257. Example — Casting Extracted Requirement

```text
Field:
CastingRequirement.extracted_value

Category:
AI_OUTPUT

Owner:
Casting AI Analysis

Writable:
AI generation process

Human:
May confirm/modify/reject

Actor Profile:
Never mutated from extraction directly
```

---

# 258. Example — Confirmed Casting Requirement

```text
Category:
OPERATIONAL / confirmed Casting fact

Owner:
Casting Domain

Source:
Original CastingSource + Human Decision

Profile:
Read-only comparison target
```

---

# 259. Example — Opportunity Stage

```text
Category:
OPERATIONAL

Owner:
Opportunity Domain

Writable:
Authorized Human via transition

Dashboard/Kanban:
Projection/action surface

AI:
May recommend next step
Cannot move stage autonomously
```

---

# 260. Example — QR Artifact

```text
Category:
DERIVED

Owner:
QR generation subsystem

Source owner:
ProfessionalLink or canonical route

Regenerable:
Yes

Manual target mutation:
No
```

---

# 261. Example — Theme Proposal

```text
Category:
AI_OUTPUT

Owner:
Theme AI

Human action:
Apply to ThemeRevision Draft

Public activation:
Separate Theme Domain publish
```

---

# 262. Example — Builder Temporary Role

```text
Category:
TEMPORARY

Owner:
PublicBuilderSession

Scope:
Generated package only

Must not create:
Role
Project
Casting
```

---

# 263. Ownership Matrix — Human/System/AI

| Data Type | Human | System | AI |
|---|---|---|---|
| Profile fact | Authoritative edit | validate/persist | suggest only |
| Portfolio classification | confirm/edit | validate | suggest |
| Emotional crop | confirm/edit | derive artifacts | suggest |
| Project/Role | authoritative | persist | draft copy only |
| Skill/Language | authoritative | validate | suggest |
| Contact visibility | authoritative | enforce | no authority |
| Questionnaire config | authoritative | validate/render | draft suggestion possible |
| Builder selection | Visitor authority | enforce eligibility | optional assist only |
| Casting extraction | review | persist | generate |
| Confirmed requirement | authoritative | validate | no direct authority |
| Opportunity stage | authoritative | enforce transition | recommend only |
| Notification delivery | configure intent | execute | none |
| BB text | apply/edit | persist | generate |
| VOP issue | resolve decision | detect/execute safe task | assist |
| Theme | publish/edit | validate/activate | propose |
| Analytics | interpret | collect | summarize/recommend |

---

# 264. Public Exposure Ownership Matrix

| Context | Data authority |
|---|---|
| Public Home | Public Projection |
| Portfolio | Portfolio Projection |
| Emotional | Emotional public projection |
| Project detail | Project public projection |
| Questionnaire HTML | Questionnaire Revision/approved projection |
| Prepared PDF | Questionnaire Revision |
| Builder UI | Builder Eligibility Projection |
| Builder PDF | Generation Snapshot |
| QR | Canonical URL + generated artifact |
| Contact page | Contact Public Projection |

---

# 265. Public Projection Rule

Public-facing renderer receives only effective authorized values.

It must not know private alternatives unless needed and explicitly permitted.

---

# 266. Admin Projection Rule

Admin may receive more data based on authorization, but each editor still writes only to owning Domain.

---

# 267. Ownership and API DTOs

DTOs SHOULD reflect operation intent.

Example good:

```text
UpdateContactVisibilityCommand
```

instead of posting entire Contact object received earlier with all fields writable.

---

# 268. Patch Semantics

PATCH-like behaviour may be used technically.

Server MUST whitelist fields belonging to current mutation/use case.

---

# 269. Mass Assignment Prevention

Client must never be able to add:

```text
is_admin=true
published=true
allow_public_builder=true
```

to arbitrary payload unless exact command authorizes those fields.

---

# 270. Ownership and Server Actions

Server action should validate target Domain ownership before mutation.

---

# 271. Ownership and Database Permissions

Application DB account architecture MAY remain centralized.

Logical domain ownership is still enforced in application/service layer.

Future DB-level security MAY complement it.

---

# 272. Ownership and Tests

Tests MUST verify not only successful mutation but **prohibited cross-domain mutation**.

---

# 273. E2E-OWN-001 — Language

Editing Questionnaire cannot modify current ActorLanguageProficiency.

---

# 274. E2E-OWN-002 — Builder

Manipulated Public Builder request cannot change Profile/Project/Contact.

---

# 275. E2E-OWN-003 — Casting AI

Analysis cannot create ActorSkill automatically.

---

# 276. E2E-OWN-004 — Feedback

Sender phone never appears in Actor Contact directory automatically.

---

# 277. E2E-OWN-005 — Notification

WhatsApp failure does not modify Feedback state.

---

# 278. E2E-OWN-006 — Dashboard

Changing Opportunity through Dashboard changes authoritative Opportunity and history, not local card only.

---

# 279. E2E-OWN-007 — Historical Questionnaire

Current Profile update leaves previous Revision unchanged.

---

# 280. E2E-OWN-008 — QR

Changing ProfessionalLink invalidates/regenerates QR rather than editing URL inside QR subsystem.

---

# 281. E2E-OWN-009 — Theme

Theme change does not modify content visibility/professional facts.

---

# 282. E2E-OWN-010 — Search

Deleting/rebuilding search index does not affect Source content.

---

# 283. E2E-OWN-011 — Media

Deleting PortfolioItem does not delete MediaAsset when still retained by Media domain/policy.

---

# 284. E2E-OWN-012 — Grid

Editing EmotionalGrid crop leaves original MediaAsset unchanged.

---

# 285. E2E-OWN-013 — BB Assistant

Applying Biography draft changes Profile Draft; later Profile edit does not mutate historical AIDraft.

---

# 286. E2E-OWN-014 — Opportunity

Booked → Create Project Draft creates distinct Project owned by Project domain with provenance.

---

# 287. E2E-OWN-015 — Public Visibility

Private Contact is absent from public payload, not merely read-only.

---

# 288. E2E-OWN-016 — AI Staleness

Source changes after AIDraft generation; stale AI cannot overwrite target silently.

---

# 289. E2E-OWN-017 — Import

Imported CV candidate Skill requires human confirmation before ActorSkill exists.

---

# 290. E2E-OWN-018 — Migration

Legacy Project→Training migration preserves mapping provenance.

---

# 291. E2E-OWN-019 — Social

Social post caption edit does not modify BB draft source record.

---

# 292. E2E-OWN-020 — Audit

Removing/rebuilding derived projections does not erase Audit history.

---

# 293. Ownership Traceability

Required chain:

```text
Business Fact
 ↓
Owning Domain
 ↓
Writable Entity
 ↓
Mutation Command
 ↓
Audit
 ↓
Projection Consumers
 ↓
Snapshots / Derived Artifacts
```

---

# 294. Data Ownership Decision Tree

```text
Is this a professional/business fact?
 ├─ YES
 │   ↓
 │ Does it have independent identity/lifecycle?
 │   ├─ YES → MASTER/OPERATIONAL ENTITY
 │   └─ NO  → field/value object on owner
 │
 └─ NO
     ↓
Is it context-specific selection/presentation?
 ├─ YES → CONFIGURATION
 └─ NO
     ↓
Is it historical frozen data?
 ├─ YES → SNAPSHOT
 └─ NO
     ↓
Is it computed/generated?
 ├─ YES → DERIVED
 └─ NO
     ↓
Is it AI output?
 ├─ YES → AI_OUTPUT
 └─ TEMPORARY / AUDIT / investigate semantics
```

---

# 295. Mutation Authority Decision Tree

```text
Requested change
      ↓
Which Domain owns the value?
      ↓
Is caller authorized for that Domain?
      ↓
Is this Human-only professional decision?
      ├─ YES → explicit Human confirmation
      │
      └─ NO
          ↓
Is mutation deterministic/reversible/derived?
          ├─ YES → system automation may execute
          └─ NO → human review/confirmation
```

---

# 296. Source / AI / Human Decision Doctrine

Canonical:

```text
SOURCE FACT
    !=
AI EXTRACTION
    !=
AI RECOMMENDATION
    !=
HUMAN DECISION
    !=
DERIVED PRESENTATION
```

These distinctions MUST be visible in domain architecture, database model and API contracts.

---

# 297. Ownership Compliance Criteria

Implementation complies with DOC-063 if:

1. every professional fact has one writable owner;
2. Profile is not duplicated into Questionnaires;
3. Questionnaire config references/snapshots Source;
4. Public Builder cannot edit Master Data;
5. Casting Source remains separate from Profile;
6. AI extracted requirements remain distinct from confirmed requirements;
7. Profile Match remains derived;
8. Opportunity alone owns pipeline stage;
9. Dashboard/Kanban do not own stage copies;
10. Notification does not own business records;
11. Contact visibility belongs Contacts domain;
12. QR does not own URL;
13. PDF does not become source data;
14. Search/cache/read models are rebuildable;
15. AI draft application is explicit and auditable;
16. Theme cannot mutate professional facts or authorization;
17. analytics cannot create business truth;
18. Public projection is server-authoritative;
19. external/public input cannot mutate Profile directly;
20. imported candidate data requires ownership transfer via validation/human decision;
21. historical snapshots remain independent of current Source changes;
22. relation-specific configuration is not stored as universal entity fact;
23. writable APIs respect domain boundaries;
24. significant source mutations are audited;
25. prohibited cross-domain mutation is covered by tests.

---

# 298. Итоговая модель владения

```text
                        AUTHORITATIVE MASTER DATA
                                  │
       ┌──────────────────────────┼──────────────────────────┐
       ↓                          ↓                          ↓
    PROFILE                     MEDIA                   PROJECTS
  SKILLS/LANGUAGES            PORTFOLIO                  ROLES
    CONTACTS                  EMOTIONAL                 TRAINING
       │                          │                          │
       └──────────────────────────┼──────────────────────────┘
                                  ↓
                         CONFIGURATION LAYER
                       Questionnaire / Builder
                                  │
                 ┌────────────────┼────────────────┐
                 ↓                ↓                ↓
              SNAPSHOT        PROJECTION        DERIVED
                 │                │                │
               PDF            Public UI        QR / Media
                 │
                 ↓
                       PROFESSIONAL WORKFLOW
                 Feedback → Casting → Opportunity
                                  │
                                  ↓
                               Booked
                                  │
                                  ↓
                         Project / Role Draft


AI operates beside this chain:

Source → AI Output → Human Decision → Owning Domain

AI never becomes the owner of professional truth merely because it generated a plausible value.
```

---

# 299. Финальный принцип

> **Владение данными определяет, где находится профессиональная правда и кто имеет право её изменить. Портфолио владеет профессиональной классификацией фотографии, Media — оригиналом файла, Project — фактом проекта, Contacts — контактными данными и их видимостью, Questionnaire — только конфигурацией и историческими снимками, Casting — требованиями конкретного кастинга, Opportunity — бизнес-стадией, а AI — исключительно своими предложениями, извлечениями и черновиками. Все остальные экраны, PDF, QR, поисковые индексы, Dashboard и аналитические представления должны быть производными от этих владельцев, а не конкурирующими источниками истины.**