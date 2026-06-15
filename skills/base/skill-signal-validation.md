# Skill: Signal Validation
**AgentOS Name:** si-signal-validation
**JumpSeat Agents:** 09 Signal QA → 10 Account List
**Type:** Base — works for any client
**Version:** 1.0 | June 2026

> Audits every Confirmed signal from the signal research phase against its cited source and event date, then produces the Signal Qualified Account List (SQAL) that prioritizes the sprint. The Signal QA report is the first document the client ever sees.

---

## Input
- `signal-research.json` — one per account, enriched output from `skill-signal-research` + `skill-conviction-algorithm` (Phase II complete)
- `client-profile.yaml` → `signals[]` (confirmation standards + perishability windows per signal type) + `conviction` thresholds + DX signal list
- Full batch context — all accounts in the sprint (needed for Agent 10 to rank across accounts)

## Output / Artifact
- `signal-qa-[company].md` — Signal QA report per account: post-QA signal status, source verification, event dates, rejection log
- Updated `signal-research-qa.json` — status fields updated to post-QA codes (C / I / N / U / S). Versioned — never overwrites the original.
- `sqal-[client]-sprint[N].md` — Signal Qualified Account List: all accounts ranked HIGH / MEDIUM / LOW / DEPRIORITIZED / FLAG with conviction score and one-line rationale

## Feeds into
- `skill-dossier-builder` — HIGH accounts advance to Phase IV
- Sprint queue — MEDIUM accounts held for next sprint
- Agent 18 Objection & Reactivation — DEPRIORITIZED and FLAG accounts

---

## Why this is a separate skill

Dan's rule: *"Did you really not find it? It's like, oh my gosh, I did find it. This changes everything."*

Signal QA exists because signal research is imperfect. Claude marks signals Confirmed when evidence is indirect. Event dates get omitted. Signals age out of their window without being flagged. Without an explicit audit pass, a HIGH conviction account can be built on three signals that wouldn't survive scrutiny — and the dossier, the emails, and the rep's first call all rest on shaky ground.

The QA report is also the first artifact the client sees. It proves the work is rigorous. A rejection log that shows you downgraded two signals builds more trust than a clean matrix that looks too good.

---

## Signal QA codes

| Code | Label | Definition |
|---|---|---|
| **C** | Confirmed | Post-QA verified — source is direct, event date present and within window |
| **I** | Inferred | No direct source — logically derived. Counts at 0.5× in conviction scoring. |
| **N** | Not Found | Searched and not found |
| **U** | Unknown | Not covered in research brief — would require additional targeted research |
| **S** | Stale | Event was confirmed, but event date is past the perishability window — does NOT count in scoring |

---

## Perishability windows (default — override with client-profile.yaml)

| Signal type | Window | Logic |
|---|---|---|
| Earnings release / guidance | 90 days | Next quarter supersedes |
| SEC 8-K material event | 90 days | Material events are acted on quickly |
| Press release / announcement | 90 days | News cycle moves on |
| Job posting (active) | 45 days | Postings expire or fill fast |
| Leadership hire / change | 180 days | New leaders evaluate vendors for ~6 months |
| Capital project announcement | 180 days | Project windows are longer |
| Facility expansion news | 180 days | Construction timelines allow longer windows |
| Regulatory filing (permit, EIS) | 180 days | Permitting windows are multi-month |
| Sustainability report / ESG | 365 days | Annual publications |
| Contract award / win | 90 days | Urgency is around award, not after |
| Acquisition / M&A | 180 days | Integration period is active |

If `client-profile.yaml` has a `perishability_days` field for a specific signal → use that instead.
If event date is unknown → flag as U-DATE. Do not recode as Stale, but flag for manual verification.

---

## SQAL tier rules (Agent 10)

| Tier | Rule |
|---|---|
| **HIGH** | 3+ post-QA Confirmed (C) signals, conviction score meets threshold, no active DX signal, fit-locus CLEAN or HIGH_PROVISIONAL |
| **MEDIUM** | 2 post-QA Confirmed signals, OR 3+ with soft barrier or MISMATCH fit-locus |
| **LOW** | 1 post-QA Confirmed signal |
| **DEPRIORITIZED** | Valid ICP target, but timing barrier or soft blocker makes it premature |
| **FLAG** | Active DX signal (STOP impact) or hard structural barrier |

**DX signals are absolute:** Any confirmed DX signal with impact STOP overrides the tier to FLAG — regardless of conviction score. Do not advance FLAG accounts to Phase IV.

Within HIGH and MEDIUM: rank by conviction score descending. Ties broken by: (1) number of Confirmed signals, (2) lead signal weight, (3) recency of lead signal event date.

---

## How to run

### Part 1 — Signal QA (one account at a time)

1. Read `client-profile.yaml` — load `signals[]` perishability windows, confirmation standards, DX signal list
2. Receive `signal-research.json` for this account — the enriched Phase II output
3. Run the **Signal QA prompt** below
4. Save output as `signal-qa-[company].md` and `signal-research-qa.json`
5. Repeat for every account in the batch

### Part 2 — Account List (full batch)

1. Collect all `signal-research-qa.json` files and their updated conviction data
2. Read `client-profile.yaml` — load conviction thresholds and DX definitions
3. Run the **Account List prompt** below
4. Save output as `sqal-[client]-sprint[N].md`

---

## Prompt — Part 1: Signal QA

```
You are the Signal QA Agent for the JumpSeat Signal Intelligence program.
Your job is to audit every Confirmed signal in this account's signal matrix.
You do not re-hunt signals. You verify that what was marked Confirmed can actually survive scrutiny.

CLIENT PROFILE — load perishability windows and confirmation standards:
[PASTE client-profile.yaml — signals[] section, including perishability_days per signal if present]

SIGNAL RESEARCH OUTPUT — the Phase II result for this account:
[PASTE signal-research.json for this account — full signals{} object with findings, sources, event dates]

Today's date: [TODAY'S DATE]

---

Run the following audit steps. Show your work for every C signal.

STEP 1 — EXTRACT ALL CONFIRMED SIGNALS
List every signal where status = C.
These are your audit targets. I, N, U signals are accepted as-is — do not re-audit them.

STEP 2 — VERIFY EACH CONFIRMED SIGNAL
For each C signal, check four things in order:

  Check 1 — SOURCE EXISTS
  Is there a URL, document name (e.g. "10-K Properties"), or specific reference in the finding field?
  If no source cited → downgrade to I. Note: "No source cited in finding."

  Check 2 — SOURCE IS DIRECT
  Does the source actually say what was reported?
  Or is the finding an inference from the source (e.g., "company has multiple DCs" → "they probably need yard services")?
  If inferential → downgrade to I. Note: "Source does not directly confirm — finding is an inference."

  Check 3 — EVENT DATE PRESENT
  Is there a specific date (YYYY-MM-DD or at minimum YYYY-MM) for when the confirmed event happened?
  If event date is missing → flag as U-DATE. Do not recode to S. Note: "Event date unknown — re-verify before outreach."

  Check 4 — WITHIN PERISHABILITY WINDOW
  Calculate: days_since_event = today - event_date
  Compare to the perishability window for this signal type (from client-profile.yaml or the default table).
  If days_since_event > window → recode to S. Note: "Event date [date] is [N] days old. Window: [N] days. Recoded Stale."
  If days_since_event > window × 0.8 → flag as Expiring Soon (still C, but flag for re-verification).

STEP 3 — COMPILE THE REJECTION LOG
List every signal that was downgraded with:
  - Original status (C)
  - New status (I / N / S)
  - Reason (which check failed, exactly)

STEP 4 — RECOUNT
After audit, recount:
  Confirmed (C): [N]
  Inferred (I): [N]
  Not Found (N): [N]
  Unknown (U): [N]
  Stale (S): [N]
  Delta vs pre-QA: [which signals changed]

STEP 5 — DX CHECK
Check if any DX (Distraction) signals in the matrix are Confirmed.
For each confirmed DX: read its impact from client-profile.yaml.
If impact = STOP → note "PENDING OVERRIDE — [reason]"
If impact = FLAG → note only, do not override

Return the Signal QA report in the format below.
```

---

## Output Format — Part 1: Signal QA Report

`signal-qa-[company].md`:

```markdown
# Signal QA Report — [Company Name]
**Client:** [client] | **QA Date:** [YYYY-MM-DD] | **Sprint:** [N]

## Summary
Pre-QA Confirmed: [N] | Post-QA Confirmed: [N]
Downgraded: [N] (C→I: [N] | C→N: [N] | C→S: [N]) | Flagged U-DATE: [N] | Expiring Soon: [N]

## Signal Status (post-QA)

| Code | Signal Name | Pre-QA | Post-QA | Source | Event Date | Window | QA Note |
|------|-------------|--------|---------|--------|------------|--------|---------|
| PJ-01 | [name] | C | C | [URL or doc] | [date] | 180d | Within window. ✅ |
| LS-01 | [name] | C | I | — | — | — | No source cited in finding. |
| GR-01 | [name] | C | S | [URL] | 2024-08-10 | 90d | 310 days old. Recoded Stale. |
| FN-01 | [name] | I | I | — | — | — | Accepted as inferred. No audit required. |

## Rejection Log

| Code | Signal | Pre-QA | Post-QA | Check Failed | Reason |
|------|--------|--------|---------|--------------|--------|
| LS-01 | [name] | C | I | Check 1 — Source exists | Finding says "leadership change inferred from org chart" — no URL or document |

## DX Status
[If no DX confirmed: "No Distraction signals confirmed. Proceed."]
[If DX confirmed: "DX-01 confirmed — impact: STOP. Account set to PENDING OVERRIDE. Reactivation trigger: [condition]."]
```

`signal-research-qa.json` — same structure as signal-research.json, status fields updated to post-QA codes. Filename versioned: `signal-research-qa-[company].json`.

---

## Prompt — Part 2: Account List (SQAL)

```
You are the Account List Agent for the JumpSeat Signal Intelligence program.
You have Signal QA reports for every account in this sprint.
Your job is to produce the Signal Qualified Account List (SQAL) — the prioritized queue that tells the team where to invest next.

CLIENT PROFILE — load conviction thresholds and DX rules:
[PASTE client-profile.yaml — conviction section + DX signal list + signal weights]

SIGNAL QA RESULTS — one entry per account:
[PASTE summary row from each signal-qa report: Company | Post-QA Confirmed | Conviction Score | DX Status | Fit-locus quality]

---

STEP 1 — APPLY TIER RULES
For each account, assign a priority tier using these rules:

  HIGH: 3+ post-QA Confirmed signals AND conviction score ≥ threshold AND no active DX (STOP) AND fit-locus is CLEAN or HIGH_PROVISIONAL
  MEDIUM: 2 post-QA Confirmed signals, OR 3+ with soft barrier, OR 3+ with MISMATCH fit-locus
  LOW: 1 post-QA Confirmed signal
  DEPRIORITIZED: Valid ICP target, but a barrier or timing issue blocks it
  FLAG: Active DX signal (STOP impact) OR hard structural barrier (acquisition in progress, company for sale, etc.)

STEP 2 — RANK WITHIN TIER
Within HIGH and MEDIUM: rank by conviction score descending.
For ties: use (1) number of post-QA Confirmed signals, (2) lead signal weight, (3) lead signal event date recency.

STEP 3 — WRITE ONE-LINE RATIONALE PER ACCOUNT
Every account gets one sentence: why this tier, right now.
Be specific — name the signals, name the barrier, name the DX if applicable.
No generic rationales ("good ICP fit" is not a rationale).

Return the SQAL in the format below.
```

---

## Output Format — Part 2: SQAL

`sqal-[client]-sprint[N].md`:

```markdown
# Signal Qualified Account List — [Client] — Sprint [N]
**Date:** [YYYY-MM-DD] | **Accounts reviewed:** [N]
**HIGH:** [N] | **MEDIUM:** [N] | **LOW:** [N] | **DEPRIORITIZED:** [N] | **FLAG:** [N]

---

## HIGH — Advance to Phase IV (Dossier Queue)

| Rank | Company | Conviction Score | Post-QA Confirmed | Lead Signal | Rationale |
|------|---------|-----------------|-------------------|-------------|-----------|
| 1 | Burlington Stores | 8.2 | 4 | GR-01 New DC | New 1.2M sqft DC confirmed + new SVP SC hired 6 weeks ago. Window is now. |
| 2 | Conagra Brands | 7.6 | 3 | LS-01 New SC Leader | New EVP Supply Chain confirmed (LinkedIn, 3 months in role). Capex filing active. |

## MEDIUM — Sprint Queue

| Rank | Company | Conviction Score | Post-QA Confirmed | Gap to HIGH | Rationale |
|------|---------|-----------------|-------------------|-------------|-----------|
| 1 | WeatherTech | 5.4 | 2 | 1 more C signal | YP gate partially resolved. Dock door count unverified. |

## LOW — Monitor Queue

| Company | Post-QA Confirmed | Rationale |
|---------|-------------------|-----------|
| [name] | 1 | Single GR signal — not enough to advance. Monitor for second signal. |

## DEPRIORITIZED

| Company | Barrier | Clear-condition |
|---------|---------|----------------|
| [name] | [what blocks it] | [what would change this] |

## FLAG — Do Not Contact

| Company | Reason |
|---------|--------|
| [name] | DX-01 confirmed — [what the distraction is]. Reactivation trigger: [condition]. |

---

## Sprint summary
- [N] accounts advanced to Phase IV → dossier queue
- [N] accounts queued for Sprint [N+1]
- [N] accounts in monitoring queue
- [N] accounts flagged — do not contact
```

---

## Quality gate

| Gate | Rule |
|---|---|
| Every C signal audited | No Confirmed signal skipped in QA — every one gets a row in the audit table |
| Rejection log complete | Every downgrade has a specific reason — not just "failed QA" |
| DX checked before tier | SQAL tier assignment happens AFTER DX check — FLAG accounts cannot be HIGH |
| No HIGH with <3 post-QA C | If QA reduced a HIGH account to 2 confirmed signals → tier drops to MEDIUM |
| Rationale is specific | "Good ICP fit" is not a rationale. Every tier needs a sentence that names signals, dates, or barriers |
| Versioned files | signal-research-qa.json never overwrites signal-research.json — always a new filename |

---

## CASCADE → Next step

When the SQAL is complete:

> "Signal Validation complete. Save:
> - `signal-qa-[company].md` for each account (QA reports)
> - `signal-research-qa-[company].json` for each account (updated status codes)
> - `sqal-[client]-sprint[N].md` (the SQAL)
>
> → **Next: run skill-dossier-builder** on every HIGH account in the SQAL.
> Bring: signal-qa-[company].md + signal-research-qa-[company].json + conviction-output.json
> It will write the account narrative (sections 1–5) and the full 8-section dossier."
>
> MEDIUM accounts: add to sprint queue. No dossier yet.
> DEPRIORITIZED / FLAG accounts: notify Agent 18 Objection & Reactivation.
