# Lab 2.3 - Plattformgrenzen und technische Limits erkennen

## Warum ein SA die Limits kennen muss

Ein SA, der die technischen Limits der Plattform nicht kennt, entwirft Loesungen, die in der Produktion scheitern. Die typische Geschichte: Die Loesung funktioniert perfekt in der Entwicklungsumgebung mit 100 Testdatensaetzen. Im ersten Produktivmonat erreicht sie 10.000 Datensaetze und faengt an zu versagen. Der SA muss solche Szenarien voraussehen.

## API Request Limits

Jeder lizenzierte Power Platform-Nutzer hat ein tagesbasiertes Kontingent an API-Anfragen.

| Lizenz | API-Requests pro 24 Stunden |
|---|---|
| Power Apps Premium (per User) | 40.000 |
| Power Automate Premium (per User) | 40.000 |
| Dynamics 365 Enterprise-Lizenzen | 40.000 |
| Microsoft 365 (ohne Premium) | 6.000 |
| Per App Plan | 10.000 pro App |

Was zaehlt als API-Request?
- Jede Dataverse-Abfrage aus einer App
- Jede Flow-Aktion die auf Dataverse oder einen Connector zugreift
- Jede Plugin-Ausfuehrung

**Praxisbeispiel:** Ein Power Automate Flow verarbeitet eingehende Bestellungen. Jede Bestellung erfordert 5 Dataverse-Aktionen (Lesen, Schreiben, 3 Lookups). Bei 2.000 Bestellungen pro Tag werden 10.000 API-Requests verbraucht. Das ist weit unter dem Limit eines Premium-Nutzers. ABER: Wenn der Flow unter einem Service Account laeuft und dieser Account das Limit erreicht, schlaegt der Flow mit einer 429-Fehlermeldung fehl.

**SA-Massnahme:** Fuer automatisierte Loesungen mit hohem Volumen wird ein dedizierter Service Account mit ausreichend Kapazitaet eingeplant oder Power Platform für die Automatisierung entsprechend lizenziert.

## Service Protection Limits

Zusaetzlich zu den tagesbasierten Limits gibt es kurzfristige Schutzmechanismen.

**Drosselung tritt ein wenn:**
- Mehr als 6.000 API-Anfragen in einem 5-Minuten-Fenster von einem einzelnen Nutzer oder einer Anwendung
- Mehr als 52 Anfragen pro Sekunde in einem 20-Sekunden-Fenster

Bei Ueberschreitung antwortet Dataverse mit HTTP 429 (Too Many Requests). Der Header Retry-After gibt an, wie lange gewartet werden soll.

**SA-Massnahme:** Bulk-Operationen werden mit Pausen und Retry-Logik implementiert. ExecuteMultiple (SDK) fasst bis zu 1.000 Operationen in einer API-Anfrage zusammen und reduziert so den API-Overhead erheblich.

## Rollup-Spalten: Das asynchrone Problem

Rollup-Spalten berechnen Aggregationen ueber Kindentitaeten. Beispiel: Anzahl der offenen Aufgaben zu einem Kunden, Gesamtumsatz aller Bestellungen.

**Wichtige Fakten:**
- Rollup-Berechnungen sind asynchron, nicht in Echtzeit.
- Der Standard-Berechnungszyklus ist alle 12 Stunden.
- Ein Datensatz kann maximal alle 24 Stunden einmal manuell neu berechnet werden.
- Der Wert in der Rollup-Spalte kann also bis zu 12 Stunden alt sein.

```mermaid
timeline
    title Rollup-Berechnungszyklus
    00:00 : Systemjob berechnet alle Rollups
    06:00 : Neue Bestellungen eingegangen
    08:00 : Nutzer schaut auf Kunden-Total
    08:00 : Rollup zeigt noch alten Wert von 00:00
    12:00 : Systemjob berechnet alle Rollups neu
    12:00 : Nutzer sieht jetzt den aktualisierten Wert
```

**SA-Konsequenz:** Wenn ein Geschaeftsprozess auf einen aktuellen Aggregatwert angewiesen ist (zum Beispiel "Bestellung ablehnen wenn Gesamtkredit ueberschritten"), kann eine Rollup-Spalte das nicht leisten. Alternativen:
- Formelspalten (Power Fx, Echtzeit, aber nur fuer Berechnungen innerhalb eines Datensatzes)
- Plugin im PreOperation-Stage, der den Wert live berechnet
- Azure Function die synchron aufgerufen wird

## Canvas App Delegation: Das haeufigste Performance-Problem

Canvas Apps haben ein zentrales Limit, das viele Entwickler nicht kennen: die Data Row Limit.

**Wie Delegation funktioniert:**
Eine Canvas App sendet Abfragen an die Datenquelle. Wenn die Datenquelle die Abfrage vollstaendig ausfuehren kann (Filtern, Sortieren, Zaehlen), spricht man von Delegation. Die Datenquelle gibt nur die benoetigten Datensaetze zurueck.

Wenn die Datenquelle eine bestimmte Funktion nicht kennt (zum Beispiel IsBlank()), muss Canvas Apps alle Datensaetze auf den Client laden und die Funktion lokal ausfuehren. Das Limit dafuer: standardmaessig 500 Datensaetze, konfigurierbar bis 2.000.

**Delegierbare Funktionen in Dataverse (Auswahl):**

| Funktion | Delegierbar |
|---|---|
| Filter nach Lookup oder Text-Feld | Ja |
| StartsWith() | Ja |
| Search() | Ja |
| CountRows() | Ja (nur Top-Level) |
| Sum(), Average() | Ja |
| IsBlank() | Nein |
| Left(), Mid(), Right() | Nein |
| DateDiff(), DateAdd() | Nein |
| First(), Last() | Nur First() |

**Praxisbeispiel:**
```
// Falsch - nicht delegierbar, liefert nur 500/2000 Datensaetze
Filter(Bestellungen, Left(Bestellnummer, 2) = "OR")

// Richtig - delegierbar, liefert alle passenden Datensaetze
Filter(Bestellungen, StartsWith(Bestellnummer, "OR"))
```

**SA-Aufgabe:** Alle Datenabfragen in Canvas Apps auf Delegierbarkeit pruefen. Power Apps Studio zeigt eine gelbe Warnung fuer nicht-delegierbare Ausdrucke. Diese Warnungen duerfen nicht ignoriert werden.

## Weitere wichtige Limits

**Maximale Anzahl von Tabellen pro Umgebung:** Kein hartes Limit, aber Performance-Empfehlung: nicht mehr als 1.000 Custom Tables.

**Maximale Anzahl von Spalten pro Tabelle:** 400 Custom Columns.

**Maximale Zeilengrenzen in Dataverse:** Kein Datensatz-Limit, aber Speicherlimits gelten.

**Power Automate Flow-Limits:**
- Maximale Laufzeit eines einzelnen Flow-Runs: 30 Tage
- Maximale Anzahl von Aktionen in einem Flow: 500
- Maximale Parallelitaet in Foreach-Loops: 50 parallele Branches
