# TERMINOLOGY

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Единый словарь терминов проекта

**Целевой файл:** `docs/product/terminology.md`  
**Документ:** DOC-011  
**Статус:** ✅ Completed  
**Тип:** Product Foundation

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`

---

# 1. Назначение документа

Настоящий документ устанавливает единую терминологию для всего проекта.

Все последующие документы:

- Customer Journey;
- UX;
- Domain Model;
- Architecture;
- Data Model;
- API;
- Module Specifications;
- Migrations;
- Tests;
- GitHub Epics;
- GitHub Issues;

MUST использовать термины настоящего документа в том же значении.

Основная цель:

> исключить ситуации, когда одна сущность в разных частях проекта называется разными словами либо одно слово используется для нескольких разных сущностей.

---

# 2. Правила использования терминологии

## TERM-RULE-001 — Один термин — одно значение

Один нормативный термин MUST обозначать одну конкретную сущность, процесс или состояние.

---

## TERM-RULE-002 — Разные сущности имеют разные названия

Если две сущности различаются по ownership, lifecycle или business meaning, они MUST иметь разные термины.

---

## TERM-RULE-003 — Английский canonical term

Для технической документации каждому ключевому понятию назначается canonical English term.

Русское название используется для интерфейса и пояснений.

Пример:

```text
Актёрская анкета
Canonical: Questionnaire
```

---

## TERM-RULE-004 — Canonical term используется в коде и технических документах

Если не определено иначе, названия:

- сущностей;
- API;
- domain events;
- test IDs;
- GitHub labels;

SHOULD основываться на canonical term.

---

## TERM-RULE-005 — UI может использовать более естественную формулировку

Например canonical:

```text
Public Questionnaire Builder
```

Public UI:

```text
Собрать свою анкету
```

Это не создаёт новую сущность.

---

# 3. Основные продуктовые термины

---

## TERM-001 — Product

**Русский:** Продукт  
**Canonical:** `Product`

Полная профессиональная система сайта актрисы, включающая:

- публичный сайт;
- административную панель;
- управление профессиональными данными;
- анкеты;
- кастинги;
- коммуникации;
- автоматизацию;
- аналитику.

Не использовать слово Product как синоним отдельного Project.

---

## TERM-002 — Actor Profile

**Русский:** Актёрский профиль / Профиль  
**Canonical:** `Actor Profile`

Корневая бизнес-сущность, объединяющая достоверные профессиональные данные актрисы.

Profile является основным ownership boundary для:

- Identity;
- Portfolio;
- Skills;
- Languages;
- Projects;
- Training;
- Contacts;
- Questionnaires;
- Castings.

Не путать с публичной страницей Profile Page.

---

## TERM-003 — Profile Page

**Русский:** Страница профиля  
**Canonical:** `Profile Page`

Публичное представление Actor Profile.

Это Presentation, а не отдельный источник данных.

---

## TERM-004 — Professional Identity

**Русский:** Профессиональная идентичность  
**Canonical:** `Professional Identity`

Совокупность базовых данных, определяющих профессиональный профиль:

- имя;
- позиционирование;
- location;
- biography;
- appearance;
- skills;
- languages.

---

# 4. Данные и источники истины

---

## TERM-010 — Master Data

**Русский:** Основные данные / Мастер-данные  
**Canonical:** `Master Data`

Достоверные данные, являющиеся основным источником истины.

Примеры:

- Skill;
- Language;
- Project;
- Contact;
- Training Item.

---

## TERM-011 — Source

**Русский:** Источник / Исходные данные  
**Canonical:** `Source`

Первичный материал или факт, на основе которого формируется дальнейшее представление.

Примеры:

- оригинальная фотография;
- текст кастинга;
- Contact;
- Project record.

---

## TERM-012 — Source of Truth

**Русский:** Источник истины  
**Canonical:** `Source of Truth`

Авторитетная сущность, из которой другие представления получают значение.

---

## TERM-013 — Single Source of Truth

**Русский:** Единый источник истины  
**Canonical:** `Single Source of Truth`, `SSOT`

Принцип, согласно которому один профессиональный факт имеет одно авторитетное место хранения.

---

## TERM-014 — Configuration

**Русский:** Конфигурация  
**Canonical:** `Configuration`

Настройки того, как Source используется или отображается.

Примеры:

- crop;
- порядок;
- выбор блоков;
- PDF settings;
- Theme tokens.

Configuration не является новым профессиональным фактом.

---

## TERM-015 — Derived Content

**Русский:** Производный контент  
**Canonical:** `Derived Content`

Результат автоматической или конфигурационной обработки Source.

Примеры:

- thumbnail;
- Emotional Grid image;
- PDF;
- Preview.

---

## TERM-016 — Projection

**Русский:** Проекция / Представление данных  
**Canonical:** `Projection`

Контекстно ограниченное представление Master Data для конкретного потребителя.

Основные Projection:

- Admin Projection;
- Public Projection;
- Casting Quick View Projection;
- Public Builder Projection;
- Search Projection;
- AI Casting Projection;
- PDF Snapshot Projection.

---

## TERM-017 — Snapshot

**Русский:** Снимок данных  
**Canonical:** `Snapshot`

Фиксированное состояние выбранных данных в определённый момент.

Используется для:

- Published Questionnaire;
- generated PDF;
- Casting Analysis;
- других исторически значимых результатов.

Snapshot не обновляется автоматически вместе с Master Data.

---

# 5. Media

---

## TERM-020 — Media Library

**Русский:** Медиатека  
**Canonical:** `Media Library`

Единый административный каталог media assets.

Не является самостоятельным профессиональным разделом публичного сайта.

---

## TERM-021 — Media Asset

**Русский:** Медиаобъект / Медиафайл  
**Canonical:** `Media Asset`

Логическое представление физического media resource.

Типы:

- image;
- video;
- audio;
- document.

---

## TERM-022 — Original Asset

**Русский:** Оригинал  
**Canonical:** `Original Asset`

Исходный неизменённый файл.

Original Asset MUST NOT перезаписываться производными операциями.

---

## TERM-023 — Media Derivative

**Русский:** Производная версия медиа  
**Canonical:** `Media Derivative`

Файл, созданный из Original Asset.

Примеры:

- Thumbnail;
- Web Derivative;
- PDF Derivative;
- Preview.

---

## TERM-024 — Thumbnail

**Русский:** Миниатюра  
**Canonical:** `Thumbnail`

Небольшая производная версия для списков и previews.

---

## TERM-025 — Web Derivative

**Русский:** Web-версия  
**Canonical:** `Web Derivative`

Оптимизированная версия media asset для публичного Web UI.

---

## TERM-026 — PDF Derivative

**Русский:** PDF-версия изображения  
**Canonical:** `PDF Derivative`

Версия, оптимизированная для использования в PDF.

---

# 6. Portfolio

---

## TERM-030 — Portfolio

**Русский:** Основное актёрское портфолио  
**Canonical:** `Portfolio`

Самостоятельный набор профессиональных фотографий, предназначенных для представления внешности и профессионального образа актрисы.

Portfolio не является Project.

Portfolio не является Emotional Portfolio.

---

## TERM-031 — Portfolio Item

**Русский:** Фотография портфолио / Элемент портфолио  
**Canonical:** `Portfolio Item`

Бизнес-сущность, связывающая Media Asset с профессиональной ролью в Portfolio.

Содержит:

- category;
- title;
- date;
- visibility;
- questionnaire permissions;
- другие metadata.

---

## TERM-032 — Full Body

**Русский:** Фото в полный рост  
**Canonical:** `Full Body`

Portfolio category для профессиональной фотографии полного роста.

---

## TERM-033 — Close-Up

**Русский:** Крупный план  
**Canonical:** `Close-Up`

Portfolio category для крупного плана лица.

---

## TERM-034 — Primary Full Body

**Русский:** Основное фото в полный рост  
**Canonical:** `Primary Full Body`

Portfolio Item, назначенный стандартным Full Body для профиля и создаваемых анкет.

---

## TERM-035 — Primary Close-Up

**Русский:** Основной крупный план  
**Canonical:** `Primary Close-Up`

Portfolio Item, назначенный стандартным Close-Up.

---

## TERM-036 — Additional Photo

**Русский:** Дополнительная фотография  
**Canonical:** `Additional Photo`

Portfolio Item, не являющийся обязательным Full Body или Close-Up, но доступный для дополнительного материала.

---

# 7. Emotional Portfolio

---

## TERM-040 — Emotional Portfolio

**Русский:** Эмоциональное портфолио  
**Canonical:** `Emotional Portfolio`

Самостоятельная профессиональная фотосессия, предназначенная для демонстрации эмоционального диапазона актрисы.

Не является Project.

Не является обычным Portfolio.

---

## TERM-041 — Emotional Portfolio Photo

**Русский:** Фотография эмоционального портфолио  
**Canonical:** `Emotional Portfolio Photo`

Media Asset, связанный с конкретной Emotional Portfolio session.

---

## TERM-042 — Emotion Label

**Русский:** Эмоция / Метка эмоции  
**Canonical:** `Emotion Label`

Структурированное или текстовое обозначение эмоционального состояния на фотографии.

---

## TERM-043 — Shooting Date

**Русский:** Дата съёмки  
**Canonical:** `Shooting Date`

Дата создания конкретной фотосессии.

Для Emotional Portfolio является обязательным профессиональным metadata.

---

# 8. Emotional Grid

---

## TERM-050 — Emotional Grid

**Русский:** Эмоциональный ряд / Эмоциональная сетка  
**Canonical:** `Emotional Grid`

Составное профессиональное изображение, включающее crop нескольких фотографий Emotional Portfolio.

Для пользовательского интерфейса предпочтительно:

```text
Эмоциональный ряд
```

В технической документации:

```text
Emotional Grid
```

---

## TERM-051 — Grid Cell

**Русский:** Ячейка эмоционального ряда  
**Canonical:** `Grid Cell`

Одна позиция Emotional Grid.

Содержит ссылку на Source Photo и crop configuration.

---

## TERM-052 — Crop

**Русский:** Кадрирование  
**Canonical:** `Crop`

Геометрическое выделение части исходного изображения без изменения содержания изображения.

---

## TERM-053 — Grid Layout

**Русский:** Размер / Схема сетки  
**Canonical:** `Grid Layout`

Комбинация rows × columns.

Пример:

```text
4×4
```

---

## TERM-054 — Confirmed Cell

**Русский:** Подтверждённая ячейка  
**Canonical:** `Confirmed Cell`

Grid Cell, кадрирование которой подтверждено человеком.

---

# 9. Professional Experience

---

## TERM-060 — Project

**Русский:** Проект  
**Canonical:** `Project`

Реальная профессиональная работа актрисы.

Примеры:

- фильм;
- сериал;
- спектакль;
- реклама;
- озвучивание.

Не использовать Project для:

- Training;
- Emotional Portfolio;
- обычной фотосессии.

---

## TERM-061 — Role

**Русский:** Роль  
**Canonical:** `Role`

Профессиональная роль актрисы внутри Project.

---

## TERM-062 — Performance

**Русский:** Показ / Представление / Выступление  
**Canonical:** `Performance`

Конкретное датированное событие, связанное с Project, где применимо.

---

## TERM-063 — Project Media

**Русский:** Медиа проекта  
**Canonical:** `Project Media`

Media Asset, связанный с конкретным Project или Role.

Не становится автоматически Portfolio Item.

---

## TERM-064 — Featured Project

**Русский:** Избранный проект  
**Canonical:** `Featured Project`

Project, получивший повышенный presentation priority.

Это не отдельный Project copy.

---

## TERM-065 — Achievement

**Русский:** Достижение  
**Canonical:** `Achievement`

Профессиональное достижение, награда, признание или сертификация.

Может относиться:

- к Project;
- к Actor Profile.

---

# 10. Training

---

## TERM-070 — Training

**Русский:** Курсы и тренинги / Обучение  
**Canonical:** `Training`

Отдельный домен профессионального образования.

---

## TERM-071 — Training Item

**Русский:** Запись обучения  
**Canonical:** `Training Item`

Отдельный:

- курс;
- тренинг;
- мастер-класс;
- интенсив;
- семинар;
- школа;
- лаборатория;
- повышение квалификации.

---

## TERM-072 — Ongoing Training

**Русский:** Текущее обучение  
**Canonical:** `Ongoing Training`

Training Item без завершённой end date, обозначенный как продолжающийся.

---

# 11. Skills and Languages

---

## TERM-080 — Skill

**Русский:** Навык  
**Canonical:** `Skill`

Структурированная профессиональная способность актрисы.

---

## TERM-081 — Skill Level

**Русский:** Уровень навыка  
**Canonical:** `Skill Level`

Уровень владения конкретным Skill по принятой продуктом шкале.

---

## TERM-082 — Language

**Русский:** Язык  
**Canonical:** `Language`

Структурированная запись владения языком.

---

## TERM-083 — Language Level

**Русский:** Уровень языка  
**Canonical:** `Language Level`

Уровень владения языком.

Для языков может использоваться CEFR.

---

## TERM-084 — CEFR

**Русский:** Общеевропейская шкала языковой компетенции  
**Canonical:** `CEFR`

Уровни:

```text
A1
A2
B1
B2
C1
C2
```

---

# 12. Professional Media

---

## TERM-090 — Video Intro

**Русский:** Видеовизитка  
**Canonical:** `Video Intro`

Основное краткое видео профессионального представления актрисы.

---

## TERM-091 — Showreel

**Русский:** Шоурил  
**Canonical:** `Showreel`

Монтаж профессиональных сцен, демонстрирующий опыт и диапазон.

---

## TERM-092 — Audio Sample

**Русский:** Аудиоматериал / Голосовой образец  
**Canonical:** `Audio Sample`

Профессиональная аудиозапись.

---

## TERM-093 — External Link

**Русский:** Внешняя ссылка  
**Canonical:** `External Link`

Структурированный объект:

- description;
- URL;
- order;
- visibility.

---

# 13. Contacts

---

## TERM-100 — Contact Directory

**Русский:** Справочник контактов  
**Canonical:** `Contact Directory`

Единый источник контактных данных профиля.

---

## TERM-101 — Contact

**Русский:** Контакт  
**Canonical:** `Contact`

Отдельная запись Contact Directory.

---

## TERM-102 — Primary Contact

**Русский:** Основной контакт  
**Canonical:** `Primary Contact`

Контакт, используемый как основной профессиональный способ связи.

---

## TERM-103 — Guardian Contact

**Русский:** Контакт родителя / представителя  
**Canonical:** `Guardian Contact`

Контакт родителя или законного/профессионального представителя, если применимо.

---

# 14. Visibility

---

## TERM-110 — Visibility

**Русский:** Видимость / Разрешение использования  
**Canonical:** `Visibility`

Политика, определяющая, в каком контексте объект может быть доступен.

---

## TERM-111 — Public Website Visibility

**Русский:** Видимость на публичном сайте  
**Canonical:** `Public Website Visibility`

Разрешение использовать объект в обычном Public Site.

---

## TERM-112 — Admin Questionnaire Availability

**Русский:** Доступность для администраторской анкеты  
**Canonical:** `Admin Questionnaire Availability`

Разрешение использовать объект в Prepared Questionnaire.

---

## TERM-113 — Public Builder Availability

**Русский:** Доступность для публичного конструктора  
**Canonical:** `Public Builder Availability`

Разрешение внешнему пользователю выбрать объект в Public Questionnaire Builder.

---

## TERM-114 — Private

**Русский:** Закрытый / Приватный  
**Canonical:** `Private`

Объект не доступен обычному публичному пользователю.

---

## TERM-115 — Public

**Русский:** Публичный  
**Canonical:** `Public`

Объект доступен в разрешённом публичном контексте.

Public не означает доступность во всех публичных сценариях.

---

# 15. Questionnaires

---

## TERM-120 — Questionnaire

**Русский:** Актёрская анкета  
**Canonical:** `Questionnaire`

Долгоживущая бизнес-сущность профессионального документа.

Questionnaire содержит identity/configuration lifecycle, но конкретное содержимое фиксируется Revision.

---

## TERM-121 — Prepared Questionnaire

**Русский:** Подготовленная анкета  
**Canonical:** `Prepared Questionnaire`

Questionnaire, созданная и курируемая администратором.

---

## TERM-122 — Questionnaire Revision

**Русский:** Версия анкеты  
**Canonical:** `Questionnaire Revision`

Конкретная версия состава и настроек Questionnaire.

Published Revision immutable.

---

## TERM-123 — Compact Questionnaire

**Русский:** Компактная анкета  
**Canonical:** `Compact Questionnaire`

Краткая подготовленная профессиональная анкета.

---

## TERM-124 — Extended Questionnaire

**Русский:** Расширенная анкета  
**Canonical:** `Extended Questionnaire`

Подробная версия.

---

## TERM-125 — Casting Questionnaire

**Русский:** Анкета под кастинг  
**Canonical:** `Casting Questionnaire`

Questionnaire, предназначенная для конкретной кастинговой возможности.

---

## TERM-126 — Custom Questionnaire

**Русский:** Пользовательская анкета  
**Canonical:** `Custom Questionnaire`

Администраторская анкета с произвольной структурой.

Не путать с временным документом Public Builder.

---

## TERM-127 — Questionnaire Block

**Русский:** Блок анкеты  
**Canonical:** `Questionnaire Block`

Логическая секция Questionnaire.

Например:

- Skills;
- Projects;
- Contacts.

---

## TERM-128 — Primary Questionnaire

**Русский:** Основная публичная анкета  
**Canonical:** `Primary Questionnaire`

Prepared Questionnaire, выбранная как основная рекомендуемая версия на Public Site.

---

# 16. Public Questionnaire Builder

---

## TERM-130 — Public Questionnaire Builder

**Русский UI:** Собрать свою анкету / Конструктор анкеты  
**Canonical:** `Public Questionnaire Builder`

Публичный инструмент, позволяющий кастинг-специалисту сформировать собственное представление из разрешённых профессиональных данных.

---

## TERM-131 — Public Questionnaire Template

**Русский:** Шаблон публичной анкеты  
**Canonical:** `Public Questionnaire Template`

Предварительная конфигурация Public Builder.

Типы:

- Quick;
- Standard;
- Extended;
- Casting.

---

## TERM-132 — Quick Template

**Русский:** Быстрый шаблон  
**Canonical:** `Quick Template`

Минимальный вариант для быстрого shortlist.

---

## TERM-133 — Standard Template

**Русский:** Стандартный шаблон  
**Canonical:** `Standard Template`

Сбалансированный вариант.

---

## TERM-134 — Extended Template

**Русский:** Расширенный шаблон  
**Canonical:** `Extended Template`

Максимально полный публично разрешённый состав.

---

## TERM-135 — Casting Template

**Русский:** Шаблон для кастинга  
**Canonical:** `Casting Template`

Template, допускающий контекст:

- Casting Name;
- Project;
- Role.

---

## TERM-136 — Public Questionnaire Session

**Русский:** Сессия конструктора анкеты  
**Canonical:** `Public Questionnaire Session`

Временная пользовательская конфигурация Public Builder.

Не является Prepared Questionnaire.

---

## TERM-137 — Custom PDF

**Русский:** Индивидуально сформированный PDF  
**Canonical:** `Custom PDF`

PDF, созданный через Public Questionnaire Builder.

---

## TERM-138 — Builder Selection

**Русский:** Выбор пользователя  
**Canonical:** `Builder Selection`

Набор блоков и items, выбранных пользователем в рамках Public Questionnaire Session.

---

# 17. PDF

---

## TERM-140 — Questionnaire PDF

**Русский:** PDF-анкета  
**Canonical:** `Questionnaire PDF`

PDF-представление Questionnaire или Public Questionnaire Session.

---

## TERM-141 — PDF Snapshot

**Русский:** Снимок данных PDF  
**Canonical:** `PDF Snapshot`

Фиксированный набор данных, используемый для генерации одного конкретного PDF.

---

## TERM-142 — Download Picker

**Русский:** Выбор варианта анкеты  
**Canonical:** `Download Picker`

Публичный интерфейс выбора доступной Prepared Questionnaire и языка перед скачиванием.

---

## TERM-143 — QR Code

**Русский:** QR-код  
**Canonical:** `QR Code`

Машиночитаемое представление разрешённого публичного URL.

---

# 18. Casting

---

## TERM-150 — Casting

**Русский:** Кастинг / Кастинговая возможность  
**Canonical:** `Casting`

Входящая профессиональная возможность, для которой могут существовать:

- требования;
- сроки;
- роль;
- source materials;
- анализ;
- анкета;
- outcome.

Casting не является Project.

---

## TERM-151 — Casting Source

**Русский:** Исходные материалы кастинга  
**Canonical:** `Casting Source`

Первичная информация о Casting:

- text;
- image;
- screenshot;
- PDF;
- incoming Feedback.

---

## TERM-152 — Casting Asset

**Русский:** Вложение кастинга  
**Canonical:** `Casting Asset`

Private media/document, связанный с Casting.

---

## TERM-153 — Casting Requirement

**Русский:** Требование кастинга  
**Canonical:** `Casting Requirement`

Структурированное требование, извлечённое или введённое из Casting Source.

---

## TERM-154 — Profile Match

**Русский:** Сопоставление с профилем  
**Canonical:** `Profile Match`

Результат сравнения Casting Requirement с существующим Profile Master Data.

---

## TERM-155 — Casting Recommendation

**Русский:** Рекомендация по кастингу  
**Canonical:** `Casting Recommendation`

Предложение системы или ИИ о:

- составе анкеты;
- материалах;
- блоках;
- дальнейших действиях.

Не является фактом.

---

# 19. AI terminology

---

## TERM-160 — AI Extraction

**Русский:** Извлечение данных ИИ  
**Canonical:** `AI Extraction`

Структурированное извлечение информации из Source.

---

## TERM-161 — AI Interpretation

**Русский:** Интерпретация ИИ  
**Canonical:** `AI Interpretation`

Вывод, сформированный AI на основании Source, который может требовать проверки.

---

## TERM-162 — AI Recommendation

**Русский:** Рекомендация ИИ  
**Canonical:** `AI Recommendation`

Предложение действия или selection.

---

## TERM-163 — Human Decision

**Русский:** Решение пользователя  
**Canonical:** `Human Decision`

Подтверждённое человеком решение в отношении AI Recommendation.

---

## TERM-164 — Confidence

**Русский:** Уверенность анализа  
**Canonical:** `Confidence`

Оценка надёжности AI Extraction.

Минимально:

- High;
- Medium;
- Low;
- Manual Review.

---

## TERM-165 — Human-in-the-loop

**Русский:** Человек в контуре принятия решения  
**Canonical:** `Human-in-the-loop`

Архитектурный принцип, при котором AI формирует предложение, а фактическое или публикационное решение принимает человек.

---

# 20. BB Assistant

---

## TERM-170 — BB Assistant

**Русский UI:** ИИ-помощник / BB Assistant  
**Canonical:** `BB Assistant`

Административный AI-инструмент для подготовки профессиональных текстов.

Может помогать создавать и редактировать:

- biography;
- project description;
- portfolio description;
- Training description;
- cover letter;
- casting response;
- professional message;
- social copy.

---

## TERM-171 — AI Draft

**Русский:** Черновик ИИ  
**Canonical:** `AI Draft`

Текст, созданный BB Assistant и требующий human review.

AI Draft не публикуется и не применяется автоматически.

---

## TERM-172 — Apply Draft

**Русский:** Применить черновик  
**Canonical:** `Apply Draft`

Явное действие администратора, после которого AI Draft переносится в соответствующее редактируемое поле.

---

# 21. Feedback / Communication

---

## TERM-180 — Feedback

**Русский:** Обращение / Входящее сообщение  
**Canonical:** `Feedback`

Входящее сообщение через публичный профессиональный канал.

Не использовать Feedback как синоним пользовательского отзыва о дизайне.

---

## TERM-181 — Professional Inquiry

**Русский:** Профессиональное обращение  
**Canonical:** `Professional Inquiry`

Feedback профессионального характера.

---

## TERM-182 — Casting Invitation

**Русский:** Приглашение на кастинг  
**Canonical:** `Casting Invitation`

Тип Professional Inquiry.

Само сообщение ещё не является Casting entity.

---

## TERM-183 — Role Offer

**Русский:** Предложение роли  
**Canonical:** `Role Offer`

Тип Feedback/Professional Inquiry.

---

## TERM-184 — Request Materials

**Русский:** Запрос дополнительных материалов  
**Canonical:** `Request Materials`

Professional Inquiry о предоставлении дополнительного professional content.

---

## TERM-185 — Feedback Attachment

**Русский:** Вложение обращения  
**Canonical:** `Feedback Attachment`

Private file, прикреплённый к Feedback.

---

## TERM-186 — Internal Note

**Русский:** Внутренняя заметка  
**Canonical:** `Internal Note`

Административный комментарий, недоступный внешнему отправителю.

---

# 22. Notifications

---

## TERM-190 — Notification

**Русский:** Уведомление  
**Canonical:** `Notification`

Процессная сущность, сообщающая о событии.

---

## TERM-191 — Internal Notification

**Русский:** Внутреннее уведомление  
**Canonical:** `Internal Notification`

Уведомление внутри административной системы.

---

## TERM-192 — External Notification

**Русский:** Внешнее уведомление  
**Canonical:** `External Notification`

Notification через внешнюю систему, например WhatsApp.

---

## TERM-193 — Notification Delivery

**Русский:** Доставка уведомления  
**Canonical:** `Notification Delivery`

Попытка доставки Notification через конкретный channel/recipient.

---

## TERM-194 — Quiet Hours

**Русский:** Период тишины  
**Canonical:** `Quiet Hours`

Временной период, когда внешняя доставка может быть отложена.

---

# 23. Opportunity Pipeline

---

## TERM-200 — Opportunity

**Русский:** Профессиональная возможность  
**Canonical:** `Opportunity`

Бизнес-потенциал, связанный с Casting или другим профессиональным предложением.

---

## TERM-201 — Opportunity Stage

**Русский:** Этап возможности  
**Canonical:** `Opportunity Stage`

Текущая стадия профессионального процесса.

---

## TERM-202 — Qualified

**Русский:** Квалифицированная возможность  
**Canonical:** `Qualified`

Opportunity признана реальной и релевантной.

---

## TERM-203 — Self-Tape

**Русский:** Самопробы  
**Canonical:** `Self-Tape`

Запрошенная или отправленная самостоятельная видео-проба.

---

## TERM-204 — Audition

**Русский:** Пробы / Прослушивание  
**Canonical:** `Audition`

Формальный этап кастинга.

---

## TERM-205 — Callback

**Русский:** Повторный вызов  
**Canonical:** `Callback`

Повторное приглашение после первичного этапа.

---

## TERM-206 — Offer

**Русский:** Предложение  
**Canonical:** `Offer`

Формальное предложение участия.

---

## TERM-207 — Booked

**Русский:** Роль подтверждена / Получена  
**Canonical:** `Booked`

Положительный конечный бизнес-результат Opportunity.

---

# 24. Virtual Operator

---

## TERM-210 — Virtual Portfolio Operator

**Русский:** Виртуальный оператор  
**Canonical:** `Virtual Portfolio Operator`

Внутренний программный помощник для обслуживания профессионального профиля.

Допустимое сокращение:

```text
Virtual Operator
```

---

## TERM-211 — Observation

**Русский:** Наблюдение  
**Canonical:** `Observation`

Обнаруженный Virtual Operator факт о состоянии системы.

Пример:

```text
Showreel link is unavailable.
```

---

## TERM-212 — Suggestion

**Русский:** Предложение  
**Canonical:** `Suggestion`

Рекомендация Virtual Operator.

---

## TERM-213 — Action Proposal

**Русский:** Предложенное действие  
**Canonical:** `Action Proposal`

Конкретное действие, которое Virtual Operator предлагает выполнить.

---

## TERM-214 — Exception-Driven Administration

**Русский:** Управление по исключениям  
**Canonical:** `Exception-Driven Administration`

Принцип, при котором администратор получает прежде всего информацию о состояниях, требующих его решения.

---

## TERM-215 — Content Freshness

**Русский:** Актуальность контента  
**Canonical:** `Content Freshness`

Оценка того, требуется ли профессиональному материалу проверка или обновление.

Не означает автоматическую непригодность старого материала.

---

# 25. Themes / Presentation

---

## TERM-220 — Site Theme

**Русский:** Тема сайта / Оформление сайта  
**Canonical:** `Site Theme`

Конфигурация визуального представления публичного сайта.

---

## TERM-221 — Theme Revision

**Русский:** Версия темы  
**Canonical:** `Theme Revision`

Версионированная конфигурация Site Theme.

---

## TERM-222 — Design Token

**Русский:** Токен оформления  
**Canonical:** `Design Token`

Структурированный параметр визуальной системы.

Примеры:

- color;
- radius;
- shadow;
- spacing role.

---

## TERM-223 — Hero

**Русский:** Первый экран / Hero-блок  
**Canonical:** `Hero`

Главная верхняя секция публичной страницы.

---

## TERM-224 — Theme Proposal

**Русский:** Предложение оформления  
**Canonical:** `Theme Proposal`

Draft visual configuration, сформированная AI или пользователем.

---

## TERM-225 — Locked Token

**Русский:** Зафиксированный параметр  
**Canonical:** `Locked Token`

Design Token, который AI regeneration не имеет права менять.

---

## TERM-226 — Temporary Theme

**Русский:** Временная тема  
**Canonical:** `Temporary Theme`

Site Theme с ограниченным периодом действия.

---

## TERM-227 — Fallback Theme

**Русский:** Резервная тема  
**Canonical:** `Fallback Theme`

Theme, которая активируется после окончания Temporary Theme или при предусмотренном rollback scenario.

---

# 26. Search / Discovery

---

## TERM-230 — Public Search

**Русский:** Поиск по профилю  
**Canonical:** `Public Search`

Поиск по разрешённым профессиональным данным профиля.

---

## TERM-231 — Search Projection

**Русский:** Поисковое представление  
**Canonical:** `Search Projection`

Derived representation публично разрешённых searchable facts.

---

## TERM-232 — Casting Quick View

**Русский:** Быстрый профессиональный просмотр  
**Canonical:** `Casting Quick View`

Компактное агрегированное представление наиболее важных данных для первичной оценки актрисы.

Не является самостоятельным Source of Truth.

---

# 27. Customer Journey

---

## TERM-240 — Customer Journey

**Русский:** Пользовательский путь  
**Canonical:** `Customer Journey`

Последовательность взаимодействий конкретной Persona с продуктом для достижения результата.

---

## TERM-241 — Persona

**Русский:** Персона  
**Canonical:** `Persona`

Архетип реального пользователя с конкретной задачей, контекстом и ограничениями.

---

## TERM-242 — Touchpoint

**Русский:** Точка взаимодействия  
**Canonical:** `Touchpoint`

Конкретный момент взаимодействия Persona с продуктом.

---

## TERM-243 — Entry Point

**Русский:** Точка входа  
**Canonical:** `Entry Point`

Первый экран/страница, через которую пользователь начинает Journey.

---

## TERM-244 — Pain Point

**Русский:** Проблемная точка  
**Canonical:** `Pain Point`

Сложность или friction, мешающий достижению цели.

---

## TERM-245 — Desired Outcome

**Русский:** Желаемый результат  
**Canonical:** `Desired Outcome`

Результат, ради которого Persona использует продукт.

---

## TERM-246 — Friction

**Русский:** Трение / Лишнее препятствие  
**Canonical:** `Friction`

Необязательное действие или сложность, увеличивающая путь пользователя.

---

# 28. UX

---

## TERM-250 — CTA

**Русский:** Целевое действие / Кнопка действия  
**Canonical:** `Call to Action`, `CTA`

Интерфейсный элемент, предлагающий пользователю следующее значимое действие.

---

## TERM-251 — Primary CTA

**Русский:** Основное действие  
**Canonical:** `Primary CTA`

Главное действие в текущем пользовательском контексте.

---

## TERM-252 — Secondary CTA

**Русский:** Дополнительное действие  
**Canonical:** `Secondary CTA`

Второстепенное, но релевантное действие.

---

## TERM-253 — Contextual CTA

**Русский:** Контекстное действие  
**Canonical:** `Contextual CTA`

CTA, название и тип которого соответствуют профессиональному намерению пользователя.

Примеры:

- Invite to Casting;
- Request Materials;
- Offer Role.

---

## TERM-254 — Progressive Disclosure

**Русский:** Постепенное раскрытие информации  
**Canonical:** `Progressive Disclosure`

Принцип, согласно которому сначала показывается наиболее важная информация, а детали раскрываются при необходимости.

---

## TERM-255 — No Dead Ends

**Русский:** Отсутствие тупиковых страниц  
**Canonical:** `No Dead Ends`

Принцип, согласно которому ключевая профессиональная страница предлагает логичное дальнейшее действие.

---

## TERM-256 — Mobile-First

**Русский:** Приоритет мобильного сценария  
**Canonical:** `Mobile-First`

Подход, при котором ключевой UX полностью работоспособен на мобильном устройстве.

---

# 29. Marketing / Analytics

---

## TERM-260 — Conversion

**Русский:** Конверсия  
**Canonical:** `Conversion`

Целевое действие пользователя, имеющее продуктовую или бизнес-ценность.

---

## TERM-261 — Primary Conversion

**Русский:** Основная конверсия  
**Canonical:** `Primary Conversion`

Например:

- Casting Invitation;
- Role Offer;
- Qualified Inquiry.

---

## TERM-262 — Secondary Conversion

**Русский:** Вторичная конверсия  
**Canonical:** `Secondary Conversion`

Например:

- PDF Download;
- Video Play;
- Custom PDF.

---

## TERM-263 — Conversion Funnel

**Русский:** Воронка конверсии  
**Canonical:** `Conversion Funnel`

Последовательность этапов от первого взаимодействия до профессионального результата.

---

## TERM-264 — Attribution

**Русский:** Атрибуция источника  
**Canonical:** `Attribution`

Определение канала или source, приведшего пользователя или Opportunity.

---

## TERM-265 — Analytics Event

**Русский:** Событие аналитики  
**Canonical:** `Analytics Event`

Техническая запись взаимодействия пользователя с продуктом.

Не является бизнес-фактом сама по себе.

---

## TERM-266 — KPI

**Русский:** Ключевой показатель эффективности  
**Canonical:** `KPI`

Измеримый показатель эффективности продукта.

---

# 30. Lifecycle States

---

## TERM-270 — Draft

**Русский:** Черновик  
**Canonical:** `Draft`

Редактируемое состояние, не предназначенное для обычного публичного использования.

---

## TERM-271 — Ready

**Русский:** Готово  
**Canonical:** `Ready`

Состояние, в котором обязательные business validations выполнены и объект готов к публикации или использованию.

---

## TERM-272 — Published

**Русский:** Опубликовано  
**Canonical:** `Published`

Разрешённое публичное или production-active состояние.

---

## TERM-273 — Archived

**Русский:** Архив  
**Canonical:** `Archived`

Исторически сохраняемое состояние объекта, исключённого из обычных новых selections.

---

## TERM-274 — Stale

**Русский:** Устаревшая производная версия  
**Canonical:** `Stale`

Derived Content более не соответствует текущему Source/Configuration и требует пересборки.

Не означает, что Source является устаревшим профессионально.

---

## TERM-275 — Invalid

**Русский:** Невалидно  
**Canonical:** `Invalid`

Состояние, в котором объект нарушает blocking business rule.

---

# 31. Revision terminology

---

## TERM-280 — Revision

**Русский:** Ревизия / Версия  
**Canonical:** `Revision`

Нумерованная версия долгоживущей бизнес-сущности.

---

## TERM-281 — Current Revision

**Русский:** Текущая версия  
**Canonical:** `Current Revision`

Последняя рабочая Revision.

---

## TERM-282 — Published Revision

**Русский:** Опубликованная версия  
**Canonical:** `Published Revision`

Revision, используемая production/public representation.

---

## TERM-283 — Immutable

**Русский:** Неизменяемый  
**Canonical:** `Immutable`

Состояние, при котором запись не изменяется непосредственно после фиксации.

---

## TERM-284 — Rollback

**Русский:** Откат  
**Canonical:** `Rollback`

Создание нового Current/Published состояния на основе ранее сохранённой Revision.

Rollback не удаляет историю.

---

# 32. Migration

---

## TERM-290 — Migration

**Русский:** Миграция  
**Canonical:** `Migration`

Управляемое преобразование данных из старой структуры в новую.

---

## TERM-291 — Legacy Data

**Русский:** Наследуемые данные / Старые данные  
**Canonical:** `Legacy Data`

Существующие данные предыдущей модели.

---

## TERM-292 — Migration Mapping

**Русский:** Карта миграции  
**Canonical:** `Migration Mapping`

Связь:

```text
Legacy Source
→ Target Entity
```

---

## TERM-293 — Dry Run

**Русский:** Предварительный прогон  
**Canonical:** `Dry Run`

Анализ ожидаемого результата Migration без изменения production data.

---

## TERM-294 — Idempotent Migration

**Русский:** Идемпотентная миграция  
**Canonical:** `Idempotent Migration`

Migration, повторный запуск которой не создаёт duplicate или повреждение данных.

---

## TERM-295 — Provenance

**Русский:** Происхождение данных  
**Canonical:** `Provenance`

Информация о том, откуда произошла текущая сущность или значение.

---

# 33. Security

---

## TERM-300 — Public Data

**Русский:** Публичные данные  
**Canonical:** `Public Data`

Данные, разрешённые для конкретного публичного контекста.

---

## TERM-301 — Private Data

**Русский:** Закрытые данные  
**Canonical:** `Private Data`

Данные, не предназначенные для обычной публичной передачи.

---

## TERM-302 — PII

**Русский:** Персонально идентифицируемая информация  
**Canonical:** `Personally Identifiable Information`, `PII`

Данные, позволяющие прямо или косвенно идентифицировать человека.

---

## TERM-303 — Data Minimization

**Русский:** Минимизация данных  
**Canonical:** `Data Minimization`

Принцип передачи и хранения только действительно необходимых данных.

---

## TERM-304 — Access Control

**Русский:** Контроль доступа  
**Canonical:** `Access Control`

Правила определения, кто может видеть или изменять объект.

---

## TERM-305 — Ownership Check

**Русский:** Проверка принадлежности  
**Canonical:** `Ownership Check`

Проверка того, что сущность относится к текущему разрешённому Profile/context.

---

# 34. Reliability / Technical Behaviour

---

## TERM-310 — Readiness Check

**Русский:** Проверка готовности  
**Canonical:** `Readiness Check`

Проверка набора условий, необходимых для перевода объекта в Ready/Published.

---

## TERM-311 — Blocking Error

**Русский:** Блокирующая ошибка  
**Canonical:** `Blocking Error`

Ошибка, не позволяющая завершить бизнес-действие.

---

## TERM-312 — Warning

**Русский:** Предупреждение  
**Canonical:** `Warning`

Проблема, которая требует внимания, но не обязательно блокирует действие.

---

## TERM-313 — Idempotency

**Русский:** Идемпотентность  
**Canonical:** `Idempotency`

Свойство операции, при котором безопасное повторение не создаёт нежелательный дополнительный результат.

---

## TERM-314 — Optimistic Locking

**Русский:** Оптимистическая блокировка  
**Canonical:** `Optimistic Locking`

Защита от silent overwrite при одновременном редактировании.

---

## TERM-315 — Cache Invalidation

**Русский:** Инвалидация кеша  
**Canonical:** `Cache Invalidation`

Принудительное признание кешированного representation устаревшим после изменения Source/Visibility.

---

# 35. Audit

---

## TERM-320 — Audit Event

**Русский:** Событие аудита  
**Canonical:** `Audit Event`

Неизменяемая запись значимого административного действия.

---

## TERM-321 — Audit Trail

**Русский:** Журнал аудита / История действий  
**Canonical:** `Audit Trail`

Последовательность Audit Events, позволяющая восстановить историю изменения.

---

# 36. Архитектурные термины

---

## TERM-330 — Domain

**Русский:** Домен  
**Canonical:** `Domain`

Логическая область бизнес-функциональности.

Примеры:

- Portfolio Domain;
- Casting Domain;
- Questionnaire Domain.

---

## TERM-331 — Entity

**Русский:** Сущность  
**Canonical:** `Entity`

Domain object с устойчивой идентичностью.

---

## TERM-332 — Value Object

**Русский:** Объект-значение  
**Canonical:** `Value Object`

Domain concept, определяемый значением, а не идентичностью.

---

## TERM-333 — Aggregate

**Русский:** Агрегат  
**Canonical:** `Aggregate`

Группа Domain Objects с общей consistency boundary.

---

## TERM-334 — Aggregate Root

**Русский:** Корень агрегата  
**Canonical:** `Aggregate Root`

Entity, через которую должны выполняться изменения внутри Aggregate.

---

## TERM-335 — Invariant

**Русский:** Инвариант  
**Canonical:** `Invariant`

Бизнес-условие, которое должно оставаться истинным для валидного состояния Aggregate.

---

## TERM-336 — Domain Event

**Русский:** Доменное событие  
**Canonical:** `Domain Event`

Значимое событие, произошедшее в Domain.

Пример:

```text
QuestionnairePublished
```

---

## TERM-337 — Application Service

**Русский:** Прикладной сервис  
**Canonical:** `Application Service`

Слой координации use case, который вызывает Domain operations и инфраструктурные зависимости.

---

# 37. Что НЕ следует называть одинаково

Следующие пары MUST различаться.

---

## Project ≠ Casting

Project — подтверждённый профессиональный опыт.

Casting — потенциальная будущая возможность.

---

## Portfolio ≠ Emotional Portfolio

Portfolio — профессиональное представление внешности.

Emotional Portfolio — отдельная фотосессия эмоционального диапазона.

---

## Emotional Portfolio ≠ Emotional Grid

Emotional Portfolio — Source Collection.

Emotional Grid — Composite Derived Presentation.

---

## Media Asset ≠ Portfolio Item

Media Asset — файл.

Portfolio Item — профессиональный контекст файла.

---

## Questionnaire ≠ Questionnaire Revision

Questionnaire — долгоживущая identity.

Revision — конкретная версия.

---

## Prepared Questionnaire ≠ Public Questionnaire Session

Prepared Questionnaire — постоянная администраторская сущность.

Public Questionnaire Session — временный выбор внешнего пользователя.

---

## Public Template ≠ Questionnaire

Template — набор defaults/rules.

Questionnaire — конкретный профессиональный документ.

---

## Feedback ≠ Casting

Feedback — входящее сообщение.

Casting — обработанная профессиональная возможность.

---

## AI Recommendation ≠ Human Decision

Recommendation — предложение.

Decision — подтверждение человека.

---

## Published ≠ Public

Published — lifecycle state.

Public — visibility context.

Объект может быть Published, но не доступен в конкретном Public context.

---

## Stale ≠ Outdated Professional Content

Stale означает техническую несогласованность производного результата.

Content Freshness означает необходимость профессионально проверить актуальность материала.

---

# 38. Запрещённые неоднозначные термины

Без уточнения SHOULD NOT использоваться:

```text
материал
карточка
документ
портфолио-фото
кастомная анкета
версия
статус
публикация
```

В техническом документе необходимо уточнять:

```text
Media Asset
Portfolio Item
Questionnaire PDF
Public Questionnaire Session
Questionnaire Revision
Opportunity Stage
Published Revision
```

---

# 39. UI Naming Guidelines

Для публичного интерфейса предпочтительны естественные названия.

| Canonical | Public RU |
|---|---|
| Portfolio | Портфолио |
| Emotional Portfolio | Эмоциональное портфолио |
| Emotional Grid | Эмоциональный ряд |
| Questionnaire | Актёрская анкета |
| Public Questionnaire Builder | Собрать свою анкету |
| Casting Invitation | Пригласить на кастинг |
| Request Materials | Запросить материалы |
| Training | Курсы и тренинги |

---

# 40. Admin Naming Guidelines

В административной панели использовать максимально однозначные названия:

```text
Портфолио
Эмоциональное портфолио
Эмоциональный ряд
Проекты
Курсы и тренинги
Актёрские анкеты
Кастинги
Контакты
Обратная связь
Уведомления
Оформление сайта
```

---

# 41. ID Naming Conventions

Для документации рекомендуется использовать:

```text
BR-*    Business Rule
IA-*    Information Architecture
TERM-*  Terminology
FR-*    Functional Requirement
UF-*    User Flow
CJM-*   Customer Journey
AR-*    Architecture Requirement
SEC-*   Security Requirement
NFR-*   Non-Functional Requirement
MIG-*   Migration Requirement
UT-*    Unit Test
IT-*    Integration Test
E2E-*   End-to-End Test
AC-*    Acceptance Criterion
ADR-*   Architecture Decision Record
```

---

# 42. Терминологическая трассируемость

Любая новая сущность, вводимая в будущих документах, SHOULD:

1. использовать существующий TERM;
2. либо быть добавлена в Terminology;
3. не получать случайное новое название в отдельном модуле.

---

# 43. Правило изменения терминологии

Если canonical term уже используется в:

- Business Rules;
- IA;
- API;
- Tests;
- Issues;

он не должен переименовываться без:

1. документированного решения;
2. обновления всех ссылок;
3. migration terminology references при необходимости.

---

# 44. Deprecated Term

При замене старого термина использовать:

```text
Deprecated:
старое название

Use:
новое canonical название
```

Не переиспользовать старое название для другой сущности.

---

# 45. Legacy terminology

До миграции существующая реализация может использовать legacy names.

Например:

```text
Project(type=Other)
```

для Training.

В документации TO-BE необходимо использовать:

```text
Training Item
```

Legacy term упоминается только в migration context.

---

# 46. Основной словарь продукта

Краткая обязательная таблица:

| Русский | Canonical |
|---|---|
| Актёрский профиль | Actor Profile |
| Медиатека | Media Library |
| Медиафайл | Media Asset |
| Оригинал | Original Asset |
| Портфолио | Portfolio |
| Элемент портфолио | Portfolio Item |
| Фото в полный рост | Full Body |
| Крупный план | Close-Up |
| Эмоциональное портфолио | Emotional Portfolio |
| Эмоциональный ряд | Emotional Grid |
| Ячейка сетки | Grid Cell |
| Проект | Project |
| Роль | Role |
| Курсы и тренинги | Training |
| Навык | Skill |
| Язык | Language |
| Контакт | Contact |
| Актёрская анкета | Questionnaire |
| Версия анкеты | Questionnaire Revision |
| Основная анкета | Primary Questionnaire |
| Шаблон конструктора | Public Questionnaire Template |
| Конструктор анкеты | Public Questionnaire Builder |
| Сессия конструктора | Public Questionnaire Session |
| Кастинг | Casting |
| Требование кастинга | Casting Requirement |
| Сопоставление | Profile Match |
| Рекомендация ИИ | AI Recommendation |
| Решение пользователя | Human Decision |
| Обращение | Feedback |
| Приглашение на кастинг | Casting Invitation |
| Уведомление | Notification |
| Профессиональная возможность | Opportunity |
| Виртуальный оператор | Virtual Portfolio Operator |
| Тема сайта | Site Theme |
| Версия темы | Theme Revision |
| Снимок | Snapshot |
| Черновик | Draft |
| Готово | Ready |
| Опубликовано | Published |
| Архив | Archived |
| Устаревшая производная версия | Stale |

---

# 47. Definition of Terminology Consistency

Документ или реализация терминологически корректны, если:

1. Project не используется для Training.
2. Project не используется для Casting.
3. Portfolio не используется для Emotional Portfolio.
4. Media Asset не используется как синоним Portfolio Item.
5. Questionnaire не смешивается с Revision.
6. Prepared Questionnaire не смешивается с Public Builder Session.
7. Feedback не смешивается с Casting.
8. AI Recommendation не называется фактом.
9. Published и Public не используются как взаимозаменяемые состояния.
10. все новые ключевые domain concepts имеют canonical term.

---

# 48. Итоговый принцип терминологии

Весь проект должен использовать модель:

```text
ONE CONCEPT
    ↓
ONE CANONICAL TERM
    ↓
CONSISTENT USE
    ↓
PRODUCT
UX
DOMAIN
DATABASE
API
TESTS
GITHUB
```

Единая терминология является обязательным условием дальнейшей формализации Domain Model и автоматической трассируемости требований.