# VISUAL REGRESSION TESTING
## DOC-177 — screenshot baselines, theme/layout states, responsive matrices и review policy

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет визуальные regression tests для public/admin critical surfaces, Theme system и generated presentation states.

> **Visual regression detects unintended presentation drift, but screenshots never replace semantic, accessibility or business-rule assertions. A pixel match cannot prove that a hidden field is secure or a button is usable.**

---

## 2. Scope

Priority surfaces:

- home/Profile Hero;
- Main Portfolio galleries;
- Emotional Portfolio/Grid;
- Project cards/details;
- Questionnaire HTML preview;
- Public Builder;
- Admin dashboard/forms;
- Theme preview;
- Feedback/Casting/Opportunity lists/details;
- Social Draft preview;
- Help Center/Ticket timeline.

## 3. Baseline environments

Fix:

- browser engine/version in CI image;
- viewport;
- device scale factor where possible;
- fonts;
- locale;
- timezone;
- seeded fixture data;
- animation disabled/reduced;
- deterministic dates/random content.

## 4. Viewport matrix

At minimum representative:

```text
mobile portrait
mobile landscape/small
large tablet
standard desktop
wide desktop for key public layouts
```

Not every page needs every size; matrix is risk-based.

## 5. Theme testing

Every Theme candidate/active baseline checks:

- typography hierarchy;
- contrast visually/automatically;
- spacing;
- buttons/forms;
- cards/galleries;
- focus states;
- mobile layout;
- overlays/dialogs.

## 6. Content fixtures

Include edge cases:

- long RU/EN names/text;
- missing optional field;
- long URL;
- portrait/landscape images;
- multiple achievement/status badges;
- validation errors;
- loading/empty states.

## 7. Original aspect ratio

Portfolio visual tests specifically guard `contain`/no unintended crop policy where required by Admin Guide.

## 8. Emotional Grid

Baseline validates exact cell layout and intended crop transforms using fixed images/config. Screenshot does not determine face percentage rule; semantic config/test handles that.

## 9. Dynamic content masking

Mask only truly nondeterministic non-semantic regions. Excessive masking that hides real regressions is forbidden.

## 10. Threshold

Use low tolerance appropriate to browser rendering; review diffs rather than huge permissive percentage. Threshold documented in test config.

## 11. Baseline update

Screenshot baseline update is an explicit reviewed change. Never auto-approve all new images after test failure.

PR should make visual diff inspectable where tooling supports it.

## 12. Component visual tests

Useful for reusable design-system components and Theme token states, reducing page-level noise.

## 13. Page visual tests

Used for layout composition/real content relationships.

## 14. Error/empty states

Capture important states:

- no data;
- failed media;
- validation error;
- offline provider warning;
- stale/blocked state.

## 15. Dark/light

Only if product supports both; do not invent modes not in product. Dynamic Theme variants are tested according to supported configuration.

## 16. Localization

At least RU and EN representative public/admin pages where both locales exist, to catch overflow and font/glyph issues.

## 17. PDF visual regression

Generated PDF pages may be rasterized and compared for representative Questionnaire variants, but semantic PDF tests remain separate.

Visual test guards:

- pagination;
- clipping;
- QR placement/size;
- typography;
- page breaks.

## 18. QR

Visual diff can detect missing/overlapped QR but exact decode remains integration/E2E requirement.

## 19. Media player

Screenshot stable poster/control state rather than active video frames.

## 20. Animations

Freeze/disable animations before screenshots; animation behavior tested separately.

## 21. Browser differences

Primary screenshot baseline may use Chromium; targeted WebKit/Firefox visual smoke catches major differences. Pixel-identical cross-browser output is not required.

## 22. Accessibility relation

Visual tests include focus/error states but do not replace accessibility tests.

## 23. Security relation

A visually hidden private field may still leak; visual tests do not count as security proof.

## 24. Performance

Screenshot suite should not load giant media unnecessarily; use representative optimized fixtures.

## 25. CI policy

Critical visual unexpected diff blocks/requests review. Low-risk non-critical diffs may be warning according to branch/release policy.

## 26. Anti-patterns

Forbidden:

1. Huge threshold hides regressions.
2. Auto-update snapshots in CI.
3. Mask half the page.
4. Random dates/data create constant diffs.
5. Screenshot used to prove accessibility.
6. Screenshot used to prove hidden-data security.
7. Only desktop snapshots.
8. PDF visual test replaces hyperlink/QR decode assertions.
9. Custom font missing in CI makes baseline meaningless.
10. Baselines updated without review.

## 27. Acceptance criteria

`AC-VIS-001` Critical public/Admin layouts have deterministic visual baselines.  
`AC-VIS-002` Responsive matrix includes mobile and desktop.  
`AC-VIS-003` Theme changes exercise reusable components and key pages.  
`AC-VIS-004` Fixtures include long/missing/error/content edge cases.  
`AC-VIS-005` Portfolio no-unintended-crop behavior is visually guarded.  
`AC-VIS-006` Screenshot baseline changes require review.  
`AC-VIS-007` Masking/tolerance remain narrow and documented.  
`AC-VIS-008` PDF visual pagination/QR placement is tested separately from semantic PDF assertions.  
`AC-VIS-009` Localization layout regression is covered.  
`AC-VIS-010` Visual tests never substitute security/accessibility/business tests.

---

## 28. Финальная доктрина

> **Visual regression provides deterministic evidence that intended hierarchy, spacing, media framing and responsive layouts did not drift. Its authority ends at presentation: semantic correctness, access control, accessibility, links and QR decoding remain separate test contracts.**
