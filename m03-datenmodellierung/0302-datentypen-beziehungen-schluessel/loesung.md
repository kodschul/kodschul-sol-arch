# Loesung: Datentypen, Beziehungen und Schluessel gezielt einsetzen

## Aufgabe 1: Typen-Analyse

| Feldname | Aktueller Typ | Empfohlener Typ | Problem mit aktuellem Typ |
|---|---|---|---|
| Name | Single Line of Text | Single Line of Text | Korrekt, keine Aenderung |
| Adresse | Single Line of Text (alles zusammen) | 4 Felder: Strasse, PLZ, Ort, Land (je Single Line) | Nicht filterbar nach Ort, keine PLZ-Suche, keine Duplikatpruefung moeglich |
| Baujahr | Single Line of Text | Whole Number | Keine Sortierung, kein Altersberechnung, kein Vergleich ("aelter als 1990") moeglich |
| Kaufpreis | Single Line of Text | Currency | Keine Summen, kein Vergleich, keine Sortierung nach Preis, kein Waehrungssymbol |
| Verfuegbar | Single Line of Text | Two Option (Boolean) | "ja"/"Ja"/"JA" sind drei verschiedene Werte, kein Filter moeglich, fehleranfaellig |
| Eigentuemer | Single Line of Text | Lookup auf Account/Contact | Eigentuemer werden mehrfach erfasst, Tippfehler moeglich, keine Eigentuemer-uebergreifenden Berichte |
| NaechsteInspektion | Single Line of Text | Date Only | Kein automatischer Reminder moeglich, keine Sortierung, kein Vergleich |
| Flaeche | Single Line of Text | Decimal Number | Keine Berechnung moeglich, keine Sortierung, "qm" stoert die Verarbeitung |
| Typ | Single Line of Text | Choice | Tippfehler moeglich, kein kontrolliertes Vokabular, aber trotzdem Werte aus fester Liste |

---

## Aufgabe 2: Beziehungen

**Eigentuemer kann mehrere Immobilien besitzen:**
- Eigentuemer wird als Account (Firma) oder Contact (Privatperson) abgebildet
- Beziehungsart: N:1 (viele Immobilien gehoeren einem Eigentuemer)
- Umsetzung: Customer-Feld auf Immobilie (ermoeglicht Account ODER Contact als Ziel)

**Inspektionsberichte:**
- Neue Tabelle: Inspektion (mit Datum Date Only, Ergebnis Multi Line of Text, Inspektor Lookup auf Contact oder SystemUser)
- Beziehungsart: 1:N (eine Immobilie hat viele Inspektionen)
- Lookup-Feld "Immobilie" liegt auf der Inspektion-Tabelle

**Kategorien (Mehrfachzuordnung):**
- Neue Tabelle: Kategorie (mit Name, Beschreibung)
- Beziehungsart: N:N (eine Immobilie hat viele Kategorien, eine Kategorie gilt fuer viele Immobilien)
- Umsetzung: N:N-Beziehung zwischen Immobilie und Kategorie in Dataverse erstellen
- Da Kategorien keine eigenen Attribute an der Beziehung haben, reicht die automatische Zwischentabelle

---

## Aufgabe 3: Alternativschluessel

**Warum GUID nicht reicht:**
Die GUID wird von Dataverse generiert und ist dem Legacy-System unbekannt. Das Legacy-System kennt nur "IMM-00042". Wenn das Legacy-System eine Aktualisierung sendet, weiss Dataverse nicht, welcher Datensatz gemeint ist.

**Empfohlenes Feld:** Ein neues Feld "cr_LegacyID" (Single Line of Text, Required, max. 10 Zeichen) als Alternativschluessel konfigurieren.

**Anforderungen an das Feld:**
- Muss Required sein (kein leerer Wert erlaubt)
- Muss eindeutig sein (wird durch Alternativschluessel erzwungen)
- Muss Single Line of Text sein (kein Multiline, kein Image)

**Einschraenkung:** Maximal 5 Alternativschluessel pro Tabelle. Der Aufbau des Index kann bei grossen Tabellen einige Minuten dauern. Waehrend dieses Aufbaus koennen keine Upserts ueber diesen Schluessel durchgefuehrt werden.

---

## Aufgabe 4: Praxisfalle

**Warum der Reminder nicht funktioniert:**

Power Automate kann keine Datumsberechnungen auf Textfeldern durchfuehren. Ein Textfeld mit Inhalt "15.03.2025" ist fuer die Plattform eine beliebige Zeichenkette. "Heute + 14 Tage vergleichen mit dem Feld" ist nicht moeglich.

**Was veraendert werden muss:**
1. Neues Feld "NaechsteInspektionDatum" als Date Only anlegen
2. Bestehende Daten manuell oder per Flow migrieren, soweit das Format eindeutig ist
3. Werte wie "tbd" und leere Werte muessen als leer (null) belassen werden
4. Das alte Textfeld kann nach der Migration ausgeblendet oder geloescht werden

**Korrekter Typ:** Date Only (nicht Date and Time, da nur das Datum relevant ist und Zeitzonen-Probleme vermieden werden)

**Power Automate Flow-Logik danach:**
- Taeglich um 08:00 Uhr starten
- Alle Immobilien abrufen, bei denen NaechsteInspektionDatum zwischen heute und heute+14 liegt
- Pro Ergebnis eine E-Mail an den Eigentuemer senden
