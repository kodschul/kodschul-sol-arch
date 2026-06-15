# Uebung: Datentypen, Beziehungen und Schluessel gezielt einsetzen

## Szenario

Du uebernimmst das Datenmodell eines Vorgaenger-SA fuer ein Immobilienverwaltungssystem. Das Modell existiert bereits in Dataverse, zeigt aber Schwaechen. Du sollst eine Bewertung vornehmen und ein verbessertes Modell vorschlagen.

**Das bestehende Modell (vereinfacht):**

Tabelle: Immobilie
- Name: Single Line of Text
- Adresse: Single Line of Text (alles in einem Feld: "Musterstrasse 1, 70173 Stuttgart")
- Baujahr: Single Line of Text (z.B. "1987")
- Kaufpreis: Single Line of Text (z.B. "485000 EUR")
- Verfuegbar: Single Line of Text (z.B. "ja" oder "nein")
- Eigentuemer: Single Line of Text (Name des Eigentaemers als Text)
- NaechsteInspektion: Single Line of Text (z.B. "15.03.2025")
- Flaeche: Single Line of Text (z.B. "124.5 qm")
- Typ: Single Line of Text (z.B. "Wohnung", "Haus", "Gewerbe")

---

## Aufgabe 1: Typen-Analyse

Geh durch alle Felder der bestehenden Tabelle und bewerte jedes einzeln:
- Welcher Datentyp wurde gewaehlt?
- Welcher Datentyp waere korrekt?
- Was sind die konkreten Nachteile des falschen Typs?

Erstelle dafuer eine Tabelle mit den Spalten: Feldname | Aktueller Typ | Empfohlener Typ | Problem mit dem aktuellen Typ.

**Hinweis:** Denke bei jedem Feld daran, was ein Nutzer spaeter damit machen wollen koennte: Filtern, Sortieren, Summieren, Berechnen, Vergleichen.

---

## Aufgabe 2: Beziehungen erkennen und entwerfen

Die Anforderungen des Unternehmens sind weiter gewachsen. Folgende neue Anforderungen liegen vor:

- Ein Eigentuemer kann mehrere Immobilien besitzen
- Ein Eigentuemer ist entweder eine Firma (GmbH, AG) oder eine Privatperson
- Zu jeder Immobilie sollen Inspektionsberichte erfasst werden koennen (Datum, Ergebnis, Inspektor)
- Eine Immobilie kann mehreren Kategorien zugeordnet sein (z.B. "Denkmalschutz" und "Foerderprogramm A")
- Kategorien werden vom Administrator gepflegt

Entscheide fuer jede dieser Anforderungen:
- Welche neuen Tabellen werden benoetigt?
- Welche Beziehungsart (1:N, N:1, N:N) liegt vor?
- Welches Feld ist das Lookup-Feld und in welcher Tabelle liegt es?

---

## Aufgabe 3: Alternativschluessel

Das Unternehmen nutzt parallel ein Legacy-System, das Immobilien mit einer internen ID verwaltet (Format: "IMM-XXXXX", z.B. "IMM-00042"). Dieses System soll spaeter per API Daten nach Dataverse synchronisieren.

Beantworte:
- Warum reicht die Dataverse-eigene GUID nicht als Schnittstellen-ID?
- Welches Feld wuerdest du als Alternativschluessel konfigurieren?
- Welche Anforderungen muss dieses Feld erfuellen?
- Welche Einschraenkung musst du beim Erstellen des Alternativschluessels beachten?

---

## Aufgabe 4: Praxisfalle

Im Feld "NaechsteInspektion" stehen aktuell Werte wie "15.03.2025", "Maerz 2025", "tbd" und "".

Du moechtest in Power Automate einen automatischen Reminder senden, wenn die Inspektion in weniger als 14 Tagen faellig ist.

- Warum funktioniert das mit dem aktuellen Feld nicht?
- Was muss veraendert werden?
- Welcher Datentyp ist korrekt?
- Was muss mit den bestehenden Daten passieren, bevor die Umstellung moeglich ist?

---

## Hinweis zur Bearbeitung

Diese Uebung wird im Team empfohlen. Diskutiert die Typenentscheidungen gemeinsam, da es bei einigen Feldern mehrere vertretbare Antworten gibt. Das Ziel ist nicht die eine richtige Antwort, sondern das Begruenden der eigenen Entscheidung.
