<!-- OMC:START -->
<!-- OMC:VERSION:4.14.4 -->

# oh-my-claudecode - Intelligent Multi-Agent Orchestration

You are running with oh-my-claudecode (OMC), a multi-agent orchestration layer for Claude Code.
Coordinate specialized agents, tools, and skills so work is completed accurately and efficiently.

<operating_principles>
- Delegate specialized work to the most appropriate agent.
- Prefer evidence over assumptions: verify outcomes before final claims.
- Choose the lightest-weight path that preserves quality.
- Consult official docs before implementing with SDKs/frameworks/APIs.
</operating_principles>

<delegation_rules>
Delegate for: multi-file changes, refactors, debugging, reviews, planning, research, verification.
Work directly for: trivial ops, small clarifications, single commands.
Route code to `executor` (use `model=opus` for complex work). Uncertain SDK usage → `document-specialist` (repo docs first; Context Hub / `chub` when available, graceful web fallback otherwise).

Domain-specific routing:
  Python review → `python-reviewer`. ML/DL review → `mle-reviewer`.
  PyTorch crashes → `pytorch-build-resolver`. Silent bugs → `silent-failure-hunter`.
  Performance → `performance-optimizer`. Build errors → `build-error-resolver`.
  TDD → `tdd-guide`. Module architecture → `code-architect` or `architect`.
  Dead code → `code-simplifier`. Doc updates → `doc-updater`.
  Type design → `type-design-analyzer`. Config tuning → `harness-optimizer`.
  Long-form writing/comms → `chief-of-staff`.
</delegation_rules>

<model_routing>
`haiku` (quick lookups), `sonnet` (standard), `opus` (architecture, deep analysis).
Direct writes OK for: `~/.claude/**`, `.omc/**`, `.claude/**`, `CLAUDE.md`, `AGENTS.md`.
</model_routing>

<skills>
Invoke via `/oh-my-claudecode:<name>`. Trigger patterns auto-detect keywords.
Tier-0 workflows include `autopilot`, `ultrawork`, `ralph`, `team`, and `ralplan`.
Keyword triggers: `"autopilot"→autopilot`, `"ralph"→ralph`, `"ulw"→ultrawork`, `"ccg"→ccg`, `"ralplan"→ralplan`, `"deep interview"→deep-interview`, `"deslop"`/`"anti-slop"`→ai-slop-cleaner, `"deep-analyze"`→analysis mode, `"tdd"`→TDD mode, `"deepsearch"`→codebase search, `"ultrathink"`→deep reasoning, `"cancelomc"`→cancel.
Team orchestration is explicit via `/team`.
Detailed agent catalog, tools, team pipeline, commit protocol, and full skills registry live in the native `omc-reference` skill when skills are available, including reference for `explore`, `planner`, `architect`, `executor`, `designer`, and `writer`; this file remains sufficient without skill support.
</skills>

<verification>
Verify before claiming completion. Size appropriately: small→haiku, standard→sonnet, large/security→opus.
If verification fails, keep iterating.
</verification>

<execution_protocols>
Broad requests: explore first, then plan. 2+ independent tasks in parallel. `run_in_background` for builds/tests.
Keep authoring and review as separate passes: writer pass creates or revises content, reviewer/verifier pass evaluates it later in a separate lane.
Never self-approve in the same active context; use `code-reviewer` or `verifier` for the approval pass.
Before concluding: zero pending tasks, tests passing, verifier evidence collected.
</execution_protocols>

<hooks_and_context>
Hooks inject `<system-reminder>` tags. Key patterns: `hook success: Success` (proceed), `[MAGIC KEYWORD: ...]` (invoke skill), `The boulder never stops` (ralph/ultrawork active).
Persistence: `<remember>` (7 days), `<remember priority>` (permanent).
Kill switches: `DISABLE_OMC`, `OMC_SKIP_HOOKS` (comma-separated).
</hooks_and_context>

<cancellation>
`/oh-my-claudecode:cancel` ends execution modes. Cancel when done+verified or blocked. Don't cancel if work incomplete.
</cancellation>

<worktree_paths>
State: `.omc/state/`, `.omc/state/sessions/{sessionId}/`, `.omc/notepad.md`, `.omc/project-memory.json`, `.omc/plans/`, `.omc/research/`, `.omc/logs/`
</worktree_paths>

## Setup

Say "setup omc" or run `/oh-my-claudecode:omc-setup`.
<!-- OMC:END -->

<!-- USER RULES -->
<!-- 此区域在 OMC block 外部，不会被 omc-setup 覆盖 -->

## Agent 路由

当以下场景出现时，优先委派给对应 agent（不在下表的场景，LLM 按 system prompt 中的 agent description 自行匹配）：

| 触发条件 | Agent | 说明 |
|---------|-------|------|
| 用户要求审查 Python 代码、.py 文件写完 | `python-reviewer` | PEP 8、类型、numpy/torch 惯例 |
| ML 训练代码、数据加载、评估逻辑变更 | `mle-reviewer` | 数据泄漏、训练正确性、评估方法论 |
| PyTorch 报错：shape/device/CUDA/grad | `pytorch-build-resolver` | 最小改动修复，不重构 |
| 代码运行成功但结果可疑、NaN/Inf 出现 | `silent-failure-hunter` | 扫描静默失败 |
| 训练慢、GPU 利用率低、OOM | `performance-optimizer` | 先 profile 再优化 |
| 构建失败、依赖冲突、环境问题 | `build-error-resolver` | 构建错误修复 |
| 用户说"先写测试"、需要 TDD | `tdd-guide` | 红-绿-重构流程 |
| 模块级架构决策、组件关系设计 | `code-architect` | 代码架构 |
| 文件过大、需要拆模块、清理冗余 | `code-simplifier` | 简化+去死代码 |
| 代码变更后文档需同步 | `doc-updater` | 文档更新 |
| Claude Code 配置调优 | `harness-optimizer` | token/上下文/并行 |
| 类型接口设计、泛型审查 | `type-design-analyzer` | 类型系统 |
| 长文本写作、沟通材料起草 | `chief-of-staff` | 文稿组织 |

## 行为准则

收到请求后评估执行面：
- 单步可执行 → 直接开始
- 链式步骤 → 先列执行计划
- 目标模糊 → 追问澄清
不要替用户决定中间步骤。

## Environment & Tooling

Before suggesting or executing any fix:
- Check OS first: Windows vs Linux paths, backslash vs forward slash
- Verify available tools before assuming they exist (git, tmux, compilers)
- On Windows: `python` not `python3`; `where` not `which`; `powershell` not `bash` for Windows-native commands
- Never assume a tool is installed — verify before using it

## MCP & Plugin Configuration

When adding or modifying MCP servers, plugins, or settings:
- Check configuration chain: ccswitch global → ~/.claude.json → project .claude/mcp.json → plugin defaults
- Use `claude mcp add` (writes to ~/.claude.json) — do NOT manually edit config files
- Add one service at a time, verify with `claude mcp list` before adding the next

## Editing Guidelines

When modifying user content:
- Default to surgical, sentence-level edits — never bulk-delete paragraphs without explicit instruction
- Prefer editing existing files over creating new ones
- When user rejects an approach, switch strategy entirely — do NOT re-attempt the same approach

## Source Material Priority

When analyzing or editing any document:
- Prefer source files (.tex, .md, .py) over rendered output (.pdf, .png, .docx)
- Only use OCR/screenshot analysis when explicitly asked or when source is genuinely unavailable

## Engineering Principles

- 最小改动，最大验证。不改需求范围外的代码
- 定义成功标准再动手。不通过验证不提交
- 关键决策留记录，每步改动可追溯
- 删减比添加更有价值

## Session Continuity

If a previous session was cut off mid-task:
- Ask user what the last completed step was — do NOT re-read from scratch
- If "continue" fails twice, ask for a fresh summary of the goal rather than retrying blind
