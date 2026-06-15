# Lab 2.3 - Loesung: Plattformgrenzen erkennen und beruecksichtigen

## Aufgabe 1: API-Request-Analyse

**Berechnung:**
5 Aktionen pro Ticket x 500 Tickets = 2.500 API-Requests pro Tag

**Lizenzanforderung:** 
2.500 Requests liegen weit unter dem Limit von 40.000 (Power Automate Premium). Selbst der guenstigere Per Flow Plan (keine User-Lizenz, sondern Flow-basiert) wuerde genuegen.

**Lastspitzenrisiko:**
Bei 1.500 Tickets: 5 x 1.500 = 7.500 Requests. Immer noch weit unter 40.000.

Das eigentliche Risiko liegt beim Service Protection Limit: Wenn alle 1.500 Tickets innerhalb einer Stunde ankommen und der Flow sie parallel verarbeitet, koennen innerhalb eines 5-Minuten-Fensters mehr als 6.000 Requests entstehen. Beispiel: Bei 300 Tickets in 5 Minuten x 5 Aktionen = 1.500 Requests. Das ist noch in Ordnung.

**SA-Massnahme:** Concurrency-Control im Flow auf 10 parallele Ausfuehrungen begrenzen. Das serialisiert die Verarbeitung und vermeidet Spitzen.

## Aufgabe 2: Rollup vs. Echtzeit

| Anforderung | Rollup ausreichend? | Begruendung und Alternative |
|---|---|---|
| 1. Anzahl offene Reklamationen auf Detailseite | Ja | Anzeige ist informativ, leichte Verzoegerung akzeptabel. |
| 2. Kreditlimit bei Bestellerstellung pruefen | Nein | Echtzeit-Pruefung notwendig. Alternative: Plugin im PreValidation-Stage, das die Summe live berechnet. |
| 3. Monatlicher Gesamtumsatz pro Region | Ja | Bericht wird einmal pro Tag oder Woche generiert. 12h Verzoegerung ist irrelevant. |
| 4. Tagesumsatz in Echtzeit auf Dashboard | Nein | "Echtzeit" schliesst Rollup aus. Alternative: Power BI mit Direct Query auf Dataverse, oder Azure Function die live aggregiert. |

## Aufgabe 3: Delegation-Review

| Formel | Delegierbar | Erklaerung | Alternative |
|---|---|---|---|
| 1. Mid(KundenNummer, 3, 2) = "AT" | Nein | Mid() ist nicht delegierbar. | Filter nach einer dedizierten Spalte "Laendercode" oder nach StartsWith/EndsWith wenn Muster es erlaubt. |
| 2. Bestelldatum >= DateAdd(Now(), -30, ...) | Ja | Datumsvergleiche sind in Dataverse delegierbar. | Keine Alternative noetig. |
| 3. IsBlank(Beschreibung) | Nein | IsBlank() ist nicht delegierbar. | In Dataverse: Filter(Produkte, Beschreibung = "") ist delegierbar. |
| 4. Search(Mitarbeiter, Suchwort, "Nachname", "Vorname") | Ja | Search() ist in Dataverse delegierbar fuer Text-Spalten. | Keine Alternative noetig. |
| 5. SortByColumns + Filter mit StartsWith | Ja | StartsWith ist delegierbar, SortByColumns auf delegierbare Spalten auch. | Keine Alternative noetig. |

## Aufgabe 4: Limit-bewusstes Design fuer 50.000 Datensaetze

**Limit-Risiken:**
- 50.000 Dataverse-Updates in einer Nacht = 50.000 API-Requests. Das ist weit unter 40.000 pro Tag WENN der Service Account einen Premium-Lizenz-Platz hat, der 40.000 Requests/24h erlaubt. Problem: Standard 40.000 Requests/24h wuerde nur fuer 40.000 Datensaetze reichen.
- Service Protection Limit: 6.000 in 5 Minuten. Bei 50.000 in 8 Stunden (Nacht): ca. 104 pro Minute = keine Verletzung.

**Empfohlenes Design:**

Option 1: Scheduled Flow mit Paginierung
- Flow startet um 02:00 Uhr naechtlich.
- Verarbeitet Datensaetze in Seiten von 5.000.
- Pro Seite: ExecuteMultiple mit Batch-Updates (bis 1.000 Updates = 1 API-Request fuer 1.000 Operationen).
- 50.000 Datensaetze in 50 Batches = 50 API-Requests statt 50.000.
- Ergebnis: Weit unter allen Limits, deutlich schneller.

Option 2: Azure Function mit Dataverse SDK
- Fuer hochvolumige Batch-Operationen empfiehlt sich eine Azure Function.
- Azure Function hat keinen User-Kontext, laeuft unter einer App Registration.
- Nutzt ExecuteMultiple fuer optimale Performance.
- Entkoppelt die Batch-Verarbeitung von Power Platform-Limits.

**SA-Empfehlung:** Option 1 fuer moderate Volumina (< 100.000 Datensaetze), Option 2 fuer grosse Volumina oder wenn der Batch taeglich oder oefter laufen muss.
