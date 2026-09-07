# 📘 Skill（AI Agent 中的能力单元 / Agent Skills）

> **一句话定义**：在 AI Agent 框架中，"Skill" 是一个包含 `SKILL.md` 的目录，作为模型可按需加载的可调用能力单元——由模型根据任务描述自主判断何时调用，而非用户手动选择。
> **所属领域**：AI / Agentic Systems / 工具与能力扩展
> **首次提出 / 标准来源**：由 Anthropic 最初提出，发布为 **Agent Skills 开放标准**（[agentskills.io](https://agentskills.io/home)），现已被 Claude Code、Cursor、GitHub Copilot 等多工具采纳。
> **辨析提示**：本卡片聚焦"Agent Skill"。其他常见含义（人类技能、游戏 Skill 树、Windows PowerShell Skill 等）不在此展开。如需切换含义请告知。

---

## 🎯 学习目标
1. 能用自己的话解释 Agent Skill 的核心定义（SKILL.md + 渐进式披露）
2. 能区分 Personal Skill、Project Skill、Plugin Skill 三种存放位置及其适用场景（Claude Code 官方文档明确区分前两类，第三类 Plugin Skill 由后续生态延展 [1]）
3. 能说出 Skill 与 CLAUDE.md / Subagent / Tool Calling 的区别
4. 能描述 Skill 的三级加载机制（metadata → body → supporting files）

## ❓ 核心问题
1. Skill 和 CLAUDE.md 有什么区别？为什么要发明 Skill 这个新抽象？
2. Skill 是用户调用的还是模型调用的？这种"模型调用"的设计有什么好处和代价？
3. 一个项目应该把所有能力都做成 Skill 吗？还是只在某些场景下用？

## 🔍 结构化解释

### 核心机制 / 组成

基于 Anthropic 推出的 Agent Skills 开放标准 [2] 和 Claude Code 官方文档 [1]：

1. **Skill 的物理形态**：一个目录（如 `.claude/skills/<name>/`），其中必须有 `SKILL.md` 文件，可附带脚本、模板、参考文档等支持文件
2. **SKILL.md 结构**：
   - **YAML frontmatter**：`name`、`description`（**最重要**——决定模型何时调用此 skill）
   - **Markdown body**：使用该 skill 的完整指令、步骤、示例
3. **渐进式披露（Progressive Disclosure）三级加载** [2]：
   - **Level 1（启动时）**：仅加载 frontmatter（name + description，约 100 tokens/skill）
   - **Level 2（触发时）**：完整 SKILL.md body 加载（~5K tokens）
   - **Level 3（按需）**：supporting 文件 / 脚本仅在被引用时加载
4. **模型自主调用（Model-Invoked）**：description 字段是模型决定何时加载该 skill 的核心信号；用户也可以 `/skill-name` 显式调用 [1]

> ⚙️ **路径差异**：Claude Code 使用 `.claude/skills/`；本仓库的 `concept-learner` Skill 按 WorkBuddy 约定放在 `.workbuddy/skills/`。概念相同，路径随工具而变。

### 应用场景

**典型用例：项目级 Skill（共享给团队）**
- 场景：团队想把"部署到 staging 环境"这一重复过程封装给 Claude
- 实现：在 `.claude/skills/deploy-to-staging/SKILL.md` 中写好部署步骤、SSH 命令、安全检查清单
- 工作流：
  1. Claude Code 启动 → 发现 `.claude/skills/` 目录 → 加载所有 skill 的 frontmatter
  2. 用户说"帮我部署这个 PR" → Claude 读 frontmatter 的 description，匹配到 deploy-to-staging
  3. 自动加载完整 SKILL.md → 按步骤执行
  4. 需要时再加载 supporting 文件（如环境配置模板）
- 优势：通过 git 共享给整个团队，所有人得到同样的能力

### 边界与陷阱

- ⚠️ **Skill ≠ CLAUDE.md**：CLAUDE.md 提供静态项目背景（如"这是 K8s 配置仓库"）；Skill 提供可执行的工作流。两者互补，不是替代 [1]。
- ⚠️ **Description 决定生死**：description 写得太抽象（如"有用的工具"）→ 模型不会调用；写得太具体（如只在"完全匹配关键词 X 时用"）→ 又会漏触发。需要在"清楚描述场景"和"避免过度限定"之间平衡 [1][2]。
- ⚠️ **不是所有能力都该做成 Skill**：高频且每次都需要的内容放 CLAUDE.md；低频但流程化的内容做成 Skill；一次性的临时指令直接写在对话里。
- ⚠️ **Skill 是文件系统产物，不是 API 注册**：不能在程序中注册 skill，必须创建目录、写 SKILL.md。这降低了灵活性但极大简化了分发——`git push` 即上线 [1]。
- ⚠️ **误用警示**：不要把"高频但每次都不同"的任务做成 Skill（如"帮我写邮件"），这会让 description 触发混乱；适合的是"低频但流程固定"的任务（如"季度合规报告生成"）。

## 🔀 概念辨析

| 维度 | Skill | CLAUDE.md | Subagent | Tool Calling |
| --- | --- | --- | --- | --- |
| **物理形态** | `.claude/skills/*/SKILL.md` 目录 | 单个 `CLAUDE.md` 文件 | `.claude/agents/*.md` | 内置 / 自定义 function schema |
| **加载方式** | 模型按 description 触发 | 每次会话自动注入上下文 | 显式调用 `task(subagent)` | LLM 输出结构化 tool_use |
| **内容性质** | 可执行工作流（步骤+脚本） | 静态背景信息 | 独立 agent 的完整 prompt | 单个原子操作（API/函数） |
| **共享方式** | git（项目级）/ 文件（个人级） | git | git | 代码注册 |
| **何时使用** | 重复的多步骤流程 | 项目基本事实 | 隔离的子任务 | 单次原子动作 |
| **上下文开销** | 按需加载（渐进式披露） | 全量注入（每次会话） | 独立上下文 | 每次 tool call 的 schema |

## ✅ 自测题

**1.（理解层）用一句话定义 Skill，并指出它和 CLAUDE.md 的本质区别。**
- 答案要点：Skill 是含 SKILL.md 的目录，是模型按 description 触发的可调用能力单元；CLAUDE.md 是静态注入的项目背景信息。Skill 是"可调用的特殊能力"，CLAUDE.md 是"项目基本说明"。

**2.（应用层）你团队的代码仓库需要"自动生成符合规范的 PR 描述"。请描述你会怎么把它实现为 Skill，并说明 description 该怎么写。**
- 答案要点：
  ① 在 `.claude/skills/gen-pr-desc/` 创建目录和 SKILL.md；
  ② frontmatter 的 description 写成类似 *"Generate a standardized PR description following team conventions. Use when the user asks for PR description, changelog, or commit summary"*——含触发关键词但不过度限定；
  ③ body 写团队 PR 模板、章节要求、长度限制、是否需引用 issue；
  ④ 可选添加 `templates/pr-default.md` 作为 Level 3 资源。

**3.（分析层）Skill 的"渐进式披露"机制相比"一次性加载所有 skill 内容"有什么根本好处？**
- 答案要点：节省 context window——一个项目可能有几十个 skill，若一次性全部加载会撑爆窗口；三级机制让模型先只看 metadata（~100 tokens × N）按需加载 body（~5K），supporting 文件按需加载；让 skill 数量从"几十"扩展到"几百"成为可能，且不影响成本 / 速度。这是从"prompt 工程"转向"context 工程"的一个典型体现。

## 📚 参考来源（可核查）

1. Anthropic. *Extend Claude with skills*. Claude Code Docs. [https://code.claude.com/docs/en/skills](https://code.claude.com/docs/en/skills) — 官方文档（SKILL.md 结构、frontmatter 字段、Skill 存放路径、与 CLAUDE.md 的区别）| 访问日期 2026-09-07
2. Agent Skills 社区. *Agent Skills Overview*. [https://agentskills.io/home](https://agentskills.io/home) — Agent Skills 开放标准官网（标准定义、渐进式披露机制、跨工具兼容性）；`agentskills.io` 根域 308 重定向到本页 | 访问日期 2026-09-07

---
*本卡片由 `concept-learner` Skill 生成*