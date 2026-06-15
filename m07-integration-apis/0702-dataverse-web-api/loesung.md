# Lab 7.2 - Loesung: Die Dataverse Web API im Architekturkontext einordnen

## Aufgabe 1: App-Registrierungen

| System                 | Auth-Fluss                                        | Dataverse-Rolle (Beschreibung)                                         |
| ---------------------- | ------------------------------------------------- | ---------------------------------------------------------------------- |
| Schadenserfassungs-App | Delegiert (im Namen des Aussendienstmitarbeiters) | Rolle des Aussendienstmitarbeiters - Create/Write Schaden (User-Tiefe) |
| Aktuarsystem           | App-only (S2S, kein menschlicher Nutzer)          | Read-only Rolle: Read Schaden (Organization), kein Write               |
| Makler-Portal          | Delegiert (im Namen des Maklers)                  | Makler-Rolle: Read Schaden (User-Tiefe oder BU je nach Modell)         |

**Begruendungen:**

**Schadenserfassungs-App (Delegiert):**
Der Aussendienstmitarbeiter ist der Eigentuemer seiner Datensaetze. Durch delegierte Authentifizierung wird der Request im Namen des Nutzers ausgefuehrt - die Dataverse-Sicherheitsrolle des Nutzers gilt. Somit kann der Aussendienstmitarbeiter nur seine eigenen Schaeden bearbeiten, nicht die anderer.

**Aktuarsystem (App-only):**
Das Aktuarsystem laeuft automatisch ohne menschliche Interaktion. Es benoetigt eine eigene App-Registrierung in Azure AD und einen Application User in Dataverse. Da es nur lesen muss, bekommt es eine Read-only-Rolle. Die S2S-Authentifizierung ist hier die einzige sinnvolle Option.

**Sicherheitshinweis Aktuarsystem:** Read Organization auf Schaden-Tabelle ist weit. Falls der Aktuarservice kompromittiert wird, sind alle Schadensdaten betroffen. Als haertere Massnahme: Read-Zugriff nur auf bestimmte Felder (mit Spaltensicherheitsprofil).

**Makler-Portal (Delegiert):**
Makler sehen nur ihre eigenen Schaeden → delegierter Fluss, damit die Sicherheitsrolle des einzelnen Maklers greift. Wenn der Makler User-Tiefe auf Read Schaden hat und seine Schaeden ihm gehoeren, sieht er nur seine.

---

## Aufgabe 2: OData-Abfrage optimieren

**Probleme mit der aktuellen Abfrage:**

1. **Kein $select:** Alle Felder werden zurueckgegeben, inkl. grosser Textfelder, gespeicherter Dateipfade etc. Viel unnoetige Datenmenge, hoehere Verarbeitungszeit.

2. **Kein $filter:** Es werden alle Schaeden zurueckgegeben, nicht nur die neuen. Das Aktuarsystem muss dann selbst filtern. Bei vielen Datensaetzen ist das ineffizient und belastet Service-Protection-Limits.

3. **Kein $top / Paginierung:** Bei grossen Datenmengen gibt Dataverse maximal 5.000 Datensaetze pro Seite zurueck. Ohne Paginierungslogik werden Daten unvollstaendig geladen.

**Verbesserte Abfrage:**

```
GET /api/data/v9.2/cr_schadens
  ?$select=cr_schadenid,cr_schadennummer,cr_schadenart,cr_betrag,createdon
  &$filter=createdon ge 2026-06-14T00:00:00Z
  &$orderby=createdon asc
  &$top=1000
```

Dazu: Paginierungstoken aus dem `@odata.nextLink` in der Antwort fuer nachfolgende Seiten auswerten.

---

## Aufgabe 3: Makler-Datenisolation

**Wie die Isolation funktioniert:**

1. **Delegierte Authentifizierung:** Der Makler meldet sich mit seinem Azure AD-Konto am Makler-Portal an. Das Portal erhaelt ein Token im Namen des Maklers.

2. **Dataverse Sicherheitsrolle des Maklers:** Der Makler-Nutzer hat in Dataverse die Rolle "Makler" mit Read-Zugriff auf die Schaden-Tabelle mit Tiefe "User".

3. **Eigentuemer der Schaeden:** Schaeden, die der Makler einreicht, haben den Makler als Eigentuemer. Schaeden anderer Makler haben andere Eigentuemer.

4. **Automatische Filterung durch Dataverse:** Wenn die API `GET /api/data/v9.2/cr_schadens` im Namen des Maklers aufruft, gibt Dataverse automatisch nur die Datensaetze zurueck, auf die der Makler laut seiner Sicherheitsrolle und BU-Tiefe Zugriff hat. Kein zusaetzlicher Filter in der Abfrage noetig.

**Was das Portal nicht tun sollte:**
Das Portal sollte **nicht** alle Schaeden laden und dann clientseitig filtern. Das waere:

- Ineffizient (alle Daten geladen, die meisten verworfen)
- Unsicher (bei einem Fehler im Filtercode sieht der Makler alle Schaeden)
- Falsch: Sicherheit gehoert in die Datenbank, nicht in die Applikationsschicht

**Dataverse uebernimmt das Filtering serverseitig** - das ist der entscheidende Vorteil des integrierten Sicherheitsmodells gegenueber anderen Datenbankloesungen.
