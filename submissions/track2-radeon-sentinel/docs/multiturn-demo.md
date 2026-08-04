# Multi-Turn Interaction — Demo Script & Operations Manual

> 目的：坐实 Track 2 评分项 "Smooth multi-turn interaction experience" (20 分)。
> 通过同一 case_id 的连续 3 轮对话，演示**会话恢复、上下文引用、审批门控**。
>
> 全程在 Radeon Cloud 实例上用真实 GPU 模型跑（非 mock）。

---

## 前置准备（每次录视频前做一次）

```bash
# SSH 进实例
ssh -o BatchMode=yes -p 31011 root@36.150.116.206

# 进入项目 + 配置 GPU 模型
cd /workspace/radeon-sentinel
export PATH=/opt/venv/bin:$PATH
export SENTINEL_MODEL_MODE=openai
export MODEL_BASE_URL=http://127.0.0.1:8000/v1
export MODEL_NAME=Qwen2.5-7B-GPTQ-Int4
export MODEL_API_KEY=""

# 确认 vLLM 在线
curl -s http://127.0.0.1:8000/v1/models | head -3
# 应返回 {"data":[{"id":"Qwen2.5-7B-GPTQ-Int4",...}]}

# 清掉旧 memory（确保干净开始）
rm -f artifacts/sentinel.db
```

---

## 演示流程（3 轮对话，同一 case）

### Round 1 — 开案：首次诊断

```bash
python -m radeon_sentinel \
  --incident config_drift \
  --case-id demo-multiturn
```

**期望输出**（关键看点）：
- `Status: needs_approval`（审批门控生效）
- Diagnosis 里出现具体版本号：`2026.07.26-rc2` vs `2026.07.25`
- Plan 里 `Simulate remediation` 显示 `[approval_required]`
- Evidence 有 2-3 条带分数的 runbook 引用

**录制旁白**：
> "First turn: the agent investigates the config-drift incident, retrieves
> local runbooks, runs read-only diagnostics, and produces a cited diagnosis.
> The restart is gated behind explicit approval — status is needs_approval."

---

### Round 2 — 追问：测试上下文记忆

```bash
python -m radeon_sentinel \
  --incident config_drift \
  --case-id demo-multiturn \
  --query "Based on the config drift you found earlier, what specific version mismatch caused it and what evidence supports that?"
```

**期望输出**（关键看点）：
- ✅ 回答里**引用第一轮发现的版本号**（`2026.07.26-rc2` vs `2026.07.25`）
- ✅ 提到 `config version mismatch` 和 `checksum difference`（来自第一轮的日志检索）
- 这证明模型读到了历史上下文，不是重新推演

**录制旁白**：
> "Second turn: same case. The follow-up question asks about the specific
> version mismatch. Notice the answer references the exact versions from
> the first turn — the agent remembers prior context through local SQLite
> case memory."

---

### Round 3 — 深入：询问 remediation 后果

```bash
python -m radeon_sentinel \
  --incident config_drift \
  --case-id demo-multiturn \
  --query "If I approve the restart, what exactly will happen and what won't? Is it safe?"
```

**期望输出**（关键看点）：
- ✅ 解释 restart 是**模拟**，不会碰真实系统
- ✅ 引用前两轮的诊断上下文（degraded state, 21.7% error rate）
- 体现隐私和安全控制的一致性

**录制旁白**：
> "Third turn: asking about the consequences of approval. The agent confirms
> the restart is simulated and never touches a production system — consistent
> with the privacy-first design across all turns."

---

### 验证 — 展示持久化的 case memory

```bash
python -c "
from radeon_sentinel.memory import CaseMemory
m = CaseMemory('artifacts/sentinel.db')
h = m.history('demo-multiturn')
print(f'{len(h)} messages in case demo-multiturn:')
for r in h:
    print(f'  [{r[\"role\"]}] {str(r[\"content\"])[:70]}...')
events = m.audit('demo-multiturn')
print(f'{len(events)} audit events (tool calls, retrieval, approval gate)')
m.close()
"
```

**期望输出**：
```
6 messages in case demo-multiturn:
  [user] Investigate rejected jobs caused by config drift...
  [assistant] ### Diagnosis: Config drift has been identified...
  [user] Based on the config drift you found earlier...
  [assistant] ### Diagnosis: The specific version mismatch...
  [user] If I approve the restart, what exactly...
  [assistant] ### Diagnosis: The running configuration version...
15 audit events (tool calls, retrieval, approval gate)
```

**录制旁白**：
> "All three turns are persisted in local SQLite — six messages and fifteen
> audit events. The case memory is isolated per case ID and never leaves the
> local environment."

---

## 录制要点（给录屏者的 checklist）

- [ ] 终端字体 ≥ 16pt，深色背景
- [ ] 每轮开始前口播 "Round N"
- [ ] Round 2 时用鼠标/光标**高亮引用了第一轮版本号的那行**
- [ ] 最后一帧停在 memory 验证输出（6 messages + 15 events）
- [ ] 不要露出 SSH host/port、API key、邮箱
- [ ] 总时长控制在这一段约 60-90 秒（剪辑进主视频）

---

## 故障排除

| 现象 | 排查 |
|---|---|
| vLLM 返回 000 / 连不上 | `curl http://127.0.0.1:8000/v1/models` 确认在线；不在则重启 vLLM |
| 回答没引用历史 | 确认用了相同 `--case-id`；确认 `artifacts/sentinel.db` 存在且非空 |
| 第二轮报错 | 确认 `MODEL_NAME` 和 vLLM `--served-model-name` 一致 |
| 回答质量差 | Int4 模型偶有退化，重跑一次或换 float16（`MODEL_NAME=Qwen2.5-7B-Instruct`） |
