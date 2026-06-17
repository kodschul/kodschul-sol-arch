---
description: "Run a Fit/Gap analysis of a requirements list against Power Platform capabilities. Produces a prioritized table with component recommendations and license implications."
agent: ask
argument-hint: "Paste your requirements list or describe the business process"
model: Claude Sonnet 4.6
---

Analyze the following requirements against Power Platform capabilities and produce a Fit/Gap report.

${input}

## Output format

### 1. Fit/Gap Table

| #   | Requirement | Fit Level | PP Component | Gap / Risk | License needed |
| --- | ----------- | --------- | ------------ | ---------- | -------------- |

Fit levels:

- **Native** — works out of the box without customization
- **Low-Code** — achievable with Canvas App / Flow / configuration
- **Pro-Code** — requires Plugin, PCF, Azure Function, or custom connector
- **Gap** — not achievable in Power Platform, needs external system

### 2. MoSCoW Prioritization

Group requirements into Must / Should / Could / Won't Have. Justify each decision in one sentence.

### 3. License Cost Summary

List which requirements trigger a premium license:

- Power Apps Premium (Dataverse, custom connectors)
- AI Builder Credits (document processing, object detection)
- Copilot Studio Messages (agent interactions)
- Azure costs (AI Search, OpenAI)

### 4. Top 3 Architecture Risks

For the three highest-risk requirements, describe:

- The risk
- Likelihood (High / Medium / Low)
- Recommended mitigation
