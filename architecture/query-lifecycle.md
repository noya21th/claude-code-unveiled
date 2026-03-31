# 请求生命周期

> 从用户输入到 Claude 响应的完整链路。

## 完整流程

```mermaid
sequenceDiagram
    participant User as 用户
    participant REPL as REPL 界面
    participant Query as QueryEngine
    participant Prompt as Prompt 构建
    participant API as Claude API
    participant Tool as 工具执行

    User->>REPL: 输入消息
    REPL->>Query: submitMessage()

    Note over Query,Prompt: 1. 构建 System Prompt
    Query->>Prompt: fetchSystemPromptParts()
    Prompt->>Prompt: buildEffectiveSystemPrompt()
    Prompt->>Prompt: resolveSystemPromptSections()
    Prompt->>Prompt: 加载 CLAUDE.md
    Prompt->>Prompt: 加载 Memory
    Prompt->>Prompt: 注入环境信息

    Note over Query,API: 2. API 调用
    Query->>API: messages.create(stream)
    API-->>Query: 流式返回

    alt 纯文本响应
        Query-->>REPL: 渲染文本
        REPL-->>User: 显示响应
    else 工具调用
        Note over Query,Tool: 3. 工具执行循环
        Query->>Tool: 权限检查
        Tool->>Tool: 执行工具
        Tool-->>Query: 返回结果
        Query->>API: 发送工具结果 (继续对话)
        Note over Query,API: 循环直到无更多工具调用
    end

    Note over Query: 4. 后处理
    Query->>Query: 成本追踪
    Query->>Query: 检查自动压缩阈值
    Query->>Query: 记忆提取 (如启用)
    Query->>Query: 标题建议 (后台)
```

## 各阶段详解

### 阶段 1: System Prompt 构建

```mermaid
flowchart TD
    A[fetchSystemPromptParts] --> B[getSystemPrompt]
    A --> C[getSystemContext]
    A --> D[getUserContext]

    B --> E{buildEffectiveSystemPrompt}
    E --> F[静态部分<br/>身份+规则+工具说明]
    E --> G[__BOUNDARY__]
    E --> H[动态部分<br/>Memory+环境+MCP]

    C --> I[Git 状态<br/>分支/dirty/remote]
    D --> J[CLAUDE.md 文件<br/>项目级+全局级]

    F & G & H & I & J --> K[最终 System Prompt]
```

关键优化: 静态部分可被 API prompt caching，动态部分每次重算。

### 阶段 2: API 调用

```
请求体结构:
{
  model: "claude-opus-4-6",
  max_tokens: 8000,           // 初始值（非 32k）
  system: [系统提示],
  messages: [对话历史],
  tools: [工具定义],
  stream: true,
  metadata: { user_id: hash }
}
```

**重试策略**:
- 429 (Rate Limit): 等待 + 重试
- 529 (Overloaded): 仅前台任务重试
- max_tokens 命中: 自动升级到 64k 重试

### 阶段 3: 工具执行循环

```mermaid
flowchart TD
    A[API 返回 tool_use] --> B{权限检查}
    B -->|通过| C[执行工具]
    B -->|拒绝| D[返回拒绝结果]

    C --> E[获取工具结果]
    E --> F[结果注入对话]
    F --> G[再次调用 API]

    G --> H{响应类型?}
    H -->|tool_use| A
    H -->|text| I[结束循环]
    H -->|stop| I
```

单次对话中可能循环数十次（复杂任务）。

### 阶段 4: 后处理

```
并行执行:
├─ addToTotalSessionCost()      # 成本记录
├─ checkAutoCompactThreshold()  # 上下文压缩检查
├─ extractMemories()            # 记忆提取 (feature-gated)
├─ suggestTitle()               # 标题建议 (后台，不重试)
└─ updateSessionHistory()       # 历史记录
```

## 关键数字

| 节点 | 参数 |
|------|------|
| 初始 max_tokens | 8,000 |
| 升级 max_tokens | 64,000 |
| 上下文窗口 | 200,000 |
| 自动压缩阈值 | ~167,000 |
| 工具并行调用 | 支持 |
| 流式输出 | 始终启用 |

**源码位置**: `src/QueryEngine.ts` (~46K 行), `src/query.ts`
