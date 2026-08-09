# COMPATIBILITY REQUIREMENTS
## DOC-153 — browsers, devices, responsive behavior, media/PDF compatibility и graceful degradation

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт compatibility matrix для публичного сайта и Admin, включая browser/device classes, responsive behavior, media, PDF/QR и external provider degradation.

> **Compatibility means the same professional truth and primary workflows remain available across supported environments; it does not require pixel-identical rendering or unsafe legacy-browser workarounds.**

---

## 2. Browser support policy

Baseline supports current stable and recent major versions of:

```text
Chrome / Chromium-based
Safari
Firefox
Edge
Mobile Safari (iOS)
Chrome on Android
```

Exact minimum versions are maintained in project tooling/browserslist and reviewed periodically rather than frozen forever in prose.

## 3. Unsupported legacy browsers

Internet Explorer and obsolete browsers without required modern web/security capabilities are not baseline targets.

Do not weaken CSP, JavaScript security or application architecture to support obsolete engines.

## 4. Progressive enhancement

Public professional content should remain readable/navigation-capable when optional client enhancements fail. Core public content should not require a large client-only app boot merely to display biography/portfolio links.

## 5. JavaScript dependency

Admin functionality can require JavaScript. Public site may use JS for interactive galleries/media/Builder, but server-rendered meaningful content is preferred for SEO/accessibility/resilience.

## 6. Responsive breakpoints

Use content-driven responsive design rather than device-name detection. Minimum classes:

```text
mobile
small tablet
large tablet/small desktop
desktop
wide desktop
```

Business logic must not depend on viewport class.

## 7. Orientation

Public/Admin workflows remain usable in portrait and landscape where practical. No forced orientation lock baseline.

## 8. Touch/pointer

Controls support touch and mouse/pointer. Hover-only actions require visible/tappable alternative.

## 9. High-DPI

Images/QR/icons render sharply on high-DPI screens using responsive derivatives/vector assets without forcing huge downloads on low-density devices.

## 10. Media format compatibility

Media pipeline should produce web-compatible derivatives appropriate to supported browsers. Original preservation is independent.

Use codec/container choices with broad support; provider-specific social outputs are separate.

If a source codec is unsupported, transcode derivative rather than rejecting professional original unnecessarily when supported pipeline can process it.

## 11. Image format

Modern optimized formats may be used with framework/browser fallback. Original asset format remains immutable.

## 12. PDF

Generated PDF should work in common desktop/mobile PDF viewers:

- selectable text;
- standard hyperlink annotations;
- embedded/reliable fonts where licensing permits;
- vector/high-resolution QR;
- no viewer-specific JavaScript requirement.

## 13. QR

QR scanning should work from common smartphone camera/scanner software. Print density/quiet-zone rules are format-independent.

## 14. External links

Links open through standard HTTPS semantics. No dependency on custom browser extensions/app protocol unless explicitly optional.

## 15. WhatsApp/contact links

Use standard normalized web/deep-link forms with fallback semantics. Public contact action failure on device without app must not expose secret or crash page.

## 16. Theme compatibility

All published Theme tokens/components tested across support matrix. Theme does not rely on one browser-specific CSS feature without fallback where essential.

## 17. CSS features

Modern CSS may be used if supported matrix covers it. Provide fallback for essential layout/readability when feature support differs.

## 18. Fonts

Provide fallback font stack. Failure to load custom font must not break layout/content.

## 19. Localization

Cyrillic and Latin glyphs required. Font selection must include all supported locale character sets.

## 20. Date/time

Use locale-aware display but unambiguous underlying timestamps. Browser timezone cannot silently alter scheduled social publication intent; explicit timezone persists server-side.

## 21. File upload compatibility

Admin uploader works with standard browser File API and handles mobile photo/video upload where supported by OS/browser. Do not require drag-and-drop as only mechanism.

## 22. Camera/mobile uploads

Mobile browser may provide HEIC/other formats. Product can reject unsupported formats with clear message or support server conversion according to Media policy; no silent corruption.

## 23. Clipboard

Clipboard convenience is optional. Core editing must not depend on clipboard API permission.

## 24. Share API

Web Share API may enhance mobile sharing, with standard link-copy fallback.

## 25. Download compatibility

PDF/media download uses valid `Content-Disposition`, UTF-8/sanitized filenames and standard HTTP semantics.

## 26. Network quality

Public pages should degrade on slower/mobile networks through responsive images/lazy media. Heavy media should not block core text/navigation.

## 27. Offline

Full offline/PWA is not baseline. Previously loaded static page/browser cache may work naturally, but no correctness claims for offline mutations.

## 28. External provider SDKs

Prefer server-side provider integration. If client SDK/embed used, failure is localized and core page remains functional with fallback link/message.

## 29. Browser security settings

Site must function under normal privacy controls. Analytics/ad/tracker blocking should not block professional content, Feedback persistence or Admin core.

## 30. Cookie restrictions

First-party Admin sessions must follow browser cookie rules. Third-party cookies must not be required for core authentication unless provider OAuth flow explicitly redirects and remains standards-compliant.

## 31. Accessibility compatibility

Compatibility testing includes keyboard/screen-reader behavior in representative browser/OS combinations, not only visual rendering.

## 32. Visual tolerance

Pixel differences in font rasterization/subpixel layout are acceptable when hierarchy, spacing, readability and interactions remain within design quality bounds.

## 33. Test matrix

At minimum release smoke tests:

```text
Chrome desktop
Safari desktop
Firefox desktop
Edge desktop
iPhone Safari viewport/device
typical Android Chrome viewport/device
```

Critical visual/public flows may use automated Playwright browser matrix plus physical/simulator spot checks.

## 34. Critical flows

Test:

- Profile/Portfolio;
- video/audio;
- Contact links;
- Questionnaire PDF;
- Builder;
- Admin login/forms/media upload;
- Theme preview;
- Feedback/Casting/Opportunity;
- Social schedule UI;
- Help Center.

## 35. Graceful fallback examples

- custom font fails → fallback readable font;
- Web Share unavailable → Copy Link;
- embed provider blocked → clickable ProfessionalLink;
- video autoplay blocked → normal play control;
- analytics blocked → content unaffected;
- social SDK unavailable → Admin provider flow remains server-side.

## 36. Compatibility anti-patterns

Forbidden:

1. “Works only in Chrome” without documented limitation.
2. Hover-only essential control.
3. Drag-and-drop only upload.
4. Custom font failure makes text invisible.
5. Client provider SDK required for basic Profile render.
6. Browser sniffing drives business rules.
7. Mobile viewport hides mandatory Questionnaire action.
8. PDF relies on viewer JavaScript.
9. QR rendered so small only one scanner works.
10. Third-party cookie required for own Admin session.
11. Tracker blocking breaks Feedback.
12. Legacy browser support achieved by disabling security controls.

## 37. Acceptance criteria

`AC-COMP-001` Supported browser policy is maintained in tooling and reviewed periodically.  
`AC-COMP-002` Public core content works across supported desktop/mobile browsers.  
`AC-COMP-003` Responsive behavior is content/viewport-driven, not device-name business logic.  
`AC-COMP-004` Touch, mouse and keyboard have usable primary controls.  
`AC-COMP-005` Media pipeline supplies broadly compatible web derivatives.  
`AC-COMP-006` PDF uses standard text/link/QR constructs.  
`AC-COMP-007` Optional browser/provider APIs have fallbacks.  
`AC-COMP-008` Custom-font/analytics/embed failure does not hide core professional content.  
`AC-COMP-009` Admin upload has non-drag fallback.  
`AC-COMP-010` Browser/privacy restrictions do not break core workflows.  
`AC-COMP-011` Critical flows run in automated representative browser matrix.  
`AC-COMP-012` No obsolete-browser workaround weakens security/privacy contracts.

---

## 38. Финальная доктрина

> **Supported browsers and devices receive equivalent professional content and core capabilities through standards-based HTML, media, PDF and server APIs. Enhancements may vary, but essential workflows never depend on one browser, hover, drag-only input, tracker, optional provider SDK or unsafe legacy workaround.**
