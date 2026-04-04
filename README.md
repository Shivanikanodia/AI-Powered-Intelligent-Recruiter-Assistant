AI-Powered Intelligent Recruiter Assistant

An explainable AI system for semantic resume matching and recruiter-facing candidate ranking, designed to reduce manual screening time and improve candidate discovery beyond traditional ATS systems.

🚀 Business Problem
Keyword-based ATS systems miss semantically relevant candidates
No transparent ranking or explanation of candidate relevance
Recruiters spend significant time manually scanning resumes
Skill gaps and contextual relevance must be inferred manually
Limited recruiter self-service analytics
💡 Solution Overview

This system converts unstructured resume data into structured metadata using NLP techniques, handling diverse unstructured documents with varying formats and layouts, enabling efficient search, ranking, and explainability.

It combines semantic retrieval (Sentence-BERT + FAISS) with cross-encoder re-ranking to improve candidate matching.

The pipeline supports natural language queries from recruiters and returns the most relevant candidates along with transparent hiring signals (e.g., skill match, experience alignment, seniority fit, and domain relevance).

⭐ Key Features
Semantic search using Sentence-BERT (beyond keyword matching)
Hybrid ranking: FAISS + feature scoring + cross-encoder re-ranking
Section-level chunking for precise matching
Explainable scoring (skills, experience, domain, seniority, education)
Evidence-based candidate summaries
Recruiter analytics using structured metadata
End-to-end pipeline supporting multi-stage reasoning and explainable ranking
💼 Business Impact
⏱️ Reduces resume screening time from hours to seconds
🎯 Improves candidate quality via semantic matching
📈 Scales across thousands of resumes
🤝 Supports recruiter decision-making (not replaces it)
🧠 How It Works (High-Level)
Recruiter enters a natural language query, which is converted into embeddings
Query retrieves relevant resume sections from governed Delta tables
FAISS retrieves semantically similar resume sections
Scores are aggregated at the resume level using top-K max pooling
Feature-based scoring evaluates structured hiring signals (skill match, domain match, experience alignment, seniority fit)
Cross-encoder re-ranks top candidates
System generates controlled, explainable summaries using grounded prompts

The system follows a multi-stage reasoning pipeline, where retrieval, feature computation, re-ranking, and summarization progressively refine candidate relevance.

🔄 What Happens During Search
Query embedding generation
FAISS semantic retrieval
Resume-level aggregation
Feature computation using metadata from Delta tables
Cross-encoder re-ranking
Controlled LLM summarization
🔍 Detailed Architecture
<img width="799" height="750" alt="Architecture Diagram" src="https://github.com/user-attachments/assets/63591997-881c-4c6c-9eb7-f5fdbe51bb12" />
Phase 1: Offline Resume Processing & Indexing
Ingested resumes using pdfplumber and converted them into structured JSON format
Performed resume cleaning (removed whitespaces, multiple new lines, ASCII/Unicode inconsistencies, formatting fixes)
Section detection (Experience, Skills, Education)
Entity extraction using spaCy and NLTK
Normalization & Ontology Mapping
Abbreviation expansion and synonym handling
Ontology-based normalization to standardize skills and roles across varied resume formats
Feature Engineering & Storage
Extracted features: experience, domain, education, location
Stored in Delta Live Tables with lineage tracking
<img width="646" height="456" alt="Data Model" src="https://github.com/user-attachments/assets/3b55a914-1a61-4da1-979f-02717034f769" /> <img width="919" height="688" alt="ER Diagram" src="https://github.com/user-attachments/assets/afb9e1cb-420e-4985-abb3-7066eb80d9a0" />
Phase 2: Embeddings Generation
Sentence-BERT used for semantic embeddings
Section-level embeddings for higher precision
Stored in FAISS vector database
Phase 3: Online Retrieval & Ranking
Query embedding generation
FAISS retrieval (Top-N candidates)
Chunk-level similarity scoring and resume-level aggregation
Feature-Based Scoring
Skill overlap
Experience alignment
Domain match
Seniority fit
Gap penalties
Cross-Encoder Re-Ranking (Top-K)
Comparison of Scoring Approaches
Semantic similarity (RAG-only)
Cross-encoder outputs + RAG
Feature-based signals + Cross-encoder + RAG

Custom weight templates adapt scoring based on role:

Business Analyst → Experience & impact prioritized
Data Scientist → Skills & domain expertise prioritized
🧪 Prototype
Recruiter Query Input
<img width="2614" height="1448" alt="Query UI" src="https://github.com/user-attachments/assets/0eb1f3aa-7a62-44fe-8a75-a94c5ed2e13e" />
Retrieval + Reasoning Output

FAISS retrieves relevant resume sections, and the system generates explainable insights using grounded prompts. It highlights strengths and gaps with supporting evidence.

<img width="762" height="647" alt="Output" src="https://github.com/user-attachments/assets/8435cd3b-e120-4b05-956b-58ebdfc02cf4" />
Explainable Ranking Breakdown

Shows why a candidate is ranked higher using structured features and scoring logic.

<img width="736" height="183" alt="Ranking Explanation" src="https://github.com/user-attachments/assets/5e1ddd26-2cd3-4e4c-9f0a-c9876d2e9fe4" />
🔎 Explainability & Transparency
Displays contribution of each feature (skills, experience, domain, etc.)
Shows why one candidate ranks higher than another
Highlights strengths and gaps with supporting evidence
Reduces black-box decision making
📊 Evaluation Metrics

The system is evaluated across multiple dimensions to ensure retrieval quality, ranking effectiveness, and summary reliability:

Precision@K / Recall@K / NDCG@K for retrieval and ranking performance
Reduction in false positives in candidate matching
Latency across retrieval and ranking stages
Consistency and hallucination checks for LLM-generated summaries
Comparative evaluation:
RAG-only
Cross-encoder + RAG
Hybrid (features + cross-encoder + RAG)
🛠️ Tech Stack
Python 3.10
FAISS – vector similarity search
Sentence-Transformers – embeddings
Cross-Encoder (MiniLM) – re-ranking
Streamlit – UI
Ollama (Llama 3) – summarization
Databricks (Delta Lake) – structured data storage and feature engineering
Semantic Data Models – candidate feature representation
Databricks Genie – natural language querying for recruiter analytics
▶️ Setup Instructions
1. Create Environment
conda create -n resume_rank python=3.10
conda activate resume_rank
2. Install Dependencies
pip install -r requirements.txt
Step 1: Build Index (Offline)

Place resumes in:

data/resumes/

Run:

python build_index.py

Generates:

resume_index/
├── faiss.index
├── chunk_meta.pkl
└── resume_meta.pkl
Step 2: Run Application (Online)
ollama pull llama3
ollama serve
python -m streamlit run app.py
🔍 Example Query

Data analyst with Python, SQL, and Tableau experience

📈 Scoring Explained
FAISS similarity → initial retrieval
Cross-encoder → final ranking signal
Scores are relative (ranking matters more than raw values)
🔮 Future Work
Benchmark RAG vs Cross-Encoder vs Hybrid approaches
MLflow-based tuning and evaluation
Role-specific prompt templates
Responsible AI: LLM-as-judge, prompt injection safeguards
📜 License

This project is intended for educational and internal enterprise use.
