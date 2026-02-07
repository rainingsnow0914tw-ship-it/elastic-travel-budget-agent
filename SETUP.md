# Setup Guide

This guide will help you recreate the Smart Travel Expense Analyzer in your own Elastic Cloud environment.

## 📋 Prerequisites

- Elastic Cloud account (free trial available)
- Elasticsearch 8.x or higher
- Kibana access
- Agent Builder feature enabled

## 🚀 Step-by-Step Setup

### Step 1: Create Elasticsearch Index

**1.1 Open Kibana Dev Tools**
- Navigate to: Management → Dev Tools

**1.2 Create the index with mapping**

Copy the mapping from `index-mapping.json` and execute:

```json
PUT /travel_database
{
  "mappings": {
    "properties": {
      "@timestamp": { "type": "date" },
      "date": { "type": "date" },
      "amount": { "type": "long" },
      "amount_hkd": { "type": "long" },
      "currency": { "type": "keyword" },
      "category": { "type": "keyword" },
      "location": { "type": "keyword" },
      "trip_id": { "type": "keyword" },
      "merchant": { "type": "text" },
      "notes": { "type": "keyword" }
    }
  }
}
```

**1.3 Verify index creation**
```json
GET /travel_database
```

### Step 2: Import Sample Data

**2.1 Prepare data file**
- Use the `mock-data.csv` file from this repository
- Or create your own data following the same structure

**2.2 Import via Kibana**

Option A: Using Kibana Upload
1. Go to: Management → Integrations → Upload a file
2. Select `mock-data.csv`
3. Map fields to match the index mapping
4. Set index name to `travel_database`
5. Click "Import"

Option B: Using Bulk API
Convert CSV to NDJSON format and use:
```json
POST /travel_database/_bulk
{ "index": {} }
{ "date": "2023-01-26", "location": "台中", "category": "住宿", "amount": 3200, "currency": "TWD", "amount_hkd": 800, "merchant": "台中日月千禧酒店", "notes": "入住首晚", "trip_id": "2023-01-taichung" }
...
```

**2.3 Verify data import**
```json
GET /travel_database/_count
```
Should return at least 10+ documents.

### Step 3: Create Agent in Agent Builder

**3.1 Navigate to Agent Builder**
- Go to: Kibana → Management → Agent Builder
- Click "Create agent"

**3.2 Basic Configuration**
- **Agent name**: Smart Travel Expense Analyzer
- **Description**: AI-powered travel budget analyzer using historical expense data
- **Model**: Select **OpenAI GPT-5.2** (or latest available)

**3.3 Configure Tools**

Enable the following tools:
- ✅ `platform.core.search` (Primary - Required)
- ✅ `platform.core.get_index_mapping` (Optional - for debugging)
- ✅ `platform.core.get_document_by_id` (Optional - for audit)
- ✅ `platform.core.list_indices` (Optional - for overview)

**3.4 Set Agent Instructions**

Copy the entire content from `agent-prompt.txt` and paste into the "Instructions" field.

**Key sections to verify**:
- ✅ Language mapping rules (Tokyo → 東京, etc.)
- ✅ Calculation formulas (sum/cardinality)
- ✅ Sanity check requirements
- ✅ Output format specifications

**3.5 Save and Deploy**
- Click "Save"
- Click "Deploy agent"
- Wait for deployment to complete (~30 seconds)

### Step 4: Test the Agent

**4.1 Basic Test**

Ask in the chat interface:
```
How much budget do I need for Osaka for 5 days?
```

**Expected response** (in Traditional Chinese):
```
總預算（5天）：HKD 3,685
每日平均：HKD 737/天

計算方式：
- 歷史數據：大阪總花費 HKD 10,320，14 天
- 每日平均：10,320 ÷ 14 = 737 HKD/天
- 5 天預算：737 × 5 = 3,685 HKD
- 驗證：737 × 14 ≈ 10,320 ✓
```

**4.2 Verify Calculations**

You can manually verify in Dev Tools:
```json
GET /travel_database/_search
{
  "size": 0,
  "query": {
    "term": { "location": "大阪" }
  },
  "aggs": {
    "total_hkd": { "sum": { "field": "amount_hkd" } },
    "unique_days": { "cardinality": { "field": "date" } }
  }
}
```

**4.3 Test Guardrail (No Data Scenario)**

Ask:
```
How much budget do I need for Kyoto for 5 days?
```

**Expected response**:
```
目前資料庫中沒有京都（Kyoto）的旅行記錄，
無法提供預算預測。
```

### Step 5: Additional Configuration (Optional)

**5.1 Customize Agent Behavior**

You can modify the agent prompt to:
- Change response language (English vs Chinese)
- Adjust rounding precision
- Add more greeting/personality
- Enable/disable category breakdown by default

**5.2 Add More Data**

To improve predictions, add more expense records:
```json
POST /travel_database/_doc
{
  "date": "2025-03-15",
  "location": "東京",
  "category": "住宿",
  "amount": 15000,
  "currency": "JPY",
  "amount_hkd": 900,
  "merchant": "池袋酒店",
  "notes": "商務出差",
  "trip_id": "2025-03-tokyo"
}
```

**5.3 Create Index Aliases (for production)**

```json
POST /_aliases
{
  "actions": [
    {
      "add": {
        "index": "travel_database",
        "alias": "travel_expenses"
      }
    }
  ]
}
```

## 🔧 Troubleshooting

### Issue 1: Agent Returns Wrong Numbers

**Symptom**: Budget predictions are 10-100x too large

**Cause**: Agent is using `amount` field instead of `amount_hkd`

**Fix**: 
1. Check agent prompt includes: "Only use amount_hkd for calculations"
2. Verify in query results that `amount_hkd` is being used
3. Update prompt to emphasize field selection

### Issue 2: Agent Can't Find Data for City

**Symptom**: Returns "no data" for cities that exist

**Cause**: Language mismatch (querying "Tokyo" instead of "東京")

**Fix**:
1. Verify agent prompt includes language mapping section
2. Check actual data in index uses Chinese values
3. Test query manually in Dev Tools with Chinese values

### Issue 3: Calculations Don't Match

**Symptom**: `daily_average × unique_days ≠ total_hkd`

**Cause**: Agent is using `avg()` instead of `sum() / cardinality()`

**Fix**:
1. Emphasize in prompt: "NEVER use avg(amount_hkd)"
2. Add sanity check requirement to prompt
3. Consider switching to GPT-5.2 if using older model

### Issue 4: Agent Provides Category Breakdown Without Being Asked

**Symptom**: Responses are too long/detailed

**Fix**:
1. Update prompt: "Do NOT provide category breakdown unless explicitly asked"
2. Add explicit instruction to keep responses concise

## 📊 Verification Checklist

Before considering setup complete, verify:

- ✅ Index exists with correct mapping
- ✅ Sample data imported successfully (at least 10 records)
- ✅ Agent deployed and accessible
- ✅ Agent uses GPT-5.2 (or latest model)
- ✅ Tools enabled: `platform.core.search` at minimum
- ✅ Agent prompt copied correctly (check for line breaks)
- ✅ Test query returns expected numbers
- ✅ Guardrail test (Kyoto) returns "no data"
- ✅ Calculations can be manually verified in Dev Tools

## 🎯 Next Steps

1. **Test with your own data**: Replace sample data with your actual travel expenses
2. **Customize categories**: Add or modify expense categories to match your needs
3. **Add more cities**: Import data from more destinations
4. **Integrate with apps**: Use Elastic APIs to connect with expense tracking apps
5. **Build visualizations**: Create Kibana dashboards for visual analysis

## 📚 Additional Resources

- [Elastic Agent Builder Guide](https://www.elastic.co/guide/en/kibana/current/agent-builder.html)
- [Elasticsearch Aggregations Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)
- [Kibana Dev Tools](https://www.elastic.co/guide/en/kibana/current/console-kibana.html)
- [Bulk API Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html)

## 💬 Support

If you encounter issues during setup:
1. Check the troubleshooting section above
2. Verify your Elastic Cloud version is 8.x or higher
3. Review agent logs in Kibana
4. Open an issue in this GitHub repository

---

**Estimated Setup Time**: 30-45 minutes (including data import)

**Difficulty Level**: Intermediate (requires basic Elasticsearch knowledge)
