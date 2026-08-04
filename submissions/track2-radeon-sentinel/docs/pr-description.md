## Radeon Sentinel — Private SRE Co-pilot on AMD Radeon

**Track:** Track 2 — Development & Local Deployment of Private AI Agents  
**Participant:** Yichun Deng (solo participant)  
**Application:** Radeon Sentinel

Radeon Sentinel is a private incident-response agent that investigates
synthetic service failures with local retrieval, allow-listed diagnostic tools,
multi-step planning, per-case memory, and a mandatory approval gate. Core model
inference runs locally on an AMD Radeon GPU through ROCm; logs and runbooks are
never sent to a remote model API.

## Submission materials

- [Project specification](docs/SPECIFICATION.md)
- [Source, setup, and reproduction guide](README.md)
- [Demo video — 4:45, 1080p](https://app.notion.com/p/linguistwantstech/radeon-sentinel-demo-3b234433b7e280db9777d470ba4cfed1?source=copy_link)
- [Poster](docs/poster.png)
- [ROCm optimization report](docs/optimization-report.md)
- [Raw benchmark evidence](docs/evidence/)

## AMD Radeon and ROCm evidence

- AMD Radeon `gfx1100` GPU with ROCm 7.2.1.
- Qwen2.5-7B-Instruct served locally through vLLM 0.16.1.
- GPTQ-Int4 optimization increased measured throughput from 29.2 to 95.6
  tokens/s (227%) and reduced p50 latency from 3.45 s to 1.01 s (70%) across
  20 matched requests.
- Quantized model memory decreased from 14.4 GiB to 5.4 GiB.
- Offline quality evaluation retained 100% root-cause hit rate and structure
  conformance on the frozen test set.

## Agent capabilities

1. Local runbook retrieval with source citations.
2. Default-deny, allow-listed diagnostic tools.
3. Multi-step investigation and response planning.
4. Local SQLite case memory and audit trail.
5. Explicit approval before every state-changing action.

All incidents and operational data in this submission are synthetic. The
remediation tool is a simulation and never touches a production system.
