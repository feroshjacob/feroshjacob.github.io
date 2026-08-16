---
post_title: "SLAB-RS, Part 8: The Word the Embeddings Couldn't Read"
post_url: "https://feroshjacob.github.io/posts/2026/08/16/self-learning-agent-based-retail-search-part-8-the-word-the-embeddings-couldnt-read"
post_slug: "2026-08-16-self-learning-agent-based-retail-search-part-8-the-word-the-embeddings-couldnt-read"
linkedin_status: published
post_type: image
visibility: PUBLIC
post_image: "/images/self-learning-agent-based-retail-search-part-8-without-rule.png"
linkedin_post_urn: "urn:li:share:7494615884854968320"
linkedin_image_urn: "urn:li:image:D4E10AQFg8cb_abCyow"
linkedin_published_at: "2026-08-16T04:48:19Z"
hashtags:
  - AI
  - RetailSearch
  - QueryUnderstanding
  - InformationRetrieval
---

Phase 3 of my self-learning retail search project measured the whole classical toolkit — and shipped almost none of it. One thing did ship, and it wasn't a bigger model.

A million-vector embedding model — the technique that beat every benchmark in this project — cannot read the word "without." Search "refrigerator without freezer" and it cheerfully returns fridges with freezers. Scale never fixed it.

What did: reading a thousand failures, finding that negation was the one residual nothing else could touch, and writing a single rule — demote a product only when it positively mentions the excluded term, never when it advertises the absence. +4.8 nDCG on negation queries, shipped live.

The last word in Phase 3 went not to the biggest model in the stack, but to a careful reading of where it broke.

https://feroshjacob.github.io/posts/2026/08/16/self-learning-agent-based-retail-search-part-8-the-word-the-embeddings-couldnt-read

#AI #RetailSearch #QueryUnderstanding #InformationRetrieval
