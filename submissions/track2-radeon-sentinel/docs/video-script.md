# Radeon Sentinel — Demo Video Script (3–5 min)

**Track 2 · AMD AI DevMaster Hackathon**
**Language**: English (required) · **Duration target**: 4:00

Recording location: Radeon Cloud instance `u-13978-7e53ceb9` (gfx1100 / ROCm 7.2.1),
captured via SSH terminal + browser JupyterLab.

> Recording notes in `[brackets]` are cues for the presenter, not spoken.

---

## Segment 1 — Problem & Setup (0:00–0:45)

**[Screen: terminal, SSH into the Radeon Cloud instance]**

> "This is Radeon Sentinel — a private SRE co-pilot.
>
> When production breaks, on-call engineers either investigate manually, or
> paste sensitive logs into a cloud chatbot. Radeon Sentinel offers a third
> way: it investigates incidents using a model that runs entirely on a local
> AMD Radeon GPU.
>
> **Logs never leave the laptop.**
>
> Let me show you how it works."

**[Cue: run `rocm-smi` — show the GPU]**

> "We're running on an AMD Radeon gfx1100 GPU with 48 gigabytes of VRAM,
> ROCm 7.2.1, and PyTorch 2.9.1. The model is served by vLLM 0.16.1."

**[Show: `rocm-smi` output with VRAM 91% used — proves model is loaded on GPU]**

---

## Segment 2 — Core Inference on AMD Radeon GPU (0:45–1:30)

**[Screen: vLLM startup log excerpt]**

> "Let's confirm core inference is executing on the Radeon GPU."

**[Cue: show vllm.log key lines]**

> "vLLM loaded Qwen2.5-7B-Instruct in 7 seconds, using the Triton Attention
> backend — that's ROCm-native, not the CUDA-only FlashAttention. The model
> occupies 14.4 gigabytes of VRAM, with a KV cache of over 500,000 tokens."

**[Cue: run a live inference request]**

```bash
curl -s http://127.0.0.1:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"Qwen2.5-7B-Instruct","messages":[{"role":"user","content":"Checkout service API latency is spiking. One-line diagnosis and safest next action."}],"max_tokens":100}'
```

**[Show: response returns in ~3.5s, GPU activity spikes in rocm-smi]**

> "A real diagnosis, returned in three and a half seconds, computed entirely
> on the Radeon GPU."

---

## Segment 3 — Agent Workflow: Incident Investigation (1:30–3:00)

**[Screen: Radeon Sentinel CLI]**

> "Now the full agent workflow. We trigger a synthetic incident — API
> latency on the checkout service."

**[Cue: run the CLI]**

```bash
python -m radeon_sentinel --incident api_latency
```

**[Narrate the output as it appears]**

> "The agent first retrieves relevant runbooks from local storage — with
> source citations. Then it runs **allow-listed, read-only diagnostic tools**:
> inspecting service health, and searching incident logs.

> "Based on the evidence and tool results, the local model produces a cited
> diagnosis. Finally, it builds a **step-by-step response plan** with
> per-step status."

**[Cue: show the plan output with completed/needs_approval statuses]**

---

## Segment 4 — Permission Gate & Safety (3:00–3:45)

**[Screen: the config_drift scenario]**

> "Safety is enforced, not advisory. Watch the permission gate."

**[Cue: run WITHOUT approval]**

```bash
python -m radeon_sentinel --incident config_drift
```

> "For the config-drift incident, the agent proposes a simulated service
> restart. But without explicit approval, the status is **approval_required**
> — and **zero** state-changing actions execute."

**[Cue: point to the `needs_approval` status in the plan]**

**[Cue: run WITH approval]**

```bash
python -m radeon_sentinel --incident config_drift --approve-remediation
```

> "Only with the `--approve-remediation` flag does the simulated restart
> proceed — and it's still a simulation, never touching a real system. The
> entire case is logged to a local SQLite audit trail."

---

## Segment 5 — Inference Optimization (3:45–4:30)

**[Screen: optimization report table]**

> "Track 2 rewards inference optimization. We ran a measured A/B comparison."

**[Cue: show the comparison table]**

> "Baseline: Qwen2.5-7B in float16 — 29 tokens per second, p50 latency of
> 3.45 seconds.
>
> Optimized: the same model in **GPTQ-Int4** — **96 tokens per second**, a
> 227 percent improvement. Latency drops to one second.
>
> And the 9 gigabytes freed by quantization go to the KV cache — raising the
> token budget by 33 percent, from 503K to 671K tokens. That means more
> concurrent sessions for the agent's multi-turn memory."

**[Cue: show vllm-int4-startup.log line: "GPU KV cache size: 671,152 tokens"]**

---

## Segment 6 — Summary (4:30–4:45)

> "Radeon Sentinel: a privacy-first incident-response agent with all five
> Track 2 capabilities — local RAG, allow-listed tools, multi-step planning,
> multi-turn memory, and explicit permission control — running core inference
> on an AMD Radeon GPU, optimized to 96 tokens per second with GPTQ
> quantization."

**[Screen: project name + GitHub repo URL + AMD DevMaster branding]**

---

## Recording Checklist

- [ ] Terminal font large enough (≥16pt)
- [ ] `rocm-smi` visible during inference (GPU proof)
- [ ] No API keys / SSH endpoints / private URLs in any frame
- [ ] All on-screen text in English
- [ ] Total duration 3–5 min (target 4:30)
- [ ] Export 1080p, upload unlisted YouTube or submit as repo asset
