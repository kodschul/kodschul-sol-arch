# Uebung: Speicher- und Kapazitaetsauswirkungen architektonisch beruecksichtigen

## Szenario

Du wirst als Solution Architect fuer ein Versicherungsunternehmen beauftragt. Das Unternehmen moechte einen Schadensmeldungsprozess in der Power Platform abbilden. Die fachlichen Informationen:

- Pro Jahr werden ca. 80.000 Schadensfaelle geoeffnet
- Jeder Schadensfall hat einen Beschreibungstext (kann sehr lang sein, oft mehrseitige Berichte)
- Zu jedem Schadensfall werden mehrere Fotos hochgeladen (durchschnittlich 8 Fotos a 3 MB)
- Jeder Schadensfall geht durch mehrere Statusaenderungen (im Durchschnitt 12 Statusaenderungen pro Fall)
- Alle Statusaenderungen muessen pruefbar bleiben (Compliance-Anforderung)
- Faelle sollen 10 Jahre lang aufbewahrt werden
- Das Unternehmen hat 200 Nutzer mit Power Apps Premium Lizenz

---

## Aufgabe 1: Speicherbedarf abschaetzen

Berechne den Gesamtspeicherbedarf fuer 10 Jahre, aufgeteilt nach:
- Database Storage (Tabellendaten)
- File Storage (Fotos)
- Log Storage (Audit durch Statusaenderungen)

Nutze folgende Annahmen:
- Ein Datensatz ohne Anhange und ohne lange Texte: ca. 5 KB
- Ein langer Beschreibungstext: ca. 50 KB
- Ein Foto: 3 MB
- Ein Audit-Log-Eintrag (eine Feldaenderung): ca. 2 KB

---

## Aufgabe 2: Kontingent pruefen

Das Unternehmen hat 200 Power Apps Premium Nutzer (je 250 MB Database, 2 GB File Storage).

- Wie viel Database Storage steht insgesamt zur Verfuegung?
- Wie viel File Storage steht insgesamt zur Verfuegung?
- Reicht das Kontingent fuer 10 Jahre Betrieb? Falls nicht: wo ist der Engpass?

---

## Aufgabe 3: Loesungsstrategien

Du hast erkannt, dass das Kontingent nicht ausreicht. Entwickle mindestens zwei konkrete Strategien, mit denen du den Speicherverbrauch reduzieren oder das Problem umgehen kannst.

Beschreibe fuer jede Strategie:
- Was wird veraendert?
- Welche Auswirkungen hat das auf das Datenmodell?
- Welche Vorteile entstehen?
- Welche neuen Komplexitaeten entstehen?

---

## Aufgabe 4: Audit-Anforderung praeziisieren

Die Compliance-Anforderung lautet: "Alle Statusaenderungen muessen pruefbar bleiben."

Diskutiere:
- Muss dafuer zwingend der Dataverse-eigene Audit-Mechanismus verwendet werden?
- Waere eine eigene Status-Historie-Tabelle (die jede Statusaenderung als Datensatz speichert) eine Alternative?
- Welche Vor- und Nachteile hat jede Variante?

---

## Hinweis zur Bearbeitung

Die Berechnungen muessen nicht auf den Byte genau stimmen. Es geht darum, Groessenordnungen zu verstehen und Engpaesse fruehzeitig zu erkennen. Runde bei Bedarf auf ganze Zahlen.
