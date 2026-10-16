# Plan: <Feature name>

- **Spec:** ./spec.md
- **Status:** Draft | Approved
- **Last updated:** YYYY-MM-DD

> How we'll build the spec. Must respect `specs/principles.md` and accepted
> decisions in `specs/decisions/`.

## Approach
<Short summary of the solution in a paragraph or two.>

## Relevant decisions
- <Link to decision records this plan depends on, e.g. ../decisions/0001-stack.md>
- <New decisions needed? Propose a record instead of deciding here.>

## Components
| Component | Responsibility | New / changed |
|-----------|----------------|---------------|
| <e.g. Requests UI> | <…> | New |

## Data model
<Entities, key fields, relationships, constraints. Pseudocode or a table is fine.>

## Interfaces
<Endpoints, events, messages, or module boundaries with request/response shapes.>

## Authorization
<How each AC's role rules are enforced, and where.>

## Integrations
<Adapter boundaries, failure handling, retries, how they're faked in tests.>

## Migration & rollout
<Data migrations, feature flags, backwards compatibility, rollback plan.>

## Testing strategy
| AC | How it's verified |
|----|-------------------|
| AC-1 | <unit / integration / end-to-end / manual> |

## Risks & alternatives considered
- <Risk> → <mitigation>
- <Alternative> → <why not chosen>
