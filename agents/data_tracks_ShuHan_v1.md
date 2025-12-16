# 🏛️ data_tracks_ShuHan_v1.1.md

### *蜀漢多代理軍令通訊協定（Shu-Han Multi-Agent Communication Protocol）*

**Version: 1.1 (Shu-Han Edition)**  
**Last Updated: 2025-12-16**

> 「軍令如山，令出必行；令必有據，行必有報。」

---

## 🚀 Quick Start（五分鐘入門）

**情境**：想新增一個「主力行為戰報」頁面

```
1️⃣ 劉備（人類）：「我要看主力進出場的戰報頁面」
     ↓
2️⃣ 諸葛亮：開一個 TRACK_ID（SHU-20251216-01），
     寫軍令給關羽「[DIRECTIVE]: GENERATE_CODE」
     ↓
3️⃣ 關羽：實作 API 後，用奏折格式回報：
     「[STATUS]: 完成 (SUCCESS)」+ 附上檔案路徑
     ↓
4️⃣ 張飛：只接到「[DIRECTIVE]: DIAGNOSE_ERROR」才出手
     ↓
5️⃣ 姜維：把整個過程記錄在 memory/sessions/ 裡
```

**核心原則**：
- 所有任務都走「軍令 → 奏折」流程
- 五虎將之間不能橫向下令，必須透過諸葛亮
- 每個 TRACK_ID 都可追溯、重播、稽核

---

## 0. 目的（Purpose）

軍令協定（Data Tracks）是蜀漢系統中所有角色（諸葛亮、關羽、張飛、趙雲、馬超、姜維）之間的 **唯一通訊協定**。

它提供：

- 統一格式（標準化軍令）
- 可追溯性（戰報追蹤）
- 任務管理（分兵派將）
- 結構化回報（奏摺制度）
- 系統安全與可審計能力

**所有任務都必須使用軍令協定發送、回覆、記錄。**

---

## 1. TRACK_ID（軍令識別碼）

每一道軍令都必須具有唯一編號，用於：

- 任務追蹤
- 任務重播
- Debug（張飛診斷）
- 姜維（史官）記錄
- 奏摺與軍令對應

### 格式：

```
TRACK_ID: SHU-<YYYYMMDD>-<SEQ>
e.g., SHU-20251216-01
```

### 1.1 多用戶 / 多角色衝突處理

若系統支援多個劉備（團隊模式）或需區分發令角色，可加入角色前綴：

```
TRACK_ID: SHU-<ROLE>-<YYYYMMDD>-<SEQ>
e.g., SHU-ZGL-20251216-01  (諸葛亮發起)
e.g., SHU-GY-20251216-01   (關羽發起)
```

| 角色 | 前綴 | 說明 |
|------|------|------|
| 劉備（Human） | LB | 主公 / 最高決策者 |
| 諸葛亮（Strategy） | ZGL | 丞相 / 策略核心 |
| 關羽（Coding） | GY | 前將軍 / 實作 |
| 張飛（Debug） | ZF | 右將軍 / 除錯 |
| 趙雲（UI） | ZY | 虎威將軍 / UI/UX |
| 馬超（Lab） | MC | 驃騎將軍 / 實驗 |
| 黃忠（Memory） | HZ | 後將軍 / 記憶 |
| 姜維（Historian） | JW | 少將軍 / 史官 |

---

## 2. 軍令格式（Request Schema）

所有任務下達都必須用如下格式：

```
[TRACK_ID]: SHU-20251216-01
[FROM]: <角色名>
[TO]: <角色名>
[DIRECTIVE]: <祈使句動詞指令>
[PRIORITY]: 急報 | 常令 | 緩辦
[SPEC_REF]: <可選，引用的 PRD/SA/SD 路徑>
[SECURITY_CHECK]: <可選，是否需對照 docs/hacker.md>
[CONTEXT]: <任務背景、檔案路徑、規則、限制>
[DEADLINE]: <可選，ISO 8601 格式>
[TIMEOUT]: <可選，秒數>
```

### 2.1 欄位說明

#### PRIORITY（優先級）

| 等級 | 說明 | 諸葛亮處理方式 |
|------|------|----------------|
| 急報 | 緊急任務，影響系統穩定或阻塞其他任務 | 立即處理，可中斷常令/緩辦 |
| 常令 | 標準任務（預設值） | 按順序處理 |
| 緩辦 | 非緊急，可延後 | 排入佇列，空閒時處理 |

#### SPEC_REF（規格引用）

- 指向對應的 PRD / SA / SD 文件
- 例如：`specs/business/20251216_stock_analysis_PRD.md`
- 確保任務有「上位依據」

#### SECURITY_CHECK（安全檢查）

- `YES`：本任務涉及 DB / API / 用戶輸入，需對照 `docs/hacker.md`
- `NO`：純內部邏輯，無安全風險
- 預設為 `NO`

#### TIMEOUT（超時機制）

- 若設定 `[TIMEOUT]: 300`（秒），任務超過 300 秒未完成將自動觸發：

```
STATUS: 受阻
REASON: 超時未完成 (300s)，請升級或縮小範圍重試
ERROR_CODE: ERR-006
```

### 2.2 DIRECTIVE 規範

#### 必須使用「祈使句動詞」

✅ 正確範例：
- `GENERATE_CODE`（生成程式碼）
- `UPDATE_UI`（更新介面）
- `DIAGNOSE_ERROR`（診斷錯誤）
- `WRITE_SPEC`（撰寫規格）
- `PRODUCE_WIREFRAME`（製作線框圖）
- `REVIEW_SECURITY`（審查安全性）

❌ 錯誤範例：
- `CODE_GENERATION`（名詞形式）
- `UI_UPDATE`（名詞形式）

#### CONTEXT 必須包含所有必要資訊

不得依賴「上次對話」的隱含推論。每道軍令都應自給自足。

#### 禁止自我指派（Self-loop）

**例外**：
- **諸葛亮的自省任務**（允許，但必須記錄於姜維）

---

## 3. 奏摺格式（Response Schema）

所有角色必須用以下格式回覆：

```
[REF_TRACK_ID]: SHU-20251216-01
[FROM]: <角色名>
[TO]: <角色名>
[STATUS]: 完成 | 部分完成 | 失敗 | 受阻 | 拒絕
[ERROR_CODE]: <可選，見第 12 節>
[OUTPUT]: <程式碼、檔案路徑、日誌、分析結果>
[NOTES]: <可選，補充說明或偏差記錄>
```

### 3.1 TO 欄位路由規則

| 發送者 | 接收者 | 說明 |
|--------|--------|------|
| 任何角色 | 諸葛亮 | 標準回報路徑 |
| 諸葛亮 | 劉備 | 丞相向主公回報最終結果 |
| 諸葛亮 | 任何角色 | 丞相分派任務 |

**注意**：除諸葛亮外，其他角色不得直接回報給劉備（需透過諸葛亮）。

### 3.2 STATUS 說明

#### ✅ 完成（SUCCESS）

任務成功完成

→ OUTPUT 必須包含具體產物（程式碼、UI、分析等）

#### ⚠️ 部分完成（PARTIAL）

任務部分完成，例如：

- 10 個檔案中完成 7 個
- 主要功能完成，但次要功能未實作
- 因外部依賴問題，部分模組跳過

→ OUTPUT 必須包含：
  - 已完成的部分
  - 未完成的部分及原因
  - 建議的後續行動

→ 諸葛亮決定是否接受或要求補完

#### ❌ 失敗（FAILED）

任務輸入正確，但：

- 無法計算
- 無法取得資料
- 遇到邏輯錯誤

→ OUTPUT 必須包含錯誤訊息  
→ 張飛（Debug）可能會介入

#### 🚧 受阻（BLOCKED）

觸發「糧草條款」或類似阻擋條款，例如：

- 任務嘗試超過兩次未成功
- 需要額外資訊
- 需求不明確

→ 必須升級回諸葛亮與劉備

#### 🛑 拒絕（REJECTED）

因為違反 ALLSPARK_ShuHan 憲章，被合法拒絕：

- 越權操作
- 危險操作
- 非法外部存取
- 角色不符

→ 必須引用 ALLSPARK_ShuHan 條款：

```
STATUS: 拒絕 (REJECTED)
REASON: 違反 ALLSPARK_ShuHan_v1 第 5.1 條（專案邊界）
ERROR_CODE: ERR-002
```

### 3.3 STATUS 中英對照表

為便於機器解析與跨語言 LLM 處理，建議在回報中使用「雙語格式」：

| 中文 | 英文 | 雙語寫法（建議） |
|------|------|------------------|
| 完成 | SUCCESS | `完成 (SUCCESS)` |
| 部分完成 | PARTIAL | `部分完成 (PARTIAL)` |
| 失敗 | FAILED | `失敗 (FAILED)` |
| 受阻 | BLOCKED | `受阻 (BLOCKED)` |
| 拒絕 | REJECTED | `拒絕 (REJECTED)` |

**用途**：
- 人類閱讀：中文語意清晰
- 機器解析：抽取括號內英文關鍵字
- 跨語言 LLM：英文主的模型可直接處理

---

## 4. 通訊路由（Routing Rules）

本協定遵守 ALLSPARK_ShuHan §2：Hub-and-Spoke 模式。

### 4.1 允許路由

```
劉備 → 諸葛亮
諸葛亮 → 任何角色
任何角色 → 諸葛亮
```

### 4.2 禁止路由

- ❌ 關羽 → 趙雲（五虎將不得橫向互相下令）
- ❌ 趙雲 → 張飛
- ❌ 張飛 → 馬超
- ❌ 關羽 → 馬超
- ❌ 任何角色 → 劉備（需透過諸葛亮）

### 4.3 原因

- 諸葛亮是唯一的全局上下文持有者
- 保證規格一致性
- 避免角色之間越權
- 符合「單一指揮中樞」原則

---

## 5. 安全與邊界執行（Safety & Containment）

當任務有以下特徵時，角色必須 **拒絕（REJECTED）**：

- 操作超出專案資料夾
- 需外部 API（未授權）
- 試圖刪除、修改高風險檔案
- 修改角色架構或憲章
- 變更系統設定
- 影響劉備未授權領域

### 範例：

```
STATUS: 拒絕
REASON: 違反 ALLSPARK_ShuHan_v1 第 5.1 條（專案邊界越界）
ERROR_CODE: ERR-002
```

---

## 6. 糧草效率執行（Energon Efficiency）

若任務可能導致：

- 無限迴圈
- 過度冗長輸出
- 無意義嘗試次數（>2 次）
- 整檔重寫而非 patch

則必須回覆：

```
STATUS: 受阻
REASON: 觸發糧草條款（ALLSPARK_ShuHan_v1 第 4 節）
ERROR_CODE: ERR-004
```

---

## 7. 與姜維（史官）的互動

每次軍令（Request & Response）都必須寫入姜維（史官系統）。

### 姜維必須記錄：

1. TRACK_ID
2. 時間戳記（Timestamp）
3. 發令方與接令方
4. STATUS
5. OUTPUT（摘要或全文）
6. Notes
7. 任何 ALLSPARK_ShuHan 違規事件

這讓以下功能變得可行：

- 除錯診斷（張飛）
- 任務回溯（Backtracking）
- 稽核審計（Audit）
- 任務重播（Replay）

### 記錄位置：

| 類型 | 路徑 |
|------|------|
| 日常軍令 | `memory/sessions/` |
| 錯誤踩雷 | `memory/mistakes/` |
| 策略模式 | `memory/patterns/` |
| 重大事件 | `agents/MEETS_log.md` |

---

## 8. 諸葛亮的特殊規則

### 8.1 諸葛亮可以自我對話（Self-reflection）

但必須寫入姜維（史官）記錄。

### 8.2 諸葛亮不得跳過軍令協定

所有諸葛亮的任務也必須用相同格式記錄。

### 8.3 諸葛亮的安全審查責任

涉及 DB / API / 用戶輸入的任務，諸葛亮必須：
1. 在軍令中標註 `[SECURITY_CHECK]: YES`
2. 確保對應的 SA 包含 `Security Considerations` 小節
3. 引用 `docs/hacker.md` 相關條文

---

## 9. 非同步任務（目前禁止）

### 9.1 v1.0 只允許同步

原因：

- Debug 難度高
- 複雜性飆升
- 角色之間會失序
- 姜維無法判定完成狀態

### 9.2 未來非同步計畫

將於 ALLSPARK_ShuHan v2.0 評估加入：

- 長時間回測任務
- 大量資料下載
- 長時間生成流程

---

## 10. 軍令範例（Example Request）

```
[TRACK_ID]: SHU-20251216-03
[FROM]: 諸葛亮
[TO]: 關羽
[DIRECTIVE]: GENERATE_CODE
[PRIORITY]: 常令
[SPEC_REF]: specs/system/20251216_stock_api_SD.md
[SECURITY_CHECK]: YES
[CONTEXT]: 
  依據 SD 規格，建立 /code/api/stock_analysis.py
  - 使用參數化查詢（防 SQL injection）
  - 實作輸入驗證（參照 docs/hacker.md §1）
  - 遵循 specs/architecture.md 第 4.2 節
[DEADLINE]: 2025-12-16T18:00:00+08:00
```

---

## 11. 奏摺範例（Example Response）

### 成功範例：

```
[REF_TRACK_ID]: SHU-20251216-03
[FROM]: 關羽
[TO]: 諸葛亮
[STATUS]: 完成 (SUCCESS)
[OUTPUT]:
  檔案已建立: /code/api/stock_analysis.py
  - 已實作參數化查詢
  - 已加入輸入驗證函式
  (程式碼區塊)
[NOTES]:
  為相容 core/models.py，微調了回傳格式。
  已通過 docs/hacker.md §1 安全檢查清單。
```

### 拒絕範例：

```
[REF_TRACK_ID]: SHU-20251216-05
[FROM]: 關羽
[TO]: 諸葛亮
[STATUS]: 拒絕 (REJECTED)
[ERROR_CODE]: ERR-001
[OUTPUT]: 無
[NOTES]: 
  該任務（重新設計資料庫 schema）屬於諸葛亮職責。
  違反 ALLSPARK_ShuHan_v1 第 1.3 條（角色職權邊界）。
```

---

## 12. 錯誤碼標準（Error Code）

為便於自動化處理和張飛診斷，所有錯誤必須使用標準化 Error Code。

### 12.1 錯誤碼表

| Code | 名稱 | 說明 | 對應 ALLSPARK_ShuHan |
|------|------|------|----------------------|
| ERR-001 | 越權操作 | 角色執行非本職任務 | §1（角色職權邊界） |
| ERR-002 | 邊界越界 | 操作超出專案邊界 | §5.1（專案邊界） |
| ERR-003 | 未授權外部存取 | 未授權的外部 API/網路存取 | §5.1（專案邊界） |
| ERR-004 | 糧草耗盡 | 資源消耗超限（token/迴圈） | §4（糧草條款） |
| ERR-005 | 路由違規 | 違反 Hub-and-Spoke 通訊規則 | §2（指揮結構） |
| ERR-006 | 超時未完成 | 任務超時未完成 | §4（糧草條款） |
| ERR-007 | 情報不足 | CONTEXT 欄位資訊不足 | §2.2（軍令規範） |
| ERR-008 | 指令格式錯誤 | DIRECTIVE 格式錯誤（非祈使句） | §2.1（軍令規範） |
| ERR-009 | 史官異常 | 姜維記錄系統異常 | §7（記憶與史官） |
| ERR-010 | 需主公授權 | 需要劉備明確授權 | §1.1（劉備權限） |
| ERR-011 | 安全違規 | 違反 docs/hacker.md 安全規範 | §5.3（資安政策） |

### 12.2 使用範例

```
[REF_TRACK_ID]: SHU-20251216-07
[FROM]: 張飛
[TO]: 諸葛亮
[STATUS]: 受阻 (BLOCKED)
[ERROR_CODE]: ERR-004
[OUTPUT]: 無
[NOTES]: 
  同一錯誤已嘗試修復兩次，仍未成功。
  觸發糧草條款，請諸葛亮與劉備裁決。
```

### 12.3 錯誤碼擴展規則

- 新增 Error Code 需經劉備授權
- 格式：`ERR-XXX`（三位數字）
- 必須對應 ALLSPARK_ShuHan 條款
- 必須記錄於姜維（史官）

---

## 13. 與文檔體系的整合

軍令協定與 AGENTS_v5.md 文檔體系的關係：

| 文檔類型 | 在軍令中的引用 | 用途 |
|----------|----------------|------|
| PRD | `[SPEC_REF]` | 任務的「為什麼」 |
| SA / SD | `[SPEC_REF]` | 任務的「怎麼做」 |
| hacker.md | `[SECURITY_CHECK]` | 安全檢查依據 |
| E2E 測試 | `[CONTEXT]` 中引用 | 驗收標準 |

---

## 14. 檔案存放約定（File Storage Convention）

為便於自動化處理（Python / SQL 掃檔）與版本控制，建議採用以下存放方式：

### 14.1 軍令（虎符）存放

| 方式 | 路徑格式 | 適用場景 |
|------|----------|----------|
| **單檔模式**（建議） | `orders/ORD-SHU-YYYYMMDD-XX.md` | 每個 TRACK_ID 一份檔案，易於追溯 |
| 每日彙總模式 | `orders/YYYYMMDD_daily_orders.md` | 單日任務量少時使用 |

**單檔模式範例**：
```
orders/
  ├── ORD-SHU-20251216-01.md
  ├── ORD-SHU-20251216-02.md
  └── ORD-SHU-20251216-03.md
```

### 14.2 奏折（回報）存放

| 方式 | 路徑格式 | 適用場景 |
|------|----------|----------|
| **單檔模式**（建議） | `reports/RPT-SHU-YYYYMMDD-XX.md` | 與軍令一一對應 |
| 每日彙總模式 | `reports/YYYYMMDD_daily_reports.md` | 單日任務量少時使用 |

**對應關係**：
```
orders/ORD-SHU-20251216-01.md  ←→  reports/RPT-SHU-20251216-01.md
```

### 14.3 每日彙總模式規範

若採用每日彙總，檔案內各 TRACK_ID 段落必須明確標示：

```markdown
# 20251216 每日軍令彙總

---
## SHU-20251216-01
[TRACK_ID]: SHU-20251216-01
[FROM]: 諸葛亮
...

---
## SHU-20251216-02
[TRACK_ID]: SHU-20251216-02
[FROM]: 諸葛亮
...
```

### 14.4 姜維（史官）歸檔規則

| 類型 | 存放路徑 | 時機 |
|------|----------|------|
| 日常會話 | `memory/sessions/YYYYMMDD_session.md` | 每日結束時 |
| 踩雷記錄 | `memory/mistakes/YYYYMMDD_<topic>.md` | 發現錯誤時 |
| 策略模式 | `memory/patterns/<pattern_name>.md` | 發現可複用模式時 |
| 封存歸檔 | `memory/archives/YYYYMM/` | 每月封存 |

---

## 15. Pull Request 指南（GitHub 協作規範）

當蜀漢框架專案放上 GitHub 共享時，PR 視為「奏摺」的延伸形式。

### 15.1 PR 與蜀漢角色對應

| GitHub 角色 | 蜀漢角色 | 職責 |
|-------------|----------|------|
| Contributor | 五虎將 | 提交 PR（程式碼、文件） |
| Reviewer | 諸葛亮 | 審核 PR、提出修改建議 |
| Maintainer | 劉備 | 最終 Merge 決策權 |

### 15.2 Branch 命名規範

```
<類型>/<TRACK_ID>-<簡述>
```

| 類型 | 說明 | 範例 |
|------|------|------|
| `feat/` | 新功能 | `feat/SHU-20251216-01-stock-api` |
| `fix/` | 錯誤修復 | `fix/SHU-20251216-02-login-bug` |
| `docs/` | 文件更新 | `docs/SHU-20251216-03-update-readme` |
| `refactor/` | 重構 | `refactor/SHU-20251216-04-clean-code` |
| `test/` | 測試相關 | `test/SHU-20251216-05-e2e-login` |
| `exp/` | 實驗性（馬超） | `exp/SHU-20251216-06-new-model` |

### 15.3 Commit Message 格式

採用 **Conventional Commits** 風格，並關聯 TRACK_ID：

```
<類型>(<範圍>): <簡述> [TRACK_ID]

<詳細說明>

<關聯>
```

**範例**：
```
feat(api): 實作股票分析 API [SHU-20251216-01]

- 新增 /api/stock_analysis 端點
- 使用參數化查詢防止 SQL injection
- 通過 docs/hacker.md §1 安全檢查

Refs: specs/system/20251216_stock_api_SD.md
```

**類型對照表**：

| 類型 | 說明 | 對應角色 |
|------|------|----------|
| `feat` | 新功能 | 關羽 |
| `fix` | 錯誤修復 | 張飛 |
| `ui` | UI/UX 變更 | 趙雲 |
| `exp` | 實驗性功能 | 馬超 |
| `docs` | 文件更新 | 諸葛亮 / 姜維 |
| `test` | 測試相關 | 張飛 |
| `refactor` | 重構（需諸葛亮批准） | 關羽 |
| `chore` | 雜項維護 | 任何角色 |

### 15.4 PR 標題格式

```
[<類型>] <簡述> (#TRACK_ID)
```

**範例**：
- `[feat] 實作股票分析 API (#SHU-20251216-01)`
- `[fix] 修復登入驗證錯誤 (#SHU-20251216-02)`
- `[docs] 更新 ALLSPARK 憲章 (#SHU-20251216-03)`

### 15.5 PR 描述模板

建議在專案根目錄建立 `.github/PULL_REQUEST_TEMPLATE.md`：

```markdown
## 🎫 關聯軍令
- TRACK_ID: SHU-YYYYMMDD-XX
- 虎符位置: orders/ORD-SHU-YYYYMMDD-XX.md

## 📋 變更類型
- [ ] ✨ 新功能 (feat)
- [ ] 🐛 錯誤修復 (fix)
- [ ] 🎨 UI/UX (ui)
- [ ] 🧪 實驗性 (exp)
- [ ] 📝 文件 (docs)
- [ ] ♻️ 重構 (refactor)
- [ ] ✅ 測試 (test)

## 📜 變更說明
<!-- 描述你做了什麼 -->

## 🔗 相關文件
- PRD: specs/business/...
- SD: specs/system/...
- Security: docs/hacker.md §...

## ✅ 自檢清單
- [ ] 程式碼遵循 ALLSPARK_ShuHan 憲章
- [ ] 已通過 docs/hacker.md 安全檢查（若涉及 DB/API）
- [ ] 已更新相關文件
- [ ] 已加入/更新測試
- [ ] Commit message 符合規範

## 🚧 禁令確認
- [ ] 未違反角色職權邊界
- [ ] 未擅自修改架構（若有需經諸葛亮批准）
- [ ] 未引入未授權的外部依賴

## 📎 截圖/附件
<!-- 若有 UI 變更，請附上截圖 -->
```

### 15.6 PR 審核流程

```
┌─────────────────────────────────────────────────────────────────┐
│  五虎將：提交 PR                                                  │
│    ↓                                                            │
│  自動檢查：CI/CD（Lint、Test、Security Scan）                     │
│    ↓                                                            │
│  諸葛亮（Reviewer）：審核程式碼                                    │
│    ├── ✅ Approve → 劉備 Merge                                   │
│    ├── 🔄 Request Changes → 五虎將修改後重新提交                   │
│    └── ❌ Reject → 關閉 PR，記錄於 memory/mistakes/               │
│    ↓                                                            │
│  姜維：記錄 PR 結果於 memory/sessions/                            │
└─────────────────────────────────────────────────────────────────┘
```

### 15.7 PR 審核標準

**必須通過**：
1. ✅ CI/CD 全綠（Lint、Test、Build）
2. ✅ 符合 ALLSPARK_ShuHan 憲章
3. ✅ 有對應的 TRACK_ID（軍令）
4. ✅ 涉及安全相關已通過 hacker.md 檢查
5. ✅ 至少一位 Reviewer（諸葛亮）Approve

**禁止 Merge**：
- ❌ 違反角色職權邊界
- ❌ 未經批准的架構變更
- ❌ 引入未授權的外部依賴
- ❌ 安全檢查未通過

### 15.8 緊急修復流程（Hotfix）

若遇緊急 Bug 需立即修復：

1. 建立 Branch：`hotfix/SHU-YYYYMMDD-XX-<issue>`
2. PR 標題加上 `[URGENT]` 前綴
3. 可由劉備直接 Merge（跳過完整審核）
4. **事後必須**：
   - 補齊對應的虎符（orders/）
   - 記錄於 memory/mistakes/
   - 進行事後審核（Post-mortem）

---

## 16. Issue 指南（GitHub Issue 規範）

### 16.1 Issue 標籤對應

| 標籤 | 對應角色 | 說明 |
|------|----------|------|
| `bug` | 張飛 | 需要除錯修復 |
| `feature` | 關羽 | 新功能需求 |
| `ui/ux` | 趙雲 | 介面相關 |
| `experiment` | 馬超 | 實驗性需求 |
| `docs` | 姜維 | 文件相關 |
| `security` | 諸葛亮 | 安全相關 |
| `question` | 諸葛亮 | 需要討論 |
| `blocked` | 劉備 | 需要主公裁決 |

### 16.2 Issue 模板

建議建立 `.github/ISSUE_TEMPLATE/bug_report.md`：

```markdown
---
name: 🐛 Bug 回報
about: 回報錯誤或異常行為
labels: bug
assignees: ''
---

## 🐛 問題描述
<!-- 簡述問題 -->

## 📋 重現步驟
1. ...
2. ...

## ✅ 預期行為
<!-- 應該發生什麼 -->

## ❌ 實際行為
<!-- 實際發生什麼 -->

## 🔗 相關 TRACK_ID
<!-- 若有關聯的軍令 -->

## 📎 截圖/Log
<!-- 附上相關資訊 -->
```

---

## 🏛️ 本協定對所有角色具約束力

**不遵守軍令協定，視為系統錯誤。**

---

> **北伐出征，令出必行。**  
> *— 蜀漢多代理作業系統*
