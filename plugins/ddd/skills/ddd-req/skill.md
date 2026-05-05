---
name: ddd_req
description: Use when starting a new feature or project to gather and document requirements through structured Q&A before writing any code or design.
metadata:
  author: Dmytro Paliichuk
---

# Requirements Gathering

Gather and document all requirements for a feature through structured Q&A with the author, producing a self-contained requirements doc. The author makes every scoping decision instead of the AI assuming.

## When to Use

- Starting a new feature or project
- Scope is unclear or has multiple possible interpretations
- Before running `/ddd_des` — requirements must be locked first

## Instructions

I want to gather requirements for this functionality.

Before asking questions, read the relevant parts of the codebase to understand what already exists. Don't ask me things you can answer yourself by reading the code.

Start by summarizing your understanding of what is being built in 2-3 sentences. Then use `AskUserQuestion` to confirm the interpretation is correct before proceeding to questions. Provide options like "Yes, that's correct", "Partially correct — let me clarify", and "No, that's off — let me re-explain". If the `AskUserQuestion` tool is not available, fall back to asking via a text message and wait for a reply.

Ask one question at a time. Follow-up questions may depend on earlier answers, so don't batch them.
For questions with discrete choices, use the `AskUserQuestion` tool to present selectable options. This gives the user a cleaner UX with arrow-key navigation instead of typing numbers. Only list options that would actually be considered — don't pad to 4 options. Two strong options are better than two strong plus two filler. The tool automatically includes an "Other" free-text option, so the user is never forced to pick from the listed choices. Add "(Recommended)" to the label of your recommended option. For questions about visual output, UI layout, rendering, or architecture, use the `preview` field to show ASCII mockups or diagrams of each option — this renders a side-by-side view where the preview updates as the user arrows through choices. **When using previews, EVERY option must have a `preview` field** — if any option is missing one, the UI shows "No preview available" for it. Either add previews to all options or none. **ASCII art in previews must be perfectly aligned.** Before sending, write each preview to a temp file and run `LC_ALL=C awk '{ print length, $0 }' <file>` via Bash to verify alignment. `awk` counts bytes, not display columns, so Unicode box-drawing chars (`┌─┐│└┘├┤┬┴┼`) count as 3 bytes each. This means border-only lines will show a higher byte count than content lines with `│` delimiters. That's expected. What matters is: all border lines match each other, and all content lines match each other. If any line within its group differs, fix the padding and re-check. For architecture questions, show small ASCII diagrams illustrating the component relationships (e.g., `Client → API → DB` or layered boxes).

**Option descriptions must include clear pros and cons.** Every option's description field must follow this structure:
1. A one-line explanation of what the option means
2. **Pros:** bullet list of advantages (prefixed with ✓)
3. **Cons:** bullet list of disadvantages or tradeoffs (prefixed with ✗)

This helps the author make informed decisions without needing to ask follow-up questions about tradeoffs. Even when one option is clearly better, spell out why — the author may know context you don't.
If the `AskUserQuestion` tool is not available, fall back to enumerating options as text with a final free-text option.
Keep asking until all the requirements are very clear.

Don't make implementation decisions in the requirements doc. Requirements describe what the system should do and why, not how to build it. Leave technology choices, libraries, data structures, algorithms, file organization, and architecture for the design phase (`/ddd_des`). For example, "the game reads raw keyboard input (single keypress, no Enter required)" is a valid requirement, but "use `tty`/`termios` for raw input" is an implementation decision.

After the Q&A is done, write the final requirements doc. It must be self-contained — anyone reading it should understand all decisions without replaying our conversation. Include an "Out of Scope" section that explicitly lists what is NOT being built.

If a template or output path is provided in the prompt, use that instead. Otherwise, create the doc as REQ_XXXX.md where XXXX is a descriptive suffix.
Add the file to a docs/ddd_requirement/ folder at the root of the repo.
Don't add code in the requirements doc.
If absolutely necessary for clarity add signatures and short pseudocode.

After writing the requirements doc, use `AskUserQuestion` to ask the author what to do next:
- "Move to /ddd_des" — proceed to the design phase
- "Revise requirements" — make changes to the doc based on feedback
- "Done for now" — stop here and come back later

**Act on the answer yourself. Do not ask the author to type the next slash command — selecting the option IS the instruction to proceed.**
- If they pick "Move to /ddd_des", immediately invoke the `ddd:ddd_des` skill via the `Skill` tool, passing the requirements doc path so the design phase has its input.
- If they pick "Revise requirements", apply their feedback to the doc and re-confirm.
- If they pick "Done for now", stop.

If the `AskUserQuestion` tool is not available, fall back to asking via a text message and wait for a reply, then act on the reply the same way.
