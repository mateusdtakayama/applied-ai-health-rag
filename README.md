# Applied AI Health RAG

A RAG (Retrieval-Augmented Generation) pipeline for processing health education content from MedlinePlus and preparing it for AI applications.

## 📋 Overview

This project implements an end-to-end ETL pipeline to:

1. Download health education content from MedlinePlus
2. Convert HTML documents to Markdown format
3. Split documents into chunks for RAG processing
4. Prepare structured data for vector database ingestion

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
│   └── 3.chunk_markdown.ipynb              # Split Markdown into chunks
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

Activate the virtual environment:

```bash
# With uv
source .venv/bin/activate  # Linux/Mac
# or
.venv\Scripts\activate  # Windows

# With pip
source venv/bin/activate  # Linux/Mac
```

## 📓 Usage

The pipeline consists of three sequential notebooks. Execute them in order:

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

## 🔧 Dependencies

- **docling**: HTML to Markdown conversion
- **langchain-text-splitters**: Document chunking for RAG
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
Ready for Vector DB / RAG
```

## 🎯 Next Steps

- [ ] Add vector database integration (e.g., Chroma, Pinecone, Weaviate)
- [ ] Implement embedding generation
- [ ] Create RAG query interface
- [ ] Add evaluation metrics
- [ ] Expand data sources
