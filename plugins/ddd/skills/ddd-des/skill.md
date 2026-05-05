---
name: ddd_des
description: "Claude Code only: translate locked requirements into a concrete design via structured Q&A (needs AskUserQuestion + Skill tools). Use after /ddd_req; run before /ddd_cl."
metadata:
  author: Dmytro Paliichuk
---

# Design

Translate the requirements document into a concrete design through structured Q&A on meaningful implementation decisions. The author drives architecture and tradeoffs instead of reviewing the model’s autonomous choices after the fact. Keep the design as simple as the requirements allow — no over-engineering.

**Environment:** This skill targets **Claude Code** with the **AskUserQuestion** and **Skill** tools (plugin workflow). If those tools are not available, stop and say this phase requires Claude Code with DDD plugin tools enabled — do not pretend the same UX in a plain chat.

## When to Use

- After `/ddd_req` has produced a requirements doc (requirements must be locked before design)
- Before running `/ddd_cl` — the checklist depends on an agreed design

## Instructions

When invoked, design the functionality described by the **requirements document**. Its path is passed via the **`Skill`** tool when continuing from `/ddd_req`, or find it at **`docs/ddd_requirement/REQ_*.md`** (or another path the author gave in the prompt).

Before detailed questions, read the relevant parts of the codebase to understand existing patterns, structures, and code you can reuse. Do not ask the author for information you can resolve by reading the code.

Start by summarizing your understanding of scope and constraints from the requirements doc in 2–3 sentences. Then use `AskUserQuestion` to confirm that interpretation before drilling into design decisions. Offer options such as "Yes, that's correct", "Partially correct — let me clarify", and "No, that's off — let me re-explain".

The design must stay within the requirements scope. Respect **Out of Scope** — do not design excluded work.

Before writing the design doc, surface **every meaningful implementation decision** through Q&A with the author (stack, boundaries, data model shape, API contracts, error handling strategy, etc.). Requirements already captured **what** and **why**; here you nail **how** at the architecture/API level.

Ask one question at a time. Follow-up questions may depend on earlier answers, so do not batch them.

### Discrete choices (`AskUserQuestion`)

For questions with discrete choices, use `AskUserQuestion` so the author gets selectable options and arrow-key navigation. Only list options that are genuinely on the table — do not pad. Two strong options beat two strong plus two filler. The tool includes an **Other** free-text path. Add **(Recommended)** to the label of your recommended option.

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

### Write the design document

After Q&A, write the final doc. It must be **self-contained** — a reader should not need the chat history. Prefer **[Mermaid](https://mermaid.js.org/)** for architecture, data flow, and sequence where helpful. Include component structure, data models, API or module contracts, and **rationale** for major decisions.

Keep the testing section very succinct unless the change is primarily about testing.

Do **not** add these sections unless the author explicitly asks: generic design principles decks, requirements-style acceptance criteria (already in REQ), rollback strategy, migration plan, future enhancements/work backlog, references bibliography.

- If the user supplied a template or output path in the prompt, honor that.  
- Otherwise create **`docs/ddd_design/DES_<descriptive_suffix>.md`** at the repo root (create `docs/ddd_design/` if needed).  
- Tie the suffix to the same theme as the requirements file when practical.

### Next step (`AskUserQuestion` + `Skill`)

After saving the doc, use `AskUserQuestion`:

- **Move to /ddd_cl** — continue to checklist  
- **Revise design** — edit the doc from feedback  
- **Done for now** — stop; author can resume later  

**Act on the answer.** Do not ask the author to type the next slash command — the chosen option **is** the instruction.

- **Move to /ddd_cl:** Immediately invoke the `ddd:ddd_cl` skill via the **`Skill`** tool, passing the **absolute or repo-relative path** to the design file you just wrote.  
- **Revise design:** Apply feedback, update the file, re-confirm completeness.  
- **Done for now:** Stop.

If `AskUserQuestion` or `Skill` is missing, stop and state that Claude Code with the DDD plugin is required to continue the chain — do not simulate the handoff in plain text unless the user explicitly asks for a manual copy-paste workflow.
