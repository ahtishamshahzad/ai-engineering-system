---
name: file-storage
description: Use to plan file upload and storage — object storage vs disk, upload paths (direct vs presigned), server-side validation (type/size/content), key naming, access control on files, and serving (signed URLs, CDN).
---

# File Storage

## Purpose

Design how files enter, live in, and leave the system: upload path, validation, storage backend, key scheme, access control, and serving — with the server enforcing every limit clients claim to respect.

## When to Use

- When the system accepts uploads or serves stored files (avatars, documents, media, exports).
- **Not** for static app assets shipped with the build.

## Inputs

- File use cases: types, size ranges, volume, retention, who may read each class.
- Deployment shape (object storage availability), authorization model (`ownership-authorization`).

## Discovery Questions

- What file classes exist, with max size, allowed types, and expected volume?
- Public files (avatars) vs private (documents) — who may read each?
- Do large uploads justify presigned direct-to-storage upload, or is proxy-through-API fine?
- Post-upload processing (resize, scan, transcode) — sync or background?

## Responsibilities

- Choose backend: **object storage** (S3-compatible) for anything multi-instance or durable; local disk only for single-node/dev with a recorded migration path.
- Choose upload path per class: proxy-through-API (small files, simplest control) vs **presigned upload** (large files; constrain type/size in the presign policy, verify after upload).
- Validate server-side: size limits, allowed types by **content inspection** (magic bytes), not extension or client MIME; reject or sanitize dangerous types (SVG/HTML execute in browsers).
- Define keys: generated, non-guessable, scoped (`tenant/user/uuid.ext`) — never client-supplied filenames as keys; original name stored as metadata only.
- Enforce **access control on read**: private files served via ownership-checked endpoints or short-lived signed URLs (`ownership-authorization`); public classes explicitly marked.
- Plan lifecycle: orphan cleanup (upload without commit), deletion propagation, retention; heavy processing goes to `background-jobs`.

## Required Workflow

1. Build the file-class table (type, size, visibility, volume, retention).
2. Choose backend + upload path per class.
3. Define validation (size, magic-byte type, sanitization) and key scheme.
4. Define read-side access control + serving (signed URLs/CDN for public).
5. Plan processing + lifecycle jobs.
6. Specify tests: oversized rejected, spoofed type rejected, cross-user file read rejected, presign constraints enforced.

## Decision Rules

- Content-type by magic bytes; the extension is a hint, never proof.
- Presigned uploads still get server-side post-verification (size/type of what actually landed).
- Serving user uploads from your app origin risks stored XSS — serve from a separate domain/CDN with correct `Content-Type` and `Content-Disposition`.
- Files are resources: cross-user/cross-tenant negative tests apply exactly as in `ownership-authorization`.

## Rules

- Every upload endpoint has recorded size/type limits enforced server-side.
- No client-controlled storage paths or keys.
- Upload endpoints are rate-limited (`rate-limiting` — expensive class).

## Anti-Patterns

- Trusting client MIME type or extension.
- Serving private files by unguessable URL alone ("security by URL").
- Storing user uploads inside the app's public/static directory.
- Unbounded upload sizes; no orphan cleanup.
- Original filename used as storage key (collisions, traversal, injection).

## Validation Checklist

- [ ] File-class table recorded (type/size/visibility/retention).
- [ ] Backend + upload path chosen per class.
- [ ] Magic-byte validation + sanitization planned.
- [ ] Generated, scoped key scheme; original names as metadata.
- [ ] Read-side access control + serving domain decided.
- [ ] Lifecycle/processing jobs planned.
- [ ] Negative tests: oversize, spoofed type, cross-user read.

## Definition of Done

A recorded file design — classes, backend, upload paths, server-enforced validation, key scheme, read-side access control, lifecycle — with negative tests covering size, type-spoofing, and cross-user access.

## Related Skills

`ownership-authorization`, `backend-validation`, `rate-limiting`, `background-jobs`, `backend-security`, `third-party-integrations` (storage provider), `../../database/relational-schema-design` (file metadata).

## Related Knowledge

`../../../knowledge/` (file classes, retention policy).

## Related References

`../../../references/backend/files/` (class table, when populated).

## Context Loading Guidance

- **Requires:** file use cases, visibility rules, deployment shape.
- **Does not require:** image-processing details, CDN vendor docs.
- **May load:** `ownership-authorization` (read control), `background-jobs` (processing).
- **Stop when:** class table, paths, validation, and access control are recorded.

## Token Efficiency Guidance

The file-class table drives everything; decide per class, don't re-derive per endpoint.
