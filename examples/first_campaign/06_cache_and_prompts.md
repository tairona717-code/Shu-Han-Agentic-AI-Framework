# 06 Bullet Prompt & Input Cache

## Bullet System Prompt Policy

### 固定部分（不隨請求變動）

```markdown
## 角色邊界
- 你是策略分析 Agent
- 你只能分析資料，不能執行交易
- 你不能虛構任何數字

## 禁止事項（參照 ALLSPARK）
- 禁止存取未授權資料源
- 禁止輸出未經 grounding 的數字
- 禁止建議使用槓桿（除非明確授權）

## 輸出 Schema
{
  "strategy_name": string,
  "entry": string,
  "exit": string,
  "stop_loss": string,
  "take_profit": string,
  "logic": string,
  "data_used": string[]
}

## 資料引用規則
- 所有數字必須標註來源
- 格式：「數值 (來源: 欄位名)」
```

### 可變部分（隨請求變動）

```markdown
## 本次任務
- Ticker: {{ticker}}
- Period: {{period}}
- Risk Profile: {{risk_profile}}

## Context
{{fetched_data_summary}}

## Constraints
{{user_constraints}}
```

## Input Cache

### Cache Key 計算

```python
cache_key = hash(
    ticker + 
    period + 
    feature_set_version + 
    prompt_version
)
```

### Cache Policy

| 項目 | 政策 |
|------|------|
| TTL | 24 hours（價量資料）/ 7 days（財報） |
| Invalidation | 新資料進入時自動失效 |
| Hit Rate Target | > 70% |

### What is Cached

| 層級 | 快取內容 | TTL |
|------|---------|-----|
| L1 | Normalized Input | 1 hour |
| L2 | Fetched Datasets Fingerprints | 24 hours |
| L3 | Computed Feature Summary | 24 hours |
| L4 | Final Output (if deterministic) | 1 hour |

### Cache Flow

```
Request
    │
    ├── Compute cache_key
    │
    ├── L1 Hit? ─── Yes ──▶ Return cached normalized input
    │     │
    │     No
    │     │
    ├── L2 Hit? ─── Yes ──▶ Skip data fetch, use fingerprint
    │     │
    │     No
    │     │
    ├── L3 Hit? ─── Yes ──▶ Skip feature build
    │     │
    │     No
    │     │
    └── Full Pipeline Execution
              │
              ▼
         Update Cache (L1-L4)
```

## 對應蜀漢治理

- Prompt 政策須經**丞相府審核**
- Cache 失效策略記錄於 `memory/patterns/`
- 異常 Cache 行為記錄於 `memory/mistakes/`
