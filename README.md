# FlightWise
**Evidence-Grounded Airline Disruption and Passenger Policy Assistant**

Generative AI - Assignment 3, AI & Data Science Program, Jio Institute

**Team:** Aishwarya Nevrekar (27PGAI0028), Deepanshi Bansal (27PGAI0110), Kushagra Gupta (27PGAI0115), Kaushik Gadipelly (27PGAI0074)

## Project Description
Airline policy information (baggage, cancellation/refund, conditions of carriage, passenger rights) is scattered across many pages and PDFs. FlightWise is a Retrieval-Augmented Generation (RAG) assistant that answers passenger-policy questions from a custom corpus of **36 official sources** from 12 airlines (Air India, IndiGo, Air India Express, Akasa Air, SpiceJet, Alliance Air, Emirates, Qatar Airways, Etihad, Singapore Airlines, Lufthansa, British Airways) plus DGCA/MoCA regulations.

## Tech Stack
- **Language/tooling:** Python, Jupyter, [uv](https://docs.astral.sh/uv/)
- **Orchestration:** LangChain (LCEL, prompt templates), LangGraph
- **LLM:** Groq (`openai/gpt-oss-120b`, temperature 0.1)
- **Embeddings:** Ollama `nomic-embed-text`
- **Vector store:** Chroma; BM25 (`rank-bm25`) for keyword retrieval
- **Data/other:** Pydantic, pandas, BeautifulSoup, pypdf

## Approach
1. **Corpus:** 36 sources listed in `data/airline_sources.csv` with airline, category, title, URL, type and region metadata.
2. **Ingestion:** one notebook cell downloads the sources (web pages saved as PDF snapshots) into `data/policies/`, then loads the PDFs with `PyPDFLoader`, splits with `RecursiveCharacterTextSplitter` (1000 chars, 200 overlap), embed and index in Chroma.
3. **Retrieval:** compared similarity search, MMR, and hybrid (BM25 + Chroma via `EnsembleRetriever`), with airline metadata filters to avoid mixing policies.
4. **Generation:** LangGraph `retrieve -> generate` workflow; Pydantic structured output; answers grounded in retrieved sources.
5. **Evaluation:** 38 questions in `evaluation/evaluation_questions.csv`, scored by airline hit rate and category hit rate.

## How to Run
1. Install uv:
   ```bash
   curl -LsSf https://astral.sh/uv/install.sh | sh   # Windows: see docs.astral.sh/uv
   ```
2. Create the environment and install dependencies:
   ```bash
   uv venv
   source .venv/bin/activate   # Windows: .venv\Scripts\activate
   uv pip install -r requirements.txt
   ```
3. Install [Ollama](https://ollama.com), then pull the embedding model:
   ```bash
   ollama pull nomic-embed-text
   ```
4. Copy `.env.example` to `.env` and set `GROQ_API_KEY`.
5. Open `notebooks/FlightWise_RAG_36_Sources_OneCell.ipynb` and run all cells in order. The dataset cell reuses cached PDFs in `data/policies/`, so it doesn't re-download them.

## Structure
```text
├── notebooks/FlightWise_RAG_36_Sources_OneCell.ipynb   # main pipeline
├── data/                            # source manifests and policies/ (downloaded PDFs)
├── evaluation/                      # questions and retrieval results
├── presentation/                    # slides
├── FlightWise_Official_Dataset.zip  # offline copy of the corpus
└── requirements.txt
```

## Notes
- Do not commit `.env`, API keys or the Chroma database.
- Some airline pages may change or block automated loading; re-check before a demo.
- Academic prototype, not legal advice.
