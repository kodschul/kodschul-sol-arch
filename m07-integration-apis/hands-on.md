# Hands-on M07 — Integration & APIs

> **Typ:** Praktisch — Postman oder Browser + Terminal
> **Dauer:** ca. 45 Minuten
> **Lösung:** siehe `hands-on-sol.md`

---

## Aufgabe 1 — App-Registrierung erstellen

Das SAP-System soll täglich Besuche aus Dataverse lesen (Service-to-Service, kein Nutzer-Login).

Erstelle eine App-Registrierung in **Entra ID** (Azure Portal → App registrations):

- **Name:** `VisitTrack-SAP-Connector`
- **Supported account types:** Single tenant

Notiere nach der Registrierung:

- Application (Client) ID: ******\_\_\_******
- Directory (Tenant) ID: ******\_\_\_******

Erstelle danach unter **Certificates & secrets** einen neuen Client Secret und notiere den Wert.

---

## Aufgabe 2 — Application User in Dataverse anlegen

Lege in `VisitTrack-Dev` einen Application User an der die App-Registrierung repräsentiert.
Weise ihm die Rolle **ADM VisitTrack** zu — aber nur mit Read-Berechtigung auf Besuch.

---

## Aufgabe 3 — Web API Aufruf testen

Rufe alle Besuche aus Dataverse ab. Verwende dazu Postman oder den Browser nach Token-Erhalt:

```
GET https://<deine-umgebung>.api.crm4.dynamics.com/api/data/v9.2/vt_besuchs
```

Verbessere die Abfrage mit OData-Parametern:

1. Nur die Felder `vt_besuchsdatum` und `vt_besuchsstatus` zurückgeben (`$select`)
2. Nur Besuche mit Status „Durchgeführt" filtern (`$filter`)
3. Maximal 10 Einträge (`$top`)

Schreibe die fertige URL auf.

---

## Aufgabe 4 — Integrationsmuster entscheiden

Welches Muster wählst du — und warum?

| Anforderung                                       | Dein Muster | Begründung |
| ------------------------------------------------- | ----------- | ---------- |
| SAP liest Besuche täglich um 03:00 Uhr            |             |            |
| Bei neuem Besuch: Slack-Nachricht sofort          |             |            |
| Externe App schreibt 50.000 Besuche in 10 Minuten |             |            |

---

## Checkpoint ✓

- [ ] App-Registrierung `VisitTrack-SAP-Connector` in Entra ID existiert
- [ ] Application User in Dataverse mit Rolle angelegt
- [ ] Basis-Aufruf gibt Besuche zurück (HTTP 200)
- [ ] Verbesserter Aufruf mit `$select`, `$filter`, `$top` funktioniert
- [ ] Integrationsmuster-Tabelle ausgefüllt mit Begründungen
