### AI POWERED INTELLIGENT RECRUITER ASSISTANT : 

## Business Problem & Importance:
-  Keyword-based ATS systems miss semantically relevant candidates
-  No ranking or prioritization based on contextual relevance with no explanation and evidence of why a candidate ranked higher or does not rank high. 
-  Recruiters spend excessive time opening and manually scanning resumes line by line due to resume overload in ATS systems.
-  Skill gaps and weak evidence must be manually inferred, forcing recruiters to interpret context and relevance on their own.
- Limited self-service analytics for recruiters

## Example:
Current ATS systems rely heavily on keyword matching and manual screening, leading to inefficiencies, missed qualified candidates, and limited transparency in decision-making. There is a need for a scalable, explainable and responsible AI system that enables intelligent resume matching and recruiter-facing analytics.

#### The primary user of this system is the recruiter or hiring manager performing early-stage screening.

## At candidate level: 
Highly relevant candidates are missed because scoring is applied to entire resumes without surfacing specific supporting evidence.

## GOAL:

Serve Hiring Managers and Recruiters across functions (Engineering, Global Functions, Professional Services) Let users query large resume sets by skills, experience, Education, Seniory, and Domain Preference. Reduce time-to-screen by surfacing the most relevant candidates and summaries with breakdown of why they are ranked higher. 

---

### How It Works (Architecture)

#### PHASE 1: Offline (Run Once) Data Engineering & Indexing

→ Read & Chunk - Resume Ingestion and Text Normalisation
→ Resume Formatting and Removing unicode/EXTRA SPACES
→ Date standardization 
→ Abbreviations expansion, Ontology mapping  and Synonym handling 
→ Section detection (Experience, Skills, Education and NER 
→ Feature store for deterministic metadata filtering (location, experience, education)

<img width="1102" height="1030" alt="image" src="https://github.com/user-attachments/assets/e95be3d7-d411-4ba0-9b43-548167ab8710" /> 


<img width="298" height="336" alt="Screenshot 2026-02-24 at 15 39 14" src="https://github.com/user-attachments/assets/0b6ad5d2-61ef-495b-8c38-5e741451b13f" />

The individual contribution scores of each feature provide transparency into why a particular resume is ranked higher. By analyzing the weighted contribution of domain match, skill fitment, experience alignment, seniority, and education, the system can clearly indicate which attributes most influenced the ranking decision for a given job query.
The overall Feature Score represents the aggregated weighted contribution of all structured features. This combined score directly impacts the final ranking, ensuring that resumes are evaluated based on their alignment with role-specific requirements.

→ Semantic Embeddings
→ Use pre-trained Sentence-BERT
→ Generate embeddings for resume sections
→ Store embeddings for retrieval in Vector DB FAISS.

Note: Section-level embeddings improve retrieval precision by aligning query intent with relevant resume segments rather than the entire document.


---

####  PHASE 2: Online (Every Query) - Retrieval, Ranking & Explanation:

  - Natural language recruiter query input
  - The query is converted into an embedding and compared against the FAISS index of resume embeddings for similarity search.
  - FAISS retrieves Top-N semantically similar resumes using bi-encoder. 
  - Chunk-to-resume score aggregation by segment. 
  - Feature-based scoring using metadata stored after NLP Processing. 
  - Cross-encoder re-ranking of top candidates

→  For retrieved candidates:
→ Chunk Similarity: Query matched against Experience, Skills, and Projects sections.
→ Feature-Based Scoring: Skill overlap, experience alignment, domain match, seniority match, gap penalty.
→ Cross-Encoder Re-Ranking (Top-K): Joint query–resume evaluation for deep contextual relevance.

Final candidate scores are generated using a hybrid evaluation framework that combines feature computation, cross-encoder re-ranking, and section-aware chunk-level scoring. The section-aware scoring mechanism applies customized weightage templates tailored to specific roles and hiring requirements.
The system dynamically adjusts the importance of resume sections (such as experience, skills, domain expertise, education, and location) based on the role being evaluated.

#### For example:

- Business Analyst roles: Responsibilities and demonstrated impact may carry greater weight than education or domain specialization.

- Data Scientist roles: Technical skills, hands-on experience, and domain expertise may be prioritized over factors such as location or formal education.

Using these customizable weightage templates, the system aligns scoring with recruiter priorities. It then generates evidence-based summaries grounded directly in the candidate’s resume, highlighting experience, skills, strengths, gaps, and overall role fit.


### Unique aspects that differentiate this from traditional ATS:

- Recruiters can see why Candidate A ranked above Candidate B using relevance score, detected skills, experience alignment.

- Chunk-level scoring for resume sections: reduces resume length bias and focuses ranking on query relevant sections.

- Transparency by design: surfaces both strengths, skills, experience  and gaps evidence so recruiters understand tradeoffs.

- Explainability and summaries via embeddings + metadata + feature signals and prompt engineering.

- Recruiter Talent Insights: Genie operates exclusively on structured candidate metadata and does not directly query raw resume text.

---

**Evaluation Metrics:**::

<img width="852" height="130" alt="Screenshot 2025-11-06 at 16 00 45" src="https://github.com/user-attachments/assets/c6c54f71-4c18-4c74-9086-f6d29a68dccf" />

--- 

**Figma UI** - https://smog-topaz-81345570.figma.site


<img width="783" height="566" alt="Screenshot 2026-02-24 at 15 40 14" src="https://github.com/user-attachments/assets/6ae37bc9-6c1b-4c41-883f-eb647c49b14a" />



<img width="660" height="736" alt="Screenshot 2026-02-24 at 15 43 10" src="https://github.com/user-attachments/assets/c3db87ce-451f-435e-b90b-7d1b8efd130a" />



<img width="685" height="197" alt="Screenshot 2026-02-23 at 14 28 50" src="https://github.com/user-attachments/assets/394691c8-02d2-4efb-ad90-6f0187213d04" />


---

## How its helping: 
- AI-assisted, recruiter decision-making. 
- Reduces time spent on sourcing -  Strongly true — summary + ranking + skills eliminate resume skimming.
- Fairness is measurable and not assumed : The system avoids hard filters and exposes its scoring logic, making fairness observable and auditable.
- Helping in “Should I open this resume?”
- Scoring transparency:  Why is this person higher than the other?”
- Qualified candidate discovery increases, Semantics catch non-obvious matches, Projects and experience outweigh keyword lists.


## Future Work: 
- Setting up comparsion benchmarks between RAG, Cross Encoders and structured features using MLflow tuning knobs. 
- Multiple Prompt Engineering templates for Mult diverse roles in enterprise systems
- Using Responsbile AI - LLMs as Judge - Prompt Injection to safeguard the sensitive information from LLM.
- Precision@k
- Reduction of False Positives
  
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
