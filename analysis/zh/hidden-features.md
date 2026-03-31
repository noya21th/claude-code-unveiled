# 隐藏功能与代号系统

> 87 个编译时开关、60+ 斜杠命令、动物代号体系 — Claude Code 水面下的冰山。

---

## 代号体系

Anthropic 使用**动物名称**作为内部模型代号:

| 代号 | 对应模型 | 来源 |
|------|---------|------|
| **Capybara** | Sonnet 系列 | `src/constants/` |
| **Fennec** | Opus 系列 | `src/constants/` |
| **Numbat** | 未发布模型 | `src/constants/` |

### Capybara v8 已知问题

源码中记录了 Capybara v8 的技术问题:
- 停止序列误触发
- 工具结果解析失败
- 幻觉率 29-30%

---

## Feature Flag 命名混淆

运行时配置使用 `tengu_` + **随机词对**命名，故意隐藏用途:

```
tengu_frond_boric       → 分析数据流紧急开关
tengu_passport_quail    → 记忆提取门控
tengu_moth_copse        → 记忆提取启用
tengu_bramble_lintel    → 记忆提取频率
tengu_cicada_nap_ms     → 后台刷新节流
tengu_slate_prism       → 连接器文本摘要
tengu_slate_thimble     → 非交互式记忆提取
tengu_amber_json_tools  → JSON 工具格式
tengu_tool_pear         → 结构化输出
```

**设计意图**: 即使配置名泄露，也无法推断其功能。

---

## 核心隐藏功能

### Tier 1: 即将发布（成熟度 90%+）

#### KAIROS — 自主助手平台

```
- 助手模式 (/assistant)
- 简报工具 (/brief) — 消息检查点
- 频道系统 — MCP 频道集成
- 定时任务 — cron 调度
- GitHub Webhook — PR 订阅
- 推送通知
```

**控制**: `feature('KAIROS')`, `tengu_kairos`

#### VOICE_MODE — 语音交互

```
- 语音转文本 (STT)
- 语音录制和转录
- 集成 React Hook
```

**控制**: `feature('VOICE_MODE')`

### Tier 2: 开发中（成熟度 60-80%）

#### BUDDY — AI 伙伴精灵

```
- CompanionSprite 动画系统（45K 字节的完整实现）
- 浮动气泡通知
- 伙伴人格系统
```

**控制**: `feature('BUDDY')`

#### ULTRAPLAN / ULTRATHINK

```
ULTRAPLAN — 超级规划器，扩展计划生成
ULTRATHINK — 深度推理模式
TORCH — 推理增强
```

#### WEB_BROWSER_TOOL — 网页浏览器

```
- MCP 工具形式
- 面板 UI
- 内嵌浏览
```

**控制**: `feature('WEB_BROWSER_TOOL')`

### Tier 3: 实验性（成熟度 30-50%）

| 功能 | 说明 |
|------|------|
| COORDINATOR_MODE | 多智能体协调器 |
| VERIFICATION_AGENT | 验证代理 |
| AGENT_TRIGGERS | 定时代理任务 |
| AGENT_MEMORY_SNAPSHOT | 代理记忆快照 |
| EXTRACT_MEMORIES | 自动记忆提取 |
| TEMPLATES | 任务模板和分类 |
| FORK_SUBAGENT | 子代理分支 |
| HISTORY_SNIP | 历史消息片段化 |

### Tier 4: 基础设施

| 功能 | 说明 |
|------|------|
| CHICAGO_MCP | 计算机控制 MCP |
| MCP_SKILLS | MCP 资源作为技能 |
| CONNECTOR_TEXT | 连接器文本（反蒸馏） |
| BRIDGE_MODE | 远程控制桥接 |
| DAEMON | 守护进程模式 |

---

## 隐藏斜杠命令

### 需要 Feature Flag 的命令

| 命令 | 功能 | 开启条件 |
|------|------|---------|
| `/assistant` | 助手模式 | KAIROS |
| `/brief` | Brief 消息 | KAIROS_BRIEF |
| `/bridge` | 远程桥接 | BRIDGE_MODE |
| `/voice` | 语音模式 | VOICE_MODE |
| `/buddy` | AI 精灵 | BUDDY |
| `/ultraplan` | 超级规划 | ULTRAPLAN |
| `/torch` | 推理增强 | TORCH |
| `/workflows` | 工作流脚本 | WORKFLOW_SCRIPTS |
| `/fork` | 子代理分支 | FORK_SUBAGENT |
| `/peers` | 同行消息 | UDS_INBOX |
| `/proactive` | 主动规划 | PROACTIVE |
| `/force-snip` | 强制片段化 | HISTORY_SNIP |
| `/subscribe-pr` | PR 订阅 | KAIROS_GITHUB_WEBHOOKS |

### 仅限 Anthropic 内部的命令

条件: `USER_TYPE === 'ant' && !IS_DEMO`

| 命令 | 功能 |
|------|------|
| `/bughunter` | 代码 bug 自动检测 |
| `/good-claude` | 模型评估工具 |
| `/commit` | 隐藏版 Git 提交 |
| `/commit-push-pr` | 一键提交→推送→创建 PR |
| `/ctx-viz` | 上下文可视化 |
| `/break-cache` | 强制缓存失效 |
| `/mock-limits` | 模拟速率限制 |
| `/reset-limits` | 重置速率限制 |
| `/ant-trace` | Anthropic 内部追踪 |
| `/perf-issue` | 性能问题诊断 |
| `/debug-tool-call` | 工具调用调试 |
| `/agents-platform` | Agents 平台管理 |
| `/autofix-pr` | 自动修复 PR |
| `/share` | 会话分享 |
| `/summary` | 会话摘要 |

---

## 内部 vs 外部构建差异

| 维度 | 内部构建 (ant) | 外部构建 |
|------|---------------|---------|
| Feature flags | 全部可用 | 大部分被 DCE 消除 |
| 斜杠命令 | 60+ | ~45 |
| 卧底模式 | 完整 | 代码不存在 |
| 增强归属 | 贡献百分比等 | 标准归属 |
| 模型回退 | 自动降级 | 标准错误 |
| 调试工具 | `/ctx-viz`, `/debug-tool-call` 等 | 不可用 |
| GrowthBook 覆盖 | `CLAUDE_INTERNAL_FC_OVERRIDES` | 不可用 |

---

## 环境变量覆盖

内部用户可通过环境变量覆盖 feature flags:

```bash
# JSON 格式覆盖（仅 ant 构建）
CLAUDE_INTERNAL_FC_OVERRIDES='{"KAIROS":true,"VOICE_MODE":true}'
```

**源码位置**: `src/services/analytics/growthbook.ts`
