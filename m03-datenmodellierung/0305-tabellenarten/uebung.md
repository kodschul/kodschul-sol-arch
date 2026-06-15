# Uebung: Tabellenarten architekturgerecht einordnen

## Szenario

Du bist als Solution Architect fuer ein Pharmaunternehmen taetig. Das Unternehmen moechte eine Aussendienstloesung bauen. Die folgenden Anforderungen wurden im Discovery erhoben:

- Aussendienstmitarbeiter besuchen Apotheken und Arztpraxen
- Besuchsberichte werden nach jedem Besuch erfasst (Gespraechsinhalt, Produkte besprochen, naechster Schritt)
- Arztpraxen und Apotheken sind die Besuchsziele
- Produkte des Unternehmens haben eine eigene Produktdatenbank im ERP-System (SAP), die per OData abrufbar ist
- Die Produktdaten aendern sich selten, sollen aber immer aktuell sein
- Jeder Mitarbeiter hat ein Gebiet (Region), das er betreut
- Regionen sind organisationsweit gepflegt, kein individueller Eigentuemer
- Die Tourenplanung erzeugt taeglich mehrere Millionen GPS-Trackingpunkte (Position alle 30 Sekunden)

---

## Aufgabe 1: Tabellenart zuordnen

Weise jeder der folgenden Entitaeten den passenden Tabellentyp zu und begruende deine Wahl:

| Entitaet | Beschreibung | Empfohlene Tabellenart | Begruendung |
|---|---|---|---|
| Arztpraxis | Besuchsziel, eigene Adresse und Arzt-Informationen | ? | ? |
| Apotheke | Besuchsziel, Kette oder Einzelapotheke | ? | ? |
| Besuchsbericht | Pro Besuch eines Mitarbeiters bei einer Praxis | ? | ? |
| Produkt | Kommt aus SAP, soll lesend abrufbar sein | ? | ? |
| Region | Wird zentral gepflegt, kein individueller Besitzer | ? | ? |
| GPS-Trackingpunkt | Taeglich Millionen Eintraege, nur Position und Zeit | ? | ? |

---

## Aufgabe 2: Konsequenzen der Tabellenentscheidung

Fuer den Besuchsbericht habt ihr euch fuer eine Custom Activity-Tabelle entschieden.

Beschreibe, welche konkreten Vorteile das fuer den Nutzer (den Aussendienstmitarbeiter) und den Administrator mit sich bringt. Welche Einschraenkungen gibt es?

---

## Aufgabe 3: Virtuelle Tabelle beurteilen

Ein Kollege schlaegt vor, die Produktdaten aus SAP per virtueller Tabelle einzubinden. Ein anderer Kollege moechte lieber eine Nacht-Synchronisation bauen, die Produkte taeglich nach Dataverse kopiert.

Welche Argumente sprechen fuer die virtuelle Tabelle, welche dagegen? Was wuerde du empfehlen und warum?

---

## Aufgabe 4: Fehler erkennen

Im bestehenden Datenmodell eines Vorgaengers wurde die Region als "User/Team owned" Custom-Tabelle angelegt. Beim Versuch, Regionen in einer App zu lesen, melden viele Aussendienstmitarbeiter, dass sie keine Daten sehen.

Erklaere, warum dieses Problem aufgetreten ist und wie es dauerhaft geloest wird. Hinweis: Es geht nicht um Berechtigungsrollen, sondern um einen Modellierungsfehler.
