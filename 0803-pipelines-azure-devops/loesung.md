# Lab 8.3 - Loesung: Power-Platform-Pipelines und Azure DevOps Build Tools einordnen

## Aufgabe 1: Tool-Auswahl

| Projekt | Empfohlenes Werkzeug                      | Begruendung                                                                                                                                                                                                                                                                                        |
| ------- | ----------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Alpha   | Power Platform Pipelines                  | 2 Entwickler, kein Azure DevOps, einfaches Deployment alle 2 Wochen. PP Pipelines erfordern keine externe Infrastruktur, konfigurierbar in der Admin-Oberflaeche, ausreichend fuer diesen Umfang. Keine Lernkurve fuer Azure DevOps noetig.                                                        |
| Beta    | Azure DevOps + Power Platform Build Tools | 7 Entwickler, mehrere Deployments/Woche, Qualitaetspruefungen noetig. Azure DevOps ist bereits vorhanden. Build Tools bieten Solution Checker Integration, parallele Jobs, Branch-Strategien und volle Kontrolle ueber den Deployment-Prozess. PP Pipelines haben nicht die noetige Flexibilitaet. |

---

## Aufgabe 2: Pipeline-Design fuer Projekt Beta

**Pipeline-Stages:**

**Stage 1: Export & Pack**

- Zweck: Aktuelle Loesungen aus der Dev-Umgebung exportieren
- Tasks: PowerPlatformExportSolution fuer Foundation + Automation (unmanaged)
- pac solution unpack → Dateien als Artefakt speichern
- Ausloeser: Merge in Main-Branch

**Stage 2: Build (Managed)**

- Zweck: Unmanaged Solution zu Managed Solution kompilieren
- Tasks: pac solution pack --managed → Managed ZIP erzeugen
- Artefakt: Managed ZIP-Dateien fuer Foundation + Automation

**Stage 3: Quality Gate**

- Zweck: Qualitaetspruefung vor Deployment
- Tasks: PowerPlatformRunSolutionChecker (analysiert die Loesung auf Best-Practice-Verstösse)
- Bei Fehler: Pipeline bricht ab, Entwickler erhaelt Benachrichtigung
- Bei Erfolg: Weiter zu naechster Stage

**Stage 4: Deploy Test**

- Zweck: Deployment in Test-Umgebung
- Tasks: PowerPlatformImportSolution → Test-Umgebung
- Environment Variables und Connection References automatisch setzen
- Ausfuehrung: Automatisch nach Stage 3

**Stage 5: Deploy Prod**

- Zweck: Deployment in Produktion
- Ausloeser: Manueller Approval (Environment Gate in Azure DevOps)
- Tasks: PowerPlatformImportSolution → Prod-Umgebung

**Credential-Sicherheit:**

- Service Principal in Azure AD anlegen (App-Registrierung)
- Client ID und Client Secret als Azure DevOps "Service Connection" vom Typ "Power Platform" speichern
- Nie Credentials als Klartext-Variablen - immer als Secret-Variable oder Service Connection
- Pro Umgebung eine eigene Service Connection (Dev, Test, Prod haben unterschiedliche App-User-Berechtigungen)

**Manueller Approval:**

- In Azure DevOps unter "Environments" → "Prod Environment" → "Approvals and Checks" → "Approvals" konfigurieren
- Genehmigende Person(en) definieren (z.B. IT-Lead und Projektleiter)
- Optional: Timeout nach X Stunden

---

## Aufgabe 3: pac CLI fuer Git

**Schritt-fuer-Schritt:**

**Schritt 1: Authentifizieren**

```bash
pac auth create --url https://org.crm.dynamics.com
```

Browser oeffnet sich, mit Entwickler-Account anmelden.

**Schritt 2: Loesung exportieren (normal)**

```bash
pac solution export --name MeineProjektLoesung --path ./MeineProjektLoesung.zip --managed false
```

Das ergibt eine ZIP-Datei - nicht direkt fuer Git geeignet (Binaerdatei, keine sinnvollen Diffs).

**Schritt 3: Solution unpacking fuer Git**

```bash
pac solution unpack --zipfile ./MeineProjektLoesung.zip --folder ./src/MeineProjektLoesung
```

Das entpackt die Loesung in einzelne XML- und andere Dateien in einem Ordner.

**Schritt 4: In Git einchecken**

```bash
cd ./src/MeineProjektLoesung
git add .
git commit -m "Loesung Stand 15.06.2026"
git push
```

**Schritt 5: Zurueck packen fuer Deployment**
Wenn deployt werden soll, wird das Verzeichnis wieder zu einer ZIP gepackt:

```bash
pac solution pack --zipfile ./MeineProjektLoesung.zip --folder ./src/MeineProjektLoesung --managed true
```

**Unterschied normaler Export vs. Unpacking:**

| Aspekt          | Normaler Export (.zip)  | Unpacking (Ordner)              |
| --------------- | ----------------------- | ------------------------------- |
| Format          | Binaere ZIP-Datei       | Einzelne XML, JSON, JS Dateien  |
| Git-Diff        | Nicht sinnvoll (Binaer) | Sinnvoll - Aenderungen sichtbar |
| Merge-Konflikte | Nicht moeglich          | Schwierig aber moeglich         |
| Groesse im Repo | Gross                   | Klein (nur geaenderte Dateien)  |
| Lesbarkeit      | Nein                    | Eingeschraenkt (XML)            |

**Empfehlung:** Den Entpack-Ordner in Git speichern, nicht die ZIP. Die ZIP wird bei Bedarf per Pipeline aus dem Ordner erzeugt.
