# Spec: Profile page

- **ID:** 006
- **Status:** Draft
- **Last updated:** 2026-10-16
- **Depends on:** 001 (verified identity, roles)

## Problem
Users can't see what the app knows about them, such as which roles they
have, so they can't tell why something is or isn't available.

## Scope
A read-only page showing the signed-in user's own details.
**Not included:** editing anything, viewing other users' profiles, preferences.

## Acceptance criteria
- **AC-1:** Given I am signed in, when I choose "Profile" (in the header, next
  to "Sign out"), then I see my display name, stable identifier, email (if
  known) and roles.
- **AC-2:** Given I am viewing my profile, then nothing on it can be edited,
  and it says who to contact to correct my details.
- **AC-3:** Given I have no roles, when I view my profile, then it says so
  (matching the 000 empty state).
- **AC-4:** Given I am not signed in, when I open the profile page, then I am
  sent to sign in and returned here afterwards (001 AC-8).
- **AC-5:** The page only ever shows the signed-in user's own details; no
  input (such as an ID in the address) shows anyone else's.
- **AC-6:** The page shows no secrets or session details (tokens, full
  certificates, session IDs) and meets WCAG 2.2 AA.

## Notes
- Details come from the identity established at sign-in (001), so changes
  appear after the next sign-in.

## Open questions
- [ ] Show sign-in details too (mode, sign-in time, certificate issuer and expiry)? (not blocking)
- [ ] Who should users contact to correct their details? (not blocking)
