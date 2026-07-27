---
title: "SLAB-RS, Part 5: How the Agents Discovered Hybrid Search"
date: 2026-07-26
permalink: /posts/2026/07/26/self-learning-agent-based-retail-search-part-5-how-the-agents-discovered-hybrid-search
categories:
  - self-learning-agent-based-retail-search
series: self-learning-agent-based-retail-search
series_order: 6
phase: phase-2
tags:
  - retail search
  - beir
  - zero-shot retrieval
  - vector search
  - hybrid search
  - embeddings
  - opensearch
  - learning to rank
  - information retrieval
  - ai agents
  - mission-driven engineering
image: /images/self-learning-agent-based-retail-search-part-5-technique-matrix.png
excerpt: "The agents took their Phase 1 wins to fifteen new domains — and discovered hybrid search: the one method that improved every single one, by up to 64%. The keyword tricks that looked brilliant on aeronautics turned out to be aeronautics-shaped, and even the cleverest ranker got demoted by Occam's razor."
---

![How the agents discovered hybrid search: a technique-by-domain evidence grid where the BGE hybrid row is green across all six domains — gains up to +64% — while the keyword-reranker rows are a mix of small greens and reds, and the agent stands beside the one row that wins everywhere](/images/self-learning-agent-based-retail-search-part-5-technique-matrix.png)

**New here?** This series is a live experiment. The goal is to build a good retail search engine without designing it by hand — instead, AI agents discover the best method themselves. They run measured experiments on public search benchmarks, keep only what the evidence supports, and let the design emerge from results instead of opinion.

[Phase 1](/posts/2026/07/10/self-learning-agent-based-retail-search-part-4-when-embeddings-finally-earned-their-place) tried this on a single domain: 1,400 aeronautics research abstracts. Phase 2 widened it to fifteen datasets across many different domains at once — and one method came out ahead in every one of them: **hybrid search**, the discovery this article is about.

None of this asks for your trust — the system is live and the code is open. Run any query yourself at [retail-search.feroshjacob.workers.dev/phases/beir/search](https://retail-search.feroshjacob.workers.dev/phases/beir/search) and pick the **BGE hybrid** option to watch the winning method work, with an explain view behind every result. Every experiment and decision behind it is public at [github.com/Northvalley-Intelligence/retail-search](https://github.com/Northvalley-Intelligence/retail-search).

The [interlude](/posts/2026/07/14/self-learning-agent-based-retail-search-interlude-why-the-agent-is-not-in-a-store-yet) ended on an admission: the agent did not know which of its Phase 1 wins were real. A +8.8% keyword rerank, an +18% embedding hybrid, a +20% learned ranker — all of it learned on 1,400 aeronautics abstracts, none of it yet tested anywhere else.

Phase 2 tested it. The agent's techniques ran across fifteen public [BEIR](https://github.com/beir-cellar/beir) datasets spanning scientific claims, nutrition, finance, argument retrieval, and citation prediction. This is what came back.

## The Short Version

Three findings, in order of how much they surprised me.

1. **The keyword rerankers were aeronautics-shaped.** The pseudo-relevance-feedback rerank that beat Cranfield's ceiling *hurt* financial and argument retrieval. It improved three of six datasets — the scientific ones. Domain-conditional, not universal.
2. **Exactly one technique improved every dataset:** the BGE embedding hybrid, by +9.5% to +64.0%, with zero regressions. And it won biggest on precisely the datasets where the keyword rerankers failed. Dense retrieval picks up what keyword matching structurally cannot.
3. **The cleverest technique got demoted.** A learning-to-rank model beat plain BM25 everywhere — but it only beat the simpler embedding hybrid on half the datasets, so it does not run by default. Simpler wins ties.

Out of that evidence came **ARCH-0.5**, the first architecture in this project chosen from many domains instead of one. And the `501` the agent shipped honestly in Part 4 is now a live endpoint.

## First, Do the Baselines Reproduce?

Nothing downstream means anything if the measuring stick is bent. So before a single technique ran, BM25 was baselined on all fifteen public BEIR datasets — sequentially, on one free-tier OpenSearch box, index-evaluate-drop, because the corpora do not fit at once.

The aggregate: **mean nDCG@10 of 0.4261 across the fifteen datasets, against the published BEIR BM25 average of about 0.43.**[^beir] Thirteen of fifteen landed within 9% of their published numbers; ten within 5%. That includes the Tier-3 giants — NQ at 0.3263, HotpotQA at 0.6022, MS MARCO at 0.2278 over 8.84 million passages indexed on the free tier.

But "reproduces the published number" is a claim with a hidden clause, and the interlude already taught the lesson once: with ArguAna, the published BM25 baseline was 0.315 or 0.472 depending only on whether you let a query retrieve itself. **A reproduction number is meaningless unless it says which convention it reproduced.** Every number above is nDCG@10 over each dataset's standard test qrels, with self-hits handled the way BEIR's own evaluation handles them.

Then two datasets refused to reproduce, and taught the lesson a second time — one layer deeper.

## The FEVER Problem: A Second Kind of Convention

FEVER scored 0.6493 against a published 0.753. Its sibling Climate-FEVER scored 0.1862 against 0.213. Both about 13% low. Two datasets out of fifteen, both drawing on the same 5.4-million-document Wikipedia corpus.

The instinct is "harness bug." It was not — and the evidence that it was not is that thirteen other datasets reproduced, *including three other Wikipedia-derived corpora*. A bug in the shared code path would not politely spare the neighbors.

The actual cause is a convention one level below the one the interlude found. ArguAna's gap was an *evaluation* convention — do you count self-hits. FEVER's gap is a *scoring* convention: OpenSearch's BM25 implementation and the Anserini/Lucene setup behind the published BEIR numbers disagree on tokenization and length-normalization details, and on this particular corpus those details compound into 13%.

The mission has an explicit non-goal against optimizing BEIR scores for their own sake, so the honest move was to document it — [ADL-0005](https://github.com/Northvalley-Intelligence/retail-search), operator-approved — and move on, rather than tune the box until a number I do not care about looks better. The interlude's rule holds and deepens: it is not enough to name the evaluation convention. You have to name the *scorer* too.

## The Reversal, Confirmed

Now the part that made the whole phase worth running.

In Part 4 I promised BEIR would act as a filter — techniques that generalize survive, the rest get dropped. The interlude retracted that: nothing gets discarded, everything gets *classified* — Universal, Domain-conditional, or Dormant — and travels to Phase 3 with its evidence. Here is why that retraction mattered, measured.

Run the Phase 1 keyword rerankers across the six datasets where full evaluation is tractable — Cranfield plus the five smallest BEIR sets — and they split cleanly:

| Technique | Scope | Improved | The tell |
| --- | --- | ---: | --- |
| Refined PRF rerank | Domain-conditional | 3 / 6 | +7.9% on Cranfield, **−6.8% on ArguAna, −3.3% on FiQA** |
| Coverage rerank | Domain-conditional | 3 / 6 | helps scientific text, flat-to-negative elsewhere |
| **BGE embedding hybrid** | **Universal** | **6 / 6** | **+9.5% to +64.0%, zero regressions** |
| Learning-to-rank | Portfolio (see below) | 4 / 4 vs BM25 | strong, but loses to the hybrid on half |

The pseudo-relevance-feedback rerank — the technique that broke Cranfield's keyword ceiling in [Part 3](/posts/2026/07/10/self-learning-agent-based-retail-search-part-3-failure-driven-bm25-and-the-lexical-ceiling) — improved the scientific and medical datasets and actively *hurt* finance and argument retrieval. Its Cranfield win was real. It was also partly a fact about aeronautics prose, not about search. Had I kept my Part 4 promise and filtered on "does it generalize," I would have thrown it away. Instead it is logged as domain-conditional, and Phase 3 will get to ask whether retail product text looks more like the corpora it helps or the ones it hurts.

## The One Technique That Traveled

The BGE hybrid — BM25 blended with dense vectors from a retrieval-trained embedding model[^bge] — was the only technique to improve all six datasets. And its gains map almost perfectly onto the keyword rerankers' failures:

| Dataset | Lexical baseline | BGE hybrid | Gain |
| --- | ---: | ---: | ---: |
| FiQA (finance) | 0.2536 | 0.4158 | **+64.0%** |
| ArguAna (arguments) | 0.4739 | 0.6432 | **+35.7%** |
| SCIDOCS (citations) | 0.1647 | 0.2217 | **+34.6%** |
| NFCorpus (nutrition) | 0.3273 | 0.3818 | +16.7% |
| Cranfield (aeronautics) | 0.3022 | 0.3533 | +16.9% |
| SciFact (science) | 0.6906 | 0.7565 | +9.5% |

Finance and arguments — the two domains PRF *hurt* — are where dense retrieval helped most. This is the entire thesis of the series in one table. Keyword reranking sharpens matches that already share vocabulary; it cannot rescue a query whose answer uses different words. Embeddings can, and the datasets where vocabulary drifts furthest from the query are exactly where the lift is largest. The agent's biggest, most portable win is the one that works on *meaning* rather than *words* — which is also the one retail will need most, where "cheap wireless earbuds" must find a product titled "Budget Bluetooth In-Ear Headphones."

That earns the BGE hybrid a spot in the architecture core.

## The Clever Technique I Demoted

The learning-to-rank model was the most sophisticated thing the agent built — boosted trees combining embedding and lexical features, trained with query-grouped cross-validation. Against BM25 it won everywhere, +8.8% to +41.5%.

It still did not make the core.

The decision-relevant comparison is not "does LTR beat BM25" — almost anything beats raw BM25. It is "does LTR beat the simpler thing already in the core," the plain BGE hybrid. And there it won only two of four datasets: Cranfield (0.3603 vs 0.3533) and ArguAna (0.6708 vs 0.6432), while losing SciFact and NFCorpus. A coin-flip improvement over a component that carries a training pipeline, a feature store, and a model to maintain.

So by [Occam's razor](https://en.wikipedia.org/wiki/Occam%27s_razor) — when two designs tie, the simpler one runs — LTR is a **portfolio** technique, not a core one. Available, evidenced, not on by default. Its infrastructure is kept for Phase 4, where real customer behavior gives a learned ranker the signal it was actually built for. Demoting your best-engineered component because it only ties is not a defeat; it is the discipline the whole project is about.

## The 501, Paid Off

Part 4 shipped an endpoint that honestly returned `501 milestone_runtime_not_enabled`, because the BGE milestone could not generate query embeddings at request time and I would not fake a demo to cover the gap.

It is now live. The `arch-0.3-bge` endpoint generates query vectors at request time through the [Cloudflare Workers AI](https://developers.cloudflare.com/workers-ai/) binding running `@cf/baai/bge-base-en-v1.5`, and the parity check is as clean as it gets: **the live endpoint scored nDCG@10 0.3532 over 40 Cranfield queries against the offline-validated 0.3533 — a difference of 0.0001.** The Workers AI embeddings match the local ones to the fourth decimal.

One rule survived intact. The mission forbids LLM calls in the live search path. An embedding model is not an LLM in the sense that matters here: it is a deterministic encoder that turns text into the same vector every time, with no generation, no sampling, no prompt. The no-LLM-at-runtime rule holds, and the honest 501 became an honest 200.

You can hit it now: [`/api/milestones/arch-0.3-bge/search`](https://retail-search.feroshjacob.workers.dev/api/milestones/arch-0.3-bge/search?q=transonic+aileron+buzz).

## ARCH-0.5: The First Architecture Chosen by Evidence

Every earlier architecture in this project was chosen from one dataset. ARCH-0.5 is the first chosen from many, and the classification decides its shape:

- **Core (runs live, by default):** BM25 lexical retrieval, and the BGE hybrid on top — the only Universal technique.
- **Portfolio (evidenced, activatable, not default):** learning-to-rank, refined PRF, coverage rerank, and document expansion. Each helped somewhere; none earned "everywhere."
- **Dormant (backlogged, re-testable):** cross-encoder reranking, SPLADE, ColBERT, HyDE — deferred on compute or infrastructure, not rejected.

Nothing was thrown away. That is the Superset-not-Filter policy made concrete: the portfolio and even the dormant list travel to Phase 3 carrying their evidence, so when the agent meets retail product data it is matching a new domain against a map of everything it has already measured — not guessing.

## What This Cost, Honestly

Two things a reader deserves, both of which the mission would rather I state than hide.

**"Universal" has a boundary.** It means all six datasets where full-corpus dense evaluation fits on a free-tier box — Cranfield and the five smallest BEIR sets. The larger corpora were evaluated for BM25 live but for dense retrieval only offline or on subsamples, because a 4 GB instance cannot hold a live vector index past roughly half a million documents. The BGE hybrid is Universal *within the scope the hardware allowed*, and that scope is named, not blurred.

**The backend fell over mid-phase.** The managed OpenSearch instance was reclaimed during a stretch of inactivity — the endpoint simply stopped resolving. Recovery meant standing up a fresh instance, re-pointing the Worker's secrets, and reloading every index. The Cranfield BGE index was reconstructed to 1,388 of 1,400 documents from cached retrieval pools; the 12 missing were documents no query had ever retrieved, and parity checks confirm no measurable effect. I mention it because "it ran on infrastructure that survived being deleted" is part of the honest reproducibility story, not a footnote to bury.

## What Phase 3 Inherits

The agent walks into retail with something it did not have six days ago: a technique portfolio with a domain map attached to every entry. It knows that keyword reranking helps scientific prose and hurts financial questions. It knows that dense retrieval is its one portable win, largest exactly where vocabulary drifts. It knows its fanciest ranker is a tie-breaker waiting for behavioral data.

Phase 3 is [Amazon ESCI](https://github.com/amazon-science/esci-data) — real products, with titles, brands, colors, attributes, prices, and relevance labels where the substitute is sometimes the better answer than the exact match. The documents stop being flat text. The question stops being "is this relevant" and becomes "which relevant product comes first" — the question [Part 1](/posts/2026/07/02/self-learning-agent-based-retail-search-part-1-why-retail-search-is-harder-than-it-looks) opened the whole series with.

For the first time, the agent will be searching something you could actually buy.

## Takeaways

1. **A win on one dataset is a hypothesis, not a result.** The agent's keyword ceiling-breaker was real on aeronautics and wrong on finance. You only learn which by testing across domains.
2. **Classify, don't filter.** The techniques that failed to generalize are not garbage — they are domain-conditional, and their failures are a map. The one that hurt finance might be the one retail loves.
3. **"Reproduces the published number" hides a clause.** Name the evaluation convention *and* the scorer. FEVER's 13% gap was neither a bug nor a mystery; it was a Lucene-versus-OpenSearch scoring difference wearing a mystery's clothes.
4. **Simpler wins ties.** The best-engineered technique lost the core to a simpler one it could only match. That is Occam doing his job, not a failure of the model.
5. **An honest 501 can become an honest 200.** Shipping the gap instead of faking the demo is what made the eventual payoff mean something.

Phase 1 proved the method on data that could not lie. Phase 2 found out what the method actually learned. Phase 3 hands the agent products.

## References

Every reproduction figure here is measured on this project's own free-tier stack; every comparison target is a published number, cited below. Phase 2 experiment artifacts and the architecture ledger are public in the [project repository](https://github.com/Northvalley-Intelligence/retail-search).

[^beir]: Thakur, N., Reimers, N., Rücklé, A., Srivastava, A., & Gurevych, I. (2021). [BEIR: A Heterogenous Benchmark for Zero-shot Evaluation of Information Retrieval Models](https://arxiv.org/abs/2104.08663). *NeurIPS 2021 Datasets and Benchmarks Track.* arXiv:2104.08663. Source of the published BM25 nDCG@10 baselines used as reproduction targets.

[^bge]: Xiao, S., Liu, Z., Zhang, P., & Muennighoff, N. (2023). [C-Pack: Packaged Resources To Advance General Chinese Embedding](https://arxiv.org/abs/2309.07597). arXiv:2309.07597. The BGE family, including `bge-base-en-v1.5`, the retrieval-trained encoder used for dense retrieval here and served at runtime via Cloudflare Workers AI.
