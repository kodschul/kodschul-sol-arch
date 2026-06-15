# Lab 7.4 - Uebung: Event-getriebene Integrationen entwerfen

## Szenario

Ein E-Commerce-Unternehmen betreibt eine Bestellverwaltung in Dataverse. Externe Systeme muessen auf Ereignisse reagieren:

**Event A:** Wenn eine Bestellung auf "Bezahlt" gesetzt wird, soll das Lagerverwaltungssystem sofort benachrichtigt werden, damit die Kommissionierung beginnt. Das Lagersystem hat einen stabilen HTTPS-Endpunkt. Erwartetes Volumen: max. 200 Bestellungen/Stunde.

**Event B:** Alle Statusaenderungen an Bestellungen sollen in ein Data Warehouse gestreamt werden. Volumen: bis zu 10.000 Aenderungen/Stunde in Spitzen.

**Event C:** Wenn eine Bestellung storniert wird, sollen gleichzeitig (a) das Buchhaltungssystem informiert werden und (b) eine Rueckerstattungs-E-Mail an den Kunden gesendet werden.

**Event D:** Wenn ein neuer Kunde angelegt wird, soll ein Marketing-Automation-System benachrichtigt werden. Das Marketing-System ist manchmal nicht verfuegbar (Wartungsfenster). Kein Event darf verloren gehen.

---

## Aufgabe 1: Integrationsmuster waehlen (20 Minuten)

Waehle fuer jedes Event das geeignete Benachrichtigungsmuster und begruende.

| Event                             | Muster | Begruendung |
| --------------------------------- | ------ | ----------- |
| A - Lager bei "Bezahlt"           |        |             |
| B - Data Warehouse Streaming      |        |             |
| C - Storno → Buchhaltung + E-Mail |        |             |
| D - Neuer Kunde → Marketing       |        |             |

---

## Aufgabe 2: Fehlerfall entwerfen (15 Minuten)

Fuer Event A (Lager-Webhook): Was passiert, wenn das Lagersystem gerade nicht erreichbar ist?

1. Wie verhaelt sich Dataverse bei einem fehlgeschlagenen Webhook?
2. Welche Information bekommst du als SA ueber fehlgeschlagene Webhooks?
3. Welche Massnahme empfiehlst du, um sicherzustellen, dass keine Kommissionierung verloren geht?

---

## Aufgabe 3: Service Bus fuer Event D (15 Minuten)

Du entscheidest dich, fuer Event D Azure Service Bus einzusetzen. Skizziere die Architektur:

1. Was wird in Dataverse konfiguriert?
2. Was wird in Azure konfiguriert?
3. Wie stellt der Service Bus sicher, dass keine Nachricht verloren geht, auch wenn das Marketing-System 4 Stunden im Wartungsfenster ist?
