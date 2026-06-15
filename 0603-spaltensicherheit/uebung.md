# Lab 6.3 - Uebung: Spaltensicherheit fuer sensible Daten konfigurieren

## Szenario

Ein Handelsunternehmen betreibt eine Kundenverwaltung in Dataverse. Die Tabelle "Kunde" enthaelt unter anderem folgende Felder:

- Name, Adresse, Telefon (Standard-Kontaktdaten)
- Kreditlimit (Finanzdaten - nur fuer Kreditabteilung)
- Rabattstufe (nur fuer Vertriebsleiter und Einkauf)
- Bonitaetsscore (nur fuer Kreditabteilung und Geschaeftsfuehrung)
- Interne Notizen (alle Vertriebsmitarbeiter schreiben, aber nur Teamleiter lesen alle)
- IBAN (nur fuer Buchhaltung - lesen und schreiben)

**Nutzergruppen:** Vertriebsmitarbeiter, Vertriebsteamleiter, Kreditabteilung, Buchhaltung, Geschaeftsfuehrung

---

## Aufgabe 1: Spaltensicherheitsprofile entwerfen (20 Minuten)

Entscheide fuer jedes der sechs Felder:

1. Braucht es Spaltensicherheit? (Ja/Nein + Begruendung)
2. Wenn ja: Welche Nutzergruppen erhalten welches Recht (Lesen/Erstellen/Aktualisieren)?

Erstelle eine Uebersichtstabelle der Spaltensicherheitsprofile.

---

## Aufgabe 2: Deployment-Fallstrick (10 Minuten)

Das Entwicklungsteam hat die Spaltensicherheit fuer Kreditlimit und Bonitaetsscore konfiguriert. Die Konfiguration ist in der Dev-Umgebung korrekt. Bei der Migration in die Produktivumgebung beschwert sich die Kreditabteilung: "Wir sehen das Kreditlimit-Feld nicht mehr!"

Was ist wahrscheinlich passiert? Was haette beim Deployment geprueft werden muessen?

---

## Aufgabe 3: Formular-Sichtbarkeit vs. Spaltensicherheit bewerten (10 Minuten)

Der Entwickler sagt: "Ich blende das IBAN-Feld einfach im Formular aus fuer alle ausser der Buchhaltung. Das ist einfacher."

Erklaere dem Entwickler, warum das keine ausreichende Sicherheitsmassnahme ist. Nenne zwei konkrete Wege, wie ein Nutzer das Feld trotz Formular-Ausblendung sehen koennte.
