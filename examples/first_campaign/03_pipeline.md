# 03 Pipeline (STRATGEN_V1)

## Pipeline Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                      STRATGEN_V1 Pipeline                       │
├─────────────────────────────────────────────────────────────────┤
│  Stage 1    Stage 2    Stage 3    Stage 4    Stage 5    Stage 6 │
│ ┌───────┐  ┌───────┐  ┌───────┐  ┌───────┐  ┌───────┐  ┌──────┐│
│ │Validate│→│ Fetch │→│ Build │→│ Multi │→│Guard- │→│ Emit ││
│ │ Input │  │ Data  │  │Feature│  │ Agent │  │ rails │  │Output││
│ └───────┘  └───────┘  └───────┘  └───────┘  └───────┘  └──────┘│
│                                                          ↓      │
│                                              ┌──────────────────┐│
│                                              │  Stage 7         ││
│                                              │  Memory Update   ││
│                                              └──────────────────┘│
└─────────────────────────────────────────────────────────────────┘
```

## Stages Detail

### Stage 1: Validate Input

- 檢查 ticker 格式
- 驗證 risk_profile 有效性
- 確認 period 範圍

### Stage 2: Fetch Data (MCP/DB)

- 呼叫價量 API
- 查詢財報資料庫
- 取得籌碼資料
- 拉取估值指標

### Stage 3: Build Features

- 計算技術指標（MA/RSI/MACD）
- 整理財務比率
- 彙整籌碼變化

### Stage 4: Multi-Agent Reasoning (LLM + SLM)

- SLM：快速分類、schema 檢查
- LLM：長文推理、策略敘述

### Stage 5: Guardrails (Schema + Data Grounding)

- 檢查輸出符合 schema
- 驗證數據有來源（no hallucination）
- 安全檢查（參照 `docs/hacker.md`）

### Stage 6: Emit Output (Text + JSON)

- 生成人類可讀報告
- 輸出結構化 JSON

### Stage 7: Memory Update

- 記錄 mistakes（如有）
- 更新 patterns（可重用規則）
- 寫入 sessions（本次摘要）

## Data Flow

```
Input → [Validated Input]
      → [Raw Data: price, financials, chip, valuation]
      → [Features: indicators, ratios, flows]
      → [Strategies: value, momentum, balanced]
      → [Checked Output: grounded, schema-valid]
      → [Final: text + json]
      → [Memory: mistakes/patterns/sessions]
```
