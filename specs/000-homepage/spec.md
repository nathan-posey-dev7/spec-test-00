# Spec: Homepage

- **ID:** 000
- **Status:** Draft
- **Owner:** <name>
- **Last updated:** 2026-10-16

> What and why only. No frameworks, tables or endpoints here; that's `plan.md`.

## Problem
The application needs a single, predictable entry point. People who aren't
signed in need to know what the application is and how to get in. Signed-in
staff need a starting point that shows only what they can actually use, so
they don't hit dead ends or see features they have no access to.

## Goals
- Visitors who aren't signed in understand what the app is for and can start
  signing in with one click.
- Signed-in users see a personalised starting point that lists only the
  items they have permission to use.
- Establish the pattern later features use to add themselves to the homepage.

## Non-goals
- Building the sign-in mechanism itself (SSO integration). This spec only
  links to it; see `principles.md` and a future decision record.
- Role management screens.
- Dashboards, metrics, notifications or other dynamic content on the homepage.
- Visual design or branding beyond a clean, accessible layout.

## Users & roles
| Role | Can do |
|------|--------|
| Not signed in | See the explanation and a "Sign in" link |
| Any signed-in user | See their name and the homepage items their roles permit |
| Viewer / Editor / Admin | See the items granted to that role (see "Homepage items") |

## User stories
- **US-1:** As a visitor who isn't signed in, I want a short explanation of
  the app and a clear way to sign in, so I know I'm in the right place and
  can get started.
- **US-2:** As a signed-in user, I want the homepage to show the things I
  can use, so I can get to my work quickly.
- **US-3:** As a signed-in user with no permitted items, I want to be told
  that, and who to ask, so I'm not left looking at a blank page.
- **US-4:** As the team building later features, we want a consistent way to
  add an item to the homepage with the permission it requires, so access
  rules are applied the same way everywhere.

## Acceptance criteria
- **AC-1 (US-1):** Given I am not signed in, when I open the homepage, then I
  see the application name, a brief explanation of its purpose (no more than
  a short paragraph), and a "Sign in" link.
- **AC-2 (US-1):** Given I am not signed in, when I select "Sign in", then I
  am taken to the sign-in page.
- **AC-3 (US-1):** Given I am not signed in, when I view the homepage
  (including its page source and network responses), then no homepage items,
  user data or other information that needs a role is present.
- **AC-4 (US-2):** Given I am signed in, when I open the homepage, then I see
  my display name and the homepage items my roles permit, and no "Sign in"
  link.
- **AC-5 (US-2):** Given I am signed in, when I view the homepage, then items
  my roles do not permit are not shown and are not present in the page
  source or network responses.
- **AC-6 (US-2):** Given a user has more than one role, when they view the
  homepage, then they see the combined set of items for all their roles,
  each listed once.
- **AC-7 (US-3):** Given I am signed in but my roles permit no items, when I
  open the homepage, then I see a message saying I don't have access to any
  areas yet and how to request access.
- **AC-8 (US-1, US-4):** Given I complete sign-in from the homepage, when I am
  returned to the application, then I land on the homepage in its signed-in
  state.
- **AC-9:** Given my session has expired, when I open or refresh the
  homepage, then I see the not-signed-in version (AC-1).
- **AC-10 (US-4):** Given a new homepage item is defined with a required
  permission, when users view the homepage, then only users whose roles grant
  that permission see it, with no change to the homepage itself.
- **AC-11:** Both versions of the homepage meet WCAG 2.2 AA.

## Business rules & validation
- Each homepage item has: a label, a short description (optional), a
  destination, and the permission it requires.
- Items appear in a defined, stable order (alphabetical by label unless an
  order is specified).
- Visibility is decided by the user's roles at the time the page is
  requested; role changes take effect at the latest on their next sign-in.
- The homepage itself never grants access; the destination must still
  enforce its own permission check.

## Homepage items
No other features exist yet, so the initial list is a placeholder to be
replaced as features are specified.
| Item | Required role/permission | Destination |
|------|--------------------------|-------------|
| <e.g. "Manage roles"> | Admin | <future feature> |

## Data
- **Entities touched:** User (identity from SSO), Role, Homepage item
- **Classification:** Internal
- **Personal data:** Display name, shown only to that user, on their own homepage.
- **Audit:** None. Viewing the homepage isn't a business record change.

## Screens / interactions
- **Not signed in:** app name, explanation paragraph, primary "Sign in" link.
- **Signed in:** greeting with display name, list of permitted items (label,
  optional description, link), or the empty-state message from AC-7.

## Integrations
- Organization identity provider (via the sign-in flow): provides the
  user's identity, display name and role membership.

## Non-functional requirements
- None beyond `principles.md`.

## Edge cases & errors
- Identity provider unavailable: the not-signed-in homepage still loads; the
  sign-in page is responsible for reporting the failure.
- Signed-in user with no display name from the identity provider: fall back
  to their username/email.
- A homepage item's destination is unavailable: the homepage still lists it;
  the destination reports its own error.

## Open questions
- [ ] What should the explanation text say? Who writes or approves it? (owner TBD, not blocking)
- [ ] Is "Sign out" in scope for this spec, or a separate one? (blocking AC-4 layout)
- [ ] Where do roles come from: identity provider groups/claims, or managed in this app? (blocking plan)
- [ ] Does visibility work per role (as written) or through finer-grained permissions mapped to roles? (blocking plan)
- [ ] What should the empty-state message (AC-7) say about requesting access: a contact, a link, or a ticketing system? (not blocking)
- [ ] Should the homepage be reachable at all from outside the corporate network? (not blocking)
