# DELETION, RETENTION & REDACTION

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная политика архивации, удаления, retention, privacy redaction и безопасного cleanup

**Целевой файл:** `docs/database/deletion-and-retention.md`  
**Документ:** DOC-094  
**Статус:** ✅ Completed  
**Тип:** Database / Retention / Archive / Deletion / Redaction / Cleanup

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/business-rules.md`
- `docs/product/product-principles.md`
- `docs/domain/domain-model.md`
- `docs/domain/entity-relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions-and-history.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`
- `docs/architecture/media-processing.md`
- `docs/architecture/pdf-generation.md`
- `docs/architecture/background-jobs.md`
- `docs/architecture/cache.md`
- `docs/architecture/search.md`
- `docs/architecture/ai.md`
- `docs/architecture/notifications.md`
- `docs/database/database-architecture.md`
- `docs/database/data-dictionary.md`
- `docs/database/visibility-and-access.md`
- `docs/database/validation-and-constraints.md`

---

# 1. Назначение документа

Настоящий документ определяет, **что происходит с данными, когда они больше не являются текущими или нужны только ограниченное время**.

Он фиксирует:

1. Archive;
2. Soft Removal;
3. Hard Delete;
4. Redaction;
5. Retention Classes;
6. immutable revisions;
7. historical artifacts;
8. Media dependencies;
9. Feedback;
10. Castings;
11. Opportunities;
12. Builder Sessions;
13. AI outputs;
14. Notifications;
15. Jobs/Outbox;
16. Search/Cache;
17. Analytics;
18. Audit;
19. Social;
20. Support;
21. orphan cleanup;
22. temporary files;
23. backups;
24. restore;
25. legal/privacy deletion;
26. deletion workflow;
27. safety checks;
28. observability;
29. testing.

---

# 2. Главная доктрина

> **Удаление текущего представления не должно уничтожать профессиональную историю, а сохранение истории не должно автоматически давать право продолжать публично выдавать данные, которые больше не должны быть доступны.**

Canonical:

```text id="30ih3p"
CURRENT SOURCE
   ↓
ARCHIVE / REMOVE FROM CURRENT USE
   ↓
HISTORICAL REFERENCES MAY REMAIN
   ↓
RETENTION POLICY
   ↓
REDACT / DELETE WHEN PERMITTED
```

---

# 3. RET identifiers

Используются:

```text id="y6qgo4"
RET-*
RET-ARC-*
RET-DEL-*
RET-RED-*
RET-MED-*
RET-HIS-*
RET-PRI-*
RET-TMP-*
RET-OPS-*
RET-INV-*
RET-AP-*
```

---

# 4. Core terms

Система различает:

```text id="9w3e1w"
ACTIVE
ARCHIVED
SUPERSEDED
EXPIRED
REDACTED
DELETED
PURGED
```

---

# 5. ACTIVE

Entity участвует в current workflows.

---

# 6. ARCHIVED

Entity:

- сохраняется;
- не участвует в обычных current/public selections;
- остаётся доступной Admin/history;
- может быть восстановлена, если domain policy допускает.

---

# 7. SUPERSEDED

Immutable historical version, которая больше не является current.

Пример:

```text id="2p5jgi"
Questionnaire Revision 4
```

после публикации Revision 5.

---

# 8. EXPIRED

Temporary/access entity больше не действительна по времени.

Examples:

```text id="71c1y2"
BuilderSession
temporary access token
notification reminder
```

---

# 9. REDACTED

Структурная историческая запись сохраняется, но один или несколько sensitive values больше не должны быть доступны.

---

# 10. DELETED

Logical or physical removal according to domain policy.

---

# 11. PURGED

Physical deletion из primary storage и запланированное удаление из derived/temporary stores в соответствии с retention process.

---

# 12. RET-INV-001 — Archive ≠ Delete

Archive не означает уничтожение строки или бинарного файла.

---

# 13. RET-INV-002 — Superseded ≠ Obsolete Data to Destroy

Старая Revision — это исторический record.

---

# 14. RET-INV-003 — Historical ≠ Public Forever

Исторический Snapshot может быть сохранён, но доступ к нему всё ещё контролируется visibility/privacy policy.

---

# 15. RET-INV-004 — Derived Data May Be Deleted Aggressively

Если derived data полностью rebuildable и не нужен для historical evidence:

```text id="rp177i"
cache
search index
temporary derivatives
```

можно очищать намного агрессивнее.

---

# 16. RET-INV-005 — Source Data Requires Dependency Check

Hard Delete Source/entity запрещён без анализа:

```text id="y9teor"
active references
historical references
artifacts
audit/provenance
legal/privacy requirements
```

---

# 17. RET-INV-006 — Redaction Is Not History Rewrite

Redaction может скрыть payload, но не должна фальсифицировать событие.

---

# 18. RET-INV-007 — No Orphan Binary

Удаление DB metadata и binary must be coordinated.

---

# 19. RET-INV-008 — No Orphan Historical Reference

Нельзя удалить Source, если immutable historical record требует exact reference и snapshot does not preserve enough information.

---

# 20. RET-INV-009 — Retention Does Not Rely on Cache

Cache eviction не является deletion workflow.

---

# 21. RET-INV-010 — Backup Is Part of Deletion Semantics

Physical deletion из primary DB/storage не означает мгновенное исчезновение из backup sets.

Backup retention must be documented and bounded.

---

# 22. Retention classes

Canonical logical classes:

```text id="6u46t8"
R0 — EPHEMERAL
R1 — SHORT_OPERATIONAL
R2 — ACTIVE_BUSINESS
R3 — PROFESSIONAL_HISTORY
R4 — SECURITY_AUDIT
R5 — CONFIGURATION_HISTORY
R6 — DERIVED_REBUILDABLE
```

---

# 23. R0 — EPHEMERAL

Examples:

```text id="epyc19"
upload staging files
temporary render files
temporary provider files
expired browser/session material
```

Short cleanup window.

---

# 24. R1 — SHORT_OPERATIONAL

Examples:

```text id="z8tmq6"
expired Builder Sessions
old job attempts
provider transient metadata
temporary notification delivery details
```

Retained long enough for diagnostics/user continuation according to policy.

---

# 25. R2 — ACTIVE_BUSINESS

Examples:

```text id="3us6eh"
Feedback
Casting
Opportunity
current Questionnaire Draft
Social Post
```

Retained while operationally relevant plus defined post-completion period.

---

# 26. R3 — PROFESSIONAL_HISTORY

Examples:

```text id="n8na97"
QuestionnaireRevision
BuilderGenerationSnapshot if retained
EmotionalGridRevision
Project/Role historical provenance
published artifacts
```

Long-lived unless privacy/legal rule requires redaction/deletion.

---

# 27. R4 — SECURITY_AUDIT

Examples:

```text id="0w91bz"
AuditEvent
security configuration changes
significant access events
```

Retention dictated by security/audit policy.

---

# 28. R5 — CONFIGURATION_HISTORY

Examples:

```text id="hx0djo"
ThemeRevision
approved notification template history
published configuration snapshots
```

Long-lived while useful for rollback/traceability.

---

# 29. R6 — DERIVED_REBUILDABLE

Examples:

```text id="wx8rx2"
Search documents
cache
thumbnails
current projection caches
link health state
temporary QR cache
```

Can be deleted/rebuilt.

---

# 30. Exact durations

DOC-094 defines **classes and semantics**, not fixed legal time periods.

Exact durations SHALL be configuration/operations policy established in DOC-19x, subject to applicable requirements.

No arbitrary “forever” retention should be assumed.

---

# 31. Archive policy

Archive is default removal method for professional content.

---

# 32. Archivable domains

At least:

```text id="zewux5"
PortfolioItem
EmotionalSession
EmotionalGrid
Project
Role
Training
Achievement
ProfessionalLink
Contact
QuestionnaireDefinition
Theme
```

where lifecycle permits.

---

# 33. Archive effects

Canonical:

```text id="o0yyij"
ARCHIVE
   ↓
remove from current Public Projection
remove from Builder eligibility
remove from new Questionnaire selection
remove from Public Search
invalidate current cache
retain historical references
retain Admin/history access
```

---

# 34. Archive does not

```text id="ca0ndq"
delete MediaAsset
rewrite Revision
delete Audit
delete related Opportunity
```

---

# 35. Restore from archive

If supported:

```text id="y4u0av"
Archived → Draft/Active
```

requires explicit transition and validation.

---

# 36. Restore is not automatic republish

Restored professional content SHOULD normally return to:

```text id="8tzasc"
draft/non-public
```

unless explicit domain rule says otherwise.

---

# 37. Why

Avoid accidental republishing after long archive.

---

# 38. Questionnaire definitions

Archived QuestionnaireDefinition:

- removed from current public chooser;
- no longer used for new ordinary publication;
- existing Revisions remain.

---

# 39. Superseded Questionnaire Revision

Never archived simply because new Revision exists.

State is historical/superseded.

---

# 40. Revision retention

Published QuestionnaireRevision SHOULD be long-lived because it supports:

```text id="8xhgnr"
historical PDF
casting evidence
provenance
rollback pointer
audit
```

---

# 41. Historical revision deletion

Exceptional.

Requires:

```text id="8bsxvv"
retention policy permits
no legal/business hold
access/artifact implications reviewed
Audit preserved
```

---

# 42. Revision mutation prohibited

Deletion must not be implemented as:

```text id="e4tldn"
UPDATE revision content_snapshot = {}
```

unless performing explicit redaction process.

---

# 43. Redaction marker

If immutable payload requires privacy redaction:

system MAY create explicit metadata such as:

```text id="3zxcd8"
redacted_at
redaction_reason_code
redacted_fields
```

outside immutable semantic snapshot or through controlled redaction envelope.

---

# 44. Preferred redaction architecture

```text id="166acj"
IMMUTABLE HISTORICAL RECORD
        +
REDACTION POLICY/OVERLAY
        ↓
AUTHORIZED HISTORICAL PROJECTION
```

---

# 45. When overlay is insufficient

If retained payload itself must be physically removed:

perform controlled destructive redaction with Audit evidence and checksum/history metadata retained where permissible.

---

# 46. RET-RED-001

After redaction, no ordinary projection/search/cache/document generation may re-expose removed value.

---

# 47. Historical PDF redaction

If distributed/generated PDF contains now-prohibited sensitive value:

system may:

```text id="1zv0ue"
revoke delivery access
remove server-hosted artifact
generate redacted replacement if policy allows
```

It cannot erase copies already downloaded externally.

---

# 48. Important distinction

```text id="s2zuh2"
Server retention control
!=
remote recipient deletion guarantee
```

---

# 49. Prepared Questionnaire historical artifacts

Artifact retention SHOULD follow Revision retention unless:

- artifact is fully reproducible;
- distributed historical fidelity does not require original renderer bytes;
- retention policy permits regeneration.

---

# 50. Recommended baseline

Retain at least:

```text id="zptboz"
Revision Snapshot
artifact metadata
checksum
renderer/template version
```

and retain important published PDF binary where storage cost is reasonable.

---

# 51. Builder Generation Snapshot retention

Builder generated outputs are temporary-professional documents.

---

# 52. Retention consideration

Do not assume every anonymous Builder session must be stored forever.

---

# 53. Recommended separation

```text id="a3h25k"
BuilderSession = short-lived
BuilderGenerationSnapshot = longer if document actually generated
BuilderArtifact = bounded retention
```

Exact duration operations config.

---

# 54. Builder Session expiration

At:

```text id="14ie9a"
expires_at
```

session becomes unusable.

---

# 55. Expired Builder Session

May remain briefly for:

```text id="5umx6k"
diagnostics
artifact provenance
Feedback linkage
```

then purge according to retention.

---

# 56. Linked Builder Session

If Feedback references session:

cleanup MUST preserve enough provenance.

Options:

```text id="6zhjw5"
retain session summary
retain GenerationSnapshot
detach with frozen provenance
```

but never leave broken critical business history.

---

# 57. Builder temporary labels

Should not outlive their useful business context unnecessarily.

---

# 58. Media deletion doctrine

Media requires strongest dependency analysis.

---

# 59. Media reference classes

Before deletion classify references:

```text id="7xe0sa"
ACTIVE_CURRENT
DRAFT
HISTORICAL
OPERATIONAL
DERIVED
```

---

# 60. ACTIVE_CURRENT

Examples:

```text id="3cs4pz"
published Portfolio
current EmotionalSession
current Project role
current Questionnaire Draft
```

Blocks hard delete.

---

# 61. DRAFT

Still blocks hard delete unless relation removed first.

---

# 62. HISTORICAL

Examples:

```text id="xox0ht"
QuestionnaireRevision
GridRevision source provenance
published historical document
```

Typically blocks hard delete of original unless history has self-contained immutable artifact and policy explicitly permits source purge.

---

# 63. OPERATIONAL

Examples:

```text id="vt8axx"
Casting attachment
Feedback attachment
Social approved post
```

Retention depends on owning domain.

---

# 64. DERIVED

Does not block source deletion by itself.

Example:

```text id="j5rn35"
thumbnail
```

---

# 65. RET-MED-001 — Relationship Removal ≠ Binary Delete

Removing:

```text id="p2jcm5"
PortfolioItem → MediaAsset
```

does not delete MediaAsset.

---

# 66. RET-MED-002 — Replacement

Replacing professional image:

```text id="2yknz4"
create new MediaAsset
switch current relation
retain old asset if historical refs exist
```

---

# 67. Media archive

MediaAsset may be archived when no longer intended for new use but retained for history.

---

# 68. Media hard delete eligibility

Requires all:

```text id="oz3sx2"
no current professional relation
no Draft relation
no retained historical dependency requiring original
no active operational dependency
retention period satisfied
no legal/privacy hold
backup/purge policy acknowledged
```

---

# 69. Media dependency report

Admin destructive workflow SHOULD expose:

```text id="l6zeke"
where used now
where used historically
which artifacts depend on it
why deletion is blocked
```

---

# 70. Binary purge transaction

DB and storage cannot be perfectly atomic.

Use staged deletion:

```text id="8fopui"
mark PURGE_PENDING
↓
block new use
↓
delete binary
↓
verify
↓
finalize metadata state
```

---

# 71. Storage delete failure

Do not mark fully purged while binary still exists unexpectedly.

---

# 72. DB delete first anti-pattern

Avoid:

```text id="uihp31"
DELETE media_assets
→ later maybe delete file
```

because orphan binary loses ownership metadata.

---

# 73. Recommended tombstone

For physically deleted MediaAsset, application MAY retain minimal tombstone:

```text id="zfszlq"
id
checksum
deleted_at
reason
```

where useful for audit/provenance.

---

# 74. Media derivatives

Can normally be removed:

- when source archived;
- during processor upgrade;
- during storage cleanup;
- when stale;
- after artifact replacement.

They are rebuildable.

---

# 75. Emotional Grid artifacts

Historical Grid artifact retention follows GridRevision needs.

---

# 76. Grid Draft cleanup

Old abandoned drafts may be purged after bounded retention if:

```text id="1wxh78"
not current
not referenced
not needed for audit
```

---

# 77. Grid Revision

Retained as professional historical state.

---

# 78. Project/Role archive

Archive Project normally archives/removes current public child context but MUST NOT cascade physical delete Roles/Media.

---

# 79. Project hard delete

Rare.

Requires dependency analysis:

```text id="x19jn2"
Roles
Achievements
Questionnaire history
Search
Opportunity provenance
Audit
```

---

# 80. Booked provenance

Project created from Opportunity SHOULD retain source relationship even if Opportunity later becomes closed historical record.

---

# 81. Training/Achievement deletion

Archive preferred.

Hard delete allowed only under ordinary dependency/retention checks.

---

# 82. Skill dictionary deletion

If referenced by ActorSkill:

do not delete dictionary row.

Use:

```text id="c52082"
active=false
```

for deprecated reference values.

---

# 83. Language dictionary

Same.

---

# 84. Contact deletion

Contacts can be highly privacy-sensitive.

---

# 85. Contact archive

Default professional removal action.

Immediately removes current exposure according to visibility model.

---

# 86. Contact hard delete

Potentially justified for privacy.

Must evaluate:

```text id="fzzpji"
current Questionnaire Draft
historical Revision
Builder Snapshot
Feedback business relationship
Audit
```

---

# 87. Historical Contact problem

If old QuestionnaireRevision contains frozen Contact value:

deleting current Contact row does not remove the frozen historical value.

---

# 88. Therefore

Privacy deletion request involving Contact MUST inspect historical snapshots/artifacts.

---

# 89. Contact redaction propagation

Potential targets:

```text id="6lowqn"
historical Questionnaire projections
stored PDF artifacts
Search
cache
AI snapshots
Analytics if improperly stored
logs
```

---

# 90. Professional Link archive

Immediately prevents new current use.

Historical Revisions retain old URL.

---

# 91. URL safety exception

If an old URL becomes dangerous/compromised:

historical artifact access policy MAY suppress hyperlink/QR even while preserving historical source record.

---

# 92. QR artifacts

Derived.

May be purged whenever no retained artifact depends on exact binary.

Regenerated from exact URL/settings.

---

# 93. Feedback retention

Feedback is business operational data.

---

# 94. Feedback archive/close

Workflow completion does not imply deletion.

---

# 95. Feedback contains PII

Examples:

```text id="zv7s0k"
sender name
email
phone
message
attachments
```

requires stricter retention than public profile content.

---

# 96. Feedback attachment retention

Should not exceed business need without reason.

---

# 97. Attachment purge

May retain:

```text id="oi57rz"
attachment metadata
checksum
filename
purged_at
```

while deleting binary if policy permits.

---

# 98. Feedback notes

Internal notes follow Feedback/business retention.

---

# 99. Feedback hard delete

Must consider:

```text id="uf35pp"
linked Casting
Opportunity provenance
Audit
Notification
```

---

# 100. Preferred privacy deletion

If business provenance requires record existence:

redact sender/private content while preserving minimal event structure.

---

# 101. Example redacted Feedback

Could preserve:

```text id="7eih9r"
Feedback ID
type
created_at
linked Casting ID
workflow outcome
```

while removing:

```text id="hs9s52"
sender name
email
phone
message
attachments
```

if policy requires.

---

# 102. Casting retention

Casting source may contain third-party/confidential material.

---

# 103. Casting archive/close

Operationally closed Castings move to historical state.

---

# 104. Casting Source retention

Bounded according to business need, privacy and contractual requirements.

---

# 105. Casting AI outputs

Should not outlive underlying source context indefinitely without purpose.

---

# 106. Casting AnalysisRevision deletion

Can be retained for professional decision traceability while source active/historical.

If source must be privacy-purged:

AI snapshot must be included in redaction scope.

---

# 107. RET-PRI-001 — AI Snapshot Is a Data Copy

Deleting private Source but leaving full same data in AI snapshot does not satisfy privacy deletion.

---

# 108. Casting Requirements

Human-confirmed requirement may retain business significance even if raw source later purged.

Policy may preserve normalized requirement while deleting original file/text, provided this is allowed and provenance remains appropriate.

---

# 109. Opportunity retention

Opportunity pipeline is business history.

Terminal stage does not imply delete.

---

# 110. Closed Opportunity

Retain:

```text id="sfjn4u"
stage history
important dates
related Casting/Project provenance
```

according to professional/business history policy.

---

# 111. Opportunity hard delete

Rare; redaction/minimal retention preferred when linked to booked professional history.

---

# 112. Notification retention

Notification is supporting operational record.

---

# 113. In-App notifications

Can have shorter retention than source Feedback/Casting.

---

# 114. Delivery attempts

Retain long enough for:

```text id="yyfmo8"
provider diagnostics
delivery reconciliation
audit where relevant
```

then purge.

---

# 115. Notification payload snapshots

Private values should already be minimized.

Retention still bounded.

---

# 116. Provider IDs

May be retained longer than raw payload if useful for support.

---

# 117. BB Assistant retention

AIDraft is AI-generated professional draft history.

---

# 118. Generated vs applied

Applied AIDraft provenance may be useful longer than discarded experimental drafts.

---

# 119. Suggested classes

```text id="h0jhmx"
APPLIED → R3/R5-style longer history
GENERATED/EDITED → bounded
DISCARDED → shorter
FAILED → short operational
```

Exact durations later.

---

# 120. AI private context

`source_snapshot` can contain sensitive data.

Retention must follow most restrictive relevant Source classification.

---

# 121. RET-PRI-002 — AI Output Retention Cannot Exceed Sensitive Source Without Review

If a BB Casting Response contains private Casting details, its retention follows that context.

---

# 122. Theme AI

Theme Proposal contains little professional PII.

Discarded proposals may be purged relatively aggressively.

Published ThemeRevision retained long-term for rollback/history.

---

# 123. VOP

Observations are operational.

---

# 124. VOP resolved records

May be retained for diagnostics/quality then purged/aggregated.

---

# 125. VOP evidence

If evidence contains sensitive source values, retention follows those values.

Prefer IDs/status codes over copied content.

---

# 126. Social Publishing

Published SocialPost metadata may be retained as business/content history.

---

# 127. Provider credentials

Not governed by ordinary retention table data.

On disconnect:

```text id="vbc47z"
revoke/delete secret
retain non-secret connection history if useful
```

---

# 128. Social drafts

Unpublished abandoned Drafts may have bounded retention.

---

# 129. Social attempts

Operational short/medium retention.

---

# 130. Support tickets

May contain private content.

Retention bounded according to support/security need.

---

# 131. Help articles

Managed content; archive/history like standard content.

---

# 132. Audit retention

Audit is append-oriented.

---

# 133. Audit must not contain unnecessary PII

This reduces redaction conflicts.

---

# 134. Audit privacy deletion

If audit contains prohibited private value, redaction may be required while preserving:

```text id="947ebq"
who
what action
when
which entity
```

where permitted.

---

# 135. Audit hard delete

Exceptional and controlled.

---

# 136. Outbox retention

Processed Outbox events do not need indefinite retention.

---

# 137. Recommended lifecycle

```text id="e3wsj4"
pending
→ processed
→ retained for operational window
→ purge
```

---

# 138. Unprocessed Outbox

Must never be purged by age alone while still required for reliable processing.

---

# 139. Background Jobs

Completed job records may be purged after operational retention.

---

# 140. Failed jobs

Retain longer if needed for incident diagnostics.

---

# 141. Job attempts

Shorter retention than business entities.

---

# 142. Job payload private data

Should already contain minimal references, reducing retention concerns.

---

# 143. Search

Fully rebuildable.

---

# 144. Archived/hidden entities

Search rows SHOULD be deleted promptly.

---

# 145. Search full purge

Safe at any time if rebuild capability exists.

---

# 146. Cache

No business retention guarantee.

Can be flushed entirely.

---

# 147. Analytics

Analytics retention must be privacy-minimized.

---

# 148. Raw analytics events

May have bounded retention.

Long-term reporting SHOULD prefer aggregates where possible.

---

# 149. Analytics deletion

Deletion of raw events should not affect business entities.

---

# 150. Analytics privacy request

If analytics includes linkable personal/session identifiers, privacy deletion/aggregation strategy must include them.

---

# 151. Logs

Operational logs require strict bounded retention.

---

# 152. Never indefinite logs

Especially where logs can contain:

```text id="hknn2a"
IP
request metadata
provider errors
security events
```

---

# 153. Security logs

May have separate longer retention from general application logs.

---

# 154. Temporary files

Upload staging/render temp directories MUST have automatic cleanup.

---

# 155. RET-TMP-001

Temporary files should carry:

```text id="w390th"
creation timestamp
owner/job context
cleanup eligibility
```

or be stored in managed temp namespace.

---

# 156. Crash residue

Cleanup process must handle files left after worker/process crash.

---

# 157. Active temp file protection

Cleanup must not remove temp file still owned by active lease/job.

---

# 158. Orphan definition

Orphan may be:

```text id="i553sm"
binary without DB metadata
DB derivative row without binary
relation pointing to missing Source
expired temp file
stale job lock
```

---

# 159. Orphan scanner

System/VOP MAY periodically reconcile.

---

# 160. Orphan actions

Classify:

```text id="nrb8q2"
SAFE_TO_DELETE
REBUILDABLE
REQUIRES_REPAIR
REQUIRES_HUMAN_REVIEW
```

---

# 161. Never blindly delete unknown orphan

If provenance uncertain:

Human review.

---

# 162. Orphan Media binary

If file exists but DB record absent:

quarantine before purge if potentially professional material.

---

# 163. Missing derivative

Rebuild.

---

# 164. Missing original

Critical.

Do not silently substitute another current file.

---

# 165. Missing historical artifact

May regenerate from exact Revision if all source data still available and renderer compatibility permits.

---

# 166. Missing original + retained historical PDF

Historical PDF can remain, but source integrity issue must be surfaced.

---

# 167. Deletion workflow

Canonical destructive workflow:

```text id="0n6wjj"
REQUEST DELETE/PURGE
      ↓
AUTHORIZATION
      ↓
DEPENDENCY ANALYSIS
      ↓
RETENTION / HOLD CHECK
      ↓
VISIBILITY IMMEDIATE REVOCATION
      ↓
MARK DELETE/PURGE PENDING
      ↓
ASYNC PHYSICAL CLEANUP
      ↓
VERIFY
      ↓
FINALIZE
      ↓
AUDIT
```

---

# 168. Why asynchronous purge

Binary/media/history cleanup may be slow and should not hold HTTP/DB transactions.

---

# 169. Delete request states

Recommended:

```text id="a1uqom"
REQUESTED
BLOCKED
APPROVED
PURGE_PENDING
PURGING
PURGED
FAILED
```

for destructive workflows where needed.

---

# 170. Simple archive does not need DeleteRequest entity

Only complex/destructive purge.

---

# 171. RET-DEL-001 — Immediate Exposure Revocation

Once authorized privacy/destructive delete starts:

new public/token access SHOULD be blocked before physical cleanup completes.

---

# 172. Dependency analysis result

Should enumerate:

```text id="nu4oqj"
current references
historical references
private references
derived artifacts
pending jobs
legal/business hold
```

---

# 173. Delete blocker

Example:

```text id="ez8f59"
Cannot purge MediaAsset:
used by QuestionnaireRevision 8
```

---

# 174. User remediation

Offer:

```text id="3z2wzr"
archive instead
remove current relation
review historical dependency
redact historical output
```

as appropriate.

---

# 175. Deletion authorization

Hard Delete/Purge is higher-risk than Archive.

Requires elevated explicit Admin action.

Detailed auth policy DOC-140+.

---

# 176. Confirmation UX

Destructive action MUST state:

```text id="pvdilz"
what will disappear
what will remain
which history is affected
whether operation is reversible
```

---

# 177. No vague button

Avoid:

```text id="kgacgx"
Delete
```

when meaning is Archive.

Use:

```text id="ugiu2h"
Архивировать
Удалить безвозвратно
Удалить файл после проверки зависимостей
```

---

# 178. Restore capability

Archive may be reversible.

Purge generally is not, except from backups during their retention period.

---

# 179. Backup doctrine

Backups contain historical copies independently of live DB.

---

# 180. Backup retention

Must be time-bounded and documented.

---

# 181. Privacy deletion and backups

Practical policy:

```text id="g310vu"
remove from live systems
prevent re-exposure
allow old backup to age out
if backup restored before expiry, reapply deletion/redaction ledger
```

---

# 182. Deletion ledger

For privacy-critical purges, system SHOULD retain minimal deletion marker:

```text id="ph8a6t"
entity identity/hash
deletion scope
deleted_at
reason code
```

so restoration does not resurrect data silently.

---

# 183. RET-DEL-002 — Restore Must Reapply Deletions

Restore procedure MUST reconcile against deletion/redaction ledger created after backup snapshot if available.

---

# 184. Backup restore

After restore:

```text id="o7i3zq"
reapply redactions/deletions
reconcile binaries
rebuild Search
clear/rebuild cache
resume jobs carefully
```

---

# 185. Do not resend historical side effects

Restore MUST NOT blindly replay:

```text id="e0c2x2"
notifications
social posts
external messages
```

---

# 186. Legal/business hold

Architecture should support future concept:

```text id="5bjsrl"
retention_hold
```

that blocks automated purge.

---

# 187. Hold is not Public visibility

Held data may still be inaccessible publicly.

---

# 188. Privacy redaction workflow

Canonical:

```text id="9taw2w"
REQUEST
  ↓
IDENTIFY SUBJECT/SCOPE
  ↓
DISCOVER COPIES
  ↓
BLOCK NEW EXPOSURE
  ↓
REDACT/PURGE SOURCE
  ↓
REDACT HISTORICAL ACCESS/ARTIFACTS
  ↓
PURGE DERIVED COPIES
  ↓
AUDIT
```

---

# 189. Copy discovery

Must consider:

```text id="eip342"
Source tables
Questionnaire snapshots
Builder snapshots
AI source snapshots
Feedback/Casting copies
PDF artifacts
QR where target embeds sensitive data
Search
Cache
Analytics
Logs
Backups
```

---

# 190. RET-RED-002 — Search/Cache Cleanup Mandatory

Redaction incomplete while stale derived systems can still expose data.

---

# 191. PDF redaction caveat

Regenerated redacted PDF must bind either:

- redacted access overlay; or
- explicit redacted historical rendering version.

Never silently alter original Revision semantics without provenance.

---

# 192. AI redaction

AI-generated Draft text may repeat sensitive Source facts.

Subject deletion scope must inspect AI outputs.

---

# 193. Prompt/provider copies

External AI provider retention depends on provider policy and production configuration.

This must be reviewed in security/AI operations.

---

# 194. Notification provider copies

WhatsApp/Email provider retains delivered message according to external platform rules.

Application should minimize content precisely because remote deletion may not be controllable.

---

# 195. Social publication deletion

Deleting local SocialPost does not delete already published Instagram/TikTok post automatically.

---

# 196. External deletion action

Must be explicit:

```text id="88dh6w"
Delete local record
Delete remote post
Both
```

according to provider capability.

---

# 197. RET-INV-011 — Local Delete ≠ External Delete

Applies to:

```text id="rlm8vt"
Social
Email
WhatsApp
external URLs
```

---

# 198. Public Website content removal

Archive/unpublish must disappear promptly from:

```text id="2dvj7c"
Public page
Search
current cache
navigation
SEO sitemap where relevant
```

---

# 199. SEO removal

Search-engine cached copies are external and cannot be guaranteed immediate deletion.

System should remove source page and update SEO signals.

---

# 200. Token-scoped resource expiration

Expired token/grant can be purged after bounded history retention.

---

# 201. Access grant history

Minimal issuance/revocation metadata MAY be retained for security.

Raw secret token should not.

---

# 202. Idempotency records

Technical retention only as long as duplicate request risk exists.

---

# 203. After expiry

Can be purged.

---

# 204. Delete request idempotency

Repeated destructive request should not start duplicate purge workers.

---

# 205. Pending background jobs

Before Source purge, locate jobs referencing it.

---

# 206. Job behavior

Jobs may be:

```text id="nf5tej"
cancelled
allowed to finish
invalidated
```

depending semantics.

---

# 207. Example

Pending thumbnail generation for Media scheduled for purge:

```text id="o7j89x"
cancel/skip
```

---

# 208. Pending historical PDF render

If exact Revision remains valid:

may finish even if current Questionnaire is archived.

---

# 209. Job source deletion

Worker encountering deleted Source must fail/skip with explicit terminal code, not query current substitute.

---

# 210. Search cleanup

Hard delete current source must enqueue/immediately trigger search deletion.

---

# 211. Cache cleanup

Exact key invalidation where possible.

Full cache flush remains safe fallback.

---

# 212. Analytics referential integrity

Analytics may retain event with deleted entity identifier.

If privacy-sensitive, identifier may require anonymization/purge.

---

# 213. Aggregate analytics

Can usually remain if no longer attributable to person/private source.

---

# 214. Audit tombstone

Audit can state:

```text id="k7agfv"
Contact deleted/redacted
```

without retaining deleted Contact value.

---

# 215. Retention automation

Scheduler/worker may run routine cleanup.

---

# 216. Cleanup classes

```text id="ohgkw8"
TEMP_FILES
EXPIRED_BUILDER_SESSIONS
OLD_JOB_ATTEMPTS
PROCESSED_OUTBOX
EXPIRED_IDEMPOTENCY
STALE_DERIVATIVES
OLD_LOGS
OLD_ANALYTICS
DISCARDED_AI_DRAFTS
```

subject to policy.

---

# 217. Cleanup job safety

Each cleanup handler MUST:

```text id="6uhmsh"
select only eligible rows
respect hold
batch work
be idempotent
avoid long locks
record failures
```

---

# 218. Retention cutoff

Always calculated from authoritative timestamps/status.

Not filename age alone.

---

# 219. Cleanup batching

Large cleanup must run in bounded batches.

---

# 220. Vacuum/database maintenance

Physical PostgreSQL reclamation is operations concern.

Deletion success does not require immediate disk block shrink.

---

# 221. Retention observability

Minimum metrics:

```text id="hf9a4e"
archive count
purge backlog
purge failures
expired temp backlog
orphan count
redaction backlog
storage reclaimed
blocked deletes
```

---

# 222. VOP integration

VOP may surface:

```text id="9xhl7p"
orphan binary
expired Builder sessions accumulating
purge job failing
old private attachments beyond retention
stale Search after redaction
```

---

# 223. VOP AUTO-4

May safely:

```text id="lp631z"
clear cache
delete expired temp files
delete rebuildable stale derivatives
rebuild Search
retry approved purge
```

---

# 224. VOP cannot automatically

```text id="5d3g8v"
hard-delete professional history
redact Contact
delete Casting
delete QuestionnaireRevision
```

without explicit governed policy/Human approval.

---

# 225. Retention configuration

Operational retention values SHOULD be centrally configurable/versioned.

---

# 226. No per-module magic numbers

Avoid:

```text id="z3sz5m"
Builder cleanup = 7 days hard-coded
AI cleanup = 30 days elsewhere
```

without documented policy.

---

# 227. Policy names

Prefer semantic config:

```text id="0j33f3"
builder_session_retention
discarded_ai_draft_retention
job_attempt_retention
processed_outbox_retention
```

---

# 228. Security of deleted content

Deletion endpoints/actions must prevent:

```text id="yaol5m"
IDOR
unauthorized purge
mass delete
CSRF where applicable
```

detailed later.

---

# 229. Deletion audit

Significant destructive actions SHOULD record:

```text id="kkxmga"
who
what
scope
reason
when
dependency summary
result
```

without preserving prohibited deleted values.

---

# 230. Purge reason codes

Recommended categories:

```text id="pkb32e"
USER_ADMIN_REQUEST
PRIVACY_REQUEST
RETENTION_EXPIRY
DUPLICATE
MIGRATION_CLEANUP
SECURITY_RESPONSE
ORPHAN_CLEANUP
```

---

# 231. Archive reason

Optional but useful for high-value professional entities.

---

# 232. RET-AP-001

**Delete button actually hard-deletes ordinary Project**

---

# 233. RET-AP-002

**Archive PortfolioItem cascades MediaAsset deletion**

---

# 234. RET-AP-003

**New Questionnaire Revision deletes old Revision**

---

# 235. RET-AP-004

**Deleting current Contact assumed to remove value from historical PDF**

---

# 236. RET-AP-005

**Privacy deletion removes Source but leaves full AI Snapshot**

---

# 237. RET-AP-006

**Privacy deletion leaves stale Search hit/cache**

---

# 238. RET-AP-007

**DB row deleted before external binary with no tombstone/retry**

---

# 239. RET-AP-008

**Unknown orphan binary immediately deleted**

---

# 240. RET-AP-009

**Cache eviction treated as data deletion**

---

# 241. RET-AP-010

**All archived entities physically deleted nightly**

---

# 242. RET-AP-011

**Processed Outbox retained forever without purpose**

---

# 243. RET-AP-012

**Unprocessed Outbox purged because it is old**

---

# 244. RET-AP-013

**Expired Builder Session remains usable because cleanup delayed**

---

# 245. RET-AP-014

**Builder Session kept forever just because PDF once generated**

---

# 246. RET-AP-015

**Notification history retains full private Feedback payload indefinitely**

---

# 247. RET-AP-016

**Social local delete assumed to remove remote post**

---

# 248. RET-AP-017

**Backup restore resurrects previously privacy-deleted Contact**

---

# 249. RET-AP-018

**Audit stores deleted secret/value “for safety”**

---

# 250. RET-AP-019

**Historical PDF regenerated using current Profile during cleanup**

---

# 251. RET-AP-020

**VOP automatically purges professional source based on age**

---

# 252. RET-AP-021

**Archive automatically republishes when restored**

---

# 253. RET-AP-022

**Legal/business hold ignored by automated retention job**

---

# 254. RET-AP-023

**Hard deletion has no dependency analysis**

---

# 255. RET-AP-024

**Temporary file cleanup deletes active worker temp file**

---

# 256. RET-AP-025

**Analytics raw PII kept longer than source “because analytics is separate”**

---

# 257. Retention matrix — baseline classes

| Data | Class | Default removal |
|---|---|---|
| Profile current | R2/R3 | Archive |
| Portfolio | R3 | Archive |
| Media original | R3 | Dependency-aware archive/purge |
| Derivatives | R6 | Rebuild/delete |
| Emotional Grid Revision | R3 | Retain |
| Questionnaire Revision | R3 | Retain |
| Builder Session | R1 | Expire/purge |
| Builder Snapshot | R2/R3 bounded | Retain then purge |
| Feedback | R2 | Close/archive → bounded retention |
| Feedback Attachment | R2 private | Bounded purge |
| Casting | R2/R3 | Close/archive |
| Casting Source | R2 private | Bounded/private policy |
| Opportunity | R3 business history | Retain |
| AIDraft | R1/R3 depending applied status | Status-based |
| Theme Revision | R5 | Retain |
| Notification | R1/R2 | Bounded |
| Delivery Attempts | R1 | Purge |
| Audit | R4 | Long controlled |
| Outbox processed | R1 | Purge |
| Jobs | R1 | Purge |
| Search | R6 | Delete/rebuild |
| Cache | R6 | Flush |
| Analytics raw | R1/R2 | Bounded/aggregate |
| Logs | R1/R4 | Bounded |
| Temp files | R0 | Fast cleanup |

---

# 258. Archive matrix

| Entity | Archive preferred? | Restore possible? |
|---|---:|---:|
| PortfolioItem | Yes | Yes |
| Project | Yes | Yes |
| Role | Yes | Yes |
| Training | Yes | Yes |
| Achievement | Yes | Yes |
| Contact | Yes | Yes |
| ProfessionalLink | Yes | Yes |
| EmotionalSession | Yes | Yes |
| QuestionnaireDefinition | Yes | Yes |
| MediaAsset | Yes | Controlled |
| Casting | Close/archive semantics | Possibly |
| Opportunity | Terminal stage, not ordinary archive | Controlled |

---

# 259. Hard-delete blockers

Typical blockers:

```text id="8vn47y"
current reference
Draft reference
historical Revision dependency
business provenance
legal/business hold
pending side effect
unknown external state
```

---

# 260. Privacy redaction matrix

| Copy | Must inspect? |
|---|---:|
| Current Source | Yes |
| Historical Snapshot | Yes |
| AI Snapshot | Yes |
| Search | Yes |
| Cache | Yes |
| PDF Artifact | Yes |
| Notification payload | Yes |
| Analytics | Yes |
| Logs | Yes |
| Backup policy | Yes |

---

# 261. Media purge matrix

| Dependency | Purge allowed? |
|---|---:|
| Current Portfolio | No |
| Questionnaire Draft | No |
| Published Revision requiring source | No/default |
| Rebuildable thumbnail only | Yes after relation cleanup |
| No references | Yes after retention |
| Unknown orphan | Human review first |

---

# 262. E2E-RET-001 — Archive Portfolio

Archive PortfolioItem.

Expected:

```text id="vkwsqm"
Public absent
Builder absent
MediaAsset retained
historical Questionnaire unaffected
```

---

# 263. E2E-RET-002 — Restore Portfolio

Restore archived PortfolioItem.

Expected:

```text id="kexqjw"
does not become Public automatically unless explicit publish restored
```

---

# 264. E2E-RET-003 — Superseded Questionnaire

Publish R2 after R1.

R1 remains stored and immutable.

---

# 265. E2E-RET-004 — Delete Questionnaire Definition

Hard delete request while R1/R2 retained.

Expected:

```text id="bm9qp9"
blocked or converted to archive
```

according to policy.

---

# 266. E2E-RET-005 — Media current dependency

Try purge MediaAsset used by current Portfolio.

Blocked.

---

# 267. E2E-RET-006 — Media historical dependency

Remove current relation but old QuestionnaireRevision references source.

Hard purge blocked by default.

---

# 268. E2E-RET-007 — Derivative purge

Delete thumbnail derivative.

Source remains; thumbnail regenerates.

---

# 269. E2E-RET-008 — Binary delete failure

Storage deletion fails.

Entity remains `PURGE_PENDING/FAILED`, not `PURGED`.

---

# 270. E2E-RET-009 — Orphan binary

Reconciliation finds unknown professional-looking file with no DB row.

Quarantine/review; no automatic immediate deletion.

---

# 271. E2E-RET-010 — Expired Builder

Session passes `expires_at`.

Request-time access rejected before cleanup worker executes.

---

# 272. E2E-RET-011 — Builder cleanup

Expired unlinked Session purged after retention.

No Source/Profile changes.

---

# 273. E2E-RET-012 — Builder linked Feedback

Session tied to Feedback.

Cleanup preserves required provenance/snapshot before removing transient session details.

---

# 274. E2E-RET-013 — Feedback privacy redaction

Redact sender phone/email.

Current Admin/Public exposures update.

Related notification/search/cache copies cannot retain values.

---

# 275. E2E-RET-014 — Feedback attachment purge

Attachment binary removed after retention.

Feedback business record remains with safe attachment metadata if policy requires.

---

# 276. E2E-RET-015 — Casting Source privacy

Raw Casting source purged.

AI snapshot containing same private raw source is also processed under redaction scope.

---

# 277. E2E-RET-016 — Opportunity history

Closed-not-selected Opportunity retained with stage history.

No automatic hard delete on closure.

---

# 278. E2E-RET-017 — Booked Project provenance

Booked Opportunity remains linked to created Project history.

Archiving Project does not destroy Opportunity.

---

# 279. E2E-RET-018 — AIDraft policy

Discarded old AIDraft purged after configured retention.

Applied AIDraft provenance retained longer.

---

# 280. E2E-RET-019 — Theme Proposal

Discard old un-applied ThemeProposal.

Published ThemeRevision remains.

---

# 281. E2E-RET-020 — Notification cleanup

Delete old NotificationDeliveryAttempts.

Source Feedback and Notification business record remain as policy specifies.

---

# 282. E2E-RET-021 — Outbox safety

Old but unprocessed Outbox event is NOT purged by retention worker.

---

# 283. E2E-RET-022 — Processed Outbox

Processed Outbox event older than cutoff is safely purged.

---

# 284. E2E-RET-023 — Search purge

Delete all search rows.

Search rebuild succeeds; Source untouched.

---

# 285. E2E-RET-024 — Cache purge

Full cache flush causes no business/history loss.

---

# 286. E2E-RET-025 — Backup restore redaction

Restore backup predating privacy deletion.

Deletion ledger/reconciliation prevents deleted Contact from becoming publicly accessible again.

---

# 287. E2E-RET-026 — Historical privacy

Contact removed for privacy but old public PDF exists server-side.

Historical access is blocked/redacted according to policy; original current Contact is not resurrected.

---

# 288. E2E-RET-027 — Social local delete

Delete local draft/record.

Already-published remote social post remains until explicit remote deletion action.

---

# 289. E2E-RET-028 — Temp crash cleanup

Worker crashes leaving temp file.

Cleanup removes file after lease/age safety checks.

---

# 290. E2E-RET-029 — Active temp

Cleanup does not delete temp file associated with active valid worker lease.

---

# 291. E2E-RET-030 — Hold

Entity qualifies by age for purge but has retention hold.

Cleanup skips it.

---

# 292. E2E-RET-031 — Redaction Search

Sensitive value removed.

Public/Admin Search contexts no longer return deleted value according to their policy.

---

# 293. E2E-RET-032 — Redaction Cache

Cached payload containing sensitive value invalidated immediately.

---

# 294. E2E-RET-033 — AI private duplication

Delete private Contact used in old BB Draft source snapshot.

Retention/redaction processor discovers and handles copy according to policy.

---

# 295. E2E-RET-034 — Audit preservation

Privacy deletion removes Contact value but Audit still records that Contact was redacted, without retaining deleted value.

---

# 296. E2E-RET-035 — Hard delete idempotency

Submit same purge request twice.

Only one semantic purge workflow executes.

---

# 297. Retention traceability

Canonical:

```text id="s8xg1f"
DATA CLASS
   ↓
OWNER
   ↓
LIFECYCLE STATE
   ↓
RETENTION CLASS
   ↓
DEPENDENCY GRAPH
   ↓
ARCHIVE / REDACT / PURGE
   ↓
AUDIT
```

---

# 298. Media deletion traceability

```text id="wa58pg"
MediaAsset
   ↓
Dependency Resolver
   ├─ Current Relations
   ├─ Draft Relations
   ├─ Historical Revisions
   ├─ Artifacts
   └─ Operational Uses
         ↓
DELETE ELIGIBLE?
   ├─ No → Archive/Block
   └─ Yes → Purge Workflow
```

---

# 299. Privacy redaction traceability

```text id="ax3z8f"
Sensitive Source
    ↓
Current Projection Block
    ↓
Copy Discovery
    ├─ Snapshot
    ├─ AI
    ├─ PDF
    ├─ Search
    ├─ Cache
    ├─ Analytics
    └─ Logs
    ↓
Redact/Purge
    ↓
Audit + Deletion Ledger
```

---

# 300. Builder retention traceability

```text id="ze25jd"
BuilderSession
     ↓
expires
     ↓
Artifact/Feedback linked?
   ┌─────┴─────┐
   No          Yes
   ↓            ↓
short purge   retain required Snapshot/provenance
```

---

# 301. Restore traceability

```text id="mbxc4i"
BACKUP RESTORE
     ↓
DELETION/REDACTION LEDGER
     ↓
REAPPLY PRIVACY STATE
     ↓
RECONCILE STORAGE
     ↓
REBUILD SEARCH/CACHE
     ↓
RESUME SAFE OPERATIONS
```

---

# 302. Retention quality gate

Перед production implementation MUST быть определены:

- [ ] retention classes;
- [ ] exact configurable retention durations;
- [ ] archive-capable entities;
- [ ] archive restore behavior;
- [ ] hard-delete authorization;
- [ ] dependency resolver;
- [ ] historical blocker policy;
- [ ] media purge workflow;
- [ ] redaction workflow;
- [ ] privacy copy discovery;
- [ ] historical artifact policy;
- [ ] Builder expiration/cleanup;
- [ ] Feedback attachment retention;
- [ ] Casting source retention;
- [ ] AI snapshot retention;
- [ ] Notification attempt retention;
- [ ] Job/Outbox retention;
- [ ] Search/cache cleanup;
- [ ] Analytics/log retention;
- [ ] temporary-file cleanup;
- [ ] orphan reconciliation;
- [ ] backup retention;
- [ ] deletion ledger;
- [ ] restore redaction reapplication;
- [ ] retention hold;
- [ ] audit trail;
- [ ] monitoring;
- [ ] E2E cleanup tests.

---

# 303. Retention specification template

```text id="9dcsxs"
Entity:
BuilderSession

Class:
R1 — SHORT_OPERATIONAL

Active Until:
expires_at

Public Access:
none after expiry

Linked Business Context:
Feedback / GenerationSnapshot

Cleanup Preconditions:
expired
no hold
required provenance retained

Purge:
session configuration

Retain:
GenerationSnapshot if policy/business relation requires

Derived Cleanup:
cache/search if any

Audit:
not required for ordinary expiry cleanup
```

---

# 304. Media purge specification template

```text id="tb5wrm"
Entity:
MediaAsset

Default Removal:
Archive

Hard Purge:
exceptional

Required Checks:
current references
draft references
historical references
operational dependencies
retention
hold

Binary Strategy:
mark purge pending
delete storage object
verify
finalize

Historical Revision:
must remain reproducible/provenance-safe

Audit:
required
```

---

# 305. Privacy redaction specification template

```text id="8qp6qy"
Data:
Contact phone

Immediate:
revoke all current exposure

Current Source:
redact/delete

Historical:
apply privacy policy/overlay

Artifacts:
revoke/regenerate where required

AI Copies:
discover/redact

Search:
delete

Cache:
invalidate

Analytics/Logs:
discover according to policy

Backup:
age out + deletion ledger on restore

Audit:
record action without deleted value
```

---

# 306. Retention compliance criteria

Реализация соответствует DOC-094, если:

1. Archive and Hard Delete are distinct;
2. Archive is default for professional content;
3. archived content leaves current Public/Builder/Search;
4. Archive does not destroy historical references;
5. Restore does not automatically republish;
6. published Revisions remain immutable historical records;
7. old Revision is not deleted merely because it is superseded;
8. historical access remains privacy-controlled;
9. privacy redaction can override historical serving;
10. hard delete performs dependency analysis;
11. Media relation removal does not delete binary automatically;
12. Media purge respects current/draft/historical references;
13. binary deletion is staged and verifiable;
14. unknown orphan files are not blindly deleted;
15. derived data can be purged/rebuilt safely;
16. Builder Session expires independently of cleanup worker;
17. Builder transient data has bounded retention;
18. business-linked Builder provenance is preserved;
19. Feedback PII/attachments have private retention policy;
20. Casting Source and AI copies are included in privacy scope;
21. Opportunity history is not auto-deleted on terminal stage;
22. AI output retention follows source sensitivity;
23. discarded AI artifacts may have shorter retention;
24. Notification/provider technical history is bounded;
25. processed Outbox can be purged;
26. unprocessed Outbox cannot be age-purged;
27. completed jobs/attempts have operational retention;
28. Search/Cache have no business retention dependency;
29. Analytics/logs use bounded privacy-aware retention;
30. temporary files are automatically cleaned;
31. active temporary work is protected from cleanup;
32. hard purge jobs are idempotent;
33. local deletion does not imply external provider deletion;
34. privacy deletion removes or suppresses derived copies;
35. Audit can preserve action history without deleted sensitive value;
36. backups have bounded retention;
37. restore re-applies deletion/redaction state;
38. retention holds block automated purge;
39. destructive actions are audited;
40. VOP may clean deterministic technical residue but not autonomously destroy professional history.

---

# 307. Финальная доктрина

> **Deletion и retention в платформе должны сохранять баланс между профессиональной историей, операционной ценностью, privacy и технической управляемостью. Текущие профессиональные сущности по умолчанию архивируются, а не уничтожаются; immutable Revisions/Snapshots сохраняют факт того, что было опубликовано или сформировано; derived данные можно безопасно пересоздавать; private operational данные имеют ограниченный retention; а privacy redaction распространяется не только на исходную строку, но и на snapshots, AI outputs, artifacts, Search, Cache, Analytics, Logs и restore-процедуры. Hard Delete является управляемой исключительной операцией с dependency analysis, audit и проверяемым purge, а не обычным способом убрать элемент из интерфейса.**