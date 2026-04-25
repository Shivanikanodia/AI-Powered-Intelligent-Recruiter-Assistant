
### AI-Powered Intelligent Recruiter Assistant

An explainable AI system for semantic resume matching and recruiter-facing candidate ranking, designed to reduce manual screening time and improve candidate discovery beyond traditional ATS systems.

### 🚀 Business Problem
Keyword-based ATS systems fail to capture semantic relevance
Lack of transparent ranking makes candidate evaluation unclear
Recruiters spend significant time manually reviewing resumes
Skill gaps and contextual fit require manual interpretation
Limited support for recruiter-driven analytics and insights

### 💡 Solution Overview

This system transforms unstructured resume data into structured, queryable metadata using NLP techniques, enabling consistent processing across diverse formats.

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
- Controlled LLM generates concise, evidence-based candidate summaries

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
- No personally identifiable information (PII) is included  
- Covers diverse roles (e.g., Data Analyst, Data Scientist, Business Analyst)  
- Used for testing retrieval, ranking, and explainability  

These samples help demonstrate how the system performs across different candidate profiles.

---

### 🔍 Detailed Architecture

<img width="1598" height="1500" alt="image" src="https://github.com/user-attachments/assets/689309a3-f9a1-4db2-993c-c3d28c04b4bf" />




### Phase 1: Offline Resume Processing & Indexing

Ingest resumes using pdfplumber and convert them into structured JSON
Perform cleaning (whitespace normalization, formatting fixes, Unicode handling)
Detect key sections (Experience, Skills, Education)
Extract entities using spaCy and NLTK

<img width="646" height="456" alt="image" src="https://github.com/user-attachments/assets/7a09b0c7-b2a7-4913-b70b-7e4dda033ca8" />


#### Normalization & Ontology Mapping

Expand abbreviations and resolve synonyms
Map skills to a predefined ontology for consistent representation

#### Feature Engineering & Storage

Derive structured features: experience, domain, education, location
Store in Delta Live Tables with lineage tracking

### Phase 2: Embeddings & Retrieval Index
Generate section-level embeddings using Sentence-BERT
Store embeddings in FAISS for efficient similarity search

### Phase 3: Online Retrieval & Ranking

#### 🔍 Retrieval
Convert recruiter query into embeddings
Retrieve top-N relevant resume sections from FAISS
Aggregate section-level matches into resume-level relevance scores

#### 🧠 Scoring & Re-Ranking

Compute structured feature scores:

Skill overlap
Experience alignment
Domain relevance
Seniority fit
Gap penalties


##### 🎯 Role-Specific Weighting

###### Business Analyst → Higher weight on experience and business impact
###### Data Scientist → Higher weight on technical skills and domain expertise
---

### 🧪 Prototype

### Query Input:

<img width="1838" height="1044" alt="image" src="https://github.com/user-attachments/assets/e2fcac7d-cf33-4801-a081-328ef6b73df6" />

---

### Retrieval + Reasoning Output:

FAISS retrieves relevant sections, computed features and generates explainable insights using grounded prompts.

CANDIDATE A:

<img width="642" height="728" alt="Screenshot 2026-04-04 at 13 09 45" src="https://github.com/user-attachments/assets/26838bb7-fcdf-421b-8be3-14ca14e40a05" />


CANDIDATE B:

<img width="508" height="463" alt="Screenshot 2026-04-04 at 13 11 14" src="https://github.com/user-attachments/assets/4655240b-25bb-43a7-9338-eceb8dcf79b9" />



<img width="293" height="250" alt="Screenshot 2026-04-19 at 18 57 55" src="https://github.com/user-attachments/assets/93535f27-5451-4b4a-b004-87ede066d55b" />

<img width="321" height="258" alt="Screenshot 2026-04-19 at 18 57 47" src="https://github.com/user-attachments/assets/b3d5c809-e86a-4ae8-ba74-f49f512c960d" />


---

### Explainable Ranking Breakdown:

Shows why a candidate A  is ranked higher then candidate B using structured features.

<img width="1470" height="404" alt="image" src="https://github.com/user-attachments/assets/45bb6c40-1c98-48b9-99e9-01845edbb1a2" />

![Ranking](https://github.com/user-attachments/assets/5e1ddd26-2cd3-4e4c-9f0a-c9876d2e9fe4)

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
