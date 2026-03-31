# Claude Code 深度揭秘

**首个基于源码分析的 Claude Code 架构拆解与实用指南**

[English](README.md) | 中文版

---

> 2026 年 3 月 31 日，Claude Code 的 TypeScript 源码（~51.2 万行）因 npm 包中未清理的 `.map` 文件而泄露。本项目从源码中提取**可操作的洞察** — 不是搬运代码，而是架构图解、隐藏功能、省钱技巧和最佳实践。

## 内容导航

### 架构图解（Mermaid 可视化）

| 文档 | 说明 |
|------|------|
| [工具系统](architecture/tool-system.md) | 40+ 工具、权限分级、调度流程 |
| [请求生命周期](architecture/query-lifecycle.md) | 用户输入 → API 调用 → 工具循环 → 响应 |
| [权限模型](architecture/permission-model.md) | 4 种模式、Bash 命令分类、自动模式 |
| [多代理协调](architecture/multi-agent.md) | 子代理、协调器、KAIROS 自主模式 |

### 实用指南

| 文档 | 说明 |
|------|------|
| [System Prompt 完整解析](practical/system-prompts.md) | 完整还原 system prompt，含 5 层优先级 |
| [隐藏配置完全手册](practical/hidden-configs.md) | 87 个 feature flags、50+ tengu_* 配置、60+ 命令 |
| [省钱指南](practical/cost-optimization.md) | 10 个基于源码证据的省钱技巧 |
| [CLAUDE.md 最佳实践](practical/claude-md-guide.md) | 怎么写有效的 CLAUDE.md（以及什么不该写） |

### 深度分析（中英双语）

| 主题 | 中文 | English |
|------|------|---------|
| 遥测与隐私 | [分析](analysis/zh/telemetry.md) | [Analysis](analysis/en/telemetry.md) |
| 隐藏功能与代号 | [分析](analysis/zh/hidden-features.md) | [Analysis](analysis/en/hidden-features.md) |
| 卧底模式 | [分析](analysis/zh/undercover-mode.md) | [Analysis](analysis/en/undercover-mode.md) |
| 远程控制与紧急开关 | [分析](analysis/zh/remote-control.md) | [Analysis](analysis/en/remote-control.md) |
| 未来路线图 | [分析](analysis/zh/roadmap.md) | [Analysis](analysis/en/roadmap.md) |

### 横向对比（即将推出）

| 文档 | 说明 |
|------|------|
| [vs Cursor](comparison/vs-cursor.md) | 架构级对比 |
| [vs Cline](comparison/vs-cline.md) | 开源替代方案分析 |
| [功能矩阵](comparison/feature-matrix.md) | 逐项能力对照表 |

---

## 一目了然

### System Prompt 优先级

```
Override > Coordinator > Agent > Custom > Default + Append
```

### 隐藏功能代号

| 代号 | 功能 |
|------|------|
| **KAIROS** | 自主助手平台 |
| **ULTRATHINK** | 深度扩展推理 |
| **BUDDY** | AI 伙伴精灵 |
| **CHICAGO_MCP** | 计算机控制 MCP |
| **TORCH** | 推理增强 |
| **ULTRAPLAN** | 超级规划器 |

### 省钱要点

| 事实 | 详情 |
|------|------|
| 输出预留 | 8K tokens（不是 32K）— 命中限制自动升到 64K |
| 快速模式加价 | 普通 Opus 4.6 的 **6 倍** |
| 缓存读折扣 | 输入价的 **1/10** |
| 自动压缩触发 | 约 167K tokens（上下文的 83%） |
| 子代理模型 | 默认继承父级（可用环境变量覆盖为 Haiku 省钱） |

### 模型定价（每百万 token）

| 模型 | 输入 | 输出 | 缓存读 |
|------|------|------|--------|
| Haiku 4.5 | $1 | $5 | $0.10 |
| Sonnet 4.6 | $3 | $15 | $0.30 |
| Opus 4.6 | $5 | $25 | $0.50 |
| Opus 4.6 快速 | $30 | $150 | $3.00 |

---

## 为什么做这个项目

现有的仓库要么:
- 搬运原始源码（法律灰色地带，无分析）
- 写 README 描述但无可验证的代码引用

本项目专注于**带源码引用的原创分析** — 每个结论都标注具体文件和行号。

---

## 免责声明

- 本项目**与 Anthropic 无关**
- 所有原始源代码为 Anthropic 的知识产权
- 分析仅用于**教育和安全研究目的**
- 本仓库**不重新分发**任何专有源代码

---

## 贡献

欢迎 PR:
- 架构图改进
- 新增实用指南
- 与其他 AI 编码工具的对比分析
- 翻译完善

## License

分析和文档: MIT
Claude Code 原始源码: Anthropic（保留所有权利）
