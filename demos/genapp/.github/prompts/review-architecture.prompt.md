---
description: "Review a Power Platform architecture document, data model, Canvas App design, or solution structure against a standard SA checklist. Produces a structured report with OK / Warning / Problem findings."
agent: agent
argument-hint: "Paste or reference the document or file to review"
---
Review the following Power Platform architecture artefact and produce a structured SA review report.

${input}

## Review checklist

Check each item and mark as ✓ OK, ⚠ Warning, or ✗ Problem. For every Warning or Problem provide one concrete recommendation.

### Naming & Conventions
- [ ] Table prefix `vt_` used consistently
- [ ] Publisher prefix `medpharma` correct
- [ ] Power Fx variables follow `gbl/loc/col` pattern
- [ ] Controls named with `btnX/lblX/galX/frmX/txtX` pattern
- [ ] No hardcoded environment URLs (use Environment Variables)

### Data Model
- [ ] All relationships have explicit cascade rules documented
- [ ] No column name collisions with system columns
- [ ] Lookup columns end with `_id`
- [ ] Primary column (`vt_name`) present on all custom tables

### Security
- [ ] Row-Level Security strategy chosen (Owner / Team / BU)
- [ ] Column-Level Security applied where sensitive data present
- [ ] No overly broad security roles (BA: Read all > 1000 records?)

### ALM / Solution
- [ ] Component is Solution-aware (can be exported/imported)
- [ ] No dependencies outside the solution
- [ ] Managed vs Unmanaged usage is correct (Unmanaged DEV, Managed TEST/PROD)

### Performance & Limits
- [ ] Canvas App galleries use delegable filter expressions (`StartsWith`, not `Contains`)
- [ ] Cloud Flows respect daily run limits (standard: 30k/day, premium: 500k/day)
- [ ] Dataverse API usage stays within Service Protection Limits (6000 req/5min/user)

### Offline (if applicable)
- [ ] Offline-capable screens identified
- [ ] `Connection.Connected` check implemented for all online-only features

## Output format

1. **Checklist results** — each item with status symbol
2. **Findings table** — `Area | Status | Finding | Recommendation`
3. **Top 3 risks** — the most critical issues with suggested fix
4. **Approval recommendation** — Approve / Approve with changes / Reject (with reason)
