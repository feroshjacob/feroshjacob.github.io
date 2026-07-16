---
title: "Self-Learning Agent-Based Retail Search, Part 2: The Baseline Before the Agents"
date: 2026-07-06
permalink: /posts/2026/07/06/self-learning-agent-based-retail-search-part-2-the-baseline-before-the-agents
categories:
  - self-learning-agent-based-retail-search
series: self-learning-agent-based-retail-search
series_order: 2
phase: phase-1
tags:
  - retail search
  - ecommerce search
  - search relevance
  - opensearch
  - cranfield
  - information retrieval
  - search evaluation
  - ai agents
  - mission-driven engineering
image: /images/self-learning-agent-based-retail-search-part-2-cranfield-baseline.png
excerpt: "Before search agents tune anything, the system needs a measurable baseline. This article shows Phase 1 of the project: an out-of-box OpenSearch BM25 baseline on Cranfield, the live relevance numbers, and the failures agents will need to improve."
---

![Cranfield documents and queries flowing into a Phase 1 OpenSearch BM25 baseline, with evaluation metrics and failed query cases](/images/self-learning-agent-based-retail-search-part-2-cranfield-baseline.png)

In the first article, I argued that retail search is harder than it looks because ranking is not just a relevance problem. It is also a customer-experience problem, a business-goal problem, an inventory problem, and a trust problem.

That raises an obvious question:

> If agents are going to improve retail search, what do they improve first?

My answer is deliberately unglamorous.

Before agents tune analyzers, generate synonyms, rewrite queries, add semantic retrieval, or build learning-to-rank layers, they need a baseline. Not a slide. Not a claim. A measurable search system, running through a production-shaped path, with relevance numbers that can go up or down.

This article is that baseline.

More specifically, this is Phase 1 of the project: a Cranfield foundation running through OpenSearch BM25, live metrics, and visible failure cases before any search-agent tuning begins.

## The Short Version

I built a live Phase 1 Cranfield search baseline using OpenSearch behind a Cloudflare Worker:

- Live demo: [retail-search.feroshjacob.workers.dev](https://retail-search.feroshjacob.workers.dev/)
- GitHub repo: [Northvalley-Intelligence/retail-search](https://github.com/Northvalley-Intelligence/retail-search)
- Dataset: [Cranfield experiments](https://en.wikipedia.org/wiki/Cranfield_experiments)
- Search engine: OpenSearch BM25
- Query path: Cloudflare Worker to OpenSearch
- Documents indexed: 1,400
- Evaluation queries: 225
- Relevance judgments: 1,837

The current live Phase 1 baseline over the 225 Cranfield queries is:

| Metric | Value |
| --- | ---: |
| MAP | 0.2402 |
| nDCG@10 | 0.2995 |
| Precision@10 | 0.2316 |
| Recall@10 | 0.3994 |
| MRR | 0.5350 |

No search-agent tuning has been applied yet. This is the out-of-box OpenSearch BM25 baseline with simple field weights.

That matters because the next useful question is not "Can an agent make search better?" The next useful question is:

> Can an agent look at measured failures, propose a specific OpenSearch-native change, and improve the same evaluation gate without making something else worse?

## Why Start With Cranfield?

Cranfield is not a retail dataset. It is a classic information retrieval benchmark built around aeronautics documents, queries, and relevance judgments.

That is exactly why I wanted to start there.

For Phase 1, I do not want the agents arguing about merchandising, inventory, personalization, price, promotions, or product availability yet. Those are the hard retail problems, and we will get to them. But first I want the system to prove that it can run a measurable search experiment end to end.

Cranfield gives us the basic ingredients:

- documents to index
- queries to run
- relevance judgments to score against
- enough failures to analyze
- a small enough dataset to iterate quickly

It is a controlled foundation. Later phases can move toward retail datasets such as Amazon ESCI, where the ranking problem becomes more product-like. But if the system cannot learn from Cranfield, it is not ready to claim anything about retail search.

## Why OpenSearch?

OpenSearch is not the final architecture. It is the baseline execution engine.

I chose it for this phase because it is a real deployable search engine. It supports BM25, analyzers, mappings, field weights, filters, and API-backed retrieval. It can sit behind a Cloudflare Worker and behave like a production service instead of a notebook experiment.

That is important for the agent work.

If agents only improve an offline script, it is hard to know whether the improvement survives contact with an actual search API. If agents improve a live OpenSearch path, the system has to respect the same constraints a real search service would face: request handling, index mappings, query generation, ranking behavior, latency, and repeatable evaluation.

The baseline currently searches across:

- `title^3`
- `abstract^2`
- `text`

It also applies a `dataset: cranfield` filter and uses the OpenSearch English analyzer.

That is intentionally simple.

## What The Live System Does

The runtime path is:

```text
Cranfield query
  -> Cloudflare Worker
  -> normalize whitespace
  -> OpenSearch multi_match query
  -> cranfield-v0 index
  -> BM25 ranking
  -> JSON search, explain, and evaluation responses
```

The production site exposes the baseline as a small search application:

- Phase overview: [retail-search.feroshjacob.workers.dev/phases/cranfield](https://retail-search.feroshjacob.workers.dev/phases/cranfield)
- Search UI: [retail-search.feroshjacob.workers.dev/phases/cranfield/search](https://retail-search.feroshjacob.workers.dev/phases/cranfield/search)
- Data page: [retail-search.feroshjacob.workers.dev/phases/cranfield/data](https://retail-search.feroshjacob.workers.dev/phases/cranfield/data)
- Explain flow: [retail-search.feroshjacob.workers.dev/phases/cranfield/explain](https://retail-search.feroshjacob.workers.dev/phases/cranfield/explain)
- Evaluation page: [retail-search.feroshjacob.workers.dev/phases/cranfield/evaluation](https://retail-search.feroshjacob.workers.dev/phases/cranfield/evaluation)

The API also exposes search and explain routes:

- `/api/cranfield/search?q=...`
- `/api/cranfield/explain?q=...`
- `/api/cranfield/evaluation`
- `/api/search`
- `/api/explain`

This is not a full retail search product yet. It is the first measurable layer.

## The Baseline Is Useful Because It Fails

The baseline is not impressive because the numbers are high. The baseline is useful because the numbers are honest.

MAP is 0.2402. nDCG@10 is 0.2995. Precision@10 is 0.2316. Recall@10 is 0.3994.

Those numbers say the system can retrieve useful results, but it leaves a lot on the table. That is what we want. A perfect baseline would be a terrible starting point for a self-learning search project. A weak but measurable baseline gives agents something concrete to improve.

More importantly, Cranfield exposes failure cases where the current BM25 ranking looks plausible to a human skimming titles but still scores zero against the benchmark judgments.

That tension is the interesting part.

Search engineering is full of examples where "this result looks reasonable" and "this result is judged relevant" are not the same thing. The agent's job is not to blindly make the result look better to me. The agent's job is to propose changes that survive the evaluation.

## Example Failures The Agents Need To Study

Several Cranfield queries currently score zero in the top 10.

The collapsed expected-document lists below come from the Cranfield relevance judgments. They show what the benchmark considered relevant, even when the current BM25 top 10 missed them. Where the public search API could resolve the title, I include it so the expected answer is easier to inspect.

For [query 13](https://retail-search.feroshjacob.workers.dev/phases/cranfield/search?q=what%20is%20the%20basic%20mechanism%20of%20the%20transonic%20aileron%20buzz), "what is the basic mechanism of the transonic aileron buzz," the baseline returns a title that sounds almost perfect: "a theory of transonic aileron buzz, neglecting viscous effects." But the benchmark judges different documents as relevant.

<details>
  <summary>Expected benchmark documents for query 13</summary>
  <ul>
    <li>Document 65, relevance grade 4: "convection of a pattern of vorticity through a shock wave."</li>
    <li>Document 311, relevance grade 4</li>
    <li>Document 64, relevance grade 2: "unsteady oblique interaction of a shock wave with plane disturbances."</li>
    <li>Document 265, relevance grade 2: "some instabilities arising from the interaction between shock waves and boundary layer."</li>
  </ul>
</details>

For [query 22](https://retail-search.feroshjacob.workers.dev/phases/cranfield/search?q=did%20anyone%20else%20discover%20that%20the%20turbulent%20skin%20friction%20is%20not%20over%20sensitive%20to%20the%20nature%20of%20the%20variation%20of%20the%20viscosity%20with%20temperature), the baseline chases repeated surface terms around turbulent skin friction, while the judged answer is broader.

<details>
  <summary>Expected benchmark documents for query 22</summary>
  <ul>
    <li>Document 68, relevance grade 1: "some aspects of air-helium simulation and hypersonic approximations."</li>
  </ul>
</details>

For [query 28](https://retail-search.feroshjacob.workers.dev/phases/cranfield/search?q=what%20application%20has%20the%20linear%20theory%20design%20of%20curved%20wings), it returns generic linearized wing theory and curved plate matches, missing the application-oriented curved-wing documents.

<details>
  <summary>Expected benchmark documents for query 28</summary>
  <ul>
    <li>Document 224, relevance grade 3: expected application-oriented curved-wing document.</li>
    <li>Document 279, relevance grade 3: "supersonic drag calculations for a cylindrical shell wing of semicircular cross section combined with a central body of revolution."</li>
  </ul>
</details>

For [query 31](https://retail-search.feroshjacob.workers.dev/phases/cranfield/search?q=what%20size%20of%20end%20plate%20can%20be%20safely%20used%20to%20simulate%20two-dimensional%20flow%20conditions%20over%20a%20bluff%20cylindrical%20body%20of%20finite%20aspect%20ratio), the top result sounds close because it mentions end plates and bluff cylinders, but the judged relevant document is about force measurements on square and dodecagonal cylinders.

<details>
  <summary>Expected benchmark documents for query 31</summary>
  <ul>
    <li>Document 776, relevance grade 4: "force measurements on square and dodecagonal sectional cylinders at high reynolds numbers."</li>
  </ul>
</details>

For [query 38](https://retail-search.feroshjacob.workers.dev/phases/cranfield/search?q=does%20transition%20in%20the%20hypersonic%20wake%20depend%20on%20body%20geometry%20and%20size), the returned documents are semantically plausible, but the system still misses every judged-relevant document in the top 10.

<details>
  <summary>Expected benchmark documents for query 38</summary>
  <ul>
    <li>Document 557, relevance grade 4: "a numerical comparison between exact and approximate theories of hypersonic inviscid flow past slender blunt nosed bodies."</li>
    <li>Document 558, relevance grade 4: "experimental measurements of turbulent transition motion, statistics and gross radial growth behind hypervelocity object."</li>
    <li>Document 272, relevance grade 3: "oscillatory aerodynamic coefficients for a unified supersonic hypersonic strip theory."</li>
    <li>Document 24, relevance grade 2</li>
    <li>Document 283, relevance grade 2</li>
  </ul>
</details>

These are not embarrassing failures. They are the raw material for the next phase.

They give agents something specific to inspect:

- Is this a vocabulary mismatch?
- Is this a query-intent problem?
- Is this a field-weighting problem?
- Is this an analyzer problem?
- Would synonym expansion help, or would it damage other queries?
- Can a query rewrite improve nDCG without hurting MAP?
- Can the system explain why a title-similar document is not judged relevant?

Now the project has a loop: inspect failures, propose a change, run the same evaluation, and keep the change only if the evidence improves.

## What Agents Are Not Allowed To Do Yet

The current search path does not use runtime LLM calls.

That constraint is intentional.

The next step is not to put a model in the request path and hope intelligence appears. The next step is to let agents work offline as search engineers. They can inspect broken queries, analyze failure modes, propose OpenSearch-native interventions, and update configuration or query logic. Then the same 225-query evaluation decides whether the change helped.

The early agent tasks should be boring in the best way:

1. Classify zero-score queries by failure mode.
2. Propose a small OpenSearch-native change.
3. Predict which metrics should improve.
4. Run the evaluation.
5. Compare MAP, nDCG@10, Precision@10, Recall@10, MRR, and latency.
6. Keep or reject the change based on evidence.

That is the difference between "AI in search" as a slogan and agents doing useful search engineering.

## Why This Matters For Retail Search

Cranfield is not retail, but the discipline transfers.

Retail search has more objectives than Cranfield. A retail system has to balance relevance, availability, margin, price, promotions, ratings, personalization, and trust. That makes it tempting to start by designing a large architecture: query understanding, synonyms, embeddings, ranking models, behavior signals, dashboards, and merchandising rules.

But if every component is added before the baseline is measured, the system becomes hard to reason about.

This project is taking the opposite route.

Start with a simple baseline. Measure it. Expose the failures. Make every new component earn its place.

OpenSearch BM25 is not the destination. It is the first scoreboard.

## The Next Article

Now that failures are measurable, the next step is to ask agents to improve the system.

Not vaguely. Not by saying "make search smarter."

The next article will start from the broken Cranfield queries and ask agents to propose the first interventions. Synonyms, query expansion, field weighting, analyzer changes, and explanation tooling are all candidates. But each proposal has to pass through the same gate:

> Did the search system get measurably better, and can we explain why?

That is where self-learning search starts to become real.
