# AI-Powered Intelligent Recruiter Assistant

An explainable AI system for semantic resume matching and recruiter-facing candidate ranking, designed to reduce manual screening time and improve candidate discovery beyond traditional ATS systems.

---

## 🚀 Business Problem

* Keyword-based ATS systems miss semantically relevant candidates
* No transparent ranking or explanation of candidate relevance
* Recruiters spend significant time manually scanning resumes
* Skill gaps and contextual relevance must be inferred manually
* Limited recruiter self-service analytics

---

## 💡 Solution Overview

This system enables intelligent resume retrieval and ranking using a hybrid approach that combines semantic search, structured feature scoring, and deep re-ranking.

It allows recruiters to query large resume datasets using natural language and surfaces the most relevant candidates along with explainable insights on why they are ranked higher.

---

## ⭐ Key Features

* Semantic search using Sentence-BERT (beyond keyword matching)
* Hybrid ranking: FAISS + feature scoring + cross-encoder re-ranking
* Section-level chunking for precise matching
* Explainable scoring (skills, experience, domain, seniority, education)
* Evidence-based candidate summaries
* Recruiter analytics using structured metadata

---

## 💼 Business Impact

* ⏱️ Reduces resume screening time from hours to seconds
* 🎯 Improves candidate quality via semantic matching
* ⚖️ Enables transparent and auditable decision-making
* 📈 Scales across thousands of resumes
* 🤝 Supports recruiter decision-making (not replaces it)

---

## 🧠 How It Works (High-Level)

1. Recruiter enters a natural language query
2. Query is converted into embeddings
3. FAISS retrieves semantically similar resume sections
4. Scores are aggregated at resume level
5. Feature-based scoring evaluates structured signals
6. Cross-encoder re-ranks top candidates
7. System generates controlled explainable summaries

---

## 🔍 Detailed Architecture

### Data Platform & Semantic Layer

* Built on Databricks with Delta Lake for scalable data storage
* Designed semantic data models for candidate features (skills, experience, domain, seniority, education)
* Enables deterministic filtering and structured scoring
* Supports recruiter-facing analytics and explainability
* Integrated Databricks Genie for natural language querying over candidate metadata

### Phase 1: Offline Data Processing & Indexing

* Resume ingestion and cleaning (unicode removal, formatting)
* Section detection (Experience, Skills, Education)
* Named Entity Recognition (NER)
* Synonym handling and ontology mapping
* Feature extraction (experience, domain, education, location)
* Storage in structured feature store

### Phase 2: Embeddings Generation

* Sentence-BERT used for semantic embeddings
* Section-level embeddings for higher precision
* Stored in FAISS vector database

### Phase 3: Online Retrieval & Ranking

* Query embedding generation
* FAISS retrieval (Top-N candidates)
* Chunk-level similarity scoring
* Resume-level aggregation
* Feature-based scoring:

  * Skill overlap
  * Experience alignment
  * Domain match
  * Seniority fit
  * Gap penalties
* Cross-encoder re-ranking (Top-K)

### Final Scoring

Hybrid scoring combining:

* Semantic similarity
* Feature-based signals
* Cross-encoder outputs

Custom weight templates adapt scoring based on role:

* **Business Analyst**: Experience & impact prioritized
* **Data Scientist**: Skills & domain expertise prioritized

---

## 🔎 Explainability & Transparency

* Displays contribution of each feature (skills, experience, domain, etc.)
* Shows why one candidate ranks higher than another
* Highlights strengths and gaps with supporting evidence
* Reduces black-box decision making

---

## 📊 Evaluation Metrics

* Precision@K
* Reduction in false positives
* Ranking quality (NDCG)
* Latency

---

## 🛠️ Tech Stack

* Python 3.10
* FAISS – vector similarity search
* Sentence-Transformers – embeddings
* Cross-Encoder (MiniLM) – re-ranking
* Streamlit – UI
* Ollama (Llama 3) –  summarization
* Databricks (Delta Lake) – structured data storage and feature engineering
* Semantic Data Models – candidate feature representation for scoring and analytics
* Databricks Genie – natural language querying over structured recruiter insights

---

## ▶️ Setup Instructions

### 1. Create Environment

```bash
conda create -n resume_rank python=3.10
conda activate resume_rank
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### Step 1: Build Index (Offline)

* Place resumes in: `data/resumes/`

```bash
python build_index.py
```

Generates:

```
resume_index/
├── faiss.index
├── chunk_meta.pkl
└── resume_meta.pkl
```

---

### Step 2: Run Application (Online)

```bash
ollama pull llama3
ollama serve
```

```bash
python -m streamlit run app.py
```

---

## 🔍 Example Query

> Data analyst with Python, SQL, and Tableau experience

---

## 🔄 What Happens During Search

* Query embedding generation
* FAISS semantic retrieval
* Resume-level aggregation
* Features Computed using metadata from delta tables 
* Cross-encoder re-ranking
* Controlled LLM summarization

---

## 📈 Scoring Explained

* FAISS similarity → initial retrieval
* Cross-encoder → final ranking signal
* Scores are relative (ranking matters more than raw values)

---

## 🔮 Future Work

* Benchmark RAG vs Cross-Encoder vs Hybrid approaches
* MLflow-based tuning and evaluation
* Role-specific prompt templates
* Responsible AI: LLM-as-judge, prompt injection safeguards

---

## 📜 License

This project is intended for educational and internal enterprise use.
