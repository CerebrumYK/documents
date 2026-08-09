# Source Materials Archive

Этот каталог хранит исторические материалы, подготовленные до текущей canonical документации `DOC-*`.

## Правила использования

1. Файлы здесь являются **историческими источниками и evidence**, а не текущим source of truth.
2. При конфликте применяются `docs/governance.md`, текущие owning-domain документы и `docs/final/final-technical-specification.md`.
3. Исторические документы не должны автоматически возвращать устаревшие решения в реализацию (например, прежние Supabase/RLS assumptions), если они были позднее заменены canonical архитектурой.
4. Реальные исходные документы с персональными данными, production exports, secrets и private-import материалы **не коммитятся** в публичный repository.

## Импортированные материалы

### 2026-07-26
- `README-source-package.md`
- `AGENTS-v1.md`
- `CODEX_TASKS-v1.md`

### 2026-07-27
- `01-repository-audit.md`
- `03-data-relationships-v2.md`
- `04-codex-tasks-v2.md`
- `05-agents-v2.md`
- `06-ux-reference-analysis.md`

## Не импортированы намеренно

- исходная актёрская анкета DOCX — private source material;
- DOCX-копии ТЗ — бинарные дубликаты Markdown и/или документы, потенциально содержащие персональные исходные данные;
- старые полные TZ v1/v2 — сохраняются вне public Git до отдельной redaction/review, потому что исторические версии могут содержать персональные сведения и устаревшую архитектуру.

Если оригинал требуется для forensic/history review, использовать защищённое локальное хранилище, а не public Git.
