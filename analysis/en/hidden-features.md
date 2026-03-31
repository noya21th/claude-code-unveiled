# Hidden Features and Codename System

> 87 compile-time flags, 60+ slash commands, an animal codename system -- the iceberg beneath the surface of Claude Code.

---

## Codename System

Anthropic uses **animal names** as internal model codenames:

| Codename | Corresponding Model | Source |
|----------|-------------------|--------|
| **Capybara** | Sonnet series | `src/constants/` |
| **Fennec** | Opus series | `src/constants/` |
| **Numbat** | Unreleased model | `src/constants/` |

### Capybara v8 Known Issues

The source code documents technical issues with Capybara v8:
- Stop sequence misfires
- Tool result parsing failures
- Hallucination rate of 29-30%

---

## Feature Flag Name Obfuscation

Runtime configurations use the `tengu_` prefix + **random word pairs**, deliberately hiding their purpose:

```
tengu_frond_boric       → Analytics data stream killswitch
tengu_passport_quail    → Memory extraction gating
tengu_moth_copse        → Memory extraction enable
tengu_bramble_lintel    → Memory extraction frequency
tengu_cicada_nap_ms     → Background refresh throttling
tengu_slate_prism       → Connector text summarization
tengu_slate_thimble     → Non-interactive memory extraction
tengu_amber_json_tools  → JSON tool format
tengu_tool_pear         → Structured output
```

**Design Intent**: Even if configuration names leak, their functionality cannot be inferred.

---

## Core Hidden Features

### Tier 1: Near Release (Maturity 90%+)

#### KAIROS -- Autonomous Assistant Platform

```
- Assistant mode (/assistant)
- Brief tool (/brief) — message checkpoints
- Channel system — MCP channel integration
- Scheduled tasks — cron scheduling
- GitHub Webhooks — PR subscriptions
- Push notifications
```

**Control**: `feature('KAIROS')`, `tengu_kairos`

#### VOICE_MODE -- Voice Interaction

```
- Speech-to-text (STT)
- Voice recording and transcription
- Integrated React Hook
```

**Control**: `feature('VOICE_MODE')`

### Tier 2: In Development (Maturity 60-80%)

#### BUDDY -- AI Companion Sprite

```
- CompanionSprite animation system (45K bytes of full implementation)
- Floating bubble notifications
- Companion personality system
```

**Control**: `feature('BUDDY')`

#### ULTRAPLAN / ULTRATHINK

```
ULTRAPLAN — Super planner, extended plan generation
ULTRATHINK — Deep reasoning mode
TORCH — Reasoning enhancement
```

#### WEB_BROWSER_TOOL -- Web Browser

```
- MCP tool format
- Panel UI
- Embedded browsing
```

**Control**: `feature('WEB_BROWSER_TOOL')`

### Tier 3: Experimental (Maturity 30-50%)

| Feature | Description |
|---------|-------------|
| COORDINATOR_MODE | Multi-agent coordinator |
| VERIFICATION_AGENT | Verification agent |
| AGENT_TRIGGERS | Scheduled agent tasks |
| AGENT_MEMORY_SNAPSHOT | Agent memory snapshots |
| EXTRACT_MEMORIES | Automatic memory extraction |
| TEMPLATES | Task templates and categorization |
| FORK_SUBAGENT | Sub-agent forking |
| HISTORY_SNIP | History message snippeting |

### Tier 4: Infrastructure

| Feature | Description |
|---------|-------------|
| CHICAGO_MCP | Computer control MCP |
| MCP_SKILLS | MCP resources as skills |
| CONNECTOR_TEXT | Connector text (anti-distillation) |
| BRIDGE_MODE | Remote control bridging |
| DAEMON | Daemon process mode |

---

## Hidden Slash Commands

### Commands Requiring Feature Flags

| Command | Function | Activation Condition |
|---------|----------|---------------------|
| `/assistant` | Assistant mode | KAIROS |
| `/brief` | Brief messages | KAIROS_BRIEF |
| `/bridge` | Remote bridging | BRIDGE_MODE |
| `/voice` | Voice mode | VOICE_MODE |
| `/buddy` | AI sprite | BUDDY |
| `/ultraplan` | Super planning | ULTRAPLAN |
| `/torch` | Reasoning enhancement | TORCH |
| `/workflows` | Workflow scripts | WORKFLOW_SCRIPTS |
| `/fork` | Sub-agent forking | FORK_SUBAGENT |
| `/peers` | Peer messages | UDS_INBOX |
| `/proactive` | Proactive planning | PROACTIVE |
| `/force-snip` | Force snippeting | HISTORY_SNIP |
| `/subscribe-pr` | PR subscription | KAIROS_GITHUB_WEBHOOKS |

### Anthropic Internal-Only Commands

Condition: `USER_TYPE === 'ant' && !IS_DEMO`

| Command | Function |
|---------|----------|
| `/bughunter` | Automated code bug detection |
| `/good-claude` | Model evaluation tool |
| `/commit` | Hidden Git commit |
| `/commit-push-pr` | One-click commit -> push -> create PR |
| `/ctx-viz` | Context visualization |
| `/break-cache` | Force cache invalidation |
| `/mock-limits` | Simulate rate limits |
| `/reset-limits` | Reset rate limits |
| `/ant-trace` | Anthropic internal tracing |
| `/perf-issue` | Performance issue diagnostics |
| `/debug-tool-call` | Tool call debugging |
| `/agents-platform` | Agents platform management |
| `/autofix-pr` | Auto-fix PR |
| `/share` | Session sharing |
| `/summary` | Session summary |

---

## Internal vs. External Build Differences

| Dimension | Internal Build (ant) | External Build |
|-----------|---------------------|----------------|
| Feature flags | All available | Most eliminated by DCE |
| Slash commands | 60+ | ~45 |
| Undercover mode | Full | Code does not exist |
| Enhanced attribution | Contribution percentages, etc. | Standard attribution |
| Model fallback | Automatic downgrade | Standard error |
| Debug tools | `/ctx-viz`, `/debug-tool-call`, etc. | Not available |
| GrowthBook overrides | `CLAUDE_INTERNAL_FC_OVERRIDES` | Not available |

---

## Environment Variable Overrides

Internal users can override feature flags via environment variables:

```bash
# JSON format override (ant builds only)
CLAUDE_INTERNAL_FC_OVERRIDES='{"KAIROS":true,"VOICE_MODE":true}'
```

**Source**: `src/services/analytics/growthbook.ts`
