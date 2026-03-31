# 权限模型架构

> Claude Code 的每个工具调用都经过权限检查。理解这个模型能帮你更高效地使用它。

## 权限模式

```mermaid
flowchart LR
    subgraph 四种模式
        Default[Default<br/>默认：逐个确认]
        Plan[Plan<br/>规划：只读]
        Auto[Auto<br/>自动：智能判断]
        Bypass[Bypass<br/>跳过：全部允许]
    end

    Default -->|用户体验| 安全但慢
    Plan -->|用户体验| 只看不改
    Auto -->|用户体验| 平衡
    Bypass -->|用户体验| 快但危险
```

## 工具权限分级

### Level 0: 自动允许（只读操作）

```
Read (FileReadTool)     — 读取文件
Glob (GlobTool)         — 搜索文件名
Grep (GrepTool)         — 搜索文件内容
LSP                     — 语言服务器
TaskGet/TaskList         — 查看任务
ToolSearch              — 搜索工具
AskUserQuestion         — 向用户提问
EnterPlanMode           — 进入规划
ExitPlanMode            — 退出规划
```

### Level 1: 首次确认（写操作）

```
Write (FileWriteTool)   — 创建/覆盖文件
Edit (FileEditTool)     — 编辑文件
NotebookEdit            — 编辑 Notebook
WebFetch                — 抓取网页
WebSearch               — 网页搜索
Bash (安全命令)          — ls, git status, npm test 等
Skill                   — 执行技能
```

### Level 2: 每次确认（危险操作）

```
Bash (危险命令)          — rm, git push, chmod 等
EnterWorktree           — 创建 worktree
```

### Level 3: 特殊处理

```
Bash (极危险)            — rm -rf, git push --force
                         — 数据库 DROP 操作
                         — 杀进程 (kill -9)
```

## 权限检查流程

```mermaid
flowchart TD
    A[工具调用请求] --> B{检查权限模式}

    B -->|bypass| C[直接允许]

    B -->|plan| D{是只读工具?}
    D -->|是| C
    D -->|否| E[拒绝]

    B -->|default| F{检查工具级别}
    F -->|Level 0| C
    F -->|Level 1+| G{之前允许过?}
    G -->|是且规则匹配| C
    G -->|否| H[弹窗确认]
    H -->|允许| I{记住选择?}
    I -->|本次会话| J[缓存到会话]
    I -->|永久| K[写入配置]
    I -->|仅本次| C
    H -->|拒绝| E

    B -->|auto| L{转录分类器评估}
    L -->|安全| C
    L -->|需确认| H
```

## Bash 命令分类

```mermaid
flowchart TD
    Cmd[Bash 命令] --> Parse{命令解析}

    Parse --> Safe[安全命令<br/>ls, cat, echo, git status<br/>npm test, python -c]
    Parse --> Write[写入命令<br/>git commit, npm install<br/>mkdir, touch]
    Parse --> Danger[危险命令<br/>rm, git push, chmod<br/>kill, sudo]
    Parse --> Critical[极危险命令<br/>rm -rf /<br/>git push --force origin main<br/>DROP TABLE]

    Safe --> Auto[自动允许]
    Write --> Confirm1[首次确认]
    Danger --> Confirm2[每次确认]
    Critical --> Block[阻止 + 警告]
```

## 配置权限规则

在 `settings.json` 中可自定义:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(git diff)",
      "Write(src/**)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force)"
    ]
  }
}
```

**源码位置**: `src/hooks/toolPermission/`, `src/utils/settings/`

## Auto 模式的秘密

Auto 模式使用 **Transcript Classifier**（转录分类器）:

1. 分析当前对话上下文
2. 评估工具调用的安全性
3. 对明显安全的操作自动放行
4. 对有风险的操作仍然弹窗确认

这是一个 feature-gated 功能 (`TRANSCRIPT_CLASSIFIER`)，可能在未来版本中进一步优化。

**源码位置**: `src/main.tsx` 第 171 行
