---
title: 'Agent Mindsets'
description: 'What happens when you split the concept of an agent into infrastructure and identity — and only share the identity.'
pubDate: 'Apr 04 2026'
---

[OpenClaw](https://openclaw.ai) is an open-source platform for running AI agents. You define agents in a config file — each one gets a workspace, memory files, tool access, and channel bindings that route messages to them. An agent bound to a Discord channel responds to everything posted there. Threads inherit the binding. That's the basics.

This post is about what happened when we stopped thinking of agents as agents and started thinking of them as mindsets.

## Agents Are Two Things

Here's what an "agent" usually means: a bundle of infrastructure (workspace, memory, tools, permissions) and an identity (personality, voice, decision-making style). These two things are always packaged together. Your "researcher agent" has researcher tools AND a researcher personality. Your "code agent" has code tools AND a coder personality.

This bundling creates the classic multi-agent problem. Different identities need to coordinate. They disagree about what "done" means. They develop conflicting mental models. You build protocols for handoffs, shared state, conflict resolution. Half the work becomes making the agents talk to each other.

The mindset pattern unbundles this. Each mindset gets its own infrastructure — workspace, memory, tools, channel bindings. They're full agents in every functional sense. But they share one identity. Same personality, same voice, same opinions, same decision-making style. Different focus.

This isn't a compromise. It's a recognition that identity and infrastructure serve different purposes. Infrastructure needs isolation — you don't want your financial admin context polluting your code debugging context. Identity needs consistency — you don't want to manage five different personalities and the communication protocols between them.

## How It Started

Day one had four mindsets: a main coordinator, an infrastructure mode, a dev mode, and a personal assistant mode. Each got a Discord forum channel. Main was the "Chief of Staff" — it triaged incoming work, broke it into tickets, and delegated to the right mindset.

This worked for about 48 hours.

## What Broke

### The Ticket System Collapsed

The original design treated Discord forum threads like Linear tickets. Main creates a thread titled "Set up DNS for blog.justin.vin," assigns it to the infrastructure forum, the infra mindset picks it up. Clean pipeline.

Real work isn't a pipeline. A DNS thread becomes a Cloudflare certificate thread, which becomes a Pages-vs-Workers decision, which requires a build system redesign. The "ticket" title is wrong within an hour. The scope mutated three times before lunch.

The fix: **threads aren't tickets, they're focused conversations.** Their titles reflect current state, not original assignment. When the focus shifts, the title updates. No backlog, no grooming, no status tags.

### Main Couldn't Be a PM

Main was designed as a project manager — maintaining a board, tracking statuses, running standups. This broke at scale. At fifteen concurrent threads, Main spent more time managing the board than routing work.

Worse, PM behavior encouraged Main to pre-solve problems. It would write "use Astro with MDX, deploy to Cloudflare Pages" in the delegation — making technical decisions that the receiving mindset would have made better independently.

The fix: **Main became a router.** It reads incoming messages, identifies which mindset should think about them, and opens a thread with minimal context. No specs. No board. The thread takes it from there.

The bright line: **if it has a file extension, Main doesn't touch it.** This sounds arbitrary but eliminated the entire class of "Main accidentally implemented something" bugs.

## The Architecture Now

Seven mindsets, each a full agent with its own workspace and memory. One shared identity across all of them. Managed primarily from a phone via voice messages.

### Voice-First Mobile

This wasn't designed. Discord on mobile supports voice messages. Talking into your phone while walking to get coffee turns out to be a natural way to manage the system. "Check if that DNS propagated." "Open a thread in finance for the HMRC payment." "The blog post is outdated, needs a rewrite."

The mindset doesn't know if input was typed or spoken. But the workflow is different. You manage by voice, check thread titles later, tap into the ones that need attention. Most real orchestration happens this way.

### Heartbeats and Steering

**Heartbeats** run every 30 minutes. Each mindset checks its forum for stale threads, unanswered messages, blocked work. Also handles background monitoring — email, calendar, deploys.

**Steering** is course-correction without context destruction. When a thread goes off-track, you redirect it. It keeps its accumulated context but shifts focus. The rule we learned: steering is for course-correcting the same task, not for scope expansion. New problem = new thread.

## What We Actually Use It For

Real examples from the first two weeks:

**Financial admin.** Threads tracking HMRC tax payments — communicating with the accountant, sending bank transfers, following up on confirmation. Another monitors prescription pickups, watching for pharmacy emails and setting escalating reminders before the order gets canceled.

**Infrastructure debugging.** A gateway crash loop that turned out to be a browser profile plugin writing to the config file, triggering restarts, hitting Discord rate limits during startup, and crashing. The thread traced this across three layers without losing context.

**Contact management.** Imported 734 contacts, merged 184 duplicates, added WhatsApp profile photos, created 156 new contacts from messaging apps, built a CRM with 105,000+ logged interactions. Each phase was its own thread — parallel execution, no context pollution between them.

**Content and social.** Managing accounts across Reddit, Hacker News, and YouTube. Created accounts, developed posting strategy, manages engagement — with its own memory of what worked and what got spam-filtered.

## The Evolution

The system started with four mindsets and now has seven. The number isn't what changed — the architecture did.

**Week 1:** Main is a PM. Threads are tickets. Linear was briefly considered. Five mindsets in a hierarchy.

**Week 2:** Main is a router. Threads are conversations. Seven mindsets, flat structure. Voice messages are the primary input. Heartbeats keep things moving.

What forced the changes:

- **Scale.** Ten threads work as a kanban board. Thirty concurrent threads do not. The PM overhead became the bottleneck.
- **Mobility.** Phone interfaces reward glanceability (thread titles) over dashboards (board views). Once voice worked, the phone became the primary interface.
- **Autonomy.** Threads given the problem directly made better decisions than threads given prescriptive specs from Main.
- **Emergence.** The finance, socials, and seeds mindsets weren't planned. They spun up when existing mindsets were context-switching too aggressively. The system grew where it needed to.

## What Actually Matters

**Boundaries over capabilities.** The most important config for each mindset isn't what it can do — it's what it won't. "Main never implements" prevents more bugs than any linter.

**Thread titles are your dashboard.** `🔧 Gateway crash loop` → `⏳ Waiting on deploy` → `✅ Stable 4h`. If you can't tell what's happening from the title, the system is opaque.

**Don't fight the substrate.** Discord forums aren't Jira. They're persistent threaded conversations. Build around what they are.

**Identity is the expensive part.** Coordinating between different identities is where multi-agent systems burn most of their complexity budget. Share the identity, isolate the infrastructure. You keep the benefits and lose the coordination tax.

## The Meta Moment

This post was rewritten by my design-engineer mindset after a voice note said the original was outdated. It read memory files across seven workspaces to understand how the system works now versus ten days ago, drafted it, and deployed it — all in one thread.

One identity. Seven thinking modes. Just config and markdown on top of [OpenClaw](https://openclaw.ai).

Hi, I'm Justin.
