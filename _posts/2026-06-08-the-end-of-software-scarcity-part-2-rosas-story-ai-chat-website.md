---
title: "The End of Software Scarcity, Part 2: Rosa's Story — Website & AI Chat"
date: 2026-06-08
permalink: /posts/2026/06/08/the-end-of-software-scarcity-part-2-website-ai-chat
categories:
  - software-scarcity
tags:
  - ai
  - small business
  - software engineering
  - ai-assisted development
  - entrepreneurship
  - customer experience
  - ai chat
  - small business website
  - local business
excerpt: Building the website was easy. The more interesting question was whether a small business could afford an AI-powered customer experience with virtually no recurring software costs.
---
![AI Chat Workflow](/images/rosa-engagement-workflow.jpg)

In my previous article, [The End of Software Scarcity, Part 2: Rosa's Story](https://feroshjacob.github.io/posts/2026/06/01/the-end-of-software-scarcity-part-2-rosas-story), I described how Rosa's dream of having a website became a reality.

The website itself was never the interesting part. The interesting question was whether a small business could afford an AI-powered customer experience on a real website serving real customers.

# Small Business Software

Rosa currently serves approximately thirteen clients. Even if her business doubles or triples, we are still talking about dozens of customer interactions per month, not millions.

Most local businesses do not have a scalability problem. They have a customer acquisition problem, and their software needs are often much smaller than modern free tiers.

| Service | Free Tier |
|----------|----------|
| Gemini Flash | 30 requests per minute |
| Supabase | 500 MB database |
| Cloudflare Workers + OpenNext | Free-tier hosting for this traffic profile |
| GitHub | Free repositories |
| Domain Registration | Approximately $13/year |

**Website:** [www.medinaclean.com](https://www.medinaclean.com)  
**Source Code:** [https://github.com/Northvalley-Intelligence/medinaclean.com](https://github.com/Northvalley-Intelligence/medinaclean.com)

# Why AI Chat?

Most small business websites follow a familiar pattern: the visitor finds a phone number, sends a message, and waits. I wanted to explore a different approach.

The site can now answer common questions, understand cleaning needs, collect contact information, and prepare lead details before Rosa ever picks up the phone. Instead of a static website, Rosa has something closer to a digital front desk.



# Cost Optionality

The important part was not simply choosing free services. The important part was designing the software so paid services were optional.

The AI chat does not depend on one model provider. The backend can rotate between Gemini and OpenRouter:

```bash
AI_CHAT_PROVIDER_CHAIN=gemini,openrouter
```

One turn can try Gemini first. The next can try OpenRouter first. If providers fail, the site still returns a deterministic rules-based response.

Pricing rules, service-area checks, material claims, and lead capture stay in application code. The model helps with conversational polish, but the software keeps the business rules.

The project also tracks AI usage as business data: provider, model, tokens, latency, success status, and estimated cost. Instead of guessing whether AI will become expensive, Rosa can measure real usage before paying.

# Avoiding Paid APIs by Design

The same discipline appears outside the chat feature:

- The site avoids Google Maps billing by using a local ZIP centroid list around Woodstock, Georgia.
- Review photos are resized in the browser to small WebP files before upload.
- The ad planner validates budget and ZIP targeting before touching the Meta API. Live publishing requires explicit configuration, and campaigns are paused by default.
- Calendar invites are optional. If Google Calendar is not enabled, the app uses a no-op provider.

Together these choices create cost optionality: use free infrastructure by default, keep external services behind configuration, validate locally before calling paid or rate-limited APIs, and keep a working non-AI path.

# What Comes Next?

The most interesting lesson was economic. Software creation is becoming dramatically cheaper while infrastructure free tiers remain surprisingly generous. Small businesses can now access capabilities that would have been hard to justify only a few years ago.

The AI chat is only the beginning. In the next article, I'll walk through how a simple conversation becomes part of a lightweight customer acquisition workflow.
