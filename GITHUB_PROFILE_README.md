<div align="center">

# Jimmy

### Applied AI Engineer · Building end-to-end intelligent systems

[![Python](https://img.shields.io/badge/Python-3.11%2F3.12-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-async%20first-009688?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![LangGraph](https://img.shields.io/badge/LangGraph-multi--agent-1C3C3C?style=flat-square&logo=langchain&logoColor=white)](https://langchain.com)
[![OpenTelemetry](https://img.shields.io/badge/OpenTelemetry-observability-425CC7?style=flat-square&logo=opentelemetry&logoColor=white)](https://opentelemetry.io)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-production-326CE5?style=flat-square&logo=kubernetes&logoColor=white)](https://kubernetes.io)

</div>

---

I design and ship **production-grade AI systems** — from multi-agent orchestration and LLM streaming pipelines to semantic caching, SQL guardrails, and full observability stacks.

My focus is the gap between "LLM prototype" and "system you can actually run in production": correctness, latency, safety, and cost all matter.

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

## Technical Stack

**AI / LLM**
```
LangGraph · LangChain · OpenRouter · DeepSeek R1 · Qwen3 · GPT-oss
RAG · ChromaDB · Sentence Transformers · sqlglot (AST analysis)
```

**Backend**
```
Python 3.12 · FastAPI · SQLAlchemy · asyncio · Pydantic v2
PostgreSQL · MongoDB · Server-Sent Events · JWT/Bearer auth
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
```

**Frontend**
```
Vanilla JS (ES2022) · Server-Sent Events · Plotly.js · marked.js
CSS animations (streaming bubble, answer-reveal transition, narration pills)
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

<div align="center">

`python` &nbsp; `fastapi` &nbsp; `langgraph` &nbsp; `llm-orchestration` &nbsp; `rag` &nbsp; `streaming` &nbsp; `chromadb` &nbsp; `opentelemetry` &nbsp; `kubernetes`

</div>
