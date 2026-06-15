# Uebung: Grundkonzepte in Copilot Studio verstehen

## Szenario

Du planst einen internen HR-Agent fuer ein Unternehmen mit 1.200 Mitarbeitern. Der Agent soll ueber Microsoft Teams verfuegbar sein. Er soll Mitarbeitern helfen bei:

- Fragen zu Urlaubsanspruch und Urlaubsantragsprozess
- Informationen zu Benefits (Jobticket, Kantine, betriebliche Altersvorsorge)
- Erfassen von Krankmeldungen (weiterleiten an HR-System)
- Allgemeinen HR-Richtlinien und Betriebsvereinbarungen

Folgende Materialien stehen zur Verfuegung:
- SharePoint-Bibliothek "HR-Dokumente" mit 45 PDFs und Word-Dokumenten
- Power Automate Flow, der Krankmeldungen in Dataverse speichert
- Kein Zugriff auf das Gehaltsabrechnungssystem (separates System, keine API)

---

## Aufgabe 1: Topics identifizieren

Identifiziere mindestens sechs Topics fuer diesen Agent. Definiere fuer jedes Topic:
- Topic-Name
- Mindestens 5 Trigger-Phrasen
- Kurze Beschreibung: Was soll das Topic tun?
- Braucht das Topic eine Action? Falls ja, welche?

---

## Aufgabe 2: Knowledge Sources konfigurieren

Der Agent soll Fragen zu HR-Richtlinien mit generativen Antworten beantworten.

Beschreibe:
- Welche Knowledge Source wird verwendet?
- Welche Einschraenkungen musst du dem Agent-Nutzer kommunizieren?
- Welches System-Topic muss angepasst werden, wenn ein Nutzer nach Gehaltsauskunft fragt (das der Agent nicht beantworten kann)?

---

## Aufgabe 3: Konversationspfad entwerfen

Entwirf den Konversationspfad fuer das Topic "Krankmeldung erfassen" als Ablaufdiagramm. Der Pfad muss folgende Informationen vom Nutzer erfassen:
- Erster Krankheitstag
- Voraussichtliche Dauer (wenn bekannt)
- Ob ein Attest vorliegt

Am Ende soll ein Power Automate Flow aufgerufen werden, der die Krankmeldung in Dataverse speichert.

Zeichne den Pfad mit Entscheidungsknoten, Fragen und dem Flow-Aufruf.

---

## Aufgabe 4: Grenzen des Agents kommunizieren

Ein HR-Mitarbeiter fragt: "Kann der Agent auch Gehaltsauskuenfte geben?"

Formuliere eine knappe, fachlich korrekte Antwort (2 bis 3 Saetze) die du als SA gegenueber dem HR-Mitarbeiter geben wuerdest. Erklaere warum das nicht moeglich ist und was alternativ moeglich waere.
