# ddd-imp — Implement

Execute the next unblocked task from `checklist.md`, implement it fully, then pause for user approval before proceeding to the next task.

## When to use

Invoke `/ddd_imp` after `/ddd_cl` has produced an approved `checklist.md`. Re-invoke after each approval to continue with the next task.

## Instructions

1. **Read `checklist.md`.** Identify all tasks that are not yet checked off (`- [ ]`).

2. **Select the next task.** Choose the first unchecked task whose dependencies are all checked off. If multiple tasks are unblocked, pick the one with the lowest task number.

3. **Announce the task.** Before writing any code, state:
   - The task ID and title
   - What you are about to change and why
   - Which files will be affected

4. **Implement the task.** Write the code changes required to satisfy the task's "Done when" criterion. Follow these rules:
   - Stay strictly within the scope of the current task. Do not fix unrelated issues or add unrequested improvements.
   - Match the project's existing code style, naming conventions, and patterns.
   - Include tests if the task's "Done when" criterion requires verifiable behavior.
   - Do not modify `checklist.md` yet.

5. **Present a summary.** After implementation, provide:
   - A brief description of what was changed
   - How to verify the "Done when" criterion is met
   - Any decisions made during implementation that deviate from the design, with justification

6. **Pause for approval.** Ask the user: "Does this look good? Approve to continue to the next task, or give feedback to revise."

7. **On approval:**
   - Mark the task as complete in `checklist.md` (change `- [ ]` to `- [x]`).
   - Tell the user the next unblocked task (if any), or that all tasks are complete.

8. **On revision request:**
   - Apply the requested changes.
   - Re-present the summary and pause for approval again.
   - Do not mark the task complete until approved.

9. **On completion of all tasks:**
   - Confirm all items in `checklist.md` are checked.
   - Summarize what was built in two or three sentences.
   - Suggest any follow-up actions (e.g., deploy, integration test, documentation update) if obvious.

## Constraints

- **One task per invocation.** Never implement more than one task without an explicit approval in between.
- Do not start a task whose dependencies are not yet checked off.
- Do not modify files outside the scope declared in the task's "Files" field unless unavoidable, and if so, explain why.
- Do not refactor, clean up, or improve code outside the current task's scope.
- If a task's "Done when" criterion cannot be met as written (e.g., due to a missing dependency or design gap), stop and ask the user how to proceed rather than improvising.
- Keep diffs within the 50–200 line target. If the implementation is growing beyond that, stop and propose splitting the task before continuing.
