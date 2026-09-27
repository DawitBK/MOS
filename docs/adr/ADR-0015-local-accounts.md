# ADR-0015: Local username/password accounts alongside SSO

- **Status:** Accepted (planning), 2026-09-27. **Amends ADR-0005.**
- **v0.3 note:** [ADR-0017](ADR-0017-single-tenant-single-company-v1.md)/[ADR-0018](ADR-0018-lean-v1-six-months.md) make local accounts the default sign-in, configured per installation. The references below to "tenant", "MVP", "Phase 3" and WP numbers describe the v0.2 context in which this decision was taken. The breached-password check listed below moved to **v1.1** (FRD FR-PLT-018, TDD §6.1); the minimum length of 12 is the configurable default.
- **Trigger:** the client's procure-to-pay draft. Its sign-in mock-up has username, password and operation unit, with "SSO Login" as an alternative.

## Context
ADR-0005 made MOS an IdP-only relying party and rejected MOS-native passwords, to avoid owning password, MFA and lockout risk. It planned a bundled IdP in Phase 3 for customers without one. The client's draft expects local sign-in from the start, and many target plants have no identity provider. Waiting for a Phase 3 bundled IdP would block exactly the customers MOS is aimed at.

## Decision
- MOS supports **both** sign-in methods per installation: OIDC SSO (ADR-0005, unchanged) and **local accounts**. An administrator enables either or both (v0.3: local accounts are the default; ADR-0017).
- **Local accounts meet OWASP ASVS L2** (details in TDD §6.1):
  - argon2id hashing, with rehash on parameter change;
  - minimum length 12 plus a breached-password check (offline list on-prem);
  - lockout per account and per IP;
  - optional TOTP, enforceable per role (e.g. payment approvers, admins), with hashed recovery codes;
  - admin reset forces a change at next sign-in; email-link reset is optional;
  - constant-time, non-enumerating responses;
  - every attempt audited.
- Both methods create the same server-side session; authorization is identical afterwards.
- **Operation unit selection happens after authentication** (FRD FR-PLT-019), not on the anonymous page as drawn, so a public page never lists the organisation's sites.
- The Phase 3 bundled IdP is **no longer required**. It stays an option for customers who want federation or a single sign-on across several of their own apps.

## Consequences
- MOS now owns credential security: added threat-model items and WP-13 tests (brute force, enumeration, reset abuse). New risk R16.
- WP-02 grows from 3 to 4 dev-weeks; Phase 3 WP-41 shrinks from 4 to 3.
- Customers without an IdP can go live with the MVP.

## Alternatives considered
- **Bundle Keycloak/Zitadel in the MVP:** rejected; it's an extra service to operate and upgrade on every install, for 1–2 developers.
- **Keep SSO-only:** rejected; it contradicts the client's expectation and blocks plants without an IdP.
