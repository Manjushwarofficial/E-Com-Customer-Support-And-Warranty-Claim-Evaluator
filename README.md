# E-Com-Customer-Support-And-Warranty-Claim-Evaluator

An enterprise-grade, privacy-focused RAG (Retrieval-Augmented Generation) application built with **LLaMA 3.2**, **PostgreSQL (`pgvector`)**, **FastAPI**, and **Azure**.

This project demonstrates **Hybrid Search** by combining structured transactional database queries (SQL) with unstructured semantic document retrieval (Vector Search) to provide grounded, hallucination-free customer support for order returns and warranty claims.


## Architecture Overview

```text
               ┌───────────────────────────────┐
               │         Streamlit UI          │
               └───────────────┬───────────────┘
                               │
                               ▼
               ┌───────────────────────────────┐
               │    FastAPI / LangChain Backend│
               │      (Azure App Service)      │
               └───────┬───────────────┬───────┘
                       │               │
      1. SQL + Hybrid Search           2. LLaMA Prompt + Context
                       │               │
                       ▼               ▼
┌──────────────────────────────┐ ┌──────────────────────────────┐
│ Azure PostgreSQL (Flexible)  │ │ Azure Container Apps         │
│  - Orders Table (SQL)        │ │  - Ollama Server             │
│  - Policies Table (pgvector) │ │  - LLaMA 3.2 Model           │
└──────────────────────────────┘ └──────────────────────────────┘

```


## Features

* **Hybrid Retrieval:** Executes precise SQL queries on order histories (dates, prices, item status) while simultaneously performing vector searches on PDF warranty policies.
* **Open-Source LLM Hosting:** Runs `llama3.2` locally or inside Azure Container Apps using Ollama to ensure complete data privacy and zero per-token cost.
* **Zero Hallucination Focus:** Restricts model responses to grounded database context and explicit policy terms.
* **Interactive UI:** A Streamlit dashboard allowing users to test customer support prompts, select mock users, and inspect raw SQL + Vector retrieval context in real time.



## Tech Stack

* **LLM Engine:** LLaMA 3.2 via [Ollama](https://ollama.com/)
* **Database:** Azure Database for PostgreSQL (Flexible Server) with the `pgvector` extension
* **Orchestration:** LangChain / LlamaIndex
* **Backend API:** FastAPI / Python 3.11+
* **Frontend:** Streamlit
* **Cloud Infrastructure:** Azure App Service, Azure Container Apps (ACA)



## Quickstart Guide

### Prerequisites

* Python 3.11+
* PostgreSQL database with `pgvector` installed
* Ollama running locally or on a remote server

### 1. Clone & Set Up Environment

```bash
git clone https://github.com/your-username/ecommerce-rag-assistant.git
cd ecommerce-rag-assistant

python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt

```

### 2. Configure Environment Variables

Create a `.env` file in the root directory:

```env
POSTGRES_USER=your_db_user
POSTGRES_PASSWORD=your_db_password
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=ecommerce_rag

OLLAMA_BASE_URL=http://localhost:11434
EMBEDDING_MODEL=nomic-embed-text
LLM_MODEL=llama3.2

```

### 3. Initialize Database & Seed Data

Ensure PostgreSQL is running and the `pgvector` extension is enabled:

```sql
CREATE EXTENSION IF NOT EXISTS vector;

```

Run the ingestion and seed scripts to populate mock customer orders and embed sample warranty PDFs:

```bash
python scripts/seed_db.py
python scripts/ingest_policies.py

```

### 4. Run the Application

Start the FastAPI backend:

```bash
uvicorn app.main:app --reload --port 8000

```

In a separate terminal, launch the Streamlit frontend:

```bash
streamlit run frontend/app.py

```

Open your browser and navigate to `http://localhost:8501`.


## ☁️ Azure Deployment Summary

1. **Database:** Deploy an **Azure Database for PostgreSQL Flexible Server**. Enable the `vector` extension under Server Parameters (`azure.extensions`).
2. **LLM Server:** Host Ollama with `llama3.2` on **Azure Container Apps (ACA)** using GPU Workload Profiles or standard high-CPU instances.
3. **Backend API:** Deploy the FastAPI service to **Azure App Service** and configure VNet integration to securely communicate with PostgreSQL and ACA.


## License

Distributed under the MIT License. See `LICENSE` for more information.
