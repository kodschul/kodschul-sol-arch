# Hands-on-Lösung M01 — Rolle & Methodik

> Schritt-für-Schritt-Anleitung zur `hands-on.md`

---

## Lösung Schritt 1 — Discovery-Interview

**Warum dieser Schritt?**
Ohne strukturierte Discovery baut man auf Annahmen. Das Discovery-Interview deckt auf, was wirklich gebraucht wird — nicht was der PM sich vorstellt.

**Musterergebnisse für VisitTrack:**

| Frage            | Typische Antwort MedPharma                                                        |
| ---------------- | --------------------------------------------------------------------------------- |
| Nutzergruppen?   | 120 ADM (mobil), 6 Regionalmanager (Desktop), 2 HR, 1 Admin                       |
| Daten heute wo?  | Excel auf lokalen Laptops, kein zentrales System                                  |
| Schnittstellen?  | SAP für Abrechnungsdaten, Outlook für Termine                                     |
| Größte Probleme? | Keine Übersicht wer was besucht hat, kein Reporting, Datenverlust bei Ausscheiden |
| Risiken?         | DSGVO: Arztdaten sind personenbezogen, Ausfall = ADM kann nicht berichten         |

---

## Lösung Schritt 2 — Fit-Gap-Tabelle

**Wie ausfüllen:** Prüfe für jede Anforderung ob die Power Platform es von Haus aus kann (Fit), mit leichter Konfiguration (Partial Fit), oder gar nicht (Gap).

| #   | Anforderung                     | Kategorie       | Power-Platform-Möglichkeit                     | Lücke / Risiko                           |
| --- | ------------------------------- | --------------- | ---------------------------------------------- | ---------------------------------------- |
| 1   | Besuchsbericht mobil erfassen   | **Fit**         | Model-Driven App, responsiv auf Handy          | Keine — MDA läuft im Browser mobil       |
| 2   | Manager sieht nur eigene Region | **Fit**         | BU-Hierarchie + Sicherheitsrollen              | Erfordert BU-Struktur (kommt in M05)     |
| 3   | Automatische Benachrichtigung   | **Fit**         | Power Automate Flow mit Scheduler              | Kein technisches Problem                 |
| 4   | SAP-Export täglich um 03:00 Uhr | **Partial Fit** | Power Automate + Custom Connector oder Web API | SAP braucht Connector oder REST-Endpoint |
| 5   | KPI-Report Besuche pro ADM      | **Fit**         | Power BI eingebettet in MDA                    | Kein Problem, Standardfunktion           |

---

## Lösung Schritt 3 — SA-Aufgaben priorisiert

**Die 3 ersten SA-Aufgaben — und warum:**

1. **Datenmodell entwerfen** (vor allem vor dem Bauen!)

   - Tabellenstruktur falsch = alles muss später umgebaut werden
   - Entscheidung: Standardtabelle Contact für Arzt? Account für Klinik?

2. **Umgebungsstrategie festlegen**

   - Dev / Test / Prod trennen — bevor die ersten Maker starten
   - Sonst landet alles in der Default-Umgebung (wie es aktuell schon passiert ist)

3. **Sicherheitskonzept skizzieren**
   - 120 ADM × 6 Regionen = BU-Hierarchie muss von Anfang an geplant sein
   - Nachträglich BUs einrichten wenn Daten schon da sind = sehr aufwendig

**Was ist NICHT die SA-Aufgabe:**

- Den SAP-Connector selbst bauen (das macht ein Developer)
- Flows programmieren (das macht ein Maker)
- Formulare designen (das macht ein Functional Consultant)
