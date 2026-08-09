# MASTER ACCEPTANCE CRITERIA
## DOC-181 — global product acceptance checklist

**Статус:** Canonical / Required

---

## 1. Назначение

Документ консолидирует product-level acceptance criteria. Module-specific AC remain authoritative for detailed behavior; DOC-181 defines the minimum integrated acceptance state of the whole product.

> **The product is accepted only as an integrated professional system: correct Source ownership, public privacy, Human-controlled publication, immutable historical artifacts, reliable provider failure handling and operational recoverability must all hold simultaneously.**

---

# 2. Product foundation

`AC-MASTER-001` Application uses canonical Next.js/TypeScript/PostgreSQL architecture without hidden Supabase dependency.  
`AC-MASTER-002` Production storage/config matches documented `/data/kate-actor`/port 3336/Nginx model or explicitly updated canonical deployment.  
`AC-MASTER-003` Professional Source, configuration, immutable revisions, derived artifacts, projections, operational records, AI output and Audit are distinguishable in implementation.  
`AC-MASTER-004` One authoritative owner exists for every professional fact.  
`AC-MASTER-005` Save, Publish, Archive, Revoke and Delete have distinct semantics.

# 3. Public privacy

`AC-MASTER-006` Anonymous public responses contain only explicitly public data.  
`AC-MASTER-007` Hidden Contacts/Skills/Languages/Achievements/media never reach public serialized payload.  
`AC-MASTER-008` Admin, Builder and token-scoped surfaces cannot leak data across access classes.  
`AC-MASTER-009` Public Search/SEO/cache contain only currently eligible public projections.  
`AC-MASTER-010` Private filesystem/storage paths never appear in public URLs/PDF/QR/errors.

# 4. Admin/authentication

`AC-MASTER-011` Admin routes require secure server-validated authentication.  
`AC-MASTER-012` Every privileged command performs server-side authorization.  
`AC-MASTER-013` Cross-profile ID tampering is rejected.  
`AC-MASTER-014` Stale writes cannot overwrite newer Human state silently.  
`AC-MASTER-015` Security-sensitive sessions/tokens are revocable and secret-safe.

# 5. Profile/Portfolio

`AC-MASTER-016` Profile is editable/publishable without AI.  
`AC-MASTER-017` Main Portfolio categorization/order/visibility is Human-controlled.  
`AC-MASTER-018` Mandatory QNR Close-Up/Full Body are sourced only from eligible Main Portfolio.  
`AC-MASTER-019` Media originals remain immutable.  
`AC-MASTER-020` Portfolio presentation preserves intended full-source/no-unintended-crop behavior.

# 6. Emotional portfolio/grid

`AC-MASTER-021` Emotional Portfolio remains distinct from Project.  
`AC-MASTER-022` Shooting date is required.  
`AC-MASTER-023` Grid sizes/counts follow exact supported matrix.  
`AC-MASTER-024` Every grid cell is Human-confirmed before finalize.  
`AC-MASTER-025` Questionnaire uses only final composite/date/full-portfolio link for emotional block.

# 7. Projects/Training/Skills/Languages

`AC-MASTER-026` Projects/Roles and Training are separate professional domains.  
`AC-MASTER-027` Training never automatically creates/raises Skill.  
`AC-MASTER-028` Skill level is exact 1..5 Human-confirmed data.  
`AC-MASTER-029` Language proficiency is exact Native/A1…C2 and not inferred.  
`AC-MASTER-030` Site locale does not imply actor language proficiency.

# 8. Links/Contacts

`AC-MASTER-031` ProfessionalLink and managed MediaAsset remain distinct.  
`AC-MASTER-032` External link editor preserves number/description/URL contract.  
`AC-MASTER-033` URLs are safe/clickable and stable in HTML/PDF.  
`AC-MASTER-034` Contact person and ContactMethod are separate.  
`AC-MASTER-035` Public Contacts never automatically configure notification recipients.

# 9. Questionnaire/PDF/QR

`AC-MASTER-036` COMPACT/EXTENDED/CASTING/CUSTOM questionnaire types operate through Draft→Publish→immutable Revision.  
`AC-MASTER-037` Exactly one primary public questionnaire exists where required.  
`AC-MASTER-038` Historical revisions do not change after current Source edits.  
`AC-MASTER-039` Generated PDF has selectable text and actual hyperlinks.  
`AC-MASTER-040` QR encodes exact frozen canonical URL, is decode-verified and print-safe.  
`AC-MASTER-041` QR never contains Admin/filesystem/raw-storage target.  
`AC-MASTER-042` QR is supplementary to clickable digital link.

# 10. Public Builder

`AC-MASTER-043` Builder requires no mandatory registration baseline.  
`AC-MASTER-044` Browser receives only Builder-eligible projection.  
`AC-MASTER-045` Mandatory identity/Close-Up/Full Body/Profile URL cannot be removed.  
`AC-MASTER-046` Generate revalidates current Source and creates immutable snapshot.  
`AC-MASTER-047` Builder cannot mutate Source/Contacts/URLs/Skill/Language facts.  
`AC-MASTER-048` Public Builder is rate-limited/anti-abuse protected.

# 11. Casting/AI

`AC-MASTER-049` Casting preserves original source/provenance and remains distinct from Project/Opportunity.  
`AC-MASTER-050` AI analysis requires explicit invocation and immutable source snapshot.  
`AC-MASTER-051` Missing casting requirement is reported as unknown/`Не указано`, not guessed.  
`AC-MASTER-052` AI factual proposals expose evidence.  
`AC-MASTER-053` Only Human confirmation creates authoritative CastingRequirement.  
`AC-MASTER-054` AI never publishes/sends/changes Profile or Opportunity.

# 12. Feedback/Notifications

`AC-MASTER-055` Accepted Feedback commits before notification provider work.  
`AC-MASTER-056` Notification failure never loses committed Feedback.  
`AC-MASTER-057` Feedback read/workflow/assignment/notes/next action are separate states/data.  
`AC-MASTER-058` Feedback attachments are private and validated.  
`AC-MASTER-059` CreateCastingFromInquiry is explicit, idempotent and provenance-preserving.  
`AC-MASTER-060` In-App/Email/WhatsApp delivery states remain independent.  
`AC-MASTER-061` Notification read does not resolve/read business object automatically.  
`AC-MASTER-062` WhatsApp outbound uses approved Business integration only.

# 13. Opportunity

`AC-MASTER-063` Opportunity stages/history are explicit and Human-controlled.  
`AC-MASTER-064` Business priority/next action/owner are not inferred by Analytics/VOP.  
`AC-MASTER-065` BOOKED is committed only by authorized Human workflow.  
`AC-MASTER-066` BOOKED→Project/Role creation requires explicit Human conversion.  
`AC-MASTER-067` Feedback/Casting provenance remains linked.

# 14. Themes/BB/VOP

`AC-MASTER-068` Theme AI generates structured Draft only.  
`AC-MASTER-069` Theme publish/activate requires accessibility validation and Human action.  
`AC-MASTER-070` Theme cannot alter actress content/access/appearance.  
`AC-MASTER-071` BB Assistant generates grounded Draft from frozen Source Snapshot.  
`AC-MASTER-072` BB Generate != Apply != Publish/Send.  
`AC-MASTER-073` BB cannot invent awards/skills/languages/availability/commercial terms.  
`AC-MASTER-074` VOP Findings are explainable/fingerprinted.  
`AC-MASTER-075` VOP safe automation is hard-allowlisted and cannot change professional/business truth.

# 15. Analytics

`AC-MASTER-076` Analytics event registry distinguishes client interactions/server business events.  
`AC-MASTER-077` Analytics is privacy-minimized and does not store private messages/tokens.  
`AC-MASTER-078` Bot/Admin/non-production traffic is separable.  
`AC-MASTER-079` Funnel/attribution definitions are explicit and domain provenance has priority.  
`AC-MASTER-080` Analytics never automatically changes ordering, visibility, Theme or Opportunity.

# 16. Help/Social/Achievements

`AC-MASTER-081` Help Ticket remains technical support, not professional Feedback.  
`AC-MASTER-082` Internal support notes/attachments remain private.  
`AC-MASTER-083` Closing ticket does not mutate linked professional entity.  
`AC-MASTER-084` Social OAuth credentials are server-side/revocable.  
`AC-MASTER-085` Social publication requires explicit Human approval/schedule/publish intent.  
`AC-MASTER-086` Scheduled publishing uses immutable snapshot and durable worker.  
`AC-MASTER-087` Provider UNKNOWN_OUTCOME reconciles before retry.  
`AC-MASTER-088` Achievement status distinguishes Winner/Nominated/Selection/Finalist/etc.  
`AC-MASTER-089` Project recognition never becomes personal award automatically.  
`AC-MASTER-090` BB/SEO cannot say award-winning without confirming evidence.

# 17. Security/NFR

`AC-MASTER-091` Authentication/authorization/CSRF/XSS/SSRF/upload/secrets/token controls pass security gates.  
`AC-MASTER-092` Public critical pages satisfy agreed performance budgets.  
`AC-MASTER-093` Applicable UI targets WCAG 2.2 AA and critical keyboard/screen-reader checks.  
`AC-MASTER-094` Supported browser/mobile smoke matrix passes.  
`AC-MASTER-095` SEO contains only public confirmed data and token/private surfaces are non-indexable.  
`AC-MASTER-096` Analytics missing/partial data is never presented as zero certainty.

# 18. Migration/Operations

`AC-MASTER-097` Migrations are versioned/idempotent and never invent missing professional facts.  
`AC-MASTER-098` Media originals checksum-match after migration.  
`AC-MASTER-099` Ambiguous legacy data enters Human review.  
`AC-MASTER-100` Backup/restore is documented/tested and does not replay outbound side effects.  
`AC-MASTER-101` Deployment validates schema/config/health before traffic.  
`AC-MASTER-102` Monitoring covers app/DB/jobs/providers/security with secret-safe logs.  
`AC-MASTER-103` Incident and maintenance runbooks exist.

# 19. Engineering quality

`AC-MASTER-104` Strict TypeScript/build/lint/tests pass.  
`AC-MASTER-105` DTOs/projections do not expose ORM entities directly.  
`AC-MASTER-106` Background jobs are durable/idempotent/restart-safe.  
`AC-MASTER-107` Search/cache/derived artifacts are rebuildable.  
`AC-MASTER-108` Critical invariants have test traceability.  
`AC-MASTER-109` High-risk changes have docs/migration/security review.  
`AC-MASTER-110` Release quality gates DOC-179 pass.

# 20. Enterprise integrated implementation

`AC-MASTER-111` Final application uses exactly one canonical production technology stack defined by DOC-208.  
`AC-MASTER-112` Runtime/package/container versions are pinned and reproducible; production uses no floating `latest`.  
`AC-MASTER-113` No unauthorized parallel ORM/migration/queue/search/storage/styling/test stack remains at Enterprise acceptance.  
`AC-MASTER-114` All baseline product modules are implemented within the continuous DOC-225 Enterprise campaign.  
`AC-MASTER-115` Every release-critical FR has actual code and passing-test traceability.  
`AC-MASTER-116` Full unit/integration/E2E/security/accessibility/performance/visual/migration/restore gates pass.  
`AC-MASTER-117` P0/P1 residual implementation gaps are zero at Enterprise DONE.  
`AC-MASTER-118` Interface implementation is verified through the pinned DOC-156/224 lifecycle and project-owned design evidence.  
`AC-MASTER-119` Backup/restore rehearsal demonstrates documented achievable RPO/RTO before production acceptance.  
`AC-MASTER-120` Final Enterprise report identifies exact non-VERIFIED provider/environment limitations; no external success is fabricated.

---

## 21. Acceptance state

Product status may be:

```text
NOT_ACCEPTABLE
PARTIALLY_ACCEPTABLE
ACCEPTABLE_WITH_NON_BLOCKING_GAPS
ACCEPTED
```

Security/privacy/data-loss/Human-authority blockers always force `NOT_ACCEPTABLE` for production release.

## 22. Финальная доктрина

> **Acceptance is system-wide. The project is not accepted because individual screens work: the same data must remain correctly owned, private where required, historically immutable where published, Human-controlled where professional decisions matter, and recoverable when workers/providers/migrations fail.**
