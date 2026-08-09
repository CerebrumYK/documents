# CODEX_TASKS v2.0

Работать отдельными Pull Request. Каждый этап завершается зелёным CI, миграциями, тестами, документацией и проверкой приватности.

## Task 1 — Auth, MFA и scoped permissions

- исправить локальные callback URL;
- login/logout/recovery;
- обязательная MFA для owner;
- рекомендованная MFA для actress/editor;
- server-side защита `/admin`;
- `profile_access` и resource scope;
- owner bootstrap;
- audit service;
- тесты ролей и профилей.

## Task 2 — Общий каркас самостоятельной админ-консоли

- навигация;
- dashboard полноты;
- guided mode и advanced mode;
- breadcrumbs;
- global save status;
- уведомления;
- undo/restore;
- contextual help drawer;
- responsive/mobile UX;
- E2E для роли actress.

## Task 3 — Профиль, внешние данные и контакты

- profile CRUD;
- translations;
- appearance history;
- measurements;
- privacy controls;
- private contacts encryption;
- autosave;
- validation;
- audit.

## Task 4 — Навыки и шкалы

- categories;
- five-level system scale;
- custom scales;
- CEFR languages;
- segment selector 1–5;
- hide-level toggle;
- assessment source and evidence;
- verification;
- public/PDF rendering;
- tests.

## Task 5 — Образование, организации и люди

- education entries;
- courses;
- teachers;
- organizations;
- related certificates;
- date ranges/current toggle;
- repeatable editor sections.

## Task 6 — Достижения и сертификаты

- type directory;
- issuer organization/person/text;
- issued/valid from/valid until;
- lifetime and not applicable;
- evidence documents;
- public catalog/detail;
- expiry warnings;
- tests.

## Task 7 — Проекты, спектакли, даты и роли

- normalized productions;
- roles;
- events;
- event-role assignments;
- lead/supporting scale;
- required role photo;
- date-grouped public view;
- validation and tests.

## Task 8 — Медиатека и ссылки

- private buckets and RLS;
- upload validation;
- EXIF/GPS stripping;
- image variants;
- local video/audio;
- external URL allow-list and validation;
- full-body/close-up flags;
- rights and consent;
- signed URLs.

## Task 9 — Редактор актёрской анкеты

- document list;
- duplicate/rename/archive;
- tabs: Editor, Template settings, Publish/share;
- accordion sections;
- repeatable records;
- add/duplicate/delete/reorder;
- autosave and revisions;
- two-pane live preview;
- completeness indicator;
- optional blocks;
- field visibility;
- guided prompts;
- mobile layout;
- tests.

## Task 10 — Шаблоны и генерация документов

- professional actor profile;
- compact, film, theatre, dubbing, English résumé;
- PDF/DOCX/HTML;
- full-body and close-up photos;
- videovisitka;
- clickable links;
- QR codes;
- page-break control;
- immutable snapshots;
- visual regression;
- protected links.

## Task 11 — Блог и социальное распространение

- database blog;
- rich editor;
- scheduling;
- social drafts;
- Instagram/Facebook official integrations;
- WhatsApp/Telegram share;
- status and audit;
- manual fallback.

## Task 12 — Творческий ИИ-помощник

- «О себе»;
- project/role descriptions;
- blog/social drafts;
- cover letters;
- casting brief analysis;
- missing-material checklist;
- drafts only;
- permissions and audit;
- privacy tests.

## Task 13 — Центр помощи и справочный ИИ-агент

- help categories/articles/search;
- contextual help by route/form;
- glossary;
- onboarding checklist;
- support tickets/messages;
- diagnostics;
- grounded help agent;
- answer citations;
- action proposals with confirmation;
- escalation;
- retention controls;
- tests for unsupported questions and private data.

## Task 14 — Contact workflow, SEO, analytics

- safe casting inquiries;
- anti-spam;
- notifications;
- sitemap/hreflang/JSON-LD;
- privacy-conscious analytics;
- download/share events.

## Task 15 — Hardening and operations

- CSP;
- pinned CLI/actions;
- background jobs;
- monitoring;
- backups;
- restore drill;
- security scan;
- performance/accessibility.

## Task 16 — Migration and launch

- import questionnaire privately;
- verify all facts;
- add approved media;
- prepare help content;
- user acceptance testing by actress role;
- initial documents;
- production deployment;
- handover.

> Historical source note: current implementation sequencing is governed by DOC-206 and DOC-223.
