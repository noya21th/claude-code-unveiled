# Claude Code 省钱指南

> 基于源码中的成本追踪、上下文压缩和模型路由逻辑，提炼出可操作的省钱技巧。

---

## 模型定价速查

| 模型 | 输入/Mtok | 输出/Mtok | 缓存读/Mtok | 缓存写/Mtok |
|------|----------|----------|------------|------------|
| Haiku 4.5 | $1 | $5 | $0.10 | $1.25 |
| Sonnet 4.6 | $3 | $15 | $0.30 | $3.75 |
| Opus 4.6 | $5 | $25 | $0.50 | $6.25 |
| Opus 4.6 (快速) | $30 | $150 | $3.00 | $37.50 |

**源码位置**: `src/utils/modelCost.ts`

> 快速模式 (Fast Mode) 的 Opus 4.6 价格是普通模式的 **6 倍**。

---

## 技巧 1: 理解槽位预留机制（自动生效）

Claude Code **不会**预留 32k 输出 token 槽位，而是只预留 **8k**:

```
p99 实际输出 = 4,911 tokens
默认预留 = 8,000 tokens (而非 32,000)
命中限制时 → 自动升级到 64,000 重试
```

**效果**: 每次请求节省 ~24k token 的预留空间，让更多输入内容进入上下文窗口。

**源码位置**: `src/utils/context.ts` 第 24-25 行

---

## 技巧 2: 控制 CLAUDE.md 长度

CLAUDE.md 的内容在**每次请求**时都会作为 system prompt 的一部分发送。

```
每次请求成本增量 = CLAUDE.md 字数 × 模型输入价格
```

**建议**:
- 保持 CLAUDE.md 在 500 字以内
- 不重复 system prompt 已有的规则
- 不写"你是一个 AI 助手"这类废话
- 把参考文档放在项目文件里，需要时让 Claude 自己读

---

## 技巧 3: 善用 /compact 命令

自动压缩在上下文使用到 **167k tokens** 时触发:

```
有效窗口 = 200k - 20k(输出保留) = 180k
自动压缩阈值 = 180k - 13k(缓冲) = 167k
```

**主动触发** `/compact` 的好处:
- 手动压缩只预留 3k 缓冲（vs 自动的 13k）
- 可以附带自定义摘要指令，保留你关心的上下文
- 在长对话中定期压缩，避免重复发送旧内容

**源码位置**: `src/services/compact/autoCompact.ts` 第 62-91 行

---

## 技巧 4: 不要频繁切换模型

源码中有 **18 个维度** 的缓存破坏检测:

```typescript
// 会导致缓存失效的操作:
- 切换模型 (model != previous)
- 切换快速模式
- 修改 Beta headers
- 修改 Effort 值
- 修改系统提示
- 修改工具 schema
```

**每次缓存失效 = 重新计费全部输入 token**

**建议**: 一个会话内尽量用同一个模型。需要切换时，开新会话。

**源码位置**: `src/services/api/promptCacheBreakDetection.ts` 第 28-99 行

---

## 技巧 5: 快速模式 (Fast Mode) 按需使用

```
/fast 开启 → Opus 4.6 [1M context]
价格: 输入 $30/Mtok, 输出 $150/Mtok

/fast 关闭 → Opus 4.6 (普通)
价格: 输入 $5/Mtok, 输出 $25/Mtok
```

**6 倍价差！** 只在需要快速响应的短任务中使用，长对话/大重构关掉它。

**源码位置**: `src/utils/fastMode.ts`

---

## 技巧 6: API Key 用户默认省钱

模型选择逻辑:

| 认证方式 | 默认模型 | 原因 |
|---------|---------|------|
| OAuth (claude.ai) | Opus 4.6 | 订阅制，不按 token 计费 |
| API Key | **Sonnet** | 成本与质量均衡 |
| Bedrock/Vertex | 供应商特定 | 跟随云厂商定价 |

如果你用 API Key 且任务不复杂，**不需要手动切到 Opus**，默认 Sonnet 已经是成本最优选择。

**源码位置**: `src/utils/model/model.ts` 第 178-207 行

---

## 技巧 7: 子代理模型继承

子代理默认**完全继承父线程模型**:

```typescript
// 子代理模型优先级:
1. CLAUDE_CODE_SUBAGENT_MODEL 环境变量
2. 工具指定的模型
3. 代理配置的模型
4. 默认: 'inherit' (继承父级)
```

**省钱操作**:
```bash
# 让子代理用更便宜的模型
export CLAUDE_CODE_SUBAGENT_MODEL=haiku
```

子代理通常执行搜索、读文件等简单任务，Haiku 4.5 完全够用，成本降低 **5 倍**。

**源码位置**: `src/utils/model/agent.ts` 第 37-95 行

---

## 技巧 8: 后台任务不重试（已内置）

源码已经做了优化 — 非关键后台任务遇到 429/529 错误时不重试:

```
不重试的任务: summary, title_suggestion, classifier
会重试的任务: repl_main_thread, agent:*
```

**效果**: 避免容量级联时的 3-10 倍成本放大。

**源码位置**: `src/services/api/withRetry.ts` 第 62-88 行

---

## 技巧 9: 压缩后文件恢复有预算

压缩后系统会自动恢复最近使用的文件，但有严格预算:

```
总预算: 50,000 tokens
最多恢复: 5 个文件
单文件上限: 5,000 tokens
技能预算: 25,000 tokens
```

**启示**: 如果你在处理大文件，压缩后可能丢失部分上下文。在压缩前完成对大文件的操作。

**源码位置**: `src/services/compact/compact.ts` 第 122-130 行

---

## 技巧 10: 用 /cost 命令实时监控

```
/cost
```

输出:
```
Total cost:           $X.XX
Usage by model:
  haiku:   XXX input, XXX output, XXX cache read ($X.XX)
  sonnet:  XXX input, XXX output, XXX cache read ($X.XX)
  opus:    XXX input, XXX output, XXX cache read ($X.XX)
```

**源码位置**: `src/cost-tracker.ts` 第 181-226 行

---

## 关键数字速查

| 参数 | 值 | 说明 |
|------|-----|------|
| 默认上下文窗口 | 200,000 tokens | 所有模型 |
| 压缩预留输出 | 20,000 tokens | 压缩操作保留 |
| 默认 max_output | 8,000 tokens | 自动优化后 |
| 升级 max_output | 64,000 tokens | 命中限制时 |
| 自动压缩缓冲 | 13,000 tokens | 提前触发 |
| 手动压缩缓冲 | 3,000 tokens | 更激进 |
| 会话记忆 min | 10,000 tokens | 压缩后保留 |
| 会话记忆 max | 40,000 tokens | 硬上限 |
| 缓存读折扣 | 输入价的 1/10 | 所有模型 |

---

## 成本计算公式

```
总成本 = (输入tokens / 1M × 输入价格)
       + (输出tokens / 1M × 输出价格)
       + (缓存读tokens / 1M × 缓存读价格)
       + (缓存写tokens / 1M × 缓存写价格)
       + (Web查询次数 × $0.01)
```

**源码位置**: `src/utils/modelCost.ts` 第 131-141 行
