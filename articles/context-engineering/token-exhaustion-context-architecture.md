---
title: "Token Exhaustion Is a Context Architecture Problem"
date: 2026-04-15
author: "Krish Sunkavalli"
tags:
  - context-engineering
  - ai-architecture
  - production
  - llm
description: "Token budgets run out faster than expected not because models are hungry but because context architecture was never designed."
categories:
  - Context Engineering
image: assets/images/token-exhaustion-context-architecture-social.png
---

# Token Exhaustion Is a Context Architecture Problem

Token budgets run out before teams expect them to. Not because the models are greedy or the subscriptions are undersized. Because nobody designed the context architecture before the first session started.

Most teams discover their token consumption problem the same way: a subscription balance that drops faster than the work justifies, an agent that starts losing track of its goal mid-session, or a week where three different tools hit their limits and the root cause turns out to be the same across all of them. The instinct is to blame the tool or the pricing tier. The real cause is structural. And structural problems do not resolve with better habits.

## Context Has No Garbage Collector

Every token that enters an AI session stays there until the session ends. File attachments, tool outputs, reasoning traces, conversation history. The context window fills like a buffer with no flush. This is not a bug. It is the architecture.

The problem is that most practitioners treat context like working memory in a human conversation: something the model manages automatically, discarding what is no longer relevant. It does not. The model holds everything it was given for the duration of the session and cannot distinguish between a planning note that expired three exchanges ago and a constraint that is still active. It holds all of it, and that holding costs tokens on every subsequent call.

This is why long-lived sessions are expensive regardless of how few prompts they contain. A session that starts with exploration, drifts into planning, and finishes in execution accumulates noise from all three stages. The model's attention dilutes. You spend the rest of the session correcting drift, which burns more tokens than the original task would have required in a clean context. The reframe is simple: context is a shared resource, and the session designer is the only garbage collector it has.

## Global Instructions Are a Per-Session Tax

Persistent agent instructions applied globally, across all sessions regardless of task, are tokens spent before a single useful exchange begins.

The pattern is common: a developer sets up a detailed writing profile, a code style guide, a project conventions file, and attaches it as global context so every session starts with a consistent foundation. The intention is reasonable. The cost is real. A writing profile loaded into a debugging session contributes nothing to the task and inflates the context with constraints that are irrelevant to the problem. When those instructions conflict with the task-specific prompt, the model gets confused and the session deteriorates faster.

The principle to apply here is the same one that governs dependency injection: inject only what is needed for the specific invocation. Instructions should be scoped to task boundaries, not to users or tools. The global default should be lean. Reference material that a session occasionally needs should be pulled on demand, not preloaded into every session regardless of whether it is needed.

## Sessions Without Scope Accumulate Noise

The pattern that wastes the most tokens is also the most natural one: a session that starts as planning, becomes execution, then becomes debugging, all within the same context window.

Each phase accumulates artifacts from the previous one. When the model starts losing track of the current goal, the response quality drops and the corrective prompts multiply. Each correction adds more tokens to an already noisy context. The session becomes progressively more expensive as it gets longer, not because you are doing more work but because you are managing an increasingly contaminated context.

The architectural fix is a hard boundary: one task, one session. When a task is complete, end the session. Before starting the next, write a focused initialization prompt that carries only the state that matters. This does not require losing continuity. It requires a deliberate handoff: ask the agent to summarize decisions made, constraints that are still active, and the precise starting point for the next task. That summary becomes the first token in the next session, and the next session starts clean.

## Not Every Task Needs an Agent

The more capable the tool, the more tempting it is to delegate tasks that do not require it. Merging a branch. Searching and replacing a string. Running a known terminal command. Each takes three seconds in the IDE and costs real tokens in an agent session.

Individually, any single delegation is negligible. In aggregate, these small delegations become the invisible line items in every session budget. The pattern is insidious because it feels productive. The agent handles it, the task gets done, and the token cost is invisible until the balance drops.

The principle to enforce is clear: AI agents amplify intent. They are not replacements for tools that already handle a task faster and cheaper. Use the IDE for what the IDE does best. Use search for what search handles. Reserve the agent for tasks where inference and judgment are actually required. The habit is recoverable once named, but it needs to be named explicitly before a team can apply it consistently.

## Model Tier Is an Architectural Decision

Defaulting to the frontier model for every task is the AI equivalent of spinning up a production instance to run a lint check. The capability is there. The cost is not justified.

Flash-tier and mini models handle a significant share of routine tasks well: summarization, reformatting, simple completions, code explanation, short drafts. Frontier models are warranted for complex reasoning, multi-step planning, ambiguous problem decomposition, and tasks where getting it right on the first attempt saves more tokens than the model upgrade costs. The cost differential between tiers compounds quickly across a team operating at scale.

Model selection should be a deliberate architectural decision, not a default. Define which model tier is appropriate for which task category. Treat it the same way you treat instance sizing in infrastructure: right-size for the workload, escalate when the task genuinely requires it.

## Token Governance Belongs in Your AI Governance Framework

Individual token discipline matters. Enterprise-scale context architecture matters more.

When multiple engineers share AI tooling budgets, the patterns above stop being habits and become policies. An engineer who attaches a 40 KB instruction file to every session is not making a personal choice. They are consuming shared budget. A team that has no session scope guidelines is not being inefficient by accident. They are operating without a policy.

Instrument before you optimize. Without visibility into token consumption by session type, model tier, and task category, optimization is guesswork. The measurement layer has to exist before the governance conversation is meaningful.

Establish session checkpoints as a team standard for long-running work. For tasks that genuinely require sustained context across multiple sessions, define a continuation pattern: a structured prompt that summarizes state, active decisions, and the precise next step into a compact initialization block. This prevents context sprawl without sacrificing momentum and creates an auditable record of what the agent was told at each stage.

Treat global instructions as a shared interface, not a personal notepad. Enforce minimalism at the default level. Task-specific context belongs in task-specific sessions.

## Design for the Token Budget Before You Hit It

The engineers who sustain high output from AI tooling are not the ones who prompt better. They are the ones who treat session design as an architectural discipline: scope boundaries, resource constraints, and a defined exit condition for each unit of work.

Token exhaustion surfaces faster than expected because context architecture was never treated as architecture. It was treated as a usage question, which means it was never designed. Fix the structure, and the budget problem resolves. Keep treating it as a discipline problem, and you will keep solving it after the wall, when the evidence is already expensive.
