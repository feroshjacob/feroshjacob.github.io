---
title: 'Artificial Intelligent Retail Search is here! (1) - The Legacy & The Problem'
date: 2026-01-31
permalink: /posts/2026/01/31/artificial-intelligent-search-1
categories:
  - retail-search
tags:
  - search
  - ai
  - retail search
  - ecommerce search
  - search relevance
  - product discovery
  - endeca
excerpt: A look back at the "Black Box" era of retail search, why we moved away from it, and the fundamental tension between finding products and making money.
---

![Endeca/WEX](/images/endeca.png)

## The Real Problem Statement

The goal of retail search sounds deceptively simple: **Help the customer find what they want.**

In practice, this is a minefield. Customers often don't know what they want, or they use vague language. Worse, what they *think* they want often conflicts with what the business needs to sell. We can't simply rank the most expensive items at the top—users will bounce immediately. Conversely, if we give them exactly what they asked for too efficiently, we might miss the opportunity to cross-sell or inspire discovery.

Beyond the psychological tug-of-war, there is the data reality. Catalogs are messy. With thousands of suppliers constantly onboarding and retiring SKUs, even the best teams struggle with misclassifications, missing attributes, and "dirty" data.

The actual goal isn't just retrieval; it's a balancing act: **Connect the user to the right product while ensuring the underlying economics of the business survive.**

## A Brief History of Search (and why Retail is different)

Search is as old as the internet. We had inverted indices in libraries long before we had a World Wide Web. While academic interest in general web search waned as Google and Bing established dominance, retail search remained a distinct, thorny beast.

For the average user, Google set an impossibly high bar. We trust Google so implicitly that if a search fails, we assume we made a typo. Customers bring that same expectation to online retail stores. They expect Google-level fault tolerance, speed, and semantic understanding from a shoe store’s search bar.

Meeting that bar was expensive.

This series walks through the evolution of retail search—from the "Black Box" legacy systems to the cloud era, and finally to the AI revolution. It is difficult to put precise dates on these phases because the industry is uneven; some retailers are still running on tech from 2010, while others are bleeding edge.

But as AI advances, we are approaching a point where the historic friction of search might simply evaporate—much like how the transition from horses to cars solved the problem of manure in the streets. We traded one set of problems for another, but the old mess is gone.

## The Data: A Practical Example

Before we look at the legacy tech, let's establish the data we will be using throughout this series to benchmark our systems. Theoretical discussions are fine, but search is best understood through edge cases.

I’ll be using a dataset of recipes collected from the web (MXP and FXP formats). While this is a "Recipe" search, the structural challenges mirror retail product catalogs perfectly: messy text, varying attribute structures, and subjective categorization.

You can grab the sample data [here](https://github.com/recipegrace/RecipeGrace/blob/master/core/resources/data.zip).

Here is a raw MXP example:

```text
Recipe By     :"xx" <xx@xx.com>
Serving Size  : 1     Preparation Time :0:00
Categories    : Daily Bread Mailing List        Miscellaneous & Tips


  Amount  Measure       Ingredient -- Preparation Method
--------  ------------  --------------------------------
  3             pounds  apples
  1                cup  apple cider -- to 2 cups
  1                cup  sugar -- more or less

I make apple butter in my crock pot and it is very easy and very tasty...
```

Our goal is to handle queries like **“breakfast recipes,” “chicken curry,”** or **“easy chicken and rice.”**

So, how did the "Old Guard" systems handle this?

## The Legacy Era: Endeca, WEX, and the Black Box

I recently asked a few veteran search engineers what they used in the early days of e-commerce. The consensus was murky, but many recalled using out-of-the-box search modules bundled with application servers like IBM WebSphere (the ancestor of Watson Explorer/WEX).

However, in my experience, one name stood above the rest: **Endeca**.

I was part of a team that eventually migrated off Endeca, but I still have respect for it. It was the standard-bearer for a reason.

### The "Black Box" Experience
The defining characteristic of this era was the "Black Box." We had very little visibility into *how* the engine ranked results. We fed data in one side, and results came out the other.

Engines like Endeca provided tools to manually curate rules—"people who search for *X* should be boosted toward *Y*." But ultimately, being a Search Engineer in this era was less about programming (Java/Python) and more about **Operations and Configuration**. We were essentially specialized DevOps engineers tweaking XML files and proprietary dashboards. The teams were small because there wasn't much code to write.

### Why did we migrate?
If Endeca worked, why did the industry move to open-source solutions like Solr and Elasticsearch?

1.  **Scale & Stability:** As traffic surged, the dreaded "Black Friday Site Outage" became a career-ending risk for CTOs. Retailers needed horizontal scalability that legacy proprietary systems struggled to provide cost-effectively.
2.  **Control:** Retailers wanted to own their destiny. The "Black Box" was too limiting. We wanted to tweak the retrieval algorithms, experiment with semantic search, and control the ranking logic directly.

Interestingly, many of the initial migrations were "lift-and-shift." We replaced a proprietary engine with Solr or Elasticsearch but often replicated the exact same logic. In some cases, performance actually dipped initially. We accepted that short-term pain for the long-term gain of owning our infrastructure.

Now that we've covered the legacy landscape, the [next post](posts/2026/02/01/artificial-intelligent-search-2) will dive into the "Builder's Era"—how we used open-source tools to finally take control of the search bar.
