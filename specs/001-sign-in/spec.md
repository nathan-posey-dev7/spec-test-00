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
failures and audit. Sign-out is defined in [005](../005-sign-out/spec.md). How each mode actually verifies a user is
defined in its own spec:

| Spec | Mode | Summary |
|------|------|---------|
| [002](../002-sign-in-hardware-token/spec.md) | `hardware-token` | User presents a hardware token (client certificate) directly to the app |
| [003](../003-sign-in-hardware-token-proxy/spec.md) | `hardware-token-proxy` | A trusted reverse proxy verifies the token and passes the identity to the app |
| [004](../004-sign-in-identity-provider/spec.md) (TBD) | `identity-provider` | User signs in at the organization's identity provider |

## Goals
- Exactly one sign-in mode is active per deployment, set in deployment
  configuration when the app is deployed, and fixed for as long as it runs.
- Every mode results in the same thing: a signed-in user with a stable
  identity, a display name and roles.
- The app fails closed: misconfiguration prevents it from starting rather
  than leaving it open or half-secured.
- Sign-in and session behaviour look and work the same regardless of mode.

## Non-goals
- Password-based sign-in, in any mode, ever.
- Running more than one mode at the same time, or letting users choose a mode.
- Choosing or changing the mode from inside the running app (admin
  screen, API, database setting, feature flag service). Changing the mode
  means changing deployment configuration and redeploying or restarting.
- A default mode. If nothing is configured, nothing is active.
- Mode-specific verification details (see 002–004).
- Sign-out (see 005).
- Role management screens.

## Users & roles
| Role | Can do |
|------|--------|
| Not signed in | Reach the sign-in page and the not-signed-in homepage (000) only |
| Any signed-in user | Sign out (005) |
| Operator (deploys the app) | Set the active mode and its settings in deployment configuration |
| Admin (in the app) | Nothing related to the mode: they can't see secrets or change it |

## User stories
- **US-1:** As a member of staff, I want to sign in using the method my
  organization requires, so I can use the application.
- **US-2:** As a member of staff, I want to return to the page I was trying to
  reach after signing in, so I don't lose my place.
- **US-3:** _Moved to 005 (sign-out)._
- **US-4:** As an operator, I want to set exactly one sign-in mode in the
  deployment's configuration, and have the app refuse to start if that
  configuration is wrong, so a deployment can't accidentally be insecure.
- **US-6:** As a security reviewer, I want the sign-in mode to be decided
  only by deployment configuration, so nobody using the running app can
  weaken how users are verified.
- **US-5:** As a security reviewer, I want sign-in activity recorded, so I can
  investigate suspicious access.

## Acceptance criteria

### Mode selection
See "Mode configuration" under Business rules for where the setting comes from.
- **AC-1 (US-4):** Given deployment configuration sets exactly one valid mode
  with all its required settings, when the app starts, then that mode is
  active and the startup log states which mode it is and which source
  (configuration file or deployment environment) it came from.
- **AC-2 (US-4):** Given deployment configuration sets no mode, more than one
  mode, an unknown mode, sets different modes in the configuration file and
  the deployment environment, or is missing a setting the chosen mode requires, when the
  app starts, then it refuses to start and logs a clear reason, without
  logging any secrets.
- **AC-2a (US-6):** Given the app is running, when anyone changes the
  configuration file or anything else, then the active mode stays the same
  until the app is restarted or redeployed. Nothing in the app's UI, API or
  database can change it.
- **AC-2b (US-6):** Given the app is running, when an admin or user looks
  for sign-in settings in the app, then there is no way to see or change the
  mode's secrets (keys, client secrets, proxy shared secrets).
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

### Sessions
- **AC-11:** _Moved to 005 (sign-out)._
- **AC-12:** _Moved to 005 (sign-out)._
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

### Mode configuration
- The mode is a single setting, provisionally named `auth.mode`, read
  **once at startup**. It is part of deployment configuration, and comes from
  either:
  - the app's **configuration file** that ships with or is mounted into the
    deployment, or
  - the **deployment environment** (for example an environment variable such
    as `AUTH_MODE`, set by the deployment tooling).
- If both sources set it, they must agree; if they disagree the app refuses
  to start (AC-2). Neither silently overrides the other.
- Each mode's own settings (certificate authorities, proxy address, identity
  provider details) live under that mode's section, for example
  `auth.hardware_token.*`. Settings for modes that aren't active are ignored,
  and their presence is logged as a warning.
- Secrets used by a mode come from the deployment's secret mechanism, never
  from the configuration file in source control (per `principles.md`).
- Exact file format, key names and environment variable names are decided
  in the plan, once the stack is chosen.
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
- **Signed-in pages and Signed out page:** see 005.

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
- Configuration is changed while the app is running: ignored until restart (AC-2a).
- Several instances of the app run behind a load balancer with different
  modes configured by mistake: TBD; at minimum, each logs its mode at startup
  so the mismatch is visible.

## Open questions
- [ ] "Hardware token": X.509 client certificate on a smart card (e.g. PIV/CAC), FIDO2/WebAuthn security keys, or both? This spec assumes client certificates, which is what makes the reverse-proxy mode meaningful. (blocking 002/003)
- [ ] Who may use the app once verified: anyone the mode verifies, or only users known to the app / in certain groups? Decides AC-10. (blocking plan)
- [ ] Where do roles come from in each mode? Identity provider groups/claims work for 004, but certificates rarely carry roles, so 002/003 may need a directory lookup or roles managed in the app. (blocking plan; same question as in 000)
- [ ] Session idle timeout and maximum lifetime. (not blocking)
- [ ] Are multiple concurrent sessions per user allowed? (not blocking)
- [ ] Should a not-signed-in visitor to a protected page go straight into sign-in (when the mode allows), or always see the sign-in page first? (not blocking)
