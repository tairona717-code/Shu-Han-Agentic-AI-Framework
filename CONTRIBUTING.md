# 🏛️ 貢獻指南（Contributing Guidelines）

歡迎加入蜀漢多代理框架的北伐行列！

本指南說明如何參與專案貢獻，請在提交任何 PR 前詳閱。

---

## 📜 必讀文件

在開始貢獻前，請先閱讀以下核心文件：

| 文件 | 說明 |
|------|------|
| [ALLSPARK_ShuHan_v1.md](agents/ALLSPARK_ShuHan_v1.md) | 軍紀憲章（最高準則） |
| [AGENTS_v5.1.md](agents/AGENTS_v5.1.md) | 國家治理架構 |
| [data_tracks_ShuHan_v1.md](agents/data_tracks_ShuHan_v1.md) | 軍令通訊協定（含 PR 指南） |
| [docs/hacker.md](docs/hacker.md) | 資安政策 |

---

## 🎭 角色對應

| GitHub 角色 | 蜀漢角色 | 職責 |
|-------------|----------|------|
| Contributor | 五虎將 | 提交 PR |
| Reviewer | 諸葛亮 | 審核 PR |
| Maintainer | 劉備 | 最終決策 |

---

## 🚀 快速開始

### 1. Fork & Clone

```bash
git clone https://github.com/<your-username>/Shu-Han.git
cd Shu-Han
```

### 2. 建立 Branch

```bash
git checkout -b feat/SHU-YYYYMMDD-XX-your-feature
```

### 3. 開發與提交

```bash
git add .
git commit -m "feat(api): 實作新功能 [SHU-YYYYMMDD-XX]"
```

### 4. 推送與建立 PR

```bash
git push origin feat/SHU-YYYYMMDD-XX-your-feature
```

---

## ⚔️ 無虎符，不行動

**任何改動都必須對應一張軍令（虎符）**：

- **路徑**：`orders/ORD-YYYYMMDD-XX.md`
- **內容**：目的、依據、範圍、驗收、風險、回滾

```markdown
# 軍令範例
**ID**: ORD-20251217-01
**目的**: 新增策略生成 API
**依據**: PRD-strategy-v1.md
**範圍**: code/api/strategy.py
**驗收**: E2E 測試通過
**風險**: API 變更可能影響前端
**回滾**: git revert + 通知前端團隊
```

> ⚠️ **沒有 order 的 PR 會被拒絕**

---

## 📋 先規格，後程式

功能改動需先有：

1. **PRD**（`specs/business/`）— 業務需求
2. **SA/SD**（`specs/system/`）— 系統設計

小修可用「Mini-SD」寫在 order 裡：

```markdown
## Mini-SD
- 改動檔案: xxx.py
- 改動原因: 修復 edge case
- 影響範圍: 僅該函數
```

---

## 📝 Commit Message 格式

```
<類型>(<範圍>): <簡述> [TRACK_ID]

<詳細說明>

Refs: <相關文件>
```

**類型**：
- `feat` — 新功能（關羽）
- `fix` — 錯誤修復（張飛）
- `ui` — UI/UX 變更（趙雲）
- `exp` — 實驗性功能（馬超）
- `docs` — 文件更新（姜維）
- `test` — 測試相關
- `refactor` — 重構（需經批准）
- `chore` — 雜項維護

---

## 🌿 Branch 命名

```
<類型>/<TRACK_ID>-<簡述>
```

範例：
- `feat/SHU-20251216-01-stock-api`
- `fix/SHU-20251216-02-login-bug`
- `docs/SHU-20251216-03-update-readme`

---

## ✅ PR 自檢清單

提交 PR 前，請確認：

- [ ] 程式碼遵循 ALLSPARK_ShuHan 憲章
- [ ] 已通過 docs/hacker.md 安全檢查（若涉及 DB/API）
- [ ] 已更新相關文件
- [ ] 已加入/更新測試
- [ ] Commit message 符合規範
- [ ] 有對應的 TRACK_ID（軍令）
- [ ] PR 描述包含 Security considerations

---

## 🧠 記憶系統要更新

修 bug 或踩雷後，請更新至少一項：

| 目錄 | 用途 | 更新時機 |
|------|------|----------|
| `memory/mistakes/` | 避免重犯 | 發現錯誤後 |
| `memory/patterns/` | 可重用規則 | 發現好做法 |
| `memory/sessions/` | 重要迭代摘要 | Pipeline 變更後 |

範例：

```markdown
# memory/mistakes/M-20251217-01.md

**錯在哪**: 未驗證用戶輸入導致 SQL injection
**如何避免**: 使用 parameterized query
**關聯 PR**: #42
```

> 📌 **不更新記憶 = 同樣的錯誤會重複發生**

---

## 🚫 禁止事項

依據 ALLSPARK_ShuHan 憲章，以下行為將導致 PR 被拒絕：

1. **架構叛亂** — 未經批准擅自大改架構
2. **越權操作** — 超出角色職責範圍
3. **幻象資訊** — 編造不實資訊
4. **邊界越界** — 操作專案外資源
5. **安全違規** — 違反 hacker.md 規範

---

## 📋 PR 審核流程

```
提交 PR → CI 檢查 → 諸葛亮審核 → 劉備 Merge
                        ↓
                  Request Changes
                        ↓
                    修改重提
```

---

## 🆘 需要幫助？

- 開 Issue 並加上 `question` 標籤
- 查閱 [data_tracks_ShuHan_v1.md](agents/data_tracks_ShuHan_v1.md) §15-16

---

> **北伐出征，令出必行。**  
> *— 蜀漢多代理作業系統*
