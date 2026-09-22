---
name: git-push
description: 走完 Git 提交与推送全流程（status → add → commit → push），把当前项目的改动安全地提交到本地仓库并推送到远程（如 GitHub）。This skill should be used when the user asks to 提交作业、推送代码、push、commit、git 提交、保存到仓库、上传改动, or wants to complete the git workflow for the current project.
agent_created: true
---

# Git 提交推送全流程（git-push）

## 目的

为本仓库（ai-concept-learning-hub，远程为 `github.com/Zxz124/ai-concept-learning-hub`）完成一次完整、安全、可追溯的 Git 流程：查看改动 → 暂存 → 提交 → 推送。面向 Git 新手，每一步都要让用户看得懂、可确认。

## 前置检查（按顺序执行）

1. 确认当前工作目录在 Git 仓库内：`git rev-parse --is-inside-work-tree`
2. 了解改动全貌：`git status --short` 与 `git diff --stat`
3. 确认远程：`git remote -v`。若无远程，流程止步于"本地提交完成"，并明确告知用户"只提交到了本地，未推送远程"

## 提交流程

1. **审阅改动**：向用户展示将要提交的文件清单（新增 / 修改 / 删除），等用户确认或直接按用户点名的文件操作。
2. **敏感文件检查**：`.env`、`*.key`、`*.pem`、`config/`、`secrets/`、文件名含 `password` / `token` / `secret` / `api_key` / `credentials` 的文件一律**不得提交**（`.gitignore` 已覆盖，但 add 前仍需人工核对一遍 status 输出）。
3. **暂存**：优先逐个 `git add <文件>` 用户点名的文件；仅当用户明确说"全部提交"时才 `git add -A`。
4. **提交**：`git commit -m "<信息>"`。提交信息一行即可，说清"改了什么 + 为什么"，例如 `完成 01.ipynb 第一个 print 练习`。不加 `--no-verify`，不改写已有历史。
5. **推送**：`git push`；首次推送或无 upstream 时用 `git push -u origin <分支名>`（默认 `main`）。无远程则跳过此步。
6. **确认与汇报**：`git log --oneline -5` 查看最近提交，向用户汇报：提交号、信息、是否已推送成功。

## 硬性安全规则（任何情况下不得违反）

- 禁止 `git push --force` / `-f`
- 禁止 `git reset --hard`、`git clean`、`git checkout -- <file>`、`git rebase` 等破坏性命令
- 禁止提交敏感文件（见提交流程第 2 步）
- push 被拒（non-fast-forward）时：先 `git pull`，若出现冲突则展示冲突内容并请用户决定，**绝不强推**
- 任何不可逆操作，先向用户说明后果并获得明确同意

## 常见问题处理

| 现象 | 处理 |
| --- | --- |
| `Please tell me who you are` | 引导执行 `git config --global user.name "<姓名>"` 与 `git config --global user.email "<邮箱>"` 后重试 |
| push 提示 `non-fast-forward` | 先 `git pull` 合并远程改动，再 `git push`；有冲突走安全规则 |
| push 网络超时 | 重试一次；仍失败则检查网络/代理，告知用户稍后再推（本地提交已安全） |
| GitHub 认证失败 | 引导用户完成一次浏览器登录或配置 Personal Access Token，不要在命令行明文写入密码 |
| `nothing to commit` | 告知用户没有新改动，无需提交 |
