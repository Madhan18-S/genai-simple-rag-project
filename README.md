# GenAI Simple RAG Project

A simple **Retrieval-Augmented Generation (RAG)** project built with Python, LangChain, FAISS, Hugging Face embeddings, and Groq LLM.

The application loads a PDF document, splits the content into smaller chunks, converts the chunks into embeddings, stores them in a FAISS vector store, retrieves the most relevant chunks for a question, and generates an answer using a Groq-hosted LLM.

## 🚀 Project Overview

This project demonstrates the basic RAG pipeline:

```text
PDF Document
     ↓
PyPDFLoader
     ↓
Document Splitting
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

The LLM is instructed to answer **only from the retrieved document context**. If the required information is not available in the context, it returns:

```text
I don't know....
```

## 🛠️ Technologies Used

* Python
* LangChain
* LangChain Community
* LangChain Groq
* LangChain Hugging Face
* LangChain Text Splitters
* PyPDF
* FAISS
* Hugging Face Sentence Transformers
* Groq LLM
* python-dotenv
* Jupyter Notebook

## ✨ Features

* Load PDF documents using `PyPDFLoader`
* Split documents using `RecursiveCharacterTextSplitter`
* Generate embeddings using Hugging Face
* Store embeddings using FAISS
* Retrieve the top 2 relevant document chunks
* Generate answers using Groq LLM
* Restrict answers to retrieved document context
* Return `"I don't know...."` when information is unavailable
* Test the RAG system with multiple questions

## 📂 Project Structure

```text
genai-simple-rag-project/
│
├── document/
│   └── Gen AI.pdf
│
├── genai_rag.ipynb
├── .env
├── .gitignore
├── requirements.txt
└── README.md
```

> **Note:** The `.env` file should not be uploaded to GitHub. It contains the Groq API key and must be added to `.gitignore`.

## ⚙️ Installation

### 1. Clone the Repository

```bash
git clone https://github.com/Madhan18-S/genai-simple-rag-project.git
```

### 2. Navigate to the Project

```bash
cd genai-simple-rag-project
```

### 3. Create a Virtual Environment

```bash
python -m venv venv
```

### 4. Activate the Virtual Environment

Windows PowerShell:

```powershell
venv\Scripts\Activate.ps1
```

### 5. Install Dependencies

```bash
pip install langchain langchain-community langchain-groq langchain-text-splitters
```

```bash
python -m pip install -U langchain-huggingface
```

```bash
pip install faiss-cpu
```

You can also install the required packages using:

```bash
pip install -r requirements.txt
```

## 🔑 API Key Configuration

The project uses a Groq API key.

Create a `.env` file in the project root:

```text
GROQ_API_KEY=your_groq_api_key
```

The application loads the API key using:

```python
from dotenv import load_dotenv
import os

load_dotenv()

api_key = os.environ["GROQ_API_KEY"]
```

### ⚠️ Security

**Never upload your `.env` file or Groq API key to GitHub.**

Your `.gitignore` should contain:

```text
.env
venv/
.venv/
__pycache__/
*.pyc
```

## 📄 PDF Document

The PDF used in this project is located at:

```text
document/Gen AI.pdf
```

It is loaded using:

```python
document_loader = PyPDFLoader("document/Gen AI.pdf")

document = document_loader.load()
```

## ✂️ Document Chunking

The loaded document is divided into smaller chunks using:

```python
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50
)

chunks = splitter.split_documents(document)
```

### Configuration

| Parameter       | Value | Purpose                            |
| --------------- | ----: | ---------------------------------- |
| `chunk_size`    |   500 | Maximum size of each text chunk    |
| `chunk_overlap` |    50 | Overlap between consecutive chunks |

The overlap helps preserve context between neighboring chunks.

## 🧠 Embeddings

The project uses the Hugging Face model:

```text
sentence-transformers/all-MiniLM-L6-v2
```

Code:

```python
embeddings = HuggingFaceEmbeddings(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)
```

The embedding model converts text into numerical vectors that can be compared for semantic similarity.

## 🗂️ FAISS Vector Store

The document chunks and their embeddings are stored using FAISS:

```python
vector = FAISS.from_documents(
    documents=chunks,
    embedding=embeddings
)
```

FAISS is used for efficient similarity search over the document embeddings.

## 🔎 Retriever

The FAISS vector store is converted into a retriever:

```python
retriever = vector.as_retriever(
    search_kwargs={"k": 2}
)
```

The value `k=2` means the system retrieves the **2 most relevant chunks** for each question.

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

| Parameter   | Value                 |
| ----------- | --------------------- |
| Model       | `openai/gpt-oss-120b` |
| Temperature | `0`                   |

A temperature of `0` is used to make responses more consistent and focused.

## 🔄 RAG Process

For every question, the application follows these steps:

### Step 1 — User Question

Example:

```text
What is Gen AI?
```

### Step 2 — Retrieval

The question is passed to the retriever:

```python
response = retriever.invoke(q)
```

The retriever searches the FAISS vector store and returns the most relevant chunks.

### Step 3 — Context Creation

The retrieved chunks are combined:

```python
context = "\n".join(
    doc.page_content for doc in response
)
```

### Step 4 — Prompt Construction

The retrieved context and question are provided to the LLM.

The prompt instructs the model to:

* Use only the provided context
* Not use general knowledge
* Not make assumptions
* Return `"I don't know...."` if the answer is unavailable

### Step 5 — Answer Generation

The prompt is sent to the Groq LLM:

```python
result = llm.invoke(prompt)
```

The generated answer is then displayed.

## 🧪 Test Questions

The project tests the RAG system with questions such as:

```text
What is Gen AI?
What is the full form of Gen AI?
What is RAG?
Who is the PM of India?
What is Playwright?
What is the capital of India?
```

Questions that are not answered by the PDF should result in:

```text
I don't know....
```

This demonstrates the importance of grounding LLM responses in retrieved context.

## 🎯 Learning Objectives

This project helps understand the fundamentals of:

* Generative AI
* Large Language Models
* Retrieval-Augmented Generation
* PDF document loading
* Text splitting
* Embeddings
* Semantic similarity
* Vector databases/vector stores
* FAISS
* Retrievers
* Prompt engineering
* Context grounding
* Hallucination reduction
* LangChain
* Groq LLM integration

## 🔮 Future Improvements

The project can be extended with:

* Multiple PDF document support
* Persistent FAISS indexes
* Metadata filtering
* MMR retrieval
* Reranking
* Conversational RAG
* Chat history
* FastAPI backend
* Streamlit interface
* RAG evaluation
* Retrieval metrics such as Precision@K, Recall@K, Hit Rate@K, and MRR
* Better document chunking strategies

## 👨‍💻 Project Summary

This project demonstrates how a basic RAG application can combine **document retrieval with Generative AI**.

Instead of directly asking an LLM to answer a question, the system first retrieves relevant information from the PDF and then provides that information as context to the LLM.

This approach helps create responses that are more closely grounded in the source document.

---

**Technologies:** Python · LangChain · FAISS · Hugging Face · Groq · RAG
