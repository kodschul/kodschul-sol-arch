# Lab 6.1 - Uebung: Hierarchiesicherheit gezielt einsetzen

## Szenario

Ein Beratungsunternehmen mit 600 Mitarbeitern betreibt eine Dataverse-basierte Projektverwaltung. Die Struktur ist:

- **Practice Leads (3 Personen):** Leiten jeweils einen Fachbereich (Cloud, Data, ERP), verantwortlich fuer alle Projekte ihres Bereichs
- **Senior Consultants (12 Personen):** Berichten an Practice Leads, leiten selbst Teams
- **Consultants (ca. 50 Personen):** Berichten an Senior Consultants, bearbeiten Projekte
- **Projektdatensaetze:** Eigentuemer ist immer der verantwortliche Consultant

Das AD-Feld "Manager" ist aktuell nicht systematisch gepflegt. HR pflegt es "so gut es geht".

---

## Aufgabe 1: Eignung bewerten (15 Minuten)

Bewerte, ob Hierarchiesicherheit fuer dieses Unternehmen geeignet ist. Gehe ein auf:

1. Was spricht dafuer?
2. Was spricht dagegen?
3. Was ist die Voraussetzung, damit es funktioniert?
4. Welche Alternative gaebe es?

---

## Aufgabe 2: Tiefenkonfiguration bestimmen (10 Minuten)

Wenn Hierarchiesicherheit eingesetzt wird: Welche Tiefe braucht ein Practice Lead, um alle Projektdatensaetze seines Fachbereichs zu sehen?

Zeichne die Hierarchiekette und begruende die Tiefe.

---

## Aufgabe 3: Risikoszenario durchdenken (15 Minuten)

Ein Senior Consultant wechselt intern den Fachbereich. HR aktualisiert das Manager-Feld in AD erst drei Wochen spaeter.

Welche konkreten Zugriffsprobleme entstehen in dieser Zeit? Welche Massnahme empfiehlst du dem Unternehmen als SA?
