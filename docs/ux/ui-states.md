# UI STATES

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная модель состояний пользовательского интерфейса

**Целевой файл:** `docs/ux/ui-states.md`  
**Документ:** DOC-047  
**Статус:** ✅ Completed  
**Тип:** UX / UI State Model

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/virtual-operator.md`
- `docs/customer-journey/automation-matrix.md`
- `docs/ux/ux-principles.md`
- `docs/ux/public-navigation.md`
- `docs/ux/admin-navigation.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/public-questionnaire-builder.md`
- `docs/ux/forms-and-validation.md`

---

# 1. Назначение документа

Настоящий документ определяет каноническую модель UI States целевого продукта.

Он устанавливает:

1. какие состояния интерфейса существуют;
2. чем они отличаются друг от друга;
3. как они отображаются;
4. какие действия разрешены в каждом состоянии;
5. как состояния переходят друг в друга;
6. как различаются UI State, Domain State и Business State;
7. как отображаются asynchronous operations;
8. как отображаются ошибки;
9. как работает graceful degradation;
10. как показывается staleness;
11. как отображаются AI-generated recommendations;
12. как UI обрабатывает concurrency;
13. как Public и Admin состояния различаются;
14. какие states являются blocking;
15. какие states должны быть доступны assistive technologies.

---

# 2. Главный принцип

Интерфейс всегда должен отвечать пользователю на четыре вопроса:

```text
Что сейчас происходит?
Что уже произошло?
Можно ли продолжить?
Что делать дальше?
```

Состояние не должно определяться только цветом, spinner или disabled button.

---

# 3. UI State ≠ Domain State

Необходимо различать:

```text
UI State
Domain State
Business State
Processing State
```

Пример:

```text
Questionnaire

Domain State:
Draft

UI Form State:
Dirty

Processing State:
Saving

Readiness:
Blocked
```

Все четыре состояния могут существовать одновременно.

---

# 4. UI State IDs

Используется prefix:

```text
UIST-*
```

Например:

```text
UIST-LOAD-001
UIST-ERR-001
UIST-AI-001
```

---

# 5. Основные группы состояний

Канонические группы:

```text
UIST-LOAD — Loading
UIST-EMPTY — Empty
UIST-READY — Ready
UIST-EDIT — Editing / Dirty
UIST-VALID — Validation
UIST-PROC — Processing
UIST-SUCCESS — Success
UIST-WARN — Warning
UIST-ERROR — Error
UIST-BLOCK — Blocking
UIST-STALE — Stale
UIST-CONFLICT — Concurrency
UIST-AUTH — Authentication / Authorization
UIST-EXPIRE — Expired
UIST-DEGRADED — Degraded Service
UIST-LIFECYCLE — Draft / Published / Archived / Historical
UIST-AI — AI-specific
UIST-OFFLINE — Connectivity
```

---

# 6. Core UI State Model

```text
INITIAL
   ↓
LOADING
   ↓
 ┌───────────────┬──────────────────┐
 │               │                  │
READY           EMPTY             ERROR
 │
 ├── VIEW
 │
 ├── EDIT
 │     ↓
 │   DIRTY
 │     ↓
 │  VALIDATING
 │   /      \
 │ INVALID   VALID
 │             ↓
 │         PROCESSING
 │          /      \
 │      SUCCESS    ERROR
 │
 └── ASYNC PROCESSING
       ↓
 READY / WARNING / ERROR
```

---

# 7. State Layering

Несколько states MAY отображаться одновременно.

Пример:

```text
Published Project
+
One warning
+
AI suggestion available
```

Это не конфликт.

UI должен позволять пользователю понять каждый слой отдельно.

---

# 8. State Priority

Если состояния конкурируют за визуальное внимание, используется порядок:

```text
1. Security / Privacy Block
2. Data Loss / Conflict
3. Business Blocking Error
4. Processing Failure
5. Warning
6. Informational State
7. Success
8. Optimization Suggestion
```

---

# 9. UIST-LOAD-001 — Initial Loading

Используется при первом получении критичных данных страницы.

UI SHOULD:

- сохранять layout context;
- показывать skeleton/placeholder при необходимости;
- оставлять global navigation доступной;
- не показывать fake values.

---

# 10. Full-Page Loader

Допустим только когда страница действительно не имеет достаточного контекста для отображения.

Не использовать full-page spinner для каждого secondary request.

---

# 11. Skeleton State

Skeleton SHOULD соответствовать примерной структуре content.

Не должен выглядеть как настоящий текст или реальные значения.

---

# 12. Skeleton Scope

Лучше:

```text
Header visible
Profile identity visible
Gallery skeleton
```

чем:

```text
Entire screen blank
→ spinner
```

---

# 13. Loading vs Processing

Важно различать:

```text
Loading
=
получение существующих данных

Processing
=
создание/изменение результата
```

Например:

```text
Loading Project
```

и:

```text
Generating PDF
```

— разные states.

---

# 14. UIST-LOAD-002 — Partial Loading

Secondary content MAY загружаться независимо.

Пример:

```text
Profile ready
Video metadata loading
Projects loading
```

Основной page interaction остаётся доступен.

---

# 15. UIST-LOAD-003 — Lazy Content

Для secondary gallery/archive:

```text
Load more
```

или lazy-loading.

Не блокирует P0 content.

---

# 16. UIST-LOAD-004 — Loading Failure

Если secondary content fails:

```text
primary page remains ready
+
local error
+
retry
```

---

# 17. UIST-EMPTY-001 — Empty State

Empty означает:

```text
Request succeeded
but there is no applicable content
```

Это не Error.

---

# 18. Public Empty State

Example:

```text
Опубликованные проекты пока отсутствуют.
```

Не показывать technical wording:

```text
0 records returned.
```

---

# 19. Admin Empty State

Должен объяснять:

```text
что здесь хранится
+
что можно сделать дальше
```

Пример:

```text
Пока нет подготовленных анкет.

[Создать анкету]
```

---

# 20. Empty State ≠ Missing Required Data

Если обязательный элемент отсутствует в Draft:

это может быть:

```text
Readiness Blocker
```

а не ordinary Empty State.

---

# 21. Empty State ≠ Unauthorized

Если объект существует, но недоступен пользователю:

не показывать Empty.

---

# 22. Empty State ≠ Loading

Do not temporarily show:

```text
Нет проектов
```

before request finishes.

---

# 23. UIST-READY-001 — Ready

Ready означает:

```text
данные загружены
+
текущий интерфейс доступен для intended task
```

---

# 24. Ready ≠ Ready to Publish

UI state `Ready` отличается от Domain Readiness.

Например:

```text
Form UI = Ready
Questionnaire Readiness = Blocked
```

---

# 25. UIST-READY-002 — Read-Only Ready

Historical/Published content MAY быть отображён в read-only state.

---

# 26. UIST-EDIT-001 — Editing

Admin начал редактирование.

UI SHOULD обозначать editing context там, где это помогает избежать ошибок.

---

# 27. UIST-EDIT-002 — Pristine

Form loaded but unchanged.

---

# 28. UIST-EDIT-003 — Dirty

Есть изменения, не сохранённые authoritative backend.

---

# 29. Dirty Indicator

Для длинных Admin flows SHOULD быть видно:

```text
Несохранённые изменения
```

или equivalent.

---

# 30. Dirty State and Navigation

Если autosave не гарантирован:

leaving current screen MAY trigger navigation guard.

---

# 31. UIST-EDIT-004 — Saving

```text
Dirty
→ Saving
```

UI MAY disable duplicate save invocation.

---

# 32. UIST-EDIT-005 — Saved

После успешного persistence:

```text
Saved
```

Не означает `Published`.

---

# 33. Saved Indicator

Recommended:

```text
Сохранено
```

или subtle timestamp/status.

---

# 34. UIST-EDIT-006 — Save Failed

UI MUST show clearly:

```text
Не удалось сохранить изменения.
```

Current edits remain visible.

---

# 35. UIST-VALID-001 — Validating

Используется, когда система проверяет данные.

Examples:

- form validation;
- URL validation;
- readiness;
- QR decode verification.

---

# 36. Validation Scope

Validation state SHOULD attach to relevant:

- field;
- form;
- entity;

instead of entire application.

---

# 37. UIST-VALID-002 — Valid

Field/entity passes currently applicable checks.

---

# 38. Valid State Visibility

Success checkmark SHOULD be used only where useful.

Do not show green indicators on every ordinary valid field.

---

# 39. UIST-VALID-003 — Invalid

Field/entity violates rule.

Must include:

- problem;
- resolution guidance.

---

# 40. UIST-VALID-004 — Checking

For asynchronous validation:

```text
Checking link…
```

---

# 41. UIST-VALID-005 — Could Not Verify

Important distinction:

```text
Could not verify
```

is not:

```text
Invalid
```

Example temporary URL network timeout.

---

# 42. UIST-PROC-001 — Processing

Used when system performs asynchronous work.

Examples:

- media derivatives;
- PDF;
- AI analysis;
- Theme proposal;
- bulk migration;
- social publish;
- notification retry.

---

# 43. Processing State Must Name Task

Better:

```text
Формируем PDF…
```

than:

```text
Processing…
```

---

# 44. UIST-PROC-002 — Queued

If job waits for worker:

```text
В очереди
```

may be shown when useful.

---

# 45. UIST-PROC-003 — In Progress

Task actively running.

---

# 46. UIST-PROC-004 — Background Processing

User MAY leave workspace if task does not require presence.

UI should not falsely imply work is cancelled by navigation unless it is.

---

# 47. UIST-PROC-005 — Long-Running Operation

Should provide:

- current state;
- cancel only if safe/implemented;
- ability to continue other work.

---

# 48. Fake Progress Prohibited

Do not show:

```text
73%
```

if system does not know progress.

Use indeterminate state.

---

# 49. UIST-PROC-006 — Partial Completion

For bulk actions:

```text
10 completed
2 failed
```

Partial success MUST be represented explicitly.

---

# 50. UIST-SUCCESS-001 — Local Success

Examples:

```text
Сохранено
Ссылка скопирована
Добавлено в анкету
```

A toast MAY be appropriate.

---

# 51. UIST-SUCCESS-002 — Transaction Success

High-value business success requires persistent confirmation.

Examples:

```text
Обращение принято.
№123
```

---

# 52. Transaction Success Must Be Authoritative

Never show success based solely on:

- button click;
- optimistic assumption;
- analytics event.

---

# 53. UIST-SUCCESS-003 — Async Success

Example:

```text
PDF готов.
[Скачать]
```

---

# 54. UIST-SUCCESS-004 — Success With Warning

Possible:

```text
Изменения сохранены.

Не удалось проверить внешнюю ссылку.
```

Success and warning can coexist.

---

# 55. UIST-WARN-001 — Warning

Warning means:

```text
action may remain possible
but there is meaningful concern
```

---

# 56. Warning Example

```text
Видеоссылку сейчас не удалось проверить.
```

---

# 57. UIST-WARN-002 — Content Freshness Warning

Example:

```text
Основное фото давно не пересматривалось.
Рекомендуется проверить актуальность.
```

It is not automatic invalidation.

---

# 58. UIST-WARN-003 — Missing Translation

Optional localization gap may be Warning.

Critical production locale gap may become Blocker depending context.

---

# 59. UIST-WARN-004 — Stale Derived Output

If current last-valid output exists:

```text
Предпросмотр устарел.
Обновляем…
```

or action to rebuild.

---

# 60. Warning Must Not Block Without Rule

A yellow state should not randomly disable action.

---

# 61. UIST-ERROR-001 — Field Error

Scoped to individual field.

---

# 62. UIST-ERROR-002 — Form Error

Multiple validation errors or server business failure.

---

# 63. UIST-ERROR-003 — Component Error

Example:

```text
Video unavailable
```

while rest of page works.

---

# 64. UIST-ERROR-004 — Page Error

Page cannot load meaningful requested content.

Still preserve:

- site/admin shell;
- navigation;
- recovery path.

---

# 65. UIST-ERROR-005 — Service Error

Example:

```text
PDF service unavailable
```

Should indicate impact, not infrastructure implementation.

---

# 66. UIST-ERROR-006 — Unknown Error

Use only when more specific mapping unavailable.

Provide:

- safe message;
- retry;
- reference/error ID where useful.

---

# 67. Raw Error Prohibition

Public MUST NOT show:

- stack traces;
- SQL errors;
- internal paths;
- secret values.

Admin MAY access technical detail via diagnostics.

---

# 68. UIST-BLOCK-001 — Blocking State

Blocking means requested high-level action cannot safely continue.

Examples:

- Publish blocked;
- PDF generation impossible;
- access prohibited;
- required data missing.

---

# 69. Blocking State Must Explain Why

Example:

```text
Анкету нельзя опубликовать.

Исправьте:
• выберите фото в полный рост;
• укажите разрешённый контакт.
```

---

# 70. Blocker Provides Resolution

Each blocker SHOULD link directly to relevant:

- field;
- entity;
- settings page.

---

# 71. UIST-BLOCK-002 — Security Block

Examples:

```text
Private URL cannot be used in public QR.
```

Cannot be overridden by ordinary warning acknowledgement.

---

# 72. UIST-BLOCK-003 — Readiness Block

Entity is saved but not ready for target action.

---

# 73. Draft Save While Blocked

A Draft MAY still save even if Publish is blocked.

---

# 74. UIST-STALE-001 — Stale

Stale means:

```text
representation is based on an older source state
```

It does NOT automatically mean invalid.

---

# 75. Examples of Stale

- Questionnaire preview;
- AI Draft;
- localization;
- PDF candidate;
- search index;
- QR cache.

---

# 76. Stale ≠ Historical

Historical Revision is intentionally fixed.

Stale is a current representation that needs refresh.

---

# 77. UIST-STALE-002 — Stale But Usable

Example:

```text
Последняя опубликованная версия остаётся доступной.
Новая версия требует обновления.
```

---

# 78. UIST-STALE-003 — Stale and Blocking

If stale representation can no longer safely be generated due to revoked permission:

output action blocks.

---

# 79. UIST-STALE-004 — AI Draft Stale

Example:

```text
Этот текст был создан до изменения языкового уровня.
Проверьте его перед применением.
```

---

# 80. UIST-STALE-005 — Stale Suggestion

If AI/VOP suggestion based on older entity version:

```text
Предложение устарело.
[Обновить анализ]
```

---

# 81. UIST-CONFLICT-001 — Concurrency Conflict

Occurs when:

```text
user edits v5
source becomes v6
user attempts save/apply
```

---

# 82. Conflict Must Not Be Shown as Validation Error

Use distinct message:

```text
Данные изменились после открытия формы.
```

---

# 83. Conflict Resolution Actions

Where possible:

```text
Просмотреть новую версию
Скопировать мои изменения
Сравнить
Повторить вручную
```

---

# 84. UIST-CONFLICT-002 — AI Apply Conflict

Before applying suggestion:

server checks current source revision/version.

---

# 85. UIST-CONFLICT-003 — Builder Session Conflict

Admin permission/configuration changed while Public Builder session active.

Server revalidates current eligibility.

---

# 86. UIST-AUTH-001 — Unauthenticated

For Admin:

```text
authentication required
```

Redirect to login according to Security design.

---

# 87. UIST-AUTH-002 — Session Expired

Admin should see:

```text
Сессия завершилась.
Войдите снова.
```

with safe recovery of unsaved work where possible.

---

# 88. UIST-AUTH-003 — Unauthorized

User authenticated but action/resource unavailable.

---

# 89. Unauthorized vs Not Found

Security policy determines whether resource existence can be disclosed.

---

# 90. UIST-AUTH-004 — Reauthentication Required

High-risk action MAY later require re-authentication if Security spec defines it.

---

# 91. UIST-EXPIRE-001 — Public Builder Session Expired

```text
Эта версия анкеты больше недоступна.
[Создать новую]
```

---

# 92. Expired State Is Not Error

Expiration is expected lifecycle outcome.

---

# 93. UIST-EXPIRE-002 — Temporary Theme Expired

Admin sees temporary theme no longer active and current permanent theme restored.

---

# 94. UIST-EXPIRE-003 — OAuth Expired

Example:

```text
TikTok — требуется повторное подключение
```

---

# 95. UIST-DEGRADED-001 — Degraded Capability

System or integration impaired, but core product still works.

Examples:

```text
AI unavailable
WhatsApp unavailable
Analytics unavailable
```

---

# 96. Degraded ≠ Down

Use degraded state when:

- affected subset known;
- fallback available.

---

# 97. UIST-DEGRADED-002 — AI Degraded

Manual workflow remains visible as primary fallback.

---

# 98. UIST-DEGRADED-003 — Notification Degraded

Feedback/business record persistence unaffected.

---

# 99. UIST-DEGRADED-004 — Analytics Degraded

Do not show alarming public error.

Admin MAY see data delay.

---

# 100. UIST-DEGRADED-005 — External Media Degraded

Affected media local error; rest of page remains available.

---

# 101. Lifecycle State Model

UI MUST distinguish:

```text
Draft
Ready
Published
Archived
Historical Revision
Deleted / Removed where applicable
```

---

# 102. UIST-LIFECYCLE-001 — Draft

Meaning:

```text
editable working state
not automatically public
```

---

# 103. Draft Badge

Admin SHOULD have visible status:

```text
Черновик
```

---

# 104. UIST-LIFECYCLE-002 — Ready

Business/readiness checks satisfied.

Still not necessarily Published.

---

# 105. UIST-LIFECYCLE-003 — Published

Current active published state.

---

# 106. Published Does Not Always Mean Public

Visibility may still restrict exposure according to Domain Rules.

UI MUST avoid confusing:

```text
Published
```

with:

```text
Public to everyone
```

where these differ.

---

# 107. UIST-LIFECYCLE-004 — Archived

Historical entity removed from active/default flows.

---

# 108. Archived UI

SHOULD:

- be visibly distinct;
- avoid normal Publish/Edit action unless reactivation supported;
- retain history.

---

# 109. UIST-LIFECYCLE-005 — Historical Revision

Immutable snapshot.

---

# 110. Historical Revision Header

Example:

```text
Revision 12
Published 8 Aug 2026
Read-only
```

---

# 111. Historical Revision Is Not “Disabled Form”

Prefer clean read-only representation where possible.

---

# 112. UIST-LIFECYCLE-006 — Superseded

Useful for:

- AI suggestion;
- operator observation;
- outdated Draft configuration;

when newer state made current item obsolete.

---

# 113. Publication State Matrix

| State | Editable | Public candidate | Publish action |
|---|---:|---:|---:|
| Draft | Yes | No | Possible after readiness |
| Ready | Yes | No | Yes |
| Published | Depends | Current | May create new draft/revision |
| Archived | Limited | No | Reactivate flow if supported |
| Historical Revision | No | Historical only | No |

---

# 114. AI State Model

AI output MUST have explicit state.

Suggested canonical states:

```text
Idle
Generating
Generated
Edited
Applied
Discarded
Failed
Stale
```

---

# 115. UIST-AI-001 — AI Available

Contextual action:

```text
Создать черновик
Анализировать
Предложить
```

---

# 116. UIST-AI-002 — Generating

Task-specific message:

```text
Анализируем требования кастинга…
```

not generic:

```text
AI thinking…
```

---

# 117. UIST-AI-003 — Generated

Output is visibly labelled:

```text
AI Draft
```

or:

```text
AI Suggestion
```

---

# 118. Generated AI Output Is Not Applied

System/source remains unchanged until explicit action.

---

# 119. UIST-AI-004 — Edited

Human modified AI Draft.

UI should preserve that it originated from AI where provenance matters.

---

# 120. UIST-AI-005 — Applied

Applied to target Draft/editor.

Still not automatically Published.

---

# 121. UIST-AI-006 — Discarded

No source change.

---

# 122. UIST-AI-007 — Failed

Manual workflow remains available.

---

# 123. UIST-AI-008 — Stale

AI output based on outdated source.

---

# 124. UIST-AI-009 — AI Not Available

Example:

```text
BB Assistant сейчас недоступен.
Текст можно редактировать вручную.
```

---

# 125. Casting AI State Layers

Must distinguish:

```text
Source
AI Extraction
Human Confirmed Requirement
```

---

# 126. Theme AI State Layers

```text
Current Published Theme
Theme Draft
AI Proposal
Preview
```

Do not visually merge.

---

# 127. Virtual Operator State Model

Operator exception MAY use:

```text
Open
Acknowledged
Deferred
Resolved
Dismissed
Superseded
```

Exact Domain Model later.

---

# 128. UIST-VOP-001 — Open Exception

Requires attention or active issue.

---

# 129. UIST-VOP-002 — Suggested Action

Recommendation shown separately from observation.

---

# 130. UIST-VOP-003 — Resolved

Problem no longer active.

---

# 131. UIST-VOP-004 — Auto-Resolved

System fixed safe technical issue.

May appear in recent history but not Action Required.

---

# 132. Public UI State Model

Public interface SHOULD use fewer internal state labels.

Public user generally sees:

```text
Loading
Ready
Empty
Unavailable
Processing
Success
Validation Error
Expired
```

Not internal:

```text
Projection invalidation pending
job retry count = 3
```

---

# 133. Admin UI State Model

Admin MAY see richer information:

```text
Draft
Stale
Blocked
Processing
Conflict
Degraded
Archived
AI Draft
Needs Review
```

---

# 134. Public State — Video

Possible:

```text
Ready
Loading
Playing
Unavailable
External
```

---

# 135. Public Video Unavailable

Must retain:

- Questionnaire;
- Portfolio;
- Contact.

---

# 136. Public State — Questionnaire

Possible:

```text
Available
Generating PDF
PDF Ready
Generation Failed
Unavailable
```

---

# 137. Public State — Builder

Possible:

```text
Selecting
Validating
Preview Ready
Generating
Generated
Expired
Invalid Selection
```

---

# 138. Builder “Invalid Selection”

Use when one or more previously selected items no longer eligible.

---

# 139. Builder Required Item Missing

Blocking.

Message includes direct correction path.

---

# 140. Public Inquiry States

```text
Idle
Editing
Submitting
Submitted
Validation Error
Submission Failed
```

---

# 141. Public Inquiry Submitted

Must only occur after persistence.

---

# 142. Public Inquiry Notification Failure

Should normally NOT change visitor-facing `Submitted` state.

---

# 143. Admin Media States

Conceptually:

```text
Uploaded
Processing
Ready
Processing Failed
Unclassified
Archived
```

---

# 144. Media Original vs Derivative State

Original MAY be:

```text
Ready
```

while derivative:

```text
Failed
```

UI must not incorrectly show entire Media Asset as lost.

---

# 145. Admin Portfolio States

Possible:

```text
Draft
Published
Archived
Needs Classification
Primary
```

`Primary` is a role/marker, not lifecycle state.

---

# 146. Emotional Grid States

Suggested conceptual progression:

```text
Draft
Incomplete
Needs Cell Review
Ready to Finalize
Finalized
Published
Archived
```

Exact Domain state machine later.

---

# 147. Emotional Grid Blocking Conditions

Examples:

- unsupported grid size;
- incorrect photo count;
- unconfirmed cell;
- invalid source media.

---

# 148. Questionnaire States

Need distinguish:

```text
Questionnaire lifecycle
Revision lifecycle
Readiness
PDF processing
```

Example:

```text
Questionnaire = Published
Current Draft = Dirty
Latest Published Revision = Immutable
Preview = Stale
```

UI must support this without collapsing into one ambiguous status.

---

# 149. Casting States

Casting business state is separate from:

```text
AI Analysis state
Questionnaire state
Opportunity stage
```

---

# 150. Opportunity State

Pipeline stages are Business States, not generic UI States.

UI maps them consistently but does not redefine them.

---

# 151. Notification States

Conceptually:

```text
Pending
Scheduled
Sent
Failed
Retrying
Cancelled
```

Exact notification domain later.

---

# 152. Notification “Sent” ≠ Inquiry Exists

Inquiry persistence remains separate.

---

# 153. Social Publish States

Possible:

```text
Draft
Publishing
Published
Failed
```

External account may independently be:

```text
Connected
Expired
Disconnected
```

---

# 154. Theme States

Conceptually:

```text
Draft
Preview
Ready
Published
Temporary Active
Archived
```

---

# 155. Service Health States

Admin system status MAY use:

```text
Healthy
Degraded
Unavailable
Unknown
```

---

# 156. Unknown Health State

Do not show `Healthy` just because no error was reported.

---

# 157. Connectivity States

---

# 158. UIST-OFFLINE-001 — Browser Offline

If detectable:

```text
Нет соединения с сетью.
```

---

# 159. Offline Public Pages

Already loaded content MAY remain readable where architecture permits.

Do not imply forms were submitted.

---

# 160. Offline Admin Editing

If local unsaved state exists:

show:

```text
Изменения ещё не сохранены.
```

---

# 161. UIST-OFFLINE-002 — Reconnected

System may retry safe requests automatically.

High-impact mutations with uncertain status require idempotent status check.

---

# 162. Unknown Submit Outcome

Example:

```text
User submits Feedback
network disconnects after server accepted
```

Client MUST NOT blindly create duplicate on retry.

Use idempotency/status architecture.

---

# 163. Disabled State

Disabled is not a sufficient state by itself.

---

# 164. UIST-BLOCK-004 — Disabled With Reason

If button unavailable:

```text
[Publish] disabled

2 blockers must be resolved.
```

---

# 165. Hidden vs Disabled

Use hidden when:

- action unavailable by authorization;
- revealing it provides no value.

Use disabled when:

- action is relevant;
- user can reasonably make it available;
- explanation helps.

---

# 166. Loading Button

During mutation:

```text
[Saving…]
```

or equivalent.

Prevent duplicate activation.

---

# 167. Button Success State

Temporary:

```text
Saved
```

MAY be used, but button should return to consistent normal label.

---

# 168. Toast State Rules

Appropriate for:

- small completed action;
- non-blocking success;
- low-risk status update.

Not sufficient for:

- destructive failure;
- transaction submission;
- blocking error.

---

# 169. Banner State Rules

Suitable for:

- cross-page service degradation;
- security/session state;
- global operational concern.

Do not use global banners for local optional warnings.

---

# 170. Inline State Rules

Best for:

- field validation;
- current component failure;
- readiness blocker;
- local async status.

---

# 171. Modal State Rules

Modal should not be used solely to announce every error.

Appropriate for:

- high-impact confirmation;
- session expiry requiring reauth;
- critical decision.

---

# 172. Status Badge Rules

Badge suitable for persistent business/lifecycle states:

```text
Draft
Published
Archived
Blocked
```

---

# 173. Badge Overload Prohibited

Do not display 8 competing badges on one entity card.

Prioritize meaningful state.

---

# 174. Color Semantics

Exact palette determined by design system.

Semantics must stay consistent:

```text
Error
Warning
Success
Neutral
Informational
```

---

# 175. Color Alone Prohibited

Every state requires one or more:

- text;
- icon;
- label;
- semantic markup.

---

# 176. Icon Semantics

Same icon should not mean:

```text
warning
```

on one screen and:

```text
archived
```

on another.

---

# 177. Motion Semantics

Animations MAY communicate:

- loading;
- progress;
- state change.

Critical meaning must remain without motion.

---

# 178. Reduced Motion

All animated state transitions must respect user preference where applicable.

---

# 179. Focus After Error

For large form:

```text
focus → error summary
```

or first invalid field according to form design.

---

# 180. Focus After Success

Do not unexpectedly move focus away unless context changes significantly.

---

# 181. Focus After Modal Close

Return to initiating control.

---

# 182. Screen Reader Live Announcements

Use appropriately for:

- form submit success/error;
- PDF ready;
- item added to Builder;
- async validation.

Avoid announcing every minor background refresh.

---

# 183. State Copy Principles

State copy MUST be:

```text
specific
brief
actionable
non-technical by default
```

---

# 184. Bad Error Copy

```text
Something went wrong.
```

---

# 185. Better Error Copy

```text
Не удалось сформировать PDF.
Ваш выбор сохранён.

[Повторить]
```

---

# 186. Bad Warning Copy

```text
Warning!
```

---

# 187. Better Warning Copy

```text
Не удалось проверить ссылку на видеовизитку.
Вы можете сохранить черновик и проверить ссылку позже.
```

---

# 188. State Copy Must Not Overpromise

Do not say:

```text
Мы обязательно свяжемся.
```

unless guaranteed.

---

# 189. State Copy Must Respect Unknowns

If cause unknown:

```text
Не удалось загрузить видео.
```

not:

```text
Видео удалено.
```

---

# 190. State Persistence

Some UI states are ephemeral.

Others need persistence.

---

# 191. Ephemeral States

Examples:

- hover;
- menu open;
- temporary toast;
- current tab unless route-backed.

---

# 192. Persistent States

Examples:

- Draft;
- Archived;
- Published;
- Feedback status;
- Operator exception;
- processing job state.

---

# 193. URL-Persisted States

Useful for:

- current route;
- meaningful filter;
- public locale;
- entity identity.

---

# 194. Session-Persisted States

Examples:

- Public Builder selection;
- active locale;
- temporary wizard progress.

---

# 195. Server-Persisted States

Business truth remains server-side.

---

# 196. State Hydration

Client MUST avoid visual flash of incorrect privacy or lifecycle state.

Example prohibited:

```text
private contact briefly shown
→ hidden after hydration
```

---

# 197. Server-Rendered Privacy

Private/public projection must be determined before client receives data.

---

# 198. State Transition Validation

Every business transition must be server-authoritative.

UI merely reflects valid choices.

---

# 199. Optimistic UI

May be used for low-risk actions:

- toggle temporary Builder selection;
- marking notification viewed;

if rollback safe.

---

# 200. Optimistic UI Prohibited by Default for

- Publish;
- Booked;
- create Casting;
- delete professional content;
- send professional reply;
- Contact visibility.

---

# 201. UIST-OPT-001 — Optimistic Pending

If optimistic state used, UI needs pending distinction until server confirms where ambiguity matters.

---

# 202. Optimistic Rollback

Failure returns UI to authoritative server state and explains issue.

---

# 203. Readiness State Model

Recommended UI-level representation:

```text
Ready
Attention
Blocked
```

with underlying reasons.

---

# 204. Readiness Is Explainable

Never use only opaque:

```text
82% ready
```

without reasons.

---

# 205. Ready

No blocker for target action.

---

# 206. Attention

Warnings exist but action MAY remain possible.

---

# 207. Blocked

One or more hard conditions prevent target action.

---

# 208. Readiness Per Capability

Admin MAY see separate readiness:

```text
Profile Readiness
Portfolio Readiness
Questionnaire Readiness
I18N Readiness
Link / QR Readiness
```

---

# 209. No Actress Scoring

Readiness does not rank acting ability, attractiveness or professional desirability.

---

# 210. State Aggregation

Parent module may aggregate child state.

Example:

```text
Questionnaire: Blocked
Reason:
1 required link invalid
```

---

# 211. Aggregation Must Preserve Detail

Click gives exact blocker.

---

# 212. State Invalidation

When source changes, dependent state is recalculated or marked stale.

---

# 213. State Event Model

Conceptually:

```text
Source Changed
 ↓
Dependency Invalidated
 ↓
Dependent UI State = Stale / Processing
 ↓
Rebuild
 ↓
Ready / Error
```

---

# 214. State Recovery Principle

Every recoverable state SHOULD provide a path out.

---

# 215. Error Recovery Examples

```text
Retry
Edit field
Reconnect account
Open source entity
Start new session
```

---

# 216. Non-Recoverable State

If action truly unavailable:

provide useful alternative destination.

Example:

```text
This questionnaire is no longer available.
[Open current questionnaire]
```

---

# 217. 404 State

Must distinguish product-safe absence while retaining navigation.

---

# 218. Admin 404

Could offer:

```text
Dashboard
Parent module
```

---

# 219. Public 404

Could offer:

```text
Profile
Portfolio
Questionnaire
Contact
```

---

# 220. Maintenance State

If planned maintenance mode ever added:

public/admin behaviour must be explicitly designed.

Do not expose infrastructure details.

---

# 221. State and Analytics

Meaningful states MAY emit analytics.

Examples:

```text
public_builder_pdf_failed
questionnaire_generation_failed
```

But analytics is not state authority.

---

# 222. State and Audit

Business/lifecycle changes should produce Audit Events.

UI-only state generally does not.

---

# 223. Examples Requiring Audit

```text
Draft → Published
Published → Archived
Opportunity → Booked
Contact public visibility enabled
AI suggestion applied
```

---

# 224. Examples Not Requiring Business Audit

```text
Menu opened
Accordion expanded
Tooltip viewed
```

---

# 225. State and Notifications

Notification MAY reflect state change but must not create the business state.

---

# 226. Example

```text
Feedback persisted
→ state = New Inquiry
→ notification triggered
```

not reverse.

---

# 227. Public Questionnaire Builder State Machine

Conceptually:

```text
NEW
 ↓
CONFIGURING
 ↓
VALIDATING
 ↓
PREVIEW_READY
 ↓
GENERATING
 ↓
GENERATED
```

Branches:

```text
VALIDATING → INVALID_CONFIGURATION
GENERATING → GENERATION_FAILED
ANY ACTIVE → EXPIRED
```

---

# 228. Builder Session UI States

Recommended labels should be user-friendly, not expose internal enum names.

---

# 229. Questionnaire State Example

```text
Questionnaire:
Published

Current Editing Draft:
Unsaved Changes

Readiness:
Attention

PDF:
Ready

EN Translation:
Stale
```

UI must support layered state.

---

# 230. Emotional Grid State Example

```text
Grid:
Draft

Photo Count:
16/16

Cells:
14/16 confirmed

Readiness:
Blocked

Reason:
2 cells require confirmation
```

---

# 231. Media State Example

```text
Original:
Ready

Web derivative:
Ready

PDF derivative:
Failed

Usage:
Published Portfolio
```

Admin should not interpret asset as entirely broken.

---

# 232. Casting AI State Example

```text
Source:
Saved

AI Analysis:
Generated

Requirements:
3 confirmed
2 pending review

Questionnaire:
Not created
```

---

# 233. Virtual Operator State Example

```text
Issue:
Primary Showreel unavailable

Severity:
High

Status:
Open

Suggested action:
Replace URL

Last check:
...
```

---

# 234. Notification State Example

```text
Feedback:
Saved

Internal Notification:
Sent

WhatsApp Notification:
Failed
```

This exact separation is critical.

---

# 235. UI State Test Matrix

Every major screen MUST test minimum:

```text
Loading
Ready
Empty
Error
```

Plus domain-specific:

```text
Warning
Blocked
Processing
Archived
Unauthorized
Stale
Conflict
```

where applicable.

---

# 236. UIST-TEST-001 — Loading

No fake content, navigation available.

---

# 237. UIST-TEST-002 — Empty

No error styling; meaningful next action.

---

# 238. UIST-TEST-003 — Partial Failure

One failed secondary component does not collapse full page.

---

# 239. UIST-TEST-004 — Form Validation

Values preserved, errors correctly mapped.

---

# 240. UIST-TEST-005 — Save Failure

Dirty state remains; no false `Saved`.

---

# 241. UIST-TEST-006 — Publish Block

Clear blockers and resolution links.

---

# 242. UIST-TEST-007 — Async Operation

Processing → success/error correctly.

---

# 243. UIST-TEST-008 — Concurrency

No silent overwrite.

---

# 244. UIST-TEST-009 — Stale AI Output

Cannot silently apply stale suggestion.

---

# 245. UIST-TEST-010 — Session Expiry

Public Builder fails safely.

---

# 246. UIST-TEST-011 — Notification Failure

Feedback state remains successful.

---

# 247. UIST-TEST-012 — Service Degradation

AI outage leaves core manual workflow.

---

# 248. UIST-TEST-013 — Accessibility

State changes announced appropriately and not represented by color only.

---

# 249. UIST-TEST-014 — Mobile Error

Error remains actionable with mobile keyboard/viewport.

---

# 250. UIST-TEST-015 — Historical Revision

Clearly read-only and distinguishable from current Draft.

---

# 251. UI State Anti-Pattern UIST-AP-001

**Spinner Everywhere**

Every request blanks the screen.

---

# 252. UIST-AP-002

**Empty Before Loaded**

Zero-state flashes while loading.

---

# 253. UIST-AP-003

**Disabled Without Reason**

---

# 254. UIST-AP-004

**Green Means Published**

Without text/lifecycle semantics.

---

# 255. UIST-AP-005

**Save Success Before Server Confirmation**

---

# 256. UIST-AP-006

**Notification Success = Inquiry Success**

---

# 257. UIST-AP-007

**Stale = Deleted**

Old valid representation disappears unnecessarily.

---

# 258. UIST-AP-008

**Historical = Stale**

Immutable revision shown as needing update.

---

# 259. UIST-AP-009

**AI Generated = Applied**

---

# 260. UIST-AP-010

**Applied = Published**

---

# 261. UIST-AP-011

**Could Not Verify = Invalid**

---

# 262. UIST-AP-012

**Conflict = Generic Error**

---

# 263. UIST-AP-013

**One Media Derivative Failure = Entire Asset Missing**

---

# 264. UIST-AP-014

**Every Warning Is Red**

---

# 265. UIST-AP-015

**Every Warning Blocks**

---

# 266. UIST-AP-016

**Private Data Flash**

Rendered then hidden client-side.

---

# 267. UIST-AP-017

**Fake Progress Percentage**

---

# 268. UIST-AP-018

**Toast-Only Critical Error**

User misses important failure.

---

# 269. UIST-AP-019

**Global Banner for Local Error**

Creates unnecessary alarm.

---

# 270. UIST-AP-020

**Opaque Readiness Score**

No explanation.

---

# 271. UIST-AP-021

**Published = Public**

When visibility rules differ.

---

# 272. UIST-AP-022

**Optimistic Booked**

Opportunity looks Booked before server confirmation.

---

# 273. UIST-AP-023

**AI Outage Blocks Manual Workflow**

---

# 274. UIST-AP-024

**Expired Builder Restores Stale Private Items**

---

# 275. UIST-AP-025

**Raw Infrastructure Status in Public UI**

---

# 276. UI State Quality Gate

Before a screen/flow is implementation-ready, define:

- [ ] Initial state
- [ ] Loading state
- [ ] Ready state
- [ ] Empty state
- [ ] Editing/dirty state where applicable
- [ ] Validation state
- [ ] Processing state
- [ ] Success state
- [ ] Warning state
- [ ] Error state
- [ ] Blocking state
- [ ] Stale state where applicable
- [ ] Unauthorized state
- [ ] Expired state where applicable
- [ ] Conflict state where applicable
- [ ] Degraded dependency state
- [ ] Lifecycle state
- [ ] AI state where applicable
- [ ] Recovery action
- [ ] Accessibility announcement
- [ ] Mobile presentation
- [ ] Analytics/Audit requirements

---

# 277. State Specification Template

Для каждого non-trivial state SHOULD фиксироваться:

```text
State ID
Scope
Trigger
Meaning
Visual treatment
Message
Allowed actions
Blocked actions
Recovery
Persistence
Analytics
Audit
Accessibility
```

---

# 278. Example — PDF Generation Failure

```text
State ID:
UIST-PDF-FAIL-001

Trigger:
PDF renderer failed.

Meaning:
Configuration is valid but output could not be generated.

User message:
"Не удалось сформировать PDF. Ваш выбор сохранён."

Allowed:
Retry
Return to configuration
Use HTML preview

Blocked:
Download nonexistent PDF

Persistence:
Builder session remains active.

Audit:
Technical job log, not necessarily business audit.
```

---

# 279. Example — Questionnaire Publish Blocked

```text
State ID:
UIST-QST-BLOCK-001

Trigger:
Readiness blockers exist.

Message:
"Анкету нельзя опубликовать."

Reasons:
• отсутствует фото в полный рост;
• один QR ведёт на недоступную ссылку.

Actions:
[Исправить фото]
[Открыть ссылки]
```

---

# 280. Example — Feedback Notification Failure

```text
Feedback:
Saved

Public state:
Success

Admin notification:
Failed

Operator:
Warning / High depending policy

Correct UI:
"Обращение принято."
```

---

# 281. Example — AI Stale

```text
State:
AI Draft — Stale

Reason:
Source Profile changed after generation.

Actions:
[Просмотреть изменения]
[Создать заново]
[Продолжить редактирование вручную]
```

---

# 282. Example — Media Partial Processing

```text
Original
✓ Ready

Web
✓ Ready

PDF
✕ Failed

Thumbnail
✓ Ready
```

Action:

```text
[Повторить PDF derivative]
```

---

# 283. Example — Builder Permission Revocation

```text
Selected item:
Project A

Current eligibility:
Revoked

Preview:
"Один из выбранных материалов больше недоступен."

Action:
[Проверить состав]
```

No private policy detail.

---

# 284. Example — Admin Concurrency

```text
Вы начали редактирование версии 7.
Сейчас сохранена версия 8.

[Просмотреть новую версию]
[Скопировать мои изменения]
```

---

# 285. Example — Service Degraded

Admin:

```text
BB Assistant временно недоступен.

Ручное редактирование работает без ограничений.
```

Public:

no AI-related state at all if not relevant.

---

# 286. State Consistency Across Modules

The same semantic state SHOULD use consistent wording.

Example:

```text
Draft
```

should not become:

```text
Pending
Unpublished
Working Copy
```

randomly across modules unless they represent distinct states.

---

# 287. State Localization

Canonical internal state identifier remains stable.

UI label localized.

Example:

```text
DRAFT
RU → Черновик
EN → Draft
```

---

# 288. State Localization Consistency

Switching locale MUST NOT change underlying lifecycle state.

---

# 289. State Time Information

Some state benefits from timestamp:

```text
Last saved
Last validated
Generated
Published
Last retry
```

Only show where meaningful.

---

# 290. Relative Time

Admin MAY show:

```text
5 минут назад
```

but detailed exact time should be available where audit/operations matter.

---

# 291. Avoid Misleading Freshness

`Uploaded today` does not equal `Shot today`.

State/date labels must use correct semantics.

---

# 292. State Ownership

Every important state MUST have an owning domain/system.

Example:

```text
Questionnaire Readiness
owned by Questionnaire validation rules

Notification Delivery State
owned by notification subsystem

AI Draft State
owned by BB Assistant
```

Avoid multiple subsystems mutating same state informally.

---

# 293. Derived State

Some UI states are computed projections:

```text
Attention
Blocked
Ready
```

They should be reproducible from authoritative facts/rules.

---

# 294. No AI-Owned Readiness

AI MAY explain/suggest.

Hard readiness gates use deterministic rules wherever possible.

---

# 295. State Transition Audit Rule

Audit significant transitions:

```text
Draft → Published
Published → Archived
Open → Resolved
Offer → Booked
Visibility false → true
```

---

# 296. UI-Only Transitions Do Not Need Audit

```text
Tab 1 → Tab 2
Collapsed → Expanded
```

---

# 297. State and Caching

Cached UI MUST not serve a privacy/lifecycle state known to be invalid.

Security/visibility invalidation has priority over cache convenience.

---

# 298. State and Public Projection

Public UI receives final effective projection state.

It does not infer:

```text
Draft + visibility
```

client-side.

---

# 299. State and Background Jobs

Background job completion SHOULD trigger dependent refresh/invalidation.

---

# 300. State Refresh

UI MAY use:

- polling;
- SSE;
- WebSocket;
- route refresh;
- manual refresh;

architecture later decides.

UX contract only requires coherent observable state.

---

# 301. State Flicker

Frequent switching:

```text
Ready → Loading → Ready
```

for tiny background refreshes SHOULD be avoided.

Use subtle refreshing state if needed.

---

# 302. Refreshing State

Optional:

```text
Updating…
```

while existing valid content remains visible.

---

# 303. Last Valid Content Principle

If new derived fetch/rebuild fails:

retain last valid public representation where policy permits.

---

# 304. State Accessibility Requirement

Every state that materially changes available action MUST be perceivable:

- visually;
- programmatically where applicable;
- without relying on motion/color alone.

---

# 305. State Mobile Requirement

Status banners/cards MUST not consume unreasonable viewport space.

Critical actions remain visible.

---

# 306. State Performance Requirement

State handling must not trigger unnecessary full-page rerenders that disrupt user position/context.

---

# 307. State Security Requirement

Error/state messages MUST not disclose:

- private resource names;
- private contact values;
- backend paths;
- access control internals;
- secrets.

---

# 308. UI State Acceptance Criteria

UI State system считается корректным, если:

1. Loading и Empty различаются;
2. Loading и Processing различаются;
3. Save и Publish states различаются;
4. Saved Draft не выглядит Published;
5. Warning и Error различаются;
6. Warning не блокирует без business rule;
7. blockers объясняют причину и путь исправления;
8. Stale отличается от Invalid и Historical;
9. concurrency имеет отдельный Conflict state;
10. Unauthorized/Expired различаются от generic Error;
11. AI-generated output явно обозначен;
12. AI Generated, Applied и Published не смешиваются;
13. optional service degradation локализована;
14. partial failures представлены частично, а не как total failure;
15. Feedback success не зависит от notification delivery;
16. Public UI не показывает internal technical state без необходимости;
17. Admin UI имеет достаточно detail для resolution;
18. state semantics consistent across modules;
19. state changes accessible keyboard/screen reader users;
20. private state/data не появляются transiently в client.

---

# 309. Final Unified State Model

```text
                         UI STATE SYSTEM

                              START
                                ↓
                             LOADING
                                ↓
                ┌───────────────┼───────────────┐
                ↓               ↓               ↓
              READY           EMPTY           ERROR
                │
                ├──────── VIEW / NAVIGATE ─────────────┐
                │                                       │
                ↓                                       │
              EDIT                                      │
                ↓                                       │
              DIRTY                                     │
                ↓                                       │
           VALIDATING                                   │
             /      \                                   │
         INVALID      VALID                             │
            │           │                               │
            │           ↓                               │
            │       PROCESSING                          │
            │        /     \                            │
            │    SUCCESS   ERROR                        │
            │                                           │
            └────── FIX / RETRY ────────────────────────┘

    CROSS-CUTTING STATES:
    WARNING
    BLOCKED
    STALE
    CONFLICT
    UNAUTHORIZED
    EXPIRED
    DEGRADED
    OFFLINE

    BUSINESS/LIFECYCLE STATES:
    DRAFT
    READY
    PUBLISHED
    ARCHIVED
    HISTORICAL

    AI STATES:
    GENERATED
    EDITED
    APPLIED
    DISCARDED
    FAILED
    STALE
```

---

# 310. Финальный принцип

> **Состояние интерфейса должно быть точным отражением реального состояния задачи, а не декоративным эффектом. Пользователь всегда должен понимать, загружается ли информация, сохранены ли изменения, выполняется ли операция, существует ли блокирующая проблема, устарел ли результат, был ли конфликт версий и какое действие доступно дальше. Особое внимание необходимо уделять разграничению Draft, Published, Stale, Error, AI Suggestion и подтверждённого профессионального факта — эти состояния не должны визуально или логически смешиваться.**