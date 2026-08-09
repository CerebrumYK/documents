# MEDIA MIGRATIONS
## DOC-163 — immutable originals, checksums, storage relocation, derivatives, public URLs и media reconciliation

**Статус:** Canonical / Required

---

## 1. Назначение

Документ определяет перенос images/video/audio/documents из legacy storage в canonical Media architecture.

> **Media migration preserves the original byte identity and provenance first. Storage paths may change and derivatives may be regenerated, but an original is never silently recompressed, cropped, retouched or replaced merely to simplify migration.**

---

## 2. Media classes

Inventory and migrate separately:

```text
IMAGE
VIDEO
AUDIO
DOCUMENT
EVIDENCE_ATTACHMENT
FEEDBACK_ATTACHMENT
HELP_ATTACHMENT
DERIVED_ARTIFACT
```

## 3. Original vs derivative

Classify every file as:

```text
ORIGINAL_CONFIRMED
DERIVED_CONFIRMED
UNKNOWN_PROVENANCE
```

Unknown file cannot become authoritative original automatically when a better source may exist.

## 4. Checksums

Before move/copy compute cryptographic checksum (e.g. SHA-256) for originals. After transfer verify byte-for-byte checksum.

Migration manifest stores:

```text
legacy path/id
new MediaAsset id
size
checksum
classification
status
```

## 5. Immutable original rule

Original bytes are copied/moved without image quality conversion. EXIF may be removed only from derived public versions, not by rewriting immutable original baseline.

## 6. Storage layout

Target storage is application-managed under configured root such as `/data/kate-actor`, with server-generated paths. Legacy filename preserved as metadata, not storage authority.

## 7. Path traversal/symlinks

Inventory symlinks and resolve policy explicitly. Migration must not follow malicious/out-of-root symlink blindly. Normalize/canonicalize filesystem roots before copy.

## 8. Permissions

Target originals/private attachments use private filesystem permissions and are served only through application resolver. Do not copy permissive legacy web-root ACL blindly.

## 9. Metadata

Preserve confirmed:

- original filename;
- MIME/type;
- dimensions;
- duration;
- creation/import source if known;
- capture/shooting metadata when domain-owned;
- legacy ID/path in migration provenance.

Do not promote EXIF fields to professional Source automatically.

## 10. MIME validation

During migration re-probe media content. Extension mismatch or corrupt file is flagged; do not silently relabel invalid bytes.

## 11. Corruption

Corrupt/zero-byte/unreadable media:

```text
INVALID_SOURCE
REQUIRES_REVIEW
```

Record relation to referencing entities so Human can repair.

## 12. Duplicate bytes

Checksum-identical files may be duplicate candidates. Deduplication is permitted only if it does not destroy distinct source provenance/context and target Media model supports shared bytes safely.

Do not auto-merge different professional records solely because bytes match.

## 13. Role/project photos

Preserve relation/context separately. MediaAsset does not own role-specific caption/order/visibility if canonical domain defines relation object.

## 14. Emotional portfolio

Preserve shooting date/source shoot relation separately from MediaAsset. Original photos remain originals. Existing grid composite is classified derived; crop/grid config migrates if reconstructable.

## 15. Grid composites

If only flattened legacy grid exists with no cell config, it may migrate as legacy derived artifact/reference but must not fabricate cell crop coordinates/Human confirmations.

## 16. Main Portfolio categories

Map photo category only when legacy category is explicit/trustworthy. Filename/AI image analysis cannot silently assign mandatory Close-Up/Full Body Source type.

## 17. Video/audio

Originals preserved; web/provider derivatives can be regenerated through media pipeline. Probe duration/codecs after migration.

## 18. Professional external media

External YouTube/Vimeo/SoundCloud URL is `ProfessionalLink`, not MediaAsset, unless actual file exists/imported. Inventory must not create fake local media row for URL-only source.

## 19. Documents

Private PDFs/certificates/casting/support attachments remain private according to owning domain. Do not put all documents into public Media Library.

## 20. Attachment separation

Feedback/Help attachments are operational/private even if binary storage implementation is shared. Migration preserves access class and owner relation.

## 21. Derived artifacts

Preferred strategy:

```text
migrate originals/config
→ regenerate thumbnails/web/video/posters/QR/PDF as needed
```

Do not treat cached derivative as irreplaceable source.

## 22. Regeneration version

Derived artifacts generated after migration use current renderer/processor version and are clearly new derived generation; historical professional semantic snapshots remain unchanged.

## 23. Public URLs

Legacy raw filesystem/static paths are translated to stable application-controlled routes when needed. Do not expose new filesystem layout.

## 24. URL redirects

If public legacy media URLs have external references, configure explicit redirect/resolver strategy where feasible. Redirect never points to private original without access validation.

## 25. QR/PDF

Legacy QR/PDF can be retained as historical artifacts, but QR reuse requires decode verification. New PDF/QR regeneration uses frozen snapshot/canonical URL rules.

## 26. Media publication eligibility

Migration of file does not automatically enable:

```text
show_on_public_site
questionnaire eligibility
Builder eligibility
social publishing rights
```

Map only known legacy semantics; otherwise review/private.

## 27. Social rights

Presence in legacy gallery does not prove third-party social distribution rights. `allow_social_publishing` defaults conservative unless explicit source exists.

## 28. Orphan files

Classify:

```text
REFERENCED
ORPHAN_CANDIDATE
SYSTEM_DERIVED
TEMPORARY
UNKNOWN
```

Do not delete until migration acceptance/retention review.

## 29. Missing files

DB record pointing to absent file produces migration defect report. Preserve metadata/reference where needed for Human repair rather than silently dropping record.

## 30. Batch transfer

Use bounded batches, copy to temporary target, checksum, atomically register/finalize where practical. Interrupted copy does not mark file migrated.

## 31. Idempotency

Same legacy media ID/checksum mapping should reuse existing target mapping. Rerun must not duplicate MediaAsset/domain relations.

## 32. Cleanup

Legacy media removal only after:

- checksums pass;
- reference counts reconcile;
- public/Admin render smoke tests;
- backup confirmed;
- Human sign-off.

## 33. Security

Migration tool must not:

- log private file content;
- expose absolute paths publicly;
- follow out-of-root paths;
- make private attachments world-readable;
- upload to external provider automatically.

## 34. Validation metrics

Per class:

```text
source file count/bytes
migrated count/bytes
checksum matches
missing/corrupt count
orphan count
relation count
derivative regeneration failures
```

## 35. Anti-patterns

Forbidden:

1. Recompress originals.
2. Crop originals.
3. Strip original metadata by rewriting source.
4. Treat thumbnail as original.
5. Expose `/data/...` URL.
6. Copy all files to public web root.
7. URL-only YouTube row becomes fake MediaAsset.
8. Private support attachment becomes portfolio image.
9. Legacy grid image fabricates editable grid config.
10. Missing file record silently deleted.
11. Byte duplicate auto-merges professional contexts.
12. Old public visibility automatically means social publishing rights.
13. Delete legacy storage before checksum/reference validation.

## 36. Acceptance criteria

`AC-MEDMIG-001` Original media bytes are checksum-preserved.  
`AC-MEDMIG-002` Original and derivative provenance is classified explicitly.  
`AC-MEDMIG-003` Target storage paths are server-controlled/private as required.  
`AC-MEDMIG-004` Corrupt/missing media is reported, not silently discarded.  
`AC-MEDMIG-005` Domain relations/captions/orders remain separate from binary identity.  
`AC-MEDMIG-006` Emotional Grid config is migrated only when actually known.  
`AC-MEDMIG-007` External ProfessionalLinks remain distinct from local media.  
`AC-MEDMIG-008` Feedback/Help/evidence attachments preserve private access class.  
`AC-MEDMIG-009` Derived artifacts are rebuildable and need not be treated as Source.  
`AC-MEDMIG-010` Public URLs do not expose target filesystem paths.  
`AC-MEDMIG-011` Social/Builder/QNR/public permissions are mapped conservatively.  
`AC-MEDMIG-012` Migration rerun does not duplicate MediaAsset/relations.

---

## 37. Финальная доктрина

> **Media migration is successful when every authoritative original can be proven byte-identical, every contextual relation and access class is preserved, corrupt/missing/ambiguous files are explicit, and all public delivery moves behind stable application-controlled URLs. Derivatives are regenerated rather than mistaken for Source.**
