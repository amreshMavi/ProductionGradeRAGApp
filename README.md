#  Production-Grade RAG AI Agent

A production-ready Retrieval-Augmented Generation (RAG) application built in Python, featuring document ingestion, vector search, and a chat interface — with enterprise-grade observability, rate limiting, throttling, and concurrency control powered by **Inngest**.

---

##  What Is This?

Most RAG tutorials show you how to build something that *works*. This project shows you how to build something you can actually *ship*. It adds the production infrastructure that hobby projects typically miss:

- **Observability & logging** — know what your agent is doing and why
- **Retries** — handle transient failures gracefully
- **Rate limiting & throttling** — stay within API quotas without crashing
- **Concurrency control** — manage parallel requests safely

---

## 🏗️ Architecture & Tech Stack

```
User → Frontend (Chat UI)
          ↓
    Inngest Functions
    (Orchestration, Retries, Rate Limiting)
          ↓
    LlamaIndex (RAG Pipeline)
    ├── PDF Loading & Chunking
    ├── Embedding Generation (OpenAI)
    └── Query Engine
          ↓
    Qdrant (Vector Database)
    (Store & Retrieve document embeddings)
          ↓
    LLM (OpenAI GPT)
    (Generate final answers)
```

| Layer | Tool | Purpose |
|---|---|---|
| Orchestration | [Inngest](https://www.inngest.com/) | Background jobs, retries, rate limiting, throttling |
| RAG Framework | [LlamaIndex](https://www.llamaindex.ai/) | Document loading, chunking, indexing, querying |
| Vector Database | [Qdrant](https://qdrant.tech/) | Store and search document embeddings |
| LLM | OpenAI GPT | Generate responses from retrieved context |
| Frontend | Python / HTML | Chat interface for querying documents |

---

## ✨ Features

-  **PDF Ingestion** — Upload and process PDF documents into a vector store
-  **Semantic Search** — Query your documents using natural language
-  **AI-Powered Answers** — Get accurate, context-grounded responses via RAG
-  **Automatic Retries** — Failed steps are retried without re-running the entire pipeline
-  **Rate Limiting** — Prevent API overuse with built-in throttling
-  **Concurrency Control** — Safely handle multiple simultaneous ingestion jobs
-  **Observability** — Full step-by-step visibility via the Inngest Dev Server dashboard

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10+
- An [OpenAI API key](https://platform.openai.com/api-keys)
- A [Qdrant Cloud](https://cloud.qdrant.io/) account (or local Qdrant instance)
- An [Inngest](https://www.inngest.com/) account (free tier available)

### 1. Clone the Repository

```bash
git clone https://github.com/amreshMavi/ProductionGradeRAGApp.git
cd ProductionGradeRAGApp
```

### 2. Create a Virtual Environment

```bash
python -m venv venv
source venv/bin/activate        # macOS/Linux
venv\Scripts\activate           # Windows
```

### 3. Install Dependencies

```bash
uv add fastapi inngest llama-index-core llama-index-readers-file python-dotenv qdrant-client uvicorn streamlit openai
```

### 4. Configure Environment Variables

Create a `.env` file in the root directory:

```env
OPENAI_API_KEY=your_openai_api_key
```

### 5. Start the Inngest Dev Server

```bash
npx inngest-cli@latest dev
```

This opens the Inngest dashboard at `http://localhost:8288`, where you can monitor all background functions, retries, and logs.

### 6. Run the Application

```bash
uvicorn main:app --reload
```

---

## 📂 Project Structure

```
ProductionGradeRAGApp/
├── main.py                  # App entry point & API routes
├── inngest_client.py        # Inngest client configuration
├── functions/
│   ├── ingest.py            # PDF ingestion Inngest function
│   └── query.py             # Query handler Inngest function
├── rag/
│   ├── loader.py            # PDF loading & chunking logic
│   ├── indexer.py           # Qdrant vector store setup
│   └── query_engine.py      # LlamaIndex query engine
├── frontend/
│   └── index.html           # Chat interface
├── requirements.txt
└── .env.example
```

---

## 🔄 How It Works

### Document Ingestion Pipeline

1. A PDF is uploaded via the frontend
2. An Inngest event is triggered (`pdf/ingest`)
3. Inngest runs the ingestion function with retry support:
   - The PDF is loaded and split into chunks
   - Each chunk is embedded using OpenAI's embedding model
   - Embeddings are stored in Qdrant with metadata
4. The document is now queryable

### Query Pipeline

1. User submits a question via the chat interface
2. An Inngest event is triggered (`rag/query`), respecting rate limits
3. LlamaIndex retrieves the top-k most relevant chunks from Qdrant
4. The retrieved context + user question is sent to the LLM
5. The AI's answer is streamed back to the user

---

## ⚙️ Production Features Deep Dive

### Rate Limiting
Inngest throttles how many ingestion or query jobs run per minute, protecting you from hitting OpenAI API limits during high traffic.

### Retries
If any step (embedding, DB write, LLM call) fails due to a transient error, Inngest automatically retries only that step — not the entire pipeline.

### Concurrency Control
Set a max concurrency limit so that simultaneous PDF uploads don't overwhelm your vector DB or exhaust API quota.

### Observability
Every function invocation is visible in the Inngest dashboard — inputs, outputs, duration, retries, and errors — without any additional logging code.

---

## 📚 Resources

- 📖 [Inngest Python Docs](https://www.inngest.com/docs/apps)
- 🗄️ [Qdrant Documentation](https://qdrant.tech/documentation/)
- 🦙 [LlamaIndex Documentation](https://docs.llamaindex.ai/)


---

## 📄 License

This project is open-source. See [LICENSE](LICENSE) for details.
