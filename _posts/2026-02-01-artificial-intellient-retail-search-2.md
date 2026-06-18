---
title: 'Artificial Intelligent Retail Search is here! (2) - The Data Science Era'
date: 2026-02-01
permalink: /posts/2026/02/01/artificial-intelligent-search-2
categories:
  - retail-search
series: retail-search
series_order: 2
tags:
  - search
  - ai
  - retail search
  - ecommerce search
  - search relevance
  - query understanding
  - data science
excerpt: Moving beyond simple keyword matching. How the industry transitioned to hybrid systems, query understanding, and the "Builder's Era" of search relevance.
---

![Elastic/Solr](/images/elastic.png)

In the previous post, we looked at the "Black Box" era of search. Those legacy systems lived and died by **lexical similarity**—matching the exact characters a user typed to the exact characters in a document. 

While that works for a user who knows the exact name of a dish, it fails the average home cook. In a world where one person searches for "mandy" and another for "mandhi" or "yemeni rice," lexical matching is a blunt instrument. To fix this, we entered an era of "hybrid" systems: combining the raw speed of keyword search with the nuance of semantic intent.

## The Builder’s Era: Open Source & Cloud Search

The rise of Solr, Elasticsearch, and managed services (Google Cloud Search, Azure Cognitive Search) changed the game. For the first time, developers could actually *build* their own search stacks. Even those using managed APIs began building custom wrappers to handle complex routing, personalization, and domain-specific tuning.

The focus shifted from "How do we keep the engine running?" to "How do we make the results relevant?"



To understand the complexity here, let’s look at the "relevancy stack" in the order a query actually travels through the system using our recipe dataset.

### 1. The Pre-Search Layer: Autocomplete & Suggestions
These are the "front door" of search. Whether powered by simple search logs or advanced Deep Learning models, their job is to guide the user. By nudging a user toward a "high-performing" query (e.g., suggesting "Classic Chicken Curry" when they type "chick..."), we solve the relevancy problem before the search even starts.

### 2. Query Cleaning: Normalization & Synonyms
Users are messy. They misspell ingredients, use plurals inconsistently, and swap word orders. 
* **Normalization:** Handling casing and resolving "surface forms" (e.g., "BBQ Chicken" vs "Barbecue Chicken").
* **Synonym Expansion:** If a user searches for "cilantro," but your recipe uses the term "coriander," you have a recall problem. Building and maintaining these synonym libraries—linking "capsicum" to "bell pepper"—is a massive, ongoing effort.

### 3. Query Understanding: Entity Recognition
This is the "crown jewel" of classical search. If your recipe catalog classifies data by **Course**, **Protein**, and **Cuisine**, your search engine needs to translate raw text into those fields.
* **User Query:** "Spicy Italian pasta with shrimp"
* **Entity Mapping:** Flavor: `Spicy` | Cuisine: `Italian` | Category: `Pasta` | Main Ingredient: `Shrimp`
A strong normalization layer makes this mapping much easier, turning chaotic user intent into structured data points that the engine can filter precisely.

### 4. The Signal Layer: User Engagement
Retrieving a recipe isn't enough; you need to know if it's actually "good." We started passing behavioral signals into the retrieval engine to ensure that recipes with high ratings, saves, or print rates naturally rose to the top.
This involves complex modeling to:
* Remove **position bias** (the first recipe gets clicked more just because it's first).
* Account for **seasonality** (showing "Pumpkin Pie" in October, not July).
* **Personalize** based on the user’s dietary preferences (e.g., boosting vegetarian options for a specific user).

### 5. The Retrieval Engine & The Data Mess
Once the query is structured and the signals are ready, it hits the retrieval engine. The engine’s success depends entirely on the quality of the data it’s searching. 

Recipe data is notoriously dirty. In our MXP and FXP datasets, you’ll find duplicate fields, inconsistent units (3 pounds vs. 1.5 kg), and "lazy" classifications. Even a mid-sized recipe site with 3,000 tags is essentially facing a massive, multi-label classification problem. Before you can have great search, you must have a "Data Cleaning" pipeline that standardizes your ingredients and instructions.



---

### Why are we migrating again?

If we built all these sophisticated layers—normalization, entity recognition, and behavioral signals—why is the industry moving again?

The "Builder's Era" hit a ceiling. These systems are **static**. They rely on manual rules, siloed data, and a limited understanding of "vibe" or context. They struggle with queries like "something light for a summer evening" because the engine is looking for keywords, not "feelings." 

We’ve reached the limits of what "hand-crafted" search can do. In the [final article](posts/2026/03/01/artificial-intelligent-search-3), we’ll look at how LLMs and Vector Search are finally making the "Horse Dung" problem of recipe search a thing of the past.
