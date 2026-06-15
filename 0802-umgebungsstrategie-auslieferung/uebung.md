# Lab 8.2 - Uebung: Umgebungsstrategie fuer professionelle Auslieferung aufbauen

## Szenario

Ein Unternehmensberatung entwickelt fuer einen Kunden eine Power Platform-Loesung. Der Kunde hat folgende Situation:

- 5 Entwickler (externe Berater + 2 interne IT-Mitarbeiter)
- Strenge DSGVO-Anforderungen: Personaldaten und Kundendaten in der Loesung
- Fachbereich (ca. 30 Personen) soll regelmaessig neue Features testen koennen
- Aktuell: Alle entwickeln in der Produktivumgebung, weil "schneller"
- Erstes ernsthaftes Release in 6 Wochen

---

## Aufgabe 1: Ziel-Umgebungsarchitektur (25 Minuten)

Entwirf die Ziel-Umgebungsarchitektur fuer das Projekt. Deine Antwort soll enthalten:

1. Welche Umgebungen (Name, Typ, Zweck)
2. Wer hat Zugang zu welcher Umgebung (und mit welchen Rechten)
3. Wie werden Personaldaten und Kundendaten im Test sicher behandelt?
4. Was ist der minimale erste Schritt, der in den naechsten 5 Arbeitstagen umgesetzt werden muss?

---

## Aufgabe 2: Datenisolationsproblem loesen (15 Minuten)

Der Projektmanager sagt: "Fuer den UAT-Test brauchen wir echte Kundendaten, sonst meckert der Fachbereich, dass die Tests nicht realistisch sind."

Formuliere eine Antwort, die:

1. Das DSGVO-Risiko erklaert
2. Eine konkrete Alternative nennt
3. Den Fachbereich mitnimmt (nicht nur "das geht nicht")

---

## Aufgabe 3: Aktueller Zustand: Alle in Prod (15 Minuten)

Das Projekt laeuft seit 3 Monaten, alle entwickeln in Prod. Es gibt keine Loesungsstruktur.

Beschreibe einen realistischen Migrationsplan:

1. Wie kommt man aus diesem Zustand heraus, ohne den laufenden Betrieb zu gefaehrden?
2. In welcher Reihenfolge werden welche Schritte gemacht?
3. Was sind die groessten Risiken bei der Migration?
