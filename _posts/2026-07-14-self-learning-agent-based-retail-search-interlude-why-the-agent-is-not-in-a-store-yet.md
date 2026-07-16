---
title: "SLAB-RS, Interlude: Why the Agent Is Not in a Store Yet"
date: 2026-07-14
permalink: /posts/2026/07/14/self-learning-agent-based-retail-search-interlude-why-the-agent-is-not-in-a-store-yet
categories:
  - self-learning-agent-based-retail-search
series: self-learning-agent-based-retail-search
series_order: 5
phase: phase-2
tags:
  - retail search
  - beir
  - benchmarks
  - zero-shot retrieval
  - search evaluation
  - opensearch
  - bm25
  - information retrieval
  - ai agents
  - mission-driven engineering
image: /images/self-learning-agent-based-retail-search-interlude-road-to-retail.png
excerpt: "Four articles into a series called retail search and there is not a single product — only aeronautics abstracts. Here is the map: what BEIR is, why the same BM25 scores 0.158 on one dataset and 0.789 on another, why I was wrong to call Phase 2 a filter, and what has to happen before the agent reaches real products."
---

![The four-phase road to retail search: the agent stands at Phase 2 on BEIR, with Phase 1 Cranfield complete behind it, and Amazon ESCI products and customer behavior still ahead — the storefront sits at the far end of the road, past the stretch marked academic corpora](/images/self-learning-agent-based-retail-search-interlude-road-to-retail.png)

A reader four articles into a series called *Self-Learning Agent-Based Retail Search* is entitled to a complaint: there are no products. There is no store. There are 1,400 abstracts about supersonic wing heating, published in 1960s aeronautics journals, and an agent that has spent six days learning to rank them slightly better.

The complaint is fair. This article answers it.

## The Short Version

The agent is not in a store yet because I do not know which of its wins are real. Everything it learned in [Phase 1](/posts/2026/07/10/self-learning-agent-based-retail-search-part-4-when-embeddings-finally-earned-their-place) — a +8.8% keyword rerank, an +18.0% embedding hybrid, a +20.3% learned ranker — was learned on one corpus, in one domain, in one writing style. A technique that only works on 1960s aeronautics abstracts is not a search technique. It is an overfit.

So Phase 2 takes every technique the agent discovered and runs it across [BEIR](https://github.com/beir-cellar/beir): fifteen public datasets spanning scientific claims, nutrition, finance, arguments, forum questions, fact-checking, and web search. Not to find a winner. To build a map of where each technique works — and then carry the whole map into Phase 3, where the documents finally become products.

## What Cranfield Could and Could Not Teach

Cranfield's job was to make the method real before the data got interesting. It did that. The agent now has a measured baseline, an evaluation harness it cannot lie to, a habit of publishing its failures, and a promotion gate that refuses to accept a technique on vibes.

What Cranfield could not teach is whether any of it transfers. Every document in Cranfield is flat text written by aerospace researchers for aerospace researchers. Every query is a well-formed technical question. Nothing in that corpus resembles someone typing `wireless earbuds cheap` into a phone at a bus stop.

There are two ways to find out if the agent learned search or just learned Cranfield. I could go straight to retail data and hope. Or I could test the techniques against a benchmark built specifically to catch this mistake.

## What BEIR Is

[BEIR](https://github.com/beir-cellar/beir) — Benchmarking-IR — is a suite of retrieval datasets deliberately chosen to be different from each other, published in 2021 by a team at TU Darmstadt.[^beir] Same task in every one: given a query, return relevant documents from a corpus. Everything else varies as much as the authors could manage.

The point is **zero-shot** evaluation. You build a system somewhere else, then point it at a domain it has never seen, with no tuning and no training on it, and see what survives. It is the closest thing information retrieval has to an honesty test.

Scores throughout this article are [nDCG@10](https://en.wikipedia.org/wiki/Discounted_cumulative_gain) — the standard retrieval measure, roughly "how good are the top ten results, counting the higher positions for more."[^ndcg] It runs 0 to 1, and it is the number BEIR reports.

The domains are chosen to have as little in common as possible. One dataset asks you to verify a scientific claim. Another wants the best answer to a personal finance question. Another wants the duplicate of a forum post. Another is plain web search.

BEIR ships eighteen datasets, but four of them — BioASQ, Signal-1M, TREC-NEWS, and Robust04 — need licences or manual assembly and cannot simply be downloaded. That leaves **fifteen that anyone can fetch and reproduce**, and those fifteen are what this project commits to. Reproducibility is a selection criterion here, not a footnote: a result you cannot re-run is a claim, not evidence.

None of them are aeronautics. None of them are retail either — and that is worth saying plainly rather than pretending BEIR is the destination. BEIR is the honesty test between the lab and the store.

The scale shift is the other half. Cranfield was 1,400 documents. The largest BEIR corpus is 8.8 million — about **6,300 times larger**. The agent has been doing careful work in a room. Phase 2 opens the door.

## The Number That Changes the Plan

Before running a single experiment, one fact from BEIR's published baselines reorganized my thinking about this entire project.

[BM25](https://en.wikipedia.org/wiki/Okapi_BM25) is the plainest keyword ranking function in information retrieval — it scores a document by how often your query's words appear in it, adjusted for how common those words are and how long the document is.[^bm25] No learning, no embeddings, no tuning to speak of. It is the same handful of arithmetic everywhere it runs.

Point it at BEIR and the published baselines put it at **0.158 on SCIDOCS and 0.789 on Quora** — the same algorithm, the same metric, a five-fold spread driven entirely by which corpus it happens to be reading.[^beir] The other thirteen land everywhere in between.

Sit with that. If the most universal technique in the field swings that far on domain alone, then a score is not a grade. It is a reading taken at a location. Cranfield's 0.2995 baseline was never a statement about BM25's quality — it was a statement about Cranfield. Put my agent's whole Phase 1 on that scale and it sits in the bottom third of a spread it never knew existed, having spent six days pushing one reading up by a fifth.

And it means the question I had been asking was wrong.

## I Said BEIR Was a Filter. I Was Wrong.

Part 4 ended with a promise:

> Phase 2 is about transferability: the candidates that won here go to BEIR... and only the techniques that prove they generalize beyond aeronautics keep their place in the architecture. That gate decides what travels with us toward real retail product search.

That was the wrong instinct, and the BM25 spread is why. Suppose a technique clearly helps financial question-answering and does nothing at all for citation prediction. "Does it generalize?" has no useful answer for that technique. The honest answer is *it depends on the domain* — which is not a verdict, it is a map. Filtering on a global yes/no throws the map away and keeps a single bit.

Worse, it optimizes for exactly the wrong thing at exactly the wrong time. Phase 3 is retail. If I discard a technique in Phase 2 because it only helped two of fifteen academic datasets, I will never learn that those two datasets were the ones with short, keyword-like, product-title-shaped queries — the ones most like retail. I would have thrown away the most relevant evidence in the project because it failed a popularity contest held in the wrong domain.

So the policy changed. Nothing gets discarded. BEIR evidence *classifies* each technique instead:

- **Universal** — improves at least 70% of datasets, no serious regression, latency within budget. Goes into the architecture core.
- **Domain-conditional** — improves at least two datasets. Kept, with its evidence attached, for the domains where it earns its place.
- **Dormant** — improves fewer than two. Kept anyway, with the record of where it failed.

Every technique travels to Phase 3. What changes is that each one arrives carrying a map of where it has worked. When the agent meets Amazon product data, it will not be guessing which of its tools to reach for — it will be matching a new domain against fifteen it has already measured.

Superset, not filter. It is a smaller-sounding change than it is.

## The Published Number Is a Convention, Not a Fact

Phase 2's first mission is not an experiment. It is an evaluation harness that every dataset — Cranfield and all fifteen BEIR sets — plugs into identically, so that no technique gets an advantage from the plumbing. The rule I am holding it to: reproduce the published number before believing your own. A harness that lands far from a well-established reference is a harness with a bug, and every result it produces afterward is fiction.

I will report those reproductions when the phase is done, not while it is running. But there is a lesson available before any of my numbers exist, and ArguAna is where it lives.

ArguAna's published BM25 baseline is either **0.315 or 0.472** depending on which source you read. Both are correct. The BEIR paper reports 0.315;[^beir] a careful 2024 reproduction reports 0.472.[^bm25s] The difference is not implementation quality, and neither party made a mistake. It is that 1,298 of ArguAna's 1,406 test queries *also exist as documents in its own corpus* — so the single easiest result to retrieve is the query finding itself. The BEIR paper counted those self-hits. The modern reproduction excludes them. One decision, undocumented in either number, worth roughly 50%.

This is the part I want a reader to take away more than any metric in this series: **the published number everyone cites is a convention, and you only discover which convention by rebuilding it yourself.** A benchmark is not a scoreboard handed down from authority. It is a pile of decisions someone made, most of them invisible in the figure that gets quoted. Take a leaderboard number at face value and you can be off by half while doing everything else correctly.

It also sets the bar for what I am allowed to claim later. When Phase 2 reports a reproduction, the number is meaningless unless it says which convention it reproduced.

## One Small Box and 8.8 Million Documents

Running fifteen datasets sounds like a research design. It is mostly a budget.

This entire project runs on a single free-tier OpenSearch instance: 4 GB of RAM, 20 GB of storage, one node. The largest BEIR corpus is 8.8 million documents. Six of the fifteen are measured in millions. They cannot all live on that box, and several cannot hold a live vector index at all — past roughly half a million documents, live nearest-neighbor search is simply out of reach on this hardware.

So Phase 2 runs sequentially: fetch one dataset, index it, evaluate it, cache the results, drop the index, move to the next. And the fifteen sort themselves into three groups by what the box can physically do with them — what I call tiers:

| Tier | Corpus size | What the box can do | Datasets |
| --- | ---: | --- | --- |
| **1** | 3.6K – 58K | Everything: live keyword and live vectors | SciFact, NFCorpus, FiQA, ArguAna, SCIDOCS |
| **2** | 171K – 523K | Live keyword; vectors computed offline | TREC-COVID, Touché-2020, CQADupStack, Quora |
| **3** | 2.7M – 8.8M | Sequential lexical only; vectors via subsampling | NQ, HotpotQA, FEVER, Climate-FEVER, DBpedia, MS MARCO |

A tier is not a statement about a dataset's quality or difficulty. It is a statement about my hardware, and it decides how much of the truth I can afford to measure. Where a corpus is too large for full treatment, the article that reports it will say so, and will say exactly what was subsampled.

I could rent a bigger cluster. I am deliberately not doing that yet. A method that only works with money behind it teaches small teams nothing, and this project is supposed to be reproducible by someone with a laptop and a free tier.

## What to Expect in Phase 2

Five missions, in order:

1. **Baselines everywhere.** BM25 across all three tiers, each sanity-checked against published references, with the infrastructure limit for each dataset recorded rather than hidden.
2. **The evidence matrix.** Every Phase 1 technique — field weighting, coverage, the PRF rerank, the BGE hybrid, the learned ranker — run across every dataset the hardware permits. Technique × dataset. This grid is the actual deliverable of the phase; everything else supports it.
3. **Paying off the 501.** Part 4 shipped an endpoint that honestly returns `501 milestone_runtime_not_enabled`, because the embedding milestone could not generate query vectors at request time and I would not fake a demo to hide it. Phase 2 wires in runtime query embedding, validates it produces vectors identical to the offline path, and turns that 501 into a working public endpoint.
4. **Failure analysis and new techniques.** Where BM25 falls furthest short of its published reference, look at the queries and find out why. New techniques get tested here and back-tested against Cranfield, so the ledger stays consistent in both directions.
5. **Classification and ARCH-0.5.** Every technique labeled Universal, Domain-conditional, or Dormant, with the evidence behind each label published, and a cross-domain architecture core declared.

I expect several Phase 1 winners to be exposed as Cranfield-specific. I would rather find that out now, publicly, than carry it into retail and mistake it for a product insight.

## What Retail Actually Adds

Phase 3 is Amazon ESCI — real product search data with real relevance judgments — and it is a bigger jump than the dataset swap makes it sound, because a product is not a document.

A Cranfield document is flat text. A product is structured: a title, a brand, a color, a size, a price, bullet points, attributes. Relevance stops being one question and becomes several. `nike running shoes size 10` has a brand constraint, a category, and a hard filter that ranking must not talk its way around. ESCI's labels encode the distinction the entire series opened with in [Part 1](/posts/2026/07/02/self-learning-agent-based-retail-search-part-1-why-retail-search-is-harder-than-it-looks) — exact match, substitute, complement, irrelevant — because in retail, "relevant" is not binary and the substitute is sometimes the better answer.

Phase 4 adds the last thing academic data structurally cannot provide: customers. Clicks, add-to-cart, purchases. Signals about what people actually chose, which is the only evidence that separates a search engine that is technically correct from one that sells anything.

That is the whole arc. Phase 1 proved the method on data that could not lie. Phase 2 finds out what the method actually learned. Phase 3 gives the agent products. Phase 4 gives it customers.

## Takeaways

1. **A score is a reading, not a grade.** The same BM25 scores 0.158 and 0.789 on two different corpora. Any relevance number without its dataset attached is decoration.
2. **Classify, do not filter.** "Does it generalize?" collapses a map into a bit. Keep every technique and keep the record of where it worked — the failures in one domain are evidence for another.
3. **Reproduce the published number before trusting your own.** And when two sources disagree by 50%, the disagreement is usually a convention nobody wrote down.
4. **Constraints belong in the article.** One 4 GB box, 8.8 million documents, and a sequential loop is not an embarrassment to hide behind a bigger cluster. It is the reproducibility.

The agent is not in a store yet. It is doing the thing that has to happen first: finding out which of the things it believes are true anywhere but here.

Phase 2 starts now. The results will be published whether or not they are flattering — that has been the rule since Part 1, and the rejections are still what give the accepted numbers their meaning.

## References

Every BM25 figure in this article is someone else's published number, not a measurement of mine. Phase 2's own results will be reported when the phase is done.

[^beir]: Thakur, N., Reimers, N., Rücklé, A., Srivastava, A., & Gurevych, I. (2021). [BEIR: A Heterogenous Benchmark for Zero-shot Evaluation of Information Retrieval Models](https://arxiv.org/abs/2104.08663). *NeurIPS 2021 Datasets and Benchmarks Track.* arXiv:2104.08663. Source of the published BM25 nDCG@10 baselines quoted here, including ArguAna at 0.315. Datasets and download links: [github.com/beir-cellar/beir](https://github.com/beir-cellar/beir).

[^bm25s]: Lù, X. H. (2024). [BM25S: Orders of magnitude faster lexical search via eager sparse scoring](https://arxiv.org/abs/2407.03618). arXiv:2407.03618. Source of the Lucene-class ArguAna reproduction at 0.472, measured with self-hits excluded.

[^bm25]: Robertson, S., & Zaragoza, H. (2009). [The Probabilistic Relevance Framework: BM25 and Beyond](https://www.staff.city.ac.uk/~sbrp622/papers/foundations_bm25_review.pdf). *Foundations and Trends in Information Retrieval*, 3(4), 333–389.

[^ndcg]: Järvelin, K., & Kekäläinen, J. (2002). [Cumulated gain-based evaluation of IR techniques](https://doi.org/10.1145/582415.582418). *ACM Transactions on Information Systems*, 20(4), 422–446. The paper that introduced nDCG.
