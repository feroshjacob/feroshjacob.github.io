---
post_title: "SLAB-RS, Part 4: The Agent Breaks Its Ceiling with Embeddings"
post_url: "https://feroshjacob.github.io/posts/2026/07/10/self-learning-agent-based-retail-search-part-4-when-embeddings-finally-earned-their-place"
post_slug: "2026-07-10-self-learning-agent-based-retail-search-part-4-when-embeddings-finally-earned-their-place"
linkedin_status: ready
visibility: PUBLIC
post_image: "/images/self-learning-agent-based-retail-search-part-4-embeddings-break-ceiling.png"
hashtags:
  - AI
  - RetailSearch
  - VectorSearch
  - Embeddings
  - OpenSearch
  - AIAgents
---

An 8-billion-parameter language model's embeddings scored five times worse than random vectors. That failure is my favorite result in Part 4 of the SLAB-RS series — because it is what makes the eventual win believable.

Part 3 ended at a measured keyword ceiling: nDCG@10 0.3260, with 27 of 225 queries finding nothing because they use words their answer documents never use. Part 4 is the story of breaking that ceiling with embeddings — which took the agent three attempts and one instructive detour.

The attempts, in order: fake vectors as a control group (passed, exactly as fake vectors should), embeddings from a chat model (failed spectacularly — "semantic search" is not one thing, and training objective beats model size), and finally BGE, a compact model trained specifically for retrieval. BGE beat the ceiling by 4.9% alone, 8.4% combined with keywords, and 10.5% once its signals fed a learned ranker. Then the whole thing was validated live on a real OpenSearch vector index — offline and production numbers agreed to four decimal places.

My favorite production decision: the public BGE endpoint returns an honest "501 — runtime not enabled" with evidence, instead of a faked demo. And nothing gets promoted to the public default until it proves it generalizes on a second benchmark. Phase 1 closes here; Phase 2 is BEIR and transferability.

#AI #RetailSearch #VectorSearch #Embeddings #OpenSearch #AIAgents

Permanent link:
https://feroshjacob.github.io/posts/2026/07/10/self-learning-agent-based-retail-search-part-4-when-embeddings-finally-earned-their-place
