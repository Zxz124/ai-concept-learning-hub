# 作业提交说明（给老师查阅用）

> **作业题目**：使用 AI Skill 工具生成 AI 概念学习资料
> **作者**：Zxz124
> **完成日期**：2026-09-07
> **GitHub 仓库**：[https://github.com/Zxz124/ai-concept-learning-hub](https://github.com/Zxz124/ai-concept-learning-hub)
> **仓库状态**：公开（无需登录、无需权限即可访问全部内容）

---

## 一、本作业做了什么

我按照作业要求，分 7 步在 GitHub 上完成了一个完整、可追溯、可复用的 AI 概念学习资料生成工具：

1. **建仓库**：在 GitHub 创建公开仓库 `ai-concept-learning-hub`
2. **基础文件**：配置 `.gitignore`（排除 `.env`、`.key`、`.pem`、`config/`、`secrets/` 等敏感文件）
3. **写 Skill**：在 `.workbuddy/skills/concept-learner/SKILL.md` 设计了一个「概念学习卡片」生成器（输入概念名 → 自动产出结构化卡片）
5. **跑 Skill 生成资料**：调用该 Skill 学习三个概念（Agent / 大模型的上下文 / Skill），产出三份 Markdown 卡片
6. **写关系文档**：用自己的话阐述三个概念之间的关系（含 Mermaid 流程图与个人判断）
7. **写 README**：说明调用方式、AI 与人工分工、隐私处理
8. **提交上线**：commit + push 到 GitHub 公开仓库

---

## 二、查阅入口（按重要性排序）

老师只需按以下顺序点开即可快速了解本次作业全貌：

| 顺序 | 文件 | 路径 | 用途 |
| --- | --- | --- | --- |
| ① | README.md | [查看](https://github.com/Zxz124/ai-concept-learning-hub/blob/main/README.md) | 5 分钟读懂仓库用途、Skill 用法、AI/人工分工 |
| ② | Skill 文件 | [查看](https://github.com/Zxz124/ai-concept-learning-hub/blob/main/.workbuddy/skills/concept-learner/SKILL.md) | 我设计的「概念学习卡片」生成器（核心交付） |
| ③ | 概念关系 | [查看](https://github.com/Zxz124/ai-concept-learning-hub/blob/main/learning-materials/concept-relationship.md) | 三概念的关系图与个人判断 |
| ④ | Agent 卡片 | [查看](https://github.com/Zxz124/ai-concept-learning-hub/blob/main/learning-materials/agent.md) | 调用 Skill 产出的样例 1 |
| ⑤ | 上下文卡片 | [查看](https://github.com/Zxz124/ai-concept-learning-hub/blob/main/learning-materials/llm-context.md) | 样例 2 |
| ⑥ | Skill 卡片 | [查看](https://github.com/Zxz124/ai-concept-learning-hub/blob/main/learning-materials/skill.md) | 样例 3 |

**直达文件夹**：[learning-materials/](https://github.com/Zxz124/ai-concept-learning-hub/tree/main/learning-materials) · [.workbuddy/skills/](https://github.com/Zxz124/ai-concept-learning-hub/tree/main/.workbuddy/skills)

---

## 三、关键设计决策（供评分参考）

### 1. Skill 设计中的几个"硬约束"

我在 `concept-learner` Skill 里写了以下纪律，并在执行时严格遵守：

- **来源必须真实可访问**：每个引用 URL 都通过 WebFetch 实测返回 200，写访问日期
- **找不到的来源必须标"待补"，禁止编造**：宁可空着也不许写假论文/假 DOI
- **概念辨析选"用户真会搞混的"**：不是 Wikipedia 摘要堆砌
- **自测题必须分层**：理解 / 应用 / 分析 三档各一道
- **边界与陷阱不许跳过**：这是 Skill 自检里明确写明的"最有价值的部分"

### 2. AI 与人工分工的诚实拆分

| 角色 | 做了什么 |
| --- | --- |
| **AI 起草** | Skill 文件、三张学习卡片、概念关系文档、README 全部内容 |
| **人工决策** | 仓库架构（用项目级 Skill 而非仓库级）、选题范围（选哪三个概念）、安全规则（.gitignore 覆盖范围）、风格要求（必须体现个人理解）、流程卡点（一步一步确认） |
| **坦白** | 三张卡片只经过 AI 一轮自审，未经人类逐字校对；agent.md 末尾已标注 |

### 3. 隐私保护

`.gitignore` 已覆盖：`.env`、`.env.*`、`*.key`、`*.pem`、`*.keystore`、`config/`、`secrets/`、以及按文件名兜底的 `*credentials*`/`*password*`/`*api_key*`/`*secret*`/`.netrc`/`.npmrc` 等。仓库全程没有出现任何 API Key、token、个人隐私数据。

---

## 四、本仓库的可复用性

任何人都可以按以下三步复制这套工作流：

1. Fork 或 Clone 本仓库
2. 在自己环境里打开 WorkBuddy
3. 对 AI 说："**使用 concept-learner 学习 [任意概念名]**" 即可生成同等质量的概念学习卡片

Skill 是声明式的——读 SKILL.md 就能复现完整工作流，不需要任何额外配置。

---

## 五、自查清单结果

提交前自查 7 项全部通过：✅
- ① Skill 文件含 YAML 元数据 ✓
- ② 三份学习资料完整（机制/场景/边界/来源齐全）✓
- ③ 概念关系文档含个人判断 ✓
- ④ README 完整说明调用方式与人工修改过程 ✓
- ⑤ .gitignore 排除敏感文件 ✓
- ⑥ commit + push 完成（commit hash: bff2111）✓
- ⑦ 仓库公开可访问（HTTP 200 无认证、private: False）✓

---

**完整资料已全部公开托管在 GitHub，欢迎老师审阅。**