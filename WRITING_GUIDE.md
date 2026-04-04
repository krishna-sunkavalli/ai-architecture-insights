# Writing Guide: AI Insights

This guide defines the voice, structure, front matter standards, and cross-posting workflow for every article published on this site. Read it before writing. Re-read it before publishing.

---

## Author Context

Senior Presales Architect at Microsoft. Focused on Azure AI, cloud infrastructure, and enterprise AI adoption at scale. Writing toward a Principal Architect / Staff-level personal brand. Articles are published on GitHub Pages and cross-posted to LinkedIn.

---

## Target Audience

Principal Architects, Staff Engineers, VPs of Engineering, CIOs.

Readers who already know the fundamentals. Do not explain what a vector database is. Write to the person making the architectural decision, not the person learning the concept.

---

## Voice and Tone

**Direct and confident.** State positions. Do not qualify everything into uselessness.

**Principal-level insight.** The kind of thing a senior architect says in a whiteboard session that reframes how someone thinks about a problem. Not the feature list. The implication of the feature list.

**No hedging.** Strike these phrases before publishing:

- "it depends"
- "you might want to consider"
- "there are many ways to"
- "it's worth noting"
- "in some cases"
- "generally speaking"

**No filler openers.** Never start an article with:

- A definition of the topic
- A history lesson
- "In today's rapidly evolving AI landscape"
- "AI is transforming..."
- "As organizations increasingly..."

**Not vendor documentation.** Do not restate what the Azure docs already say. Every article must carry a point of view — an argument the docs do not make.

**Not a tutorial.** No step-by-step portal instructions, no screenshots of configuration screens, no numbered setup guides. Practitioners understand concepts without hand-holding.

**Practitioner voice.** Write from experience, not from a syllabus. "In production, X happens" is more valuable than "according to the documentation, X should happen."

---

## Formatting Rules

**No em dashes.** Neither `—` nor `--`. Use a period or restructure the sentence.

**No bullet point walls.** If you have more than four bullets, convert to prose or group them under a subheading. Bullet lists are for reference material, not arguments.

**No bold emphasis mid-sentence for effect.** Bold is for genuinely scannable reference content: table headers, key terms on first use. Not for dramatic emphasis.

**Headers must earn their place.** Use declarative statements or sharp questions. Not generic labels:

| Weak | Strong |
|------|--------|
| Overview | Why the Loop Model Breaks at Scale |
| Introduction | The Assumption That Gets Teams into Trouble |
| Background | What the Framework Docs Skip |
| Summary | What to Do With This |

**Length targets:**

- LinkedIn post: 150-250 words
- GitHub Pages article: up to 1,800 words
- LinkedIn cross-post (truncated version): 1,200 words max

---

## Article Structure

Every article follows this five-section structure. Sections do not need to have headings with these exact names. They are stages, not labels.

### 1. Opening

One sharp paragraph that states the problem or the position. No warmup. The first sentence should make the reader want to continue reading.

The opening is not an abstract or a table of contents. It is an argument starter. By the end of the opening paragraph, the reader should know what you think and why it matters.

### 2. The Insight

The reframe. The non-obvious observation. The thing the vendor docs present as a footnote but is actually the central issue.

This section should deliver something the reader could not have gotten from the product documentation. A pattern you have seen, a failure mode you have observed, a mental model that changes how the problem looks.

### 3. Architecture or Decision Section

Concrete and specific. Options: a pattern description, a tradeoff table, a decision framework, a comparison of approaches.

Diagrams belong here if they add clarity. Mermaid is supported. Use it when text alone cannot convey structure.

### 4. Implications

What does this mean for how you build, evaluate, or operate? This section bridges the insight to action. Not instructions. Architectural consequences.

"If this is true, then the thing you need to rethink is X."

### 5. Close

One tight paragraph. A forward-looking position or a clear recommendation. Do not summarize what you just wrote. End with a direction or a provocation, not a recap.

---

## What Makes a Good Article Topic

A good article topic is a decision architects actually face, with a defensible point of view on what the right answer is:

- A pattern that works in production that the docs present as an afterthought
- A failure mode that is common but underdocumented
- A reframe of how people think about a concept (example: context engineering is not prompt engineering)
- A tradeoff that receives nuanced treatment nowhere else
- A position on a build-vs-buy or platform decision that has a right answer

A bad topic is a survey of options with no position. If your article can be summarized as "here are the different approaches and each has pros and cons," you do not have an article. You have a vendor comparison matrix.

---

## What to Avoid

- **"In this article I will..."** Just start.
- **Announcing the structure before delivering it.** The reader will discover the structure by reading.
- **"I hope this was helpful"** or any variant. Do not end on deference.
- **Referring to the author in third person.** This is a personal site.
- **Lists of features.** Features are documented elsewhere. Implications of features are not.
- **Hedged conclusions.** If you do not know what to recommend, do not publish. Come back when you do.

---

## Front Matter Standard

Every article must include the following front matter block at the top of the file:

```yaml
---
title: "Sharp, declarative title. No clickbait. No questions that answer themselves."
date: YYYY-MM-DD
author: "[YOUR NAME]"
tags:
  - tag-one
  - tag-two
description: "One sentence. What the article argues, not what it covers. Use active voice."
categories:
  - Agents
image: assets/images/article-slug-social.png
---
```

**Title:** Declarative, not vague. "Agent Architecture Patterns That Work in Production" rather than "A Look at Agent Architecture."

**Date:** ISO 8601 format. Use the publication date, not the draft date.

**Tags:** 2-4 tags. Lowercase, hyphenated. From this set: `agents`, `context-engineering`, `prompt-engineering`, `ai-architecture`, `responsible-ai`, `ai-strategy`, `orchestration`, `multi-agent`, `rag`, `evaluation`, `governance`, `production`, `azure`, `llm`.

**Description:** One sentence. Describes the argument, not the topic. "Why most agent orchestration patterns fail under compliance scrutiny" rather than "An overview of agent orchestration patterns."

**Categories:** One of: `Agents`, `Context Engineering`, `Prompt Engineering`, `AI Architecture`, `Responsible AI`, `AI Strategy`.

**Image:** Path to the per-article social card image (1200x630px, PNG). Place files in `docs/assets/images/`. Name using the article slug. Used by the OG meta tag override for LinkedIn preview cards.

---

## Social Card Images

Each article should have a dedicated social card image for LinkedIn preview.

**Recommended dimensions:** 1200 x 630px

**Naming convention:** `docs/assets/images/[article-slug]-social.png`

**Reference in front matter:** `image: assets/images/[article-slug]-social.png`

Tools for generating social cards: Canva, Figma, or any image editor. The site uses a default fallback at `docs/assets/images/social-card.png` for articles without a custom image. Create this default card first.

---

## LinkedIn Cross-Posting Format

**Hook first.** The first line of the LinkedIn post is the hook. It must work as a standalone statement that makes someone pause mid-scroll. A declarative position, a counterintuitive claim, or a sharp question.

**Post structure:**

1. Hook (1 sentence)
2. The problem or pattern (2-4 sentences)
3. The key point (2-4 sentences)
4. Call to action linking to the full article

**Example structure:**

```
Most agent systems fail at the same boundary: the handoff between orchestration and execution.

The loop model works in demos. In production, it becomes a black box you cannot audit, cannot replay, and cannot explain to a compliance team.

The fix is structural, not a matter of better prompting. Three patterns hold up under real load. I wrote them up with tradeoff tables and decision criteria.

Full article with architecture depth: [link]

#AzureAI #AgentArchitecture #EnterpriseAI
```

**No hashtag walls.** Maximum 3 hashtags. Place them at the end, not scattered through the text.

**Link placement.** Include the full GitHub Pages URL before the hashtags: `Full article with architecture depth: [link]`

**Length target.** 150-250 words for the LinkedIn post. The article on GitHub Pages can go to 1,800 words.

**Timing.** Post on LinkedIn within 24 hours of publishing to GitHub Pages. LinkedIn posts perform best Tuesday-Thursday between 8-10am or 12-1pm in your primary audience's time zone.

---

## Publishing Checklist

Before publishing to `main`:

- [ ] Front matter is complete and follows the standard
- [ ] Description describes the argument, not the topic
- [ ] Opening paragraph has no warmup sentences
- [ ] No em dashes in the article
- [ ] No bullet lists with more than four items
- [ ] All headers are declarative statements or sharp questions
- [ ] Article ends with direction, not a summary or thanks
- [ ] Social card image created and referenced in front matter
- [ ] Article length within target (1,800 words max for GitHub Pages version)
- [ ] LinkedIn post drafted (150-250 words) with hook, position, and link

---

## Category Descriptions

**Agents:** Orchestration patterns, multi-agent systems, memory architecture, tool design, agent evaluation, production failure modes.

**Context Engineering:** Context window construction, retrieval design, chunking strategies, context compression, RAG architecture above the retrieval layer.

**Prompt Engineering:** System prompt design, few-shot patterns, instruction taxonomies, output structure, evaluation frameworks for probabilistic systems.

**AI Architecture:** Inference infrastructure, end-to-end system design, integration patterns, deployment architecture, latency and cost tradeoffs.

**Responsible AI:** Governance frameworks, bias evaluation, auditability architecture, red-teaming, compliance integration, human-in-the-loop design.

**AI Strategy:** Build vs. buy decisions, platform selection, team structure, AI program governance, the organizational constraints that determine what is actually buildable.
