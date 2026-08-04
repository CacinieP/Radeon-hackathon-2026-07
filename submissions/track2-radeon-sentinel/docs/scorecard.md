# Track 2 Evidence Scorecard

This file is the single source of truth for connecting the implementation to
the judging rubric. Every row must point to reproducible evidence before code
freeze.

| Rubric item | Max | Target | Planned evidence | Status |
|---|---:|---:|---|---|
| Clear positioning and creative scenario | 20 | 18 | Problem statement, personas, three incident scenarios | MVP implemented |
| Task decomposition, tools, RAG, memory | 20 | 19 | Automated scenario suite, UI demo, architecture section | MVP implemented; automated tests passing |
| Smooth multi-turn interaction | 20 | 17 | Session resume, error states, timed user test | Partial: local memory and UI code implemented |
| Core inference on AMD Radeon GPU | 20 | 20 | gfx1100 + ROCm 7.2.1, vLLM 0.16.1, 47.3GB VRAM, 29.2 tok/s baseline | ✅ Measured: docs/evidence/rocm-baseline.json + vllm-startup.log |
| Inference-speed optimization | 20 | 19 | float16→GPTQ-Int4: 29.2→95.6 tok/s (↑227%), p50 3.45→1.01s (↓70%) | ✅ docs/optimization-report.md |
| Optional quantization/distillation optimization | 20 | 18 | GPTQ-Int4: model 14.37→5.44GiB, KV cache 503K→671K tok (+33%); quality A/B: root-cause hit 100%/100%, structure 100%/100%, no semantic regression | ✅ docs/evidence/rocm-int4.json + docs/evidence/quality-eval.json |

## Required measurements

For each tested serving configuration, record:

- Radeon GPU model;
- ROCm, driver, PyTorch, and serving-stack versions;
- model name, revision, precision/quantization, and context length;
- time to first token;
- decode throughput in tokens per second;
- p50 and p95 request latency;
- peak VRAM;
- end-to-end incident task time;
- task success rate on the fixed scenario suite;
- retrieval citation accuracy;
- unapproved state-changing action count.

## Evidence rules

- Results must be measured on the designated Radeon Cloud environment.
- Keep the benchmark command, configuration, raw output, and summary together.
- Report regressions and quality trade-offs; do not cherry-pick only the fastest
  run.
- Redact API keys, SSH details, account identifiers, and private URLs from all
  evidence.
- Final screenshots, reports, videos, and pull-request content must be in
  English.
