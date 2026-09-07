# 📘 Agent（AI Agent / 自主智能体）

> **一句话定义**：能感知环境、自主规划、调用工具并采取行动以完成目标的 AI 系统，通常以 LLM 作为推理核心。
> **所属领域**：AI / LLM / Agentic Systems（智能体系统）
> **首次提出**：经典"Intelligent Agent"概念源自 AI 早期；LLM-based Agent 的现代架构由 Lilian Weng 于 2023-06-23 在《LLM Powered Autonomous Agents》中系统化。
> **辨析提示**：本卡片聚焦"LLM-based Agent"。Anthropic 2024 进一步区分了 *workflow* 与 *agent* 两种 agentic system 范式。

---

## 🎯 学习目标
1. 能用自己的话复述 LLM Agent 的四要素（LLM + 规划 + 记忆 + 工具）及其相互作用
2. 能区分 Workflow（代码控制流）与 Agent（模型控制流）的本质差异
3. 能判断一个任务应该用 Workflow 还是 Agent（"先简单再复杂"原则）
4. 能列举 ReAct、Chain-of-Thought、Reflexion 等关键技术名词并解释其作用

## ❓ 核心问题
1. 为什么 LLM 本身"不够用"——为什么需要把它包装成 Agent？
2. Agent 和 Workflow 在"控制权归属"上有什么本质区别？
3. 什么情况下**不该**用 Agent？（避免过度设计）

## 🔍 结构化解释

### 核心机制 / 组成

基于 Lilian Weng 2023 年的综述 [1]，一个 LLM Agent 由四个核心组件构成：

1. **LLM（大脑）**——负责理解输入、推理、决策、生成响应，相当于系统的"中央处理器"。
2. **规划（Planning）**：
   - **任务分解**：将复杂目标拆为可执行的子目标（Chain-of-Thought、Tree of Thoughts）
   - **反思 / 自我批评**：ReAct、Reflexion、Chain of Hindsight 等框架让 Agent 从错误中学习
3. **记忆（Memory）**：
   - **短期记忆**：当前对话上下文（in-context learning）
   - **长期记忆**：外部向量数据库（FAISS、Chroma、Pinecone 等），通过 ANN 算法检索
4. **工具（Tools）**：通过 API 调用、function calling、代码执行扩展 LLM 自身能力边界

Agent 通过 **"思考 → 行动 → 观察"（think-act-observe）** 循环运行，直到任务完成或达到停止条件（最大迭代次数 / 检查点 / 阻塞错误）[2]。

### 应用场景

**典型案例：开放式编程任务（SWE-bench 风格）**
- 用户："修复这个开源仓库的 issue #123"
- Agent 流程：
  1. 用文件工具读取仓库，理解代码结构
  2. 规划要修改哪些文件、需补充哪些测试
  3. 工具调用：编辑文件 → 跑测试 → 读报错
  4. 反思：测试失败 → 修改代码 → 再跑测试（迭代）
  5. 提交 PR 或汇报修复结果

Anthropic 在自家工程博客里就把 SWE-bench coding agent 作为 agentic 模式的典型用例 [2]。

### 边界与陷阱

- ⚠️ **Agent ≠ 复杂框架**。Anthropic 反复强调："Consistently, the most successful implementations use simple, composable patterns rather than complex frameworks"——很多团队硬塞 LangChain/AutoGen，结果比"LLM 在循环里调工具"的几十行代码更难调试 [2]。
- ⚠️ **Agent 适合开放性问题**（步骤数难以预知），**Workflow 适合明确任务**（流程可枚举）。用 Agent 处理固定流程反而引入不确定性。
- ⚠️ **自主性越强，误差累积越大**。多轮 LLM 调用的小错可能在第 10 轮变成致命错——Anthropic 建议在沙盒环境做充分测试 [2]。
- ⚠️ **常见误解**：以为调了 LangChain / AutoGen / CrewAI 就"做了 Agent"。其实"LLM 在循环里根据反馈调用工具"已经是 Agent 的全部核心——框架是脚手架，不是本质。

## 🔀 概念辨析

| 维度 | Agent（自主智能体） | Workflow（工作流） | 普通 LLM 调用 |
| --- | --- | --- | --- |
| **控制权归属** | LLM 动态决定路径 | 代码预设路径 | 无路径，单轮调用 |
| **步骤数** | 不固定，运行时决定 | 固定，由编排代码决定 | 1 步 |
| **适用场景** | 开放问题、不可预测步骤 | 明确任务、固定子任务 | 简单问答 / 生成 |
| **成本 / 延迟** | 高（多轮推理） | 中（按部就班） | 低 |
| **失败模式** | 误差累积、可能跑偏 | 单点失败可控 | 无累积 |
| **典型例子** | SWE-bench coding agent | Prompt chaining、Routing、Parallelization | 单轮问答 |

> Workflow 又细分为五种模式（按复杂度递增）：Prompt Chaining → Routing → Parallelization → Orchestrator-Workers → Evaluator-Optimizer [2]。

## ✅ 自测题

**1.（理解层）用自己的话复述 Agent = LLM + Planning + Memory + Tools 四要素中每个组件的作用。**
- 答案要点：LLM=推理核心；Planning=任务分解+反思；Memory=短期上下文+长期向量库；Tools=扩展 LLM 能力边界（API/函数/代码）；四者通过 think-act-observe 循环串起来。

**2.（应用层）给定任务"自动帮用户预订去东京出差的行程"。请描述 Agent 会如何规划与调用工具。**
- 答案要点：拆解子任务（查航班/比酒店/查日历/核对差旅政策）；用偏好（长期记忆）避免重复询问；调用 Google Flights API、酒店 API、日历 API；若首选航班售罄，自主调整方案；最后呈现完整行程或在授权后直接下单。

**3.（分析层）为什么"客服 + 自动退款"场景比"营销文案生成 + 多语言翻译"场景更适合用 Agent？**
- 答案要点：客服流程依赖对话分支、需读取外部数据（订单/工单/知识库）、动作可程序化执行（退款/改单）、结果可衡量——满足 Anthropic 提出的 Agent 适用条件；文案生成+翻译是固定两步序列，更适合 Prompt Chaining 这种 Workflow（甚至单轮 LLM + 后处理就够）。

## 📚 参考来源（可核查）

1. Lilian Weng. *LLM Powered Autonomous Agents*. Lil'Log, **2023-06-23**. [https://lilianweng.github.io/posts/2023-06-23-agent/](https://lilianweng.github.io/posts/2023-06-23-agent/) — 综述博客（首次系统化 LLM Agent = LLM + Planning + Memory + Tools 架构）| 访问日期 2026-09-07
2. Erik Schluntz & Barry Zhang. *Building effective agents*. Anthropic Engineering Blog, **2024-12-19**. [https://www.anthropic.com/engineering/building-effective-agents](https://www.anthropic.com/engineering/building-effective-agents) — 工程指南（区分 Workflow vs Agent，提出"先简单再复杂"原则）| 访问日期 2026-09-07
3. Anthropic Research. *Building Effective AI Agents*. [https://www.anthropic.com/research/building-effective-agents](https://www.anthropic.com/research/building-effective-agents) — 同篇文章在 Research 板块的镜像页 | 访问日期 2026-09-07

---
*本卡片由 `concept-learner` Skill 生成*