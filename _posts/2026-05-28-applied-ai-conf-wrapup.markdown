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

### The Limbs: LlamaCloud for document understanding

Filesystem access alone only gets you plain text. LobsterX reaches for three [LlamaCloud](https://cloud.llamaindex.ai) tools to actually understand documents — each with its own typed input schema, which keeps the structured-output guarantee intact all the way down:

![The Limbs document tools](/assets/2026/05/lobsterx-limbs-llamacloud.jpg)

- **LlamaParse** — layout-aware parsing for PDFs, DOCX, PPTX, XLSX, images. Tables stay tables; figures get described by VLMs; reading order is preserved across columns
- **LlamaExtract** — you hand it a JSON schema, it hands back populated objects, typed, citation-linked, validated. "No glue prompt engineering on the agent side"
- **LlamaClassify** — classification into user-defined categories with confidence signals, used to route documents (invoice? contract? report?) before the agent decides what to do next

![Why these tools change the game](/assets/2026/05/lobsterx-llamacloud-why.jpg)

The key line: *"Each tool exposes a typed input schema, so the Act step can call them with full structured-output guarantees end to end."* The whole pipeline from document in to action out stays typed. That's not a small thing.

### Ears & Mouth: async by default

The interface choice was deliberate. Telegram was picked because messaging is async — no spinner, no held-open HTTP connection. Documents come in as attachments, land in AgentFS, and the workflow starts. The agent pings you back when it's done, which could be minutes or half an hour.

![Ears and Mouth async](/assets/2026/05/lobsterx-ears-mouth.jpg)

The slide had a line worth framing: *"The right interface for a long-running agent isn't a chatbot — it's a colleague who replies when they're finished."*

I've been building small personal agents that use the same pattern for my music project [trianglecore.rocks](https://trianglecore.rocks) — async Telegram bots that handle things I don't want to babysit. There's something genuinely calming about the UX once you stop expecting instant responses and start treating the agent like a person who's doing a task.

### A note on safety

The final recap slide was honest in a way I appreciated:

![Safety note](/assets/2026/05/lobsterx-safety.jpg)

Virtual filesystem, no shell access, no delete primitive, credential files excluded, custom behavior via `AGENTS.md` not arbitrary instructions — all of that is real. And then the caveat: *"None of this prevents prompt injection from a malicious document the agent has been asked to read. The mitigations bound the blast radius; they don't eliminate it."*

This is the right way to talk about agent safety. The goal isn't zero risk. The goal is knowing exactly what can go wrong and making sure the damage is contained when it does. Both talks today circled back to this: Dust with their egress proxy, LobsterX with the virtual filesystem. The industry seems to be converging on blast-radius thinking over "we solved it" thinking. That feels like progress.

---

## Kafka as agent infrastructure

One of the other talks made a case I wasn't expecting: use Kafka and Flink SQL as the communication backbone for agents, rather than synchronous A2A protocols.

![Flink SQL for stream processing](/assets/2026/05/flink-sql-agents.jpg)

The argument is straightforward once you hear it. Synchronous agent-to-agent communication means one agent calls another and waits. For anything with real latency — document processing, external API calls, multi-step reasoning — that wait is a problem. You're holding a connection open, you're blocking, and if something fails midway, you're rebuilding state from scratch.

Kafka flips this. Agents publish to topics and move on. Other agents consume when they're ready. The workflow state lives in the stream, not in the call stack.

The Flink SQL angle specifically was about access: SQL via a gateway is readable by business users, AI included. Where the DataStream API requires "battle-tested Java code maintained by capable engineers," Flink SQL can be generated, inspected, and modified by an agent without Java expertise. The slide framed this as a spectrum — DataStream for mission-critical, high-SLA workloads with a handful of complex jobs; Flink SQL for long-tail use cases, potentially millions of jobs, where AI is in the loop generating or modifying queries.

It's a natural pairing with what LobsterX was doing with Telegram. The interface is async, the transport is async, and now the agent-to-agent communication is async too. The whole stack is designed around the assumption that work takes time.

No Haystack integration with Kafka or Flink exists currently — something worth watching if you're wiring observability agents into a streaming pipeline.

---

## Parloa: the engineer as product-minded architect

One line from the [Parloa](https://parloa.com) talk that I'm still turning over: **"the engineer is now a product-minded architect."**

Parloa builds an AI agent management platform for enterprise contact centers — Allianz, Booking.com, SAP are customers, $3B valuation as of January this year. So when they say this, they're not talking about hobby projects.

The framing resonates though. When you're building agent-driven systems, the line between "does it work technically" and "does it do the right thing for the user" collapses. You can't hand off the product decisions to a PM and go back to your terminal. The agent's behavior, its tone, when it escalates, when it doesn't — those are engineering decisions with product consequences. An engineer who doesn't think in those terms is going to build something that passes tests and fails in production in ways no test would catch.

It's a role shift I feel working on observability at deepset. Instrumenting a system well isn't just a technical question. It's: what does the person on call actually need to know? What does "this is working" mean to someone who uses the product, not someone who reads the metrics? The tooling I build shapes what people pay attention to.

---

## Gradium: full-duplex voice AI

Not my domain, but a talk worth noting: [Gradium](https://gradium.ai) making the case that current voice AI is fundamentally half-duplex.

![Half-duplex vs full-duplex](/assets/2026/05/gradium-duplex.jpg)

The analogy is a good one. A walkie-talkie is half-duplex — one side talks, the other listens, you swap. A phone call is full-duplex — both sides can make noise at the same time. Human conversation works the same way: the listening party constantly produces small signals ("oh", "hmm", "right") that aren't interruptions, they're acknowledgements that keep the other person talking. Today's voice AI treats all of that as interruption and trips over it.

Gradium was founded by Neil Zeghidour, formerly of Google DeepMind and Kyutai — the research lab that released [Moshi](https://moshi.chat), the open-source full-duplex conversational AI they've commercialized.

The product they announced is **Phonon** — a ~100M parameter TTS model, private beta currently. Runs offline at 6x real-time on a single CPU core, which means it can live on a phone without ever touching a server. At that size, interesting for privacy-sensitive or truly offline use cases.

![Gradium Phonon](/assets/2026/05/gradium-phonon.jpg)

---

## The caching talk: prompt caching as the main cost lever

Caching came up in several conversations today, but one talk went deep on it specifically in the context of LLM cost management.

![What can you do about LLM costs](/assets/2026/05/caching-llm-costs.jpg)

The setup was a good rhetorical device: here's everything you *could* do to cut LLM costs when that's your core business — raise prices, add usage limits, remove expensive models, limit tool calling, prompt compression, smarter context windows. All struck through. The answer left standing: optimize prompt caching.

The nuts and bolts comparison of how OpenAI and Anthropic handle it differently was useful — the kind of operational detail that's obvious in hindsight but easy to miss:

![Turn on prompt caching](/assets/2026/05/caching-config.jpg)

OpenAI enables caching by default with a 5–10 minute retention window, but you can extend to 24 hours via `prompt_cache_retention` and customize the cache key. Anthropic disables it by default and gives you two modes — automatic caching or explicit cache breakpoints — with TTL-based pricing: 5 minutes at 1.25x cost, 1 hour at 2x.

The second half was about making this observable. The dashboard they showed tracked **Model Cache Rate** across all models in production — gpt-5.2, claude-opus-4-6, gpt-5.4, claude-sonnet-4-6 — with average hit rates in the 70–80% range. The token composition view breaking down cached vs non-cached tokens per request, with a note that green (cached) should consistently grow to cover the full bar as a conversation progresses, was a clean way to think about it.

![Build observability for caching](/assets/2026/05/caching-observability.jpg)

![Cache hit rate dashboard](/assets/2026/05/caching-dashboard.jpg)

This resonates with the observability work I'm doing at deepset. Cache hit rate is exactly the kind of metric that doesn't feel important until you've been paying for tokens you didn't need to pay for for three months. It belongs on the same dashboard as latency and error rate.

Worth noting: [Langfuse](https://langfuse.com) — whose observability UI appeared in the slides — has an official Haystack integration. If you're building on Haystack and want this kind of cache visibility, it's already there.

---

## Peec AI: the heavy model teaches, the cheap model ships

[Peec AI](https://peec.ai) is a Berlin company tracking brand visibility across AI search engines — ChatGPT, Claude, Gemini. The talk was about how they built the AI inside their product.

This one had one of the best single slides of the day.

![A heavy model judges what a cheap model produces](/assets/2026/05/peecai-asymmetry.jpg)

*"A heavy model can judge what a cheap model produces."*

The idea is elegant: expensive models are slow and costly to run in production, but they're genuinely smarter at evaluating quality. Cheap models are fast and cheap to run, but they need guidance. So don't use the expensive model in production — use it to teach the cheap model, then throw it away.

The mechanism is a tight feedback loop:

![The loop](/assets/2026/05/peecai-loop.jpg)

1. **Generate** — small model proposes a candidate based on the current prompt
2. **Reward** — three judges score it in parallel: relevance, tone-appropriateness, response-ability. Each returns a score *and a reason*
3. **Optimize** — heavy model reads the full history (scores, explanations, previous prompt) and rewrites the prompt
4. **Iterate** — back to step one, until reward saturates or the prompt stops moving
5. **Ship** — freeze the prompt, discard the heavy model. Production runs only the small model

The detail I found sharp: the optimizer never sees a raw number. It sees *"tone-appropriateness 0.3, profile is dry and self-deprecating, opener is enthusiastic and uses three exclamation points."* The explanation is the gradient.

No labeled data needed. Runs offline.

### Distill principles, not examples

The other key design decision: they instruct the optimizer to extract *principles*, not memorize good outputs.

![Distill principles not examples](/assets/2026/05/peecai-principles.jpg)

After four turns, a generic instruction like "write an engaging opener based on the profile" grows into: reference one concrete detail from the profile, match the writing style of the bio, end with a question answerable in one sentence. The heavy model is reverse-engineering what makes a good output good, then encoding that as instruction.

That distinction matters. If you memorize examples, you overfit. If you extract principles, you generalize.

### Reward hacking

The central failure mode they named:

![Reward hacking](/assets/2026/05/peecai-reward-hacking.jpg)

*"The optimizer satisfies every defined reward, while violating the unstated intent. Your reward model is your specification. Any gap is a hole the optimizer will find."*

They cited Goodhart, 1975: "When a measure becomes a target, it ceases to be a good measure." Fifty years older. Same idea.

The takeaway slide was the best line of the day:

![The loop is easy, the rewards are the engineering](/assets/2026/05/peecai-takeaway.jpg)

**"The loop is easy. The rewards are the engineering."**

Reward hacking isn't a risk to mitigate, it's the default. The advice: build the loop flexible enough that you can add a new reward in an afternoon, because you'll need to.

And the second takeaway, which is the cleanest summary of the whole approach:

![Pay the heavy model once to teach the light one](/assets/2026/05/peecai-pay-once.jpg)

**"Pay the heavy model once to teach the light one."**

The footnote: *"Same shape as distillation or RLAIF. The difference is that the output is a prompt, not a model. Cheaper to iterate on. Cheaper to debug. Cheaper to ship."*

That last part is what makes it practical. Changing a frozen prompt is a deploy. Retraining a model is a project.

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
