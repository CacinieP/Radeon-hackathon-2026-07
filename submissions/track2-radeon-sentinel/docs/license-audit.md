# License & IP Audit Checklist

> Run before code freeze (8/4 22:00). Track 2 rules require an explicit
> license in the public repo and no uncleared third-party assets.

## 1. Project license

- [x] **Apache-2.0** selected for the project.
- [x] Full Apache License 2.0 text present at repository root.
- [x] `README.md` license section matches.
- [x] `SPECIFICATION.md` Section 6 records the project and model licenses.

## 2. Model license

- [x] **Qwen2.5-7B-Instruct** — Apache-2.0, verified on the official model card.
- [x] **Qwen2.5-7B-Instruct-GPTQ-Int4** — Apache-2.0, verified on the official
      Qwen quantized-model card.
- [x] Model licenses stated in the README license section.

## 3. Dependencies (all open source)

| Dependency | License | OK? |
|---|---|---|
| Python 3.10 | PSF License | ✅ |
| vLLM 0.16.1 | Apache-2.0 | ✅ |
| PyTorch 2.9.1 (ROCm) | BSD-3-Clause | ✅ |
| ROCm 7.2.1 | MIT/NCSA (AMD) | ✅ |
| Gradio (optional UI) | Apache-2.0 | ✅ |
| pytest | MIT | ✅ |

- [x] No proprietary / closed-source dependencies in the core path.
- [x] `pyproject.toml` lists all dependencies; no pinned private packages.

## 4. Data & assets

- [x] **All incident data is synthetic** (`demo_data/incidents/*.json`,
      `demo_data/runbooks/*.md`) — created for this project, no real data.
- [x] No third-party images, fonts, music, or video.
- [x] No scraped datasets.
- [x] Video reviewed: no account email, private key, API token, or private SSH
      endpoint is visible.

## 5. Secrets scan

- [x] Run `gitleaks dir . --redact` before push: no leaks found.
- [x] Run `gitleaks git . --redact` before push: no leaks found across 11
      commits.
- [x] Manual source-pattern scan performed:
  ```bash
  # scan git history for secrets
  grep -rniE "api[_-]?key|secret|password|token|ghp_|sk-" . --include="*.py" --include="*.md" --include="*.json" --include="*.sh" | grep -v ".env.example\|replace-me\|<.*>"
  ```
- [x] `.env.example` uses placeholders (`replace-me`), no real keys.
- [x] Model API key (if any) comes from environment variables at runtime,
      never committed.
- [x] Confirmed that Git history contains no committed secrets:
  ```bash
  git log -p | grep -iE "ghp_|sk-|API_KEY=" | head
  ```

## 6. Public repo readiness

- [ ] Create public fork of `AMD-DEV-CONTEST/Radeon-hackathon-2026-07`.
- [ ] Copy project files into the fork per the official README layout.
- [ ] Verify repo is readable in a **signed-out browser**.
- [ ] PR title: `Track 2, Yichun Deng, Radeon Sentinel`.
- [ ] PR description links to: spec doc, video, poster.

## Sign-off

- Auditor: ________ · Date: ________
- All items checked: [ ]
