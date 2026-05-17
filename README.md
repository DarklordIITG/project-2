# VectorDB

A vector database built from scratch in C++ with a browser UI. Implements three search algorithms side-by-side and includes a local RAG pipeline via Ollama.

Built to understand how production vector databases like Pinecone and Chroma actually work under the hood.

---

## Features

- **3 search algorithms** — HNSW, KD-Tree, Brute Force — run and benchmark all three simultaneously
- **3 distance metrics** — Cosine, Euclidean, Manhattan
- **2D PCA scatter plot** — live visualization of semantic clusters forming in vector space
- **Real embeddings** — paste any text, Ollama embeds it with `nomic-embed-text` (768D); long documents are auto-chunked
- **RAG pipeline** — ask questions about your documents; HNSW retrieves relevant chunks, `llama3.2` generates the answer
- **REST API** — full CRUD: insert, delete, search, benchmark, HNSW graph inspection

---

## Stack

| Layer | Tech |
|---|---|
| Backend | C++17, single-binary HTTP server (`cpp-httplib`) |
| Algorithms | HNSW (`O(log N)`), KD-Tree (`O(log N)`), Brute Force (`O(N·d)`) |
| Embeddings | Ollama → `nomic-embed-text` |
| LLM | Ollama → `llama3.2` |
| Frontend | Vanilla HTML/JS, PCA scatter plot |

---

## Setup

**Requirements:** MSYS2 (g++), Git, Ollama

```bash
# Pull models
ollama pull nomic-embed-text
ollama pull llama3.2

# Clone and compile
git clone https://github.com/DarklordIITG/VectorDB.git
cd VectorDB
g++ -std=c++17 -O2 main.cpp -o db -lws2_32

# Run
./db
# → http://localhost:8080
```

> On a slow machine, switch to `llama3.2:1b` for faster generation — change `genModel` in `main.cpp` and recompile.

---

## How HNSW Works

Vectors are indexed in a multilayer graph — upper layers act as a highway to the right neighborhood, layer 0 does the fine-grained search. This gives `O(log N)` approximate nearest-neighbor lookup, which is why it's used in production by Pinecone, Weaviate, and Milvus.

KD-Tree works well here (≤16D demo vectors) but degrades badly at 768D due to the curse of dimensionality — HNSW doesn't have this problem.

---

## Project Structure

```
VectorDB/
├── main.cpp     ← all backend logic: algorithms, HTTP server, RAG, Ollama client
├── httplib.h    ← cpp-httplib (single header)
└── index.html   ← frontend
```

---
