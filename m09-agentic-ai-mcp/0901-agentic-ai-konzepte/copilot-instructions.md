# VisitTrack — Solution Architecture Workspace

> Diese Datei gehört nach `.github/copilot-instructions.md` im Workspace-Root.
> Copilot liest sie automatisch bei jedem Chat-Aufruf.

## Projektkontext

- **Kunde:** MedPharma GmbH
- **Projekt:** VisitTrack — Power Platform Lösung zur Besuchserfassung für Außendienstmitarbeiter
- **Rollen:** ADM (Außendienstmitarbeiter), Manager, Solution Architect, Admin
- **Umgebungen:** DEV → TEST → PROD (Power Platform Pipelines)

## Technologie-Stack

- **Power Apps:** Canvas Apps (mobil, offline-fähig), Model-Driven Apps (Manager/Admin)
- **Dataverse:** Primäre Datenhaltung, Row-Level Security, Column-Level Security
- **Power Automate:** Cloud Flows für Integration, Scheduled Flows für Batch-Prozesse
- **Copilot Studio:** Agents für ADM-Unterstützung und Management-Analytics
- **Azure:** Azure AD (Identität), Azure AI Search (RAG/Knowledge), Azure OpenAI (GPT-4o)
- **ALM:** PAC CLI, GitHub Actions, Power Platform Pipelines

## Konventionen

### Dataverse

- Tabellenpräfix: `vt_` (z.B. `vt_visits`, `vt_physicians`, `vt_users`)
- Publisher-Prefix: `medpharma`
- Solution Name: `VisitTrack`
- Spaltennamen: snake_case mit Präfix (z.B. `vt_visit_date`, `vt_physician_name`)
- Beziehungen: immer mit Cascade-Delete-Strategie dokumentieren (None / RemoveLink / Cascade / Restrict)

### Power Fx (Canvas Apps)

- Globale Variablen: `gbl`-Präfix (z.B. `gblCurrentUser`, `gblSelectedVisit`)
- Lokale Variablen: `loc`-Präfix (z.B. `locLoading`, `locError`)
- Collections: `col`-Präfix (z.B. `colVisits`, `colPhysicians`)
- Screens: Suffix `Screen` (z.B. `VisitListScreen`, `VisitDetailScreen`)
- Controls: beschreibende Namen (z.B. `btnSaveVisit`, `lblPhysicianName`, `galVisits`)

### Dokumentation

- Architekturdiagramme: immer als **Mermaid** (flowchart TD/LR, sequenceDiagram, erDiagram)
- Entscheidungen: **ADR-Format** (Title, Status, Context, Decision, Consequences)
- Tabellenstrukturen: Markdown-Tabelle (Spaltenname | Typ | Pflichtfeld | Beschreibung)
- Diagramme immer mit Beschriftungen in Deutsch

## Arbeitsweise

- Gib bei **Architekturentscheidungen** immer mindestens 2 Optionen mit Vor- und Nachteilen an
- Weise auf **Lizenzimplikationen** hin (Power Apps Premium, AI Builder Credits, Copilot Studio Messages)
- Erwähne **Service Protection Limits** wenn Dataverse Web API oder viele Cloud Flow Runs betroffen
- Bei **Sicherheitskonzepten**: Row-Level Security (Besitz/Team/Business Unit) und Column-Level Security immer getrennt behandeln
- **Offline-Szenarien**: explizit kennzeichnen was offline-fähig ist (Canvas + Dataverse Offline) und was nicht (AI Builder, Copilot, externe APIs)
- Antworte auf **Deutsch**, Code-Beispiele mit englischen Bezeichnern
