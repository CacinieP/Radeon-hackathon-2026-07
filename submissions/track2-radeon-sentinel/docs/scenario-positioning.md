# Scenario Positioning — Reframing for Maximum Score

> 目标：提升 Track 2 评分项 "Clear task positioning and creative application
> scenarios" (20 分)。
>
> 问题：当前定位"运维事件响应 Agent"准确但**记忆点不足**——评委看 30+ 个
> 项目，"又一个 devops agent"容易被淹没。
>
> 解法：不改变代码，**重新包装定位话术**，把 Track 2 的关键词（Private /
> Local / Privacy）提到最前面，给项目一个评委能记住的"一句话钩子"。

---

## 当前定位 vs 新定位

### 旧定位（准确但平淡）
> "A privacy-first incident-response agent for on-call engineers."

问题：
- "incident-response" 是成熟品类，评委会觉得"见过"
- "on-call engineers" 用户面窄，不够普世
- "privacy-first" 藏在修饰语里，没当卖点

### 新定位（钩子 + 差异化）

**一句话钩子**：
> **"Logs never leave the laptop."**
> Radeon Sentinel is a **private SRE co-pilot** that investigates incidents
> entirely on a local AMD Radeon GPU — your logs, configs, and runbooks stay
> on your machine, never sent to any cloud API.

**三个记忆点**（评委能复述给别人的程度）：

1. **"Logs never leave the laptop"** — 隐私是 Track 2 的核心关键词，把它
   变成口号。核心推理在本地 Radeon GPU，不是调远程 API。

2. **"Co-pilot, not autopilot"** — 强调**人机协作**而非全自动。每个
   state-changing action 都要人工批准，agent 只做调查和建议。这是和
   "全自动 agent"对手的差异化。

3. **"Three scenarios, zero unapproved actions"** — 量化安全证据。三个
   场景（API 饱和 / 配置漂移 / 依赖故障）里，**未经批准的变更执行数 = 0**。

---

## 重写后的场景描述（用于 SPECIFICATION + 视频 + 海报）

### Problem（视频 Segment 1 用）

> "When production breaks at 3 AM, the on-call engineer's first 15 minutes
> decide everything. They repeat the same steps: find the right runbook,
> read the right logs, decide if a restart is safe. Today they either do
> this manually, or paste sensitive logs into a cloud chatbot — exposing
> infrastructure secrets to a third party.
>
> Radeon Sentinel is a third option: a **private SRE co-pilot**. It runs the
> investigation **entirely on a local AMD Radeon GPU**. Your logs, service
> configs, and runbooks are read by a model that lives on your machine.
> Logs never leave the laptop."

### Why Track 2（强调合规契合）

> "Track 2 asks for private, locally-deployed agents. Radeon Sentinel is
> architected around exactly that constraint: core inference on AMD Radeon
> GPU via ROCm, no remote APIs for core functions, and a default-deny tool
> policy that makes the agent safe to give real diagnostic access."

### Differentiator table（海报/PR 用）

| | Cloud chatbot | Auto-pilot agent | **Radeon Sentinel** |
|---|---|---|---|
| Logs leave your machine | ❌ yes | depends | ✅ **never** |
| Core inference | remote API | varies | **local Radeon GPU** |
| State-changing actions | n/a | auto-executed | **human-gated** |
| Audit trail | none | partial | **full, per-case SQLite** |
| Unapproved actions executed | — | possible | **0** |

---

## 具体替换清单（改哪些文件的哪段）

| 文件 | 改什么 |
|---|---|
| `README.md` 第一段 | 把 "privacy-first incident-response agent" 换成新定位 + 钩子句 |
| `docs/SPECIFICATION.md` §1 | 替换 Problem 段为新版（3 AM + logs never leave） |
| `docs/SPECIFICATION.md` §3 表头 | 加 "Co-pilot, not autopilot" 说明 |
| `docs/video-script.md` Segment 1 旁白 | 换新 Problem 段 |
| `docs/poster-copy.md` 左栏 | 换新 Problem + differentiator table |
| PR 描述正文第一段 | 用钩子句开头 |

> 代码不用改——这是纯定位/话术调整，让同样的功能在评委眼里更亮。

---

## 视频开场话术（最终版，直接用）

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

（停顿 1 秒，切到终端）

---

## 为什么这个定位更能拿分

1. **贴 Track 2 原文关键词**：规则反复强调 "Private / Local deployment /
   privacy protection"，新定位把这些词变成核心卖点而非修饰语。

2. **"Logs never leave the laptop" 是可验证的**：不是空话——核心推理确实在
   本地 Radeon GPU，有 rocm-smi + vLLM 日志铁证。评委信服。

3. **有明确的"对手"**（cloud chatbot + auto-pilot agent），对比表让差异化
   一目了然。评委看完能一句话复述这个项目和别人有什么不同。

4. **量化安全**（"zero unapproved actions"）给了信任感，这是 agent 类项目
   最容易被评委质疑的点。
