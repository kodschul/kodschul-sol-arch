# Hands-on-Lösung M02 — Plattformarchitektur

> Schritt-für-Schritt-Anleitung zur `hands-on.md`

---

## Lösung Aufgabe 1 — Umgebung anlegen

**Wo:** [admin.powerplatform.microsoft.com](https://admin.powerplatform.microsoft.com)

1. Linke Navigation → **Environments** → **+ New** (oben rechts)
2. Rechtes Panel öffnet sich — fülle aus:
   - **Name:** `VisitTrack-Dev`
   - **Type:** `Sandbox` ← wichtig, nicht Default!
   - **Region:** `Europe (Germany)` oder `Europe`
   - **Purpose:** (optional) "VisitTrack Entwicklungsumgebung"
   - **Add a Dataverse data store:** Toggle auf **Yes**
   - **Language:** `German`
   - **Currency:** `EUR`
3. Klicke **Save**
4. Warte ca. 2–3 Minuten — Status wechselt von „Provisioning" zu „Ready"

**Warum Sandbox statt Default?**
Die Default-Umgebung steht automatisch jedem Nutzer im Tenant offen. Eine Sandbox kann auf bestimmte Nutzer beschränkt werden — das ist die erste Sicherheitsgrenze.

---

## Lösung Aufgabe 2 — Komponenten zuordnen

**Wo:** Kein Tool nötig — konzeptionelle Entscheidung

| Anforderung                                | Lösung                                                     | Begründung                                                       |
| ------------------------------------------ | ---------------------------------------------------------- | ---------------------------------------------------------------- |
| ADM erfasst Besuch mobil, nur eigene Daten | **Model-Driven App**                                       | MDA ist responsiv, Sicherheit über BU/Rollen direkt eingebaut    |
| Manager KPI-Auswertung                     | **Power BI** eingebettet in MDA                            | Aggregationen über viele Datensätze — Power BI ist dafür gemacht |
| SAP-Export täglich 03:00 Uhr               | **Power Automate** (Scheduled Flow) + **Custom Connector** | Geplante Ausführung, kein Nutzer-Trigger                         |
| ADM fragt per Sprache                      | **Copilot Studio Agent**                                   | Natürliche Sprache → Topics → Dataverse-Abfrage                  |
| Mahnung nach 24h ohne Bericht              | **Power Automate** (Automated Flow, Zeitplan-Trigger)      | Regelmäßige Prüfung auf fehlende Datensätze                      |

---

## Lösung Aufgabe 3 — Dataverse erkunden

**Wo:** [make.powerapps.com](https://make.powerapps.com) → Umgebung `VisitTrack-Dev` auswählen (oben rechts) → **Dataverse** → **Tables**

**Standardtabellen für VisitTrack:**

- **Ärzte** → Tabelle **Contact** (`contact`)

  - Bereits Felder: Vorname, Nachname, E-Mail, Telefon, Firma
  - Vorteil: Integration mit Outlook, Teams, Dynamics 365 automatisch
  - So erkennbar: Klicke auf Contact → sieh dir Columns an

- **Kliniken** → Tabelle **Account** (`account`)
  - Bereits Felder: Name, Adresse, Telefon, Webseite
  - Contact hat Lookup auf Account (Arzt gehört zu Klinik — bereits modelliert!)

**Warum Standardtabellen bevorzugen?**

1. Beziehung Contact → Account existiert bereits, kein eigenes ERD nötig
2. Power BI-Standardberichte funktionieren direkt
3. Microsoft pflegt die Tabellen — Updates kommen automatisch
4. Custom Connectors und andere Lösungen sprechen nativ dagegen
