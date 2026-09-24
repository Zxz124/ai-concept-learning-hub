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
| push 网络超时 / 返回 502 | **先原地重试 2–3 次、每次间隔 5–10 秒**——本机出口是间歇性的，实测第 3 次就成功过。仍失败再走下面的连通性预检（本地提交始终安全） |
| GitHub 认证失败 | 引导用户完成一次浏览器登录或配置 Personal Access Token，不要在命令行明文写入密码 |
| `nothing to commit` | 告知用户没有新改动，无需提交 |

## 推送前必做：连通性预检

推送失败时**不要把网络问题和代码问题混在一起**交给用户。先跑：

```bash
git ls-remote origin -h >/dev/null 2>&1 && echo 通 || echo 不通
```

- 通 → 网络没问题，push 失败另有原因（认证、非快进等），按上表处理。
- 不通 → **先重试 push 2–3 次（间隔 5–10 秒）再下判断**：本机出口是间歇性的，"预检失败但紧接着 push 成功"实测发生过（2026-09-24）。
- 连续多次仍不通 → 再走下面的"到 GitHub 的路"排查，并把结论如实告诉用户。

**判断推送是否成功，只认这条命令**（`git status` 里的 `ahead/gone` 在本机可能不准）：

```bash
git ls-remote origin refs/heads/main   # 输出哈希，与 git rev-parse main 比对
```

## 到 GitHub 的路（本机环境实测结论）

**关键前提（2026-09-24 更正）**：git / curl 实际走的出口来自**环境变量** `HTTP_PROXY` / `HTTPS_PROXY` = `http://127.0.0.1:<动态端口>`（历史见过 50791 / 52673 / 62971，**每次会话都变**）。本机**没有** Clash / v2ray / Mihomo 进程，系统代理 `ProxyEnable=0`；雷神加速器是**游戏**加速器、当时并未运行。
→ 这个 127.0.0.1 代理属于**运行环境自带的出口代理**，不是用户的代理软件。它**间歇性可用**。

| 通道 | 结果 |
| --- | --- |
| 环境出口代理（`$HTTPS_PROXY`） | 国内站点 200 ✅；GitHub **时好时坏**：502 与成功交替出现 ⚠️ |
| 直连 https://github.com:443（`curl --noproxy '*'`） | 超时 ❌ |
| SSH 22 端口 | 超时 ❌ |
| SSH over 443（`ssh.github.com:443`） | 超时 / `Connection reset` ❌（SNI 阻断） |
| IPv6 直连 | 无 AAAA 解析 ❌ |
| hosts 加速 | **无效**（属 SNI 阻断，不是 DNS 污染） |

**结论：先靠"重试 2–3 次"消化间歇性 502，不要一见 502 就断言"没有出境出口"。** 只有连续多次不通，才按下面的处置顺序走。

遇到"不通"时的处置顺序：

1. 明确告诉用户："这不是代码问题，是本机没有能到 GitHub 的网络出口"，并给出结论性证据（上面的表格）。
2. 立刻保底：`git bundle create <桌面>/ai-hub-backup.bundle --all`，并在仓库内 `git bundle verify` 确认 "records a complete history"。数据安全永远是第一优先级。
3. 给出替代路径，让用户选：
   - **国内云端备份**：绑定 Gitee 远端（`git remote add gitee <地址>` 后 `git push -u gitee main`）。国内直连实测 200，秒推成功。
   - **等有网络时再推 GitHub**：桌面 `push-to-github.bat` 双击即可（内含 `git ls-remote` 预检）。
4. 绝不建议把 GitHub 账号密码/Token 交给第三方"推送代理"服务。

## 权限：403 与只读令牌（本机特有）

本机 `~/.gitconfig` 为 `credential.https://github.com` 配置了 WorkBuddy 自带的 gh 助手
（`...\.workbuddy\binaries\gh\pkg\bin\gh.exe auth git-credential`），它返回的是**只读令牌**（`ghu_` 前缀，`X-OAuth-Scopes` 为空）。表现是：`git ls-remote` 成功，但 push 被拒：

```
remote: Permission to <owner>/<repo>.git denied to <user>.
fatal: unable to access '...': The requested URL returned error: 403
```

排查顺序：

1. `git credential fill` 看 `username=` 与令牌前缀，确认在用哪张令牌。
2. `curl -sI -H "Authorization: Bearer <token>" https://api.github.com/user` 看 `X-OAuth-Scopes` —— 为空即无写权限。
3. 注意：`/repos/{owner}/{repo}` 返回的 `permissions.admin/push` 表示的是**该用户的角色**，不代表**令牌被授予的范围**，两者都要看，别被 `push: true` 误导。
4. 修复：在**该仓库的 `.git/config`** 里先用空值重置、再挂 GCM。⚠️ 空值必须**直接写进配置文件**，`git config key ""` 不会把空值保存下来：

```ini
[credential "https://github.com"]
	helper =
	helper = manager
```

之后 `git push` 会弹出 GitHub 登录/授权窗口（GCM），由用户本人完成一次授权，凭据存入 Windows 凭据管理器，之后自动复用。

5. 面向新手的一键版：桌面 `login-and-push.bat`（含提示与错误指引）。
6. 绝不把账号密码/令牌写进命令行或仓库，也不用第三方"推送代理"。
