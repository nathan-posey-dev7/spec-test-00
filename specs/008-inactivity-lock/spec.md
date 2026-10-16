# Spec: Lock inactive accounts

- **ID:** 008
- **Status:** Draft
- **Last updated:** 2026-10-16
- **Depends on:** 001 (sign-in, deployment configuration, audit), 007 (access requests)

## Problem
Accounts that are no longer used are a security risk, so access should
lapse automatically when someone stops signing in.

## Scope
Locking accounts after a configurable period without a successful sign-in.
**Not included:** unlocking (see Open questions), locking after failed
sign-in attempts, admins locking accounts by hand.

## Acceptance criteria
- **AC-1:** Given my last successful sign-in was more than the inactivity
  period ago (default **30 days**), then my account is locked.
- **AC-2:** Given I was granted access but have never signed in, then the
  period counts from when access was granted.
- **AC-3:** Given my account is locked, when the active mode verifies me,
  then I am not signed in, and I see a message that my account is locked
  because of inactivity and how to regain access.
- **AC-4:** Given an account is locked, then its roles and history are kept;
  locking only stops sign-in.
- **AC-5:** Given an account becomes locked, then a security event is
  recorded once (001 AC-17), and so is each refused sign-in.
- **AC-6:** Given deployment configuration sets the period (in whole days,
  provisional name `auth.inactivity_lock_days`), when the app starts, then
  that period is used; a missing value means 30, and zero, a negative or
  non-numeric value stops the app from starting (001 AC-2).

## Notes
- The period is part of deployment configuration, read at startup, like
  the sign-in mode (001). It can't be changed from inside the app.
- With 001's maximum session lifetime, nobody can stay signed in long
  enough to avoid the check.

## Open questions
- [ ] How does a locked user regain access: a new access request (007), or
  a separate unlock process? (blocking AC-3 message)
- [ ] Should any accounts be exempt, such as emergency admin accounts? (not blocking)
- [ ] Warn users before they're locked, for example by email? (not blocking)
