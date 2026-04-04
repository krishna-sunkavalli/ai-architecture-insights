---
title: "Context Engineering: The Architecture Discipline Behind Production AI Systems"
date: 2026-04-04
author: "Krish Sunkavalli"
tags:
  - context-engineering
  - rag
  - ai-architecture
  - production
description: "Model quality is fixed at inference time. What you control is what the model sees. Most production AI failures are context failures, not model failures."
categories:
  - Context Engineering
---

# Context Engineering: The Architecture Discipline Behind Production AI Systems

Every LLM has a hard constraint that no amount of model scaling fully eliminates: the context window. It is finite. It is stateless between calls. Everything the model knows at inference time — your instructions, your data, your conversation history, your tool outputs — must fit inside it, right now, for this single call. That constraint is not a footnote. It is the central architectural problem of building AI systems at scale.

Andrej Karpathy framed it well: if the LLM is the CPU, the context window is RAM. Context engineering is the operating system — deciding what gets loaded into that RAM, in what order, at what granularity, and for how long. Get it wrong and your system is slow, expensive, incoherent, or all three. Get it right and you have the foundation of something that actually scales.

The key insight: model quality is largely fixed at inference time. What you control is what the model sees. A focused, well-constructed 8K context will outperform a bloated, unfocused 128K context on most real-world tasks. Engineering that assembly deliberately is where the real leverage is.

---

## The Four Core Patterns

Every production AI system — customer service agent, code copilot, financial reasoning workflow — uses some combination of these four patterns.

### Retrieval-Augmented Generation

RAG is the workhorse. Instead of baking domain knowledge into model weights, you retrieve relevant documents at query time and inject them into the context window.

The architectural decision tree is non-trivial:

**Chunking strategy.** Fixed-size vs. semantic vs. hierarchical. Chunk size is a dial between retrieval precision and contextual completeness. Neither extreme is right.

**Retrieval mechanism.** Dense vector search, sparse keyword search (BM25), or hybrid. Hybrid outperforms either approach alone in enterprise settings, where queries range from specific keyword lookups to broad conceptual questions.

**Re-ranking.** Retrieved chunks usually need a second pass (cross-encoder or LLM-based ranker) to surface what is most relevant before injection.

**Graph RAG.** For knowledge domains with complex entity relationships — compliance frameworks, product hierarchies, org structures — pure vector retrieval misses traversal. Graph RAG adds a knowledge graph layer to navigate relationships, not just semantic similarity.

The tradeoff nobody designs for upfront: retrieval latency. Injecting five high-quality chunks can add hundreds of milliseconds at p95, depending on embedding model, index type, and reranking. At scale, this compounds. You need to decide whether to pre-compute retrieval results, cache aggressively, or accept the latency as a user experience cost.

There is also a faithfulness gap: even with high-quality retrieval, models hallucinate over retrieved content rather than grounding their response in it. Production RAG systems need faithfulness evaluation — citation verification, grounding metrics — not just retrieval quality metrics.

*Reference architecture: [rag-pipeline.drawio](rag-pipeline.drawio)*

### Memory Architecture

Stateless LLMs are a fundamental limitation. Every call starts fresh. Memory systems give AI applications continuity, and the architecture has four tiers:

| Memory Type | What It Holds | Lifetime | Mechanism |
|---|---|---|---|
| In-context | Active conversation, current task state | Single session | Direct inclusion in context window |
| External short-term | Recent interactions, session metadata | Hours to days | Cache (Redis, Cosmos DB) |
| External long-term | User preferences, facts, entity-centric knowledge | Persistent | Vector store + retrieval |
| Episodic | What happened, what was decided | Persistent | Compressed summaries + retrieval |

The design failure I see most often: context dumping. Putting an entire conversation history, a raw 5MB API response, or a full PDF transcript directly into the prompt creates a permanent per-turn token tax, buries instructions, and degrades model attention on what actually matters.

The right pattern: treat large data as artifacts managed by an external service. Pass references into the context, not raw payloads. Only retrieve and inject what the current turn actually needs.

*Reference architecture: [memory-architecture.drawio](memory-architecture.drawio)*

### Context Compression and Window Management

Context windows have grown dramatically (128K, 200K, 1M tokens). Large context does not mean better results. Liu et al. (2023) demonstrated "lost in the middle" degradation: models perform best when relevant information is at the beginning or end of the context, and significantly worse when it is buried in the middle. Cost and latency also grow linearly — or worse — with context size.

The practical implication: place the most critical content (instructions, key facts) at the start and end of the context, not the middle. This makes window management an active engineering concern, not a passive one.

The main techniques:

**Summarization.** Use a lightweight LLM call to compress older conversation turns into a structured summary before they fall out of the active window. Effective at agent-agent handoff boundaries.

**Context pruning.** Hard-coded heuristics to remove stale tool outputs, deprecated state, or low-relevance history. Faster and cheaper than LLM-based summarization, but less intelligent.

**Sliding window.** Maintain a fixed-length rolling history. Simplest to implement. Loses long-range context but predictable in cost and latency.

**KV-cache optimization.** Modern LLM inference runtimes cache key-value representations of static context. Structuring prompts to maximize cache hit rates is a meaningful cost optimization at scale. Anthropic, OpenAI, and Azure OpenAI now offer API-level prompt caching: static prefix tokens cached server-side at reduced cost. To benefit, keep your system prompt and static content at the front of the context and avoid reordering between calls.

*Reference architecture: [context-compression.drawio](context-compression.drawio)*

### Multi-Agent Context Isolation

For complex, multi-step workflows, forcing one agent to operate over a giant shared context is brittle and hard to govern. Multi-agent architectures partition the problem across specialized agents, each receiving only the context scope relevant to its task.

The key design principles:

**Scope by default.** Each agent sees the minimum context required for its subtask. Do not pass full conversation history across agent boundaries unless explicitly needed.

**Structured handoffs.** Agent-to-agent communication should use structured, compressed summaries — not raw chat logs.

**Fan-out and fan-in.** An orchestrator agent decomposes a task, spawns parallel specialist agents, and synthesizes their outputs. This pattern builds deep research pipelines, multi-source analysis workflows, and complex approval chains.

**Tool schema budgeting.** Tool and function schemas consume significant context budget. In agentic systems with 20-50 tools, schemas alone can consume 3-8K tokens per call. Which tools to expose per agent is a context engineering decision, not just a tooling decision.

**Data isolation and PII governance.** Context isolation is not just an architectural concern — it is a compliance concern. An agent processing customer PII must not leak that context into another agent's scope unless explicitly designed and audited. In regulated industries, this is a hard requirement.

*Reference architecture: [multi-agent-fanout.drawio](multi-agent-fanout.drawio)*

---

## The Failure Modes Worth Probing For

**Context poisoning.** A hallucinated or incorrect fact enters the context and gets treated as ground truth in subsequent turns. Mitigation requires validation layers and explicit grounding instructions.

**Context rot.** Performance degrades as context accumulates over a long session. The model starts losing coherence with early instructions. Compression and summarization are the antidotes.

**Context overflow.** Naive concatenation of all history, retrieved documents, and tool outputs exceeds the model's window. Requires deliberate budget management: allocating token headroom across context components before the request is assembled.

**Context fragmentation in multi-agent systems.** An orchestrator loses track of what specialist agents have already retrieved or decided. Requires structured state objects passed across agent boundaries, not freeform conversation history.

---

## What This Means for Platform Selection

Context engineering capability is a first-class differentiator when evaluating AI platforms. The questions worth asking:

Does the platform support hybrid retrieval (dense + sparse) natively, or will you bolt it on? What is the memory and session state story — is there a managed session service, or are you building that persistence layer yourself? How does the platform handle agentic context boundaries? What are the observability primitives — can you trace what was in the context window for a given inference call?

On Azure, the combination of Azure AI Foundry (model hosting and orchestration), Azure AI Search (hybrid retrieval and semantic reranking), Cosmos DB (session state and memory persistence), and Azure Monitor (context observability) gives you a coherent context engineering stack. It still requires deliberate architectural choices to wire together correctly. The platform does not make those choices for you.

---

## Context Strategy Is Architecture

The teams getting durable value from AI are not the ones with the best prompts or the largest models. They are the ones that treat context as a first-class system component with its own design patterns, failure modes, observability requirements, and engineering discipline.

The choices you make about retrieval granularity, memory tier, compression strategy, and agent scoping determine cost profile, reliability, and latency for the life of the system. They compound. They are significantly harder to refactor once you have real workloads on the system than to get right upfront.

If your architecture review does not include a context strategy, the architecture is incomplete.
