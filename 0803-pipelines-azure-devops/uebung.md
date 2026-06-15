# Lab 8.3 - Uebung: Power-Platform-Pipelines und Azure DevOps Build Tools einordnen

## Szenario

Ein Unternehmen hat zwei Teams, die unterschiedliche Projekte auf der Power Platform entwickeln:

**Projekt Alpha (Team A):** 2 Entwickler, kleines internes Tool fuer 50 Nutzer, kein Azure DevOps im Einsatz, Deployment ca. alle 2 Wochen.

**Projekt Beta (Team B):** 7 Entwickler, kritische Kundenloesung fuer 500 Nutzer, Azure DevOps lizenziert und genutzt, Deployment mehrmals pro Woche, Qualitaetspruefungen vor Deployment erforderlich.

---

## Aufgabe 1: Tool-Auswahl begruenden (20 Minuten)

Entscheide fuer Projekt Alpha und Projekt Beta, welches ALM-Werkzeug empfohlen wird. Begruende deine Entscheidung anhand der Projektcharakteristika.

| Projekt | Empfohlenes Werkzeug | Begruendung |
| ------- | -------------------- | ----------- |
| Alpha   |                      |             |
| Beta    |                      |             |

---

## Aufgabe 2: Pipeline-Design fuer Projekt Beta (25 Minuten)

Entwirf auf konzeptioneller Ebene (kein YAML noetig, Beschreibung reicht) eine Azure DevOps Pipeline fuer Projekt Beta.

Die Pipeline soll:

- Automatisch ausloesen wenn Code in den Main-Branch gemergt wird
- Die Foundation- und Automation-Loesung exportieren
- Einen Solution Checker laufen lassen (Qualitaetspruefung)
- Bei Bestehen automatisch in Test deployen
- Den Deployment in Prod nur nach manueller Genehmigung ausfuehren

Beschreibe:

1. Die Stages der Pipeline (Name + Zweck)
2. Wo werden Credentials gespeichert (Security)?
3. Wie wird der manuelle Approval-Schritt realisiert?

---

## Aufgabe 3: pac CLI verstehen (15 Minuten)

Dein Kollege aus Projekt Alpha fragt: "Ich habe pac CLI installiert. Wie exportiere ich die Loesung so, dass ich sie in Git einchecken kann?"

Beschreibe Schritt fuer Schritt, was er tun muss, und erklaere den Unterschied zwischen einem normalen Loesungs-Export und dem "Unpacking" fuer Source Control.
