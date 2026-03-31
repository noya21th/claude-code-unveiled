# Claude Code 隐藏配置项完全手册

> 基于 v2.1.88 源码提取的所有 feature flags、隐藏命令和内部配置。

---

## 一、编译时 Feature Flags（87个）

这些开关在构建时通过 `bun:bundle` 的 `feature()` 函数决定是否编译进最终产物。普通用户版本中大部分被 dead-code-eliminated。

### 核心功能代号

| 代号 | 功能 | 状态 |
|------|------|------|
| **KAIROS** | 助手/协作平台框架 | 内部 |
| **PROACTIVE** | 主动式任务规划 | 内部 |
| **BRIDGE_MODE** | 移动/Web 远程控制 | 内部 |
| **BUDDY** | AI 伙伴精灵系统 | 内部 |
| **VOICE_MODE** | 语音输入/输出 | 内部 |
| **COORDINATOR_MODE** | 多智能体协调 | 部分公开 |
| **ULTRAPLAN** | 超级规划器 | 内部 |
| **ULTRATHINK** | 扩展深度推理 | 内部 |
| **TORCH** | 推理增强 | 内部 |

**源码位置**: `src/commands.ts`

### 上下文和压缩

| 代号 | 功能 |
|------|------|
| CONTEXT_COLLAPSE | 智能上下文折叠 |
| REACTIVE_COMPACT | 反应式会话压缩 |
| CACHED_MICROCOMPACT | 微型缓存压缩 |
| TIME_BASED_MICROCOMPACT | 时间触发压缩 |
| HISTORY_SNIP | 历史消息片段化 |
| BG_SESSIONS | 后台会话管理 |
| AWAY_SUMMARY | 离开时自动摘要 |

### AI 模型与推理

| 代号 | 功能 |
|------|------|
| TRANSCRIPT_CLASSIFIER | 转录分类器（自动模式权限） |
| VERIFICATION_AGENT | 验证智能体 |
| CONNECTOR_TEXT | 连接器文本摘要（反蒸馏） |

### 记忆与协作

| 代号 | 功能 |
|------|------|
| EXTRACT_MEMORIES | 自动记忆提取 |
| TEMPLATES | 任务模板系统 |
| AGENT_MEMORY_SNAPSHOT | 代理记忆快照 |
| AGENT_TRIGGERS | 定时代理任务 |
| AGENT_TRIGGERS_REMOTE | 远程代理触发 |
| UDS_INBOX | Unix Socket 同行通信 |

### MCP 与工具

| 代号 | 功能 |
|------|------|
| CHICAGO_MCP | 计算机使用 MCP 服务器 |
| MCP_SKILLS | MCP 资源作为技能 |
| MCP_RICH_OUTPUT | MCP 富文本输出 |
| MONITOR_TOOL | 后台监控工具 |
| WEB_BROWSER_TOOL | 网页浏览器工具 |
| EXPERIMENTAL_SKILL_SEARCH | 技能搜索 |

### 网络与远程

| 代号 | 功能 |
|------|------|
| CCR_REMOTE_SETUP | 远程设置 |
| CCR_MIRROR | 会话镜像 |
| DIRECT_CONNECT | 直连模式 |
| SSH_REMOTE | SSH 远程连接 |
| LODESTONE | Lodestone 框架集成 |
| DAEMON | 守护进程模式 |

### 工作流与自动化

| 代号 | 功能 |
|------|------|
| WORKFLOW_SCRIPTS | 工作流脚本系统 |
| HOOK_PROMPTS | Hook 提示支持 |
| RUN_SKILL_GENERATOR | 技能生成器 |
| BYOC_ENVIRONMENT_RUNNER | 自带环境运行（企业） |
| FORK_SUBAGENT | 子代理分支 |

### 调试与性能

| 代号 | 功能 |
|------|------|
| DUMP_SYSTEM_PROMPT | 转储系统提示 |
| PERFETTO_TRACING | Perfetto 性能追踪 |
| SLOW_OPERATION_LOGGING | 慢操作日志 |
| HARD_FAIL | 硬失败模式 |

---

## 二、运行时动态配置（tengu_* gates）

通过 GrowthBook 远程下发，可动态开关。命名模式: `tengu_` + 随机词对（故意混淆用途）。

### Killswitch（紧急开关）

| 配置名 | 控制目标 |
|--------|---------|
| `tengu_frond_boric` | 分析数据上报（Datadog/FirstParty） |
| `tengu_read_dedup_killswitch` | 重复读取去重 |
| `tengu_compact_line_prefix_killswitch` | 压缩行前缀 |

### 自动模式控制

| 配置名 | 说明 |
|--------|------|
| `tengu_auto_mode_config` | 自动模式允许的模型列表 |
| `tengu_transcript_classifier` | 转录分类器配置 |

### 记忆系统

| 配置名 | 说明 |
|--------|------|
| `tengu_bramble_lintel` | 记忆提取频率（默认 1） |
| `tengu_moth_copse` | 记忆提取启用开关 |
| `tengu_passport_quail` | 记忆提取门控 |
| `tengu_slate_thimble` | 非交互式记忆提取 |

### 会话压缩

| 配置名 | 说明 |
|--------|------|
| `tengu_sm_compact_config` | 会话记忆压缩配置 |
| `tengu_cicada_nap_ms` | 后台刷新节流（毫秒） |

### Bridge 远程

| 配置名 | 说明 |
|--------|------|
| `tengu_bridge_poll_interval_config` | 轮询间隔 |
| `tengu_sessions_elevated_auth_enforcement` | 会话认证升级 |

### 频道与协作

| 配置名 | 说明 |
|--------|------|
| `tengu_harbor` | 频道消息权限 |
| `tengu_harbor_permissions` | 频道权限检查 |
| `tengu_harbor_ledger` | 频道消息日志 |
| `tengu_mcp_channel_gate` | MCP 频道门控 |

### 其他

| 配置名 | 说明 |
|--------|------|
| `tengu_max_version_config` | 最大版本限制 |
| `tengu_ant_model_override` | 内部模型覆盖 |
| `tengu_tool_pear` | 结构化输出（strict tools） |
| `tengu_amber_json_tools` | JSON 工具格式（token 高效） |
| `tengu_event_sampling_config` | 事件采样率 |

---

## 三、斜杠命令完整列表

### 公开命令（50+）

| 命令 | 功能 | 命令 | 功能 |
|------|------|------|------|
| `/add-dir` | 添加工作目录 | `/model` | 模型选择 |
| `/advisor` | AI 顾问 | `/memory` | 记忆管理 |
| `/agents` | 代理管理 | `/mcp` | MCP 管理 |
| `/branch` | 分支管理 | `/mobile` | 移动 QR 码 |
| `/chrome` | Chrome 集成 | `/output-style` | 输出样式 |
| `/clear` | 清屏 | `/passes` | 通行证 |
| `/compact` | 压缩上下文 | `/permissions` | 权限管理 |
| `/config` | 配置 | `/plan` | 规划模式 |
| `/context` | 上下文管理 | `/plugin` | 插件管理 |
| `/copy` | 复制最后消息 | `/privacy-settings` | 隐私设置 |
| `/cost` | 成本统计 | `/rate-limit-options` | 速率限制 |
| `/desktop` | 桌面设置 | `/remote-env` | 远程环境 |
| `/diff` | 差异对比 | `/rename` | 重命名会话 |
| `/effort` | 工作量指示 | `/review` | 代码审查 |
| `/exit` | 退出 | `/rewind` | 回退消息 |
| `/export` | 导出会话 | `/sandbox-toggle` | 沙箱开关 |
| `/extra-usage` | 额外额度 | `/session` | 远程会话 |
| `/fast` | 快速模式 | `/skills` | 技能列表 |
| `/feedback` | 反馈 | `/stats` | 统计 |
| `/files` | 文件列表 | `/status` | 状态 |
| `/help` | 帮助 | `/stickers` | 贴纸 |
| `/hooks` | Hook 管理 | `/tasks` | 任务管理 |
| `/ide` | IDE 集成 | `/theme` | 主题 |
| `/insights` | 会话分析 | `/upgrade` | 升级 |
| `/keybindings` | 快捷键 | `/vim` | Vim 模式 |
| `/ultrareview` | 超级审查 | `/teleport` | 会话传送 |

### 隐藏命令（需 feature flag）

| 命令 | 功能 | 开启条件 |
|------|------|---------|
| `/assistant` | 助手模式 | KAIROS |
| `/brief` | Brief 消息 | KAIROS / KAIROS_BRIEF |
| `/bridge` | 远程控制桥接 | BRIDGE_MODE |
| `/voice` | 语音模式 | VOICE_MODE |
| `/buddy` | AI 伙伴精灵 | BUDDY |
| `/ultraplan` | 超级规划 | ULTRAPLAN |
| `/torch` | 推理增强 | TORCH |
| `/workflows` | 工作流脚本 | WORKFLOW_SCRIPTS |
| `/fork` | 子代理分支 | FORK_SUBAGENT |
| `/peers` | 同行消息 | UDS_INBOX |
| `/proactive` | 主动规划 | PROACTIVE / KAIROS |
| `/force-snip` | 强制片段化 | HISTORY_SNIP |
| `/subscribe-pr` | PR 订阅 | KAIROS_GITHUB_WEBHOOKS |
| `/remote-setup` | 远程设置 | CCR_REMOTE_SETUP |
| `/remote-control-server` | 远程控制服务器 | DAEMON + BRIDGE_MODE |

### 内部命令（仅 Anthropic 员工）

条件: `USER_TYPE === 'ant' && !IS_DEMO`

| 命令 | 功能 |
|------|------|
| `/bughunter` | Bug 检测 |
| `/good-claude` | 模型评估 |
| `/commit` | Git 提交（隐藏版） |
| `/commit-push-pr` | 一键提交推送 PR |
| `/ctx-viz` | 上下文可视化 |
| `/break-cache` | 缓存破坏 |
| `/mock-limits` | 模拟限制 |
| `/reset-limits` | 重置限制 |
| `/ant-trace` | Anthropic 追踪 |
| `/perf-issue` | 性能诊断 |
| `/debug-tool-call` | 工具调用调试 |
| `/agents-platform` | Agents 平台 |
| `/autofix-pr` | 自动修复 PR |
| `/backfill-sessions` | 回填会话 |
| `/share` | 会话分享 |
| `/summary` | 会话摘要 |

---

## 四、环境变量速查

### 模型控制

```bash
ANTHROPIC_MODEL=claude-opus-4-6          # 主模型
ANTHROPIC_SMALL_FAST_MODEL=claude-haiku-4-5  # 辅助模型
CLAUDE_CODE_SUBAGENT_MODEL=sonnet        # 子代理模型
CLAUDE_CODE_DISABLE_FAST_MODE=1          # 禁用快速模式
CLAUDE_CODE_DISABLE_ADVISOR_TOOL=1       # 禁用 Advisor
```

### 上下文控制

```bash
CLAUDE_CODE_MAX_OUTPUT_TOKENS=16000      # 输出 token 上限
CLAUDE_CODE_MAX_CONTEXT_TOKENS=150000    # 有效上下文
CLAUDE_CODE_DISABLE_1M_CONTEXT=1         # 禁用 1M 上下文
CLAUDE_CODE_AUTO_COMPACT_WINDOW=180000   # 自动压缩窗口
CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=80       # 按百分比触发压缩
```

### 行为控制

```bash
CLAUDE_CODE_SIMPLE=1                     # 极简 system prompt
CLAUDE_CODE_DISABLE_CLAUDE_MDS=1         # 禁用 CLAUDE.md
CLAUDE_CODE_COORDINATOR_MODE=1           # 强制协调器模式
CLAUDE_CODE_REMOTE=1                     # 远程会话标识
DISABLE_INTERLEAVED_THINKING=1           # 禁用交织思考
```

### API 控制

```bash
ANTHROPIC_BETAS="custom-beta-1,beta-2"  # 自定义 beta headers
CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS=1 # 禁用实验 beta
USE_CONNECTOR_TEXT_SUMMARIZATION=1       # 连接器文本摘要
USE_API_CONTEXT_MANAGEMENT=1             # API 上下文管理
```

---

## 五、108 个缺失模块

以下模块存在于 Anthropic 内部 monorepo，但在 npm 发布版中被 dead-code-eliminated:

**守护进程**: daemon 系统、后台任务调度器
**技能发现**: skill search、skill generator
**协调器**: 多 worker 编排器
**语音**: 完整语音管线（输入/输出/转录）
**浏览器**: WebBrowserTool、REPL Tool
**监控**: MonitorTool、性能追踪
**远程**: Bridge 完整服务端、CCR 镜像
**协作**: 频道系统、团队记忆同步
**AI 伙伴**: Buddy 精灵完整系统

完整列表见 README 的 Missing Modules 章节。
