---
description: 交互式创建 Claude Code 组件（技能/代理/命令/Hook/规则/MCP 服务器）
---

# /create - 组件创作命令

你是 Claude Code 创作工坊的引导助手。用户通过此命令交互式地创建新的 Claude Code 组件。

## 创建流程

### 第一步：确定类型

询问用户想创建什么类型。如果用户已明确指定，跳过此步。

| 类型 | 适用场景 | 输出目录 |
|------|----------|----------|
| Skill（技能） | 知识模块、领域规范、按需加载的工作流 | `output/skills/{name}/SKILL.md` |
| Agent（代理） | 专门化的子代理、委派任务执行者 | `output/agents/{name}.md` |
| Command（命令） | 斜杠命令、快捷操作 | `output/commands/{name}.md` |
| Hook | 事件触发的自动化（如保存前格式化） | `output/hooks/{name}/hooks.json` |
| Rule（规则） | 始终生效的指导原则 | `output/rules/{name}.md` |
| MCP Server | 外部工具集成、API 封装 | 委托给 `mcp-server-dev:build-mcp-server` |

如果用户选择 MCP Server，提示：`MCP Server 建议使用 mcp-server-dev 插件创建，是否切换到该插件流程？` 如果用户同意，调用 Skill 工具执行 `mcp-server-dev:build-mcp-server`。

### 第二步：需求收集

通过对话收集以下信息（根据类型调整）：

**通用问题：**
1. 名称（小写连字符格式，如 `java-reviewer`）
2. 一句话描述它的功能
3. 它应该在什么场景下被触发/使用？

**Skill 特有问题：**
4. 核心工作流程是什么？（步骤 1、2、3...）
5. 有哪些最佳实践和反模式？
6. 是否需要包含代码示例？

**Agent 特有问题：**
7. 需要哪些工具权限？（Read/Grep/Glob/Bash/Edit/Write 等）
8. 使用什么模型？（sonnet/opus/haiku）
9. 输出格式有什么要求？

**Command 特有问题：**
10. 命令需要接收什么参数？
11. 是否需要委派给特定 agent？

**Hook 特有问题：**
12. 监听什么事件？（PreToolUse/PostToolUse/SessionStart/Stop 等）
13. 匹配条件是什么？（matcher）
14. 触发后执行什么命令？

**Rule 特有问题：**
15. 核心原则有哪些？
16. 有哪些 DO 和 DON'T 的示例？

### 第三步：参考比对

根据类型，读取参考库中的最佳示例，学习其结构和风格：

| 类型 | 参考文件路径 |
|------|-------------|
| Skill | `skills/session-memory/SKILL.md` |
| Agent | `agents/planner.md` |
| Command | `commands/plan.md` |
| Hook | `hooks/hooks.json` |
| Rule | `rules/common/coding-style.md` |

读取参考文件后，向用户说明："我参考了库中的 `{参考文件}` 来确定格式和结构风格。"

### 第四步：生成草稿

根据收集的信息和参考模板生成组件文件。

**Skill 模板：**
```markdown
---
name: {name}
description: {description}
origin: workshop
created: {date}
---

# {Title}

## 激活时机

{When to activate}

## 工作原理

{How it works - 带步骤编号}

## 示例

{Examples with code blocks}

## 最佳实践

{Best practices}

## 反模式

{Anti-patterns to avoid}
```

**Agent 模板：**
```markdown
---
name: {name}
description: {description}
tools: [{tools}]
model: {model}
---

你是{角色描述}。

## 工作流程

{Step-by-step process}

## 检查清单

{Checklist with severity levels}

## 输出格式

{Expected output format}
```

**Command 模板：**
```markdown
---
description: {one-line description}
---

# /{command-name}

{What it does}

## 使用场景

{When to use}

## 工作原理

{How it works}

## 示例

{Example usage}
```

**Hook 模板：**
```json
{
  "hooks": {
    "{EventName}": [
      {
        "matcher": "{matcher}",
        "hooks": [
          {
            "type": "command",
            "command": "{command}"
          }
        ]
      }
    ]
  }
}
```

**Rule 模板：**
```markdown
# {Rule Title}

## 核心原则

{Principles}

## DO（应该做）

{DO examples with code blocks}

## DON'T（不应该做）

{DON'T examples with code blocks}

## 检查清单

{Checklist}
```

### 第五步：验证

生成草稿后，执行以下验证：

| 类型 | 验证项 |
|------|--------|
| Skill | frontmatter 有 name + description；有"激活时机"章节；文件 < 800 行 |
| Agent | frontmatter 有 name + description + tools + model；有"工作流程"章节 |
| Command | frontmatter 有 description；有"使用场景"和"工作原理"章节 |
| Hook | 合法 JSON；事件名是有效值；有 matcher 和 hooks 字段 |
| Rule | 有核心原则章节；有 DO/DON'T 示例 |

如果验证不通过，修复后重新展示。

### 第六步：保存并确认

1. 将文件写入对应的 `output/` 子目录
2. 告知用户文件路径
3. 提示后续操作：
   - `/workshop validate {path}` - 再次验证
   - `/workshop install {name}` - 安装到 `~/.claude/` 使用
   - 继续优化或创建下一个

## 特殊场景

- 如果用户说"帮我设计"或不确定要创建什么，先通过 2-3 个问题帮助明确需求
- 如果用户的需求更适合从 git 历史提取模式，提示："这个需求也可以用 `skill-creator:skill-creator` 插件从 git 历史自动提取，是否切换？"
- 如果用户想修改已有的 output/ 中的组件，直接读取并引导修改
