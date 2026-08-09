# DOMAIN DATA MIGRATIONS
## DOC-164 — mapping legacy professional/operational data into canonical domains

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет per-domain mapping rules для Profile, Portfolio, Projects, Training, Skills/Languages, Links, Contacts, Questionnaires, Castings, Feedback, Opportunity, Themes, AI, Help, Social и Achievements.

> **Domain migration follows canonical ownership. It separates concepts that legacy code may have mixed, preserves one owner per fact, and sends ambiguous mappings to Human review instead of creating plausible but unsupported professional data.**

---

## 2. Global rules

- same-profile relations verified;
- one owner per fact;
- no cross-domain duplicated mutable Source;
- source timestamps/provenance preserved where known;
- visibility mapped conservatively;
- no AI-generated fact promotion;
- migration idempotent.

## 3. Profile

Map identity/biography/profile data to Profile Source. If same biography is duplicated in several legacy fields, choose authoritative source by explicit precedence and report conflicts.

Conflicting values are not concatenated automatically.

## 4. Portfolio

Legacy photo assignments migrate to Portfolio relation with explicit type/order/visibility. Mandatory CLOSE_UP/FULL_BODY classification only maps from reliable legacy category/Human mapping.

## 5. Emotional Portfolio

Separate from Projects. Migrate shooting date, source images, captions and public state. If emotional photos were stored as project media, create appropriate relations while preserving original binary identity.

## 6. Emotional Grid

Map only supported sizes and known cell config. A flattened collage without cell state is not converted into fabricated editable grid; mark legacy-derived/review.

## 7. Projects/Roles

Create Project and Role entities/relations rather than one flattened string where legacy data allows. Preserve role photos links/order limits through target validation.

## 8. Legacy Project “Other” → Training

Automatic conversion only when legacy fields clearly indicate educational/training nature. Ambiguous “Other” remains Project/REQUIRES_REVIEW.

## 9. Training

Map course/institution/teacher/date exactly. Missing credential level/type remains unknown. Training never creates Skill automatically.

## 10. Skills

Free-text known skill maps via `SkillDefinition` dictionary only when exact match/approved synonym. Unknown custom skill creates review/dictionary extension workflow rather than arbitrary unvalidated Source.

Level maps only exact valid 1..5. Missing/qualitative stays review.

## 11. Languages

Map language to stable language definition/ISO identifier. Proficiency only exact mapping to `NATIVE/A1…C2`. Terms like `fluent/good/basic` require reviewed mapping unless legacy product documented exact equivalence.

## 12. Professional Media/Links

Classify each legacy entry:

```text
managed local file → MediaAsset/ProfessionalMedia relation
external URL → ProfessionalLink
raw embed HTML → extract known provider URL/ID or review
```

Preserve description/order/visibility.

## 13. Contacts

Split legacy contact row into:

```text
ContactEntry (who)
ContactMethod (how)
```

Parent/agent/manager role mapped only when explicit. Normalize phone without guessing country if source insufficient.

Notification recipients are not automatically created from Public Contact methods.

## 14. Questionnaires

Current saved questionnaire configurations migrate to `QuestionnaireDefinition/Draft` as semantics permit. Existing immutable/exported PDF can be attached as legacy artifact/history but does not fabricate exact Revision fields if source configuration missing.

Exactly one primary public Questionnaire is resolved through deterministic rule/Human review.

## 15. QR

Existing QR decoded and exact target compared. Unsafe/Admin/expired targets rejected. Do not reuse by filename/appearance.

## 16. Public Builder

If absent legacy, no data migration. New templates/config start from canonical defaults; source eligibility flags default conservative until Admin config.

## 17. Castings

Separate:

```text
Casting Source
AI Analysis
Human-confirmed Requirements
Materials
```

Legacy extracted AI text cannot become confirmed requirement unless provenance proves Human confirmation.

## 18. Feedback

Preserve original message/sender/timestamp/type if known, attachments, internal notes and workflow separately. Untyped professional message defaults OTHER/review, never guessed CASTING.

Existing converted Casting receives relation, not duplicate Casting.

## 19. Opportunity Pipeline

If legacy Opportunity absent, do not infer full historical pipeline from Project/Casting. Existing explicit opportunity/status data may map to canonical stages when semantics exact.

BOOKED cannot be inferred merely because a Project exists unless business provenance confirms relation.

## 20. Themes

Legacy visual settings map to structured Theme tokens only where values known. Hardcoded CSS can be captured as legacy baseline/reference, not automatically normalized by AI into authoritative design values without review.

## 21. BB/AI

Legacy AI-authored text:

- target current content remains owned by target domain;
- provenance `AI_LEGACY_UNKNOWN` if known AI but no snapshot;
- do not manufacture prompt/model/source snapshot.

## 22. Notifications

Legacy recipients/preferences are reviewed separately from Contacts. Historical delivery records may import as operational history; pending sends do not execute automatically.

## 23. VOP

Legacy health scripts/logs do not become active Findings without current revalidation. Safe automation must pass new allowlist/idempotency requirements.

## 24. Analytics

Historical analytics can migrate only when event/metric semantics known. Unknown counters remain legacy metric; do not reinterpret as canonical sessions/conversions.

## 25. Help Tickets

Technical support data maps to HelpTicket. Professional inquiries stay Feedback. Ambiguous legacy support/inquiry records require classification review.

## 26. Social Publishing

Social account metadata can map; credentials require validity verification. Legacy drafts/schedules imported as non-executing/reconciliation state until approved. Provider post marked PUBLISHED only with provider evidence/ID.

## 27. Achievements

Recognition status maps only exact semantic terms. Project recognition subject stays Project; no personal actress award inference.

## 28. Visibility triplet

For modules supporting:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

each flag maps independently. Legacy one `public=true` cannot automatically enable all three.

## 29. Ordering

Preserve explicit legacy ordering. If no ordering exists, use deterministic fallback and mark generated order where useful, not arbitrary random order.

## 30. Primary markers

Where target allows one primary item, detect multiple legacy primaries. Resolve through Human review unless a documented legacy precedence exists.

## 31. Archive/delete

Legacy soft-delete maps to archive/delete semantics only after understanding meaning. Deleted records do not reappear active.

## 32. Duplicate handling

Domain-specific duplicate candidates are listed. Auto-merge only when deterministic identity equality is proven and no provenance/context loss occurs.

## 33. Validation

Per-domain validation after mapping:

- required fields/invariants;
- same-profile references;
- uniqueness;
- visibility;
- lifecycle;
- source/mapping counts;
- representative UI/PDF projections.

## 34. Review report

Each unresolved item contains:

```text
legacy ID/value
candidate target
why ambiguous
possible choices
source evidence
```

No AI auto-decision for professional ambiguity.

## 35. Anti-patterns

Forbidden:

1. Training automatically creates Skill.
2. Project award becomes personal Achievement.
3. Language “fluent” becomes C1 silently.
4. Missing Skill level defaults 1.
5. One legacy public flag enables Builder/QNR/public all at once.
6. Notification recipients copied from public Contacts automatically.
7. AI extraction becomes confirmed Casting Requirement.
8. Existing Project implies Opportunity BOOKED.
9. Old pending social schedule resumes automatically.
10. Legacy VOP script becomes trusted safe action without review.
11. Existing PDF fabricates QuestionnaireRevision source fields.
12. Raw embed HTML migrates directly to public page.

## 36. Acceptance criteria

`AC-DOMMIG-001` Canonical domain ownership boundaries are preserved.  
`AC-DOMMIG-002` Profile/Portfolio/Emotional/Projects/Training are separated correctly.  
`AC-DOMMIG-003` Skills/Languages map only exact confirmed levels.  
`AC-DOMMIG-004` Local media and external links remain distinct.  
`AC-DOMMIG-005` Contacts and NotificationRecipients are decoupled.  
`AC-DOMMIG-006` Questionnaire/Builder history/eligibility is not fabricated.  
`AC-DOMMIG-007` Casting AI output is not promoted to confirmed requirement automatically.  
`AC-DOMMIG-008` Feedback/Casting/Opportunity provenance is preserved without inferred stages.  
`AC-DOMMIG-009` Legacy AI/VOP/Social operational state cannot trigger new side effects automatically.  
`AC-DOMMIG-010` Achievement recognition semantics remain exact.  
`AC-DOMMIG-011` Independent visibility flags are migrated conservatively.  
`AC-DOMMIG-012` All ambiguous professional mappings enter Human review queue.

---

## 37. Финальная доктрина

> **Domain migration disentangles legacy convenience structures into canonical owners without upgrading semantics. Every automated mapping must be evidence-backed; every ambiguous professional fact remains unresolved for Human review; and no migration side effect can silently publish, notify, book, confirm, schedule or otherwise manufacture business history.**
