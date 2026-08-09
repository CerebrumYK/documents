# AUTHENTICATION
## DOC-141 — Admin identity, sessions, login, recovery и MFA-ready architecture

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет authentication boundary для Admin-контура. Authentication отвечает только на вопрос **кто principal**, но не определяет, какие domain actions ему разрешены — это DOC-142.

> **Authenticated ≠ Authorized. Session proves an authenticated principal, while every privileged command still requires server-side authorization.**

---

## 2. Baseline principals

```text
ANONYMOUS
ADMIN_USER
SUPPORT_USER
SYSTEM_WORKER
PROVIDER_CALLBACK
TOKEN_SCOPED_RECIPIENT
```

`SYSTEM_WORKER`, provider callback и token-scoped recipient не являются browser Admin sessions.

## 3. Admin account

Минимальные поля:

```text
id
login/email identifier
credential state
status
last_login_at?
credential_changed_at?
session_generation
created_at
updated_at
```

Passwords, password hashes и recovery secrets никогда не сериализуются наружу.

## 4. Account states

```text
ACTIVE
LOCKED
DISABLED
RECOVERY_REQUIRED
```

Disabled/locked account не может создавать новую session. Existing sessions должны быть revocable.

## 5. Password policy

Если применяется password authentication:

- современный memory-hard/approved password hashing algorithm;
- unique per-password salt;
- no reversible encryption of password;
- no password logging;
- sufficiently long password support;
- запрет искусственного усечения long passphrases;
- compromised/default credentials запрещены;
- server-side login rate limit.

Password complexity policy не должна стимулировать предсказуемые шаблоны; длина и защита от compromised credentials важнее механического набора символов.

## 6. Session model

Preferred browser model: server-recognized session with opaque identifier in secure cookie.

Cookie baseline:

```text
HttpOnly
Secure in production
SameSite appropriate to application
narrow Domain/Path
bounded Max-Age/Expires
```

Session record SHOULD contain:

```text
id/hash
principal_id
created_at
last_seen_at
expires_at
absolute_expires_at
session_generation
revoked_at?
metadata_minimized
```

Raw session token should be stored hashed if plaintext recovery is unnecessary.

## 7. Session rotation

Session identifier rotates after:

- successful login;
- password/credential change;
- privilege elevation where relevant;
- MFA completion;
- account recovery.

This prevents session fixation.

## 8. Session expiry

Use both:

- inactivity/idle lifetime;
- absolute lifetime.

Expired session is rejected server-side even if browser still holds cookie.

## 9. Logout

Logout revokes server session and clears cookie.

`Logout all sessions` SHOULD increment session generation/revoke all active sessions.

## 10. Login flow

```text
POST login
 ↓
normalize identifier
 ↓
rate-limit check
 ↓
constant-behavior credential verification
 ↓
optional MFA challenge
 ↓
session creation/rotation
 ↓
audit security event
```

Do not reveal whether account exists through materially different public error messages/timing where avoidable.

## 11. Brute-force protection

Controls:

- account/IP/device-scope rate limits;
- exponential delays/backoff where appropriate;
- security telemetry;
- temporary lock only with policy that cannot be abused for trivial permanent denial-of-service;
- alerts for unusual failures.

## 12. MFA-ready architecture

Even if MFA is outside current runtime baseline, account/session schema and flows MUST permit later MFA without redesign.

Supported future factors may include TOTP/passkeys/security keys.

MFA secrets are `SECRET` class and never exposed after enrollment except required setup flow.

## 13. Recovery

Recovery token requirements:

- high entropy;
- single use;
- short bounded lifetime;
- hashed at rest where possible;
- invalidated after successful use;
- session rotation/revocation after credential reset;
- no password sent over email.

Generic response prevents account enumeration.

## 14. Email change

Changing login email/identity is security-sensitive and must require authenticated re-verification according to policy, audit, and session handling.

## 15. Session theft mitigation

- HTTPS only;
- Secure/HttpOnly cookies;
- CSP/XSS protections;
- no token in URL;
- no localStorage Admin token baseline;
- session revocation;
- optional anomaly telemetry.

IP/user-agent binding must not be so strict that normal network/browser changes cause unsafe lockouts; use as risk signal, not sole identity proof.

## 16. CSRF relationship

Cookie-authenticated mutations use CSRF/origin protections. Authentication endpoints themselves (login/logout/recovery) must be reviewed for login-CSRF/session fixation concerns.

## 17. Public Builder and share tokens

Builder session/token does **not** authenticate an Admin.

```text
BuilderSession ≠ AdminSession
ShareToken ≠ AdminSession
```

No endpoint may accept a token-scoped identifier as substitute for Admin principal.

## 18. OAuth provider accounts

Instagram/TikTok OAuth authenticates an external publishing account, not the Admin browser principal. Provider OAuth tokens cannot create Admin session.

## 19. Provider callbacks

Webhook signature identifies provider message authenticity within the integration contract; it does not create Admin session.

## 20. Worker identity

Background worker uses service/runtime identity and typed job authorization. Worker must not fabricate Admin identity; audit should distinguish system execution from Human request provenance.

## 21. Logging

Allowed:

```text
principal_id
session id hash/prefix only if needed
login result
reason code
request/correlation id
```

Forbidden:

- password;
- password hash;
- recovery token;
- full session token;
- MFA secret;
- cookie/Authorization header.

## 22. Audit events

At minimum:

```text
AdminLoginSucceeded
AdminLoginFailed (privacy-safe)
AdminLogout
AdminSessionsRevoked
CredentialChanged
RecoveryRequested
RecoveryCompleted
AccountLocked/Unlocked
MFAEnabled/Disabled (future)
```

## 23. Authentication anti-patterns

Forbidden:

1. Admin JWT/session token in localStorage baseline.
2. Password in logs or analytics.
3. Reversible password storage.
4. Infinite session lifetime.
5. Session ID unchanged after login/reset.
6. Logout only clears browser cookie without server revocation.
7. Recovery token reusable.
8. Recovery token in long-lived logs.
9. Error “user does not exist” exposed publicly when enumeration is avoidable.
10. OAuth social token reused as Admin auth.
11. Share token grants `/admin` access.
12. Provider webhook establishes Admin principal.
13. Worker action recorded as Human login.
14. MFA secret stored in client/browser after enrollment.
15. Password reset leaves all stolen sessions valid by default.

## 24. E2E cases

- valid Admin login creates secure session;
- invalid credential denied without useful account enumeration;
- repeated failures trigger rate policy;
- session cookie is HttpOnly/Secure in production;
- expired/revoked session denied;
- logout invalidates server session;
- password reset revokes/rotates sessions according to policy;
- recovery token single-use and expires;
- forged Builder/share token cannot open Admin route;
- social OAuth token cannot call Admin authenticated API;
- session rotation occurs after authentication;
- CSRF-protected mutation fails from hostile origin;
- disabled user cannot create/use session;
- logs contain no raw credentials.

## 25. Acceptance criteria

`AC-AUTH-001` Admin authentication uses server-validated sessions.  
`AC-AUTH-002` Session credentials are never exposed to client JavaScript baseline.  
`AC-AUTH-003` Sessions have idle/absolute expiry and server revocation.  
`AC-AUTH-004` Session rotates on login/credential change.  
`AC-AUTH-005` Login and recovery are rate-limited.  
`AC-AUTH-006` Recovery tokens are high-entropy, expiring and one-time.  
`AC-AUTH-007` Credential reset handles existing sessions securely.  
`AC-AUTH-008` Architecture is MFA-ready.  
`AC-AUTH-009` Builder/share/provider credentials never authenticate Admin.  
`AC-AUTH-010` Authentication/security logs contain no raw secrets.  
`AC-AUTH-011` Cookie-based state changes are CSRF-aware.  
`AC-AUTH-012` Automated E2E coverage validates login, logout, recovery, expiry, revocation and token-boundary cases.

---

## 26. Финальная доктрина

> **Admin identity is established only by the dedicated authentication flow and a server-recognized revocable session. Password/recovery/MFA/session secrets never become browser-readable application state, social OAuth and share tokens never become Admin credentials, and every session remains bounded, rotatable, revocable and auditable.**
