# Skill: Deep Research
**JumpSeat Agent:** 02 Research  
**Type:** Base — works for any client  
**Version:** 2.0 | June 2026

> Builds a structured research brief on a company by running three data sources in parallel — real job postings (JobSpy), SEC filings (edgartools), and web crawl (jina) — before any signal classification happens.

---

## Input
- `client-profile.yaml` — for `client.research_keywords` (role keywords to search)
- Company name + website URL

## Output / Artifact
- `research-brief.json` — structured brief with real data from all sources, ready for `skill-icp-fit-scoring` and `skill-signal-research`

## Feeds into
- `skill-icp-fit-scoring` → gates the account before signal research
- `skill-signal-research` → uses the brief to classify every signal

## Rule
Every finding must have a source. "Probably has DCs" without a URL = Inferred, not Confirmed. This step is what separates a system that detects real signals from one that invents them.

---

## Tracks — choose based on your setup

**TRACK A (full — with Python):** Runs JobSpy + edgartools + jina. Produces the highest-quality brief. Required for labor signals (LB-01, LB-02) and SEC signals (GR-02, FN-01, FN-02).

**TRACK B (no Python — jina + web search only):** Runs Phases 4–7 only. Labor signals will be marked Unknown. SEC signals for private companies marked N/A. Valid brief — just with more Unknown fields. Use this if you can't run Python.

```
TRACK B entry point: skip to Phase 4 — Web crawl.
Mark job_postings section as: { "track": "B — jina only", "relevant_roles_active": [], "note": "JobSpy not run — labor signals LB-01 and LB-02 are Unknown for this account" }
Mark sec_filings section as: { "track": "B — jina only", "note": "edgartools not run — check if public company and re-run Phase 3 if needed" }
```

---

## Setup — install dependencies once (Track A only)

```bash
pip install jobspy edgartools
```

---

## Phase 1 — Read client config

```python
import yaml

with open("client-profile.yaml", "r") as f:
    profile = yaml.safe_load(f)

client_name = profile["client"]["name"]
role_keywords = profile["client"]["research_keywords"]
# role_keywords example for Lazer: ["spotter driver", "yard truck driver", "hostler driver"]
```

---

## Phase 2 — Job postings (JobSpy — real data)

Detects signals: **LB-01** (in-house roles posted), **LB-02** (repeat postings), **LS-01** (leadership hires)

```python
from jobspy import scrape_jobs
import pandas as pd

# Run once per keyword batch
jobs = scrape_jobs(
    site_name=["indeed", "linkedin", "glassdoor", "zip_recruiter"],
    search_term=" OR ".join(role_keywords),
    google_search_term=f"{company_name} {' OR '.join(role_keywords)}",
    location="USA",
    results_wanted=50,
    hours_old=720,          # last 30 days
    country_indeed="USA"
)

# Also search for leadership hires
leadership_jobs = scrape_jobs(
    site_name=["linkedin", "indeed"],
    search_term=f"{company_name} VP Supply Chain OR VP Logistics OR VP Operations OR VP DC",
    results_wanted=20,
    hours_old=540           # last 18 months
)

# Save to file for research brief assembly
jobs.to_csv("_jobspy_output.csv", index=False)
leadership_jobs.to_csv("_jobspy_leadership.csv", index=False)
```

**What to extract from JobSpy output:**
- Active postings matching `role_keywords` → source for LB-01
- Same role posted 2+ times in last 90 days → source for LB-02
- VP/Director Supply Chain / Logistics / Operations with start date < 18 months → source for LS-01
- Posting location → helps confirm facility states

---

## Phase 3 — SEC filings (edgartools — real data)

Detects signals: **GR-02** (M&A via 8-K), **FN-01** (PE ownership via 10-K), **FN-02** (cost-cut language via 10-K MD&A), **GR-01** (new facility via 8-K)

```python
from edgar import Company, get_filings, set_identity

# Required: identify yourself to EDGAR (free, no API key needed)
set_identity("your-name your@email.com")

company = Company(company_name)

# Pull last 3 8-K filings (material events: M&A, new facilities, leadership changes)
filings_8k = company.get_filings(form="8-K").latest(3)
for filing in filings_8k:
    doc = filing.obj()
    print(f"8-K filed: {filing.filing_date}")
    print(doc.text[:2000])   # first 2000 chars of each filing

# Pull latest 10-K (annual report: ownership, MD&A cost language, facility list)
filing_10k = company.get_filings(form="10-K").latest(1)
if filing_10k:
    doc = filing_10k[0].obj()
    mda = doc.get("Management Discussion")   # MD&A section — cost language lives here
    properties = doc.get("Properties")       # facility list
    print(mda[:3000])
    print(properties[:2000])
```

**What to extract from edgartools output:**
- 8-K with "acquisition", "merger", "agreement to acquire" → source for GR-02
- 8-K with "new distribution center", "facility opening" → source for GR-01
- 10-K Properties section → facility count, sq footage → source for YP-03, YP-04, YP-08
- 10-K MD&A with "streamlining", "cost reduction", "rightsizing", "operational efficiency" → source for FN-02
- 10-K ownership section or parent company reference → source for FN-01

**Note:** edgartools only works for public companies (SEC-registered). For private companies, skip Phase 3 and mark SEC signals as research gaps.

---

## Phase 4 — Web crawl (jina reader)

Detects signals: **YP-01–08** (facility/operations), **SU-01/02** (ESG), **CT-01** (incumbent vendor), distraction signals

```
Fetch these URLs via r.jina.ai:

Company website:
  r.jina.ai/[domain]/about
  r.jina.ai/[domain]/about-us
  r.jina.ai/[domain]/careers
  r.jina.ai/[domain]/newsroom
  r.jina.ai/[domain]/sustainability
  r.jina.ai/[domain]/locations
  r.jina.ai/[domain]/facilities

Background:
  r.jina.ai/https://en.wikipedia.org/wiki/[Company_Name]
  r.jina.ai/https://www.crunchbase.com/organization/[slug]

LinkedIn company page:
  r.jina.ai/https://www.linkedin.com/company/[company-slug]/
  Extract: employee count, headquarters, recent posts, leadership team

Headers to add for better extraction:
  X-Return-Format: markdown
  X-Timeout: 10
```

---

## Phase 5 — News search (jina search + Google)

Detects signals: **GR-02** (M&A news), **LS-01** (leadership changes), **DX-01/02/03** (distraction signals)

```
Run these searches via s.jina.ai (searches + fetches top 5 results):

s.jina.ai/[company name] acquisition merger 2025 2026
s.jina.ai/[company name] new distribution center warehouse 2025 2026
s.jina.ai/[company name] CEO CFO VP Supply Chain hired appointed 2025 2026
s.jina.ai/[company name] layoffs restructuring cost cutting 2025 2026
s.jina.ai/[company name] lawsuit legal investigation 2026

Also fetch via r.jina.ai:
  r.jina.ai/https://www.google.com/search?q=[company+name]+news&tbs=qdr:m3
```

---

## Phase 6 — OSHA lookup

Detects signals: **SF-01** (OSHA citation)

```
r.jina.ai/https://www.osha.gov/pls/imis/establishment.search?p_logger=1&p_state=&p_county=&p_cn=&p_cn_flg=&p_establishment=[COMPANY+NAME]

Extract: citation number, inspection date, violation type, penalty amount
```

---

## Phase 7 — Executive research

Detects signals: **LS-01** (new leader), **DX-01** (CEO distraction)

```
r.jina.ai/https://www.google.com/search?q=[CEO+name]+[company]+board+commission+appointed+2026

Extract: any outside roles, board positions, political appointments, legal proceedings
```

---

## Phase 8 — Compile research brief

Pass all collected data to Claude with this prompt:

```
You are assembling a research brief from multiple data sources.

Company: [COMPANY_NAME]
Date: [TODAY]

DATA COLLECTED:
- JobSpy job postings: [paste _jobspy_output.csv content]
- JobSpy leadership postings: [paste _jobspy_leadership.csv content]
- SEC 8-K filings: [paste edgartools 8-K output]
- SEC 10-K excerpts (MD&A + Properties): [paste edgartools 10-K output]
- Website crawl: [paste jina outputs]
- News search results: [paste s.jina.ai results]
- OSHA lookup: [paste result]
- Executive research: [paste result]

Compile everything into the Research Brief JSON below.

Rules:
- Every field must have a source. No finding without a URL or document name.
- If a source returned nothing: mark as research gap, don't invent data.
- If two sources confirm the same fact: note both sources.
- Flag any distraction signals prominently.
- For job postings: identify repeat postings (same role posted 2+ times).
- For SEC data: note the filing type, date, and exact quote where relevant.

Return the Research Brief JSON.
```

---

## Output Format — Research Brief JSON

```json
{
  "company": "",
  "research_date": "YYYY-MM-DD",
  "data_sources_used": ["jobspy", "edgartools", "jina", "osha"],
  "company_profile": {
    "revenue_estimate": {"value": "", "source": ""},
    "employee_count": {"value": "", "source": ""},
    "founded": "",
    "ownership": "public / private / PE / family-owned",
    "ownership_source": "",
    "hq_address": "",
    "all_known_locations": [
      {"address": "", "sq_footage": "", "dock_doors": "", "source": ""}
    ],
    "product_lines": []
  },
  "leadership": {
    "ceo": {"name": "", "tenure": "", "outside_roles": [], "source": ""},
    "recent_leadership_changes": [
      {"name": "", "title": "", "start_date": "", "source": ""}
    ]
  },
  "job_postings": {
    "relevant_roles_active": [
      {"title": "", "board": "", "posted_date": "", "location": "", "url": ""}
    ],
    "repeat_postings": [
      {"title": "", "count": 0, "date_range": "", "source": ""}
    ],
    "leadership_roles_active": [
      {"title": "", "board": "", "posted_date": "", "url": ""}
    ]
  },
  "sec_filings": {
    "latest_8k": [
      {"date": "", "summary": "", "ma_flag": false, "facility_flag": false, "source": ""}
    ],
    "10k_mda_excerpts": [
      {"text": "", "cost_cut_flag": false, "source": ""}
    ],
    "10k_properties": [
      {"location": "", "sq_footage": "", "dock_doors": "", "source": ""}
    ]
  },
  "recent_news": {
    "ma_activity": [{"headline": "", "date": "", "source": ""}],
    "facility_news": [{"headline": "", "date": "", "source": ""}],
    "leadership_news": [{"headline": "", "date": "", "source": ""}],
    "financial_news": [{"headline": "", "date": "", "source": ""}],
    "distraction_signals": [{"type": "", "description": "", "source": ""}]
  },
  "osha_records": {
    "citations_found": [
      {"case_number": "", "date": "", "violation_type": "", "penalty": "", "source": ""}
    ],
    "last_inspection": ""
  },
  "esg": {
    "sustainability_report_url": "",
    "scope3_targets": "",
    "certifications": []
  },
  "research_gaps": [],
  "confidence": "HIGH / MEDIUM / LOW",
  "confidence_reasoning": ""
}
```

---

## Quality gate

| Gate | Rule |
|------|------|
| Source required | Every confirmed finding needs a URL, filing name, or named source |
| JobSpy ran | If job postings section is empty, check that JobSpy executed — don't mark as N without running it |
| edgartools scope | Only runs for public companies. Private = mark SEC fields as N/A, not research gaps |
| Repeat postings | Always check jobspy output for same role posted 2+ times — most competitors miss this |
| Distraction signals | Always searched. If CEO has outside role, flag prominently before proceeding |
| Confidence threshold | LOW confidence → flag to user before running signal research — data may produce false results |

---

## CASCADE → Next step

When the research brief is complete, tell the user:

> "Research brief ready. Save as `research-brief-[company-name].json`.
>
> → **Next: run skill-icp-fit-scoring**
> Bring: this research-brief.json + your client-profile.yaml (icp.criteria section).
> It will score this account against your ICP and tell you whether to proceed."
