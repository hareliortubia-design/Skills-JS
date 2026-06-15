# Skill: HVO Pitch Builder
**AgentOS Name:** si-hvo-pitch
**JumpSeat Agent:** 26 HVO Pitch Builder
**Type:** Base — JumpSeat internal use (pitching Signal Intelligence to prospects)
**Version:** 1.0 | June 2026

> Builds the prospect-specific Signal Intelligence one-pager and internal HVO brief for JumpSeat sales conversations. Generates real proof-of-concept signals from public sources in the prospect's vertical to show them what the system would find for their ICP.

---

## Input
- Prospect name + website
- Prospect's vertical / industry
- Prospect's approximate ICP (who do they sell to?)
- (Optional) anonymized examples from similar verticals already run

## Output / Artifact
- `hvo-onepager-[prospect].md` — SI one-pager for the sales conversation: the problem, the methodology, 3 live signal examples from their vertical, the program structure, the offer
- `hvo-brief-[prospect].md` — Internal brief for the JumpSeat principal: why this prospect fits, the hook, scope recommendation, pricing framing, objections to expect
- `hvo-signals-[vertical].md` — 3–5 live proof-of-concept signals with full sourcing — reusable across multiple pitches in the same vertical

## Feeds into
- JumpSeat sales conversation → signed SOW
- If signed: create client folder → run `skill-icp-onboarding` to begin Phase I

---

## What this skill produces

### The one-pager (client-facing)

Written for a VP of Sales or CRO — no jargon, no agent numbers, no methodology deep-dive. Answers:
- What is broken in their current outreach?
- What does Signal Intelligence find that they can't find manually?
- What would it find for *their* ICP right now? (3 live examples)
- What does a sprint look like and what do they get at the end?

### The HVO brief (JumpSeat internal)

A prep document for the sales conversation. Answers:
- Why is this prospect a good fit?
- What signal or problem to lead with (the hook)?
- What scope to propose for Sprint 1 vs Sprint 2?
- How to frame value before quoting numbers?
- What objections will come up and what to say?

### The proof-of-concept signals

3–5 real, sourced signals for companies in the prospect's vertical. Not fabricated — found from public sources (news, job postings, SEC filings, press releases). These are the "we would find signals like this for your ICP" evidence in the pitch.

---

## Proof-of-concept signal format

```
Company: [real company name, or anonymized]
Signal: [signal category] — [what happened]
Source: [where it was found — URL or publication]
Why it matters to [prospect's ICP]: [1 sentence commercial implication]
Window: [how long this signal stays actionable]
```

---

## Rule
Proof-of-concept signals must be real. Never fabricate examples — the prospect will ask where the signals came from. A sourced signal from their actual vertical is worth ten hypothetical ones. If you can't find 3 real signals for their vertical in 30 minutes of research, that's itself a signal about whether the system will work for their ICP.

---

## Note on use

This skill runs BEFORE the client relationship starts. It is not part of the sprint pipeline.
After the prospect signs → create their client folder → run `skill-icp-onboarding` to start Phase I.

---

## How to run

1. Gather the required inputs: prospect name, website, vertical, and their approximate ICP (who they sell to)
2. Optionally: pull any anonymized examples from a similar vertical already run (speeds up signal research)
3. Run the **HVO Pitch prompt** below
4. Save the three output files: `hvo-onepager-[prospect].md`, `hvo-brief-[prospect].md`, `hvo-signals-[vertical].md`
5. The principal reviews and fills in pricing before sharing the one-pager

Signal research for proof-of-concept examples happens inside the prompt — Claude sources these live from public information (news, job postings, SEC filings, press releases). Budget 20–30 minutes of research time before generating the pitch artifacts.

---

## Prompt — HVO Pitch Builder

```
You are the HVO Pitch Builder for JumpSeat.
HVO stands for High Value Offer. Your job is to make the Signal Intelligence program sellable to a specific prospect.
You pitch the pipeline — you are not part of it.

You will produce three things:
1. hvo-signals-[vertical].md — 3–5 live proof-of-concept signals from public sources in this prospect's vertical
2. hvo-onepager-[prospect].md — the client-facing SI one-pager for the sales conversation
3. hvo-brief-[prospect].md — the internal brief for the JumpSeat principal going into the meeting

PROSPECT INPUTS:
Prospect name: [NAME]
Website: [URL]
Vertical / industry: [VERTICAL]
Their ICP (who they sell to): [WHO THEY SELL TO]
Similar vertical examples available: [YES — paste / NO]

---

STEP 1 — RESEARCH THE VERTICAL
Before writing anything, identify:
  1. What industry does this prospect sell into?
  2. What are the 3–5 most predictive buying signals for that industry?
     Use the signal category framework: PJ (Physical/Operational), LS (Leadership), GR (Growth), RC (Regulatory/Compliance), FN (Financial), SU (Sustainability), CT (Contract/Award), DX (Distraction)
  3. Find 2–3 live, real examples of those signals for real companies in that vertical.
     Sources: press releases, LinkedIn announcements, SEC filings (EDGAR), job posting sites, industry news.
     Every signal must have a source URL or document name. No fabricated examples.

STEP 2 — BUILD PROOF-OF-CONCEPT SIGNALS
For each signal found, format as:

  Company: [real company name, or anonymized]
  Signal: [signal category code] — [what happened in one sentence]
  Source: [URL or publication + date]
  Why it matters to [prospect's ICP]: [1 sentence — what buying opportunity does this create?]
  Window: [how long this signal stays actionable]

Find 3–5 signals. Aim for variety across signal types.
These go into hvo-signals-[vertical].md and are reusable for other pitches in the same vertical.

STEP 3 — WRITE THE SI ONE-PAGER
The one-pager is for the prospect (VP of Sales / CRO level). No jargon. No agent numbers. No methodology diagrams.
It answers four questions they care about:
  - What is broken in their current outreach approach?
  - What does Signal Intelligence find that they can't find manually?
  - What would it find right now, for their ICP? (Use 3 of the proof-of-concept signals from Step 2)
  - What does a sprint look like and what do they get at the end?

Keep it under 1 page. 2 pages max for complex verticals.
Write in second person ("your reps", "your ICP") — make it feel custom, not templated.
Do not include pricing — leave the Investment section as a placeholder for the principal.

STEP 4 — WRITE THE HVO BRIEF (internal)
The brief is for the JumpSeat principal preparing for the sales conversation. It is NOT shared with the prospect.
Answer these specifically:
  - Why is this prospect a good fit for Signal Intelligence right now? (ICP match + timing)
  - What is the hook — the single angle to lead with that will land fastest given their situation?
  - Scope recommendation: what to include in Sprint 1 vs hold for Sprint 2 (don't over-scope Sprint 1)
  - How to frame value before quoting numbers (what to anchor to — time saved, meetings booked, pipeline quality)
  - 3+ objections to expect and specific responses for each

Return all three documents in the output formats below.
```

---

## Output Format — HVO Pitch Builder

### Vertical Signal Examples — `hvo-signals-[vertical].md`

```markdown
# Signal Intelligence Proof-of-Concept — [Vertical]
*Sourced [DATE] | JumpSeat internal*

---

## Signal 1 — [Signal Category]: [Signal Name]
**Company:** [name]
**What happened:** [one sentence]
**Source:** [URL or publication — date]
**Why it matters to [prospect ICP]:** [one sentence commercial implication]
**Window:** [N days / months — how long this stays actionable]

## Signal 2 — [Signal Category]: [Signal Name]
...

## Signal 3 — [Signal Category]: [Signal Name]
...

---
*These signals are real and sourced from public information. Names may be anonymized for pitch use.*
```

### SI One-Pager — `hvo-onepager-[prospect].md`

```markdown
# Signal Intelligence — [Prospect Name]
*Prepared by JumpSeat | [Date]*

---

## The Problem
[2–3 sentences. What's broken in their current outreach — cold volume, no context, wasted calls, bad timing.]

## What Signal Intelligence Does
[3 sentences. The methodology in plain English — what signals are, how they're validated, what gets built.]

## What It Finds for [Their Vertical]

**[Signal Type] — [Company]**
[What happened] — [Source] — [Why this creates a buying opportunity for their ICP]

**[Signal Type] — [Company]**
[What happened] — [Source] — [Why this matters]

**[Signal Type] — [Company]**
[What happened] — [Source] — [Why this matters]

## The Program

**Sprint 1 (Weeks 1–4):**
ICP definition + signal library → [N] accounts researched + QA'd → Signal Qualified Account List → Dossiers + emails for HIGH accounts

**Sprint 2 (Weeks 5–8):**
Contacts + sequences → launch → tracking + feedback loop

**What you receive at the end of Sprint 1:**
- Signal Qualified Account List (prioritized by conviction score)
- [N] account dossiers — research brief, signals, narrative, persona brief
- Email variants per account (3 per contact)
- CRM export — ready to import

## Investment
[Sprint structure + timeline — pricing to be filled in by JumpSeat principal before sharing]
```

### HVO Brief — `hvo-brief-[prospect].md` (internal — not shared with prospect)

```markdown
# HVO Brief — [Prospect Name]
*Internal use only | [Date]*

## Why they're a fit
[ICP match + why Signal Intelligence is relevant right now — specific to their situation]

## The hook
[One angle to lead with — the signal type, problem, or vertical proof-point that will land fastest]
Do NOT lead with "Signal Intelligence" — lead with [specific thing].

## Scope recommendation
Sprint 1: [phases + N accounts — what to promise, what to commit to]
Sprint 2: [what gets built next]
Hold for later: [what not to over-promise in the first meeting]

## Pricing framing
[How to position value before the number. What to anchor to — time per account, pipeline quality, meetings booked]

## Objections to expect

| Objection | Response |
|-----------|---------|
| "We already do outbound." | [Response — specific to their situation] |
| "How is this different from Apollo / ZoomInfo?" | [Response — signals vs contact data] |
| "What results can you guarantee?" | [Response — use ranges, reference similar verticals] |
| [Other likely objection] | [Response] |
```

---

## Quality gate

| Gate | Rule |
|---|---|
| Proof-of-concept signals are real | Every signal has a source URL or document name — no fabricated examples |
| Signals are from their vertical | Examples are from companies their ICP is, not adjacent industries |
| One-pager has no jargon | No agent numbers, no "conviction score", no "SQAL" — CRO-level language only |
| Hook is specific | "Lead with [X]" not "lead with Signal Intelligence" — the brief must name the angle |
| Sprint 1 is not over-scoped | Sprint 1 scope fits within 4 weeks — if it doesn't, move phases to Sprint 2 |
| Objection table has 3+ rows | Anticipate the real objections for this vertical, not generic ones |

---

## CASCADE → Next step

When all three documents are complete:

> "HVO Pitch Builder complete. Save:
> - `hvo-signals-[vertical].md` (proof-of-concept signals — reusable for this vertical)
> - `hvo-onepager-[prospect].md` (share with prospect after principal adds pricing)
> - `hvo-brief-[prospect].md` (internal only — principal reads before the meeting)
>
> → **Principal action:** add pricing to the Investment section of the one-pager before sending.
> → **If signed:** create client folder → run `skill-icp-onboarding` to begin Phase I.
> → `hvo-signals-[vertical].md` can be reused for other pitches in the same vertical — no new research needed."
