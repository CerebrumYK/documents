# CONVERSION FUNNEL

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Профессиональная воронка конверсии сайта актрисы

**Целевой файл:** `docs/customer-journey/conversion-funnel.md`  
**Документ:** DOC-029  
**Статус:** ✅ Completed  
**Тип:** Customer Journey / Conversion Architecture

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
- `docs/customer-journey/international-casting.md`
- `docs/customer-journey/virtual-operator.md`

---

# 1. Назначение документа

Настоящий документ определяет единую Conversion Funnel Model целевого продукта.

Воронка предназначена для ответа на вопросы:

1. Какие действия пользователя действительно имеют профессиональную ценность.
2. Как связать посещение сайта с профессиональным обращением.
3. Как отличать обычное взаимодействие с контентом от коммерчески значимого intent.
4. Как Professional Inquiry превращается в Casting.
5. Как Casting переходит в Self-Tape, Audition, Callback, Offer и Booked.
6. Какие этапы необходимо измерять.
7. Какие этапы не должны считаться конверсией автоматически.
8. Как учитывать разные Entry Sources.
9. Как учитывать короткие и нелинейные пользовательские пути.
10. Как использовать аналитику без нарушения Privacy.
11. Как передавать результаты аналитики Virtual Operator.
12. Как отличать product optimization от автоматического изменения контента.

---

# 2. Основной принцип

Главная воронка продукта:

```text id="mp2w0d"
REACH
 ↓
RELEVANT PROFESSIONAL VISIT
 ↓
PROFESSIONAL EVALUATION
 ↓
EVIDENCE
 ↓
QUESTIONNAIRE / PROFESSIONAL PACKAGE
 ↓
PROFESSIONAL INTENT
 ↓
PROFESSIONAL INQUIRY
 ↓
CASTING / OPPORTUNITY
 ↓
SELF-TAPE / AUDITION
 ↓
CALLBACK
 ↓
OFFER
 ↓
BOOKED
```

---

# 3. Конечная цель воронки

Конечным бизнес-результатом считается:

```text id="975xbh"
BOOKED
```

то есть подтверждённое получение роли / участия в профессиональном проекте.

---

# 4. Воронка не заканчивается PDF

Действия:

- просмотр Portfolio;
- просмотр Video;
- открытие Questionnaire;
- скачивание PDF;

являются промежуточными сигналами.

Они не являются конечным бизнес-результатом.

---

# 5. Воронка не заканчивается Contact

Professional Contact является сильной конверсией, но также остаётся промежуточным этапом.

Цель продукта:

```text id="d9uk3p"
Interest
→ Professional Opportunity
→ Professional Outcome
```

---

# 6. Главные классы конверсии

В системе различаются:

```text id="b4ufhc"
Exposure
Engagement
Evaluation
Intent
Inquiry
Opportunity
Outcome
```

---

# 7. CF-STAGE-01 — Exposure

Пользователь столкнулся с профилем или ссылкой.

Примеры:

- search result;
- social link;
- recommendation;
- QR;
- campaign;
- shared Questionnaire.

Exposure сам по себе не означает Visit.

---

# 8. CF-STAGE-02 — Relevant Professional Visit

Посетитель открыл Public Site или professional deep link.

Важно отличать:

```text id="z9j28o"
Any Visit
```

от:

```text id="zj6d1n"
Relevant Professional Visit
```

---

# 9. Relevant Visit

На продуктовом уровне Relevant Visit — посещение, которое содержит признаки реального взаимодействия с профессиональным содержимым.

Примеры:

- Quick View;
- Portfolio;
- Video;
- Questionnaire;
- professional Search;
- Casting CTA.

Точная analytics classification определяется позднее.

---

# 10. Неавторизованный посетитель не классифицируется автоматически как Casting Specialist

Продукт не должен скрыто утверждать:

```text id="kql9zc"
этот visitor = Casting Director
```

только на основании поведения.

Persona используется для UX-моделирования, а не для необоснованного profiling.

---

# 11. CF-STAGE-03 — Professional Evaluation

Пользователь начинает оценку профессионального соответствия.

Сигналы могут включать:

- Quick View;
- structured facts;
- Portfolio category;
- Skills;
- Languages;
- Search.

---

# 12. Evaluation не означает Interest

Пользователь может проверить профиль и решить, что он не подходит.

Поэтому:

```text id="e3h76p"
Evaluation
≠
Qualified Interest
```

---

# 13. CF-STAGE-04 — Evidence Engagement

Пользователь изучает доказательства профессиональной пригодности.

Примеры:

```text id="102va5"
Video Intro
Showreel
Emotional Grid
Project
Role
Role Media
```

---

# 14. Evidence Depth

Evidence engagement может иметь разную глубину:

```text id="0gm453"
E1
single evidence interaction

E2
multiple evidence categories

E3
deep project / role exploration
```

Такая классификация MAY применяться аналитически, но не является самостоятельным business status.

---

# 15. CF-STAGE-05 — Questionnaire Intent

Пользователь переходит к структурированному professional package.

Примеры:

- Questionnaire View;
- Download Picker;
- Public Builder.

Это более сильный сигнал intent, чем обычный content view.

---

# 16. CF-STAGE-06 — Professional Package

Пользователь получает:

```text id="0a67ph"
Prepared PDF
```

или:

```text id="pfeaja"
Custom PDF
```

---

# 17. Значение PDF

PDF означает, что пользователь хочет:

- сохранить информацию;
- передать её;
- использовать во внутреннем workflow;
- продолжить профессиональную оценку.

Но PDF Download ещё не означает Casting Invitation.

---

# 18. CF-STAGE-07 — Professional Intent

Сильные intent actions:

```text id="3de1xy"
Invite to Casting started
Offer Role started
Request Materials started
Collaboration started
Professional Contact started
```

---

# 19. Start ≠ Completion

Необходимо различать:

```text id="azej6n"
CTA Started
```

и:

```text id="8av2xq"
Professional Inquiry Submitted
```

---

# 20. CF-STAGE-08 — Professional Inquiry

Professional Inquiry существует только после успешного сохранения Feedback.

Пример:

```text id="piwf6d"
Casting Invitation submitted
→ Feedback persisted
```

---

# 21. Notification не определяет Inquiry

Правильная модель:

```text id="rw14j9"
Feedback Saved
= Professional Inquiry Exists
```

Неправильно:

```text id="6jf83p"
WhatsApp delivered
= Inquiry Exists
```

---

# 22. Primary Conversion

Основной Public Conversion:

```text id="ocbvb3"
Qualified Professional Inquiry
```

Например:

- Casting Invitation;
- Role Offer;
- Request Materials;
- professional Collaboration inquiry.

---

# 23. CF-STAGE-09 — Qualification

Admin рассматривает Professional Inquiry.

Результат:

```text id="h1j0kw"
Qualified
```

или:

```text id="ppg28f"
Not Qualified
```

или другой предусмотренный workflow state.

---

# 24. Qualification является Human Decision

Analytics или AI не должны автоматически назначать:

```text id="lyno8l"
Qualified
```

только по поведению посетителя.

---

# 25. CF-STAGE-10 — Opportunity / Casting

После Human Review профессиональное обращение MAY превратиться в:

```text id="mdx7a5"
Casting
```

или другую Opportunity согласно Domain Model.

---

# 26. Feedback ≠ Casting

Критическая граница:

```text id="woq9ag"
Feedback
→ Human Review
→ Casting
```

а не:

```text id="r5bnyw"
Feedback
= Casting
```

---

# 27. Opportunity Pipeline

Основные стадии:

```text id="565v6v"
New
 ↓
Qualified
 ↓
Materials Requested
 ↓
Self-Tape Requested
 ↓
Audition
 ↓
Callback
 ↓
Offer
 ↓
Booked
```

Дополнительные конечные:

```text id="mxm4tf"
Closed — Not Selected
Withdrawn
```

---

# 28. CF-STAGE-11 — Materials Requested

Production team запросила:

- дополнительные фото;
- видео;
- voice sample;
- specific role evidence;
- иной professional material.

Это сильный признак продолжения интереса.

---

# 29. CF-STAGE-12 — Self-Tape Requested

Запрос Self-Tape означает переход к активному кастинговому процессу.

Необходимо различать:

```text id="0dn7w3"
Self-Tape Requested
```

и:

```text id="1lumta"
Self-Tape Submitted
```

если в дальнейшем обе стадии поддерживаются Domain Model.

---

# 30. CF-STAGE-13 — Audition

Актриса приглашена на формальные пробы / audition.

---

# 31. CF-STAGE-14 — Callback

Повторный вызов после предыдущего этапа.

Это значимый downstream business conversion.

---

# 32. CF-STAGE-15 — Offer

Получено профессиональное предложение участия.

---

# 33. Offer ≠ Booked

Offer ещё может:

- обсуждаться;
- быть отклонён;
- измениться;
- быть отозван.

Поэтому:

```text id="fsxrbl"
Offer
≠
Booked
```

---

# 34. CF-STAGE-16 — Booked

Роль / участие подтверждены.

Это главный конечный положительный результат.

---

# 35. Negative Outcome Is Valuable Data

```text id="hl32zh"
Closed — Not Selected
```

также является важным business outcome.

Он позволяет:

- анализировать воронку;
- понимать conversion rates;
- не оставлять Opportunities бесконечно открытыми.

---

# 36. Withdrawn

`Withdrawn` означает прекращение участия по инициативе актрисы/представителя либо по согласованной внутренней причине.

Не следует смешивать с:

```text id="05kxx0"
Not Selected
```

---

# 37. Основная Funnel Diagram

```text id="cj442e"
VISITOR
  │
  ▼
RELEVANT PROFESSIONAL VISIT
  │
  ▼
PROFESSIONAL EVALUATION
  │
  ├───────────────┐
  ▼               │
EVIDENCE           │
  │               │
  ▼               │
QUESTIONNAIRE      │
  │               │
  ├── Prepared PDF│
  │               │
  └── Custom PDF  │
          │        │
          ▼        │
  PROFESSIONAL INTENT
          │
          ▼
       FEEDBACK
          │
          ▼
      QUALIFIED?
       /      \
     NO        YES
     │          │
   CLOSED     CASTING
                │
                ▼
      MATERIALS / SELF-TAPE
                │
                ▼
             AUDITION
                │
                ▼
             CALLBACK
                │
                ▼
               OFFER
                │
                ▼
              BOOKED
```

---

# 38. Воронка является нелинейной

Пользователь не обязан проходить каждый этап.

Корректные пути:

```text id="o75iq9"
Profile
→ Casting Invitation
```

```text id="h6c4lm"
Shared Questionnaire
→ PDF
→ Contact
```

```text id="ygngpk"
Project
→ Request Materials
```

```text id="wsjbuv"
Emotional Portfolio
→ Casting Invitation
```

---

# 39. Funnel Stages Are Analytical, Not Navigation Requirements

UX MUST NOT заставлять пользователя проходить:

```text id="tm6qyg"
Home
→ Portfolio
→ Video
→ Project
→ Questionnaire
```

только потому, что аналитическая воронка содержит эти этапы.

---

# 40. Shortest Valid Conversion

Один из самых коротких корректных сценариев:

```text id="le9jzv"
Direct Profile
 ↓
Quick Qualification
 ↓
Invite to Casting
```

---

# 41. Longest Professional Exploration

Также допустимо:

```text id="i0u8in"
Search
 ↓
Profile
 ↓
Portfolio
 ↓
Video
 ↓
Emotional
 ↓
Projects
 ↓
Training
 ↓
Extended Questionnaire
 ↓
Builder
 ↓
PDF
 ↓
Request Materials
```

---

# 42. Funnel Compression

Хороший UX может уменьшать число intermediate steps без снижения качества решения.

Например:

```text id="tijgbo"
Casting Quick View
```

компрессирует:

- identity;
- facts;
- visual evidence;
- navigation.

---

# 43. Funnel Acceleration

Capabilities, ускоряющие движение:

```text id="444110"
Casting Quick View
Public Search
Portfolio Filters
Video Intro
Emotional Grid
Primary Questionnaire
Public Builder
Contextual CTA
```

---

# 44. Funnel Friction

Основные причины потери пользователя:

```text id="errpqk"
unclear identity
slow load
missing Full Body
hidden Video
broken links
no localized version
too long PDF
no suitable questionnaire
hidden Contact
poor mobile UX
registration wall
```

---

# 45. Funnel Leakage

`Leakage` означает прекращение Journey между этапами.

Leakage не всегда является проблемой.

Если кандидат не подходит требованиям, выход после Qualification является нормальным результатом.

---

# 46. Good Leakage vs Bad Leakage

## Good Leakage

Пользователь получил ясный ответ и закончил screening.

## Bad Leakage

Пользователь покинул сайт потому что:

- не нашёл Video;
- не понял язык;
- PDF сломался;
- форма неудобна;
- страница медленная.

---

# 47. Аналитика должна различать причину там, где это возможно без intrusive tracking

Нельзя автоматически считать каждый exit UX failure.

---

# 48. Conversion Hierarchy

Устанавливается следующая иерархия сигналов:

```text id="zkrsjg"
LEVEL 0 — Exposure

LEVEL 1 — Professional Visit

LEVEL 2 — Evaluation

LEVEL 3 — Evidence

LEVEL 4 — Questionnaire / Package

LEVEL 5 — Professional Intent

LEVEL 6 — Inquiry

LEVEL 7 — Qualified Opportunity

LEVEL 8 — Casting Process

LEVEL 9 — Offer

LEVEL 10 — Booked
```

---

# 49. Primary Public Conversions

Ключевые:

```text id="gkdiq7"
Casting Invitation Submitted
Role Offer Submitted
Qualified Professional Inquiry Submitted
```

---

# 50. Secondary Public Conversions

```text id="tawpbo"
Questionnaire Viewed
Prepared PDF Downloaded
Custom PDF Generated
Request Materials Submitted
Video Started
Emotional Grid Viewed
```

---

# 51. Tertiary Engagement Signals

Например:

- Portfolio Filter Used;
- Project Opened;
- Search Used;
- Training Opened.

Они помогают понять Journey, но сами по себе не должны считаться главной продуктовой конверсией.

---

# 52. Vanity Metrics

Следующие показатели не должны использоваться как главный показатель успеха:

```text id="2h94xd"
raw page views
raw session duration
number of photos opened
scroll depth alone
```

---

# 53. Business Metrics Take Priority

Главная последовательность:

```text id="27fo8n"
Professional Inquiry
 ↓
Casting
 ↓
Audition
 ↓
Callback
 ↓
Offer
 ↓
Booked
```

---

# 54. Conversion Source / Attribution

Для профессионального действия SHOULD быть возможно определить исходный acquisition context, если он доступен.

Минимальные source classes:

```text id="kmu6iw"
direct
search
social
referral
campaign
shared_profile
shared_questionnaire
shared_project
casting_specific_link
qr
other
```

---

# 55. Source Is Not Persona

Пример:

```text id="9qyco2"
source = Instagram
```

не означает:

```text id="t9rl7d"
persona = Commercial Producer
```

---

# 56. Landing Page

Attribution SHOULD сохранять исходную Landing Page:

- Home;
- Portfolio;
- Emotional Portfolio;
- Project;
- Questionnaire;
- Casting-specific page.

---

# 57. Referrer

Referrer MAY использоваться, если доступен и соответствует Privacy policy.

---

# 58. UTM

Campaign parameters MAY использоваться:

```text id="mlr0yd"
utm_source
utm_medium
utm_campaign
```

или согласованная эквивалентная модель.

---

# 59. Attribution Persistence

Attribution SHOULD сохраняться достаточно долго, чтобы связать Visit с Professional Inquiry в рамках разумной пользовательской session/journey.

Точная retention/model определяется Analytics Architecture.

---

# 60. Attribution Must Be Explainable

Администратор SHOULD видеть понятный source:

```text id="qbjoq8"
Instagram campaign
```

а не только opaque internal tracking ID.

---

# 61. Attribution Privacy

Не требуется создавать persistent cross-site profile посетителя.

---

# 62. Data Minimization

Для оценки funnel достаточно минимальных event/attribution данных.

Нельзя собирать PII «на всякий случай».

---

# 63. Anonymous Analytics

До Professional Inquiry visitor обычно SHOULD анализироваться анонимно/псевдонимно согласно выбранной Privacy architecture.

---

# 64. Identity Boundary

После Feedback данные отправителя существуют как часть конкретного Professional Inquiry.

Это не означает автоматического объединения всей предыдущей browsing history с персоной без утверждённой policy.

---

# 65. Public Builder Funnel

Отдельная подворонка:

```text id="dwz7vf"
Builder Opened
 ↓
Template Selected
 ↓
Configuration Modified
 ↓
Preview Opened
 ↓
PDF Generated
 ↓
Professional CTA
```

---

# 66. Public Builder Success

Основной результат Builder:

```text id="w9999g"
Useful Professional Package Generated
```

не максимальное число включённых блоков.

---

# 67. Builder Abandonment

Если пользователь открыл Builder и вышел, это не всегда проблема.

Возможно:

- Prepared Questionnaire оказалась достаточной;
- пользователь решил не продолжать.

Контекст необходимо оценивать через другие события.

---

# 68. Builder Friction Indicator

Высокая доля:

```text id="s41y28"
Builder Open
→ no Template Selection
```

может указывать на:

- сложный первый экран;
- непонятную ценность;
- performance issue.

---

# 69. Template Effectiveness

Для каждого Template MAY анализироваться:

- selection rate;
- generation rate;
- commonly removed blocks;
- commonly added blocks;
- subsequent professional action.

---

# 70. No Automatic Template Optimization

Даже если analytics показывает явную тенденцию, Template не меняется автоматически.

Правильно:

```text id="pe219i"
Analytics
→ Recommendation
→ Human Product Decision
```

---

# 71. Prepared Questionnaire Funnel

```text id="o4zck1"
Questionnaire Page
 ↓
Prepared Version Opened
 ↓
Download Picker
 ↓
PDF Download
 ↓
Contact / Casting CTA
```

---

# 72. Prepared vs Public Builder Funnel

Не следует считать Builder более успешным только потому, что он сложнее.

Если Prepared Questionnaire сразу удовлетворяет пользователя, это более эффективный Journey.

---

# 73. Questionnaire Success Principle

Идеальная модель:

```text id="o6px2q"
Prepared version solves most cases
+
Builder solves specialized cases
```

---

# 74. Video Funnel

```text id="awmcbd"
Video CTA visible
 ↓
Video Started
 ↓
Meaningful Engagement
 ↓
Next Professional Action
```

Точная модель meaningful engagement определяется analytics module.

---

# 75. Video Start Is More Useful Than Video Impression

Автоматическое появление Video card на экране не должно интерпретироваться как активный interest.

---

# 76. Emotional Funnel

```text id="793u5i"
Emotional Grid Viewed
 ↓
Full Emotional Portfolio
 ↓
Questionnaire / CTA
```

---

# 77. Portfolio Funnel

```text id="j8jj6m"
Portfolio Opened
 ↓
Professional Category Selected
 ↓
Photo Opened
 ↓
Video / Questionnaire / CTA
```

---

# 78. Search Funnel

```text id="fdxmnw"
Search Started
 ↓
Result Found
 ↓
Source Entity Opened
 ↓
Questionnaire / Professional Action
```

---

# 79. Search Zero Results

Zero Result является важным signal, но не Conversion Failure автоматически.

Он может означать:

- реальное отсутствие опубликованного факта;
- плохую terminology mapping;
- missing structured data.

---

# 80. Virtual Operator Use of Zero Results

Operator MAY агрегированно сообщить:

```text id="u59p1w"
Professional visitors repeatedly search for a term not represented in structured Profile data.
```

Human review required.

---

# 81. Contact Funnel

```text id="e9u2qu"
CTA Visible
 ↓
CTA Started
 ↓
Form Started
 ↓
Validation
 ↓
Feedback Persisted
 ↓
Success
```

---

# 82. Contact Form Drop-Off

Form abandonment MAY быть UX signal.

Особенно проверяются:

- excessive fields;
- mobile input issues;
- attachment failures;
- unclear consent;
- validation loops.

---

# 83. Feedback Funnel

После persistence:

```text id="25sv7q"
New
 ↓
Reviewed
 ↓
Qualified?
```

---

# 84. Qualified Feedback Funnel

```text id="pg3m5i"
Feedback
 ↓
Qualified
 ↓
Casting Created
```

---

# 85. Casting Funnel

```text id="zsok03"
Casting
 ↓
Requirements
 ↓
Profile Match
 ↓
Human Decision
 ↓
Materials / Questionnaire
 ↓
Self-Tape / Audition
```

---

# 86. Casting AI Is Not a Conversion Stage

AI Analysis:

```text id="ebicig"
supports workflow
```

но не представляет business outcome.

---

# 87. `AI analysis complete` ≠ `Qualified`

Критическое правило.

---

# 88. Opportunity Stage Changes Require Business Evidence

Этап:

```text id="ox1lpz"
Callback
```

не должен назначаться на основании:

- page view;
- email open;
- AI guess.

Нужен подтверждённый professional event.

---

# 89. Offer Funnel

```text id="bklmjf"
Audition / Callback
 ↓
Offer
 ↓
Accepted / Booked
```

Если future Domain Model различит Accepted и Booked, это будет детализировано соответствующим модулем.

---

# 90. Booked to Project Loop

После Booked:

```text id="x23shu"
Booked
 ↓
Project / Role created when appropriate
 ↓
Professional materials
 ↓
Profile enrichment
 ↓
Future funnel strengthened
```

---

# 91. Closed Opportunity Learning

Closed — Not Selected может сохранять:

- stage reached;
- source;
- reason category where known;
- relevant dates.

Но нельзя принудительно требовать unavailable reason.

---

# 92. Funnel by Persona

Разные Persona используют разные сокращённые пути.

---

# 93. Casting Director Funnel

```text id="0hqozz"
Quick View
→ Evidence
→ Questionnaire
→ Casting Invitation
```

---

# 94. Casting Assistant Funnel

```text id="bp6qdp"
Search
→ Verify
→ Compact PDF
→ Handoff
```

Его локальная конверсия может быть не Public Inquiry, а successful handoff.

---

# 95. Commercial Producer Funnel

```text id="1ujz80"
Visual
→ Emotional
→ Video
→ Contextual CTA
```

---

# 96. Director Funnel

```text id="lehdnu"
Video
→ Emotional
→ Project / Role
→ Request Materials / Casting
```

---

# 97. International Funnel

```text id="05tw6h"
Locale
→ Structured Facts
→ Evidence
→ Localized PDF
→ Contact
```

---

# 98. Virtual Operator Funnel

Внутренняя операционная модель:

```text id="tymqef"
Exception Detected
 ↓
Actionable Suggestion
 ↓
Human Resolution
 ↓
Journey Health Improved
```

Она не смешивается с external conversion analytics.

---

# 99. Funnel and Customer Journey

Customer Journey отвечает:

> что делает конкретная Persona?

Conversion Funnel отвечает:

> какое профессиональное значение имеет действие и куда оно ведёт дальше?

---

# 100. Funnel and Opportunity Pipeline

Conversion Funnel шире Opportunity Pipeline.

```text id="hmbvqh"
Conversion Funnel
=
anonymous visit
→ professional outcome
```

```text id="qyqh9p"
Opportunity Pipeline
=
qualified professional opportunity
→ business outcome
```

---

# 101. Funnel Handoff Boundary

Ключевой момент:

```text id="mxrkta"
Public Funnel
→ Persisted Feedback
→ Internal Workflow
```

---

# 102. Public Analytics Must Not Become CRM Automatically

Anonymous browser activity не должна автоматически создавать Opportunity.

---

# 103. Funnel Event Model

События должны быть разделены по уровням.

---

# 104. Exposure Events

Примеры:

```text id="fsu3hn"
landing_viewed
campaign_landing_viewed
```

---

# 105. Evaluation Events

```text id="z5n86x"
quick_view_opened
professional_search_started
portfolio_opened
portfolio_filter_used
```

---

# 106. Evidence Events

```text id="lkf9nw"
video_started
showreel_started
emotional_grid_viewed
project_opened
role_opened
```

---

# 107. Questionnaire Events

```text id="b3ucip"
questionnaire_viewed
questionnaire_downloaded
public_builder_opened
public_builder_template_selected
public_builder_pdf_generated
```

---

# 108. Intent Events

```text id="2jx6z2"
casting_cta_started
role_offer_started
request_materials_started
professional_contact_started
```

---

# 109. Inquiry Events

```text id="dm27up"
professional_inquiry_submitted
casting_invitation_submitted
request_materials_submitted
```

---

# 110. Internal Business Events

```text id="fl9bfz"
feedback_qualified
casting_created
opportunity_stage_changed
offer_received
opportunity_booked
opportunity_closed
```

---

# 111. Naming Principle

Analytics Event name не должен подменять business entity.

Например:

```text id="h6rygv"
casting_cta_clicked
```

не означает:

```text id="jy199r"
Casting created
```

---

# 112. Client Event Reliability

Критический business outcome не должен зависеть только от client analytics event.

Источник истины:

```text id="4hic3e"
Business Transaction
```

Например `Feedback persisted`.

---

# 113. Server-Side Business Event Priority

Для критических conversion points предпочтителен business-backed event:

```text id="iopjb7"
Feedback created
```

вместо только:

```text id="sim6om"
submit button clicked
```

---

# 114. Analytics Failure Must Not Break Funnel

Если analytics service недоступен:

- Profile работает;
- Builder работает;
- Feedback сохраняется;
- Casting workflow работает.

---

# 115. No Conversion Loss Through Analytics

Analytics tracking MUST NOT быть blocking dependency professional action.

---

# 116. KPI Framework

KPI разделяются на:

```text id="ehsxwv"
Acquisition
Evaluation
Professional Intent
Inquiry
Opportunity
Outcome
Operational Health
```

---

# 117. Acquisition KPIs

Примеры:

- Relevant Visits;
- Source mix;
- Landing Page distribution.

---

# 118. Evaluation KPIs

- Quick View usage;
- Search success;
- Portfolio interaction;
- Evidence engagement.

---

# 119. Questionnaire KPIs

- Questionnaire View Rate;
- Prepared PDF Download Rate;
- Builder Open Rate;
- Builder Completion Rate;
- Custom PDF Generation Rate.

---

# 120. Intent KPIs

- CTA Start Rate;
- Request Materials Start;
- Casting Invitation Start.

---

# 121. Inquiry KPIs

- Professional Inquiry Submission Rate;
- form completion rate;
- qualified inquiry rate.

---

# 122. Opportunity KPIs

- Inquiry → Casting;
- Casting → Self-Tape;
- Casting → Audition;
- Audition → Callback;
- Callback → Offer.

---

# 123. Outcome KPIs

- Offer Rate;
- Booked Rate;
- Closed Not Selected Rate;
- time through opportunity stages.

---

# 124. North-Star-Oriented Metrics

Наиболее значимая цепочка:

```text id="qahxvg"
Relevant Professional Visits
      ↓
Qualified Professional Inquiries
      ↓
Castings
      ↓
Offers
      ↓
Booked Roles
```

---

# 125. Conversion Rate Denominator Must Be Explicit

Нельзя публиковать:

```text id="qsw68f"
Casting conversion = 12%
```

без определения denominator.

Возможны разные:

```text id="rvhho9"
Castings / Relevant Visits
Castings / Professional Inquiries
Offers / Castings
Booked / Offers
```

---

# 126. Time-Based KPIs

Полезны:

```text id="esttzu"
Time to Questionnaire
Time to Professional Action
Time to Feedback Review
Time to Casting Qualification
Time to Next Opportunity Stage
```

---

# 127. Five-Minute Journey KPI

Для UX исследований можно измерять:

```text id="0jp3gl"
share of tested professional journeys
completed within 5 minutes
```

Это не обязательно production browser metric.

---

# 128. Conversion Funnel by Entry Source

Можно анализировать:

```text id="if7ttw"
Search
Social
Referral
Shared Questionnaire
QR
Casting-Specific
```

---

# 129. Source Quality

High traffic source не обязательно является лучшим.

Например:

```text id="e58oc2"
Social
→ high visits
→ low qualified inquiry
```

против:

```text id="b5dq0p"
Referral
→ low visits
→ high casting conversion
```

---

# 130. Qualified Conversion Over Traffic Volume

Бизнес-решения SHOULD учитывать качество downstream outcome, а не только traffic.

---

# 131. Deep Link Conversion

Следует отдельно понимать эффективность:

- Portfolio links;
- Questionnaire links;
- Project links;
- Emotional links.

---

# 132. QR Conversion

QR analytics MAY фиксировать переход на approved public URL, если выбранная architecture это поддерживает.

QR не должен содержать invasive tracking requirement как условие доступа.

---

# 133. Casting-Specific Link Conversion

В future/target models casting-specific link MAY позволять измерять:

```text id="345br3"
specific professional share
→ document interaction
→ inquiry
```

с соблюдением Privacy.

---

# 134. International Funnel Segmentation

Можно анализировать:

- selected locale;
- localized Questionnaire;
- localized PDF;
- international Professional Inquiry.

---

# 135. Locale Conversion

Важно понимать, приводит ли localized Journey к реальному professional action.

---

# 136. Mobile Funnel

Особенно для Commercial Producer:

```text id="i1n57m"
Mobile Visit
→ Visual Evidence
→ Video
→ CTA
```

---

# 137. Mobile vs Desktop

Device class MAY использоваться для UX optimization.

Но не должен применяться для дискриминационного изменения professional facts.

---

# 138. Funnel Quality Indicators

Хорошая funnel:

- короткая;
- нелинейная;
- допускает deep links;
- не требует регистрации;
- сохраняет context;
- имеет reliable handoff;
- измеряет business outcomes.

---

# 139. Funnel Anti-Pattern CF-AP-01 — Pageview Funnel

```text id="i0xkum"
Home
→ Portfolio
→ Projects
→ Contacts
```

считается «обязательной» воронкой.

Неправильно.

---

# 140. Funnel Anti-Pattern CF-AP-02 — Time-on-Site Optimization

Искусственно увеличивать время пользователя на сайте — против целей продукта.

---

# 141. Funnel Anti-Pattern CF-AP-03 — Builder Forced

Пользователя заставляют использовать Builder даже если Prepared Questionnaire подходит.

---

# 142. Funnel Anti-Pattern CF-AP-04 — Contact as Dead End

Inquiry отправлена, но:

- не сохраняется;
- нет Reference Number;
- workflow дальше отсутствует.

---

# 143. Funnel Anti-Pattern CF-AP-05 — Notification as CRM

Professional Inquiry считается существующей только потому, что отправлен WhatsApp.

---

# 144. Funnel Anti-Pattern CF-AP-06 — AI Qualification

AI автоматически делает Opportunity Qualified.

---

# 145. Funnel Anti-Pattern CF-AP-07 — Vanity Optimization

Homepage redesign делается только потому, что увеличивает scroll depth, но снижает Casting CTA conversion.

---

# 146. Funnel Anti-Pattern CF-AP-08 — Intrusive Tracking

Избыточный сбор PII ради полной реконструкции поведения посетителя.

---

# 147. Funnel Anti-Pattern CF-AP-09 — Hidden Professional Action

CTA существует только в Contacts footer.

---

# 148. Funnel Anti-Pattern CF-AP-10 — Misleading Conversion

`button click` отчётно считается `Casting`.

---

# 149. Funnel Optimization Process

Правильная модель:

```text id="zgfdf8"
Observe Funnel
 ↓
Identify Friction
 ↓
Form Hypothesis
 ↓
UX/Product Review
 ↓
Implement Approved Change
 ↓
Test
 ↓
Measure
```

---

# 150. No Automatic Optimization

Analytics или Virtual Operator не меняют Public UX автоматически.

---

# 151. Virtual Operator Role

Operator MAY выявлять:

- unusual Builder abandonment;
- repeated broken links;
- high zero-result search;
- unusually low form completion;
- stale Questionnaire;
- conversion-impacting technical issue.

---

# 152. Virtual Operator Recommendation Example

```text id="t3v4gr"
Observation:
Many Casting-template users add Emotional Grid before generating PDF.

Recommendation:
Review default Casting template composition.
```

Human Product Decision required.

---

# 153. Funnel and Content Freshness

Если Primary Video broken, это может создать measurable funnel leakage.

Operator должен воспринимать это как high-impact exception.

---

# 154. Funnel and Public Search

Zero-result search MAY reveal professional data gap, но не должен автоматически создавать missing Skill.

---

# 155. Funnel and BB Assistant

BB Assistant MAY повышать качество:

- Biography;
- Project description;
- professional response;
- cover letter.

Но success BB Assistant измеряется не количеством generated drafts, а полезностью в professional workflow.

---

# 156. Funnel and Theme AI

Theme AI MAY улучшить presentation.

Но visual theme не должна ухудшать:

- content discovery;
- accessibility;
- CTA;
- performance.

---

# 157. Theme Conversion Guardrail

Если Theme снижает readability или мешает professional actions, она считается product regression независимо от эстетического качества.

---

# 158. Funnel and Accessibility

Недоступный keyboard/form interaction означает funnel failure для соответствующего пользователя.

Accessibility является частью conversion quality.

---

# 159. Funnel and Performance

Медленный First Useful Content может снижать Relevant Visit → Evaluation.

Поэтому performance metrics должны связываться с Customer Journey.

---

# 160. Funnel and Reliability

Broken:

- Video;
- PDF;
- CTA;
- Feedback;

могут напрямую разрывать воронку.

Они должны иметь высокий operational priority.

---

# 161. Funnel and Search Engine Traffic

SEO traffic полезен только если приводит на meaningful professional content и далее к relevant action.

---

# 162. Funnel and Blog

Blog MAY поддерживать:

```text id="0lbxv2"
Discovery
```

и SEO.

Но не является обязательным этапом professional conversion.

---

# 163. Blog-to-Professional Funnel

Возможная модель:

```text id="d52scr"
Blog
 ↓
Professional Profile Context
 ↓
Portfolio / Questionnaire
 ↓
Professional Action
```

---

# 164. Social Funnel

```text id="m35rek"
Social
 ↓
Visual Landing / Profile
 ↓
Portfolio / Emotional / Video
 ↓
CTA
```

---

# 165. Referral Funnel

```text id="nq5s2z"
Direct Recommendation
 ↓
Quick View
 ↓
Questionnaire
 ↓
Casting
```

Referral traffic может иметь высокий business intent.

---

# 166. Questionnaire Share Funnel

```text id="n2sl92"
Shared PDF / URL
 ↓
Questionnaire
 ↓
Official Profile
 ↓
Current Evidence
 ↓
Professional Action
```

---

# 167. Offline Funnel

```text id="fp6y1g"
Printed Material
 ↓
QR
 ↓
Digital Profile
 ↓
Current Information
 ↓
CTA
```

---

# 168. Funnel Continuity

Следующие context values SHOULD сохраняться где допустимо:

- locale;
- acquisition source;
- landing context;
- Builder Session.

---

# 169. Funnel Context Must Not Override Security

Даже если visitor пришёл по:

```text id="1k6nuu"
casting campaign
```

он не получает private information без разрешённой access model.

---

# 170. Funnel and Historical PDFs

Historical PDF может содержать snapshot data.

Переход по Official Profile URL позволяет пользователю получить current data.

---

# 171. Snapshot Transparency

Generated Date помогает понять разницу между:

```text id="rkid1t"
Document at generation time
```

и:

```text id="ct1m1l"
Current Profile
```

---

# 172. Funnel and Contact Visibility

Если direct Contact скрыт, Professional CTA всё равно может работать через Feedback Form.

---

# 173. Funnel Resilience

У пользователя SHOULD оставаться альтернативный path, если одна secondary capability недоступна.

Примеры:

```text id="lhqqnh"
Video broken
→ Portfolio + Questionnaire + Contact remain

WhatsApp broken
→ Feedback remains

Analytics broken
→ all professional actions remain
```

---

# 174. Funnel Acceptance Questions

Для каждой публичной страницы необходимо спросить:

1. Какой stage funnel она поддерживает?
2. Какой professional action доступен дальше?
3. Какие Persona используют страницу?
4. Может ли пользователь попасть сюда напрямую?
5. Есть ли dead end?
6. Измеряется ли meaningful interaction?
7. Не нарушается ли Privacy?

---

# 175. Funnel Coverage by Capability

| Capability | Funnel Stage |
|---|---|
| Home/Profile | Visit / Evaluation |
| Quick View | Evaluation |
| Search | Evaluation |
| Portfolio | Evaluation / Evidence |
| Video | Evidence |
| Emotional Grid | Evidence |
| Projects/Roles | Evidence |
| Questionnaire | Package |
| Public Builder | Package / Intent |
| Contacts | Intent |
| Feedback | Inquiry |
| Castings | Opportunity |
| Opportunity Pipeline | Outcome |
| Virtual Operator | Funnel Health |

---

# 176. Funnel Coverage Requirement

Крупный Public capability SHOULD иметь понятную роль в professional funnel.

Если не имеет, его business priority должен быть отдельно обоснован.

---

# 177. Marketing Content Boundary

Контент может повышать Discovery и Trust.

Но Marketing layer не изменяет факты для повышения conversion.

---

# 178. No Conversion Manipulation

Запрещены:

- ложная срочность;
- искусственные scarcity messages;
- скрытие информации ради CTA;
- вводящие в заблуждение professional claims.

---

# 179. Trust Over Short-Term Conversion

Если конкретный UX-трюк повышает clicks, но снижает достоверность или professional trust, он противоречит продукту.

---

# 180. Business Attribution to Booked

Если данные позволяют, желательно связать:

```text id="p88dps"
Booked
```

с:

- original Inquiry;
- Casting;
- source;
- relevant Questionnaire;
- campaign/referral context.

---

# 181. Attribution Is Evidence, Not Absolute Causality

Если пользователь впервые пришёл из Instagram, а позже получил рекомендацию напрямую, attribution может быть неоднозначной.

Система не должна выдавать сложный профессиональный результат за абсолютно причинённый одним click.

---

# 182. Multi-Touch Future Compatibility

Architecture MAY позднее поддержать multi-touch attribution.

Но текущая модель не должна требовать чрезмерного tracking.

---

# 183. Required Funnel Outputs for Dashboard

Admin/Product Dashboard SHOULD в итоге позволять увидеть минимум:

```text id="4jmwqp"
Professional Visits
Questionnaire Downloads
Custom PDFs
Professional Inquiries
Qualified Inquiries
Castings
Auditions
Callbacks
Offers
Booked
```

при наличии соответствующих данных.

---

# 184. Funnel Health Dashboard

Дополнительно:

- broken critical assets;
- Builder failure rate;
- form errors;
- notification failures;
- stale primary documents.

---

# 185. Dashboard Must Separate Analytics From Operational State

Например:

```text id="3z1kkh"
Casting inquiries this month: 4
```

— analytics/business metric.

```text id="inzd9n"
1 inquiry notification failed
```

— operational exception.

Не смешивать.

---

# 186. Funnel and Manual Offline Outcomes

Некоторые stages могут происходить вне сайта:

- Audition;
- Callback;
- Offer;
- Booked.

Admin должен иметь возможность зафиксировать их вручную.

---

# 187. Website Is Not the Entire Professional Process

Продукт поддерживает и фиксирует outcome, но не контролирует все внешние процессы.

---

# 188. Unknown Outcome

Если итог неизвестен, система не должна придумывать stage.

Возможность остаётся в корректном текущем состоянии до human update/closure.

---

# 189. No Automatic Closure by Inactivity Without Explicit Policy

Длительное отсутствие обновления не означает автоматически:

```text id="a8v7o2"
Not Selected
```

---

# 190. Conversion Funnel Traceability IDs

Для будущей трассировки используются:

```text id="8m5h0d"
CF-EXP-*
CF-VIS-*
CF-EVAL-*
CF-EVID-*
CF-QST-*
CF-INTENT-*
CF-INQ-*
CF-OPP-*
CF-OUT-*
CF-ATTR-*
CF-PRIV-*
```

---

# 191. Key Funnel Requirements

Минимальный нормативный набор:

```text id="yukvv2"
CF-REQ-001
Relevant Visit must be distinguishable analytically from raw page load where feasible.

CF-REQ-002
Prepared Questionnaire and Public Builder must be measured separately.

CF-REQ-003
CTA start and completed Professional Inquiry must be separate events.

CF-REQ-004
Feedback persistence is authoritative for inquiry creation.

CF-REQ-005
Feedback does not automatically become Casting.

CF-REQ-006
Opportunity stage changes require authoritative business action/data.

CF-REQ-007
Booked is the primary final positive business outcome.

CF-REQ-008
Analytics failure must not block professional workflow.

CF-REQ-009
Attribution must respect Data Minimization.

CF-REQ-010
Analytics recommendations must not automatically mutate Public content.
```

---

# 192. Relevant Product Principles

Особенно:

```text id="htkrnr"
PP-004 Casting Professional Drives Public UX
PP-005 Minimum Actions
PP-008 No Dead Ends
PP-055 Contextual CTA
PP-057 Save Before Notify
PP-060 Feedback ≠ Opportunity Automatically
PP-062 Opportunity Outcome Is a Business Fact
PP-063 Analytics Supports Decisions, Not Automatic Changes
PP-064 Business Outcome Over Vanity Metrics
PP-065 Attribution Must Be Privacy-Conscious
PP-080 Failure Must Be Contained
```

---

# 193. Future Functional Requirement Areas

```text id="3kwe8u"
FR-ANALYTICS
FR-ATTRIBUTION
FR-QUESTIONNAIRE
FR-PQB
FR-FEEDBACK
FR-CASTING
FR-OPPORTUNITY
FR-NOTIFICATIONS
FR-DASHBOARD
FR-PRIVACY
```

---

# 194. Future Testing Areas

Необходимо создать:

```text id="ykttcy"
E2E-FUNNEL-001 Visit → Questionnaire
E2E-FUNNEL-002 Questionnaire → Feedback
E2E-FUNNEL-003 Builder → PDF → Feedback
E2E-FUNNEL-004 Feedback persistence
E2E-FUNNEL-005 Feedback → Casting
E2E-FUNNEL-006 Opportunity stage transitions
E2E-FUNNEL-007 Booked outcome
E2E-FUNNEL-008 Analytics outage resilience
E2E-FUNNEL-009 Attribution privacy
E2E-FUNNEL-010 Mobile commercial funnel
```

---

# 195. Funnel Acceptance Criteria

Conversion Funnel считается корректно реализованной, если:

1. профессиональный пользователь не обязан проходить фиксированную последовательность страниц;
2. все основные Persona имеют короткий путь к Professional Action;
3. Prepared Questionnaire и Public Builder поддерживают разные needs;
4. Inquiry создаётся только после persistence;
5. notification failure не теряет Inquiry;
6. Feedback и Casting являются разными stages/entities;
7. Opportunity Pipeline отражает реальные business events;
8. Offer не равен Booked;
9. негативный outcome фиксируется отдельно;
10. attribution не требует избыточной PII;
11. analytics не блокирует workflow;
12. analytics не меняет сайт автоматически;
13. deep links могут конвертировать самостоятельно;
14. mobile Journey полноценно включён;
15. конечная business metric может доходить до Booked.

---

# 196. Главная Funnel Формула

```text id="esd8sw"
PROFESSIONAL VALUE
=
Relevant Visitors
×
Quality of Evaluation Experience
×
Ease of Professional Action
×
Quality of Opportunity Handling
×
Probability of Professional Outcome
```

Продукт может влиять на первые четыре компонента напрямую или косвенно.

Он не может гарантировать последний.

---

# 197. Финальная модель

```text id="jssbo6"
                    PROFESSIONAL CONVERSION FUNNEL

DISCOVERY / REACH
        ↓
RELEVANT PROFESSIONAL VISIT
        ↓
QUICK PROFESSIONAL EVALUATION
        ↓
PORTFOLIO / VIDEO / EMOTIONAL / EXPERIENCE
        ↓
PREPARED QUESTIONNAIRE
        │
        ├──────────────┐
        │              │
        ▼              ▼
   READY PDF      PUBLIC BUILDER
        │              │
        └───────┬──────┘
                ↓
        PROFESSIONAL INTENT
                ↓
             FEEDBACK
                ↓
           HUMAN REVIEW
                ↓
             CASTING
                ↓
      MATERIALS / SELF-TAPE
                ↓
             AUDITION
                ↓
             CALLBACK
                ↓
               OFFER
                ↓
              BOOKED
                ↓
       NEW PROJECT / ROLE
                ↓
       STRONGER ACTOR PROFILE
                ↓
        NEW PROFESSIONAL REACH
```

---

# 198. Финальный принцип

> **Conversion Funnel продукта должна измерять не количество кликов, а движение от релевантного профессионального интереса к реальному профессиональному результату. Сайт ускоряет оценку, предоставляет доказательства, даёт подходящий формат анкеты и минимизирует friction при обращении; внутренняя система не теряет запрос, квалифицирует его, сопровождает кастинг и фиксирует конечный outcome. Главным результатом остаётся не просмотр страницы и не скачивание PDF, а профессиональная возможность, которая способна завершиться подтверждённой ролью.**