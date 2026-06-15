# Lab 7.2 - Uebung: Die Dataverse Web API im Architekturkontext einordnen

## Szenario

Ein Versicherungsunternehmen integriert drei externe Systeme mit Dataverse:

1. **Schadenserfassungs-App** (mobile App, JavaScript-Frontend): Aussendienstmitarbeiter erfassen Schaden vor Ort und senden Daten direkt an Dataverse.
2. **Aktuarsystem** (internes Legacy-System, Java): Liest taeglich alle neuen Schadenmeldungen aus Dataverse fuer Risikoberechnungen.
3. **Makler-Portal** (externe Partneranwendung): Makler koennen den Status ihrer eingereichten Schaeden lesen - aber nur ihre eigenen.

---

## Aufgabe 1: App-Registrierungen entwerfen (20 Minuten)

Entscheide fuer jedes der drei Systeme:

1. Welcher Authentifizierungsfluss ist geeignet (Delegiert = im Namen eines Nutzers / App-only = Service-to-Service)?
2. Welche Sicherheitsrolle bekommt der jeweilige Application User in Dataverse?
3. Welche Tabellen und mit welchen Rechten?

| System                 | Auth-Fluss | Dataverse-Rolle (Beschreibung) |
| ---------------------- | ---------- | ------------------------------ |
| Schadenserfassungs-App |            |                                |
| Aktuarsystem           |            |                                |
| Makler-Portal          |            |                                |

---

## Aufgabe 2: OData-Abfrage optimieren (15 Minuten)

Das Aktuarsystem ruft jeden Morgen um 02:00 Uhr alle neuen Schaden ab. Aktuell wird folgende URL verwendet:

```
GET /api/data/v9.2/cr_schadens
```

Identifiziere drei Probleme mit dieser Abfrage und schreibe eine verbesserte Version.

---

## Aufgabe 3: Makler-Datenisolation (15 Minuten)

Das Makler-Portal soll pro Makler nur dessen eigene Schaeden sehen. Der Makler authentifiziert sich mit seinem Azure-AD-Konto.

Beschreibe, wie du sicherstellst, dass ein Makler durch die Web API keine Schaden anderer Makler lesen kann. Welche Dataverse-Sicherheitsmechanismen greifen hier?
