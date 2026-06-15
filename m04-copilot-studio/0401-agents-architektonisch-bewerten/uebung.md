# Uebung: Den Einsatz von Agents architektonisch bewerten

## Szenario

Du bist Solution Architect bei einer mittelstaendischen Bank. Das Management hat entschieden, "Chatbots" in verschiedenen Bereichen einzufuehren und moechte von dir eine Bewertung, wo Agents sinnvoll sind und wo nicht.

Die folgenden fuenf Anforderungen wurden gemeldet:

---

**Anforderung 1: IT-Helpdesk**
Mitarbeiter sollen bei IT-Problemen zuerst einen Agent fragen, bevor sie ein Ticket erstellen. Der Agent soll haeufige Probleme (Passwort zuruecksetzen, VPN-Verbindungsprobleme, Drucker einrichten) selbststaendig loesen oder erklaeren. Wenn er das Problem nicht loesen kann, soll er automatisch ein Ticket in Dataverse anlegen.

**Anforderung 2: Kreditantrag Privatkundenberatung**
Kundenberater sollen waehrend des Kundengespraechs einen Kreditantrag erfassen. Dazu muessen Name, Geburtsdatum, Einkommensnachweis (als PDF), Beschaeftigungsverhaeltnis, Wohnform und Kreditbetrag eingegeben werden. Es gibt 23 Pflichtfelder und Validierungsregeln (z.B. Kreditbetrag haengt von Einkommen ab).

**Anforderung 3: Wissensportal fuer Mitarbeiter**
Mitarbeiter sollen Fragen zu internen Richtlinien, Compliance-Vorgaben und Prozessen stellen koennen. Die Richtlinien liegen als PDFs und Word-Dokumente in SharePoint. Es gibt ca. 500 Dokumente. Die Dokumente aendern sich alle paar Monate.

**Anforderung 4: Kunden-FAQ auf der Bankenwebsite**
Externe Kunden sollen auf der Bank-Website Fragen zu Produkten stellen koennen (Zinsen, Konditionen, Prozesse). Die Antworten sollen kontrollierbar sein, da falsche Informationen zu Kundenbeschwerden fuehren koennen.

**Anforderung 5: Tagesabschluss-Bericht**
Jeden Abend um 22:00 Uhr soll automatisch ein Bericht ueber alle neu erstellten Konten des Tages generiert und per E-Mail an die Teamleiter gesendet werden. Kein Mensch ist in diesem Prozess involviert.

---

## Aufgabe 1: Agent oder kein Agent?

Bewerte jede der fuenf Anforderungen. Erstelle eine Tabelle:

| Anforderung | Agent geeignet? | Wenn ja: welcher Modus? | Alternativwerkzeug wenn kein Agent |
|---|---|---|---|
| 1 | ? | ? | ? |
| 2 | ? | ? | ? |
| 3 | ? | ? | ? |
| 4 | ? | ? | ? |
| 5 | ? | ? | ? |

---

## Aufgabe 2: Risikobewertung

Fuer Anforderung 4 (Kunden-FAQ) wurde entschieden, dass ein Agent mit generativer KI eingesetzt wird. Der Agent soll die Dokumente aus SharePoint nutzen und Antworten generieren.

Formuliere mindestens drei konkrete Risiken, die du als SA in dieser Entscheidung siehst, und schlage fuer jedes Risiko eine Gegenmassnahme vor.

---

## Aufgabe 3: Architektur skizzieren

Fuer Anforderung 1 (IT-Helpdesk) sollst du eine Architekturskizze erstellen.

Beantworte folgende Fragen und zeichne einen einfachen Ablaufplan:
- Wo wird der Agent deployed (Kanal)?
- Wer sind die Nutzer (intern)?
- Welche Datenquellen benoetigt der Agent?
- Was passiert, wenn der Agent das Problem nicht loest?
- Wo entsteht das Ticket und in welcher Tabelle?

---

## Hinweis

Es gibt keine Einheitsantwort fuer alle Anforderungen. Das Ziel ist, die Entscheidungslogik zu verstehen: Wann leistet ein Agent Mehrwert, wann ist er das falsche Werkzeug?
