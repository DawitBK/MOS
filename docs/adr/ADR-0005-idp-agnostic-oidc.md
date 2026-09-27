# ADR-0005: IdP-agnostic OIDC relying party; authorization and sessions inside MOS

- **Status:** Accepted (planning), 2026-09-26.
  - **Amended by [ADR-0015](ADR-0015-local-accounts.md)** (2026-09-27): local accounts are supported alongside SSO.
  - **Amended by [ADR-0017](ADR-0017-single-tenant-single-company-v1.md) and [ADR-0018](ADR-0018-lean-v1-six-months.md)** (2026-09-27):
    - IdP configuration is **per installation**, not per tenant.
    - **Local accounts are the default** sign-in; OIDC SSO is an optional second method.
    - The bundled multi-tenant IdP is removed from the roadmap. It is revisited only if pooled SaaS is ever built.
    - Machine API keys and edge-device keys move to Later, because v1 has no machine clients.
  - **Reading note:** the Decision text below keeps its v0.2 wording. "Per tenant" and "tenant-scoped" now mean per installation; "Phase 2" (edge-device keys) and "Phase 3" (bundled IdP) now mean Later and "only if pooled SaaS is built", as listed above.

## Context
The existing SSO serves one organisation only:
- it has no tenant claim and no `client_credentials` grant;
- access tokens are opaque;
- PKCE is required for all clients, even though its guide says otherwise;
- back-channel logout exists only for DMS.

A company-neutral SaaS product cannot depend on one company's SSO. DMS kept permissions inside its JWT, and they went stale.

## Decision
- MOS is a standards-based OIDC relying party (`openid-client`: discovery, PKCE S256, JWKS verification).
- IdP configuration is set per tenant: issuer, client, scopes, and the group-claim path.
- **Authorization lives in MOS:** tenant-scoped roles with optional company/site scope, and an `authz_version` that makes permission changes immediate. Mapping IdP groups to roles is optional.
- **Sessions:** server-side in Postgres, with an opaque HttpOnly cookie and a CSRF token.
- **Machine access:** MOS-issued hashed API keys, and in Phase 2 edge-device keys.
- **Development:** the existing SSO is the reference IdP. Its `mos` client and portal tile are gated behind `MOS_ENABLED`, because SSO refuses to boot in production with dev secrets or localhost URIs on *any* seeded client. That SSO change is a small, separate step inside WP-02.
- **Phase 3:** a bundled multi-tenant IdP (e.g. Zitadel organisations) for shared SaaS.

## Consequences
- MOS works with any IdP a customer brings (the reference SSO, Entra ID, Keycloak and others).
- MOS owns session lifetime, because there is no back-channel logout for new SSO clients: 8 h idle, 12 h absolute.

## Alternatives considered
- **Make the existing SSO multi-tenant:** rejected; a large rework, and it would make SSO SaaS-critical.
- **MOS-native password authentication:** rejected at the time; it takes on all password, MFA and lockout risk. **Reversed by [ADR-0015](ADR-0015-local-accounts.md)** after the client's draft.
