# Hands-on M05 — Sicherheitsgrundlagen

> **Typ:** Praktisch im Admin Center + make.powerapps.com
> **Dauer:** ca. 40 Minuten
> **Lösung:** siehe `hands-on-sol.md`

---

## Kontext

MedPharma hat drei Vertriebsregionen: **Nord**, **Süd**, **Ost**.
Regionalmanager sehen nur Besuche ihrer eigenen Region.
ADM sehen ausschließlich ihre eigenen Besuche.

---

## Aufgabe 1 — Business Units anlegen

Lege folgende BU-Struktur in `VisitTrack-Dev` an:

```
MedPharma GmbH  (Root-BU — existiert bereits)
├── Region Nord
├── Region Süd
└── Region Ost
```

---

## Aufgabe 2 — Sicherheitsrollen erstellen

Erstelle zwei Custom-Sicherheitsrollen:

**Rolle: ADM VisitTrack**

| Tabelle | Create | Read | Write | Delete | Tiefe        |
| ------- | ------ | ---- | ----- | ------ | ------------ |
| Besuch  | Ja     | Ja   | Ja    | Ja     | User         |
| Arzt    | —      | Ja   | —     | —      | Organization |
| Klinik  | —      | Ja   | —     | —      | Organization |

**Rolle: Regionalmanager VisitTrack**

| Tabelle | Read | Tiefe         |
| ------- | ---- | ------------- |
| Besuch  | Ja   | Business Unit |
| Arzt    | Ja   | Organization  |
| Klinik  | Ja   | Organization  |

---

## Aufgabe 3 — Testnutzer zuweisen

Weise einem Testnutzer die Rolle **ADM VisitTrack** zu und ordne ihn der BU **Region Nord** zu.

Teste danach: Kann der Nutzer einen Besuch anlegen?

---

## Checkpoint ✓

- [ ] 3 Child-BUs unterhalb Root-BU angelegt
- [ ] 2 Sicherheitsrollen mit korrekten Berechtigungen und Tiefen
- [ ] Testnutzer ist in BU Nord und hat Rolle ADM VisitTrack
- [ ] Testnutzer kann Besuch anlegen und sieht nur eigene Datensätze
