# Lab 5.3 - Loesung: Sicherheitsrollen und Berechtigungstiefe sicher einsetzen

## Aufgabe 1: Berechtigungsmatrix

| Tabelle        | Aussendienstmitarbeiter | Regionsmanager | Marketing-Analyst | Systemadmin   |
| -------------- | ----------------------- | -------------- | ----------------- | ------------- |
| Besuchsbericht | C/R/W (U)               | R (PC)         | R (Org)           | R/W/C/D (Org) |
| Kontakt        | R (Org)                 | R (Org)        | R (Org)           | R/W/C/D (Org) |
| Klinik         | R (Org)                 | R (Org)        | R (Org)           | R/W/C/D (Org) |
| Produkt        | R (Org)                 | R (Org)        | R (Org)           | R/W/C/D (Org) |
| Besuchsziel    | R/W (U)                 | R (PC)         | -                 | R/W/C/D (Org) |

**Begruendungen:**

- **Besuchsbericht Aussendienstmitarbeiter (U):** Jeder Mitarbeiter sieht nur seine eigenen Berichte. C erlaubt neue Berichte, W erlaubt Nachbearbeitung.
- **Besuchsbericht Regionsmanager (PC):** Sieht alle Berichte untergeordneter BUs (seine Aussendienstler). Kein Create, weil Regionsmanager keine eigenen Besuche erfassen.
- **Kontakt/Klinik/Produkt (Org):** Stammdaten muessen fuer alle lesbar sein. Kein Schreibrecht fuer Aussendienstler, Marketing - Stammdatenpflege ist zentral.
- **Besuchsziel (U fuer Aussendienst):** Vertraulich - jeder sieht nur seine eigenen Ziele.
- **Marketing-Analyst (-) auf Besuchsziel:** Zielvorgaben sind vertraulich, Marketing braucht nur aggregierte Berichte.

---

## Aufgabe 2: Rollenkumulierung

**Was passiert:**

- Aussendienstmitarbeiter-Rolle: Besuchsbericht Read = User (U)
- Regionsmanager-Rolle: Besuchsbericht Read = Parent:Child (PC)
- Mit beiden Rollen: Read = Parent:Child (die weitreichendere Berechtigung gewinnt)

**Ist das problematisch?**
Ja. Der Nutzer sieht nun alle Besuchsberichte seiner Region, nicht nur seine eigenen. Das war fuer die Aussendienstrolle nicht vorgesehen.

**Architektonische Loesung:**
Optionen:

1. **Temporaere Deaktivierung der Regionsmanager-Rolle** waehrend der Aussendienstphase (manuelle Verwaltung, fehleranfaellig).
2. **Team-basierter Ansatz:** Die Regionsmanager-Berechtigung ueber ein Team vergeben. Das Team wird nur dann zugewiesen, wenn der Nutzer aktiv als Regionsmanager arbeitet.
3. **Rollendesign ueberdenken:** Wenn Mitarbeiter regelmaessig zwischen beiden Funktionen wechseln, ist eine dritte kombinierte Rolle ("Aussendienst + regionale Sicht") sauberer als zwei kumulative Rollen.

---

## Aufgabe 3: Anti-Pattern

**Problem 1: Read Organization auf alle Tabellen**
Marketing-Analysten koennen Besuchsziele lesen - die als vertraulich eingestuft sind. Der Ansatz "alle sehen alles" widerspricht dem Minimalprinzip.

**Problem 2: Systemrollen in Produktion**
Basic User ist eine Systemrolle mit undokumentierten Microsoft-seitigen Aenderungen. Bei Platform-Updates koennte sich der Umfang aendern. Eigene Basisrollen sind stabiler und kontrollierbar.

**Problem 3: "Einfacher zu verwalten" ist keine Sicherheitsbegruendung**
Fuer ein Pharmaunternehmen gilt die EU-GMP-Richtlinie. Wenn ein Audit nachfragt, warum Marketing-Mitarbeiter Zielvorgaben des Aussendienstes einsehen koennen, ist "einfacher zu verwalten" keine akzeptable Antwort.

**Problem 4: Organization-Tiefe fuer Grundzugriff**
Wenn alle Nutzer standardmaessig alles lesen, verliert die BU-Hierarchie ihren Sinn. Das BU-Modell wird faktisch ausgehebelt.

**Konstruktive Korrektur:**
"Ich verstehe den Wunsch nach Einfachheit. Lass uns das loesen, indem wir Stammdaten-Tabellen (Kontakt, Klinik, Produkt) mit Organization-Tiefe freigeben - das ist legitim, weil alle Nutzer diese brauchen. Fuer transaktionale Tabellen (Besuchsbericht, Besuchsziel) verwenden wir gezielt niedrigere Tiefen. Das sind nur drei zusaetzliche Zeilen in der Konfiguration, verhindern aber ein Compliance-Problem."
