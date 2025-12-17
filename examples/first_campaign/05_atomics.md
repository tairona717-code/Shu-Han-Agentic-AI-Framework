# 05 Atomics (Task Units)

## 什麼是 Atomic

Atomic 是 Pipeline 中**最小的可執行單元**，具備：

- **單一職責**：只做一件事
- **可獨立測試**：有明確的輸入/輸出
- **可重組**：不同 Pipeline 可複用

## Atomic List

| ID | 名稱 | 負責 Agent | 輸入 | 輸出 |
|----|------|-----------|------|------|
| A01 | validate_ticker | Orchestrator | ticker string | validated ticker |
| A02 | fetch_price_series | Data Agent | ticker, period | price array |
| A03 | fetch_valuation_metrics | Data Agent | ticker | PE, PB, etc. |
| A04 | fetch_chip_summary | Data Agent | ticker, window | chip flows |
| A05 | fetch_fundamentals | Data Agent | ticker, years | financial statements |
| A06 | build_features | Analysis Agent | raw data | feature vectors |
| A07 | generate_value_strategy | Strategy Agent | features | value strategy |
| A08 | generate_momentum_strategy | Strategy Agent | features | momentum strategy |
| A09 | generate_balanced_strategy | Strategy Agent | features | balanced strategy |
| A10 | guardrail_grounding | Guardrail Agent | strategies | grounded strategies |
| A11 | emit_text | Report Agent | strategies | text report |
| A12 | emit_json | Report Agent | strategies | JSON output |
| A13 | write_memory_updates | Memory Agent | session data | memory entries |

## Atomic Execution Flow

```
A01 ──┬── A02 ──┐
      ├── A03 ──┼── A06 ──┬── A07 ──┐
      ├── A04 ──┤         ├── A08 ──┼── A10 ──┬── A11 ──┐
      └── A05 ──┘         └── A09 ──┘         └── A12 ──┼── A13
                                                        └───────
```

## Atomic 規格範例：A07 generate_value_strategy

```yaml
id: A07
name: generate_value_strategy
agent: Strategy Agent
description: 根據估值與財務特徵生成價值投資策略

input:
  - features.valuation (PE, PB, dividend_yield)
  - features.financials (ROE, debt_ratio, FCF)
  - constraints (no_leverage, risk_profile)

output:
  strategy:
    name: "Value"
    entry: "條件描述"
    exit: "條件描述"
    stop_loss: "X%"
    take_profit: "Y%"
    logic: "策略邏輯說明"
    data_used: ["PE", "PB", "ROE", ...]

error_handling:
  - missing_data: 標註「資料不足」，降級輸出
  - invalid_features: 拒絕執行，回傳錯誤碼

test_cases:
  - input: {...}
    expected_output: {...}
```

## 對應蜀漢治理

- 每個 Atomic 可視為一道**軍令的執行單元**
- 執行前須檢查權限（參照 `ALLSPARK` 憲章）
- 執行後須記錄（參照 `data_tracks` 協定）
