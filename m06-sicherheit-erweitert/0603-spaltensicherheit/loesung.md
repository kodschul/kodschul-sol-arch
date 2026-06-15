# Lab 6.3 - Loesung: Spaltensicherheit fuer sensible Daten konfigurieren

## Aufgabe 1: Spaltensicherheitsprofile

| Feld                   | Spaltensicherheit? | Begruendung                                               |
| ---------------------- | ------------------ | --------------------------------------------------------- |
| Name, Adresse, Telefon | Nein               | Standard-Kontaktdaten, alle Nutzergruppen benoetigen sie  |
| Kreditlimit            | Ja                 | Finanzdaten, Minimalprinzip, nur Kreditabteilung          |
| Rabattstufe            | Ja                 | Wettbewerbsrelevant, nur Vertriebsleiter + Einkauf        |
| Bonitaetsscore         | Ja                 | Kreditentscheidungsgrundlage, streng limitiert            |
| Interne Notizen        | Ja                 | Teilweise eingeschraenkt (Schreiben alle, Lesen selektiv) |
| IBAN                   | Ja                 | Bankdaten, DSGVO-relevant, nur Buchhaltung                |

**Spaltensicherheitsprofile:**

| Profil                      | Felder                               | Lesen | Erstellen | Aktualisieren | Nutzergruppen        |
| --------------------------- | ------------------------------------ | ----- | --------- | ------------- | -------------------- |
| Kreditprofil                | Kreditlimit, Bonitaetsscore          | Ja    | Ja        | Ja            | Kreditabteilung      |
| GF-Leseprofil               | Bonitaetsscore                       | Ja    | Nein      | Nein          | Geschaeftsfuehrung   |
| Vertriebsleiter-Profil      | Rabattstufe, Interne Notizen (Lesen) | Ja    | Ja        | Ja            | Vertriebsteamleiter  |
| Vertriebsmitarbeiter-Profil | Interne Notizen (Schreiben)          | Nein  | Ja        | Ja (eigene)   | Vertriebsmitarbeiter |
| Buchhaltungs-Profil         | IBAN                                 | Ja    | Ja        | Ja            | Buchhaltung          |
| Einkaufs-Profil             | Rabattstufe                          | Ja    | Nein      | Nein          | Einkauf              |

**Hinweis zu Internen Notizen:** Wenn Vertriebsmitarbeiter schreiben aber nicht lesen sollen (nur Teamleiter lesen alle), ist das mit Standard-Spaltensicherheit schwer abzubilden, weil "Erstellen" ohne "Lesen" ungewoehnlich ist. Alternativer Ansatz: Interne Notizen als eigene verknuepfte Tabelle mit BU-Sicherheit statt Spaltensicherheit.

---

## Aufgabe 2: Deployment-Fallstrick

**Was wahrscheinlich passiert ist:**
Die Spaltensicherheitsprofile wurden in der Dev-Umgebung manuell konfiguriert und nicht in eine Loesung aufgenommen. Beim Deployment wurde die Tabellenkonfiguration (Feld mit aktivierter Spaltensicherheit) migriert, aber ohne das zugehoerige Profil und ohne die Nutzerzuweisung zum Profil.

Das Ergebnis: Das Feld ist in der Produktivumgebung als "gesichert" markiert (Spaltensicherheit ist aktiviert), aber es gibt kein Profil, das der Kreditabteilung Zugriff gibt. Daher sehen alle Nutzer das Feld nicht.

**Was haette geprueft werden muessen:**

1. Sind alle Spaltensicherheitsprofile in die Loesung aufgenommen worden?
2. Sind die Profilzuweisungen (Nutzer/Teams) dokumentiert und koennen sie in der Produktivumgebung reproduziert werden?
3. Post-Deployment-Test: Kann ein Test-Nutzer der Kreditabteilung das Feld sehen?

**Loesung:** Profil in Produktivumgebung erstellen und der Kreditabteilung zuweisen. Fuer die Zukunft: Deployment-Checkliste mit Spaltensicherheitsprofile als Pflichtpunkt.

---

## Aufgabe 3: Formular-Sichtbarkeit vs. Spaltensicherheit

**Weg 1: Erweiterte Suche (Advanced Find)**
Jeder Nutzer mit Read-Zugriff auf die Kunden-Tabelle kann in der Erweiterten Suche eine Ansicht erstellen, die das IBAN-Feld als Spalte enthaelt. Das Formular ist nur eine Darstellungsform - die Erweiterte Suche umgeht das Formular vollstaendig.

**Weg 2: Dataverse Web API**
Wer die Dataverse Web API aufrufen kann (jeder Nutzer mit einer Power Apps-Lizenz), kann per HTTP-Anfrage den Datensatz inkl. IBAN-Feld abrufen:

```
GET /api/data/v9.2/accounts(guid)?$select=cr_iban
```

Ohne Spaltensicherheit kommt das Feld in der Antwort, auch wenn es im Formular nicht angezeigt wird.

**Fazit fuer den Entwickler:**
"Das Formular ist eine Sichtmaske, keine Sicherheitsmassnahme. Dataverse hat viele Zugangswege auf dieselben Daten: Formulare, Ansichten, API, Excel-Export, Power BI-Connector. Nur Spaltensicherheit gilt fuer alle diese Wege gleichzeitig. Fuer IBAN als Bankdaten ist das die einzige technisch belastbare Option - und bei einem Datenschutzaudit wird genau das geprueft."
