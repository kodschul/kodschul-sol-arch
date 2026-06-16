# Hands-On M03 — Agentic: Data Schema Generation (für Adrian)

> **Typ:** Dataverse Schema Auto-Generator bauen  
> **Dauer:** ca. 60 Minuten  
> **Lösung:** siehe `hands-on-agentic-sol.md`  
> **Werkzeuge:** Claude MCP, Dataverse CLI, OData MCP

---

## Szenario: Von Anforderungen zu Dataverse Tables in 5 Minuten

**Ausgangssituation:**
Du hast die Architektur aus M02. Jetzt brauchst du Dataverse Tabellen.

Klassisch: SA / Developer sitzt hin, denkt über Entities nach, erstellt Tabellen manuell. 45+ Minuten.

Agentic: Agent generiert Schema → Developer deployed es mit CLI.

---

## Aufgabe 1: Dataverse Schema Language definieren

**Dauer:** 15 Minuten

Definiere ein DSL (Domain-Specific Language) für Dataverse Schemas:

```yaml
# VisitTrack Schema Definition Language (VTSDL)

tables:
  - name: vt_physician
    display_name: "Arzt"
    ownership: "organization" # or "user"
    fields:
      - name: name
        type: "Text"
        required: true
        max_length: 200
      - name: specialty
        type: "Choice"
        choices: ["Allgemeinmedizin", "Chirurgie", "Pädiatrie"]
      - name: phone
        type: "Phone"
        required: false
      - name: email
        type: "Email"
      - name: address
        type: "Text"
        max_length: 500

    indexes:
      - fields: [name]
        unique: false

  - name: vt_visit
    display_name: "Besuch"
    ownership: "user"
    fields:
      - name: visit_date
        type: "Date"
        required: true
      - name: visit_time
        type: "Time"
      - name: duration_minutes
        type: "Integer"
        min: 0
        max: 480
      - name: notes
        type: "MultilineText"
        required: false
      - name: status
        type: "Choice"
        choices: ["Planned", "Completed", "Cancelled"]

    relationships:
      - name: physician_visits
        target_table: vt_physician
        cardinality: "N:1"
        cascade_delete: false

    indexes:
      - fields: [physician_id, visit_date]
        unique: false

views:
  - table: vt_visit
    name: "Visits This Month"
    filter: "visit_date >= TODAY-30"
    columns: [visit_date, physician_name, status]
    sort_by: visit_date
```

**Aufgabe:** Schreib ein vollständiges Schema für VisitTrack mit min. 3 Tabellen, Relationships, Indexes.

**Deliverable:** YAML Schema File (`visittrack-schema.yaml`).

---

## Aufgabe 2: Schema Validator Agent

**Dauer:** 15 Minuten

Definiere Validation Rules, die dein Agent prüft:

```
Validation Rules (min. 10):

1. No Redundant Fields
   Rule: Wenn Field X auch in related Table existiert → Warning
   Example: visit.physician_name UND physician.name → redundant!

2. Normalization Check
   Rule: Prüfe auf denormalized fields
   Severity: Warning (nicht Error, manchmal ok für Performance)

3. Relationship Cycles
   Rule: Keine zirkulären Dependencies (A→B→C→A)
   Severity: Error

4. Index Coverage
   Rule: Primary Access Patterns haben Indexes
   Example: visit (physician_id, visit_date) sollte indexed sein

5. Choice Field Validation
   Rule: Choice Fields brauchen min. 2 options
   Severity: Warning

6. Required Field Logic
   Rule: Fields, die in Relationships used werden, sollten required sein
   Example: visit.physician_id sollte required sein

7. Data Type Consistency
   Rule: Related Fields haben same data type
   Example: physician.id (GUID) und visit.physician_id (GUID) ✓

8. Performance: Large Text Fields
   Rule: MultilineText Fields sollten nicht indexed sein
   Severity: Warning

9. Naming Convention
   Rule: Feldnamen follow pattern (snake_case)
   Severity: Info

10. Calculated vs. Storage
    Rule: Wenn Feld berechnbar ist → als Formula? oder Storage?
    Severity: Decision Point (Agent asks)
```

**Aufgabe:** Schreib 10+ Validation Rules als YAML oder JSON.

**Deliverable:** Validation Rules File.

---

## Aufgabe 3: Denormalization Advisor

**Dauer:** 15 Minuten

Definiere einen Decision Tree für Denormalisierung:

```
Decision: Sollte ich ein Feld denormalisieren?

Input:
  - Field Name (z.B. "physician.name")
  - Access Pattern (z.B. "Show in Visit List")
  - Query Frequency (z.B. "100k/day")
  - Data Volatility (z.B. "Rarely changes")

Decision Tree:
  IF query_frequency > 10k/day
    AND related_table.field_changes_rarely
    AND performance_is_critical
    THEN: Denormalize (aber mit Sync Flow!)

  ELSE: DON'T denormalize (stay normalized)

For VisitTrack:
  Q: Sollte ich physician.name in visit denormalisieren?
  A:
    - Query Frequency: "Show in Visit List" → 100k/day? Ja, wahrscheinlich
    - Data Volatility: "Physician Namen ändern sich rarely" → Gut
    - Performance Critical: "Dashboard loading speed" → Ja
    => Denormalisieren IST OK, ABER mit Trigger Flow:
       "When physician updated → update all related visits"
```

**Aufgabe:** Schreib einen Decision Tree / Algorithm für Denormalization.

**Deliverable:** Pseudocode oder Mermaid Flowchart.

---

## Aufgabe 4: RLS Strategy Mapper

**Dauer:** 10 Minuten

Definiere wie dein Agent RLS-Strategien vorschlägt:

```
Input: Security Requirement
  "Manager sieht nur Besuche der eigenen Region"

Agent Analysis:
  1. Erkenne: "Region-basierte Sicherheit"
  2. Frage: "Haben wir eine hierarchische Org-Struktur?"
     - Wenn JA → Business Unit Strategy
     - Wenn NEIN → Field-Level Strategy
  3. Implementierung:
     - Business Unit Approach:
       - user.business_unit = region
       - visit.business_unit = region
       - Dataverse RLS: Nutzer sieht nur eigene BU
     - Field-Level Approach:
       - user.region = field
       - visit.region = field
       - Dataverse RLS: Nutzer sieht nur matching region

Output:
  {
    "strategy": "Business Unit",
    "implementation": {
      "user_field": "business_unit",
      "data_field": "business_unit",
      "rls_rule": "user.business_unit == record.business_unit"
    },
    "performance_impact": "High (10x faster than field-based)"
  }
```

**Aufgabe:** Schreib einen RLS Strategy Recommendation Engine.

**Deliverable:** Algorithm oder Pseudocode.

---

## Aufgabe 5: Auto-Generated Schema Deployment

**Dauer:** 5 Minuten

Generiere ein Deployment Script:

```bash
#!/bin/bash
# deploy-schema.sh — Generated by Agentic Schema Agent

echo "Deploying VisitTrack Schema..."

# Using Power Apps CLI (pac)
pac auth create -u $TENANT_URL -t $TENANT_ID

# Create Tables
pac table create -n vt_physician -d "Arzt" --json-schema visittrack-schema.yaml

# Create Fields
pac field create -t vt_physician -n specialty -t "Choice" --choices "Allgemeinmedizin,Chirurgie,Pädiatrie"

# Create Relationships
pac relationship create -t vt_visit --target vt_physician -n "physician_visits"

# Create Indexes
pac index create -t vt_visit -f physician_id,visit_date

# Create Views
pac view create -t vt_visit -n "Visits This Month" --filter "visit_date >= TODAY-30"

echo "✓ Schema deployed successfully"
```

**Aufgabe:**
Generiere ein Bash oder PowerShell Deployment Script basierend auf deinem Schema.

**Deliverable:** Deployment Script.

---

## Aufgabe 6: Integration mit Agentic Agent

**Dauer:** 5 Minuten

Schreib eine Prompt für einen Data Schema Generator Agent:

```
Du bist ein Dataverse Schema Experte.

Input:
  - Architecture (aus M02)
  - Requirements (aus M01)
  - Performance Constraints

Aufgaben:
1. Parse Architecture → Entities erkennen (Canvas: "Visit", "Physician")
2. Generiere Dataverse Tabellen mit Feldern
3. Erkenne Relationships (1:N, N:N)
4. Prüfe gegen Normalization Rules
5. Warne vor Denormalisierung wenn unnötig
6. Generiere RLS Strategy
7. Output: VTSDL Schema + Deployment Script

Constraints:
- Keine redundanten Felder außer für Performance
- Business Unit statt Field-Level wenn möglich
- Indexes auf Access Patterns
```

**Aufgabe:** Schreib einen vollständigen Prompt für einen Data Schema Generator Agent.

**Deliverable:** Prompt File.

---

## Aufgabe 7: Test Your Agent (Simulation)

**Dauer:** 10 Minuten

Simuliere den Agent-Output:

**Input:**

```json
{
  "architecture": {
    "components": {
      "frontend": ["Canvas App"],
      "data": ["Dataverse"],
      "backend": ["Power Automate"]
    }
  },
  "requirements": [
    { "text": "Mobile offline capture", "entities": ["Visit", "Physician"] },
    { "text": "Manager reports", "entities": ["Visit", "ADM"] },
    { "text": "Daily SAP export", "entities": ["Visit"] }
  ]
}
```

**Expected Output:**

```yaml
# Auto-Generated Schema
tables:
  - name: vt_physician
    fields: [name, specialty, email, phone]
  - name: vt_adm # ADM = Außendienstmitarbeiter
    fields: [user_id, region, phone]
  - name: vt_visit
    fields: [visit_date, duration, notes, status, physician_id, adm_id]

warnings:
  - "No Denormalization detected ✓"
  - "RLS Strategy: Business Unit recommended (region-based)"
  - "Performance: Consider index on (physician_id, visit_date)"

deployment:
  script: "deploy-schema.sh"
  estimated_time: "5 minutes"
```

**Aufgabe:** Schreib den erwarteten Agent-Output.

**Deliverable:** JSON / YAML Output.

---

## Checkpoint ✓

- [ ] Dataverse Schema DSL definiert (YAML)
- [ ] Validation Rules (10+) dokumentiert
- [ ] Denormalization Decision Tree erstellt
- [ ] RLS Strategy Mapper geschrieben
- [ ] Deployment Script generiert
- [ ] Agent Prompt geschrieben
- [ ] Test Output simuliert
- [ ] Erkannt: **Schema ist richtig, aber Performance-Tuning brauchst du noch!**
