# Übung: Agentic Deployment Pipeline aufbauen

## Szenario

Du hast in M09 einen MCP Server und einen Agent gebaut. Jetzt deployst du das System automatisiert — von `git push` bis Production, mit Tests und Rollback-Fähigkeit.

---

## Aufgabe 1: agents.md erstellen (15 Minuten)

Erstelle eine `agents.md` Datei für das VisitTrack Projekt mit:

- Mindestens 2 Agents (VisitTrack Assistant + ein zweiter)
- Für jeden Agent: Modell, System Prompt (Kurzform), erlaubte Tools, verbotene Tools
- MCP Server Konfiguration für beide Agents
- Umgebungsvariablen als Referenzen (keine echten Werte)

---

## Aufgabe 2: Unit Tests für MCP Server (20 Minuten)

Schreibe Unit Tests für deinen MCP Server aus Aufgabe 0902:

```typescript
// tests/visittrack-mcp.test.ts
import { describe, it, expect } from "vitest";

describe("get_visits tool", () => {
  it("returns visits for known user", async () => { ... });
  it("returns empty array for unknown user", async () => { ... });
  it("respects date_from filter", async () => { ... });
  it("respects limit parameter", async () => { ... });
});

describe("create_visit tool", () => {
  it("creates visit with valid data", async () => { ... });
  it("rejects invalid physician_id", async () => { ... });
  it("rejects duration > 480", async () => { ... });
  it("warns but allows future date", async () => { ... });
});
```

Implementiere mindestens 6 Tests. Führe sie mit `npx vitest` aus.

---

## Aufgabe 3: GitHub Actions Workflow (20 Minuten)

Erstelle `.github/workflows/deploy.yml` mit mindestens:

- Trigger: Push auf `main`, nur wenn Dateien in `mcp-servers/` oder `agents.md` geändert wurden
- Job 1: Unit Tests
- Job 2: Deploy to Test (nach bestandenen Tests) — nutze PAC CLI als Pseudocode falls kein echter Tenant
- Job 3: Deploy to Production — mit `environment: production` (Manual Approval)

Kommentiere jede Section kurz was sie tut.

---

## Aufgabe 4: Rollback-Strategie (15 Minuten)

Beantworte folgende Fragen schriftlich (je 3-5 Sätze):

1. Was tust du wenn ein fehlerhafter Agent in Production deployed wurde und Nutzer falsche Antworten bekommt?
2. Wie rollst du eine Power Platform Solution zurück?
3. Wie würdest du einen Feature-Flag für einen neuen Agent implementieren (50% der Nutzer bekommen neue Version, 50% alte)?

---

## Aufgabe 5: Monitoring Dashboard Design (10 Minuten)

Skizziere ein Monitoring-Dashboard für den VisitTrack Agent:

- Welche 5 KPIs misst du?
- Welches Alerting-Threshold setzt du für jeden KPI?
- Welches Tool nutzt du (Power BI, Azure Monitor, Application Insights)?

---

## Abgabe

- `agents.md` für VisitTrack
- `tests/visittrack-mcp.test.ts` mit 6+ Tests + Screenshot vom Test-Run
- `.github/workflows/deploy.yml`
- Rollback-Strategie Dokument
- Monitoring Dashboard Skizze
