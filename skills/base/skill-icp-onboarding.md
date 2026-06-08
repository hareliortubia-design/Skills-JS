# Skill: ICP Onboarding
**JumpSeat Agents:** 01 Memory & Learning Layer → 02 Foundation → 04 Account Fit Review  
**Type:** Base (reutilizable — works for any client) | **Version:** 2.0 — June 2026

---

**What it does:** Runs a structured intake interview with a new client, analyzes their best customers, and produces a `client-profile.yaml` + a calibrated ICP scoring prompt (Mode 0) ready to run on any account list. This is the first step of the JumpSeat pipeline — everything downstream depends on it.

**Input:** Client website URL + list of closed-won accounts (10–30 minimum)  
**Output:**
- `client-profile.yaml` — structured ICP definition with hard filters, soft preferences, and fit tiers
- `icp-prompt.md` — calibrated Mode 0 prompt ready to score any account list
- Signal Hunter seed — ICP characteristics that feed the signal workshop (next step)

**Feeds into:** Signal Hunter workshop → Signal Research (Mode A)

**Rule:** Never start signal research without a validated ICP. A fuzzy ICP produces a useless signal list.

---

## When to run this skill

- New client onboarding (always — before anything else)
- ICP refresh (annually, or when win rate drops significantly)
- New product line or segment expansion

**Trigger phrases:** "new client", "onboard [client name]", "define ICP for", "build ICP", "set up [client name]"

---

## Phase 1 — Website Analysis

Before asking any questions, analyze the client's website.

```
Fetch and analyze:
- r.jina.ai/[client-website]/
- r.jina.ai/[client-website]/about
- r.jina.ai/[client-website]/customers (or /case-studies, /clients)
- r.jina.ai/[client-website]/solutions
- r.jina.ai/[client-website]/pricing

Extract:
- What they sell (one sentence)
- Who they sell to (inferred from case studies, logos, testimonials)
- Value proposition (what outcome they deliver)
- Social proof (logos, metrics, named customers)
- Pricing model (if visible)
- Proposed ICP starting point (titles, industries, company size)
```

Present your analysis to the client before asking questions:

> "Based on your website, here's what I understand about [CLIENT NAME]:
> - **What you sell:** [one sentence]
> - **Who buys:** [inferred titles and company types]
> - **Best customer examples from your site:** [names if visible]
> - **Proposed ICP starting point:** [draft]
> 
> Does this look right? We'll refine it through the intake questions."

---

## Phase 2 — Intake Interview (12 questions, one at a time)

Ask questions **one at a time**. Do not batch them. Wait for the answer before asking the next.

Reject vague answers — see Critical Rules below.

| # | Question | What you're really asking |
|---|----------|--------------------------|
| 1 | What do you sell in one sentence? | Force precision. Pre-fill from site, confirm. |
| 2 | Who is your single best customer — name them. | Anchor the ICP to a real account, not a hypothesis. |
| 3 | What job title signs the contract? And who influences the decision? | Buyer vs. champion distinction. |
| 4 | What's the minimum company size that makes sense as a customer? And maximum? | Hard headcount or revenue floor/ceiling. |
| 5 | Which industries are in? Which are out? | Force explicit exclusions, not just inclusions. |
| 6 | What geography — countries, regions, or specific states? | |
| 7 | Are there any triggers that make a company more likely to buy right now? (new hire, funding, M&A, regulation, expansion, tech change…) | These become signal candidates in Phase 4. |
| 8 | Any disqualifiers? (competitors, existing customers, companies in legal trouble, wrong business model…) | |
| 9 | What's your offer — what are you asking them to do in the first email? | |
| 10 | Is there something free or low-risk you can offer to lower the barrier? (audit, assessment, benchmark, pilot…) | Lead magnet for outreach. |
| 11 | What tone fits your brand — peer-to-peer and direct, or more formal? | |
| 12 | Any legal constraints or words that can never appear in outreach? | |

---

## Phase 3 — Closed-Won Account Analysis

This is what separates JumpSeat's ICP from a hypothesis. Analyze the client's **best customers** — the ones that closed, stayed, and renewed.

```
Ask: "Can you give me a list of your 10–30 best closed-won accounts? 
Company name + industry + revenue/size + why they bought is enough."

If they can't provide a list, ask:
"Name your 5 best customers. I'll research the rest."
```

For each closed-won account, extract:
- Industry / vertical
- Company size (revenue + headcount)
- Geography
- What triggered the sale (if known)
- Business model characteristics (how they operate)
- Any shared patterns across accounts

Then surface the patterns:

> "Looking at your [N] best customers, here's what they have in common:
> - **Vertical:** [most common]
> - **Size:** [range]
> - **Trigger:** [most common buying trigger]
> - **Shared characteristic:** [operational or structural pattern]
> 
> Does this match your intuition? Anything surprising here?"

**This is also the input for the Signal Hunter workshop** — the patterns you find here become the seed for signal candidates.

---

## Phase 4 — Hard Filter vs. Soft Preference Classification

For each ICP criterion identified, ask:

> "If a company matched everything except **[CRITERION]** — would you still reach out? Yes or no?"

- **YES → Soft preference.** Nice to have. Influences priority but doesn't disqualify.
- **NO → Hard filter.** If missing, skip the account entirely.

Common patterns (use as guide, not rule):
| Criterion | Usually |
|-----------|---------|
| Job title | HARD — wrong title = wrong door |
| Industry | HARD — wrong vertical = no problem to solve |
| Revenue / headcount minimum | HARD — below threshold = can't afford or wrong scale |
| Revenue / headcount maximum | SOFT — ceiling is more flexible |
| Geography | HARD if service is local; SOFT if remote-first |
| Triggers (funding, new hire…) | ALWAYS SOFT — triggers filter for timing, not fit |
| Headcount edges | SOFT in most cases |

**Rule:** Triggers are NEVER hard filters. A company without a trigger is still a fit — just lower urgency. Triggers belong in signal research, not ICP gating.

---

## Phase 5 — ICP Validation Loop

Before locking the ICP, test it against real accounts.

```
Select 10 accounts — mix of:
- 3–4 known good fits (closed-won or strong pipeline)
- 3–4 accounts that should NOT qualify
- 2–3 edge cases (borderline)

Run the draft Mode 0 scoring prompt against all 10.
```

Present results in table:

| Company | Fit Tier | Score | Reasoning |
|---------|----------|-------|-----------|
| [name] | Tier 1 / Tier 2 / Not ICP | X/10 | [one sentence] |

Ask:
> "Are these results correct? Which ones are wrong — and why?"

Iterate until **2 consecutive rounds have zero corrections**.

When converged:
- Save the tuned prompt as `icp-prompt.md`
- Lock the `client-profile.yaml`
- Note how many rounds it took to converge (signals ICP clarity)

---

## Output 1 — `client-profile.yaml`

This is the format all downstream skills read. Generate every section below.
Sections marked `# → skill-signal-hunter` are left empty — signal-hunter fills them next.
Sections marked `# → confirm with client` need the client to verify before locking.

```yaml
# client-profile.yaml — [CLIENT NAME]
# Generated by: skill-icp-onboarding
# Version: 1.0 | [DATE]

# ─────────────────────────────────────────────
# 1. CLIENT
# ─────────────────────────────────────────────

client:
  name: <CLIENT NAME>
  description: >
    <What they sell and to whom — 2 sentences max. From website analysis + Q1.>
  service: <What they sell — one short phrase>
  buyers:
    - <Job title that signs the contract — from Q3>
    - <Title synonym 1>
    - <Title synonym 2 — influencer role>
  deal_profile:
    type: <Contract / SaaS subscription / Project / Retainer>
    sales_cycle: <e.g. "3–6 months" — from Q2 + closed-won patterns>
    relationship_driven: <true / false>
  research_keywords:
    - <keyword 1 — job posting title that signals active pain for this client>
    - <keyword 2>
    - <keyword 3>
    # These feed skill-deep-research Phase 2 (JobSpy).
    # Use the job titles a prospect posts when they're doing in-house what this client solves.

# ─────────────────────────────────────────────
# 2. ICP CRITERIA
# ─────────────────────────────────────────────

icp:
  summary: >
    <One sentence: who is the ideal buyer and what makes them qualified.>
  criteria:
    - criterion: <Primary vertical — from Q5 + closed-won patterns>
      values: [<industry 1>, <industry 2>]
      points: 2
      how_to_evaluate: "Industry classification"
    - criterion: <Revenue or size threshold — from Q4>
      points: 2
      how_to_evaluate: "Public revenue data or estimate"
    - criterion: <Operational scale qualifier — from closed-won patterns>
      points: 2
      how_to_evaluate: "Company website, SEC filings"
    - criterion: <Business model match — from Q2>
      points: 2
      how_to_evaluate: "Business model inference"
    - criterion: <Geography qualifier — from Q6>
      points: 2
      how_to_evaluate: "HQ location"
  exclusions:
    industries_out: [<from Q5 explicit exclusions>]
    excluded_domains: [<competitor.com>, <existing-customer.com>]
  tier_thresholds:
    tier_1:
      range: "8–10"
      action: "Run full signal research immediately"
    tier_2:
      range: "5–7"
      action: "Run signal research, lower priority queue"
    not_icp:
      range: "1–4"
      action: "Skip — do not invest in signal research"

# ─────────────────────────────────────────────
# 3. CONVICTION THRESHOLDS
# ─────────────────────────────────────────────

conviction:
  levels:
    high:
      min_confirmed_signals: 7
      action: "Build dossier immediately. Outreach this week."
    medium:
      min_confirmed_signals: 3
      action: "Research signal gaps. Queue for next sprint."
    pending:
      condition: "Strong ICP fit but 0–2 confirmed signals"
      action: "Monitor queue. Define reactivation triggers. Never discard."
    low:
      condition: "Weak ICP fit and 0–2 confirmed signals"
      action: "Do not prioritize."
  rules:
    - "PENDING ≠ LOW. Strong fit, wrong timing. Set reactivation condition."
    - "If any DX signal is confirmed → move to PENDING regardless of score."

# ─────────────────────────────────────────────
# 4. SIGNAL LIBRARY  →  skill-signal-hunter fills this
# ─────────────────────────────────────────────

signals: []
# Run skill-signal-hunter to generate signal candidates.
# Workshop with client → finalize.
# signal-hunter seed from closed-won patterns:
signal_hunter_seed:
  - <pattern 1 from closed-won analysis — e.g. "companies that recently hired a new ops leader">
  - <pattern 2 — e.g. "companies opening new facilities">
  - <pattern 3 — from Q7 buying triggers>

distraction_signals: []
signal_combos: []
signal_timing: []

# ─────────────────────────────────────────────
# 5. EMAIL RULES
# ─────────────────────────────────────────────

email_rules:
  email_1_max_words: 75
  email_2_max_words: 35
  tone: <from Q11 — e.g. "Direct, peer-to-peer, confident" or "formal, executive">
  offer:
    primary_cta: <from Q9 — what you ask the lead to do>
    lead_magnet: <from Q10 — free or low-risk offer>
  hard_rules:
    - "Never pitch. Never mention the service explicitly in Email 1."
    - "Line 1 must be a specific observation — not a question."
    - "End Email 1 with one soft question. Not a meeting request."
    - "Email 2 must stand alone. Never open with 'Following up'."
    - "Email 1 > 75 words → rewrite. Email 2 > 35 words → rewrite."
  banned_words: <from Q12 — legal or brand constraints>

# ─────────────────────────────────────────────
# 6. CRM EXPORT  →  confirm with client
# ─────────────────────────────────────────────

crm:
  platform: <Salesforce / HubSpot / Other — confirm with client>
  object: <Account / Contact / Lead — confirm with client>
  fields: {}
  # TODO: fill in CRM field names once confirmed with client.
  # Standard fields (update with client's actual names):
  # account_name, industry, revenue, fit_tier, fit_score,
  # conviction_score, weighted_score, urgency, top_signal,
  # dossier_status, next_action, last_updated

# ─────────────────────────────────────────────
# 7. MODEL ROUTING
# ─────────────────────────────────────────────

models:
  deep_research: "claude-opus-4-8"
  icp_fit_scoring: "claude-sonnet-4-6"
  signal_research: "claude-sonnet-4-6"
  dossier_builder: "claude-opus-4-8"
  email_personalization: "claude-opus-4-8"
  crm_export: "claude-sonnet-4-6"

# ─────────────────────────────────────────────
# METADATA
# ─────────────────────────────────────────────

version: "1.0"
built_by: <JumpSeat team member>
last_updated: <YYYY-MM-DD>
program: <JumpSeat × CLIENT NAME>
status: "Draft — pending signal workshop"
next_step: "Run skill-signal-hunter to generate signal candidates"
```

---

## Output 2 — `icp-prompt.md`

The calibrated scoring prompt saved for reuse in Mode 0.

```
You are an ICP analyst for [CLIENT NAME].

[CLIENT ONE-LINER]

Evaluate this company for ICP fit. Return a Fit Tier and score.

Company data: [COMPANY NAME] | [INDUSTRY] | [REVENUE] | [HEADCOUNT] | [LOCATION]

HARD FILTERS — if any of these fail, return Not ICP immediately:
- [HARD FILTER 1]
- [HARD FILTER 2]
- [HARD FILTER 3]

DISQUALIFIERS — if any of these match, return Not ICP:
- [DISQUALIFIER 1]
- [DISQUALIFIER 2]

SCORING CRITERIA (for accounts that pass hard filters):
- [CRITERION 1]: +2 points
- [CRITERION 2]: +2 points
- [CRITERION 3]: +2 points
- [CRITERION 4]: +2 points
- [CRITERION 5]: +2 points

FIT TIERS:
8–10 = Tier 1 → Run full signal research immediately
5–7  = Tier 2 → Run signal research, lower priority queue
1–4  = Not ICP → Skip — do not invest in signal research

Return JSON:
{
  "company": "",
  "fit_score": 0,
  "fit_tier": "Tier 1 / Tier 2 / Not ICP",
  "fit_reasoning": "one sentence",
  "go_no_go": "GO / NO-GO",
  "hard_filter_failed": null or "[which filter failed]",
  "skip_reason": null or "[why disqualified]"
}
```

---

## Critical Rules

**Reject vague answers:**
- "SMBs" → ask for headcount numbers
- "Anyone who needs X" → ask for a named customer example
- "Most industries" → ask which ones are explicitly OUT
- "It depends" → push for a default assumption

**Include title synonyms always.** VP of Supply Chain = VP of Logistics = VP of DC Operations = Director of Supply Chain. Missing synonyms = missing leads.

**Triggers are NEVER hard filters.** They go in `icp_soft_preferences.triggers` and feed the Signal Hunter workshop. A company without a trigger is still a fit — just not in an active window right now.

**The closed-won analysis is not optional.** An ICP built from "who we think we should sell to" diverges from "who actually bought." Always validate against real accounts.

**Never lock the ICP without running the validation loop.** If it takes more than 4 rounds to converge, the ICP is still fuzzy — push the client to make harder choices about hard filters.

---

## File storage convention

```
skills/client/[client-slug]/
  client-profile.yaml       ← locked ICP definition
  icp-prompt.md             ← calibrated Mode 0 prompt
  icp-validation-log.md     ← which 10 companies were tested, what was corrected, how many rounds
```
