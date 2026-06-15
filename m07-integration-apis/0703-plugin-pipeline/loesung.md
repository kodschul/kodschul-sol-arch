# Lab 7.3 - Loesung: Plug-In-Pipeline und Ausfuehrungsmodelle verstehen

## Aufgabe 1: Plugin oder Flow?

| Anforderung                  | Loesung                             | Begruendung                                                                                                                                                                                     |
| ---------------------------- | ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1 - Bonitaetsscore abrufen   | Asynchroner Flow (nach Speicherung) | Externer HTTP-Aufruf, nicht transaktional, 200-800ms - kein synchrones Plugin (Anti-Pattern). Flow mit HTTP-Aktion, aktualisiert Datensatz nach Antwort                                         |
| 2 - Antragsnummer generieren | Plugin (synchron, Pre-Operation)    | Atomare Nummerngenerierung mit Zaehler-Tabelle erfordert Transaktionssicherheit. Plugin liest Zaehler und schreibt Antragsnummer innerhalb derselben Transaktion - kein Race Condition moeglich |
| 3 - E-Mail bei Ablehnung     | Asynchroner Flow (Post-Operation)   | E-Mail-Versand ist keine transaktionale Operation. Bei Fehler soll nicht die Statusaenderung zurueckgerollt werden. Flow ist einfacher zu warten, Fehlerbehandlung eingebaut                    |
| 4 - Betrag-Validierung       | Plugin (synchron, Pre-Validation)   | Abbrechen einer Operation vor der Speicherung = Pre-Validation Plugin. Plugin wirft InvalidPluginExecutionException mit der Fehlermeldung, die dem Nutzer angezeigt wird                        |

---

## Aufgabe 2: Pipeline-Phase

**Anforderung 2 - Antragsnummer (Plugin):**

- Phase: Pre-Operation (innerhalb der Transaktion, vor finaler Speicherung)
- Synchron: Ja (muss vor dem Speichern fertig sein)
- Images: Pre-Image nicht noetig (neuer Datensatz). Post-Image nicht noetig (wir schreiben selbst ins InputParameters-Feld).
- Logik: Plugin liest den Zaehler-Datensatz (mit pessimistischem Lock), erhoeht den Wert, setzt die Antragsnummer im InputParameter-Feld des Execution Context.

**Anforderung 4 - Betrag-Validierung (Plugin):**

- Phase: Pre-Validation (vor Datenbankzugriff, kann noch abbrechen)
- Synchron: Ja
- Images: Kein Image noetig - der Betrag ist im InputParameters enthalten (der Wert, der gespeichert werden soll).
- Logik: Plugin liest den Betrag aus InputParameters["Target"], prueft ob > 100.000, wirft bei Ueberschreitung eine InvalidPluginExecutionException.

---

## Aufgabe 3: Anti-Pattern Bonitaetsscore

**Warum das ein Anti-Pattern ist:**

**Problem 1: Latenz blockiert den Nutzer**
Ein synchrones Plugin laeuft innerhalb der Transaktionsverarbeitung. 200-800ms Wartezeit bedeutet: Der Nutzer klickt "Speichern" und wartet 0,2-0,8 Sekunden - im besten Fall. Bei Spitzenlasten des Bonitaetsdienstes kann das auf mehrere Sekunden ansteigen. Das ist eine schlechte User Experience.

**Problem 2: Externe Abhaengigkeit blockiert das System**
Wenn der Bonitaetsdienst nicht antwortet (Timeout), laeuft das Plugin in ein Timeout. Das Dataverse-Limit fuer synchrone Plugin-Ausfuehrung ist 2 Minuten. Waehrend dieser Zeit haengt die Speicheroperation. Der Nutzer sieht einen Fehler.

**Problem 3: Kein Retry bei Fehler**
Wenn der Bonitaetsdienst einen Fehler zurueckgibt, wirft das Plugin eine Exception, die die gesamte Speicheroperation abbricht. Der Kreditantrag wird nicht gespeichert - obwohl das Ziel war, ihn zu speichern und zusaetzlich einen Score abzurufen.

**Problem 4: Sandbox-Einschraenkungen**
Plugins laufen im Sandbox-Modus. Externe HTTP-Aufrufe sind technisch moeglich (IOrganizationService hat keine Netzwerkbeschraenkung, aber direkte HTTP-Clients haben sandbox-seitige Einschraenkungen je nach Konfiguration).

**Bessere Loesung:**

1. Kreditantrag wird normal gespeichert (kein Plugin fuer den Score)
2. Ein asynchroner Power Automate Flow wird durch das Erstellen des Datensatzes ausgeloest
3. Der Flow ruft den Bonitaetsdienst auf (HTTP-Aktion)
4. Bei Erfolg: Flow aktualisiert den Datensatz mit dem Score
5. Bei Fehler: Flow sendet Benachrichtigung an Sachbearbeiter, Retry-Logik konfigurieren

Optional: Status-Feld "Score wird geladen" auf dem Datensatz, das der Flow auf "Verfuegbar" setzt. Nutzer sehen, dass der Score noch nicht vorhanden ist.
