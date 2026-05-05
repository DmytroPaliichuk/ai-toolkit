---
name: ddd_cl
description: "Claude Code only: break an agreed design into dependency-ordered, review-sized tasks (needs AskUserQuestion + Skill tools). Use after /ddd_des; run before /ddd_imp."
metadata:
  author: Dmytro Paliichuk
---

# Checklist

Decompose the design document into a granular, **dependency-ordered** list of tasks. Each task targets a **single coherent change** with an expected diff of **50–200 lines** — small enough to review confidently, large enough to be meaningful.

**Environment:** This skill targets **Claude Code** with the **AskUserQuestion** and **Skill** tools (plugin workflow). If those tools are not available, stop and say this phase requires Claude Code with DDD plugin tools enabled — do not pretend the same UX in a plain chat.

## When to Use

- After `/ddd_des` has produced a design doc (design must be agreed before checklist)
- Before running `/ddd_imp` — implementation follows the checklist order

## Instructions

When invoked, build the checklist from the **design document**. Its path is passed via the **`Skill`** tool when continuing from `/ddd_des`, or find it at **`docs/ddd_design/DES_*.md`** (or another path the author gave in the prompt).

First, read the design doc. Then read the relevant parts of the codebase so tasks reflect what already exists — do not plan work for code that is already done.

Tasks must be **clearly enumerated** so they are easy to reference. Each task should name the files it will create or modify.

Order tasks by **dependency** (what must land before what). Each task should be a **compilable**, commit-sized unit where possible. For large types or files: skeleton first in a buildable state, then helpers/utilities, then behavior that depends on those pieces.

**Sizing:** Aim for **50–200 lines of diff** per task. If a slice would be **under 50 lines**, merge it with the next dependent task. If it would exceed **200 lines**, split it (e.g. stubs/skeleton first, then implementations as follow-up tasks).

Use these **status** values on each task: `not started`, `in progress`, `pending approval`, `completed`. Treat **`completed`** as **author-approved only** — see `/ddd_imp` for the approval gate.

- If the user supplied a template or output path in the prompt, honor that.  
- Otherwise create **`docs/ddd_checklist/CL_<descriptive_suffix>.md`** at the repo root (create `docs/ddd_checklist/` if needed).  
- Align the suffix with the design/requirements naming when practical.

### Next step (`AskUserQuestion` + `Skill`)

After saving the checklist, use `AskUserQuestion`:

- **Move to /ddd_imp** — start implementation  
- **Revise checklist** — adjust breakdown, ordering, or sizing from feedback  
- **Done for now** — stop; author can resume later  

**Act on the answer.** Do not ask the author to type the next slash command — the chosen option **is** the instruction.

- **Move to /ddd_imp:** Immediately invoke the `ddd:ddd_imp` skill via the **`Skill`** tool, passing the **absolute or repo-relative path** to the checklist file you just wrote.  
- **Revise checklist:** Apply feedback and re-confirm.  
- **Done for now:** Stop.

If `AskUserQuestion` or `Skill` is missing, stop and state that Claude Code with the DDD plugin is required to continue the chain — do not simulate the handoff in plain text unless the user explicitly asks for a manual copy-paste workflow.
