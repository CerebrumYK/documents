# PERFORMANCE TESTING
## DOC-175 — Web Vitals, API/DB latency, load, queue, media и regression budgets

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет test methodology для DOC-151 budgets.

> **Performance tests model representative public/admin workloads, separate application latency from provider latency, and fail on meaningful regressions without encouraging unsafe shortcuts.**

---

## 2. Test categories

```text
LAB_WEB
API_BENCHMARK
DB_QUERY
LOAD
BURST
SOAK
BACKGROUND_JOB
MEDIA_PIPELINE
REGRESSION
```

## 3. Public lab tests

Run Lighthouse/WebPageTest-equivalent or Playwright browser metrics on representative pages with controlled mobile-like CPU/network.

Track LCP/CLS/INP proxies/TBT where tool permits, TTFB, JS bytes, image bytes, request count.

## 4. Web Vitals RUM

If enough privacy-safe production data exists, compare field p75 to targets. Small sample labeled; lab remains release signal when RUM insufficient.

## 5. Page scenarios

At minimum:

- home/profile;
- Main Portfolio;
- Emotional Portfolio;
- Project detail;
- public Questionnaire;
- Builder initial load.

## 6. Image audit

Assert viewport receives appropriately sized derivative and does not download full original for thumbnails/cards. Measure hero image priority and lazy gallery behavior.

## 7. JavaScript budget

Track route JS bundle changes. Significant unexpected increase triggers warning/review. Admin bundles separated from public routes.

## 8. API latency

Benchmark representative server actions/queries with warmed DB under controlled host:

```text
public profile projection
admin list/detail
Feedback submission DB phase
Questionnaire readiness
search
```

External provider work excluded/recorded separately.

## 9. Database tests

Use seeded representative dataset and `EXPLAIN (ANALYZE where safe)`/query metrics. Detect N+1 via query count or trace.

## 10. Load profiles

### Normal
Expected concurrent public traffic.

### Burst
Link shared after casting/social event.

### Builder burst
Several PDF generations in short window.

### Admin + workers
Background media/reindex running while Admin performs core actions.

## 11. Soak

Longer lower-load test can detect memory/connection/job leaks before major release.

## 12. Queue performance

Measure:

```text
oldest job age
throughput
p95 execution
retry rate
worker saturation
```

CPU-heavy jobs should not starve lightweight queues if separated.

## 13. Media pipeline

Fixture sizes/classes measure image derivative/video transcode/audio metadata. Set reasonable timeout/memory bounds rather than one global latency target.

## 14. PDF

Measure generation p50/p95 and artifact size on Compact/Extended/Casting questionnaires. Never disable QR verification or selectable text for performance test success.

## 15. Search

Measure realistic query/filters and index size. Verify result latency under concurrent read plus index refresh.

## 16. Cache

Compare cold/warm public paths. Validate that performance benefit does not cross access-class boundaries.

## 17. Provider latency

Provider fake injects realistic delays to prove HTTP request does not block unrelated core path and timeout/backoff works.

## 18. Failure under load

System should fail boundedly:

- rate limit/queue backpressure;
- normalized errors;
- no data corruption;
- no duplicate side effects.

## 19. Baseline storage

Keep benchmark baseline per release/hardware profile. Compare percent and absolute budgets; hardware noise accounted for.

## 20. Regression policy

Example:

- hard budget exceeded → fail;
- material regression > configured percentage → warning/fail based path;
- explain accepted variance in release note/issue.

## 21. Environment

Performance tests run on stable dedicated/controlled environment when possible. Shared noisy CI numbers are used cautiously.

## 22. Instrumentation overhead

Measure with production-like telemetry enabled; debug profilers should not define release baseline.

## 23. Test data

Representative but synthetic. Include enough Portfolio/Media/Feedback/Audit rows to expose scaling defects.

## 24. Anti-patterns

Forbidden:

1. Benchmark empty DB only.
2. Measure provider time as app DB latency.
3. Performance fix disables authorization.
4. Performance fix serves full hidden Source to client cache.
5. PDF speed test ignores correctness.
6. One laptop run becomes universal SLO evidence.
7. Queue latency unmeasured.
8. Bundle growth never tracked.
9. Only warm-cache tests.
10. Load test creates uncontrolled real provider side effects.

## 25. Acceptance criteria

`AC-PTEST-001` Critical public pages have repeatable lab performance tests.  
`AC-PTEST-002` Image/JS payload regressions are measured.  
`AC-PTEST-003` Interactive API/DB hot paths have representative benchmarks.  
`AC-PTEST-004` Normal/burst/worker workloads are load-tested.  
`AC-PTEST-005` Queue and media/PDF pipelines have throughput/latency metrics.  
`AC-PTEST-006` Cold and warm cache behavior are tested without security leakage.  
`AC-PTEST-007` Provider delays/failures are isolated using fakes.  
`AC-PTEST-008` Regression budgets are machine-evaluable.  
`AC-PTEST-009` Test environment/data are documented and reproducible.  
`AC-PTEST-010` Performance failures never justify violating correctness/security/accessibility.

---

## 26. Финальная доктрина

> **Performance testing measures the real bottlenecks—browser payloads, DB queries, queues, media/PDF processing and burst behavior—against stable baselines. It separates provider latency and rejects any optimization that obtains speed by weakening professional correctness, accessibility or security.**
