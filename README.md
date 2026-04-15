<div align="center">

# Ahmed Soliman (Jimmy)

### Data & AI Engineer · Building end-to-end intelligent systems

[![Python](https://img.shields.io/badge/Python-3.11%2F3.12-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-async%20first-009688?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![LangGraph](https://img.shields.io/badge/LangGraph-multi--agent-1C3C3C?style=flat-square&logo=langchain&logoColor=white)](https://langchain.com)
[![OpenTelemetry](https://img.shields.io/badge/OpenTelemetry-observability-425CC7?style=flat-square&logo=opentelemetry&logoColor=white)](https://opentelemetry.io)
[![Docker](https://img.shields.io/badge/Docker-containerized-2496ED?style=flat-square&logo=docker&logoColor=white)](https://docker.com)
[![Flask](https://img.shields.io/badge/Flask-web%20framework-000000?style=flat-square&logo=flask&logoColor=white)](https://flask.palletsprojects.com)
[![SQL](https://img.shields.io/badge/SQL-relational%20DB-4479A1?style=flat-square&logo=postgresql&logoColor=white)](https://postgresql.org)
[![MagenticOne](https://img.shields.io/badge/MagenticOne-multi--agent-5C2D91?style=flat-square&logo=microsoft&logoColor=white)](https://microsoft.com)
[![AWS](https://img.shields.io/badge/AWS-cloud-FF9900?style=flat-square&logo=amazonaws&logoColor=white)](https://aws.amazon.com)
[![Azure](https://img.shields.io/badge/Azure-cloud-0078D4?style=flat-square&logo=microsoftazure&logoColor=white)](https://azure.microsoft.com)

📍 Giza, Egypt &nbsp;·&nbsp; ✉️ a.g.soliman@student.aast.edu

</div>

---

I design and ship **production-grade AI systems** — from multi-agent orchestration and LLM streaming pipelines to semantic caching, SQL guardrails, and full observability stacks.

My focus is the gap between "LLM prototype" and "system you can actually run in production": correctness, latency, safety, and cost all matter. With a background spanning agentic AI research, enterprise ERP systems, and real-time data engineering, I bring both breadth and depth to every system I build.

---

## Experience

**Data & AI Engineer** &nbsp;·&nbsp; Dayra Market &nbsp;·&nbsp; *Mar 2025 – Present*
- Build AI architectures including LLMs, multi-agent orchestrations, and Computer Vision models for dynamic pricing and automated trade-in services.
- Automated 90% of incoming customer traffic through an LLM-based Chatbot service.
- Lead development of data warehouses, data flow, and ETL pipelines.
- Reduced 60% of time and effort required for core business processes.

**R&D Engineer (Generative AI)** &nbsp;·&nbsp; Electronic Government Authority of RAK &nbsp;·&nbsp; *Nov 2024 – Feb 2025*
- Researched latest AI trends and adapted them to government sectors (Labor Courts, Traffic Control, Rental Courts).
- Developed an architecture for automated generative orchestration of AI agents with no-code scalability.

**Technical Lead** &nbsp;·&nbsp; Microsoft &nbsp;·&nbsp; *Aug 2024 – Nov 2024*
- Youngest Tech Lead in the MENA region, leading a squad of support engineers.
- Delivered 12 technical knowledge sessions across SQL, AI, OOP, BI, and RPA.
- Achieved ~75% Claim/Closure rate without Dev Team involvement; 4.91★ customer satisfaction.

**Software Support Engineer – D365 F&O** &nbsp;·&nbsp; Microsoft &nbsp;·&nbsp; *Apr 2023 – Aug 2024*
- Built data pipelines and integrated AI models with clients' ERP systems.
- Debugged application code (X++, .NET, Python) and conducted performance analysis.

---

## What I Build

**Multi-Agent Orchestration** &nbsp;·&nbsp; Stateful LangGraph workflows with conditional routing, interrupt/resume flows, and per-node LLM specialisation. Agents collaborate — planner decides intent, orchestrator builds tasks, specialists execute, insight agent surfaces patterns, narrator explains progress in plain language.

**Streaming AI Pipelines** &nbsp;·&nbsp; End-to-end token streaming: LLM `astream_events v2` → Server-Sent Events → animated browser UI. Narration tokens, answer tokens, and workflow events all flow over a single SSE channel with a typed protocol both server and client agree on.

**Semantic Caching** &nbsp;·&nbsp; ChromaDB HNSW cosine-similarity index detects near-duplicate queries (threshold ≥ 0.85) and serves cached answers without touching the LLM. Schema semantics are cached separately with TTL-based invalidation and double-checked locking to prevent stampede on startup.

**SQL Safety & Validation** &nbsp;·&nbsp; Stateless validation layer enforcing read-only queries, CTE-based LIMIT injection, deny-listed patterns (`xp_cmdshell`, `LOAD DATA`, stacked statements), and role-based row caps (admin 10k / analyst 5k / viewer 1k) — all before a single query touches the database.

**Schema-Aware Intelligence** &nbsp;·&nbsp; Background warm-up: `pg_catalog` introspection → LLM enrichment adds business descriptions, semantic roles, and typical use-cases per column → cached as a `SchemaSemantic` object. Every downstream agent (planner, SQL, insight) receives structured schema context, not raw DDL.

**Insight Generation** &nbsp;·&nbsp; Converts SQL result sets into narratives, anomaly flags (IQR/Z-score), trend direction via Pearson correlation, and next-best-action suggestions — all from a single structured LLM call. Column semantic tags resolved via three-layer lookup: schema cache → SQL AST alias lineage (sqlglot) → type heuristic.

---

## Flagship Project — SIA-Brain

An analytics AI that takes a natural-language question and produces a researched answer, interactive Plotly chart, and insight cards — all streamed in real time.

```
User question
    │
    ▼
schema_check ──► planner ──► orchestrator
                                 │
              ┌──────────────────┼────────────────┐
              ▼                  ▼                 ▼
            sql               rag            answer (cache hit)
         (+ validation)    (ChromaDB)
              │
       ┌──────┴──────┐
       ▼             ▼
  visualization   insight ──► answer ──► END
  (Plotly JSON)  (narratives,
                  anomalies,
                  trends)
```

**Architecture highlights:**

| Concern | Approach |
|---|---|
| Agent orchestration | LangGraph 8-node StateGraph, MemorySaver checkpointer |
| LLM routing | OpenRouter — different models per task (DeepSeek R1 for planning, Qwen3-32B for SQL/insight, GPT-oss-120B for final answer) |
| Streaming | `astream_events(version="v2")` → typed SSE frames → browser EventSource |
| Progress narration | Dedicated `ProgressNarratorAgent` — template messages for zero-latency node transitions, LLM streaming for task-plan narration |
| Schema warm-up | Background thread at startup; `_build_lock` prevents duplicate enrichment builds |
| Caching | 4-collection ChromaDB store: schema (2 hr TTL), session (30 min), answer (hash-keyed), semantic similarity (HNSW) |
| SQL safety | `ValidationAgent`: deny-list, CTE LIMIT injection, read-only enforcement, RBAC row caps |
| Policy | OPA Rego RBAC — `ENABLE_POLICY_CHECK` gate, role/action/resource model |
| Observability | OpenTelemetry → OTLP Collector → Grafana Tempo; correlation IDs propagated via ContextVar |
| Deployment | Multi-stage Docker, gunicorn+uvicorn, Kubernetes HPA (2→10), nginx SSE-compatible ingress, CI/CD via GitHub Actions → GHCR |

---

## Other Projects

### Smart Judge — Chinese Criminal Law MAO
A research project benchmarking multi-agent orchestration (MAO) against a single LLM on high-complexity legal judgement tasks. The system processes Chinese criminal case facts, extracts legally relevant features via fine-tuned LLMs, and predicts verdict sentences — outperforming a single-agent baseline by 10 months in MAE on sentence length prediction.

**Tech Stack:** `Python` `LangGraph` `LangChain` `XGBoost` `PyTorch` `LLM Fine-tuning` `Pandas` `JSON`

---

### Sia-Pulse
A healthcare AI system that leverages machine learning to deliver insights and analytics for better healthcare outcomes — covering patient data trends, risk indicators, and operational metrics.

**Tech Stack:** `Python` `FastAPI` `PyTorch` `Pandas` `PostgreSQL` `LangChain` `Azure ML`

---

### ChatBot
An e-commerce conversational AI designed to enhance customer engagement, answer product queries, and streamline the shopping experience through natural language interactions.

**Tech Stack:** `Python` `FastAPI` `LangChain` `Azure OpenAI` `LangGraph` `PostgreSQL` `Vector DB`

---

### Fraud Detector
A robust fraud detection application that identifies and flags suspicious activities across transactional platforms using anomaly detection and classification models.

**Tech Stack:** `Python` `PyTorch` `Pandas` `Scikit-learn` `XGBoost` `PostgreSQL` `FastAPI`

---

### Phone Segmentation
A data analysis project focused on segmenting mobile phone users by usage patterns and demographics to surface consumer behaviour insights for business decision-making.

**Tech Stack:** `Python` `Pandas` `Scikit-learn` `Matplotlib` `R` `Statistical Analysis`

---

### TradeIn API
A RESTful API that powers trade-in workflows for retailers, enabling customers to submit, assess, and complete device trade-ins programmatically with automated valuation logic.

**Tech Stack:** `Python` `FastAPI` `PostgreSQL` `SQLAlchemy` `Pydantic` `Docker`

---

## Technical Stack

**AI / LLM**
```
LangGraph · LangChain · OpenRouter · DeepSeek R1 · Qwen3 · GPT-oss
AutoGen · Swarm · RAG · ChromaDB · Sentence Transformers · sqlglot (AST analysis)
Azure OpenAI · Azure ML Studio · LLM Fine-tuning
```

**Backend**
```
Python 3.12 · FastAPI · Flask · SQLAlchemy · asyncio · Pydantic v2
PostgreSQL · MS SQL · MongoDB · Vector DB · Server-Sent Events · JWT/Bearer auth
```

**Data & ML**
```
Pandas · PyTorch · Keras · OpenCV · XGBoost · Scikit-learn
ETL Pipelines · Data Warehousing · Statistical Analysis · R · Matlab
```

**Observability & Security**
```
OpenTelemetry SDK · OTLP · Grafana Tempo · OPA (Open Policy Agent)
Structured logging · Correlation IDs · slowapi rate limiting
```

**Infrastructure**
```
Docker (multi-stage) · Kubernetes · nginx · Helm
GitHub Actions · GHCR · SSH deploy · ruff · black · mypy · pytest
Cloud: Azure ML · AWS Fundamentals
```

**Frontend**
```
Vanilla JS (ES2022) · Server-Sent Events · Plotly.js · marked.js
CSS animations (streaming bubble, answer-reveal transition, narration pills)
HTML5 · CSS · JS · .NET Framework (C#, X++)
```

---

## Engineering Principles

**No silent failures.** Every agent has a typed output contract. Validation errors surface before they reach the database. LLM parse failures fall back gracefully without crashing the pipeline.

**Latency where it counts.** Schema warm-up runs in a background thread so the first user request is fast. Template-based narration messages emit at zero cost while LLM-generated narration streams in parallel. Base64 chart images are stripped from SSE payloads (saves ~150 KB per response).

**Safety is not optional.** SQL agents operate behind a validation layer that can never be bypassed. OPA policies enforce RBAC at the action/resource level. Rate limiting sits at the middleware boundary, not the application layer.

**Observable by default.** Every request carries a correlation ID from middleware through to LLM calls. Spans are exported to a Tempo backend. Node execution times are logged and surfaced in SSE events.

---

## Currently Exploring

- Agent evaluation frameworks — measuring answer quality, hallucination rate, and task completion across LLM providers
- Structured outputs with constrained decoding for more reliable JSON from smaller models
- Async agent memory with semantic deduplication

---

## Education

**BS in Computer Science** (ABET-Accredited) &nbsp;·&nbsp; Arab Academy for Science and Technology &nbsp;·&nbsp; *2018 – 2022*
Core: AI & Digital Data &nbsp;|&nbsp; Minor: Information Systems

**Non-degree Summer Exchange** &nbsp;·&nbsp; University of Arkansas &nbsp;·&nbsp; *June – Aug 2021*

---

<div align="center">

`python` &nbsp; `fastapi` &nbsp; `langgraph` &nbsp; `llm-orchestration` &nbsp; `rag` &nbsp; `streaming` &nbsp; `chromadb` &nbsp; `opentelemetry` &nbsp; `kubernetes`

</div>
