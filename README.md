### AI POWERED RESUME SEARCH AND RANKING SYSTEM: 

To build an AI-powered resume ranking system using semantic search, chunk-level relevance scoring, and cross-encoder reranking, combined with structured features to deliver evidence-backed candidate recommendations, mirroring production-grade hiring intelligence systems.

## PROBLEM:

## Challenges at recruiter level: 

- Exact keyword dependency causes vocabulary mismatch ( “ML Engineer” vs “Machine Learning Engineer”), even though we test and try with so many boolean searches. 
- No ranking or prioritization based on contextual relevance, No explanation and evidence of why a candidate matches or does not match.
- Recruiters spend excessive time opening and manually scanning resumes line by line due to resume overload in ATS systems.
- Skill gaps and weak evidence must be manually inferred, forcing recruiters to interpret context and relevance on their own.
- Time to screen, shortlist, and hire increases, directly impacting hiring velocity and candidate experience.

## At candidate level: 
Highly relevant candidates are missed because scoring is applied to entire resumes without surfacing specific supporting evidence.

## GOAL:

Serve Hiring Managers and Recruiters across functions (Engineering, Global Functions, Professional Services, etc.) Let users query large resume sets by skills, experience, and Reduce time-to-screen by surfacing the most relevant candidates and summaries.

---

### How It Works (Architecture)

PHASE 1: Offline (Run Once) Data Engineering & Indexing 
Resumes
→ Read & Chunk - Resume Ingestion and Text Normalisation
→ Section detection (Experience, Skills, Education)
→ Feature store for deterministic metadata filtering (location, experience, education)
  Resume chunking (Section based) and embedding generation (Sentence Transformers) 
  FAISS-based semantic index 
---
PHASE 2: Online (Every Query) - Retrieval, Ranking & Explanation
→ Natural language recruiter query input
  Bi-encoder semantic retrieval
  Chunk-to-resume score aggregation by segment. 
  Feature-based scoring
  Cross-encoder re-ranking of top candidates
  Final score based on these three.
  Evidence based summary from resume (experience, skills, gaps, strengths etc)
→ Streamlit UI
---

#### Key Design Principles

1️⃣ Offline vs Online Separation:

Index construction is compute-heavy and runs once

Query-time inference is fast and lightweight

Prevents recomputation and improves scalability

2️⃣ Deterministic Ranking, Generative Summaries:

Ranking is driven by retrieval + reranking

LLMs are used only for post-hoc summarization

Avoids hallucinations and keeps decisions explainable


___

### Unique aspects that differentiate this from traditional ATS:

- Chunk-level scoring for sections: reduces resume length bias and focuses ranking on strongest query-relevant evidence.

- Transparency by design: surfaces both strengths and gaps/weak evidence so recruiters understand tradeoffs.

- Not brand-driven: does not rely on school/company brand, social influence, or engagement proxies.

- Explainability via scores + metadata + feature signals: recruiters can see why Candidate A ranked above Candidate B using semantic match, relevance score, detected skills, and evidence-backed summaries.

- Recruiter workflow compatible: modular pipeline that can plug into ATS workflows—retrieve → rerank → explain—without changing final decision ownership.
  
---

**Evaluation Metrics:**::

<img width="852" height="130" alt="Screenshot 2025-11-06 at 16 00 45" src="https://github.com/user-attachments/assets/c6c54f71-4c18-4c74-9086-f6d29a68dccf" />

Relevancy score: Using cosine similarity and cross encoder scores. 
--- 

**Streamlit UI**

<img width="537" height="629" alt="Screenshot 2026-02-03 at 21 01 18" src="https://github.com/user-attachments/assets/76036c73-fb74-4de6-9819-dea824938634" />

<img width="537" height="629" alt="Screenshot 2026-02-03 at 21 01 18" src="https://github.com/user-attachments/assets/fbe35973-903f-4f23-a508-3d8fc5cafb04" />

---

## How its helping: 
- AI-assisted, recruiter decision-making/
- Reduces time spent on sourcing -  Strongly true — summary + ranking + skills eliminate resume skimming.
- Fairness is measurable and not assumed : The system avoids hard filters and exposes its scoring logic, making fairness observable and auditable.
- Helping in “Should I open this resume?”
- Scoring transparency:  Why is this person higher than the other?”
- Qualified candidate discovery increases, Semantics catch non-obvious matches, Projects and experience outweigh keyword lists.


## Future Work: 
- Comparison from baseline (TF-IDF cosine similarity and boolean searches)
- Precision@k
- Reduction of False Positives
- Multi-query to let user know if missing out on a candidate due to hard constraints.
  
## Technology Stack

- Python 3.10
- FAISS – fast vector similarity search
- Sentence-Transformers – semantic embeddings
- Cross-Encoder (MiniLM) – reranking for precision
- Streamlit – interactive UI
- Ollama (Llama 3) – optional local LLM for summarization

---

## Setup Instructions (Local)
1️⃣ Create Environment
conda create -n resume_rank python=3.10
conda activate resume_rank

2️⃣ Install Dependencies:

pip install -r requirements.txt

###  Step 1: Build the Index (Offline)

Place resumes inside:
data/resumes/

Run:
python build_index.py

This generates: resume_index/

├── faiss.index

├── chunk_meta.pkl

└── resume_meta.pkl

This step is executed only when resumes change.

### Step 2: Run the Application (Online)
Start the local LLM (optional but recommended)
ollama pull llama3
ollama serve

Launch the Streamlit app
python -m streamlit run app.py

### Open the browser and enter queries like:

Data analyst with Python, SQL, and Tableau experience

----

### What Happens During a Search:

- Query Embedding
- Query is converted to a vector
- FAISS Vector Search
- Retrieves semantically similar resume chunks
- Resume-Level Aggregation
- Prevents longer resumes from dominating
- Cross-Encoder Re-Ranking
- Improves final ordering accuracy
- Optional LLM Summarization
- Generates structured, concise summaries
- Uses only top-ranked content
- Transparent relevance scores

## Scoring Explained:

Semantic Match (FAISS)
Cosine similarity 
Final Relevance (Cross-Encoder)
Stronger signal used for final ordering.

Scores are relative, not absolute — ranking quality matters more than raw values.

## Business Impact:
1. Faster Hiring

Reduces resume screening time from hours to seconds

2. Better Candidate Quality:

Finds strong matches beyond keyword overlap

3. Reduced Bias

Anonymization removes identity-based signals

4. Scalable

Works for tens or thousands of resumes

5. Trustworthy AI

LLM does not make decisions
Used only for summarization and explanation

---

📜 **License**

This project is intended for educational and internal enterprise use.
