# Übung: MCP Server entwickeln

## Szenario

VisitTrack bekommt einen KI-Assistenten. Du baust den MCP Server, der dem Assistenten Zugriff auf Dataverse gibt. Der Server soll sicher, erweiterbar und deploybar sein.

---

## Aufgabe 1: MCP Server aufsetzen (20 Minuten)

Initialisiere ein neues MCP-Server-Projekt für VisitTrack:

```bash
mkdir visittrack-mcp && cd visittrack-mcp
npm init -y
npm install @modelcontextprotocol/sdk zod
npm install -D typescript @types/node tsx
```

Erstelle `src/index.ts` mit dem Grundgerüst:

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "visittrack-mcp",
  version: "1.0.0",
});

// Hier kommen deine Tools

const transport = new StdioServerTransport();
await server.connect(transport);
```

Füge ein `package.json` Script hinzu:

```json
"scripts": {
  "dev": "tsx src/index.ts",
  "build": "tsc"
}
```

Starte den Server und prüfe, dass er ohne Fehler läuft.

---

## Aufgabe 2: Drei Tools implementieren (25 Minuten)

Implementiere diese drei Tools. Nutze Mock-Daten statt echter Dataverse-Aufrufe:

**Tool 1: `get_visits`**

- Input: `adm_user_id` (string), `date_from` (date, optional), `limit` (number, default 10)
- Output: Liste von Visit-Objekten `{id, visit_date, physician_name, duration_minutes, status}`
- Mock: Gib 3 hartcodierte Testdatensätze zurück wenn `adm_user_id` `"user-123"` ist

**Tool 2: `get_physician`**

- Input: `name` (string, partial match erlaubt)
- Output: `{id, name, specialty, address}` oder `null`
- Mock: Gib einen Arzt zurück wenn Name "Mueller" enthält

**Tool 3: `create_visit`**

- Input: `physician_id` (string), `visit_date` (string), `duration_minutes` (number, optional)
- Output: `{visit_id, status: "created"}` oder Error
- Validation: Lehne ab wenn `physician_id` kein valides Format hat (min. 5 Zeichen)

---

## Aufgabe 3: VS Code MCP Integration (15 Minuten)

Konfiguriere den Server in VS Code:

Erstelle `.vscode/mcp.json`:

```json
{
  "servers": {
    "visittrack": {
      "command": "npx",
      "args": ["tsx", "${workspaceFolder}/visittrack-mcp/src/index.ts"]
    }
  }
}
```

Teste im VS Code Chat-Panel:

- Öffne Copilot Chat
- Schreibe: `@visittrack Zeige Besuche für user-123`
- Prüfe ob das `get_visits` Tool aufgerufen wird

---

## Aufgabe 4: Resource hinzufügen (15 Minuten)

Füge eine Resource hinzu, die das VisitTrack Datenschema beschreibt:

```typescript
server.resource(
  "visittrack-schema",
  "dataverse://visittrack/schema",
  async (uri) => ({
    contents: [
      {
        uri: uri.href,
        mimeType: "application/json",
        text: JSON.stringify({
          /* Dein Schema hier */
        }),
      },
    ],
  })
);
```

Das Schema soll die drei Tabellen aus M03 (vt_visit, vt_physician, vt_adm) mit ihren Feldern und Beziehungen beschreiben.

Teste: Agent soll die Schema-Resource lesen und daraus erklären können "Wie ist VisitTrack aufgebaut?"

---

## Aufgabe 5: Fehlerhandling (10 Minuten)

Erweitere `create_visit` um folgendes Fehlerhandling:

1. Wenn `physician_id` nicht existiert (simuliere: alle IDs außer `"phys-001"` und `"phys-002"` sind ungültig) → Fehlermeldung die der Nutzer versteht
2. Wenn `visit_date` in der Zukunft liegt → Warnung ausgeben (aber trotzdem erstellen)
3. Wenn `duration_minutes` > 480 → Ablehnen mit Begründung

```typescript
return {
  isError: true,
  content: [{ type: "text", text: "Fehler: ..." }],
};
```

---

## Abgabe

- `visittrack-mcp/src/index.ts` mit allen 3 Tools + Resource
- Screenshot oder Terminal-Output der zeigt, dass VS Code das Tool aufruft
- Kurze Notiz (3-5 Sätze): Was würdest du ändern wenn du echte Dataverse-Calls implementierst?
