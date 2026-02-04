# SiteLens – Requirements, Design & Architecture (RDA)

## 1. Overview

**Project Name:** SiteLens
**Purpose:** Turn any public website into a searchable, shareable knowledge layer using Retrieval-Augmented Generation (RAG).

SiteLens allows users to paste a public website URL and instantly get:

* A conversational Q&A interface
* Auto-generated FAQs
* A public shareable link
* An embeddable widget

The system is designed to be modular, scalable, and extensible for future features (analytics, auth, billing, APIs).

---

## 2. Goals & Non-Goals

### 2.1 Goals

* Instant value with minimal user input (URL only)
* High-quality answers grounded in website content
* Public-first experience (no login required for MVP)
* Scalable RAG architecture
* Clear extension points for future growth

### 2.2 Non-Goals (MVP)

* Authentication / user accounts
* Payments or subscriptions
* Private or authenticated websites
* Fine-tuned models
* Browser extensions

---

## 3. Functional Requirements

### 3.1 Website Ingestion

* Accept a public website URL
* Crawl only same-domain pages
* Configurable depth and page limits
* Respect robots.txt
* Basic rate limiting to avoid abuse

### 3.2 Content Processing

* HTML cleaning and boilerplate removal
* Text normalization
* Chunking with overlap
* Metadata attachment (URL, title, heading, timestamp)

### 3.3 Embedding & Indexing

* Generate vector embeddings for chunks
* Store embeddings in an abstracted vector store
* Support multiple vector DBs via adapter layer

### 3.4 RAG-based Q&A

* Accept user questions via chat UI
* Retrieve relevant chunks
* Generate grounded answers using LLM
* Return citations (source URLs)

### 3.5 FAQ Generation

* Auto-generate 5–10 FAQs from indexed content
* FAQs stored and reusable
* Display FAQs on public page

### 3.6 Public Access

* Generate a public, shareable link per project
* Read-only access to chat + FAQs

### 3.7 Embed Support

* Provide iframe or JS embed snippet
* Embed shows chat + FAQs

---

## 4. Non-Functional Requirements

### 4.1 Scalability

* Handle 100 → 10,000 users
* Async ingestion and embedding
* Stateless API design

### 4.2 Performance

* Chat response < 5s (P95)
* Background ingestion completion feedback

### 4.3 Cost Control

* Token usage limits per project
* Chunk and crawl limits
* Model abstraction for cheaper providers

### 4.4 Reliability

* Graceful failure for crawl/LLM errors
* Partial ingestion support

### 4.5 Security

* Only public URLs allowed
* Input sanitization
* Rate limiting for crawl and chat endpoints

---

## 5. System Architecture

### 5.1 High-Level Components

* Frontend (Next.js)
* API Gateway (FastAPI)
* Ingestion Service
* Embedding Service
* Vector Store
* LLM Provider(s)
* Background Worker + Queue

### 5.2 Architecture Style

* Service-oriented, modular monolith (MVP)
* Async background processing
* Provider abstraction layers

---

## 6. Data Model (Logical)

### 6.1 Project

* id
* root_url
* status (pending, ready, failed)
* created_at

### 6.2 Page

* id
* project_id
* url
* title
* raw_text

### 6.3 Chunk

* id
* project_id
* page_id
* content
* embedding_vector
* metadata

### 6.4 FAQ

* id
* project_id
* question
* answer

### 6.5 Chat Session

* id
* project_id
* messages

---

## 7. API Design (Key Endpoints)

### 7.1 Project

* POST /projects → create project from URL
* GET /projects/{id} → project status

### 7.2 Chat

* POST /chat → ask question

### 7.3 FAQ

* GET /projects/{id}/faqs

### 7.4 Embed

* GET /embed/{project_id}

---

## 8. RAG Flow (Step-by-Step)

1. User submits URL
2. Project created (status: pending)
3. Background worker crawls pages
4. Text cleaned and chunked
5. Embeddings generated
6. Chunks stored in vector DB
7. FAQs generated
8. Project marked as ready
9. User asks questions → retrieve → generate → respond

---

## 9. Technology Stack

### Frontend

* Next.js
* TypeScript
* Tailwind CSS

### Backend

* Python
* FastAPI
* Pydantic

### AI / Data

* Embedding models (abstracted)
* LLM providers (OpenAI/Anthropic compatible)
* Vector DB (Redis / Pinecone / pgvector)

### Infra

* Background queue (Celery / RQ / Cloud Tasks)
* Deployment: Vercel + Containerized API

---

## 10. Extension Points (Future)

* Authentication & user accounts
* Project dashboards
* Analytics (queries, top questions)
* Re-crawling & scheduling
* Multi-site projects
* API access
* Billing & quotas
* Team collaboration

---

## 11. Success Metrics

* Time-to-first-answer
* Number of projects created
* Chat queries per project
* Public link shares
* Embed usage

---

## 12. Risks & Mitigations

* Crawl abuse → strict rate limits
* High LLM costs → quotas + cheaper models
* Poor answers → chunk tuning + prompt iteration
* Scaling issues → async processing + caching

---

## 13. MVP Definition of Done

* URL → working chat in <2 minutes
* FAQs visible
* Public link accessible
* Embed snippet functional
* Deployed and demoable
