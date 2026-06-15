# Lab 1.3 - Loesung: Fit-Gap-Analyse durchfuehren

## Aufgabe 1: Fit-Gap-Tabelle

| ID | Beschreibung | Kategorie | Power Platform Moeglichkeit | Luecke |
|---|---|---|---|---|
| ANF-001 | Urlaubsantraege digital stellen | Fit | Canvas App oder Model-Driven App mit Dataverse-Formular | Keine |
| ANF-002 | E-Mail-Benachrichtigung Vorgesetzte | Fit | Power Automate mit Outlook-Connector und Approval-Modul | Keine |
| ANF-003 | HR-Uebersicht offene Antraege | Fit | Model-Driven App mit Ansichten und Filtern, oder Canvas App mit Galerie | Keine |
| ANF-004 | SAP-Integration Personalstamm | Partial Fit | SAP-Connector existiert in Power Automate (SAP ERP-Connector) | Datenmapping und ggf. SAP-RFC-Konfiguration durch SAP-Team noetig |
| ANF-005 | Resturlaub anzeigen | Partial Fit | Berechnung in Dataverse moeglich, aber Ausgangsdaten muessen aus SAP kommen | Abhaengig von ANF-004; ohne SAP-Integration manuelle Datenpflege noetig |
| ANF-006 | Vertretungsregelung | Partial Fit | Power Automate kann Bedingungslogik abbilden | Vertreterlogik muss als Datenmodell und Workflow entworfen werden |
| ANF-008 | Automatische Erinnerung nach 3 Tagen | Fit | Scheduled Power Automate Flow prueft taeglich offene Antraege und sendet E-Mail | Keine |
| ANF-011 | Protokollierung mit Zeitstempel | Fit | Dataverse-Audit-Protokoll nativ vorhanden, alternativ Audit-Tabelle | Konfigurationsaufwand fuer Audit-Aktivierung |
| ANF-013 | Mobile Nutzung | Fit | Canvas Apps sind responsiv, Power Apps Mobile App verfuegbar | Responsive Design braucht Entwicklungsaufwand |
| T-001 | Datenspeicherung in der EU | Fit | Power Platform-Umgebungen koennen in Europa betrieben werden | Umgebungsregion muss bei Anlage korrekt gewaehlt werden |
| T-002 | Single Sign-On Microsoft 365 | Fit | Power Platform ist nativ mit Microsoft Entra ID integriert | Keine |
| T-003 | Keine separaten Nutzerkonten | Fit | Nutzer authentifizieren sich mit ihrem bestehenden M365-Konto | Keine |

## Aufgabe 2: Gaps priorisieren

**Projektstoprisiko:**
ANF-004 (SAP-Integration) ist das groesste Risiko, weil ANF-005 (Resturlaub) direkt davon abhaengt. Ohne SAP-Integration fehlen die Ausgangsdaten fuer die Urlaubsberechnung. Dies ist ein Should-have, aber wenn SAP-Daten nicht zugreifbar sind, muss ein Alternativkonzept entwickelt werden.

**Beherrschbar:**
ANF-006 (Vertretungsregelung) ist ein Partial Fit mit klar beschreibbarem Loesungsweg. Der Aufwand ist schaetzbar und das Risiko ist gering.

ANF-005 (Resturlaub) ist nur dann ein Risiko, wenn ANF-004 ein Risiko ist. Wenn SAP erreichbar ist, ist ANF-005 ein Fit.

## Aufgabe 3: SA-Empfehlung fuer ANF-004

**Ausgangslage:**
Das Unternehmen nutzt SAP fuer Personalstammdaten. Diese Daten (Name, Abteilung, Urlaubsanspruch) muessen im Power Platform-System verfuegbar sein. Der native SAP ERP-Connector in Power Automate erlaubt den Zugriff auf SAP via RFC/BAPI, erfordert aber eine On-Premises Data Gateway-Installation und die Konfiguration der entsprechenden SAP-Bausteine durch das SAP-Team.

**Option A: SAP ERP-Connector mit Power Automate**
Vorteil: Nativer Microsoft-Connector, kein Drittanbieter noetig, Daten koennen bei Aenderung synchronisiert werden.
Nachteil: Erfordert On-Premises Data Gateway (zusaetzliche Infrastruktur), SAP-seitige Konfiguration durch SAP-Team, RFC-Berechtigungen in SAP notwendig.

**Option B: Taeglicher CSV-Export aus SAP, Import in Dataverse**
Vorteil: Kein Gateway noetig, geringer technischer Aufwand, SAP-Team liefert eine bekannte CSV-Datei.
Nachteil: Daten sind maximal einen Tag alt, manuelle oder halbautomatische Konfiguration des Imports noetig.

**Option C: Manuelles Anlegen von Mitarbeiterdaten in Power Platform**
Vorteil: Keine SAP-Abhaengigkeit, sofort umsetzbar.
Nachteil: HR muss Daten doppelt pflegen, Fehleranfaelligkeit, nicht skalierbar.

**Empfehlung:** Option A fuer die Zielarchitektur. Fuer den initialen Go-Live Option B als Brueckenloesung, wenn das SAP-Gateway nicht rechtzeitig konfiguriert werden kann. Option C wird explizit ausgeschlossen.

**Konsequenzen:** Das Projektteam muss fruehzeitig das SAP-Team einbinden. Die Gateway-Installation benoetigt IT-Infrastruktur und muss in den Projektplan aufgenommen werden. Ohne fruehzeitige Klaerung ist ANF-004 ein Terminrisiko.

## Aufgabe 4: Gesamtbewertung

Das Urlaubsantragssystem ist mit der Power Platform sehr gut realisierbar. Der ueberwiegende Teil der Anforderungen ist ein direkter Fit, und die identifizierten Partial Fits haben klare Loesungswege. Das einzige nennenwerte Risiko ist die SAP-Integration, die eine fruehzeitige Abstimmung mit dem SAP-Team erfordert und bei Verzoegerung durch eine CSV-Brueckenloesung abgefedert werden kann. Die Empfehlung ist, das Projekt mit Power Platform zu starten und die SAP-Integration als eigenen Workstream mit dem SAP-Team parallel voranzutreiben.
