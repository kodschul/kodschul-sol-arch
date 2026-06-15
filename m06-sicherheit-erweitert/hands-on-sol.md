# Hands-on-Lösung M06 — Erweiterte Sicherheit

> Schritt-für-Schritt-Anleitung zur `hands-on.md`

---

## Lösung Aufgabe 1 — Spaltensicherheit für Zielquote

**Schritt 1: Feld anlegen**

**Wo:** make.powerapps.com → `VisitTrack-Dev` → Tables → `vt_besuch` → Columns → + New column

1. **Display name:** `Jahres-Zielquote`
2. **Data type:** `Currency` oder `Decimal number`
3. **Column security:** Toggle auf **Enable** ← das ist der entscheidende Schalter
4. **Save**

> Wichtig: Column Security kann nur beim Erstellen oder danach separat aktiviert werden. Ein normales Feld wird damit zu einem geschützten Feld. Ohne Column Security Profile kann **niemand** das Feld lesen — auch nicht Admins über die App.

**Schritt 2: Column Security Profile erstellen**

**Wo:** Admin Center → Umgebung → Settings → **Users + permissions** → **Column security profiles** → **+ New**

1. **Name:** `HR-Only`
2. **Description:** `Nur HR darf Jahres-Zielquote lesen und schreiben`
3. **Save**
4. Öffne das Profil → Tab **Column permissions**
5. Suche das Feld `Jahres-Zielquote` auf Tabelle `vt_besuch`
6. Setze: **Read:** Allow, **Update:** Allow, **Create:** Allow
7. **Save**

**Schritt 3: Profil einem Nutzer zuweisen**

1. Öffne Profil `HR-Only` → Tab **Users**
2. **+ Add users** → wähle den HR-Testnutzer
3. **Save**

**Test:**

- Als ADM-Nutzer: Öffne einen Besuch in der App → Feld `Jahres-Zielquote` erscheint nicht (oder zeigt `****`)
- Als HR-Nutzer: Feld ist sichtbar und editierbar

**Warum Formular ausblenden nicht reicht:**
Wenn du das Feld nur aus dem Formular entfernst, ist es über die Web API (`$select=vt_jahreszielquote`) trotzdem lesbar. Column Security greift auf Datenbankebene — unabhängig von App, Formular oder API.

---

## Lösung Aufgabe 2 — AAD Group Team

**Wo:** Admin Center → Umgebung → Settings → Users + permissions → **Teams** → **+ Create team**

1. **Team name:** `ADM-Nord-Team`
2. **Business unit:** `Region Nord`
3. **Team type:** `AAD Security group` ← das ist der AAD Group Team Typ
4. **AAD Group:** Wähle eine vorhandene Azure AD Gruppe (oder erstelle eine in [portal.azure.com](https://portal.azure.com) → Groups → + New group)
5. **Administrator:** dein Admin-Nutzer
6. **Save**

Danach → Öffne das Team → **Manage security roles** → hake **ADM VisitTrack** an → **Save**

**Warum besser als Rolle direkt am Nutzer?**

| Direkt am Nutzer                                             | AAD Group Team                                                    |
| ------------------------------------------------------------ | ----------------------------------------------------------------- |
| Jeder neue ADM muss manuell in Dataverse eine Rolle bekommen | Neuer ADM kommt in die AAD-Gruppe → bekommt automatisch die Rolle |
| IT-Admin muss Dataverse kennen                               | IT-Admin pflegt nur Azure AD (bekannte Oberfläche)                |
| Offboarding: Rolle muss in Dataverse manuell entzogen werden | AD-Gruppe deaktivieren = Zugriff sofort weg                       |

---

## Lösung Aufgabe 3 — Sicherheits-Review

| Schicht                   | Konfiguriert? | Wo eingestellt                                                   |
| ------------------------- | ------------- | ---------------------------------------------------------------- |
| Umgebungszugang (Sandbox) | Ja            | Admin Center → Environments → VisitTrack-Dev (Typ: Sandbox)      |
| Business Units            | Ja            | Admin Center → Settings → Business Units: Nord, Süd, Ost         |
| Sicherheitsrollen         | Ja            | make.powerapps.com → Settings → Security roles                   |
| Row-Level (User-owned)    | Ja            | Tabelle vt_besuch → Ownership = User or team                     |
| Spaltensicherheit         | Ja            | Column Security auf vt_jahreszielquote aktiviert, Profil HR-Only |
| Teams                     | Ja            | Admin Center → Teams → ADM-Nord-Team (AAD Group Type)            |
