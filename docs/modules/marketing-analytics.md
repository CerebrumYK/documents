# MARKETING & PORTFOLIO ANALYTICS MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация privacy-conscious analytics, portfolio engagement, attribution, funnels, share tracking, bot filtering, retention и read-only business boundaries

**Целевой файл:** `docs/modules/marketing-analytics.md`  
**Документ:** DOC-129  
**Статус:** ✅ Completed  
**Тип:** Module / Analytics / Marketing Attribution / Portfolio Engagement / Funnel / Privacy

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
- `docs/modules/profile.md`
- `docs/modules/media-library.md`
- `docs/modules/portfolio.md`
- `docs/modules/emotional-portfolio.md`
- `docs/modules/emotional-grid.md`
- `docs/modules/projects.md`
- `docs/modules/professional-media-links.md`
- `docs/modules/contacts.md`
- `docs/modules/questionnaires.md`
- `docs/modules/public-questionnaire-builder.md`
- `docs/modules/castings.md`
- `docs/modules/feedback.md`
- `docs/modules/notifications.md`
- `docs/modules/opportunity-pipeline.md`
- `docs/modules/site-themes.md`
- `docs/modules/virtual-operator.md`

---

# 1. Назначение модуля

Marketing & Portfolio Analytics Module измеряет, **как публичный профессиональный контент используется и приводит к профессиональным действиям**, не превращаясь при этом в источник профессиональных фактов или автоматический optimization engine.

Модуль должен давать ответы на вопросы:

1. Сколько людей посещает профиль?
2. Какие разделы реально просматривают?
3. Какие Portfolio items открывают?
4. Какие видео запускают?
5. Какие профессиональные ссылки открывают?
6. Какие Questionnaire variants используются?
7. Сколько PDF скачивается?
8. Используется ли QR/share flow?
9. Как используют Public Questionnaire Builder?
10. Из каких источников приходят посетители?
11. Какие каналы приводят к Feedback/Inquiry?
12. Какие источники далее приводят к Casting/Opportunity/Booked?
13. Какие профессиональные материалы чаще используются в успешных workflows?
14. Где наблюдаются технические/UX потери?
15. Какие данные можно безопасно использовать для принятия Human decisions?

---

# 2. Главная доктрина

> **Analytics наблюдает и агрегирует уже произошедшие события. Она никогда не становится write-authority для Source domains. Ни число просмотров, ни conversion rate, ни модель attribution, ни AI insight не могут автоматически изменить Profile, Portfolio, Visibility, Questionnaire, Theme, Opportunity stage, professional ordering или public publication state.**

Canonical:

```text id="anl-canon-001"
User / Admin / Business Event
          ↓
   Analytics Event
          ↓
 Validation / Minimization
          ↓
   Event Store / Queue
          ↓
     Aggregation
          ↓
   Analytics Read Model
          ↓
 Dashboard / Insights
          ↓
      HUMAN DECISION
```

Не допускается:

```text id="anl-canon-002"
Analytics Metric
      ↓
automatic Source mutation
```

---

# 3. Fundamental separation

```text id="anl-canon-003"
AnalyticsEvent
≠ BusinessEvent
≠ Source Fact
≠ AuditEvent
≠ Notification
≠ Search Index
≠ OpportunityStageHistory
≠ Marketing Insight
```

---

# 4. Module identifiers

Используются:

```text id="anl-ids-001"
ANL-*
ANL-EVT-*
ANL-SES-*
ANL-ATR-*
ANL-FNL-*
ANL-SHR-*
ANL-MED-*
ANL-QNR-*
ANL-BLD-*
ANL-FBK-*
ANL-OPP-*
ANL-BOT-*
ANL-PRV-*
ANL-RET-*
ANL-AI-*
ANL-INV-*
ANL-AP-*
E2E-ANL-*
```

---

# 5. Analytics authority

Analytics MAY:

- collect;
- validate;
- aggregate;
- count;
- compare;
- segment;
- visualize;
- attribute;
- summarize;
- detect trends;
- produce advisory insights.

Analytics MUST NOT:

- edit Profile;
- hide/show Portfolio items;
- change visibility;
- reorder professional content automatically;
- publish Questionnaire;
- change primary Questionnaire;
- change Theme;
- activate Theme;
- change Opportunity stage;
- mark BOOKED;
- create Project;
- send professional reply;
- assign Skill level;
- infer professional facts.

---

# 6. ANL-INV-001 — Analytics Is Read-Only Relative to Business Source

---

# 7. Event taxonomy

Canonical high-level event classes:

```text id="anl-events-001"
PAGE
SECTION
PORTFOLIO
MEDIA
LINK
CONTACT
QUESTIONNAIRE
BUILDER
FEEDBACK
CASTING
OPPORTUNITY
SHARE
SEARCH
THEME
SYSTEM
```

---

# 8. Event naming

Machine names are stable, locale-neutral.

Examples:

```text id="anl-events-002"
page_view
section_view
portfolio_item_open
media_play
media_complete
professional_link_click
contact_click
questionnaire_open
questionnaire_pdf_download
builder_started
builder_generated
feedback_submitted
opportunity_created
opportunity_stage_changed
share_link_created
share_link_opened
```

---

# 9. ANL-EVT-001 — Localized UI Text Is Not Event Identity

---

# 10. AnalyticsEvent

Suggested logical entity/store:

```text id="anl-event-entity-001"
analytics_events
```

---

# 11. Representative fields

```text id="anl-event-fields-001"
id
event_name
event_version
occurred_at
received_at
profile_id
session_id?
anonymous_actor_id?
authenticated_actor_id?
source_entity_type?
source_entity_id?
context
attribution_context?
client_event_id?
is_bot?
privacy_class
```

---

# 12. Event version

Required:

```text id="anl-event-version-001"
event_version
```

---

# 13. ANL-EVT-002 — Analytics Event Schema Is Versioned

---

# 14. Why

Позволяет изменять:

- payload;
- field names;
- attribution model;
- device context;

без неоднозначного прочтения старых событий.

---

# 15. Event occurrence vs receipt

Store separately:

```text id="anl-time-001"
occurred_at
received_at
```

---

# 16. ANL-EVT-003 — Receipt Time Does Not Replace Event Time

---

# 17. Client-provided time

Untrusted for high-integrity calculations.

Server may bound/skew-check it.

---

# 18. ANL-EVT-004 — Client Timestamp Is Advisory

---

# 19. Page analytics

At minimum:

```text id="anl-page-events-001"
page_view
page_engaged
page_exit
```

---

# 20. `page_view`

Count valid public page render/navigation.

---

# 21. `page_engaged`

MAY represent meaningful active engagement based on deterministic threshold.

Threshold must be documented.

---

# 22. `page_exit`

Optional and inherently less reliable.

Do not require exactness.

---

# 23. ANL-INV-002 — Missing Exit Event Is Not Data Corruption

---

# 24. Section analytics

Useful sections:

```text id="anl-section-001"
hero
profile/about
main portfolio
emotional portfolio
projects
training
skills
languages
professional media
contacts
questionnaire CTA
```

---

# 25. Section view condition

Should require meaningful viewport intersection and minimum duration if duration semantics are used.

---

# 26. ANL-EVT-005 — Section Impression Rule Is Deterministic

---

# 27. Avoid

Firing repeated `section_view` continuously while user scrolls.

---

# 28. Session dedupe

At most one impression per configured section/entity per session/window where appropriate.

---

# 29. Portfolio analytics

Events MAY include:

```text id="anl-portfolio-001"
portfolio_view
portfolio_item_open
portfolio_item_next
portfolio_item_previous
portfolio_filter_used
emotional_portfolio_open
emotional_grid_open
```

---

# 30. Portfolio item identity

Use stable entity/revision identifiers.

Do not send image filesystem paths.

---

# 31. ANL-PRV-001 — Analytics Never Stores Storage Path

---

# 32. Image interaction metrics

Can measure:

- opens;
- navigation;
- dwell;
- gallery completion.

---

# 33. They do not mean professional quality.

---

# 34. ANL-INV-003 — More Viewed ≠ Better Professional Photo

---

# 35. No automatic ordering

A highly clicked photo does not move itself to first position.

---

# 36. ANL-INV-004 — Engagement Never Auto-Reorders Portfolio

---

# 37. Emotional Portfolio analytics

Can measure:

```text id="anl-emotional-001"
portfolio opens
grid opens
full emotional portfolio follow-through
```

---

# 38. It cannot modify:

- Grid cell crop;
- primary Grid;
- Human confirmation;
- emotional classification.

---

# 39. Media analytics

Events:

```text id="anl-media-001"
media_impression
media_play
media_pause
media_progress
media_complete
media_error
media_external_open
```

---

# 40. Progress buckets

Prefer bounded milestones:

```text id="anl-media-002"
25
50
75
100
```

rather than streaming every playback second.

---

# 41. ANL-MED-001 — High-Frequency Telemetry Is Bounded

---

# 42. Media completion

Defined only when valid playback reaches configured threshold/end.

---

# 43. Playback error

Operationally useful.

But Analytics is not replacement for Media Processing health monitoring.

---

# 44. ANL-MED-002 — Playback Analytics ≠ Media Operational Source

---

# 45. Professional Link analytics

Events:

```text id="anl-link-001"
professional_link_impression
professional_link_click
```

---

# 46. Link URL privacy

Analytics SHOULD store:

```text id="anl-link-002"
ProfessionalLink ID
link type
provider/domain class where useful
```

instead of repeatedly copying full URL.

---

# 47. ANL-PRV-002 — Entity Reference Preferred Over Full URL

---

# 48. URL query parameters

Do not indiscriminately ingest.

May contain:

- personal tokens;
- private IDs;
- campaign secrets.

---

# 49. ANL-PRV-003 — Destination Query String Is Not Analytics Payload By Default

---

# 50. Contact analytics

Allowed:

```text id="anl-contact-001"
contact_section_view
contact_method_click
```

---

# 51. Never record phone/email value as event property when ID/type is sufficient.

---

# 52. Example:

```text id="anl-contact-002"
method_type = WHATSAPP
contact_method_id = UUID
```

not:

```text id="anl-contact-003"
phone = "+..."
```

---

# 53. ANL-PRV-004 — Contact PII Is Data-Minimized

---

# 54. A contact click

Does NOT prove:

- message sent;
- call answered;
- casting received.

---

# 55. ANL-INV-005 — Click ≠ Conversion Outcome

---

# 56. Questionnaire analytics

Events MAY include:

```text id="anl-qnr-001"
questionnaire_open
questionnaire_variant_selected
questionnaire_pdf_request
questionnaire_pdf_download
questionnaire_link_click
questionnaire_qr_display
```

---

# 57. QR scan analytics

A static QR encoding an external canonical URL generally cannot be directly observed by the site unless target routing passes through an application-controlled trackable redirect.

---

# 58. Baseline

Do not compromise exact canonical QR semantics solely for tracking.

---

# 59. ANL-QNR-001 — Analytics Must Not Change Required QR Target Semantics

If QR requirement says exact canonical URL:

analytics cannot replace it with tracking redirect.

---

# 60. Download ≠ sent

`questionnaire_pdf_download` does not imply:

```text id="anl-qnr-002"
submitted to casting
```

---

# 61. ANL-QNR-002 — PDF Download Is Not Professional Submission

---

# 62. QuestionnaireRevision attribution

Event should bind to exact revision where relevant.

---

# 63. ANL-QNR-003 — Historical Questionnaire Analytics Is Revision-Bound

---

# 64. Public Questionnaire Builder analytics

Events:

```text id="anl-builder-001"
builder_open
builder_started
builder_template_selected
builder_item_added
builder_item_removed
builder_previewed
builder_generate_requested
builder_generated
builder_generation_failed
```

---

# 65. Analytics must not record hidden eligibility candidates.

Only actually served/selected IDs.

---

# 66. ANL-BLD-001 — Builder Analytics Cannot Leak Hidden Source Items

---

# 67. Builder generation

Bind to:

```text id="anl-builder-002"
GenerationSnapshot ID
template ID
```

where needed.

---

# 68. Do not copy entire generated questionnaire payload into Analytics.

---

# 69. ANL-PRV-005 — Snapshot Reference Over Snapshot Copy

---

# 70. Feedback conversion analytics

Events:

```text id="anl-feedback-001"
feedback_form_open
feedback_form_started
feedback_submit_attempt
feedback_submitted
feedback_validation_failed
```

---

# 71. Successful conversion event

`feedback_submitted` must be emitted only after authoritative Feedback commit.

---

# 72. ANL-FBK-001 — Analytics Cannot Count Failed Persistence as Successful Feedback

---

# 73. Feedback analytics payload

Can include:

```text id="anl-feedback-002"
feedback_type
source_context
attribution IDs
```

---

# 74. Must not contain by default:

```text id="anl-feedback-003"
sender email
sender phone
message body
attachments
Admin notes
```

---

# 75. ANL-PRV-006 — Feedback Analytics Does Not Duplicate Inbox

---

# 76. Casting analytics

Can consume domain events such as:

```text id="anl-casting-001"
casting_created
casting_review_started
casting_submitted
```

---

# 77. Casting Source text does not belong in Analytics.

---

# 78. Opportunity analytics

Critical business funnel.

Events should derive from committed Opportunity/StageHistory.

---

# 79. Canonical opportunity events:

```text id="anl-opportunity-001"
opportunity_created
opportunity_stage_changed
opportunity_booked
opportunity_closed_not_selected
opportunity_withdrawn
```

---

# 80. ANL-OPP-001 — Opportunity Analytics Is Derived From Domain Commit

---

# 81. Analytics cannot submit a synthetic stage transition.

---

# 82. Stage path

Use actual StageHistory.

---

# 83. Do not infer skipped stages.

---

# 84. ANL-OPP-002 — Funnel Respects Actual Stage Path

---

# 85. Booked conversion

`BOOKED` analytics event exists only if authoritative Opportunity has committed BOOKED.

---

# 86. ANL-OPP-003 — Model Prediction Cannot Emit Business Booked Event

---

# 87. Source attribution

Opportunity can carry provenance from:

```text id="anl-attribution-001"
Feedback
Casting
Campaign
Social
Direct
Referral
Questionnaire
Builder
```

---

# 88. Attribution then allows analysis:

```text id="anl-attribution-002"
Source
→ Feedback
→ Casting
→ Opportunity
→ BOOKED
```

---

# 89. Attribution is analytical interpretation.

It does not rewrite domain provenance.

---

# 90. ANL-ATR-001 — Analytics Attribution ≠ Domain Provenance

---

# 91. Attribution data hierarchy

Preferred:

```text id="anl-attribution-003"
explicit domain provenance
campaign parameters
referrer
session source
direct/unknown
```

---

# 92. Explicit provenance wins over heuristic attribution for factual linkage.

---

# 93. ANL-ATR-002 — Heuristic Attribution Cannot Override Explicit Provenance

---

# 94. UTM parameters

Supported allowlist:

```text id="anl-utm-001"
utm_source
utm_medium
utm_campaign
utm_content
utm_term
```

---

# 95. Sanitization

Bound lengths and allowed encoding.

---

# 96. Do not persist arbitrary URL query string.

---

# 97. ANL-ATR-003 — UTM Is Allowlisted

---

# 98. Campaign identity

Prefer server/domain campaign ID where controlled.

UTM strings remain descriptive attribution.

---

# 99. Referrer

May store normalized origin/domain/classification.

---

# 100. Avoid storing full referrer URL with sensitive path/query by default.

---

# 101. ANL-PRV-007 — Referrer Is Minimized

---

# 102. Referrer classifications

Example:

```text id="anl-referrer-001"
DIRECT
SEARCH
INSTAGRAM
TIKTOK
OTHER_SOCIAL
MESSENGER
AGENCY
CASTING_PLATFORM
OTHER
UNKNOWN
```

---

# 103. Unknown remains unknown.

Do not force attribution.

---

# 104. ANL-ATR-004 — Unknown Attribution Is Valid

---

# 105. First-touch attribution

May store first known acquisition source for a session/anonymous journey.

---

# 106. Last-touch attribution

May store final source before conversion.

---

# 107. Multi-touch attribution

Optional future analytical model.

---

# 108. ANL-ATR-005 — Attribution Model Is Explicitly Named

Never present “source” without saying whether it means first-touch, last-touch or explicit provenance.

---

# 109. Session model

Optional analytics session:

```text id="anl-session-001"
analytics_sessions
```

---

# 110. Suggested fields

```text id="anl-session-002"
id
profile_id
started_at
last_seen_at
first_touch
last_touch
device_class?
locale?
is_bot
```

---

# 111. Session identity

Should be privacy-minimized.

---

# 112. ANL-PRV-008 — Session Identifier Is Pseudonymous and Scoped

---

# 113. Avoid cross-site tracking.

---

# 114. No fingerprinting baseline

Do not build identity from:

- canvas fingerprint;
- font inventory;
- hardware entropy;
- hidden device fingerprint.

---

# 115. ANL-PRV-009 — Browser Fingerprinting Is Not Baseline

---

# 116. Anonymous visitor ID

If used:

- first-party;
- random;
- scoped;
- bounded retention;
- resettable.

---

# 117. Authenticated Admin

Admin analytics can be separately identified only for internal product usage where needed.

Public marketing analytics should not mix Admin traffic.

---

# 118. ANL-BOT-001 — Admin/Internal Traffic Is Excludable

---

# 119. Bot filtering

Analytics should distinguish likely human traffic from:

- search crawlers;
- uptime checks;
- social preview crawlers;
- monitoring bots;
- obvious scripted abuse.

---

# 120. Bot signals

Could include:

```text id="anl-bot-001"
known crawler UA
request pattern
robots/preview context
server classification
rate anomaly
```

---

# 121. Bot flag

```text id="anl-bot-002"
is_bot
bot_class?
```

---

# 122. ANL-BOT-002 — Bot Classification Is Operational Heuristic

---

# 123. Do not delete raw legitimate events merely because heuristic marks bot unless retention design explicitly does so.

Prefer flag/exclusion in reports.

---

# 124. Search engine bots

Useful separately for SEO technical analysis.

But should not inflate audience engagement.

---

# 125. Social preview crawlers

Must not count as real page engagement.

---

# 126. ANL-BOT-003 — Preview Crawler ≠ Human View

---

# 127. IP address

Do not store raw IP indefinitely for marketing analytics.

---

# 128. If needed for abuse/session aggregation:

use ephemeral/short-retention processing and/or privacy-preserving transformation.

---

# 129. ANL-PRV-010 — Raw IP Is Not Long-Term Analytics Dimension

---

# 130. Geography

If used, prefer coarse:

```text id="anl-geo-001"
country
region/city only when justified
```

---

# 131. Do not retain exact coordinates from analytics unless explicit product need exists.

---

# 132. ANL-PRV-011 — No Precise Location Analytics Baseline

---

# 133. Device context

Allowed coarse properties:

```text id="anl-device-001"
mobile
tablet
desktop
browser family
OS family
viewport class
locale
```

---

# 134. No fingerprint entropy collection.

---

# 135. Share tracking

Module should support explicit share-link analytics.

---

# 136. Shareable resources may include:

```text id="anl-share-001"
profile
questionnaire
portfolio
project
professional media
casting-safe questionnaire link
```

subject to access policy.

---

# 137. Share Link

Suggested entity:

```text id="anl-share-entity-001"
share_links
```

---

# 138. Representative fields

```text id="anl-share-fields-001"
id
profile_id
target_type
target_id
target_revision_id?
share_code
campaign_id?
channel_label?
access_scope
created_by
created_at
expires_at?
revoked_at?
```

---

# 139. Share link is optional wrapper

It may route to canonical target while recording controlled open event.

---

# 140. Security

Share wrapper must not broaden access.

---

# 141. ANL-SHR-001 — Share Tracking Cannot Escalate Target Access

---

# 142. Public target

May use public share code/redirect.

---

# 143. Private/casting-safe target

Must preserve exact token/access scope.

Never convert private target to public.

---

# 144. ANL-SHR-002 — Access Scope Comes From Target Policy

---

# 145. Share open event

Can record:

```text id="anl-share-002"
share_link_opened
```

then safely redirect.

---

# 146. Redirect must validate destination from server-side stored relation.

Do not accept arbitrary `?url=` destination.

---

# 147. ANL-SEC-001 — Share Redirect Is Not Open Redirect

---

# 148. Share channel labels

Optional:

```text id="anl-share-003"
WHATSAPP
EMAIL
INSTAGRAM
TIKTOK
AGENCY
CASTING_PLATFORM
CUSTOM
```

---

# 149. Channel label is attribution metadata.

Not proof of actual delivery channel.

---

# 150. ANL-SHR-003 — Share Label ≠ Verified Send

---

# 151. Share link revocation

Stops future access/redirect where link is the access layer.

Historical analytics remain.

---

# 152. Existing target URL changes

If Share Link points to stable entity resolver, resolver uses current allowed target semantics unless link is intentionally revision-bound.

---

# 153. Revision-bound questionnaire share

Should continue pointing to exact intended QuestionnaireRevision/access artifact.

---

# 154. ANL-SHR-004 — Share Target Semantics Are Explicitly Current or Revision-Bound

---

# 155. QR integration

DOC-130 owns QR generation.

Analytics rule:

> QR must not be made less correct or less direct solely to obtain tracking.

---

# 156. If QR target itself is application-controlled share URL by explicit product choice, analytics may observe it.

---

# 157. If QR must encode external canonical URL exactly, no scan analytics are expected.

---

# 158. ANL-SHR-005 — Tracking Is Secondary to QR Correctness

---

# 159. Funnel analytics

Suggested funnels:

### Public professional funnel

```text id="anl-funnel-001"
Profile View
→ Portfolio Engagement
→ Professional Media Engagement
→ Contact / Questionnaire
→ Feedback
```

### Casting funnel

```text id="anl-funnel-002"
Feedback
→ Casting
→ Opportunity
→ Audition
→ Callback
→ Offer
→ Booked
```

### Builder funnel

```text id="anl-funnel-003"
Builder Open
→ Started
→ Template Selected
→ Preview
→ Generated
```

---

# 160. ANL-FNL-001 — Funnel Steps Use Explicit Event Definitions

---

# 161. Funnel denominator

Every report must define its cohort.

Example:

```text id="anl-funnel-004"
unique human sessions with profile_view
```

not ambiguous “visitors”.

---

# 162. Conversion rate

Always show numerator/denominator/time period.

---

# 163. ANL-FNL-002 — Conversion Rate Is Reproducible

---

# 164. Cross-domain funnel

For Feedback→Booked:

prefer stable domain IDs/provenance.

Do not rely solely on same browser cookie.

---

# 165. ANL-FNL-003 — Business Funnel Uses Domain Provenance Where Available

---

# 166. Time-to-conversion

Possible:

```text id="anl-funnel-005"
Feedback submitted → Casting created
Casting created → Opportunity
Qualified → Audition
Offer → Booked
```

---

# 167. Event time accuracy

Use authoritative domain timestamps for domain conversions.

---

# 168. ANL-FNL-004 — Domain Timestamp Wins for Business Funnel

---

# 169. Marketing dashboard

Recommended sections:

```text id="anl-dashboard-001"
Overview
Traffic
Portfolio
Media
Questionnaires
Builder
Contacts & Feedback
Acquisition
Share Links
Opportunity Funnel
Conversion
Technology
Data Quality
```

---

# 170. Overview metrics

Examples:

```text id="anl-dashboard-002"
human profile views
engaged sessions
portfolio engagement
video plays/completions
questionnaire downloads
Builder generations
Feedback submissions
Opportunities
Booked outcomes
```

---

# 171. No single “success score” baseline.

---

# 172. ANL-INV-006 — Dashboard Does Not Reduce Professional Performance to One Opaque Score

---

# 173. Trend comparison

Can compare:

```text id="anl-dashboard-003"
current period
previous period
year-over-year where data exists
```

---

# 174. Avoid presenting statistically weak samples as decisive conclusions.

---

# 175. Minimum sample warnings

Recommended for percentages/AI recommendations.

---

# 176. ANL-AI-001 — Small Sample Size Must Be Visible

---

# 177. Analytics AI

Optional capability for natural-language analytics explanation.

Examples:

```text id="anl-ai-001"
“Какие материалы чаще открывали за последние 30 дней?”
“Какие источники привели к Feedback?”
```

---

# 178. AI receives aggregated/read-only analytics where possible.

---

# 179. ANL-AI-002 — AI Should Prefer Aggregates Over Raw Visitor Events

---

# 180. AI may:

- summarize trends;
- explain funnels;
- compare periods;
- propose experiments/manual actions.

---

# 181. AI cannot:

- hide low-performing photo;
- reorder portfolio;
- activate Theme;
- change CTA;
- change Opportunity stage;
- publish content.

---

# 182. ANL-AI-003 — Analytics AI Is Advisory Only

---

# 183. Example

Allowed:

```text id="anl-ai-002"
“Showreel has a higher completion rate than video intro in this period.”
```

---

# 184. Not allowed:

```text id="anl-ai-003"
“I hid the video intro because it performs worse.”
```

---

# 185. Correlation

AI/dashboard must avoid claiming causation from observational analytics.

---

# 186. ANL-AI-004 — Correlation Is Not Causation

---

# 187. Theme analytics

Analytics may compare performance by active ThemeRevision.

---

# 188. Event context MAY include:

```text id="anl-theme-001"
active_theme_revision_id
```

---

# 189. This enables Human analysis.

---

# 190. Analytics cannot auto-activate better-performing theme.

---

# 191. ANL-INV-007 — Theme Optimization Requires Human/Experiment Workflow

---

# 192. Content revision context

Events MAY bind to:

- active QuestionnaireRevision;
- ThemeRevision;
- Profile/Projection generation;
- relevant media/link ID.

---

# 193. This prevents comparing content without knowing version context.

---

# 194. ANL-EVT-006 — Significant Published Revision Context Is Captured Where Needed

---

# 195. Visibility

Analytics must receive only public-surface identifiers from public events.

---

# 196. A hidden entity ID should never appear just because client had an oversized hydration payload.

---

# 197. ANL-PRV-012 — Analytics Cannot Leak Hidden Entity IDs

---

# 198. Client analytics contract

Public browser may send allowlisted events only.

---

# 199. Client cannot invent authoritative business events such as:

```text id="anl-security-001"
opportunity_booked
feedback_submitted
casting_submitted
```

---

# 200. ANL-SEC-002 — Business Conversion Events Are Server-Emitted

---

# 201. Browser-eligible events

Examples:

```text id="anl-browser-001"
page_view
section_view
portfolio_item_open
media_play
professional_link_click
builder_started
```

---

# 202. Server-eligible events

Examples:

```text id="anl-server-001"
feedback_submitted
casting_created
opportunity_stage_changed
opportunity_booked
questionnaire_pdf_generated
```

---

# 203. ANL-EVT-007 — Event Authority Is Declared Per Event Type

---

# 204. Client event validation

Server validates:

- event name;
- version;
- field allowlist;
- payload size;
- entity eligibility;
- timestamps;
- rate.

---

# 205. Unknown event

Rejected or quarantined.

---

# 206. ANL-SEC-003 — Arbitrary Client Event Properties Are Rejected

---

# 207. Client event ID

Useful for dedupe:

```text id="anl-client-event-001"
client_event_id
```

---

# 208. Same client event retry

Stored once.

---

# 209. ANL-EVT-008 — Client Event Ingestion Is Idempotent

---

# 210. Duplicate page events

Separate navigations can legitimately create separate events.

Idempotency only covers same transmission identity.

---

# 211. Rate limits

Public event ingestion is bounded.

---

# 212. Event flood

Must not degrade core public profile.

---

# 213. ANL-SEC-004 — Analytics Failure Must Not Break Public Site

---

# 214. Client send

Use non-blocking mechanism.

Failure to send analytics cannot block navigation/contact action.

---

# 215. ANL-INV-008 — Analytics Is Non-Critical Path

---

# 216. Feedback submission exception

Feedback Source persistence remains critical.

Analytics after commit is optional.

---

# 217. Search analytics

MAY measure:

```text id="anl-search-001"
public_search_performed
public_search_result_open
admin_search_performed
```

---

# 218. Public search query retention

Potentially sensitive.

Use minimization/aggregation.

---

# 219. ANL-PRV-013 — Search Query Text Has Separate Retention/Privacy Policy

---

# 220. Internal/Admin traffic

Admin preview/page activity should be excluded from public audience metrics by:

- authenticated session;
- preview flag;
- internal environment marker.

---

# 221. ANL-BOT-004 — Admin Preview Does Not Inflate Public Metrics

---

# 222. Development/staging traffic

Must be separable.

---

# 223. Environment

Include:

```text id="anl-env-001"
production
staging
development
```

or route events to separate datasets.

---

# 224. ANL-EVT-009 — Non-Production Analytics Cannot Pollute Production KPIs

---

# 225. Consent/privacy

Analytics implementation must comply with applicable privacy policy and technical consent requirements.

---

# 226. Baseline design

Prefer first-party, privacy-minimized analytics.

---

# 227. ANL-PRV-014 — Collect Only Metrics With Defined Product Purpose

---

# 228. Every event/property should have:

- purpose;
- owner;
- retention;
- sensitivity classification.

---

# 229. Event registry

Recommended:

```text id="anl-registry-001"
AnalyticsEventRegistry
```

---

# 230. Registry fields

```text id="anl-registry-002"
event_name
version
authority
allowed properties
privacy class
retention class
bot policy
sampling policy
owner
```

---

# 231. ANL-EVT-010 — No Undocumented Production Events

---

# 232. Sensitive fields

Prohibited by default:

```text id="anl-sensitive-001"
password
auth token
session secret
private share token
raw contact value
Feedback body
Casting Source text
Admin notes
provider credentials
full private URL
```

---

# 233. ANL-PRV-015 — Secret/Private Payload Has Hard Deny List

---

# 234. URL sanitation

For page event:

store normalized route/template.

Example:

```text id="anl-url-001"
/questionnaire/:publicSlug
```

rather than secret-bearing full query.

---

# 235. ANL-PRV-016 — URL Analytics Is Canonicalized/Redacted

---

# 236. Error analytics

Can measure:

```text id="anl-errors-001"
page_error
media_error
builder_generation_failed
questionnaire_pdf_failed
```

---

# 237. Do not send stack traces containing secrets to marketing analytics.

Observability owns detailed engineering diagnostics.

---

# 238. ANL-INV-009 — Analytics ≠ Observability

---

# 239. Operational metrics vs Marketing analytics

Operational monitoring:

```text id="anl-ops-001"
CPU
queue depth
job latency
DB error
```

belongs observability/operations.

Marketing analytics consumes only selected product/business health summaries if needed.

---

# 240. Aggregations

Suggested rollups:

```text id="anl-rollup-001"
hourly
daily
weekly
monthly
```

---

# 241. Raw event retention

Shorter than aggregate retention where possible.

---

# 242. ANL-RET-001 — Raw Events Do Not Need Infinite Retention

---

# 243. Aggregated reports

Can retain longer because lower privacy sensitivity.

---

# 244. Retention classes

Recommended:

```text id="anl-retention-001"
RAW_PUBLIC_EVENTS
RAW_BUSINESS_ANALYTICS_EVENTS
SESSION_DATA
ATTRIBUTION_DATA
SHARE_LINK_DATA
DAILY_AGGREGATES
AI_ANALYTICS_SUMMARIES
```

---

# 245. ANL-RET-002 — Retention Is Class-Specific

---

# 246. Session/visitor identifier expiry

Should be bounded.

---

# 247. Historical business funnel

Can remain through domain IDs and aggregate facts after visitor session ID expires.

---

# 248. ANL-PRV-017 — Business Analytics Should Not Require Perpetual Visitor Identity

---

# 249. Deletion/privacy request

System must locate visitor-linked analytics data where technically/legally applicable.

---

# 250. Aggregated non-identifiable statistics may remain if no longer attributable.

---

# 251. ANL-RET-003 — Deletion Does Not Require Corrupting Truly Anonymous Aggregate Statistics

---

# 252. Share token privacy

Raw private access token must not be copied into analytics.

Store ShareLink entity ID.

---

# 253. ANL-PRV-018 — Private Access Token Never Becomes Analytics Dimension

---

# 254. Data quality

Analytics dashboard must surface:

```text id="anl-quality-001"
event ingestion gap
bot ratio
unknown attribution ratio
schema rejection rate
late event rate
duplicate rate
```

---

# 255. ANL-INV-010 — Analytics Reports Their Own Data Quality

---

# 256. Missing analytics

Should be shown as unavailable/incomplete.

Not zero.

---

# 257. ANL-INV-011 — Missing Data ≠ Zero

---

# 258. Example

If ingestion failed for two hours:

dashboard marks partial period.

---

# 259. Late events

May update aggregates within bounded correction window.

---

# 260. Aggregation generation

Reports should indicate through-date/generation.

---

# 261. ANL-EVT-011 — Aggregates Are Rebuildable Derived Data

---

# 262. Raw event immutability

After ingestion/validation, raw accepted event should normally be append-only.

---

# 263. Corrections

Use derived normalization/flags rather than silently rewriting history.

---

# 264. ANL-EVT-012 — Accepted Event History Is Append-Oriented

---

# 265. Fraud/bot reclassification

Can update classification metadata or derived view.

Do not fabricate user actions.

---

# 266. Dashboard filters

Recommended:

```text id="anl-filter-001"
date range
human/bot
source
campaign
device
country
page/section
questionnaire variant
ThemeRevision
source channel
Opportunity stage
```

---

# 267. Small cohorts

Avoid exposing highly identifying breakdowns for tiny public visitor groups.

---

# 268. ANL-PRV-019 — Analytics UI Uses Small-Cohort Privacy Guardrails Where Appropriate

---

# 269. CSV/export

If supported:

Admin-only.

---

# 270. Export should obey current analytics permissions and privacy redactions.

---

# 271. ANL-SEC-005 — Analytics Export Is Privileged

---

# 272. Raw event export

Not baseline.

Prefer aggregated export.

---

# 273. Marketing campaigns

Optional entity can track controlled campaigns:

```text id="anl-campaign-001"
campaigns
```

---

# 274. Fields:

```text id="anl-campaign-002"
id
name
channel
start_at?
end_at?
utm defaults?
status
```

---

# 275. Campaign does not change professional content automatically.

---

# 276. ANL-INV-012 — Campaign Tracking Is Attribution, Not Publication Authority

---

# 277. Social integration

Social publishing module may create campaign/share references.

Analytics can attribute incoming visits to social posts when safe source markers exist.

---

# 278. OAuth tokens

Never enter Analytics.

---

# 279. ANL-PRV-020 — Social Credentials Are Outside Analytics Domain

---

# 280. Link click tracking

For application-owned links, event may fire before navigation.

Navigation must proceed even if analytics endpoint fails.

---

# 281. ANL-LINK-001 — Tracking Failure Cannot Block Link Click

---

# 282. Contact click

Same principle.

---

# 283. PDF download tracking

Download should proceed even if analytics collection fails.

---

# 284. ANL-QNR-004 — Analytics Is Non-Blocking for Professional Artifacts

---

# 285. Server-side authoritative download event

Where feasible, application-controlled PDF response can emit server analytics after access validation.

---

# 286. Browser event may complement, not replace server artifact-access event.

---

# 287. ANL-EVT-013 — Server Event Preferred for Authoritative Artifact Delivery

---

# 288. Share link attribution

Opening ShareLink can create authoritative server-side open event.

---

# 289. Bot detection still applies.

---

# 290. Repeated reloads

Should be represented honestly; unique-session reports dedupe separately.

---

# 291. Unique visitor terminology

If anonymous IDs expire/change, label metrics appropriately:

```text id="anl-unique-001"
unique sessions
```

rather than guaranteed unique people.

---

# 292. ANL-INV-013 — Session Metric Is Not “Unique Human” Unless Technically Supported

---

# 293. Dashboard wording

Prefer:

```text id="anl-wording-001"
Sessions
Profile views
Engaged sessions
```

over unsupported identity claims.

---

# 294. Conversion to Feedback

Can link session attribution into Feedback creation through safe first-party attribution context.

---

# 295. Store attribution summary, not full browsing history, in Feedback provenance if needed.

---

# 296. ANL-ATR-006 — Business Record Receives Minimal Attribution Snapshot

---

# 297. Example:

```text id="anl-attr-snapshot-001"
source = Instagram
campaign = showreel_august
share_link_id = ...
```

---

# 298. Not:

```text id="anl-attr-snapshot-002"
full list of every page/scroll/click in session
```

---

# 299. Opportunity attribution

Opportunity may derive source through authoritative provenance:

```text id="anl-opportunity-attr-001"
Opportunity
→ Casting
→ Feedback
→ Attribution Snapshot
```

---

# 300. Analytics can report campaign-to-Booked outcome.

---

# 301. It must not copy private casting content into campaign reports.

---

# 302. ANL-OPP-004 — Funnel Join Uses IDs, Not Private Payload Duplication

---

# 303. Theme comparison

Analytics may show:

```text id="anl-theme-compare-001"
ThemeRevision 8:
engagement X

ThemeRevision 9:
engagement Y
```

---

# 304. Unless formal experiment exists, dashboard must not call this causal A/B result.

---

# 305. ANL-AI-005 — Before/After Comparison ≠ Controlled Experiment

---

# 306. Experimentation

Future A/B testing must be separate explicit experiment architecture.

---

# 307. Analytics module may consume experiment assignment/result later.

It does not silently assign variants baseline.

---

# 308. Search/privacy

Analytics endpoints and dashboards:

```text id="anl-security-002"
Admin-only
```

except public event ingestion endpoint.

---

# 309. Public ingestion endpoint is write-only.

---

# 310. ANL-SEC-006 — Public Event Endpoint Cannot Query Analytics

---

# 311. Dashboard authorization

Requires authenticated Admin permission.

---

# 312. Row/profile scope

If multi-profile future:

enforce profile scope.

---

# 313. ANL-SEC-007 — Cross-Profile Analytics Access Is Rejected

---

# 314. Event source entity validation

A public browser claiming:

```text id="anl-fake-entity-001"
portfolio_item_id = hidden item
```

must not create accepted public engagement event without eligibility validation.

---

# 315. ANL-SEC-008 — Analytics Cannot Be Used to Probe Hidden Entity Existence

---

# 316. Response behavior

Avoid distinguishing:

```text id="anl-probe-001"
hidden exists
```

from:

```text id="anl-probe-002"
unknown
```

in public ingestion errors where that leaks information.

---

# 317. Sampling

High-volume low-value events MAY be sampled.

---

# 318. Critical business conversion events

Must not be sampled away.

---

# 319. ANL-EVT-014 — Sampling Policy Is Event-Class Specific

---

# 320. Example

May sample:

```text id="anl-sample-001"
scroll progress
minor hover
```

Do not sample:

```text id="anl-sample-002"
feedback_submitted
opportunity_booked
```

---

# 321. Event batching

Client may batch low-risk analytics.

---

# 322. Maximum batch size bounded.

---

# 323. One invalid event

Should not necessarily invalidate all valid events if contract supports per-event result.

---

# 324. ANL-EVT-015 — Batch Validation Is Per Event

---

# 325. Analytics background jobs

Suggested:

```text id="anl-jobs-001"
ANALYTICS_INGEST
ANALYTICS_AGGREGATE_HOURLY
ANALYTICS_AGGREGATE_DAILY
ANALYTICS_REBUILD_AGGREGATE
ANALYTICS_BOT_RECLASSIFY
ANALYTICS_RETENTION_CLEANUP
ANALYTICS_ATTRIBUTION_RECONCILE
```

---

# 326. At-least-once jobs

Aggregation must be idempotent.

---

# 327. ANL-EVT-016 — Aggregate Rebuild Does Not Double-Count

---

# 328. Aggregate key

Example:

```text id="anl-aggregate-key-001"
date
profile
event
dimension set
generation
```

---

# 329. Rebuild

Can replace same derived aggregate generation transactionally.

Raw accepted events remain.

---

# 330. Analytics ingestion failure

Should be visible in operations/VOP.

---

# 331. VOP may detect:

```text id="anl-vop-001"
analytics ingestion gap
aggregate job stale
bot ratio anomaly
```

---

# 332. VOP may safely retry rebuild/reaggregation if idempotent.

---

# 333. VOP cannot change professional content based on result.

---

# 334. ANL-INV-014 — VOP Analytics Repair Is Derived-State Only

---

# 335. Notifications

Analytics anomalies generally use In-App operational notification.

Avoid external notification spam for routine KPI changes.

---

# 336. Example worth notifying:

```text id="anl-notify-001"
analytics pipeline unavailable
```

---

# 337. Example not necessarily:

```text id="anl-notify-002"
views down 12%
```

unless explicit monitoring policy exists.

---

# 338. ANL-INV-015 — KPI Change Is Not Automatically Incident

---

# 339. Audit

Audit high-impact configuration changes:

```text id="anl-audit-001"
analytics policy changed
retention changed
campaign created/edited
ShareLink created/revoked
analytics export generated
```

---

# 340. Individual page views do not belong in Audit.

---

# 341. ANL-INV-016 — Analytics Event Store ≠ Audit Log

---

# 342. API commands — public analytics

Possible:

```text id="anl-commands-001"
IngestAnalyticsEvent
IngestAnalyticsBatch
```

---

# 343. Server-side analytics commands/events

Internal:

```text id="anl-commands-002"
RecordBusinessAnalyticsEvent
RebuildAnalyticsAggregate
ReconcileAnalyticsAttribution
```

---

# 344. Share management

```text id="anl-commands-003"
CreateShareLink
RevokeShareLink
ArchiveShareLink
```

---

# 345. Analytics Admin

```text id="anl-commands-004"
CreateCampaign
UpdateCampaign
ArchiveCampaign
ExportAnalyticsReport
```

---

# 346. Queries

```text id="anl-queries-001"
GetAnalyticsOverview
GetTrafficAnalytics
GetPortfolioAnalytics
GetMediaAnalytics
GetQuestionnaireAnalytics
GetBuilderAnalytics
GetFeedbackAnalytics
GetAttributionAnalytics
GetShareAnalytics
GetOpportunityFunnelAnalytics
GetAnalyticsDataQuality
GetCampaignAnalytics
```

---

# 347. Public ingestion input

Conceptually:

```text id="anl-ingest-input-001"
eventName
eventVersion
clientEventId
occurredAt?
entityRef?
context
attributionContext?
```

---

# 348. Client cannot set

```text id="anl-ingest-deny-001"
isAdmin
isBusinessConversion
opportunityStage
feedbackPersisted
booked=true
rawIp
serverAttributionOverride
```

---

# 349. ANL-SEC-009 — Server Derives Trusted Context

---

# 350. Business event mapping

Example:

```text id="anl-business-map-001"
OpportunityBooked domain event
→ analytics event opportunity_booked
```

---

# 351. Mapping occurs after commit/outbox.

---

# 352. ANL-EVT-017 — Business Analytics Event Follows Source Commit

---

# 353. Error taxonomy

At minimum:

```text id="anl-errors-001"
ANALYTICS_EVENT_INVALID
ANALYTICS_EVENT_VERSION_UNSUPPORTED
ANALYTICS_EVENT_PROPERTY_INVALID
ANALYTICS_EVENT_TOO_LARGE
ANALYTICS_ENTITY_NOT_ELIGIBLE
ANALYTICS_RATE_LIMITED
ANALYTICS_BATCH_TOO_LARGE
ANALYTICS_DUPLICATE_EVENT
ANALYTICS_EXPORT_FORBIDDEN
ANALYTICS_DATE_RANGE_INVALID
ANALYTICS_CAMPAIGN_NOT_FOUND
ANALYTICS_SHARE_LINK_NOT_FOUND
ANALYTICS_SHARE_LINK_REVOKED
ANALYTICS_SHARE_TARGET_INVALID
ANALYTICS_ATTRIBUTION_INVALID
ANALYTICS_DATA_PARTIAL
```

---

# 354. Public error safety

Do not reveal hidden entity existence.

---

# 355. Analytics data model

Logical layers:

```text id="anl-model-001"
Raw Validated Event
        ↓
Normalized Event
        ↓
Session / Attribution
        ↓
Aggregates
        ↓
Dashboard Projections
```

---

# 356. Business-domain provenance remains separate.

---

# 357. Data ownership

Analytics owns:

- analytics event;
- aggregate;
- campaign attribution model;
- share analytics;
- dashboard projections.

---

# 358. It does not own:

- Feedback;
- Casting;
- Opportunity;
- Portfolio;
- Theme;
- Questionnaire.

---

# 359. ANL-INV-017 — Domain Ownership Is Explicit

---

# 360. Anti-patterns

`ANL-AP-001`  
Low-view photo automatically hidden.

`ANL-AP-002`  
High-view photo automatically made primary.

`ANL-AP-003`  
Analytics changes `show_on_public_site`.

`ANL-AP-004`  
Analytics changes Builder eligibility.

`ANL-AP-005`  
Analytics changes Questionnaire primary state.

`ANL-AP-006`  
Analytics publishes Questionnaire.

`ANL-AP-007`  
Analytics changes Theme automatically.

`ANL-AP-008`  
Analytics activates “better” Theme.

`ANL-AP-009`  
Analytics advances Opportunity stage.

`ANL-AP-010`  
Predicted success emits BOOKED event.

`ANL-AP-011`  
Video completion becomes Casting submission.

`ANL-AP-012`  
PDF download becomes submission.

`ANL-AP-013`  
Contact click becomes confirmed inquiry.

`ANL-AP-014`  
WhatsApp click becomes confirmed message.

`ANL-AP-015`  
Page view becomes unique person claim.

`ANL-AP-016`  
Bot preview counted as engaged human.

`ANL-AP-017`  
Admin preview inflates public visits.

`ANL-AP-018`  
Staging traffic enters production dashboard.

`ANL-AP-019`  
Raw phone/email stored in event props.

`ANL-AP-020`  
Feedback body copied into Analytics.

`ANL-AP-021`  
Casting Source copied into campaign report.

`ANL-AP-022`  
Admin notes copied into Analytics.

`ANL-AP-023`  
Private share token stored as dimension.

`ANL-AP-024`  
Full URL/query copied blindly.

`ANL-AP-025`  
Full referrer URL stored indefinitely.

`ANL-AP-026`  
Raw IP kept forever.

`ANL-AP-027`  
Precise location collected without purpose.

`ANL-AP-028`  
Browser fingerprinting introduced for “unique people”.

`ANL-AP-029`  
Cross-site tracking introduced unnecessarily.

`ANL-AP-030`  
Client can emit `opportunity_booked`.

`ANL-AP-031`  
Client can send arbitrary custom event properties.

`ANL-AP-032`  
Hidden entity IDs accepted from browser without validation.

`ANL-AP-033`  
Analytics ingestion errors reveal hidden entity existence.

`ANL-AP-034`  
Analytics endpoint blocks page navigation.

`ANL-AP-035`  
Analytics failure blocks Feedback submission.

`ANL-AP-036`  
Tracking failure blocks professional link click.

`ANL-AP-037`  
Tracking failure blocks PDF download.

`ANL-AP-038`  
QR canonical URL replaced with tracker against QR contract.

`ANL-AP-039`  
Share redirect accepts arbitrary destination URL.

`ANL-AP-040`  
Share wrapper broadens private access.

`ANL-AP-041`  
Distinct business provenance overwritten by UTM heuristic.

`ANL-AP-042`  
Unknown attribution forced to “Direct”.

`ANL-AP-043`  
First-touch and last-touch shown as same metric.

`ANL-AP-044`  
Skipped Opportunity stages fabricated in funnel.

`ANL-AP-045`  
Missing analytics period displayed as zero traffic.

`ANL-AP-046`  
Raw events retained forever without purpose.

`ANL-AP-047`  
Daily aggregates double-count after rebuild.

`ANL-AP-048`  
Marketing analytics stores engineering stack traces.

`ANL-AP-049`  
Analytics replaces Observability.

`ANL-AP-050`  
Analytics replaces Audit.

`ANL-AP-051`  
Tiny cohort presented as decisive conclusion.

`ANL-AP-052`  
AI claims causation from simple correlation.

`ANL-AP-053`  
AI automatically “optimizes” actress content.

`ANL-AP-054`  
AI ranks actress attractiveness/marketability.

`ANL-AP-055`  
Analytics creates opaque professional success score.

`ANL-AP-056`  
OAuth/social credentials copied into Analytics.

`ANL-AP-057`  
Search query text retained forever without policy.

`ANL-AP-058`  
Analytics dashboard is publicly accessible.

`ANL-AP-059`  
Cross-profile analytics data leaked.

`ANL-AP-060`  
Private event export available without authorization.

---

# 361. Core invariants

`ANL-INV-018`  
Analytics is read-only relative to all professional/business Source domains.

`ANL-INV-019`  
Analytics events use versioned stable machine contracts.

`ANL-INV-020`  
Event authority is explicit per event type.

`ANL-INV-021`  
Browser cannot authoritatively emit business conversion events.

`ANL-INV-022`  
Business conversion analytics are emitted only after committed Source event.

`ANL-INV-023`  
Client analytics ingestion is validated, bounded and idempotent.

`ANL-INV-024`  
Analytics failure never blocks public professional workflows.

`ANL-INV-025`  
Analytics never changes Portfolio ordering automatically.

`ANL-INV-026`  
Analytics never changes Visibility/Public/Builder flags.

`ANL-INV-027`  
Analytics never publishes or activates content/theme.

`ANL-INV-028`  
Analytics never changes Opportunity stage/outcome.

`ANL-INV-029`  
Engagement metric never becomes professional quality fact.

`ANL-INV-030`  
Portfolio/media analytics reference entity IDs instead of storage paths.

`ANL-INV-031`  
Contact analytics avoids raw contact PII.

`ANL-INV-032`  
Feedback analytics does not duplicate incoming message data.

`ANL-INV-033`  
Casting analytics does not duplicate Casting Source text.

`ANL-INV-034`  
Opportunity analytics derives from actual StageHistory.

`ANL-INV-035`  
Skipped business stages are never fabricated.

`ANL-INV-036`  
Analytics attribution does not overwrite explicit domain provenance.

`ANL-INV-037`  
Unknown attribution remains valid.

`ANL-INV-038`  
Attribution model is explicitly identified.

`ANL-INV-039`  
UTM/query/referrer ingestion uses allowlists/minimization.

`ANL-INV-040`  
Anonymous session IDs are first-party, pseudonymous and bounded.

`ANL-INV-041`  
Browser fingerprinting is not baseline.

`ANL-INV-042`  
Raw IP is not long-term marketing dimension.

`ANL-INV-043`  
Precise geolocation is not baseline.

`ANL-INV-044`  
Admin, crawler and non-production traffic are distinguishable.

`ANL-INV-045`  
Social preview bots do not count as human engagement.

`ANL-INV-046`  
Share tracking cannot broaden target access.

`ANL-INV-047`  
Share redirect cannot become open redirect.

`ANL-INV-048`  
QR tracking never overrides required canonical QR semantics.

`ANL-INV-049`  
Funnel definitions have explicit steps/cohorts.

`ANL-INV-050`  
Conversion rates expose numerator/denominator/period.

`ANL-INV-051`  
Cross-domain business funnel uses domain provenance where available.

`ANL-INV-052`  
Business funnel uses authoritative domain timestamps.

`ANL-INV-053`  
Analytics does not collapse professional success into opaque single score.

`ANL-INV-054`  
Analytics AI remains advisory.

`ANL-INV-055`  
Analytics AI prefers aggregated data over raw visitor data.

`ANL-INV-056`  
Analytics AI cannot auto-optimize Source/Theme/Opportunity.

`ANL-INV-057`  
Correlation is not represented as causation.

`ANL-INV-058`  
Theme comparison does not become implicit A/B experiment.

`ANL-INV-059`  
Hidden entity IDs never leak through analytics payloads.

`ANL-INV-060`  
Secret/private fields have hard-deny analytics policy.

`ANL-INV-061`  
Analytics URL fields are canonicalized/redacted.

`ANL-INV-062`  
Analytics and Observability remain separate concerns.

`ANL-INV-063`  
Analytics and Audit remain separate concerns.

`ANL-INV-064`  
Raw analytics retention is bounded.

`ANL-INV-065`  
Aggregates may outlive raw events where privacy-safe.

`ANL-INV-066`  
Perpetual visitor identity is not required for historical business funnels.

`ANL-INV-067`  
Privacy deletion locates visitor-linked analytics where applicable.

`ANL-INV-068`  
Anonymous aggregate statistics can remain when no longer attributable.

`ANL-INV-069`  
Analytics reports expose data-quality limitations.

`ANL-INV-070`  
Missing analytics data is not treated as zero.

`ANL-INV-071`  
Aggregates are rebuildable derived artifacts.

`ANL-INV-072`  
Aggregate rebuilds are idempotent and do not double-count.

`ANL-INV-073`  
Accepted raw events are append-oriented.

`ANL-INV-074`  
Small-cohort privacy controls apply where appropriate.

`ANL-INV-075`  
Analytics exports are privileged and privacy-filtered.

`ANL-INV-076`  
Campaign attribution does not own professional publication.

`ANL-INV-077`  
Social credentials never enter Analytics.

`ANL-INV-078`  
Tracking failure never blocks link/contact/PDF navigation.

`ANL-INV-079`  
Server-side artifact/access events are preferred for authoritative delivery measurement.

`ANL-INV-080`  
“Unique people” terminology is not used where only sessions are known.

`ANL-INV-081`  
Business records receive only minimized attribution snapshot.

`ANL-INV-082`  
Marketing reports join cross-domain data by IDs/provenance, not payload duplication.

`ANL-INV-083`  
Sampling never drops critical business conversion events.

`ANL-INV-084`  
Analytics jobs are durable/idempotent.

`ANL-INV-085`  
VOP can repair only derived analytics state.

`ANL-INV-086`  
KPI movement alone is not automatically business incident.

`ANL-INV-087`  
Analytics Event Registry defines purpose, privacy and retention.

`ANL-INV-088`  
No undocumented custom event enters production analytics contract.

`ANL-INV-089`  
Analytics dashboard/search remain Admin-only.

`ANL-INV-090`  
All analytics boundaries are server-enforced.

---

# 362. E2E-ANL-001 — Page view

Human visits public Profile.

One valid `page_view` accepted.

---

# 363. E2E-ANL-002 — Retry dedupe

Same `client_event_id` resent after timeout.

One accepted event.

---

# 364. E2E-ANL-003 — Separate reload

Independent page reload produces separate event but same session where applicable.

---

# 365. E2E-ANL-004 — Bot preview

Social preview crawler loads page.

Marked/excluded from human engagement report.

---

# 366. E2E-ANL-005 — Search crawler

Crawler traffic does not inflate engaged sessions.

---

# 367. E2E-ANL-006 — Admin preview

Authenticated Theme/Profile preview excluded from public audience metrics.

---

# 368. E2E-ANL-007 — Staging

Staging events do not enter production KPI dataset.

---

# 369. E2E-ANL-008 — Section impression

Section crosses configured visibility threshold.

One section view per dedupe rule.

---

# 370. E2E-ANL-009 — Scroll oscillation

Repeated scroll across same section does not create uncontrolled impressions.

---

# 371. E2E-ANL-010 — Portfolio open

Public eligible Portfolio item opened.

Event references item ID, not storage path.

---

# 372. E2E-ANL-011 — Hidden portfolio probe

Browser submits hidden Portfolio ID.

Event rejected without leaking existence.

---

# 373. E2E-ANL-012 — Analytics ordering

One image has highest engagement.

Portfolio display order remains unchanged.

---

# 374. E2E-ANL-013 — Video play

Valid professional video play recorded.

---

# 375. E2E-ANL-014 — Progress milestones

Player emits bounded 25/50/75/100 milestones rather than per-second event flood.

---

# 376. E2E-ANL-015 — Media error

Playback error recorded analytically.

Media Source/processing status is not directly changed by Analytics.

---

# 377. E2E-ANL-016 — Professional link

Click records link ID/type.

Full destination query string absent.

---

# 378. E2E-ANL-017 — Tracking failure

Analytics endpoint unavailable.

Professional link still opens.

---

# 379. E2E-ANL-018 — Contact click

WhatsApp ContactMethod click records method ID/type, not phone.

---

# 380. E2E-ANL-019 — Contact click semantics

Contact click does not create Feedback or Opportunity.

---

# 381. E2E-ANL-020 — Questionnaire revision

Questionnaire R7 viewed/downloaded.

Analytics binds event to R7.

---

# 382. E2E-ANL-021 — PDF download

Download event succeeds.

No CastingMaterial SUBMITTED status is created.

---

# 383. E2E-ANL-022 — PDF tracking failure

PDF still downloads when analytics unavailable.

---

# 384. E2E-ANL-023 — QR canonical

QR requiring exact external canonical URL remains unchanged even though scan cannot be directly tracked.

---

# 385. E2E-ANL-024 — Builder start

Public Builder session starts.

Allowed analytics event stored without hidden available-item payload.

---

# 386. E2E-ANL-025 — Builder generated

Successful GenerationSnapshot produces `builder_generated` after success.

---

# 387. E2E-ANL-026 — Builder failure

Failed generation produces failure event, not success.

---

# 388. E2E-ANL-027 — Feedback commit

Successful Feedback persistence emits `feedback_submitted` after commit.

---

# 389. E2E-ANL-028 — Feedback DB failure

No successful feedback conversion analytics event.

---

# 390. E2E-ANL-029 — Feedback privacy

Analytics event contains type/source context but not message/email/phone.

---

# 391. E2E-ANL-030 — Casting create

Casting domain commit produces analytics event without source text.

---

# 392. E2E-ANL-031 — Opportunity stage

Committed Opportunity transition generates correct analytics stage event.

---

# 393. E2E-ANL-032 — Client fake Booked

Browser submits `opportunity_booked`.

Rejected.

---

# 394. E2E-ANL-033 — Actual Booked

Domain transitions Opportunity to BOOKED.

Server analytics event generated.

---

# 395. E2E-ANL-034 — Skipped stages

QUALIFIED → OFFER.

Analytics funnel does not report Materials/Audition/Callback as completed.

---

# 396. E2E-ANL-035 — Attribution UTM

Allowed UTM values captured with bounded sanitization.

---

# 397. E2E-ANL-036 — Arbitrary query

Secret query parameter outside allowlist not stored.

---

# 398. E2E-ANL-037 — Referrer

Normalized source/domain stored.

Sensitive query/path removed.

---

# 399. E2E-ANL-038 — Unknown attribution

Visitor with no usable source remains UNKNOWN/DIRECT according to explicit model, without invented campaign.

---

# 400. E2E-ANL-039 — Explicit provenance precedence

Opportunity provenance says source Casting from Feedback.

Heuristic last-touch does not overwrite domain provenance.

---

# 401. E2E-ANL-040 — Session privacy

Random first-party analytics session exists without fingerprinting.

---

# 402. E2E-ANL-041 — No raw IP retention

Raw IP absent from long-lived marketing event data according to policy.

---

# 403. E2E-ANL-042 — Share link

Admin creates ShareLink to public Questionnaire.

Open records share event and redirects to stored target.

---

# 404. E2E-ANL-043 — Open redirect attack

Attacker adds arbitrary destination query to ShareLink.

Server ignores/rejects; stored target used.

---

# 405. E2E-ANL-044 — Revoked share

Revoked ShareLink no longer grants intended share access.

Historical events remain.

---

# 406. E2E-ANL-045 — Private token

Private target token is not copied into analytics event dimensions.

---

# 407. E2E-ANL-046 — Funnel cohort

Dashboard shows explicit numerator/denominator/date range for Feedback conversion.

---

# 408. E2E-ANL-047 — Missing period

Analytics outage period shown as partial/unavailable, not zero.

---

# 409. E2E-ANL-048 — Aggregate rebuild

Daily aggregation job rerun.

Counts unchanged; no double count.

---

# 410. E2E-ANL-049 — Raw retention

Expired raw public events removed according to policy.

Longer-lived privacy-safe aggregate remains.

---

# 411. E2E-ANL-050 — Privacy deletion

Visitor-linked pseudonymous data is removed/anonymized where required.

Non-attributable aggregate remains valid.

---

# 412. E2E-ANL-051 — Analytics AI

AI summarizes aggregate trends.

No raw Feedback body provided.

---

# 413. E2E-ANL-052 — AI advisory boundary

AI suggests using Showreel more prominently.

No content order/theme change occurs automatically.

---

# 414. E2E-ANL-053 — Correlation

Theme R2 has better engagement than R1.

Dashboard does not label it causal A/B result without experiment.

---

# 415. E2E-ANL-054 — Small cohort

Tiny sample receives data-quality/sample warning.

---

# 416. E2E-ANL-055 — Public analytics read

Anonymous user attempts dashboard/query endpoint.

Denied.

---

# 417. E2E-ANL-056 — Public ingestion write-only

Public event endpoint accepts allowed event but cannot enumerate existing analytics.

---

# 418. E2E-ANL-057 — Cross-profile

Admin scoped to another profile cannot read target profile analytics.

---

# 419. E2E-ANL-058 — Analytics export

Authorized Admin exports aggregate report.

Privacy-denied raw fields absent.

---

# 420. E2E-ANL-059 — VOP repair

Aggregate generation stale.

VOP triggers safe idempotent rebuild.

No Source domain changes.

---

# 421. E2E-ANL-060 — Analytics pipeline failure

Public Profile, Questionnaire, Feedback and Opportunity workflows continue operating.

---

# 422. Architecture diagram

```text id="anl-diagram-001"
PUBLIC CLIENT EVENTS             BUSINESS DOMAIN EVENTS
        │                                 │
        ▼                                 ▼
 Client Validation                 Domain Commit
        │                                 │
        ▼                                 ▼
 Analytics Ingestion                Outbox Mapping
        └──────────────┬──────────────────┘
                       ▼
              Validated Event Store
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
          Sessions           Attribution
             │                   │
             └─────────┬─────────┘
                       ▼
                  Aggregation
                       │
                       ▼
                Analytics Read Model
                       │
                       ▼
                  Admin Dashboard
```

---

# 423. Authority diagram

```text id="anl-diagram-002"
Analytics
   │
   ├── Observe
   ├── Aggregate
   ├── Compare
   ├── Attribute
   └── Recommend
          │
          ▼
    HUMAN DECISION

Analytics has no direct path to:
Profile / Portfolio / Theme / Opportunity / Publication
```

---

# 424. Opportunity funnel diagram

```text id="anl-diagram-003"
Feedback
   ↓
Casting
   ↓
Opportunity
   ↓
NEW
   ↓
QUALIFIED
   ↓
(actual path only)
   ↓
AUDITION / CALLBACK / OFFER
   ↓
BOOKED

Analytics reads provenance + StageHistory.
It never creates missing stages.
```

---

# 425. Attribution diagram

```text id="anl-diagram-004"
UTM / Referrer / ShareLink
          │
          ▼
      Session Source
          │
          ▼
      Feedback attribution
          │
          ▼
Explicit domain provenance
          │
          ▼
Casting → Opportunity

Explicit provenance wins over heuristic attribution.
```

---

# 426. Privacy diagram

```text id="anl-diagram-005"
Raw Public Interaction
        ↓
Allowlisted Event Schema
        ↓
PII / Secret Redaction
        ↓
Pseudonymous Session
        ↓
Bounded Raw Retention
        ↓
Aggregated Metrics
```

---

# 427. Share tracking diagram

```text id="anl-diagram-006"
ShareLink ID
    │
    ▼
Server Resolver
    │
    ├── verify active/access scope
    ├── record safe open event
    └── resolve stored destination
             │
             ▼
        Target Resource

No arbitrary destination parameter.
```

---

# 428. QR analytics boundary

```text id="anl-diagram-007"
Exact canonical QR required?
        │
      yes
        ▼
Encode canonical URL directly
        │
        ▼
No forced tracking redirect

Correctness > tracking.
```

---

# 429. Quality gate

Перед implementation должны быть подтверждены:

- [ ] Analytics Event Registry;
- [ ] versioned event schemas;
- [ ] browser/server event authority classification;
- [ ] public event ingestion;
- [ ] batch validation;
- [ ] idempotent `client_event_id`;
- [ ] rate limiting;
- [ ] non-blocking client delivery;
- [ ] Page analytics;
- [ ] Section analytics;
- [ ] Portfolio analytics;
- [ ] Emotional Portfolio/Grid analytics;
- [ ] media milestones;
- [ ] ProfessionalLink analytics;
- [ ] Contact click analytics without raw PII;
- [ ] Questionnaire revision analytics;
- [ ] Builder analytics;
- [ ] Feedback server-side conversion analytics;
- [ ] Casting analytics;
- [ ] Opportunity/StageHistory analytics;
- [ ] explicit funnel definitions;
- [ ] attribution hierarchy;
- [ ] UTM allowlist;
- [ ] referrer normalization;
- [ ] first-/last-touch distinction;
- [ ] session privacy;
- [ ] no fingerprinting baseline;
- [ ] bot/social-preview filtering;
- [ ] Admin/staging exclusion;
- [ ] ShareLink model;
- [ ] open-redirect protection;
- [ ] ShareLink access-scope protection;
- [ ] QR correctness-over-tracking rule;
- [ ] dashboard;
- [ ] data-quality indicators;
- [ ] partial-period handling;
- [ ] privacy/PII hard-deny list;
- [ ] URL/query sanitation;
- [ ] raw event retention;
- [ ] aggregate retention;
- [ ] privacy deletion;
- [ ] aggregate rebuild/idempotency;
- [ ] Admin-only analytics access;
- [ ] cross-profile isolation;
- [ ] export controls;
- [ ] analytics AI advisory-only;
- [ ] VOP derived-state repair boundary;
- [ ] deterministic E2E coverage.

---

# 430. Acceptance criteria

`AC-ANL-001`  
Analytics is read-only relative to all professional/business Source domains.

`AC-ANL-002`  
Production analytics events use explicit versioned schemas.

`AC-ANL-003`  
Every event has declared client/server authority and property allowlist.

`AC-ANL-004`  
Public browser cannot authoritatively emit Feedback/Casting/Opportunity business conversion events.

`AC-ANL-005`  
Business conversion analytics are emitted only after corresponding Source transaction commits.

`AC-ANL-006`  
Public analytics ingestion is bounded, validated, idempotent and non-blocking.

`AC-ANL-007`  
Analytics subsystem failure does not break Profile, links, PDF, Builder, Feedback or Opportunity workflows.

`AC-ANL-008`  
Portfolio engagement never automatically changes ordering, primary state or visibility.

`AC-ANL-009`  
Media analytics use bounded progress milestones rather than uncontrolled event streams.

`AC-ANL-010`  
ProfessionalLink analytics use IDs/types instead of unnecessary full destination URLs.

`AC-ANL-011`  
Contact analytics does not store raw phone/email when entity ID/type is sufficient.

`AC-ANL-012`  
Questionnaire analytics are revision-aware.

`AC-ANL-013`  
Questionnaire PDF download is not treated as casting submission.

`AC-ANL-014`  
Public Builder analytics cannot expose Builder-ineligible items.

`AC-ANL-015`  
Feedback analytics does not duplicate sender/message/attachment data.

`AC-ANL-016`  
Casting analytics does not copy Casting Source.

`AC-ANL-017`  
Opportunity analytics derive from committed StageHistory and preserve actual stage path.

`AC-ANL-018`  
BOOKED analytics event exists only after authoritative Opportunity BOOKED transition.

`AC-ANL-019`  
Heuristic marketing attribution never overrides explicit domain provenance.

`AC-ANL-020`  
UTM/referrer/query parameters are allowlisted/minimized.

`AC-ANL-021`  
Unknown attribution remains a supported state.

`AC-ANL-022`  
First-touch, last-touch and explicit provenance are distinguishable.

`AC-ANL-023`  
Anonymous session identity is first-party, pseudonymous and bounded in retention.

`AC-ANL-024`  
Browser fingerprinting/cross-site tracking are not baseline analytics mechanisms.

`AC-ANL-025`  
Raw IP and exact location are not retained as long-term marketing dimensions baseline.

`AC-ANL-026`  
Bot/crawler/social-preview and Admin traffic can be excluded from human engagement metrics.

`AC-ANL-027`  
Development/staging events cannot pollute production KPIs.

`AC-ANL-028`  
Share tracking cannot broaden access to target resources.

`AC-ANL-029`  
Share redirect uses stored validated destination and cannot become an open redirect.

`AC-ANL-030`  
Private share/access tokens are never copied into analytics dimensions.

`AC-ANL-031`  
QR target correctness has precedence over scan tracking.

`AC-ANL-032`  
Funnels define explicit steps, cohort, numerator, denominator and period.

`AC-ANL-033`  
Cross-domain business funnels use domain provenance and authoritative timestamps where available.

`AC-ANL-034`  
Skipped Opportunity stages are never fabricated for funnel completeness.

`AC-ANL-035`  
Dashboard does not reduce professional performance to a single opaque score.

`AC-ANL-036`  
Analytics AI is optional, aggregate-first and advisory-only.

`AC-ANL-037`  
Analytics AI cannot mutate Source, Theme, Questionnaire or Opportunity.

`AC-ANL-038`  
Analytics does not claim causality from observational correlation.

`AC-ANL-039`  
Theme before/after analytics does not masquerade as controlled A/B experiment.

`AC-ANL-040`  
Hidden/private entity IDs cannot leak through public analytics payloads or errors.

`AC-ANL-041`  
Secrets, private URLs, raw contact data, Feedback text and Admin notes are hard-denied analytics properties.

`AC-ANL-042`  
Analytics URL and referrer data are canonicalized/redacted.

`AC-ANL-043`  
Analytics remains distinct from Observability and Audit.

`AC-ANL-044`  
Raw analytics events use bounded retention.

`AC-ANL-045`  
Privacy-safe aggregates may retain longer than raw visitor events.

`AC-ANL-046`  
Historical business conversion analytics do not require perpetual visitor identity.

`AC-ANL-047`  
Privacy deletion/anonymization discovers attributable analytics data where required.

`AC-ANL-048`  
Analytics dashboard exposes data-quality gaps and does not interpret missing periods as zero.

`AC-ANL-049`  
Aggregate rebuilds are deterministic/idempotent and do not double-count.

`AC-ANL-050`  
Small cohort/sample limitations are visible.

`AC-ANL-051`  
Analytics dashboard and exports are Admin-only and profile-scoped.

`AC-ANL-052`  
Social OAuth/provider credentials never enter Analytics.

`AC-ANL-053`  
Tracking failures never block professional navigation or artifact delivery.

`AC-ANL-054`  
Server-side events are preferred for authoritative artifact/business actions.

`AC-ANL-055`  
Session metrics are not mislabeled as guaranteed unique people.

`AC-ANL-056`  
Feedback/business records receive only minimized attribution snapshots rather than full browsing history.

`AC-ANL-057`  
Cross-domain reports join by IDs/provenance rather than copying private business payloads.

`AC-ANL-058`  
Sampling never removes critical business conversion events.

`AC-ANL-059`  
VOP can repair only rebuildable analytics projections/aggregates.

`AC-ANL-060`  
All privacy, attribution, event authority, bot filtering, funnel and read-only boundaries have deterministic E2E coverage.

---

# 431. Финальная доктрина

> **Marketing & Portfolio Analytics Module является privacy-conscious read-only measurement layer. Он принимает только versioned allowlisted events, разделяет browser interaction events и authoritative server-side business events, фильтрует bots/Admin/non-production traffic, минимизирует URL/referrer/contact/visitor data и строит агрегаты, attribution и funnels поверх уже совершившихся событий. Feedback, Casting и Opportunity conversions учитываются только после commit соответствующего Source, а Opportunity funnel формируется исключительно из фактической StageHistory без выдуманных промежуточных стадий. Share tracking и UTM/referrer attribution не имеют права расширять доступ или заменять explicit domain provenance; QR correctness имеет приоритет над tracking. Analytics AI может объяснять агрегированные тенденции и предлагать Human actions, но не может автоматически менять Portfolio ordering, Visibility, Questionnaire, Theme, Opportunity stage или professional content. Raw visitor events имеют ограниченный retention, а долгосрочная аналитика должна по возможности опираться на privacy-safe aggregates и domain provenance вместо постоянной идентификации посетителя.**