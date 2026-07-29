---
title: "MS-SBN, Part 3: My Ten-Year-Old Built a Video Game From Four-Word Prompts"
date: 2026-07-28
permalink: /posts/2026/07/28/modern-software-for-small-businesses-and-nonprofits-part-3-my-ten-year-old-built-a-game-from-four-word-prompts
categories:
  - modern-software-small-businesses-nonprofits
series: modern-software-small-businesses-nonprofits
series_order: 3
tags:
  - ai
  - small business software
  - ai-assisted development
  - codex
  - coding agents
  - vibe coding
  - prompts
  - game development
  - kids and ai
  - mission-driven engineering
image: /images/themathaigame-mathai-gameplay.gif
excerpt: "I typed two sentences into a coding agent, asked it to write a plan, and handed the laptop to my ten-year-old son. Over four weeks Mathai built a real, playable 2D game without writing a line of code. It was a deliberate experiment: proof that when every step has to verify it works, the software keeps working — whoever is driving. That is the core of Mission-Driven Engineering."
---

![Gameplay from The Mathai Game: a pixel-art character runs and jumps across a 2D platformer level built by a ten-year-old with a coding agent](/images/themathaigame-mathai-gameplay.gif)

A few weeks ago I set up a [Mission-Driven Engineering](https://feroshjacob.github.io/series/application-generation/) project from a single, deliberately general line — *"build me a simple game like Mario"* — and let it expand that into a proper plan, a *mission*, before any code was written. The starting request can be that vague; the method is what turns it into something real. Then I handed the laptop to my ten-year-old son, Mathai.

I did it to test something I actually believe. Mission-Driven Engineering is built on one simple idea: **make software in small steps, and let every step prove it works before it counts.** If that idea is right, the software keeps working the whole way through — whoever is driving. And Mathai was the ideal person to try it on, precisely because he cannot read a line of code: anything that worked would be the method working, not him.

An hour later, he was hooked. Not watching videos about games, not playing someone else's — building his own, typing what he wanted in plain English and watching it appear on the screen. Four weeks later, he still is. The method carried him, beautifully.

## The Short Version

Over about four weeks, Mathai built a real, playable 2D game. It has a pixel-art character editor, a chasing "wall of flesh," dogs and sharks, a soccer match with goalies, a boss fight, and a six-branch skill tree with nineteen skills. You can play it right now, on your phone or your laptop: **[themathaigame.feroshjacob.workers.dev](https://themathaigame.feroshjacob.workers.dev/)**.

He never wrote a line of code. He never read one. He typed short requests — often three or four words — and a [coding agent](https://feroshjacob.github.io/series/application-generation/) turned each one into a working feature. I did exactly two things myself: I put the game online, and near the end I started writing down what he was doing, so I could tell this story.

The point of this article is not that my kid is special. It is that the *method* did the work. As long as every change had to prove it worked before it was allowed to stay, the software kept working — for four weeks, across dozens of changes, driven by a child. That is the claim I set out to test, and it held. And the whole thing is out in the open: the game is public, the code is public, and the checks that prove each piece works are public. You can play it, and read it, yourself.

## What He Actually Typed

Here is how it worked. A coding agent is an AI — I used [Codex and Claude, and my own method lets me swap between them](https://feroshjacob.github.io/series/application-generation/) — that can read and write code and run a computer, driven entirely by plain-English instructions. You tell it what you want. It writes the code, runs it, and fixes it. You never have to look under the hood.

I gave it a two-line start: build a small game like Mario, and write a one-page mission first. That mission had one rule I want to quote, because it is better scoping advice than most software projects ever get:

> Do not make the first version too complicated. Build the smallest fun version first, then improve it step by step.

Then Mathai took over. Here are nine of the requests he typed in a single session, in order, exactly as he typed them — spelling and all, because his spelling is the most human thing in the whole record:

1. "make a skill tree"
2. "put 2 jump an dash in the skill tree"
3. "make it so you give a key to the skill tree after the first level"
4. "make the dog take 3 bullet tokill"
5. "make new skill tree path"
6. "make an boss"
7. "amke the boss spown on the 5th level"
8. "add more gum paths"
9. "make a wall of death it one shoot you no mater what it spons if you go behind the wall of flesh"

Every one of those became a working feature. "make an boss" produced a boss with ten health points and its own arena. "amke the boss spown on the 5th level" put it on every fifth level. The agent read four misspelled words and wrote the game logic, the graphics, and — this is the part that matters most — the tests that check the feature actually works.

This is the thing I want a non-programmer to sit with. The barrier used to be knowing *how* to say it to a computer. That barrier is mostly gone. A ten-year-old typing "make an boss" now gets a boss.

## He Didn't Plan It

When I asked Mathai how he decided what to build, he said:

> I only asked for one thing and another was another, I didn't plan nine things.

There is a whole philosophy of software in that sentence. Most business software fails because someone tried to plan all nine things up front, spent a year and a budget building them, and discovered too late that they wanted something else. Mathai never planned. He asked for one thing, saw it, reacted to it, and asked for the next thing. He could do that because each change cost him almost nothing — a sentence and a minute, not a meeting and an invoice.

That is the real shift for a small business or nonprofit. It is not that software got smart. It is that changing your mind got cheap. You no longer have to know exactly what you want before you start. You can find out by building.

## He Asked for Harder, Not Easier

Here is what surprised me most. Look back at his nine requests. He asks for a skill, then immediately asks for it to be *earned* behind a key. He makes an enemy take three bullets instead of one. He builds a boss. He invents a "wall of death" that kills him in one hit "no mater what." A ten-year-old with unlimited power at his fingertips mostly used it to make his own game *harder*.

And he was designing, not just requesting. This is his own account of the enemy he is proudest of:

> The dog is very annoying because it can jump. I originally had it as an evil guy... but then I thought of adding guns and "let shoot dogs." So if I shoot them or throw tnt at them, they explode... This is too easy to beat so I made to shoot three times to explode.

Borrowed inspiration, a redesign driven by a new mechanic, and a difficulty tuning decision made weeks later. That is product management. He just doesn't know it has a name.

## How Does a Ten-Year-Old Ship Without Breaking Everything?

This is the question a cautious business owner should ask, and answering it is the entire point of the experiment. The answer is verification: nothing counts as done until it is proven to work. In this method, every small step — every "generation" — writes not just the feature but the checks that confirm the feature behaves. Over a single session the game went from 50 of these automated checks to 101 — little programs that play the game and confirm each feature still does what it should, every time anything changes.

Those checks caught four real bugs before Mathai ever ran into them. Two examples: buying "Double Jump" did nothing, because landing on the ground quietly reset it; and the boss could be skipped entirely, because its arena inherited the normal exit door and you could just walk past the fight and win. Both bugs passed the question "does the feature exist" and failed the question "does the feature *work*." The checks asked the second question.

This is the base of the method, and it is the part that makes everything else possible. It is also the part that is easy to leave out — and leaving it out is where the disappointing AI-built software comes from, the kind that demos beautifully and falls apart the moment someone actually uses it. Keep the verification in, and a ten-year-old ships safely for a month. What carries him is the checking itself: every change had to prove it worked before it earned its place.

I will be honest about one gap. For the first few weeks I kept no written records of Mathai's sessions — a mistake, and the reason I eventually started tracking them at all. But the method's rule did not change whether I was writing it down or not: the software only advances when the new piece is verified. The tracked session is simply where you can *see* that rule working, four caught bugs and all.

For an organization with no engineers, this is the part that makes it safe to build your own tools. You are not trusting the AI to be perfect. You are trusting the method to check every change, out loud, every time — and to refuse to move forward when something fails.

## It Was Not Flawless

I am not going to pretend this was clean. While the agent was testing the game in a browser, it wiped Mathai's saved progress — eleven cleared levels and nineteen unlocked skills — gone. We recovered it only because, by luck, the code happened to print the old data first. We wrote a rule so it can't happen again.

There is more. Mathai shipped nineteen skills in one afternoon and then told me he cannot tell what he owns in his own skill tree — every test was green, and the thing was still confusing to use. The boss, in his words, "doesn't make the game harder." One skill he calls "kind of useless."

I am leaving all of that in, because a story where nothing goes wrong is neither true nor useful. The lesson is not that AI makes flawless software. It is that AI plus honest testing makes software you can *trust enough to fix* — which is all any working software ever is.

## What This Means for the Rest of Us

If describing what you want, in plain words, can produce software that actually runs, then the skill that matters is no longer coding. It is knowing what you want, noticing when it is wrong, and being willing to change your mind cheaply. A ten-year-old has all three.

So does the owner of a small business who knows exactly how her scheduling should work but was told real software costs more than her whole quarter. So does the nonprofit that needs one specific tool that no off-the-shelf product quite fits. The distance from "I wish it did this" to "it does this now" is collapsing, and my son just walked across it without noticing there was ever a gap.

There is one thing that makes it dependable, and it is worth copying above everything else: the verification. The lesson is not "let an AI build it." It is "build it in small steps, and let every step prove it works before you keep it." Do that, and you have more than a one-time win — you have a method you can run again, and again, and expect the same thing each time: software that works. My son ran it, on purpose, and the proof is a game you can play right now.

## He's Not Done

The game is not finished, and it is not supposed to be. When I asked Mathai what he wants next, he said:

> I don't know what I want probably like fixing things.

So by the time you play it, it will have moved on from what I described here — because he keeps coming back to it. That, more than any feature, is the thing I would want a small business or nonprofit to take from this: the software you build this way is never done, and that is the point. It bends to you as you change your mind.

And when I asked him what he thinks about using AI to build games, my ten-year-old gave me the most ten-year-old answer possible, which also happens to be a reasonable software licensing philosophy:

> Dude, you can AI for games as long as you don't make money.

---

**Play the game:** [themathaigame.feroshjacob.workers.dev](https://themathaigame.feroshjacob.workers.dev/) · **Watch a build session:** [one of Mathai's vibe-coding sessions](https://youtu.be/X-aqc_JTteM) (most of the time he is playing the game — that *is* the building) · **Read the code:** [github.com/Northvalley-Intelligence/themathaigame](https://github.com/Northvalley-Intelligence/themathaigame) · **How the mission approach works:** [the Application Generation series](https://feroshjacob.github.io/series/application-generation/).

This is the third article in the series [Modern Software for Small Businesses and Nonprofits](https://feroshjacob.github.io/series/modern-software-small-businesses-nonprofits/). The [first](/posts/2026/07/01/modern-software-for-small-businesses-and-nonprofits-part-1-the-website-is-becoming-the-software) argued that the website is becoming the first useful software surface for small organizations; the [second](/posts/2026/07/06/modern-software-for-small-businesses-and-nonprofits-part-2-amy-built-the-website-she-had-imagined-since-high-school) followed Amy Givens turning a domain she'd owned since high school into a real business. This one is about my son. The thread through all three is the same: the people who know what the software should do no longer have to wait for someone else to build it.
