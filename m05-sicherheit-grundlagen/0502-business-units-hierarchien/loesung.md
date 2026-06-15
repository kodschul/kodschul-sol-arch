# Lab 5.2 - Loesung: Business Units und Hierarchien strukturiert aufbauen

## Aufgabe 1: BU-Hierarchie

**Empfohlene Hierarchie (2 Ebenen unter Root):**

```
Root BU: Versicherung AG
├── Zentrale
│   ├── IT
│   ├── HR
│   ├── Finanzen
│   └── Compliance
├── Region Nord
│   ├── Vertrieb Nord
│   └── Schaden Nord
├── Region Sued
│   ├── Vertrieb Sued
│   └── Schaden Sued
└── Region West
    ├── Vertrieb West
    └── Schaden West
```

**Begruendung der 2-Ebenen-Struktur:**
Die Region als mittlere Ebene ist architektonisch sinnvoll, weil Regionsleitern genau auf "Parent: Child"-Ebene zugreifen muessen. Haette man Vertrieb und Schaden auf gleicher Ebene ohne Regionseltern, koennte man Regionalleiter-Zugriff nicht sauber durch BU-Tiefe loesen.

**Compliance-Loesung:** Compliance-Nutzer erhalten die Zugriffstiefe "Organization" auf die Schaden-Tabelle. Die BU-Hierarchie beeinflusst das nicht direkt - es ist eine Rollenentscheidung.

**BU-Trennung Vertrieb/Schaden:** Ja, Vertrieb und Schaden muessen in einer Region als getrennte BUs abgebildet werden. Nur so laesst sich verhindern, dass Vertriebsmitarbeiter (BU: Vertrieb Nord) die Schadensdaten der Kollegen (BU: Schaden Nord) sehen.

---

## Aufgabe 2: Zugriffstiefe

| Rolle                              | Erwarteter Datenzugriff                 | Benoetiate Zugriffstiefe     |
| ---------------------------------- | --------------------------------------- | ---------------------------- |
| Schadenbearbeiter Schaden Nord     | Nur Schaeden in BU Schaden Nord         | Business Unit                |
| Regionsleiter Region Nord          | Alle Schaeden in Schaden Nord (Kind-BU) | Parent: Child Business Units |
| Compliance-Mitarbeiter Zentrale    | Alle Schaeden aller Regionen            | Organization                 |
| Vertriebsmitarbeiter Vertrieb Nord | Kein Zugriff auf Schaden-Tabelle        | None                         |

**Hinweis zu Compliance:** Der Compliance-Nutzer sitzt in BU "Compliance" (Unterkind der Zentrale). Mit "Parent: Child" wuerde er nur die eigene BU und Unter-BUs sehen. Da Compliance keine Kinder-BUs hat, waere das nur die eigene BU. Fuer unternehmensweiten Zugriff ist "Organization" die einzige Option.

---

## Aufgabe 3: Grenzfall Befoerderung

**BU-Aenderung:**
Der Nutzer wird von BU "Region Nord" (oder "Schaden Nord", je nach bisheriger Zuordnung) in BU "Zentrale" oder "Compliance" verschoben.

**Was mit den Datensaetzen passiert:**
Die Datensaetze, deren Eigentuemer der Nutzer war, bleiben der alten BU zugeordnet (BU-Eigentuemer des Datensatzes = BU des Nutzers zum Zeitpunkt der Erstellung). Sie "wandern" nicht mit in die neue BU.

**Risiko ohne Massnahmen:**

- Die alten Schaeden in Region Nord sind nun im Besitz eines Nutzers, der in der Zentrale-BU sitzt. Je nach Rollenkonfiguration der bisherigen Kollegen in Region Nord koennen diese die Datensaetze moeglicherweise nicht mehr sehen (Eigentuemer ist nicht mehr in ihrer BU).
- Der beförderte Nutzer selbst sieht die Datensaetze weiterhin, weil er persoenlicher Eigentuemer ist.

**Empfohlene Massnahmen:**

1. Vor der BU-Verschiebung: Datensaetze des Nutzers einem Team oder einem anderen Nutzer in Region Nord uebergeben (Reassign).
2. Nach der BU-Verschiebung: Neue Sicherheitsrolle mit "Organization"-Tiefe fuer Schaden-Tabelle zuweisen.
3. Dokumentieren: Prozess fuer Befoerderungen und Abteilungswechsel als Standard-Offboarding/Onboarding-Schritt definieren.

**Best Practice:** Datensaetze sollten in der Produktion Teams als Eigentuemer haben, nicht einzelnen Nutzern. Das verhindert genau dieses Problem, weil das Team weiterhin in der richtigen BU bleibt, auch wenn einzelne Mitglieder wechseln.
