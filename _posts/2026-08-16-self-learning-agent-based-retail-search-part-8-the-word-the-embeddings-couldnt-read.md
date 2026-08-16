---
title: "SLAB-RS, Part 8: The Word the Embeddings Couldn't Read"
date: 2026-08-16
permalink: /posts/2026/08/16/self-learning-agent-based-retail-search-part-8-the-word-the-embeddings-couldnt-read
categories:
  - self-learning-agent-based-retail-search
series: self-learning-agent-based-retail-search
series_order: 9
phase: phase-3
tags:
  - retail search
  - amazon esci
  - query understanding
  - negation
  - product search
  - failure analysis
  - information retrieval
  - ai agents
  - mission-driven engineering
image: /images/self-learning-agent-based-retail-search-part-8-without-rule.png
excerpt: "Phase 3 measured the whole classical toolkit and shipped almost none of it. One thing did ship — and it didn't come from a bigger model. It came from reading a thousand failures and noticing that a million-vector embedding model cannot read the word 'without'. The fix was a single linguistic rule. +4.8 nDCG on negation queries, live."
---

![How the shipped negation rule ranks "refrigerator without freezer": a fridge with no freezer kept at the top, a fridge that positively has a freezer demoted, and a "freezer-free" product protected because it advertises the absence — with the rule stated: demote only a positive mention of the excluded term](/images/self-learning-agent-based-retail-search-part-8-without-rule.png)

[Part 7](/posts/2026/08/08/self-learning-agent-based-retail-search-part-7-the-textbook-wins-the-benchmark-and-loses-the-trade) ended on a hard verdict: the classical retail-search toolkit was measured end to end, and almost none of it earned its place. But *almost* none is not none. One component shipped in Phase 3, and it is the most interesting result of the phase — because it did not come from a bigger model, a training pipeline, or more data. It came from reading the failures.

The phase opened with a funny one. A search for `chikko` — a misspelling — was returning coffee, and the hybrid seemed to quietly fix it. Encouraging. But "seemed to" is not a number, so the agent stopped trusting the anecdote and measured a thousand queries. The fuller truth was less flattering, and much more useful.

## The Short Version

The agent built a **failure taxonomy**: it took 1,000 queries that each *have* a known-correct Exact product, and asked a precise question — when the right answer exists, how often does the system fail to rank it first, and *why*?

That taxonomy exposed a residual the million-vector embedding model could not touch: **negation.** Queries like `refrigerator without freezer` failed just as often with dense retrieval as without it — because an embedding model does not encode the word *without*. The fix was not a bigger model. It was a **single linguistic rule**, and it shipped: **+4.8 nDCG on negation queries, no regression, live.**

And the counterpoint that proves the discipline: a second, similar fix for attribute constraints was measured and **deliberately not shipped**, because the taxonomy predicted — correctly — that it had almost no headroom left.

## Reading a Thousand Failures

Most retrieval evaluation reports one aggregate number. A failure taxonomy asks a sharper question, and it needs *provable* failures to be honest — cases where you can prove the system got it wrong because a correct answer demonstrably existed and was not ranked first.

Across the 1,000 queries that have an Exact match,[^esci] plain BM25 fails to rank that Exact first **37.2% of the time** (372 queries). The hybrid brings that to 30.4% (304). But the interesting part is *what kind* of failure each one is:

| Failure type | BM25 top-1 misses | After hybrid |
| --- | ---: | ---: |
| A substitute outranks the exact | 197 | 163 |
| A complement/irrelevant outranks it | 122 | 95 |
| Brand mismatch | 13 | 6 |
| **Intent / negation** | **40** | **40** |

Read that last row twice. Every other category improved when dense retrieval was added — brand mismatch was nearly eliminated, substitutes were cut. **Negation did not move at all.** Forty failures before, forty after. And underneath the flat total, it was not stable — the hybrid fixed 5 negation queries and broke 5 others. That is not improvement. That is a coin toss. The single most powerful technique in the entire project — dense embeddings, the thing that broke every ceiling from Cranfield to a million products — is *blind* to negation.

## Why the Obvious Fix Is Wrong Twice

The obvious reaction to `refrigerator without freezer` is a `must_not` filter: exclude any product whose text contains "freezer." It is wrong, and it is wrong in two different directions.

**Trap one: the word is part of a name.** "Without" often appears in product titles that have nothing to do with a constraint — *Cemetery Without Crosses* (a film), a book, a band's album. A `must_not` on the excluded term throws these out for containing their own title.

**Trap two: the correct product advertises the absence.** The single best answer to "refrigerator without freezer" is often a product whose description literally says *"no freezer compartment"* or *"freezer-free design."* A filter that removes anything mentioning "freezer" deletes exactly the product the shopper wanted, because it named the very thing it lacks.

So a naive filter fails the movie *and* the freezer-free fridge — the two populations negation splits into. An embedding model, meanwhile, sees "refrigerator" and "freezer" as highly related words and cheerfully returns fridges *with* freezers. Neither scale nor a filter solves this. It needs to be *read*.

## The Rule the Agent Shipped

The fix is one observation stated as a rule: **demote a product only when it makes a *positive* mention of the excluded term — not a mention of its absence.**

A fridge whose description says "spacious freezer" is a positive mention of "freezer" → demote it. A fridge that says "no freezer" or "freezer-free" is a mention of the term's *absence* → protect it. A film called *Cemetery Without Crosses* never positively asserts the excluded attribute at all → untouched. One rule, both traps closed.

On the 61 real negation queries in the set, it moved nDCG[^ndcg] from **0.7784 to 0.8267 — up 4.8 points** — and fixed 10 of the 28 top-1 failures, with no regression elsewhere. It costs nothing at query time and needs no model. It shipped to production, and it is the "hybrid + negation" row that sat at 0.8528 in [Part 7's](/posts/2026/08/08/self-learning-agent-based-retail-search-part-7-the-textbook-wins-the-benchmark-and-loses-the-trade) scoreboard — the lean stack the full training cascade could only beat by a tenth of a percent.

You can watch it work: [`refrigerator without freezer`](https://retail-search.feroshjacob.workers.dev/phases/esci/search?q=refrigerator+without+freezer&mode=hybrid) — freezer-equipped units drop, and the "without freezer" products hold their place.

## The Fix the Agent Refused to Ship

Here is the part that makes it a discipline and not a lucky guess. Negation has a sibling: attribute constraints, like a color or a size that must match. The agent built the same kind of reranker for attribute conflicts and measured it exactly the same way.

On its own 44-query subset it looked fine — nDCG 0.8679 to 0.8855. But across the full thousand queries the effect was **+0.0008**, and it fixed exactly **one** of eleven top-1 failures. The taxonomy had already predicted this: attribute failures were a small, mostly-handled category. So it was **not shipped.** The measurement existed only to answer "is this worth building?" — and the honest answer was no.

That is the whole method in one contrast. Two nearly identical fixes, built and measured the same way. One found a residual nothing else could touch and shipped. The other confirmed there was no residual left and was set aside. The taxonomy is what told them apart *before* either became a maintenance burden.

## The Point

Phase 3's biggest, most portable win was a million-vector embedding model. Its last, shipped win was a single sentence about the word *without* — something that model, for all its scale, does not encode. The residual that survived more data and more model fell to *reading the data*.

That is where structural query understanding earns its keep: not as a layer you add because the textbook says so ([the textbook cost more than it returned](/posts/2026/08/08/self-learning-agent-based-retail-search-part-7-the-textbook-wins-the-benchmark-and-loses-the-trade)), but at the exact point where a bigger model stops helping and a human observation starts. You find that point by measuring failures, not by measuring averages.

## Closing Phase 3

Three articles, one arc. [Part 6](/posts/2026/08/02/self-learning-agent-based-retail-search-part-6-the-agent-meets-a-million-real-products): the academic stack transferred to a million real products at production latency — parity, on a free-tier box. [Part 7](/posts/2026/08/08/self-learning-agent-based-retail-search-part-7-the-textbook-wins-the-benchmark-and-loses-the-trade): the full classical toolkit was the ceiling, and not worth its cost. Part 8: the one thing worth shipping came from understanding a failure, not adding scale.

What Phase 3 ships is exactly that lean: BM25 + a BGE hybrid + one negation rule, all inside 700 ms on one small box. Everything else was measured, mapped, and left on the shelf with its evidence attached.

Phase 4 is where the last thing academic data cannot provide arrives: **customers.** Clicks, add-to-cart, purchases — the only signal that separates a search engine that is technically correct from one that actually sells. That is where the learning-to-rank infrastructure kept in the portfolio, dormant since Phase 2, may finally get the signal it was built for.

## Takeaways

1. **Measure failures, not just averages.** The aggregate nDCG hid the fact that dense retrieval does nothing for negation. A per-category failure taxonomy is what surfaced the one residual worth fixing.
2. **Some meaning is not in the vectors.** A retrieval-tuned embedding model that beat every benchmark still cannot read the word "without." Scale did not close that gap; a rule did.
3. **The obvious fix can be wrong in two directions at once.** A `must_not` filter on negation kills both product names and products that advertise the absence. The correct rule demotes only a positive mention of the excluded term.
4. **Knowing what not to build is the return on measuring.** The attribute reranker worked on its own subset and was still correctly refused, because the taxonomy showed it had no headroom. That refusal is the discipline, not a failure of it.

Phase 1 proved the method. Phase 2 found out what it learned. Phase 3 took it to real products — and the last word belonged not to the biggest model in the stack, but to a careful reading of where it broke.

## References

Reproduction figures are measured on this project's own free-tier stack, over the 1,000-query ESCI evaluation subsample. Phase 3 experiment artifacts and the architecture ledger are public in the [project repository](https://github.com/Northvalley-Intelligence/retail-search).

[^esci]: Reddy, C. K., Màrquez, L., Valero, F., Rao, N., Zaragoza, H., Bandyopadhyay, S., Biswas, A., Xing, A., & Subbian, K. (2022). [Shopping Queries Dataset: A Large-Scale ESCI Benchmark for Improving Product Search](https://arxiv.org/abs/2206.06588). arXiv:2206.06588. Source of the products, the graded Exact/Substitute/Complement/Irrelevant labels, and the query set the failure taxonomy is built on.

[^ndcg]: Järvelin, K., & Kekäläinen, J. (2002). [Cumulated gain-based evaluation of IR techniques](https://doi.org/10.1145/582415.582418). *ACM Transactions on Information Systems*, 20(4), 422–446. nDCG@10 with the ESCI graded gain mapping is the metric throughout; all comparisons are on the 1,000-query evaluation subsample.
