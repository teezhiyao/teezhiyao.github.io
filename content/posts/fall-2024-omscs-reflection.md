---
title: "Fall 2024 OMSCS: GIOS and IHPC"
date: 2024-12-15
draft: false
categories: [learning, omscs]
tags: [omscs, georgia-tech, cs-6200, cse-6220, operating-systems, high-performance-computing]
description: "My reflection on Fall 2024 — my first OMSCS semester with GIOS and IHPC."
---

Fall 2024 was my first semester in OMSCS, and I took on two courses: CS 6200 Graduate Introduction to Operating Systems (GIOS) and CSE 6220 Intro to High Performance Computing (IHPC). It was a heavy start, but the two courses complemented each other well — GIOS covered the abstractions and mechanisms of modern systems, while IHPC pushed into the algorithmic side of parallel computation.

---

## CS 6200 — Graduate Introduction to Operating Systems

GIOS was the anchor course of the semester — a deep, practical dive into how operating systems work, from single-node mechanisms through to distributed systems. The course is structured around the lens of abstractions, mechanisms, and policies, which became a recurring mental model I still use when thinking about systems design.

### What The Course Covered

The course moved from the ground up. It started with OS fundamentals — user vs. kernel mode, system calls, and the abstraction/arbitration role of the OS. Then processes and process management: address spaces, the process lifecycle, context switching (and how expensive it is), and inter-process communication through message-passing and shared memory.

Threads and concurrency followed — mutexes, condition variables, deadlock, and the practical decision of when to spin vs. when to block (the 2× context-switch overhead heuristic was one of those rules of thumb that kept coming up). Scheduling came next, from basic algorithms (FCFS, Round Robin, MLFQ) through to Linux's O(1) scheduler and the Completely Fair Scheduler with its red-black tree of vruntime.

Memory management covered virtual-to-physical translation, hierarchical page tables (and why single-level 64-bit tables don't work), TLBs, and demand paging. Then a module on virtualization — hypervisor-based vs. hosted models, paravirtualization, and hardware extensions like Intel-VT.

The second half shifted to distributed systems. Remote Procedure Calls — the full lifecycle from registration through marshalling to execution, with Sun RPC and XDR as the concrete implementation. Distributed File Systems — NFS, Sprite, and the design tradeoffs around caching, stateless vs. stateful servers, and file sharing semantics. Distributed Shared Memory — the consistency model spectrum from strict (impossible) to weak (fast but subtle). Finally, a high-level overview of datacenter technologies and cloud computing.

### What I Built (Conceptually)

The projects were the heart of GIOS. The first focused on multithreaded programming with synchronization primitives — understanding deadlock, race conditions, and the real performance implications of lock contention. The second involved implementing single-node OS mechanisms: scheduling policies and inter-process communication. The third built a distributed service using RPC — writing interface definitions, generating stubs, handling marshalling and unmarshalling, and managing client-server communication across address spaces.

The RPC project in particular brought everything together — you needed to understand the abstraction (remote calls look local), the mechanism (XDR encoding, stub generation), and the policy decisions (binding strategies, partial failure handling).

---

## CSE 6220 — Intro to High Performance Computing

IHPC was the more mathematically demanding course of the two. It covered three distinct parallel computing models and required comfort with Big-O analysis, probability, and linear algebra. The practical programming was in OpenMP and MPI, which made the concepts tangible.

### What The Course Covered

The course was organised around three models. Unit 1 covered two-level memory — the I/O model where a fast scratchpad sits between processor and main memory, and the core algorithmic challenge is minimising data movement. This included cache-aware and cache-oblivious algorithms, plus a reading session on the classic Hong & Kung paper on I/O complexity.

Unit 2 covered shared-memory parallelism through the work-span model — where speedup is fundamentally bounded by the longest chain of dependencies (the span). OpenMP was the practical framework here. Topics included parallel sorting, prefix sums, linked list algorithms, and parallel graph algorithms like BFS.

Unit 3 covered distributed-memory parallelism through message passing. MPI was the programming model. This unit covered network topology effects, dense and sparse linear algebra, distributed sorting, and graph partitioning — which turned out to be a cross-cutting concern for both load balancing and minimising communication volume.

There was also a guest lecture from the NSA on the real-world HPC lifecycle, which was an interesting look at how these techniques are applied at scale in practice.

### What I Built (Conceptually)

The mini-projects involved writing parallel code in OpenMP and MPI. The OpenMP work focused on shared-memory parallelism — parallel loops, reductions, and managing thread-level synchronisation. The MPI work dealt with distributed communication — send/receive semantics, collective operations, and the practical challenges of decomposing work across processes that can't share memory.

---

### The Pairing

GIOS and IHPC turned out to be a good combination. GIOS gave me a deep understanding of the systems that parallel code runs on — caching, memory hierarchies, scheduling, and the OS abstractions that sit between hardware and applications. IHPC took that understanding and showed how to design algorithms that work well on those systems. The practical overlap was in the programming projects: GIOS's systems programming (C, synchronisation, RPC) and IHPC's parallel programming (OpenMP, MPI) required similar attention to low-level performance details, just at different layers of the stack.

The workload was substantial. Two graduate courses in a first semester was ambitious, and there were weeks where the project deadlines lined up in a way that made things tight. But having the two perspectives — systems and algorithms — made the effort worthwhile.

### What Was Hard

GIOS's heaviest lift was the RPC project, where understanding the full lifecycle from interface definition through marshalling to distributed execution required keeping a lot of moving parts in your head at once. The scheduling calculations and page table size problems in the exam were also precision-intensive.

IHPC's difficulty was more theoretical. The I/O complexity analysis and the work-span model required a level of mathematical formality that took some adjustment. The MPI programming had its own challenges — debugging distributed code with deadlocks and communication mismatches is a very different skill from debugging sequential programs.

### Concepts That Stuck

- **The abstraction/mechanism/policy lens** from GIOS is the most transferable mental model I picked up all semester. It applies to nearly every systems problem.
- **The 2× context-switch heuristic** — if idle time exceeds twice the context switch cost, yielding is worth it. A simple, practical rule.
- **Page table hierarchies exist because the alternative is absurd** — a single-level 64-bit page table is 32PB per process.
- **Memory hierarchy is a first-class algorithmic concern** — minimising data movement is often more important than minimising operations. IHPC drove this home.
- **Work-span vs. distributed memory** — shared-memory and message-passing parallelism require fundamentally different algorithm design approaches. The span bound is a clean abstraction; MPI's blocking semantics force you to think about coordination explicitly.
- **Graph partitioning as a cross-cutting concern** — appears in both load balancing (shared memory) and communication minimisation (distributed memory).

### What's Next

This was a strong foundation semester. GIOS gave me the systems grounding I needed for everything that followed, and IHPC introduced the parallel mindset that shows up in architecture and performance work. I knew going forward that I could handle two courses in a semester, but also that pairing matters — complementary courses like these work better than two that compete for the same mental bandwidth.

*Iterated with my own opinion and thoughts with the help of AI.*
