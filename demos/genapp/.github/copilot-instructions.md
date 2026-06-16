# Projekt: TaskTrack — Power Platform Demo App

## Kontext

Demo-Projekt für Power Platform Solution Architecture Seminar.
Stack: Canvas Apps, Dataverse, Power Automate.
Ziel: Aufgabenverwaltung für Teams (max. 50 Nutzer).
Umgebung: Power Platform DEV Environment (contoso).

## Konventionen

- Tabellenpräfix: `tt_` (Publisher: contoso)
- Power Fx Naming: `gbl*` für globale Variablen, `loc*` für lokale, `col*` für Collections
- Controls: `btnX` (Buttons), `lblX` (Labels), `galX` (Galerien), `frmX` (Forms), `txtX` (Texteingaben)
- Diagramme: immer als Mermaid
- Farben: Primär #0078D4 (Microsoft Blue), Hintergrund #F5F5F5

## Arbeitsweise des Agenten

- Einfache Lösungen bevorzugen — kein Over-Engineering für eine Demo
- Bei Dataverse-Empfehlungen: immer Row-Level Security erwähnen
- Lizenzimplikationen erwähnen wenn Premium Connectors benötigt werden (z.B. Dataverse = Premium)
- Power Fx Formeln immer mit Kommentar erklären
- Bei Screens: Mobile-First denken (auch wenn Tablet-Layout)

## Datenmodell

Haupt-Tabelle: `tt_task` (Aufgabe)

- `tt_title` — Text, Pflichtfeld, Anzeigename: Titel
- `tt_description` — Multiline Text, Anzeigename: Beschreibung
- `tt_duedate` — Date, Anzeigename: Fälligkeitsdatum
- `tt_status` — Choice, Anzeigename: Status
  - Werte: Offen (1), In Bearbeitung (2), Erledigt (3)
- `tt_assignedto` — Lookup auf systemuser, Anzeigename: Zugewiesen an
