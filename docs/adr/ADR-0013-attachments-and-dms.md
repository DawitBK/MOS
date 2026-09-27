# ADR-0013: MOS's own attachment store; optional DMS archive connector later

- **Status:** Accepted (planning), 2026-09-26. **Amended by [ADR-0017](ADR-0017-single-tenant-single-company-v1.md) and [ADR-0019](ADR-0019-infrastructure-not-assumed.md)** (2026-09-27):
  - The **local-disk adapter is the default**; the S3-compatible store (MinIO or cloud) is optional.
  - Object keys use an installation prefix instead of a tenant prefix.
  - ClamAV is optional, with fail-open or fail-closed set by configuration.
  - The DMS connector moves to Later (the "Phase 3" in the Decision text below).
  - Downloads are streamed through the API after the authorisation check; presigned URLs are used only with the S3 adapter (TDD §17.4).

## Context
MOS needs photos (weigh tickets, incidents), supplier KYC documents, CoAs, and generated PDFs. The existing DMS has strong storage patterns (content checks, ClamAV, per-tenant keys). However, **it has no service-to-service API today**: every route needs a user JWT, and its internal key covers only its roles endpoints. MOS must also work for customers who don't run DMS.

## Decision
- **Store:** MOS has its own S3-compatible attachment store: MinIO in development and on-prem, any S3-compatible service in the cloud, and a local-disk adapter for tiny installs.
- **Upload pipeline:** stream → content sniffing → size limit → ClamAV (fail-closed by default in production) → installation-prefixed key → `core.attachment` with SHA-256.
- **Transaction rule:** uploads happen before, and outside, business transactions. Business commands reference only a `file_id`.
- **Downloads:** presigned, short-lived URLs after an authorization check.
- **Phase 3:** an optional **DMS archive connector**, an outbox consumer that files approved-document PDFs into DMS. It requires a coordinated DMS change that adds a scoped service principal.

## Consequences
- MOS is self-sufficient; DMS integration adds value without becoming a dependency.
