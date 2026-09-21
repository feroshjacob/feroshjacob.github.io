---
title: "Software Is Getting Cheaper. But By How Much?"
date: 2026-09-20
permalink: /posts/2026/09/20/software-is-getting-cheaper-but-by-how-much
categories:
  - software-scarcity
series: software-scarcity
series_order: 5
tags:
  - software-economics
  - ai-assisted-development
  - developer-productivity
  - build-vs-buy
  - software-pricing
image: /images/software-cheaper-by-how-much.png
excerpt: "I've spent months arguing AI is making software cheaper. So I went looking for the evidence, applied my own rule, and found something more interesting than a percentage: software purchasing power."
---

![Same $1,000, more software: a price tag sliding down on the left, an hourly rate meter rising on the right](/images/software-cheaper-by-how-much.png)

*Part 5 of [The End of Software Scarcity](/series/software-scarcity/).*

One of the first things you learn when working with AI is simple: ask for evidence.

I've spent the last few months arguing that AI is making software cheaper.

Then I realized I should take my own advice.

Where is the evidence?

## Simple Economics

Here's the mechanism: when supply rises faster than demand, price falls.

Code supply has visibly risen since coding agents arrived. GitHub's own [Octoverse 2025 report](https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/) puts a number on it: developers pushed nearly **1 billion commits in 2025, up 25.1% year-over-year** — the platform's biggest jump on record, including almost 100 million commits in August alone. Commits aren't code exactly, but they're the closest supply meter we have, and it's spinning faster than ever.

So: **does that mean software cost is about to come down?**

Not evenly. Price isn't set by supply alone — it's set by what buyers can substitute for what they already pay. Routine, well-specified work is already getting repriced: demand for AI-substitutable freelance skills is down 20–50% relative to trend, exactly where a price collapse hits first (see References). Bespoke work that used to sit outside the budget becomes affordable — that's the build-vs-buy shift below. And demand for software was never fixed: cheaper production unlocks buyers who never bought custom software at the old price, eating part of the supply increase. The invoice doesn't fall uniformly. Software purchasing power beats a price index because it survives demand that moves too.

## The Short Version

There is no single number for "how much cheaper software has become." The honest evidence is messier and more interesting than that. Developer productivity, labor cost, production cost, market price, and what I'll call software purchasing power can all move in different directions at once. Below, two strong supporting signals, one important counterexample, and the concept I think actually matters.

## What the Evidence Actually Shows

### Developers really can get more done

The strongest evidence I found is a [Microsoft Research field experiment](https://www.microsoft.com/en-us/research/publication/the-effects-of-generative-ai-on-high-skilled-work-evidence-from-three-field-experiments-with-software-developers/) covering 4,867 professional developers. Randomized access to an AI coding assistant increased completed tasks by roughly 26%.

That's real. But it measures *productivity* — output per developer — not what anyone paid for the finished software. A 26% productivity gain does not automatically mean a 26% cheaper product.

### Businesses are starting to build instead of buy

The second signal is a build-vs-buy shift. In [McKinsey's State of AI](https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai) survey, about 32% of respondents said their organization had decided *against* purchasing a software product or feature because agentic coding tools let them build it internally.

That's a meaningful early signal, not proof. McKinsey didn't verify that these companies rebuilt an identical commercial product for less. What they reported is a changed decision: build now looks viable where buy used to win by default. That's still worth taking seriously, because it's exactly the mechanism my broader thesis depends on.

## The Counterevidence, Front and Center

Here's where it gets uncomfortable for my own argument.

[METR](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/) studied 16 experienced open-source developers completing 246 real tasks in mature codebases they already knew well. Using early-2025 AI tools, they took about **19% longer** — even though they *believed* AI had made them faster.

This is not a small caveat. It suggests the Microsoft/Accenture gains may not generalize to experienced developers doing complex work in codebases they already understand. Greenfield work, junior developers, and well-bounded tasks may behave completely differently from brownfield maintenance on a system an expert already knows cold.

So: productivity gains from AI are real in some settings and absent, or negative, in others. Anyone telling you it's universal is skipping the METR result.

## Software Purchasing Power

Here's the reframe I think is more useful than any single percentage.

Instead of asking "how much cheaper is software?", ask: **how much more software can I buy for $1,000 today than I could two years ago?**

The evidence above doesn't point to invoices falling by some clean number. It points somewhere else: for the same money, you can plausibly get more functionality. A [GoodFirms survey](https://www.goodfirms.co/resources/custom-software-development-cost-survey) of software companies found roughly 61% expect AI to cut development costs 10–25% — but it also surfaces something more interesting: some of that saving doesn't show up as a lower invoice at all. It shows up as scope that used to be out of budget getting included instead.

Same money. More software. That's technological deflation of the kind we've seen before in other industries, where capability rises faster than the sticker price falls.

## Back to the Thesis

This connects to the claim I made when I started this series: that software scarcity is ending.

The old model was straightforward. Software was expensive to build, so businesses adapted themselves to it:

**Business → adapts to → Software**

If the marginal cost of building and modifying software keeps falling, that relationship can start to reverse:

**Software → adapts to → Business**

The McKinsey build-vs-buy number is an early signal of exactly this — companies choosing custom over generic because custom got cheap enough to be worth it. I'm not declaring SaaS dead. I'm saying the calculation is shifting, and I can point to a specific data point instead of just asserting it.

## So, How Much Cheaper Has Software Become?

The honest answer is that we don't have a single number.

In controlled settings, developer output rose by roughly 26%, and other field research has found gains as high as 55%. Software companies expect AI to cut development costs 10–25%. Some freelance categories are already under pricing and demand pressure. Some companies are now building what they used to buy. And experienced developers on complex, familiar codebases saw *no* gain — some got slower.

None of those numbers means "software is 26% cheaper" or "55% cheaper." Forcing them into one figure would be the opposite of asking for evidence.

The more interesting possibility is that we're measuring the wrong thing. The price of software may not collapse. The amount of software we can afford might explode instead.

Historically, software was expensive and ideas were cheap. If software production keeps becoming abundant, that relationship may flip: software becomes cheap, and good ideas become the expensive part.

That, I think, is what the end of software scarcity actually means.

## Footnote: Five Things That Move Separately

Before any of the evidence above makes sense, keep five things separate: developer productivity (output per developer), labor cost (cost of engineering time), production cost (total cost of creating the software), market price (what customers pay), and software purchasing power (functionality per dollar). They don't move together — an AI-skilled developer can charge more per hour while finishing in far fewer hours, so labor gets pricier per hour while the software gets cheaper to produce. Illustration, not data: before AI, 40 hours × $60/hour = $2,400; with AI, 10 hours × $100/hour = $1,000. Nobody measured this exact project, but it shows productivity, labor cost, and production cost moving in three directions in one paragraph.

## References

Additional evidence reviewed for this article, not covered in the body above:

- [BIS / Ant Group CodeFuse field experiment](https://www.bis.org/publ/work1208.htm) — a field experiment found generative AI increased code output by roughly 55%, concentrated among less-experienced developers.
- [Ramp Economics Lab](https://ramp.com/data/ai-labor-market-impact-freelancers) — businesses with high AI exposure saw roughly $0.03 of increased AI spending for every $1 of reduced freelance spending. This is spending substitution, not proof that AI output equals freelance output.
- [Upwork Future Workforce Index 2026](https://www.upwork.com/research/research-future-workforce-index-2026) — AI-using freelancers earn about 34% more per hour, while lower-complexity generative-AI contract work saw starts up ~90% and per-contract earnings down ~13%.
- [GoodFirms Custom Software Development Cost Survey](https://www.goodfirms.co/resources/custom-software-development-cost-survey) — survey of 100+ development companies on AI's expected effect on cost and scope.
- [Freelance-market research on generative AI](https://www.sciencedirect.com/science/article/pii/S0167268124004591) — analysis of 3M+ freelance postings found demand for AI-substitutable skills fell 20–50% relative to trend, while complementary AI-skill demand rose.
