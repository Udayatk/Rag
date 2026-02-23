# 🧠 Production RAG Pipeline

A complete **Retrieval-Augmented Generation (RAG)** pipeline built with **Google Gemini**, **ChromaDB**, and **PyPDF2**. This project demonstrates how to go from a raw PDF document all the way to an intelligent Q&A system powered by a Large Language Model.

---

## 📌 Overview

This notebook (`14_production_rag.ipynb`) walks through building a production-ready RAG system step by step:

```
PDF → Extract Text → Chunk → Embed → Store in Vector DB
                                        ↓
Question → Embed → Search → Retrieve → LLM → Answer
```

---

## 🚀 Features

- 📄 **PDF Ingestion** — Load and extract text from any PDF using `PyPDF2`
- ✂️ **Text Chunking** — Split documents into overlapping chunks for better context preservation
- 🗄️ **Vector Storage** — Store embeddings in **ChromaDB** for fast semantic search
- 🔍 **Semantic Retrieval** — Find the most relevant chunks using Gemini embeddings
- 🤖 **LLM Generation** — Generate accurate, context-grounded answers using **Gemini 2.5 Flash Lite**

---

## 🛠️ Tech Stack

| Component        | Tool/Library                  |
|------------------|-------------------------------|
| Embeddings       | `gemini-embedding-001` (Google Gemini) |
| LLM              | `gemini-2.5-flash-lite` (Google Gemini) |
| Vector Database  | `ChromaDB`                    |
| PDF Parsing      | `PyPDF2`                      |
| Environment Vars | `python-dotenv`               |
| Language         | Python 3.13                   |

---

## 📁 Repository Structure

```
Rag/
├── 14_production_rag.ipynb   # Main RAG pipeline notebook
├── yash.pdf                  # Sample PDF used for Q&A (not tracked)
├── .env                      # Environment variables (not tracked)
└── README.md                 # Project documentation
```

---

## ⚙️ Setup & Installation

### 1. Clone the Repository

```bash
git clone https://github.com/Udayatk/Rag.git
cd Rag
```

### 2. Install Dependencies

```bash
pip install google-genai chromadb PyPDF2 python-dotenv
```

Or install the PDF library directly in the notebook:

```python
%pip install PyPDF2 -q
```

### 3. Configure Environment Variables

Create a `.env` file in the **parent directory** of this project:

```env
GEMINI_API_KEY=your_google_gemini_api_key_here
```

> 🔑 Get your Gemini API key from [Google AI Studio](https://aistudio.google.com/).

---

## 📖 How It Works

### Step 1: Load PDF

```python
from PyPDF2 import PdfReader

reader = PdfReader("your_document.pdf")
full_text = ""
for page in reader.pages:
    full_text += page.extract_text() + "\n"
```

### Step 2: Chunk the Document

Text is split into overlapping chunks (500 chars, 50-char overlap) to preserve context across boundaries:

```python
def chunk_text(text, chunk_size=500, overlap=50):
    chunks = []
    start = 0
    while start < len(text):
        end = start + chunk_size
        chunk = text[start:end].strip()
        if chunk:
            chunks.append(chunk)
        start = end - overlap
    return chunks
```

### Step 3: Embed & Store in ChromaDB

Each chunk is embedded using Gemini and stored in a ChromaDB collection:

```python
embedding = client.models.embed_content(
    model="gemini-embedding-001",
    contents=chunk
).embeddings[0].values

collection.add(documents=[chunk], embeddings=[embedding], ids=[f"chunk_{i}"])
```

### Step 4: Ask Questions (RAG)

```python
answer, sources = ask("What is Yash's educational background?")
print(answer)
```

The `ask()` function:
1. Embeds the question
2. Retrieves the top-3 most relevant chunks from ChromaDB
3. Passes them as context to Gemini to generate a grounded answer

---

## 💬 Example Output

```
❓ What is Yash's educational background?

💬 BITS Pilani Work Integrated Learning Programmes - Master of Technology - MTech,
   Computer Software Engineering (2023 - 2025)
   Global Academy Of Technology - Bachelor's degree, Computer Science (2019 - 2023)

📄 Sources:
  [1] ...and validation processes for critical quarterly changes...
  [2] ...AI Solutions Development Engineer June 2024 - March 2025...
```

---

## 🔑 Key Takeaways

1. **Extract** text from PDFs using `PyPDF2`
2. **Chunk** with overlap to preserve context between segments
3. **Embed & Store** in a vector database for fast semantic search
4. **Retrieve & Generate** — that's the essence of RAG

---

## 🗺️ What's Next?

This project wraps up RAG fundamentals. The next step is **Agents** — when retrieval alone isn't enough and the system needs to take autonomous actions.

---

## 📄 License

This project is open-source and available under the [MIT License](LICENSE).

---

## 🙋 Author

**Udayatk** — [GitHub Profile](https://github.com/Udayatk)