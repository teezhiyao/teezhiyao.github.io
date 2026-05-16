---
layout: single
title: "Setting Up Hermes Agent with Obsidian: My Personal Dev Environment"
date: 2025-05-16
categories: [setup, automation, homelab]
tags: [hermes-agent, obsidian, raspberry-pi, telegram, git]
author_profile: true
---

I've been using Obsidian for years—long before AI agents became a thing. Like many long-time users, my vault started clean and purposeful, but over time it got... messy. Notes scattered everywhere, inconsistent tagging, half-finished projects buried under daily logs. I used GitHub for sync across devices, which worked, but the vault felt like a dumping ground rather than a well-organized knowledge base.

This post covers how I turned things around by integrating Hermes Agent with my Obsidian vault, running everything on a Raspberry Pi with Telegram bots as the interface.

## Why Obsidian for an AI Agent?

The Markdown file format is what makes Obsidian such a good fit for LLM-powered workflows. Every note is a plain `.md` file on disk—no proprietary database, no hidden format. This means:

- Hermes can read, write, and search notes directly using standard file tools
- Git handles version control natively
- You're not locked into any one tool

If you're starting fresh, I'd still recommend Obsidian over other note-taking tools specifically because the LLM-friendly format makes AI integration much simpler.

## The Inspiration

The foundation for this setup came from a [Reddit post on r/hermesagent](https://www.reddit.com/r/hermesagent/comments/1stz6gd/how_i_use_obsidian_as_the_longterm_memory/) about using Obsidian as long-term memory for Hermes Agent. The core idea: instead of treating your agent's memory as a black box, store knowledge in your existing Obsidian vault where you can read, edit, and organize it naturally.

I adapted this for my existing vault. Instead of making Hermes the root of my vault, I created a `Hermes/` folder inside it. This lets the agent coexist with all my other notes without restructuring everything. The folder structure evolves over time as I discover what works best—don't overthink it upfront.

## Running on a Raspberry Pi

I run everything on a Raspberry Pi 4. Here's the high-level approach:

1. **Flash the OS** — Raspberry Pi OS Lite (no desktop needed)
2. **Enable SSH** — `touch ssh` on the boot partition before first boot
3. **Base tools** — `git`, `curl`, `vim`, `htop`, `net-tools`
4. **Static IP** — Set via router reservation for reliable SSH access

The Pi runs 24/7 as my always-on backend node. It's low-power, silent, and perfectly adequate for running Hermes gateways and syncing my vault.

## Telegram Bot Profiles

Hermes Agent supports profiles—separate configurations with their own Telegram bots, personalities, and access permissions. I run a couple of profiles for different contexts:

- **Blog** — Used for content management and writing
- **QA Planner** — A dedicated QA engineering bot with its own persona

Each profile gets its own Telegram bot token (created via [@BotFather](https://t.me/BotFather)) and runs as an independent gateway process.

**Two ways to interact:**

1. **Direct messages** — DM the bot for one-on-one conversations
2. **Group chats** — Add the bot as a group admin. This is useful for team contexts or having multiple bots in the same chat for different roles

## The Sub-Repo Pattern

One pattern I've found really useful is using sub-repositories (separate git repos) inside the Obsidian vault for modular management.

For example, this blog at `teezhiyao.github.io` is a standalone Jekyll site that lives inside my Obsidian vault under `Hermes/Personal/Blog/`. It has its own git repo and its own GitHub Pages deployment, completely separate from the main vault repo. But because it's inside the vault, my blog profile bot can reference and edit blog content naturally while my personal notes stay in their own repo.

This pattern works well for:
- **Blog/portfolio sites** — Jekyll or Hugo sites as their own repos
- **Project documentation** — Per-project repos that are also part of your knowledge graph
- **Configuration** — Dotfiles or setup scripts you want version-controlled separately

## Sync Flow

My Raspberry Pi runs a sync script that periodically pulls changes from GitHub and pushes local edits. The flow is straightforward:

1. Write notes in Obsidian (on any device)
2. Commit and push to GitHub
3. RPi picks up changes via the sync script
4. Hermes agents access the latest notes on the Pi

This keeps GitHub as the source of truth while the Pi serves as the always-on node where agents run.

## Key Takeaways

- **Use Markdown-first tools** — They make AI integration easier
- **Don't restructure everything** — A `Hermes/` folder inside your existing vault is fine
- **SSH over HTTPS** — For git operations from automated agents, SSH keys are more reliable
- **Profiles are powerful** — Separate Telegram bots for different contexts
- **Sub-repos keep things modular** — Each component version-controlled independently

The setup evolves constantly. What works today might change next month, and that's fine—the Markdown + Git foundation makes it easy to adapt without losing anything.
