# CODEX INTERFACE SYSTEM INTEGRATION
## DOC-224 — normative bootstrap for applying GPT-Skill-Enterfaces to the actor-site application repository

**Documentation status:** COMPLETE  
**Implementation status:** NOT_IMPLEMENTED / NOT_VERIFIED  
**Universal source:** `CerebrumYK/GPT-Skill-Enterfaces@598677eb16196398f0b563ed039bff341a5cf197`

---

## 1. Objective

When implementation begins in the actual actor-site application repository, Codex must integrate the universal interface build/review system without moving project-specific business rules into the generic skill repository.

This file is the project-side normative bridge. It does not mean the integration already exists.

---

## 2. Mandatory external read order

Read from the pinned revision:

```text
docs/CODEX_MASTER.md
docs/CODEX_PROJECT_INTEGRATION_SPEC.md
docs/INTERFACE_AUTOMATION_LIFECYCLE.md
docs/CHECKLIST_DESIGN_INTEGRATION.md
docs/NO_SIGNUPS_PRINCIPLES.md
docs/OPEN_DESIGN_INTEGRATION.md
docs/AWESOME_DESIGN_MD_INTEGRATION.md
docs/TYPEUI_INTEGRATION.md
docs/EXTERNAL_REFERENCES.md
docs/OPERATIONS_AND_MAINTENANCE.md

templates/CODEX_TASK.md
templates/PROJECT_PROFILE.md
templates/DESIGN.md
templates/AGENTS_INTERFACE_SECTION.md
templates/interface-skills-version.json
templates/INTERFACE_QUALITY_REPORT.md

skills/manifest.json
```

Also read complete source directories for the chosen installation mode.

---

## 3. Actor project read order before UI changes

After universal docs, inspect actual application evidence:

1. all applicable `AGENTS.md`;
2. this project documentation repository / imported canonical requirements;
3. actual architecture/README;
4. existing design system / `DESIGN.md` if any;
5. tokens/styles/components/typography/icons;
6. localization;
7. security/auth/privacy boundaries;
8. tests/CI/build commands;
9. existing branch/PR for the same feature;
10. representative existing screens/components.

Verify actual application repository reality first, then reconcile it to DOC-208. Next.js/TypeScript and Playwright are canonical target technologies; CSS Modules/design tokens are the canonical styling baseline. Do not add Tailwind or another parallel UI/test stack merely because the legacy repository differs.

---

## 4. Installation mode

Target baseline for the actor site is **full-build** unless a scoped task explicitly needs review-only/domain-only mode.

Required skills:

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

Copy complete directories. Generic copies are immutable vendor material from the pinned source.

---

## 5. Pin contract

Target application must create/adapt:

```text
.interface-skills-version.json
```

with:

```json
{
  "repository": "CerebrumYK/GPT-Skill-Enterfaces",
  "ref": "598677eb16196398f0b563ed039bff341a5cf197",
  "destination": ".agents/skills",
  "skills": [
    "interface-build",
    "better-interface",
    "interface-review",
    "better-accessibility",
    "better-layout",
    "better-writing",
    "better-typography",
    "better-colors",
    "better-ui"
  ]
}
```

No moving branch fallback.

---

## 6. Required project-owned interface files

Create or map:

```text
DESIGN.md
docs/interface-project-profile.md
```

and add project-owned interface lifecycle instructions to `AGENTS.md`.

If an authoritative equivalent already exists, map it rather than creating a competing system.

`DESIGN.md` must be derived from actual application evidence and project requirements, not invented from generic templates.

---

## 7. Required automation tooling

Use the project's native toolchain to implement:

```text
interface skill sync
interface skill verification/drift detection
interface change detection
interface quality orchestration
```

A JavaScript/TypeScript project may use `.mjs` scripts, but this is not mandatory if the actual stack differs.

Verification must distinguish structural validity from pinned-source byte equality.

---

## 8. Automatic routing

Project `AGENTS.md` must state:

- explicit UI/UX/interface intent activates `interface-build`;
- material rendered-surface diff can also activate the lifecycle;
- explicit user intent beats path heuristics;
- ordinary users do not enumerate domain skills;
- tiny isolated UI changes may use compressed workflow.

---

## 9. Actor-site interface-specific constraints

The interface workflow must honor, among others:

- public vs Admin vs Builder visibility separation;
- hidden/private data absent before serialization;
- media originals immutable;
- no appearance-changing photo processing;
- contextual Emotional Grid crop semantics only where explicitly allowed;
- mandatory Main Portfolio Close-Up/Full Body semantics for questionnaires;
- no AI-generated professional facts;
- no auto-publish/send/BOOKED/stage changes;
- Public Builder no-registration baseline with server-filtered eligible data;
- Admin authentication/authorization required;
- token-scoped casting links do not become public;
- PDF hyperlinks/QR exact target semantics;
- Social publish requires explicit Human approval/schedule intent.

A design review may improve presentation but cannot redefine these domain rules.

---

## 10. Design Preflight

For substantial tasks resolve:

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

External references add completeness prompts only; they do not create features.

---

## 11. Runtime and accessibility proof

Substantial UI work must verify applicable rendered states, not just code:

- desktop;
- narrow/reflow;
- keyboard path and visible focus;
- overlays/focus restore;
- loading/empty/error/success/read-only/permission states;
- long RU/EN content where supported;
- media fit/crop behavior;
- 200% zoom/reflow where practical;
- deterministic accessibility checks.

Unavailable evidence is `Not verified`.

---

## 12. Review/repair loop

After implementation:

```text
structured critique
→ repair must-fix
→ better-interface full
→ repair confirmed HIGH/MEDIUM
→ rerun invalidated runtime checks
→ interface-review final diff/branch/PR
→ repair Introduced/Regression HIGH/MEDIUM
→ target deterministic gates
→ quality report
```

`Pre-existing` findings remain separate unless they block the requested task.

---

## 13. CI

CI may deterministically verify:

- skill pin/integrity/drift;
- formatting/lint/typecheck;
- tests/build;
- E2E/accessibility;
- project-specific design-system checks.

Do not label ordinary deterministic CI as semantic `better-interface` AI review unless an actual supported agent execution route is configured.

---

## 14. Safety boundaries

Automation may edit code/tests/docs, run safe checks, repair its own findings and prepare a draft PR when requested.

It must not:

- auto-merge merely because review passed;
- weaken security/privacy/accessibility/tests;
- silently change pin SHA;
- overwrite unrelated work;
- introduce unsupported business behavior;
- perform unrelated publication/destructive actions.

---

## 15. Definition of Done for integration

Integration is complete only when actual application evidence proves:

- all required skills installed from pinned SHA;
- sync + drift verification works;
- `AGENTS.md` automatic activation works;
- `DESIGN.md`/equivalent is grounded in repository evidence;
- interface profile is populated from verified facts;
- a representative UI request routes without manual skill enumeration;
- runtime/accessibility verification participates in the lifecycle;
- `better-interface` and `interface-review` participate in completion;
- target deterministic gates remain intact;
- update/rollback procedure is documented.

Until then status remains `NOT_IMPLEMENTED / NOT_VERIFIED`.

---

## 16. Acceptance criteria

`AC-INT224-001` Integration uses immutable SHA, never moving `main`.  
`AC-INT224-002` Full-build installs complete nine-skill package.  
`AC-INT224-003` Project-specific rules stay outside generic skill bodies.  
`AC-INT224-004` `DESIGN.md` is derived from real target evidence.  
`AC-INT224-005` Project profile contains verified facts only.  
`AC-INT224-006` Explicit UI intent activates `interface-build`.  
`AC-INT224-007` Sync/drift checks fail closed on mismatch/partial state.  
`AC-INT224-008` Runtime-dependent claims require runtime evidence.  
`AC-INT224-009` Holistic and change-scoped review are both represented.  
`AC-INT224-010` External references remain advisory/offline-safe.  
`AC-INT224-011` Actor-site domain/security invariants outrank generic design advice.  
`AC-INT224-012` Integration status cannot become complete from documentation alone.

---

## 17. Финальная доктрина

> **Codex should receive one pinned, repeatable interface workflow, while the actor-site repository remains the owner of its product truth and design evidence. The universal system orchestrates how UI is built and reviewed; it does not certify implementation merely because integration instructions exist.**
