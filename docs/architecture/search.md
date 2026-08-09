# SEARCH ARCHITECTURE

> **Статус документации:** COMPLETE / canonical imported specification  
> **Статус реализации:** `NOT_IMPLEMENTED_AS_SPECIFIED` до подтверждения кодом, миграциями, тестами и runtime evidence согласно `docs/implementation/implementation-status.md` (DOC-207).  
> `✅ Completed` в исходном тексте означает завершённость спецификации, а не реализацию функции.

## Нормативная архитектура публичного, Builder- и Admin-поиска, индексации, relevance и privacy boundaries

**Целевой файл:** `docs/architecture/search.md`  
**Документ:** DOC-078  
**Статус:** ✅ Completed  
**Тип:** Architecture / Search / Indexing / Discovery / Privacy

**Связанные документы:**
- `docs/TZ.md`
- `docs/product/terminology.md`
- `docs/product/business-rules.md`
- `docs/product/information-architecture.md`
- `docs/product/product-principles.md`
- `docs/customer-journey/personas.md`
- `docs/customer-journey/method-6-3-5.md`
- `docs/ux/public-navigation.md`
- `docs/ux/admin-navigation.md`
- `docs/ux/public-user-flows.md`
- `docs/ux/admin-user-flows.md`
- `docs/ux/public-questionnaire-builder.md`
- `docs/ux/content-hierarchy.md`
- `docs/ux/accessibility.md`
- `docs/domain/domain-model.md`
- `docs/domain/entity-relationships.md`
- `docs/domain/state-machines.md`
- `docs/domain/data-ownership.md`
- `docs/domain/revisions-and-history.md`
- `docs/architecture/architecture-overview.md`
- `docs/architecture/components.md`
- `docs/architecture/data-flows.md`
- `docs/architecture/projections.md`
- `docs/architecture/background-jobs.md`
- `docs/architecture/cache.md`

---

# 1. Назначение документа

Настоящий документ определяет целевую архитектуру Search subsystem.

Он фиксирует:

1. какие виды поиска существуют;
2. чем Public Search отличается от Builder Search;
3. чем Admin Global Search отличается от Public Search;
4. какие сущности индексируются;
5. какие поля могут участвовать в поиске;
6. какие поля запрещено индексировать публично;
7. как применяются visibility и lifecycle rules;
8. как строится relevance;
9. как работает exact match;
10. как работает prefix/partial match;
11. как учитывается locale;
12. как выполняется incremental indexing;
13. как выполняется full rebuild;
14. как обрабатывается stale index;
15. как защищаются скрытые/private entities;
16. почему Search Index не является Source of Truth;
17. как Search взаимодействует с Public Builder;
18. как Search взаимодействует с cache;
19. как Search работает при outage;
20. какой backend является baseline;
21. когда допустим dedicated search engine;
22. как Search тестируется.

---

# 2. Главный принцип

> **Поиск помогает профессиональному пользователю быстрее найти уже разрешённую и авторитетную информацию, но не определяет, существует ли профессиональный факт, является ли объект опубликованным или имеет ли пользователь право его видеть.**

Canonical:

```text
AUTHORITATIVE DATA
       ↓
ELIGIBILITY / VISIBILITY
       ↓
SEARCH PROJECTION
       ↓
SEARCH INDEX
       ↓
QUERY
       ↓
RESULT CANDIDATES
       ↓
FINAL ELIGIBILITY CHECK where required
       ↓
USER
```

---

# 3. Search IDs

Используются:

```text
SEARCH-*
SEARCH-PUB-*
SEARCH-PQB-*
SEARCH-ADM-*
SEARCH-IDX-*
SEARCH-RANK-*
SEARCH-SEC-*
```

Invariants:

```text
SEARCH-INV-*
```

---

# 4. Search Contexts

В системе существует минимум три независимых search context:

```text
PUBLIC SEARCH
PUBLIC BUILDER SEARCH
ADMIN GLOBAL SEARCH
```

Они MUST иметь разные access semantics.

---

# 5. SEARCH-INV-001 — No Universal Search Index

Нельзя построить один unrestricted индекс:

```text
all data
```

и затем надеяться скрывать лишнее только на UI.

---

# 6. Почему

Потому что:

```text
Public visibility
!=
Builder eligibility
!=
Admin authorization
```

---

# 7. Public Search

Назначение:

> позволить casting professional быстро найти профессионально значимую информацию внутри публичного профиля.

---

# 8. Builder Search

Назначение:

> позволить посетителю найти только те элементы, которые разрешено добавить в Public Questionnaire Builder.

---

# 9. Admin Global Search

Назначение:

> позволить оператору быстро найти Master, Operational и Configuration entities внутри Admin в рамках разрешений.

---

# 10. SEARCH-INV-002 — Search Is Derived

Search Index является:

```text
DERIVED DATA
```

и должен быть полностью rebuildable.

---

# 11. SEARCH-INV-003 — Search Result Is Not Authority

Search hit:

```text
does not prove current eligibility
```

Особенно при eventual consistency.

---

# 12. SEARCH-INV-004 — Search Must Not Leak Private Data

Private value MUST NOT появляться:

```text
in result title
snippet
autocomplete
facet
match explanation
URL
serialized payload
```

в Public context.

---

# 13. SEARCH-INV-005 — No AI Suitability Ranking

Search MUST NOT использовать generative AI или implicit “professional suitability score” для перестановки actress evidence по perceived attractiveness, emotional quality или casting suitability.

---

# 14. SEARCH-INV-006 — No Popularity Authority

Click/view popularity MUST NOT автоматически определять professional relevance.

---

# 15. Baseline Search Backend

Для текущего масштаба canonical baseline:

> **PostgreSQL-first search architecture**

---

# 16. Почему PostgreSQL-first

Текущий продукт:

- один Actor Profile;
- ограниченный объём professional content;
- не marketplace;
- не требует поиска по миллионам профилей;
- уже использует PostgreSQL;
- выигрывает от минимального operational footprint.

---

# 17. Возможности baseline

Initial implementation MAY использовать:

```text
PostgreSQL full-text search
ILIKE/prefix matching
normalized search columns
tsvector
trigram indexes
```

где это оправдано.

---

# 18. Dedicated Search Engine

Elasticsearch/OpenSearch/Meilisearch/Typesense/другой engine MUST NOT добавляться без реальной необходимости.

---

# 19. Триггеры для dedicated engine

Допустимо рассматривать при:

```text
materially larger corpus
advanced multilingual ranking
high QPS
complex fuzzy matching
faceted search at scale
measured PostgreSQL search bottleneck
```

---

# 20. Backend Abstraction

Application SHOULD скрывать backend за:

```text
SearchQueryService
SearchIndexer
```

чтобы migration не меняла UI/domain semantics.

---

# 21. Search Data Classes

Search subsystem индексирует только:

```text
projection-approved fields
```

---

# 22. Public Search Eligible Domains

Canonical candidates:

```text
Actor Identity
Portfolio categories/captions
Skills
Languages
Projects
Roles
Training
Achievements
Professional Media descriptions
approved public professional links where useful
```

---

# 23. Public Search Excluded Domains

MUST NOT индексировать:

```text
Feedback
Feedback Attachments
Casting Source
Casting AI Analysis
Opportunity
Admin Notes
VOP observations
BB drafts
Theme proposals
Audit
OAuth data
Support tickets
private Contacts
private media metadata
```

---

# 24. Contact Search

Public Search SHOULD generally not expose Contact values as ordinary search corpus.

Contact remains action destination.

---

# 25. Public Search Purpose

Primary tasks:

```text
find a skill
find a language
find a project
find a role
find a portfolio category
find training
```

---

# 26. Persona Requirement

Особенно поддерживает:

```text
INS-CD-02 Search Inside Profile
```

для Casting Director.

---

# 27. Search Scope

Public search — **inside this actress profile**, not global casting marketplace.

---

# 28. No Actor Marketplace Semantics

Не требуется:

```text
search thousands of actors
compare actors
rank actors
recommend actress vs other actresses
```

---

# 29. Public Search Result Types

Canonical:

```text
PROFILE_FACT
PORTFOLIO
SKILL
LANGUAGE
PROJECT
ROLE
TRAINING
ACHIEVEMENT
PROFESSIONAL_MEDIA
```

---

# 30. Search Result Contract

Каждый result SHOULD содержать:

```text
result_type
title
matched_value
context
short safe snippet
target_route
optional thumbnail
locale
```

---

# 31. Search Result Does Not Need Full Entity

Result payload должен быть компактным.

---

# 32. Example — Skill Result

```text
Type:
Skill

Title:
Верховая езда

Context:
Навыки

Matched Value:
Верховая езда

Route:
/#skills
```

---

# 33. Example — Project Result

```text
Type:
Project

Title:
Название проекта

Context:
Роль: ...

Route:
/projects/{slug}
```

---

# 34. Example — Portfolio Result

```text
Type:
Portfolio

Title:
Фото в профиль

Context:
Портфолио

Route:
/portfolio?filter=profile
```

---

# 35. Result Context Must Be Professional

Avoid technical:

```text
entity_type=portfolio_item
database_id=...
```

in Public UI.

---

# 36. Public Search Entry Point

Search SHOULD be available with:

```text
one meaningful action
```

from relevant public experience where design permits.

---

# 37. Search Is Supplemental Navigation

Global primary navigation remains functional without Search.

---

# 38. SEARCH-INV-007 — No Search Dependency for Critical Routes

Profile, Portfolio, Questionnaire, Contact MUST remain directly navigable if Search fails.

---

# 39. Search Query Normalization

Canonical normalization MAY include:

```text
trim whitespace
case normalization
Unicode normalization
locale-aware tokenization
safe punctuation handling
```

---

# 40. Query Length

Empty/very short query behaviour MUST be explicit.

---

# 41. Empty Query

Public Search SHOULD not return arbitrary entire database dump.

Possible:

```text
suggest key professional categories
recently relevant static shortcuts
or no results until query
```

---

# 42. One-Character Query

System MAY require minimum length for broad fuzzy search while supporting exact category shortcuts.

---

# 43. Query Security

Search input is untrusted.

Must be protected from:

```text
SQL injection
wildcard abuse
regex DoS if regex supported
oversized queries
control characters
```

---

# 44. Exact Match Priority

Professional exact matches SHOULD rank highest.

---

# 45. Canonical relevance precedence

Recommended:

```text
1 Exact normalized value match
2 Exact token/phrase match
3 Prefix match
4 Strong partial/fuzzy lexical match
5 Contextual description match
```

---

# 46. Entity Priority

When textual match quality is equal, professional importance MAY affect ranking.

Suggested context-sensitive baseline:

```text
P0/P1 structured facts
→ Skills/Languages
→ Project/Role
→ Training/Achievements
→ Narrative/supporting text
```

---

# 47. Query Example — "английский"

Expected high relevance:

```text
Language: English — B2/C1/etc.
```

before biography paragraph mentioning English casually.

---

# 48. Query Example — "верховая езда"

Expected:

```text
Skill exact result
```

before Project description containing same words.

---

# 49. Query Example — Project title

Exact Project title ranks before loose text mention.

---

# 50. Search Relevance Is Deterministic

Same index/query SHOULD generally produce stable ranking.

---

# 51. No Random Ranking

Prohibited for professional search.

---

# 52. No Analytics Auto-Ranking

A frequently clicked Project MUST NOT automatically outrank exact Skill match.

---

# 53. AI Search

Generative semantic search is not required baseline.

---

# 54. Future Semantic Search

MAY be evaluated later for natural-language queries, but:

- must remain optional;
- cannot override privacy;
- cannot invent facts;
- cannot score actress suitability;
- must link result to real Source evidence.

---

# 55. Search Match Explanation

Where useful, UI MAY show:

```text
Совпадение: Навык
```

или contextual snippet.

---

# 56. Match Explanation Must Be Source-Grounded

No AI-generated rationale without clear labeling/need.

---

# 57. Locale Architecture

Search index/query MUST support active locale.

---

# 58. Localized Narrative

Project/Training/Biography localized content should be indexed per approved locale.

---

# 59. Structured Facts

Language names/skill definitions may have localized labels.

---

# 60. Locale-Specific Index

Possible models:

```text
one index row per locale
```

or:

```text
localized fields in shared record
```

Physical decision later.

---

# 61. Search Result Locale

Requested locale should return matching localized presentation.

---

# 62. Locale Fallback

If approved localized value absent:

follow canonical product locale fallback.

Do not AI-translate during search.

---

# 63. Cross-Language Search

Not mandatory baseline.

Example Russian query finding English localized title MAY be supported later through aliases/transliterations, but must remain explicit/deterministic.

---

# 64. Transliteration

MAY be supported for names where useful.

It MUST NOT create alternate professional facts.

---

# 65. Public Search Index Projection

Canonical flow:

```text
MASTER DATA
   ↓
PUBLIC ELIGIBILITY
   ↓
SEARCH FIELD ALLOWLIST
   ↓
LOCALIZATION
   ↓
NORMALIZATION
   ↓
PUBLIC SEARCH INDEX
```

---

# 66. Search Index Record

Conceptually:

```text
id
profile_id
entity_type
entity_id
public_route
locale
title
searchable_text
matched_fields
priority_class
source_version
indexed_at
```

Exact DB schema later.

---

# 67. Search Record Is Derived

Cannot be edited manually.

---

# 68. Source Version

SHOULD be retained when practical to detect stale record.

---

# 69. Index Timestamp

Useful for diagnostics:

```text
indexed_at
```

---

# 70. Public Search Result Security

Search candidate MUST correspond to currently permissible public context.

---

# 71. Stale Index Problem

Example:

```text
Project public
→ indexed
→ Admin archives Project
→ index update delayed
```

Search may temporarily still contain hit.

---

# 72. SEARCH-SEC-001 — Stale Hit Must Not Grant Access

Clicking stale result MUST still hit authoritative public route eligibility.

Archived Project returns unavailable/404 rather than page leak.

---

# 73. Result-Level Revalidation

For high-risk fields/entity types, query layer MAY also revalidate candidate before returning.

---

# 74. Recommended Public Strategy

Use both:

```text
prompt event-driven removal
+
authoritative route eligibility
```

---

# 75. Visibility Revocation

Search removal SHOULD be high priority for:

```text
public entity → hidden
Project archived
Portfolio item unpublished
```

---

# 76. Sensitive Value Rule

A private value that was previously public can remain in stale search snippets if not promptly removed.

Therefore visibility revocation MUST invalidate/reindex quickly.

---

# 77. SEARCH-INV-008 — Never Index Data More Private Than Its Search Context

Builder-only item cannot enter Public Search merely because it exists in Builder.

---

# 78. Builder Search

Builder Search indexes/queries only:

```text
allow_in_public_questionnaire_builder = true
```

plus context eligibility.

---

# 79. Builder Search Purpose

Long Builder item lists may need search for:

```text
Projects
Roles
Skills
Languages
Training
Portfolio items
Achievements
Media/Links
```

---

# 80. Builder Search Is Session-Aware

Search results MAY depend on:

```text
profile
template
section
limits
locale
current selected state
```

---

# 81. Builder Search Projection

Canonical:

```text
CURRENT MASTER
     ↓
BUILDER ELIGIBILITY
     ↓
TEMPLATE/CONTEXT RULES
     ↓
BUILDER SEARCH PROJECTION
     ↓
QUERY
```

---

# 82. Builder Search Can Expose Builder-Only Entity

Valid:

```text
show_on_public_site = false
allow_in_public_questionnaire_builder = true
```

---

# 83. But Builder-Only Result Must Remain in Builder

Target action:

```text
Add to questionnaire
```

not necessarily navigate to a Public Profile page that intentionally hides it.

---

# 84. Builder Search Result

Potential:

```text
type
title
compact metadata
thumbnail
eligibility
selected/not selected
add action
```

---

# 85. Raw Permission Flags

Builder UI does not need raw private permission architecture.

Server sends eligible result.

---

# 86. Session Version

Builder Search MAY use current session version to show selected state.

---

# 87. Permission Flip During Search

If Admin revokes Builder permission:

future query must stop returning item.

Existing session may show:

```text
Больше недоступно
```

after revalidation.

---

# 88. SEARCH-PQB-INV-001 — Search Result Does Not Guarantee Generate Eligibility Forever

Generate performs current authoritative revalidation.

---

# 89. Builder Search Cache

Must be scoped according to DOC-077:

```text
profile
session/context
locale
builder policy version
```

---

# 90. Admin Global Search

Admin Search is authorization-aware cross-domain discovery.

---

# 91. Admin Search Candidate Domains

Potential:

```text
Profile
Media
Portfolio
Emotional Sessions/Grids
Projects
Roles
Training
Achievements
Skills
Languages
Professional Links
Contacts
Questionnaires
Castings
Feedback
Opportunities
Notifications
BB Drafts
VOP Observations
Themes
Support Tickets
```

subject to permissions.

---

# 92. Audit Search

May have dedicated filtering/search UI rather than generic Admin Global Search because of volume/sensitivity.

---

# 93. Secrets Excluded

Admin Search MUST NEVER index:

```text
OAuth access token
API secret
password/hash
session secret
private signed token
```

---

# 94. Private Content Search

Feedback/Casting text MAY be searchable in Admin if product/security policy permits.

Such index must be:

```text
ADMIN-ONLY
authorization-aware
not shared with Public
```

---

# 95. Admin Search Permission

Result inclusion must consider current Admin rights.

---

# 96. Future Multiple Roles

Even if current deployment has a small Admin set, Search architecture must not assume every authenticated user can see every Admin entity forever.

---

# 97. Admin Result Types

Should identify:

```text
entity type
title/identity
matched context
current lifecycle/state
route
```

---

# 98. Example — Casting

```text
Casting
"Commercial Campaign X"
Stage/Status: Active
Matched: role text
```

---

# 99. Example — Contact

May show safe Admin Contact summary only to authorized Admin.

---

# 100. Admin Search Result Does Not Execute Mutations

Navigation only.

---

# 101. Quick Actions

Admin Search MAY offer limited safe quick actions later, but action still routes to owning Application Service and requires authorization.

---

# 102. Admin Search Ranking

Priority:

```text
exact identifier/name/title
→ exact structured field
→ prefix
→ semantic context
→ long narrative
```

---

# 103. Recency

Admin Search MAY use recency as tie-breaker.

Public professional search SHOULD not use upload recency as primary relevance.

---

# 104. Search Scope Filters

Admin MAY filter by:

```text
Media
Projects
Contacts
Questionnaires
Castings
Opportunities
```

---

# 105. Public Filters

Public Search SHOULD remain simple.

Potential result-category filter:

```text
Навыки
Проекты
Языки
Обучение
```

if result volume justifies.

---

# 106. Search UX — No Results

Public zero results SHOULD:

```text
state no matching professional data
offer relevant navigation
preserve query
```

---

# 107. No Result Is Not Error

Canonical distinction.

---

# 108. Zero Results Privacy

Do not reveal:

```text
“3 private projects matched but hidden”
```

---

# 109. Builder Zero Results

Can suggest:

```text
change query
change section
return to selected content
```

but never disclose ineligible items.

---

# 110. Admin Zero Results

Can suggest filters/alternate terms.

---

# 111. Autocomplete

MAY be supported.

---

# 112. Public Autocomplete

Suggestions must come only from public search projection.

---

# 113. Builder Autocomplete

Only Builder-eligible content.

---

# 114. Admin Autocomplete

Authorization-aware.

---

# 115. No Cross-Scope Shared Autocomplete Cache

Normative.

---

# 116. Search Snippets

Snippet should show the matched professional context.

---

# 117. Snippet Generation

Preferred deterministic source field extraction.

---

# 118. Highlighting

Matched term MAY be highlighted.

Escaping mandatory.

---

# 119. HTML Injection

Highlighting/search snippets MUST NOT create unsafe raw HTML from source/query.

---

# 120. Fuzzy Search

May use trigram/edit-distance style matching.

---

# 121. Fuzzy Search Limit

Avoid broad fuzzy behaviour that makes irrelevant results appear professional matches.

---

# 122. Typo Tolerance

Useful for:

```text
project names
skills
languages
```

within controlled thresholds.

---

# 123. Exact Professional Meaning

Example:

```text
B2
```

should not fuzzy-match arbitrary numeric/string data.

---

# 124. Synonyms

Could be supported through controlled synonym dictionary.

Example:

```text
верховая езда
конный спорт
```

only if semantically approved.

---

# 125. Synonym Authority

Synonym dictionary is product/search configuration, not AI-generated automatically in production.

---

# 126. Skill Aliases

SkillDefinition MAY own approved aliases later.

---

# 127. Search Fields by Domain — Actor Identity

Index:

```text
professional name
approved transliteration
professional title
location
```

---

# 128. Portfolio

Index:

```text
category
approved caption
```

Not:

```text
facial AI description
technical EXIF
filename
```

unless Admin Search specifically needs filename.

---

# 129. Skills

Index:

```text
skill name
approved aliases
category
```

Level MAY be returned but generally not needed as search text.

---

# 130. Languages

Index:

```text
language names
localized names
approved aliases
```

---

# 131. Projects

Index:

```text
title
localized title
approved short description
year/type
```

---

# 132. Roles

Index:

```text
role name
approved description
parent Project title
```

---

# 133. Training

Index:

```text
course/program
institution
approved description
```

---

# 134. Achievements

Index:

```text
title
issuer/context
```

---

# 135. Professional Links

Public search MAY index description/type.

It SHOULD NOT generally index raw full URL as prominent search content.

---

# 136. Media Technical Fields

Public Search excludes:

```text
filename
checksum
dimensions
codec
storage path
```

---

# 137. Admin Media Search

MAY include:

```text
original filename
media type
technical metadata
usage
```

to help operator locate source.

---

# 138. Questionnaire Search

Admin Search MAY index:

```text
questionnaire name
type
casting context
state
```

Not full historical content across every Revision unless separately justified.

---

# 139. Feedback Search

If implemented:

```text
sender
organization
type
subject
message text
```

authorized Admin-only.

---

# 140. Casting Search

Can include:

```text
casting title
role
project/client context
source summary
```

Admin-only.

---

# 141. Opportunity Search

Can include:

```text
casting/project title
stage
next action
```

Admin-only.

---

# 142. AI Draft Search

Optional.

If implemented, only Admin-authorized.

It must not mix generated drafts with confirmed facts without clear result typing.

---

# 143. VOP Search

Optional operational use.

Observation result clearly labeled as VOP Observation.

---

# 144. Search Index Lifecycle

Canonical:

```text
SOURCE CHANGE
      ↓
DOMAIN EVENT
      ↓
INDEX JOB
      ↓
BUILD SEARCH DOCUMENT
      ↓
UPSERT / DELETE
      ↓
INDEX CURRENT
```

---

# 145. Incremental Indexing

Default everyday update mechanism.

---

# 146. Incremental Job

Examples:

```text
ProjectPublished
→ upsert Project search entry

ProjectArchived
→ delete/disable Public search entry
```

---

# 147. Search Job Semantic Class

```text
CURRENT_STATE_DERIVED
```

per DOC-076.

---

# 148. Superseded Jobs

May safely coalesce.

Example:

```text
Project v9 reindex
Project v10 reindex
```

final index only needs current v10.

---

# 149. SEARCH-INV-009 — Historical Search Is Separate Concern

Do not force Public current search to index every historical Revision.

---

# 150. Full Reindex

System MUST support complete rebuild.

---

# 151. Full Reindex Source

Only authoritative current data + context policies.

---

# 152. Full Reindex Flow

```text
START REBUILD
 ↓
read authoritative eligible entities
 ↓
construct new index representation
 ↓
validate
 ↓
replace/switch index
```

---

# 153. PostgreSQL Implementation

May rebuild table/vector fields transactionally or in batches.

---

# 154. Dedicated Engine Future

Prefer new index generation + atomic alias switch where supported.

---

# 155. Full Rebuild Must Be Safe

Search may remain available on old index during rebuild where architecture supports.

---

# 156. Rebuild Failure

Old valid index remains, or Search enters degraded state.

Source data unaffected.

---

# 157. Search Index Deletion

If index is completely lost:

rebuild from Source.

---

# 158. Search Backup

Search index generally does not require backup.

---

# 159. Search Integrity

System SHOULD periodically/rebuild-check that index corresponds reasonably to eligible Source.

---

# 160. Index Drift

Potential states:

```text
MISSING_ENTRY
STALE_ENTRY
STALE_FIELDS
ORPHAN_ENTRY
```

---

# 161. VOP Integration

VOP may detect:

```text
index rebuild failure
search stale
search job final failure
public entity still searchable after archive
```

---

# 162. VOP Safe Automation

AUTO-4:

```text
reindex entity
full rebuild search index
delete orphan search record
```

when deterministic.

---

# 163. Search Status

System Status:

```text
HEALTHY
DEGRADED
UNAVAILABLE
```

---

# 164. Search Outage

Public site remains navigable.

---

# 165. Public UX During Outage

Search box may show:

```text
Поиск временно недоступен
```

with navigation still functional.

---

# 166. Admin Search Outage

Direct Admin navigation/workspaces remain.

---

# 167. Builder Search Outage

Builder MUST still permit manual browsing/selection of eligible sections/items where possible.

Search outage MUST NOT make Builder unusable.

---

# 168. SEARCH-INV-010 — Search Is Convenience, Not Functional Gate

Critical product workflows cannot require Search to exist.

---

# 169. Search Performance

Target should make profile-scale search feel immediate.

Exact latency NFR belongs performance docs.

---

# 170. Query Limits

Server SHOULD bound:

```text
query length
page size
offset/cursor
fuzzy cost
```

---

# 171. Pagination

Public profile-scale corpus may often fit in small result set.

Architecture should nevertheless support bounded result count.

---

# 172. Result Count

Do not dump hundreds of matches by default.

---

# 173. Top Results

Return professionally meaningful top results with optional:

```text
Показать ещё
```

---

# 174. Search Facets

Not mandatory baseline.

---

# 175. Admin Facets

May become useful:

```text
entity type
state
date
```

---

# 176. Builder Facets

Can map to Builder sections rather than generic search facets.

---

# 177. Search URL State

Public search query MAY be reflected in URL if useful for navigation/accessibility.

---

# 178. Query Privacy

Admin/private search queries should not be leaked into public analytics/referrer unnecessarily.

---

# 179. Search Analytics

May capture:

```text
public search used
query category
result clicked
zero-result
```

subject to privacy.

---

# 180. Raw Query Logging

Avoid storing sensitive Admin search strings unnecessarily.

---

# 181. Public Query Analytics

Could store normalized query if policy permits.

Avoid using it to automatically alter professional data.

---

# 182. Search Insights

Analytics may recommend:

```text
common skill searched but hard to find
common zero-result terms
```

---

# 183. Search Insight Limit

Recommendation only.

No auto-create skill or alias from user queries.

---

# 184. Marketing/SEO Search

Internal Search is independent from external search engines/SEO.

---

# 185. Search Engine Indexing

Public SEO pages use SEO architecture, not internal Search Index as truth.

---

# 186. Search and Deep Links

Result routes MUST be stable public/Admin routes.

---

# 187. Context Preservation

Public search result may carry query/filter return state.

---

# 188. Portfolio Result

Opening a Portfolio filter should preserve:

```text
category/filter
scroll context
```

where UX supports it.

---

# 189. Search and Casting Quick View

Search may be accessible inside Casting Quick View or profile quick-navigation.

It still uses public safe index.

---

# 190. Search and Questionnaire

Public Search SHOULD NOT index every field from a Questionnaire Revision separately if identical authoritative Source already exists.

Questionnaire remains a result/navigation surface, not duplicate fact index.

---

# 191. Search Questionnaire Result

May show:

```text
Актёрская анкета
```

as route/action result for queries like:

```text
анкета
questionnaire
```

through static navigation aliases.

---

# 192. Navigation Aliases

Search MAY index canonical navigation concepts:

```text
анкета
видео
портфолио
контакты
эмоции
```

to route users quickly.

---

# 193. Navigation Alias Is Not Master Data

It is Search/navigation configuration.

---

# 194. Search Safety for Video

Query:

```text
видеовизитка
```

should route to Video Intro if available.

---

# 195. Search and P0 Actions

Search may improve one-action budget for:

```text
Video
Questionnaire
Skill
Project
```

---

# 196. Search Accessibility

Search UI MUST support:

```text
keyboard navigation
visible focus
semantic input label
clear submit/clear controls
announced result count
announced no-results/error
```

---

# 197. Combobox Pattern

If autocomplete used, it MUST follow accessible combobox/listbox semantics.

---

# 198. Keyboard

Arrow-key support for suggestions MAY supplement normal Tab/Enter navigation.

No keyboard trap.

---

# 199. Screen Reader Result

Each result must expose:

```text
title
type/context
destination
```

---

# 200. Search Highlight

Must not break screen-reader reading order.

---

# 201. Mobile Search

Must be fully usable with touch/virtual keyboard.

---

# 202. Search Overlay

If modal/overlay pattern used:

focus management follows DOC-050.

---

# 203. Query Clear

User must be able to clear search easily.

---

# 204. Responsive Result Density

Mobile may show condensed result metadata.

Must preserve matched meaning.

---

# 205. Search Privacy Boundary — Public

The safest design is:

```text
index only already-public-searchable values
```

rather than index all and filter query results later.

---

# 206. Defense in Depth

Still perform final access checks at resource route.

---

# 207. Search Privacy Boundary — Builder

Index/query only Builder-authorized content.

---

# 208. Search Privacy Boundary — Admin

Index may include private content but query service checks Admin permissions.

---

# 209. Separate Physical Indexes

Implementation MAY use physically separate:

```text
public_search
builder_search
admin_search
```

or logical scope columns.

---

# 210. Preferred Safety

Where private content is involved, stronger logical/physical separation is preferred over one universal text blob.

---

# 211. Public and Admin Search Corpus

MUST NOT share one materialized document containing both public and private text unless access isolation is rigorously guaranteed.

---

# 212. Search Index Encryption

If Admin private search index stores sensitive text, storage protection follows database security policy.

---

# 213. Search Logs

No private result payload in ordinary debug logs.

---

# 214. Query Error Handling

Invalid query:

```text
safe validation error
```

not SQL parser error.

---

# 215. Backend Failure

Return standardized SearchUnavailable.

---

# 216. No Raw SQL Error

Never expose:

```text
syntax error at or near tsquery...
```

to user.

---

# 217. Search Query Builder

Central component SHOULD safely translate normalized query into backend query.

---

# 218. No String-Concatenated SQL

Use parameterized queries.

---

# 219. PostgreSQL FTS

If used:

query normalization must safely handle special tsquery characters.

---

# 220. Trigram

Can improve typo tolerance.

Use indexes and thresholds to bound cost.

---

# 221. Search Ranking Inputs

Allowed:

```text
textual match strength
field importance
entity professional priority
explicit featured/primary context where relevant
small deterministic tie-breakers
```

---

# 222. Search Ranking Inputs Not Allowed by Default

```text
attractiveness score
facial analysis
AI emotional score
visitor popularity
social follower count
conversion score
```

---

# 223. Featured Content

`Featured` MAY affect tie-breaking for general Project result if textual match is equal.

Exact textual match still dominates.

---

# 224. Primary Portfolio

Primary Close-Up/Full Body may receive contextual priority for generic query:

```text
фото
```

but not override exact category match for another photo type.

---

# 225. Search Ranking Explainability

Ranking should be simple enough that team can explain why result appeared.

---

# 226. No Black-Box Ranking Requirement

Baseline does not require machine-learned ranking.

---

# 227. Search Index Update Triggers

At least:

```text
ProfileChanged
PortfolioPublished/Archived/Changed
PrimaryPortfolioChanged if search projection affected
ProjectPublished/Archived/Changed
RoleChanged
TrainingChanged
AchievementChanged
SkillChanged
LanguageChanged
ProfessionalLinkChanged if indexed
BuilderEligibilityChanged
LocaleContentChanged
```

---

# 228. Contact Changes

Only relevant to Admin Search unless a specific public navigation alias depends on Contacts.

---

# 229. Questionnaire Changes

May update navigation/result availability:

```text
Primary Questionnaire published/archived
```

---

# 230. Search Event Batching

Rapid batch edits MAY coalesce into one reindex per entity/current state.

---

# 231. Bulk Migration

Large migrations SHOULD support full rebuild after data migration rather than emitting millions of unnecessary incremental events at current scale.

---

# 232. Search Reindex Idempotency

Same current Source produces same semantic Search Projection.

---

# 233. Search Job Failure

Does not roll back Source mutation.

---

# 234. High-Priority Removal

Archive/privacy-related search removal SHOULD receive higher operational priority than non-critical text update.

---

# 235. Search Freshness Metadata

Admin diagnostics MAY expose:

```text
last indexed
index version
pending reindex
```

---

# 236. Public UI Should Not Show Technical Index Age

Unless there is a meaningful degraded-state message.

---

# 237. Search Rebuild Admin Action

System Status/Admin MAY offer:

```text
Rebuild Search Index
```

as safe technical action.

---

# 238. VOP Rebuild

May execute automatically if deterministic and safe.

---

# 239. Search Health Check

Should validate:

```text
backend reachable
simple query succeeds
index metadata available
```

---

# 240. Full Rebuild State

Canonical:

```text
IDLE
REBUILDING
READY
FAILED
```

as processing state, not business state.

---

# 241. Search Version

Index SHOULD have:

```text
schema_version
```

or equivalent.

---

# 242. Schema Change

If searchable document format changes:

```text
build new version/reindex
```

---

# 243. Deployment Compatibility

New code must not assume index schema updated before migration/rebuild completes unless deployment order guarantees it.

---

# 244. Rebuild on Deployment

May be deployment step when index schema changes.

---

# 245. Search Index Backup

Generally unnecessary.

Rebuild from authoritative data.

---

# 246. Disaster Recovery

After DB restore:

```text
Search = stale/empty
→ full rebuild
```

No professional data lost.

---

# 247. Search and Cache

Search result cache is optional.

---

# 248. Cache Key

If used:

```text
scope
locale
normalized query
filters
index/schema version
```

---

# 249. Public/Builder/Admin Search Cache Separation

Mandatory.

---

# 250. Search Cache Invalidation

Can rely on short TTL + index version where result cache is low-risk.

For visibility revocation, actual target access remains authoritative.

---

# 251. Builder Search Cache

Must include Builder policy/session context when selected state or eligibility differs.

---

# 252. Search and Background Jobs

Incremental/full indexing run via durable background jobs when not trivial synchronous.

---

# 253. Immediate Critical Removal

System MAY perform synchronous delete/disable from search projection for privacy-sensitive revocation plus async reconciliation.

---

# 254. Search Write Ownership

Only Search Indexer writes Search Index.

Domain modules publish state changes/events.

---

# 255. SEARCH-INV-011 — Domain Does Not Write Search Tables Directly

Avoid:

```text
ProjectService
→ UPDATE public_search_index directly
```

unless behind Search application interface.

---

# 256. Search Projection Builder

Responsible for converting each eligible domain entity into normalized search document.

---

# 257. Search Projection Builder Must Reuse Visibility Policies

Do not duplicate inconsistent:

```text
if published && public...
```

logic independently from Public Projection system.

---

# 258. Search and Archived Entities

Public:

```text
exclude
```

Admin:

```text
may include when archive search/filter requested
```

---

# 259. Historical Revision Search

Admin MAY provide separate revision-history search later.

Not baseline Global Search requirement.

---

# 260. Search and Deleted Data

Hard-deleted current entity MUST be removed during reindex/reconciliation.

Historical snapshot content remains governed by historical access, not Public current search.

---

# 261. Search and Redaction

Privacy redaction must trigger removal/rebuild of affected indexes.

---

# 262. Search and Public Builder Permissions

Each item-level flag affects Builder Search eligibility.

---

# 263. Search and Main Portfolio Mandatory Photos

Builder search may help choose extra Portfolio.

It MUST NOT allow replacing mandatory Primary Close-Up/Full Body with arbitrary search result.

---

# 264. Search Result Action

For mandatory fields, Builder may show:

```text
Required
Already included
```

rather than removable/addable arbitrary item.

---

# 265. Search and Emotional Grid

Builder/Public Search result SHOULD represent approved Grid/session meaning, not individual unapproved cell source.

---

# 266. Search and Project Role Media

Public search may route Project/Role.

It should not expose every underlying RoleMedia image as separate search result unless explicit UX value exists.

---

# 267. Search and Professional Links

Query:

```text
видеовизитка
```

can map directly to the professional media/link surface.

---

# 268. Search Navigation Configuration

Canonical aliases SHOULD be localized.

Example RU:

```text
анкета
видео
контакты
портфолио
навыки
языки
проекты
```

---

# 269. English aliases

```text
questionnaire
video
contact
portfolio
skills
languages
projects
```

---

# 270. Search Result Priority for Navigation Alias

For exact navigation task:

```text
"анкета"
```

Questionnaire navigation action may rank above content mentions containing word.

---

# 271. Search Result Grouping

Public MAY group:

```text
Навигация
Навыки и языки
Проекты
Портфолио
Обучение
```

if useful.

---

# 272. Avoid Result Overload

Do not return 20 nearly identical media results when one category route is more useful.

---

# 273. Search Relevance and Content Hierarchy

Search results follow professional action model:

```text
find exact fact
→ show evidence/context
→ allow continuation/action
```

---

# 274. No Dead End

Every public search result route SHOULD provide continuation:

```text
Questionnaire
Contact
navigation
```

according to global UX.

---

# 275. Search Rate Limiting

Public Search MAY be rate-limited to prevent abuse/resource exhaustion.

---

# 276. Search Rate Limit Is Not User Registration

No mandatory account required.

---

# 277. Search Bot Traffic

Internal Search endpoint need not be crawlable/indexable by external search bots.

---

# 278. Search Endpoint

Specific REST/Server Action design deferred to DOC-100–103.

---

# 279. Search Error Codes

Recommended:

```text
SEARCH_QUERY_INVALID
SEARCH_UNAVAILABLE
SEARCH_SCOPE_DENIED
SEARCH_REBUILD_FAILED
SEARCH_INDEX_STALE
```

---

# 280. Public Error Behaviour

`SEARCH_UNAVAILABLE` gives recoverable UI without page crash.

---

# 281. Builder Error Behaviour

Search unavailable:

manual Builder item browsing still works.

---

# 282. Admin Error Behaviour

Global Search unavailable:

direct workspace navigation remains.

---

# 283. Observability

Minimum metrics:

```text
query count
latency
zero-result rate
error rate
index size
index freshness lag
reindex queue
rebuild duration
stale/orphan detection
```

---

# 284. Public Search Metrics

May include:

```text
top normalized queries
zero-result queries
result click-through
```

subject to privacy policy.

---

# 285. Admin Search Metrics

Avoid storing potentially sensitive raw query text by default.

Operational latency/error metrics sufficient.

---

# 286. Search Performance Diagnostics

Slow-query logging MUST redact/limit private query terms where appropriate.

---

# 287. Index Size

Not a business KPI.

Used only operationally.

---

# 288. SEARCH-AP-001

**One search index containing public and private data with client-side filters**

---

# 289. SEARCH-AP-002

**Public Search indexes hidden Contacts**

---

# 290. SEARCH-AP-003

**Builder Search reuses Public Search and loses Builder-only permitted content**

---

# 291. SEARCH-AP-004

**Builder Search uses Admin Search and leaks private data**

---

# 292. SEARCH-AP-005

**Search index treated as current publication authority**

---

# 293. SEARCH-AP-006

**Archived Project remains accessible because search hit existed**

---

# 294. SEARCH-AP-007

**Search ranking based on actress attractiveness**

---

# 295. SEARCH-AP-008

**AI invents synonym/fact and presents it as profile match**

---

# 296. SEARCH-AP-009

**Most-clicked Project automatically becomes top result for unrelated query**

---

# 297. SEARCH-AP-010

**Latest upload outranks exact match by default**

---

# 298. SEARCH-AP-011

**Raw technical media filenames dominate Public Search**

---

# 299. SEARCH-AP-012

**Search outage makes Questionnaire inaccessible**

---

# 300. SEARCH-AP-013

**Search result serializes full ORM entity**

---

# 301. SEARCH-AP-014

**Admin search index stores OAuth/API secrets**

---

# 302. SEARCH-AP-015

**Autocomplete reveals private entity name before authorization**

---

# 303. SEARCH-AP-016

**Search result cache shared between Public and Admin**

---

# 304. SEARCH-AP-017

**Fuzzy matching so broad that irrelevant fact looks like exact professional match**

---

# 305. SEARCH-AP-018

**Historical Questionnaire facts mixed into current Public Search as if current**

---

# 306. SEARCH-AP-019

**Search query interpolated directly into SQL**

---

# 307. SEARCH-AP-020

**No full rebuild capability**

---

# 308. SEARCH-AP-021

**Rebuild edits Source entities**

---

# 309. SEARCH-AP-022

**Index backup considered necessary to recover professional data**

---

# 310. SEARCH-AP-023

**Search aliases stored as duplicated profile facts**

---

# 311. SEARCH-AP-024

**Zero-result UI reveals existence of hidden matches**

---

# 312. SEARCH-AP-025

**Search recommendation automatically edits Portfolio/Skills/Projects**

---

# 313. Search Quality Gate

Перед implementation MUST быть определены:

- [ ] Public Search scope;
- [ ] Builder Search scope;
- [ ] Admin Search scope;
- [ ] eligible domains;
- [ ] excluded/private domains;
- [ ] field allowlists;
- [ ] locale handling;
- [ ] query normalization;
- [ ] exact/prefix/fuzzy semantics;
- [ ] relevance ordering;
- [ ] navigation aliases;
- [ ] result DTO;
- [ ] public route eligibility;
- [ ] Builder permission enforcement;
- [ ] Admin authorization;
- [ ] incremental indexing;
- [ ] full rebuild;
- [ ] stale-hit protection;
- [ ] reindex event mapping;
- [ ] index schema version;
- [ ] backend implementation;
- [ ] cache boundaries;
- [ ] background job model;
- [ ] failure fallback;
- [ ] observability;
- [ ] privacy tests;
- [ ] accessibility.

---

# 314. Public Search Specification Template

```text
Search Context:
PUBLIC

Source Projection:
Public Search Projection

Eligible Types:
Skills
Languages
Projects
Roles
Portfolio Categories
Training
Achievements

Excluded:
Private/Admin/Builder-only data

Locale:
request locale

Ranking:
Exact
Phrase
Prefix
Partial
Professional field priority

Authorization:
public eligibility before indexing
final route revalidation

Failure:
navigation remains available
```

---

# 315. Builder Search Specification Template

```text
Search Context:
PUBLIC_BUILDER

Source:
Builder Eligibility Projection

Eligibility:
same profile
active
allow_in_public_questionnaire_builder
template/context permits

Session:
selection state may be included

Generation:
server revalidates independently

Public Site Visibility:
not required

Failure:
manual Builder browsing remains
```

---

# 316. Admin Search Specification Template

```text
Search Context:
ADMIN

Source:
Admin-authorized Search Projection

Entities:
Projects
Media
Contacts
Questionnaires
Castings
Feedback
Opportunities
etc.

Authorization:
per result/current Admin

Secrets:
never indexed

Archived:
searchable with explicit context/filter

Actions:
navigate to owning workspace
```

---

# 317. Search Relevance Matrix

| Match | Relative Priority |
|---|---|
| Exact structured value | Highest |
| Exact title/name | Highest |
| Exact phrase | High |
| Prefix | High |
| Strong partial | Medium |
| Narrative mention | Lower |
| Fuzzy distant match | Low/omit |

---

# 318. Public Entity Priority Matrix

When text score is comparable:

| Type | Priority |
|---|---:|
| Navigation/P0 action exact alias | Very High |
| Structured Profile fact | Very High |
| Skill / Language | High |
| Portfolio professional category | High |
| Project / Role | High |
| Training / Achievement | Medium |
| Long narrative text | Lower |

---

# 319. Search Update Matrix

| Domain Event | Public | Builder | Admin |
|---|---:|---:|---:|
| SkillChanged | ✓ | ✓ | ✓ |
| LanguageChanged | ✓ | ✓ | ✓ |
| PortfolioPublished | ✓ | ✓ | ✓ |
| PortfolioArchived | remove | remove | update |
| ProjectPublished | ✓ | ✓ | ✓ |
| ProjectArchived | remove | remove | update |
| BuilderPermissionChanged | maybe no | ✓ | ✓ |
| ContactChanged | usually no | if eligible | ✓ |
| FeedbackCreated | no | no | ✓ |
| CastingChanged | no | no | ✓ |
| OpportunityChanged | no | no | ✓ |

---

# 320. E2E-SEARCH-001 — Public Skill

Search exact skill.

Skill result ranks above narrative mentions.

---

# 321. E2E-SEARCH-002 — Language

Search English/Английский in matching locale.

Confirmed language result returned.

---

# 322. E2E-SEARCH-003 — Private Contact

Private Contact value never appears in public results/autocomplete/snippets.

---

# 323. E2E-SEARCH-004 — Builder-Only Item

```text
public=false
builder=true
```

Expected:

```text
absent Public Search
present Builder Search
```

---

# 324. E2E-SEARCH-005 — Builder-Forbidden Public Item

```text
public=true
builder=false
```

Expected:

```text
may appear Public Search
absent Builder Search
```

---

# 325. E2E-SEARCH-006 — Permission Revocation

Builder item indexed.

Permission revoked.

Next authoritative Builder search/generation no longer permits it.

---

# 326. E2E-SEARCH-007 — Archived Project

Project archived before index update.

Old result/direct click cannot expose archived public content.

---

# 327. E2E-SEARCH-008 — Incremental Reindex

Change Project title.

Reindex updates result without full rebuild.

---

# 328. E2E-SEARCH-009 — Job Coalescing

Project v10 and v11 indexing queued.

Final Search reflects v11.

---

# 329. E2E-SEARCH-010 — Full Rebuild

Delete Public Search Index.

Full rebuild reconstructs current results from Source.

---

# 330. E2E-SEARCH-011 — Rebuild Failure

Full rebuild fails.

Source data unchanged; site direct navigation remains usable.

---

# 331. E2E-SEARCH-012 — Search Outage

Disable Search backend.

Profile/Portfolio/Questionnaire/Contact direct routes remain functional.

---

# 332. E2E-SEARCH-013 — Builder Search Outage

Builder manual list/sections remain usable.

---

# 333. E2E-SEARCH-014 — Admin Search Authorization

Less-privileged future Admin cannot receive more-privileged cached/indexed result.

---

# 334. E2E-SEARCH-015 — Secret Exclusion

Search index inspection confirms no OAuth/API/session secrets.

---

# 335. E2E-SEARCH-016 — Locale

RU query returns RU presentation.

EN query/context returns EN projection.

No cross-locale cache contamination.

---

# 336. E2E-SEARCH-017 — Navigation Alias

Search:

```text
анкета
```

returns high-priority Questionnaire navigation result.

---

# 337. E2E-SEARCH-018 — No Popularity Ranking

Increase clicks on unrelated Project.

Exact Skill query ranking remains Skill-first.

---

# 338. E2E-SEARCH-019 — No AI Score

No result contains attractiveness/emotional suitability ranking.

---

# 339. E2E-SEARCH-020 — SQL Injection

Malicious query is safely parameterized/normalized and cannot alter DB.

---

# 340. E2E-SEARCH-021 — Autocomplete Privacy

Autocomplete never reveals hidden/private result names.

---

# 341. E2E-SEARCH-022 — Zero Results

No-match query returns accessible zero-result state, not error.

---

# 342. E2E-SEARCH-023 — Fuzzy Typo

Minor typo in a known Skill MAY find the Skill if configured threshold supports it.

Irrelevant distant values are excluded.

---

# 343. E2E-SEARCH-024 — Search Cache Scope

Public, Builder and Admin results for same query cannot cross-scope cache.

---

# 344. E2E-SEARCH-025 — Search Accessibility

Keyboard-only user can:

```text
focus search
enter query
navigate results
open result
clear search
```

---

# 345. Search Traceability

Canonical:

```text
SOURCE ENTITY
      ↓
ELIGIBILITY POLICY
      ↓
SEARCH PROJECTION
      ↓
SEARCH INDEX
      ↓
QUERY
      ↓
RESULT
      ↓
AUTHORITATIVE TARGET
```

---

# 346. Public Search Traceability

```text
Skill
 ↓
Public Eligibility
 ↓
Public Search Entry
 ↓
Search Result
 ↓
Skills Section
```

---

# 347. Builder Search Traceability

```text
Project
 ↓
allow_in_public_questionnaire_builder
 ↓
Template Eligibility
 ↓
Builder Search Result
 ↓
Add to Session
 ↓
Generate Revalidation
```

---

# 348. Admin Search Traceability

```text
Casting
 ↓
Admin Authorization
 ↓
Admin Search Projection
 ↓
Search Result
 ↓
Casting Workspace
```

---

# 349. Stale-Hit Defense

```text
STALE SEARCH ENTRY
      ↓
USER OPENS RESULT
      ↓
AUTHORITATIVE ROUTE CHECK
      ↓
ALLOW
or
DENY / NOT AVAILABLE
```

---

# 350. Final Search Architecture

```text
                   AUTHORITATIVE DATA
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
    PUBLIC POLICY     BUILDER POLICY    ADMIN POLICY
          │                │                │
          ▼                ▼                ▼
    PUBLIC SEARCH     BUILDER SEARCH    ADMIN SEARCH
      PROJECTION        PROJECTION        PROJECTION
          │                │                │
          ▼                ▼                ▼
       INDEX A          INDEX B          INDEX C
          │                │                │
          ▼                ▼                ▼
     Public User      Builder User       Admin User
```

Physical implementation MAY combine indexes only if logical/privacy isolation remains equivalent.

---

# 351. Search Compliance Criteria

Реализация соответствует DOC-078, если:

1. Public, Builder and Admin search contexts are explicitly separated;
2. Search Index is derived and rebuildable;
3. Public Search indexes only public-search-eligible data;
4. Builder Search uses Builder eligibility, not Public visibility;
5. Admin Search remains authorization-aware;
6. private Contacts/Feedback/Casting data cannot leak into Public Search;
7. secrets are never searchable;
8. exact structured matches receive highest relevance;
9. narrative/fuzzy matches do not override stronger professional matches;
10. AI attractiveness/suitability ranking is absent;
11. popularity is not professional authority;
12. upload recency is not default relevance authority;
13. Search result DTO is field-allowlisted and compact;
14. locale is respected;
15. internal navigation aliases may improve professional task access;
16. incremental reindex is event-driven;
17. full rebuild exists;
18. indexing jobs are durable/idempotent;
19. current-derived indexing may coalesce superseded work;
20. stale index cannot grant access to now-private content;
21. visibility revocations trigger prompt removal/reindex;
22. direct navigation works during Search outage;
23. Builder remains usable if Builder Search fails;
24. Search cache scopes Public/Builder/Admin separately;
25. query input is parameterized and bounded;
26. autocomplete follows same privacy rules as full results;
27. zero-results disclose no private match information;
28. Search is accessible from keyboard/screen reader;
29. Search analytics do not auto-edit professional content;
30. entire index can be reconstructed after disaster recovery.

---

# 352. Финальная доктрина

> **Search subsystem должен сокращать путь casting professional к нужному профессиональному факту или доказательству, но не создавать новую интерпретацию актрисы. Public Search ищет только внутри разрешённого публичного профессионального профиля, Builder Search — только внутри текущего Builder eligibility, а Admin Search — внутри авторизованного административного контура. Поиск ранжирует прежде всего точность и профессиональную структуру данных, а не популярность, AI-оценку или визуальную привлекательность. Индекс всегда остаётся производным: его можно удалить и полностью восстановить из Source of Truth без потери профессиональной информации.**