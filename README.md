# 🦁 Elastic Travel Budget Agent (Powered by GPT-5.2)

> **"Confidence without data is a well-dressed lie."**


## 🚨 The Philosophy
This repository contains the source code for a **Deterministic Budget Agent** designed to fight AI hallucinations in financial planning. Unlike standard agents that "guess," this agent operates on a strict **"No Query, No Number"** protocol.

## 📂 The "Truth" Architecture (File Structure)

### 📜 `prompts_guardrail-rules.txt` (The Constitution)
This is the core safety layer. It enforces the **Ironclad Laws** of the agent:
- `Rule #3`: **No query → no number**
- `Rule #4`: **No aggregation → no calculation**
- `Rule #7`: **Numbers must come from ES|QL aggregation only**

### 🧠 `prompts_agent-system-prompt.txt` (The Identity)
Defines the agent's persona not as a "chat assistant," but as a **"deterministic budget agent."** It forces the model to prioritize `platform.core.search` over its own internal knowledge base.

### 📊 `mock-data.csv` (The Reality Test)
Contains high-variance, real-world spending scenarios (Cross-currency: TWD/JPY, Specific Merchants: *Ichiran Ramen*, *Times Car Rental*) to stress-test the agent's contextual aggregation logic.

### 🏗️ `docs_architecture.md` (The Blueprint)
Documents the **Unidirectional Truth Flow**:
`User` → `Acoustic Intent` → `ES|QL Query` → `Elasticsearch Aggregation` → `Deterministic Answer`

## 🚀 Getting Started
(妳原本的安裝教學...)

---

## 🏁 Principle

Data first. Then answer.
