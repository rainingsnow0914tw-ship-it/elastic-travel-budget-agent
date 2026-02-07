# Smart Travel Expense Analyzer (Elastic Agent Builder)

A deterministic, query-grounded travel budget agent built with **Elastic Agent Builder + Elasticsearch**.  
It answers budget questions using **only** verified query results from an Elasticsearch index (no guessing).

## 🎯 Problem

Travel expense records are messy and hard to turn into decisions:
- Users cannot reliably estimate how much money they need for an upcoming trip.
- Manual spreadsheets are slow, error-prone, and difficult to audit.
- "Chatty" assistants often hallucinate numbers without verifying data.

## 💡 Solution

This project builds a **multi-step AI agent** that:
1. Parses a user request (can be asked in English or Chinese)
2. Normalizes query values to match Chinese database values (e.g., Osaka → 大阪)
3. Calls Elasticsearch via Agent Builder tools
4. Computes budgets using **deterministic formulas** based on tool results
5. Performs a sanity check to prevent arithmetic / aggregation mistakes
6. Returns a reproducible, auditable answer (filters + fields + formula + numbers)

## ⭐ Key Features

### Core Feature: Budget Prediction
Ask: "How much budget do I need for Osaka for 5 days?"

The agent:
- Queries Elasticsearch for historical data (location = 大阪)
- Calculates: `daily_average = sum(amount_hkd) / cardinality(date)`
- Predicts: `budget_5_days = daily_average × 5`
- Shows complete calculation process with verification

### Additional Capabilities
- **Overview Analysis**: Calculate total spending across all trips
- **Category Breakdown**: Analyze spending by category (accommodation, dining, transportation, shopping, attractions)
- **City Comparison**: Compare daily average costs across different cities
- **Anti-Hallucination Guardrail**: Returns "no data" for cities without records (never estimates)

### Language Intelligence
- Accepts questions in **English or Traditional Chinese**
- Automatically maps English terms to Chinese database values
  - Tokyo → 東京, Osaka → 大阪, Fukuoka → 福岡, Taichung → 台中
  - Accommodation → 住宿, Dining → 餐飲, etc.

## 🔧 Key Elastic Features Used

- **Elastic Agent Builder** (reasoning model + tool-driven workflow)
- **Elasticsearch aggregations** via tool call (`platform.core.search`)
  - `sum(amount_hkd)` for totals
  - `cardinality(date)` for unique travel days
  - `terms` aggregation for category breakdown
- **Guardrails** to ensure **tool-first** behavior and prevent hallucinations

## 📊 Data Model

**Index**: `travel_database`

**Key fields used for calculations**:
- `amount_hkd` (long) — all money calculations use this field only
- `date` (date) — used for counting unique days via `cardinality`
- `location` (keyword) — Chinese values only (東京/大阪/福岡/台中)
- `category` (keyword) — Chinese values only (住宿/餐飲/交通/購物/景點)

**Sample data**: 80 real expense records from 14 trips (2023-2025)

See: `index-mapping.json` and `mock-data.csv`

## ✅ Correct Calculation Rules (Deterministic)

- **Total spending**: `total_hkd = sum(amount_hkd)`
- **Unique days**: `unique_days = cardinality(date)`
- **Daily average**: `daily_average = total_hkd / unique_days`
- **Predicted budget for N days**: `predicted_budget = daily_average × N`

**Forbidden** (common mistakes):
- ❌ `avg(amount_hkd)` (per-transaction average, not per-day)
- ❌ `sum(amount_hkd) / doc_count` (counts transactions, not days)
- ❌ Using `amount` field instead of `amount_hkd`

## 🎬 Example Demo Questions

1. **Budget Prediction** (English input supported):
   - "How much budget do I need for Osaka for 5 days?"
   - "去大阪 5 天需要準備多少預算？"

2. **City Comparison**:
   - "Which city is most expensive?"
   - "哪個城市最貴？"

3. **Guardrail Test** (no data scenario):
   - "How much budget do I need for Kyoto for 5 days?" → Returns "no data" ✅

4. **Category Analysis** (when explicitly asked):
   - "去大阪 5 天，各類別分別需要多少預算？"

## 📈 Measurable Impact

- ✅ Replaces manual spreadsheet steps with a single query-driven answer
- ✅ Reduces errors from incorrect averaging methods (avg per transaction vs per day)
- ✅ Produces answers that are verifiable and reproducible from Elasticsearch
- ✅ Prevents budget overestimation through deterministic calculations
- ✅ Saves ~15-20 minutes per trip planning session

## 📁 Repository Contents

- `README.md` — This file
- `ARCHITECTURE.md` — System overview & flow diagram
- `SETUP.md` — Step-by-step deployment guide for Elastic Cloud
- `demo-script.md` — <3 minute demo plan
- `agent-prompt.txt` — Final agent instruction prompt (strict mode)
- `sample-queries.json` — Example Elasticsearch queries used by the agent
- `index-mapping.json` — Index mapping snapshot
- `mock-data.csv` — Example dataset used for testing/demo
- `LICENSE` — MIT License
- `.gitignore` — Git ignore rules

## 🚀 Quick Start

See [SETUP.md](SETUP.md) for detailed deployment instructions.

**TL;DR**:
1. Create Elasticsearch index with mapping from `index-mapping.json`
2. Import sample data from `mock-data.csv`
3. Create Agent in Elastic Agent Builder
4. Copy `agent-prompt.txt` to Agent instructions
5. Enable `platform.core.search` tool
6. Select GPT-5.2 as reasoning model
7. Test with: "How much budget do I need for Osaka for 5 days?"

## 🎥 Demo Video

[Link to be added - YouTube video < 3 minutes]

## 🏆 Hackathon Context

Built for: **Elasticsearch Agent Builder Hackathon 2025**  
Category: Travel & Lifestyle  
Developer: Chloe  
Date: February 2025

## 🔗 Technical Details

- **Platform**: Elasticsearch Cloud (8.x+)
- **Agent Framework**: Elastic Agent Builder
- **AI Model**: OpenAI GPT-5.2
- **Language**: Traditional Chinese (繁體中文) + English input support
- **Query Language**: Elasticsearch DSL (aggregations)

## 📄 License

This project is open-sourced under the **MIT License** (see [LICENSE](LICENSE)).

---

⭐ If this project helps you, please give it a star!

💬 For questions or feedback, please open an issue.
