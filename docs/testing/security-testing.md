# SECURITY TESTING
## DOC-174 — authentication, authorization, XSS, CSRF, SSRF, uploads, secrets, tokens и provider security tests

**Статус:** Canonical / Required

---

## 1. Назначение

Документ задаёт обязательный security testing program для DOC-140…146.

> **Security tests must prove prohibited access and side effects, not merely verify that security middleware exists. Every trust boundary receives negative tests using hostile inputs, forged identifiers, replay and failure scenarios.**

---

## 2. Layers

```text
STATIC
UNIT POLICY
INTEGRATION
E2E AUTHORITY
DEPENDENCY/SECRET SCANNING
MANUAL REVIEW/PEN TEST
```

## 3. Authentication tests

- valid/invalid login;
- account enumeration resistance;
- rate limit;
- session rotation;
- cookie flags;
- expiry/revocation;
- logout-all;
- recovery expiry/single-use;
- disabled account;
- CSRF/login fixation scenarios.

## 4. Authorization matrix

For every critical endpoint test:

```text
anonymous
valid admin
wrong role
wrong profile
stale lifecycle
forged resource ID
token-scoped caller
```

## 5. Serialization leakage

Inspect raw network response/RSC/JSON for forbidden field names/values. Hidden fields must be absent, not only invisible.

Targets:

- Contacts;
- Builder eligibility;
- Help internal notes;
- Feedback private data;
- token URLs;
- notification endpoints.

## 6. IDOR/BOLA

Generate resource IDs from another profile/private class and attempt read/write/delete. Server must reject without existence leakage where relevant.

## 7. CSRF

Attempt state-changing requests from hostile origin/cross-site context, including logout/login-sensitive flows. GET/HEAD mutation tests verify none exist.

## 8. XSS

Payload corpus across:

- Feedback;
- Casting source;
- Help ticket;
- BB/AI output;
- captions/descriptions;
- imported legacy text.

Test HTML/attribute/URL/script-context vectors according to render surfaces.

## 9. Rich text/embed

Attempt scripts, event handlers, `javascript:` URLs, unsafe iframe/object, SVG payloads. Sanitization/allowlisted provider embedding must block execution.

## 10. CSP/headers

Automated production-like response checks:

- CSP present/expected directives;
- HSTS in HTTPS production config;
- `nosniff`;
- Referrer-Policy;
- private cache headers;
- no wildcard expansion unexpectedly.

## 11. SSRF

Controlled test endpoints cover:

```text
127.0.0.1
::1
RFC1918
link-local
metadata IP
private DNS
redirect public→private
DNS rebinding simulation where possible
non-http schemes
```

All blocked before protected content access.

## 12. Open redirect

Share/redirect endpoints reject arbitrary external destination query and resolve stored approved relation only.

## 13. Upload security

Test:

- extension/MIME mismatch;
- malware fixture/safe test signature where scanner available;
- oversized file;
- decompression bomb fixture;
- path traversal filename;
- unsafe SVG;
- polyglot candidate;
- executable;
- quarantine access;
- unauthorized download.

## 14. File path disclosure

Error responses/log snapshots must not expose `/data/kate-actor/...`, stack traces or secret storage paths publicly.

## 15. Token tests

- entropy/format sanity;
- wrong scope/resource;
- expired;
- revoked;
- replay where single-use;
- cache after revoke;
- token absent from analytics/log/referrer scenario.

## 16. QR/PDF

Scan generated artifacts for:

- Admin URL;
- filesystem URL;
- expired/unsafe signed URL;
- private token leakage beyond intended scope;
- mismatched hyperlink/QR.

## 17. Secrets scanning

CI scans repository/diffs for high-confidence secrets. Test build artifacts/client bundles for server-only env leakage where possible.

## 18. OAuth

Test:

- state mismatch;
- code replay;
- redirect URI tamper;
- scope validation;
- token secrecy;
- refresh/revoke;
- cross-profile/account mismatch.

## 19. Webhooks

- invalid/missing signature;
- replay/duplicate event;
- wrong account;
- oversized body;
- malformed schema;
- out-of-order status;
- attempt to mutate unrelated domain.

## 20. Provider unknown outcome

Simulate timeout after possible side effect. Assert no blind duplicate resend and reconciliation required.

## 21. AI prompt injection

Provide malicious source text requesting secrets/tools/system override. Assert model/application cannot access unrestricted DB/filesystem/network/provider and output stays within schema.

## 22. VOP privilege boundary

Attempt to call unregistered/prohibited safe action such as publish/change Skill/Booked. Must fail server-side regardless configuration/client payload.

## 23. SQL injection

ORM/parameterized queries tested with representative payloads on search/filter/input. No raw concatenated SQL from user values.

## 24. Rate limiting

Exercise thresholds for login, Feedback, Builder, analytics, invalid token/webhook. Verify legitimate records already committed are not lost because downstream rate/provider path fails.

## 25. Cache poisoning/leakage

Request Admin/token/public variants with same resource IDs and verify cache isolation. Host/header/query manipulation must not poison canonical links/SEO output.

## 26. Dependency scanning

Automated dependency vulnerability scan with severity policy. Findings reviewed for reachability/exposure, not ignored blindly.

## 27. Static analysis

Use TypeScript/lint/security tooling to detect dangerous patterns where useful:

- raw HTML;
- dynamic eval;
- unsafe child process;
- hardcoded secrets;
- path construction.

## 28. Manual threat review

High-risk changes trigger security review:

- auth/authz;
- upload/parser;
- public token;
- OAuth/provider;
- deletion/privacy;
- migrations.

## 29. Test evidence

Release retains machine-readable security test result/check summary, not secret payloads.

## 30. Anti-patterns

Forbidden:

1. Security test only checks 401 on `/admin` root.
2. Hidden data not inspected in raw response.
3. SSRF tested only with malformed URL.
4. OAuth tested only happy path.
5. Webhook signature code untested.
6. Real malware used unsafely in CI.
7. Security test logs tokens.
8. Dependency scan always non-blocking regardless critical issue.
9. AI prompt injection considered “model problem” without application boundary tests.
10. Cache access-class isolation untested.

## 31. Acceptance criteria

`AC-SECTEST-001` Authentication/session/recovery negative tests exist.  
`AC-SECTEST-002` Authorization matrix includes anonymous/role/profile/lifecycle/token cases.  
`AC-SECTEST-003` Hidden fields are asserted absent from raw responses.  
`AC-SECTEST-004` XSS/CSRF/SSRF/open-redirect tests are automated.  
`AC-SECTEST-005` Upload/path/quarantine security is tested.  
`AC-SECTEST-006` Token scope/expiry/revoke/cache behavior is tested.  
`AC-SECTEST-007` OAuth/webhook/unknown-provider-outcome cases are tested.  
`AC-SECTEST-008` AI/VOP privilege elevation attempts fail.  
`AC-SECTEST-009` Secret/dependency/static scans run in CI.  
`AC-SECTEST-010` High-risk changes receive manual threat review when needed.  
`AC-SECTEST-011` Security test outputs are themselves secret-safe.  
`AC-SECTEST-012` Critical security failures block release.

---

## 32. Финальная доктрина

> **Security testing attacks the actual trust boundaries with forged IDs, hostile content, replay, private-network targets, invalid signatures, stale tokens and ambiguous provider outcomes. A security control is accepted only when tests prove the forbidden action/data remains impossible across the real assembled stack.**
