# Applied AI Health RAG

A RAG (Retrieval-Augmented Generation) pipeline for processing health education content from MedlinePlus and preparing it for AI applications.

## 📋 Overview

This project implements an end-to-end ETL pipeline to:

1. Download health education content from MedlinePlus
2. Convert HTML documents to Markdown format
3. Split documents into chunks for RAG processing
4. Ingest chunks into ChromaDB vector store with OpenAI embeddings

## 🏗️ Project Structure

```text
applied-ai-health-rag/
├── data/
│   ├── raw/                    # Raw HTML files downloaded from sources
│   │   └── health_education/
│   ├── processed/               # Converted Markdown files
│   │   └── health_education/
│   └── chunks/                  # Document chunks for RAG
│       ├── all_chunks.json      # All chunks in a single file
│       └── chunks_by_file.json  # Chunks grouped by source file
├── notebooks/
│   ├── 1.download_raw_data.ipynb           # Download HTML from MedlinePlus
│   ├── 2.convert_html_to_markdown.ipynb    # Convert HTML to Markdown
│   ├── 3.chunk_markdown.ipynb              # Split Markdown into chunks
│   └── 4.ingest_chunks_to_chromadb.ipynb   # Ingest chunks to ChromaDB
├── chroma_db/                   # ChromaDB vector store (created by notebook 4)
├── src/                         # Source code (for future use)
├── etl/                         # ETL scripts (for future use)
├── pyproject.toml               # Project dependencies
└── README.md                    # This file
```

## 🚀 Getting Started

### Prerequisites

- Python 3.12+
- [uv](https://github.com/astral-sh/uv) (recommended) or pip

### Installation

1. Clone the repository:

```bash
git clone <repository-url>
cd applied-ai-health-rag
```

1. Install dependencies using uv:

```bash
uv sync
```

Or using pip:

```bash
pip install -e .
```

### Setup

1. Activate the virtual environment:

```bash
# With uv
source .venv/bin/activate  # Linux/Mac
# or
.venv\Scripts\activate  # Windows

# With pip
source venv/bin/activate  # Linux/Mac
```

1. Set up OpenAI API key (required for notebook 4):

```bash
export OPENAI_API_KEY="your-api-key-here"
```

Or create a `.env` file in the project root:

```bash
OPENAI_API_KEY=your-api-key-here
```

## 📓 Usage

The pipeline consists of four sequential notebooks. Execute them in order:

### 1. Download Raw Data

**Notebook:** `notebooks/1.download_raw_data.ipynb`

Downloads health education content from MedlinePlus and saves HTML files to `data/raw/health_education/`.

**Output:** HTML files organized by type (conditions/treatments) and condition (diabetes/hypertension).

### 2. Convert HTML to Markdown

**Notebook:** `notebooks/2.convert_html_to_markdown.ipynb`

Converts all HTML files to Markdown format using [docling](https://github.com/IBM/docling).

**Output:** Markdown files in `data/processed/health_education/` maintaining the same directory structure.

### 3. Chunk Markdown Documents

**Notebook:** `notebooks/3.chunk_markdown.ipynb`

Splits Markdown documents into smaller chunks suitable for RAG processing using [langchain-text-splitters](https://github.com/langchain-ai/langchain/tree/master/libs/text-splitters).

**Configuration:**

- Chunk size: 1000 characters
- Chunk overlap: 200 characters
- Separators: Headers, paragraphs, newlines

**Output:**

- `data/chunks/all_chunks.json`: All chunks in a single array
- `data/chunks/chunks_by_file.json`: Chunks grouped by source file

**Chunk Structure:**

```json
{
  "chunk_id": "diabetes_medlineplus_overview_chunk_0000",
  "chunk_index": 0,
  "text": "Chunk content...",
  "metadata": {
    "source_file": "path/to/file.md",
    "filename": "diabetes_medlineplus_overview.md",
    "file_stem": "diabetes_medlineplus_overview",
    "type": "conditions",
    "condition": "diabetes"
  }
}
```

### 4. Ingest Chunks to ChromaDB

**Notebook:** `notebooks/4.ingest_chunks_to_chromadb.ipynb`

Loads the chunked documents and ingests them into a ChromaDB vector store with OpenAI embeddings for RAG applications.

**Requirements:**

- `OPENAI_API_KEY` environment variable must be set
- OpenAI API access (for embeddings)

**Configuration:**

- Embedding model: `text-embedding-3-small` (OpenAI)
- Vector store: ChromaDB (persisted locally)
- Collection name: `health_education_chunks`

**Output:**

- `chroma_db/`: Persistent ChromaDB vector store directory
- Vector store ready for similarity search and RAG queries

**Features:**

- Converts JSON chunks to LangChain Document objects
- Generates embeddings using OpenAI
- Persists vector store to disk
- Includes verification and sample similarity search

## 🔧 Dependencies

- **docling**: HTML to Markdown conversion
- **langchain-text-splitters**: Document chunking for RAG
- **langchain-chroma**: ChromaDB integration for LangChain
- **langchain-openai**: OpenAI embeddings and models integration
- **langchain-core**: Core LangChain functionality
- **chromadb**: Vector database for embeddings
- **requests**: HTTP requests for downloading data
- **ipykernel**: Jupyter notebook support

See `pyproject.toml` for complete dependency list.

## 📊 Data Sources

Currently configured to download from:

- [MedlinePlus - Diabetes](https://medlineplus.gov/diabetes.html)
- [MedlinePlus - Hypertension](https://medlineplus.gov/highbloodpressure.html)
- [MedlinePlus - Diabetes Medicines](https://medlineplus.gov/diabetesmedicines.html)
- [MedlinePlus - Blood Pressure Medicines](https://medlineplus.gov/bloodpressuremedicines.html)

## 🔄 Pipeline Flow

```text
Raw HTML (MedlinePlus)
    ↓
[Notebook 1] Download
    ↓
data/raw/*.html
    ↓
[Notebook 2] Convert with docling
    ↓
data/processed/*.md
    ↓
[Notebook 3] Chunk with langchain
    ↓
data/chunks/*.json
    ↓
[Notebook 4] Ingest to ChromaDB with OpenAI embeddings
    ↓
chroma_db/ (Vector Store)
    ↓
Ready for RAG Applications
```

## 🎯 Next Steps

- [x] Add vector database integration (ChromaDB)
- [x] Implement embedding generation (OpenAI)
- [ ] Create RAG query interface
- [ ] Add evaluation metrics
- [ ] Expand data sources
