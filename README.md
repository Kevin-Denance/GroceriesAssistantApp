🛒 Smart Grocery Sentinel
Full-Stack AI Pipeline for Real-Time Grocery Deal Discovery
Smart Grocery Sentinel is an end-to-end data engineering solution designed to fight food waste and grocery inflation in Toronto. It automates the extraction of "near-expiry" inventory from the Flashfood API, enriches the data using Local Edge AI (Ollama), and provides a natural language interface for users via Discord and Google Gemini.

🏗️ System Architecture
Code snippet
graph TD
    subgraph "Phase 1: Ingestion (Cloud/GitHub)"
    A[GitHub Actions] -->|Daily Cron| B(Python Scraper)
    B -->|API Reverse Engineering| C[Flashfood API]
    B -->|Insert Raw Data| D[(Supabase Master Table)]
    end

    subgraph "Phase 2: Batch Enrichment (Local Edge AI)"
    D -->|Query NULL Categories| E[Python ETL Script]
    E -->|Batch Inference| F[Ollama / Llama 3.2:3b]
    F -->|Classified Results| E
    E -->|Bulk Update| D
    end

    subgraph "Phase 3: Consumer Interface (n8n)"
    G[Discord User] <--> H[n8n Bot / Docker]
    H <--> I[Gemini AI]
    I <-->|NL-to-SQL| D
    end
🚀 Key Technical Features
1. Automated Data Ingestion & API Sniffing
Reverse Engineering: Used Postman to intercept and analyze Flashfood mobile app network traffic, identifying the internal API endpoints and authentication headers required for scraping.

Serverless Execution: The Python scraper is hosted on GitHub and triggered daily via GitHub Actions, ensuring a zero-cost, persistent data pipeline.

Multi-Store Coverage: Aggregates live data across multiple Toronto-based store locations simultaneously.

2. Local AI Batch Enrichment (The "Transformer" Layer)
Instead of relying on expensive cloud APIs for data cleaning, this project implements a Local-First ETL approach:

Windowed Processing: A dedicated Python script identifies records in the Supabase "Master Table" that lack categorization.

Ollama Integration: The script fetches these "Pending" rows in batches and uses Llama 3.2 (3b) running locally via Ollama to classify items (e.g., Produce, Meat, Dairy).

Bulk Sync: Once classified, the script pushes the enriched data back to Supabase in a single batch update, optimizing database performance and minimizing network overhead.

3. AI-Powered Query Interface
n8n Orchestration: A self-hosted n8n instance (running in Docker) serves as the central brain, connecting the Discord Webhook to the database.

NL-to-SQL (Gemini): Integrated Google Gemini to act as a SQL interpreter. Users can ask questions in plain English (e.g., "Find me any meat deals under $5 in Downtown"), and the AI generates the precise PostgreSQL query to fetch results from Supabase Views.
