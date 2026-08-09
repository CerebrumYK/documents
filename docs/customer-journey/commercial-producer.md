# COMMERCIAL PRODUCER JOURNEY

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Подробный Customer Journey коммерческого кастинг-продюсера

**Целевой файл:** `docs/customer-journey/commercial-producer.md`  
**Документ:** DOC-025  
**Статус:** ✅ Completed  
**Тип:** Customer Journey

**Persona:** `PERSONA-CP — Commercial Casting Producer`  
**Рабочее имя Persona:** Мария

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/overview.md`
- `docs/customer-journey/personas.md`
- `docs/customer-journey/method-6-3-5.md`
- `docs/customer-journey/casting-director.md`
- `docs/customer-journey/casting-assistant.md`

---

# 1. Назначение документа

Настоящий документ описывает Customer Journey коммерческого кастинг-продюсера.

Основная особенность Persona:

> **решение часто начинается с визуального впечатления, принимается на мобильном устройстве и требует очень короткого пути от просмотра актуальной внешности и эмоционального диапазона до контакта или приглашения на кастинг.**

Документ является нормативным источником для проектирования:

- Mobile Public UX;
- Hero;
- Portfolio;
- Emotional Grid;
- Shooting Date;
- Video Intro;
- Contextual CTA;
- Quick Questionnaire;
- Public Questionnaire Builder;
- Feedback;
- Social Entry;
- Marketing Attribution;
- E2E Mobile Tests.

---

# 2. Journey ID

Основной Journey:

```text
CJM-CP-001
```

Название:

```text
Commercial Casting Producer — Mobile Visual Evaluation to Professional Action
```

---

# 3. Persona Summary

## Persona

Commercial Casting Producer.

## Рабочее имя

Мария.

## Профессиональная область

Чаще всего:

- реклама;
- брендовые ролики;
- digital production;
- lifestyle video;
- social campaigns;
- промоматериалы;
- корпоративные ролики;
- короткие коммерческие форматы.

---

# 4. Основная профессиональная задача

> Быстро определить, подходит ли внешность, типаж, эмоциональная подача и присутствие актрисы в кадре для конкретного коммерческого проекта, после чего немедленно перейти к контакту или следующему этапу кастинга.

---

# 5. Decision Authority

Commercial Producer MAY:

- самостоятельно формировать shortlist;
- рекомендовать кандидата клиенту;
- отправлять Casting Invitation;
- запрашивать Self-Tape;
- запрашивать дополнительное видео или фото;
- передавать профиль режиссёру или клиенту.

В некоторых проектах Persona является непосредственным decision maker.

---

# 6. Основное отличие от Casting Director

Casting Director чаще начинает с:

```text
requirements
→ structured qualification
→ evidence
```

Commercial Producer нередко начинает с:

```text
visual impression
→ emotional fit
→ camera presence
→ practical facts
→ action
```

Поэтому информационная и визуальная иерархия Journey отличается.

---

# 7. Основной контекст использования

Типичная ситуация:

- пользователь находится не в офисе;
- ссылка пришла через Instagram, TikTok, WhatsApp или Telegram;
- устройство — смартфон;
- одновременно обсуждаются несколько кандидатов;
- решение нужно быстро;
- длинное CV может вообще не открываться;
- фотографии и короткое видео имеют высокий вес.

---

# 8. Primary Device

Основное устройство:

```text
Smartphone
```

Вторичные:

- Tablet;
- Desktop;
- Notebook.

Этот Journey является одним из основных оснований для mobile-first проектирования критических Public flows.

---

# 9. Entry Sources

Типичные точки входа:

```text
CP-ENTRY-01 Instagram
CP-ENTRY-02 TikTok
CP-ENTRY-03 Messenger Link
CP-ENTRY-04 Direct Recommendation
CP-ENTRY-05 Shared Portfolio
CP-ENTRY-06 Shared Emotional Portfolio
CP-ENTRY-07 QR Code
CP-ENTRY-08 Search
CP-ENTRY-09 Shared Questionnaire
```

---

# 10. Entry Source Importance

В отличие от desktop-oriented Casting Assistant, Commercial Producer очень часто приходит не через Home Page.

Следовательно, direct page:

```text
/portfolio
/emotional-portfolio/{slug}
/questionnaire
/projects/{slug}
```

MUST сохранять:

- Actress Identity;
- current professional context;
- access to Video;
- access to Questionnaire;
- Contextual CTA.

---

# 11. Primary Mental Model

Commercial Producer мысленно проходит последовательность:

```text
Как выглядит?
 ↓
Актуально ли это?
 ↓
Как меняется эмоционально?
 ↓
Как выглядит в движении?
 ↓
Есть ли практические ограничения?
 ↓
Как быстро связаться?
```

---

# 12. Desired Outcome

Основной результат:

```text
Visual and Commercial Fit Confirmed
+
Professional Contact Initiated
```

Возможные следующие действия:

```text
Invite to Casting
Offer Role
Request Self-Tape
Request Materials
Collaboration
```

---

# 13. Business Outcome

Primary Conversion:

```text
Commercial Professional Inquiry
```

или:

```text
Casting Invitation
```

Secondary:

```text
Video Play
Emotional Grid View
Questionnaire Download
Custom PDF
```

---

# 14. Time Budget

Рекомендуемая модель:

```text
00:00–00:10
Visual Recognition

00:10–00:45
Current Appearance

00:45–01:30
Emotional Range

01:30–02:15
Video

02:15–03:00
Practical Qualification

03:00–04:00
Questionnaire if needed

04:00–05:00
Professional Action
```

В ряде коммерческих сценариев целевое действие может произойти уже на первой или второй минуте.

---

# 15. Stage CP-01 — Social / Mobile Entry

Пользователь открывает ссылку на smartphone.

Первое состояние не должно содержать:

- тяжёлую заставку;
- обязательный Cookie/Signup wall сверх необходимого;
- desktop-style mega menu;
- большой блок текста до фотографии;
- autoplay с неудобным звуком.

---

# 16. Stage CP-02 — Visual Recognition

**Время:** `00:00–00:10`

Пользователь должен увидеть:

- имя;
- Primary Close-Up;
- professional identity;
- краткий context;
- быстрые actions.

---

# 17. First-Screen Requirement

Mobile first screen SHOULD позволять понять:

```text
Who
Visual Type
Professional Role
What Next
```

без обязательного scroll через декоративный content.

---

# 18. Mobile Hero Priority

Рекомендуемый logical priority:

```text
Primary Photo
Name
Actress
Current Location / Key Fact
Video
Questionnaire
Contact / Casting CTA
```

---

# 19. Stage CP-03 — Current Appearance

**Время:** `00:10–00:45`

Commercial Producer хочет увидеть:

- Current Close-Up;
- Full Body;
- Appearance;
- Three Quarter;
- дополнительные естественные ракурсы.

---

# 20. Appearance Is Evidence

Для этой Persona Portfolio выполняет не декоративную функцию.

Он отвечает на профессиональный вопрос:

> «Как актриса реально выглядит сейчас и подходит ли этот типаж проекту?»

---

# 21. Shooting Date as Trust Signal

Дата съёмки SHOULD быть показана там, где она известна.

Особенно полезно:

```text
Съёмка: июль 2026
```

вместо неопределённого ощущения, что фото могли быть сделаны несколько лет назад.

---

# 22. Shooting Date Semantics

Дата:

- помогает оценить freshness;
- не заменяет judgement;
- не означает автоматического устаревания старых материалов;
- не должна скрываться в admin metadata.

---

# 23. Portfolio Mobile Interaction

На mobile Portfolio SHOULD поддерживать:

- swipe;
- tap;
- fullscreen/lightbox;
- быстрые category filters;
- predictable back navigation.

---

# 24. No Tiny Desktop Gallery

Недопустимо:

```text
desktop grid
→ просто уменьшена до экрана телефона
```

Mobile UX должен учитывать touch interaction и размер изображения.

---

# 25. Image Loading Priority

На mobile критично:

1. сначала Primary image;
2. затем visible gallery items;
3. затем остальной материал.

Original high-resolution assets не должны загружаться для обычных thumbnails.

---

# 26. Stage CP-04 — Emotional Evaluation

**Время:** `00:45–01:30`

После оценки внешности Persona хочет увидеть:

> «Насколько быстро меняется эмоциональный образ?»

---

# 27. Emotional Grid as Primary Entry

Первым представлением SHOULD быть:

```text
Emotional Grid
```

а не длинный список отдельных фотографий.

---

# 28. Why Emotional Grid Matters

Коммерческий ролик может требовать в течение короткой сцены:

- радость;
- удивление;
- сомнение;
- серьёзность;
- обеспокоенность;
- естественную улыбку.

Grid позволяет быстро оценить разнообразие.

---

# 29. Emotional Grid Mobile Requirement

Composite должен:

- хорошо читаться на smartphone;
- не требовать горизонтального scroll;
- сохранять различимость каждой ячейки;
- открываться крупнее по tap.

---

# 30. Grid to Full Portfolio

Если пользователь хочет больше:

```text
Emotional Grid
→ Full Emotional Portfolio
```

Это реализация Progressive Disclosure.

---

# 31. Emotional Shooting Date

Shooting Date должна быть доступна рядом с Emotional content.

Для Commercial Producer это особенно важный Trust Signal.

---

# 32. Emotional Authenticity

Emotional representation MUST использовать реальные фотографии.

Не допускается AI-generation выражений лица или синтетических эмоций.

---

# 33. Stage CP-05 — Video Intro

**Время:** `01:30–02:15`

Commercial Producer запускает короткое Video Intro.

---

# 34. Video Purpose

На этом этапе Persona оценивает:

- camera presence;
- natural movement;
- voice;
- facial dynamics;
- comfort in front of camera.

---

# 35. Video Length UX Principle

Продукт не обязан технически ограничивать Video Intro определённой длиной в данном документе.

Но Public UX должен позволять быстро понять:

- что это Video Intro;
- длительность, если доступна;
- что происходит после play.

---

# 36. Video Mobile Behaviour

Video MUST:

- работать inline/fullscreen предсказуемо;
- иметь понятный play control;
- не ломать page navigation;
- не требовать hover;
- иметь fallback при external provider failure.

---

# 37. Autoplay Policy

Video SHOULD NOT неожиданно воспроизводить звук при загрузке страницы.

Профессиональный пользователь может находиться:

- на встрече;
- на площадке;
- в транспорте.

---

# 38. Video Failure

Если Video недоступно:

```text
Photo Journey continues
Questionnaire remains available
CTA remains available
```

Ошибка одного media provider не блокирует весь путь.

---

# 39. Stage CP-06 — Practical Qualification

**Время:** `02:15–03:00`

Только после visual fit Commercial Producer может проверить:

- location;
- language;
- specific Skill;
- selected experience;
- availability-related published facts, если такие появятся в Scope.

---

# 40. Practical Facts Must Be Compact

Для коммерческого сценария не нужен большой information wall.

Пример:

```text
Алматы
Русский — родной
English — B2

Навыки:
Вождение
Плавание
Танцы
```

---

# 41. Skills Relevance

Skills MAY играть очень высокую роль для отдельных реклам:

- спорт;
- танцы;
- вождение;
- вокал;
- работа с животными.

Поэтому Persona должна иметь быстрый доступ к structured Skill.

---

# 42. Search Usage

Если нужен конкретный навык:

```text
Search
→ Skill
```

должен быть быстрее изучения Biography или Projects.

---

# 43. Projects Priority

В коммерческом Journey Projects чаще являются secondary evidence.

Сначала:

```text
Visual
Emotional
Video
```

затем:

```text
Selected commercial / relevant Projects
```

---

# 44. Relevant Commercial Experience

Если у актрисы есть релевантный рекламный опыт, Featured Projects MAY повысить доверие.

Но отсутствие такого опыта не должно автоматически означать низкую suitability.

---

# 45. Stage CP-07 — Decision Point

На этом этапе Commercial Producer может уже быть готов действовать.

Система не должна насильно вести его через Questionnaire.

---

# 46. Short Conversion Path

Корректный Journey:

```text
Social Link
 ↓
Close-Up / Full Body
 ↓
Emotional Grid
 ↓
Video
 ↓
Invite to Casting
```

Questionnaire может не открываться вообще.

---

# 47. Stage CP-08 — Questionnaire Optional Path

Если production process требует structured document:

```text
Quick / Standard Questionnaire
```

должна быть доступна немедленно.

---

# 48. Most Relevant Questionnaire Types

Для Persona наиболее вероятны:

```text
Quick
Standard
Casting
```

Extended чаще является optional deeper path.

---

# 49. Questionnaire Visual Priority

Commercial document MAY логически приоритизировать:

- Close-Up;
- Full Body;
- Emotional Grid;
- Video;
- practical facts;
- Contact.

Точный Template composition определяется Questionnaire module.

---

# 50. Public Builder Use Case

Public Builder полезен, если:

- нужен короткий клиентский PDF;
- Training нерелевантен;
- нужен конкретный Emotional Grid;
- необходимо добавить specific Skill;
- нужно выбрать только свежие Portfolio photos;
- необходимо показать один relevant Project.

---

# 51. Preferred Builder Entry

Оптимально:

```text
Quick / Standard
→ Customize
```

---

# 52. Mobile Builder Requirement

Поскольку Persona mobile-first, Builder MUST быть реально usable на smartphone.

Не просто технически доступен.

---

# 53. Mobile Builder Sequence

Рекомендуемый logical pattern:

```text
Choose Template
 ↓
Review Included Blocks
 ↓
Adjust Sections
 ↓
Select Items
 ↓
Preview
 ↓
Generate
```

---

# 54. Builder Must Avoid Dense Desktop UI

На smartphone не следует отображать:

- три колонки;
- drag-and-drop как единственный способ reordering;
- мелкие checkboxes;
- длинные nested trees без progressive disclosure.

---

# 55. Item Selection

Для mobile предпочтительны:

- large touch targets;
- cards;
- concise labels;
- thumbnail selection;
- selected state.

---

# 56. Builder Preview

Preview должен быть достаточно точным, чтобы пользователь понял:

- что включено;
- порядок;
- основные изображения;
- приблизительный объём.

---

# 57. PDF Generation

Generated PDF должен быть удобен для:

- пересылки клиенту;
- отправки в messenger;
- открытия на smartphone;
- просмотра на desktop.

---

# 58. Stage CP-09 — Contextual CTA

Для Commercial Producer универсальное:

```text
Связаться
```

может быть недостаточно конкретным.

Рекомендуемые действия:

```text
Пригласить на кастинг
Предложить роль
Предложить сотрудничество
Запросить материалы
```

---

# 59. CTA Selection by Context

На Portfolio:

```text
Пригласить на кастинг
```

На Project:

```text
Предложить сотрудничество
```

После Questionnaire:

```text
Пригласить на кастинг
```

После Video:

```text
Запросить материалы
```

Точный mapping определяется UX specification.

---

# 60. CTA Must Not Imply Commitment

Кнопка:

```text
Предложить роль
```

создаёт Professional Inquiry.

Она не означает автоматическое согласие актрисы на участие.

---

# 61. Stage CP-10 — Professional Form

Форма должна быть особенно удобна на smartphone.

---

# 62. Mobile Form Requirements

Минимально:

- соответствующий keyboard type;
- large inputs;
- autofill where safe;
- clear error;
- no unnecessary fields;
- attachment handling suited to mobile.

---

# 63. Minimum Professional Inquiry

На первом контакте разумно запросить:

- Name;
- Organization/Brand;
- Project;
- Message;
- reply Contact.

Optional:

- Role;
- deadline;
- file.

---

# 64. Contact Flexibility

Commercial Producer может предпочитать:

- Phone;
- WhatsApp;
- Telegram;
- email-like form where supported.

Public Contacts availability определяется Visibility policy.

---

# 65. Feedback Persistence

Порядок:

```text
Validate
 ↓
Persist Feedback
 ↓
Show Success
 ↓
Internal Notification
 ↓
Optional WhatsApp Notification
```

---

# 66. Notification Failure

Если WhatsApp notification не отправилось, Commercial Producer не должен видеть ошибку:

```text
Ваше обращение потеряно
```

если Feedback успешно сохранён.

---

# 67. Success Confirmation

После submit SHOULD отображаться:

- успешная отправка;
- Reference Number;
- что произойдёт дальше в нейтральной форме.

---

# 68. Main Happy Path CJM-CP-001

```text
Instagram
 ↓
Mobile Profile
 ↓
Current Close-Up
 ↓
Full Body
 ↓
Emotional Grid
 ↓
Video Intro
 ↓
Invite to Casting
 ↓
Feedback Submitted
```

---

# 69. Questionnaire Path CJM-CP-002

```text
Messenger Link
 ↓
Portfolio
 ↓
Emotional Grid
 ↓
Video
 ↓
Quick Questionnaire
 ↓
Download PDF
 ↓
Offer Role
```

---

# 70. Custom PDF Path CJM-CP-003

```text
Profile
 ↓
Standard Questionnaire
 ↓
Customize
 ↓
Remove Training
 ↓
Choose Current Portfolio
 ↓
Add Emotional Grid
 ↓
Generate PDF
 ↓
Share with Client
```

---

# 71. Emotional-First Path CJM-CP-004

```text
Shared Emotional Portfolio Link
 ↓
Emotional Grid
 ↓
Full Emotional Portfolio
 ↓
Video
 ↓
Profile Quick Facts
 ↓
Casting CTA
```

---

# 72. QR Path CJM-CP-005

```text
Printed / PDF Material
 ↓
QR
 ↓
Current Mobile Profile
 ↓
Video / Portfolio
 ↓
Contact
```

---

# 73. Direct Project Path CJM-CP-006

```text
Relevant Commercial Project
 ↓
Role / Media
 ↓
Current Portfolio
 ↓
Video
 ↓
Professional CTA
```

---

# 74. Mobile Sticky Action Bar

Для длинных страниц MAY использоваться:

```text
Видео
Анкета
Связаться
```

или contextual equivalent.

---

# 75. Sticky CTA Rules

Sticky UI MUST NOT:

- перекрывать captions;
- перекрывать form buttons;
- уменьшать usable viewport чрезмерно;
- дублировать слишком много actions.

---

# 76. Friction CP-FRC-01 — Slow Mobile Load

Причины:

- originals вместо derivatives;
- тяжёлый Hero;
- eager-loaded gallery.

Impact:

Persona может просто закрыть ссылку.

---

# 77. Resolution CP-FRC-01

Использовать:

- optimized derivatives;
- responsive image sizes;
- lazy loading secondary content;
- priority loading P0 asset.

---

# 78. Friction CP-FRC-02 — Desktop Navigation

Если smartphone user должен открывать nested menu для Video или Questionnaire — Journey ухудшается.

---

# 79. Friction CP-FRC-03 — Missing Shooting Date

Пользователь не понимает, насколько материал отражает current appearance.

---

# 80. Friction CP-FRC-04 — Too Many Emotional Photos

30 отдельных фотографий требуют слишком много времени.

Resolution:

```text
Emotional Grid first
```

---

# 81. Friction CP-FRC-05 — Long Video Required

Если единственным video evidence является длинный Showreel, пользователь может не получить quick signal.

Resolution:

```text
Video Intro
+
Showreel as depth
```

---

# 82. Friction CP-FRC-06 — Huge PDF

Большой Extended PDF плохо работает как быстрый коммерческий документ.

Resolution:

- Quick;
- Standard;
- Builder.

---

# 83. Friction CP-FRC-07 — Hidden Contact

Contact должен быть доступен из текущего professional context.

---

# 84. Friction CP-FRC-08 — Tiny Mobile Form

Форма, спроектированная только для desktop, создаёт direct conversion loss.

---

# 85. Friction CP-FRC-09 — Social Link Loses Context

После Instagram пользователь не должен попадать на абстрактную заставку и искать Portfolio заново.

---

# 86. Friction CP-FRC-10 — Artificially Edited Appearance

Если professional material выглядит недостоверно, trust падает.

---

# 87. Trust Signals

Для Commercial Producer особенно важны:

```text
Current real photography
Shooting Date
Natural visual presentation
Emotional Grid
Working Video Intro
Clear location
Simple professional CTA
Official profile identity
```

---

# 88. Trust Signal — Consistency Across Media

Primary Portfolio, Emotional Portfolio и Video должны представлять одного человека без вводящих в заблуждение визуальных трансформаций.

---

# 89. Trust Signal — Date

Date особенно полезна в:

- Emotional Portfolio;
- current Portfolio shoots.

---

# 90. Trust Signal — Fast Working Links

Commercial user часто принимает решение быстро.

Broken Video/CTA имеет высокий negative impact.

---

# 91. Information Priority — P0

```text
Primary Close-Up
Full Body
Professional Identity
Shooting Date
Emotional Grid
Video Intro
Location
Casting / Role CTA
```

---

# 92. Information Priority — P1

```text
Appearance
Key Skills
Languages
Selected Projects
Quick Questionnaire
```

---

# 93. Information Priority — P2

```text
Roles
Training
Achievements
Showreel
Additional Links
```

---

# 94. Information Priority — P3

```text
Full Biography
Archive
Blog
Full Training History
```

---

# 95. Biography Role

Biography MAY support personal/professional brand.

Она не должна находиться между:

```text
visual interest
```

и:

```text
Portfolio / Video
```

как обязательный большой блок.

---

# 96. Social Entry Optimization

Social-origin visitor already has some visual context.

Поэтому page MAY prioritize:

```text
Current Professional Facts
Portfolio
Video
CTA
```

вместо повторного social-style storytelling.

---

# 97. Attribution

Если пользователь приходит из:

```text
Instagram
TikTok
campaign
messenger
```

source MAY сохраняться для aggregate conversion analytics.

---

# 98. Attribution Must Not Change Facts

Source MAY менять:

- priority;
- highlighted CTA;
- landing context.

Но не:

- Profile facts;
- visibility;
- experience;
- Skills.

---

# 99. Conversion Events

Рекомендуемые logical events:

```text
commercial_profile_viewed
mobile_portfolio_opened
portfolio_photo_opened
emotional_grid_viewed
emotional_portfolio_opened
video_intro_started
quick_questionnaire_opened
public_builder_opened
public_builder_pdf_generated
casting_cta_started
role_offer_started
collaboration_started
professional_inquiry_submitted
```

---

# 100. KPI — Mobile Professional Action Rate

Доля mobile professional sessions, закончившихся:

- Casting CTA;
- Role Offer;
- Request Materials;
- Questionnaire.

---

# 101. KPI — Time to Visual Evidence

Время до:

```text
Portfolio / Emotional Grid / Video
```

---

# 102. KPI — Emotional Grid Engagement

Полезно анализировать:

```text
Emotional Grid viewed
→ Full Emotional Portfolio
→ Professional CTA
```

---

# 103. KPI — Video Start Rate

Особенно важен для visual-first traffic.

---

# 104. KPI — Mobile Form Completion

Если form starts много, а completion низок, необходимо анализировать friction.

---

# 105. KPI — Quick vs Extended PDF

Для Commercial Persona может быть полезно видеть, какой format используется чаще.

---

# 106. Analytics Caution

Большое число Photo Views не обязательно означает лучший Journey.

Пользователь мог не найти нужный ракурс.

Поэтому Gallery Depth должна анализироваться вместе с:

- Portfolio filters;
- subsequent CTA;
- Questionnaire conversion.

---

# 107. Virtual Operator Support

Для этого Journey Virtual Operator SHOULD приоритизировать:

- broken Primary Image derivative;
- broken Video Intro;
- missing Shooting Date;
- missing Primary Full Body;
- Emotional Grid render failure;
- broken Contact;
- mobile-critical content problems where observable.

---

# 108. Virtual Operator Content Freshness

Operator MAY предложить проверить старый Primary Portfolio Item.

Но не имеет права автоматически:

- скрывать;
- удалять;
- заменять.

---

# 109. Relevant 6-3-5 Insights

Основные:

```text
INS-CP-01 Mobile-First Casting Experience
INS-CP-02 Shooting Date as Trust Signal
INS-CP-03 Contextual Commercial CTA
```

Дополнительные:

```text
INS-CD-01 Casting Quick View
INS-CD-03 Casting-Specific CTA
INS-CA-02 Portfolio Filters
INS-CA-03 Compact PDF
INS-DIR-01 Emotional Range
```

---

# 110. Relevant Business Rules

Ключевые:

```text
BR-POR-003
BR-POR-004
BR-POR-012
BR-POR-013

BR-EMO-003
BR-EMO-005
BR-GRD-006
BR-GRD-008
BR-GRD-009

BR-CJM-004
BR-CJM-005
BR-CJM-006
BR-CJM-007

BR-CTA-001
BR-CTA-002
BR-CTA-003

BR-PQB-001–030
BR-FDB-001–015
```

---

# 111. Relevant Product Principles

Особенно:

```text
PP-004 Casting Professional Drives Public UX
PP-005 Minimum Actions
PP-006 Progressive Disclosure
PP-007 Professional Signal Before Decoration
PP-008 No Dead Ends
PP-029 AI Must Not Alter Actor Appearance
PP-030 Safe Geometric Image Processing
PP-052 Mobile Is a First-Class Experience
PP-053 Five-Minute Professional Journey
PP-054 Quick Decision in Seconds
PP-055 Contextual CTA
PP-071 Responsive Means Contextual
PP-083 Performance Serves Professional Flow
PP-084 Originals Are Not Public Thumbnails
```

---

# 112. Future User Flows

Из Journey должны быть сформированы минимум:

```text
UF-PUB-CP-001 Open Profile From Social
UF-PUB-CP-002 Review Current Portfolio
UF-PUB-CP-003 Open Full Body
UF-PUB-CP-004 Open Emotional Grid
UF-PUB-CP-005 Open Full Emotional Portfolio
UF-PUB-CP-006 Play Video Intro
UF-PUB-CP-007 Check Skill / Language
UF-QST-CP-001 Open Quick Questionnaire
UF-PQB-CP-001 Customize Commercial Questionnaire
UF-PQB-CP-002 Generate Mobile-Initiated PDF
UF-FDB-CP-001 Invite to Casting
UF-FDB-CP-002 Offer Role
UF-FDB-CP-003 Collaboration Inquiry
```

---

# 113. Functional Requirement Areas

```text
FR-MOBILE
FR-PROFILE
FR-PORTFOLIO
FR-EMOTIONAL
FR-VIDEO
FR-SKILLS
FR-QUESTIONNAIRE
FR-PQB
FR-FEEDBACK
FR-PERFORMANCE
FR-ANALYTICS
```

---

# 114. E2E-CJM-CP-001 — Mobile Happy Path

```text
Given
a professional profile with public visual content

When
Commercial Producer opens it on a mobile viewport

Then
they can:
- identify actress
- open Close-Up
- open Full Body
- see Shooting Date where available
- open Emotional Grid
- start Video Intro
- start Casting Invitation
- submit inquiry
```

---

# 115. E2E-CJM-CP-002 — Social Deep Link

```text
Given
user opens a direct Emotional Portfolio URL

Then
they can:
- identify actress
- view Emotional Grid
- open Video
- access Questionnaire
- start Professional CTA
```

without first navigating through Home.

---

# 116. E2E-CJM-CP-003 — Mobile Builder

Проверить на mobile viewport:

- Template selection;
- block selection;
- item selection;
- Preview;
- PDF generation;
- no horizontal overflow;
- accessible controls.

---

# 117. E2E-CJM-CP-004 — Broken Video

```text
Given
Video Intro is unavailable

Then
Portfolio, Emotional Grid, Questionnaire and Contact remain usable
```

---

# 118. E2E-CJM-CP-005 — Contact Privacy

Only Contacts explicitly allowed for relevant Public context appear.

Private Guardian Contact MUST NOT be returned to browser.

---

# 119. E2E-CJM-CP-006 — Emotional Authenticity Configuration

Grid rendering MUST use approved source photo + geometric crop configuration and must not require/generated synthetic image content.

---

# 120. E2E-CJM-CP-007 — Feedback Notification Failure

Feedback remains saved if external notification fails.

---

# 121. E2E-CJM-CP-008 — Responsive Images

Mobile Public Gallery should request/use an appropriate derivative rather than automatically serving the maximum original for every card.

Exact performance thresholds are defined later in NFR.

---

# 122. Journey Score Target

| Критерий | Target |
|---|---:|
| Recognition | 2 |
| Visual Evidence | 2 |
| Emotional / Video | 2 |
| Mobile Usability | 2 |
| Professional Action | 2 |

Цель:

```text
10/10
```

Минимум:

```text
9/10
```

---

# 123. Hard Failure Conditions

Journey требует redesign, если:

1. ключевой visual content плохо работает на mobile;
2. Full Body трудно найти;
3. Shooting Date существует, но недоступна там, где важна freshness;
4. Emotional Range можно оценить только просмотром большого альбома;
5. Video нельзя удобно открыть на smartphone;
6. Professional CTA спрятан;
7. форма неудобна на touch device;
8. Public Builder фактически desktop-only;
9. social deep link превращается в dead end;
10. private data попадают в client payload;
11. изображения профессионально изменяют внешность;
12. уведомительная ошибка приводит к потере обращения.

---

# 124. Mobile Performance Principle

Для Persona особенно важно:

```text
First Useful Visual
>
Complete Page Download
```

Система должна сначала обеспечивать полезный visible content, а не загружать весь media archive.

---

# 125. Mobile Interaction Principle

Любая функция, требующая:

```text
hover
```

как единственного способа взаимодействия, считается недостаточной для этого Journey.

---

# 126. Touch Target Principle

Критические interactive elements должны иметь комфортные touch targets.

Конкретные accessibility размеры фиксируются в UX/Accessibility документации.

---

# 127. One-Handed Use Consideration

Primary mobile actions SHOULD располагаться с учётом обычного smartphone interaction, если это не конфликтует с accessibility и content hierarchy.

---

# 128. Orientation

Journey должен оставаться работоспособным в portrait.

Landscape MAY улучшать:

- Video;
- Grid Preview;

но не должен быть обязательным.

---

# 129. Commercial Visual First ≠ Appearance Only

Несмотря на визуальный приоритет, продукт не должен сводить профессиональное решение только к внешности.

Следующие layers доступны сразу после visual fit:

- Video;
- Skills;
- Languages;
- Experience;
- Questionnaire.

---

# 130. No Appearance Ranking

Продукт не оценивает:

- привлекательность;
- соответствие beauty standards;
- сравнение внешности с другими актрисами.

Persona самостоятельно принимает профессиональное решение.

---

# 131. No Artificial Freshness

Система не должна автоматически ставить:

```text
актуальное
```

только потому, что файл недавно загружен.

Используется фактическая Shooting Date, если она известна.

---

# 132. Commercial Questionnaire Principle

Коммерческий документ должен быть способен предоставить:

```text
strong visual evidence
+
minimal required facts
```

без принудительного включения большого объёма нерелевантной профессиональной истории.

---

# 133. Share to Client Principle

Commercial Producer может быть не конечным decision maker.

Поэтому:

```text
Profile URL
PDF
Selected visual material
```

должны быть понятны после пересылки клиенту или режиссёру.

---

# 134. QR Use

QR особенно полезен для:

- PDF;
- printed shortlist;
- презентации клиенту.

Но QR дополняет, а не заменяет clickable URL.

---

# 135. Direct Contact vs Feedback

Если разрешён direct Contact:

Commercial Producer MAY использовать его.

Если нужен structured professional request:

используется Feedback.

Оба пути могут сосуществовать согласно Contact Visibility и UX.

---

# 136. Context Preservation

При переходе:

```text
Social
→ Profile
→ Questionnaire
→ Back
```

желательно сохранять:

- language;
- source attribution;
- active Builder Session if any.

---

# 137. Commercial Journey and Blog

Blog не является частью critical five-minute path.

Он MAY использоваться как secondary marketing/SEO content.

---

# 138. Commercial Journey and Training

Training обычно P2.

Он не должен визуально конкурировать с:

- Portfolio;
- Emotional;
- Video;
- CTA.

---

# 139. Commercial Journey and Achievements

Achievement MAY усиливать доверие, но не является обязательным этапом коммерческого screening.

---

# 140. Mental Model

```text
LOOK
 ↓
FEEL
 ↓
SEE IN MOTION
 ↓
CHECK
 ↓
ACT
```

---

# 141. Product Response

```text
LOOK
→ Current Portfolio

FEEL
→ Emotional Grid

SEE IN MOTION
→ Video Intro

CHECK
→ Quick Facts / Questionnaire

ACT
→ Contextual CTA
```

---

# 142. Definition of Journey Compliance

Public UX соответствует `CJM-CP-001`, если Commercial Producer:

1. может начать Journey непосредственно с social/deep link;
2. получает meaningful visual impression за секунды;
3. быстро открывает Current Close-Up и Full Body;
4. видит Shooting Date, где она известна;
5. получает Emotional Range через компактный Grid;
6. запускает Video удобно на mobile;
7. проверяет practical facts без чтения длинной Biography;
8. может открыть Quick/Standard Questionnaire;
9. при необходимости использует Public Builder со smartphone;
10. быстро достигает Contextual CTA;
11. может отправить Professional Inquiry без регистрации;
12. private data не раскрываются;
13. core Journey реалистично укладывается в пять минут.

---

# 143. Итоговая схема

```text
COMMERCIAL CASTING PRODUCER

Social / Messenger / Referral
            ↓
       MOBILE ENTRY
            ↓
      CURRENT CLOSE-UP
            ↓
        FULL BODY
            ↓
      SHOOTING DATE
            ↓
     EMOTIONAL GRID
            ↓
       VIDEO INTRO
            ↓
       QUICK FACTS
            ↓
     Need document?
        /       \
      NO         YES
       │          │
       │      Quick / Standard
       │          │
       │      Customize?
       │       /     \
       │     NO       YES
       │      │         │
       │   Download   Builder
       │      │         │
       └──────┴────┬────┘
                   ↓
           CONTEXTUAL CTA
                   ↓
      Casting / Role / Collaboration
                   ↓
                FEEDBACK
```

---

# 144. Финальный принцип

> **Commercial Producer Journey должен позволять принять первичное профессиональное решение непосредственно со смартфона: быстро увидеть актуальную реальную внешность, оценить эмоциональный диапазон и поведение в кадре, проверить несколько практических критериев и немедленно перейти к релевантному профессиональному действию. Mobile в этом сценарии является не дополнительным представлением desktop-сайта, а полноценной рабочей средой кастинга.**