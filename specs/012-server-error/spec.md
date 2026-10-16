# Spec: Server error (500)

- **ID:** 012
- **Status:** Draft
- **Last updated:** 2026-10-16

## Problem
When something goes wrong in the app, users need a clear message and
something they can give to support, without seeing technical details.

## Acceptance criteria
- **AC-1:** Given an unexpected error happens while handling my request, then
  I see a "Something went wrong" message **in place**: the address bar still
  shows what I requested.
- **AC-2:** The message shows the address I requested as plain text and an
  error reference that support can use to find the matching log entry.
- **AC-3:** The page never shows technical details (error text, stack traces,
  internal names).
- **AC-4:** The response has status 500, and the page offers links to try
  again and to the homepage.
- **AC-5:** The error is logged with the same reference (per `principles.md`
  logging rules).
- **AC-6:** The page meets WCAG 2.2 AA, and still displays if the normal
  layout itself is what failed.

## Open questions
- [ ] Who is "support", and should the page say how to contact them? (not blocking)
