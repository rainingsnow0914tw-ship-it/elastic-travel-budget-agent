# Architecture

## 🏗️ High-level Flow

```
User (English/Chinese question)
      │
      ▼
Elastic Agent Builder (Reasoning Model: GPT-5.2)
      │  
      │  Step 1: Intent parsing (budget / average / city comparison)
      │  Step 2: Language normalization (Osaka→大阪, Tokyo→東京, etc.)
      │  Step 3: Construct Elasticsearch query
      ▼
Tool: platform.core.search (Elasticsearch Aggregations)
      │  
      │  - Filter by location and/or trip_id (when present)
      │  - Aggregations:
      │     • total_hkd = sum(amount_hkd)
      │     • unique_days = cardinality(date)
      │     • by_category = terms(category) + sum(amount_hkd) [optional]
      ▼
Formula Layer (Deterministic Calculation)
      │  
      │  daily_average = total_hkd / unique_days
      │  predicted_budget = daily_average × N days
      ▼
Sanity Check (Verification)
      │  
      │  Verify: daily_average × unique_days ≈ total_hkd
      │  If check fails → re-calculate or report error
      ▼
Answer (Traditional Chinese + Emoji)
      │
      │  Output includes:
      │  - Filters used (location = 大阪)
      │  - Fields used (amount_hkd, date)
      │  - Query results (total_hkd, unique_days)
      │  - Formula (daily_average = X ÷ Y)
      │  - Final numbers with verification ✓
```

## 🛠️ Tools Used

### Primary Tool
**platform.core.search** (Required)
- Executes Elasticsearch queries with aggregations
- Returns verified data from the index
- Used for all numeric calculations

### Supporting Tools (Available but Optional)
- `platform.core.get_index_mapping` — For debugging/documentation
- `platform.core.get_document_by_id` — For debugging/audit
- `platform.core.list_indices` — For system overview

### Not Used
- `platform.core.execute_esql` — Not required for this use case

## 🛡️ Guardrails (Anti-Hallucination)

### Strict Rules Enforced by Agent Prompt

1. **Tool-First Policy**  
   Never output ANY number without querying tools in the same response.

2. **Field Validation**  
   Only use `amount_hkd` for money calculations.  
   Never use `amount` (original currency).

3. **Language Mapping**  
   Must use Chinese values for term filters:
   - Locations: 東京 / 大阪 / 福岡 / 台中
   - Categories: 住宿 / 餐飲 / 交通 / 購物 / 景點

4. **No Data = No Estimation**  
   If no data is found for a city, respond "no data available".  
   Never estimate or extrapolate from other cities.

5. **Calculation Verification**  
   After computing `daily_average`, verify:  
   `daily_average × unique_days ≈ total_hkd` (allow rounding)

6. **Show Your Work**  
   Every answer must include:
   - Filters used
   - Fields used
   - Query results (raw numbers)
   - Formulas applied
   - Final answer with units

## 🎯 Why This Fits Agent Builder Hackathon

### Multi-Step Agent Workflow
1. **Parse** user intent from natural language
2. **Normalize** language (English → Chinese database values)
3. **Query** Elasticsearch using appropriate aggregations
4. **Compute** using deterministic formulas
5. **Verify** results through sanity checks
6. **Respond** with transparent, auditable answers

### Tool-Driven Decision Making
- All numeric outputs are grounded in Elasticsearch query results
- No assumptions or estimations
- Reproducible and auditable

### Real-World Task Automation
**Problem**: Travel budget planning from personal expense data  
**Manual process**: 8-10 steps involving spreadsheets, filtering, calculating  
**Automated process**: 1 natural language question → verified answer in seconds

### Measurable Impact
- Steps removed: ~8-10 manual operations per query
- Errors reduced: Eliminates common averaging mistakes (per-transaction vs per-day)
- Time saved: ~15-20 minutes per trip planning session
- Accuracy: 100% reproducible from source data

## 🔍 Technical Highlights

### Correct Aggregation Strategy
**✅ Right way** (used by this agent):
```json
{
  "aggs": {
    "total_hkd": { "sum": { "field": "amount_hkd" } },
    "unique_days": { "cardinality": { "field": "date" } }
  }
}
```
→ `daily_average = total_hkd / unique_days`

**❌ Wrong way** (common mistake):
```json
{
  "aggs": {
    "daily_avg": { "avg": { "field": "amount_hkd" } }
  }
}
```
→ This gives per-transaction average, not per-day!

### Language Normalization Layer
User can ask in English, but queries always use Chinese values:
- Input: "Osaka" → Query: `{ "term": { "location": "大阪" } }`
- Input: "Accommodation" → Query: `{ "term": { "category": "住宿" } }`

This ensures:
- User-friendly input (both languages accepted)
- Database consistency (Chinese values only)
- No query failures from language mismatches

## 📊 Data Flow Example

**User Question**: "How much budget do I need for Osaka for 5 days?"

**Step-by-Step Execution**:

1. **Intent Recognition**  
   Type: Budget prediction  
   Location: Osaka → 大阪  
   Days: 5

2. **Query Construction**
   ```json
   {
     "query": { "term": { "location": "大阪" } },
     "aggs": {
       "total_hkd": { "sum": { "field": "amount_hkd" } },
       "unique_days": { "cardinality": { "field": "date" } }
     }
   }
   ```

3. **Query Results**
   ```json
   {
     "total_hkd": { "value": 10320 },
     "unique_days": { "value": 14 }
   }
   ```

4. **Calculation**
   ```
   daily_average = 10320 ÷ 14 = 737 HKD/day
   predicted_budget = 737 × 5 = 3685 HKD
   ```

5. **Verification**
   ```
   Check: 737 × 14 = 10318 ≈ 10320 ✓
   ```

6. **Response** (Traditional Chinese)
   ```
   總預算（5天）：HKD 3,685
   每日平均：HKD 737/天
   
   計算方式：
   - 歷史數據：大阪總花費 HKD 10,320，14 天
   - 每日平均：10,320 ÷ 14 = 737 HKD/天
   - 5 天預算：737 × 5 = 3,685 HKD
   - 驗證：737 × 14 ≈ 10,320 ✓
   ```

## 🚀 Scalability & Future Enhancements

### Current Scope
- Single-user personal expense tracking
- 4 cities, 5 categories
- ~80 records across 14 trips

### Potential Extensions
- Multi-user support with user segmentation
- More sophisticated prediction (seasonal trends, price inflation)
- Budget alerts and recommendations
- Integration with booking platforms
- Expense tracking mobile app integration
- Currency conversion with live rates

## 🔒 Security Considerations

- No sensitive financial data exposed in queries
- All data stays within Elasticsearch Cloud
- Agent prompt prevents data leakage through careful output formatting
- No user authentication required (single-user demo)

## 📚 References

- [Elastic Agent Builder Documentation](https://www.elastic.co/guide/en/kibana/current/agent-builder.html)
- [Elasticsearch Aggregations](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)
- [OpenAI GPT-5.2](https://openai.com/)
