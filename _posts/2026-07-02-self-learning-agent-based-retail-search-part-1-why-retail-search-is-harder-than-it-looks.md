---
title: "Self-Learning Agent-Based Retail Search, Part 1: Why Retail Search Is Harder Than It Looks"
date: 2026-07-02
permalink: /posts/2026/07/02/self-learning-agent-based-retail-search-part-1-why-retail-search-is-harder-than-it-looks
categories:
  - self-learning-agent-based-retail-search
series: self-learning-agent-based-retail-search
series_order: 1
tags:
  - retail search
  - ecommerce search
  - search relevance
  - product discovery
  - ai agents
  - mission-driven engineering
  - information retrieval
  - ranking
image: /images/self-learning-agent-based-retail-search-part-1.png
excerpt: "Retail search is not just about returning relevant products. The hard part is deciding which relevant product should come first when customer needs, business goals, inventory, promotions, trust, and mobile behavior all compete."
---

![Specialized AI bots working on query understanding, normalization, indexing, ranking, and evaluation for retail search](/images/self-learning-agent-based-retail-search-part-1.png)

I have been teaching a graduate-level Information Retrieval course since 2018. One of my favorite first-day questions is not about indexes, embeddings, query parsers, or evaluation metrics.

It is this:

> Assume every product returned by a retail search engine is semantically relevant. Ignore personalization for now. Which product should appear first?

Then I give the class a few choices.

1. The product with the highest customer engagement during the last X days.
2. The product with the biggest discount.
3. The product with the highest profit margin.
4. The product with the highest customer rating or review score.

I like this question because every answer is defensible.

## The Core Idea

Retail search is not just retrieval. Relevance is only the beginning.

A product can match the query and still be the wrong product to show first. The top result is a customer-experience decision, a business decision, and a trust decision at the same time.

Highest engagement may reflect what shoppers currently want. Biggest discount may help move inventory or drive conversion. Highest margin may be important to the business. Highest ratings may reduce customer anxiety and improve trust.

The problem is that each choice optimizes for a different objective.

Engagement can reward popularity, but popularity can also be biased by yesterday's ranking. Discounts can create urgency, but overusing them can make search feel like a clearance rack. Margin matters, but ranking only for margin can damage trust. Ratings help, but a 4.9 rating from 12 reviews is not the same as a 4.6 rating from 5,000 reviews.

So which answer is correct?

My preferred answer has always been:

> All of the above.

That answer is not meant to be clever. It exposes the real problem: retail search is a balancing act. A good system has to weigh customer satisfaction, business objectives, merchandising, inventory, promotions, and long-term trust.

## The First Result Matters More On Mobile

This matters more now because shopping is increasingly mobile.

On a desktop, customers can scan many products at once. On a phone, the first product may occupy most of the screen. The customer may see one product card, part of a second product card, and a filter button.

That gives the top result enormous power. If it feels right, the customer continues. If it feels wrong, the customer may refine the query, open filters, leave the site, or assume the store does not understand them.

## We Ignored Personalization On Purpose

The opening question intentionally ignored personalization, and the problem was still complicated.

Now add customer context.

For "water heater," the right result may depend on gas versus electric, home compatibility, and whether the customer is repairing or replacing. For "windshield washer fluid," local weather may matter. For "running shoes," previous purchases, terrain, size availability, and brand preference may all change the answer.

Personalization does not remove the ranking problem. It makes the balancing problem harder.

## What This Series Will Do

This series is about building a production-quality retail search system using AI agents and Mission-Driven Engineering.

For years, search teams have handled complexity by adding more components: query understanding, synonym handling, normalization, indexing, ranking models, dashboards, evaluation jobs, and rules. Some of that is necessary. But it is easy to start with the architecture and then ask what business problem each component solves.

What if we started with the business goal instead?

We will begin with public datasets, starting with Cranfield for controlled experiments and later moving toward retail datasets such as Amazon ESCI. The work will follow the [Mission-Driven Engineering approach from my earlier series](/series/application-generation/): define the desired outcome, let agents propose changes, and use validation to decide whether the system actually improved.

I do not know what architecture the agents will eventually build, and that is what makes the project interesting.

> If agents are allowed to improve the system through measured experiments, what kind of search architecture will actually emerge?

In the next article, we will look at how retail search systems are traditionally built before we start challenging those assumptions with agents, missions, and evidence.
