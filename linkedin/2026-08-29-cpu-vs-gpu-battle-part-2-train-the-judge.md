---
post_title: "CPU vs GPU battle, Round 1: train the judge, don't prompt it"
post_url: "https://feroshjacob.github.io/posts/2026/08/29/cpu-vs-gpu-battle-part-2-train-the-judge"
post_slug: "2026-08-29-cpu-vs-gpu-battle-part-2-train-the-judge"
linkedin_status: ready
post_type: image
visibility: PUBLIC
post_image: "/images/cpu-vs-gpu-battle-part-2.png"
hashtags:
  - MachineLearning
  - SearchRelevance
  - FineTuning
  - CrossEncoder
  - OnDeviceAI
---

In Part 1, I prompted a general-purpose LLM to grade e-commerce search relevance and watched it miscalibrate. This round I stop prompting and train the judge.

The move is unglamorous and it's the right one: not a bigger prompt, a different tool. A bge-reranker cross-encoder with a small 4-class head, fine-tuned on Amazon's ESCI data, running on the laptop's own Apple-Metal GPU. It grades a (query, product) pair by argmax over four logits — no rubric in the prompt, no free text to parse. Fine-tuning bakes the rubric in.

It's the first arm in the series to beat the shipped on-device judge on both test sets (ESCI QWK 0.360, WANDS 0.299), at ~28 ms per pair in about 1 GB of RAM. Small, fast, and finally calibrated.

But keep the scorecard honest: a much bigger GPU model, prompted with no training at all, matched it — and didn't break past the same ~0.35 ceiling. That's the real lesson of the round. The ceiling here is the task, not the size of the model. Sometimes the win is a purpose-built specialist that fits in a gigabyte.

Round 1, Part 2:
https://feroshjacob.github.io/posts/2026/08/29/cpu-vs-gpu-battle-part-2-train-the-judge

#MachineLearning #SearchRelevance #FineTuning #CrossEncoder #OnDeviceAI
