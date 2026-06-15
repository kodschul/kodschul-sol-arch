# Lab 2.5 - Uebung: Erweiterungsoptionen auswaehlen

## Aufgabe 1: Erweiterungs-Klassifikation (25 Minuten)

Ordne die folgenden Anforderungen der richtigen Erweiterungsebene zu. Begruende jede Entscheidung.

Die Ebenen sind: Konfiguration, Business Rule / Formelspalte, Power Automate Flow, Plugin, Custom Connector / Azure Function.

| Nr. | Anforderung |
|---|---|
| 1 | Das Feld "Lieferdatum" wird beim Speichern automatisch auf Bestelldatum + 5 Werktage gesetzt. |
| 2 | Wenn ein Auftrag auf Status "Abgeschlossen" gesetzt wird, soll der Kundensaldo in SAP aktualisiert werden. |
| 3 | Beim Erstellen eines Kontakts soll geprueft werden, ob bereits ein Kontakt mit der gleichen E-Mail existiert. Wenn ja, soll das Speichern verhindert und eine Fehlermeldung angezeigt werden. |
| 4 | Ein Formular soll das Feld "Steuer-ID" nur anzeigen wenn der Kundentyp "Geschaeftskunde" ist. |
| 5 | Naechtlich sollen alle Kunden, die seit 12 Monaten keine Bestellung hatten, auf Status "Inaktiv" gesetzt werden. |
| 6 | Eine externe Bonitaets-API (Creditreform) soll bei jeder neuen Kundenanlage automatisch abgefragt werden. Die API hat keinen Standard-Connector in Power Platform. |
| 7 | Das Feld "Vollstaendiger Name" soll automatisch als Kombination aus "Vorname" und "Nachname" angezeigt werden. |
| 8 | Wenn ein Projekt auf "Abgelehnt" gesetzt wird, sollen alle offenen Aufgaben zu diesem Projekt automatisch auf "Storniert" gesetzt werden. |

## Aufgabe 2: Plugin-Pipeline einordnen (20 Minuten)

Ordne die folgenden Anforderungen dem richtigen Plugin-Stage zu (PreValidation, PreOperation, PostOperation) oder erklaere, warum kein Plugin benoetigt wird:

1. "Beim Erstellen einer Bestellung muss der Lagerbestand geprueft werden. Wenn der Bestand nicht ausreicht, soll die Bestellung nicht gespeichert werden."
2. "Beim Speichern einer Bestellung soll der eindeutige Bestellnummer-Code automatisch gesetzt werden (Format: ORD-JJJJ-LAUFNUMMER)."
3. "Nach dem Loeschen eines Kunden sollen alle zugehoerigen Kontakte archiviert werden."
4. "Beim Aendern des Lieferdatums soll eine E-Mail an den Kunden gesendet werden."

## Aufgabe 3: Entscheidungsbaum bauen (15 Minuten)

Erstelle einen eigenen Entscheidungsbaum (Mermaid oder Text) mit fuenf Ja/Nein-Fragen, der einem Junior-Developer hilft, die richtige Erweiterungsebene zu finden. Der Baum soll alle fuenf Ebenen abdecken.

## Aufgabe 4: Overengineering erkennen (10 Minuten)

Folgender Loesungsvorschlag wurde von einem Developer prasentiert:

"Wir implementieren ein Plugin im PostOperation-Stage, das bei jedem Speichern eines Urlaubsantrags ausgeloest wird und ein Azure Function aufruft, welche die Anzahl der Urlaubstage berechnet und das Ergebnis zurueck in Dataverse schreibt."

Was ist an diesem Vorschlag aus SA-Sicht problematisch? Welche einfachere Alternative gibt es?
