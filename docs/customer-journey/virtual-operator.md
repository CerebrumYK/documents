# VIRTUAL OPERATOR JOURNEY

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Подробный Customer Journey виртуального оператора профессионального портфолио

**Целевой файл:** `docs/customer-journey/virtual-operator.md`  
**Документ:** DOC-028  
**Статус:** ✅ Completed  
**Тип:** Customer Journey / Internal Operations

**Persona:** `PERSONA-VOP — Virtual Portfolio Operator`

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/overview.md`
- `docs/customer-journey/personas.md`
- `docs/customer-journey/method-6-3-5.md`

---

# 1. Назначение документа

Настоящий документ описывает Customer Journey внутреннего Virtual Portfolio Operator.

Virtual Operator не является:

- публичным chatbot;
- владельцем профессиональных данных;
- автономным администратором;
- Casting Decision Maker;
- BB Assistant;
- системой автоматической публикации.

Его роль:

> **постоянно анализировать состояние профессионального профиля и превращать большой объём технической и административной рутины в короткий список понятных действий, действительно требующих внимания человека.**

---

# 2. Journey ID

Основной Journey:

```text
CJM-VOP-001
```

Название:

```text
Virtual Portfolio Operator — Observe, Detect, Prepare, Escalate, Resolve
```

---

# 3. Главная продуктовая цель

Целевая административная модель:

```text
Admin manually checks everything
```

должна быть заменена на:

```text
System monitors normal state
        ↓
Virtual Operator detects exceptions
        ↓
Admin reviews only actionable items
```

---

# 4. Primary User of Virtual Operator

Хотя Virtual Operator является отдельной Persona, его непосредственным потребителем является:

```text
Administrator / Profile Owner
```

Следовательно, качество Virtual Operator оценивается по тому, насколько он:

- сокращает ручные проверки;
- не создаёт лишний шум;
- не выполняет опасные действия;
- объясняет свои выводы;
- предоставляет быстрый путь к исправлению.

---

# 5. Основной Mental Model администратора

Администратор не должен спрашивать:

```text
Что мне сегодня проверить?
```

Система должна отвечать:

```text
Вот что действительно требует внимания сегодня.
```

---

# 6. Основной Mental Model Virtual Operator

```text
OBSERVE
 ↓
UNDERSTAND CHANGE
 ↓
CHECK DEPENDENCIES
 ↓
DETECT EXCEPTION
 ↓
ASSESS PRIORITY
 ↓
PREPARE ACTION
 ↓
ASK HUMAN ONLY IF NECESSARY
 ↓
VERIFY RESULT
```

---

# 7. Основные функции Persona

Virtual Operator выполняет пять классов задач:

```text
VOP-CAP-01 Observation
VOP-CAP-02 Validation
VOP-CAP-03 Suggestion
VOP-CAP-04 Safe Automation
VOP-CAP-05 Exception Prioritization
```

---

# 8. VOP-CAP-01 — Observation

Operator отслеживает изменения и состояние системы.

Примеры:

- загружено новое изображение;
- изменён Profile Fact;
- изменён Contact visibility;
- опубликован новый Project;
- истекает срок Casting;
- внешний URL перестал отвечать;
- Questionnaire dependency изменилась.

---

# 9. VOP-CAP-02 — Validation

Operator проверяет формальные условия:

- completeness;
- broken links;
- missing mandatory metadata;
- stale derivatives;
- invalid references;
- expired temporary data;
- readiness blockers.

---

# 10. VOP-CAP-03 — Suggestion

Operator может предложить:

- classification;
- crop;
- metadata completion;
- Questionnaire rebuild;
- update candidate;
- relation between entities;
- revised template default.

Suggestion не является фактом.

---

# 11. VOP-CAP-04 — Safe Automation

Полностью автоматически допускаются только операции, не меняющие профессиональный смысл.

Например:

```text
thumbnail generation
web derivative generation
cache invalidation
preview rebuild
safe metadata extraction
link validation
readiness recalculation
```

---

# 12. VOP-CAP-05 — Exception Prioritization

Operator должен определять:

```text
что важно сейчас
```

а не просто создавать список всех найденных несовершенств.

---

# 13. Основной Journey

```text
Domain Change
 ↓
Observation
 ↓
Automatic Validation
 ↓
No Issue?
 ├── YES → Safe Automatic Processing → Done
 └── NO
       ↓
   Exception Created
       ↓
   Priority Calculated
       ↓
   Can safely auto-resolve?
       ├── YES → Resolve → Verify
       └── NO
             ↓
         Suggest Action
             ↓
         Human Review
          /       \
      Approve    Reject/Edit
         │           │
         └─────┬─────┘
               ↓
             Apply
               ↓
             Verify
               ↓
          Recalculate State
```

---

# 14. Operator Trigger Model

Virtual Operator может запускаться:

- по Domain Event;
- после изменения сущности;
- по scheduled validation;
- при открытии Dashboard;
- после failed background job;
- после analytics aggregation;
- по explicit Admin request.

Конкретная техническая реализация определяется Architecture.

---

# 15. Event-Driven Principle

Где возможно, система SHOULD реагировать на изменения.

Например:

```text
PortfolioItemUpdated
→ Questionnaire dependency check
→ Public projection invalidation
```

Это предпочтительнее постоянного полного пересканирования всей системы.

---

# 16. Scheduled Validation Principle

Некоторые состояния требуют периодической проверки:

- external URL;
- Casting deadline;
- expiring public session;
- stale content age signal;
- notification retry.

Для них допустим scheduled process.

---

# 17. Main Five-Minute Scenario

Метод 6-3-5 определил типовой внутренний сценарий:

```text
00:00–00:30
New events received

00:30–01:15
New media analysed

01:15–02:00
Missing metadata detected

02:00–02:45
Stale Questionnaire detected

02:45–03:30
Broken URL detected

03:30–04:15
Casting deadline prioritized

04:15–05:00
Admin Action Dashboard prepared
```

---

# 18. Five-Minute Success Criterion

Через короткий операционный цикл Admin должен получить:

```text
critical exceptions
+
why they matter
+
where they are
+
what is suggested
+
what requires confirmation
```

а не технический raw log.

---

# 19. Stage VOP-01 — New Media Detection

Пример:

```text
16 new images uploaded
```

Operator фиксирует:

- Media Asset IDs;
- type;
- dimensions;
- technical metadata;
- source context;
- possible duplicates.

---

# 20. Media Analysis Boundary

Operator MAY определить вероятные свойства:

- portrait/landscape;
- probable Close-Up;
- probable Full Body;
- possible Emotional Portfolio image;
- visually similar duplicate.

Но classification остаётся:

```text
Suggestion
```

пока business meaning не подтверждён.

---

# 21. Incorrect Model

Недопустимо:

```text
AI thinks image = Full Body
→ automatically makes Primary Full Body
→ publishes
```

---

# 22. Correct Model

```text
Media uploaded
 ↓
Probable Full Body detected
 ↓
Suggestion
 ↓
Admin confirms Portfolio category
 ↓
Optional Primary selection
 ↓
Publish flow
```

---

# 23. Duplicate Detection

Operator MAY обнаруживать:

- exact file duplicate;
- technical duplicate;
- probable visual duplicate.

---

# 24. Duplicate Action

Для exact duplicates MAY предлагаться reuse existing Media Asset.

Но автоматическое удаление нового файла без documented safe policy не допускается.

---

# 25. Stage VOP-02 — Missing Metadata

Operator проверяет mandatory metadata по Domain Rules.

Пример:

```text
Emotional Portfolio
→ Shooting Date missing
```

Создаётся blocking Observation.

---

# 26. Observation Structure

Каждая значимая Observation SHOULD содержать:

```text
Observation ID
Entity Type
Entity ID
Issue Type
Severity
Reason
Detected At
Suggested Action
Affected Journeys
Status
```

Конкретная Data Model определяется позже.

---

# 27. Observation Example

```text
Entity:
Emotional Portfolio — Session 2026-07

Issue:
Missing Shooting Date

Severity:
High

Impact:
Cannot become Ready for professional publication

Suggested action:
Add Shooting Date
```

---

# 28. Stage VOP-03 — Readiness Evaluation

После изменения Operator пересчитывает readiness.

Например:

```text
Questionnaire
 ├── Primary Close-Up ✓
 ├── Primary Full Body ✓
 ├── Valid Contact ✓
 ├── Broken Video URL ✕
 └── Public URL ✓
```

---

# 29. Blocking vs Warning

Operator MUST отличать:

### Blocking

Невозможно безопасно завершить действие.

### Warning

Действие возможно, но требует внимания.

### Information

Полезная информация без необходимости решения.

---

# 30. Example Severity

```text
CRITICAL
Primary public profile unavailable

HIGH
Primary Video broken

MEDIUM
Optional description missing

LOW
Archived item lacks caption
```

Точная severity matrix определяется Operations/UX documentation.

---

# 31. Stage VOP-04 — Dependency Analysis

Ключевая функция:

> после изменения Source определить, какие представления зависят от него.

Пример:

```text
Primary Close-Up changed
```

может влиять на:

- Public Profile;
- Quick View;
- Prepared Questionnaire draft/current revision;
- Builder defaults;
- Preview;
- Search projection;
- future PDF generation.

---

# 32. Change Once, Propagate Everywhere

Нормативный принцип:

```text
Master Data Change
 ↓
Dependency Graph
 ↓
Affected Projections
 ↓
Invalidate / Rebuild / Mark Stale
```

Admin не должен вручную заходить в каждый раздел.

---

# 33. Historical Snapshot Exception

Не все зависимые данные должны обновляться.

Например:

```text
Published historical Questionnaire Revision
```

остаётся immutable.

Operator должен различать:

```text
dynamic projection
```

и:

```text
historical snapshot
```

---

# 34. Stage VOP-05 — Stale Detection

Derived Content может стать Stale.

Примеры:

- Questionnaire Preview;
- Search index;
- Emotional Grid preview;
- cached Public Projection;
- generated non-historical export candidate.

---

# 35. Stale ≠ Invalid

Объект может быть:

```text
Stale but still last valid published representation
```

Поэтому failed rebuild не должен автоматически удалять working published result.

---

# 36. Stale Handling

Пример:

```text
Source changed
 ↓
Derived asset marked Stale
 ↓
Rebuild queued
 ↓
New derivative validates
 ↓
Replace active derivative
```

Если rebuild fails:

```text
old valid derivative remains
+
admin exception
```

где это допустимо архитектурой.

---

# 37. Stage VOP-06 — External Link Validation

Operator должен проверять критические Public Links.

Например:

- Video Intro;
- Showreel;
- external Project media;
- public professional URLs.

---

# 38. Broken Link Behaviour

Нельзя автоматически удалять broken link.

Правильно:

```text
Broken URL detected
 ↓
Observation
 ↓
Admin can:
- fix
- replace
- disable
- ignore temporarily
```

---

# 39. Link Validation Caveat

Temporary network error не должен сразу трактоваться как окончательный broken resource.

Architecture должна предусмотреть:

- retries;
- timeout;
- transient failure distinction.

---

# 40. Stage VOP-07 — QR Validation

Operator SHOULD проверять QR-related readiness для Questionnaire/Public Builder outputs.

Минимально:

```text
target URL valid
target allowed
target not admin/private
canonical target available
```

---

# 41. QR Dependency

Если canonical link изменился:

```text
URL changed
 ↓
QR derivative/cache stale
 ↓
regeneration required
```

---

# 42. QR Safety Exception

Высокий priority:

```text
QR target resolves to non-public/admin resource
```

Такой QR не должен выпускаться в новый public document.

---

# 43. Stage VOP-08 — Casting Deadline Monitoring

Castings имеют временную чувствительность.

Operator должен выявлять:

- upcoming deadline;
- deadline today;
- overdue action;
- pending unanswered request.

---

# 44. Deadline Priority

Приближающийся Casting deadline обычно важнее:

- missing optional Portfolio caption;
- low-value metadata issue.

---

# 45. Example Dashboard Priority

```text
🔴 Casting deadline tomorrow
🔴 New role invitation unread
🟠 Primary Showreel unavailable
🟠 Questionnaire has blocking validation
🟡 12 photos require classification
🟢 4 thumbnails regenerated automatically
```

---

# 46. Stage VOP-09 — Feedback Monitoring

Operator MAY выделять:

- unread professional inquiries;
- unanswered high-priority Feedback;
- Casting Invitation;
- Role Offer;
- Request Materials.

---

# 47. Feedback Boundary

Operator не определяет самостоятельно:

```text
This inquiry is definitely a Casting
```

Он может предложить classification.

Human decides whether to create/link Casting.

---

# 48. Stage VOP-10 — Notification Monitoring

Operator контролирует:

- failed notification;
- retry exhausted;
- delayed quiet-hours delivery;
- channel unavailable.

---

# 49. Notification Failure Priority

Notification failure не равна Feedback loss.

Dashboard должен различать:

```text
Inquiry safely stored
External notification failed
```

---

# 50. Stage VOP-11 — Emotional Grid Assistance

Operator MAY помогать при создании Emotional Grid.

---

# 51. Allowed Emotional Grid Assistance

Допускаются suggestions:

- source photo candidates;
- probable face region;
- crop proposal;
- scale proposal;
- cell ordering proposal.

---

# 52. Mandatory Human Confirmation

Operator MUST NOT автоматически finalize/publish Emotional Grid.

Причины:

- композиция является профессиональным выбором;
- target ≈90% face area требует визуальной проверки;
- эмоциональная репрезентация требует human judgement.

---

# 53. Appearance Safety

Operator не может использовать генеративное редактирование для:

- заполнения crop;
- изменения expression;
- исправления лица;
- создания недостающей части изображения.

---

# 54. Stage VOP-12 — Questionnaire Monitoring

Operator должен отслеживать:

- missing mandatory Portfolio items;
- invalid links;
- stale Preview;
- revision dependency;
- no Primary Questionnaire;
- public configuration inconsistency.

---

# 55. Questionnaire Recommendation

Operator MAY предложить:

```text
Compact Questionnaire is often customized by users to remove Training.
Review default composition?
```

Это аналитическая recommendation.

---

# 56. No Automatic Template Mutation

Analytics никогда не должна приводить к:

```text
system automatically removes Training from default template
```

без Product/Admin decision.

---

# 57. Stage VOP-13 — Public Builder Monitoring

Operator MAY анализировать агрегированно:

- template usage;
- often added blocks;
- often removed blocks;
- generation failures;
- zero-result searches.

---

# 58. Builder Analytics Purpose

Не персонализировать скрыто конкретного visitor.

Цель:

```text
improve default professional presentation
```

---

# 59. Example Builder Recommendation

```text
Observation:
61% of Casting template sessions add Emotional Grid manually.

Suggestion:
Consider making Emotional Grid default in Casting template.

Decision:
Human Product/Admin Review required.
```

---

# 60. Stage VOP-14 — Content Freshness

Operator MAY создавать сигнал:

```text
Review recommended
```

для материала, который потенциально требует проверки.

---

# 61. Content Freshness Is Not Expiration

Старое фото:

```text
age > threshold
```

не означает автоматически:

```text
invalid
```

Operator лишь предлагает review.

---

# 62. Freshness Factors

Potential signals MAY включать:

- Shooting Date;
- age of primary Portfolio media;
- broken links;
- outdated biography references;
- ended Training incorrectly marked ongoing.

Но policy определяется модулями.

---

# 63. No Automatic Deletion by Age

Virtual Operator MUST NOT:

- delete;
- hide;
- archive;

professional content только по возрасту.

---

# 64. Stage VOP-15 — Localization Monitoring

Operator SHOULD выявлять:

- untranslated critical field;
- locale fallback in critical Questionnaire;
- inconsistent name spelling;
- missing localized Project description;
- stale localized PDF;
- missing translated Template label.

---

# 65. Translation Suggestion

Operator MAY вызвать/предложить BB Assistant для draft translation.

Но Virtual Operator и BB Assistant остаются отдельными capabilities.

---

# 66. Virtual Operator ≠ BB Assistant

## Virtual Operator

Отвечает:

```text
Что требует внимания?
Что сломано?
Что устарело?
Что можно подготовить?
```

## BB Assistant

Отвечает:

```text
Как профессионально сформулировать текст?
```

---

# 67. Virtual Operator ≠ Casting AI

## Virtual Operator

Следит за состоянием системы.

## Casting AI

Анализирует конкретный Casting Source и сопоставляет requirements с Profile.

---

# 68. Virtual Operator ≠ Theme AI

## Virtual Operator

Может обнаружить:

```text
published theme has accessibility warning
```

## Theme AI

Создаёт visual Theme Proposal.

---

# 69. Four AI Boundaries

Целевой продукт должен различать:

```text
1. Virtual Operator
   Operations / Exceptions

2. BB Assistant
   Professional Writing

3. Casting AI
   Casting Requirement Analysis

4. Theme AI
   Visual Theme Proposal
```

Они не должны сливаться в одного неконтролируемого «AI Assistant».

---

# 70. Stage VOP-16 — Dashboard Composition

Virtual Operator предоставляет Admin dashboard, ориентированный на действия.

---

# 71. Dashboard Must Answer

Минимально:

```text
Что новое?
Что критично?
Что сломано?
Что требует подтверждения?
Что приближается по сроку?
Что система уже исправила сама?
```

---

# 72. Dashboard Must Not Be

Главный экран не должен быть просто:

```text
Profile: 1
Projects: 24
Photos: 180
Contacts: 8
```

Это inventory, а не операционная помощь.

---

# 73. Dashboard Sections

Концептуально:

```text
ACTION REQUIRED
DEADLINES
NEW PROFESSIONAL INQUIRIES
CONTENT HEALTH
PENDING REVIEW
AUTOMATICALLY RESOLVED
```

---

# 74. Admin Action Card

Каждая action card SHOULD отвечать:

- что произошло;
- почему это важно;
- какой объект;
- recommended action;
- severity;
- affected public capability;
- direct link to resolve.

---

# 75. Bad Alert Example

```text
Validation Error 3427
```

---

# 76. Good Alert Example

```text
Showreel недоступен

Публичная ссылка возвращает ошибку.
Это влияет на Journey Casting Director и Director.

[Открыть видео]
[Заменить ссылку]
[Отключить]
```

---

# 77. Explainability Principle

Operator output должен быть объяснимым.

Admin SHOULD видеть:

```text
Observed fact
Reason
Recommendation
```

отдельно.

---

# 78. Observation ≠ Recommendation

Пример:

```text
Observation:
Video URL returned repeated failure.

Recommendation:
Temporarily disable public Video until link is fixed.
```

Факт и предложение не должны смешиваться.

---

# 79. Recommendation ≠ Decision

```text
AI Recommendation
 ↓
Human Decision
```

---

# 80. Human Decision States

Возможные:

```text
Approved
Modified
Rejected
Deferred
```

---

# 81. Rejected Suggestion

Reject не должен означать, что Operator бесконечно создаёт тот же recommendation каждую минуту.

Должны существовать suppression/recheck rules.

Точная модель — Architecture.

---

# 82. Deferred Suggestion

Admin MAY отложить action до:

- даты;
- изменения condition;
- manual revisit.

---

# 83. Operator Noise Control

Критическое требование:

> полезность Virtual Operator зависит не от числа Alerts, а от отношения actionable signals к noise.

---

# 84. Alert Deduplication

Одна проблема не должна создавать десятки одинаковых cards.

Пример:

```text
same broken URL
→ one active exception
```

с историей проверок.

---

# 85. Exception Grouping

Связанные проблемы MAY группироваться.

Например:

```text
Questionnaire "Casting EN"
- broken Video
- stale preview
- untranslated heading
```

---

# 86. Priority Model

Conceptual priority MAY учитывать:

```text
Severity
×
Public Journey Impact
×
Deadline
×
Business Importance
```

---

# 87. Journey Impact

Особенно высокий priority имеют проблемы, блокирующие:

- Primary Profile;
- Full Body;
- Close-Up;
- Video Intro;
- Primary Questionnaire;
- Public Builder;
- Casting CTA;
- incoming professional Feedback.

---

# 88. Safe Automatic Actions

Без human approval MAY выполняться:

```text
thumbnail generation
format conversion
cache clear
search index rebuild
safe preview generation
readiness recalculation
temporary retry
technical metadata extraction
```

если operation не меняет business meaning.

---

# 89. Suggest + Confirm Actions

Требуют human confirmation:

```text
photo professional category
Primary Full Body choice
Primary Close-Up choice
Emotional crop
Project classification
Training migration classification
Contact visibility
Questionnaire default composition
```

---

# 90. Always Human-Controlled Actions

Никогда не должны выполняться автоматически:

```text
publish professional fact
send professional response
accept Casting
accept Role Offer
delete important professional content
change actor appearance
publish Theme
invent experience
```

---

# 91. Safe Automation Failure

Если safe automatic operation fails:

```text
Original / current valid data remains
+
exception created
```

---

# 92. Example — Thumbnail Failure

```text
Original Asset ✓
Existing thumbnail ✓
New derivative failed ✕

Result:
existing valid representation remains
+
retry / admin warning
```

---

# 93. Example — Search Index Failure

Public source data MUST remain authoritative.

Search MAY temporarily degrade, но data не должны изменяться ради индекса.

---

# 94. Example — Public Projection Failure

Последняя корректная Published Projection SHOULD сохраняться там, где архитектура позволяет, пока новый результат не подтверждён.

---

# 95. Admin Daily Journey

Идеальный ежедневный flow:

```text
Admin opens Dashboard
 ↓
3 high-priority actions
 ↓
Resolve Casting deadline
 ↓
Fix Showreel
 ↓
Confirm 4 photo classifications
 ↓
Done
```

Вместо:

```text
Open every section
→ manually inspect everything
```

---

# 96. Admin New-Content Journey

```text
Upload media
 ↓
Operator scans
 ↓
Admin receives grouped suggestions
 ↓
Confirm categories
 ↓
System propagates
 ↓
Only publication decisions remain
```

---

# 97. Admin Casting Journey

```text
New Casting Inquiry
 ↓
Operator highlights
 ↓
Admin reviews
 ↓
Create Casting
 ↓
Casting AI analyses requirements
 ↓
Admin approves requirements
 ↓
Questionnaire Draft
```

Virtual Operator координирует attention, но Casting AI выполняет semantic Casting analysis.

---

# 98. Admin Localization Journey

```text
New RU Project description
 ↓
EN translation missing
 ↓
Operator Observation
 ↓
[Create translation draft]
 ↓
BB Assistant
 ↓
Admin review
 ↓
Apply
```

---

# 99. Admin Theme Journey

```text
Theme accessibility issue detected
 ↓
Operator Warning
 ↓
Admin opens Theme
 ↓
Theme AI/manual editing
 ↓
Preview
 ↓
Human publish
```

---

# 100. Main Happy Path CJM-VOP-001

```text
New photos uploaded
 ↓
Operator extracts metadata
 ↓
Possible duplicates identified
 ↓
Possible Portfolio categories suggested
 ↓
Admin reviews
 ↓
Confirms categories
 ↓
Web derivatives generated
 ↓
Readiness recalculated
 ↓
Public projection updated after allowed publish flow
```

---

# 101. Exception Path CJM-VOP-002

```text
Showreel link breaks
 ↓
Repeated validation failure
 ↓
Exception created
 ↓
High journey impact
 ↓
Dashboard alert
 ↓
Admin replaces URL
 ↓
Validation reruns
 ↓
Exception resolved
```

---

# 102. Dependency Path CJM-VOP-003

```text
Primary Close-Up changed
 ↓
Dependency analysis
 ↓
Quick View invalidated
 ↓
Questionnaire Draft preview stale
 ↓
Search projection stale
 ↓
safe rebuilds occur
 ↓
historical published revisions unchanged
```

---

# 103. Deadline Path CJM-VOP-004

```text
Casting deadline approaches
 ↓
Priority escalates
 ↓
Dashboard
 ↓
Admin opens Casting
 ↓
reviews required next action
```

---

# 104. Emotional Grid Path CJM-VOP-005

```text
Emotional Portfolio completed
 ↓
Operator suggests 16 candidate photos
 ↓
4×4 draft prepared
 ↓
crop proposals
 ↓
Admin adjusts
 ↓
confirms every cell
 ↓
final derivative generated
```

---

# 105. Questionnaire Health Path CJM-VOP-006

```text
External Video URL changes/fails
 ↓
Questionnaires using link identified
 ↓
future outputs marked not ready / warning according to policy
 ↓
Admin fixes source
 ↓
dependencies become valid
```

---

# 106. QR Path CJM-VOP-007

```text
Canonical public URL changed
 ↓
QR cache invalidated
 ↓
new QR generated
 ↓
decode validation
 ↓
exact URL match confirmed
```

---

# 107. Public Builder Analytics Path CJM-VOP-008

```text
Aggregate usage collected
 ↓
Operator identifies repeated customization pattern
 ↓
Recommendation created
 ↓
Admin/Product owner reviews
 ↓
Template may be manually updated
```

---

# 108. Failure Path VOP-F01 — AI unavailable

If AI-based suggestion component unavailable:

- safe deterministic validation continues;
- manual administration remains available;
- no data is lost;
- operator feature may degrade gracefully.

---

# 109. Failure Path VOP-F02 — Background Job unavailable

If background processing unavailable:

- source changes persist;
- jobs remain retryable where applicable;
- critical failed processing is visible;
- no silent data loss.

---

# 110. Failure Path VOP-F03 — False Positive

Admin rejects suggestion.

System:

- records rejection;
- does not apply change;
- SHOULD use recheck/suppression rules to reduce repeated noise.

---

# 111. Failure Path VOP-F04 — Conflicting Admin Change

If Admin edits entity while suggestion is open, Operator MUST revalidate context before applying approved action.

Stale suggestion cannot overwrite newer state silently.

---

# 112. Failure Path VOP-F05 — Permission Restriction

Operator action executes only within authorized administrative context.

It must not bypass Access Control merely because process is automated.

---

# 113. Failure Path VOP-F06 — Derived Rebuild Error

Previous valid result remains available where safe.

Exception remains visible.

---

# 114. Failure Path VOP-F07 — Notification Retry Exhausted

Operator creates actionable exception:

```text
Feedback safely stored.
WhatsApp delivery failed after retries.
```

---

# 115. Failure Path VOP-F08 — Broken QR

New document generation must fail readiness or omit QR according to defined policy rather than output known-invalid QR silently.

---

# 116. Noise Anti-Pattern VOP-AP-01

```text
180 photos
→ 180 separate alerts
```

Correct:

```text
180 new photos
→ grouped review task
```

---

# 117. Noise Anti-Pattern VOP-AP-02

Daily repeated alert for same unchanged low-priority issue.

Use persistent exception state and controlled reminders.

---

# 118. Automation Anti-Pattern VOP-AP-03

```text
analytics shows low use
→ automatically hide section
```

Prohibited.

---

# 119. Automation Anti-Pattern VOP-AP-04

```text
AI detects "professional"
→ adds achievement
```

Prohibited.

---

# 120. Automation Anti-Pattern VOP-AP-05

```text
photo seems old
→ archive automatically
```

Prohibited.

---

# 121. AI Anti-Pattern VOP-AP-06

A universal AI process gets unrestricted access to:

- private Castings;
- Contacts;
- Feedback;
- all media;

without task-specific need.

Prohibited by least-data principle.

---

# 122. Dashboard Anti-Pattern VOP-AP-07

Dashboard dominated by vanity statistics instead of actions.

---

# 123. Dashboard Anti-Pattern VOP-AP-08

Alert has no direct path to affected entity.

---

# 124. Dashboard Anti-Pattern VOP-AP-09

System reports:

```text
AI confidence 0.74
```

without explaining:

- what was analysed;
- what is being proposed.

---

# 125. Operator Trust Signals

Admin trust increases when system provides:

```text
Clear reason
Source reference
Affected entity
Affected journey
Suggested action
Reversibility
Audit Trail
```

---

# 126. Operator Trust Signal — No Hidden Actions

Any automatic action beyond trivial technical processing MUST be visible in history.

---

# 127. Audit Requirements

Significant actions SHOULD be recorded:

```text
Observation created
Suggestion generated
Suggestion approved
Suggestion rejected
Automatic action executed
Action failed
Exception resolved
```

---

# 128. Technical Background Work vs Audit

Not every trivial cache hit requires business Audit Event.

Audit focuses on meaningful operational/business changes.

Technical observability is handled separately.

---

# 129. Operator State Model

Conceptually an actionable item MAY have states:

```text
Open
Acknowledged
Approved
Rejected
Deferred
Resolved
Dismissed
Superseded
```

Exact model is defined in architecture.

---

# 130. Superseded State

Если новая информация делает suggestion obsolete:

```text
old suggestion
→ Superseded
```

а не применяется к уже изменившемуся entity.

---

# 131. Direct Resolution

Некоторые exceptions закрываются автоматически после исправления source.

Например:

```text
broken link
→ Admin fixes URL
→ validation succeeds
→ exception resolved
```

---

# 132. Manual Resolution

Admin MAY explicitly mark issue reviewed where automatic proof невозможен.

---

# 133. Metrics — Manual Work Reduction

Ключевой KPI:

```text
manual routine actions avoided
```

Нужно интерпретировать осторожно; цель не в максимизации automation любой ценой.

---

# 134. Metrics — Exception Resolution Time

```text
critical exception detected
→ resolved
```

Особенно для:

- Casting deadline;
- broken Video;
- public Contact problem.

---

# 135. Metrics — Suggestion Acceptance Rate

Может показывать качество recommendation.

Но низкий acceptance rate не должен автоматически заставлять AI менять business policy.

---

# 136. Metrics — False Positive Rate

Высокий false-positive rate снижает доверие и должен рассматриваться как серьёзная проблема.

---

# 137. Metrics — Alert Noise

Полезно отслеживать:

```text
actions requiring user attention
/
total alerts
```

Цель — высокая actionable density.

---

# 138. Metrics — Automated Recovery

Количество безопасных технических проблем, устранённых без участия Admin.

Например:

- derivative retry;
- cache rebuild;
- search reindex.

---

# 139. Metrics — Missed Critical Exception

Критически важная метрика качества:

> был ли значимый operational problem, который система должна была обнаружить, но не обнаружила.

---

# 140. Analytics Boundary

Operator MAY использовать aggregate analytics для recommendations.

Он не должен создавать ненужные персональные профили посетителей.

---

# 141. Privacy Boundary

Private data могут использоваться Operator только для конкретной authorized внутренней задачи.

Они не переходят автоматически в:

- Public Projection;
- external AI;
- external notification.

---

# 142. AI Provider Boundary

Если для отдельной Operator-функции используется внешний AI provider, передаваемые данные должны быть минимизированы.

Детально определяется AI/Security documentation.

---

# 143. Sensitive Data

Особое внимание:

- Guardian Contacts;
- Feedback sender details;
- Casting attachments;
- private notes;
- unpublished media.

---

# 144. Operator and Access Control

Operator не должен иметь концептуального:

```text
superuser bypass everything
```

Автоматизация должна соблюдать domain security constraints.

---

# 145. Manual Fallback

Для каждого AI-assisted operator action должен существовать ручной workflow, если без него core business operation была бы невозможна.

---

# 146. Operator Availability

Public Site не должен зависеть от работоспособности Virtual Operator.

Если Operator unavailable:

```text
Public Site works
Admin manual workflows work
Data remains intact
```

---

# 147. Core Dependency Principle

Virtual Operator является:

```text
Optimization Layer
```

а не:

```text
Single Point of Business Truth
```

---

# 148. Relevant 6-3-5 Insights

Основные:

```text
INS-VOP-01 Change Once, Propagate Everywhere
INS-VOP-02 Draft Automation
INS-VOP-03 Exception-Driven Administration
```

Также связаны:

```text
INS-CD-02 Search Inside Profile
INS-CP-02 Shooting Date Trust
INS-INT-03 Localized PDF
```

через health/readiness monitoring.

---

# 149. Relevant Product Principles

Ключевые:

```text
PP-001 Single Source of Truth
PP-002 One Input, Multiple Uses
PP-011 Source ≠ Configuration ≠ Derived
PP-024 Human-in-the-loop
PP-025 AI Is an Assistant, Not an Authority
PP-026 Missing Is Better Than Invented
PP-027 Explainable AI
PP-038 Automation by Exception
PP-039 Automate Derived Work
PP-040 Do Not Automate Business Decisions
PP-041 Virtual Operator Manages Attention
PP-042 Virtual Operator Must Be Reversible
PP-079 Silent Overwrite Is Prohibited
PP-080 Failure Must Be Contained
PP-081 Core Must Work Without AI
PP-092 Every Derived Representation Needs Invalidation Rules
PP-094 Every AI Capability Needs a Data Boundary
```

---

# 150. Relevant Business Rule Areas

Virtual Operator должен соблюдать правила областей:

```text
BR-GEN
BR-MED
BR-POR
BR-EMO
BR-GRD
BR-QST
BR-PQB
BR-CST
BR-FDB
BR-NOT
BR-VOP
BR-I18N
BR-SEC
BR-AUDIT
BR-CONCURRENCY
```

Точная traceability будет сформирована в `traceability.md`.

---

# 151. Future User Flows

Из этого Journey должны быть созданы минимум:

```text
UF-ADM-VOP-001 Open Exception Dashboard
UF-ADM-VOP-002 Review Observation
UF-ADM-VOP-003 Approve Suggestion
UF-ADM-VOP-004 Modify Suggestion
UF-ADM-VOP-005 Reject Suggestion
UF-ADM-VOP-006 Defer Suggestion
UF-ADM-VOP-007 Resolve Broken Link
UF-ADM-VOP-008 Review New Media Classification
UF-ADM-VOP-009 Review Stale Questionnaire
UF-ADM-VOP-010 Handle Casting Deadline
UF-ADM-VOP-011 Review Localization Gap
UF-ADM-VOP-012 Review QR Validation Error
UF-ADM-VOP-013 Review Failed Notification
```

---

# 152. Functional Requirement Areas

Будущие требования потребуются в:

```text
FR-VOP
FR-DASHBOARD
FR-READINESS
FR-MEDIA
FR-DEPENDENCY
FR-INVALIDATION
FR-JOBS
FR-CASTING
FR-NOTIFICATION
FR-QR
FR-I18N
FR-AUDIT
FR-AI
```

---

# 153. E2E-CJM-VOP-001 — New Media

```text
Given
new media assets are uploaded

When
Virtual Operator processes them

Then
technical metadata may be extracted automatically

And
professional classifications are proposed

But
no professional category is published without required human decision
```

---

# 154. E2E-CJM-VOP-002 — Broken Showreel

```text
Given
the primary Showreel URL repeatedly fails validation

Then
one actionable exception is created

And
it links to the affected media

And
the system does not automatically delete the Showreel
```

---

# 155. E2E-CJM-VOP-003 — Dependency Invalidation

```text
Given
Primary Close-Up changes

Then
dynamic dependent projections are invalidated/rebuilt as defined

And
historical immutable Questionnaire revisions remain unchanged
```

---

# 156. E2E-CJM-VOP-004 — Emotional Grid

Operator may propose crop/layout.

Final grid cannot become Published until required human confirmation is complete.

---

# 157. E2E-CJM-VOP-005 — Deadline

Upcoming critical Casting deadline creates a higher-priority exception than optional low-value content warning.

---

# 158. E2E-CJM-VOP-006 — Safe Automation Failure

If derivative regeneration fails:

- Original remains intact;
- previous valid derivative is not destroyed where applicable;
- exception becomes visible.

---

# 159. E2E-CJM-VOP-007 — Stale Suggestion

```text
Given
Operator generated suggestion for entity version 5

And
Admin manually updates entity to version 6

When
old suggestion is approved

Then
system must revalidate and must not silently overwrite version 6
```

---

# 160. E2E-CJM-VOP-008 — QR Safety

Operator must flag/deny new output when configured QR points to an admin/private URL.

---

# 161. E2E-CJM-VOP-009 — Notification Failure

Feedback remains persisted while notification exception is surfaced independently.

---

# 162. E2E-CJM-VOP-010 — Core Without Operator

When Operator/AI service is unavailable:

- Profile works;
- Public Site works;
- manual Admin editing works;
- Casting data remains accessible.

---

# 163. Journey Score Model

Для Virtual Operator используется адаптированная оценка:

| Критерий | Target |
|---|---:|
| Exception Detection | 2 |
| Prioritization | 2 |
| Explainability | 2 |
| Safe Automation | 2 |
| Human Resolution Efficiency | 2 |

Target:

```text
10/10
```

Минимум:

```text
9/10
```

---

# 164. Hard Failure Conditions

Virtual Operator требует redesign, если:

1. автоматически меняет professional facts без approval;
2. автоматически публикует content;
3. скрывает источник AI conclusion;
4. генерирует слишком много duplicate alerts;
5. не различает blocking issue и warning;
6. не показывает affected entity;
7. не показывает recommended action;
8. удаляет content по возрасту;
9. изменяет внешность в Emotional Grid;
10. перезаписывает более новую Admin change;
11. становится обязательным для работы Public Site;
12. notification failure смешивается с loss of Feedback;
13. historical immutable snapshot автоматически обновляется;
14. private data передаются внешнему AI без task-specific justification;
15. broken QR может попасть в новый public PDF без warning/blocking policy.

---

# 165. Operator Priority Layers

## P0 — Immediate Professional Risk

- New Casting/Role inquiry;
- near deadline;
- Public Profile unavailable;
- Critical Contact failure.

## P1 — Major Journey Failure

- Primary Video broken;
- Primary Questionnaire unavailable;
- Primary Portfolio item missing;
- Public Builder failure.

## P2 — Content Integrity

- missing metadata;
- stale derivative;
- missing localization;
- unclassified media.

## P3 — Optimization

- template recommendations;
- analytics patterns;
- optional content freshness review.

---

# 166. Dashboard Sorting Principle

Default sort SHOULD emphasize:

```text
Urgency
+
Professional Journey Impact
```

не просто date created.

---

# 167. Operator Feedback Loop

```text
Detect
 ↓
Suggest
 ↓
Human Decision
 ↓
Outcome
 ↓
Quality Evaluation
```

Operator MAY use aggregate result to improve recommendation quality where the architecture permits.

---

# 168. No Self-Modifying Business Rules

Даже если Operator часто получает rejection, он MUST NOT самостоятельно менять Business Rules.

---

# 169. Product Decision Boundary

Если recurring operator findings показывают systemic issue:

```text
Repeated Observation
 ↓
Product Recommendation
 ↓
Human Product Decision
 ↓
Documentation Change
 ↓
Implementation
```

---

# 170. Example — Repeated Builder Pattern

```text
Users repeatedly add Languages to Quick Template
```

Operator MAY recommend:

```text
Review Quick Template default.
```

Он не меняет template самостоятельно.

---

# 171. Example — Repeated Missing Metadata

Если Admin регулярно забывает Shooting Date, продуктовая рекомендация может быть:

```text
make Shooting Date more prominent / required earlier in flow
```

Это уже UX/Product improvement, а не автоматический data change.

---

# 172. Operator and Documentation

Virtual Operator rules должны быть отражены в:

- Domain Model;
- State Machines;
- Background Processing;
- Notifications;
- AI Boundaries;
- Audit;
- Security;
- Module Specification;
- Testing.

---

# 173. Operator and Observability

Необходимо различать:

## Product Exception

Требует Admin/business action.

## Technical Incident

Требует infrastructure/operations response.

Пример:

```text
Showreel URL broken
→ Product Exception

Database unavailable
→ Technical Incident
```

Некоторые technical incidents могут отображаться Admin, но это отдельная модель.

---

# 174. Operator and Health Checks

Operator MAY использовать health state сервисов:

- PDF;
- AI;
- storage;
- notifications;

для корректной интерпретации failed actions.

Например:

```text
PDF generation failed
because PDF service unavailable
```

лучше, чем generic:

```text
Unknown error
```

---

# 175. Operator and Background Processing

Virtual Operator логически зависит от background-processing capability для части задач, но Customer Journey не диктует конкретный queue technology.

---

# 176. Operator and Idempotency

Повторная обработка одного события не должна создавать:

- duplicate derived files;
- duplicate alerts;
- duplicate notifications;
- duplicate business entities.

---

# 177. Operator and Concurrency

Перед применением suggestion необходимо проверять актуальное состояние entity.

---

# 178. Operator and Archive

Archived content обычно исключается из обычного high-priority monitoring, если только:

- historical link broken;
- regulatory/security reason;
- archived content всё ещё используется immutable public snapshot.

---

# 179. Operator and Deletion

Перед destructive operation система должна показывать dependencies.

Virtual Operator MAY обнаружить unused item, но не должен автоматически удалять его.

---

# 180. Operator and Public Search

Если structured fact изменён:

```text
Skill update
 ↓
Search Projection invalidated
 ↓
reindex
```

может выполняться автоматически.

---

# 181. Operator and Contacts

Особенно важны:

- invalid public phone/link formatting;
- broken social URL;
- visibility conflicts;
- Contact used by Questionnaire but no longer allowed.

---

# 182. Contact Visibility Change

```text
Contact public-builder permission disabled
 ↓
new Builder sessions cannot select it
 ↓
existing temporary sessions revalidate at generation
```

Operator/invalidation system должен обеспечить consistency.

---

# 183. Operator and Prepared Questionnaires

Published historical Questionnaire остаётся immutable.

Но:

- Primary designation;
- current Draft;
- future generation;

должны учитывать current valid data.

---

# 184. Operator and Public Builder Sessions

Temporary sessions:

- expire;
- do not require permanent Admin cleanup one by one;
- may be handled through automated retention.

---

# 185. Operator and Retention

Safe cleanup MAY быть автоматизирован для explicitly temporary derived/session data согласно retention policy.

Это отличается от удаления professional Master Data.

---

# 186. Operator and BB Drafts

Old/discarded AI Draft retention определяется отдельной policy.

Operator MAY напоминать о stale draft, но не публикует его.

---

# 187. Operator and Social Publishing

Для существующей social publishing capability Operator MAY обнаруживать:

- expired OAuth;
- failed publish;
- missing required media.

Но не должен публиковать social content автоматически без утверждённого workflow.

---

# 188. Operator and Help/Tickets

Если существующая Help Center/Ticket функциональность сохраняется, operational error MAY предложить переход к support flow.

Но Help Center не является core operator datastore.

---

# 189. Exception-Driven Administration Definition

Система соответствует этому принципу, если Admin может в нормальном режиме начать рабочий день с Dashboard и получить большинство реально необходимых действий без обхода всех модулей.

---

# 190. Final Mental Model

```text
DON'T MAKE ME CHECK EVERYTHING
 ↓
WATCH THE SYSTEM
 ↓
FIND WHAT MATTERS
 ↓
EXPLAIN WHY
 ↓
PREPARE THE SAFE PART
 ↓
ASK ME ONLY FOR THE DECISION
 ↓
VERIFY THE RESULT
```

---

# 191. Product Response

```text
WATCH
→ Domain Events + Scheduled Validation

FIND
→ Observations + Readiness + Health

PRIORITIZE
→ Exception Ranking

PREPARE
→ Safe Automation + Draft Suggestions

DECIDE
→ Human Approval

VERIFY
→ Revalidation + Audit
```

---

# 192. Definition of Journey Compliance

Virtual Operator соответствует `CJM-VOP-001`, если:

1. автоматически обнаруживает значимые изменения и проблемы;
2. отличает technical fact от AI suggestion;
3. умеет определять dependency impact;
4. безопасно автоматизирует derived work;
5. группирует и приоритизирует exceptions;
6. не создаёт professional facts самостоятельно;
7. не публикует professional content самостоятельно;
8. сохраняет human approval для business meaning;
9. не изменяет внешность;
10. не уничтожает последний valid result при failed rebuild;
11. работает с audit/history;
12. учитывает concurrency;
13. не становится single point of failure;
14. отделён от BB Assistant, Casting AI и Theme AI;
15. позволяет Admin работать преимущественно по модели exception-driven administration.

---

# 193. Итоговая схема

```text
                  VIRTUAL PORTFOLIO OPERATOR

                         DOMAIN EVENTS
                              ↓
                         OBSERVATION
                              ↓
                         VALIDATION
                              ↓
                   ┌──────────┴──────────┐
                   │                     │
               NORMAL                EXCEPTION
                   │                     │
          Safe Automation          PRIORITIZATION
                   │                     │
                VERIFY              SUGGESTION
                                         │
                                   HUMAN REVIEW
                                  /      |      \
                             Approve   Modify   Reject
                                │         │        │
                                └────┬────┘        │
                                     ↓             │
                                   APPLY           │
                                     ↓             │
                                  VERIFY ←─────────┘
                                     ↓
                            RECALCULATE STATE
                                     ↓
                                 RESOLVED
```

---

# 194. Финальный принцип

> **Virtual Portfolio Operator должен не заменять администратора, а системно избавлять его от необходимости искать работу вручную. Система сама наблюдает за профилем, зависимостями, публикациями, ссылками, производными материалами и сроками; безопасную техническую рутину выполняет автоматически, спорные действия готовит в виде объяснимых предложений, а профессионально значимые решения всегда оставляет человеку.**