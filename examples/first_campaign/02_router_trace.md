# 02 Router Trace

## Routing Decision

| 欄位 | 值 |
|------|-----|
| intent_class | `strategy_generation` |
| pipeline | `STRATGEN_V1` |
| confidence | 0.92 |
| router_agent | 諸葛亮 (Orchestrator) |

## Why This Pipeline

選擇 `STRATGEN_V1` 的原因：

1. **需要資料聚合** — valuation + technical + chip + fundamentals
2. **輸出需雙格式** — text + json
3. **需 guardrails** — no hallucination / no leverage

## Decision Tree

```
User Intent
    │
    ├─ 是否需要策略生成？ → Yes
    │
    ├─ 資料需求？
    │   ├─ 價量資料 ✓
    │   ├─ 財報資料 ✓
    │   ├─ 籌碼資料 ✓
    │   └─ 估值指標 ✓
    │
    └─ Pipeline = STRATGEN_V1
```

## Fallback / Degrade Plan

| 情境 | 降級方案 |
|------|----------|
| 財報資料缺失 | 降級為「技術+籌碼」策略，明確標註限制 |
| 資料延遲 | 先回傳「暫定策略」+ 待補欄位清單 |
| API 超時 | 使用快取資料（標註 stale） |
| 全面失敗 | 回傳錯誤碼 + 建議人工介入 |

## Trace Log (去敏)

```
[2025-01-XX 10:00:00] Router received intent
[2025-01-XX 10:00:01] Intent classified: strategy_generation (0.92)
[2025-01-XX 10:00:01] Pipeline selected: STRATGEN_V1
[2025-01-XX 10:00:02] Dispatched to Execution Court
```
