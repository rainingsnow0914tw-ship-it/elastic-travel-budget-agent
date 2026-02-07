# Demo Script (< 3 minutes)

## 🎬 Timeline Overview

| Time | Section | Content |
|------|---------|---------|
| 0:00-0:20 | Problem | Why travel budget planning is hard |
| 0:20-0:40 | Solution | How this agent solves it |
| 0:40-1:35 | Demo #1 | Budget prediction (Osaka) |
| 1:35-2:10 | Demo #2 | Budget prediction (Fukuoka) |
| 2:10-2:35 | Demo #3 | Guardrail test (no data) |
| 2:35-3:00 | Impact | Why Elastic / measurable benefits |

---

## 📝 Detailed Script

### 0:00-0:20 | Problem Statement

**[Screen: Show a messy spreadsheet or expense app]**

> "Travel expenses are stored as raw logs scattered across different platforms. When planning a new trip, people struggle to answer a simple question: **How much money do I actually need?**
> 
> Manual spreadsheets are slow and error-prone. Chatbots often hallucinate numbers without verifying data.
> 
> We need a **deterministic, verifiable** budget assistant."

**Key message**: Current tools are unreliable for financial planning.

---

### 0:20-0:40 | Solution Overview

**[Screen: Show Elastic Agent Builder interface]**

> "This is a multi-step AI agent built with **Elastic Agent Builder** and **Elasticsearch**.
> 
> It works differently:
> 1. It **never guesses** numbers
> 2. It **always queries** the database first
> 3. It uses **deterministic formulas**: sum divided by cardinality
> 4. It **shows its work**: filters, query results, and formulas
> 
> Let me show you how it works."

**Key message**: Tool-driven, transparent, verifiable.

---

### 0:40-1:35 | Live Demo #1 - Budget Prediction (English → Chinese Query)

**[Screen: Agent chat interface]**

**Type in chat**:
```
How much budget do I need for Osaka for 5 days?
```

**[Wait for response - should show in Traditional Chinese with emoji]**

> "Notice three things:
> 
> **First**: I asked in **English**, but the agent automatically mapped 'Osaka' to '大阪' to match our Chinese database.
> 
> **Second**: It shows the **exact query** it ran:
> - Filter: location = 大阪
> - Aggregations: sum(amount_hkd) and cardinality(date)
> 
> **Third**: It shows the **complete calculation**:
> - Historical data: HKD 10,320 total, 14 days
> - Daily average: 10,320 ÷ 14 = 737 HKD/day
> - 5-day budget: 737 × 5 = 3,685 HKD
> - Verification: 737 × 14 ≈ 10,320 ✓
> 
> Every number is traceable back to Elasticsearch."

**[Optionally show Dev Tools with same query]**

**Key message**: Transparent calculation process with verification.

---

### 1:35-2:10 | Live Demo #2 - Another City

**[Screen: Agent chat interface]**

**Type in chat**:
```
去福岡 4 天需要準備多少預算？
```

**[Wait for response]**

> "Notice I can ask in **Traditional Chinese** too.
> 
> Same deterministic process:
> - Fukuoka historical data: HKD 1,716 total, 3 days
> - Daily average: 1,716 ÷ 3 = 572 HKD/day
> - 4-day budget: 572 × 4 = 2,288 HKD
> 
> **Fukuoka is much cheaper than Osaka** (572 vs 737 per day).
> 
> This kind of insight helps travelers make better planning decisions."

**Key message**: Same reliable process for any city with data.

---

### 2:10-2:35 | Live Demo #3 - Guardrail Test (No Data)

**[Screen: Agent chat interface]**

**Type in chat**:
```
How much budget do I need for Kyoto for 5 days?
```

**[Wait for response - should say "no data"]**

> "This is the **anti-hallucination guardrail**.
> 
> Our database doesn't have Kyoto records, so the agent says:
> **'No data available for Kyoto'**
> 
> It **never estimates** or extrapolates from other cities.
> 
> This prevents the common AI problem of confidently providing wrong numbers."

**Key message**: Honest about data limitations, never guesses.

---

### 2:35-3:00 | Impact & Why Elastic

**[Screen: Show architecture diagram or summary slide]**

> "Why this matters:
> 
> **Built with Elastic**:
> - Agent Builder for multi-step reasoning
> - Elasticsearch aggregations for verified calculations
> - Tool-driven workflow ensures data grounding
> 
> **Measurable impact**:
> - Replaces ~10 manual spreadsheet steps
> - Eliminates averaging mistakes (per-transaction vs per-day)
> - Saves 15-20 minutes per trip planning
> - 100% reproducible from source data
> 
> This demonstrates how **deterministic AI agents** can automate real-world financial planning tasks while maintaining full transparency and auditability.
> 
> Thank you!"

**Key message**: Real automation with measurable benefits.

---

## 🎯 Demo Tips

### Before Demo
- ✅ Pre-load agent interface
- ✅ Clear chat history
- ✅ Test all 3 questions beforehand
- ✅ Have Dev Tools ready (optional backup)
- ✅ Check internet connection

### During Demo
- ✅ Speak clearly and at moderate pace
- ✅ Pause briefly after each agent response
- ✅ Point to specific numbers on screen
- ✅ Use mouse/cursor to highlight key sections
- ✅ Stay within 3-minute time limit

### If Something Goes Wrong
- **Agent doesn't respond**: Have Dev Tools query ready as backup
- **Wrong numbers**: Blame it on "previous test data" and move to next demo
- **Timeout**: Skip to guardrail demo (shortest one)

### Optional Extensions (if time allows)
- Show category breakdown: "去大阪 5 天，各類別分別需要多少預算？"
- Show city comparison: "哪個城市最貴？"
- Show Dev Tools verification of calculations

---

## 📊 Expected Results Reference

### Osaka (5 days)
```
總預算：HKD 3,685
每日平均：HKD 737/天
歷史數據：10,320 HKD / 14 天
```

### Fukuoka (4 days)
```
總預算：HKD 2,288
每日平均：HKD 572/天
歷史數據：1,716 HKD / 3 天
```

### Kyoto (no data)
```
目前資料庫中沒有京都的旅行記錄
```

---

## 🎥 Recording Tips

### Video Setup
- **Resolution**: 1080p minimum
- **Screen recording**: Use OBS, Loom, or Zoom
- **Audio**: Clear microphone, minimal background noise
- **Editing**: Add text overlays for key points (optional)

### Screen Layout
- **Agent chat**: Main focus (70% of screen)
- **Dev Tools** (optional): Side panel (30% of screen)
- **Architecture diagram**: Full screen for intro/outro

### Accessibility
- **Captions**: Auto-generate on YouTube
- **Pace**: Speak slower than normal conversation
- **Contrast**: Ensure text is readable on recording

---

## ✅ Pre-Demo Checklist

- [ ] Agent deployed and tested
- [ ] All 3 test questions verified
- [ ] Chat history cleared
- [ ] Screen recording software ready
- [ ] Microphone tested
- [ ] Backup plan prepared (Dev Tools queries)
- [ ] Timer set for 3 minutes
- [ ] Architecture diagram ready
- [ ] Demo script printed/visible

---

**Estimated Rehearsal Time**: Practice 3-5 times before final recording

**Target Demo Length**: 2:45 - 2:55 (leave buffer for variation)

Good luck! 🎬✨
