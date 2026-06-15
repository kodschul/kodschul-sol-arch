# Lab 5.1 - Loesung: Umgebungen als erste Sicherheitsgrenze verstehen

## Aufgabe 1: Risikoanalyse

**Strukturelles Problem der Default-Umgebung:**
Die Default-Umgebung gewaehrt automatisch allen lizenzierten Nutzern des Tenants die Rolle "Environment Maker". Das bedeutet, jeder Mitarbeiter mit einer Power Apps-Lizenz kann Apps lesen, ausfuehren und theoretisch eigene Ressourcen erstellen. Der HR-Vorfall ist keine Fehlkonfiguration - er ist die erwartbare Konsequenz dieser Architektur.

**Gefaehrdete Daten:**

- Alle Dataverse-Tabellen in der Default-Umgebung: Sicherheitsrollen koennen Zugriff begrenzen, aber die Tabellenexistenz ist fuer alle Maker sichtbar.
- SharePoint-Verbindungen in Apps: Wer eine App teilt, teilt implizit auch den Datenzugriff.
- Verbindungsanmeldedaten: In der Default-Umgebung geteilte Verbindungen koennen von anderen Makern nachgenutzt werden.

**Risiko der 23 Apps:**

- Keine zentrale Uebersicht, wer welche App gebaut hat und welche Daten sie verarbeitet.
- Keine DLP-Policy-Erzwingung, weil niemand weiss, welche Verbindungen verwendet werden.
- DSGVO-Risiko: Personendaten in Apps ohne dokumentierten Verantwortlichen.

---

## Aufgabe 2: Ziel-Umgebungsarchitektur

| Umgebungsname                           | Typ        | Zweck                                              | Zugriffsgruppe           |
| --------------------------------------- | ---------- | -------------------------------------------------- | ------------------------ |
| PP-Default (umbenannt: "Sandbox Maker") | Sandbox    | Freies Experimentieren, kein Produktivdatenzugriff | Alle Maker               |
| PP-HR-Prod                              | Produktion | HR-Anwendung, DSGVO-Daten                          | Nur HR-Nutzer + IT-Admin |
| PP-HR-Dev                               | Entwickler | Entwicklung der HR-Loesung                         | HR-Entwickler            |
| PP-Marketing                            | Produktion | Marketing-Apps, nicht-sensible Daten               | Marketing-Nutzer + Maker |
| PP-ERP-Dev                              | Sandbox    | ERP-Integrationsprojekt Entwicklung                | ERP-Projektteam          |
| PP-ERP-Test                             | Sandbox    | ERP-Integrationsprojekt Test + UAT                 | ERP-Team + Fachbereich   |
| PP-ERP-Prod                             | Produktion | ERP-Integration Produktion                         | Alle betroffenen Nutzer  |

**Begruendung HR-Isolierung:** DSGVO Art. 25 (Privacy by Design) erfordert technische Massnahmen zur Datentrennung. Eine separate Umgebung mit explizit gesteuertem Zugang ist die staerkste verfuegbare Massnahme in Power Platform.

**Begruendung Marketing-Umgebung:** Hohes Maker-Volumen braucht einen kontrollierten Bereich ausserhalb der Default-Umgebung, wo DLP-Policies gelten, aber Kreativitaet moeglich ist.

---

## Aufgabe 3: Migrationsrisiken

**Was moeglich ist:**

- Canvas Apps koennen exportiert und in andere Umgebungen importiert werden.
- Dataverse-Tabellen koennen ueber Loesungen migriert werden.

**Was nicht automatisch geht:**

- **Verbindungen:** Verbindungen sind umgebungsspezifisch. Jede App braucht neue Verbindungen in der Zielumgebung. Verbindungsanmeldedaten muessen neu eingegeben werden.
- **Nutzerberechtigungen:** Sicherheitsrollen sind umgebungsspezifisch. Rollenzuweisungen muessen neu vorgenommen werden.
- **Umgebungsvariablen:** URLs, Konfigurationen, die in der Quellumgebung definiert sind, muessen in der Zielumgebung angepasst werden.

**Abhaengigkeiten pruefen:**

1. Welche Datenquellen nutzt die App? (SharePoint, Dataverse, SQL, SAP?)
2. Welche Flows sind mit der App verbunden?
3. Wer nutzt die App aktuell, und haben diese Nutzer Zugang zur Zielumgebung?

**Empfohlene Vorgehensweise:**

1. Inventarisierung aller 23 Apps (Datenquellen, Nutzer, Verantwortliche)
2. Klassifizierung nach Datensensibilitaet
3. Sicherheitsrelevante Apps (HR-Daten, Finanzdaten) sofort in dedizierte Umgebungen migrieren
4. Restliche Apps in eine kontrollierte "Maker-Umgebung" migrieren
5. Default-Umgebung durch DLP-Policy auf unkritische Verbindungen beschraenken
6. Verantwortliche fuer jede App dokumentieren

**Zeitplan-Empfehlung:** HR-App in 2 Wochen migrieren (aktives Risiko). Rest im Rahmen eines strukturierten 3-Monats-Plans.
