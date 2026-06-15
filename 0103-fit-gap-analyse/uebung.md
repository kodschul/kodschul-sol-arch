# Lab 1.3 - Uebung: Fit-Gap-Analyse durchfuehren

## Szenario

Du setzt die Discovery aus Lab 1.2 fort. Der Urlaubsantragsprozess soll mit der Power Platform umgesetzt werden. Die MoSCoW-Priorisierung liegt vor. Jetzt fuehrst du die Fit-Gap-Analyse durch.

## Anforderungsliste (Ergebnis aus Lab 1.2)

Die folgenden Anforderungen wurden als Must have oder Should have eingestuft:

| ID | Beschreibung | MoSCoW |
|---|---|---|
| ANF-001 | Mitarbeiter koennen Urlaubsantraege digital stellen (Datum, Typ, Kommentar) | Must have |
| ANF-002 | Vorgesetzte erhalten E-Mail-Benachrichtigung bei neuen Antraegen | Must have |
| ANF-003 | HR sieht eine Uebersicht aller offenen Antraege mit Filtermoeglichkeit | Must have |
| ANF-004 | Integration mit SAP fuer Personalstammdaten (Name, Abteilung, Urlaubsanspruch) | Should have |
| ANF-005 | Mitarbeiter sehen ihren Resturlaub im System | Must have |
| ANF-006 | Vertretungsregelung: Wenn Vorgesetzter abwesend ist, genehmigt ein Vertreter | Should have |
| ANF-008 | Automatische Erinnerungs-E-Mail wenn ein Antrag laenger als drei Tage offen ist | Should have |
| ANF-011 | Alle Genehmigungen werden mit Zeitstempel und Kommentar protokolliert | Must have |
| ANF-013 | Das System kann auch von mobilen Geraeten genutzt werden | Should have |

Zusaetzlich sind folgende technische Anforderungen aus dem Workshop mit dem CTO bekannt:
- T-001: Alle Daten muessen in der EU gespeichert sein (DSGVO).
- T-002: Single Sign-On ueber das bestehende Microsoft 365-Konto.
- T-003: Keine separaten Nutzerkonten, die verwaltet werden muessen.

## Aufgabe 1: Fit-Gap-Tabelle erstellen (30 Minuten)

Erstelle eine vollstaendige Fit-Gap-Tabelle fuer alle 12 Anforderungen (9 funktionale und 3 technische). Fuer jede Anforderung:
- Bewerte als Fit, Partial Fit oder Gap
- Beschreibe kurz, was die Power Platform dazu von Haus aus kann
- Beschreibe die Luecke, falls vorhanden

Hinweis: Ueberprueffe fuer ANF-004 (SAP-Integration), ob es einen nativen SAP-Connector in Power Platform gibt und was dieser kann.

## Aufgabe 2: Gaps priorisieren (15 Minuten)

Betrachte die Anforderungen, die du als Gap oder Partial Fit bewertet hast. Ordne sie nach Risiko fuer das Projekt:
- Welche Gaps sind Projektstoprisiken?
- Welche sind beherrschbar?

## Aufgabe 3: SA-Empfehlung fuer den kritischsten Gap (20 Minuten)

Waehle den aus deiner Sicht kritischsten Gap aus und schreibe eine strukturierte SA-Empfehlung mit:
- Ausgangslage
- Mindestens zwei Optionen mit Trade-offs
- Klare Empfehlung
- Konsequenzen

## Aufgabe 4: Gesamtbewertung (10 Minuten)

Formuliere eine zusammenfassende Einschaetzung fuer den Auftraggeber (zwei bis drei Saetze):
- Ist das Projekt mit der Power Platform grundsaetzlich realisierbar?
- Was sind die groessten Risiken?
- Was ist die Empfehlung fuer das weitere Vorgehen?
