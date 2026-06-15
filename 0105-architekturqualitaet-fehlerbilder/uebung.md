# Lab 1.5 - Uebung: Architekturqualitaet bewerten

## Szenario

Das Urlaubsantragsprojekt ist nach vier Monaten Entwicklung im Abnahmetest. Vor dem Go-Live fuehrst du als SA ein Architektur-Review durch. Dir liegen folgende Beobachtungen vor:

**Beobachtung 1:** Die Produktivumgebung wird auch fuer Entwicklung und Test verwendet. Es gibt nur diese eine Umgebung.

**Beobachtung 2:** Alle Nutzer haben die Sicherheitsrolle "Basic User" plus eine neue Rolle "Urlaubsverwaltung Full Access", die auf alle Datensaetze in der cr_UrlaubsAntrag-Tabelle Vollzugriff gibt, unabhaengig von Abteilung oder Hierarchie.

**Beobachtung 3:** Der Benachrichtigungs-Flow hat keine Fehlerbehandlung. Wenn Outlook nicht erreichbar ist, schlaegt der Flow still fehl.

**Beobachtung 4:** In der Canvas App wird folgender Filter verwendet: Filter(cr_UrlaubsAntrag, IsBlank(cr_Kommentar) = false && cr_AntragstellerID = User().Email)

**Beobachtung 5:** Die URL der externen Urlaubskalender-API ist direkt im Flow hardgecodiert: https://api.intern.firma.de/kalender

**Beobachtung 6:** Alle Komponenten wurden direkt in der Umgebung erstellt, ohne Solution.

**Beobachtung 7:** Es gibt kein Monitoring-Dashboard. Die IT-Abteilung weiss nicht, ob Flows laufen oder fehlschlagen.

## Aufgabe 1: Well-Architected-Bewertung (25 Minuten)

Bewerte die Loesung anhand der fuenf Saeulen des Well-Architected Frameworks. Vergib fuer jede Saule eine Bewertung von 1 (kritisch) bis 5 (gut) und begruende sie mit konkreten Beobachtungen aus dem Szenario.

## Aufgabe 2: Fehlerbilder identifizieren (15 Minuten)

Ordne jeder der sieben Beobachtungen das passende Fehlerbild aus der Theorie zu und erklaere, welche Konsequenz dieses Fehlerbild bei Go-Live haben wird.

## Aufgabe 3: Go-Live-Empfehlung (20 Minuten)

Auf Basis deiner Analyse: Empfiehlst du Go-Live in zwei Wochen, oder nicht?

Wenn ja: Welche Risiken traegt der Auftraggeber bewusst?
Wenn nein: Was sind die drei dringendsten Korrekturen, die vor Go-Live zwingend notwendig sind, und was kann danach nachgeliefert werden?

## Aufgabe 4: Prioritaetsmatrix (10 Minuten)

Erstelle eine Matrix mit allen sieben Beobachtungen. Ordne jede in eine von vier Kategorien:
- Vor Go-Live blocken (muss korrigiert werden)
- Vor Go-Live empfehlen (sollte korrigiert werden)
- Nach Go-Live nachliefern (kann warten)
- Akzeptiertes Risiko (kann dauerhaft so bleiben)
