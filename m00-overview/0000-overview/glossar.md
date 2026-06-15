# Glossar: Power Platform Solution Architect

Alle Begriffe, Abkürzungen und Konzepte die im Kurs verwendet werden — alphabetisch sortiert.

---

## A

**AAD** → Abkürzung für **Azure Active Directory**. Inzwischen umbenannt zu **Entra ID** (s. dort). In älteren Dokumenten und Code noch häufig als AAD zu finden.

**ADO** → Abkürzung für **Azure DevOps** (s. dort).

**ALM** → _Application Lifecycle Management_ — Verwaltung des gesamten Lebenszyklus einer Anwendung: von der Entwicklung über Tests bis zur Auslieferung und Wartung. In Power Platform umfasst ALM den Einsatz von Solutions, Umgebungen, Pipelines und Source Control.

**API** → _Application Programming Interface_ — Schnittstelle, über die zwei Systeme miteinander kommunizieren. Im Kontext Power Platform meist REST-APIs (HTTP-basiert).

**App Registration** → _App-Registrierung_ — Eintrag in Entra ID, der einer Anwendung eine eindeutige Identität gibt. Notwendig für Service-to-Service-Kommunikation mit der Dataverse Web API ohne menschliche Anmeldung.

**App User** → _Application User_ — Technischer Benutzer in Dataverse (kein Mensch), der einer App-Registrierung entspricht. Erhält Sicherheitsrollen und ruft APIs im eigenen Namen auf.

**ASB** → Abkürzung für **Azure Service Bus** (s. dort).

**Azure DevOps (ADO)** → Microsofts Plattform für CI/CD-Pipelines, Source Control (Git), Work Items und Artefaktverwaltung. Kann über die **Power Platform Build Tools** mit der Power Platform verbunden werden.

**Azure Event Hub** → Hochvolumen-Streaming-Dienst in Azure. Geeignet für Millionen von Nachrichten pro Sekunde (IoT, Logs, Telemetrie). Unterschied zu Azure Service Bus: kein garantiertes Exactly-Once-Delivery, dafür massiv skalierbarer Durchsatz.

**Azure Function** → Serverloses Ausführungsmodell in Azure für benutzerdefinierten Code (.NET, Python, JavaScript, etc.). Geeignet für komplexe Logik, externe Bibliotheken oder Hochlast-Szenarien, die Plugins oder Flows übersteigen.

**Azure Service Bus (ASB)** → Nachrichtenbroker-Dienst in Azure. Nachrichten werden in Queues oder Topics (Themen mit Subscriptions/Abonnements) gespeichert und von Empfängern verarbeitet. Garantiert Zustellung, auch wenn Empfänger temporär nicht verfügbar ist.

---

## B

**Bearer Token** → Zugriffstoken (kurze Zeichenkette), das nach Azure-AD-Anmeldung ausgestellt wird. Wird im HTTP-Header mitgeschickt (`Authorization: Bearer <token>`) und beweist dem Zielsystem, dass der Aufrufer berechtigt ist.

**BPF** → Abkürzung für **Business Process Flow** (s. dort).

**BU** → Abkürzung für **Business Unit** (s. dort).

**Build Tools** → Kurzform für **Power Platform Build Tools** — Azure DevOps-Erweiterung von Microsoft, die PowerShell/YAML-Tasks für Export, Import, Solution Checker und weitere ALM-Operationen bereitstellt.

**Business Process Flow (BPF)** → Geführter Prozessablauf in Model-Driven Apps. Zeigt dem Nutzer Schritt für Schritt, welche Informationen in welcher Reihenfolge zu erfassen sind. Konfigurierbar ohne Code.

**Business Unit (BU)** → Organisationseinheit innerhalb einer Dataverse-Umgebung. Bildet die Aufbauorganisation ab und bestimmt, welche Datensätze ein Nutzer standardmäßig sehen kann (Row-Level Security durch Eigentuemer-BU).

---

## C

**Canvas App** → App-Typ in Power Apps mit vollständig frei gestaltbarem UI (wie ein leeres Zeichenblatt). Kann viele verschiedene Datenquellen verbinden. Geeignet für mobile Apps und maßgeschneiderte Oberflächen.

**CDM** → Abkürzung für **Common Data Model** — Standardisiertes Schema von Microsoft mit vordefinierten Tabellen (Account, Contact, etc.) für gängige Geschäftsszenarien. Bildet die Grundlage für Dataverse-Standardtabellen.

**CI/CD** → _Continuous Integration / Continuous Delivery_ (oder Deployment) — Praxis, bei der Code-Änderungen automatisch gebaut, getestet und ausgeliefert werden. In Power Platform: Lösungen werden automatisch exportiert, geprüft und in Zielumgebungen importiert.

**CoE** → Abkürzung für **Center of Excellence** (s. dort).

**Center of Excellence (CoE)** → Governance-Konzept und Microsoft-Starterkit für die verwaltete Einführung der Power Platform im Unternehmen. Umfasst DLP-Monitoring, Maker-Onboarding, Inventarisierung aller Apps/Flows, und Richtlinien-Durchsetzung.

**Common Data Model (CDM)** → s. CDM.

**Connection Reference** → Platzhalter in einer Lösung für eine Verbindung (Connector). Ermöglicht es, dass derselbe Flow in verschiedenen Umgebungen verschiedene Verbindungen nutzt (z. B. Dev-SharePoint vs. Prod-SharePoint).

**Connector** → Fertig gebaute Integration zu einem externen Dienst (z. B. SharePoint Connector, Teams Connector, SAP Connector). Power Platform hat über 1.000 vorgefertigte Connectors.

**Custom Connector** → Selbst erstellter Connector für eine REST-API, die keinen Standard-Connector hat.

**Customization Prefix** → Kürzel (z. B. `cr_`, `contoso_`) das automatisch vor alle benutzerdefinierten Tabellen- und Spaltennamen in Dataverse gesetzt wird. Wird durch den **Publisher** festgelegt und kann nach Erstellung nicht mehr geändert werden.

---

## D

**Dataverse** → Relationale Datenbankplattform der Power Platform (basierend auf Azure SQL). Bietet integriertes Sicherheitsmodell, Geschäftslogik-Schichten, OData-API und ALM-Integration. Das Herzstück professioneller Power Platform-Lösungen.

**Dataverse Web API** → REST-Schnittstelle auf Basis von OData v4 für den programmatischen Zugriff auf Dataverse-Daten. Authentifizierung ausschließlich über Entra ID (Bearer Token).

**DLP** → Abkürzung für **Data Loss Prevention** (s. dort).

**Data Loss Prevention (DLP)** → Richtlinien im Power Platform Admin Center, die festlegen, welche Connectors in einer Umgebung verwendet werden dürfen und welche miteinander kombiniert werden dürfen. Verhindert, dass sensible Daten in unerwünschte externe Dienste fließen.

**Dead Letter Queue (DLQ)** → Sonderwarteschlange im Azure Service Bus. Nachrichten, die nach maximaler Anzahl von Zustellversuchen nicht verarbeitet werden konnten, landen hier automatisch zur manuellen Prüfung.

---

## E

**Elastic Table** → Spezielle Dataverse-Tabelle, die auf Azure Cosmos DB (NoSQL) statt Azure SQL basiert. Für Hochvolumen-Szenarien (Millionen von Datensätzen, IoT-Daten, Logs). Eingeschränkte Beziehungs- und Feature-Unterstützung im Vergleich zu Standard-Tabellen.

**Environment Variable** → _Umgebungsvariable_ — Benannter Konfigurationswert in einer Lösung, der je nach Umgebung unterschiedlich sein kann (z. B. eine URL, die in Dev anders ist als in Prod). Ermöglicht konfigurationsfreies Deployment.

**Entra ID** → Microsofts Identitäts- und Zugriffsverwaltungsdienst (früher: Azure Active Directory / Azure AD / AAD). Stellt Tokens für alle Azure- und Microsoft-365-Dienste aus.

**ExecuteMultiple** → Dataverse API-Operation, die mehrere Einzeloperationen (Create, Update, Delete) in einem einzigen HTTP-Request bündelt. Reduziert API-Aufrufe und verbessert Performance bei Massen-Importen.

---

## F

**FetchXML** → XML-basierte Abfragesprache für Dataverse-Daten. Alternative zu OData-Filtern für komplexere Abfragen. Wird in SSRS-Berichten, Advanced Find und Plugins verwendet.

**Fit-Gap-Analyse** → Architekturwerkzeug, bei dem jede Anforderung bewertet wird: Kann die Plattform das von Haus aus (Fit), mit Konfiguration (Partial Fit), oder gar nicht (Gap)?

**Flow** → Kurzbezeichnung für einen **Power Automate Flow** — automatisierter Workflow, der Aktionen ausführt, wenn ein Ereignis eintritt oder ein Zeitplan greift.

---

## G

**Go-Live** → Zeitpunkt, an dem eine Anwendung für echte Endnutzer freigeschaltet wird (Produktivbetrieb startet).

**Governance** → Gesamtheit der Regeln, Prozesse und Werkzeuge, die sicherstellen, dass die Power Platform im Unternehmen kontrolliert, sicher und nachhaltig genutzt wird.

---

## I

**IOrganizationService** → .NET-Interface in Dataverse für den programmatischen Zugriff auf Dataverse-Operationen aus Plugins. Vergleichbar mit einem SDK-Client.

**Isolation Mode** → Sandbox-Ausführungsmodus für Dataverse-Plugins. Schränkt Dateisystem- und Netzwerkzugriff ein, erhöht Sicherheit und Stabilität.

---

## J

**JS** → Abkürzung für **JavaScript** — Skriptsprache für clientseitige Logik in Model-Driven Apps (Web Resources). Wird für Formularvalidierungen, Feldlogik und UI-Anpassungen verwendet.

---

## L

**Lab** → Kurseinheit mit Theorie + Übung + Lösung. Jedes Lab ist in einem eigenen Ordner (`XXXX-name/`) gespeichert.

**Layer / Solution Layer** → Ebene im Solution-Layering-System. Wenn mehrere Lösungen dieselbe Komponente definieren, gewinnt die Lösung mit dem höchsten Layer.

**Lösung** → s. **Solution**.

---

## M

**Maker** → Nutzer, der mit Power Platform Anwendungen erstellt (oft ohne tiefen Code-Hintergrund). Ein Maker ist nicht dasselbe wie ein Entwickler oder Solution Architect.

**Managed Environment** → Premium-Feature für Power Platform Umgebungen mit erweiterten Governance-Kontrollen: IP-Firewall, Solution Checker Enforcement, Sharing-Limits, Maker-Willkommensinhalt.

**Managed Solution** → Exportierter, geschützter Lösungscontainer für Test- und Produktivumgebungen. Komponenten können nicht direkt bearbeitet werden — nur durch Updates der Lösung.

**MDA** → Abkürzung für **Model-Driven App** (s. dort).

**Model-Driven App (MDA)** → App-Typ in Power Apps, der vollständig auf Dataverse basiert und automatisch ein UI aus dem Datenmodell generiert. Ideal für strukturierte Geschäftsprozesse mit komplexen Datenmodellen.

---

## O

**OData** → _Open Data Protocol_ — Standardprotokoll für REST-APIs mit eingebauten Filter-, Sortier- und Paginierungsmechanismen (`$filter`, `$select`, `$orderby`, `$top`). Dataverse Web API basiert auf OData v4.

---

## P

**pac** → Abkürzung für **Power Apps CLI** (Command Line Interface) — Kommandozeilenwerkzeug von Microsoft für Power Platform-Entwickler. Ermöglicht Export, Import, Packen und Entpacken von Lösungen, Authentifizierungsverwaltung und vieles mehr. Vollständig: `pac` steht für **P**ower **A**pps **C**LI.

**pac CLI** → s. **pac**.

**Plugin** → Benutzerdefinierter .NET-Code, der in die Dataverse-Verarbeitungspipeline eingehängt wird. Läuft serverseitig, kann synchron (transaktional) oder asynchron ausgeführt werden.

**Plugin Registration Tool (PRT)** → Grafisches Tool von Microsoft zum Registrieren von Plugins und Webhooks in Dataverse. Wird über NuGet heruntergeladen.

**PL-600** → Prüfungsbezeichnung für _Microsoft Power Platform Solution Architect_ — die Zertifizierung, auf die dieser Kurs vorbereitet.

**Power Apps CLI** → s. **pac**.

**Power Automate** → Low-Code-Automatisierungsplattform der Power Platform. Erstellt automatisierte Workflows (Flows) zwischen Apps und Diensten.

**Power Pages** → Portal-Plattform für externe Nutzer (Kunden, Lieferanten, Partner), die keinen Power Platform-Account haben. Verbindet sich mit Dataverse.

**Power Platform** → Microsoft-Produktfamilie: Power Apps, Power Automate, Power BI, Power Pages, Copilot Studio — alle auf Dataverse aufbauend.

**Power Platform Build Tools** → s. **Build Tools**.

**Power Platform Pipelines (PP Pipelines)** → Eingebautes ALM-Feature in Power Platform (ohne Azure DevOps) zum gesteuerten Deployment von Lösungen zwischen Umgebungen. Geeignet für kleinere Teams.

**PP** → Abkürzung für **Power Platform**.

**PP Pipelines** → Abkürzung für **Power Platform Pipelines** (s. dort).

**Pre-Validation** → Erste Phase der Dataverse Plugin-Pipeline. Läuft vor dem Datenbankzugriff. Geeignet für Validierungen, die eine Operation abbrechen sollen.

**Pre-Operation** → Zweite Phase der Dataverse Plugin-Pipeline. Läuft nach dem Datenbankzugriff, aber vor der endgültigen Speicherung. Geeignet zum Ändern von Feldwerten.

**Post-Operation** → Dritte Phase der Dataverse Plugin-Pipeline. Läuft nach der Speicherung. Synchron: kann noch Rollback auslösen. Asynchron: läuft nach der Transaktion, geeignet für Folgeaktionen.

**PRT** → Abkürzung für **Plugin Registration Tool** (s. dort).

**Publisher** → Besitzer-Entität einer Lösung in Dataverse. Legt das **Customization Prefix** fest. Sollte zu Projektbeginn sorgfältig gewählt werden.

---

## R

**RLS** → Abkürzung für **Row-Level Security** — Zeilensicherheit (s. dort).

**REST** → _Representational State Transfer_ — Architekturstil für HTTP-APIs. Die meisten modernen APIs (inkl. Dataverse Web API) sind REST-konform.

**Retry-After** → HTTP-Antwort-Header, der bei einer 429-Antwort angibt, wie viele Sekunden gewartet werden soll, bevor erneut versucht werden darf.

**Rollup-Spalte** → Dataverse-Feldtyp, der einen aggregierten Wert über verknüpfte Datensätze berechnet (SUM, COUNT, MIN, MAX, AVG). Wird nicht in Echtzeit, sondern asynchron alle 12 Stunden aktualisiert.

**Row-Level Security (RLS)** → Mechanismus zur Einschränkung des Datenzugriffs auf Datensatz-Ebene (nicht Tabellen-Ebene). In Dataverse über Business Units, Eigentuemer und Zugriffstiefe der Sicherheitsrolle realisiert.

---

## S

**SA** → Abkürzung für **Solution Architect** (s. dort).

**Sandbox** → Nicht-Produktive Umgebung für Entwicklung und Tests. Auch: Ausführungsmodus für Plugins (Isolation Mode = Sandbox).

**SAS-Token** → _Shared Access Signature Token_ — Zeitlich begrenzter Zugriffstoken für Azure-Dienste (z. B. Azure Service Bus). Ersatz für vollständige Zugangsdaten bei eingeschränktem Zugriff.

**SDK** → _Software Development Kit_ — Sammlung von Bibliotheken und Tools für die Entwicklung mit einem bestimmten System. Dataverse hat ein .NET-SDK für Plugin-Entwicklung.

**Sicherheitsrolle** → Sammlung von Berechtigungen in Dataverse, die einem Nutzer oder Team zugewiesen werden. Definiert welche Tabellen, Aktionen und Zugriffstiefen erlaubt sind.

**Solution** → _Lösung_ — Container in Power Platform, der Anpassungen und Komponenten für den Transport zwischen Umgebungen bündelt. Basis für ALM.

**Solution Architect (SA)** → Rolle, die für die Gesamtarchitektur einer Power Platform-Lösung verantwortlich ist. Trifft strukturelle und langfristige Architekturentscheidungen.

**Solution Checker** → Automatisches Prüfwerkzeug, das Lösungen auf Best-Practice-Verstöße, Performance-Probleme und Sicherheitslücken analysiert.

**Solution Layering** → Mechanismus, durch den mehrere Lösungen dieselbe Komponente überlagern können. Die Lösung mit dem höchsten Layer (zuletzt importiert / aktiver Layer) gewinnt.

**SPL** → Abkürzung für **Service Protection Limits** (s. dort).

**Service Protection Limits (SPL)** → Eingebaute Schutzmechanismen der Dataverse Web API: max. 6.000 Anfragen / 5 Minuten, max. 20 Minuten Ausführungszeit / 5 Minuten, max. 52 parallele Anfragen — jeweils pro Nutzer/App-User.

---

## T

**Tenant** → Microsoft-365-/Azure-Mandant — die logische Organisation eines Unternehmens in der Microsoft-Cloud. Alle Power Platform-Umgebungen eines Unternehmens gehören zu einem Tenant.

**TTL** → _Time to Live_ — Zeitraum, nach dem ein Datensatz oder eine Nachricht automatisch gelöscht wird. In Elastic Tables und Azure Service Bus konfigurierbar.

---

## U

**UAT** → _User Acceptance Testing_ — Abnahmetest durch den Fachbereich. Wird in einer dedizierten Test-/UAT-Umgebung durchgeführt, nie in der Produktivumgebung.

**Umgebung** → _Environment_ — Isolierter Container in Power Platform mit eigenem Dataverse, eigenen Apps, Flows und Konfigurationen. Gleichzeitig die stärkste Sicherheitsgrenze.

**Unmanaged Solution** → Nicht geschützte Lösung in der Entwicklungsumgebung. Alle Komponenten sind frei editierbar. Wird für die Entwicklung verwendet, nie in Produktivumgebungen deployt.

---

## V

**Virtual Table** → _Virtuelle Tabelle_ — Dataverse-Tabelle, die Daten aus einer externen Quelle (REST-API, SharePoint, etc.) live abfragt und als native Dataverse-Tabelle darstellt. Keine lokale Datenspeicherung.

---

## W

**Webhook** → HTTP-Endpunkt, an den Dataverse bei bestimmten Ereignissen automatisch eine Benachrichtigung sendet (HTTP POST mit JSON-Payload).

---

## X

**XRM** → Abkürzung für _Extensible Relationship Management_ — historische interne Bezeichnung für die Dataverse-/CRM-Plattform. Taucht noch in älteren SDK-Namespaces auf (z. B. `Microsoft.Xrm.Sdk`).

---

## Z

**Zugriffstiefe** → Definiert in einer Sicherheitsrolle, auf welche Datensätze ein Nutzer zugreifen darf: `User` (nur eigene), `Business Unit` (eigene BU), `Parent: Child Business Units` (BU + Untereinheiten), `Organization` (alles).
