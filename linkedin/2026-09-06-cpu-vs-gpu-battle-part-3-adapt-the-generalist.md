---
post_title: "CPU vs GPU battle, Round 1: adapt the generalist, read the logits"
post_url: "https://feroshjacob.github.io/posts/2026/09/06/cpu-vs-gpu-battle-part-3-adapt-the-generalist"
post_slug: "2026-09-06-cpu-vs-gpu-battle-part-3-adapt-the-generalist"
linkedin_status: "published"
post_type: image
visibility: PUBLIC
post_image: "/images/cpu-vs-gpu-battle-part-3.png"
hashtags:
  - MachineLearning
  - SearchRelevance
  - LoRA
  - FineTuning
  - OnDeviceAI
linkedin_post_urn: "urn:li:share:7502506311507435520"
linkedin_published_at: "2026-09-06T23:21:34.958012+00:00"
linkedin_thumbnail_urn: "urn:li:image:D4E10AQHaY6sBNQhDVg"
---

Part 1 prompted a generalist and watched it miscalibrate. Part 2 trained a specialist that won in-domain but leaned on its training data. This round tries the third thing: keep the generalist, but stop prompting it.

Arm D takes a frozen 4-bit Llama-3.2-3B and adapts it into a grader with a small LoRA, entirely on the laptop's GPU via MLX. At inference it never generates text — it reads the logits for the four label tokens "0 1 2 3" at one position and takes the argmax. No JSON, no parsing, zero parse failures. Training peaked at ~5.5 GB of RAM; the kind of job you start before dinner and read the next morning.

Here's the honest lesson. In-domain it ties the trained specialist (ESCI QWK 0.353 vs 0.360, inside the noise). But out of domain it's the only arm that goes UP: WANDS QWK 0.486, +0.133, while BM25, the prompted model, and the cross-encoder all fall. Every other arm loses ground crossing domains; the adapted generalist gains it.

I'm calling that a lead, not a trophy. WANDS is one collection with a coarser label space, so you can't read "0.486 > 0.353" as a cross-dataset win — what's fair is the cross-arm direction: three of four degrade out of domain, one doesn't. The real proof is a reverse run (train WANDS, test ESCI), and that's deliberately future work. Adapting a generalist looks like it may travel better than training a specialist — strong enough to chase, honest enough to admit it rests on one domain.

Round 1, Part 3:
https://feroshjacob.github.io/posts/2026/09/06/cpu-vs-gpu-battle-part-3-adapt-the-generalist

#MachineLearning #SearchRelevance #LoRA #FineTuning #OnDeviceAI
