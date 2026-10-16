# spec-test-00
A repo for experimenting with spec driven development, without committing to
a specific tool. Plain Markdown and a simple loop:

**spec → plan → tasks → implement → verify**

## Layout
```
CLAUDE.md                 # workflow rules for AI assistants
specs/
  principles.md           # project-wide rules (auth, data, quality bars)
  decisions/              # numbered decision records (stack, hosting, …)
  _templates/             # spec.md, plan.md, tasks.md, decision.md
  NNN-feature-name/       # one folder per feature
    spec.md               # what & why (tech-neutral)
    plan.md               # how
    tasks.md              # ordered checklist tied to acceptance criteria
```

## Starting a feature
```sh
mkdir specs/001-my-feature
cp specs/_templates/{spec,plan,tasks}.md specs/001-my-feature/
```
Then draft `spec.md` with your AI assistant: ask it to interview you and
list open questions before writing a plan or any code.

## Tips
- Fill in the `TBD`s in `specs/principles.md` as you learn them.
- Change the spec first, then the code.
- Keep a short note of what the spec caught or missed after each feature,
  to decide what (if anything) is worth automating.
