# Lösung: Als Solution Architect mit AI arbeiten

## Aufgabe 1: copilot-instructions.md

Fertige Referenzdatei: `copilot-instructions.md` in diesem Ordner.

**Prüfkriterium**: Copilot verwendet automatisch `vt_`-Präfix und Mermaid-Diagramme ohne explizite Aufforderung.

**Minimale Checkliste für die Datei:**

- [ ] Projektname und Auftraggeber genannt
- [ ] Stack vollständig (Canvas, MDA, Dataverse, Automate, Copilot Studio)
- [ ] Tabellenpräfix `vt_` und Publisher `medpharma` definiert
- [ ] Power Fx Naming-Konventionen (gbl/loc/col)
- [ ] Diagrammformat: Mermaid
- [ ] Lizenz-Hinweis-Regel
- [ ] Sicherheits-Hinweis-Regel

---

## Aufgabe 2: agents.md

Fertige Referenzdatei: `agents.md` in diesem Ordner.

Enthält 4 konfigurierte Agents:

- `schema-agent` — Dataverse Datenmodell
- `requirement-analyst` — Fit/Gap und User Stories
- `review-agent` — Architecture Review mit Checkliste
- `pac-cli-agent` — Deployment-Skripte

---

## Aufgabe 3: ADR Referenz

```markdown
# ADR-001: Canvas App vs. Model-Driven App für Besuchsbericht

## Status

Entschieden (2026-06-16)

## Kontext

Manager benötigen einen Besuchsbericht mit Filtermöglichkeiten nach ADM, Zeitraum und Arzt.
Ca. 50 Manager, monatliche Nutzung, keine Offline-Anforderung.

## Entscheidung

Canvas App — wegen:

- Custom Layout für Dashboard-Ansicht
- Kombination von Filtern flexibler als Model-Driven Views
- Performance bei aggregierten Berechnungen (Summarize, Average)

## Konsequenzen

- ✓ Flexibles Layout und Navigation
- ✓ Einfache Integration von Power BI Embedded
- ✗ Höherer Pflegeaufwand als Model-Driven
- ✗ Keine automatischen Ansichten bei Schema-Änderungen

## Abgewogene Alternativen

- Model-Driven App: Weniger Aufwand, aber eingeschränktes Layout
- Power BI: Optimal für Read-Only Reports, aber keine Drilldown-Aktionen
```

---

## Aufgabe 4: Verbesserte Prompts

**Prompt A verbessert:**

```
"Erstelle eine Canvas App für VisitTrack.
 Nutzergruppe: Außendienstmitarbeiter (ADM), ca. 200 Personen, Mobilgeräte.
 Kernaufgaben: Besuch erfassen, Arzt suchen, eigene Besuche ansehen.
 Datenquelle: Dataverse, Tabellen vt_visits und vt_physicians.
 Anforderungen: Offline-fähig, Touch-optimiert.
 Output: Screen-Liste mit Navigationsfluss als Mermaid-Diagramm."
```

**Prompt B verbessert:**

```
"Vergleiche Power Automate Flow vs. Copilot Studio Agent für:
 Ein ADM schickt eine Textnachricht mit dem Inhalt eines Arztbesuchs.
 Das System soll daraus einen strukturierten Datensatz in Dataverse anlegen.
 Bewertungskriterien: Komplexität, Kosten, Wartbarkeit, Fehlerhandling.
 Output: Entscheidungsmatrix + Empfehlung mit Begründung."
```

**Prompt C verbessert:**

```
"Entwirf ein Sicherheitskonzept für VisitTrack in Dataverse.
 Rollen: ADM (sieht nur eigene Besuche), Manager (sieht Team-Besuche), Admin (alles).
 Tabellen: vt_visits (Besitz-basiert), vt_physicians (lesend für alle).
 Anforderungen: Keine Cross-Team-Sichtbarkeit zwischen ADMs.
 Output: Privilege-Matrix (Tabelle) + empfohlene Row-Level Security-Methode."
```

---

## Aufgabe 5: Prüfkriterien für Prompt-Outputs

### `/generate-dataverse-schema` — Mindesterwartungen

- Tabelle enthält `vt_visit_date` (Date Only), `vt_duration` (Whole Number), `vt_notes` (Multiline Text), `vt_status` (Choice: Geplant/Durchgeführt/Abgesagt)
- Beziehung zu `vt_physicians`: `vt_physician_id` Lookup, Cascade-Regel `RemoveLink` oder `Restrict` mit Begründung
- Mermaid `erDiagram` zeigt `vt_visits ||--o{ vt_physicians : "belongs to"`
- RLS-Empfehlung: Owner-based (ADM besitzt eigene Besuche)

### `/fit-gap-analysis` — Mindesterwartungen

| Anforderung            | Erwarteter Fit Level                                                                                    |
| ---------------------- | ------------------------------------------------------------------------------------------------------- |
| ADM offline erfassen   | Low-Code (Canvas + Dataverse Offline)                                                                   |
| Manager Performance    | Low-Code (Power BI embedded oder Model-Driven App)                                                      |
| SAP Export 03:00       | Low-Code (Scheduled Flow + Custom Connector)                                                            |
| Fragen zur Produktdoku | Low-Code (Copilot Studio + Knowledge Source) — **AI Builder Credits / Copilot Studio Messages** erwähnt |
| Visitenkarte scannen   | Low-Code (AI Builder Form Recognizer) — **AI Builder Credits** erwähnt                                  |

### `/generate-adr` — Pflichtabschnitte

- `## Status` — "Proposed"
- `## Context` — Offline-Anforderung und mobile Nutzung erwähnt
- `## Decision` — Canvas App mit Begründung (Offline, Custom Layout)
- `## ALM Impact` — "Canvas Apps sind Solution-aware, können als Teil der VisitTrack-Solution exportiert/importiert werden"
- `## Alternatives Considered` — mindestens Model-Driven App und PWA als verworfene Optionen

---

## Aufgabe 6: Referenz-Instruction

Beispiel für `copilot-studio-topics.instructions.md`:

```markdown
---
description:
  "Use when designing, reviewing, or creating Copilot Studio Topics, triggers,
  entities, or agent system prompts for Power Platform conversational AI."
---

# Copilot Studio Topic Guidelines — VisitTrack

## Topic Naming

- Beschreibende Namen: "Besuche abfragen", "Arzt suchen", nicht "Topic 1"
- Trigger-Phrasen: min. 5 Varianten, verschiedene Formulierungen

## Topic Struktur

1. Trigger (Nutzer-Intent erkenn)
2. Entitäten extrahieren (falls nötig)
3. Cloud Flow oder MCP Tool aufrufen
4. Antwort mit Adaptive Card oder Text

## System Prompt Regeln

- Sprache auf Deutsch festlegen
- Halluzinationen verhindern: "Antworte nur basierend auf deiner Knowledge Base"
- Quellenangaben erzwingen: "(Quelle: [Dokument])"
- Out-of-Scope klar definieren

## Sicherheit

- Kein direkter Datenbankzugriff im Topic — immer über Cloud Flow
- Authentifizierung: Azure AD SSO konfigurieren bevor Live-Schaltung
```
