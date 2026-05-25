---
title: "Spring 2026 OMSCS — Game AI & Software Analysis"
date: 2026-05-02
categories: [omscs, academics]
tags: [cs7632, cs6340, game-ai, software-analysis, gtech, omscs]
draft: false
---

Spring 2026 was a two-course semester. Coming into it, I knew it'd be a stretch — Game AI on the creative side, Software Analysis on the formal side — but I wanted to see how they'd play off each other while balancing a full-time QA role. Turned out they complemented each other better than I expected.

---

## CS7632 — Game AI

Game AI is fundamentally different from academic AI. You're not trying to prove optimality or solve an intractable problem in the general case — you're making a player have a good time. That means cutting corners, embracing approximations, and yes, cheating when the player won't notice. The entire course is built around this philosophy.

### What I Built (Conceptually)

**Spatial Queries** started things off — implementing bounding box checks, overlapping range tests, and traversability validation. The core concept here was **computational geometry for games**: point containment, line intersection, and the messy reality of floating-point precision. You learn quickly that epsilon comparisons and integer conversion tricks aren't academic footnotes — they matter when your AI agent phases through a wall it shouldn't.

**Path Network Generation** came next. This was about building a graph representation of the game world — discretizing continuous space into nodes and edges, then validating that edges are actually traversable (no obstacles, enough clearance for the agent to pass). The concept: **state space discretization for navigation**. The same idea underpins everything from robot vacuum cleaners to warehouse automation.

**Pathfinding** tied it together — running search algorithms over the generated network to move agents from point A to B. This is where **A*** and heuristic search concepts came to life. The interesting bit wasn't the algorithm itself (it's textbook) — it was the engineering choices around it: how coarse should your grid be? How do you clean up paths to look natural instead of robotic? When is good-enough better than optimal?

### Personal Takeaways

The assignments were the highlight — genuinely well-designed. Each one had a **properly gated structure**: an easy warm-up to get oriented, a meaty core, then a hard optimization stretch where you could go absolutely nuts. There's real room for people who want to obsess over performance, and I respect that the course doesn't clip your wings.

- **Dodgeball** and **racetrack** were my favourites. Dodgeball because the AI decision-making under uncertainty maps directly to real gameplay. Racetrack because it's A* with a twist — velocity state makes the search space way more interesting.
- The **final terrain design** assignment was a different beast — creative, almost architectural. I can see someone with an artsier bent loving that one. My own output was more of a "prove I understood the course concepts" exercise than anything beautiful.

### Concepts That Stuck

- **Heuristic ≠ hack.** A well-designed heuristic makes pathfinding tractable. A poorly designed one makes agents look stupid.
- **Distribute intelligence.** Don't put everything in the agent. Bake navigation hints into the environment — navmeshes, waypoints, cover points. The level design is part of the AI.
- **Degrade deliberately.** An AI that always makes the perfect play isn't fun. Artificial reaction delays, imperfect aiming, rubber-banding — these make games playable.
- **Genre dictates technique.** Fighting games want learning and pattern analysis. Platformers want predictable enemies you can master. RTS needs group coordination. There is no one-size-fits-all.

---

## CS6340 — Software Analysis

This was the formal counterpart — learning how to automatically discover useful facts about programs. The entire course orbits one fundamental trade-off: **you cannot be sound, complete, and terminating at the same time**. Every real tool picks two and compromises on the third.

### What I Built (Conceptually)

**LLVM Pass Instrumentation** was the first hands-on piece. The concept: **dynamic analysis at the compiler IR level**. You write an LLVM pass that inserts monitoring code at specific program points — in this case, runtime checks for divide-by-zero. The same approach powers tools like AddressSanitizer and ThreadSanitizer. Understanding how compiler-level instrumentation works changed how I think about testing at the lower levels.

**Automated Test Generation** followed — implementing systematic and feedback-driven approaches. Two contrasting paradigms:

- **Systematic generation (ala Korat)**: enumerate all valid input structures up to a bound. The key insight — use pre-conditions (repOK) to prune the search space as you go. If the pre-condition doesn't access a field, don't bother expanding it. The concept: **bounded exhaustive testing with smart pruning**.
- **Feedback-directed generation (ala Randoop)**: build method sequences incrementally, execute immediately, classify results. Illegal sequences get discarded. Bugs get reported. Redundant sequences get dropped. Useful ones get extended. The concept: **test generation as a feedback loop**.

**Dataflow Analysis** was the deepest piece — implementing **reaching definitions** and **live variable analysis** on control-flow graphs. The concept: **static analysis as fixed-point computation**. You start with optimistic assumptions, propagate facts through the CFG, and iterate until nothing changes. Soundness means you may report facts that can't occur (false positives), but you won't miss real ones.

The pointer analysis material (allocation-site vs. type-based heap abstraction, flow sensitivity) tied back to the real world — understanding why Coverity or Infer report what they do requires knowing where they sit on the soundness-completeness axis.

### Personal Takeaways

This module was manageable — partly because my day job is QA/performance testing, so the core concepts (test generation, coverage, mutation analysis) were familiar ground. The concepts clicked faster having worked with them in practice.

**Compiler-level instrumentation** was the standout. Writing LLVM passes to instrument IR at compile time opened my eyes to a layer of testing I'd never touched. It honestly made me consider taking the Compilers module (CS 6241) — though I'm not sure it'll fit into my remaining course slots. If you're a testing person and you've never seen how AddressSanitizer works under the hood, this module gives you that picture.

### Concepts That Stuck

- **Testing shows the presence, not absence, of bugs.** Corollary: mutation analysis helps you measure how good your test suite is at finding bugs, not how bug-free the code is.
- **Small test case hypothesis.** If a bug exists, there's usually a small input that exposes it. This makes bounded testing practical.
- **Soundness-completeness-termination is the fundamental triangle.** Every program analysis tool makes a choice here. Knowing which choice a tool made tells you how to interpret its output.
- **Compiler IR is the right abstraction layer for many analyses.** LLVM passes can instrument, analyze, and transform code without recompiling from source.

---

## The Pairing

Game AI taught me to ask: *is this fun?* Software Analysis taught me to ask: *can we prove this is correct?*

On the surface they're opposite. But in practice they share something — both are about **making pragmatic trade-offs under constraints**. Game AI trades optimality for player experience. Software Analysis trades completeness for termination. Neither field has a perfect answer, but both have proven techniques that work well enough to ship.

If I had any advice for someone considering this pair: take them in the same semester if you can handle the workload. One is a pressure release for the other — when you're tired of proving liveness, you debug a wandering NPC in Unity. When you're tired of epsilon comparisons, you reason about control-flow graphs.

---

## What's Next

I kept lighter notes this semester than I'd have liked — too much time in the code, not enough in the markdown. For future courses I want to be more disciplined about maintaining reference notes as assignments progress, so the reflection writes itself.

*This post was summarized from course notes and materials with AI assistance.*
