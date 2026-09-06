---
title: "CPU vs GPU battle, Round 1: adapt the generalist, read the logits"
date: 2026-09-06
permalink: /posts/2026/09/06/cpu-vs-gpu-battle-part-3-adapt-the-generalist
categories:
  - cpu-vs-gpu-battle
series: cpu-vs-gpu-battle
series_order: 3
tags:
  - search-relevance
  - llm-as-judge
  - lora
  - mlx
  - gpu
  - fine-tuning
image: /images/cpu-vs-gpu-battle-part-3.png
excerpt: "Part 2 trained a specialist cross-encoder that won in-domain but leaned on its training data. This round we take the generative Llama-3.2-3B, LoRA-tune it on the laptop GPU with MLX, and grade by reading the label-token logits instead of prompting for JSON. In-domain it lands about even with the specialist (ESCI QWK 0.353), but it is the only arm that does not degrade out of domain - WANDS QWK 0.486, up from its own ESCI, while every other arm falls."
---

![Architecture of the Part 3 LoRA relevance judge (Arm D): a (query + product title) pair becomes a chat-template prompt that ends with no trailing space, then feeds a Llama-3.2-3B-Instruct 4-bit base model shown frozen (locked) - the only trainable part is a small pair of low-rank LoRA adapter matrices A and B (rank r) injected into each of the 16 transformer layers, so the layer computes h = W&#183;x + B&#183;A&#183;x with W frozen. At the output, the model's logits at the last token position are read over just the four label tokens (ids 15/16/17/18 = "0"/"1"/"2"/"3") and an argmax gives the 0-3 grade. A zoom-in shows the frozen full-size weight matrix beside the thin trainable LoRA detour, with the base frozen at ~1.9 GB and training peaking ~5.5 GB on a 16 GB laptop](/images/cpu-vs-gpu-battle-part-3.png)

*Part 3 of the series. Part 1 prompted a generalist and watched it miscalibrate.
Part 2 trained a specialist and watched it win in-domain but travel less well.
This round we try the third thing: keep the generalist, but stop prompting it -
LoRA-tune it on the laptop GPU and read the grade straight off the logits.*

## The Short Version

Part 2's cross-encoder was the first arm to beat the shipped on-device judge, but
it was a **specialist** - trained on Amazon ESCI product search, and you could see
it leaning on that domain when it met furniture (WANDS). This round asks the
opposite question: instead of building a purpose-made classifier, can we take the
*generative* model from Part 1 - `Llama-3.2-3B` - and **adapt** it into a grader
with a small LoRA, still entirely on the laptop's GPU?

Two changes make that work on a 16 GB machine:

- **LoRA on a 4-bit base.** We freeze a 4-bit quantized Llama-3.2-3B (~1.9 GB on
  disk) and train only small low-rank adapters on top - QLoRA-style, via
  Apple's [`mlx_lm`](https://github.com/ml-explore/mlx-lm). Training peaked at
  about **5.5 GB of RAM**, comfortable on a 16 GB laptop.
- **Read the logits, don't generate text.** At inference we don't ask the model to
  write JSON. The digits `0 1 2 3` are single tokens; we read the model's logits
  for those four tokens at the one position where the grade goes and take the
  argmax. No free generation, no parsing, **zero parse failures**.

The result is the interesting one of the series so far. In-domain, the adapted
generalist lands about **even** with the trained specialist (ESCI QWK **0.353** vs
the cross-encoder's 0.360). But out of domain it is the **only arm that does not
fall**: on WANDS it scores QWK **0.486**, while BM25, the prompted model, and the
cross-encoder all drop from their ESCI numbers. That is a lead worth chasing, not a
victory lap - and below I am careful about why.

*(Numbers: `results/mlx_lora__{esci,wands}_eval_sample.json`; journal
`07-arm-d-lora.md`. n = 3,000 pairs per set, held-out, independent human labels.)*

## Where we left off: a specialist that showed its seams

Part 2's `bge-reranker-v2-m3` cross-encoder won the deployment tradeoff outright -
more accurate than the prompted judge *and* ~200x faster - but it ended on a crack.
Its edge on WANDS furniture (QWK 0.299) was real but thinner than on its home turf,
ESCI (0.360). That is the classic shape of a model that fit its training domain
well and travels less well. A cross-encoder with a bolted-on 4-class head has no
choice: it only knows what it was trained on.

So the Part 3 move is to keep the broad prior knowledge a general chat model already
has - it has "seen" furniture, electronics, and negation queries in pre-training -
and just teach it *this scale* with a light touch, rather than building a classifier
from the domain up.

## The idea: adapt a generalist instead of training a specialist

> **New to LoRA?** LoRA (Low-Rank Adaptation) is a way to teach a model a new skill
> without retraining all of its billions of weights. You **freeze** the original
> model untouched and slip small pairs of low-rank matrices - the *adapters* - into
> its layers; only those tiny matrices ever get trained. Here it's the QLoRA flavor:
> the base Llama is 4-bit quantized and frozen (~1.9 GB), and the adapters train on
> top (peaking ~5.5 GB on a 16 GB Apple-Silicon laptop, 16 layers adapted). The
> payoff is that the trainable part is tiny and quick to train, and because the base
> model stays intact you can keep it and swap a different adapter in for a different
> task.

There are three genuinely different ways to turn a `(query, product title)` pair
into a grade, and this series has now built all three. Side by side:

| Approach | How it judges | Training | This-round result |
|---|---|---|---|
| **1 &#183; Prompted generalist** &#8212; `llama3.2:3b` (Part 1) | Zero-shot: *generates* JSON, you parse the digit back out | None &#8212; off the shelf | ESCI QWK 0.288 &#183; WANDS 0.244 |
| **2 &#183; Trained specialist** &#8212; `bge` cross-encoder (Part 2) | Argmax over a bolted-on 4-class head &#8212; discriminative, no text | Whole model fine-tuned on ESCI, Apple-Metal (MPS) GPU | ESCI QWK **0.360** &#183; WANDS 0.299 |
| **3 &#183; Adapted generalist** &#8212; `Llama-3.2-3B` + LoRA (this round) | Reads the four label-token logits, argmax &#8212; no generation | LoRA adapters only, frozen 4-bit base, on the GPU | ESCI QWK 0.353 &#183; WANDS **0.486** |

The same contrast in words:

- **Approach 1 - prompt a generalist (Part 1).** Take `llama3.2:3b` off the shelf,
  hand it a two-line rubric, and ask it to *generate* a grade as JSON. No training.
  It improvises the rubric on every call, and you have to parse free text back out.
- **Approach 2 - train a specialist (Part 2).** Take a cross-encoder, replace its
  head with four class logits, and fine-tune the whole thing on ESCI. It is
  *discriminative* - it does not generate anything, it scores. One forward pass, an
  argmax, no text.
- **Approach 3 - adapt a generalist (this round).** Take the generative
  `Llama-3.2-3B`, add LoRA adapters, and train *only* those on ESCI so the frozen
  base keeps its general knowledge. Then, at inference, skip generation entirely and
  read the label-token logits. A generative model turned into a judge by
  **adaptation**, not by prompting and not by rebuilding.

The point of Approach 3 is to get the reliability of a classifier (no parsing, no
malformed output) *without* throwing away the broad prior that a general model
carries into domains the training set never covered.

## How you read a grade off the logits

This is the mechanic that makes the arm honest, so it is worth being concrete. In
this tokenizer, `"0"`, `"1"`, `"2"`, `"3"` each encode to a **single** token (ids
15, 16, 17, 18 - a leading space would split them into two tokens, so the prompt is
built to avoid one). The chat template ends the prompt right where the assistant's
answer begins, with no trailing space, so the *very next token position* is
unambiguously the grade digit.

That means we never let the model talk. We run one forward pass over the prompt,
look at the logits for exactly those four token ids at that final position, and take
the argmax. For a given pair the model emits four label-token logits and we take
the largest - they might look like `[26.77, 24.55, 25.44, 24.77]` (illustrative,
not measured values), where the argmax lands on grade 0.

Two consequences fall out for free:

- **No parse failures, ever.** The results file records `parse_failure_count: 0`
  with the note *"not applicable - argmax over the 4 label-digit token logits at one
  forward-pass position, no free-generation or text parsing."* Part 1's prompted arm
  had to strip `<think>` tags and fences and occasionally threw a pair away; a
  logit read cannot malform its output, exactly like Part 2's classifier.
- **It is a fair comparison of *method*, not data.** Arm D trains on the *identical*
  36k-row balanced ESCI split the Part 2 cross-encoder used (a 4k dev split held
  aside), so Approach 2 vs Approach 3 is a clean comparison of *cross-encoder
  classifier* against *generative LoRA logit-read*, not of who got the better
  training rows.

## Training it on the laptop GPU

The GPU does real work again this round. On the Apple-Metal GPU via `mlx_lm`, we
LoRA-tune the 4-bit base for one full epoch (9,000 iterations over the 36k rows,
batch size 4, `--num-layers 16`, learning rate 1e-5), computing the loss **only on
the completion digit** (`--mask-prompt`), not on the instruction text. On a 16 GB
machine the run peaked at about **5.5 GB of RAM** and took a handful of hours - the
kind of job you start before dinner and read the next morning, no datacenter, no API.

The 4-bit base is a deliberate choice, not a shortcut: an fp16 3B model is ~6-7 GB
in weights alone, which leaves almost no headroom for activations and optimizer
state on a 16 GB laptop. Freezing a 4-bit base and training fp32 LoRA adapters on
top is `mlx_lm`'s well-supported path and the reason this fits at all.

As always in this series, every score below is measured against **independent
human-derived labels** (Amazon ESCI and WANDS), never the service's own past
judgments - roughly 92% of those were written by the same local model, so agreeing
with them would measure reproduction, not accuracy. That circularity trap from
Part 1 still governs every number here.

## Does it win? In-domain it ties; out of domain it holds

Here is the whole board, all four arms this series has built, on the same seeded
held-out samples. Compare arms *down a column* (within one dataset) - the ESCI and
WANDS columns are **not** on the same ruler, and I say why right after the table.

| Arm | ESCI QWK | WANDS QWK | ESCI &#8594; WANDS | speed / pair | memory |
|---|---|---|---|---|---|
| A &#183; BM25 lexical | 0.222 | 0.155 | &#8211;0.067 | instant | tiny |
| B &#183; Prompted `llama3.2:3b` (&#8776; shipped judge) | 0.288 | 0.244 | &#8211;0.044 | ~1&#8211;5 s | ~5&#8211;6 GB |
| C &#183; bge cross-encoder, fine-tuned (Part 2) | **0.360** | 0.299 | &#8211;0.061 | **~28 ms** | **~1 GB** |
| **D &#183; Llama-3.2-3B + LoRA, logit-read (this round)** | 0.353 | **0.486** | **+0.133** | ~0.4&#8211;0.9 s | ~2.7 GB |

*(Arm D: ESCI QWK 0.353, 95% CI 0.320-0.385, accuracy 0.483, off-by-one 0.722,
mean nDCG 0.840; WANDS QWK 0.486, 95% CI 0.453-0.517, accuracy 0.603, off-by-one
0.816, mean nDCG 0.884 - all from `results/mlx_lora__{esci,wands}_eval_sample.json`,
inference peak RSS ~2.67 GB, warm median ~0.87 s/pair on ESCI. Arms A-C from
Parts 1-2 and `results/bge_reranker__*.json`.)*

Read the last two columns together, because that is where the story lives:

- **In-domain, the adapted generalist ties the specialist.** On ESCI, Arm D's 0.353
  and Arm C's 0.360 sit inside each other's confidence intervals (D: 0.320-0.385).
  Building the grader by *adapting a generalist* got essentially the same in-domain
  accuracy as *training a purpose-built classifier*. That alone is a result: you did
  not have to give up the general model to get classifier-grade reliability.
- **Out of domain, it is the only arm that goes up.** Every other arm loses ground
  from ESCI to WANDS - BM25 -0.067, the prompted model -0.044, the specialist
  cross-encoder -0.061. Arm D moves the *other* way: **+0.133**. It is the single
  arm in the whole experiment whose ordinal agreement improves on a domain it never
  trained on.

## Why "0.486 > 0.353" is a lead, not a trophy

I want to be exact about what that +0.133 does and does not prove, because it is the
easiest number in this series to over-claim.

- **You cannot compare the two datasets directly.** WANDS uses a coarser 3-class
  label space (Exact / Partial / Irrelevant, no grade-1 bin), which is simply an
  easier target than ESCI's four grades. So "0.486 on WANDS beats 0.353 on ESCI" is
  **not** a valid sentence - they are different rulers. The claim is *not* that Arm D
  is better on WANDS than on ESCI in absolute terms.
- **What *is* comparable is the direction, across arms.** Every arm was scored on the
  same two rulers. Three of the four degrade going out of domain; one does not. That
  cross-arm *pattern* - who holds up and who slides - is a fair comparison even when
  the absolute cross-dataset numbers are not. Arm D holding up (indeed improving) is
  the honest signal.
- **One out-of-domain collection is not proof of generalization.** WANDS is a single
  domain (furniture). A model that travels well to one new domain has earned a
  hypothesis, not a law. The real test is a **reverse run** - train on WANDS, test on
  ESCI - and see whether the adapted generalist still holds up when the home and away
  fields are swapped. That is deliberately left as future work; I would rather flag
  it than quietly bank the win.

So the fair summary is: *adapting a generalist looks like it may travel better than
training a specialist* - a lead strong enough to chase, on the strength of being the
only arm that did not degrade out of domain, and honest enough to admit it rests on
one collection and a coarser label space.

## Where it loses (because it does)

- **It did not beat the specialist in-domain.** On ESCI, Arm C is still nominally
  ahead (0.360 vs 0.353), even if the gap is inside the noise. If ESCI product
  search were the only domain you cared about, Part 2's cross-encoder is still the
  arm to ship - and it is far cheaper to run.
- **It is slower and heavier than the specialist.** A generative 3B forward pass -
  even reading a single position - runs at roughly **0.4-0.9 s/pair** and ~2.7 GB of
  RAM at inference, versus the cross-encoder's ~28 ms and ~1 GB. That is much faster
  than Part 1's prompted arm (seconds, and it actually generated text), but it is
  still one to two orders of magnitude off the specialist. Adaptation buys
  generalization; it does not buy the classifier's speed.
- **The absolute ceiling is still modest.** QWK in the low-to-mid 0.3s (ESCI) is
  *better*, not *good*. Every arm in this experiment lives in the ~0.3-0.5
  neighborhood; a lead over the baseline is not a solved problem, and I am not going
  to dress it up as one.

## Honest caveats (they travel with every round)

- **Compare arms within a dataset, never across.** Restating it because it is the
  one that makes the headline honest: the ESCI and WANDS columns use different label
  spaces and are not on the same scale. Read down a column.
- **This is an on-device contest, not "we beat the cloud."** The shipped service
  default is cloud-first (a hosted Gemini model), which was **not** benchmarked here
  and would very likely beat every laptop-bound arm. The win we chase is narrow and
  specific: the best relevance grader that runs on *this machine* with no API call -
  for privacy, cost, and offline reasons.
- **The GPU is the Apple M-series integrated GPU (Metal), not a discrete card.** No
  hand-written CUDA or Metal kernels - `mlx_lm` handles device dispatch. The whole
  thing, training and inference, fits on a 16 GB laptop, which is the entire point.
- **Agreement with the service's own history is reproduction, not accuracy** - the
  circularity trap from Part 1 still holds, which is why every number here is against
  independent labels.

## Next: leave the laptop

Three rounds in, the on-device picture is clear enough to name a winner *per goal*:
if you only serve ESCI-shaped product search, the Part 2 cross-encoder is the arm -
fastest, lightest, best in-domain. But if you care about grading queries from
domains you did not train on, the adapted generalist is the one that did not fall
over, and that is the more interesting lead.

The open question the whole series has been circling is still open: is the
~0.3-0.5 QWK ceiling the **task** - pointwise relevance grading is just hard - or is
it the **size** of a model that fits on a laptop? Every arm so far has been
laptop-bound by design. In **Part 4** we finally leave the machine and hand the same
problem, the same held-out labels, to a real datacenter GPU - to find out whether
the ceiling moves when the hardware does. And somewhere in there is the reverse
generalization test this round earned but did not run.

*Same rules every round: independent labels, held-out numbers, and the losses
reported next to the wins.*

Code and reproduction details: [github.com/Northvalley-Intelligence/cortex/tree/main/experiments/relevancy-replacement](https://github.com/Northvalley-Intelligence/cortex/tree/main/experiments/relevancy-replacement) — journals 00-08 and `results/*.json`.
