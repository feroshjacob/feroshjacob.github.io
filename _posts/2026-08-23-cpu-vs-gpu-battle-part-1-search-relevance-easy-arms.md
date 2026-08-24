---
title: "CPU vs GPU battle, Round 1: the agents brought the GPU"
date: 2026-08-23
permalink: /posts/2026/08/23/cpu-vs-gpu-battle-part-1-search-relevance-easy-arms
categories:
  - cpu-vs-gpu-battle
series: cpu-vs-gpu-battle
series_order: 1
tags:
  - search-relevance
  - llm-as-judge
  - bm25
  - evaluation
  - on-device
image: /images/cpu-vs-gpu-battle-part-1.png
excerpt: "How well does a small prompted LLM grade e-commerce search relevance on a 0-3 scale, entirely on a laptop? We start the CPU-vs-GPU battle with the two easy arms - BM25 and a prompted local model - and find the honest surprise is not accuracy but calibration and circularity."
---

![CPU vs GPU Battle, Round 1: the agents brought the GPU — a face-off between the veteran CPU and a newcomer GPU wheeled into the ring by a coding agent](/images/cpu-vs-gpu-battle-part-1.png)

## The Short Version

We want to grade how relevant a product is to a search query, on a 0-3 scale, for
an e-commerce search team. This is Round 1 of an open-ended series we call **CPU vs
GPU battle**: take a real problem, solve it first the easy, CPU-friendly way, and keep
an honest scorecard - a different problem each round, the same tension every time.

This round covers the two things you would reach for first, both of which run
entirely on a laptop with no cloud API:

- **A lexical baseline (BM25).** Instant, tiny, and a genuinely useful floor.
- **A prompted local LLM judge** (`llama3.2:3b` via Ollama) - which is essentially
  what our relevance service already does on-device today.

Both are cheap and neither is embarrassing. The prompted model is the better
grader of the two. But the interesting finding of this round is not a leaderboard - it
is two traps that anyone doing this work will hit: the model is systematically
*miscalibrated* (which makes raw agreement understate it), and the "ground truth"
we were tempted to grade against was **92% produced by the same model we were
testing** - so agreeing with it measures reproduction, not accuracy.

Code and full method: [github.com/Northvalley-Intelligence/cortex — experiments/relevancy-replacement](https://github.com/Northvalley-Intelligence/cortex/tree/main/experiments/relevancy-replacement) (the method
and the input/output contract are written out in full below, so this round stands
on its own without the link).

---

## The Problem: turning "is this relevant?" into a number

An e-commerce search team lives or dies by one question, asked millions of times a
day: *for this query, is this product any good?* You need the answer as a **number** -
three systems consume it: **ranking** (a score per candidate), **evaluation** (graded
judgments for metrics like NDCG), and **guardrails** (a cheap flag for "this does not
belong here").

So we grade each `(query, product)` pair on a 4-point ordinal scale - the exact scale
the relevance service uses, and the single source of truth every approach here is
measured against:

| Grade | Meaning |
|---|---|
| **0** | Irrelevant - no match to the intent |
| **1** | Marginal - same broad category, doesn't satisfy the intent |
| **2** | Relevant - satisfies the intent, not the ideal match |
| **3** | Perfect - exactly what the searcher wanted |

The hard part is the **1-vs-2 gap**: a phone case for the query "phone" is
category-adjacent but doesn't satisfy the intent - a 1, not a 0 or a 2. Most of the
difficulty lives there.

The contract is deliberately minimal - query and title in, an integer out:

```
INPUT   { query, product_title }
OUTPUT  integer grade in {0, 1, 2, 3}
```

The judge prompt is two lines, so query plus title alone is enough to grade. (An
unparseable model response is surfaced as a failure, never silently coerced to 0 - a
detail that bites us later.)

---

## It's easy, and it's not bad

Standing this up is genuinely a weekend's work. You pull a small instruction-tuned
model with Ollama, write the two-line prompt plus the rubric above, and you have a
relevance grader running on your own machine with no API key and no per-call bill.

And on the easy pairs it behaves the way you would hope. These are the *kinds* of
pairs it handles well (illustrative, generic - no client data):

| Search term | Product title | Sensible grade |
|---|---|---|
| `air buds` | Samsung Galaxy Buds Plus, True Wireless Earbuds | **3** - exactly the intent |
| `insulated water bottle` | 24 oz Stainless Steel Vacuum Insulated Water Bottle | **3** - a direct match |
| `ergonomic desk` | Stand Up Desk Converter, Ergonomic Tabletop Riser | **2** - satisfies intent, not the ideal item |
| `office chair` | 24 oz Stainless Steel Water Bottle | **0** - wrong intent entirely |

> Sourcing note: the query/title shapes above are drawn from public evaluation
> data and a live smoke-test of the service; the grades shown are illustrative of
> how the rubric is *meant* to read, not a per-pair transcript of one model's
> output. The hard evidence for "not bad" is the aggregate score below, not any
> single row.

Across a held-out sample of 3,000 Amazon ESCI pairs, the prompted `llama3.2:3b`
judge lands within one grade of the reference label **77% of the time** (its mean
absolute error is about **0.95** of a grade). For a 3B model running on a laptop,
grading e-commerce relevance it was never trained on, that is a reasonable place to
start. It is also, notably, better on ordinal agreement than the pure lexical
baseline - more on that in the scorecard.

So far, so encouraging. Then you look at where it disagrees with the labels, and
the story gets more interesting.

---

## Then it misclassifies - and that's the interesting part

Look at where the prompted model disagrees with the independent labels and a pattern
emerges. Here are three real held-out cases - the query, the product, the human gold
grade, and the grade the current approach gave (✗ marks a miss):

| Query | Product title | Gold | Current (prompted LLM) |
|---|---|---|---|
| `the not a cat cat` | *Think Like a Cat* (a cat-care book) | 0 · Irrelevant | **3 · Perfect** ✗ |
| `iphone 8 screen protector not plus` | Ailun screen protector for iPhone 8 (4.7") | 3 · Perfect | **1 · Marginal** ✗ |
| `lg uhd tv 75 un70` | TCL 4K UHD Roku Smart TV (a substitute) | 2 · Relevant | **0 · Irrelevant** ✗ |

The current approach fails in **two opposite directions**:

- **It overgrades on word overlap** (row 1): a book whose title contains "cat" scores
  *Perfect* for a query about a cat. The model latched onto the shared word - off by
  three whole grades on something a human dismisses instantly.
- **It undergrades real matches** (rows 2-3): a protector that fits the iPhone 8 is
  called *Marginal* because the model over-weighted "not plus"; a valid cross-brand TV
  substitute is called *Irrelevant*. Both are genuine matches it was too harsh on -
  and negation queries ("without", "not plus") are a recurring weak spot.

These are not cherry-picked oddities - they are the everyday failure surface of the
easy approach: word-overlap false positives and negation-driven false negatives, over
and over. This round stays on the arms you would actually reach for first, and on being
honest about where they break.

### Effect 1: it's mostly a calibration problem

Look at the two easy arms side by side on the held-out ESCI sample:

| Approach | Exact-match accuracy | Ordinal agreement (QWK) |
|---|---|---|
| BM25 lexical | **0.401** | 0.222 |
| Prompted `llama3.2:3b` | 0.317 | **0.288** |

The prompted model gets *fewer* grades exactly right than a bag-of-words baseline, yet
scores meaningfully **higher** on Quadratic Weighted Kappa - the metric that rewards
being ordinally close and punishes being far off. That combination points at a
*systematic* problem rather than random noise: most of its errors are near-misses that
land next to the right grade (which is why QWK beats BM25 despite the lower exact
accuracy), even though the word-overlap traps like the cat book show it can still miss
badly. Near-miss, systematic error is a *calibration* problem - the kind of error that is
fixable in principle, not a sign the model fails to understand. **Low raw agreement
does not mean a bad model; it often means an uncalibrated one.**

(Why QWK and not accuracy as the headline? On an ordinal scale, calling a 3 a 2 is
a small slip and calling a 3 a 0 is a disaster - plain accuracy scores them
identically. QWK weights the miss by the square of the distance, which is what you
actually care about for ranking and guardrails.)

### Effect 2: the circularity trap - don't grade a model against itself

The bigger, more dangerous trap is about *what you grade against*. We also had
4,576 of our own past relevance judgments sitting in a database, and it was
tempting to treat those as "gold" and measure each approach against them.

Then we checked where those judgments came from: **about 92% of them were produced by
the same local `llama3.2:3b` model we were now testing.** On that set the prompted model
"agrees with the gold" enormously well - but that is just the model reproducing its own
cached grades at `temperature: 0`. It is self-consistency, not accuracy. Grading a model
against labels it largely authored tells you whether it is reproducible, not whether it
is right.

The fix is to insist on *independent* labels. Every accuracy number here is measured
against human-derived datasets the models never authored:

- **Amazon ESCI** - real query/product relevance labels (Exact / Substitute /
  Complement / Irrelevant, mapped to 3 / 2 / 1 / 0).
- **WANDS** - furniture search relevance, used held-out only as an
  out-of-domain generalization test (Exact / Partial / Irrelevant, mapped to
  3 / 2 / 0).

Our own historical judgments are kept only to *illustrate* this trap - never as the
accuracy verdict. If you take one thing from this round: the labels you are auditing
are not automatically ground truth, and if your model helped write them, agreement
is a mirror.

---

## The scorecard for the two easy arms

Both arms run entirely on the laptop, no API. Here is the honest read on each.

### Arm 1 - BM25 (the lexical floor)

Classic bag-of-words scoring: how much do the query's terms overlap the title,
weighted by term rarity. Thresholds were calibrated on ESCI-train only, then frozen.

| Dataset | QWK | Exact acc | Speed | Memory |
|---|---|---|---|---|
| ESCI (held-out) | 0.222 | 0.401 | instant | tiny |
| WANDS (held-out) | 0.155 | 0.377 | instant | tiny |

Where it wins: nothing to train, nothing to load, effectively free, and a real
floor to beat. Where it loses: lexical overlap is not intent. It does worst on
WANDS furniture, where the right product often shares few words with the query.
Keep it as the baseline; it is not a contender.

### Arm 2 - prompted `llama3.2:3b` (what we run on-device today)

The two-line prompt plus the rubric, one model call per pair, on Ollama.

| Dataset | QWK | Exact acc | Speed | Memory |
|---|---|---|---|---|
| ESCI (held-out) | 0.288 | 0.317 | ~1-5 s / pair | ~5-6 GB |
| WANDS (held-out) | 0.244 | 0.310 | ~1-5 s / pair | ~5-6 GB |

Where it wins: better ordinal agreement than BM25 on both datasets, no training
required, and it produces a human-readable reason alongside each grade. Where it
loses: it is the slowest on-device option (seconds per pair with the full rubric
prompt), it is heavy (the model weights sit at 5-6 GB of RAM), it occasionally
emits JSON we cannot parse (about 0.2% of pairs on ESCI - remember that parsing
detail), and it is miscalibrated as shown above.

---

## Honest caveats (they travel with every round)

Credibility is the point, so the fine print is not buried:

- **Absolute scores are modest.** Pointwise relevance grading is genuinely hard -
  even the better of the two easy arms lands well short of a perfect grader. Treat
  these as a floor to beat, not a solved problem.
- **Compare arms within a dataset, never across.** WANDS numbers look higher than
  ESCI for the same arm partly because WANDS is a coarser 3-class label space (no
  grade-1 bin), which is simply an easier target. A cross-dataset comparison would
  be meaningless.
- **Don't over-read the speed numbers.** Latency was measured with different methods
  per arm (concurrency here, serial there), so treat speed as order-of-magnitude,
  not a stopwatch photo finish.
- **This is an on-device contest, not "we beat the cloud."** A hosted cloud model
  (GPT- or Gemini-class) would very likely beat every laptop-bound approach here on
  accuracy - we did **not** benchmark one. The win we are chasing is a specific,
  constrained one: **the best relevance grader that runs on this machine with no API
  call** - for privacy, cost, and offline reasons. Not a claim about beating the cloud.

---

## Next: bring in the GPU

The two easy arms told us something precise: the prompted model's problem is
**calibration**, not comprehension, and the honest way to measure any fix is against
independent labels, not our own history. Both of those point at the same move -
stop *prompting* a general model and start *training* one to output this exact
scale.

That is where the GPU in the laptop earns its keep. In **part 2** we fine-tune a
cross-encoder reranker to emit the 0-3 grade directly - and find out whether
learning the labels fixes the calibration gap (and what it does to that seconds-per-
pair latency). In **part 3** we take the generative model itself, LoRA-tune it, and
read the label-token logits instead of free-generating JSON - and see whether a
tuned generalist beats a specialist out of domain.

The open question I most want answered: can a GPU-trained model fix *both* failure modes
at once - the word-overlap overgrades like the cat book *and* the harsh undergrades on
negation queries - without trading one for the other, and at what cost in speed and
memory? How many of the misclassifications from this round survive contact with a GPU?
We will put the numbers on the board next week.

Code and reproduction details: [github.com/Northvalley-Intelligence/cortex/tree/main/experiments/relevancy-replacement](https://github.com/Northvalley-Intelligence/cortex/tree/main/experiments/relevancy-replacement) — journals 00–08 and `results/*.json`.
