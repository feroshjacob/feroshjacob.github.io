---
post_title: "CPU vs GPU battle, Round 1: leave the laptop, the ceiling comes with you"
post_url: "https://feroshjacob.github.io/posts/2026/09/13/cpu-vs-gpu-battle-part-4-leave-the-laptop"
post_slug: "2026-09-13-cpu-vs-gpu-battle-part-4-leave-the-laptop"
linkedin_status: "draft"
post_type: image
visibility: PUBLIC
post_image: "/images/cpu-vs-gpu-battle-part-4.png"
hashtags:
  - MachineLearning
  - SearchRelevance
  - LLMasJudge
  - GPUComputing
  - Benchmarking
---

Three parts of this series ran on one 16 GB laptop, and every arm landed in the same narrow band — nothing past 0.5 QWK. Which leaves an obvious suspect: maybe the ceiling was never the task. Maybe it was just the machine.

So for the final part of Round 1, I left the laptop. Same 3,000 held-out ESCI pairs, same 3,000 WANDS pairs, same system prompt (verified byte-for-byte by sha256, not "looks the same"), same parser, same QWK implementation cross-checked to 1e-9 against the original. Two things changed and only two: the model (3B → Qwen2.5-7B-Instruct) and the hardware (laptop → a datacenter NVIDIA GPU on the National Research Platform, served by vLLM).

Result: ESCI QWK 0.361, WANDS 0.354. Against the 3B it scaled up from, that's +0.073 and +0.110 — a real lift, and the prompted 7B now ties a fine-tuned cross-encoder in-domain and beats it out of domain with zero training. But it is still sitting in the same band, and it never got near the best number in the whole experiment: the 0.486 that a LoRA-adapted 3B produced on a laptop in Part 3.

The ceiling is the task, not the model size. So I didn't run the 14B. The plan said go bigger only if the ceiling visibly moved; it didn't, and spending someone else's GPU-hour on a shared academic cluster to decorate a negative result isn't science.

Two things I'll flag rather than bury: the fallback to Qwen changes model family as well as size (Llama-3.1-8B is gated and I had no token), so the lift is "bigger and different," not "bigger." And the practical lesson of the round has nothing to do with models — every 48 GB card in the cluster was allocated while dozens of 24 GB cards sat idle. A 7B at fp16 is ~15 GB. Ask for the GPU your model needs, not the biggest one you can name.

Happy to share that this work — with Jiho Noh (Kennesaw State University) — has been accepted as a poster at SCD 2026 at Georgia State University: "The Quality Ceiling Is the Task, Not the Model Size: A Reproducible CPU/GPU Benchmark of Search-Relevance Judges." I'll be giving a 3-minute lightning talk in the Artificial Intelligence & Machine Learning session on September 22. If you're there, come argue with the poster.

Round 1, Part 4:
https://feroshjacob.github.io/posts/2026/09/13/cpu-vs-gpu-battle-part-4-leave-the-laptop

#MachineLearning #SearchRelevance #LLMasJudge #GPUComputing #Benchmarking
