# Lösung: MCP Server entwickeln

## Aufgabe 2: Referenzimplementierung

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "visittrack-mcp",
  version: "1.0.0",
});

// Mock-Daten
const mockVisits = [
  {
    id: "v-001",
    visit_date: "2024-06-10",
    physician_name: "Dr. Mueller",
    duration_minutes: 30,
    status: "Completed",
  },
  {
    id: "v-002",
    visit_date: "2024-06-12",
    physician_name: "Dr. Schmidt",
    duration_minutes: 45,
    status: "Completed",
  },
  {
    id: "v-003",
    visit_date: "2024-06-14",
    physician_name: "Dr. Mueller",
    duration_minutes: 20,
    status: "Completed",
  },
];

const mockPhysicians = [
  {
    id: "phys-001",
    name: "Dr. Mueller",
    specialty: "Allgemeinmedizin",
    address: "Hauptstr. 1, München",
  },
  {
    id: "phys-002",
    name: "Dr. Schmidt",
    specialty: "Chirurgie",
    address: "Bahnhofstr. 5, Berlin",
  },
];

// Tool 1: get_visits
server.tool(
  "get_visits",
  "Gibt Besuchsdatensätze für einen ADM zurück",
  {
    adm_user_id: z.string().describe("User ID des ADM"),
    date_from: z.string().optional().describe("Startdatum ISO (YYYY-MM-DD)"),
    limit: z.number().default(10).describe("Max. Anzahl Ergebnisse"),
  },
  async ({ adm_user_id, date_from, limit }) => {
    if (adm_user_id !== "user-123") {
      return { content: [{ type: "text", text: "[]" }] };
    }
    let results = mockVisits;
    if (date_from) {
      results = results.filter((v) => v.visit_date >= date_from);
    }
    return {
      content: [
        { type: "text", text: JSON.stringify(results.slice(0, limit)) },
      ],
    };
  }
);

// Tool 2: get_physician
server.tool(
  "get_physician",
  "Sucht einen Arzt anhand des Namens (partial match)",
  {
    name: z.string().describe("Teilweise oder vollständiger Arztname"),
  },
  async ({ name }) => {
    const found = mockPhysicians.find((p) =>
      p.name.toLowerCase().includes(name.toLowerCase())
    );
    return {
      content: [{ type: "text", text: found ? JSON.stringify(found) : "null" }],
    };
  }
);

// Tool 3: create_visit
server.tool(
  "create_visit",
  "Erstellt einen neuen Besuchsdatensatz",
  {
    physician_id: z.string().describe("ID des Arztes aus get_physician"),
    visit_date: z.string().describe("Besuchsdatum (YYYY-MM-DD)"),
    duration_minutes: z.number().optional().describe("Dauer in Minuten"),
  },
  async ({ physician_id, visit_date, duration_minutes }) => {
    // Validation: physician_id Format
    if (physician_id.length < 5) {
      return {
        isError: true,
        content: [{ type: "text", text: "Ungültige physician_id: zu kurz." }],
      };
    }

    // Validation: physician existiert
    const physician = mockPhysicians.find((p) => p.id === physician_id);
    if (!physician) {
      return {
        isError: true,
        content: [
          {
            type: "text",
            text: `Arzt mit ID '${physician_id}' nicht gefunden.`,
          },
        ],
      };
    }

    // Validation: Datum in Zukunft
    const today = new Date().toISOString().split("T")[0];
    let warning = "";
    if (visit_date > today) {
      warning = " (Hinweis: Datum liegt in der Zukunft)";
    }

    // Validation: Dauer
    if (duration_minutes !== undefined && duration_minutes > 480) {
      return {
        isError: true,
        content: [
          {
            type: "text",
            text: "Dauer darf 480 Minuten (8 Stunden) nicht überschreiten.",
          },
        ],
      };
    }

    const newId = `v-${Date.now()}`;
    return {
      content: [
        {
          type: "text",
          text:
            JSON.stringify({ visit_id: newId, status: "created" }) + warning,
        },
      ],
    };
  }
);

// Resource: Schema
server.resource(
  "visittrack-schema",
  "dataverse://visittrack/schema",
  async (uri) => ({
    contents: [
      {
        uri: uri.href,
        mimeType: "application/json",
        text: JSON.stringify(
          {
            tables: [
              {
                name: "vt_visit",
                fields: [
                  "visit_date",
                  "duration_minutes",
                  "status",
                  "physician_id",
                  "adm_user_id",
                ],
              },
              {
                name: "vt_physician",
                fields: ["name", "specialty", "address"],
              },
              { name: "vt_adm", fields: ["user_id", "region", "phone"] },
            ],
            relationships: [
              {
                from: "vt_visit",
                to: "vt_physician",
                type: "N:1",
                fk: "physician_id",
              },
            ],
          },
          null,
          2
        ),
      },
    ],
  })
);

const transport = new StdioServerTransport();
await server.connect(transport);
```

## Aufgabe 5: Notizen für echte Dataverse-Calls

Für echte Dataverse-Calls würde ich folgendes ändern:

- MSAL-Bibliothek (`@azure/msal-node`) für OAuth-Token-Handling — Token cachen (15 min TTL)
- Alle Fetch-Calls mit `try/catch` wrappen und HTTP-Status-Codes prüfen (401 = Token refresh, 429 = Rate Limit, 500 = Error)
- Pagination implementieren (`@odata.nextLink`) da Dataverse maximal 5000 Records pro Request zurückgibt
- Environment-Variablen für `DATAVERSE_URL`, `TENANT_ID`, `CLIENT_ID`, `CLIENT_SECRET` — nie im Code hardcodieren
