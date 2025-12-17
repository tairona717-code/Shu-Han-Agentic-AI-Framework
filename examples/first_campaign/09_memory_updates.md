# 09 Memory Updates

## 記憶系統結構

```
memory/
├── mistakes/    # 錯誤記錄（避免重犯）
├── patterns/    # 可重用規則
└── sessions/    # Pipeline 執行摘要
```

## mistakes/

### M-20250101-01.md

```markdown
# Mistake Record

**ID**: M-20250101-01  
**Date**: 2025-01-01  
**Pipeline**: STRATGEN_V1  
**Trace ID**: TRACE-20250101-XXXX

## 錯在哪

財報資料查詢時使用了錯誤的年份範圍，導致 ROE 計算使用了過時資料。

## 根本原因

- `get_financials` 的 `year_range` 參數預設值未更新
- 缺乏資料新鮮度檢查

## 如何避免

1. 在 A05 (fetch_fundamentals) 加入資料日期驗證
2. 若資料超過 90 天，標註 `stale` 警告
3. 更新 Guardrail 規則，檢查 `data_freshness`

## 狀態

- [x] 已識別
- [x] 已分析
- [ ] 已修復
- [ ] 已驗證

## 關聯

- Pattern: P-20250101-01 (資料新鮮度檢查規則)
```

## patterns/

### P-20250101-01.md

```markdown
# Pattern Record

**ID**: P-20250101-01  
**Date**: 2025-01-01  
**Category**: Data Validation  
**Reusability**: High

## 可重用規則

### 資料新鮮度檢查

在任何資料拉取後，執行以下檢查：

```python
def check_data_freshness(data, max_age_days=90):
    latest_date = data['date'].max()
    age_days = (today - latest_date).days
    
    if age_days > max_age_days:
        return {
            'status': 'stale',
            'warning': f'資料已過時 {age_days} 天',
            'recommendation': '建議更新資料源'
        }
    return {'status': 'fresh'}
```

## 適用場景

- 價量資料（建議 max_age: 1 天）
- 財報資料（建議 max_age: 90 天）
- 籌碼資料（建議 max_age: 7 天）

## 來源

- Mistake: M-20250101-01

## 狀態

- [x] 已定義
- [x] 已實作
- [ ] 已測試
- [ ] 已部署
```

## sessions/

### S-20250101-01.md

```markdown
# Session Record

**ID**: S-20250101-01  
**Date**: 2025-01-01  
**Pipeline**: STRATGEN_V1  
**Trace ID**: TRACE-20250101-XXXX

## Pipeline Summary

| 階段 | 狀態 | 耗時 |
|------|------|------|
| Validate Input | ✅ | 50ms |
| Fetch Data | ✅ | 800ms |
| Build Features | ✅ | 300ms |
| Multi-Agent | ✅ | 1200ms |
| Guardrails | ✅ | 100ms |
| Emit Output | ✅ | 50ms |
| **Total** | ✅ | **2500ms** |

## Key Metrics

| 指標 | 值 |
|------|-----|
| Cache Hit | No |
| Guardrails Passed | Yes |
| Output Quality Score | 0.92 |
| User Satisfaction | Pending |

## Issues Encountered

1. 財報資料略有延遲（已標註）
2. 無其他重大問題

## Follow-ups

- [ ] 驗證策略回測結果
- [ ] 收集用戶反饋
- [ ] 更新 Cache 策略

## Related Records

- Mistake: M-20250101-01
- Pattern: P-20250101-01
```

## 對應蜀漢治理

- **mistakes/** → 姜維諫言的「省」階段
- **patterns/** → 可傳承的戰術知識
- **sessions/** → 每次北伐的戰報
