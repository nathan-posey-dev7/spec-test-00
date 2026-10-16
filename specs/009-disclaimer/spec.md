# Spec: Disclaimer acceptance

- **ID:** 009
- **Status:** Draft
- **Last updated:** 2026-10-16

## Problem
Users must acknowledge a disclaimer (for example terms of use or a
monitoring notice) before using the application.

## Scope
A disclaimer page that must be accepted once per browsing session before
any other page can be used.
**Not included:** writing the disclaimer text, per-user acceptance records,
non-browser clients (e.g. APIs).

## Acceptance criteria
- **AC-1:** Given I haven't accepted the disclaimer in this browsing session,
  when I open any page (including the homepage, sign-in and request access),
  then I see the disclaimer page instead, with "Accept" and "Decline".
- **AC-2:** Given I accept, then I am taken to the page I originally asked
  for (only within this app, as 001 AC-9), or the homepage.
- **AC-3:** Given I have accepted, when I open more pages, tabs or windows in
  the same browser, then I am not asked again.
- **AC-4:** Given I have accepted, when I sign in, sign out, my sign-in
  session expires, or any amount of time passes, then I am **not** asked
  again. Acceptance isn't tied to sign-in or any timer.
- **AC-5:** Given I close the browser (ending the browsing session) and open
  the app again, then I am asked again. A private window counts as a
  separate browsing session.
- **AC-6:** Given the app restarts or I am routed to a different instance,
  then my acceptance still counts, because it is kept by my browser,
  not by the server.
- **AC-7:** Given I decline, then I see a message that I can't use the
  application without accepting, and a way to return to the disclaimer.
- **AC-8:** The disclaimer page meets WCAG 2.2 AA and works without any
  other page having been loaded first.

## Notes
- The browser holds the acceptance, so a determined user could fake it.
  That's acceptable: this is a notice, not an access control. Access
  control stays with sign-in and roles.
- Only the disclaimer page and what it needs to display (styles, images)
  are reachable before acceptance.

## Open questions
- [ ] Where does the disclaimer text come from: deployment configuration
  (varies per deployment) or built into the app? (blocking plan)
- [ ] Should the page require scrolling to the end or ticking a box before
  "Accept" is enabled? (not blocking)
- [ ] Do we need to record acceptances (e.g. count, or user after sign-in)
  for compliance? (not blocking)
