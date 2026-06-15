# Hands-on M03 — Datenmodellierung

> **Typ:** Praktisch in Dataverse
> **Dauer:** ca. 45 Minuten
> **Lösung:** siehe `hands-on-sol.md`

---

## Aufgabe 1 — Tabellen anlegen

Lege in `VisitTrack-Dev` folgende Custom Tables an:

| Tabellenname | Anzeigename | Typ                |
| ------------ | ----------- | ------------------ |
| `vt_besuch`  | Besuch      | User-owned         |
| `vt_arzt`    | Arzt        | Organization-owned |
| `vt_klinik`  | Klinik      | Organization-owned |

---

## Aufgabe 2 — Felder ergänzen

Füge der Tabelle **Besuch** folgende Felder hinzu:

| Feldname         | Typ            | Hinweis                                  |
| ---------------- | -------------- | ---------------------------------------- |
| Besuchsdatum     | Date Only      | Pflichtfeld                              |
| Besuchsstatus    | Choice         | Werte: Geplant / Durchgeführt / Abgesagt |
| Besuchsnotiz     | Multiline Text | Optional                                 |
| Dauer in Minuten | Whole Number   | Min 0, Max 480                           |

---

## Aufgabe 3 — Beziehungen erstellen

Erstelle folgende 1:N-Beziehungen:

- **Arzt** → kann mehrere **Besuche** haben
- **Klinik** → kann mehrere **Ärzte** haben

---

## Aufgabe 4 — Formelspalte

Erstelle in **Besuch** eine Formelspalte `Besuchtitel`:

> Besuchsdatum + " — " + Name des Arztes

---

## Checkpoint ✓

- [ ] 3 Tabellen existieren in VisitTrack-Dev
- [ ] Besuch hat alle 4 Felder mit korrekten Typen
- [ ] 2 Beziehungen angelegt und im Diagramm sichtbar
- [ ] Formelspalte gibt korrekten kombinierten Wert aus
