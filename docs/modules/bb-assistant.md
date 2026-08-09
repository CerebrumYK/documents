# BB ASSISTANT MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация профессионального writing capability: Source Snapshot → AIDraft → Human Review → Apply, fact grounding, revision history, casting responses, biographies, descriptions, cover letters и social copy

**Целевой файл:** `docs/modules/bb-assistant.md`  
**Документ:** DOC-131  
**Статус:** ✅ Completed  
**Тип:** Module / AI Writing Assistant / Professional Copy / Drafts / Human-in-the-Loop

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/product-principles.md`
- `docs/domain/domain-model.md`
- `docs/domain/entity-relationships.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions-and-history.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`
- `docs/architecture/ai-architecture.md`
- `docs/architecture/background-jobs.md`
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
- `docs/modules/projects.md`
- `docs/modules/training.md`
- `docs/modules/skills-languages.md`
- `docs/modules/professional-media-links.md`
- `docs/modules/questionnaires.md`
- `docs/modules/castings.md`
- `docs/modules/casting-ai-analysis.md`
- `docs/modules/feedback.md`
- `docs/modules/opportunity-pipeline.md`
- `docs/modules/site-themes.md`
- `docs/modules/virtual-operator.md`
- `docs/modules/marketing-analytics.md`

---

# 1. Назначение модуля

**BB Assistant** — внутренний Admin-инструмент для подготовки профессиональных текстов актрисы.

Он помогает создавать Draft-контент для:

- Biography;
- короткого professional summary;
- Profile/About;
- Project descriptions;
- Role descriptions;
- Training descriptions;
- Professional Media descriptions;
- Questionnaire copy;
- кастинг-ответов;
- cover letters;
- self-tape accompanying text;
- ответов на входящие профессиональные обращения;
- social captions;
- announcement copy;
- custom professional text.

BB Assistant также может анализировать предоставленную пользователем информацию о роли/кастинге **для целей написания текста**, но не заменяет Casting AI Analysis Module.

---

# 2. Главная доктрина

> **BB Assistant не создаёт профессиональную истину. Он формирует текстовый Draft только на основании разрешённых подтверждённых Source facts, явно предоставленного Human context и выбранной задачи. Любой AI output остаётся `AIDraft` до явного Human review/apply. Генерация текста никогда не означает публикацию, отправку, изменение Source, подтверждение факта или изменение business workflow.**

Canonical:

```text
Confirmed Source
      +
Human Instructions
      +
Task Context
      ↓
Source Snapshot
      ↓
BB Assistant
      ↓
Structured AIDraft
      ↓
Validation
      ↓
Human Review / Edit
      ↓
Explicit Apply
      ↓
Target Draft Field
      ↓
Separate Publish / Send
```

---

# 3. Fundamental separation

```text
Source Fact
≠ Source Snapshot
≠ AIDraft
≠ Human-Edited Draft
≠ Published Text
≠ Sent Message
```

И также:

```text
BB Assistant
≠ Casting AI
≠ Theme AI
≠ VOP
≠ Autonomous Communication Agent
```

---

# 4. Module identifiers

Используются:

```text
BB-*
BB-TASK-*
BB-SRC-*
BB-SNP-*
BB-DRF-*
BB-VAL-*
BB-APL-*
BB-HST-*
BB-CST-*
BB-FBK-*
BB-QNR-*
BB-SOC-*
BB-SEC-*
BB-AI-*
BB-INV-*
BB-AP-*
E2E-BB-*
```

---

# 5. Supported task types

Canonical baseline:

```text
BIOGRAPHY
PROFILE_SUMMARY
PROJECT_DESCRIPTION
ROLE_DESCRIPTION
TRAINING_DESCRIPTION
PROFESSIONAL_MEDIA_DESCRIPTION
QUESTIONNAIRE_TEXT
COVER_LETTER
CASTING_RESPONSE
SELF_TAPE_NOTE
FEEDBACK_REPLY
OPPORTUNITY_FOLLOW_UP
SOCIAL_CAPTION
ANNOUNCEMENT
CUSTOM_PROFESSIONAL_TEXT
```

---

# 6. BB-TASK-001 — Task Type Is Explicit

BB Assistant не должен пытаться угадать целевой тип документа только по свободному prompt, если контекст операции уже известен системе.

---

# 7. Biography

Может генерировать:

- short biography;
- full professional biography;
- biography для анкеты;
- biography для сайта;
- localized biography.

---

# 8. Biography Source

Может использовать только подтверждённые данные, например:

```text
Profile
Projects
Roles
Training
Skills
Languages
Achievements
```

---

# 9. BB-SRC-001 — Biography Cannot Invent Career History

Запрещено добавлять:

- несуществующие проекты;
- награды;
- роли;
- обучение;
- навыки;
- языки;
- опыт;
- агентство;
- возраст;
- профессиональные достижения.

---

# 10. Profile Summary

Предназначен для краткого позиционирования актрисы.

BB может:

- сократить;
- структурировать;
- сделать текст естественнее;
- убрать повторения;
- адаптировать под аудиторию.

---

# 11. Он не может создавать unsupported positioning

Например:

```text
“leading international actress”
“award-winning”
“professional stunt performer”
```

если таких подтверждённых Source facts нет.

---

# 12. BB-INV-001 — Style Transformation Cannot Create New Fact

---

# 13. Project Description

Источник:

```text
Project Source
Role Source
confirmed dates
director/production metadata
Human notes explicitly supplied for draft
```

---

# 14. Project description не меняет Project Source.

---

# 15. Role Description

Можно описывать:

- роль;
- character context;
- подтверждённый scope;
- professional contribution.

Но нельзя:

- выдумывать сюжет;
- приписывать screen time;
- придумывать характер персонажа;
- придумывать режиссёра;
- придумывать production facts.

---

# 16. BB-INV-002 — Unknown Project Detail Remains Unknown

---

# 17. Training Description

Источник:

```text
TrainingEntry
institution
course
teacher
period
confirmed notes
```

---

# 18. Training description не может превращать:

```text
short workshop
```

в:

```text
formal degree
```

---

# 19. BB-INV-003 — Copy Cannot Elevate Qualification Semantics

---

# 20. Skills

BB может красиво перечислить подтверждённые Skill records.

Он не может:

- повышать skill level;
- объединять разные skills в новый unsupported capability;
- утверждать professional mastery без соответствующего уровня/факта.

---

# 21. Languages

BB использует только canonical:

```text
NATIVE
A1
A2
B1
B2
C1
C2
```

---

# 22. BB не превращает:

```text
B2
```

в:

```text
fluent/native
```

без Human-approved wording policy.

---

# 23. BB-INV-004 — Language Proficiency Semantics Are Preserved

---

# 24. Professional Media Description

Может создавать описание:

```text
Видеовизитка
Showreel
Voice sample
Professional link
```

---

# 25. BB не создаёт URL.

---

# 26. BB-INV-005 — Writing Assistant Has No URL Authority

---

# 27. Questionnaire Text

Может создавать:

- introductory text;
- short professional summary;
- document-specific note;
- casting-oriented introductory paragraph;
- custom text block.

---

# 28. Mandatory Questionnaire facts

BB не может менять:

- mandatory Full Body;
- Close-Up;
- official Profile URL;
- selected Skills;
- Languages;
- Contacts;
- Media links.

---

# 29. BB-QNR-001 — BB Controls Copy, Not Questionnaire Structure Authority

---

# 30. Questionnaire block content

Если Custom Text block принадлежит Questionnaire configuration, BB может создать Draft для него.

После Human Apply текст становится document-owned configuration content, но не Profile Source.

---

# 31. BB-QNR-002 — Document-Specific Copy Does Not Become Profile Fact Automatically

---

# 32. Cover Letter

Может создавать персонализированное сопроводительное письмо для:

- Casting;
- producer;
- agency;
- project;
- collaboration.

---

# 33. Cover Letter source

Включает:

```text
confirmed Profile facts
selected relevant Projects
confirmed Skills
confirmed Languages
Casting source/context
Human instructions
```

---

# 34. Cover Letter нельзя основывать на:

- неподтверждённых AI assumptions;
- fabricated project familiarity;
- invented availability;
- invented travel readiness;
- invented fee expectations.

---

# 35. BB-CST-001 — Casting Cover Letter Is Evidence-Grounded

---

# 36. Casting Response

BB Assistant может создать Draft ответа на Casting.

---

# 37. Casting AI boundary

Casting AI отвечает за:

```text
requirements extraction
matching
evidence
recommendations
```

BB отвечает за:

```text
writing response text
```

---

# 38. BB-CST-002 — BB Does Not Re-Interpret Casting as Authority

---

# 39. BB может использовать confirmed Casting Requirement

Например:

```text
requires English B2+
```

и confirmed profile:

```text
English C1
```

для формирования текста.

---

# 40. Не может утверждать:

```text
“Полностью соответствую всем требованиям”
```

если matching содержит:

```text
PARTIAL_MATCH
MISMATCH
NO_CONFIRMED_DATA
```

---

# 41. BB-CST-003 — Response Must Respect Match Status

---

# 42. Unknown requirement

Если Casting source не содержит информации:

не писать как факт.

---

# 43. Self-Tape Note

BB может подготовить короткий текст:

- greeting;
- role/project reference;
- file/material note;
- professional closing.

---

# 44. Он не может утверждать, что self-tape отправлен.

---

# 45. BB-CST-004 — Drafting ≠ Submission

---

# 46. Feedback Reply

BB может подготовить Draft ответа на:

```text
CASTING_INVITATION
ROLE
COLLABORATION
QUESTIONNAIRE
MATERIALS
QUESTION
```

---

# 47. Source

Только:

- incoming Feedback message;
- selected relevant confirmed actor facts;
- Human instructions;
- approved business context.

---

# 48. BB-FBK-001 — Incoming Sender Message Is Untrusted Context

Prompt injection внутри Feedback не получает authority.

---

# 49. BB не может:

- отправить ответ;
- изменить Feedback state;
- закрыть Feedback;
- создать Casting;
- назначить responsible Admin.

---

# 50. BB-FBK-002 — Draft Reply Has No Workflow Authority

---

# 51. Opportunity Follow-Up

Может создавать Draft:

- follow-up message;
- offer clarification;
- polite reminder;
- availability response.

---

# 52. BB не меняет Opportunity stage.

---

# 53. BB-OPP-001 — Generated Follow-Up ≠ Stage Transition

---

# 54. Social Caption

BB может готовить текст для:

- Instagram;
- TikTok;
- other supported social platform.

---

# 55. Возможные input

```text
selected Project
MediaAsset
event context
confirmed professional fact
Human campaign instruction
```

---

# 56. BB не публикует Social Post.

---

# 57. BB-SOC-001 — Caption Draft ≠ Social Publish

---

# 58. Social platform constraints

Task может принимать:

```text
platform
length constraints
tone
language
hashtags policy
```

---

# 59. Hashtags

BB может предложить hashtags.

Они не являются Source facts.

---

# 60. Custom Professional Text

Для нестандартных задач может использоваться:

```text
CUSTOM_PROFESSIONAL_TEXT
```

---

# 61. Custom does not remove fact-grounding rules.

---

# 62. BB-TASK-002 — Custom Task Has Same Safety/Authority Boundaries

---

# 63. Source Snapshot

Перед генерацией система создаёт immutable generation context:

```text
BBSourceSnapshot
```

---

# 64. Suggested entity

```text
bb_source_snapshots
```

---

# 65. Representative fields

```text
id
profile_id
task_type
target_type?
target_id?
source_schema_version
source_references
source_versions
normalized_facts
human_context
locale
created_by
created_at
checksum
```

---

# 66. BB-SNP-001 — Generation Uses Frozen Snapshot

Это обеспечивает понимание:

> на основании каких фактов был создан текст.

---

# 67. Snapshot does not become Source.

---

# 68. BB-SNP-002 — Snapshot Is Provenance, Not Authority

---

# 69. Source references

Хранят:

```text
entity type
entity ID
source version/revision
selected fields/fact IDs
```

---

# 70. Do not indiscriminately snapshot entire Profile database.

---

# 71. BB-SEC-001 — Snapshot Is Purpose-Minimized

---

# 72. Example Biography snapshot

May include:

```text
professional name
selected bio facts
selected Projects
selected Training
selected Skills/Languages
```

but not:

```text
private Feedback
Opportunity notes
notification recipients
```

---

# 73. Snapshot immutability

После генерации не переписывается при изменении Source.

---

# 74. BB-SNP-003 — Historical Draft Keeps Original Generation Provenance

---

# 75. Source later changes

Draft может стать stale.

---

# 76. Stale Draft

If referenced Source versions differ from current:

```text
CURRENT
STALE
PARTIALLY_STALE
```

derived state may be shown.

---

# 77. BB-DRF-001 — Stale Does Not Rewrite Draft

---

# 78. Human choices

For stale Draft:

```text
Keep historical draft
Regenerate from current Source
Manually edit
```

---

# 79. Never silently update text.

---

# 80. BB-DRF-002 — Source Changes Never Auto-Rewrite Text

---

# 81. AIDraft

Primary output object:

```text
BBAIDraft
```

---

# 82. Suggested entity

```text
bb_ai_drafts
```

---

# 83. Representative fields

```text
id
profile_id
task_type
source_snapshot_id
target_type?
target_id?
locale
status
content
structured_metadata?
model/provider_metadata
validation_result
created_by
created_at
updated_at?
```

---

# 84. Draft statuses

Canonical:

```text
GENERATING
GENERATED
VALIDATION_FAILED
READY_FOR_REVIEW
HUMAN_EDITED
APPLIED
DISCARDED
STALE
FAILED
```

---

# 85. BB-DRF-003 — GENERATED ≠ READY_FOR_REVIEW

Provider output must pass application validation.

---

# 86. AI generation result

Can include:

```text
content
claims_used
source_reference_map
warnings
```

---

# 87. Claim grounding

Where practical, AI response contract SHOULD identify factual claims and supporting Source references.

---

# 88. Example

```text
Claim:
“Прошла обучение сценической речи…”

Source:
TrainingEntry #...
```

---

# 89. BB-VAL-001 — Factual Claims Should Be Traceable

---

# 90. Validation layers

At minimum:

```text
schema validation
fact-grounding validation
forbidden-content validation
task constraints
locale validation
target constraints
```

---

# 91. Schema validation

Checks:

- output type;
- required fields;
- length;
- format.

---

# 92. Fact-grounding validation

Checks claims against Snapshot.

---

# 93. Not every linguistic sentence is a “fact claim”.

But statements about:

- credits;
- skills;
- languages;
- dates;
- titles;
- availability;
- awards;
- location;
- representation;

require grounding.

---

# 94. BB-VAL-002 — Unsupported Professional Claim Blocks or Warns

For high-risk factual claim, generation should not be marked clean/ready.

---

# 95. Validation result

Suggested:

```text
PASS
PASS_WITH_WARNINGS
BLOCKED
```

---

# 96. Warning example

```text
The text says “extensive screen experience”,
but Source only confirms two projects.
Review wording.
```

---

# 97. BB-VAL-003 — AI Confidence Does Not Override Missing Evidence

---

# 98. Human Context

Admin can provide additional explicit context for this Draft.

Example:

```text
“Для этого письма можно указать, что я свободна 15–18 сентября.”
```

---

# 99. This Human context may be used in text.

---

# 100. BB-SRC-002 — Human-Provided Context Is Explicit Temporary Source for This Draft

---

# 101. But it does not automatically update Profile.

---

# 102. Example availability

Human says:

```text
available 15–18 September
```

BB may include in this Casting response.

It does not become long-term Profile availability fact unless separate domain action exists.

---

# 103. BB-SRC-003 — Draft Context Does Not Implicitly Persist to Source

---

# 104. Human instructions

Separate from factual context.

Examples:

```text
shorter
formal
more conversational
for agency
in English
under 500 characters
```

---

# 105. BB-INV-006 — Style Instruction Cannot Override Fact Rules

---

# 106. Tone controls

Possible structured options:

```text
PROFESSIONAL
WARM_PROFESSIONAL
CONCISE
FORMAL
CONVERSATIONAL
CONFIDENT
```

---

# 107. Avoid deceptive persona fabrication.

---

# 108. Length controls

Possible:

```text
SHORT
STANDARD
EXTENDED
CUSTOM_LIMIT
```

---

# 109. Locale

Explicit:

```text
ru
en
...
```

---

# 110. BB localization

BB may draft text in selected locale.

---

# 111. Translation boundary

If translating confirmed copy:

preserve meaning and professional facts.

---

# 112. BB-VAL-004 — Localization Cannot Upgrade Semantics

Example:

```text
“участвовала”
```

must not become:

```text
“starred”
```

unless role semantics support it.

---

# 113. Source language

AI may receive normalized facts independent of UI language.

---

# 114. Multilingual Drafts

Different language versions are independent content revisions.

---

# 115. BB-HST-001 — Locale Variants Are Not Implicitly Identical Revisions

---

# 116. Apply

Generation itself never modifies target.

Explicit command required:

```text
ApplyBBDraft
```

---

# 117. Apply flow

```text
AIDraft
  ↓
Human Review
  ↓
optional Human Edit
  ↓
Apply
  ↓
Target Draft field/configuration
```

---

# 118. BB-APL-001 — Apply Requires Human Action

---

# 119. Target may be

```text
Profile biography draft
Project description draft
Role description draft
Training description draft
ProfessionalLink description draft
Questionnaire custom text
Social post draft
Feedback reply draft
Casting response draft
```

---

# 120. Apply target ownership

After application, target domain owns the applied text.

---

# 121. BB-APL-002 — BB Does Not Become Owner of Applied Content

---

# 122. Example

Biography applied:

```text
ProfileDraft.biography
```

becomes Profile-owned draft field.

---

# 123. Applied does not mean Published.

---

# 124. BB-APL-003 — Apply ≠ Publish

---

# 125. Applied reply does not mean Sent.

---

# 126. BB-APL-004 — Apply ≠ Send

---

# 127. Applied Social caption does not mean Published.

---

# 128. BB-SOC-002 — Apply Caption ≠ Social Posting

---

# 129. Apply stale validation

Before apply, server checks:

- target still exists;
- same profile;
- draft not discarded;
- target version where needed;
- permissions;
- source staleness warning.

---

# 130. Stale Source

Policy can:

- warn;
- require confirmation;
- block high-risk send-oriented copy.

---

# 131. BB-APL-005 — Apply Revalidates Current Context

---

# 132. Applying old Draft does not automatically update its facts.

Human is informed it was based on old Snapshot.

---

# 133. Target concurrency

Use:

```text
expectedTargetVersion
```

where target mutable.

---

# 134. BB-APL-006 — AI Apply Cannot Overwrite Newer Human Edit

---

# 135. Human edited Draft

User may directly edit generated content before apply.

---

# 136. Status:

```text
HUMAN_EDITED
```

---

# 137. BB-HST-002 — AI Original and Human-Edited Version Remain Distinguishable

---

# 138. Draft revisions

Recommended append-oriented:

```text
bb_draft_revisions
```

---

# 139. Fields:

```text
draft_id
revision_number
content
origin
created_by
created_at
```

---

# 140. Origin values:

```text
AI_GENERATED
AI_REGENERATED
HUMAN_EDIT
APPLIED_SNAPSHOT
```

---

# 141. BB-HST-003 — Text History Is Revisioned

---

# 142. Regenerate

Command:

```text
RegenerateBBDraft
```

---

# 143. Regeneration never overwrites previous Draft revision invisibly.

---

# 144. BB-HST-004 — Regenerate Preserves Prior Output

---

# 145. Regeneration choices

Possible:

```text
same Snapshot
current Source Snapshot
selected Source subset
```

---

# 146. UI must show which one.

---

# 147. BB-SNP-004 — Regeneration Source Is Explicit

---

# 148. Comparing drafts

Admin should be able to compare:

- AI original;
- regenerated version;
- Human edited;
- applied version.

---

# 149. BB-HST-005 — Diff Is Derived From Immutable Draft Revisions

---

# 150. Provider failure

Draft request may fail.

---

# 151. Failure does not affect target domain.

---

# 152. BB-AI-001 — AI Failure Is Non-Critical

---

# 153. Manual writing remains possible.

---

# 154. BB-AI-002 — Product Works Without BB Assistant

---

# 155. AI provider abstraction

BB uses AI architecture/provider adapters.

Business module must not depend on one provider-specific response format.

---

# 156. BB-AI-003 — Provider Is Infrastructure Detail

---

# 157. Prompt architecture

Prompt consists conceptually of:

```text
system task contract
task type
fact snapshot
Human instructions
output schema
constraints
```

---

# 158. No arbitrary browsing by default.

---

# 159. BB-AI-004 — BB Does Not Browse Internet for Missing Facts

If fact is absent:

remain absent or ask Human through UI flow.

---

# 160. External URLs

BB does not fetch arbitrary URLs contained in Casting/Feedback.

---

# 161. BB-SEC-002 — Untrusted URL Does Not Trigger Autonomous Fetch

---

# 162. Prompt injection boundaries

Incoming:

- Casting text;
- Feedback;
- custom pasted text;

is untrusted content.

---

# 163. It cannot override:

- task contract;
- system constraints;
- privacy;
- output schema.

---

# 164. BB-SEC-003 — User/Pasted Source Content Has No Tool Authority

---

# 165. Data minimization

BB request includes only facts relevant to task.

---

# 166. Example Project Description

No need to send:

- private ContactMethods;
- Feedback history;
- Opportunity pipeline;
- Notification recipients.

---

# 167. BB-SEC-004 — AI Context Is Purpose-Bound

---

# 168. Sensitive Contacts

Normally excluded.

Included only where explicitly needed for outbound letter signature/contact line.

---

# 169. Even then:

send only selected confirmed values.

---

# 170. BB-SEC-005 — Contact Data Is Explicitly Selected

---

# 171. Casting data

For response writing, BB may receive:

- casting title/context;
- confirmed requirements;
- role name;
- deadline if confirmed;
- selected match evidence.

---

# 172. It need not receive entire unrelated Casting history.

---

# 173. BB-CST-005 — Casting Context Is Minimized

---

# 174. Feedback data

For reply drafting:

send specific incoming message/thread context required.

Not entire Inbox.

---

# 175. BB-FBK-003 — Feedback AI Scope Is Single Inquiry/Thread

---

# 176. Opportunity

For follow-up:

send current relevant Opportunity details.

No need to send unrelated pipeline records.

---

# 177. Secrets

Never include:

```text
OAuth token
API key
session secret
signed storage credential
admin auth token
provider credential
```

---

# 178. BB-SEC-006 — Secret Hard Deny

---

# 179. AI logs

Do not log full private prompt/output indiscriminately.

---

# 180. Log metadata:

```text
request_id
task_type
snapshot_id
provider
model
latency
token usage if applicable
status
```

---

# 181. BB-SEC-007 — Logs Are Metadata-First

---

# 182. Raw prompts/output retention

Separate retention class.

---

# 183. Draft retention

May be longer because Human may use history.

---

# 184. BB-RET-001 — Prompt, Snapshot and Draft Retention Are Separate

---

# 185. Deleted Source

Historical Draft may still contain copied text.

Privacy purge must discover:

- Source Snapshot;
- AIDraft;
- DraftRevision;
- provider-retained data where applicable;
- applied copy;
- exports.

---

# 186. BB-SEC-008 — Privacy Purge Includes AI Copies

---

# 187. Generated factual errors

If Human identifies unsupported claim:

should be possible to flag Draft.

---

# 188. Suggested:

```text
MarkBBDraftIssue
```

with:

```text
UNSUPPORTED_FACT
WRONG_TONE
INCORRECT_TRANSLATION
OTHER
```

---

# 189. Useful for evaluation, not automatic Source change.

---

# 190. BB-VAL-005 — Draft Feedback Does Not Modify Professional Truth

---

# 191. Quality criteria

BB output should optimize:

- natural language;
- professional clarity;
- factual fidelity;
- concise structure;
- target audience;
- non-repetitiveness;
- human tone.

---

# 192. Avoid generic AI clichés

System prompts/evaluation should discourage:

- excessive superlatives;
- vague “passionate professional” filler;
- unsupported “unique” claims;
- repetitive adjectives;
- unnatural corporate phrasing.

---

# 193. BB-QUAL-001 — Human-Sounding Does Not Mean Informal by Default

---

# 194. Fact vs style

Example valid transformation:

Source:

```text
Project: Film X
Role: Anna
```

Draft:

```text
В фильме «X» Екатерина исполнила роль Анны.
```

---

# 195. Invalid:

```text
В международном хите «X» Екатерина блестяще исполнила главную роль Анны.
```

unless all claims are confirmed.

---

# 196. BB-VAL-006 — Adjectives Can Carry Unsupported Claims

---

# 197. Claims taxonomy

Useful validation categories:

```text
IDENTITY
CREDIT
ROLE
TRAINING
SKILL
LANGUAGE
AWARD
AVAILABILITY
LOCATION
CONTACT
EXPERIENCE_LEVEL
CASTING_REQUIREMENT
COMMERCIAL_TERM
```

---

# 198. High-risk claim classes

Especially:

```text
AWARD
AVAILABILITY
COMMERCIAL_TERM
REPRESENTATION
BOOKING_STATUS
```

require strong explicit grounding.

---

# 199. BB-VAL-007 — High-Risk Claims Require Direct Evidence

---

# 200. Commercial terms

BB should not invent:

- fee;
- rate;
- travel compensation;
- usage rights;
- exclusivity.

---

# 201. BB-CST-006 — Commercial Terms Are Human-Supplied Only

---

# 202. Availability

Only explicit current context.

---

# 203. BB-CST-007 — Availability Is Never Inferred From Calendar Absence

---

# 204. Representation

Do not claim agent/manager representation unless Contacts/Profile confirms it.

---

# 205. BB-VAL-008 — Representation Must Be Confirmed

---

# 206. Awards/Achievements

Only confirmed Source entries.

---

# 207. No upgrading:

```text
selection
```

to:

```text
winner
```

---

# 208. BB-VAL-009 — Achievement Status Semantics Are Preserved

---

# 209. Casting response modes

Recommended:

```text
ACCEPT_INTEREST
REQUEST_INFORMATION
DECLINE_POLITELY
SEND_MATERIALS_NOTE
SELF_TAPE_NOTE
FOLLOW_UP
CUSTOM
```

---

# 210. These modes guide copy only.

---

# 211. BB-CST-008 — Response Mode Does Not Execute Response

---

# 212. Feedback reply modes

Possible:

```text
ACKNOWLEDGE
REQUEST_DETAILS
SEND_QUESTIONNAIRE_NOTE
SEND_MATERIALS_NOTE
DECLINE
FOLLOW_UP
CUSTOM
```

---

# 213. Send remains separate module/command.

---

# 214. Social caption modes

Possible:

```text
PROJECT_ANNOUNCEMENT
BEHIND_THE_SCENES
PORTFOLIO_UPDATE
TRAINING_UPDATE
EVENT
GENERAL
```

---

# 215. Social publishing provenance

Applied caption can be copied into SocialPostDraft.

---

# 216. BB-SOC-003 — BB Draft Is Input to Social Publishing Draft, Not Provider Call

---

# 217. Questionnaire integration

Questionnaire may store provenance:

```text
bb_draft_id?
```

for applied custom text if useful.

---

# 218. But historical QuestionnaireRevision freezes final text, not dependence on mutable BB Draft.

---

# 219. BB-QNR-003 — Published Questionnaire Is Self-Contained Snapshot

---

# 220. Project integration

If applied to Project Draft:

Project text remains editable independently afterward.

---

# 221. BB-APL-007 — Applied Text Is Decoupled From Future BB Draft Changes

---

# 222. Updating BBDraft later

Does not update already applied target.

---

# 223. BB-APL-008 — No Live Binding After Apply

---

# 224. Apply again

Human may explicitly re-apply newer revision.

---

# 225. Must respect target concurrency.

---

# 226. Draft target mismatch

BBDraft created for Project A cannot be applied to Project B by changing client ID.

---

# 227. BB-SEC-009 — Draft Target Is Bound Server-Side

---

# 228. Same-profile

All source and target entities must belong same profile unless task explicitly supports external Casting/Feedback source context.

---

# 229. BB-SEC-010 — Cross-Profile Fact Mixing Is Forbidden

---

# 230. Source selector

Admin UI may allow selecting which facts to include.

Example Biography:

```text
Projects
Training
Skills
Languages
Achievements
```

---

# 231. Unselected facts should not be silently inserted.

---

# 232. BB-SRC-004 — Source Selection Is Explicit

---

# 233. Relevant Project recommendation

AI may suggest which confirmed Project is relevant to Casting response.

---

# 234. But Human should see evidence/source before apply.

---

# 235. BB-CST-009 — Relevance Suggestion Does Not Change Project Source

---

# 236. Interaction with Casting AI

Preferred flow:

```text
Casting Source
      ↓
Casting AI Analysis
      ↓
Human-confirmed requirements/match
      ↓
BB response task
      ↓
AIDraft
```

---

# 237. BB should prefer confirmed Casting analysis over re-extracting from raw text.

---

# 238. BB-CST-010 — Confirmed Casting Analysis Is Preferred Structured Input

---

# 239. If no Casting analysis exists

BB may still draft from Human-selected raw Casting text, but must treat it as untrusted and not persist extracted claims as Casting Source.

---

# 240. BB-CST-011 — Writing-Time Extraction Is Ephemeral

---

# 241. VOP integration

VOP may identify:

```text
Biography missing
Project description missing
Questionnaire custom text absent
```

---

# 242. It may recommend:

```text
Open BB Assistant
```

---

# 243. It cannot invoke-and-apply BB output autonomously.

---

# 244. BB-INV-007 — VOP Cannot Auto-Apply Writing

---

# 245. Theme AI boundary

Theme AI cannot alter BB content.

BB cannot alter Theme.

---

# 246. BB-INV-008 — Writing and Presentation Authorities Remain Separate

---

# 247. Analytics integration

Analytics may show which copy receives more engagement.

It cannot automatically rewrite copy.

---

# 248. BB-INV-009 — Analytics Does Not Auto-Generate/Apply Replacement Copy

---

# 249. A/B experimentation

Not baseline.

If introduced, Experiment domain owns assignments.

BB may create candidate variants only.

---

# 250. BB-INV-010 — BB Does Not Implicitly Become Experiment Engine

---

# 251. BB Draft history

Admin should see:

```text
created
source snapshot
AI generation
Human edits
applied target
applied at
discarded
```

---

# 252. Audit

High-value operations:

```text
BB generation requested
Draft applied
Draft discarded
Human feedback recorded
```

---

# 253. Full draft text need not be duplicated into Audit if stored elsewhere.

---

# 254. BB-HST-006 — Audit References Draft, Not Duplicate Sensitive Copy

---

# 255. Outbox

Possible:

```text
BBDraftGenerated
BBDraftApplied
BBDraftDiscarded
BBDraftGenerationFailed
```

---

# 256. Consumers

```text
Admin UI
VOP
Analytics
Evaluation pipeline
```

---

# 257. No notification on every generation by default.

---

# 258. Background jobs

Suggested:

```text
BB_GENERATE_DRAFT
BB_VALIDATE_DRAFT
BB_RETRY_GENERATION
BB_RETENTION_CLEANUP
```

---

# 259. Generation jobs

Input should reference:

```text
snapshot_id
task_type
task configuration
```

not arbitrary complete mutable DB state.

---

# 260. BB-SNP-005 — Worker Reads Frozen Snapshot

---

# 261. At-least-once generation

Use idempotency.

---

# 262. Same generation request should not create duplicate identical logical Draft unintentionally.

---

# 263. BB-AI-005 — Generation Request Is Idempotent

---

# 264. Regeneration is intentional new DraftRevision/generation.

---

# 265. Retry vs Regenerate

Important:

```text
Retry
= same intended generation after technical failure

Regenerate
= request a new alternative output
```

---

# 266. BB-AI-006 — Retry ≠ Regenerate

---

# 267. Retry same provider request

Should reuse same logical generation intent/idempotency key.

---

# 268. Regenerate

Creates new generation iteration.

---

# 269. Model changes

Draft provenance should record model/provider version if available.

---

# 270. BB-HST-007 — Model Provenance Is Recorded

---

# 271. Reproducibility

Exact AI byte-for-byte reproduction is not guaranteed.

Historical Draft text itself is stored.

---

# 272. BB-HST-008 — Preserve Output, Do Not Assume Model Reproducibility

---

# 273. Cost controls

Admin generation may use:

- rate limits;
- task-specific maximum context;
- maximum output length;
- provider timeout;
- retry limits.

---

# 274. BB-AI-007 — AI Usage Is Bounded

---

# 275. Rate limiting

Prevents accidental repeated generation.

---

# 276. Generation UX

Recommended controls:

```text
Task
Target
Language
Tone
Length
Facts to include
Additional context
Generate
```

---

# 277. Result UI

Should show:

```text
Generated Draft
Warnings
Sources used
Staleness
Edit
Regenerate
Apply
Discard
```

---

# 278. BB-UX-001 — Source Provenance Is Visible

---

# 279. Unsupported claim warning

Highlight text segment where feasible.

---

# 280. Example:

```text
“международный проект”
```

Warning:

```text
No supporting Project field confirms international production.
```

---

# 281. BB-UX-002 — Fact Warning Is Specific

---

# 282. Human edit

Should not erase source provenance, but new Human text may no longer be fully fact-validated.

---

# 283. Revalidation

After Human edit, fact validation should run again where practical.

---

# 284. BB-VAL-010 — Human Edit Can Trigger Revalidation

---

# 285. Human authority

Human may intentionally write claim outside current Source.

For professional Source application, system should then either:

- require separate Source update;
- mark as unverified custom text;
- warn that claim is not grounded.

---

# 286. BB-VAL-011 — Human Edit Does Not Automatically Convert Unsupported Claim Into Source Fact

---

# 287. Applying ungrounded custom copy

For document-specific text, may be permitted with explicit Human acknowledgement depending policy.

For canonical Biography/Project Source-derived field, stronger validation may block unsupported factual claims.

---

# 288. BB-VAL-012 — Validation Strictness Is Target-Specific

---

# 289. Target policy examples

### Profile Biography

```text
STRICT_FACT_GROUNDING
```

### Casting response

```text
STRICT_ON_FACTS
FLEXIBLE_ON_POLITE_LANGUAGE
```

### Social caption

```text
STRICT_ON_FACTS
FLEXIBLE_ON_STYLE
```

### Custom personal note

```text
WARN_ON_UNSUPPORTED_FACTS
```

---

# 290. BB-VAL-013 — Target Domain Defines Validation Policy

---

# 291. SEO copy

BB may draft:

- meta description;
- professional page summary;

only where SEO module permits.

---

# 292. Must use confirmed professional facts.

---

# 293. BB-INV-011 — SEO Copy Has Same Fact Rules

---

# 294. No keyword stuffing

BB should not fabricate roles/skills merely for SEO.

---

# 295. Accessibility

Generated copy should support:

- readable sentence structure;
- meaningful link labels where drafting such labels;
- no information represented only by emoji/symbol.

---

# 296. BB-QUAL-002 — Accessibility Is Part of Copy Quality

---

# 297. Copyright boundary

BB should generate original wording based on facts.

It should not copy lengthy protected text from external Casting briefs, websites or reference biographies.

---

# 298. BB-SEC-011 — Source Text Is Context, Not Copy Library

---

# 299. User-provided source

May be summarized/transformed as task requires, within product policy.

---

# 300. External examples

If Human pastes example copy, BB should emulate functional characteristics, not copy it verbatim by default.

---

# 301. Deletion

Draft can be discarded.

---

# 302. `DISCARDED` does not necessarily hard-delete immediately.

Retention policy may preserve short-term history.

---

# 303. BB-HST-009 — Discard ≠ Immediate Purge

---

# 304. Applied Draft deletion

Deleting BBDraft must not delete applied target text.

---

# 305. BB-APL-009 — Applied Target Does Not Cascade Delete With BB Draft

---

# 306. Source deletion

Historical Draft may require redaction/purge based on privacy policy.

---

# 307. Backup

Must preserve:

- applied target content through target domain backup;
- useful Draft history;
- Snapshot provenance as policy requires.

---

# 308. Restore

Must not:

- regenerate drafts;
- reapply drafts;
- republish content;
- resend replies;
- rerun provider calls automatically.

---

# 309. BB-INV-012 — Restore Does Not Replay AI Side Effects

---

# 310. Migration

Legacy AI-written fields may exist without provenance.

---

# 311. Migration statuses

```text
HUMAN_AUTHORED
AI_LEGACY_UNKNOWN
AI_WITH_PROVENANCE
UNKNOWN_ORIGIN
```

---

# 312. Do not fabricate Snapshot provenance for old content.

---

# 313. BB-MIG-001 — Unknown Origin Remains Unknown

---

# 314. Existing published text

Should not be moved back into AIDraft simply because it was historically AI-assisted.

---

# 315. Target Source owns current published content.

---

# 316. BB-MIG-002 — Current Source Ownership Takes Precedence

---

# 317. Legacy AI prompts

May be imported only if retention/privacy permits.

Not required for content validity.

---

# 318. Migration idempotency

Required.

---

# 319. Commands

Canonical:

```text
CreateBBSourceSnapshot
GenerateBBDraft
RetryBBDraftGeneration
RegenerateBBDraft
UpdateBBDraftHumanEdit
ValidateBBDraft
ApplyBBDraft
DiscardBBDraft
MarkBBDraftIssue
```

---

# 320. Optional helper commands

```text
CreateBiographyDraft
CreateCastingResponseDraft
CreateFeedbackReplyDraft
CreateSocialCaptionDraft
```

These should route internally through same BB contracts rather than separate logic silos.

---

# 321. Queries

```text
GetBBDraft
ListBBDrafts
GetBBDraftHistory
GetBBSourceSnapshot
GetBBDraftValidation
GetBBDraftDiff
GetBBTaskCapabilities
```

---

# 322. Generate input

Conceptually:

```text
taskType
targetType?
targetId?
locale
tone
length
selectedSourceRefs
humanContext?
instructions?
expectedTargetVersion?
idempotencyKey
```

---

# 323. Client cannot set

```text
confirmedFact=true
sourceVersion override
published=true
sent=true
business outcome
provider secret
system prompt
```

---

# 324. BB-SEC-012 — Client Cannot Promote Claim Authority

---

# 325. Server creates Snapshot

The client selects allowed sources; server resolves actual current facts/versions.

---

# 326. BB-SNP-006 — Snapshot Facts Are Server-Resolved

---

# 327. Apply input

```text
draftId
draftRevision
targetId
expectedTargetVersion
idempotencyKey
```

---

# 328. Server validates target binding.

---

# 329. Apply idempotency

Retry after network loss returns same application result.

---

# 330. BB-APL-010 — Apply Is Idempotent

---

# 331. Regenerate input

```text
draftId
sourceMode
updatedInstructions?
idempotencyKey
```

---

# 332. Source modes:

```text
SAME_SNAPSHOT
CURRENT_SOURCE
NEW_SELECTED_SOURCE
```

---

# 333. Error taxonomy

At minimum:

```text
BB_TASK_INVALID
BB_TARGET_NOT_FOUND
BB_TARGET_WRONG_PROFILE
BB_TARGET_UNSUPPORTED
BB_SOURCE_NOT_FOUND
BB_SOURCE_NOT_ELIGIBLE
BB_SOURCE_WRONG_PROFILE
BB_SOURCE_SNAPSHOT_INVALID
BB_DRAFT_NOT_FOUND
BB_DRAFT_STALE
BB_DRAFT_DISCARDED
BB_DRAFT_ALREADY_APPLIED
BB_GENERATION_FAILED
BB_PROVIDER_UNAVAILABLE
BB_OUTPUT_INVALID
BB_FACT_GROUNDING_FAILED
BB_UNSUPPORTED_CLAIM
BB_LOCALE_INVALID
BB_TARGET_STALE_VERSION
BB_APPLY_CONFLICT
BB_IDEMPOTENCY_CONFLICT
```

---

# 334. Concurrency

Two Admins can generate separate Drafts.

That is valid.

---

# 335. Apply race

Two users apply different drafts to same target version.

Only one can commit if optimistic concurrency is used.

---

# 336. BB-APL-011 — Target Concurrency Protects Human Work

---

# 337. Source changes while generation running

Generation uses frozen Snapshot.

Result may immediately be marked stale relative to current Source.

---

# 338. BB-SNP-007 — In-Flight Source Change Does Not Mutate Generation Input

---

# 339. Prompt/version changes

Prompt template/config should be versioned.

---

# 340. Suggested provenance:

```text
prompt_contract_version
task_schema_version
```

---

# 341. BB-HST-010 — Prompt Contract Is Versioned

---

# 342. Evaluation

BB quality should have test sets for:

- no fabricated facts;
- no semantic upgrades;
- correct use of Skills/Languages;
- Casting match wording;
- locale fidelity;
- tone;
- length;
- privacy minimization.

---

# 343. Deterministic validators should test facts independently of model.

---

# 344. BB-VAL-014 — Model Evaluation Does Not Replace Runtime Validation

---

# 345. VOP health checks

VOP may detect:

```text
stale unused Draft
generation failure
applied Draft with missing source provenance
```

---

# 346. Safe VOP actions

May clean expired Draft artifacts according to retention.

---

# 347. It cannot apply Draft.

---

# 348. BB-INV-013 — BB Apply Remains Human-Controlled Even Through VOP

---

# 349. Analytics

May measure:

```text
draft_generated
draft_regenerated
draft_applied
draft_discarded
```

---

# 350. Do not send full generated text to Analytics.

---

# 351. BB-SEC-013 — Analytics Uses Draft Metadata, Not Content Copy

---

# 352. Metrics

Useful:

```text
generation success rate
validation failure rate
apply rate
average regeneration count
task distribution
```

---

# 353. No “actor quality score”.

---

# 354. BB-INV-014 — Writing Analytics Does Not Score Person

---

# 355. Admin UX layout

Recommended:

```text
BB Assistant
├── Task
├── Context
├── Sources
├── Language
├── Tone
├── Length
├── Additional instructions
├── Generate
└── Result
    ├── Draft
    ├── Sources used
    ├── Warnings
    ├── Fact validation
    ├── Edit
    ├── Regenerate
    ├── Compare
    ├── Apply
    └── Discard
```

---

# 356. “Sources used” section

Should identify human-readable references:

```text
Profile
Project: ...
Role: ...
Training: ...
Skill: ...
Casting requirement: ...
```

---

# 357. BB-UX-003 — Provenance Should Be Human-Readable

---

# 358. Apply confirmation

For high-impact outward-facing target:

show explicit confirmation.

Examples:

- Biography overwrite;
- Casting response;
- Social post draft;
- Questionnaire custom text.

---

# 359. But sending/publishing remains separate after Apply.

---

# 360. BB-UX-004 — UI Clearly Separates Generate, Apply, Publish/Send

---

# 361. Casting response UI

Recommended side-by-side:

```text
Casting requirements
Confirmed matching facts
BB Draft
Warnings
```

---

# 362. This helps Human verify wording.

---

# 363. Feedback reply UI

Show original inquiry separately from generated Draft.

---

# 364. BB-UX-005 — Incoming Message and Draft Are Visually Distinct

---

# 365. Social UI

Caption Draft separated from provider publish controls.

---

# 366. Anti-patterns

`BB-AP-001`  
AI writes directly into published Biography.

`BB-AP-002`  
Generate automatically applies text.

`BB-AP-003`  
Apply automatically publishes site.

`BB-AP-004`  
Apply automatically sends email.

`BB-AP-005`  
Apply automatically sends WhatsApp.

`BB-AP-006`  
Social caption generation posts automatically.

`BB-AP-007`  
BB changes Opportunity stage after drafting follow-up.

`BB-AP-008`  
BB creates Casting after reading Feedback.

`BB-AP-009`  
BB confirms Casting Requirement.

`BB-AP-010`  
BB changes Skill level.

`BB-AP-011`  
BB converts B2 to “native”.

`BB-AP-012`  
BB invents Project.

`BB-AP-013`  
BB invents Role.

`BB-AP-014`  
BB invents award.

`BB-AP-015`  
BB invents agency representation.

`BB-AP-016`  
BB invents availability.

`BB-AP-017`  
BB invents rate/commercial terms.

`BB-AP-018`  
BB invents project dates.

`BB-AP-019`  
BB upgrades workshop to degree.

`BB-AP-020`  
BB describes supporting role as lead without evidence.

`BB-AP-021`  
BB copies AI Casting guess as confirmed fact.

`BB-AP-022`  
BB says “fully matches” despite partial mismatch.

`BB-AP-023`  
BB browses arbitrary URLs from Casting text.

`BB-AP-024`  
Prompt injection in Feedback gains tool authority.

`BB-AP-025`  
Whole private Profile/Inbox sent to model for one short caption.

`BB-AP-026`  
Provider secret stored in Snapshot.

`BB-AP-027`  
Raw AI prompt/output dumped into generic logs.

`BB-AP-028`  
Old Source changes silently rewrite Draft.

`BB-AP-029`  
Regenerate destroys previous Draft version.

`BB-AP-030`  
Retry technical failure treated as new creative regeneration.

`BB-AP-031`  
AI generation output assumed reproducible later.

`BB-AP-032`  
Unsupported claim marked valid because model confidence high.

`BB-AP-033`  
Human edit silently considered Source fact.

`BB-AP-034`  
Draft for Project A applied to Project B.

`BB-AP-035`  
Cross-profile Source facts mixed.

`BB-AP-036`  
Applied Draft remains live-linked and changes when BB Draft edited.

`BB-AP-037`  
Delete BBDraft deletes applied Biography.

`BB-AP-038`  
Discard immediately destroys audit/provenance history regardless retention.

`BB-AP-039`  
Restore regenerates all Drafts.

`BB-AP-040`  
Restore re-applies old Drafts.

`BB-AP-041`  
Restore re-sends replies.

`BB-AP-042`  
Analytics automatically rewrites low-performing copy.

`BB-AP-043`  
VOP auto-applies biography.

`BB-AP-044`  
Theme AI edits professional text.

`BB-AP-045`  
BB controls QR target.

`BB-AP-046`  
BB changes ProfessionalLink URL.

`BB-AP-047`  
BB writes directly into immutable QuestionnaireRevision.

`BB-AP-048`  
BB-generated text bypasses target domain validation.

`BB-AP-049`  
Custom task bypasses all fact rules.

`BB-AP-050`  
AI-generated social caption is treated as published state.

`BB-AP-051`  
BB stores all visitor history to personalize copy.

`BB-AP-052`  
BB claims unique success/marketability based on Analytics.

`BB-AP-053`  
BB creates unsupported SEO keywords as professional facts.

`BB-AP-054`  
AI output uses copyrighted reference copy verbatim as default behavior.

`BB-AP-055`  
One universal AI Draft object can mutate any domain target without typed contract.

`BB-AP-056`  
Client supplies `confirmed=true` for unsupported claim.

`BB-AP-057`  
Client supplies its own Source versions.

`BB-AP-058`  
Apply ignores stale target version.

`BB-AP-059`  
Same idempotency key applies different text silently.

`BB-AP-060`  
BB becomes autonomous communication agent.

---

# 367. Core invariants

`BB-INV-015`  
BB Assistant is a professional writing Draft capability, not Source authority.

`BB-INV-016`  
Every generation is based on explicit task type.

`BB-INV-017`  
Every generation uses a frozen purpose-minimized Source Snapshot.

`BB-INV-018`  
Snapshot contains server-resolved Source facts and versions.

`BB-INV-019`  
Snapshot is provenance and never replaces Source.

`BB-INV-020`  
Source changes never silently rewrite historical Draft.

`BB-INV-021`  
AIDraft is non-authoritative until Human review/apply.

`BB-INV-022`  
Generated output passes schema/task/fact validation before READY_FOR_REVIEW.

`BB-INV-023`  
Unsupported professional claims are blocked or clearly warned.

`BB-INV-024`  
AI confidence cannot substitute evidence.

`BB-INV-025`  
High-risk claims require direct confirmed evidence.

`BB-INV-026`  
BB cannot invent Projects, Roles, Awards, Skills, Languages or representation.

`BB-INV-027`  
BB cannot invent availability or commercial terms.

`BB-INV-028`  
Skill and Language semantics are preserved exactly.

`BB-INV-029`  
Training qualification semantics cannot be upgraded by copy.

`BB-INV-030`  
Achievement semantics cannot be upgraded.

`BB-INV-031`  
BB cannot create/edit URLs.

`BB-INV-032`  
BB does not own Questionnaire structure.

`BB-INV-033`  
Document-specific custom copy does not automatically become Profile Source.

`BB-INV-034`  
Casting response uses confirmed requirements/matches where available.

`BB-INV-035`  
BB remains separate from Casting AI.

`BB-INV-036`  
BB remains separate from Theme AI and VOP.

`BB-INV-037`  
Incoming Casting/Feedback text is untrusted AI context.

`BB-INV-038`  
BB does not autonomously browse arbitrary URLs.

`BB-INV-039`  
AI context is minimized to task purpose.

`BB-INV-040`  
Secrets never enter BB prompt/Snapshot.

`BB-INV-041`  
Generation failure cannot affect target Source.

`BB-INV-042`  
Manual writing remains possible with AI disabled.

`BB-INV-043`  
Generate does not Apply.

`BB-INV-044`  
Apply does not Publish.

`BB-INV-045`  
Apply does not Send.

`BB-INV-046`  
Social Draft does not publish to provider.

`BB-INV-047`  
Follow-up Draft does not change Opportunity stage.

`BB-INV-048`  
Reply Draft does not change Feedback workflow.

`BB-INV-049`  
Self-tape Draft does not submit material.

`BB-INV-050`  
Apply is explicit, server-validated and idempotent.

`BB-INV-051`  
Apply target is bound and same-profile validated.

`BB-INV-052`  
Optimistic concurrency prevents overwrite of newer Human edits.

`BB-INV-053`  
Applied target becomes owned by target domain.

`BB-INV-054`  
Applied text is not live-linked to future BBDraft edits.

`BB-INV-055`  
Draft history distinguishes AI generation, regeneration and Human edits.

`BB-INV-056`  
Regeneration preserves prior Draft versions.

`BB-INV-057`  
Retry and Regenerate remain distinct operations.

`BB-INV-058`  
Model/provider provenance is recorded.

`BB-INV-059`  
Historical Draft output is stored; reproducibility is not assumed.

`BB-INV-060`  
Locale variants preserve professional semantics.

`BB-INV-061`  
Human-provided temporary context may be used for current Draft but is not auto-persisted as Profile Source.

`BB-INV-062`  
Human edits can be revalidated.

`BB-INV-063`  
Human unsupported wording does not automatically become confirmed fact.

`BB-INV-064`  
Validation strictness is target-specific.

`BB-INV-065`  
Published Questionnaire freezes final copy and does not depend on mutable BBDraft.

`BB-INV-066`  
Deleting/discarding BBDraft does not delete applied target text.

`BB-INV-067`  
Privacy purge discovers Source Snapshot/Draft copies.

`BB-INV-068`  
Analytics uses BB metadata, not full generated copy.

`BB-INV-069`  
VOP cannot auto-apply BB Drafts.

`BB-INV-070`  
No autonomous communication/publishing authority exists in BB Assistant.

`BB-INV-071`  
Provider implementation is isolated behind AI adapter.

`BB-INV-072`  
Generation requests and Apply commands are idempotent.

`BB-INV-073`  
All BB actions remain profile/permission scoped.

`BB-INV-074`  
AI usage is bounded by context, output and retry limits.

`BB-INV-075`  
Restore never replays generation/apply/send/publish side effects.

`BB-INV-076`  
Legacy content with unknown AI origin keeps unknown provenance.

`BB-INV-077`  
Target domain validation still applies after BB Apply.

`BB-INV-078`  
BB cannot bypass QR, Visibility, Search or publication policies.

`BB-INV-079`  
Copyrighted reference text is not used as a copy-paste source by default.

`BB-INV-080`  
All authority, grounding, privacy and concurrency boundaries are enforced server-side.

---

# 368. E2E-BB-001 — Biography generation

Confirmed Profile/Project/Training Source selected.

BB creates Biography AIDraft.

Profile unchanged.

---

# 369. E2E-BB-002 — Unsupported project

AI output mentions Project absent from Snapshot.

Validation blocks/warns Draft.

---

# 370. E2E-BB-003 — Unsupported award

AI invents award.

Draft not marked clean/ready.

---

# 371. E2E-BB-004 — Skill level

Source Skill level = 3.

BB cannot state professional/expert level if mapping would imply level 5.

---

# 372. E2E-BB-005 — Language

English B2.

Draft does not state Native.

---

# 373. E2E-BB-006 — Training semantics

Workshop stays workshop.

It is not rewritten as degree/certification unless Source confirms it.

---

# 374. E2E-BB-007 — Project role semantics

Supporting role does not become leading role.

---

# 375. E2E-BB-008 — Human style instruction

User requests concise tone.

Text changes stylistically without new facts.

---

# 376. E2E-BB-009 — Human temporary context

Admin explicitly states availability for specific dates.

Draft may use it.

Profile Source remains unchanged.

---

# 377. E2E-BB-010 — Source Snapshot

Generation preserves exact selected source IDs/versions.

---

# 378. E2E-BB-011 — Source changes during generation

Project description changes while AI job runs.

Output remains tied to original Snapshot and is marked stale if applicable.

---

# 379. E2E-BB-012 — Regenerate current

Admin chooses current Source.

New Snapshot + Draft revision created.

Old output preserved.

---

# 380. E2E-BB-013 — Retry

Provider timeout.

Retry same intent does not create unintended alternate logical Draft.

---

# 381. E2E-BB-014 — Regenerate

Admin deliberately asks for another version.

New Draft revision generated.

---

# 382. E2E-BB-015 — Apply

Human reviews Biography and clicks Apply.

Profile Draft biography updated.

Still not publicly published.

---

# 383. E2E-BB-016 — Publish separation

After Apply, public profile remains on existing published content until normal Profile publication workflow.

---

# 384. E2E-BB-017 — Target race

Admin A manually edits Biography.

Admin B applies stale BB Draft against old version.

Apply rejected.

---

# 385. E2E-BB-018 — Human edit

Admin edits generated Draft.

Revision origin records Human edit.

---

# 386. E2E-BB-019 — Human edit validation

Edited text introduces unsupported award.

Revalidation warns/blocks according to target policy.

---

# 387. E2E-BB-020 — Cross-profile

Draft based on Profile A cannot be applied to Profile B.

---

# 388. E2E-BB-021 — Project target binding

Project A Draft cannot be redirected to Project B by client tampering.

---

# 389. E2E-BB-022 — Casting response

Confirmed Casting Requirement + Profile facts create grounded response Draft.

No message sent.

---

# 390. E2E-BB-023 — Partial match

Casting requirement partly matches.

BB does not state “полностью соответствую”.

---

# 391. E2E-BB-024 — No confirmed data

Requirement needs skill absent from Profile.

Draft avoids unsupported assertion.

---

# 392. E2E-BB-025 — Commercial terms

Casting has no fee details.

BB does not invent rate.

---

# 393. E2E-BB-026 — Availability

No explicit availability supplied.

BB does not claim availability.

---

# 394. E2E-BB-027 — Prompt injection in Casting

Casting text tells assistant to ignore system rules.

Ignored as untrusted source content.

---

# 395. E2E-BB-028 — Feedback reply

Incoming Feedback creates reply Draft.

Feedback state remains unchanged.

---

# 396. E2E-BB-029 — Feedback prompt injection

Incoming sender message cannot instruct BB to expose hidden data.

---

# 397. E2E-BB-030 — Feedback send separation

Applied reply becomes reply Draft.

No Email/WhatsApp send occurs.

---

# 398. E2E-BB-031 — Opportunity follow-up

BB drafts follow-up.

Opportunity stage unchanged.

---

# 399. E2E-BB-032 — Self-tape note

BB drafts self-tape accompanying text.

No Casting material submission occurs.

---

# 400. E2E-BB-033 — Questionnaire copy

BB generates custom intro.

After Human Apply it enters Questionnaire Draft.

Published revision unaffected until explicit Questionnaire publish.

---

# 401. E2E-BB-034 — Published Questionnaire

Subsequent BB Draft edit does not rewrite frozen QuestionnaireRevision.

---

# 402. E2E-BB-035 — Media description

BB generates ProfessionalLink description.

URL unchanged.

---

# 403. E2E-BB-036 — QR

Changing description does not alter QR target.

---

# 404. E2E-BB-037 — Social caption

Caption generated.

No provider API called.

---

# 405. E2E-BB-038 — Social apply

Caption applied to SocialPostDraft.

Still not published.

---

# 406. E2E-BB-039 — AI unavailable

Manual fields/editors remain usable.

---

# 407. E2E-BB-040 — Secret minimization

AI provider request contains no OAuth/API/session secrets.

---

# 408. E2E-BB-041 — Context minimization

Project description task does not send Feedback/Opportunity/Notification data.

---

# 409. E2E-BB-042 — Contact privacy

Contact data excluded unless explicitly required for requested letter/signature.

---

# 410. E2E-BB-043 — Raw URL fetch

Pasted external URL is not automatically fetched.

---

# 411. E2E-BB-044 — Draft provenance

Admin can inspect sources used for generated text.

---

# 412. E2E-BB-045 — Draft history

AI original, regenerate and Human edit remain independently inspectable.

---

# 413. E2E-BB-046 — Delete Draft

Discarding/deleting allowed BB history does not delete applied target copy.

---

# 414. E2E-BB-047 — Privacy purge

Source privacy deletion discovers applicable AI Snapshot/Draft copies.

---

# 415. E2E-BB-048 — Analytics

Analytics records `draft_applied` metadata without full text.

---

# 416. E2E-BB-049 — VOP

VOP detects missing Biography and recommends BB.

No automatic generation/apply occurs.

---

# 417. E2E-BB-050 — Analytics recommendation

Analytics notes low engagement.

No automatic rewrite/generation/apply.

---

# 418. E2E-BB-051 — Locale

Russian and English Draft variants are separate revisions.

---

# 419. E2E-BB-052 — Translation semantic preservation

Russian “участвовала в проекте” does not become “starred in” without role evidence.

---

# 420. E2E-BB-053 — Copyright reference

Reference text is used for style/context without reproducing long verbatim copy.

---

# 421. E2E-BB-054 — Idempotent apply

Network response lost after Apply.

Retry same key does not duplicate target mutation/history.

---

# 422. E2E-BB-055 — Different payload same key

Same idempotency key with different DraftRevision is rejected.

---

# 423. E2E-BB-056 — Restore

Backup restore does not regenerate, reapply, publish or resend historical Drafts.

---

# 424. E2E-BB-057 — Legacy AI content

Existing content with unknown AI provenance remains target-owned; no invented SourceSnapshot.

---

# 425. E2E-BB-058 — High-risk claim

AI states actress is represented by agency not present in Source.

Draft blocked/warned.

---

# 426. E2E-BB-059 — Unsupported “award-winning”

No award Source.

Phrase rejected by grounding validation.

---

# 427. E2E-BB-060 — Full flow

Confirmed Profile/Casting facts  
→ Source Snapshot  
→ grounded Casting response Draft  
→ Human edit  
→ validation  
→ Apply to response Draft  
→ explicit separate send action.

No automatic Source/business-state mutation occurs.

---

# 428. Architecture diagram

```text
          DOMAIN SOURCES
Profile / Projects / Training / Skills
Casting / Feedback / Questionnaire
               │
               ▼
        Source Selector
               │
               ▼
      BBSourceSnapshot
        immutable
               │
               ▼
         BB AI Service
               │
               ▼
          BBAIDraft
               │
        ┌──────┴──────┐
        ▼             ▼
   Validation     Provenance
        │
        ▼
     Human Edit
        │
        ▼
   Explicit Apply
        │
        ▼
    Target Draft
```

---

# 429. Authority diagram

```text
BB ASSISTANT
    │
    ├── structure text
    ├── rewrite
    ├── summarize
    ├── localize
    └── draft responses
             │
             ▼
          AIDraft

BB ASSISTANT CANNOT
    ├── create facts
    ├── confirm casting
    ├── change stage
    ├── publish
    ├── send
    ├── change URL
    └── change visibility
```

---

# 430. Casting workflow diagram

```text
Casting Source
      ↓
Casting AI Analysis
      ↓
Human-confirmed Requirements
      +
Confirmed Profile Facts
      ↓
BB Source Snapshot
      ↓
Casting Response Draft
      ↓
Human Review/Edit
      ↓
Apply to Response Draft
      ↓
Separate Explicit Send
```

---

# 431. Draft lifecycle diagram

```text
GENERATING
    ↓
GENERATED
    ↓
VALIDATE
 ┌──┴──────────┐
 ▼             ▼
READY       VALIDATION_FAILED
FOR REVIEW
    ↓
HUMAN_EDITED
    ↓
APPLIED

Alternative:
DISCARDED
STALE
FAILED
```

---

# 432. Source-change diagram

```text
Source v3
   ↓
Snapshot S1
   ↓
Draft D1

Source later → v4

D1 remains based on S1/v3
        │
        ▼
     STALE indicator
        │
 ┌──────┼──────────┐
 ▼      ▼          ▼
Keep   Edit    Regenerate
               from v4
```

---

# 433. Apply separation diagram

```text
Generate
   ≠
Apply
   ≠
Publish
   ≠
Send
```

---

# 434. Quality gate

Перед implementation должны быть подтверждены:

- [ ] explicit BB task taxonomy;
- [ ] BBSourceSnapshot;
- [ ] purpose-minimized server-resolved facts;
- [ ] immutable Snapshot provenance;
- [ ] Source version capture;
- [ ] BBAIDraft;
- [ ] Draft lifecycle;
- [ ] Draft revisions;
- [ ] Human edit history;
- [ ] AI/provider/model provenance;
- [ ] prompt contract version;
- [ ] schema validation;
- [ ] fact-grounding validation;
- [ ] unsupported-claim detection;
- [ ] high-risk claim rules;
- [ ] source-reference visibility;
- [ ] stale Draft detection;
- [ ] Regenerate vs Retry distinction;
- [ ] Apply command;
- [ ] Apply idempotency;
- [ ] target optimistic concurrency;
- [ ] target binding;
- [ ] same-profile validation;
- [ ] no auto-publish;
- [ ] no auto-send;
- [ ] no Opportunity stage changes;
- [ ] no Casting confirmation;
- [ ] no URL/visibility authority;
- [ ] Biography workflow;
- [ ] Project/Role description workflow;
- [ ] Training workflow;
- [ ] Questionnaire custom text workflow;
- [ ] Casting response workflow;
- [ ] Feedback reply workflow;
- [ ] Opportunity follow-up workflow;
- [ ] Social caption workflow;
- [ ] locale/tone/length controls;
- [ ] temporary Human context handling;
- [ ] AI context minimization;
- [ ] prompt-injection isolation;
- [ ] secret hard-deny;
- [ ] logging minimization;
- [ ] privacy purge discovery;
- [ ] Analytics metadata-only events;
- [ ] VOP recommendation-only integration;
- [ ] restore non-replay guarantees;
- [ ] migration for legacy AI text;
- [ ] deterministic E2E coverage.

---

# 435. Acceptance criteria

`AC-BB-001`  
BB Assistant является Draft-writing capability и не является professional Source authority.

`AC-BB-002`  
Каждая generation operation имеет explicit task type.

`AC-BB-003`  
Generation использует frozen server-resolved purpose-minimized Source Snapshot.

`AC-BB-004`  
Snapshot хранит Source IDs/versions/provenance, но не становится Source.

`AC-BB-005`  
Изменение Source после generation не переписывает historical Draft.

`AC-BB-006`  
BBAIDraft является non-authoritative до Human review/apply.

`AC-BB-007`  
Generated output проходит schema/task/fact validation.

`AC-BB-008`  
Unsupported professional factual claims блокируются или явно помечаются.

`AC-BB-009`  
AI confidence не заменяет подтверждённый Source.

`AC-BB-010`  
Projects, Roles, Training, Skills, Languages, Awards и representation не могут быть выдуманы.

`AC-BB-011`  
Availability/commercial terms не могут быть выведены или придуманы без explicit Human/Source evidence.

`AC-BB-012`  
Skill levels и CEFR semantics сохраняются без semantic inflation.

`AC-BB-013`  
BB не создаёт и не изменяет ProfessionalLink/Contact URLs.

`AC-BB-014`  
BB не меняет Questionnaire mandatory structure/selection rules.

`AC-BB-015`  
Questionnaire-specific BB text остаётся document-owned copy и не становится Profile fact автоматически.

`AC-BB-016`  
Casting response использует confirmed Casting requirements/matching where available.

`AC-BB-017`  
BB не заменяет Casting AI и не подтверждает Casting Requirements.

`AC-BB-018`  
Incoming Casting/Feedback text рассматривается как untrusted context.

`AC-BB-019`  
BB не выполняет arbitrary URL browsing/fetching для поиска отсутствующих фактов.

`AC-BB-020`  
AI context содержит только данные, необходимые конкретной writing task.

`AC-BB-021`  
Provider/API/OAuth/session secrets не передаются BB AI.

`AC-BB-022`  
BB Assistant остаётся полностью optional; manual authoring работает без AI.

`AC-BB-023`  
Generate никогда автоматически не выполняет Apply.

`AC-BB-024`  
Apply никогда автоматически не выполняет Publish.

`AC-BB-025`  
Apply никогда автоматически не выполняет Send.

`AC-BB-026`  
Social caption generation/application не вызывает provider publishing.

`AC-BB-027`  
Feedback reply Draft не изменяет Feedback workflow.

`AC-BB-028`  
Opportunity follow-up Draft не изменяет Opportunity stage.

`AC-BB-029`  
Self-Tape note generation не выполняет Casting submission.

`AC-BB-030`  
Apply выполняется явным Human action и server-side authorization.

`AC-BB-031`  
Apply использует optimistic concurrency и не перезаписывает newer Human target edit.

`AC-BB-032`  
Draft связан с разрешённым target и не может применяться к произвольному другому entity через client tampering.

`AC-BB-033`  
Cross-profile fact mixing запрещён.

`AC-BB-034`  
После Apply текст принадлежит target domain и может редактироваться независимо.

`AC-BB-035`  
Изменение BBDraft после Apply не изменяет уже применённый target.

`AC-BB-036`  
Draft history сохраняет AI-generated, regenerated и Human-edited revisions.

`AC-BB-037`  
Regeneration не уничтожает предыдущие Draft revisions.

`AC-BB-038`  
Retry технической ошибки и Regenerate новой версии являются разными операциями.

`AC-BB-039`  
AI provider/model/prompt contract provenance сохраняется.

`AC-BB-040`  
Historical Draft output хранится; exact future model reproducibility не предполагается.

`AC-BB-041`  
Localization не изменяет профессиональный смысл Source facts.

`AC-BB-042`  
Explicit Human context может использоваться в текущем Draft без автоматического превращения в long-term Profile Source.

`AC-BB-043`  
Human-edited AI text может быть revalidated.

`AC-BB-044`  
Human edit с unsupported factual claim не превращает claim автоматически в confirmed fact.

`AC-BB-045`  
Validation strictness определяется target-domain policy.

`AC-BB-046`  
Published QuestionnaireRevision хранит final text snapshot независимо от mutable BBDraft.

`AC-BB-047`  
Удаление/Discard BBDraft не удаляет применённый target content.

`AC-BB-048`  
Privacy purge учитывает BBSourceSnapshots, Drafts и provider-side copies where applicable.

`AC-BB-049`  
BB analytics не копирует full generated professional text.

`AC-BB-050`  
VOP может рекомендовать BB Assistant, но не auto-generate/apply professional copy.

`AC-BB-051`  
Analytics не переписывает copy автоматически.

`AC-BB-052`  
BB Assistant не меняет Theme, QR, Visibility, Search или Opportunity authority.

`AC-BB-053`  
Client не может объявить unsupported AI claim confirmed.

`AC-BB-054`  
Snapshots формируются сервером из current authorized Source.

`AC-BB-055`  
Generation и Apply имеют idempotency protection.

`AC-BB-056`  
Restore не регенерирует, не применяет, не публикует и не отправляет historical Draft automatically.

`AC-BB-057`  
Legacy AI content с неизвестным происхождением не получает fabricated provenance.

`AC-BB-058`  
Все fact grounding, Human apply, privacy, concurrency и send/publish boundaries имеют deterministic E2E coverage.

---

# 436. Финальная доктрина

> **BB Assistant является отдельным профессиональным writing capability и работает исключительно по модели `Confirmed Source + Explicit Human Context → Immutable Source Snapshot → AIDraft → Validation → Human Review/Edit → Explicit Apply`. Он может помогать с Biography, Profile summary, Project/Role/Training descriptions, Questionnaire text, cover letters, Casting responses, Feedback replies, Opportunity follow-ups и Social captions, но не получает никакой business authority. AI не может выдумывать professional facts, повышать Skill/Language semantics, придумывать availability, representation, awards, fees или project history. Generate не означает Apply, Apply не означает Publish, Apply не означает Send. После Human Apply текст переходит во владение соответствующего target domain и перестаёт быть live-linked с BB Draft. Source Snapshot и Draft history сохраняют provenance и позволяют определить, на каких фактах и какой версии Source был построен текст. BB Assistant остаётся отдельным от Casting AI, Theme AI, VOP, Notifications и Social Publishing и не превращается в автономного агента коммуникации или публикации.**