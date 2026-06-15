# Loesung: Copilot Studio in die Loesungsarchitektur integrieren

## Aufgabe 1: Integrationsarchitektur

**Entscheidung: Power Automate Flow als Vermittler**

Der ausschlaggebende Grund ist die E-Mail-Benachrichtigung an den Vorgesetzten. Eine E-Mail-Aktion kann nicht direkt im Copilot Studio Agent ausgefuehrt werden. Dafuer wird zwingend ein Flow benoetigt. Da der Flow sowieso benoetigt wird, ist es architektonisch sauber, auch die Dataverse-Speicherung im Flow zu bündeln.

Zweiter Grund: Die Rueckgabe der Antragsnummer ist einfacher umsetzbar, wenn der Flow die Nummer nach dem Anlegen des Datensatzes zurueckgibt, statt dass der Agent einen zweiten Connector-Aufruf macht.

---

## Aufgabe 2: Flow-Schnittstelle definieren

**Eingabeparameter des Agents an den Flow:**

| Parameter | Typ | Herkunft |
|---|---|---|
| ErsterKrankheitstag | Datum | Nutzereingabe im Agent |
| VoraussichtlichesDauer | Text | Nutzereingabe (oder "Unbekannt") |
| AttestVorhanden | Boolean | Nutzereingabe |
| NutzerSystemUserId | GUID | System-Variable des Agents (User.Id) |

**Rueckgabe des Flows an den Agent:**

| Rueckgabewert | Typ | Bedeutung |
|---|---|---|
| Erfolg | Boolean | True = Datensatz angelegt, False = Fehler |
| KrankmeldungsNummer | Text | Generierte Nummer (z.B. "KM-00042") |
| FehlerMeldung | Text | Wenn Fehler: Beschreibung |

**Dataverse-Aktionen im Flow:**
1. Neuen Datensatz in cr_Krankmeldung anlegen mit den uebergebenen Feldern
2. Nach dem Anlegen: ID des neuen Datensatzes lesen und Nummer-Feld zurueckgeben

**Weitere Aktionen im Flow:**
3. Vorgesetzten-E-Mail-Adresse aus dem SystemUser-Datensatz des Nutzers lesen (Lookup auf Manager-Feld)
4. E-Mail an Vorgesetzten senden mit: Mitarbeitername, Erster Krankheitstag, Antragsnummer

---

## Aufgabe 3: Fehlerbehandlung im Agent

**Variable nach Flow-Aufruf pruefen:** Der Flow gibt die Variable "Erfolg" (Boolean) zurueck.

**Im Erfolgsfall (Erfolg = True):**
- Agent zeigt Nachricht: "Ihre Krankmeldung wurde erfasst. Ihre Meldungsnummer lautet: [KrankmeldungsNummer]. Gute Besserung!"

**Im Fehlerfall (Erfolg = False):**
- Agent zeigt Nachricht: "Leider konnte Ihre Krankmeldung nicht gespeichert werden. Bitte wenden Sie sich an hr@firma.de oder ruf die HR-Hotline (030-12345) an."

**Konversationsknoten-Typ in Copilot Studio:** "Condition" (Bedingungsknoten). Nach dem Action-Knoten (Flow-Aufruf) wird ein Condition-Knoten eingefuegt, der prueft ob "Erfolg = true". Abhaengig vom Ergebnis verzweigt der Dialog.

---

## Aufgabe 4: Authentifizierung und Nutzeridentitaet

**Authentifizierungsmodus:** Microsoft Authentication (Azure AD / Entra ID). Dieser Modus muss in den Agent-Einstellungen unter "Security" konfiguriert werden. Nutzer muessen sich mit ihrem Microsoft-Account anmelden.

**System-Variable in Copilot Studio:** `System.User.DisplayName` enthaelt den Anzeigenamen des angemeldeten Nutzers (fuer die Begruessungsnachricht). `System.User.Id` enthaelt die Azure AD Object ID des Nutzers.

**Verwendung im Flow:** Die Nutzer-ID (System.User.Id) wird als Parameter an den Flow uebergeben. Im Flow kann damit per Dataverse-Lookup der SystemUser-Datensatz gefunden werden, der die Manager-Verknuepfung enthaelt. Damit kann die E-Mail-Adresse des direkten Vorgesetzten ermittelt werden, ohne dass der Nutzer sie selbst eingeben muss.
