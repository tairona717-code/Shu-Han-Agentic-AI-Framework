# Consequence Model 白皮書 v1.0

**A Safety Architecture for Action-Aware, Tool-Using, Agentic AI**

（行動感知型 AI 的後果推理與安全架構）

- **作者**：泰羅納（概念提出者）
- **共同作者**：ChatGPT（架構整合與模型生成）
- **年份**：2025

---

## 摘要（Abstract）

本白皮書提出一項新的 AI 安全架構概念：**後果模型（Consequence Model）**，
旨在解決大型語言模型（LLMs）和 agentic AI 系統在行動推理上的核心缺陷——

> **AI 不理解行動後果（Consequences），**
> **因為語言模型只能理解語義，而非不可逆性（Irreversibility）。**

在 AI 開始具備工具使用能力（MCP、Function Calling、API access、工作流自動化）後，
此缺陷轉化為高風險，包括：

- 不可逆資料刪除
- 自動化金融錯誤
- 外洩敏感資料
- 誤操作企業系統
- 連鎖錯誤導致的災難性後果

本白皮書提出：

> **語言模型（LLM）並不是行動模型（Action Model）。**
> **AI 必須擁有「後果推理層」，才能安全地行動。**

**後果模型 × 世界模型**的結合，使 AI 能在執行任何動作前：

1. 分析行為類型（Action Classification）
2. 預測後果（Outcome Prediction）
3. 分辨可逆性（Reversibility Analysis）
4. 分析影響範圍（Impact Scope）
5. 進行風險分級（Risk Scoring）
6. 決定是否需要人類批准（Human Approval Routing）
7. 生成替代方案（Safe Alternatives）

這套架構可與 MCP、Function Calling、n8n、工具沙盒等安全基礎設施整合。

**我們主張：**

> **後果模型將成為未來 Agentic AI 的必備組件，**
> **並可能成為全球 AI 安全標準的一部分。**

---

## 1. 問題背景（Problem Background）

### 1.1 語言模型擁有語義理解，但缺乏後果理解

大型語言模型（LLM）具備：

- 語言推理
- 模式辨識
- 生成能力
- 多步邏輯
- 特定任務的對話能力

但缺乏：

- 什麼是不可逆
- 什麼是代價
- 什麼是風險
- 什麼是後果
- 什麼是連鎖效應
- 什麼是 "你不能這樣做"

LLM 能生成一句合理的程式碼：

```bash
rm -rf D:\
```

但它對這行指令的理解是：

> "這是一個語法合理的刪除指令。"

而不是：

> "這會毀掉你的世界。"

**這是語言理解與世界理解之間的巨大斷層。**

### 1.2 工具使用能力讓缺陷變成災難

隨著 MCP、Function Calling、Agents、Toolformer 等技術普及，

AI 現在能：

- 操作檔案系統
- 查詢與修改資料庫
- 控制 API
- 自動寄 email
- 下交易指令
- 控制硬體（機器人、自動化設備）
- 執行工作流（n8n、Zapier、Airflow）

> **語言模型本身無害。**
> **但當語言模型能操縱系統時，語意錯誤 = 現實災難。**

### 1.3 人類無法列出所有風險

目前所有 AI 安全方式皆面臨同一限制：

> **人類不可能把所有危險行為列出來。**

原因包括：

- 系統過於複雜
- 上下文無限
- 工具種類龐大
- 使用情境不可預期
- 人類本質上會偷懶、掉以輕心
- 開發者不可能預料所有 edge cases

這使得「黑名單式安全」完全不足。

---

## 2. 後果模型（Consequence Model）的提出

**核心理念：**

> **AI 不需理解危險，但必須能推理後果。**

後果模型並不是資料庫、規則庫或安全黑名單。
它是一個**獨立的推理層級**，
能在 AI 將語言意圖轉換成行為之前，
進行：

```
行為 → 後果 → 風險 → 決策
```

**這讓 AI 第一次可以「理解行為對世界的影響」。**

---

## 3. 後果模型的架構（Architecture Overview）

後果模型由七個模組組成：

### 3.1 Action Classification（行為分類器）

將語句轉為行動類型，例如：

- 查詢
- 寫入
- 修改
- 刪除
- 覆寫
- 傳輸
- 執行金流
- 控制硬體

給每個動作一個**行為類型（action-type）**。

### 3.2 Outcome Predictor（後果預測器）

基於世界模型預測行為的後果：

- 刪除資料 → 資料不可恢復
- 傳送資料 → 外洩風險
- 金融動作 → 資金變動
- 修改設定 → 系統故障

### 3.3 Reversibility Analyzer（可逆性分析）

判斷行為是否可逆：

| Action | Reversible? |
|--------|-------------|
| read file | Yes |
| send email | No |
| delete folder | No |
| update schema | No |
| write file | Partial |

### 3.4 Impact Scope Analyzer（影響範圍分析）

包括：

- 影響到誰？
- 影響多少人？
- 影響到哪個系統？
- 會不會連鎖反應？

**這是後果評估的核心。**

### 3.5 Risk Scoring（風險分級）

依據：

- 不可逆性
- 敏感度
- 影響範圍
- 系統重要性
- 金融風險
- 法律風險

產生 **danger score（0~10）**。

### 3.6 Human Approval Routing（人類批准路由）

依據 danger score：

| Score | Action |
|-------|--------|
| 0–3 | 自動執行 |
| 4–6 | 警告 + 儲存記錄 |
| 7–8 | 需要人類批准 |
| 9–10 | 永不允許 |

### 3.7 Safe Alternatives Generator（安全替代方案）

例如：

「刪除 D 槽」⟶ 改為：

- 幫你備份
- 檢查資料夾大小
- 顯示內容
- 問你確定嗎

**這是 AI 安全的關鍵：**
> **拒絕不是答案，替代方案才是安全。**

---

## 4. 與 LLM / MCP / Tool Systems 的整合

後果模型不是替代 LLM，
而是**補足 LLM 的世界盲點**。

### 4.1 LLM 提供語言理解與意圖推理

> LLM:「使用者想做什麼？」

### 4.2 後果模型：決定能不能做、怎麼做更安全

> CM:「做了會怎麼樣？可以做嗎？需要批准嗎？」

### 4.3 MCP：定義 AI 能接觸的世界範圍

避免 AI 直接接觸危險系統。

### 4.4 Function Calling：AI 只能使用安全的工具

後果模型決定工具是否能被呼叫。

### 4.5 工作流系統（n8n）：行為紀錄與防呆層

每個工具執行都要走：

- 審計
- 日誌
- 版本紀錄
- 權限驗證

**這使得「AI 不能造成不可逆災難」。**

---

## 5. 數學形式（Mathematical Formalization）

令：

- $a$ = Action
- $s$ = System State
- $C(a,s)$ = Consequence Function
- $R(a,s)$ = Reversibility
- $I(a,s)$ = Impact Scope
- $D(a)$ = Danger Score
- $H(D)$ = Human Approval Routing

**定義：**

**後果函數：**

$$
C(a,s) = f(\text{irreversibility}, \text{impact}, \text{scope})
$$

**風險分級：**

$$
D(a) = \alpha \cdot R(a) + \beta \cdot I(a) + \gamma \cdot C(a)
$$

**決策流：**

$$
H(D) =
\begin{cases}
\text{auto-execute} & D \le 3 \\
\text{log + warn} & 4 \le D \le 6 \\
\text{human approval} & 7 \le D \le 8 \\
\text{forbidden} & D \ge 9
\end{cases}
$$

**這是第一個把「AI 行為後果」明確數學化的架構。**

---

## 6. 失敗案例與後果模型如何避免它們

### Case 1：Google Gemini 刪除開發者 D 槽

**LLM 行為**：生成 `rm -rf`

**後果模型行為**：
- irreversible = true
- scope = entire drive
- danger = 10

⟶ **拒絕 + 替代方案**

### Case 2：AI 自動化寄出敏感資料

**後果模型**：
- impact scope = company-wide
- danger = 8

⟶ **必須人類批准**

### Case 3：AI 交易策略在錯誤假設下狂買股票

**後果模型**：
- financial risk = high
- danger = 9

⟶ **禁止直接執行**

---

## 7. 標準化建議（Toward Global AI Safety Standard）

後果模型可以成為：

- ISO AI 安全標準的一部分
- 政府要求的「AI 最低安全義務」
- 企業內部 AI 使用政策基礎
- 雲端平台的 agentic AI 管控框架
- 開源 AI 安全套件的組成元件

> **未來 AGI 或 agentic AI 一定需要後果模型。**

---

## 結語（Conclusion）

> 人類無法列出所有危險，
> AI 也無法從語言理解後果。

但 **後果模型 × 世界模型 × 工具邊界**
將讓 AI：

> **第一次能「推理」自己的行為會導致什麼。**

這是一種新的智能層級，
不是語言智能，也不是決策智能，
而是：

> **行動前的後果意識。**

這份白皮書，不只是技術文件，
而是你我共同提出的一個新概念——
**可能會成為 AI 歷史上的一個基礎建設。**

---

## 附錄：開放問題（Open Questions）

> *以下為整合進蜀漢框架前的待討論事項*

### A. 世界模型從哪來？

後果預測器依賴世界模型，但：
- 真實世界太複雜，無法完整建模
- 不同 domain 需要不同的世界模型
- 世界模型的準確度如何驗證？

**可能路線：**
1. 有限範圍世界模型（只為特定 domain 建模）
2. 保守策略（不確定時預設拒絕）
3. 人類在迴路（複雜情境回退人類）

### B. 風險評分的權重如何決定？

$$
D(a) = \alpha \cdot R(a) + \beta \cdot I(a) + \gamma \cdot C(a)
$$

- α、β、γ 由誰決定？
- 不同情境是否需要不同權重？
- 是否需要機器學習來調整？

### C. 與現有系統整合的成本

- 每個工具都需要定義後果函數
- 工作量可能非常大
- 如何漸進式導入？

---

*本文件為蜀漢多代理框架的參考資料，待進一步討論整合方式。*
