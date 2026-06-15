# Lab 1.4 - Uebung: Von der Fachanforderung zum technischen Konzept

## Szenario

Das Urlaubsantragsprojekt geht in die Konzeptionsphase. Du hast die Discovery und die Fit-Gap-Analyse abgeschlossen. Jetzt erstellst du technische Konzepte fuer die wichtigsten Anforderungen.

## Aufgabe 1: Kritikalitaet und Komplexitaet bewerten (20 Minuten)

Bewerte die folgenden Anforderungen auf einer Skala von 1 (niedrig) bis 5 (hoch) fuer Kritikalitaet und Komplexitaet. Begruende jede Bewertung in einem Satz.

| ID | Beschreibung |
|---|---|
| ANF-001 | Mitarbeiter koennen Urlaubsantraege digital stellen |
| ANF-004 | SAP-Integration Personalstamm |
| ANF-006 | Vertretungsregelung bei abwesendem Vorgesetzten |
| ANF-008 | Automatische Erinnerung nach drei Tagen |
| ANF-011 | Protokollierung mit Zeitstempel |

Welche der fuenf Anforderungen verdient die meiste Planungsaufmerksamkeit?

## Aufgabe 2: Technisches Konzept erstellen (30 Minuten)

Erstelle ein vollstaendiges technisches Konzept fuer ANF-006 (Vertretungsregelung):

"Wenn der direkte Vorgesetzte eines Mitarbeiters im Zeitraum des beantragten Urlaubs selbst abwesend ist, soll der Antrag automatisch an den Vertreter des Vorgesetzten weitergeleitet werden."

Dein Konzept muss folgende Punkte beantworten:
- Datenmodell: Welche Felder und Beziehungen werden benoetigt?
- Prozesslogik: Was passiert wann und in welcher Reihenfolge?
- Annahmen: Welche Annahmen machst du, die mit dem Kunden bestaetigt werden muessen?
- Testkriterien: Wie weiss man, dass es funktioniert?

## Aufgabe 3: ADR schreiben (20 Minuten)

Es muss eine Architekturentscheidung getroffen werden: Wie wird die Vertreterlogik gespeichert?

Option A: Jeder Vorgesetzte pflegt seinen Vertreter in einem eigenen Profil-Datensatz in Dataverse.
Option B: Die Vertreterkonfiguration wird im Benutzerprofil (SystemUser) als custom field gespeichert.
Option C: Die Vertreterkonfiguration kommt aus SAP zusammen mit den Stammdaten.

Schreibe ein ADR im Format aus der Theorie. Begruende deine Entscheidung.

## Aufgabe 4: Hinterfragen einer Anforderung (10 Minuten)

ANF-006 enthalt eine Unklarheit: Was passiert, wenn auch der Vertreter abwesend ist? Formuliere drei Praezisierungsfragen, die du dem Kunden stellen wuerdest, bevor du weitermachst.
