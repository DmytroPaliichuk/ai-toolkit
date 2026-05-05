# Decision Driven Development (DDD)

| | |
|---|---|
| **Author** | Dmytro Paliichuk |
| **Version** | 0.1.0 |
| **Works with** | Claude Code |

---

## Overview

DDD is a four-phase workflow that front-loads all ambiguity resolution before a single line of code is written. Each phase produces a document that becomes the input for the next, giving you a clear audit trail from business intent to working software.

Inspired by [Spec-Driven Development: AI Assisted Coding Explained](https://www.youtube.com/watch?v=mViFYTwWvcM&t=350s).

---

## The Four Phases

```
/ddd_req  →  /ddd_des  →  /ddd_cl  →  /ddd_imp
```

| Phase | Command | Purpose |
|-------|---------|---------|
| Requirements | `/ddd_req` | Capture all business and technical requirements via structured Q&A |
| Design | `/ddd_des` | Translate requirements into a concrete design with diagrams |
| Checklist | `/ddd_cl` | Decompose the design into dependency-ordered, implementable tasks |
| Implement | `/ddd_imp` | Execute one task at a time, pausing for approval between each |

---

## Phase Details

### `/ddd_req` — Requirements

Conducts a structured Q&A session to surface every functional requirement, constraint, and open question before design begins. Nothing is assumed. The session ends only when the requirements are unambiguous and complete.

**Output:** `docs/ddd_requirement/REQ_<descriptive_suffix>.md` (unless the session specifies another path) — goals, user stories, acceptance criteria, constraints, and out-of-scope items.

---

### `/ddd_des` — Design

Takes the requirements document and walks through every meaningful implementation decision via Q&A. Produces Mermaid diagrams for architecture, data flow, and sequence where helpful. Design remains simple — no over-engineering.

**Input:** The requirements file from phase 1 (path passed via `Skill`, or `docs/ddd_requirement/REQ_*.md`)  
**Output:** A `docs/ddd_design/DES_<descriptive_suffix>.md` document with component diagrams, data models, API contracts, and rationale for each decision.

---

### `/ddd_cl` — Checklist

Decomposes the design into a granular, dependency-ordered list of tasks. Each task targets a diff of 50–200 lines — small enough to review confidently, large enough to be meaningful.

**Input:** `docs/ddd_design/DES_<descriptive_suffix>.md`
**Output:** A `docs/ddd_checklist/CL_<descriptive_suffix>.md` with tasks ordered by dependency, each scoped to a single coherent change.

---

### `/ddd_imp` — Implement

Picks the next unblocked task from the checklist, implements it fully, then pauses for your review and approval before continuing. No task is started until the previous one is approved.

**Input:** `docs/ddd_checklist/CL_<descriptive_suffix>.md`
**Output:** Code changes committed one task at a time, with the checklist updated after each approval.

---

## Why This Order Matters

Skipping ahead is the most common source of rework. Writing code before requirements are clear means discovering mismatches late. Writing code before design is settled means refactoring structure while fixing bugs. DDD makes each phase's output a stable contract for the next, so the implementation phase is purely mechanical execution of well-understood decisions.
