# Hands-on M08 — ALM & Deployment

> **Typ:** Praktisch — make.powerapps.com + pac CLI Terminal
> **Dauer:** ca. 45 Minuten
> **Lösung:** siehe `hands-on-sol.md`

---

## Aufgabe 1 — Test-Umgebung anlegen

Lege eine zweite Umgebung an:

- **Name:** `VisitTrack-Test`
- **Typ:** Sandbox
- **Region:** Europa
- **Mit Dataverse:** Ja

---

## Aufgabe 2 — Lösung erstellen und befüllen

Erstelle in `VisitTrack-Dev` eine neue Lösung:

- **Name:** `VisitTrack Foundation`
- **Publisher:** Eigener Publisher (Kürzel: `vt`, Prefix: `vt`)

Füge zur Lösung hinzu:

- Alle 3 VisitTrack-Tabellen (`vt_besuch`, `vt_arzt`, `vt_klinik`)
- Die 2 Sicherheitsrollen (ADM VisitTrack, Regionalmanager VisitTrack)

---

## Aufgabe 3 — pac CLI: Solution exportieren

Öffne ein Terminal und führe aus:

```bash
# 1. Authentifizierung gegen Dev-Umgebung einrichten
pac auth create --url https://<deine-dev-umgebung>.crm4.dynamics.com

# 2. Verfügbare Lösungen anzeigen
pac solution list

# 3. Lösung als Managed exportieren (für Deployment in Test/Prod)
pac solution export \
  --name VisitTrackFoundation \
  --path ./export \
  --managed true
```

Prüfe: Existiert die ZIP-Datei unter `./export/`?

---

## Aufgabe 4 — In Test-Umgebung importieren

```bash
# Auf Test-Umgebung wechseln
pac auth create --url https://<deine-test-umgebung>.crm4.dynamics.com

# Lösung importieren
pac solution import \
  --path ./export/VisitTrackFoundation_managed.zip
```

Prüfe danach in `VisitTrack-Test` unter **Dataverse → Tables**: Sind `vt_besuch`, `vt_arzt`, `vt_klinik` vorhanden?

---

## Aufgabe 5 — Environment Variable

Erstelle in der Lösung `VisitTrack Foundation` eine Environment Variable:

- **Name:** `SAP API Endpoint`
- **Typ:** String
- **Standardwert:** `https://dev.sap.medpharma.de/api`

Beantworte: Warum ist das besser als die URL direkt in einen Flow zu schreiben?
Was passiert beim Deployment in Prod, wenn die URL eine andere ist?

---

## Checkpoint ✓

- [ ] `VisitTrack-Test` Umgebung im Admin Center angelegt
- [ ] Lösung `VisitTrack Foundation` mit Tabellen und Rollen gefüllt
- [ ] `pac solution export` erfolgreich — ZIP-Datei unter `./export/` vorhanden
- [ ] `pac solution import` in Test erfolgreich — HTTP 200 oder Meldung „Import succeeded"
- [ ] Tabellen in `VisitTrack-Test` sichtbar
- [ ] Environment Variable angelegt und Frage schriftlich beantwortet
