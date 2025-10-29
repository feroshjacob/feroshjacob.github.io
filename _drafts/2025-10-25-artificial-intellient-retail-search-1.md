---
title: 'Artificial Intelligent Retail Search is here! (1) - Problem and Legacy '
date: 2025-10-25
permalink: /posts/2025/10/25/artificial-intelligent-search-1
tags:
  - search
  - ai
  - retail search
excerpt: In this article, I explore the evolution of retail search and attempt to predict the future of retail search
  
---

![Endeca/WEX](/images/endeca.png)

## Problem stetement

At first glance, the goal of retail search seems simple: help the customer find the product they’re looking for. But in practice, it’s anything but simple. Sometimes, customers themselves aren’t sure what they want. Other times, what they think they want may not align with what the retailer aims to promote or sell. Of course, we can’t just place the most expensive products at the top of the search results — customers won’t buy, and they certainly won’t return for their next purchase. On the other hand, if we satisfy their needs too perfectly — say, by helping them find exactly what they need right away — they might not come back to explore more.

Beyond understanding customer intent, we must also understand the products we’re selling. With thousands of suppliers constantly onboarding and retiring products, even well-supported catalogs face persistent challenges — misclassifications, duplicate or missing attributes, inconsistent units and values, incomplete data, and more. So, the traditional goal of “helping the customer find the product” is incomplete without considering the business dimension and the underlying data work required to build a great search experience.

 To put it more precisely: We need to help the customer find the right product — while also driving sustainable business value.

## Beginning 
I would assume that search particularly retail search  has existed since the very beginning of the internet. Of course, the idea of search itself isn’t new; we had inverted indices in libraries long before the web existed.    Web search was one of the earliest and most exciting areas of research. With the rise and eventual domination of Google (and to some extent, Bing), it has been quite some time since we’ve seen significant new academic work in this space.

Interestingly, we’re now seeing a renewed interest in internet-scale data — not for indexing and searching this time, but for training large language models. Companies like Google, Microsoft, and others continue to invest heavily in search to make our experience more seamless and intuitive. To be honest, I can’t remember the last time I went to the second page of Google search results. Most of us have come to trust it so much that if we don’t find what we need, we often blame ourselves before blaming Google.

The story, however, has been very different for retailers. Building a powerful, intuitive, and scalable search experience was expensive and challenging. Customers, accustomed to Google-like precision and speed, expected the same experience from every online retailer — a bar that was incredibly hard to meet.


This article series walks through the evolution of retail search — from legacy systems to the cloud era — and explores how it continues to adapt to meet customer expectations. Of course, not every online retailer has followed the same path. Some may have skipped certain stages entirely, especially those rebuilding their search platforms in recent years. It’s also worth noting that there have always been front-runners and followers, influenced by financial conditions, leadership priorities, and broader market trends. Because of these differences, it’s difficult to assign precise timelines to each phase of this evolution. The article series closes with a reflection: as artificial intelligence continues to advance, retail search may soon reach a point where many of its long-standing challenges simply disappear — much like how we no longer worry about cleaning horse dung off the streets once cars replaced horses. 

In this article, we take a quick look back at some of the earlier or legacy implementations of retail search.


## Legacy Search Systems (Endeca, WEX, etc.)
I reached out to a few of my “old” search friends to find out what systems they used for product search in the early days — the time between when websites first went live and before the adoption of the well-known legacy search engines. I couldn’t get a definitive answer, but the general guess was that many relied on an out-of-the-box solution from their application server software, say  IBM WebSphere, which was likely the predecessor to Watson Explorer (WEX) — one of the legacy search engines included in this analysis.
From my own experience, though, the most popular and influential retail search engine — without question — was Endeca. I was part of a team that migrated from Endeca to cloud-based solutions, and I can say with confidence: Endeca deserves a lot of respect.

### What were our projects?

Most of the legacy systems  were essentially black boxes — we had little visibility into how search was implemented or how results were ranked. Typically, we were given options to specify the data sources for search and a few levers to tweak the ranking of products in the output. Sophisticated engines like Endeca offered reliable mechanisms for defining rules and incorporating customer engagement signals ( “people who searched for these terms ended up buying these products”).  But ultimately, being a subject matter expert in a legacy search system meant being more of a DevOps engineer than a Java or Python programmer. The tuning options were limited, and as a result, search teams were small — focused more on system configuration and operations than on algorithmic experimentation.


### Why did we migrate?

This shift was largely a technological decision aimed at supporting growing traffic and improving site stability. As online shopping surged, scalability became a serious concern — no retailer wanted to end up on the dreaded “Black Friday site-down” list. In some cases, large retailers sought greater control over retrieval and ranking, preferring more customizable, cloud-based search solutions like Solr and Elasticsearch over the more black-box systems offered by Google and Microsoft. This claim opened the door to personalized and semantic search, aligning technical migration with business goals. However, most of these migrations were “lift-and-shift” in nature — delivering improved infrastructure resilience but little immediate impact on sales. In some cases, there was even a slight dip in short-term performance, accepted as the price of a more scalable and adaptable future.

In this article, we provided a high-level overview of the retail search problem and examined some of the earlier approaches and tools used to tackle it. In the [next article](posts/2025/10/27/artificial-intelligent-search-2), we’ll explore the tools and methodologies that emerged to address the limitations of these legacy solutions.