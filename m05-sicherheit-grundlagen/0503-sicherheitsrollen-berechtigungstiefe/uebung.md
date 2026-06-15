# Lab 5.3 - Uebung: Sicherheitsrollen und Berechtigungstiefe sicher einsetzen

## Szenario

Du bist Solution Architect fuer ein Pharmaunternehmen, das eine Loesung zur Verwaltung von Aussendienst-Besuchen bei Aerzten und Kliniken baut (Model-Driven App, Dataverse).

**Nutzergruppen:**

- **Aussendienstmitarbeiter:** Besuchen Aerzte, erfassen Besuchsberichte, sehen nur eigene Datensaetze und Stammdaten der besuchten Kontakte
- **Regionsmanager:** Sehen alle Besuchsberichte ihrer Region, koennen keine eigenen Besuche erfassen
- **Marketing-Analysten:** Koennen alle Besuchsberichte lesen (fuer Analysen), aber nichts bearbeiten
- **Systemadmin:** Vollzugriff auf Konfiguration, nicht auf Produktivdaten

**Tabellen:**

- Besuchsbericht (erstellt durch Aussendienstmitarbeiter)
- Kontakt (Aerzte und Klinik-Ansprechpartner)
- Klinik (Stammdaten)
- Produkt (Stammdaten der beworbenen Produkte)
- Besuchsziel (jaehrliche Zielvorgaben je Mitarbeiter - vertraulich)

---

## Aufgabe 1: Berechtigungsmatrix erstellen (25 Minuten)

Erstelle eine Berechtigungsmatrix fuer die vier Nutzergruppen und alle fuenf Tabellen.

Nutze das Format:

- Berechtigungen: C (Create), R (Read), W (Write), D (Delete)
- Tiefe: (U) = User, (BU) = Business Unit, (PC) = Parent:Child, (Org) = Organization, (-) = kein Zugriff

| Tabelle        | Aussendienstmitarbeiter | Regionsmanager | Marketing-Analyst | Systemadmin |
| -------------- | ----------------------- | -------------- | ----------------- | ----------- |
| Besuchsbericht |                         |                |                   |             |
| Kontakt        |                         |                |                   |             |
| Klinik         |                         |                |                   |             |
| Produkt        |                         |                |                   |             |
| Besuchsziel    |                         |                |                   |             |

---

## Aufgabe 2: Rollenkumulierungs-Fallstrick erkennen (10 Minuten)

Ein Regionsmanager wird auch als temporaerer Aussendienstmitarbeiter eingesetzt. Er soll beide Rollen erhalten.

Analysiere:

1. Was passiert mit seinen Berechtigungen auf "Besuchsbericht"?
2. Ist das Ergebnis korrekt oder problematisch?
3. Wie loesung du das architektonisch?

---

## Aufgabe 3: Anti-Pattern identifizieren (15 Minuten)

Dein Kollege hat fuer alle Nutzergruppen folgende vereinfachte Konfiguration vorgeschlagen:

> "Ich gebe allen Nutzern die System-Rolle 'Basic User' plus eine eigene Rolle. In der eigenen Rolle setze ich fuer jede Tabelle Read auf Organization, damit alle immer alles sehen koennen. Schreibrechte sind nach Bedarf. Das ist einfacher zu verwalten."

Identifiziere alle Sicherheitsprobleme in diesem Ansatz und formuliere eine konstruktive Korrektur.
