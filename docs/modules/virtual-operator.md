# VIRTUAL PORTFOLIO OPERATOR MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация внутреннего VOP для content health, readiness, stale-state detection, operational findings, recommendations и безопасной deterministic automation

**Целевой файл:** `docs/modules/virtual-operator.md`  
**Документ:** DOC-128  
**Статус:** ✅ Completed  
**Тип:** Module / Admin Assistant / Operational Control / Content Health / Readiness / Recommendations / Safe Automation

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/product-principles.md`
- `docs/domain/domain-model.md`
- `docs/domain/entity-relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions-and-history.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/components.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`
- `docs/architecture/media-processing.md`
- `docs/architecture/pdf-generation.md`
- `docs/architecture/background-jobs.md`
- `docs/architecture/cache-strategy.md`
- `docs/architecture/search.md`
- `docs/architecture/ai-architecture.md`
- `docs/architecture/notifications.md`
- `docs/database/database-architecture.md`
- `docs/database/data-dictionary.md`
- `docs/database/visibility-and-access.md`
- `docs/database/validation-and-constraints.md`
- `docs/database/deletion-and-retention.md`
- `docs/api/server-actions.md`
- `docs/api/contracts.md`
- `docs/api/errors.md`
- `docs/api/idempotency.md`
- `docs/modules/profile.md`
- `docs/modules/media-library.md`
- `docs/modules/portfolio.md`
- `docs/modules/emotional-portfolio.md`
- `docs/modules/emotional-grid.md`
- `docs/modules/projects.md`
- `docs/modules/training.md`
- `docs/modules/skills-languages.md`
- `docs/modules/professional-media-links.md`
- `docs/modules/contacts.md`
- `docs/modules/questionnaires.md`
- `docs/modules/public-questionnaire-builder.md`
- `docs/modules/castings.md`
- `docs/modules/casting-ai-analysis.md`
- `docs/modules/feedback.md`
- `docs/modules/notifications.md`
- `docs/modules/opportunity-pipeline.md`
- `docs/modules/site-themes.md`

---

# 1. Назначение

Virtual Portfolio Operator, далее **VOP**, является внутренним Admin capability для постоянной оценки operational и content health сайта актрисы.

VOP помогает обнаруживать:

- отсутствующие обязательные данные;
- неполные разделы;
- stale revisions;
- stale questionnaires;
- broken/unsafe links;
- недоступные media derivatives;
- failed background jobs;
- duplicate candidates;
- проблемы readiness;
- противоречивые visibility settings;
- проблемы Public Builder readiness;
- Casting/Opportunity follow-up gaps;
- Theme technical/accessibility issues;
- notification delivery failures;
- search/index/cache inconsistencies;
- migration leftovers;
- другие технически определимые проблемы.

---

# 2. Главная доктрина

> **VOP наблюдает систему, объясняет найденную проблему, рекомендует действие и может самостоятельно выполнять только заранее разрешённые deterministic, reversible или safely idempotent operational actions. VOP не является владельцем professional Source и не получает business authority.**

Canonical:

```text
OBSERVE
   ↓
DETECT
   ↓
EXPLAIN
   ↓
RECOMMEND
   ↓
┌─────────────────────────────┐
│ Human decision required?    │
└──────────────┬──────────────┘
               │
        yes    │    no, safe deterministic
               │
         ▼     │      ▼
      HUMAN    │   SAFE ACTION
      APPLY    │      │
               └──────┘
```

---

# 3. Fundamental separation

```text
VOP Finding
≠ Professional Fact
≠ AI Extraction
≠ AI Draft
≠ Business Decision
≠ Domain Mutation
≠ Notification
≠ Audit
```

---

# 4. VOP AI role separation

Система содержит отдельные AI/cognitive capabilities:

```text
VOP
Casting AI
Theme AI
BB Assistant
```

Их authority не объединяется.

---

# 5. VOP отвечает за

```text
system/content health
readiness
consistency
technical drift
operational follow-up
recommendations
safe deterministic remediation
```

---

# 6. VOP не отвечает за

```text
professional writing
casting interpretation
visual theme creation
professional fact creation
business outcome decisions
```

---

# 7. VOP-INV-001 — No Omnipotent AI

Наличие единого AI provider/gateway не создаёт единого omnipotent AI actor.

---

# 8. Capability classes

VOP должен разделять свои действия на четыре класса:

```text
OBSERVE
RECOMMEND
SAFE_AUTOMATION
HUMAN_ONLY
```

---

# 9. OBSERVE

Read-only evaluation.

Примеры:

```text
Check questionnaire readiness
Check broken links
Check missing contact
Check stale PDF
Check unresolved media job
```

---

# 10. RECOMMEND

VOP создаёт Finding/Recommendation, но ничего не меняет.

---

# 11. SAFE_AUTOMATION

Разрешён только для заранее определённых технических действий.

---

# 12. HUMAN_ONLY

Любое действие, меняющее professional/business truth, требует Human.

---

# 13. VOP-INV-002 — Default Is No Mutation

Если action не включён в explicit safe-action allowlist, он считается Human-only.

---

# 14. VOP Finding

Основной объект:

```text
VOPFinding
```

---

# 15. Suggested entity

```text
vop_findings
```

---

# 16. Representative fields

```text
id
profile_id
finding_type
severity
source_entity_type?
source_entity_id?
status
title_code
explanation
evidence
detected_at
last_observed_at
resolved_at?
resolution_type?
fingerprint
generation
```

---

# 17. Finding classes

Canonical high-level taxonomy:

```text
CONTENT
READINESS
MEDIA
LINK
QUESTIONNAIRE
BUILDER
CASTING
OPPORTUNITY
NOTIFICATION
THEME
SEARCH
CACHE
BACKGROUND_JOB
MIGRATION
SECURITY
DATA_INTEGRITY
```

---

# 18. Severity

Recommended:

```text
CRITICAL
HIGH
MEDIUM
LOW
INFO
```

---

# 19. CRITICAL

Reserved for issues such as:

```text
private-data exposure risk
public access policy violation
severe data integrity failure
active theme/render failure
```

---

# 20. HIGH

Examples:

```text
primary public questionnaire broken
required Main Portfolio photo unavailable
important public link broken
persistent failed media processing
```

---

# 21. MEDIUM

Examples:

```text
missing optional professional metadata
stale questionnaire
unassigned actionable inquiry
```

---

# 22. LOW / INFO

Non-blocking optimization/readiness information.

---

# 23. VOP-INV-003 — Severity Is Operational

Severity does not mutate:

- priority of Opportunity;
- Casting priority;
- public ordering;
- Search relevance.

---

# 24. Finding status

Canonical:

```text
OPEN
ACKNOWLEDGED
RESOLVED
SUPPRESSED
STALE
```

---

# 25. OPEN

Current issue exists.

---

# 26. ACKNOWLEDGED

Human has seen/accepted issue but it still exists.

---

# 27. RESOLVED

Current deterministic evaluation indicates issue is gone.

---

# 28. SUPPRESSED

Human explicitly suppresses this finding or class/context.

---

# 29. STALE

Finding no longer corresponds to current source generation and awaits reconciliation.

---

# 30. VOP-INV-004 — Acknowledged ≠ Resolved

---

# 31. Fingerprint

Each deterministic finding must have stable logical fingerprint.

Example:

```text
profile:{id}:questionnaire:{id}:missing-close-up
```

---

# 32. VOP-INV-005 — Fingerprint Prevents Alert Duplication

Repeated scan of same unresolved condition updates existing Finding rather than creating uncontrolled duplicates.

---

# 33. Generation

Finding should be bound to relevant source/entity generation or versions where needed.

---

# 34. Evidence

Every Finding should explain why it exists.

---

# 35. Example

```text
Finding:
Primary questionnaire is not publish-ready.

Evidence:
Main Portfolio CLOSE_UP is missing or no longer questionnaire-eligible.
```

---

# 36. VOP-INV-006 — Finding Requires Explainable Evidence

---

# 37. No opaque “AI says bad”

Forbidden baseline:

```text
Site health: 62%
```

without explainable factors.

---

# 38. Health scores

If introduced later, they must remain derivative summaries.

They cannot replace individual Findings.

---

# 39. VOP-INV-007 — Score Is Never Authority

---

# 40. Content health checks

VOP may inspect Profile completeness.

Examples:

```text
missing required profile field
empty biography locale
missing current location
public Hero projection incomplete
```

---

# 41. VOP cannot fill missing field automatically.

---

# 42. VOP-CONT-001 — Missing Data Is Recommendation Only

---

# 43. Example

Allowed:

```text
“English biography is missing.”
```

Not allowed:

```text
Generate biography and publish automatically.
```

---

# 44. Portfolio checks

VOP may detect:

```text
no eligible CLOSE_UP
no eligible FULL_BODY
missing primary image
duplicate Portfolio assignment
archived media still referenced
broken derivative
invalid display order
```

---

# 45. VOP cannot reclassify image category.

---

# 46. VOP-CONT-002 — Portfolio Classification Is Human Source

---

# 47. Emotional Portfolio checks

Possible:

```text
shooting date missing
portfolio not complete
source asset unavailable
public portfolio references archived asset
```

---

# 48. Emotional Grid checks

Possible:

```text
wrong photo count
unconfirmed cells
derived composite missing
primary Grid unavailable
questionnaire references non-final GridRevision
```

---

# 49. VOP cannot confirm cells.

---

# 50. VOP-CONT-003 — Emotional Human Confirmation Cannot Be Automated

---

# 51. Projects checks

VOP may identify:

```text
Project missing Role
Project public but required media unavailable
duplicate candidate
incomplete localization
legacy educational Project still not migrated
```

---

# 52. Duplicate candidate

Only recommendation.

---

# 53. VOP-CONT-004 — Duplicate Detection Never Auto-Merges Source

---

# 54. Training checks

Examples:

```text
legacy Project Other candidate for Training migration
missing provider/institution
date inconsistency
duplicate candidate
```

---

# 55. Skills checks

Examples:

```text
duplicate current ActorSkill
invalid historical level
highlighted but Builder/Public eligibility absent
stale migrated free-text skill
```

---

# 56. VOP cannot set Skill level.

---

# 57. VOP-CONT-005 — Skill Level Is Human-Owned

---

# 58. Languages checks

Possible:

```text
legacy qualitative proficiency requiring review
duplicate language assignment
invalid CEFR
missing localization label
```

---

# 59. VOP cannot infer CEFR.

---

# 60. VOP-CONT-006 — Language Level Is Human-Owned

---

# 61. Professional Media/Links health

VOP may run or schedule safe link-health checks.

---

# 62. Link health states may generate findings for:

```text
BROKEN
TIMEOUT
UNSAFE
REDIRECTED
```

---

# 63. Redirect

VOP may recommend canonical URL update.

It does not apply automatically.

---

# 64. VOP-LINK-001 — Redirect Does Not Auto-Rewrite Source URL

---

# 65. Broken external link

May recommend:

```text
Review link
Archive link
Replace URL
```

---

# 66. Link checks must use SSRF-safe infrastructure.

---

# 67. VOP-LINK-002 — Link Health Checker Has No Private Network Reach

---

# 68. Professional media checks

VOP may inspect:

```text
web derivative absent
poster derivative missing
playback derivative stale
unsupported codec derivative
failed transcoding job
```

---

# 69. Safe remediation

May retry/rebuild derived media.

---

# 70. VOP-MEDIA-001 — Rebuild Derived Artifact Is Safe Automation

Provided:

- original Source unchanged;
- operation idempotent;
- output derived;
- access class preserved.

---

# 71. VOP must never replace original.

---

# 72. VOP-MEDIA-002 — Immutable Original Is Never Mutated

---

# 73. Questionnaire health

VOP may detect:

```text
primary questionnaire absent
multiple primary questionnaires
mandatory Close-Up missing
mandatory Full Body missing
stale snapshot/revision
link broken
QR invalid
PDF failed
published questionnaire references archived Source
```

---

# 74. VOP cannot publish questionnaire.

---

# 75. VOP-QNR-001 — Publish Is Human-Only

---

# 76. VOP may regenerate derived PDF from existing immutable QuestionnaireRevision.

---

# 77. VOP-QNR-002 — Regenerate Artifact ≠ Republish Content

---

# 78. PDF regeneration is safe when:

```text
same immutable revision
same renderer version or controlled new renderer generation
no content mutation
```

---

# 79. Stale questionnaire

Need distinguish:

```text
Current Source changed
```

from:

```text
Historical QuestionnaireRevision invalid
```

---

# 80. Historical Revision remains historical.

---

# 81. VOP-QNR-003 — Source Update Does Not Rewrite Questionnaire History

---

# 82. VOP may recommend:

```text
Create new questionnaire revision
```

---

# 83. It cannot create/publish that professional revision autonomously baseline.

---

# 84. Public Builder health

VOP may check:

```text
Builder enabled but no eligible Close-Up
Builder enabled but no eligible Full Body
template not ready
invalid default template
Builder-only Contact missing
stale Builder cache
```

---

# 85. Safe actions

May:

```text
recompute Builder readiness
invalidate Builder cache
rebuild Builder projection
```

---

# 86. VOP-BLD-001 — VOP Cannot Enable Builder Eligibility

---

# 87. Casting checks

VOP may flag:

```text
new Casting not reviewed
deadline approaching
requirements unconfirmed
analysis stale
analysis failed
requested material missing
```

---

# 88. VOP cannot confirm Casting Requirement.

---

# 89. VOP-CST-001 — Casting Requirement Confirmation Is Human-Only

---

# 90. VOP cannot submit Casting materials.

---

# 91. VOP-CST-002 — Submission Is Human/Explicit Workflow

---

# 92. Casting AI interaction

VOP may detect:

```text
no analysis requested
analysis stale
analysis provider failure
analysis pending too long
```

---

# 93. Baseline VOP may recommend new AI analysis.

---

# 94. Automatic re-analysis

Not baseline.

It creates cost/privacy/provider work and semantic changes.

---

# 95. VOP-CST-003 — Casting AI Analysis Is Explicit by Default

---

# 96. Technical retry

If an already-requested AI job failed retryably, VOP/worker policy may safely retry the same request.

---

# 97. VOP-CST-004 — Retry Existing Request ≠ New Analysis Intent

---

# 98. Feedback checks

VOP may find:

```text
unread high-priority inquiry
unassigned inquiry
next action overdue
Casting Invitation not reviewed
notification failed
```

---

# 99. VOP cannot reply.

---

# 100. VOP-FBK-001 — Professional Reply Is Human-Controlled

---

# 101. VOP cannot create Casting from Feedback.

---

# 102. VOP-FBK-002 — Feedback Conversion Is Human-Only

---

# 103. Opportunity checks

VOP may detect:

```text
NEW opportunity not qualified/reviewed
next action overdue
stage stalled
OFFER without follow-up
BOOKED without Project/Role Draft
source provenance inconsistency
```

---

# 104. VOP cannot move stage.

---

# 105. VOP-OPP-001 — Opportunity Stage Is Human Authority

---

# 106. VOP cannot create Project after Booked.

---

# 107. VOP-OPP-002 — Booked Conversion Is Human-Only

---

# 108. Notifications checks

VOP may detect:

```text
delivery repeatedly failing
recipient disabled
WhatsApp auth expired
email provider unavailable
notification backlog
```

---

# 109. Safe action

May retry a **retryable existing delivery** through Notifications policy.

---

# 110. VOP-NOT-001 — VOP Cannot Invent New Professional Notification Content

---

# 111. Final failed delivery

VOP may surface action.

Do not infinitely resend.

---

# 112. Theme checks

VOP may detect:

```text
contrast blocker
deprecated theme schema
broken render artifact
responsive overflow
activation health failure
expired temporary-theme inconsistency
```

---

# 113. Safe actions

May:

```text
rerun deterministic validation
regenerate theme derived bundle
invalidate theme cache
```

---

# 114. VOP-THEME-001 — VOP Cannot Publish/Activate Theme

---

# 115. Automatic technical rollback

If implemented, belongs Theme safety policy, not free-form VOP decision.

---

# 116. VOP-THEME-002 — VOP Cannot Choose Design Direction

---

# 117. Search checks

VOP may detect:

```text
index generation behind source generation
public index candidate with invalid current visibility
Builder index stale
Admin index job failed
```

---

# 118. Safe actions

May:

```text
reindex
refresh search projection
clear stale search cache
```

---

# 119. VOP-SRCH-001 — Reindex Cannot Change Source Eligibility

---

# 120. Cache checks

VOP may detect stale generations or failed invalidation.

---

# 121. Safe actions:

```text
invalidate cache
rebuild projection
recompute alias
```

---

# 122. VOP-CACHE-001 — Cache Repair Is Safe Operational Automation

---

# 123. Background-job checks

VOP may identify:

```text
stuck lease
retry exhaustion
dead-letter item
repeated processor failure
queue backlog
```

---

# 124. Safe retry

Only when job class explicitly supports idempotent retry.

---

# 125. VOP-JOB-001 — Retry Requires Declared Idempotency

---

# 126. Dead-letter

Human review may be required depending task type.

---

# 127. Never blindly retry:

```text
external send with unknown outcome
payment-like side effects
business submission with ambiguous provider outcome
```

---

# 128. VOP-JOB-002 — Unknown External Outcome Is Not Safe Retry

---

# 129. Data-integrity checks

VOP may detect structural anomalies such as:

```text
multiple current primaries
broken same-profile reference
orphan relation
invalid lifecycle combination
missing outbox projection repair
```

---

# 130. VOP may not directly “repair” business Source with arbitrary SQL.

---

# 131. VOP-DATA-001 — Source Integrity Repair Uses Domain Commands or Human Review

---

# 132. Purely derived repair

Examples:

```text
rebuild projection
rebuild search index
regenerate derivative
```

can be safe.

---

# 133. Migration checks

VOP may report:

```text
legacy unmapped skill
legacy qualitative language
legacy unsafe embed HTML
legacy educational Project not reviewed
stale old path/reference
```

---

# 134. VOP cannot decide ambiguous migration mapping.

---

# 135. VOP-MIG-001 — Ambiguous Migration Is Human Review

---

# 136. Readiness Engine

VOP should consume existing domain readiness services rather than duplicate their rules.

---

# 137. Example

```text
QuestionnaireReadinessService
BuilderReadinessService
MediaReadinessService
ThemeValidationService
```

---

# 138. VOP-INV-008 — VOP Does Not Reimplement Domain Truth

---

# 139. Correct architecture

```text
Domain Validation / Readiness
          ↓
VOP Observer
          ↓
Finding
```

---

# 140. Incorrect

```text
VOP invents separate rules conflicting with Domain
```

---

# 141. Detection modes

VOP can run through:

```text
EVENT_DRIVEN
SCHEDULED_SCAN
ON_DEMAND_SCAN
```

---

# 142. EVENT_DRIVEN

Triggered from domain/outbox events.

Example:

```text
QuestionnairePublished
→ recompute relevant health
```

---

# 143. SCHEDULED_SCAN

Useful for:

```text
external link health
overdue next actions
stale jobs
provider integration health
```

---

# 144. ON_DEMAND_SCAN

Admin requests:

```text
Проверить состояние сайта
```

---

# 145. VOP-INV-009 — Scan Does Not Mutate by Default

---

# 146. Scan entity

Optional:

```text
vop_scans
```

---

# 147. Fields

```text
id
scope
mode
status
started_at
completed_at?
checks_run
findings_created
findings_updated
error_summary?
```

---

# 148. Scan states

```text
QUEUED
RUNNING
SUCCEEDED
PARTIAL
FAILED
```

---

# 149. VOP scan failure

Does not mark all previous findings resolved.

---

# 150. VOP-INV-010 — Failed Scan Cannot Produce False Health

---

# 151. Finding reconciliation

After successful check:

```text
condition exists → OPEN/update
condition gone → RESOLVED
```

---

# 152. If relevant Source changed during scan

result should be version/generation checked.

---

# 153. VOP-INV-011 — Stale Scan Result Cannot Resolve Newer Finding Incorrectly

---

# 154. Recommendation

Separate object where richer lifecycle is useful:

```text
VOPRecommendation
```

---

# 155. Suggested entity

```text
vop_recommendations
```

---

# 156. Representative fields

```text
id
finding_id
action_type
action_class
description
target_entity_type?
target_entity_id?
risk_level
requires_human
created_at
status
```

---

# 157. Recommendation status

```text
OPEN
ACCEPTED
APPLIED
DISMISSED
OBSOLETE
```

---

# 158. VOP-INV-012 — Recommendation ≠ Action Execution

---

# 159. Action classes

Canonical:

```text
READ_ONLY
SAFE_AUTOMATED
HUMAN_CONFIRM_REQUIRED
HUMAN_ONLY
```

---

# 160. READ_ONLY

Examples:

```text
open affected record
view source
view diff
```

---

# 161. SAFE_AUTOMATED

Examples:

```text
invalidate cache
rebuild search index
regenerate media derivative
retry idempotent failed job
recompute readiness
run safe link check
regenerate PDF artifact from immutable revision
```

---

# 162. HUMAN_CONFIRM_REQUIRED

Examples:

```text
archive broken ProfessionalLink
replace URL
create new Questionnaire Draft
request new Casting AI analysis
```

Even if action is technically simple, it affects professional workflow.

---

# 163. HUMAN_ONLY

Examples:

```text
change Skill level
change CEFR
confirm Casting Requirement
publish Questionnaire
activate Theme
send professional reply
move Opportunity stage
create Project/Role credit
```

---

# 164. VOP-INV-013 — Business Meaning Overrides Technical Simplicity

A technically easy DB update can still be Human-only.

---

# 165. Safe Action Registry

Required central allowlist:

```text
VOPSafeActionRegistry
```

---

# 166. Each safe action declares:

```text
action type
allowed target types
preconditions
idempotency semantics
rollback/rebuild behavior
required authorization
audit behavior
```

---

# 167. VOP-ACT-001 — No Dynamic Arbitrary Tool Execution

---

# 168. VOP must not receive:

- unrestricted SQL;
- shell;
- generic HTTP client;
- arbitrary mutation endpoint;
- generic send-message action.

---

# 169. VOP-ACT-002 — Tools Are Narrow and Typed

---

# 170. Safe Action Execution

Suggested entity:

```text
vop_action_executions
```

---

# 171. Fields

```text
id
recommendation_id?
finding_id?
action_type
target
requested_by
execution_mode
status
idempotency_key
started_at
completed_at?
result_summary
error_code?
```

---

# 172. Execution modes

```text
AUTOMATIC_POLICY
ADMIN_ONE_CLICK
SYSTEM_RETRY
```

---

# 173. Execution states

```text
REQUESTED
RUNNING
SUCCEEDED
FAILED_RETRYABLE
FAILED_FINAL
CANCELLED
```

---

# 174. VOP-ACT-003 — Execution History Is Auditable

---

# 175. Automatic policy

Only for `SAFE_AUTOMATED` actions explicitly enabled in configuration.

---

# 176. Example policy:

```text
Auto rebuild missing web thumbnail = enabled
Auto republish questionnaire = impossible
```

---

# 177. VOP-ACT-004 — Automation Is Opt-In Per Action Type

---

# 178. Safe action preflight

Before execution:

```text
reload current target
check generation/version
check action remains applicable
check authorization
check idempotency
```

---

# 179. VOP-ACT-005 — Recommendation Is Revalidated Before Apply

---

# 180. Example

Finding says:

```text
media derivative missing
```

but Human manually regenerated it.

One-click action should return already-resolved/no-op, not create conflicting work.

---

# 181. Idempotency

All Safe Actions must be idempotent or have explicit dedupe.

---

# 182. VOP-ACT-006 — Safe Automation Requires Idempotency

---

# 183. Retry

Technical retry is allowed only for registered retryable action.

---

# 184. No infinite retry.

---

# 185. AI use inside VOP

VOP may optionally use AI for:

```text
natural-language explanation
summarizing multiple findings
prioritization suggestions
admin conversational querying
```

---

# 186. Deterministic checks remain source of health truth.

---

# 187. VOP-AI-001 — AI Does Not Determine Structural Validity Where Deterministic Rule Exists

---

# 188. Example

Whether Questionnaire has Close-Up is deterministic.

LLM not needed.

---

# 189. AI explanation

Could transform:

```text
QNR_CLOSE_UP_MISSING
```

into:

```text
“Основная анкета не готова: в Main Portfolio нет доступного крупного плана.”
```

---

# 190. The code remains authoritative.

---

# 191. VOP-AI-002 — Explanation Text Is Non-Authoritative

---

# 192. AI prioritization

May recommend:

```text
fix broken public video before optional English description
```

but cannot change domain priorities automatically.

---

# 193. VOP-AI-003 — AI Priority Is Advisory

---

# 194. Conversational VOP

Admin may ask:

```text
Что сейчас мешает сайту быть полностью готовым?
```

---

# 195. Response should be grounded in current Findings/readiness.

---

# 196. It should distinguish:

```text
blockers
warnings
recommendations
```

---

# 197. VOP-AI-004 — Conversational Answer Must Be Grounded in Current System State

---

# 198. No fabricated issue

If VOP cannot verify a condition:

state uncertainty.

---

# 199. VOP-AI-005 — Unknown ≠ Finding

---

# 200. VOP vs BB Assistant

VOP:

```text
“В биографии отсутствует английская версия.”
```

BB Assistant:

```text
drafts English biography when Human requests.
```

---

# 201. VOP-INV-014 — VOP Does Not Become BB Writer

---

# 202. VOP vs Casting AI

VOP:

```text
“Casting analysis is stale.”
```

Casting AI:

```text
extracts casting requirements.
```

---

# 203. VOP-INV-015 — VOP Does Not Interpret Casting Brief

---

# 204. VOP vs Theme AI

VOP:

```text
“Current theme fails contrast validation.”
```

Theme AI:

```text
proposes new visual tokens.
```

---

# 205. VOP-INV-016 — VOP Does Not Choose Visual Style

---

# 206. Dashboard

Recommended VOP Admin dashboard:

```text
Overview
Critical
Action Required
Content
Media & Links
Questionnaires
Castings
Opportunities
Theme
System
Resolved
```

---

# 207. Overview metrics

Possible:

```text
Open Critical
Open High
Readiness blockers
Broken public links
Failed jobs
Overdue next actions
```

---

# 208. Avoid single vanity score as only UX.

---

# 209. VOP-UX-001 — Problems Must Be Individually Actionable

---

# 210. Finding card

Should show:

```text
severity
title
explanation
evidence
affected object
detected time
recommended action
action authority
```

---

# 211. Example

```text
HIGH
Primary questionnaire PDF unavailable

Reason:
PDF generation failed for QuestionnaireRevision 8.

Safe action:
[Retry PDF generation]
```

---

# 212. Human-only example

```text
MEDIUM
English level uses legacy qualitative value

Reason:
Legacy import contains “Fluent”, but canonical model requires Native/A1–C2.

Action:
[Open Language Record]
```

No “Fix automatically”.

---

# 213. VOP-UX-002 — UI Must Show Why Automation Is or Is Not Allowed

---

# 214. Finding suppression

Human may suppress false-positive/non-actionable condition.

---

# 215. Suppression scope

Should be explicit:

```text
this finding
this entity/check
until date
permanent until config change
```

as supported.

---

# 216. VOP-INV-017 — Suppression Does Not Change Source

---

# 217. Suppressed condition

May still be visible in history/settings.

---

# 218. New materially different condition

Should not be hidden accidentally by overly broad fingerprint unless suppression scope explicitly covers it.

---

# 219. VOP-FND-001 — Suppression Scope Is Deterministic

---

# 220. Resolution

Finding resolved when current condition no longer exists.

---

# 221. Human can manually mark acknowledged, but should not manually fake RESOLVED if deterministic condition remains.

---

# 222. VOP-FND-002 — Deterministic Finding Resolution Is Condition-Based

---

# 223. Manual resolve

Allowed only for non-deterministic/advisory findings, with explicit reason.

---

# 224. Resurfacing

If resolved issue returns:

reuse/open fingerprint generation or create new occurrence according to audit design.

---

# 225. Preserve recurrence history.

---

# 226. Notification integration

VOP can create Notification Intent for significant Findings.

---

# 227. Examples:

```text
CRITICAL privacy/access issue
HIGH broken primary questionnaire
overdue high-priority professional action
```

---

# 228. VOP-NOT-001 — Finding Notification Is Attention Only

---

# 229. Quiet hours/priorities remain Notification module-owned.

---

# 230. VOP does not directly call WhatsApp/email.

---

# 231. VOP-NOT-002 — VOP Emits Intent/Event, Not Provider Calls

---

# 232. Notification spam prevention

Repeated same Finding should not repeatedly emit external alert every scan.

---

# 233. Notify on:

```text
new finding
severity escalation
meaningful recurrence
```

according to policy.

---

# 234. VOP-NOT-003 — Finding Fingerprint Drives Attention Dedupe

---

# 235. Search

VOP Findings are Admin-only.

---

# 236. Public Search/SEO

Never.

---

# 237. VOP-SEC-001 — Findings Do Not Enter Public Search

---

# 238. VOP conversational search

May search Admin-visible Findings only under Admin auth.

---

# 239. Public visitor must not invoke VOP health queries.

---

# 240. VOP-SEC-002 — VOP Is Admin-Private

---

# 241. Data access

VOP read access should use domain queries/projections.

---

# 242. Avoid unrestricted database crawling if narrow APIs exist.

---

# 243. VOP-SEC-003 — Read Access Is Purpose-Scoped

---

# 244. Private domains

VOP may inspect Admin-private health state, but output remains Admin-private.

---

# 245. AI provider context

If AI explanation used:

send only finding/evidence necessary.

---

# 246. No need to send unrelated full DB records.

---

# 247. VOP-SEC-004 — VOP AI Context Is Minimized

---

# 248. PII

Avoid sending raw:

- phone;
- email;
- casting source text;
- private notes;

unless necessary for explicit supported task.

---

# 249. Logs

VOP logs use:

```text
scan_id
finding_type
entity_id
action_type
status
duration
correlation_id
```

---

# 250. Do not log full professional/private payloads by default.

---

# 251. VOP-SEC-005 — Logs Are Metadata-First

---

# 252. Scheduler

Scheduled VOP checks may run:

```text
hourly
daily
weekly
```

depending class.

---

# 253. Frequency examples

Fast-changing operational checks:

```text
job backlog
provider auth status
```

can be frequent.

---

# 254. Stable completeness checks

Can be event-driven/daily.

---

# 255. External link health

Should not hammer providers.

Use reasonable cadence.

---

# 256. VOP-INV-018 — Scan Frequency Is Risk/Change-Rate Appropriate

---

# 257. No sub-hour compulsive polling unless system need requires.

---

# 258. Scheduled check execution

Uses Background Jobs infrastructure.

---

# 259. Job type examples

```text
VOP_SCAN_CONTENT
VOP_SCAN_LINKS
VOP_SCAN_QUESTIONNAIRES
VOP_SCAN_OPERATIONS
VOP_RECONCILE_FINDINGS
VOP_EXECUTE_SAFE_ACTION
```

---

# 260. VOP-JOB-003 — Scan Jobs Are Idempotent/Reconciling

---

# 261. Partial scan failure

Only successfully evaluated checks can reconcile their findings.

---

# 262. Do not resolve untested checks.

---

# 263. VOP-JOB-004 — Partial Failure Cannot Produce False Resolution

---

# 264. Safe action jobs

Bind to:

```text
action_execution_id
target generation
idempotency key
```

---

# 265. Worker reloads current state before action.

---

# 266. VOP-ACT-007 — Old Queued Action Cannot Mutate Newer Incompatible State

---

# 267. Action result

Should classify:

```text
SUCCEEDED
NO_LONGER_NEEDED
BLOCKED_BY_CURRENT_STATE
FAILED_RETRYABLE
FAILED_FINAL
```

---

# 268. `NO_LONGER_NEEDED`

is successful reconciliation, not error.

---

# 269. Audit

Must audit:

```text
safe automated action executed
Human one-click VOP action
suppression change
VOP configuration change
automation policy enable/disable
```

---

# 270. Read-only scans

Operational logs, not necessarily full Audit per check.

---

# 271. VOP-INV-019 — Scan Telemetry ≠ Business Audit

---

# 272. Outbox events

Possible:

```text
VOPFindingOpened
VOPFindingEscalated
VOPFindingResolved
VOPSafeActionExecuted
VOPSafeActionFailed
```

---

# 273. Consumers

```text
Notification Center
Admin Dashboard
Analytics
Observability
```

---

# 274. Analytics

Can measure:

```text
time to resolve findings
recurring problem categories
safe action success rate
broken link rate
questionnaire readiness rate
media processing health
```

---

# 275. VOP analytics does not influence Source automatically.

---

# 276. VOP-ANL-001 — Metrics Are Improvement Signals Only

---

# 277. Health analytics

Can identify recurring operational debt.

Example:

```text
40% of PDF failures caused by unavailable derivative
```

---

# 278. Useful for engineering prioritization.

---

# 279. Not professional scoring.

---

# 280. VOP-ANL-002 — No Actress Quality Score

---

# 281. No ranking of actress attractiveness, professionalism or “marketability”.

---

# 282. VOP checks health of **system representation**, not worth of person.

---

# 283. VOP-SEC-006 — No Human Desirability Scoring

---

# 284. Readiness aggregation

Can group:

```text
Public Site Readiness
Questionnaire Readiness
Builder Readiness
Casting Workflow Readiness
Operational Health
```

---

# 285. Readiness status

Example:

```text
READY
READY_WITH_WARNINGS
BLOCKED
UNKNOWN
```

---

# 286. VOP-INV-020 — Readiness Is Context-Specific

No one universal “site completeness percentage” required.

---

# 287. Public Site readiness

May include:

```text
Profile publishable
Hero data available
Main Portfolio available
Contacts valid
primary video if required
public links healthy
```

according to real domain rules.

---

# 288. Questionnaire readiness

Uses Questionnaire domain.

---

# 289. Builder readiness

Uses Builder domain.

---

# 290. VOP-INV-021 — VOP Consumes Canonical Readiness Services

---

# 291. Configuration

Suggested:

```text
vop_policies
```

or code/config-based policies.

---

# 292. Policy may define:

```text
check enabled
scan cadence
severity mapping
notification threshold
safe automation enabled
suppression defaults
```

---

# 293. VOP-POL-001 — Policy Cannot Grant Forbidden Business Authority

Configuration cannot turn:

```text
Confirm Casting Requirement
```

into safe automation.

---

# 294. Hard-deny actions

Canonical forbidden automation set:

```text
CREATE_OR_CHANGE_PROFILE_FACT
CHANGE_SKILL_LEVEL
CHANGE_LANGUAGE_LEVEL
CHANGE_CONTACT
CHANGE_VISIBILITY
PUBLISH_QUESTIONNAIRE
PUBLISH_PROJECT
PUBLISH_THEME
ACTIVATE_THEME
CONFIRM_CASTING_REQUIREMENT
SEND_PROFESSIONAL_REPLY
SUBMIT_CASTING
CHANGE_OPPORTUNITY_STAGE
MARK_BOOKED
CREATE_PROJECT_CREDIT
DELETE_SOURCE
```

---

# 295. VOP-INV-022 — Forbidden Authority Cannot Be Enabled by Admin Policy Toggle

---

# 296. Destructive actions

Archive/Delete Source are Human-only baseline.

---

# 297. Derived artifacts

May be deleted/regenerated safely if source retained and action registered.

---

# 298. VOP-ACT-008 — Derived Destruction Is Distinct From Source Destruction

---

# 299. Security findings

VOP may consume security health signals.

Examples:

```text
public route serving private projection
invalid access-cache generation
expired integration credential
```

---

# 300. VOP must not attempt improvised security repair.

---

# 301. Critical security issue

Recommended:

```text
open CRITICAL finding
send Admin notification
optionally execute pre-approved containment action
```

---

# 302. Containment action example

If architecture supports explicit safe operation:

```text
invalidate public cache
disable affected public projection alias
```

---

# 303. VOP-SEC-007 — Security Containment Must Be Predefined

No arbitrary autonomous remediation.

---

# 304. Integrity finding examples

```text
multiple primary questionnaires
two active themes
same-profile violation
orphan projection
```

---

# 305. DB constraints should prevent many of these.

VOP is secondary detection, not substitute.

---

# 306. VOP-DATA-002 — VOP Does Not Replace Database Constraints

---

# 307. Manual Admin request

Admin may ask:

```text
Проверь всё, что мешает отправить анкету на кастинг.
```

VOP should scope checks to relevant domains.

---

# 308. It should not automatically modify content.

---

# 309. VOP-UX-003 — User Intent Does Not Imply Mutation Unless Explicit Action Chosen

---

# 310. One-click safe actions

Examples:

```text
Пересобрать PDF
Повторить генерацию thumbnail
Обновить поисковый индекс
Очистить stale cache
Повторить безопасную проверку ссылки
```

---

# 311. Human-required actions

Buttons navigate/open domain editor:

```text
Открыть язык
Открыть ссылку
Открыть анкету
Открыть кастинг
Открыть Opportunity
```

---

# 312. VOP-UX-004 — “Fix” Button Is Used Only for Truly Safe Registered Fixes

---

# 313. Bulk actions

Safe bulk remediation may be supported only for homogeneous registered actions.

Example:

```text
Rebuild all missing thumbnails
```

---

# 314. VOP-ACT-009 — Bulk Safe Action Has Per-Target Validation

One bad target cannot authorize unsafe mutation of others.

---

# 315. Bulk Human decisions

No baseline:

```text
Confirm all AI requirements
Publish all questionnaires
```

through VOP.

---

# 316. Explainability

Every recommendation should answer:

```text
What is wrong?
Why does it matter?
What evidence supports it?
What can safely be done?
What requires Human?
```

---

# 317. VOP-INV-023 — Recommendation Is Actionable and Explainable

---

# 318. No manipulative urgency

Use operational facts.

Example:

```text
“Deadline is tomorrow and materials are not marked ready.”
```

rather than unsupported alarm.

---

# 319. Historical finding

Old resolved Finding remains available for diagnostics.

---

# 320. VOP-INV-024 — Resolution Does Not Delete Operational History

---

# 321. Retention

Separate classes:

```text
Findings
Recommendations
Action executions
Scan telemetry
AI explanations
Suppression policies
```

---

# 322. Long-term

Useful Finding/action history can support reliability analysis.

---

# 323. AI raw explanation/provider payload

May have shorter retention.

---

# 324. Scan telemetry

Can be compacted.

---

# 325. VOP-INV-025 — Retention Is Component-Specific

---

# 326. Privacy deletion

If Finding references deleted private entity:

- remove/redact prohibited evidence;
- preserve safe operational tombstone if required.

---

# 327. VOP-SEC-008 — Findings Do Not Preserve Prohibited PII Indefinitely

---

# 328. Example

Instead of retaining deleted phone:

```text
“ContactMethod <redacted> had validation issue.”
```

---

# 329. Restore

After backup restore:

- do not replay Safe Actions already externally completed without reconciliation;
- do not reopen resolved Findings blindly;
- do not clear valid suppressions;
- do not rerun provider-cost AI automatically;
- reconcile current source generations first.

---

# 330. VOP-INV-026 — Restore Reconciles Before Acting

---

# 331. Safe derived rebuild after restore

Can be queued if current artifact absent.

---

# 332. External side-effect actions

Need conservative reconciliation.

---

# 333. Migration

Legacy health warnings/scripts should map into current Finding taxonomy where meaningful.

---

# 334. Do not migrate plain log errors as active Findings without current revalidation.

---

# 335. VOP-MIG-002 — Current Condition Must Be Rechecked

---

# 336. Existing cron repair scripts

Must be classified:

```text
safe deterministic
human-required
obsolete
unsafe
```

---

# 337. VOP-MIG-003 — Legacy Automation Is Not Trusted Automatically

---

# 338. Existing manual maintenance scripts

Should not become VOP tools until they satisfy:

- narrow input;
- authorization;
- idempotency;
- current-state revalidation;
- bounded effect;
- auditability.

---

# 339. Migration idempotency

Required.

---

# 340. Commands

Canonical:

```text
RunVOPScan
RunVOPCheck
AcknowledgeVOPFinding
SuppressVOPFinding
UnsuppressVOPFinding
DismissVOPRecommendation
ExecuteVOPSafeAction
RetryVOPSafeAction
CancelVOPSafeAction
UpdateVOPPolicy
```

---

# 341. Queries

```text
GetVOPOverview
ListVOPFindings
GetVOPFinding
GetVOPRecommendations
GetVOPReadiness
GetVOPScan
ListVOPScans
GetVOPActionExecution
ListVOPActionExecutions
GetVOPPolicy
```

---

# 342. Safe action input

Conceptually:

```text
findingId
recommendationId?
expectedFindingGeneration
idempotencyKey
```

---

# 343. Client cannot supply

```text
arbitrary SQL
arbitrary URL fetch
arbitrary mutation type
source value replacement
authorization override
```

---

# 344. VOP-ACT-010 — Action Type Comes From Server-Registered Recommendation

---

# 345. Scan input

```text
scope
checkSet?
idempotencyKey?
```

---

# 346. Scope examples

```text
ALL
PUBLIC_SITE
QUESTIONNAIRES
MEDIA
LINKS
CASTINGS
OPPORTUNITIES
THEME
OPERATIONS
```

---

# 347. Errors

At minimum:

```text
VOP_FINDING_NOT_FOUND
VOP_RECOMMENDATION_NOT_FOUND
VOP_SCAN_ALREADY_RUNNING
VOP_SCAN_FAILED
VOP_SAFE_ACTION_NOT_ALLOWED
VOP_SAFE_ACTION_STALE
VOP_SAFE_ACTION_NOT_APPLICABLE
VOP_SAFE_ACTION_ALREADY_COMPLETED
VOP_SAFE_ACTION_FAILED
VOP_ACTION_UNKNOWN_OUTCOME
VOP_POLICY_INVALID
VOP_SUPPRESSION_INVALID
VOP_TARGET_NOT_FOUND
VOP_TARGET_WRONG_PROFILE
VOP_STALE_GENERATION
```

---

# 348. Concurrency

Finding reconciliation uses fingerprint + generation.

---

# 349. Safe Action concurrency

Two attempts against same Finding/action must dedupe.

---

# 350. VOP-ACT-011 — Same Safe Fix Cannot Execute Twice Accidentally

---

# 351. Human changes Source during action

Worker reloads current state.

If target changed incompatibly:

```text
BLOCKED_BY_CURRENT_STATE
```

---

# 352. VOP-ACT-012 — Newer Human State Wins

---

# 353. Anti-patterns

`VOP-AP-001`  
VOP directly edits Profile facts.

`VOP-AP-002`  
VOP writes arbitrary SQL fixes.

`VOP-AP-003`  
VOP changes Skill 3→4.

`VOP-AP-004`  
VOP converts “Fluent” to C1 automatically.

`VOP-AP-005`  
VOP publishes missing biography generated by AI.

`VOP-AP-006`  
VOP confirms Emotional Grid cells.

`VOP-AP-007`  
VOP merges duplicate Projects automatically.

`VOP-AP-008`  
VOP rewrites redirected ProfessionalLink URL automatically.

`VOP-AP-009`  
VOP replaces immutable media original.

`VOP-AP-010`  
VOP changes media visibility.

`VOP-AP-011`  
VOP publishes Questionnaire.

`VOP-AP-012`  
VOP creates new historical QuestionnaireRevision automatically.

`VOP-AP-013`  
VOP enables Builder eligibility automatically.

`VOP-AP-014`  
VOP confirms Casting Requirement.

`VOP-AP-015`  
VOP starts new Casting AI analysis without explicit policy/Human intent.

`VOP-AP-016`  
VOP replies to Feedback.

`VOP-AP-017`  
VOP creates Casting from Feedback.

`VOP-AP-018`  
VOP transitions Opportunity.

`VOP-AP-019`  
VOP marks BOOKED.

`VOP-AP-020`  
VOP creates Project credit.

`VOP-AP-021`  
VOP publishes/activates Theme.

`VOP-AP-022`  
VOP chooses color palette autonomously as repair.

`VOP-AP-023`  
VOP sends WhatsApp directly.

`VOP-AP-024`  
VOP retries unknown-outcome external send blindly.

`VOP-AP-025`  
VOP uses unrestricted generic HTTP client.

`VOP-AP-026`  
VOP receives shell access.

`VOP-AP-027`  
VOP treats AI confidence as system health truth.

`VOP-AP-028`  
VOP creates opaque 0–100 actress quality score.

`VOP-AP-029`  
VOP scores attractiveness/marketability.

`VOP-AP-030`  
VOP duplicates domain readiness logic independently.

`VOP-AP-031`  
Failed scan marks all findings resolved.

`VOP-AP-032`  
Stale scan resolves newer issue.

`VOP-AP-033`  
Every scan creates duplicate Finding.

`VOP-AP-034`  
Acknowledgement marks problem fixed.

`VOP-AP-035`  
Suppression mutates Source.

`VOP-AP-036`  
Suppression hides materially different future issue unintentionally.

`VOP-AP-037`  
Recommendation automatically executes without action-class policy.

`VOP-AP-038`  
Admin policy can turn BOOKED transition into safe automation.

`VOP-AP-039`  
Safe action runs without current-state preflight.

`VOP-AP-040`  
Safe action lacks idempotency.

`VOP-AP-041`  
Old queued action overrides newer Human state.

`VOP-AP-042`  
Bulk action skips per-target validation.

`VOP-AP-043`  
VOP scans send full private database to AI.

`VOP-AP-044`  
AI explanation stored as professional fact.

`VOP-AP-045`  
VOP indexes findings publicly.

`VOP-AP-046`  
VOP public API exposes internal health.

`VOP-AP-047`  
Repeated same finding sends WhatsApp every hour.

`VOP-AP-048`  
VOP replaces DB constraints.

`VOP-AP-049`  
VOP automatically fixes ambiguous migration mappings.

`VOP-AP-050`  
Restore re-executes all previous safe actions.

`VOP-AP-051`  
Restore reruns all AI analyses.

`VOP-AP-052`  
Finding evidence retains deleted PII forever.

`VOP-AP-053`  
Link checker can reach localhost/private network.

`VOP-AP-054`  
Broken link auto-archived without Human.

`VOP-AP-055`  
Theme contrast problem auto-activates AI theme.

`VOP-AP-056`  
Booked-without-project finding creates Project automatically.

`VOP-AP-057`  
Overdue inquiry causes autonomous reply.

`VOP-AP-058`  
Notification delivery failure is treated as lost Feedback.

`VOP-AP-059`  
Derived artifact rebuild changes Source revision.

`VOP-AP-060`  
One universal VOP AI agent owns all system authority.

---

# 354. Core invariants

`VOP-INV-027`  
VOP is an Admin-only operational assistant.

`VOP-INV-028`  
VOP observes system state but does not own professional Source.

`VOP-INV-029`  
VOP authority is divided into Observe, Recommend, Safe Automation and Human-only classes.

`VOP-INV-030`  
Unknown/unregistered actions default to Human-only.

`VOP-INV-031`  
Every deterministic Finding has evidence and stable fingerprint.

`VOP-INV-032`  
Repeated scans reconcile existing Findings instead of flooding duplicates.

`VOP-INV-033`  
Acknowledged and Resolved remain different states.

`VOP-INV-034`  
Failed/partial scans cannot falsely resolve unchecked conditions.

`VOP-INV-035`  
Stale scan results cannot override newer system generation.

`VOP-INV-036`  
Health score, if any, remains derivative and non-authoritative.

`VOP-INV-037`  
VOP never creates or modifies professional Profile facts.

`VOP-INV-038`  
VOP never sets Skill level or Language proficiency.

`VOP-INV-039`  
VOP never confirms Emotional Grid cells.

`VOP-INV-040`  
VOP never auto-merges duplicate Source entities.

`VOP-INV-041`  
VOP never rewrites ProfessionalLink URL automatically.

`VOP-INV-042`  
VOP may rebuild derived Media artifacts but never originals.

`VOP-INV-043`  
VOP never changes Media visibility/access.

`VOP-INV-044`  
VOP never publishes Questionnaire.

`VOP-INV-045`  
VOP may regenerate artifact from immutable QuestionnaireRevision without changing content.

`VOP-INV-046`  
VOP never changes Public Builder eligibility.

`VOP-INV-047`  
VOP never confirms Casting Requirements.

`VOP-INV-048`  
VOP never submits Casting materials.

`VOP-INV-049`  
VOP never replies to Feedback.

`VOP-INV-050`  
VOP never creates Casting from Inquiry autonomously.

`VOP-INV-051`  
VOP never changes Opportunity stage or outcome.

`VOP-INV-052`  
VOP never performs Booked→Project conversion autonomously.

`VOP-INV-053`  
VOP never publishes/activates Theme.

`VOP-INV-054`  
VOP never sends directly through WhatsApp/Email providers.

`VOP-INV-055`  
Safe notification retry obeys Notifications module authority/idempotency.

`VOP-INV-056`  
VOP may reindex Search but cannot change Source eligibility.

`VOP-INV-057`  
VOP may invalidate/rebuild Cache but cache remains derived.

`VOP-INV-058`  
Background-job retry is allowed only for declared idempotent tasks.

`VOP-INV-059`  
Unknown external outcome is not automatically retried.

`VOP-INV-060`  
VOP consumes domain readiness/validation services instead of duplicating them.

`VOP-INV-061`  
VOP scans are read-only by default.

`VOP-INV-062`  
Recommendation and Action Execution remain separate objects.

`VOP-INV-063`  
Safe Action Registry is explicit and allowlisted.

`VOP-INV-064`  
VOP receives only narrow typed operational tools.

`VOP-INV-065`  
Safe automation is opt-in by action type.

`VOP-INV-066`  
Every Safe Action revalidates current state before execution.

`VOP-INV-067`  
Safe Actions are idempotent/deduplicated.

`VOP-INV-068`  
Newer Human state wins over stale automation.

`VOP-INV-069`  
Forbidden business authority cannot be enabled through configuration.

`VOP-INV-070`  
Destructive Source actions remain Human-only baseline.

`VOP-INV-071`  
Derived artifact cleanup/rebuild is distinct from Source destruction.

`VOP-INV-072`  
AI in VOP is optional and used for explanation/summarization, not deterministic validity.

`VOP-INV-073`  
AI explanation is non-authoritative.

`VOP-INV-074`  
AI prioritization remains advisory.

`VOP-INV-075`  
Conversational VOP responses must be grounded in current Findings/readiness.

`VOP-INV-076`  
VOP remains distinct from Casting AI, Theme AI and BB Assistant.

`VOP-INV-077`  
Finding suppression does not alter Source.

`VOP-INV-078`  
Deterministic Finding resolution reflects actual current condition.

`VOP-INV-079`  
Notification of Finding is deduplicated and does not directly call provider.

`VOP-INV-080`  
VOP is absent from Public Search/SEO.

`VOP-INV-081`  
VOP private-data access is purpose-scoped.

`VOP-INV-082`  
VOP AI context and logs are data-minimized.

`VOP-INV-083`  
VOP does not score human desirability, attractiveness or marketability.

`VOP-INV-084`  
Readiness is context-specific and domain-grounded.

`VOP-INV-085`  
Policy cannot grant prohibited professional/business authority.

`VOP-INV-086`  
DB constraints remain primary structural guardrails.

`VOP-INV-087`  
Critical security containment is allowed only through predefined narrow actions.

`VOP-INV-088`  
Bulk Safe Actions validate every target independently.

`VOP-INV-089`  
Resolved Finding history remains available.

`VOP-INV-090`  
Retention/privacy rules apply separately to Findings, executions, scans and AI explanations.

`VOP-INV-091`  
Restore reconciles current state before scans/actions resume.

`VOP-INV-092`  
Legacy automation is reviewed before being registered as safe.

`VOP-INV-093`  
Migration revalidates current condition rather than importing old log error as current truth.

`VOP-INV-094`  
VOP workflows remain functional with AI disabled.

`VOP-INV-095`  
All VOP write paths are server-authorized and audit-safe.

---

# 355. E2E-VOP-001 — Full scan

Admin runs `RunVOPScan(ALL)`.

Findings generated from current state.

No Source data changed.

---

# 356. E2E-VOP-002 — Missing Close-Up

No eligible Main Portfolio Close-Up.

VOP creates readiness Finding with evidence.

---

# 357. E2E-VOP-003 — Repeated scan

Condition unchanged.

Same Finding updated, not duplicated.

---

# 358. E2E-VOP-004 — Condition resolved

Human adds valid Close-Up.

Successful new scan resolves Finding.

---

# 359. E2E-VOP-005 — Acknowledge

Admin acknowledges unresolved Finding.

Status becomes ACKNOWLEDGED, not RESOLVED.

---

# 360. E2E-VOP-006 — Suppress

Admin suppresses Finding.

Source unchanged.

---

# 361. E2E-VOP-007 — Failed scan

Scan fails halfway.

Unchecked previous Findings remain open; no false mass-resolution.

---

# 362. E2E-VOP-008 — Stale scan

Source changes while scan runs.

Old scan result cannot resolve newer relevant Finding incorrectly.

---

# 363. E2E-VOP-009 — Skill issue

Legacy Skill requires Human mapping.

VOP recommends review; level unchanged.

---

# 364. E2E-VOP-010 — Language issue

Legacy “Fluent” detected.

No automatic CEFR assignment.

---

# 365. E2E-VOP-011 — Broken link

Safe checker detects broken ProfessionalLink.

VOP opens Finding.

URL unchanged.

---

# 366. E2E-VOP-012 — Redirect

Link redirects.

VOP suggests review/canonical update.

No automatic Source rewrite.

---

# 367. E2E-VOP-013 — SSRF

Stored URL resolves to private network.

Health checker blocks request and creates/records unsafe state without private-network fetch.

---

# 368. E2E-VOP-014 — Missing thumbnail

Derived web thumbnail absent.

VOP offers registered safe action.

---

# 369. E2E-VOP-015 — Rebuild thumbnail

One-click action regenerates derivative.

Original MediaAsset unchanged.

---

# 370. E2E-VOP-016 — Action retry

Lost response, same idempotency key.

One logical derivative rebuild.

---

# 371. E2E-VOP-017 — Current-state preflight

Finding exists, Human fixes manually, then queued action runs.

Result `NO_LONGER_NEEDED`.

---

# 372. E2E-VOP-018 — Immutable media

Attempted VOP original replacement has no registered action and is rejected.

---

# 373. E2E-VOP-019 — Questionnaire stale

Profile changed after QuestionnaireRevision.

VOP flags potential current questionnaire staleness.

Historical Revision unchanged.

---

# 374. E2E-VOP-020 — Failed PDF

Immutable QuestionnaireRevision PDF generation failed.

VOP safely retries artifact generation.

No new QuestionnaireRevision created.

---

# 375. E2E-VOP-021 — Publish forbidden

VOP recommendation attempts Publish Questionnaire.

Action registry rejects as Human-only.

---

# 376. E2E-VOP-022 — Builder readiness

Builder missing eligible Full Body.

Finding created.

No eligibility flag changed.

---

# 377. E2E-VOP-023 — Casting unreviewed

New Casting remains unreviewed beyond configured threshold.

VOP creates Finding.

No status change.

---

# 378. E2E-VOP-024 — Casting Requirement

VOP cannot confirm proposed AI requirement.

---

# 379. E2E-VOP-025 — Casting AI failed retryable

Existing AI request failed due temporary provider error.

Registered technical retry retries same request, not new analysis revision intent.

---

# 380. E2E-VOP-026 — Feedback overdue

Feedback next action overdue.

VOP creates recommendation/notification.

No reply sent.

---

# 381. E2E-VOP-027 — Create Casting forbidden

VOP cannot execute `CreateCastingFromInquiry`.

---

# 382. E2E-VOP-028 — Opportunity stalled

Opportunity in OFFER with overdue next action.

Finding created.

Stage remains OFFER.

---

# 383. E2E-VOP-029 — Booked without Project

BOOKED Opportunity has no Project Draft.

VOP recommends explicit Human conversion.

No Project created.

---

# 384. E2E-VOP-030 — Notification failed

Retryable Notification Delivery failure detected.

VOP can call registered Notifications retry flow.

---

# 385. E2E-VOP-031 — Unknown outcome send

Notification send outcome ambiguous.

VOP cannot blindly retry; Finding requires provider reconciliation.

---

# 386. E2E-VOP-032 — Theme contrast

Active theme fails newly introduced validation.

Finding created.

Theme unchanged.

---

# 387. E2E-VOP-033 — Theme derived bundle

Bundle missing/corrupt.

VOP safely rebuilds derived theme artifact.

ThemeRevision unchanged.

---

# 388. E2E-VOP-034 — Theme activation forbidden

VOP cannot activate recommended theme.

---

# 389. E2E-VOP-035 — Search stale

Public search generation stale.

VOP reindexes.

Public visibility rules remain unchanged.

---

# 390. E2E-VOP-036 — Cache stale

VOP invalidates/rebuilds safe cache generation.

Source unchanged.

---

# 391. E2E-VOP-037 — Duplicate Project candidate

VOP flags likely duplicate.

No merge.

---

# 392. E2E-VOP-038 — Migration ambiguity

Legacy Project/Training mapping ambiguous.

Finding recommends Human review.

No automatic migration mapping.

---

# 393. E2E-VOP-039 — AI explanation

AI produces readable explanation from deterministic Finding.

Finding type/evidence remain authoritative.

---

# 394. E2E-VOP-040 — AI unavailable

Scans, deterministic Findings and Safe Actions continue working.

---

# 395. E2E-VOP-041 — AI hallucination

AI explanation mentions unsupported issue.

Unsupported claim is not persisted as deterministic Finding without check/evidence.

---

# 396. E2E-VOP-042 — Conversational query

Admin asks:

```text
Что мешает анкете быть готовой?
```

Response uses current Questionnaire readiness Findings.

---

# 397. E2E-VOP-043 — Public access

Anonymous user cannot read VOP Findings/dashboard.

---

# 398. E2E-VOP-044 — Search privacy

VOP Finding title does not appear in Public Search.

---

# 399. E2E-VOP-045 — Finding notification

New HIGH Finding creates Notification Intent according to policy.

VOP does not call WhatsApp directly.

---

# 400. E2E-VOP-046 — Notification dedupe

Repeated same Finding scans do not create repeated external alerts every cycle.

---

# 401. E2E-VOP-047 — Severity escalation

Finding moves MEDIUM→HIGH due current state.

Notification policy may emit escalation alert.

---

# 402. E2E-VOP-048 — Safe action opt-out

Admin disables automatic thumbnail rebuild.

Finding remains; no automatic action executes.

---

# 403. E2E-VOP-049 — Forbidden policy toggle

Attempt to configure `AUTO_BOOK_OPPORTUNITY=true`.

Rejected by hard-deny policy.

---

# 404. E2E-VOP-050 — Concurrent Human change

Human edits target while VOP action queued.

Worker reloads and respects newer state.

---

# 405. E2E-VOP-051 — Bulk derivative rebuild

Bulk rebuild validates each media target independently.

---

# 406. E2E-VOP-052 — Scan telemetry

Scan records checks/results without copying full private dataset into logs.

---

# 407. E2E-VOP-053 — PII privacy

Finding related to deleted Contact is redacted according to retention policy.

---

# 408. E2E-VOP-054 — Restore

Restored VOP does not immediately replay all historical Safe Actions.

---

# 409. E2E-VOP-055 — Restore resolved finding

Current-state reconciliation determines whether finding is still resolved/open.

---

# 410. E2E-VOP-056 — Restore AI

Old AI explanation request is not rerun automatically.

---

# 411. E2E-VOP-057 — Legacy repair script

Migration does not register arbitrary old script as safe action until it passes safety requirements.

---

# 412. E2E-VOP-058 — Database constraint

Attempt to create duplicate primary constrained at DB/domain level regardless of VOP.

VOP is not sole defense.

---

# 413. E2E-VOP-059 — Human desirability

No VOP check or dashboard score ranks actress attractiveness/body/marketability.

---

# 414. E2E-VOP-060 — Accessibility

Finding cards, severity and action authority remain keyboard/screen-reader accessible and not color-only.

---

# 415. Architecture diagram

```text
                DOMAIN SOURCES
                      │
                      ▼
          Validation / Readiness APIs
                      │
                      ▼
                 VOP CHECKS
                      │
                      ▼
                  Findings
                      │
          ┌───────────┴───────────┐
          ▼                       ▼
   Recommendations          Notifications
          │
          ▼
   Action classification
   ┌──────┼─────────────┐
   ▼      ▼             ▼
 Read   Safe          Human
 only   action        required
          │
          ▼
 Current-state preflight
          │
          ▼
 Registered narrow command
```

---

# 416. AI separation diagram

```text
VOP
│
├── health/readiness
├── operational findings
└── safe technical actions

Casting AI
│
└── casting extraction/matching

Theme AI
│
└── structured visual draft

BB Assistant
│
└── professional writing draft

Shared AI infrastructure is allowed.
Business authority is not shared.
```

---

# 417. Safe-action boundary

```text
SAFE
├── rebuild thumbnail
├── regenerate PDF artifact
├── reindex search
├── invalidate cache
├── recompute readiness
└── retry declared idempotent technical job

HUMAN ONLY
├── change profile fact
├── set skill/language level
├── confirm casting requirement
├── publish questionnaire
├── activate theme
├── send reply
├── move opportunity stage
└── create professional credit
```

---

# 418. Finding reconciliation diagram

```text
Check condition
      │
 ┌────┴─────┐
 │ exists ? │
 └────┬─────┘
    yes│no
       │
 ┌─────┴────────┐
 ▼              ▼
Open/update   Resolve
Finding       matching Finding
   │
stable fingerprint
```

---

# 419. Safe Action execution

```text
Finding
   ↓
Recommendation
   ↓
Safe Action Registry
   ↓
Current-state reload
   ↓
Generation/version check
   ↓
Idempotency check
   ↓
Typed narrow action
   ↓
Execution result
   ↓
Re-scan / reconcile Finding
```

---

# 420. Readiness diagram

```text
Profile readiness ──────┐
Questionnaire readiness ┤
Builder readiness ──────┤
Media health ───────────┤
Links health ───────────┤
Operational health ─────┤
                        ▼
                 VOP Overview

VOP aggregates.
Domains remain authoritative.
```

---

# 421. Authority diagram

```text
             VOP
              │
       observe/explain
              │
         recommend
              │
       ┌──────┴──────┐
       ▼             ▼
Safe technical     Human
remediation        decision
       │             │
       ▼             ▼
Derived state     Domain command

VOP cannot cross Human authority boundary.
```

---

# 422. Quality gate

Перед implementation должны быть подтверждены:

- [ ] VOP Finding model;
- [ ] stable Finding fingerprint;
- [ ] severity taxonomy;
- [ ] Finding lifecycle;
- [ ] evidence model;
- [ ] domain-readiness reuse;
- [ ] event-driven checks;
- [ ] scheduled scans;
- [ ] on-demand scans;
- [ ] scan reconciliation;
- [ ] stale scan protection;
- [ ] Recommendation model;
- [ ] action classification;
- [ ] Safe Action Registry;
- [ ] hard-deny business actions;
- [ ] narrow typed tools;
- [ ] Safe Action Execution history;
- [ ] current-state preflight;
- [ ] idempotency;
- [ ] action generation/version checks;
- [ ] safe media derivative rebuild;
- [ ] safe PDF artifact regeneration;
- [ ] safe Search reindex;
- [ ] safe Cache invalidation;
- [ ] safe readiness recomputation;
- [ ] SSRF-safe link checks;
- [ ] safe retry rules;
- [ ] unknown-outcome protection;
- [ ] Questionnaire health checks;
- [ ] Builder readiness checks;
- [ ] Casting follow-up checks;
- [ ] Opportunity follow-up checks;
- [ ] Notification health checks;
- [ ] Theme validation checks;
- [ ] migration-health checks;
- [ ] duplicate candidate detection;
- [ ] no professional Source mutation;
- [ ] no publish/send/outcome authority;
- [ ] AI explanation optionality;
- [ ] AI grounding;
- [ ] AI capability separation;
- [ ] Finding suppression;
- [ ] Notification dedupe;
- [ ] Admin-only access;
- [ ] PII minimization;
- [ ] audit/outbox;
- [ ] retention/redaction;
- [ ] restore reconciliation;
- [ ] migration review of old automation;
- [ ] deterministic E2E coverage.

---

# 423. Acceptance criteria

`AC-VOP-001`  
VOP является Admin-only operational assistant и не является professional Source owner.

`AC-VOP-002`  
VOP разделяет Observe, Recommend, Safe Automation и Human-only actions.

`AC-VOP-003`  
Все неразрешённые действия по умолчанию Human-only.

`AC-VOP-004`  
Каждый deterministic Finding имеет explainable evidence и stable fingerprint.

`AC-VOP-005`  
Повторный scan не создаёт duplicate Findings для одного unresolved condition.

`AC-VOP-006`  
Acknowledged Finding не считается Resolved.

`AC-VOP-007`  
Failed/partial scan не закрывает непроверенные Findings.

`AC-VOP-008`  
Stale scan result не может переписать состояние более новой generation.

`AC-VOP-009`  
VOP использует canonical Domain validation/readiness services вместо дублирования business rules.

`AC-VOP-010`  
VOP никогда не создаёт/изменяет Profile professional facts.

`AC-VOP-011`  
VOP не изменяет Skill levels или Language proficiency.

`AC-VOP-012`  
VOP не подтверждает Emotional Grid cells.

`AC-VOP-013`  
VOP не auto-merges duplicate Source entities.

`AC-VOP-014`  
VOP не переписывает ProfessionalLink URL автоматически.

`AC-VOP-015`  
VOP может regenerate derived Media artifacts, не изменяя immutable originals.

`AC-VOP-016`  
VOP не изменяет Media visibility/access.

`AC-VOP-017`  
VOP не публикует Questionnaire.

`AC-VOP-018`  
VOP может безопасно regenerate PDF artifact из существующей immutable QuestionnaireRevision.

`AC-VOP-019`  
VOP не включает Public Builder eligibility автоматически.

`AC-VOP-020`  
VOP не подтверждает Casting Requirements и не отправляет Casting materials.

`AC-VOP-021`  
VOP не отвечает на Feedback и не создаёт Casting from Inquiry автоматически.

`AC-VOP-022`  
VOP не изменяет Opportunity stage/outcome и не выполняет Booked→Project conversion.

`AC-VOP-023`  
VOP не публикует/активирует Themes.

`AC-VOP-024`  
VOP не вызывает WhatsApp/Email provider напрямую.

`AC-VOP-025`  
Safe delivery retry выполняется только через Notifications domain policy.

`AC-VOP-026`  
VOP может reindex Search, но не меняет Source eligibility/visibility.

`AC-VOP-027`  
VOP может invalidate/rebuild Cache без изменения Source.

`AC-VOP-028`  
Background-job retry допускается только для declared idempotent jobs.

`AC-VOP-029`  
Ambiguous external side-effect outcome не подвергается blind retry.

`AC-VOP-030`  
Safe actions хранятся в explicit allowlisted Registry.

`AC-VOP-031`  
VOP не имеет unrestricted SQL/shell/generic mutation authority.

`AC-VOP-032`  
Safe automation включается явно per action type.

`AC-VOP-033`  
Перед Safe Action система повторно загружает current target state.

`AC-VOP-034`  
Safe actions idempotent/deduplicated.

`AC-VOP-035`  
Newer Human state имеет приоритет над stale queued automation.

`AC-VOP-036`  
Configuration не может разрешить hard-denied business authority.

`AC-VOP-037`  
Source deletion/archive remains Human-only baseline.

`AC-VOP-038`  
AI внутри VOP optional и не требуется для deterministic checks.

`AC-VOP-039`  
AI explanation не становится Finding truth без deterministic evidence.

`AC-VOP-040`  
VOP conversational answers grounded in current Findings/readiness.

`AC-VOP-041`  
VOP остаётся отдельным capability от Casting AI, Theme AI и BB Assistant.

`AC-VOP-042`  
Suppression не меняет Source и имеет explicit scope.

`AC-VOP-043`  
Deterministic Finding resolution зависит от фактического disappearance condition.

`AC-VOP-044`  
VOP Finding notifications проходят через Notifications Module и имеют dedupe.

`AC-VOP-045`  
VOP и Findings отсутствуют в Public Search/SEO.

`AC-VOP-046`  
VOP read access и AI context data-minimized/purpose-scoped.

`AC-VOP-047`  
VOP не создаёт attractiveness/body/marketability scores.

`AC-VOP-048`  
Readiness остаётся context-specific и domain-grounded.

`AC-VOP-049`  
DB/domain constraints остаются первичным structural guardrail.

`AC-VOP-050`  
Security containment выполняется только predefined narrow actions.

`AC-VOP-051`  
Bulk Safe Actions валидируют каждый target отдельно.

`AC-VOP-052`  
Resolved Finding history сохраняется.

`AC-VOP-053`  
Finding/Action/Scan/AI retention и privacy управляются раздельно.

`AC-VOP-054`  
Restore выполняет current-state reconciliation до возобновления automation.

`AC-VOP-055`  
Migration не превращает legacy scripts/cron jobs в safe automation без review.

`AC-VOP-056`  
Legacy log error не становится current Finding без revalidation.

`AC-VOP-057`  
VOP остаётся функциональным при полностью отключённом AI.

`AC-VOP-058`  
Все safe action, stale-state, idempotency, privacy и authority boundaries имеют deterministic E2E coverage.

---

# 424. Финальная доктрина

> **Virtual Portfolio Operator является внутренним operational-control capability, а не владельцем профессиональной истины. VOP получает canonical domain readiness/validation state, формирует explainable Findings с evidence и stable fingerprint, предлагает Recommendations и может исполнять только зарегистрированные narrow, idempotent Safe Actions — например пересборку производного media artifact, PDF regeneration из immutable revision, reindex Search, Cache invalidation или retry заведомо idempotent technical job. Любое действие, которое изменяет смысл профессиональных данных или business outcome — Skill/Language, Contacts, Visibility, Casting Requirements, Questionnaire publication, Theme activation, Feedback response, Opportunity stage, BOOKED или Project credit — остаётся строго Human-controlled и не может быть разрешено VOP даже configuration toggle. AI внутри VOP используется только как опциональный слой объяснения и навигации поверх deterministic system state и не заменяет Domain rules.**