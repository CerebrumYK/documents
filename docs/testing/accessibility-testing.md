# ACCESSIBILITY TESTING
## DOC-176 — automated, keyboard, screen-reader, contrast и PDF accessibility verification

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет test program для DOC-152.

> **Automated accessibility tools detect only part of the problem. Release confidence requires automated checks plus keyboard, focus, responsive and screen-reader validation of critical journeys.**

---

## 2. Automated layer

Use axe-core/Playwright accessibility tooling or equivalent for representative pages/components.

Check:

- semantic violations;
- labels/names;
- contrast where tool supports;
- ARIA misuse;
- landmarks/headings;
- duplicate IDs;
- focusable hidden content.

## 3. Component tests

Interactive component coverage:

- buttons/links;
- dialogs;
- tabs;
- selects/autocomplete;
- carousels;
- media controls;
- upload controls;
- sortable/reorder UI;
- toasts/live regions.

## 4. Keyboard tests

Critical flows navigated using keyboard only:

```text
Tab / Shift+Tab
Enter / Space
Arrow keys where component pattern requires
Escape for dialogs
```

No mouse used to complete test.

## 5. Focus

Verify:

- focus visible;
- logical order;
- dialog focus entry/contain/return;
- error focus behavior;
- route/navigation focus where SPA transitions require management.

## 6. Screen-reader spot checks

Representative NVDA/VoiceOver or equivalent checks for:

- public Profile;
- Portfolio/media controls;
- Contact links;
- forms/errors;
- Builder;
- Admin complex flow;
- status/notification text.

## 7. Headings/landmarks

Automated DOM assertions ensure one meaningful page H1 and logical hierarchy. Main/nav landmarks identifiable.

## 8. Forms

Test label association, required state, error description, preserved input, error summary and focus.

## 9. Color/contrast

Theme token matrix is tested automatically for configured foreground/background/component states. Visual/actual browser spot checks cover gradients/images/dynamic overlays.

## 10. Color independence

Tests check status includes visible/programmatic text/icon label in addition to color.

## 11. Zoom/reflow

Manual/automated viewport checks at high browser zoom/text scaling where feasible. Ensure no clipped essential content/horizontal full-page overflow.

## 12. Reduced motion

Emulate `prefers-reduced-motion: reduce`; assert decorative animations disabled/reduced and functionality unchanged.

## 13. Touch targets

Responsive audit checks primary mobile controls are sufficiently targetable/spaced.

## 14. Portfolio

Keyboard gallery open/next/previous/close, meaningful alt, no focus loss. Full-source contain behavior does not create inaccessible controls.

## 15. Video/audio

Player controls have accessible names and keyboard support. No autoplay audio. Captions/transcript controls tested when feature/source present.

## 16. Carousel

Home swipe carousel includes actual previous/next controls and keyboard access; swipe is not sole interaction.

## 17. Emotional Grid editor

Admin crop/position has keyboard/numeric equivalent for core adjustments. Final grid preview remains understandable.

## 18. Builder

Template/item selection has labels; reorder/toggle status accessible; live preview does not steal focus; generation status announced appropriately.

## 19. QR

Verify nearby meaningful clickable link exists in HTML/PDF; alt describes QR purpose without requiring scan.

## 20. PDF

Automated/manual checks:

- text extraction works;
- links selectable/clickable;
- reading order visually/logically reasonable;
- contrast/font size acceptable;
- QR labels unambiguous;
- no screenshot-only text pages.

## 21. Analytics charts

Ensure table/text equivalent for key values and color-independent legend/labels.

## 22. Help/Admin timelines

Status/priority/read state accessible as text. Timeline order meaningful in DOM.

## 23. Theme regression

Every Theme publish candidate runs contrast and critical-page accessibility smoke. Theme AI cannot skip gate.

## 24. Browser matrix

Accessibility browser/device spot coverage includes at least:

- Chromium + keyboard;
- Safari/WebKit relevant mobile/VoiceOver path;
- one desktop screen-reader/browser combination.

## 25. Severity

Release blockers:

- core path impossible by keyboard;
- missing label on required form;
- focus trap;
- critical contrast violation;
- screen reader cannot access main professional content;
- essential QR/color-only information.

## 26. CI

Automated accessibility checks are blocking for new critical violations. Known baseline exceptions need issue/owner and should shrink over time.

## 27. Manual evidence

Release/high-risk design change checklist records keyboard/screen-reader/theme/PDF spot result.

## 28. Anti-patterns

Forbidden:

1. “axe passes, therefore accessible”.
2. Only desktop mouse testing.
3. Contrast checked only in design file, not rendered states.
4. Drag-only crop/reorder.
5. QR with no link alternative.
6. Screen-reader labels generated from raw IDs.
7. Focus outline hidden for visual style.
8. Accessibility snapshot approved without interaction testing.

## 29. Acceptance criteria

`AC-A11YTEST-001` Automated accessibility scan covers critical pages/components.  
`AC-A11YTEST-002` Keyboard-only critical journeys are tested.  
`AC-A11YTEST-003` Focus order/dialog/error behavior is verified.  
`AC-A11YTEST-004` Representative screen-reader spot tests exist.  
`AC-A11YTEST-005` Theme contrast states are machine-validated.  
`AC-A11YTEST-006` Reduced-motion and zoom/reflow behavior are tested.  
`AC-A11YTEST-007` Media/carousel/complex editor alternatives are verified.  
`AC-A11YTEST-008` PDF/QR accessibility is inspected.  
`AC-A11YTEST-009` Critical violations block release.  
`AC-A11YTEST-010` Manual accessibility evidence supplements automated scans.

---

## 30. Финальная доктрина

> **Accessibility testing proves interaction, not merely markup. Automated scanners guard common regressions, while keyboard, focus, screen-reader, responsive, theme and PDF checks demonstrate that real professional content and workflows remain operable without visual/pointer-only assumptions.**
