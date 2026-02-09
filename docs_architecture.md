# Architecture — Elastic Travel Budget Agent

Tool-driven deterministic agent using Elastic Agent Builder and ES|QL aggregations.

Execution flow:

User → Agent → platform.core.search → ES|QL → Elasticsearch → deterministic calculation → answer

All numeric computation happens in Elasticsearch, not in the LLM.

Reasoning trace and tool calls are visible in Agent Builder tool panel.
