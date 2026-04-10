# NetMonitor Mini-RAG (Knowledge-Augmented AI Diagnostics)

This folder contains the **static knowledge layer** used by NetMonitor’s AI diagnostics system.

It is a lightweight, practical alternative to a full Retrieval-Augmented Generation (RAG) pipeline and is designed to improve the quality, consistency, and grounding of local LLM-based network analysis **without requiring embeddings or a vector database**.

---

## What is Mini-RAG in NetMonitor?

In this project, **Mini-RAG** means:

- curated operational knowledge stored as markdown files
- simple rule-based selection of relevant knowledge
- prompt injection of matching knowledge into the AI analyzer
- structured JSON output from the LLM
- post-processing guardrails to reduce unsupported claims

Instead of using a full semantic retrieval system, NetMonitor uses a **static knowledge base + conditional loading** approach.

This makes the system:

- easier to understand
- easier to maintain
- easier to run locally
- safer for early-stage AI-assisted diagnostics

---

## Why use Mini-RAG instead of full RAG?

A full RAG system typically requires:

- document chunking
- embeddings
- a vector database
- semantic retrieval logic
- more complexity and infrastructure

For NetMonitor’s current scope, that would be unnecessary overhead.

The Mini-RAG approach gives most of the immediate value by:

- grounding AI analysis with known network runbooks
- reinforcing threshold-aware reasoning
- referencing common incident patterns
- keeping the system fully local and lightweight

---

## How it works

### AI Analysis Flow

1. NetMonitor fetches recent network metrics from InfluxDB
2. The backend computes a summarized metric snapshot
3. Threshold rules classify metrics (e.g. `ok`, `warning`, `degraded`)
4. The analyzer selects relevant knowledge files from this folder
5. The selected knowledge is injected into the LLM prompt as **supporting context**
6. The LLM returns structured JSON analysis
7. The result is sanitized to reduce hallucinated or unsupported claims
8. The frontend renders the structured output

---

## Knowledge Folder Structure

```text id="s7w8z5"
knowledge/
├── README.md
├── runbooks/
│   ├── high_latency.md
│   ├── high_jitter.md
│   └── packet_loss.md
├── incidents/
│   └── transient_congestion.md
├── thresholds/
│   └── network_baselines.md
└── architecture/
    └── ai_analysis.md
