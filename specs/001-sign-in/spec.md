# Spec: Sign-in (shared behaviour across sign-in modes)

- **ID:** 001
- **Status:** Draft
- **Owner:** <name>
- **Last updated:** 2026-10-16

> What and why only. No frameworks, tables or endpoints here; that's `plan.md`.

## Problem
The application is deployed into environments with different authentication
infrastructure. Some require staff to sign in with a hardware token, either
directly or through a reverse proxy that handles the token, and others use
the organization's identity provider. The app has to support all three
without weakening any of them, and without the user experience or security
rules differing more than necessary between deployments.

## Scope of this spec
This spec defines what is **common to every sign-in mode**: how a mode is
chosen, the sign-in page, what a successful sign-in produces, sessions,
sign-out, failures and audit. How each mode actually verifies a user is
defined in its own spec:

| Spec | Mode | Summary |
|------|------|---------|
| 002 (planned) | `hardware-token` | User presents a hardware token (client certificate) directly to the app |
| 003 (planned) | `hardware-token-proxy` | A trusted reverse proxy verifies the token and passes the identity to the app |
| 004 (planned) | `identity-provider` | User signs in at the organization's identity provider |

## Goals
- Exactly one sign-in mode is active per deployment, chosen by configuration.
- Every mode results in the same thing: a signed-in user with a stable
  identity, a display name and roles.
- The app fails closed: misconfiguration prevents it from starting rather
  than leaving it open or half-secured.
- Sign-in, sign-out and session behaviour look and work the same regardless of mode.

## Non-goals
- Password-based sign-in, in any mode, ever.
- Running more than one mode at the same time, or letting users choose a mode.
- Switching modes at runtime without a restart/redeploy.
- Mode-specific verification details (see 002–004).
- Role management screens.

## Users & roles
| Role | Can do |
|------|--------|
| Not signed in | Reach the sign-in page and the not-signed-in homepage (000) only |
| Any signed-in user | Sign out |
| Operator (deploys the app) | Choose and configure the active mode |

## User stories
- **US-1:** As a member of staff, I want to sign in using the method my
  organization requires, so I can use the application.
- **US-2:** As a member of staff, I want to return to the page I was trying to
  reach after signing in, so I don't lose my place.
- **US-3:** As a signed-in user, I want to sign out, so nobody else can use my session.
- **US-4:** As an operator, I want to choose exactly one sign-in mode in
  configuration and have the app refuse to start if that configuration is
  wrong, so a deployment can't accidentally be insecure.
- **US-5:** As a security reviewer, I want sign-in activity recorded, so I can
  investigate suspicious access.

## Acceptance criteria

### Mode selection
- **AC-1 (US-4):** Given configuration names exactly one valid mode with all
  its required settings, when the app starts, then that mode is active and
  the startup log states which mode it is.
- **AC-2 (US-4):** Given configuration names no mode, more than one mode, an
  unknown mode, or is missing a setting the chosen mode requires, when the
  app starts, then it refuses to start and logs a clear reason, without
  logging any secrets.
- **AC-3 (US-4):** Given a mode is active, when a request uses a sign-in
  method belonging to a different mode (for example, identity headers sent
  as if from a reverse proxy while the `hardware-token` mode is active),
  then it is ignored and does not authenticate anyone.

### Sign-in page
- **AC-4 (US-1):** Given I am not signed in, when I open the sign-in page,
  then I see the application name and a single action appropriate to the
  active mode (for example "Sign in with your smart card" or "Continue to
  <identity provider name>"), and no username or password fields.
- **AC-5 (US-1):** Given the active mode can sign me in without any action
  from me (for example, a reverse proxy has already verified my token),
  when I open the sign-in page or any protected page, then I am signed in
  without an extra click. Each mode spec says whether this applies.
- **AC-6 (US-1):** Given I am already signed in, when I open the sign-in
  page, then I am sent to the homepage.

### After sign-in
- **AC-7 (US-1):** Given the active mode verifies me, when sign-in completes,
  then I have a session that identifies me by a stable identifier
  (one that doesn't change if my name or email changes), with my display name
  and roles available to the rest of the app.
- **AC-8 (US-2):** Given I tried to open a protected page while not signed
  in, when I sign in, then I am taken to that page. Without a remembered
  page, I am taken to the homepage.
- **AC-9 (US-2):** Given the remembered destination is outside this
  application, when I sign in, then I am taken to the homepage instead
  (no open redirects).
- **AC-10 (US-1):** Given I am verified but not known to the application or
  allowed to use it (see Open questions), when sign-in completes, then
  <behaviour TBD: denied with a "no access" message, or signed in with no
  roles and shown the 000 empty state>.

### Sessions & sign-out
- **AC-11 (US-3):** Given I am signed in, when I choose "Sign out", then my
  session ends, I see the not-signed-in homepage, and using the browser's
  back button doesn't show protected content.
- **AC-12 (US-3):** Given the active mode has its own session outside the
  application (for example, at the identity provider), when I sign out,
  then the mode spec defines whether that session is also ended, and the
  sign-out confirmation says so honestly.
- **AC-13:** Given my session has been idle for longer than the idle timeout
  or has reached its maximum lifetime, when I next make a request, then I am
  treated as not signed in.
- **AC-14:** Given I sign in, when my session is created, then it doesn't
  reuse any session identifier that existed before sign-in.

### Failures
- **AC-15 (US-1):** Given sign-in fails for any reason (invalid, expired or
  revoked token, identity provider error, user cancelled), when I am
  returned to the application, then I see a plain-language message and a
  way to try again, without technical details that would help an attacker.
- **AC-16:** Given a sign-in dependency (identity provider, revocation check,
  proxy) is unavailable, when someone tries to sign in, then sign-in fails
  closed and nobody is signed in on partial information.

### Audit
- **AC-17 (US-5):** Given any sign-in success, sign-in failure, sign-out or
  session expiry, when it happens, then a security event is recorded with
  time, active mode, outcome, user identifier (if known), source address and
  a failure reason category, and without tokens, certificates' private data
  or other secrets.

## Business rules & validation
- Sign-in modes (names are configuration values):
  `hardware-token`, `hardware-token-proxy`, `identity-provider`.
- Every mode must give the shared sign-in logic either a **verified identity**
  or a **failure with a reason category**. A verified identity has:
  - a stable unique identifier
  - a display name (falling back to username or email if missing)
  - the attributes used to work out roles (for example group membership)
- Roles are worked out from the verified identity the same way in every mode
  (see Open questions).
- Session timeouts: idle TBD (suggest 30 min), maximum lifetime TBD (suggest 12 h).
- Only the routes and settings of the active mode are enabled.

## Data
- **Entities touched:** User, Session, Role, Security event
- **Classification:** Internal (security events: Confidential)
- **Personal data:** Identifier, display name, source address in security
  events. Needed for access control and security investigation.
- **Audit:** Security events per AC-17. Retention: TBD per `principles.md`.

## Screens / interactions
- **Sign-in page:** app name, one mode-specific action, area for failure messages.
- **Signed-in pages:** a "Sign out" control available everywhere (000 shows it on the homepage).
- **Signed out:** the not-signed-in homepage (000), optionally with a "You have signed out" note.

## Integrations
- Depends on the active mode: hardware token infrastructure (certificate
  authorities, revocation services), a reverse proxy, or the identity
  provider. Details in 002–004.

## Non-functional requirements
- No secrets, tokens or full certificates in logs (per `principles.md`).
- The sign-in page meets WCAG 2.2 AA in every mode.

## Edge cases & errors
- A user's roles change while they're signed in: takes effect at the latest
  on their next sign-in, unless a mode spec says it can be sooner.
- The same user is signed in on two devices: allowed, sessions are independent (TBD).
- Configuration is changed while the app is running: ignored until restart.

## Open questions
- [ ] "Hardware token": X.509 client certificate on a smart card (e.g. PIV/CAC), FIDO2/WebAuthn security keys, or both? This spec assumes client certificates, which is what makes the reverse-proxy mode meaningful. (blocking 002/003)
- [ ] Who may use the app once verified: anyone the mode verifies, or only users known to the app / in certain groups? Decides AC-10. (blocking plan)
- [ ] Where do roles come from in each mode? Identity provider groups/claims work for 004, but certificates rarely carry roles, so 002/003 may need a directory lookup or roles managed in the app. (blocking plan; same question as in 000)
- [ ] Session idle timeout and maximum lifetime. (not blocking)
- [ ] Are multiple concurrent sessions per user allowed? (not blocking)
- [ ] Should a not-signed-in visitor to a protected page go straight into sign-in (when the mode allows), or always see the sign-in page first? (not blocking)
