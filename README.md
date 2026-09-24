# Simple RAG Project using LangChain, FAISS, Hugging Face & Groq

## 📌 Project Overview

This project is a simple **Retrieval-Augmented Generation (RAG)** application built with Python and LangChain.

The application reads information from a PDF document, splits the document into smaller chunks, converts the chunks into vector embeddings, stores them in a FAISS vector database, retrieves relevant information based on a user's question, and sends the retrieved context to a Groq-powered Large Language Model.

The LLM is instructed to answer **only from the retrieved document context**.

If the required information is not available in the document, the application responds:

```text
I don't know....
```

---

## 🔄 RAG Workflow

```text
PDF Document
     ↓
PyPDFLoader
     ↓
Document Loading
     ↓
Text Splitting
     ↓
RecursiveCharacterTextSplitter
     ↓
Text Chunks
     ↓
Hugging Face Embeddings
     ↓
FAISS Vector Store
     ↓
Retriever
     ↓
Relevant Context
     ↓
Groq LLM
     ↓
Final Answer
```

---

## 🛠️ Technologies Used

* Python
* LangChain
* LangChain Community
* LangChain Groq
* LangChain Text Splitters
* PyPDF
* Hugging Face Sentence Transformers
* FAISS
* Groq LLM
* python-dotenv
* Jupyter Notebook / VS Code

---

## 📂 Project Structure

```text
genai-simple-rag-project/
│
├── Gen AI.pdf
├── rag.ipynb
├── requirements.txt
├── README.md
├── .env
├── .gitignore
└── venv/
```

> The `.env` file and `venv/` folder should not be uploaded to GitHub.

---

## 📦 Installation

### 1. Clone the repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd genai-simple-rag-project
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

### 3. Activate the virtual environment

### Windows PowerShell

```powershell
.\venv\Scripts\activate
```

### 4. Install dependencies

```bash
pip install -r requirements.txt
```

---

## 🔑 Groq API Key

This project uses the Groq API.

Create a `.env` file in the project root:

```text
GROQ_API_KEY=your_groq_api_key_here
```

The application loads the API key using:

```python
from dotenv import load_dotenv
import os

load_dotenv()

api_key = os.environ["GROQ_API_KEY"]
```

### ⚠️ Security

Never upload your real API key to GitHub.

Add this to `.gitignore`:

```text
.env
venv/
__pycache__/
.ipynb_checkpoints/
```

---

## 📄 PDF Document

The project uses:

```python
document_loader = PyPDFLoader("Gen AI.pdf")
document = document_loader.load()
```

Make sure `Gen AI.pdf` is located in the project directory.

---

## ✂️ Text Splitting

The PDF content is divided into smaller chunks using `RecursiveCharacterTextSplitter`.

```python
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50
)

chunks = splitter.split_documents(document)
```

### Configuration

| Parameter       | Value | Purpose                                |
| --------------- | ----: | -------------------------------------- |
| `chunk_size`    |   500 | Maximum size of each chunk             |
| `chunk_overlap` |    50 | Shared text between neighboring chunks |

Chunking helps the retrieval system work with smaller and more relevant pieces of the document.

---

## 🧠 Embeddings

The project uses:

```text
sentence-transformers/all-MiniLM-L6-v2
```

through LangChain's `HuggingFaceEmbeddings`.

```python
embeddings = HuggingFaceEmbeddings(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)
```

The embedding model converts text into numerical vectors.

For example:

```text
"Generative AI creates new content"
             ↓
        Embedding Model
             ↓
     [0.12, -0.31, 0.45, ...]
```

These vectors allow the application to perform semantic similarity search.

---

## 🗄️ FAISS Vector Store

The generated embeddings are stored in FAISS:

```python
vector = FAISS.from_documents(
    documents=chunks,
    embedding=embeddings
)
```

FAISS is used for efficient similarity search over the document embeddings.

---

## 🔎 Retrieval

The application retrieves relevant document chunks for each question.

The intended configuration is:

```python
retriever = vector.as_retriever(
    search_kwargs={"k": 2}
)
```

Here:

```text
k = 2
```

means the application retrieves the **2 most relevant chunks** for each question.

---

## 🤖 Groq LLM

The project uses Groq through LangChain:

```python
llm = ChatGroq(
    api_key=os.environ["GROQ_API_KEY"],
    model="openai/gpt-oss-120b",
    temperature=0
)
```

### Configuration

| Parameter   | Value                 | Purpose                      |
| ----------- | --------------------- | ---------------------------- |
| Model       | `openai/gpt-oss-120b` | LLM used for answering       |
| Temperature | `0`                   | More deterministic responses |

---

## 📝 Prompt

The LLM receives the retrieved document context along with the user's question.

The prompt instructs the model to:

1. Answer only using the provided context.
2. Avoid using general knowledge.
3. Avoid assumptions.
4. Return `"I don't know...."` when the answer isn't explicitly available.

This helps reduce answers that are unrelated to the source document.

---

## 🧪 Example Questions

The project tests questions such as:

```text
what is gen ai?
what is the full form of gen ai?
what is rag?
who is the PM of India?
what is playright?
what is the capital of India?
```

Questions whose answers are present in the PDF can be answered using the retrieved context.

Questions outside the document should produce:

```text
I don't know....
```

---

## ▶️ How to Run

Open the notebook:

```text
rag.ipynb
```

Select the project's Python kernel and run the cells.

If using VS Code, select:

```text
Python (GenAI RAG venv)
```

as the notebook kernel.

---

## 💡 Key RAG Concepts Demonstrated

This project demonstrates the following GenAI concepts:

* RAG
* Document Loading
* PDF Processing
* Text Chunking
* Chunk Overlap
* Embeddings
* Semantic Search
* Vector Database
* FAISS
* Retrieval
* Context Injection
* LLM Generation
* Prompt Engineering
* Grounded Question Answering
* Hallucination Reduction
* Environment Variables
* API Key Management

---

## 🎯 Project Objective

The main objective of this project is to understand how a basic RAG pipeline connects a private document with an LLM.

Instead of directly asking the LLM:

```text
Question → LLM → Answer
```

the application follows:

```text
Question
   ↓
Retriever
   ↓
Relevant Document Chunks
   ↓
Context
   ↓
LLM
   ↓
Answer
```

This allows the LLM to generate answers based on information retrieved from the provided document.

---

## 🚀 Future Improvements

Possible improvements include:

* Add a web/API interface using FastAPI
* Add a Streamlit UI
* Support multiple PDF documents
* Add metadata filtering
* Implement MMR retrieval
* Add reranking
* Add conversation history
* Add source/page citations
* Add evaluation metrics such as Precision@K, Recall@K and MRR
* Add document upload functionality
* Store FAISS index locally
* Add authentication and API security
* Add input/output guardrails
* Deploy the application

---

## 📚 Learning Outcome

After completing this project, you should understand the basic flow of a RAG application:

```text
Load → Split → Embed → Store → Retrieve → Generate
```

This project provides a foundation for building more advanced GenAI applications using LangChain, vector databases, retrieval techniques, and LLMs.

---

