---
description: "Use when making architecture decisions, comparing technology options, writing Architecture Decision Records (ADRs), evaluating trade-offs between Power Platform components, or deciding between Canvas App vs Model-Driven App, Flow vs Agent, custom code vs low-code."
---
# Architecture Decision Guidelines — Power Platform SA

## ADR Format
Every significant decision gets an ADR. Use this structure:

```markdown
# ADR-NNN: [Short title]

## Status
[Proposed | Accepted | Deprecated | Superseded by ADR-NNN]

## Context
[What is the situation? What forces are at play? 2-4 sentences.]

## Decision
[What was decided and why? Be specific.]

## Consequences
- ✓ [Positive outcome]
- ✓ [Positive outcome]
- ✗ [Negative outcome / trade-off]
- ✗ [Negative outcome / trade-off]

## Alternatives Considered
| Option | Reason rejected |
|---|---|
| ... | ... |
```

## Common Decision Patterns

### Canvas App vs Model-Driven App
- Canvas App when: custom layout, offline required, mobile-first, non-standard navigation
- Model-Driven App when: standard CRUD on many entities, admin/back-office, automatic views/forms

### Power Automate Flow vs Copilot Studio Agent
- Flow when: deterministic logic, scheduled tasks, system-to-system integration
- Agent when: natural language input, variable multi-step reasoning, Q&A over documents

### Copilot Studio Knowledge Source vs Azure AI Search
- Knowledge Source when: <100 docs, standard chunking, no Azure budget, setup in <1 day
- Azure AI Search when: large corpus, hybrid search, custom chunking, production quality RAG

### Plugin vs Cloud Flow
- Plugin when: synchronous business logic, pre/post operation validation, >10 req/sec
- Cloud Flow when: async processes, integration, low-code maintainability preferred

## Always Include in Architecture Decisions
1. **License cost** — which tier is required (Power Apps Premium, AI Builder Credits, Copilot Studio Messages)
2. **Service Protection Limits** — API call limits for Dataverse Web API (6000 req/5min per user)
3. **Offline impact** — what breaks without connectivity
4. **ALM impact** — is the component Solution-aware, can it be promoted DEV→TEST→PROD
