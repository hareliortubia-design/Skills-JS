# Skill: ICP Fit Scoring
**Mode:** 0 | **JumpSeat Agent:** 03 Account Fit  
**Type:** Base (reutilizable) | **Version:** 1.0 — June 2026

---

**What it does:** Gates the account list before signal research. Scores each company against the client's ICP criteria and returns a Tier (1/2/Not ICP) + Fit Score. Prevents wasted research cycles on companies that will never buy.

**Input:** Research Brief (from Mode R) + client ICP parameters  
**Output:** Fit Tier (Tier 1 / Tier 2 / Not ICP) + Fit Score 1–10 + go/no-go decision  
**Feeds into:** Signal Research (Mode A) — Tier 1 and Tier 2 only

**Rule:** Do not run signal research on Not ICP accounts. The ICP gate exists to protect research capacity.

---

## How to parameterize this skill for a new client

Before running Mode 0 for a new client, define their ICP criteria in the following table.
Each criterion maps to a point value. The scoring logic stays the same — only the criteria change.

### ICP Parameter Template

```
Client: [CLIENT NAME]
What they sell: [one sentence]
Who buys: [job titles + company types]
Minimum deal threshold: [revenue or contract size]

ICP CRITERIA:
┌──────────────────────────────────────────┬────────┬──────────────────────────────────┐
│ Criterion                                │ Points │ How to evaluate                  │
├──────────────────────────────────────────┼────────┼──────────────────────────────────┤
│ [Primary vertical match]                 │  +2    │ Industry classification           │
│ [Minimum revenue threshold]              │  +2    │ Public revenue data or estimate   │
│ [Operational scale qualifier]            │  +2    │ Company website, SEC filings      │
│ [Business model match]                   │  +2    │ Business model inference          │
│ [Geography / region qualifier]           │  +2    │ HQ location                       │
└──────────────────────────────────────────┴────────┴──────────────────────────────────┘

TIER THRESHOLDS:
8–10 = Tier 1 → Run full signal research immediately
5–7  = Tier 2 → Run signal research, lower priority queue
1–4  = Not ICP → Skip — do not invest in signal research
```

### Lazer Logistics ICP (reference implementation)

```
Client: Lazer Logistics
What they sell: Yard management and spotting services (moving trailers inside distribution yards)
Who buys: VP Supply Chain, VP Logistics, VP DC Operations at large manufacturers/retailers
Minimum deal threshold: Multi-year contract, $500M+ revenue company

ICP CRITERIA:
┌──────────────────────────────────────────────────────────┬────────┬──────────────────────────────────┐
│ Criterion                                                │ Points │ How to evaluate                  │
├──────────────────────────────────────────────────────────┼────────┼──────────────────────────────────┤
│ Distribution-heavy vertical (retail, food & bev, 3PL,   │  +2    │ Industry classification           │
│ industrial)                                              │        │                                  │
│ Revenue $500M+                                           │  +2    │ Public revenue data or estimate   │
│ Multi-site network (3+ DCs)                              │  +2    │ Company website, SEC filings      │
│ High-volume trailer ops (drop-and-hook model likely)     │  +2    │ Business model inference          │
│ US-based primary operations                              │  +2    │ HQ location                       │
└──────────────────────────────────────────────────────────┴────────┴──────────────────────────────────┘
```

---

## Prompt

```
You are an ICP analyst for [CLIENT NAME].

[CLIENT ONE-SENTENCE DESCRIPTION]

Evaluate this company for ICP fit BEFORE investing in signal research.

Company: [COMPANY_NAME]
Research Brief: [PASTE MODE R OUTPUT]

Score 1–10 using the ICP criteria below:
[PASTE CLIENT ICP CRITERIA TABLE]

Return JSON:
{
  "company": "",
  "fit_score": 0,
  "fit_tier": "Tier 1 / Tier 2 / Not ICP",
  "fit_reasoning": "one sentence explaining the score",
  "go_no_go": "GO / NO-GO",
  "skip_reason": "if Not ICP, why — what disqualifies them"
}
```

---

## Output Format

```json
{
  "company": "Burlington Stores",
  "fit_score": 9,
  "fit_tier": "Tier 1",
  "fit_reasoning": "Large retail DC network with 1,000+ stores, drop-and-hook model, US-based, $9B+ revenue — strong ICP match across all criteria.",
  "go_no_go": "GO",
  "skip_reason": null
}
```

---

## Quality gate

- Never score above 8 without evidence for at least 3 criteria
- "Probably has DCs" = inference, not confirmed → cap at Tier 2
- If research brief is LOW confidence, note it in fit_reasoning
- A high Fit Score is not a signal — it's a precondition. Fit ≠ conviction.

---

## CASCADE → Next step

When the fit score is complete, tell the user:

> **If Tier 1 or Tier 2 (GO):**
> "ICP fit confirmed. Save as `icp-fit-[company-name].json`.
>
> → **Next: run skill-signal-research**
> Bring: research-brief.json + signal-library.md + client-profile.yaml.
> It will classify every signal as Confirmed, Inferred, Not Found, or Unknown."
>
> **If Not ICP (NO-GO):**
> "This account doesn't meet the ICP. Stop here — do not invest in signal research.
>
> → **Next: run skill-crm-export** to log this account as Not ICP.
> No dossier, no outreach."
