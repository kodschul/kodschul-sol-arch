# Loesung: Tabellenarten architekturgerecht einordnen

## Aufgabe 1: Tabellenart zuordnen

| Entitaet | Empfohlene Tabellenart | Begruendung |
|---|---|---|
| Arztpraxis | Standard-Tabelle Account | Arztpraxen sind Organisationen, Account hat Adressfelder bereits, Dynamics-kompatibel |
| Apotheke | Standard-Tabelle Account | Ebenfalls eine Organisation, Unterscheidung per RecordType oder Choice-Feld "Typ" |
| Besuchsbericht | Custom Activity-Tabelle | Ist eine Kommunikationsart, soll in der Timeline des Accounts sichtbar sein, hat "Regarding"-Feld |
| Produkt | Virtuelle Tabelle mit OData-Provider | Daten kommen aus SAP, keine Duplikation gewuenscht, lesend genuegt, immer aktuell |
| Region | Custom-Tabelle, Organization owned | Referenzdaten ohne individuellen Eigentuemer, alle Nutzer lesen dieselben Daten |
| GPS-Trackingpunkt | Elastic Table | Millionen Eintraege taeglich, kein Sicherheitsmodell auf Zeilenebene noetig, hoher Schreibdurchsatz |

---

## Aufgabe 2: Konsequenzen der Activity-Tabellen-Entscheidung

**Vorteile fuer den Aussendienstmitarbeiter:**
- Der Besuchsbericht erscheint automatisch in der Timeline des Accounts (Arztpraxis, Apotheke)
- Wenn ein Kollege den Account aufruft, sieht er sofort alle Besuchsberichte ohne extra navigieren zu muessen
- Das "Regarding"-Feld verknuepft den Bericht automatisch mit der Praxis

**Vorteile fuer den Administrator:**
- Keine manuelle Verknuepfungslogik noetig
- Activity-Tabellen werden von Power Automate nativ unterstuetzt
- Timeline-Komponente in Model-Driven Apps ohne Konfiguration nutzbar

**Einschraenkungen:**
- Activity-Tabellen haben ein festes Schema von der Basisentitaet (Subject, Description, ActualEnd etc.), das moeglicherweise nicht zum Besuchsbericht passt
- Es koennen keine eigenen Beziehungstypen zu anderen Activity-Tabellen erstellt werden
- Anzahl Custom Activity-Tabellen pro Umgebung ist begrenzt

---

## Aufgabe 3: Virtuelle Tabelle versus Synchronisation

**Argumente fuer die virtuelle Tabelle:**
- Immer aktuell: Kein Sync-Delay, Nutzer sieht SAP-Daten in Echtzeit
- Keine Datenduplizierung: Single Source of Truth im ERP
- Kein Sync-Job zu entwickeln und zu warten
- Speicher wird gespart

**Argumente dagegen (fuer Synchronisation):**
- Offline-Faehigkeit: Wenn der Aussendienstler kein Netz hat (Keller einer Praxis), sieht er bei virtueller Tabelle keine Produkte
- Performance: Bei jedem Seitenaufruf wird SAP abgefragt, bei vielen Nutzern kann das SAP belasten
- Filterbarkeit und Reporting: Virtuelle Tabellen haben eingeschraenkte FetchXML-Faehigkeiten, Berichte ueber Produkte sind schwieriger
- Rollup und Beziehungen: Auf virtuelle Tabellen koennen keine Rollup-Felder aufgebaut werden

**Empfehlung:**
Wenn Offline-Nutzung nicht erforderlich ist und das SAP eine stabile, performante OData-API hat, ist die virtuelle Tabelle die sauberere Loesung. Wenn Offline-Faehigkeit erwartet wird oder SAP-Performance ein Thema ist, sollte eine taegl. Synchronisation mit einer Custom-Tabelle bevorzugt werden. Das muss mit dem Kunden abgeklaert werden.

---

## Aufgabe 4: Fehler erkennen

**Ursache des Problems:**
"User/Team owned" Tabellen nutzen das Dataverse-Sicherheitsmodell auf Zeilenebene. Das bedeutet: Ein Datensatz gehoert einem bestimmten Nutzer oder Team. Wenn ein anderer Nutzer keinen expliziten Zugriff hat (weder durch Rolle noch durch Sharing), sieht er den Datensatz nicht.

Regionen, die von einer zentralen Stelle angelegt wurden, gehoeren dem anliegenden Nutzer. Alle anderen Nutzer sehen diese Datensaetze nicht, ausser ihre Sicherheitsrolle erlaubt Organisation-weiten Lesezugriff.

**Dauerhafter Fix:**
Die Tabelle haette von Anfang an als "Organization owned" angelegt werden muessen. Da dieser Typ nicht nachtraeglich geaendert werden kann, gibt es zwei Optionen:

1. Neue Tabelle cr_Region neu erstellen als Organization owned, Daten migrieren, alte Tabelle loeschen
2. Sicherheitsrolle aller Aussendienstmitarbeiter erweitern, sodass sie "Read" auf Organisations-Ebene fuer die Region-Tabelle haben

Option 1 ist die architektonisch saubere Loesung. Option 2 ist ein Workaround, der das falsche Grundmodell bestehen laesst.
