# agent-skills

通用的 AI 代理工作流技能：拆卡、派卡验收、项目初始化。Claude Code、Codex 等支持 `SKILL.md` 的代理都能用。

| 技能 | 用途 | 触发说法 |
|---|---|---|
| `project-init` | 给项目生成 `AGENTS.md`（含「任务配置」）、`CLAUDE.md`、`docs/TASK.md`、Codex 写卡子代理 | 初始化项目 / 补任务配置 |
| `task-breakdown` | 把需求拆成 `docs/tasks/` 下的任务卡和 `docs/TASK.md` 总表 | 拆卡 / 加一张卡 / 更新进度 |
| `dispatch-task` | 把可开工的卡派给子代理写代码，主会话只验收、改状态、提交 | 派卡 / 做 t05 / 验收 t05 |

## 安装

```bash
# 本机全局（所有项目、所有代理）
npx skills add BiYuqi/agent-skills -g -a '*'

# 装进某个项目（写入 .agents/skills/ 和 skills-lock.json，随仓库提交）
npx skills add BiYuqi/agent-skills

# 更新
npx skills update
```

## 设计

技能只写通用流程；项目差异全部放在项目根目录 `AGENTS.md` 的 `## 任务配置` 一节（卡号前缀、模块、检查命令、验收红线、提交策略……），技能运行时读取。新项目用 `project-init` 生成这一节，以后改流程参数只改项目里这一处。

派卡时子代理怎么开取决于工具：Claude Code 用 Agent 工具（可指定模型），Codex 用子代理（项目里的 `.codex/agents/card-worker.toml`），都不支持时在同一会话里做完再单独验收。

## 许可证

MIT
