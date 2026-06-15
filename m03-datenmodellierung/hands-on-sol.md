# Hands-on-Lösung M03 — Datenmodellierung

> Schritt-für-Schritt-Anleitung zur `hands-on.md`

---

## Lösung Aufgabe 1 — Tabellen anlegen

**Wo:** [make.powerapps.com](https://make.powerapps.com) → `VisitTrack-Dev` → **Dataverse** → **Tables** → **+ New table** → **Add columns and data**

**Tabelle 1: Besuch**

1. Klicke **+ New table** → „Add columns and data"
2. **Display name:** `Besuch`
3. **Plural name:** `Besuche` (automatisch, prüfen)
4. Unter **Advanced options:**
   - **Schema name:** `vt_besuch` (Prefix `vt_` kommt vom Publisher — wenn noch kein Publisher: erst Publisher in M08 anlegen, oder vorerst Standard-Prefix nutzen)
   - **Ownership:** `User or team` ← Pflicht! ADM soll nur eigene Besuche sehen
5. Klicke **Save**

**Tabelle 2: Arzt**

1. **+ New table** → Display name: `Arzt`, Plural: `Ärzte`
2. **Ownership:** `Organization` ← Arzt-Stammdaten gehören keinem einzelnen Nutzer
3. Klicke **Save**

**Tabelle 3: Klinik**

1. **+ New table** → Display name: `Klinik`, Plural: `Kliniken`
2. **Ownership:** `Organization`
3. Klicke **Save**

**Warum User-owned für Besuch, aber Organization-owned für Arzt/Klinik?**

- Besuch: Jeder ADM soll nur seine eigenen Besuche sehen → Eigentümer = der ADM der ihn anlegt
- Arzt/Klinik: Stammdaten — jeder soll lesen können, niemand „besitzt" einen Arzt

---

## Lösung Aufgabe 2 — Felder ergänzen

**Wo:** Tabelle `vt_besuch` → **Columns** → **+ New column**

**Feld 1: Besuchsdatum**

1. **Display name:** `Besuchsdatum`
2. **Data type:** `Date and time` → wähle **Date only**
3. **Required:** `Business required` ← Pflichtfeld
4. Klicke **Save**

**Feld 2: Besuchsstatus**

1. **Display name:** `Besuchsstatus`
2. **Data type:** `Choice`
3. Klicke **+ New choice** (lokale Choice für diese Tabelle)
4. Werte hinzufügen: `Geplant` · `Durchgeführt` · `Abgesagt`
5. **Default value:** `Geplant`
6. Klicke **Save**

**Feld 3: Besuchsnotiz**

1. **Display name:** `Besuchsnotiz`
2. **Data type:** `Text` → wähle **Multiline text**
3. **Required:** `Optional`
4. Klicke **Save**

**Feld 4: Dauer in Minuten**

1. **Display name:** `Dauer in Minuten`
2. **Data type:** `Whole number`
3. **Minimum value:** `0`
4. **Maximum value:** `480` (= 8 Stunden)
5. Klicke **Save**

---

## Lösung Aufgabe 3 — Beziehungen erstellen

**Wo:** Tabelle `vt_besuch` → **Relationships** → **+ New relationship** → **Many-to-one**

**Beziehung 1: Arzt → Besuch**

1. Öffne Tabelle `vt_besuch`
2. Relationships → **+ New relationship** → **Many-to-one**
3. **Related table (one):** `Arzt`
4. **Lookup column display name:** `Arzt`
5. Klicke **Done** → **Save table**

_Bedeutung: Viele Besuche gehören zu einem Arzt. Der Lookup-Wert auf dem Besuch zeigt welcher Arzt besucht wurde._

**Beziehung 2: Klinik → Arzt**

1. Öffne Tabelle `vt_arzt`
2. Relationships → **+ New relationship** → **Many-to-one**
3. **Related table (one):** `Klinik`
4. **Lookup column display name:** `Klinik`
5. Klicke **Done** → **Save table**

---

## Lösung Aufgabe 4 — Formelspalte

**Wo:** Tabelle `vt_besuch` → **Columns** → **+ New column**

1. **Display name:** `Besuchtitel`
2. **Data type:** `Formula`
3. Formel eingeben:

```
Text(vt_besuchsdatum, "DD.MM.YYYY") & " - " & LookUp(vt_arzt, vt_arztid = vt_besuch.vt_arzt, fullname)
```

Einfachere Alternative wenn Beziehung als `vt_arzt_id` angelegt:

```
Text(vt_besuchsdatum, "DD.MM.YYYY") & " - " & vt_arzt_id.fullname
```

4. Klicke **Save**

**Warum Formelspalte statt berechneter Spalte?**
Formelspalten werden beim Abruf berechnet — sie sind immer aktuell. Berechnete Spalten (deprecated) werden nur bei Speicherung berechnet und können veraltete Werte zeigen.
