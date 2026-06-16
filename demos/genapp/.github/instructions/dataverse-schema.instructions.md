---
description: "Use when designing, reviewing, or generating Dataverse table schemas, entity relationships, columns, keys, or security roles for Power Platform. Covers naming conventions, relationship cascade rules, and column type selection."
---
# Dataverse Schema Guidelines — VisitTrack

## Naming
- Table prefix: `vt_` — e.g. `vt_visits`, `vt_physicians`, `vt_users`
- Publisher prefix: `medpharma`, Solution: `VisitTrack`
- Column names: snake_case with table prefix — e.g. `vt_visit_date`, `vt_physician_name`
- Lookup columns: suffix `_id` — e.g. `vt_physician_id`

## Column Types
| Need | Type |
|---|---|
| Short text (names, codes) | Single Line of Text |
| Long text, notes | Multiline Text |
| Date only | Date Only |
| Date + time | Date and Time |
| Whole number | Whole Number |
| Decimal / money | Currency or Decimal Number |
| True/False flag | Two Options (boolean) |
| Constrained list | Choice (local) or Global Choice |
| Related record | Lookup |

## Relationships & Cascade Rules
Always specify cascade behavior explicitly:

| Scenario | Delete Rule |
|---|---|
| Child record meaningless without parent | `Cascade` (delete child) |
| Child record useful independently | `RemoveLink` (clear lookup) |
| Must not delete parent while children exist | `Restrict` |
| No automatic action | `None` |

## Required Fields to Always Include
Every custom table needs:
- `vt_name` (Single Line of Text, required) — primary name column
- `statecode` / `statuscode` — active/inactive (built-in, do not recreate)

## Output Format
When generating a schema, always output:
1. Markdown table: `Column Name | Display Name | Type | Required | Description`
2. Mermaid `erDiagram` with relationship types (`||--o{`, `}o--||`)
3. List of cascade rules per relationship

## Storage Limits
- Max columns per table: 400 (soft limit ~100 for maintainability)
- Max tables per solution: no hard limit, keep related tables in one solution
- File/image columns count against 10 GB storage allowance
