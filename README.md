
### AI-Powered Intelligent Recruiter Assistant

An explainable AI system for semantic resume matching and recruiter-facing candidate ranking and insights , designed to reduce manual screening time, keyword based bias and improve candidate discovery.

### 🚀 Business Problem
Keyword based ATS systems fail to capture semantic relevance
Lack of transparent ranking makes candidate evaluation unclear
Recruiters spend significant time manually reviewing resumes
Limited support for recruiter-driven analytics and insights

### 💡 Solution Overview: 

This system transforms unstructured resume data into structured, querying data using Databricks AI functions and SQL( AI_Parse_document and AI_Extract), enabling consistent processing across diverse formats.

It leverages semantic retrieval and hybrid ranking to identify the most relevant candidates. By combining embedding-based search with structured feature scoring and re-ranking, the system evaluates candidates on skills, experience, domain alignment, and seniority.

The platform also supports natural language queries and generates grounded, evidence-based summaries to help recruiters quickly assess candidate fit.

### 💼 Business Impact
⏱️ Significantly reduces resume screening time
🎯 Improves candidate quality through context-aware matching
📈 Scales efficiently across large candidate datasets
🤝 Enhances recruiter decision-making with transparent insights

### 🧠 How It Works (High-Level)

- Recruiter submits a natural language query
- Query is converted into embeddings for semantic search
- Relevant resume sections are retrieved using FAISS
- Section-level results are aggregated to compute resume-level relevance
- Structured feature scoring evaluates candidate fit across key dimensions
- Cross-encoder re-ranks top candidates for improved precision
- Controlled LLM generates concise, evidence based candidate summaries

---

### Project Structure

- `app.py` - Streamlit application for recruiter query input, retrieval, ranking, and summary generation  
- `preprocessing.py` - Resume cleaning, parsing, section detection, and metadata extraction  
- `build_index.py` - Offline pipeline to generate embeddings and build the FAISS index  
- `config.py` - Model configuration, file paths, and scoring parameters  
- `resume_index/` - Stored FAISS index and metadata artifacts

---

### 📁 Sample Data

This repository includes a small set of anonymized sample resumes for demonstration purposes.

- Located in `data/resumes/`    
- Covers diverse roles (e.g., Data Analyst, Data Scientist, Business Analyst)  
- Used for testing retrieval, ranking, and explainability  

These samples help demonstrate how the system performs across different candidate profiles.

---

### 🔍 Data Pipeline Architecture:


<img width="843" height="439" alt="Screenshot 2026-04-26 at 13 49 02" src="https://github.com/user-attachments/assets/23d05169-5299-40b8-8b1b-4c24850b2b70" />


### Phase 1: Resume Processing & Indexing:

The pipeline transforms unstructured resume PDFs into structured data using Databricks AI functions.

- Ingestion: Resumes are read from a Unity Catalog Volume using read_files.

- Parsing: ai_parse_document converts PDFs into structured JSON while preserving layout.
  
- Extraction: ai_extract (2-pass approach) extracts contact details and detailed profile information (skills, experience, education).
  
- Modeling: Data is flattened into tables (resume_core, resume_skills, resume_experience, resume_education) using explode() for efficient querying and lineage.****
  
- Cleaning: (whitespace normalization, formatting fixes, Unicode handling)
n)

- Synonyms : Saved data is expanded using abbreviations and matched with synonyms for consistent matching
  
- Mapping : Map skills and tittles to a predefined ontology dictionary for consistent representation.


### Phase 2: Embeddings & Retrieval Index
Generate section-level embeddings using Sentence-BERT
Store embeddings in FAISS for efficient similarity search

### Phase 3: Online Retrieval:

#### 🔍 Retrieval
Convert recruiter query into embeddings
Retrieve top-N relevant resume sections from FAISS
Aggregate section-level matches into resume-level relevance scores

#### 🧠 Scoring & Re-Ranking

Compute structured feature scores:

- Skill overlap
- Experience alignment
- Domain relevance
- Seniority fit
- Gaps

### PHASE 4: RE-RANKING:

Deep model: evaluates query + resume together
Captures contextual meaning
Improves precision (better ordering)


### 🧪 Prototype


### Reasoning and explianable ranking Output:


<img width="598" height="507" alt="Screenshot 2026-04-24 at 17 06 26" src="https://github.com/user-attachments/assets/417ee2c5-df2b-4b3e-9c37-f44211e67d00" />

---

### 🔎 Explainability & Transparency

- Displays contribution of each feature  
- Shows why one candidate ranks higher than another  
- Highlights strengths and gaps with evidence  
- Reduces black-box decision making  

---

### 📊 Evaluation Metrics:

The system was evaluated across latency, ranking quality, and reliability.

- Latency across pipeline stages
- Relevance Score
- Precision@K / Recall@K  
- Consistency and hallucination checks

<img width="507" height="160" alt="Screenshot 2026-04-03 at 19 55 18" src="https://github.com/user-attachments/assets/35539c2a-8516-4cec-851b-cd1e26d180f1" />

#### Notes

- Relevance Score represents the semantic similarity between query and retrieved candidates after ranking  
- Low error rate indicates stable pipeline execution  
- Latency includes retrieval, re-ranking, and summary generation

--- 

### ⚖️ Comparison of Scoring Approaches

- Semantic similarity (RAG-only)  
- Cross-encoder + RAG  
- Feature-based signals + Cross-encoder + RAG

<img width="838" height="368" alt="Screenshot 2026-04-24 at 17 03 33" src="https://github.com/user-attachments/assets/3bd354df-0d4e-401a-ad8c-b4149f36e053" />

<img width="861" height="446" alt="Screenshot 2026-04-24 at 17 03 44" src="https://github.com/user-attachments/assets/71dc0f2d-0822-47aa-be2f-679ede81d0c4" />


---

### 🛠️ Tech Stack

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

### ⚠️ Current Status

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
