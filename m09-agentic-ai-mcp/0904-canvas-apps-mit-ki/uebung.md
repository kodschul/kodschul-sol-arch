# Übung: Canvas App mit KI-Integration bauen

## Szenario

Der VisitTrack ADM soll beim Arztbesuch schneller Daten erfassen können. Heute tippt er alles manuell. Du baust eine enhanced Canvas App mit KI-Unterstützung.

---

## Aufgabe 1: KI-Feature Priorisierung (15 Minuten)

Bewertet als Team folgende KI-Features für die VisitTrack Canvas App:

| Feature                                           | AI-Technologie | Complexity | Business Value | Offline-fähig? | Empfehlung |
| ------------------------------------------------- | -------------- | ---------- | -------------- | -------------- | ---------- |
| Visitenkarte fotografieren → Arzt-Felder autofill |                |            |                |                |            |
| Chat: "Zeige mir alle Besuche bei Dr. Mueller"    |                |            |                |                |            |
| Besuchsnotizen automatisch zusammenfassen         |                |            |                |                |            |
| Stimmungsanalyse von Besuchsnotizen für Manager   |                |            |                |                |            |
| Spracheingabe für Besuchsnotiz                    |                |            |                |                |            |

Fülle die Tabelle aus. Nutze für Complexity und Value: `Low / Medium / High`.

Ergänze zusätzlich eine Spalte `Agentisch?` und entscheide, ob das Feature besser von einem Agenten vorbereitet, direkt in der App gebaut oder komplett als eigenständiger Agent umgesetzt wird.

---

## Aufgabe 2: Canvas App agentisch entwerfen (20 Minuten)

Schreibe einen Prompt für einen App-Design-Agenten, der aus den Anforderungen eine Canvas App-Skizze erzeugt.

Der Agent soll ausgeben:
- Screens und Navigation
- Controls je Screen
- Datenbindungen zu Dataverse
- erste Power Fx Formeln
- Offline-Verhalten
- Stellen, an denen ein Copilot Panel sinnvoll ist
- mindestens ein Mermaid-Diagramm für den App-Blueprint

Nutze dieses Format als Ausgangspunkt:

```text
Du bist ein Power Apps App Builder Agent.

Input:
- App-Ziel: VisitTrack für Außendienstmitarbeiter
- Datenquelle: Dataverse
- Kernaufgaben: Besuch erfassen, Arzt anlegen, Besuche ansehen
- KI-Funktionen: Visitenkarten-Scan, Chat, Zusammenfassung
- Offline: Ja

Output:
1. Screens mit Zweck
2. Controls mit Namen und Zweck
3. Navigation zwischen Screens
4. Power Fx Startformeln
5. Vorschlag für Copilot-Integration
6. Vorschlag für Offline-Fallback
```

---

## Aufgabe 3: App-Skelett in Power Apps umsetzen (20 Minuten)

Erstelle in Power Apps ein erstes Skelett der VisitTrack App:

1. Screen `VisitListScreen` mit einer Gallery für Besuche
2. Screen `VisitDetailScreen` mit Formular und Zusammenfassung
3. Screen `NewPhysicianScreen` mit Visitenkarten-Upload
4. Eine Navigationsleiste zwischen den Screens
5. Ein leeres Copilot Panel als Platzhalter

Nutze möglichst wenig manuelle Detailarbeit. Der Fokus liegt darauf, dass der App-Aufbau durch den agentischen Entwurf bereits vorstrukturiert ist.

Stelle dein Ergebnis als Mermaid-Flowchart dar, bevor du die Screens in Power Apps anlegst.

---

## Aufgabe 4: Visitenkarten-Scanner bauen (15 Minuten)

Baue in einer Canvas App einen Screen `NewPhysicianScreen`:

1. Ein `Camera`-Steuerelement oder `Add Media`-Button
2. Einen Button "Visitenkarte scannen"
3. `OnSelect` des Buttons: AI Builder Form Recognizer aufrufen (nutze das vorgefertigte Business Card Modell)
4. Die extrahierten Felder in Input-Felder übertragen: Name, Telefon, E-Mail, Adresse
5. Einen "Arzt speichern" Button, der den Arzt in Dataverse anlegt

Nutze Mock-Daten wenn AI Builder Credits nicht verfügbar sind:

```powerfx
// Mock: Simuliere AI Builder Ergebnis
Set(cardData, {
    fields: {
        name: {value: "Dr. Anna Bauer"},
        phone: {value: "+49 89 12345678"},
        email: {value: "a.bauer@praxis-bauer.de"},
        address: {value: "Maximilianstr. 12, 80539 München"}
    }
});
```

---

## Aufgabe 5: Copilot Chat Panel integrieren (15 Minuten)

Füge zur VisitTrack-Hauptapp ein Copilot Chat Panel hinzu:

1. Erstelle einen Copilot Studio Agent `VisitTrack Assistant` (aus M04)
2. Füge in der Canvas App eine `Copilot` Komponente ein
3. Verbinde den Agent mit der Komponente
4. Übergib den Kontext: aktuell ausgewählter Arzt + Besuch

Teste folgende User Stories im Chat:

- "Wie viele Besuche hatte ich diese Woche?"
- "Erstelle einen neuen Besuch für den ausgewählten Arzt"
- "Was ist die Spezialität von Dr. Mueller?"

---

## Aufgabe 6: Offline Graceful Degradation (10 Minuten)

Implementiere Offline-Handling für die KI-Features:

```powerfx
// Formel: KI-Features nur wenn online
Visible = Connection.Connected
```

Baue eine `OfflineBanner`-Komponente:

- Anzeige wenn `!Connection.Connected`
- Text: "Offline-Modus — KI-Funktionen nicht verfügbar"
- KI-Buttons (Visitenkarte, Chat) verstecken wenn offline
- Standard-Felder bleiben weiterhin editierbar

---

## Aufgabe 7: Architekturentscheidung dokumentieren (15 Minuten)

Dokumentiere deine Architekturentscheidungen für VisitTrack KI-Features in diesem Format:

```
Feature: Visitenkarten-Scanner
  Technologie: AI Builder Form Recognizer
  Begründung: ...
  Alternative: ...
  Warum nicht Alternative: ...
  Risiko: ...
  Kosten-Schätzung (AI Credits): ...

Feature: Copilot Chat Panel
  Technologie: ...
  Begründung: ...
  ...
```

---

## Abgabe

- Der Prompt für den App-Design-Agenten
- Screenshot oder Export des App-Skeletts
- Screenshot der Canvas App mit Visitenkarten-Scanner
- Screenshot des Copilot Chat Panels
- Power Fx-Code für Offline-Handling
- Architektur-Dokumentation (Aufgabe 7)
