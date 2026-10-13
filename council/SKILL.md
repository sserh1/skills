---
name: council
description: "Multi-agent collaborative debate producing visible round-by-round transcripts with real intellectual friction — members are topic-briefed custom agents, run as a 3-round DEBATE or a 1-round QUICK check, to find the best path. USE WHEN council, debate, multiple perspectives, weigh options, deliberate, get different views, what would experts say, pros and cons."
license: MIT
metadata:
  source: "Adapted from danielmiessler/LifeOS, LifeOS/install/skills/Council v1.1.20 (MIT, see LICENSE.txt)"
---

# Council

## What It Does

Runs a multi-agent debate. Custom-composed agents discuss a topic over rounds, respond to each other's actual points, and surface insights through real intellectual friction. You get a visible round-by-round transcript plus a synthesis. DEBATE runs three rounds; QUICK runs one for a fast perspective check.

## The Problem

When you ask one model for an opinion, you get one frame and one set of blind spots. Asking for "pros and cons" gives you a flat list with no one actually pushing back. Real deliberation needs distinct experts who disagree on the merits and argue it out, so the weak parts of an idea get exposed before you commit. Generic built-in agents all sound the same and produce bland agreement; this skill composes topic-specific agents that create genuine friction.

## How It Works

Custom-composed agents discuss topics in rounds, respond to each other's points, and surface insights through intellectual friction.

## Members Are Custom Briefs

Write each council member inline as a short brief — a name, a role, a stance, and what they'll push on — then launch it with `subagent_type: "council-member"`. A bare built-in type with no persona is topic-ignorant and produces bland agreement. The friction comes from four *different* briefs, each with real domain expertise and a distinct analytical angle.

See `CouncilMembers.md` for the slot guidance and an example brief.

**Collaborative-adversarial:** members debate to find the best path rather than only attacking an idea. For a pure attack, give one member an explicit red-team brief.

## Ground Rules

- **Independent first.** Round 1 members never see each other's positions; launch them in parallel. This prevents anchoring on the first opinion voiced.
- **Label claims.** Members mark claims as fact (with source), assumption, or idea. The synthesis keeps the labels.
- **Keep dissent visible.** Consensus is not truth, and a 3–1 split is not evidence. Minority positions go into Remaining Disagreements, not into a footnote.


## Workflow Routing

Route to the appropriate workflow based on the request.

| Trigger | Workflow |
|---------|----------|
| Full structured debate (3 rounds, visible transcript) | `Workflows/Debate.md` |
| Quick consensus check (1 round, fast) | `Workflows/Quick.md` |


## Quick Reference

| Workflow | Purpose | Rounds | Output |
|----------|---------|--------|--------|
| **DEBATE** | Full structured discussion | 3 | Complete transcript + synthesis |
| **QUICK** | Fast perspective check | 1 | Initial positions only |

## Context Files

| File | Content |
|------|---------|
| `CouncilMembers.md` | How to write council member briefs inline |
| `RoundStructure.md` | Three-round debate structure and timing |
| `OutputFormat.md` | Transcript format templates |

## Core Philosophy

**Origin:** Best decisions emerge from diverse perspectives challenging each other. Not just collecting opinions - genuine intellectual friction where domain-specific experts respond to each other's actual points.

**Agents:** Every council member is a custom brief you write for the topic, launched with the `council-member` agent. This gives each member a distinct role, stance, and domain expertise. Generic agents produce generic debate; topic-specific briefs produce sharp, informed debate.

**Speed:** Parallel execution within rounds, sequential between rounds. A 3-round debate of 4 agents = 12 agent calls but only 3 sequential waits. Complete in 40-90 seconds.

## Examples

```
"Council: Should we use WebSockets or SSE?"
-> Write 4 member briefs (real-time architect, frontend-DX, ops skeptic, researcher)
-> DEBATE workflow -> 3-round transcript

"Quick council check: Is this API design reasonable?"
-> Write 4 member briefs with API-relevant roles
-> QUICK workflow -> Fast perspectives

"Council: Is AI overhyped?"
-> Write briefs: AI builder, security skeptic, pragmatic engineer, evidence analyst
-> DEBATE workflow -> 3-round transcript
```

## Integration

**Works well with:**
- **/think pipeline** — the council debates options built from the first-principles, systems-thinking and root-cause-analysis reports.
- **scientific-critical-thinking** — check the recommended path for unsupported claims after the debate.

## Best Practices

1. Use QUICK for sanity checks, DEBATE for important decisions
2. Write each member's brief around the specific topic, not a generic role
3. Give each member a distinct stance — four identical agents produce no friction

---

**Last Updated:** 2026-03-18

## Gotchas

- **Council members are inline briefs launched with the `council-member` agent — there is no composition tool.** Write four different topic-specific briefs; don't launch bare built-in types with no persona.
- **Debates need genuine disagreement to be valuable.** If all agents agree, the topic may not warrant Council.
- **More agents ≠ better debate.** 4-6 well-briefed agents outperform 12 generic ones.

