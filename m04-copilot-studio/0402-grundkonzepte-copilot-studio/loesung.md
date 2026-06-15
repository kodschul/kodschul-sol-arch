# Loesung: Grundkonzepte in Copilot Studio verstehen

## Aufgabe 1: Topics identifizieren

**Topic 1: Urlaubsantrag stellen**
Trigger-Phrasen: "Ich moechte Urlaub beantragen", "Wie stelle ich einen Urlaubsantrag?", "Urlaub eintragen", "Freie Tage beantragen", "Jahresurlaub planen"
Beschreibung: Agent erklaert den Prozess (ueber Self-Service-Portal, E-Mail an Fuehrungskraft oder Canvas App), fragt nicht nach konkreten Daten da kein direktes System angebunden ist.
Action: Keine direkte Aktion, Link zum Urlaubsportal

**Topic 2: Urlaubsanspruch pruefen**
Trigger-Phrasen: "Wie viel Urlaub habe ich noch?", "Resturlaub", "Urlaubstage verbleibend", "Mein Urlaubskonto", "Urlaubsanspruch anzeigen"
Beschreibung: Agent kann keinen direkten Zugriff auf Gehaltsabrechnungssystem geben, erklaert wo der Nutzer diese Information findet.
Action: Link zum HRIS-Portal, Informationstext

**Topic 3: Krankmeldung erfassen**
Trigger-Phrasen: "Ich bin krank", "Krankmeldung", "Ich komme heute nicht", "Bin arbeitsunfaehig", "Arbeitsunfaehigkeitsbescheinigung einreichen"
Beschreibung: Sammelt Daten und loest Flow aus.
Action: Power Automate Flow (Krankmeldung in Dataverse)

**Topic 4: Benefits und Zusatzleistungen**
Trigger-Phrasen: "Was sind meine Benefits?", "Jobticket", "betriebliche Altersvorsorge", "Kantine Zuschuss", "Firmenwagen anfragen"
Beschreibung: Generative Antwort aus SharePoint-Dokumenten ueber Benefits-Paket.
Action: Keine, generative Knowledge-Source-Antwort

**Topic 5: Betriebsvereinbarungen und Richtlinien**
Trigger-Phrasen: "Regelung Homeoffice", "Betriebsvereinbarung", "Welche Regeln gelten fuer mich?", "Dresscode", "Gleitzeitregelung"
Beschreibung: Generative Antwort aus SharePoint-Richtlinien.
Action: Keine, generative Antwort mit Quellenangabe

**Topic 6: Agent weiss es nicht (Eskalation)**
Trigger-Phrasen: (wird vom Fallback-System-Topic ausgeloest, keine manuellen Phrasen noetig)
Beschreibung: Wenn kein Topic matched und keine Knowledge Source antwortet: Weiterleitung zu HR-Hotline oder Ticket-Erstellung.
Action: Optionaler Flow fuer HR-Anfrage-Ticket

---

## Aufgabe 2: Knowledge Sources konfigurieren

Verwendete Knowledge Source: SharePoint-Bibliothek "HR-Dokumente"

Einschraenkungen fuer den Nutzer kommunizieren:
- Antworten sind generativ und basieren auf den hochgeladenen Dokumenten. Wenn ein Dokument veraltet ist, koennen die Antworten veraltet sein.
- Der Agent kann keine rechtsverbindlichen Auskuenfte geben.
- Bei Unsicherheit sollte immer direkt HR kontaktiert werden.

System-Topic fuer Gehaltsauskunft: Das Fallback-Topic muss angepasst werden, sodass bei Fragen zu Gehalt, Lohn, Steuerklasse eine klare Antwort kommt: "Fuer Gehaltsauskuenfte wende dich bitte direkt an hr@firma.de oder ruf die HR-Hotline an."

---

## Aufgabe 3: Konversationspfad Krankmeldung

```
[Start: Topic "Krankmeldung erfassen" getriggert]
    |
    v
Nachricht: "Ich nehme Ihre Krankmeldung auf."
    |
    v
Frage: "Ab welchem Tag sind Sie krank?" -> Variable: ErsterTag (Datum)
    |
    v
Frage: "Wissen Sie schon, wie lange Sie fehlen werden?" -> Ja / Nein
    |---> Nein -> Variable: Dauer = "Unbekannt"
    |
    |---> Ja -> Frage: "Bis einschliesslich welchem Tag?" -> Variable: LetzterTag (Datum)
    |
    v
Frage: "Liegt ein aerztliches Attest vor?" -> Ja / Nein -> Variable: AttestVorhanden (Boolean)
    |
    v
Action: Power Automate Flow aufrufen
  Parameter: ErsterTag, Dauer/LetzterTag, AttestVorhanden, NutzerID (aus Kontext)
    |
    v
Nachricht: "Ihre Krankmeldung wurde erfasst. Gute Besserung!"
```

---

## Aufgabe 4: Grenzen des Agents kommunizieren

"Der Agent hat keinen Zugriff auf das Gehaltsabrechnungssystem, da dieses keine API-Schnittstelle fuer Copilot Studio bereitstellt. Solche sensiblen Personaldaten sollten auch architektonisch nicht ueber einen Chatbot ausgegeben werden, da keine sichere Nutzeridentifikation auf Chatbot-Ebene stattfindet. Alternativ koennen Mitarbeiter ihre Gehaltsauskuenfte direkt im HRIS-Mitarbeiterportal abrufen, das eine gesicherte Anmeldung erfordert."
