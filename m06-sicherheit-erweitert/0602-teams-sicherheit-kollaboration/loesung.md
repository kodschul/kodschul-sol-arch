# Lab 6.2 - Loesung: Teams als Sicherheits- und Kollaborationswerkzeug bewerten

## Aufgabe 1: Team-Typen zuordnen

| Anforderung                                                     | Empfohlener Team-Typ                   | Begruendung                                                                                |
| --------------------------------------------------------------- | -------------------------------------- | ------------------------------------------------------------------------------------------ |
| 1. Patientenakten gehoeren der Station                          | AAD Group Team (Owner)                 | Mitgliedschaft aus AD, Datensatz-Eigentuemerschaft moeglich, stabile Grundlage             |
| 2. Neue Station bei Verlegung erhaelt Zugriff, alte behaelt ihn | Access Team auf dem Patientendatensatz | Datensatzspezifischer Zugriff, mehrere Stationen gleichzeitig, dynamisch per Flow vergeben |
| 3. Konsiliararzt temporaer                                      | Access Team (oder manuelles Sharing)   | Einzelner Datensatz, zeitlich begrenzt, kein dauerhaftes Team noetig                       |

**Zu Anforderung 2 im Detail:**
Bei einer Verlegung soll die neue Station Zugriff erhalten, aber die alte behalten. Das bedeutet: Mehrere Stationen sollen gleichzeitig lesend auf denselben Datensatz zugreifen koennen, ohne dass einer Station der Zugriff entzogen wird. Owner-Teams koennen nur ein Eigentuemer sein. Access Teams erlauben mehrere Zugriffsberechtigte pro Datensatz.

**Empfohlener Flow:** Wenn Verlegungsdatensatz erstellt wird → Flow fuegt neue Station als Access Team hinzu → Alte Station bleibt (kein Entzug). Ggf. Archivierung nach Entlassung.

---

## Aufgabe 2: AAD Group Teams konfigurieren

**BU fuer "Station Chirurgie":**
Das Team sollte der BU "Chirurgie" zugewiesen werden (sofern diese BU existiert). Nicht der Root-BU, weil das Team sonst Root-BU-Eigentuemer von Patientenakten wuerde, was zu zu weit reichenden Sichbarkeiten fuehren kann.

**Sicherheitsrolle:**

- Create/Read/Write auf Patientenakte: Business Unit (nur eigene Stations-Akten)
- Read auf Stammdaten (Diagnosen, Medikamente): Organization
- Kein Delete fuer normale Stationsnutzer

**Synchronisierungsverzoegerung:**
Azure AD Aenderungen koennen bis zu 12 Stunden brauchen, um in Dataverse zu gelangen. In einem Krankenhaus mit Notaufnahmebetrieb ist das kritisch. IT muss wissen:

- Neuen Mitarbeiter am ersten Tag: Dataverse-Zugriff ggf. erst am naechsten Tag
- Ausgeschiedener Mitarbeiter: Zugriff bleibt bis zu 12 Stunden nach AD-Deaktivierung
- Massnahme: Kritische Deaktivierungen direkt in Dataverse vornehmen, nicht nur in AD warten

---

## Aufgabe 3: Rollenvererbung

**Was passiert:**
Der Chirurgie-Arzt erhaelt durch Hinzufuegen zum Notaufnahme-Team die Sicherheitsrolle des Teams - zusaetzlich zu seiner eigenen Chirurgie-Rolle. Rollen kumulieren sich.

Ergebnis: Er sieht nun alle Patientenakten aller Stationen mit Read-Org - also auch Akten aus der Psychiatrie, Gynaekologie, etc., die er als Chirurgie-Arzt normalerweise nie gesehen haette.

**Ist das ein Problem?**
Ja. In einem Krankenhaus unterliegen Patientendaten dem Datenschutz und dem Berufsgeheimnis. Ein Chirurg hat kein berechtigtes Interesse an psychiatrischen Patientenakten.

**Architektonische Konsequenz:**
Das Notaufnahme-Team sollte keine dauerhaft breite Sicherheitsrolle haben. Stattdessen:

- Option A: Notaufnahme-Zugriff ueber Access Teams pro Patient (nur die tatsaechlich behandelten Patienten werden freigegeben)
- Option B: Notaufnahme-Team hat eine eigene Rolle, aber Temporaer-Mitglieder erhalten diese Rolle nicht automatisch - sie werden stattdessen direkt dem Access Team des einzelnen Patienten hinzugefuegt

Die Lektion: Team-Rollen sind "alles oder nichts" fuer Mitglieder. Wer in ein Team aufgenommen wird, erhaelt alle Team-Rollen - das muss bei der Aufnahme von Gastmitgliedern bedacht werden.
