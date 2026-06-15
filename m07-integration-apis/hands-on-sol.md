# Hands-on-Lösung M07 — Integration & APIs

> Schritt-für-Schritt-Anleitung zur `hands-on.md`

---

## Lösung Aufgabe 1 — App-Registrierung erstellen

**Wo:** [portal.azure.com](https://portal.azure.com) → Suche oben „App registrations" → **+ New registration**

1. **Name:** `VisitTrack-SAP-Connector`
2. **Supported account types:** `Accounts in this organizational directory only (Single tenant)`
3. **Redirect URI:** leer lassen (Service-to-Service braucht keine)
4. Klicke **Register**

**Nach der Registrierung — Werte notieren:**

Auf der Übersichtsseite:

- **Application (client) ID:** `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ← notieren
- **Directory (tenant) ID:** `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ← notieren

**Client Secret erstellen:**

1. Linke Navigation → **Certificates & secrets** → **Client secrets** → **+ New client secret**
2. **Description:** `SAP-Integration`
3. **Expires:** `12 months`
4. Klicke **Add**
5. **Value** sofort kopieren ← wird nur einmal angezeigt!

---

## Lösung Aufgabe 2 — Application User in Dataverse

**Wo:** Admin Center → Umgebung `VisitTrack-Dev` → **Settings** → **Users + permissions** → **Application users** → **+ New app user**

1. Klicke **+ Add an app**
2. Suche nach `VisitTrack-SAP-Connector` → auswählen
3. **Business unit:** Root-BU (MedPharma GmbH)
4. Klicke **Create**
5. Öffne den neuen Application User → **Edit security roles** → hake **ADM VisitTrack** an → **Save**

**Warum Application User statt normaler Nutzer?**

- Application User hat kein Passwort und keine MFA — perfekt für automatische Prozesse
- Authentifizierung läuft über Client ID + Secret (Service Principal)
- Lässt sich jederzeit deaktivieren ohne einen echten Nutzer zu sperren

---

## Lösung Aufgabe 3 — Web API Aufruf testen

**Schritt 1: Bearer Token holen** (mit Postman)

Erstelle in Postman einen neuen Request:

- **Method:** `POST`
- **URL:** `https://login.microsoftonline.com/<TENANT_ID>/oauth2/v2.0/token`
- **Body** (x-www-form-urlencoded):

| Key             | Value                                           |
| --------------- | ----------------------------------------------- |
| `grant_type`    | `client_credentials`                            |
| `client_id`     | `<APPLICATION_ID>`                              |
| `client_secret` | `<CLIENT_SECRET>`                               |
| `scope`         | `https://<umgebung>.crm4.dynamics.com/.default` |

Klicke **Send** → kopiere den `access_token` aus der Antwort.

**Schritt 2: Besuche abrufen**

Neuer Request in Postman:

- **Method:** `GET`
- **URL:** `https://<umgebung>.api.crm4.dynamics.com/api/data/v9.2/vt_besuchs`
- **Header:** `Authorization: Bearer <access_token>`
- **Header:** `OData-MaxVersion: 4.0`
- **Header:** `OData-Version: 4.0`

Klicke **Send** → Antwort: JSON mit allen Besuchen.

**Verbesserte Abfrage (alle drei Parameter kombiniert):**

```
GET .../vt_besuchs
  ?$select=vt_besuchsdatum,vt_besuchsstatus
  &$filter=vt_besuchsstatus eq 2
  &$top=10
```

> Hinweis: Der Choice-Wert „Durchgeführt" ist intern eine Zahl (1, 2 oder 3 je nach Reihenfolge beim Anlegen). Prüfe den genauen Wert mit einem unfiltered Call zuerst.

---

## Lösung Aufgabe 4 — Integrationsmuster

| Anforderung                   | Muster                                                                                       | Begründung                                                                     |
| ----------------------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| SAP liest täglich 03:00 Uhr   | **Scheduled Flow + HTTP-Action** oder **Azure Logic App**                                    | Batch, zeitgesteuert, kein Echtzeit-Bedarf, keine verlorenen Nachrichten nötig |
| Slack bei neuem Besuch sofort | **Power Automate Automated Flow** (Dataverse Trigger: When a row is added) + Slack Connector | Event-getrieben, Echtzeit, kein SAP-Puffer nötig                               |
| 50.000 Besuche in 10 Minuten  | **Dataverse Web API + ExecuteMultiple**                                                      | Direct API write, Batching reduziert API-Calls, SPL-sicher                     |
