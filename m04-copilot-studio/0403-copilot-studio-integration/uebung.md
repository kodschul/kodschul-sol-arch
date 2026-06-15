# Uebung: Copilot Studio in die Loesungsarchitektur integrieren

## Szenario

Du erweiterst den HR-Agent aus Lab 4.2. Jetzt soll der Agent tatsaechlich mit Dataverse und Power Automate integriert werden. Die folgenden neuen Anforderungen liegen vor:

- Der Agent soll bei Krankmeldungen den Datensatz direkt in Dataverse in der Tabelle "cr_Krankmeldung" speichern
- Nach der Speicherung soll der direkte Vorgesetzte eine E-Mail-Benachrichtigung erhalten
- Der Agent soll dem Nutzer nach Speicherung die Antragsnummer (cr_KrankmeldungNummer) zurueckgeben
- Wenn die Speicherung fehlschlaegt, soll der Agent eine Fehlermeldung anzeigen
- Der Agent soll nur von angemeldeten Mitarbeitern verwendet werden koennen (nicht anonym)

---

## Aufgabe 1: Integrationsarchitektur planen

Entscheide: Soll der Agent die Krankmeldung per direktem Dataverse-Connector speichern, oder soll ein Power Automate Flow als Vermittler dienen?

Begruende deine Entscheidung anhand der Anforderungen. Welche Anforderung gibt den Ausschlag?

---

## Aufgabe 2: Flow-Schnittstelle definieren

Wenn du dich fuer einen Flow als Vermittler entschieden hast, definiere die Schnittstelle:

- Welche Eingabeparameter uebergibt der Agent an den Flow?
- Was gibt der Flow zurueck? (Erfolg/Fehler, Antragsnummer)
- Welche Dataverse-Aktionen fuehrt der Flow durch?
- Welche weiteren Aktionen (E-Mail etc.) fuehrt der Flow durch?

Erstelle eine Tabelle oder einen strukturierten Ablaufplan.

---

## Aufgabe 3: Fehlerbehandlung im Agent

Beschreibe, wie der Agent mit einem Flow-Fehler umgehen soll. Konkret:
- Welche Variable prueft der Agent nach dem Flow-Aufruf?
- Was passiert im Erfolgsfall?
- Was passiert im Fehlerfall?
- Welcher Konversationsknoten-Typ wird in Copilot Studio verwendet, um zwischen Erfolg und Fehler zu unterscheiden?

---

## Aufgabe 4: Authentifizierung und Nutzeridentitaet

Der Agent soll den Vornamen des Nutzers in der Begruessungsnachricht verwenden ("Guten Tag, [Vorname]. Wie kann ich Ihnen helfen?"). Der Agent soll ausserdem die Nutzer-ID automatisch an den Flow uebergeben, ohne den Nutzer zu fragen.

Beantworte:
- Welcher Authentifizierungsmodus muss konfiguriert werden?
- Wie heisst die System-Variable in Copilot Studio, die den angemeldeten Nutzer enthaelt?
- Wo wird die Nutzer-ID im Flow fuer die Dataverse-Operation verwendet?

---

## Hinweis

Die Aufgaben sind konzeptionell. Du brauchst keinen Zugriff auf ein Copilot Studio Environment. Das Ziel ist, die Integrationsarchitektur sauber zu denken und zu dokumentieren, bevor mit dem Bau begonnen wird.
