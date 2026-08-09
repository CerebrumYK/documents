# END-TO-END TESTING
## DOC-173 — critical browser journeys, authority boundaries, publication workflows и cross-module scenarios

**Статус:** Canonical / Required

---

## 1. Назначение

E2E tests prove that assembled application behavior matches the documented Customer/Admin journeys and security boundaries.

> **E2E tests are reserved for critical cross-layer contracts: what the user can actually see/do, what must remain impossible, and whether the final artifact/provider state corresponds to the exact Human-approved source.**

---

## 2. Tooling

Preferred browser automation: Playwright or equivalent cross-browser framework integrated with real application, PostgreSQL and controlled storage/provider fakes.

## 3. Test personas

```text
Anonymous Visitor
Casting Specialist / Builder Visitor
Admin
Read-only/limited Admin where roles exist
Support User
Token-scoped Recipient
```

## 4. Public Profile journey

Verify:

- public Profile loads;
- hidden data absent from response/DOM;
- Main Portfolio works;
- media playback/link navigation;
- Contacts only allowed methods;
- localized content;
- no Admin route/data leakage.

## 5. Portfolio

Test responsive gallery, item open/navigation, full-source frame/contain rules where required, keyboard access and archived/private exclusions.

## 6. Emotional Portfolio/Grid

Test public composite/full portfolio semantics, shooting date, no individual emotional photos leaking into Questionnaire row, supported grid finalization and historical rendering.

## 7. Questionnaire

Admin:

```text
create/edit draft
→ readiness
→ publish
→ immutable revision
→ HTML/PDF
```

Public:

- current primary chooser;
- links clickable;
- QR exact decode;
- required Close-Up/Full Body rules.

Update Source afterward and prove historical Revision unchanged.

## 8. Public Builder

```text
open
→ choose template
→ receive eligible items only
→ configure
→ preview
→ generate
→ immutable snapshot/PDF
```

Forge hidden item ID and prove server rejects without leak.

## 9. Feedback

```text
submit public form
→ Feedback committed
→ success shown
→ Admin Inbox record exists
→ In-App notification exists
```

Make external notification provider fail; visitor success/Feedback still persist.

Test attachment validation and no public read-by-UUID.

## 10. Feedback→Casting

Human Admin explicit conversion creates one Casting with bidirectional provenance. Double click/concurrent retry creates one Casting.

## 11. Casting AI

Explicit request only. Use provider fake. Review proposed requirements, confirm/reject Human side. AI failure leaves Casting Source intact.

## 12. Casting Questionnaire

AI/BB may create Draft/recommendation, but publish/send require explicit Human actions. Test no autonomous publication.

## 13. Opportunity

Test actual stage transition machine, StageHistory, next actions, outcome. Forbidden skip rejected where configured. BOOKED does not auto-create Project until explicit Human conversion.

## 14. Theme

Create/edit/preview/publish/activate according to theme contract. Accessibility failure blocks activation. Rollback restores prior ThemeRevision atomically.

AI theme generation stays Draft until Human approval.

## 15. BB Assistant

Generate grounded Draft via fake AI → warnings/sources → Human edit → Apply → target Draft changes → public remains unchanged until separate publish.

## 16. VOP

Run scan → Finding → safe derived action. Attempt prohibited VOP business action and assert impossible. Re-scan resolves only actual fixed condition.

## 17. Notifications

Domain event → In-App + external delivery attempts. Mark notification read and prove Feedback read/workflow unchanged. Quiet hours defer external but In-App remains.

## 18. Social Publishing

Fake provider journey:

```text
connect account
→ create Draft
→ select eligible media
→ BB caption apply
→ Human approve
→ schedule/publish
→ immutable snapshot
→ provider accepted
→ webhook published
```

Edit Draft after scheduling and prove snapshot unchanged. Timeout → UNKNOWN_OUTCOME → reconcile before retry.

## 19. Achievements

NOMINATED/OFFICIAL_SELECTION cannot render as WINNER/award-winning. Questionnaire snapshots exact status; current correction does not rewrite old PDF.

## 20. Help Tickets

Admin creates technical ticket, adds attachment, support public reply/internal note; requester/ordinary scope never receives internal note. Ticket status changes do not mutate linked professional entity.

## 21. Authentication

Login/logout/session expiry/revocation/recovery smoke flow. Unauthenticated Admin route denied.

## 22. Authorization

Forge IDs/profile/surface flags. Read-only/support/token persona cannot perform higher-privilege command.

## 23. Deletion/retention

Archive/revoke/restore scenarios prove current exposure changes without historical rewrite or unintended resend.

## 24. Search/cache

Change visibility/archive, then verify public search/cache no longer serves record. Admin still sees according to policy.

## 25. Mobile

Critical public + core Admin flows run in representative mobile viewport. Touch navigation, forms, media and Builder tested.

## 26. Cross-browser

Critical public smoke and main Admin form smoke in Chromium, Firefox, WebKit according to CI capacity.

## 27. Test IDs

Use existing module E2E IDs from docs where available. Canonical suite maps ID → automated test file.

## 28. Fixtures

Seed one compact but semantically rich profile with public/private variants, media, links, castings etc. Keep fixture creation through factories/domain helpers, not huge brittle SQL dump.

## 29. Provider fakes

Tests can deterministically choose response scenario. No external internet required for blocking E2E suite.

## 30. Browser assertions

Do not assert only text hidden. Inspect network/serialized response for sensitive-field absence where boundary matters.

## 31. Downloads

Capture generated PDF and inspect with helper, not merely “download event occurred”.

## 32. Flakiness

Wait for semantic UI/job state, not fixed sleeps. Jobs expose test-observable completion/status.

## 33. Anti-patterns

Forbidden:

1. Hundreds of trivial unit edge cases duplicated in browser.
2. Fixed `sleep(5000)` synchronization.
3. Test depends on live social/AI provider.
4. Hidden field test only checks CSS.
5. PDF test ignores downloaded bytes.
6. No negative forged-ID tests.
7. E2E state shared across parallel tests unpredictably.
8. Test creates source directly bypassing domain for every scenario.
9. Browser test marks provider published without reconciliation simulation.

## 34. Acceptance criteria

`AC-E2E-001` Every critical public/Admin business journey has browser-level coverage.  
`AC-E2E-002` Public/private/Builder/token negative boundaries are tested.  
`AC-E2E-003` Questionnaire/Builder historical snapshots survive later Source changes.  
`AC-E2E-004` Feedback remains durable through notification failure.  
`AC-E2E-005` AI modules cannot auto-confirm/publish/send.  
`AC-E2E-006` Opportunity/Theme/Social high-impact actions require Human authority.  
`AC-E2E-007` Social unknown outcome reconciles before retry.  
`AC-E2E-008` Help internal notes and private attachments remain private.  
`AC-E2E-009` PDF/QR are inspected semantically.  
`AC-E2E-010` Critical mobile/cross-browser smoke exists.  
`AC-E2E-011` Provider fakes make CI deterministic.  
`AC-E2E-012` Module E2E IDs are traceable to automated tests.

---

## 35. Финальная доктрина

> **E2E coverage proves the real professional journeys and the forbidden boundaries between them: public visitors never receive hidden data, AI never becomes Human authority, provider failures never erase Source, immutable documents remain historically exact and outbound publication happens only from explicit approved snapshots.**
