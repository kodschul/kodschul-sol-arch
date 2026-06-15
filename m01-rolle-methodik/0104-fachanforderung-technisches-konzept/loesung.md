# Lab 1.4 - Loesung: Von der Fachanforderung zum technischen Konzept

## Aufgabe 1: Kritikalitaet und Komplexitaet

| ID | Kritikalitaet (1-5) | Komplexitaet (1-5) | Begruendung |
|---|---|---|---|
| ANF-001 | 5 | 2 | Kernfunktion ohne die das System keinen Zweck hat; technisch aber einfach. |
| ANF-004 | 3 | 5 | Fehler hier brechen keine Kernfunktion, aber die Integration ist hochkomplex. |
| ANF-006 | 4 | 4 | Fehler hier fuehren zu nicht bearbeiteten Antraegen; Vertretungslogik ist komplex. |
| ANF-008 | 2 | 1 | Erinnerungen sind nice-to-have und technisch trivial. |
| ANF-011 | 4 | 1 | Protokollierung ist rechtlich relevant; Dataverse-Audit ist aber einfach zu aktivieren. |

**Groesste Planungsaufmerksamkeit verdient ANF-006:** Hohe Kritikalitaet (4) kombiniert mit hoher Komplexitaet (4). Fehler im Vertretungsprozess fuehren dazu, dass Antraege in einem Deadlock stecken.

## Aufgabe 2: Technisches Konzept ANF-006

**Datenmodell:**

Neue Tabelle cr_AbwesenheitsPlanung mit Feldern:
- cr_BenutzerID (Lookup SystemUser, Pflichtfeld): Wer ist abwesend?
- cr_VertreterID (Lookup SystemUser, Pflichtfeld): Wer vertritt?
- cr_GueltigVon (Datum, Pflichtfeld)
- cr_GueltigBis (Datum, Pflichtfeld)
- cr_Aktiv (Boolean, berechnet aus Datumsbereich)

Tabelle cr_UrlaubsAntrag erhaelt zusaetzliches Feld:
- cr_AktuellerGenehmiger (Lookup SystemUser): Wer muss aktuell genehmigen?

**Prozesslogik:**

Trigger: Neuer cr_UrlaubsAntrag wird erstellt.

Schritt 1: Power Automate prueft, ob der Vorgesetzte (cr_VorgesetzterID) einen aktiven Eintrag in cr_AbwesenheitsPlanung hat, der den Antragszeitraum ueberlappt.

Schritt 2a (Vorgesetzter verfuegbar): cr_AktuellerGenehmiger = cr_VorgesetzterID. E-Mail an Vorgesetzten.

Schritt 2b (Vorgesetzter abwesend): cr_AktuellerGenehmiger = cr_VertreterID aus cr_AbwesenheitsPlanung. E-Mail an Vertreter mit Hinweis, dass er als Vertreter handelt.

**Annahmen (muessen mit Kunden bestaetigt werden):**
1. Jeder Vorgesetzte hat maximal einen aktiven Vertreter gleichzeitig.
2. Wenn kein Vertreter eingetragen ist und der Vorgesetzte abwesend ist, geht der Antrag trotzdem an den abwesenden Vorgesetzten (Eskalation durch HR muss manuell erfolgen).
3. Bereits erstellte Antraege werden nicht rueckwirkend umgeleitet wenn eine Vertretung eingetragen wird.

**Testkriterium:** 
Vorgesetzter A ist vom 20. bis 27. Juni abwesend. Vertreter B ist fuer diesen Zeitraum eingetragen. Mitarbeiter stellt Antrag fuer 22. bis 24. Juni. Ergebnis: Vertreter B erhaelt die E-Mail-Benachrichtigung, nicht Vorgesetzter A. Vorgesetzter A erhaelt keine Benachrichtigung.

## Aufgabe 3: ADR

```
Titel: ADR-002 - Speicherung der Vertreterkonfiguration

Datum: 15.06.2026
Status: Akzeptiert

Kontext:
Die Vertretungslogik benoetigt Information darueber, wer in einem bestimmten Zeitraum
wen vertritt. Diese Information muss zur Laufzeit abfragbar sein.

Optionen:
Option A: Eigene Tabelle cr_AbwesenheitsPlanung in Dataverse
Option B: Custom Field in SystemUser-Tabelle
Option C: Aus SAP uebernehmen

Entscheidung:
Option A (eigene Tabelle).

Begruendung:
Option B (SystemUser-Feld) wuerde nur einen einzigen Vertreter ohne Zeitraum
ermoeglichen. Das reicht fuer Urlaubsplanung nicht aus.
Option C ist abhaengig von ANF-004 und somit ein Risiko. Wenn SAP nicht rechtzeitig
integriert ist, kann die Vertretungslogik nicht funktionieren.
Option A erlaubt zeitraumbasierte Vertretungen, ist unabhaengig von SAP und kann
von HR-Sachbearbeiterinnen direkt in der App gepflegt werden.

Konsequenzen:
- HR muss Vertretungen in der App einpflegen, bevor sie wirksam sind.
- Es wird eine Admin-Ansicht in der HR-App benoetigt fuer die Verwaltung.
- Wenn SAP spaeter integriert wird, kann cr_AbwesenheitsPlanung automatisch 
  aus SAP befuellt werden.
```

## Aufgabe 4: Praezisierungsfragen

1. "Was soll passieren, wenn auch der Vertreter des Vorgesetzten im selben Zeitraum abwesend ist? Gibt es eine Eskalationskette oder entscheidet dann HR manuell?"

2. "Wer ist dafuer verantwortlich, die Vertretungen im System einzupflegen? Macht das jeder Vorgesetzte selbst, oder wird das zentral durch HR verwaltet?"

3. "Gilt die Vertretungsregel fuer alle Typen von Abwesenheiten des Vorgesetzten (Urlaub, Krankheit, Dienstreise), oder nur wenn der Vorgesetzte selbst Urlaub beantragt hat?"
