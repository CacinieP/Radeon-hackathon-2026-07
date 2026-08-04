# Radeon Sentinel — Poster Copy (single page)

> Layout guide for a one-page poster (A3 or 16:9). Submit as PDF/PNG.
> All text English. Keep technical terms readable from 1m away.

---

## Header

**Radeon Sentinel**
**Logs never leave the laptop.** — A Private SRE Co-pilot on AMD Radeon GPU

*AMD AI DevMaster Hackathon · Track 2 · Yichun Deng*

---

## Left column — The Problem

**When production breaks at 3 AM**, engineers either investigate manually
(slow, error-prone) or paste sensitive logs into a cloud chatbot (exposing
secrets to a third party).

**Radeon Sentinel is the third option:** a private SRE co-pilot that runs
investigation entirely on a local AMD Radeon GPU via ROCm. Core inference
never calls a remote API.

**Co-pilot, not autopilot:** every state-changing action is gated behind
human approval. Three scenarios, **zero** unapproved actions.

---

## Center — Architecture diagram

```
┌─────────────────────────────────────────┐
│            Gradio UI / CLI              │
├─────────────────────────────────────────┤
│         Policy Gate (Approval)          │
├─────────────────────────────────────────┤
│        Agent Orchestrator               │
│  ┌──────────┬──────────┬──────────┐    │
│  │ Local    │ Tool     │ Case     │    │
│  │ RAG      │ Broker   │ Memory   │    │
│  │(cited)   │(allow-   │(SQLite,  │    │
│  │          │ listed)  │ audited) │    │
│  └──────────┴──────────┴──────────┘    │
├─────────────────────────────────────────┤
│   vLLM · Qwen2.5-7B · AMD Radeon GPU   │
│          gfx1100 · ROCm 7.2.1           │
└─────────────────────────────────────────┘
```

---

## Right column — Results

### Five Agent Capabilities (all implemented)
- ✅ Local RAG with source citations
- ✅ Allow-listed tool invocation (default-deny)
- ✅ Multi-step task planning
- ✅ Per-case multi-turn memory (SQLite)
- ✅ Explicit permission gate (0 unapproved actions)

### Performance (A/B, 20 runs, gfx1100)

| | float16 | **GPTQ-Int4** |
|---|---|---|
| Throughput | 29 tok/s | **96 tok/s** |
| p50 latency | 3.45 s | **1.01 s** |
| Model size | 14.4 GiB | **5.4 GiB** |
| KV cache | 503K tok | **671K tok** |

**↑ 227% throughput · ↓ 70% latency**

---

## Footer

Three demo scenarios: API saturation · config drift · dependency failure

GitHub: `https://github.com/CacinieP/radeon-sentinel` · License: `Apache-2.0`

---

## Design notes

- Color: AMD red (#ED1C24) accents on dark background
- Font: sans-serif (Inter / Helvetica), ≥24pt for body, ≥48pt for headline
- Architecture diagram: boxes with rounded corners, GPU box highlighted red
- Results table: green checkmarks, bold the Int4 column
