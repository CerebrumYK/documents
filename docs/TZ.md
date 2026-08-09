# ТЕХНИЧЕСКОЕ ЗАДАНИЕ

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.


## Развитие профессионального сайта актрисы и системы управления актёрским профилем

**Статус документа:** целевая консолидированная спецификация
**Назначение:** единый источник требований для дальнейшего проектирования, разработки, тестирования и приёмки
**Приоритет:** требования настоящего документа имеют приоритет над ранее подготовленными фрагментарными описаниями новых функций

---

# 1. Цель проекта

Необходимо развить существующий сайт актрисы из обычного публичного портфолио и административной панели в единую профессиональную систему, обеспечивающую полный цикл:

1. ведения достоверного актёрского профиля;
2. управления фотографиями, видео, аудио и профессиональными материалами;
3. представления внешности и актёрского диапазона;
4. ведения проектов, ролей, образования и достижений;
5. формирования профессиональных актёрских анкет;
6. самостоятельного формирования кастинг-специалистом нужной ему анкеты;
7. получения профессиональных обращений;
8. обработки кастингов;
9. анализа кастингов с помощью ИИ;
10. подготовки материалов под конкретную роль;
11. отслеживания результатов кастингов;
12. управления публичным представлением сайта;
13. анализа эффективности материалов;
14. максимально возможной автоматизации рутинных операций.

Главный бизнес-результат системы:

> **сократить путь от первого знакомства профессионального посетителя с актрисой до получения нужных материалов, контакта, приглашения на кастинг и в конечном итоге получения роли.**

---

# 2. Основная продуктовая концепция

Сайт не должен рассматриваться как статическая галерея или электронная биография.

Целевая модель:

```text
ДОСТОВЕРНЫЙ ПРОФИЛЬ
        ↓
СТРУКТУРИРОВАННЫЕ ПРОФЕССИОНАЛЬНЫЕ ДАННЫЕ
        ↓
ПУБЛИЧНОЕ ПРОФЕССИОНАЛЬНОЕ ПРЕДСТАВЛЕНИЕ
        ↓
ПОРТФОЛИО / ВИДЕО / ЭМОЦИОНАЛЬНЫЙ ДИАПАЗОН
        ↓
АКТЁРСКАЯ АНКЕТА
        ↓
ГОТОВАЯ ИЛИ СОБРАННАЯ КАСТИНГ-СПЕЦИАЛИСТОМ ВЕРСИЯ
        ↓
ПРОФЕССИОНАЛЬНОЕ ОБРАЩЕНИЕ
        ↓
КАСТИНГ
        ↓
ИИ-АНАЛИЗ И ПОДГОТОВКА МАТЕРИАЛОВ
        ↓
ПРОБЫ / CALLBACK / OFFER
        ↓
РОЛЬ
        ↓
НОВЫЕ ПРОФЕССИОНАЛЬНЫЕ МАТЕРИАЛЫ
        ↓
ОБНОВЛЕНИЕ ПРОФИЛЯ
```

---

# 3. Основные принципы разработки

## 3.1. Single Source of Truth

Каждый факт должен вводиться и храниться в одном основном месте.

Например:

* язык — в структурированных языках;
* навык — в навыках;
* дата эмоциональной съёмки — в карточке съёмки;
* телефон — в справочнике контактов;
* проект — в карточке проекта.

Другие разделы используют эти данные через связи.

Не допускается ручное повторное ведение одного значения в разных разделах.

---

## 3.2. Source → Configuration → Derived

Необходимо разделять:

### Source

Исходные данные:

* фотография;
* видео;
* текст;
* контакт;
* навык;
* проект;
* кастинг.

### Configuration

Способ использования:

* crop;
* порядок;
* выбор в анкете;
* layout;
* PDF settings;
* visibility.

### Derived

Производный результат:

* thumbnail;
* emotional grid;
* PDF;
* AI recommendation;
* theme preview.

Изменение Derived не должно изменять Source.

---

## 3.3. Оригинальные фотографии неизменяемы

Оригинальные фотографии должны храниться без изменения.

Разрешено создавать:

* thumbnail;
* web derivative;
* PDF derivative;
* crop representation;
* composite Emotional Grid.

Запрещено физически перезаписывать оригинал.

---

## 3.4. Повторное использование без копирования

Один media asset может использоваться:

* в портфолио;
* в проекте;
* в анкете;
* в эмоциональном портфолио.

Физическая копия файла не создаётся.

Создаются связи.

---

## 3.5. Human-in-the-loop

ИИ является помощником.

Модель:

```text
Input
 ↓
AI Analysis / Proposal
 ↓
Preview
 ↓
Human Review
 ↓
Human Edit
 ↓
Human Approval
 ↓
Apply
```

ИИ не должен автоматически:

* изменять фактические данные;
* публиковать анкету;
* публиковать тему;
* отправлять ответ кастинг-директору;
* изменять внешность;
* принимать решение об участии.

---

# 4. Пользовательские роли

## 4.1. Администратор

Управляет:

* профилем;
* портфолио;
* эмоциональным портфолио;
* Emotional Grid;
* проектами;
* курсами;
* навыками;
* языками;
* контактами;
* медиа;
* анкетами;
* кастингами;
* обращениями;
* оформлением;
* публикацией.

---

## 4.2. Editor

В перспективе может:

* редактировать контент;
* готовить материалы;
* обрабатывать обращения;
* создавать draft.

Публикационные права определяются отдельно.

---

## 4.3. Viewer

Имеет read-only доступ к разрешённым административным данным.

---

## 4.4. Публичный посетитель

Может:

* изучать опубликованный профиль;
* смотреть материалы;
* использовать публичные контакты;
* отправлять сообщения.

---

## 4.5. Кастинг-специалист

Дополнительно может:

* использовать Casting Quick View;
* искать сведения;
* смотреть видео;
* открывать анкету;
* скачивать готовый PDF;
* самостоятельно сформировать собственную анкету;
* отправлять приглашение на кастинг.

Регистрация для базового публичного сценария не требуется.

---

# 5. Структура административной панели

Основное меню:

1. Главная.
2. Профиль.
3. Портфолио.
4. Эмоциональное портфолио.
5. Эмоциональный ряд.
6. Проекты.
7. Курсы и тренинги.
8. Актёрские анкеты.
9. Кастинги.
10. Контакты.
11. Видео, аудио и ссылки.
12. Медиатека.
13. Обратная связь.
14. Уведомления.
15. Оформление сайта.
16. Настройки.

Главная административной панели в перспективе должна быть action-oriented dashboard, а не только каталогом ссылок.

---

# 6. Структура публичного сайта

Минимальный публичный контур:

* Главная.
* Профиль.
* Портфолио.
* Эмоциональное портфолио.
* Проекты.
* Курсы и тренинги.
* Актёрская анкета.
* Конструктор анкеты.
* Контакты.

Закрытые:

* кастинги;
* анализ кастингов;
* обращения;
* admin notes;
* audit;
* настройки.

---

# 7. Принцип Admin → Public

Любой новый профессиональный функционал должен проектироваться одновременно в двух плоскостях:

```text
ADMIN
Управление данными
        ↓
PUBLIC
Профессиональное использование данных
```

Исключение — исключительно служебные функции.

---

# 8. Матрица публичного отображения

| Admin               | Public                                        |
| ------------------- | --------------------------------------------- |
| Profile             | Home / Profile / Quick View                   |
| Portfolio           | Portfolio                                     |
| Emotional Portfolio | Emotional Portfolio                           |
| Emotional Grid      | Profile / Questionnaire / Emotional Portfolio |
| Projects            | Projects / Selected Projects                  |
| Training            | Training                                      |
| Skills              | Profile / Questionnaire / Search              |
| Languages           | Profile / Questionnaire                       |
| Video               | Profile / Media / Questionnaire               |
| Audio               | Media / Questionnaire                         |
| Links               | Questionnaire                                 |
| Contacts            | Contacts / CTA                                |
| Questionnaires      | Questionnaire / PDF                           |
| Public Templates    | Public Builder                                |
| Theme               | весь публичный сайт                           |
| Feedback config     | Feedback Form                                 |

---

# 9. Видимость профессиональных данных

Для данных, которые могут использоваться публично, предусмотреть независимые разрешения:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

Необходимо исключить ситуацию, когда скрытый элемент загружается браузеру и просто скрывается CSS.

Сервер возвращает только разрешённые данные.

---

# 10. Customer Journey как обязательный слой проектирования

До разработки любой публичной функции необходимо определить:

* persona;
* источник перехода;
* цель;
* информационный приоритет;
* Primary CTA;
* Secondary CTA;
* mobile flow;
* five-minute journey;
* conversion event.

Customer Journey является входным требованием для:

* Information Architecture;
* UX;
* Homepage;
* Search;
* Questionnaire;
* Analytics;
* E2E.

---

# 11. Метод 6-3-5

Для проектирования используются шесть персон, каждая проходит собственный пяти­минутный сценарий и формирует три ключевых продуктовых вывода.

## Persona 1 — Casting Director

Цель:

быстро принять решение о приглашении.

Выводы:

1. Casting Quick View.
2. Search Inside Profile.
3. Casting CTA.

## Persona 2 — Casting Assistant

Цель:

сделать shortlist.

Выводы:

1. Structured Quick Facts.
2. Portfolio Filters.
3. Compact PDF.

## Persona 3 — Commercial Casting Producer

Цель:

быстро оценить внешний типаж и эмоциональность на смартфоне.

Выводы:

1. Mobile-first.
2. Shooting Date.
3. Contextual CTA.

## Persona 4 — Director / Producer

Цель:

понять диапазон и опыт.

Выводы:

1. Emotional Range как ключевой раздел.
2. Progressive Projects.
3. Request Materials.

## Persona 5 — International Casting Specialist

Цель:

получить локализованные материалы.

Выводы:

1. Global language state.
2. Быстрый language switch.
3. Share-ready PDF.

## Persona 6 — Virtual Portfolio Operator

Цель:

сократить ручное администрирование.

Выводы:

1. Change once — propagate.
2. Draft automation.
3. Exception-driven administration.

---

# 12. UX-бюджет профессионального посетителя

Целевые показатели:

| Действие                             |   Максимум |
| ------------------------------------ | ---------: |
| Запуск видеовизитки                  | 1 действие |
| Открыть портфолио                    |          1 |
| Emotional Range                      |          1 |
| Открыть анкету                       |          1 |
| Скачать выбранную готовую анкету     |         ≤2 |
| Начать Casting Invitation            |         ≤2 |
| Найти навык                          |   1 search |
| Переключить язык                     |          1 |
| Получить public contact              |         ≤1 |
| Создать типовую custom questionnaire |     <3 мин |
| Создать расширенную кастинговую      |     <5 мин |

---

# 13. Первый экран сайта

Hero должен одновременно выполнять визуальную и профессиональную функцию.

Минимальное содержимое:

* основная фотография;
* имя;
* профессиональное позиционирование;
* ключевые структурированные сведения;
* город;
* языки;
* 3–5 ключевых навыков.

Primary CTA:

* Смотреть видео.
* Актёрская анкета.
* Связаться.

Secondary:

* Портфолио.
* Emotional Range.
* Собрать свою анкету.

---

# 14. Casting Quick View

Создать агрегированное профессиональное представление.

Не является отдельным источником данных.

Содержит:

* headshot;
* имя;
* статус;
* location;
* ключевые параметры;
* языки;
* навыки;
* full-body;
* close-up;
* video;
* emotional grid;
* выбранные проекты.

CTA:

```text
Смотреть видео
Открыть анкету
Пригласить на кастинг
```

---

# 15. Поиск внутри профиля

Добавить профессиональный поиск:

```text
Найти навык, язык, проект...
```

Искать по:

* навыкам;
* языкам;
* проектам;
* ролям;
* обучению;
* доступным профессиональным сведениям.

Не индексировать:

* contacts private;
* castings;
* feedback;
* notes;
* drafts.

---

# 16. Основное портфолио

Создать самостоятельный модуль.

Не использовать Project как контейнер основного фотопортфолио.

Типы:

* Full Body.
* Close-Up.
* Waist.
* Profile.
* Three Quarter.
* Staged.
* Appearance.
* Additional.
* Other.

---

# 17. Модель Portfolio Item

Поля:

```text
id
profile_id
media_asset_id
type
title
caption
alt_text
shooting_date
photographer
studio
sort_order
is_published
is_primary_full_body
is_primary_close_up
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
show_on_public_site
allow_extra_material
created_at
updated_at
```

Один профиль может иметь максимум:

* один основной Full Body;
* один основной Close-Up.

Переключение выполнять атомарно.

---

# 18. Обязательные фотографии анкеты

Любая готовая анкета должна иметь:

1. Full Body.
2. Close-Up.

Они выбираются исключительно из Portfolio.

Если материал присутствует только в Media Library — сначала создать Portfolio Item.

---

# 19. Дополнительные фотографии анкеты

Portfolio Item может быть разрешён:

```text
allow_extra_material = true
```

Администратор или Public Builder может выбрать конкретные разрешённые материалы в пределах своей policy.

---

# 20. Публичное портфолио

Поддержать:

* фильтры;
* full-screen preview;
* даты;
* captions;
* сохранение пропорций;
* responsive derivatives;
* lazy loading.

Если пользователь находится в Public Questionnaire Builder Session, разрешённый материал может иметь:

```text
Добавить в мою анкету
```

---

# 21. Эмоциональное портфолио

Создать отдельную сущность, полностью независимую от Project.

Не должно участвовать:

* в project filters;
* project counts;
* role timeline;
* performance dates.

---

# 22. Emotional Portfolio Data Model

```text
id
profile_id
slug
title
shooting_date NOT NULL
location
photographer
studio
description
status
sort_order
published_at
created_at
updated_at
deleted_at
```

Status:

```text
draft
ready
published
archived
```

---

# 23. Фотографии Emotional Portfolio

Хранить:

```text
id
profile_id
emotional_portfolio_id
media_asset_id
emotion_label
caption
alt_text
sort_order
is_published
allow_in_public_questionnaire_builder
created_at
updated_at
```

---

# 24. Обязательная дата эмоциональной съёмки

Без даты запрещено:

* публиковать портфолио;
* финализировать Emotional Grid;
* использовать Grid в готовой анкете.

Дата отображается:

* admin;
* public;
* profile;
* questionnaire;
* PDF.

---

# 25. Emotional Grid

Создать самостоятельную сущность.

Допустимые сетки:

```text
1x2
1x3
1x4
2x2
2x3
2x4
3x2
3x3
3x4
4x2
4x3
4x4
```

---

# 26. Emotional Grid Data Model

```text
id
profile_id
emotional_portfolio_id
title
rows
columns
shooting_date
status
is_primary
is_published
allow_in_public_questionnaire_builder
master_asset_path
web_asset_path
pdf_asset_path
thumbnail_asset_path
render_version
created_at
updated_at
```

---

# 27. Emotional Grid Cell

```text
id
grid_id
profile_id
position
media_asset_id

crop_x
crop_y
crop_width
crop_height
scale
offset_x
offset_y
rotation

verification_status
verified_by
verified_at

created_at
updated_at
```

Status:

* unchecked;
* needs_adjustment;
* confirmed.

---

# 28. UX Emotional Grid

Flow:

1. выбрать Emotional Portfolio;
2. выбрать grid size;
3. получить пустые cells;
4. выбрать photo;
5. открыть crop editor;
6. настроить zoom;
7. position;
8. проверить face framing;
9. подтвердить cell;
10. заполнить остальные;
11. readiness validation;
12. render;
13. preview;
14. save;
15. publish при необходимости.

---

# 29. Правило 90% лица

Цель:

примерно 90% полезной площади каждой ячейки должно занимать лицо модели крупным планом.

Система может использовать face detection как вспомогательный инструмент.

Но:

* компьютерная оценка не является единственным критерием;
* окончательное подтверждение выполняет человек.

---

# 30. Запрет изменения внешности

Для Emotional Grid разрешены только геометрические операции:

* crop;
* scale;
* position;
* orientation correction.

Запрещены:

* retouch;
* генерация лица;
* изменение выражения;
* изменение волос;
* изменение кожи;
* background generation;
* inpainting;
* outpainting;
* object generation/removal;
* body modification.

---

# 31. Рендеринг Grid

Создавать:

* master;
* web;
* PDF;
* thumbnail.

При изменении:

* crop;
* cell source;
* order;
* dimensions;

derivatives становятся `stale`.

До успешного render может продолжать использоваться последняя валидная опубликованная производная версия.

---

# 32. Emotional Grid в анкете

В анкету попадают:

* одно composite image;
* shooting date;
* hyperlink на full Emotional Portfolio.

Отдельные исходные эмоциональные фотографии в блок анкеты не включаются.

---

# 33. Курсы и тренинги

Создать отдельный модуль.

Типы:

* Course.
* Training.
* Masterclass.
* Intensive.
* Laboratory.
* Seminar.
* School.
* Qualification.
* Other Education.

---

# 34. Training Data Model

```text
id
profile_id
type
title
organizer
teacher
start_date
end_date
is_ongoing
format
location
description
skills_text
status
sort_order
published_at
created_at
updated_at
deleted_at
```

Relations:

* images;
* video;
* certificates;
* documents;
* links.

---

# 35. Миграция Training

Создать wizard:

```text
Project(type=Other)
        ↓
Предварительный анализ
        ↓
Выбор записей
        ↓
Mapping
        ↓
Preview
        ↓
Migration
        ↓
Validation
        ↓
Archive source
```

Файлы не копировать.

Повторный запуск не создаёт дубль.

---

# 36. Видео, аудио и внешние ссылки

Media и Links должны быть логически разделены.

Видеоматериалы:

* Video Intro;
* Showreel;
* Additional Video.

Аудио:

* Voice;
* Audio Intro;
* Other.

---

# 37. Структурированные ссылки

Редактор:

| № | Описание | URL |
| - | -------- | --- |

Поля:

```text
id
profile_id
description
url
sort_order
is_active
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
created_at
updated_at
```

Номер вычисляется по sort order.

---

# 38. Контакты

Единый справочник:

* Phone.
* Mother Phone.
* Father Phone.
* WhatsApp.
* Telegram.
* Instagram.
* TikTok.
* YouTube.
* Other.

---

# 39. Contact Data Model

```text
id
profile_id
type
custom_type
value
url
label
sort_order
is_active
is_primary
show_on_public_site
include_in_questionnaire_default
allow_in_public_questionnaire_builder
created_at
updated_at
```

Для `other` custom_type обязателен.

---

# 40. Публичные контакты

Сервер должен выбирать только:

```text
show_on_public_site = true
AND
is_active = true
```

Закрытые данные не передаются в HTML/API/browser payload.

---

# 41. Контакты в анкете

Каждая Questionnaire Revision должна иметь собственные contact links.

Глобальные defaults используются только при первоначальном создании draft.

После этого выбор независим.

---

# 42. Публичная страница Contacts

Содержит:

* основной контакт;
* доступные контакты;
* social;
* guardian contacts при разрешении;
* Feedback Form.

Layout:

* desktop ≤3;
* tablet ≤2;
* mobile 1.

---

# 43. Actor Questionnaire V2

Разделить:

```text
Questionnaire
Questionnaire Revision
```

---

# 44. Questionnaire Data Model

```text
id
profile_id
type
title
description
casting_id
status
is_primary
allow_public_download
show_in_download_picker
available_from
available_until
published_revision_id
created_at
updated_at
```

Types:

```text
compact
extended
casting
custom
```

Status:

```text
draft
ready
published
archived
```

---

# 45. Questionnaire Revision

```text
id
questionnaire_id
revision_number
language
configuration
pdf_configuration
html_configuration
created_by
created_at
is_immutable
```

Published Revision immutable.

Редактирование означает создание следующей revision.

---

# 46. Questionnaire Blocks

Поддержать:

* Basic Info.
* Full Body.
* Close-Up.
* Appearance.
* Professional Info.
* Skills.
* Languages.
* Projects.
* Roles.
* Performances.
* Emotional Grid.
* Training.
* Achievements.
* Video.
* Audio.
* Links.
* Extra Photos.
* Contacts.

---

# 47. Block Configuration

Каждый блок:

```text
enabled
sort_order
title
mode
max_items
start_new_page
show_in_html
show_in_pdf
```

UI — drag-and-drop.

---

# 48. Compact Questionnaire

Default:

* Basic Info.
* Full Body.
* Close-Up.
* Appearance.
* Main Skills.
* Languages.
* Video Intro.
* Selected Projects.
* Contacts.

---

# 49. Extended Questionnaire

Default включает расширенный набор:

* профиль;
* фотографии;
* эмоциональный ряд;
* проекты;
* роли;
* навыки;
* языки;
* training;
* achievements;
* video;
* audio;
* links;
* contacts.

---

# 50. Casting Questionnaire

Дополнительные поля:

* casting name;
* project;
* role;
* deadline.

Создаётся draft.

Не публикуется автоматически.

---

# 51. Questionnaire Readiness

UI:

```text
Готовность: 82%

✓ Full Body
✓ Close-Up
✓ Profile
✗ Emotional Grid incomplete
✓ Contacts
✗ Link invalid
✓ HTML
✗ PDF stale
```

Ready запрещён при blocking error.

---

# 52. PDF Settings

Для Revision настраивать:

* A4;
* orientation;
* margins;
* language;
* title;
* casting;
* generation date;
* header;
* footer;
* page numbers;
* filename pattern.

---

# 53. PDF Layout Rules

Не допускать:

* text overflow;
* пустых страниц;
* orphan heading;
* разрыв single contact;
* distortion photo;
* broken Cyrillic.

URL визуально может сокращаться, но hyperlink остаётся полным.

---

# 54. Основная публичная анкета

Одновременно одна Questionnaire может быть Primary.

Переключение:

1. ownership;
2. readiness;
3. HTML;
4. PDF;
5. media;
6. transaction;
7. clear old primary;
8. set new;
9. invalidate cache;
10. audit.

---

# 55. Download Picker

Кнопка «Скачать PDF» не должна сразу начинать загрузку.

Modal:

```text
Компактная
Расширенная
Casting X
```

Далее:

* language;
* download.

Показываются только разрешённые и неистёкшие варианты.

---

# 56. Public Questionnaire Builder

Это отдельный публичный профессиональный модуль.

Существует одновременно с готовой администраторской анкетой.

---

# 57. Основной смысл Public Builder

Кастинг-специалист может:

* взять готовую анкету за основу;
* выбрать шаблон;
* убрать лишние блоки;
* добавить разрешённые сведения;
* выбрать конкретные проекты;
* выбрать навыки;
* выбрать языки;
* выбрать фотографии;
* выбрать training;
* выбрать Emotional Grid;
* сформировать PDF.

Он не может:

* редактировать профиль;
* менять факты;
* видеть hidden data;
* изменять администраторскую анкету.

---

# 58. Точка входа Public Builder

На странице Questionnaire:

```text
Рекомендуемая актёрская анкета

[Открыть]
[Скачать PDF]

Нужен другой набор информации?

[Собрать анкету под свой кастинг]
```

---

# 59. Public Questionnaire Templates

Templates:

* Quick.
* Standard.
* Extended.
* Casting.
* Custom — при необходимости.

---

# 60. Quick Template

Default:

* Basic.
* Close-Up.
* Full Body.
* Parameters.
* Languages.
* Main Skills.
* Video.
* Primary Contact.

---

# 61. Standard Template

Default:

* Basic;
* Close-Up;
* Full Body;
* Appearance;
* Skills;
* Languages;
* Emotional Grid;
* Selected Projects;
* Video;
* Training Compact;
* Contacts.

---

# 62. Extended Template

Может включать все публично разрешённые профессиональные блоки.

---

# 63. Casting Template

Дополнительно предлагает необязательные поля:

* Casting Name.
* Project.
* Role.

Они используются только для текущей temporary questionnaire.

Не записываются в профиль.

---

# 64. Public Builder UI

Первый шаг:

```text
Какой документ вам нужен?

[Быстрый]
[Стандартный]
[Расширенный]
[Для кастинга]
```

Затем показываются selected defaults.

Пользователь не начинает с пустого документа.

---

# 65. Выбор блоков Public Builder

```text
[x] Basic
[x] Full Body
[x] Close-Up
[ ] Additional Photos
[x] Appearance
[x] Skills
[x] Languages
[x] Emotional Grid
[x] Projects
[ ] Training
[x] Video
[ ] Audio
[ ] Links
[x] Contacts
```

---

# 66. Выбор конкретных элементов

Проекты:

```text
[x] Project A
[ ] Project B
[x] Project C
```

Skills:

```text
[x] Dance
[x] Stage Movement
[ ] Swimming
```

Аналогично:

* languages;
* training;
* images;
* links;
* media.

---

# 67. Public Builder не является редактором документа

Внешний пользователь не настраивает:

* fonts;
* margins;
* colors;
* page break;
* exact image sizes.

Он выбирает только содержание.

Layout рассчитывается системой.

---

# 68. Live Preview

Desktop:

```text
┌──────────────────┬──────────────────────┐
│ Blocks           │ Live Preview         │
│                  │                      │
│ [x] Photos       │ Questionnaire        │
│ [x] Skills       │                      │
│ [ ] Training     │                      │
│                  │                      │
│ Generate PDF     │                      │
└──────────────────┴──────────────────────┘
```

Mobile — последовательный интерфейс.

---

# 69. Temporary Questionnaire Session

Модель:

```text
id
profile_id
template_id
language
configuration
casting_name
role_name
expires_at
created_at
```

Не сохранять hidden data.

---

# 70. Server-side Revalidation

При PDF generation каждый выбранный объект повторно проверить:

```text
profile ownership
published/available
allow_in_public_questionnaire_builder = true
```

Нельзя доверять переданным браузером IDs.

---

# 71. Public Builder PDF

PDF должен содержать:

* выбранные материалы;
* generated date;
* официальный источник;
* ссылку на актуальный профиль.

Опционально QR.

---

# 72. QR в PDF

QR может вести:

* на основной профиль;
* разрешённую публичную анкету.

Нельзя использовать:

* admin URL;
* permanent secret token;
* PII в URL.

---

# 73. Builder без обязательной регистрации

Базовый UX:

```text
Open
→ Choose
→ Preview
→ Generate
```

Account creation не требуется.

---

# 74. Public Builder Anti-Abuse

Предусмотреть:

* rate limit;
* session TTL;
* max items;
* max generated documents;
* server validation;
* filename sanitization.

---

# 75. Public Builder Analytics

Разрешённые агрегированные events:

```text
builder_opened
template_selected
section_enabled
section_disabled
pdf_generated
casting_cta_started
```

Не отправлять внешним analytics:

* phone;
* email;
* casting text;
* attachments.

---

# 76. Кастинги

Создать самостоятельную сущность.

Кастинг не является Project.

---

# 77. Casting Data Model

```text
id
profile_id
title
project_name
role_name
company
agency
received_at
deadline
source_text
status
linked_questionnaire_id
source_feedback_id
created_at
updated_at
```

Status:

```text
draft
ready_for_analysis
analyzed
questionnaire_created
closed
archived
```

---

# 78. Casting Assets

Поддержать:

* screenshots;
* PDFs;
* images;
* briefs;
* role cards;
* references.

Хранятся private.

---

# 79. AI Casting Analysis

Pipeline:

```text
Source Ingestion
 ↓
Text/Image Extraction
 ↓
Requirement Extraction
 ↓
Normalization
 ↓
Profile Matching
 ↓
Confidence
 ↓
Recommendations
 ↓
Human Review
 ↓
Questionnaire Draft
```

---

# 80. Требования кастинга

Каждый requirement хранить структурированно:

```text
category
value
source_type
source_reference
confidence
requires_manual_review
```

Confidence:

* high;
* medium;
* low;
* manual_review.

---

# 81. Запрет ИИ-галлюцинаций

Если значение отсутствует:

```text
Не указано в материалах кастинга
```

Запрещено:

```text
Предположительно...
```

для фактических требований.

---

# 82. Сопоставление с профилем

Result:

* Match.
* Partial.
* No Data.
* Manual Review.

Каждый результат должен ссылаться на profile source.

---

# 83. AI Recommendations

Структура:

```text
section
action
reason
source_requirement_ids
selected_entity_ids
requires_approval
```

Action:

* include;
* exclude;
* include_compact;
* include_full;
* manual_review.

---

# 84. Создание кастинговой анкеты

После human approval:

1. создать Questionnaire(type=casting);
2. создать Revision;
3. перенести подтверждённые selections;
4. сохранить draft;
5. связать Casting;
6. не публиковать;
7. не включать public download.

---

# 85. Публичная Feedback Form

Типы:

* Casting Invitation.
* Role Offer.
* Collaboration.
* Questionnaire Request.
* Additional Materials.
* Question.
* Comment.
* Other.

---

# 86. Feedback Mandatory Fields

* Name.
* Type.
* Subject.
* Message.
* At least one contact.
* Consent.

Optional:

* organization;
* position;
* project;
* role;
* email;
* phone;
* WhatsApp;
* Telegram;
* deadline;
* URL;
* attachments.

---

# 87. Feedback Save Sequence

Критический порядок:

```text
Validate
 ↓
Rate Limit
 ↓
Validate Attachments
 ↓
SAVE FEEDBACK
 ↓
COMMIT
 ↓
SUCCESS RESPONSE
 ↓
Internal Notification
 ↓
WhatsApp Queue
```

WhatsApp не находится в критическом пути сохранения.

---

# 88. Feedback Data Model

```text
id
public_number
profile_id
type
source_page
sender_name
sender_surname
organization
position
subject
message
preferred_contact_type
read_status
workflow_status
priority
assigned_to
next_action_at
created_at
updated_at
archived_at
```

---

# 89. Feedback Workflow

Read:

```text
unread
read
```

Business status:

```text
new
in_progress
waiting_information
waiting_decision
reply_sent
completed
rejected
spam
archived
```

Priority:

* low;
* normal;
* high;
* urgent.

---

# 90. Feedback Attachments

Хранить private.

Проверять:

* real MIME;
* allowed formats;
* max size;
* total size;
* checksum;
* executable content.

Прямой публичный URL запрещён.

---

# 91. Admin Feedback Inbox

Колонки:

* №;
* date;
* unread;
* status;
* priority;
* type;
* sender;
* organization;
* subject;
* preferred contact;
* attachment;
* assigned;
* next action.

Поддержать:

* search;
* filter;
* sort;
* pagination.

---

# 92. Feedback Internal Notes

```text
id
feedback_id
author_id
text
created_at
updated_at
```

Notes:

* private;
* не отправляются visitor;
* не отправляются WhatsApp.

---

# 93. Создание Casting из Feedback

До выполнения показать mapping:

```text
Title ← Subject
Company ← Organization
Project ← Project
Role ← Role
Deadline ← Deadline
Requirements ← Message
Attachments ← Selected
```

Каждый пункт можно исключить.

После создания — bilateral relation.

---

# 94. WhatsApp Notifications

Использовать официально подключённый или утверждённый владельцем канал.

Notification по умолчанию:

* feedback number;
* type;
* name;
* organization;
* subject;
* date;
* protected admin link.

Не передавать:

* полный message;
* attachment;
* internal note.

---

# 95. WhatsApp Status

```text
pending
processing
sent
delivered
failed
retrying
cancelled
```

Ошибка WhatsApp не меняет статус принятия Feedback.

---

# 96. Notification Idempotency

Unique key:

```text
feedback:{id}:recipient:{recipient}:new-feedback
```

Повторная обработка не должна создавать duplicate primary notification.

---

# 97. Quiet Hours

Настройки:

* immediate;
* quiet period;
* delayed until allowed period.

Internal notification всегда создаётся сразу.

---

# 98. Notification Center

Types:

* New Feedback.
* WhatsApp Failed.
* Feedback Overdue.
* Feedback Unassigned.
* Casting Deadline Soon.

Каждое уведомление ведёт к entity.

---

# 99. Opportunity Pipeline

Добавить возможность фиксировать бизнес-результат кастинга:

```text
New
Qualified
Materials Requested
Self-Tape Requested
Audition
Callback
Offer
Booked
Closed — Not Selected
Withdrawn
```

Это необходимо для анализа эффективности сайта.

---

# 100. Marketing Funnel

Основная воронка:

```text
Relevant Visitor
 ↓
Profile View
 ↓
Portfolio / Video
 ↓
Questionnaire
 ↓
Ready PDF / Custom Builder
 ↓
Professional Inquiry
 ↓
Casting
 ↓
Self-Tape / Audition
 ↓
Callback
 ↓
Offer
 ↓
Booked
```

---

# 101. Primary Conversion

* Casting Invitation.
* Role Offer.
* Qualified Professional Inquiry.

Secondary:

* PDF Download.
* Custom PDF.
* Video Play.
* Emotional Portfolio View.
* Request Materials.

---

# 102. Attribution

При необходимости хранить:

* direct;
* search;
* social;
* campaign;
* shared questionnaire;
* casting-specific link.

Также:

* landing page;
* UTM при наличии.

Не передавать private data в внешнюю аналитику.

---

# 103. Virtual Portfolio Operator

Виртуальный оператор является административным помощником.

Не является публичным chatbot.

---

# 104. Задачи Virtual Operator

Может:

* анализировать новые media;
* находить duplicate;
* предлагать тип;
* проверять completeness;
* искать missing fields;
* проверять links;
* определять stale PDF;
* предлагать Emotional Grid;
* предлагать crop;
* формировать draft;
* обновлять derivatives;
* формировать recommendations;
* анализировать агрегированную аналитику.

Не может:

* публиковать;
* придумывать факты;
* отправлять professional reply;
* менять профиль без подтверждения.

---

# 105. Exception-Driven Administration

Admin Dashboard должен показывать не весь объём данных, а то, что требует внимания.

Пример:

```text
Сегодня

🔴 Новое приглашение на кастинг
🟠 Deadline через 2 дня
🟡 2 Emotional Grid cells требуют проверки
🟡 Questionnaire PDF stale
🟢 Все public links работают
```

---

# 106. Content Freshness

Virtual Operator ищет:

* missing shooting dates;
* outdated review candidates;
* broken URLs;
* incomplete projects;
* incomplete training;
* expired casting questionnaire;
* stale generated files;
* unused media.

Он только предлагает действие.

---

# 107. Virtual Operator и Public Builder Analytics

Оператор может сообщать:

```text
70% кастинг-специалистов используют Languages.
```

или:

```text
Training чаще всего выключают в Quick Template.
```

Это рекомендация по оптимизации template.

Автоматическое изменение template запрещено.

---

# 108. AI Theme Builder

Создать административный модуль:

* Current Theme.
* AI Generator.
* Manual Editor.
* Preview.
* Saved Themes.
* History.

---

# 109. Theme AI Input

Пользователь описывает:

* настроение;
* стиль;
* palette;
* undesirable colors;
* contrast;
* hero;
* cards;
* buttons.

Пример:

```text
Сделать сайт более кинематографичным,
тёмным, с тёплым светом и без ярко-красного.
```

---

# 110. Theme AI Output

ИИ не генерирует произвольный исполняемый CSS.

ИИ формирует validated theme schema.

Пример:

```text
mode
backgroundPrimary
backgroundSecondary
surface
textPrimary
textSecondary
accentPrimary
accentSecondary
link
buttonPrimary
buttonPrimaryText
border
focus
radius
shadow
motion
```

---

# 111. Manual Theme Editing

После AI generation любой token редактируем.

Поддержать Lock:

```text
🔒 Accent
```

Locked value не меняется при следующей AI generation.

---

# 112. Hero Editor

Источники:

* current photo;
* Portfolio;
* Media Library;
* solid;
* gradient;
* decorative.

Разрешено:

* crop;
* zoom;
* position;
* overlay;
* text position.

Запрещено изменение внешности.

---

# 113. Theme Scope

AI разрешено менять:

* palette;
* typography scale;
* surface;
* cards;
* radius;
* shadow;
* buttons;
* hero composition;
* decorative elements;
* motion intensity.

Запрещено:

* routes;
* profile data;
* contacts;
* questionnaire content;
* SEO text;
* security;
* forms;
* permissions.

---

# 114. Theme Preview

Обязательные viewports:

* Desktop.
* Tablet.
* Mobile.

Обязательные страницы:

* Home.
* Profile.
* Portfolio.
* Emotional Portfolio.
* Projects.
* Training.
* Questionnaire.
* Public Builder.
* Contacts.
* Feedback.
* PDF Picker.

---

# 115. Side-by-Side Preview

Поддержать:

```text
Current | Proposed
```

с синхронным viewport.

---

# 116. Accessibility Theme Validation

Проверять:

* body text;
* secondary text;
* links;
* buttons;
* forms;
* focus;
* contact card;
* hero text.

Critical error блокирует Publish.

---

# 117. Theme Revision

Разделить:

```text
Theme
Theme Revision
```

Published Revision immutable.

Rollback создаёт новую Revision на основе старой.

---

# 118. Temporary Theme

Настройки:

```text
start_at
end_at
fallback_theme
```

По окончании активировать fallback.

Удаление используемого fallback запрещено.

---

# 119. Конкурентное редактирование

Для ключевых объектов использовать optimistic locking.

Например:

```text
version
```

или `updated_at`.

При conflict:

```text
409
```

UI:

```text
Эта запись была изменена другим пользователем.
Обновите данные перед сохранением.
```

Запрещено silent overwrite.

---

# 120. Soft Delete и Archive

Не физически удалять связанную entity без проверки dependency.

Пример:

```text
Фотография используется:
— Questionnaire revision 4
— Emotional Grid
```

Удаление блокируется или предлагается Archive.

---

# 121. Audit Log

Хранить:

```text
id
profile_id
actor_id
entity_type
entity_id
action
old_revision_id
new_revision_id
metadata
created_at
```

Не хранить secrets.

---

# 122. Обязательные Audit Events

* portfolio.created;
* portfolio.updated;
* emotional_portfolio.created;
* emotional_grid.rendered;
* questionnaire.revision_created;
* questionnaire.published;
* questionnaire.primary_changed;
* public_builder.pdf_generated;
* casting.created;
* casting.analysis_completed;
* feedback.received;
* feedback.status_changed;
* notification.failed;
* theme.generated;
* theme.published;
* theme.rolled_back;
* migration.executed.

---

# 123. Миграция Emotional Portfolio

Сначала Dry Run.

Отчёт:

* найденные записи;
* photos;
* video;
* questionnaire relations;
* conflicts.

После approval:

* новая сущность;
* relation migration;
* no copy files;
* source archive.

---

# 124. Миграция Training

Аналогично:

```text
Project Other
→ Preview
→ Classification
→ Course/Training
→ Relations
→ Validation
→ Archive Source
```

---

# 125. Миграция обязательных фотографий

Существующие Full Body и Close-Up, используемые в анкетах, зарегистрировать как Portfolio Item.

Media Asset остаётся прежним.

---

# 126. Idempotent Migrations

Хранить migration mapping:

```text
source_entity_type
source_entity_id
migration_version
target_entity_id
```

Повторный запуск:

```text
Already migrated
```

---

# 127. Media Lifecycle

Каждый Media Asset должен иметь:

* original;
* metadata;
* derivatives;
* usage relations.

При удалении relation original остаётся, если используется где-либо ещё.

---

# 128. Image Performance

Публичные galleries используют:

* thumbnail;
* responsive Web derivative;
* lazy loading.

Не загружать full original в grid view.

---

# 129. PDF Lifecycle

Status:

```text
Not Generated
Generating
Ready
Error
Stale
```

Изменение draft data помечает PDF `stale`.

Published immutable revision не меняется.

---

# 130. Background Processing

Длительные операции должны быть отделены от UI request, если это необходимо архитектурой:

* master Emotional Grid rendering;
* PDF rendering;
* AI analysis;
* notifications.

Пользователь должен видеть состояние.

---

# 131. Feature Flags

Предусмотреть возможность включать:

```text
emotionalPortfolioV2
trainingSection
questionnaireVariants
publicQuestionnaireBuilder
castings
feedback
whatsapp
aiThemeBuilder
virtualOperator
```

---

# 132. Health Status

Admin Settings может показывать:

* Media Storage.
* PDF Renderer.
* AI.
* WhatsApp.

Недоступность optional AI/WhatsApp не ломает публичный сайт.

---

# 133. SEO публичных страниц

Для публичных sections поддержать:

* title;
* description;
* canonical;
* Open Graph;
* indexing flag;
* localized metadata.

Admin и private не индексируются.

---

# 134. Многоязычность

Язык должен быть глобальным состоянием пользовательского пути.

При переключении:

* profile;
* CTA;
* questionnaire;
* Public Builder;
* captions;
* PDF;
* link descriptions;

должны использовать выбранный язык при наличии локализованных данных.

---

# 135. Accessibility

Новые публичные и административные интерфейсы обязаны поддерживать:

* keyboard;
* visible focus;
* proper labels;
* accessible icon names;
* modal focus trap;
* Escape close;
* form error association;
* logical tab order.

---

# 136. Error Catalogue

Создать технические error codes.

Примеры:

```text
GRID_PHOTO_MISSING
GRID_CELL_UNCONFIRMED
QUESTIONNAIRE_FULL_BODY_REQUIRED
QUESTIONNAIRE_CLOSE_UP_REQUIRED
QUESTIONNAIRE_PDF_FAILED
PUBLIC_BUILDER_ITEM_NOT_ALLOWED
CONTACT_INVALID_URL
CASTING_NO_SOURCE
FEEDBACK_CONTACT_REQUIRED
FEEDBACK_ATTACHMENT_REJECTED
THEME_CONTRAST_FAILED
THEME_SCHEMA_INVALID
```

Для каждого:

* technical code;
* user message;
* severity;
* blocking;
* field;
* remediation.

---

# 137. Безопасность Feedback

Обязательно:

* rate limit;
* sanitization;
* honeypot;
* MIME validation;
* private storage;
* consent.

Дополнительная challenge-проверка — только при подозрительной активности.

---

# 138. Public Builder Security

Обязательно:

* no arbitrary profile IDs;
* server-side ownership validation;
* public permission validation;
* TTL;
* generation rate limit;
* safe filename;
* no hidden contacts;
* no admin token;
* no private casting material.

---

# 139. Privacy

Закрытые данные не должны присутствовать:

* в HTML;
* в JS state;
* в public API;
* в metadata;
* в URL;
* в search index.

---

# 140. Customer Journey — Casting Director

E2E business flow:

```text
Landing
→ Quick View
→ Portfolio
→ Video
→ Emotional Range
→ Questionnaire
→ Ready PDF OR Custom Builder
→ Casting Invitation
```

Весь сценарий должен укладываться в 5 минут для подготовленного пользователя.

---

# 141. Customer Journey — Casting Assistant

```text
Landing
→ Quick Facts
→ Search Skill
→ Portfolio Filter
→ Projects
→ Compact PDF
```

Biography не должна быть обязательной для поиска фактов.

---

# 142. Customer Journey — Commercial Producer

Mobile:

```text
Social
→ Profile
→ Appearance Photos
→ Emotional Grid
→ Video
→ Shooting Date
→ Role/Collaboration CTA
```

---

# 143. Customer Journey — Director

```text
Recommendation
→ Video
→ Emotional Range
→ Selected Projects
→ Role
→ Request Materials
```

---

# 144. Customer Journey — International Specialist

```text
Landing
→ EN
→ Quick Facts
→ Languages
→ Video
→ English Questionnaire
→ English PDF
→ Contact
```

---

# 145. Customer Journey — Virtual Operator

```text
New Content
→ Analyze
→ Suggest Classification
→ Identify Missing Fields
→ Create Drafts
→ Update Derivatives
→ Readiness Check
→ Show Exceptions
```

---

# 146. No Dead Ends

Любая профессиональная страница должна иметь дальнейшее действие.

После:

* Portfolio;
* Emotional Portfolio;
* Project;
* Training;
* Questionnaire;

предлагать релевантно:

* Video;
* Questionnaire;
* Contact;
* Casting Invitation.

---

# 147. Contextual CTA

Использовать:

* Invite to Casting.
* Offer Role.
* Request Materials.
* Collaboration.
* Ask Question.

Все могут работать через один Feedback Backend с разным `type`.

---

# 148. Source-Aware Journey

Допускается менять приоритет визуального представления в зависимости от входа:

### Search

Profile + Professional Facts.

### Social

Visual + Video.

### Direct Questionnaire

Questionnaire + PDF.

### Casting-specific link

Relevant Questionnaire + Contact.

Фактические данные при этом неизменны.

---

# 149. Маркетинговая аналитика

Не считать Page Views главным KPI.

Цель:

```text
Relevant Visitor
→ Professional Interest
→ Material Consumption
→ Questionnaire
→ Contact
→ Casting
→ Role
```

---

# 150. KPI

Рекомендуемые продуктовые KPI:

* Questionnaire View Rate.
* Questionnaire Download Rate.
* Public Builder Open Rate.
* Custom PDF Generation Rate.
* Questionnaire → Contact Rate.
* Custom Questionnaire → Casting Rate.
* Casting → Audition.
* Casting → Callback.
* Casting → Offer.
* Casting → Booked.

---

# 151. Analytics by Content

Агрегированно анализировать:

* video plays;
* Emotional Range views;
* most selected Public Builder sections;
* frequently removed sections;
* selected projects;
* selected skills;
* template usage.

Результат предназначен для рекомендаций.

---

# 152. Information Priority

P0:

* Main Photo;
* Name;
* Identity;
* Video;
* Questionnaire;
* Contact.

P1:

* Full Body;
* Close-Up;
* Parameters;
* Languages;
* Main Skills;
* Emotional Range.

P2:

* Selected Projects;
* Roles;
* Achievements;
* Training.

P3:

* archive;
* extended details;
* extra media.

---

# 153. Admin Daily Dashboard

Главная админки должна отвечать:

> Что требует моего внимания?

Widgets:

* new casting opportunities;
* overdue feedback;
* deadlines;
* stale PDF;
* broken links;
* Emotional Grid checks;
* incomplete records;
* service health.

---

# 154. Business Rules IDs

В документации правила должны иметь постоянные IDs.

Примеры:

```text
BR-EMO-001 Shooting date required.
BR-EMO-002 Grid uses only allowed source photos.
BR-QST-001 Full Body required.
BR-QST-004 Published Revision immutable.
BR-PQB-001 Public Builder returns only allowed data.
BR-FDB-003 WhatsApp failure does not lose feedback.
BR-THM-001 AI theme requires human approval.
```

---

# 155. Traceability

Создать матрицу:

| Requirement                | Business Rule | Issue    | Test    |
| -------------------------- | ------------- | -------- | ------- |
| Emotional Date             | BR-EMO-001    | TASK-... | E2E-... |
| Public Builder permissions | BR-PQB-001    | TASK-... | IT-...  |
| WhatsApp isolation         | BR-FDB-003    | TASK-... | E2E-... |

---

# 156. Документация репозитория

Целевая структура:

```text
docs/
├── TZ.md
├── product/
│   ├── product-overview.md
│   ├── business-rules.md
│   ├── information-architecture.md
│   └── terminology.md
│
├── customer-journey/
│   ├── overview.md
│   ├── personas.md
│   ├── method-6-3-5.md
│   ├── casting-director.md
│   ├── casting-assistant.md
│   ├── commercial-producer.md
│   ├── director.md
│   ├── international-casting.md
│   ├── virtual-operator.md
│   ├── conversion-funnel.md
│   └── automation-matrix.md
│
├── ux/
│   ├── public-navigation.md
│   ├── admin-navigation.md
│   ├── user-flows.md
│   ├── forms.md
│   └── states.md
│
├── architecture/
│   ├── overview.md
│   ├── domain-model.md
│   ├── database-schema.md
│   ├── state-machines.md
│   ├── media-lifecycle.md
│   ├── questionnaire-revisions.md
│   ├── notifications.md
│   └── ai-boundaries.md
│
├── modules/
│   ├── portfolio.md
│   ├── emotional-portfolio.md
│   ├── emotional-grid.md
│   ├── training.md
│   ├── contacts.md
│   ├── questionnaires.md
│   ├── public-questionnaire-builder.md
│   ├── castings.md
│   ├── feedback.md
│   ├── notifications.md
│   ├── themes.md
│   └── virtual-operator.md
│
├── migrations/
├── security/
├── testing/
└── implementation/
```

---

# 157. State Machines

Отдельно задокументировать:

* Emotional Portfolio.
* Emotional Grid.
* Questionnaire.
* Casting.
* Feedback.
* Notification.
* Theme.

Для transition:

* from;
* to;
* trigger;
* permissions;
* validation;
* side effects.

---

# 158. Server Operations

Минимально документировать:

```text
createPortfolioItem
updatePortfolioItem
setPrimaryFullBody
setPrimaryCloseUp

createEmotionalPortfolio
publishEmotionalPortfolio

createEmotionalGrid
updateGridCell
validateGrid
renderGrid

createQuestionnaire
createQuestionnaireRevision
publishQuestionnaire
setPrimaryQuestionnaire

getPublicQuestionnaireTemplates
getPublicBuilderAvailableData
createPublicQuestionnaireSession
validatePublicQuestionnaire
generatePublicQuestionnairePdf

createCasting
analyzeCasting
createCastingQuestionnaire

submitFeedback
updateFeedback
createCastingFromFeedback

generateThemeProposal
publishTheme
rollbackTheme
```

Для каждой операции задать:

* input;
* output;
* auth;
* business validation;
* transaction boundary;
* error codes;
* audit;
* side effects.

---

# 159. Definition of Ready

Issue готов к разработке только если известны:

* Goal.
* Business Rule.
* Persona.
* User Flow.
* Scope.
* Out of Scope.
* Dependencies.
* Data Model.
* Backend.
* Admin UI.
* Public UI.
* Validation.
* Permissions.
* Error Cases.
* Tests.
* Acceptance Criteria.

---

# 160. Definition of Done

Issue закрывается только если:

1. реализована бизнес-логика;
2. реализована server validation;
3. реализован Admin UI, если применимо;
4. реализован Public UI, если применимо;
5. реализованы permissions;
6. обработаны edge cases;
7. migration добавлена, если требуется;
8. unit tests проходят;
9. integration tests проходят;
10. E2E проходит;
11. accessibility проверена;
12. documentation обновлена;
13. regression отсутствует.

---

# 161. Unit Tests

Минимально:

* allowed grid sizes;
* grid count;
* crop persistence;
* primary photo uniqueness;
* questionnaire readiness;
* revision immutability;
* contact visibility;
* Public Builder permissions;
* Public Builder revalidation;
* casting normalization;
* feedback validation;
* notification idempotency;
* theme schema;
* locked theme tokens.

---

# 162. Integration Tests

Проверить:

* PostgreSQL constraints;
* cross-profile protection;
* primary questionnaire transaction;
* theme publication transaction;
* media relations;
* migrations;
* feedback before notification;
* retry;
* Public Builder hidden data;
* revision immutability.

---

# 163. E2E — Emotional Portfolio

```text
Create Shoot
→ Date
→ 16 Photos
→ 4x4 Grid
→ Crop
→ Confirm
→ Render
→ Publish
→ Public View
→ Questionnaire
```

---

# 164. E2E — Portfolio / Questionnaire

```text
Create Full Body
→ Create Close-Up
→ Set Primary
→ Compact Questionnaire
→ PDF
→ Publish
→ Public Download
```

---

# 165. E2E — Public Builder

```text
Open Questionnaire
→ Customize
→ Standard Template
→ Remove Training
→ Add Project
→ Add Emotional Grid
→ Preview
→ Generate PDF
→ Verify only allowed data
```

---

# 166. E2E — Casting

```text
Create Casting
→ Paste Text
→ Attach Image
→ Analyze
→ Review Requirements
→ Modify Recommendation
→ Create Questionnaire Draft
```

---

# 167. E2E — Feedback

```text
Submit Casting Invitation
→ Receive Number
→ Admin Badge
→ Open Message
→ Note
→ Create Casting
→ Verify Relation
```

---

# 168. E2E — WhatsApp Failure

```text
Submit Feedback
→ Save
→ Provider Failure
→ Feedback Exists
→ Internal Alert
→ Retry
→ No Duplicate
```

---

# 169. E2E — Theme

```text
Prompt
→ Generate
→ Lock Accent
→ Regenerate
→ Preview
→ Accessibility
→ Publish
→ Rollback
```

---

# 170. E2E — 6-3-5 Customer Journeys

Обязательно проверять:

* CJM Casting Director.
* CJM Casting Assistant.
* CJM Commercial Producer.
* CJM Director.
* CJM International.
* CJM Virtual Operator.

---

# 171. Порядок будущей реализации

## Phase 0 — Documentation

* Business Rules.
* Customer Journey.
* Domain Model.
* UX.
* Data Model.
* Acceptance.

## Phase 1 — Foundation

* migrations framework;
* permissions;
* ownership guards;
* audit;
* validation.

## Phase 2 — Content Domains

* Portfolio;
* Emotional Portfolio;
* Grid;
* Training;
* Contacts;
* Links.

## Phase 3 — Questionnaire V2

* types;
* revisions;
* builder;
* PDF;
* publication.

## Phase 4 — Public Questionnaire Builder

* templates;
* visibility;
* session;
* preview;
* PDF;
* security.

## Phase 5 — Existing Data Migration

* Emotional.
* Training.
* Portfolio mandatory photos.

## Phase 6 — Casting

* entity;
* assets;
* AI;
* recommendation;
* questionnaire.

## Phase 7 — Communication

* public feedback;
* inbox;
* notes;
* casting creation.

## Phase 8 — Notifications

* internal;
* WhatsApp;
* retry;
* quiet hours.

## Phase 9 — Marketing / Opportunity Pipeline

* attribution;
* business statuses;
* conversion analytics.

## Phase 10 — Theme System

* schema;
* manual;
* AI;
* hero;
* preview;
* accessibility;
* rollback.

## Phase 11 — Virtual Operator

* freshness;
* exceptions;
* recommendations;
* aggregated insights.

## Phase 12 — QA / Production

* Unit.
* Integration.
* E2E.
* Accessibility.
* Migration rehearsal.
* Production build.
* Regression.
* Documentation.

---

# 172. Функции вне автоматического управления ИИ

ИИ не имеет права самостоятельно:

* публиковать актёрскую анкету;
* менять основной контакт;
* публиковать закрытые контакты;
* принимать Casting Invitation;
* отвечать кастинг-директору;
* изменять физические параметры;
* добавлять несуществующий опыт;
* изменять фотографии актрисы;
* публиковать Theme;
* удалять важные данные.

---

# 173. Что не должно реализовываться как побочный эффект

Запрещено, чтобы:

* создание Casting автоматически создавало опубликованную Questionnaire;
* включение контакта в Questionnaire публиковало его на сайте;
* добавление фото в Media автоматически публиковало Portfolio;
* AI classification автоматически меняла категории;
* Public Builder менял Admin Questionnaire;
* Feedback автоматически считался подтверждённым Casting;
* Theme AI менял content.

---

# 174. Основные критерии финальной приёмки

Система считается соответствующей целевому ТЗ, если:

1. Portfolio отделено от Project.
2. Emotional Portfolio отделено от Project.
3. Training отделено от Project.
4. Full Body/Close-Up берутся из Portfolio.
5. Grid поддерживает все предусмотренные размеры.
6. Исходные фото не модифицируются.
7. Emotional Date обязательна.
8. Grid используется как один объект анкеты.
9. Полное Emotional Portfolio доступно по ссылке.
10. Contacts имеют независимую visibility.
11. Links структурированы.
12. Questionnaire поддерживает revisions.
13. Published Revision immutable.
14. Готовая анкета существует на сайте.
15. Public Builder существует на сайте.
16. Public Builder использует шаблоны.
17. Casting Specialist может выбрать блоки.
18. Может выбрать конкретные Projects/Skills/Languages/Training/Photos.
19. Hidden data не доступны Builder.
20. Builder не изменяет профиль.
21. Builder генерирует server-side PDF.
22. Builder работает без mandatory registration.
23. PDF содержит generation date и official source.
24. Casting является отдельной сущностью.
25. AI не создаёт отсутствующие факты.
26. Recommendation требует approval.
27. Casting Questionnaire создаётся Draft.
28. Feedback сначала сохраняется.
29. WhatsApp не влияет на сохранение.
30. Admin Inbox имеет unread/workflow.
31. Feedback превращается в Casting после команды.
32. Opportunity Pipeline фиксирует результат.
33. Theme AI использует structured schema.
34. AI Theme не публикуется автоматически.
35. Original actor images не изменяются Theme AI.
36. Theme доступна в Desktop/Tablet/Mobile Preview.
37. Rollback работает.
38. Virtual Operator работает как recommendation/draft assistant.
39. Cross-profile access блокируется.
40. Migration идемпотентны.
41. Optimistic locking защищает данные.
42. Unit Tests проходят.
43. Integration Tests проходят.
44. E2E проходят.
45. 6 Customer Journeys проходят.
46. Accessibility проходит.
47. Production Build проходит.
48. Existing functionality не регрессировал.
49. Documentation обновлена.
50. Требования трассируются до тестов.

---

# 175. Главная конечная бизнес-логика

Внешний цикл:

```text
DISCOVERY
   ↓
PROFESSIONAL QUICK VIEW
   ↓
EVIDENCE
   ↓
READY QUESTIONNAIRE
        OR
CUSTOM QUESTIONNAIRE
   ↓
PROFESSIONAL CONTACT
   ↓
CASTING
   ↓
AUDITION
   ↓
CALLBACK
   ↓
OFFER
   ↓
ROLE
```

Внутренний цикл:

```text
NEW CONTENT
   ↓
MEDIA LIBRARY
   ↓
VIRTUAL OPERATOR
   ↓
CLASSIFICATION / RECOMMENDATION
   ↓
ADMIN APPROVAL
   ↓
SINGLE SOURCE OF TRUTH
   ↓
AUTOMATIC DERIVATIVES
   ↓
PUBLIC SITE
   ↓
QUESTIONNAIRES
   ↓
CASTING WORKFLOW
   ↓
RESULT
   ↓
NEW PROFESSIONAL EXPERIENCE
   ↓
PROFILE UPDATE
```

---

# 176. Итоговый принцип продукта

Конечный сайт должен работать по модели:

> **минимум действий для администратора, минимум поиска для кастинг-специалиста, максимум достоверных профессиональных данных, максимум автоматизации производных операций и максимальное сокращение пути от интереса к приглашению на кастинг.**

Администратор управляет достоверностью и публикацией.

Система автоматизирует повторяющиеся операции.

ИИ помогает анализировать и предлагать.

Кастинг-специалист получает свободу получить либо профессионально подготовленную анкету, либо самостоятельно собрать из разрешённых данных именно тот документ, который нужен для конкретной роли.

Все эти функции должны рассматриваться как части единого продукта, а не как независимые дополнительные страницы.
