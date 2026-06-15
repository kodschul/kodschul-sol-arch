# Lab 1.2 - Loesung: Discovery steuern

## Aufgabe 1: Stakeholder-Map

| | Hoher Einfluss | Geringer Einfluss |
|---|---|---|
| Hohes Interesse | Abteilungsleiter HR (Sponsor), CTO, HR-Sachbearbeiterinnen, Abteilungsleiter aus Produktion/Vertrieb/Verwaltung | Endnutzer (Mitarbeiter) |
| Geringes Interesse | (keine in diesem Szenario) | (keine weiteren genannten) |

**Intensiv einbinden (hoher Einfluss, hohes Interesse):**
Der Abteilungsleiter HR trifft Budgetentscheidungen und definiert Prioritaeten. Die HR-Sachbearbeiterinnen kennen den Prozess am besten und werden das System taeglich nutzen. Die Abteilungsleiter muessen als Genehmiger im System abgebildet werden. Der CTO hat Anforderungen bezueglich IT-Sicherheit und Infrastruktur.

**Regelmaessig informieren (geringer Einfluss, hohes Interesse):**
Die fuenf Mitarbeiter als Endnutzer haben kein Projektbudget und keinen direkten Einfluss, aber ihre Usability-Anforderungen sind fuer den Erfolg des Systems entscheidend. Sie werden in Workshop 2 eingebunden, um typische Nutzungsszenarien zu erkunden.

## Aufgabe 2: Pre-Discovery-Hypothesen

1. **Hypothese:** Es gibt komplexe Vertretungsregelungen wenn ein Vorgesetzter selbst im Urlaub ist.
   **Prueffrage:** "Was passiert heute, wenn ein Mitarbeiter einen Urlaubsantrag stellt, waehrend sein direkter Vorgesetzter selbst im Urlaub ist?"

2. **Hypothese:** Die Genehmigungsfristen sind ein grosser Schmerzpunkt.
   **Prueffrage:** "Wie lange dauert es im Schnitt, bis ein Urlaubsantrag genehmigt oder abgelehnt wird? Wann wird das von Mitarbeitern als 'zu lang' empfunden?"

3. **Hypothese:** Es gibt unterschiedliche Urlaubstypen (Erholungsurlaub, Sonderurlaub, Gleitzeit) mit unterschiedlichen Genehmigungsprozessen.
   **Prueffrage:** "Welche verschiedenen Arten von Abwesenheiten werden heute ueber das bestehende System abgewickelt?"

4. **Hypothese:** Die Resturlaubsberechnung ist ein manueller und fehleranfaelliger Prozess.
   **Prueffrage:** "Wie wird heute der Resturlaub eines Mitarbeiters berechnet und wer ist dafuer verantwortlich?"

5. **Hypothese:** SAP enthaelt die verbindlichen Personalstammdaten und muss als Single Source of Truth behandelt werden.
   **Prueffrage:** "Welche Personaldaten sind in SAP und welche in anderen Systemen? Was passiert wenn die Daten nicht uebereinstimmen?"

## Aufgabe 3: Workshop-Planung

**Workshop 1 (Prozesse und Fachlichkeit) - Teilnehmer: HR-Sachbearbeiterinnen, Abteilungsleiter HR**

Themen: Aktueller Prozess von A bis Z, Schmerzpunkte, Sonderregeln.

Fuenf Kernfragen:
1. Beschreiben Sie mir den Weg eines Urlaubsantrags von der Einreichung bis zur finalen Genehmigung.
2. Was passiert wenn ein Vorgesetzter abwesend ist oder nicht reagiert?
3. Welche Ausnahmefaelle gibt es, die nicht dem Standardprozess folgen?
4. Was sind die drei groessten Probleme mit dem heutigen Prozess?
5. Welche Regeln gelten bezueglich gleichzeitigem Urlaub im selben Team?

**Workshop 2 (Technische Anforderungen und Nutzerszenarien) - Teilnehmer: CTO, Fuenf Endnutzer, Abteilungsleiter**

Themen: Systemintegration, Nutzererwartungen, Geraete und Zugriff.

Fuenf Kernfragen:
1. Ueber welche Geraete soll auf das System zugegriffen werden? (PC, Smartphone, Tablet?)
2. Welche Daten soll das System aus SAP beziehen und wie oft sollen diese synchronisiert werden?
3. Welche Datenschutzanforderungen gelten fuer Urlaubsdaten?
4. Was erwarten Sie als Mitarbeiter als Bestaetigung, wenn Ihr Antrag eingegangen ist?
5. Wie viele Antraege werden pro Monat geschaetzt gestellt?

**Workshop 3 (Validierung und Priorisierung) - Alle Stakeholder**

Themen: Prasentation der konsolidierten Anforderungen, MoSCoW-Priorisierung, offene Fragen klaeren.

Fuenf Kernfragen:
1. Haben wir alle Anforderungen richtig verstanden?
2. Was haben wir vergessen?
3. Was ist fuer den ersten Go-Live unverzichtbar?
4. Was kann in einer zweiten Version nachgeliefert werden?
5. Gibt es Anforderungen, die wir explizit ausschliessen sollen?

## Aufgabe 4: MoSCoW-Priorisierung

| Anforderung | Kategorie | Begruendung |
|---|---|---|
| 1. Antraege digital stellen | Must have | Kernzweck des Systems ohne den es keinen Mehrwert gibt. |
| 2. E-Mail-Benachrichtigung Vorgesetzte | Must have | Ohne Benachrichtigung werden Antraege nicht bearbeitet, der Prozess bricht zusammen. |
| 3. HR-Uebersicht offene Antraege | Must have | HR-Sachbearbeiterinnen benoetigen Kontrolle und Eingriff bei Blockaden. |
| 4. SAP-Integration Personalstamm | Should have | Wichtig fuer Datenkonsistenz, aber initial koennte ein manueller Import genuegen. |
| 5. Resturlaub anzeigen | Must have | Mitarbeiter brauchen diese Information zum Zeitpunkt der Antragstellung. |
| 6. Vertretungsregelungen | Should have | Wichtig fuer reibungslosen Betrieb, aber im Notfall koennte HR manuell eingreifen. |
| 7. Mobile Nutzung | Could have | Wuenschenswert, aber kein Blocker fuer Go-Live wenn Desktop-Zugang vorhanden ist. |
| 8. Automatische Erinnerungen | Should have | Reduziert manuellen Aufwand bei HR erheblich, ist aber kein Go-Live-Blocker. |
| 9. Geschaeftsleitungsberichte | Could have | Wert fuer die Organisation, aber kein Teil des Kernprozesses. |
| 10. Kollegen-Urlaubs-Einsicht | Could have | Hilfreich fuer Teamplanung, aber nicht fuer den Genehmigungsprozess notwendig. |
