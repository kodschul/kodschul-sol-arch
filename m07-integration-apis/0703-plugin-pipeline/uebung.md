# Lab 7.3 - Uebung: Plug-In-Pipeline und Ausfuehrungsmodelle verstehen

## Szenario

Ein Finanzdienstleister baut eine Kreditantragsloesung in Dataverse. Folgende Anforderungen sind in der Entwicklung:

**Anforderung 1:** Wenn ein Kreditantrag gespeichert wird, soll automatisch ein Bonitaetsscore aus einem externen Schufa-aehnlichen Dienst abgerufen werden. Der Dienst antwortet in der Regel in 200-800ms.

**Anforderung 2:** Das Feld "Antragsnummer" soll automatisch mit einem eindeutigen Praefix "KA-JJJJ-XXXXXX" berechnet werden, wenn der Antrag erstellt wird. Die Logik ist komplex (Jahr + laufende Nummer aus einer separaten Zaehler-Tabelle, atomar).

**Anforderung 3:** Wenn ein Kreditantrag auf "Abgelehnt" gesetzt wird, soll automatisch eine E-Mail an den Antragsteller gesendet werden.

**Anforderung 4:** Wenn ein Antragsbetrag ueber 100.000 EUR liegt, soll die Operation automatisch abgebrochen werden und eine Fehlermeldung angezeigt werden: "Antraege ueber 100.000 EUR erfordern eine manuelle Bearbeitung."

---

## Aufgabe 1: Plugin oder Flow? (20 Minuten)

Entscheide fuer jede Anforderung, ob ein Plugin oder ein Power Automate Flow die geeignetere Loesung ist. Begruende.

| Anforderung                          | Loesung | Begruendung |
| ------------------------------------ | ------- | ----------- |
| 1 - Bonitaetsscore abrufen           |         |             |
| 2 - Antragsnummer generieren         |         |             |
| 3 - E-Mail bei Ablehnung             |         |             |
| 4 - Betrag-Validierung > 100.000 EUR |         |             |

---

## Aufgabe 2: Pipeline-Phase bestimmen (15 Minuten)

Fuer die Anforderungen, bei denen du ein Plugin gewaehlt hast:

1. In welcher Phase der Plugin-Pipeline registrierst du das Plugin?
2. Synchron oder asynchron?
3. Braucht das Plugin Pre- oder Post-Images? Welche Felder?

---

## Aufgabe 3: Anti-Pattern erkennen (15 Minuten)

Dein Entwickler sagt: "Fuer Anforderung 1 (Bonitaetsscore) mache ich ein synchrones Pre-Operation Plugin. Dann steht der Score direkt auf dem Datensatz, wenn er gespeichert wird."

Erklaere, warum das ein Anti-Pattern ist und welche konkreten Probleme entstehen koennen. Schlage eine bessere Loesung vor.
