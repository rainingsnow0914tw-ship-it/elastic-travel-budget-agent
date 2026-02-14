# 🦁 Elastic Travel Budget Agent (Powered by GPT-5.2)

> **"Confidence without data is a well-dressed lie."** 

🚀 Join the Movementhttps://www.elastic.co/
https://elasticstack.slack.com/signup#/domain-signup

---

1. Acoustic Intent Extraction (Voice-First Interface)

Recognizing that anxiety stems from complex interfaces, we implemented a seamless voice-driven command layer.

Input: Users verbally "dump" chaotic travel plans (e.g., "I want to go to Tokyo for 5 days, can I afford wagyu?").

Process: The system captures the Acoustic Intent and transmutes it into structured ES|QL queries.

Result: A friction-less, anxiety-free financial planning experience.

---

2. The Logic Pipeline

The agent follows a strict, unidirectional flow of truth: Query first → Aggregate → Calculate → Answer.

Elastic Agent Builder: Orchestrates the reasoning.

platform.core.search: The only authorized source of truth.

ES|QL Aggregations: Performs the heavy lifting (SUM, COUNT_UNIQUE, AVG) in the database layer, not the LLM layer.

---

📜 The "Constitution" (Guardrail Rules)

We implemented a Draconian Safety Layer directly into the system prompt ([prompts_guardrail-rules.txt](https://github.com/rainingsnow0914tw-ship-it/elastic-travel-budget-agent/blob/main/prompts_guardrail-rules.txt)). This isn't just code; it's Law.

Rule #1: No Query → No Number.

Rule #2: No Record → No Estimate.

Rule #3: Only aggregation results may produce numeric answers.

If the query returns null, the agent is chemically lobotomized from guessing. It must admit ignorance.

---

📊 Real-World Reality Testing
We didn't train on clean academic data. We engineered [mock-data.csv](https://github.com/rainingsnow0914tw-ship-it/elastic-travel-budget-agent/blob/main/mock-data.csv) with high-variance real-world scenarios to prove the agent's robustness:

Cross-Currency Normalization: Handling mixed portfolios (TWD vs. JPY).

Contextual Merchant Recognition: Distinguishing between dining (Ichiran Ramen) and transport (Times Car Rental).

Deterministic Calculation Formulas:

---

🧠 Transparency & Traceability

Trust is our primary currency. The Agent Builder tool panel provides full visibility into the Reasoning Trace:

✅ Visible Tool Calls

✅ Raw ES|QL Queries

✅ Aggregation Steps

✅ Calculation Inputs

---

🚀 Future Roadmap: Democratizing Sovereignty

Global Expansion: Scaling the Acoustic Intent Engine for multi-lingual support.

The "Anxiety Shield": Anomaly detection for pre-emptive spending alerts.

Accessibility First: Empowering users with motor impairments via our Voice-First architecture.

---

🏁 Principle
Data first. Then answer.
