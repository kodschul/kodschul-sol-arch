# Hands-On M04 — Agentic: Agent Builder Agent

> **Typ:** Baue einen Agent, der Agents baut 🤯  
> **Dauer:** ca. 60 Minuten  
> **Lösung:** siehe `hands-on-agentic-sol.md`  
> **Werkzeuge:** Claude MCP, Copilot Studio API, Power Automate MCP

---

## Szenario: Vom Schema zum funktionalen Agent in 10 Minuten

**Ausgangssituation:**
Du hast:

- Discovery (M01) ✓
- Architektur (M02) ✓
- Dataverse Schema (M03) ✓

Jetzt brauchst du einen **funktionalen Copilot Studio Agent**.

Klassisch: Developer klickt im UI, erstellt Topics, schreibt Antworten. 2+ Stunden.

Agentic: Agent generiert Topics + Actions + Prompting. 5 Minuten.

---

## Aufgabe 1: Topic Generation Language

**Dauer:** 15 Minuten

Definiere ein Language für Topic-Definition:

```yaml
# Agent Topic Definition Language (ATDL)

metadata:
  agent_name: "VisitTrack Assistant"
  language: "de-DE"
  model: "gpt-4o"
  knowledge_source: "SharePoint: HR-Docs"

topics:
  - id: "topic_001"
    name: "Record Visit"
    description: "User erfasst einen Besuch bei einem Arzt"

    intent_detection:
      phrases:
        - "Ich will einen Besuch aufzeichnen"
        - "Arzt-Besuch eintragen"
        - "Neuer Besuch"
      min_confidence: 0.8

    slots: # = Entities to collect
      - name: visit_date
        type: "Date"
        required: true
        prompt: "Wann war der Besuch?"
        validation: "date >= TODAY - 365"

      - name: physician_name
        type: "String"
        required: true
        prompt: "Bei welchem Arzt?"

      - name: visit_duration
        type: "Integer"
        required: false
        prompt: "Wie lange hat der Besuch gedauert? (Minuten)"
        validation: "0 <= duration <= 480"

    actions:
      - type: "flow_call"
        flow_name: "Create_Visit_Record"
        inputs:
          visit_date: "{{visit_date}}"
          physician_name: "{{physician_name}}"
          duration: "{{visit_duration}}"
          adm_id: "{{user.id}}"
        error_handling: "If flow fails, ask user to retry"

    response:
      success: "✓ Besuch auf {{visit_date}} bei {{physician_name}} erfasst!"
      error: "Oops, etwas ist schiefgelaufen. Versuch es später."

  - id: "topic_002"
    name: "Check My Visits"
    description: "Nutzer sieht seine heutigen Besuche"

    intent_detection:
      phrases:
        - "Was steht heute an?"
        - "Meine heutigen Besuche"
        - "Welche Termine habe ich?"

    actions:
      - type: "query"
        source: "Dataverse"
        query: "SELECT visit FROM vt_visit WHERE adm_id = {{user.id}} AND visit_date = TODAY"
        result_format: "List of {date, physician_name, duration}"

    response:
      template: "Du hast {{visit_count}} Besuche heute:\n{{visits}}"

  - id: "topic_003"
    name: "Get HR Information"
    description: "Beantworte HR-Fragen basierend auf Knowledge Source"

    intent_detection:
      phrases:
        - "Wie funktioniert Urlaub?"
        - "Krankmeldung"
        - "Benefits"

    knowledge_source:
      type: "SharePoint"
      library: "HR-Dokumentation"
      chunking: "semantic"
      similarity_threshold: 0.85

    response_type: "RAG-generated" # LLM + Knowledge Source
```

**Aufgabe:** Definiere ein Topic Language mit min. 3 Topics für VisitTrack.

**Deliverable:** YAML Topic Definition File (`visittrack-topics.yaml`).

---

## Aufgabe 2: RAG Configuration Wizard

**Dauer:** 15 Minuten

Definiere wie ein Agent RAG optimal konfiguriert:

```
RAG Configuration Decision Tree:

Input: Knowledge Source
  - Source: SharePoint Library "HR-Docs"
  - Size: 50 PDFs, ~500 MB
  - Format: PDFs, Word Docs
  - Update Frequency: Weekly

Agent Decision Points:

1. Indexing Strategy
   Q: Semantic oder Simple chunking?
   A:
     - Semantic (besser): Chunk nach Meaning
     - Simple (schneller): Chunk nach Paragraph
   Decision: Semantic (weil HR-Docs komplex)

2. Chunk Size
   Q: 256, 512, oder 1024 tokens?
   A:
     - Smaller (256): Better relevance, more chunks
     - Larger (1024): Faster retrieval, less accurate
   Decision: 512 tokens (best balance)

3. Similarity Threshold
   Q: 0.7, 0.8, oder 0.9?
   A:
     - Stricter (0.9): Fewer hallucinations, many "I don't know"
     - Looser (0.7): More hallucinations, better coverage
   Decision: 0.85 (balance)

4. Fallback Strategy
   Q: What if no matching docs?
   A:
     - Option A: "I don't have info, contact HR"
     - Option B: LLM tries to answer anyway (risky!)
   Decision: Option A (safer for HR sensitive topics)

5. Prompt Optimization
   Q: Generic oder custom Prompt?
   A:
     - Generic: "Answer based on knowledge source"
     - Custom: "You are HR expert, be helpful, warn if unsure"
   Decision: Custom (optimized for HR domain)

Output Configuration:
{
  "indexing_strategy": "semantic",
  "chunk_size": 512,
  "similarity_threshold": 0.85,
  "fallback": "Tell user: 'Ich habe diese Info nicht. Kontakt: hr@company.com'",
  "prompt": "Du bist HR-Assistant. Beantworte Fragen basierend auf Dokumenten..."
}
```

**Aufgabe:** Schreib einen RAG Configuration Wizard / Decision Tree.

**Deliverable:** YAML oder Mermaid Flowchart.

---

## Aufgabe 3: Topic → Flow Orchestration

**Dauer:** 15 Minuten

Definiere wie Topics Power Automate Flows aufrufen:

```yaml
# Topic: "Record Visit" → Flow: "Create_Visit_Record"

Topic Definition:
  slots: [visit_date, physician_name, duration]
  action:
    type: flow_call
    flow_name: "Create_Visit_Record"

Auto-Generated Flow (Power Automate):
  Trigger: HTTP Request (from Copilot Studio)
  Input:
    - visit_date
    - physician_name
    - duration
    - user_id (from context)

  Actions:
    1. Lookup Physician ID (Dataverse query)
       SELECT physician_id WHERE name = "{{physician_name}}"

    2. Create Visit Record (Dataverse create)
       Table: vt_visit
       Fields:
         - visit_date
         - physician_id (from lookup)
         - duration
         - status: "Completed"
         - owner: user_id

    3. Send Notification (optional)
       Send email to manager

    4. Return Response (HTTP)
       Status: "Success"
       visit_id: "{{created_record_id}}"

Error Handling:
  - If physician not found: Flow fails, Agent shows "Physician not found"
  - If Dataverse creates fails: Retry 2x, then error
  - Timeout: 30 seconds

From Agent Perspective:
  Agent receives:
    - Success: visit_id + confirmation
    - Error: error message

  Agent responds to user:
    - Success: "✓ Besuch gespeichert!"
    - Error: "Fehler: {{error_message}}"
```

**Aufgabe:** Definiere Flow-Integration für min. 2 Topics.

**Deliverable:** YAML Flow Orchestration File.

---

## Aufgabe 4: Agent Builder Prompt

**Dauer:** 15 Minuten

Schreib einen umfassenden Prompt für einen Agent Builder Agent:

```
Du bist ein Copilot Studio Agent Builder Experte.

Deine Aufgabe:
1. Lese die VisitTrack Requirements + Architecture
2. Generiere einen Copilot Studio Agent mit:
   - Topics für häufige User Intents
   - Slot-basierte Konversation (Daten sammeln)
   - Power Automate Flow Integrations
   - RAG für Knowledge Sources
   - Error Handling & Fallbacks
3. Output im ATDL (Agent Topic Definition Language) Format
4. Validiere auf Best Practices:
   - Keine zu langen Conversations
   - Clear error messages
   - Graceful fallback wenn Agent unsicher ist

Inputs:
  - requirements.json (aus M01)
  - architecture.yaml (aus M02)
  - schema.yaml (aus M03)
  - topic_templates.yaml (Best Practices)

Outputs:
  - visittrack-topics.yaml (Topics in ATDL)
  - flow-definitions.yaml (Power Automate Flows)
  - rag-config.yaml (Knowledge Source Setup)
  - agent-manifest.json (Deploy-ready Manifest)

Constraints:
  - Agents sollten simple sein (max 5-7 Topics)
  - Nutzer sollten nie >3 Klicks brauchen
  - Halluzinationen minimieren (RAG nur für verified sources)
  - Multilingual support (de-DE + en-US)
```

**Aufgabe:** Schreib einen vollständigen Agent Builder Prompt.

**Deliverable:** Prompt File.

---

## Aufgabe 5: Deployment Manifest

**Dauer:** 10 Minuten

Generiere ein Deployment Manifest:

```json
{
  "agent": {
    "name": "VisitTrack Assistant",
    "version": "1.0.0",
    "created_by": "Agentic Agent Builder",
    "created_at": "2024-06-16",
    "deployment_target": "Production"
  },

  "topics": [
    {
      "id": "topic_001",
      "name": "Record Visit",
      "enabled": true,
      "trigger_phrases": ["Ich will Besuch...", "Arzt-Besuch eintragen"]
    },
    {
      "id": "topic_002",
      "name": "Check My Visits",
      "enabled": true
    },
    {
      "id": "topic_003",
      "name": "Get HR Information",
      "enabled": false, // Disabled until RAG is validated
      "reason": "Needs HR team approval before going live"
    }
  ],

  "integrations": [
    {
      "type": "Power Automate",
      "flow": "Create_Visit_Record",
      "topic": "topic_001",
      "status": "ready"
    },
    {
      "type": "Dataverse",
      "query": "vt_visit",
      "topic": "topic_002",
      "status": "ready"
    }
  ],

  "knowledge_sources": [
    {
      "type": "SharePoint",
      "location": "HR-Dokumentation",
      "topic": "topic_003",
      "status": "pending_validation"
    }
  ],

  "readiness_checklist": {
    "topics_created": true,
    "flows_tested": true,
    "dataverse_connected": true,
    "knowledge_source_indexed": false,
    "error_handling_configured": true,
    "user_testing_completed": false,
    "deployment_ready": false
  }
}
```

**Aufgabe:** Erstelle einen Deployment Manifest.

**Deliverable:** JSON Manifest File.

---

## Aufgabe 6: Testing Strategy

**Dauer:** 10 Minuten

Definiere wie der Agent getestet wird:

```yaml
# Agent Testing Strategy

unit_tests:
  - test_intent_detection
    input: "Ich will Besuch aufzeichnen"
    expected_topic: "topic_001"
    tolerance: ">90% confidence"

  - test_slot_collection
    steps:
      1. User: "Arzt-Besuch eintragen"
      2. Agent: "Wann war der Besuch?" (expects: date)
      3. User: "Gestern"
      4. Agent: "Bei welchem Arzt?" (expects: string)
      5. User: "Dr. Mueller"
      6. Agent: "Dauer?" (expects: number)
      7. User: "30 Minuten"
    expected_result: "Alle Slots gefüllt ✓"

  - test_flow_integration
    input: slots from unit_test_slot_collection
    expected_flow_call: "Create_Visit_Record with visit_date='yesterday', physician='Mueller', duration=30"
    expected_response: "✓ Besuch gespeichert!"

integration_tests:
  - test_end_to_end_conversation
    scenario: "ADM wants to record a visit"
    steps:
      1. User opens agent in Teams
      2. User: "Neuer Besuch"
      3. Agent collects info (date, physician, duration)
      4. Agent calls flow, creates record
      5. Agent confirms success
    expected_duration: "<2 minutes"
    expected_user_satisfaction: ">4/5"

rag_validation:
  - test_hallucination_rate
    queries: 50 common HR questions
    baseline_hallucination: <10%
    target_hallucination: <2%

  - test_knowledge_coverage
    questions: HR FAQ list
    target_accuracy: >90%

user_acceptance_testing:
  - participants: 5 ADMs
  - duration: 1 hour each
  - success_criteria:
    - Task completion: >80%
    - Error recovery: Graceful
    - User satisfaction: >4/5
```

**Aufgabe:** Definiere eine Testing Strategy.

**Deliverable:** YAML Testing Plan.

---

## Aufgabe 7: Simulation & Deployment

**Dauer:** 5 Minuten

**Input (aus M01-M03):**

- Requirements: Mobile Capture, Offline, Manager Dashboard, SAP Export
- Architecture: Canvas + Dataverse + Power Automate
- Schema: vt_visit, vt_physician, vt_adm

**Expected Output (Auto-Generated Topics):**

```yaml
topics:
  - "Record Visit" → Slot collection + Flow call
  - "Check My Visits" → Dataverse query
  - "Get HR Info" → RAG fallback
  - "Offline Sync Status" → Check sync queue
```

**Aufgabe:**
Simuliere den Agent Builder Agent:

1. Lese deine ATDL Topic Definition
2. Generiere die erwarteten Topics
3. Erstelle den Deployment Manifest

**Deliverable:** Simulation Output (YAML/JSON).

---

## Aufgabe 8: Reflection

**Dauer:** 5 Minuten

Beantworte diese Fragen:

1. **Speed vs. Quality:** Der Agent-Generated Agent ist schneller, aber brauchst du immer noch Human Review?
2. **Hallucination Risk:** Wo könnten Agent-Fehler problematisch sein?
3. **Scalability:** Kannst du jetzt für jeden neuen Use Case einen neuen Agent bauen?
4. **CI/CD:** Wie würde ein Agent-Builder in eine DevOps Pipeline passen?

**Deliverable:** Kurze Reflexion (1-2 Seiten).

---

## Checkpoint ✓

- [ ] Topic Definition Language (ATDL) definiert
- [ ] RAG Configuration Wizard erstellt
- [ ] Flow Orchestration definiert
- [ ] Agent Builder Prompt geschrieben
- [ ] Deployment Manifest erstellt
- [ ] Testing Strategy definiert
- [ ] Simulation durchgeführt
- [ ] Reflexion geschrieben
- [ ] **Erkannt: Agentic Agent Building funktioniert! Aber Human Validation ist still critical.**
