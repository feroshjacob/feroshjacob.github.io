---
title: "From Code Generation to Application Generation, Part 3: Why I Stopped Looking at Generated Code"
date: 2026-06-11
permalink: /posts/2026/06/11/from-code-generation-to-application-generation-part-3-why-i-stopped-looking-at-generated-code
categories:
  - application-generation
tags:
  - ai
  - software engineering
  - code generation
  - application generation
  - model driven engineering
  - ai agents
  - software validation
  - ai-assisted development
image: application-generation-part3.gif
excerpt: As AI coding agents improved, the generated code became less interesting than the final application outcome. The question shifted from whether the code looked right to whether the application solved the problem.
---

![Why I Stopped Looking at Generated Code](/images/application-generation-part3.gif)

In this series, I am exploring a simple idea:

> Enough of code. Let's generate applications.

The goal is not to generate more code.

The goal is to describe the problem, define success, validate the result, and let the implementation details be handled by the agent.

In [Part 1](https://feroshjacob.github.io/posts/2026/06/09/from-code-generation-to-application-generation-part-1-when-does-the-agent-stop), I wrote about the importance of success criteria. AI agents can keep improving an implementation, but unless we define what success looks like, we do not know when the work is done.

In [Part 2](https://feroshjacob.github.io/posts/2026/06/10/from-code-generation-to-application-generation-part-2-i-knew-how-to-fix-it), I wrote about validation. If we want agents to move beyond writing code and start producing applications, we need broader validation gates that check functionality, technical readiness, usability, UI standards, mission coverage, and project-specific risks.

This article is about the next shift.

Outcome.

# Starting in the IDE

Like many AI adopters, I started by using coding agents inside an IDE.

The workflow felt familiar.

The agent generated code.

I reviewed the code.

I found issues.

Sometimes I found violations of basic software engineering principles and felt smart for catching them.

At that stage, it was easy to think of the agent as an intern who could type extremely fast.

It could produce a lot of code quickly, but I still felt responsible for reviewing every important decision.

That was probably reasonable in the beginning.

Some of the early models made mistakes that experienced engineers would immediately notice. They could structure code poorly, miss edge cases, duplicate logic, or make questionable decisions around configuration.

Occasionally, they would do things that made me nervous, such as attempting to include local environment files in a repository.

So I watched the code closely.

# The Models Got Better

It did not take long for me to realize that some of my concerns were tied to the quality of the models I was using.

As the models improved, many of the obvious software engineering mistakes became less common.

The newer agents became better at:

- organizing code
- following existing patterns
- writing tests
- respecting configuration boundaries
- understanding deployment workflows
- fixing build failures
- avoiding obvious bad practices

They are not perfect.

But they improved quickly enough that my attention started moving elsewhere.

I became less interested in whether every generated function looked elegant.

I became more interested in whether the application worked.

Could it deploy?

Could users complete the workflow?

Did validation pass?

Did the output match the mission?

That was a very different way to think about software delivery.

# Production First, Usability Next

For a while, production issues were the obvious priority.

If the application did not build, deploy, or run correctly, nothing else mattered.

Once agents became better at handling those issues, new problems became more visible.

Usability became one of them.

A generated application may technically work but still frustrate the user.

A form may validate input but erase everything the user already typed.

A button may look clickable but do nothing useful.

Two buttons may perform nearly the same action and confuse the user.

A search feature may return results in 500 milliseconds, but slightly better results in 700 milliseconds may create a better experience.

Before AI coding agents, automated usability validation felt less urgent because we were still fighting basic implementation and production issues.

Once the agents started handling more of those problems, usability became harder to ignore.

The question was no longer only:

> Does the code work?

The question became:

> Can a real user accomplish the goal without unnecessary frustration?

# Code Used to Be the Delivery Artifact

It may seem strange to say that an article about outcomes starts with why I stopped looking at code.

For most of software history, code felt like the primary delivery artifact.

Yes, the application was the real product.

But because humans spent so much time writing, reviewing, organizing, and maintaining code, the journey became almost as important as the destination.

Code reviews mattered.

Architecture mattered.

Naming mattered.

Structure mattered.

These things still matter.

But AI changes the balance.

When business users can move quickly from an idea to a working application, the final output becomes much more important than the intermediate implementation.

The code becomes less like the product and more like a generated artifact.

It is still necessary.

It still has to be correct.

It still has to be safe.

But it is no longer always the best place for humans to spend their attention.

# The Code Is Still There

I am not saying code does not matter.

Bad code can still create real problems.

Security vulnerabilities matter.

Performance problems matter.

Maintainability matters.

Operational reliability matters.

But if the agent can generate code, run tests, validate behavior, check deployment, scan for issues, and iterate on failures, then the human does not need to inspect every file manually.

The human should inspect the outcome.

That means reviewing:

- the application behavior
- the user workflow
- the validation results
- the mission coverage
- the remaining risks
- the business outcome

In traditional software development, we reviewed code because code was the most concrete representation of the system.

In application generation, validation becomes the concrete representation of readiness.

# Why Is Code Text?

This made me wonder about something more basic.

Why is code text?

Historically, the answer is obvious.

Humans needed to read it.

Humans needed to write it.

Humans needed to review it.

Text was the right interface between human intent and machine execution.

But agents change that assumption.

Machines already understand binary formats.

Humans understand text.

AI agents now understand text in a way that is much closer to how humans use it.

Because most software artifacts are text, agents learned from text.

Code, configuration files, pipelines, scripts, logs, documentation, and tests are all written as text.

So, naturally, agents generate more text.

That is the phase we are in now.

But it may not be the final phase.

It is possible that future systems will use formats that are optimized more for machines than for humans. These formats may not look like programming languages as we understand them today.

They may be harder for humans to read but easier for agents to manipulate, validate, and transform.

That is probably a separate article.

For now, the important point is simpler.

If agents are becoming responsible for generating and maintaining implementation details, then humans may not need to treat generated source code as the primary artifact anymore.

# Code Becomes a Second-Class Citizen

Manual coding brought us here.

We should not dismiss it.

Every programming language, framework, build tool, deployment pipeline, and testing practice that agents use today came from decades of human software engineering.

AI agents learned from that world.

But the role of code is changing.

In manual software engineering, code was the center.

In AI-assisted application generation, code becomes one layer in a larger system.

The mission matters.

The success criteria matter.

The validation strategy matters.

The user outcome matters.

The code supports those things.

It does not replace them.

That is why I stopped looking at generated code as much as I used to.

Not because I trust every line blindly.

But because I care more about whether the application fulfills the mission.

# From Reviewing Code to Reviewing Outcomes

This is the larger shift.

The engineer's job is moving from reviewing implementation details to reviewing application outcomes.

Instead of asking:

> Does this code look right?

We increasingly ask:

> Does this application solve the problem?

Instead of asking:

> Did the agent generate clean files?

We ask:

> Did the validation gate pass?

Instead of asking:

> Is this function elegant?

We ask:

> Can the user complete the workflow?

Instead of asking:

> Did the agent follow my preferred coding style?

We ask:

> Is the mission covered?

This does not eliminate engineering judgment.

It moves engineering judgment to a higher level.

# What Comes Next?

The first three articles introduced the core ideas behind this series.

Part 1 focused on success criteria.

Part 2 focused on validation.

Part 3 focused on outcomes.

The next articles will move from the idea to the implementation.

I will walk through how I started building an MDE-based framework that helps agents generate applications, not just code.

The goal is to make the mission, validation strategy, generation process, and application outcome first-class artifacts.

Because if we are serious about application generation, we need more than better coding agents.

We need a better operating model for building software with them.
