# ACCESSIBILITY REQUIREMENTS
## DOC-152 — WCAG-oriented requirements for Public Site, Admin, media, forms, PDF и QR

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт accessibility contract проекта для публичного сайта, Admin, Questionnaire/Builder, Media и generated documents.

> **Accessibility is a release requirement, not post-release decoration. All primary professional information and actions must be perceivable, operable and understandable without relying solely on mouse, hover, color, sound or QR scanning.**

---

## 2. Target standard

Baseline target: WCAG 2.2 Level AA for applicable web UI patterns, with pragmatic documentation of exceptions caused by third-party provider limitations.

## 3. POUR model

Requirements grouped by:

```text
Perceivable
Operable
Understandable
Robust
```

## 4. Semantic HTML

Use native semantic elements before custom ARIA:

- headings in logical hierarchy;
- buttons for actions;
- anchors for navigation;
- lists/tables where semantic;
- form labels;
- landmarks (`header`, `nav`, `main`, `footer`).

### A11Y-INV-001
ARIA does not replace correct native semantics.

## 5. Keyboard access

Every interactive control must be usable by keyboard.

No keyboard traps. Modal/dialog focus is contained and returned appropriately. Drag/reorder has keyboard alternative.

## 6. Focus visibility

Visible focus indicator required. Theme cannot remove focus outline without equivalent stronger replacement.

## 7. Focus order

DOM/focus order matches logical visual/content order. CSS reordering cannot create confusing keyboard sequence.

## 8. Skip navigation

Public/Admin pages with repeated navigation provide skip-to-main mechanism where appropriate.

## 9. Headings

Pages have meaningful H1 and hierarchical headings. Visual typography does not determine semantic heading level automatically.

## 10. Images

Professional images require meaningful alt strategy:

- informative portfolio photo: concise contextual alt;
- purely decorative element: empty alt/hidden;
- avoid stuffing appearance descriptions unrelated to purpose.

Alt text must not fabricate personal attributes.

## 11. Emotional Grid

Composite image should have meaningful accessible description and link to full Emotional Portfolio when used in Questionnaire. Accessibility does not require exposing individual photos that product contract intentionally excludes.

## 12. Video

Video player must be keyboard operable. Controls labeled. No autoplay audio. Where spoken/dialogue content is professionally important, captions/transcript should be supported according to available source and publication requirements.

## 13. Audio

Audio player has labeled play/pause/progress/volume controls and does not auto-play unexpectedly.

## 14. Color contrast

Theme validation must enforce WCAG AA contrast for normal/large text and essential UI components according to applicable criteria.

Color is not sole indicator for:

- errors;
- status;
- priority;
- selected state;
- charts.

## 15. Text resizing/reflow

Content remains usable at browser zoom/text enlargement and responsive reflow. Avoid fixed-height text clipping.

## 16. Responsive/mobile

Touch targets should be adequately sized/spaced. Primary navigation/forms work in portrait mobile without horizontal page scrolling for normal content.

## 17. Forms

Every field has programmatic label. Placeholder is not sole label.

Required/optional status is conveyed in text/semantics.

## 18. Validation errors

Error:

- associated with field;
- described textually;
- summary/focus management for complex forms;
- does not rely only on red border;
- preserves entered safe values where appropriate.

## 19. Admin editors

Complex controls (crop, reorder, theme editor, builder) provide keyboard-accessible or equivalent numeric/form controls for core actions.

For image crop, keyboard/numeric alternative may manipulate scale/position rather than requiring pointer-only drag.

## 20. Dialogs/toasts

Dialogs use accessible name, focus trap and close mechanism. Important async errors are announced through appropriate live-region semantics without excessive chatter.

## 21. Loading/progress

Long jobs provide textual status, not spinner only. Status changes should be announced when meaningful.

## 22. Tables

External links editor exact columns `№ / Описание / URL` uses semantic table/grid behavior appropriate to editing and accessible labels.

## 23. QR

QR is supplementary.

HTML/PDF includes human-readable/clickable link near QR. QR image gets purpose-oriented alt/label.

### A11Y-INV-002
No essential content/action is available only by scanning QR.

## 24. PDF

Generated Questionnaire should:

- contain selectable text;
- clickable hyperlinks;
- logical reading order as renderer permits;
- actual text instead of screenshot-only pages;
- sufficient contrast/font size;
- labels near QR/media links.

Tagged PDF is desirable if renderer stack supports reliable tagging; inability to tag does not justify screenshot PDF.

## 25. Language attributes

HTML document uses correct `lang`. Localized content sections may use appropriate language markup when different from page language.

## 26. Link text

Avoid repeated ambiguous “click here”. Use meaningful names:

```text
Открыть видеовизитку
Скачать анкету PDF
Открыть showreel
```

## 27. New window/external behavior

Do not unexpectedly open new windows unless needed; if new context is used consistently, accessible cue is recommended.

## 28. Reduced motion

Respect `prefers-reduced-motion`. Theme animation/motion must have reduced/disabled alternative for non-essential effects.

## 29. Flashing

No content flashes at unsafe frequency.

## 30. Carousels

Home swipe carousel must provide:

- keyboard controls;
- previous/next labeled buttons;
- understandable current position if appropriate;
- no forced auto-rotation baseline, or pause controls if ever introduced.

## 31. Charts/Analytics

Charts have textual metric/table equivalent. Color palettes retain contrast and are not sole encoding.

## 32. Status badges

Examples:

```text
P1_CRITICAL
FAILED
PUBLISHED
```

must have text labels, not icon/color alone.

## 33. Help Center

Articles use logical headings/lists/code blocks and accessible search/results. Support timeline is readable without visual-only connectors.

## 34. AI output

Generated text is plain semantic text until explicitly formatted safely. AI cannot emit inaccessible arbitrary HTML widgets.

## 35. Theme system

Theme tokens have accessibility validation before publish/activation. Theme AI recommendations cannot override contrast/focus/readability checks.

## 36. Testing

Required layers:

- automated accessibility lint/scanner;
- component tests;
- keyboard manual/smoke tests;
- screen-reader spot checks on critical workflows;
- contrast validation;
- PDF/QR manual/automated inspection.

Automated scanner alone is insufficient.

## 37. Critical journeys

Must be tested:

- public navigation/profile/portfolio;
- media playback;
- Contact actions;
- public Questionnaire/PDF;
- Builder create/generate;
- Admin login;
- Admin Profile/Media edit;
- Questionnaire editor;
- Casting/Feedback/Opportunity workflow;
- Theme editor;
- Help Tickets;
- Social Draft/schedule.

## 38. Accessibility severity

Blocking examples:

- core action impossible by keyboard;
- form without accessible labels;
- severe contrast failure;
- modal traps focus;
- essential information only in color/QR;
- public content hidden from screen reader.

## 39. Anti-patterns

Forbidden:

1. Clickable `div` without keyboard semantics.
2. Placeholder-only label.
3. Focus outline removed.
4. Error only by red color.
5. QR only way to open media.
6. Autoplay audio.
7. Carousel pointer-only.
8. Theme sacrifices contrast for branding.
9. PDF is screenshots of text.
10. Icon-only unlabeled admin buttons.
11. Drag-and-drop with no alternative.
12. Motion ignores reduced-motion.
13. Chart no textual alternative.
14. Automated scanner treated as complete accessibility validation.

## 40. Acceptance criteria

`AC-A11Y-001` Applicable UI targets WCAG 2.2 AA.  
`AC-A11Y-002` All primary controls are keyboard operable.  
`AC-A11Y-003` Focus is visible and logical.  
`AC-A11Y-004` Forms have labels and programmatically associated errors.  
`AC-A11Y-005` Images/media use appropriate alternatives/accessible controls.  
`AC-A11Y-006` No essential action relies only on color, hover, sound or QR.  
`AC-A11Y-007` Themes pass contrast/focus/readability validation.  
`AC-A11Y-008` Generated PDF retains selectable text, links and scan-safe labeled QR.  
`AC-A11Y-009` Complex editors expose keyboard/equivalent controls.  
`AC-A11Y-010` Reduced-motion preference is respected.  
`AC-A11Y-011` Critical journeys receive automated plus manual accessibility testing.  
`AC-A11Y-012` Blocking accessibility defects block release.

---

## 41. Финальная доктрина

> **Professional content and workflows remain usable across keyboard, screen reader, zoom, mobile and reduced-motion contexts. Themes, media, PDF and advanced editors preserve semantics and alternatives, while QR, color and visual layout remain supplementary rather than exclusive communication channels.**
