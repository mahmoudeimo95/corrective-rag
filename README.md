# Corrective RAG System

A Retrieval-Augmented Generation pipeline that **checks its own retrieval** before answering. If the retrieved context is weak or irrelevant, it rewrites the query and retrieves again. Answers are generated only from verified context and always shown with sources.

## How it works

```
Upload → Extract → Clean → Split → Embed → ChromaDB
                                              │
Question → Retrieve top-k → Grade each chunk ─┤
                 ▲                            │ relevant?
                 │ no                         ▼ yes
        Rewrite query (max 2×)        Generate answer from verified chunks
                 │                            │
                 └── still nothing ──► "Not found in documents"
                                              ▼
                                   Answer + sources + pipeline trace
```

## Features

- Ingestion pipeline for PDF, DOCX and TXT files (upload, extract, clean, split)
- Multilingual embeddings (Arabic + English) stored in ChromaDB
- LLM-based relevance grading of every retrieved chunk
- Automatic query rewriting and re-retrieval when context is weak
- Answers generated from verified context only, with numbered citations
- Graceful handling of unanswerable questions
- Gradio web app with sources and a step-by-step pipeline trace

## Tech stack

Python · ChromaDB · Sentence-Transformers · Hugging Face Transformers (Qwen2.5-3B-Instruct) · Gradio

## Run on Google Colab

1. Open `Corrective_RAG.ipynb` in Colab.
2. Runtime → Change runtime type → **T4 GPU**.
3. Run all cells and open the public Gradio link.
4. Upload documents, click **Index documents**, then ask questions.

## Run locally (GPU recommended)

```bash
pip install -r requirements.txt
python app.py
```

## Configuration

Edit the config block at the top of `app.py`:

| Setting | Default | Purpose |
|---|---|---|
| `LLM_NAME` | Qwen2.5-3B-Instruct | Grader, rewriter and generator |
| `EMBED_NAME` | paraphrase-multilingual-MiniLM-L12-v2 | Embedding model |
| `CHUNK_SIZE` / `CHUNK_OVERLAP` | 800 / 150 | Chunking |
| `TOP_K` | 6 | Chunks retrieved per attempt |
| `MIN_RELEVANT` | 1 | Verified chunks needed to answer |
| `MAX_REWRITES` | 2 | Query rewrites before giving up |

## Limitations

- Scanned PDFs without a text layer need OCR (not included).
- A small local LLM grades less accurately than larger models; try Qwen2.5-7B-Instruct or an API model for better results.

## Author

Mahmoud Atef — [GitHub](https://github.com/mahmoudeimo95)
