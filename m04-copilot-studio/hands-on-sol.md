# Hands-on-Lösung M04 — Copilot Studio

> Schritt-für-Schritt-Anleitung zur `hands-on.md`

---

## Lösung Aufgabe 1 — Agent erstellen

**Wo:** [copilotstudio.microsoft.com](https://copilotstudio.microsoft.com) → Umgebung oben rechts auf `VisitTrack-Dev` setzen

1. Linke Navigation → **Agents** → **+ New agent**
2. Wähle **Skip to configure** (nicht den geführten Wizard nutzen)
3. Fülle aus:
   - **Name:** `VisitTrack Assistent`
   - **Description:** `Unterstützt Außendienstmitarbeiter bei der Besuchsverwaltung`
   - **Instructions:** `Du bist ein Assistent für Außendienstmitarbeiter der MedPharma GmbH. Beantworte Fragen zu Besuchsterminen, Ärzten und Kliniken.`
   - **Language:** `Deutsch (Deutschland)`
4. Klicke **Create**

---

## Lösung Aufgabe 2 — Topic hinzufügen

**Wo:** Im Agent → linke Navigation → **Topics** → **+ Add a topic** → **From blank**

1. **Topic name:** `Heutige Besuche`
2. Im **Trigger**-Block: Klicke auf „Edit" → Füge Phrasen ein:
   - `Welche Besuche habe ich heute?`
   - `Meine heutigen Termine`
   - `Was steht heute an?`
   - `Zeige meine Besuche`
   - `Heutige Arztbesuche`
3. Klicke unter dem Trigger-Block auf **+** → **Send a message**
4. Nachricht eingeben: `Ich suche deine heutigen Besuche... (Dataverse-Verbindung folgt in M07)`
5. Klicke **Save** (oben rechts)

---

## Lösung Aufgabe 3 — System Topics erkunden

**Wo:** Topics → Tab **System** oben wechseln

**Greeting:**

- Wird ausgelöst wenn der Nutzer die Konversation startet (kein Trigger-Satz nötig)
- Standardantwort: Begrüßungsnachricht
- Anpassen: Ersetze den Standardtext mit MedPharma-spezifischer Begrüßung, z. B. „Hallo! Ich bin dein VisitTrack Assistent. Wie kann ich helfen?"

**Fallback (Conversational boosting / Escalate):**

- Wird ausgelöst wenn kein Topic zur Eingabe passt
- Standardverhalten: „Das habe ich nicht verstanden" oder Weiterleitung
- Als SA wichtig: Hier keine sensiblen Fehlerdetails anzeigen — nur freundliche Rückmeldung + Option zum Eskalieren

**Praxis-Nutzen für ADM:**

- „Welche Klinik besuche ich morgen?" → Topic mit Dataverse-Abfrage (M07)
- „Trage Besuch für Dr. Müller ein" → Topic mit Dataverse-Schreibzugriff via Flow
- „Zeige meine offenen Ziele diese Woche" → Topic + Berechtigungsprüfung

---

## Lösung Aufgabe 4 — Agent testen

**Wo:** Rechte Seite in Copilot Studio → **Test your agent** Panel

1. Klicke auf **Test your agent** (oben rechts, Chat-Icon)
2. Tippe: `Welche Besuche habe ich heute?`
3. Erwartete Antwort: `Ich suche deine heutigen Besuche... (Dataverse-Verbindung folgt in M07)`

**Wenn keine Antwort kommt:**

- Prüfe ob das Topic gespeichert wurde (Save-Button geklickt?)
- Prüfe ob die Trigger-Phrasen exakt eingetragen sind
- Klicke **Refresh** im Test-Panel
