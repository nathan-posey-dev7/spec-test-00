# Spec: Not found (404)

- **ID:** 010
- **Status:** Draft
- **Last updated:** 2026-10-16

## Problem
Users who follow a broken or mistyped link need to know the page doesn't
exist, and which address caused it.

## Acceptance criteria
- **AC-1:** Given I open an address that doesn't exist in the app, then I see
  a "Page not found" message **in place**: the address bar still shows
  what I requested (no redirect to a separate error address).
- **AC-2:** The message shows the address I requested, displayed as plain
  text (anything in it is shown, never run or rendered as content).
- **AC-3:** The response has status 404, and the page offers a link to the homepage.
- **AC-4:** The page uses the normal app layout and meets WCAG 2.2 AA.

## Notes
- The disclaimer (009) still comes first.

## Open questions
- [ ] Show the query string (`?…`) of the address too, or only the path? (not blocking)
