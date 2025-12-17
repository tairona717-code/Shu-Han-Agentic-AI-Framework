# 10 E2E Test

## Test Case: Strategy Generation

### Given (前置條件)

```yaml
input:
  ticker: "XXXX"  # 或使用測試用 ticker
  period: "1y"
  risk_profile: "balanced"
  
environment:
  pipeline: STRATGEN_V1
  mock_data: false  # 使用真實資料
  timeout: 30s
```

### When (執行動作)

```bash
# CLI 執行範例
$ shu-han run stratgen --ticker XXXX --period 1y --risk balanced

# 或 API 呼叫
POST /api/v1/strategy/generate
{
  "ticker": "XXXX",
  "period": "1y",
  "risk_profile": "balanced"
}
```

### Then (預期結果)

#### 1. 輸出結構正確

```yaml
expect:
  - strategies.length == 3
  - strategies[*].strategy_name in ["Value", "Momentum", "Balanced"]
  - strategies[*].entry != null
  - strategies[*].exit != null
  - strategies[*].stop_loss matches /^-?\d+%$/
  - strategies[*].take_profit matches /^-?\d+%$/
```

#### 2. 資料來源完整

```yaml
expect:
  - strategies[*].data_used.length > 0
  - all data_used items exist in fetched_data
```

#### 3. 無幻覺數字

```yaml
expect:
  - all numeric values in output are grounded
  - no fabricated statistics
  - no invented percentages
```

#### 4. 效能要求

```yaml
expect:
  - total_execution_time < 10s  # 或你的 SLA
  - api_response_time < 15s
```

#### 5. 記憶更新

```yaml
expect:
  - session record created in memory/sessions/
  - trace_id is valid and queryable
```

## Test Script (Pseudocode)

```python
def test_stratgen_e2e():
    # Arrange
    input_data = {
        "ticker": "XXXX",
        "period": "1y",
        "risk_profile": "balanced"
    }
    
    # Act
    start = time.now()
    result = pipeline.run("STRATGEN_V1", input_data)
    elapsed = time.now() - start
    
    # Assert - Structure
    assert len(result["strategies"]) == 3
    for s in result["strategies"]:
        assert s["strategy_name"] in ["Value", "Momentum", "Balanced"]
        assert s["entry"] is not None
        assert s["exit"] is not None
        assert re.match(r"^-?\d+%$", s["stop_loss"])
        assert re.match(r"^-?\d+%$", s["take_profit"])
        assert len(s["data_used"]) > 0
    
    # Assert - Grounding
    assert result["metadata"]["guardrails_passed"] == True
    
    # Assert - Performance
    assert elapsed.seconds < 10
    
    # Assert - Memory
    session_id = result["metadata"]["trace_id"]
    assert memory.session_exists(session_id)
    
    print(f"✅ E2E Test Passed in {elapsed}s")
```

## Test Scenarios Matrix

| 場景 | 輸入 | 預期行為 |
|------|------|----------|
| Happy Path | 正常 ticker + period | 3 策略輸出 |
| Invalid Ticker | "INVALID" | 錯誤碼 + 說明 |
| Missing Period | ticker only | 使用預設 period (1y) |
| Extreme Risk | risk=aggressive | 策略參數調整 |
| Data Unavailable | ticker 無財報 | 降級輸出 + 警告 |
| Timeout | 模擬慢 API | 回傳部分結果 + 超時標記 |

## CI/CD Integration

```yaml
# .github/workflows/e2e.yml
name: E2E Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  e2e:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Environment
        run: |
          pip install -r requirements.txt
      - name: Run E2E Tests
        run: |
          pytest tests/e2e/ -v --timeout=60
      - name: Upload Test Results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: e2e-results
          path: tests/e2e/results/
```

## 對應蜀漢治理

- E2E 測試 = **北伐驗收**
- 測試通過 = 軍令完成 (`STATUS: 完成 (SUCCESS)`)
- 測試失敗 = 進入姜維諫言迴圈（省→再戰）
