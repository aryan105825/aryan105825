# Aryan Rajput

**SDE & AI Engineer** — I build production-grade AI systems end-to-end, from low-level inference infrastructure to the interfaces people actually use.

My current focus is on systems where performance is non-negotiable: LLM serving, retrieval-augmented generation, and the full-stack glue that ties them into coherent products. I care about the complete picture — correctness, measured performance, and the user experience of working with AI in real time.

---

## What I'm Building

### Nexus Platform
A multi-service AI workspace combining three purpose-built components into a single real-time collaborative environment.

The compute core is written in Rust — chosen specifically to eliminate GC pauses and unpredictable tail latency inherent to Python-based inference servers. It uses a pre-allocated memory pool to prevent allocation thrashing under concurrent load, a lock-free bounded job queue for admission control, and an LRU model registry for stable memory under model-churn workloads. Scheduling overhead is consistently sub-100µs regardless of model compute time.

The retrieval layer is a Python service handling semantic search via HNSW vector indexing, returning relevant document context at P95 under 80ms. The frontend is a Next.js 14 application that orchestrates both services in a sequential pipeline — retrieval context is assembled and passed to the inference engine, with token output streamed back to a rich-text editor surface in real time.

The system was designed around a clear principle: the frontend owns no intelligence. All heavy computation is delegated to purpose-built backends, keeping each layer independently optimizable and the total end-to-end latency budget predictable.

**Core technologies:** Rust · Axum · ONNX Runtime · Python · HNSW · Next.js 14 · TypeScript · Supabase · Zustand · Prometheus

---

## Technical Range

My work spans three areas that I treat as deeply connected rather than separate disciplines.

**Systems & Infrastructure** — Rust, concurrent scheduling, memory management, lock-free data structures, ONNX model serving, Prometheus observability, structured tracing, CI/CD with strict quality gates.

**AI & Machine Learning** — RAG pipeline design, semantic embedding, NER, LLM application development, deep learning (CNNs, RNNs, Transformers), GANs, NLP, computer vision, TensorFlow, PyTorch.

**Full-Stack Engineering** — React, Next.js, Node.js, TypeScript, REST API design, real-time interfaces, edge auth, scalable system architecture.

---

## Education & Certifications

**B.Tech, Computer Science** — Noida Institute of Engineering & Technology

**DeepLearning.AI Specializations** — Deep Learning · Natural Language Processing · GANs · TensorFlow: Advanced Techniques · TensorFlow: Data and Deployment · Generative AI · Mathematics for Machine Learning and Data Science

**Other** — CS50x (Harvard) · Structuring Machine Learning Projects · LangChain & LLM Application Development · Full-Stack Engineering

---

## Currently

- Building and refining the Nexus platform
- Open to remote engineering roles in AI infrastructure, LLM systems, or full-stack AI product development

📬 aryanrajput1058@gmail.com · [LinkedIn](https://www.linkedin.com/in/aryanrajput1058)
