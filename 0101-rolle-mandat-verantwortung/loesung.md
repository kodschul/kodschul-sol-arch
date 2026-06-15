# Lab 1.1 - Loesung: Rolle und Verantwortung des Solution Architects

## Aufgabe 1: Rollenklarheit schaffen

**Empfohlene Antwort:**
"Ich werde keinen Flow bauen, weil das nicht meine Rolle als Solution Architect ist und dem Projekt schaden wuerde. Wenn ich anfange zu entwickeln, verliert das Projekt seinen unabhaengigen Architekten. Ich wuerde dann Features bauen, statt sicherzustellen, dass alle Features auf einem soliden Fundament aufgebaut werden. Was ich stattdessen tun werde: Ich klaere zuerst die offenen Architekturprobleme und unterstuetze euch dabei, die Ressourcensituation zu loesen."

**Warum dieser Ansatz richtig ist:** Ein SA der entwickelt verliert die Distanz, die er braucht, um Architekturprobleme zu erkennen. Er kann nicht mehr unbefangen sagen, dass eine technische Entscheidung falsch war, wenn er selbst an ihr beteiligt war.

## Aufgabe 2: SA-Aufgaben nach Dringlichkeit

**Sofort:**
1. Sicherheitsrolle mit Vollzugriff in Produktiv deaktivieren. Begruendung: Aktives Sicherheitsrisiko, jeder Nutzer hat Vollzugriff auf alle Daten.
2. Entwicklungsstopp in Produktivumgebung anordnen. Begruendung: Jede neue Aenderung kann Produktivdaten beschaedigen.

**Diese Woche:**
3. Umgebungsstrategie definieren und Dev-Umgebung einrichten.
4. Datenmodell-Chaos klaeren: Eine offizielle Kundentabelle festlegen, Migrationsplan erstellen.
5. Alle Sicherheitsrollen pruefen und dokumentieren.

**Naechste zwei Wochen:**
6. Loesungsstruktur in Solutions ueberfuehren.
7. Governance-Regeln mit dem Team vereinbaren.
8. Architekturentscheidungen rueckwirkend dokumentieren.

## Aufgabe 3: Rollenabgrenzung

| Problem | SA-Aufgabe | Kuenftige Praevention |
|---|---|---|
| Keine Umgebungsstrategie | SA definiert Umgebungsstrategie in Woche 1 eines Projekts | SA-Kickoff mit Umgebungsplanung als Pflichtpunkt |
| Drei Kundentabellen | SA genehmigt neue Tabellen vorab und dokumentiert das Datenmodell | Architektur-Review vor jeder neuen Tabellenerstellung |
| Vollzugriff-Sicherheitsrolle | SA prueft alle Sicherheitsrollen vor Aktivierung | Security-Review als Teil des Deployment-Prozesses |

## Aufgabe 4: Eroeffnung im Team-Meeting

"Meine Aufgabe als Solution Architect ist es, dafuer zu sorgen, dass diese Loesung in einem Jahr noch funktioniert und von jemandem uebernommen werden kann, der heute noch nicht dabei ist. Ich werde keine Features entwickeln, aber ich werde jeden von euch dabei unterstuetzen, die richtigen Features auf die richtige Art zu bauen. Was ich als erstes tun werde, ist mir anzuschauen, was bisher entstanden ist, und gemeinsam mit euch zu klaeren, was wir anpassen muessen."

**Warum diese drei Saetze funktionieren:**
- Satz 1 erklaert den Zweck der Rolle ohne Hierarchie zu betonen.
- Satz 2 setzt die Erwartung, dass der SA nicht entwickelt, aber hilft.
- Satz 3 klaert den naechsten Schritt und zeigt Kooperationsbereitschaft.
