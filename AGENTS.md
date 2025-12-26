# AGENTS.md — Project Runbook（五虎將／操作者用）

> **治理與權責**：請參照 `agents/AGENTS_v5.x.md`（蜀漢 v5）與 `ALLSPARK`。  
> **行動授權**：僅限 `orders/` 虎符；無虎符，不得動工。  
> **說明**：本檔不談治理哲學，只提供最短命令與邊界；一切制度以蜀漢 v5 / ALLSPARK 為準。

---

## 0. 對號入座（你的定位）

| 角色 | 你要做的事 |
|------|-----------|
| **關羽（Coding）** | 實作功能、補齊單元測試 |
| **張飛（Debug）** | 修錯、補防回歸測試 |
| **趙雲（UI）** | UI/UX 行為與可自動化斷言 |
| **馬超（Sandbox）** | 實驗與 PoC（不可直接結案） |

> **回報只接受「已通過單元測試」的成果；E2E 是軍令驗收，不替代單元測試。**（測試金字塔精神）

---

## 1. 專案概述

- **目的**：蜀漢多代理 AI 框架（Shu-Han Multi-Agent Framework）
- **願景**：建立一套具有文化厚度且具備自我迭代能力的 AI 多代理治理架構
- **非功能性**：可審計、可追溯、人類保有最終控制權

---

## 2. 文件結構（你會用到的就這些）

```
/
├── agents/                    # 🏛️ 蜀漢治理核心
│   ├── AGENTS_v5.1.md         # 國家治理架構（繁體）
│   ├── AGENTS_v5.1_ZH_CN.md   # 國家治理架構（簡體）
│   ├── AGENTS_v6_skill.md     # Skill 治理與工具適配層
│   ├── ALLSPARK_ShuHan_v1.md  # 軍紀憲章
│   └── data_tracks_ShuHan_v1.md # 虎符通訊協定
├── specs/                     # 📋 規格文件
│   ├── business/              # BRD / PRD
│   ├── system/                # SA / SD
│   └── testing/e2e/           # E2E 測試場景
├── orders/                    # 🐯 虎符（唯一合法指令）
├── reports/                   # 📜 奏摺（執行回報）
├── memory/                    # 📚 太史院
│   ├── mistakes/              # 踩雷記錄
│   ├── patterns/              # 策略模式
│   ├── sessions/              # 會話記錄
│   └── archives/              # 歸檔
├── code/                      # 關羽駐地（後端實作）
├── design/                    # 趙雲駐地（前端設計）
├── debug/                     # 張飛駐地（除錯）
├── sandbox/                   # 馬超駐地（實驗區）
├── tests/                     # ✅ 測試（集中管理）
├── docs/
│   ├── hacker.md              # 資安規範
│   └── 蜀漢 × AI 公司...md    # 落地實戰指南
├── examples/                  # 範例（first_campaign）
└── AGENTS.md                  # 本檔（Runbook）
```

> **命名規則**：測試檔案 `test_*.py` 或 `*_test.py`；函式 `test_*`；類名 `Test*`。

---

## 3. 開發程序（六步走，照順序）

```
1️⃣ 收虎符 → 讀 orders/ORD-xxxx.md，確認責任範圍與 E2E 驗收條件（GWT）
       ↓
2️⃣ 開分支 → git switch -c feat/<ord-id>-<slug>
       ↓
3️⃣ 實作 → 只改虎符授權範圍的模組（不得擴 scope）
       ↓
4️⃣ 單元測試 → 新增或更新 tests/ 中對應案例（測不過＝未完成）
       ↓
5️⃣ 回報奏摺 → 提交 reports/，附通過的測試摘要
       ↓
6️⃣ E2E 驗收 → 依虎符描述走完；全綠 → 北伐完成（合併）
```

> **詳細制度與模板**：見 `agents/AGENTS_v5.1.md` 與 `agents/ALLSPARK_ShuHan_v1.md`。

---

## 4. 快速命令

### Git 操作

```bash
# 開分支
git switch -c feat/ORD-20251226-01-feature-name

# 提交
git add -A
git commit -m "feat(scope): description"
git push
```

### 測試命令（Python 專案）

```bash
# 執行所有測試
uv run pytest

# 指定檔案／單一測試
uv run pytest tests/test_x.py
uv run pytest tests/test_x.py::test_case

# 常用標記
uv run pytest -m unit          # 單元測試
uv run pytest -m integration   # 整合測試
uv run pytest -m e2e           # E2E 測試

# 詳情與覆蓋率
uv run pytest -v
uv run pytest --cov
```

---

## 5. E2E（你需要知道的）

- **E2E 是軍令驗收**：在虎符內用 GWT（Given/When/Then）描述完整路徑
- **你的責任**：把單元層級打乾淨，不把可預防的錯誤留給 E2E
- **E2E 不替代單測**：單元測試是基礎，E2E 是最終驗收

### E2E 驗收範例（GWT 格式）

```gherkin
Given 使用者開啟蜀漢框架文檔
When 輸入「建立新功能」的需求
Then 系統生成符合虎符格式的指令
And 指令包含完整的驗收條件
```

---

## 6. 虎符格式速查

```markdown
# 虎符 (Tiger Tally)
**令號**：ORD-YYYYMMDD-XX
**發令者**：諸葛亮
**接令者**：關羽 / 張飛 / 趙雲 / 馬超

## 🎯 戰略目標 (Directive)
（一句話說明要做什麼）

## 📜 依據 (Context)
- specs/system/xxx.md
- docs/hacker.md §X

## 🚧 禁令與邊界 (Constraints)
- （不得做的事）

## 🔧 Skill 授權 (Skill Authorization)
- ✅ 允許：[skill-list]
- ❌ 禁止：[skill-list]

## ✅ 驗收標準 (Acceptance Criteria)
1. （可驗證的條件）

## ⚠️ 升級條件 (Escalation)
若失敗超過 2 次，停止嘗試，回報給丞相。
```

---

## 7. 奏摺格式速查

```markdown
# 奏摺 (Report)
**對應令號**：ORD-YYYYMMDD-XX
**上奏者**：關羽
**結果**：SUCCESS / PARTIAL / FAILED / BLOCKED

## 📝 執行內容
- （做了什麼）

## 🔧 Skill 使用紀錄
- 使用：xxx
- 未使用：xxx（原因）

## 🐛 遭遇問題
（無 / 問題描述）

## 📎 附件
- （相關檔案路徑）
```

---

## 8. 禁止事項（軍紀）

| ❌ 禁止 | 說明 |
|--------|------|
| 無虎符動工 | 沒有 `orders/` 授權，不得修改 code |
| 跳過單元測試 | 測不過 = 未完成 |
| 擅自修改 specs | 規格需經諸葛亮 + 劉備核准 |
| 擴大任務範圍 | 只做虎符授權的事 |
| 「之後補測試」 | 不接受此理由的提交 |
| 自選 Skill | 執行層不得自行啟用或切換 Skill（v6） |

---

## 9. 安全規範

所有開發人員必須閱讀並遵守 `docs/hacker.md` 中的安全規範：

- 參數化查詢（防 SQL Injection）
- 輸入驗證
- 最小權限原則
- SA/SD 必須包含 Security Considerations

---

## 10. 人類對齊門檻（Human Alignment Gate）

> ⚠️ **鐵律：沒有 Approval 的 PRD / SD，不得發虎符，不得動 code。**

所有規格文件（BD/MRD/PRD/SA/SD/E2E）末尾必須包含 Approval 區塊：

```markdown
## Approval

| 欄位 | 值 |
|------|-----|
| **Status** | DRAFT / APPROVED |
| **Approved by** | <Human 名稱> |
| **Approved at** | YYYY-MM-DD |
| **Reference** | <order id / issue link / session id> |
```

只有 Status 為 `APPROVED` 的文件，才可作為虎符依據。

---

## 一句話總結

> **單元測試 ＝ 五虎將軍紀；E2E ＝ 軍師驗收；一切行動，以虎符為準。**

---

> **相關文件**：
> - [agents/AGENTS_v5.1.md](agents/AGENTS_v5.1.md) — 蜀漢治理架構
> - [agents/AGENTS_v6_skill.md](agents/AGENTS_v6_skill.md) — Skill 治理
> - [agents/ALLSPARK_ShuHan_v1.md](agents/ALLSPARK_ShuHan_v1.md) — 軍紀憲章
> - [docs/hacker.md](docs/hacker.md) — 資安規範
> - [docs/蜀漢 × AI 公司：Agentic AI 落地實戰指南.md](docs/蜀漢%20×%20AI%20公司：Agentic%20AI%20落地實戰指南.md) — 落地指南
