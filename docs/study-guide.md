# Study Guide

## Future Agent 首读路径

如果你是后续读取本仓库的 AI agent，先按问题选入口，不要把某个 repo 当默认第一站：

1. 先读本 `AGENTS.md`。
2. 如果目标是 generic workflow/runtime harness，先读 Archon、LangGraph、OpenHands、Cline 和 OpenHarness。
3. 如果目标是 Claude / Codex plugin-first harness，再读 cc10x；它不是 generic runtime 的默认首读。
4. 如果目标是 prompt/skill 层 workflow discipline，再读 Superpowers，并和 durable runtime reference 对照。
5. 继续读 OpenAI Agents SDK 的 guardrail、handoff、session、tracing、HITL 和 sandbox example。
6. 继续读 Microsoft Agent Framework 和 Mastra，学习 production 与 TypeScript workflow pattern。
7. 继续读 CrewAI 和 AutoGen，学习 multi-agent orchestration vocabulary。
8. 最后读 AutoGPT，学习 platform-scale workflow/product architecture。

generic workflow/runtime harness 的 P0 首读重点：

- Archon：YAML workflow DAG、artifact handoff、worktree isolation、workflow event 和 adapter boundary。
- LangGraph：state graph、conditional routing、checkpoint 和 interrupt implementation。
- OpenHands：conversation、event、action/observation、runtime 和 sandbox implementation。
- Cline：permission、diff、checkpoint、terminal、browser 和 restore flow。
- OpenHarness：agent loop、tool registry、skills/plugins、permissions/hooks、memory/compaction、sandbox 和 swarm coordination。

plugin-first harness 的 cc10x 阅读边界：

- 只在研究 Claude / Codex plugin-first harness 时提前阅读。
- 重点看 router-owned orchestration、workflow artifact、lifecycle event log、hook/gate boundary、replay validation、resume/compaction survival 和 anti-false-completion discipline。
- 不要把 cc10x 当成 generic workflow/runtime harness 的默认 baseline。

Do not 因为 AutoGPT star 多就先读它。star 可以作为 popularity signal，但 harness clarity 更重要。

## 阅读时必须回答的设计问题

每个 repo 都必须回答这些问题：

1. 谁拥有 orchestration？
2. workflow state 存在哪里？
3. transition 是 explicit 还是 implicit？
4. 记录了哪些 event？
5. risky action 前有哪些 gate？
6. gate 是否 fail closed？
7. tool call 如何 approve、reject、retry 或 rollback？
8. 系统如何在 agent、tool 或 workflow phase 之间 route？
9. 系统如何承受 long context、compaction、restart 或 crash？
10. 系统如何 prove completion？
11. human 如何 interrupt、approve、edit 或 reject work？
12. execution 如何 trace 和 debug？
13. 哪些 pattern 值得复制？
14. 哪些东西明确不应该复制？

## Harness Extraction Template

研究某个 repo 时，按这个格式产出 notes：

```text
Repo:
Harness dimension:
Files read:
Core mechanism:
Why it matters:
Could be copied into our own harness as:
Risks / anti-patterns:
Open questions:
```

## Submodule Update Policy

clone 后初始化本仓库：

```bash
git submodule update --init --recursive --depth 1
```

检查 submodule status：

```bash
git submodule status
```

只有在用户明确要求时，才允许把所有 submodule 更新到 tracked branch head：

```bash
git submodule foreach 'git pull --ff-only || true'
```

Do not 在普通分析中静默更新 submodule。submodule commit 是本仓库 reference snapshot 的一部分。

## Lightweight Inspection Commands

快速抽样 path：

```bash
find references/<repo> -maxdepth 3 -type f | sed 's#^#- #' | head -200
```

搜索 harness 相关内容：

```bash
rg -n "state|StateGraph|checkpoint|interrupt|guardrail|handoff|router|routing|event|workflow|permission|approval|sandbox|runtime|trace|tracing|gate|human" references/<repo> --glob '!**/node_modules/**' --glob '!**/.git/**' --glob '!**/dist/**' --glob '!**/build/**'
```

除非用户明确要求 upstream patch，否则 inspection 必须保持 read-only。
