# Lab 5.2 - Uebung: Business Units und Hierarchien strukturiert aufbauen

## Szenario

Ein Versicherungsunternehmen mit 1.800 Mitarbeitern baut eine Power Platform-Loesung fuer die Schadensverwaltung. Die Organisationsstruktur ist wie folgt:

- **Zentrale** (Muenchen): IT, HR, Finanzen, Compliance
- **Region Nord** (Hamburg): Vertrieb Nord, Schaden Nord
- **Region Sued** (Stuttgart): Vertrieb Sued, Schaden Sued
- **Region West** (Koeln): Vertrieb West, Schaden West

**Datenanforderungen:**

- Schadenbearbeiter sehen nur Schaeden in ihrer Region
- Regionsleitern sehen alle Schaeden ihrer Region und aller Unterabteilungen
- Compliance-Mitarbeiter (Zentrale) sehen alle Schaeden im gesamten Unternehmen
- Vertriebsmitarbeiter sehen keine Schadensdaten (andere Abteilung, andere Daten)
- HR hat keinen Zugriff auf Schaden- oder Vertriebsdaten

---

## Aufgabe 1: BU-Hierarchie entwerfen (20 Minuten)

Zeichne oder beschreibe die BU-Hierarchie fuer dieses Unternehmen.

Begruende deine Entscheidungen:

- Wie viele BU-Ebenen brauchst du?
- Wo ziehst du BU-Grenzen zwischen Vertrieb und Schaden in einer Region?
- Wie loest du den Compliance-Zugriff auf alle Schaeden?

---

## Aufgabe 2: Zugriffstiefe zuordnen (15 Minuten)

Definiere fuer die folgenden Rollen, welche Zugriffstiefe auf die Tabelle "Schaden" noetig ist:

| Rolle                              | Erwarteter Datenzugriff | Benoetiate Zugriffstiefe |
| ---------------------------------- | ----------------------- | ------------------------ |
| Schadenbearbeiter Schaden Nord     |                         |                          |
| Regionsleiter Region Nord          |                         |                          |
| Compliance-Mitarbeiter Zentrale    |                         |                          |
| Vertriebsmitarbeiter Vertrieb Nord |                         |                          |

---

## Aufgabe 3: Grenzfall bewerten (15 Minuten)

Ein Regionsleiter wird befördert und wechselt zur Zentrale. Er soll kuenftig bundesweit Schaeden einsehen koennen.

Beschreibe:

1. Was aendert sich an seiner BU-Zugehoerigkeit?
2. Was passiert mit den Schadensdatensaetzen, die er bisher in Region Nord bearbeitet hat?
3. Welches Risiko entsteht, wenn nichts unternommen wird?
4. Welche Massnahmen empfiehlst du?
