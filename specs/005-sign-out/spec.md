# Spec: Sign-out

- **ID:** 005
- **Status:** Draft
- **Owner:** <name>
- **Last updated:** 2026-10-16
- **Depends on:** 001 (sessions, audit), 002–004 (mode-specific guidance)

## Problem
Staff often use shared or unattended workstations. When they sign out
they need to be certain it worked, and the app mustn't quietly sign them
straight back in, which is a real risk in the hardware-token modes, where
the browser or proxy keeps presenting their identity.

## Goals
- Signing out is always one obvious action away.
- After signing out, the user sees a clear "You have successfully signed
  out" confirmation, and only when it's true.
- Nothing from the signed-out session can be reused or viewed afterwards.

## Non-goals
- Signing out other devices or sessions ("sign out everywhere").
- Admins forcibly ending other users' sessions.
- Session timeouts (001 AC-13); an expired session isn't a sign-out and
  doesn't show the confirmation.

## User stories
- **US-1:** As a signed-in user, I want a clearly labelled "Sign out" control
  on every page, so I can find it quickly.
- **US-2:** As a user who has just signed out, I want a clear message that I
  signed out successfully, so I can leave the workstation with confidence.
- **US-3:** As a security reviewer, I want sign-out to fully end the session
  and not be undone automatically, so the next person at the workstation
  can't act as the previous user.

## Acceptance criteria
- **AC-1 (US-1):** Given I am signed in, when I view any page, then a
  "Sign out" control is visible in the same place, and reachable by keyboard.
- **AC-2 (US-2, US-3):** Given I am signed in, when I choose "Sign out", then
  my session ends and I see a **Signed out** page with the message "You have
  successfully signed out of <application name>." and a "Sign in again" link.
- **AC-3 (US-2):** Given the active mode needs the user to do something more
  to fully finish (for example remove a hardware token, or a still-active
  identity provider session), when the Signed out page is shown, then it
  also shows that mode's guidance below the confirmation (see Business rules).
- **AC-4 (US-2):** Given sign-out fails (for example the session can't be
  ended because of a server error), when I choose "Sign out", then I do
  **not** see the success message; I see a plain-language error and can try again.
- **AC-5 (US-2):** Given I didn't just sign out (I'm not signed in, my
  session expired, or I'm still signed in), when I open the Signed out page
  directly or refresh it later, then I don't see the success message and I'm
  taken to the homepage.
- **AC-6 (US-3):** Given I signed out, when I use the browser's back button,
  then no protected content is shown, and any action I try treats me as not
  signed in.
- **AC-7 (US-3):** Given I signed out and the active mode can sign me in
  automatically (001 AC-5), when I open any page afterwards, then I am not
  signed back in until I choose "Sign in again" or "Sign in".
- **AC-8 (US-3):** Given I signed out, when anything (another tab, a saved
  link, a copied session cookie) tries to use the old session, then it is
  treated as not signed in.
- **AC-9 (US-3):** Given another website links or submits to the sign-out
  address, when my browser follows it, then I am not signed out.
- **AC-10:** Given I sign out, when it succeeds, then a sign-out security
  event is recorded (001 AC-17).
- **AC-11:** The Signed out page meets WCAG 2.2 AA, and the confirmation is
  announced to screen readers.

## Business rules & validation
- The Signed out page shows no user data (not even the display name).
- Mode-specific guidance (AC-3), provided by each mode spec:
  | Mode | Guidance |
  |------|----------|
  | `hardware-token` (002) | "To finish, remove your smart card or close your browser." |
  | `hardware-token-proxy` (003) | Same as 002 |
  | `identity-provider` (004) | TBD: depends on whether 004 also signs out at the identity provider |
- The AC-7 "don't sign back in automatically" state lasts until the user
  chooses to sign in, or the browser is closed (TBD, see Open questions).

## Data
- **Entities touched:** Session, Security event
- **Personal data:** none beyond 001's security events.

## Screens / interactions
- **Sign out control:** in the page header on every signed-in page (000 homepage included).
- **Signed out page:** confirmation message, optional mode guidance, "Sign in again" link.

## Edge cases & errors
- User clicks "Sign out" twice or in two tabs: second click shows the
  Signed out page only if the first just succeeded in that browser;
  otherwise the homepage (AC-5).
- Session already expired when "Sign out" is clicked: treat as signed out
  and show the confirmation; the user's intent is met and nothing is at risk.

## Open questions
- [ ] Is the exact message wording ("You have successfully signed out of
  <application name>.") right, or is there house style? (not blocking)
- [ ] How long does the "don't sign back in automatically" state last
  (AC-7): until next explicit sign-in, until browser close, or a fixed time? (not blocking)
- [ ] Should signing out in one tab immediately update other open tabs, or
  only on their next request? (not blocking)
