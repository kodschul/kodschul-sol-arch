# Lab 2.2 - Uebung: Dataverse als Architekturentscheidung

## Aufgabe 1: Standardtabellen identifizieren (15 Minuten)

Fuer das Urlaubsantragssystem werden verschiedene Entitaeten benoetigt. Entscheide fuer jede Entitaet, ob eine Dataverse-Standardtabelle genutzt werden kann oder ob eine Custom Table benoetigt wird. Begruende jede Entscheidung.

| Entitaet | Beschreibung |
|---|---|
| Mitarbeiter | Person, die einen Antrag stellt |
| Vorgesetzter | Person, die genehmigt |
| Urlaubsantrag | Der eigentliche Antrag mit Datum, Typ, Status |
| Abteilung | Organisationseinheit des Mitarbeiters |
| Urlaubstyp | Kategorisierung: Erholungsurlaub, Sonderurlaub, Gleitzeit |
| Genehmigungsprotokoll | Wer hat wann entschieden |

## Aufgabe 2: Dataverse vs. SQL-Entscheidung (20 Minuten)

Drei verschiedene Kunden haben unterschiedliche Anforderungen. Empfehle fuer jeden Kunden Dataverse oder SQL Server (oder eine Kombination) und begruende die Entscheidung.

**Kunde A:** Ein Produktionsunternehmen moechte Maschinendaten erfassen. Pro Stunde werden 50.000 Messpunkte generiert (Temperatur, Druck, Umdrehungen). Die Daten sollen 10 Jahre aufbewahrt werden. Visualisierung erfolgt ueber Power BI.

**Kunde B:** Ein Beratungsunternehmen moechte Projektzeiten und Reisekostenabrechnung fuer 500 Berater verwalten. Komplexe Genehmigungsprozesse, Row-Level-Zugriff (Berater sehen nur eigene Eintraege), Integration in SAP.

**Kunde C:** Ein Grosshandelsunternehmen hat bereits einen SQL Server mit ERP-Daten (Produkte, Bestellungen, Kunden). Sie moechten eine Power App fuer den Aussendienstmitarbeiter, der Bestellungen aufnehmen und Kundeninfos abrufen kann.

## Aufgabe 3: Speicherplanung (15 Minuten)

Das Urlaubsantragssystem wird 5.000 Mitarbeitern bereitgestellt. Schaetze den Speicherverbrauch nach zwei Jahren:
- Jeder Mitarbeiter stellt durchschnittlich 8 Urlaubsantraege pro Jahr.
- Bei Sonderurlaub (ca. 10% der Antraege) wird ein Anhang (PDF, durchschnittlich 500 KB) hochgeladen.
- Jeder Antrag erzeugt 3 Audit-Eintraege.

Berechne:
1. Anzahl der Datensaetze nach zwei Jahren
2. Geschaetztes Datenvolumen (Database, File, Log)
3. SA-Empfehlung zur Speicheroptimierung

## Aufgabe 4: Die vier Schichten einordnen (10 Minuten)

Ordne die folgenden Anforderungen der richtigen Dataverse-Schicht zu:

1. "Wenn ein Antrag erstellt wird, soll der Vorgesetzte eine E-Mail erhalten."
2. "Nur der Antragsteller und sein Vorgesetzter duerfen den Antrag sehen."
3. "Das Startdatum darf nicht in der Vergangenheit liegen."
4. "Das externe SAP-System soll den Status eines Antrags per API abfragen koennen."
