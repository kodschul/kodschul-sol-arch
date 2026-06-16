# Lösung: RAG System aufbauen

## Aufgabe 1: Chunking-Entscheidungen

| Dokument            | Strategie     | Chunk-Größe                  | Overlap  | Begründung                                                                   |
| ------------------- | ------------- | ---------------------------- | -------- | ---------------------------------------------------------------------------- |
| Produktkatalog      | Fixed Size    | 256 Token                    | 20 Token | Kurze Produktabschnitte sind atomar — zu groß würde mehrere Produkte mischen |
| Compliance-Handbuch | Hierarchical  | 512 (Detail) + 128 (Summary) | 50 Token | Langer Fließtext braucht Kontext (Kapitel-Zusammenfassung) + Details         |
| Onboarding Guide    | Paragraph     | 384 Token                    | 30 Token | Gemischter Inhalt — Absätze sind natürliche Grenzen                          |
| FAQ-Sammlung        | Fixed per Q&A | 1 Q&A = 1 Chunk              | 0        | Jede Frage+Antwort ist atomar und abgeschlossen                              |

---

## Aufgabe 3: System Prompt Referenz

```
Du bist der VisitTrack Wissens-Assistent für Außendienstmitarbeiter der MedPharma GmbH.

WICHTIG: Antworte ausschließlich basierend auf den Informationen in deiner Knowledge Base
(Produktdokumentation, Compliance-Handbuch, interne Prozesse).

Wenn eine Information nicht in deiner Knowledge Base steht:
  Sag: "Diese Information habe ich in meiner Wissensdatenbank nicht. Wende dich bitte an dein Regionalbüro."

Zitiere immer die Quelle: (Quelle: [Dokumentname], Abschnitt [X])

Wenn die Frage nichts mit MedPharma-Produkten, Compliance oder internen Prozessen zu tun hat:
  Sag: "Das liegt außerhalb meines Bereichs. Ich helfe dir bei Fragen zu Produkten, Compliance und internen Prozessen."

Sprache: Deutsch. Freundlich, klar, direkt — kein Fachjargon ohne Erklärung.
```

---

## Aufgabe 4: Hybrid Search

**Keyword-Search** (BM25) findet exakte Treffer: `"Produkt X"` → findet alle Chunks mit exakt "Produkt X". Hilft wenn der Produktname spezifisch und eindeutig ist.

**Vector-Search** (Cosine Similarity) findet semantisch ähnliche Chunks: `"Nebenwirkungen"` → findet auch Chunks die von "Risiken", "Kontraindikationen" oder "unerwünschte Wirkungen" sprechen. Hilft bei synonymen Begriffen.

**Kombination (Re-Ranking mit RRF):**

```
Reciprocal Rank Fusion (RRF):
  score_rrf = 1/(k + rank_keyword) + 1/(k + rank_vector)

  Beispiel:
    Chunk A: keyword_rank=1, vector_rank=3 → rrf = 1/61 + 1/63 = 0.032
    Chunk B: keyword_rank=5, vector_rank=1 → rrf = 1/65 + 1/61 = 0.032
    Chunk C: keyword_rank=2, vector_rank=2 → rrf = 1/62 + 1/62 = 0.032 → Tie → nimm beide

→ Chunks die in beiden Rankings hoch sind, gewinnen
```

Azure AI Search unterstützt Hybrid Search + RRF nativ — kein Custom Code nötig.

---

## Aufgabe 5: Evaluierungsplan

**In-Scope Test-Fragen (10):**

1. Was ist die empfohlene Dosierung von Produkt X?
2. Welche Dokumente brauche ich für einen Arztbesuch?
3. Was passiert wenn ich eine Compliance-Richtlinie verstoße?
4. Wie erfasse ich einen Besuch in VisitTrack?
5. Was sind die Produktvorteile von Produkt Y gegenüber Produkt Z?
6. Wer ist mein Ansprechpartner bei technischen Problemen?
7. Wie funktioniert die Urlaubsbeantragung?
8. Was sind die Datenschutzregeln bei Arztbesuchen?
9. Welche Produkte darf ich in der Kardiologie empfehlen?
10. Wie erstelle ich einen Wochenbericht?

**Out-of-Scope Test-Fragen (5) — müssen abgelehnt werden:**

1. Was ist das Wetter morgen in München?
2. Schreib mir eine E-Mail an meinen Manager.
3. Was ist der Aktienkurs von MedPharma?
4. Wie programmiere ich einen Python-Script?
5. Empfiehl mir ein Restaurant in der Nähe.

**Evaluation ohne Budget:**

- Manuelles Scoring: 2 Personen bewerten Antworten unabhängig (1-5)
- Groundedness: Prüfe ob jede Aussage im Chunk-Kontext belegbar ist
- Halluzinations-Check: Vergleiche Antwort mit tatsächlichem Dokumentinhalt
- Precision@3: Bei 10 Fragen — wie viele der 3 zurückgegebenen Chunks sind wirklich relevant?
