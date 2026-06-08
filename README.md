# AgentForge

> 锻造更好的 Agent。构建更好的软件。  
> *Forge better agents. Build better software.*

**AgentForge** 是一套面向编程 Agent 的精选技能集，融合了领域驱动工程与多 Agent 编排的最佳实践。语言无关、Token 高效，面向真实软件工程——而非"氛围编程"。

*A curated skill set for coding agents (Claude Code, Cursor, Copilot, Gemini CLI, etc.) combining domain-driven engineering with multi-agent orchestration. Language-agnostic and token-efficient.*

---

## 为什么选择 AgentForge？

大多数编程 Agent 技能分为两类：

- **流程重型** — 精细的多阶段流水线，功能强大但臃肿，难以定制。
- **实用轻量** — 领域感知好，但缺乏处理复杂工作的多 Agent 协调能力。

AgentForge 取两者之长，去两者之短。

| 来源 | 我们保留的 | 我们舍弃的 |
|------|-----------|-----------|
| **Matt Pocock** | 领域词汇表 (CONTEXT.md)、ADR、垂直切片、深模块、反馈回路优先调试、caveman 模式 | TypeScript 专属示例 |
| **Superpowers** | 多 Agent 编排、双阶段审查、验证门控、git worktree | 冗长流程图、重复解释、JS 专属命令 |

---

## 工作流

```
对齐 → 规划 → 构建 → 调试 → 审查 → 交付
  ↓      ↓      ↓      ↓      ↓      ↓
grill  to-    tdd  diagnose  code  finish
-with- issues (多Agent)     review branch
-docs
```

---

## 18 个技能

### 对齐 — 理解后再动手
| 技能 | 用途 |
|------|------|
| `grill-with-docs` | 步步紧逼地访谈，消除每个模糊决策。构建共享语言 (CONTEXT.md) 和决策记录 (ADR)。 |
| `to-prd` | 将共识合成为 PRD，发布到 Issue Tracker。 |

### 规划 — 把需求切成垂直切片
| 技能 | 用途 |
|------|------|
| `to-issues` | 将 PRD 分解为垂直切片 Issue，每个切片端到端可交付。 |
| `triage` | 通过状态机管理 Issue（`needs-triage` → `ready-for-agent` → done）。 |

### 构建 — 有纪律地实现
| 技能 | 用途 |
|------|------|
| `tdd` | 红-绿-重构 + 垂直切片 + 深模块。测试只验证公共接口的行为。 |
| `subagent-driven-development` | 每 Issue 分派独立子 Agent + 双阶段审查（规格合规 → 代码质量）。 |
| `dispatching-parallel-agents` | 并行分派独立任务。 |

### 调试 — 用反馈回路定位问题
| 技能 | 用途 |
|------|------|
| `diagnose` | 先建立确定性的 pass/fail 信号，再假设、插桩、修复。修复后必须回归测试。 |

### 审查 — 交付前验证
| 技能 | 用途 |
|------|------|
| `requesting-code-review` | 分派代码审查子 Agent，针对 git diff 范围。 |
| `receiving-code-review` | 以技术严谨性处理审查反馈：先验证再实现，错误的意见要驳回。 |
| `verification-before-completion` | 硬门控：无新证据不声称完成。 |
| `finishing-a-development-branch` | 结构化的 merge / PR / discard 工作流，附带测试验证。 |

### 架构 — 保持代码库健康
| 技能 | 用途 |
|------|------|
| `improve-codebase-architecture` | 发现深模块机会，生成 HTML 可视化报告。 |
| `zoom-out` | 用领域词汇获取陌生代码的高层地图。 |

### 效率 — 更快地工作
| 技能 | 用途 |
|------|------|
| `caveman` | 极简通信模式，节省约 75% Token。 |
| `handoff` | 压缩当前会话，供另一个 Agent 无缝接续。 |
| `using-git-worktrees` | 隔离工作空间管理。 |
| `writing-skills` | 用 TDD 方法论创建新技能。 |
| `setup-agentforge` | 一次性仓库配置：Issue Tracker、标签体系、领域文档结构。 |

---

## 快速开始

### 1. 克隆仓库

```bash
git clone https://github.com/yuanfanzz/agentforge.git
cd agentforge
```

### 2. 根据你的 Agent 工具做一条链接

| 工具 | 命令 |
|------|------|
| **Claude Code** | `ln -s "$(pwd)/skills" ~/.claude/skills/agentforge` |
| **Oh My Pi** | `ln -s "$(pwd)/skills" ~/.omp/skills/agentforge` |
| **Cursor** | 将 `skills/` 下的 `SKILL.md` 复制到项目 `.cursor/rules/`，或在 Composer 中直接引用 |
| **Gemini CLI / Codex CLI** | 将 `skills/` 路径加入 Agent 的上下文搜索目录，或手动复制 `SKILL.md` |

> 如果你不确定自己的 Agent 使用哪个目录，运行 `setup-agentforge` 技能里的检测逻辑，或直接阅读对应 `SKILL.md` 手动应用。

### 3. 初始化项目

在 Agent 对话中运行：

```
/setup-agentforge
```

它会引导你完成：
- 选择 Issue Tracker（GitHub / GitLab / Linear / 本地文件）
- 配置 triage 标签
- 建立项目领域文档结构

---

## 设计原则

1. **语言无关** — 命令说"运行你的测试套件"，而不是 `npx vitest run`。适配 Laravel、Next.js、Rails、Django 等所有技术栈。
2. **Token 高效** — 能用有序列表就不用流程图，不说三遍同一规则。每个词都必须Earn its place。
3. **领域感知** — 所有技能引用你项目的 CONTEXT.md 词汇表。Agent 说你的领域语言，而不是泛泛的 CS 术语。
4. **多 Agent 默认** — 复杂工作自动并行。质量由独立审查者验证，而非自我评估。
5. **硬门控** — 交付前必须验证。TDD 红-绿-重构。复现后再假设。没有商量余地。

---

## English Summary

AgentForge provides 18 skills across the full development lifecycle: **Align → Plan → Build → Debug → Review → Ship**. It is language-agnostic and works with Claude Code, Cursor, Copilot, Gemini CLI, and other agent frameworks. Install by cloning and symlinking the `skills/` directory into your agent's skill path, then run `/setup-agentforge` to configure your project.

---

## 致谢

AgentForge 融合了以下项目的思想：

- **[Matt Pocock's skills](https://github.com/mattpocock/skills)** — 领域词汇表、ADR、垂直切片、深模块、diagnose、caveman
- **[Superpowers](https://github.com/obra/superpowers)** — 多 Agent 编排、双阶段审查、验证门控、git worktree

## 许可证

MIT
