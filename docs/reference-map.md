# Reference Map

## Priority 模型

priority 先看 harness-engineering value，再看 GitHub popularity。

- **P0**: core harness reference。按研究目标选择首读，不把单个 repo 当固定第一。
- **P1**: 强 production primitive 或 language-specific pattern。
- **P2**: 有用的 secondary reference、historical system 或较窄的 orchestration pattern。
- **P3**: star 很高或 platform-scale，但噪音也较大的系统。

## Priority 快速表

| Priority | Repo | Path | 主要学习点 |
| --- | --- | --- | --- |
| P0 | Archon | `references/archon` | YAML workflow DAG、deterministic/AI node composition、artifact handoff、worktree isolation、approval gate、workflow event log |
| P0 | LangGraph | `references/langgraph` | 显式 state graph、conditional routing、durable state、checkpoint、interrupt |
| P0 | OpenHands | `references/openhands` | conversation/event service、sandbox service、coding-agent platform runtime、action/observation feedback surface |
| P0 | Cline | `references/cline` | IDE-native tool safety、permission gate、diff review、terminal/browser feedback、checkpoint、rollback |
| P0 | OpenHarness | `references/openharness` | Python agent loop、tool registry、skills/plugins、permissions/hooks、memory/compaction、sandbox、swarm coordination |
| P0 | cc10x | `references/cc10x` | Claude Code plugin-first harness、router-owned orchestration、fail-closed gate、workflow artifact、event log |
| P1 | OpenAI Agents SDK Python | `references/openai-agents-python` | guardrail、handoff、session、tracing、human-in-the-loop primitive、sandbox agent |
| P1 | Microsoft Agent Framework | `references/microsoft-agent-framework` | production workflow orchestration、graph pattern、durability、restartability、observability、HITL |
| P1 | Mastra | `references/mastra` | TypeScript agent/workflow framework、model routing、branch/parallel workflow、durable agent execution state |
| P1 | Superpowers | `references/superpowers` | skill-driven workflow discipline、session bootstrap、TDD/process gate、subagent review loop、skill eval |
| P2 | CrewAI | `references/crewai` | Crew 与 Flow 的边界、event-driven orchestration、task/agent collaboration、checkpoint/event state |
| P2 | AutoGen | `references/autogen` | multi-agent message routing、actor-style runtime、agent communication；当前 repo 已将 Microsoft Agent Framework 指向 successor，因此主要作为 historical reference |
| P3 | AutoGPT | `references/autogpt` | 大型 high-star agent platform、workflow builder/platform architecture、block、trigger、continuous agent；有价值但噪音较大 |

---

# 按 Repo 的 Study Map

## P0 - cc10x

路径：`references/cc10x`

### 为什么这个 repo 重要

cc10x 是本仓库的 Claude Code plugin-first baseline reference。它仍是 P0，但不再是 generic harness runtime 的默认第一参考。

如果目标是设计通用 workflow/runtime harness，优先从 Archon、LangGraph、OpenHands、Cline 和 OpenHarness 读起；如果目标是设计 Claude / Codex plugin-first harness，再把 cc10x 提前到第一批。

它最值得研究的核心设计是 **router-owned orchestration**：

- router 拥有 workflow advancement
- specialist agent 不拥有 global workflow state
- workflow state 存在 durable artifact 中
- lifecycle event 追加写入 JSONL log
- hook 负责 guardrail 和 diagnostics，不是第二套 orchestration plane
- gate 设计为 fail closed
- 系统要求 evidence 才能推进 phase，降低 false completion 风险

### 优先阅读

- `README.md`
- `CLAUDE.md`
- `docs/cc10x-orchestration-safety.md`
- `docs/cc10x-orchestration-logic-analysis.md`
- `docs/cc10x-orchestration-bible.md`
- `docs/router-invariants.md`
- `docs/prompt-invariants.md`
- `docs/agent-contract-registry.md`
- `plugins/cc10x/.claude-plugin/plugin.json`
- `plugins/cc10x/config/hook-mode.json`
- `plugins/cc10x/hooks/hooks.json`
- `plugins/cc10x/hooks/README.md`
- `plugins/cc10x/skills/cc10x-router/SKILL.md`
- `plugins/cc10x/skills/cc10x-router/references/`
- `plugins/cc10x/skills/session-memory/SKILL.md`
- `plugins/cc10x/skills/plan-review-gate/SKILL.md`
- `plugins/cc10x/skills/verification-before-completion/SKILL.md`
- `plugins/cc10x/agents/`
- `plugins/cc10x/scripts/cc10x_hooklib.py`
- `plugins/cc10x/scripts/cc10x_pretooluse_guard.py`
- `plugins/cc10x/scripts/cc10x_posttooluse_artifact_guard.py`
- `plugins/cc10x/scripts/cc10x_sessionstart_context.py`
- `plugins/cc10x/scripts/cc10x_precompact_state.py`
- `plugins/cc10x/scripts/cc10x_postcompact_context.py`
- `plugins/cc10x/scripts/cc10x_stop_persist.py`
- `plugins/cc10x/scripts/cc10x_stop_failure_log.py`
- `plugins/cc10x/scripts/cc10x_task_completed_guard.py`
- `plugins/cc10x/scripts/cc10x_workflow_replay_check.py`
- `plugins/cc10x/scripts/cc10x_harness_audit.py`
- `plugins/cc10x/tests/fixtures/`

Search:

```bash
rg -n "router|orchestration|gate|fail-closed|workflow|events.jsonl|artifact|hook|remediation|verifier|memory|compaction" references/cc10x
```

### 需要提取什么

- Router supremacy invariant
- Workflow artifact schema
- Event log design
- Agent contract format
- Phase gate rules
- Remediation loop constraints
- Resume 和 compaction survival strategy
- Hook boundary: guardrail, not orchestrator

### Harness lesson

cc10x 说明：agent harness 不能只依赖 prompt instruction。它需要 durable state、explicit phase rule 和 evidence gate，让模型更难在没有 proof 的情况下声称完成。

## P0 - LangGraph

路径：`references/langgraph`

### 为什么这个 repo 重要

LangGraph 是研究 **explicit state-machine orchestration** 最清晰的 reference。

它的价值在于不把 control flow 藏在 chat history 里。它迫使 system designer 把 execution 建模为 state、node、edge、conditional routing、checkpointed execution 和 resumable interruption。

### 优先阅读

- `README.md`
- `AGENTS.md`
- `CLAUDE.md`
- `docs/llms.txt`
- `docs/redirects.json`
- `libs/langgraph/README.md`
- `libs/langgraph/langgraph/graph/state.py`
- `libs/langgraph/langgraph/graph/_branch.py`
- `libs/langgraph/langgraph/graph/_node.py`
- `libs/langgraph/langgraph/types.py`
- `libs/langgraph/langgraph/runtime.py`
- `libs/langgraph/langgraph/pregel/main.py`
- `libs/langgraph/langgraph/pregel/_loop.py`
- `libs/langgraph/langgraph/pregel/_runner.py`
- `libs/langgraph/langgraph/pregel/_checkpoint.py`
- `libs/langgraph/langgraph/pregel/_algo.py`
- `libs/langgraph/langgraph/pregel/debug.py`
- `libs/langgraph/langgraph/stream/`
- `libs/checkpoint/README.md`
- `libs/checkpoint/langgraph/checkpoint/base/__init__.py`
- `libs/checkpoint/langgraph/checkpoint/memory/__init__.py`
- `libs/checkpoint/langgraph/checkpoint/serde/`
- `libs/checkpoint-postgres/README.md`
- `libs/checkpoint-postgres/langgraph/checkpoint/postgres/base.py`
- `libs/checkpoint-sqlite/README.md`
- `libs/checkpoint-sqlite/langgraph/checkpoint/sqlite/__init__.py`
- `examples/human_in_the_loop/wait-user-input.ipynb`
- `examples/multi_agent/`
- `examples/subgraph.ipynb`

Search:

```bash
rg -n "StateGraph|conditional|checkpoint|interrupt|Command|edge|node|route|routing|durable|state" references/langgraph
```

### 需要提取什么

- state 如何表示
- node 如何 read/write state
- conditional edge 如何 route execution
- loop 如何 bound 或 control
- checkpoint 如何保存
- interrupt 和 resume 如何实现
- graph execution 与 linear agent loop 的差异

### Harness lesson

LangGraph 说明：严肃的 harness 应该把 workflow control 建模为 inspectable graph，而不是隐藏的 model behavior。

## P0 - OpenHands

路径：`references/openhands`

### 为什么这个 repo 重要

OpenHands 是 full coding-agent platform 的强 reference。

这个 checkout 里，platform harness 最清楚地体现在 conversation service、event service、sandbox service、frontend action/observation model 和 analytics 中。Do not 假设旧版本的 `openhands/core` path 在当前 snapshot 存在。

### 优先阅读

- `README.md`
- `AGENTS.md`
- `config.template.toml`
- `openhands/app_server/README.md`
- `openhands/app_server/app.py`
- `openhands/app_server/v1_router.py`
- `openhands/app_server/types.py`
- `openhands/app_server/app_conversation/app_conversation_service.py`
- `openhands/app_server/app_conversation/live_status_app_conversation_service.py`
- `openhands/app_server/app_conversation/app_conversation_router.py`
- `openhands/app_server/event/README.md`
- `openhands/app_server/event/event_service.py`
- `openhands/app_server/event/event_store.py`
- `openhands/app_server/event/filesystem_event_service.py`
- `openhands/app_server/event/aws_event_service.py`
- `openhands/app_server/event/google_cloud_event_service.py`
- `openhands/app_server/event/event_router.py`
- `openhands/app_server/event_callback/`
- `openhands/app_server/sandbox/README.md`
- `openhands/app_server/sandbox/sandbox_models.py`
- `openhands/app_server/sandbox/sandbox_service.py`
- `openhands/app_server/sandbox/sandbox_router.py`
- `openhands/app_server/sandbox/docker_sandbox_service.py`
- `openhands/app_server/sandbox/process_sandbox_service.py`
- `openhands/app_server/sandbox/remote_sandbox_service.py`
- `openhands/app_server/sandbox/session_auth.py`
- `openhands/analytics/EVENTS.md`
- `openhands/analytics/analytics_service.py`
- `frontend/src/services/actions.ts`
- `frontend/src/services/observations.ts`
- `frontend/src/message.d.ts`
- `frontend/src/mocks/mock-ws-helpers.ts`

Search:

```bash
rg -n "Event|Action|Observation|Runtime|Sandbox|Controller|State|Pause|Reject|UserReject|event stream|trajectory|memory" references/openhands
```

### 需要提取什么

- Event envelope shape
- Action 与 observation separation
- Runtime/sandbox boundary
- Agent-controller 或 conversation-service interaction
- user rejection 作为 gate 的位置，如果存在
- Persistence model
- execution history 如何变得 inspectable

### Harness lesson

OpenHands 说明：coding agent 需要 runtime，而不只是 prompt。tool action、observation、sandbox execution 和 state transition 都必须是系统中的 explicit part。

## P0 - Cline

路径：`references/cline`

### 为什么这个 repo 重要

Cline 是研究 **real IDE 内 safe tool execution** 的强 reference。

它把 permission gate、auto-approval、diff review、terminal/browser feedback、checkpoint、restore 和 user-facing recovery 这些困难的 harness 问题 productize 了。

### 优先阅读

- `README.md`
- `CHANGELOG.md`
- `proto/cline/state.proto`
- `proto/cline/task.proto`
- `proto/cline/checkpoints.proto`
- `proto/cline/browser.proto`
- `proto/cline/hooks.proto`
- `proto/host/diff.proto`
- `proto/host/workspace.proto`
- `docs/core-workflows/checkpoints.mdx`
- `docs/core-workflows/working-with-files.mdx`
- `docs/core-workflows/task-management.mdx`
- `docs/features/auto-approve.mdx`
- `docs/features/web-tools.mdx`
- `docs/features/worktrees.mdx`
- `docs/features/auto-compact.mdx`
- `docs/tools-reference/browser-automation.mdx`
- `src/core/assistant-message/diff.ts`
- `src/core/permissions/CommandPermissionController.ts`
- `src/core/permissions/types.ts`
- `src/core/task/TaskState.ts`
- `src/core/task/TaskPresentationScheduler.ts`
- `src/core/task/tools/ToolExecutorCoordinator.ts`
- `src/core/task/tools/ToolValidator.ts`
- `src/core/task/tools/autoApprove.ts`
- `src/core/task/tools/handlers/ExecuteCommandToolHandler.ts`
- `src/core/task/tools/handlers/BrowserToolHandler.ts`
- `src/core/task/tools/handlers/WriteToFileToolHandler.ts`
- `src/core/task/tools/handlers/ApplyPatchHandler.ts`
- `src/core/prompts/system-prompt/tools/`
- `src/core/controller/checkpoints/subscribeToCheckpoints.ts`
- `src/core/controller/checkpoints/checkpointDiff.ts`
- `src/core/controller/checkpoints/checkpointRestore.ts`
- `src/integrations/checkpoints/MultiRootCheckpointManager.ts`
- `src/integrations/checkpoints/CheckpointGitOperations.ts`
- `src/integrations/checkpoints/CheckpointTracker.ts`
- `src/integrations/checkpoints/CheckpointUtils.ts`
- `src/integrations/terminal/CommandExecutor.ts`
- `src/integrations/terminal/CommandOrchestrator.ts`
- `src/integrations/editor/DiffViewProvider.ts`
- `src/hosts/vscode/VscodeDiffViewProvider.ts`
- `src/hosts/vscode/terminal/VscodeTerminalManager.ts`
- `src/hosts/vscode/terminal/VscodeTerminalProcess.ts`
- `src/hosts/vscode/hostbridge/diff/`
- `src/services/browser/BrowserSession.ts`
- `src/services/browser/BrowserDiscovery.ts`
- `src/services/browser/UrlContentFetcher.ts`
- `webview-ui/src/components/chat/DiffEditRow.tsx`
- `webview-ui/src/components/common/CheckpointControls.tsx`
- `webview-ui/src/components/chat/task-header/CheckpointError.tsx`
- `cli/src/agent/permissionHandler.ts`
- `cli/src/components/DiffView.tsx`
- `cli/src/components/CheckpointMenu.tsx`
- `cli/src/acp/ACPDiffViewProvider.ts`
- `cli/src/acp/AcpTerminalManager.ts`

Search:

```bash
rg -n "checkpoint|restore|permission|approval|askApproval|diff|revert|terminal|browser|tool|tool use|auto approve|Task|execute" references/cline
```

### 需要提取什么

- Permission gate model
- Tool risk classification
- File edit diff workflow
- User edit/revert workflow
- Terminal feedback loop
- Browser feedback loop
- Checkpoint 和 restore model
- UI 如何成为 harness 的一部分

### Harness lesson

Cline 说明：让 agent 安全触碰真实 developer workspace 的关键，不只是“agent 能改文件”；关键是 risky action 必须有 review surface、feedback channel 和 recovery path。

## P0 - Archon

路径：`references/archon`

### 为什么这个 repo 重要

Archon 是研究 **AI coding workflow harness builder** 的强 reference。

它把 coding-agent 工作流做成 repo-local YAML DAG：workflow 定义 phases、dependencies、loops、deterministic script node、AI node、human approval gate 和 artifact flow。它还把 workflow run 放进 git worktree isolation，并通过 core database、workflow events、adapters 和 Web UI 让 execution 可以被监控、恢复和审计。

重点看：谁拥有 workflow execution，YAML node graph 如何约束 agent 行为，artifact 如何替代 chat-history handoff，loop / retry / validation 如何 fail closed，CLI/Web/Slack/Telegram/GitHub adapter 如何接入同一个 orchestrator。

### 优先阅读

- `README.md`
- `CLAUDE.md`
- `.archon/config.yaml`
- `.archon/workflows/defaults/archon-idea-to-pr.yaml`
- `.archon/workflows/defaults/archon-fix-github-issue.yaml`
- `.archon/workflows/defaults/archon-plan-to-pr.yaml`
- `.archon/workflows/defaults/archon-smart-pr-review.yaml`
- `.archon/workflows/defaults/archon-comprehensive-pr-review.yaml`
- `.archon/workflows/defaults/archon-validate-pr.yaml`
- `.archon/workflows/defaults/archon-resolve-conflicts.yaml`
- `.archon/workflows/defaults/archon-piv-loop.yaml`
- `.archon/commands/`
- `.claude/docs/workflow-yaml-reference.md`
- `.claude/docs/architecture-deep-dive.md`
- `.claude/docs/isolation-and-worktree-guide.md`
- `packages/workflows/src/schemas/workflow.ts`
- `packages/workflows/src/schemas/dag-node.ts`
- `packages/workflows/src/schemas/loop.ts`
- `packages/workflows/src/loader.ts`
- `packages/workflows/src/dag-executor.ts`
- `packages/workflows/src/executor.ts`
- `packages/workflows/src/event-emitter.ts`
- `packages/workflows/src/router.ts`
- `packages/workflows/src/validator.ts`
- `packages/workflows/src/workflow-discovery.ts`
- `packages/core/src/orchestrator/orchestrator.ts`
- `packages/core/src/orchestrator/orchestrator-agent.ts`
- `packages/core/src/operations/workflow-operations.ts`
- `packages/core/src/db/workflows.ts`
- `packages/core/src/db/workflow-events.ts`
- `packages/core/src/db/sessions.ts`
- `packages/core/src/db/messages.ts`
- `packages/isolation/src/factory.ts`
- `packages/isolation/src/providers/worktree.ts`
- `packages/isolation/src/resolver.ts`
- `packages/isolation/src/worktree-copy.ts`
- `packages/adapters/src/forge/github/adapter.ts`
- `packages/adapters/src/chat/slack/adapter.ts`
- `packages/adapters/src/chat/telegram/adapter.ts`
- `packages/server/src/adapters/web/workflow-bridge.ts`
- `packages/server/src/routes/api.workflow-runs.test.ts`
- `packages/workflows/src/*.test.ts`

Search:

```bash
rg -n "workflow|DAG|node|depends_on|loop|retry|artifact|approval|interactive|worktree|isolation|event|jsonl|session|adapter|orchestrator|router|validate|fresh" references/archon
```

### 需要提取什么

- YAML workflow schema 与 node graph model
- Node dependency、parallel layer 和 convergence 规则
- Deterministic node 与 AI node 的边界
- Loop / retry / until / max-iteration 如何限制 agent wandering
- Artifact handoff 如何替代隐式 chat context
- Fresh context session 如何强制节点只依赖 artifacts
- Human approval / interactive gate 的位置
- Worktree isolation lifecycle
- Workflow run、session、message 和 event persistence model
- CLI/Web/chat/forge adapter 如何接入同一个 orchestrator
- Workflow validation 与 resource validation 的 fail-closed 边界

### Harness lesson

Archon 说明：AI coding harness 可以像 CI/CD 一样被声明成 repo-local workflow。关键不是让模型更自由，而是让流程、依赖、验证、artifact 和 isolation 由 harness 拥有；AI 只在需要判断和生成的节点内工作。

## P0 - OpenHarness

路径：`references/openharness`

### 为什么这个 repo 重要

OpenHarness 是研究 **lightweight agent harness runtime** 的强 reference。

它用 Python 实现 agent loop、provider API、tool registry、skills/plugins、MCP client、permission checker、PreToolUse/PostToolUse hook、persistent memory、auto-compaction、sandbox、swarm coordination、TUI runtime 和 channel bridge。相比 Archon 的 repo-local workflow DAG，OpenHarness 更适合研究一个完整 agent runtime 内部怎样把 tool、context、governance 和 multi-agent coordination 串起来。

重点看：agent loop 如何处理 streaming tool-call cycle，tool 执行前后如何挂 permissions/hooks，skills/plugins 如何被发现和加载，memory/compaction 如何支撑 long session，swarm/team/task 如何落地多代理协作，ohmo gateway 如何把 chat channel 接入同一个 harness。

### 优先阅读

- `README.md`
- `README.zh-CN.md`
- `pyproject.toml`
- `src/openharness/engine/query_engine.py`
- `src/openharness/engine/stream_events.py`
- `src/openharness/engine/messages.py`
- `src/openharness/tools/base.py`
- `src/openharness/tools/bash_tool.py`
- `src/openharness/tools/file_edit_tool.py`
- `src/openharness/tools/mcp_tool.py`
- `src/openharness/tools/task_create_tool.py`
- `src/openharness/tools/team_create_tool.py`
- `src/openharness/permissions/checker.py`
- `src/openharness/permissions/modes.py`
- `src/openharness/hooks/executor.py`
- `src/openharness/hooks/loader.py`
- `src/openharness/hooks/schemas.py`
- `src/openharness/skills/loader.py`
- `src/openharness/skills/registry.py`
- `src/openharness/plugins/loader.py`
- `src/openharness/plugins/schemas.py`
- `src/openharness/mcp/client.py`
- `src/openharness/mcp/config.py`
- `src/openharness/memory/manager.py`
- `src/openharness/memory/memdir.py`
- `src/openharness/services/compact/__init__.py`
- `src/openharness/services/session_storage.py`
- `src/openharness/sandbox/adapter.py`
- `src/openharness/sandbox/docker_backend.py`
- `src/openharness/sandbox/path_validator.py`
- `src/openharness/swarm/registry.py`
- `src/openharness/swarm/team_lifecycle.py`
- `src/openharness/swarm/subprocess_backend.py`
- `src/openharness/swarm/worktree.py`
- `src/openharness/tasks/manager.py`
- `src/openharness/ui/runtime.py`
- `src/openharness/ui/permission_dialog.py`
- `src/openharness/bridge/session_runner.py`
- `src/openharness/autopilot/service.py`
- `ohmo/runtime.py`
- `ohmo/gateway/service.py`
- `ohmo/gateway/router.py`
- `ohmo/session_storage.py`
- `tests/test_engine/`
- `tests/test_tools/`
- `tests/test_permissions/`
- `tests/test_hooks/`
- `tests/test_skills/`
- `tests/test_plugins/`
- `tests/test_sandbox/`
- `tests/test_swarm/`
- `tests/test_services/`

Search:

```bash
rg -n "query|stream|tool_use|ToolResult|Permission|Hook|skill|plugin|MCP|memory|compact|sandbox|swarm|team|task|session|gateway|autopilot|approval" references/openharness/src references/openharness/ohmo references/openharness/tests references/openharness/README.md
```

### 需要提取什么

- Agent loop 的 streaming/tool-call/control flow
- Tool registry、tool input schema 和 tool result contract
- Permission modes、path rules、interactive dialog 和 skip-permission 语义
- PreToolUse / PostToolUse hook lifecycle
- Skill 与 plugin discovery、loading 和 trust boundary
- MCP client 和 MCP tool bridge
- Persistent memory、session storage 和 auto-compaction
- Sandbox adapter、Docker backend 和 path validator
- Swarm/team/task lifecycle、mailbox、worktree 和 permission sync
- TUI/runtime 与 channel/gateway 如何接入 harness core
- ohmo 如何把 personal agent、chat channel、repo work 和 PR flow 组合起来

### Harness lesson

OpenHarness 说明：agent harness 可以被拆成一组轻量但完整的 runtime primitives：agent loop、tools、skills、memory、permissions、hooks、sandbox 和 swarm coordination。它适合作为“如果从零实现一个 Python harness，需要哪些模块”的代码级 reference。

## P1 - OpenAI Agents SDK Python

路径：`references/openai-agents-python`

### 为什么这个 repo 重要

OpenAI Agents SDK 的 primitive 相比大型 platform 更小、更清晰，因此很适合研究。

重点看 guardrail、handoff、session、tracing、human-in-the-loop、sandbox agent，以及 tool 如何作为 controlled execution surface。

### 优先阅读

- `README.md`
- `AGENTS.md`
- `mkdocs.yml`
- `src/agents/run.py`
- `src/agents/run_state.py`
- `src/agents/run_context.py`
- `src/agents/run_config.py`
- `src/agents/run_internal/run_loop.py`
- `src/agents/run_internal/turn_preparation.py`
- `src/agents/run_internal/turn_resolution.py`
- `src/agents/run_internal/tool_execution.py`
- `src/agents/run_internal/tool_planning.py`
- `src/agents/run_internal/approvals.py`
- `src/agents/run_internal/guardrails.py`
- `src/agents/run_internal/session_persistence.py`
- `src/agents/run_internal/run_steps.py`
- `src/agents/run_internal/items.py`
- `src/agents/guardrail.py`
- `src/agents/tool_guardrails.py`
- `src/agents/handoffs/__init__.py`
- `src/agents/handoffs/history.py`
- `src/agents/memory/session.py`
- `src/agents/memory/sqlite_session.py`
- `src/agents/memory/openai_responses_compaction_session.py`
- `src/agents/memory/openai_conversations_session.py`
- `src/agents/tracing/`
- `src/agents/tool.py`
- `src/agents/tool_context.py`
- `src/agents/agent_tool_state.py`
- `src/agents/sandbox/sandbox_agent.py`
- `src/agents/sandbox/runtime.py`
- `src/agents/sandbox/runtime_session_manager.py`
- `src/agents/sandbox/manifest.py`
- `src/agents/sandbox/snapshot.py`
- `src/agents/sandbox/materialization.py`
- `src/agents/sandbox/workspace_paths.py`
- `src/agents/sandbox/session/`
- `src/agents/sandbox/capabilities/`
- `src/agents/sandbox/capabilities/tools/`
- `src/agents/sandbox/memory/`
- `src/agents/extensions/sandbox/`
- `tests/test_guardrails.py`
- `tests/test_tool_guardrails.py`
- `tests/test_hitl_utils.py`
- `tests/test_hitl_session_scenario.py`
- `tests/test_run_context_approvals.py`
- `tests/sandbox/`
- `tests/mcp/test_mcp_approval.py`

Search:

```bash
rg -n "guardrail|tripwire|handoff|session|trace|tracing|human|approval|sandbox|tool|Runner" references/openai-agents-python
```

### 需要提取什么

- Input guardrail design
- Output guardrail design
- Tool guardrail design
- Handoff routing model
- Session persistence
- Trace/span model
- HITL insertion point
- Sandbox agent lifecycle

### Harness lesson

OpenAI Agents SDK 说明：harness concern 可以被包装成 composable primitive，而不必做成一个庞大的 monolithic runtime。

## P1 - Microsoft Agent Framework

路径：`references/microsoft-agent-framework`

### 为什么这个 repo 重要

Microsoft Agent Framework 值得作为 production-oriented agent workflow framework 来研究。

重点看 graph-based workflow pattern、sequential/concurrent/handoff/group collaboration、durability、restartability、observability、governance、HITL，以及 Python/.NET architecture comparison。

### 优先阅读

- `README.md`
- `TRANSPARENCY_FAQ.md`
- `docs/features/durable-agents/README.md`
- `docs/features/durable-agents/durable-agents-ttl.md`
- `docs/features/durable-agents/AGENTS.md`
- `docs/decisions/0003-agent-opentelemetry-instrumentation.md`
- `docs/decisions/0006-userapproval.md`
- `docs/decisions/0007-agent-filtering-middleware.md`
- `docs/decisions/0018-agentthread-serialization.md`
- `docs/decisions/0019-python-context-compaction-strategy.md`
- `docs/decisions/0024-codeact-integration.md`
- `docs/decisions/0024-prompt-injection-defense.md`
- `schemas/durable-agent-entity-state.json`
- `declarative-agents/workflow-samples/`
- `python/README.md`
- `python/AGENTS.md`
- `python/PACKAGE_STATUS.md`
- `python/packages/core/agent_framework/_agents.py`
- `python/packages/core/agent_framework/_tools.py`
- `python/packages/core/agent_framework/_middleware.py`
- `python/packages/core/agent_framework/observability.py`
- `python/packages/core/agent_framework/_sessions.py`
- `python/packages/core/agent_framework/_compaction.py`
- `python/packages/core/agent_framework/_workflows/_workflow.py`
- `python/packages/core/agent_framework/_workflows/_workflow_builder.py`
- `python/packages/core/agent_framework/_workflows/_runner.py`
- `python/packages/core/agent_framework/_workflows/_edge.py`
- `python/packages/core/agent_framework/_workflows/_edge_runner.py`
- `python/packages/core/agent_framework/_workflows/_executor.py`
- `python/packages/core/agent_framework/_workflows/_agent_executor.py`
- `python/packages/core/agent_framework/_workflows/_workflow_executor.py`
- `python/packages/core/agent_framework/_workflows/_state.py`
- `python/packages/core/agent_framework/_workflows/_events.py`
- `python/packages/core/agent_framework/_workflows/_checkpoint.py`
- `python/packages/core/agent_framework/_workflows/_checkpoint_encoding.py`
- `python/packages/core/agent_framework/_workflows/_validation.py`
- `python/packages/core/agent_framework/_workflows/_viz.py`
- `python/packages/core/agent_framework/_workflows/_functional.py`
- `python/packages/core/agent_framework/_workflows/_workflow_context.py`
- `python/packages/orchestrations/agent_framework_orchestrations/_sequential.py`
- `python/packages/orchestrations/agent_framework_orchestrations/_concurrent.py`
- `python/packages/orchestrations/agent_framework_orchestrations/_handoff.py`
- `python/packages/orchestrations/agent_framework_orchestrations/_group_chat.py`
- `python/packages/orchestrations/agent_framework_orchestrations/_magentic.py`
- `python/packages/orchestrations/agent_framework_orchestrations/_orchestration_state.py`
- `python/packages/durabletask/agent_framework_durabletask/_durable_agent_state.py`
- `python/packages/durabletask/agent_framework_durabletask/_orchestration_context.py`
- `python/packages/durabletask/agent_framework_durabletask/_client.py`
- `python/packages/durabletask/agent_framework_durabletask/_worker.py`
- `python/packages/durabletask/agent_framework_durabletask/_entities.py`
- `python/packages/durabletask/agent_framework_durabletask/_executors.py`
- `python/packages/durabletask/agent_framework_durabletask/_callbacks.py`
- `python/packages/ag-ui/agent_framework_ag_ui/_agent_run.py`
- `python/packages/ag-ui/agent_framework_ag_ui/_workflow_run.py`
- `python/packages/ag-ui/agent_framework_ag_ui/_event_converters.py`
- `python/packages/ag-ui/agent_framework_ag_ui/_state.py`
- `python/packages/ag-ui/agent_framework_ag_ui/_orchestration/`

Search:

```bash
rg -n "workflow|graph|checkpoint|durable|restart|human|approval|handoff|middleware|observability|trace|routing|edge" references/microsoft-agent-framework
```

### 需要提取什么

- Production workflow model
- Routing 和 graph control
- Durability/restartability model
- HITL gate model
- Middleware 和 governance hook
- Observability structure
- Python、.NET 和 declarative workflow surface 如何比较

### Harness lesson

Microsoft Agent Framework 说明：当 production constraint 变成 first-class concern 时，agent orchestration 会自然包含 restartability、governance、telemetry 和 human control。

## P1 - Mastra

路径：`references/mastra`

### 为什么这个 repo 重要

Mastra 是研究 TypeScript agent 与 workflow orchestration 的有用 reference。

重点看 model routing、graph-based workflow、branching、parallel execution、execution state、suspend/resume、tool approval、tracing，以及 TypeScript agent infrastructure 的 API ergonomics。

### 优先阅读

- `README.md`
- `docs/`
- `packages/core/src/workflows/workflow.ts`
- `packages/core/src/workflows/execution-engine.ts`
- `packages/core/src/workflows/types.ts`
- `packages/core/src/workflows/step.ts`
- `packages/core/src/workflows/handlers/control-flow.ts`
- `packages/core/src/workflows/handlers/entry.ts`
- `packages/core/src/workflows/handlers/step.ts`
- `packages/core/src/workflows/evented/workflow.ts`
- `packages/core/src/workflows/evented/execution-engine.ts`
- `packages/core/src/workflows/evented/step-executor.ts`
- `packages/core/src/workflows/evented/types.ts`
- `packages/core/src/workflows/scheduler/`
- `packages/core/src/agent/agent.ts`
- `packages/core/src/agent/durable/create-durable-agent.ts`
- `packages/core/src/agent/durable/create-evented-agent.ts`
- `packages/core/src/agent/durable/evented-agent.ts`
- `packages/core/src/agent/durable/durable-agent.ts`
- `packages/core/src/agent/durable/run-registry.ts`
- `packages/core/src/agent/durable/types.ts`
- `packages/core/src/llm/model/router.ts`
- `packages/core/src/llm/model/embedding-router.ts`
- `packages/core/src/memory/`
- `packages/core/src/storage/`
- `packages/core/src/storage/domains/`
- `packages/core/src/observability/`
- `packages/core/src/evals/scoreTraces/`
- `packages/core/src/agent/__tests__/tool-approval.test.ts`
- `packages/core/src/agent/__tests__/tool-suspension.test.ts`
- `packages/core/src/agent/__tests__/resume-stream-no-snapshot.test.ts`
- `packages/core/src/agent/__tests__/active-tools-enforcement.test.ts`
- `packages/core/src/agent/__tests__/supervisor-integration.test.ts`
- `packages/core/src/harness/subagent-workspace-integration.test.ts`
- `packages/core/src/harness/subagent-tool.test.ts`
- `packages/core/src/harness/tracing-propagation.test.ts`

Search:

```bash
rg -n "workflow|branch|parallel|router|routing|model routing|suspend|resume|human|approval|state|storage|trace|agent" references/mastra
```

### 需要提取什么

- TypeScript workflow DSL
- Branch/parallel modeling
- Model routing abstraction
- Execution state model
- Human approval 和 tool suspension model
- Durable agent pattern
- framework API 如何清晰暴露 harness concept

### Harness lesson

Mastra 说明：TypeScript-native harness API 可以做得像 application infrastructure，而不是外置 research framework。

## P1 - Superpowers

路径：`references/superpowers`

### 为什么这个 repo 重要

Superpowers 是研究 **skill-driven workflow control** 的强 reference。

它不提供 LangGraph、OpenHands 或 Cline 那种 durable runtime、state graph、sandbox 或 event service。它的价值在于把 coding agent 的工作流拆成可触发、可审查、可组合的 skills，并通过 session bootstrap、TDD、debugging、planning、subagent execution 和 review gate 约束 agent 行为。

重点看：skill 如何成为 workflow control surface，bootstrap 如何让 skills 自动进入 session，文档型 skill 如何通过 pressure test 和 eval 变成行为约束，subagent-driven development 如何把 controller、implementer 和 reviewer 的职责分开。

### 优先阅读

- `README.md`
- `CLAUDE.md`
- `.codex-plugin/plugin.json`
- `.claude-plugin/plugin.json`
- `.cursor-plugin/plugin.json`
- `.opencode/plugins/superpowers.js`
- `gemini-extension.json`
- `hooks/hooks.json`
- `hooks/hooks-cursor.json`
- `hooks/session-start`
- `docs/README.opencode.md`
- `docs/testing.md`
- `.github/PULL_REQUEST_TEMPLATE.md`
- `skills/using-superpowers/SKILL.md`
- `skills/brainstorming/SKILL.md`
- `skills/writing-plans/SKILL.md`
- `skills/subagent-driven-development/SKILL.md`
- `skills/subagent-driven-development/implementer-prompt.md`
- `skills/subagent-driven-development/spec-reviewer-prompt.md`
- `skills/subagent-driven-development/code-quality-reviewer-prompt.md`
- `skills/test-driven-development/SKILL.md`
- `skills/systematic-debugging/SKILL.md`
- `skills/systematic-debugging/root-cause-tracing.md`
- `skills/verification-before-completion/SKILL.md`
- `skills/requesting-code-review/SKILL.md`
- `skills/requesting-code-review/code-reviewer.md`
- `skills/writing-skills/SKILL.md`
- `skills/writing-skills/testing-skills-with-subagents.md`
- `scripts/sync-to-codex-plugin.sh`
- `tests/skill-triggering/`
- `tests/explicit-skill-requests/`
- `tests/subagent-driven-dev/`
- `tests/codex-plugin-sync/`

Search:

```bash
rg -n "skill|bootstrap|SessionStart|brainstorming|TDD|subagent|review|gate|verification|pressure|eval|human partner|slop|workflow|plan" references/superpowers
```

### 需要提取什么

- Session bootstrap 如何注入 skill discipline
- Skill trigger 规则如何降低 agent 随手开写
- Brainstorming -> plan -> execution 的 gate chain
- TDD 和 verification-before-completion 如何约束 false completion
- Subagent-driven development 的 controller / implementer / reviewer 分工
- Spec compliance review 与 code quality review 的顺序约束
- Writing-skills 如何把 prompt/skill 文档当作需要 eval 的行为代码
- PR governance 如何拦截 fabricated、speculative、bulk agent contribution
- 多 harness plugin packaging 的边界：Claude、Codex、Cursor、OpenCode、Gemini

### Harness lesson

Superpowers 说明：prompt/skill 层也可以承载 harness discipline，但必须有 bootstrap、强触发规则、review gate 和 eval pressure 才接近可靠。它不能替代 durable state、event log 或 runtime-enforced gate；它适合作为 skill surface 和 workflow discipline 的 reference。

## P2 - CrewAI

路径：`references/crewai`

### 为什么这个 repo 重要

CrewAI 适合研究 agent team、task execution、flow、event-driven orchestration、callback、process model 和 state persistence 之间的边界。

它的核心 lesson 是如何把“谁做工作”和“工作如何 orchestration”分开。

### 优先阅读

- `README.md`
- `docs/en/quickstart.mdx`
- `docs/en/concepts/flows.mdx`
- `docs/en/concepts/crews.mdx`
- `docs/en/telemetry.mdx`
- `lib/crewai/src/crewai/crew.py`
- `lib/crewai/src/crewai/task.py`
- `lib/crewai/src/crewai/agent/core.py`
- `lib/crewai/src/crewai/agents/crew_agent_executor.py`
- `lib/crewai/src/crewai/agents/step_executor.py`
- `lib/crewai/src/crewai/agents/tools_handler.py`
- `lib/crewai/src/crewai/flow/flow.py`
- `lib/crewai/src/crewai/flow/flow_wrappers.py`
- `lib/crewai/src/crewai/flow/flow_config.py`
- `lib/crewai/src/crewai/flow/flow_context.py`
- `lib/crewai/src/crewai/flow/human_feedback.py`
- `lib/crewai/src/crewai/flow/input_provider.py`
- `lib/crewai/src/crewai/flow/async_feedback/providers.py`
- `lib/crewai/src/crewai/flow/persistence/base.py`
- `lib/crewai/src/crewai/flow/persistence/sqlite.py`
- `lib/crewai/src/crewai/flow/visualization/`
- `lib/crewai/src/crewai/events/event_bus.py`
- `lib/crewai/src/crewai/events/event_listener.py`
- `lib/crewai/src/crewai/events/event_context.py`
- `lib/crewai/src/crewai/events/handler_graph.py`
- `lib/crewai/src/crewai/events/types/flow_events.py`
- `lib/crewai/src/crewai/events/types/task_events.py`
- `lib/crewai/src/crewai/events/types/agent_events.py`
- `lib/crewai/src/crewai/events/types/crew_events.py`
- `lib/crewai/src/crewai/events/types/tool_usage_events.py`
- `lib/crewai/src/crewai/events/types/checkpoint_events.py`
- `lib/crewai/src/crewai/events/listeners/tracing/trace_listener.py`
- `lib/crewai/src/crewai/state/checkpoint_config.py`
- `lib/crewai/src/crewai/state/checkpoint_listener.py`
- `lib/crewai/src/crewai/state/event_record.py`
- `lib/crewai/src/crewai/state/runtime.py`
- `lib/crewai/src/crewai/state/provider/json_provider.py`
- `lib/crewai/src/crewai/state/provider/sqlite_provider.py`
- `lib/crewai/src/crewai/tasks/llm_guardrail.py`
- `lib/crewai/src/crewai/tasks/hallucination_guardrail.py`
- `lib/crewai/src/crewai/core/providers/human_input.py`
- `lib/crewai/src/crewai/tools/tool_usage.py`
- `lib/crewai/src/crewai/tools/tool_calling.py`
- `lib/crewai/src/crewai/tools/agent_tools/delegate_work_tool.py`

Search:

```bash
rg -n "Flow|Crew|Task|Process|router|listen|event|callback|state|guardrail|human|delegation" references/crewai/lib references/crewai/docs/en
```

### 需要提取什么

- Crew 与 Flow 的边界
- Task lifecycle
- Event/listener model
- Delegation model
- Callback/telemetry hook
- Conditional branching
- Checkpoint 和 state-provider model

### Harness lesson

CrewAI 说明：multi-agent coordination 可以暴露为更高层的 workflow API。它对 low-level harness design 的中心性弱于 LangGraph 或 OpenHands，但仍然提供有用的 orchestration vocabulary。

## P2 - AutoGen

路径：`references/autogen`

### 为什么这个 repo 重要

AutoGen 是 historically important multi-agent framework。

重点看 agent-to-agent messaging、routed agent、actor-style runtime concept、conversation/group chat orchestration、termination condition 和 multi-agent communication protocol。

当前 README 将用户指向 Microsoft Agent Framework 作为 successor。因此 AutoGen 应作为 historical and architectural reference，而不是新 framework design 的 primary source。

### 优先阅读

- `README.md`
- `docs/design/01 - Programming Model.md`
- `docs/design/02 - Topics.md`
- `docs/design/03 - Agent Worker Protocol.md`
- `docs/design/04 - Agent and Topic ID Specs.md`
- `docs/design/05 - Services.md`
- `protos/agent_worker.proto`
- `protos/cloudevent.proto`
- `python/README.md`
- `python/packages/autogen-core/README.md`
- `python/packages/autogen-core/src/autogen_core/`
- `python/packages/autogen-agentchat/README.md`
- `python/packages/autogen-agentchat/src/autogen_agentchat/`
- `python/samples/core_async_human_in_the_loop/main.py`
- `python/samples/core_semantic_router/`
- `python/samples/core_distributed-group-chat/`
- `python/samples/core_grpc_worker_runtime/`
- `dotnet/test/Microsoft.AutoGen.Core.Tests/`

Search:

```bash
rg -n "RoutedAgent|message|MessageContext|runtime|actor|topic|subscription|termination|GroupChat|handoff|route|routing" references/autogen
```

### 需要提取什么

- Message routing model
- Actor/runtime model
- Agent communication lifecycle
- Topic/subscription model
- Termination condition
- Multi-agent coordination pattern

### Harness lesson

AutoGen 说明：multi-agent system 可以被建模为 message-passing runtime。真正有价值的点不是“很多 agent”，而是 controlled communication、termination 和 routing。

## P3 - AutoGPT

路径：`references/autogpt`

### 为什么这个 repo 重要

AutoGPT popularity 很高，platform scope 也很大。

它适合研究 agent platform architecture、workflow builder、block/component、continuous agent、external trigger、marketplace/product flow，以及 classic autonomous-agent history。

它很大且噪音多。如果目标是 clean harness primitive，Do not 先读它。

### 优先阅读

- `README.md`
- `AGENTS.md`
- `docs/home/README.md`
- `docs/platform/what-is-autogpt-platform.md`
- `docs/platform/agent-blocks.md`
- `docs/platform/new_blocks.md`
- `docs/platform/block-sdk-guide.md`
- `docs/platform/create-basic-agent.md`
- `docs/platform/edit-agent.md`
- `docs/platform/integrating/api-guide.md`
- `docs/platform/integrating/oauth-guide.md`
- `docs/integrations/block-integrations/`
- `autogpt_platform/backend/schema.prisma`
- `autogpt_platform/backend/backend/api/rest_api.py`
- `autogpt_platform/backend/backend/api/conn_manager.py`
- `autogpt_platform/backend/backend/api/features/v1.py`
- `autogpt_platform/backend/backend/api/features/builder/routes.py`
- `autogpt_platform/backend/backend/api/features/builder/db.py`
- `autogpt_platform/backend/backend/api/features/builder/model.py`
- `autogpt_platform/backend/backend/api/features/executions/review/routes.py`
- `autogpt_platform/backend/backend/api/features/executions/review/model.py`
- `autogpt_platform/backend/backend/executor/manager.py`
- `autogpt_platform/backend/backend/executor/scheduler.py`
- `autogpt_platform/backend/backend/executor/simulator.py`
- `autogpt_platform/backend/backend/executor/activity_status_generator.py`
- `autogpt_platform/backend/backend/executor/cluster_lock.py`
- `autogpt_platform/backend/backend/executor/automod/manager.py`
- `autogpt_platform/backend/backend/blocks/_base.py`
- `autogpt_platform/backend/backend/blocks/human_in_the_loop.py`
- `autogpt_platform/backend/backend/blocks/io.py`
- `autogpt_platform/backend/backend/blocks/iteration.py`
- `autogpt_platform/backend/backend/blocks/llm.py`
- `autogpt_platform/backend/backend/blocks/mcp/block.py`
- `autogpt_platform/backend/backend/blocks/github/triggers.py`
- `autogpt_platform/backend/backend/blocks/airtable/triggers.py`
- `autogpt_platform/backend/backend/integrations/webhooks/_base.py`
- `autogpt_platform/backend/backend/integrations/webhooks/_manual_base.py`
- `autogpt_platform/backend/backend/integrations/webhooks/graph_lifecycle_hooks.py`
- `autogpt_platform/backend/backend/integrations/webhooks/github.py`
- `autogpt_platform/backend/backend/integrations/webhooks/telegram.py`
- `autogpt_platform/backend/backend/notifications/notifications.py`
- `autogpt_platform/frontend/src/app/(platform)/build/stores/graphStore.ts`
- `autogpt_platform/frontend/src/app/(platform)/build/stores/nodeStore.ts`
- `autogpt_platform/frontend/src/app/(platform)/build/stores/edgeStore.ts`
- `autogpt_platform/frontend/src/app/(platform)/build/stores/blockMenuStore.ts`
- `autogpt_platform/frontend/src/app/(platform)/build/stores/historyStore.ts`
- `autogpt_platform/frontend/src/services/builder-draft/draft-service.ts`
- `classic/README.md`
- `classic/SECURITY.md`
- `classic/forge/forge/permissions.py`
- `classic/direct_benchmark/direct_benchmark/harness.py`
- `classic/direct_benchmark/direct_benchmark/state.py`
- `classic/original_autogpt/autogpt/`

Search:

```bash
rg -n "workflow|block|component|trigger|agent|platform|event|execution|schedule|continuous|builder|server" references/autogpt --glob '!**/node_modules/**' --glob '!**/.git/**' --glob '!**/dist/**' --glob '!**/build/**'
```

### 需要提取什么

- Platform-level workflow representation
- Block/component abstraction
- Trigger model
- Continuous agent execution model
- UI-to-runtime relationship
- Human review 和 execution status model
- Classic workspace 和 permission model

### Harness lesson

AutoGPT 说明：当 agent orchestration 变成 product platform 时，系统会长成什么样。它适合看 platform perspective，但不适合作为 clean first reference。

---

# Unavailable / Needs Manual Check

当前 snapshot 中没有。所有 canonical submodule 都已添加并 checkout。

checkout 过程中，`cline`、`microsoft-agent-framework` 和 `autogen` 暴露了 Git LFS filter，但本机没有 `git-lfs`。这些 submodule 已通过 local LFS smudge filter disabled 的方式 checkout，因此 text source file 可用于研究。除非用户明确要求，Do not 安装 Git LFS 或 fetch LFS blob。
