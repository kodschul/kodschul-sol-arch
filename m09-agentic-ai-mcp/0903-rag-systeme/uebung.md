# Übung: RAG System aufbauen

## Szenario

VisitTrack bekommt eine interne Wissensdatenbank. Außendienstmitarbeiter sollen Fragen zur Produktdokumentation, Compliance-Richtlinien und internen Prozessen stellen können. Du baust das RAG-System dahinter.

---

## Aufgabe 1: Chunking-Strategie entscheiden (15 Minuten)

Du hast folgende Dokumente zu indexieren:

| Dokument                 | Seiten | Format | Charakteristik                             |
| ------------------------ | ------ | ------ | ------------------------------------------ |
| Produktkatalog MedPharma | 120    | PDF    | Tabellarisch, kurze Abschnitte pro Produkt |
| Compliance-Handbuch      | 45     | Word   | Fließtext, Kapitel mit Unterkapiteln       |
| ADM-Onboarding Guide     | 20     | PDF    | Mischung: Text + Checklisten               |
| FAQ-Sammlung             | 8      | Word   | 50 Q&A-Paare                               |

Entscheide für jedes Dokument:

1. Welche Chunking-Strategie?
2. Welche Chunk-Größe (Token)?
3. Overlap ja/nein, wie viel?

Begründe jede Entscheidung in einem Satz.

---

## Aufgabe 2: RAG Pipeline skizzieren (20 Minuten)

Skizziere die vollständige RAG-Pipeline für VisitTrack als Mermaid-Diagramm oder strukturierte Beschreibung.

Die Pipeline muss folgende Schritte abdecken:

- Wo kommen die Dokumente her? (SharePoint)
- Wie werden sie verarbeitet? (Extraktion, Chunking)
- Welches Embedding-Modell?
- Welcher Vector Store?
- Wie läuft eine User-Abfrage ab?
- Wie wird das Ergebnis an Copilot Studio übergeben?

---

## Aufgabe 3: System Prompt gegen Halluzinationen (15 Minuten)

Schreibe einen System Prompt für den VisitTrack Knowledge Agent.

Anforderungen:

- Agent antwortet nur basierend auf Knowledge Base
- Bei fehlender Information: klares "weiß ich nicht" statt Raten
- Jede Aussage mit Quelle belegen
- Sprache: Deutsch, professionell, aber nicht formell
- Wenn Frage nicht zum Thema (Produktdoku, Compliance, Prozesse) → freundlich ablehnen

---

## Aufgabe 4: Retrieval-Qualität verbessern (15 Minuten)

Ein Nutzer fragt: `"Was sind die Nebenwirkungen von Produkt X?"`

Der naive RAG-Ansatz findet nur Chunks die "Nebenwirkungen" enthalten, aber nicht den Kontext zu Produkt X.

Beschreibe eine **Hybrid-Search-Strategie** die das Problem löst:

- Was ist Keyword-Search und wo hilft sie hier?
- Was ist Vector-Search und wo hilft sie hier?
- Wie kombinierst du beide (Re-Ranking)?

---

## Aufgabe 5: Evaluierung planen (10 Minuten)

Erstelle einen Evaluierungsplan für das RAG-System:

- 10 Test-Fragen die abgedeckt werden sollen
- 5 Test-Fragen die **nicht** beantwortet werden dürfen (Out-of-Scope)
- Wie misst du Precision und Groundedness (ohne großes Budget)?

---

## Abgabe

- Chunking-Entscheidungen Tabelle (Aufgabe 1)
- RAG-Pipeline Diagramm (Aufgabe 2)
- System Prompt Dokument (Aufgabe 3)
- Hybrid-Search Beschreibung (Aufgabe 4)
- Evaluierungsplan (Aufgabe 5)
- Power Platform Entscheidung (Aufgabe 6)

---

## Aufgabe 6: RAG-Option für Power Platform entscheiden (15 Minuten)

VisitTrack bekommt eine Knowledge-Funktion. Die Anforderungen:

- **Dokumente:** 3 Word-Dokumente in SharePoint (Produktkatalog, Compliance, Onboarding)
- **Nutzer:** Außendienstmitarbeiter, ca. 200 Personen
- **Fragen:** "Was sind die Nebenwirkungen von Produkt X?" / "Wie läuft ein Erstbesuch ab?"
- **Budget:** Kein separates Azure-Budget genehmigt
- **Timeline:** Live in 2 Wochen

Entscheide:

1. **Copilot Studio Knowledge Source** oder **Azure AI Search** — welche Option passt?
2. Schreibe den System Prompt für den Knowledge Agent (max. 10 Zeilen)
3. Definiere 3 Test-Fragen die beantwortet werden müssen
4. Definiere 2 Test-Fragen die der Agent ablehnen muss (Out-of-Scope)

Begründe deine Option-Entscheidung in 2 Sätzen.
