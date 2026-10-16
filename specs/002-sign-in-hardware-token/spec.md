# Spec: Sign-in mode `hardware-token` (direct)

- **ID:** 002
- **Status:** Draft
- **Owner:** <name>
- **Last updated:** 2026-10-16
- **Depends on:** 001 (shared sign-in behaviour)

> Only what's specific to this mode. Everything else (sessions, return-to,
> failures, audit) follows 001; sign-out follows 005.

## Problem
Some deployments require staff to sign in with a hardware token holding a
client certificate (for example a smart card), and have no reverse proxy
or identity provider in front of the app. The app must check the
certificate itself.

## Goals
- Users sign in by presenting their hardware token to the app, with no
  password.
- Only certificates issued by the organization's trusted certificate
  authorities, currently valid and not revoked, can sign anyone in.

## Non-goals
- Issuing, enrolling or managing tokens and certificates.
- Accepting certificates or identity forwarded by a proxy (that's 003, and
  001 AC-3 says forwarded identity is ignored in this mode).

## User stories
- **US-1:** As a member of staff, I want to sign in by inserting my token and
  choosing my certificate, so I don't need a password.
- **US-2:** As a security reviewer, I want only valid, unrevoked certificates
  from trusted authorities to be accepted, so a lost or revoked token can't
  be used.

## Acceptance criteria
- **AC-1 (US-1):** Given I am not signed in, when I open the homepage or
  sign-in page, then I am not asked for a certificate.
- **AC-2 (US-1):** Given I am not signed in, when I select "Sign in with your
  smart card", then my browser asks me to choose a certificate (and enter my
  token PIN if the token needs one), and on success I am signed in per 001.
- **AC-3 (US-2):** Given I present a certificate that doesn't chain to a
  configured trusted authority, is expired or not yet valid, or isn't
  allowed for client authentication, when I try to sign in, then sign-in
  fails (001 AC-15).
- **AC-4 (US-2):** Given I present a certificate that has been revoked, when I
  try to sign in, then sign-in fails.
- **AC-5 (US-2):** Given the revocation status can't be checked (service
  unreachable, stale data), when I try to sign in, then sign-in fails closed
  (001 AC-16).
- **AC-6 (US-1):** Given my certificate is valid, when I sign in, then my
  stable identifier and display name are taken from the configured
  certificate fields (see Business rules).
- **AC-7:** _Moved to 005 (sign-out) AC-7._
- **AC-8:** _Moved to 005 (sign-out) AC-3; this mode's guidance is listed there._
- **AC-9:** Given I am signed in, when a later request presents a different
  certificate identity, then my session ends and I am treated as not signed in.
- **AC-10:** Given this mode is active, when the app starts without trusted
  authorities, revocation settings or identity field mapping configured,
  then it refuses to start (001 AC-2).

## Business rules & validation
- Mode-specific settings (names provisional, under `auth.hardware_token.*`):
  - trusted certificate authorities (one or more)
  - revocation checking method and sources (e.g. OCSP, CRL)
  - which certificate field gives the **stable identifier** (e.g. a
    subject alternative name such as the UPN, or a specific subject attribute)
  - which field gives the **display name** (default: subject common name)
- The identifier must not be the certificate serial number or fingerprint,
  which change when a token is reissued.
- Certificate validation happens on every sign-in, not just the first one.
- Audit events (001 AC-17) also record the certificate issuer and serial
  number; never the full certificate.

## Data
- **Personal data:** identifier and name from the certificate; issuer and
  serial number in security events.

## Integrations
- Trusted certificate authority certificates (provided via deployment configuration).
- Revocation services (OCSP responders and/or CRL distribution points).

## Edge cases & errors
- User has several certificates on the token (e.g. signing and
  authentication): only certificates allowed for client authentication are
  accepted; the browser may still show all of them.
- Token removed while signed in: the session continues until sign-out or
  timeout unless the browser stops presenting the certificate (TBD, see Open questions).
- Certificate expires mid-session: the session continues until it ends;
  the next sign-in fails.
- User cancels the certificate prompt: failure message and "try again" (001 AC-15).

## Open questions
- [ ] Confirm "hardware token" means a client certificate on a smart card
  (e.g. PIV/CAC) and not FIDO2/WebAuthn. This spec assumes certificates. (blocking)
- [ ] Which certificate field is the stable identifier for our users? (blocking plan)
- [ ] Should removing the token end the session? Browsers make this hard to
  detect reliably. (not blocking)
- [ ] Should revocation ever "soft-fail" (allow sign-in when the check is
  unavailable)? This spec says no. (not blocking)
- [ ] Where do roles come from for certificate users? (001 open question; blocking plan)
