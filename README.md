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

### * Named Entity Recognition (NER) and Synonym handling and ontology mapping


<img width="495" height="800" alt="Screenshot 2026-03-19 at 16 25 31" src="https://github.com/user-attachments/assets/08814023-8369-43de-b351-8ce41d98d1f4" />


<img width="650" height="738" alt="Screenshot 2026-03-19 at 16 25 56" src="https://github.com/user-attachments/assets/8ee40c0b-1be9-4191-b376-0c0bee044216" />


<img width="678" height="490" alt="Screenshot 2026-03-19 at 16 25 17" src="https://github.com/user-attachments/assets/353518bb-19f7-43d7-8af6-c4b380b51857" />

### * Feature extraction (experience, domain, education, location) and Storage in delta live table in UC (Entity-relationship Diagram and Data Modelling) 

<img width="646" height="456" alt="image" src="https://github.com/user-attachments/assets/3b55a914-1a61-4da1-979f-02717034f769" />

#### * Unity Catalog - Delta tables. 

<img width="919" height="688" alt="Screenshot 2026-03-13 at 18 45 49" src="https://github.com/user-attachments/assets/afb9e1cb-420e-4985-abb3-7066eb80d9a0" />

### *Recruiter self-service analytics for talent insights. 

<img width="1376" height="566" alt="image" src="https://github.com/user-attachments/assets/f274b2e4-68ff-49b4-9764-c093909c4186" />


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
### Prototype:

#### HR/Recruiter/HM puts a query:

<img width="2614" height="1448" alt="image" src="https://github.com/user-attachments/assets/0eb1f3aa-7a62-44fe-8a75-a94c5ed2e13e" />

#### RAG Retrives evidence from resume using meta data, embeddings and Controlled Prompt Engineering, Highlights strengths and gaps with supporting evidence.  

<img width="762" height="647" alt="Screenshot 2026-03-15 at 22 46 30" src="https://github.com/user-attachments/assets/8435cd3b-e120-4b05-956b-58ebdfc02cf4" />

#### Breakdown of why this candidate is ranked higher using structured features and Ranking logic.  

<img width="736" height="183" alt="Screenshot 2026-03-15 at 22 46 39" src="https://github.com/user-attachments/assets/5e1ddd26-2cd3-4e4c-9f0a-c9876d2e9fe4" />


## 🔎 Explainability & Transparency:

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
