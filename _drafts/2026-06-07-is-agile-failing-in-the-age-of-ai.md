---
title: 'Is Agile Failing in the Age of AI?'
date: 2026-06-07
permalink: /posts/2026/06/07/is-agile-failing-in-the-age-of-ai
tags:
  - ai
  - agile
  - software engineering
  - product development
  - engineering leadership
excerpt: Agile transformed software development by adapting to changing requirements. But what happens when AI dramatically reduces the cost of implementation? Are we still optimizing for the right bottleneck?
---

# Is Agile Failing in the Age of AI?

Before you sharpen your pitchforks, let me clarify something.

I do not believe Agile is failing because Agile was wrong.

I believe Agile is struggling because it was designed for a different world.

For decades, software development was expensive. Writing code was expensive. Testing was expensive. Changing requirements was expensive. Missing deadlines was expensive.

Agile emerged as a response to those realities.

But AI is changing the economics of software development so dramatically that some of Agile's core assumptions deserve to be revisited.

# A Brief History

Software development began with heavy planning.

Organizations spent months gathering requirements, producing design documents, reviewing architecture, and planning implementation. This became known as the waterfall model.

The motivation was simple: software was expensive, and mistakes were even more expensive.

Unfortunately, by the time many projects were delivered, requirements had changed, stakeholders had moved on, or someone had fallen asleep during the requirements phase.

Agile emerged as an escape from this problem.

Instead of trying to predict everything upfront, teams delivered software incrementally and adapted based on feedback.

For more than two decades, Agile dramatically improved how software was built.

The question is whether the assumptions behind Agile still hold in an AI-enabled world.

# The Original Bottleneck

Most Agile practices assume that software implementation is the bottleneck.

Stories need to be estimated because engineering capacity is limited.

Backlogs need to be prioritized because teams cannot build everything.

PI Planning exists because coordination across teams is expensive.

Scrum ceremonies exist because communication failures are costly.

All of these practices make sense if software development is constrained by human effort.

But what happens when that effort changes dramatically?

# Strange Things I've Observed

Working with modern coding agents has produced several observations that feel increasingly difficult to reconcile with traditional Agile practices.

## Observation #1: Stories Are No Longer Small

Agile encourages INVEST stories:

- Independent
- Negotiable
- Valuable
- Estimable
- Small
- Testable

But modern agents routinely complete work that would have previously been considered multi-week efforts.

I've seen agents estimate a week of work and complete it in minutes.

I've seen tests generated automatically.

I've seen documentation generated automatically.

I've seen implementation plans generated automatically.

If a story that once took weeks now takes minutes, what exactly does "Small" mean?

## Observation #2: The Engineer Is No Longer the Bottleneck

Traditional Agile assumes:

```text
Story → Engineer → Code
```

AI increasingly looks like:

```text
Story → Engineer → Agent → Code
```

The engineer becomes less of a code producer and more of a reviewer, planner, and integrator.

The bottleneck shifts from typing code to managing attention.

## Observation #3: Parallel Work Becomes Natural

One of the strangest effects of coding agents is that they create idle time.

While one agent is generating code, another may be running tests.

A third may be exploring a bug.

A fourth may be creating documentation.

Many developers report managing multiple parallel streams of work simultaneously.

This creates a problem.

Agile generally discourages multitasking because context switching is expensive.

AI encourages multitasking because waiting for agents is expensive.

The assumptions conflict.

## Observation #4: We Now Have Two Sets of Stories

Agile already decomposes work into:

- Epics
- Features
- Stories
- Tasks

But agents immediately create their own decomposition:

- Plans
- Subtasks
- Execution steps
- Validation tasks

Why are humans breaking work into tasks if agents immediately break it down again?

Increasingly, it feels like we are maintaining two parallel planning systems.

# The PI Planning Problem

The biggest challenge may not be Scrum.

It may be PI Planning.

In large organizations, hundreds of people can spend days planning a quarter's worth of work.

The goal is understandable.

Teams need alignment.

Dependencies need visibility.

Release schedules need coordination.

But AI changes the equation.

In many situations today, stories can be implemented faster than organizations can finish planning them.

The bottleneck is no longer implementation.

The bottleneck is organizational coordination.

Traditional PI Planning focuses heavily on:

- Architecture
- Dependencies
- Team allocations
- Story commitments
- Delivery schedules

An AI-native planning process might focus instead on:

- Business vision
- Experiments
- Customer feedback
- Success metrics
- Cost constraints

Historically, software was expensive and ideas were cheap.

Increasingly, software is becoming cheap and good ideas are becoming expensive.

# Do We Still Need Story Points?

Story points were designed to estimate human effort.

Velocity was designed to measure human throughput.

But what exactly are we measuring now?

Human effort?

Agent effort?

Review effort?

Business complexity?

A developer using multiple coding agents may appear dramatically more productive.

But has productivity increased, or has code generation increased?

Those are not necessarily the same thing.

# From INVEST to HAI

I don't think Agile disappears.

I think it evolves.

The INVEST framework helped teams reason about human-delivered software.

Perhaps AI-enabled teams need something different.

Perhaps stories should explicitly separate:

- Human responsibilities
- AI responsibilities

I call these HAI stories.

Human and AI stories.

Instead of assuming the developer performs every step, the story identifies:

- What requires human judgment
- What can be delegated to AI
- What must be reviewed
- What must be validated

This makes ownership clearer.

It also recognizes a reality many teams are already experiencing.

Software is increasingly being built by humans and AI working together.

Our stories should reflect that.

# The New Bottleneck

I don't believe AI eliminates uncertainty.

Customers remain unpredictable.

Markets remain unpredictable.

Business strategy remains unpredictable.

What AI reduces is implementation uncertainty.

And that changes everything.

For years, software organizations optimized around producing software.

Today, many of those organizations are discovering that producing software is no longer the hardest part.

The new challenge is deciding what to build.

Agile spent twenty years optimizing software production.

AI may have reduced software production from the bottleneck to a commodity.

If that's true, then the next generation of software processes won't be optimized for implementation.

They'll be optimized for learning.

And that may require us to rethink some of our most cherished Agile practices.

