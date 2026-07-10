---
post_title: "SLAB-RS, Part 3: The Agent Climbs Keyword Search to Its Ceiling"
post_url: "https://feroshjacob.github.io/posts/2026/07/10/self-learning-agent-based-retail-search-part-3-failure-driven-bm25-and-the-lexical-ceiling"
post_slug: "2026-07-10-self-learning-agent-based-retail-search-part-3-failure-driven-bm25-and-the-lexical-ceiling"
linkedin_status: "ready"
visibility: PUBLIC
post_image: "/images/self-learning-agent-based-retail-search-part-3-keyword-ceiling.png"
hashtags:
  - AI
  - RetailSearch
  - SearchRelevance
  - OpenSearch
  - AIAgents
  - MissionDrivenEngineering
---

In my experience, this would have been a quarter's roadmap for a core search team of 10-15 people. An AI agent did it in six days — and the most interesting win came from a technique almost nobody ships in production.

Part 3 of my Self-Learning Agent-Based Retail Search (SLAB-RS) series covers the keyword round: eight experiments on a live OpenSearch baseline, each one picked by failure evidence rather than hunches. Two experiments were rejected and kept as public evidence. My only intervention in the whole article was pointing the agent at a published paper to compare against.

The surprise winner was pseudo-relevance feedback — a decades-old IR textbook idea that usually dies in production reviews over latency. The agent's version reranks inside the already-retrieved pool, adds no second search round trip, and delivered the biggest single jump of the phase: nDCG@10 from 0.2995 to 0.3253.

Then the improvements ran out. The last refinement was worth two tenths of a percent, and 27 of 225 queries still find nothing relevant — because they use words their answer documents never use. That is the lexical ceiling, and it is measurable. Part 4 is about breaking it.

Permanent link:
https://feroshjacob.github.io/posts/2026/07/10/self-learning-agent-based-retail-search-part-3-failure-driven-bm25-and-the-lexical-ceiling

#AI #RetailSearch #SearchRelevance #OpenSearch #AIAgents #MissionDrivenEngineering
