# 01 Input (匿名化)

## User Intent

- **目標**：輸入股票代號，產出三策略（價值 / 動能 / 平衡）
- **風險偏好**：保守 / 平衡 / 進取（三選一）
- **期間**：近 1 年 / 3 年 / 5 年（可選）

## Constraints

| 限制項 | 說明 |
|--------|------|
| 不可虛構數據 | 所有數字必須來自真實資料源 |
| 必須可回測 | entry/exit/SL/TP 全量化 |
| 需列出 data_used | 來源與欄位明確標示 |

## Input Schema

```json
{
  "ticker": "XXXX",
  "risk_profile": "balanced",
  "period": "1y",
  "constraints": {
    "no_leverage": true,
    "no_hallucination": true
  }
}
```

## 對應蜀漢治理

- **軍令來源**：`orders/ORD-YYYYMMDD-XX.md`
- **PRD 依據**：`specs/business/PRD-strategy-generation.md`
