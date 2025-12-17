# 08 Output

## Text Output (Excerpt)

```markdown
# 策略分析報告

**標的**：XXXX  
**分析日期**：2025-01-XX  
**風險偏好**：平衡型  
**分析期間**：近一年

---

## 價值策略 (Value Strategy)

**進場條件**：
- PE < 15 且 PB < 1.5 (來源: valuation_metrics)
- ROE > 12% (來源: financials.roe)
- 自由現金流為正 (來源: financials.fcf)

**出場條件**：
- PE > 20 或獲利達 20%

**停損**：-10%  
**停利**：+25%

**邏輯**：
本策略基於估值面與基本面篩選，尋找被低估的優質標的...

---

## 動能策略 (Momentum Strategy)

**進場條件**：
- 股價站上 MA60 (來源: price_series)
- RSI(14) > 50 且 < 70 (來源: computed_indicators)
- 成交量放大 > 20日均量 (來源: volume_series)

**出場條件**：
- 股價跌破 MA20

**停損**：-8%  
**停利**：+20%

**邏輯**：
本策略追蹤中期趨勢，在動能確認後進場...

---

## 平衡策略 (Balanced Strategy)

結合價值與動能條件，詳見 JSON 輸出...

---

## 風險提示

- 本分析僅供參考，不構成投資建議
- 過去績效不代表未來表現
- 資料來源：[已去敏]

## 使用資料

- price_series (2024-01-01 ~ 2025-01-01)
- financials (2022-2024)
- valuation_metrics (as of 2025-01-XX)
- chip_flows (近 60 日)
```

## JSON Output

```json
{
  "ticker": "XXXX",
  "as_of": "2025-01-XX",
  "risk_profile": "balanced",
  "period": "1y",
  "strategies": [
    {
      "strategy_name": "Value",
      "entry": "PE < 15 AND PB < 1.5 AND ROE > 12% AND FCF > 0",
      "exit": "PE > 20 OR profit >= 20%",
      "stop_loss": "-10%",
      "take_profit": "+25%",
      "logic": "估值面與基本面篩選，尋找被低估的優質標的",
      "risk": "價值陷阱風險",
      "data_used": [
        "valuation_metrics.PE",
        "valuation_metrics.PB",
        "financials.ROE",
        "financials.FCF"
      ]
    },
    {
      "strategy_name": "Momentum",
      "entry": "price > MA60 AND RSI(14) IN (50, 70) AND volume > MA20_volume",
      "exit": "price < MA20",
      "stop_loss": "-8%",
      "take_profit": "+20%",
      "logic": "追蹤中期趨勢，在動能確認後進場",
      "risk": "假突破風險",
      "data_used": [
        "price_series",
        "computed_indicators.RSI",
        "computed_indicators.MA60",
        "volume_series"
      ]
    },
    {
      "strategy_name": "Balanced",
      "entry": "(Value.entry OR Momentum.entry) AND chip_flow > 0",
      "exit": "Value.exit OR Momentum.exit",
      "stop_loss": "-9%",
      "take_profit": "+22%",
      "logic": "結合價值與動能，加入籌碼確認",
      "risk": "條件過多可能錯過機會",
      "data_used": [
        "valuation_metrics.*",
        "price_series",
        "chip_flows"
      ]
    }
  ],
  "metadata": {
    "pipeline": "STRATGEN_V1",
    "trace_id": "TRACE-20250101-XXXX",
    "execution_time_ms": 2500,
    "cache_hit": false,
    "guardrails_passed": true
  }
}
```

## Output Validation

| 檢查項 | 狀態 |
|--------|------|
| Schema Valid | ✅ |
| All Numbers Grounded | ✅ |
| No Hallucination | ✅ |
| Data Sources Listed | ✅ |
| Risk Warnings Present | ✅ |
