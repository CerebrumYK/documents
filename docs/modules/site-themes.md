# SITE THEMES MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация Theme Domain, manual editing, AI-generated structured drafts, design tokens, previews, accessibility validation, immutable ThemeRevision, activation и rollback

**Целевой файл:** `docs/modules/site-themes.md`  
**Документ:** DOC-127  
**Статус:** ✅ Completed  
**Тип:** Module / Theme / Presentation / Design Tokens / AI-Assisted Styling / Publication

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
- `docs/architecture/components.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`
- `docs/architecture/background-jobs.md`
- `docs/architecture/cache-strategy.md`
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
- `docs/modules/media-library.md`
- `docs/modules/portfolio.md`
- `docs/modules/emotional-portfolio.md`
- `docs/modules/questionnaires.md`
- `docs/modules/public-questionnaire-builder.md`

---

# 1. Назначение модуля

Site Themes Module управляет **визуальным представлением публичного сайта актрисы** без изменения профессионального содержания.

Модуль отвечает за:

- theme drafts;
- design tokens;
- typography;
- colors;
- spacing;
- surface styles;
- Hero presentation;
- decorative elements;
- layout presets;
- responsive behavior;
- manual editing;
- AI-assisted theme proposals;
- field locking;
- desktop/tablet/mobile preview;
- accessibility/contrast validation;
- ThemeRevision;
- publication;
- activation;
- temporary activation;
- rollback;
- theme history.

---

# 2. Главная доктрина

> **Theme управляет только presentation layer. Он не является владельцем Profile, Portfolio, Contacts, Questionnaire, Projects, Visibility или Security. Ни manual Theme Editor, ни Theme AI не имеют права изменять professional facts, разрешения, доступность, URL, content ownership или внешность актрисы.**

Canonical:

```text id="thm-canon-001"
Professional Source Data
        │
        ▼
Public Projections
        │
        ▼
Theme Presentation Rules
        │
        ▼
Rendered Public UI
```

Не допускается:

```text id="thm-canon-002"
Theme
  └── mutate Profile / Media / Visibility / Security
```

---

# 3. Fundamental separation

```text id="thm-canon-003"
ThemeDraft
≠ ThemeRevision
≠ ActiveThemePointer
≠ Site Content
≠ MediaAsset
≠ Profile
≠ Visibility Policy
≠ AI Theme Proposal
```

---

# 4. Core invariant

```text id="thm-canon-004"
CONTENT AUTHORITY ≠ PRESENTATION AUTHORITY
```

---

# 5. Module identifiers

Используются:

```text id="thm-ids-001"
THM-*
THM-DRF-*
THM-TOK-*
THM-HERO-*
THM-DEC-*
THM-LCK-*
THM-PRV-*
THM-A11Y-*
THM-AI-*
THM-REV-*
THM-ACT-*
THM-RBK-*
THM-SEC-*
THM-INV-*
THM-AP-*
E2E-THM-*
```

---

# 6. Theme scope

Theme MAY control:

```text id="thm-scope-001"
colors
typography
font scale
spacing
radius
borders
shadows
surface/background rules
buttons
cards
section spacing
navigation styling
hero layout
hero overlay
decorative graphics
section presentation presets
responsive presentation tokens
motion tokens within accessibility limits
```

---

# 7. Theme does NOT own

Theme MUST NOT own:

```text id="thm-scope-002"
actor name
biography
height
age/date data
skills
languages
projects
roles
training
contacts
media classification
questionnaire selection
casting data
feedback
visibility flags
permissions
public/private state
SEO professional facts
canonical URLs
```

---

# 8. THM-INV-001 — Theme Cannot Mutate Content

---

# 9. Theme content references

Theme MAY reference presentation slots such as:

```text id="thm-scope-003"
hero image slot
primary CTA style
section layout preset
decorative background slot
```

but must not redefine the underlying professional entity.

---

# 10. Example

Valid:

```text id="thm-example-001"
Hero layout = split-left
```

Invalid:

```text id="thm-example-002"
Hero actor name = "new name"
```

---

# 11. Theme lifecycle

Recommended:

```text id="thm-state-001"
DRAFT
VALIDATED
PUBLISHED
ARCHIVED
```

Activation is orthogonal.

---

# 12. Theme publication vs activation

A ThemeRevision may be published but not currently active.

---

# 13. THM-INV-002 — Published ≠ Active

---

# 14. Active pointer

Current active theme is a separate configuration pointer.

Conceptually:

```text id="thm-active-001"
active_theme_revision_id
```

---

# 15. Theme Draft

Editable working configuration.

---

# 16. ThemeRevision

Immutable published theme snapshot.

---

# 17. Active Theme

Pointer to exactly one applicable ThemeRevision per scope.

---

# 18. Scope baseline

For current product:

```text id="thm-scope-004"
one active public site theme
```

per profile/site context.

---

# 19. THM-INV-003 — At Most One Active Theme Per Scope

---

# 20. ThemeDraft entity

Suggested logical entity:

```text id="thm-draft-001"
theme_drafts
```

---

# 21. Representative fields

```text id="thm-draft-002"
id
profile_id
name
base_revision_id?
configuration
version
validation_state
created_by
created_at
updated_at
```

---

# 22. Draft configuration

Should be structured.

Avoid unrestricted arbitrary CSS.

---

# 23. THM-DRF-001 — Theme Draft Uses Typed Schema

---

# 24. Example structure

```text id="thm-draft-003"
{
  tokens,
  components,
  hero,
  navigation,
  sections,
  decoration,
  motion,
  responsive
}
```

---

# 25. No arbitrary executable content

Theme config MUST NOT contain:

- JavaScript;
- arbitrary React;
- remote executable code;
- inline event handlers;
- unrestricted HTML.

---

# 26. THM-SEC-001 — Theme Is Declarative

---

# 27. Design tokens

Canonical token groups:

```text id="thm-tokens-001"
color
typography
spacing
radius
border
shadow
layout
motion
breakpoint
```

---

# 28. Color tokens

Examples:

```text id="thm-tokens-002"
color.background.primary
color.background.secondary
color.surface
color.text.primary
color.text.secondary
color.text.inverse
color.accent
color.accent.hover
color.border
color.focus
color.error
color.success
```

---

# 29. Semantic tokens preferred

Avoid component-specific hardcoded colors where semantic token works.

---

# 30. THM-TOK-001 — Semantic Tokens Over Raw Styling Duplication

---

# 31. Typography tokens

Examples:

```text id="thm-tokens-003"
font.family.display
font.family.body
font.weight.regular
font.weight.medium
font.weight.bold
font.size.xs
font.size.sm
font.size.md
font.size.lg
font.size.xl
font.size.hero
line_height.*
letter_spacing.*
```

---

# 32. Font safety

Only approved/available font families.

---

# 33. THM-TOK-002 — Theme Cannot Reference Arbitrary Unsafe External Font Source

---

# 34. External font loading

If supported:

must follow privacy/performance/CSP policy.

---

# 35. Spacing tokens

Use bounded scale.

Example:

```text id="thm-tokens-004"
space.1
space.2
space.3
space.4
space.6
space.8
space.12
space.16
```

---

# 36. Radius/border/shadow

Controlled token schema.

---

# 37. Motion

Allowed for:

- hover;
- transitions;
- section reveal;
- nonessential decorative motion.

---

# 38. THM-TOK-003 — Reduced Motion Must Be Respected

---

# 39. Theme cannot make content dependent on animation.

---

# 40. Hero configuration

Theme may control:

```text id="thm-hero-001"
layout preset
text alignment
content max width
image placement
overlay style
background treatment
CTA visual hierarchy
decorative accents
```

---

# 41. Theme cannot control Hero professional facts

Not allowed:

```text id="thm-hero-002"
change actor name
rewrite title
remove required CTA by content override
change profile location
replace contact target
```

---

# 42. THM-HERO-001 — Hero Content Comes From Projection

---

# 43. Hero image

Theme may choose a **presentation mode** for an already eligible configured Hero media source.

---

# 44. Theme must not independently choose hidden/private MediaAsset.

---

# 45. THM-HERO-002 — Theme Cannot Escalate Media Eligibility

---

# 46. Hero crop

For actor imagery:

default must respect module/source framing.

Theme cannot introduce destructive arbitrary crop that contradicts media/module rules.

---

# 47. Especially

Full Body/portfolio semantics remain protected.

---

# 48. THM-HERO-003 — Theme Cannot Violate Media Framing Rules

---

# 49. Appearance edits

Theme and Theme AI MUST NOT:

- reshape face/body;
- retouch actress;
- alter skin;
- change hair;
- alter body proportions;
- generate replacement appearance;
- alter clothing/person.

---

# 50. THM-SEC-002 — Theme Cannot Alter Actress Appearance

---

# 51. Decorative elements

Theme may use:

```text id="thm-decor-001"
gradients
lines
geometric shapes
subtle textures
background accents
abstract SVGs
section separators
```

---

# 52. Decorative image assets

If product supports uploaded decorative assets:

must be explicitly classified as Theme Decoration, not professional actor media.

---

# 53. THM-DEC-001 — Decoration ≠ Portfolio Media

---

# 54. Decoration cannot obscure professional content or CTA.

---

# 55. THM-DEC-002 — Decoration Is Subordinate to Information Hierarchy

---

# 56. Component styling

Theme can define presentation variants for:

```text id="thm-component-001"
button
card
tag
badge
navigation
section
gallery frame
video container
questionnaire CTA
contact CTA
footer
```

---

# 57. Structural limitation

Theme MAY choose from controlled layout presets.

It should not arbitrarily recompose domain information beyond approved presentation options.

---

# 58. THM-INV-004 — Theme Cannot Change Information Architecture Semantics

---

# 59. Example

Allowed:

```text id="thm-example-003"
Projects = cards / compact rows
```

Not allowed:

```text id="thm-example-004"
Move private Casting records onto homepage
```

---

# 60. Manual Theme Editor

Admin can modify structured fields.

---

# 61. Recommended editor sections

```text id="thm-editor-001"
Overview
Colors
Typography
Spacing
Components
Navigation
Hero
Sections
Decoration
Motion
Responsive
Accessibility
Preview
History
Publish
```

---

# 62. Live preview

Required device modes:

```text id="thm-preview-001"
DESKTOP
TABLET
MOBILE
```

---

# 63. THM-PRV-001 — All Three Preview Modes Required

---

# 64. Preview should use representative current Public Projection.

---

# 65. It must not use Admin-only/hidden content to “fill layout”.

---

# 66. THM-PRV-002 — Preview Respects Public Visibility

---

# 67. Preview does not publish.

---

# 68. THM-PRV-003 — Preview ≠ Activation

---

# 69. Preview frame

Should allow switching common viewport classes.

Exact pixel presets are implementation detail.

---

# 70. Responsive validation

Must check:

- overflow;
- unusable navigation;
- clipped CTAs;
- unreadable typography;
- media overflow;
- horizontal scrolling caused by theme.

---

# 71. THM-PRV-004 — Responsive Preview Has Validation, Not Only Screenshot

---

# 72. Manual field locking

Admin can lock theme fields/tokens before AI generation.

---

# 73. Examples

```text id="thm-lock-001"
lock accent color
lock typography
lock Hero layout
lock navigation style
```

---

# 74. Lock semantics

AI regeneration must preserve locked values exactly.

---

# 75. THM-LCK-001 — Locked Field Is Immutable to AI Proposal Apply

---

# 76. Manual edit after AI

Human can edit any unlocked allowed field.

---

# 77. Lock is Theme configuration metadata.

Not security permission.

---

# 78. THM-LCK-002 — Lock Prevents AI Overwrite, Not Authorized Human Editing

Unless product explicitly offers hard Human lock mode.

Baseline lock is AI protection.

---

# 79. AI theme generation

User can provide prompt such as:

```text id="thm-ai-001"
Сделай сайт более кинематографичным, минималистичным, тёмным,
с акцентом на портфолио и видеовизитку.
```

---

# 80. AI output

MUST be structured Theme Proposal.

---

# 81. THM-AI-001 — AI Never Returns Executable Theme Code as Authority

---

# 82. AI may propose:

```text id="thm-ai-002"
tokens
component variants
hero layout preset
section spacing
decoration preset
motion preset
```

---

# 83. AI cannot propose arbitrary:

```text id="thm-ai-003"
CSS
JavaScript
React code
HTML injection
database changes
visibility changes
```

as directly applicable configuration.

---

# 84. AI prompt input

May include:

- textual style intent;
- current structured theme;
- locked fields;
- approved design constraints;
- accessibility constraints.

---

# 85. AI does NOT need

- private Contacts;
- Castings;
- Feedback;
- Opportunities;
- Admin notes.

---

# 86. THM-AI-002 — Theme AI Context Is Presentation-Only

---

# 87. Theme AI output state

Suggested:

```text id="thm-ai-004"
GENERATED
VALIDATED
NEEDS_REVIEW
APPLIED_TO_DRAFT
DISCARDED
```

---

# 88. AI proposal is not ThemeRevision.

---

# 89. THM-AI-003 — AI Proposal Is Non-Authoritative

---

# 90. Apply flow

Canonical:

```text id="thm-ai-flow-001"
Prompt
  ↓
Theme AI
  ↓
Structured Proposal
  ↓
Schema validation
  ↓
Lock validation
  ↓
Accessibility validation
  ↓
Human preview
  ↓
Human Apply to Draft
```

---

# 91. THM-AI-004 — Human Apply Required

---

# 92. AI cannot auto-publish.

---

# 93. THM-AI-005 — AI Cannot Activate Theme

---

# 94. Schema validation

AI output validated against allowlisted Theme schema.

---

# 95. Unknown property

Rejected.

---

# 96. Unsafe token

Rejected.

---

# 97. Invalid component preset

Rejected.

---

# 98. THM-AI-006 — Provider Output Is Untrusted

---

# 99. Prompt injection

If current page content contains malicious text, Theme AI must not treat that content as authority.

---

# 100. Baseline

Theme AI should not need arbitrary public page content at all.

---

# 101. THM-AI-007 — Theme AI Does Not Consume Untrusted Site Copy Unless Required

---

# 102. AI regeneration

May support:

```text id="thm-ai-005b"
Regenerate all unlocked fields
Regenerate colors
Regenerate typography
Regenerate Hero
Regenerate decoration
```

---

# 103. Locked fields persist.

---

# 104. AI generation provenance

Record:

```text id="thm-ai-prov-001"
prompt
prompt_version
provider
model
source_theme_version
locked_fields
generated proposal
created_by
created_at
```

according to AI retention policy.

---

# 105. THM-AI-008 — AI Provenance Is Traceable

---

# 106. Accessibility

Theme must pass accessibility checks before Publish.

---

# 107. Required checks

At minimum:

```text id="thm-a11y-001"
text contrast
interactive contrast
focus visibility
minimum usable font sizes
link/button distinguishability
reduced motion
responsive readability
keyboard-visible focus
```

---

# 108. Contrast

Use applicable WCAG contrast thresholds in NFR/accessibility documents.

Theme module must expose deterministic validation result.

---

# 109. THM-A11Y-001 — Contrast Validation Is Server/Application Rule

Not model opinion.

---

# 110. AI cannot waive contrast.

---

# 111. THM-A11Y-002 — AI Cannot Override Accessibility Blockers

---

# 112. Accessibility validation classes

```text id="thm-a11y-002"
BLOCKER
WARNING
INFO
```

---

# 113. Blockers

Examples:

- body text contrast below allowed threshold;
- invisible focus;
- text effectively unreadable;
- required CTA not distinguishable;
- layout unusable at required viewport.

---

# 114. Warnings

Examples:

- decorative contrast low but nonessential;
- dense typography;
- excessive motion recommendation.

---

# 115. Publish blocked on hard accessibility failures.

---

# 116. THM-A11Y-003 — Publish Requires Hard Accessibility Checks Passing

---

# 117. Theme validation

Broader than accessibility.

---

# 118. Validation dimensions

```text id="thm-validation-001"
SCHEMA
ACCESSIBILITY
RESPONSIVE
CONTENT_SAFETY
MEDIA_FRAMING
SECURITY
COMPONENT_COMPATIBILITY
```

---

# 119. Content safety validation

Ensures Theme config does not:

- hide mandatory professional blocks through unsupported mechanism;
- replace URLs;
- override source strings;
- suppress legal/privacy content;
- expose private sections.

---

# 120. THM-INV-005 — Theme Validation Includes Domain Boundary Checks

---

# 121. ThemeRevision

Immutable published snapshot.

Suggested:

```text id="thm-rev-001"
theme_revisions
```

---

# 122. Fields

Conceptually:

```text id="thm-rev-002"
id
profile_id
revision_number
name
configuration_snapshot
schema_version
validation_snapshot
published_by
published_at
checksum
```

---

# 123. THM-REV-001 — ThemeRevision Is Immutable

---

# 124. Revision numbering

Monotonic per site/profile theme lineage.

---

# 125. Entity version vs revision number

Distinct.

---

# 126. THM-REV-002 — Draft Version ≠ Theme Revision Number

---

# 127. Publish

Canonical command:

```text id="thm-cmd-001"
PublishTheme
```

---

# 128. Publish flow

```text id="thm-pub-flow-001"
load Draft
↓
expectedVersion
↓
validate schema
↓
validate locks/config
↓
validate accessibility
↓
validate responsive compatibility
↓
validate security/content boundaries
↓
BEGIN
 create immutable ThemeRevision
 Audit
 Outbox
COMMIT
```

---

# 129. Publish does not automatically need to Activate.

---

# 130. THM-REV-003 — Publish and Activate Are Separate Commands

---

# 131. Why

Allows:

- review;
- staging;
- scheduled/internal approval;
- instant rollback safety.

---

# 132. Activate Theme

Canonical:

```text id="thm-cmd-002"
ActivateThemeRevision
```

---

# 133. Preconditions

```text id="thm-activate-001"
published revision exists
same profile/scope
validation state acceptable
not archived/revoked
expected active-generation valid
```

---

# 134. Activation atomic

```text id="thm-activate-002"
BEGIN
 lock active theme scope
 update active pointer
 record activation history
 Audit
 Outbox
COMMIT
```

---

# 135. THM-ACT-001 — Activation Is Atomic

---

# 136. Exactly one active revision

DB/application guard.

---

# 137. Activation cache invalidation

Must invalidate/repoint:

- public page render cache;
- theme token cache;
- SSR/static rendering dependencies;
- preview/current theme alias.

---

# 138. THM-ACT-002 — Active Pointer Is Strongly Consistent

---

# 139. Temporary Theme

Product requirement allows temporary themes.

---

# 140. Canonical use cases

- event/premiere;
- seasonal campaign;
- short-term promotional style.

---

# 141. Temporary activation model

May include:

```text id="thm-temp-001"
activate_at?
deactivate_at?
fallback_revision_id
```

---

# 142. Baseline rule

Temporary activation still references a published ThemeRevision.

---

# 143. THM-ACT-003 — Temporary Theme Is Not Unpublished Draft

---

# 144. Scheduled activation

If implemented:

scheduler executes pre-approved activation command at configured time.

---

# 145. THM-ACT-004 — Scheduler Cannot Invent/Generate Theme

---

# 146. Scheduled deactivation

Returns to explicitly stored fallback revision.

---

# 147. No random “previous theme” guess if activation history ambiguous.

---

# 148. THM-ACT-005 — Temporary Theme Requires Explicit Fallback

---

# 149. Rollback

Rollback means switching active pointer to a prior valid ThemeRevision.

---

# 150. It does NOT mutate/remove newer revisions.

---

# 151. Command:

```text id="thm-cmd-003"
RollbackTheme
```

---

# 152. THM-RBK-001 — Rollback Preserves History

---

# 153. Rollback preconditions

Target revision must:

- exist;
- be same scope;
- not be prohibited/revoked;
- remain compatible enough with current renderer schema or pass compatibility layer.

---

# 154. Compatibility

If very old ThemeRevision uses unsupported schema:

rollback may require migration/re-render compatibility validation.

---

# 155. THM-RBK-002 — Historical Revision Is Not Automatically Activatable Forever

History stays immutable, but activation compatibility can change.

---

# 156. Rollback is explicit Human action baseline.

---

# 157. Automatic rollback

May be allowed only for deterministic technical failure after activation if explicitly configured.

Example:

```text id="thm-rbk-auto-001"
theme render health check fails catastrophically
```

---

# 158. Even then:

rollback target must be known last-known-good revision.

---

# 159. THM-RBK-003 — Auto-Rollback Is Technical Safety, Not Design Decision

---

# 160. Theme activation history

Suggested:

```text id="thm-history-001"
theme_activation_history
```

---

# 161. Fields

```text id="thm-history-002"
from_revision_id
to_revision_id
reason
activated_by
activated_at
```

---

# 162. THM-ACT-006 — Activation History Is Append-Only

---

# 163. Draft from Revision

Admin may create new Draft based on any compatible ThemeRevision.

---

# 164. Command:

```text id="thm-cmd-004"
CreateThemeDraftFromRevision
```

---

# 165. This copies structured configuration into mutable Draft.

Does not mutate source Revision.

---

# 166. THM-REV-004 — Editing Published Theme Always Produces Draft/New Revision

---

# 167. Clone Theme

Allowed.

Creates new Draft lineage.

---

# 168. No copied publication history.

---

# 169. THM-INV-006 — Clone Does Not Fake Revision History

---

# 170. Default Theme

System should have safe baseline/default ThemeRevision.

---

# 171. If no custom theme

Fallback to system default.

---

# 172. THM-ACT-007 — Site Must Remain Renderable Without AI-Created Theme

---

# 173. Theme failure

If current Theme config cannot render due corruption:

fail safely to last-known-good/default presentation according to operations policy.

---

# 174. Never expose raw config error publicly.

---

# 175. THM-INV-007 — Theme Failure Must Not Take Down Professional Content

---

# 176. Renderer compatibility

Components use theme tokens/presets through typed interfaces.

---

# 177. UI components must not query AI output directly.

---

# 178. THM-INV-008 — Public Renderer Consumes Active ThemeRevision Only

Not ThemeDraft or raw AI proposal.

---

# 179. Current alias

Public rendering resolves current active revision.

---

# 180. Immutable revision cache

Can key:

```text id="thm-cache-001"
theme_revision_id + renderer_schema_version
```

---

# 181. Current theme cache

Uses active revision pointer generation.

---

# 182. THM-ACT-008 — Stale Cache Cannot Keep Old Theme Active After Pointer Change

---

# 183. Cache race

Old rebuild for Revision N cannot overwrite current alias pointing to Revision N+1.

---

# 184. THM-ACT-009 — Theme Cache Uses Generation-Safe Pointer Semantics

---

# 185. Search

Theme itself is not Public Search content.

---

# 186. Search indexing of professional content must not depend on visual theme.

---

# 187. THM-INV-009 — Theme Change Does Not Change Search Truth

---

# 188. SEO

Theme MAY affect presentation metadata style components, but not professional SEO facts/structured data semantics.

---

# 189. Theme cannot alter canonical professional values in JSON-LD.

---

# 190. THM-INV-010 — Theme Does Not Rewrite SEO Source Facts

---

# 191. Questionnaire integration

Theme may visually style public Questionnaire pages.

---

# 192. It cannot:

- remove mandatory Close-Up;
- remove mandatory Full Body;
- alter link targets;
- recompose Emotional Grid;
- hide shooting date required by document semantics.

---

# 193. THM-INV-011 — Questionnaire Semantic Rules Override Theme

---

# 194. PDF

Site Theme should not automatically control professional PDF document templates unless explicitly mapped.

PDF module has its own controlled renderer/template behavior.

---

# 195. THM-INV-012 — Site Theme ≠ PDF Document Authority

---

# 196. Public Builder

Theme may style Builder shell/preview.

It cannot change Builder eligibility or generated content.

---

# 197. THM-INV-013 — Builder Security Is Theme-Independent

---

# 198. Media Library

Theme uses already approved derivatives.

---

# 199. It must not request original/private media directly.

---

# 200. THM-SEC-003 — Public Theme Cannot Escalate Storage Access

---

# 201. Contact CTA

Theme may style buttons.

It cannot change:

```text id="thm-contact-001"
phone
email
WhatsApp URL
social URL
```

---

# 202. THM-INV-014 — CTA Target Is Domain-Owned

---

# 203. Navigation

Theme may choose visual navigation variant.

---

# 204. It cannot expose hidden Admin/public-disabled routes.

---

# 205. THM-SEC-004 — Navigation Visibility Comes From Route/Product Policy

---

# 206. Accessibility and content order

Theme may alter visual composition but must preserve logical DOM/reading order where accessibility requires it.

---

# 207. THM-A11Y-004 — Visual Reordering Cannot Break Semantic Reading Order

---

# 208. Motion accessibility

If user has `prefers-reduced-motion`:

nonessential animations must be disabled/reduced.

---

# 209. THM-A11Y-005 — Reduced Motion Is User Override

---

# 210. Focus style

Theme must provide visible focus indicator.

---

# 211. Cannot set:

```text id="thm-bad-focus-001"
outline: none
```

without accessible alternative.

---

# 212. THM-A11Y-006 — Focus Visibility Is Mandatory

---

# 213. Text scaling

Theme should remain usable with browser text zoom.

---

# 214. No fixed-height clipping of critical text.

---

# 215. THM-A11Y-007 — Critical Content Cannot Be Clipped by Theme

---

# 216. Responsive rules

Theme config may define controlled variants by breakpoint.

---

# 217. Example:

```text id="thm-responsive-001"
hero.desktop = split
hero.tablet = stacked
hero.mobile = stacked
```

---

# 218. Breakpoints themselves should be globally controlled/bounded.

---

# 219. THM-PRV-005 — AI Cannot Invent Arbitrary Breakpoint Explosion

---

# 220. Theme schema version

Required.

---

# 221. Example:

```text id="thm-schema-001"
theme_schema_version = 3
```

---

# 222. Schema migration

Old revisions remain immutable.

Renderer/activation compatibility layer handles them.

---

# 223. THM-REV-005 — Schema Migration Does Not Rewrite Historical Revision

---

# 224. Draft migration

Mutable Draft may be migrated forward.

---

# 225. Published revision

Never rewritten in place.

---

# 226. Import/export

Optional future capability:

```text id="thm-import-001"
Export theme configuration
Import theme configuration
```

---

# 227. Import must validate same typed schema.

---

# 228. Imported Theme starts as Draft.

---

# 229. THM-SEC-005 — Imported Theme Is Never Auto-Activated

---

# 230. Arbitrary CSS file import

Not baseline.

---

# 231. Security

Theme config must be safe to deserialize/render.

---

# 232. No:

- prototype pollution payload;
- arbitrary object path override;
- unsanitized CSS URL;
- script URL;
- remote code.

---

# 233. THM-SEC-006 — Theme Schema Is Strictly Allowlisted

---

# 234. CSS value validation

If raw low-level values are allowed:

must use bounded validated formats.

Prefer enum/token references.

---

# 235. Example valid:

```text id="thm-css-valid-001"
borderRadius = "lg"
```

instead of arbitrary:

```text id="thm-css-invalid-001"
borderRadius = "calc(... malicious/unbounded ...)"
```

---

# 236. External asset URL

Theme should not accept arbitrary remote background URL baseline.

---

# 237. THM-SEC-007 — Theme Is Not SSRF/Remote Asset Fetch Surface

---

# 238. Audit

Must include:

```text id="thm-audit-001"
Theme Draft created
AI proposal requested
AI proposal applied/discarded
Theme published
Theme activated
Theme rolled back
Temporary activation scheduled/cancelled
Theme archived
```

---

# 239. Routine field edits

May be covered by Draft version/history depending audit granularity.

---

# 240. THM-INV-015 — Publish/Activate/Rollback Are High-Impact Audited Actions

---

# 241. Outbox events

Suggested:

```text id="thm-events-001"
ThemeDraftUpdated
ThemePublished
ThemeActivated
ThemeRolledBack
ThemeArchived
ThemeValidationFailed
```

---

# 242. Consumers

```text id="thm-events-002"
Public cache
SSR/rendering
VOP
Analytics
Admin Dashboard
Observability
```

---

# 243. Notification

Theme activation itself usually does not need external notification.

Failure may produce Admin In-App alert.

---

# 244. AI provider failure

Does not affect manual Theme Editor.

---

# 245. THM-AI-009 — Manual Theme Workflow Works Without AI

---

# 246. VOP

May detect:

```text id="thm-vop-001"
contrast issue
responsive overflow
broken theme token
stale preview
failed activation health check
theme using deprecated schema
```

---

# 247. VOP may:

- recompute validation;
- invalidate cache;
- rerun technical preview build.

---

# 248. VOP cannot:

- choose brand direction;
- publish theme;
- activate theme;
- change locked design values.

---

# 249. THM-INV-016 — VOP Cannot Become Autonomous Designer/Publisher

---

# 250. Analytics

Can measure:

```text id="thm-analytics-001"
theme_revision activated
page conversion before/after
CTA engagement
mobile performance
```

---

# 251. Critical rule

Analytics may recommend theme improvement.

It cannot auto-switch active Theme.

---

# 252. THM-INV-017 — Analytics Does Not Auto-Optimize Theme

---

# 253. A/B testing

Not baseline.

If later supported:

must be explicit experiment domain.

Do not silently route visitors to AI-generated variants.

---

# 254. THM-INV-018 — Theme Module Does Not Implicitly Become Experimentation Platform

---

# 255. Performance

Theme must not permit:

- huge unbounded shadows/filters;
- excessive animation;
- large remote decoration payloads;
- layout thrashing patterns.

---

# 256. Theme validation may include performance budgets.

---

# 257. THM-INV-019 — Theme Cannot Override NFR Performance Budgets

---

# 258. Core Web rendering

Theme token resolution should be deterministic and cheap.

---

# 259. No runtime AI calls during page render.

---

# 260. THM-INV-020 — Public Render Is Deterministic

---

# 261. AI output is precomputed Draft only.

---

# 262. Theme preview URL

Admin-only or protected preview context.

---

# 263. Preview token

If tokenized, scoped and short-lived.

---

# 264. THM-SEC-008 — Preview Cannot Expose Admin Data

---

# 265. Public visitor must never discover unpublished Draft through predictable URL.

---

# 266. THM-SEC-009 — Draft Preview Is Access-Controlled

---

# 267. Preview Source

Use Public Projection.

Not Admin full model.

---

# 268. THM-SEC-010 — Theme Preview Does Not Leak Hidden Content

---

# 269. Draft save

Normal save is not Publish.

---

# 270. THM-DRF-002 — Save ≠ Publish

---

# 271. Publish is explicit.

---

# 272. Activate is explicit.

---

# 273. Rollback is explicit.

---

# 274. State diagram

```text id="thm-state-002"
DRAFT
  ↓ validate
VALIDATED
  ↓ publish
PUBLISHED REVISION
  ↓ activate
ACTIVE

Editing ACTIVE revision:
ACTIVE REVISION
  ↓ clone to Draft
DRAFT
  ↓ publish
NEW REVISION
```

---

# 275. Archive

Draft or published Theme lineage may be archived.

---

# 276. Active ThemeRevision cannot be destructively archived without first switching active pointer safely.

---

# 277. THM-ACT-010 — Active Theme Cannot Be Removed Without Replacement/Fallback

---

# 278. Hard delete

Exceptional.

Historical ThemeRevision referenced by activation history should normally remain.

---

# 279. Theme artifact purge

Generated CSS/token bundles are rebuildable derived artifacts.

May be purged/regenerated.

---

# 280. THM-INV-021 — ThemeRevision Outlives Derived Render Artifact

---

# 281. Retention

Classes:

```text id="thm-retention-001"
Theme Draft
ThemeRevision
AI Proposal
Activation History
Generated theme assets
Preview artifacts
```

---

# 282. ThemeRevision/ActivationHistory

Long-term history.

---

# 283. AI raw output

Potentially shorter retention.

---

# 284. Preview artifacts

Short-lived.

---

# 285. THM-INV-022 — Theme Retention Is Component-Specific

---

# 286. Restore

After backup restore:

- active pointer must reconcile with activation ledger;
- expired temporary activation must not reappear incorrectly;
- scheduled old activation must not fire if already superseded;
- AI request must not rerun;
- previous activation notifications not replayed.

---

# 287. THM-ACT-011 — Restore Reconciles Activation Generation Before Serving Public Site

---

# 288. Last-known-good

Operations should know last valid active revision.

---

# 289. If restored current pointer references unavailable/corrupt artifact:

regenerate artifact or use validated fallback according to operations policy.

---

# 290. Migration

Existing hardcoded site styles need migration to typed Theme schema.

---

# 291. Migration should identify:

```text id="thm-mig-001"
colors
fonts
spacing
hero presentation
component variants
decorative assets
responsive rules
```

---

# 292. Legacy arbitrary CSS

Do NOT blindly import unrestricted CSS into canonical Theme schema.

---

# 293. THM-MIG-001 — Legacy CSS Requires Mapping/Review

---

# 294. Unsupported rule

Classify:

```text id="thm-mig-002"
MAPPED
REQUIRES_REVIEW
DROPPED_AS_UNSAFE
```

with report.

---

# 295. No silent styling loss.

---

# 296. Initial current production look

Should be captured as a baseline ThemeRevision after validated migration.

---

# 297. THM-MIG-002 — Migration Establishes Baseline Revision

---

# 298. Existing custom Hero crop/style

Must be reconciled with Media/Portfolio framing rules.

---

# 299. If legacy style violates them:

do not preserve the violation merely for pixel-perfect migration.

---

# 300. THM-MIG-003 — Domain/Accessibility Rules Override Legacy Styling

---

# 301. AI migration

Do not ask AI to reinterpret entire legacy CSS as authoritative migration.

AI may assist mapping suggestions.

Human/validator decides.

---

# 302. THM-MIG-004 — AI Migration Suggestion Is Non-Authoritative

---

# 303. Migration idempotency

Required.

---

# 304. Error taxonomy

At minimum:

```text id="thm-errors-001"
THEME_NOT_FOUND
THEME_DRAFT_NOT_FOUND
THEME_REVISION_NOT_FOUND
THEME_SCHEMA_INVALID
THEME_TOKEN_INVALID
THEME_COMPONENT_VARIANT_INVALID
THEME_LOCK_VIOLATION
THEME_ACCESSIBILITY_BLOCKED
THEME_CONTRAST_INVALID
THEME_RESPONSIVE_INVALID
THEME_MEDIA_FRAMING_INVALID
THEME_SECURITY_POLICY_VIOLATION
THEME_AI_NOT_AVAILABLE
THEME_AI_RESPONSE_INVALID
THEME_AI_LOCK_CONFLICT
THEME_PUBLISH_CONFLICT
THEME_ACTIVATION_CONFLICT
THEME_REVISION_NOT_ACTIVATABLE
THEME_ROLLBACK_TARGET_INVALID
THEME_TEMPORARY_FALLBACK_REQUIRED
THEME_STALE_VERSION
```

---

# 305. Commands

Canonical:

```text id="thm-commands-001"
CreateThemeDraft
CreateThemeDraftFromRevision
UpdateThemeTokens
UpdateThemeComponentConfig
UpdateThemeHeroConfig
UpdateThemeDecoration
UpdateThemeResponsiveConfig
LockThemeField
UnlockThemeField
RequestThemeAIProposal
ApplyThemeAIProposal
DiscardThemeAIProposal
ValidateThemeDraft
PublishTheme
ActivateThemeRevision
ScheduleTemporaryTheme
CancelTemporaryTheme
RollbackTheme
ArchiveTheme
RestoreTheme
```

---

# 306. Queries

```text id="thm-queries-001"
GetThemeDraft
GetThemePreview
GetThemeValidation
GetActiveTheme
GetThemeRevision
ListThemeRevisions
GetThemeRevisionDiff
GetThemeActivationHistory
GetThemeAIProposal
```

---

# 307. AI request input

Conceptually:

```text id="thm-ai-input-001"
themeDraftId
prompt
scope?
expectedVersion
```

---

# 308. Client cannot specify

```text id="thm-ai-input-002"
raw system prompt
provider secret
arbitrary CSS execution
visibility overrides
content changes
```

---

# 309. Apply AI Proposal input

```text id="thm-ai-apply-001"
proposalId
expectedDraftVersion
selectedSections?
```

---

# 310. Server revalidates locks/current Draft.

---

# 311. THM-AI-010 — Proposal Apply Is Concurrency-Safe

---

# 312. Publish input

```text id="thm-publish-input-001"
themeDraftId
expectedVersion
idempotencyKey
```

---

# 313. Publish idempotency

Same semantic Draft publication retry → same ThemeRevision result.

---

# 314. THM-REV-006 — Publish Is Idempotent

---

# 315. Activate input

```text id="thm-activate-input-001"
themeRevisionId
expectedActiveGeneration
idempotencyKey
```

---

# 316. Activation idempotency

Retry cannot create duplicate activation side effects.

---

# 317. THM-ACT-012 — Activation Is Idempotent

---

# 318. Rollback input

```text id="thm-rollback-input-001"
targetRevisionId
reason?
expectedActiveGeneration
idempotencyKey
```

---

# 319. Concurrency

Draft uses `version`.

Active theme pointer uses generation/version.

---

# 320. Two Admins publishing same Draft

One semantic publish wins according to expectedVersion/idempotency.

---

# 321. Two Admins activating two revisions

Serialized by active-theme scope lock/generation.

---

# 322. THM-ACT-013 — Concurrent Activation Cannot Produce Two Active Revisions

---

# 323. Temporary activation race

Scheduled activation rechecks current schedule generation.

---

# 324. Old scheduled job cannot override newer manual activation unexpectedly.

---

# 325. THM-ACT-014 — Scheduled Activation Is Generation-Safe

---

# 326. Revision diff

Admin should be able to compare:

```text id="thm-diff-001"
colors
typography
spacing
Hero
components
decoration
responsive
motion
```

---

# 327. THM-REV-007 — Diff Is Derived, Revisions Stay Immutable

---

# 328. Admin UX — theme list

Recommended card:

```text id="thm-ux-list-001"
Theme name
Draft status
Latest revision
Active marker
Accessibility status
Last published
Last activated
AI-assisted marker if useful
```

---

# 329. Editor save status

Clearly distinguish:

```text id="thm-ux-status-001"
Saved Draft
Validated
Published
Active
```

---

# 330. THM-UX-001 — Publication State Must Be Obvious

---

# 331. Preview banner

Unpublished Draft preview should display:

```text id="thm-ux-preview-001"
Предпросмотр темы — изменения не опубликованы
```

---

# 332. Activation confirmation

Should show:

- revision;
- accessibility status;
- current active revision;
- rollback target availability.

---

# 333. THM-UX-002 — Activation Is Deliberate High-Impact Action

---

# 334. Rollback UX

Display revision diff/date rather than only revision number.

---

# 335. AI UX

AI prompt area should communicate:

> AI proposes design settings; it does not alter actress data or publish automatically.

---

# 336. Suggested actions

```text id="thm-ai-ux-001"
Сгенерировать вариант
Сравнить
Применить к черновику
Отклонить
```

---

# 337. No “Generate & Publish” baseline.

---

# 338. THM-AI-011 — AI Generation and Publish Are Separate UX Steps

---

# 339. Anti-patterns

`THM-AP-001`  
Theme stores biography copy.

`THM-AP-002`  
Theme changes actress name.

`THM-AP-003`  
Theme edits Contact URLs.

`THM-AP-004`  
Theme changes `show_on_public_site`.

`THM-AP-005`  
Theme makes private content public.

`THM-AP-006`  
Theme accesses Admin Projection.

`THM-AP-007`  
Theme rewrites SEO professional facts.

`THM-AP-008`  
Theme stores arbitrary JavaScript.

`THM-AP-009`  
Theme allows arbitrary executable HTML.

`THM-AP-010`  
Theme editor is unrestricted CSS textarea.

`THM-AP-011`  
AI returns raw CSS and it is deployed directly.

`THM-AP-012`  
AI returns React and it is executed.

`THM-AP-013`  
Provider output bypasses schema validation.

`THM-AP-014`  
AI theme proposal auto-publishes.

`THM-AP-015`  
AI auto-activates theme.

`THM-AP-016`  
AI ignores locked tokens.

`THM-AP-017`  
Lock value can be silently overwritten by regeneration.

`THM-AP-018`  
AI receives private Casting/Feedback data for style generation.

`THM-AP-019`  
Theme AI changes actress photo appearance.

`THM-AP-020`  
Theme applies beauty filter.

`THM-AP-021`  
Theme crops Full Body destructively.

`THM-AP-022`  
Theme chooses hidden media for Hero.

`THM-AP-023`  
Theme removes mandatory Questionnaire block.

`THM-AP-024`  
Theme changes hyperlink target.

`THM-AP-025`  
Theme hides accessibility focus outline.

`THM-AP-026`  
Low-contrast theme publishes without blocker.

`THM-AP-027`  
AI can waive accessibility failure.

`THM-AP-028`  
Theme relies on animation to reveal essential content.

`THM-AP-029`  
Reduced-motion preference ignored.

`THM-AP-030`  
Mobile preview omitted.

`THM-AP-031`  
Tablet preview omitted.

`THM-AP-032`  
Preview uses Admin-only data.

`THM-AP-033`  
Preview itself activates theme.

`THM-AP-034`  
Save Draft activates theme.

`THM-AP-035`  
Publish automatically overwrites active theme without explicit policy.

`THM-AP-036`  
ThemeRevision modified in place.

`THM-AP-037`  
Rollback deletes newer revision.

`THM-AP-038`  
Temporary theme activates unpublished Draft.

`THM-AP-039`  
Temporary activation has no fallback.

`THM-AP-040`  
Scheduled stale job overwrites newer manual theme activation.

`THM-AP-041`  
Two active themes after race.

`THM-AP-042`  
Theme cache keeps old theme after activation.

`THM-AP-043`  
Old cache rebuild rewrites current pointer.

`THM-AP-044`  
Theme failure takes entire public profile offline.

`THM-AP-045`  
Public renderer reads mutable Draft.

`THM-AP-046`  
Public renderer calls AI at request time.

`THM-AP-047`  
External background URL can be arbitrary remote URL.

`THM-AP-048`  
Imported theme auto-activates.

`THM-AP-049`  
Legacy CSS copied wholesale into new Theme engine.

`THM-AP-050`  
Migration preserves inaccessible/unsafe styling just for visual fidelity.

`THM-AP-051`  
Analytics automatically switches theme.

`THM-AP-052`  
VOP publishes design changes autonomously.

`THM-AP-053`  
Theme changes Search eligibility.

`THM-AP-054`  
Theme changes Builder eligibility.

`THM-AP-055`  
Theme changes Notification permissions.

`THM-AP-056`  
Theme controls PDF professional content.

`THM-AP-057`  
Theme token includes raw provider secret.

`THM-AP-058`  
Preview URL exposes Draft without auth.

`THM-AP-059`  
Activation restore replays expired temporary theme.

`THM-AP-060`  
Theme schema change rewrites historical revisions.

---

# 340. Core invariants

`THM-INV-023`  
Theme is presentation-only domain.

`THM-INV-024`  
Theme never owns professional Source facts.

`THM-INV-025`  
Theme never changes visibility/access/security.

`THM-INV-026`  
Theme cannot alter actress appearance.

`THM-INV-027`  
Theme cannot alter Contact/ProfessionalLink targets.

`THM-INV-028`  
Theme cannot expose hidden MediaAsset.

`THM-INV-029`  
Theme cannot violate Portfolio/Full Body framing rules.

`THM-INV-030`  
Theme configuration is typed/declarative.

`THM-INV-031`  
Arbitrary JS/HTML/React execution is forbidden.

`THM-INV-032`  
Design uses controlled semantic tokens.

`THM-INV-033`  
Theme supports controlled Hero/decorative presentation only.

`THM-INV-034`  
Decoration remains subordinate to professional information.

`THM-INV-035`  
Theme does not redefine information architecture semantics.

`THM-INV-036`  
Desktop/Tablet/Mobile previews are available.

`THM-INV-037`  
Preview respects actual Public Projection visibility.

`THM-INV-038`  
Preview does not publish/activate.

`THM-INV-039`  
Theme AI outputs structured proposals only.

`THM-INV-040`  
Theme AI is optional.

`THM-INV-041`  
Theme AI does not receive unrelated private business data.

`THM-INV-042`  
Theme AI proposals are schema validated.

`THM-INV-043`  
Human Apply is required before AI proposal changes Draft.

`THM-INV-044`  
AI cannot publish or activate Theme.

`THM-INV-045`  
AI regeneration respects locked fields.

`THM-INV-046`  
Locks protect against AI overwrite.

`THM-INV-047`  
Accessibility validation is deterministic.

`THM-INV-048`  
AI cannot waive accessibility blocker.

`THM-INV-049`  
Publish blocks on hard accessibility/security failures.

`THM-INV-050`  
Reduced-motion preferences are respected.

`THM-INV-051`  
Visible focus remains mandatory.

`THM-INV-052`  
ThemeRevision is immutable.

`THM-INV-053`  
Editing published theme creates new Draft/new Revision.

`THM-INV-054`  
Publish and Activate remain separate operations.

`THM-INV-055`  
At most one active ThemeRevision exists per scope.

`THM-INV-056`  
Activation is atomic and idempotent.

`THM-INV-057`  
Activation history is append-only.

`THM-INV-058`  
Temporary Theme uses published Revision only.

`THM-INV-059`  
Temporary activation has explicit fallback.

`THM-INV-060`  
Scheduled activation is generation-safe.

`THM-INV-061`  
Rollback changes active pointer, never historical Revision.

`THM-INV-062`  
Rollback target must be compatible/valid.

`THM-INV-063`  
Theme failure does not destroy public content availability.

`THM-INV-064`  
Public renderer consumes active ThemeRevision only.

`THM-INV-065`  
No runtime AI call is needed for public render.

`THM-INV-066`  
Current theme pointer/cache is generation-safe.

`THM-INV-067`  
Theme cannot change Search truth or SEO professional facts.

`THM-INV-068`  
Questionnaire semantic requirements override styling.

`THM-INV-069`  
Site Theme does not own PDF business semantics.

`THM-INV-070`  
Builder eligibility/security remains Theme-independent.

`THM-INV-071`  
Theme cannot escalate storage/media access.

`THM-INV-072`  
Navigation policy remains domain/route owned.

`THM-INV-073`  
Theme schema is versioned.

`THM-INV-074`  
Schema migration does not rewrite old revisions.

`THM-INV-075`  
Imported Theme begins as Draft.

`THM-INV-076`  
Theme config uses strict allowlist validation.

`THM-INV-077`  
Theme cannot become remote asset/SSRF surface.

`THM-INV-078`  
Publish/Activate/Rollback are audited high-impact actions.

`THM-INV-079`  
VOP cannot publish/activate design autonomously.

`THM-INV-080`  
Analytics cannot auto-optimize/activate Theme.

`THM-INV-081`  
Theme respects system NFR/performance constraints.

`THM-INV-082`  
Draft previews are protected and do not leak private content.

`THM-INV-083`  
Save, Publish and Activate are distinct states/actions.

`THM-INV-084`  
Active theme cannot be removed without safe fallback/replacement.

`THM-INV-085`  
ThemeRevision outlives rebuildable theme artifacts.

`THM-INV-086`  
Restore reconciles active/scheduled generations before serving.

`THM-INV-087`  
Migration does not blindly preserve unsafe legacy CSS.

`THM-INV-088`  
Migration establishes a validated baseline ThemeRevision.

`THM-INV-089`  
Theme workflows remain functional with AI disabled.

`THM-INV-090`  
All activation, accessibility, privacy and AI boundaries are server-enforced.

---

# 341. E2E-THM-001 — Create Draft

Admin creates Theme Draft.

Public site unchanged.

---

# 342. E2E-THM-002 — Manual color edit

Accent token changed in Draft.

Preview changes.

Public site unchanged.

---

# 343. E2E-THM-003 — Content mutation attempt

Client submits actor-name change inside theme payload.

Schema rejects it.

---

# 344. E2E-THM-004 — Visibility mutation attempt

Client sends:

```text id="thm-e2e-vis-001"
show_on_public_site=true
```

inside theme config.

Rejected.

---

# 345. E2E-THM-005 — Arbitrary JavaScript

Theme config containing script is rejected.

---

# 346. E2E-THM-006 — Raw CSS injection

Unsupported arbitrary CSS field rejected.

---

# 347. E2E-THM-007 — Desktop preview

Draft preview renders correctly.

---

# 348. E2E-THM-008 — Tablet preview

Tablet mode renders without overflow.

---

# 349. E2E-THM-009 — Mobile preview

Mobile mode renders functional navigation/CTA.

---

# 350. E2E-THM-010 — Hidden content

Admin-hidden/public-disabled content does not appear in Theme preview.

---

# 351. E2E-THM-011 — AI proposal

Admin enters style prompt.

Structured AI proposal created.

Draft unchanged.

---

# 352. E2E-THM-012 — AI disabled

Manual theme editing/publish/activation remain fully functional.

---

# 353. E2E-THM-013 — AI arbitrary property

Provider returns unsupported token/property.

Schema validation rejects it.

---

# 354. E2E-THM-014 — Lock accent

Accent token locked.

AI regeneration proposes different accent.

Locked value remains unchanged.

---

# 355. E2E-THM-015 — Lock typography

AI applies Hero/color changes but preserves locked typography.

---

# 356. E2E-THM-016 — Human manual override

Admin manually changes an unlocked AI-applied token.

Allowed.

---

# 357. E2E-THM-017 — AI publish attempt

No AI action can produce published ThemeRevision directly.

---

# 358. E2E-THM-018 — AI activate attempt

No activation occurs until Human command.

---

# 359. E2E-THM-019 — Private data context

Theme AI provider request does not contain Feedback/Casting/private Contacts.

---

# 360. E2E-THM-020 — Appearance modification

Theme/AI cannot request/apply actress facial/body retouch configuration.

---

# 361. E2E-THM-021 — Hidden Hero asset

Theme references non-public/non-eligible MediaAsset.

Validation rejects it.

---

# 362. E2E-THM-022 — Full Body crop

Theme tries destructive cover crop for protected Full Body representation.

Rejected/fallback to valid framing.

---

# 363. E2E-THM-023 — Low contrast

Draft introduces invalid body-text contrast.

Validation returns BLOCKER.

---

# 364. E2E-THM-024 — Publish blocked

Theme with accessibility blocker cannot publish.

---

# 365. E2E-THM-025 — Focus visibility

Theme removes focus indicator.

Validation blocks.

---

# 366. E2E-THM-026 — Reduced motion

OS/browser reduced-motion preference suppresses nonessential animation.

---

# 367. E2E-THM-027 — Publish

Valid Draft publishes immutable ThemeRevision R1.

Public site still uses previous active Revision until activation.

---

# 368. E2E-THM-028 — Revision immutable

Direct mutation of R1 rejected/impossible.

---

# 369. E2E-THM-029 — Edit published

Editing R1 creates Draft, not mutation.

---

# 370. E2E-THM-030 — Publish retry

Same idempotency key after lost response returns same ThemeRevision.

---

# 371. E2E-THM-031 — Activate

Human activates R1.

Active pointer changes atomically.

---

# 372. E2E-THM-032 — Concurrent activation

Two Admins activate different revisions concurrently.

Exactly one final active revision.

---

# 373. E2E-THM-033 — Public cache

After activation, public site resolves new Theme revision.

Old cached current alias does not persist incorrectly.

---

# 374. E2E-THM-034 — Rollback

Human rolls back R2 → R1.

R2 remains in revision history.

---

# 375. E2E-THM-035 — Invalid rollback target

Archived/incompatible prohibited revision cannot activate silently.

---

# 376. E2E-THM-036 — Temporary theme

Published R3 scheduled as temporary theme with explicit fallback R2.

---

# 377. E2E-THM-037 — Temporary expiry

At expiry, active pointer returns to configured fallback.

---

# 378. E2E-THM-038 — Stale scheduled activation

Schedule replaced by newer manual activation.

Old scheduled job cannot overwrite current generation.

---

# 379. E2E-THM-039 — Theme renderer failure

Theme artifact fails.

Professional content remains available via safe fallback/last-known-good behavior.

---

# 380. E2E-THM-040 — No runtime AI

Public page render causes no AI provider request.

---

# 381. E2E-THM-041 — Questionnaire semantics

Theme styles questionnaire but cannot hide mandatory Full Body/Close-Up.

---

# 382. E2E-THM-042 — Emotional Grid

Theme cannot recompose/crop finalized Emotional Grid.

---

# 383. E2E-THM-043 — Contact CTA

Theme restyles WhatsApp button.

Target URL remains exact Contact Source.

---

# 384. E2E-THM-044 — Builder

Theme styles Public Builder but cannot expose Builder-disabled item.

---

# 385. E2E-THM-045 — Search

Theme activation changes visuals only.

Public Search content/eligibility unchanged.

---

# 386. E2E-THM-046 — SEO

JSON-LD professional facts remain same before/after theme change.

---

# 387. E2E-THM-047 — Draft preview authorization

Anonymous visitor cannot open protected Draft preview.

---

# 388. E2E-THM-048 — Preview private leak

Draft preview response uses Public Projection only.

---

# 389. E2E-THM-049 — Imported theme

Imported valid configuration creates Draft only.

No activation.

---

# 390. E2E-THM-050 — Unsafe imported CSS

Migration/import rejects or flags unsupported arbitrary rules.

---

# 391. E2E-THM-051 — Baseline migration

Existing production design maps to validated baseline ThemeRevision.

---

# 392. E2E-THM-052 — Legacy violation

Legacy destructive crop/accessibility failure is not preserved merely for pixel parity.

---

# 393. E2E-THM-053 — VOP contrast issue

VOP flags issue.

Does not change or publish Theme.

---

# 394. E2E-THM-054 — Analytics recommendation

Analytics suggests theme improvement.

Active Theme remains unchanged.

---

# 395. E2E-THM-055 — Archive inactive theme

Historical ThemeRevision remains accessible in Admin history.

---

# 396. E2E-THM-056 — Archive active theme

Cannot remove active revision without safe replacement.

---

# 397. E2E-THM-057 — Backup restore

Active Theme pointer reconciles correctly.

Expired temporary activation does not reappear.

---

# 398. E2E-THM-058 — Restore schedule

Superseded scheduled job does not execute after restore.

---

# 399. E2E-THM-059 — Accessibility keyboard

Theme Editor/preview controls and rendered site retain keyboard-visible focus.

---

# 400. E2E-THM-060 — Mobile core flow

Admin can preview, validate, publish and activate Theme from supported mobile Admin UI where product allows.

---

# 401. Architecture diagram

```text id="thm-diagram-001"
              PROFESSIONAL DATA
                    │
                    ▼
            PUBLIC PROJECTIONS
                    │
                    ▼
               ThemeRevision
                    │
             ┌──────┼──────┐
             ▼      ▼      ▼
          Desktop Tablet  Mobile
             │
             ▼
          Public UI
```

---

# 402. AI theme flow

```text id="thm-diagram-002"
Human Prompt
     ↓
Current Theme Draft
     +
Locked Fields
     +
Design Constraints
     ↓
Theme AI
     ↓
Structured Proposal
     ↓
Schema Validation
     ↓
Accessibility Validation
     ↓
Human Preview
     ↓
Apply to Draft
     ↓
Publish
     ↓
Activate
```

---

# 403. Authority diagram

```text id="thm-diagram-003"
PROFILE / MEDIA / CONTACTS / VISIBILITY
               │
               ▼
          CONTENT AUTHORITY

THEME / TOKENS / COMPONENT PRESETS
               │
               ▼
       PRESENTATION AUTHORITY

These boundaries never merge.
```

---

# 404. Revision/activation diagram

```text id="thm-diagram-004"
Draft v12
   ↓
Publish
   ↓
ThemeRevision 4
   ↓
(optional)
Activate
   ↓
Active pointer → Revision 4

Edit:
Revision 4 → new Draft → Revision 5

Rollback:
Active pointer Revision 5 → Revision 4

Neither revision is rewritten.
```

---

# 405. Temporary theme diagram

```text id="thm-diagram-005"
Current R5
   ↓
Schedule temporary R7
fallback = R5
   ↓
Activate R7
   ↓
Temporary period
   ↓
Deactivate
   ↓
Return explicitly to R5
```

---

# 406. Lock diagram

```text id="thm-diagram-006"
Theme Draft
├── Accent Color      [LOCKED]
├── Typography        [LOCKED]
├── Hero Layout       [UNLOCKED]
└── Decoration        [UNLOCKED]

AI Regenerate
   ↓
Locked fields unchanged
Unlocked fields may receive proposal
```

---

# 407. Security diagram

```text id="thm-diagram-007"
AI Provider Output
       ↓
Untrusted
       ↓
Strict Theme Schema
       ↓
Allowed Tokens/Presets
       ↓
Accessibility/Security Validation
       ↓
Human Apply
       ↓
Theme Draft

No raw executable code path.
```

---

# 408. Quality gate

Перед implementation должны быть подтверждены:

- [ ] ThemeDraft model;
- [ ] ThemeRevision model;
- [ ] Active Theme pointer;
- [ ] activation history;
- [ ] typed Theme schema;
- [ ] semantic design tokens;
- [ ] Hero presentation config;
- [ ] decoration config;
- [ ] controlled component variants;
- [ ] no arbitrary executable CSS/JS/HTML authority;
- [ ] manual Theme Editor;
- [ ] field/token locking;
- [ ] AI structured proposal;
- [ ] AI prompt provenance;
- [ ] AI lock preservation;
- [ ] AI human-apply boundary;
- [ ] Desktop preview;
- [ ] Tablet preview;
- [ ] Mobile preview;
- [ ] Public Projection-based preview;
- [ ] responsive validation;
- [ ] contrast validation;
- [ ] focus validation;
- [ ] reduced-motion support;
- [ ] media framing protection;
- [ ] professional-content boundary validation;
- [ ] immutable ThemeRevision;
- [ ] idempotent Publish;
- [ ] separate Activate;
- [ ] atomic active pointer;
- [ ] exactly one active theme;
- [ ] temporary activation;
- [ ] explicit fallback;
- [ ] scheduled activation generation safety;
- [ ] rollback;
- [ ] revision compatibility validation;
- [ ] last-known-good/fallback strategy;
- [ ] cache invalidation/generation safety;
- [ ] no Search/SEO truth mutation;
- [ ] no Questionnaire/Builder permission mutation;
- [ ] no Media access escalation;
- [ ] audit/outbox;
- [ ] Admin-only Draft preview;
- [ ] import as Draft only;
- [ ] migration from legacy styling;
- [ ] restore reconciliation;
- [ ] VOP limits;
- [ ] Analytics advisory-only;
- [ ] deterministic E2E coverage.

---

# 409. Acceptance criteria

`AC-THM-001`  
Theme Domain управляет только presentation layer.

`AC-THM-002`  
Theme cannot mutate Profile, Portfolio, Contacts, Projects, Skills, Languages or other professional Source facts.

`AC-THM-003`  
Theme cannot mutate visibility, access policy or security.

`AC-THM-004`  
Theme cannot alter actress appearance.

`AC-THM-005`  
Theme cannot change professional link/contact targets.

`AC-THM-006`  
Theme cannot access hidden/private media merely for presentation.

`AC-THM-007`  
Theme cannot violate protected media framing rules.

`AC-THM-008`  
Theme configuration uses typed declarative schema.

`AC-THM-009`  
Arbitrary JavaScript/React/executable HTML is not accepted as Theme configuration.

`AC-THM-010`  
Theme uses structured semantic design tokens.

`AC-THM-011`  
Manual editing of colors, typography, spacing, components, Hero, decoration and responsive presentation is supported.

`AC-THM-012`  
Theme Editor provides Desktop, Tablet and Mobile preview.

`AC-THM-013`  
Preview uses Public Projection and cannot leak Admin/private content.

`AC-THM-014`  
Preview never activates/publishes Draft.

`AC-THM-015`  
Theme AI accepts style intent and returns structured allowlisted proposals.

`AC-THM-016`  
Theme AI remains optional; all manual workflows function without AI.

`AC-THM-017`  
AI proposal does not modify Draft until Human Apply.

`AC-THM-018`  
AI cannot publish or activate Theme.

`AC-THM-019`  
Locked fields are preserved during AI regeneration/application.

`AC-THM-020`  
AI provider output is schema-validated server-side.

`AC-THM-021`  
Theme AI context excludes unrelated private professional data.

`AC-THM-022`  
Accessibility/contrast validation is deterministic and cannot be overridden by AI.

`AC-THM-023`  
Hard accessibility/security violations block Publish.

`AC-THM-024`  
Rendered Theme respects reduced-motion preference and visible focus requirements.

`AC-THM-025`  
ThemeRevision is immutable.

`AC-THM-026`  
Editing a published theme produces a new Draft/new Revision.

`AC-THM-027`  
Save Draft, Publish and Activate are distinct actions.

`AC-THM-028`  
Publish is version-aware and idempotent.

`AC-THM-029`  
At most one active ThemeRevision exists per site/profile scope.

`AC-THM-030`  
Activation is atomic, idempotent and generation-safe.

`AC-THM-031`  
Activation history is append-only.

`AC-THM-032`  
Temporary theme activation uses a published revision and explicit fallback.

`AC-THM-033`  
Stale scheduled activation cannot override newer active generation.

`AC-THM-034`  
Rollback changes active pointer but does not delete/rewrite later ThemeRevisions.

`AC-THM-035`  
Rollback target must pass compatibility/validity checks.

`AC-THM-036`  
Theme/render failure cannot make professional content unavailable when safe fallback exists.

`AC-THM-037`  
Public renderer consumes only active immutable ThemeRevision.

`AC-THM-038`  
Public page rendering does not call AI.

`AC-THM-039`  
Theme cache/current pointer handling prevents stale activation races.

`AC-THM-040`  
Theme does not alter Public Search eligibility or professional SEO facts.

`AC-THM-041`  
Questionnaire mandatory semantic rules override Theme presentation.

`AC-THM-042`  
Site Theme does not own PDF business content/semantics.

`AC-THM-043`  
Public Builder eligibility remains independent from Theme.

`AC-THM-044`  
Theme cannot escalate Media/storage access.

`AC-THM-045`  
Theme schema is versioned and historical revisions remain immutable across schema migrations.

`AC-THM-046`  
Imported theme configurations begin as Draft and never auto-activate.

`AC-THM-047`  
Theme does not accept arbitrary remote asset fetch behavior baseline.

`AC-THM-048`  
Publish, Activate and Rollback are audited high-impact actions.

`AC-THM-049`  
VOP can flag technical/design-health issues but cannot publish/activate Themes.

`AC-THM-050`  
Analytics can recommend improvements but cannot switch active Theme.

`AC-THM-051`  
Theme respects performance/NFR budgets.

`AC-THM-052`  
Draft preview is access-controlled.

`AC-THM-053`  
Active Theme cannot be removed without safe replacement/fallback.

`AC-THM-054`  
ThemeRevision outlives derived token/render artifacts.

`AC-THM-055`  
Restore reconciles active pointer, scheduled activation and temporary fallback state before public rendering resumes.

`AC-THM-056`  
Legacy arbitrary CSS is mapped/reviewed rather than blindly imported.

`AC-THM-057`  
Migration establishes a validated baseline ThemeRevision.

`AC-THM-058`  
Legacy accessibility/security violations are not preserved merely for visual fidelity.

`AC-THM-059`  
All Theme AI, accessibility, activation, rollback, privacy and concurrency invariants have deterministic E2E coverage.

---

# 410. Финальная доктрина

> **Site Themes Module является изолированным presentation domain. ThemeDraft хранит только типизированные визуальные настройки — design tokens, component presets, Hero/decor, responsive и motion parameters — и не владеет ни одним профессиональным фактом. Theme AI принимает лишь presentation intent и разрешённый structured context, возвращает schema-validated proposal, уважает locked fields и никогда не имеет права изменять актрису, content, visibility, URLs, permissions или publishing state. Любой AI результат сначала проверяется, просматривается человеком и только затем может быть применён к Draft. Theme публикация создаёт immutable ThemeRevision; Publish и Activate разделены, active pointer меняется атомарно, а rollback просто активирует предыдущую совместимую Revision, не переписывая историю. Temporary themes используют только опубликованные revisions и имеют explicit fallback. Accessibility, contrast, responsive behavior, focus visibility, reduced motion, media framing и security проверяются детерминированно и не могут быть отменены AI. Public renderer использует только active immutable ThemeRevision и никогда не вызывает AI во время рендеринга.**