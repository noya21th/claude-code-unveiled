# Feature Matrix: Claude Code vs Cursor vs Cline

> Architecture-level comparison based on public documentation and source code analysis.

---

## Deployment & Interface

| Aspect | Claude Code | Cursor | Cline |
|--------|------------|--------|-------|
| **Form Factor** | Terminal CLI | Desktop app (VS Code fork) | VS Code extension |
| **Architecture** | Standalone agent harness | VS Code OSS fork + AI layer | Extension APIs + webview |
| **IDE Integration** | CLI + VS Code/JetBrains extensions | Built-in editor | VS Code only |
| **Open Source** | No (source leaked via sourcemap) | No | Yes (Apache 2.0) |

## AI Models

| Aspect | Claude Code | Cursor | Cline |
|--------|------------|--------|-------|
| **Supported Models** | Claude only (Opus/Sonnet/Haiku) | OpenAI, Claude, Gemini, xAI, proprietary | Any OpenAI-compatible API |
| **Proprietary Models** | No | Composer 2.0 (MoE, RL-trained) | No |
| **Max Context** | 1M tokens (Opus/Sonnet 4.6) | ~272K (RAG-augmented) | Depends on provider |

## Agentic Capabilities

| Aspect | Claude Code | Cursor | Cline |
|--------|------------|--------|-------|
| **Execution Model** | Agentic loop (act-verify-loop) | Parallel multi-agent (up to 8) | Human-in-the-loop approval |
| **Multi-Agent** | Subagents with fresh context | Up to 8 parallel via worktrees | Single agent only |
| **Autonomous Mode** | KAIROS (unreleased, 210 file refs) | Direct execution | YOLO mode (auto-approve all) |

## Permission & Safety

| Aspect | Claude Code | Cursor | Cline |
|--------|------------|--------|-------|
| **Permission Model** | 4 modes: Default/Auto/Plan/Bypass | Implicit trust | Per-action approval |
| **Tool Classification** | 4 levels (auto-allow to always-confirm) | N/A | Binary (approve/deny) |
| **Bash Safety** | Command parsing + risk classification | Direct execution | User approval per command |
| **Config Scope** | Org -> Project -> Personal hierarchy | Editor settings | Extension settings |

## Context Management

| Aspect | Claude Code | Cursor | Cline |
|--------|------------|--------|-------|
| **Context Loading** | Auto (CLAUDE.md + memory) | RAG via semantic indexing | Explicit @-commands |
| **Persistent Memory** | Yes (MEMORY.md, cross-session) | Session-based only | Not persistent |
| **Token Management** | Auto-compaction at ~167K | 272K with RAG retrieval | Per-provider limits |
| **Codebase Search** | ripgrep + glob (on-demand) | AST chunking + embeddings | VS Code search APIs |

## Cost

| Aspect | Claude Code | Cursor | Cline |
|--------|------------|--------|-------|
| **Pricing** | Per-token API / subscription | $20-40/month subscription | Free (bring your own keys) |
| **Cost Visibility** | `/cost` per-model breakdown | Credit pool dashboard | Pass-through to provider |
| **Free Tier** | No | Limited credits | Yes (with own API keys) |

## Extensibility

| Aspect | Claude Code | Cursor | Cline |
|--------|------------|--------|-------|
| **MCP Support** | Yes (deferred tool loading) | Yes (1800+ servers) | Yes (auto-install) |
| **Custom Tools** | Skills + MCP + Hooks | MCP servers | MCP servers |
| **Automation** | Hooks (pre/post tool events) | Limited | Limited |

## Unreleased / Upcoming

| Feature | Claude Code | Cursor | Cline |
|---------|------------|--------|-------|
| Voice Input | Binaries ready (6 platforms) | No | No |
| Autonomous Agent | KAIROS (210 file references) | No | No |
| Multi-Agent Coordination | Coordinator (45 file references) | 8 parallel agents (shipped) | No |
| AI Companion | Buddy (1,298 lines) | No | No |
| Deep Reasoning | UltraThink/UltraPlan/Torch | Partial | No |
| Web Browser | Feature-gated | No | Computer Use capability |

---

## Key Takeaways

**Choose Claude Code if**: CLI workflows, long-context reasoning (1M tokens), persistent memory, multi-agent orchestration.

**Choose Cursor if**: Polished GUI, parallel agents today, multi-model support, subscription pricing.

**Choose Cline if**: Open-source transparency, model flexibility, human-in-the-loop safety, zero software cost.

> **Disclaimer**: Based on publicly available documentation and source analysis as of March 2026.
