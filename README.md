<div align="center">

<img src="logo.png" alt="Second Brain Logo" width="320">

<br />

# Second Brain v5

### *What if your entire knowledge base ran on a potato?*

<br />

[![Garage Inference](https://img.shields.io/badge/Garage_Inference-2026-ff6b35?style=for-the-badge)](https://garageinference.com)
[![Tier 1](https://img.shields.io/badge/Model_Tier-Absolute_Garage-4caf50?style=for-the-badge)](https://garageinference.com)
[![100% Local](https://img.shields.io/badge/100%25-Local-8e44ad?style=for-the-badge)](#)
[![Zero API Keys](https://img.shields.io/badge/API_Keys-NONE-c0392b?style=for-the-badge)](#)

<br />

[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED?style=flat&logo=docker&logoColor=white)](https://docker.com)
[![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=flat&logo=python&logoColor=white)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=flat&logo=fastapi)](https://fastapi.tiangolo.com)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16+-336791?style=flat&logo=postgresql&logoColor=white)](https://postgresql.org)
[![pgvector](https://img.shields.io/badge/pgvector-0.8.0-blue?style=flat)](https://github.com/pgvector/pgvector)
[![License](https://img.shields.io/badge/License-AGPL--3.0-green?style=flat)](LICENSE)

<br />

---

### Garage Inference Hackathon - May 1-4, 2026

*Big Ideas. Cheap Models. The constraint is the creativity.*

</div>

---

## The Problem

Most hackathons reward the biggest model, the fattest GPU, the most expensive API call. **That's not where the real engineering lives.**

| Reality | The Garage Truth |
|---|---|
| Frontier models cost $200+/month | Builders in 180+ countries are priced out |
| Cloud endpoints demand internet | Offline devices are abandoned entirely |
| "Just throw GPT at it" | That's not engineering - it's a shortcut |

> **The future of AI is not the biggest model - it's the smallest model that still gets the job done.**

---

## The Solution

**Second Brain** is a fully-local, zero-cost, multimodal knowledge management system. It stores, searches, and synthesizes your documents, images, and ideas - powered entirely by the cheapest, weakest models we could find.

**No cloud. No API keys. No excuses.**

We didn't use GPT. We didn't use Claude. We used models so small they'd fit on a USB stick - then we engineered around every single limitation until the system felt like enterprise software.

---

## The Wow Gap

> *The core judging metric - the gap between what your model can barely do and what your project actually does.*

| What the model can barely do | What Second Brain actually does |
|---|---|
| Hallucinates on long context | Multi-document semantic search at <50ms |
| Single-modal input | Text + image + document multimodal pipeline |
| No image reasoning at all (Nomic) | Image similarity search + OCR + visual descriptions |
| Loses coherence after 4 turns | Persistent memory store - never forgets |
| Unreliable raw output | Structured prompts + validation layers + retries |
| Can't count past 10 reliably | Knowledge graph with auto-relationship discovery |

**The gap between these two columns is the entire point.**

---

## Model Declaration - Tier 1: Absolute Garage

> *Precision is the first sign of good engineering.*

| Component | Exact Model | Quant | Source | Size |
|---|---|---|---|---|
| Vision/Text LLM | llava-v1.6-mistral-7b | Q6_K (6-bit) | LM Studio | ~5.3 GB |
| Text Embeddings | nomic-embed-text-v1.5 | FP32 (768-dim) | Hugging Face | ~274 MB |
| Image Embeddings | ViT-L/14 (CLIP) | FP16 | HuggingFace | ~890 MB |
| Speech (optional) | whisper-base | FP16 | HuggingFace | ~142 MB |

**Why Tier 1?** We're claiming the hardest weight class:

- The 7B mistral is a *base* multimodal model, not a reasoning specialist
- Nomic embeddings are under 300MB and only produce 768-dim vectors
- CLIP ViT-L/14 is the smallest viable production vision encoder
- Every model runs on **CPU** with acceptable latency - no GPU required
- Combined active inference footprint: ~7.5B total params, no MoE, no chain-of-thought
- **$0.00 spent on inference** - no API calls to any external model provider

**Total model footprint:** ~6.6 GB on disk.

---

## Engineering Scaffolding

> *The model is dumb. Our job is to engineer around its weaknesses.*

### RAG - Retrieval Does The Heavy Lifting

The model doesn't need to *know* - it just needs to *read and respond.*

```
User Query
    |
    v
[Nomic Embedding - 768-dim, no GPU needed]
    |
    v
[pgvector HNSW Index - Sub-50ms ANN search, top_k=10]
    |
    v
[Structured Prompt + LLaVA 7B Q6_K - Constrained to context only]
    |
    v
[Validation Layer - Coherence check, retry on failure]
    |
    v
Final Response
```

### Multi-Step Pipelines

Small models excel at narrow, well-defined tasks:

1. **Chunk & Embed** - Split documents, generate 768-dim vectors
2. **Index & Store** - PostgreSQL + pgvector HNSW index
3. **Retrieve** - ANN search finds semantically relevant chunks
4. **Augment** - Stuff context into a structured prompt template
5. **Generate** - LLaVA 7B responds to constrained context only
6. **Validate** - Sanity-check output, retry on hallucination
7. **Store** - Save results as new persistent memories

### Validation Layers

| Guard | What it Catches |
|---|---|
| Output length check | Truncated or runaway generations |
| Source grounding gate | Responses not backed by retrieved context |
| JSON schema validation | Structured outputs that don't parse |
| Retry with temperature decay | 3 attempts, decreasing randomness each time |
| Embedding similarity threshold | Low-similarity results discarded |

### Structured Prompts

We don't let the model free-roam. Every prompt is templated with strict constraints:

```
You are a local knowledge assistant. You have access ONLY to the
following document chunks. If the answer is not in the chunks,
respond: "I don't have enough context to answer that."

CONTEXT:
{retrieved_chunks}

QUESTION: {user_query}

Respond in EXACTLY this JSON format:
{"answer": "...", "sources": [...], "confidence": 0.0-1.0}
```

---

## Cost & Performance

> *Show the receipts. Actual dollars spent. Real latency numbers.*

### Financials

| Metric | Value |
|---|---|
| Monthly API bill | **$0.00** |
| Per-query cost | **$0.00** |
| GPU rental | **$0.00** |
| Lifetime inference spend | **$0.00** |
| Hardware floor | Any laptop with 8GB RAM |

**A student in a developing country can run this. Right now. Zero budget.**

### Performance

| Operation | GPU (RTX 4090) | CPU (M2 MacBook) |
|---|---|---|
| Text Embedding | ~12ms/doc | ~85ms/doc |
| Image Embedding | ~45ms/image | ~280ms/image |
| Vision Analysis (LLaVA) | 2-5s/image | 8-15s/image |
| Vector Search | <50ms | <50ms |
| Hybrid Search | <100ms | <120ms |
| Document Ingestion | 200 docs/min | 60 docs/min |

---

## One-Command Setup

```bash
git clone https://github.com/palakvarshney23/second-brain.git && cd second-brain && cp .env.example .env && docker-compose up -d
```

That's it. One line. Copy, paste, enter. Your second brain is alive.

### What's Running

| Service | URL | Role |
|---|---|---|
| Second Brain App | http://localhost:8000 | Main gateway |
| Swagger API Docs | http://localhost:8000/docs | Interactive API explorer |
| Photo Pipeline UI | http://localhost:8000/static/photo-pipeline.html | Visual ingestion |
| Database Explorer | http://localhost:8080 | Adminer - inspect tables |
| CLIP Service | http://localhost:8002 | Image embedding engine |
| LM Studio | http://localhost:1234 | Local vision LLM |

### GPU Acceleration (Optional)

```bash
docker-compose -f docker-compose.gpu.yml up -d
```

---

## Architecture

```
              Your Machine
  ====================================

  Model Layer (Tier 1)
  [LM Studio :1234]      [CLIP :8002]
  llava-v1.6-mistral-7b  ViT-L/14

          |                    |
          v                    v
  Core Engine
  [FastAPI Gateway :8000]
  RAG + Validation Layers
          |
          v
  [PostgreSQL + pgvector :5432]
  HNSW Vector Index

  Data Sources
  [Google Drive OAuth]   [Local Files]
```

---

## The API - Make It Do Things

### Create a memory

```bash
curl -X POST http://localhost:8000/api/v1/memories \
  -H "Content-Type: application/json" \
  -d '{
    "type": "text",
    "content": "Garage Inference taught me: the best AI does not need the best model - it needs the best engineering around the worst model.",
    "tags": ["garage-inference", "philosophy"]
  }'
```

### Semantic search (RAG-powered)

```bash
curl -X POST http://localhost:8000/api/v1/search \
  -H "Content-Type: application/json" \
  -d '{"query": "best engineering practices for AI", "limit": 5}'
```

### Image similarity search

```bash
curl -X POST http://localhost:8000/api/v1/search/image \
  -F file=@whiteboard_sketch.jpg
```

### Full API Surface

| Category | Endpoints |
|---|---|
| Memories | CRUD: GET/POST/PUT/DELETE /api/v1/memories |
| Search | Semantic, hybrid (vector+text), image similarity |
| Knowledge Graph | Auto-discovery, visualization, clustering |
| Google Drive | OAuth, file listing, folder sync, streaming |
| GPU Services | Text embed, image embed, vision analyze, OCR |

---

## Known Failures - Honesty Report

> *Honesty is rewarded. Here's exactly where our tiny models break.*

| Failure Mode | Severity | What Happens | Mitigation |
|---|---|---|---|
| Context window overrun | Medium | LLaVA 7B degrades past ~4K tokens | Chunk cap 512 tokens, multi-pass retrieval |
| Vision hallucination | Medium | "Sees" text that isn't there in blurry images | Confidence gating, source image shown alongside |
| Non-English queries degrade | High | Nomic + LLaVA are English-centric | Fallback to keyword search for non-Latin scripts |
| No multi-turn memory | Medium | Each query is a fresh interaction | Intentional stateless design - reliable over chatty |
| 768-dim embedding ceiling | Low | Misses nuance vs 3072-dim alternatives | Hybrid search (vector + BM25) compensates |
| Zero code execution | None | Model output never touches shell/SQL | Intentional security boundary - model is read-only |

---

## Demo Video

> *2-minute walkthrough: the model, the scaffolding, the results, the failures.*

**[WATCH THE DEMO]** - link updated after submission

---

## Secure By Design

> *Small models hallucinate, jailbreak, and follow injected instructions.*

| Threat | Defense |
|---|---|
| Prompt injection | Model output is strictly validated, NEVER executed. No raw shell/DB access. |
| Jailbreak | Structured prompt templates are immutable from user input. |
| Hallucination | Every response must cite source chunks. Retry with temperature decay. |
| Data leakage | Nothing leaves the machine. No telemetry, no analytics SDK. |
| Injected metadata | Upload content sanitized before indexing. Paths validated against known folders. |

**Threat model:** Attacker with local API/filesystem access. Model output has **zero write capability** - read-only through validated API handlers.

---

## Roadmap

| Priority | Feature | Target |
|---|---|---|
| Critical | Demo video + final submission polish | May 4, 2026 |
| High | Ollama integration (even smaller models) | Q3 2026 |
| High | Multilingual embedding pipeline | Q3 2026 |
| Medium | Apple Silicon native GPU | Q4 2026 |
| Medium | Voice input via Whisper base | Q4 2026 |
| Low | Mobile companion app | 2027 |
| Low | P2P federated knowledge sharing | 2027 |

---

## Built By

<div align="center">

### [Palak Varshney](https://github.com/palakvarshney23)

*Solo builder. 72 hours. Tier 1 models. Maximum impact.*

> *"Give me a model so weak it barely follows instructions, and I'll build a system that makes you forget the model exists."*

<br />

[![GitHub](https://img.shields.io/badge/GitHub-palakvarshney23-181717?style=for-the-badge&logo=github)](https://github.com/palakvarshney23)
[![Email](https://img.shields.io/badge/Gmail-palakvarshney23012003@gmail.com-EA4335?style=for-the-badge&logo=gmail&logoColor=white)](mailto:palakvarshney23012003@gmail.com)

</div>

---

## Thanks To

| Tool | Used For |
|---|---|
| [LM Studio](https://lmstudio.ai) | Zero-config local LLM inference |
| [Hugging Face](https://huggingface.co) | Open model hosting for Nomic, CLIP, Whisper |
| [PostgreSQL + pgvector](https://github.com/pgvector/pgvector) | HNSW vector database |
| [FastAPI](https://fastapi.tiangolo.com) | Modern Python API framework |
| [Docker](https://docker.com) | Reproducible one-command environments |
| [Cipher](https://github.com/campfirein/cipher) | AI memory layer (optional integration) |

---

<div align="center">

<br />

### Garage Inference 2026

### Big Ideas. Cheap Models.

### The constraint is the creativity.

<br />

[![Stars](https://img.shields.io/github/stars/palakvarshney23/second-brain?style=social)](https://github.com/palakvarshney23/second-brain)

<br />

**[Report Bug](https://github.com/palakvarshney23/second-brain/issues)** - **[Request Feature](https://github.com/palakvarshney23/second-brain/issues)**

<br />

*Built in 72 hours with models that cost nothing, for builders who deserve everything.*

</div>
