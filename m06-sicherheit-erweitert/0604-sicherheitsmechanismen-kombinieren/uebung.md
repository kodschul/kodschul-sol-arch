# Lab 6.4 - Uebung: Sicherheitsmechanismen sinnvoll kombinieren

## Szenario

Du uebernimmst als Solution Architect eine bestehende Power Platform-Loesung fuer ein Logistikunternehmen. Die Loesung verwaltet Frachtauftraege, Fahrerdaten und Kundenstammdaten.

Beim Security-Review findest du folgendes vor:

1. Es gibt 23 Nutzer, alle mit der Rolle "System Customizer"
2. Das Feld "Fahrerlohn" in der Fahrertabelle ist nur im Formular ausgeblendet - keine Spaltensicherheit
3. Die HR-Abteilung hat Read-Organization auf alle Tabellen, inkl. Frachtauftraege
4. Es existieren 847 Sharing-Beziehungen auf Frachtauftraegen
5. Alle Datensaetze gehoeren der Root-BU
6. Es gibt keine dokumentierten Sicherheitsrollen - nur die Namen im System

---

## Aufgabe 1: Risiken priorisieren (20 Minuten)

Ordne die sechs Befunde nach Kritikalitaet (1 = hoechstes Risiko). Begruende jede Einordnung.

Nutze diese Dimensionen fuer die Bewertung:

- Wie viele Nutzer sind betroffen?
- Welche Daten sind gefaehrdet?
- Wie leicht ist das Problem ausnutzbar?

---

## Aufgabe 2: Sofortmassnahmen vs. mittelfristige Loesungen (20 Minuten)

Fuer die drei kritischsten Befunde: Was machst du sofort (heute), was planst du fuer die naechsten 4 Wochen?

| Befund      | Sofortmassnahme | Mittelfristige Loesung |
| ----------- | --------------- | ---------------------- |
| Befund #... |                 |                        |
| Befund #... |                 |                        |
| Befund #... |                 |                        |

---

## Aufgabe 3: Ziel-Sicherheitsarchitektur skizzieren (20 Minuten)

Entwirf die Ziel-Sicherheitsarchitektur fuer dieses Logistikunternehmen. Deine Skizze soll enthalten:

1. BU-Struktur (min. 2 Ebenen - Logistik hat Depot-Struktur: Zentrale, Depot Nord, Depot Sued, Depot West)
2. Welche Sicherheitsrollen brauchst du (Name und Kurzbeschreibung)?
3. Welche Felder brauchen Spaltensicherheit?
4. Wann ist Sharing legitim (1-2 Szenarien), wann nicht?
