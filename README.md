# AI-Powered Intelligent Recruiter Assistant

An explainable AI system for semantic resume matching and recruiter-facing candidate ranking, designed to reduce manual screening time and improve candidate discovery beyond traditional ATS systems.

---

## 🚀 Business Problem

- Keyword-based ATS systems miss semantically relevant candidates  
- No transparent ranking or explanation of candidate relevance  
- Recruiters spend significant time manually scanning resumes  
- Skill gaps and contextual relevance must be inferred manually  
- Limited recruiter self-service analytics  

---

## 💡 Solution Overview

This system converts unstructured resume data into structured metadata using NLP techniques, handling diverse unstructured documents with varying formats and layouts, enabling efficient search, ranking, and explainability.

It combines semantic retrieval (Sentence-BERT + FAISS) with cross-encoder re-ranking and structured feature scoring to improve candidate matching.

The pipeline supports natural language queries from recruiters and returns the most relevant candidates along with transparent hiring signals (e.g., skill match, experience alignment, seniority fit, and domain relevance).

---

## ⭐ Key Features

- Semantic search using Sentence-BERT (beyond keyword matching)  
- Hybrid ranking: FAISS + feature scoring + cross-encoder re-ranking  
- Section-level chunking for precise matching  
- Explainable scoring (skills, experience, domain, seniority, education)  
- Evidence-based candidate summaries  
- Recruiter analytics using structured metadata  
- End-to-end pipeline supporting multi-stage reasoning and explainable ranking  

---

## 💼 Business Impact

- ⏱️ Reduces resume screening time from hours to seconds  
- 🎯 Improves candidate quality via semantic matching  
- 📈 Scales across thousands of resumes  
- 🤝 Supports recruiter decision-making (not replaces it)  

---

## 🧠 How It Works (High-Level)

1. Recruiter enters a natural language query, which is converted into embeddings  
2. Query retrieves relevant resume sections from governed Delta tables  
3. FAISS retrieves semantically similar resume sections  
4. Scores are aggregated at the resume level using top-K max pooling  
5. Feature-based scoring evaluates structured hiring signals (skill match, domain match, experience alignment, seniority fit)  
6. Cross-encoder re-ranks top candidates  
7. System generates controlled, explainable summaries using grounded prompts  

The system follows a multi-stage reasoning pipeline, where retrieval, feature computation, re-ranking, and summarization progressively refine candidate relevance.

Structured features and cross-encoder outputs guide context-aware reasoning, allowing the system to evaluate candidate relevance beyond surface-level similarity.

---

## 🔄 What Happens During Search

- Query embedding generation  
- FAISS semantic retrieval  
- Resume-level aggregation  
- Feature computation using metadata from Delta tables  
- Cross-encoder re-ranking  
- Controlled LLM summarization  

---

## Project Structure

- `app.py` - Streamlit application for recruiter query input, retrieval, ranking, and summary generation  
- `preprocessing.py` - Resume cleaning, parsing, section detection, and metadata extraction  
- `build_index.py` - Offline pipeline to generate embeddings and build the FAISS index  
- `config.py` - Model configuration, file paths, and scoring parameters  
- `resume_index/` - Stored FAISS index and metadata artifacts

---

## 📁 Sample Data

This repository includes a small set of anonymized sample resumes for demonstration purposes.

- Located in `data/resumes/`  
- No personally identifiable information (PII) is included  
- Covers diverse roles (e.g., Data Analyst, Data Scientist, Business Analyst)  
- Used for testing retrieval, ranking, and explainability  

These samples help demonstrate how the system performs across different candidate profiles.

---

## 🔍 Detailed Architecture

![Architecture](https://github.com/user-attachments/assets/63591997-881c-4c6c-9eb7-f5fdbe51bb12)

---

### Phase 1: Offline Resume Processing & Indexing

- Ingest resumes using `pdfplumber` and convert them into structured JSON  
- Perform resume cleaning (whitespace, formatting, ASCII/Unicode fixes)  
- Section detection (Experience, Skills, Education)  
- Entity extraction using spaCy and NLTK  

#### Normalization & Ontology Mapping

- Abbreviation expansion and synonym handling  
- Ontology-based normalization for consistent skill representation  

#### Feature Engineering & Storage

- Extract features: experience, domain, education, location  
- Store in Delta Live Tables with lineage tracking  

![Data Model](https://github.com/user-attachments/assets/3b55a914-1a61-4da1-979f-02717034f769)  
![ER Diagram](https://github.com/user-attachments/assets/afb9e1cb-420e-4985-abb3-7066eb80d9a0)

---

### Phase 2: Embeddings Generation (Retrieval Layer)

- Sentence-BERT for semantic embeddings  
- Section-level embeddings for higher precision  
- Stored in FAISS vector database  

---

### Phase 3: Online Retrieval & Ranking

#### 🔍 Retrieval

- Query embedding generation  
- FAISS retrieval (Top-N candidates)  
- Chunk-level similarity scoring and resume-level aggregation  

#### 🧠 Reasoning

- Feature-based scoring:
  - Skill overlap  
  - Experience alignment  
  - Domain match  
  - Seniority fit  
  - Gap penalties  

- Cross-encoder re-ranking for deep contextual understanding  
- Role-specific weighted scoring  

---

### ⚖️ Comparison of Scoring Approaches

- Semantic similarity (RAG-only)  
- Cross-encoder + RAG  
- Feature-based signals + Cross-encoder + RAG  

**Custom weight templates:**

- **Business Analyst** → Experience & impact prioritized  
- **Data Scientist** → Skills & domain expertise prioritized  

---

## 🧪 Prototype

### Recruiter Query Input

![Query UI](https://github.com/user-attachments/assets/0eb1f3aa-7a62-44fe-8a75-a94c5ed2e13e)

---

#### Generated Summary

Candidate demonstrates strong alignment with the role, with hands-on experience in SQL, Python, and Tableau. Their background in analytics and dashboard development makes them a strong fit.

### Retrieval + Reasoning Output

FAISS retrieves relevant sections and generates explainable insights using grounded prompts.

![Output](https://github.com/user-attachments/assets/8435cd3b-e120-4b05-956b-58ebdfc02cf4)

---

### Explainable Ranking Breakdown

Shows why a candidate is ranked higher using structured features.

![Ranking](https://github.com/user-attachments/assets/5e1ddd26-2cd3-4e4c-9f0a-c9876d2e9fe4)

---

## 🔎 Explainability & Transparency

- Displays contribution of each feature  
- Shows why one candidate ranks higher than another  
- Highlights strengths and gaps with evidence  
- Reduces black-box decision making  

---

## 📊 Evaluation Metrics:

The system was evaluated across latency, ranking quality, and reliability.

- Latency across pipeline stages
- Relevance Score
- Precision@K / Recall@K  
- Consistency and hallucination checks

<img width="507" height="160" alt="Screenshot 2026-04-03 at 19 55 18" src="https://github.com/user-attachments/assets/35539c2a-8516-4cec-851b-cd1e26d180f1" />

### Notes

- **Relevance Score** represents the semantic similarity between query and retrieved candidates after ranking  
- Low error rate indicates stable pipeline execution  
- Latency includes retrieval, re-ranking, and summary generation

- -- 

### Comparative Evaluation

- RAG-only  
- Cross-encoder + RAG  
- Hybrid (features + cross-encoder + RAG)  

---

## 🛠️ Tech Stack

- Python 3.10  
- FAISS  
- Sentence-Transformers  
- Cross-Encoder (MiniLM)  
- Streamlit  
- Ollama (Llama 3)  
- Databricks (Delta Lake)  
- Semantic Data Models  
- Databricks Genie  

---

## ⚠️ Current Status

This project is a prototype and is still under development.  

Core components such as preprocessing, semantic retrieval, feature-based scoring, and ranking are implemented. However, the full pipeline is not yet fully packaged for reproducible end-to-end execution.

This repository is intended to demonstrate:
- system design and architecture  
- hybrid retrieval and ranking approach  
- explainable candidate scoring  
- sample outputs and evaluation approach  


### 🔮 Future Work
 Benchmark RAG vs Cross-Encoder vs Hybrid approaches  
- MLflow-based evaluation and experiment tracking  
- Role-specific prompt templates  
- Responsible AI safeguards (bias checks, prompt injection handling)  

📜 License

This project is intended for educational and internal enterprise use.
