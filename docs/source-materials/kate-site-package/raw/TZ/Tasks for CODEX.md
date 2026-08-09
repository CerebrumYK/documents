# Задание Codex

Реализуй полностью спецификацию автоматизации разработки интерфейсов для проекта:

- Целевой репозиторий: `https://github.com/CerebrumYK/Kate-Actor-s-website`
- Репозиторий интерфейсных skills: `https://github.com/CerebrumYK/GPT-Skill-Enterfaces`

## Главный документ

Перед началом работы прочитай и выполни полностью:

`docs/CODEX_NEXT_TASK.md`

из репозитория:

`CerebrumYK/GPT-Skill-Enterfaces`

Если документация ещё не объединена в `main`, используй ветку:

`agent/codex-interface-automation-docs`

Также обязательно прочитай все документы, на которые ссылается `CODEX_NEXT_TASK.md`, включая:

- `docs/CODEX_PROJECT_INTEGRATION_SPEC.md`
- `docs/INTERFACE_AUTOMATION_LIFECYCLE.md`
- `docs/CHECKLIST_DESIGN_INTEGRATION.md`
- `docs/NO_SIGNUPS_PRINCIPLES.md`
- `docs/KATE_ACTOR_WEBSITE_IMPLEMENTATION_PROFILE.md`
- `docs/OPERATIONS_AND_MAINTENANCE.md`
- `docs/upstream-coverage.md`
- корневой `AGENTS.md`
- все восемь каталогов `skills/*`

Перед изменениями целевого проекта обязательно проверь существующий:

`https://github.com/CerebrumYK/Kate-Actor-s-website/pull/47`

Если его ветку можно безопасно использовать — обнови её до полной реализации новой спецификации. Если PR #47 устарел или мешает чистой реализации — создай новую ветку от актуального `main`, реализуй полный функционал и укажи, что PR #47 superseded.

## Требуемый результат

Не ограничивайся анализом или документацией.

Полностью реализуй в `Kate-Actor-s-website` автоматический lifecycle разработки интерфейсов:

`распознавание UI-задачи → recon → Design Preflight → применение domain skills → реализация → browser verification → better-interface full → автоматическое исправление HIGH/MEDIUM → interface-review → repository quality gates → отчёт → draft PR`

После интеграции пользователь не должен каждый раз вручную перечислять:

- `better-accessibility`
- `better-layout`
- `better-writing`
- `better-typography`
- `better-colors`
- `better-ui`

Для любой явной задачи по UI/UX Codex должен автоматически активировать соответствующий workflow через правила проекта.

## Source of truth

`CerebrumYK/GPT-Skill-Enterfaces` является единственным generic source of truth для interface skills.

В `Kate-Actor-s-website` должна использоваться полная pinned-копия skills под:

`.agents/skills/`

с фиксацией immutable Git commit SHA.

Нельзя использовать `main` как плавающую версию.

Нельзя вручную поддерживать сокращённые или отличающиеся копии generic skills внутри целевого проекта.

## Обязательная автоматизация

Реализуй как минимум:

```text
.agents/skills/
  better-interface/
  interface-review/
  better-accessibility/
  better-layout/
  better-writing/
  better-typography/
  better-colors/
  better-ui/

.interface-skills-version.json

scripts/
  interface-skills-sync.mjs
  interface-skills-verify.mjs
  interface-change-detect.mjs
  interface-quality.mjs

docs/
  interface-development.md
  interface-quality-automation.md

.github/workflows/
  interface-quality.yml
```

Добавь команды проекта, эквивалентные:

```text
interface:skills:sync
interface:skills:verify
interface:detect
interface:quality
```

## Design Preflight

Для каждой существенной задачи интерфейса автоматически определяй:

- affected surfaces;
- existing UI patterns to reuse;
- components;
- user flows;
- responsive states;
- loading;
- empty;
- no-results;
- error;
- success;
- disabled/read-only;
- focus-visible;
- selected/expanded;
- permissions;
- destructive states;
- локализацию RU/KZ/EN;
- long-content states;
- accessibility behavior.

Используй `https://www.checklist.design/` как внешний coverage/reference слой по модели:

`Components → Flows → Topics`

но не как runtime/CI dependency и не как источник новой бизнес-функциональности.

## Friction / identity / privacy

Используй принципы из:

`https://nosignups.net`

только как дополнительный low-friction/privacy review.

Для каждого релевантного flow различай:

- `REQUIRED` identity gate;
- `OPTIONAL`;
- `UNNECESSARY`;
- `NONE`.

Никогда не удаляй обязательную authentication/authorization, permissions, privacy boundary или confirmation ради уменьшения friction.

Для `Kate-Actor-s-website` защищёнными остаются как минимум:

- `/admin`;
- private casting workspace;
- private attachments;
- originals/private media;
- операции публикации;
- действия, требующие полномочий администратора;
- destructive и privacy-sensitive операции.

Публичный portfolio/content не должен получать искусственный signup wall.

## Интерфейсный review loop

После реализации обязательно:

1. Выполни runtime/browser verification.
2. Запусти `better-interface` в `full` mode по изменённому flow.
3. Исправь все подтверждённые `HIGH`.
4. Исправь все подтверждённые `MEDIUM`, если нет конкретного документированного ограничения.
5. Повтори соответствующие проверки.
6. Выполни `interface-review` по реальному diff/branch.
7. Исправь `Introduced` и `Regression` HIGH/MEDIUM.
8. Не превращай `Pre-existing` проблемы в бесконтрольное расширение scope.

## Существующие правила проекта

Не ослабляй существующие требования `Kate-Actor-s-website`, включая:

- Next.js App Router;
- React;
- Tailwind CSS;
- strict TypeScript;
- PostgreSQL server-only;
- protected administrator mutations;
- RU/KZ/EN;
- WCAG 2.2 AA;
- privacy protections для несовершеннолетнего субъекта;
- приватность original/private media;
- безопасные destructive actions;
- AI preview/confirmation boundaries;
- существующие server/client boundaries;
- существующую архитектуру и design system.

Generic skills дополняют эти правила, но не заменяют их.

## Проверки

Используй актуальные команды из репозитория.

На текущем baseline необходимо проверить как минимум:

```sh
corepack pnpm format:check
corepack pnpm lint
corepack pnpm check:unused
corepack pnpm typecheck
corepack pnpm test
corepack pnpm build
corepack pnpm test:e2e
```

Также выполни database/migration smoke checks, если они требуются текущим `AGENTS.md`.

Отдельно проверь:

- pinned skill integrity;
- отсутствие drift;
- корректность всех Markdown references;
- UI-change detection;
- explicit UI task activation;
- browser verification;
- accessibility/axe;
- отсутствие зависимости CI от `checklist.design` и `nosignups.net`.

## Автономность выполнения

Не останавливайся после плана.

Продолжай самостоятельно:

`inspect → implement → run checks → review → repair → rerun checks → publish draft PR`

Не запрашивай подтверждение между обычными техническими этапами.

Запрашивай решение только если возникает действительно неразрешимый по репозиторию продуктовый выбор, где безопасный default существенно изменит бизнес-поведение.

## Завершение

Создай или обнови draft PR и в финальном отчёте укажи:

- использованную ветку;
- номер PR;
- immutable SHA `GPT-Skill-Enterfaces`;
- какие файлы добавлены/изменены;
- как работает автоматическая активация UI lifecycle;
- как реализован Design Preflight;
- как используются Checklist Design и NoSignups;
- как работает skill sync и drift detection;
- какие browser/accessibility проверки выполнены;
- результаты всех repository quality gates;
- что было исправлено после `better-interface`;
- результаты `interface-review`;
- оставшиеся `Not verified` или blockers;
- был ли PR #47 обновлён или superseded.

Задача считается завершённой только после полной реализации, проверки и публикации draft PR.