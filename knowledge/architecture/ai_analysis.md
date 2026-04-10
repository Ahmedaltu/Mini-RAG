# Architecture: AI Analysis in NetMonitor

## Overview
The AI analysis pipeline provides natural-language explanations of network health by combining recent metrics from InfluxDB with a local LLM via Ollama.

## Components

### app/ai/analyzer.py
- `fetch_recent_summary(settings, window_minutes=30)` — queries InfluxDB for `network_metrics` measurements over the last N minutes, computes per-field mean/min/max/samples.
- `analyze(settings)` — builds a structured prompt from the metrics summary and sends it to the Ollama `/api/generate` endpoint; returns the model's plain-text analysis.

### Ollama (LLM backend)
- Runs as a Docker container (`netmonitor-ollama`) on port 11434.
- Default model: `llama3.2:1b` (1.3 GB; fast, local, private).
- Reached by the backend at `http://ollama:11434/api/generate` (Docker network).
- The model must be pre-pulled: `ollama pull llama3.2:1b`.

### API endpoint
- `GET /explain` — calls `analyze(settings)` and returns the LLM's response as JSON.
- No authentication required (read-only analysis).

## Data Flow
```
InfluxDB (last 30 min)
        │
        ▼
fetch_recent_summary()  →  metrics dict (mean/min/max per field)
        │
        ▼
build_prompt()          →  structured text prompt
        │
        ▼
Ollama /api/generate    →  plain-text network health analysis
        │
        ▼
GET /explain response
```

## Configuration
| Setting          | Config key              | Env override   | Default                             |
|------------------|-------------------------|----------------|-------------------------------------|
| Ollama endpoint  | `ai.url`                | `OLLAMA_URL`   | `http://ollama:11434/api/generate`  |
| Model name       | `ai.model`              | `OLLAMA_MODEL` | `llama3.2:1b`                       |
| Request timeout  | `ai.timeout`            | —              | 60 s                                |
| Metrics window   | hardcoded in `analyze`  | —              | 30 minutes                          |

## Future Extension: RAG
The `knowledge/` directory at the project root contains operational runbooks, incident patterns, and threshold references. These are currently static markdown files intended as the future retrieval corpus for a context-augmented prompt strategy:

1. Embed knowledge files at startup (e.g. sentence-transformers)
2. At `/explain` time, retrieve top-k relevant chunks based on the current metrics summary
3. Inject retrieved context into the LLM prompt before the metrics data
4. LLM produces a grounded, runbook-aware explanation

No RAG code is implemented yet. The knowledge files are the preparation step.
