---
name: think
description: Full thinking pipeline for a hard problem or decision. Clarifies the ask with the user (brainstorming), analyzes it in parallel through first principles, systems thinking and, for failures, root cause analysis, debates the options in a council, then checks the recommendation for evidence and bias. Run manually as /think <problem>, or /think quick <problem> for a one-round council.
argument-hint: "[quick] <problem or decision>"
disable-model-invocation: true
---

# Think

Turns a messy problem or decision into a checked recommendation.

You, the main session, are the conductor. You talk to the user, launch the subagents, and write the final answer. Subagents can't talk to the user and don't see this conversation, so every prompt you give them must carry the brief and whatever else they need, in full.

Write everything user-facing in the user's language.

**Request:** $ARGUMENTS

If the request is empty, ask what to think through. If it starts with `quick`, run quick mode: the council runs one round instead of three. Everything else stays the same.

| Stage | Who | Agent launches (full / quick) |
|---|---|---|
| 1. Clarify | you and the user | 0 |
| 2. Analyze | analysts, in parallel | 2–3 |
| 3. Options | you | 0 |
| 4. Council | you and four members | 12 / 4 |
| 5. Check | critic | 1 |
| 6. Answer | you | 0 |

## Stage 1: Clarify

Load the `brainstorming` skill and follow its conversation rules: the first message, one question per message, playback, guesses marked as guesses. Its "Inside /think" section says how it ends.

For an analysis, the ground to cover is:
- the problem or decision, in one sentence
- what exists now, what has been tried, and what happened
- what a good outcome looks like, and what would make it a failure even if it "worked"
- real limits: time, money, people, technology
- who decides, and by when
- what the user already believes the answer is

If the request already covers this, skip the questions and go straight to the brief. In quick mode, ask no more than three questions.

If the problem turns out to be a quick, clear question, say so and offer to answer it directly instead of running the pipeline.

### The brief

```
**Brief**
- **Question:** the problem to solve or decision to make, in one sentence
- **Stakes:** why it matters now
- **Current state:** what exists, what was tried, what happened
- **Success:** what a good outcome looks like; what would count as failure
- **Constraints:** each tagged (stated) or (my guess)
- **Known facts:** each with its source
- **Unknowns:** what we don't know that matters
- **Current bet:** the answer the user leans toward, if any. Analysts test it; they don't assume it.
- **Lenses:** first principles, systems; root cause yes/no, and why
```

Root cause analysis runs only when there is a failure, an incident, or a problem that keeps coming back. A forward-looking choice (a design, a strategy, a purchase) gets two lenses.

**Gate:** show the brief and ask whether it's right. Nothing gets analyzed until the user approves it. If the user opted out of the questions, show the brief and go on without waiting.

## Stage 2: Analyze

Launch the analysts in one message so they run in parallel:
- `first-principles-analyst`
- `systems-analyst`
- `root-cause-analyst`, only if the brief says yes

Each prompt contains the approved brief verbatim, any file paths or links the analysis needs, and the language to write in.

When the reports come back, show a digest: 2–4 bullets per lens with its key insight and its top assumption to test. Keep the full reports; offer them, don't paste them.

If two or more lenses say the question itself is wrong (the real problem is something else), stop here. Show why and ask whether to reframe before going on.

## Stage 3: Options

From the reports, build 2–4 options that differ in approach, not in degree. Include the current path or "do nothing" as a baseline when it is a real choice. For each option, give what it is in one line, which lens it came from, and the assumption it rests on.

Before comparing anything, set 3–5 criteria from the brief's success and constraints. List hard constraints separately as vetoes; a veto is never averaged into a score.

Show the options and criteria, then go straight on to the council.

## Stage 4: Council

Load the `council` skill. Run its Debate workflow, or its Quick workflow in quick mode.

- **Topic:** which option best meets the criteria, given the constraints.
- **Context for every member:** the brief, the analysis digest, the options, and the criteria.
- **Members:** four briefs written for this topic. One member's brief must be to attack whichever option the analyses favor.

Inside /think, don't show the full transcript. Show each member's final position in one line, then the Council Synthesis. Offer the full transcript.

## Stage 5: Check

Draft the recommendation without showing it. Launch `evidence-critic` with the brief, the analysis digest, the options and criteria, the council synthesis, and the draft.

Fix what the critic finds. If a critical finding undermines the recommendation, say so plainly in the answer and lower the confidence. Don't bury it.

## Stage 6: Answer

```
**Recommendation**
1–3 sentences.

**Why**
3–5 bullets, each tied to a lens or a council argument.

**Still disputed**
Open disagreements, and who holds which side.

**Test these assumptions first**
| Assumption | How to check | What changes if it's false |

**Confidence:** high / medium / low, and why in one line.

**First step:** one concrete action.
```

Then offer, in one line, the full analyst reports, the council transcript, or saving the whole run as a file.

## Rules

- Mark claims as fact (with source), assumption, or idea all the way through. Never pass the user's current bet off as a fact.
- Launch parallel work in one message: all analysts together, all members of a council round together.
- Between stages, tell the user in one line what's running.
- A full run is about 16 agent launches, a quick one about 8. If the user wants something lighter still, skip the council and go from the options straight to the check.
