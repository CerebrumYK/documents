# CONTACTS MODULE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная спецификация Contacts Directory, contact methods, parent/representative relationships, contextual visibility и safe clickable actions

**Целевой файл:** `docs/modules/contacts.md`  
**Документ:** DOC-119  
**Статус:** ✅ Completed  
**Тип:** Module / Contacts / Professional Communication / Privacy / Visibility

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/product-overview.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/scope.md`
- `docs/product/product-principles.md`
- `docs/domain/domain-model.md`
- `docs/domain/entity-relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions-and-history.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/components.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`
- `docs/architecture/cache-strategy.md`
- `docs/architecture/search.md`
- `docs/architecture/ai-architecture.md`
- `docs/architecture/notifications.md`
- `docs/database/database-architecture.md`
- `docs/database/data-dictionary.md`
- `docs/database/visibility-and-access.md`
- `docs/database/validation-and-constraints.md`
- `docs/database/deletion-and-retention.md`
- `docs/api/server-actions.md`
- `docs/api/contracts.md`
- `docs/api/errors.md`
- `docs/api/idempotency.md`
- `docs/modules/profile.md`
- `docs/modules/professional-media-links.md`

---

# 1. Назначение модуля

Contacts Module является единым Master Source профессиональных контактных данных, доступных в:

- Public Profile;
- Contact section;
- Casting CTA;
- prepared Questionnaire;
- Public Questionnaire Builder;
- PDF;
- professional sharing;
- Casting workflow;
- Feedback/Inquiry handoff.

Модуль должен позволять хранить и безопасно использовать:

```text
phone
email
WhatsApp
Telegram
social profile
agency/manager contact
parent/guardian contact
other approved professional contact method
```

---

# 2. Главная доктрина

> **Contact Directory отвечает за то, с кем и каким способом можно связаться. Visibility определяет, где конкретный контакт разрешено показывать. Admin notification recipients являются отдельной operational configuration и никогда не выводятся из Public Contacts автоматически.**

Canonical:

```text
Contact Person / Contact Role
            ↓
       Contact Methods
            ↓
 Contextual Visibility
   ┌────────┼─────────┐
   ▼        ▼         ▼
Public   Admin QNR   Builder
```

---

# 3. Fundamental separation

```text
Contact
≠ ContactMethod
≠ Public CTA
≠ Feedback
≠ NotificationRecipient
≠ Admin User
≠ ProfessionalLink
```

---

# 4. Module identifiers

Используются:

```text
CNT-*
CNT-PER-*
CNT-MTH-*
CNT-REL-*
CNT-VIS-*
CNT-URL-*
CNT-QNR-*
CNT-BLD-*
CNT-PRV-*
CNT-AI-*
CNT-INV-*
CNT-AP-*
E2E-CNT-*
```

---

# 5. Business purpose

Контактный слой должен обеспечивать профессиональному посетителю быстрый переход от решения:

```text
актриса подходит
```

к действию:

```text
пригласить на кастинг
запросить материалы
обсудить роль
связаться с представителем
```

---

# 6. Action budget

Canonical customer journey:

```text
Contact ≤ 2 actions
```

от ключевого профиля/CTA.

---

# 7. Contacts Directory

Recommended model separates:

```text
ContactEntry
```

and:

```text
ContactMethod
```

---

# 8. ContactEntry

Описывает **кого** представляет контакт.

Representative fields:

```text
id
profile_id
contact_role
display_name
organization?
note?
is_primary?
lifecycle_state
display_order
version
created_at
updated_at
archived_at
```

---

# 9. ContactMethod

Описывает **как** связаться.

Representative fields:

```text
id
contact_entry_id
method_type
value
normalized_value
display_value
label?
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
is_primary_for_method?
display_order
version
created_at
updated_at
archived_at
```

---

# 10. Why two entities

Один представитель может иметь:

```text
phone
email
WhatsApp
Telegram
```

без дублирования его имени/роли четыре раза.

---

# 11. CNT-INV-001 — Contact Person ≠ Contact Method

---

# 12. Contact roles

Controlled baseline:

```text
ACTRESS
PARENT
MOTHER
FATHER
GUARDIAN
AGENT
MANAGER
REPRESENTATIVE
AGENCY
OTHER
```

---

# 13. ACTRESS

Direct professional contact of actress.

---

# 14. PARENT / MOTHER / FATHER

Explicit family/parental representation.

---

# 15. GUARDIAN

Legal/professional guardian relationship where relevant.

---

# 16. AGENT

Professional talent agent.

---

# 17. MANAGER

Professional manager.

---

# 18. REPRESENTATIVE

Other confirmed representative.

---

# 19. AGENCY

Organization-level contact.

---

# 20. OTHER

Only where none of the controlled roles is correct.

---

# 21. CNT-REL-001 — Parent Relationship Must Be Explicit

Нельзя хранить:

```text
"Мама: +..."
```

только как неструктурированную строку.

---

# 22. Why

Structured relation is needed for:

- labels;
- Questionnaire;
- Builder;
- international localization;
- privacy;
- contact routing.

---

# 23. Parent labels

Localized rendering may show:

```text
Мама
Отец
Родитель
Опекун
```

or English equivalents.

Canonical enum remains language-neutral.

---

# 24. Contact name

`display_name` is Human-confirmed.

---

# 25. CNT-INV-002 — Relationship Does Not Infer Legal Authority

`MOTHER`, `FATHER`, `PARENT` or `GUARDIAN` represent configured contact context.

System must not make legal assumptions beyond explicitly stored permissions/business rules.

---

# 26. Contact methods

Canonical baseline:

```text
PHONE
EMAIL
WHATSAPP
TELEGRAM
INSTAGRAM
FACEBOOK
VK
WEBSITE
OTHER_SOCIAL
OTHER
```

---

# 27. Professional media link boundary

Social/contact profile belongs Contacts when it primarily serves as:

```text
contact identity / communication channel
```

A showreel/media destination belongs Professional Media & Links.

---

# 28. CNT-INV-003 — Same URL Must Have One Clear Source Owner

Avoid duplicated independent truth in both Contacts and ProfessionalLinks.

---

# 29. Phone Source

Phone must be stored in normalized form separately from display format.

---

# 30. Preferred phone normalization

Canonical:

```text
E.164
```

where number supports it.

Example conceptually:

```text
+77001234567
```

---

# 31. Display format

Localized/human-readable representation may be:

```text
+7 700 123 45 67
```

---

# 32. CNT-MTH-001 — Display Formatting ≠ Stored Identity

---

# 33. `tel:` action

Clickable phone URL:

```text
tel:+77001234567
```

---

# 34. CNT-URL-001 — Phone CTA Uses Normalized Number

---

# 35. WhatsApp

WhatsApp contact action uses normalized phone identity.

---

# 36. Canonical web link

Conceptually:

```text
https://wa.me/<digits>
```

with digits from normalized international number.

---

# 37. Example

Stored:

```text
+77001234567
```

WhatsApp destination:

```text
https://wa.me/77001234567
```

---

# 38. CNT-URL-002 — `wa.me` Target Is Derived

Do not maintain separately editable WhatsApp URL when WhatsApp is phone-based.

---

# 39. Why

Avoid:

```text
phone changed
WhatsApp old
```

inconsistency.

---

# 40. WhatsApp-specific number

If WhatsApp uses another number, model it as separate ContactMethod.

---

# 41. CNT-INV-004 — Same Human Can Have Multiple Distinct Methods

---

# 42. WhatsApp prefilled text

May be supported later.

If implemented:

- encoded safely;
- never includes private/internal data by default;
- generated from approved template;
- not authoritative Source.

---

# 43. Email

Normalized for comparison/delivery while preserving display address semantics.

---

# 44. Clickable email

```text
mailto:name@example.com
```

---

# 45. CNT-URL-003 — Email CTA Uses Safe `mailto:` Construction

---

# 46. Email query parameters

Avoid arbitrary untrusted:

```text
subject
body
cc
bcc
```

from URL input.

If supported, generate from controlled template.

---

# 47. Telegram

Can support:

```text
https://t.me/username
```

when public username exists.

---

# 48. Telegram phone exposure

Do not generate public Telegram contact from phone unless explicitly configured.

---

# 49. Instagram/social

Store canonical profile URL or canonical username + provider.

---

# 50. Preferred

Provider-specific normalization where stable.

---

# 51. CNT-URL-004 — Social URL Must Pass Safe URL Validation

No:

```text
javascript:
file:
localhost
admin route
```

---

# 52. Website contact

HTTPS destination preferred.

---

# 53. Contact value vs link

For some methods:

```text
value = phone/email/username
href = derived
```

For generic social/website:

```text
value/canonical_url
```

may be same destination.

---

# 54. CNT-MTH-002 — Derived Link Is Not Independent Source

---

# 55. Contact labels

Optional contextual label.

Examples:

```text
Для кастингов
Агент
WhatsApp
Рабочая почта
```

---

# 56. Label cannot contradict role

Do not rely on free-text label instead of structured `contact_role`.

---

# 57. Primary Contact

Product MAY define one preferred primary contact for prominent CTA.

---

# 58. `is_primary`

Human-controlled presentation/routing configuration.

---

# 59. CNT-INV-005 — At Most One Primary Contact Entry

Recommended per profile if global primary semantics are enabled.

---

# 60. Primary Contact Method

Within one ContactEntry, may designate:

```text
preferred method
```

for CTA.

---

# 61. Example

Primary contact:

```text
Mother
```

Preferred method:

```text
WhatsApp
```

---

# 62. Global CTA

Can resolve:

```text
Primary Contact
→ Preferred eligible ContactMethod
```

---

# 63. CNT-INV-006 — Contact CTA Is Projection

No duplicated phone/URL in Profile Hero.

---

# 64. Primary does not imply public

A primary Admin contact can remain non-public.

---

# 65. CNT-INV-007 — Primary ≠ Visibility

---

# 66. Visibility ownership

Visibility belongs **ContactMethod**, because one method may be public while another method for the same person is private.

---

# 67. Example

Manager:

```text
email → Public
personal phone → Admin Questionnaire only
```

---

# 68. CNT-VIS-001 — Visibility Is Per Method

---

# 69. Canonical visibility triplet

Each ContactMethod:

```text
show_on_public_site
allow_in_admin_questionnaires
allow_in_public_questionnaire_builder
```

---

# 70. CNT-VIS-002 — Three Dimensions Independent

---

# 71. Valid combination

```text
Public=true
AdminQ=true
Builder=true
```

---

# 72. Valid combination

```text
Public=false
AdminQ=true
Builder=false
```

---

# 73. Valid combination

```text
Public=false
AdminQ=false
Builder=true
```

where intentional.

---

# 74. CNT-VIS-003 — Builder Permission Does Not Imply Public Site Permission

---

# 75. Hidden contacts

Hard doctrine:

> **Hidden ContactMethod must be removed server-side before serialization.**

---

# 76. CNT-PRV-001 — No Client-Side Hiding

Prohibited:

```text
send phone to browser
→ CSS display:none
```

---

# 77. Public DTO

May contain only methods satisfying current Public policy.

---

# 78. Builder DTO

May contain only methods satisfying current Builder policy.

---

# 79. Admin Questionnaire DTO

May contain methods satisfying Admin Questionnaire policy plus Admin authorization.

---

# 80. CNT-INV-008 — Surface Projection Is Separate

---

# 81. ContactEntry with zero visible methods

Should not appear in Public Contact projection.

---

# 82. Why

Avoid exposing:

```text
Mother
```

without any usable contact method unless there is explicit business reason.

---

# 83. Contact privacy

Contacts can include personal data.

Therefore:

- public exposure is explicit;
- no implicit publication;
- no raw contact values in unnecessary logs;
- analytics minimized;
- token/public routing separated.

---

# 84. CNT-PRV-002 — Publicness Must Be Explicit

---

# 85. Save vs Publish

A newly created ContactMethod should not automatically become public.

---

# 86. Recommended lifecycle

```text
DRAFT
ACTIVE
ARCHIVED
```

or equivalent.

---

# 87. `ACTIVE`

Means valid current contact record.

It still requires surface permission.

---

# 88. CNT-INV-009 — Active ≠ Public

---

# 89. Archive

Removes method from current use.

Historical Questionnaires preserve frozen data unless privacy/access policy requires redaction.

---

# 90. Restore

Preferred:

```text
ARCHIVED → DRAFT/ACTIVE review
```

without automatically restoring old visibility.

---

# 91. CNT-INV-010 — Restore Does Not Auto-Expose

---

# 92. Public Contact section

Should show concise professional contact options.

---

# 93. Recommended structure

```text
Связаться
→ Contact person / role
→ phone / WhatsApp / email / approved social
```

---

# 94. Direct actress contact

May be shown only when explicitly permitted.

---

# 95. Representative contact

Can be preferred CTA where configured.

---

# 96. Parent contact

Should be clearly labeled by relationship.

---

# 97. CNT-REL-002 — Do Not Present Parent Number as Actress Number

---

# 98. International rendering

Example:

```text
Parent / Representative
WhatsApp
Email
```

localized without changing Source.

---

# 99. Phone clickable

Public:

```text
<a href="tel:+...">
```

---

# 100. WhatsApp clickable

Public:

```text
<a href="https://wa.me/...">
```

---

# 101. Social clickable

Canonical HTTPS profile link.

---

# 102. Email clickable

`mailto:`.

---

# 103. Accessibility

Link label must explain action.

Good:

```text
Позвонить
Написать в WhatsApp
Отправить email
Открыть Instagram
```

---

# 104. Avoid

Multiple indistinguishable:

```text
Открыть
Открыть
Открыть
```

---

# 105. CNT-INV-011 — Contact Actions Must Be Semantically Labeled

---

# 106. Mobile UX

Phone/WhatsApp actions should be touch-friendly.

---

# 107. Desktop UX

Display human-readable contact value where appropriate.

---

# 108. Copy action

Optional:

```text
Copy phone
Copy email
```

---

# 109. Copy action must copy normalized/useful value, not hidden metadata.

---

# 110. Hero Contact CTA

Sticky:

```text
Связаться
```

should resolve current preferred public-safe contact action or Contact section.

---

# 111. If multiple methods

Better:

```text
Связаться
→ Contact section / chooser
```

rather than arbitrary auto-open.

---

# 112. CNT-INV-012 — No Dead CTA

If no public contact:

- do not show broken direct CTA;
- use Feedback/Contact Form where configured.

---

# 113. Contact Form boundary

Public Feedback/Inquiry is separate module.

---

# 114. `Связаться`

May route either:

```text
direct ContactMethod
```

or:

```text
Feedback form
```

according to configured UX.

---

# 115. CNT-INV-013 — Contact Directory ≠ Feedback Inbox

---

# 116. Feedback record

A public inquiry is persisted separately.

It does not create/modify Contact Directory.

---

# 117. Notification recipients boundary

Critical:

```text
Public Contacts
≠
Admin Notification Recipients
```

---

# 118. Admin Notification Recipient

Operational configuration says:

> куда отправлять системные уведомления о Feedback/Casting/etc.

---

# 119. ContactMethod says:

> как professional visitor may contact the actress/representative.

---

# 120. CNT-INV-014 — No Automatic Recipient Derivation

Do not automatically send system notifications to every public email/WhatsApp ContactMethod.

---

# 121. Reverse direction also prohibited

Admin notification recipient does not automatically become Public Contact.

---

# 122. CNT-INV-015 — Notification Recipient Does Not Grant Public Exposure

---

# 123. Shared underlying identity

Future UX MAY allow explicitly linking an Admin Notification Recipient to existing ContactMethod for convenience.

But the operational permission remains separate.

---

# 124. Contacts vs Admin Users

Admin user account email/phone is authentication identity.

It does not automatically enter Contacts Directory.

---

# 125. CNT-INV-016 — Admin Identity ≠ Professional Contact

---

# 126. Contacts vs Professional Links

Examples:

```text
WhatsApp → Contacts
email → Contacts
agent phone → Contacts
Instagram used for contact → Contacts

showreel URL → Professional Media & Links
YouTube scene → Professional Media & Links
```

---

# 127. One social profile ambiguity

If Instagram serves both professional presentation and contact:

define one Source owner and reference it.

Avoid divergent duplicate URLs.

---

# 128. Questionnaire integration

Questionnaire may include selected contact methods.

---

# 129. CNT-QNR-001 — Contact Selection Is Explicit

Prepared Questionnaire configuration determines which eligible contact methods are included.

---

# 130. Default contacts

Admin can configure default questionnaire contacts.

---

# 131. Compact Questionnaire

Should include minimal usable professional contact.

---

# 132. Extended Questionnaire

May include more approved methods.

---

# 133. Casting Questionnaire

Can include casting-appropriate contact.

---

# 134. Questionnaire presentation

Example:

```text
Контакты

Родитель / представитель
Телефон: +7 ...
WhatsApp: ...
Email: ...
```

---

# 135. Phone hyperlink in HTML

Clickable `tel:`.

---

# 136. WhatsApp hyperlink in HTML/PDF

Actual clickable HTTPS URL.

---

# 137. Email hyperlink

Actual `mailto:` where renderer supports.

---

# 138. Social links

Actual hyperlinks.

---

# 139. CNT-QNR-002 — PDF Contact Values Must Remain Actionable

Where PDF technology allows actual hyperlink annotations.

---

# 140. QR

Contact QR is **not automatically required**.

QR subsystem may later support selected contact destinations if product explicitly enables them.

---

# 141. Recommended QR priority

Professional media/profile links before direct personal contact details.

---

# 142. CNT-QNR-003 — Never Generate Contact QR Implicitly

---

# 143. Historical Questionnaire

At publish, freeze selected Contact representation.

---

# 144. Freeze includes

Conceptually:

```text
contact role/display label
selected method type
display value
action URL
ordering
```

---

# 145. Current phone changes later

Historical Questionnaire retains historical contact snapshot semantics.

---

# 146. Important privacy exception

Current privacy/legal revocation can block access to old artifact/distribution without rewriting semantic snapshot according to DOC-092/DOC-094.

---

# 147. CNT-INV-017 — Historical Snapshot ≠ Perpetual Access Right

---

# 148. Builder integration

Public Builder receives only:

```text
allow_in_public_questionnaire_builder=true
```

methods.

---

# 149. Visitor may select/include permitted methods.

---

# 150. Builder cannot

```text
edit phone
edit email
edit WhatsApp
edit name
change relationship
```

---

# 151. CNT-BLD-001 — Builder Selects, Never Edits Contact Source

---

# 152. Generate revalidation

Before snapshot:

```text
ContactEntry current
ContactMethod current
same profile
Builder eligible
not archived
normalized value valid
```

---

# 153. Permission revoked mid-session

Generation must block/remove contact.

---

# 154. CNT-BLD-002 — Preview Eligibility Is Not Generate Authority

---

# 155. Public Builder privacy

Builder-only ContactMethod is sent only inside active Builder context.

Not ordinary Public Profile payload.

---

# 156. CNT-PRV-003 — Builder Projection Is Not Public Projection

---

# 157. Search

Public Search should generally **not index raw phone/email values**.

---

# 158. Searchable contact section

Search may index safe terms:

```text
контакты
WhatsApp
агент
представитель
```

to navigate to Contact section.

---

# 159. CNT-PRV-004 — Public Search Does Not Need Contact PII

---

# 160. Admin Search

May search contact person/name/method where authorized.

---

# 161. SEO

Raw phone/email should not be sprayed through:

```text
unnecessary JSON-LD
OpenGraph
sitemap text
```

unless explicit SEO/business policy requires it.

---

# 162. Structured data

If professional contact is included in structured data, it must follow same Public visibility policy.

---

# 163. CNT-INV-018 — SEO Cannot Exceed Public Contact Visibility

---

# 164. Analytics

Allowed high-level events:

```text
contact_section_viewed
contact_phone_clicked
contact_whatsapp_clicked
contact_email_clicked
contact_social_clicked
```

---

# 165. Analytics must prefer

```text
contact_method_id
method_type
```

over raw:

```text
phone
email
URL
```

---

# 166. CNT-PRV-005 — Do Not Put Raw Contact Value in Analytics by Default

---

# 167. Casting integration

Casting workflow may need recommended contact method.

---

# 168. Casting AI can state

```text
available contact: representative WhatsApp
```

based only on current allowed Admin context.

---

# 169. AI cannot send automatically.

---

# 170. CNT-AI-001 — AI Has No Contact-Sending Authority

---

# 171. BB Assistant

May draft contact/casting response text.

It does not change Contact Directory.

---

# 172. CNT-AI-002 — BB Cannot Invent Phone/Email

---

# 173. AI provider context

Raw personal contacts should be excluded from AI context unless absolutely necessary for task.

---

# 174. Example

Biography generation does not need phone number.

---

# 175. CNT-PRV-006 — AI Context Is Data-Minimized

---

# 176. VOP

May detect:

```text
invalid phone format
invalid email
broken social URL
duplicate method
primary contact has no public method
Builder has no eligible contact
Questionnaire default points to archived method
```

---

# 177. VOP safe actions

May:

```text
revalidate URL
invalidate cache
recompute readiness
```

---

# 178. VOP cannot

```text
change phone
publish contact
change parent role
make method public
choose primary
```

automatically.

---

# 179. CNT-AI-003 — Contact Mutations Require Human Authority

---

# 180. Contact validation — Phone

Server validates normalized number.

---

# 181. Invalid phone

Reject or Draft-warning according to lifecycle.

---

# 182. Published/actionable phone

Must be valid enough to produce safe `tel:` URL.

---

# 183. WhatsApp validation

Requires valid WhatsApp-compatible international phone representation.

---

# 184. Email validation

Syntactic validation.

Do not claim mailbox existence solely from syntax.

---

# 185. CNT-MTH-003 — Valid Syntax ≠ Verified Ownership

---

# 186. Optional verification

Future email/phone ownership verification may exist.

Not baseline.

---

# 187. Social URL validation

Provider-specific canonical host where method type is provider-specific.

---

# 188. Example

`INSTAGRAM` should not point to random unrelated domain.

---

# 189. Generic social

Use `OTHER_SOCIAL` when provider not in controlled set.

---

# 190. CNT-MTH-004 — Method Type and Destination Must Agree

---

# 191. Duplicate methods

Recommended uniqueness:

```text
same ContactEntry
+
same method type
+
same normalized value
```

should not duplicate.

---

# 192. Same phone on two ContactEntries

May be legitimate shared family/agency number.

Do not universally reject.

---

# 193. CNT-INV-019 — Duplicate Detection ≠ Forced Merge

---

# 194. Duplicate warning

Admin may be warned:

```text
Этот номер уже используется другим контактом.
```

---

# 195. Contact ordering

Explicit `display_order`.

---

# 196. CNT-INV-020 — CreatedAt Is Not Contact Priority

---

# 197. ContactMethod ordering

Explicit within ContactEntry.

---

# 198. Primary/ordering

Primary contact can be shown first, followed by explicit order.

---

# 199. Admin UX

Recommended:

```text
Contacts
├── Primary
├── Actress
├── Parents / Guardians
├── Agent / Manager / Agency
├── Other
└── Archived
```

---

# 200. Contact card

Should show:

```text
display name
relationship/role
methods
Site
Questionnaire
Builder
Primary indicator
```

---

# 201. Contact editor

Sections:

1. Role/relationship;
2. Name;
3. Organization if relevant;
4. Contact methods;
5. Method visibility;
6. Primary/preferred routing;
7. Questionnaire defaults;
8. Builder preview;
9. Readiness;
10. Usage/history.

---

# 202. Method row

Recommended:

```text
Type | Value | Site | QNR | Builder | Primary
```

---

# 203. Visibility at method level

Do not put one global contact-level checkbox as sole authority.

---

# 204. Optional bulk toggle

UI may offer:

```text
Make all methods public
```

only as explicit batch action.

Server still writes each method policy.

---

# 205. CNT-INV-021 — Bulk UX Does Not Collapse Data Model

---

# 206. Relationship change

Changing:

```text
MOTHER → MANAGER
```

is a significant professional meaning change.

Requires explicit Human action/version validation.

---

# 207. Parent contact name

May be hidden publicly while role + method shown, if product policy permits.

---

# 208. Therefore name visibility may need contextual control

Two approaches:

1. ContactEntry name always follows method exposure;
2. explicit `show_name_on_*` flags if UX requires.

---

# 209. Baseline recommendation

Keep model simple:

Public Contact projection may omit `display_name` and show role only when configured as nonessential.

If name-specific visibility becomes required, add explicit field policy rather than client hiding.

---

# 210. CNT-PRV-007 — Name Privacy Must Be Server-Enforced

---

# 211. Agency

ContactEntry:

```text
contact_role=AGENCY
display_name=<agency name>
```

Methods:

```text
phone
email
website
```

---

# 212. Agent within agency

If individual identity matters:

separate `AGENT` ContactEntry with organization reference/text.

---

# 213. No complex CRM baseline

Contacts module is professional directory, not full CRM.

---

# 214. CNT-INV-022 — Do Not Build Unnecessary CRM Graph

---

# 215. Public readiness

Contact section can be READY if at least one current public actionable method exists.

---

# 216. Suggested code

```text
CONTACT_PUBLIC_METHOD_MISSING
```

---

# 217. Questionnaire readiness

If Questionnaire contact block enabled:

at least one eligible actionable contact is required.

---

# 218. Suggested:

```text
CONTACT_QUESTIONNAIRE_METHOD_MISSING
```

---

# 219. Builder readiness

If Builder template requires contact:

```text
CONTACT_BUILDER_METHOD_MISSING
```

---

# 220. Method-specific readiness

Examples:

```text
CONTACT_PHONE_INVALID
CONTACT_EMAIL_INVALID
CONTACT_WHATSAPP_INVALID
CONTACT_SOCIAL_URL_INVALID
CONTACT_METHOD_ARCHIVED
```

---

# 221. Primary readiness

```text
CONTACT_PRIMARY_HAS_NO_ELIGIBLE_METHOD
```

warning/block depending context.

---

# 222. Public Contact fallback

If no direct public ContactMethod but Feedback form is available:

Profile may still provide:

```text
Связаться
→ Feedback Form
```

---

# 223. CNT-INV-023 — Public Contact Readiness Can Be Satisfied by Approved Inquiry Flow Where Product Intends

But this does not fabricate a ContactMethod.

---

# 224. Contact lifecycle

ContactEntry archive should archive/remove all its methods from current surface eligibility.

---

# 225. Effective method state

```text
METHOD_ACTIVE =
  ContactEntry active
  AND ContactMethod active
```

---

# 226. CNT-INV-024 — Child Method Cannot Exceed Archived Parent

---

# 227. Archive ContactEntry

Effects:

```text
Public removed
QNR current eligibility removed
Builder current eligibility removed
Search/navigation adjusted
Primary pointer invalidated
```

---

# 228. Historical Revisions

Remain frozen.

---

# 229. Archive ContactMethod

Only that method removed.

ContactEntry can remain with other active methods.

---

# 230. Restore

Does not automatically restore old visibility.

---

# 231. Hard delete

Exceptional for contacts because personal-data deletion may be required.

---

# 232. Privacy deletion

Must inspect:

```text
Questionnaire Revisions
Builder Snapshots
PDF artifacts
Casting materials
AI snapshots
Analytics
Logs
Notification configs if separately linked
```

---

# 233. CNT-PRV-008 — Contact Privacy Deletion Requires Copy Discovery

---

# 234. Current revocation

Must immediately remove from all current projections.

---

# 235. Historical access

Can be revoked/redacted according to DOC-092/DOC-094.

---

# 236. Do not mutate historical snapshot to another phone

---

# 237. CNT-INV-025 — Never Substitute Historical Contact

---

# 238. Concurrency

ContactEntry and ContactMethod use `version`.

---

# 239. Updates require `expectedVersion`.

---

# 240. Primary switch

If global primary configured:

atomic transaction.

---

# 241. Duplicate create race

DB/domain uniqueness where applicable.

---

# 242. Visibility update

Source + Audit + Outbox atomic.

---

# 243. External URL checks

Post-commit/operational.

---

# 244. No provider calls inside DB transaction.

---

# 245. Commands — ContactEntry

Canonical:

```text
CreateContactEntry
UpdateContactEntry
UpdateContactRole
SetPrimaryContact
ArchiveContactEntry
RestoreContactEntry
ReorderContacts
```

---

# 246. Commands — ContactMethod

```text
AddContactMethod
UpdateContactMethod
UpdateContactMethodVisibility
SetPreferredContactMethod
ReorderContactMethods
ArchiveContactMethod
RestoreContactMethod
```

---

# 247. Queries

```text
GetPublicContacts
GetAdminContacts
GetContactEntry
GetContactReadiness
GetPrimaryPublicContact
ListQuestionnaireEligibleContacts
ListBuilderEligibleContacts
PreviewPublicContacts
GetContactUsage
```

---

# 248. Add ContactMethod input

Conceptually:

```text
contactEntryId
methodType
value
visibility
```

---

# 249. Server derives

Where relevant:

```text
normalizedValue
actionUrl
```

---

# 250. Client cannot set authority fields

```text
normalizedValue
verified=true
publishedAt
publicHrefOverride
notificationRecipient=true
```

---

# 251. Contact role update

Does not mutate method values.

---

# 252. Visibility input

Exactly:

```text
showOnPublicSite
allowInAdminQuestionnaires
allowInPublicQuestionnaireBuilder
```

---

# 253. Public DTO

Conceptually:

```text
PublicContactDTO {
  role
  displayName?
  methods: [
    {
      type
      label
      displayValue
      href
    }
  ]
}
```

---

# 254. Public DTO excludes

```text
hidden methods
normalized internal-only data where unnecessary
version
visibility flags
notification routing
Admin notes
audit
```

---

# 255. Builder DTO

Conceptually:

```text
BuilderContactOptionDTO {
  contactId
  role
  displayName?
  methods: [
    {
      methodId
      type
      displayValue
      selected
    }
  ]
}
```

Only eligible methods.

---

# 256. Questionnaire projection

Document-oriented frozen contact structure.

---

# 257. Admin DTO

May include:

```text
id
version
role
displayName
organization
methods
visibility
primary/preferred status
usage
readiness
lifecycle
```

---

# 258. Error taxonomy

At minimum:

```text
CONTACT_NOT_FOUND
CONTACT_ROLE_INVALID
CONTACT_METHOD_NOT_FOUND
CONTACT_METHOD_TYPE_INVALID
CONTACT_METHOD_VALUE_REQUIRED
CONTACT_PHONE_INVALID
CONTACT_EMAIL_INVALID
CONTACT_WHATSAPP_INVALID
CONTACT_SOCIAL_URL_INVALID
CONTACT_METHOD_DUPLICATE
CONTACT_WRONG_PROFILE
CONTACT_ARCHIVED
CONTACT_METHOD_ARCHIVED
CONTACT_PRIMARY_CONFLICT
CONTACT_PRIMARY_HAS_NO_ELIGIBLE_METHOD
CONTACT_QUESTIONNAIRE_NOT_ELIGIBLE
CONTACT_BUILDER_NOT_ELIGIBLE
```

---

# 259. Public errors

Do not expose hidden Contact existence via guessed ID.

Return safe Not Found where enumeration risk applies.

---

# 260. Audit

High-impact:

```text
phone/email/WhatsApp change
relationship/role change
visibility change
primary change
archive/restore
public name exposure change
privacy deletion/redaction
```

---

# 261. Why

Professional outreach and privacy depend on these values.

---

# 262. Outbox events

Suggested:

```text
ContactCreated
ContactUpdated
ContactRoleChanged
ContactPrimaryChanged
ContactArchived
ContactMethodCreated
ContactMethodUpdated
ContactMethodVisibilityChanged
ContactMethodArchived
```

---

# 263. Consumers

```text
Public Contact Projection
Hero/Sticky CTA
Questionnaire readiness
Builder eligibility
Search/navigation
Cache
VOP
Analytics
```

---

# 264. Notification subsystem is not a consumer that blindly copies public contacts

Any explicit synchronization must be user-configured.

---

# 265. Cache

Public Contacts are cacheable derived projection.

---

# 266. Privacy revocation

High priority.

---

# 267. CNT-INV-026 — Contact Visibility Revocation Must Beat Stale Cache

---

# 268. Search cache/index

Must not retain raw hidden values.

---

# 269. Background jobs

Possible:

```text
CONTACT_SOCIAL_LINK_CHECK
CONTACT_READINESS_RECOMPUTE
```

but not required for phone/email baseline.

---

# 270. No automated contact scraping

System should not crawl public web to discover phone/email and silently save them.

---

# 271. CNT-AI-004 — AI/Web Discovery Cannot Create Contact Source Automatically

---

# 272. Imported contact data

If migration/import exists, it must preserve provenance and require review where ambiguous.

---

# 273. Migration — existing profile contacts

Legacy fields:

```text
phone
email
whatsapp
parentPhone
socialUrl
```

must migrate into structured ContactEntry/ContactMethod records.

---

# 274. Example

Legacy:

```text
mother_phone = +7...
```

becomes:

```text
ContactEntry(role=MOTHER)
  └── ContactMethod(type=PHONE)
```

---

# 275. If same number used for WhatsApp

May create a separate WhatsApp method referencing same normalized number or use channel capability according to final data model.

---

# 276. Preferred semantic model

Separate method records for:

```text
PHONE
WHATSAPP
```

because their surface eligibility can differ.

---

# 277. CNT-MIG-001 — Migration Must Preserve Channel Permissions

---

# 278. Legacy flat `contacts` text

Must be parsed cautiously.

---

# 279. Ambiguous text

Requires Human review.

---

# 280. CNT-MIG-002 — Migration Does Not Guess Relationship

---

# 281. Example ambiguous

```text
+7 700 ...
```

with no owner.

Do not assume ACTRESS/MOTHER/MANAGER.

---

# 282. Legacy visibility

If old field was public, map `show_on_public_site=true`.

Questionnaire/Builder flags must use explicit migration policy, not automatically mirror Public unless business migration specification says so.

---

# 283. CNT-MIG-003 — New Visibility Dimensions Need Explicit Migration Defaults

---

# 284. Suggested conservative defaults

For newly introduced Builder permission:

```text
false
```

unless intentionally enabled after review.

---

# 285. Why

Builder can expose data outside ordinary page flow.

---

# 286. Migration idempotency

Same legacy record cannot produce duplicate ContactMethods on rerun.

---

# 287. Migration provenance

Store/log source field or migration mapping.

---

# 288. Privacy-safe logs

Do not put full phone/email in migration logs unnecessarily.

Use masked/hash/reference where practical.

---

# 289. International phone migration

Normalize if unambiguous.

If country code cannot be resolved confidently, flag review.

---

# 290. CNT-MIG-004 — Do Not Invent Country Code

---

# 291. Social migration

Normalize known provider URLs.

Unknown valid destination → `OTHER_SOCIAL` review.

---

# 292. Testing privacy boundary

Tests must inspect actual serialized response, not only visible DOM.

---

# 293. CNT-INV-027 — Privacy Test Targets Payload, Not CSS

---

# 294. Security headers/link behavior

External social/WhatsApp links follow safe external navigation policy.

---

# 295. `target="_blank"`

If used, include safe relationship behavior according to framework/browser standards.

---

# 296. `tel:` and `mailto:`

Should not be routed through arbitrary redirect handler unless required.

---

# 297. Token/casting contact access

If a contact is exposed only in token-scoped casting Questionnaire:

delivery route must validate token scope.

---

# 298. CNT-PRV-009 — Token Scope Cannot Reveal Other Contact Methods

---

# 299. Public Builder session

Does not become an authorization token for arbitrary contact API queries.

---

# 300. Builder API returns only eligible options bound to session/profile.

---

# 301. Contact value enumeration

Public endpoints must not allow:

```text
/contact-method/{uuid}
```

to reveal arbitrary hidden values.

---

# 302. CNT-PRV-010 — No Direct Public PII Lookup by ID

---

# 303. Rate limiting

Feedback/contact form rate limits belong Feedback module.

Direct `tel:`/WhatsApp clicks do not call server-side provider.

---

# 304. WhatsApp notification distinction

Two different things:

### Public contact
```text
Visitor → wa.me → representative
```

### System notification
```text
Platform → official WhatsApp Business provider → configured Admin recipient
```

---

# 305. CNT-INV-028 — These WhatsApp Flows Are Independent

---

# 306. Why

Public contact can be personal/direct.

System notification must use approved provider and operational recipient policy.

---

# 307. No automatic system messaging from Public CTA

Click merely opens user-controlled communication client.

---

# 308. Privacy by surface matrix

Conceptually:

| Method | Public Site | Admin QNR | Public Builder | Admin Notification |
|---|---:|---:|---:|---:|
| Actress email | explicit | explicit | explicit | separate config |
| Parent phone | explicit | explicit | explicit | separate config |
| Agent WhatsApp | explicit | explicit | explicit | separate config |
| Internal admin number | false | false | false | possible |

---

# 309. CNT-INV-029 — Notification Column Is Not a Fourth Visibility Flag

It belongs another domain/entity.

---

# 310. Contact readiness matrix

```text
PUBLIC_READY
  = at least one valid Public method
    OR configured Feedback fallback

QUESTIONNAIRE_READY
  = at least one valid Admin-QNR eligible method
    when contact block required

BUILDER_READY
  = at least one valid Builder-eligible method
    when template requires contact
```

---

# 311. Current method mutation

Example:

```text
phone A
→ phone B
```

requires:

- version check;
- normalization;
- validation;
- Audit;
- projection invalidation;
- Questionnaire draft staleness where relevant.

---

# 312. Historical Questionnaire

Still contains A.

---

# 313. Current website

Uses B.

---

# 314. CNT-INV-030 — Current Contact and Historical Snapshot Are Deliberately Different

---

# 315. Broken social URL

Operational health may warn.

No automatic replacement.

---

# 316. Email bounce

If future email verification exists, bounce is operational state.

It does not silently delete contact.

---

# 317. Phone verification

Likewise.

---

# 318. Contact source authority remains Human/admin-owned.

---

# 319. Anti-patterns

`CNT-AP-001`  
Store all contacts in one freeform textarea.

`CNT-AP-002`  
Store “Мама: +7...” without structured relationship.

`CNT-AP-003`  
Store phone and WhatsApp URL independently when same identity could be derived.

`CNT-AP-004`  
Expose raw private phone to browser and hide with CSS.

`CNT-AP-005`  
Expose hidden email in RSC/JSON.

`CNT-AP-006`  
One global `is_public` controls Site/QNR/Builder.

`CNT-AP-007`  
Contact-level visibility is only permission while individual methods differ.

`CNT-AP-008`  
Primary contact automatically becomes public.

`CNT-AP-009`  
Public contact automatically becomes Admin notification recipient.

`CNT-AP-010`  
Admin notification recipient automatically becomes Public contact.

`CNT-AP-011`  
Admin user email automatically appears on site.

`CNT-AP-012`  
Parent number displayed as actress's direct number.

`CNT-AP-013`  
Country/nationality used to guess phone country code.

`CNT-AP-014`  
Invalid number silently normalized into guessed number.

`CNT-AP-015`  
Builder edits phone/email.

`CNT-AP-016`  
Builder enumerates raw Contacts Directory.

`CNT-AP-017`  
Builder Public projection reused for ordinary Site.

`CNT-AP-018`  
Questionnaire includes every stored contact automatically.

`CNT-AP-019`  
Private certificate/document email extracted as Contact automatically.

`CNT-AP-020`  
AI invents representative contact.

`CNT-AP-021`  
AI sends WhatsApp message automatically.

`CNT-AP-022`  
BB updates phone from generated text.

`CNT-AP-023`  
Social profile duplicated in multiple modules with divergent URLs.

`CNT-AP-024`  
Search indexes raw private phone/email.

`CNT-AP-025`  
Analytics logs complete contact value on every click.

`CNT-AP-026`  
SEO exposes hidden contact.

`CNT-AP-027`  
Archive ContactEntry hard-deletes methods/history.

`CNT-AP-028`  
Restore automatically republishes previous methods.

`CNT-AP-029`  
Current phone update rewrites historical Questionnaire.

`CNT-AP-030`  
Privacy deletion substitutes old historical phone with new phone.

`CNT-AP-031`  
Direct public endpoint fetches ContactMethod by guessable UUID.

`CNT-AP-032`  
Token-scoped Questionnaire exposes all Contact methods.

`CNT-AP-033`  
Email syntax validation is called ownership verification.

`CNT-AP-034`  
Telegram URL generated from phone without explicit configuration.

`CNT-AP-035`  
Phone stored only in display-formatted string.

`CNT-AP-036`  
`tel:` link built from unsanitized free text.

`CNT-AP-037`  
WhatsApp link built from arbitrary user-provided URL without normalization.

`CNT-AP-038`  
Social provider type points to unrelated domain.

`CNT-AP-039`  
CreatedAt determines preferred contact.

`CNT-AP-040`  
VOP automatically makes a contact public because no other public contact exists.

`CNT-AP-041`  
AI provider receives entire Contacts Directory for unrelated writing task.

`CNT-AP-042`  
Public page fails because one external social URL is down.

`CNT-AP-043`  
One ContactMethod mutation bypasses expectedVersion.

`CNT-AP-044`  
Contact privacy policy implemented only in React component.

`CNT-AP-045`  
Public WhatsApp CTA confused with outbound WhatsApp Business notification.

`CNT-AP-046`  
Questionnaire PDF prints Admin notification number.

`CNT-AP-047`  
Migration assumes unlabeled legacy number belongs to actress.

`CNT-AP-048`  
Migration automatically enables Public Builder for all historical public contacts.

`CNT-AP-049`  
Private phone retained indefinitely in debug logs.

`CNT-AP-050`  
Theme decides which contact is public.

---

# 320. Core invariants

`CNT-INV-031`  
Contacts Directory is the Master Source of professional contact data.

`CNT-INV-032`  
ContactEntry and ContactMethod are distinct entities.

`CNT-INV-033`  
Each ContactEntry belongs to one Actor Profile.

`CNT-INV-034`  
Every ContactMethod belongs to one ContactEntry.

`CNT-INV-035`  
Relationship/role is structured.

`CNT-INV-036`  
Parent/guardian relationships are explicit.

`CNT-INV-037`  
Contact methods use controlled types.

`CNT-INV-038`  
Phone identity is normalized separately from display formatting.

`CNT-INV-039`  
Clickable phone uses safe normalized `tel:` value.

`CNT-INV-040`  
WhatsApp target derives from explicitly configured WhatsApp method.

`CNT-INV-041`  
Email uses safe `mailto:` semantics.

`CNT-INV-042`  
Social URLs are validated.

`CNT-INV-043`  
Contact role and method label remain separate.

`CNT-INV-044`  
Primary Contact remains presentation configuration.

`CNT-INV-045`  
Primary Contact does not imply Public visibility.

`CNT-INV-046`  
Visibility is evaluated per ContactMethod.

`CNT-INV-047`  
Public/QNR/Builder permissions remain independent.

`CNT-INV-048`  
Hidden ContactMethod is removed before Public serialization.

`CNT-INV-049`  
Builder-only method is not ordinary Public data.

`CNT-INV-050`  
ContactEntry with no usable Public methods is not exposed by default.

`CNT-INV-051`  
Public contact does not automatically become NotificationRecipient.

`CNT-INV-052`  
NotificationRecipient does not automatically become Public contact.

`CNT-INV-053`  
Admin user identity does not automatically become professional contact.

`CNT-INV-054`  
Contact Directory remains separate from Feedback Inbox.

`CNT-INV-055`  
Direct contact and system notification WhatsApp flows are separate.

`CNT-INV-056`  
Questionnaire contact selection is explicit.

`CNT-INV-057`  
Builder selects but cannot edit contact Source.

`CNT-INV-058`  
Builder Generate revalidates current method eligibility.

`CNT-INV-059`  
Historical Questionnaire freezes exact contact representation.

`CNT-INV-060`  
Current contact updates do not rewrite historical Revision.

`CNT-INV-061`  
Historical snapshot does not guarantee perpetual access after privacy revocation.

`CNT-INV-062`  
Public Search does not need raw contact PII.

`CNT-INV-063`  
SEO cannot exceed Public contact visibility.

`CNT-INV-064`  
Analytics minimizes raw contact values.

`CNT-INV-065`  
AI cannot invent contact details.

`CNT-INV-066`  
AI cannot send professional communication automatically.

`CNT-INV-067`  
AI context uses minimum necessary contact data.

`CNT-INV-068`  
VOP cannot change contact facts or visibility autonomously.

`CNT-INV-069`  
Contact method syntax validation remains server-side.

`CNT-INV-070`  
Valid syntax does not equal verified ownership.

`CNT-INV-071`  
Duplicate detection does not force merge across contacts.

`CNT-INV-072`  
Display ordering is explicit.

`CNT-INV-073`  
Archived parent prevents child method current exposure.

`CNT-INV-074`  
Archive does not rewrite history.

`CNT-INV-075`  
Restore does not auto-expose.

`CNT-INV-076`  
Privacy deletion performs dependency/copy discovery.

`CNT-INV-077`  
Historical contact is never silently substituted.

`CNT-INV-078`  
Contact mutations are version-aware.

`CNT-INV-079`  
Primary switch is atomic where enabled.

`CNT-INV-080`  
Visibility changes propagate through Audit/Outbox.

`CNT-INV-081`  
Cache cannot preserve revoked contact publicly.

`CNT-INV-082`  
Public API cannot enumerate hidden ContactMethods by ID.

`CNT-INV-083`  
Token-scoped access reveals only scoped contacts.

`CNT-INV-084`  
Migration does not guess owner/relationship.

`CNT-INV-085`  
Migration does not invent country code.

`CNT-INV-086`  
New visibility dimensions use explicit migration policy.

`CNT-INV-087`  
Migration is idempotent.

`CNT-INV-088`  
Public privacy tests inspect payloads, not only rendered DOM.

`CNT-INV-089`  
Theme has no contact privacy/data authority.

`CNT-INV-090`  
Core Contacts workflow functions without AI.

---

# 321. E2E-CNT-001 — Create parent contact

Create:

```text
role=MOTHER
display_name=<confirmed name>
```

Expected structured ContactEntry.

---

# 322. E2E-CNT-002 — Add phone

Valid international phone normalized and stored.

---

# 323. E2E-CNT-003 — Phone display

UI shows formatted value while `tel:` uses normalized number.

---

# 324. E2E-CNT-004 — WhatsApp

Configured WhatsApp method generates correct `wa.me` target.

---

# 325. E2E-CNT-005 — Invalid phone

Rejected/not publish-ready.

---

# 326. E2E-CNT-006 — Invalid email

Rejected/not publish-ready.

---

# 327. E2E-CNT-007 — Social URL unsafe

`javascript:` destination rejected.

---

# 328. E2E-CNT-008 — Instagram wrong domain

Provider-specific validation warns/rejects invalid destination.

---

# 329. E2E-CNT-009 — Public phone

Public=true.

Phone appears in Contact projection with clickable action.

---

# 330. E2E-CNT-010 — Hidden phone

Public=false.

Inspect server payload.

Phone value is absent.

---

# 331. E2E-CNT-011 — Questionnaire-only email

```text
Public=false
AdminQ=true
Builder=false
```

Absent Public, available prepared Questionnaire.

---

# 332. E2E-CNT-012 — Builder-only method

Appears only Builder eligibility projection.

---

# 333. E2E-CNT-013 — Builder tamper

Visitor submits modified phone.

Server ignores/rejects and loads current Source.

---

# 334. E2E-CNT-014 — Builder revocation

Selected method loses Builder permission before Generate.

Generation removes/blocks it.

---

# 335. E2E-CNT-015 — ContactEntry no Public methods

Parent role/name does not appear in normal Public Contacts by default.

---

# 336. E2E-CNT-016 — Primary contact

Human selects one Primary Contact.

Projection updates.

---

# 337. E2E-CNT-017 — Primary private

Primary but Public=false.

Public Hero does not leak it.

---

# 338. E2E-CNT-018 — Preferred method

Primary representative has phone + WhatsApp.

Preferred WhatsApp CTA resolves safely.

---

# 339. E2E-CNT-019 — Notification separation

Make public WhatsApp.

No Admin NotificationRecipient is created automatically.

---

# 340. E2E-CNT-020 — Reverse notification separation

Configure internal notification number.

It does not appear in Public Contacts.

---

# 341. E2E-CNT-021 — Admin identity

Admin login email not shown publicly unless separately created as ContactMethod.

---

# 342. E2E-CNT-022 — Questionnaire contact

Selected phone/email appear with correct labels/hyperlinks.

---

# 343. E2E-CNT-023 — Questionnaire not all contacts

Only selected eligible methods appear.

---

# 344. E2E-CNT-024 — Historical phone

Publish QNR with phone A.

Change current phone to B.

Historical QNR remains A.

---

# 345. E2E-CNT-025 — New Questionnaire

New Revision after update uses B.

---

# 346. E2E-CNT-026 — Privacy revocation

Historical artifact containing A can be access-revoked/redacted according to policy without substitution with B.

---

# 347. E2E-CNT-027 — Search

Public Search can navigate to Contacts section without indexing private raw phone/email.

---

# 348. E2E-CNT-028 — SEO

Private method absent from JSON-LD/OpenGraph.

---

# 349. E2E-CNT-029 — Analytics

Click event records method ID/type, not raw phone by default.

---

# 350. E2E-CNT-030 — AI biography

AI context does not receive private contact values when task does not need them.

---

# 351. E2E-CNT-031 — AI invented phone

AI output cannot create ContactMethod automatically.

---

# 352. E2E-CNT-032 — VOP primary warning

Primary contact has no Public method.

VOP warns; does not make one public.

---

# 353. E2E-CNT-033 — Archive method

Phone disappears from current surfaces; other contact methods remain.

---

# 354. E2E-CNT-034 — Archive ContactEntry

All child methods cease current exposure.

---

# 355. E2E-CNT-035 — Restore

Restored Contact does not automatically recover prior Public exposure.

---

# 356. E2E-CNT-036 — Concurrent phone edit

Admin A commits version 7.

Admin B submits version 6.

Expected `STALE_VERSION`.

---

# 357. E2E-CNT-037 — Duplicate same method

Same phone/method twice under same ContactEntry rejected/warned.

---

# 358. E2E-CNT-038 — Shared family number

Same number intentionally used under two different ContactEntries can remain possible with warning.

---

# 359. E2E-CNT-039 — Guessed ID

Anonymous request for hidden ContactMethod UUID does not reveal data.

---

# 360. E2E-CNT-040 — Token scope

Casting token permitting one contact does not expose other private methods.

---

# 361. E2E-CNT-041 — Public Feedback fallback

No direct public methods but Feedback form active.

Contact CTA routes to valid inquiry flow without inventing contact data.

---

# 362. E2E-CNT-042 — Direct WhatsApp

Click opens visitor-controlled WhatsApp destination.

No platform outbound notification is sent.

---

# 363. E2E-CNT-043 — Social outage

Broken external social link does not break entire Contact section/Profile.

---

# 364. E2E-CNT-044 — Migration parent field

Legacy `mother_phone` becomes structured `MOTHER → PHONE`.

---

# 365. E2E-CNT-045 — Migration ambiguous number

Unlabeled phone remains review-required.

System does not assume ACTRESS.

---

# 366. E2E-CNT-046 — Migration missing country code

No guessed international prefix.

---

# 367. E2E-CNT-047 — Builder migration default

Legacy public phone is not automatically Builder-enabled unless migration policy explicitly says so.

---

# 368. E2E-CNT-048 — Privacy payload test

Automated test verifies hidden contact string does not occur anywhere in returned Public HTML/RSC/JSON.

---

# 369. E2E-CNT-049 — Accessibility

Keyboard/screen-reader user can activate Phone/WhatsApp/Email actions with meaningful labels.

---

# 370. E2E-CNT-050 — No AI

Contacts CRUD, Public display, Questionnaire and Builder remain fully functional with AI disabled.

---

# 371. Domain diagram

```text
Actor Profile
     │
     ▼
 ContactEntry
     │
     ├── Role / Name / Organization
     │
     ▼
 ContactMethod
     │
     ├── Phone
     ├── Email
     ├── WhatsApp
     ├── Telegram
     └── Social
```

---

# 372. Visibility diagram

```text
ContactMethod
      │
      ├── show_on_public_site
      ├── allow_in_admin_questionnaires
      └── allow_in_public_questionnaire_builder
             │
             ▼
      Server-side Projection

Hidden method:
never serialized to unauthorized surface.
```

---

# 373. Notification separation diagram

```text
CONTACTS DOMAIN

Professional Contact
      ↓
Public visitor communication

                ≠

NOTIFICATIONS DOMAIN

Notification Recipient
      ↓
Platform-generated operational alert
```

---

# 374. Questionnaire flow

```text
Contacts Directory
       ↓
Admin-QNR Eligible Methods
       ↓
Explicit Selection
       ↓
Questionnaire Draft
       ↓
Publish
       ↓
Frozen Contact Snapshot
       ↓
HTML / PDF
```

---

# 375. Builder flow

```text
Contacts Directory
       ↓
Builder Eligibility Projection
       ↓
Visitor Selects Allowed Methods
       ↓
Generate Revalidation
       ↓
BuilderGenerationSnapshot
```

---

# 376. Phone/WhatsApp diagram

```text
Normalized Phone
   +77001234567
       │
       ├── PHONE
       │     ↓
       │ tel:+77001234567
       │
       └── WHATSAPP
             ↓
       https://wa.me/77001234567
```

---

# 377. Privacy gate diagram

```text
Request
  ↓
Surface Context
  ↓
Authentication / Token Scope
  ↓
Contact Visibility Policy
  ↓
Projection Allowlist
  ↓
Safe DTO
  ↓
Serialization
```

---

# 378. Quality gate

Перед implementation должны быть подтверждены:

- [ ] ContactEntry model;
- [ ] ContactMethod model;
- [ ] structured role taxonomy;
- [ ] explicit Parent/Mother/Father/Guardian semantics;
- [ ] method type taxonomy;
- [ ] normalized phone model;
- [ ] `tel:` generation;
- [ ] WhatsApp URL generation;
- [ ] email action semantics;
- [ ] social URL validation;
- [ ] primary contact semantics;
- [ ] preferred method semantics;
- [ ] per-method visibility triplet;
- [ ] server-side privacy filtering;
- [ ] Hero/Sticky Contact projection;
- [ ] Feedback fallback;
- [ ] NotificationRecipient separation;
- [ ] Admin User separation;
- [ ] ProfessionalLink ownership boundary;
- [ ] Questionnaire explicit contact selection;
- [ ] Builder selection-only behavior;
- [ ] historical contact snapshot;
- [ ] Search/SEO privacy;
- [ ] analytics minimization;
- [ ] AI data minimization;
- [ ] VOP boundaries;
- [ ] lifecycle/archive;
- [ ] privacy deletion/copy discovery;
- [ ] concurrency;
- [ ] audit/outbox;
- [ ] migration;
- [ ] payload-level privacy tests;
- [ ] accessibility;
- [ ] E2E suite.

---

# 379. Acceptance criteria

`AC-CNT-001`  
Contacts Directory является единым Master Source профессиональных контактов.

`AC-CNT-002`  
ContactEntry отделён от ContactMethod.

`AC-CNT-003`  
Родительские/представительские роли моделируются явно.

`AC-CNT-004`  
Phone/Email/WhatsApp/Social являются typed methods.

`AC-CNT-005`  
Телефон хранит нормализованную identity отдельно от display formatting.

`AC-CNT-006`  
Phone action использует безопасный `tel:` target.

`AC-CNT-007`  
WhatsApp action формируется из подтверждённого WhatsApp method.

`AC-CNT-008`  
Social/email destinations проходят server-side validation.

`AC-CNT-009`  
Public/QNR/Builder visibility управляется независимо.

`AC-CNT-010`  
Visibility применяется на уровне ContactMethod.

`AC-CNT-011`  
Hidden contact value не попадает в unauthorized browser payload.

`AC-CNT-012`  
Primary status не делает Contact публичным автоматически.

`AC-CNT-013`  
Public Contact не становится Admin NotificationRecipient автоматически.

`AC-CNT-014`  
Admin NotificationRecipient не становится Public Contact автоматически.

`AC-CNT-015`  
Admin user identity не публикуется как Contact автоматически.

`AC-CNT-016`  
Hero/Contact CTA использует Projection, а не duplicated Profile field.

`AC-CNT-017`  
Parent contact clearly identified as parent/representative.

`AC-CNT-018`  
Prepared Questionnaire включает только explicitly selected eligible methods.

`AC-CNT-019`  
Public Builder не может редактировать contact values.

`AC-CNT-020`  
Builder Generate revalidates contact eligibility.

`AC-CNT-021`  
Historical Questionnaire сохраняет exact contact snapshot.

`AC-CNT-022`  
Current contact update не переписывает historical Revision.

`AC-CNT-023`  
Privacy revocation может блокировать historical delivery без подмены значения.

`AC-CNT-024`  
Public Search/SEO не раскрывают hidden contact PII.

`AC-CNT-025`  
Analytics не требует raw phone/email value.

`AC-CNT-026`  
AI не может придумывать контактные данные.

`AC-CNT-027`  
AI не может автоматически отправлять professional communication.

`AC-CNT-028`  
AI context минимизирует персональные contact data.

`AC-CNT-029`  
VOP не меняет contact values/visibility автоматически.

`AC-CNT-030`  
Archive removes current exposure without rewriting history.

`AC-CNT-031`  
Restore does not auto-expose.

`AC-CNT-032`  
Privacy deletion performs dependent-copy discovery.

`AC-CNT-033`  
Public ContactMethod cannot be enumerated via hidden ID lookup.

`AC-CNT-034`  
Token-scoped access exposes only scoped contacts.

`AC-CNT-035`  
Contact mutations are version-aware.

`AC-CNT-036`  
Visibility mutations emit Audit/Outbox for derived invalidation.

`AC-CNT-037`  
Migration does not guess contact owner/relationship.

`AC-CNT-038`  
Migration does not invent international phone code.

`AC-CNT-039`  
New Builder/QNR visibility dimensions use explicit migration policy.

`AC-CNT-040`  
Privacy tests verify serialization payload, not only visual hiding.

`AC-CNT-041`  
Direct WhatsApp contact and platform WhatsApp notifications remain independent workflows.

`AC-CNT-042`  
Core Contacts Module operates fully without AI.

---

# 380. Финальная доктрина

> **Contacts Module является единым структурированным Master Source профессиональной связи с актрисой и её представителями. Контактное лицо/роль и способы связи моделируются отдельно, а родительские, агентские и представительские отношения задаются явно. Каждый ContactMethod самостоятельно управляет разрешениями для Public Site, prepared Questionnaire и Public Builder; скрытые телефоны, email, WhatsApp и social URLs исключаются server-side до сериализации и никогда не полагаются на client-side hiding. Clickable phone, WhatsApp, email и social actions строятся из нормализованных подтверждённых Source values. Historical Questionnaires freeze exact contact representation, однако privacy/security revocation может ограничить последующий доступ без подмены исторического значения. Public Contacts, Admin identities и Notification Recipients остаются разными domains: публичный WhatsApp CTA не является outbound WhatsApp Business notification channel и никогда автоматически не определяет получателя системных уведомлений.**