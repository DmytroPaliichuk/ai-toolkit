---
name: ddd_imp
description: "Claude Code only: implement the next unblocked checklist task and pause for approval before starting another (needs AskUserQuestion). Use after /ddd_cl."
metadata:
  author: Dmytro Paliichuk
---

# Implement

Pick the **next single unblocked** task from the checklist, implement it fully, update the checklist, then pause for review and approval before starting another task. **No new task begins until the current one is approved** — implementation stays mechanical execution of agreed design.

**Environment:** This skill targets **Claude Code** with the **AskUserQuestion** tool (plugin workflow). If it is not available, stop and say this phase requires Claude Code with DDD plugin tools enabled — do not pretend the same approval UX in a plain chat.

## When to Use

- After `/ddd_cl` has produced a checklist
- To continue after the author approved the previous task

## Instructions

When invoked, locate the checklist at the path passed via **`Skill`** from `/ddd_cl`, or at **`docs/ddd_checklist/CL_*.md`**, or another path the author gave. Also open the **`docs/ddd_design/DES_*.md`** design doc that matches this effort when you need architectural context.

Read the checklist and design, then read every file you intend to change **before** editing.

### One task at a time

Select exactly **one** task that is:

- `not started` (or ready to resume if you left it `in progress` in the same session), and  
- not blocked by any incomplete prerequisite task.

Do **not** batch multiple independent tasks into one approval cycle unless the README-sized rule forces merging (see below).

**Status flow:**

- Set **`in progress`** when you start work on the chosen task.  
- Set **`pending approval`** when implementation for that task is ready for review.  
- Set **`completed`** only after the author explicitly approves via **`AskUserQuestion`** (see below). Never mark **`completed`** preemptively.

### Diff sizing vs checklist tasks

If the checklist already sized tasks to **50–200 lines**, implement **one checklist task** per pause. If finishing one task yields **under ~50 lines** of diff and the next task is unblocked and trivially related, you may **merge that execution into one pause** — but still update statuses per checklist row you finished.

If one checklist task would still exceed **200 lines**, split the **work** across pauses (e.g. stubs first, then fill-in) and reflect that in checklist rows or sub-bullets so review stays bounded.

### Quality bar

Add comments where they help future readers understand **why**, not what.

Ensure the change **builds** and **tests pass** when the repo has a standard way to verify that.

### Pause for approval (`AskUserQuestion`)

After each implemented task (or merged small slice per sizing rule), use `AskUserQuestion`:

- **Approved — next task** — mark the task **`completed`**, update the checklist file, then start the **next** unblocked task (still one primary task per cycle unless the under-50 merge rule applies).  
- **Approved — stop here** — mark **`completed`**, update the checklist, stop.  
- **Create PR** — create a branch. If the task depends on another task whose PR exists but is not merged, branch off that PR’s branch (stacked PR); if it depends on a locally completed task without a PR, branch off the current branch; otherwise branch off the default branch (e.g. `main`). Commit, push, open the PR, and record the PR link on the checklist. The task stays **`pending approval`**. After the PR is created, ask again with **Approved — next task**, **Approved — stop here**, and **Stop without approving**.  
- **Stop without approving** — stop; leave the task **`pending approval`** (or **`in progress`** if work is incomplete).

If the author responds with free-text instead of an option, treat it as **changes requested** — adjust implementation or checklist, then ask again.

If **`AskUserQuestion`** is missing, stop and state that Claude Code with the DDD plugin is required — do not simulate approval in plain text unless the user explicitly asks for a manual workflow.

### Approval scope

Ask for approval for **production code** changes. Exceptions:

1. **Whole-file deletions** — no separate approval gate solely for the deletion operation.  
2. **Unit tests** and **documentation-only** edits — may ship in the same approval round as the task they support without a second gate **unless** the author asked otherwise.
