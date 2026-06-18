---
title: "From Code Generation to Application Generation, Part 5: Why I Ended Up Looking Back at Model-Driven Engineering"
date: 2026-06-18
permalink: /posts/2026/06/18/from-code-generation-to-application-generation-part-5-model-driven-engineering
categories:
  - application-generation
series: application-generation
series_order: 5
tags:
  - ai
  - software engineering
  - code generation
  - application generation
  - model driven engineering
  - mission-driven development
  - ai agents
  - software validation
  - ai-assisted development
image: /images/application-generation-part5-mde.png
excerpt: "AI application generation feels new, but it echoes Model-Driven Engineering: humans describe intent, machines generate implementation, and independent validation decides whether the result actually works."
---

![Mission-driven development loop inspired by Model-Driven Engineering](/images/application-generation-part5-mde.png)

In [Part 4](https://feroshjacob.github.io/posts/2026/06/16/from-code-generation-to-application-generation-part-4-my-coding-agents-are-productive-i-am-exhausted), I described the human cost of supervising multiple coding agents at once.

Over the last few articles, I described several challenges I encountered while building applications with AI coding agents.

The problems were not related to code generation itself.

Modern coding agents can write code surprisingly well.

The real challenge was everything surrounding the code:

- Knowing when to stop
- Determining what should be fixed
- Validating whether the application actually works
- Managing multiple parallel work streams
- Preventing the human from becoming the bottleneck

As I worked through these challenges, I realized something interesting.

The solution might not be entirely new.

In fact, parts of it have existed for decades.

That realization led me back to a field I had not thought deeply about in years:

**Model-Driven Engineering (MDE).**

## The Core Idea

AI application generation feels new, but it is revisiting an old software engineering question: what should be the primary artifact?

In traditional development, the primary artifact was source code.

In Model-Driven Engineering, the primary artifact was the model.

In AI-assisted application generation, I suspect the primary artifact becomes the mission: the intent, constraints, expectations, and validation rules that define whether an application works.

The code still matters.

But the code is no longer the only thing being produced.

The mission, validation process, test evidence, deployment behavior, and accumulated lessons become part of the system too.

## We Have Seen This Movie Before

Many engineers view AI-generated code as something completely new.

But generating code from higher-level descriptions is not new at all.

For decades, Model-Driven Engineering attempted to raise software development above the level of source code.

Instead of treating code as the primary artifact, MDE treated **models** as the primary artifact.

Developers would define a model describing a system.

Tools would then generate source code, configuration files, deployment descriptors, documentation, tests, and other artifacts from that model.

Sound familiar?

Replace "model" with "prompt" and the similarities become obvious.

The difference is that AI systems are dramatically better at filling in missing details than traditional generators ever were.

Yet the fundamental idea remains the same:

> Humans describe intent. Machines generate implementation.

## Models, Meta-Models, and Why They Matter

To understand why MDE became relevant again, we need to understand one of its core ideas.

## What Is a Model?

A model describes a specific system.

For example:

```text
Customer
    Name
    Email

Order
    Date
    Total

Customer places Order
```

This is a model of a business domain.

It describes what exists, but not necessarily how it is implemented.

## What Is a Meta-Model?

A meta-model describes how models themselves are structured.

For example:

```text
Entity
    has Attributes

Relationship
    connects Entities
```

The meta-model defines the rules that every valid model must follow.

You can think of it as a grammar for building models.

A model conforms to a meta-model.

## The Interesting Part

Eventually researchers realized something fascinating.

A meta-model can describe itself.

In other words:

```text
Meta-Model
    describes Models

Meta-Model
    is itself a Model
```

This creates a recursive system where the framework used to describe models can itself be represented within the framework.

At first glance this seems academic.

For AI-generated applications, however, it becomes extremely practical.

Because our development process must evolve as we learn.

The framework cannot remain static.

It must be able to describe and improve itself.

## Why Traditional MDE Was Not Enough

If MDE already existed, why did we not simply use it?

Because traditional MDE had limitations.

The models had to be extremely precise.

Generators were rigid.

Every new requirement usually required modifying the framework itself.

In practice, many teams spent more effort maintaining models and generators than building software.

AI changes that equation.

Large language models can fill in gaps that previously required explicit specifications.

Instead of generating code from a perfectly defined model, they can generate code from intent.

That changes the role of the model.

The model no longer needs to describe everything.

It only needs to describe what matters.

## From Model-Driven Engineering to Mission-Driven Development

This observation led me to what I currently call **Mission-Driven Development (MDD).**

The central artifact is no longer source code.

It is not even the application model.

It is the mission.

The mission captures:

- What problem we are solving
- Success criteria
- Constraints
- User expectations
- Validation requirements

As development progresses, the mission evolves through mission updates.

The application becomes a continuously improving implementation of that mission.

The code is simply one generated artifact among many.

## The Core Loop

The framework I have been evolving follows a surprisingly simple loop.

1. Define the mission.
2. Capture mission updates.
3. Generate a fresh implementation.
4. Validate independently.
5. Repeat until validation succeeds twice without modification.

The important part is that validation is independent from generation.

A generator should never be trusted to evaluate its own work.

Instead, fresh validation passes must continuously challenge the implementation.

Over time, the mission becomes richer, validation becomes stronger, and the generated application becomes more reliable.

## Why This Felt Familiar

The more I worked on this approach, the more it reminded me of MDE.

Not because I was generating code.

We have generated code for years.

What felt familiar was the idea that software development is fundamentally about managing increasingly higher levels of abstraction.

Source code used to be the primary artifact.

Then models became primary artifacts.

Now missions may become primary artifacts.

The implementation details continue moving farther away from human attention.

## Looking Ahead

This article intentionally stayed conceptual.

The goal was not to explain the implementation.

The goal was to explain why I started looking backward before moving forward.

Many of the ideas that appear revolutionary in the age of AI have roots in decades of software engineering research.

Model-Driven Engineering provided many of the concepts.

AI makes those concepts practical at a scale that was previously difficult to achieve.

In the next article, I will walk through the actual implementation architecture of Mission-Driven Development:

- Mission and mission updates
- Generation pipelines
- Independent validation
- Two-pass verification
- Fresh agent generations
- Feedback loops
- Self-improving development workflows

The goal is not merely to generate code.

The goal is to generate applications that can prove they work.
