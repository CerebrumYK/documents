# BRANCH & RELEASE STRATEGY
## DOC-222 — short-lived branches, releases, hotfixes, tags and production promotion

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт branch/release model для небольшого, но production-critical профессионального сайта.

> **Branches organize review; releases identify deployable product states. Neither branch names nor merged PRs are production truth until the exact release has passed quality gates and deployment validation.**

---

## 2. Baseline branch model

Use trunk-oriented workflow around:

```text
main
```

with short-lived branches for implementation.

Long-lived `develop` is not baseline unless team size/release cadence later requires it.

## 3. Branch classes

```text
feat/*
fix/*
refactor/*
security/*
migration/*
ops/*
docs/*
hotfix/*
```

## 4. Short-lived principle

Branches should integrate frequently and stay scoped. Large dependency-aware work is decomposed by DOC-206 rather than kept as months-long hidden branch.

## 5. `main`

`main` represents latest integrated canonical code/documentation and should pass blocking CI.

Direct destructive history rewrite/force-push is forbidden.

## 6. Release identity

Every production release identifies exact Git commit SHA and optional semantic tag.

Suggested tags:

```text
v0.x.y
v1.x.y
```

Versioning scheme becomes stable when application release process is formalized; exact semver policy may evolve.

## 7. Pre-1.0

During major implementation/migration, `0.x` releases may intentionally be incomplete but must accurately state available features/migration compatibility.

## 8. Release candidate

High-risk major releases may use:

```text
v1.0.0-rc.1
```

or equivalent staging marker. RC does not bypass migration/security gates.

## 9. Release content

Release notes include:

- user-visible changes;
- security fixes where disclosure safe;
- schema/data migrations;
- config changes;
- provider changes;
- known limitations;
- rollback compatibility;
- required manual post-deploy actions.

## 10. Migration compatibility

Every release states expected DB schema range/migration set. A code rollback across incompatible lossy schema change requires explicit DB rollback/restore plan.

## 11. Production promotion

Recommended:

```text
merge main
→ blocking CI
→ build exact commit
→ release candidate/evidence
→ production preflight
→ deploy exact commit
→ migrations
→ smoke/monitor
→ mark production release
```

No rebuilding from a different uncommitted workspace for production.

## 12. Hotfix

For urgent production defect:

```text
branch from production release/appropriate main point
→ minimal fix
→ targeted + security tests
→ review/merge
→ deploy
→ ensure fix exists in main
```

If main has moved substantially, use explicit merge/cherry-pick reconciliation.

## 13. Security hotfix

Minimize public issue details before containment when vulnerability disclosure could increase risk. Rotate compromised secrets independently of code release when necessary.

## 14. Rollback

Application rollback target is known release artifact/commit. Do not rollback code blindly if DB schema no longer compatible.

Decision matrix:

```text
backward-compatible DB → code rollback possible
incompatible/lossy migration → DB restore/forward fix plan required
external side-effect issue → reconcile provider before retry/rollback actions
```

## 15. Feature flags

Can allow staged enablement of optional module/provider after deployment. Flag cannot bypass migration/security/authority rules.

## 16. Database migrations and branches

Concurrent PR migrations should use ordered naming/tool mechanism and rebase/reconcile conflicts before merge. Never merge two migrations both assuming same previous state without validation.

## 17. Documentation release

Canonical docs on `main` reflect intended current/target architecture. If implementation is incomplete, traceability/gap docs state status; docs should not falsely imply deployment.

## 18. Release branches

Not baseline. May be introduced if supporting multiple production versions, but requires clear cherry-pick/security patch policy.

## 19. Dependency upgrades

Major framework/DB/provider SDK upgrade should be isolated release concern where possible, with compatibility/performance/provider testing.

## 20. Release artifact reproducibility

Use lockfile and documented runtime. Build should be reproducible enough to map deployed artifact to commit/dependencies/config schema.

## 21. Environment config

Secrets not part of tag. Release notes list required variable names/schema changes without values.

## 22. Release audit

Record:

```text
version/tag
commit SHA
migration versions
deployment timestamp
operator
result
rollback if any
```

## 23. Deprecated features

Deprecation identifies replacement/removal release and migration path. Do not silently remove externally used public URL/data format.

## 24. Backward compatibility

Public URLs, PDF semantics, API contracts and stored snapshots considered before breaking change. Historical immutable revisions remain renderable as documented.

## 25. Anti-patterns

Forbidden:

1. Months-long integration branch diverges from main.
2. Production deploy from uncommitted local tree.
3. Tag points to code different from deployed artifact unnoticed.
4. Code rollback across incompatible DB without plan.
5. Hotfix never returns to main.
6. Feature flag used to disable auth.
7. Migration conflict resolved by renaming file only without semantics.
8. Release notes omit required config/migration change.
9. `main` force-pushed to “clean history”.
10. Merge equals automatic production success assumption.

## 26. Acceptance criteria

`AC-BR-001` `main` is the canonical integration branch with blocking CI.  
`AC-BR-002` Feature work uses short-lived scoped branches.  
`AC-BR-003` Production release maps to exact commit/artifact.  
`AC-BR-004` Releases document migrations/config/provider changes.  
`AC-BR-005` Hotfixes are reconciled back into main.  
`AC-BR-006` Rollback checks DB/external-side-effect compatibility.  
`AC-BR-007` Feature flags cannot bypass hard invariants.  
`AC-BR-008` Concurrent migrations are semantically reconciled before merge.  
`AC-BR-009` Secrets remain environment-managed, not release artifacts.  
`AC-BR-010` Release/deployment result is auditable.

---

## 27. Финальная доктрина

> **The project stays close to `main`, releases exact tested commits and treats migrations and external side effects as part of release compatibility. Short-lived branches maximize review feedback, while tags/releases and deployment records provide the durable identity of what actually reached production.**
