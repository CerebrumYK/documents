# Сайт актрисы — Documentation
## DOC-002 — canonical documentation entry point

**Documentation repository:** `CerebrumYK/documents`  
**Important:** this repository is a specification source, **not proof that the application is implemented**.

---

## 1. Read first

For Codex / implementation work:

1. [`governance.md`](./governance.md)
2. [`implementation/implementation-status.md`](./implementation/implementation-status.md) — **do not confuse specification with implementation**
3. [`final/final-technical-specification.md`](./final/final-technical-specification.md)
4. [`final/source-package-and-implementation-audit.md`](./final/source-package-and-implementation-audit.md)
5. [`github/codex-execution-plan.md`](./github/codex-execution-plan.md)
6. [`implementation/implementation-sequencing.md`](./implementation/implementation-sequencing.md)
7. relevant owning Product/Domain/Architecture/Module docs
8. relevant Security/NFR/Testing/Migration docs
9. `requirements/*`

For UI/UX work additionally read:

1. [`nfr/interface-design-and-quality-system.md`](./nfr/interface-design-and-quality-system.md)
2. [`github/interface-system-integration.md`](./github/interface-system-integration.md)
3. pinned `CerebrumYK/GPT-Skill-Enterfaces@598677eb16196398f0b563ed039bff341a5cf197` starting at `docs/CODEX_MASTER.md`.

---

## 2. Implementation-status doctrine

```text
DOCUMENTED
≠ IMPLEMENTED
≠ VERIFIED
```

The supplied `kate-site.zip` contains the source specification package for DOC-001 and DOC-010…134. Per current project instruction, those requirements are treated as:

```text
NOT_IMPLEMENTED_AS_SPECIFIED
```

until actual application code/runtime/test evidence proves a narrower status.

A `✅ Completed` label inside those files means the **document** was completed.

See DOC-207.

---

## 3. Source package

Manifest:

[`source-materials/kate-site-package-manifest.md`](./source-materials/kate-site-package-manifest.md)

Recorded source:

```text
kate-site.zip
SHA-256 5ad6757df46b0fdc7608f068b78b4ef442e9d04b8f56e61adf11b052aeee4ec0
90 archive entries
```

The source gap and canonical-import gap for DOC-001…134 are now resolved: all canonical files are physically present at governed paths. This still does not prove application implementation.

---

## 4. Canonical architecture doctrine

```text
Professional Source
    ↓
Configuration / Human intent
    ↓
Revision / Snapshot
    ↓
Derived Artifact / Projection
    ↓
Public / Admin / Builder / Provider surface
```

Global principles include:

- one owner per professional fact;
- PostgreSQL authoritative structured store;
- immutable media originals;
- `Save != Publish`;
- `Generate != Apply`;
- `Apply != Publish/Send`;
- historical Revision/Snapshot immutable;
- hidden data absent before serialization;
- AI/VOP/Analytics have no silent professional authority;
- external provider work occurs after commit through durable jobs/outbox;
- retries are idempotent and ambiguous external outcomes reconcile;
- migrations never invent missing professional facts.

---

## 5. Universal interface system

Canonical generic source:

```text
CerebrumYK/GPT-Skill-Enterfaces
598677eb16196398f0b563ed039bff341a5cf197
```

Required future full-build set:

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

The actor-site application must ultimately maintain project-owned `AGENTS.md`, `DESIGN.md`, `docs/interface-project-profile.md`, immutable skill pinning, sync/drift verification, automatic UI activation and runtime/review evidence.

**These are documented requirements and are not currently claimed implemented.**

---

## 6. External design references

Checklist Design, NoSignups, Open Design, Awesome DESIGN.md and TypeUI are advisory inputs through the pinned universal interface repository.

They do not override product/security rules and must not become mandatory runtime/build/ordinary-CI network dependencies.

---

## 7. Current physical documentation package

Present in this repository:

```text
DOC-002/003 Governance/navigation
DOC-140…146 Security
DOC-150…156 NFR + Interface quality
DOC-160…165 Migrations
DOC-170…179 Testing
DOC-180…182 Requirements
DOC-190…196 Operations
DOC-200…207 Implementation/status
DOC-220…224 GitHub/Codex/interface integration
DOC-230…235 Finalization/current audit
historical source archive
source-package manifest
```

DOC-001 and DOC-010…134 are now **canonically imported and physically present**. Their implementation baseline remains `NOT_IMPLEMENTED_AS_SPECIFIED` until application evidence proves otherwise.

---

## 8. Historical materials

Earlier July source/audit/Codex materials remain under:

[`archive/source-materials/`](./archive/source-materials/)

They are provenance only and do not override current canonical doctrine.

---

## 9. Recommended first implementation action

Do not ask Codex to claim the whole system is done from these documents.

First perform Phase 0 against the actual actor-site application repository:

```text
inventory code/routes/DB/media/auth/providers/jobs/tests/CI/deployment/UI system
→ map each requirement to ABSENT/PARTIAL/IMPLEMENTED_UNVERIFIED/VERIFIED/LEGACY_CONFLICT
→ establish project DESIGN.md/profile and pinned interface integration status
→ create dependency-aware implementation issues/PRs
```

---

## 10. Final doctrine

> **This repository defines what the actor-site platform must become. It does not certify what the application already is. Every implementation claim must come from application evidence; every UI workflow must preserve actor-domain/security truth while using the pinned universal interface system as the repeatable build/review process.**
