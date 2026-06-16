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

---

## Aufgabe 2: Visitenkarten-Scanner bauen (20 Minuten)

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

## Aufgabe 3: Copilot Chat Panel integrieren (20 Minuten)

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

## Aufgabe 4: Offline Graceful Degradation (10 Minuten)

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

## Aufgabe 5: Architekturentscheidung dokumentieren (15 Minuten)

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

- Screenshot der Canvas App mit Visitenkarten-Scanner
- Screenshot des Copilot Chat Panels
- Power Fx-Code für Offline-Handling
- Architektur-Dokumentation (Aufgabe 5)
