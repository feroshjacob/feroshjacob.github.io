---
title: "CPU vs GPU battle, Round 1: leave the laptop, the ceiling comes with you"
date: 2026-09-13
permalink: /posts/2026/09/13/cpu-vs-gpu-battle-part-4-leave-the-laptop
categories:
  - cpu-vs-gpu-battle
series: cpu-vs-gpu-battle
series_order: 4
tags:
  - search-relevance
  - llm-as-judge
  - datacenter-gpu
  - vllm
  - kubernetes
  - benchmarking
image: /images/cpu-vs-gpu-battle-part-4.png
excerpt: "Three rounds of this experiment lived on a 16 GB laptop and nothing broke 0.5 QWK - a lexical floor at 0.222, and every learned or prompted arm crowded between 0.24 and 0.49. So we left the machine: the same prompt, the same parser, the same 3,000 held-out pairs, handed to a 7B judge on a datacenter GPU. It scored ESCI QWK 0.361 and WANDS 0.354 - better than the 3B it scaled up from, and still inside the same band. The ceiling is the task, not the model size. The result is now a poster at SCD 2026."
---

![Architecture of the Part 4 datacenter-GPU arm: the same (query + product title) pairs and the same byte-for-byte system prompt from Part 1's Arm B are gzipped into Kubernetes ConfigMaps and shipped to a National Research Platform GPU node, where vLLM serves Qwen2.5-7B-Instruct; the generated JSON goes through the identical ported parser and the identical QWK formula, and the scoreboard underneath plots all five arms on one QWK axis, showing a lexical floor near 0.22 and the learned and prompted arms spread between roughly 0.24 and 0.49, with nothing breaking 0.5, regardless of whether the arm ran on a laptop CPU, a laptop GPU, or a datacenter GPU](/images/cpu-vs-gpu-battle-part-4.png)

*Part 4, and the last part of Round 1. Part 1 prompted a small generalist and watched it
miscalibrate. Part 2 trained a specialist that won in-domain. Part 3 adapted a generalist
with LoRA and found the only arm that did not degrade out of domain. All three ran on one
16 GB laptop. This round we finally leave it - same problem, same labels, a real
datacenter GPU - to answer the question the series has been circling since Part 1.*

## The Short Version

**Nothing in this experiment has broken 0.5 QWK** against independent human labels, and
every learned or prompted arm has landed somewhere between 0.24 and 0.49. That is the
whole shape of Round 1: a lexical floor at 0.222, a prompted 3B at 0.288, a fine-tuned
cross-encoder at 0.360, a LoRA-adapted generative model at 0.353 - and the single best
number anywhere, 0.486, out of domain. Four methods that share nothing all landed under
the same 0.5 ceiling, spread across roughly a quarter of the scale rather than clustered
together.

Which leaves one obvious suspect. Every one of those arms was **laptop-bound by design**.
Maybe the ceiling was never the problem. Maybe it was the machine.

So this round changes exactly two things - the model and the hardware - and nothing else.
A prompted `Qwen2.5-7B-Instruct` on a datacenter NVIDIA GPU, served by vLLM, grading the
identical 3,000 held-out ESCI pairs and 3,000 held-out WANDS pairs, with the same system
prompt (verified byte-for-byte by hash), the same parser, and the same QWK implementation
Round 1 used.

The answer:

- **ESCI QWK 0.361** (95% CI 0.333-0.389), **WANDS QWK 0.354** (95% CI 0.326-0.382).
- Versus the 3B it scaled up from: **+0.073 on ESCI, +0.110 on WANDS**. Real, and not small.
- And landed **inside the 0.24-0.49 band** the learned laptop arms already occupied. It
  did not beat Part 3's 0.486. It did not break anything.

**The ceiling is the task, not the model size.** Pointwise relevance grading against
independent human labels is genuinely hard, and a 2.3x bigger prompted judge on
datacenter hardware does not change that. That finding is what the whole round was for,
and it is now a poster at SCD 2026 at Georgia State University - more on that at the end.

## Where we left off: a ceiling nobody could break

Round 1's scorecard after three parts, all of it on one laptop:

| Arm | Where it ran | ESCI QWK | WANDS QWK |
|---|---|---|---|
| A - BM25 | laptop CPU | 0.222 | 0.155 |
| B - prompted `llama3.2:3b` | laptop, Ollama | 0.288 | 0.244 |
| C - fine-tuned `bge-reranker-v2-m3` | laptop GPU (Metal) | 0.360 | 0.299 |
| D - LoRA `Llama-3.2-3B` | laptop GPU (MLX) | 0.353 | **0.486** |

Two honest readings were available at the end of Part 3, and they point in opposite
directions:

1. **The task is hard.** Four very different methods - lexical, prompted, discriminative
   fine-tune, generative adapter - all landed under the same 0.5 ceiling, with the three
   learned or prompted methods filling the 0.24-0.49 range above the lexical floor. When
   methods that share nothing land under the same ceiling, the thing they share is the
   problem.
2. **The machines are small.** Every arm fit in 16 GB of unified memory because that was
   the constraint we set, not because it was the right size for the job. A 3B model is a
   small model. Nobody serious grades relevance with a 3B model if they have a choice.

You cannot settle that from inside the laptop. You have to go get a bigger machine.

## The experiment: change two things, hold everything else

This is the part that makes the number mean anything. If you change the model *and* the
prompt *and* the parser *and* the scoring, a QWK movement tells you nothing about which
change caused it. So the round was built as an ablation against Part 1's Arm B, with the
changed surface reduced to **model + hardware**:

| Held constant | How it was held |
|---|---|
| Judge **system prompt** | Dumped byte-for-byte out of the production source and shipped as a file. Its **sha256 matched on the laptop and inside the cluster ConfigMap** - not "looks the same," identical. |
| Per-pair **user prompt** | The prompt builder ported function-for-function from JavaScript to Python. |
| **Parser** | Same port. Strip reasoning tags and code fences, take first `{` to last `}`, require an integer grade in 0-3, otherwise **throw**. A parse failure is recorded as a failure and **excluded** from QWK - never quietly coerced to 0. |
| **Eval sets** | The exact same `esci_eval_sample.jsonl` and `wands_eval_sample.jsonl` files, 3,000 rows each, that every Round-1 arm scored on - gzipped into a ConfigMap and shipped to the cluster. |
| **QWK** | The scoring function ported and then **cross-checked to 1e-9** against the original JavaScript on seeded arrays before anything was run. |

The model-delivery mechanism is matched too: Ollama applied `llama3.2`'s own instruct
chat template for Arm B, and vLLM's `chat()` applies Qwen's. Same shape, different model.

### The model, and an honest confound

The model I *wanted* was **Llama-3.1-8B-Instruct** - same family as Arm B's Llama-3.2-3B,
so 3B to 8B would isolate **size** and only size. It is gated on HuggingFace and no access
token was available in the cluster environment, so it could not be pulled.

The model I *used* was **`Qwen/Qwen2.5-7B-Instruct`** - open, ungated, and a strong
instruct model at roughly the same scale. That means this run changes model **family** as
well as size. So the +0.073 / +0.110 lift over Arm B is "a bigger, different-family
prompted model," not a clean size ablation, and I am going to say that every time I quote
the number. The clean version needs one more run with a token in the environment.

It is worth being clear about what the confound does and does not threaten. It muddies the
attribution of the *lift*. It does not touch the headline, which is a **ceiling** claim: a
strong 7B on datacenter hardware still landed at ~0.35.

## Getting onto a datacenter GPU (the part nobody writes about)

The compute came from the **National Research Platform's Nautilus cluster** - a shared
academic Kubernetes GPU pool. Writing the judge took an afternoon. Getting a pod onto a
card took three separate fixes, and I think they are more useful to read than the YAML:

1. **Resource ratios.** The cluster enforces that CPU, memory, and ephemeral storage
   limits sit within 1.2x of their requests. The fix is to set requests equal to limits
   and stop being clever: 3 CPU, 20 GiB memory, 40 GiB ephemeral.
2. **The big cards are tainted.** 48 GB cards carry a `large-gpu` hardware taint, which is
   a hardware label, not a preemption one - so tolerating it does not make the job
   preemptible. Without the toleration you simply never schedule.
3. **The pool was too narrow.** The manifest asked for an RTX A6000 by node affinity, and
   every A6000 in the cluster was allocated. But Qwen2.5-7B at fp16 is about 15 GB - it
   fits any card with 24 GB or more. Widening the affinity to the pool of non-quota-gated
   24-48 GB products scheduled it immediately, because the 24 GB tier had dozens of free
   cards sitting idle while I queued for the 48 GB one.

That third one is the practical lesson of the round: **ask for the GPU your model needs,
not the biggest GPU you can name.** The queue is at the top of the range.

The job itself is deliberately boring and safe for a shared cluster: a `vllm-openai`
image with the entrypoint overridden to the judge script, code and data mounted from
ConfigMaps (the repo was private, so no clone), `restartPolicy: Never`, `backoffLimit: 0`,
and a 2-hour `activeDeadlineSeconds` so a hung pod can never idle on a card it is not
using. Results come back on stdout between marker lines and get collected with
`kubectl logs`.

## The result: the ceiling did not move

<!-- TODO(ferosh): journal 09 records the node (k8s-gen4-05) and the widened 24-48 GB affinity pool,
     but never names the exact card the pod landed on. The article deliberately says "a 24 GB-or-larger
     datacenter NVIDIA GPU" rather than guessing. If you want the product named, `kubectl` history or
     the job log would have it - tell me and I'll add it. -->
Model load took **103.8 seconds**. After that, greedy generation ran at **23.5 pairs/sec**
on ESCI and **32.8 pairs/sec** on WANDS - 3,000 pairs in about two minutes per set.

Here is the full Round-1 scorecard with the datacenter arm in it:

| Arm | Hardware | ESCI QWK | WANDS QWK | ESCI acc | WANDS acc | Parse fails |
|---|---|---|---|---|---|---|
| A - BM25 | laptop CPU | 0.222 | 0.155 | 0.401 | 0.377 | - |
| B - prompted `llama3.2:3b` | laptop | 0.288 | 0.244 | 0.317 | 0.310 | 7 / 11 |
| C - fine-tuned bge | laptop GPU | 0.360 | 0.299 | **0.517** | 0.473 | - |
| D - LoRA Llama-3.2-3B | laptop GPU | 0.353 | **0.486** | 0.483 | **0.603** | - |
| **R2 - prompted Qwen2.5-7B** | **datacenter GPU** | **0.361** | 0.354 | 0.343 | 0.332 | 3 / 0 |

Read down a column, never across - ESCI and WANDS use different label spaces.

The bigger prompted model on the bigger machine scored **0.361 and 0.354**. Arm C, a
fine-tuned cross-encoder that runs in about a gigabyte of RAM on a laptop, scored **0.360**
on ESCI. Those two numbers are the same number. The 95% confidence intervals - [0.333,
0.389] and [0.326, 0.382] - sit right on top of the band the laptop fine-tunes were already in.

**So the answer to the question that closed Part 3 is: the ceiling is the task.** Scaling
the prompted judge 2.3x and moving it onto a card with more memory than the entire laptop
moved it from one part of the 0.24-0.49 band to another part of the same band. Pointwise
relevance grading against independent human labels is hard in a way that more parameters
do not fix.

## Where the bigger model genuinely did help

I do not want the headline to flatten a real result. Against Arm B - identical method,
identical prompt, identical parser, just 3B instead of 7B and a laptop instead of a
cluster - QWK rose **+0.073 on ESCI** (0.288 to 0.361) and **+0.110 on WANDS** (0.244 to
0.354). Scale helps *inside* the band, and it helps by a lot.

The more interesting consequence is what that buys you with **no training at all**. The
prompted 7B:

- **Ties the fine-tuned cross-encoder on ESCI** - 0.361 vs 0.360, in-domain, on the data
  the cross-encoder was fine-tuned on.
- **Beats it out of domain on WANDS** - 0.354 vs 0.299.

A model that never saw a single labeled example from this task matched a purpose-built
fine-tune on its home turf and generalized better off it. If you have GPU access and no
labeled data, that is a genuinely useful place to start, and it is the strongest practical
argument in this round for reaching for the bigger machine.

## Where it loses (because it does)

- **It did not catch the LoRA out of domain.** WANDS: 0.354 vs Part 3's **0.486**. This is
  the sharpest loss in the round and the clearest evidence the ceiling held - the best
  number in the whole experiment still belongs to a 3B model that was adapted on a laptop
  before dinner. A bigger prompted judge on better hardware did not get near it.
- **It loses badly on exact accuracy to both fine-tunes.** 0.343 / 0.332, against bge's
  0.517 / 0.473 and the LoRA's 0.483 / 0.603. Its QWK is respectable only because its
  mistakes are ordinally *close*: off-by-one accuracy 0.79 on ESCI and 0.87 on WANDS, MAE
  0.91 and 0.80. It agrees with the humans about **rank** far better than about **label**.
- **You can see why in the prediction distribution: it hugs the middle.** On WANDS it
  predicted grade 3 only **148 times out of 3,000**, and grade 1 **1,469 times**. On ESCI
  it used grade 3 only 479 times out of 2,997. A judge that will not commit to "exactly
  relevant" gets the ordering roughly right and the label consistently wrong.
- **Deployment cost is in a different universe.** This needs a 24 GB-or-larger datacenter
  GPU and a 104-second model load. Throughput is excellent once it is warm - 23 to 33
  pairs/sec batched - but Arm C does about **28 ms/pair in ~1 GB** on a laptop. For a
  shipping on-device judge, the cross-encoder still wins the efficiency trade by a wide
  margin, and it is not close.
- **It wrote three pieces of invalid JSON.** Three parse failures on ESCI, zero on WANDS,
  all the same failure mode: the model put a literal unescaped `"` inside its `reason`
  string - an inch mark in `40"`, or quoting the query `"cream and sugar"` back at itself.
  A prompted judge that writes free-text reasons will occasionally produce unparseable
  output. The parser rejected all three rather than inventing a grade, which is the
  correct behavior and is exactly why they are reported here instead of silently becoming
  zeros.

## Why I did not run a 14B

The plan going in was explicit: run ~8B first, go to 14B **only if the ceiling visibly
moved**, and do not jump to 70B or A100-class hardware unless the trend was clear.

It did not move. The 7B landed on the same ~0.35 plateau as two 3B fine-tunes, and the 3B
to 7B trend is already showing diminishing returns inside the band. A 14B prompted judge
would very likely land in the same band at more cost, on a shared academic cluster where
that GPU-hour is somebody else's queue. So I did not spend it. The manifest is one
`--model` change away if the datapoint is ever worth having.

Reporting a negative result and then *not* buying a bigger one to dress it up is, I think,
the whole point of keeping an honest scorecard.

## Honest caveats (they travel with every round)

- **Compare arms within a dataset, never across.** ESCI and WANDS use different label
  spaces - WANDS has no grade 1 at all - and are not on the same scale. Read down a column.
- **Family confound, restated.** Qwen2.5-7B vs Llama-3.2-3B mixes family with size. The
  lift is "bigger and different," not "bigger." The ceiling claim is unaffected.
- **This is still an on-device contest at heart.** The shipped production default is
  cloud-first (a hosted Gemini model), which was **not** benchmarked in this experiment and
  would very likely beat every arm here, including this one. What Round 1 asked was
  narrower: what is the best relevance judge you can get without an API call, and does
  leaving the laptop change the answer.
- **Agreement with the production system's own history is reproduction, not accuracy** -
  the circularity trap from Part 1 holds for every round, which is why every number in this
  series is scored against independent labels.
- **One run per arm, 3,000 pairs per set.** The confidence intervals are bootstrap
  intervals over that sample, not variance across repeated runs. Greedy decoding makes the
  run reproducible; it does not make the sample bigger.

## Where this went: a poster at SCD 2026

<!-- TODO(ferosh): conference naming. The accepted poster's own header reads
     "ARCTIC · Scientific Computing Day (SCD) 2026 · Georgia State University", but the GSU
     registration page is titled "Science and Cyberinfrastructure for Discovery (SCD)". The article
     uses just "SCD 2026 at Georgia State University" to avoid picking the wrong expansion in public.
     Tell me which one the organisers use and I'll spell it out. -->
<!-- TODO(ferosh): co-author. Jiho Noh is named here because he is on the accepted poster. Confirm
     he is happy being named in a public blog post and on LinkedIn before this goes out. -->
This finding - that the quality ceiling is a property of the task rather than of model
size - was written up with **Jiho Noh** (Department of Computer Science, Kennesaw State
University) as *"The Quality Ceiling Is the Task, Not the Model Size: A Reproducible
CPU/GPU Benchmark of Search-Relevance Judges,"* and it was **accepted as a poster at SCD
2026 at Georgia State University**, with a 3-minute lightning talk in the Artificial
Intelligence and Machine Learning session on September 22.

The poster is the compressed version of all four parts: one pipeline, one label mapping
taken from the production system's own source rather than retyped, one metric, and every
arm scored identically across three hardware tiers. If you want the argument on a single
page instead of across four posts, that is the page.

<!-- TODO(ferosh): the poster PDF link below points at the cortex repo on GitHub. Confirm you want it public in the article before this publishes. -->
Poster: [The Quality Ceiling Is the Task, Not the Model Size](https://github.com/Northvalley-Intelligence/cortex/blob/main/experiments/relevancy-replacement/drafts/poster-build/poster.pdf)

## Next: Round 1 closes, the battle does not

Four parts in, Round 1 has an answer, and it is a negative one - which is usually the kind
worth having. The ceiling on pointwise relevance grading is the task. It survived a
lexical baseline, a prompted small model, a discriminative fine-tune, a generative
adapter, and a 2.3x bigger prompted judge on a datacenter GPU. Five methods, three
hardware tiers, one ceiling nobody broke.

What that leaves you with is not "which model is best" but **which arm is best for which
goal**, and the series has a clear answer to that now:

- **Shipping an on-device judge for one domain?** The Part 2 cross-encoder. 28 ms, 1 GB,
  best in-domain accuracy of anything here.
- **Grading queries from domains you did not train on?** The Part 3 LoRA. Still holds the
  best number in the experiment, and it holds it on a laptop.
- **No labeled data, but you have GPU access?** This round's prompted 7B. It ties a
  fine-tune in-domain with zero training.
- **Need to actually break 0.5?** Nothing here does it. That needs a different problem
  formulation - pairwise or listwise comparison instead of pointwise grading, or richer
  inputs than a query and a product title - not a bigger model.

Two things Round 1 earned but did not run, and I would rather list them than pretend they
are done: the clean same-family size ablation (Llama-3.1-8B, once there is a token in the
cluster environment), and the reverse generalization test Part 3 asked for - train on
WANDS, test on ESCI - which is the real proof that adapting a generalist travels.

The battle itself is open-ended. Round 1 took search relevance. Round 2 takes a different
problem, and the CPU gets to swing first again.

*Same rules every round: independent labels, held-out numbers, and the losses reported
next to the wins.*

<!-- TODO(ferosh): journal 09, the k8s manifest, gpu_judge.py and the raw results are still only on the
     `round2-gpu-arm` branch, not on `main`. The link below therefore points at a branch. Cleaner:
     merge round2-gpu-arm into cortex main before this article publishes, and I'll repoint the link at
     `main` so the citation doesn't rot if the branch is ever deleted. -->
Code and reproduction details: [github.com/Northvalley-Intelligence/cortex](https://github.com/Northvalley-Intelligence/cortex/tree/main/experiments/relevancy-replacement) — journals 00-08 and `results/*.json` on `main`; this round's [journal 09](https://github.com/Northvalley-Intelligence/cortex/blob/round2-gpu-arm/experiments/relevancy-replacement/09-round2-gpu-arm.md), the Kubernetes manifest, and the raw `gpu_prompt_qwen2.5-7b__round2.json` on the `round2-gpu-arm` branch.
