---
description: 工坊管理命令 - 查看状态、验证组件、同步上游、安装到本地
---

# /workshop - 工坊管理命令

根据用户参数执行不同的管理操作。

## /workshop status

查看工坊当前状态。

执行以下操作：
1. 读取 `output/` 目录，统计各类型已创建的组件数量
2. 列出所有已创建组件的名称和简要描述
3. 显示参考库的版本信息（`git log upstream/main --oneline -1`）

输出格式：
```
## 工坊状态

### 已创建组件
- Skills: {n} 个 — {列出名称}
- Agents: {n} 个 — {列出名称}
- Commands: {n} 个 — {列出名称}
- Hooks: {n} 个 — {列出名称}
- Rules: {n} 个 — {列出名称}
- MCP Servers: {n} 个 — {列出名称}

### 参考库版本
{upstream 最新 commit}
```

## /workshop list [type]

按类型列出已创建的组件。

- `type` 可选值：skills、agents、commands、hooks、rules、mcp-servers
- 省略 type 时列出全部

对每个组件，读取文件并提取名称和描述，展示为表格。

## /workshop validate [path]

验证指定组件文件的格式合规性。

验证规则：

| 类型 | 检查项 |
|------|--------|
| Skill | YAML frontmatter 有 name + description；有"激活时机"章节；文件 < 800 行 |
| Agent | YAML frontmatter 有 name + description + tools + model；有"工作流程"章节 |
| Command | YAML frontmatter 有 description；有"使用场景"章节 |
| Hook | 合法 JSON；事件名是有效值（PreToolUse/PostToolUse/SessionStart/SessionEnd/Stop/PreCompact）；有 hooks 字段 |
| Rule | 有核心原则或 DO/DON'T 章节 |

如果 path 省略，验证 `output/` 下所有组件。

输出每个组件的验证结果：PASS 或 FAIL + 具体问题。

## /workshop sync

从上游 ECC 仓库获取更新（仅 fetch + merge，不 pull）。

执行命令：
```bash
git fetch upstream
git merge upstream/main
```

同步后报告：
- 新增/变更的文件数量
- 是否有冲突（如有，列出冲突文件）
- `output/` 不受上游影响（上游不包含此目录）

多设备同步则使用 `git pull origin main` / `git push origin main`（通过自己的 fork）。

## /workshop install [name]

将 `output/` 中已创建的组件安装到本地 `~/.claude/` 目录。

安装规则：
- Skill → 复制到 `~/.claude/skills/{name}/SKILL.md`
- Agent → 复制到 `~/.claude/agents/{name}.md`（如果目录存在）
- Command → 复制到 `~/.claude/commands/{name}.md`
- Hook → 提示用户手动合并到 settings.json（Hook 需要特殊处理）
- Rule → 复制到 `~/.claude/rules/{name}.md`（如果目录存在）

如果 name 省略，列出所有可安装的组件让用户选择。

安装前确认目标路径是否存在，如果已存在询问是否覆盖。

## /workshop catalog

重新生成 `output/` 下所有组件的目录清单。扫描 `output/` 所有子目录，读取每个组件的 frontmatter，生成摘要表格。
