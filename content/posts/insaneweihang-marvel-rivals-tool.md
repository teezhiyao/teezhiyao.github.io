---
title: "Building insaneweihang.com - A Marvel Rivals Team-Up Tool"
date: 2026-07-30
categories: [project, game, web-dev]
tags: [marvel-rivals, github-pages, cloudflare, analytics, github-actions, side-project]
draft: true
---

I built [insaneweihang.com](https://insaneweihang.com), a small Marvel Rivals team-up browser and planner for a friend who creates Marvel Rivals content.

The site started from a simple question: given the way Marvel Rivals team-ups work, what six-hero teams can be formed where every hero is fully enhanced? I do not actively play the game myself, but the problem was interesting because it was part game knowledge, part combinatorics, and part product design.

This post is a rough write-up of what I built, how I approached it, and the setup work around deployment, domain routing, and basic traffic tracking.

## What I Built

The site is a single-page static web app with three main views:

- **Team Browser** - browse valid fully enhanced team compositions, filter by hero or role, and toggle between all teams and 2-2-2 compositions.
- **Team Planner** - manually build a team and see which team-ups activate.
- **Maps** - keep map information in the same lightweight reference tool.

The goal was not to build a full game database. It was to answer a specific practical question quickly: "Which teams work if I want everyone powered up?"

## Using AI To Build Faster

I used AI heavily as a coding assistant for this project. The useful part was not asking it to magically build the whole product, but using it to move faster through small loops:

- sketching the first HTML/CSS/JS structure
- iterating on UI layout and filter behaviour
- turning the team-up rules into testable logic
- refactoring rough code into clearer functions
- checking edge cases in the composition-generation logic
- drafting small deployment and analytics snippets

The constraint was that I still had to understand the output. The team-up rules are domain-specific, and small mistakes can produce teams that look plausible but are wrong. AI was useful for acceleration, but the correctness still came from reviewing the rules, testing generated teams, and manually checking behaviour on the site.

## The Team-Up Algorithm

At a high level, the algorithm treats each hero as data:

- hero name
- role
- team-up relationships
- whether the hero receives an enhancement from another hero

From there, the site can evaluate a candidate team of six heroes by checking each selected hero against the team-up data. A team is considered fully enhanced only if every hero who needs a matching partner has that condition satisfied within the same six-hero composition.

The browser side then generates or stores valid compositions and lets the UI filter them by:

- selected hero
- role
- all valid teams versus 2-2-2 teams

The 2-2-2 filter was important because raw valid teams are useful mathematically, but players often care about role balance. Separating "all valid teams" from "role-balanced teams" made the tool more usable.

## Static Site Architecture

I kept the app deliberately simple:

- vanilla HTML, CSS, and JavaScript
- no frontend framework
- no build step for the app itself
- static hosting through GitHub Pages

For a small reference tool, this was enough. It also made the deployment path straightforward: update the files, push to GitHub, and let GitHub Pages serve the new version.

## Deployment With GitHub Actions

I added a GitHub Actions deployment flow so the site can be updated consistently from the repository rather than relying on manual upload steps.

The workflow is intentionally boring:

- push changes to the repo
- GitHub Actions builds or prepares the static output
- GitHub Pages publishes it

For this kind of side project, boring infrastructure is a feature. The less deployment ceremony there is, the more likely I am to keep the data current when Marvel Rivals patches change the team-up rules.

## Domain Routing

I also helped set up the custom domain so the project lives at:

[insaneweihang.com](https://insaneweihang.com)

The domain was bought separately, then routed to GitHub Pages. That involved connecting the custom domain in the repository settings and making sure the DNS records pointed at GitHub Pages correctly.

The value of the custom domain is mostly practical. It is easier to share on stream, in Discord, or in a video description than a long GitHub Pages URL.

## Analytics And Baseline Tracking

I added two basic layers of traffic tracking:

- **Cloudflare Web Analytics** for lightweight baseline traffic visibility.
- **Google Analytics 4** for deeper traffic and usage reporting.

For a side project like this, I mainly wanted to answer simple questions:

- Are people actually visiting the tool?
- Which pages or views are getting used?
- Did a content mention or Discord share lead to a traffic spike?

Cloudflare gives a simple privacy-friendly baseline, while GA is useful when more detailed reporting is needed. I did not want analytics to become the project; the point was just to have enough signal to know whether the tool was useful.

## Feedback Form

The site also has a feedback path backed by Cloudflare Workers and D1.

The form collects:

- feedback type
- title
- message
- optional contact information

The static site submits to a Cloudflare Worker, and the Worker writes the response into a D1 database. That gave the project a tiny backend without turning the whole thing into a full-stack application.

## What I Learned

This was a good reminder that small tools are often valuable when they answer one specific question clearly.

The interesting parts were not only technical. I had to translate a game mechanic I did not personally play into a data model, an algorithm, and a UI that players could actually use. That meant checking assumptions with someone closer to the game, keeping the interface focused, and making infrastructure decisions that would not become maintenance overhead.

If I continue improving it, I would look at:

- moving more game data into structured JSON
- adding clearer validation tests around team-up rules
- making patch updates easier to review
- tracking which filters or team compositions users actually care about

For now, the useful part is that the site exists, is shareable, and answers the original question quickly.
