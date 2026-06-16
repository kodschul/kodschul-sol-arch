# Hands-On M01 — Agentic: Discovery Automation (für Adrian)

> **Typ:** Agentic Discovery Tool bauen  
> **Dauer:** ca. 60 Minuten  
> **Lösung:** siehe `hands-on-agentic-sol.md`  
> **Werkzeuge:** Claude MCP, SharePoint MCP, Dataverse MCP

---

## Szenario: Discovery im Turbo-Modus

**Ausgangssituation:**
MedPharma GmbH kommt mit mündlichen Anforderungen. Ein PM beschreibt das Projekt in Freitext. Deine Aufgabe: Ein **agentic Discovery Tool** bauen, das:

1. Freitext-Anforderungen entgegennimmt
2. Automatisch strukturiert (Entities, Anforderungen, Risiken)
3. Fit-Gap-Analyse generiert
4. MoSCoW priorisiert
5. Alles nach Dataverse speichert

---

## Aufgabe 1: Agent Design

**Dauer:** 15 Minuten

Schreibe ein **System Prompt** für einen Discovery Assistant. Der Agent soll:

```
Agent-Aufgabe:
Du bist ein Solution Architect Assistant spezialisiert auf Power Platform.
Ein PM gibt dir Anforderungen im Freitext. Du extrahierst:

1. **Entities** (Personen, Objekte, Prozesse)
   Format: { name, description, relationships }

2. **Anforderungen** (Must/Should/Could/Won't Have)
   Format: { id, text, category, priority, complexity }

3. **Risiken**
   Format: { risk, impact, mitigation }

4. **Fit-Gap-Analyse**
   Format: { requirement_id, power_platform_fit, gap, workaround }

Output im JSON-Format, strukturiert und validierbar.
```

**Deliverable:** Ein `.md` oder `.txt` File mit deinem System Prompt.

---

## Aufgabe 2: MCP Tool Definition

**Dauer:** 20 Minuten

Definiere 3 MCP Tools, die dein Discovery Agent braucht:

```yaml
Tools:
  1. parse-requirements:
    Description: Analysiert Freitext, extrahiert Entities + Requirements
    Input:
      - raw_text: string (Anforderungen im Freitext)
    Output:
      - entities: []
      - requirements: []
      - risks: []

  2. fit-gap-analyzer:
    Description: Mapped Anforderungen auf Power Platform Komponenten
    Input:
      - requirement_id: string
      - requirement_text: string
    Output:
      - component: (Canvas|PowerBI|Flow|Dataverse|etc.)
      - fit_level: (Full|Partial|Gap)
      - reasoning: string

  3. dataverse-persister:
    Description: Speichert Analysis-Ergebnisse in Dataverse
    Input:
      - analysis_json: object
    Output:
      - created_records: int
      - dataverse_links: []
```

**Deliverable:** YAML-Datei mit 3 MCP Tools definiert.

---

## Aufgabe 3: Test-Input strukturieren

**Dauer:** 10 Minuten

Erstelle einen Test-Input für deinen Agent. Nutze die MedPharma-Anforderungen:

```
Input Text (so wie ein PM es erzählen würde):

"Wir haben 120 Außendienstmitarbeiter, die täglich zu Ärzte-Terminen fahren.
Momentan notieren sie Besuche in Excel. Das ist mühsam, weil:
  - Jeder hat seine eigene Excel-Datei
  - Manager sehen nicht automatisch, wer wo war
  - Wir können nicht ins SAP exportieren
  - Im Außendienst funktioniert Internet nicht zuverlässig

Wir brauchen:
  - Mobile App für Außendienstler (must)
  - Offline-Modus (must)
  - Manager-Dashboard mit Berichten (should)
  - SAP-Integration um 03:00 täglich (could)
  - Multi-language Support (won't haben, erst später)

Risiken:
  - Wenn Sync fehlschlägt, haben wir doppelte Daten
  - Datenschutz: Ärzte sind sensitive Daten
  - IT-Team hat wenig Power Platform Erfahrung"

Speichere das als `input-medpharma.txt`
```

**Deliverable:** `input-medpharma.txt` mit strukturiertem Test-Input.

---

## Aufgabe 4: Agent Execution (Simulation oder Real)

**Dauer:** 15 Minuten

**Option A (Simulation — wenn Tenant nicht verfügbar):**

Schreib den erwarteten Agent Output als JSON:

```json
{
  "entities": [
    {
      "name": "ADM",
      "type": "role",
      "description": "Außendienstmitarbeiter",
      "count": 120
    },
    { "name": "Arzt", "type": "object", "description": "Ärzte & Kliniken" },
    { "name": "Visit", "type": "process", "description": "Besuchsbericht" }
  ],
  "requirements": [
    {
      "id": "R001",
      "text": "Mobile App",
      "category": "Functional",
      "priority": "Must",
      "complexity": "High"
    },
    {
      "id": "R002",
      "text": "Offline-Modus",
      "category": "Technical",
      "priority": "Must",
      "complexity": "Very High"
    }
  ],
  "fit_gap": [
    {
      "req_id": "R001",
      "component": "Canvas App",
      "fit": "Full",
      "gap": null,
      "reasoning": "Canvas App native mobile support"
    },
    {
      "req_id": "R002",
      "component": "Dataverse Sync",
      "fit": "Partial",
      "gap": "Conflict Resolution",
      "reasoning": "Dataverse hat Offline, aber Sync-Konflikte müssen manuell gehandhabt werden"
    }
  ],
  "moscow": [
    { "req_id": "R001", "m_priority": "M" },
    { "req_id": "R002", "m_priority": "M" }
  ],
  "risks": [
    {
      "risk": "Sync-Konflikte bei Offline-Editing",
      "impact": "High",
      "mitigation": "Konflikt-Resolution Flow + User Training"
    }
  ]
}
```

**Option B (Real — wenn du einen Tenant hast):**

Nutze Claude API mit MCP:

```bash
# Pseudocode
agent = DiscoveryAgent(system_prompt)
result = agent.analyze("input-medpharma.txt", tools=[parse_requirements, fit_gap_analyzer, dataverse_persister])
print(result)
```

**Deliverable:** Entweder JSON Output (Simulation) oder Run-Screenshot (Real).

---

## Aufgabe 5: Impact Analysis

**Dauer:** 5 Minuten

Vergleich:

| Metrik          | Klassisch (manuell) | Agentic   | Ersparnis |
| --------------- | ------------------- | --------- | --------- |
| Time to Fit-Gap | 2-3 Stunden         | 5 Minuten | 96% ⬇️    |
| Mistakes        | 3-5 pro Projekt     | <1        | ?         |
| Consistency     | Variabel            | 100%      | ✓         |
| Scalability     | 1 Projekt/Tag       | 10+ / Tag | ?         |

**Aufgabe:** Fülle die `?` Felder mit deinen Schätzungen aus, begründe in 2-3 Sätzen.

---

## Checkpoint ✓

- [ ] System Prompt geschrieben & dokumentiert
- [ ] 3 MCP Tools definiert (YAML)
- [ ] Test-Input strukturiert (`input-medpharma.txt`)
- [ ] Agent Output generiert (JSON oder Screenshot)
- [ ] Impact Analysis ausgefüllt
- [ ] Erkannt, dass dieser Agent **Zeit spart, aber Quality nicht verbessert** — SA muss immer noch validieren!
