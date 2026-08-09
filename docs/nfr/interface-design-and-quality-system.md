# INTERFACE DESIGN & QUALITY SYSTEM
## DOC-156 — project-specific integration contract for the universal interface build/review system

**Documentation status:** COMPLETE  
**Implementation status:** NOT_IMPLEMENTED / NOT_VERIFIED  
**Canonical external source:** `CerebrumYK/GPT-Skill-Enterfaces`  
**Pinned revision:** `598677eb16196398f0b563ed039bff341a5cf197`

---

## 1. Назначение

Этот документ определяет, как требования к UX/UI проекта сайта актрисы должны реализовываться через универсальную систему `GPT-Skill-Enterfaces`.

Документ является **требованием к будущей реализации**, а не доказательством того, что skills, `DESIGN.md`, project profile, sync/drift tooling или review loop уже внедрены в application repository.

> **Наличие данного документа означает DOCUMENTED. Реализация считается только после появления code/config/tests/runtime evidence в целевом application repository.**

---

## 2. Source of truth

Universal interface source:

```text
repository: CerebrumYK/GPT-Skill-Enterfaces
ref: 598677eb16196398f0b563ed039bff341a5cf197
```

Этот SHA является текущим merged `main` baseline на момент подготовки документа и должен использоваться как immutable integration pin.

Moving refs запрещены:

```text
main
master
HEAD
latest
```

как project integration pin.

---

## 3. Required skill set

Full-build mode проекта должен использовать девять skills:

```text
interface-build
better-interface
interface-review
better-accessibility
better-layout
better-writing
better-typography
better-colors
better-ui
```

Роли:

| Skill | Role |
|---|---|
| `interface-build` | creation/modification orchestrator |
| `better-interface` | holistic review orchestrator |
| `interface-review` | final diff/branch/PR attribution review |
| `better-accessibility` | accessibility domain |
| `better-layout` | layout/responsive hierarchy |
| `better-writing` | UX copy |
| `better-typography` | typography |
| `better-colors` | semantic colors/contrast |
| `better-ui` | components/surfaces/icons/feedback/motion |

---

## 4. Precedence

Для сайта актрисы действует следующая иерархия:

1. Security/privacy/legal и explicit professional product requirements из этого repository;
2. application repository `AGENTS.md` и фактическая architecture;
3. executable components/tokens/styles/tests application repository;
4. project-specific `DESIGN.md`;
5. pinned `GPT-Skill-Enterfaces` skills;
6. project interface profile/automation guidance;
7. external advisory references.

Universal skills не имеют права переписывать бизнес-правила Profile, Questionnaire, Casting, Contacts, privacy, media provenance, Human approval или social publishing.

---

## 5. Required target-repository artifacts

При будущей реализации в application repository должны появиться либо быть явно сопоставлены эквиваленты:

```text
AGENTS.md
DESIGN.md
.interface-skills-version.json

docs/interface-project-profile.md
docs/interface-development.md
docs/interface-quality-automation.md

.agents/skills/
├── interface-build/
├── better-interface/
├── interface-review/
├── better-accessibility/
├── better-layout/
├── better-writing/
├── better-typography/
├── better-colors/
└── better-ui/

<project-native tooling>/
├── interface-skills-sync
├── interface-skills-verify
├── interface-change-detect
└── interface-quality
```

Не требуется использовать именно Node.js: tooling обязан соответствовать реальному stack application repository.

---

## 6. Vendoring / drift contract

`.interface-skills-version.json` должен содержать immutable SHA и полный declared skill set.

Sync обязан:

1. получить ровно pinned revision;
2. копировать полные skill directories, не только `SKILL.md`;
3. сохранять `agents/openai.yaml`, `references/` и future-owned files;
4. валидировать временную копию до replacement;
5. не трогать unrelated `.agents` content;
6. fail closed при partial sync;
7. никогда не fallback на moving branch.

Verify обязан различать:

```text
STRUCTURALLY_VALID
SOURCE_EQUALITY_VERIFIED
```

Структурная локальная проверка без доступа к pinned source не может называться source-equality verification.

---

## 7. Project DESIGN contract

`DESIGN.md` описывает project-specific reusable visual intent, а не создаёт второй token system.

Он должен содержать, где применимо:

- visual/product character;
- semantic color roles;
- typography;
- spacing/grid/layout;
- responsive transformations;
- component language;
- interaction/data states;
- surfaces/borders/radii/elevation;
- icons/media treatment;
- motion;
- UX voice;
- project-specific accessibility expression;
- Do/Avoid rules;
- evidence pointers к executable sources.

Exact values принадлежат code/tokens/components.

---

## 8. Project interface profile

`docs/interface-project-profile.md` заполняется только проверенными фактами application repository.

Минимум:

```text
product boundaries
architecture/framework/rendering model
styling/component/token sources
typography/localization
security/privacy/auth boundaries
destructive-action rules
supported viewports/appearance modes
quality commands
browser/runtime verification matrix
UI-relevant path patterns
public/private flows
priority surfaces
existing migration/PR context
```

Запрещено придумывать отсутствующие product requirements.

---

## 9. Automatic activation

Любой explicit запрос на создание или material изменение rendered UI должен автоматически активировать `interface-build`.

Пользователь не обязан перечислять skills вручную.

Path/diff detection используется как routing aid. Explicit UI/UX intent имеет приоритет над эвристикой путей.

Documentation-only, DB-only и server-only change не должен искусственно запускать UI review, если rendered behavior не меняется.

---

## 10. Required lifecycle

Для substantial UI change:

```text
TASK
↓
CLASSIFY INTERFACE RELEVANCE
↓
VERIFY PINNED SKILLS
↓
READ AGENTS.md + DESIGN.md + PROJECT PROFILE
↓
RECON executable tokens/components/comparable UI
↓
DESIGN PREFLIGHT
↓
VARIATIONS only if structural direction remains ambiguous
↓
DESIGN-DIRECTION LOCK
↓
OPTIONAL PROTOTYPE when it reduces rework
↓
IMPLEMENT with domain skills
↓
LOCAL CLEANUP
↓
RUNTIME / BROWSER VERIFICATION
↓
STRUCTURED CRITIQUE
↓
REPAIR
↓
better-interface full
↓
REPAIR HIGH / MEDIUM
↓
interface-review final change scope
↓
REPAIR Introduced / Regression HIGH / MEDIUM
↓
TARGET DETERMINISTIC GATES
↓
QUALITY REPORT
↓
DRAFT PR / COMPLETE when requested
```

Tiny isolated corrections use compressed path without artificial variants/prototypes.

---

## 11. Design Preflight

For substantial work:

```yaml
surfaces: []
existingPatternsToReuse: []
components: []
flows: []
topics: []
states: []
informationHierarchy: []
primaryAction: null
secondaryActions: []
responsiveStrategy: null
accessibilityModel: null
frictionAndIdentity: null
designDirectionRequired: false
variationExplorationRequired: false
prototypeUseful: false
externalReferencesConsulted: []
```

State list отражает только реально возможные состояния:

- default / hover / focus-visible / active;
- selected / expanded / checked;
- disabled / read-only;
- loading / pending;
- empty / no-results;
- success/populated;
- error/recovery;
- permission/unavailable;
- destructive confirmation;
- narrow/reflow;
- long localized/user-generated content;
- missing optional media/data;
- high-volume stress state where applicable.

---

## 12. Actor-site design constraints

Universal workflow обязан сохранять профессиональные constraints проекта:

1. **Media originals immutable.**
2. Portfolio photos не подвергаются appearance-changing AI/beauty treatment.
3. Обычные portfolio/admin previews сохраняют исходное соотношение сторон и используют non-destructive presentation; contextual crop допускается только там, где owning module прямо его определяет (например Emotional Grid).
4. Hidden/private/contact/admin data отсутствуют до serialization на public surface.
5. Public Questionnaire Builder остаётся public/no-registration baseline, но получает только server-eligible data.
6. `/admin` и private operations требуют authenticated authorization.
7. Publication/send/destructive operations остаются Human-controlled.
8. UI не может превращать `Save` в скрытый `Publish`, `Apply` в `Send`, AI recommendation в domain decision.
9. Interface copy не может создавать неподтверждённые professional claims.
10. Responsive redesign не может ухудшать professional media semantics или accessibility.

---

## 13. Identity/friction classification

Baseline:

| Surface/flow | Identity gate |
|---|---|
| Public profile/portfolio | NONE |
| Public Questionnaire viewing/download where PUBLIC | NONE |
| Public Questionnaire Builder baseline | NONE |
| Public Feedback/Inquiry submission | NONE unless anti-abuse challenge is triggered |
| Token-scoped casting material | REQUIRED token scope, no account signup by default |
| Admin | REQUIRED |
| Social OAuth/account management | REQUIRED |
| Publish/send/destructive admin actions | REQUIRED + explicit confirmation according to domain |

NoSignups principles reduce unnecessary friction but never remove required Security boundaries.

---

## 14. External references

The universal source incorporates these as advisory inputs only:

### Checklist Design

Use for Components → Flows → Topics → applicable states coverage.

Do not create product features because they appear in a checklist.

### NoSignups

Use for identity/data-friction and dark-pattern review.

Classify gates as:

```text
REQUIRED | OPTIONAL | UNNECESSARY | NONE
```

### Open Design

Use for design-production workflow, design contracts, design direction, optional prototypes and critique loops.

No mandatory CLI/MCP/cloud dependency.

### Awesome DESIGN.md

Use as a comparative corpus for structuring project `DESIGN.md`, not as a brand source.

### TypeUI

Use advisory concepts for modular design guidance, structural variations, local cleanup and audit organization.

External references are not application runtime/build/ordinary-CI dependencies.

---

## 15. Runtime evidence

Source review alone is insufficient for runtime-dependent claims.

Where supported, verify:

- primary desktop layout;
- smallest supported/reflow width;
- keyboard-only primary flow;
- visible focus;
- 200% zoom/reflow where practical;
- modal/drawer focus lifecycle;
- changed loading/empty/error/disabled/read-only states;
- RU/EN and long-content behavior where those locales/surfaces exist;
- supported appearance modes;
- deterministic accessibility automation.

Unavailable evidence = `Not verified`.

---

## 16. Review contract

`better-interface full` owns holistic consolidated review.

Confirmed HIGH and MEDIUM findings are repaired unless blocked by a higher-priority explicit constraint.

Then `interface-review` reviews final diff/branch/PR and classifies:

```text
Introduced
Regression
Pre-existing
```

Introduced/Regression HIGH and MEDIUM are repair targets. Pre-existing findings stay separate unless they block requested work.

---

## 17. CI boundary

Deterministic CI may run:

- skill integrity/drift validation;
- format/lint/typecheck/static analysis;
- unit/component tests;
- production build;
- migration/database checks;
- browser/E2E;
- deterministic accessibility checks;
- report artifact generation.

CI must not claim semantic `better-interface` AI review unless a real supported agent/API execution path is deliberately configured.

---

## 18. Anti-generic-AI interface guard

Reject UI that:

- ignores established project components/tokens;
- invents a new visual language without evidence;
- adds arbitrary gradients/blobs/cards/radii/shadows/motion;
- fabricates actor metrics, testimonials, roles, awards or other professional facts;
- uses filler content as real data;
- falls back to unrelated generic dashboard/landing patterns;
- weakens privacy/security/accessibility for visual convenience.

---

## 19. Implementation status

As of this documentation revision:

```text
Universal interface source: VERIFIED at pinned SHA
Project integration requirement: DOCUMENTED
Integration in application repository: NOT_IMPLEMENTED / NOT_VERIFIED
DESIGN.md in application repository: NOT_VERIFIED
Project interface profile: NOT_VERIFIED
Vendored skills: NOT_VERIFIED
Sync/drift tooling: NOT_VERIFIED
Runtime interface lifecycle evidence: NOT_VERIFIED
```

No status may be upgraded solely because a specification file exists.

---

## 20. Acceptance criteria

`AC-UI156-001` Future application integration pins immutable SHA `598677...`.  
`AC-UI156-002` Full-build mode contains all nine declared skills.  
`AC-UI156-003` Generic vendored skills are not hand-edited for project rules.  
`AC-UI156-004` Project owns its `AGENTS.md`, `DESIGN.md`, interface profile, executable tokens/components/tests.  
`AC-UI156-005` Explicit UI intent automatically activates `interface-build`.  
`AC-UI156-006` Sync never falls back to moving refs.  
`AC-UI156-007` Verify distinguishes structural validity from source equality.  
`AC-UI156-008` Substantial work uses Design Preflight and project evidence.  
`AC-UI156-009` Variants/prototypes are used only when they materially help.  
`AC-UI156-010` Runtime-dependent claims require runtime evidence or `Not verified`.  
`AC-UI156-011` `better-interface` and `interface-review` are distinct completion stages.  
`AC-UI156-012` Deterministic CI does not pretend to perform semantic AI review.  
`AC-UI156-013` External design references are advisory, not runtime/build dependencies.  
`AC-UI156-014` Public low-friction rules never weaken Admin/token/privacy boundaries.  
`AC-UI156-015` Actor professional facts/media semantics remain governed by owning DOC modules.  
`AC-UI156-016` Documentation completion never implies implementation completion.

---

## 21. Финальная доктрина

> **The universal interface system is a pinned project-aware development process, not a replacement design system and not evidence that UI work is already implemented. The actor site's product/security/media rules remain authoritative; `GPT-Skill-Enterfaces` supplies the repeatable interface-build, verification and review lifecycle that the application repository must explicitly integrate and prove through code, runtime evidence and tests.**
