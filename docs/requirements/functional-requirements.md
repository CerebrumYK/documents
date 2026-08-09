# FUNCTIONAL REQUIREMENTS
## DOC-180 — master catalog of functional requirements

**Статус:** Canonical / Required

---

## 1. Назначение

Документ консолидирует обязательные функциональные требования проекта в единый `FR-*` catalog. Детальная семантика остаётся в соответствующих domain/module/architecture documents; этот файл является master checklist для implementation/traceability.

> **A requirement is satisfied only when its owning domain semantics, security boundaries, lifecycle and acceptance tests are implemented—not merely when a similarly named UI control exists.**

---

# 2. Profile

`FR-PRO-001` Система хранит professional Actor Profile как authoritative Source.  
`FR-PRO-002` Profile поддерживает multilingual public content.  
`FR-PRO-003` Draft/current/published presentation semantics разделены where applicable.  
`FR-PRO-004` Hidden Profile facts are absent from public serialization.  
`FR-PRO-005` Profile changes use validation/versioning/audit appropriate to field.  
`FR-PRO-006` Hero/public projection consumes authoritative Profile/Media/Link sources rather than duplicate shadow fields.

# 3. Media Library

`FR-MED-001` Images, video, audio and documents are managed as MediaAsset with immutable originals.  
`FR-MED-002` Upload validates type/size/content and stores private until ready.  
`FR-MED-003` Derived thumbnails/web/video/poster artifacts are rebuildable.  
`FR-MED-004` Public media uses stable application-controlled URLs, never filesystem paths.  
`FR-MED-005` Access class/private attachments are server-enforced.  
`FR-MED-006` Media processing cannot alter actress appearance generatively/retouch automatically.

# 4. Main Portfolio

`FR-POR-001` Main Portfolio supports controlled photo types including FULL_BODY and CLOSE_UP.  
`FR-POR-002` Questionnaire mandatory Full Body and Close-Up can only come from eligible Main Portfolio.  
`FR-POR-003` Public/Admin ordering/visibility are Human-controlled.  
`FR-POR-004` Source image uses original aspect ratio/full frame policy unless explicit contextual crop exists.  
`FR-POR-005` Project/Role media context does not overwrite global MediaAsset metadata.

# 5. Emotional Portfolio

`FR-EMO-001` Emotional Portfolio is standalone domain, not Project.  
`FR-EMO-002` Shooting date is mandatory.  
`FR-EMO-003` Source photos remain immutable.  
`FR-EMO-004` Public Emotional Portfolio can contain source photos/captions according to visibility.  
`FR-EMO-005` Questionnaire emotional block uses finalized composite + shooting date + link to full Emotional Portfolio, not individual emotional photos.

# 6. Emotional Grid

`FR-EGR-001` Supported sizes are exactly `1x2,1x3,1x4,2x2,2x3,2x4,3x2,3x3,3x4,4x2,4x3,4x4`.  
`FR-EGR-002` Selected photo count exactly matches cell count.  
`FR-EGR-003` Each cell supports crop/pan/scale/rotation as contextual derived presentation.  
`FR-EGR-004` Target face useful-area criterion is >=90% according to defined workflow.  
`FR-EGR-005` Every cell requires Human confirmation before finalization.  
`FR-EGR-006` Final grid produces master/web/PDF/thumb derived artifacts.  
`FR-EGR-007` Multiple grids allowed with Human primary marker.

# 7. Projects & Roles

`FR-PRJ-001` Projects and Roles are structured professional Source.  
`FR-PRJ-002` Role media supports configured limits/context.  
`FR-PRJ-003` Project links/media remain contextual and do not duplicate reusable global Source unnecessarily.  
`FR-PRJ-004` Education/training is not stored as Project when canonical Training applies.  
`FR-PRJ-005` Project publication/history obeys explicit lifecycle.

# 8. Training

`FR-TRN-001` Training is standalone domain distinct from Project, Skill and Achievement.  
`FR-TRN-002` Institution/course/teacher/date data are stored without qualification inflation.  
`FR-TRN-003` Training does not automatically create or raise Skill.  
`FR-TRN-004` Legacy Project Other education is migrated only when semantics are clear/reviewed.

# 9. Skills

`FR-SKL-001` SkillDefinition dictionary is separate from ActorSkill Source.  
`FR-SKL-002` Skill level is integer exactly 1..5.  
`FR-SKL-003` No decimals/percentages/freeform canonical levels.  
`FR-SKL-004` Evidence/Training/Projects never auto-calculate level.  
`FR-SKL-005` One current assignment per profile+SkillDefinition.  
`FR-SKL-006` Public/QNR/Builder permissions are independent.  
`FR-SKL-007` AI cannot change Skill level.

# 10. Languages

`FR-LNG-001` LanguageDefinition uses stable language/ISO identity.  
`FR-LNG-002` Allowed proficiency is `NATIVE,A1,A2,B1,B2,C1,C2`.  
`FR-LNG-003` Native remains distinct from C2.  
`FR-LNG-004` Multiple Native languages are allowed.  
`FR-LNG-005` No automatic proficiency from training/nationality/AI speech analysis.  
`FR-LNG-006` Site UI locale does not imply actor language proficiency.  
`FR-LNG-007` Visibility flags are independent.

# 11. Professional Media & Links

`FR-LNK-001` ProfessionalLink and MediaAsset remain distinct Source types.  
`FR-LNK-002` External link editor uses exact columns `№ / Описание / URL`.  
`FR-LNK-003` Links are clickable in HTML/PDF.  
`FR-LNK-004` Managed media uses stable application URL, never storage locator.  
`FR-LNK-005` Unsafe schemes/Admin/private/local URLs are rejected for public professional targets.  
`FR-LNK-006` Link-health is derived operational state and never auto-rewrites Source URL.  
`FR-LNK-007` Health checker is SSRF-safe.

# 12. Contacts

`FR-CNT-001` ContactEntry (who) and ContactMethod (how) are separate.  
`FR-CNT-002` Parent/agent/manager/representative roles are explicit.  
`FR-CNT-003` Phone/email/WhatsApp/social actions are derived from normalized Source.  
`FR-CNT-004` Public/QNR/Builder visibility is per ContactMethod and independent.  
`FR-CNT-005` Hidden ContactMethods never reach public/browser payload.  
`FR-CNT-006` Public Contacts, NotificationRecipients, Feedback sender and Admin identity remain separate domains.

# 13. Questionnaires

`FR-QNR-001` Supported types: COMPACT, EXTENDED, CASTING, CUSTOM.  
`FR-QNR-002` Draft/configuration and immutable QuestionnaireRevision are separate.  
`FR-QNR-003` Mandatory minimum includes identification, eligible CLOSE_UP, FULL_BODY and official Profile URL.  
`FR-QNR-004` Save != Publish.  
`FR-QNR-005` Exactly one primary public Questionnaire per applicable scope.  
`FR-QNR-006` HTML/PDF render from frozen Revision/DocumentModel.  
`FR-QNR-007` PDF text remains selectable and links clickable.  
`FR-QNR-008` Historical revision never silently follows current Source changes.

# 14. Public Questionnaire Builder

`FR-BLD-001` Builder is public self-service without mandatory registration baseline.  
`FR-BLD-002` Templates: QUICK, STANDARD, EXTENDED, CASTING.  
`FR-BLD-003` Browser receives Builder Eligibility Projection only.  
`FR-BLD-004` Builder cannot edit authoritative Source.  
`FR-BLD-005` Mandatory identification/CLOSE_UP/FULL_BODY/Profile URL remains.  
`FR-BLD-006` Builder session is temporary/opaque/versioned.  
`FR-BLD-007` Generate revalidates current eligibility and creates immutable GenerationSnapshot.  
`FR-BLD-008` Anti-abuse/rate limits apply.

# 15. QR Links

`FR-QR-001` QR is Derived Artifact from authorized canonical URL.  
`FR-QR-002` Hyperlink/PDF annotation/QR same item use exact same frozen target.  
`FR-QR-003` Per item `show_link` and `show_qr` supported.  
`FR-QR-004` QR is supplementary, not sole digital route.  
`FR-QR-005` Admin/filesystem/storage/private-network URLs forbidden.  
`FR-QR-006` Production QR requires exact decode verification.  
`FR-QR-007` Print quiet zone >=4 modules; module physical size >=0.4mm; baseline overall >=20mm.  
`FR-QR-008` Historical QNR/Builder snapshots freeze QR target semantics.

# 16. Castings

`FR-CST-001` Casting is private first-class professional opportunity/request object distinct from Project/Opportunity.  
`FR-CST-002` Original casting source/provenance is preserved.  
`FR-CST-003` CastingRequirement supports proposed/confirmed/rejected/superseded; confirmed drives deterministic comparison.  
`FR-CST-004` Materials relation distinguishes generated/prepared/submitted.  
`FR-CST-005` Feedback→Casting conversion is explicit Human idempotent action with bidirectional provenance.

# 17. Casting AI

`FR-CAI-001` Analysis starts only by explicit Human request baseline.  
`FR-CAI-002` Analysis uses immutable authorized Source Snapshot.  
`FR-CAI-003` Extract only source-present requirements; missing=`Не указано`.  
`FR-CAI-004` Factual proposals include evidence.  
`FR-CAI-005` AI output is immutable revision and non-authoritative until Human confirmation.  
`FR-CAI-006` Match statuses use canonical enum.  
`FR-CAI-007` AI can create/recommend Questionnaire Draft, never publish/send/change Source.

# 18. Feedback & Inquiries

`FR-FBK-001` Feedback types include CASTING_INVITATION, ROLE, COLLABORATION, QUESTIONNAIRE, MATERIALS, QUESTION, COMMENT, OTHER.  
`FR-FBK-002` Accepted Feedback persists before notification work.  
`FR-FBK-003` Notification provider failure never loses committed Feedback.  
`FR-FBK-004` Read state and workflow state are distinct.  
`FR-FBK-005` Attachments private/validated/quarantinable.  
`FR-FBK-006` Internal notes/assignment/next action are private operational metadata.  
`FR-FBK-007` CreateCastingFromInquiry is explicit Human idempotent provenance-preserving command.  
`FR-FBK-008` Public submit does not grant read-by-ID access.

# 19. Notifications

`FR-NOT-001` Domain business state commits before NotificationIntent.  
`FR-NOT-002` In-App, Email and WhatsApp delivery are independent channel records.  
`FR-NOT-003` Public Contacts/Feedback senders are not automatic NotificationRecipients.  
`FR-NOT-004` Quiet hours/preferences/timezone apply to external delivery.  
`FR-NOT-005` Provider accepted != delivered unless provider confirms.  
`FR-NOT-006` Retries are idempotent/bounded; unknown outcome reconciled.  
`FR-NOT-007` Reading notification does not mark Feedback/Casting business object read/resolved.  
`FR-NOT-008` WhatsApp uses official approved Business channel only.

# 20. Opportunity Pipeline

`FR-OPP-001` Opportunity is separate from Casting/Project and has explicit stage/history.  
`FR-OPP-002` Stage transitions are Human-controlled and validated.  
`FR-OPP-003` Next action/owner/priority are explicit operational fields.  
`FR-OPP-004` BOOKED is authoritative business outcome only after Human transition.  
`FR-OPP-005` BOOKED does not automatically create Project credit; explicit conversion required.  
`FR-OPP-006` Provenance from Feedback/Casting preserved.

# 21. Site Themes

`FR-THM-001` Theme is structured tokens/config, not arbitrary injected code.  
`FR-THM-002` Prompt/AI output creates editable Draft only.  
`FR-THM-003` Human can edit/lock tokens and preview responsive result.  
`FR-THM-004` Accessibility/contrast validation blocks invalid publish/activation.  
`FR-THM-005` Publish/activate/rollback are explicit atomic operations.  
`FR-THM-006` Theme cannot change actress content/appearance/access authority.

# 22. BB Assistant

`FR-BB-001` Supports Biography/Profile/Project/Role/Training/Link/QNR/Cover Letter/Casting/Feedback/Opportunity/Social/custom writing tasks.  
`FR-BB-002` Generation uses immutable purpose-minimized Source Snapshot.  
`FR-BB-003` AI Draft remains non-authoritative until Human review/apply.  
`FR-BB-004` Unsupported professional factual claims block/warn.  
`FR-BB-005` Generate != Apply != Publish != Send.  
`FR-BB-006` Draft history/provenance/model/source versions preserved.  
`FR-BB-007` BB never invents URL, Skill/Language level, award, availability, commercial term or representation.

# 23. VOP

`FR-VOP-001` VOP creates explainable Findings from canonical readiness/health checks.  
`FR-VOP-002` Findings have stable fingerprint/evidence/lifecycle.  
`FR-VOP-003` Safe Automation uses hard allowlisted typed idempotent actions only.  
`FR-VOP-004` VOP cannot mutate professional/business truth, publish, send, stage-transition or confirm AI.  
`FR-VOP-005` VOP may rebuild derived artifacts/reindex/invalidate cache/retry safe technical jobs.  
`FR-VOP-006` AI explanation is optional and non-authoritative.

# 24. Marketing Analytics

`FR-ANL-001` Event schemas/version/authority are registry-defined.  
`FR-ANL-002` Browser interactions and committed server business events are separated.  
`FR-ANL-003` Analytics is privacy-minimized/read-only.  
`FR-ANL-004` UTM/referrer/ShareLink attribution never overrides explicit domain provenance.  
`FR-ANL-005` Bot/Admin/non-production traffic separable.  
`FR-ANL-006` Funnels expose explicit cohort/numerator/denominator/time window.  
`FR-ANL-007` Analytics never changes Source/visibility/order/Theme/Opportunity automatically.

# 25. Help Center & Tickets

`FR-HLP-001` Russian Help Center provides canonical-product guidance.  
`FR-HLP-002` HelpTicket is technical support domain distinct from professional Feedback/Casting/Opportunity.  
`FR-HLP-003` Ticket category/priority/status/assignment/SLA lifecycle supported.  
`FR-HLP-004` Public replies and internal notes are separate; internal notes filtered server-side.  
`FR-HLP-005` Attachments private/validated/scanned.  
`FR-HLP-006` Closing Ticket never changes linked professional entity automatically.

# 26. Social Publishing

`FR-SOC-001` Instagram/TikTok integrations use OAuth provider adapters.  
`FR-SOC-002` OAuth tokens are server-side secrets with refresh/revoke lifecycle.  
`FR-SOC-003` SocialPostDraft references eligible media and Caption Draft.  
`FR-SOC-004` Website visibility does not imply social distribution right.  
`FR-SOC-005` Human approval required; material edit invalidates approval.  
`FR-SOC-006` Schedule/Publish freezes immutable PublishSnapshot.  
`FR-SOC-007` Worker publishes snapshot, not mutable Draft.  
`FR-SOC-008` PublishAttempt models accepted/published/failure/unknown outcome.  
`FR-SOC-009` Unknown outcome reconciles before retry.  
`FR-SOC-010` AI/VOP/Analytics cannot autonomously schedule/publish.

# 27. Achievements

`FR-ACH-001` Achievement is separate professional Source.  
`FR-ACH-002` WINNER/NOMINATED/FINALIST/SHORTLISTED/OFFICIAL_SELECTION/SPECIAL_MENTION semantics are distinct.  
`FR-ACH-003` Project recognition does not automatically become personal actress award.  
`FR-ACH-004` Evidence/provenance can be attached and is private by default.  
`FR-ACH-005` Public/QNR/Builder visibility independent.  
`FR-ACH-006` BB/SEO/AI cannot use `award-winning` without genuine winner/award evidence.

# 28. Cross-cutting Architecture

`FR-ARC-001` System is Modular Monolith + Background Worker baseline.  
`FR-ARC-002` PostgreSQL is authoritative relational Source.  
`FR-ARC-003` Source/Configuration/Revision/Derived/Projection/Operational/AI/Audit classes remain distinct.  
`FR-ARC-004` Same fact has one owner.  
`FR-ARC-005` Save != Publish; Archive != Delete; Snapshot/Revision immutable.  
`FR-ARC-006` Background jobs are durable at-least-once + idempotent.  
`FR-ARC-007` Search/cache are derived/rebuildable.  
`FR-ARC-008` DTO != ORM model; client has no DB/filesystem/provider authority.

# 29. Cross-cutting Security

`FR-SEC-001` Admin authentication uses secure revocable server sessions.  
`FR-SEC-002` Every privileged action is authorized server-side.  
`FR-SEC-003` Public/Builder/token projections exclude hidden data before serialization.  
`FR-SEC-004` Upload/URL/SSRF/XSS/CSRF/CSP protections implemented.  
`FR-SEC-005` Secrets never reach browser/logs/analytics.  
`FR-SEC-006` Token-scoped access is high-entropy/resource-bound/revocable.  
`FR-SEC-007` Provider webhooks authenticated/deduplicated.  
`FR-SEC-008` Public write surfaces rate-limited/idempotent as relevant.

# 30. Cross-cutting NFR

`FR-NFR-001` Public pages target good Core Web Vitals class.  
`FR-NFR-002` Accessibility target is WCAG 2.2 AA for applicable UI.  
`FR-NFR-003` Supported browser/device matrix is defined/tested.  
`FR-NFR-004` SEO uses only public confirmed projection.  
`FR-NFR-005` Analytics reports its data quality/uncertainty.  
`FR-NFR-006` Backup/restore is tested and never replays external side effects blindly.

# 31. Cross-cutting Operations

`FR-OPS-001` Production runs behind Nginx/HTTPS with configured app port 3336.  
`FR-OPS-002` Deployment validates migrations/config/health.  
`FR-OPS-003` PostgreSQL/media/config have backup/restore runbook.  
`FR-OPS-004` Monitoring covers app/DB/jobs/media/provider/security signals.  
`FR-OPS-005` Incident/rollback/maintenance runbooks exist.

# 32. Acceptance of this catalog

A `FR-*` is complete only when:

```text
owning design document exists
implementation exists
negative/positive tests exist
security/access behavior is proven
migration/operations impact handled
traceability entry exists
```

## 33. Финальная доктрина

> **DOC-180 is the master feature contract: every capability is defined by the domain that owns it and by the authority boundaries surrounding it. A matching button or endpoint is not sufficient; the implementation must preserve immutable history, Human control, independent visibility, durable side effects and the documented security/NFR semantics.**
