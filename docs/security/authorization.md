# AUTHORIZATION
## DOC-142 — permissions, scopes, ownership, command/query policy и server-side access control

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет authorization model для Admin, support, public, Builder, token-scoped и system operations.

> **Authorization is evaluated server-side for every privileged command/query from current principal, permission, profile scope, entity ownership, lifecycle/access class and action-specific invariants. UI state, hidden buttons, UUIDs, AI decisions, client flags and cached eligibility never constitute authority.**

---

## 2. Core questions

Для каждой операции сервер отвечает:

```text
WHO is acting?
WHAT action is requested?
ON WHICH resource?
IN WHICH profile/scope?
IN WHICH current state?
WITH WHICH authority?
IS the requested transition allowed now?
```

## 3. Surface authorization classes

```text
PUBLIC_READ
PUBLIC_WRITE_CONSTRAINED
TOKEN_SCOPED_READ
TOKEN_SCOPED_ACTION
ADMIN_READ
ADMIN_WRITE
SUPPORT_PRIVILEGED
SYSTEM_TYPED_ACTION
PROVIDER_RECONCILIATION
```

## 4. Permission model

Проект может использовать RBAC, policy-based authorization или их комбинацию, но canonical checks должны быть выразимы как explicit permissions.

Suggested permissions:

```text
PROFILE_READ / PROFILE_EDIT / PROFILE_PUBLISH
MEDIA_READ_PRIVATE / MEDIA_EDIT
QUESTIONNAIRE_EDIT / QUESTIONNAIRE_PUBLISH
CASTING_READ / CASTING_EDIT
FEEDBACK_READ / FEEDBACK_MANAGE
OPPORTUNITY_READ / OPPORTUNITY_MANAGE
THEME_EDIT / THEME_ACTIVATE
SOCIAL_EDIT / SOCIAL_APPROVE / SOCIAL_PUBLISH
ANALYTICS_READ
HELP_TICKET_READ / HELP_TICKET_MANAGE
SECURITY_ADMIN
SYSTEM_MAINTENANCE
```

Exact role bundles are implementation configuration; permissions remain action-oriented.

## 5. Roles

Possible roles:

```text
ADMIN
EDITOR
SUPPORT
SECURITY_ADMIN
READ_ONLY
```

Single-user deployment may grant ADMIN all intended permissions, but code must not assume `if authenticated then allow everything`.

## 6. Profile scope

Every profile-owned entity carries `profile_id` or equivalent ownership chain.

Authorization checks same-profile ownership before mutation/reference.

### AUTHZ-INV-001
Cross-profile entity IDs cannot be combined by client tampering.

## 7. Resource ownership

Relations such as:

```text
Profile → Project
Profile → Media
Profile → Questionnaire
Profile → Casting
```

must be verified through authoritative DB relation, not client-supplied profile ID alone.

## 8. Lifecycle-aware authorization

Permission to edit does not mean every state is editable.

Examples:

- immutable QuestionnaireRevision cannot be edited;
- archived entity may require restore command;
- Social PublishSnapshot is immutable;
- Builder GenerationSnapshot cannot be rewritten;
- provider Attempt history is append-only.

### AUTHZ-INV-002
Authorization includes lifecycle invariants, not only role checks.

## 9. Command authorization

Each Server Action/command should define:

```text
required principal class
required permission
resource loader
profile/ownership check
state preconditions
validation
expected version
idempotency policy
```

No generic `updateEntity(type,id,payload)` with arbitrary cross-domain fields.

## 10. Query authorization

Queries must return dedicated DTO/projection for surface.

```text
GetPublicProfile → PublicProjection
GetBuilderEligibility → BuilderProjection
GetAdminProfile → AdminProjection
```

Do not fetch full ORM model then rely on client to hide fields.

## 11. Field-level visibility

Visibility flags (`show_on_public_site`, `allow_in_admin_questionnaires`, `allow_in_public_questionnaire_builder`) are data authorization inputs, not CSS preferences.

They are applied before serialization/search indexing/cache publication.

## 12. Public read

Anonymous public access only to currently published/eligible projection.

Archived/draft/private records return not-found/forbidden semantics that avoid unnecessary existence disclosure.

## 13. Public constrained write

Feedback, Builder and analytics ingestion are public write surfaces, but accept only narrow command schemas.

Anonymous caller cannot set:

- workflow status;
- admin assignee;
- visibility;
- provider result;
- approval;
- internal notes;
- domain ownership.

## 14. Builder authorization

Builder session can:

- read Builder-eligible projection;
- configure temporary allowed selections;
- generate snapshot/artifact through server validation.

Builder cannot:

- mutate Source;
- access Admin questionnaire pool;
- change URL/contact/skill/language facts;
- bypass current eligibility.

## 15. Token-scoped authorization

Token resolves to explicit resource/scope.

Server must check:

```text
token hash/state
resource binding
scope
expiry
revocation
current security policy
```

Token never widens into profile-wide Admin access.

## 16. Support authorization

Help Ticket support role may manage ticket operations but does not receive generic permission to edit professional Source.

If support repair is necessary, dedicated maintenance/domain action with separate privilege/audit is required.

## 17. AI authorization

AI outputs have no principal authority.

AI may produce draft/recommendation, but only authenticated Human command can apply where domain permits.

### AUTHZ-INV-003
AI confidence/model output cannot satisfy permission check.

## 18. VOP authorization

VOP safe automation is constrained by a hard allowlisted action registry.

Configuration cannot grant prohibited business authority such as publish, change Skill level, confirm Casting requirement or Book Opportunity.

## 19. Worker authorization

Worker receives typed job + target IDs/snapshot IDs, reloads current state and executes only registered handler.

Worker does not accept arbitrary method name/SQL/URL command from job payload.

## 20. Provider authorization

Webhook provider can update only integration-specific reconciliation state after signature verification. It cannot invoke arbitrary domain commands.

## 21. Social publishing authority

Only explicit Human approval/publish/schedule command creates publishing intent.

Worker executes immutable approved snapshot; it does not self-approve.

## 22. Notification authority

Domain event creates notification intent through policy. Notification provider callback updates delivery evidence only and never business record status.

## 23. Optimistic concurrency

High-impact mutable entities use `expectedVersion` or equivalent.

A stale authorized user is still not allowed to overwrite newer state silently.

### AUTHZ-INV-004
Permission does not override concurrency conflict.

## 24. Re-authentication for critical actions

Security-sensitive actions MAY require recent authentication/MFA in future:

- credential change;
- security settings;
- destructive purge;
- provider credential connect/disconnect;
- emergency revoke.

Architecture should permit step-up authentication.

## 25. Access-denied behavior

Public errors avoid existence leaks.

Admin errors may distinguish permission vs state where useful and safe.

Canonical errors:

```text
AUTHENTICATION_REQUIRED
ACCESS_DENIED
RESOURCE_NOT_FOUND
RESOURCE_WRONG_PROFILE
ACTION_NOT_ALLOWED_IN_STATE
STALE_VERSION
TOKEN_INVALID
TOKEN_EXPIRED
TOKEN_REVOKED
```

## 26. Database enforcement

DB constraints reinforce ownership/integrity, but app authorization remains mandatory.

Foreign keys should prevent impossible cross-reference patterns where schema permits.

## 27. Search authorization

Public/Builder/Admin indexes or projections remain separate. Search result hydration applies current access check before serialization.

## 28. Cache authorization

Cache key includes access class/profile/generation where relevant. A cached result is not proof caller may read it.

## 29. File authorization

Private attachment/media download uses server resolver checking current permission/token scope. Raw predictable filesystem path is not accepted.

## 30. Audit

Audit authorization-sensitive events:

- permission/role changes;
- failed privileged access attempts where useful;
- visibility changes;
- publish/activate/send;
- token creation/revocation;
- destructive actions;
- security overrides.

## 31. Authorization anti-patterns

Forbidden:

1. `if (session) allow all`.
2. Client sends `isAdmin=true`.
3. UI hidden button used as protection.
4. UUID secrecy used as ACL.
5. Client-supplied `profile_id` trusted without relation check.
6. Builder can request arbitrary entity by ID.
7. Public visibility filter applied only after full payload reaches browser.
8. Support role gets generic DB update.
9. AI output sets `approved=true`.
10. VOP configuration enables forbidden business command.
11. Worker executes arbitrary serialized function name.
12. Webhook changes Opportunity stage.
13. Notification delivery marks Feedback resolved.
14. Cached Admin DTO served to public route.
15. Stale authorized write overwrites newer Human edit.

## 32. E2E cases

- anonymous cannot call Admin query/action;
- read-only role cannot publish;
- editor cannot invoke security-admin action;
- cross-profile relation tampering rejected;
- hidden entity ID probe on Builder reveals no data;
- token grants only exact resource scope;
- expired/revoked token denied;
- immutable revision edit rejected despite admin permission;
- stale version rejected;
- support ticket permission cannot edit Profile;
- AI/VOP cannot publish;
- webhook cannot create new business intent;
- worker unknown action type rejected;
- public search excludes hidden result;
- private media path cannot bypass resolver.

## 33. Acceptance criteria

`AC-AUTHZ-001` Every privileged command/query is authorized server-side.  
`AC-AUTHZ-002` Permissions, profile ownership and lifecycle are checked independently.  
`AC-AUTHZ-003` Public/Builder/Admin DTOs are projection-specific.  
`AC-AUTHZ-004` Hidden fields never rely on client filtering.  
`AC-AUTHZ-005` Builder/token scopes cannot elevate into Admin access.  
`AC-AUTHZ-006` Cross-profile ID tampering is rejected.  
`AC-AUTHZ-007` AI, VOP, worker and provider callbacks have only explicitly registered authority.  
`AC-AUTHZ-008` Support role has no generic professional Source mutation.  
`AC-AUTHZ-009` Stale version cannot overwrite newer data even when caller has permission.  
`AC-AUTHZ-010` Search/cache/file delivery enforce the same access policy as direct queries.  
`AC-AUTHZ-011` Critical authorization changes are audited.  
`AC-AUTHZ-012` E2E tests prove anonymous, role, profile, lifecycle and token boundaries.

---

## 34. Финальная доктрина

> **Authorization is an explicit server-side decision made for the current action and current resource state. Identity, UI state, entity IDs, AI recommendations, cached eligibility, worker execution or provider messages never grant authority by themselves. Every surface sees only its dedicated projection and every mutation remains permission-, ownership-, lifecycle-, concurrency- and invariant-aware.**
