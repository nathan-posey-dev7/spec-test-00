# Spec: <Feature name>

- **ID:** NNN
- **Status:** Draft | Ready for plan | Planned | Done
- **Owner:** <name>
- **Last updated:** YYYY-MM-DD

> What and why only. No frameworks, tables or endpoints here; that's `plan.md`.

## Problem
<2–4 sentences: who has what problem today, and why it matters.>

## Goals
- <Outcome, ideally measurable>

## Non-goals
- <Explicitly out of scope for this feature>

## Users & roles
| Role | Can do |
|------|--------|
| <Viewer/Editor/Admin/…> | <actions> |

## User stories
- **US-1:** As a <role>, I want <capability> so that <benefit>.

## Acceptance criteria
Each criterion must be checkable. Reference stories where useful.
- **AC-1 (US-1):** Given <context>, when <action>, then <observable result>.
- **AC-2:** Given a user without <role>, when they attempt <action>, then access is denied.

## Business rules & validation
- <Rule, e.g. "A request over $5,000 requires a second approver.">

## Data
- **Entities touched:** <business terms, e.g. Request, Approver>
- **Classification:** <per principles.md>
- **Personal data:** <what and why, or "none">
- **Audit:** <which changes must be audit-logged>

## Screens / interactions (optional)
<Rough description or links to sketches. Describe behaviour, not visual design.>

## Integrations
- <External system, direction (in/out), what data, how often; or "none">

## Non-functional requirements
Only list where this feature differs from `principles.md`.
- <e.g. "Export of up to 50k rows completes within 30s.">

## Edge cases & errors
- <What happens when … ?>

## Open questions
- [ ] <Question> (owner, blocking?)
