# Claude Code Unveiled

**The first architecture deep-dive and practical guide for Claude Code, based on v2.1.88 source analysis.**

[中文版](README_CN.md) | English

---

> On March 31, 2026, Claude Code's TypeScript source (~512K lines) was exposed through an unstripped `.map` file in the npm package. This project analyzes the source to extract **actionable insights** — not just code, but architecture patterns, hidden features, cost optimization strategies, and best practices.

## What's Inside

### Architecture Diagrams (Mermaid)

| Document | Description |
|----------|-------------|
| [Tool System](architecture/tool-system.md) | 40+ tools, permission levels, dispatch flow |
| [Query Lifecycle](architecture/query-lifecycle.md) | User input → API → tool loop → response |
| [Permission Model](architecture/permission-model.md) | 4 modes, Bash command classification, auto-mode |
| [Multi-Agent](architecture/multi-agent.md) | Sub-agents, Coordinator, KAIROS autonomous mode |

### Practical Guides

| Document | Description |
|----------|-------------|
| [System Prompts](practical/system-prompts.md) | Complete system prompt reconstruction with 5-layer priority |
| [Hidden Configs](practical/hidden-configs.md) | 87 feature flags, 50+ tengu_* gates, 60+ slash commands |
| [Cost Optimization](practical/cost-optimization.md) | 10 money-saving tips backed by source code evidence |
| [CLAUDE.md Guide](practical/claude-md-guide.md) | How to write effective CLAUDE.md (and what NOT to write) |

### Deep Analysis (Bilingual)

| Document | 中文 | English |
|----------|------|---------|
| Telemetry & Privacy | [遥测与隐私](analysis/zh/telemetry.md) | [Telemetry](analysis/en/telemetry.md) |
| Hidden Features | [隐藏功能](analysis/zh/hidden-features.md) | [Hidden Features](analysis/en/hidden-features.md) |
| Undercover Mode | [卧底模式](analysis/zh/undercover-mode.md) | [Undercover Mode](analysis/en/undercover-mode.md) |
| Remote Control | [远程控制](analysis/zh/remote-control.md) | [Remote Control](analysis/en/remote-control.md) |
| Future Roadmap | [未来路线图](analysis/zh/roadmap.md) | [Roadmap](analysis/en/roadmap.md) |

### Comparison (Coming Soon)

| Document | Description |
|----------|-------------|
| [vs Cursor](comparison/vs-cursor.md) | Architecture-level comparison |
| [vs Cline](comparison/vs-cline.md) | Open-source alternative analysis |
| [Feature Matrix](comparison/feature-matrix.md) | Side-by-side capability table |

---

## Key Findings at a Glance

### System Prompt Priority

```
Override > Coordinator > Agent > Custom > Default + Append
```

### Hidden Feature Codenames

| Codename | What It Does |
|----------|-------------|
| **KAIROS** | Autonomous assistant platform |
| **ULTRATHINK** | Extended deep reasoning |
| **BUDDY** | AI companion sprite |
| **CHICAGO_MCP** | Computer-use MCP server |
| **TORCH** | Inference enhancement |

### Cost Facts

| Fact | Detail |
|------|--------|
| Output reservation | 8K tokens (not 32K) — auto-escalates to 64K |
| Fast mode markup | **6x** price vs normal Opus 4.6 |
| Cache read discount | **90%** off input price |
| Auto-compact trigger | At ~167K tokens (83% of context) |
| Sub-agent default | Inherits parent model (override with env var) |

### Model Pricing (per Mtok)

| Model | Input | Output | Cache Read |
|-------|-------|--------|------------|
| Haiku 4.5 | $1 | $5 | $0.10 |
| Sonnet 4.6 | $3 | $15 | $0.30 |
| Opus 4.6 | $5 | $25 | $0.50 |
| Opus 4.6 Fast | $30 | $150 | $3.00 |

---

## Why This Project Exists

Existing repos either:
- Mirror raw source code (legal grey area, no analysis)
- Write README descriptions without verifiable code references

This project focuses on **original analysis with source citations** — every claim links back to a specific file and line number.

---

## Disclaimer

- This project is **NOT affiliated with Anthropic**
- All original source code is Anthropic's intellectual property
- Analysis is for **educational and security research purposes only**
- No proprietary source code is redistributed in this repository

---

## Contributing

PRs welcome for:
- Architecture diagram improvements
- Additional practical guides
- Comparison analysis with other AI coding tools
- Translations

## License

Analysis and documentation: MIT
Original Claude Code source: Anthropic (all rights reserved)
