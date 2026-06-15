# Hands-on M01 — Rolle & Methodik

> **Typ:** Konzept & Analyse — kein Tenant nötig
> **Dauer:** ca. 45 Minuten
> **Lösung:** siehe `hands-on-sol.md`

---

## Fallbeispiel: VisitTrack — MedPharma GmbH

**Situation:**
Die MedPharma GmbH hat 120 Außendienstmitarbeiter (ADM), die täglich Ärzte und Kliniken besuchen. Besuchsberichte werden aktuell in Excel gepflegt. Ein Projektmanager hat bereits zwei Maker engagiert, die eine Canvas App gebaut haben. Du wirst als Solution Architect hinzugezogen.

---

## Schritt 1: Discovery-Interview simulieren

**Dauer:** 20 Minuten (Partnerarbeit — einer spielt PM/Fachbereich, einer spielt SA)

Führe ein 10-minütiges Discovery-Gespräch anhand dieser Einstiegsfragen:

1. Wer sind die Nutzergruppen und was machen sie täglich mit der Lösung?
2. Welche Daten werden heute wo gespeichert?
3. Welche Schnittstellen zu anderen Systemen gibt es (z. B. SAP, CRM)?
4. Was sind die drei größten Probleme mit der aktuellen Excel-Lösung?
5. Was darf auf keinen Fall schiefgehen? (Risiken)

Halte die Antworten schriftlich fest — sie werden in Schritt 2 verwendet.

---

## Schritt 2: Fit-Gap-Tabelle erstellen

Trage die ermittelten Anforderungen in diese Tabelle ein:

| #   | Anforderung                                           | Kategorie | Power-Platform-Möglichkeit | Lücke / Risiko |
| --- | ----------------------------------------------------- | --------- | -------------------------- | -------------- |
| 1   | Besuchsbericht mobil erfassen                         |           |                            |                |
| 2   | Manager sieht nur eigene Region                       |           |                            |                |
| 3   | Automatische Benachrichtigung bei überfälligem Besuch |           |                            |                |
| 4   | Export ins SAP-System täglich um 03:00 Uhr            |           |                            |                |
| 5   | KPI-Report: Besuche pro ADM pro Monat                 |           |                            |                |

**Kategorien:** `Fit` · `Partial Fit` · `Gap`

---

## Schritt 3: SA-Aufgaben für VisitTrack priorisieren

Schreibe 3 Aufgaben auf, die **du als SA als erstes** angehen würdest — und begründe warum.

Hinweis: Denk an Datenmodell, Umgebungen, Sicherheit, Integrationen — bevor auch nur eine Zeile gebaut wird.

---

## Checkpoint ✓

Am Ende von M01 hast du:

- [ ] Eine ausgefüllte Fit-Gap-Tabelle für VisitTrack
- [ ] 3 priorisierte SA-Aufgaben schriftlich begründet
- [ ] Den Unterschied zwischen SA-Aufgabe und Entwickleraufgabe an einem konkreten Beispiel aus dem Gespräch benannt
