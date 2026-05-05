# ddd-cl — Checklist

Decompose an approved `design.md` into a granular, dependency-ordered list of implementable tasks, where each task targets a diff of 50–200 lines.

## When to use

Invoke `/ddd_cl` after `/ddd_des` has produced an approved `design.md`. Do not proceed to `/ddd_imp` until this phase is complete and the user has confirmed the checklist.

## Instructions

1. **Read `design.md` and `requirements.md`.** Understand the full scope before generating any tasks.

2. **Identify the implementation units.** Break the design into the smallest coherent changes that can be implemented, tested, and reviewed independently. Each unit should:
   - Have a single clear purpose
   - Target a diff of 50–200 lines (not a hard limit, but a strong guideline)
   - Be verifiable — there is a concrete way to confirm it is done

3. **Determine dependencies.** For each task, identify which other tasks must be complete before it can start. Tasks with no dependencies are the starting tasks.

4. **Order the tasks.** Sequence them so that every task's dependencies appear before it in the list. Where multiple tasks are unblocked at the same time, order them by logical cohesion (e.g., schema before service before handler before test).

5. **Draft `checklist.md`.** Produce the document using the structure below. Present it to the user and ask for confirmation or corrections.

6. **Iterate until approved.** Apply any corrections and re-present. Repeat until the user explicitly approves.

7. **Signal completion.** When approved, tell the user: "Checklist is complete. Run `/ddd_imp` to begin implementation."

## Output format

Save to `checklist.md` in the project root (or a `docs/` directory if one exists).

```markdown
# Implementation Checklist: [Feature/System Name]

Each task is scoped to a single coherent change (target: 50–200 lines).
Check off tasks as they are completed and approved.

## Tasks

- [ ] **T01 · [Task title]**
  - What: [One sentence describing what changes]
  - Files: [List of files expected to change]
  - Done when: [Concrete, testable completion criterion]
  - Depends on: none

- [ ] **T02 · [Task title]**
  - What: [One sentence describing what changes]
  - Files: [List of files expected to change]
  - Done when: [Concrete, testable completion criterion]
  - Depends on: T01

- [ ] **T03 · [Task title]**
  ...
```

## Sizing guidelines

| Scenario | Guidance |
|---|---|
| Task is > 200 lines | Split into two or more tasks |
| Task is < 20 lines | Consider merging with a related task unless it is a standalone config or schema change |
| Task has more than 3 dependencies | Review whether a preceding task can be merged to reduce dependency depth |
| Task cannot be verified | Rewrite the "Done when" criterion or split until it can be |

## Constraints

- Every task MUST have a "Done when" criterion that is concrete and testable.
- Do not create tasks for documentation, comments, or code style unless they are part of the requirements.
- Do not create a task for "write tests" in isolation — testing is part of the task it covers.
- Do not write implementation code during this phase.
- The checklist is the source of truth for `/ddd_imp`. It must be complete enough that implementation requires no further design decisions.
