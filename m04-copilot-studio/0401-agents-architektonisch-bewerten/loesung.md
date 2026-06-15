# Loesung: Den Einsatz von Agents architektonisch bewerten

## Aufgabe 1: Agent oder kein Agent?

| Anforderung | Agent geeignet? | Modus | Alternativ |
|---|---|---|---|
| 1 IT-Helpdesk | Ja | Klassischer Topic-Agent mit Actions | Kein sinnvolles Alternativwerkzeug fuer dialogbasierte Erstberatung |
| 2 Kreditantrag | Nein | Kein Agent | Model-Driven App oder Canvas App mit Formular |
| 3 Wissensportal | Ja | Generativer Agent mit SharePoint als Knowledge Source | Statisches Intranet (schlechtere Nutzererfahrung) |
| 4 Kunden-FAQ | Ja, mit Vorsicht | Klassischer Agent mit kontrollierten Antworten oder generativ mit Guardrails | Statische FAQ-Seite (weniger interaktiv) |
| 5 Tagesabschluss | Nein | Kein Agent | Power Automate Scheduled Flow |

**Begruendungen:**

Anforderung 2: 23 Pflichtfelder, Validierungsregeln, PDF-Upload und Abhaengigkeiten zwischen Feldern machen einen Agent ungeeignet. Ein Agent kann nicht zuverlaessig 23 Felder abfragen und validieren. Eine strukturierte App ist hier effizienter und fehlerfreier.

Anforderung 5: Kein Mensch ist beteiligt, kein Dialog findet statt. Das ist ein klassischer Automatisierungsfall. Ein Agent waere hier voellig falsch am Platz. Ein Scheduled Flow erledigt das in wenigen Minuten.

---

## Aufgabe 2: Risikobewertung Kunden-FAQ

**Risiko 1: Halluzination bei Konditionsangaben**
Ein generativer Agent kann Zinssaetze oder Gebühren erfinden, wenn die Dokumente veraltet sind oder die Frage zu spezifisch ist.
Gegenmassnahme: Klare Disclaimers in den Agent-Antworten ("Diese Information dient nur als erste Orientierung, verbindliche Konditionen erhalten Sie von einem Berater"). Ausserdem regelmaessige Pruefung der Dokumente und Aktualisierung der Knowledge Sources.

**Risiko 2: Informationen aus falschen Dokumenten**
Bei 500 Dokumenten in SharePoint kann der Agent inhaltlich aehnliche aber inhaltlich falsche Dokumente als Quelle verwenden.
Gegenmassnahme: Knowledge Sources in Copilot Studio auf gepruefte Dokument-Ordner beschraenken. Nicht das gesamte SharePoint freigeben, sondern nur den Ordner "Kundenkommunikation genehmigt".

**Risiko 3: Datenschutz bei Nutzerfragen**
Externe Kunden koennen persoenliche Daten in ihre Fragen einbetten ("Ich habe Konto 12345 und..."). Diese Daten werden an das LLM gesendet.
Gegenmassnahme: Datenschutzhinweis vor dem Chat. Technisch: DLP-Richtlinien pruefen, Copilot Studio ist in der Microsoft Cloud und unterliegt EU-Datenschutzstandards, aber Nutzer sollten informiert werden.

---

## Aufgabe 3: Architekturskizze IT-Helpdesk

**Kanal:** Microsoft Teams (Mitarbeiter nutzen Teams bereits, kein neues Login noetig)

**Nutzer:** Alle Mitarbeiter der Bank (intern)

**Datenquellen:**
- SharePoint: IT-Handbuch, Anleitungen, FAQ-Dokumente
- Dataverse: Ticket-Tabelle (zum Anlegen neuer Tickets)

**Ablauf:**
```
Mitarbeiter sendet Nachricht in Teams
    -> Agent liest Anliegen
    -> Agent prueft Topics: Passwort-Reset, VPN, Drucker
    -> Wenn Topic gefunden: Schritt-fuer-Schritt Anleitung aus SharePoint
    -> Wenn Topic nicht gefunden: "Ich erstelle fuer Sie ein Ticket"
        -> Agent fragt: Titel des Problems, Beschreibung, Dringlichkeit
        -> Agent loest Power Automate Flow aus
            -> Flow legt Ticket in Dataverse cr_ITTicket an
            -> Flow sendet Bestaetigung an Mitarbeiter
```

**Ticket-Tabelle in Dataverse:** cr_ITTicket mit Feldern:
- cr_Titel (Single Line Text)
- cr_Beschreibung (Multi Line Text)
- cr_Dringlichkeit (Choice: Normal, Hoch, Kritisch)
- cr_ErstelltVon (Lookup auf SystemUser)
- cr_Status (Choice: Offen, In Bearbeitung, Geloest)
- cr_ErstelltAm (Date and Time, auto)
