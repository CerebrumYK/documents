# OPPORTUNITY PIPELINE MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация professional opportunity pipeline, stage history, next actions, Casting/Feedback provenance, Booked → Project/Role Draft и conversion analytics

**Целевой файл:** `docs/modules/opportunity-pipeline.md`  
**Документ:** DOC-126  
**Статус:** ✅ Completed  
**Тип:** Module / Opportunity / Professional Pipeline / Stage History / Provenance / Conversion

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
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
- `docs/modules/projects.md`
- `docs/modules/questionnaires.md`
- `docs/modules/public-questionnaire-builder.md`
- `docs/modules/castings.md`
- `docs/modules/casting-ai-analysis.md`
- `docs/modules/feedback.md`
- `docs/modules/notifications.md`

---

# 1. Назначение модуля

Opportunity Pipeline Module управляет **профессиональной возможностью от первичного интереса до фактического результата**.

Он отвечает за:

- единый pipeline;
- current stage;
- immutable stage history;
- provenance;
- связи с Casting и Feedback;
- next action;
- responsible Admin;
- deadlines/follow-ups;
- materials/request context;
- outcome;
- Booked workflow;
- Project/Role Draft creation;
- conversion analytics;
- workflow integrity;
- audit.

---

# 2. Главная доктрина

> **Opportunity является самостоятельным профессиональным workflow object. Его текущая стадия изменяется только через валидированный Domain Transition и всегда создаёт append-only Stage History. Casting, Feedback, AI Recommendation, Notification, Analytics и Project не имеют права напрямую переписывать stage. `BOOKED` означает подтверждённый положительный outcome, но даже он не публикует профессиональный Project/Role: только отдельное Human-controlled idempotent действие создаёт Project/Role Draft с bidirectional provenance.**

Canonical:

```text
Feedback / Casting / Manual Lead
              ↓
         Opportunity
              ↓
          NEW
              ↓
          QUALIFIED
              ↓
    MATERIALS_REQUESTED
              ↓
    SELF_TAPE_REQUESTED
              ↓
          AUDITION
              ↓
          CALLBACK
              ↓
           OFFER
              ↓
          BOOKED
              ↓
      Human explicit action
              ↓
      Project / Role Draft
```

Alternative terminal outcomes:

```text
CLOSED_NOT_SELECTED
WITHDRAWN
```

---

# 3. Fundamental separation

```text
Opportunity
≠ Casting
≠ Feedback
≠ Project
≠ Role
≠ OpportunityStageHistory
≠ NextAction
≠ Notification
≠ AI Recommendation
≠ Analytics Event
```

---

# 4. Module identifiers

Используются:

```text
OPP-*
OPP-STG-*
OPP-HST-*
OPP-SRC-*
OPP-NXT-*
OPP-OWN-*
OPP-BKD-*
OPP-PRJ-*
OPP-ANL-*
OPP-AI-*
OPP-SEC-*
OPP-INV-*
OPP-AP-*
E2E-OPP-*
```

---

# 5. Business purpose

Pipeline должен позволять в любой момент ответить:

1. Какие реальные professional opportunities открыты?
2. На какой стадии каждая возможность?
3. Что нужно сделать следующим?
4. Кто отвечает?
5. Откуда opportunity возникла?
6. Какой Casting/Feedback с ней связан?
7. Какие материалы уже были запрошены?
8. Был ли self-tape/audition/callback?
9. Есть ли offer?
10. Получена ли роль?
11. Если получена — создан ли Project/Role Draft?
12. Где и почему opportunity была закрыта?

---

# 6. Canonical stages

Точный canonical pipeline:

```text
NEW
QUALIFIED
MATERIALS_REQUESTED
SELF_TAPE_REQUESTED
AUDITION
CALLBACK
OFFER
BOOKED
CLOSED_NOT_SELECTED
WITHDRAWN
```

---

# 7. OPP-STG-001 — Stage Names Are Stable Machine Values

UI может показывать локализованные названия:

```text
Новая
Квалифицирована
Запрошены материалы
Запрошен self-tape
Прослушивание
Callback
Предложение
Роль получена
Закрыта — не выбраны
Отозвана
```

Но бизнес-логика работает только с machine enum.

---

# 8. NEW

Opportunity зафиксирована, но ещё не подтверждена как достаточно конкретная/релевантная для дальнейшей работы.

---

# 9. QUALIFIED

Human подтвердил:

> это реальная professional opportunity, которую стоит вести через pipeline.

---

# 10. MATERIALS_REQUESTED

Внешняя сторона запросила:

- questionnaire;
- photos;
- portfolio;
- showreel;
- video;
- additional professional materials.

---

# 11. SELF_TAPE_REQUESTED

Явно запрошен self-tape.

---

# 12. AUDITION

Произошло или подтверждено прослушивание/кастинг-аудишн.

---

# 13. CALLBACK

Получен последующий вызов/следующий раунд.

---

# 14. OFFER

Получено явное предложение роли/работы.

---

# 15. BOOKED

Роль/работа подтверждена как полученная.

---

# 16. CLOSED_NOT_SELECTED

Возможность закончилась без выбора актрисы.

---

# 17. WITHDRAWN

Opportunity была снята с дальнейшего участия по решению актрисы/представителя либо по иному подтверждённому внутреннему решению.

---

# 18. OPP-INV-001 — Terminal Outcome Is Explicit

Не использовать один общий:

```text
CLOSED
```

без результата.

---

# 19. Почему `CLOSED_NOT_SELECTED` отдельно

Чтобы analytics различала:

```text
not selected
```

от:

```text
withdrawn
```

---

# 20. Opportunity entity

Suggested logical entity:

```text
opportunities
```

---

# 21. Representative fields

```text
id
profile_id
title
current_stage
source_type
source_feedback_id?
source_casting_id?
project_name?
role_name?
responsible_admin_id?
priority?
next_action?
next_action_at?
started_at
qualified_at?
booked_at?
closed_at?
withdrawn_at?
version
created_at
updated_at
archived_at?
```

---

# 22. Title

Administrative/pipeline identity.

Example:

```text
Сериал «X» — роль врача
```

---

# 23. OPP-INV-002 — Opportunity Title Is Not Professional Credit

Title alone does not become Project/Role fact.

---

# 24. Unknown project/role names

Remain absent/unknown.

Do not invent names for pipeline convenience.

---

# 25. Source types

Recommended:

```text
CASTING
FEEDBACK
MANUAL
REFERRAL
DIRECT_CONTACT
OTHER
```

---

# 26. OPP-SRC-001 — Source Type Is Provenance

Not stage.

---

# 27. Opportunity from Casting

Preferred for casting workflows:

```text
Casting
↓
Human Create Opportunity
↓
Opportunity
```

---

# 28. OPP-SRC-002 — Casting Does Not Auto-Create Opportunity

---

# 29. Opportunity from Feedback

Possible for:

- collaboration;
- direct role;
- other professional opportunity.

---

# 30. For casting-specific Feedback

Preferred path:

```text
Feedback
→ Casting
→ Opportunity
```

---

# 31. Direct Feedback → Opportunity

Allowed only when Casting domain semantics are unnecessary.

---

# 32. OPP-SRC-003 — Provenance Path Must Remain Explicit

---

# 33. Manual Opportunity

Admin can create from:

- phone call;
- referral;
- meeting;
- offline conversation;
- known producer contact.

---

# 34. Manual source

Requires a Human-entered source note/context if useful.

---

# 35. OPP-INV-003 — Manual Opportunity Is Valid Without Casting

---

# 36. Source provenance

Recommended generic relation:

```text
opportunity_sources
```

or explicit source FKs/relations.

---

# 37. Provenance must support bidirectional navigation where source entity exists.

---

# 38. Casting link

```text
Opportunity → Casting
Casting → Opportunity
```

---

# 39. Feedback link

```text
Opportunity → Feedback
Feedback → Opportunity
```

where direct relation exists.

---

# 40. OPP-SRC-004 — Provenance Does Not Merge Domains

---

# 41. Stage history

Critical entity:

```text
opportunity_stage_history
```

---

# 42. Every accepted stage transition creates immutable history.

---

# 43. Suggested fields

```text
id
opportunity_id
from_stage
to_stage
changed_by
changed_at
reason_code?
reason_note?
source_event_type?
source_entity_type?
source_entity_id?
```

---

# 44. OPP-HST-001 — Stage History Is Append-Only

---

# 45. Current stage

Stored on Opportunity for efficient current query.

---

# 46. History

Remains complete authoritative transition history.

---

# 47. OPP-HST-002 — Current Stage and Stage History Must Agree

---

# 48. Atomic transition

```text
BEGIN
  lock/reload Opportunity
  validate expectedVersion
  validate from_stage
  validate transition
  update current_stage
  insert StageHistory
  update stage-specific timestamp
  Audit
  Outbox
COMMIT
```

---

# 49. OPP-HST-003 — Stage + History Commit Atomically

---

# 50. No history after-the-fact

Forbidden:

```text
UPDATE opportunity
COMMIT
later insert history
```

---

# 51. Transition service

Canonical application/domain service:

```text
TransitionOpportunityStage
```

---

# 52. OPP-STG-002 — Direct Arbitrary Stage Assignment Forbidden

---

# 53. Valid progression

Canonical forward path:

```text
NEW
→ QUALIFIED
→ MATERIALS_REQUESTED
→ SELF_TAPE_REQUESTED
→ AUDITION
→ CALLBACK
→ OFFER
→ BOOKED
```

---

# 54. Real-world workflow is not always linear

Opportunity may legitimately skip stages.

Examples:

```text
QUALIFIED → AUDITION
QUALIFIED → OFFER
MATERIALS_REQUESTED → CALLBACK
```

if that reflects actual history.

---

# 55. OPP-STG-003 — Pipeline Allows Explicit Stage Skips

But stage skip is:

- Human-controlled;
- audited;
- optionally reasoned.

---

# 56. No fake intermediate stages

If opportunity came directly as Offer:

do not fabricate:

```text
Materials Requested
Self-Tape
Audition
Callback
```

with fake timestamps.

---

# 57. OPP-STG-004 — Pipeline Represents Reality, Not Idealized Sequence

---

# 58. Backward transitions

Need controlled handling.

Example:

```text
CALLBACK → AUDITION
```

may represent correction, not actual pipeline movement.

---

# 59. Preferred semantics

For correction:

use explicit administrative correction command with Audit, or new transition with reason if history must reflect correction.

---

# 60. OPP-STG-005 — Stage Correction Is Explicit

Do not silently rewrite history.

---

# 61. Terminal stages

```text
BOOKED
CLOSED_NOT_SELECTED
WITHDRAWN
```

are outcome states.

---

# 62. Reopening

May be allowed through explicit:

```text
ReopenOpportunity
```

with reason.

---

# 63. OPP-STG-006 — Terminal Reopen Requires Human Reason

---

# 64. BOOKED is special terminal-positive stage

Booked may still trigger post-booking actions.

---

# 65. BOOKED does not mean:

- Project published;
- Role published;
- professional credit finalized.

---

# 66. OPP-BKD-001 — Booked ≠ Published Credit

---

# 67. BOOKED transition requirements

At minimum:

- Human-authenticated action;
- valid current Opportunity;
- explicit confirmation of booking outcome.

---

# 68. Optional contextual fields

May record:

```text
booking_confirmation_note
booking_date
```

if business value exists.

---

# 69. No AI booking

AI cannot infer BOOKED from:

```text
“sounds promising”
“we like her”
“probably selected”
```

---

# 70. OPP-AI-001 — AI Cannot Transition to BOOKED

---

# 71. No analytics booking

Conversion report cannot mark stage.

---

# 72. OPP-ANL-001 — Analytics Is Read-Only

---

# 73. OFFER vs BOOKED

Offer means:

> external offer exists.

Booked means:

> role/work accepted/confirmed as won according to Human-confirmed workflow.

---

# 74. OPP-INV-004 — Offer ≠ Booked

---

# 75. Offer detail

Optional fields/entities may later capture:

- terms;
- fee;
- dates;
- conditions.

Not required baseline unless product scope expands.

---

# 76. Sensitive commercial data

If added, private Admin-only.

---

# 77. Opportunity → Project/Role Draft

Critical post-booking workflow.

---

# 78. Canonical:

```text
BOOKED
↓
Human: Create Project/Role Draft
↓
Project Draft
↓
Role Draft
↓
Provenance link
```

---

# 79. Command

```text
CreateProjectRoleDraftFromBookedOpportunity
```

---

# 80. OPP-PRJ-001 — Command Is Explicit Human Action

---

# 81. Preconditions

At minimum:

```text
authenticated Admin
Opportunity exists
current_stage = BOOKED
same profile
no conflicting prior completed conversion
source provenance valid
idempotency key valid
```

---

# 82. Human review

Before command or inside workflow Admin should confirm:

- Project title if known;
- project type if known;
- Role/character name if known;
- what information is actually confirmed.

---

# 83. OPP-PRJ-002 — Unknown Credits Remain Unknown

---

# 84. Draft only

Result:

```text
Project.lifecycle = DRAFT
Role.lifecycle = DRAFT
```

---

# 85. Never:

```text
PUBLISHED
show_on_public_site=true automatically
homepage featured automatically
```

---

# 86. OPP-PRJ-003 — Booked Conversion Never Auto-Publishes

---

# 87. Existing Project

If Project already exists:

Human should be able to choose:

```text
Use existing Project
```

and create/link Role Draft rather than duplicate Project.

---

# 88. OPP-PRJ-004 — Existing Project Reuse Preferred Over Duplicate

---

# 89. Existing Role

If appropriate Role already exists:

link provenance rather than duplicate.

---

# 90. Identity matching

Never based solely on fuzzy title.

Human confirms existing entity.

---

# 91. OPP-PRJ-005 — AI/Fuzzy Match Cannot Auto-Merge Credits

---

# 92. Provenance

Created Project/Role Draft must preserve:

```text
source Opportunity
source Casting?
source Feedback?
```

where available.

---

# 93. Bidirectional:

```text
Opportunity → Project/Role Draft
Project/Role → source Opportunity
```

---

# 94. OPP-PRJ-006 — Provenance Is Bidirectional

---

# 95. Draft seed data

May include only confirmed known values:

```text
project_name
role_name
project_type if confirmed
```

---

# 96. No copied AI speculation.

---

# 97. OPP-PRJ-007 — Draft Seed Uses Confirmed Facts Only

---

# 98. Casting AI analysis

Can provide recommendations for review.

It is not Project Draft Source.

---

# 99. If a name originated from confirmed Casting source:

provenance can point back through Opportunity/Casting.

---

# 100. Idempotency

`CreateProjectRoleDraftFromBookedOpportunity` MUST be idempotent.

---

# 101. Lost response

Retry same key returns same created Draft identities.

---

# 102. OPP-PRJ-008 — Duplicate Conversion Is Prevented

---

# 103. Concurrent conversion

Two Admin users click simultaneously.

One canonical conversion wins.

---

# 104. OPP-PRJ-009 — Booked Conversion Is Race-Safe

---

# 105. Conversion status

Opportunity may expose:

```text
project_conversion_state
```

as derived/config state:

```text
NOT_STARTED
DRAFT_CREATED
LINKED_EXISTING
```

---

# 106. Do not create another pipeline stage:

```text
PROJECT_CREATED
```

unless business lifecycle explicitly requires it.

---

# 107. OPP-INV-005 — Post-Booking Conversion Is Orthogonal to Stage

---

# 108. Next Action

Opportunity supports:

```text
next_action
next_action_at
```

---

# 109. Examples

```text
Prepare portfolio
Send questionnaire
Record self-tape
Confirm audition time
Follow up
Review offer
Create Project/Role Draft
```

---

# 110. OPP-NXT-001 — Next Action Is Work Planning

It is not automatic execution.

---

# 111. Next Action completion

Can be cleared/replaced by Human.

---

# 112. Scheduler/VOP

May identify:

```text
due
overdue
```

---

# 113. OPP-NXT-002 — Overdue Reminder Does Not Execute Action

---

# 114. Next action due date

Stored with explicit timezone semantics if time-specific.

---

# 115. Date-only follow-up

Use date semantics rather than fake midnight timestamp where appropriate.

---

# 116. Responsible Admin

```text
responsible_admin_id
```

optional.

---

# 117. OPP-OWN-001 — Responsibility Is Internal

Does not affect Public Contacts.

---

# 118. Assignment

Human-controlled.

---

# 119. VOP may suggest unassigned Opportunities.

Cannot silently assign business owner unless explicit deterministic admin policy allows technical routing.

---

# 120. Priority

Optional:

```text
LOW
NORMAL
HIGH
URGENT
```

---

# 121. Human-controlled baseline.

---

# 122. Deadline-related urgency

System may recommend priority.

Does not change it autonomously.

---

# 123. OPP-AI-002 — AI/VOP Cannot Set Final Priority

---

# 124. Opportunity notes

Private Admin notes may exist.

---

# 125. Recommended append-oriented entity:

```text
opportunity_notes
```

---

# 126. Notes not stage history.

---

# 127. OPP-INV-006 — Note ≠ Stage Transition Evidence

---

# 128. Stage reason

Transition can have structured reason/note distinct from general notes.

---

# 129. CLOSED_NOT_SELECTED reason

May optionally classify:

```text
OTHER_CANDIDATE_SELECTED
NO_RESPONSE
ROLE_CHANGED
PROJECT_CANCELLED
OTHER
UNKNOWN
```

only if known.

---

# 130. OPP-STG-007 — Closed Reason Is Optional, Never Invented

---

# 131. WITHDRAWN reason

Examples:

```text
SCHEDULE_CONFLICT
TERMS
LOCATION
PERSONAL_DECISION
OTHER
```

Human supplied.

---

# 132. Do not infer withdrawal reason.

---

# 133. Materials integration

Opportunity may reference:

- questionnaire;
- portfolio;
- self-tape;
- other submitted materials.

---

# 134. But detailed requested/submitted material tracking remains primarily Casting/Material workflow where Casting exists.

---

# 135. OPP-INV-007 — Opportunity Does Not Duplicate Entire Casting Material State

---

# 136. Opportunity may project summary:

```text
Materials requested
Self-tape requested
```

from stage/history/linked Casting.

---

# 137. Stage transition to MATERIALS_REQUESTED

Should only occur when actual request is known.

---

# 138. OPP-STG-008 — Generated Materials ≠ Materials Requested

---

# 139. SELF_TAPE_REQUESTED

Only when external request exists.

---

# 140. Recording a self-tape voluntarily does not automatically imply this stage.

---

# 141. OPP-STG-009 — Self-Tape Exists ≠ Self-Tape Requested

---

# 142. AUDITION

Should correspond to confirmed audition state/event.

---

# 143. Audition scheduled vs occurred

If product needs distinction later, model event details separately.

Do not overload current stage with unbounded scheduling semantics.

---

# 144. Callback

Represents real callback/next round.

---

# 145. OPP-STG-010 — Callback Must Be Explicitly Supported by Source/Human Confirmation

---

# 146. Offer

Requires explicit offer evidence/Human confirmation.

---

# 147. Opportunity dates

Potential:

```text
created_at
qualified_at
materials_requested_at
self_tape_requested_at
audition_at?
callback_at?
offer_at?
booked_at?
closed_at?
withdrawn_at?
```

---

# 148. Stage-specific timestamps

Derived/set during transition, not manually guessed.

---

# 149. OPP-HST-004 — Timestamp Represents Actual Recorded Transition Time Unless Exact External Event Time Is Explicitly Stored Separately

---

# 150. Backdating

For historical import, explicit event timestamp may be provided by privileged migration/admin workflow.

---

# 151. Must distinguish:

```text
recorded_at
occurred_at
```

if both needed.

---

# 152. OPP-HST-005 — Do Not Fake Historical Event Time From Record Creation Time

---

# 153. Search

Admin Opportunity search/filter supports:

```text
title
project
role
stage
responsible
priority
next action
source Casting
source Feedback
date range
```

---

# 154. Public Search

Never.

---

# 155. OPP-SEC-001 — Opportunity Is Admin-Private

---

# 156. SEO

None.

---

# 157. Public Profile

Opportunity stage never appears publicly.

Only resulting Human-reviewed Project/Role may later be published.

---

# 158. OPP-SEC-002 — Pipeline Data Never Leaks Through Project Public Projection

---

# 159. Opportunity Dashboard

Recommended views:

```text
Pipeline
Needs Action
Due Today
Overdue
Offers
Booked
Closed
```

---

# 160. Kanban

Pipeline is suitable for Kanban visualization.

---

# 161. OPP-UX-001 — Dragging Card Is Stage Transition Command

Not client-only reorder.

---

# 162. Kanban drag

Must invoke server transition validation.

---

# 163. Invalid drop

Rejected and UI restored.

---

# 164. OPP-UX-002 — UI Cannot Bypass Transition Rules

---

# 165. List/table

Also needed for precise filtering/history.

---

# 166. Opportunity card

Recommended:

```text
title
role/project
current stage
source
responsible
next action
due date
priority
```

---

# 167. Detail page

Recommended:

1. Overview;
2. Current Stage;
3. Stage History;
4. Source/Provenance;
5. Casting;
6. Feedback;
7. Materials summary;
8. Next Action;
9. Notes;
10. Notifications;
11. Booked Conversion;
12. Audit.

---

# 168. Stage history UI

Chronological timeline:

```text
NEW
↓
QUALIFIED
↓
AUDITION
↓
CALLBACK
↓
OFFER
```

Skipped stages remain visibly absent.

---

# 169. OPP-UX-003 — Timeline Must Not Fabricate Missing Stages

---

# 170. Stage transition dialog

For high-impact states:

```text
BOOKED
CLOSED_NOT_SELECTED
WITHDRAWN
```

request optional/required contextual confirmation.

---

# 171. BOOKED confirmation

Should clearly state:

> This marks the opportunity as successfully booked. It does not publish a Project automatically.

---

# 172. OPP-UX-004 — Positive Outcome Is Explicit

---

# 173. Project Draft CTA after Booked

Display:

```text
Создать проект/роль
```

or:

```text
Связать с существующим проектом
```

---

# 174. If conversion already completed

Display existing Project/Role links.

---

# 175. OPP-UX-005 — No Duplicate Conversion CTA After Success

---

# 176. Analytics

Opportunity Pipeline is primary source for professional conversion analytics.

---

# 177. Funnel:

```text
NEW
→ QUALIFIED
→ MATERIALS_REQUESTED
→ SELF_TAPE_REQUESTED
→ AUDITION
→ CALLBACK
→ OFFER
→ BOOKED
```

---

# 178. Analytics can compute:

- count per stage;
- conversion rate;
- time-to-stage;
- time-to-book;
- source attribution;
- casting-to-booking rate;
- feedback-to-opportunity rate;
- offer-to-booking rate;
- closed reason distribution.

---

# 179. OPP-ANL-002 — Analytics Reads Stage History

Do not maintain separate editable analytics stage.

---

# 180. Conversion formula example

```text
Booked / Qualified
```

within defined cohort/timeframe.

---

# 181. Stage skip handling

Analytics must use actual history/current outcome.

---

# 182. Example:

```text
QUALIFIED → OFFER
```

should not fabricate Materials/Self-Tape/Audition conversion events.

---

# 183. OPP-ANL-003 — Analytics Respects Actual Path

---

# 184. Source attribution

Can compare:

```text
Casting
Feedback
Referral
Direct
Campaign
```

---

# 185. Marketing analytics may consume aggregate pipeline outcomes.

---

# 186. Critical boundary

Analytics cannot:

- transition stages;
- close opportunity;
- mark Booked;
- create Project;
- modify priority.

---

# 187. OPP-ANL-004 — Metrics Never Become Workflow Authority

---

# 188. Predictive analytics

Future AI may estimate likelihood.

If implemented:

- advisory only;
- no stage mutation;
- no ranking based on appearance;
- clearly labelled estimate.

---

# 189. Baseline

No win-probability AI required.

---

# 190. OPP-AI-003 — AI Scoring Is Not Baseline

---

# 191. Casting AI integration

Casting AI may recommend:

```text
This casting appears actionable
Prepare questionnaire
Relevant project X
```

---

# 192. It cannot create/qualify Opportunity.

---

# 193. OPP-AI-004 — AI Recommendation ≠ QUALIFIED

---

# 194. BB Assistant

Can draft:

- response;
- follow-up;
- offer reply;
- cover note.

---

# 195. BB Draft does not change stage.

---

# 196. OPP-AI-005 — Generated Reply ≠ Stage Transition

---

# 197. VOP

May detect:

```text
NEW opportunity not reviewed
next action overdue
Offer with no follow-up
Booked without Project Draft
Opportunity stalled
Casting/Opportunity state inconsistency
```

---

# 198. VOP can recommend.

---

# 199. VOP deterministic safe actions

Could:

- recompute dashboard;
- reindex search;
- refresh derived counters;
- trigger allowed reminder notification.

---

# 200. VOP cannot:

```text
QUALIFY
AUDITION
CALLBACK
OFFER
BOOKED
CLOSE
WITHDRAW
CREATE PROJECT/ROLE
```

autonomously.

---

# 201. OPP-INV-008 — VOP Has No Outcome Authority

---

# 202. Notifications integration

Potential intents:

```text
OPPORTUNITY_CREATED
OPPORTUNITY_NEXT_ACTION_DUE
OPPORTUNITY_OVERDUE
OPPORTUNITY_OFFER_RECEIVED
OPPORTUNITY_BOOKED
OPPORTUNITY_BOOKED_PROJECT_DRAFT_MISSING
```

---

# 203. Notification remains derived attention.

---

# 204. NOTIFICATION failure

Never rolls back transition.

---

# 205. OPP-INV-009 — Stage Commit Precedes Notification

---

# 206. Transition flow

```text
Human command
↓
validate
↓
DB TX
  update Opportunity
  append Stage History
  Audit
  Outbox
COMMIT
↓
Notification/Analytics async
```

---

# 207. Search/index/cache updates after commit.

---

# 208. OPP-INV-010 — External Side Effects Occur Post-Commit

---

# 209. Concurrency

Opportunity has:

```text
version
```

---

# 210. Every mutable high-impact command includes:

```text
expectedVersion
```

---

# 211. OPP-INV-011 — Optimistic Concurrency Mandatory

---

# 212. Stage transition race

Admin A:

```text
QUALIFIED → AUDITION
```

Admin B:

```text
QUALIFIED → CLOSED_NOT_SELECTED
```

simultaneously.

Only one transition commits against current version.

---

# 213. OPP-STG-011 — Stage Transition Race Cannot Produce Split Current State

---

# 214. Stage history uniqueness

One committed transition event per successful command.

---

# 215. Retry

Idempotency recommended/required for high-impact stage transitions.

Especially:

```text
BOOKED
terminal close
Project/Role Draft conversion
```

---

# 216. OPP-INV-012 — Duplicate Network Retry Cannot Append Duplicate Stage History

---

# 217. Idempotency fingerprint

Includes:

```text
opportunity_id
from_stage
to_stage
semantic transition payload
```

---

# 218. Same key + changed target

Conflict.

---

# 219. OPP-INV-013 — Idempotency Key Cannot Be Reused for Different Outcome

---

# 220. Normal Next Action edit

Optimistic concurrency usually sufficient.

---

# 221. Opportunity creation

May require idempotency when created from Casting/Feedback.

---

# 222. Create from Casting

Canonical:

```text
CreateOpportunityFromCasting
```

---

# 223. Preconditions

```text
Casting exists
Human authorized
not already linked under baseline one-opportunity-per-casting rule
same profile
```

---

# 224. OPP-SRC-005 — Create From Casting Is Idempotent

---

# 225. Default initial stage

Usually:

```text
NEW
```

---

# 226. Human may explicitly create as QUALIFIED if opportunity is already clearly qualified and command supports it.

---

# 227. If historical import at later stage

Use explicit privileged/historical creation path.

---

# 228. No fake stage history.

---

# 229. One Casting → Opportunity cardinality

Baseline:

```text
Casting 0..1 Opportunity
```

---

# 230. If future needs multiple opportunities from one Casting:

must be deliberate data-model change.

---

# 231. OPP-SRC-006 — Cardinality Guard Is Server/DB Protected

---

# 232. Feedback direct opportunity

Baseline may allow:

```text
Feedback 0..1 direct Opportunity
```

where not converted through Casting.

---

# 233. Prevent ambiguous double source

If Feedback already converted to Casting and that Casting owns an Opportunity, creating a second direct Opportunity should warn/block unless explicitly intended.

---

# 234. OPP-SRC-007 — Duplicate Pipeline Creation Across Provenance Paths Is Guarded

---

# 235. Profile ownership

Every Opportunity belongs to one `profile_id`.

---

# 236. Same-profile invariant applies to linked:

- Casting;
- Feedback;
- Project/Role Draft.

---

# 237. OPP-SEC-003 — Cross-Profile Linking Rejected

---

# 238. API commands

Canonical:

```text
CreateOpportunity
CreateOpportunityFromCasting
CreateOpportunityFromFeedback
TransitionOpportunityStage
CorrectOpportunityStage
ReopenOpportunity
AssignOpportunityResponsible
SetOpportunityPriority
SetOpportunityNextAction
ClearOpportunityNextAction
AddOpportunityNote
CreateProjectRoleDraftFromBookedOpportunity
LinkBookedOpportunityToExistingProjectRole
ArchiveOpportunity
RestoreOpportunity
```

---

# 239. Queries

```text
GetOpportunity
ListOpportunities
GetOpportunityPipeline
GetOpportunityStageHistory
GetOpportunityTimeline
GetOpportunityNextActions
GetOverdueOpportunities
GetOpportunityProvenance
GetOpportunityBookedConversion
GetOpportunityAnalyticsProjection
```

---

# 240. Transition input

Conceptually:

```text
opportunityId
toStage
expectedVersion
reasonCode?
reasonNote?
occurredAt?
idempotencyKey
```

---

# 241. Client cannot set

```text
fromStage
stageHistory
changedBy
bookedAt
closedAt
conversionRate
createdProjectId
```

as authority.

---

# 242. OPP-SEC-004 — Server Derives Transition Authority Fields

---

# 243. `fromStage`

Server reads current.

---

# 244. `changedBy`

Authenticated principal.

---

# 245. stage timestamps

Server/domain determines according to transition.

---

# 246. Historical `occurredAt`

Only allowed through validated privileged flow.

---

# 247. Next Action command

Conceptually:

```text
opportunityId
text/actionType
dueAt?
expectedVersion
```

---

# 248. Structured next action type recommended

Possible:

```text
REVIEW
PREPARE_MATERIALS
SEND_MATERIALS
RECORD_SELF_TAPE
FOLLOW_UP
CONFIRM_AUDITION
REVIEW_OFFER
CREATE_PROJECT_DRAFT
CUSTOM
```

---

# 249. Free-text note optional.

---

# 250. OPP-NXT-003 — Structured Action Type Helps Dashboard/Notifications

---

# 251. Project conversion input

Conceptually:

```text
opportunityId
mode: CREATE_NEW | LINK_EXISTING
projectId?
roleId?
projectTitle?
projectType?
roleName?
expectedVersion
idempotencyKey
```

---

# 252. `CREATE_NEW`

Creates Draft Project and Draft Role from confirmed input.

---

# 253. `LINK_EXISTING`

Links provenance to selected existing project/role.

---

# 254. Server checks current:

```text
BOOKED
same profile
target eligibility
conversion not already completed
```

---

# 255. OPP-PRJ-010 — Browser Cannot Force Published State During Conversion

---

# 256. Error taxonomy

At minimum:

```text
OPPORTUNITY_NOT_FOUND
OPPORTUNITY_WRONG_PROFILE
OPPORTUNITY_STAGE_INVALID
OPPORTUNITY_TRANSITION_INVALID
OPPORTUNITY_ALREADY_TERMINAL
OPPORTUNITY_REOPEN_REASON_REQUIRED
OPPORTUNITY_STALE_VERSION
OPPORTUNITY_SOURCE_NOT_FOUND
OPPORTUNITY_CASTING_ALREADY_LINKED
OPPORTUNITY_FEEDBACK_ALREADY_LINKED
OPPORTUNITY_DUPLICATE_SOURCE_PIPELINE
OPPORTUNITY_NEXT_ACTION_INVALID
OPPORTUNITY_PRIORITY_INVALID
OPPORTUNITY_BOOKED_REQUIRED
OPPORTUNITY_PROJECT_CONVERSION_ALREADY_COMPLETED
OPPORTUNITY_PROJECT_WRONG_PROFILE
OPPORTUNITY_ROLE_WRONG_PROFILE
OPPORTUNITY_PROJECT_CONVERSION_CONFLICT
OPPORTUNITY_IDEMPOTENCY_CONFLICT
```

---

# 257. Public error surface

Opportunity is Admin-private.

Do not expose existence via public IDs.

---

# 258. Admin authorization

Only authorized Admin roles can:

- create;
- transition;
- close;
- book;
- convert to Project Draft.

---

# 259. OPP-SEC-005 — Stage Transition Is Privileged Business Action

---

# 260. Audit

Must capture:

```text
Opportunity creation
Source/provenance link
Every stage transition
Stage correction
Reopen
Responsible change
Priority change
Next action change
Booked conversion
Project/Role linkage
Archive/restore
```

---

# 261. StageHistory vs Audit

Both are required for different purposes.

---

# 262. StageHistory answers:

> What stages did this Opportunity traverse?

---

# 263. Audit answers:

> Who invoked what system action, with what context?

---

# 264. OPP-HST-006 — StageHistory ≠ Audit

---

# 265. Outbox events

Suggested:

```text
OpportunityCreated
OpportunityStageChanged
OpportunityQualified
OpportunityMaterialsRequested
OpportunitySelfTapeRequested
OpportunityAudition
OpportunityCallback
OpportunityOffer
OpportunityBooked
OpportunityClosedNotSelected
OpportunityWithdrawn
OpportunityNextActionChanged
OpportunityProjectDraftCreated
OpportunityLinkedToProjectRole
OpportunityArchived
```

---

# 266. Event consumers

```text
Notification Center
Analytics
Admin Dashboard
VOP
Search
Cache invalidation
Projects provenance/readiness
```

---

# 267. No event consumer may override source state independently.

---

# 268. Analytics event

Derived from committed transition.

---

# 269. OPP-ANL-005 — Analytics Cannot Be Emitted Before Transition Commit

---

# 270. Cache

Current pipeline projections can be cached briefly.

---

# 271. Stage changes invalidate:

- pipeline columns;
- opportunity detail;
- counts;
- overdue/next-action projections;
- analytics current view where applicable.

---

# 272. StageHistory immutable cache can be longer-lived.

---

# 273. OPP-INV-014 — Cache Is Not Stage Authority

---

# 274. Search

Admin-only.

---

# 275. Search stale candidate

Hydrate/revalidate current Opportunity and auth.

---

# 276. OPP-SEC-006 — Search Result Does Not Grant Opportunity Access

---

# 277. Notifications

`OPPORTUNITY_BOOKED` may be HIGH but policy-driven.

---

# 278. Booked without Project Draft

VOP/reminder may surface:

```text
Роль получена — проект/роль ещё не оформлены.
```

---

# 279. It must not create the Draft automatically.

---

# 280. OPP-PRJ-011 — Missing Draft Is Action Item, Not Auto-Fix

---

# 281. Archive

Opportunity can be archived after terminal state or manually where appropriate.

---

# 282. Archive removes from standard active/closed operational views as configured.

---

# 283. Archive preserves:

```text
source
stage history
notes
provenance
Project links
```

---

# 284. OPP-INV-015 — Archive Does Not Erase Pipeline History

---

# 285. Restore

Does not automatically reopen terminal stage.

---

# 286. Example:

Archived BOOKED Opportunity restored for inspection:

stage remains BOOKED.

---

# 287. OPP-INV-016 — Restore ≠ Reopen

---

# 288. Reopen is separate command.

---

# 289. Hard delete

Exceptional.

---

# 290. Dependencies include:

```text
Casting
Feedback
Stage History
Project/Role provenance
Analytics attribution
Audit
Notifications
```

---

# 291. OPP-SEC-007 — Opportunity Purge Is Dependency-Aware

---

# 292. Project created from Booked

Deleting Opportunity must not delete published Project/Role.

Instead preserve minimal provenance/tombstone where required.

---

# 293. OPP-PRJ-012 — Project Is Not Cascade-Deleted With Opportunity

---

# 294. Privacy

Opportunity may contain third-party/casting/private business data.

Admin-only.

---

# 295. Analytics should avoid:

- raw notes;
- private contact;
- casting source text.

---

# 296. OPP-SEC-008 — Pipeline Analytics Is Data-Minimized

---

# 297. Retention

Stage history is business historical data.

Should generally outlive short-lived:

- notifications;
- cache;
- derived analytics intermediates.

---

# 298. Restore

After backup restore:

- do not resend historical stage notifications;
- do not recreate Project Draft;
- do not rerun AI;
- do not reset Booked to Offer;
- do not duplicate StageHistory.

---

# 299. OPP-INV-017 — Restore Does Not Replay Pipeline Side Effects

---

# 300. Worker resume

Reconcile outbox/idempotency before processing.

---

# 301. Migration

Existing casting/contact workflows may contain implicit stages.

---

# 302. Migration rule

Only create StageHistory entries when evidence exists.

---

# 303. Example

Legacy record says:

```text
booked = true
created_at = June 1
```

without intermediate events.

Correct migration:

```text
initial migrated state BOOKED
```

with migration provenance.

Do not fabricate:

```text
QUALIFIED
MATERIALS
AUDITION
CALLBACK
OFFER
```

dates.

---

# 304. OPP-MIG-001 — Migration Never Fabricates Funnel History

---

# 305. Historical current stage

May be imported with:

```text
source = MIGRATION
```

and explicit `occurred_at` only if known.

---

# 306. Existing Project/Role

If legacy Opportunity already led to existing Project:

link provenance.

Do not create duplicate Project.

---

# 307. OPP-MIG-002 — Migration Reuses Existing Credits Where Proven

---

# 308. Existing Casting

Link same-profile Casting if reliable.

---

# 309. Ambiguous matching

Human review.

---

# 310. OPP-MIG-003 — Fuzzy Similarity Cannot Create Provenance Automatically

---

# 311. Migration idempotency

Required.

---

# 312. Historical outcome unknown

Use best-supported current stage or manual review.

Do not default to CLOSED_NOT_SELECTED.

---

# 313. OPP-MIG-004 — Unknown Outcome Remains Unknown/Reviewable, Not “Not Selected”

---

# 314. Accessibility

Pipeline UI:

- stages have text labels;
- Kanban works without drag-only interaction;
- Move/Transition action keyboard accessible;
- priority not color-only;
- due/overdue status textual;
- stage timeline readable by screen reader.

---

# 315. Mobile

Admin must be able to:

- view pipeline;
- open opportunity;
- update stage;
- set next action;
- inspect provenance;
- perform Booked conversion workflow.

---

# 316. OPP-UX-006 — Core Pipeline Is Not Desktop-Only

---

# 317. Anti-patterns

`OPP-AP-001`  
Use Casting status as Opportunity stage.

`OPP-AP-002`  
Use Feedback workflow state as Opportunity stage.

`OPP-AP-003`  
One generic `status` field covers Casting, Opportunity, Project and notifications.

`OPP-AP-004`  
Stage changed by direct SQL from another module.

`OPP-AP-005`  
Stage history generated later from current state.

`OPP-AP-006`  
Current stage updated without StageHistory transaction.

`OPP-AP-007`  
StageHistory editable/deletable by ordinary UI.

`OPP-AP-008`  
Skipped stages fabricated for cleaner funnel.

`OPP-AP-009`  
Unknown intermediate timestamps inferred.

`OPP-AP-010`  
Offer automatically becomes Booked.

`OPP-AP-011`  
AI marks Booked.

`OPP-AP-012`  
Analytics marks Booked.

`OPP-AP-013`  
VOP changes stage automatically.

`OPP-AP-014`  
Dragging Kanban card updates client state only.

`OPP-AP-015`  
Browser sends trusted `fromStage`.

`OPP-AP-016`  
Browser sets `changedBy`.

`OPP-AP-017`  
Reading a Notification advances Opportunity.

`OPP-AP-018`  
Sending materials automatically advances to AUDITION.

`OPP-AP-019`  
Generating self-tape automatically sets SELF_TAPE_REQUESTED.

`OPP-AP-020`  
Questionnaire download advances stage.

`OPP-AP-021`  
Casting creation automatically creates Opportunity.

`OPP-AP-022`  
Every Feedback automatically creates Opportunity.

`OPP-AP-023`  
One Casting creates multiple duplicate Opportunities through race.

`OPP-AP-024`  
Feedback→Casting→Opportunity and direct Feedback→Opportunity both create duplicate leads silently.

`OPP-AP-025`  
AI classification qualifies Opportunity.

`OPP-AP-026`  
AI match score changes stage.

`OPP-AP-027`  
AI probability shown as factual outcome.

`OPP-AP-028`  
Priority auto-set from model sentiment.

`OPP-AP-029`  
Next Action automatically executes itself.

`OPP-AP-030`  
Overdue reminder changes stage.

`OPP-AP-031`  
BOOKED automatically publishes Project.

`OPP-AP-032`  
BOOKED automatically marks Project public.

`OPP-AP-033`  
BOOKED automatically chooses homepage showcase.

`OPP-AP-034`  
Booked conversion invents Role name.

`OPP-AP-035`  
Booked conversion uses unconfirmed AI fact.

`OPP-AP-036`  
Existing Project duplicated because title match not checked/reviewed.

`OPP-AP-037`  
Fuzzy AI project match auto-merges records.

`OPP-AP-038`  
Retry Booked conversion creates duplicate Project.

`OPP-AP-039`  
Project deletion cascades from Opportunity delete.

`OPP-AP-040`  
Opportunity delete rewrites published questionnaire history.

`OPP-AP-041`  
Archive deletes StageHistory.

`OPP-AP-042`  
Restore reopens terminal Opportunity automatically.

`OPP-AP-043`  
Restore recreates Project Draft.

`OPP-AP-044`  
Restore resends historical notifications.

`OPP-AP-045`  
Public Search indexes Opportunity titles/role negotiations.

`OPP-AP-046`  
Pipeline data appears in SEO.

`OPP-AP-047`  
Analytics stores private notes/source text.

`OPP-AP-048`  
Analytics funnel fabricates skipped stages.

`OPP-AP-049`  
Migration fabricates audition/callback dates.

`OPP-AP-050`  
Migration defaults unknown outcome to Not Selected.

`OPP-AP-051`  
Stage correction deletes old history.

`OPP-AP-052`  
Terminal stage changed without reason/audit.

`OPP-AP-053`  
Notification failure rolls back stage change.

`OPP-AP-054`  
Opportunity stage depends on AI provider availability.

`OPP-AP-055`  
Opportunity data returned through Public Profile API.

`OPP-AP-056`  
Stale Admin version overwrites newer stage.

`OPP-AP-057`  
Same idempotency key reused to change OFFER→BOOKED and then BOOKED→WITHDRAWN.

`OPP-AP-058`  
Stage history is used as mutable note log.

`OPP-AP-059`  
Responsible Admin becomes public contact.

`OPP-AP-060`  
Dashboard card count is treated as authoritative current stage data.

---

# 318. Core invariants

`OPP-INV-018`  
Opportunity is an independent professional workflow domain.

`OPP-INV-019`  
Opportunity belongs to exactly one Actor Profile.

`OPP-INV-020`  
Canonical stages are fixed and machine-readable.

`OPP-INV-021`  
Current stage changes only through validated Domain transition.

`OPP-INV-022`  
Every stage transition atomically appends immutable StageHistory.

`OPP-INV-023`  
StageHistory remains append-only.

`OPP-INV-024`  
Current stage and last committed StageHistory state remain consistent.

`OPP-INV-025`  
Pipeline can skip stages when real process skipped them.

`OPP-INV-026`  
Skipped stages are never fabricated.

`OPP-INV-027`  
Stage correction is explicit and audited.

`OPP-INV-028`  
BOOKED, CLOSED_NOT_SELECTED and WITHDRAWN are distinct outcomes.

`OPP-INV-029`  
OFFER never automatically means BOOKED.

`OPP-INV-030`  
BOOKED requires explicit Human confirmation.

`OPP-INV-031`  
AI cannot change Opportunity stage.

`OPP-INV-032`  
Analytics cannot change Opportunity stage.

`OPP-INV-033`  
VOP cannot change Opportunity outcome.

`OPP-INV-034`  
Notification read/delivery does not change Opportunity stage.

`OPP-INV-035`  
Opportunity can originate from Casting, Feedback or manual source.

`OPP-INV-036`  
Casting does not automatically create Opportunity.

`OPP-INV-037`  
Source provenance is explicit and bidirectional where applicable.

`OPP-INV-038`  
Source entities remain distinct after linking.

`OPP-INV-039`  
Duplicate pipeline creation from same source is guarded.

`OPP-INV-040`  
Cross-profile source/entity linking is rejected.

`OPP-INV-041`  
Next Action is planning metadata, not execution authority.

`OPP-INV-042`  
Responsible Admin is internal configuration.

`OPP-INV-043`  
Priority is Human-controlled baseline.

`OPP-INV-044`  
Opportunity notes remain private and distinct from StageHistory.

`OPP-INV-045`  
Materials state is not redundantly recreated when Casting already owns detailed material workflow.

`OPP-INV-046`  
MATERIALS_REQUESTED requires actual request evidence/Human confirmation.

`OPP-INV-047`  
SELF_TAPE_REQUESTED requires actual request.

`OPP-INV-048`  
CALLBACK requires explicit real-world confirmation.

`OPP-INV-049`  
OFFER requires explicit real-world confirmation.

`OPP-INV-050`  
Stage timestamps are not invented.

`OPP-INV-051`  
Historical event time and record time remain distinguishable where needed.

`OPP-INV-052`  
Opportunity remains Admin-private.

`OPP-INV-053`  
Pipeline data never enters Public Search/SEO.

`OPP-INV-054`  
Kanban drag/drop invokes server-side transition.

`OPP-INV-055`  
Pipeline timeline displays actual path only.

`OPP-INV-056`  
Conversion analytics derive from committed stage history.

`OPP-INV-057`  
Analytics does not fabricate missing funnel stages.

`OPP-INV-058`  
AI probability/matching remains advisory and non-authoritative.

`OPP-INV-059`  
BB draft generation does not alter stage.

`OPP-INV-060`  
Stage commit precedes notifications/analytics/search updates.

`OPP-INV-061`  
Notification failure never rolls back Opportunity transition.

`OPP-INV-062`  
Opportunity mutations are optimistic-concurrency protected.

`OPP-INV-063`  
Concurrent stage transitions cannot both commit against same version.

`OPP-INV-064`  
High-impact stage retry is idempotent.

`OPP-INV-065`  
Same idempotency key cannot represent a different outcome.

`OPP-INV-066`  
BOOKED does not create/publish Project automatically.

`OPP-INV-067`  
Project/Role creation after Booked requires explicit Human command.

`OPP-INV-068`  
Booked conversion creates Draft only.

`OPP-INV-069`  
Booked conversion uses confirmed facts only.

`OPP-INV-070`  
Unknown Project/Role details remain unknown.

`OPP-INV-071`  
Existing Project/Role may be linked rather than duplicated.

`OPP-INV-072`  
AI/fuzzy matching cannot auto-merge existing credits.

`OPP-INV-073`  
Opportunity→Project/Role provenance is bidirectional.

`OPP-INV-074`  
Booked conversion is idempotent and race-safe.

`OPP-INV-075`  
Post-booking conversion state remains orthogonal to Opportunity stage.

`OPP-INV-076`  
Project/Role is never cascade-deleted with Opportunity.

`OPP-INV-077`  
Archive preserves StageHistory/provenance.

`OPP-INV-078`  
Restore does not reopen terminal stage automatically.

`OPP-INV-079`  
Reopen is an explicit separate command.

`OPP-INV-080`  
Opportunity purge is dependency-aware.

`OPP-INV-081`  
Analytics payloads are data-minimized.

`OPP-INV-082`  
Restore does not replay notifications/AI/Project conversion.

`OPP-INV-083`  
Migration does not fabricate funnel stages.

`OPP-INV-084`  
Migration does not fabricate unknown outcomes.

`OPP-INV-085`  
Migration reuses existing proven Project/Role relationships rather than duplicating them.

`OPP-INV-086`  
Migration is idempotent.

`OPP-INV-087`  
Pipeline core works without AI.

`OPP-INV-088`  
Pipeline core works without external notification provider.

`OPP-INV-089`  
StageHistory and Audit remain distinct histories.

`OPP-INV-090`  
All stage, provenance, conversion, privacy and concurrency rules are server-enforced.

---

# 319. E2E-OPP-001 — Manual create

Admin creates manual Opportunity.

Expected:

```text
stage = NEW
```

with source provenance.

---

# 320. E2E-OPP-002 — From Casting

Human creates Opportunity from Casting.

Bidirectional link established.

---

# 321. E2E-OPP-003 — Casting does not auto-create

New Casting alone creates no Opportunity.

---

# 322. E2E-OPP-004 — Create from Casting retry

Lost response + same idempotency key.

One Opportunity only.

---

# 323. E2E-OPP-005 — Concurrent source creation

Two Admins attempt Opportunity from same Casting.

One canonical Opportunity.

---

# 324. E2E-OPP-006 — Direct Feedback

Eligible direct collaboration Feedback can create Opportunity through explicit Human action.

---

# 325. E2E-OPP-007 — Duplicate provenance path

Feedback already → Casting → Opportunity.

Second direct Feedback → Opportunity creation is blocked/warned under policy.

---

# 326. E2E-OPP-008 — Cross-profile Casting

Attempt to link Casting from another profile.

Rejected.

---

# 327. E2E-OPP-009 — NEW → QUALIFIED

Human transition succeeds.

Current stage/history both update atomically.

---

# 328. E2E-OPP-010 — Stage history

History contains:

```text
NEW → QUALIFIED
```

with actor/time.

---

# 329. E2E-OPP-011 — Skip stage

QUALIFIED → AUDITION accepted when explicitly confirmed.

No fake Materials/Self-Tape history.

---

# 330. E2E-OPP-012 — Direct Offer

QUALIFIED → OFFER accepted where real workflow supports it.

No fabricated intermediate stages.

---

# 331. E2E-OPP-013 — Invalid transition

Disallowed transition returns typed error.

---

# 332. E2E-OPP-014 — Stale transition

Admin A advances version.

Admin B submits old expectedVersion.

Rejected.

---

# 333. E2E-OPP-015 — Double transition retry

Same idempotency key/network retry appends one StageHistory event only.

---

# 334. E2E-OPP-016 — Key conflict

Same key reused with different `toStage`.

Rejected.

---

# 335. E2E-OPP-017 — Materials semantics

Generating PDF alone does not set MATERIALS_REQUESTED.

---

# 336. E2E-OPP-018 — Actual materials request

Human confirms real request.

Stage may transition to MATERIALS_REQUESTED.

---

# 337. E2E-OPP-019 — Self-tape existence

Private self-tape exists but was not requested.

Stage does not become SELF_TAPE_REQUESTED.

---

# 338. E2E-OPP-020 — Self-tape request

Confirmed request transitions appropriately.

---

# 339. E2E-OPP-021 — Callback

Callback only after Human/confirmed external evidence.

---

# 340. E2E-OPP-022 — Offer

Offer transition succeeds with explicit Human confirmation.

---

# 341. E2E-OPP-023 — Offer is not Booked

After OFFER, stage remains OFFER until separate action.

---

# 342. E2E-OPP-024 — AI cannot Book

AI recommends likely success.

Opportunity remains unchanged.

---

# 343. E2E-OPP-025 — Analytics cannot Book

Analytics pipeline reports high probability.

No stage change.

---

# 344. E2E-OPP-026 — Booked

Human explicitly transitions OFFER → BOOKED.

Booked history/timestamp recorded.

---

# 345. E2E-OPP-027 — Booked no auto Project

After BOOKED, no Project exists until separate command.

---

# 346. E2E-OPP-028 — Create Project/Role Draft

Human invokes conversion.

Draft Project + Role created.

---

# 347. E2E-OPP-029 — Draft not public

Created Project/Role:

```text
DRAFT
```

and absent from Public Profile/Search.

---

# 348. E2E-OPP-030 — Unknown role

Booked Opportunity has unknown character name.

Conversion leaves Role name unset/unknown.

---

# 349. E2E-OPP-031 — Existing Project

Human selects existing same-profile Project.

System links/creates Role Draft instead of duplicate Project.

---

# 350. E2E-OPP-032 — Existing wrong-profile Project

Rejected.

---

# 351. E2E-OPP-033 — Conversion retry

Same idempotency key returns same Project/Role IDs.

---

# 352. E2E-OPP-034 — Conversion race

Two Admins click simultaneously.

One conversion only.

---

# 353. E2E-OPP-035 — Provenance

Project/Role can navigate back to Opportunity/Casting.

Opportunity links to resulting Project/Role.

---

# 354. E2E-OPP-036 — AI speculation exclusion

Casting AI guessed Project type.

Unconfirmed value does not seed Project Draft.

---

# 355. E2E-OPP-037 — Closed not selected

Human transitions to CLOSED_NOT_SELECTED.

Distinct from WITHDRAWN.

---

# 356. E2E-OPP-038 — Withdrawn

Human explicitly withdraws.

Analytics records separate terminal outcome.

---

# 357. E2E-OPP-039 — Unknown close reason

Reason remains absent/unknown, not invented.

---

# 358. E2E-OPP-040 — Reopen

Terminal Opportunity can be reopened only through explicit command/reason according to policy.

---

# 359. E2E-OPP-041 — Archive Booked

Archive hides Opportunity from active operational view.

Booked stage/history retained.

---

# 360. E2E-OPP-042 — Restore archive

Restore does not reset BOOKED or re-create Project.

---

# 361. E2E-OPP-043 — Next Action

Admin sets Follow Up for tomorrow.

No communication is sent automatically.

---

# 362. E2E-OPP-044 — Overdue

Scheduler/VOP flags overdue action.

Stage remains unchanged.

---

# 363. E2E-OPP-045 — Responsible

Assign Admin A.

No public contact data changes.

---

# 364. E2E-OPP-046 — Priority suggestion

VOP suggests HIGH.

Stored priority unchanged until Human action.

---

# 365. E2E-OPP-047 — Kanban drag

Drag QUALIFIED card to AUDITION.

Server transition command executes and history is appended.

---

# 366. E2E-OPP-048 — Invalid Kanban drop

Server rejects.

UI restores original column.

---

# 367. E2E-OPP-049 — Timeline

Opportunity skipping Self-Tape displays actual path only.

---

# 368. E2E-OPP-050 — Public lookup

Anonymous user guesses Opportunity ID.

No data returned.

---

# 369. E2E-OPP-051 — Public Search

Opportunity title absent from public index.

---

# 370. E2E-OPP-052 — Admin Search

Authorized Admin filters by OFFER stage.

Correct private results.

---

# 371. E2E-OPP-053 — Notification failure

Stage transition commits.

Notification provider fails.

Stage remains committed.

---

# 372. E2E-OPP-054 — Analytics transition

Committed StageHistory produces analytics event after commit.

---

# 373. E2E-OPP-055 — Analytics skipped stages

QUALIFIED → OFFER.

Funnel analytics does not pretend AUDITION/CALLBACK occurred.

---

# 374. E2E-OPP-056 — Conversion rate

Booked/Qualified calculated from real historical cohort.

No source mutation.

---

# 375. E2E-OPP-057 — Notes

Add private note.

Stage/history unchanged.

---

# 376. E2E-OPP-058 — Restore backup

No duplicate StageHistory or Project conversion generated.

---

# 377. E2E-OPP-059 — Migration late-stage record

Legacy BOOKED record imports without fabricated intermediate stages.

---

# 378. E2E-OPP-060 — Migration existing Project

Existing proven Project linked rather than duplicated.

---

# 379. E2E-OPP-061 — Migration ambiguous Project

No fuzzy automatic provenance relation.

Flag Human review.

---

# 380. E2E-OPP-062 — Unknown migrated outcome

Not automatically classified as Not Selected.

---

# 381. E2E-OPP-063 — Purge dependency

Attempt to purge Opportunity linked to Project history detects dependency.

No cascade Project deletion.

---

# 382. E2E-OPP-064 — Accessibility

Keyboard user can change stage without drag-and-drop.

---

# 383. E2E-OPP-065 — No AI

Complete Opportunity creation → stages → Booked → Project Draft works with AI disabled.

---

# 384. Domain diagram

```text
Feedback / Casting / Manual
          │
          ▼
      Opportunity
          │
          ├── Current Stage
          ├── Next Action
          ├── Responsible
          ├── Priority
          ├── Notes
          └── Provenance
          │
          ▼
   Stage History
   append-only
```

---

# 385. Stage diagram

```text
NEW
 │
 ▼
QUALIFIED
 │
 ▼
MATERIALS_REQUESTED
 │
 ▼
SELF_TAPE_REQUESTED
 │
 ▼
AUDITION
 │
 ▼
CALLBACK
 │
 ▼
OFFER
 │
 ▼
BOOKED

Alternative terminal:
CLOSED_NOT_SELECTED
WITHDRAWN
```

Real workflow may explicitly skip stages.

---

# 386. Stage history transaction diagram

```text
Transition Command
       ↓
expectedVersion
       ↓
validate current/from/to
       ↓
BEGIN
  update current_stage
  append StageHistory
  Audit
  Outbox
COMMIT
       ↓
Notifications / Analytics / Search
```

---

# 387. Booked conversion diagram

```text
Opportunity BOOKED
       ↓
Human review
       ↓
┌───────────────────────┐
│ Create new Project?   │
│ or Link existing?     │
└──────────┬────────────┘
           ↓
   Project DRAFT
           +
      Role DRAFT
           ↓
Bidirectional provenance
           ↓
Human later reviews/publishes
```

---

# 388. Authority diagram

```text
Casting AI ───────────────┐
Analytics ────────────────┤
VOP ──────────────────────┤
Notifications ────────────┤
                         ▼
               Recommendations / Signals

                         ≠

                 Stage Authority
                         │
                         ▼
                  Human Command
                         │
                         ▼
                Domain Transition
```

---

# 389. Funnel analytics diagram

```text
StageHistory
    ↓
Analytics Projection
    ↓
Conversions
Time-to-stage
Source attribution
Booked rate
Closed outcomes

Analytics never writes stage.
```

---

# 390. Quality gate

Перед implementation должны быть подтверждены:

- [ ] Opportunity entity;
- [ ] exact canonical stages;
- [ ] current-stage model;
- [ ] append-only StageHistory;
- [ ] atomic stage+history transition;
- [ ] stage-skip semantics;
- [ ] explicit correction semantics;
- [ ] terminal outcomes;
- [ ] reopen policy;
- [ ] Casting provenance;
- [ ] Feedback provenance;
- [ ] manual source;
- [ ] duplicate source guards;
- [ ] same-profile invariants;
- [ ] responsible Admin;
- [ ] priority;
- [ ] next action;
- [ ] notes;
- [ ] Kanban server transition;
- [ ] timeline;
- [ ] Admin-only search;
- [ ] no Public/SEO exposure;
- [ ] optimistic concurrency;
- [ ] transition idempotency;
- [ ] BOOKED explicit Human confirmation;
- [ ] Booked → Project/Role Draft explicit command;
- [ ] create vs link-existing flow;
- [ ] Draft-only result;
- [ ] Booked conversion idempotency/race safety;
- [ ] Project/Role provenance;
- [ ] no AI/Analytics/VOP stage authority;
- [ ] notifications post-commit;
- [ ] analytics from StageHistory;
- [ ] skipped-stage analytics correctness;
- [ ] archive/restore;
- [ ] dependency-aware purge;
- [ ] migration without fabricated history;
- [ ] accessibility/mobile;
- [ ] deterministic E2E coverage.

---

# 391. Acceptance criteria

`AC-OPP-001`  
Opportunity является самостоятельным domain object и не совпадает с Casting/Feedback/Project.

`AC-OPP-002`  
Используются точные стадии `NEW`, `QUALIFIED`, `MATERIALS_REQUESTED`, `SELF_TAPE_REQUESTED`, `AUDITION`, `CALLBACK`, `OFFER`, `BOOKED`, `CLOSED_NOT_SELECTED`, `WITHDRAWN`.

`AC-OPP-003`  
Current stage изменяется только через server-side validated transition.

`AC-OPP-004`  
Каждый успешный переход атомарно создаёт append-only StageHistory.

`AC-OPP-005`  
Current stage и StageHistory остаются согласованными.

`AC-OPP-006`  
Реальный workflow может явно пропускать стадии.

`AC-OPP-007`  
Пропущенные стадии никогда не создаются искусственно.

`AC-OPP-008`  
Stage correction выполняется явно и не уничтожает historical provenance.

`AC-OPP-009`  
`BOOKED`, `CLOSED_NOT_SELECTED` и `WITHDRAWN` остаются разными outcomes.

`AC-OPP-010`  
`OFFER` никогда автоматически не становится `BOOKED`.

`AC-OPP-011`  
`BOOKED` требует явного Human confirmation.

`AC-OPP-012`  
AI, Analytics, VOP и Notifications не имеют stage authority.

`AC-OPP-013`  
Opportunity может быть создана из Casting, Feedback или вручную.

`AC-OPP-014`  
Casting не создаёт Opportunity автоматически.

`AC-OPP-015`  
Casting/Feedback provenance сохраняется bidirectionally.

`AC-OPP-016`  
Создание Opportunity из source является idempotent/race-safe where applicable.

`AC-OPP-017`  
Cross-profile provenance rejected.

`AC-OPP-018`  
Next Action не исполняется автоматически.

`AC-OPP-019`  
Responsible Admin остаётся private operational configuration.

`AC-OPP-020`  
Priority Human-controlled baseline.

`AC-OPP-021`  
Opportunity notes не используются вместо StageHistory.

`AC-OPP-022`  
Generating/submitting materials does not implicitly fabricate unrelated stages.

`AC-OPP-023`  
`SELF_TAPE_REQUESTED` фиксируется только при фактическом request.

`AC-OPP-024`  
`CALLBACK` и `OFFER` требуют real-world/Human confirmation.

`AC-OPP-025`  
Stage timestamps/history do not invent event dates.

`AC-OPP-026`  
Opportunity остаётся Admin-private и отсутствует в Public Search/SEO.

`AC-OPP-027`  
Kanban drag/drop всегда вызывает server transition command.

`AC-OPP-028`  
Timeline показывает только actual stage path.

`AC-OPP-029`  
Opportunity mutations используют optimistic concurrency.

`AC-OPP-030`  
Concurrent transitions cannot both commit from same version.

`AC-OPP-031`  
High-impact transition retries are idempotent.

`AC-OPP-032`  
Idempotency key cannot represent different target outcomes.

`AC-OPP-033`  
`BOOKED` never publishes or creates Project automatically.

`AC-OPP-034`  
Booked → Project/Role requires explicit Human command.

`AC-OPP-035`  
Booked conversion creates Project/Role in DRAFT state only.

`AC-OPP-036`  
Unknown Project/Role facts remain unknown.

`AC-OPP-037`  
Booked Draft seed contains only confirmed Source facts.

`AC-OPP-038`  
Existing Project/Role can be linked instead of duplicated.

`AC-OPP-039`  
Fuzzy/AI entity matching cannot auto-merge credits.

`AC-OPP-040`  
Opportunity↔Project/Role provenance is bidirectional.

`AC-OPP-041`  
Booked conversion is idempotent and race-safe.

`AC-OPP-042`  
Project/Role is never cascade-deleted with Opportunity.

`AC-OPP-043`  
Notifications and Analytics are emitted only after committed stage transition.

`AC-OPP-044`  
Notification failure never rolls back stage.

`AC-OPP-045`  
Conversion analytics derive from committed StageHistory.

`AC-OPP-046`  
Analytics preserves actual skipped-stage path.

`AC-OPP-047`  
Analytics cannot qualify, close or Book an Opportunity.

`AC-OPP-048`  
VOP may surface stalled/overdue/booked-without-project cases but cannot resolve them autonomously.

`AC-OPP-049`  
Archive preserves StageHistory/provenance.

`AC-OPP-050`  
Restore does not reopen terminal states or replay business side effects.

`AC-OPP-051`  
Opportunity purge is dependency-aware.

`AC-OPP-052`  
Pipeline analytics do not contain unnecessary private notes/source payloads.

`AC-OPP-053`  
Migration does not fabricate missing stages/timestamps/outcomes.

`AC-OPP-054`  
Migration reuses existing proven Project/Role relationships instead of creating duplicates.

`AC-OPP-055`  
Migration is idempotent.

`AC-OPP-056`  
Pipeline remains fully operational without AI.

`AC-OPP-057`  
Pipeline remains operational during external notification provider outage.

`AC-OPP-058`  
All stage, history, concurrency, provenance, Booked conversion and analytics invariants have deterministic E2E tests.

---

# 392. Финальная доктрина

> **Opportunity Pipeline является единственным authoritative workflow для профессионального progression от новой возможности до результата. Текущая стадия хранится отдельно от immutable append-only Stage History и меняется только через server-side Domain Transition с optimistic concurrency, idempotency, Audit и Outbox. Pipeline отражает реальность и позволяет явно пропускать стадии — система никогда не создаёт фиктивные Materials, Self-Tape, Audition или Callback ради красивой воронки. `OFFER` и `BOOKED` принципиально различаются, а `BOOKED` подтверждается только человеком. Даже успешный `BOOKED` не превращается автоматически в публичный профессиональный credit: отдельный Human-controlled idempotent workflow создаёт или связывает только Project/Role Draft с двусторонним provenance. Casting, Feedback, AI, VOP, Notifications и Analytics остаются связанными, но не получают права изменять stage. Conversion analytics строится исключительно из фактической Stage History и не имеет обратного write-path в pipeline.**