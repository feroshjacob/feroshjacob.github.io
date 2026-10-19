---
title: "Part 1: Why I Am Running These Experiments"
date: 2026-10-18
permalink: /posts/2026/10/18/ai-search-vs-seo-part-1-why-i-am-running-these-experiments
categories:
  - ai-search-vs-seo
series: ai-search-vs-seo
series_order: 1
tags:
  - ai search
  - seo
  - aeo
  - geo
  - chatgpt
  - gemini
  - perplexity
  - local seo
  - ai ranking
  - small business
image: /images/ai-search-vs-seo.png
excerpt: "AI assistants now answer 'who should I hire?' from indexes that are not Google's, with lists that can change between runs. This series measures what that does to a business that paid for SEO."
---

<p class="align-center">
  <img src="/images/ai-search-vs-seo.png" alt="Illustration: a personal AI agent hands a party plan card with balloons, a venue and a budget to a shop, and a young woman shopkeeper returns a box of decorations that fit, with a crossed-out search magnifier between them." style="max-width: 100%; height: auto;" />
</p>

This is Part 1 of the [AI Search vs SEO](https://feroshjacob.github.io/series/ai-search-vs-seo/) series. There is no experiment yet, just the reasons I am running them.

For twenty years, "being found" meant ranking on Google. Now customers ask a chatbot, and nobody can tell a business owner why they are on the list, or why they are not.

I run into this from two sides. As a technologist, I want to know how the machine decides. As someone who works with small business owners, I hear the plain version of the question: they paid for SEO, and they want to know whether any of it matters when a customer just asks ChatGPT. I do not have an evidence-based answer yet, and I would rather say so than sell one.

## Are people really moving from Google to AI chat?

You will often hear that 25% of search has already moved to chatbots. That number is a forecast, not a measurement. Gartner [predicted in February 2024](https://www.gartner.com/en/newsroom/press-releases/2024-02-19-gartner-predicts-search-engine-volume-will-drop-25-percent-by-2026-due-to-ai-chatbots-and-other-virtual-agents) that search engine volume would drop 25% by 2026.

Here is what has been measured:

- Pew Research Center's [Americans and AI 2026](https://www.pewresearch.org/internet/2026/06/17/americans-and-ai/) survey (fielded February 17-23, 2026) found that 49% of US adults use AI chatbots, up from 33% in 2024. About four in ten US adults say they use chatbots to search for information.
- [StatCounter](https://gs.statcounter.com/search-engine-market-share) still shows Google at 89.94% of worldwide search in September 2026, all platforms.

Usage of AI chat is moving fast, and Google's measured share has barely moved. Both are true at once. So I am not going to treat "25% has moved" as a fact, and you should not either.

## What is this traffic worth?

Alphabet's [Q4 2025 earnings release](https://s206.q4cdn.com/479360582/files/doc_financials/2025/q4/2025q4-alphabet-earnings-release.pdf) reports "Google Search & other" revenue of $63,073 million for that quarter alone. Everything a business pays an SEO agency is a bid for a slice of that attention.

## How is an AI answer made, and why is it not a Google ranking?

An AI assistant does not run PageRank over a link graph. It retrieves results from some index, then a language model writes a list. Which index differs by assistant:

- **ChatGPT** search uses OpenAI's own crawler, [OAI-SearchBot](https://platform.openai.com/docs/bots), plus third-party search results. [TechCrunch notes](https://techcrunch.com/2025/03/21/anthropic-appears-to-be-using-brave-to-power-web-searches-for-its-claude-chatbot/) that OpenAI has a partnership with Bing but also uses other undisclosed sources.
- **Perplexity** runs its own index, which it describes in its [Search API announcement](https://www.perplexity.ai/hub/blog/introducing-the-perplexity-search-api) as covering hundreds of billions of webpages.
- **Claude** is reported to use Brave Search. Anthropic added Brave Search to its subprocessor list in March 2025, as [TechCrunch reported](https://techcrunch.com/2025/03/21/anthropic-appears-to-be-using-brave-to-power-web-searches-for-its-claude-chatbot/) and [Simon Willison confirmed](https://simonwillison.net/2025/Mar/21/anthropic-used-brave/) in his own testing. "Reported" is the right word, because Anthropic has not made a public statement about it.
- **Gemini** is the exception. Google documents [grounding with Google Search](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/multimodal/ground-with-google-search) for its Gemini models, so it is the assistant with Google's own index behind it.

Then there is the writing step. The same question can produce a different list each time, even with the randomness turned off. Thinking Machines explains why in [Defeating Nondeterminism in LLM Inference](https://thinkingmachines.ai/blog/defeating-nondeterminism-in-llm-inference/): the arithmetic on a busy server changes with how many requests are batched together, so the output can change too.

## What happens when the agent knows you better than the store does?

AI agents now know a lot about us. They can work out what we can buy, what we should buy, and what we cannot and should not. Retailers, of course, do not make their profit by selling us only what we need. An agent that knows what is good for us and a store that profits from what we do not need are not always on the same side.

Now imagine an AI agent that organized a party. Instead of searching Amazon for decorations, it shares the context with Amazon: the party, the budget, the venue, the restrictions. The context may or may not come from one of our personal agents, and the store uses it to fetch what fits, within the restrictions we set. That is not a search. It is sharing context.

In that world, product ranking is aimed at agents, not humans. I do not know how far away that reality is. For this series I assume businesses and products continue to exist and continue to rank for human readers. Today a retailer has no access to such context and we have no channel to share it: the protocols being built, OpenAI's [Agentic Commerce Protocol](https://developers.openai.com/commerce) and Google's [AP2](https://cloud.google.com/blog/products/ai-machine-learning/announcing-agents-to-payments-ap2-protocol), carry checkout and payment, and Amazon is [in court](https://www.retaildive.com/news/amazon-sues-perplexity-ai-shopping-agents/804871/) over a shopping agent it did not invite.

## Does paying for SEO still buy anything?

Paying an SEO company bought a position on a results page. I build affordable SEO tooling for small businesses myself (see [Part 4 of my small business software series](https://feroshjacob.github.io/posts/2026/10/05/modern-software-for-small-businesses-and-nonprofits-part-4-providing-affordable-seo-for-small-business-using-jev-part-1)), so I care about the answer.

My hypothesis is that a #1 Google rank does not guarantee a #1 ChatGPT rank. That is a hypothesis, not a finding. It could turn out that Google rank predicts AI rank well, and I will publish that too.

If the hypothesis holds, a business owner needs to know that the position they paid for does not carry over automatically. If it fails, SEO spend is protected, and that is worth knowing too. Either way, an owner deciding where to put a marketing budget should be looking at measurements, not at a confident-sounding forecast.

## What will this series do?

I will run systematic experiments: the same questions asked repeatedly across AI assistants and search engines, with the exact questions, dates and results published whether or not they flatter SEO. If a result is noisy, I will say so.

Part 2 compares ChatGPT's ranking with Google and Bing for "top business near me" queries. You can follow the whole series on the [series page](https://feroshjacob.github.io/series/ai-search-vs-seo/).
