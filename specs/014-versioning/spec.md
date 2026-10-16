# Spec: App version and version history

- **ID:** 014
- **Status:** Draft
- **Last updated:** 2026-10-16
- **Depends on:** 013 (startup is a logged system action)

## Problem
Users, support and operators need to know exactly which version of the app
they're using and what changed between versions.

## Version format (strict)
- `MAJOR.MINOR.PATCH`: exactly three non-negative whole numbers separated
  by dots, with no leading zeros (`0`, not `00`), and nothing else: no
  `v` prefix and no suffixes like `-beta` or `+build`.
- Valid: `0.1.0`, `1.12.3`. Invalid: `1.2`, `v1.2.3`, `1.02.3`, `1.2.3-rc1`.
- **MAJOR**: changes operators or users must act on (e.g. configuration
  changes, removed features). **MINOR**: new features. **PATCH**: fixes only.
  Increasing one resets the numbers to its right to `0`.

## Acceptance criteria
- **AC-1:** The app has exactly one version, defined in one place, and it
  must match the format above. A build with a missing or invalid version fails.
- **AC-2:** Each release's version is higher than the previous release's.
  A build that reuses or lowers a version fails.
- **AC-3:** Each release has a version history entry: version, release date
  and a short summary of changes, ideally referencing spec IDs. A build whose
  version has no entry fails.
- **AC-4:** The current version is shown in the footer of every page and is
  recorded in the startup log event (013).
- **AC-5:** A **Version history** page lists all entries, newest first, with
  the current version highlighted. It's linked from the version in the footer.
- **AC-6:** The page meets WCAG 2.2 AA.

## Open questions
- [ ] Who can see the version and history: everyone (after the disclaimer,
  009) or only signed-in users? Showing it publicly tells attackers which
  version is running. (blocking plan)
- [ ] First version: `0.1.0`? And what makes it `1.0.0`? (not blocking)
