# 📚 ai-concept-learning-hub

> 个人 AI 概念学习资料生成工具与学习笔记仓库

---

## 这是什么

这是一个**两用仓库**：

1. **工具侧** —— 自带一个名为 `concept-learner` 的项目级 Skill。当你给它一个概念名（不限 AI 领域），它会按固定模板生成结构化的"概念学习卡片"（定义 / 学习目标 / 核心问题 / 机制 / 场景 / 边界 / 辨析 / 自测 / 来源）。
2. **笔记侧** —— 仓库内 `learning-materials/` 目录收录已经生成好的概念学习资料，可以直接阅读、复习、对外分享。

设计哲学：**让"学概念"这件事可重复、可核查、可演进**——而不是每次问 AI 拿一段聊完就丢。

---

## 仓库结构

```
ai-concept-learning-hub/
├── .gitignore                     # 排除 .env、密钥、证书、配置/私密目录等
├── README.md                      # 你正在读的文件
├── .workbuddy/                    # 项目级 Skill 目录（不进工作目录技能表，仅项目内生效）
│   └── skills/
│       └── concept-learner/
│           └── SKILL.md           # 核心：概念学习卡片生成器
└── learning-materials/            # 已生成的学习资料（按概念分子目录/文件）
    ├── .gitkeep
    ├── agent.md                   # 📘 Agent（AI Agent / 自主智能体）
    ├── llm-context.md             # 📘 大模型的上下文（LLM Context Window）
    ├── skill.md                   # 📘 Skill（AI Agent 中的能力单元）
    └── concept-relationship.md    # 🔗 三个概念的关系说明（含 Mermaid 图）
```

---

## Skill 使用

### 调用方式

1. 在 WorkBuddy 中打开本仓库（作为工作目录）
2. 输入任意一个概念名，例如：
   - `使用 concept-learner 学习 Agent`
   - `用 concept-learner 学一下 "LoRA"`
   - `用 concept-learner 解释 "基尼系数"`
3. Skill 自动按 7 步流程生成卡片，输出到 `learning-materials/<概念>.md`

> 💡 概念名是英文 / 中文都可。遇到歧义（如 "Skill"、"Attention"），Skill 会先反问确认你指的是哪个含义。

### 当前 Skill 行为要点

- **强制引用核查**：每个引用 URL 必须经过实际访问验证，不许编造论文 / DOI / 博客标题
- **找不到的来源**：会标 `[来源待补]`，绝不凑数
- **输出三要素必备**：核心机制 + 应用场景 + 边界陷阱，缺一不可
- **自测题分层**：理解 / 应用 / 分析三档，每道附答案要点

详见：`.workbuddy/skills/concept-learner/SKILL.md`

### 已生成的资料

| 文件 | 内容 | 主要来源 |
| --- | --- | --- |
| `learning-materials/agent.md` | Agent 的四要素架构、与 Workflow 的边界 | Lilian Weng 2023 综述 + Anthropic 2024 工程指南 |
| `learning-materials/llm-context.md` | 上下文窗口机制、Lost in the Middle、context rot | arxiv 2307.03172 + Redis 工程博客 |
| `learning-materials/skill.md` | Agent Skills 标准、渐进式披露、与 CLAUDE.md 的区别 | code.claude.com 官方文档 + agentskills.io |
| `learning-materials/concept-relationship.md` | 三个概念作为系统的咬合关系 + 个人判断 | ——（综合卡片 + 个人写作） |

---

## AI 辅助 vs 人工核查

为了对未来的我（或他人）诚实，这里拆清楚**这次会话里 AI 和人工各做了什么**：

### AI（本次会话的对话智能体）生成的部分

- ✅ `.workbuddy/skills/concept-learner/SKILL.md`（232 行，约 8.4 KB）—— 全部由 AI 按用户给的需求规格起草
- ✅ `learning-materials/agent.md`、`llm-context.md`、`skill.md`（共约 250 行）—— AI 按 SKILL.md 工作流生成，包括：
  - WebSearch 多轮检索候选来源
  - WebFetch 验证 URL 可达
  - 按模板填入卡片内容
- ✅ `learning-materials/concept-relationship.md`（162 行）—— AI 起草的关系分析与个人判断
- ✅ `.gitignore` —— AI 按用户规则草拟 + 补全运行时产物兜底
- ✅ 本 README.md（当前文件）

### 人工（用户 Zxz124）做的决策与核查

- 📌 **架构决策**：用项目级 Skill 而非用户级 Skill；放在 `.workbuddy/skills/` 而非 `.claude/skills/`
- 📌 **范围裁剪**：选 Agent / 上下文 / Skill 这三个概念作为初始集
- 📌 **安全规则**：明确指定 `.gitignore` 必须排除 `.env`、`*.key`、`*.pem`、`config/`、`secrets/` 等
- 📌 **风格要求**：明确要求概念关系文档"必须体现个人理解和判断，不能照搬 AI 生成内容"
- 📌 **流程卡点**：每一步完成后等用户确认再进入下一步（避免 AI 一次性跑完全部步骤）

### AI 做了但用户没有逐字审查的部分（坦白说明）

- 三张学习卡片**只做了一轮 AI 自审**（用户授权 AI "逐份阅读、修改、再保存"），未经过人类逐字校对
- 概念关系文档**完全由 AI 起草**，未经人类二次审阅
- 如发现事实错误或表述不顺，欢迎直接改文件并 commit

### 我会怎么建议后续协作

> AI 适合负责：检索 / 整理 / 模板填充 / 来源核查
> 人类适合负责：取舍 / 立场 / 判断 / 终审
>
> 这条分工原则正是这份仓库本身在演示的事。

---

## 隐私与敏感信息

`.gitignore` 当前覆盖范围：

| 类型 | 规则示例 | 说明 |
| --- | --- | --- |
| 环境变量 | `.env`, `.env.*`（保留 `.env.example`） | 用户要求 |
| 密钥 / 证书 | `*.key`, `*.pem`, `*.p12`, `*.pfx`, `*.crt`, `*.cer`, SSH 私钥 | 用户要求 |
| 配置 / 私密目录 | `config/`, `secrets/` | 用户要求 |
| 凭据兜底（按文件名） | `*credentials*`, `*password*`, `*api_key*`, `*access_token*`, `*private_key*`, `*secret*`, `.netrc`, `.npmrc`, `.pypirc`, `.htpasswd` | 用户要求"包含 API Key / 密码 / 隐私" 兜底匹配（gitignore 只能按文件名筛） |
| 运行时产物 | `__pycache__/`, `node_modules/`, `.vscode/`, `.idea/`, `*.log`, `*.tmp` | 顺手加的，避免学习材料误带 |
| 本地草稿 | `drafts/`, `scratch/` | 顺手加的 |

### 还没覆盖但你应该自己评估的

- ❌ 大型二进制文件（建议用 Git LFS 或不放进仓库）
- ❌ 个人身份信息文件（笔记、通讯录）—— 建议不放进仓库，或放到 `private/` 后再加进 `.gitignore`
- ❌ `.DS_Store` / `Thumbs.db` —— 已覆盖

---

## 后续可扩展的方向

- [ ] 给 concept-learner 增加"卡片索引"自动汇总（避免概念多了找不到）
- [ ] 增加 README 模板的多语言版本（中文 / 英文）
- [ ] 增加一个 `templates/` 目录，存放常用 Skill 的写作模板
- [ ] 给每个学习资料加 frontmatter（难度 / 时长 / 标签），方便后续做检索与复习调度

---

## 许可与归属

- 本仓库由用户 Zxz124 创建并维护
- 学习卡片中引用的外部资料版权归原作者所有，链接仅作学习用途
- 仓库本身的组织结构（Skill + learning-materials）欢迎借鉴使用

---

*最后更新：2026-09-07*