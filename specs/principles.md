# Project principles

Standing rules that apply to every feature. Specs may tighten these, but
should not loosen them without a decision record in `specs/decisions/`.

Mark items `TBD` until decided; replace with concrete values as they are.

## Users & access
- Users are internal staff. Exactly one sign-in mode is active per
  deployment: hardware token, hardware token via reverse proxy, or the
  organization's identity provider. See spec 001.
- No passwords: the application never asks for or stores them.
- Authorization is role-based. Roles are defined centrally (see below) and
  every feature spec must state which roles can do what.
- The only way to be granted access is an approved access request (007).
- Deny by default: a new endpoint or screen is inaccessible until a role is
  explicitly granted.

### Roles (initial)
| Role | Description |
|------|-------------|
| Viewer | Read-only access to data they're permitted to see |
| Editor | Can create and modify records |
| Admin  | Manages configuration and role assignments |

## Data
- Data classification levels: TBD (e.g. Public / Internal / Confidential / Restricted).
  Every spec states the classification of the data it touches.
- Personal data is minimized; specs must justify any personal data collected.
- Retention and deletion rules: TBD.
- Changes to business records are audit-logged: who, what, when, before/after.
- All timestamps should be UTC
- All strings should be trimmed before saving.
- All whitespace strings should be converted to null before saving.
- Timestamps should be checked for DateTime default values and dissallowed.
- On delete cascade type behavior should generally be avoided.
- Favor soft-delete type behavior over permanent deletion.

## Quality bars
- Accessibility: WCAG 2.2 AA.
- Supported browsers: current versions of the organization's managed browsers (TBD).
- Performance default: common pages usable within 2s on the corporate network,
  unless a spec states otherwise.
- Availability target: business hours / 24×7 (TBD).

## Operations
- Environments: dev, test/staging, production (TBD).
- All configuration and secrets come from the environment, never source control.
- Structured logs with a request/correlation ID; no secrets or sensitive data in logs (see 013).
- Every feature should be testable automatically; manual-only checks are the exception and must be noted.

## Integration
- Known systems we may integrate with: TBD (e.g. HR system, ERP, email, data warehouse).
- Integrations go through a clearly bounded adapter so the external system can be faked in tests.
