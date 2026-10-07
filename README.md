# 📄 PDF RAG Chatbot

Ask questions about your own PDF documents and get answers grounded strictly in their content. This project uses a **hybrid retrieval pipeline** (BM25 + TF-IDF), **LLM-based query rewriting and reranking**, and **Groq's Llama 3.3 70B** for answer generation.

> Built to demonstrate how Retrieval-Augmented Generation (RAG) reduces hallucinations by making the model answer from retrieved documents instead of its own memory.

---

## ✨ Features

- 📚 Reads multiple PDFs from a folder and splits them into chunks with source and page metadata
- 🔎 **Hybrid search**: combines keyword search (BM25) with TF-IDF cosine similarity
- ✍️ **Query rewriting**: converts a natural question into short search keywords
- 🔀 **LLM reranking**: reorders retrieved chunks by relevance before answering
- 🎓 **Grounded answers** in a clear format: Definition → Explanation → Examples
- 🚫 Says *"I don't know, this is not in the document"* when the answer isn't in the PDFs
- 📌 Cites the source file and page number used for each answer's context

---

## ⚙️ How It Works

```
PDFs ──► Chunking (500 words) ──► BM25 index + TF-IDF vectors
                                          │
User question ──► Query rewriting ──► Hybrid search (0.7 BM25 + 0.3 TF-IDF)
                                          │
                                   Top 8 chunks ──► LLM reranking
                                                        │
                                          Top 3 chunks ──► Answer generation (Llama 3.3 70B)
```

1. **Chunking**: each PDF page is split into chunks of ~500 words.
2. **Indexing**: chunks are indexed with BM25 (keyword search) and TF-IDF vectors (similarity search).
3. **Query rewriting**: the LLM turns the user's question into 3–5 keywords for better retrieval.
4. **Hybrid search**: BM25 and TF-IDF scores are combined (70% / 30%) to fetch the top 8 candidate chunks.
5. **Reranking**: the LLM reorders the candidates by relevance.
6. **Answer generation**: the LLM answers using only the top 3 reranked chunks.

---

## 🛠️ Tech Stack

| Purpose | Tool |
| --- | --- |
| Language | Python |
| PDF text extraction | [pypdf](https://pypi.org/project/pypdf/) |
| Keyword retrieval | [rank-bm25](https://pypi.org/project/rank-bm25/) |
| TF-IDF & cosine similarity | [scikit-learn](https://scikit-learn.org/) |
| LLM inference | [Groq](https://groq.com/) (Llama 3.3 70B Versatile) |
| Config / secrets | [python-dotenv](https://pypi.org/project/python-dotenv/) |
| Interface | Jupyter Notebook |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.9 or higher
- A free [Groq API key](https://console.groq.com/keys)

### Installation

1. **Clone the repository**

```bash
   git clone https://github.com/muzzammil03/RAG-chatbot.git
   cd RAG-chatbot
```

2. **Install dependencies**

```bash
   pip install -r requirements.txt
   pip install notebook
```

3. **Add your PDFs**

   Put your PDF files inside the `pdfs/` folder.

4. **Set up your API key**

```bash
   cp .env.example .env
```

   Then open `.env` and add your key:

```env
   GROQ_API_KEY=your_groq_api_key_here
```

5. **Run the notebook**

```bash
   jupyter notebook rag_chatbot.ipynb
```

   Run the cells in order. After the index is built, type your question in the chat loop and type `exit` to quit.

---

## 💬 Example

```
>> What is overfitting?

Answer:
**Definition:** (taken from your document)

**Explanation:** (in simple words)

**Examples:**
- Example 1: ...
- Example 2: ...
- Example 3: ...
```

---

## 📁 Project Structure

```
RAG-chatbot/
├── rag_chatbot.ipynb    # Main notebook (indexing + chat loop)
├── pdfs/                # Put your PDF files here (not committed)
├── requirements.txt     # Python dependencies
├── .env.example         # Template for environment variables
├── .gitignore
└── README.md
```

---

## 🔧 Configuration

You can tweak these values in the config cell of the notebook:

| Setting | Default | Description |
| --- | --- | --- |
| `CHUNK_SIZE` | `500` | Words per chunk |
| `TOP_K` | `8` | Chunks retrieved before reranking |
| Hybrid weights | `0.7 / 0.3` | BM25 vs TF-IDF contribution |
| Model | `llama-3.3-70b-versatile` | Groq model used for all LLM calls |

---

## ⚠️ Limitations

- The similarity part of the hybrid search uses **TF-IDF, not neural embeddings**, so it matches words rather than true meaning.
- Works only with **text-based PDFs**. Scanned PDFs need OCR first.
- The index is rebuilt every time the notebook runs and is not saved to disk.

## 🔮 Future Improvements

- Replace TF-IDF with `sentence-transformers` embeddings and a vector store (e.g. FAISS)
- Add a Streamlit / Gradio web interface
- Save and reload the index to avoid re-indexing
- Add conversation memory for follow-up questions
- OCR support for scanned documents

---

## 🔐 Security Note

Never commit your `.env` file or hardcode API keys in the notebook. The included `.gitignore` already blocks `.env`.

---

## 📬 Contact

**Muzzammil Ahmed**

- 💼 [LinkedIn](https://www.linkedin.com/in/muzzammilahmed03/)
- 🐙 [GitHub](https://github.com/muzzammil03)
- 📧 muzzammil.cse3@gmail.com

⭐ If you found this useful, consider giving the repo a star!
