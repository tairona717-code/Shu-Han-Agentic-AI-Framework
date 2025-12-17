# 07 Tools & DB Calls (去敏)

## Tools Called

### Data Fetching Tools

| Tool | Signature | Purpose |
|------|-----------|---------|
| `get_price_series` | `(ticker, start, end)` | 取得歷史價量 |
| `get_financials` | `(ticker, year_range)` | 取得財務報表 |
| `get_chip_flows` | `(ticker, window)` | 取得籌碼變化 |
| `get_valuation` | `(ticker)` | 取得估值指標 |

### Example Tool Calls (去敏)

```json
{
  "tool": "get_price_series",
  "params": {
    "ticker": "XXXX",
    "start": "2024-01-01",
    "end": "2025-01-01"
  },
  "response_time_ms": 120,
  "status": "success",
  "data_points": 252
}
```

```json
{
  "tool": "get_financials",
  "params": {
    "ticker": "XXXX",
    "year_range": [2022, 2023, 2024]
  },
  "response_time_ms": 85,
  "status": "success",
  "tables": ["income", "balance", "cashflow"]
}
```

## DB Function Calls

### Query Functions

| Function | Purpose | 敏感度 |
|----------|---------|--------|
| `query_price_history(...)` | 查詢價格歷史 | 低 |
| `query_financial_ratios(...)` | 查詢財務比率 | 低 |
| `query_chip_data(...)` | 查詢籌碼資料 | 中 |

### Write Functions

| Function | Purpose | 敏感度 |
|----------|---------|--------|
| `upsert_memory_event(...)` | 寫入記憶事件 | 低 |
| `write_report_artifact(...)` | 儲存報告產出 | 低 |
| `log_pipeline_trace(...)` | 記錄執行軌跡 | 低 |

### Example DB Calls (去敏)

```sql
-- Query (去敏版)
SELECT date, open, high, low, close, volume
FROM price_history
WHERE ticker = 'XXXX'
  AND date BETWEEN '2024-01-01' AND '2025-01-01'
ORDER BY date;

-- 結果：252 rows, 15ms
```

```sql
-- Write (去敏版)
INSERT INTO memory_events (
  event_type, session_id, content, created_at
) VALUES (
  'pattern', 'SESSION-XXXX', '...', NOW()
);
```

## Security Notes

### 去敏規則

| 移除項目 | 原因 |
|----------|------|
| API Keys | 敏感憑證 |
| User Identifiers | 隱私保護 |
| Internal IPs | 安全考量 |
| Actual Ticker (視情況) | 商業機密 |

### 保留項目

| 保留項目 | 原因 |
|----------|------|
| Function Signatures | 展示能力 |
| Timing/Ordering | 效能分析 |
| Data Structure | 架構說明 |
| Error Codes | 除錯參考 |

## 對應蜀漢治理

- 所有 Tool 呼叫須符合 `docs/hacker.md` 安全規範
- DB 操作須有 audit log
- 敏感操作須經 Guardrail Agent 審核
