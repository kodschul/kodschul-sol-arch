# Hands-On M02 — Agentic: Architecture Code Generation

> **Typ:** Architektur-Auto-Generator bauen  
> **Dauer:** ca. 60 Minuten  
> **Lösung:** siehe `hands-on-agentic-sol.md`  
> **Werkzeuge:** Claude MCP, Mermaid, Azure CLI MCP

---

## Szenario: Von Requirements zum Architecture Canvas in 10 Minuten

**Ausgangssituation:**
Du hast die strukturierten Requirements aus M01. Jetzt brauchst du schnell eine Architektur.

Klassisch: SA sitzt hin, überlegt 2 Stunden, zeichnet in Visio.

Agentic: Agent generiert Architektur → SA validiert in 10 Minuten.

---

## Aufgabe 1: Architecture Schema definieren

**Dauer:** 15 Minuten

Definiere das Schema, das dein Architecture Generator ausgeben soll:

```yaml
Architecture:
  metadata:
    project_name: string
    risk_level: enum (LOW|MEDIUM|HIGH)
    team_size: enum (SMALL|MEDIUM|LARGE)

  components:
    frontend: [] # Canvas, Model-Driven, Portal
    backend: [] # Power Automate, Azure Functions
    data: [] # Dataverse, SharePoint, SQL
    analytics: [] # Power BI, Analytics
    integration: [] # Connectors, APIs, ESB

  environments:
    - name: string
      type: enum (Dev|Test|Prod|Staging)
      dataverse: bool
      users: string # "Admin only" oder "Team" etc.

  security:
    - component: string
      strategy: string # "Business Unit", "Field-Level", "Row-Level"

  risks:
    - risk: string
      mitigation: string
      probability: enum (LOW|MEDIUM|HIGH)

  deployment:
    approach: string # "Manual", "Automated", "Pipeline"
    frequency: string # "Weekly", "On-Demand", "Continuous"
```

**Deliverable:** YAML Schema Datei.

---

## Aufgabe 2: Agent Prompt schreiben

**Dauer:** 15 Minuten

Schreib einen Prompt für einen Architecture Designer Agent:

```
Du bist ein Architektur-Experte für Power Platform und Azure.

Input:
  - Anforderungen (aus M01 Discovery)
  - Team-Größe, Risiko-Level, Deployment-Frequenz
  - Sicherheitsanforderungen

Aufgaben:
1. Mapppe jede Anforderung auf Power Platform Komponenten
2. Erkenne Risiken (Delegation-Limit, Performance, Sicherheit)
3. Schlage Umgebungsstrategie vor (Dev/Test/Prod oder mehr)
4. Definiere Sicherheitsmodell (Business Unit / Field-Level)
5. Generiere Architektur im YAML-Schema (siehe Schema)

Output:
- Vollständige Architecture YAML
- Risks mit Mitigationen
- Begründungen pro Entscheidung
```

**Deliverable:** Prompt Datei.

---

## Aufgabe 3: Risk Detection

**Dauer:** 15 Minuten

Definiere 10 Auto-Detection Rules, die dein Agent nutzt:

```
Rule 1: Large Dataset + Canvas Gallery
  Condition: data_volume > 10k AND component == "Canvas Gallery"
  Risk: "Delegation Limit exceeded"
  Mitigation: "Use Power BI or Power Automate + caching"

Rule 2: Offline Requirement + Model-Driven App
  Condition: offline_required == true AND component == "Model-Driven"
  Risk: "Offline support is less tested for MDA"
  Mitigation: "Use Canvas App with offline-capable data"

Rule 3: Daily SAP Export + Scheduled Flow
  Condition: integration == "SAP" AND frequency == "Daily"
  Risk: "Flow timeout if data > 1GB or API limit hit"
  Mitigation: "Use Service Bus Topic for async processing"

[... 7 more rules]
```

**Aufgabe:** Schreib 10 solcher Rules. (Mind. 10, max. 15.)

**Deliverable:** Rules als YAML oder JSON.

---

## Aufgabe 4: Environment Strategy Generator

**Dauer:** 10 Minuten

Definiere einen Decision Tree, wann 3-Stufen vs 4-Stufen Umgebung sinnvoll ist:

```mermaid
flowchart TD
    A[Start: Wähle Umgebungsstrategie] --> B{Team-Größe?}
    B -->|Small (1-3)| C[3-Stufen: Dev/Test/Prod]
    B -->|Medium (4-10)| D{Deployment-Frequenz?}
    B -->|Large (>10)| E[4-Stufen: Dev/Test/Staging/Prod]

    D -->|Weekly or less| C
    D -->|Multiple times/week| F[4-Stufen empfohlen]

    C --> G[Minimize Overhead]
    E --> H[Isolate Changes]
    F --> H
```

**Aufgabe:**

- Zeichne einen Decision Tree (Mermaid) für Umgebungsstrategie
- Oder schreib einen if-else Tree als Pseudocode

**Deliverable:** Mermaid Diagram oder Code.

---

## Aufgabe 5: Auto-Generated Architecture

**Dauer:** 5 Minuten

**Input (von M01 Discovery):**

```json
{
  "project_name": "VisitTrack",
  "team_size": "MEDIUM",
  "risk_level": "MEDIUM",
  "requirements": [
    {
      "id": "R001",
      "text": "Mobile App",
      "category": "Functional",
      "priority": "Must"
    },
    { "id": "R002", "text": "Offline-Modus", "priority": "Must" },
    { "id": "R003", "text": "Manager Dashboard", "priority": "Should" },
    { "id": "R004", "text": "SAP Export Daily 03:00", "priority": "Must" }
  ]
}
```

**Aufgabe:**

Option A (Simulation):
Schreib die erwartete Agent-Output Architektur:

```yaml
Architecture:
  project_name: "VisitTrack"
  components:
    frontend:
      - type: "Canvas App"
        purpose: "Mobile Visit Capture"
        offline: true
    backend:
      - type: "Power Automate"
        trigger: "Daily 03:00"
        action: "Export to SAP"
    analytics:
      - type: "Power BI"
        dataset: "Visit Aggregations"
  environments:
    - name: "Dev"
      users: "Maker only"
    - name: "Test"
      users: "QA + Stakeholder"
    - name: "Prod"
      users: "End-Users"
  risks:
    - risk: "Offline Sync Conflicts"
      mitigation: "Implement Last-Write-Wins Strategy"
```

Option B (Real):
Führe einen echten Agent aus (falls Tenant verfügbar).

**Deliverable:** YAML Architektur Output.

---

## Aufgabe 6: Deployment Pipeline Code

**Dauer:** 10 Minuten

Generiere ein Pipeline-Manifest für deine Architektur:

```yaml
# pac-pipeline.yaml (Power Apps CLI)
apiVersion: 1
metadata:
  name: VisitTrack-Pipeline
  environments:
    - name: dev
      app_id: canvas-app-dev-uuid
      tables: [vt_arzt, vt_klinik, vt_besuch]
    - name: test
      app_id: canvas-app-test-uuid
    - name: prod
      app_id: canvas-app-prod-uuid

stages:
  - name: Deploy to Test
    trigger: pull-request
    actions:
      - build-solution
      - unit-tests
      - deploy-to-test

  - name: Deploy to Prod
    trigger: manual
    actions:
      - build-solution
      - smoke-tests
      - deploy-to-prod
```

**Aufgabe:**
Erstelle ein Pipeline-Manifest basierend auf deiner Architektur.

**Deliverable:** YAML Pipeline File.

---

## Checkpoint ✓

- [ ] Architecture Schema definiert (YAML)
- [ ] Architecture Designer Prompt geschrieben
- [ ] 10+ Risk Detection Rules definiert
- [ ] Environment Strategy Decision Tree (Mermaid oder Code)
- [ ] Auto-generated Architecture (Simulation oder Real)
- [ ] Deployment Pipeline Manifest erstellt
- [ ] Erkannt: **Architektur ist schneller, aber brauchst immer noch SA-Validation!**
