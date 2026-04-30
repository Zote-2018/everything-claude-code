# CLAUDE.md

## 项目定位

这是一个 **Claude Code 创作工坊**。项目内含完整的 ECC 参考库（289+ skills、65+ agents、70+ commands、106+ rules），作为创建新组件的模板和灵感来源。用户的创作输出存放在 `output/` 目录。

## 工作模式

### 参考库（只读，来自上游 ECC）

以下目录来自上游项目，是参考材料，**不要修改**：

| 目录 | 内容 | 数量 |
|------|------|------|
| `skills/` | 参考技能模板 | 289 |
| `agents/` | 参考代理模板 | 65 |
| `commands/` | 参考命令模板 | 70 |
| `hooks/` | 参考 Hook 模板 | 2 |
| `rules/` | 参考规则模板 | 106 |
| `schemas/` | 格式校验 schema | hooks.schema.json, plugin.schema.json |

### 创作输出（可写）

- `output/` - 用户创作的新组件，按类型组织
- 使用 `/create` 命令开始交互式创建

### 已启用的插件

- `skill-creator` - 从 git 历史提取模式生成技能
- `mcp-server-dev` - MCP 服务器开发和打包

## 关键命令

- `/create` - 交互式创建新组件（技能/代理/命令/Hook/规则/MCP 服务器）
- `/workshop` - 工坊管理（查看状态、验证、同步上游、安装到本地）

## 格式参考

### Skill（技能）
- 格式：Markdown + YAML frontmatter
- Frontmatter 必须字段：`name`（小写连字符）、`description`
- 必须包含章节：激活时机、工作原理、示例
- 参考：`skills/session-memory/SKILL.md`

### Agent（代理）
- 格式：Markdown + YAML frontmatter
- Frontmatter 必须字段：`name`、`description`、`tools`（数组）、`model`（sonnet/opus/haiku）
- 必须包含章节：角色、工作流程、检查清单
- 参考：`agents/planner.md`

### Command（命令）
- 格式：Markdown + YAML frontmatter
- Frontmatter 必须字段：`description`（一行描述）
- 必须包含章节：使用场景、工作原理、示例
- 参考：`commands/plan.md`

### Hook
- 格式：JSON
- 参考：`hooks/hooks.json`、`schemas/hooks.schema.json`

### Rule（规则）
- 格式：Markdown（无 frontmatter）
- 包含原则、DO/DON'T 示例
- 参考：`rules/common/coding-style.md`

### MCP Server
- 委托给 `mcp-server-dev:build-mcp-server` 插件

## 文件命名

统一使用小写 + 连字符（如 `code-reviewer.md`、`tdd-workflow.md`）

## Git 工作流

- `origin` → 你的 fork（多设备同步用，正常 push/pull）
- `upstream` → 原始 ECC 仓库（仅 fetch + merge，永远不要 pull）

```bash
# 多设备同步：从自己的 fork 拉取和推送
git pull origin main
git push origin main

# 获取上游 ECC 更新（仅 fetch，不 pull）
git fetch upstream && git merge upstream/main
```

## 测试

```bash
node tests/run-all.js
```
