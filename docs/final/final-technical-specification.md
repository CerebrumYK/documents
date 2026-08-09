# FINAL TECHNICAL SPECIFICATION
## DOC-233 — consolidated technical specification for the actress website platform

**Статус:** Canonical / Final Consolidation  
**Purpose:** consolidated implementation contract for Codex, architecture review, development and acceptance.

---

# 1. Product purpose

The product is a professional digital platform for an actress combining:

- multilingual public actor profile;
- professional photo/video/audio portfolio;
- Projects and Roles;
- Training;
- Skills and Languages;
- Achievements;
- professional media links;
- structured Contacts;
- professional actor Questionnaires in HTML/PDF;
- Public Questionnaire Builder for casting specialists;
- Emotional Portfolio and Emotional Grid;
- private Casting workflow and AI-assisted analysis;
- professional Feedback/Inquiry Inbox;
- Opportunity Pipeline;
- Admin Notification Center;
- BB Assistant for professional writing;
- VOP for operational/content health;
- Site Themes;
- privacy-conscious Marketing Analytics;
- Help Center and Support Tickets;
- Instagram/TikTok Social Publishing;
- secure administration, migrations, testing and operations.

The system is not merely a public website. It is a professional-content and opportunity-management platform in which public publication, private professional workflows and external-provider automation must remain explicitly separated.

---

# 2. Technology baseline

Canonical target architecture:

```text
Next.js
TypeScript
PostgreSQL
Modular Monolith
Background Worker
Nginx / HTTPS
filesystem-backed managed media storage
```

Production baseline:

```text
persistent application data root: /data/kate-actor
application port: 3336
reverse proxy/public ingress: Nginx HTTPS
```

Supabase is not part of the canonical architecture.

External integrations may include:

```text
AI provider(s)
Email provider
official WhatsApp Business provider
Instagram OAuth/publishing API
TikTok OAuth/publishing API
```

External providers are optional/degradable dependencies and never become authoritative Source.

---

# 3. Core architecture doctrine

Every data object belongs to one primary architectural class:

```text
Source / Master
Configuration
Revision / Snapshot
Derived Artifact
Projection
Operational
AI Output
Audit
```

Examples:

```text
ActorSkill                         → Source
QuestionnaireDraft                 → Configuration
QuestionnaireRevision              → immutable Revision
QR/PDF/thumbnail                    → Derived Artifact
PublicProfileDTO                    → Projection
NotificationDelivery               → Operational
CastingAnalysisRevision             → AI Output
AuditEvent                          → Audit
```

## 3.1 One owner per fact

Every professional fact has one authoritative owning domain.

Other modules reference, project or snapshot that fact rather than creating mutable copies.

## 3.2 Source vs presentation

Public/Admin/Builder UI consumes projections. UI state never becomes authoritative Source merely because it is visible or editable.

## 3.3 Save vs Publish

```text
Save != Publish
Apply != Publish
Generate != Apply
Schedule != Publish result
Archive != Delete
Revoke != Delete
```

## 3.4 Historical integrity

Current professional Source may evolve. Published/generated historical representations that require reproducibility are immutable:

```text
QuestionnaireRevision
BuilderGenerationSnapshot
CastingAnalysisRevision
BBSourceSnapshot / Draft history
SocialPublishSnapshot
historical QR target semantics
```

Current Source changes never silently rewrite these records.

---

# 4. Domain relationships

Relationship classes:

```text
OWNERSHIP
REFERENCE
PROVENANCE
CONFIGURATION
DERIVED_FROM
OPERATIONAL_LINK
```

Aggregate ownership does not automatically imply cascade hard delete.

Cross-entity relations owned by one actress profile must be same-profile unless an explicitly external source is being referenced.

Media is normally referenced, not duplicated.

---

# 5. Profile

The Profile is authoritative professional identity/source for the actress.

It supports multilingual public information and feeds public profile, Questionnaire, BB Assistant and other projections.

Rules:

- only confirmed professional facts become Source;
- public visibility is explicit;
- biography/public summaries may be AI-assisted only through BB Draft + Human Apply;
- Hero/video/profile presentation references Media/ProfessionalLink Source rather than copying URL/facts;
- changes use validation, versioning and audit according to importance.

---

# 6. Media Library

Supported managed media classes:

```text
IMAGE
VIDEO
AUDIO
DOCUMENT
```

## 6.1 Originals

Original uploaded bytes are immutable.

They must never be automatically:

- beauty-filtered;
- retouched;
- generatively altered;
- overwritten by crop/resize;
- replaced by a derivative.

## 6.2 Derivatives

System may produce:

- thumbnails;
- responsive web images;
- posters;
- transcoded video/audio;
- PDF/social-specific derivatives.

Derivatives are rebuildable from the original/configuration.

## 6.3 Storage

Filesystem/storage locator is never a public URL.

Public or casting-safe access uses an application-controlled resolver with appropriate access class.

---

# 7. Main Portfolio

The Main Portfolio is the authoritative source for standard actor photographs.

Supported categories include, at minimum:

```text
FULL_BODY
CLOSE_UP
WAIST
PROFILE
THREE_QUARTER
STAGED / contextual categories
```

Questionnaire mandatory:

```text
FULL_BODY
CLOSE_UP
```

must come only from eligible Main Portfolio items.

No Emotional, Project or AI-generated substitute is allowed.

Ordering/highlight/visibility are Human-controlled.

The system preserves original source frame/aspect ratio; contextual crop is stored separately when explicitly supported.

---

# 8. Emotional Portfolio

Emotional Portfolio is independent from Projects.

Requirements:

- shooting date is mandatory;
- source photos immutable;
- captions/public state supported;
- full portfolio can have a public page;
- Questionnaire does not embed all emotional source photos.

Questionnaire representation:

```text
finalized Emotional Grid composite
+ shooting date
+ clickable link to full Emotional Portfolio
+ optional verified QR
```

---

# 9. Emotional Grid

Supported grid sizes are exactly:

```text
1x2, 1x3, 1x4,
2x2, 2x3, 2x4,
3x2, 3x3, 3x4,
4x2, 4x3, 4x4
```

Selected image count must equal cell count exactly.

Per cell:

```text
crop
pan
scale
rotation
Human confirmation
```

Target composition: face occupies at least approximately 90% of useful cell area according to workflow validation/Human confirmation.

Appearance/generative modification is prohibited.

Every cell must be Human-confirmed before finalization.

Outputs:

```text
master composite
web derivative
PDF derivative
thumbnail
```

Multiple grids may exist; primary grid is Human-selected.

---

# 10. Projects and Roles

Project and Role are professional Source records.

They can contain:

- title;
- date/year;
- production/director metadata;
- Role relation;
- contextual media;
- links;
- publication configuration.

Role media context does not redefine MediaAsset globally.

Educational entries belong to Training where semantics are educational/training rather than project credit.

---

# 11. Training

Training is distinct from:

```text
Project
Skill
Achievement
```

It stores confirmed course/institution/teacher/period information.

Training may be evidence for Human assessment but never automatically creates or raises a Skill level.

---

# 12. Skills

Canonical model:

```text
SkillDefinition
→ ActorSkill
```

ActorSkill level is integer exactly:

```text
1, 2, 3, 4, 5
```

No percentages, decimals or free-form canonical levels.

Human-confirmed level is authoritative.

Projects, Training, AI or Analytics cannot auto-calculate or raise it.

Independent visibility:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 13. Languages

Canonical proficiency:

```text
NATIVE
A1
A2
B1
B2
C1
C2
```

Native is separate from C2.

The system supports multiple Native languages.

Proficiency cannot be inferred automatically from:

- nationality;
- country;
- training;
- UI locale;
- speech AI.

Site language and actor language proficiency are independent concerns.

---

# 14. Achievements and Awards

Achievement is independent professional Source.

Recognition statuses include:

```text
WINNER
NOMINATED
FINALIST
SHORTLISTED
OFFICIAL_SELECTION
SPECIAL_MENTION
HONORABLE_MENTION
RECIPIENT
RECOGNIZED
OTHER_CONFIRMED
```

These statuses have distinct semantics.

No automatic upward semantic conversion is allowed.

A Project official selection/award is not automatically a personal actress award.

Evidence may be attached and is private by default unless explicitly published.

BB/SEO/AI wording `award-winning` requires confirmed award/winner evidence supporting the personal claim being made.

---

# 15. Professional Media and Links

`ProfessionalLink` and `MediaAsset` are distinct.

External link editor has exact columns:

```text
№ | Описание | URL
```

The visible number is ordering-derived, not entity identity.

Supported professional links/media may include:

- Video Intro;
- Showreel;
- video/audio samples;
- professional external profiles/resources.

URLs use safe canonicalization without changing destination semantics.

Unsafe schemes, embedded credentials, Admin/internal/private destinations are rejected for public targets.

Link health is Operational/derived state and never rewrites Source automatically.

Server-side link health checks are SSRF-safe.

---

# 16. Contacts

Canonical split:

```text
ContactEntry = who
ContactMethod = how
```

Roles may include:

```text
ACTRESS
PARENT
MOTHER
FATHER
GUARDIAN
AGENT
MANAGER
REPRESENTATIVE
AGENCY
OTHER
```

Methods may include phone/email/WhatsApp/Telegram/social/website.

Every method independently controls Public/QNR/Builder eligibility.

Hidden values are removed before serialization.

These are separate domains:

```text
Public Contact
Notification Recipient
Feedback Sender
Admin Identity
Social OAuth Credential
```

No automatic coupling is permitted.

---

# 17. Questionnaires

Supported types:

```text
COMPACT
EXTENDED
CASTING
CUSTOM
```

Canonical lifecycle:

```text
QuestionnaireDefinition
→ mutable Draft/Configuration
→ server revalidation
→ Publish
→ immutable QuestionnaireRevision
→ HTML/PDF Derived Artifacts
```

Mandatory minimum:

- actress identification;
- eligible Main Portfolio CLOSE_UP;
- eligible Main Portfolio FULL_BODY;
- official Profile URL.

Exactly one primary public Questionnaire is permitted per relevant profile/scope.

Historical revisions are immutable.

---

# 18. PDF

PDF is rendered from normalized DocumentModel derived from immutable revision/snapshot.

Requirements:

- selectable text;
- actual hyperlink annotations;
- readable professional layout;
- safe public/casting URLs;
- no private/Admin/filesystem targets;
- print-safe QR when enabled;
- no screenshot-only document baseline.

---

# 19. QR Links

QR is a Derived Artifact.

For one frozen item:

```text
HTML hyperlink target
=
PDF hyperlink target
=
QR encoded target
```

Production QR requires exact decode verification:

```text
decode(QR) === expected canonical URL
```

No fuzzy/redirect equivalence.

Print requirements:

```text
quiet zone >= 4 modules
module size >= 0.4 mm baseline
overall size >= 20 mm baseline
vector SVG preferred for PDF
```

QR is supplementary; clickable link remains available digitally.

Admin/filesystem/raw-storage/private-network target is forbidden.

Historical Questionnaire/Builder snapshot freezes QR target semantics.

---

# 20. Public Questionnaire Builder

Builder is a public self-service tool for casting specialists.

No mandatory registration baseline.

Templates:

```text
QUICK
STANDARD
EXTENDED
CASTING
```

Browser receives only Builder Eligibility Projection.

Builder cannot edit Source values.

Mandatory identification/CLOSE_UP/FULL_BODY/Profile URL remains.

Temporary session is opaque, versioned and expiring.

Preview uses current eligible source + temporary configuration.

Generate:

1. reloads/revalidates every item server-side;
2. rejects revoked/ineligible content;
3. creates immutable GenerationSnapshot;
4. produces artifact from snapshot;
5. uses scoped/public access policy as appropriate.

Rate limiting/anti-abuse apply.

---

# 21. Castings

Casting is private professional opportunity/request data, distinct from Project and Opportunity Pipeline.

It preserves original source/provenance including text/images/documents/external references supplied to the system.

Canonical requirements states:

```text
PROPOSED
CONFIRMED
REJECTED
SUPERSEDED
```

Only Human-confirmed requirements are authoritative for deterministic matching.

CastingMaterial distinguishes prepared/generated/submitted state.

---

# 22. Casting AI Analysis

AI analysis begins only by explicit Human request baseline.

Flow:

```text
authorized Casting Source
→ immutable minimized SourceSnapshot
→ AI request
→ structured validated output
→ immutable CastingAnalysisRevision
→ Human review
→ explicit requirement confirmation
```

Missing source information must remain:

```text
Не указано
```

AI factual extraction/recommendation includes evidence.

Canonical match statuses:

```text
MATCH
PARTIAL_MATCH
MISMATCH
NO_CONFIRMED_DATA
REQUIRES_HUMAN_INTERPRETATION
NOT_APPLICABLE
```

AI can create/recommend a Questionnaire Draft, never publish/send/change Profile/Casting/Opportunity directly.

Prompt injection in casting text is data, not tool authority.

---

# 23. Feedback & Inquiries

Types:

```text
CASTING_INVITATION
ROLE
COLLABORATION
QUESTIONNAIRE
MATERIALS
QUESTION
COMMENT
OTHER
```

Canonical persistence flow:

```text
BEGIN
  insert Feedback
  bind validated private attachments
  append Audit
  append Outbox
COMMIT
→ notification processing
```

Provider failure after commit does not convert successful Feedback into failure or lose it.

Read state and workflow state are separate.

Suggested workflow:

```text
NEW
IN_REVIEW
ACTION_REQUIRED
WAITING_EXTERNAL
RESOLVED
CLOSED
ARCHIVED
```

Assignee, internal notes and next action are private operational metadata.

Public submission is write-only baseline; returned UUID is not a read credential.

`CreateCastingFromInquiry` is explicit Human idempotent action preserving bidirectional provenance.

---

# 24. Notifications

Fundamental separation:

```text
Domain Event
≠ NotificationIntent
≠ In-App Notification
≠ NotificationDelivery
≠ Provider Message
```

Channels baseline:

```text
IN_APP
EMAIL
WHATSAPP
```

Notification recipients/endpoints/preferences are operational configuration separate from Contacts.

External channel delivery respects:

- enabled preference;
- timezone;
- quiet hours;
- provider availability;
- retry classification;
- idempotency.

Provider accepted does not mean delivered unless provider confirms delivery.

Notification read/unread does not alter Feedback/Casting/Opportunity state.

WhatsApp outbound uses official/approved Business API/provider only.

---

# 25. Opportunity Pipeline

Opportunity is distinct from Casting and Project.

It has:

- current stage;
- append-only StageHistory;
- owner/responsible person;
- priority;
- next action/date;
- provenance;
- outcome.

Stage transitions are Human-controlled and validated.

`BOOKED` is authoritative only after explicit authorized Human transition.

`BOOKED` does not automatically create a Project/Role credit. Conversion is explicit Human action preserving provenance.

---

# 26. Site Themes

Theme is structured configuration/tokens, not arbitrary executable CSS/JS.

Capabilities:

- tokens;
- hero/decor configuration;
- responsive preview;
- manual editing;
- locks;
- temporary theme;
- publish/activate;
- rollback.

Accessibility/contrast validation is mandatory before activation.

Theme AI may create structured editable Draft only.

Theme never changes actress appearance/content or access rules.

---

# 27. BB Assistant

Supported professional writing tasks include:

- Biography;
- Profile summary;
- Project/Role/Training descriptions;
- Professional Media descriptions;
- Questionnaire text;
- Cover Letter;
- Casting response;
- Feedback reply;
- Opportunity follow-up;
- Social caption;
- custom professional text.

Canonical flow:

```text
Confirmed Source + explicit Human context
→ immutable BBSourceSnapshot
→ AI Draft
→ schema/factual validation
→ Human review/edit
→ explicit Apply
→ target Draft field
→ separate Publish/Send
```

BB cannot invent:

- Project/Role/Award;
- Skill/Language level;
- representation;
- availability;
- commercial terms;
- URL.

Generate != Apply != Publish != Send.

---

# 28. Virtual Portfolio Operator (VOP)

VOP is operational/content-health assistant.

Flow:

```text
Observe
→ Detect
→ Explain
→ Recommend
→ Safe deterministic action OR Human action
```

Finding has:

- stable fingerprint;
- evidence;
- severity;
- lifecycle;
- source entity context.

Safe automated actions may include:

- rebuild derived media;
- regenerate PDF/QR;
- reindex Search;
- invalidate cache;
- recompute readiness;
- retry declared idempotent technical job.

Hard-denied autonomous actions include:

- changing Profile fact;
- Skill/Language level;
- visibility;
- Questionnaire publish;
- Casting requirement confirmation;
- Feedback reply;
- Opportunity transition/BOOKED;
- Theme activation;
- Social publish.

AI explanation is optional/non-authoritative.

---

# 29. Marketing Analytics

Analytics is privacy-conscious and read-only.

Event model is versioned and declares client/server authority.

Client events may represent interaction. Business outcomes such as Feedback commit/Opportunity Booked come only from server/domain events.

Analytics includes:

- page/section engagement;
- Portfolio/Media interaction;
- links/contact clicks;
- Questionnaire/Builder usage;
- ShareLink attribution;
- Feedback/Casting/Opportunity funnel;
- UTM/referrer attribution;
- bot/internal traffic classification;
- data-quality indicators.

Explicit domain provenance outranks heuristic attribution.

Raw PII/private messages/tokens are excluded.

Analytics never automatically changes content, ordering, Theme, visibility or Opportunity state.

---

# 30. Help Center & Support Tickets

Help Center provides Russian Admin guidance.

HelpTicket is technical/operational support, distinct from professional Feedback/Casting/Opportunity.

Ticket supports:

- category;
- priority;
- status lifecycle;
- assignment;
- SLA metadata;
- public replies;
- internal notes;
- private validated attachments;
- links to affected technical/domain object.

Internal notes are filtered server-side.

Closing a Help Ticket never changes the linked professional entity automatically.

---

# 31. Social Publishing

Baseline providers:

```text
Instagram
TikTok
```

Provider adapters hide API-specific behavior.

OAuth requirements:

- state validation;
- PKCE where applicable;
- fixed redirect URI;
- least-privilege scopes;
- server-side encrypted credential storage;
- refresh/revoke lifecycle.

SocialPostDraft is mutable and references existing eligible media.

Website-public media is not automatically social-publishing eligible. Distribution rights/embargo are independently validated.

Publishing flow:

```text
Draft
→ validation
→ Human approval + fingerprint
→ Publish Now OR exact Schedule
→ immutable SocialPublishSnapshot
→ durable worker
→ SocialPublishAttempt
→ provider
→ webhook/poll reconciliation
```

Editing approved content invalidates approval.

Provider `ACCEPTED` != `PUBLISHED`.

Timeout that may have produced side effect creates `UNKNOWN_OUTCOME`; reconciliation occurs before retry.

AI/BB/VOP/Analytics cannot autonomously publish or schedule.

---

# 32. Security architecture

Trust zones include:

```text
Public Browser
Admin Browser
Builder Session
Token-scoped Recipient
Application Server
Background Worker
PostgreSQL
Private Media Storage
External Providers
Provider Webhooks
Backups
```

Key rules:

- secure revocable Admin sessions;
- authentication != authorization;
- server-side permission/ownership/lifecycle checks;
- hidden data removed before serialization;
- CSRF for cookie-auth mutations;
- XSS/rich-text escaping/sanitization;
- CSP/security headers;
- SSRF-safe server URL fetching;
- upload type/magic/size/quarantine controls;
- secret isolation/rotation;
- token-scoped high-entropy revocable access;
- rate limiting for public abuse surfaces;
- Audit and secret-safe logs;
- public/token/Admin cache/search separation.

---

# 33. PostgreSQL implementation

PostgreSQL is authoritative for structured data.

Baseline conventions:

```text
UUID PKs
FK constraints
explicit lifecycle/status
version for optimistic concurrency
created_at/updated_at
partial unique indexes for “one primary” constraints
CHECK constraints for structural enums/ranges
```

Important tables conceptually include:

- Source domain tables;
- revision/snapshot tables;
- audit_events;
- outbox_events;
- jobs;
- idempotency records;
- notification deliveries/attempts;
- social publish attempts;
- migration maps.

JSONB is limited to genuinely flexible/versioned metadata, not used to hide all structured business data.

Binary media lives outside relational rows baseline.

---

# 34. Concurrency and idempotency

High-impact mutable commands use optimistic version or equivalent.

Retryable public/provider actions use idempotency.

Rules:

```text
same key + same semantic request → same logical result
same key + changed semantic request → conflict
```

Examples requiring protection:

- Feedback submission;
- Builder Generate;
- CreateCastingFromInquiry;
- Questionnaire publish;
- Opportunity transition;
- Notification delivery;
- Social publishing;
- derived artifact generation.

---

# 35. Background jobs and Outbox

Domain transaction writes Outbox atomically with Source.

Worker processes typed/versioned durable jobs at least once.

Every handler declares:

```text
idempotency/retry class
max attempts
backoff
current-state revalidation
unknown-outcome reconciliation requirements
```

Job payload never contains raw secret or executable function/shell code.

---

# 36. Search and Cache

Search and cache are Derived Projections.

Public/Admin/Builder surfaces remain separate.

Current hydration/invalidation prevents stale hidden data from being returned.

Loss of cache/index must be recoverable from authoritative Source.

---

# 37. Migrations

Migration principles:

- inventory before transform;
- backup before destructive step;
- preserve originals/provenance;
- exact mappings only;
- ambiguity → Human review;
- idempotent/restartable;
- no automatic provider side effects;
- no fabricated history;
- no public-default fallback for unknown visibility.

Media originals are checksum-preserved.

Migration success requires counts, referential/domain/access validation, representative E2E and rollback evidence—not script exit code alone.

---

# 38. Non-functional requirements

## Performance

Target good Core Web Vitals class for major public pages, with representative targets:

```text
LCP <= 2.5s target
INP <= 200ms target
CLS <= 0.1 target
```

Heavy work is asynchronous. Responsive media derivatives, pagination, indexes and bounded worker concurrency are required.

## Accessibility

Applicable UI targets WCAG 2.2 AA.

Core workflows must be keyboard accessible; focus visible; forms labeled; errors associated; contrast validated; reduced motion respected; QR never sole access method.

## Compatibility

Support current/recent Chrome/Chromium, Safari, Firefox, Edge, iOS Safari and Android Chrome according to project browserslist/test matrix.

## SEO

SEO uses only public confirmed projection. Token/Admin/private content is excluded from sitemap/indexing and remains access-protected independently.

## Analytics quality

Missing data != zero; sessions != guaranteed unique people; click != business outcome; attribution model/cohort must be explicit.

---

# 39. Testing

Test pyramid:

```text
Unit
Integration
E2E
Security
Performance
Accessibility
Visual Regression
Migration
```

Critical tests include negative/prohibited paths.

Required examples:

- hidden field absent from raw public response;
- cross-profile forged ID denied;
- Feedback durable during notification outage;
- current Source changes do not rewrite historical QNR;
- QR final PDF decode exact target;
- duplicate Builder/Feedback/Social requests do not duplicate effects;
- unknown social provider outcome reconciles before retry;
- AI/VOP cannot publish/change business facts;
- migration ambiguous values remain review-required;
- restore does not replay external side effects.

Release gate DOC-179 is blocking for security/privacy/data-loss issues.

---

# 40. Operations

Production startup validates:

- PostgreSQL connectivity/schema;
- `/data/kate-actor` mount/permissions/free space;
- required config;
- application readiness.

Optional providers report degraded state without taking core Profile offline.

Nginx provides HTTPS/reverse proxy; port 3336 is not baseline public ingress.

Monitoring covers:

- app requests/latency/errors;
- PostgreSQL;
- jobs/outbox;
- storage/media/PDF/QR;
- AI/notification/social providers;
- security signals;
- backups.

Backups cover PostgreSQL and immutable required media, with periodic isolated restore testing.

Restore holds provider/outbound workers until reconciliation.

---

# 41. Implementation sequence

Mandatory dependency-aware order:

```text
0 repository audit
1 security/config/DB/audit/outbox/jobs foundations
2 core Profile/Media/Portfolio/Projects/Training/Skills/Languages/Contacts/Achievements
3 Emotional Portfolio/Grid
4 Professional Links/media delivery
5 Questionnaires/PDF/QR
6 Public Builder
7 Feedback/Notifications
8 Castings/Opportunity
9 Casting AI
10 BB Assistant
11 Themes + Theme AI
12 VOP
13 Help Center/Tickets
14 Social Publishing
15 Analytics
16 operational/NFR hardening
17 migration/cutover
18 final implementation audit
```

Manual deterministic workflow precedes AI/provider automation.

---

# 42. GitHub and Codex

Implementation work uses scoped branches/issues/PRs referencing:

```text
DOC-*
FR-*
AC-*
```

Every substantial PR reports:

- functional scope;
- domain ownership;
- migration;
- security/privacy;
- tests;
- provider/side-effect behavior;
- operations;
- traceability.

Codex must inspect actual repository before changes and cannot invent implementation paths or mark unverified requirements complete.

---

# 43. Release acceptance

A production release is acceptable only when:

- build/typecheck blocking tests pass;
- critical functional E2E pass;
- security/visibility tests pass;
- migrations validated/backed up;
- accessibility blockers zero;
- affected performance/compatibility/SEO gates pass;
- PDF/QR semantic tests pass when changed;
- backup/restore posture acceptable;
- provider/job workflows observable;
- docs/traceability updated;
- no known P1/P2 privacy/data-integrity defect.

---

# 44. Current documentation-repository limitation

This consolidated specification captures the target architecture and requirements, including semantics already authored earlier in the project.

However the current `CerebrumYK/documents` repository began empty during this operation. DOC-140+ and finalization files are present, while earlier detailed DOC-001…134 remain to be imported if the repository is expected to contain the complete historical document set physically.

This limitation does not authorize substituting the detailed earlier domain docs with invented approximations during implementation. Use DOC-230/231 governance and import the canonical earlier documents.

---

# 45. Final acceptance criteria

`AC-FTS-001` Technology baseline is Next.js/TypeScript/PostgreSQL with Modular Monolith + Worker.  
`AC-FTS-002` Source/Revision/Derived/Projection/Operational/AI/Audit classes remain distinct.  
`AC-FTS-003` One domain owns every professional fact.  
`AC-FTS-004` Public/Admin/Builder/token surfaces receive only authorized projections.  
`AC-FTS-005` Media originals remain immutable and public delivery never exposes storage paths.  
`AC-FTS-006` Questionnaire/Builder/PDF/QR preserve exact immutable snapshot semantics.  
`AC-FTS-007` AI/BB/VOP/Analytics cannot acquire autonomous professional/business authority.  
`AC-FTS-008` Feedback is committed before notifications and provider failures never erase business Source.  
`AC-FTS-009` Opportunity/Theme/Social publication high-impact actions remain Human-controlled.  
`AC-FTS-010` External side effects use durable jobs, idempotency and reconciliation.  
`AC-FTS-011` Security, accessibility, performance, compatibility, SEO, migration and recovery are release requirements.  
`AC-FTS-012` Implementation proceeds in dependency-aware phases with migrations/tests/docs included.  
`AC-FTS-013` GitHub/Codex trace actual implementation evidence rather than documentation assumptions.  
`AC-FTS-014` Earlier missing repository documents are imported/superseded explicitly before claiming a physically complete standalone documentation set.

---

# 46. Финальная доктрина

> **The actress website is a professional-source platform with a public portfolio surface, private opportunity workflow and carefully bounded automation. PostgreSQL and immutable media preserve truth; revisions preserve history; projections preserve privacy; workers preserve durability; Human confirmation preserves professional authority; and AI/providers remain assistants and delivery mechanisms rather than autonomous owners of facts, publication or career decisions.**
