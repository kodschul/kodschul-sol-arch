# pac CLI — Power Apps CLI Befehlsreferenz

**pac** steht für **P**ower **A**pps **C**LI — das offizielle Kommandozeilenwerkzeug von Microsoft für die Power Platform.

**Installation:**
```bash
# Als .NET Tool
dotnet tool install --global Microsoft.PowerApps.CLI.Tool

# Oder via MSI-Installer: https://aka.ms/PowerAppsCLI
```

**Version prüfen:**
```bash
pac --version
```

---

## Authentifizierung (`pac auth`)

Bevor Befehle gegen eine Umgebung ausgeführt werden können, muss eine Authentifizierung eingerichtet werden.

| Befehl | Wozu | Beispiel |
|---|---|---|
| `pac auth create` | Neue Verbindung anlegen (Browser-Login) | `pac auth create --url https://org.crm.dynamics.com` |
| `pac auth create --kind ServicePrincipal` | Verbindung mit Service Principal (für Pipelines) | `pac auth create --url https://org.crm.dynamics.com --kind ServicePrincipal --applicationId <id> --clientSecret <secret> --tenant <tenantId>` |
| `pac auth list` | Alle gespeicherten Verbindungen anzeigen | `pac auth list` |
| `pac auth select` | Aktive Verbindung wechseln | `pac auth select --index 2` |
| `pac auth delete` | Verbindung entfernen | `pac auth delete --index 1` |
| `pac auth clear` | Alle Verbindungen entfernen | `pac auth clear` |

**Service Principal (SP):** Technisches Konto in Entra ID (früher Azure AD) für nicht-interaktive Authentifizierung — z. B. in Azure DevOps-Pipelines. Benötigt eine **App-Registrierung** in Entra ID und einen **Application User** in Dataverse.

---

## Lösungen verwalten (`pac solution`)

| Befehl | Wozu | Beispiel |
|---|---|---|
| `pac solution list` | Alle Lösungen in der aktiven Umgebung anzeigen | `pac solution list` |
| `pac solution export` | Lösung aus Umgebung exportieren (als .zip) | `pac solution export --name MeineLoesung --path ./MeineLoesung.zip --managed false` |
| `pac solution export --managed` | Als Managed Solution exportieren | `pac solution export --name MeineLoesung --path ./MeineLoesung_managed.zip --managed true` |
| `pac solution import` | Lösung in Umgebung importieren | `pac solution import --path ./MeineLoesung.zip` |
| `pac solution import --async` | Import asynchron (bei großen Lösungen) | `pac solution import --path ./MeineLoesung.zip --async` |
| `pac solution unpack` | .zip entpacken in Einzeldateien (für Git) | `pac solution unpack --zipfile ./MeineLoesung.zip --folder ./src/MeineLoesung` |
| `pac solution pack` | Einzeldateien zu .zip zusammenbauen | `pac solution pack --zipfile ./MeineLoesung.zip --folder ./src/MeineLoesung --managed true` |
| `pac solution publish` | Alle Anpassungen in der Umgebung veröffentlichen | `pac solution publish` |
| `pac solution check` | Solution Checker ausführen (Qualitätsprüfung) | `pac solution check --path ./MeineLoesung.zip --geo Europe` |
| `pac solution upgrade` | Solution Upgrade nach Import anwenden | `pac solution upgrade --solution-name MeineLoesung` |
| `pac solution delete` | Lösung aus Umgebung löschen | `pac solution delete --solution-name MeineLoesung` |
| `pac solution clone` | Lösung aus Umgebung klonen (lokal + unpack) | `pac solution clone --name MeineLoesung --outputDirectory ./src` |

### `unpack` vs. normaler Export — Unterschied

```
Normaler Export:      MeineLoesung.zip  ← Binärdatei, nicht Git-freundlich
pac solution unpack:  src/MeineLoesung/
                        ├── customizations.xml
                        ├── solution.xml
                        ├── Entities/
                        │   └── account/
                        │       ├── FormXml/
                        │       └── Views/
                        └── Workflows/
```

Im Git-Repository wird der **entpackte Ordner** eingecheckt, nicht die ZIP. Die ZIP wird bei Bedarf (z. B. in einer Pipeline) per `pac solution pack` aus dem Ordner erzeugt.

---

## Umgebungen verwalten (`pac admin`)

| Befehl | Wozu | Beispiel |
|---|---|---|
| `pac admin list` | Alle Umgebungen des Tenants anzeigen | `pac admin list` |
| `pac admin create` | Neue Umgebung anlegen | `pac admin create --name "PP-Dev-Shared" --type Sandbox --region europe` |
| `pac admin delete` | Umgebung löschen (⚠️ destruktiv) | `pac admin delete --environment <Env-URL>` |
| `pac admin backup` | Backup einer Umgebung erstellen | `pac admin backup --environment <Env-URL> --label "Vor-Release-1.2"` |
| `pac admin restore` | Umgebung aus Backup wiederherstellen | `pac admin restore --environment <Env-URL> --selected-backup <Backup-ID>` |
| `pac admin copy` | Umgebung kopieren | `pac admin copy --source-environment <Quelle-URL> --target-environment <Ziel-URL>` |
| `pac admin set-governance-config` | Managed-Environment-Settings setzen | `pac admin set-governance-config --environment <URL> --protection-level Standard` |

---

## Verbindungen & Connection References (`pac connection`)

| Befehl | Wozu | Beispiel |
|---|---|---|
| `pac connection list` | Alle Verbindungen in der aktiven Umgebung | `pac connection list` |
| `pac connection create` | Neue Verbindung anlegen (interaktiv) | `pac connection create --connector-name shared_sharepointonline` |

**Connection Reference Mapping beim Import:**
```bash
pac solution import \
  --path ./MeineLoesung.zip \
  --connection-references '{"cr_SharePointMain": {"connectionId": "/providers/Microsoft.PowerApps/connections/abc123"}}'
```

---

## Environment Variables setzen (`pac env`)

| Befehl | Wozu | Beispiel |
|---|---|---|
| `pac env list` | Umgebungsvariablen auflisten | `pac env list` |
| `pac env set-variable` | Wert einer Umgebungsvariable setzen | `pac env set-variable --name cr_SharePointSiteUrl --value "https://contoso.sharepoint.com/sites/prod"` |

---

## Power Apps & Flows (`pac canvas`, `pac flow`)

| Befehl | Wozu | Beispiel |
|---|---|---|
| `pac canvas pack` | Canvas App aus Quellcode packen | `pac canvas pack --sources ./CanvasApp/src --msapp ./CanvasApp.msapp` |
| `pac canvas unpack` | Canvas App (.msapp) in Quellcode entpacken | `pac canvas unpack --msapp ./CanvasApp.msapp --sources ./CanvasApp/src` |

---

## Typischer Workflow: Lokale Entwicklung → Git → Deployment

```bash
# 1. In Entwicklungsumgebung anmelden
pac auth create --url https://dev-org.crm.dynamics.com

# 2. Lösung exportieren (unmanaged)
pac solution export --name MeineLoesung --path ./MeineLoesung_unmanaged.zip --managed false

# 3. Für Git entpacken
pac solution unpack --zipfile ./MeineLoesung_unmanaged.zip --folder ./src/MeineLoesung

# 4. In Git einchecken
git add ./src/MeineLoesung
git commit -m "Feature: Neue Kontaktfelder"
git push

# 5. Als Managed Solution packen (für Test/Prod)
pac solution pack --zipfile ./MeineLoesung_managed.zip --folder ./src/MeineLoesung --managed true

# 6. In Test-Umgebung importieren
pac auth select --index 2   # Test-Umgebung auswählen
pac solution import --path ./MeineLoesung_managed.zip
```

---

## Pipeline-Kontext: Wichtige Flags

In Azure DevOps Pipelines wird pac meist mit Service Principal verwendet. Wichtige Flags:

| Flag | Bedeutung |
|---|---|
| `--environment` | Ziel-Umgebungs-URL |
| `--async` | Asynchroner Import (kein Timeout bei großen Lösungen) |
| `--skip-dependency-check` | Abhängigkeitsprüfung überspringen (⚠️ nur wenn sicher) |
| `--import-as-holding` | Import als Holding Solution (für Upgrade-Prozess) |
| `--force-overwrite` | Bestehende Lösung überschreiben |
| `--publish-changes` | Nach Import automatisch veröffentlichen |

---

## Nützliche Hilfsbefehle

```bash
pac --help                    # Alle Hauptbefehle
pac solution --help           # Alle solution-Unterbefehle
pac solution export --help    # Hilfe für einen spezifischen Befehl
pac org --help                # Org-bezogene Befehle (Alias für manche admin-Befehle)
pac telemetry disable         # Telemetrie-Datenübertragung deaktivieren
```
