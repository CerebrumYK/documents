# DOCUMENTATION GOVERNANCE
## DOC-003 — source-of-truth, precedence, change control, supersession and requirement governance

**Статус:** Canonical Governance / Highest Process Authority

---

## 1. Назначение

Документ определяет, как использовать многочисленные проектные документы без конфликтов и как изменять specification после начала implementation.

> **Documentation is resolved by ownership and specificity, not by file order or document number. A consolidated/final document summarizes the system; the owning domain/module document defines its detailed semantics; Security/Privacy invariants constrain every domain and cannot be weakened by lower-level convenience.**

---

# 2. Core governance rules

1. Every professional fact has one owning domain.
2. Every requirement has one primary owning document/family.
3. More specific canonical owner beats a generic summary for detailed behavior.
4. Security/privacy/access constraints apply across all domains.
5. Historical documents are not silently rewritten to hide prior decisions.
6. Requirement IDs remain stable once referenced.
7. Implementation evidence never overrides product doctrine silently; conflict must be resolved/documented.
8. Documentation does not prove implementation.

---

# 3. Precedence hierarchy

When two documents appear to conflict, use this resolution order.

## Level 0 — Explicit governance / approved decision

```text
DOC-003 Documentation Governance
explicit approved supersession/decision record
```

Governance decides **how** to resolve conflict; it does not itself redefine every domain fact.

## Level 1 — Security/privacy hard constraints

For security/privacy/access behavior:

```text
DOC-140…146
```

A module requirement cannot weaken authentication, authorization, privacy, secrets, upload/SSRF or token-access constraints merely for convenience.

Example:

```text
Module says “show link”
Security says URL is Admin/private/storage target
→ Security constraint blocks public exposure.
```

## Level 2 — Owning Domain / Module

For business/professional semantics, the owning module/domain document is authoritative.

Examples:

```text
Skills/Languages → DOC-117
Questionnaires → DOC-120
Feedback → DOC-124
Opportunity → DOC-126
QR → DOC-130
Social → DOC-133
Achievements → DOC-134
```

These detailed documents are now canonically imported and override generic summaries where wording differs in specificity but not security.

## Level 3 — Domain / Architecture / Database / API contracts

These define cross-module mechanics:

```text
DOC-060…064 Domain
DOC-070…080 Architecture
DOC-090…094 Database
DOC-100…103 API
```

Owning module semantics and cross-cutting architecture must be reconciled together; neither may violate explicit Security hard constraints.

## Level 4 — Master requirements and acceptance

```text
DOC-180 Functional Requirements
DOC-181 Master Acceptance Criteria
DOC-182 Traceability
```

These are indexes/contracts that consolidate owning docs. If a master line conflicts with a more detailed owning module because the master summary is stale, fix the master line rather than changing domain semantics silently.

## Level 5 — Final consolidated specification

```text
DOC-233 Final Technical Specification
```

DOC-233 is the best high-level consolidated reading source, but it is not intended to override finer owning-domain details.

## Level 6 — Implementation/Operations/GitHub process

```text
DOC-190…223
```

These specify how to build/run/release the defined product. They cannot redefine professional facts for implementation convenience.

## Level 7 — Audit/Gap/Index

```text
DOC-230…234
```

These describe repository/status/navigation. They do not invent product behavior.

---

# 4. No “last document wins” rule

A higher DOC number, later creation date or later Git commit does **not** automatically override an older owning document.

Example:

```text
DOC-233 says “Skills are confirmed” generically
DOC-117 says level is integer 1..5, Human-controlled, evidence never auto-calculates
→ DOC-117 detail remains authoritative.
```

---

# 5. Hard invariants

The following are global and may not be weakened without explicit architecture/product decision and security review:

```text
one owner per professional fact
PostgreSQL authoritative structured Source
immutable media originals
hidden data absent before serialization
Save != Publish
Generate != Apply
Apply != Publish/Send
historical Revision/Snapshot immutable
AI does not become professional Source automatically
VOP safe actions are allowlisted
Analytics is read-only relative to Source
external provider calls do not occur inside authoritative DB transaction
unknown external outcome reconciles before retry
migration does not invent missing professional facts
```

---

# 6. Requirement IDs

Stable ID families:

```text
FR-*             Functional Requirements
AC-*             Acceptance Criteria
*-INV-*          Invariants
E2E-*            End-to-end cases
*-AP-*           Anti-patterns
```

Once referenced in code/issue/PR, an ID should not be reused for unrelated semantics.

---

# 7. Semantic changes

When a requirement genuinely changes:

1. identify owning document;
2. state old behavior;
3. state new behavior;
4. explain rationale;
5. assess security/privacy;
6. assess DB/migration/history compatibility;
7. update FR/AC;
8. update tests;
9. update traceability/index;
10. create issue/PR evidence.

Do not update only implementation to “make it easier”.

---

# 8. Supersession

A document/requirement may be superseded only explicitly.

Recommended marker:

```text
Status: SUPERSEDED
Superseded by: DOC-xxx / FR-...
Reason: ...
Effective from: ...
```

Keep historical file or redirect/index record so old GitHub references remain understandable.

---

# 9. Corrections vs semantic changes

### Editorial correction

- typo;
- broken link;
- canonical path correction;
- clearer wording preserving semantics.

Can retain same ID.

### Semantic change

- allowed state changes;
- visibility behavior;
- authority;
- mandatory field;
- lifecycle;
- provider side effect;
- migration interpretation.

Requires explicit requirement change process.

---

# 10. Documentation path changes

DOC ID is more stable than filename. If file moves:

- update DOC-234;
- update internal links;
- preserve Git history/redirect note if practical;
- do not create two active canonical copies.

---

# 11. Known canonical path overrides

Earlier documentation must use:

```text
DOC-061 docs/domain/entity-relationships.md
DOC-064 docs/domain/revisions-and-history.md
DOC-074 docs/architecture/media-processing.md
DOC-075 docs/architecture/pdf-generation.md
DOC-077 docs/architecture/cache-strategy.md
DOC-078 docs/architecture/search.md
DOC-079 docs/architecture/ai-architecture.md
DOC-080 docs/architecture/notifications.md
DOC-090 docs/database/database-architecture.md
DOC-091 docs/database/data-dictionary.md
DOC-092 docs/database/visibility-and-access.md
DOC-093 docs/database/validation-and-constraints.md
DOC-094 docs/database/deletion-and-retention.md
DOC-100 docs/api/server-actions.md
DOC-101 docs/api/contracts.md
DOC-102 docs/api/errors.md
DOC-103 docs/api/idempotency.md
```

Stale aliases are corrected after import rather than maintained as competing canonical files.

---

# 12. Repository evidence rule

A project conversation, memory, issue, screenshot or final summary may help locate documentation but does not prove a file exists in Git.

For repository status:

```text
fetch/search actual repository
→ then mark PRESENT
```

This is why DOC-230/234 distinguish earlier authored documents from physically imported files.

---

# 13. Implementation evidence rule

A documented FR is `DOCUMENTED`, not `IMPLEMENTED`.

`IMPLEMENTED_UNVERIFIED` requires actual code evidence.

`VERIFIED` requires:

- implementation evidence;
- relevant migration evidence;
- passing test/acceptance evidence.

---

# 14. Code conflict with documentation

If existing production code conflicts with canonical docs:

1. do not overwrite/drop data immediately;
2. inventory current semantics/data;
3. determine whether code is legacy defect or docs are stale;
4. follow precedence/decision process;
5. create migration/compatibility plan;
6. update docs only through explicit semantic change if necessary.

---

# 15. Ambiguous professional data

Documentation can define allowed target semantics but cannot invent a real actress fact.

If migration encounters:

```text
unknown Skill level
ambiguous CEFR
unclear Achievement status
unclear Training/Project classification
```

result is Human review/unknown, regardless implementation pressure.

---

# 16. AI-generated documentation/code

AI/Codex can draft/update documentation and code, but:

- must cite/reference owning requirements internally where appropriate;
- cannot change business semantics silently;
- cannot infer missing repository files as present;
- cannot mark tests passed without running them;
- cannot invent professional data to satisfy schema.

---

# 17. External/current technical facts

Provider/framework/API behavior changes over time.

When implementation depends on current behavior, use official primary documentation during implementation and record relevant version/assumption.

This can refine **technical adapter implementation** without changing professional product semantics unless an explicit requirement change is needed.

---

# 18. Security conflict rule

If an explicit product feature appears to require a security exception, do not silently create it.

Required decision:

```text
threat/risk
business reason
safer alternatives
scope
mitigation
approval
tests
```

Security-by-default remains until approved.

---

# 19. NFR conflict rule

Performance/visual design cannot override:

- security;
- privacy;
- professional correctness;
- accessibility blockers;
- historical integrity.

Optimize implementation instead.

---

# 20. Governance for optional/future features

A future roadmap capability is not baseline merely because architecture can support it.

Mark:

```text
BASELINE
OPTIONAL_CONFIGURED
FUTURE
DEFERRED
```

Do not implement unsafe placeholder behavior to make FUTURE appear complete.

---

# 21. Documentation review

Substantive docs changes should receive the same review discipline as code:

- correctness;
- conflict scan;
- security implications;
- requirement IDs;
- migration impact;
- link/index updates.

---

# 22. Documentation audit cadence

Run audit:

- after major domain design changes;
- before major implementation phase;
- before production migration/cutover;
- after importing earlier DOC families;
- before claiming final documentation complete.

---

# 23. GitHub/Codex precedence

Codex must read:

1. this governance file;
2. Final Technical Specification;
3. Codex Execution Plan;
4. relevant owning docs;
5. relevant Security/NFR/Migration/Testing docs.

When conflict found, Codex reports it rather than choosing whichever instruction is easier to implement.

---

# 24. Change record recommendation

For major product decisions maintain lightweight ADR/decision issue or PR description including:

```text
decision
alternatives
reason
impacted DOC/FR/AC
migration/compatibility
```

Formal ADR directory may be added when needed; not mandatory for trivial changes.

---

# 25. Anti-patterns

Forbidden:

1. “newer doc number wins”.
2. Final summary silently overrides owning module.
3. Security restriction ignored because UX doc says easier flow.
4. Implementation behavior becomes canonical automatically because it already exists.
5. Requirement ID reused for unrelated behavior.
6. Document deleted with no supersession trace.
7. AI regenerates missing earlier doc approximately and marks it original.
8. Code marked VERIFIED without tests.
9. Migration fills unknown professional data from assumptions.
10. GitHub issue closure treated as product acceptance.
11. Performance goal used to expose private cache/data.
12. Stale path aliases retained as parallel canonical docs indefinitely.

---

# 26. Governance acceptance criteria

`AC-GOV-001` Document precedence is ownership/specificity based, not numeric/date based.  
`AC-GOV-002` Security/privacy hard constraints apply globally.  
`AC-GOV-003` Owning Domain/Module documents define detailed business semantics.  
`AC-GOV-004` Master/final docs consolidate rather than silently override owning detail.  
`AC-GOV-005` Requirement IDs remain stable and semantic changes are explicit.  
`AC-GOV-006` Superseded docs/requirements retain traceable successor/reason.  
`AC-GOV-007` Repository/file/implementation/test status is evidence-based.  
`AC-GOV-008` Ambiguous professional facts remain unknown/review-required.  
`AC-GOV-009` Code/document conflicts trigger review/migration decision rather than silent overwrite.  
`AC-GOV-010` Current provider/framework assumptions are verified from primary documentation during implementation.  
`AC-GOV-011` Documentation indexes/links/traceability update with semantic/path changes.  
`AC-GOV-012` Codex reports unresolved conflicts rather than inventing resolution.

---

## 27. Финальная доктрина

> **The project has many documents but one coherent source-of-truth model: detailed semantics belong to the domain that owns them, security/privacy constrain every surface, master/final documents summarize rather than supersede, and every change remains traceable through stable requirement IDs, Git history, migrations and tests. Neither a newer file, existing code nor AI output can silently rewrite that authority model.**

## Implementation technology authority — DOC-208

DOC-208 `docs/implementation/unified-technology-stack.md` is authoritative for concrete production implementation technology. Generic alternatives/examples in imported Architecture/Database/API documents remain explanatory and MUST NOT be interpreted as permission to create parallel production stacks. A technology substitution requires an explicit ADR/governance decision plus migration/test/rollback plan.

DOC-225 is the canonical one-campaign Enterprise implementation task. It remains subordinate to Security/privacy and owning Domain semantics while wrapping DOC-206 phases into one continuous execution campaign. DOC-237 is the current consistency-audit authority.
