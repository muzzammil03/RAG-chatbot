# PDF RAG Chatbot

Ask questions about your own PDF documents and get answers grounded strictly in their content, powered by a hybrid retrieval pipeline and Groq's Llama 3.3 70B.

## How it works

1. **Chunking** — each PDF page is split into ~500-word chunks.
2. **Indexing** — chunks are indexed two ways: BM25 (keyword search) and TF-IDF vectors (semantic-ish similarity).
3. **Query rewriting** — the user's question is turned into a short keyword query for better retrieval.
4. **Hybrid search** — BM25 and TF-IDF scores are combined (70/30) to retrieve the top candidate chunks.
5. **Reranking** — the LLM reorders the retrieved chunks by relevance.
6. **Answer generation** — the LLM answers using only the top reranked chunks, with a definition + simple-example format.

## Setup

```bash
git clone <your-repo-url>
cd pdf-rag-chatbot
pip install -r requirements.txt
```

1. Add your PDFs to the `pdfs/` folder.
2. Copy `.env.example` to `.env` and add your [Groq API key](https://console.groq.com/keys):
   ```bash
   cp .env.example .env
   ```
3. Open `rag_chatbot.ipynb` in Jupyter and run the cells in order.

## Project structure

```
pdf-rag-chatbot/
├── rag_chatbot.ipynb   # main notebook
├── pdfs/                # put your PDF files here (not committed)
├── requirements.txt
├── .env.example
└── .gitignore
```

## Tech stack

- [pypdf](https://pypi.org/project/pypdf/) — PDF text extraction
- [rank-bm25](https://pypi.org/project/rank-bm25/) — keyword-based retrieval
- [scikit-learn](https://scikit-learn.org/) — TF-IDF vectorization + cosine similarity
- [Groq](https://groq.com/) — fast LLM inference (Llama 3.3 70B) for query rewriting, reranking, and answer generation

## Notes

- This uses TF-IDF (not real embeddings) for the similarity leg of hybrid search, which keeps the project dependency-free but is weaker than a proper embedding model. Swapping in something like `sentence-transformers` is a natural next step.
- Never commit a `.env` file or hardcode API keys in the notebook — `.gitignore` is already set up to block this.
