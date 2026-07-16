---
post_title: "SLAB-RS, Interlude: Why the Agent Is Not in a Store Yet"
post_url: "https://feroshjacob.github.io/posts/2026/07/14/self-learning-agent-based-retail-search-interlude-why-the-agent-is-not-in-a-store-yet"
post_slug: "2026-07-14-self-learning-agent-based-retail-search-interlude-why-the-agent-is-not-in-a-store-yet"
linkedin_status: "published"
visibility: PUBLIC
post_image: "/images/self-learning-agent-based-retail-search-interlude-road-to-retail.png"
hashtags:
  - AI
  - RetailSearch
  - InformationRetrieval
  - SearchRelevance
  - OpenSearch
  - AIAgents
linkedin_post_urn: "urn:li:share:7483374444674146305"
linkedin_published_at: "2026-07-16T04:18:22.359739+00:00"
linkedin_thumbnail_urn: "urn:li:image:D4D10AQGZnKbuP_hqRA"
---

Four articles into a series called Self-Learning Agent-Based Retail Search, there is not a single product. No store. Just 1,400 aeronautics abstracts from the 1960s and an agent that got slightly better at ranking them. That complaint is fair, and this interlude answers it.

Start with the number that reorganized my thinking. BM25 — the plainest keyword ranking function in search, no learning, no tuning, the same arithmetic everywhere it runs — scores 0.158 on one BEIR dataset and 0.789 on another. Same algorithm, same metric, five-fold spread, driven entirely by which corpus it happens to be reading. If the most universal technique in the field swings that far on domain alone, then a relevance score is not a grade. It is a reading taken at a location.

Which means I got Phase 2 wrong when I wrote Part 4. I promised BEIR would be a filter: techniques that prove they generalize keep their place, the rest get dropped. But "does it generalize?" collapses a map into a single bit — and it would discard the evidence that matters most. A technique that helps only two of fifteen academic datasets might be helping precisely the two that look most like retail. So nothing gets discarded. Every technique is classified — Universal, Domain-conditional, or Dormant — and travels to Phase 3 carrying its evidence. Superset, not filter.

My favorite part costs nothing to learn: ArguAna's published BM25 baseline is either 0.315 or 0.472 depending on which paper you read, and both are correct. 1,298 of its 1,406 test queries also exist as documents in its own corpus, so one source counts a query retrieving itself and the other does not. The published number everyone cites is a convention, and you only discover which convention by rebuilding it yourself.

#AI #RetailSearch #InformationRetrieval #SearchRelevance #OpenSearch #AIAgents

Permanent link:
https://feroshjacob.github.io/posts/2026/07/14/self-learning-agent-based-retail-search-interlude-why-the-agent-is-not-in-a-store-yet
