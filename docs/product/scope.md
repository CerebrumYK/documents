# PRODUCT SCOPE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Границы продукта и область реализации

**Целевой файл:** `docs/product/scope.md`  
**Документ:** DOC-014  
**Статус:** ✅ Completed  
**Тип:** Product Foundation

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`

---

# 1. Назначение документа

Настоящий документ определяет границы целевого продукта и фиксирует:

1. что обязательно входит в продукт;
2. какие функции относятся к текущей целевой архитектуре;
3. какие возможности сознательно не входят в Scope;
4. какие возможности могут быть реализованы позднее;
5. какие функции прямо запрещено вводить без изменения Product Scope;
6. какие существующие функции должны быть сохранены;
7. какие legacy-модели должны быть преобразованы;
8. какие внешние системы могут использоваться;
9. где заканчивается ответственность сайта;
10. какие решения требуют отдельного изменения продуктовой документации.

Основная цель:

> **не допустить неконтролируемого расширения продукта и смешения профессионального сайта актрисы с CRM агентства, casting marketplace, социальной сетью или автономной AI-системой.**

---

# 2. Определение Scope

`In Scope` означает:

- функция является частью целевой продуктовой модели;
- для неё должны быть разработаны UX, Domain Model, Architecture, Tests и Acceptance Criteria;
- она должна быть учтена при планировании реализации.

`Out of Scope` означает:

- функция сознательно не является частью текущего целевого продукта;
- она не должна реализовываться как скрытый побочный эффект другой задачи;
- для её добавления требуется отдельное изменение Product Scope.

`Future Scope` означает:

- архитектура MAY учитывать возможность будущего расширения;
- реализация сейчас не обязательна;
- наличие будущего направления не должно усложнять MVP без необходимости.

---

# 3. Главная область продукта

В Scope входит создание единой системы:

```text
Professional Actor Profile
+
Public Professional Website
+
Admin CMS
+
Media Management
+
Questionnaire Platform
+
Public Questionnaire Builder
+
Casting Workflow
+
Professional Feedback
+
Notifications
+
Opportunity Tracking
+
AI Assistance
+
Virtual Operator
+
Marketing Analytics
+
Site Theme Management
```

---

# 4. IN SCOPE — Actor Profile

В продукт MUST входить единый Actor Profile.

Он должен включать и предоставлять структурированный доступ минимум к:

- имени;
- профессиональному позиционированию;
- biography;
- location;
- профессиональным параметрам;
- навыкам;
- языкам;
- публичным и внутренним metadata.

Actor Profile является Single Source of Truth.

---

# 5. IN SCOPE — Skills

В Scope входит:

- ведение Skills;
- категории;
- уровни;
- сортировка;
- использование в Profile;
- Prepared Questionnaire;
- Public Builder;
- Casting Analysis;
- Public Search.

---

# 6. IN SCOPE — Languages

В Scope входит:

- структурированное хранение Languages;
- Language Level;
- CEFR;
- отображение на сайте;
- использование в Questionnaire;
- Public Builder;
- Casting matching;
- международном Customer Journey.

---

# 7. IN SCOPE — Media Library

В Scope входит единая Media Library.

Поддерживаемые базовые типы:

- image;
- video;
- audio;
- document.

Должны поддерживаться:

- browser upload;
- локальные/серверные источники, где уже поддерживаются проектом;
- существующие внешние URL-механизмы;
- metadata;
- references;
- derived files;
- usage tracking.

---

# 8. IN SCOPE — Immutable Originals

В Scope входит обязательный принцип:

- Original Asset сохраняется;
- derivatives создаются отдельно;
- crop не перезаписывает Original;
- повторное использование не создаёт необязательных физических копий.

---

# 9. IN SCOPE — Main Portfolio

В Scope входит самостоятельный Portfolio Domain.

Минимальные категории:

- Full Body;
- Close-Up;
- Waist;
- Profile;
- Three Quarter;
- Staged;
- Appearance;
- Additional;
- Other.

---

# 10. IN SCOPE — Primary Portfolio Photos

Система MUST поддерживать:

- Primary Full Body;
- Primary Close-Up.

Они используются как default для профессиональных представлений и Questionnaires.

---

# 11. IN SCOPE — Portfolio Public UI

В Scope входят:

- Public Portfolio Page;
- фильтры;
- responsive gallery;
- lightbox/preview;
- captions;
- dates;
- accessibility;
- связь с Questionnaire и CTA.

---

# 12. IN SCOPE — Emotional Portfolio

В Scope входит самостоятельный Emotional Portfolio Domain.

Он включает:

- Emotional Portfolio Sessions;
- mandatory Shooting Date;
- photos;
- captions;
- emotion labels;
- photographer/studio;
- public presentation;
- Questionnaire integration.

---

# 13. IN SCOPE — Emotional Grid

В Scope входит Emotional Grid Builder.

Поддерживаемые layouts:

- 1×2;
- 1×3;
- 1×4;
- 2×2;
- 2×3;
- 2×4;
- 3×2;
- 3×3;
- 3×4;
- 4×2;
- 4×3;
- 4×4.

---

# 14. IN SCOPE — Emotional Grid Editing

В Scope входят:

- photo selection;
- cell assignment;
- crop;
- scale;
- position;
- orientation correction;
- verification;
- preview;
- finalization;
- multiple grids;
- primary grid;
- derived renders.

---

# 15. IN SCOPE — Emotional Grid Safety

В Scope входит строгий запрет:

- изменения лица;
- generative expansion;
- retouching;
- background generation;
- изменения внешности;
- body manipulation.

---

# 16. IN SCOPE — Projects

В Scope входит самостоятельный Project Domain.

Project должен поддерживать:

- title;
- project type;
- roles;
- descriptions;
- media;
- videos;
- dates;
- achievements;
- public presentation;
- Questionnaire selection.

---

# 17. IN SCOPE — Roles

В Scope входят:

- Role как дочерняя сущность Project;
- role-specific content;
- role-specific photos;
- role-specific video, где применимо.

Сохраняется существующая возможность использования role portfolio.

---

# 18. IN SCOPE — Performances

В Scope входит хранение дат выступлений/показов там, где они имеют смысл.

Например:

- theatre;
- live performance;
- event.

---

# 19. IN SCOPE — Achievements

В Scope входят:

- профессиональные достижения;
- награды;
- сертификации;
- связь с Project или Profile;
- публичное представление;
- Questionnaire inclusion.

---

# 20. IN SCOPE — Training

В Scope входит самостоятельный Training Domain.

Поддерживаются:

- Course;
- Training;
- Masterclass;
- Intensive;
- Seminar;
- School;
- Laboratory;
- Qualification;
- Other Education.

---

# 21. IN SCOPE — Training Media

Training MAY иметь:

- certificates;
- documents;
- images;
- videos;
- links.

---

# 22. IN SCOPE — Migration of Legacy Training

В Scope входит migration существующих образовательных записей из legacy Project-type structures.

Migration должна быть:

- previewable;
- idempotent;
- non-destructive;
- confirmed where classification is ambiguous.

---

# 23. IN SCOPE — Professional Video

В Scope входят:

- Video Intro;
- Showreel;
- additional professional video;
- project video;
- external video URL;
- local media where supported.

---

# 24. IN SCOPE — Professional Audio

В Scope входят:

- Audio Intro;
- Voice Sample;
- dubbing/voice materials;
- other professional audio.

---

# 25. IN SCOPE — Structured External Links

В Scope входит структурированный справочник External Links.

Минимальная UI-модель:

| № | Описание | URL |

Поддерживаются:

- validation;
- sorting;
- enable/disable;
- Questionnaire inclusion;
- Public Builder inclusion;
- clickable output.

---

# 26. IN SCOPE — QR Codes

В Scope входит генерация QR Code для разрешённых профессиональных ссылок.

QR должен поддерживаться минимум в Questionnaire PDF.

Для каждого разрешённого link/media URL система должна позволять:

- clickable hyperlink;
- QR Code;
- включение/отключение QR;
- проверку target;
- безопасную генерацию.

QR Code не должен кодировать Private/Admin URL.

---

# 27. IN SCOPE — Contact Directory

В Scope входит единый Contact Directory.

Минимальные Contact Types:

- Phone;
- Mother Phone;
- Father Phone;
- WhatsApp;
- Telegram;
- Instagram;
- TikTok;
- YouTube;
- Other.

---

# 28. IN SCOPE — Independent Contact Visibility

Для Contact должны независимо контролироваться:

- Admin visibility;
- Public Website visibility;
- Prepared Questionnaire availability;
- Public Builder availability.

---

# 29. IN SCOPE — Public Contacts Page

В Scope входит отдельная Public Contacts Page.

Она должна включать:

- разрешённые Contacts;
- professional CTA;
- Feedback Form.

---

# 30. IN SCOPE — Prepared Questionnaires

В Scope входит полноценная Questionnaire Platform.

Минимальные типы:

- Compact;
- Extended;
- Casting;
- Custom.

---

# 31. IN SCOPE — Questionnaire Revisions

Prepared Questionnaires должны поддерживать:

- Draft Revision;
- Ready;
- Published Revision;
- Archive;
- immutable Published Revision;
- revision history.

---

# 32. IN SCOPE — Questionnaire Block Builder

Администратор должен иметь возможность управлять:

- enabled blocks;
- block order;
- item selection;
- max items;
- HTML visibility;
- PDF visibility;
- page break behaviour.

---

# 33. IN SCOPE — Questionnaire Blocks

Минимальный набор:

- Basic Info;
- Full Body;
- Close-Up;
- Appearance;
- Professional Info;
- Skills;
- Languages;
- Projects;
- Roles;
- Performances;
- Emotional Grid;
- Training;
- Achievements;
- Video;
- Audio;
- External Links;
- Additional Photos;
- Contacts.

---

# 34. IN SCOPE — HTML Questionnaire

В Scope входит HTML representation Prepared Questionnaire.

HTML и PDF должны использовать согласованный content composition.

---

# 35. IN SCOPE — PDF Questionnaire

В Scope входит server-side PDF generation.

PDF должен поддерживать:

- localization;
- clickable links;
- contacts;
- QR;
- images;
- Emotional Grid;
- dates;
- pagination;
- safe layout.

---

# 36. IN SCOPE — Primary Questionnaire

В Scope входит выбор одной основной рекомендованной Prepared Questionnaire.

---

# 37. IN SCOPE — Download Picker

Public Site MUST поддерживать выбор доступной PDF Questionnaire.

В Download Picker могут отображаться:

- Compact;
- Extended;
- public Casting-specific;
- другие разрешённые variants.

---

# 38. IN SCOPE — Public Questionnaire Builder

В Scope входит самостоятельное формирование анкеты кастинг-специалистом.

Это ключевой Public capability.

---

# 39. IN SCOPE — Public Builder Templates

Минимальные templates:

- Quick;
- Standard;
- Extended;
- Casting.

---

# 40. IN SCOPE — Public Builder Selection

Кастинг-специалист может выбирать:

- blocks;
- Projects;
- Skills;
- Languages;
- Training;
- Additional Photos;
- Emotional Grid;
- Video;
- Audio;
- Links;
- Contacts;

только из разрешённого набора.

---

# 41. IN SCOPE — Public Builder Preview

В Scope входит Preview пользовательского документа до генерации.

---

# 42. IN SCOPE — Public Builder PDF

В Scope входит создание temporary Custom PDF.

Он должен:

- формироваться server-side;
- использовать revalidated data;
- содержать дату;
- содержать official profile URL;
- поддерживать QR, если разрешён template.

---

# 43. IN SCOPE — Public Builder без регистрации

Для стандартного сценария обязательная регистрация кастинг-специалиста не требуется.

---

# 44. IN SCOPE — Public Builder Security

В Scope входят:

- server-side permissions;
- TTL;
- rate limiting;
- item revalidation;
- safe filenames;
- protection from arbitrary IDs.

---

# 45. IN SCOPE — Casting Quick View

В Scope входит компактное professional representation для первичной оценки.

Quick View использует существующий Profile data и не создаёт новую Master Data копию.

---

# 46. IN SCOPE — Public Search

В Scope входит поиск по опубликованным профессиональным сведениям.

Минимально:

- Skills;
- Languages;
- Projects;
- Roles;
- Training;
- public professional facts.

---

# 47. IN SCOPE — Contextual CTA

В Scope входят профессиональные CTA:

- Invite to Casting;
- Offer Role;
- Request Materials;
- Collaboration;
- Ask Question.

---

# 48. IN SCOPE — Feedback

В Scope входит Public Feedback System.

Она должна поддерживать профессиональные обращения.

---

# 49. IN SCOPE — Feedback Types

Минимально:

- Casting Invitation;
- Role Offer;
- Collaboration;
- Questionnaire Request;
- Additional Materials;
- Question;
- Comment;
- Other.

---

# 50. IN SCOPE — Feedback Attachments

В Scope входят private attachments с проверкой:

- MIME;
- type;
- size;
- security;
- access control.

---

# 51. IN SCOPE — Feedback Admin Inbox

Администратор должен иметь:

- unread state;
- workflow status;
- priority;
- assignee, если включён;
- search;
- filters;
- notes;
- history;
- next action.

---

# 52. IN SCOPE — Feedback to Casting

В Scope входит создание Casting из Feedback после explicit human action.

---

# 53. IN SCOPE — Internal Notifications

В Scope входит внутренний Notification Center.

---

# 54. IN SCOPE — WhatsApp Notifications

В Scope входит внешний WhatsApp notification channel при наличии корректно настроенного официального/разрешённого подключения.

WhatsApp не является обязательным для работоспособности Feedback.

---

# 55. IN SCOPE — Notification Failure Handling

В Scope входят:

- status;
- retry;
- error visibility;
- idempotency;
- Quiet Hours.

---

# 56. IN SCOPE — Casting Workspace

В Scope входит закрытый Admin Casting Workspace.

Он должен поддерживать:

- manual creation;
- creation from Feedback;
- deadlines;
- project;
- role;
- source text;
- source assets;
- analysis;
- linked Questionnaire;
- outcome.

---

# 57. IN SCOPE — Casting Assets

В Scope входят private:

- screenshots;
- PDFs;
- briefs;
- role cards;
- images;
- documents.

---

# 58. IN SCOPE — AI Casting Analysis

В Scope входит AI-assisted analysis.

Он должен:

- extract requirements;
- identify missing information;
- compare with Profile;
- produce recommendations;
- retain confidence;
- retain source references.

---

# 59. IN SCOPE — Casting Analysis Revisions

В Scope входит история повторных анализов.

Новый анализ не должен бесследно перезаписывать предыдущий.

---

# 60. IN SCOPE — Casting Questionnaire Draft

На основании подтверждённых рекомендаций система должна уметь создать Draft Casting Questionnaire.

---

# 61. IN SCOPE — Opportunity Pipeline

В Scope входит отслеживание профессионального результата.

Минимальные стадии:

- New;
- Qualified;
- Materials Requested;
- Self-Tape Requested;
- Audition;
- Callback;
- Offer;
- Booked;
- Closed — Not Selected;
- Withdrawn.

---

# 62. IN SCOPE — Attribution

В Scope входит базовое определение источника профессионального Journey.

Например:

- direct;
- search;
- social;
- campaign;
- shared questionnaire;
- casting-specific link.

---

# 63. IN SCOPE — Conversion Analytics

В Scope входит измерение минимум:

- Profile engagement;
- Video Play;
- Questionnaire View;
- PDF Download;
- Public Builder Open;
- Custom PDF Generation;
- Casting CTA;
- Feedback;
- Casting;
- Opportunity Result.

---

# 64. IN SCOPE — Virtual Portfolio Operator

В Scope входит внутренний Virtual Operator.

Он работает как:

- Observation Engine;
- Suggestion Engine;
- Draft Assistant;
- Exception Detector.

---

# 65. IN SCOPE — Virtual Operator Content Analysis

Оператор может:

- анализировать новые media;
- предлагать classification;
- искать duplicates;
- выявлять missing metadata;
- находить broken links;
- выявлять stale derived assets;
- предлагать improvements.

---

# 66. IN SCOPE — Virtual Operator Emotional Grid Assistance

Он MAY:

- предложить source photos;
- предложить crop;
- предложить layout.

Human confirmation остаётся обязательным.

---

# 67. IN SCOPE — Exception-Driven Dashboard

Admin Dashboard должен концентрироваться на:

- новых opportunities;
- deadline;
- unread Feedback;
- incomplete records;
- stale PDFs;
- broken links;
- pending confirmation;
- service problems.

---

# 68. IN SCOPE — BB Assistant

В Scope входит BB Assistant / ИИ-помощник для текстов.

Он должен поддерживать draft generation минимум для:

- biography;
- Portfolio descriptions;
- Project descriptions;
- Training descriptions;
- cover letters;
- Casting responses;
- Role responses;
- Questionnaire texts;
- Social captions;
- Custom professional text.

---

# 69. IN SCOPE — BB Assistant Human Approval

AI Draft:

- editable;
- versioned;
- not automatically published;
- not automatically sent.

---

# 70. IN SCOPE — BB Assistant Fact Safety

BB Assistant должен использовать подтверждённые Profile facts.

Он не должен придумывать:

- roles;
- skills;
- awards;
- Projects;
- training;
- professional experience.

---

# 71. IN SCOPE — Site Themes

В Scope входит управление публичным визуальным оформлением.

---

# 72. IN SCOPE — AI Theme Builder

Администратор может описать желаемый стиль текстом и получить Theme Proposal.

---

# 73. IN SCOPE — Structured Theme Configuration

AI output должен преобразовываться в контролируемые Theme Tokens.

Не arbitrary executable code.

---

# 74. IN SCOPE — Manual Theme Editing

Все основные Theme parameters должны быть доступны для manual editing.

---

# 75. IN SCOPE — Locked Theme Tokens

Администратор может фиксировать параметры перед AI regeneration.

---

# 76. IN SCOPE — Hero Configuration

В Scope входят:

- Portfolio image;
- Media image;
- solid;
- gradient;
- decorative composition;
- overlay;
- crop;
- positioning.

---

# 77. IN SCOPE — Theme Preview

Обязательны:

- Desktop;
- Tablet;
- Mobile.

---

# 78. IN SCOPE — Theme Revision & Rollback

Published Theme должна иметь history и rollback.

---

# 79. IN SCOPE — Temporary Themes

Допускаются временные Theme с:

- start date;
- end date;
- fallback.

---

# 80. IN SCOPE — Multilingual Public Site

В Scope входит существующая и целевая multilingual architecture.

Выбранный язык должен последовательно использоваться:

- в Profile;
- Public Pages;
- Questionnaire;
- Public Builder;
- PDF;
- CTA;
- captions.

---

# 81. IN SCOPE — Blog

Существующий Blog остаётся частью продукта.

Он должен сохраняться при развитии архитектуры.

Blog не должен смешиваться с Projects.

---

# 82. IN SCOPE — Help Center

Существующий Help Center / support functionality должен сохраняться.

Он относится к административной помощи, а не к Public Professional Profile.

---

# 83. IN SCOPE — Social Publishing

Существующая официальная OAuth-интеграция Instagram/TikTok должна учитываться и не регрессировать.

Она может использовать BB Assistant drafts после human approval.

---

# 84. IN SCOPE — Existing Role Photo Logic

Существующий функционал role-specific photos должен сохраняться.

Целевая модель должна поддерживать существующее ограничение/правила отображения после проверки модульной документации.

---

# 85. IN SCOPE — Home “В образе”

Существующий carousel/представление role imagery должен быть сохранён как часть публичного presentation layer, если он не противоречит будущему UX.

Он не является отдельным Source of Truth.

---

# 86. IN SCOPE — Accessibility

Accessibility входит в Scope всей системы:

- keyboard;
- focus;
- labels;
- contrast;
- forms;
- modals;
- responsive behavior.

---

# 87. IN SCOPE — SEO

В Scope входят:

- public title;
- description;
- canonical;
- Open Graph;
- localized metadata;
- indexing control.

---

# 88. IN SCOPE — Audit

В Scope входит Audit Trail критических административных действий.

---

# 89. IN SCOPE — Optimistic Locking

В Scope входит защита критичных редактируемых сущностей от silent overwrite.

---

# 90. IN SCOPE — Archive and Soft Delete

Для исторически значимых данных система должна поддерживать Archive/Soft Delete вместо безусловного физического удаления.

---

# 91. IN SCOPE — Health / Service State

В Scope входит административная видимость состояния критичных зависимых сервисов:

- storage;
- PDF;
- AI;
- notifications.

---

# 92. IN SCOPE — Migrations

В Scope входят миграции:

- Emotional Portfolio;
- Training;
- existing questionnaire Portfolio photos;
- Questionnaire V2, если требуется;
- связей и visibility.

---

# 93. IN SCOPE — Documentation-First

В Scope проекта входит обязательная подготовка документации до GitHub decomposition.

---

# 94. IN SCOPE — Testing

Обязательны:

- Unit;
- Integration;
- E2E;
- Customer Journey;
- Security;
- Accessibility;
- Migration;
- Regression;
- Acceptance Tests.

---

# 95. IN SCOPE — Traceability

Критические требования должны иметь трассируемость:

```text
Business Rule
→ Functional Requirement
→ Architecture / Module
→ Issue
→ Test
```

---

# 96. OUT OF SCOPE — Casting Marketplace

Продукт не является публичной площадкой, объединяющей:

- множество актёров;
- кастинг-директоров;
- агентства;
- объявления.

---

# 97. OUT OF SCOPE — Multi-Actor Agency CRM

Без отдельного изменения Scope система не превращается в CRM агентства с десятками актёров.

Техническая Profile Isolation может сохраняться для архитектурной корректности, но продуктовая функция multi-actor agency не входит в текущую цель.

---

# 98. OUT OF SCOPE — Public Casting Database

Не реализуется публичный каталог кастингов.

Castings относятся к private administrative workflow.

---

# 99. OUT OF SCOPE — Automatic Casting Applications

Система не должна автоматически отправлять заявки:

- на casting platforms;
- агентствам;
- работодателям.

---

# 100. OUT OF SCOPE — Autonomous Professional Communication

AI не должен самостоятельно:

- отправлять письма;
- отвечать Casting Director;
- договариваться;
- подтверждать участие;
- вести переговоры.

---

# 101. OUT OF SCOPE — Automatic Acceptance

Система не принимает решение:

- участвовать ли;
- соглашаться ли на роль;
- подписывать ли условия.

---

# 102. OUT OF SCOPE — Automatic Profile Fact Changes

AI/Virtual Operator не должны самостоятельно менять:

- Skills;
- Languages;
- Experience;
- Projects;
- Roles;
- physical attributes;
- achievements.

---

# 103. OUT OF SCOPE — Appearance Modification

Запрещена продуктовая функция генеративного изменения внешности актрисы для профессионального представления.

В частности:

- face replacement;
- age change;
- body change;
- hair replacement;
- clothing replacement;
- synthetic actor portrait.

---

# 104. OUT OF SCOPE — Synthetic Portfolio Photos

Система не должна создавать несуществующие профессиональные фото актрисы для Portfolio.

---

# 105. OUT OF SCOPE — Autonomous Emotional Grid Generation

Virtual Operator может предложить Grid, но не публикует его без human review.

---

# 106. OUT OF SCOPE — Full CRM Correspondence

На текущем этапе не требуется превращать Feedback в полноценную email/WhatsApp переписку внутри продукта.

Хранение status, notes и follow-up входит в Scope.

Полноценный omnichannel inbox — Future Scope.

---

# 107. OUT OF SCOPE — Mass Marketing

Не входит:

- массовая email-рассылка;
- WhatsApp marketing;
- bulk outreach;
- lead marketing automation.

---

# 108. OUT OF SCOPE — Public User Accounts

Обычный Casting Specialist не должен создавать обязательный аккаунт.

Полноценная Public User Account System не входит в текущий Scope.

---

# 109. OUT OF SCOPE — Social Network Features

Не входят:

- followers;
- likes;
- comments;
- user profiles;
- public activity feed.

---

# 110. OUT OF SCOPE — Public Rating System

Не реализуется рейтинг актрисы, проектов или отзывов посетителей.

---

# 111. OUT OF SCOPE — Payment / Commerce

Не входят:

- ecommerce;
- paid subscriptions;
- online payments;
- booking payment.

---

# 112. OUT OF SCOPE — Contract Management

Система не является юридической системой управления:

- договорами;
- электронными подписями;
- invoice;
- payment settlement.

Casting Asset может содержать документ как private attachment, но полноценный Contract Lifecycle Management не входит.

---

# 113. OUT OF SCOPE — Production Management

Продукт не управляет:

- съёмочной группой;
- call sheets;
- production budget;
- shooting schedule всего проекта.

---

# 114. OUT OF SCOPE — Automated Role Suitability Decision

AI не выдаёт конечное решение:

```text
Подходит / Не подходит
```

как авторитетный профессиональный вердикт.

Допускается только explainable comparison.

---

# 115. OUT OF SCOPE — Autonomous SEO Content Generation and Publication

AI может готовить drafts, но не публиковать автоматически SEO content без human approval.

---

# 116. OUT OF SCOPE — Autonomous Theme Publication

Theme Proposal не становится Public автоматически.

---

# 117. OUT OF SCOPE — External Advertiser Data Sharing

Данные Feedback, Contacts, Castings и Public Builder selections не предназначены для передачи рекламным системам как персональные профили.

---

# 118. OUT OF SCOPE — Permanent Tracking of Individual Casting Specialists

Не требуется создавать скрытые постоянные профили внешних пользователей.

Analytics должна строиться преимущественно на минимизированных событиях.

---

# 119. OUT OF SCOPE — Facial Ranking

Face detection может использоваться для crop assistance.

Не входит:

- оценка привлекательности;
- рейтинг внешности;
- сравнение внешности с другими актрисами.

---

# 120. OUT OF SCOPE — Automatic Content Deletion

Virtual Operator не должен самостоятельно удалять профессиональные материалы на основании возраста или analytics.

---

# 121. FUTURE SCOPE — Private Casting Links

В будущем MAY быть реализованы:

- expiring private links;
- tokenized casting-specific presentations;
- additional protected materials.

Они должны быть отдельны от Public Builder.

---

# 122. FUTURE SCOPE — Advanced CRM Correspondence

В будущем возможно:

- полноценная история переписки;
- email sync;
- conversation threads;
- communication templates.

Не включать в текущую реализацию без изменения Scope.

---

# 123. FUTURE SCOPE — Multi-Admin Advanced Roles

В дальнейшем MAY появиться более детальная permission model:

- Owner;
- Admin;
- Editor;
- Content Manager;
- Casting Manager;
- Viewer.

Базовая архитектура должна этому не препятствовать.

---

# 124. FUTURE SCOPE — MFA

Дополнительная multi-factor authentication может быть реализована в дальнейшем.

Security architecture должна позволять её добавить.

---

# 125. FUTURE SCOPE — Scheduled Social Publishing

Может быть реализована отдельная очередь планируемых публикаций в social media.

---

# 126. FUTURE SCOPE — Advanced Recommendation Engine

В дальнейшем Virtual Operator может анализировать исторические business outcomes и формировать более сложные content recommendations.

Они всё равно не должны применяться без контроля человека.

---

# 127. FUTURE SCOPE — Private Material Requests

Возможно создание workflow:

```text
Casting Specialist
→ Request Private Material
→ Admin Approval
→ Expiring Access
```

Не является обязательным текущим capability.

---

# 128. FUTURE SCOPE — Advanced Casting Import

Возможно автоматизированное получение Casting Source из внешних сервисов при наличии разрешённых API.

Не входит в текущую реализацию.

---

# 129. FUTURE SCOPE — Calendar Integration

Может быть реализована интеграция сроков Casting/Callback/Audition с календарём.

Не является текущим обязательным requirement.

---

# 130. FUTURE SCOPE — Advanced Notification Channels

Архитектура MAY позже поддержать:

- email;
- Telegram;
- push;
- other business messaging.

Текущий Scope — Internal + WhatsApp where configured.

---

# 131. FUTURE SCOPE — Advanced Search Filters

В будущем Public Search может расшириться:

- semantic search;
- advanced filters;
- context-based suggestions.

Базовый structured search входит в текущий Scope.

---

# 132. FUTURE SCOPE — Multiple Professional Profiles

Архитектура может предусматривать несколько profile contexts, но пользовательский продукт текущего этапа ориентирован на один основной актёрский профиль.

---

# 133. FUTURE SCOPE — Rich Casting Statistics

В дальнейшем MAY анализироваться:

- conversion by role type;
- conversion by source;
- material-to-callback correlation;
- seasonality.

---

# 134. FUTURE SCOPE — Professional Availability Calendar

Может быть создан профессиональный availability calendar.

Не является частью текущего обязательного цикла.

---

# 135. FUTURE SCOPE — External Casting Submission Package

Система MAY в будущем формировать export package для ручной загрузки на сторонний casting portal.

Автоматическая отправка всё равно требует отдельного Scope change.

---

# 136. EXPLICIT NON-GOAL — Максимизация количества функций

Продукт не должен оцениваться по количеству разделов.

Приоритет:

```text
Professional Outcome
>
Feature Count
```

---

# 137. EXPLICIT NON-GOAL — Максимизация времени на сайте

Цель — быстрое профессиональное решение, а не искусственное удержание пользователя.

---

# 138. EXPLICIT NON-GOAL — Полная автономность ИИ

ИИ не является управляющим лицом продукта.

---

# 139. EXPLICIT NON-GOAL — Замена администратора

Virtual Operator должен уменьшать рутину, а не исключать контроль администратора.

---

# 140. EXPLICIT NON-GOAL — Одна универсальная анкета

Продукт сознательно поддерживает несколько Prepared Questionnaires и Public Builder.

---

# 141. EXPLICIT NON-GOAL — Дублирование профессиональных фактов

Нельзя создавать независимые копии данных только потому, что конкретному экрану так проще.

---

# 142. EXPLICIT NON-GOAL — Разработка Public Site после Admin как отдельного этапа

Admin и Public representations должны проектироваться совместно для профессиональных данных.

---

# 143. EXPLICIT NON-GOAL — Скрытие private data только CSS

Privacy должна обеспечиваться Projection/Data Layer.

---

# 144. EXPLICIT NON-GOAL — Бесконтрольное использование внешних API

Подключение внешнего сервиса должно иметь:

- понятную бизнес-задачу;
- Security review;
- Privacy review;
- failure model;
- fallback.

---

# 145. Existing Functionality Preservation

При развитии продукта необходимо сохранить работоспособность существующих capabilities, если они не заменяются явно новой моделью.

К ним относятся, в частности:

- Public Profile;
- Projects;
- Roles;
- role photos;
- performance dates;
- achievements;
- multilingual content;
- Blog;
- Media Library;
- Skills;
- Languages;
- Help Center;
- social publishing;
- authentication;
- PostgreSQL;
- local storage infrastructure.

---

# 146. Legacy Functionality Replacement

Некоторые существующие модели должны не сохраняться как есть, а мигрировать:

### Emotional Project

```text
Legacy Project
→ Emotional Portfolio
```

### Educational Project / Other

```text
Legacy Project Other
→ Training
```

### Direct questionnaire photos

```text
Media Asset
→ Portfolio Item
→ Questionnaire
```

---

# 147. Feature Removal Policy

Существующая функция MAY быть удалена только если:

1. функционально заменена;
2. migration определена;
3. пользовательский Journey не ухудшается;
4. data loss отсутствует;
5. regression testing выполнен.

---

# 148. Integration Scope

Внешние интеграции являются частью продукта только в пределах конкретного documented capability.

Например:

### Social OAuth

Только professional publishing.

### WhatsApp

Notification channel.

### AI Provider

Analysis / Draft / Theme Proposal.

Внешняя система не должна становиться источником истины для Actor Profile без отдельной модели.

---

# 149. Failure Scope

Отказ optional integration не должен останавливать core product.

Например:

```text
AI unavailable
→ Profile works

WhatsApp unavailable
→ Feedback works

Analytics unavailable
→ Public Site works
```

---

# 150. Core Product Boundary

Core Product MUST оставаться функциональным при отсутствии:

- AI;
- WhatsApp;
- analytics;
- Virtual Operator.

Core включает:

- Profile;
- Portfolio;
- Emotional Portfolio;
- Projects;
- Training;
- Contacts;
- Prepared Questionnaires;
- Public Builder;
- Public Site.

---

# 151. Scope Priority Levels

Для дальнейшего планирования capability разделяются.

## P0 — Core Professional Identity

- Profile;
- Portfolio;
- Media;
- Skills;
- Languages;
- Contacts.

## P1 — Professional Presentation

- Emotional Portfolio;
- Emotional Grid;
- Projects;
- Training;
- Professional Media;
- Prepared Questionnaire.

## P2 — Casting Conversion

- Public Builder;
- Quick View;
- Search;
- Feedback;
- Casting CTA.

## P3 — Casting Operations

- Casting Workspace;
- AI Analysis;
- Opportunity Pipeline;
- Notifications.

## P4 — Optimization & Automation

- Virtual Operator;
- Theme AI;
- advanced analytics;
- BB Assistant enhancements.

Priority does not mean P4 is outside target Scope.

Все уровни входят в TO-BE, но могут реализовываться поэтапно.

---

# 152. Scope Change Trigger

Отдельное изменение Product Scope требуется, если предлагается:

- новая пользовательская роль;
- новый внешний рынок данных;
- новый публичный тип сущности;
- финансовая операция;
- автоматическая внешняя отправка;
- автономное решение ИИ;
- multi-actor business model;
- public casting marketplace;
- новый класс персональных данных.

---

# 153. Scope Change Process

Новая идея проходит:

```text
Proposal
 ↓
Scope Impact
 ↓
Business Rules Impact
 ↓
Information Architecture Impact
 ↓
Customer Journey Impact
 ↓
Security / Privacy Impact
 ↓
Documentation Update
 ↓
Approval
 ↓
Implementation Planning
```

До прохождения процесса она не считается утверждённой функциональностью.

---

# 154. Scope Creep Protection

GitHub Issue MUST NOT самостоятельно расширять Product Scope.

Если во время реализации разработчик обнаруживает полезную дополнительную функцию:

```text
Issue implementation
→ Scope Extension Proposal
```

а не:

```text
Issue implementation
→ silently add new product behaviour
```

---

# 155. MVP vs Target Product

Необходимо различать:

### Target Product

Полный Scope настоящего документа.

### Implementation Milestone / MVP

Подмножество Target Product, реализуемое на конкретной стадии.

MVP MUST NOT переписывать продуктовую архитектуру так, чтобы последующий Target Product стал невозможен.

---

# 156. Scope и Documentation

Все последующие документы должны находиться внутри настоящих границ.

Если Architecture или Module Specification вводит функцию, не указанную:

- в Product Overview;
- Business Rules;
- настоящем Scope;

необходимо проверить, является ли она:

- технической реализацией существующего requirement;
- либо новым Product Scope.

---

# 157. Scope и Customer Journey

Customer Journey может уточнять:

- порядок;
- интерфейс;
- точки взаимодействия;

но не должен самостоятельно создавать новый бизнес-capability за пределами Scope.

---

# 158. Scope и Domain Model

Domain Model должен формализовать только:

- утверждённые domain concepts;
- необходимые технические supporting concepts.

Новая business entity требует проверки Scope.

---

# 159. Scope и Public Builder

Public Questionnaire Builder является частью Core Target Product и не должен быть отложен как «дополнительный необязательный инструмент».

Он является одной из ключевых отличительных функций профессионального сайта.

---

# 160. Scope и Virtual Operator

Virtual Operator входит в Target Product, но:

- не является обязательной зависимостью Core Public Site;
- его отсутствие не должно делать невозможным ручное администрирование.

---

# 161. Scope и AI

Любая функция с AI MUST иметь manual fallback, если без AI бизнес-процесс в принципе может быть выполнен человеком.

---

# 162. Scope и Marketing

Marketing в рамках продукта означает:

- discoverability;
- SEO;
- conversion UX;
- attribution;
- analytics;
- content effectiveness.

Marketing не означает:

- массовую рекламу;
- buying media;
- рекламные кабинеты;
- автоматическую закупку трафика.

---

# 163. Scope и Professional Outcome

Система сопровождает opportunity до результата, но не гарантирует:

- приглашение;
- callback;
- offer;
- role.

Продукт оптимизирует процесс и качество представления, но не является системой прогнозирования гарантированного кастингового успеха.

---

# 164. Privacy Boundary

Professional promotion не означает полную публичность всех сведений.

Каждый sensitive Contact и private professional material остаётся под отдельным visibility control.

---

# 165. Product Boundary Diagram

```text
┌──────────────────────────────────────────────────┐
│                 TARGET PRODUCT                   │
│                                                  │
│  Actor Profile                                   │
│  Portfolio / Emotional / Projects / Training     │
│  Media / Contacts                                │
│  Questionnaires                                  │
│  Public Builder                                  │
│  Public Site                                     │
│  Feedback                                        │
│  Castings                                        │
│  Opportunity Pipeline                            │
│  AI Assistance                                   │
│  Virtual Operator                                │
│  Theme System                                    │
│  Analytics                                       │
│                                                  │
└──────────────────────────────────────────────────┘

             ↓ integrates with ↓

┌──────────────────────────────────────────────────┐
│             EXTERNAL SERVICES                    │
│                                                  │
│  Social platforms                                │
│  WhatsApp provider                               │
│  AI provider                                     │
│  Analytics provider                              │
│                                                  │
└──────────────────────────────────────────────────┘

             ≠ product does not become

┌──────────────────────────────────────────────────┐
│         EXCLUDED PRODUCT CATEGORIES              │
│                                                  │
│  Casting Marketplace                             │
│  Talent Agency CRM                               │
│  Social Network                                  │
│  Autonomous Casting Bot                          │
│  Mass Marketing System                           │
│  Contract / Payment Platform                     │
│                                                  │
└──────────────────────────────────────────────────┘
```

---

# 166. Definition of Scope Compliance

Функция соответствует Product Scope, если:

1. поддерживает утверждённый capability;
2. не создаёт новый бизнес-домен без документации;
3. не нарушает Business Rules;
4. не превращает продукт в исключённую категорию;
5. не увеличивает публичность private data;
6. не даёт AI неутверждённую автономность;
7. имеет понятный Customer Journey;
8. вписывается в Information Architecture.

---

# 167. Definition of Scope Violation

Scope считается нарушенным, если реализация:

- вводит новый независимый business workflow без утверждения;
- автоматически отправляет внешние профессиональные заявки;
- превращает Castings в public marketplace;
- создаёт обязательные public accounts;
- автоматически публикует AI content;
- использует private data в marketing;
- меняет внешность актрисы;
- обходит visibility policies;
- создаёт дублирующие источники профессиональных фактов.

---

# 168. Итоговое положение

Целевой продукт представляет собой:

> **персональную профессиональную платформу актрисы, предназначенную для управления достоверным профессиональным профилем, его качественного публичного представления, самостоятельного получения кастинг-специалистом необходимых материалов, обработки профессиональных обращений и сопровождения кастинговой возможности до результата.**

Продукт сознательно не расширяется до marketplace, агентской CRM, социальной сети, автономного кастингового агента или системы массового маркетинга без отдельного изменения Product Scope.