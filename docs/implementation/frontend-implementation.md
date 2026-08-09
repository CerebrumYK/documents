# FRONTEND IMPLEMENTATION
## DOC-204 — Next.js UI architecture, public/admin projections, forms, accessibility and state boundaries

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт implementation rules для Next.js public/Admin frontend.

> **Frontend renders authorized projections and collects user intent; it does not own professional truth, permission, provider credentials or business state transitions. Every client-visible field must already be allowed for that surface before it reaches the browser.**

---

## 2. Rendering model

Use server rendering/server components for public/read-heavy content where framework architecture makes sense, with client components only for actual interaction.

Avoid making whole public site client-only.

## 3. Route groups

Conceptually separate:

```text
(public)
admin
public-builder
token-scoped resources
provider callbacks/API
```

Provider callbacks are server routes, not UI pages.

## 4. Public data loading

Public page calls dedicated Public Projection query. Never call Admin query then destructure fields client-side.

## 5. Admin data loading

Authenticated Server boundary loads Admin DTO with authorization. Sensitive server-only fields still need not reach client components if only server action needs them.

## 6. Builder data

Builder client receives `BuilderEligibilityDTO` only. Forged IDs revalidated on server Generate.

## 7. Client/server components

Server-only imports (DB, filesystem, secrets, provider SDKs) are prohibited in client component dependency graph.

Build/lint checks should prevent accidental leakage.

## 8. Forms

Pattern:

```text
Server DTO/defaults
→ accessible form
→ client UX validation
→ Server Action/API schema validation
→ application command
→ typed result/error
```

## 9. Optimistic UI

Use only where rollback semantics clear and no professional/publication state can be falsely represented as committed. High-impact Publish/Send/Stage transition should show authoritative server result.

## 10. Version/concurrency

Editable forms include hidden/internal expected version supplied by server. On `STALE_VERSION`, show conflict/reload/compare rather than overwrite automatically.

## 11. Save vs Publish UX

Buttons/states clearly separate:

```text
Save Draft
Publish
Activate
Send
Schedule
```

Do not use one ambiguous “Save” that also publishes externally.

## 12. AI Draft UX

Display:

- source/provenance;
- warnings;
- generated vs Human edited state;
- Apply separate;
- Publish/Send separate.

No automatic application on generation completion.

## 13. VOP UX

Finding card shows evidence/action class. Safe one-click action only when server registry says safe. Human-only recommendations navigate to owning editor.

## 14. Media uploader

- accessible file input + optional drag/drop;
- staged upload progress;
- server status (validating/quarantine/ready);
- no public preview URL for private/quarantined file;
- original filename display sanitized.

## 15. Portfolio

Use responsive derivatives while preserving configured contain/aspect/crop semantics. Gallery keyboard/touch accessible and lazy-loaded.

## 16. Emotional Grid editor

- exact supported grid presets;
- exact photo count;
- per-cell crop controls;
- keyboard/numeric alternative;
- confirmation state per cell;
- finalization disabled until all valid/confirmed.

Frontend cannot bypass server validation.

## 17. Tables/lists

Admin lists paginated/filterable. Status/priority text plus color/icon. Accessible row actions; avoid nested uncontrolled click targets.

## 18. Questionnaires

Editor shows mandatory blocks distinctly, source eligibility, stale/readiness warnings, live preview. Publish calls server command and then displays immutable revision ID/status.

## 19. PDF/QR preview

Preview should represent final DocumentModel as closely as practical. QR status includes verification; “generated” without verification is not READY.

## 20. Contacts

Per-method visibility controls explicit three surfaces. UI must not imply “public contact” = notification recipient.

## 21. Feedback Inbox

Separate visual states:

- unread/read;
- workflow;
- priority;
- assignee;
- next action;
- notification delivery.

Opening notification/Feedback does not automatically conflate these states.

## 22. Opportunity Pipeline

Kanban/list may visualize stages, but drag transition must call validated domain command. UI cannot locally commit stage based on drag.

## 23. Theme editor

Structured tokens/forms, responsive preview, lock state, contrast/accessibility results. No arbitrary CSS/JS injection field baseline.

## 24. Notifications Center

Unread/read/archive state per Notification. Clicking source link should not mutate business read/workflow automatically.

## 25. Social

Draft editor visibly separates account/platform/media/caption/approval/schedule. Show approval stale after edits. UNKNOWN_OUTCOME visually distinct from failed.

## 26. Help

Help article search and Ticket thread separates public reply/internal note using role-aware server DTO; internal note is never merely hidden by CSS from requester.

## 27. Accessibility

Implement semantic native controls, focus management, keyboard support, labels/errors, reduced motion, contrast-aware Theme.

## 28. Localization

Use stable translation keys for UI; professional content localization comes from Source/Draft. Do not machine-translate factual content silently at render time.

## 29. Errors

Map stable error code to user-friendly RU/locale message. Preserve correlation ID for support. Never render raw stack/provider response.

## 30. Loading states

Use skeleton/progress where helpful without layout shift. Async job shows actual server state and refresh/poll/subscription strategy bounded.

## 31. Polling

Do not poll every second indefinitely. Use reasonable backoff/stop when terminal. Realtime only if justified.

## 32. Caching

Framework page/data caching only for surfaces compatible with access/invalidation. Admin/token pages default dynamic/private unless explicitly safe.

## 33. SEO

Public pages render metadata from public confirmed projection. Admin/Builder temporary/token routes noindex as policy.

## 34. Analytics

Client emits allowlisted interaction events asynchronously. No raw contact/message/token in properties. Analytics failure never blocks action/navigation.

## 35. Testing hooks

Prefer accessible roles/text/test IDs only for truly ambiguous selectors. Do not add hidden production data solely for test convenience.

## 36. Anti-patterns

Forbidden:

1. Admin API response reused for public page.
2. DB/provider SDK import in client component.
3. Client sets `approved/published/rights_ok`.
4. Optimistic UI says published before server/provider result.
5. Hidden fields stored in React state.
6. One Save button silently publishes.
7. Drag-only critical control.
8. Raw provider error/stack rendered.
9. Quarantined file public blob URL.
10. Infinite 1s polling.
11. Theme accepts arbitrary script/CSS.
12. Analytics event contains full Feedback body.

## 37. Acceptance criteria

`AC-FE-001` Public/Admin/Builder use dedicated authorized DTOs.  
`AC-FE-002` Client bundles exclude DB/filesystem/secrets/provider credentials.  
`AC-FE-003` Forms revalidate server-side and carry expected version where required.  
`AC-FE-004` Save/Publish/Apply/Send/Schedule are visibly distinct.  
`AC-FE-005` AI/VOP UX exposes provenance/authority rather than auto-action.  
`AC-FE-006` Media/Portfolio/Grid UI preserves source and accessibility rules.  
`AC-FE-007` Feedback/Notification/Opportunity states are not conflated.  
`AC-FE-008` Theme/Social high-impact actions reflect server approval/state.  
`AC-FE-009` Errors/loading/polling are bounded and user-actionable.  
`AC-FE-010` Public metadata/analytics remain privacy-safe.  
`AC-FE-011` WCAG-oriented semantic/keyboard/focus requirements are implemented.  
`AC-FE-012` Frontend never becomes authorization/business-source authority.

---

## 38. Финальная доктрина

> **The Next.js frontend is a projection-and-intent layer. It renders only already-authorized data, gathers explicit Human actions and presents durable server/provider states accurately, while Source ownership, authorization, publication, side effects and secrets remain entirely on the server side.**
