# REVISIONS AND SNAPSHOTS

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная модель версий, ревизий, исторических снимков и воспроизводимости

**Целевой файл:** `docs/domain/revisions.md`  
**Документ:** DOC-064  
**Статус:** ✅ Completed  
**Тип:** Domain / Revisions / Versioning / Snapshots / History

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/forms-and-validation.md`
- `docs/ux/ui-states.md`
- `docs/domain/domain-model.md`
- `docs/domain/relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`

---

# 1. Назначение документа

Настоящий документ определяет единую модель versioning и revision history для продукта.

Документ отвечает на вопросы:

1. какие сущности требуют revision history;
2. чем Revision отличается от Entity Version;
3. чем Snapshot отличается от live reference;
4. что является immutable;
5. как работает Draft;
6. как создаётся Published Revision;
7. что происходит после изменения Source Data;
8. как работают historical PDFs;
9. как работает rollback;
10. как фиксируются AI analyses;
11. как версионируются Themes;
12. как версионируется Emotional Grid;
13. как работает Public Builder snapshot;
14. как решается concurrency;
15. как предотвращается silent overwrite;
16. как сохраняется provenance;
17. как поддерживается historical reproducibility;
18. где revisioning не нужен.

---

# 2. Главный принцип

> **Текущая профессиональная правда может изменяться, но уже опубликованная или значимая историческая версия не должна менять своё содержание задним числом.**

Canonical model:

```text
CURRENT SOURCE
     │
     ├──── changes over time
     │
     ↓
CURRENT DRAFT / CONFIGURATION
     │
     ↓
PUBLISH / GENERATE / ANALYZE
     │
     ↓
IMMUTABLE REVISION / SNAPSHOT
```

---

# 3. Revision IDs

Используется prefix:

```text
REV-*
```

Snapshot-specific:

```text
SNAP-*
```

Version-specific:

```text
VER-*
```

Invariants:

```text
REV-INV-*
```

---

# 4. Revision ≠ Version

Эти понятия MUST различаться.

## Entity Version

Технический concurrency/version indicator:

```text
version = 7
```

показывает, что mutable entity изменялась.

Используется для:

- optimistic locking;
- stale client detection;
- conflict prevention.

## Revision

Исторически значимая фиксированная версия содержимого.

Например:

```text
Questionnaire Revision 5
Theme Revision 12
Casting Analysis Revision 3
```

Revision имеет самостоятельное историческое значение.

---

# 5. Snapshot

Snapshot — зафиксированное состояние входных данных в конкретный момент.

Он может быть частью Revision, но не обязан быть отдельной user-visible revision.

Examples:

```text
BuilderGenerationSnapshot
AIDraft.source_snapshot
CastingAnalysisRevision.source_snapshot
QuestionnaireRevision.content_snapshot
```

---

# 6. Revision Categories

В продукте используются четыре основных категории:

```text
PUBLISHED REVISION
ANALYSIS REVISION
CONFIGURATION REVISION
GENERATION SNAPSHOT
```

---

# 7. Published Revision

Представляет официально опубликованное историческое состояние.

Основной пример:

```text
QuestionnaireRevision
```

---

# 8. Analysis Revision

Представляет результат анализа конкретной версии Source.

Example:

```text
CastingAnalysisRevision
```

---

# 9. Configuration Revision

Представляет сохранённую версию конфигурации.

Examples:

```text
ThemeRevision
EmotionalGrid finalized configuration/version
```

---

# 10. Generation Snapshot

Фиксирует входные данные, использованные для одного generated artifact.

Example:

```text
BuilderGenerationSnapshot
```

---

# 11. REV-INV-001 — Published Revision Is Immutable

После успешного перехода:

```text
READY
→ PUBLISHED
```

содержимое Published Revision MUST NOT изменяться обычным редактированием.

---

# 12. Editing Published Content

Редактирование после публикации создаёт:

```text
NEW WORKING DRAFT
```

для следующей Revision.

Не:

```text
UPDATE Revision N
```

---

# 13. Canonical Publication Model

```text
Questionnaire Definition
       │
       ↓
Current Draft
       │
       ↓
Validation
       │
       ↓
Publish
       │
       ↓
Revision 1
       │
       ├──── remains immutable
       │
       ↓
New edits
       │
       ↓
Current Draft
       │
       ↓
Publish
       │
       ↓
Revision 2
```

---

# 14. Revision Numbering

Within one logical revisioned entity, revision number SHOULD be:

```text
1, 2, 3, 4...
```

monotonic and unique.

---

# 15. Revision Number Is Not Database ID

Example:

```text
revision_id = UUID
revision_number = 7
```

They serve different purposes.

---

# 16. Revision Number Allocation

Revision number MUST be allocated atomically.

Two concurrent publishes MUST NOT both create:

```text
Revision 8
```

---

# 17. Revision Timestamp

Each Revision MUST record at least:

```text
created_at
```

and where semantically applicable:

```text
published_at
generated_at
```

---

# 18. Revision Actor

Significant Revision SHOULD record:

```text
created_by
published_by
```

or corresponding actor metadata.

---

# 19. Revision Provenance

Revision SHOULD identify:

```text
source entity
previous revision where relevant
source/config version
locale/config context
actor
generation/publication reason where applicable
```

---

# 20. Parent Logical Entity

Revision MUST belong to a stable logical parent.

Example:

```text
QuestionnaireDefinition
  ├─ Revision 1
  ├─ Revision 2
  └─ Revision 3
```

---

# 21. Revision Does Not Replace Parent Identity

Public questionnaire URL may continue to represent logical QuestionnaireDefinition while current published pointer changes.

Historical-specific route MAY target Revision if product exposes history.

---

# 22. Current Published Pointer

Revisioned publishable domain SHOULD maintain conceptually:

```text
current_published_revision_id
```

or equivalent deterministic lookup.

---

# 23. Pointer Change Is Atomic

Publication must not temporarily expose:

```text
new current revision pointer
+
incomplete revision
```

---

# 24. Previous Published Revision

When new Revision becomes current:

previous Revision remains historical.

It MAY be labelled:

```text
SUPERSEDED
```

as derived metadata.

Its content remains unchanged.

---

# 25. SUPERSEDED Is Not Deleted

Normative.

---

# 26. Draft Model

Draft is mutable working state.

Draft SHOULD NOT normally increment public revision number on every save.

---

# 27. Autosave

Autosave may increment entity technical `version`.

Example:

```text
Draft version 15
Draft version 16
```

but still belongs to future:

```text
Revision 8
```

until publish.

---

# 28. Draft Version ≠ Revision Number

Critical invariant.

---

# 29. Draft Persistence

Draft MUST survive ordinary:

- navigation;
- validation failure;
- optional service failure;

according to UX rules.

---

# 30. Draft May Be Incomplete

Draft can fail readiness while still being valid to save.

---

# 31. Publish Snapshot Boundary

At publication time, system MUST construct a stable publication snapshot before/within publication transaction.

This prevents source data changing halfway through generation.

---

# 32. Canonical Snapshot Sequence

```text
Validate Current Draft
      ↓
Validate Source Versions
      ↓
Create Frozen Snapshot
      ↓
Finalize Revision
      ↓
Generate/Queue Derived Artifacts
      ↓
Switch Current Published Pointer
```

Exact transaction split depends on architecture, but observable result MUST be consistent.

---

# 33. Snapshot Content Principle

Snapshot SHOULD contain all data necessary to reproduce the historical professional output without consulting mutable current Source fields.

---

# 34. Snapshot May Use Stable Immutable References

Not every byte/value must be duplicated.

Snapshot can reference an immutable object safely.

Example:

```text
MediaAsset immutable original
```

However context-specific mutable metadata SHOULD be frozen.

---

# 35. Safe Historical Reference

Potentially safe:

```text
MediaAsset original hash
immutable derivative
```

Potentially unsafe:

```text
live Project.title
live Contact.phone
live LanguageProficiency.level
```

for historical Questionnaire.

---

# 36. Snapshot Strategy Categories

For each field/reference choose:

```text
COPY VALUE
IMMUTABLE REFERENCE
VERSIONED REFERENCE
DERIVED COPY
```

---

# 37. COPY VALUE

Use for mutable professional facts needed in historical output.

Example:

```text
English B2
Project title
Contact display value
```

---

# 38. IMMUTABLE REFERENCE

Use where target is truly immutable.

Example:

```text
original MediaAsset hash
```

subject to retention/access rules.

---

# 39. VERSIONED REFERENCE

Use if target has its own Revision and exact Revision can be referenced.

---

# 40. DERIVED COPY

Use when actual generated representation is important.

Example:

```text
PDF-rendered Emotional Grid image
```

---

# 41. Snapshot Self-Consistency

A Revision MUST represent one coherent point-in-time/configuration view.

It MUST NOT mix:

```text
old Contact
new Language level
old Project title
```

due race conditions during render.

---

# 42. Source Version Capture

Snapshot SHOULD record versions/revisions of relevant Source entities where feasible.

Purpose:

- reproducibility;
- debugging;
- stale detection;
- provenance.

---

# 43. Questionnaire Revision Model

Questionnaire is the most important revisioned professional document.

Canonical model:

```text
QuestionnaireDefinition
     │
     ├─ Current Draft Configuration
     │
     ├─ Revision 1
     ├─ Revision 2
     └─ Revision N
```

---

# 44. QuestionnaireRevision Content

Must freeze at least all output-relevant:

```text
profile identification
selected professional facts
selected photos/artifact references
appearance parameters
skills
languages
Emotional Grid reference/snapshot
Projects/Roles
Training/Achievements
Video/Audio/Links
Contacts
block ordering
item ordering
localized copy
QR configuration
canonical URLs
document metadata
```

---

# 45. Historical Questionnaire Independence

After Revision creation:

```text
Profile changed
Contact changed
Project renamed
Skill changed
```

MUST NOT mutate the historical Revision.

---

# 46. Questionnaire Revision Locale Strategy

Two valid implementation strategies exist:

## Strategy A — Revision Per Locale

```text
Questionnaire Revision 5 RU
Questionnaire Revision 6 EN
```

## Strategy B — Multi-Locale Revision

One Revision contains approved localized values for multiple locales.

---

# 47. Canonical Recommendation

Prefer logical Revision containing one coherent publication generation, with locale-specific snapshot/render data explicitly versioned.

The implementation MUST ensure:

```text
RU historical output
EN historical output
```

remain reproducible.

Exact DB layout deferred to DOC-090/091.

---

# 48. Localized Mutation

Changing EN translation after publication MUST create a new relevant Revision/publication state.

It must not rewrite old EN PDF.

---

# 49. Primary Questionnaire and Revisions

`Primary` belongs logical QuestionnaireDefinition/public selection.

It does not mean only one historical Revision exists.

---

# 50. Primary Switch

When another Questionnaire becomes Primary:

historical Revisions of previous primary remain intact.

---

# 51. Questionnaire Artifact Revision Binding

Every published PDF MUST bind to exact:

```text
questionnaire_revision_id
locale
render version
generated_at
```

---

# 52. PDF Regeneration

Regenerating PDF from same immutable Revision SHOULD reproduce semantically identical content.

Minor renderer-level differences MAY occur if renderer changes, unless archival binary preservation is required.

---

# 53. Render Version

Generated artifact SHOULD record:

```text
renderer_version/template_version
```

where needed for reproducibility/debugging.

---

# 54. Historical Binary Preservation

For important published Questionnaire PDFs, system SHOULD consider storing generated binary rather than relying only on future regeneration.

Reason:

- layout engine may evolve;
- fonts may change;
- renderer may change;
- exact historical appearance may matter.

---

# 55. Binary vs Semantic Reproducibility

Two levels:

## Semantic reproducibility

Same facts/links/configuration.

## Pixel/binary reproducibility

Exactly same visual/PDF bytes.

The product MUST guarantee semantic reproducibility.

Exact binary reproducibility is desirable for retained published artifacts but not universally required unless later specified.

---

# 56. QR in Historical Questionnaire

Historical Revision MUST retain the canonical URL encoded at publication.

If current ProfessionalLink URL changes:

old Revision QR MUST NOT silently point to new URL.

---

# 57. Historical QR Validity

If old target is intentionally retired:

historical PDF may contain a now-unavailable link.

System MAY retain redirect according to URL policy.

It MUST NOT rewrite QR inside historical PDF silently.

---

# 58. Privacy Override

If a historical link/contact must be removed for privacy/security reasons:

controlled redaction MAY override immutability.

This is not ordinary revision editing.

---

# 59. Redaction Model

Historical redaction MUST record:

```text
revision_id
redacted field/artifact
reason
actor
timestamp
policy/legal basis where applicable
```

---

# 60. Redaction Must Be Visible Internally

Admin/Audit should be able to distinguish:

```text
original historical snapshot
+
controlled redaction
```

subject to retention/security policy.

---

# 61. Public Builder Snapshot Model

`PublicBuilderSession` is mutable and temporary.

`BuilderGenerationSnapshot` is immutable.

---

# 62. Builder Generation Flow

```text
Builder Session
    ↓
current selections
    ↓
server revalidation
    ↓
Generation Snapshot
    ↓
PDF
```

---

# 63. Multiple Builder Generations

One Session MAY produce:

```text
Snapshot 1
Snapshot 2
Snapshot 3
```

Each corresponds to exact generation state.

---

# 64. Builder Snapshot Numbering

Builder snapshots do not need professional Revision numbering.

They SHOULD have:

```text
generation_id
created_at
session_id
```

and optionally sequential generation number for observability.

---

# 65. Builder Snapshot Lifetime

Retention may be shorter than Prepared Questionnaire revisions.

Defined later in DOC-094.

---

# 66. Downloaded Builder PDF

Once generated:

changes to Session afterward MUST NOT mutate previously generated artifact.

---

# 67. Builder Snapshot Source Change

If Source changes after Snapshot:

previous generated PDF remains historical generation.

Next generation uses current authorized Source after revalidation.

---

# 68. Builder Snapshot Permission Change

If Contact permission is revoked after Snapshot:

future download/access policy may need privacy enforcement.

The immutable snapshot semantics do NOT override privacy policy.

Retention/access exact rule defined later.

---

# 69. Casting Analysis Revision Model

Every meaningful AI analysis pass MUST create a new:

```text
CastingAnalysisRevision
```

---

# 70. Analysis Regeneration

Prohibited:

```text
overwrite previous extracted requirements
```

Canonical:

```text
Analysis Revision 1
Analysis Revision 2
Analysis Revision 3
```

---

# 71. Analysis Revision Source Snapshot

Must preserve:

```text
CastingSource content/version
relevant Profile snapshot/version
analysis configuration
prompt version
model metadata
generation timestamp
```

---

# 72. Why Profile Snapshot Matters

Example:

Analysis #1:

```text
English B2
```

Later Profile:

```text
English C1
```

Reopening Analysis #1 should explain why old MatchResult was produced.

---

# 73. Casting Analysis Match Reproducibility

Historical AnalysisRevision SHOULD retain:

- source requirements;
- Profile fact snapshot or source versions;
- computed MatchResult;
- AI recommendation.

---

# 74. Human Review and Analysis Revision

Human confirmation MUST NOT rewrite original AI extraction provenance.

Recommended logical structure:

```text
AI extracted value
Human confirmed/modified value
Human decision metadata
```

inside/attached to analysis review history.

---

# 75. New Human Review After New Analysis

A new AnalysisRevision should receive its own confirmation/review state.

Do not automatically copy old confirmations blindly.

System MAY propose carry-forward if Source requirement is deterministically unchanged, but Human review authority remains.

---

# 76. Requirement Stable Identity

Requirement may have logical identity across revisions if architecture supports matching.

However historical extracted/confirmed values remain revision-specific.

---

# 77. AIDraft Revision Semantics

`AIDraft` itself provides history of one generation.

Regenerate SHOULD preferably create:

```text
new AIDraft
```

rather than overwrite original generated text.

---

# 78. AIDraft Generated Text

`generated_text` SHOULD remain immutable provenance.

Human edit can be stored separately:

```text
edited_text
```

or revision history.

---

# 79. BB Assistant Regeneration

Example:

```text
AIDraft #1
AIDraft #2
AIDraft #3
```

allows comparison/discard.

---

# 80. Applied AIDraft

After Apply:

AIDraft remains historical provenance.

Target Draft may then diverge independently.

---

# 81. Target Change Does Not Rewrite AIDraft

Normative.

---

# 82. Stale AIDraft

AIDraft becomes stale when relevant Source changes.

Stale is calculated/recorded relation to source version.

It does not change original generated content.

---

# 83. Theme Revision Model

`SiteTheme` has immutable/configured ThemeRevisions.

Canonical:

```text
SiteTheme
 ├─ Revision 1
 ├─ Revision 2
 └─ Revision 3
```

---

# 84. Theme Draft

Current editable Theme configuration MUST be separate from already Published Revision.

---

# 85. Theme Publish

Creates/finalizes a ThemeRevision.

Activation then targets exact ThemeRevision.

---

# 86. Theme Rollback

Rollback MUST NOT edit current revision.

Correct:

```text
activate previous ThemeRevision
```

or create new Revision cloned from previous if publication model requires.

---

# 87. Theme History

Must allow answering:

```text
Which visual configuration was active on date X?
```

where activation history retained.

---

# 88. Temporary Theme

Temporary activation references exact ThemeRevision.

Expiry switches effective activation.

It does not generate an invisible mutable variant.

---

# 89. Theme Proposal

AI `ThemeProposal` is not ThemeRevision unless explicitly applied to Draft.

---

# 90. Emotional Grid Revision Semantics

EmotionalGrid must preserve source configuration after finalized/published output.

---

# 91. Grid Draft Editing

Before finalization:

same working Grid may mutate.

---

# 92. Grid Finalization Boundary

At:

```text
READY_TO_FINALIZE
→ FINALIZED
```

system MUST freeze exact:

```text
dimensions
cell order
source media
crop/pan/scale/rotation
confirmation state
source media version/hash
```

used to generate artifacts.

---

# 93. Grid Edit After Finalization

Material change after Finalized/Published MUST create:

```text
new Grid configuration revision
```

or new EmotionalGrid derived from prior configuration.

Implementation choice later.

---

# 94. Canonical Recommendation for Grid

Use stable logical EmotionalGrid plus versioned/finalized GridRevision if implementation complexity is acceptable.

Conceptual:

```text
EmotionalGrid
  ├─ GridRevision 1
  └─ GridRevision 2
```

Even if physical DB initially stores finalized copies as separate Grid records, semantics MUST preserve history.

---

# 95. Grid Artifacts Bind to Exact Configuration Revision

No artifact may ambiguously reference “current Grid”.

It must map to exact finalized configuration.

---

# 96. Emotional Source Replacement

If MediaAsset original is replaced through controlled workflow:

old GridRevision must continue to reference original historical source/version where retained.

---

# 97. Portfolio Revisioning

Ordinary PortfolioItem edits do NOT necessarily require full formal revisions.

Use:

```text
entity version
audit history
```

for normal changes.

---

# 98. Formal Portfolio Revision Required When

Only if future requirements demand historical replay of full public Portfolio versions.

Current product does not require dedicated `PortfolioRevision` entity.

---

# 99. Project Revisioning

Projects/Roles likewise primarily use:

```text
current mutable Source
+
Audit
+
historical Questionnaire snapshots
```

not dedicated ProjectRevision by default.

---

# 100. Why Not Revision Everything

Excessive revision entities create complexity without business value.

Formal Revision required when one or more are true:

```text
published document must remain immutable
historical generated output depends on state
AI analysis must be explainable later
visual configuration must rollback atomically
regulatory/business history needs exact reconstruction
```

---

# 101. Entities Requiring Formal Revision/Snapshot

Canonical:

```text
QuestionnaireRevision — YES
BuilderGenerationSnapshot — YES
CastingAnalysisRevision — YES
ThemeRevision — YES
EmotionalGrid finalized version — YES conceptually
AIDraft generation history — YES via immutable draft records
```

---

# 102. Entities Primarily Using Version + Audit

Canonical:

```text
ActorProfile
PortfolioItem
Project
Role
Training
Achievement
ActorSkill
ActorLanguageProficiency
ProfessionalLink
Contact
Feedback
Casting
Opportunity
```

unless later module rules add formal revisions.

---

# 103. Entity Version Model

Mutable authoritative entities SHOULD support optimistic concurrency.

Conceptually:

```text
version: integer
```

---

# 104. Version Increment

Every authoritative mutation increments:

```text
version N
→ version N+1
```

atomically.

---

# 105. Updated At Is Not Sufficient Alone

`updated_at` MAY support concurrency, but explicit version/ETag is safer for precise conflict detection.

Final implementation later.

---

# 106. Expected Version

Mutation MAY include:

```text
expected_version = 12
```

Server validates current version remains 12.

---

# 107. Conflict

If current version:

```text
13
```

server rejects silent overwrite.

---

# 108. No Last-Write-Wins for Professional Facts

High-value Admin edits MUST NOT use uncontrolled last-write-wins semantics.

---

# 109. Concurrency Scope

Especially required for:

```text
Profile facts
Contact visibility
Primary selections
Questionnaire Draft
Casting review
Opportunity stage
Theme Draft/publish
```

---

# 110. Draft Concurrency

Two users/tabs editing same Draft need conflict handling.

Current product may often have one Admin, but architecture MUST remain safe for:

- multiple tabs;
- automated jobs;
- future collaborators.

---

# 111. AI Concurrency

AI output generated from Source version N must be checked before Apply against current Source/target version.

---

# 112. Stale AI Apply

If target changed:

system MUST NOT silently overwrite newer target state.

---

# 113. Publish Concurrency

Two simultaneous Questionnaire publish requests MUST result in:

- one consistent revision sequence;
- no duplicate revision numbers;
- no ambiguous current published pointer.

---

# 114. Primary Marker Concurrency

Changing Primary entity must use transactional uniqueness.

Examples:

```text
Primary Close-Up
Primary Full Body
Primary Questionnaire
Primary Emotional Grid
```

---

# 115. Rollback Definition

Rollback means:

> restore an earlier known configuration/content as the basis of a new current state without mutating historical records.

---

# 116. Rollback ≠ Time Travel Mutation

Prohibited:

```text
delete Revision 7
make Revision 4 mutable/current by editing it
```

---

# 117. Questionnaire Rollback

Recommended:

```text
Revision 7 current
↓
Admin selects Revision 4
↓
Create new Draft copied from Revision 4
↓
Review
↓
Publish as Revision 8
```

---

# 118. Why Create Revision 8

Because history becomes:

```text
R4 original historical publication
R5
R6
R7
R8 restored-from-R4
```

This is auditable.

---

# 119. Direct Revision Reactivation

A system MAY support re-pointing current published pointer to an old immutable revision for emergency rollback.

If used:

- exact operation explicit;
- Audit mandatory;
- compatibility/security validated;
- public artifacts still available.

For normal editorial rollback, clone-to-new-Draft is preferred.

---

# 120. Theme Rollback

Theme may more naturally reactivate an earlier immutable ThemeRevision directly.

Because ThemeRevision is complete visual configuration and activation is separate.

Still Audit required.

---

# 121. Casting Analysis Rollback

Analysis is historical interpretation.

Do not “roll back” current Source.

Admin MAY mark an older AnalysisRevision as reference/current-reviewed view, but new analysis/history remains preserved.

---

# 122. Builder Rollback

Not needed.

Visitor may change Session selections or start new Session.

Generation Snapshots remain immutable.

---

# 123. Revision Branching

Full Git-like branching is NOT required.

Canonical history is predominantly linear per logical document/configuration.

---

# 124. Draft Fork

System MAY allow:

```text
Create new Questionnaire from Revision
```

This creates new logical QuestionnaireDefinition, not branch of same revision history unless explicitly chosen.

---

# 125. Copy vs Revision

`Duplicate Questionnaire`:

```text
new QuestionnaireDefinition
```

`Edit published Questionnaire`:

```text
same QuestionnaireDefinition
new Draft → next Revision
```

---

# 126. Copy Provenance

Duplicated entity MAY record:

```text
copied_from_questionnaire_id/revision_id
```

but has independent future revision sequence.

---

# 127. Revision Deletion

Published Revisions SHOULD generally not be deletable through normal UI.

---

# 128. Draft Revision Cleanup

Abandoned temporary Draft snapshots may be cleaned according to retention policy.

---

# 129. Failed Publication Attempts

Failed publish attempt SHOULD NOT consume public revision number unless implementation deliberately allocates number before final commit.

Preferred:

```text
revision number allocated/finalized only on successful committed revision
```

---

# 130. Gaps in Revision Number

If database implementation naturally causes gaps due transaction rollback, gaps MAY be acceptable technically.

UI MUST NOT imply missing content merely because numbers skip.

Prefer no gaps when straightforward.

---

# 131. Audit and Revision

Revision history answers:

```text
what content version existed?
```

Audit answers:

```text
who did what and when?
```

Both may be needed.

---

# 132. Revision Does Not Replace Audit

Publishing Revision 8 still requires AuditEvent:

```text
QuestionnairePublished
revision=8
actor=...
```

---

# 133. Audit Does Not Replace Revision

Audit deltas alone are not preferred source for reconstructing a published Questionnaire.

---

# 134. Revision Diff

Admin SHOULD eventually support comparing significant revisions.

Especially:

```text
Questionnaire Revision
Theme Revision
Casting Analysis Revision
```

---

# 135. Diff Semantics

Diff should be domain-aware.

Questionnaire example:

```text
Language English: B2 → C1
Added Project X
Removed Contact Y
QR enabled for Video Intro
```

not only raw JSON diff.

---

# 136. Theme Diff

Examples:

```text
primary typography changed
hero spacing changed
background token changed
```

---

# 137. Analysis Diff

Examples:

```text
new requirement extracted
requirement changed
profile match changed
```

---

# 138. Diff Is Derived

Diff can be recalculated from revisions.

It is not authoritative content.

---

# 139. Revision Labels

Admin MAY attach optional label/comment:

```text
"Updated showreel and English level"
```

for revision history.

This does not replace system-generated metadata.

---

# 140. Revision Comment

Should be optional unless change governance requires mandatory reason.

High-impact rollback MAY require reason.

---

# 141. Historical View

Viewing Revision MUST clearly indicate:

```text
Historical Revision
Revision #
Published date
Read-only
```

---

# 142. Historical View Must Not Look Editable

Do not render ordinary editable form with disabled fields if a semantic read-only representation is clearer.

---

# 143. Historical Public Access

Whether old Revision is publicly addressable is product/security policy.

Current requirement only mandates internal historical preservation and public current selection.

---

# 144. Shared Old Questionnaire Link

If a stable published Revision URL was externally shared, route strategy SHOULD avoid silently changing content behind it.

Preferred patterns:

```text
logical current URL → current Revision
revision-specific URL/token → fixed Revision
```

Exact routes later.

---

# 145. Casting-Specific Shared Questionnaire

If temporary/unlisted:

its revision/access lifecycle must preserve fixed content for intended validity window.

Security/access policy later.

---

# 146. Revision and Slug Changes

Changing public slug must not alter Revision contents.

Historical snapshot can retain source URL at generation time where needed.

---

# 147. Revision and Canonical Profile URL

Generated PDF footer uses profile URL at generation/publication.

If domain changes later, redirect strategy SHOULD preserve usefulness.

---

# 148. Revision and Contact Privacy

Contact historical snapshot is sensitive.

Storage/access retention MUST obey privacy rules even though document is immutable.

---

# 149. Revision and Source Deletion

Deleting/retiring mutable Source does not automatically invalidate historical Revision.

---

# 150. But Historical Dependency Must Remain Resolvable

Options include:

```text
snapshot value
retained immutable media
retained derivative
redacted historical representation
```

---

# 151. Historical Media Strategy

For published professional documents, exact media used SHOULD be retainable through:

- immutable MediaAsset;
- immutable derivative;
- revision artifact.

---

# 152. Media Retention Conflict

If original MediaAsset must be removed for privacy/legal reason:

historical artifact handling follows deletion/redaction policy.

---

# 153. Revision and Background Jobs

Async generation job MUST identify exact Revision/Snapshot.

Never:

```text
generate PDF for "current Questionnaire"
```

without resolving/fixing exact input version first.

---

# 154. Correct Background Job

```text
GenerateQuestionnairePdf(
  revision_id = X,
  locale = EN,
  renderer_version = Y
)
```

---

# 155. Incorrect Background Job

```text
GenerateQuestionnairePdf(questionnaire_id)
```

if worker later resolves mutable live Draft/current source ambiguously.

---

# 156. Revision Job Idempotency

Same:

```text
revision + locale + artifact type + renderer/config version
```

SHOULD have idempotent generation behaviour.

---

# 157. Artifact Cache Key

Conceptually:

```text
revision_id
locale
artifact_type
render_settings_version
```

---

# 158. QR Cache Key

Separate:

```text
canonical_url
QR_settings_version
```

---

# 159. Revision Schema Version

Snapshot/configuration payload SHOULD store:

```text
schema_version
```

where JSON/structured config may evolve.

---

# 160. Why Schema Version

Future application must interpret old Revision correctly even after configuration schema changes.

---

# 161. Snapshot Migration

Historical snapshot SHOULD generally not be destructively rewritten to newest schema.

Preferred:

```text
read old schema through compatibility adapter
```

or controlled lossless migration with provenance.

---

# 162. Snapshot Migration Rule

If migration is necessary:

- preserve semantics;
- retain original version/provenance;
- test historical rendering.

---

# 163. Revision Render Compatibility

Renderer SHOULD support retained relevant historical schema versions or normalized compatibility representation.

---

# 164. Migration Failure

Historical Revision must not become unreadable silently.

VOP/System Health should surface compatibility failures.

---

# 165. Revision and Localization Schema

Locale identifiers SHOULD be canonical stable codes.

Historical Revision should not depend on mutable UI translation keys for professional content.

---

# 166. UI Labels vs Content

System UI label:

```text
Language
```

may translate using current application locale resources.

Professional snapshot value:

```text
English — B2
```

must retain historical data.

---

# 167. Revision and Enumeration Changes

If enum display names change later, historical semantic value remains identifiable.

Store stable enum code plus snapshot display where professional rendering needs historical wording.

---

# 168. Revision and Time

All Revision timestamps MUST have unambiguous timezone semantics.

Persist canonical timestamp form.

Display according to Admin/public locale/timezone rules.

---

# 169. Shooting Date

Date-only professional fact remains date-only.

Snapshot must not turn it into timezone-shiftable timestamp.

---

# 170. Revision and Source Attribution

A generated Revision should be able to indicate internally:

```text
which Profile/entity versions contributed
```

for diagnosis.

This metadata need not be public.

---

# 171. Revision Dependency Map

Example QuestionnaireRevision:

```text
QuestionnaireRevision
├─ ActorProfile snapshot
├─ Portfolio snapshot references
├─ EmotionalGridRevision
├─ Projects/Roles snapshot
├─ Skills/Languages snapshot
├─ Contacts snapshot
├─ ProfessionalLinks snapshot
├─ QR config snapshot
└─ locale/layout config
```

---

# 172. Revision Ownership

Revision belongs to domain that created it.

QuestionnaireRevision:

```text
Questionnaire Domain
```

ThemeRevision:

```text
Theme Domain
```

AnalysisRevision:

```text
Casting AI Domain
```

---

# 173. Cross-Domain Snapshot Does Not Transfer Ownership

Questionnaire snapshot includes Project data.

Project domain still owns current Project.

Revision owns only historical copy.

---

# 174. Revision Freshness

Published historical Revision is never `STALE` merely because current Source changed.

---

# 175. Current Draft May Become Stale

A Draft/configuration MAY be stale relative to changed Source references.

---

# 176. AI Analysis May Become Stale

Because its analytical usefulness depends on current source/profile state.

Historical analysis remains valid as history.

---

# 177. Builder Session May Become Stale/Invalid

Because it is intended to generate current allowed output.

---

# 178. Theme Revision Does Not Become Stale From Content Changes

Theme config remains same.

Its compatibility/readiness MAY require revalidation if component/design contract changes.

---

# 179. Revision Validity vs Relevance

Historical content can be:

```text
valid historical record
but no longer current/relevant
```

These concepts must not be conflated.

---

# 180. Revision Expiry

Formal published Revision generally does not expire automatically.

Temporary access token/link may expire independently.

---

# 181. Snapshot Expiry

BuilderGenerationSnapshot MAY expire per retention policy.

---

# 182. Casting Analysis Revision Retention

Should normally retain analysis history while Casting record retained, subject to privacy policy.

---

# 183. AIDraft Retention

Applied/discarded AI drafts retention may be bounded.

Exact policy DOC-094.

---

# 184. Theme Revision Retention

Published ThemeRevisions SHOULD be retained for rollback/history.

---

# 185. Draft Cleanup

System MAY clean:

- abandoned unsaved temporary UI drafts;
- expired Builder sessions;
- transient failed generation state;

according to retention.

It MUST NOT remove current active Draft unexpectedly.

---

# 186. Revision Restoration After Data Loss

Backups SHOULD restore:

```text
logical parent
revisions
current published pointer
artifacts or ability to regenerate
```

consistent together.

Operations docs later.

---

# 187. Backup Consistency

Restoring database Revision without matching retained artifacts must trigger artifact regeneration/availability handling.

---

# 188. Revision and External CDN/Storage

Stored artifact key SHOULD include stable revision/generation identity.

Avoid overwriting one generic:

```text
questionnaire.pdf
```

for every version if historical preservation required.

---

# 189. Example Artifact Path Concept

Not normative filesystem syntax:

```text
questionnaires/{questionnaire-id}/revisions/{revision}/en/pdf/...
```

---

# 190. Public Alias

A stable current URL MAY point to current artifact/revision:

```text
/questionnaire/main
```

while internal storage remains revision-specific.

---

# 191. Revision and CDN Invalidation

New current publication may invalidate alias/current cache.

Historical revision-specific resources SHOULD remain immutable-cache friendly.

---

# 192. Revision Security

Access policy must apply independently from immutability.

Immutable does not mean universally readable.

---

# 193. Private Casting Questionnaire Revision

Can be immutable yet require:

```text
valid access token
not expired
```

---

# 194. Revision Token Rotation

Rotating/revoking access token does not modify document revision content.

---

# 195. Revision and Audit Correlation

Publication operation SHOULD use correlation identifier linking:

- command;
- Revision;
- artifact jobs;
- AuditEvent.

---

# 196. Revision and Notification

If publication triggers notification, notification should reference exact published Revision where relevant.

---

# 197. Revision and Analytics

Analytics may record Revision/template identifier if privacy-safe.

It must not change revision state.

---

# 198. Revision and VOP

VOP may detect:

```text
current Questionnaire Draft stale
artifact generation failed
old ThemeRevision incompatible
```

It may recommend rebuild/review.

---

# 199. VOP Cannot Rewrite Historical Revision

Normative.

---

# 200. Revision and Manual Repair

If corrupted historical metadata requires repair:

use controlled administrative/migration procedure.

Ordinary editor must not expose historical mutation.

---

# 201. Repair Provenance

Repair should record:

```text
original value
new repaired technical value
reason
actor/migration
timestamp
```

---

# 202. Semantic Historical Correction

If a Published Questionnaire contained incorrect professional data, preferred correction:

```text
publish new Revision
```

not silently edit old.

---

# 203. Correction Notice

If old historical Revision remains accessible externally, product MAY later support marking:

```text
superseded/corrected by Revision N
```

without changing original content.

---

# 204. Revision Diff Base

By default compare:

```text
Revision N
vs
Revision N-1
```

Admin MAY choose arbitrary two revisions.

---

# 205. Draft Diff

Useful comparison:

```text
Current Draft
vs
Current Published Revision
```

---

# 206. Draft Diff Use Case

Answers:

```text
What will change if I publish now?
```

Important for Questionnaires/Themes.

---

# 207. Publish Confirmation

High-value publish UI SHOULD summarize significant changes where feasible.

---

# 208. Revision and Human Review

A Revision becoming PUBLISHED implies required human publication decision occurred.

It does not imply every Source fact was manually re-reviewed at that exact moment if existing confirmed facts reused.

---

# 209. AI-Generated Content Inside Revision

A QuestionnaireRevision MAY contain copy originally generated by BB Assistant.

Once human applied/published:

snapshot records final professional text.

Provenance may indicate AI origin internally.

---

# 210. AI Origin Does Not Make Published Text Mutable

Normative.

---

# 211. Revision and Derived Readiness

Readiness used to permit publication should be recorded optionally as publication validation result.

It need not remain recalculated against future Source.

---

# 212. Publication Validation Record

For critical docs SHOULD record:

```text
validation schema/version
result
warnings
timestamp
```

for auditability.

---

# 213. Historical Readiness

A historical Revision should not become “blocked” because current rules changed.

It may be marked:

```text
created under validation version X
```

and still render historically.

---

# 214. Security Exception

A newly discovered serious security/privacy violation can block serving old Revision despite historical validity.

Again:

```text
access/policy override
!= content mutation
```

---

# 215. Revision and Business Rule Evolution

Changing current business rules does not retroactively make historical Revision invalid as a record.

---

# 216. But New Generation Uses Current Rules

Regenerating a new output/Draft must pass current rules unless explicit historical reproduction mode is used.

---

# 217. Historical Reproduction Mode

When regenerating a historical artifact, system SHOULD use:

```text
historical snapshot
historical config/schema compatibility
```

not current Source validation that changes content.

---

# 218. Current Publication Mode

Uses current rules.

These modes are distinct.

---

# 219. Revision API Semantics

Conceptual commands SHOULD include:

```text
saveDraft
validateDraft
publishRevision
getRevision
listRevisions
compareRevisions
restoreRevisionToDraft
```

Exact API later.

---

# 220. Prohibited API

Avoid normal:

```text
updatePublishedRevision()
```

---

# 221. Historical Read Endpoint

Read-only.

---

# 222. Revision List Ordering

Default:

```text
newest first
```

Admin should show:

- revision number;
- date;
- status/current marker;
- author;
- optional summary.

---

# 223. Current Marker

Exactly one applicable current published revision per logical published document.

---

# 224. No Current Published Revision

Valid when:

```text
Questionnaire has never been published
```

or all active publication removed/archived.

Public UI uses appropriate unavailable state.

---

# 225. Archive Parent Questionnaire

Historical revisions remain.

Current public pointer is removed from active chooser.

---

# 226. Unarchive

Should restore logical Questionnaire to Draft/active management.

Must not necessarily republish old Revision automatically.

---

# 227. Revision and Primary Marker

Archiving current Primary Questionnaire MUST require selecting replacement or intentionally entering no-primary/unavailable state according to business rule.

If public Questionnaire is required, readiness prevents invalid state.

---

# 228. Revision and Publication Atomicity

A visitor MUST never observe a half-published Revision.

---

# 229. Atomic Publication Observable Contract

Public sees either:

```text
Revision N
```

or:

```text
Revision N+1
```

not partial mixture.

---

# 230. Snapshot Isolation

Publication should read consistent source versions.

Architecture may implement:

- transaction snapshot;
- explicit source version verification;
- prebuilt immutable publication payload.

---

# 231. Source Mutation During Publication

If relevant Source changes before commit:

system SHOULD:

```text
abort/revalidate
```

rather than publish mixed state.

---

# 232. Post-Snapshot Source Mutation

If immutable snapshot already accepted transactionally:

publication MAY proceed based on frozen snapshot according to workflow.

Current Source then differs, which is valid.

---

# 233. Revision Hash

Important revisions/artifacts MAY store content/config hash.

Useful for:

- deduplication;
- integrity;
- idempotency;
- reproducibility.

---

# 234. Hash Is Technical Metadata

Not user-facing revision identity.

---

# 235. Duplicate Publication

If Draft content hash equals current Published Revision:

system MAY warn:

```text
No meaningful changes to publish
```

and avoid unnecessary Revision.

But exact policy is product choice.

---

# 236. No-Change Revision

Generally SHOULD be avoided unless explicit business reason.

---

# 237. Semantically Meaningful Diff

Different technical serialization/order that renders identically should not necessarily force user-visible Revision.

Canonical normalization may help.

---

# 238. Revision and Ordering

Order is part of snapshot when output order matters.

Examples:

- Questionnaire blocks;
- selected Projects;
- Contacts;
- Links.

---

# 239. Revision and Visibility

Effective visibility used at publication belongs snapshot.

Changing source visibility later affects new publications, not historical content unless privacy override applies.

---

# 240. Revision and Primary Media

Questionnaire historical snapshot should retain photo used at publication, even if current Primary Close-Up changes later.

---

# 241. Current Questionnaire Draft

If configured to “use current primary photo dynamically”, current Draft preview may update when Primary changes.

At publish, selected effective media is frozen.

---

# 242. Explicit vs Dynamic References

Draft configuration MAY support:

```text
EXPLICIT_ITEM_REFERENCE
DYNAMIC_PRIMARY_REFERENCE
```

if product later chooses.

At snapshot boundary both resolve to exact output value/reference.

---

# 243. Recommended Questionnaire Behaviour

For professional stability, Admin questionnaire should expose clear selected items rather than invisible dynamic substitutions when possible.

---

# 244. Public Builder Uses Current Effective Data

Unlike historical Prepared Revision, Builder uses current permitted Source at generation.

Then freezes it into GenerationSnapshot.

---

# 245. Revision and Derived Emotional Grid

Questionnaire snapshot should reference exact finalized Grid version/artifact.

Not generic:

```text
current primary emotional grid
```

after publication.

---

# 246. Revision and Link Health

Historical snapshot captures URL.

Current health check can be displayed operationally but does not rewrite Revision.

---

# 247. Broken Historical Link

VOP may report:

```text
Revision X contains currently unreachable URL
```

But ordinary repair should create new current Revision or redirect target, not mutate PDF.

---

# 248. Revision and Redirects

Redirect infrastructure can preserve old URLs without document mutation.

Preferred for canonical route changes.

---

# 249. Revision Retention Classes

Conceptual retention classes:

```text
PERMANENT/PROFESSIONAL_HISTORY
LONG_TERM
TEMPORARY
DERIVED_REGENERABLE
```

Exact durations DOC-094.

---

# 250. Likely Permanent/Long-Term

- Published QuestionnaireRevision;
- Opportunity stage history;
- published ThemeRevision;
- CastingAnalysisRevision while business record retained;
- Audit related to publication/business decisions.

---

# 251. Temporary

- BuilderSession;
- transient previews;
- failed draft jobs.

---

# 252. Derived Regenerable

- thumbnails;
- some QR caches;
- search indexes.

---

# 253. Revision Export

Future administrative export SHOULD be able to include:

```text
revision metadata
snapshot data
artifact references
provenance
```

for portability/backups.

---

# 254. Revision Import

Importing historical Revision must preserve:

- original identity mapping;
- numbering or source numbering metadata;
- timestamps where trusted;
- snapshot semantics.

---

# 255. Migration From Existing Implementation

Existing Questionnaire variants/PDFs need audit to determine:

```text
which currently behave as mutable records
which have historical copies
which need backfilled revision history
```

---

# 256. Backfill Principle

Do not invent detailed historical Revisions that were never stored.

Possible migration:

```text
current known state → baseline Revision 1
```

with migration metadata:

```text
history_before_migration = unavailable
```

---

# 257. Existing Published Questionnaire Baseline

If current implementation has one current published questionnaire but no Revision history:

migration SHOULD create a baseline snapshot representing current known state.

---

# 258. Existing Emotional Grids

If finalized composites already exist without version metadata:

migration SHOULD establish:

- source relation where recoverable;
- current configuration;
- baseline finalized version.

Do not fabricate unavailable crop history.

---

# 259. Existing Theme

Current theme can become baseline ThemeRevision.

---

# 260. Existing AI Drafts

If current implementation only stores transient generated text:

no fake history required.

New model starts revision/provenance tracking after migration.

---

# 261. Revision Anti-Pattern REV-AP-001

**Editing Published Revision in place**

---

# 262. REV-AP-002

**Using `updated_at` as revision history**

---

# 263. REV-AP-003

**Incrementing revision number on every autosave**

---

# 264. REV-AP-004

**Historical Questionnaire reads current Profile data**

---

# 265. REV-AP-005

**Historical PDF QR changes when current URL changes**

---

# 266. REV-AP-006

**Rollback deletes newer Revisions**

---

# 267. REV-AP-007

**Theme rollback mutates current ThemeRevision contents**

---

# 268. REV-AP-008

**Casting AI regeneration overwrites prior Analysis**

---

# 269. REV-AP-009

**AI source snapshot omitted**

making historical analysis unexplained.

---

# 270. REV-AP-010

**Builder PDF continues changing after download because it references live data**

---

# 271. REV-AP-011

**Grid artifact points to current mutable Grid**

rather than exact finalized configuration.

---

# 272. REV-AP-012

**Two concurrent publishes receive same revision number**

---

# 273. REV-AP-013

**Last write silently overwrites newer Admin changes**

---

# 274. REV-AP-014

**Historical Revision marked stale because Profile changed**

---

# 275. REV-AP-015

**Every entity gets full revision history regardless of business value**

---

# 276. REV-AP-016

**Audit log used as sole document snapshot**

---

# 277. REV-AP-017

**Snapshot copied without schema version**

for evolving JSON format.

---

# 278. REV-AP-018

**Old Revision silently migrated to new semantics**

---

# 279. REV-AP-019

**Public Builder Session treated as permanent Questionnaire Revision**

---

# 280. REV-AP-020

**Privacy-redacted Revision presented internally as if nothing changed**

---

# 281. Revision Quality Gate

Before implementing a revisioned domain, MUST define:

- [ ] logical parent;
- [ ] revision identity;
- [ ] revision number policy;
- [ ] mutable Draft boundary;
- [ ] immutable boundary;
- [ ] publication/generation trigger;
- [ ] source snapshot strategy;
- [ ] locale strategy;
- [ ] media strategy;
- [ ] URL/QR strategy;
- [ ] schema version;
- [ ] current pointer;
- [ ] rollback behaviour;
- [ ] artifact binding;
- [ ] concurrency;
- [ ] idempotency;
- [ ] retention;
- [ ] privacy redaction policy;
- [ ] audit events;
- [ ] migration/backfill.

---

# 282. Snapshot Field Quality Gate

For every output field ask:

```text
Can this Source value change later?
```

If YES:

determine whether output requires historical value.

If YES:

```text
snapshot/copy/versioned reference required
```

---

# 283. Revision Specification Template

```text
Revision Type:
QuestionnaireRevision

Parent:
QuestionnaireDefinition

Mutable Before Publish:
Yes

Immutable After Publish:
Yes

Revision Number:
Monotonic per parent

Snapshot Inputs:
...

Artifacts:
...

Current Pointer:
...

Rollback:
Restore to new Draft

Concurrency:
Expected parent/draft version

Audit:
Publish / restore / archive / primary switch

Retention:
Long-term
```

---

# 284. Snapshot Specification Template

```text
Snapshot:
BuilderGenerationSnapshot

Source:
PublicBuilderSession + current authorized Source

Trigger:
PDF generation

Immutable:
Yes

Publicly Editable:
No

Can Generate Multiple Per Session:
Yes

Retention:
Temporary/defined by policy

Source Revalidation:
Required before creation
```

---

# 285. Example — Questionnaire Publication

```text
Current:
Questionnaire Draft version 27

Sources:
Profile version 18
Language version 4
Contact version 12
Project A version 9

Action:
Publish

Result:
Questionnaire Revision 8

Revision 8 contains:
- exact selected values
- exact ordering
- exact media
- exact URLs
- QR config
- locale content
- publication metadata

Later:
Language B2 → C1

Revision 8:
still B2

Current Draft:
may show C1 / be marked changed
```

---

# 286. Example — Builder

```text
Session active

12:00:
Generate Snapshot A
English B2

12:10:
Admin changes English → C1

12:12:
Session preview refreshes/current data revalidates

12:15:
Generate Snapshot B
English C1

Snapshot A remains B2.
Snapshot B contains C1.
```

---

# 287. Example — Casting AI

```text
Analysis Revision 1
Profile: English B2
Result: Partial

Profile later:
English C1

Analysis Revision 1 remains historical.

New/current match:
may be recalculated or Analysis Revision 2 generated.
```

---

# 288. Example — Theme Rollback

```text
Theme R4 active
→ publish R5
→ R5 active
→ issue detected
→ rollback
→ activate R4
```

R5 remains history.

---

# 289. Example — Emotional Grid

```text
Grid Revision 1
4×4
16 confirmed cells
Published

Admin changes crop in cell 7

Result:
new Draft/Grid Revision 2

Revision 1 remains unchanged.
```

---

# 290. Example — Concurrent Questionnaire Publish

```text
Admin Tab A:
Draft v20

Admin Tab B:
Draft v20

Tab A saves:
Draft v21

Tab B attempts publish using expected v20

Result:
CONFLICT

No Revision created.
```

---

# 291. Example — Duplicate Network Retry

```text
Publish command id = XYZ

Request 1:
Revision 9 created

Network response lost

Request 2:
same command id XYZ

Result:
return Revision 9
not Revision 10
```

---

# 292. E2E-REV-001 — Immutable Questionnaire

Publish R1.

Change Profile.

Verify R1 unchanged.

---

# 293. E2E-REV-002 — New Questionnaire Revision

Edit after R1.

Publish.

Verify R2 created and R1 retained.

---

# 294. E2E-REV-003 — Revision Number Concurrency

Concurrent publication cannot duplicate revision number.

---

# 295. E2E-REV-004 — Rollback

Restore R2 while R5 current.

Verify:

```text
new Draft from R2
→ publish as R6
```

R3–R5 retained.

---

# 296. E2E-REV-005 — Historical QR

Publish PDF with URL A.

Change ProfessionalLink to URL B.

Verify old PDF/Revision still encodes A.

---

# 297. E2E-REV-006 — Builder Snapshot

Generate PDF.

Modify Session.

Verify first PDF unchanged.

---

# 298. E2E-REV-007 — Casting Analysis

Generate Analysis R1.

Change Source.

Generate R2.

Verify R1 preserved with old source snapshot.

---

# 299. E2E-REV-008 — AIDraft

Generate Draft A.

Regenerate.

Verify Draft A remains history and Draft B is separate.

---

# 300. E2E-REV-009 — Theme

Publish Theme R1/R2.

Activate R1 rollback.

Verify R2 remains in history.

---

# 301. E2E-REV-010 — Grid

Finalize Grid R1.

Edit crop.

Verify R1 artifact/config unchanged.

---

# 302. E2E-REV-011 — Historical Contact

Publish questionnaire containing Contact A.

Change current Contact A.

Verify historical Revision retains original snapshot subject to privacy policy.

---

# 303. E2E-REV-012 — Conflict

Stale Admin form cannot overwrite newer Profile value silently.

---

# 304. E2E-REV-013 — Draft Autosave

Multiple autosaves increment technical version without creating public revisions.

---

# 305. E2E-REV-014 — Artifact Binding

PDF job for Revision 4 must use Revision 4 even if Revision 5 publishes before worker runs.

---

# 306. E2E-REV-015 — Snapshot Schema

Old Revision remains readable after snapshot schema evolves.

---

# 307. E2E-REV-016 — Historical View

Revision screen clearly read-only and labelled historical.

---

# 308. E2E-REV-017 — Privacy Redaction

Authorized privacy redaction:

- removes/restricts affected historical exposure;
- logs reason;
- does not masquerade as ordinary edit.

---

# 309. E2E-REV-018 — Failed Publish

Artifact/validation failure before commit does not replace current published Revision.

---

# 310. E2E-REV-019 — Restore From Backup

Revision/current pointer/artifacts restore consistently.

---

# 311. E2E-REV-020 — Current vs Historical

Current Language = C1.

Historical Questionnaire = B2.

Both render correctly in their respective contexts.

---

# 312. Revision Authority Matrix

| Object | Revisioned? | Mutable current? | Immutable historical? |
|---|---:|---:|---:|
| ActorProfile | No formal revision by default | Yes | Audit |
| MediaAsset original | Immutable source | No destructive mutation | Yes source |
| PortfolioItem | Version + Audit | Yes | Audit |
| EmotionalGrid | Yes conceptually | Draft yes | Finalized yes |
| Project | Version + Audit | Yes | via snapshots/Audit |
| Role | Version + Audit | Yes | via snapshots/Audit |
| Training | Version + Audit | Yes | via snapshots/Audit |
| Skill | Version + Audit | Yes | via snapshots/Audit |
| Language | Version + Audit | Yes | via snapshots/Audit |
| Contact | Version + Audit | Yes | snapshot where used |
| Questionnaire | Yes | Draft yes | Revision yes |
| Builder Session | Temporary mutable | Yes | no |
| Builder Snapshot | Snapshot | No | Yes |
| Casting Analysis | Yes | new revisions | Yes |
| Opportunity | Stage history | Yes via transitions | history immutable |
| AIDraft | Generation records | editable draft | generated provenance |
| Theme | Yes | Draft yes | Revision yes |
| AuditEvent | Append-only | No | Yes |

---

# 313. Revision vs Audit Matrix

| Need | Revision | Audit |
|---|---:|---:|
| Reproduce exact document | Yes | No |
| Know who published | Metadata + Audit | Yes |
| Know previous value | Yes where snapshot | Yes delta/history |
| Restore prior configuration | Yes | Not ideal |
| Investigate security action | Sometimes | Yes |
| Rebuild PDF | Yes | No |
| Track stage transitions | Stage history | Audit supplementary |

---

# 314. Revision vs Snapshot Matrix

| Object | Revision | Snapshot |
|---|---|---|
| Prepared Questionnaire | numbered historical version | embedded content snapshot |
| Public Builder | no prepared revision | generation snapshot |
| Casting AI | analysis revision | source/profile snapshot |
| BB Assistant | draft record/generation | source snapshot |
| Theme | theme revision | configuration snapshot |
| Emotional Grid | finalized version | cell/config snapshot |

---

# 315. Reproducibility Doctrine

For any persisted professional output, system SHOULD be able to answer:

```text
What did the user see?
Which facts were used?
Which media were used?
Which URLs were used?
Which configuration was used?
When was it generated?
Which Revision/Snapshot produced it?
```

---

# 316. AI Reproducibility Doctrine

For meaningful AI output, system SHOULD be able to answer:

```text
Which source did AI see?
Which model/prompt version was used?
What did AI originally produce?
What did the human change?
What was ultimately applied?
```

---

# 317. Revision Traceability

Required chain:

```text
SOURCE ENTITY VERSION
        ↓
CONFIGURATION / DRAFT
        ↓
SNAPSHOT
        ↓
REVISION
        ↓
ARTIFACT
        ↓
PUBLIC/ADMIN USE
```

---

# 318. Change Traceability

```text
Human/System Command
      ↓
Mutation
      ↓
Entity Version Increment
      ↓
AuditEvent
      ↓
Dependent Invalidations
```

---

# 319. Publication Traceability

```text
Draft Version
   ↓
Validation Result
   ↓
Published Revision
   ↓
Artifact Jobs
   ↓
Current Published Pointer
   ↓
AuditEvent
```

---

# 320. Revision Governance

New formal Revision type SHOULD only be introduced when business value requires immutable historical reconstruction.

Approval criteria:

```text
Does the exact prior state matter professionally?
Does output need historical reproducibility?
Is rollback valuable?
Does AI/source analysis require explanation?
```

If mostly no:

prefer Version + Audit.

---

# 321. Revision Naming

Use explicit terms:

```text
Revision
Version
Snapshot
Draft
Artifact
```

Do not use them interchangeably in documentation/code.

---

# 322. Canonical Definitions

## Version

Technical mutable entity concurrency number.

## Draft

Current editable unpublished work.

## Snapshot

Frozen values/input at a point in time.

## Revision

Historically meaningful immutable domain version.

## Artifact

Generated representation from Revision/Snapshot.

## Audit Event

Immutable record of action/change.

---

# 323. Revision Compliance Criteria

Implementation complies with DOC-064 if:

1. Draft and Published Revision are distinct;
2. Published Revisions are immutable;
3. autosave does not create public Revision;
4. technical version and revision number are distinct;
5. every Revision belongs to stable logical parent;
6. revision numbering is concurrency-safe;
7. historical Questionnaire does not query mutable current facts;
8. PDF binds to exact Revision/Snapshot;
9. QR historical target does not change silently;
10. Builder generation creates immutable Snapshot;
11. Builder Session changes do not modify prior generated PDFs;
12. Casting analysis regeneration preserves prior revisions;
13. AI source snapshots support provenance;
14. Theme rollback preserves all ThemeRevisions;
15. finalized Emotional Grid config is historical/versioned;
16. Project/Profile/etc. use Version + Audit unless formal Revision justified;
17. stale client writes cannot silently overwrite current source;
18. publish requests are idempotent;
19. rollback does not erase newer history;
20. snapshot schemas are versionable;
21. historical rendering remains compatible;
22. privacy redaction is explicit exceptional workflow;
23. source changes invalidate current derived work, not historical Revision;
24. significant publish/rollback actions are audited;
25. migration does not fabricate nonexistent history.

---

# 324. Итоговая модель

```text
                        CURRENT SOURCE DATA
                               │
                         mutable version
                               │
                               ↓
                         CURRENT DRAFT
                               │
                save / autosave / validate
                               │
                               ↓
                    PUBLICATION BOUNDARY
                               │
                       freeze snapshot
                               │
                               ↓
                     IMMUTABLE REVISION
                               │
                 ┌─────────────┼─────────────┐
                 ↓             ↓             ↓
                PDF           HTML           QR
                 │             │             │
                 └─────────────┼─────────────┘
                               ↓
                       HISTORICAL OUTPUT


CURRENT SOURCE later changes
            │
            ├─────────────→ New Draft
            │
            ├─────────────→ Current projections update
            │
            └─────────────→ Previous Revision unchanged


AI:

SOURCE SNAPSHOT
      ↓
AI OUTPUT REVISION
      ↓
HUMAN REVIEW
      ↓
APPLY
      ↓
TARGET DRAFT
```

---

# 325. Финальный принцип

> **Revision architecture должна позволять продукту изменяться без переписывания прошлого. Текущий профиль может обновляться, язык — повышаться, контакты — меняться, новые фотографии — становиться основными, ссылки — обновляться, а визуальная тема — полностью заменяться. Но уже опубликованная анкета, сформированный кастинговый пакет, завершённый анализ или использованная конфигурация должны оставаться воспроизводимыми и объяснимыми. Поэтому mutable Source, Draft, technical Version, immutable Revision, Snapshot, Artifact и Audit Event являются разными понятиями и не должны смешиваться ни в базе данных, ни в API, ни в интерфейсе.**