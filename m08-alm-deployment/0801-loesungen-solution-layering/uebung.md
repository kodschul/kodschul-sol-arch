# Lab 8.1 - Uebung: Loesungen und Solution Layering sicher beherrschen

## Szenario

Ein Softwarehaus baut eine Power Platform-Loesung fuer einen Kunden. Das Projektteam besteht aus:

- Team A: Zustaendig fuer Datenmodell und Sicherheit
- Team B: Zustaendig fuer Canvas Apps
- Team C: Zustaendig fuer Power Automate Flows und Integrationen

Das Projekt hat drei Umgebungen: Dev, Test, Prod. Bisher existiert eine einzige Loesung namens "GesamtLoesung" mit allen 47 Komponenten.

---

## Aufgabe 1: Solution-Segmentierungsstrategie (25 Minuten)

Die "GesamtLoesung"-Strategie verursacht Probleme:

- Team B und C aendern gleichzeitig dieselbe Loesung und muessen ihre Aenderungen koordinieren
- Ein Hotfix auf einen Flow soll in Prod ohne die halbfertigen App-Aenderungen von Team B deployt werden
- Sicherheitsrollen sollen separat vom Rest deployt werden

Entwirf eine Solution-Segmentierungsstrategie. Beantworte:

1. Welche Loesungen schlaeust du vor (Name + Inhalt)?
2. In welcher Reihenfolge werden sie deployt?
3. Wie loest das die drei beschriebenen Probleme?

---

## Aufgabe 2: Managed vs. Unmanaged (15 Minuten)

Ein Entwickler bittet dich: "Ich muss schnell einen kleinen Fehler in der Produktivumgebung beheben. Kann ich die Managed Loesung dort direkt bearbeiten?"

Erklaere:

1. Warum das problematisch ist (technisch und architektonisch)
2. Was der korrekte Weg ist
3. In welchem Ausnahmefall eine direkte Aenderung in Prod akzeptierbar waere

---

## Aufgabe 3: Environment Variables und Connection References (10 Minuten)

Beim Deployment in die Test-Umgebung schlaegt der Import fehl mit der Meldung: "Connection Reference 'cr_SharePointMain' ist nicht verbunden."

Beschreibe:

1. Was ist eine Connection Reference und warum ist sie noetig?
2. Was muss vor dem Import in Test getan werden?
3. Wie automatisierst du das in einer Pipeline, damit es nicht manuell passieren muss?
