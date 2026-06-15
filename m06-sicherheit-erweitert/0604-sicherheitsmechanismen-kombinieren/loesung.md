# Lab 6.4 - Loesung: Sicherheitsmechanismen sinnvoll kombinieren

## Aufgabe 1: Risiken nach Kritikalitaet

| Rang | Befund                                     | Begruendung                                                                                                                                                                    |
| ---- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1    | System Customizer fuer 23 Nutzer           | Vollzugriff auf Datenstruktur und alle Daten. Jeder kann Tabellen, Felder, Flows loeschen. Kritischste Rolle im System, betrifft 23 Nutzer.                                    |
| 2    | Fahrerlohn ohne Spaltensicherheit          | Persoenliche Gehaltsdaten sind nach DSGVO besonders schutzwuerdig. Formular-Ausblendung ist keine Sicherheitsmassnahme - jeder kann per API oder Advanced Find das Feld lesen. |
| 3    | 847 Sharing-Beziehungen undokumentiert     | Kein Mensch weiss, wer welche Frachtauftraege aus welchem Grund sieht. Hoechstwahrscheinlich sehen viele Nutzer Daten, die sie nicht sollten. Risiko schleichend aber gross.   |
| 4    | HR mit Read-Organization auf alle Tabellen | HR hat keinen legitimen Bedarf an Frachtauftraegen. Verletzt Minimalprinzip. Weniger akut als die oberen drei, aber klar falsch.                                               |
| 5    | Alle Datensaetze in Root-BU                | Ohne BU-Struktur ist Row-Level Security faktisch nicht moeglich. Solange alle in Root-BU sind, sieht jeder mit BU-Tiefe alles.                                                 |
| 6    | Keine Rollendokumentation                  | Nicht unmittelbar ein Sicherheitsrisiko, aber das groesste Hindernis fuer jede Verbesserung. Ohne Dokumentation weiss niemand, was gerade gilt.                                |

---

## Aufgabe 2: Sofortmassnahmen vs. mittelfristige Loesungen

| Befund                            | Sofortmassnahme                                                                                                                      | Mittelfristige Loesung                                                                                                                                    |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| System Customizer fuer 23 Nutzer  | Liste aller 23 Nutzer erstellen, mit Fachabteilung klaeren, wer legitim Admin ist (vermutlich 2-3), Rest sofort entfernen            | Rollenmatrix dokumentieren, Admin-Rolle nur an IT-Admins, Review-Prozess einfuehren                                                                       |
| Fahrerlohn ohne Spaltensicherheit | Spaltensicherheit fuer Feld aktivieren, Profil fuer HR erstellen, alle anderen werden automatisch ausgesperrt                        | Alle Fahrerdaten-Felder reviewen, weitere sensible Felder sichern                                                                                         |
| 847 Sharing-Beziehungen           | Keine sofortige Loeschung (zu riskant ohne Kontext). Stattdessen: Auditbericht erstellen (welcher Nutzer, welcher Datensatz, warum?) | Rollenarchitektur ueberarbeiten, sodass legitime Zugriffe durch Rollen abgebildet werden. Sharing-Beziehungen, die durch Rollen ersetzt werden, entfernen |

---

## Aufgabe 3: Ziel-Sicherheitsarchitektur

**BU-Struktur:**

```
Root BU: Logistik GmbH
├── Zentrale
│   ├── HR
│   ├── Buchhaltung
│   └── Disposition (zentral)
├── Depot Nord
│   ├── Fahrer Nord
│   └── Disponenten Nord
├── Depot Sued
│   ├── Fahrer Sued
│   └── Disponenten Sued
└── Depot West
    ├── Fahrer West
    └── Disponenten West
```

**Sicherheitsrollen:**

| Rollenname           | Zugriff                                                                                         |
| -------------------- | ----------------------------------------------------------------------------------------------- |
| Fahrer-Basis         | Eigene Tourendaten lesen, eigene Quittungen erfassen (User-Tiefe)                               |
| Disponent-Depot      | Frachtauftraege des Depots lesen/bearbeiten (BU-Tiefe), Fahrerdaten lesen (BU-Tiefe, ohne Lohn) |
| Depotleiter          | Alle Daten des Depots + Unter-BUs (Parent:Child-Tiefe)                                          |
| HR-Fachbereich       | Fahrerdaten inkl. Lohn lesen (Org-Tiefe auf Fahrertabelle), kein Zugriff auf Frachtauftraege    |
| Buchhaltung          | Frachtauftraege lesen (Org), Rechnungsdaten bearbeiten                                          |
| Zentrale-Disposition | Alle Frachtauftraege lesen (Org), Zuweisung aendern                                             |
| IT-Admin             | System Administrator - nur 2 Personen                                                           |

**Spaltensicherheit:**

| Feld              | Profil        | Berechtigt                                            |
| ----------------- | ------------- | ----------------------------------------------------- |
| Fahrerlohn        | Lohn-Profil   | HR-Fachbereich (lesen/schreiben), Buchhaltung (lesen) |
| Kundenkreditlimit | Kredit-Profil | Buchhaltung, Zentrale-Disposition (lesen)             |
| IBAN Fahrer       | IBAN-Profil   | Buchhaltung                                           |

**Legitimes Sharing:**

- Ein Spediteur aus einem anderen Depot uebernimmt kurzfristig einen Frachtauftrag: Access Team fuer diesen Auftrag
- Externer Auditor prueft einzelne Auftraege: Zeitlich begrenztes Sharing auf spezifische Datensaetze

**Nicht legitimes Sharing:**

- Systematisches Teilen aller Auftraege eines Depots mit einem anderen Depot → dafuer Rolle mit Parent:Child anpassen
- HR soll "manchmal mal schauen koennen" → legitimen Bedarf klaeren oder ablehnen
