# DOMAIN RELATIONSHIPS

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Канонические связи, cardinality и aggregate boundaries доменной модели

**Целевой файл:** `docs/domain/entity-relationships.md`  
**Документ:** DOC-061  
**Статус:** ✅ Completed  
**Тип:** Domain / Relationships / Aggregate Boundaries

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/product-principles.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/public-user-flows.md`
- `docs/domain/domain-model.md`

---

# 1. Назначение документа

Настоящий документ определяет нормативные отношения между каноническими сущностями DOC-060.

Он фиксирует:

1. cardinality;
2. обязательность отношений;
3. parent/child ownership;
4. aggregate boundaries;
5. cross-domain references;
6. допустимые nullable relations;
7. orphan rules;
8. reuse rules;
9. bidirectional navigation requirements;
10. historical/snapshot references;
11. derived relationships;
12. relationship invariants;
13. ограничения при archive/delete;
14. правила для AI provenance;
15. отношения между Feedback, Casting и Opportunity;
16. связи Public Builder;
17. связи Questionnaire;
18. связи Media reuse;
19. relationship traceability.

---

# 2. Основной принцип

> **Отношение между сущностями должно выражать реальный доменный смысл и не использоваться только как технический shortcut.**

Например:

```text
Role → Project
```

означает:

> роль существует в контексте конкретного проекта.

А:

```text
Questionnaire → PortfolioItem
```

означает:

> анкета использует существующий профессиональный материал.

---

# 3. Relationship ID Convention

Используется:

```text
REL-{DOMAIN}-{NNN}
```

Примеры:

```text
REL-MEDIA-001
REL-QST-004
REL-CST-007
```

---

# 4. Cardinality Notation

Используются обозначения:

```text
1      exactly one
0..1   zero or one
0..N   zero or many
1..N   one or many
```

Пример:

```text
Project 1 → 0..N Role
Role    1 → 1 Project
```

---

# 5. Ownership vs Reference

Необходимо различать:

## Ownership

Child lifecycle тесно связан с parent.

Пример:

```text
QuestionnaireDefinition
  owns
QuestionnaireRevision
```

## Reference

Entity использует другую entity, но не владеет ею.

Пример:

```text
PortfolioItem
  references
MediaAsset
```

Удаление PortfolioItem не должно автоматически удалить MediaAsset.

---

# 6. Aggregate Boundary

Aggregate определяет transactional consistency boundary.

Это НЕ означает автоматический database cascade delete.

Canonical aggregate root обозначается:

```text
[ROOT]
```

---

# 7. High-Level Aggregate Map

```text
[ActorProfile]
 ├─ ActorSkill
 ├─ ActorLanguageProficiency
 ├─ PortfolioItem
 ├─ Training
 ├─ Achievement
 ├─ ProfessionalLink
 ├─ Contact
 └─ owns identity-level configuration

[MediaAsset]
 └─ MediaDerivative

[EmotionalSession]
 ├─ EmotionalSessionMedia
 └─ EmotionalGrid
      └─ EmotionalGridCell

[Project]
 ├─ Role
 │   └─ RoleMedia
 └─ Performance

[QuestionnaireDefinition]
 └─ QuestionnaireRevision

[PublicBuilderSession]
 └─ BuilderGenerationSnapshot

[Feedback]
 ├─ FeedbackAttachment
 └─ FeedbackNote

[Casting]
 ├─ CastingSource
 ├─ CastingSourceAsset
 ├─ CastingAnalysis
 │   └─ CastingAnalysisRevision
 └─ confirmed/reviewed casting data

[Opportunity]
 └─ OpportunityStageHistory

[Notification]
 └─ NotificationDelivery

[SiteTheme]
 ├─ ThemeRevision
 └─ ThemeActivation

[SupportTicket]
 └─ SupportTicketMessage
```

---

# 8. REL-PRO-001 — ActorProfile → PortfolioItem

```text
ActorProfile 1
PortfolioItem 0..N
```

Every PortfolioItem MUST belong to exactly one ActorProfile.

---

# 9. Portfolio Orphan Rule

A PortfolioItem without Profile is invalid.

---

# 10. REL-PRO-002 — ActorProfile → ActorSkill

```text
ActorProfile 1
ActorSkill 0..N
```

ActorSkill MUST belong to exactly one Profile.

---

# 11. REL-PRO-003 — SkillDefinition → ActorSkill

```text
SkillDefinition 1
ActorSkill 0..N
```

ActorSkill MUST reference exactly one canonical SkillDefinition.

---

# 12. ActorSkill Uniqueness

Within one Profile, canonical combination SHOULD be unique:

```text
profile_id + skill_definition_id
```

unless future domain explicitly supports multiple context-specific proficiencies.

Default:

```text
one ActorSkill per SkillDefinition per Profile
```

---

# 13. REL-PRO-004 — ActorProfile → ActorLanguageProficiency

```text
ActorProfile 1
ActorLanguageProficiency 0..N
```

---

# 14. REL-PRO-005 — LanguageDefinition → ActorLanguageProficiency

```text
LanguageDefinition 1
ActorLanguageProficiency 0..N
```

Default uniqueness:

```text
profile_id + language_definition_id
```

---

# 15. REL-PRO-006 — ActorProfile → Training

```text
ActorProfile 1
Training 0..N
```

---

# 16. REL-PRO-007 — ActorProfile → Achievement

```text
ActorProfile 1
Achievement 0..N
```

Achievement MAY additionally reference Project or Role where context exists.

---

# 17. REL-PRO-008 — ActorProfile → Contact

```text
ActorProfile 1
Contact 0..N
```

---

# 18. REL-PRO-009 — ActorProfile → ProfessionalLink

```text
ActorProfile 1
ProfessionalLink 0..N
```

---

# 19. REL-PRO-010 — ActorProfile → Project

```text
ActorProfile 1
Project 0..N
```

Project MUST belong to one Profile in current target architecture.

---

# 20. REL-PRO-011 — ActorProfile → EmotionalSession

```text
ActorProfile 1
EmotionalSession 0..N
```

---

# 21. REL-PRO-012 — ActorProfile → QuestionnaireDefinition

```text
ActorProfile 1
QuestionnaireDefinition 0..N
```

---

# 22. REL-PRO-013 — ActorProfile → PublicBuilderTemplate

Conceptually:

```text
ActorProfile 1
PublicBuilderTemplate 0..N
```

Template MAY alternatively be system-level reusable configuration if later architecture deliberately chooses so.

However actual eligible content remains Profile-specific.

---

# 23. REL-MEDIA-001 — ActorProfile → MediaAsset

```text
ActorProfile 1
MediaAsset 0..N
```

Every professional MediaAsset MUST belong to the actor/profile context.

---

# 24. REL-MEDIA-002 — MediaAsset → MediaDerivative

```text
MediaAsset 1
MediaDerivative 0..N
```

MediaDerivative MUST reference its source MediaAsset unless artifact is based on a higher-level composite configuration such as EmotionalGrid.

---

# 25. MediaDerivative Ownership

MediaAsset owns ordinary derivatives.

Deleting a derivative MUST NOT delete its source.

---

# 26. REL-MEDIA-003 — PortfolioItem → MediaAsset

```text
PortfolioItem 1
MediaAsset     1
```

Each PortfolioItem references exactly one source MediaAsset.

---

# 27. Reverse Media Relation

One MediaAsset MAY be referenced by:

```text
0..N PortfolioItem/domain relations
```

although duplicate Portfolio classification for exactly the same Profile/category SHOULD be avoided unless intentionally allowed.

---

# 28. Media Reuse Invariant

The same MediaAsset MAY be reused in:

- Main Portfolio;
- Emotional Portfolio;
- Project;
- Role;
- Questionnaire through Portfolio/Role relationships;
- social publication;

without duplicating binary source.

---

# 29. REL-MEDIA-004 — RoleMedia → MediaAsset

```text
RoleMedia   1
MediaAsset  1
```

---

# 30. REL-MEDIA-005 — EmotionalSessionMedia → MediaAsset

```text
EmotionalSessionMedia 1
MediaAsset             1
```

---

# 31. Emotional Source Integrity

MediaAsset referenced by EmotionalSessionMedia MUST belong to the same ActorProfile as EmotionalSession.

---

# 32. REL-MEDIA-006 — SocialPost → MediaAsset

```text
SocialPost 1
MediaAsset 0..N
```

via explicit relation/join if media selection supported.

SocialPost does not own source files.

---

# 33. REL-EMO-001 — EmotionalSession → EmotionalSessionMedia

```text
EmotionalSession 1
EmotionalSessionMedia 1..N
```

A useful EmotionalSession normally has at least one media item.

Draft session MAY temporarily contain zero before save workflow, but cannot become publish-ready without applicable media.

---

# 34. REL-EMO-002 — EmotionalSession → EmotionalGrid

```text
EmotionalSession 1
EmotionalGrid    0..N
```

Grid MUST belong to exactly one EmotionalSession.

---

# 35. REL-EMO-003 — EmotionalGrid → EmotionalGridCell

```text
EmotionalGrid 1
EmotionalGridCell 0..N during Draft
```

Before finalization:

```text
exactly rows × columns cells
```

---

# 36. REL-EMO-004 — EmotionalGridCell → EmotionalSessionMedia

```text
EmotionalGridCell       1
EmotionalSessionMedia   1
```

A cell MUST reference media belonging to the same EmotionalSession.

---

# 37. Cross-Session Cell Rule

Prohibited:

```text
Grid Session A
→ Cell
→ Media from Session B
```

unless future business rule explicitly defines multi-session composite.

Current canonical requirement:

```text
one Grid = one EmotionalSession
```

---

# 38. REL-EMO-005 — EmotionalGrid → EmotionalGridArtifact

```text
EmotionalGrid 1
EmotionalGridArtifact 0..N
```

Artifacts MAY include:

- master;
- web;
- PDF;
- thumbnail.

---

# 39. Grid Artifact Validity

Artifact MUST remain traceable to:

```text
grid_id
grid version/configuration
```

---

# 40. Primary Emotional Grid Relationship

Within applicable Profile/session context:

```text
0..1 Primary Grid
```

depending published data availability.

If multiple published Grids exist:

exactly one SHOULD be primary when public presentation requires one.

---

# 41. REL-PRJ-001 — Project → Role

```text
Project 1
Role    0..N
```

Role MUST belong to exactly one Project.

---

# 42. Project Without Role

A Project MAY temporarily exist without Role in Draft.

Professional publication SHOULD normally contain relevant role context if the actress participated in the Project.

---

# 43. REL-PRJ-002 — Role → RoleMedia

```text
Role 1
RoleMedia 0..N
```

---

# 44. Role Photo Limit

For configured role-photo use case:

```text
max 5 relevant role photos
```

according to current product rule.

This limit applies to applicable public/showcase relation, not necessarily entire underlying Media Library.

---

# 45. REL-PRJ-003 — RoleMedia → Home Showcase

Home “В образе” is a presentation marker over RoleMedia.

Canonical cardinality for currently selected showcase:

```text
0..2 RoleMedia
```

per active public home context.

---

# 46. Home Showcase Is Not Separate Media

It references RoleMedia/MediaAsset.

---

# 47. REL-PRJ-004 — Project → Performance

```text
Project 1
Performance 0..N
```

---

# 48. REL-PRJ-005 — Role → Performance

A Performance MAY optionally reference:

```text
Role 0..1
```

depending whether performance is project-level or role-specific.

Therefore:

```text
Performance → Project = required
Performance → Role = optional
```

If Role supplied, Role MUST belong to same Project.

---

# 49. REL-PRJ-006 — Achievement → Project

```text
Achievement 0..1 → Project
```

Optional contextual relation.

---

# 50. REL-PRJ-007 — Achievement → Role

```text
Achievement 0..1 → Role
```

If Achievement references Role:

the Role's Project provides parent professional context.

---

# 51. Achievement Context Rule

Achievement MAY be:

```text
profile-level
project-level
role-level
```

but remains one Achievement entity.

---

# 52. REL-TRN-001 — Training → Evidence Media/Link

Training MAY reference:

```text
0..N MediaAsset
0..N ProfessionalLink
```

for certificates/evidence.

These references do not transfer ownership of source media/link.

---

# 53. REL-ACH-001 — Achievement → Evidence Media/Link

Same principle.

---

# 54. REL-LINK-001 — ProfessionalLink → QRConfiguration

Conceptually:

```text
ProfessionalLink 1
QRConfiguration 0..N
```

because QR visibility/configuration may vary by Questionnaire/context.

This MAY physically be stored within Questionnaire block/item config rather than standalone table.

---

# 55. REL-LINK-002 — ProfessionalLink → QRArtifact

```text
ProfessionalLink / Canonical URL
→
0..N QRArtifact
```

depending rendering settings/version.

---

# 56. QR Cache Key Relationship

QRArtifact MUST be functionally tied to:

```text
canonical URL
+
QR settings
```

not merely link ID.

---

# 57. Public Route QR

QR MAY also point to canonical application route such as Profile or Questionnaire.

In that case, source relation is:

```text
CanonicalPublicRoute
→ QRArtifact
```

not necessarily ProfessionalLink.

---

# 58. REL-CON-001 — Contact → Visibility Scopes

Visibility is associated with Contact but independently evaluated per context.

Canonical scopes:

```text
Public Site
Prepared Questionnaires
Public Builder
```

---

# 59. Contact Does Not Belong to Questionnaire

Questionnaire only references eligible Contact.

---

# 60. REL-QST-001 — QuestionnaireDefinition → QuestionnaireRevision

```text
QuestionnaireDefinition 1
QuestionnaireRevision   0..N
```

QuestionnaireRevision MUST belong to one QuestionnaireDefinition.

---

# 61. Revision Numbering

Within one QuestionnaireDefinition:

```text
revision_number
```

MUST be unique and monotonically assigned according to revision policy.

Exact strategy DOC-064.

---

# 62. REL-QST-002 — QuestionnaireDefinition → Current Draft

Logical relation:

```text
QuestionnaireDefinition
→ 0..1 active editable draft/configuration
```

depending storage design.

Draft MAY physically live in QuestionnaireDefinition fields or dedicated draft/version structure.

Domain semantics must remain distinct from immutable Revision.

---

# 63. REL-QST-003 — QuestionnaireDefinition → ActorProfile

Required:

```text
QuestionnaireDefinition 1
ActorProfile            1
```

---

# 64. REL-QST-004 — Questionnaire Configuration → PortfolioItem

```text
Questionnaire 1
PortfolioItem 0..N
```

as selected references.

---

# 65. Questionnaire Media Selection Rule

Questionnaire SHOULD prefer domain-semantic references:

```text
PortfolioItem
RoleMedia
EmotionalGrid
ProfessionalLink
```

rather than raw MediaAsset IDs where professional context matters.

---

# 66. REL-QST-005 — Questionnaire → EmotionalGrid

```text
Questionnaire 1
EmotionalGrid 0..1
```

for standard emotional questionnaire block unless future templates intentionally allow more.

Canonical current questionnaire requirement:

```text
composite + date + link to full Emotional Portfolio
```

not arbitrary individual emotional-photo collection.

---

# 67. REL-QST-006 — Questionnaire → Project

```text
Questionnaire 1
Project 0..N
```

---

# 68. REL-QST-007 — Questionnaire → Role

```text
Questionnaire 1
Role 0..N
```

If Role selected, parent Project context MUST remain available to render.

---

# 69. REL-QST-008 — Questionnaire → Training

```text
Questionnaire 1
Training 0..N
```

---

# 70. REL-QST-009 — Questionnaire → Achievement

```text
Questionnaire 1
Achievement 0..N
```

---

# 71. REL-QST-010 — Questionnaire → ActorSkill

```text
Questionnaire 1
ActorSkill 0..N
```

---

# 72. REL-QST-011 — Questionnaire → ActorLanguageProficiency

```text
Questionnaire 1
ActorLanguageProficiency 0..N
```

---

# 73. REL-QST-012 — Questionnaire → ProfessionalLink

```text
Questionnaire 1
ProfessionalLink 0..N
```

with per-use configuration:

```text
show hyperlink
show QR
display order
description projection
```

---

# 74. REL-QST-013 — Questionnaire → Contact

```text
Questionnaire 1
Contact 0..N
```

Only if:

```text
allow_in_admin_questionnaires = true
```

at configuration/publication according to business rules.

---

# 75. Questionnaire Contact Revalidation

Eligibility MUST be validated before publish/generation.

Historical Revision retains its historical snapshot according to DOC-064/privacy retention rules.

---

# 76. REL-QST-014 — QuestionnaireRevision → Snapshot Data

QuestionnaireRevision MUST be self-consistent and immutable.

It MAY retain:

```text
snapshot copies
stable references
or mixed strategy
```

depending final revision architecture.

But old revision MUST NOT depend on mutable live values for historical truth.

---

# 77. REL-QST-015 — QuestionnaireRevision → QuestionnaireArtifact

```text
QuestionnaireRevision 1
QuestionnaireArtifact 0..N
```

Examples:

```text
PDF RU
PDF EN
HTML static/derived
```

---

# 78. Artifact Locale Relation

One Revision MAY generate multiple locale-specific artifacts only where revision semantics support localized snapshot content.

Final details DOC-064.

---

# 79. Primary Questionnaire Relation

Within one ActorProfile/public context:

```text
0..1 primary active public QuestionnaireDefinition
```

When public questionnaire capability is enabled and valid:

target invariant:

```text
exactly 1
```

---

# 80. Primary Marker Uniqueness

Setting new Primary MUST unset previous Primary atomically.

---

# 81. REL-PQB-001 — PublicBuilderTemplate → ActorProfile

Template belongs to Profile or system scope.

For current product:

it MUST resolve to one ActorProfile's allowed projection.

---

# 82. REL-PQB-002 — PublicBuilderSession → PublicBuilderTemplate

```text
PublicBuilderSession 1
PublicBuilderTemplate 1
```

or template/version snapshot reference.

---

# 83. Template Version

Session SHOULD retain:

```text
template version/config version
```

to avoid unpredictable structure changes during active session.

---

# 84. REL-PQB-003 — PublicBuilderSession → ActorProfile

```text
PublicBuilderSession 1
ActorProfile         1
```

---

# 85. REL-PQB-004 — PublicBuilderSession → Selected Entities

Session MAY temporarily reference:

```text
PortfolioItem 0..N
EmotionalGrid 0..1
Project 0..N
Role 0..N
Training 0..N
Achievement 0..N
ActorSkill 0..N
ActorLanguageProficiency 0..N
ProfessionalLink 0..N
Contact 0..N
```

subject to Template and eligibility rules.

---

# 86. Builder Cross-Profile Rule

Every selected entity MUST belong to the Session's ActorProfile.

---

# 87. Builder Server Revalidation

Selection existence in Session does NOT guarantee current eligibility.

Before Preview/Generate:

```text
belongs to Profile
AND
current Builder permission
AND
current availability
AND
Template rules
```

must pass.

---

# 88. REL-PQB-005 — PublicBuilderSession → BuilderGenerationSnapshot

```text
PublicBuilderSession 1
BuilderGenerationSnapshot 0..N
```

One session MAY generate multiple snapshots/documents during its lifetime.

---

# 89. Generation Snapshot Ownership

Snapshot belongs to one Builder Session.

It is immutable for its generated output.

---

# 90. REL-PQB-006 — BuilderGenerationSnapshot → QuestionnaireArtifact

```text
BuilderGenerationSnapshot 1
QuestionnaireArtifact 0..N
```

typically PDF and potentially HTML preview artifact.

---

# 91. Builder Snapshot != QuestionnaireRevision

No relationship should imply it is official Prepared Questionnaire history.

---

# 92. REL-PQB-007 — Prepared Questionnaire → PublicBuilderSession

Optional provenance:

```text
PublicBuilderSession 0..1
started_from_questionnaire_id/revision
```

when user selects:

```text
Настроить эту версию под себя
```

---

# 93. Prepared Starting Point Rule

Builder copies allowed configuration semantics.

It does not mutate original Questionnaire.

---

# 94. REL-FDB-001 — ActorProfile → Feedback

```text
ActorProfile 1
Feedback     0..N
```

---

# 95. REL-FDB-002 — Feedback → FeedbackAttachment

```text
Feedback 1
FeedbackAttachment 0..N
```

---

# 96. Attachment Ownership

Feedback owns its private attachment relation.

Physical file storage may be shared infrastructure.

---

# 97. REL-FDB-003 — Feedback → FeedbackNote

```text
Feedback 1
FeedbackNote 0..N
```

Always private.

---

# 98. REL-FDB-004 — Feedback → QuestionnaireDefinition / Revision

Feedback MAY reference:

```text
0..1 Questionnaire
0..1 QuestionnaireRevision
```

where inquiry originated from prepared questionnaire.

Prefer most precise known context.

---

# 99. REL-FDB-005 — Feedback → PublicBuilderSession

Feedback MAY reference:

```text
0..1 PublicBuilderSession
```

when user begins professional action from Builder.

---

# 100. REL-FDB-006 — Feedback → BuilderGenerationSnapshot

Prefer snapshot/package reference where inquiry occurred after custom PDF generation.

```text
Feedback 0..1 → BuilderGenerationSnapshot
```

---

# 101. REL-FDB-007 — Feedback → Project / Role

Contextual CTA MAY attach:

```text
Project 0..1
Role    0..1
```

If Role referenced, it MUST belong to referenced Project or Project can be derived from Role.

---

# 102. REL-FDB-008 — Feedback → AttributionContext

```text
Feedback 0..1
AttributionContext 0..1
```

or attribution values embedded according to final model.

---

# 103. Feedback Context Is Metadata, Not User Fact

Source page/project/builder references do not become professional facts.

---

# 104. REL-CST-001 — Feedback ↔ Casting

Canonical relationship:

```text
Feedback 0..N ↔ 0..N Casting
```

Recommended common case:

```text
one Feedback → zero or one initial Casting
one Casting → one or more source Feedback records possible
```

Final physical cardinality MAY be simplified if product UX guarantees one-to-one creation.

Domain MUST support bidirectional provenance.

---

# 105. Why Not Strict 1:1

A Casting may receive:

- original casting invitation;
- later material request;
- role update;

as separate Feedback records.

Therefore multiple Feedback → same Casting is a valid future/current operational scenario.

---

# 106. Casting Creation Is Explicit

Relationship is created only by explicit Admin/system-authorized operation.

Not by Feedback persistence itself.

---

# 107. REL-CST-002 — ActorProfile → Casting

```text
ActorProfile 1
Casting      0..N
```

---

# 108. REL-CST-003 — Casting → CastingSource

```text
Casting 1
CastingSource 1..N
```

A meaningful Casting SHOULD have at least one Source.

Draft manually created Casting MAY temporarily have zero until source/context entered.

---

# 109. REL-CST-004 — CastingSource → CastingSourceAsset

```text
CastingSource 1
CastingSourceAsset 0..N
```

---

# 110. Source Asset Ownership

CastingSourceAsset is private to Casting context.

It does not become Portfolio Media through this relationship.

---

# 111. REL-CST-005 — Casting → CastingAnalysis

```text
Casting 1
CastingAnalysis 0..N or 0..1 logical analysis root
```

Recommended domain design:

```text
Casting 1
CastingAnalysis 0..1
CastingAnalysis 1
CastingAnalysisRevision 0..N
```

This gives one analysis history per Casting.

---

# 112. REL-CST-006 — CastingAnalysis → CastingAnalysisRevision

```text
CastingAnalysis 1
CastingAnalysisRevision 0..N
```

---

# 113. Analysis Revision Source Relation

Every CastingAnalysisRevision MUST identify exact source snapshot/revisions used.

---

# 114. REL-CST-007 — CastingAnalysisRevision → CastingSource

Conceptually:

```text
AnalysisRevision
→ 1..N CastingSource snapshot references
```

or frozen combined source snapshot.

The analysis must remain reproducible/explainable relative to source used.

---

# 115. REL-CST-008 — CastingAnalysisRevision → CastingRequirement

```text
CastingAnalysisRevision 1
CastingRequirement 0..N
```

---

# 116. Requirement Provenance

Each extracted requirement SHOULD retain relation/evidence to:

```text
CastingSource
source fragment/page/asset
```

where technically feasible.

---

# 117. REL-CST-009 — CastingRequirement → Human Confirmation

A Requirement may have:

```text
0..1 current confirmed interpretation
```

plus Audit history.

Human confirmation does not erase original extraction.

---

# 118. REL-CST-010 — CastingRequirement → ProfileMatchResult

```text
CastingRequirement 1
ProfileMatchResult 0..1 per analysis/profile snapshot
```

or multiple if versioned analyses retained.

---

# 119. Match Source Relation

ProfileMatchResult MUST identify Profile facts used to reach match.

Examples:

```text
ActorSkill
ActorLanguageProficiency
Portfolio/Project evidence
```

---

# 120. REL-CST-011 — CastingAnalysisRevision → CastingRecommendation

```text
CastingAnalysisRevision 1
CastingRecommendation 0..N
```

---

# 121. Recommendation Evidence

Recommendation SHOULD be traceable to:

```text
requirement(s)
+
profile match/evidence
```

where relevant.

---

# 122. REL-CST-012 — Casting → Casting Questionnaire Draft

Casting MAY create:

```text
0..N QuestionnaireDefinition/Drafts
```

with explicit relation:

```text
questionnaire.context_casting_id
```

or equivalent.

---

# 123. Casting Questionnaire Is Still Questionnaire

It MUST remain governed by Questionnaire lifecycle and publication rules.

---

# 124. REL-CST-013 — Casting Questionnaire → Casting

```text
Casting Questionnaire 1
Casting 1
```

for casting-specific prepared draft.

A general Compact Questionnaire has no Casting relation.

---

# 125. REL-CST-014 — Casting → Opportunity

Canonical:

```text
Casting 1
Opportunity 0..1
```

for one actor-specific opportunity arising from one Casting.

If future workflow requires multiple roles/opportunities from one Casting, relation MAY evolve to:

```text
Casting 1 → 0..N Opportunity
```

Current recommended model keeps cardinality extensible.

---

# 126. Casting vs Opportunity Boundary

Casting stores:

```text
opportunity under evaluation/source context
```

Opportunity stores:

```text
business progression/outcome
```

---

# 127. REL-OPP-001 — ActorProfile → Opportunity

```text
ActorProfile 1
Opportunity 0..N
```

---

# 128. REL-OPP-002 — Opportunity → Casting

```text
Opportunity 1
Casting     1
```

for casting-originated opportunities.

A future direct Role Offer MAY originate from Feedback without formal Casting, but current business flow SHOULD still permit creation of Casting/context or explicitly documented nullable Casting relationship.

Recommended:

```text
casting_id = required for normal flow
```

unless future requirements establish direct opportunities.

---

# 129. REL-OPP-003 — Opportunity → Feedback

Optional provenance:

```text
Opportunity 0..1
source_feedback_id
```

or relation through Casting.

Direct relation MAY be retained for efficient traceability.

---

# 130. REL-OPP-004 — Opportunity → OpportunityStageHistory

```text
Opportunity 1
OpportunityStageHistory 1..N
```

At creation, initial stage record SHOULD be present.

---

# 131. Stage History Immutability

Each stage transition appends history.

Do not overwrite history.

---

# 132. REL-OPP-005 — Opportunity → Project Draft

After `Booked`:

```text
Opportunity 0..1
Project Draft
```

MAY be created explicitly.

---

# 133. REL-OPP-006 — Opportunity → Role Draft

After Booked:

```text
Opportunity 0..1
Role Draft
```

normally through resulting Project.

---

# 134. Booked Conversion Provenance

Project/Role created from Opportunity MUST retain provenance:

```text
source_opportunity_id
```

or equivalent Audit/reference.

---

# 135. Opportunity Does Not Own Project

After creation:

Project becomes professional experience domain entity.

Deleting/closing Opportunity MUST NOT delete resulting published Project.

---

# 136. REL-NOT-001 — Notification → Source Entity

Notification MAY reference one primary source:

```text
Feedback
Casting
Opportunity
VOPObservation
System Event
```

via typed reference or explicit fields.

---

# 137. Notification Source Rule

Every actionable Notification SHOULD link to source context.

---

# 138. REL-NOT-002 — Notification → NotificationDelivery

```text
Notification 1
NotificationDelivery 0..N
```

One Notification can be delivered through multiple channels.

---

# 139. Delivery Independence

Each channel delivery has independent state.

Example:

```text
In-App = Sent
WhatsApp = Failed
```

---

# 140. REL-NOT-003 — Feedback → Notification

```text
Feedback 1
Notification 0..N
```

after Feedback persistence.

---

# 141. REL-NOT-004 — Casting → Notification

Examples:

- deadline;
- next action.

```text
Casting 1
Notification 0..N
```

---

# 142. REL-NOT-005 — Opportunity → Notification

Examples:

- callback deadline;
- next action;
- offer follow-up.

---

# 143. Notification Lifecycle Does Not Cascade

Deleting/archiving source according to policy does not necessarily destroy historical delivery audit.

Retention policy decides.

---

# 144. REL-BBA-001 — AIDraft → ActorProfile

```text
AIDraft 1
ActorProfile 1
```

AIDraft always belongs to Profile professional context.

---

# 145. REL-BBA-002 — AIDraft → Context Entity

AIDraft MAY reference exactly one primary context entity:

```text
0..1 Project
0..1 PortfolioItem
0..1 Training
0..1 Casting
0..1 Feedback
0..1 Questionnaire
0..1 SocialPost
```

via typed contextual relation.

---

# 146. One Primary Context Rule

Each AIDraft SHOULD have one primary semantic target/context.

Additional Source facts may be included in source snapshot.

---

# 147. REL-BBA-003 — AIDraft → Source Snapshot

Persistent Draft SHOULD retain enough source snapshot/provenance to explain:

```text
what facts existed when generated
```

---

# 148. REL-BBA-004 — AIDraft → Applied Target

If applied:

```text
AIDraft 0..1
→ target draft/entity field
```

Application event SHOULD be auditable.

---

# 149. Applied Target Does Not Create Ownership

AIDraft does not own the target content after Apply.

---

# 150. REL-VOP-001 — VOPObservation → Target Entity

Each observation SHOULD reference one primary affected entity.

Examples:

```text
ProfessionalLink
QuestionnaireDefinition
MediaAsset
Contact
Casting
Theme
```

---

# 151. Multiple Affected Entities

Observation MAY contain secondary related entity references, but one primary resolution target SHOULD exist where possible.

---

# 152. REL-VOP-002 — VOPObservation → VOPRecommendation

```text
VOPObservation 1
VOPRecommendation 0..N
```

---

# 153. REL-VOP-003 — VOPRecommendation → AutomationExecution

Approved safe recommendation MAY lead to:

```text
0..N AutomationExecution
```

---

# 154. REL-VOP-004 — AutomationExecution → Target Entity

Every execution MUST identify target/context.

---

# 155. VOP Does Not Own Target

Resolving Observation does not change target ownership.

---

# 156. REL-THM-001 — SiteTheme → ThemeRevision

```text
SiteTheme 1
ThemeRevision 0..N
```

---

# 157. REL-THM-002 — SiteTheme → ThemeActivation

```text
SiteTheme 1
ThemeActivation 0..N
```

---

# 158. REL-THM-003 — ThemeActivation → ThemeRevision

```text
ThemeActivation 1
ThemeRevision   1
```

Activation always targets exact revision.

---

# 159. Current Theme Invariant

For one public site/context at a point in time:

```text
exactly one effective active ThemeRevision
```

after resolving temporary/permanent activation precedence.

---

# 160. REL-THM-004 — ThemeProposal → SiteTheme/Revision

ThemeProposal MAY be associated with:

```text
existing SiteTheme
or
new Theme Draft
```

Human adoption creates/updates ThemeRevision Draft.

---

# 161. Theme Proposal Does Not Own Activation

Normative.

---

# 162. REL-ANL-001 — AnalyticsEvent → ActorProfile

Most product events SHOULD be scoped to:

```text
ActorProfile 1
```

where relevant.

---

# 163. REL-ANL-002 — AnalyticsEvent → Context Entity

Event MAY safely reference:

- Project;
- Questionnaire;
- Builder Template;
- public page;
- source type.

Only privacy-approved identifiers.

---

# 164. Analytics Must Not Reference Private Payload

Do not attach:

- Contact value;
- Feedback body;
- Casting source text;
- private attachment.

---

# 165. REL-ANL-003 — Feedback → AttributionContext

Professional conversion can retain acquisition attribution.

---

# 166. REL-ANL-004 — BuilderSession → AttributionContext

Optional.

Used to understand Builder entry source.

---

# 167. REL-SOC-001 — SocialAccountConnection → SocialPost

```text
SocialPost 1
target SocialAccountConnection 1..N
```

through target relation.

---

# 168. REL-SOC-002 — SocialPost → SocialPublishAttempt

```text
SocialPost 1
SocialPublishAttempt 0..N
```

one per platform/retry as appropriate.

---

# 169. REL-SOC-003 — SocialPost → AIDraft

Optional:

```text
SocialPost 0..N AIDraft
```

for generated captions/history.

---

# 170. REL-HLP-001 — SupportTicket → SupportTicketMessage

```text
SupportTicket 1
SupportTicketMessage 0..N
```

---

# 171. REL-HLP-002 — SupportTicket → Context Entity

Ticket MAY reference source admin module/entity for support context.

Must not leak private data unnecessarily.

---

# 172. REL-AUD-001 — AuditEvent → Target Entity

AuditEvent SHOULD identify:

```text
entity_type
entity_id
```

where applicable.

---

# 173. REL-AUD-002 — AuditEvent → Actor

Audit actor may be:

```text
AdminUser
System
Virtual Operator
Casting AI
BB Assistant
Theme AI
Migration
```

---

# 174. Audit Actor Is Typed

Do not represent all automated actors as fake AdminUser.

---

# 175. REL-ID-001 — AdminUser → AdminRole

Target-compatible model:

```text
AdminUser 1
AdminRole 1..N
```

or one role + permissions depending architecture.

Exact authorization model later.

---

# 176. AdminRole Does Not Control Public Persona

Normative.

---

# 177. Cross-Domain Relationship Classes

Every cross-domain relation SHOULD be classified as one of:

```text
OWNERSHIP
REFERENCE
PROVENANCE
CONFIGURATION
DERIVED_FROM
OPERATIONAL_LINK
```

---

# 178. Ownership Example

```text
Project
OWNERSHIP
Role
```

---

# 179. Reference Example

```text
PortfolioItem
REFERENCE
MediaAsset
```

---

# 180. Provenance Example

```text
Project Draft
PROVENANCE
Opportunity
```

---

# 181. Configuration Example

```text
Questionnaire
CONFIGURATION
Contact
```

---

# 182. Derived From Example

```text
QRArtifact
DERIVED_FROM
ProfessionalLink canonical URL
```

---

# 183. Operational Link Example

```text
Feedback
OPERATIONAL_LINK
Casting
```

---

# 184. Same-Profile Invariant

Unless entity is global/system definition, related professional entities MUST belong to the same ActorProfile.

Examples:

```text
Questionnaire ↔ PortfolioItem
EmotionalSession ↔ MediaAsset
Casting ↔ Opportunity
Feedback ↔ BuilderSession
```

---

# 185. Cross-Profile Violation

Any attempted relation:

```text
Profile A Questionnaire
→ Profile B Contact
```

MUST be rejected server-side.

---

# 186. Definition Entities Exception

Global definitions MAY be shared:

```text
SkillDefinition
LanguageDefinition
```

but Actor-specific relation remains Profile-scoped.

---

# 187. Orphan Rules

The following MUST NOT exist as meaningful persistent orphan entities:

```text
Role without Project
EmotionalGrid without EmotionalSession
EmotionalGridCell without EmotionalGrid
QuestionnaireRevision without QuestionnaireDefinition
NotificationDelivery without Notification
OpportunityStageHistory without Opportunity
SupportTicketMessage without SupportTicket
```

---

# 188. Temporary Draft Orphans

UI MAY temporarily create unsaved client-side objects.

Server persistence should still respect domain invariants or explicit Draft exceptions.

---

# 189. Parent Archive Behaviour

Archiving parent MUST define child visibility implications.

At relationship level:

```text
Project archived
→ Role no longer independently active publicly
```

even if Role records remain.

---

# 190. Project Archive Cascade Semantics

Recommended:

```text
Project Archive
→ Roles effectively non-public
→ RoleMedia effectively non-public in Project context
→ historical references retained
```

This is lifecycle projection behaviour, not hard deletion.

---

# 191. EmotionalSession Archive Behaviour

```text
Session archived
→ its Grids no longer active public candidates
→ historical QuestionnaireRevision may still retain snapshot
```

---

# 192. Questionnaire Archive Behaviour

```text
QuestionnaireDefinition archived
→ removed from active public chooser
→ historical Revisions retained
```

---

# 193. Contact Archive Behaviour

Archived Contact:

```text
not eligible for new public projections
not eligible for new Builder selection
historical snapshot retention follows revision/privacy rules
```

---

# 194. Link Archive Behaviour

Archived ProfessionalLink:

not eligible for new generation.

Dependent current dynamic QR/previews become stale/invalid.

---

# 195. Media Archive Behaviour

Archiving source MediaAsset with active dependencies SHOULD normally be blocked or require dependency resolution.

---

# 196. Hard Delete Relationships

Hard delete policy belongs DOC-094.

At relationship level:

source hard deletion MUST NOT leave invalid references.

---

# 197. Derived Artifact Cleanup

Deleting a derived artifact does not break source relationships if artifact can regenerate.

---

# 198. Relationship Stability Across Revisions

Historical Revision should preserve relationship semantics as they existed at snapshot time.

Example:

```text
Revision 5 included Project A
```

Later archiving Project A does not rewrite Revision 5.

---

# 199. Snapshot Relationship Strategy

DOC-064 will choose exact technical representation, but domain requires:

```text
historical snapshot remains self-consistent
```

even when source relations change.

---

# 200. Live Relationship Strategy

Current Draft/public projection uses current eligible Source relationships.

---

# 201. Current vs Historical Example

Current Profile:

```text
Contact A archived
```

Current Builder:

```text
Contact A unavailable
```

Historical QuestionnaireRevision:

```text
may still display historical Contact A
```

subject to privacy/retention/redaction policy.

---

# 202. Privacy Override

Privacy/legal removal MAY override historical display/availability.

Revision immutability does not prohibit controlled redaction required by policy.

Exact policy later.

---

# 203. Relationship Mutation Audit

The following relationship changes SHOULD be audited:

- Primary Portfolio change;
- Contact visibility relation changes;
- Questionnaire item selection;
- Feedback↔Casting link;
- Casting→Opportunity creation;
- Opportunity→Project provenance;
- Theme activation;
- AI Draft application.

---

# 204. Relationship Mutation Concurrency

High-impact relation changes MUST be concurrency-aware.

Examples:

```text
Primary Questionnaire
Primary Close-Up
Primary Emotional Grid
Opportunity stage
```

---

# 205. Primary Marker Relationship Pattern

For:

```text
Primary Close-Up
Primary Full Body
Primary Questionnaire
Primary Emotional Grid
```

setting one new primary must atomically remove previous conflicting primary.

---

# 206. Primary Media Scope

`Primary` MUST have clear scope.

Example:

```text
Primary Close-Up
within ActorProfile + Portfolio category
```

not global across all Media.

---

# 207. Featured Marker Relationship

Featured MAY allow:

```text
0..N
```

unlike Primary.

Examples:

```text
Featured Projects
```

---

# 208. Display Order Relationship

Ordering is presentation metadata.

It MUST NOT change underlying entity ownership.

---

# 209. Link Description Localization

ProfessionalLink identity remains same across locales.

Localized description belongs same entity/projection.

---

# 210. Role Localization

Role name/description translations belong same Role.

Not separate roles.

---

# 211. Project Localization

Same Project identity across RU/EN.

---

# 212. Questionnaire Locale Relation

QuestionnaireDefinition MAY support:

```text
1..N locales
```

QuestionnaireRevision/publication rules determine how localized snapshots are represented.

---

# 213. Builder Locale Relation

PublicBuilderSession has one active output locale at a time.

Changing locale does not change selected entity identities.

---

# 214. AI Source Relationship Integrity

AI output MUST NOT reference a source fact that was not available in its recorded source snapshot without explicit later update.

---

# 215. Stale AI Relationship

If referenced Source entity changes:

AI Draft/Recommendation MAY become:

```text
STALE
```

but is not automatically deleted.

---

# 216. VOP Observation Resolution Relation

Observation may be resolved because:

```text
target entity changed
automation succeeded
condition disappeared
human dismissed with reason
```

Resolution SHOULD retain causal reference.

---

# 217. Notification Source Deletion

Notification history may retain safe source summary/reference even if source later archived/deleted according to retention policy.

Must not create broken public access.

---

# 218. Feedback Source Page

Feedback SHOULD retain safe immutable source context:

```text
route
entity reference
campaign/QR attribution
```

where useful.

---

# 219. QR Attribution Relationship

QR entry MAY create AttributionContext:

```text
source = qr
target = profile/questionnaire/video/etc.
```

without modifying target entity.

---

# 220. Search Relationship

SearchResult → Source Entity is a projection relation.

SearchResult is not persisted business ownership.

---

# 221. Casting Quick View Relationship

Quick View aggregates:

```text
ActorProfile
Primary Portfolio
Languages
Skills
Video
Questionnaire
```

No independent writable relation.

---

# 222. Dashboard Relationship

Dashboard cards SHOULD link to exact source entities.

Examples:

```text
New Inquiry → Feedback
Deadline → Casting
Broken QR → Questionnaire/Link
```

---

# 223. Help Context Relationship

Contextual Help MAY relate:

```text
route/module
→ HelpArticle
```

without domain ownership.

---

# 224. Social Link Relationship

Public social profile URLs MAY be modeled as ProfessionalLink or SocialAccount/public connection projection depending semantics.

Avoid duplicate independent URL facts.

---

# 225. External Account vs Public Link

```text
SocialAccountConnection
= authentication/publishing integration

ProfessionalLink
= public URL/display reference
```

They MAY refer to same external platform/account but serve different domain purposes.

---

# 226. Contact vs SocialAccount

A social account used only for publishing credentials is not automatically public Contact.

Visibility remains explicit.

---

# 227. Notification Channel vs Contact

Internal delivery configuration for Admin WhatsApp MAY reference Contact/configuration.

It MUST NOT automatically make that number public.

---

# 228. Feedback Reply Contact

Incoming sender contact details belong Feedback.

They MUST NOT automatically create Actor Contact.

---

# 229. Casting Source Contacts

Producer/casting contact data found in Casting Source belongs Casting operational context.

It MUST NOT become Actor Contact.

---

# 230. Opportunity Contacts

Opportunity MAY reference professional counterparty/context from Casting, but these are operational, not actor-owned Contacts.

---

# 231. Actor Contact vs External Person

Canonical distinction:

```text
Contact
= how to contact actress/representative

Casting contact
= external business counterparty data
```

If future CRM model requires Counterparty entity, document separately.

Not required in current DOC-061.

---

# 232. Feedback → Notification Sequence Relationship

Correct:

```text
Feedback
persisted
   ↓
Notification created
   ↓
NotificationDelivery attempted
```

---

# 233. Opportunity → Analytics Sequence

Correct:

```text
Opportunity stage changes
   ↓
business history persisted
   ↓
analytics event may be emitted
```

not inverse.

---

# 234. Theme → Analytics

Analytics may compare outcomes by Theme version, but Theme MUST NOT depend on AnalyticsEvent for activation correctness.

---

# 235. Builder → Feedback Sequence

```text
PublicBuilderSession
 ↓
BuilderGenerationSnapshot optional
 ↓
Professional Inquiry
 ↓
Feedback
```

Feedback can preserve session/snapshot context.

No Casting created automatically.

---

# 236. Questionnaire → Casting Sequence

Prepared/custom questionnaire can be linked to Casting for material preparation.

But:

```text
Questionnaire
≠ Casting
```

---

# 237. Casting → Questionnaire Draft Sequence

```text
Casting
 ↓
Confirmed Requirements
 ↓
Questionnaire Draft
```

Questionnaire owns document lifecycle; Casting owns professional opportunity context.

---

# 238. Requirement → Questionnaire Selection

Confirmed CastingRequirement MAY inform recommended Questionnaire item selection.

It MUST NOT itself directly mutate Questionnaire without explicit creation/apply operation.

---

# 239. AI Recommendation → Relationship Mutation

Any relationship change proposed by AI:

```text
Add Project to Questionnaire
Set Photo Category
```

requires proper automation-level rule.

AI recommendation alone does not create relationship.

---

# 240. Safe Automatic Relationship Mutation

AUTO-4 MAY modify only deterministic derived/system relations such as:

```text
derivative generated
cache invalidated
search index association updated
```

not semantic professional relationships.

---

# 241. Semantic Relationship Changes

Require human authority for:

```text
Portfolio classification
Primary photo
Project/Role association
Contact visibility
Questionnaire inclusion
Casting qualification
Opportunity business state
```

according to automation matrix.

---

# 242. Relationship Integrity at API Boundary

All mutation endpoints/actions MUST validate:

```text
source entity exists
target entity exists
both belong to allowed Profile/context
relationship permitted
current lifecycle permits relation
authorization permits operation
```

---

# 243. Relationship Integrity at Database Boundary

Where practical, physical schema SHOULD enforce:

- foreign keys;
- uniqueness;
- non-null;
- constrained references;

for core relationships.

Business-only constraints may remain application/service validation.

---

# 244. Soft-Deleted/Archived References

Queries/projections MUST account for lifecycle of referenced entities.

An FK existing does not mean entity remains eligible for active public use.

---

# 245. Referential Integrity ≠ Eligibility

Example:

```text
Questionnaire still references Contact
Contact exists
Contact now Builder-disabled
```

Referential integrity passes.

Builder eligibility fails.

These are distinct layers.

---

# 246. Relation Eligibility Layers

Canonical:

```text
1. Structural validity
2. Same-profile integrity
3. Lifecycle validity
4. Visibility/permission
5. Context-specific business rules
6. Current readiness
```

---

# 247. Relationship Error Categories

Recommended domain/API categories:

```text
RELATION_TARGET_NOT_FOUND
RELATION_CROSS_PROFILE
RELATION_NOT_ALLOWED
RELATION_TARGET_ARCHIVED
RELATION_VISIBILITY_DENIED
RELATION_LIMIT_EXCEEDED
RELATION_CONFLICT
```

Exact API error codes later.

---

# 248. Relationship Limits

Examples:

```text
Role public photos ≤ 5
Home "В образе" ≤ 2
Emotional Grid exact cells
Primary item ≤ 1 per scope
```

Server authority required.

---

# 249. Relationship Ordering

Ordered relationships SHOULD explicitly store ordering/configuration.

Do not rely on insertion order.

---

# 250. Examples Requiring Order

- Portfolio display;
- Questionnaire blocks;
- selected Projects;
- Grid cells;
- Contacts;
- Professional Links;
- Home role showcase.

---

# 251. Relationship Ordering Scope

Order should belong to the relationship/context when different contexts may require different order.

Example:

```text
Project global display order
```

and:

```text
Questionnaire selected-project order
```

are different.

---

# 252. Same Entity, Different Context Order

Project A may be:

```text
3rd on public Projects
1st in Casting Questionnaire
```

without duplicating Project.

---

# 253. Relationship Metadata

Join/config relationships MAY carry:

```text
display_order
visibility
role/context
is_featured
show_qr
caption override
```

when metadata applies to relation, not entity.

---

# 254. Avoid Entity Pollution

Do not add global field:

```text
Project.questionnaire_order
```

because order is Questionnaire-specific.

---

# 255. Avoid Join Pollution

Conversely, universal professional fact such as Project title should not be stored only on Questionnaire relation.

---

# 256. Relationship Localization

Relation-specific labels MAY be localized when they genuinely differ by context.

Default should reuse canonical entity localized content.

---

# 257. Relationship Snapshotting

When Revision is created, relation metadata relevant to output MUST be captured.

Examples:

```text
selected entity
order
visibility
QR setting
display label
```

---

# 258. Revision Relation Change

Current Questionnaire can later remove Project A.

Old Revision still records Project A.

---

# 259. Relationship Provenance

Important generated relationships SHOULD record source.

Examples:

```text
Project Draft source = Opportunity
Casting source = Feedback
AIDraft context = Casting
```

---

# 260. Provenance Is Immutable History

Changing current association should not erase historical origin unless correction policy explicitly does so.

---

# 261. Bidirectional Admin Navigation Requirements

The following MUST support navigation both ways where permissions permit:

```text
MediaAsset ↔ Used In
Project ↔ Roles
Feedback ↔ Casting
Casting ↔ Questionnaire Draft
Casting ↔ Opportunity
Opportunity ↔ resulting Project/Role
Questionnaire ↔ selected source entities
VOPObservation ↔ affected entity
Notification ↔ source entity
AIDraft ↔ target context
```

---

# 262. Bidirectional Navigation != Bidirectional Ownership

Example:

```text
Feedback ↔ Casting
```

both screens link to each other.

Neither necessarily owns the other.

---

# 263. Used-In Projection

MediaAsset Admin detail SHOULD expose:

```text
Portfolio
Emotional
Project/Role
Social
other current usage
```

through derived relationship lookup.

---

# 264. Dependency Graph

System SHOULD be capable of determining dependencies for high-impact changes.

Example:

```text
ProfessionalLink
 ├─ Questionnaire Draft
 ├─ QRArtifact
 ├─ Public Profile
 └─ Builder projection
```

---

# 265. Dependency Is Not Necessarily Stored Relation

May be computed from:

- references;
- configuration;
- derived cache metadata.

---

# 266. Dependency Categories

```text
HARD_REFERENCE
SOFT_REFERENCE
DERIVED_DEPENDENCY
HISTORICAL_REFERENCE
```

---

# 267. Hard Reference

Entity cannot be meaningful without target.

Example:

```text
Role → Project
```

---

# 268. Soft Reference

Context optional.

Example:

```text
Achievement → Project
```

---

# 269. Derived Dependency

Example:

```text
ProfessionalLink → QRArtifact
```

---

# 270. Historical Reference

Example:

```text
QuestionnaireRevision → snapshot of old Contact
```

---

# 271. Relationship Impact Calculation

Before destructive/visibility changes, Admin SHOULD see impacts grouped by category:

```text
Active Public
Draft
Derived
Historical
```

---

# 272. Example — Contact Visibility

```text
Contact A
Public Builder permission OFF
```

Impact:

```text
New Builder projection: removed
Active sessions: revalidate
Historical questionnaire revision: unchanged unless privacy policy requires
```

---

# 273. Example — Media Deletion

```text
MediaAsset X
used by:
Primary Close-Up
Project A Role
Questionnaire Draft
```

Deletion should be blocked until dependencies resolved.

---

# 274. Example — Project Archive

Impact:

```text
public Projects
current Questionnaire Drafts
Builder eligibility
search projection
```

Historical Revision unaffected.

---

# 275. Example — Link URL Change

Impact:

```text
ProfessionalLink current URL
QRArtifact stale
current Questionnaire preview stale
Public profile updated
historical Revision remains old
```

---

# 276. Example — Language Level Change

Impact:

```text
Profile current projection
Quick View
current Builder
current Questionnaire Draft
search
```

Historical published Revision remains prior value.

---

# 277. Relationship Anti-Pattern REL-AP-001

**Cascade delete MediaAsset when PortfolioItem removed**

---

# 278. REL-AP-002

**Role with no Project**

---

# 279. REL-AP-003

**Emotional Grid mixing multiple sessions**

---

# 280. REL-AP-004

**Questionnaire references raw private Contact without eligibility validation**

---

# 281. REL-AP-005

**Builder references entity from another Profile**

---

# 282. REL-AP-006

**Feedback automatically creates Casting relation**

---

# 283. REL-AP-007

**Casting AI Requirement overwrites Profile fact**

---

# 284. REL-AP-008

**Opportunity Booked directly publishes Project**

---

# 285. REL-AP-009

**Notification owns Feedback lifecycle**

---

# 286. REL-AP-010

**AIDraft owns published target content**

---

# 287. REL-AP-011

**Theme links directly to data visibility**

---

# 288. REL-AP-012

**Analytics event creates business relationship**

---

# 289. REL-AP-013

**Historical Revision uses mutable live relation**

---

# 290. REL-AP-014

**One generic public=true relation for every context**

---

# 291. REL-AP-015

**Generic entity_type/entity_id used for every core relationship**

---

# 292. REL-AP-016

**Questionnaire order stored globally on Project**

---

# 293. REL-AP-017

**Role media duplicates physical file**

---

# 294. REL-AP-018

**External sender contact added to Actor Contacts automatically**

---

# 295. REL-AP-019

**Casting Source attachment appears in public Media Library**

---

# 296. REL-AP-020

**Primary markers allow multiple active conflicting records**

---

# 297. Relationship Quality Gate

Before physical schema implementation every important relation MUST define:

- [ ] source entity;
- [ ] target entity;
- [ ] cardinality;
- [ ] required/optional;
- [ ] ownership/reference/provenance class;
- [ ] same-profile requirement;
- [ ] lifecycle constraints;
- [ ] visibility constraints;
- [ ] ordering metadata;
- [ ] uniqueness;
- [ ] archive implications;
- [ ] delete implications;
- [ ] snapshot behaviour;
- [ ] audit requirement;
- [ ] API validation;
- [ ] bidirectional navigation requirement.

---

# 298. Canonical Relationship Matrix — Profile Content

| Source | Target | Cardinality | Type |
|---|---|---:|---|
| ActorProfile | MediaAsset | 1:N | ownership context |
| ActorProfile | PortfolioItem | 1:N | ownership |
| PortfolioItem | MediaAsset | N:1 | reference |
| ActorProfile | EmotionalSession | 1:N | ownership |
| EmotionalSession | EmotionalGrid | 1:N | ownership |
| EmotionalGrid | EmotionalGridCell | 1:N | ownership |
| GridCell | EmotionalSessionMedia | N:1 | reference |
| ActorProfile | Project | 1:N | ownership |
| Project | Role | 1:N | ownership |
| Role | RoleMedia | 1:N | ownership |
| RoleMedia | MediaAsset | N:1 | reference |
| Project | Performance | 1:N | ownership |
| ActorProfile | Training | 1:N | ownership |
| ActorProfile | Achievement | 1:N | ownership |
| ActorProfile | ActorSkill | 1:N | ownership |
| ActorProfile | LanguageProficiency | 1:N | ownership |
| ActorProfile | Contact | 1:N | ownership |
| ActorProfile | ProfessionalLink | 1:N | ownership |

---

# 299. Canonical Relationship Matrix — Questionnaires

| Source | Target | Cardinality | Type |
|---|---|---:|---|
| ActorProfile | QuestionnaireDefinition | 1:N | ownership |
| QuestionnaireDefinition | QuestionnaireRevision | 1:N | ownership |
| Questionnaire | PortfolioItem | N:N logical | configuration |
| Questionnaire | EmotionalGrid | N:0..1 logical | configuration |
| Questionnaire | Project | N:N logical | configuration |
| Questionnaire | Role | N:N logical | configuration |
| Questionnaire | Training | N:N logical | configuration |
| Questionnaire | Skill | N:N logical | configuration |
| Questionnaire | Language | N:N logical | configuration |
| Questionnaire | ProfessionalLink | N:N logical | configuration |
| Questionnaire | Contact | N:N logical | configuration |
| QuestionnaireRevision | Artifact | 1:N | derived |
| ProfessionalLink/Route | QRArtifact | 1:N | derived |

---

# 300. Canonical Relationship Matrix — Public Builder

| Source | Target | Cardinality | Type |
|---|---|---:|---|
| BuilderTemplate | BuilderSession | 1:N | configuration |
| ActorProfile | BuilderSession | 1:N | context |
| BuilderSession | eligible source entities | N:N logical | temporary selection |
| BuilderSession | GenerationSnapshot | 1:N | ownership |
| GenerationSnapshot | PDF/Artifact | 1:N | derived |
| Prepared Questionnaire | BuilderSession | 1:N optional | provenance |

---

# 301. Canonical Relationship Matrix — Business Workflow

| Source | Target | Cardinality | Type |
|---|---|---:|---|
| ActorProfile | Feedback | 1:N | operational |
| Feedback | Attachment | 1:N | ownership |
| Feedback | Casting | N:N logical | operational/provenance |
| ActorProfile | Casting | 1:N | operational |
| Casting | Source | 1:N | ownership |
| Casting | Analysis | 1:0..1 root | ownership |
| Analysis | AnalysisRevision | 1:N | ownership |
| AnalysisRevision | Requirement | 1:N | ownership |
| Requirement | MatchResult | 1:0..N | derived |
| AnalysisRevision | Recommendation | 1:N | derived/AI |
| Casting | Opportunity | 1:0..N future-safe | operational |
| Opportunity | StageHistory | 1:N | ownership |
| Opportunity | Project Draft | 1:0..1 | provenance |

---

# 302. Canonical Relationship Matrix — Cross-Cutting

| Source | Target | Cardinality | Type |
|---|---|---:|---|
| Notification | Delivery | 1:N | ownership |
| Notification | source entity | N:1 | operational reference |
| AIDraft | context entity | N:0..1 | AI context |
| AIDraft | target Draft | N:0..1 | application provenance |
| VOPObservation | target entity | N:1 | observation |
| Observation | Recommendation | 1:N | ownership |
| Recommendation | AutomationExecution | 1:N | execution |
| SiteTheme | ThemeRevision | 1:N | ownership |
| ThemeActivation | ThemeRevision | N:1 | configuration |
| SocialPost | MediaAsset | N:N | reference |
| SocialPost | PublishAttempt | 1:N | ownership |
| SupportTicket | Message | 1:N | ownership |
| AuditEvent | entity | N:1 logical | audit reference |

---

# 303. Canonical End-to-End Relationship Chain

```text
ActorProfile
     │
     ├─────────────── MediaAsset
     │                    │
     │              ┌─────┼─────────┐
     │              ↓     ↓         ↓
     │          Portfolio Role   Emotional
     │
     ├────────────── Project
     │                    │
     │                   Role
     │
     ├────────────── Skills / Languages
     │
     ├────────────── Contacts / Links
     │
     └────────────── Questionnaire
                           │
                   ┌───────┴────────┐
                   ↓                ↓
                Revision       Builder Session
                   │                │
                   ↓                ↓
                 PDF        Generation Snapshot
                   │                │
                   └───────┬────────┘
                           ↓
                       Feedback
                           │
                           ↓
                        Casting
                           │
                    Source / Analysis
                           │
                           ↓
                       Opportunity
                           │
                           ↓
                         Booked
                           │
                           ↓
                     Project / Role
```

---

# 304. Provenance Chain

Full professional provenance SHOULD be reconstructible:

```text
Public Inquiry
    ↓
Feedback
    ↓
Casting
    ↓
Casting Source
    ↓
Confirmed Requirements
    ↓
Opportunity
    ↓
Booked
    ↓
Project
    ↓
Role
```

---

# 305. Questionnaire Provenance Chain

```text
Source Profile Entities
      ↓
Questionnaire Definition
      ↓
Configuration
      ↓
Revision Snapshot
      ↓
PDF / HTML / QR
```

---

# 306. Public Builder Provenance Chain

```text
Current Eligible Source Data
      ↓
Builder Template
      ↓
Builder Session
      ↓
Server Revalidation
      ↓
Generation Snapshot
      ↓
Custom PDF
```

---

# 307. AI Provenance Chain

```text
Source Entity / Snapshot
      ↓
AI Output
      ↓
Human Review
      ↓
Apply / Confirm / Reject
      ↓
Target Domain Mutation
      ↓
Audit
```

---

# 308. Relationship Compliance Criteria

Implementation complies with DOC-061 if:

1. Role cannot exist independently of Project;
2. Portfolio references Media rather than owns binary;
3. EmotionalGrid belongs to one EmotionalSession;
4. Grid cells can only use media from that session;
5. Questionnaire uses existing professional entities;
6. QuestionnaireRevision remains bound to logical Questionnaire;
7. Builder selections are temporary and Profile-bound;
8. Builder session IDs do not grant data access;
9. Feedback and Casting remain distinct;
10. bidirectional Feedback↔Casting provenance exists;
11. Casting Source remains separate from AI Analysis;
12. AI Requirement does not mutate Profile;
13. Casting and Opportunity remain distinct;
14. Opportunity stage history is preserved;
15. Booked can create Project/Role Draft with provenance;
16. Notification is linked to source but does not own source lifecycle;
17. AIDraft retains context/source provenance;
18. VOP Observation links directly to affected entity;
19. ThemeActivation targets exact ThemeRevision;
20. Analytics links never replace business records;
21. cross-profile relationships are rejected;
22. contextual ordering stays on relation/configuration where appropriate;
23. primary markers are scope-unique;
24. archive does not silently destroy historical relations;
25. delete implications can be computed from dependency relationships.

---

# 309. Финальный принцип

> **Relationships должны сохранять единую профессиональную правду и одновременно позволять одному и тому же факту или материалу участвовать в разных профессиональных сценариях. MediaAsset может использоваться в портфолио и роли, Project — в публичном профиле и анкете, Contact — в разных разрешённых проекциях, Feedback — стать источником Casting, а Booked Opportunity — источником нового Project Draft. При этом ни одна связь не должна размывать границы владения: Questionnaire не владеет профилем, Casting AI не владеет фактами актрисы, Notification не владеет обращением, а производные документы и QR не владеют исходными URL или данными.**