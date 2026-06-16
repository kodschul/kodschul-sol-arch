# Hands-on M09 — Lösung

> Referenzlösungen für alle Phasen des Hands-on.

---

## Phase 1: MCP Server — get_performance_summary

```typescript
server.tool(
  "get_performance_summary",
  "Gibt eine Performance-Zusammenfassung für einen ADM zurück",
  {
    adm_user_id: z.string(),
    period_days: z.number().default(30),
  },
  async ({ adm_user_id, period_days }) => {
    if (adm_user_id !== "user-123") {
      return {
        content: [
          {
            type: "text",
            text: JSON.stringify({
              total_visits: 0,
              avg_duration: 0,
              top_physicians: [],
            }),
          },
        ],
      };
    }

    const visits = mockVisits; // aus Phase 1

    const summary = {
      total_visits: visits.length,
      avg_duration: Math.round(
        visits.reduce((sum, v) => sum + v.duration_minutes, 0) / visits.length
      ),
      top_physicians: [...new Set(visits.map((v) => v.physician_name))].slice(
        0,
        3
      ),
      period_days,
    };

    return { content: [{ type: "text", text: JSON.stringify(summary) }] };
  }
);
```

---

## Phase 2: RAG MCP Server — Keyword Search

```typescript
// mcp-servers/rag/src/index.ts
import { readFileSync, readdirSync } from "fs";
import { join } from "path";

const KB_PATH = process.env.KB_PATH ?? "./knowledge-base";

function keywordSearch(query: string, category: string): object[] {
  const files = readdirSync(KB_PATH).filter((f) => {
    if (category === "all") return f.endsWith(".md");
    return f.startsWith(category) && f.endsWith(".md");
  });

  const results: object[] = [];
  const queryLower = query.toLowerCase();

  for (const file of files) {
    const content = readFileSync(join(KB_PATH, file), "utf-8");
    const lines = content.split("\n");

    for (let i = 0; i < lines.length; i++) {
      if (lines[i].toLowerCase().includes(queryLower)) {
        // Kontext: 2 Zeilen vor und nach dem Treffer
        const start = Math.max(0, i - 2);
        const end = Math.min(lines.length - 1, i + 2);
        results.push({
          source: file.replace(".md", ""),
          line: i + 1,
          text: lines.slice(start, end + 1).join("\n"),
          relevance: "keyword-match",
        });
      }
    }
  }

  return results.slice(0, 5); // Max 5 Ergebnisse
}

server.tool(
  "search_knowledge",
  "Sucht in der VisitTrack Wissensdatenbank",
  {
    query: z.string(),
    category: z
      .enum(["products", "compliance", "process", "all"])
      .default("all"),
  },
  async ({ query, category }) => {
    const results = keywordSearch(query, category);

    if (results.length === 0) {
      return {
        content: [
          {
            type: "text",
            text: JSON.stringify({
              results: [],
              message:
                "Keine Informationen zu dieser Anfrage in der Wissensdatenbank gefunden.",
            }),
          },
        ],
      };
    }

    return { content: [{ type: "text", text: JSON.stringify({ results }) }] };
  }
);
```

---

## Phase 2: System Prompt Referenz

```
Du bist VisitTrack KI-Assistent für Außendienstmitarbeiter der MedPharma GmbH.

WICHTIG — Wissensfragen:
Beantworte Fragen zu Produkten, Compliance und Prozessen NUR basierend auf
deiner Wissensdatenbank (search_knowledge Tool).

Wenn die Antwort nicht in der Datenbank steht:
  "Dazu habe ich keine Information in meiner Wissensdatenbank.
   Wende dich bitte an deinen Vorgesetzten oder die zentrale HR."

Gib immer die Quelle an: (Quelle: [Dateiname], Zeile [X])

WICHTIG — Besuchsdaten:
Gib Besuchsdaten immer strukturiert aus (Datum, Arzt, Dauer).
Erstelle Besuche nur wenn der Nutzer es explizit bestätigt hat.

Sprache: Deutsch. Freundlich, präzise, kein Fachjargon.
Wenn du unsicher bist: Frag nach statt zu raten.
```

---

## Phase 5: Monitoring KPIs Referenz

| KPI                                | Threshold                 | Tool                     |
| ---------------------------------- | ------------------------- | ------------------------ |
| Agent Error Rate                   | Alert wenn > 5% in 15 min | Application Insights     |
| P95 Response Time                  | Alert wenn > 10s          | Application Insights     |
| Daily Token Cost                   | Alert wenn > 120% Budget  | Azure Cost Management    |
| search_knowledge Null-Results Rate | Alert wenn > 40%          | Custom Log Analytics     |
| Conversation Completion Rate       | Alert wenn < 60%          | Copilot Studio Analytics |
