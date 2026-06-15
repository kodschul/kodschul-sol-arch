# Lab 7.1 - Loesung: Integrationsmuster systematisch auswaehlen

## Aufgabe 1: Muster zuordnen

| Anforderung                     | Muster                                           | Begruendung                                                                                                                                                                                                                                                               |
| ------------------------------- | ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| A - SAP sendet Auftraege        | Asynchron: Azure Service Bus + Flow oder Webhook | Volumen bis 3.000/Stunde = bis 50/Minute. Synchroner Ansatz wuerde bei Spitzenlasten versagen. Service Bus puffert die Nachrichten, Flow verarbeitet nach Kapazitaet. Ausserdem: SAP kann ohne Bestaetigung senden, keine Abhaengigkeit von Power Platform-Verfuegbarkeit |
| B - Lagerbestand in Echtzeit    | Synchroner Custom Connector in Canvas App / Flow | Nutzer wartet bewusst auf das Ergebnis, Echtzeit ist die Anforderung. HTTP-Aufruf direkt bei Laden der App oder bei Klick auf Button                                                                                                                                      |
| C - SAP-Buchung bei Genehmigung | Asynchron: Azure Service Bus oder Queue Storage  | 3-8 Sekunden synchron wuerden das Formular blockieren. Ausserdem: Was passiert, wenn SAP down ist? Mit asynchronem Muster wird die Nachricht in der Queue gepuffert, SAP-Fehler koennen mit Retry-Logik behandelt werden                                                  |
| D - Taeglicher CRM-Export       | Scheduled Flow (Batch)                           | Kein Echtzeit-Bedarf, einmal taeglich ist ein klassischer Batch-Anwendungsfall. Scheduled Flow mit HTTP-Aktion gegen die REST-API des Archivs                                                                                                                             |
| E - Lieferantenportal           | Power Pages (Portal) + Dataverse                 | Externe Nutzer ohne Lizenz = Power Pages. Lieferanten bekommen ein Portal, das per Dataverse-Integration Daten einpflegt. Keine direkte API-Freigabe noetig                                                                                                               |

---

## Aufgabe 2: Risiken bei Anforderung C

**Synchroner Flow mit 3-8 Sekunden Wartezeit:**

- Der Nutzer, der "Genehmigt" klickt, wartet 3-8 Sekunden, bevor das Formular reagiert. Bei schlechter Verbindung oder SAP-Peaks laenger.
- Power Automate hat ein Timeout fuer synchrone HTTP-Aktionen. Bei langen Antwortzeiten kann der Flow ein Timeout erzeugen.
- Wenn SAP einen Fehler zurueckgibt (z.B. Validierungsfehler), muss der Flow den Fehler behandeln und dem Nutzer melden. Das erfordert explizite Fehlerbehandlung (Scope + Configure Run After).
- Wenn SAP voellig unavailable ist, schlaegt jede Genehmigung fehl. Das System blockiert den Geschaeftsprozess.

**Asynchrones Muster mit Azure Service Bus:**

- Der Nutzer klickt "Genehmigt" und bekommt sofort eine Bestaetigung: "Genehmigung gespeichert. SAP-Buchung wird verarbeitet."
- Der Flow sendet eine Nachricht an den Service Bus (in < 1 Sekunde).
- Ein separater Flow oder Azure Function liest die Nachricht aus dem Service Bus und sendet sie an SAP.
- Wenn SAP einen Fehler zurueckgibt, bleibt die Nachricht im Service Bus (Dead Letter Queue) und kann erneut verarbeitet werden, wenn das Problem behoben ist.
- Wenn SAP down ist, werden Nachrichten gepuffert und verarbeitet, sobald SAP wieder verfuegbar ist. Der Geschaeftsprozess wird nicht blockiert.

---

## Aufgabe 3: Virtual Tables Bewertung

**Vorteile Virtual Tables:**

- Lagerbestand erscheint wie ein nativer Dataverse-Datensatz - keine Canvas App-Programmierung noetig
- In Model-Driven Apps direkt als Unterraster oder Formularfeld nutzbar
- Power Platform-Nutzer brauchen keine Kenntnisse der Quell-API
- Filterung und Sortierung koennen (je nach Provider) auf der Quellseite passieren

**Nachteile / Risiken Virtual Tables:**

- Performance: Jede Ansicht, die Virtual Table-Daten zeigt, loest eine Live-Abfrage an das Lagerverwaltungssystem aus. Bei vielen Nutzern gleichzeitig koennen das viele parallele Abfragen sein.
- Kein Offline-Support: Virtual Tables funktionieren nur online.
- Eingeschraenkte Dataverse-Features: Advanced Find, Rollup-Spalten, Beziehungen funktionieren nicht oder eingeschraenkt mit Virtual Tables.
- Entwicklungsaufwand: Ein Virtual Table-Provider muss entwickelt werden (OData-Endpoint oder Custom Provider).
- Fehlerbehandlung: Wenn das Lagerverwaltungssystem nicht antwortet, ist die gesamte Ansicht kaputt.

**Empfehlung fuer diesen Use Case:**
Virtual Table ist sinnvoll, wenn der Lagerbestand als vollstaendige Tabelle integriert werden soll (z.B. Lagerbestand-Uebersicht, Filterung nach Artikel, etc.).

Fuer "Lagerbestand eines einzelnen Kunden beim Oeffnen des Datensatzes anzeigen" ist ein synchroner Custom-Connector-Aufruf in einem Canvas App-Steuerelement oder einem Flow mit On-Demand-Trigger einfacher, robuster und schneller zu implementieren. Die Daten muessen nicht dauerhaft in Dataverse vorhanden sein - nur beim Anzeigen abgerufen werden.
