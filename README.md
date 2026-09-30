# FlightWise ✈️

> **Evidence-grounded airline disruption and passenger-policy assistant**

FlightWise is a Retrieval-Augmented Generation (RAG) prototype that helps passengers find clear, airline-specific answers about baggage, cancellations, refunds, conditions of carriage, and passenger rights. Answers are generated from a curated policy corpus and include retrieved evidence so users can verify the result.

**Generative AI — Assignment 3 · AI & Data Science Program · Jio Institute**

## Team

- Aishwarya Nevrekar — `27PGAI0028`
- Deepanshi Bansal — `27PGAI0110`
- Kushagra Gupta — `27PGAI0115`
- Kaushik Gadipelly — `27PGAI0074`

## Why FlightWise?

Airline policies are often distributed across multiple webpages and PDF documents, written in airline-specific language, and updated over time. FlightWise addresses this by:

- restricting retrieval to the selected airline where possible;
- combining semantic and keyword-based retrieval;
- generating structured, evidence-grounded responses; and
- exposing the supporting policy sources used for an answer.

> **Important:** FlightWise is an academic prototype, not legal, financial, or travel advice. Airline policies and passenger-rights rules can change. Always verify important decisions against the airline's current official policy.

## Features

- Airline-specific policy questions
- Coverage of baggage, cancellation/refund, conditions of carriage, and passenger rights
- Hybrid retrieval using Chroma similarity search and BM25 keyword search
- Metadata-aware retrieval to reduce accidental mixing of airline policies
- LangGraph `retrieve → generate` workflow
- Pydantic-structured responses
- Source-backed answers for easier verification
- Gradio demo interface
- Evaluation set with 38 questions and retrieval metrics

## Architecture

```text
User question + airline selection
                │
                ▼
        Query and metadata filters
                │
                ▼
   Hybrid retrieval: Chroma + BM25
                │
                ▼
        Relevant policy passages
                │
                ▼
  LangGraph retrieve → generate workflow
                │
                ▼
 Structured answer with supporting sources
```

## Technical Stack

| Area | Technology |
| --- | --- |
| Language and tooling | Python, Jupyter, [uv](https://docs.astral.sh/uv/) |
| Orchestration | LangChain, LCEL, prompt templates, LangGraph |
| Demo UI | Gradio |
| LLM | Groq — `openai/gpt-oss-120b` with temperature `0.1` |
| Embeddings | Ollama — `nomic-embed-text` |
| Vector search | Chroma |
| Keyword search | BM25 via `rank-bm25` |
| Data and parsing | pandas, BeautifulSoup, pypdf, lxml |
| Validation and configuration | Pydantic, python-dotenv |

## Repository Layout

```text
.
├── notebooks/
│   └── FlightWise_RAG_36_Sources.ipynb   # End-to-end ingestion, retrieval, evaluation, and demo
├── data/
│   ├── airline_sources.csv               # Source manifest and metadata
│   └── policies/                         # Downloaded/cached policy PDF snapshots
├── evaluation/
│   ├── evaluation_questions.csv          # Evaluation questions
│   └── ...                               # Retrieval/evaluation outputs
├── presentation/                         # Project presentation materials
├── FlightWise_Official_Dataset.zip       # Offline copy of the corpus
├── .env.example                           # Environment-variable template
├── requirements.txt                        # Direct dependencies
├── requirements-lock.txt                  # Locked dependency versions
└── README.md
```

## Quick Start

### 1. Clone the repository

```bash
git clone https://github.com/aishwaryanevrekar/FlightWise_RAG_Project.git
cd FlightWise_RAG_Project
```

### 2. Create a virtual environment

Install [uv](https://docs.astral.sh/uv/getting-started/installation/) if it is not already available:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Create and activate the environment:

```bash
uv venv

# macOS/Linux
source .venv/bin/activate

# Windows PowerShell
.venv\Scripts\Activate.ps1
```

Install the project dependencies:

```bash
uv pip install -r requirements.txt
```

### 3. Configure the language model

Install [Ollama](https://ollama.com), start the Ollama service, and download the embedding model:

```bash
ollama pull nomic-embed-text
```

Create a local environment file:

```bash
cp .env.example .env
```

On Windows, copy `.env.example` to `.env` using File Explorer or PowerShell. Add your Groq API key to `.env`:

```dotenv
GROQ_API_KEY=your_groq_api_key_here
```

Never commit `.env` or API keys.

### 4. Run the notebook

Open `notebooks/FlightWise_RAG_36_Sources.ipynb` and run the cells in order. The notebook covers:

1. loading the source manifest;
2. downloading or reusing cached policy snapshots;
3. parsing and chunking documents;
4. creating the Chroma and BM25 retrievers;
5. running retrieval and evaluation experiments; and
6. launching the Gradio demo.

Cached files in `data/policies/` are reused when available, so the corpus does not need to be downloaded again for every run.

## Data and Evaluation

- The corpus contains **36 policy sources** listed in `data/airline_sources.csv`.
- Source metadata includes airline, category, title, URL, document type, and region.
- The evaluation set contains **38 questions** in `evaluation/evaluation_questions.csv`.
- Retrieval is compared across similarity search, MMR, and hybrid BM25 + Chroma retrieval.
- Evaluation includes airline-hit and category-hit measurements.

Because airline webpages and policies may change, refresh the source snapshots and re-run evaluation before using the project for a new demonstration or analysis.

## Troubleshooting

### Ollama connection or model errors

Confirm that Ollama is installed and running, then verify the model is available:

```bash
ollama list
ollama pull nomic-embed-text
```

### Missing API key

Confirm that `.env` exists at the repository root and contains a valid `GROQ_API_KEY`. Restart the notebook kernel after changing environment variables.

### Stale or unavailable policy pages

Some airline websites may block automated requests, change their URL structure, or update their content. Check the source URL in `data/airline_sources.csv`, refresh the cached snapshot, and record any source changes before re-running the pipeline.

### Notebook state issues

If results become inconsistent, restart the kernel and run all cells from the beginning. Avoid committing generated vector-store files or local notebook outputs unless they are intentionally part of the project deliverables.

## Limitations

- The system answers only from the documents available in its indexed corpus.
- Retrieved policy text may be incomplete, stale, or affected by PDF extraction quality.
- A source-backed answer is not a guarantee that the policy applies to a specific itinerary or passenger.
- The prototype does not replace the airline, airport, regulator, or a qualified legal professional.

## License and Academic Use

This repository was created as an academic project for the Generative AI course at Jio Institute. Refer to the repository contents for the included dataset and presentation materials. Airline policy documents remain subject to their respective publishers' terms and copyrights.
