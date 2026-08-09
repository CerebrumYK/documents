# ISSUE & PULL REQUEST TEMPLATES
## DOC-221 — canonical planning/review templates for GitHub and Codex work

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет reusable Markdown templates для implementation tasks, bugs, migrations, security-sensitive changes и Pull Requests.

> **A template should force the author to state outcome, requirement ownership, risks, migrations and tests before code review—not add ceremony after the work is done.**

---

# 2. Feature / Implementation Issue Template

```md
# Goal
<What concrete product outcome is required?>

## Requirements
- DOC: <DOC-xxx path>
- FR: <FR-* IDs>
- AC: <AC-* IDs>

## Existing implementation to inspect first
- <known paths, or “resolve in repository before editing”>

## Scope
### In scope
- ...

### Out of scope
- ...

## Domain / authority boundaries
- Source owner: ...
- Human-only actions: ...
- Derived/operational data: ...

## Security / privacy
- Public/Admin/Builder/token surfaces affected: ...
- PII/secrets/provider implications: ...

## Database / migration
- Schema change: yes/no
- Migration/backfill required: ...
- Legacy ambiguity rules: ...

## Required tests
- Unit: ...
- Integration: ...
- E2E/security: ...
- Accessibility/performance/visual: ...

## Definition of Done
- [ ] Functional AC pass
- [ ] Negative authority cases pass
- [ ] Migration tested if applicable
- [ ] Docs/traceability updated
- [ ] Observability added
- [ ] No new blocking security/accessibility issue
```

# 3. Bug Template

```md
# Observed behavior
...

# Expected behavior
...

## Requirement reference
- DOC/FR/AC: ...

## Reproduction
1. ...
2. ...

## Environment
- release/commit:
- browser/runtime:
- DB/schema version if relevant:

## Severity / impact
- P1/P2/P3/P4:
- Professional workflow impact:
- Security/privacy/data-loss risk:

## Evidence
<screenshots/correlation IDs; never paste secrets/private payload unnecessarily>

## Suspected scope
...

## Regression test required
...
```

# 4. Migration Issue Template

```md
# Migration goal
...

## Source
- schema/version/commit:
- data inventory reference:

## Target
- migration ID/version:
- target DOC/FR:

## Mapping rules
- exact mappings:
- unknown/ambiguous handling:
- visibility handling:
- historical/provenance handling:

## Dry run
- expected counts:
- review queue:

## Media/files
- checksum strategy:
- missing/corrupt behavior:

## Side-effect safety
- notifications/social/jobs/AI handling:

## Rollback
- backup ID/type:
- stop/rollback triggers:

## Validation
- counts
- constraints
- access negative tests
- E2E
```

# 5. Security Change Template

```md
# Security goal / threat
...

## Trust boundary
...

## Assets at risk
- PII / secret / Source / token / provider / availability

## Attack paths considered
- ...

## Control design
- authentication:
- authorization:
- validation:
- rate limiting:
- audit/telemetry:

## Negative tests
- ...

## Rollback / containment
- ...

## Secret handling
<state names/locations only, never values>
```

# 6. Provider Integration Template

```md
# Provider capability
<AI / Email / WhatsApp / Instagram / TikTok / ...>

## Domain intent
...

## Credential/scopes
- required scopes:
- storage/rotation:

## Request contract
...

## Status/error mapping
...

## Retry/idempotency
...

## UNKNOWN_OUTCOME reconciliation
...

## Webhooks
- signature validation:
- dedupe:
- out-of-order handling:

## Provider failure behavior
<what authoritative core still works?>

## Tests
- fake adapter
- sandbox smoke if appropriate
```

# 7. Documentation Change Template

```md
# Documentation change
...

## Canonical docs affected
- ...

## Requirement semantic change?
- yes/no
- old behavior:
- new behavior:

## Implementation/migration implications
...

## Traceability updates
- FR/AC/tests:

## Conflicts resolved
...
```

# 8. Pull Request Template

```md
# Summary
...

## Requirements implemented / changed
- DOC:
- FR:
- AC:
- Issue:

## What changed
- ...

## What intentionally did not change
- ...

## Domain / authority review
- Source owner:
- Human-only boundaries preserved:
- Snapshot/history impact:

## Security & privacy
- auth/authz:
- public/private projection:
- PII/secrets:
- upload/URL/provider implications:

## Database / migration
- [ ] No schema/data migration
- [ ] Migration included: ...
- Backfill/rollback notes:

## External side effects
- notifications:
- AI:
- social/provider:
- idempotency/reconciliation:

## Tests
- [ ] Unit
- [ ] Integration
- [ ] E2E
- [ ] Security
- [ ] Accessibility
- [ ] Performance
- [ ] Visual
- [ ] Migration
- Relevant IDs/results: ...

## UI evidence
<screenshots/visual diff if relevant; no secrets/private production data>

## Operations/deployment
- config changes:
- worker/job changes:
- monitoring/runbook:
- rollback:

## Documentation/traceability
- [ ] Docs updated
- [ ] FR/AC traceability updated

## Known gaps / follow-up
...
```

# 9. Codex Task Template

```md
# Codex Task

## Goal
<one concrete implementation outcome>

## Mandatory source documents
Read before changes:
- <DOC paths>

## Requirements
- FR: ...
- AC: ...

## First action
Inspect the current repository implementation and report the exact files/schema/routes that already implement or conflict with this scope. Do not assume paths from documentation if the code differs.

## Constraints
- Preserve current working behavior outside scope.
- Do not invent professional facts/defaults.
- Do not broaden public/Builder/token access.
- Do not add provider/AI authority beyond documented contracts.
- Do not perform external provider calls inside DB transactions.
- Preserve immutable revisions/original media.

## Implementation scope
1. ...
2. ...

## Database / migration
...

## Tests required
...

## Completion report
Return:
- files changed
- migrations
- tests/results
- unresolved gaps
- requirement IDs satisfied
```

# 10. Review Checklist Template

```md
- [ ] Canonical requirement understood
- [ ] Correct domain owns write path
- [ ] No duplicate Source fact introduced
- [ ] Server-side authz/visibility correct
- [ ] Hidden fields absent before serialization
- [ ] State machine/version/idempotency correct
- [ ] Immutable history preserved
- [ ] External provider side effects post-commit/reconcilable
- [ ] Secrets/PII not leaked
- [ ] Migration preserves unknown/ambiguous values honestly
- [ ] Accessibility considered
- [ ] Negative tests present
- [ ] Docs/traceability updated
```

# 11. Template maintenance

Templates SHOULD be implemented under `.github/ISSUE_TEMPLATE`, `.github/PULL_REQUEST_TEMPLATE.md` or chosen GitHub configuration when implementation repo adopts them.

This document remains canonical prose; actual template files should be generated/maintained consistently rather than diverging.

# 12. Minimalism rule

Not every tiny chore needs every field. For substantive implementation/security/migration/provider work, sections are mandatory enough to prevent missing risk. Do not delete sections solely because answer is “N/A”; state why N/A where it matters.

# 13. Anti-patterns

Forbidden:

1. “Implement feature X” issue with no FR/AC.
2. PR says “works” with no tests.
3. Migration PR omits ambiguity/rollback.
4. Provider PR omits idempotency/webhook semantics.
5. Security issue contains live token.
6. Screenshot substitutes negative authority tests.
7. Codex task tells agent to guess repository paths and overwrite broadly.
8. Template becomes 100-line bureaucracy for typo-only PR.

# 14. Acceptance criteria

`AC-TPL-001` Feature issues identify goal, DOC/FR/AC, scope, migration, security and tests.  
`AC-TPL-002` Bug reports identify expected requirement and reproduction.  
`AC-TPL-003` Migration template requires dry-run/ambiguity/rollback/side-effect safety.  
`AC-TPL-004` Security template captures trust boundary/negative tests without secrets.  
`AC-TPL-005` Provider template requires scopes, status mapping, retry and reconciliation.  
`AC-TPL-006` PR template covers authority, security, migration, tests, operations and docs.  
`AC-TPL-007` Codex task template requires repository inspection before edits.  
`AC-TPL-008` Templates remain proportional to change risk.  
`AC-TPL-009` Actual `.github` templates, when added, remain consistent with this document.  
`AC-TPL-010` No template encourages disclosure of production secrets/private data.

---

## 15. Финальная доктрина

> **GitHub templates turn the architecture into the questions every implementation change must answer: what requirement is being satisfied, who owns the fact, what authority is allowed, how data migrates, which side effects can occur and what tests prove the result.**
