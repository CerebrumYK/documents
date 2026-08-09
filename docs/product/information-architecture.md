# INFORMATION ARCHITECTURE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.


## Информационная архитектура профессионального сайта актрисы

**Целевой файл:** `docs/product/information-architecture.md`
**Статус:** нормативный документ
**Назначение:** определить, какие информационные сущности существуют в продукте, как они связаны, где являются источником истины, в каких интерфейсах используются и какие уровни доступа применяются.

---

# 1. Цель документа

Информационная архитектура должна обеспечить:

1. единый источник профессиональных данных;
2. отсутствие дублирования фактов;
3. чёткое разделение публичных и внутренних сущностей;
4. предсказуемое использование данных в анкетах;
5. возможность повторного использования медиа;
6. независимость проектов, обучения и эмоционального портфолио;
7. единый путь от исходных данных к публичным представлениям;
8. поддержку Public Questionnaire Builder;
9. поддержку кастингового workflow;
10. поддержку автоматизации Virtual Operator;
11. возможность дальнейшего расширения без разрушения структуры.

---

# 2. Архитектурный принцип

Центральной сущностью всей системы является:

```text
ACTOR PROFILE
```

Все профессиональные данные должны быть либо:

* непосредственной частью профиля;
* дочерней бизнес-сущностью профиля;
* производным представлением данных профиля;
* входящим профессиональным процессом, связанным с профилем.

---

# 3. Верхнеуровневая модель

```text
ACTOR PROFILE
│
├── IDENTITY
│   ├── Basic Information
│   ├── Appearance
│   ├── Skills
│   └── Languages
│
├── VISUAL PRESENTATION
│   ├── Portfolio
│   ├── Emotional Portfolio
│   │   └── Emotional Grid
│   └── Media Library
│
├── PROFESSIONAL EXPERIENCE
│   ├── Projects
│   ├── Roles
│   ├── Performances
│   └── Achievements
│
├── EDUCATION
│   └── Training
│
├── PROFESSIONAL MEDIA
│   ├── Video
│   ├── Audio
│   └── External Links
│
├── CONTACTS
│
├── QUESTIONNAIRES
│   ├── Prepared Questionnaires
│   │   └── Revisions
│   ├── Public Templates
│   └── Public Questionnaire Sessions
│
├── CASTING WORKFLOW
│   ├── Castings
│   ├── Casting Assets
│   ├── AI Analysis Revisions
│   └── Opportunity Pipeline
│
├── COMMUNICATION
│   ├── Feedback
│   ├── Attachments
│   ├── Internal Notes
│   └── Notifications
│
├── PRESENTATION
│   ├── Site Themes
│   └── Theme Revisions
│
└── OPERATIONS
    ├── Virtual Operator
    ├── Audit
    ├── Health
    └── Migrations
```

---

# 4. Классы информации

Все данные делятся на шесть типов.

## IA-CLASS-01 — Master Data

Основные достоверные данные.

Примеры:

* имя;
* навыки;
* языки;
* контакты;
* проекты;
* Training.

Master Data используется как источник для других представлений.

---

## IA-CLASS-02 — Media Assets

Физические или логические медиаобъекты:

* image;
* video;
* audio;
* document.

Media Asset не определяет бизнес-контекст сам по себе.

Контекст создаётся relation.

---

## IA-CLASS-03 — Business Content

Профессиональные сущности:

* Portfolio Item;
* Emotional Portfolio;
* Project;
* Training;
* Achievement.

---

## IA-CLASS-04 — Configuration

Настройки представления:

* crop;
* block selection;
* visibility;
* sort order;
* PDF settings;
* theme configuration.

---

## IA-CLASS-05 — Derived Content

Автоматически созданные результаты:

* thumbnail;
* Emotional Grid image;
* PDF;
* preview;
* AI recommendation.

---

## IA-CLASS-06 — Process Data

Данные рабочих процессов:

* Casting;
* Feedback;
* Notification;
* Opportunity Stage;
* Audit Event.

---

# 5. Actor Profile

Actor Profile является корневым доменным объектом.

Он не должен содержать все данные непосредственно в одной записи.

Профиль объединяет связанные сущности.

---

# 6. Profile — Basic Information

Содержит базовые профессиональные сведения:

* имя;
* фамилия;
* сценическое имя при наличии;
* профессиональное позиционирование;
* краткое описание;
* biography;
* location/base;
* допустимые публичные профессиональные параметры.

---

# 7. Appearance

Appearance должен быть структурированным блоком.

Необходимо отделять:

* структурированные значения;
* свободное описание.

Структурированные данные могут использоваться:

* Public Quick View;
* Questionnaire;
* Casting matching;
* Public Builder;
* Search.

---

# 8. Skills

Skills — самостоятельный справочник профессиональных навыков.

Каждый Skill относится к Profile.

Skill может иметь:

* category;
* title;
* level;
* description;
* sort order;
* visibility.

Skill не должен храниться только внутри biography.

---

# 9. Languages

Language является структурированной сущностью.

Минимально:

* language;
* level;
* notes;
* sort order;
* visibility.

Уровень может использовать CEFR или существующую принятую модель продукта.

---

# 10. Media Library

Media Library является единым техническим каталогом файлов.

Она не является самостоятельным профессиональным разделом публичного сайта.

Media Library служит источником для:

* Portfolio;
* Emotional Portfolio;
* Projects;
* Training;
* Video;
* Audio;
* Castings;
* Feedback Attachments.

---

# 11. Media Asset

Media Asset должен быть отделён от бизнес-сущности.

Пример:

```text
MEDIA ASSET
    │
    ├── used by Portfolio Item
    ├── used by Project
    ├── used by Emotional Portfolio
    └── used by Questionnaire
```

Один файл не должен копироваться для каждого использования.

---

# 12. Media Asset Types

Минимально:

* image;
* video;
* audio;
* document.

Дополнительные типы могут быть введены позже без изменения базовой модели.

---

# 13. Media Derivatives

Derived media должны быть связаны с Original Asset.

Примеры:

```text
Original
├── Thumbnail
├── Web
├── PDF
└── Preview
```

Derived file не является новым независимым профессиональным материалом.

---

# 14. Portfolio

Portfolio — основная профессиональная фотографическая витрина.

Portfolio является дочерней сущностью Actor Profile.

```text
PROFILE
  └── PORTFOLIO ITEMS
          └── MEDIA ASSET
```

---

# 15. Portfolio Categories

Минимально:

* Full Body;
* Close-Up;
* Waist;
* Profile;
* Three Quarter;
* Staged;
* Appearance;
* Additional;
* Other.

---

# 16. Portfolio Item

Portfolio Item содержит бизнес-контекст media asset:

* type;
* title;
* caption;
* shooting date;
* photographer;
* order;
* visibility;
* questionnaire permissions.

---

# 17. Primary Portfolio Items

Profile может ссылаться на:

```text
primary_full_body_portfolio_item
primary_close_up_portfolio_item
```

Это ссылки на Portfolio Items, а не непосредственно на Media Assets.

---

# 18. Portfolio Public Representation

Portfolio Items используются в:

* Public Portfolio Page;
* Casting Quick View;
* Prepared Questionnaire;
* Public Questionnaire Builder;
* Project-independent professional gallery.

---

# 19. Emotional Portfolio

Emotional Portfolio — самостоятельная фотосессия.

Структура:

```text
PROFILE
  └── EMOTIONAL PORTFOLIO
         ├── METADATA
         ├── PHOTOS
         └── EMOTIONAL GRIDS
```

---

# 20. Emotional Portfolio Metadata

Обязательный источник:

* title;
* shooting date.

Дополнительно:

* location;
* photographer;
* studio;
* description.

---

# 21. Emotional Portfolio Photo

Связывает:

```text
Emotional Portfolio
        ↓
Media Asset
```

Дополняется:

* emotion label;
* caption;
* order;
* visibility.

---

# 22. Emotional Grid

Emotional Grid — производное профессиональное представление одной Emotional Portfolio.

```text
EMOTIONAL PORTFOLIO
       ↓
EMOTIONAL GRID
       ↓
GRID CELLS
       ↓
SOURCE PHOTOS
```

---

# 23. Emotional Grid Cell

Grid Cell хранит не новый файл, а:

* reference source photo;
* position;
* crop configuration;
* confirmation state.

---

# 24. Emotional Grid Derived Assets

После finalization создаются:

* Master Grid;
* Web Grid;
* PDF Grid;
* Thumbnail.

Они считаются Derived Content.

---

# 25. Projects

Project — профессиональная работа актрисы.

Project не должен использоваться для:

* Training;
* Emotional Portfolio;
* обычных портфолио-съёмок.

---

# 26. Project Structure

```text
PROFILE
 └── PROJECT
       ├── ROLES
       ├── PERFORMANCES
       ├── PROJECT MEDIA
       ├── LINKS
       └── ACHIEVEMENTS
```

---

# 27. Role

Role является дочерней сущностью Project.

Она может содержать:

* character name;
* role type;
* description;
* media relations;
* sort order;
* public visibility.

---

# 28. Performances

Performance Date используется только для проектов, где событие имеет конкретную дату:

* theatre;
* performance;
* show;
* event.

Не должна использоваться Training.

---

# 29. Achievements

Achievement может быть:

* связан с Project;
* либо относиться к Profile в целом.

Архитектура должна позволять оба варианта.

---

# 30. Training

Training является отдельным доменом.

```text
PROFILE
  └── TRAINING ITEM
       ├── CERTIFICATES
       ├── MEDIA
       └── LINKS
```

---

# 31. Training не зависит от Projects

Не допускается целевая структура:

```text
Project
  type=Other
  subtype=Course
```

Целевая модель:

```text
Training
```

---

# 32. Professional Media

Видео и аудио должны быть доступны независимо от проектов.

Например:

```text
PROFILE
 ├── VIDEO
 └── AUDIO
```

При этом конкретный видеофайл может также быть связан с Project.

---

# 33. Video

Video может иметь semantic type:

* Video Intro;
* Showreel;
* Scene;
* Project Video;
* Other.

---

# 34. Audio

Audio может иметь:

* Voice Sample;
* Audio Intro;
* Dubbing;
* Singing;
* Other.

---

# 35. External Links

External Link является самостоятельным структурированным объектом.

```text
PROFILE
 └── EXTERNAL LINKS
```

Содержит:

* description;
* URL;
* order;
* visibility.

---

# 36. Contacts

Contacts находятся в едином Profile Contact Directory.

```text
PROFILE
 └── CONTACT DIRECTORY
```

Нельзя создавать независимые копии одного телефона в разных анкетах.

---

# 37. Contact Types

Минимально:

* Phone;
* Mother Phone;
* Father Phone;
* WhatsApp;
* Telegram;
* Instagram;
* TikTok;
* YouTube;
* Other.

---

# 38. Contact Visibility

Для каждого Contact отдельно определяется:

```text
Admin Only
Public Website
Admin Questionnaires
Public Questionnaire Builder
```

Один Contact может быть разрешён в нескольких контекстах.

---

# 39. Prepared Questionnaires

Prepared Questionnaire — курируемый администратором профессиональный документ.

Структура:

```text
QUESTIONNAIRE
   └── REVISION
         ├── BLOCK CONFIG
         ├── ITEM SELECTIONS
         ├── PDF CONFIG
         └── HTML CONFIG
```

---

# 40. Questionnaire Identity

Questionnaire содержит долгоживущую идентичность:

* title;
* type;
* purpose;
* casting relation;
* publication settings.

Контент хранится в Revision.

---

# 41. Questionnaire Revision

Revision является снимком конфигурации анкеты.

После публикации Revision становится immutable.

---

# 42. Questionnaire Types

Минимально:

* Compact;
* Extended;
* Casting;
* Custom.

---

# 43. Questionnaire Blocks

Логическая структура:

```text
Questionnaire Revision
├── Basic Info
├── Full Body
├── Close-Up
├── Appearance
├── Skills
├── Languages
├── Projects
├── Roles
├── Emotional Grid
├── Training
├── Achievements
├── Video
├── Audio
├── External Links
├── Extra Photos
└── Contacts
```

---

# 44. Questionnaire Relations

Выбор конкретных элементов должен храниться отдельно от исходной сущности.

Например:

```text
Questionnaire Revision
        ↓
Questionnaire Project Selection
        ↓
Project
```

То же:

* Skills;
* Languages;
* Training;
* Contacts;
* Media.

---

# 45. Primary Questionnaire

Profile должен иметь логическую связь с одной опубликованной Primary Questionnaire.

Публичная страница `/questionnaire` по умолчанию использует эту связь.

---

# 46. Download Picker

Download Picker является Public View над разрешёнными Prepared Questionnaires.

Он не является отдельным источником данных.

---

# 47. Public Questionnaire Templates

Public Template — конфигурационный объект.

Он определяет стартовую структуру внешнего конструктора.

```text
PUBLIC TEMPLATE
├── DEFAULT BLOCKS
├── REQUIRED BLOCKS
├── LIMITS
└── DISPLAY SETTINGS
```

---

# 48. Public Template Types

Минимально:

* Quick;
* Standard;
* Extended;
* Casting.

---

# 49. Public Template не содержит копии Profile Data

Template хранит:

* structure;
* rules;
* limits;

но не должен сохранять копии:

* Projects;
* Contacts;
* Skills.

Эти данные подгружаются из текущего разрешённого Profile.

---

# 50. Public Questionnaire Builder

Builder является временным presentation layer.

```text
PROFILE PUBLIC DATA
        ↓
PUBLIC TEMPLATE
        ↓
PUBLIC QUESTIONNAIRE SESSION
        ↓
PREVIEW
        ↓
PDF SNAPSHOT
```

---

# 51. Public Questionnaire Session

Session хранит:

* selected template;
* selected blocks;
* selected item IDs;
* language;
* optional casting name;
* optional role;
* expiration.

Не должна становиться постоянной Prepared Questionnaire.

---

# 52. Public Builder Available Data

Должен существовать логический агрегатор разрешённых данных:

```text
Public Builder Data
├── Profile
├── Portfolio
├── Emotional Grid
├── Projects
├── Skills
├── Languages
├── Training
├── Achievements
├── Video
├── Audio
├── Links
└── Contacts
```

Каждый список уже отфильтрован сервером.

---

# 53. Public Builder Snapshot

При генерации PDF создаётся фиксированный generation snapshot.

Он нужен для целостности конкретного PDF.

Snapshot не становится новым источником истины.

---

# 54. Castings

Casting — входящая профессиональная возможность.

```text
PROFILE
  └── CASTING
       ├── SOURCE MATERIALS
       ├── ANALYSIS REVISIONS
       ├── QUESTIONNAIRE
       └── OPPORTUNITY STATE
```

---

# 55. Casting Source

Source может быть:

* manual entry;
* Feedback;
* external brief;
* screenshot;
* document.

---

# 56. Casting Assets

Casting Assets приватны.

Они не должны участвовать в публичной Media Library.

---

# 57. Casting Analysis

Разделить:

```text
SOURCE
 ↓
EXTRACTED REQUIREMENTS
 ↓
PROFILE MATCH
 ↓
RECOMMENDATIONS
 ↓
HUMAN DECISIONS
```

Это разные информационные уровни.

---

# 58. Extracted Requirement

Отдельная логическая запись должна содержать:

* category;
* value;
* source reference;
* confidence;
* manual review flag.

---

# 59. Profile Match

Сопоставляет Requirement с конкретным Master Data Profile.

Не изменяет Profile.

---

# 60. Recommendation

Recommendation является Derived AI Content.

Она не должна считаться фактом.

---

# 61. Human Decision

Подтверждение или отклонение рекомендации хранится независимо.

Это позволяет отличать:

```text
AI suggested
```

от:

```text
Admin approved
```

---

# 62. Opportunity Pipeline

Casting имеет business outcome state.

Это отдельное измерение от technical status.

Пример:

```text
Technical:
Analyzed

Business:
Audition
```

---

# 63. Feedback

Feedback — входящий коммуникационный объект.

```text
PROFILE
  └── FEEDBACK MESSAGE
       ├── SENDER CONTACTS
       ├── ATTACHMENTS
       ├── INTERNAL NOTES
       ├── HISTORY
       └── NOTIFICATIONS
```

---

# 64. Feedback Sender

Sender Data не должен автоматически становиться Contact Directory актрисы.

Это отдельные входящие контактные данные.

---

# 65. Feedback Attachment

Feedback Attachment относится к Feedback, а не к основной публичной Media Library.

Может использоваться при создании Casting после разрешённого действия.

---

# 66. Internal Notes

Internal Notes не должны использоваться публичными представлениями.

---

# 67. Feedback History

History фиксирует workflow.

Не является обычным текстовым полем Feedback.

---

# 68. Feedback → Casting Relation

После создания Casting:

```text
Feedback
   ↔
Casting
```

Связь двусторонняя.

---

# 69. Notifications

Notification является отдельной процессной сущностью.

```text
ENTITY EVENT
   ↓
NOTIFICATION
   ↓
CHANNEL DELIVERY
```

---

# 70. Notification Channels

Минимально архитектура должна поддерживать:

* Internal;
* WhatsApp.

Позже может расширяться другими каналами.

---

# 71. Notification не является источником Feedback

Удаление notification не должно удалять исходный Feedback.

---

# 72. Site Themes

Theme — набор presentation rules.

```text
SITE THEME
   └── THEME REVISION
```

---

# 73. Theme Revision

Содержит:

* design tokens;
* hero config;
* component config;
* motion config;
* validation.

Не содержит копии Profile Content.

---

# 74. Published Theme

Публичный сайт получает ссылку на активную Published Theme Revision.

---

# 75. Theme не управляет контентом

Theme Architecture не должна иметь ownership над:

* Biography;
* Projects;
* Contacts;
* Questionnaires.

---

# 76. Virtual Operator

Virtual Operator не должен иметь отдельный дублирующий store профессиональных данных.

Он читает существующие domain objects и формирует:

* observations;
* suggestions;
* draft operations;
* alerts.

---

# 77. Virtual Operator Output

Логически разделить:

```text
Observation
Suggestion
Action Proposal
Execution Result
```

---

# 78. Virtual Operator Dashboard

Dashboard агрегирует:

* new opportunities;
* incomplete records;
* stale content;
* broken links;
* pending confirmations;
* deadlines.

Dashboard является представлением, не новой master entity.

---

# 79. Audit

Audit Event является append-oriented process information.

Он связывается:

* actor/admin;
* entity;
* action;
* timestamp.

---

# 80. Search Architecture

Public Search должен индексировать только разрешённые публичные данные.

Источники:

* Profile facts;
* Skills;
* Languages;
* Projects;
* Roles;
* Training.

---

# 81. Search не является отдельной базой фактов

Search index — derived representation.

При конфликте источником истины остаётся domain entity.

---

# 82. Public Navigation Architecture

Публичная навигация должна строиться вокруг профессиональных задач, а не внутренней структуры базы.

Рекомендуемый основной путь:

```text
Home
Profile
Portfolio
Emotional Range
Projects
Questionnaire
Contacts
```

Training может отображаться как самостоятельный раздел или профессиональный подраздел Profile в зависимости от финального UX.

---

# 83. Home

Home — агрегированное представление.

Не является отдельным источником профессиональных данных.

Использует:

* Profile;
* Portfolio;
* Video;
* Emotional Grid;
* Selected Projects;
* Primary Questionnaire;
* Primary Contact.

---

# 84. Profile Page

Profile — профессиональная сводка.

Использует:

* Identity;
* Appearance;
* Skills;
* Languages;
* Primary Portfolio;
* Selected Projects;
* Training preview;
* Primary CTA.

---

# 85. Portfolio Page

Источник:

```text
Portfolio Items
```

Не Project Media.

Project photo MAY быть также зарегистрирована как Portfolio Item, если принято такое решение, но связь должна быть явной.

---

# 86. Emotional Portfolio Index

Источник:

```text
Published Emotional Portfolios
```

---

# 87. Emotional Portfolio Detail

Использует:

* Emotional Portfolio Metadata;
* Emotional Photos;
* Published Emotional Grids.

---

# 88. Projects Page

Использует:

* Projects;
* Roles;
* Project Media;
* relevant Achievements.

Training и Emotional Portfolio исключаются.

---

# 89. Training Page

Использует:

* Training Items;
* certificates;
* approved media.

---

# 90. Questionnaire Page

Является professional hub.

Использует:

```text
Primary Prepared Questionnaire
Available Prepared Questionnaires
Public Builder Templates
```

---

# 91. Contacts Page

Использует:

* public Contact Directory entries;
* Feedback Form.

---

# 92. Casting Quick View

Quick View — агрегат:

```text
Profile
Primary Close-Up
Primary Full Body
Key Skills
Languages
Video Intro
Emotional Grid
Selected Projects
Primary Questionnaire
Primary Contact
```

---

# 93. Presentation vs Domain

Важно разделять:

### Domain Entity

Например:

```text
Project
```

### Presentation

Например:

```text
Selected Projects on Home
```

Selected Projects не должны становиться независимыми копиями Projects.

---

# 94. Featured / Selected Collections

Для публичного приоритета можно использовать relation/config:

```text
featured=true
```

или отдельную curated collection.

Нельзя копировать сущность целиком.

---

# 95. Visibility Architecture

Необходимо различать минимум:

```text
Admin visibility
Public website visibility
Admin questionnaire availability
Public questionnaire builder availability
```

В дальнейшем могут добавляться:

* private share;
* casting-only access.

---

# 96. Visibility разрешение всегда проверяется сервером

Presentation layer не является источником security policy.

---

# 97. Slugs

Публичные detail entities должны иметь стабильный slug.

Например:

```text
/emotional-portfolio/{slug}
/projects/{slug}
/training/{slug}
```

Если конкретный раздел имеет публичный detail view.

---

# 98. Slug не является ID безопасности

Знание slug не должно открывать непубличный объект.

---

# 99. Архивные сущности

Archived entity сохраняется для истории, но не должна автоматически попадать:

* в public navigation;
* Public Builder;
* default selections.

---

# 100. Draft Entities

Draft доступны только разрешённому Admin UI и Preview.

Не должны присутствовать в обычном Public Data Layer.

---

# 101. Published Entities

Published означает доступность в конкретном разрешённом публичном контексте.

Однако одного `published` может быть недостаточно.

Дополнительно действуют visibility policies.

---

# 102. Prepared Questionnaire Publication

Questionnaire public availability требует одновременно:

```text
Published Revision
+
Public Download/Display Permission
+
Availability Period
```

---

# 103. Public Builder Availability

Для использования item во внешнем Builder требуется одновременно:

```text
Valid Domain Object
+
Not Archived
+
Allowed for Public Builder
+
Any additional publication policy
```

---

# 104. Public Data Aggregation Layer

Рекомендуется концептуально предусмотреть Public Profile Data Layer.

Он агрегирует:

* только разрешённые данные;
* без admin-only fields;
* без private relations.

Public UI не должен самостоятельно фильтровать полный административный domain graph.

---

# 105. Admin Data Layer

Admin получает более полный domain graph в соответствии с правами пользователя.

---

# 106. AI Data Boundaries

AI должен получать только данные, необходимые для конкретной операции.

Например Casting Analysis:

* Casting Source;
* разрешённые Profile Facts.

Theme AI не требуется доступ к:

* Feedback Contacts;
* Castings;
* private notes.

---

# 107. Public Builder Data Boundary

Builder получает только:

```text
Builder-Allowed Public Professional Data
```

Не получает:

* admin-only data;
* source casting documents;
* private contacts;
* audit;
* unpublished drafts.

---

# 108. Marketing Analytics Architecture

Analytics Events являются производными telemetry data.

Они не должны становиться источником business facts.

Например:

```text
questionnaire_downloaded
```

не означает автоматически:

```text
Casting received
```

---

# 109. Business Outcome Data

Casting outcome вводится отдельно или поступает из достоверного workflow.

Analytics не должны автоматически менять Opportunity Status.

---

# 110. Информационные потоки

## Flow IA-FLOW-001 — Добавление фотографии Portfolio

```text
Upload
 ↓
Media Asset
 ↓
Portfolio Item
 ↓
Visibility / Category
 ↓
Public Portfolio
 ↓
Questionnaire availability
```

---

# 111. Flow IA-FLOW-002 — Emotional Portfolio

```text
Media Assets
 ↓
Emotional Portfolio
 ↓
Emotional Photos
 ↓
Emotional Grid Configuration
 ↓
Rendered Grid
 ↓
Public Emotional Portfolio
 ↓
Questionnaire
```

---

# 112. Flow IA-FLOW-003 — Prepared Questionnaire

```text
Profile Data
+ Portfolio
+ Projects
+ Training
+ Media
+ Contacts
        ↓
Questionnaire Revision
        ↓
Readiness
        ↓
Preview
        ↓
Publish
        ↓
HTML / PDF
```

---

# 113. Flow IA-FLOW-004 — Public Questionnaire Builder

```text
Published / Allowed Profile Data
        +
Public Template
        ↓
Temporary Selection
        ↓
Preview
        ↓
Server Revalidation
        ↓
Generation Snapshot
        ↓
PDF
```

---

# 114. Flow IA-FLOW-005 — Feedback

```text
Public Form
 ↓
Feedback
 ↓
Internal Notification
 ↓
Optional WhatsApp
 ↓
Admin Workflow
```

---

# 115. Flow IA-FLOW-006 — Feedback to Casting

```text
Feedback
 ↓
Human Review
 ↓
Create Casting
 ↓
Casting Source Reference
 ↓
AI Analysis
 ↓
Questionnaire Draft
```

---

# 116. Flow IA-FLOW-007 — Theme

```text
Current Theme
 ↓
Prompt / Manual Change
 ↓
Draft Theme Revision
 ↓
Validation
 ↓
Preview
 ↓
Publish
 ↓
Public Presentation
```

---

# 117. Flow IA-FLOW-008 — Virtual Operator

```text
Domain Changes
 ↓
Operator Analysis
 ↓
Observation
 ↓
Suggestion
 ↓
Human Decision
 ↓
Approved Derived Action
```

---

# 118. Dependency Graph

```text
PROFILE
│
├── MEDIA
│   ├── PORTFOLIO
│   ├── EMOTIONAL PORTFOLIO
│   │      └── EMOTIONAL GRID
│   ├── PROJECTS
│   └── TRAINING
│
├── SKILLS
├── LANGUAGES
├── CONTACTS
│
├── QUESTIONNAIRES
│   ├── PORTFOLIO
│   ├── EMOTIONAL GRID
│   ├── PROJECTS
│   ├── TRAINING
│   ├── SKILLS
│   ├── LANGUAGES
│   ├── MEDIA
│   └── CONTACTS
│
├── PUBLIC BUILDER
│   └── ALLOWED PUBLIC DATA
│
├── FEEDBACK
│      └── CASTING
│             ├── ANALYSIS
│             └── QUESTIONNAIRE
│
└── THEME
       └── PUBLIC PRESENTATION
```

---

# 119. Не допускаемые архитектурные связи

Следующие модели считаются неправильными.

## IA-ANTI-001

```text
Training → Project(type=Other)
```

В целевой модели запрещено.

---

## IA-ANTI-002

```text
Emotional Portfolio → Project
```

Запрещено как основная модель.

---

## IA-ANTI-003

```text
Questionnaire stores copied profile facts permanently
```

Без revision/snapshot rationale — нежелательно.

---

## IA-ANTI-004

```text
Public Builder → All Profile Data → UI hides secret items
```

Запрещено.

---

## IA-ANTI-005

```text
Theme → modifies Profile Content
```

Запрещено.

---

## IA-ANTI-006

```text
Feedback → automatically becomes Casting
```

Запрещено.

---

## IA-ANTI-007

```text
AI Recommendation → Profile Fact
```

без human confirmation — запрещено.

---

# 120. Информационные ownership boundaries

## Profile owns

* Identity;
* Skills;
* Languages;
* Contacts.

## Portfolio owns

* professional photo classification.

## Emotional Portfolio owns

* emotional shoot metadata;
* emotional photos.

## Emotional Grid owns

* grid composition configuration.

## Project owns

* roles;
* performance context.

## Training owns

* education context.

## Questionnaire owns

* curated presentation configuration.

## Public Builder Session owns

* temporary visitor selection.

## Casting owns

* incoming casting requirements.

## Feedback owns

* original inbound communication.

## Theme owns

* presentation configuration.

---

# 121. Lifecycle ownership

Исходная сущность определяет жизненный цикл master data.

Пример:

если Project архивируется, Questionnaire Revision, уже опубликованная как immutable snapshot, не должна автоматически исчезать.

Draft Questionnaire может быть помечена как requiring review.

---

# 122. Derived Content Invalidations

Необходимо определить зависимости.

Примеры:

### Portfolio Item changed

Может инвалидировать:

* Questionnaire Draft Preview;
* Public Builder cache;
* Search Index.

### Emotional Grid changed

Инвалидирует:

* Grid derivatives;
* linked Draft Questionnaire preview.

### Contact visibility changed

Инвалидирует:

* Public Contacts cache;
* Public Builder available data.

---

# 123. Search Index Invalidation

Изменение публично индексируемой сущности должно обновлять Search Derived Representation.

---

# 124. SEO Metadata Ownership

SEO metadata может принадлежать публичной domain page или presentation config.

Не должно храниться внутри Theme.

Theme влияет на presentation, но не на смысловые metadata.

---

# 125. Homepage Ownership

Home не должна хранить копии:

* имени;
* primary photo;
* selected skills.

Она должна агрегировать их из domain data.

Разрешены presentation settings:

* какие секции показывать;
* порядок;
* featured collections.

---

# 126. Public Builder Limits Ownership

Лимиты и required blocks принадлежат Public Questionnaire Template.

Не Profile.

---

# 127. Prepared Questionnaire Limits Ownership

Параметры конкретной Prepared Questionnaire принадлежат её Revision.

---

# 128. Public Builder and Prepared Questionnaire Separation

Обязательно различать:

```text
Prepared Questionnaire
=
Admin-curated persistent document

Public Questionnaire Session
=
Visitor-curated temporary presentation
```

Они не должны использовать одну сущность как два режима без явного архитектурного разграничения.

---

# 129. Admin Preview

Admin Preview может использовать draft data.

Public Site — только опубликованные и разрешённые данные.

Это два разных data contexts.

---

# 130. Private Casting Share

Архитектура MAY в дальнейшем поддерживать ограниченные private casting links.

Они не должны смешиваться с Public Builder и обычным Public Site.

---

# 131. Public URL Architecture

Базовая рекомендуемая схема:

```text
/
/profile
/portfolio
/emotional-portfolio
/emotional-portfolio/{slug}
/projects
/projects/{slug}
/training
/questionnaire
/questionnaire/build
/contacts
```

Локализованные маршруты могут использовать текущую архитектуру приложения.

---

# 132. Admin URL Architecture

Концептуально:

```text
/admin
/admin/profile
/admin/portfolio
/admin/emotional-portfolio
/admin/emotional-grid
/admin/projects
/admin/training
/admin/questionnaires
/admin/castings
/admin/contacts
/admin/media
/admin/feedback
/admin/notifications
/admin/themes
/admin/settings
```

Точные URL могут учитывать существующую структуру проекта.

---

# 133. Information Architecture и UX

IA определяет:

* что существует;
* как связано;
* где источник истины.

UX определяет:

* что пользователь видит;
* в каком порядке;
* каким способом взаимодействует.

UX не должен создавать новую бизнес-сущность только ради отображения, если достаточно presentation layer.

---

# 134. Information Architecture и Database Schema

IA не является физической схемой БД.

Одна логическая сущность может быть реализована:

* одной таблицей;
* несколькими таблицами;
* relations;
* JSON configuration;

если сохраняются настоящие бизнес-границы.

---

# 135. Information Architecture и API

API должен отражать domain boundaries.

Не рекомендуется создавать один универсальный endpoint, который отдаёт весь профиль целиком во все интерфейсы.

Public, Admin, Builder и AI contexts должны иметь разные projections.

---

# 136. Context Projections

Необходимо концептуально различать:

```text
Admin Profile Projection
Public Profile Projection
Casting Quick View Projection
Public Builder Projection
AI Casting Projection
PDF Snapshot Projection
Search Projection
```

Все они строятся из общих Master Data, но имеют разные правила доступа и состава.

---

# 137. Admin Profile Projection

Содержит максимально доступные управляемые данные согласно роли администратора.

---

# 138. Public Profile Projection

Содержит только опубликованный публичный профессиональный контент.

---

# 139. Casting Quick View Projection

Содержит компактное curated представление ключевых профессиональных данных.

---

# 140. Public Builder Projection

Содержит только элементы, разрешённые для visitor selection.

---

# 141. AI Casting Projection

Содержит только факты, разрешённые для анализа соответствия Casting Requirements.

---

# 142. PDF Snapshot Projection

Содержит фиксированное представление выбранных данных на момент генерации.

---

# 143. Search Projection

Содержит только searchable public facts.

---

# 144. Information Freshness

Необходимо различать:

* Current Master Data;
* Published Snapshot;
* Generated PDF;
* Archived Record.

Нельзя считать их одной и той же сущностью.

---

# 145. Current vs Published

Изменение Current Draft может не влиять на Published до отдельного publish action.

Это особенно важно для:

* Questionnaires;
* Themes;
* Emotional Grid derivatives.

---

# 146. Archive vs Delete

Archive означает:

* запись исторически сохраняется;
* исключается из новых selection;
* не обязательно исчезает из immutable historical snapshots.

Delete — физическое удаление и используется ограниченно.

---

# 147. Migration Information Architecture

Legacy data должна быть преобразована в новые domain boundaries.

Основные migration paths:

```text
Emotional Project
→ Emotional Portfolio

Project(type=Other, Education)
→ Training

Existing Questionnaire Full Body
→ Portfolio Item

Existing Questionnaire Close-Up
→ Portfolio Item
```

---

# 148. Migration Provenance

После migration SHOULD сохраняться связь:

```text
Legacy Source
   ↓
Migration Mapping
   ↓
New Domain Entity
```

---

# 149. Traceability IDs

Ключевые архитектурные сущности рекомендуется обозначать постоянными IDs:

```text
IA-ENT-PRF
IA-ENT-MED
IA-ENT-POR
IA-ENT-EMO
IA-ENT-GRD
IA-ENT-PRJ
IA-ENT-TRN
IA-ENT-QST
IA-ENT-PQB
IA-ENT-CST
IA-ENT-FDB
IA-ENT-NOT
IA-ENT-THM
IA-ENT-AUD
```

---

# 150. Traceability к Business Rules

Пример:

```text
IA-ENT-PQB
supports:
BR-PQB-001
BR-PQB-004
BR-PQB-006
BR-PQB-008
```

---

# 151. Traceability к UX

Пример:

```text
IA-ENT-GRD
used by:
UF-EMO-CreateGrid
UF-QST-SelectEmotionalGrid
CJM-CastingDirector
```

---

# 152. Traceability к Implementation

Каждая implementation task должна указывать затрагиваемые domain entities.

Пример:

```text
TASK-PQB-014

Entities:
IA-ENT-PQB
IA-ENT-POR
IA-ENT-PRJ
IA-ENT-CON
```

---

# 153. Критерии корректности IA

Информационная архитектура считается соблюдённой, если:

1. Profile является корневой сущностью.
2. Media Asset отделён от бизнес-контекста.
3. Portfolio отделено от Projects.
4. Emotional Portfolio отделено от Projects.
5. Emotional Grid отделён от Emotional Portfolio photos как самостоятельное derived/config entity.
6. Training отделено от Projects.
7. Contact Directory единый.
8. Prepared Questionnaire и Revision разделены.
9. Public Builder Session отделена от Prepared Questionnaire.
10. Casting отделён от Project.
11. Feedback отделён от Casting.
12. AI Extraction отделена от Source Fact.
13. AI Recommendation отделена от Human Decision.
14. Theme отделена от Content.
15. Search является derived projection.
16. Homepage является aggregated presentation, а не master data store.
17. Public Builder получает отдельную projection.
18. Private data не попадает в Public Projection.
19. Published snapshots не смешиваются с Current Draft.
20. Legacy migrations переводят данные в правильные новые domain boundaries.

---

# 154. Итоговая архитектурная модель

```text
                         ACTOR PROFILE
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
       IDENTITY            CONTENT            CONTACTS
          │                   │                   │
     Skills/Langs       Media Library             │
                              │                   │
             ┌────────────────┼──────────────┐    │
             │                │              │    │
         Portfolio      Emotional       Projects │
                            Portfolio       │     │
                               │          Roles   │
                         Emotional Grid    │      │
                                          │      │
                        Training ──────────┘      │
                              │                  │
                              └──────────┬───────┘
                                         │
                                  QUESTIONNAIRES
                                  │             │
                             Prepared       Public Builder
                             Revisions       Sessions
                                  │             │
                                  └──────┬──────┘
                                         │
                                    PUBLIC SITE
                                         │
                                  Professional CTA
                                         │
                                      FEEDBACK
                                         │
                                      CASTING
                                  ┌──────┴──────┐
                                  │             │
                              AI ANALYSIS    OUTCOME
                                  │             │
                             Questionnaire     Role
                                 Draft
```

---

# 155. Главный принцип Information Architecture

Все интерфейсы продукта должны строиться из общих достоверных сущностей, но получать **своё строго определённое представление данных**.

Итоговый принцип:

```text
ONE SOURCE OF TRUTH
        ↓
MULTIPLE CONTROLLED PROJECTIONS
        ↓
ADMIN / PUBLIC / BUILDER / PDF / AI / SEARCH
```

Ни один presentation layer не должен создавать независимую конкурирующую копию профессиональных фактов.
