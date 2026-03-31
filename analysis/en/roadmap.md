# Future Roadmap

> Inferring Claude Code's development direction from 87 feature flags and missing modules. All assessments are based on verifiable metrics such as code volume and cross-file reference counts.

---

## Feature Maturity Assessment Method

This document uses the following verifiable metrics to assess feature maturity, rather than subjective percentages:

| Metric | Description |
|--------|-------------|
| Dedicated file count | Number of source files in the feature's directory |
| Lines of code | Code volume of dedicated modules |
| Cross-file reference count | Number of other files that reference the feature |
| Infrastructure evidence | Whether cross-platform binaries, config schemas, GrowthBook gates, etc. exist |

---

## Tier 1: Deep Integration (100+ file references)

### KAIROS — Autonomous Assistant Platform

**This is Claude Code's largest unreleased feature.**

| Metric | Data |
|--------|------|
| Cross-file references | **210 files** (highest in the entire codebase) |
| Dedicated modules | `src/assistant/`, `src/tools/BriefTool/` |
| Lines of code | 597 dedicated lines + extensively distributed across other modules |
| GrowthBook gates | `tengu_kairos`, `tengu_kairos_cron_config`, `tengu_kairos_brief` |
| Subsystems | Assistant Mode, Brief Tool, Channels, Cron Tasks, GitHub Webhooks, Push Notifications, Dream |

**Rationale**: 210 file references means KAIROS is deeply embedded in the core architecture — not an isolated experiment.

### Voice Mode — Voice Interaction

| Metric | Data |
|--------|------|
| Cross-file references | **38 files** |
| Dedicated modules | `src/voice/` |
| Cross-platform binaries | `vendor/audio-capture/` contains .node files for 6 platforms |
| Components | voiceStreamSTT, useVoiceIntegration |

**Rationale**: Cross-platform native binaries are already compiled (arm64/x64 × darwin/linux/win32), indicating it has passed the engineering validation phase.

---

## Tier 2: Active Development (15-50 file references)

### Coordinator Mode — Multi-Agent Coordination

| Metric | Data |
|--------|------|
| Cross-file references | **45 files** |
| Dedicated modules | `src/coordinator/` (1 file, 369 lines) |
| Environment variable | `CLAUDE_CODE_COORDINATOR_MODE` |
| System Prompt | Dedicated prompt (priority 1, higher than default) |

**Rationale**: 45 references + a dedicated system prompt indicate the framework is in place, but the small dedicated code volume suggests core logic may still reside in the internal monorepo.

### UltraPlan + UltraThink + Torch

- **UltraPlan**: Extended plan generation with selection dialogs
- **UltraThink**: Deep reasoning mode
- **Torch**: Reasoning enhancement

All three have independent command registrations in `src/commands.ts` and may merge into a unified "deep thinking" mode in the future.

---

## Tier 3: Early Experiments (< 15 file references, small dedicated code volume)

### Buddy — AI Companion Sprite

| Metric | Data |
|--------|------|
| Cross-file references | **14 files** |
| Dedicated modules | `src/buddy/` (6 files, 1,298 lines) |
| Core components | CompanionSprite (514 lines of animation), Companion (133 lines of logic), useBuddyNotification |

**Rationale**: The dedicated code volume is substantial (1,298 lines), but the low cross-file reference count indicates it has not yet been deeply integrated into core workflows.

### Other Experimental Features

| Feature | Dedicated Code | Description |
|---------|---------------|-------------|
| Agent Triggers | Distributed across tasks/ | Scheduled agent tasks (cron scheduling) |
| Memory Extract | Feature flag gated | Automatically extract memories from conversations |
| Agent Memory Snapshot | Feature flag gated | Agent-level memory snapshots |
| Templates | Feature flag gated | Task templates and categorization system |
| Web Browser Tool | 0 dedicated files | MCP tool format, likely entirely in the internal monorepo |

---

## Tier 4: 108 Missing Modules

The npm published version has approximately 108 modules eliminated by dead-code-elimination, existing only in Anthropic's internal monorepo:

| Category | Includes |
|----------|----------|
| **Daemon** | Daemon system, background task scheduling |
| **Skill Discovery** | Skill search, skill generator |
| **Browser** | WebBrowserTool, REPL Tool |
| **Monitoring** | MonitorTool, performance tracing |
| **Remote** | Complete Bridge server-side, CCR images |
| **Collaboration** | Channel system, team memory sync |
| **AI Companion** | Complete Buddy sprite system |

---

## Development Direction Predictions

### Short-term

1. **Voice Mode public release** — Cross-platform binaries are ready, 38 file references
2. **UltraThink/UltraPlan** — Deep reasoning mode
3. **Web Browser Tool** — MCP integration

### Mid-term

4. **KAIROS gradual rollout** — 210 file references indicate deep integration
5. **Agent Triggers** — CI/CD automation
6. **Coordinator Mode** — Multi-agent collaboration

### Long-term

7. **AI Development Team** — Coordinator + KAIROS + Memory
8. **Autonomous Development Loop** — Agent autonomously discovers issues → fixes → verifies
9. **Team Collaboration** — Multi-person + multi-agent hybrid workflows

---

## Competitive Landscape Impact

| Feature | Claude Code | Cursor | Cline |
|---------|------------|--------|-------|
| Autonomous Agent (KAIROS) | 210 file references | None | None |
| Voice Interaction | Cross-platform binaries ready | None | None |
| Multi-agent Coordination | 45 file references | None | None |
| AI Companion | 1,298 lines of dedicated code | None | None |
| Deep Reasoning | Independent command registrations | Partial | None |

**Conclusion**: Claude Code is evolving from an "AI coding assistant" to an "AI development platform," with a feature roadmap that far exceeds current competitors at the code level.

> **Note**: The timelines above are predictions based on code evidence, not an official Anthropic roadmap. Actual release cadence depends on internal priorities and quality standards.
