---
layout: post
title: "A day at Applied AI Conf: agents, auth layers, and one big idea I keep thinking about"
date: 2026-05-28 20:00:00.000000000 +02:00
type: post
parent_id: '0'
published: false
password: ''
status: draft
categories:
- AI
- Conferences
tags:
- AI
- Agents
- MCP
- Observability
meta:
  _edit_last: '1'
  hefo_before: '0'
  hefo_after: '0'
author:
  login: graffzon
  email: zonov@duck.com
  display_name: Kirill
  first_name: ''
  last_name: ''
permalink: "/applied-ai-conf-2026-wrapup/"
excerpt: "Notes and takes from a day at Applied AI Conf {Tech: Europe} — on agents as org-level infrastructure, MCP vs CLI tradeoffs, and why the boring parts of agent architecture (auth, egress, audit) are actually the interesting parts."
---

![Applied AI Conf badge](/assets/2026/05/applied-ai-conf-badge.jpg)

I don't go to many conferences. Between the "AI is eating the world" keynotes and the vendor booths with free socks, the signal-to-noise ratio can get rough. But Applied AI Conf felt different on paper — smaller, more practitioner-focused, the kind of event where someone might actually show code. So I went.

I work as an SRE at deepset.ai, where I'm currently in the middle of replacing Datadog with an open-source observability stack and wiring it into an AI-native workflow. So my radar for anything about tooling, infrastructure, and how agents interact with the systems around them was tuned particularly high today.

Here's what stuck.

## Dust: agents are not a personal tool, they're company infrastructure

The first talk was from [Dust](https://dust.tt), and the framing they opened with landed immediately: stop thinking of an AI agent as something that belongs to a person. Think of it as something that belongs to the organization.

Their slide said "Agents Deserve Servers, Not Computers" — and the analogy is actually pretty clean. Today, most agent setups look like the personal computer model: one agent, one sandbox, one person. Dust is pushing toward a shared server model where a single agent (or set of agents) runs on shared infrastructure — SQLite db, custom UI, bash access, auth layer — accessible by humans, teams, and other agents alike.

![Agents Deserve Servers, Not Computers](/assets/2026/05/dust-agents-deserve-servers.jpg)

It's one of those ideas that sounds obvious once you hear it but changes how you think about the problem. If you're building tooling for a company — not just a personal productivity hack — agents probably shouldn't be ephemeral processes that live and die per session.

### The MCP vs CLI vs MCP CLI breakdown

The other thing from this talk I'll be thinking about for a while: their comparison of integration patterns for giving agents access to tools.

![MCP vs CLI vs Code](/assets/2026/05/dust-mcp-vs-cli.jpg)

The short version:
- **CLI/Code** is chainable and has zero schema overhead, but every integration needs its own auth plumbing and there's no audit trail — "can't track what the agent did"
- **MCP** gives you auth discovery and typed audit trails, but you pay in schema bloat (they called out GitHub MCP: 93 tools = 55k tokens) and no chainability between calls
- **MCP CLI** is their proposed middle ground: POSIX composability, lazy schema loading (fetched only when needed), MCP-style auth and governance, per-action authorization levels

The per-action authorization bit is what caught my eye — `never_ask / low / medium / high` — a structured way to say "this action is safe to auto-approve, that one needs a human in the loop." Most teams I know are handling this with a mix of vibes and prayer, so seeing a formal model for it was refreshing.

### The egress proxy

![Authentication and Egress Control](/assets/2026/05/dust-egress-proxy.jpg)

The part that landed hardest for me given my current work: they've built an egress proxy that's deny-by-default. Every outbound TCP/HTTPS call from a sandbox goes through it. Allowlisted domains pass; anything else is blocked until a user explicitly approves it.

They're also doing secret substitution at the proxy layer — the agent never sees the actual API key, just a placeholder, and the proxy swaps it at egress time. Clean separation of concerns, and it means your audit trail shows what the agent *tried to do* rather than just what it successfully did.

This is exactly the kind of infrastructure I'd want in an AI-native observability stack. The challenge right now isn't just "can the agent query my metrics" — it's "can I audit what it queried, limit where it calls out, and not hand it the keys to everything." The egress proxy pattern answers all three at once.

The bi-directional harness diagram — Sandbox, Projects, Tasks, Conversations, Files, Tools all flowing both ways between human and agent — felt like a roadmap for what mature agent infrastructure actually looks like.

![Bi-directional Harness](/assets/2026/05/dust-bidirectional-harness.jpg)

---

## LobsterX: dissecting an agent with a scalpel

The second talk took a different approach — instead of architecture philosophy, it was a live autopsy of a real agent.

[Clelia Astra Bertelli](https://github.com/AstraBert) (AstraBert on GitHub) built [LobsterX](https://github.com/AstraBert/workflows-acp/blob/main/packages/lobsterx/README.md) — a document-processing agent that lives in Telegram. You send it a PDF and a task; it parses, extracts, classifies, reasons, and replies when it's done. The whole agent is ~600 lines of code. The workflow orchestration underneath is ~1.5k. Three swappable LLM providers.

![What is LobsterX](/assets/2026/05/lobsterx-what-is.jpg)

The framing device was four anatomical metaphors: **Brain** (the LLM), **Loop** (the event-driven workflow), **Eyes & Limbs** (filesystem and tools), **Ears & Mouth** (how it talks to a human).

![Why dissect an agent](/assets/2026/05/lobsterx-why-dissect.jpg)

It's a good framework. The "interesting engineering lives in the gap between prompt-in and answer-out" line is one of the better one-liners I heard today.

### Structured outputs all the way down

The Brain section had something I'll probably steal: every LLM call is constrained by a typed JSON schema. No free-form prose allowed, ever. One schema per operation type — a `Think` call looks different from an `Act` call — which forces a hard separation between reasoning and action at the code level.

![Steering structured outputs](/assets/2026/05/lobsterx-structured-outputs.jpg)

There's no "raw chat" escape hatch in the agent code. The wrapper exposes only structured-generation methods. This means the same schema works across OpenAI, Anthropic, and Google without any provider-specific branching in the agent logic.

I like this. It's the kind of constraint that feels annoying when you're building but you thank yourself for later.

### The virtual filesystem

The Eyes section was about [AgentFS](https://agentfs.ai) — a virtualized filesystem layer built by [Turso](https://turso.tech) that LobsterX uses instead of the real machine FS. The agent gets `read / write / edit / grep / glob`. No delete. No shell execution. Credential files like `.env` are excluded entirely.

![The Eyes virtual filesystem](/assets/2026/05/lobsterx-eyes-agentfs.jpg)

The slide had a line I wrote down verbatim: *"If the agent is jailbroken into writing something destructive, the damage stays inside the virtual FS. Nothing leaks to the host unless you explicitly sync it."*

That's a containment model. Same spirit as the Dust egress proxy — you're not trying to make the agent perfectly safe, you're bounding the blast radius when it's not.

AgentFS from Turso is worth a closer look if you're building agent infrastructure. It's a standalone open-source project with Python, TypeScript, and Rust SDKs.

---

<!-- MORE TALKS TO BE ADDED THROUGHOUT THE DAY -->

---

## Takeaways

<!-- TO BE WRITTEN AT END OF DAY -->

---

<!-- LINKEDIN ANNOUNCEMENT DRAFT -->
<!--
Just published my wrap-up from Applied AI Conf {Tech: Europe} 👇

The talk that stuck with me most wasn't about a new model or a new benchmark — it was about something much more boring: auth layers, egress proxies, and audit trails for AI agents.

Dust made the case that agents shouldn't be personal tools — they're company infrastructure. And if that's true, the unsexy plumbing (who can call what, where does it call out, what gets logged) matters a lot.

Full notes and takes on my blog: [LINK]
-->
