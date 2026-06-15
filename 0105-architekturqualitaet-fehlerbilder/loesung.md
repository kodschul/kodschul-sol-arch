# Lab 1.5 - Loesung: Architekturqualitaet bewerten

## Aufgabe 1: Well-Architected-Bewertung

| Saule | Bewertung | Begruendung |
|---|---|---|
| Reliability | 2/5 | Kein Fehlerhandling in Flows (Beobachtung 3). Bei Outlook-Ausfall bleiben Benachrichtigungen aus ohne Alarm. |
| Security | 2/5 | Sicherheitsrolle mit Vollzugriff fuer alle Nutzer ignoriert das Datentrennung-Prinzip (Beobachtung 2). Mitarbeiter koennen Antraege anderer Abteilungen lesen und aendern. |
| Operational Excellence | 1/5 | Kein Monitoring (Beobachtung 7), keine Solutions (Beobachtung 6), keine Umgebungsstrategie (Beobachtung 1), hardgecodete Konfiguration (Beobachtung 5). |
| Performance Efficiency | 3/5 | Nicht-delegierbarer Filter (Beobachtung 4) ist ein Problem, aber bei derzeit kleiner Datenmenge noch nicht spuerbar. Wird sich verschlechtern. |
| Experience Optimization | 3/5 | Keine expliziten Informationen dazu aus dem Szenario. Neutrale Bewertung. |

## Aufgabe 2: Fehlerbilder

| Beobachtung | Fehlerbild | Go-Live-Konsequenz |
|---|---|---|
| 1: Nur eine Umgebung | Alles in einer Umgebung | Entwickler koennen Produktivdaten beschaedigen. Kein Rollback moeglich. |
| 2: Vollzugriff-Sicherheitsrolle | Sicherheitsrollen als Nachgedanke | Datenschutzverletzung: Mitarbeiter sehen Antraege anderer Abteilungen. DSGVO-Risiko. |
| 3: Kein Fehlerhandling | Keine Fehlerbehandlung in Flows | Genehmigungs-E-Mails koennen verloren gehen ohne Alarm. Antraege bleiben unbemerkt offen. |
| 4: IsBlank()-Filter | Nicht-delegierbare Filter | Bei mehr als 500 Eintraegen werden Antraege nicht mehr angezeigt. Nutzer sehen unvollstaendige Daten. |
| 5: Hardgecodete URL | Hardgecodete Konfiguration | Wenn die API-URL aendert, muss der Flow manuell angepasst werden. Kein geordneter Deployment-Prozess moeglich. |
| 6: Keine Solutions | Solutions werden nicht genutzt | Kein geordnetes Deployment in eine neue Umgebung moeglich. Keine Versionshistorie. |
| 7: Kein Monitoring | Operational Excellence-Luecke | IT-Abteilung weiss nicht wann und ob Flows laufen. Probleme werden erst durch Nutzer-Beschwerden entdeckt. |

## Aufgabe 3: Go-Live-Empfehlung

**Empfehlung: Go-Live in zwei Wochen nicht empfohlen.**

Die Kombination aus Beobachtung 2 (DSGVO-Verletzung) und Beobachtung 3 (stille Fehler) sind Go-Live-Blocker.

**Vor Go-Live zwingend:**
1. Sicherheitsrolle korrigieren: Nutzer duerfen nur ihre eigenen Antraege sehen. Vorgesetzte nur Antraege ihrer direkten Mitarbeiter. Das ist eine DSGVO-Anforderung, kein Nice-to-have.
2. Fehlerhandling in den Benachrichtigungs-Flow einbauen: Mindestens eine E-Mail an HR bei Flow-Fehler.
3. IsBlank()-Filter durch eine delegierbare Alternative ersetzen, zum Beispiel Abfrage nur auf cr_AntragstellerID (delegierbar) mit nachgelagerten Filter in der App.

**Nach Go-Live nachliefern:**
- Umgebungsstrategie und Solution-Struktur
- Monitoring-Dashboard
- Umgebungsvariablen statt hardgecodeter URL

## Aufgabe 4: Prioritaetsmatrix

| Beobachtung | Kategorie | Begruendung |
|---|---|---|
| Beobachtung 2: Sicherheitsrolle | Vor Go-Live blocken | DSGVO-Verletzung, rechtliches Risiko |
| Beobachtung 3: Kein Fehlerhandling | Vor Go-Live blocken | Kernprozess kann still fehlschlagen |
| Beobachtung 4: IsBlank()-Filter | Vor Go-Live blocken | Datenverlust bei Wachstum |
| Beobachtung 1: Nur eine Umgebung | Vor Go-Live empfehlen | Hohes Risiko, aber kurzfristig durch Disziplin beherrschbar |
| Beobachtung 5: Hardgecodete URL | Nach Go-Live nachliefern | Kein aktives Risiko wenn URL stabil ist |
| Beobachtung 6: Keine Solutions | Nach Go-Live nachliefern | Kein Nutzer-Impact, aber ALM ist erschwert |
| Beobachtung 7: Kein Monitoring | Nach Go-Live nachliefern | Wichtig, aber kein Go-Live-Blocker |
