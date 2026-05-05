# ddd-des — Design

Translate an approved `requirements.md` into a concrete technical design document through a structured decision-making Q&A, producing `design.md` with diagrams.

## When to use

Invoke `/ddd_des` after `/ddd_req` has produced an approved `requirements.md`. Do not proceed to `/ddd_cl` until this phase is complete and the user has confirmed the design document.

## Instructions

1. **Read `requirements.md`.** Parse the requirements before asking any questions. Identify the key design decisions that need to be made.

2. **Present the decision list.** Show the user a numbered list of the design decisions you identified. Ask if anything is missing before proceeding.

3. **Work through each decision via Q&A.** For each decision:
   - State the decision clearly (e.g., "How should authentication tokens be stored?").
   - Offer 2–3 concrete options with a one-line tradeoff for each.
   - Ask the user to choose or propose an alternative.
   - Record the decision and the rationale.

   Work through decisions in this order:
   - **Architecture** — Overall structure, layers, deployment model.
   - **Data model** — Entities, relationships, schemas, storage technology.
   - **API contracts** — Endpoints, request/response shapes, error codes.
   - **Component responsibilities** — What each module/service does and does not own.
   - **External integrations** — How third-party systems are called and failure is handled.
   - **Error handling and edge cases** — What happens when things go wrong.
   - **Security** — Authentication, authorization, data protection, input validation.
   - **Observability** — Logging, metrics, tracing, alerting.

4. **Generate diagrams.** After decisions are made, produce Mermaid diagrams as appropriate:
   - Architecture diagram (component/C4 style)
   - Data model (entity-relationship or class diagram)
   - Key sequence diagrams for non-obvious flows

5. **Draft `design.md`.** Produce the document using the structure below. Present it to the user and ask for confirmation or corrections.

6. **Iterate until approved.** Apply any corrections and re-present. Repeat until the user explicitly approves.

7. **Signal completion.** When approved, tell the user: "Design is complete. Run `/ddd_cl` to generate the implementation checklist."

## Output format

Save to `design.md` in the project root (or a `docs/` directory if one exists).

```markdown
# Design: [Feature/System Name]

## Architecture

[Description of overall structure]

```mermaid
[architecture diagram]
```

## Data Model

[Description of entities and relationships]

```mermaid
[entity-relationship or class diagram]
```

## API Contracts

[Endpoint definitions, request/response shapes, error codes]

## Component Responsibilities

[What each module/service owns and its boundaries]

## External Integrations

[How each external system is called, retry strategy, failure handling]

## Error Handling

[Error taxonomy, propagation strategy, user-facing messages]

## Security

[Auth model, authorization rules, data protection, input validation]

## Observability

[Logging strategy, key metrics, alerting thresholds]

## Key Sequence Flows

[Mermaid sequence diagrams for non-obvious paths]

## Decision Log

| Decision | Options Considered | Choice | Rationale |
|---|---|---|---|
```

## Constraints

- Keep the design **as simple as the requirements allow**. No speculative abstractions.
- Ask one decision at a time. Do not batch decisions into a single question.
- Do not write implementation code.
- Every decision in the document must have a recorded rationale.
- Diagrams must use valid Mermaid syntax.
