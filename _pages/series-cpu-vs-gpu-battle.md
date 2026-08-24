---
layout: series
title: "CPU vs GPU Battle"
permalink: /series/cpu-vs-gpu-battle/
series_key: cpu-vs-gpu-battle
description: "A recurring experiment: solve a real problem the easy, CPU-friendly way, then bring in the GPU and deep learning — with an honest scorecard each round."
image: cpu-vs-gpu-battle.png
tags:
  - cpu vs gpu
  - on-device ml
  - classical vs deep learning
  - coding agents
  - benchmarking
author_profile: true
---

## Why this battle is worth watching

In graduate school I once spent hours — days, really — hand-optimizing a small GPU
program. Tiling, memory coalescing, occupancy, the whole ritual, chasing a kernel that
would finally saturate the hardware. It was painstaking, specialist work, and getting it
right felt like an achievement in itself.

That kind of optimization is exactly what coding agents now absorb. The tuning that used
to eat a week gets handled, and we get to *harvest* the benefit instead of grinding for
it. Reaching for the GPU has never been cheaper.

But here is the twist that makes the fight interesting: **CPUs have been around far
longer, and the compilers, libraries, and habits of a whole generation of engineers have
a much deeper grasp of the CPU than the GPU.** So on any given problem it is genuinely
unclear which corner wins — the mature, well-understood CPU stack that runs anywhere, or
the raw, newly-accessible power of the GPU.

This series runs that battle one real problem at a time. Each round: take a problem, solve
it first the easy CPU-friendly way, then bring in the GPU and deep learning — and keep an
honest scorecard, wins *and* losses. The problem changes from post to post; the tension
does not.
