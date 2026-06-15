# Lab 7.1 - Uebung: Integrationsmuster systematisch auswaehlen

## Szenario

Ein mittelstaendisches Fertigungsunternehmen (Automobilzulieferer) plant eine neue Power Platform-Loesung. Du bist SA und bekommst folgende Integrationsanforderungen:

---

**Anforderung A:** Das ERP-System (SAP S/4HANA) sendet stundlich neue Auftraege an Dataverse. Pro Stunde koennen es zwischen 10 und 3.000 Auftraege sein.

**Anforderung B:** Wenn ein Vertriebsmitarbeiter einen Kunden in der Model-Driven App oeffnet, soll sofort der aktuelle Lagerbestand aus dem Lagerverwaltungssystem angezeigt werden (keine Dataverse-Daten, echte Echtzeit-Abfrage).

**Anforderung C:** Wenn ein Auftrag in Dataverse auf "Genehmigt" gesetzt wird, soll automatisch eine Buchung in SAP angelegt werden. Die SAP-API benoetigt im Schnitt 3-8 Sekunden fuer diese Operation.

**Anforderung D:** Einmal taeglich sollen alle neuen Kundendatensaetze aus Dataverse in ein zentrales CRM-Archiv-System exportiert werden (REST API des Archivs).

**Anforderung E:** Externe Lieferanten sollen ueber ein Portal direkt Lieferstatus-Updates in Dataverse einpflegen koennen, ohne Power Platform-Lizenz zu benoetigen.

---

## Aufgabe 1: Muster zuordnen (20 Minuten)

Weise jeder Anforderung das geeignetste Integrationsmuster zu. Begruende jede Entscheidung.

| Anforderung                     | Muster | Begruendung |
| ------------------------------- | ------ | ----------- |
| A - SAP sendet Auftraege        |        |             |
| B - Lagerbestand in Echtzeit    |        |             |
| C - SAP-Buchung bei Genehmigung |        |             |
| D - Taeglicher CRM-Export       |        |             |
| E - Lieferantenportal           |        |             |

---

## Aufgabe 2: Risiken je Muster (15 Minuten)

Fuer Anforderung C (SAP-Buchung bei Genehmigung mit 3-8 Sekunden Wartezeit):

1. Was passiert, wenn du einen synchronen Flow-Aufruf verwendest und SAP laesst auf sich warten?
2. Was passiert, wenn SAP waehrend des Aufrufs einen Fehler zurueckgibt?
3. Wie veraendert sich das Verhalten, wenn du auf ein asynchrones Muster wechselst (z.B. Azure Service Bus)?

---

## Aufgabe 3: Virtual Tables bewerten (15 Minuten)

Fuer Anforderung B (Lagerbestand in Echtzeit) schlaegt dein Kollege vor: "Wir bauen eine Virtual Table, die das Lagerverwaltungssystem als Quelle nutzt. Dann erscheint der Lagerbestand wie ein normaler Dataverse-Datensatz."

Bewerte diesen Vorschlag. Was sind die Vor- und Nachteile gegenueber einem synchronen Custom-Connector-Aufruf im Flow oder einer Canvas App?
