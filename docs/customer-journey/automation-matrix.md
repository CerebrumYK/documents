# AUTOMATION MATRIX

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Матрица автоматизации, подтверждения и человеческого контроля

**Целевой файл:** `docs/customer-journey/automation-matrix.md`  
**Документ:** DOC-030  
**Статус:** ✅ Completed  
**Тип:** Customer Journey / Automation Governance

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
- `docs/customer-journey/virtual-operator.md`
- `docs/customer-journey/conversion-funnel.md`

---

# 1. Назначение документа

Настоящий документ определяет нормативную матрицу автоматизации целевого продукта.

Он отвечает на вопросы:

1. Какие действия система выполняет автоматически.
2. Какие действия система может только предложить.
3. Какие действия требуют явного подтверждения человека.
4. Какие действия запрещено выполнять автоматически.
5. Какие действия могут быть выполнены AI.
6. Какие действия должны иметь deterministic/manual fallback.
7. Какие действия должны быть обратимыми.
8. Какие автоматические процессы являются критичными.
9. Какие сбои не должны блокировать основной продукт.
10. Где должен существовать Audit Trail.
11. Как различаются Virtual Operator, Casting AI, BB Assistant и Theme AI.
12. Как автоматизация взаимодействует с Customer Journey.

---

# 2. Главный принцип автоматизации

Автоматизируется:

```text
рутина
+
производные операции
+
проверки
+
техническое обслуживание
```

Не автоматизируется без человека:

```text
профессиональный факт
+
профессиональное решение
+
публикация
+
коммуникационное обязательство
+
изменение внешности
```

---

# 3. Основная модель

```text
CAN SYSTEM DO IT SAFELY
WITHOUT CHANGING PROFESSIONAL MEANING?
             │
       ┌─────┴─────┐
      YES           NO
       │             │
       ▼             ▼
SAFE AUTOMATION   CAN SYSTEM
                  PREPARE IT?
                     │
                ┌────┴────┐
               YES        NO
                │          │
                ▼          ▼
             SUGGEST     HUMAN ONLY
                │
                ▼
           HUMAN REVIEW
                │
       ┌────────┼────────┐
       ▼        ▼        ▼
    Approve   Modify   Reject
```

---

# 4. Automation Levels

Фиксируются пять уровней:

```text
AUTO-0 — Human Only
AUTO-1 — System Observation
AUTO-2 — System Suggestion
AUTO-3 — Human-Confirmed Automation
AUTO-4 — Safe Automatic Execution
```

---

# 5. AUTO-0 — Human Only

Система не должна самостоятельно принимать решение.

Примеры:

- подтвердить профессиональный факт;
- принять Role Offer;
- принять Casting;
- опубликовать Theme;
- выбрать Primary professional photo;
- принять спорную classification;
- отправить обязательство от имени актрисы.

---

# 6. AUTO-1 — System Observation

Система может только сообщить факт.

Примеры:

```text
URL validation failed
Shooting Date missing
PDF stale
Casting deadline approaching
```

Система не предлагает или не применяет business change автоматически.

---

# 7. AUTO-2 — System Suggestion

Система:

- анализирует;
- формирует recommendation;
- показывает основания.

Но ничего не применяет.

Примеры:

- probable Full Body;
- probable duplicate;
- suggested crop;
- suggested Casting Requirement;
- suggested text.

---

# 8. AUTO-3 — Human-Confirmed Automation

Система готовит изменение.

Человек явно подтверждает.

После подтверждения система может выполнить техническую часть.

Примеры:

```text
Confirm crop
→ generate Emotional Grid

Approve AI Draft
→ insert into editor

Approve photo category
→ create Portfolio relation
```

---

# 9. AUTO-4 — Safe Automatic Execution

Автоматическая операция разрешена, если она:

1. не меняет professional meaning;
2. не раскрывает private data;
3. имеет idempotent behaviour where required;
4. не уничтожает единственную valid copy;
5. имеет failure handling.

Примеры:

- thumbnail generation;
- web derivative;
- cache invalidation;
- search reindex;
- deterministic validation;
- preview regeneration.

---

# 10. Automation Decision Test

Для любой новой автоматизации необходимо ответить:

### Q1

Меняется ли professional fact?

Если да → минимум `AUTO-3`, часто `AUTO-0`.

### Q2

Меняется ли Public state?

Если да → human confirmation.

### Q3

Создаётся ли professional commitment?

Если да → Human Only.

### Q4

Можно ли точно определить результат deterministic rules?

Если да → возможен AUTO-4.

### Q5

Есть ли semantic uncertainty?

Если да → Suggestion + Human Review.

### Q6

Есть ли безопасный rollback?

Если нет → уровень автоматизации понижается.

---

# 11. Классы автоматизации

Матрица разделяется на:

```text
AM-01 Media
AM-02 Portfolio
AM-03 Emotional Portfolio
AM-04 Projects / Roles
AM-05 Training
AM-06 Skills / Languages
AM-07 Links / QR
AM-08 Contacts
AM-09 Questionnaires
AM-10 Public Builder
AM-11 Castings
AM-12 Feedback
AM-13 Notifications
AM-14 Opportunity Pipeline
AM-15 BB Assistant
AM-16 Theme AI
AM-17 Virtual Operator
AM-18 Analytics
AM-19 Localization
AM-20 Security / Audit
```

---

# 12. AM-01 — Media Upload

## Upload file

**Level:** `AUTO-3 / Human initiated`

Пользователь явно выбирает файл.

Система автоматически выполняет техническое сохранение.

---

# 13. AM-01 — Technical Metadata

Примеры:

- dimensions;
- MIME;
- file size;
- checksum;
- orientation.

**Level:** `AUTO-4`

Причина:

не меняет professional meaning.

---

# 14. AM-01 — Thumbnail Generation

**Level:** `AUTO-4`

Условия:

- Original immutable;
- failure does not destroy source;
- retry/idempotency.

---

# 15. AM-01 — Web Derivative

**Level:** `AUTO-4`

Разрешены:

- resize;
- compression;
- format conversion;
- orientation normalization if source semantics preserved.

---

# 16. AM-01 — Duplicate Detection

## Exact duplicate

**Level:** `AUTO-1/AUTO-2`

Система может сообщить:

```text
Exact duplicate detected.
```

## Visual probable duplicate

**Level:** `AUTO-2`

Нельзя автоматически удалять.

---

# 17. AM-01 — Media Deletion

**Level:** `AUTO-0 / Human Only`

Система должна сначала показать dependencies.

---

# 18. AM-02 — Portfolio Classification

Пример:

```text
Close-Up
Full Body
Profile
Three Quarter
```

Если classification предложена AI/vision:

**Level:** `AUTO-2`

---

# 19. AM-02 — Portfolio Classification Apply

После Human confirmation:

**Level:** `AUTO-3`

---

# 20. AM-02 — Primary Close-Up Selection

**Level:** `AUTO-0 / Human decision`

Система MAY предложить candidates.

Но Primary selection — профессиональная presentation decision.

---

# 21. AM-02 — Primary Full Body Selection

Та же модель:

```text
Suggestion → Human selection
```

---

# 22. AM-02 — Public Portfolio Rendering

После опубликованной configuration:

**Level:** `AUTO-4`

---

# 23. AM-03 — Emotional Portfolio Candidate Detection

Система MAY предложить фотографии из одной фотосессии.

**Level:** `AUTO-2`

---

# 24. AM-03 — Shooting Date Extraction

Если дата точно присутствует в authoritative source metadata:

MAY быть предложена.

**Level:** `AUTO-2`

Не следует считать file modification date фактической Shooting Date без rule.

---

# 25. AM-03 — Emotional Grid Cell Selection

AI MAY предложить source photos.

**Level:** `AUTO-2`

---

# 26. AM-03 — Face Region Detection

Technical vision algorithm MAY определить probable face bounding region.

**Level:** `AUTO-4` как computational helper.

Но результат не является professional crop decision.

---

# 27. AM-03 — Crop Proposal

**Level:** `AUTO-2`

---

# 28. AM-03 — Crop Adjustment

Пользователь может изменить:

- pan;
- zoom;
- rotation where allowed.

**Level:** Human interaction.

---

# 29. AM-03 — Grid Finalization

Все клетки должны быть подтверждены.

**Level:** `AUTO-3`

```text
Human confirms
→ system generates derivative
```

---

# 30. AM-03 — Grid Publication

**Level:** Human-controlled publication.

Не AUTO-4.

---

# 31. AM-03 — Appearance Modification

Например:

- expression synthesis;
- face replacement;
- generative fill affecting face/body;
- age changes.

**Level:** PROHIBITED.

---

# 32. AM-04 — Project Creation

Professional Project — business fact.

**Level:** Human-controlled.

AI MAY prepare draft from reliable source, but cannot create confirmed Project autonomously.

---

# 33. AM-04 — Project Type Suggestion

**Level:** `AUTO-2`

---

# 34. AM-04 — Role Extraction

Если source text states Role explicitly:

Casting/import assistant MAY extract.

Но:

```text
AI Extraction ≠ Source Fact confirmation
```

**Level:** `AUTO-2/AUTO-3`

Human confirms before Master Data.

---

# 35. AM-04 — Featured Project Suggestion

Analytics/AI MAY recommend.

**Level:** `AUTO-2`

Featured state requires Human decision.

---

# 36. AM-04 — Project Ordering

Curated ordering:

**Level:** Human.

Rendering of saved order:

**Level:** AUTO-4.

---

# 37. AM-05 — Training Migration Suggestion

Legacy `Project / Other` MAY be suggested as Training.

**Level:** `AUTO-2`

---

# 38. AM-05 — Training Migration

Ambiguous record:

**Level:** Human confirmation required.

Unambiguous migration after approved mapping rules MAY run automatically as a migration process with audit.

---

# 39. AM-06 — Skill Creation

Professional Skill:

**Level:** Human-controlled.

AI MAY suggest based on explicit source.

---

# 40. AM-06 — Skill Level

Must not be inferred from casual textual mention.

**Level:** Human-confirmed fact.

---

# 41. AM-06 — Language Level

Example:

```text
English B2
```

If explicitly present in trusted source, migration/import MAY prefill as suggestion.

Final professional Master Data must follow approved import rules.

---

# 42. AM-06 — CEFR Conversion

If mapping is deterministic from authoritative stored value:

**Level:** AUTO-4.

If source says:

```text
good English
```

AI must not silently map to `B2`.

---

# 43. AM-07 — External Link Validation

**Level:** AUTO-4.

Includes:

- syntax;
- protocol;
- reachability where feasible;
- public safety rules.

---

# 44. AM-07 — Broken Link Detection

**Level:** AUTO-1.

---

# 45. AM-07 — Link Auto-Removal

**PROHIBITED** as default behaviour.

---

# 46. AM-07 — QR Generation

For approved canonical Public URL:

**Level:** AUTO-4.

---

# 47. AM-07 — QR Decode Validation

After generation:

```text
encode
→ decode
→ exact target equality
```

**Level:** AUTO-4.

---

# 48. AM-07 — QR Publication

QR is derived from approved link.

If enabled in Questionnaire configuration, it can render automatically.

However configuration to expose a link/QR remains human-controlled.

---

# 49. AM-07 — Unsafe QR Target

If target is:

- admin;
- private;
- invalid;
- unauthorized;

system MUST block new public output or apply explicitly documented safe policy.

No silent bypass.

---

# 50. AM-08 — Contact Formatting

Normalization/display formatting MAY be automatic where deterministic.

**Level:** AUTO-4.

---

# 51. AM-08 — Contact Visibility

Flags such as:

```text
show_on_public_site
allow_in_questionnaire
allow_in_public_builder
```

**Level:** Human-controlled.

---

# 52. AM-08 — Contact Visibility Recommendation

System MAY warn:

```text
Primary questionnaire has no permitted contact.
```

**Level:** AUTO-1/AUTO-2.

---

# 53. AM-08 — Guardian/Parent Contact Exposure

Never inferred from relation alone.

Explicit Visibility required.

---

# 54. AM-09 — Questionnaire Draft Recalculation

Dynamic Draft/Preview based on current configuration:

**Level:** AUTO-4.

---

# 55. AM-09 — Published Revision Mutation

PROHIBITED.

Published revisions immutable.

---

# 56. AM-09 — Questionnaire Readiness

**Level:** AUTO-4.

Checks MAY include:

- required photos;
- valid links;
- permitted contacts;
- required data;
- QR validity.

---

# 57. AM-09 — Questionnaire Publication

**Level:** Human-controlled.

---

# 58. AM-09 — Primary Questionnaire Selection

**Level:** Human-controlled.

Only one active Primary where required.

---

# 59. AM-09 — PDF Generation

From approved revision/configuration:

**Level:** AUTO-4.

---

# 60. AM-09 — PDF Regeneration

Draft/current derived PDF:

**Level:** AUTO-4.

Historical immutable PDF:

must remain associated with its Revision.

---

# 61. AM-09 — Download Picker

Available versions determined automatically from Published/Allowed state.

**Level:** AUTO-4.

---

# 62. AM-10 — Public Builder Session

Session creation from allowed Template:

**Level:** AUTO-4 after explicit user action.

---

# 63. AM-10 — Template Defaults

Configured by Admin/Product owner.

Application to new session:

**Level:** AUTO-4.

---

# 64. AM-10 — Item Authorization

Every item:

```text
belongs to profile
AND
is allowed
AND
is available
```

**Level:** AUTO-4 server-side.

---

# 65. AM-10 — Public Builder PDF

After user selection and server validation:

**Level:** AUTO-4.

---

# 66. AM-10 — Expired Session Cleanup

Temporary session retention cleanup:

**Level:** AUTO-4 according to retention policy.

---

# 67. AM-10 — Public Selection Changes Master Data

PROHIBITED.

---

# 68. AM-10 — Builder Recommendation

System MAY recommend:

```text
Standard template may better fit your selected content.
```

If added later:

**Level:** AUTO-2.

No forced change.

---

# 69. AM-11 — Casting Source Ingestion

User/Admin provides:

- text;
- files;
- images.

Storage:

**Level:** Human initiated + safe automated processing.

---

# 70. AM-11 — Casting Requirement Extraction

Casting AI MAY extract:

```text
age range
language
skill
deadline
role
location
```

only if present in source.

**Level:** AUTO-2.

---

# 71. AM-11 — Missing Requirement Handling

If absent:

```text
Не указано
```

Automatically.

**Level:** AUTO-4 deterministic output from absence.

No invented value.

---

# 72. AM-11 — Profile Match

System MAY calculate match evidence from confirmed Profile facts.

**Level:** AUTO-2.

---

# 73. AM-11 — Casting Recommendation

Examples:

```text
Matches English requirement.
No published evidence for horse riding.
```

**Level:** AUTO-2.

---

# 74. AM-11 — Casting Qualification Decision

**Level:** Human Only.

AI does not set business outcome automatically.

---

# 75. AM-11 — Casting Questionnaire Draft

On explicit command:

```text
source requirements
+
confirmed Profile facts
→ Draft
```

**Level:** AUTO-2/AUTO-3.

Human reviews before publication/use.

---

# 76. AM-11 — Casting Application Submission

Automatic submission to third-party casting systems:

**OUT OF SCOPE / PROHIBITED in current scope.**

---

# 77. AM-12 — Feedback Persistence

After valid user submit:

**Level:** AUTO-4 business transaction.

Must happen before notification.

---

# 78. AM-12 — Feedback Type Initial Classification

If user selected CTA type:

```text
Invite to Casting
Request Materials
Offer Role
```

system may assign matching Feedback type deterministically.

**Level:** AUTO-4.

---

# 79. AM-12 — AI Reclassification

AI MAY suggest another type.

**Level:** AUTO-2.

---

# 80. AM-12 — Feedback → Casting

**Level:** Human-controlled.

System MAY prepare mapping.

---

# 81. AM-12 — Attachment Processing

Safe operations:

- MIME validation;
- size check;
- quarantine/storage;
- malware controls if implemented.

**Level:** AUTO-4.

Semantic interpretation:

AUTO-2 if AI-assisted.

---

# 82. AM-13 — Internal Notification

After Feedback persistence:

**Level:** AUTO-4.

---

# 83. AM-13 — WhatsApp Notification

According to configured approved channel:

**Level:** AUTO-4.

But only notification, not professional reply.

---

# 84. AM-13 — Notification Retry

**Level:** AUTO-4.

Requirements:

- bounded;
- idempotent;
- observable.

---

# 85. AM-13 — Quiet Hours

Delivery delay/schedule according to configured quiet-hours rules:

**Level:** AUTO-4.

---

# 86. AM-13 — Notification Content

Minimal template generated deterministically:

**Level:** AUTO-4.

AI-written outbound notification is unnecessary by default.

---

# 87. AM-13 — Professional Reply

**Level:** Human-controlled.

BB Assistant MAY draft.

---

# 88. AM-14 — Opportunity Stage Suggestion

System MAY suggest next stage based on explicit event.

Example:

```text
self-tape request recorded
→ suggest Self-Tape Requested
```

**Level:** AUTO-2/AUTO-3 depending on authoritative workflow.

---

# 89. AM-14 — Opportunity Stage Change

For professional business stages:

**Level:** Human-controlled or authoritative deterministic transaction.

Must never be inferred from page analytics alone.

---

# 90. AM-14 — Booked

`Booked` is an authoritative business fact.

**Level:** Human / authoritative business action only.

---

# 91. AM-14 — Auto-close by inactivity

Not allowed unless later explicitly defined by business policy.

Current default:

**PROHIBITED.**

---

# 92. AM-15 — BB Assistant Generation

BB Assistant MAY create Draft for:

- Biography;
- Project description;
- Training description;
- Cover Letter;
- Casting Response;
- Role Response;
- Questionnaire Text;
- Social Caption;
- Custom text.

**Level:** AUTO-2.

---

# 93. AM-15 — BB Assistant Source Context

System may automatically gather permitted current Profile facts required by task.

**Level:** AUTO-4 as context assembly.

---

# 94. AM-15 — BB Assistant Fact Invention

PROHIBITED.

---

# 95. AM-15 — Apply Draft

Human explicitly selects Apply.

**Level:** AUTO-3.

Apply means:

```text
insert into editable content
```

not Publish.

---

# 96. AM-15 — Publish AI Draft

**Level:** Human publication.

Never automatic.

---

# 97. AM-15 — Send AI Reply

AI MAY draft.

Sending remains Human-controlled.

---

# 98. AM-16 — Theme Prompt Interpretation

Theme AI MAY transform prompt into structured Theme Proposal.

**Level:** AUTO-2.

---

# 99. AM-16 — Theme Tokens Draft

AI MAY suggest:

- colors;
- typography settings;
- spacing;
- hero treatment;
- decorative rules.

**Level:** AUTO-2.

---

# 100. AM-16 — Accessibility Validation

Contrast/readability checks:

**Level:** AUTO-4.

---

# 101. AM-16 — Theme Preview

Automatic generation:

**Level:** AUTO-4.

---

# 102. AM-16 — Theme Publish

**Level:** Human-controlled.

---

# 103. AM-16 — Temporary Theme Activation

Requires explicit human action and duration/configuration.

Not autonomous AI decision.

---

# 104. AM-16 — Theme Content Mutation

PROHIBITED.

Theme may not rewrite:

- Biography;
- Contact;
- Questionnaire;
- professional facts.

---

# 105. AM-17 — Virtual Operator Observation

**Level:** AUTO-1.

---

# 106. AM-17 — Virtual Operator Suggestion

**Level:** AUTO-2.

---

# 107. AM-17 — Safe Derived Action

Examples:

- preview rebuild;
- reindex;
- derivative retry.

**Level:** AUTO-4.

---

# 108. AM-17 — Professional Semantic Change

Requires:

```text
Suggestion
→ Human confirmation
```

---

# 109. AM-17 — Alert Grouping

**Level:** AUTO-4.

Duplicate/related observations SHOULD be aggregated.

---

# 110. AM-17 — Priority Calculation

System MAY calculate operational priority.

**Level:** AUTO-4/AUTO-2 depending on model.

It must not itself change business stage.

---

# 111. AM-17 — Stale Suggestion Protection

Before applying a previously approved suggestion, current entity version MUST be checked.

**Level:** AUTO-4 safety rule.

---

# 112. AM-18 — Analytics Event Collection

For approved event schema:

**Level:** AUTO-4.

Subject to Privacy.

---

# 113. AM-18 — Conversion Calculation

Deterministic aggregate calculation:

**Level:** AUTO-4.

---

# 114. AM-18 — Product Recommendation

Example:

```text
Users often remove Training from Compact template.
```

**Level:** AUTO-2.

---

# 115. AM-18 — Automatic UX Mutation

PROHIBITED.

---

# 116. AM-18 — Automatic Featured Project by Views

PROHIBITED by default.

Analytics MAY suggest.

---

# 117. AM-18 — Automatic Content Deletion

PROHIBITED.

---

# 118. AM-19 — Interface Localization

Rendering stored approved translations:

**Level:** AUTO-4.

---

# 119. AM-19 — Machine Translation Draft

AI MAY prepare Draft.

**Level:** AUTO-2.

---

# 120. AM-19 — Translation Publication

Professional/critical translation:

**Level:** Human-reviewed.

---

# 121. AM-19 — Deterministic Localized Labels

Canonical enum labels:

```text
Close-Up
Full Body
Project
Training
```

may render automatically from approved dictionaries.

**Level:** AUTO-4.

---

# 122. AM-19 — Name Transliteration

If approved international name exists, use it.

System MUST NOT create alternate professional spelling and silently publish it.

---

# 123. AM-20 — Audit Event

Significant professional/admin state change:

**Level:** AUTO-4.

Audit recording occurs automatically as part of transaction.

---

# 124. AM-20 — Authorization Check

Every protected action:

**Level:** AUTO-4.

Cannot be disabled by AI.

---

# 125. AM-20 — Optimistic Lock Check

Before concurrent update:

**Level:** AUTO-4.

---

# 126. AM-20 — Auto-Merge Conflicting Professional Data

PROHIBITED unless exact deterministic merge semantics are explicitly defined.

Default:

```text
conflict
→ user review
```

---

# 127. Master Automation Matrix

| Capability | Action | Level | Human Confirmation |
|---|---|---:|---:|
| Media | Metadata extraction | AUTO-4 | No |
| Media | Thumbnail | AUTO-4 | No |
| Media | Probable duplicate | AUTO-2 | Yes for action |
| Portfolio | Category suggestion | AUTO-2 | Yes |
| Portfolio | Primary selection | AUTO-0 | Yes |
| Emotional | Face detection | AUTO-4 helper | Final crop yes |
| Emotional | Crop proposal | AUTO-2 | Yes |
| Emotional | Grid generation | AUTO-3 | Yes |
| Emotional | Appearance generation | Prohibited | — |
| Project | Project draft | AUTO-2 | Yes |
| Role | Role extraction | AUTO-2 | Yes |
| Training | Migration suggestion | AUTO-2 | Yes if ambiguous |
| Links | Validation | AUTO-4 | No |
| Links | Auto-delete broken link | Prohibited | — |
| QR | Generate/decode test | AUTO-4 | No after link approval |
| Contacts | Visibility | AUTO-0 | Yes |
| Questionnaire | Readiness | AUTO-4 | No |
| Questionnaire | PDF rendering | AUTO-4 | No |
| Questionnaire | Publish revision | AUTO-0 | Yes |
| Builder | Permission revalidation | AUTO-4 | No |
| Builder | Generate PDF | AUTO-4 | User-triggered |
| Casting AI | Requirement extraction | AUTO-2 | Yes |
| Casting AI | Match recommendation | AUTO-2 | Yes |
| Casting | Qualification | AUTO-0 | Yes |
| Feedback | Persist | AUTO-4 | User-triggered |
| Feedback | Create Casting | AUTO-3 | Admin confirms |
| Notifications | Internal notification | AUTO-4 | No |
| Notifications | WhatsApp notification | AUTO-4 | No |
| Professional reply | Draft | AUTO-2 | Yes |
| Professional reply | Send | AUTO-0 | Yes |
| Opportunity | Stage update | Human/authoritative | Usually yes |
| Opportunity | Booked | AUTO-0 | Yes |
| BB Assistant | Draft text | AUTO-2 | Yes |
| BB Assistant | Apply draft | AUTO-3 | Yes |
| BB Assistant | Publish/send | AUTO-0 | Yes |
| Theme AI | Proposal | AUTO-2 | Yes |
| Theme | Accessibility validation | AUTO-4 | No |
| Theme | Publish | AUTO-0 | Yes |
| Virtual Operator | Detect issue | AUTO-1 | No |
| Virtual Operator | Recommendation | AUTO-2 | Yes |
| Analytics | Aggregate metrics | AUTO-4 | No |
| Analytics | Recommend change | AUTO-2 | Yes |
| Analytics | Apply UX/content change | Prohibited | — |
| Localization | Render approved strings | AUTO-4 | No |
| Localization | AI translation draft | AUTO-2 | Yes |
| Security | Authorization | AUTO-4 | No |
| Audit | Record state change | AUTO-4 | No |

---

# 128. Human Control Matrix by Consequence

## Low consequence

Примеры:

- thumbnail;
- cache;
- validation.

Допускается AUTO-4.

## Medium consequence

Примеры:

- classification suggestion;
- crop;
- translation draft.

AUTO-2/AUTO-3.

## High consequence

Примеры:

- professional fact;
- public visibility;
- opportunity stage;
- publication.

Human approval.

## Critical consequence

Примеры:

- accepting Offer;
- changing appearance;
- sending professional commitment.

Human only / prohibited automation.

---

# 129. Automation and Publication Boundary

Общий закон:

```text
Generate automatically
Prepare automatically
Validate automatically

BUT

Publish explicitly
```

---

# 130. Exception — Deterministic Derived Public Content

Если Published Source уже разрешает автоматическое создание derived representation:

например responsive image derivative,

это MAY публиковаться технически автоматически, потому что не меняет business meaning.

---

# 131. Public Fact Boundary

Новый Public professional fact никогда не должен появляться исключительно потому, что:

```text
AI predicted it
```

---

# 132. Source Fact Rule

Правильная последовательность:

```text
Source
 ↓
Extraction
 ↓
Human Confirmation when semantic
 ↓
Master Data
 ↓
Public Projection
```

---

# 133. AI Separation Matrix

| AI Capability | Основная роль | Может рекомендовать | Может публиковать |
|---|---|---:|---:|
| Casting AI | Analyze Casting | Yes | No |
| BB Assistant | Write Drafts | Yes | No |
| Theme AI | Visual Proposal | Yes | No |
| Virtual Operator | Detect/Recommend | Yes | No |

---

# 134. AI Common Restrictions

Все AI capabilities MUST NOT:

- invent professional facts;
- bypass permissions;
- silently publish;
- silently send commitments;
- alter protected history;
- expose private data without task need.

---

# 135. AI Fallback Principle

Если AI недоступен:

- manual Profile editing works;
- manual Casting analysis works;
- manual copywriting works;
- manual theme editing works;
- Public Site works.

---

# 136. Deterministic Before AI

Если задача решается надёжным deterministic rule, AI не должен использоваться без причины.

Пример:

```text
Is URL syntactically valid?
```

не требует LLM.

---

# 137. AI for Ambiguity

AI наиболее полезен там, где присутствует:

- unstructured text;
- classification uncertainty;
- writing;
- recommendation;
- semantic extraction.

---

# 138. No AI for Authorization

Authorization определяется только deterministic Security Model.

---

# 139. No AI for Publication State

Draft/Published status не определяется LLM.

---

# 140. No AI for Visibility

AI не решает, какие Contacts public.

---

# 141. Automation Reversibility

Каждое AUTO-3 действие SHOULD быть:

- reviewable;
- traceable;
- reversible where domain allows.

---

# 142. Reversibility Exceptions

Некоторые операции технически irreversible:

например external notification already sent.

Поэтому такие действия требуют более строгой confirmation policy, если сообщение имеет professional meaning.

---

# 143. Notifications vs Replies

Важно различать:

## System Notification

```text
New casting inquiry received.
```

может отправляться автоматически.

## Professional Reply

```text
We accept your invitation.
```

требует человека.

---

# 144. Automatic Communication Boundary

Автоматически разрешены только:

- transactional confirmation;
- operational alert;
- technical notification;

если template заранее утверждён.

---

# 145. Professional Communication Boundary

Требует человека:

- negotiation;
- acceptance;
- rejection;
- commitment;
- personal professional response.

---

# 146. Transactional Confirmation

Public visitor MAY автоматически получить:

```text
Обращение №123 принято.
```

Это не является содержательным ответом от актрисы.

---

# 147. Safe Automation Preconditions

AUTO-4 разрешён, если выполняются все условия:

1. input authorized;
2. operation deterministic;
3. no semantic professional change;
4. no privacy expansion;
5. retry safe;
6. result validated;
7. source protected;
8. failure observable.

---

# 148. Auto-4 Failure Rule

Автоматическая операция не имеет права silently fail, если от неё зависит professional Journey.

---

# 149. Idempotency Requirement

Особенно важно для:

- PDF generation;
- QR;
- notification retries;
- derivative generation;
- migration;
- background jobs;
- event processing.

---

# 150. Duplicate Side Effects

Повтор одного job не должен создавать:

```text
3 Feedback records
4 WhatsApp messages
5 duplicate PDFs
```

---

# 151. Automation Scheduling

Scheduled processes MAY использоваться для:

- link health;
- deadlines;
- retention;
- retries;
- content freshness signals.

---

# 152. Scheduling Does Not Create Business Truth

Например:

```text
deadline passed
```

может быть обнаружено автоматически.

Но:

```text
Casting = Closed — Not Selected
```

не следует автоматически без business rule.

---

# 153. Content Freshness Automation

Allowed:

```text
detect age
→ suggest review
```

Not allowed:

```text
age threshold
→ hide photo
```

---

# 154. Analytics Automation

Allowed:

```text
calculate conversion
detect anomaly
suggest UX review
```

Not allowed:

```text
low engagement
→ remove content automatically
```

---

# 155. Search Automation

Allowed:

- index;
- reindex;
- synonym mapping from curated dictionary.

Not allowed:

- AI adds Skill because search query resembles biography phrase.

---

# 156. Migration Automation

Allowed:

- deterministic transformations;
- controlled field mapping;
- idempotent derived migration.

Human required:

- semantic ambiguity;
- conflicts;
- uncertain entity type.

---

# 157. Migration Dry Run

Important migrations SHOULD support:

```text
analyze
→ report
→ execute
```

where appropriate.

---

# 158. Archive Automation

Automatic archive of professional Master Data is disabled by default.

Temporary sessions/technical derived artifacts MAY use automated retention.

---

# 159. Deletion Automation

Professional content auto-deletion:

**PROHIBITED by default.**

---

# 160. Retention Automation

May automatically remove:

- expired temporary Builder Sessions;
- transient generated technical cache;
- expired technical artifacts;

according to documented retention policy.

---

# 161. Audit Automation

Audit SHOULD automatically record at least:

- who;
- what;
- before/after or relevant diff;
- when;
- source/action context.

---

# 162. AI Audit

For AI-assisted semantic operations SHOULD be possible to determine:

- AI capability;
- input/source context;
- output;
- human action;
- applied/not applied.

---

# 163. Source Snapshot

For AI Drafts, source context SHOULD be captured sufficiently to understand what facts were available at generation time.

---

# 164. Staleness of AI Draft

If source facts change before AI Draft is applied, system SHOULD warn or revalidate where meaningful.

---

# 165. Example — Biography Draft Staleness

```text
Draft generated
↓
Language Level changed
↓
Draft still claims old level
```

System SHOULD flag Draft as potentially stale if dependency tracking allows.

---

# 166. Automatic Dependency Invalidation

Dynamic derivatives SHOULD automatically become:

```text
stale
```

or be rebuilt when Source changes.

---

# 167. Historical Snapshot Protection

Published historical outputs MUST NOT be updated merely because Source changed.

---

# 168. Automation Priority Model

Automation investment priority:

```text
1. Prevent data loss
2. Prevent privacy/security failure
3. Protect professional Journey
4. Eliminate repetitive work
5. Improve administrative convenience
6. Optimize non-critical presentation
```

---

# 169. Highest-Priority Automation

Examples:

- save Feedback before notify;
- validate Public Builder permissions;
- protect Original media;
- revalidate QR;
- prevent concurrency overwrite;
- maintain audit.

---

# 170. High-Value Routine Automation

Examples:

- derivatives;
- link validation;
- readiness;
- stale detection;
- PDF generation;
- search indexing.

---

# 171. Low-Value Automation

Automation SHOULD NOT be added merely because possible.

Например:

```text
AI rewrites every caption automatically
```

creates unnecessary risk and noise.

---

# 172. Automation UX

Human-confirmed automation should show:

```text
What will change?
Why?
What source?
Can it be undone?
```

before application where appropriate.

---

# 173. Confirm Button Language

Avoid vague:

```text
Continue
```

for high-impact action.

Prefer explicit:

```text
Опубликовать анкету
Применить классификацию
Создать кастинг
```

---

# 174. Bulk Automation

Bulk action MAY be supported.

Example:

```text
12 photos classified as Close-Up suggestions
```

Admin can review and approve selected items.

---

# 175. Bulk Approval Safety

Bulk approval MUST NOT obscure:

- affected objects;
- consequences;
- exceptions.

---

# 176. Confidence

AI confidence MAY be displayed as support signal.

But:

```text
high confidence
```

does not remove human review requirement where semantic approval is mandatory.

---

# 177. Low Confidence

Low confidence SHOULD lead to:

- clearer warning;
- human review;
- no automatic apply.

---

# 178. Automation and Accessibility

Automated theme/content generation must pass accessibility validation where relevant.

---

# 179. Automation and Performance

Background automation must not unnecessarily block Public request path.

Examples:

- thumbnail processing asynchronous where appropriate;
- analytics non-blocking;
- AI not required to render public page.

---

# 180. Automation and Reliability

Optional service failure SHOULD degrade capability, not entire product.

---

# 181. Dependency Isolation

Examples:

```text
AI unavailable
→ manual editing available

WhatsApp unavailable
→ Feedback saved

Analytics unavailable
→ Public site functional

Theme AI unavailable
→ current Theme active
```

---

# 182. Automation Observability

For critical background processes system SHOULD expose:

- status;
- failed state;
- retry;
- last attempt;
- error summary.

---

# 183. User-Facing Technical Detail

Admin needs actionable information, not raw stack traces by default.

Raw technical detail MAY be available in diagnostic layer.

---

# 184. Automation Matrix and Customer Journey

Public visitors SHOULD rarely notice automation directly.

They should notice outcomes:

- fast site;
- valid links;
- current PDFs;
- quick generation;
- reliable forms.

---

# 185. Automation Matrix and Admin Journey

Admin SHOULD see:

```text
What system did automatically
What it suggests
What requires decision
What failed
```

---

# 186. Automation Matrix and Virtual Operator

Virtual Operator is the main orchestrator of human attention but not necessarily the technical executor of every automated job.

---

# 187. Automation Matrix and Background Jobs

Background jobs execute technical work.

Virtual Operator interprets operational/business relevance.

These concepts MUST remain separate.

---

# 188. Automation Matrix and Readiness

Readiness is a deterministic automated gate wherever requirements are deterministic.

---

# 189. Readiness Does Not Replace Approval

```text
Ready
```

means:

```text
system checks passed
```

not:

```text
approved for publication
```

---

# 190. Automation Matrix and Validation

Validation has three outcomes:

```text
Error
Warning
Information
```

---

# 191. Error

Blocks action where rule requires.

---

# 192. Warning

Does not necessarily block.

May require acknowledgement.

---

# 193. Information

No action required.

---

# 194. Automated Recommendation Priority

Recommendations SHOULD be ranked by impact, not AI novelty.

---

# 195. Examples of High Impact

```text
Primary Questionnaire missing
Casting deadline tomorrow
Video Intro broken
Public Contact unavailable
```

---

# 196. Examples of Low Impact

```text
Archived Project has no optional caption
```

---

# 197. Automation Governance Rule

Любое новое AUTO-4 действие должно быть документировано с:

```text
Trigger
Input
Output
Side Effects
Idempotency
Failure Behaviour
Security Boundary
Audit Requirement
```

---

# 198. AI Governance Rule

Любая новая AI-функция дополнительно должна определить:

```text
Purpose
Allowed Data
Forbidden Data
Prompt/Task Type
Output Type
Human Review
Fallback
Source Traceability
```

---

# 199. Human Approval Rule

Любое действие, создающее новый professional meaning, должно иметь конкретную точку human approval.

Нельзя считать:

```text
user opened page
```

или:

```text
user did not reject suggestion
```

эквивалентом approval.

---

# 200. Explicit Consent Rule

Approval — явное действие пользователя.

Например:

```text
Применить
Опубликовать
Создать кастинг
Отправить
```

---

# 201. Automation Security Rule

Автоматический процесс не получает больше permissions, чем требуется для задачи.

---

# 202. Background Actor Identity

Автоматические actions SHOULD иметь различимый system actor в Audit Trail.

Например:

```text
system
virtual-operator
casting-ai
bb-assistant
theme-ai
```

как conceptual actors.

---

# 203. No Shared Untraceable AI Actor

Все AI actions не должны записываться просто как:

```text
AI
```

если важно понимать capability/context.

---

# 204. Manual Override

Admin MAY исправить automatic technical result, если это предусмотрено domain.

Например classification suggestion.

---

# 205. Manual Override Does Not Disable Validation

Ручное действие не позволяет обходить Security/Privacy hard rules.

---

# 206. Fail Closed vs Fail Open

Для security/privacy:

```text
Fail Closed
```

Для optional enhancement:

```text
Graceful Degradation
```

Примеры:

```text
Public Builder authorization uncertainty
→ deny

Analytics failure
→ continue
```

---

# 207. QR Safety Failure

If system cannot establish QR target as safe/public:

```text
do not emit QR
```

или block output according to later exact policy.

Никогда не guess.

---

# 208. AI Fact Safety Failure

If source does not contain required fact:

```text
Not specified
```

not inferred.

---

# 209. Translation Safety Failure

If critical translation is unavailable:

use defined fallback / mark missing.

Do not silently fabricate.

---

# 210. Notification Safety Failure

If external notification fails:

```text
persisted business record remains
```

---

# 211. Automation Acceptance Checklist

Для каждого automated action:

- [ ] Purpose defined
- [ ] Automation Level assigned
- [ ] Trigger defined
- [ ] Inputs defined
- [ ] Authorization defined
- [ ] Idempotency defined
- [ ] Failure behaviour defined
- [ ] Audit requirement defined
- [ ] Human review defined where needed
- [ ] Rollback/reversibility considered
- [ ] Privacy boundary defined
- [ ] AI boundary defined if applicable
- [ ] Manual fallback defined where required

---

# 212. Automation Acceptance Criteria

Матрица считается соблюдённой, если:

1. технические derivatives автоматизированы;
2. professional facts не создаются автономно;
3. publication остаётся explicit;
4. AI outputs остаются distinguishable from facts;
5. Casting qualification является human decision;
6. BB Assistant не публикует и не отправляет autonomously;
7. Theme AI не публикует Theme;
8. Emotional Grid требует human confirmation;
9. Public Builder permissions revalidate server-side;
10. notification failure не уничтожает business record;
11. broken links не удаляются автоматически;
12. historical revisions остаются immutable;
13. analytics не изменяет UX/content самостоятельно;
14. safe automation idempotent where required;
15. optional automation failure не ломает core product;
16. critical automated actions имеют audit/observability.

---

# 213. Forbidden Automation Summary

Прямо запрещена автономная автоматизация:

```text
Invent professional fact
Change actor appearance
Publish professional content
Accept Casting
Accept Role Offer
Send professional commitment
Expose private Contact
Delete important professional content
Turn analytics recommendation into live change
Convert ambiguous legacy content without review
Rewrite published historical revision
```

---

# 214. Preferred Automation Summary

Предпочтительно автоматизировать:

```text
Metadata extraction
Derivatives
Validation
Readiness
Permission checks
QR generation/validation
PDF rendering
Search indexing
Cache invalidation
Retry logic
Deadline detection
Stale detection
Exception grouping
Audit logging
```

---

# 215. Suggestion-First Summary

Предпочтительно предлагать, а не применять:

```text
Photo classification
Emotional crop
Casting requirements
Profile match
Featured Project
Template optimization
Professional translation
Professional text
Theme proposal
Content freshness review
```

---

# 216. Customer Journey Outcome

Автоматизация должна приводить к двум конечным результатам.

## Для профессионального посетителя

```text
Less friction
More reliable content
Faster professional action
```

## Для Admin

```text
Less repetitive work
Fewer missed issues
More time for professional decisions
```

---

# 217. Master Automation Principle

```text
SYSTEM DOES
WHAT CAN BE VERIFIED

SYSTEM SUGGESTS
WHAT REQUIRES INTERPRETATION

HUMAN DECIDES
WHAT CHANGES PROFESSIONAL MEANING
```

---

# 218. Definition of Automation Compliance

Capability соответствует Automation Matrix, если:

1. имеет назначенный Automation Level;
2. automated behaviour не выходит за этот level;
3. semantic uncertainty не скрывается;
4. human approval является explicit;
5. professional meaning не меняется незаметно;
6. safe technical work автоматизировано там, где это снижает рутину;
7. failure не создаёт data loss;
8. AI не становится authority;
9. permissions соблюдаются независимо от automation source;
10. значимые изменения трассируются.

---

# 219. Итоговая схема

```text
                         PRODUCT AUTOMATION

                              EVENT
                                ↓
                           VALIDATION
                                ↓
                  Does it change professional meaning?
                       /                    \
                     NO                      YES
                     │                        │
                     ▼                        ▼
              Can result be exact?      Can system assist?
                /          \              /          \
              YES           NO          YES           NO
               │             │            │             │
               ▼             ▼            ▼             ▼
            AUTO-4        OBSERVE       AUTO-2        HUMAN
          Safe Execute      │          Suggest          │
               │            │             │             │
               │            └──────┬──────┘             │
               │                   ▼                    │
               │              HUMAN REVIEW              │
               │              /    |    \               │
               │         Approve Modify Reject          │
               │              │                         │
               └──────────────┴──────────────┬──────────┘
                                             ▼
                                          VERIFY
                                             ↓
                                           AUDIT
```

---

# 220. Финальный принцип

> **Автоматизация продукта должна быть максимально агрессивной там, где результат объективно проверяем и не меняет профессионального смысла, и максимально консервативной там, где речь идёт о фактах, репутации, внешности, публикации, коммуникации и кастинговых решениях. Система должна выполнять рутину, готовить варианты и находить проблемы; человек — подтверждать профессиональную реальность и принимать значимые решения.**