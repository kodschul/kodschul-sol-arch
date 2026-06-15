# Lab 5.4 - Uebung: Row-Level-Zugriff, Besitz und Sharing bewerten

## Szenario

Ein Energieversorger betreibt eine Power Platform-Loesung zur Verwaltung von Kundenvertraegen und Netzstoerungen. Die Datenlage ist wie folgt:

- Vertriebsmitarbeiter besitzen ihre Kundenvertraege (User-owned)
- Netztechniker bearbeiten Stoerungsmeldungen (User-owned)
- Wenn eine Stoerung einen Grosskunden betrifft, soll der zustaendige Vertriebsmitarbeiter diese Stoerung ebenfalls sehen koennen
- Das Unternehmen hat keine klare Team-Strategie: Alles laeuft auf einzelne Nutzer
- Es gibt drei Vertriebsleiter, die alle Vertraege ihrer Mitarbeiter sehen muessen

---

## Aufgabe 1: Eigentuemerstrategie bewerten (15 Minuten)

Das Unternehmen hat bisher alle Datensaetze auf einzelne Nutzer ausgestellt. Ein Mitarbeiter ist kuerzlich ausgeschieden, und seine 340 Kundenvertraege sind nun ohne aktiven Eigentuemer.

Beschreibe:

1. Was ist das konkrete Problem mit dieser Situation in Dataverse?
2. Welche Team-Strategie empfiehlst du als SA fuer die Zukunft?
3. Wie migrierst du die 340 bestehenden Datensaetze?

---

## Aufgabe 2: Sharing-Szenario entwerfen (20 Minuten)

Das Szenario: Wenn eine Stoerungsmeldung eine Klinik mit aktivem Vertrag betrifft, soll der zustaendige Vertriebsmitarbeiter die Stoerungsmeldung (nicht alle Stoerungen) lesen koennen.

Entscheide und begruende:

1. Soll das per Sharing oder per Rollenarchitektur geloest werden?
2. Wenn Sharing: Wie wird der richtige Vertriebsmitarbeiter identifiziert und wie wird der Zugriff vergeben?
3. Wenn Rollen: Welche Zugriffstiefe waere noetig und welches Problem entsteht dabei?

---

## Aufgabe 3: Vertriebsleiter-Zugriff konzipieren (15 Minuten)

Drei Vertriebsleiter sollen alle Vertraege ihrer Mitarbeiter sehen. Die Mitarbeiter-Manager-Beziehung ist im Active Directory gepflegt und wird in Dataverse synchronisiert.

Vergleiche zwei Ansaetze:

- **Ansatz A:** BU-Hierarchie mit "Parent:Child"-Tiefe auf die Vertraege-Rolle
- **Ansatz B:** Hierarchiesicherheit aktivieren

Welchen Ansatz empfiehlst du? Begruende mit Pro/Contra.
