---
description: "Generate a Dataverse table schema with columns, relationships, and ER diagram from a plain-text requirements description"
agent: agent
argument-hint: "Describe the entity and its requirements in plain text"
---
Generate a Dataverse table schema for the VisitTrack solution based on the following requirements:

${input}

## Requirements for the output

1. **Column table** in this format:

| Column Name | Display Name | Type | Required | Description |
|---|---|---|---|---|

   - Use table prefix `vt_` and publisher `medpharma`
   - Always include `vt_name` as the primary column (Single Line of Text, required)
   - Lookup columns end with `_id`

2. **Relationship list** — for each foreign key:
   - Related table
   - Relationship type (1:N or N:M)
   - Cascade delete rule (Cascade / RemoveLink / Restrict / None) with justification

3. **Mermaid erDiagram** showing all tables and relationships

4. **Row-Level Security recommendation** — which security model fits best:
   - Owner-based (user owns their own records)
   - Team-based (team shares records)
   - Business Unit hierarchy

5. **Open questions** — anything that needs clarification before implementation
