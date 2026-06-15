# Hands-on-Lösung M08 — ALM & Deployment

> Schritt-für-Schritt-Anleitung zur `hands-on.md`

---

## Lösung Aufgabe 1 — Test-Umgebung anlegen

**Wo:** [admin.powerplatform.microsoft.com](https://admin.powerplatform.microsoft.com) → **Environments** → **+ New**

1. **Name:** `VisitTrack-Test`
2. **Type:** `Sandbox`
3. **Region:** Europa
4. **Add Dataverse:** Yes
5. Klicke **Save** → warte bis Status „Ready"

**Warum eine zweite Sandbox und nicht direkt Prod?**
Test = Fachbereich-Abnahme (UAT). Der Fachbereich testet mit echten Szenarien aber ohne Produktivdaten. Prod bleibt sauber bis zur expliziten Freigabe.

---

## Lösung Aufgabe 2 — Lösung erstellen und befüllen

**Schritt 1: Publisher anlegen** (einmalig)

**Wo:** make.powerapps.com → `VisitTrack-Dev` → **Solutions** → **Publishers** → **+ New publisher**

1. **Display name:** `VisitTrack`
2. **Name:** `visittrack`
3. **Prefix:** `vt`
4. **Klicke Save**

> Der Publisher-Prefix `vt_` wird vor alle Custom-Komponenten gesetzt. Das verhindert Namenskollisionen mit anderen Lösungen.

**Schritt 2: Lösung anlegen**

**Wo:** Solutions → **+ New solution**

1. **Display name:** `VisitTrack Foundation`
2. **Name:** `VisitTrackFoundation` (kein Leerzeichen)
3. **Publisher:** `VisitTrack` (vt)
4. **Version:** `1.0.0.0`
5. Klicke **Create**

**Schritt 3: Komponenten hinzufügen**

Öffne die Lösung → **+ Add existing** → **Table**

1. Wähle `vt_besuch` → **Next** → **Add all objects** → **Add**
2. Wiederhole für `vt_arzt` und `vt_klinik`

Danach: **+ Add existing** → **Security role**

1. Wähle `ADM VisitTrack` und `Regionalmanager VisitTrack` → **Add**

---

## Lösung Aufgabe 3 — pac CLI exportieren

**Voraussetzung:** pac CLI installiert (`pac --version` im Terminal prüfen)

```bash
# Terminal öffnen (macOS: Terminal.app / Windows: PowerShell)

# 1. Neues Verzeichnis anlegen
mkdir ~/visittrack-alm && cd ~/visittrack-alm

# 2. Authentifizierung gegen Dev-Umgebung
pac auth create --url https://<deine-dev-umgebung>.crm4.dynamics.com
# Browser öffnet sich → mit deinem M365-Konto anmelden

# 3. Verfügbare Lösungen anzeigen — VisitTrackFoundation sollte erscheinen
pac solution list

# 4. Lösung als Managed exportieren
pac solution export \
  --name VisitTrackFoundation \
  --path ./export \
  --managed true

# Erwartete Ausgabe:
# Solution VisitTrackFoundation exported to ./export/VisitTrackFoundation_managed.zip
```

**Prüfen:**

```bash
ls ./export/
# Zeigt: VisitTrackFoundation_managed.zip
```

**Warum `--managed true`?**
Managed Solutions in Prod/Test können nicht direkt bearbeitet werden — das verhindert, dass jemand in Test oder Prod „mal schnell etwas ändert" ohne den ALM-Prozess zu durchlaufen. In Dev bleibt die Unmanaged-Version für die Weiterentwicklung.

---

## Lösung Aufgabe 4 — Import in Test

```bash
# Authentifizierung wechseln auf Test-Umgebung
pac auth create --url https://<deine-test-umgebung>.crm4.dynamics.com
# Browser öffnet sich → anmelden

# Welches Auth-Profil ist aktiv?
pac auth list
# Wechsle wenn nötig:
pac auth select --index 2  # je nach Nummer in der Liste

# Lösung importieren
pac solution import \
  --path ./export/VisitTrackFoundation_managed.zip \
  --activate-plugins true

# Erwartete Ausgabe:
# Solution import completed successfully
```

**Prüfen in der Oberfläche:**

1. Öffne make.powerapps.com
2. Wechsle auf `VisitTrack-Test` (oben rechts)
3. **Dataverse** → **Tables** → Suche nach `vt_besuch`
4. Tabelle ist vorhanden, aber grau / schreibgeschützt (= Managed)

---

## Lösung Aufgabe 5 — Environment Variable

**Wo:** Lösung `VisitTrack Foundation` → **+ Add new** → **Environment variable**

1. **Display name:** `SAP API Endpoint`
2. **Name:** `vt_SAPAPIEndpoint` (Prefix automatisch)
3. **Data type:** `Text`
4. **Default value:** `https://dev.sap.medpharma.de/api`
5. **Save**

**Warum besser als URL direkt im Flow?**

| URL im Flow                                                                  | Environment Variable                                       |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------- |
| Beim Deployment nach Prod muss der Flow geöffnet und manuell geändert werden | Deployment-Tool setzt automatisch den Prod-Wert            |
| Wenn 5 Flows dieselbe URL nutzen: 5x ändern                                  | 1x Environment Variable ändern → alle 5 Flows aktualisiert |
| Entwickler sieht die Prod-URL → Sicherheitsrisiko                            | URL nur in der Umgebung gespeichert, nicht im Code         |

**Beim Import in Prod:** Der Import fragt nach dem aktuellen Wert für die Environment Variable → hier `https://prod.sap.medpharma.de/api` eintragen. Kein Flow muss angefasst werden.
