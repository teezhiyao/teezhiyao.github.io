---
title: "OMSCS: CS 6290 High Performance Computer Architecture"
date: 2025-05-01
draft: false
categories: [learning, omscs]
tags: [omscs, georgia-tech, cs-6290, computer-architecture]
description: "My reflection on HPCA — from pipelining to cache coherence."
---

CS 6290 was one of those courses where I came out knowing exactly how a processor works under the hood in a way I hadn't before. It covers the full stack of modern processor design — from the simplest pipelining all the way up to multi-core coherence protocols and memory consistency models. It's dense, quantitative, and rewards meticulous attention to detail.

## What The Course Covered

The course followed a logical arc that built up in complexity. It started with fundamental metrics — the Iron Law of Performance (execution time = instruction count × CPI × cycle time) and Amdahl's Law, which became the recurring mental models for evaluating every design tradeoff.

From there it moved through pipelining — the classic 5-stage Fetch-Decode-ALU-Mem-Write pipeline, data hazards (RAW, WAW, WAR), and control hazards. Branch prediction came next: branch target buffers, history tables, 1-bit and 2-bit predictors, and the more sophisticated GShare and PShare schemes. The penalty of a misprediction grows with pipeline depth, which makes this topic more consequential than it first appears.

Then came instruction-level parallelism and the heart of the course — out-of-order execution. Register renaming via a Register Allocation Table eliminates false dependencies (WAR and WAW), and Tomasulo's Algorithm with reservation stations and a common data bus enables out-of-order execution. The Reorder Buffer (ROB) was the piece that made everything click: it lets the processor execute out of order but commit results in program order, solving both exception handling and branch misprediction recovery. The Load/Store Queue handles memory ordering on top of that.

The compiler's role in extracting ILP got its own module — tree height reduction, loop unrolling, software pipelining — and then VLIW as the alternative philosophy where the compiler does the heavy lifting instead of complex hardware.

The second half descended into the memory hierarchy. Cache basics (block offset, index, tag, AMAT), replacement policies, and write policies. Then virtual memory with TLBs, and the clever VIPT trick that overlaps TLB and cache access. Advanced cache topics like non-blocking caches with MSHRs, prefetching, and multi-level hierarchies. Then the memory technology itself — SRAM vs. DRAM, the memory wall (DRAM improves at 7%/year vs processors at 55%/year), and storage.

The final stretch covered multiprocessing: cache coherence protocols (MSI, MOSI, MESI, MOESI — and tracking state machine transitions across a sequence of reads and writes), synchronization primitives, memory consistency (coherence vs. consistency, sequential vs. relaxed), and on-chip networks for many-core scalability.

## What I Built (Conceptually)

The assignments were entirely quiz-based and problem-set driven. There was no processor simulator to modify, no pipeline to implement. The exercises involved tracing Tomasulo's Algorithm reservation station state across cycles, filling ROB timing diagrams, computing cache coherence state machine transitions while counting bus and memory accesses, and calculating performance under various architectural configurations.

The coherence exercises in particular were the most demanding — tracking the state of a cache line across four cores through a sequence of reads, writes, and invalidations, then counting how many bus transactions and memory accesses occurred. One off-by-one error in tracking a transition and the entire answer cascaded.

## Concepts That Stuck

- **The Iron Law** is the most concise and useful mental model in the entire course. Every processor optimization trades off instructions, CPI, or cycle time. If you can't articulate which one you're trading, you don't understand the optimisation.

- **Register renaming is the linchpin.** It's a simple idea — map architectural registers to physical ones to eliminate false dependencies — but it enables everything else in OOO execution.

- **The ROB is elegant engineering.** Execute out of order but commit in order. It lets you have the performance of OOO with the precise exception model of in-order. That's a genuinely clever piece of design.

- **VIPT caches are a practical masterstroke.** If your cache is small enough relative to the page size, you can index into it with the virtual address while the TLB translates in parallel, then tag-check with the physical address. No aliasing, no TLB serialisation.

- **Coherence vs. consistency is a distinction worth making.** Coherence deals with ordering accesses to the same address; consistency deals with ordering across different addresses. The data-race-free guarantee — write correct locks and any model gives you sequential consistency — is a clean and useful abstraction.

## What Was Hard

The cache coherence exercises were the hardest part of the course. Tracking MSI/MESI state machine transitions across multiple cores, each performing reads and writes, while counting bus transactions and memory accesses, requires careful step-by-step reasoning that's easy to slip up on. The directory-based coherence quiz was also demanding — tracking request-forward-send sequences across four cores.

Tomasulo's Algorithm tracing was the other difficult area. Managing reservation station fields (busy, op, Vj, Vk, Qj, Qk, disp) across multiple cycles with different functional unit latencies requires methodical bookkeeping.

The memory consistency reasoning — determining whether a particular outcome (e.g., R1=1, R2=0) is possible under different consistency models — requires precise thinking about instruction ordering that doesn't come naturally.

## Workload And Rhythm

The course was quiz-heavy. Regular lecture quizzes and problem sets that required careful numerical tracking. The workload was manageable if you stayed on top of the exercises — falling behind on a coherence or Tomasulo tracing exercise meant the next one was much harder to catch up on. I'd estimate 12-16 hours per week on average.

## Takeaways

HPCA gave me a proper understanding of how modern processors actually work — not just the abstraction layer that operating systems and compilers present, but the hardware mechanisms underneath. The mental models (Iron Law, Amdahl's Law, the memory wall) are generally useful for reasoning about performance in any systems context.

## What's Next

This course connects directly to the systems thinking I use daily — understanding cache behaviour, memory hierarchy, and parallelism fundamentals helps when reasoning about performance in any context. I'm not planning to go deeper into architecture specifically, but the concepts here are foundational for any performance-oriented engineering work.

*Iterated with my own opinion and thoughts with the help of AI.*
