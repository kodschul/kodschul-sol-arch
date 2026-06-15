# Hands-on M06 — Erweiterte Sicherheit

> **Typ:** Praktisch in Dataverse + Admin Center
> **Dauer:** ca. 35 Minuten
> **Lösung:** siehe `hands-on-sol.md`

---

## Aufgabe 1 — Spaltensicherheit für Zielquote

Das Feld **Jahres-Zielquote** darf nur von HR eingesehen werden — nicht von ADM oder Managern.

1. Füge der Tabelle `vt_besuch` ein Feld `Jahres-Zielquote` (Decimal Number) hinzu
2. Aktiviere **Column Security** für dieses Feld
3. Erstelle ein Column Security Profile: `HR-Only`
4. Weise das Profil einem HR-Testnutzer zu

Teste danach:

- Kann ein ADM-Nutzer das Feld sehen? (Erwartung: Nein)
- Kann der HR-Nutzer das Feld sehen? (Erwartung: Ja)

---

## Aufgabe 2 — AAD Group Team

Erstelle ein Azure AD Group Team in Dataverse:

- **Teamname:** `ADM-Nord-Team`
- **Typ:** AAD Group Team
- **Rolle:** ADM VisitTrack

Beantworte: Warum ist das besser als die Rolle direkt am einzelnen Nutzer?

---

## Aufgabe 3 — Sicherheits-Review

Fülle die vollständige Review-Checkliste für VisitTrack aus:

| Schicht                                  | Konfiguriert? | Wo eingestellt |
| ---------------------------------------- | ------------- | -------------- |
| Umgebungszugang (Sandbox, nicht Default) |               |                |
| Business Units (Nord / Süd / Ost)        |               |                |
| Sicherheitsrollen (ADM, Regionalmanager) |               |                |
| Row-Level Security (User-owned Besuch)   |               |                |
| Spaltensicherheit (Jahres-Zielquote)     |               |                |
| Teams (AAD Group Team)                   |               |                |

---

## Checkpoint ✓

- [ ] Feld `Jahres-Zielquote` mit aktivierter Column Security angelegt
- [ ] ADM-Nutzer kann das Feld **nicht** lesen
- [ ] HR-Nutzer kann das Feld lesen
- [ ] `ADM-Nord-Team` als AAD Group Team mit Rolle existiert
- [ ] Review-Checkliste vollständig ausgefüllt
