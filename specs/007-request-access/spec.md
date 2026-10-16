# Spec: Request access form

- **ID:** 007
- **Status:** Draft
- **Last updated:** 2026-10-16
- **Depends on:** 000 (homepage link), principles (access only via request)

## Problem
Access is granted only through an access request, so people without
access need a way to ask for it.

## Scope
The form a not-signed-in user fills in, and what they see after submitting.
**Not included:** reviewing, approving or rejecting requests, notifications,
granting roles, or a requester checking request status.

## Acceptance criteria
- **AC-1:** Given I am not signed in, when I open the homepage or sign-in
  page, then I see a "Request access" link to the form.
- **AC-2:** Given I open the form, then it asks for: full name, work email,
  department/team, the role(s) I'm requesting (from the roles in
  `principles.md`) and a reason. All are required.
- **AC-3:** Given a field is missing or invalid (for example an email outside
  the organization's allowed domains), when I submit, then nothing is
  submitted and each problem is shown next to its field, keeping my input.
- **AC-4:** Given a valid form, when I submit, then the request is saved as
  **pending** and I see a confirmation with a reference number and a note
  that someone will review it.
- **AC-5:** Given the confirmation, then it's the same whether or not the
  email already has access or a pending request, so the form can't be used
  to discover who has access.
- **AC-6:** Given many submissions from the same source or email in a short
  time, when another arrives, then it is refused with a "try again later"
  message.
- **AC-7:** Given I am signed in, when I open the form, then I'm sent to the
  homepage (signed-in users with no roles see 000 AC-7 instead).
- **AC-8:** The form meets WCAG 2.2 AA, and every submission is recorded as
  a security event (001 AC-17).

## Notes
- **Data:** name, work email, department, reason: personal data, Internal.
- A submitted request grants nothing by itself.

## Open questions
- [ ] Should the requester prove who they are first (e.g. present their
  hardware token, or sign in at the identity provider) rather than typing an
  email? This would also settle 001 AC-10 ("verified but not known"). (blocking plan)
- [ ] Are the fields right? Manager/approver, or a location? (not blocking)
- [ ] Which email domains are allowed? (not blocking)
