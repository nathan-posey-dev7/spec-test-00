# Spec: Sign-in mode `identity-provider`

- **ID:** 004
- **Status:** TBD (not yet drafted)
- **Owner:** <name>
- **Depends on:** 001 (shared sign-in behaviour), 005 (sign-out)

Placeholder. To be drafted once 001–003 have settled. Topics it will need
to cover:

- Protocol(s): OIDC, SAML, or both
- Which identity provider(s) and how the app is registered with them
- Mapping claims/attributes to the stable identifier, display name and roles
- Sign-out: app only, or also at the identity provider, and the guidance
  text for 005's Signed out page
- Token and signing-key validation, clock skew, key rotation
- Behaviour when the identity provider is unavailable (001 AC-16)
