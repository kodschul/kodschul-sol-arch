# Lab 2.3 - Uebung: Plattformgrenzen erkennen und berucksichtigen

## Aufgabe 1: API-Request-Analyse (20 Minuten)

Ein Unternehmen plant folgende automatisierte Loesung:
- Ein Power Automate Flow verarbeitet eingehende Supporttickets (E-Mail Trigger).
- Pro Ticket werden ausgefuehrt: 1 Dataverse-Schreibaktion (Ticket erstellen), 2 Lookups (Kunde und Produkt suchen), 1 Dataverse-Update (Kundendatensatz aktualisieren), 1 Benachrichtigung (Teams-Nachricht an Support-Team).
- Taeglich kommen ca. 500 Tickets ein.
- Der Flow laeuft unter einem dedizierten Service Account.

Berechne:
1. Wieviele API-Requests werden taeglich verbraucht?
2. Welche Lizenz benoetigt der Service Account?
3. Gibt es Risiken bei Lastspitzen (z.B. 1.500 Tickets an einem Tag)?

## Aufgabe 2: Rollup vs. Echtzeit (15 Minuten)

Bewerte folgende Anforderungen: Kann eine Rollup-Spalte diese Anforderung erfullen, oder ist eine Alternative noetig?

1. "Auf der Kunden-Detailseite soll die Anzahl offener Reklamationen angezeigt werden."
2. "Beim Erstellen einer Bestellung soll geprueft werden, ob der Kreditlimit des Kunden ueberschritten wird (Summe aller offenen Bestellungen + neue Bestellung)."
3. "Im monatlichen Bericht soll der Gesamtumsatz pro Vertriebsregion erscheinen."
4. "Auf dem Dashboard des Verkaufsmanagers soll der Tagesumsatz seines Teams in Echtzeit aktualisiert werden."

## Aufgabe 3: Delegation-Review (20 Minuten)

Prueffe folgende Canvas App-Formeln auf Delegierbarkeit in Dataverse. Markiere jede als delegierbar oder nicht delegierbar, erklaere warum und schlage eine delegierbare Alternative vor, wo moeglich.

1. `Filter(Kunden, Mid(KundenNummer, 3, 2) = "AT")`
2. `Filter(Bestellungen, Bestelldatum >= DateAdd(Now(), -30, TimeUnit.Days))`
3. `Filter(Produkte, IsBlank(Beschreibung))`
4. `Search(Mitarbeiter, Suchwort, "Nachname", "Vorname")`
5. `SortByColumns(Filter(Antraege, StartsWith(Antragsnummer, "ANT")), "Erstelldatum", SortOrder.Descending)`

## Aufgabe 4: Limit-bewusstes Design (15 Minuten)

Entwerfe fuer folgende Anforderung eine Loesung, die die Plattformlimits nicht verletzt:

"Das System soll naechtlich alle 50.000 Kunden-Datensaetze durchgehen und fuer jeden Kunden den Status basierend auf den letzten Aktivitaeten aktualisieren."

Welche Limit-Risiken gibt es? Wie wuerdes du die Loesung entwerfen?
