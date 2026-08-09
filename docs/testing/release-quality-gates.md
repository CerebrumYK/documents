# RELEASE QUALITY GATES
## DOC-179 — blocking CI/release criteria, evidence, exceptions и sign-off

**Статус:** Canonical / Required

---

## 1. Назначение

Документ объединяет testing/NFR/security requirements в единый release decision framework.

> **A release is promotable only when blocking quality gates pass or an explicit documented risk acceptance exists where policy allows. Security/privacy/data-loss blockers are not waived as routine delivery pressure.**

---

## 2. Gate classes

```text
BLOCKING
CONDITIONAL
WARNING
OBSERVATIONAL
```

## 3. Mandatory blocking gates

At minimum:

```text
build/typecheck
critical unit tests
critical integration tests
critical E2E
security authorization/projection tests
migration validation when schema changes
accessibility blockers
secret scanning
```

## 4. Security blockers

Zero accepted known release blockers for:

- public private-data exposure;
- auth bypass;
- privilege escalation;
- raw secret exposure;
- destructive data corruption;
- unsafe migration fact loss;
- duplicate uncontrolled external publication.

## 5. Functional blockers

Critical professional workflows must work:

- public Profile/Portfolio;
- mandatory Questionnaire generation;
- Admin Source edit;
- Feedback persistence;
- core media availability.

Module-specific optional provider feature may release disabled/degraded if documented and core unaffected.

## 6. Test gate

No failed blocking suite. Flaky blocking test cannot be simply rerun until green without issue; repeated nondeterminism is release risk.

## 7. Migration gate

If migration included:

- backup confirmed;
- migration test passes;
- dry run/reconciliation complete as relevant;
- rollback point exists;
- ambiguous public-risk items zero;
- post-migration smoke plan ready.

## 8. Accessibility gate

No critical keyboard/focus/form/contrast/core-content accessibility blockers. Automated scan plus critical manual evidence for major UI/theme change.

## 9. Performance gate

Hard budgets for critical public paths must pass or documented exceptional acceptance where no security/accessibility degradation occurs. Significant regression requires owner/explanation.

## 10. Compatibility gate

Critical smoke passes supported browser matrix; known browser-specific blocker prevents release to affected declared-supported platform.

## 11. SEO gate

For public-release changes:

- canonical valid;
- private/token pages non-indexable;
- sitemap valid;
- no token/storage path metadata leak.

## 12. Documentation gate

Behavioral/schema/API changes update canonical documentation and acceptance/traceability IDs before release.

## 13. Observability gate

New critical background/provider workflow has:

- normalized status/errors;
- logs/metrics;
- failure visibility;
- no secret logging.

## 14. Backup/recovery gate

High-risk DB/media changes require current backup and known restore procedure. Periodic restore test must not be indefinitely stale.

## 15. Dependency gate

Critical exploitable dependency vulnerability blocks release unless patched/mitigated with documented security acceptance.

## 16. Configuration gate

Production required environment variables/secrets validated at startup/deploy. No missing provider credential should crash unrelated core unless provider marked required.

## 17. Provider gate

Social/Notification/AI provider integrations can use sandbox/adapter tests. Real-provider smoke is required when integration contract changed materially and a safe sandbox/test account exists.

## 18. Theme gate

New active Theme passes contrast/accessibility/responsive/visual regression. Theme AI output itself is not sufficient evidence.

## 19. PDF/QR gate

Questionnaire changes require:

- PDF render success;
- selectable text;
- hyperlink target assertions;
- QR exact decode where enabled;
- no private/admin URL leak.

## 20. Release candidate evidence

Produce summary:

```text
commit/release id
schema migration set
test results
security scan
accessibility status
performance comparison
known issues
backup status
manual checks
approver/sign-off
```

## 21. Risk acceptance

Allowed only for non-critical known issues according to governance. Record:

- issue;
- impact;
- mitigation;
- owner;
- expiry/follow-up;
- approver.

No vague “accepted” without owner/date.

## 22. Emergency release

Emergency fix may use reduced non-relevant suite, but security/targeted tests/build remain. Full regression follows immediately according to operations policy.

## 23. Rollback decision

Release plan defines signals for rollback vs forward fix. Data/privacy/security corruption biases rollback/containment rather than prolonged degraded exposure.

## 24. Post-deploy smoke

Immediately verify:

- health endpoint/app startup;
- DB schema version;
- public Profile;
- Admin login;
- critical media;
- Feedback/Questionnaire smoke where safe;
- queue/provider health.

## 25. Post-release observation

Monitor error/security/job/provider metrics for elevated period after migration/high-risk release.

## 26. Gate ownership

CI automates deterministic gates. Human sign-off owns manual PDF/visual/provider/migration decisions where automation incomplete.

## 27. Release status

Canonical:

```text
NOT_READY
READY_WITH_WARNINGS
READY
BLOCKED
RELEASED
ROLLED_BACK
```

`READY_WITH_WARNINGS` cannot contain blocking security/privacy/data-loss defect.

## 28. Anti-patterns

Forbidden:

1. Merge/release with red CI “because probably flaky”.
2. Security failure waived for deadline.
3. Migration without backup/rollback.
4. Accessibility only checked after release.
5. Performance “optimization” bypasses security.
6. Documentation deferred indefinitely.
7. Provider failure invisible operationally.
8. Risk acceptance without owner/expiry.
9. Post-deploy smoke skipped after schema change.
10. Release marked READY solely because app builds.

## 29. Acceptance criteria

`AC-GATE-001` Blocking gate set is automated in CI where feasible.  
`AC-GATE-002` Security/privacy/data-loss blockers cannot enter READY_WITH_WARNINGS.  
`AC-GATE-003` Critical business journeys pass before production.  
`AC-GATE-004` Migration changes require backup/validation/rollback evidence.  
`AC-GATE-005` Accessibility/performance/compatibility gates are explicit.  
`AC-GATE-006` PDF/QR correctness is blocking when affected.  
`AC-GATE-007` Documentation/traceability update is Definition of Done.  
`AC-GATE-008` Provider/background workflows have observability.  
`AC-GATE-009` Risk acceptance is explicit, owned and expiring.  
`AC-GATE-010` Post-deploy smoke/observation occurs for production release.  
`AC-GATE-011` Emergency release retains targeted security/correctness tests.  
`AC-GATE-012` Release evidence identifies exact commit/migrations/test state.

---

## 30. Финальная доктрина

> **Release readiness is an evidence-backed state, not an opinion. Build, functional, security, migration, accessibility, compatibility, performance, PDF/QR, documentation and observability gates combine into one decision, with no routine waiver path for private-data exposure, corrupted professional truth or uncontrolled external side effects.**
