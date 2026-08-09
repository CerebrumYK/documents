# ANALYTICS QUALITY REQUIREMENTS
## DOC-155 — data quality, measurement integrity, attribution reliability, privacy и reporting semantics

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт non-functional quality requirements для Analytics Module: точность событий, полнота, deduplication, bot filtering, attribution, partial data и reproducible reports.

> **Analytics is useful only when uncertainty and data quality are explicit. Missing events are not zero, sessions are not guaranteed unique people, provider/click events are not business outcomes, and every KPI must be reproducible from a versioned event definition and stated cohort.**

---

## 2. Quality dimensions

```text
VALIDITY
COMPLETENESS
UNIQUENESS
TIMELINESS
CONSISTENCY
TRACEABILITY
PRIVACY
REPRODUCIBILITY
```

## 3. Event registry

Every production event has:

```text
event_name
event_version
authority: CLIENT/SERVER
allowed properties
privacy class
retention class
bot policy
sampling policy
owner
```

Unknown undocumented event does not silently enter dashboards.

## 4. Schema validation

Ingestion validates event name/version/property types/limits. Invalid properties are rejected rather than stored as arbitrary JSON forever.

## 5. Authority

Client may emit interaction events; committed business outcomes are server-emitted.

Browser cannot authoritatively emit:

```text
feedback_submitted
opportunity_booked
casting_submitted
```

## 6. Idempotency/uniqueness

`client_event_id` dedupes transmission retry, not legitimate repeated action. Server business event maps once per domain event/outbox ID.

## 7. Completeness

Pipeline tracks ingestion gaps. If telemetry unavailable for a period, report is `PARTIAL/UNKNOWN`, not zero.

## 8. Timeliness

Track:

```text
occurred_at
received_at
aggregate_through
```

Late events may update aggregates inside bounded correction window.

## 9. Clock trust

Client `occurred_at` is advisory and skew-bounded. Domain business event timestamp is authoritative for business funnel transitions.

## 10. Bot filtering

Known crawler/preview/monitor traffic is flagged/excluded from human engagement reports. Classification is heuristic and reportable.

Bot detection failure must not alter professional Source.

## 11. Admin/internal traffic

Authenticated Admin preview/testing excluded or separately segmented from public audience KPIs.

## 12. Environment separation

Production/staging/development events are separated. Production dashboards cannot ingest test fixture traffic by default.

## 13. Session semantics

Metric names must say `sessions` unless technology genuinely supports people identity. Anonymous random first-party identifier is not proof of unique person.

## 14. Attribution

Every report labels attribution model:

```text
EXPLICIT_DOMAIN_PROVENANCE
FIRST_TOUCH
LAST_TOUCH
MULTI_TOUCH (future)
```

Heuristic attribution never overwrites authoritative domain provenance.

## 15. UTM/referrer quality

Only allowlisted UTM fields stored. Referrer minimized to source/domain/classification. Invalid/unknown source remains unknown.

## 16. Funnel definitions

Every funnel declares:

```text
name
step definitions
cohort
window
numerator
denominator
attribution mode
bot/internal exclusions
```

## 17. Business funnel

Feedback→Casting→Opportunity→Booked joins through domain IDs/provenance where available, not browser cookie guess.

Skipped Opportunity stages are not fabricated.

## 18. Conversion semantics

Examples:

```text
contact_click ≠ message sent
pdf_download ≠ casting submission
video_complete ≠ role interest
opportunity_booked = committed BOOKED state only
```

## 19. Sampling

Critical business events are not sampled. High-frequency low-value interaction events may use documented sampling.

Sampled metrics must be labeled/aggregated correctly.

## 20. Aggregation

Aggregates are deterministic/rebuildable. Re-running a day/hour aggregation must not double-count.

Each aggregate has generation/through-date.

## 21. Report reproducibility

Given event registry version, date range, filters and attribution mode, report should be reproducible within documented late-event/bot-reclassification corrections.

## 22. Data quality dashboard

Track at least:

- ingestion availability;
- schema rejection rate;
- duplicate rate;
- bot ratio;
- unknown attribution rate;
- late event rate;
- aggregate freshness;
- pipeline errors.

## 23. Small samples

Percentage/conversion reports show counts. Very small cohorts display warning and are not presented as statistically decisive.

## 24. Privacy quality

Tests ensure events exclude:

- raw phone/email;
- Feedback/Casting message body;
- private token;
- OAuth credential;
- full sensitive URL query;
- Admin notes.

## 25. Raw retention

Raw public events have bounded retention. Aggregates may retain longer if privacy-safe.

## 26. Deletion

Where attributable visitor data is deleted/anonymized, truly non-attributable aggregate may remain. Rebuild strategy documents effect.

## 27. Analytics availability

Analytics is non-critical path. Ingestion outage must not block public page, link, Feedback or PDF delivery.

## 28. Performance

Client collection uses non-blocking batching/beacon/fetch patterns. Batch size/request rates bounded.

## 29. Analytics AI

AI gets aggregate/read-only data where possible. Every AI insight displays period/sample/context and remains advisory.

No causal wording from simple observational correlation.

## 30. Theme/content comparison

Before/after metrics are not called A/B test unless explicit experiment assignment/control exists.

## 31. Export

Export includes filters/date/source definitions, respects privacy, and is Admin-only. Raw event export is not baseline.

## 32. Monitoring

Alert on pipeline unavailable/stale aggregate, not routine KPI movement by default.

## 33. Version changes

Changing event semantics requires new version or documented backward-compatible change. Historical events are not silently reinterpreted under new incompatible definition.

## 34. Metric definitions

Maintain canonical metric registry for key dashboard metrics, including exact SQL/query/business definition where implementation exists.

## 35. Anti-patterns

Forbidden:

1. Missing period shown as zero.
2. Session labeled unique person.
3. Click labeled confirmed conversion.
4. Client emits Booked.
5. Preview bot counted human.
6. Admin testing inflates public metrics.
7. Staging data mixed with production.
8. First/last-touch unlabeled.
9. Unknown attribution forced into a campaign.
10. Aggregate rerun double-counts.
11. KPI lacks numerator/denominator.
12. Tiny sample shown as decisive.
13. Analytics failure blocks Feedback.
14. Raw PII/token stored for convenience.
15. AI correlation called causation.
16. Before/after called experiment without assignment.

## 36. Test cases

- duplicate same client event stored once;
- legitimate repeated action stored separately;
- forged business event rejected client-side;
- outage period marked partial;
- late server event updates bounded aggregate;
- bot/admin/staging excluded as configured;
- same funnel query reproducible;
- actual StageHistory respected;
- aggregate rebuild idempotent;
- raw token/PII prohibited by schema/test fixture;
- small cohort warning visible;
- analytics endpoint outage does not affect business action.

## 37. Acceptance criteria

`AC-ANQ-001` Every event is versioned and registry-defined.  
`AC-ANQ-002` Event authority distinguishes client interactions from server business outcomes.  
`AC-ANQ-003` Transmission retries dedupe without suppressing legitimate repeated actions.  
`AC-ANQ-004` Missing/incomplete periods are explicitly marked.  
`AC-ANQ-005` Bot/Admin/non-production traffic is classifiable/excludable.  
`AC-ANQ-006` Session metrics are not mislabeled as unique people.  
`AC-ANQ-007` Attribution model is explicit and cannot override domain provenance.  
`AC-ANQ-008` Funnel definitions include cohort/numerator/denominator/window.  
`AC-ANQ-009` Aggregates are idempotent/rebuildable/freshness-visible.  
`AC-ANQ-010` Privacy-denied fields never enter production event schemas.  
`AC-ANQ-011` Analytics remains non-critical path.  
`AC-ANQ-012` Data-quality tests/monitoring are release requirements.

---

## 38. Финальная доктрина

> **Analytics reports what can actually be supported by versioned events and explicit cohorts. It exposes gaps, bot/internal traffic, attribution uncertainty and small samples rather than inventing precision; business outcomes come from committed domains; and measurement remains privacy-minimized, reproducible and non-blocking.**
