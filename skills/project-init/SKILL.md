---
name: project-init
description: 初始化项目的代理工作流：生成 AGENTS.md（含「任务配置」）、CLAUDE.md、docs/TASK.md、.codex/agents/card_worker.toml，让 task-breakdown / dispatch-task 在这个项目里开箱即用，各家代理（Claude Code、Codex 等）都认。也用于给已有项目补上或修改「任务配置」。用户说「初始化项目」「新建项目」「接入拆卡派卡」「补任务配置」「project init」时使用。
---

# 初始化项目

目标：用户以后在这个项目里只说「拆卡」「派卡」，不用再解释怎么做。通用流程在 `task-breakdown`、`dispatch-task` 技能里；项目只提供参数，参数集中在 `AGENTS.md` 的 `## 任务配置` 一节。

模板在本技能目录的 `templates/` 下：`AGENTS.md`、`CLAUDE.md`、`TASK.md`、`card_worker.toml`。

## 1. 先看现状，不覆盖

在项目根目录（`git rev-parse --show-toplevel`；不是 git 仓库就问用户要不要 `git init`）检查：

| 文件 | 已存在时 |
|---|---|
| `AGENTS.md` | 不重写。只补缺的 `## 任务配置` 一节（已有就按用户要求改），其余内容不动 |
| `CLAUDE.md` | 内容只有 `@AGENTS.md` → 不动。有别的内容 → 告诉用户「建议把这些规则搬进 AGENTS.md，CLAUDE.md 只留 `@AGENTS.md`，这样各家代理读同一份」，用户同意再搬，原文一字不改地搬 |
| `docs/TASK.md`、`docs/tasks/` | 不动（已有卡片的项目，前缀和目录按现有的填进任务配置） |
| `.codex/agents/card_worker.toml` | 不动 |

## 2. 收集配置：先自己找，再问缺的

先从仓库里推断，能推断的不问：
- 项目一句话：`README.md`、`package.json` 的 description。
- 模块：顶层目录（`apps/*`、`packages/*`、`src/` 下的大块）、架构文档。
- 安装依赖 / 提交前检查：锁文件判断包管理器（`pnpm-lock.yaml` → pnpm，`package-lock.json` → npm，`uv.lock` / `pyproject.toml` → uv / pip）；`package.json` scripts 里的 `check`、`lint`、`typecheck`、`test`；`Makefile`；CI 配置。
- 是不是多项目仓库（几个互不 import 的子目录各有自己的 `package.json` 和文档）→ 用子项目表。
- 已有的规则：`README.md`、`CONTRIBUTING.md`、旧的 `CLAUDE.md`、`.cursorrules` 里的「必须 / 不许」→ 候选验收红线。

然后**一次性**问用户推断不出或需要拍板的（一般 2–4 个，不要逐个来回问）：
1. 项目是什么（推断不出时）。
2. 验收红线：给出你从文档里找到的候选，问要不要加减；全新项目可以先空着，以后补。
3. 提交策略：`先问`（每张卡验收后问用户再提交）还是 `直接提交`（验收通过就提交，推送仍要问）。推荐 `直接提交`，派卡连续性好。
4. 提交前检查命令还没有时：确认「由第一张脚手架卡建立」。

用户在代理配置里已有的个人规则，不重复写进项目；项目确实要不同的，才写进「约定」。

## 3. 生成

1. `AGENTS.md`：用模板，把 `{{…}}` 全部替换成实际内容；没内容的行删掉，不留占位符。已有 `AGENTS.md` 时只插入 `## 任务配置` 一节（放在约定之后）。
2. `CLAUDE.md`：模板内容只有 `@AGENTS.md`。
3. `docs/TASK.md`：用模板；配置里没有模块就删掉「模块」列。建空目录 `docs/tasks/` 时放一个 `.gitkeep`。
4. `.codex/agents/card_worker.toml`：原样复制模板。用户指定了 Codex 写卡模型，就填上 `model` / `model_reasoning_effort`。
5. `.gitignore`：确保有 `.env`、`.env.*`、`!.env.example`；其他条目按技术栈补，已有的不删。

## 4. 收尾

- 列出新建 / 修改了哪些文件，以及「任务配置」的最终内容。
- 提交照用户的规则：没有授权就先问；提交信息用 `chore: 初始化代理工作流配置`。
- 告诉用户下一步：「说『拆卡』并给出要做什么或参考哪份文档」。
