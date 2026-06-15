# Tagesablauf — Power Platform Solution Architect (2 Tage)

> **Format:** Jeder Block: **Was** (Inhalt) · **Warum** (Einordnung) · **Lab** (Übungsreferenz)
> Kurs ist hands-on — Theorie dient als Rahmen, Labs sind die eigentliche Arbeit.

---

## Tag 1 — SA-Methodik · Plattform · Datenmodell · Copilot Studio

---

### 09:00 – 10:30 | Einheit 1 — Modul 01: Rolle und Methodik

| Zeit  | Block                         | Was                                                                              | Warum                                                                                      | Lab  |
| ----- | ----------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | ---- |
| 09:00 | Begrüßung & Kursziel          | Aufbau beider Tage, Bezug PL-600                                                 | Roter Faden: was du nach diesen 2 Tagen konkret kannst                                     | —    |
| 09:15 | Rolle, Mandat & Verantwortung | Was macht ein SA — was NICHT? 6 Kernbereiche, Abgrenzung zu Dev/FC               | Rollenunklarheit ist die häufigste SA-Falle: SA baut Features statt Architektur zu steuern | 0101 |
| 09:50 | Anforderungen & Discovery     | Wie strukturiert ein SA ein Discovery-Interview? Fachlich → technisch übersetzen | Ohne strukturierte Discovery baut man auf falschen Annahmen                                | 0102 |

---

### 10:30 – 10:45 | ☕ Pause

---

### 10:45 – 12:15 | Einheit 2 — Modul 01 Abschluss + Modul 02: Plattform

| Zeit  | Block                                 | Was                                                                                 | Warum                                                                                               | Lab  |
| ----- | ------------------------------------- | ----------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | ---- |
| 10:45 | Fit-Gap-Analyse                       | Fit / Partial Fit / Gap kategorisieren, Tabelle aufbauen                            | Verhindert dass Erwartungen und Plattformmöglichkeiten auseinanderfallen — PL-600 testet das direkt | 0103 |
| 11:10 | Fachanforderung → technisches Konzept | Anforderung in Komponenten (Datenmodell, Sicherheit, Automation) übersetzen         | Kernkompetenz SA: ohne Konzept wird gebaut ohne Architektur                                         | 0104 |
| 11:35 | Architekturqualität & Anti-Pattern    | Warnsignale in bestehenden Lösungen erkennen                                        | Wer Probleme früh erkennt, verhindert teure Umbauten später                                         | 0105 |
| 11:45 | Kernkomponenten im Zusammenspiel      | Canvas App vs. MDA, Power Automate, Dataverse, Copilot Studio, Power BI — wann was? | Bevor Plattformgrenzen verständlich sind, muss das Zusammenspiel klar sein                          | 0201 |

---

### 12:15 – 13:15 | Mittagspause

---

### 13:15 – 14:45 | Einheit 3 — Modul 02 Fortsetzung + Modul 03: Datenmodell

| Zeit  | Block                          | Was                                                                 | Warum                                                                                    | Lab       |
| ----- | ------------------------------ | ------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- | --------- |
| 13:15 | Dataverse als Fundament        | 4 Schichten: Sicherheit, Daten, Logik, API. Standardtabellen, CDM   | Alle nachfolgenden Themen (Sicherheit, Integration) bauen auf Dataverse auf              | 0202      |
| 13:35 | Plattformgrenzen & Limits      | Delegation (500/100k), API-Limits (SPL), Plugin-Timeout (2 min)     | SA der Limits nicht kennt, verspricht Dinge die die Plattform nicht kann                 | 0203      |
| 13:50 | Architektur um Limits herum    | Muster: Async, Pagination, Azure-Erweiterungen                      | Limits kennen reicht nicht — der SA muss wissen wie er damit umgeht                      | 0204      |
| 14:05 | Erweiterungsoptionen auswählen | Konfiguration → Formel → Flow → Plugin → Azure Function — wann was? | Jede Ebene hat ihren Platz — die richtige Wahl spart Zeit und vermeidet Over-Engineering | 0205      |
| 14:20 | Datenmodell-Strategien & Typen | Standard- vs. Custom-Tabellen, Normalisierung, Datentypen, Alt Keys | Datenmodell ist die folgenreichste Entscheidung, kaum rückgängig zu machen               | 0301+0302 |

---

### 14:45 – 15:00 | ☕ Pause

---

### 15:00 – 16:00 | Einheit 4 — Modul 03 Abschluss + Modul 04: Copilot Studio

| Zeit  | Block                          | Was                                                                           | Warum                                                                              | Lab       |
| ----- | ------------------------------ | ----------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- | --------- |
| 15:00 | Berechnungslogik               | Rollup (async, 12h!) vs. berechnet (deprecated) vs. Formelspalte (bevorzugen) | Rollup für Echtzeit = falsche Werte — klassischer Produktionsfehler                | 0303      |
| 15:20 | Speicher & Tabellenarten       | Database/File/Log Storage. Standard vs. Elastic (Cosmos) vs. Virtual Table    | Speicherkosten sind eine Architekturentscheidung, nicht IT-Ops                     | 0304+0305 |
| 15:30 | Copilot Studio als SA-Baustein | Topics, Knowledge, Actions, Governance für Agents                             | SA-Perspektive: wann einen Agent einsetzen, welche Governance-Anforderungen gelten | 0401–0404 |

---

---

## Tag 2 — Sicherheit · Integration · ALM

---

### 09:00 – 10:30 | Einheit 1 — Modul 05: Sicherheitsgrundlagen

| Zeit  | Block                                  | Was                                                                          | Warum                                                                                      | Lab  |
| ----- | -------------------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | ---- |
| 09:00 | Wiederholung Tag 1                     | Offene Fragen, Rekapitulation                                                | Erwachsene Lernende verarbeiten Inhalte nach — 10 min sichert Retention                    | —    |
| 09:10 | Umgebungen als Sicherheitsgrenze       | Default (jeder hat Zugang!) / Sandbox / Prod / Managed Env                   | Sicherheit beginnt nicht bei Rollen — sondern: wer kommt überhaupt in die Umgebung?        | 0501 |
| 09:45 | Business Units & Hierarchien           | BU = Eigentuemer-Container. Zugriffstiefen: User, BU, Parent:Child, Org      | BUs sind der Mechanismus für Row-Level Security — ohne sie keine Datenisolation            | 0502 |
| 10:05 | Sicherheitsrollen & Berechtigungstiefe | 8 Typen inkl. Append/Append To. **Rollen kumulieren — niemals einschränken** | Das Kumulierungsprinzip wird häufig falsch verstanden — hier wird es ein für alle Mal klar | 0503 |

---

### 10:30 – 10:45 | ☕ Pause

---

### 10:45 – 12:15 | Einheit 2 — Modul 05 Abschluss + Modul 06: Erweiterte Sicherheit

| Zeit  | Block                               | Was                                                                                                                | Warum                                                                       | Lab       |
| ----- | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------- | --------- |
| 10:45 | Berechtigungsmatrix Abschluss       | Fertigstellen + Anti-Pattern: Read Organization auf alles                                                          | Gruppenreflexion verankert das Minimalprinzip stärker als jede Erklärung    | 0503      |
| 11:00 | Row-Level-Zugriff, Besitz & Sharing | User-owned vs. Org-owned. Owner Teams (bevorzugen). Access Teams. Sharing = Anti-Pattern?                          | Sharing ist die häufigste Notlösung wenn Rollenarchitektur fehlt            | 0504      |
| 11:20 | Hierarchiesicherheit                | Manager- vs. Positions-Hierarchie. Risiko: abhängig von AD-Manager-Feld                                            | Klingt einfach — funktioniert nur wenn AD-Manager-Pflege sichergestellt ist | 0601      |
| 11:35 | Teams als Sicherheitswerkzeug       | Owner / Access / AAD Group Teams — welcher Typ wann?                                                               | AAD Group Teams lösen manuelle Benutzerpflege in Dataverse                  | 0602      |
| 11:50 | Spaltensicherheit + Kombinieren     | Column-Level Security. Formular ausblenden ≠ Sicherheit (API umgeht Formular!). Review-Checkliste alle 6 Schichten | Schließt die letzte Lücke; DSGVO-relevante Felder (Gehalt, IBAN)            | 0603+0604 |

---

### 12:15 – 13:15 | Mittagspause

---

### 13:15 – 14:45 | Einheit 3 — Modul 07: Integration & APIs

> Dieser Block ist der technisch tiefste des Kurses — hier ist Zeit für Fragen und Vertiefung eingeplant.

| Zeit  | Block                              | Was                                                                                                                    | Warum                                                              | Lab       |
| ----- | ---------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ | --------- |
| 13:15 | Integrationsmuster auswählen       | Richtung, Zeitlichkeit, Auslöser. Spektrum: Connector → Custom Connector → Webhook → ASB → Event Hub                   | Falsches Muster = häufigste Ursache für Performance-Probleme       | 0701      |
| 13:50 | Dataverse Web API                  | URL-Schema, Auth **nur** via Entra ID Bearer Token, App-Registrierung + App User, OData ($select $filter $top $expand) | Grundlage aller externen Systemanbindungen                         | 0702      |
| 14:10 | Plugin-Pipeline                    | 4 Phasen: Pre-Val, Pre-Op, Post-Op sync, Post-Op async. Anti-Pattern: synchroner HTTP-Call im Plugin                   | Plugins sind der nächste Schritt wenn Flows an ihre Grenzen stoßen | 0703      |
| 14:25 | Events + Service Protection Limits | Webhooks, Azure Service Bus (kein verlorenes Event), SPL: 6000/5min = HTTP 429. Gegenmittel: ExecuteMultiple           | SPL-Probleme = häufigste Fehlerursache bei Importen                | 0704+0705 |

---

### 14:45 – 15:00 | ☕ Pause

---

### 15:00 – 16:00 | Einheit 4 — Modul 08: ALM & Deployment

| Zeit  | Block                        | Was                                                                                                             | Warum                                                                | Lab  |
| ----- | ---------------------------- | --------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- | ---- |
| 15:00 | Lösungen & Solution Layering | Managed vs. Unmanaged. Segmentierung: Foundation, Security, Config, Apps. Env Vars & Connection References      | Ohne Segmentierung und Env Vars funktionieren Pipelines nicht sauber | 0801 |
| 15:20 | Umgebungsstrategie           | Dev → Shared Dev → Test/UAT → Prod. Entwickler kein Maker-Recht in Prod. Produktivdaten in Test = DSGVO-Problem | Eine Pipeline zu Prod ohne Test-Stufe ist kein ALM                   | 0802 |
| 15:35 | PP Pipelines & Azure DevOps  | PP Pipelines (eingebaut, kein ADO nötig) vs. ADO Build Tools (YAML, CI/CD). Service Principal für Pipelines     | Vom manuellen Export zur vollautomatischen Deployment-Pipeline       | 0803 |
| 15:50 | Kursabschluss                | PL-600 Prüfungsformat, häufig getestete Themen, Registrierung, Ressourcen                                       | Klarer nächster Schritt: was jetzt noch fehlt bis zur Zertifizierung | —    |

---

## Zeitplan

```
09:00 ------------------- 10:30   Einheit 1  (90 min)
10:30 ---- 10:45          Pause
10:45 ------------------- 12:15   Einheit 2  (90 min)
12:15 ------------------- 13:15   Mittagspause
13:15 ------------------- 14:45   Einheit 3  (90 min)
14:45 ---- 15:00          Pause
15:00 ------------------- 16:00   Einheit 4  (60 min)
```

5h 30min Inhalt/Tag · 11h gesamt · 35 Labs
