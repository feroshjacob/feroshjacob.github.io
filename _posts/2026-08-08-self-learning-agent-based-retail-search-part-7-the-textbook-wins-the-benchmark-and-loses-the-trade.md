---
title: "SLAB-RS, Part 7: The Textbook Wins the Benchmark, and Loses the Trade"
date: 2026-08-08
permalink: /posts/2026/08/08/self-learning-agent-based-retail-search-part-7-the-textbook-wins-the-benchmark-and-loses-the-trade
categories:
  - self-learning-agent-based-retail-search
series: self-learning-agent-based-retail-search
series_order: 8
phase: phase-3
tags:
  - retail search
  - amazon esci
  - learning to rank
  - field weighting
  - query understanding
  - product search
  - information retrieval
  - ai agents
  - mission-driven engineering
image: /images/self-learning-agent-based-retail-search-part-7-the-trade.png
excerpt: "Retail is the first dataset in the project with real graded labels — so it is the first place the classical retail-search toolkit gets a fair audition. Field weighting stayed dormant. Learning-to-rank finally woke up, and then relearned the hybrid it was supposed to beat. The full textbook cascade won the benchmark — by +0.13% over the lean stack that was already shipped. Measure everything, ship almost nothing."
---

![The trade, side by side: an elaborate textbook pipeline (query understanding features feeding a retrained learning-to-rank model) reaching nDCG 0.8539, next to a lean stack (BM25 + BGE hybrid plus one negation fix) reaching 0.8528 — the two summits almost level, the gap marked +0.13%, and the agent standing on the lean stack](/images/self-learning-agent-based-retail-search-part-7-the-trade.png)

[Part 6](/posts/2026/08/02/self-learning-agent-based-retail-search-part-6-the-agent-meets-a-million-real-products) established that the lean academic stack — BM25 blended with BGE embeddings — reaches parity with the published baseline on 1.2 million real products, inside the phase's two rules: one free-tier box, 700 ms per query. That is the floor the rest of Phase 3 has to beat.

And retail hands the agent something no dataset before it did: **real graded relevance labels**, assigned by humans, at scale.[^esci] That matters for a specific reason. Through two phases, a whole toolkit of classical retail-search techniques sat unused — field weighting, learning-to-rank, query understanding — not because they were rejected, but because there were no real labels to train or tune them against. ESCI removes that excuse. This is their first fair audition. The question is simple: **does any of the textbook earn its keep?**

## The Short Version

The honest answer is *measure everything, ship almost nothing.*

- **Field weighting** — the single most canonical retail-search lever — is **dormant**: tuned by cross-validation it scores 0.8237, *below* the 0.8252 default. The textbook's first move makes things slightly worse.
- **Learning-to-rank reactivates** for the first time in the entire project: with real labels it finally beats the fixed hybrid, 0.849 to 0.8472. Then you read the weights it learned and find it rebuilt the hybrid almost exactly. A +0.2% win that is inside the noise, for a technique that rediscovered what already ships.
- **The full classical cascade** — query-understanding signals fed as features into a retrained ranker — is the **single best configuration measured, 0.8539.** The textbook, run end to end, genuinely wins the benchmark.
- And it beats the lean stack already in production by **+0.13%.** A complete training pipeline, to gain eleven ten-thousandths of an nDCG point.

The cascade is the ceiling. The lean stack is already touching it. You do not ship a training loop to imitate a result you have for free.

## Field Weighting: The Canonical Lever That Does Nothing

If you asked any search engineer for the first retail-search improvement to try, most would say field weighting — boost the title, weight the brand, tune how much the description counts. It is the textbook's opening move. On ESCI, cross-validated over the evaluation queries, it scored **0.8237 against the 0.8252 default** — very slightly *worse*.

The reason is mechanical and worth knowing, because it is a trap. The default is OpenSearch `best_fields`, which scores a document by its single strongest-matching field and adds only a fraction of the rest (a tie-breaker). Learned field weighting, as tested here, pushed toward an additive sum across fields — and summing lets several weak, partial field matches outvote one strong, coherent one. For products, where the title is usually the one field that matters, "best field plus a tie-breaker" is simply a better model of relevance than "add everything up." The canonical lever is not just unhelpful here; it points the wrong way.

## Learning-to-Rank Wakes Up — and Relearns the Hybrid

Now the interesting result. In [Phase 2](/posts/2026/07/26/self-learning-agent-based-retail-search-part-5-how-the-agents-discovered-hybrid-search), learning-to-rank was classified *portfolio, not core*: it beat plain BM25 everywhere but only tied the hybrid, so by the project's Occam rule it did not ship. The standing hypothesis was that LTR was dormant **for lack of real labels**, not for lack of value — that given genuine graded relevance it would come alive.

It did. Trained with query-grouped cross-validation on ESCI's real labels, LTR scored **0.849, beating the fixed hybrid's 0.8472** — the first time in the entire project that learning-to-rank beat the shipped configuration. The hypothesis was right.

Then you look at the weights it learned:

| Feature | Learned weight |
| --- | ---: |
| BGE dense score | 3.6 |
| BM25 score | 1.5 |
| title field | 0.0 |
| color field | 0.4 |
| brand field | 0.25 |
| bullets / description | 0.1 |

Handed every structured field and a free hand to weight them, the model leaned almost entirely on the two signals that already constitute the hybrid — BGE and BM25 — and set the field weights to near-zero, the title literally to zero. It did not discover a better ranking function. **It rediscovered the hybrid**, and its +0.2% edge is comfortably inside cross-validation noise. A technique that reactivates only to rebuild the thing it was meant to beat has proved the hypothesis and failed the audition in the same breath. Unpromoted.

## The Classical Cascade: The Textbook, Validated

There is one more configuration, and it is the one the textbook actually prescribes: **query understanding → retrieval → learning-to-rank.** Don't just hand LTR the retrieval scores — hand it *query-understanding signals* too, as features, and retrain. Specifically, two signals the agent built for the failure analysis in the next part: whether a product violates a negation in the query, and whether it conflicts on a stated attribute.

This is the best number in all of Phase 3: **0.8539.** And unlike field weighting, LTR genuinely *values* the new features — the learned weights put negation-violation at 2.8 and attribute-conflict at 3.4, right alongside BGE at 3.4. The cascade is not noise. The textbook, run end to end on real labels, is validated: it is the ceiling.

## …and Loses the Trade

Here is the scoreboard, every configuration cross-validated on the same evaluation queries:

| Configuration | nDCG@10[^ndcg] | |
| --- | ---: | --- |
| BM25 (structured fields) | 0.8252 | the floor |
| Fixed BM25 + BGE hybrid | 0.8472 | Part 6's parity result |
| Learning-to-rank (dense + lexical) | 0.849 | relearns the hybrid |
| **Hybrid + negation fix** | **0.8528** | **the lean stack, shipped** |
| Classical cascade (QU → LTR) | 0.8539 | the textbook ceiling |

Read the bottom two rows together, because they are the whole point. The full classical cascade — a feature pipeline, a trained model, and the retraining loop to keep it fresh — reaches 0.8539. The lean production stack — the same hybrid, plus a single hand-built negation fix that is the subject of the next part — sits at 0.8528. The cascade's entire advantage over the thing already running in production is **+0.0011 nDCG, or 0.13%.**

That is the trade. To bank a tenth of a percent, you take on a training pipeline: labeled data to maintain, a model that drifts, a retraining schedule, and a feature store — all of it forever, on a project whose first rule is one free-tier box. The textbook wins the benchmark. It loses the trade badly. The lean stack was already at the ceiling for free.

## Measure Everything, Ship Almost Nothing

This is the discipline that has governed every promotion decision in the project, stated plainly here because retail is where it bites hardest. The value of an audition is not what you ship out of it. Most of the time you ship nothing. The value is knowing, with evidence, where the ceiling is — and whether you are already at it.

Phase 2 demoted LTR because it only tied the hybrid. Phase 3 gave LTR real labels, watched it wake up exactly as predicted, and demoted it again — this time for winning by too little to be worth its cost. Same rule, applied twice, to a technique that got stronger in between. That consistency is the point. A method that says yes to everything that improves a number would have shipped the training pipeline for its 0.13% and called it progress. This one measured the whole textbook, learned exactly where the ceiling sits, confirmed the lean stack is already there, and shipped none of it.

## Who Did What

As in every phase: the operator kicked off the audition and approved (or declined) each promotion; the agent built the field-weighting experiment, trained the rankers, ran the cross-validation, and read the learned weights back. The human set the Occam rule and made the ship/no-ship calls. The agent produced the evidence those calls were made on.

## What's Next

Almost nothing shipped from the textbook. But one thing did ship in Phase 3 — the negation fix sitting at 0.8528 in that scoreboard — and it did not come from a bigger model or a training loop. It came from reading a thousand failures and noticing a pattern about language that no embedding encodes. That is the next part, and it is the one where the agent actually wins.

## Takeaways

1. **Real labels were the missing ingredient, exactly as predicted — and it still wasn't enough.** LTR reactivated on ESCI's graded labels, vindicating a two-phase-old hypothesis, then relearned the hybrid and won by noise.
2. **The canonical lever can point the wrong way.** Field weighting, the textbook's first move, scored *below* the default — because additive field-sum is a worse model of product relevance than best-field-plus-tie-breaker.
3. **The textbook is the ceiling, not the product.** The full cascade is genuinely the best configuration measured. It beats the shipped stack by 0.13%, which is not worth a training pipeline on a free-tier box.
4. **Measure everything, ship almost nothing.** The point of the audition was to learn where the ceiling is and confirm the lean stack is already at it. Knowing what *not* to build is the return on measuring it.

Phase 1 proved the method. Phase 2 found out what it learned. Phase 3 — retail — is where the toolkit finally got its fair hearing, and mostly earned a polite no.

## References

Reproduction figures are measured on this project's own free-tier stack, cross-validated on the ESCI evaluation queries. Phase 3 experiment artifacts and the architecture ledger are public in the [project repository](https://github.com/Northvalley-Intelligence/retail-search).

[^esci]: Reddy, C. K., Màrquez, L., Valero, F., Rao, N., Zaragoza, H., Bandyopadhyay, S., Biswas, A., Xing, A., & Subbian, K. (2022). [Shopping Queries Dataset: A Large-Scale ESCI Benchmark for Improving Product Search](https://arxiv.org/abs/2206.06588). arXiv:2206.06588. Source of the products, the graded Exact/Substitute/Complement/Irrelevant labels, and the official gain mapping used to score every configuration here.

[^ndcg]: Järvelin, K., & Kekäläinen, J. (2002). [Cumulated gain-based evaluation of IR techniques](https://doi.org/10.1145/582415.582418). *ACM Transactions on Information Systems*, 20(4), 422–446. nDCG@10 with the ESCI graded gain mapping is the metric throughout; all comparisons are cross-validated on the evaluation query subsample.
