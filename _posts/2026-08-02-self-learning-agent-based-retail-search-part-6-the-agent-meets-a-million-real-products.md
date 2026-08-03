---
title: "SLAB-RS, Part 6: The Agent Meets a Million Real Products"
date: 2026-08-02
permalink: /posts/2026/08/02/self-learning-agent-based-retail-search-part-6-the-agent-meets-a-million-real-products
categories:
  - self-learning-agent-based-retail-search
series: self-learning-agent-based-retail-search
series_order: 7
phase: phase-3
tags:
  - retail search
  - amazon esci
  - product search
  - vector search
  - hybrid search
  - quantization
  - opensearch
  - information retrieval
  - ai agents
  - mission-driven engineering
image: /images/self-learning-agent-based-retail-search-part-6-scale-jump.gif
excerpt: "Five parts of a retail search series, and the documents were never products. Phase 3 changes that: 1.2 million real Amazon products, graded Exact/Substitute/Complement/Irrelevant labels, and one question — does the academic stack survive contact with real retail? It does. The BGE hybrid lands at parity with the published single-model baseline, at production latency, and int8 quantization makes a live million-vector index fit on a free-tier box, losslessly."
---

![The scale jump the agent survived: an animated counter racing from 1,400 aeronautics abstracts in Phase 1 to 1,215,854 Amazon products in Phase 3 — the corpus grid filling and shifting from blue to teal — while a "BM25 + BGE hybrid — still works" badge stays lit the whole way](/images/self-learning-agent-based-retail-search-part-6-scale-jump.gif)

For five parts, a series called *Self-Learning Agent-Based Retail Search* has contained no retail. [Phase 1](/posts/2026/07/10/self-learning-agent-based-retail-search-part-4-when-embeddings-finally-earned-their-place) proved the method on 1,400 aeronautics abstracts. [Phase 2](/posts/2026/07/26/self-learning-agent-based-retail-search-part-5-how-the-agents-discovered-hybrid-search) took the winning techniques across fifteen academic benchmarks to find out which ones travel. Both were rehearsals. Neither had a product in it.

Phase 3 does. The agent is now searching **1.2 million real Amazon products** from the [ESCI Shopping Queries Dataset](https://github.com/amazon-science/esci-data)[^esci] — real titles, brands, colors, attributes, and real human relevance judgments. This is the test the whole project was built to reach, and it opens with the only question that matters at the door: **does the academic stack survive contact with real retail?**

## The Short Version

It survives. On 1.2 million ESCI products, the BM25 + BGE hybrid the agent carried out of Phase 2 lands at **nDCG@10 0.847, within 0.4% of the published single-model baseline of 0.850** — essentially parity — at production latency, with no cross-encoder running at query time.[^ndcg]

Two things made that possible and honest. First, **int8 quantization is lossless here** (0.8473 vs 0.8472 at full precision), which shrinks the vectors 4× and lets a live million-vector index fit on the free-tier box — 4 GB of RAM, 20 GB of storage. Second, the claim is deliberately modest: parity with the *single-model baseline*, not the [KDD Cup 2022](https://amazonkddcup.github.io/) leaderboard winner at 0.904 — that number is a query-time ensemble of large cross-encoders, a different latency class entirely, and never the target under a 700 ms budget.

You can search the corpus yourself: **[retail-search.feroshjacob.workers.dev/phases/esci/search](https://retail-search.feroshjacob.workers.dev/phases/esci/search?q=laptop)**.

## Two Rules, Set at the Start

Two constraints were fixed before Phase 3 began, and they are what make the word *parity* mean anything here.

**One box.** Everything runs on a single free-tier OpenSearch instance — **4 GB of RAM, 20 GB of storage, one node.** No cluster, no GPU, no scaling out. If a technique cannot run on that box, it does not ship.

**700 milliseconds.** Every query has a production latency budget of **under 700 ms.** A method that is more accurate but slower than that is not more accurate in any sense a shopper experiences — it is a different product.

These two rules are the reason the numbers below are worth reading. Anyone can buy accuracy with a bigger machine and a slower query. The question this phase asks is what the agent can do when it is not allowed to.

## Why a Product Is Not a Document

Every dataset before this one, academic or not, was flat text: a query, a passage, a relevance judgment that was essentially yes-or-no. A product breaks all three assumptions at once, which is exactly why [Part 1](/posts/2026/07/02/self-learning-agent-based-retail-search-part-1-why-retail-search-is-harder-than-it-looks) argued retail search is a harder problem than it looks.

A product is **structured** — title, brand, color, bullet points, attributes — not a bag of words. And relevance stops being binary. ESCI grades every query-product pair as **Exact, Substitute, Complement, or Irrelevant**, with graded gains (Exact = 1.0, Substitute = 0.1, Complement = 0.01, Irrelevant = 0). A *substitute* — a different product that would still satisfy the shopper — carries real credit. Sometimes the substitute is the best answer available, and a search engine that only chases exact matches leaves the customer with nothing. Ranking quality here is genuinely multi-objective, and for the first time in the project it is measured against labels a human actually assigned.

## Two Modes, Never Averaged

Before any number means anything, one discipline has to be stated, because getting it wrong is the easiest way to publish a lie by accident. ESCI can be evaluated two completely different ways, and they must never be mixed.

- **Official rerank mode.** The dataset hands you up to 40 candidate products per query; you re-rank them. This is directly comparable to published numbers, because everyone is ranking the same list. It is the mode where "parity with the baseline" is a meaningful claim.
- **End-to-end retrieval mode.** You index the full 1.2M-product corpus and retrieve from scratch — the real problem a shopper poses. But ESCI's judgments are *incomplete*: only a fraction of what you retrieve was ever labeled. At full scale, judged coverage is about **27%**, so the absolute nDCG (~0.26) is floored by missing labels, not by the system. That number is real, but it can only be read alongside its coverage, and it can never be compared to a published figure.

Averaging these two would produce a meaningless middle number that flatters nothing and describes nothing. Every result below states which mode it is.

## The Baselines Reproduce

The BM25 baseline in official rerank mode scores **0.8254** on a 1,000-query subsample and **0.8268** across all 8,956 English test queries. The published single-model baseline for this task is 0.8503, so plain keyword matching over structured product fields already gets most of the way there. Good — the harness is sane, and the headroom the hybrid has to prove is small and real, not an artifact of a broken baseline.

## The Hybrid, at 1.2 Million Products

The agent embedded all 1.2 million products with the same retrieval-tuned BGE model[^bge] that won Phase 2, and blended dense vector scores with BM25. Here is the transfer test, in official rerank mode across all 8,956 queries:

| System | nDCG@10 | vs BM25 | Notes |
| --- | ---: | ---: | --- |
| BM25 (structured fields) | 0.8268 | — | the Phase 1 lexical core |
| BGE dense only | 0.8449 | +2.2% | meaning alone already beats keywords |
| **BM25 + BGE hybrid** | **0.8468** | **+2.5%** | within 0.4% of published 0.8503 |
| *Published single-model baseline* | *0.8503* | — | *mBERT cross-encoder (the parity target)* |
| *KDD Cup 2022 winner* | *0.9043* | — | *query-time ensemble — out of scope* |

The hybrid reaches parity with the published single-model baseline — and it does it at roughly **0.45 seconds** with no cross-encoder at query time, where the baseline it matches runs a transformer over every candidate. The same architecture the agent discovered on aeronautics abstracts and validated across fifteen academic domains transfers, intact, to a million real products. That is the whole point of the two phases of rehearsal: the win was never Cranfield-shaped or BEIR-shaped. It was real.

What it does **not** do is reach the 0.904 KDD winner, and it is worth being precise about why that is not a failure — it is the second rule doing its job. That number is an ensemble of large cross-encoders scoring candidates at query time. Our budget is under 700 ms per query; running a stack of cross-encoders over every candidate blows past that by orders of magnitude, and it will not run on a 4 GB free-tier box at all. It is a different latency class entirely, and never the target. Chasing it would mean optimizing for a leaderboard at a latency no shopper would tolerate. Parity with the single-model baseline, inside the 700 ms budget, is the honest claim.

## Making a Million Vectors Fit

This is where the first rule bites. There was a hardware wall between "the hybrid works" and "the hybrid runs": 1.2 million full-precision vectors are 3.74 GB, and Phase 2 had already established the ceiling — on this same 4 GB box, no corpus above roughly half a million documents could hold a live nearest-neighbor index. So the agent tested whether the vectors could be compressed without losing the win.

| Precision | nDCG@10 | vs float32 | Corpus size |
| --- | ---: | ---: | ---: |
| float32 | 0.8472 | — | 3.74 GB |
| **int8** | **0.8473** | **+0.0001** | **0.93 GB** |
| binary | 0.8392 | −0.008 | 0.12 GB |

**int8 quantization is lossless at 4× compression** — the +0.0001 is rounding. That single result broke the Phase 2 ceiling. The 1.2M int8 index loaded on the free-tier box at 4.8 GB — well under the 20 GB storage limit, and the nearest-neighbor graph fit inside the 4 GB of RAM — and the cluster stayed green. It is wired into the live endpoint (Workers AI query embedding → int8 vectors → kNN blended with BM25). Binary quantization trades about 1% nDCG for 32× compression — kept in the portfolio for a scale where even int8 will not fit, but not needed yet.

## The Honest Limit

Parity does not mean shipped-as-default, and here is why. Reranking 40 candidates is fast (~0.45s), but retrieving from scratch across 1.2 million int8 vectors on a free-tier instance takes **2.4 to 7 seconds** warm — the nearest-neighbor step alone is about 2.5s, far over the project's 700ms production target.

So the agent did not pretend the problem away. The default `/api/esci/search` stays fast BM25 (~0.43s, production-shaped), and the hybrid is **opt-in** via `?mode=hybrid` — the semantic wins are fully demonstrable without making the default search slow. A production-latency hybrid needs a bigger instance, which is an approval-gated cost decision, not an engineering surprise. The system tells you exactly what it can and cannot do at the latency it actually runs at.

Try the difference yourself: [`?mode=hybrid`](https://retail-search.feroshjacob.workers.dev/phases/esci/search?q=laptop&mode=hybrid) against the default.

## Who Did What

As in every phase, the division of labor is stated plainly. The operator's role in Phase 3 was direction and approvals — kicking off the phase, the "go" decisions, approving the deploy, catching UI-parity issues. The measurement, the embedding pipeline, the quantization experiment, and the implementation were the agent's. The human decided *what to test and whether to ship*; the agent did the testing and the building.

## What's Next

Parity is the entry ticket, not the destination. Retail is the first dataset in this entire project with real graded labels — which means it is the first place the classical retail-search toolkit gets a fair audition. Field weighting, learning-to-rank, query understanding: techniques that sat dormant through two phases for lack of labels finally get to prove whether they earn their keep on real product data. The next part is that audition — and the answer is more surprising than "the textbook works."

## Takeaways

1. **The academic core transferred.** BM25 + BGE hybrid hits parity with the published single-model baseline on 1.2M real products — the two phases of academic rehearsal paid off exactly as designed.
2. **Claim the right baseline.** Parity at production latency with the single-model baseline is honest; chasing a query-time ensemble leaderboard at 7-second latency is not.
3. **int8 was free.** Lossless 4× compression is what turns "the hybrid works in an experiment" into "the hybrid runs on the hardware we have."
4. **State the mode, and state the coverage.** Official rerank and end-to-end retrieval are different questions; conflating them, or comparing an incompletely-judged retrieval score to a published number, is how honest projects accidentally lie.

Phase 1 proved the method. Phase 2 found out what it learned. Phase 3 hands the agent products — and the first thing the products confirmed is that nothing so far was wasted.

## References

Reproduction figures are measured on this project's own free-tier stack; comparison targets are published numbers, cited below. Phase 3 experiment artifacts and the architecture ledger are public in the [project repository](https://github.com/Northvalley-Intelligence/retail-search).

[^esci]: Reddy, C. K., Màrquez, L., Valero, F., Rao, N., Zaragoza, H., Bandyopadhyay, S., Biswas, A., Xing, A., & Subbian, K. (2022). [Shopping Queries Dataset: A Large-Scale ESCI Benchmark for Improving Product Search](https://arxiv.org/abs/2206.06588). arXiv:2206.06588. Source of the products, the Exact/Substitute/Complement/Irrelevant labels, the official gain mapping, and the published baseline (0.8503) and KDD Cup 2022 winner (0.9043) reference numbers.

[^bge]: Xiao, S., Liu, Z., Zhang, P., & Muennighoff, N. (2023). [C-Pack: Packaged Resources To Advance General Chinese Embedding](https://arxiv.org/abs/2309.07597). arXiv:2309.07597. The BGE family, including `bge-base-en-v1.5`, the retrieval-trained encoder used for dense retrieval and served at runtime via Cloudflare Workers AI.

[^ndcg]: Järvelin, K., & Kekäläinen, J. (2002). [Cumulated gain-based evaluation of IR techniques](https://doi.org/10.1145/582415.582418). *ACM Transactions on Information Systems*, 20(4), 422–446. nDCG@10 with the ESCI graded gain mapping is the metric throughout.
