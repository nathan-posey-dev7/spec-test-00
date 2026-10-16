# Spec: Forbidden (403)

- **ID:** 011
- **Status:** Draft
- **Last updated:** 2026-10-16

## Problem
Signed-in users who reach something their roles don't allow need to know
it's a permissions issue, not a broken page.

## Acceptance criteria
- **AC-1:** Given I am signed in and open an address my roles don't permit,
  then I see an "Access denied" message **in place**: the address bar still
  shows what I requested.
- **AC-2:** The message shows the address I requested as plain text, and
  says how to get access (an access request, 007).
- **AC-3:** The response has status 403, and the page offers a link to the homepage.
- **AC-4:** Given I am **not** signed in, then I am sent to sign in instead
  (001 AC-8), not shown this page.
- **AC-5:** The page doesn't reveal anything about the content I was denied.
- **AC-6:** The page uses the normal app layout and meets WCAG 2.2 AA.

## Open questions
- [ ] For sensitive items, should a denied user see "Not found" (010) instead,
  so they can't tell the item exists? (not blocking)
