# ADR-001: Use Canvas Apps Instead of Model-Driven Apps

## Status

Proposed

## Context

The kodschul demo team is building a task management application targeting max 50 users with a focus on rapid demo iteration and mobile-first user experience. The team must choose between Power Apps Canvas Apps and Model-driven Apps. Canvas Apps offer faster prototyping and direct UI control, while Model-driven Apps provide enterprise-scale metadata-driven architecture. For a demo scenario with mobile-first requirements and tight iteration cycles, the development velocity and form-factor flexibility become critical constraints.

## Decision

We will use **Canvas Apps** as the primary UI framework for this demo task management solution. Canvas Apps enable rapid mobile-friendly UI development, reduce dependency on admin-heavy metadata configurations, and align with the demo's goal of delivering high visual impact with minimal overhead.

## Consequences

### Positive

- **Rapid prototyping**: Canvas Apps compile and deploy faster; UI changes are immediately visible without metadata refresh cycles
- **Mobile-first flexibility**: Native responsive design and touch-optimized controls reduce mobile UX friction
- **Demo agility**: Can pivot UI/UX quickly based on stakeholder feedback without entity redesigns
- **Lower governance overhead**: No need for complex entity relationships or column security for a 50-user demo
- **Cost efficiency**: Fewer Premium connectors required for basic task management vs. advanced model-driven scenarios

### Negative / Trade-offs

- **Limited enterprise patterns**: Canvas Apps lack built-in workflows, email integration flows, and audit trails that Model-driven Apps provide via metadata
- **Component reuse**: Canvas component library is less sophisticated; code duplication risk across multiple screens
- **Form generation**: Cannot auto-generate forms from entity metadata; all screens must be hand-built
- **Scalability ceiling**: Service Protection Limits on Web API calls become visible at scale; 50 users is manageable but row-level security logic must be handled in formulas
- **Maintenance burden**: Custom Power Fx logic replaces model-driven rule engine; increased testing surface

## Alternatives Considered

| Option  | Why rejected |
| ------- | ------------ |
| Model-driven Apps | Enterprise metadata architecture introduces unnecessary complexity for a 50-user demo; slower iteration cycles due to solution layering; mobile experience requires additional work through mobile app studio |
| Hybrid (Model-driven + Portal) | Splits team focus; adds Portal licensing cost and ALM complexity; defeats goal of rapid mobile-first delivery |
| Power Pages | Not appropriate for internal task management; designed for external user engagement; lacks real-time collaboration UI patterns needed for team tasks |

## License & Cost Impact

**Canvas Apps Licensing:**
- Per-app licenses (USD ~5–10/user/month) or per-user licenses (USD ~20/user/month) cover the 50-user scenario affordably
- Dataverse storage at 50 users is minimal; no premium connector licensing needed for basic task CRUD (SharePoint, Excel, Office 365 connections included in Power Apps license)
- No model-driven app designer licensing overhead

**Potential cost escalations avoided:**
- Avoiding Model-driven apps eliminates need for Dynamics 365 licensing (~USD 50+/user/month) if enterprise features are later required
- Avoiding Portal eliminates USD ~200–500/month subscription

## ALM Impact

**Solution readiness:**
- Canvas App (.pa.yaml) is Solution-aware; can move DEV → TEST → PROD via Power Platform CLI (pac) and solution-based deployments
- Dataverse-backed data (tables, columns) must be Solution-aware and deployed first
- Power Automate flows (if added later) are also Solution-aware
- **Limitation**: Power Fx formulas are not easily diffable or code-reviewed in source control; recommend pair programming and manual test gates at each environment

**Deployment pipeline:**
1. Create unmanaged solution in DEV
2. Add Canvas App + Dataverse table to solution
3. Export as managed solution
4. Import into TEST, validate, approve
5. Import into PROD
6. Supported by Power Platform Pipelines (native CI/CD tool, no external infrastructure)

**Recommendation:** Implement Solution layering early; avoid ad-hoc Canvas App deployments outside solutions.

---

## Next Steps

- Validate Dataverse table schema with team
- Define row-level security scoping in Power Fx (if multi-team visibility needed)
- Plan Power Automate flow integration for task notifications
- Set up DEV/TEST/PROD solution structure with Power Platform Pipelines
