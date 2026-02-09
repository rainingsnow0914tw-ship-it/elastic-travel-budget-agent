# Elastic Travel Budget Agent

Elastic Travel Budget Agent is a tool-driven AI agent built with **Elastic Agent Builder** and **Elasticsearch ES|QL queries** to produce deterministic, evidence-backed travel budget answers.

This project demonstrates a strict rule:

Query first → aggregate → calculate → then answer  
No query result → no numeric answer.

The agent is not allowed to guess numbers. All numeric outputs must come from Elasticsearch query results.

---

## 🎯 Problem

Many AI systems provide confident financial answers without verifying real data.  
In travel budgeting, this leads to incorrect estimates, failed payments, and real financial stress.

Confidence without data is a well-dressed lie.

---

## 💡 Solution

Elastic Travel Budget Agent enforces tool-first reasoning:

- Every numeric answer must come from ES|QL query results
- Aggregations are computed in Elasticsearch
- The LLM does not generate numbers
- If no records exist → the agent returns “no data available”

---

## ⚙️ Core Mechanism

The agent uses:

- Elastic Agent Builder
- platform.core.search tool
- ES|QL aggregation queries
- Deterministic calculation formulas

Example aggregation pipeline:

sum(amount_hkd)  
count_unique(date)  
daily_average = total / unique_days  
projected_budget = daily_average × planned_days

---

## 🧠 Tool Transparency

Agent Builder tool panel shows:

- tool calls
- ES|QL queries
- aggregation steps
- reasoning trace
- calculation inputs

---

## 🛡 Guardrail Rule

No query → No number  
No record → No estimate  
Only aggregation results may produce numeric answers

---

## 🏁 Principle

Data first. Then answer.

---

Final agent guardrail prompt is in /prompts_agent-system-prompt.txt
