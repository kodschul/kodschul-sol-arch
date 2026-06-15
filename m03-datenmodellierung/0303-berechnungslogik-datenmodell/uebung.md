# Uebung: Berechnungslogik im Datenmodell korrekt bewerten

## Szenario

Du arbeitest an einem Projektmanagementsystem in Dataverse. Folgende Tabellen existieren:

- Projekt (mit Feldern: Name, Startdatum, Enddatum, Status, Budget)
- Aufgabe (mit Feldern: Titel, Stunden geplant, Stunden tatsaechlich, Status, Faelligkeitsdatum, Projekt-Lookup)
- Mitarbeiter (SystemUser)

Du sollst fuer verschiedene Anforderungen den richtigen Berechnungsmechanismus waehlen und begruenden.

---

## Aufgabe 1: Mechanismus zuordnen

Ordne jeder der folgenden Anforderungen den passenden Mechanismus zu (Rollup, Formelspalte, Calculated Column, gespeichertes Feld mit Flow/Plugin). Begruende deine Wahl in ein bis zwei Saetzen.

**Anforderung A:**
Am Projekt soll die Gesamtsumme aller geplanten Stunden aller verknuepften Aufgaben sichtbar sein. Die Anzeige darf bis zu einige Stunden verzoegert sein.

**Anforderung B:**
Jede Aufgabe soll ein Feld "Abweichung in Stunden" zeigen, das sich aus "Stunden tatsaechlich minus Stunden geplant" ergibt. Das Ergebnis soll immer aktuell sein.

**Anforderung C:**
Das Projekt soll ein Feld "Projektstatus Ampel" haben: Wenn Enddatum in der Vergangenheit und Status nicht "Abgeschlossen", dann "Rot". Wenn Enddatum in weniger als 14 Tagen und Status "In Bearbeitung", dann "Gelb". Sonst "Gruen".

**Anforderung D:**
Wenn die Summe der tatsaechlichen Stunden ueber dem Budget des Projekts liegt, soll automatisch eine Benachrichtigung an den Projektleiter gesendet werden.

---

## Aufgabe 2: Fallstricke erkennen

Ein Kollege hat eine Formelspalte "GesamtStunden" auf der Aufgabe-Tabelle erstellt, die die Summe aller Stunden aller Unteraufgaben berechnen soll. Der Kollege meldet, dass die Formel nicht funktioniert.

Erklaere, warum eine Formelspalte fuer diesen Zweck nicht geeignet ist, und welchen Mechanismus der Kollege stattdessen verwenden sollte.

---

## Aufgabe 3: Deprecated vs. aktuell

Im Datenmodell existieren noch zwei "Calculated Columns" aus einem aelteren Projektstand. Der Projektleiter fragt, ob diese umgebaut werden sollen.

Bewerte:
- Welche Vor- und Nachteile haben die bestehenden Calculated Columns?
- Was spricht fuer eine Migration zu Formelspalten?
- Was muss bei der Migration beachtet werden?

---

## Aufgabe 4: Timing ist entscheidend

Das Projektmanagement-Team moechte folgendes: Wenn ein Mitarbeiter eine Aufgabe als "Erledigt" markiert, soll automatisch geprueft werden, ob alle Aufgaben des Projekts erledigt sind. Falls ja, soll das Projekt auf "Abgeschlossen" gesetzt werden.

Erklaere, warum ein Rollup-Feld ("Anzahl offener Aufgaben") fuer diese Automatisierung problematisch waere, und wie du das Problem korrekt loesen wuerdest.
