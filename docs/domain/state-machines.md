# DOMAIN STATE MACHINES

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Канонические состояния, переходы и правила жизненного цикла доменных сущностей

**Целевой файл:** `docs/domain/state-machines.md`  
**Документ:** DOC-062  
**Статус:** ✅ Completed  
**Тип:** Domain / State Machines / Lifecycle

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/conversion-funnel.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/forms-and-validation.md`
- `docs/ux/ui-states.md`
- `docs/domain/domain-model.md`
- `docs/domain/relationships.md`

---

# 1. Назначение документа

Настоящий документ определяет нормативные state machines основных доменных сущностей.

Он отвечает на вопросы:

1. какие состояния существуют;
2. какие состояния являются business/lifecycle состояниями;
3. какие переходы допустимы;
4. какие переходы запрещены;
5. какие переходы требуют Human Decision;
6. где допускается безопасная автоматизация;
7. какие состояния являются terminal;
8. как работают revisions;
9. как обрабатываются failures;
10. как обрабатывается staleness;
11. как работают concurrency guards;
12. как state transitions отражаются в Audit;
13. какие переходы должны быть idempotent;
14. чем Domain State отличается от UI State;
15. как сущности разных domains взаимодействуют через события.

---

# 2. Главный принцип

> **Domain State — это авторитетное состояние бизнес-сущности, а не визуальный статус интерфейса. Любой переход должен быть объясним бизнес-правилом, выполняться через валидированный transition и сохранять историю там, где состояние имеет профессиональное или операционное значение.**

---

# 3. State Machine IDs

Используется:

```text
SM-{DOMAIN}-{NNN}
```

Transition:

```text
TR-{DOMAIN}-{NNN}
```

Invariant:

```text
SM-INV-{NNN}
```

---

# 4. Domain State ≠ UI State

Пример:

```text
Questionnaire Domain State:
DRAFT

UI State:
SAVING

Readiness:
BLOCKED

PDF Processing:
IDLE
```

Эти состояния существуют независимо.

---

# 5. Основные виды состояния

В продукте MUST различаться:

```text
Lifecycle State
Business Workflow State
Processing State
Readiness State
Publication State
Delivery State
AI Review State
Derived Freshness State
```

---

# 6. Lifecycle State

Определяет жизненный цикл сущности.

Например:

```text
DRAFT
ACTIVE
ARCHIVED
```

---

# 7. Business Workflow State

Отражает профессиональный процесс.

Например:

```text
Opportunity:
NEW
QUALIFIED
AUDITION
CALLBACK
OFFER
BOOKED
```

---

# 8. Processing State

Техническая операция:

```text
QUEUED
PROCESSING
SUCCEEDED
FAILED
```

---

# 9. Readiness State

Вычисляемая способность выполнить целевое действие:

```text
READY
ATTENTION
BLOCKED
```

Readiness SHOULD преимущественно быть computed state, а не вручную редактируемым business fact.

---

# 10. Freshness State

Derived output MAY быть:

```text
CURRENT
STALE
INVALID
```

Это не lifecycle Source entity.

---

# 11. State Transition Contract

Каждый важный transition MUST определить:

```text
source state
target state
trigger
actor
preconditions
authorization
validation
side effects
audit
idempotency
failure behaviour
```

---

# 12. Human Authority Classes

Используется Automation Matrix:

```text
AUTO-0 Human Only
AUTO-1 Observation
AUTO-2 Suggestion
AUTO-3 Human-Confirmed Automation
AUTO-4 Safe Automatic Execution
```

State transition MUST respect соответствующий уровень.

---

# 13. Generic Publication State Machine

Для publishable content применяется базовая модель:

```text
DRAFT
  ↓
READY
  ↓
PUBLISHED
  ↓
ARCHIVED
```

Но `READY` чаще является readiness projection, а не обязательным persisted lifecycle state.

Рекомендуемая физическая модель:

```text
lifecycle = DRAFT | PUBLISHED | ARCHIVED
readiness = READY | ATTENTION | BLOCKED
```

---

# 14. Generic Publication Flow

```text
DRAFT
 ↓
Validate
 ↓
BLOCKED? ── yes ──> remain DRAFT
 ↓ no
Preview
 ↓
Human Publish
 ↓
PUBLISHED
```

---

# 15. Save ≠ Publish

Critical invariant:

```text
SAVE
!=
PUBLISH
```

---

# 16. Published Edit Rule

Для revision-based entities:

```text
PUBLISHED
 ↓
Edit
 ↓
NEW DRAFT / working copy
```

Current historical Published Revision remains immutable.

---

# 17. Archive Rule

```text
PUBLISHED
→ ARCHIVED
```

requires explicit human action for professional content.

---

# 18. Restore Rule

If archive restore supported:

```text
ARCHIVED
→ DRAFT or ACTIVE
```

depending entity.

Restoration MUST NOT silently republish if review is required.

---

# 19. SM-INV-001 — No Implicit Publish

No AI/background/autosave action can cause professional content publication without explicit authorized transition.

---

# 20. SM-INV-002 — No Silent Reverse Transition

System MUST NOT move business entity backward/forward merely due analytics or timeout unless state machine explicitly defines such automated behaviour.

---

# 21. SM-MEDIA-001 — MediaAsset Processing

Source lifecycle:

```text
UPLOADING
 ↓
STORED
 ↓
PROCESSING
 ↓
READY
```

Failure branches:

```text
UPLOADING → FAILED_UPLOAD
PROCESSING → PROCESSING_PARTIAL
PROCESSING → PROCESSING_FAILED
```

---

# 22. Media Original State

Once source binary has been successfully persisted and verified:

```text
ORIGINAL_READY
```

should remain distinct from derivative processing state.

---

# 23. Media Composite State

Example:

```text
Original = READY
Thumbnail = READY
Web = READY
PDF derivative = FAILED
```

Overall MediaAsset MAY still be operationally usable.

---

# 24. TR-MEDIA-001 — Upload Start

```text
NEW
→ UPLOADING
```

Actor:

```text
Human/System upload action
```

---

# 25. TR-MEDIA-002 — Original Stored

```text
UPLOADING
→ STORED
```

Requires:

- valid storage;
- MIME validation;
- size validation;
- checksum/metadata as applicable.

---

# 26. TR-MEDIA-003 — Processing Start

```text
STORED
→ PROCESSING
```

AUTO-4 allowed.

---

# 27. TR-MEDIA-004 — Processing Complete

```text
PROCESSING
→ READY
```

if required derivatives/metadata succeed.

---

# 28. TR-MEDIA-005 — Partial Processing

```text
PROCESSING
→ PROCESSING_PARTIAL
```

if source remains valid but non-critical derivative failed.

---

# 29. TR-MEDIA-006 — Retry

```text
PROCESSING_PARTIAL
→ PROCESSING
```

or failed derivative independently retries.

AUTO-4 allowed if deterministic/idempotent.

---

# 30. Media Processing Failure Does Not Delete Source

Critical invariant.

---

# 31. Media Classification State

Professional classification may independently be:

```text
UNCLASSIFIED
SUGGESTED
CONFIRMED
```

AI suggestion cannot move directly to CONFIRMED without human authority where semantic classification is involved.

---

# 32. SM-PORT-001 — PortfolioItem Lifecycle

Recommended:

```text
DRAFT
→ PUBLISHED
→ ARCHIVED
```

Readiness computed separately.

---

# 33. Portfolio Primary Marker

State-like scoped relation:

```text
NOT_PRIMARY
PRIMARY
```

but only one valid Primary per defined scope.

---

# 34. TR-PORT-001 — Publish PortfolioItem

```text
DRAFT
→ PUBLISHED
```

Requires:

- valid MediaAsset;
- valid professional category;
- permitted visibility;
- human confirmation.

---

# 35. TR-PORT-002 — Archive PortfolioItem

```text
PUBLISHED
→ ARCHIVED
```

Human-controlled.

---

# 36. TR-PORT-003 — Set Primary

```text
NOT_PRIMARY
→ PRIMARY
```

Atomically:

```text
previous PRIMARY
→ NOT_PRIMARY
```

---

# 37. Primary Change Is Audited

At least:

- actor;
- category/scope;
- previous;
- new;
- timestamp.

---

# 38. SM-EMO-001 — EmotionalSession Lifecycle

Recommended:

```text
DRAFT
→ ACTIVE/PUBLISHED
→ ARCHIVED
```

If public publication occurs primarily through Grids, Session state MAY remain:

```text
DRAFT
ACTIVE
ARCHIVED
```

while Grid owns public publication.

---

# 39. EmotionalSession Ready Preconditions

At minimum:

- Shooting Date present;
- valid session media.

---

# 40. SM-EMO-002 — EmotionalGrid State Machine

Canonical:

```text
DRAFT
 ↓
INCOMPLETE
 ↓
NEEDS_REVIEW
 ↓
READY_TO_FINALIZE
 ↓
FINALIZING
 ↓
FINALIZED
 ↓
PUBLISHED
 ↓
ARCHIVED
```

Implementation MAY collapse `DRAFT/INCOMPLETE`, but semantics MUST remain.

---

# 41. Grid DRAFT

Grid exists but configuration not complete.

---

# 42. Grid INCOMPLETE

Examples:

- wrong cell count;
- missing source;
- invalid dimension.

---

# 43. Grid NEEDS_REVIEW

All structural elements may exist, but one or more cells require human confirmation.

---

# 44. Grid READY_TO_FINALIZE

Requires:

```text
allowed dimensions
exact cell count
all cells valid
all cells confirmed
```

---

# 45. TR-EMO-001 — Finalize Grid

```text
READY_TO_FINALIZE
→ FINALIZING
```

Human-triggered.

---

# 46. TR-EMO-002 — Generate Grid Artifacts

During:

```text
FINALIZING
```

system generates:

- master;
- web;
- PDF;
- thumbnail.

AUTO-4 technical execution.

---

# 47. TR-EMO-003 — Finalization Complete

```text
FINALIZING
→ FINALIZED
```

only if required derived artifacts satisfy release requirements.

---

# 48. TR-EMO-004 — Finalization Failure

```text
FINALIZING
→ READY_TO_FINALIZE
```

with processing error attached.

User configuration remains.

---

# 49. TR-EMO-005 — Publish Grid

```text
FINALIZED
→ PUBLISHED
```

Human-controlled.

---

# 50. Grid Mutation After Finalization

Changing crop/cell/source after FINALIZED MUST create:

```text
editable draft/new grid revision/config version
```

rather than silently mutate published artifact.

Exact revision strategy DOC-064.

---

# 51. Grid Cell Review State

Each cell:

```text
UNREVIEWED
SUGGESTED
CONFIRMED
```

AI/system crop suggestion may produce `SUGGESTED`.

Only human decision produces `CONFIRMED`.

---

# 52. Cell Edit After Confirmation

If source/crop changes materially:

```text
CONFIRMED
→ UNREVIEWED
```

or equivalent review-required state.

---

# 53. SM-PROJ-001 — Project Lifecycle

Recommended:

```text
DRAFT
→ PUBLISHED
→ ARCHIVED
```

---

# 54. Project Draft

May contain incomplete Role/media.

---

# 55. Project Publication

Requires applicable professional facts and human approval.

---

# 56. Project Archive

Removes Project from active public projections.

Roles/RoleMedia become effectively non-public in Project context.

---

# 57. SM-ROLE-001 — Role Lifecycle

Role SHOULD inherit/project parent Project activity but MAY have own lifecycle:

```text
DRAFT
ACTIVE
ARCHIVED
```

A Role cannot be publicly ACTIVE if Project is archived.

---

# 58. Parent State Constraint

```text
Project = ARCHIVED
⇒ Role cannot be effective-public
```

---

# 59. SM-TRN-001 — Training Lifecycle

```text
DRAFT
→ PUBLISHED/ACTIVE
→ ARCHIVED
```

---

# 60. SM-ACH-001 — Achievement Lifecycle

Same base pattern.

---

# 61. SM-SKL-001 — ActorSkill Lifecycle

Typically:

```text
ACTIVE
→ ARCHIVED
```

Draft may exist through Admin editor.

Skill level change updates current fact rather than creates publication revision unless later history model requires it.

Audit records significant changes.

---

# 62. SM-LANG-001 — Language Proficiency Lifecycle

Same principle.

---

# 63. Language Value Mutation

```text
B2
→ C1
```

is Source Fact mutation.

It MUST:

- be human-authorized;
- invalidate relevant current projections;
- not rewrite historical Questionnaire Revisions.

---

# 64. SM-LINK-001 — ProfessionalLink Lifecycle

```text
DRAFT
→ ACTIVE/PUBLISHED
→ ARCHIVED
```

Independent health state:

```text
UNKNOWN
CHECKING
REACHABLE
TEMPORARILY_UNREACHABLE
INVALID
```

---

# 65. Link Lifecycle ≠ Link Health

A link can be:

```text
ACTIVE
+
TEMPORARILY_UNREACHABLE
```

---

# 66. Link Health Transitions

```text
UNKNOWN
→ CHECKING
→ REACHABLE
```

or:

```text
CHECKING
→ TEMPORARILY_UNREACHABLE
```

or deterministically:

```text
CHECKING
→ INVALID
```

when URL structurally unsafe/invalid.

---

# 67. Link Failure Never Auto-Archives

Critical invariant.

---

# 68. URL Change

When canonical URL changes:

```text
health → UNKNOWN/CHECKING
dependent QRArtifact → STALE
dependent current preview → STALE
```

Historical snapshots unaffected.

---

# 69. SM-CON-001 — Contact Lifecycle

Recommended:

```text
ACTIVE
→ ARCHIVED
```

Draft may exist during creation.

---

# 70. Contact Visibility Is Separate State Vector

Example:

```text
public_site = true
prepared_questionnaires = true
public_builder = false
```

These are independent permissions, not one lifecycle state.

---

# 71. Contact Visibility Transition

Each visibility change requires:

- authorization;
- dependency impact;
- audit;
- public cache/projection invalidation.

---

# 72. Sensitive Contact Publication

Enabling sensitive Contact visibility MAY require stronger explicit confirmation according to security/privacy policy.

---

# 73. SM-QST-001 — QuestionnaireDefinition Lifecycle

Canonical:

```text
DRAFT
→ ACTIVE
→ ARCHIVED
```

where active logical Questionnaire may own multiple immutable published revisions.

Alternative implementation MAY represent:

```text
ACTIVE QuestionnaireDefinition
+
Draft working state
+
Published revisions
```

This is recommended.

---

# 74. Questionnaire Working State

Logical editable state:

```text
CLEAN
DIRTY
STALE
```

is not necessarily domain lifecycle.

---

# 75. SM-QST-002 — Questionnaire Revision Lifecycle

Canonical:

```text
DRAFT_SNAPSHOT
→ VALIDATING
→ READY
→ PUBLISHING
→ PUBLISHED
```

Historical published revision remains terminal/immutable:

```text
PUBLISHED
```

It may later be:

```text
SUPERSEDED
```

as metadata/projection, not mutated content.

---

# 76. Questionnaire Revision PUBLISHED

Immutable content/configuration snapshot.

---

# 77. TR-QST-001 — Validate Draft

```text
DRAFT
→ VALIDATING
```

---

# 78. Validation Failure

```text
VALIDATING
→ DRAFT
```

with blockers/warnings.

---

# 79. Validation Success

```text
VALIDATING
→ READY
```

---

# 80. READY ≠ Published

Critical invariant.

---

# 81. TR-QST-002 — Publish Revision

```text
READY
→ PUBLISHING
```

requires explicit human action.

---

# 82. Publish Side Effects

On success:

```text
PUBLISHING
→ PUBLISHED
```

and:

- immutable revision created/finalized;
- current published pointer updated;
- artifacts generated/queued;
- public projection invalidated;
- audit recorded.

---

# 83. Publish Failure

```text
PUBLISHING
→ READY
```

or `DRAFT` if validation changed.

Previous Published Revision remains active.

---

# 84. New Edit After Publication

```text
PUBLISHED Revision N
+
edit
→ current Draft for Revision N+1
```

Revision N remains immutable.

---

# 85. Primary Questionnaire State

Scoped state:

```text
PRIMARY
NOT_PRIMARY
```

Only published/eligible QuestionnaireDefinition can become Primary.

---

# 86. Primary Change

Must be atomic and audited.

---

# 87. Questionnaire Artifact Processing

Each PDF artifact:

```text
QUEUED
→ GENERATING
→ READY
```

or:

```text
GENERATING
→ FAILED
```

---

# 88. PDF Failure

Does NOT roll back Published Questionnaire Revision if HTML/public revision publication already succeeded and business rules allow artifact retry.

If PDF is mandatory publication blocker, publication transaction/workflow must reflect that before final `PUBLISHED`.

Exact readiness architecture later.

---

# 89. QR Readiness State

Per applicable link:

```text
NOT_REQUIRED
READY
STALE
INVALID
FAILED
```

---

# 90. Invalid QR May Block Publish

If Questionnaire configuration requires QR for link:

```text
QR INVALID
⇒ Questionnaire Readiness BLOCKED
```

---

# 91. SM-PQB-001 — PublicBuilderTemplate Lifecycle

```text
DRAFT
→ ENABLED
→ DISABLED
→ ARCHIVED
```

Admin-controlled.

---

# 92. Template Disable

New sessions cannot start.

Existing sessions must revalidate according to active-template policy.

---

# 93. SM-PQB-002 — PublicBuilderSession State Machine

Canonical:

```text
ACTIVE
 ↓
CONFIGURING
 ↓
PREVIEWABLE
 ↓
GENERATING
 ↓
ACTIVE / GENERATED
```

Cross-cutting terminal:

```text
EXPIRED
INVALIDATED
```

Recommended simplification:

```text
ACTIVE
GENERATING
EXPIRED
INVALIDATED
```

with configuration/readiness computed separately.

---

# 94. Session Is Temporary

`GENERATED` does not need to be terminal.

User may modify and generate again while Session remains ACTIVE.

---

# 95. Builder Configuration Readiness

Computed:

```text
VALID
INVALID
BLOCKED
```

---

# 96. TR-PQB-001 — Start Session

```text
NONE
→ ACTIVE
```

Requires:

- valid Template;
- Profile;
- current locale/configuration.

---

# 97. TR-PQB-002 — Update Selection

```text
ACTIVE
→ ACTIVE
```

Mutation must revalidate selected target.

---

# 98. TR-PQB-003 — Generate Preview

Requires current server revalidation.

Session remains ACTIVE.

---

# 99. TR-PQB-004 — Generate PDF

```text
ACTIVE
→ GENERATING
```

for current generation job.

---

# 100. Builder Generation Snapshot

Before render:

```text
server revalidation
→ immutable generation snapshot
```

---

# 101. PDF Success

```text
GENERATING
→ ACTIVE
```

with Generated Artifact attached.

User may download/edit/generate again.

---

# 102. PDF Failure

```text
GENERATING
→ ACTIVE
```

with error.

Selection remains.

---

# 103. TR-PQB-005 — Expire Session

```text
ACTIVE
→ EXPIRED
```

automatic lifecycle expiration allowed.

---

# 104. Expired Is Terminal

Expired Session MUST NOT reactivate with old authorization state.

User starts new session.

---

# 105. INVALIDATED Session

Use where session can no longer safely operate due structural/template/security changes.

Could become:

```text
ACTIVE
→ INVALIDATED
```

If simple item revocation can be handled by revalidation/removal, Session need not fully invalidate.

---

# 106. Builder Permission Revocation

Selected item state:

```text
SELECTED_VALID
→ SELECTED_INVALID
```

Session remains active if valid recovery possible.

---

# 107. SM-FDB-001 — Feedback Lifecycle

Canonical workflow:

```text
NEW
→ READ
→ IN_REVIEW
→ ACTION_REQUIRED
→ RESOLVED
```

Additional:

```text
CLOSED
ARCHIVED
```

MAY be used depending Inbox implementation.

Avoid overcomplicated status taxonomy.

---

# 108. Feedback Business Type Is Separate

Example:

```text
type = CASTING_INVITATION
status = NEW
```

---

# 109. TR-FDB-001 — Feedback Created

```text
NONE
→ NEW
```

only after authoritative persistence.

---

# 110. Notification Happens After NEW

```text
Feedback NEW
→ Notification
```

Notification result does not affect Feedback state.

---

# 111. TR-FDB-002 — Mark Read

```text
NEW
→ READ
```

AUTO/user operation.

Does not mean reviewed/qualified.

---

# 112. TR-FDB-003 — Begin Review

```text
READ
→ IN_REVIEW
```

or NEW directly → IN_REVIEW if opened and reviewed in one action.

---

# 113. TR-FDB-004 — Action Required

```text
IN_REVIEW
→ ACTION_REQUIRED
```

if next human/business step defined.

---

# 114. TR-FDB-005 — Resolve

```text
IN_REVIEW/ACTION_REQUIRED
→ RESOLVED
```

Requires appropriate human resolution.

---

# 115. Create Casting From Feedback

Does NOT automatically resolve Feedback unless workflow explicitly says so.

Recommended:

```text
Feedback remains IN_REVIEW/ACTION_REQUIRED
+
Casting relation created
```

Resolution occurs when inbound item itself no longer needs action.

---

# 116. Feedback Cannot Become Casting State

No status:

```text
CASTING
```

inside Feedback lifecycle.

Casting is separate entity.

---

# 117. SM-CST-001 — Casting Lifecycle

Recommended operational state machine:

```text
DRAFT
→ OPEN
→ IN_REVIEW
→ ACTIVE
→ CLOSED
```

with optional terminal:

```text
ARCHIVED
```

But Casting lifecycle MUST NOT duplicate Opportunity stages.

---

# 118. Casting `ACTIVE`

Means Casting case/workspace is currently active.

It does NOT mean Qualified/Offer/etc.

---

# 119. Recommended Simplified Casting States

Canonical preferred:

```text
DRAFT
OPEN
CLOSED
ARCHIVED
```

and use:

- analysis state;
- deadline;
- Opportunity stage;

for detailed workflow.

This avoids duplicate state machines.

---

# 120. TR-CST-001 — Create Casting

```text
NONE
→ DRAFT
```

manual or from Feedback.

---

# 121. TR-CST-002 — Open Casting

```text
DRAFT
→ OPEN
```

Requires minimum source/context.

---

# 122. TR-CST-003 — Close Casting

```text
OPEN
→ CLOSED
```

Human-controlled.

Reason/outcome context SHOULD be captured.

---

# 123. Closed Casting with Opportunity

Casting may close while Opportunity remains terminal/historical.

Do not delete related Opportunity.

---

# 124. SM-CAI-001 — Casting Analysis Processing

Canonical:

```text
NOT_STARTED
→ QUEUED
→ ANALYZING
→ GENERATED
→ UNDER_REVIEW
→ REVIEWED
```

Failure:

```text
QUEUED/ANALYZING
→ FAILED
```

Staleness:

```text
GENERATED/REVIEWED
→ STALE
```

when source/profile snapshot materially changes.

---

# 125. TR-CAI-001 — Run Analysis

```text
NOT_STARTED/STALE/FAILED
→ QUEUED
```

Human-triggered or explicit allowed workflow.

---

# 126. TR-CAI-002 — Analysis Begin

```text
QUEUED
→ ANALYZING
```

AUTO-4 technical execution.

---

# 127. TR-CAI-003 — Analysis Generated

```text
ANALYZING
→ GENERATED
```

Creates AnalysisRevision.

---

# 128. Generated ≠ Confirmed

Critical invariant.

---

# 129. TR-CAI-004 — Review

```text
GENERATED
→ UNDER_REVIEW
→ REVIEWED
```

Human confirms/modifies/rejects requirement interpretations.

---

# 130. Analysis Failure

Manual Casting workflow remains available.

---

# 131. Analysis Revision Immutability

Each generated analysis pass is revisioned.

Regenerate:

```text
Revision N
→ new Revision N+1
```

not overwrite Revision N.

---

# 132. SM-CAI-002 — CastingRequirement Review State

Each extracted requirement:

```text
EXTRACTED
→ CONFIRMED
```

or:

```text
EXTRACTED
→ MODIFIED
```

or:

```text
EXTRACTED
→ REJECTED
```

Missing explicit source:

```text
NOT_SPECIFIED
```

---

# 133. Requirement `NOT_SPECIFIED`

This is a valid semantic result.

Not an error.

---

# 134. Modified Requirement

Human modification MUST retain:

- original AI extraction;
- confirmed value;
- source evidence;
- audit.

---

# 135. Requirement Staleness

If CastingSource changes after confirmation:

requirements based on prior source MAY be marked:

```text
STALE
```

and require review/new analysis.

---

# 136. SM-CAI-003 — Profile Match State

Computed states:

```text
MATCH
PARTIAL
MISMATCH
NO_CONFIRMED_DATA
NOT_APPLICABLE
```

Not manually set as actor facts.

---

# 137. Match State Recalculation

When:

- Profile fact changes;
- requirement changes;
- analysis revision changes;

current Match Result becomes stale and is recalculated.

---

# 138. SM-CAI-004 — CastingRecommendation State

```text
OPEN
→ APPROVED
```

or:

```text
OPEN
→ MODIFIED
```

or:

```text
OPEN
→ REJECTED
```

or:

```text
OPEN
→ DEFERRED
```

or:

```text
OPEN
→ SUPERSEDED
```

---

# 139. Recommendation Approval

Approval MAY trigger a separate human-confirmed action, but approval itself MUST NOT silently perform high-impact target mutation unless explicit UI action combines both transparently.

---

# 140. SM-OPP-001 — Opportunity Pipeline

Canonical business state machine:

```text
NEW
 ↓
QUALIFIED
 ↓
MATERIALS_REQUESTED
 ↓
SELF_TAPE_REQUESTED
 ↓
AUDITION
 ↓
CALLBACK
 ↓
OFFER
 ↓
BOOKED
```

Alternative terminal outcomes:

```text
CLOSED_NOT_SELECTED
WITHDRAWN
```

---

# 141. Pipeline Is Nonlinear Where Business Reality Requires

Not every Opportunity must visit every stage.

Valid skip examples MAY include:

```text
QUALIFIED → AUDITION
QUALIFIED → OFFER
MATERIALS_REQUESTED → AUDITION
AUDITION → OFFER
```

if real-world event supports it.

---

# 142. Pipeline Must Not Invent Missing Milestones

Do not insert fake Self-Tape/Audition merely to satisfy linear visualization.

---

# 143. Terminal States

Canonical terminal:

```text
BOOKED
CLOSED_NOT_SELECTED
WITHDRAWN
```

---

# 144. BOOKED Meaning

Confirmed professional booking/business outcome.

Requires human authoritative decision.

---

# 145. CLOSED_NOT_SELECTED Meaning

Opportunity ended without selection.

Requires real professional evidence/decision, not inactivity timeout alone.

---

# 146. WITHDRAWN Meaning

Actress/representative intentionally withdraws.

Human-controlled.

---

# 147. TR-OPP-001 — Create Opportunity

```text
NONE
→ NEW
```

Usually explicit from Casting.

---

# 148. TR-OPP-002 — Qualify

```text
NEW
→ QUALIFIED
```

Human business decision.

---

# 149. TR-OPP-003 — Materials Requested

```text
NEW/QUALIFIED
→ MATERIALS_REQUESTED
```

Requires authoritative external/business event.

---

# 150. TR-OPP-004 — Self-Tape Requested

From eligible active stages:

```text
→ SELF_TAPE_REQUESTED
```

---

# 151. TR-OPP-005 — Audition

```text
QUALIFIED
MATERIALS_REQUESTED
SELF_TAPE_REQUESTED
→ AUDITION
```

---

# 152. TR-OPP-006 — Callback

```text
AUDITION
→ CALLBACK
```

Normally.

If direct callback information arrives, system MAY permit valid shortcut with audit reason.

---

# 153. TR-OPP-007 — Offer

Valid from:

```text
QUALIFIED
MATERIALS_REQUESTED
SELF_TAPE_REQUESTED
AUDITION
CALLBACK
```

where actual business evidence exists.

---

# 154. TR-OPP-008 — Booked

Typically:

```text
OFFER
→ BOOKED
```

Direct transition from earlier state SHOULD be permitted only if real booking exists and Admin explicitly records skipped stages.

---

# 155. TR-OPP-009 — Not Selected

Any non-terminal active stage MAY transition:

```text
→ CLOSED_NOT_SELECTED
```

with human decision.

---

# 156. TR-OPP-010 — Withdraw

Any non-terminal active stage MAY transition:

```text
→ WITHDRAWN
```

human-controlled.

---

# 157. Terminal Reopen

Reopening terminal Opportunity SHOULD be exceptional.

If allowed:

- explicit confirmation;
- reason;
- audit;
- transition rules.

Default:

```text
terminal states cannot silently reopen
```

---

# 158. Stage History

Every business stage transition MUST append:

```text
from
to
actor
timestamp
context/reason where applicable
```

---

# 159. Stage History Cannot Be Rewritten

Corrections should produce explicit correction/audit semantics rather than silent history deletion.

---

# 160. Booked Side Effect

On BOOKED, system MAY offer:

```text
Create Project Draft
Create Role Draft
```

But MUST NOT auto-publish.

---

# 161. SM-NOT-001 — Notification Lifecycle

Notification intent:

```text
CREATED
→ ACTIVE
→ COMPLETED
```

or simpler persisted notification object.

Delivery states carry operational detail.

---

# 162. SM-NOT-002 — NotificationDelivery

Canonical:

```text
PENDING
→ SCHEDULED
→ SENDING
→ SENT
```

Failure:

```text
SENDING
→ FAILED
```

Retry:

```text
FAILED
→ RETRY_SCHEDULED
→ SENDING
```

Terminal optional:

```text
CANCELLED
```

---

# 163. Quiet Hours

If notification waits due quiet hours:

```text
PENDING
→ SCHEDULED
```

not FAILED.

---

# 164. Retry Idempotency

Repeated retry MUST not create duplicate external delivery when provider outcome is already known.

---

# 165. Notification Failure Does Not Change Source State

Critical invariant.

---

# 166. SM-BBA-001 — AIDraft Lifecycle

Canonical:

```text
GENERATING
→ GENERATED
→ EDITED
→ APPLIED
```

Alternative:

```text
GENERATED/EDITED
→ DISCARDED
```

Failure:

```text
GENERATING
→ FAILED
```

Freshness:

```text
GENERATED/EDITED
→ STALE
```

---

# 167. AIDraft Generated

System-created text exists.

Target entity unchanged.

---

# 168. Edited

Human modified generated Draft.

---

# 169. Applied

Draft content copied/applied into target editable field/context.

---

# 170. APPLIED Is Not Terminal Publication State

AIDraft may be terminal as draft history, but target remains governed by its own lifecycle.

---

# 171. Discarded

No target mutation.

May remain in audit/history according to retention.

---

# 172. Stale Draft

Source facts changed after generation.

Stale Draft MAY still be viewable/editable, but Apply may require warning/revalidation.

---

# 173. AI Draft Apply

High-level transition:

```text
GENERATED/EDITED
→ APPLIED
```

requires explicit Human action.

---

# 174. AI Draft Cannot Auto-Send

No transition:

```text
GENERATED
→ SENT
```

inside BB Assistant.

Sending belongs target communication workflow.

---

# 175. SM-VOP-001 — VOPObservation Lifecycle

Canonical:

```text
OPEN
→ ACKNOWLEDGED
→ RESOLVED
```

Alternative:

```text
OPEN/ACKNOWLEDGED
→ DEFERRED
```

or:

```text
→ DISMISSED
```

Automatic:

```text
OPEN
→ AUTO_RESOLVED
```

when underlying deterministic condition disappears/is fixed.

---

# 176. Observation `OPEN`

Condition currently matters.

---

# 177. `ACKNOWLEDGED`

Admin has seen/accepted responsibility but issue remains.

---

# 178. `DEFERRED`

Issue intentionally postponed.

It MAY return to OPEN based on due time/condition.

---

# 179. `RESOLVED`

Underlying condition corrected or intentional resolution recorded.

---

# 180. `DISMISSED`

Observation intentionally judged not actionable.

Dismissal requires reason for meaningful alerts where appropriate.

---

# 181. `AUTO_RESOLVED`

Used when deterministic safe resolution occurred.

Should not be confused with human resolution.

---

# 182. Superseded Observation

If same condition is replaced by more recent observation:

```text
OPEN
→ SUPERSEDED
```

MAY be used.

Avoid duplicate active noise.

---

# 183. SM-VOP-002 — VOPRecommendation Lifecycle

```text
OPEN
→ APPROVED
MODIFIED
REJECTED
DEFERRED
SUPERSEDED
```

same semantic review family.

---

# 184. SM-VOP-003 — AutomationExecution

```text
QUEUED
→ RUNNING
→ SUCCEEDED
```

or:

```text
RUNNING
→ FAILED
```

Retry only if safe/idempotent.

---

# 185. Automation Success Does Not Guarantee Observation Resolution

After execution:

```text
revalidate condition
```

Only then mark Observation resolved.

---

# 186. SM-THM-001 — SiteTheme Lifecycle

Logical Theme:

```text
DRAFT
→ ACTIVE
→ ARCHIVED
```

ThemeRevision handles exact publication history.

---

# 187. SM-THM-002 — ThemeRevision Lifecycle

Canonical:

```text
DRAFT
→ VALIDATING
→ READY
→ PUBLISHED
```

Optional:

```text
PUBLISHED
→ SUPERSEDED
```

as derived marker.

---

# 188. Theme Validation

Checks include:

- schema;
- required tokens;
- contrast/accessibility;
- guardrails;
- preview integrity.

---

# 189. Theme Publish

```text
READY
→ PUBLISHED
```

Human-only/explicit authorized transition.

---

# 190. ThemeProposal State

AI proposal:

```text
GENERATING
→ GENERATED
→ REVIEWED
```

then human may:

```text
APPLY_TO_DRAFT
REJECT
```

Proposal never activates site.

---

# 191. SM-THM-003 — ThemeActivation

Canonical state:

```text
SCHEDULED
→ ACTIVE
→ EXPIRED
```

or:

```text
ACTIVE
→ REPLACED
```

For permanent activation, `expires_at = null`.

---

# 192. Effective Theme

At any point:

```text
exactly one effective public ThemeRevision
```

must resolve.

---

# 193. Temporary Theme Expiry

```text
ACTIVE temporary
→ EXPIRED
```

system automatically restores next applicable permanent/effective activation.

AUTO-4 allowed because behaviour deterministic/pre-authorized.

---

# 194. Theme Rollback

Rollback SHOULD create/activate explicit previous ThemeRevision.

Do not mutate current revision contents backward.

---

# 195. SM-SOC-001 — SocialPost Lifecycle

Canonical:

```text
DRAFT
→ READY
→ PUBLISHING
→ PUBLISHED
```

Failure:

```text
PUBLISHING
→ PARTIAL_FAILED
or FAILED
```

---

# 196. Multi-Platform Social State

A SocialPost may have aggregate state:

```text
PUBLISHED
PARTIALLY_PUBLISHED
FAILED
```

derived from per-platform attempts.

---

# 197. Social Publish Is External Side Effect

Requires Human authority unless explicit future scheduling rules pre-authorize exact action/time.

---

# 198. Scheduled Social Publishing

If enabled:

```text
READY
→ SCHEDULED
→ PUBLISHING
```

Scheduling itself is human-authorized.

Execution at approved time MAY be AUTO-4.

---

# 199. SM-SOC-002 — SocialAccountConnection

```text
CONNECTED
→ EXPIRING
→ EXPIRED
```

or:

```text
CONNECTED
→ DISCONNECTED
```

Reconnection:

```text
EXPIRED/DISCONNECTED
→ CONNECTED
```

after OAuth.

---

# 200. SM-HLP-001 — SupportTicket

Suggested:

```text
OPEN
→ IN_PROGRESS
→ WAITING
→ RESOLVED
→ CLOSED
```

Exact Help module later.

---

# 201. SM-ID-001 — AdminUser Lifecycle

Conceptual:

```text
ACTIVE
SUSPENDED
DISABLED
```

Detailed security states later.

---

# 202. Session State Is Not AdminUser State

Authentication session:

```text
ACTIVE
EXPIRED
REVOKED
```

belongs security/session domain.

---

# 203. SM-AUD-001 — AuditEvent

AuditEvent has no mutable workflow.

Canonical:

```text
APPENDED
```

and immutable thereafter, except tightly controlled retention/redaction policy.

---

# 204. Audit Correction

Do not edit old AuditEvent to hide mistake.

Append corrective/related event where necessary.

---

# 205. SM-ANL-001 — AnalyticsEvent

Normally append-only event.

Processing pipeline MAY have:

```text
RECEIVED
PROCESSED
FAILED
```

but business analytics event itself should not become complex user-facing lifecycle.

---

# 206. Derived Freshness State Machine

Applicable to:

- QR;
- PDF preview;
- search index;
- current Questionnaire preview;
- AI result;
- media derivative.

Canonical:

```text
CURRENT
→ STALE
→ REBUILDING
→ CURRENT
```

Failure:

```text
REBUILDING
→ STALE/FAILED
```

---

# 207. INVALID vs STALE

```text
STALE
=
based on older but potentially valid source

INVALID
=
cannot safely be used
```

---

# 208. Example QR

URL changes:

```text
QR CURRENT
→ STALE
→ REBUILDING
→ CURRENT
```

If URL becomes private:

```text
QR CURRENT
→ INVALID
```

and Public usage blocks.

---

# 209. Example Search Index

Profile update:

```text
Search projection CURRENT
→ STALE
→ REINDEXING
→ CURRENT
```

Public core page does not depend on index availability.

---

# 210. Example Questionnaire Preview

Source changes:

```text
Preview CURRENT
→ STALE
```

Published Revision remains unaffected.

---

# 211. Concurrency Guard State

When mutation uses older entity version:

```text
CURRENT_VERSION_MISMATCH
```

This causes transition rejection.

It MUST NOT create a new business state on target entity.

---

# 212. Transition Concurrency Rule

For high-impact transition, command SHOULD provide/validate expected current state/version.

Example:

```text
Opportunity expected_stage = OFFER
command = BOOKED
```

If current stage already changed:

reject/re-evaluate.

---

# 213. Idempotent Transition Rule

If exact requested target state is already current due same request/retry:

operation SHOULD return safe idempotent result where appropriate.

---

# 214. Idempotent Example — Feedback Submit

Same idempotency key:

```text
first request → Feedback created
retry → same Feedback/result
```

not duplicate.

---

# 215. Idempotent Example — Notification

Same delivery intent/provider key prevents accidental duplicate delivery.

---

# 216. Idempotent Example — QR

Same URL/settings yields reusable same logical artifact/cache entry.

---

# 217. Idempotent Example — Publish

Repeated network retry of one publication command MUST NOT create multiple identical revisions unintentionally.

---

# 218. Transition Authorization

Every persisted transition MUST revalidate current permission server-side.

UI visibility does not authorize.

---

# 219. Transition Validation Layers

Canonical:

```text
Authentication
→ Authorization
→ Current State
→ Version/Concurrency
→ Structural Validation
→ Business Rule Validation
→ Transition
→ Persistence
→ Side Effects
```

---

# 220. Side Effects After Commit

Whenever possible, external/non-transactional side effects occur after authoritative domain transition commits.

Examples:

```text
Feedback persist
→ notification

Opportunity stage persist
→ analytics/event

Questionnaire revision persist
→ PDF background generation
```

---

# 221. Transactional vs Async Boundaries

Business state commit should not depend on optional asynchronous service unless that service is itself a mandatory condition of state transition.

---

# 222. Example Feedback

```text
DB commit succeeds
WhatsApp fails
```

Feedback remains NEW.

---

# 223. Example Theme

If deterministic accessibility validation is mandatory before Publish:

failure prevents:

```text
READY → PUBLISHED
```

because validation is a transition precondition, not optional side effect.

---

# 224. Example Questionnaire PDF

If published Questionnaire requires a valid PDF as core deliverable, state workflow MAY use:

```text
PUBLISHING
→ generating mandatory PDF
→ PUBLISHED
```

If PDF is regenerable secondary artifact:

Revision may publish first and PDF retries separately.

Final architectural choice belongs Questionnaire/PDF architecture docs.

---

# 225. State Transition Audit Matrix

Must audit:

```text
Portfolio Primary changed
Project published/archived
Contact visibility changed
Questionnaire published
Primary Questionnaire changed
Feedback status resolved
Casting created/closed
Casting Requirement confirmed/modified
Opportunity stage changed
Opportunity booked
AIDraft applied
VOP recommendation approved
Theme published/activated/rolled back
Social post externally published
```

---

# 226. Technical Processing Audit

Routine derivative success MAY use operational logs rather than permanent business Audit.

Examples:

- thumbnail generated;
- cache rebuilt.

Failures affecting professional publication SHOULD remain observable.

---

# 227. State Transition Notification Policy

Not every state change produces Notification.

Examples likely notify:

- new professional Feedback;
- Casting deadline;
- critical processing failure;
- failed external notification.

Examples not notify:

- user manually opens tab;
- derivative cache success.

---

# 228. State Transition Analytics Policy

Analytics MAY record:

- Questionnaire generated;
- Builder PDF completed;
- professional inquiry persisted;
- Opportunity Booked.

Analytics never determines authoritative state.

---

# 229. Cross-Domain State Interaction — Feedback to Casting

```text
Feedback NEW/IN_REVIEW
      ↓ explicit Admin action
Casting DRAFT
```

Feedback does not automatically change to RESOLVED.

---

# 230. Cross-Domain State Interaction — Casting to Opportunity

```text
Casting OPEN
      ↓ human business decision
Opportunity NEW
```

Casting remains source/workspace.

---

# 231. Cross-Domain State Interaction — Opportunity to Project

```text
Opportunity BOOKED
      ↓ explicit action
Project DRAFT
Role DRAFT
```

No direct PUBLISHED state.

---

# 232. Cross-Domain State Interaction — Profile Change to Questionnaire

```text
Profile Fact Changed
→ current Questionnaire Draft/Preview STALE
→ Published Revision unchanged
```

---

# 233. Cross-Domain State Interaction — Contact Permission Change

```text
Contact Builder Permission ON
→ OFF
```

Effects:

```text
new Builder Projection excludes Contact
active sessions revalidate
current Builder preview may become INVALID/STALE
historical revision unchanged unless privacy policy overrides
```

---

# 234. Cross-Domain State Interaction — ProfessionalLink URL Change

```text
Link ACTIVE
canonical URL changed
→ health UNKNOWN
→ QR STALE
→ current projections refresh
```

---

# 235. Cross-Domain State Interaction — Casting Source Change

```text
CastingSource updated/new revision
→ previous Analysis becomes STALE
→ previous Requirement confirmations may require review
```

No silent regeneration unless explicitly requested/allowed.

---

# 236. Cross-Domain State Interaction — Theme Publication

```text
ThemeRevision PUBLISHED
→ ThemeActivation ACTIVE
→ public presentation cache invalidated
```

Professional data states unchanged.

---

# 237. Cross-Domain State Interaction — VOP

VOP observation state changes do not alter target lifecycle unless explicit approved automation executes target action.

---

# 238. Terminal State Doctrine

Terminal state means normal state machine does not continue.

Canonical examples:

```text
Opportunity:
BOOKED
CLOSED_NOT_SELECTED
WITHDRAWN

PublicBuilderSession:
EXPIRED

Historical QuestionnaireRevision:
PUBLISHED immutable
```

---

# 239. Terminal Does Not Mean Delete

Terminal entities remain historical records.

---

# 240. Archival Doctrine

Archive removes object from active/default workflows but preserves identity/history.

---

# 241. Deletion Doctrine

Delete is NOT ordinary state transition in most professional entities.

Deletion/retention defined separately in DOC-094.

---

# 242. State Recovery Doctrine

Recoverable processing failure SHOULD return entity to actionable prior state.

Example:

```text
Grid FINALIZING
→ failure
→ READY_TO_FINALIZE
```

not destroy Draft.

---

# 243. State Compensation Doctrine

For external side effects, compensation may be required rather than DB rollback.

Example:

Social post published externally cannot be “unpublished” by simply reverting internal state if provider requires separate delete action.

---

# 244. State Machine Anti-Pattern SM-AP-001

**One universal status enum for all entities**

---

# 245. SM-AP-002

**UI loading state stored as business state**

---

# 246. SM-AP-003

**Readiness stored as manually editable status**

---

# 247. SM-AP-004

**Save automatically transitions Draft → Published**

---

# 248. SM-AP-005

**Published Revision edited in place**

---

# 249. SM-AP-006

**Broken URL automatically archives ProfessionalLink**

---

# 250. SM-AP-007

**Grid finalizes with unconfirmed cells**

---

# 251. SM-AP-008

**AI crop proposal automatically confirms cell**

---

# 252. SM-AP-009

**Public Builder EXPIRED → ACTIVE**

---

# 253. SM-AP-010

**Notification failure changes Feedback to Failed**

---

# 254. SM-AP-011

**Feedback status becomes Casting**

---

# 255. SM-AP-012

**Casting lifecycle duplicates Opportunity pipeline**

---

# 256. SM-AP-013

**AI Analysis GENERATED means requirements confirmed**

---

# 257. SM-AP-014

**No Profile data → MISMATCH**

---

# 258. SM-AP-015

**Opportunity automatically advances based on elapsed time**

---

# 259. SM-AP-016

**Offer automatically becomes Booked**

---

# 260. SM-AP-017

**AIDraft APPLIED means Published/Sent**

---

# 261. SM-AP-018

**Theme AI proposal activates site**

---

# 262. SM-AP-019

**Analytics event changes business stage**

---

# 263. SM-AP-020

**VOP recommendation mutates source before approval**

---

# 264. SM-AP-021

**Retry creates duplicate Feedback/notification/revision**

---

# 265. SM-AP-022

**Processing failure erases user's valid configuration**

---

# 266. SM-AP-023

**Archived Project hard-deletes Role history**

---

# 267. SM-AP-024

**Stale historical Revision marked invalid merely because source changed**

---

# 268. SM-AP-025

**Terminal business outcome reopened silently**

---

# 269. State Machine Quality Gate

Перед implementation любой non-trivial state machine MUST определить:

- [ ] state identifiers;
- [ ] semantic meaning;
- [ ] initial state;
- [ ] terminal states;
- [ ] valid transitions;
- [ ] invalid transitions;
- [ ] transition actor;
- [ ] automation level;
- [ ] preconditions;
- [ ] business validation;
- [ ] authorization;
- [ ] concurrency strategy;
- [ ] idempotency requirement;
- [ ] side effects;
- [ ] failure recovery;
- [ ] audit;
- [ ] analytics;
- [ ] notification impact;
- [ ] archive/delete implications.

---

# 270. Transition Specification Template

```text
Transition ID:
TR-...

Entity:
...

From:
...

To:
...

Actor:
Human / System / AI-assisted

Automation Level:
AUTO-...

Preconditions:
...

Validation:
...

Side Effects:
...

Audit:
...

Idempotency:
...

Failure:
...
```

---

# 271. Example — Questionnaire Publish

```text
Transition ID:
TR-QST-PUBLISH

Entity:
QuestionnaireRevision

From:
READY

To:
PUBLISHED

Actor:
Authorized Admin

Automation Level:
AUTO-0 / AUTO-3 for technical execution after explicit confirmation

Preconditions:
- readiness has no blockers
- permissions valid
- current draft version matches
- required links/QR valid
- required photos present

Side Effects:
- immutable revision finalized
- current published pointer updated
- artifacts generated/queued
- public caches invalidated
- audit event created

Idempotency:
required

Failure:
previous published revision remains current
```

---

# 272. Example — Opportunity Booked

```text
Transition ID:
TR-OPP-BOOKED

From:
OFFER
or explicitly permitted earlier active state

To:
BOOKED

Actor:
Authorized Human

Automation:
AUTO-0

Preconditions:
- real booking/business outcome confirmed
- Opportunity active
- concurrency version valid

Side Effects:
- stage history append
- analytics/business event
- optional recommendation to create Project/Role Draft

Prohibited:
auto-publish Project
```

---

# 273. Example — Grid Finalize

```text
Transition ID:
TR-EMO-FINALIZE

From:
READY_TO_FINALIZE

To:
FINALIZED

Actor:
Human trigger + system execution

Preconditions:
- allowed dimensions
- exact cell count
- all cells confirmed
- valid source photos

Side Effects:
- generate master/web/PDF/thumb derivatives

Failure:
return to READY_TO_FINALIZE
preserve cell configuration
```

---

# 274. Example — Feedback Create

```text
Transition ID:
TR-FDB-CREATE

From:
NONE

To:
NEW

Actor:
Public Visitor/System

Preconditions:
- valid form
- anti-abuse validation
- attachment security rules
- idempotency key

Side Effects after commit:
- create notifications
- analytics event
- Dashboard/Inbox visibility

Failure:
no success state shown
```

---

# 275. Example — BB Apply

```text
Transition ID:
TR-BBA-APPLY

From:
GENERATED or EDITED

To:
APPLIED

Actor:
Admin

Automation:
AUTO-0/AUTO-3

Preconditions:
- target still valid
- source staleness checked
- authorization valid

Side Effects:
- target Draft field updated
- audit/provenance recorded

Prohibited:
Publish
Send
```

---

# 276. Example — VOP Auto Resolve

```text
Transition ID:
TR-VOP-AUTORESOLVE

From:
OPEN

To:
AUTO_RESOLVED

Actor:
System

Automation:
AUTO-4

Preconditions:
- underlying condition deterministically resolved
- no human semantic decision needed

Side Effects:
- resolution evidence recorded

Prohibited:
auto-resolve professional decision conflicts
```

---

# 277. State Naming Rules

State names MUST be:

- canonical;
- finite;
- unambiguous;
- business-specific where required.

Avoid free-text statuses.

---

# 278. Internal vs UI Labels

Internal:

```text
CLOSED_NOT_SELECTED
```

UI RU:

```text
Закрыто — не выбрана
```

UI EN:

```text
Closed — Not Selected
```

Underlying state remains same.

---

# 279. State Enum Versioning

Changing state vocabulary is a migration event.

Do not casually rename persisted enum values without migration/update plan.

---

# 280. Deprecated State Handling

If existing implementation uses legacy states:

migration MUST map them explicitly.

Unknown legacy state MUST NOT be guessed silently.

---

# 281. Migration Example

Legacy:

```text
status = "done"
```

must be reviewed/mapped to one of:

```text
RESOLVED
CLOSED
PUBLISHED
```

based on domain context.

---

# 282. State Machines and Documentation Traceability

Required chain:

```text
Business Rule
 ↓
State Machine
 ↓
Transition
 ↓
Server Action/API
 ↓
Acceptance Criterion
 ↓
Test
```

---

# 283. State Transition Acceptance Test

Every high-impact transition requires tests for:

```text
valid path
invalid source state
permission denial
validation failure
concurrency conflict
duplicate retry
side-effect failure
audit creation
```

---

# 284. E2E-SM-001 — Questionnaire

```text
DRAFT
→ READY
→ PUBLISHED
→ edit
→ new Draft
```

Previous Published Revision unchanged.

---

# 285. E2E-SM-002 — Grid

```text
DRAFT
→ 16/16 photos
→ 15 confirmed
→ Finalize blocked
→ 16 confirmed
→ Finalized
→ Published
```

---

# 286. E2E-SM-003 — Media Partial Failure

```text
Original ready
→ PDF derivative fails
→ original/web remain ready
→ retry
→ PDF ready
```

---

# 287. E2E-SM-004 — Builder Expiry

```text
ACTIVE
→ EXPIRED
→ generation denied
→ start new session
```

---

# 288. E2E-SM-005 — Feedback Notification Failure

```text
Feedback NEW
→ notification delivery FAILED
→ Feedback remains NEW
```

---

# 289. E2E-SM-006 — Casting Analysis

```text
NOT_STARTED
→ ANALYZING
→ GENERATED
→ UNDER_REVIEW
→ REVIEWED
```

No Profile mutation.

---

# 290. E2E-SM-007 — Requirement Review

```text
EXTRACTED
→ MODIFIED
```

Original extraction preserved.

---

# 291. E2E-SM-008 — Opportunity Happy Path

```text
NEW
→ QUALIFIED
→ AUDITION
→ CALLBACK
→ OFFER
→ BOOKED
```

Stage history complete.

---

# 292. E2E-SM-009 — Opportunity Negative Outcome

```text
AUDITION
→ CLOSED_NOT_SELECTED
```

No automatic reopen.

---

# 293. E2E-SM-010 — Direct Offer

```text
QUALIFIED
→ OFFER
→ BOOKED
```

allowed with real business evidence.

---

# 294. E2E-SM-011 — BB Assistant

```text
GENERATED
→ EDITED
→ APPLIED
```

Target remains Draft/unpublished.

---

# 295. E2E-SM-012 — Theme

```text
Theme Draft
→ Validating
→ Ready
→ Published
→ Activated
```

inaccessible Theme blocks before Publish.

---

# 296. E2E-SM-013 — VOP

```text
OPEN
→ safe technical fix
→ revalidate
→ AUTO_RESOLVED
```

---

# 297. E2E-SM-014 — QR Freshness

```text
CURRENT
→ URL change
→ STALE
→ regenerate/decode
→ CURRENT
```

---

# 298. E2E-SM-015 — Concurrency

```text
Opportunity = OFFER v7
Admin A opens
Admin B → BOOKED v8
Admin A → CLOSED_NOT_SELECTED using v7
→ rejected/conflict
```

---

# 299. E2E-SM-016 — Duplicate Publish Retry

Same publish command/request must not create two revisions.

---

# 300. E2E-SM-017 — Archived Project

```text
PUBLISHED
→ ARCHIVED
```

Roles disappear from current public projection while historical revisions remain consistent.

---

# 301. E2E-SM-018 — Contact Permission Revocation

```text
Builder allowed
→ permission OFF
→ active Builder session revalidation
→ Contact invalid for new output
```

---

# 302. E2E-SM-019 — Stale AI

```text
AI Draft GENERATED
→ Profile fact changes
→ AI Draft STALE
→ Apply requires warning/revalidation
```

---

# 303. E2E-SM-020 — Social Partial Publish

```text
Instagram SENT
TikTok FAILED
→ SocialPost PARTIALLY_PUBLISHED
```

Retry TikTok without duplicating Instagram post.

---

# 304. Canonical State Catalogue

## Media

```text
UPLOADING
STORED
PROCESSING
READY
PROCESSING_PARTIAL
FAILED
```

## Portfolio

```text
DRAFT
PUBLISHED
ARCHIVED
```

## Emotional Grid

```text
DRAFT
INCOMPLETE
NEEDS_REVIEW
READY_TO_FINALIZE
FINALIZING
FINALIZED
PUBLISHED
ARCHIVED
```

## Questionnaire

```text
DRAFT
VALIDATING
READY
PUBLISHING
PUBLISHED
ARCHIVED
```

## Builder Session

```text
ACTIVE
GENERATING
EXPIRED
INVALIDATED
```

## Feedback

```text
NEW
READ
IN_REVIEW
ACTION_REQUIRED
RESOLVED
ARCHIVED
```

## Casting

```text
DRAFT
OPEN
CLOSED
ARCHIVED
```

## Casting Analysis

```text
NOT_STARTED
QUEUED
ANALYZING
GENERATED
UNDER_REVIEW
REVIEWED
FAILED
STALE
```

## Requirement Review

```text
EXTRACTED
CONFIRMED
MODIFIED
REJECTED
NOT_SPECIFIED
STALE
```

## Opportunity

```text
NEW
QUALIFIED
MATERIALS_REQUESTED
SELF_TAPE_REQUESTED
AUDITION
CALLBACK
OFFER
BOOKED
CLOSED_NOT_SELECTED
WITHDRAWN
```

## Notification Delivery

```text
PENDING
SCHEDULED
SENDING
SENT
FAILED
RETRY_SCHEDULED
CANCELLED
```

## AI Draft

```text
GENERATING
GENERATED
EDITED
APPLIED
DISCARDED
FAILED
STALE
```

## VOP Observation

```text
OPEN
ACKNOWLEDGED
DEFERRED
RESOLVED
AUTO_RESOLVED
DISMISSED
SUPERSEDED
```

## Theme Revision

```text
DRAFT
VALIDATING
READY
PUBLISHED
SUPERSEDED
```

---

# 305. State Ownership Matrix

| State | Owning Domain |
|---|---|
| Portfolio Published | Portfolio |
| Grid Finalized | Emotional |
| Questionnaire Published | Questionnaire |
| Builder Expired | Public Builder |
| Feedback New | Feedback |
| Casting Open | Casting |
| Analysis Reviewed | Casting AI |
| Opportunity Booked | Opportunity |
| Notification Sent | Notifications |
| BB Draft Applied | BB Assistant |
| VOP Resolved | Virtual Operator |
| Theme Published | Themes |

---

# 306. Authority Matrix

| Transition | Authority |
|---|---|
| Media derivative generated | System |
| Photo category confirmed | Human |
| Primary photo changed | Human |
| Grid cell confirmed | Human |
| Grid derivative generated | System |
| Grid published | Human |
| Questionnaire published | Human |
| Builder session expired | System |
| Feedback created | Valid public submission |
| Feedback read | Admin/System UX action |
| Casting created | Human-authorized |
| Requirement confirmed | Human |
| Opportunity qualified | Human |
| Opportunity booked | Human |
| Notification retry | System/Admin under idempotency |
| BB Draft generated | AI |
| BB Draft applied | Human |
| VOP safe technical fix | System |
| Theme proposal | AI |
| Theme published | Human |

---

# 307. State Machine Compliance Criteria

Implementation соответствует DOC-062, если:

1. Domain State не смешивается с UI Loading/Processing;
2. Save не публикует автоматически;
3. Published historical revisions immutable;
4. Media source survives derivative failure;
5. Portfolio primary selection atomic;
6. Emotional Grid cannot finalize without exact cells and confirmations;
7. Project/Casting lifecycles remain separate;
8. link health does not control link lifecycle automatically;
9. Contact lifecycle and visibility permissions distinct;
10. Questionnaire publication is explicit and revisioned;
11. Builder session expiry is terminal;
12. Builder generation failure preserves selection;
13. Feedback persists before notification;
14. Feedback does not become Casting status;
15. Casting Analysis Generated does not mean Human Confirmed;
16. `Не указано` is valid requirement state;
17. Profile Match `NO_CONFIRMED_DATA` differs from mismatch;
18. Opportunity pipeline preserves real business history;
19. Offer and Booked remain distinct;
20. Opportunity cannot advance from analytics/timeout alone;
21. Notification delivery state cannot roll back source business entity;
22. BB Assistant Apply does not Publish/Send;
23. VOP suggestion does not mutate semantic source without allowed decision;
24. Theme AI proposal cannot activate public Theme;
25. Derived stale state is separate from lifecycle;
26. high-impact transitions are concurrency-protected;
27. duplicate retries are idempotent;
28. side-effect failure is contained;
29. significant transitions are audited;
30. state vocabulary is canonical and migration-safe.

---

# 308. Итоговая модель state transitions

```text
                       SOURCE / MASTER DATA
                               │
                               ↓
                             DRAFT
                               │
                         SAVE / VALIDATE
                               │
                    ┌──────────┴──────────┐
                    ↓                     ↓
                 BLOCKED                READY
                    │                     │
                    └────── FIX ──────────┘
                                          ↓
                                  HUMAN PUBLISH
                                          ↓
                                      PUBLISHED
                                          │
                             ┌────────────┴─────────────┐
                             ↓                          ↓
                           EDIT                       ARCHIVE
                             ↓                          ↓
                         NEW DRAFT                   ARCHIVED


                    OPERATIONAL BUSINESS FLOW

Feedback
  ↓
Casting
  ↓
Opportunity
  ↓
New → Qualified → Materials → Self-Tape → Audition
                                      ↓
                                  Callback
                                      ↓
                                    Offer
                                      ↓
                                    Booked
                                      ↓
                               Project / Role Draft


                         AI ASSISTANCE

Source
  ↓
AI Generated
  ↓
Human Review
  ↓
Approved / Modified / Rejected
  ↓
Apply
  ↓
Target Draft
  ↓
Separate Human Publish / Send


                      DERIVED PROCESSING

CURRENT
  ↓ source change
STALE
  ↓
REBUILDING
  ↓
CURRENT
```

---

# 309. Финальный принцип

> **State machine продукта должна отражать реальные профессиональные и технические события, а не удобство конкретного экрана. Публикация, подтверждение кастингового требования, переход Opportunity в Booked, применение AI-текста и доставка уведомления — это разные действия с разными владельцами и последствиями. Система может автоматически выполнять только проверяемую техническую работу; профессиональный смысл, публичное состояние и реальные бизнес-результаты остаются под явным человеческим контролем.**