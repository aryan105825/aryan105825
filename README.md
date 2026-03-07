# Aryan Rajput

**AI Engineer · Rust inference · RAG pipelines · CPU-constrained LLM serving**

I build the infrastructure layer of AI systems — not the wrappers around APIs, but the engines underneath them. My current focus is local LLM serving, retrieval-augmented generation, and making inference work correctly under real resource constraints.

---

## What I've Built

### [Nexus](https://github.com/aryan105825/nexus-workspace) — Three-service AI workspace

> **Constraint:** 8 GB RAM · CPU only · No GPU · No cloud inference

A production-designed system built to answer one question: how do you run a complete AI pipeline locally when memory is the hard limit?

**Rust inference engine** (`infer-engine`)
- Pre-allocated 100 MB memory pool — 25 slots × 4 MB, `Drop`-based reclamation, leaks structurally impossible
- Bounded job queue (depth 20) with `try_send` — overload rejected at the boundary, never absorbed
- LRU model registry (max 5) — deterministic memory under model-churn workloads
- Scheduler overhead: **~81 µs P50** regardless of model compute time
- Zero GC pauses. Zero allocation at request time.

**Python RAG pipeline** (`rag-main`)
- Phi-3.5-mini-instruct Q4_K_M running locally at **~115 tok/s**, TTFT **<100 ms**
- Embedding delegated to Rust over loopback HTTP — saves **~500 MB** baseline RAM vs loading PyTorch in-process
- Guardrails run synchronously before `StreamingResponse` is returned — HTTP error semantics preserved
- Vector retrieval: **85 ms P50**, 186 ms P95 via Supabase pgvector

**Next.js 14 frontend** (`nexus-frontend`)
- The frontend owns no intelligence — it orchestrates two purpose-built backends
- SSE delivered via Route Handler bypass — Next.js rewrites buffer response bodies, causing ECONNRESET; Route Handler pipes ReadableStream directly
- AbortController lifecycle wired to three abort triggers: query change, palette close, stop button
- Live knowledge graph via Supabase Realtime subscription on note saves

Every failure mode returns the correct HTTP status. The system rejects work rather than degrading silently.
  
→ **[Portfolio & benchmarks](https://frontend-port-one.vercel.app)**

---

## Stack

```
Systems    Rust · Axum · crossbeam · tract-onnx · tower · Prometheus
AI/ML      llama.cpp · Phi-3.5-mini · sentence-transformers · ONNX · PyTorch
Backend    Python · FastAPI · Node.js · Supabase · pgvector · Pydantic
Frontend   Next.js 14 · TypeScript · React · Tiptap · ReactFlow · Zustand · D3
Infra      k6 · Criterion · Prometheus · Vercel · Git
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
- Open to **remote AI infrastructure and LLM systems roles** — US and EU firms
- IST (UTC+5:30) — 4–6 hr overlap with EU, available late IST for US East Coast

📬 aryan105825@gmail.com · [LinkedIn](https://linkedin.com/in/aryan-rajput1058) · 
