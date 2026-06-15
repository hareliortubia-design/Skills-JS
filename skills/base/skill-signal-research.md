# Skill: Signal Research
**JumpSeat Agents:** 04 Signal Hunter → 06 Signal Implication → 05 Timing → 07 Conviction Scoring → 08 Prioritization & Stacking  
**Type:** Base — works for any client  
**Version:** 2.0 | June 2026

> Reads the client's signal library and the research brief, then classifies every signal as Confirmed, Inferred, Not Found, or Unknown. A signal is only Confirmed if a real source backs it — job posting URL, SEC filing, news article, or crawled page.

---

## Input
- `client-profile.yaml` — signals[], signal_combos[], conviction thresholds, signal_timing[]
- `signal-library.md` — the finalized signal list for this client (from `skill-signal-hunter`)
- `research-brief.json` — output from `skill-deep-research` (with JobSpy + edgartools + jina data)

## Output / Artifact
- Signal matrix with every signal classified C/I/N/U + key finding + source
- Feeds into `skill-conviction-algorithm`

## Feeds into
- `skill-conviction-algorithm` → calculates weighted score + priority
- `skill-crm-export` → all accounts regardless of conviction level

## Rule
A signal is a neutral fact — not good or bad. What matters is the implication. Never elevate an Inferred signal to Confirmed. If you can't cite a source from the research brief, it stays I.

---

## Signal classification system

| Code | Label | Definition |
|------|-------|-----------|
| **C** | Confirmed | Source found in research brief. URL, document reference, or direct evidence cited. |
| **I** | Inferred | Logically derived from business model or industry. No direct source in research brief. |
| **N** | Not Found | Researched in brief. Evidence not found across all sources. |
| **U** | Unknown | Not covered in research brief — would require additional research. |

**Rule:** Never elevate Inferred to Confirmed. A strong inference is still I.

---

## Source hierarchy (what counts as Confirmed)

| Source type | Counts as Confirmed for |
|-------------|------------------------|
| JobSpy posting with URL + date | LB-01, LB-02, LS-01 (if leadership role) |
| SEC 8-K filing with date | GR-02 (M&A), GR-01 (new facility) |
| SEC 10-K MD&A with exact quote | FN-02 (cost-cut language) |
| SEC 10-K Properties section | YP-03, YP-04, YP-08 (facility data) |
| Company press release with URL | GR-01, GR-02, LS-01 |
| LinkedIn profile with start date | LS-01 (new leader) |
| OSHA database with case number | SF-01 |
| Company sustainability report URL | SU-02 |
| CARB WAIRE database entry | SU-01 |
| PitchBook / Crunchbase / Owler | FN-01 (PE ownership) |
| News article with URL + date | GR-02, FN-02, DX signals |
| Google Maps / informal sources | ❌ NOT Confirmed — Inferred at best |
| "Company probably has 24/7 ops" | ❌ NOT Confirmed — Inferred |

---

## How to run

1. Read `client-profile.yaml` — load `signals[]`, `signal_combos[]`, `conviction.levels`, `signal_timing[]`
2. Read `signal-library.md` — get the finalized signal list for this client
3. Receive `research-brief.json` — the data collected by `skill-deep-research`
4. Run the Signal Research prompt below
5. Output the signal matrix JSON

---

## Prompt

```
You are a B2B signal research analyst. Classify each signal for this account
using only data found in the research brief — no assumptions, no inferences
without flagging them clearly.

CLIENT PROFILE — load signal definitions and weights:
[PASTE client-profile.yaml — sections: signals[], signal_combos[], conviction, signal_timing]

RESEARCH BRIEF — the actual data collected:
[PASTE research-brief.json]

---

For each signal in the client's signal library, do the following:

1. Search the research brief for evidence of this signal.
   Look specifically in these sections based on signal type:
   - LB-01, LB-02: research_brief.job_postings.relevant_roles_active + repeat_postings
   - LS-01: research_brief.job_postings.leadership_roles_active + research_brief.leadership.recent_leadership_changes
   - GR-02: research_brief.sec_filings.latest_8k + research_brief.recent_news.ma_activity
   - GR-01: research_brief.sec_filings.latest_8k + research_brief.recent_news.facility_news
   - FN-02: research_brief.sec_filings.10k_mda_excerpts
   - FN-01: research_brief.company_profile.ownership + research_brief.company_profile.ownership_source
   - YP-03, YP-04, YP-08: research_brief.sec_filings.10k_properties + research_brief.company_profile.all_known_locations
   - SF-01: research_brief.osha_records
   - SU-02: research_brief.esg
   - DX signals: research_brief.recent_news.distraction_signals + research_brief.leadership.ceo.outside_roles

2. Assign classification:
   C — if the research brief contains a source (URL, filing name, or document) that directly confirms this signal
   I — if the signal is logically derivable from the company's business model but no source in the brief confirms it
   N — if the research brief covered this area and found nothing
   U — if the research brief did not cover this signal's detection sources at all

3. Write the key finding in one sentence.
   If C: include the source (URL or document name).
   If I: state what you're inferring from and why.
   If N: state what was searched and what was not found.
   If U: state what additional research would be needed.

4. After classifying all signals:
   - Check distraction signals first. If any DX is C → flag PENDING OVERRIDE.
   - Pass the completed signal matrix to skill-conviction-algorithm for scoring.

Return the full signal matrix JSON.
```

---

## Output Format

```json
{
  "company": "",
  "research_date": "YYYY-MM-DD",
  "signals": {
    "[SIGNAL-CODE]": {
      "status": "C / I / N / U",
      "finding": "One sentence. If C: include source. If I: state the inference basis.",
      "source": "URL or document name — required if status = C"
    }
  },
  "distraction_signals": {
    "[DX-CODE]": {
      "status": "C / N",
      "finding": "",
      "source": ""
    }
  },
  "pending_override": false,
  "pending_override_reason": "",
  "ready_for_conviction": true
}
```

---

## Quality gate

| Gate | Rule |
|------|------|
| Source required for C | Confirmed signal with no source URL or document → downgrade to I |
| Research brief must exist | If research-brief.json is missing or LOW confidence → stop, re-run deep-research first |
| JobSpy data checked | LB signals must reference JobSpy data. If job_postings is empty, flag U not N. |
| edgartools data checked | FN/GR signals for public companies must reference SEC data. |
| Distraction signals first | Always check DX signals before scoring. Read their impact field — STOP = PENDING, FLAG = note only. |
| Agent bias check | If all signals are C or all are I — review. Real accounts have a mix. |
| No invented sources | "Based on industry norms" or "likely has" = I, never C. |

---

## CASCADE → Next step

When the signal matrix is complete, tell the user:

> "Signal matrix ready. Save as `signal-research-[company-name].json`.
>
> → **Next: run skill-conviction-algorithm**
> Bring: this signal matrix + client-profile.yaml (signals, signal_combos, conviction, signal_timing sections).
> It will calculate the weighted conviction score and tell you whether to build a dossier."
