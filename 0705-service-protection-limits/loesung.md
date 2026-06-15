# Lab 7.5 - Loesung: Service-Protection-Limits und Bulk-Muster beruecksichtigen

## Aufgabe 1: Limit-Analyse

**Anfragen pro 5-Minuten-Fenster:**

- 50.000 Datensaetze in 30 Minuten = 1.667 Anfragen/Minute
- In 5 Minuten: 1.667 × 5 = 8.333 Anfragen

**Welches Limit wird ueberschritten:**
Das Limit von 6.000 Anfragen pro 5-Minuten-Fenster wird deutlich ueberschritten (8.333 > 6.000). Selbst bei gleichmaessiger Verteilung - das Skript wuerde nach ca. 3,6 Minuten gedrosselt.

**Was passiert dem Skript:**
Nach ca. 3.600 Anfragen (in den ersten 3 Minuten) antwortet die API mit HTTP 429 Too Many Requests. Der Response-Header enthaelt `Retry-After: X` Sekunden.

Wenn das Skript keine Retry-Logik hat: Exception/Fehler, der Import bricht ab. Die bereits importierten Datensaetze sind gespeichert, der Rest fehlt. Das Datenbankteam bemerkt das am naechsten Morgen.

---

## Aufgabe 2: Bulk-Import-Strategie

**Verbesserte Strategie:**

**Schritt 1: ExecuteMultiple batching**
Statt 50.000 einzelner POST-Anfragen werden Batches von 500 Operationen erstellt. Das ergibt 100 ExecuteMultiple-Requests statt 50.000 Einzelanfragen.

- Weniger HTTP-Overhead
- 100 Requests in 30 Minuten = problemlos unter allen SPL-Limits

**Schritt 2: Rate-Limiting im Skript**
Das Skript sendet maximal 1.000 Anfragen pro 5-Minuten-Fenster (mit Sicherheitsabstand zum Limit von 6.000). Zwischen Batches wird eine kurze Pause eingebaut.

**Schritt 3: Retry-After Handling**
Bei jeder API-Antwort wird der Status-Code geprueft:

- 200/204: Naechster Batch
- 429: `Retry-After`-Wert lesen, entsprechend warten, dann erneut senden
- 500+: Batch loggen, nach 3 Versuchen in Fehler-Log schreiben und weiterfahren

**Schritt 4: ContinueOnError in ExecuteMultiple**
`ContinueOnError = true` setzen. So werden auch bei einem Fehler in einem Datensatz die restlichen 499 des Batches verarbeitet. Fehlerhafte Datensaetze werden einzeln geloggt.

**Optionale Erweiterung: Mehrere Application Users**
Wenn der zeitliche Rahmen enger werden sollte: 2-3 Application Users erstellen, Import-Datei aufteilen, parallele Prozesse starten. Jeder User hat sein eigenes 6.000-Anfragen-Budget.

---

## Aufgabe 3: Elastic Table fuer Anruf-Logs

**Empfehlung: Elastic Table**

**Begruendung:**

| Kriterium                          | Standard Table                                          | Elastic Table                                            |
| ---------------------------------- | ------------------------------------------------------- | -------------------------------------------------------- |
| 180 Mio. Datensaetze               | Technisch moeglich, aber sehr teuer (Datenbank-Storage) | Optimiert fuer grosse Mengen, Cosmos DB-basiert          |
| Kosten                             | Hoher Datenbankspeichers-Verbrauch → hohe Lizenzkosten  | Cosmos DB Throughput-basiert, bei Bulk besser skalierbar |
| Schreib-Performance                | Wird bei dieser Datenmenge langsamer                    | Hoher Durchsatz nativ unterstuetzt                       |
| Abfrage-Performance fuer Reporting | Gut fuer kleine Mengen, bei 180 Mio. schwieriger        | Gut fuer zeitbasierte Abfragen                           |
| Power Apps Integration             | Vollstaendig                                            | Eingeschraenkt (kein Model-Driven direkt)                |
| Power BI                           | Direkt per Connector                                    | Besser per Azure Synapse Link                            |

**Einschraenkungen Elastic Table beachten:**

- Keine Standardbeziehungen zu anderen Dataverse-Tabellen (z.B. Lookup auf Kontakt eingeschraenkt)
- Kein direktes Model-Driven App-Formular
- TTL (Time to Live) wird nativ unterstuetzt - ideal fuer 90-Tage-Aufbewahrung (automatisches Loeschen)

**Empfohlene Architektur fuer Reporting:**
Elastic Table als Speicher → Azure Synapse Link → Power BI ueber Synapse. Nicht direkt Power Apps fuer 180 Mio. Datensaetze.

**Fazit:** Fuer operative Anruf-Informationen eines einzelnen Kunden (letzte 10 Anrufe) → Standard Table mit Beziehung zum Kontakt. Fuer die Massenspeicherung und Analyse aller Logs → Elastic Table.
