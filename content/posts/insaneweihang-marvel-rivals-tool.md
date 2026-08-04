---
title: "Building insaneweihang.com — A Marvel Rivals Team-Up Tool"
date: 2026-07-30
categories: [project, game, web-dev]
tags: [marvel-rivals, github-pages, cloudflare, analytics, d1, side-project]
draft: true
---

I put together a little tool for Marvel Rivals called [insaneweihang.com](https://insaneweihang.com) — a team-up browser and planner that helps you find fully enhanced team compositions quickly. This post covers how it's set up and some of the infrastructure decisions.

## Background

The project came out of a question from a friend — a content creator who mostly plays Marvel Rivals. I don't play the game myself, but his question was essentially a math problem: each hero can get a power-up when another corresponding hero is on the team, so how many teams of 6 can be formed where every hero is powered up? The site is the answer to that — instead of working through the combinations by hand, it enumerates every fully-enhanced composition and makes them browsable.

## What It Does

Three views in a single-page app:

- **Team Browser** — Browse all valid team compositions, filter by hero or role, toggle between all teams and 2-2-2 only
- **Team Planner** — Build your own composition and see which team-ups activate
- **Maps** — Map info reference

Data is kept current with patch updates as Marvel Rivals evolves.

## Hosting: GitHub Pages + Custom Domain

The site is a vanilla HTML/CSS/JS static site hosted on GitHub Pages with a custom domain pointed at `insaneweihang.com`. The CNAME record and Pages settings are configured through GitHub's repo settings — nothing fancy, just a `docs/` or root folder deployment with the domain hooked up.

## Analytics

Two layers:

### Google Analytics (GA4)

A standard GA4 tag is embedded in the `<head>`:

```html
<script async src="https://www.googletagmanager.com/gtag/js?id=G-LPFXF8XZSR"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag("js", new Date());
  gtag("config", "G-LPFXF8XZSR");
</script>
```

### Cloudflare Web Analytics

Cloudflare's privacy-focused Web Analytics runs alongside it for a lightweight, ad-blocker-friendly view of traffic. Both give slightly different data, and having both is useful — GA4 for depth, Cloudflare for basic reach without cookies.

## Feedback: Cloudflare Workers + D1

The feedback form submits to a Cloudflare Worker endpoint:

```
https://marvel-rivals-feedback.insaneweihang.workers.dev/feedback
```

The Worker writes submissions to a **Cloudflare D1** database — serverless SQLite that requires no provisioning, scales to zero, and costs next to nothing for a low-traffic side project.

The form collects:
- Feedback type (dropdown)
- Title
- Message
- Optional contact info

Honeypot field for spam, client-side validation, and a clean status feedback UI. The Worker handles the insert and returns a success/error response.

## Design

Kept it simple: light theme, Inter font, role-coloured badges (Vanguard blue, Duelist red, Strategist green). Sticky header, tab navigation, Discord and Ko-fi links in the header. No framework, no build step — just HTML, CSS, and a single `app.js` file loaded cache-busted with the date.

## Why Static + Workers

A static site keeps things simple: no servers, no containers, just push to GitHub and it's live. But static sites can't do forms. Cloudflare Workers + D1 fills that gap without spinning up a backend or paying for a database — the Worker is a single script, D1 is free tier, and both are managed through Cloudflare's dashboard.

## What I'd Do Differently

If I were starting over or adding features:
- **Data as JSON** — The team composition data could be fetched from a Worker endpoint so updates don't require a full site redeploy
- **Service worker cache** — The data is fairly static between patches, so a service worker could make it work offline
- **D1 for more than feedback** — Could use it to track which teams users check most, or store community-contributed team ratings

---

You can find the site at [insaneweihang.com](https://insaneweihang.com), join the [Discord](https://discord.gg/82V8xT8qRx) if you play, or send feedback right from the site.
