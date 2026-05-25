---
title: "OMSCS: CS 7650 Natural Language"
date: 2025-12-11
draft: false
categories: [learning, omscs]
tags: [omscs, georgia-tech, cs-7650, natural-language-processing]
description: "My reflection on NLP — from probability foundations to Transformers."
---

Taking Natural Language Processing in Fall 2025 was a mixed experience. I'd taken a similar course during undergrad, and there was significant overlap — enough that a good portion of the material felt like revisiting ground I'd already covered. That said, the deeper dive into Transformers and attention mechanisms was new and genuinely useful.

## Course Snapshot

| | |
|---|---|
| **Course** | CS 7650 Natural Language |
| **Term** | Fall 2025 |
| **Credits** | 3 |

## Why I Took It

I've always been drawn to language — how we communicate, how meaning is constructed, why the same sentence can mean different things in different contexts. NLP sits at the intersection of that human interest and the machine learning techniques I've been building up through OMSCS. Plus, with LLMs becoming ubiquitous, I wanted a proper academic foundation for understanding what's happening under the hood — not just the API-calling layer.

## What The Course Covered

The course started with foundations and gradually built up to modern architectures. The first few modules covered core probability theory — Bayes' rule, the Naive Bayes assumption — applied to text classification with Bag-of-Words features. Then we moved through language modeling with n-grams and RNNs/LSTMs, which gave me a much better appreciation for what "understanding fluency" actually means in a mathematical sense.

The middle of the semester covered document semantics and word embeddings — the shift from TF-IDF and cosine similarity to distributional semantics (skip-gram, CBOW). The idea that "a word is known by the company it keeps" is deceptively simple; the mechanics of learning those embeddings from co-occurrence statistics is where the real insight lives.

Then came Transformers. The course spent significant time walking through the architecture — self-attention, multi-headed attention, positional encoding, residual connections, layer normalization. The QKV hash-table analogy was the thing that made it click for me: queries, keys, and values as a differentiable retrieval mechanism. BERT and GPT were covered as specific instantiations of the Transformer paradigm, which helped contextualise the explosion of models that came after.

The second half of the course surveyed applied NLP tasks: classical information retrieval (inverted indices, TF-IDF, evaluation metrics), task-oriented dialogue systems (intent prediction, slot filling, state tracking), summarization (extractive vs. abstractive, the hallucination problem), machine reading and information extraction, open-domain question answering (the retriever-reader framework), and machine translation.

## What I Built (Conceptually)

The assignments spanned the full pipeline of NLP system design. One focused on building a text classification system — feature engineering with Bag-of-Words, implementing Naive Bayes and logistic regression from scratch, then comparing against a neural approach. Another centred on language modelling: building an n-gram model with smoothing, implementing perplexity calculation, and seeing how model size affects fluency.

The later assignments shifted to modern architectures — implementing attention mechanisms and working with pre-trained Transformer models for downstream tasks like summarization and question answering. One project involved building a retriever-reader pipeline for open-domain QA, which tied together everything from information retrieval to neural reading comprehension.

That said, I found the assignments poorly designed overall. They tended to test a narrow band of understanding — mostly implementation recall rather than deeper reasoning, analysis, or design. There wasn't much variation in difficulty or in the type of thinking required, which made the course feel flat compared to others in the program. A course covering such a broad field should have assignments that challenge different levels of understanding, and this one didn't deliver on that front.

## Concepts That Stuck

- **Attention as a general mechanism, not just a Transformer thing.** The course made clear that attention was originally developed to enhance RNNs — the fact that it became powerful enough to entirely replace recurrence is remarkable. Understanding the QKV intuition has made reading literally any modern NLP paper easier.

- **The two-stage pipeline pattern.** Whether it's IR (retrieval → reranking), QA (retriever → reader), or summarization (selector → generator), there's a recurring architectural pattern of coarse selection followed by fine-grained processing. This design philosophy shows up everywhere.

- **Evaluation metrics are deeply flawed.** BLEU and ROUGE measure n-gram overlap, not meaning. Newer metrics like BERTScore and BARTScore improve on this, but human evaluation remains the gold standard — and that's a real problem for rapid iteration.

- **Hallucination is the key barrier to deployment.** For any generative task, fluency and grammar are no longer the bottleneck. The hard problem is factual faithfulness — making sure the model says something true, not just something that sounds right.

- **Distant supervision is both brilliant and brittle.** Automatically labelling training data from existing knowledge bases is a powerful idea, but the noise it introduces means you're always fighting dirty data.

## What Was Hard

The Transformer architecture was the most conceptually dense part of the course. Understanding how self-attention, multi-headed attention, positional encoding, residual connections, and layer normalisation interact in a single forward pass took multiple passes. The mechanics of folding tensors for multi-headed attention (and the subsequent un-folding) were particularly tricky.

The mathematical side was also demanding in places — computing gradients of cross-entropy loss through softmax, backpropagation through attention mechanisms, and understanding perplexity as exponentiated normalised cross-entropy all required solid probability fundamentals.

## Workload And Rhythm

The workload was manageable but steady. Weekly lectures with associated readings from the Eisenstein textbook, regular quizzes to reinforce concepts, and a few larger assignments spaced across the semester. The project-based assignments required more time than the quizzes, particularly the ones involving attention and Transformer-based models. I'd estimate 10–15 hours per week on average, with spikes during assignment deadlines.

## Takeaways

NLP is a field in rapid transition, and the course does a reasonable job of covering the landscape from classical to modern approaches. The Transformer and attention content was the most valuable part. The assignments were the weakest link — they didn't do justice to the breadth of the material. Combined with the significant overlap with my undergrad NLP course, it wasn't the most rewarding course in the program, but the newer material on attention, Transformers, and retriever-reader pipelines was worth the time.

## What's Next

This course has given me a much better foundation for understanding LLMs and building systems around them. I'm not planning to dive deeper into NLP specifically, but the concepts here — especially the retriever-reader pattern and attention mechanisms — are directly applicable to the agent-based systems and RAG pipelines I work with in my homelab and personal projects.

*This post was summarized from course notes and materials with AI assistance.*
