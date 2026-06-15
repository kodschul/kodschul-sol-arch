# Lab 8.2 - Loesung: Umgebungsstrategie fuer professionelle Auslieferung aufbauen

## Aufgabe 1: Ziel-Umgebungsarchitektur

**Umgebungen:**

| Name                        | Typ                       | Zweck                                             | Dataverse |
| --------------------------- | ------------------------- | ------------------------------------------------- | --------- |
| PP-Sandbox-[Entwicklername] | Developer (je Entwickler) | Unabhaengige Entwicklung, Experimente             | Ja        |
| PP-Dev-Shared               | Sandbox                   | Team-Integration, Zusammenfuehren von Aenderungen | Ja        |
| PP-Test-UAT                 | Sandbox                   | Fachbereich-Tests, Abnahme                        | Ja        |
| PP-Prod                     | Production                | Echtbetrieb                                       | Ja        |

**Zugriffsmatrix:**

| Umgebung     | Externe Entwickler               | Interne IT                  | Fachbereich      | End-Nutzer |
| ------------ | -------------------------------- | --------------------------- | ---------------- | ---------- |
| Sandbox (je) | Environment Maker (vollstaendig) | Admin                       | Nein             | Nein       |
| Dev-Shared   | Environment Maker                | Admin                       | Nein             | Nein       |
| Test-UAT     | Kein Maker-Recht                 | Admin                       | Testnutzer-Rolle | Nein       |
| Prod         | Kein Zugang                      | Admin (nur fuer Deployment) | Nein             | Ja         |

**DSGVO-Datenschutz:**

- In Test-UAT: Ausschliesslich synthetische Testdaten (generierte Personen, fiktive Kundendaten)
- Referenzdaten (Produktkatalog, Laenderlisten) koennen aus Prod exportiert und anonymisiert importiert werden
- Transaktions- und Personaldaten werden mit Tools wie Faker oder eigenen Skripten generiert

**Erster Schritt (5 Arbeitstage):**

1. Tag 1-2: Geteilte Dev-Umgebung anlegen, Entwickler migrieren ihre Arbeit dorthin
2. Tag 3: Test-UAT-Umgebung anlegen, erste Loesung (Foundation) exportieren und importieren
3. Tag 4: Bestehende Prod-Anpassungen rueckwirkend in Loesungen aufnehmen (Solution erstellen, alle Custom-Komponenten hinzufuegen)
4. Tag 5: Entwicklungsstopp in Prod ankuendigen und durchsetzen

---

## Aufgabe 2: Datenisolationsproblem

**Antwort an den Projektmanager:**

"Ich verstehe den Wunsch nach realistischen Testdaten. Lassen Sie mich erklaeren, warum echte Kundendaten im Test ein rechtliches Problem sind.

Nach DSGVO Art. 5 duerfen personenbezogene Daten nur fuer den Zweck genutzt werden, fuer den sie erhoben wurden. Kundendaten wurden fuer den operativen Betrieb erhoben, nicht fuer Software-Tests. Wenn 30 Fachbereichsmitarbeiter in einer Test-Umgebung auf echte Kundendaten zugreifen, ist das eine zweckfremde Verarbeitung. Bei einem Datenschutz-Audit oder einer Beschwerde kann das zu Bussgeldern fuehren.

Was wir stattdessen tun: Ich generiere realistische, aber fiktive Testdaten. Wenn die Testdaten gute deutsche Namen, realistische Firmennamen, echte PLZ und Betraege in den richtigen Groessenordnungen haben, sind die Tests genauso aussagekraeftig. Ich kann das bis zum naechsten UAT-Termin vorbereiten. Der Fachbereich merkt im Test keinen Unterschied - und wir sind rechtlich sicher."

---

## Aufgabe 3: Migration aus dem Prod-Entwicklungs-Chaos

**Migrationsplan:**

**Phase 1: Bestandsaufnahme (Woche 1)**

- Alle Custom-Komponenten in Prod inventarisieren (Tabellen, Felder, Apps, Flows, Sicherheitsrollen)
- Dokumentieren welche Aenderungen "live in Prod" existieren
- Entwicklungsstopp in Prod ankuendigen: Keine neuen Aenderungen direkt in Prod ab sofort

**Phase 2: Loesungsstruktur aufbauen (Woche 1-2)**

- Neuen Publisher und Solution anlegen in Prod (oder besser in einer frischen Dev-Umgebung)
- Alle Custom-Komponenten in die Loesung aufnehmen
- Als Unmanaged Loesung exportieren → das ist die Baseline

**Phase 3: Dev-Umgebung einrichten (Woche 2)**

- Geteilte Dev-Umgebung anlegen
- Baseline-Loesung (Unmanaged) in Dev importieren
- Entwickler wechseln zu Dev

**Phase 4: Test-Umgebung (Woche 3)**

- Test-Umgebung anlegen
- Loesung als Managed exportieren und in Test importieren
- Fachbereich macht erste Tests in Test statt Prod

**Phase 5: Produktion stabilisieren (Woche 4-6)**

- Erstes regulaeres Deployment: Managed Loesung von Dev → Test → Prod
- Direkte Aenderungen in Prod sind ab jetzt verboten
- Verbleibende Unmanaged-Anpassungen in Prod werden durch Managed ersetzt

**Groesste Risiken:**

1. **Abhängige Flows/Apps die nicht in Loesungen sind:** Wenn vergessen wurde, eine Komponente in die Loesung aufzunehmen und sie deployt wird, fehlt sie in der neuen Umgebung.
2. **Verbindungen und Anmeldedaten:** Flows mit Verbindungen die in Prod funktionieren, haben in Dev/Test andere Verbindungszugangsdaten.
3. **Daten-Abhängigkeiten:** Flows die auf bestimmte Datensatz-GUIDs zeigen (z.B. ein bestimmtes Team oder einen Nutzer) funktionieren in einer anderen Umgebung nicht, weil die GUIDs sich unterscheiden → Environment Variables verwenden.
