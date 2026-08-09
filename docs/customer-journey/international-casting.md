# INTERNATIONAL CASTING JOURNEY

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Подробный Customer Journey международного кастинг-специалиста

**Целевой файл:** `docs/customer-journey/international-casting.md`  
**Документ:** DOC-027  
**Статус:** ✅ Completed  
**Тип:** Customer Journey

**Persona:** `PERSONA-INT — International Casting Specialist`  
**Рабочее имя Persona:** Sophie

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
- `docs/customer-journey/commercial-producer.md`
- `docs/customer-journey/director.md`

---

# 1. Назначение документа

Настоящий документ описывает Customer Journey международного кастинг-специалиста.

Основная задача:

> **предоставить иностранному профессиональному пользователю полноценный профессиональный профиль, который не требует знания русского языка и не теряет бизнес-смысл при локализации.**

International Journey является не отдельной урезанной версией продукта, а локализованной проекцией общего Actor Profile.

---

# 2. Journey ID

Основной Journey:

```text
CJM-INT-001
```

Название:

```text
International Casting Specialist — Localized Evaluation to Share-Ready Professional Package
```

---

# 3. Persona Summary

## Persona

International Casting Specialist.

## Рабочее имя

Sophie.

## Возможные должности

- International Casting Coordinator;
- Casting Producer;
- Talent Researcher;
- Casting Assistant;
- Production Coordinator;
- Talent Coordinator.

---

# 4. Основная профессиональная задача

> Быстро понять профессиональный профиль актрисы, проверить языки, location, ключевые навыки и опыт, посмотреть доказательства и получить документ, который можно передать международной production team без дополнительного перевода или пояснений.

---

# 5. Ключевая особенность Persona

Для этой Persona важна не только доступность данных, но и их **международная интерпретируемость**.

Например:

```text
English — B2
```

намного полезнее, чем:

```text
English — хороший
```

А:

```text
Based in Almaty, Kazakhstan
```

понятнее, чем внутреннее сокращение или локальное обозначение без страны.

---

# 6. Decision Authority

International Casting Specialist может:

- проводить screening;
- формировать shortlist;
- передавать кандидата Casting Director;
- запрашивать материалы;
- отправлять Casting Invitation;
- координировать международную production communication.

Уровень authority зависит от конкретной роли, поэтому система не должна предполагать, что Persona всегда принимает окончательное решение.

---

# 7. Типичный профессиональный контекст

Пользователь:

- находится в другой стране;
- работает преимущественно на английском или другом поддерживаемом языке;
- может не знать локальный рынок;
- не понимает русскоязычные сокращения;
- может пересылать материал другим участникам production team;
- сравнивает кандидатов из разных стран;
- ожидает стандартные профессиональные формулировки.

---

# 8. Primary Device

Основные:

- Desktop;
- Notebook.

Дополнительно:

- Tablet;
- Smartphone.

Journey MUST оставаться полноценным на mobile.

---

# 9. Entry Sources

Типичные Entry Points:

```text
INT-ENTRY-01 Search Engine
INT-ENTRY-02 Casting Platform Reference
INT-ENTRY-03 Recommendation
INT-ENTRY-04 Messenger
INT-ENTRY-05 Email Link
INT-ENTRY-06 Shared Questionnaire
INT-ENTRY-07 Shared PDF
INT-ENTRY-08 QR Code
INT-ENTRY-09 Project Deep Link
INT-ENTRY-10 Casting-Specific Link
```

---

# 10. Primary Mental Model

Persona проходит последовательность:

```text
Can I understand this profile?
 ↓
Where is she based?
 ↓
What languages does she speak?
 ↓
Does she match the professional requirements?
 ↓
Can I see credible evidence?
 ↓
Can I get an English/localized package?
 ↓
Can I share or contact immediately?
```

---

# 11. Desired Outcome

Основной результат:

```text
Internationally Usable Professional Profile
+
Localized Share-Ready Questionnaire
+
Professional Next Action
```

---

# 12. Business Outcome

Primary:

```text
Qualified International Professional Interest
```

Возможные действия:

- Download Localized Questionnaire;
- Generate Custom Localized PDF;
- Request Materials;
- Contact;
- Invite to Casting.

---

# 13. Time Budget

Целевой сценарий:

```text
00:00–00:15
Language / Identity Recognition

00:15–00:45
Location + Languages

00:45–01:30
Portfolio / Skills

01:30–02:30
Video / Experience

02:30–03:30
Localized Questionnaire

03:30–05:00
Customize / Download / Contact
```

---

# 14. Stage INT-01 — Entry

International User может открыть любую public page напрямую.

Первый экран должен позволить:

- понять Actress Identity;
- увидеть или выбрать язык;
- продолжить Professional Journey.

---

# 15. Language Availability

Language Switch должен быть очевидным, но не мешать основному профессиональному content.

Минимально:

```text
RU
EN
```

при фактической поддержке этих языков.

Другие языки добавляются только если их content действительно поддерживается.

---

# 16. Stage INT-02 — Language Selection

Если текущий язык пользователю неудобен, он выполняет:

```text
Current Page
→ Language Switch
→ Same Logical Page
```

---

# 17. Global Language State Principle

Выбранный язык должен распространяться на весь дальнейший Journey.

Минимально:

```text
Profile
Portfolio labels
Projects
Questionnaire
Public Builder
PDF
CTA
Forms
Validation Messages
```

---

# 18. Language Switch Must Preserve Context

Правильно:

```text
/projects/example?lang=ru
→ switch EN
→ /en/projects/example
```

или эквивалентная архитектура.

Неправильно:

```text
Project
→ switch language
→ Home Page
```

---

# 19. Builder Language Preservation

Если Public Builder Session уже активна:

```text
Builder EN
→ browse Project
→ return
```

должна сохранять выбранный язык и selection, где это технически и безопасно возможно.

---

# 20. Language Is Presentation, Not New Facts

Перевод:

```text
Driving
```

и:

```text
Вождение
```

представляет один Skill.

Нельзя хранить две независимые professional facts только из-за локализации.

---

# 21. Translation Principle

Перевод MUST сохранять профессиональный смысл.

Он не должен:

- добавлять experience;
- повышать Skill Level;
- менять Role;
- менять Language Level;
- интерпретировать непонятный факт как подтверждённый.

---

# 22. Missing Translation

Если локализованный текст отсутствует, система должна следовать определённой fallback policy.

Она не должна незаметно заменять отсутствующий перевод AI-generated professional claim без human review.

Точная fallback policy определяется I18N/UX документацией.

---

# 23. Stage INT-03 — Location / Base

После выбора языка один из первых вопросов:

> «Где находится актриса?»

---

# 24. Location Presentation

Location должна быть международно понятной.

Например:

```text
Almaty, Kazakhstan
```

При необходимости могут отдельно существовать:

- Current Base;
- Location;
- Travel availability;

только если такие данные входят в утверждённую Domain Model.

---

# 25. Location Must Not Be Inferred

Нельзя автоматически считать:

```text
Project filmed in country X
```

равным:

```text
Actress based in country X
```

---

# 26. Stage INT-04 — Languages

Languages являются P0 information для Persona.

---

# 27. Required Language Information

Минимально:

- Language;
- Language Level;
- CEFR where applicable.

Пример:

```text
Russian — Native
English — B2
```

---

# 28. CEFR Principle

Для поддерживаемых language records международно понятная шкала SHOULD использоваться там, где это соответствует реальным данным.

---

# 29. No AI Language Inflation

Если Profile хранит:

```text
English — B2
```

AI/translation MUST NOT преобразовывать это в:

```text
Fluent English
```

если такой equivalence не определён business rules.

---

# 30. Language Search

Public Search должен позволить искать:

```text
English
Russian
B2
```

где это предусмотрено Search Specification.

---

# 31. Stage INT-05 — Visual Qualification

Persona проверяет:

- Close-Up;
- Full Body;
- current Portfolio;
- Appearance;
- relevant categories.

---

# 32. Portfolio Terminology

Категории должны использовать ясные международные labels.

Например:

```text
Close-Up
Full Body
Profile
Three-Quarter
```

а не буквальный непрофессиональный машинный перевод локального UI.

---

# 33. Photo Date

Если Shooting Date известна, она SHOULD отображаться в локализованном формате.

Например:

```text
Shot: July 2026
```

---

# 34. Date Semantics

Дата не должна зависеть от timezone таким образом, чтобы календарный день фотосессии менялся при локализации.

---

# 35. Stage INT-06 — Skills

International user должен иметь structured Skills.

---

# 36. Skill Localization

Skill имеет:

```text
canonical identity
+
localized display label
```

Например:

```text
Skill ID: driving
RU: Вождение
EN: Driving
```

---

# 37. Skill Level

Если уровень определён Product Model, он должен отображаться единообразно во всех языках.

---

# 38. Stage INT-07 — Video

International Specialist открывает Video Intro / Showreel.

---

# 39. Video Language Context

Если Video имеет metadata о языке, она MAY отображаться:

```text
Video Intro — English
```

Это помогает international screening.

---

# 40. Captions / Subtitles

Если продукт располагает approved subtitles/captions, они MAY повышать международную пригодность.

Автоматически созданные subtitles должны быть чётко отделены от human-reviewed professional text, если их точность не подтверждена.

---

# 41. Video Link Behaviour

External provider должен открываться/встраиваться предсказуемо.

International Journey не должен зависеть от локальной платформы, которая недоступна основной целевой аудитории, без fallback where possible.

---

# 42. Video Failure

При недоступности Video:

- Profile остаётся работоспособен;
- Portfolio остаётся;
- Questionnaire остаётся;
- Contact остаётся;
- Admin получает сигнал о проблеме.

---

# 43. Stage INT-08 — Professional Experience

Persona может проверить:

- Projects;
- Roles;
- Project Type;
- dates;
- relevant media.

---

# 44. Project Localization

Перевод Project description не создаёт отдельный Project.

Структура:

```text
Project
 ├── shared structured facts
 └── localized presentation fields
```

---

# 45. Project Titles

Официальное название Project SHOULD сохраняться.

Если существует официальный international title, он может использоваться согласно data model.

Не следует самовольно переводить собственные названия произведений как фактическое официальное название.

---

# 46. Role Names

Если Role/Character имеет собственное имя, оно должно локализоваться осторожно.

Имя персонажа не должно автоматически переводиться как обычное слово без подтверждённого professional context.

---

# 47. Project Type

Типы:

- Film;
- Series;
- Theatre;
- Commercial;
- Voice;
- Other;

должны иметь canonical localized labels.

---

# 48. Stage INT-09 — Training and Achievements

Для International Persona Training может быть важнее, если:

- школа международно узнаваема;
- методика релевантна;
- сертификат имеет международный контекст.

Но остаётся secondary layer после core evidence.

---

# 49. Training Localization

Нужно различать:

```text
Official Institution Name
```

и:

```text
Localized Description
```

Нельзя выдумывать официальное английское название организации.

---

# 50. Achievement Localization

Название награды/сертификата SHOULD использовать официальную international form, если она известна.

Иначе сохраняется корректная transliteration/original name + explanation where approved.

---

# 51. Stage INT-10 — Questionnaire

Наиболее важный document capability:

```text
Localized Questionnaire
```

---

# 52. Questionnaire Language

Пользователь должен иметь возможность выбрать доступный язык до PDF generation/download.

---

# 53. Localized Prepared Questionnaire

Prepared Questionnaire должна использовать:

- localized labels;
- localized approved text;
- одинаковые Master Data;
- корректное форматирование dates;
- международно понятные contacts.

---

# 54. Language Does Not Change Visibility

Переключение:

```text
RU → EN
```

не должно:

- открывать новые private fields;
- скрывать visibility restrictions;
- давать доступ к другой security projection.

Visibility определяется context, а не language.

---

# 55. Language Does Not Change Questionnaire Facts

Пример:

```text
RU: Английский — B2
EN: English — B2
```

Значение остаётся тем же.

---

# 56. Stage INT-11 — Download Picker

Если доступны версии:

```text
Compact
Extended
Casting
```

названия и descriptions должны быть локализованы.

---

# 57. Recommended International Default

Для обычного external professional Journey рекомендуется сначала предлагать понятный default:

```text
Recommended Questionnaire
```

с указанием языка.

---

# 58. Localized PDF Requirements

PDF должен быть полностью пригоден для пересылки международной команде.

Минимально:

- Actress Name;
- Professional Identity;
- Location;
- Languages;
- relevant Skills;
- Photos;
- professional media links;
- Contacts where allowed;
- Generated Date;
- Official Profile URL.

---

# 59. Avoid Mixed-Language PDF

Нежелательно:

```text
Heading — English
Field label — Russian
Description — English
Button text — Russian
```

если для используемого content существует утверждённая локализация.

---

# 60. Acceptable Original-Language Content

Собственные названия:

- фильмов;
- школ;
- наград;
- компаний;

MAY сохраняться в оригинале, если официального перевода нет.

Это не считается localization failure.

---

# 61. QR in International PDF

QR SHOULD быть визуально и семантически понятен.

Например рядом может быть:

```text
Official profile
```

или:

```text
Video Intro
```

---

# 62. QR Target

QR кодирует точный разрешённый URL.

Он не должен:

- вести в Admin;
- содержать credentials;
- открывать private unpublished resource.

---

# 63. Hyperlink + QR Principle

Для professional URL в PDF:

```text
Clickable Hyperlink
+
QR Code where configured
```

QR не является заменой hyperlink.

---

# 64. Stage INT-12 — Public Questionnaire Builder

Если Prepared Questionnaire не подходит, Persona использует Builder.

---

# 65. International Builder Use Cases

Например:

### INT-BLD-01

Нужны только English-language facts.

### INT-BLD-02

Нужно добавить конкретные Projects.

### INT-BLD-03

Нужно включить подробные Languages.

### INT-BLD-04

Нужны relevant Skills.

### INT-BLD-05

Нужно исключить Training.

### INT-BLD-06

Нужен Casting-specific document.

---

# 66. Builder Language State

Builder MUST сохранять активную locale.

Все:

- labels;
- validation;
- templates;
- Preview;
- PDF;

должны использовать выбранный язык.

---

# 67. Template Selection

Наиболее вероятны:

```text
Standard
Extended
Casting
```

Quick остаётся доступным для shortlist.

---

# 68. Casting Template Context

Persona MAY ввести:

- Casting Name;
- Project Name;
- Role Name;

как временный document context.

Это не создаёт соответствующие Master Data entities внутри Actor Profile.

---

# 69. Builder Selection

International user MAY выбрать разрешённые:

- Projects;
- Skills;
- Languages;
- Training;
- Achievements;
- Portfolio Photos;
- Emotional Grid;
- Video;
- Audio;
- Links;
- Contacts.

---

# 70. Server Revalidation

Каждый item перед output:

```text
belongs to profile
AND
allowed in Public Builder
AND
available in current context
```

---

# 71. Public Builder Privacy

Нельзя получить private data, заменив ID через browser request.

---

# 72. Localized Preview

Preview должен отражать ту локаль, в которой будет создан PDF.

---

# 73. Stage INT-13 — Share-Ready Output

После PDF generation Persona может:

- скачать;
- переслать;
- добавить во внутреннюю casting system;
- отправить Casting Director;
- открыть Profile через link/QR.

---

# 74. Filename

Filename SHOULD быть международно безопасным и понятным.

Например концептуально:

```text
actor-name_casting-role_en.pdf
```

при наличии Casting context.

Точные правила — PDF Architecture.

---

# 75. Filename Sanitization

Название не должно позволять:

- path traversal;
- unsafe characters;
- uncontrolled filename injection.

---

# 76. Stage INT-14 — Contact

После проверки Persona может:

```text
Contact
Invite to Casting
Request Materials
```

---

# 77. Contact Localization

Contact type должен быть понятен:

```text
Phone
WhatsApp
Telegram
```

где разрешено.

---

# 78. International Phone Presentation

Телефонный номер SHOULD отображаться в международно пригодном формате, если это возможно из сохранённого verified source.

Система не должна угадывать country code из неполного номера.

---

# 79. Time Zone

Если в будущем отображается availability/contact time information, timezone должен быть однозначным.

Текущий базовый Journey не требует автоматического scheduling capability.

---

# 80. Feedback Form Language

Форма обращения должна использовать активную locale.

---

# 81. User Message Language

Если пользователь вводит сообщение на английском, оно сохраняется как Source Communication.

Система не должна автоматически заменять исходный текст переводом.

---

# 82. Optional Translation for Admin

В будущем AI MAY помочь Admin перевести входящее сообщение.

Но:

```text
Original Message
```

должно сохраняться отдельно от:

```text
AI Translation
```

---

# 83. BB Assistant International Use

BB Assistant MAY помочь подготовить:

- English cover letter;
- English Casting response;
- localized professional message;
- Project description translation/draft.

Но:

- результат является AI Draft;
- factual claims берутся из approved data;
- automatic send запрещён.

---

# 84. Main Happy Path CJM-INT-001

```text
Recommendation
 ↓
Open Profile
 ↓
Switch EN
 ↓
Same Profile Context
 ↓
Check Location
 ↓
Check Languages / CEFR
 ↓
Open Close-Up / Full Body
 ↓
Check Skills
 ↓
Play Video
 ↓
Open Standard English Questionnaire
 ↓
Download PDF
 ↓
Contact / Casting Invitation
```

---

# 85. Builder Path CJM-INT-002

```text
Profile EN
 ↓
Questionnaire
 ↓
Customize
 ↓
Casting Template
 ↓
Select Languages
 ↓
Select Projects
 ↓
Add Emotional Grid
 ↓
Preview EN
 ↓
Generate PDF EN
 ↓
Share
```

---

# 86. Questionnaire-First Path CJM-INT-003

```text
Shared English Questionnaire
 ↓
Review
 ↓
Official Profile
 ↓
Verify Current Facts
 ↓
Contact
```

---

# 87. Project Deep-Link Path CJM-INT-004

```text
Shared Project
 ↓
Switch EN
 ↓
Same Project
 ↓
Role / Media
 ↓
Profile
 ↓
Questionnaire
```

---

# 88. QR Path CJM-INT-005

```text
Printed / Shared PDF
 ↓
Scan QR
 ↓
Localized / Locale-Aware Public Profile
 ↓
Current Information
 ↓
Professional Action
```

---

# 89. Mobile Path CJM-INT-M01

```text
Mobile Deep Link
 ↓
Language Switch
 ↓
Quick Facts
 ↓
Portfolio
 ↓
Video
 ↓
Questionnaire
 ↓
Contact
```

---

# 90. Friction INT-FRC-01 — Partial Translation

Проблема:

основная страница на English, но key blocks на русском.

Impact:

- снижение доверия;
- необходимость внешнего перевода;
- abandonment.

---

# 91. Resolution INT-FRC-01

Локализация должна быть end-to-end для критического Journey.

---

# 92. Friction INT-FRC-02 — Language Switch Resets Page

Resolution:

same logical context.

---

# 93. Friction INT-FRC-03 — Ambiguous Language Levels

Проблема:

```text
English — Good
```

Resolution:

structured Level / CEFR where approved.

---

# 94. Friction INT-FRC-04 — Unclear Location

Проблема:

только city без country или локальное сокращение.

Resolution:

internationally interpretable presentation.

---

# 95. Friction INT-FRC-05 — Mixed-Language PDF

Resolution:

consistent localized PDF generation.

---

# 96. Friction INT-FRC-06 — Transliteration Used as Translation

Транслитерация не заменяет semantic localization там, где требуется перевод.

---

# 97. Friction INT-FRC-07 — Improvised Official Names

Система не должна выдумывать английское название школы, Project или award.

---

# 98. Friction INT-FRC-08 — Broken Global Media

External Video provider может быть недоступен в стране пользователя.

Если возможно, необходимо иметь fallback strategy, но без нарушения media licensing/access rules.

---

# 99. Friction INT-FRC-09 — Local Phone Format

Контакт может быть непригоден для международного использования.

---

# 100. Friction INT-FRC-10 — Builder Locale Loss

Пользователь начал EN Builder, а Preview/PDF внезапно возвращается на RU.

Это hard failure.

---

# 101. Trust Signals

Особенно важны:

```text
Consistent localization
Clear country/location
CEFR
Official project names
Accurate professional translation
Current real photos
Localized PDF
Official Profile URL
Generated Date
Working international links
```

---

# 102. Trust Through Terminology

Используются стабильные professional terms:

- Actor;
- Role;
- Project;
- Showreel;
- Full Body;
- Close-Up;
- Training;
- Language Level.

---

# 103. Trust Through Consistent Identity

Name spelling/transliteration SHOULD быть единообразной в:

- Profile;
- PDF;
- metadata;
- public links;

согласно утверждённым profile fields.

---

# 104. Name Transliteration

Система не должна автоматически генерировать новую Latin spelling имени, если approved international spelling уже сохранена.

---

# 105. Information Priority — P0

```text
Name
Professional Identity
Location / Base
Primary Close-Up
Full Body
Languages
CEFR
Key Skills
Video
Localized Questionnaire
Contact
```

---

# 106. Information Priority — P1

```text
Selected Projects
Roles
Emotional Grid
Training
Showreel
```

---

# 107. Information Priority — P2

```text
Achievements
Additional Media
Full Project Archive
Full Emotional Portfolio
```

---

# 108. Information Priority — P3

```text
Blog
Extended narrative
Historical archive
```

---

# 109. International Quick View

Casting Quick View SHOULD иметь полноценную localized version.

Он не должен содержать RU-only hardcoded labels.

---

# 110. International Search

Search query MAY быть введён на активном языке.

Поисковая архитектура в дальнейшем должна определить mapping localized terms к canonical structured data.

Пример:

```text
Driving
→ canonical Skill: driving
```

---

# 111. Search Must Not Translate Facts by Guess

Поиск может сопоставлять labels/synonyms, но не должен создавать новый Skill из семантического предположения.

---

# 112. Date Localization

Display format MAY различаться:

```text
8 August 2026
```

против локального русского формата.

Source Date остаётся той же.

---

# 113. Units

Если профессиональные параметры используют единицы измерения, архитектура SHOULD разделять:

```text
Source Value
+
Display Unit
```

чтобы в будущем поддерживать международно понятное представление.

Конкретные параметры определяются Domain Model.

---

# 114. No Silent Conversion Errors

Конвертация единиц не должна менять исходный Master Data.

---

# 115. International SEO

Localized public pages SHOULD иметь согласованные:

- title;
- description;
- canonical/hreflang model where selected architecture supports it.

Подробности — SEO/NFR documentation.

---

# 116. Deep Links

Localized deep links должны быть shareable.

Получатель не должен обязательно знать предыдущую session, чтобы понять страницу.

---

# 117. QR Locale Strategy

QR MAY вести:

- на конкретную localized URL;
- либо на locale-aware official URL.

Точная политика определяется QR/PDF architecture.

Но результат всегда должен быть публичным и стабильным.

---

# 118. Marketing Attribution

International source может включать:

```text
search
referral
casting_link
social
campaign
shared_questionnaire
```

---

# 119. Analytics Events

Рекомендуемые logical events:

```text
international_profile_viewed
language_switched
language_profile_continued
language_skill_opened
localized_video_started
localized_questionnaire_viewed
localized_questionnaire_downloaded
public_builder_localized_opened
public_builder_localized_pdf_generated
international_contact_started
international_casting_inquiry_submitted
```

---

# 120. KPI — Language Switch Continuation

Показывает, продолжает ли пользователь Journey после смены языка.

Если после switch высокий exit rate, это может указывать на localization failure.

---

# 121. KPI — Localized Questionnaire Conversion

```text
Localized Profile
→ Localized Questionnaire
→ PDF
```

---

# 122. KPI — International Contact Rate

Доля international sessions, переходящих к professional action.

---

# 123. KPI — Mixed-Language Error Rate

В automated UI QA может контролироваться отсутствие untranslated interface keys в critical localized Journey.

---

# 124. KPI — Builder Locale Consistency

Все steps:

```text
Template
Selection
Preview
PDF
```

должны использовать одну selected locale, если пользователь её не меняет.

---

# 125. Virtual Operator Support

Virtual Operator SHOULD выявлять:

- missing required translation;
- broken localized link;
- missing international name spelling where required;
- untranslated Questionnaire block;
- incomplete Language Level;
- missing Location country;
- stale localized PDF;
- QR target failure.

---

# 126. Virtual Operator Translation Boundary

Virtual Operator MAY предложить translation draft.

Но professional translated content, где смысл критичен, требует human review.

---

# 127. BB Assistant and Translation Boundary

Нужно различать:

```text
Translation
```

и:

```text
Professional Rewriting
```

BB Assistant MAY делать оба типа Draft, но они имеют разный intent и должны сохранять original source.

---

# 128. Relevant 6-3-5 Insights

Основные:

```text
INS-INT-01 Global Language State
INS-INT-02 Fast Language Switch
INS-INT-03 Share-Ready Localized PDF
```

Дополнительные:

```text
INS-CD-01 Casting Quick View
INS-CD-02 Search Inside Profile
INS-CA-01 Structured Quick Facts
INS-CA-03 Compact PDF
INS-CP-01 Mobile-First Casting
```

---

# 129. Relevant Business Rules

Критичные:

```text
BR-I18N-001+
BR-QST-*
BR-PDF-*
BR-PQB-*
BR-CON-*
BR-LNK-*
BR-CJM-*
BR-SEC-*
```

При последующей traceability используются точные утверждённые IDs из Business Rules.

---

# 130. Relevant Product Principles

Особенно:

```text
PP-001 Single Source of Truth
PP-002 One Input, Multiple Uses
PP-021 Privacy by Projection
PP-022 Visibility Is Contextual
PP-047 Professional Documents Must Be Share-Ready
PP-048 QR Complements Hyperlink
PP-049 Public URLs Must Be Safe
PP-053 Five-Minute Professional Journey
PP-088 Multilingual Means End-to-End
PP-089 Translation Does Not Create Facts
```

---

# 131. Future User Flows

Из Journey должны быть созданы минимум:

```text
UF-PUB-INT-001 Open International Profile
UF-PUB-INT-002 Switch Language
UF-PUB-INT-003 Preserve Page Context
UF-PUB-INT-004 Review Location
UF-PUB-INT-005 Review Languages / CEFR
UF-PUB-INT-006 Review Portfolio
UF-PUB-INT-007 Play Localized/Tagged Video
UF-PUB-INT-008 Review Project / Role
UF-QST-INT-001 Open Localized Questionnaire
UF-QST-INT-002 Download Localized PDF
UF-PQB-INT-001 Open Builder in Selected Language
UF-PQB-INT-002 Build International Casting Questionnaire
UF-PQB-INT-003 Generate Localized PDF
UF-FDB-INT-001 Submit International Inquiry
```

---

# 132. Functional Requirement Areas

```text
FR-I18N
FR-PROFILE
FR-LOCATION
FR-LANGUAGES
FR-SKILLS
FR-PORTFOLIO
FR-VIDEO
FR-PROJECTS
FR-QUESTIONNAIRE
FR-PQB
FR-PDF
FR-QR
FR-CONTACT
FR-FEEDBACK
FR-SEO
FR-ANALYTICS
```

---

# 133. E2E-CJM-INT-001 — Main International Journey

```text
Given
the public profile supports English

When
International Casting Specialist opens the profile

Then
they can:
- switch to English
- remain on the same logical page
- understand Location
- view Languages and CEFR
- view Close-Up and Full Body
- inspect Skills
- play Video
- open English Questionnaire
- download English PDF
- start Professional Contact
```

---

# 134. E2E-CJM-INT-002 — Language Persistence

```text
Given
user selected EN

When
they navigate:
Profile
→ Project
→ Questionnaire
→ Public Builder

Then
the selected locale remains EN
```

unless explicitly changed.

---

# 135. E2E-CJM-INT-003 — Builder Language

Generated PDF MUST use selected Builder language and must not silently revert to default locale.

---

# 136. E2E-CJM-INT-004 — Visibility Equality

A private Contact hidden in RU context MUST remain hidden in EN context.

Language switch cannot change authorization.

---

# 137. E2E-CJM-INT-005 — CEFR Consistency

Language Level MUST match between:

- Profile;
- Questionnaire;
- Public Builder;
- generated PDF;

except immutable historical snapshot cases.

---

# 138. E2E-CJM-INT-006 — QR

For every enabled QR in generated international PDF:

1. decode QR;
2. obtain URL;
3. compare to canonical configured target;
4. ensure URL is public/safe;
5. ensure associated hyperlink matches target where required.

---

# 139. E2E-CJM-INT-007 — Deep Link Language Switch

Direct Project or Questionnaire link MUST preserve logical entity when switching language.

---

# 140. E2E-CJM-INT-008 — Missing Translation

Critical UI must use documented fallback behaviour and MUST NOT expose raw internal localization keys.

---

# 141. E2E-CJM-INT-009 — Original Professional Name

Approved Latin name spelling must remain consistent in English Profile and PDF.

---

# 142. E2E-CJM-INT-010 — Broken Video

Video failure MUST NOT prevent:

- localized Profile use;
- Questionnaire;
- PDF;
- Contact.

---

# 143. Journey Score Target

| Критерий | Target |
|---|---:|
| Language Recognition | 2 |
| International Findability | 2 |
| Evidence | 2 |
| Localized Questionnaire | 2 |
| Share / Contact | 2 |

Target:

```text
10/10
```

Minimum acceptable:

```text
9/10
```

---

# 144. Hard Failure Conditions

Journey требует redesign, если:

1. English user обязан понимать русский для критического action.
2. Language Switch возвращает на Home без необходимости.
3. Location невозможно интерпретировать международно.
4. Language Level неоднозначен при наличии structured data.
5. Profile и PDF показывают разные current Language facts.
6. Builder теряет locale.
7. PDF смешивает языки без оправданной причины.
8. official names выдумываются переводом.
9. switch языка меняет visibility.
10. international PDF невозможно безопасно переслать.
11. QR ведёт на private/admin URL.
12. обязательная регистрация блокирует базовый Journey.
13. direct localized deep link становится dead end.

---

# 145. International Journey Is Not English-Only

Архитектурный принцип должен быть:

```text
Locale-Aware Product
```

а не:

```text
Russian Site
+
Special English Pages
```

Даже если фактически на первом этапе реализованы только RU и EN.

---

# 146. Localization Completeness Levels

Для будущего контроля можно различать:

### L0 — Unsupported

Locale отсутствует.

### L1 — UI Localized

Интерфейс переведён, но business content неполный.

### L2 — Professional Journey Localized

Critical professional content доступен.

### L3 — Fully Localized Professional Package

Profile + Questionnaire + Builder + PDF + CTA полностью пригодны.

Для поддерживаемой production locale целевой уровень:

```text
L3
```

---

# 147. Professional Meaning Before Literal Translation

Цель локализации:

> не буквально перевести каждую строку, а сохранить однозначный профессиональный смысл.

---

# 148. No Translation Hallucinations

Если исходное значение:

```text
Не указано
```

локализованная версия:

```text
Not specified
```

а не AI-generated предположение.

---

# 149. Share-Ready Principle

После передачи PDF третьему лицу новый получатель должен без предыдущего контекста понять:

- имя актрисы;
- профессию;
- основные данные;
- язык документа;
- дату формирования;
- официальный source;
- доступный способ дальнейшего действия.

---

# 150. Cross-Border Privacy Principle

Международность Journey не означает расширение visibility.

Передача данных внешним сервисам, AI и analytics регулируется отдельными Security/Privacy документами.

---

# 151. No Geography-Based Hidden Fact Changes

Location пользователя MAY влиять на:

- display format;
- timezone presentation;
- language suggestion.

Не должна автоматически менять professional facts или content permissions без отдельно утверждённого rule.

---

# 152. Time Zone Presentation Principle

При наличии future casting deadlines необходимо показывать timezone явно, если ambiguity materially affects professional action.

Подробно это будет определено Castings/UX documentation.

---

# 153. Questionnaire Date Principle

Generated Date отображается в локализованном формате, но должна представлять один и тот же timestamp.

---

# 154. International Contact Handoff

Основная модель:

```text
Localized Professional Profile
 ↓
Localized Questionnaire / PDF
 ↓
Professional Inquiry
 ↓
Feedback
 ↓
Admin Review
 ↓
Casting
```

Language не меняет workflow semantics.

---

# 155. Original Message Preservation

Если international user отправляет:

```text
We would like to invite...
```

оригинальный текст должен сохраняться.

Любой внутренний перевод является отдельным derived/AI-assisted representation.

---

# 156. Analytics Segmentation

Analytics MAY агрегировать:

```text
locale
country-level technical context where legitimately available
entry source
questionnaire language
```

только в пределах Privacy/Data Minimization rules.

---

# 157. International Journey and SEO

Localized search result должен приводить пользователя на локализованный релевантный Entry Point, а не обязательно на default-language Home.

---

# 158. International Journey and Public Builder

Public Builder является особенно важным для международного workflow, поскольку разные production teams требуют разные package structures.

---

# 159. International Journey and QR

QR связывает static/transferred document с текущим digital source.

Это особенно важно при пересылке PDF между:

- Casting Assistant;
- Casting Director;
- Producer;
- Director.

---

# 160. Mental Model

```text
UNDERSTAND
 ↓
VERIFY
 ↓
LOCALIZE
 ↓
PACKAGE
 ↓
SHARE / ACT
```

---

# 161. Product Response

```text
UNDERSTAND
→ Global Language State

VERIFY
→ Structured Location / Languages / Skills / Evidence

LOCALIZE
→ End-to-End Professional Localization

PACKAGE
→ Prepared Questionnaire / Public Builder

SHARE / ACT
→ Localized PDF / Contact / Casting CTA
```

---

# 162. Definition of Journey Compliance

Public UX соответствует `CJM-INT-001`, если International Casting Specialist:

1. может определить или быстро выбрать подходящий язык;
2. после switch остаётся в том же logical context;
3. понимает Location/Base;
4. видит структурированные Languages и Levels;
5. получает профессиональные Portfolio/Video materials;
6. понимает Project/Role terminology;
7. получает полноценно localized Questionnaire;
8. может создать localized custom Questionnaire;
9. получает share-ready PDF;
10. clickable URLs и QR ведут на safe public resources;
11. может выполнить Professional CTA без регистрации;
12. не получает Private Data;
13. перевод не создаёт новых профессиональных фактов;
14. весь типовой Journey реалистично выполняется в пределах пяти минут.

---

# 163. Итоговая схема Journey

```text
INTERNATIONAL CASTING SPECIALIST

Search / Referral / Shared Link / QR
                  ↓
             PUBLIC PROFILE
                  ↓
           LANGUAGE STATE
                  ↓
       SAME LOGICAL CONTEXT
                  ↓
     LOCATION / BASE / LANGUAGES
                  ↓
      CLOSE-UP / FULL BODY
                  ↓
          SKILLS / VIDEO
                  ↓
       PROJECTS / EXPERIENCE
                  ↓
      LOCALIZED QUESTIONNAIRE
                  ↓
          Is sufficient?
           /        \
         YES         NO
          │           │
      Download    Public Builder
          │           │
          └─────┬─────┘
                ↓
       LOCALIZED PDF
                ↓
        SHARE / CONTACT
                ↓
   CASTING / MATERIAL REQUEST
```

---

# 164. Финальный принцип

> **International Casting Journey должен превращать единый Actor Profile в полноценный международный профессиональный пакет без создания отдельного «английского сайта». Язык меняет представление, но не факты, права доступа или бизнес-смысл. Пользователь должен получить понятные location, languages, experience, evidence, Questionnaire и PDF и перейти к профессиональному действию без необходимости самостоятельно переводить или интерпретировать профиль.**