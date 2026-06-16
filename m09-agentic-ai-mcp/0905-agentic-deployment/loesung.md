# Lösung: Agentic Deployment Pipeline

## Aufgabe 1: agents.md Referenz

```markdown
# agents.md — VisitTrack AI System

## Agents

### visittrack-assistant

- **Modell:** claude-3-5-sonnet
- **Zweck:** Hilft ADMs bei Besuchserfassung und Arzt-Lookup
- **System Prompt:** >
  Du bist VisitTrack-Assistent für Außendienstmitarbeiter der MedPharma GmbH.
  Hilf beim Erfassen von Arztbesuchen, Suchen von Ärzten und Abfragen der eigenen Performance.
  Antworte auf Deutsch. Sei präzise und hilfreich.
  Wenn du dir unsicher bist, sag es statt zu raten.
- **MCP Servers:** visittrack-dataverse, visittrack-sharepoint
- **Tools erlaubt:** get_visits, get_physician, create_visit, search_documents
- **Tools verboten:** delete_record, bulk_export, admin_operations

### visittrack-architect

- **Modell:** claude-3-5-sonnet
- **Zweck:** Solution Architecture Assistent für SA-Team
- **System Prompt:** >
  Du bist Solution Architecture Assistant für Power Platform Projekte.
  Analysiere Anforderungen, erkenne Risiken und schlage Architekturen vor.
  Begründe jede Entscheidung. Warnt vor Delegation-Limits, Performance-Risiken und Sicherheitslücken.
- **MCP Servers:** visittrack-dataverse (Read-Only), architecture-kb
- **Tools erlaubt:** get_schema, analyze_requirements, get_best_practices
- **Tools verboten:** alle Write-Operationen

## MCP Servers

### visittrack-dataverse

- **Command:** `node ./mcp-servers/dataverse/dist/index.js`
- **Env:** DATAVERSE_URL, TENANT_ID, CLIENT_ID, CLIENT_SECRET

### visittrack-sharepoint

- **Command:** `node ./mcp-servers/sharepoint/dist/index.js`
- **Env:** SHAREPOINT_SITE_URL, SHAREPOINT_CLIENT_ID, SHAREPOINT_CLIENT_SECRET

### architecture-kb

- **Command:** `node ./mcp-servers/architecture-kb/dist/index.js`
- **Env:** KB_PATH=./knowledge-base
```

---

## Aufgabe 2: Unit Tests Referenz

```typescript
import { describe, it, expect } from "vitest";
import { callTool } from "../src/index"; // Export der Tool-Handler

describe("get_visits tool", () => {
  it("returns visits for known user", async () => {
    const result = await callTool("get_visits", { adm_user_id: "user-123" });
    expect(JSON.parse(result.content[0].text)).toHaveLength(3);
  });

  it("returns empty array for unknown user", async () => {
    const result = await callTool("get_visits", { adm_user_id: "unknown" });
    expect(JSON.parse(result.content[0].text)).toHaveLength(0);
  });

  it("respects date_from filter", async () => {
    const result = await callTool("get_visits", {
      adm_user_id: "user-123",
      date_from: "2024-06-13",
    });
    const visits = JSON.parse(result.content[0].text);
    expect(visits.every((v: any) => v.visit_date >= "2024-06-13")).toBe(true);
  });

  it("respects limit parameter", async () => {
    const result = await callTool("get_visits", {
      adm_user_id: "user-123",
      limit: 1,
    });
    expect(JSON.parse(result.content[0].text)).toHaveLength(1);
  });
});

describe("create_visit tool", () => {
  it("creates visit with valid data", async () => {
    const result = await callTool("create_visit", {
      physician_id: "phys-001",
      visit_date: "2024-06-10",
    });
    expect(result.isError).toBeFalsy();
    const data = JSON.parse(result.content[0].text);
    expect(data.status).toBe("created");
  });

  it("rejects invalid physician_id", async () => {
    const result = await callTool("create_visit", {
      physician_id: "bad",
      visit_date: "2024-06-10",
    });
    expect(result.isError).toBe(true);
  });

  it("rejects duration > 480", async () => {
    const result = await callTool("create_visit", {
      physician_id: "phys-001",
      visit_date: "2024-06-10",
      duration_minutes: 500,
    });
    expect(result.isError).toBe(true);
    expect(result.content[0].text).toContain("480");
  });
});
```

---

## Aufgabe 4: Rollback-Strategie

**1. Sofortmaßnahme bei fehlerhaftem Agent:**
Den Agent in Copilot Studio sofort deaktivieren (Publish → Unpublish). Das ist die schnellste Maßnahme innerhalb von Minuten. Parallel: Im Deployment-System die vorherige Version identifizieren.

**2. Power Platform Solution Rollback:**
Jede Solution-Version wird als `.zip` im CI/CD-System archiviert (GitHub Releases oder Azure Artifacts). Rollback: `pac solution import --path ./releases/v1.2.3/VisitTrack_managed.zip --environment prod`. Wichtig: Managed Solutions in Production verhindern versehentliche Änderungen — nur Import kann Rollback durchführen.

**3. Feature Flag für Agent-Versionen:**
Nutze eine Dataverse-Tabelle `vt_feature_flags` mit `{feature_name, enabled_for_users: [user_ids], enabled_percentage: 50}`. Der Agent prüft beim Start ob der Nutzer zur neuen Version-Gruppe gehört. Für 50/50-Split: `Mod(user_id_hash, 100) < 50 → neue Version`. Copilot Studio unterstützt kein natives Feature-Flagging — muss via MCP Tool implementiert werden.

---

## Aufgabe 5: Monitoring KPIs

| KPI                                         | Threshold Alert          | Tool                     |
| ------------------------------------------- | ------------------------ | ------------------------ |
| Error Rate (Agent Fehler / Gesamt)          | > 5% in 15 min           | Application Insights     |
| P95 Response Time                           | > 10 Sekunden            | Application Insights     |
| Token-Verbrauch pro Tag                     | > 120% des Tages-Budgets | Azure Cost Alerts        |
| Tool-Aufruf-Fehlerrate pro MCP              | > 10% in 30 min          | Custom Log Analytics     |
| Nutzer-Abbruchrate (Conversation Abandoned) | > 30%                    | Copilot Studio Analytics |
