# Hands-on-Lösung M05 — Sicherheitsgrundlagen

> Schritt-für-Schritt-Anleitung zur `hands-on.md`

---

## Lösung Aufgabe 1 — Business Units anlegen

**Wo:** [admin.powerplatform.microsoft.com](https://admin.powerplatform.microsoft.com) → Umgebung `VisitTrack-Dev` anklicken → **Settings** → **Business management** → **Business units**

1. Du siehst die Root-BU `MedPharma GmbH` (oder den Tenant-Namen)
2. Klicke auf die Root-BU → **+ New**
3. Fülle aus:
   - **Name:** `Region Nord`
   - **Parent Business:** `MedPharma GmbH` (Root)
4. Klicke **Save and close**
5. Wiederhole für **Region Süd** und **Region Ost**

**Ergebnis prüfen:** Du siehst jetzt 3 Child-BUs unterhalb der Root.

**Warum BUs vor Rollen?**
Sicherheitsrollen referenzieren Zugriffstiefen (z. B. „Business Unit"). Diese Tiefe bezieht sich auf die BU des Datensatz-Eigentümers. Ohne BU-Struktur hat die Tiefe keine Wirkung.

---

## Lösung Aufgabe 2 — Sicherheitsrollen erstellen

**Wo:** [make.powerapps.com](https://make.powerapps.com) → `VisitTrack-Dev` → **Settings** (Zahnrad oben rechts) → **Advanced settings** → neues Tab öffnet sich → **Security** → **Security roles** → **+ New**

**Rolle 1: ADM VisitTrack**

1. **Role name:** `ADM VisitTrack`
2. Wechsle zum Tab **Custom Entities**
3. Suche `vt_besuch`:
   - Klicke auf den Kreis unter **Create** → einmal klicken = User (blauer Kreis mit 1 von 4 ausgefüllt)
   - Wiederhole für **Read**, **Write**, **Delete** → alle auf User-Tiefe
4. Suche `vt_arzt`:
   - **Read** → viermal klicken = Organization (voller Kreis)
5. Suche `vt_klinik`:
   - **Read** → Organization
6. Klicke **Save and close**

**Tiefen-Übersicht (wie oft klicken):**

- 1x = User (nur eigene)
- 2x = Business Unit (eigene BU)
- 3x = Parent: Child Business Units
- 4x = Organization (alle)

**Rolle 2: Regionalmanager VisitTrack**

1. **Role name:** `Regionalmanager VisitTrack`
2. `vt_besuch` → **Read** → 2x klicken = Business Unit
3. `vt_arzt` → **Read** → Organization
4. `vt_klinik` → **Read** → Organization
5. **Save and close**

---

## Lösung Aufgabe 3 — Testnutzer zuweisen

**Wo:** Admin Center → Umgebung `VisitTrack-Dev` → **Settings** → **Users + permissions** → **Users**

1. Suche deinen Testnutzer (oder nutze deinen eigenen Account zum Testen)
2. Klicke auf den Nutzer → **Manage security roles**
3. Hake **ADM VisitTrack** an → **Save**
4. Zurück zum Nutzer → **Change business unit**
5. Wähle **Region Nord** → **OK**

**Test durchführen:**

1. Öffne [make.powerapps.com](https://make.powerapps.com) als Testnutzer (oder im InPrivate-Fenster)
2. Navigiere zu `VisitTrack-Dev` → **Dataverse** → **Tables** → `vt_besuch`
3. Klicke **+ New row**
4. Fülle Besuchsdatum und Status aus → **Save**

**Erwartung:** Zeile wird gespeichert. Wenn ein anderer Nutzer (ohne Rolle) versucht zu speichern, bekommt er „You do not have permission".
