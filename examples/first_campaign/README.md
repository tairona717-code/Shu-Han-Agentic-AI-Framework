# First Campaign — Evidence Chain (示範北伐)

這個資料夾展示「蜀漢 Agentic System」如何從需求走到可驗證輸出：

```
BRD/PRD → Router → Pipeline → Multi-Agent → Atomics → Cache/Tools → Output → Memory
```

## 目標

- 讓外部工程師 **3 分鐘看懂** 系統怎麼跑
- 不暴露商業機密（可用匿名/假資料/刪減片段）
- 提供可複製的證據鏈模板

## 目錄結構

| 檔案 | 說明 |
|------|------|
| [01_input.md](01_input.md) | 使用者需求（匿名化） |
| [02_router_trace.md](02_router_trace.md) | 路由決策（為何走這條 pipeline） |
| [03_pipeline.md](03_pipeline.md) | Pipeline stages 與資料流 |
| [04_agents.md](04_agents.md) | Multi-agent 協作（LLM + SLM） |
| [05_atomics.md](05_atomics.md) | Atomic 任務單元清單 |
| [06_cache_and_prompts.md](06_cache_and_prompts.md) | Bullet prompt / Input cache 命中與規則 |
| [07_tools_and_db_calls.md](07_tools_and_db_calls.md) | Function calls / DB calls（去敏） |
| [08_output.md](08_output.md) | 最終輸出（文字 + JSON） |
| [09_memory_updates.md](09_memory_updates.md) | mistakes/patterns/sessions 的落盤摘要 |
| [10_e2e_test.md](10_e2e_test.md) | E2E 測試（驗收腳本/條件） |

## 如何使用

1. **閱讀**：按順序讀完 01-10，理解完整資料流
2. **複製**：建立自己的 `examples/your_campaign/`，套用相同模板
3. **填寫**：用真實（去敏後）的資料填寫每個檔案
4. **驗證**：確保 E2E 測試能通過

## 對應蜀漢治理文件

- 軍令格式：`agents/AGENTS_v5.1.md` §4 虎符制度
- 通訊協定：`agents/data_tracks_ShuHan_v1.md`
- 資安政策：`docs/hacker.md`
