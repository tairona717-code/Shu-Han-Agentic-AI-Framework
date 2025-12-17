# 04 Agents

## Agent Roles (蜀漢官職對應)

| Agent | 蜀漢角色 | 職責 |
|-------|---------|------|
| Orchestrator/Router | 丞相（諸葛亮） | 意圖理解、Pipeline 分派 |
| Data Agent | 尚書令（資料官） | 取價量/財報/籌碼 |
| Analysis Agent | 太常卿（分析官） | 指標/特徵計算 |
| Strategy Agent | 將軍（策略官） | 三策略生成 |
| Guardrail Agent | 御史大夫（監察官） | Grounding + Schema 檢查 |
| Report Agent | 太史令（報告官） | 人類可讀 + JSON 輸出 |

## Agent Communication

```
                    ┌─────────────────┐
                    │   Orchestrator  │
                    │    (諸葛亮)      │
                    └────────┬────────┘
                             │ dispatch
         ┌───────────────────┼───────────────────┐
         ▼                   ▼                   ▼
   ┌───────────┐      ┌───────────┐      ┌───────────┐
   │Data Agent │ ───▶ │ Analysis  │ ───▶ │ Strategy  │
   │ (尚書令)   │      │  Agent    │      │  Agent    │
   └───────────┘      └───────────┘      └───────────┘
                                               │
                                               ▼
                                        ┌───────────┐
                                        │ Guardrail │
                                        │  Agent    │
                                        └─────┬─────┘
                                              │
                                              ▼
                                        ┌───────────┐
                                        │  Report   │
                                        │  Agent    │
                                        └───────────┘
```

## LLM + SLM Split

### SLM (Small Language Model) 負責

| 任務 | 說明 |
|------|------|
| Fast Classification | 意圖分類、實體識別 |
| Schema Checks | 輸出格式驗證 |
| Simple Computations | 指標計算、數值轉換 |
| Caching Decisions | 快取命中判斷 |

### LLM (Large Language Model) 負責

| 任務 | 說明 |
|------|------|
| Long-form Reasoning | 策略邏輯推導 |
| Strategy Narrative | 策略敘述生成 |
| Edge-case Explanation | 異常情況說明 |
| Risk Assessment | 風險評估描述 |

## Agent Protocol

所有 Agent 間通訊遵循 `agents/data_tracks_ShuHan_v1.md`：

```yaml
TRACK_ID: STRATGEN-20250101-001-A04
FROM: Data Agent (尚書令)
TO: Analysis Agent (太常卿)
PRIORITY: 正常 (NORMAL)
PAYLOAD:
  price_series: [...]
  financials: {...}
  chip_data: {...}
STATUS: 完成 (SUCCESS)
```
