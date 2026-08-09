# ENTERPRISE UNIFIED TECHNOLOGY STACK
## DOC-208 — single canonical implementation stack for the actress website platform

**Статус документации:** Canonical / Required  
**Статус реализации:** NOT_IMPLEMENTED / NOT_VERIFIED until application evidence exists  
**Authority:** implementation-technology selection under DOC-003; business/domain/security semantics remain owned by their canonical documents.

---

# 1. Назначение

Этот документ закрывает implementation-level неоднозначность между несколькими технически допустимыми библиотеками и инфраструктурными вариантами.

> **The product has one production implementation stack. Architectural interfaces remain replaceable in principle, but the implementation MUST NOT simultaneously introduce competing ORM, migration, queue, cache, search, styling, validation or test stacks. Any future substitution requires an explicit ADR/governance decision and a migration plan rather than silent parallel adoption.**

Generic examples such as `Prisma/Drizzle/native SQL`, `Playwright or equivalent`, `systemd/container/process manager`, `Redis-backed queue` or `S3-compatible storage` in older documents describe architectural boundaries or alternatives; they do not override the single baseline selected here.

---

# 2. Core production baseline

```text
Architecture        Modular Monolith + Background Worker
Web framework       Next.js App Router / React
Language            TypeScript, strict mode
Runtime             Node.js LTS, exact version pinned in implementation repository
Package manager     pnpm via Corepack, exact package-manager version pinned
Primary DB          PostgreSQL, one pinned supported major
DB access           Drizzle ORM on node-postgres (`pg`)
Migrations          drizzle-kit generated/reviewed SQL + one canonical migration chain
Runtime schemas     Zod
Admin auth          first-party PostgreSQL-backed opaque sessions
Password hashing    Argon2id
Jobs / Outbox       PostgreSQL durable tables + `FOR UPDATE SKIP LOCKED`
Search              PostgreSQL FTS + `pg_trgm`
Cache               Next.js/application generation cache + bounded in-process cache where safe
Media storage       managed filesystem under `/data/kate-actor`
Image processing    Sharp
Video/audio         FFmpeg + ffprobe
PDF                  HTML/DocumentModel → Playwright-managed Chromium print-to-PDF
PDF verification    pdfjs-dist or canonical equivalent selected once and pinned
QR generation       Node QR encoder (`qrcode` baseline)
QR verification     independent ZXing-based decoder (`@zxing/library` baseline)
Forms               React Hook Form for complex interactive forms + Zod contracts
Styling             CSS Modules + CSS Custom Properties/design tokens
Unit/integration    Vitest
Browser/E2E         Playwright
Accessibility       axe-core integrated with Playwright + manual critical checks
Performance         Lighthouse CI + controlled Playwright/browser measurements
Logging             Pino structured JSON
Telemetry           OpenTelemetry APIs/SDK/export pipeline
Reverse proxy       Nginx / HTTPS
Packaging           Docker Compose for web + worker + PostgreSQL production bundle
CI                  GitHub Actions
Interface workflow  GPT-Skill-Enterfaces pinned by DOC-156/DOC-224
```

Exact library/runtime versions are not floating `latest`. Phase 0 of implementation verifies current supported versions against official primary documentation, then pins them in the lockfile/runtime/container manifests before broad implementation begins.

---

# 3. One stack, not one layerless application

Unified stack does **not** mean collapsing architectural boundaries.

Required dependency direction remains:

```text
Presentation
   ↓
Application
   ↓
Domain
   ↓ ports
Infrastructure / Providers
```

The same TypeScript repository contains the web runtime and worker code, while the worker is a separate runtime process using the same domain/application packages.

---

# 4. Node / Next.js / TypeScript

The application MUST use:

- one pinned Node.js LTS runtime;
- one pinned Next.js major/minor compatible with that runtime;
- one React version supplied/required by the selected Next.js release;
- strict TypeScript;
- a single `pnpm-lock.yaml`;
- Corepack/packageManager metadata;
- no npm/yarn lockfile in parallel.

Version changes are controlled upgrades with build/test/migration verification.

---

# 5. PostgreSQL and DB access

PostgreSQL remains the sole authoritative relational database.

Canonical DB access implementation:

```text
Drizzle ORM
+
node-postgres (`pg`)
+
reviewed PostgreSQL SQL for features requiring explicit database semantics
```

Raw SQL is allowed only through the infrastructure/repository layer and remains parameterized and tested.

Do not introduce in parallel:

```text
Prisma
Knex
Sequelize
TypeORM
Kysely
another migration runner
SQLite as behavioral substitute
```

Existing legacy DB access found during Phase 0 is either migrated to Drizzle or isolated behind a temporary compatibility adapter with an explicit removal condition.

---

# 6. PostgreSQL migration doctrine

Use one migration chain under the canonical repository migration directory.

`drizzle-kit` may generate migration SQL, but every migration is reviewed as SQL and remains compatible with DOC-160…165.

PostgreSQL-specific features intentionally used by the system include where justified:

- partial unique indexes;
- CHECK constraints;
- JSONB for bounded flexible payloads;
- `FOR UPDATE SKIP LOCKED`;
- FTS/GIN indexes;
- `pg_trgm` for fuzzy matching/search;
- transactional Audit/Outbox/Job state.

No hidden schema changes outside migrations.

---

# 7. Runtime validation

Zod is the canonical runtime schema layer for:

- Server Action/Route inputs;
- public form submissions;
- environment/configuration validation;
- job payload versions;
- provider/webhook payload normalization;
- AI structured-output schemas where applicable.

TypeScript types alone are never treated as trust-boundary validation.

Do not introduce another general-purpose runtime schema framework in parallel unless an explicit ADR replaces Zod project-wide.

---

# 8. Authentication/session implementation

Admin authentication baseline:

```text
first-party account records in PostgreSQL
+
Argon2id password hashes
+
opaque high-entropy session credentials
+
server-side PostgreSQL session state
+
Secure / HttpOnly / SameSite cookies
```

MFA-ready architecture from DOC-141 remains mandatory.

No Supabase Auth, Firebase Auth, Auth0 or social OAuth is a hidden Admin identity dependency.

Instagram/TikTok OAuth remains provider-account authorization only.

---

# 9. Durable jobs and Outbox

The canonical asynchronous infrastructure is PostgreSQL-backed.

```text
business transaction
→ Audit + Outbox
→ durable PostgreSQL job
→ worker lease/claim
→ typed handler
→ provider/derived operation
→ normalized result/reconciliation
```

Claiming uses transaction-safe PostgreSQL semantics such as `FOR UPDATE SKIP LOCKED`.

No baseline dependency on:

```text
Redis
BullMQ
RabbitMQ
Kafka
cloud queue service
```

A broker may be introduced only by explicit future architecture decision based on measured need; it cannot coexist as an undocumented second job authority.

---

# 10. Search

Canonical search implementation:

```text
PostgreSQL full-text search
+
pg_trgm where fuzzy matching is required
+
explicit public/admin/builder projections
```

No Elasticsearch/OpenSearch/Meilisearch/Typesense baseline.

Search remains derived and rebuildable.

---

# 11. Cache

Canonical baseline:

- Next.js/application data caching where access semantics allow;
- generation/version/tag-based invalidation;
- bounded process-local caches for non-authoritative technical data only;
- PostgreSQL remains authority for durable state.

Redis is not a baseline cache dependency.

Admin/token/private responses are dynamic/private unless explicitly proven cache-safe.

---

# 12. Media stack

Authoritative storage remains filesystem-backed under `/data/kate-actor` with generated internal storage keys.

Canonical processing:

```text
images       Sharp
video/audio  FFmpeg + ffprobe
```

Rules:

- immutable originals;
- derivatives rebuildable;
- no direct filesystem URL exposure;
- contextual crops stored as configuration;
- appearance-changing processing prohibited;
- private/quarantined files never served as public assets.

S3/MinIO/object storage remains a possible future adapter only, not current baseline.

---

# 13. PDF stack

Canonical PDF pipeline:

```text
immutable Revision/Snapshot
→ normalized DocumentModel
→ semantic HTML/CSS
→ Playwright-managed Chromium
→ print-to-PDF
→ PDF semantic verification
```

The renderer MUST preserve:

- selectable/searchable text;
- real hyperlinks;
- deterministic layout;
- QR exact target;
- no screenshot-only pages;
- no Admin/storage/private URL leakage.

PDF tests use one pinned parser/verification library (`pdfjs-dist` baseline) plus raster/QR checks where necessary.

---

# 14. QR stack

Canonical QR implementation:

```text
qrcode encoder
→ SVG preferred / PNG fallback
→ independent ZXing-based decoder
→ exact payload comparison
→ READY
```

Generation and verification MUST use independent code paths/libraries.

PDF release tests decode QR after final PDF rendering, not only before embedding.

---

# 15. Frontend and design stack

Canonical interface implementation:

```text
React / Next.js Server Components where appropriate
Client Components only for real interaction
CSS Modules
CSS Custom Properties / semantic design tokens
React Hook Form for complex forms
Zod schemas
```

No baseline parallel design framework:

```text
Tailwind utility system
Material UI
shadcn-based second component system
another CSS-in-JS design system
```

Existing reusable UI found in the target repository is reconciled into the project design-token/component contract rather than duplicated.

`GPT-Skill-Enterfaces` is a development/review workflow, not a runtime CSS/component dependency.

---

# 16. Interface quality workflow

The consuming application MUST integrate the pinned interface system defined by DOC-156 and DOC-224.

Substantial interface work follows:

```text
interface-build
→ Design Preflight
→ implementation with project evidence
→ runtime/browser verification
→ better-interface full
→ repair HIGH/MEDIUM
→ interface-review final diff
→ repair Introduced/Regression HIGH/MEDIUM
→ deterministic gates
```

The generic skill repository never owns product facts or production tokens/components.

---

# 17. Test stack

Canonical deterministic test tools:

```text
Vitest      unit + application/integration orchestration tests
PostgreSQL  real DB integration target; no SQLite substitution
Playwright  Chromium + Firefox + WebKit E2E/browser/visual tests
axe-core    automated accessibility checks integrated into Playwright
Lighthouse CI / controlled browser metrics for public performance budgets
```

Provider/AI normal CI uses typed fakes/contracts. Real sandbox smoke runs separately where credentials and safe test accounts exist.

---

# 18. Static quality and formatting

Canonical development quality tools:

- TypeScript `tsc --noEmit`/framework typecheck;
- ESLint;
- Prettier;
- dependency/license/security review appropriate to release;
- no second formatter/linter stack unless replacing the canonical tool globally.

---

# 19. Security scanning

Enterprise CI SHOULD include, where supported:

```text
CodeQL for JavaScript/TypeScript
secret scanning / gitleaks-style deterministic scan
dependency vulnerability audit
Trivy or equivalent for production container images
```

Tool output is evidence, not a substitute for DOC-174 security tests and Human review of trust boundaries.

---

# 20. Logging and observability

Canonical application logging:

```text
Pino structured JSON
```

Canonical telemetry instrumentation:

```text
OpenTelemetry
```

Use shared correlation/request/job/attempt IDs.

Never log raw:

- passwords/session tokens;
- OAuth/API secrets;
- private message bodies by default;
- full provider payloads;
- filesystem private paths to public logs.

The actual OTel collector/backend may be deployment-specific, but instrumentation in application code is singular and vendor-neutral.

---

# 21. Production packaging

Canonical target packaging:

```text
Docker Compose
├── web
├── worker
└── postgres

host Nginx
└── HTTPS → 127.0.0.1:3336 web binding
```

Persistent storage is bind-mounted/managed outside replaceable application layers under `/data/kate-actor`.

No Kubernetes baseline.

Migration runs as a controlled one-shot command/job against the same pinned release.

If Phase 0 discovers a currently working production topology that cannot safely be replaced immediately, Codex may use an explicitly documented temporary compatibility phase; final Enterprise acceptance requires one canonical production path or an approved ADR amending this document.

---

# 22. Configuration and version pinning

Before implementation proceeds beyond Phase 0, the implementation repository MUST pin:

- Node runtime;
- pnpm version;
- Next.js/React;
- PostgreSQL image/major;
- all npm dependencies through lockfile;
- FFmpeg/container image/runtime dependencies where packaged;
- Docker image tags/digests according to release policy;
- `GPT-Skill-Enterfaces` immutable SHA.

No `latest` floating production dependencies.

---

# 23. External providers

Provider implementations remain adapters and may be disabled independently:

```text
AI provider
Email provider
official WhatsApp Business provider
Instagram
TikTok
```

One adapter implementation per configured provider capability is active at a time.

A missing production credential prevents live-provider `VERIFIED` status but does not permit fabricating provider success or breaking deterministic core workflows.

---

# 24. Legacy technology reconciliation

Phase 0 MUST inventory all existing technologies.

For each non-canonical implementation dependency:

```text
KEEP_AS_CANONICAL
MIGRATE
TEMPORARY_COMPATIBILITY_ADAPTER
REMOVE
```

A temporary adapter requires:

- owner;
- reason;
- removal condition;
- tests;
- no second source of truth.

No “temporary forever” parallel stack.

---

# 25. Enterprise stack change control

Changing a selected technology requires:

1. explicit ADR/approved decision under DOC-003;
2. reason based on measured requirement/security/operational need;
3. compatibility/migration plan;
4. test and rollback plan;
5. update to DOC-208/DOC-233/DOC-225 and traceability;
6. removal of replaced stack unless intentional transition window is documented.

Convenience or agent preference is not sufficient.

---

# 26. Prohibited production-stack combinations

Without an explicit migration ADR, implementation MUST NOT end with:

- Prisma + Drizzle for parallel domain ownership;
- multiple migration runners;
- PostgreSQL jobs + Redis/BullMQ as competing job authority;
- Postgres FTS + external search engine as competing public search authority;
- filesystem + object storage as ambiguous media Source;
- CSS Modules + a second competing site-wide design system;
- Vitest + Jest as parallel unit-test standards;
- Playwright + Cypress as parallel E2E standards;
- multiple runtime schema frameworks defining the same contracts;
- host/manual deployment and Docker Compose both described as canonical production paths.

---

# 27. Acceptance criteria

`AC-STACK208-001` Application uses one pinned Node/Next/TypeScript/pnpm toolchain.  
`AC-STACK208-002` PostgreSQL is the only authoritative DB and Drizzle/pg is the canonical DB access layer.  
`AC-STACK208-003` One drizzle-kit/SQL migration chain exists; no parallel migration runner remains.  
`AC-STACK208-004` Zod is the common runtime boundary validation stack.  
`AC-STACK208-005` Admin auth uses first-party PostgreSQL sessions and Argon2id baseline.  
`AC-STACK208-006` Jobs/Outbox use PostgreSQL; no Redis/BullMQ/broker baseline is introduced.  
`AC-STACK208-007` Search uses PostgreSQL FTS/pg_trgm; no external search engine baseline exists.  
`AC-STACK208-008` Cache remains derived and does not require Redis.  
`AC-STACK208-009` Media uses managed filesystem + Sharp + FFmpeg/ffprobe with immutable originals.  
`AC-STACK208-010` PDF uses DocumentModel/HTML + Playwright Chromium and semantic verification.  
`AC-STACK208-011` QR uses one encoder plus an independent decoder and final-artifact verification.  
`AC-STACK208-012` Frontend uses the canonical CSS Modules/tokens/forms stack without competing site-wide framework.  
`AC-STACK208-013` Vitest/Playwright/axe/Lighthouse form the canonical deterministic quality toolchain.  
`AC-STACK208-014` Pino/OpenTelemetry provide one application observability instrumentation path.  
`AC-STACK208-015` Docker Compose + host Nginx is the single target production packaging path.  
`AC-STACK208-016` Exact runtime/dependency versions are verified and pinned before broad implementation.  
`AC-STACK208-017` Legacy non-canonical technology is removed or explicitly bounded as a temporary compatibility adapter.  
`AC-STACK208-018` No parallel implementation stack can be added without an approved ADR/migration.  
`AC-STACK208-019` GPT-Skill-Enterfaces integration remains pinned and project-specific design evidence stays in the application repository.  
`AC-STACK208-020` Full stack remains subordinate to all Security/Privacy/domain authority invariants.

---

# 28. Финальная доктрина

> **The actress platform is implemented as one TypeScript/Next.js modular monolith with a TypeScript worker, PostgreSQL authority, PostgreSQL durable jobs/search, filesystem media, a single UI/test/validation/observability toolchain and one reproducible Docker Compose production topology. Architectural abstractions preserve future replaceability, but current implementation simplicity and reliability come from exactly one selected production implementation per concern.**
