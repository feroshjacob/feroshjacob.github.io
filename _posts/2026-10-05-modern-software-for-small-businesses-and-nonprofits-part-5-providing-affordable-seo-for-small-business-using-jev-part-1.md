---
title: "MS-SBN, Part 5: Providing Affordable SEO for Small Business Using Jev (Part 1)"
date: 2026-10-05
permalink: /posts/2026/10/05/modern-software-for-small-businesses-and-nonprofits-part-5-providing-affordable-seo-for-small-business-using-jev-part-1
categories:
  - modern-software-small-businesses-nonprofits
series: modern-software-small-businesses-nonprofits
series_order: 5
tags:
  - seo for small business
  - local business listings
  - jev ai
  - typesafe ai
  - browser automation
  - ai agents
  - human in the loop
  - business profile automation
  - software validation
image: /images/modern-software-part5-jev-seo-architecture.png
excerpt: "The SEO tools that manage several sites at once price out a small business before it starts. So I'm building a semi-automated tool, with Jev narrowing ambiguous choices, that fills out business listings across ten sites without pretending every site allows a bot at the keyboard."
---

<p class="align-center">
  <img src="/images/modern-software-part5-jev-seo-architecture.png" alt="Diagram: one approved business record feeding four levels of automation -- API automated, prefill with auto-submit (unused), prefill with human submit, and assist-the-user -- with a narrow Jev assist loop and a validation and frontier review gate in front of every path" style="max-width: 100%; height: auto;" />
</p>

This is Part 5 of the [Modern Software for Small Businesses and Nonprofits](https://feroshjacob.github.io/series/modern-software-small-businesses-nonprofits/) series, and Part 1 of a new sub-topic inside it: providing affordable SEO for small business, starting with local business-listing profiles, and starting with Jev doing the narrow work it's actually good at.

## The short version

SEO tools that can manage more than five or six sites at once can easily cost $1,000 a month. For an agency to make a living reselling that, a small business ends up paying several thousand dollars a month — money that is not affordable for most of them. So I'm building a small, human-assisted tool that creates one approved business record once and prepares it for ten listing sites, as an experiment. It is semi-automated on purpose, because the sites themselves don't all allow the same thing, and I'd rather be honest about that than ship something that gets a client's listing suspended.

## The problem: SEO pricing locks small business out before it starts

I've said this plainly to Ferosh and I'll say it the same way here: the popular SEO tools that can manage more than five or six client sites can easily cost $1,000 a month. An agency buying that tooling has to recover it across its client base, and the bill that lands on an individual small business is several thousand dollars a month. That's not a business decision a solo tea shop or a two-truck pet-waste company gets to make — it's simply out of reach.

Local listing profiles (Google, Bing, Yelp, Nextdoor, and the rest) are one of the cheapest, highest-leverage pieces of local SEO there is, and they're exactly the kind of repetitive, multi-site data entry a small agency charges for. So the first experiment in this sub-series is narrow: can one approved record be reused across ten listing platforms without an agency retainer, and without quietly breaking any site's rules to do it?

## What Jev is, in plain terms

I don't expect anyone to already know what Jev is, so here it is simply: Jev is an AI model, reached through [TypeSafe's](https://typesafe.ai) official SDK ([docs.typesafe.ai](https://docs.typesafe.ai)), that I use for narrow, bounded decisions — not for writing code and not for driving a browser. The app talks to it over `@typesafe-ai/sdk`, pointed at Jev's own endpoint (`https://jev-ai.pro/api`) rather than TypeSafe's default API, and pins the model version (`jev-1.13.0`) while validating behavior rather than trusting the floating `jev-latest` alias.

The lesson that mattered most here had nothing to do with SEO: changing an SDK key does not change where the SDK talks to. Getting Jev's base URL and key right was a security and billing control, not a configuration footnote — and a successful API call only proves the transport works, not that the model's decisions are good ones.

Jev shows up in exactly one place in the running application: a loopback-only "runtime assist" endpoint. A human supplies compact, visible page state and a closed set of candidate answers; Jev picks among them; the endpoint validates the choice against the candidates it was given and always returns `executionAllowed: false`. It cannot navigate, fill a field, upload anything, submit anything, or expand what an adapter is allowed to do. One real call through that endpoint, during Foursquare category selection, used 552 paid input tokens and 41 output tokens, charged zero credits, and still required a human to review the result before anything moved forward.

## Why semi-automated, not fully automated

The client-facing part of this asked me to validate, not assume, what each site's rules actually say — so here's what I found, and where I still don't know.

The L1 framing I started from was: some sites simply won't let a bot click "submit," and stricter sites won't let a bot populate the form fields at all. That's real, but it undersells how granular the actual permission question is. The project tracks each platform's permission on several separate dials — business eligibility, free-profile availability, read/navigate, fill, upload, submit, account/verification requirements, and address visibility — and every one of them defaults to **unknown means deny**. A Jev answer cannot grant a capability; only documented, official evidence can.

What that produced, platform by platform:

- **Foursquare** documents that service API keys are explicitly intended for internal automation, scripts, and agents — the only platform of the ten with an affirmative automation grant. ([authentication](https://docs.foursquare.com/fsq-developers-places/reference/authentication), [place search](https://docs.foursquare.com/fsq-developers-places/reference/place-search), [suggest place](https://docs.foursquare.com/fsq-developers-places/reference/places-suggest-place))
- **Bing Places** documents a free claim/add/update flow and lets an eligible service-area business hide its public address, but nothing in the official help establishes permission to automate its web interface. ([Bing for Business help](https://www.bing.com/forbusiness/help/modernExperience?setlang=en))
- **Nextdoor** documents a free Business Page for local, casual-service, and home-service providers, requiring an owner or authorized representative to claim it. ([Create a Business Page](https://business.nextdoor.com/en-us/getting-started/business-page))
- **Data Axle** documents a free route for one to ten listings with duplicate matching and verification. ([Local Listings](https://www.data-axle.com/marketing-solutions/local-listings-management/))
- **Yelp** documents that claiming a page is free but requires owner or representative verification. ([Creating a Yelp Page](https://business.yelp.com/resources/articles/creating-a-yelp-page-for-your-brand-new-business/))
- **BBB** separates a free Business Profile from paid accreditation — the pilot is never allowed to drift into the accreditation funnel. ([Get Listed](https://www.bbb.org/get-listed))
- **Angi** is the one I actually blocked: its signup funnel asks for a mobile number and explicitly authorizes marketing calls and texts, and I could not find a free, no-lead-funnel profile route. Until that changes, it's blocked outright, not "manual." ([Angi pro join](https://signup.angi.com/pro/join))
- **Apple Business**, **Yellow Pages**, and **Facebook Pages** all have a documented free or claimable path, but none of them establish enough about address visibility, eligibility, or current terms to automate anything beyond preparing values for a human.

So "semi-automated" isn't a hedge — it's the honest description of ten sites that each permit something different, with the default on every undocumented permission set to deny.

## The architecture

The diagram above is the real shape of the system: one approved, source-attributed business record feeds four possible levels of automation, Jev sits in a narrow assist loop that any of those levels can call into but that can never act on its own, and nothing reaches a live platform without passing a validation and frontier-review gate first.

1. **API automated — Foursquare.** The only platform with a documented automation grant. The adapter does an authenticated duplicate search, an explicit `dry_run=true` preview, and then one human-approved `dry_run=false` suggestion write — never a browser interaction with Foursquare's business portal.
2. **Prefill + automated submit.** Architecturally supported by the adapter contract, but currently unused by every one of the ten platforms, because none of them has granted fill-and-submit permission. This is the level where "unknown means deny" actually bites: Jev being confident about a field does not open this path either.
3. **Prefill + human submits — Bing.** A fixture-tested draft review is prepared from the approved record, checked against known duplicates, and handed to a person to claim, review, and submit. No browser drives Bing's site.
4. **Assist the user — the other eight platforms.** Values and instructions are prepared; the person does every step themselves, in their own browser, with their own login.

Jev's runtime assist loop sits underneath all four paths, not inside any one of them: it takes compact page state and a closed candidate list, returns one validated choice, and never sees a screenshot, raw HTML, a credential, or a private email.

## What implementing this actually taught me

**Browser-level testing caught a real privacy bug that unit tests missed.** The first local Playwright run failed because a syntax error silently broke the client-side submit handler, and the browser fell back to a GET form submission — which puts private intake emails directly in the URL. Unit tests of the server-side rules never would have caught this; only exercising the actual browser path did. The fix added a POST fallback, a regression test for the parse error, and an end-to-end assertion that private values never reach the address bar.

**A green test suite is an input to release review, not the release decision.** The first Foursquare candidate passed strict typing, 73 automated tests, and three browser scenarios — and the frontier review still rejected it. The adversarial pass found that two different HTTP action IDs could acquire two external writes for the same approved review, that an uncertain timeout could be replayed through a fresh preview, and that a stored rejected response could be mistaken for success after a restart. The fix bound the one consequential write to the immutable publication-review ID rather than to any button or action ID — idempotency by review, not by click.

**That rejection happened four separate times, each one subtler than the last.** The first frontier pass found four gaps in the general workflow (a required fact could silently lose approval, a Bing duplicate could leave the operator stranded). The second and third passes went after the first fix as a general class rather than checking only the reported examples, and found a verification-code filter that still let some code formats through, plus a database migration that didn't actually restore the meaning of an old saved listing. The fourth pass found a genuine recovery dead end — a public candidate URL that safely failed review but left the operator with no valid next action. Each pass made the release gate stricter without the happy-path tests ever changing.

**Foursquare's category pick is the clearest real example of what Jev is for.** Deterministic code supplied four current Foursquare category candidates plus an explicit `unknown` option; the `jev-latest` alias call failed outright with zero usage reported, but the pinned `jev-1.13.0` call selected "Home Service" using 594 paid input tokens and 62 output tokens, charged zero credits. That selection became a draft for human approval, not a published fact — which is exactly the boundary the whole project is built around.

**Foursquare became the first (and so far only) fully adapter-automated platform**, specifically because it's the only one with documented automation permission. Getting there took a correction, too: the first live duplicate-search filter was too broad and would have blocked on any business sharing a word with a nearby result, so it was narrowed to exact phone/website/name matches or strong name overlap, with the provider's own `dry_run=true` check as a second guard.

## Status

The project is still in progress, and it's a ten-site experiment, not a finished product: one platform (Foursquare) has a real, tested API adapter; one (Bing) has a fixture-tested human-submit workflow; eight are deliberately manual-guidance or, in Angi's case, blocked. Live `dry_run` and live writes against the real Foursquare endpoint for the pilot business are still untested, pending the client's approval of the location and category facts. If it keeps working, the plan — in the author's own words — is: "we will open source the project if we can get it working :)"

Part 2 of this sub-series will pick up once there's a live, human-approved publication to report on.

---

*Previous: [Part 4 — She Calls Him Indispensable. He Says Check His Work.](https://feroshjacob.github.io/posts/2026/09/26/modern-software-for-small-businesses-and-nonprofits-part-4-she-calls-him-indispensable-he-says-check-his-work) · Series: [Modern Software for Small Businesses and Nonprofits](https://feroshjacob.github.io/series/modern-software-small-businesses-nonprofits/)*
