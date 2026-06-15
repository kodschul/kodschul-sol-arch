# Hands-on M02 — Plattformarchitektur

## VisitTrack — Umgebung einrichten & Plattform erkunden

**Voraussetzung:** Microsoft 365 / Power Platform Trial-Tenant  
**Ziel:** Eine dedizierte Entwicklungsumgebung für VisitTrack anlegen und die Plattformkomponenten zuordnen.

---

## Schritt 1: Developer-Umgebung anlegen

1. Öffne [admin.powerplatform.microsoft.com](https://admin.powerplatform.microsoft.com)
2. Linke Navigation → **Environments** → **+ New**
3. Fülle aus:
   - **Name:** `VisitTrack-Dev`
   - **Type:** `Sandbox`
   - **Region:** `Europe`
   - **Add Dataverse:** `Yes`
   - **Language:** `German`
   - **Currency:** `EUR`
4. Klicke **Save** — Umgebung wird in ca. 2 Minuten bereitgestellt

> Warum Sandbox statt Default? → Default-Umgebung ist für alle Tenant-Nutzer zugänglich. Eine Sandbox gibt dir eine isolierte Arbeitsumgebung.

---

## Schritt 2: Plattformkomponenten für VisitTrack zuordnen

Öffne [make.powerapps.com](https://make.powerapps.com) und wähle oben rechts `VisitTrack-Dev`.

Notiere für jede VisitTrack-Anforderung welche Plattformkomponente du wählen würdest:

| Anforderung                                    | Komponente | Begründung |
| ---------------------------------------------- | ---------- | ---------- |
| ADM erfasst Besuch auf dem Handy               |            |            |
| Manager sieht Reports & KPIs                   |            |            |
| Tägliche Benachrichtigung bei fehlendem Besuch |            |            |
| SAP-Export um 03:00 Uhr                        |            |            |
| Chatbot: "Zeige meine heutigen Besuche"        |            |            |

---

## Schritt 3: Dataverse-Umgebung erkunden

1. In make.powerapps.com → **Dataverse** → **Tables**
2. Öffne die Tabelle **Contact**
3. Sieh dir die Felder an: Welche Standardfelder könnten für VisitTrack (Arzt-Stammdaten) wiederverwendet werden?
4. Öffne die Tabelle **Account** — passt sie für Kliniken?

Notiere deine Entscheidung: Standard-Tabelle nutzen oder Custom Table — und warum.

---

## Schritt 4: Delegationslimit testen (gedanklich)

VisitTrack soll alle Besuchsberichte der letzten 12 Monate anzeigen. Bei 120 ADM × 5 Besuche/Tag × 365 Tage = **ca. 219.000 Datensätze**.

Beantworte:

1. Was passiert in einer Canvas App wenn kein Delegations-kompatibler Filter gesetzt ist?
2. Welche Alternative wäre architektonisch besser?

---

## Checkpoint ✓

Am Ende von M02 hast du:

- [ ] Umgebung `VisitTrack-Dev` mit Dataverse existiert im Admin Center
- [ ] Plattformkomponenten-Tabelle ausgefüllt und begründet
- [ ] Entschieden ob Contact/Account für VisitTrack verwendet werden oder nicht
- [ ] Das Delegationsproblem für den 219k-Datensatz-Fall gelöst
