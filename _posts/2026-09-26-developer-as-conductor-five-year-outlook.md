---
title: "The Developer's Job in Five Years: Conductor, Not Coder"
date: 2026-09-26
permalink: /posts/2026/09/26/developer-as-conductor-five-year-outlook
categories:
  - software-scarcity
series: software-scarcity
series_order: 6
tags:
  - software-scarcity
  - ai-agents
  - developer-productivity
  - context-engineering
  - software-economics
image: /images/developer-as-conductor.png
excerpt: "Coding is getting cheap, but the developer's job doesn't vanish — it moves upstream into definition and downstream into validation. Three testable hypotheses for the next five years, including the counterexample that should make you skeptical of all three."
---

![A conductor figure directs four separate isolated agent workspaces, each running its own task in parallel](/images/developer-as-conductor.png)

*Part 6 of [The End of Software Scarcity](/series/software-scarcity/). Previously: [Software Is Getting Cheaper. But By How Much?](/posts/2026/09/20/software-is-getting-cheaper-but-by-how-much)*

Two camps have formed around AI and software development, and I don't trust either one.

The first camp says nothing has really changed. Senior engineers still gatekeep every merge. Five years from now looks like today, just with better autocomplete.

The second camp says code is already free. Software will be generated instantly, forever, for everyone, at zero marginal cost.

Neither extreme survives contact with evidence. Believing either one is exactly the mistake this series keeps warning against: picking a comfortable story and skipping the part where you check it.

So here are three specific, checkable hypotheses about what actually changes in the next five years — not "AI changes everything," but three separate claims, each with its own evidence, and its own reason to doubt it.

## The Short Version

Coding is getting cheaper, but the developer's job doesn't disappear. It moves upstream into **defining the problem correctly** and downstream into **validating what an agent produced**. Software's price doesn't fall in a straight line either — the same budget increasingly buys more capability instead of a lower invoice. And the whole shift is bottlenecked by something more mundane than any of that: getting an agent enough of the *right* context about one specific, messy, real business without it confidently building the wrong thing.

Three hypotheses. Here's the evidence for each, and the counterevidence that should keep you honest.

## Hypothesis 1: The Developer Becomes a Conductor

The clearest version of this future is already shipping. **[Conductor](https://www.conductor.build/)** is a Mac app that runs "parallel Claude Code, Codex, and Cursor agents in isolated workspaces" — each agent gets its own copy of your codebase, works a separate task, and reports back to a single dashboard. You stop writing every line yourself and start deciding what gets built, in what order, by which agent, and whether the result is good enough to merge.

That's the shape of the hypothesis: human value moves **upstream** to specifying the task well enough that an isolated agent can't misunderstand it, and **downstream** to reviewing four diffs instead of writing one function. The scarce skill stops being "can you write a for-loop" and becomes "can you write a spec an agent can't misread" and "can you tell good code from confident code."

Here's why I'm not popping champagne over it.

The reason is in the [last article](/posts/2026/09/20/software-is-getting-cheaper-but-by-how-much): the **METR** study found experienced developers got slower with AI on codebases they already knew, while believing the opposite the whole time.

If conducting several parallel agents is harder than writing code yourself — and reviewing four unfamiliar diffs at once is a plausible way for that to be true — the "conductor" role could easily produce the same illusion: busier, more confident, and not actually faster. Being the conductor raises the validation burden precisely where METR shows humans are worst at judging their own performance.

So Hypothesis 1 isn't "developers become conductors and everything gets better." It's "developers become conductors, and whether that's actually better depends entirely on whether validation keeps up" — which is an open question, not a settled one.

Five years is roughly the timeline I'd bet on for this role to become the default for professional developers, not because the tooling needs five years to get better — it's already usable today — but because **validation habits** take longer to build than the tools that make them necessary. Learning to write code and learning to distrust code you didn't write are different skills, and most developers currently have only practiced the first one.

## Hypothesis 2: Software's Purchasing Power Explodes

The second hypothesis is one I've already made the case for in this series, so I'll be brief and point you to the receipts.

The naive version of "AI makes software cheaper" imagines a flat discount: the same product, 25% off the invoice. The evidence doesn't support that shape. What it supports is **purchasing power** — for the same $1,000, a business gets custom workflows, integrations, and features that used to be completely out of budget. The invoice doesn't necessarily shrink. What you can get for it grows.

I laid out the mechanism and the strongest supporting signal — a McKinsey build-vs-buy survey — in [the previous article in this series](/posts/2026/09/20/software-is-getting-cheaper-but-by-how-much). The short version of the thesis: for decades, the relationship was **Business → adapts to → Software**, because changing software was expensive and changing your own process was cheap. If the marginal cost of building and modifying software keeps falling, that reverses into **Software → adapts to → Business**. Custom stops being the expensive option by default.

I'm not restating the percentages here, because restating a number outside the analysis that produced it is how a caveated finding turns into a headline. Go read the receipts if you want them. The point for this article is narrower: hypothesis 2 is about **what a dollar buys**, never about **the price printed on the invoice**. Collapsing that distinction is the single error this whole series is built to catch.

## Hypothesis 3: Context Is the New Bottleneck

Here's the part that doesn't get said enough.

Writing new code from a blank file is the easy case for a coding agent. Give it a well-specified feature in a clean repo, and current models are genuinely good at it. The hard case is everything else: a fifteen-year-old schema nobody fully documented, a security rule that exists because of an incident in 2019, an integration held together by a Slack thread and one engineer's memory. An agent doesn't know what it doesn't know, and it will confidently generate plausible, syntactically correct code that quietly violates a rule it was never told about.

This isn't a hypothetical concern. A recent [study of AI coding agents on a real 108,000-line production codebase](https://arxiv.org/abs/2602.20478) found exactly the failure mode you'd expect from an agent without durable memory of the project: it **"loses coherence across sessions, forgets project conventions, and repeats known mistakes"** — not because the model can't code, but because nothing was carrying the accumulated context of the project between sessions. The fix the researchers built was infrastructure for *persisting* that context — specs, conventions, and domain knowledge encoded outside any one conversation — because without it, capability alone wasn't enough.

A major vendor is already betting on this being the real problem. In December 2025, [Google shipped Conductor](https://developers.googleblog.com/conductor-introducing-context-driven-development-for-gemini-cli/), a Gemini CLI extension (open source, Apache 2.0, [gemini-cli-extensions](https://github.com/gemini-cli-extensions/conductor)) that makes a human-reviewed spec and plan — not the prompt — the unit of work: context first, then a written spec, then a plan you approve, then implementation. It's one vendor's tool, not proof of an industry trend, but it's a concrete, checkable data point that a major AI lab thinks context infrastructure, not raw code generation, is where the next increment of value sits.

METR's slowdown wasn't a syntax problem — those developers already knew their codebases; what cost them time was *feeding the model enough of what they already knew, correctly*. That's the context bottleneck showing up in the one dataset in this article that actually measured real developers doing real work.

**Context is the new code.** Not "prompting is a skill" — actually building and maintaining the structural knowledge (schemas, constraints, history, conventions) that lets an agent act safely inside a specific business is turning into the harder engineering problem, right as writing the code itself gets easier.

This is also why "five years" is doing real work as a horizon in this article, rather than being a round number I picked for the title. Greenfield capability is improving on a timeline measured in months. Context infrastructure — the unglamorous work of encoding a specific organization's schemas, history, and unwritten rules somewhere an agent can actually use them — is an organizational problem, not a model problem, and organizations change slowly. I'd expect the gap between "AI can write good code" and "AI can be trusted with your specific legacy system" to close in years, not months, and to close unevenly: greenfield teams first, complex regulated brownfield systems last.

## Where the Three Meet

Put the three together and a pattern shows up that none of them states alone.

If developers become conductors (Hypothesis 1), and software's purchasing power explodes because custom work gets cheap enough to be worth it (Hypothesis 2), the thing that makes both of those actually pay off is solving the context bottleneck (Hypothesis 3). An agent that can't safely absorb a business's real structure can't be trusted to run unsupervised, which caps how much conducting-not-coding can scale, which caps how much purchasing power actually explodes. The three hypotheses aren't independent bets. Two of them are conditional on the third.

And solving the context bottleneck has an uncomfortable requirement baked into it: **an agent that's genuinely useful at this needs deep, ongoing access to the private, messy, specific data that makes a business what it is.** Not a sanitized API. Not a scrubbed demo dataset. The real schemas, the real customer records, the real internal Slack history that explains why the weird rule exists. Extreme software abundance and extreme data access turn out to be the same request.

That should make you pause. It made me pause. If the payoff of this whole shift depends on agents holding that much intimate context about how a business actually runs, a new question becomes unavoidable:

> What happens when massive third parties or competitors try to buy, trade, or steal that intimate agent context? We will tackle the high-stakes world of Agentic Data Privacy in our next article.
