# Loesung: Governance-Anforderungen fuer Agents beruecksichtigen

## Aufgabe 1: Bestandsaufnahme-Checkliste

**Dimension: Zugang und Identitaet**
1. Welcher Authentifizierungsmodus ist konfiguriert? (Anonym, Microsoft, Anderer)
2. Wer hat Zugriff auf den Agent? (Alle Mitarbeiter, bestimmte Abteilung, extern erreichbar?)
3. Auf welche Datenquellen hat der Agent Zugriff? (SharePoint-Ordner, Dataverse-Tabellen, externe APIs)

**Dimension: Inhalte und Verhalten**
4. Sind Custom Instructions konfiguriert? Was steht drin?
5. Gibt es Off-Topic-Regeln? Was passiert bei sensiblen Fragen?
6. Welche Knowledge Sources sind angebunden? Sind diese Quellen geprueft und aktuell?

**Dimension: Betrieb und Ueberwachung**
7. Gibt es Analytics-Einsicht? Wer schaut regelmaessig rein?
8. Werden Konversationen gespeichert? Gibt es eine Datenschutzerklaerung fuer Nutzer?

**Dimension: Lebenszyklus und Pflege**
9. Wer ist der definierte Owner des Agents?
10. Wann wurde der Agent zuletzt geprueft oder aktualisiert?
11. Gibt es einen Prozess fuer das Ausser-Betrieb-Nehmen?

---

## Aufgabe 2: Sofortmassnahmen

**Risiko 1: Oeffentlich zugaengliche Agents**
Konkretes Risiko: Zwei Agents ohne Login-Pflicht koennen von beliebigen externen Personen genutzt werden. Wenn diese Agents auf interne SharePoint-Inhalte zeigen, sind interne Informationen faktisch oeffentlich.

Sofortmassnahme (7 Tage): Beide Agents sofort auf "Disabled" setzen oder Authentifizierung auf "Microsoft" umstellen. Die Deaktivierung dauert weniger als 5 Minuten.

Mittelfristige Loesung (3 Monate): Entscheiden ob diese Agents ueberarbeitet und gezielt als externe Agents (mit eingeschraenkten Knowledge Sources) wieder deployed werden sollen.

**Risiko 2: Agents ohne definierten Eigentuemer**
Konkretes Risiko: Wenn niemand fuer einen Agent verantwortlich ist, wird er nicht gepflegt, und veraltete Antworten werden als aktuelle Wahrheit ausgegeben.

Sofortmassnahme (7 Tage): Im Power Platform Admin Center alle 17 Agents auflisten und den Erstellungsnutzer kontaktieren. Eigentuemer fuer jeden Agent benennen und dokumentieren.

Mittelfristige Loesung (3 Monate): Governance-Richtlinie einfuehren (siehe Aufgabe 3), CoE Toolkit installieren fuer kontinuierliches Monitoring.

**Risiko 3: Zugriff auf Personalinformationen**
Konkretes Risiko: Agents die auf SharePoint-Ordner mit Personalinformationen zeigen, koennen diese Informationen an beliebige Nutzer ausgeben (je nach Authentifizierungskonfiguration).

Sofortmassnahme (7 Tage): Knowledge Sources dieser Agents pruefen und Zugriff auf sensible Ordner sofort einschraenken oder entfernen.

Mittelfristige Loesung (3 Monate): Klare Regeln welche SharePoint-Bereiche als Knowledge Source erlaubt sind. Datenschutzfolgenabschaetzung fuer betroffene Agents.

---

## Aufgabe 3: Governance-Richtlinie (Entwurf)

**Agent-Governance-Richtlinie v1.0**

1. Nur Power Platform Maker mit genehmigtem Maker-Zugang duerfen Agents bauen.
2. Jeder neue Agent muss vor Deployment vom IT-Team registriert werden (Name, Zweck, Owner, Zielnutzer).
3. Jeder Agent muss einen definierten Eigentuemer haben. Eigentuemer ist eine Abteilung, nicht eine Einzelperson.
4. Interne Agents muessen Microsoft-Authentifizierung aktiviert haben.
5. Jeder Agent muss Off-Topic-Anweisungen (Custom Instructions) enthalten, die den Nutzungsbereich klar begrenzen.
6. Knowledge Sources duerfen nur geprueften SharePoint-Bereiche oder vom IT-Team freigegebene Quellen umfassen.
7. Agents mit generativen Antworten benoetigen eine datenschutzrechtliche Freigabe durch den Datenschutzbeauftragten.
8. Jeder Agent muss halbjährlich durch den Eigentuemer geprueft und bestaetigt werden.
9. Agents ohne Aktivitaet in 90 Tagen werden automatisch deaktiviert.
10. Verstoeße gegen diese Richtlinie fuhren zur sofortigen Deaktivierung des Agents.

---

## Aufgabe 4: DLP-Konfiguration

**Was ist eine DLP-Richtlinie?**
Eine Data Loss Prevention (DLP) Richtlinie in der Power Platform definiert, welche Connectors in Apps, Flows und Agents gemeinsam genutzt werden duerfen. Sie verhindert, dass Daten aus einer Quelle (z.B. Dataverse) unerlaubt in eine andere Quelle (z.B. Twitter) fliessen.

**Connector-Klassifizierungen:**
- Business: Erlaubte Connectors fuer geschaeftliche Nutzung (z.B. SharePoint, Dataverse, Office 365)
- Non-Business: Alle anderen Connectors (z.B. Twitter, Facebook, persoenliche Dropbox)
- Blocked: Vollstaendig gesperrte Connectors

Connectors aus verschiedenen Klassifizierungen koennen nicht in derselben App oder demselben Agent verwendet werden.

**Konfiguration:**
Im Power Platform Admin Center unter "Data Policies" eine neue Richtlinie erstellen:
- Twitter, Facebook, LinkedIn Connectors: In "Non-Business" oder "Blocked" setzen
- Persoenliche Microsoft-Konten (MSA-Connector): "Blocked"
- Dataverse, SharePoint, Exchange Online: "Business"

Damit wird technisch verhindert, dass ein Agent gleichzeitig auf Dataverse und auf Twitter zugreifen kann.

**Wer nimmt die Konfiguration vor?**
Nur Power Platform Administrators oder Global Administrators des Tenants koennen DLP-Richtlinien erstellen und aendern. Diese Rolle sollte nicht an Maker vergeben werden.
