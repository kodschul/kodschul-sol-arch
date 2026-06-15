# Uebung: Datenmodell-Strategien fuer Loesungen entwickeln

## Szenario

Du bist als Solution Architect bei einem mittelstaendischen Logistikunternehmen beauftragt worden, ein neues System zur Verwaltung von Lieferauftraegen zu entwerfen. Das Unternehmen hat bisher alles in Excel verwaltet. Die folgende Anforderungsliste wurde im Discovery-Workshop erarbeitet:

**Fachliche Anforderungen:**
- Lieferauftraege werden von Disponenten angelegt
- Jeder Auftrag gehoert genau einem Kunden
- Kunden haben Name, Adresse (Strasse, PLZ, Ort, Land), Ansprechpartner und Telefonnummer
- Ein Auftrag hat ein Wunschlieferdatum, ein tatsaechliches Lieferdatum, einen Status und eine Prioritaet (Normal, Express, Sondertransport)
- Auftraege koennen mehrere Positionen enthalten (Artikel, Menge, Gewicht in kg)
- Fahrer werden Auftraegen zugewiesen
- Zu jedem Auftrag sollen Notizen der Disponenten erfasst werden koennen
- Spaeter soll ein Bericht moeglich sein, der anzeigt welcher Fahrer wie viele Auftraege in welchem Monat erledigt hat

---

## Aufgabe 1: Entitaeten identifizieren

Lies die Anforderungen sorgfaeltig durch. Identifiziere alle Entitaeten (also alle Objekte, die als eigene Tabelle in Dataverse abgebildet werden sollten).

Beantworte folgende Fragen schriftlich:
- Welche Entitaeten erkennst du?
- Begruende fuer jede Entitaet, warum sie eine eigene Tabelle sein sollte (oder nicht sein muss)
- Welche Entitaeten kommen moeglicherweise bereits als Systemtabellen in Dataverse vor?

**Hinweis:** Denke daran, dass Dataverse bereits Standardtabellen wie Account, Contact und SystemUser mitbringt. Pruefe, welche dieser Tabellen wiederverwendet werden koennen.

---

## Aufgabe 2: Beziehungen modellieren

Zeichne ein Entity-Relationship-Diagramm (handschriftlich oder digital) mit allen identifizierten Tabellen und ihren Beziehungen. Verwende folgende Notation:

- 1:N bedeutet: Eine Zeile in Tabelle A kann mit mehreren Zeilen in Tabelle B verknuepft sein
- N:1 bedeutet: Mehrere Zeilen in Tabelle A verweisen auf eine Zeile in Tabelle B (das ist ein Lookup-Feld)
- N:N bedeutet: Beliebig viele Zeilen auf beiden Seiten

Beantworte dazu:
- Welche Beziehungsart hat Auftrag zu Auftragsposition?
- Welche Beziehungsart hat Auftrag zu Kunde?
- Koennte ein Fahrer gleichzeitig mehreren Auftraegen zugeordnet sein? Was bedeutet das fuer die Beziehungsart?

---

## Aufgabe 3: Strategie begruenden

Entscheide, welche der vier Modellierungsstrategien (Flaches Modell, Normalisiertes Modell, Erweiterungsmodell, Event-Log-Modell) fuer den Kern dieses Systems am besten geeignet ist.

Schreibe eine kurze Begruendung (ca. 150 Woerter) warum du diese Strategie waehlen wuerdest und was die Nachteile der anderen Strategien in diesem konkreten Fall waeren.

---

## Aufgabe 4: Risiken bewerten

Im spaeter geplanten Berichtswesen soll gezeigt werden, welcher Fahrer in welchem Monat wie viele Auftraege erledigt hat.

Welche Anforderungen stellt das an dein Datenmodell? Pruefe:
- Sind alle notwendigen Informationen im Modell vorhanden?
- Gibt es Felder die du ergaenzen muesstest?
- Koenntest du diesen Bericht mit einem flachen Modell (alle Daten in einer Tabelle) genauso gut umsetzen? Warum oder warum nicht?

---

## Abgabe

Halte deine Ergebnisse in einer strukturierten Dokumentation fest. Du brauchst fuer diese Uebung keinen Laptop, kein Dataverse und kein Power Apps. Es geht um konzeptionelles Denken auf Architekturebene.
