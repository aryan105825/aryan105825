# Aryan Rajput
**AI Engineer · Rust inference · RAG pipelines · Multimodal AI · CPU-constrained LLM serving**

I build the infrastructure layer of AI systems — not the wrappers around APIs, but the engines underneath them. My focus is on local LLM serving, retrieval-augmented generation, multimodal pipelines, and making inference work correctly under real resource constraints.

---

## What I've Built

### [Nexus](https://github.com/aryan105825/nexus-system) — Three-service local AI workspace
> **Constraint:** 8 GB Docker memory · CPU only · No GPU · No cloud inference

A production-designed system built to answer one question: how do you run a complete AI pipeline locally when memory is the hard limit?

**Rust inference engine** (`infer-engine`)
- Pre-allocated memory pool: 25 slots × 4 MB, `Drop`-based reclamation — leaks are structurally impossible
- Bounded job queue (depth 20) with `try_send` — overload rejected at the boundary, never absorbed
- LRU model registry (max 5) — deterministic memory under model-churn workloads
- **~2 µs scheduler overhead** (Criterion); **3.93 ms P95** at 50 VUs; **342 req/s** at 0.00% error rate (k6)
- Zero GC pauses. Zero allocation at request time.

**Python RAG pipeline** (`rag-main`)
- Phi-3.5-mini-instruct Q4_K_M running locally at **~143 tok/s** warm, TTFT **0.91s** warm / **4.73s** cold
- Embedding delegated to Rust over loopback HTTP — saves **~500 MB** baseline RAM vs loading PyTorch in-process
- Guardrails run synchronously before `StreamingResponse` is returned — HTTP error semantics preserved
- Memory guardrail: **3,936 MB free at peak load** — service stays live under Phi-3.5's 4.4 GB footprint

**Next.js 14 frontend** (`nexus-frontend`)
- The frontend owns no intelligence — it orchestrates two purpose-built backends
- SSE via Route Handler bypass — Next.js rewrites buffer response bodies causing ECONNRESET; Route Handler pipes ReadableStream directly
- AbortController lifecycle wired to three abort triggers: query change, palette close, stop button
- Live knowledge graph via Supabase Realtime subscription on note saves

Every failure mode returns the correct HTTP status. The system rejects work rather than degrading silently.

→ **[Portfolio & benchmarks](https://frontend-port-one.vercel.app)**

---

### [BehaviorLens](https://github.com/aryan105825/behaviorlens) — Multimodal RAG behavioral intelligence pipeline
> **Problem:** Naive VLM-on-video pipelines hallucinate ~18% of the time — confabulated UI labels, fabricated causal chains, missed micro-interactions.

An open-source event-driven pipeline that analyzes user sessions by grounding Vision-Language Model outputs in deterministic browser SDK events, eliminating the hallucination problem at the architectural level.

**Architecture** (6-stage event-driven pipeline)
- **TypeScript SDK** captures DOM mutations, click coordinates, rage clicks, scroll positions, form events, and JS errors — with in-browser PII masking before any data leaves the client
- **Three-phase timestamp sync** (ADR-004): calibration → EMA drift detection (α=0.3) → quarantine for sessions with >500ms drift. Maintains ±50ms frame-event alignment.
- **Event-driven frame sampling** (ADR-002): extracts frames only on trigger events with per-priority windows and frame counts — **~85% reduction** in frames processed vs. uniform 1fps sampling
- **LangGraph agent router** dispatches to Transcription / Anomaly / KPI / Behavior agents based on event priority; every output validated against Pydantic schemas with retry on failure
- Every model output requires a `timestamp_citation` referencing a specific SDK event ID — hallucination rate target: **<2%** on verified events (down from 18% baseline)
- **One-line production switch**: `LLM_PROVIDER=anthropic` in `.env` — all agent code is provider-agnostic (prototype uses Gemini 2.5 Flash; production path targets Claude Sonnet 4 + Kafka MSK + Pinecone)

---

## Stack
```
Systems      Rust · Axum · crossbeam · tract-onnx · tower · Prometheus
AI/ML        llama.cpp · Phi-3.5-mini · sentence-transformers · ONNX · PyTorch · LangGraph · Gemini 2.5 Flash
Backend      Python · FastAPI · Node.js · Supabase · pgvector · PostgreSQL · Redis Streams · Pydantic
Frontend     Next.js 14 · TypeScript · React · Tiptap · ReactFlow · Zustand · D3
Infra        Docker Compose · k6 · Criterion · Prometheus · Vercel · FFmpeg
```

---

## Writing
TinyLlama 1.1B consistently fabricated relationships between unrelated entities in multi-document context windows. Every prompt mitigation failed. Here's why model scale was the only fix, and how I diagnosed it.

---

## Background
B.Tech Computer Science — NIET  
DeepLearning.AI: Deep Learning · NLP · GANs · TensorFlow · Generative AI · Mathematics for ML  
CS50x (Harvard)

---

## Currently
- Actively building on Nexus — batching, speculative decoding, extended observability
- Actively building on BehaviorLens — production Kafka integration, Pinecone vector store
- Open to **remote AI infrastructure and LLM systems roles** — US and EU firms
- IST (UTC+5:30) — 4–6 hr overlap with EU, available late IST for US East Coast

📬 aryan105825@gmail.com · [LinkedIn](https://linkedin.com/in/aryan-rajput1058) · [Portfolio](https://frontend-port-one.vercel.app)
