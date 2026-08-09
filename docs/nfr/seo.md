# SEO REQUIREMENTS
## DOC-154 — indexability, metadata, structured data, canonical URLs, sitemaps и privacy-safe discoverability

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет SEO requirements для публичной профессиональной части сайта.

> **SEO may improve discoverability only for already-public professional content. It never grants visibility, invents claims, indexes Admin/token-scoped data or rewrites professional truth for keywords.**

---

## 2. SEO scope

SEO applies to intended public pages such as:

- public Profile/home;
- public Portfolio/Emotional Portfolio;
- public Projects where enabled;
- public Training/Achievements where enabled;
- public professional media/link landing surfaces where applicable;
- current public Questionnaire page if product policy allows indexing.

Excluded:

- `/admin`;
- Feedback/Casting/Opportunity;
- Help Tickets;
- Builder temporary sessions;
- token-scoped/casting-safe resources;
- provider callback URLs;
- private media;
- internal search/results intended only for Admin.

## 3. Public-source rule

SEO output is derived from Public Projection. It must never query private Source fields merely for richer metadata.

### SEO-INV-001
If a fact is not public, it is not SEO metadata.

## 4. Titles

Each indexable page has unique descriptive `<title>` based on confirmed public facts. Avoid keyword stuffing and unsupported professional claims.

## 5. Meta description

Use concise public summary. BB Assistant may draft, but Human/publication validation applies and unsupported facts are forbidden.

## 6. Canonical URL

Every indexable page has stable canonical application URL. Canonical tags prevent duplicate locale/query/share variants from fragmenting indexing.

## 7. URL structure

URLs should be readable/stable and avoid exposing:

- database IDs where unnecessary;
- tokens;
- internal storage paths;
- Admin semantics.

Changing slug should preserve redirect/canonical policy where appropriate.

## 8. HTTPS

Canonical public URLs use HTTPS in production.

## 9. robots directives

Private/token/admin surfaces explicitly use non-indexable behavior as defense-in-depth, while actual access control remains server-side.

`noindex` is not security.

## 10. robots.txt

May guide crawlers away from irrelevant/private routes but cannot be relied on to protect secrets. Do not list sensitive token URLs individually.

## 11. Sitemap

Generate sitemap from current public/indexable projection only.

Include appropriate:

- canonical URL;
- last modification based on meaningful public content change where available;
- locale alternates if supported.

Archived/private pages are removed on rebuild.

## 12. Locales/hreflang

If localized public pages exist, use valid language/region alternates and self-referential canonical/hreflang policy. Missing translation should not produce fake duplicate machine content automatically unless product intentionally provides fallback.

## 13. Structured data

JSON-LD may describe Person/creative work/profile using schema.org types appropriate to actual confirmed public facts.

Structured data must not claim:

- awards not confirmed;
- representation not public;
- skills/languages not visible;
- project roles stronger than Source.

## 14. Achievements semantics

`OFFICIAL_SELECTION`, `NOMINATED`, `FINALIST`, `WINNER` remain distinct in SEO copy/structured data. No `award-winning` from nomination/selection only.

## 15. Images

Public image SEO uses allowed derivatives, descriptive alt/context and stable URLs. Do not expose original storage filenames/path as SEO strategy.

## 16. Open Graph

Public pages should provide OG title/description/image based on public confirmed content.

Preview crawler requests count as bots in Analytics, not human engagement.

## 17. Social metadata

Twitter/X-style card metadata may be supported as generic social preview mechanism even without publishing integration. No private provider credentials involved.

## 18. Hero image

OG image selection is Human/configuration-controlled or deterministic public-safe default; Analytics does not auto-replace based on clicks baseline.

## 19. Video metadata

Where public video exists, page may expose safe metadata/link. Do not embed raw private media URL or ephemeral storage signature in structured data.

## 20. Link semantics

Internal navigation uses real anchors/crawlable links where appropriate. Do not make all navigation JS-only click handlers.

## 21. External links

Professional external links are normal links. `nofollow`/sponsored/etc only where semantically appropriate; do not blanket-hide legitimate professional links without reason.

## 22. Performance relationship

SEO public pages must meet performance requirements. Largest images/media optimized. Core content server-renderable where suitable.

## 23. Accessibility relationship

SEO does not override accessibility: headings, alt text and link names remain human-meaningful rather than keyword-stuffed.

## 24. Duplicate content

Potential duplicates:

- query parameters;
- share wrappers;
- alternate PDF/HTML;
- locale fallback;
- old slugs.

Use canonical/redirect/noindex according to semantic target.

## 25. Questionnaire PDFs

PDF may be publicly downloadable, but web indexability is policy-specific. PDF should not become preferred duplicate over canonical profile unnecessarily. HTTP headers/linking can express intended index behavior.

## 26. Token-scoped PDF

Never add token-scoped artifact to sitemap/structured data. `robots` is supplemental; access control is mandatory.

## 27. Search page indexing

Internal site search result pages are generally not primary SEO targets. Avoid crawler traps/infinite query combinations.

## 28. Pagination

If public collections paginate, each page has valid crawlable navigation/canonical strategy. Avoid inaccessible infinite-scroll-only content for important pages.

## 29. 404/410/redirect

- deleted/never-existing → correct 404;
- deliberately permanently removed may use 410 when policy supports;
- moved public page → appropriate permanent redirect;
- unauthorized private resource should not reveal unnecessary existence.

## 30. Archive

Archived Project/Achievement public URL behavior is explicit: remove from sitemap, redirect only if meaningful canonical replacement exists, otherwise noindex/404/410 according to policy.

## 31. Dynamic theme

Theme changes must not remove semantic headings/main content/metadata. Visual Theme cannot rewrite professional SEO facts.

## 32. Analytics

Measure organic/referral traffic privacy-safely, but SEO metrics never auto-publish/hide content.

## 33. Search Console/tools

Optional operations may use external webmaster tools. Credentials belong secrets/config, not page code/docs.

## 34. SEO testing

Automated checks should validate:

- title/meta presence;
- canonical URL;
- robots directives;
- sitemap entries;
- private routes not indexable;
- structured data parse;
- duplicate/redirect basics;
- OG image URL validity;
- no token/storage path leaks.

## 35. Anti-patterns

Forbidden:

1. Private field inserted into meta description.
2. Admin page in sitemap.
3. Token URL in sitemap.
4. `noindex` used as only privacy control.
5. Fake `award-winning` keyword.
6. Hidden SEO text/keyword stuffing.
7. Structured data claims unsupported skills/awards.
8. Canonical points to Admin/internal URL.
9. OG image is private storage signed URL.
10. Share tracking URL becomes canonical unintentionally.
11. Preview bot counted as human conversion.
12. Theme removes semantic page content.
13. Browser-only rendering leaves no meaningful core content where SSR feasible.
14. SEO AI auto-publishes generated claims.

## 36. Acceptance criteria

`AC-SEO-001` SEO reads only public confirmed projections.  
`AC-SEO-002` Indexable pages have stable HTTPS canonical URLs and meaningful title/description.  
`AC-SEO-003` Admin/private/token-scoped surfaces are excluded from sitemap/indexing and protected independently.  
`AC-SEO-004` Sitemap is generated from current public eligibility.  
`AC-SEO-005` Localization/hreflang preserves real language versions.  
`AC-SEO-006` Structured data preserves exact professional semantics.  
`AC-SEO-007` OG/social previews use public-safe stable assets.  
`AC-SEO-008` Share/tracking URLs do not silently replace canonical URL.  
`AC-SEO-009` Archive/delete/move uses explicit redirect/404/noindex policy.  
`AC-SEO-010` SEO never changes Source/visibility based on ranking metrics automatically.  
`AC-SEO-011` Automated SEO checks prevent token/private/storage-path leakage.  
`AC-SEO-012` Performance/accessibility remain constraints on SEO implementation.

---

## 37. Финальная доктрина

> **SEO is a projection of already-public professional truth. Canonical URLs, sitemap, structured data and social metadata improve discoverability without exposing private/token-scoped resources, inventing professional claims or turning ranking metrics into publication authority.**
