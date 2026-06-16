# Übung: Canvas App mit AI-Editor bauen

## Szenario

Du baust den ersten Entwurf der VisitTrack Canvas App nicht manuell — du nutzt GitHub Copilot (oder Claude Code) als Builder-Assistenten. Ziel ist ein funktionsfähiges App-Skelett das du danach manuell verfeinern kannst.

---

## Aufgabe 1: App-Blueprint per Prompt generieren (15 Minuten)

Schicke diesen Prompt an Copilot Chat (oder Claude):

```
Du bist ein Power Apps Architekt.

Erstelle einen App-Blueprint für VisitTrack:
- Zielgruppe: Außendienstmitarbeiter (ADM) auf Mobilgeräten
- Datenquelle: Dataverse (vt_visits, vt_physicians)
- Kernaufgaben: Besuche ansehen, neuen Besuch anlegen, Arzt suchen
- Anforderungen: Offline-fähig, Touch-optimiert

Output:
1. Screen-Liste mit Zweck je Screen (Mermaid flowchart)
2. Controls je Screen (als Tabelle: Name | Typ | Zweck)
3. Navigationsfluss zwischen Screens
4. Welche Screens sind offline-fähig, welche nicht
```

Bewerte das Ergebnis:

- Sind alle notwendigen Screens vorhanden?
- Fehlt etwas Wichtiges?
- Was hat Copilot/Claude falsch eingeschätzt?

---

## Aufgabe 2: Power Fx Formeln generieren (20 Minuten)

Generiere mit Copilot/Claude folgende drei Formeln:

**Formel A — Gefilterte Gallery:**

```
Prompt: "Schreibe die Items-Formel für eine Gallery die nur Besuche
         des aktuellen Nutzers aus vt_visits anzeigt,
         sortiert nach vt_visit_date absteigend,
         mit Offline-Fallback auf eine lokale Collection colOfflineVisits."
```

**Formel B — Besuch speichern:**

```
Prompt: "Schreibe die OnSelect-Formel für 'Besuch speichern'.
         Felder: vt_physician_id (Lookup aus drpPhysician),
                 vt_visit_date (aus datePicker),
                 vt_duration (aus txtDuration, Number),
                 vt_notes (aus txtNotes).
         Validierung: Arzt pflichtfeld, Datum darf nicht in der Zukunft liegen.
         Fehlerhandling: Notify() bei Fehler, Navigate() zurück bei Erfolg."
```

**Formel C — Arzt suchen:**

```
Prompt: "Schreibe die Items-Formel für eine Physician-Gallery
         die live nach vt_physician_name filtert
         basierend auf dem Text in txtSearch.
         Zeige max. 20 Ergebnisse."
```

Für jede Formel: Prüfe auf Delegation-Warnings, Pflichtfelder, Offline-Tauglichkeit.

---

## Aufgabe 3: PAC CLI-Script generieren (15 Minuten)

Generiere mit Copilot/Claude ein PowerShell-Script für:

```
Prompt: "Generiere ein PowerShell-Script für das VisitTrack Deployment:
         1. pac auth create für DEV ($DEV_ENV) und TEST ($TEST_ENV)
         2. Solution 'VisitTrack' aus DEV exportieren (Unmanaged)
         3. Als Managed Solution in TEST importieren
         4. Bei Fehler: Script abbricht, Fehlermeldung ausgeben
         Publisher: medpharma
         Output: kommentiertes PowerShell mit Variablen-Block am Anfang"
```

Prüfe das generierte Script:

- Sind die PAC CLI-Befehle syntaktisch korrekt?
- Fehlt der Unterschied Managed/Unmanaged?
- Ist der Error-Handling-Block vollständig?

---

## Aufgabe 4: CLAUDE.md / copilot-instructions.md für das Projekt (10 Minuten)

Erstelle eine `CLAUDE.md` Datei die Claude Code auf den VisitTrack-Kontext ausrichtet.

Pflicht-Abschnitte:

- Project (Ziel, Stack)
- Conventions (Tabellenpräfix, Power Fx Naming, Control-Benennung)
- Key Tables (die 3 wichtigsten Tabellen mit kurzem Zweck)
- What AI should NOT do (3 explizite Verbote)

Teste: Schicke ohne weiteren Kontext die Frage:

```
"Erstelle eine Filter-Formel für vt_visits"
```

Nutzt der AI-Editor automatisch `vt_`-Präfix und die richtigen Variablennamen?
