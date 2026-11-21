---
title: "Part 6: ChatGPT Helps You Shop. Claude Helps You Build."
date: 2026-11-28
permalink: /posts/2026/11/28/ai-search-vs-seo-part-6-chatgpt-helps-you-shop-claude-helps-you-build
categories:
  - ai-search-vs-seo
series: ai-search-vs-seo
series_order: 6
tags:
  - ai search
  - seo
  - aeo
  - geo
  - ecommerce
  - chatgpt
  - claude
  - chatgpt shopping
  - agentic commerce
  - ai shopping agents
  - retail
image: /images/ai-search-vs-seo-part-6.png
excerpt: "I ran Part 4's eight product searches in ChatGPT on three evenings. ChatGPT sent shoppers to stores: 56 of its 92 links went to stores, 28 to the maker's own site, 2 to review sites, none to Amazon. In Part 4, Claude read reviews instead. Separately, Anthropic has open-sourced a blueprint for agents merchants run on their own sites. My framing: ChatGPT helps you shop; Claude helps you build. What that means for a small seller."
---

<p class="align-center">
  <img src="/images/ai-search-vs-seo-part-6.png" alt="A small storefront split down the middle. The left half is a finished shop in autumn, with pumpkins at the door, a pie in the window and two shopping bags carrying a tag that reads '56 of 92 links went to stores', under the words 'ChatGPT helps you shop.' The right half is the same shop drawn as a blueprint with a toolbox and the notes 'Open-source blueprint', 'The agent runs on your own site' and 'Checkout stays with you', under the words 'Claude helps you build.'" style="max-width: 100%; height: auto;" />
  <br><em>One store, two halves: ChatGPT sent shoppers to stores in my test; Anthropic's blueprint, as reported, is something a merchant builds with.</em>
</p>

## The short version

In this test, ChatGPT acted as a shopping layer: it sent shoppers to stores. I ran the eight product searches from [Part 4](https://feroshjacob.github.io/posts/2026/11/08/ai-search-vs-seo-part-4-ask-for-a-product-google-shows-stores-claude-reads-reviews) in ChatGPT on three evenings. Of its 92 links, 56 went to stores, 28 to the maker's own site and 2 to review sites. None went to Amazon, the store Google put in its top five on 18 of 24 pages.

In Part 4, Claude, through Anthropic's API, read reviews: 313 of the 367 sites it opened. It named a store in 20 of 72 answers. That is shopping research, not building; the "build" half rests on one outside report, below. My framing, not a finding: **ChatGPT helps you shop. Claude helps you build.**

## ChatGPT helps you shop

ChatGPT answered with product cards that link out. Walmart (12 links), Home Depot (12) and Best Buy (11) led. Its text named Amazon as a store in 3 of 27 answers, always in a list of several, but it never linked to Amazon, and Amazon was not among the 202 source links it listed.

<p class="align-center">
  <img src="/images/ai-search-vs-seo-part-6-chart.png" alt="Bar chart titled 'A shopper asks for a product: where does it lead?'. For eight product searches over three evenings: Google's top five links were 18 Amazon, 36 other stores, 34 maker sites, 23 review sites and 7 others, 118 in all. Bing's were 10 Amazon, 41 other stores, 15 maker sites, 19 review sites and 4 others, 89 in all. The sites Claude read were 0 Amazon, 32 other stores, 17 maker sites, 313 review sites and 5 others, 367 in all. ChatGPT's links were 0 Amazon, 56 other stores, 28 maker sites, 2 review sites and 6 others, 92 in all." style="max-width: 100%; height: auto;" />
  <br><em>Where a shopper's product question leads. Google, Bing and Claude from Part 4 (4 to 6 November 2026); ChatGPT eleven days later (15, 16 and 18 November 2026).</em>
</p>

| Where the search leads | Google, top 5 links | Bing, top 5 links | Claude, sites it read | ChatGPT, links it gave |
|---|---|---|---|---|
| Amazon | 18 | 10 | 0 | 0 |
| Big-box chains (Walmart, Best Buy, Home Depot…) | 18 | 27 | 1 | 38 |
| Other stores | 18 | 14 | 31 | 18 |
| Maker's own site | 34 | 15 | 17 | 28 |
| Reviews, news, blogs | 23 | 19 | 313 | 2 |
| Other | 7 | 4 | 5 | 6 |
| **Total** | **118** | **89** | **367** | **92** |

Google and Bing count every top-five link; Claude each site once per answer; ChatGPT every link. Its 6 "other" links reopened ChatGPT.

**It cites reviewers but links to stores.** Asked for its sources, ChatGPT gave 102 sites, counting a site once per answer; 40 were review, news or blog sites. The reviewers get the citation; the store gets the click.

**The store on the card is not always the store you get.** 63 links named a store on the card or its button; 16 went somewhere else, 12 to another store or the maker and 4 to a new ChatGPT search. A card marked "Target" opened Crate & Barrel.

**It changes from evening to evening.** On Part 4's overlap score (1.0 means the same sites every evening), ChatGPT's links scored 0.33, against 0.71 for Google and 0.70 for Bing.

**It is not a copy of search.** Of the 36 store or maker sites ChatGPT linked, 18 were in none of Google's, Bing's or Claude's results for the same search.

## Claude helps you build

My data says nothing about building; this half rests on one report. As [Linas Beliūnas describes it](https://linas.substack.com/p/claude-commerce-agents), on 2 September 2026 Anthropic open-sourced a blueprint for building shopping agents and merchant-operations agents on Claude. The agent's code lives inside the merchant's own website, app and back office, and checkout, payment and customer ownership stay with the merchant. In his words, "shoppers discover in AI but buy on the merchant's property".

## The limits

ChatGPT ran eleven to twelve days after Part 4's runs, so prices and stock differ; I compare stores, not products. One ChatGPT answer per search per evening (27), against Claude's 81. Claude was Anthropic's API with web search, not the Claude app. ChatGPT: Free plan, signed in, a temporary chat with web search and memory on. On three answers the cards opened a side panel of stores; I recorded the first store listed (14 of the 92 links). For "tires near me", ChatGPT drew its own map (Mapbox), not Google's map pack. Store categories are my own sorting; method as in [Part 4's "How I measured"](https://feroshjacob.github.io/posts/2026/11/08/ai-search-vs-seo-part-4-ask-for-a-product-google-shows-stores-claude-reads-reviews#how-i-measured). I did not test Anthropic's blueprint; I rely on Beliūnas's account. Eight searches show a direction, not a law.

## What can a small seller do?

**Be findable where ChatGPT shops:**

- **Get your products into ChatGPT.** As [Part 5](https://feroshjacob.github.io/posts/2026/11/15/ai-search-vs-seo-part-5-how-can-i-sell-products-through-chatgpt) found, Shopify and Etsy catalogs are already connected; others apply at [chatgpt.com/merchants](https://chatgpt.com/merchants/).
- **Keep your store pages and feed complete.** 56 of 92 links went to store pages and 28 to makers' own sites; none to Amazon.
- **Get in front of reviewers.** 40 of the 102 sites ChatGPT cited were review, news or blog sites.
- **Click through your own products in ChatGPT.** 16 of 63 store-named links went elsewhere.

**Consider building on your own site:**

- **An assistant on your own site is an option, not a must.** In the blueprint Beliūnas describes, the agent lives in the merchant's site and checkout stays with the merchant. It needs a developer.

## What's next

Part 4 left ChatGPT out of the product test; this part fills that gap.

Happy Thanksgiving weekend, and thank you for reading.
