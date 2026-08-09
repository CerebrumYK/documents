# GITHUB WORKFLOW
## DOC-220 — repository workflow, issues, pull requests, reviews, CI and documentation traceability

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет GitHub workflow для реализации проекта по документации.

> **GitHub is the change-control and traceability surface: every meaningful implementation change identifies the requirements it implements, carries tests/migrations where necessary, and is merged only after review and blocking quality gates.**

---

## 2. Default branch

Canonical integration branch:

```text
main
```

`main` should remain releasable or at least continuously validated by blocking CI.

## 3. Change flow

Recommended:

```text
Issue / planned task
→ short-lived branch
→ implementation + tests + docs/migration
→ Pull Request
→ CI
→ review
→ merge
→ deployment/release separately
```

Direct production deployment is not implied by merge.

## 4. Branch naming

Examples:

```text
feat/questionnaire-revisions
fix/feedback-notification-durability
refactor/media-storage-boundary
migration/training-normalization
security/token-revocation
ops/backup-restore
```

Include issue number optionally.

## 5. Issue requirement linkage

Implementation issue references:

```text
FR-* / AC-* / DOC-* IDs
```

Issue should state which requirement is being implemented or changed.

## 6. Pull request scope

Prefer one coherent vertical concern. Avoid mixing broad codebase reformat/refactor, database migration and unrelated provider feature in same PR.

## 7. PR description

Required sections:

```text
Summary
Requirements / docs
Behavior changes
Database/migration impact
Security/privacy impact
Testing
Screenshots/visual evidence when relevant
Operations/deployment impact
Known gaps/follow-up
```

## 8. CI checks

Blocking baseline according to change:

- formatting/lint;
- TypeScript/typecheck;
- unit tests;
- integration tests;
- build;
- critical E2E;
- security scans/tests;
- accessibility tests;
- migration validation;
- performance/visual gates where affected.

## 9. Required review focus

Reviewer checks:

1. requirement semantics;
2. domain ownership;
3. data migration;
4. authentication/authorization;
5. privacy/public projection;
6. concurrency/idempotency;
7. external side effects;
8. tests;
9. operations;
10. docs/traceability.

## 10. High-risk PRs

Require stronger review for:

```text
authentication/authorization
public/token visibility
secrets/OAuth
uploads/SSRF
DB migrations
retention/deletion
Questionnaire publication
Social publishing
provider reconciliation
```

## 11. Draft PRs

Use Draft PR for active work when early CI/collaboration useful. Mark Ready only when scope/description/tests sufficient for review.

## 12. Merge strategy

Repository may use squash/rebase/merge according to project preference. Preferred baseline for feature PRs is **squash merge** when it produces one meaningful change record and preserves issue/PR discussion.

Do not force squash when individual commits have migration/release significance that should remain.

## 13. Commit messages

Use concise semantic messages:

```text
feat: add immutable questionnaire revisions
fix: preserve feedback on provider failure
security: block private-network link checks
migration: normalize training records
```

No requirement for ceremonial conventional-commit purity if repository standard differs; messages must remain meaningful.

## 14. Branch protection

Recommended for `main`:

- PR required;
- blocking CI required;
- conversation/review resolved;
- no force push;
- no branch deletion by accidental push;
- signed commits optional according to operator policy.

If sole-maintainer workflow temporarily permits direct writes, release-quality gates still apply; this documentation generation bootstrap is not a precedent for implementation bypassing review.

## 15. CODEOWNERS/reviewers

Optional. High-risk areas can request security/domain review explicitly even in small team.

## 16. Labels

Suggested:

```text
area:profile
area:media
area:questionnaire
area:casting
area:security
area:ops
area:docs
kind:feature
kind:bug
kind:migration
kind:security
priority:P1/P2/P3/P4
status:blocked
```

Use only if it adds navigation value; avoid huge label bureaucracy.

## 17. Milestones

May map implementation phases/releases from DOC-206. Milestone is planning aid, not proof requirement verified.

## 18. Issues

Issue contains concrete outcome and acceptance criteria. Avoid “implement whole website” issue; decompose by dependency-aware vertical slices.

## 19. Bug issues

Include:

- observed behavior;
- expected behavior/FR/AC;
- reproduction;
- environment/release;
- severity;
- privacy/security indication;
- evidence without secrets.

## 20. Security issues

Do not publish exploit details/secrets in public issue if disclosure risk exists. Use private/security handling appropriate to repository visibility and incident process.

## 21. Documentation PRs

Docs are production artifacts. Broken path/contradictory requirement changes require review like code because Codex will consume them as implementation authority.

## 22. Requirement changes

PR changing canonical requirement must explicitly identify:

- prior semantics;
- new semantics;
- migration/backward compatibility;
- tests affected;
- traceability update.

## 23. Generated files

Do not commit runtime generated media/cache/PDF/secret artifacts. Generated code/config schema outputs may be committed only if repository build requires it and source generator is canonical.

## 24. Migration PRs

Include:

```text
dry-run/report
rollback/backup expectation
idempotency
legacy ambiguity handling
migration tests
```

## 25. Provider PRs

Include fake/sandbox contract tests and document scopes/secrets/webhook/idempotency. Never include production token in issue/PR logs.

## 26. UI PRs

Include relevant screenshots/visual diffs and keyboard/accessibility testing evidence. Screenshot does not replace functionality tests.

## 27. CI artifacts

May retain test reports/screenshots/performance output with bounded retention. Ensure they do not contain private production data/secrets.

## 28. Review threads

Resolve only after code/docs actually address concern or reviewer explicitly accepts rationale. Do not mass-resolve without inspection.

## 29. Merge conflicts

When conflicts touch migrations/requirements, re-run relevant tests and reconsider semantic ordering; do not choose “ours/theirs” blindly.

## 30. Releases

Git tag/release should identify exact commit, migration notes, operator/deployment notes and known issues. GitHub release does not itself run production restore/deploy unless automation explicitly does so.

## 31. Hotfixes

Branch from production/current release, make minimal fix, targeted tests/security, merge/cherry-pick according to release strategy, then reconcile main so fix is not lost.

## 32. Dependabot/automated dependency PRs

Still require build/tests and review of breaking/security impact. Do not auto-merge major provider/framework dependency change solely because bot opened PR.

## 33. Codex usage

Codex tasks should operate through issue/branch/PR-sized scopes and reference DOC/FR/AC. Codex must inspect existing code before changes and report what it could not verify.

## 34. Anti-patterns

Forbidden:

1. One PR “implement everything”.
2. Merge red blocking CI.
3. Requirement change without docs/tests.
4. Migration hidden inside unrelated UI PR without notes.
5. Production secret pasted into issue.
6. Review based only on screenshots.
7. Closed issue interpreted as verified requirement automatically.
8. Direct `main` force-push.
9. Provider dependency major update auto-merged blindly.
10. Hotfix never reconciled to main.

## 35. Acceptance criteria

`AC-GH220-001` Meaningful code work is traceable to issue/FR/AC.  
`AC-GH220-002` PRs are scoped and describe migration/security/testing impact.  
`AC-GH220-003` Blocking CI precedes merge.  
`AC-GH220-004` High-risk changes receive explicit security/domain review.  
`AC-GH220-005` Migration/provider/UI PRs include their specialized evidence.  
`AC-GH220-006` Requirements/docs change with semantics, not after implementation.  
`AC-GH220-007` Secrets/private production data never enter GitHub artifacts/discussion.  
`AC-GH220-008` Review threads are resolved substantively.  
`AC-GH220-009` Releases identify exact commit/migrations/known issues.  
`AC-GH220-010` Hotfixes are reconciled back to main.  
`AC-GH220-011` Codex work uses dependency-aware issue/PR scopes.  
`AC-GH220-012` GitHub status never substitutes actual release/production verification.

---

## 36. Финальная доктрина

> **GitHub turns documented requirements into reviewable, testable increments. Issues define outcomes, PRs carry code/migrations/tests/docs together, blocking checks protect `main`, and every merged change remains traceable to the domain contract it implements rather than becoming an opaque code-only decision.**
