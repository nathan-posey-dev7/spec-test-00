# Spec: Sign-in mode `hardware-token-proxy`

- **ID:** 003
- **Status:** Draft
- **Owner:** <name>
- **Last updated:** 2026-10-16
- **Depends on:** 001 (shared sign-in behaviour), 002 (certificate rules)

> Only what's specific to this mode. Everything else (sessions, sign-out,
> return-to, failures, audit) follows 001.

## Problem
Some deployments put a reverse proxy in front of the app that handles TLS
and checks staff hardware-token certificates. The app never sees the TLS
connection, so it has to rely on what the proxy tells it. If that trust
isn't tightly controlled, anyone who can reach the app or forge a header
could sign in as anyone.

## Goals
- Users sign in with their hardware token, verified by the proxy.
- The app accepts identity **only** from the configured, trusted proxy.
- The same certificate rules as 002 apply (trusted authorities, validity,
  stable identifier), whether checked by the proxy, the app, or both.

## Non-goals
- Configuring or operating the proxy itself (only the contract it must meet).
- Proxies that authenticate users some other way (e.g. a proxy that signs
  in with an identity provider; that's closer to 004).

## User stories
- **US-1:** As a member of staff, I want to sign in with my token through the
  organization's proxy, so I don't need a password.
- **US-2:** As a security reviewer, I want the app to ignore identity
  information that didn't come from the trusted proxy, so it can't be forged.
- **US-3:** As an operator, I want a clear, written contract for what the
  proxy must send, so I can configure it correctly.

## Acceptance criteria
- **AC-1 (US-1):** Given the proxy has verified my certificate and forwards my
  identity, when I open any page, then I am signed in automatically, with no
  extra click (001 AC-5 applies to this mode).
- **AC-2 (US-2):** Given a request did not come from the trusted proxy (per
  the configured trust mechanism), when it carries identity headers, then
  they are ignored and nobody is signed in. The request is rejected or
  treated as not signed in (see Open questions).
- **AC-3 (US-2):** Given a request from the trusted proxy says verification
  failed, or is missing required identity information, when it reaches the
  app, then nobody is signed in and the failure is audited.
- **AC-4 (US-2):** Given the app is configured to re-check the forwarded
  certificate, when the forwarded certificate fails any 002 validation rule,
  then sign-in fails, even if the proxy said it succeeded.
- **AC-5 (US-1):** Given my identity is forwarded, when I sign in, then my
  stable identifier and display name come from the same configured fields
  as in 002.
- **AC-6:** Given I am signed in, when a request arrives from the proxy with a
  different identity, or with none, then my session ends and the request
  is treated accordingly (re-sign-in as the new identity, or not signed in).
- **AC-7:** Given I signed out, when I return to the homepage, then I am not
  signed back in automatically until I select "Sign in", even though the
  proxy still forwards my identity.
- **AC-8:** Given I signed out, when the sign-out confirmation is shown, then
  it tells me to remove my token or close the browser to fully end use of my
  certificate (001 AC-12).
- **AC-9:** Given this mode is active, when the app starts without a trust
  mechanism for the proxy or without the identity header settings, then it
  refuses to start (001 AC-2).

## Business rules & validation
- **Proxy contract** (what the proxy must do, documented for operators):
  - verify the client certificate against the organization's trusted
    authorities, including revocation (same rules as 002);
  - **remove any incoming identity headers** from the client before adding
    its own;
  - forward the verification result and either the client certificate or
    the identity fields the app needs, in configured headers.
- Mode-specific settings (names provisional, under `auth.hardware_token_proxy.*`):
  - **trust mechanism**: one or more of: allowed proxy network addresses;
    mutual TLS between proxy and app; a shared secret header. At least one
    is required; mutual TLS or a shared secret is preferred over addresses alone.
  - header names for the verification result, certificate and/or identity fields
  - whether the app re-checks the forwarded certificate (default: yes, if
    the proxy forwards the certificate), with the 002 settings needed to do so
  - identity field mapping (as in 002)
- Deployment requirement: the app must not be reachable except through the
  proxy. This is an operator responsibility; AC-2 is the safety net.
- Audit events (001 AC-17) also record that the identity came via the proxy,
  and the certificate issuer and serial number when available.

## Data
- **Personal data:** as 002.

## Integrations
- The reverse proxy (contract above).
- If re-checking certificates: trusted authorities and revocation services, as in 002.

## Edge cases & errors
- Proxy forwards a certificate in an unexpected encoding or truncated:
  sign-in fails and is audited.
- Several proxies or instances: each must meet the contract; the trust
  mechanism must cover all of them.
- Proxy unavailable: users can't reach the app at all; nothing for the app to do.

## Open questions
- [ ] Same as 002: confirm hardware token = client certificate. (blocking)
- [ ] Which proxy(ies) are in use, and what can they forward (full
  certificate vs. selected fields)? Decides whether re-checking is possible. (blocking plan)
- [ ] Which trust mechanism(s) will deployments actually support? (blocking plan)
- [ ] Untrusted request with identity headers: reject outright (e.g. 403) or
  just treat as not signed in? (not blocking)
- [ ] Where do roles come from for certificate users? (001 open question; blocking plan)
