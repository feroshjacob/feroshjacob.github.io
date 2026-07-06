---
post_title: "Self-Learning Agent-Based Retail Search, Part 2: The Baseline Before the Agents"
post_url: "https://feroshjacob.github.io/posts/2026/07/06/self-learning-agent-based-retail-search-part-2-the-baseline-before-the-agents"
post_slug: "2026-07-06-self-learning-agent-based-retail-search-part-2-the-baseline-before-the-agents"
linkedin_status: ready
visibility: PUBLIC
post_image: "/images/self-learning-agent-based-retail-search-part-2-cranfield-baseline.png"
hashtags:
  - AI
  - RetailSearch
  - SearchRelevance
  - OpenSearch
  - AIAgents
  - MissionDrivenEngineering
---

The first useful result of self-learning search engineering is not a clever model. It is a measurable baseline.

In Part 2 of my Self-Learning Agent-Based Retail Search series, I walk through Phase 1 of the project: Cranfield documents and queries, an out-of-box OpenSearch BM25 baseline, live relevance metrics, and the failure cases agents will need to improve.

The current live baseline over 225 Cranfield queries is MAP 0.2402, nDCG@10 0.2995, Precision@10 0.2316, Recall@10 0.3994, and MRR 0.5350.

The important part is not that those numbers are high. They are not. The important part is that the failures are now visible, clickable, and measurable. That gives agents a real loop: inspect broken queries, propose OpenSearch-native changes, rerun the same evaluation gate, and keep only what improves the evidence.

Permanent link:
https://feroshjacob.github.io/posts/2026/07/06/self-learning-agent-based-retail-search-part-2-the-baseline-before-the-agents

#AI #RetailSearch #SearchRelevance #OpenSearch #AIAgents #MissionDrivenEngineering
