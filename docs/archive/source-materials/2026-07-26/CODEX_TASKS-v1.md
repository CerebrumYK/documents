# CODEX_TASKS.md — historical v1

Работать строго по этапам. Каждый этап завершается зелёным CI, обновлённой документацией и отдельным commit/PR.

## Task 0 — Repository bootstrap

- Next.js + TypeScript strict;
- pnpm;
- lint/format/typecheck;
- Vitest, Playwright, axe;
- Dockerfile/Compose;
- `.env.example`;
- safe `.gitignore` including `private-import/`;
- GitHub Actions;
- locales `ru`, `kk`, `en`;
- public/admin layouts.

## Task 1 — Authentication, roles, and audit

- Auth;
- MFA;
- admin users, roles, permissions;
- server-side admin protection;
- audit log;
- anonymous/role boundary tests.

## Task 2 — Profile domain

- profiles/translations;
- appearance history;
- measurements;
- private contacts;
- public page/admin CRUD;
- field visibility;
- tests.

## Task 3 — Education, skills, languages, awards

- normalized entities;
- sorting/history;
- public sections/admin forms;
- translation completeness;
- tests.

## Task 4 — Projects and credits

- project types;
- people/organizations;
- theatre/film/dubbing views;
- filters/events/dates;
- embargo/rights;
- media;
- tests.

## Task 5 — Media library

- image/video/audio/document upload;
- MIME/signature validation;
- EXIF/GPS stripping;
- private originals;
- derivatives;
- rights/consent;
- signed/scoped delivery;
- galleries;
- audit/tests.

## Task 6 — Dubbing and voice portfolio

- voice profile;
- audio categories;
- waveform/language filters;
- public/private playback;
- no autoplay;
- tests.

## Task 7 — DOCX import

- private source upload;
- extract paragraphs/tables/images;
- mapping preview;
- preserve original strings;
- warnings/manual confirmation;
- private classification for sensitive data;
- import report;
- idempotency;
- redacted fixtures only.

**Never add the real source document to Git.**

## Task 8 — Casting document generator

- versioned templates;
- PDF/DOCX/print HTML;
- language/field selection;
- QR;
- immutable generated versions;
- visual regression.

## Task 9 — Protected casting links

- expiring unguessable links;
- optional access code;
- revoke;
- noindex/no-cache;
- access audit;
- tests.

## Task 10 — Blog and press

- drafts/rich content;
- scheduling;
- categories/tags/media/translations/SEO;
- related projects;
- version history/tests.

## Task 11 — Instagram integration

- official API only;
- encrypted server-side credentials;
- sync/dedupe/moderation;
- manual link/embed fallback;
- graceful failure;
- mocked provider tests.

## Task 12 — Contact workflow

- professional contact form;
- anti-spam;
- safe attachments;
- notifications;
- admin inbox/statuses/internal notes;
- retention/tests.

## Task 13 — SEO, accessibility, and performance

- sitemap/robots/canonical/hreflang;
- privacy-safe JSON-LD;
- Open Graph;
- keyboard/screen reader;
- subtitles/transcripts;
- performance/error/empty states;
- tests.

## Task 14 — Analytics, export, backup, operations

- privacy-conscious analytics;
- downloads/media events;
- export;
- backup/restore drill;
- monitoring/alerts;
- operations dashboard.

## Task 15 — Initial content migration and launch

- import source through private workflow;
- verify facts manually;
- resolve ambiguities;
- add approved media/translations;
- create initial documents;
- security review/full E2E;
- production deployment;
- handover.

## Historical phase gate template

```text
Implemented:
Changed files:
Database migrations:
Tests run:
Results:
Privacy/security checks:
Known limitations:
Questions requiring owner decision:
Recommended next task:
```

> Historical source note: current execution sequencing is DOC-206 and DOC-223; this file is retained only for provenance.
