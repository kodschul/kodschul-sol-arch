# Lab 7.4 - Loesung: Event-getriebene Integrationen entwerfen

## Aufgabe 1: Integrationsmuster

| Event                        | Muster                        | Begruendung                                                                                                                                                                                                              |
| ---------------------------- | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| A - Lager bei "Bezahlt"      | Webhook                       | Stabiler HTTPS-Endpunkt vorhanden, Volumen niedrig (200/h = 3-4/Minute). Direkte Benachrichtigung ohne Zwischeninfrastruktur.                                                                                            |
| B - Data Warehouse Streaming | Azure Event Hub               | 10.000/h = ~167/Minute in Spitzen. Service Bus wuerde funktionieren, Event Hub ist fuer Hochvolumen-Streaming optimiert und in Data-Analytics-Pipelines (Azure Data Factory, Synapse) nativ integriert.                  |
| C - Storno → zwei Empfaenger | Service Bus mit Subscriptions | Ein Topic mit zwei Subscriptions: eine fuer Buchhaltung, eine fuer E-Mail-Versand. Beide Empfaenger erhalten dieselbe Nachricht unabhaengig voneinander. Power Automate Flow koennte auch funktionieren fuer 2 Aktionen. |
| D - Neuer Kunde → Marketing  | Azure Service Bus Queue       | "Kein Event darf verloren gehen" bei einem unzuverlaessigen Empfaenger = klarer Service-Bus-Fall. Nachrichten werden gepuffert bis das Marketing-System wieder verfuegbar ist.                                           |

---

## Aufgabe 2: Fehlerfall Webhook

**Verhalten bei fehlgeschlagenem Webhook:**
Dataverse sendet den HTTP-POST an die Webhook-URL. Wenn keine Antwort (Timeout 60 Sekunden) oder eine Fehlerantwort (4xx/5xx) kommt, markiert Dataverse den Webhook-Aufruf als fehlgeschlagen.

Dataverse versucht es erneut mit exponential backoff:

- 1. Retry: Nach ca. 1 Minute
- 2. Retry: Nach ca. 5 Minuten
- Weitere Retries bis zu 12 Mal insgesamt ueber mehrere Stunden

Nach 12 Fehlversuchen gibt Dataverse auf. Die Nachricht gilt als verloren.

**Monitoring:**

- Im Power Platform Admin Center unter "Webhooks" koennen fehlgeschlagene Aufrufe eingesehen werden
- Keine proaktive Benachrichtigung standardmaessig - SA muss Monitoring einrichten

**Massnahme fuer Zuverlaessigkeit:**
Der Webhook-Endpunkt des Lagersystems sollte eine "Received"-Bestaetigung zurueckgeben (HTTP 202 Accepted) und die eigentliche Verarbeitung asynchron durchfuehren. So wird der Webhook schnell als erfolgreich markiert.

Fuer kritische Kommissionierungsprozesse (kein Event darf verloren gehen) ist ein **Fallback auf Azure Service Bus** empfehlenswert: Service Bus als Webhook-Endpunkt, Lagersystem liest aus Service Bus. Dann sind Nachrichten persistent gepuffert, auch wenn das Lagersystem kurzfristig nicht erreichbar ist.

---

## Aufgabe 3: Service Bus Architektur fuer Event D

**In Dataverse konfigurieren:**

1. Service Endpoint Registrierung: Azure Service Bus Namespace URL + Queue-Name + Authentifizierungs-Key (SAS-Token) eintragen
2. Plugin Step oder Flow-Step mit dem Service Endpoint verknuepfen: "Wenn Account erstellt → schreibe in Service Bus"

**In Azure konfigurieren:**

1. Service Bus Namespace anlegen (mindestens Standard-Tier fuer Queues)
2. Queue "neue-kunden" anlegen
3. Message Time-to-Live konfigurieren: Wie lange sollen ungeverarbeitete Nachrichten bleiben? (z.B. 7 Tage)
4. Azure Function oder Logic App, die die Queue abhoert und das Marketing-System aufruft
5. Dead Letter Queue aktivieren (automatisch): Nachrichten, die nach N Versuchen nicht zustellbar sind, landen hier

**Wie Nachrichten bei 4-Stunden-Wartungsfenster nicht verloren gehen:**

- Dataverse schreibt die Nachricht in die Service Bus Queue: Dauert Millisekunden, unabhaengig vom Marketing-System
- Die Nachricht liegt in der Queue - Service Bus haelt sie persistent (in Azure-Speicher gesichert)
- Azure Function versucht die Nachricht zu verarbeiten: Marketing-System antwortet mit Fehler → Nachricht bleibt in Queue (Lock Renewal)
- Nach dem Wartungsfenster: Marketing-System wieder verfuegbar → Azure Function verarbeitet alle gepufferten Nachrichten der Reihe nach
- Keine Nachricht ist verloren, keine manuelle Intervention noetig

**Wichtiger Konfigurationspunkt:** Message Lock Duration auf dem Consumer (Azure Function) korrekt einstellen. Wenn die Verarbeitung laenger dauert als die Lock Duration, gibt die Nachricht das Lock frei und ein anderer Consumer koennte sie doppelt verarbeiten. Idempotenz im Marketing-System sicherstellen.
