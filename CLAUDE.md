# Working agreement for AI assistants

This repo uses a lightweight, tool-free spec-driven workflow. Specs are the
source of truth; code follows them.

## Before doing anything
1. Read `specs/principles.md` (project-wide rules that apply to every feature).
2. Read the relevant feature folder in `specs/NNN-feature-name/`.
3. Check `specs/decisions/` for accepted decisions (stack, hosting, auth, etc.).

## The loop
1. **Spec** (`spec.md`): what and why. No technology choices. Before drafting,
   ask the human clarifying questions. List anything unresolved under
   "Open questions" rather than guessing.
2. **Plan** (`plan.md`): how. Only start once the spec has no blocking open
   questions. Respect accepted decisions; if a new cross-cutting decision is
   needed, propose a decision record in `specs/decisions/` instead of burying
   it in the plan.
3. **Tasks** (`tasks.md`): small, ordered, each traceable to an acceptance
   criterion (AC-n). One task ≈ one commit.
4. **Implement**: one task at a time. Tick the box in `tasks.md` in the same
   commit as the change.
5. **Verify**: check every acceptance criterion; each should be covered by a
   test or an explicit manual check noted in `tasks.md`.

## Rules
- If the code needs to diverge from the spec, stop and update the spec
  (or ask) first. Never let them silently drift.
- Don't add scope that isn't in the spec. Suggest it under "Open questions"
  or as a future feature instead.
- No framework, language or vendor is chosen until a decision record says so.
  Until then, keep specs and plans technology-neutral or present options.
- Keep documents short. Delete template sections that don't apply rather than
  filling them with "N/A".
