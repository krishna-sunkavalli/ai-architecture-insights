---
title: "AI Has Two Security Problems and Most Organizations Are Ignoring One of Them"
date: 2026-04-23
author: "Krish Sunkavalli"
tags:
  - ai-architecture
  - responsible-ai
  - ai-strategy
  - governance
  - azure
description: "AI is simultaneously accelerating the threat landscape and introducing a new attack surface that security teams were never designed to protect."
categories:
  - Responsible AI
image: assets/images/ai-security-emerging-threats-social.png
---

# AI Has Two Security Problems and Most Organizations Are Ignoring One of Them

The security conversation around AI has been almost entirely defensive in the wrong direction. Organizations are focused on whether AI will be used against them. The more pressing problem is that they have already deployed AI that is itself attackable, and they have no controls at the layer where those attacks land.

There are two distinct shifts happening simultaneously. First, AI has changed the economics and speed of exploitation. Second, the AI stack itself is a new attack surface with no established security baseline. Neither problem is fully solved by traditional security controls. Together, they require a structural rethink of posture management, not a feature bolt-on.

## The Exploit Window Closed While You Were Planning Your Patch Cycle

Until recently, the operational window between vulnerability disclosure and active exploitation gave security teams room to respond. Prioritize, test, patch, deploy. The cycle was uncomfortable but manageable. AI has closed that window.

Advanced AI models can autonomously discover weaknesses in code, chain multiple lower-severity findings into working end-to-end exploits, and produce proof-of-concept code. This is not a theoretical threat. Microsoft's own Secure Future Initiative work surfaces this directly: AI-driven vulnerability discovery runs at a pace that compresses the time between disclosure and exploitation from weeks to hours in high-value cases.

The implication is not that patching stops mattering. It matters more than ever. But the posture model that assumes organizations have time to triage, test, and schedule patches before exploitation occurs is broken. The new model is closer to: if it is patchable and you know about it, you have less time than you think.

Microsoft's identification of five dimensions where autonomous AI-driven attacks gain disproportionate advantage is a useful map of where exposure risk concentrates: unpatched software, untrusted open-source dependencies, customer source code, internet-facing assets, and baseline security hygiene gaps. These are not new categories. What is new is that attackers with AI-assisted tooling can now systematically probe all five dimensions at scale, automatically, without the bottleneck of human operator time.

The response has to match. Continuous asset discovery, automated scanning across first-party and open-source code, and security hygiene enforcement that does not rely on humans to catch every gap. The tools exist. The gap is treating them as optional rather than foundational.

## AI Models Are Not Trusted Code Running in a Safe Environment

The second problem receives less attention than it deserves, and it is the harder one.

When a development team pulls a pretrained model from a public registry, integrates a third-party ML framework, or builds an AI agent with access to internal APIs, they are introducing untrusted software into production with broad privileges and no security baseline. Pretrained models can carry embedded malware or backdoors that activate under specific conditions. Third-party datasets used in fine-tuning can embed adversarial patterns. The model lifecycle, from data sourcing through training, packaging, deployment, and decommissioning, is a supply chain problem. It has none of the tooling maturity that application security built up over the last two decades.

Traditional application security starts with a working assumption: the code is potentially buggy, but the runtime is trusted. AI inverts that. The runtime behavior of a model is opaque by design, training provenance is often unknown, and outputs are probabilistic. There is no equivalent of a signed binary or a vulnerability scan that clears a model as safe. Every model you deploy is effectively untrusted code executing with elevated privileges.

The controls that address this are now reaching production readiness. Model scanning in Microsoft Defender covers malware, unsafe operators, and backdoors across common model formats stored in Azure ML registries. CLI integration brings scanning into CI/CD pipelines so teams can gate model artifacts before they reach a registry. The principle is identical to what mature security teams already apply to container images: scan early, gate on risk, do not ship what you have not inspected. The operationalization differs because model formats are not containers, but the posture is the same.

The organizations that treat model deployment like application deployment but skip the inspection step are running untrusted binaries in production. That is not a risk that lives in the future tense.

## Runtime Enforcement at the Agent Layer Is the New Requirement

The most recent shift is at the agent layer, and it is where the security industry is most behind.

AI agents deployed in business workflows do not just process data. They invoke tools, make decisions with access to sensitive systems, and take actions with downstream consequences. An agent with access to internal APIs, email, and customer records is a high-value target for manipulation regardless of how securely it was built. Prompt injection, goal hijacking, and silent data exfiltration are viable attack vectors against agents. None of them are caught by traditional endpoint or application security controls.

The correct control point is the tool invocation layer. An agent that cannot route data to an untrusted destination cannot exfiltrate it, regardless of how the attack was structured. Real-time enforcement that evaluates every tool invocation before execution, blocks high-confidence threats before data access occurs, and generates SOC-ready alerts with full context on what was stopped, why it was flagged, and which agent and user were involved, gives security teams visibility into a threat class that was previously undetectable.

Microsoft Defender's integration with Agent 365's tools gateway reflects this architectural positioning correctly. The protection sits at the action boundary, not the model boundary. This matters because you cannot sanitize an LLM's internal state at runtime, but you can enforce constraints on what it is allowed to do. The enforcement model mirrors how mature organizations already approach least-privilege for service accounts: the constraint is on the capability, not on policing the actor.

That analogy extends further than it looks. An agent that has access to a tool capable of exfiltrating data should be treated identically to a service account with access to a secrets vault: every invocation audited, anomalous usage alerting, and high-risk action categories requiring explicit blocking or approval workflows. Building AI agents without a tool governance model is the equivalent of deploying service accounts with no access logging.

## What This Changes About How You Build and Operate AI

These two shifts change the operational model for AI deployments in ways that do not show up in most architecture reviews.

Security for AI is not a post-deployment concern. The model lifecycle requires controls starting at the supply chain layer. Verify where models come from, scan them before they reach a registry, and gate CI/CD pipelines on scanning results. Security posture for AI has to start at the same point where application security starts: before anything runs in production.

The time-to-patch assumption is now miscalibrated. Organizations running self-hosted or on-premises Microsoft workloads need to operate as if the update window is weeks shorter than it historically has been. Staying current is no longer a best-practice recommendation. It is the primary exposure control against AI-accelerated attack tooling. Cloud-hosted PaaS and SaaS workloads get this automatically; the risk falls disproportionately on hybrid environments where update deployment is manual and slow.

Posture management has to be continuous, not periodic. The five exposure dimensions that matter most under AI-driven threats map directly to capabilities that exist today: internet-facing asset discovery, code scanning across first-party and open-source repositories, and baseline hygiene enforcement. The architecture is available. The gap is treating these as always-on infrastructure rather than compliance checkboxes run before an audit.

## The Defense Has to Move at the Same Speed as the Threat

The organizations that will be meaningfully harder to compromise are the ones that close the gap between when they learn about a risk and when they act on it. AI has compressed that window on the attacker side. The defender response is to compress it on their side: automate discovery, automate patching workflows, enforce agent tool governance from the first deployment, and reduce the surface area that requires human triage.

The AI stack is not more dangerous than what came before it in any absolute sense. But it is different in ways that require controls at earlier stages of the lifecycle and at more granular enforcement points during runtime. The supply chain risk is real. The exploit window compression is real. The agent attack surface is real. Security teams that treat AI workloads as just another application tier are operating with a gap between their model and the threat landscape. That gap is already being measured in incidents, not hypotheticals.
