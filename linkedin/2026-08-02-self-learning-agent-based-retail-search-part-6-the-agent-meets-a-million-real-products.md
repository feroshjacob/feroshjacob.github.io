---
post_title: "SLAB-RS, Part 6: The Agent Meets a Million Real Products"
post_url: "https://feroshjacob.github.io/posts/2026/08/02/self-learning-agent-based-retail-search-part-6-the-agent-meets-a-million-real-products"
post_slug: "2026-08-02-self-learning-agent-based-retail-search-part-6-the-agent-meets-a-million-real-products"
linkedin_status: published
post_type: video
visibility: PUBLIC
post_image: "/images/self-learning-agent-based-retail-search-part-6-scale-jump.gif"
linkedin_post_urn: "urn:li:ugcPost:7489855808873353216"
linkedin_video_urn: "urn:li:video:D5610AQH47nZ7Kg2gQQ"
linkedin_published_at: "2026-08-03T21:26:56Z"
hashtags:
  - AI
  - RetailSearch
  - ProductSearch
  - VectorSearch
  - HybridSearch
  - InformationRetrieval
---

For five parts, a series called Self-Learning Agent-Based Retail Search had no retail in it — 1,400 aeronautics abstracts, then fifteen academic benchmarks. Rehearsals. Phase 3 is the real thing: 1.2 million actual Amazon products, with human Exact/Substitute/Complement/Irrelevant labels, where a substitute is sometimes the best answer.

One question at the door: does the academic stack survive contact with real retail? It does. The BM25 + BGE hybrid the agent discovered on aeronautics abstracts lands at nDCG@10 0.847 on a million products — within 0.4% of the published single-model baseline. Parity.

The constraints are the whole story. Everything runs on one free-tier box — 4 GB RAM, 20 GB storage — and every query must return in under 700 ms. int8 quantization turned out to be lossless, which fit a live million-vector index on that box and broke a ceiling that had held since Phase 2. What the system does not chase is the KDD leaderboard winner: that is a query-time ensemble of cross-encoders, a different latency class no shopper would tolerate. Parity at production latency is the honest claim.

Same method, 869x the data. Two phases of academic rehearsal, and none of it was wasted.

The full write-up, with numbers, tables, and the honest limits:
https://feroshjacob.github.io/posts/2026/08/02/self-learning-agent-based-retail-search-part-6-the-agent-meets-a-million-real-products

#AI #RetailSearch #ProductSearch #VectorSearch #HybridSearch #InformationRetrieval
