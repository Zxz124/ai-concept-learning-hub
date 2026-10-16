# 0917 课堂要点与课程作业

- **来源**：腾讯文档《0917课堂要点》 <https://docs.qq.com/doc/DV1pubFZxaGFIb3BI>
- **整理日期**：2026-09-24

## 一、课堂要点（环境配置）

1. 安装 vscode、python、git
2. 设置 pip 为国内源

   ```bash
   python -m pip install --upgrade pip
   pip config set global.index-url https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
   ```

3. 设置 vscode 为中文界面
4. 设置 vscode 为自动保存
5. （文档该条为 VS Code 界面截图，未附文字说明）

## 二、课程作业

1. 创建 .py 文件并运行
2. 创建 .ipynb 文件并运行
3. 提交到 github 远程仓库
   - a. 建议使用 workbuddy 创建仓库级别的 skill，完成。

## 三、本仓库完成情况

| 作业项 | 对应文件 / 位置 | 状态 |
| --- | --- | --- |
| 1. 创建 .py 文件并运行 | `learning-materials/scripts/01.py` | ✅ 运行成功，输出 `我也会写程序啦！` |
| 2. 创建 .ipynb 文件并运行 | `learning-materials/scripts/01.ipynb` | ✅ 已运行并保存输出 |
| 3. 提交到 github 远程仓库 | 本仓库 `main` 分支 | ✅ 已推送至 <https://github.com/Zxz124/ai-concept-learning-hub> |
| 3a. 仓库级别的 skill | `.workbuddy/skills/git-push/` | ✅ 已创建：走完 status → add → commit → push 全流程，含安全规则 |

## 四、本机环境（对照 1-4 条要点）

| 要点 | 实际状态 |
| --- | --- |
| 安装 vscode、python、git | VS Code 1.138 / Python 3.12.10 / Git 2.55.0.5 |
| pip 国内源 | 清华 TUNA：`pypi.tuna.tsinghua.edu.cn/simple` |
| vscode 中文界面 | 已启用（Chinese Simplified 扩展） |
| vscode 自动保存 | `files.autoSave: "afterDelay"` |
