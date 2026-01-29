# Week 2 Hands-On — Applied RAG Product Results (CS 5588)

## Product Overview
- **Product name:** TrustDoc AI  
- **Target users:** Students, analysts, and early-career professionals using AI for document-based research  
- **Core problem:** General chatbots generate fluent answers but may hallucinate or overstate confidence when answering questions that require grounding in specific documents  
- **Why RAG:** Retrieval-Augmented Generation ensures answers are grounded in real documents, includes citations, and can abstain when sufficient evidence is unavailable  

---

## Dataset Reality
- **Source / owner:** Public academic sources and instructor-provided course materials  
- **Sensitivity:** Public to low-sensitivity (no PII or regulated data)  
- **Document types:** Course notes, technical articles, AI trust guidelines  
- **Expected scale in production:**  
  - Pilot: 500–2,000 documents  
  - Scaled: 10,000+ documents with periodic updates  

---

## User Stories + Rubric
- **U1 (Normal):**  
  Student asks questions about course documents and expects cited, accurate answers  

- **U2 (High-Stakes):**  
  Researcher requires evidence-backed answers to avoid hallucinated conclusions  

- **U3 (Ambiguous / Failure):**  
  User asks an unsupported question and expects the system to clearly say “Not enough evidence”  

**Rubric:**  
- Acceptable evidence: retrieved document chunks with citations  
- Correct answers must be grounded in evidence or explicitly abstain  

---

## System Architecture
- **Chunking:** Semantic paragraph-based chunking (≈1000 characters)  
- **Keyword retrieval:** BM25 for exact term and compliance-style queries  
- **Vector retrieval:** Sentence embeddings + FAISS (cosine similarity)  
- **Hybrid α:** α = 0.5 balancing keyword precision and semantic recall  
- **Reranking governance:** Lightweight reranking to filter weak or irrelevant chunks  
- **LLM / generation option:** FLAN-T5 with fallback evidence-only mode  

---

## Results

| User Story | Method | Precision@5 | Recall@10 | Trust (1–5) | Confidence (1–5) |
|---|---|---:|---:|---:|---:|
| U1 Normal | Hybrid RAG | 0.40 | 0.67 | 4 | 4 |
| U2 High-Stakes | Hybrid + Rerank | 0.60 | 0.75 | 4 | 4 |
| U3 Ambiguous | Abstention | N/A | N/A | 5 | 2 |

---

## Failure + Fix
- **Failure:** Ambiguous queries retrieved semantically related but insufficient evidence  
- **Layer:** Retrieval + Generation  
- **Consequence:** Risk of confident but unsupported answers  
- **Safeguard / next fix:**  
  Evidence sufficiency gating, α tuning by query type, and human-in-the-loop review for abstained high-risk queries  

---

## Evidence of Grounding

**Query:** Why does RAG improve trust in AI systems?

**Answer:**  
RAG improves trust by grounding model outputs in retrieved documents rather than relying solely on parametric memory. This reduces hallucination risk and allows users to verify claims through citations [Chunk 1], [Chunk 2].

