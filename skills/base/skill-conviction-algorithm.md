# Skill: Conviction Algorithm
**JumpSeat Agent:** 07 Signal Conviction → 08 Prioritization & Stacking  
**Type:** Base — works for any client  
**Version:** 1.0 | June 2026

> Takes a completed signal matrix and calculates a weighted conviction score, assigns a priority level, and explains exactly why the account lands where it does — step by step.

---

## Input
- `client-profile.yaml` — for signal weights, conviction thresholds, and combo bonuses
- Signal matrix output from `skill-signal-research` — one account's classified signals (C/I/N/U)

## Output / Artifact
- `conviction-output.json` — full scoring breakdown with raw score, weighted score, level, urgency, lead signal, stack story, and next action. Every number is explained.

## Feeds into
- `skill-dossier-builder` — HIGH and CRITICAL accounts only
- `skill-crm-export` — all accounts

---

## Why this is a separate skill

Dan's rule: *"If you can't fully explain it, you probably shouldn't be doing it."*

The conviction score is the most important number in the pipeline. Every sales rep who reads a dossier and every manager who looks at the Salesforce queue deserves to understand exactly why an account is HIGH vs MEDIUM. This skill makes the algorithm visible and auditable — not a black box.

---

## How to run

1. Read `client-profile.yaml` — extract `signals[]` (weights), `signal_combos[]`, `conviction.levels`, `signal_timing[]`
2. Receive the signal matrix (output from `skill-signal-research`)
3. Run the Conviction Algorithm prompt below
4. Return full scoring breakdown in JSON

---

## Prompt

```
You are a conviction scoring analyst. Your job is to calculate and fully explain
the conviction score for one account, using the signal matrix and client profile below.

CLIENT PROFILE (weights + thresholds):
[PASTE client-profile.yaml — sections: signals (weights only), signal_combos, conviction, signal_timing]

SIGNAL MATRIX FOR THIS ACCOUNT:
[PASTE signal-research output — the signals{} object with C/I/N/U classifications and findings]

---

Calculate the conviction score in this exact order. Show every step.

STEP 1 — RAW CONVICTION SCORE
Count only Confirmed (C) signals. List each one.
Raw score = total count of C signals.

STEP 2 — WEIGHTED CONVICTION SCORE
For each Confirmed signal: multiply 1 point × its weight from client-profile.yaml.
List: [signal code] × [weight] = [points]
Sum all weighted points.

STEP 3 — COMBO BONUSES
Check if any signal combos from client-profile.yaml are fully confirmed (all signals in the combo are C).
For each triggered combo: add the bonus points and explain the story.
List: [combo signals] → [bonus points] — "[story]"

STEP 4 — DISTRACTION CHECK
Check each distraction signal (DX) in the matrix.
For each Confirmed DX signal: read its `impact` field from client-profile.yaml.

If impact = "STOP" → PENDING override. Do not outreach. Define reactivation trigger.
If impact = "FLAG" → Do NOT override conviction level. Note the DX in the output.
  Proceed with outreach only if at least one Lead-stack signal (stack: "Lead" in client-profile.yaml) is Confirmed.
  If no Lead-stack signal is Confirmed → still PENDING, but reason is low signal count, not the DX.

Explain which DX signals were found, their impact level, and what action follows.

STEP 5 — CONVICTION LEVEL
Apply the thresholds from client-profile.yaml.
Assign: HIGH / MEDIUM / PENDING / LOW
Explain in one sentence why this account lands at this level.

STEP 6 — URGENCY
Look at the half-life table from client-profile.yaml (signal_timing[]).
Find the highest-urgency confirmed signal.
Assign: CRITICAL / HIGH / MEDIUM / LOW
State: which signal drives urgency, when it expires, and what happens if we wait.

STEP 7 — LEAD SIGNAL
Identify the single strongest confirmed signal to open the conversation with.
Rules: must be a Lead-stack signal (not Context). Must be Confirmed (C), not Inferred.
If no Lead signal is Confirmed: flag this — outreach should wait.

STEP 8 — STACK STORY
Which confirmed signals cluster together and what story do they tell?
Write 1–2 sentences the sales rep can use internally to frame why this account matters now.

STEP 9 — NEXT ACTION
Based on conviction level + urgency + dossier status:
HIGH/CRITICAL + no dossier → "Build dossier"
HIGH/CRITICAL + dossier exists → "Outreach ready"
MEDIUM → "Research signal gaps"
PENDING → "Monitor — [state the reactivation trigger]"
LOW → "Do not prioritize"

STEP 10 — STALE FLAGS
Cross-check each Confirmed signal against signal_timing[] reverify_days.
If the signal's confirmed date + reverify_days < today → flag as STALE.
List: [signal] — confirmed [date] — reverify by [date] — ACTION: re-verify before outreach

Return the full output in the JSON format below.
```

---

## Output Format

```json
{
  "company": "",
  "scored_date": "YYYY-MM-DD",

  "scoring_breakdown": {
    "step_1_raw_score": {
      "confirmed_signals": ["YP-01", "LS-01"],
      "raw_count": 2
    },
    "step_2_weighted_score": {
      "line_items": [
        {"signal": "YP-01", "weight": 1.0, "points": 1.0},
        {"signal": "LS-01", "weight": 2.0, "points": 2.0}
      ],
      "weighted_subtotal": 3.0
    },
    "step_3_combo_bonuses": [
      {
        "combo": ["LS-01", "GR-02"],
        "triggered": false,
        "bonus": 0,
        "story": "GR-02 not confirmed — combo not triggered"
      }
    ],
    "step_4_distraction_check": {
      "distraction_signals_found": [],
      "pending_override": false,
      "reason": ""
    },
    "step_5_conviction_level": {
      "final_weighted_score": 3.0,
      "level": "MEDIUM",
      "reasoning": ""
    },
    "step_6_urgency": {
      "level": "HIGH",
      "driving_signal": "LS-01",
      "window": "12–18 months post-hire",
      "expires": "YYYY-MM-DD",
      "wait_cost": ""
    },
    "step_7_lead_signal": {
      "signal": "LS-01",
      "finding": "",
      "why_lead": ""
    },
    "step_8_stack_story": "",
    "step_9_next_action": "",
    "step_10_stale_flags": []
  },

  "summary": {
    "raw_conviction_score": 0,
    "weighted_conviction_score": 0.0,
    "conviction_level": "HIGH / MEDIUM / PENDING / LOW",
    "urgency": "CRITICAL / HIGH / MEDIUM / LOW",
    "lead_signal": "",
    "stack_story": "",
    "next_action": "",
    "stale_flags": [],
    "pending_override": false,
    "reactivation_trigger": ""
  }
}
```

---

## Quality gate

| Gate | Rule |
|------|------|
| Every point is explained | No score without showing the calculation step by step |
| Inferred ≠ Confirmed | I signals never add to conviction score |
| Distraction override | If DX impact = STOP → PENDING. If DX impact = FLAG → note only, don't override conviction level |
| Lead signal must be Lead-stack | YP structural signals alone cannot be the lead signal |
| Stale signals flagged | Any signal past its reverify window must be flagged before outreach |
| Score ≠ urgency | HIGH conviction + LOW urgency is a valid state — they measure different things |

---

## CASCADE → Next step

When the conviction output is complete, tell the user:

> **If conviction level = HIGH or CRITICAL:**
> "Score ready. Save as `conviction-[company-name].json`.
>
> → **Next: run skill-dossier-builder**
> Bring: conviction-output.json + signal-research.json.
> It will build the 4–5 page intelligence dossier the sales rep reads before their first call."
>
> **If conviction level = MEDIUM:**
> "Score ready — MEDIUM conviction. Not enough confirmed signals to build a dossier yet.
>
> → **Next: research signal gaps**
> Look at your Unknown (U) signals. Re-run skill-deep-research targeting those specific signals.
> When 3+ Lead signals are Confirmed, re-run skill-conviction-algorithm."
>
> **If conviction level = PENDING:**
> "Account is PENDING. Strong ICP fit but no active buying window confirmed.
>
> → **Next: add to monitoring queue**
> Set a reactivation trigger (stated in conviction output). Check back in [reverify_days].
> Run skill-crm-export to log this account."
>
> **If conviction level = LOW:**
> "LOW conviction. Not worth pursuing.
>
> → **Next: run skill-crm-export** to log as Low, then move on."
