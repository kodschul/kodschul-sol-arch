# Lab 5.1 - Uebung: Umgebungen als erste Sicherheitsgrenze verstehen

## Szenario

Du bist Solution Architect bei einem deutschen Maschinenbauunternehmen mit 4.200 Mitarbeitern. Der Tenant hat bisher zwei Umgebungen: die Default-Umgebung und eine Produktivumgebung namens "PP-Prod". In der Default-Umgebung existieren bereits 23 Canvas Apps, die von verschiedenen Abteilungen selbst gebaut wurden. Einige dieser Apps greifen auf SharePoint-Daten zu, andere haben Dataverse-Tabellen direkt in der Default-Umgebung angelegt.

Die IT-Abteilung stellt fest, dass ein Mitarbeiter aus der Marketingabteilung versehentlich Zugriff auf eine HR-App hat, die in der Default-Umgebung laeuft. Er kann alle Personaldaten einsehen.

---

## Aufgabe 1: Risikoanalyse der aktuellen Umgebungsstruktur (20 Minuten)

Beschreibe systematisch, welche Sicherheitsrisiken durch die aktuelle Umgebungsstruktur entstehen. Gehe dabei auf folgende Punkte ein:

1. Warum ist die Default-Umgebung strukturell problematisch?
2. Welche Daten sind in welchem Zustand gefaehrdet?
3. Was ist das konkrete Risiko durch die 23 Apps in der Default-Umgebung?

---

## Aufgabe 2: Ziel-Umgebungsarchitektur entwerfen (25 Minuten)

Entwirf eine Umgebungsarchitektur fuer das Unternehmen. Beruecksichtige:

- Die HR-Anwendung verarbeitet besonders schutzwuerdige Personaldaten (DSGVO-relevant)
- Die Marketingabteilung baut regelmaessig eigene Apps (hohes Maker-Volumen)
- Es gibt ein zentrales ERP-Integrationsprojekt, das sich gerade in Entwicklung befindet
- Das Unternehmen hat noch keine Pipelines, migriert aber bald auf PP Pipelines

Erstelle eine Tabelle mit den vorgeschlagenen Umgebungen (Name, Typ, Zweck, Wer hat Zugang).

---

## Aufgabe 3: Migrationsrisiken bewerten (15 Minuten)

Die IT-Leitung fragt, ob die 23 Apps aus der Default-Umgebung einfach in neue Umgebungen verschoben werden koennen.

Beantworte diese Frage strukturiert:

- Was koennen und was koennen sie nicht?
- Welche Abhaengigkeiten muss man pruefen?
- Was ist die empfohlene Vorgehensweise?
