# Aryan Rajput
**AI Engineer · Rust inference · RAG pipelines · Multimodal AI · CPU-constrained LLM serving**

I build the infrastructure layer of AI systems — not the wrappers around APIs, but the engines underneath them. My focus is local LLM serving, retrieval-augmented generation, multimodal pipelines, and agent systems that work correctly under real constraints.

---

## What I've Built

### [Nexus](https://github.com/aryan105825/nexus-system) — Three-service local AI workspace
> **Constraint:** 8 GB Docker memory · CPU only · No GPU · No cloud inference

A production-designed system built to answer one question: how do you run a complete AI pipeline locally when memory is the hard limit?

**Rust inference engine** (`infer-engine`)
- Pre-allocated memory pool — 25 slots × 4 MB, `Drop`-based reclamation, leaks structurally impossible
- Bounded job queue (depth 20) with `try_send` — overload rejected at the boundary, never absorbed
- LRU model registry (max 5) — deterministic memory under model-churn workloads
- **~2 µs scheduler overhead** (Criterion) · **3.93 ms P95** at 50 VUs · **342 req/s** at 0.00% error rate (k6)
- Zero GC pauses. Zero allocation at request time.

**Python RAG pipeline** (`rag-main`)
- Phi-3.5-mini-instruct Q4_K_M at **~143 tok/s** warm · **0.91s** warm latency · **4.73s** cold start
- Embedding delegated to Rust over loopback HTTP — saves **~500 MB** baseline RAM vs PyTorch in-process
- Guardrails run synchronously before `StreamingResponse` — HTTP error semantics preserved
- **3,936 MB free RAM at peak load** — guardrail floor: 500 MB

**Next.js 14 frontend** (`nexus-frontend`)
- SSE via Route Handler bypass — Next.js rewrites buffer response bodies (ECONNRESET); Route Handler pipes ReadableStream directly
- AbortController wired to three abort triggers: query change, palette close, stop button
- Live knowledge graph via Supabase Realtime subscription on note saves

Every failure mode returns the correct HTTP status. The system rejects work rather than degrading silently.

---

### [BehaviorLens](https://github.com/aryan105825/behaviorlens) — Multimodal RAG behavioral intelligence pipeline
> **Problem:** Naive VLM-on-video pipelines hallucinate ~18% of the time — confabulated UI labels, missed micro-interactions, fabricated causal chains.

An open-source event-driven pipeline that analyzes user sessions by grounding Vision-Language Model outputs in deterministic browser SDK events, eliminating the hallucination problem at the architectural level.

- **TypeScript SDK** captures DOM mutations, clicks, rage clicks, scroll positions, form events, JS errors — in-browser PII masking before data leaves the client
- **Three-phase timestamp sync**: calibration → EMA drift detection (α=0.3) → quarantine for >500ms drift. ±50ms frame-event alignment.
- **Event-driven frame sampling**: frames extracted only on trigger events — **~85% reduction** vs. uniform 1fps sampling
- **LangGraph agent router** dispatches to Transcription / Anomaly / KPI / Behavior agents by event priority; Pydantic-validated outputs with retry on failure
- Every model output requires a `timestamp_citation` referencing a verified SDK event ID — hallucination target: **<2%** (down from 18% baseline)
- Provider-agnostic: one `.env` line switches from Gemini 2.5 Flash → Claude Sonnet 4 + Kafka MSK + Pinecone

---

### [DevSecOps Autopilot](https://github.com/aryan105825/35222941) — Four-agent security pipeline on GitLab Duo
> **Trigger:** Mention or assign to any Merge Request. The pipeline does the rest.

A multi-agent system built on the GitLab Duo Agent Platform. One MR mention triggers a four-stage sequential pipeline — scans code, maps to security standards, posts a structured review with fix snippets — zero human involvement.

- **MR Context Builder** — pulls diffs, file contents, existing SAST findings, CI failures into a single JSON object. No analysis, no commentary.
- **Security Scanner** — scans `added_lines` for 12 vulnerability classes (SQLi, XSS, SSRF, hardcoded secrets, IDOR, prototype pollution, eval misuse). HIGH/MEDIUM/LOW confidence. Verdicts on existing GitLab findings: CONFIRMED / FALSE_POSITIVE / NEEDS_INVESTIGATION.
- **Compliance Mapper** — enriches with OWASP Top 10 (2021), CWE, CVSS 3.1 (chain-of-thought scored), P0–P3 priority, merge recommendation: BLOCK / REVIEW / PASS.
- **Remediation Writer** — posts structured code review, creates GitLab vulnerability issues for P0/P1 findings, confirms or dismisses existing SAST findings. Fix snippets touch only the vulnerable lines.

---

## Stack
```
Systems      Rust · Axum · crossbeam · tract-onnx · tower · Prometheus
AI/ML        llama.cpp · Phi-3.5-mini · sentence-transformers · ONNX · LangGraph · GitLab Duo Agent Platform
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
- Building on Nexus — batching, speculative decoding, extended observability
- Building on BehaviorLens — production Kafka integration, Pinecone vector store
- Open to **remote AI infrastructure and LLM systems roles** — US and EU firms
- IST (UTC+5:30) — 4–6 hr overlap with EU, available late IST for US East Coast

📬 aryan105825@gmail.com · [LinkedIn](https://linkedin.com/in/aryan-rajput1058)
