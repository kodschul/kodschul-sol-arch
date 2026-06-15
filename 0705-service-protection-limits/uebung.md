# Lab 7.5 - Uebung: Service-Protection-Limits und Bulk-Muster beruecksichtigen

## Szenario

Ein Telekommunikationsunternehmen importiert taeglich Kundendaten aus einem Legacy-CRM in Dataverse. Der Import umfasst ca. 50.000 Datensaetze und laeuft um 03:00 Uhr nachts.

Der bisherige Ansatz: Ein Python-Skript ruft fuer jeden Datensatz die Web API einzeln auf:

```
POST /api/data/v9.2/contacts (50.000x)
```

---

## Aufgabe 1: Limit-Analyse (15 Minuten)

Das Skript laeuft unter einem einzigen Application User. Das Skript versucht, alle 50.000 Datensaetze in 30 Minuten zu importieren.

1. Wie viele Anfragen pro 5-Minuten-Fenster sind das im Schnitt?
2. Welches der drei SPL-Limits wird wahrscheinlich ueberschritten?
3. Was passiert dem Skript, wenn das Limit greift?

---

## Aufgabe 2: Bulk-Import-Strategie entwerfen (20 Minuten)

Entwirf eine verbesserte Import-Strategie, die die Service Protection Limits respektiert. Beruecksichtige:

- ExecuteMultiple (max. 1.000 Operationen pro Request)
- Retry-After bei 429-Antworten
- Moeglicherweise mehrere Application Users

Schreibe eine strukturierte Beschreibung der Strategie (kein Code, Konzept).

---

## Aufgabe 3: Architekturentscheidung Elastic Table (15 Minuten)

Das Unternehmen plant, zukuenftig auch Anruf-Logs zu speichern: ca. 2 Millionen Eintraege/Tag, 90 Tage Aufbewahrung = 180 Millionen Datensaetze.

Bewerte: Soll das in einer Standard Dataverse-Tabelle oder einer Elastic Table gespeichert werden? Gehe ein auf Kosten, Limits und Nutzbarkeit in Power Apps/Power BI.
