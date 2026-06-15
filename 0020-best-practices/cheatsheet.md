# Best Practices & Cheat Sheets — Power Platform Solution Architect

---

## 1. Solution-Strategie — Cheat Sheet

### Wann welcher Solution-Typ?

| Situation | Lösungstyp |
|---|---|
| Aktive Entwicklung in Dev-Umgebung | Unmanaged Solution |
| Import in Test-/Produktivumgebung | Managed Solution |
| Hotfix direkt in Prod bearbeiten | ❌ Niemals — immer über Deployment |
| Konfigurationswerte (URLs, Keys) | Environment Variables |
| Verbindungen (SharePoint, Teams) | Connection References |

### Solution-Segmentierung — Empfohlene Struktur

```
[Projekt]_Foundation       → Tabellen, Spalten, Beziehungen, Alt-Schlüssel
[Projekt]_Security         → Sicherheitsrollen, BU-Konfiguration
[Projekt]_Config           → Environment Variables, Connection References
[Projekt]_Automation       → Power Automate Flows, Plugins
[Projekt]_Apps             → Canvas Apps, Model-Driven Apps
```

**Deployment-Reihenfolge:** Foundation → Security → Config → Automation → Apps

### Solution Health Checklist

- [ ] Eigener Publisher mit sinnvollem Prefix (nicht `cr_` Default nutzen)
- [ ] Alle Anpassungen in einer Lösung enthalten (nicht "Default Solution" benutzen)
- [ ] Environment Variables für alle umgebungsspezifischen Werte
- [ ] Connection References für alle Connector-Verbindungen
- [ ] Solution Checker vor jedem Deployment ausgeführt
- [ ] Versionsnummer nach SemVer (Major.Minor.Patch.Build): z. B. `1.2.0.3`

---

## 2. Sicherheitsarchitektur — Cheat Sheet

### Schichten-Modell

```
Schicht 1  Azure AD / Entra ID     Wer ist der Nutzer?
Schicht 2  Umgebungsmitgliedschaft Darf er in diese Umgebung?
Schicht 3  Sicherheitsrolle        Welche Tabellen & Aktionen?
Schicht 4  BU-Hierarchie           Welche Datensätze?
Schicht 5  Spaltensicherheit       Welche Felder?
Schicht 6  Sharing / Access Teams  Ausnahmen im Einzelfall
```

### Sicherheitsrollen — Grundregeln

| Regel | Begründung |
|---|---|
| Rollen kumulieren sich — nie einschränken | Zwei Rollen → weitreichendere gewinnt |
| Systemrollen (Admin, Customizer) nur in Dev | In Prod zu gefährlich |
| Least Privilege — nur was gebraucht wird | Minimalprinzip |
| Rollen dokumentieren (Matrix) | Bei Audit nachvollziehbar |
| Nie "Organization" ohne Begründung | Zu weit, schwer rückzunehmen |

### BU-Hierarchie — Entscheidungsregeln

| Anforderung | Lösung |
|---|---|
| Nutzer sieht nur eigene Datensätze | Zugriffstiefe: User |
| Nutzer sieht alle in seiner Abteilung | Zugriffstiefe: Business Unit |
| Manager sieht Abteilung + Unterabteilungen | Zugriffstiefe: Parent:Child BUs |
| Admin / Compliance sieht alles | Zugriffstiefe: Organization |
| Datensatz soll mehrere Teams sehen | Access Team oder Sharing |

### Spaltensicherheit — Wann aktivieren?

| Feldtyp | Spaltensicherheit? |
|---|---|
| Gehalt, Bonität, Kreditlimit | ✅ Ja |
| IBAN, Kontonummer | ✅ Ja |
| Rabattstufe, Einkaufspreis | ✅ Ja |
| API-Keys, Tokens | ✅ Ja |
| Name, Adresse, Telefon | ❌ Nein |
| Status, Typ, Kategorie | ❌ Nein |

> **Merksatz:** Formular ausblenden ≠ Sicherheit. Nur Spaltensicherheit gilt für API, Export und alle Zugangswege.

---

## 3. Integrationsarchitektur — Cheat Sheet

### Muster-Auswahl

| Szenario | Empfehlung |
|---|---|
| Externes System → Dataverse, Echtzeit, niedrig | Webhook oder Flow mit HTTP-Trigger |
| Externes System → Dataverse, hoch / robust | Azure Service Bus (ASB) |
| Dataverse → Externes System, synchron | Custom Connector oder HTTP-Aktion im Flow |
| Dataverse → Externes System, asynchron / robust | Azure Service Bus (ASB) |
| Externe Daten lesen ohne Speicherung | Virtual Table |
| Regelmäßiger Batch-Import | Scheduled Flow oder Azure Data Factory |
| Hochvolumen-Streaming | Azure Event Hub |
| Externe Nutzer ohne Lizenz | Power Pages |

### Plugin vs. Power Automate Flow

| Kriterium | Plugin | Flow |
|---|---|---|
| Transaktionssicherheit (Rollback) | ✅ | ❌ |
| Externe HTTP-Aufrufe | ❌ Anti-Pattern | ✅ |
| Einfache Wartung | ❌ .NET Code | ✅ Low Code |
| Performance-kritisch | ✅ | ❌ |
| Fehlerbehandlung & Monitoring | Aufwendig | Eingebaut |

### Service Protection Limits (SPL) — Merken

```
6.000 Anfragen     pro 5 Minuten  (pro Nutzer/App-User)
20 Minuten         Ausführungszeit pro 5 Minuten
52 gleichzeitig    parallele Anfragen
→ Antwort: HTTP 429 mit Retry-After Header
```

**Lösung bei SPL-Problemen:**
1. ExecuteMultiple (Batch statt Einzeloperationen)
2. Retry-After Header respektieren
3. Mehrere App-User aufteilen
4. Asynchrone Queue-Architektur

---

## 4. ALM & Deployment — Cheat Sheet

### Umgebungstypen

| Typ | Zweck | Solutions |
|---|---|---|
| Developer Sandbox | Einzelner Entwickler | Unmanaged |
| Shared Dev | Team-Integration | Unmanaged |
| Test / UAT | Fachbereich-Abnahme | Managed |
| Pre-Prod (optional) | Lasttest | Managed |
| Production | Echtbetrieb | Managed |

### Deployment-Checkliste

- [ ] Solution Checker ohne kritische Fehler
- [ ] Environment Variables für Zielumgebung gesetzt
- [ ] Connection References mit Verbindungen verknüpft
- [ ] Backup der Zielumgebung vor Import
- [ ] Deployment in Test erfolgreich (vor Prod)
- [ ] Rollback-Plan definiert
- [ ] Fachbereich-Abnahme erfolgt
- [ ] Go-Live-Kommunikation vorbereitet

### pac CLI — Die 5 wichtigsten Befehle

```bash
# Lösung exportieren (unmanaged, für Git)
pac solution export --name MeineLoesung --path ./out.zip --managed false

# Für Git entpacken
pac solution unpack --zipfile ./out.zip --folder ./src/MeineLoesung

# Als Managed packen (für Deployment)
pac solution pack --zipfile ./managed.zip --folder ./src/MeineLoesung --managed true

# In Zielumgebung importieren
pac solution import --path ./managed.zip

# Solution Checker ausführen
pac solution check --path ./managed.zip --geo Europe
```

---

## 5. Datenmodell — Cheat Sheet

### Tabellentypen

| Typ | Einsatz | Sicherheit |
|---|---|---|
| Standard (User-owned) | Geschäftsdaten mit Eigentümer | BU-Hierarchie greift |
| Standard (Org-owned) | Referenzdaten (z. B. Währungen) | Keine Row-Isolation |
| Elastic Table | Hochvolumen, IoT, Logs | Eingeschränkt |
| Virtual Table | Externe Daten live lesen | Nur externe Quelle |

### Berechnungslogik — Wann was?

| Methode | Echtzeit? | Einschränkung | Empfehlung |
|---|---|---|---|
| Formelspalte | Ja | Komplexität begrenzt | Erste Wahl für einfache Berechnungen |
| Berechnete Spalte (Calculated) | Bei Abfrage | Deprecated → zu Formelspalte migrieren | Nicht für neue Projekte |
| Rollup-Spalte | Nein (12h Delay) | Nur Aggregation über 1:N | Für Reports, nicht für Transaktionen |
| Plugin / Flow | Ja (sync) / Async | Code nötig | Für komplexe Logik |

### Schlüsselstrategie

| Schlüsseltyp | Zweck |
|---|---|
| Primary Key (GUID) | Intern, automatisch, nie exponieren |
| Alternativschlüssel (Alt Key) | Für Integrationen — externe ID-Mapping |
| Lookup | Beziehung zu einer anderen Tabelle |

---

## 6. Architekturentscheidungen — Anti-Pattern Radar

| Anti-Pattern | Problem | Bessere Lösung |
|---|---|---|
| SA entwickelt Features selbst | Verliert Architekturüberblick | SA berät, Maker/Entwickler bauen |
| Alles in einer Solution | Kein selektives Deployment möglich | Solution-Segmentierung |
| Entwicklung in Produktivumgebung | Sofortiges Produktivitätsrisiko | Dev → Test → Prod |
| Produktivdaten in Test | DSGVO-Verstoß | Synthetische Testdaten |
| Sharing statt Rollenarchitektur | Hunderte unkontrollierter Sharing-Links | Rollenarchitektur überarbeiten |
| Synchrones Plugin mit HTTP-Aufruf | Blockiert Nutzer bei externem Fehler | Async Flow für externe Calls |
| Read:Organization auf alles | Verletzt Minimalprinzip | Gezielte Zugriffstiefen |
| Formular-Ausblendung als Sicherheit | Umgehbar per API/Advanced Find | Spaltensicherheit |
| Keine Environment Variables | Hardcoded URLs/Werte in Flows | Environment Variables |
| Keine Connection References | Flows nicht portabel zwischen Umgebungen | Connection References |
| Default-Umgebung für Produktivdaten | Alle Tenant-Nutzer haben Zugang | Dedizierte Produktivumgebung |
| Systemrollen in Produktion (Customizer) | Vollzugriff auf Datenbankstruktur | Nur in Dev, nie in Prod |
