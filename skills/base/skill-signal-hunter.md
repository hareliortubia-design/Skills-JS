# Skill: Signal Hunter
**JumpSeat Agent:** 05 Signal Hunter  
**Type:** Base — works for any client  
**Version:** 1.0 | June 2026

> Takes a client's ICP and generates a signal library of 30–50 candidate signals, categorized and weighted, ready for a workshop with the client to finalize.

---

## Input
- `client-profile.yaml` — required. Run `icp-onboarding` first if you don't have it.

## Output / Artifact
- `signal-library.md` — 30–50 candidate signals organized by category, each with detection method, source, weight, and classification rules. Ready for client workshop.

## Feeds into
- Client workshop (human review) → finalize signal list
- `skill-signal-research` — uses the finalized signal library to classify accounts

---

## How to run

1. Read `client-profile.yaml`
2. Extract: `client.description`, `client.service`, `client.buyers`, `icp.criteria`, `icp.summary`
3. Run the Signal Hunter prompt below
4. Output the signal library organized by category
5. Present to client for review — they approve, remove, or refine signals
6. Save finalized version as `signal-library.md` in the client's folder

---

## Prompt

```
You are a B2B signal intelligence strategist. Your job is to build a signal library
for a client — a list of observable facts that indicate a prospect company is in an
active buying window for this client's service.

Read the client profile below and generate 30–50 candidate signals.

CLIENT PROFILE:
[PASTE client-profile.yaml — sections: client, icp]

---

A signal is a NEUTRAL FACT — not good or bad on its own. What matters is the
implication: does this fact suggest the prospect is more likely to buy right now?

For each signal, define:
- What it is (one line)
- What it implies (why it suggests a buying window)
- How to detect it (specific source — not "research it", but exactly where to look)
- Whether it's OBVIOUS (any competitor with the same tools finds it) or NON-OBVIOUS
  (requires deep research, cross-referencing, or knowing where to look)
- Suggested weight: 2.0× (highest value), 1.5× (active pain), 1.25× (pressure event), 1.0× (structural qualifier)
- Stack tier: Lead (strong enough to open the conversation), Support (reinforces once door is open),
  Context (background qualifier — necessary but not urgency-driving)

Organize signals into these categories:
1. STRUCTURAL QUALIFIERS — Does this company fundamentally need this service?
   (size, operations model, geography, industry — these confirm fit, not urgency)
2. CHANGE AGENT — Is there a new decision-maker evaluating vendor relationships?
   (new hire, leadership change, org restructure)
3. ACTIVE PAIN — Are they struggling with something this service solves right now?
   (failed hiring, operational incidents, complaints, backlog)
4. TRIGGER EVENT — Did something happen that opens a buying window?
   (M&A, new facility, funding, regulatory mandate, cost program)
5. DISTRACTION SIGNALS — Does something CLOSE the window?
   (legal crisis, leadership distraction, hiring freeze, budget cut)
   For each distraction signal, define its impact level:
   - impact: "STOP" → confirmed = hard PENDING, do not outreach (affects the whole org: legal crisis, mass layoff, budget freeze)
   - impact: "FLAG" → confirmed = note in dossier as barrier, but do NOT auto-PENDING (only affects one person, not the buyer: e.g. CEO distraction when buyer is an operational VP)

Output the full signal library. For each regular signal:

[SIGNAL CODE] · [Signal Name]  [OBVIOUS or NON-OBVIOUS]  [STACK: Lead / Support / Context]
Weight: [multiplier]
What it means: [implication for buying window]
How to detect: [specific detection method]
Sources: [list of specific sources]
Classify as:
  C (Confirmed) if: [condition]
  I (Inferred) if: [condition]
  N (Not Found) if: [condition]

For each distraction signal, add the impact field:

[DX CODE] · [Signal Name]
Impact: STOP or FLAG  ← define this based on whether it blocks the whole org (STOP) or only one person (FLAG)
Impact detail: [one sentence — who is affected and why it does or doesn't block outreach]
What it means: [implication]
How to detect: [specific detection method]
Classify as:
  C (Confirmed) if: [condition]
  N (Not Found) if: [condition]

After generating all signals:
1. Identify the 3–5 HIGHEST VALUE signals (the ones that most strongly predict active buying)
2. Identify 3–5 signal COMBOS — pairs that together tell a stronger story than each alone
3. Flag any signals that require paid data sources
```

---

## Output Format

Save the output as `signal-library.md` with this structure:

```markdown
# Signal Library — [Client Name]
Generated: [date] | Signals: [count] | Status: DRAFT — pending client workshop

---

## Highest Value Signals (top 5)
1. [code] [name] — [why it's highest value]
...

---

## CATEGORY 1: Structural Qualifiers
[signals]

## CATEGORY 2: Change Agent
[signals]

## CATEGORY 3: Active Pain
[signals]

## CATEGORY 4: Trigger Events
[signals]

## CATEGORY 5: Distraction Signals
[signals]

---

## Signal Combos
- [CODE-A] + [CODE-B] = [story they tell together] → bonus: [points]
...

---

## Signals requiring paid data sources
[list any that require Crunchbase, PitchBook, SimilarWeb, etc.]

---

## Workshop notes
Status: DRAFT
Next step: Review with client. Remove, refine, or add signals. 
Target: 10–20 finalized signals.
Rule: If you can't fully explain a signal, remove it.
```

---

## Quality gate

| Gate | Rule |
|------|------|
| Signal is a fact | Each signal must be a detectable, verifiable fact — not an opinion or inference |
| Source is specific | Each signal must have a named source (Indeed, SEC EDGAR, LinkedIn) — not "research online" |
| Detection is actionable | "Check Indeed for [title] postings" — not "look for hiring activity" |
| Distraction signals included | At least 3 distraction signals must be in the library |
| No jargon signals | If the signal can't be explained in one sentence to a non-expert, rewrite it |

---

## Workshop instructions (after generating)

Present the draft signal library to the client and ask:
1. "Which of these are you already seeing in your best current customers?"
2. "Which signals would you NOT want to pursue — too competitive, or wrong fit?"
3. "Are there signals we missed that you know from experience predict a win?"

Target: reduce 30–50 candidates to 10–20 finalized signals with agreed weights.
Validate the final list against 5–10 closed-won accounts before using at scale.
