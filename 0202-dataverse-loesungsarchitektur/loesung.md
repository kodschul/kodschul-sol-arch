# Lab 2.2 - Loesung: Dataverse als Architekturentscheidung

## Aufgabe 1: Standardtabellen identifizieren

| Entitaet | Standardtabelle oder Custom | Begruendung |
|---|---|---|
| Mitarbeiter | Standardtabelle: SystemUser | Alle Power Platform-Nutzer sind bereits als SystemUser vorhanden. Zusaetzliche Felder koennen als Custom Columns hinzugefuegt werden. |
| Vorgesetzter | Standardtabelle: SystemUser | Vorgesetzter ist auch ein SystemUser. Die Beziehung wird als Lookup-Spalte auf SystemUser in der UrlaubsAntrag-Tabelle abgebildet. |
| Urlaubsantrag | Custom Table: cr_UrlaubsAntrag | Keine Standardtabelle deckt diesen Anwendungsfall ab. |
| Abteilung | Standardtabelle: BusinessUnit | BusinessUnit ist die Dataverse-Standardabbildung von Organisationseinheiten und ist direkt in das Sicherheitsmodell integriert. |
| Urlaubstyp | Custom Table oder Choice | Fuer wenige, stabile Typen genuegt eine Choice-Spalte direkt auf cr_UrlaubsAntrag. Wenn Urlaubstypen eigene Eigenschaften haben (z.B. maximale Tage), wird eine separate Custom Table benoetigt. |
| Genehmigungsprotokoll | Custom Table: cr_Genehmigungsprotokoll | Keine Standardtabelle passt. Der Dataverse-Audit-Trail koennte alternativ genutzt werden, ist aber fuer Endnutzer-Ansichten ungeeignet. |

## Aufgabe 2: Dataverse vs. SQL-Entscheidung

**Kunde A: Maschinendaten**

Empfehlung: SQL Server oder Azure SQL / Azure Data Explorer, NICHT Dataverse.

Begruendung: 50.000 Messpunkte pro Stunde sind 438 Millionen Datensaetze pro Jahr. Dataverse-Speicher ist bei dieser Menge prohibitiv teuer und fuer Zeitreihendaten nicht optimiert. Azure Data Explorer oder InfluxDB sind fuer Zeitreihendaten die richtige Wahl. Power BI verbindet sich direkt mit diesen Quellen.

**Kunde B: Projektzeiterfassung fuer Berater**

Empfehlung: Dataverse.

Begruendung: Row-Level-Zugriff (Berater sehen nur eigene Eintraege) erfordert das Dataverse-Sicherheitsmodell. Die komplexen Genehmigungsprozesse profitieren von Power Automate mit Dataverse-Integration. 500 Berater sind eine gut handhabbare Datenmenge.

**Kunde C: Grosshandel mit bestehendem SQL Server**

Empfehlung: SQL Server behalten, Power Apps per Direct Connection oder Virtual Tables anbinden.

Begruendung: Die Datenbasis existiert bereits und ist in ERP-Prozesse eingebunden. Eine Migration nach Dataverse waere teuer und riskant. Power Apps kann direkt auf SQL Server zugreifen. Fuer eine einfache Aussendienst-App ist das ausreichend.

## Aufgabe 3: Speicherplanung

**Berechnung:**

Antraege insgesamt: 5.000 Mitarbeiter x 8 Antraege x 2 Jahre = 80.000 Antraege

Anhange (10% der Antraege): 8.000 Anhange x 500 KB = 4.000 MB = ca. 4 GB File Storage

Audit-Eintraege: 80.000 Antraege x 3 = 240.000 Audit-Datensaetze

Datenbankgroesse (Schatzung je Antrag ca. 5 KB inkl. Beziehungen): 80.000 x 5 KB = 400 MB Database Storage

**SA-Empfehlung zur Speicheroptimierung:**

Fuer die Anhange wird SharePoint statt Dataverse File Storage empfohlen. SharePoint-Speicher ist in den meisten M365-Lizenzen enthalten. 4 GB in Dataverse kosten ueberschlagig mehrere hundert Euro pro Monat zusaetzlich. Die Anhange werden in SharePoint gespeichert, der Link wird als URL-Feld in Dataverse hinterlegt.

Fuer den Audit-Log wird eine Retention Policy von 12 Monaten empfohlen. Aeltere Audit-Daten werden in ein separates Log-Archiv exportiert.

## Aufgabe 4: Die vier Schichten

| Anforderung | Schicht | Begruendung |
|---|---|---|
| 1. E-Mail bei Antragserstellung | Logikschicht | Power Automate Flow wird durch Dataverse-Event (record created) ausgeloest |
| 2. Nur Antragsteller und Vorgesetzter sehen Antrag | Sicherheitsschicht | Business Units und Sicherheitsrollen mit Row-Level-Zugriff |
| 3. Startdatum nicht in der Vergangenheit | Logikschicht | Geschaeftsregel (Business Rule) direkt auf der Tabelle, ohne Code |
| 4. SAP-System fragt Status per API ab | API-Schicht | Dataverse Web API (OData), SAP authentifiziert sich ueber OAuth |
