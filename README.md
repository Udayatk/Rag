# 🔍 RAG — Retrieval-Augmented Generation

![Python](https://img.shields.io/badge/Python-3.9%2B-blue?logo=python&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Active-brightgreen)

> A production-ready **Retrieval-Augmented Generation (RAG)** pipeline that grounds large language model (LLM) responses in your own documents, dramatically reducing hallucinations and enabling accurate, citation-backed answers over private knowledge bases.

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Architecture](#-architecture)
- [Tech Stack](#-tech-stack)
- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
- [Configuration](#-configuration)
- [Usage](#-usage)
- [Project Structure](#-project-structure)
- [How It Works](#-how-it-works)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🧠 Overview

RAG combines the power of **dense vector retrieval** with **generative AI** to answer questions using your own data. Instead of relying solely on the training knowledge of an LLM, this system:

1. **Ingests** your documents (PDF, DOCX, TXT, HTML, etc.).
2. **Chunks and embeds** them into a high-dimensional vector space.
3. **Stores** the embeddings in a vector database for lightning-fast similarity search.
4. At query time, **retrieves** the most relevant chunks.
5. **Augments** the LLM prompt with retrieved context to produce grounded, accurate answers.

---

## ✨ Features

- 📄 **Multi-format document ingestion** — PDF, Word, plain text, Markdown, HTML
- ✂️ **Configurable text chunking** — overlap-aware splitting with customizable chunk size
- 🔢 **Multiple embedding models** — OpenAI `text-embedding-ada-002`, HuggingFace sentence-transformers, and more
- 🗄️ **Pluggable vector stores** — FAISS (local), Chroma, Pinecone, Weaviate
- 🤖 **Multiple LLM backends** — OpenAI GPT-4/3.5, Anthropic Claude, local Ollama models
- 🔗 **LangChain integration** for composable chains and agents
- 💬 **Conversational memory** — multi-turn chat with history-aware retrieval
- 📝 **Source citation** — every answer references the document(s) it came from
- 🚀 **REST API** built with FastAPI
- 🖥️ **Interactive UI** via Streamlit
- 🐳 **Docker support** for easy deployment

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                       INGESTION PIPELINE                    │
│                                                             │
│  Documents  ──►  Loader  ──►  Chunker  ──►  Embedder       │
│  (PDF/DOCX/        │            │              │            │
│   TXT/HTML)        │            │              ▼            │
│                    │            │         Vector Store      │
│                    └────────────┘         (FAISS/Chroma)    │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                        QUERY PIPELINE                       │
│                                                             │
│  User Query ──► Embedder ──► Vector Store ──► Top-k Docs   │
│                                                    │        │
│                                                    ▼        │
│                              Prompt Template + Context      │
│                                                    │        │
│                                                    ▼        │
│                                               LLM (GPT/     │
│                                               Claude/Ollama)│
│                                                    │        │
│                                                    ▼        │
│                                           Answer + Sources  │
└─────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Language | Python 3.9+ |
| LLM Orchestration | LangChain |
| Embedding Models | OpenAI / HuggingFace sentence-transformers |
| Vector Database | FAISS / Chroma / Pinecone |
| LLM Providers | OpenAI GPT-4, Anthropic Claude, Ollama |
| API Framework | FastAPI |
| UI | Streamlit |
| Containerization | Docker / Docker Compose |

---

## ✅ Prerequisites

- Python **3.9** or higher
- An **OpenAI API key** (or any supported LLM provider key)
- `pip` or `conda` for package management
- *(Optional)* Docker & Docker Compose

---

## 🚀 Installation

### 1. Clone the repository

```bash
git clone https://github.com/Udayatk/Rag.git
cd Rag
```

### 2. Create and activate a virtual environment

```bash
python -m venv venv
# On macOS / Linux
source venv/bin/activate
# On Windows
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. *(Optional)* Run with Docker

```bash
docker-compose up --build
```

---

## ⚙️ Configuration

Copy the example environment file and fill in your credentials:

```bash
cp .env.example .env
```

Edit `.env`:

```env
# LLM Provider
OPENAI_API_KEY=your_openai_api_key_here

# Embedding Model
EMBEDDING_MODEL=text-embedding-ada-002

# Vector Store  (faiss | chroma | pinecone)
VECTOR_STORE=faiss

# Pinecone (only if VECTOR_STORE=pinecone)
PINECONE_API_KEY=your_pinecone_api_key
PINECONE_ENVIRONMENT=us-east-1-aws
PINECONE_INDEX_NAME=rag-index

# Chunking
CHUNK_SIZE=500
CHUNK_OVERLAP=50

# Retrieval
TOP_K=5
```

---

## 💻 Usage

### Ingest documents

Place your documents in the `data/` directory, then run:

```bash
python ingest.py --source data/
```

### Start the API server

```bash
uvicorn app.main:app --reload --port 8000
```

API docs will be available at `http://localhost:8000/docs`.

### Launch the Streamlit UI

```bash
streamlit run ui/app.py
```

### Query via CLI

```bash
python query.py --question "What is the refund policy?"
```

### Example API request

```bash
curl -X POST http://localhost:8000/query \
  -H "Content-Type: application/json" \
  -d '{"question": "Summarize the key findings of the report."}'
```

**Example response:**

```json
{
  "answer": "The key findings of the report indicate that ...",
  "sources": [
    {"document": "annual_report_2024.pdf", "page": 12},
    {"document": "annual_report_2024.pdf", "page": 15}
  ]
}
```

---

## 📁 Project Structure

```
Rag/
├── app/                    # FastAPI application
│   ├── main.py             # App entry point & routes
│   ├── models.py           # Pydantic request/response models
│   └── rag_chain.py        # Core RAG chain logic
├── data/                   # Raw documents (not committed)
├── embeddings/             # Persisted vector store (not committed)
├── ui/
│   └── app.py              # Streamlit UI
├── ingest.py               # Document ingestion script
├── query.py                # CLI query script
├── requirements.txt        # Python dependencies
├── .env.example            # Environment variable template
├── docker-compose.yml      # Docker Compose configuration
├── Dockerfile              # Container definition
└── README.md               # This file
```

---

## 🔬 How It Works

### Ingestion

1. **Load** — Documents are loaded using LangChain document loaders (supports PDF, DOCX, TXT, HTML, Markdown).
2. **Split** — Text is chunked with a configurable `CHUNK_SIZE` and `CHUNK_OVERLAP` to preserve context across chunk boundaries.
3. **Embed** — Each chunk is converted to a dense vector using the configured embedding model.
4. **Store** — Vectors and their metadata (source, page number, etc.) are persisted in the vector store.

### Retrieval & Generation

1. **Embed the query** — The user's question is embedded using the same model used during ingestion.
2. **Similarity search** — The vector store returns the top-`k` most semantically similar chunks.
3. **Prompt augmentation** — Retrieved chunks are injected into a prompt template alongside the original question.
4. **LLM response** — The LLM generates an answer grounded strictly in the provided context.
5. **Citation** — Source document names and page numbers are returned alongside the answer.

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. **Fork** the repository.
2. **Create** a feature branch: `git checkout -b feature/your-feature-name`
3. **Commit** your changes: `git commit -m "feat: add your feature"`
4. **Push** to your branch: `git push origin feature/your-feature-name`
5. **Open** a Pull Request describing your changes.

Please ensure your code follows the existing style and includes relevant tests.

---

## 📜 License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details.

---

> Built with ❤️ by [Udayatk](https://github.com/Udayatk)
