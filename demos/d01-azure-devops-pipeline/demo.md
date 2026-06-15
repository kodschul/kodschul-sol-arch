# Demo D01 — Azure DevOps CI/CD Pipeline für Power Platform

## Was du am Ende dieser Demo hast

Eine vollautomatische Pipeline in Azure DevOps die:

1. Eine Power Platform Solution aus Dataverse exportiert
2. Als Managed Solution verpackt
3. In eine Zielumgebung (Test) deployt

---

## Voraussetzungen

| Was                             | Wo besorgen                                                      |
| ------------------------------- | ---------------------------------------------------------------- |
| Azure DevOps Organisation       | [dev.azure.com](https://dev.azure.com) — kostenloser Plan reicht |
| Power Platform Umgebungen       | `VisitTrack-Dev` + `VisitTrack-Test` aus M02/M08                 |
| Solution `VisitTrackFoundation` | Aus M08 — muss in Dev existieren                                 |
| Service Principal in Entra ID   | Wird in Schritt 2 angelegt                                       |
| Power Platform Build Tools      | Wird in Schritt 4 installiert                                    |

---

## Schritt 1 — Azure DevOps Projekt anlegen

**Wo:** [dev.azure.com](https://dev.azure.com) → deine Organisation → **+ New project**

1. **Project name:** `VisitTrack`
2. **Visibility:** `Private`
3. **Version control:** `Git`
4. **Work item process:** `Scrum` (oder beliebig)
5. Klicke **Create**

Das Projekt öffnet sich automatisch.

---

## Schritt 2 — Service Principal für Pipeline-Authentifizierung

Die Pipeline läuft ohne Nutzer — sie braucht einen Service Principal statt eines menschlichen Accounts.

**Wo:** [portal.azure.com](https://portal.azure.com) → **App registrations** → **+ New registration**

1. **Name:** `VisitTrack-DevOps-SP`
2. **Single tenant**
3. Klicke **Register**
4. Notiere: **Application (client) ID** und **Directory (tenant) ID**
5. Linke Navigation → **Certificates & secrets** → **+ New client secret**
   - Description: `DevOps Pipeline`
   - Expires: `24 months`
   - Klicke **Add** → Wert sofort kopieren

**Service Principal als Application User in beiden Umgebungen registrieren:**

Wiederhole für `VisitTrack-Dev` UND `VisitTrack-Test`:

1. [admin.powerplatform.microsoft.com](https://admin.powerplatform.microsoft.com) → Umgebung → **Settings** → **Users + permissions** → **Application users** → **+ New app user**
2. App auswählen: `VisitTrack-DevOps-SP`
3. Rolle zuweisen: `System Administrator` (für Export) / `System Customizer` (für Import)
4. Klicke **Create**

---

## Schritt 3 — Git Repository einrichten

**Wo:** Azure DevOps → `VisitTrack` Projekt → **Repos**

Das Repo ist leer. Initialisiere es mit einer Readme:

1. Klicke **Initialize** (mit README)
2. Klone das Repo lokal:

```bash
git clone https://dev.azure.com/<organisation>/VisitTrack/_git/VisitTrack
cd VisitTrack
```

Erstelle diese Ordnerstruktur:

```bash
mkdir -p src pipelines
```

Erstelle eine `.gitignore`:

```bash
cat > .gitignore << 'EOF'
*.zip
export/
*.user
EOF
```

Committe:

```bash
git add .
git commit -m "Initial structure"
git push
```

---

## Schritt 4 — Power Platform Build Tools installieren

**Wo:** Azure DevOps → deine Organisation (oben links) → **Organization settings** → **Extensions** → **Browse marketplace**

1. Suche: `Power Platform Build Tools`
2. Publisher: `Microsoft`
3. Klicke **Get it free** → **Install** → wähle deine Organisation → **Proceed to organization**

---

## Schritt 5 — Service Connection anlegen

Die Pipeline muss sich gegen Power Platform authentifizieren. Das geschieht über eine Service Connection.

**Wo:** Azure DevOps → `VisitTrack` → **Project settings** (unten links) → **Service connections** → **+ New service connection**

1. Wähle **Power Platform** → **Next**
2. **Authentication method:** `Application Id and client secret`
3. Fülle aus:
   - **Server URL:** `https://<dev-umgebung>.crm4.dynamics.com`
   - **Tenant Id:** `<deine Tenant ID>`
   - **Application Id:** `<Application Client ID des SP>`
   - **Client secret:** `<Client Secret Wert>`
   - **Service connection name:** `PP-VisitTrack-Dev`
4. Hake **Grant access permission to all pipelines** an
5. Klicke **Save**

Wiederhole für `VisitTrack-Test`:

- **Server URL:** `https://<test-umgebung>.crm4.dynamics.com`
- **Service connection name:** `PP-VisitTrack-Test`

---

## Schritt 6 — Pipeline YAML schreiben

**Wo:** Im geklonten Repo lokal — erstelle `pipelines/build-and-deploy.yml`

```bash
cat > pipelines/build-and-deploy.yml << 'EOF'
trigger:
  branches:
    include:
      - main

pool:
  vmImage: 'windows-latest'

variables:
  solution_name: 'VisitTrackFoundation'
  export_path: '$(Build.ArtifactStagingDirectory)/export'

stages:

  # ─── Stage 1: Export aus Dev als Unmanaged ────────────────────────────────
  - stage: Export
    displayName: 'Export Solution from Dev'
    jobs:
      - job: ExportJob
        displayName: 'Export VisitTrackFoundation'
        steps:
          - task: PowerPlatformToolInstaller@2
            displayName: 'Install PP Tools'
            inputs:
              DefaultVersion: true

          - task: PowerPlatformExportSolution@2
            displayName: 'Export as Unmanaged'
            inputs:
              authenticationType: 'PowerPlatformSPN'
              PowerPlatformSPN: 'PP-VisitTrack-Dev'
              SolutionName: '$(solution_name)'
              SolutionOutputFile: '$(export_path)/$(solution_name).zip'
              Managed: false

          - task: PowerPlatformUnpackSolution@2
            displayName: 'Unpack Solution (source control ready)'
            inputs:
              SolutionInputFile: '$(export_path)/$(solution_name).zip'
              SolutionTargetFolder: '$(Build.SourcesDirectory)/src/$(solution_name)'
              SolutionType: 'Unmanaged'

          - publish: '$(Build.SourcesDirectory)/src'
            artifact: 'solution-src'
            displayName: 'Publish unpacked source'

  # ─── Stage 2: Als Managed verpacken ─────────────────────────────────────
  - stage: Build
    displayName: 'Build Managed Solution'
    dependsOn: Export
    jobs:
      - job: BuildJob
        displayName: 'Pack as Managed'
        steps:
          - download: current
            artifact: 'solution-src'

          - task: PowerPlatformToolInstaller@2
            displayName: 'Install PP Tools'
            inputs:
              DefaultVersion: true

          - task: PowerPlatformPackSolution@2
            displayName: 'Pack as Managed'
            inputs:
              SolutionSourceFolder: '$(Pipeline.Workspace)/solution-src/$(solution_name)'
              SolutionOutputFile: '$(Build.ArtifactStagingDirectory)/$(solution_name)_managed.zip'
              SolutionType: 'Managed'

          - publish: '$(Build.ArtifactStagingDirectory)/$(solution_name)_managed.zip'
            artifact: 'managed-solution'
            displayName: 'Publish Managed ZIP'

  # ─── Stage 3: Deployment nach Test ───────────────────────────────────────
  - stage: DeployTest
    displayName: 'Deploy to VisitTrack-Test'
    dependsOn: Build
    jobs:
      - deployment: DeployToTest
        displayName: 'Import Managed Solution'
        environment: 'VisitTrack-Test'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: PowerPlatformToolInstaller@2
                  displayName: 'Install PP Tools'
                  inputs:
                    DefaultVersion: true

                - task: PowerPlatformImportSolution@2
                  displayName: 'Import to Test'
                  inputs:
                    authenticationType: 'PowerPlatformSPN'
                    PowerPlatformSPN: 'PP-VisitTrack-Test'
                    SolutionInputFile: '$(Pipeline.Workspace)/managed-solution/$(solution_name)_managed.zip'
                    ActivatePlugins: true
                    OverwriteUnmanagedCustomizations: false
                    PublishWorkflows: true
EOF
```

Committe und pushe:

```bash
git add pipelines/build-and-deploy.yml
git commit -m "Add CI/CD pipeline for VisitTrackFoundation"
git push
```

---

## Schritt 7 — Pipeline in Azure DevOps anlegen

**Wo:** Azure DevOps → `VisitTrack` → **Pipelines** → **+ New pipeline**

1. **Where is your code?** → `Azure Repos Git`
2. Repository auswählen: `VisitTrack`
3. **Configure your pipeline** → `Existing Azure Pipelines YAML file`
4. **Path:** `/pipelines/build-and-deploy.yml`
5. Klicke **Continue** → **Run**

---

## Schritt 8 — Pipeline beobachten

Die Pipeline läuft jetzt durch 3 Stages:

```
Export (Dev) ──→ Build (Managed) ──→ Deploy (Test)
     ↓                  ↓                  ↓
  ZIP export      managed.zip        Import in Test
  + Unpack        als Artifact        + Aktivierung
  in /src
```

**Was du im Log siehst:**

- **Export stage:** `Solution exported successfully`
- **Build stage:** `Solution packed as Managed`
- **Deploy stage:** `Solution import completed`

**Prüfen nach erfolgreichem Run:**

1. Öffne [make.powerapps.com](https://make.powerapps.com) → `VisitTrack-Test`
2. **Solutions** → `VisitTrack Foundation` sollte dort erscheinen
3. Tabellen `vt_besuch`, `vt_arzt`, `vt_klinik` sind vorhanden
4. Typ der Solution: **Managed** (nicht editierbar — das ist korrekt)

---

## Was passiert bei jedem `git push` auf `main`

```
Push → Trigger → Export aus Dev → Managed verpacken → Deploy nach Test
```

Kein manueller pac-CLI-Aufruf mehr. Kein „ich exportier das kurz mal schnell".

---

## Export ohne manuellen ZIP-Download — 3 Wege

### Weg A — Pipeline manuell starten (kein Push nötig)

**Wann:** Du hast Änderungen in Dev gemacht und willst die Pipeline sofort anstoßen ohne eine Codeänderung zu pushen.

**Wo:** Azure DevOps → `VisitTrack` → **Pipelines** → deine Pipeline → **Run pipeline**

1. Klicke **Run pipeline** (blauer Button oben rechts)
2. Branch: `main`
3. Klicke **Run**

Die Pipeline exportiert die aktuelle Solution aus Dev — kein ZIP-Download, kein make.powerapps.com.

---

### Weg B — Nächtlicher automatischer Export (Scheduled Trigger)

Füge in der YAML oben einen `schedules`-Block hinzu, damit die Pipeline jeden Abend läuft:

```yaml
# Ersetze den trigger-Block in build-and-deploy.yml
trigger:
  branches:
    include:
      - main

schedules:
  - cron: "0 22 * * 1-5" # Mo–Fr um 22:00 UTC (= 23:00 MEZ)
    displayName: "Nightly export from Dev"
    branches:
      include:
        - main
    always: true # Läuft auch wenn kein neuer Commit
```

**Ergebnis:** Jeden Abend wird der aktuelle Stand aus Dev exportiert, als Managed verpackt und in Test deployt — vollautomatisch.

---

### Weg C — Export per pac CLI lokal, dann Push triggert die Pipeline

**Wann:** Du willst die Solution lokal als Quellcode in Git haben (Source-of-Truth Ansatz).

```bash
# 1. Solution aus Dev exportieren und entpacken
pac auth create --url https://<dev-umgebung>.crm4.dynamics.com
pac solution export --name VisitTrackFoundation --path ./export --managed false

# 2. Solution entpacken in /src (macht die Pipeline auch — hier lokal)
pac solution unpack \
  --zipfile ./export/VisitTrackFoundation.zip \
  --folder ./src/VisitTrackFoundation \
  --processCanvasApps

# 3. Entpackten Source-Code committen
git add src/
git commit -m "chore: sync solution from Dev $(date +%Y-%m-%d)"
git push
```

**Was dann passiert:** Der Push triggert die Pipeline → sie nimmt den Source aus Git, packt ihn als Managed, deployt nach Test.

**Vorteil:** Git-History zeigt exakt welche Komponenten sich wann geändert haben — auf Feldebene, nicht nur als undurchsichtiger ZIP.

```
src/VisitTrackFoundation/
├── Entities/
│   ├── vt_besuch/
│   │   ├── Entity.xml
│   │   └── Attributes/
│   │       ├── vt_besuchsdatum.xml
│   │       └── vt_besuchsstatus.xml
│   └── vt_arzt/
└── SecurityRoles/
    ├── ADM VisitTrack.xml
    └── Regionalmanager VisitTrack.xml
```

`git diff` auf diesen Dateien zeigt: „Feld X wurde hinzugefügt, Rolle Y wurde geändert."

---

## Troubleshooting

| Problem                                      | Ursache                                               | Lösung                                                           |
| -------------------------------------------- | ----------------------------------------------------- | ---------------------------------------------------------------- |
| `Unauthorized` im Export-Step                | Service Principal hat keine Rolle in Dev              | Application User in Dev prüfen → Rolle System Administrator      |
| `Solution not found`                         | Solution-Name stimmt nicht                            | `pac solution list` ausführen — exakter Name aus Dataverse       |
| Import schlägt fehl mit `dependency missing` | Solution hat Abhängigkeiten die in Test fehlen        | Abhängige Solutions zuerst deployen                              |
| Stage `DeployTest` wartet auf Approval       | Environment `VisitTrack-Test` hat einen Approval Gate | In ADO → Environments → VisitTrack-Test → Approvals → genehmigen |
