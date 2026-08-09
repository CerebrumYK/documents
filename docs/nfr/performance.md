# PERFORMANCE REQUIREMENTS
## DOC-151 — web performance, server latency, database, media, PDF, jobs и capacity budgets

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт measurable performance requirements для Public Site, Admin, API/Server Actions, database, media, PDF/QR, search/cache и background jobs.

> **Performance optimization must preserve security, correctness and source quality. The system optimizes public projections and derived media rather than weakening authorization, lowering image fidelity blindly or moving heavy work into blocking requests.**

---

## 2. Measurement principle

Performance оценивается отдельно для:

```text
PUBLIC_NAVIGATION
ADMIN_INTERACTION
SERVER_COMMAND
DATABASE_QUERY
MEDIA_DELIVERY
BACKGROUND_JOB
EXTERNAL_PROVIDER
```

Provider latency is reported separately from application compute time where possible.

## 3. Public Web Vitals targets

For representative production mobile conditions, target at least “good” Core Web Vitals class for major public pages:

```text
LCP <= 2.5 s target
INP <= 200 ms target
CLS <= 0.1 target
```

These are release targets, measured via lab plus real-user telemetry when available; small sample is reported honestly.

## 4. TTFB

Public cached/server-rendered pages should target low server response latency. Suggested release budget under normal load:

```text
p75 TTFB <= 800 ms
```

Admin/API interactive reads/writes should generally target server processing:

```text
p95 <= 1 s
```

excluding intentionally asynchronous generation/provider completion.

## 5. Heavy operations

Operations expected to exceed interactive latency become background jobs:

- video transcoding;
- large derivative generation;
- AI generation/analysis;
- PDF generation when non-trivial;
- bulk reindex/rebuild;
- social provider async processing.

HTTP request returns accepted/status handle rather than waiting until infrastructure timeout.

## 6. Images

Public image pipeline must provide responsive derivatives (`srcset`/sizes or framework equivalent) appropriate to viewport/DPR.

Do not send full camera original for card/thumbnail.

Priority loading reserved for actual above-the-fold critical image(s), not entire gallery.

## 7. Image dimensions/layout

Reserve dimensions/aspect ratio to minimize CLS. Original aspect ratio requirements in portfolio remain respected; optimization cannot introduce unintended crop.

## 8. Video/audio

- streaming/range support where serving locally;
- poster/preview derivatives;
- metadata loaded without full media download;
- no autoplay audio;
- preload policy appropriate to page context;
- avoid loading all portfolio videos on initial page.

## 9. Fonts

Use optimized web fonts/system fallback with bounded font variants. Prevent FOIT/layout shift where practical. Do not ship unused font families/weights.

## 10. JavaScript

Public page JS should be minimized; server components/static rendering used where appropriate. Lazy-load Admin-only/editor/AI code from public bundle.

No heavy analytics/provider SDK required for first paint if simple first-party events suffice.

## 11. CSS/theme

Theme tokens must not generate unbounded CSS or runtime style recalculation. Temporary themes use same optimized rendering path.

## 12. Public API payloads

- pagination/bounds;
- projection-specific DTO;
- no hidden alternatives;
- no binary/base64 media blobs;
- avoid duplicate translations/metadata not used by surface.

## 13. Admin lists

All potentially growing lists use pagination/cursor/filters:

- Feedback;
- Castings;
- Opportunities;
- notifications;
- Help Tickets;
- audit/history;
- media.

Avoid rendering thousands of rows at once.

## 14. Database query budgets

High-traffic page should use bounded number of queries independent of child count where possible. N+1 patterns are release defects on hot paths.

Indexes required for:

- profile ownership FKs;
- lifecycle/visibility queries;
- ordering;
- status/timestamps;
- idempotency keys;
- job queue eligibility;
- search support.

## 15. Query plan review

Queries showing sequential scan over large operational tables on frequent filters require index/query redesign unless dataset demonstrably bounded and documented.

## 16. Transactions

Keep DB transactions short. Do not call external AI/email/social provider while holding business transaction open.

## 17. Connection management

Use bounded DB connection pool appropriate to server/worker concurrency. Avoid spawning uncontrolled connections per request/job.

## 18. Cache

Cache only derived/read data with explicit invalidation/generation semantics. Public immutable artifacts are ideal long-cache candidates.

Do not cache sensitive responses publicly for speed.

## 19. Search

Search latency target for ordinary Admin/Public queries should feel interactive; suggested:

```text
p95 server search <= 500 ms
```

under representative dataset.

Search index/projection freshness is separately monitored.

## 20. PDF generation

PDF generation can be asynchronous. Quality requirements (clickable links, verified QR, selectable text) cannot be removed to meet latency.

Track:

```text
queue latency
generation latency
artifact size
failure rate
```

## 21. QR

QR generation is deterministic/cacheable by target/settings fingerprint. Reuse verified artifact where access class permits.

## 22. Background queue

Measure:

- queue depth;
- oldest queued age;
- job duration p50/p95;
- retry rate;
- dead-letter/final failure count.

Backpressure prevents resource starvation.

## 23. Worker concurrency

Different job classes may have separate concurrency limits: CPU-heavy video jobs must not starve lightweight notification jobs.

## 24. External providers

Always set timeout. Provider latency does not block unrelated requests. Rate-limit/backoff policy prevents thundering herd.

## 25. Analytics

Client analytics send is non-blocking. Analytics failure never delays navigation/link/PDF/Feedback success.

## 26. SSR/ISR/cache rendering

Use framework capabilities only where consistent with strong revocation/visibility semantics. Public content can cache; Admin/token content needs appropriate private/dynamic handling.

## 27. Compression

Enable appropriate HTTP compression for text assets. Do not recompress already compressed video/images blindly.

## 28. Static assets

Content-hashed static assets use long immutable caching. HTML/data caches use lifecycle-aware invalidation.

## 29. Capacity assumptions

Baseline is a single professional portfolio, not a mass social network. Capacity tests should nevertheless include realistic peaks after casting/social link sharing and simultaneous PDF/media access.

## 30. Load profiles

At least:

```text
NORMAL_PUBLIC
BURST_PUBLIC
ADMIN_CONCURRENT
BUILDER_GENERATION_BURST
MEDIA_DOWNLOAD_BURST
BACKGROUND_REBUILD
```

## 31. Resource limits

Set explicit:

- upload max size;
- job concurrency;
- worker memory/time limits;
- HTTP body limits;
- Builder generation rate;
- AI context/output limits.

## 32. Performance regression gate

CI/pre-release comparison should fail or warn when representative critical path crosses agreed budget materially.

A release may override warning only with documented reason, never silently.

## 33. Profiling

Use server timing/tracing/query metrics to identify bottleneck before optimization. Do not reduce security checks based on assumption they are slow; measure and optimize implementation.

## 34. Performance anti-patterns

Forbidden:

1. Full-res originals as thumbnails.
2. All gallery media loaded eagerly.
3. External provider call inside DB transaction.
4. 30s+ AI call blocking normal page request.
5. Full unpaginated Feedback/Audit history.
6. N+1 query per gallery item.
7. Public client receives Admin fields then discards them.
8. Base64 media in JSON.
9. Public cache for token-scoped content.
10. Unlimited worker concurrency.
11. Analytics blocks navigation.
12. PDF becomes screenshot image solely for speed.
13. QR size/verification weakened for render speed.
14. Security/authorization removed to hit latency budget.

## 35. Performance test suite

Measure:

- homepage/profile cold/warm;
- Portfolio gallery;
- Questionnaire HTML/PDF;
- Builder load/generate;
- Admin media/Feedback/Casting lists;
- DB hot queries;
- search;
- queue processing;
- media derivative pipeline;
- restoration/reindex load where relevant.

## 36. Acceptance criteria

`AC-PERF-001` Public pages target good Core Web Vitals on representative mobile conditions.  
`AC-PERF-002` Interactive server actions are designed for sub-second-class processing absent external/heavy work.  
`AC-PERF-003` Heavy work is durable asynchronous work.  
`AC-PERF-004` Public images use correctly sized derivatives and preserve intended composition.  
`AC-PERF-005` Video/audio are lazy/streamed rather than eagerly downloaded.  
`AC-PERF-006` Public JS excludes unnecessary Admin/provider code.  
`AC-PERF-007` Growing Admin lists are paginated.  
`AC-PERF-008` Hot DB paths are indexed and N+1-free.  
`AC-PERF-009` External provider calls never execute inside business DB transaction.  
`AC-PERF-010` Queue depth/latency/retry/failure are observable.  
`AC-PERF-011` Performance optimization cannot weaken security/privacy/history correctness.  
`AC-PERF-012` Critical-path performance regression tests are part of release quality gate.

---

## 37. Финальная доктрина

> **Performance comes from bounded projections, correctly sized derived media, indexed queries, minimal public JavaScript, durable heavy work and measured caching—not from exposing hidden data, mutating originals, bypassing authorization or waiting synchronously on unreliable providers.**
