# Lab 2.4 - Uebung: Limit-bewusstes Architekturdesign

## Szenario

Ein E-Commerce-Unternehmen moechte eine Power Platform-Loesung fuer die Auftragsverarbeitung. Die Anforderungen:

- 5.000 Bestellungen pro Tag, davon 20% (1.000) in den Spitzenstunden 8:00 bis 10:00 Uhr.
- Pro Bestellung: Kundendaten abfragen, Lagerbestand pruefen, Bestellung in Dataverse speichern, Bestaetigung an Kunden senden, Lagersystem aktualisieren.
- Kunden laden haeufig Fotos von beschaedigten Paketen hoch (Reklamationen), durchschnittlich 2 MB pro Bild, 200 Reklamationen pro Monat.
- Monatlich werden 100.000 Produktdatensaetze von einem CSV-Import aus dem ERP-System aktualisiert.
- Eine Canvas App zeigt Auftragsbearbeitern offene Bestellungen. Es gibt 3.000 offene Bestellungen gleichzeitig.

## Aufgabe 1: Limit-Risiken identifizieren (20 Minuten)

Analysiere alle fuenf Anforderungen auf moegliche Plattformlimits. Fuehre eine Risikotabelle mit folgenden Spalten:

| Anforderung | Moegliches Limit | Risikobewertung (hoch/mittel/niedrig) |

## Aufgabe 2: Batch-Design fuer den CSV-Import (20 Minuten)

Der monatliche Import aktualisiert 100.000 Produktdatensaetze. Entwerfe das Flow-Design:
- Wie wird der Import in handhabbare Stuecke aufgeteilt?
- Wie werden API-Limits respektiert?
- Was passiert wenn ein Teil des Imports fehlschlaegt?
- Wie lange darf der Import maximal dauern?

Zeichne ein Ablaufdiagramm (Mermaid oder Text).

## Aufgabe 3: Datei-Strategie fuer Reklamationsfotos (15 Minuten)

Entscheide, ob Reklamationsfotos in Dataverse oder SharePoint gespeichert werden sollen. Berechne den Speicherverbrauch fuer beide Optionen nach zwei Jahren und empfehle eine Loesung.

## Aufgabe 4: Canvas App Performance-Design (15 Minuten)

Die Canvas App zeigt offene Bestellungen. Die aktuelle Implementierung ladet alle 3.000 offenen Bestellungen beim Start der App. Das dauert 8 Sekunden.

Beschreibe mindestens zwei Massnahmen, mit denen du die Ladezeit unter 2 Sekunden bringen wuerdest.
