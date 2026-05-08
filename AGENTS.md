# Harness Engineering Reference Map

## 目的

本仓库是一个用于研究 agentic software systems 中 **harness engineering** 的 curated reference map。

harness 是 AI agent 或 multi-agent system 外层的控制层。它决定：

- agent 被允许做什么
- tool call 何时被允许
- workflow state 如何表示
- event 如何记录
- routing decision 如何产生
- gate 如何 fail closed
- human 如何 approve 或 reject 高风险步骤
- execution 如何 resume、revert、trace 或 audit

本仓库不是 application repo。它把高信号 open-source system 作为 Git submodule 收集起来，让后续 agent 能直接检查真实实现，而不是从抽象概念推演。

## 仓库角色

把本仓库当作 **study index** 使用。

完整研究材料放在：

- `docs/reference-map.md`: priority 模型、快速表、每个 reference repo 的 Study Map。
- `docs/study-guide.md`: 后续 agent 的首读顺序、必须回答的问题、notes 模板和 lightweight inspection 命令。

`references/` 下的 submodule 目录是只读 reference material。除非用户明确要求 fork 或 patch 某个 upstream project，否则 Do not 编辑它们。

常用命令：

```bash
git submodule update --init --recursive --depth 1
git submodule status
git -C references/<repo> status --short
```

除非用户明确要求，Do not 在 submodule 内运行 install、build、test、package、setup、benchmark 或 project script。

只使用 GitHub repository 和 official docs 中的英文 source material。仓库包含 localized docs 时，优先读 English docs，跳过 localized folders。

## Submodule 规则

- Must 把 `references/<repo>/` 这些 submodule 目录视为 read-only。
- Do not 修改 submodule 内的文件。
- Do not 在 submodule 内运行 dependency installation。
- 除非用户明确要求，Do not 在 submodule 内运行 build 或 test command。
- Do not 执行 submodule 中的 unknown script。
- Do not 在普通分析中静默更新 submodule commit。
- 只用 `find`、`rg`、`sed` 和 read-only file viewing 做 lightweight inspection。
- If 某个 submodule 无法 clone 或 checkout, then 在 `Unavailable / needs manual check` 中记录；Do not 编造路径。
- 部分 upstream repository 可能包含 Git LFS pointer。If 本机没有 `git-lfs`, then 先研究已经 checkout 的 text file；除非用户明确要求，Do not 安装 Git LFS。

## Safety Rules

- 除非用户明确要求，Do not 编辑 `references/<repo>/` 这些 submodule 目录内的文件。
- Do not 在 submodule 内运行 package install command。
- 除非用户明确要求，Do not 在 submodule 内运行 build 或 test command。
- Do not 执行 submodule 中的 unknown script。
- Do not 把这些 repository 当作 dependency。
- Do not 从 upstream repo 复制大段 code block 到本仓库。
- 优先 summarize mechanism，并引用 path。
- 研究机制时，只使用 English docs 和 English source comments。
- If 本 `AGENTS.md` 或 `docs/` 中的 path 变 stale, then inspect repo 并更新 path；Do not guess。
