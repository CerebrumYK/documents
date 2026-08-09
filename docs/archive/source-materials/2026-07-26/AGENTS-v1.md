# AGENTS.md — historical v1

## Project Goal

Build and maintain a production-grade multilingual portfolio, casting profile, media kit, blog, and administration system for an actress working in film, theatre, and dubbing.

## Source of Truth

- Read `docs/TZ.md` and `CODEX_TASKS.md` before implementation.
- Database records are the source of truth for public pages and generated documents.
- Do not duplicate profile facts in components, seed files, and templates.
- Do not invent missing roles, dates, awards, skills, credits, translations, or contact information.

## Privacy Rules

- Never expose sensitive personal data, private contacts, school/routine information, precise location, private documents, tokens, or source DOCX publicly unless an explicit field-level policy permits it.
- Keep private contacts in protected storage with server-side authorization.
- Remove EXIF/GPS metadata from uploaded images intended for publication.
- Do not commit real personal data, source documents, production exports, or secrets.
- `private-import/` must stay outside Git.
- Public JSON-LD, metadata, logs, analytics, and errors must not leak private values.
- Closed casting documents use revocable, expiring, unguessable access.

## Engineering Rules

- TypeScript strict.
- Prefer server-side data access and least-privilege queries.
- Validate all inputs on the server with shared schemas.
- Create migrations for every schema change.
- Keep dependencies minimal and production-ready.
- No arbitrary HTML/CSS/JavaScript execution in CMS.
- Use official provider APIs; no scraping/cookie extraction/auth bypass.
- Keep external integrations behind interfaces/adapters.
- Heavy media/import work uses background jobs.
- Preserve original imported text when normalization may alter meaning/spelling.

## UI Rules

- Mobile-first, keyboard accessible and screen-reader usable.
- Target WCAG 2.2 AA.
- No autoplay audio/video.
- No public comments or public registration baseline.
- Admin destructive actions require confirmation.
- Public content supports `ru`, `kk`, `en` with explicit fallback/warnings.

## Tests Required

- unit tests for validation/business rules;
- integration tests for database/import/document generation;
- Playwright E2E for critical journeys;
- accessibility checks;
- security regression tests.

Historical completion commands included:

```bash
pnpm lint
pnpm typecheck
pnpm test
pnpm test:e2e
```

## Documentation

Historically required maintenance of README, architecture, data model, privacy, admin guide and deployment docs.

## Completion Report

Every task should report implemented behavior, changed files, migrations, tests/results, security/privacy impact, limitations and next task.

> Historical source note: wording has been privacy-normalized for safe public archival. Current engineering/governance authority is DOC-003/DOC-202/DOC-206/DOC-223.
