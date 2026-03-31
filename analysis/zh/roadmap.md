# 未来路线图

> 从 87 个 feature flags 和缺失模块中推测 Claude Code 的发展方向。所有判断基于代码量和跨文件引用数等可验证指标。

---

## 功能成熟度评估方法

本文使用以下可验证指标评估功能成熟度，而非主观百分比:

| 指标 | 说明 |
|------|------|
| 专属文件数 | 该功能目录下的源文件数量 |
| 代码行数 | 专属模块的代码量 |
| 跨文件引用数 | 其他文件中引用该功能的文件数量 |
| 基础设施证据 | 是否有跨平台二进制、配置 schema、GrowthBook 门控等 |

---

## Tier 1: 深度集成（引用 100+ 文件）

### KAIROS — 自主助手平台

**这是 Claude Code 最大的未发布功能。**

| 指标 | 数据 |
|------|------|
| 跨文件引用 | **210 个文件**（全代码库最高） |
| 专属模块 | `src/assistant/`, `src/tools/BriefTool/` |
| 代码行数 | 专属 597 行 + 大量散布在其他模块中 |
| GrowthBook 门控 | `tengu_kairos`, `tengu_kairos_cron_config`, `tengu_kairos_brief` |
| 子系统 | Assistant Mode, Brief Tool, Channels, Cron Tasks, GitHub Webhooks, Push Notifications, Dream |

**判断依据**: 210 个文件引用意味着 KAIROS 已深度嵌入核心架构，不是独立实验。

### Voice Mode — 语音交互

| 指标 | 数据 |
|------|------|
| 跨文件引用 | **38 个文件** |
| 专属模块 | `src/voice/` |
| 跨平台二进制 | `vendor/audio-capture/` 含 6 个平台的 .node 文件 |
| 组件 | voiceStreamSTT, useVoiceIntegration |

**判断依据**: 跨平台原生二进制已编译完成（arm64/x64 × darwin/linux/win32），说明已过工程验证阶段。

---

## Tier 2: 积极开发（引用 15-50 文件）

### Coordinator Mode — 多智能体协调

| 指标 | 数据 |
|------|------|
| 跨文件引用 | **45 个文件** |
| 专属模块 | `src/coordinator/` (1 文件, 369 行) |
| 环境变量 | `CLAUDE_CODE_COORDINATOR_MODE` |
| System Prompt | 专用 prompt（优先级 1，高于默认） |

**判断依据**: 45 个引用 + 专用 system prompt 说明框架已搭建，但专属代码量小，核心逻辑可能尚在内部 monorepo。

### UltraPlan + UltraThink + Torch

- **UltraPlan**: 扩展计划生成，含选择对话框
- **UltraThink**: 深度推理模式
- **Torch**: 推理增强

这三者在 `src/commands.ts` 中各有独立命令注册，可能在未来合并为统一的"深度思考"模式。

---

## Tier 3: 早期实验（引用 < 15 文件，专属代码量小）

### Buddy — AI 精灵伙伴

| 指标 | 数据 |
|------|------|
| 跨文件引用 | **14 个文件** |
| 专属模块 | `src/buddy/` (6 文件, 1,298 行) |
| 核心组件 | CompanionSprite (514行动画), Companion (133行逻辑), useBuddyNotification |

**判断依据**: 专属代码量可观（1,298 行），但跨文件引用少，说明尚未深度集成到核心流程。

### 其他实验性功能

| 功能 | 专属代码 | 说明 |
|------|---------|------|
| Agent Triggers | 分散在 tasks/ | 定时代理任务（cron 调度） |
| Memory Extract | feature flag 门控 | 自动从对话提取记忆 |
| Agent Memory Snapshot | feature flag 门控 | 代理级记忆快照 |
| Templates | feature flag 门控 | 任务模板和分类系统 |
| Web Browser Tool | 0 个专属文件 | MCP 工具形式，可能完全在内部 monorepo |

---

## Tier 4: 108 个缺失模块

npm 发布版中有约 108 个模块被 dead-code-eliminated，只存在于 Anthropic 内部 monorepo:

| 类别 | 包含 |
|------|------|
| **守护进程** | daemon 系统、后台任务调度 |
| **技能发现** | skill search、skill generator |
| **浏览器** | WebBrowserTool、REPL Tool |
| **监控** | MonitorTool、性能追踪 |
| **远程** | Bridge 完整服务端、CCR 镜像 |
| **协作** | 频道系统、团队记忆同步 |
| **AI 伙伴** | Buddy 精灵完整系统 |

---

## 发展方向推测

### 短期

1. **Voice Mode 公开发布** — 跨平台二进制已就绪，38 个文件引用
2. **UltraThink/UltraPlan** — 深度推理模式
3. **Web Browser Tool** — MCP 集成

### 中期

4. **KAIROS 逐步开放** — 210 个文件引用表明已深度集成
5. **Agent Triggers** — CI/CD 自动化
6. **Coordinator Mode** — 多代理协作

### 长期

7. **AI 开发团队** — Coordinator + KAIROS + Memory
8. **自主开发循环** — Agent 自主发现问题 → 修复 → 验证
9. **团队协作** — 多人 + 多 Agent 混合工作流

---

## 竞争格局影响

| 功能 | Claude Code | Cursor | Cline |
|------|------------|--------|-------|
| 自主代理 (KAIROS) | 210 文件引用 | 无 | 无 |
| 语音交互 | 跨平台二进制就绪 | 无 | 无 |
| 多代理协调 | 45 文件引用 | 无 | 无 |
| AI 伙伴 | 1,298 行专属代码 | 无 | 无 |
| 深度推理 | 独立命令注册 | 部分 | 无 |

**结论**: Claude Code 正在从"AI 编码助手"向"AI 开发平台"演进，其功能规划在代码层面远超当前竞品。

> **注意**: 以上时间线为基于代码证据的推测，非 Anthropic 官方路线图。实际发布节奏取决于内部优先级和质量标准。
