# Spec: Logging

- **ID:** 013
- **Status:** Draft (deliberately broad; expect follow-up specs)
- **Last updated:** 2026-10-16

## Problem
Operators and security reviewers need a record of what the app is doing:
what went wrong, how it's used, who signed in and what the app did by itself.

## Scope
Which kinds of events are logged and the minimum each contains.
**Not included (for now):** log storage/shipping, format, retention,
dashboards, alerting.

## Event categories
| Category | Examples | Referenced by |
|----------|----------|---------------|
| **Error** | Unexpected errors, failed dependencies | 012 AC-5 |
| **Telemetry** (minimal) | Page loaded, response status, duration | 010–012 |
| **Sign-in** | Sign-in success/failure **with the active mode**, sign-out, session expiry, refused locked account | 001 AC-17, 005, 008 |
| **System action** | Things the app does by itself: startup (with mode and config source), shutdown, account locked for inactivity | 001 AC-1, 008 |
| **Email** | Every attempt to send an email, successful or not | Backlog (email not yet specified) |

## Acceptance criteria
- **AC-1:** Every event in the table above is logged with at least: time,
  category, event name, outcome, and a request/correlation ID where there
  is a request.
- **AC-2:** Events tied to a signed-in user include their stable identifier
  (001); telemetry includes nothing else about the user.
- **AC-3:** No event contains secrets, tokens, full certificates, session
  IDs or form contents (per `principles.md`).
- **AC-4:** Sign-in events always record the active sign-in mode.
- **AC-5:** Telemetry records which page was loaded (path, not query
  string), its status code and how long it took, and nothing more.
- **AC-6:** Every attempted email is logged with its purpose (e.g. which
  kind of notification), recipient, and outcome (sent, failed, refused),
  but never its body.

## Open questions
- [ ] Where do logs go, in what format, and for how long? (blocking plan)
- [ ] If logging fails, should the app keep running or stop (some audit rules require stopping)? (blocking plan)
- [ ] Is "system actions" also meant to cover changes users make to data (business audit, per `principles.md`)? (not blocking)
- [ ] Should telemetry be per user or anonymous? (not blocking)
- [ ] Email recipient: log the address, or only the user's stable identifier where there is one? (not blocking)
