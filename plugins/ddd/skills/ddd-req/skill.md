---
name: ddd_req
description: "Claude Code only: gather and document requirements via structured Q&A (needs AskUserQuestion + Skill tools) before code or design. Use for new features/projects or unclear scope; run before /ddd_des."
metadata:
  author: Dmytro Paliichuk
---

# Requirements Gathering

Gather and document all requirements for a feature through structured Q&A with the author, producing a self-contained requirements doc. The author makes every scoping decision instead of the AI assuming.

**Environment:** This skill targets **Claude Code** with the **AskUserQuestion** and **Skill** tools (plugin workflow). If those tools are not available, stop and say this phase requires Claude Code with DDD plugin tools enabled — do not pretend the same UX in a plain chat.

## When to Use

- Starting a new feature or project
- Scope is unclear or has multiple possible interpretations
- Before running `/ddd_des` — requirements must be locked first

## Instructions

When invoked, gather requirements for the functionality described in the user's message (or the current task).

Before asking questions, read the relevant parts of the codebase to understand what already exists. Do not ask the author for information you can resolve by reading the code.

Start by summarizing your understanding of what is being built in 2–3 sentences. Then use `AskUserQuestion` to confirm the interpretation before detailed questions. Offer options such as "Yes, that's correct", "Partially correct — let me clarify", and "No, that's off — let me re-explain".

Ask one question at a time. Follow-up questions may depend on earlier answers, so do not batch them.

### Discrete choices (`AskUserQuestion`)

For questions with discrete choices, use `AskUserQuestion` so the author gets selectable options and arrow-key navigation. Only list options that are genuinely on the table — do not pad. Two strong options beat two strong plus two filler. The tool includes an **Other** free-text path, so the author is never trapped in the list. Add **(Recommended)** to the label of your recommended option.

### Previews (UI, layout, rendering, architecture)

For questions about visual output, UI layout, rendering, or architecture, you may use the `preview` field on each option with ASCII mockups or small diagrams. The preview updates as the author moves through choices.

- **All options or none:** If you use `preview`, **every** option must include a `preview` field. Otherwise the UI shows "No preview available" for gaps.
- **Architecture:** Prefer small ASCII diagrams (e.g. `Client → API → DB` or layered boxes).

### ASCII alignment in previews

ASCII art in previews should line up cleanly in a monospace view.

1. Write each preview to a temp file and run `LC_ALL=C awk '{ print length, $0 }' <file>` via Bash to compare line lengths within the preview.
2. This is a **best-effort** check: `awk` counts **bytes**, not terminal display width. Unicode box-drawing characters (`┌─┐│└┘├┤┬┴┼`) count as multiple bytes, so border lines may show larger byte counts than text-only lines — that can be expected. What matters is consistency within **border** lines vs **content** lines; **visual proof in the preview still wins** if the tool UI looks misaligned.
3. If Bash is unavailable, rely on careful manual alignment.

### Option descriptions (pros and cons)

Every option's `description` should include:

1. One line: what the option means  
2. **Pros:** bullets, prefix with ✓ (use `+` if Unicode is mangled)  
3. **Cons:** bullets, prefix with ✗ (use `-` if Unicode is mangled)

Spell out tradeoffs even when one option seems obviously better — the author may have context you do not.

### When Q&A is complete (exit checklist)

Stop asking only when there is nothing important left ambiguous for **what** and **why** (not **how**). Before writing the final doc, you should have covered everything that applies to this effort:

- **Actors / users** — who interacts with the system  
- **Primary flows** — happy path and main user journeys  
- **Acceptance criteria** — what "done" means in observable terms  
- **Errors & edge cases** — invalid input, empty state, partial failure, permissions  
- **Non-functionals** — performance, reliability, security/privacy, compliance, when relevant  
- **Integrations & data** — external systems, imports/exports, retention, who owns what data  
- **i18n / a11y** — if the product is multilingual or has accessibility expectations  
- **Explicit open questions or assumptions** — anything still uncertain, called out honestly

If a topic does not apply, say so in the final doc rather than inventing requirements.

### Separation from design

Do not bake implementation into the requirements doc. Requirements describe behavior, constraints, and rationale in business/product terms. Leave technology choices, libraries, structures, algorithms, file layout, and architecture to `/ddd_des`. Example: "Single keypress registers immediately without Enter" is fine; "use `termios` raw mode" is design.

### Write the requirements document

After Q&A, write the final doc. It must be **self-contained** — a reader should not need the chat history. Include an **Out of Scope** section listing what is explicitly **not** being built.

- If the user supplied a template or output path in the prompt, honor that.  
- Otherwise create **`docs/ddd_requirement/REQ_<descriptive_suffix>.md`** at the repo root (create `docs/ddd_requirement/` if needed).  
- Start the file with YAML frontmatter containing a **`created`** field: the current local time in ISO 8601 with offset (e.g. `created: 2026-09-28T14:05:00+0300`). Get it by running `date +%Y-%m-%dT%H:%M:%S%z` via Bash — do not guess. Set it once when the file is first written; do not change it on revisions.  
- Do not put implementation code in the doc; if needed, signatures or short pseudocode only.

### Next step (`AskUserQuestion` + `Skill`)

After saving the doc, use `AskUserQuestion`:

- **Move to /ddd_des** — continue to design  
- **Revise requirements** — edit the doc from feedback  
- **Done for now** — stop; author can resume later  

**Act on the answer.** Do not ask the author to type the next slash command — the chosen option **is** the instruction.

- **Move to /ddd_des:** Immediately invoke the `ddd:ddd_des` skill via the **`Skill`** tool, passing the **absolute or repo-relative path** to the requirements file you just wrote so design can read it.  
- **Revise requirements:** Apply feedback, update the file, re-confirm completeness.  
- **Done for now:** Stop.

If `AskUserQuestion` or `Skill` is missing, stop and state that Claude Code with the DDD plugin is required to continue the chain — do not simulate the handoff in plain text unless the user explicitly asks for a manual copy-paste workflow.
