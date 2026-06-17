---
description: "Write an Architecture Decision Record (ADR) for a Power Platform design decision. Requires a title and context; produces a complete ADR with alternatives, trade-offs, and consequences."
agent: agent
argument-hint: "Describe the decision to make and its context (e.g. Canvas App vs Model-Driven App for the ADM dashboard)"

model: Claude Haiku 4.5 (copilot)
---

Write an Architecture Decision Record (ADR) for the following decision:

${input}

## Output format

```markdown
# ADR-NNN: [Short imperative title]

## Status

Proposed

## Context

[2-4 sentences: what situation led to this decision, what constraints exist]

## Decision

[1-3 sentences: what was decided and the primary reason]

## Consequences

### Positive

- [outcome]
- [outcome]

### Negative / Trade-offs

- [trade-off]
- [trade-off]

## Alternatives Considered

| Option  | Why rejected |
| ------- | ------------ |
| [alt 1] | [reason]     |
| [alt 2] | [reason]     |

## License & Cost Impact

[What licensing tier does this decision require or avoid?]

## ALM Impact

[Is this component Solution-aware? Can it move DEV → TEST → PROD without manual steps?]
```

Ensure the ADR is specific to Power Platform context (Dataverse, Canvas Apps, Power Automate, Copilot Studio).
Mention Service Protection Limits if the decision involves Dataverse Web API at scale.
