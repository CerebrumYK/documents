# SKILLS & LANGUAGES MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация профессиональных навыков, уровней 1–5, языков, CEFR, visibility, casting matching и Human-confirmed proficiency

**Целевой файл:** `docs/modules/skills-languages.md`  
**Документ:** DOC-117  
**Статус:** ✅ Completed  
**Тип:** Module / Skills / Languages / Professional Capability / CEFR / Casting

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`
- `docs/domain/domain-model.md`
- `docs/domain/entity-relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions-and-history.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/projections.md`
- `docs/architecture/search.md`
- `docs/architecture/ai-architecture.md`
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
- `docs/modules/training.md`
- `docs/modules/projects.md`

---

# 1. Назначение модуля

Skills & Languages Module хранит подтверждённые профессиональные capability-факты актрисы.

Он состоит из двух связанных, но независимых bounded contexts:

```text
Skills
Languages
```

Они используются в:

- Public Profile;
- Hero / Quick Facts;
- Search inside profile;
- Casting Quick View;
- prepared Questionnaires;
- Public Questionnaire Builder;
- Casting AI matching;
- BB Assistant grounding;
- international profile;
- Search;
- professional PDF.

---

# 2. Главная доктрина

> **Training показывает, чему актриса обучалась. Skills показывают, что она умеет сейчас. Languages показывают подтверждённый уровень владения языком. Ни обучение, ни AI-анализ, ни casting requirement не могут автоматически повышать эти уровни.**

Canonical:

```text
Training Evidence
      ↓
optional recommendation
      ↓
Human review
      ↓
Skill / Language Source
```

---

# 3. Fundamental separation

```text
Skill
≠ Training
≠ Project Experience
≠ Achievement
≠ Casting Requirement
≠ AI Recommendation
```

и:

```text
Language
≠ nationality
≠ country
≠ course completion
≠ AI-detected pronunciation
```

---

# 4. Module identifiers

Используются:

```text
SKL-*
SKL-LVL-*
SKL-VIS-*
SKL-HL-*
SKL-AI-*
LNG-*
LNG-CEFR-*
LNG-VIS-*
LNG-AI-*
CAP-INV-*
CAP-AP-*
E2E-CAP-*
```

---

# 5. Skills domain

Skill представляет профессионально значимую capability.

Examples:

```text
Horse Riding
Stage Combat
Swimming
Driving
Singing
Dance
Skiing
Snowboarding
Weapons Handling
Yoga
Piano
Voice Acting
Improvisation
```

Фактический каталог должен быть controlled.

---

# 6. Skill dictionary

Preferred architecture:

```text
SkillDefinition
      ↓
ActorSkill
```

---

# 7. `SkillDefinition`

Reference/dictionary entity.

Owns:

```text
canonical_code
category
localized_name
optional description
active status
```

---

# 8. `ActorSkill`

Profile-specific Source.

Owns:

```text
profile_id
skill_definition_id
level
optional professional note
visibility
highlight/configuration
version
```

---

# 9. SKL-INV-001 — Dictionary Is Not Actor Fact

Presence of `Horse Riding` in dictionary does not mean actress has that skill.

---

# 10. Skill categories

Controlled taxonomy MAY include:

```text
PERFORMANCE
MOVEMENT
DANCE
SPORT
COMBAT
MUSIC
VOICE
DRIVING
TECHNICAL
OTHER
```

Exact taxonomy may evolve without changing ActorSkill semantics.

---

# 11. Skill level scale

Canonical project requirement:

> **Skill level uses exactly five levels.**

Allowed:

```text
1
2
3
4
5
```

---

# 12. SKL-LVL-001 — Closed Range

Hard constraint:

```text
1 <= level <= 5
```

---

# 13. Level labels

Machine value remains integer.

Human-readable labels may be:

```text
1 — Базовый
2 — Начальный практический
3 — Уверенный
4 — Продвинутый
5 — Профессиональный / экспертный
```

Final wording should be validated with product owner before production UI.

---

# 14. Important semantic rule

The five-level scale is **product-specific professional self/profile classification**, not an industry accreditation unless explicitly supported by evidence.

---

# 15. SKL-LVL-002 — Level 5 Does Not Mean Certified

---

# 16. Level meaning

Levels should describe ability rather than prestige.

Recommended principles:

### Level 1
basic familiarity.

### Level 2
basic controlled practical use.

### Level 3
confident practical capability.

### Level 4
advanced capability suitable for demanding professional context.

### Level 5
high/professional mastery, subject to Human-confirmed evidence.

---

# 17. No decimal levels

Not:

```text
3.5
4.7
```

Baseline supports integers only.

---

# 18. No percentage conversion

Do not expose:

```text
4/5 = 80%
```

as professional truth.

---

# 19. SKL-LVL-003 — Level Is Ordinal, Not Percentage

---

# 20. Skill evidence

Optional supporting context may include:

- Training;
- Project experience;
- certification;
- long-term practice;
- professional note.

---

# 21. Evidence does not automatically calculate level

---

# 22. SKL-INV-002 — No Automatic Skill Scoring

Do not compute:

```text
Training + Projects + years
→ level 4
```

without explicit Human decision.

---

# 23. Training integration

Training may have relation:

```text
TrainingItem
→ related SkillDefinition
```

but does not modify `ActorSkill.level`.

---

# 24. Projects integration

A Project may be evidence that a Skill was used.

Still no automatic level mutation.

---

# 25. Casting integration

Requirement:

```text
Horse Riding — advanced
```

may be compared with:

```text
ActorSkill Horse Riding = 4
```

using explicit matching semantics.

---

# 26. Matching scale

Application/Casting AI may map casting wording to minimum skill level only when extraction is sufficiently explicit.

---

# 27. Example

Source says:

```text
уверенная верховая езда
```

AI MAY extract:

```text
requiredSkill = HORSE_RIDING
qualitativeRequirement = "confident"
```

It should not silently assert:

```text
minLevel = 4
```

unless product mapping explicitly defines such conversion.

---

# 28. SKL-AI-001 — Qualitative Casting Text Does Not Automatically Equal Numeric Level

---

# 29. Safer matching

```text
Casting requirement
+
ActorSkill Source
↓
evidence-based recommendation
```

with reason.

---

# 30. Skill note

ActorSkill MAY have short Human-confirmed professional note.

Example:

```text
Опыт верховой езды в манеже и на открытой местности.
```

---

# 31. Skill note is not level replacement

---

# 32. Skill localization

SkillDefinition localized names belong dictionary.

Actor-specific note may require localization.

---

# 33. SKL-INV-003 — Canonical Skill Identity Is Language-Neutral

---

# 34. Duplicate skills

Same active SkillDefinition should appear only once per profile.

---

# 35. Database uniqueness

Conceptually:

```text
UNIQUE(profile_id, skill_definition_id)
```

for active/current ActorSkill.

---

# 36. SKL-INV-004 — No Duplicate Current ActorSkill

---

# 37. Skill custom entries

If unknown skill not in dictionary:

two options:

1. controlled Admin creation of new SkillDefinition;
2. limited custom skill workflow.

Preferred architecture: explicit dictionary extension.

---

# 38. SKL-INV-005 — No Arbitrary Free-Text Skill as Primary Model

Free-text only as transitional/migration mechanism.

---

# 39. Skill visibility

Canonical triplet:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 40. SKL-VIS-001 — Independent Dimensions

---

# 41. Highlighting

Skills may be designated:

```text
highlighted
```

for Hero/Quick Facts.

---

# 42. SKL-HL-001 — Highlight Is Presentation Configuration

Not proof that skill is objectively more important.

---

# 43. Highlight count

UI may enforce a reasonable bounded count to avoid clutter.

Exact limit should be defined during UX implementation.

No hard number is imposed by this document.

---

# 44. Highlight authority

Human only.

AI/VOP may recommend.

---

# 45. SKL-AI-002 — AI Cannot Auto-Highlight Skill

---

# 46. Skill ordering

Explicit:

```text
display_order
```

within Profile/admin/public contexts.

---

# 47. CreatedAt is not skill importance.

---

# 48. Skill lifecycle

Preferred:

```text
ACTIVE
ARCHIVED
```

or standard DRAFT/PUBLISHED/ARCHIVED if editorial workflow requires.

---

# 49. Recommended baseline

Because ActorSkill itself is simple factual data:

```text
ACTIVE
ARCHIVED
```

plus surface visibility may be sufficient.

However formal publication rules must remain consistent with overall Save != Publish doctrine.

---

# 50. Historical use

QuestionnaireRevision freezes exact:

```text
skill identity
localized label
level
note if included
```

---

# 51. Current level change

Does not rewrite historical Questionnaire.

---

# 52. Languages domain

Language representation:

```text
LanguageDefinition
      ↓
ActorLanguage
```

---

# 53. `LanguageDefinition`

Dictionary Source.

Owns:

```text
ISO/language code
localized names
active status
```

Examples:

```text
ru
en
kk
fr
de
```

---

# 54. LNG-INV-001 — Language Code Is Stable Identity

Do not key language by translated name.

---

# 55. ActorLanguage

Owns:

```text
profile_id
language_definition_id
proficiency
optional note
visibility
highlight/order
version
```

---

# 56. Canonical language proficiency

Supported:

```text
NATIVE
C2
C1
B2
B1
A2
A1
```

---

# 57. CEFR

CEFR values:

```text
A1
A2
B1
B2
C1
C2
```

---

# 58. Native

`NATIVE` is a separate semantic value.

---

# 59. LNG-CEFR-001 — Native Is Not C2

Native and C2 are not identical concepts.

---

# 60. Why

A native speaker may not have CEFR certification, and a C2 non-native speaker is still semantically different.

---

# 61. Proficiency ordering

For comparison purposes, application may define:

```text
A1 < A2 < B1 < B2 < C1 < C2
```

`NATIVE` handled separately.

---

# 62. Native comparison

For casting requirements:

- requirement “native” requires `NATIVE`;
- requirement “C1 or above” may accept `C1`, `C2`, and possibly Native depending business semantics;
- this rule must be explicit, not accidental string ordering.

---

# 63. LNG-CEFR-002 — Comparison Is Domain Logic, Not Lexicographic

---

# 64. No hidden numeric mapping as Source

Implementation MAY internally map for comparison:

```text
A1=1 ... C2=6
```

but stored professional meaning remains CEFR enum.

---

# 65. Language evidence

Can include:

- formal test;
- education;
- long-term use;
- residence;
- professional experience.

But ActorLanguage proficiency remains Human-confirmed profile fact.

---

# 66. LNG-INV-002 — Training Does Not Set CEFR Automatically

---

# 67. AI speech analysis

Future AI could analyze pronunciation/sample.

It cannot automatically change CEFR Source.

---

# 68. LNG-AI-001 — Speech AI Is Recommendation Only

---

# 69. Casting requirement

Examples:

```text
English B2+
Native Russian
Conversational Kazakh
```

---

# 70. Explicit CEFR requirement

If source says:

```text
English B2
```

comparison is deterministic against saved ActorLanguage.

---

# 71. Qualitative language requirement

```text
conversational English
```

AI may extract qualitative requirement, but mapping to CEFR must follow an explicit product rule or remain qualitative.

---

# 72. LNG-AI-002 — “Conversational” Does Not Implicitly Mean B1/B2 Without Mapping Rule

---

# 73. Language note

Optional Human-confirmed note.

Example:

```text
Свободное профессиональное общение на съёмочной площадке.
```

---

# 74. Accent/dialect

If professionally relevant, future structured attributes MAY include:

```text
accent
dialect
pronunciation note
```

but they should not be hidden inside CEFR.

---

# 75. LNG-INV-003 — Accent ≠ Language Proficiency

---

# 76. Multiple languages

No artificial limit.

---

# 77. Duplicate language

One active ActorLanguage per language per profile.

---

# 78. LNG-INV-004 — No Duplicate Current Language Entry

---

# 79. Language visibility

Same triplet:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 80. LNG-VIS-001 — Independent Context Permissions

---

# 81. Language highlight

Languages may be highlighted for Hero/Quick Facts.

---

# 82. Example

```text
Русский — Native
English — B2
```

---

# 83. Highlight remains Human-controlled.

---

# 84. Profile Hero integration

Hero may display:

- selected highlighted Skills;
- selected highlighted Languages.

---

# 85. CAP-INV-001 — Hero Does Not Own Skill/Language Copies

---

# 86. Quick Facts

Projection can aggregate:

```text
City
Languages
Skills
Profile parameters
```

---

# 87. Quick Facts eligibility

Uses public-safe current data only.

---

# 88. Hidden Skills/Languages

Absent from Public Hero/Quick Facts.

---

# 89. CAP-INV-002 — Hidden Capability Is Not Serialized Then Hidden

---

# 90. Public Skills section

Should emphasize professional scanning speed.

Recommended presentation:

```text
Skill
Level
optional concise note
```

---

# 91. Public level rendering

Prefer understandable labels + machine-neutral visual.

Example:

```text
Horse Riding — 4/5
```

or localized label.

---

# 92. Accessibility

Do not represent level only with five visual dots/stars.

Need textual alternative:

```text
Уровень 4 из 5
```

---

# 93. CAP-INV-003 — Visual Rating Must Have Text Semantics

---

# 94. Avoid stars if they imply review/rating

Better:

- progress markers;
- numeric 1–5;
- named level.

---

# 95. No body/appearance semantics

Skill system must not include attractiveness/body scoring.

---

# 96. Public Languages section

Recommended:

```text
Russian — Native
English — B2
Kazakh — A2
```

---

# 97. CEFR explanation

UI MAY offer tooltip:

```text
A1–C2 — Common European Framework of Reference for Languages
```

---

# 98. Questionnaire integration

Skills and Languages are independently selectable blocks/items.

---

# 99. Compact Questionnaire

Should contain:

- key Skills;
- Languages.

---

# 100. Extended Questionnaire

May contain all eligible Skills/Languages.

---

# 101. Casting Questionnaire

Should prioritize requirement-relevant capabilities.

---

# 102. CAP-QNR-001 — Selection Does Not Mutate Source

---

# 103. Default selection

Prepared Questionnaire can use Admin-defined defaults/highlights.

---

# 104. Public Builder

Builder can select among eligible Skills/Languages.

---

# 105. Template behavior

### Quick
key Skills + Languages.

### Standard
broader set.

### Extended
all eligible.

### Casting
requirement-relevant selections.

---

# 106. CAP-BLD-001 — Builder Cannot Change Level

Visitor may include/exclude:

```text
Horse Riding — 4/5
```

but cannot submit:

```text
5/5
```

as Source.

---

# 107. Builder tampering

Server reloads current authoritative ActorSkill/ActorLanguage.

---

# 108. Generate revalidation

Checks:

```text
same profile
current
eligible
not archived
```

---

# 109. Historical Questionnaire

Freezes exact skill/language data.

---

# 110. Example

At QNR Revision R2:

```text
English B2
```

Current profile later becomes:

```text
English C1
```

R2 remains B2.

---

# 111. CAP-INV-004 — Current Capability Improvement Does Not Rewrite Historical QNR

---

# 112. Casting AI integration — Skills

Pipeline:

```text
Casting Requirement
      ↓
Extract Skill Requirement
      ↓
Confirmed ActorSkill
      ↓
Compare
      ↓
Match / Partial / Mismatch / No Confirmed Data
```

---

# 113. Match reasons

Recommendation should cite:

```text
required skill
saved actor level
source fact
reason
```

---

# 114. Example

```text
Requirement: advanced horse riding
Saved profile: Horse Riding 4/5
Recommendation: likely match
Reason: confirmed ActorSkill level 4/5
```

if mapping is explicitly allowed.

---

# 115. If mapping uncertain

Return:

```text
requires human interpretation
```

not invented deterministic match.

---

# 116. Casting AI integration — Languages

Explicit requirement:

```text
English B2+
```

Saved:

```text
English C1
```

deterministic match.

---

# 117. Missing language

Return:

```text
NO_CONFIRMED_DATA
```

if no ActorLanguage exists.

---

# 118. CAP-AI-001 — Casting AI Cannot Add Missing Capability

---

# 119. No optimistic matching

Absence of evidence does not become positive match.

---

# 120. No punitive inference

Absence also does not necessarily imply absolute inability.

Use:

```text
NO_CONFIRMED_DATA
```

when appropriate.

---

# 121. BB Assistant integration

BB may use Skills/Languages as grounding facts for:

- biography;
- cover letter;
- casting response;
- questionnaire copy;
- social captions.

---

# 122. CAP-AI-002 — BB Cannot Upgrade Capability in Prose

Example Source:

```text
English B2
```

BB must not write:

```text
fluent/native-level English
```

unless mapping/policy explicitly supports such wording.

---

# 123. BB may phrase conservatively

Example:

```text
English — B2
```

or approved wording.

---

# 124. Training mention

BB may mention relevant Training separately.

It should not collapse:

```text
trained in stage combat
```

into:

```text
expert stage combat performer
```

without Skill evidence.

---

# 125. VOP

May detect:

```text
Skill with no level
duplicate Skill
duplicate Language
invalid CEFR
public Skill hidden in Hero config
highlight points to archived entry
Training related to missing Skill
casting repeatedly asks for missing capability data
```

---

# 126. VOP safe automation

May:

```text
reindex Search
invalidate cache
recompute readiness
```

---

# 127. VOP cannot

```text
increase level
set CEFR
create Skill
set Native
highlight
publish
```

automatically.

---

# 128. CAP-AI-003 — Capability Changes Require Human Authority

---

# 129. Search

Public Search may index:

```text
Skill names
public notes
Languages
CEFR labels
```

---

# 130. Search inside profile

Important casting functionality.

User should be able to search:

```text
horse
swimming
English
B2
Kazakh
dance
```

---

# 131. Public Search privacy

Only public current capabilities.

---

# 132. Builder Search

May search Builder-eligible capabilities separately.

---

# 133. Admin Search

Broader authorized scope.

---

# 134. CAP-INV-005 — Search Contexts Remain Separate

---

# 135. Search ranking

Exact Skill/Language match should rank high.

No popularity ranking needed.

---

# 136. SEO

Public capability data MAY contribute to structured actor profile metadata.

---

# 137. Hidden capability

Never in:

```text
JSON-LD
OpenGraph
public search document
sitemap-generated metadata
```

---

# 138. Skill aliases

Dictionary MAY support aliases/synonyms.

Example:

```text
Horse Riding
Equestrian
Верховая езда
```

---

# 139. Alias purpose

Search/localization only.

---

# 140. SKL-INV-006 — Alias Does Not Create Duplicate Skill

---

# 141. Language aliases

Use language codes and localized names.

---

# 142. No freeform locale confusion

Example:

```text
English
Английский
EN
```

all resolve same LanguageDefinition.

---

# 143. Admin UX — Skills

Recommended:

```text
Skills
├── Highlighted
├── All
└── Archived
```

---

# 144. Skill card

Shows:

```text
name
category
level 1–5
visibility
highlight
optional note
```

---

# 145. Skill editor

Actions:

```text
select skill
set level
edit note
set visibility
highlight
archive
```

---

# 146. Add Skill

Dictionary search first.

---

# 147. If skill absent

Explicit:

```text
Add new SkillDefinition
```

authorized Admin workflow.

---

# 148. SKL-INV-007 — Avoid Duplicate Dictionary Entries by Spelling

---

# 149. Admin UX — Languages

Recommended:

```text
Languages
├── Highlighted
├── All
└── Archived
```

---

# 150. Language editor

Actions:

```text
select language
set Native/CEFR
optional note
visibility
highlight
archive
```

---

# 151. CEFR dropdown

Only:

```text
Native
C2
C1
B2
B1
A2
A1
```

---

# 152. LNG-CEFR-003 — No Freeform CEFR Text

Not:

```text
upper-intermediate-ish
almost C1
```

as canonical proficiency.

Optional note may explain nuance.

---

# 153. Admin validation

Skill:

```text
SkillDefinition valid
level 1..5
same profile
not duplicate
```

Language:

```text
LanguageDefinition valid
proficiency allowed
same profile
not duplicate
```

---

# 154. Visibility UX

Three explicit columns/toggles:

```text
Сайт
Админ-анкета
Публичный конструктор
```

---

# 155. CAP-INV-006 — No Single “Public” Switch

---

# 156. Highlights UX

Highlight should never automatically enable public visibility.

---

# 157. CAP-INV-007 — Highlight ≠ Permission

If highlighted but not public:

Hero does not show it.

---

# 158. Public readiness

Skills are supporting evidence.

Profile can potentially remain public without every possible Skill.

---

# 159. International readiness

Languages are more important.

International readiness may warn when:

```text
no English proficiency recorded
no EN-localized supporting content
```

depending product target.

---

# 160. Questionnaire readiness

If Skills/Languages block enabled but no eligible items:

warning/block according to template requirement.

---

# 161. Builder readiness

Same.

---

# 162. Casting readiness

Missing specific requested capability produces:

```text
NO_CONFIRMED_DATA
```

not global Profile failure.

---

# 163. Lifecycle

Archive ActorSkill:

- remove current public;
- remove new QNR/Builder selection;
- retain historical snapshots.

---

# 164. Archive Language

Same.

---

# 165. CAP-INV-008 — Archive Does Not Remove Dictionary Definition

Dictionary is reusable reference data.

---

# 166. Delete

Hard delete of ActorSkill/ActorLanguage should be exceptional if historical dependencies exist.

---

# 167. Current correction

Example:

```text
Horse Riding 4 → 3
```

is valid Human-confirmed update.

---

# 168. Historical QNR

Old Revision retains 4.

---

# 169. Audit

Significant capability changes SHOULD be audited:

```text
level change
language proficiency change
public visibility
Builder/QNR eligibility
Native status
archive/restore
```

---

# 170. Why audit level changes

Casting decisions may rely on capability data.

---

# 171. Audit does not need entire public projection.

---

# 172. Concurrency

ActorSkill and ActorLanguage use `version`.

---

# 173. Stale edit

Rejected with:

```text
STALE_VERSION
```

---

# 174. Duplicate create race

Database uniqueness protects same:

```text
profile + SkillDefinition
```

or:

```text
profile + LanguageDefinition
```

---

# 175. CAP-INV-009 — Duplicate Prevention Is DB-Enforced

---

# 176. Transactions

Capability update:

```text
BEGIN
 update Source
 Audit
 Outbox
COMMIT
```

---

# 177. Post-commit consumers

```text
Hero/Quick Facts cache
Public capability projection
Search
Questionnaire readiness
Builder eligibility
VOP
```

---

# 178. Events

Suggested:

```text
ActorSkillCreated
ActorSkillUpdated
ActorSkillArchived
ActorSkillVisibilityChanged
ActorSkillHighlightChanged

ActorLanguageCreated
ActorLanguageUpdated
ActorLanguageArchived
ActorLanguageVisibilityChanged
ActorLanguageHighlightChanged
```

---

# 179. Event payloads

Minimal:

```text
profile_id
entity_id
version
changed_aspect
```

---

# 180. No sensitive narrative duplication in Outbox unless required.

---

# 181. Cache

Public capabilities derived.

Key dimensions:

```text
profile
locale
public projection generation
```

---

# 182. Visibility revocation

Strong current projection gate.

Cache invalidation post-commit.

---

# 183. CAP-INV-010 — Cache Cannot Preserve Revoked Capability Publicly

---

# 184. Analytics

Potential events:

```text
skills_section_viewed
languages_section_viewed
profile_skill_search
```

---

# 185. Analytics not used to auto-increase/decrease levels.

---

# 186. CAP-INV-011 — Engagement ≠ Capability

---

# 187. Social Publishing

BB/Social can mention Skills/Languages only from confirmed Source.

---

# 188. Example

Allowed:

```text
Владение английским языком — B2.
```

---

# 189. Not allowed without Source:

```text
Свободно владеет пятью языками.
```

---

# 190. Security/privacy

Most Skills/Languages are professional data.

Still visibility controlled.

---

# 191. Language data may reveal background but is not automatically sensitive/private or public.

Product policy decides per item.

---

# 192. Public-hidden Language

Must not leak through Hero/SEO/Search.

---

# 193. Admin DTO — Skill

Conceptually:

```text
AdminActorSkillDTO {
  id
  version
  skill
  category
  level
  note?
  visibility
  highlighted
  displayOrder
  lifecycle
}
```

---

# 194. Public Skill DTO

```text
PublicSkillDTO {
  name
  level
  levelLabel
  note?
}
```

only allowed fields.

---

# 195. Builder Skill DTO

```text
BuilderSkillDTO {
  id
  name
  level
  selected
}
```

eligible only.

---

# 196. Admin Language DTO

```text
AdminActorLanguageDTO {
  id
  version
  languageCode
  localizedName
  proficiency
  note?
  visibility
  highlighted
  displayOrder
  lifecycle
}
```

---

# 197. Public Language DTO

```text
PublicLanguageDTO {
  languageCode
  name
  proficiency
  note?
}
```

---

# 198. Commands — Skills

Canonical:

```text
CreateActorSkill
UpdateActorSkillLevel
UpdateActorSkillNote
UpdateActorSkillVisibility
SetActorSkillHighlighted
ReorderActorSkills
ArchiveActorSkill
RestoreActorSkill
```

---

# 199. Dictionary commands

Administrative:

```text
CreateSkillDefinition
UpdateSkillDefinition
ArchiveSkillDefinition
```

only when needed.

---

# 200. Commands — Languages

```text
CreateActorLanguage
UpdateActorLanguageProficiency
UpdateActorLanguageNote
UpdateActorLanguageVisibility
SetActorLanguageHighlighted
ReorderActorLanguages
ArchiveActorLanguage
RestoreActorLanguage
```

---

# 201. Queries

```text
GetPublicSkills
GetPublicLanguages
GetAdminSkills
GetAdminLanguages
GetCapabilitiesForHero
GetCapabilitiesReadiness
ListQuestionnaireEligibleSkills
ListQuestionnaireEligibleLanguages
ListBuilderEligibleSkills
ListBuilderEligibleLanguages
SearchSkillsDictionary
SearchLanguagesDictionary
```

---

# 202. Skill create input

Conceptually:

```text
skillDefinitionId
level
note?
visibility
```

---

# 203. Language create input

```text
languageDefinitionId
proficiency
note?
visibility
```

---

# 204. Client cannot set

```text
confirmedByAI
publishedAt
derivedFromTrainingAutomatically
```

as authority.

---

# 205. Error taxonomy — Skills

At minimum:

```text
SKILL_NOT_FOUND
SKILL_DEFINITION_NOT_FOUND
SKILL_LEVEL_INVALID
SKILL_DUPLICATE
SKILL_ARCHIVED
SKILL_WRONG_PROFILE
SKILL_QUESTIONNAIRE_NOT_ELIGIBLE
SKILL_BUILDER_NOT_ELIGIBLE
```

---

# 206. Error taxonomy — Languages

```text
LANGUAGE_NOT_FOUND
LANGUAGE_DEFINITION_NOT_FOUND
LANGUAGE_PROFICIENCY_INVALID
LANGUAGE_DUPLICATE
LANGUAGE_ARCHIVED
LANGUAGE_WRONG_PROFILE
LANGUAGE_QUESTIONNAIRE_NOT_ELIGIBLE
LANGUAGE_BUILDER_NOT_ELIGIBLE
```

---

# 207. Casting matching outputs

Skill:

```text
MATCH
PARTIAL_MATCH
MISMATCH
NO_CONFIRMED_DATA
REQUIRES_HUMAN_INTERPRETATION
```

---

# 208. Language:

Same pattern where qualitative source requires interpretation.

Exact CEFR requirement can be deterministic.

---

# 209. CAP-AI-004 — Match Result Is Not Source Mutation

---

# 210. Skill level wording in AI

BB/Casting AI must preserve exact source level.

---

# 211. Example source

```text
Skiing = 3/5
```

---

# 212. Safe generated text

```text
Уверенный базовый/средний навык катания на лыжах — уровень 3 из 5.
```

only if level label policy defines this equivalence.

Otherwise safest:

```text
Катание на лыжах — 3/5.
```

---

# 213. Language wording

Source:

```text
English = B2
```

Safe:

```text
Английский — B2.
```

---

# 214. Avoid ungrounded synonym

```text
свободно владеет английским
```

unless product explicitly maps B2 to that public wording.

---

# 215. CAP-AI-005 — Exact Structured Proficiency Beats Creative Rephrasing

---

# 216. Migration — Skills

Legacy freeform Skill strings should map to controlled definitions where confidence is high.

---

# 217. Example

```text
верховая езда
Horse riding
конный спорт
```

may map to one canonical definition only after migration mapping review.

---

# 218. Ambiguous skill

Requires Human review.

---

# 219. SKL-MIG-001 — Migration Does Not Invent Level

If legacy record has skill name but no 1–5 level:

level remains unresolved/review-required.

---

# 220. No default level

Never assign:

```text
3
```

because it seems “neutral”.

---

# 221. Legacy percentage/word level

If legacy uses:

```text
Beginner
Intermediate
Advanced
```

conversion to 1–5 requires explicit documented mapping or Human review.

---

# 222. SKL-MIG-002 — No Undocumented Level Conversion

---

# 223. Migration — Languages

Legacy strings like:

```text
English — intermediate
```

cannot become B1/B2 automatically unless explicit verified mapping exists.

---

# 224. LNG-MIG-001 — Qualitative Legacy Language Level Needs Mapping/Review

---

# 225. Legacy `native`

Can map to `NATIVE` when source wording is explicit and reliable.

---

# 226. Migration provenance

Should record original legacy representation where needed.

---

# 227. Migration idempotency

Same source record must not create duplicate ActorSkill/ActorLanguage.

---

# 228. Dictionary localization

Skill/Language names should not be repeated manually per profile.

---

# 229. CAP-INV-012 — Dictionary Localization Is Shared Reference Data

Actor-specific professional note remains profile-owned.

---

# 230. Archive dictionary entry

Should not destroy existing historical ActorSkill references.

---

# 231. New assignment

Archived dictionary item unavailable for new ActorSkill.

---

# 232. Existing assignment

May require migration/replacement strategy if dictionary deprecation occurs.

---

# 233. Synonym merge

If two duplicate SkillDefinitions are merged:

preserve ActorSkill identity/provenance carefully.

Not baseline Admin action.

---

# 234. International UX

Languages should be visible early for international visitors.

---

# 235. Hero hierarchy

Potential:

```text
Name
Title
City
Languages
Highlighted Skills
```

without excessive facts.

---

# 236. Mobile

Languages/Skills must be compact, not a long wall before primary CTA.

---

# 237. Accessibility

CEFR and Skill levels use readable text.

No color-only distinction.

---

# 238. Sorting

Public skills:

```text
highlighted
→ explicit order
```

Public languages:

```text
highlighted
→ explicit order
```

---

# 239. Do not sort by highest level automatically unless UX specifically requests it.

---

# 240. CAP-INV-013 — Highest Level ≠ Most Relevant Skill

---

# 241. Relevance may be context-specific

Casting-specific recommendation can reorder a temporary projection without changing Source/display order.

---

# 242. CAP-INV-014 — Casting Relevance Ordering Is Projection Only

---

# 243. Example

Normal public order:

```text
Dance
Horse Riding
Swimming
```

Casting for equestrian role:

```text
Horse Riding
Dance
Swimming
```

in casting recommendation only.

---

# 244. Source remains unchanged.

---

# 245. Anti-patterns

`CAP-AP-001`  
Store all Skills as comma-separated Profile string.

`CAP-AP-002`  
Store all Languages as freeform text field.

`CAP-AP-003`  
Allow Skill level outside 1–5.

`CAP-AP-004`  
Allow decimal Skill levels.

`CAP-AP-005`  
Interpret Skill level as percentage.

`CAP-AP-006`  
Training completion automatically creates Skill.

`CAP-AP-007`  
Training completion raises Skill to 5.

`CAP-AP-008`  
Project with horses automatically creates Horse Riding Skill.

`CAP-AP-009`  
AI analyzes photo and creates Skill.

`CAP-AP-010`  
Casting requirement adds missing Skill to Profile.

`CAP-AP-011`  
AI recommendation changes Skill level.

`CAP-AP-012`  
Engagement analytics changes highlighted Skills.

`CAP-AP-013`  
Highlight automatically makes Skill public.

`CAP-AP-014`  
One `is_public` controls Site/QNR/Builder.

`CAP-AP-015`  
Public-hidden Skill sent to browser and CSS-hidden.

`CAP-AP-016`  
Duplicate SkillDefinitions created for translations.

`CAP-AP-017`  
Store “Horse Riding” and “Верховая езда” as separate canonical Skills.

`CAP-AP-018`  
Use dictionary presence as proof actress owns Skill.

`CAP-AP-019`  
Use Training relation as current Skill level.

`CAP-AP-020`  
Store Native as C2.

`CAP-AP-021`  
Sort CEFR lexicographically.

`CAP-AP-022`  
Map “conversational” to B2 silently.

`CAP-AP-023`  
Language course changes CEFR automatically.

`CAP-AP-024`  
Speech AI automatically sets CEFR.

`CAP-AP-025`  
Nationality automatically sets Native language.

`CAP-AP-026`  
Country automatically sets Language.

`CAP-AP-027`  
AI-generated biography upgrades B2 to fluent.

`CAP-AP-028`  
Builder changes Skill 3/5 to 5/5.

`CAP-AP-029`  
Builder changes B2 to C1.

`CAP-AP-030`  
Questionnaire becomes alternate Skill source.

`CAP-AP-031`  
Current Skill update rewrites historical Questionnaire.

`CAP-AP-032`  
Current CEFR update rewrites historical PDF.

`CAP-AP-033`  
Search exposes private Language.

`CAP-AP-034`  
SEO contains archived Skill.

`CAP-AP-035`  
VOP auto-adds Skill because casting repeatedly requests it.

`CAP-AP-036`  
VOP sets English Native based on profile text.

`CAP-AP-037`  
Runtime AI required to translate dictionary labels.

`CAP-AP-038`  
Five visual stars with no textual level meaning.

`CAP-AP-039`  
Skill level represented as public rating/review score.

`CAP-AP-040`  
Castings permanently reorder public Skills.

`CAP-AP-041`  
Dictionary archive deletes historical capability references.

`CAP-AP-042`  
Migration assigns default Skill level 3.

`CAP-AP-043`  
Legacy “Intermediate” language silently becomes B2.

`CAP-AP-044`  
AI decides Native from pronunciation.

`CAP-AP-045`  
Skill alias creates second ActorSkill.

`CAP-AP-046`  
Same profile has duplicate English records.

`CAP-AP-047`  
ActorSkill hard deleted despite historical QNR references.

`CAP-AP-048`  
Skill note used to bypass controlled level.

`CAP-AP-049`  
Capability data is stored inside Hero configuration.

`CAP-AP-050`  
Theme changes level/proficiency labels semantically.

---

# 246. Core invariants

`CAP-INV-015`  
Skill and Language are separate domains.

`CAP-INV-016`  
Skills use controlled dictionary definitions.

`CAP-INV-017`  
Languages use stable language codes.

`CAP-INV-018`  
ActorSkill belongs one Profile.

`CAP-INV-019`  
ActorLanguage belongs one Profile.

`CAP-INV-020`  
One current ActorSkill per SkillDefinition/Profile.

`CAP-INV-021`  
One current ActorLanguage per Language/Profile.

`CAP-INV-022`  
Skill level is integer 1–5 only.

`CAP-INV-023`  
Skill level is ordinal, not percentage.

`CAP-INV-024`  
Skill level requires Human authority.

`CAP-INV-025`  
Training cannot set Skill level automatically.

`CAP-INV-026`  
Project experience cannot set Skill level automatically.

`CAP-INV-027`  
Casting requirement cannot set Skill level.

`CAP-INV-028`  
AI cannot increase/decrease Skill level.

`CAP-INV-029`  
ActorLanguage proficiency is one of Native/A1/A2/B1/B2/C1/C2.

`CAP-INV-030`  
Native remains distinct from C2.

`CAP-INV-031`  
CEFR comparison uses explicit domain ordering.

`CAP-INV-032`  
Language Training cannot set CEFR automatically.

`CAP-INV-033`  
Speech AI cannot set CEFR automatically.

`CAP-INV-034`  
Nationality/country cannot set Native automatically.

`CAP-INV-035`  
Unknown capability remains unknown.

`CAP-INV-036`  
Skill/Language notes cannot override structured level/proficiency.

`CAP-INV-037`  
Dictionary localization is not duplicated per profile.

`CAP-INV-038`  
Actor-specific notes remain profile-owned.

`CAP-INV-039`  
Public/QNR/Builder permissions remain independent.

`CAP-INV-040`  
Highlighted status remains independent from visibility.

`CAP-INV-041`  
Highlight is Human presentation configuration.

`CAP-INV-042`  
Hero/Quick Facts are projections.

`CAP-INV-043`  
Hidden capabilities are absent from Public DTO.

`CAP-INV-044`  
Questionnaire selection does not mutate Source.

`CAP-INV-045`  
Builder cannot alter capability values.

`CAP-INV-046`  
Builder Generate revalidates current eligibility.

`CAP-INV-047`  
Historical Questionnaire freezes exact capability values.

`CAP-INV-048`  
Current level/proficiency change never rewrites historical Revision.

`CAP-INV-049`  
Casting matching uses confirmed capability Source.

`CAP-INV-050`  
Qualitative requirement mapping to numeric/CEFR scale must be explicit.

`CAP-INV-051`  
Casting match result does not mutate Profile.

`CAP-INV-052`  
BB cannot exaggerate structured proficiency.

`CAP-INV-053`  
VOP cannot autonomously modify capabilities.

`CAP-INV-054`  
Search scope respects visibility.

`CAP-INV-055`  
SEO cannot exceed Public visibility.

`CAP-INV-056`  
Cache cannot preserve revoked capability.

`CAP-INV-057`  
Analytics cannot change capability.

`CAP-INV-058`  
Casting-specific relevance order is Projection only.

`CAP-INV-059`  
Archive removes current use but preserves history.

`CAP-INV-060`  
Dictionary archive does not erase historical references.

`CAP-INV-061`  
Core capability workflow functions without AI.

`CAP-INV-062`  
Migration cannot invent level/proficiency.

`CAP-INV-063`  
Migration requires documented mapping for qualitative legacy levels.

`CAP-INV-064`  
Migration is idempotent.

`CAP-INV-065`  
Duplicate aliases do not create duplicate capabilities.

`CAP-INV-066`  
DB uniqueness protects duplicate creation races.

`CAP-INV-067`  
Optimistic concurrency protects updates.

`CAP-INV-068`  
All authoritative mutations are server-side validated.

`CAP-INV-069`  
No appearance/body desirability scoring exists in this module.

`CAP-INV-070`  
Capability semantics remain stable across UI themes/locales.

---

# 247. E2E-CAP-001 — Create Skill

Create Horse Riding level 3.

Expected valid ActorSkill.

---

# 248. E2E-CAP-002 — Level 0

Rejected.

---

# 249. E2E-CAP-003 — Level 6

Rejected.

---

# 250. E2E-CAP-004 — Decimal level

`3.5` rejected.

---

# 251. E2E-CAP-005 — Duplicate Skill

Same SkillDefinition added twice.

Rejected by domain/DB uniqueness.

---

# 252. E2E-CAP-006 — Skill translation

RU/EN UI resolves same canonical SkillDefinition.

No duplicate ActorSkill.

---

# 253. E2E-CAP-007 — Training relation

Link Stage Combat Training to Stage Combat Skill.

Skill level remains unchanged.

---

# 254. E2E-CAP-008 — Training without Skill

Create relevant Training.

No ActorSkill created automatically.

---

# 255. E2E-CAP-009 — AI Skill suggestion

AI suggests Horse Riding level 4 from context.

No Source mutation.

---

# 256. E2E-CAP-010 — Update Skill Human

Admin changes 3→4.

Version increments; Audit/Outbox emitted.

---

# 257. E2E-CAP-011 — Stale Skill update

Second stale edit returns `STALE_VERSION`.

---

# 258. E2E-CAP-012 — Public-hidden Skill

Absent from Public Hero/Skills/Search/SEO.

---

# 259. E2E-CAP-013 — Questionnaire-only Skill

Hidden site, eligible prepared Questionnaire.

---

# 260. E2E-CAP-014 — Builder-only Skill

Visible only Builder projection.

---

# 261. E2E-CAP-015 — Highlight hidden Skill

Highlighted=true but Public=false.

Hero does not expose it.

---

# 262. E2E-CAP-016 — Hero Skill

Highlighted + public current Skill appears in Quick Facts according to configuration.

---

# 263. E2E-CAP-017 — Builder tamper Skill

Visitor submits level=5 for saved level=3.

Generation uses 3.

---

# 264. E2E-CAP-018 — Historical Skill

Questionnaire R1 contains level 3.

Current changes to 4.

R1 remains 3.

---

# 265. E2E-CAP-019 — Casting explicit Skill

Casting requires exact known skill.

Matching cites ActorSkill Source.

---

# 266. E2E-CAP-020 — Missing Skill

No ActorSkill.

Result `NO_CONFIRMED_DATA`.

---

# 267. E2E-CAP-021 — Create Language

English B2 created.

---

# 268. E2E-CAP-022 — Invalid CEFR

`B3` rejected.

---

# 269. E2E-CAP-023 — Duplicate Language

Second English ActorLanguage rejected.

---

# 270. E2E-CAP-024 — Native

Russian `NATIVE` stored distinctly.

---

# 271. E2E-CAP-025 — Native vs C2

System does not normalize Native to C2.

---

# 272. E2E-CAP-026 — CEFR comparison

Casting asks English B2+.

Profile C1 matches deterministic domain comparison.

---

# 273. E2E-CAP-027 — Native requirement

Casting asks Native English.

Profile C2 but non-Native does not silently satisfy Native.

---

# 274. E2E-CAP-028 — Qualitative language

Casting says conversational English.

No arbitrary CEFR conversion unless mapping policy configured.

---

# 275. E2E-CAP-029 — Language course

Complete English Training.

CEFR remains unchanged.

---

# 276. E2E-CAP-030 — Speech AI

AI suggests C1.

No Source change until Human action.

---

# 277. E2E-CAP-031 — Builder language tamper

Visitor changes B2→C2.

Server uses B2.

---

# 278. E2E-CAP-032 — Historical language

Questionnaire freezes B2.

Current later C1.

Historical remains B2.

---

# 279. E2E-CAP-033 — Public Language visibility

Hidden Language absent from Public Quick Facts/Search/SEO.

---

# 280. E2E-CAP-034 — Language Highlight

Public highlighted Language appears in Hero.

---

# 281. E2E-CAP-035 — AI unavailable

Skills/Languages CRUD and all public rendering remain operational.

---

# 282. E2E-CAP-036 — BB wording

Source English B2.

BB output cannot silently become “native English”.

---

# 283. E2E-CAP-037 — Search inside profile

Search Horse Riding finds public Skill.

---

# 284. E2E-CAP-038 — Search private

Private Skill not returned publicly.

---

# 285. E2E-CAP-039 — Stale Search

Visibility revoked.

Stale candidate gets removed during current hydration.

---

# 286. E2E-CAP-040 — Archive Skill

Removed from current Public/QNR/Builder.

Historical QNR preserved.

---

# 287. E2E-CAP-041 — Archive Language

Same behavior.

---

# 288. E2E-CAP-042 — Restore

Restored capability does not auto-publish if lifecycle/publication policy requires review.

---

# 289. E2E-CAP-043 — Migration missing Skill level

Legacy `Horse Riding` with no level.

No default 3 assigned.

Marked for review.

---

# 290. E2E-CAP-044 — Legacy Advanced

Legacy Skill value “Advanced”.

No undocumented integer mapping performed.

---

# 291. E2E-CAP-045 — Legacy Language Intermediate

No silent B1/B2 decision.

---

# 292. E2E-CAP-046 — Legacy Native

Explicit “native Russian” can map to NATIVE with provenance.

---

# 293. E2E-CAP-047 — Alias migration

“Horse riding” and “Верховая езда” resolve same canonical SkillDefinition.

---

# 294. E2E-CAP-048 — Accessibility

Screen reader receives:

```text
Horse Riding, уровень 4 из 5
English, уровень B2
```

rather than visual-only dots.

---

# 295. E2E-CAP-049 — Theme

Theme changes presentation but cannot change level/proficiency labels or visibility.

---

# 296. E2E-CAP-050 — Casting relevance reorder

Casting-specific projection puts Horse Riding first.

Public Profile source/display order remains unchanged.

---

# 297. Domain diagram

```text
Actor Profile
   │
   ├───────────────┐
   ▼               ▼
ActorSkill    ActorLanguage
   │               │
   ▼               ▼
SkillDefinition LanguageDefinition
```

---

# 298. Training relation diagram

```text
TrainingItem
     │
     └── related to ──→ SkillDefinition
                         │
                         ▼
                      ActorSkill
                         │
                    Human-set level

Training never sets level automatically.
```

---

# 299. Hero projection diagram

```text
ActorSkill ───────┐
ActorLanguage ────┤
Profile facts ────┤
                  ▼
            Quick Facts / Hero
```

---

# 300. Questionnaire diagram

```text
Current Skill/Language Source
          ↓
Surface Eligibility
          ↓
Questionnaire Selection
          ↓
Publish
          ↓
Immutable Revision Snapshot
```

---

# 301. Casting matching diagram

```text
Casting Requirement
       │
       ▼
Structured Extraction
       │
       ├──────── Skill Requirement
       └──────── Language Requirement
                    │
                    ▼
          Confirmed Profile Source
                    │
                    ▼
           Evidence-based Match
```

---

# 302. Human authority diagram

```text
Training / Projects / AI
          ↓
    Recommendation
          ↓
      HUMAN REVIEW
          ↓
Create / Update
ActorSkill / ActorLanguage
```

---

# 303. Quality gate

Перед implementation должны быть подтверждены:

- [ ] SkillDefinition dictionary;
- [ ] ActorSkill entity;
- [ ] exact level 1–5 scale;
- [ ] Human-confirmed level semantics;
- [ ] Skill category taxonomy;
- [ ] duplicate prevention;
- [ ] Skill localization;
- [ ] ActorSkill notes;
- [ ] Skill visibility triplet;
- [ ] Skill highlight/order;
- [ ] Training relationship without auto-level;
- [ ] Project evidence separation;
- [ ] LanguageDefinition dictionary;
- [ ] ActorLanguage entity;
- [ ] Native + CEFR A1–C2 enum;
- [ ] Native vs C2 semantics;
- [ ] explicit CEFR ordering;
- [ ] Language visibility;
- [ ] Language highlights/order;
- [ ] optional notes/accent future boundary;
- [ ] Hero/Quick Facts projections;
- [ ] Questionnaire selection;
- [ ] Builder selection-only rules;
- [ ] historical snapshot behavior;
- [ ] Casting matching;
- [ ] qualitative requirement handling;
- [ ] BB grounding rules;
- [ ] Search/SEO;
- [ ] Archive/restore;
- [ ] migration mappings;
- [ ] concurrency;
- [ ] audit/outbox;
- [ ] accessibility;
- [ ] E2E coverage.

---

# 304. Acceptance criteria

`AC-CAP-001`  
Skills и Languages существуют как отдельные structured domains.

`AC-CAP-002`  
Skills используют controlled dictionary.

`AC-CAP-003`  
Skill level допускает только integer 1–5.

`AC-CAP-004`  
Skill level не является процентом.

`AC-CAP-005`  
Skill level изменяется только Human-authorized command.

`AC-CAP-006`  
Training не создаёт Skill автоматически.

`AC-CAP-007`  
Training не повышает Skill level автоматически.

`AC-CAP-008`  
Projects не повышают Skill level автоматически.

`AC-CAP-009`  
AI не может изменять Skill level автоматически.

`AC-CAP-010`  
Один SkillDefinition не дублируется для RU/EN названия.

`AC-CAP-011`  
Один профиль не имеет duplicate current ActorSkill одного SkillDefinition.

`AC-CAP-012`  
Languages используют stable language identifiers.

`AC-CAP-013`  
Language proficiency ограничен Native/A1/A2/B1/B2/C1/C2.

`AC-CAP-014`  
Native не нормализуется в C2.

`AC-CAP-015`  
CEFR сравнивается через Domain logic.

`AC-CAP-016`  
Language Training не изменяет CEFR автоматически.

`AC-CAP-017`  
AI speech analysis не изменяет CEFR автоматически.

`AC-CAP-018`  
Country/nationality не устанавливают Native автоматически.

`AC-CAP-019`  
Public/Questionnaire/Builder permissions независимы.

`AC-CAP-020`  
Highlight не является permission.

`AC-CAP-021`  
Hero/Quick Facts используют capability projections, а не копии.

`AC-CAP-022`  
Hidden capabilities отсутствуют из Public serialization.

`AC-CAP-023`  
Prepared Questionnaire выбирает Skills/Languages без изменения Source.

`AC-CAP-024`  
Public Builder не может менять level/proficiency.

`AC-CAP-025`  
Builder Generate revalidates eligibility.

`AC-CAP-026`  
Historical Questionnaire сохраняет exact Skill level и Language proficiency.

`AC-CAP-027`  
Current capability update не переписывает historical Revision.

`AC-CAP-028`  
Casting matching основывается на confirmed Source.

`AC-CAP-029`  
Qualitative requirement не превращается в numeric/CEFR level без explicit mapping.

`AC-CAP-030`  
Missing capability yields `NO_CONFIRMED_DATA` where appropriate.

`AC-CAP-031`  
BB не может преувеличивать structured proficiency.

`AC-CAP-032`  
Search/SEO respect current visibility.

`AC-CAP-033`  
Casting relevance ordering remains Projection only.

`AC-CAP-034`  
Archive removes current exposure and preserves historical snapshots.

`AC-CAP-035`  
Migration does not invent Skill level.

`AC-CAP-036`  
Migration does not invent CEFR.

`AC-CAP-037`  
Legacy qualitative values require documented mapping or Human review.

`AC-CAP-038`  
Core Skills/Languages workflow remains fully operational without AI.

`AC-CAP-039`  
Skill/Language levels are accessible textually, not visual-only.

`AC-CAP-040`  
Concurrency, uniqueness, visibility and historical behavior have deterministic E2E coverage.

---

# 305. Финальная доктрина

> **Skills & Languages Module хранит текущие Human-confirmed профессиональные capability-факты актрисы. Навыки используют controlled dictionary и строгую пятиуровневую шкалу 1–5; языки используют стабильные language identifiers и отдельные значения Native / CEFR A1–C2. Training, Projects, AI и Casting могут предоставлять evidence или recommendations, но не имеют права автоматически создавать capability, повышать Skill level или изменять CEFR. Public Site, prepared Questionnaire и Public Builder имеют независимые eligibility permissions; Hero и Casting Quick View получают capability только через projections. Historical Questionnaires freeze exact level/proficiency values, поэтому дальнейшее профессиональное развитие не переписывает прошлые материалы.**