# Übung: Als Solution Architect mit AI arbeiten

## Szenario

Du richtest deinen VS Code Workspace für das VisitTrack-Projekt ein. Ziel: Copilot kennt das Projekt, deine Konventionen und kann direkt produktiv unterstützen — ohne bei jedem Prompt den Kontext neu zu erklären.

---

## Aufgabe 1: copilot-instructions.md schreiben (20 Minuten)

Schreibe die Datei `.github/copilot-instructions.md` für den VisitTrack-Workspace.

Sie muss folgende Abschnitte enthalten:

- **Projektkontext**: Auftraggeber, Ziel, Rollen (ADM, Manager, SA, Admin)
- **Technologie-Stack**: Welche Power Platform Komponenten, welche Azure-Dienste
- **Konventionen**: Tabellenpräfix, Power Fx Variablennamen, Diagramm-Format
- **Arbeitsweise**: Was soll Copilot bei jeder Antwort beachten (Lizenzen, Limits, Sicherheit)

Teste danach in Copilot Chat ohne zusätzlichen Kontext:

```
"Schlage eine Tabellenstruktur für Arztbesuche vor"
```

Prüfe: Verwendet Copilot automatisch das Präfix `vt_` und Mermaid-Diagramme?

---

## Aufgabe 2: agents.md für SA-Rollen (20 Minuten)

Erstelle eine `agents.md` mit 3 spezialisierten Agent-Konfigurationen:

| Agent                 | Aufgabe                          | Kann                                      | Darf nicht                            |
| --------------------- | -------------------------------- | ----------------------------------------- | ------------------------------------- |
| `schema-agent`        | Dataverse Datenmodell generieren | Tabellen, Beziehungen, Sicherheitsrollen  | Direkt deployen                       |
| `requirement-analyst` | Fit/Gap-Analyse                  | User Stories, Risiken, Lizenzeinschätzung | Implementierungsdetails ohne Klarheit |
| `review-agent`        | Architektur und Code reviewen    | Kommentieren, Checkliste abarbeiten       | Dateien direkt ändern                 |

Definiere für jeden Agent:

- System-Anweisung (1-3 Sätze Fokus und Einschränkungen)
- 3 typische Prompts als Beispiele
- Erwartetes Output-Format

---

## Aufgabe 3: SA-Workflow mit AI durchlaufen (25 Minuten)

Führe einen vollständigen SA-Workflow mit Copilot durch:

**Schritt 1 — Anforderung klären:**

```
"Ich brauche einen Besuchsbericht für Manager.
 Anforderungen: filtern nach ADM, nach Zeitraum, nach Arzt.
 Welche Power Platform Komponente ist am besten geeignet?"
```

Notiere die Antwort. Stimme zu oder widersprich mit Begründung.

**Schritt 2 — Datenmodell generieren:**

```
"Erstelle das Dataverse-Schema für den Besuchsbericht.
 Tabellen: vt_visits, vt_physicians, vt_users.
 Format: Markdown-Tabelle mit Spaltenname, Typ, Pflichtfeld"
```

Prüfe: Sind alle Beziehungen korrekt? Fehlen Cascade-Regeln?

**Schritt 3 — Architekturentscheidung dokumentieren:**

```
"Schreibe einen Architecture Decision Record (ADR) für:
 Canvas App vs. Model-Driven App für den Besuchsbericht.
 Format: Title, Status, Context, Decision, Consequences"
```

Review den ADR. Was hat Copilot richtig erkannt? Was fehlt?

---

## Aufgabe 4: Prompt-Qualität verbessern (15 Minuten)

Die folgenden 3 Prompts sind schlecht formuliert. Verbessere sie:

**Prompt A (schlecht):**

```
"Erstelle eine Canvas App"
```

**Prompt B (schlecht):**

```
"Was ist besser, Flow oder Agent?"
```

**Prompt C (schlecht):**

```
"Hilf mir mit Sicherheit"
```

Jeder verbesserte Prompt muss enthalten: **Kontext, Entitäten, Constraints, Output-Format**.

Teste die verbesserten Prompts und vergleiche die Qualität der Antworten.

---

## Aufgabe 5: Prompts aus `.github/prompts/` ausprobieren (20 Minuten)

Die fertigen Prompt-Templates liegen in `.github/prompts/`. Führe mindestens 3 davon aus:

### `/generate-dataverse-schema`

Rufe den Prompt auf und übergib:

```
Tabelle: Arztbesuch (vt_visits)
Felder: Datum, Dauer in Minuten, Notizen, Status (Geplant/Durchgeführt/Abgesagt)
Beziehungen: gehört zu einem Arzt (vt_physicians), gehört zu einem ADM (systemuser)
```

Prüfe die Ausgabe: Sind Cascade-Regeln angegeben? Ist die Mermaid `erDiagram` korrekt?

### `/fit-gap-analysis`

Übergib diese Anforderungsliste:

```
1. ADM erfasst Besuche mobil, auch offline
2. Manager sieht Performance aller ADMs in seiner Region
3. SAP erhält täglich um 03:00 Uhr alle neuen Besuche
4. ADM kann Fragen zur Produktdokumentation stellen
5. Visitenkarte scannen → Arzt wird automatisch angelegt
```

Prüfe: Werden Lizenzkosten für Anforderung 4 (Copilot Studio) und 5 (AI Builder) erwähnt?

### `/generate-adr`

Übergib:

```
Canvas App vs. Model-Driven App für das ADM-Besuchserfassungsformular.
ADMs arbeiten auf Mobilgeräten, Offline-Anforderung, ca. 200 Nutzer.
```

Prüfe: Enthält der ADR einen "ALM Impact" Abschnitt?

### `/review-architecture`

Öffne `m03-datenmodellierung/0301-datenmodell-strategien/theorie.md` und starte den Prompt:

```
@workspace Bitte reviewe die Datenmodell-Entscheidungen in [Datei referenzieren]
```

Prüfe: Benutzt der Review-Agent die SA-Checkliste aus der instruction?

---

## Aufgabe 6: Eigene Instruction schreiben (15 Minuten)

Schreibe eine eigene `.instructions.md` für einen Bereich den du in deinem Projektalltag brauchst.

Vorlage:

```markdown
---
description: "Use when [konkrete Trigger-Situation]. Covers [was die Instruction regelt]."
---

# [Titel]

## [Abschnitt 1]

- Regel 1
- Regel 2

## [Abschnitt 2]

...
```

**Ideen für eigene Instructions:**

- `copilot-studio-topics.instructions.md` — Regeln für Copilot Studio Topic-Design
- `power-automate-flow.instructions.md` — Flow-Naming, Error-Handling, Retry-Patterns
- `security-roles.instructions.md` — Privilege-Level Konventionen, BU-Hierarchie-Regeln

Speichere die Datei unter `.github/instructions/` und teste sie mit einer relevanten Frage in Copilot Chat.
