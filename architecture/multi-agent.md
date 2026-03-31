# 多代理协调架构

> Claude Code 支持子代理派生和多代理协调，是其处理复杂任务的核心能力。

## 代理层级

```mermaid
flowchart TD
    Main[主线程<br/>REPL Main Thread] -->|Agent 工具| Sub1[子代理 1<br/>Explore 类型]
    Main -->|Agent 工具| Sub2[子代理 2<br/>通用类型]
    Main -->|Agent 工具| Sub3[子代理 3<br/>自定义类型]

    Sub1 -->|只读工具| Files[Glob/Grep/Read]
    Sub2 -->|完整工具| AllTools[所有工具]
    Sub3 -->|受限工具| Limited[代理定义的工具集]

    style Main fill:#f96,stroke:#333
    style Sub1 fill:#6cf,stroke:#333
    style Sub2 fill:#6cf,stroke:#333
    style Sub3 fill:#6cf,stroke:#333
```

## 子代理类型

| 类型 | 工具集 | 用途 | 模型 |
|------|--------|------|------|
| `general-purpose` | 全部 | 复杂多步骤任务 | 继承父级 |
| `Explore` | 只读（无 Edit/Write） | 代码库探索 | 继承父级 |
| `Plan` | 只读（无 Edit/Write） | 设计实现方案 | 继承父级 |
| 内置代理 (60+) | 按定义 | 专业领域任务 | 可指定 |
| 自定义代理 | 按配置 | 用户定义 | 可指定 |

## 子代理模型路由

```mermaid
flowchart TD
    A[子代理启动] --> B{CLAUDE_CODE_SUBAGENT_MODEL?}
    B -->|设置了| C[使用环境变量指定的模型]
    B -->|未设置| D{工具指定了模型?}
    D -->|是| E{与父级同层级?}
    E -->|是| F[使用父级完整模型字符串]
    E -->|否| G[使用工具指定的模型]
    D -->|否| H{代理配置了模型?}
    H -->|是| I[使用代理配置]
    H -->|否| J[继承父级模型]
```

**关键细节**: 如果代理配置写 `model: "opus"`，但父级是 `claude-opus-4-6`，系统会匹配到同家族并使用父级的完整模型 ID，避免意外降级。

**源码位置**: `src/utils/model/agent.ts` 第 37-95 行

## Coordinator 模式

启用 `COORDINATOR_MODE` 后，进入多 Worker 编排:

```mermaid
flowchart TD
    Coord[Coordinator<br/>协调器] -->|分配任务| W1[Worker 1<br/>前端开发]
    Coord -->|分配任务| W2[Worker 2<br/>后端开发]
    Coord -->|分配任务| W3[Worker 3<br/>测试]

    W1 -->|报告进度| Coord
    W2 -->|报告进度| Coord
    W3 -->|报告进度| Coord

    Coord -->|汇总结果| User[用户]
```

Coordinator 使用专用 system prompt（优先级 1），高于默认 prompt。

**源码位置**: `src/coordinator/`

## Worktree 隔离

子代理可在 Git worktree 中运行，获得仓库的隔离副本:

```
主代理: /project (main branch)
  └─ 子代理: /tmp/worktree-xxx (临时分支)
       ├─ 完整的仓库副本
       ├─ 独立的文件修改
       └─ 完成后: 合并或清理
```

**好处**: 子代理的文件修改不影响主工作目录。

## KAIROS 自主代理（未公开）

启用 KAIROS/PROACTIVE 后的自主模式:

```
特有机制:
├─ Tick 机制 — 定时唤醒执行任务
├─ SleepTool — 主动休眠等待
├─ 首次唤醒行为 — 自动扫描环境
├─ 终端焦点感知 — 检测用户是否在看
├─ Brief 工具 — 消息检查点
└─ 偏向行动 — 减少确认，直接执行
```

**源码位置**: `src/constants/prompts.ts` (KAIROS/Proactive 部分)
