# JumpSeat Signal Intelligence — Skills System

**Version:** 1.0 | **Built by:** Hareli Ortubia | **Last updated:** June 2026  
**Access:** Private — JumpSeat team only

---

## What this is

A system of 10 reusable Claude skills that run in cascade to identify companies in an active buying window. Given a company name and website, the pipeline produces in ~30 minutes:

- ICP fit score (Tier 1 / Tier 2 / Not ICP)
- Signal matrix with confirmed evidence and sources
- Conviction score (HIGH / MEDIUM / PENDING / LOW)
- 4–5 page intelligence dossier
- Prioritized contact map
- Email 1 (≤75 words) + Email 2 (≤35 words)
- CRM export CSV

---

## Repository structure

```
skills/
├── base/                            ← 10 base skills — reusable for any client
│   ├── skill-jumpseat-kickoff.md    ← Entry point — detects state and routes
│   ├── skill-icp-onboarding.md      ← Builds client-profile.yaml for a new client
│   ├── skill-signal-hunter.md       ← Generates signal-library.md (workshop)
│   ├── skill-deep-research.md       ← Builds research-brief.json per account
│   ├── skill-icp-fit-scoring.md     ← Gates the account: Tier 1 / Tier 2 / Not ICP
│   ├── skill-signal-research.md     ← Classifies signals C/I/N/U with real sources
│   ├── skill-conviction-algorithm.md← Weighted score, auditable step by step
│   ├── skill-dossier-builder.md     ← 4–5 page intelligence dossier
│   ├── skill-contact-mapping.md     ← Who to call, in what order, with what angle
│   ├── skill-email-personalization.md ← Email 1 ≤75w + Email 2 ≤35w
│   └── skill-salesforce-export.md   ← CSV for any CRM
│
├── client/
│   └── [client-name]/
│       └── client-profile.yaml      ← Client-specific ICP + signals (not in repo)
│
└── test/
    └── [account-name]/              ← Pipeline outputs per account (not in repo)
```

---

## The pipeline

```
STAGE 0 — CLIENT SETUP (once per new client)
  skill-icp-onboarding   → client-profile.yaml
  skill-signal-hunter    → signal-library (workshop with client)

STAGE 1 — RESEARCH (per account)
  skill-deep-research    → research-brief.json
      ↓
  skill-icp-fit-scoring  → Tier 1 / Tier 2 / Not ICP

STAGE 2 — INTELLIGENCE (Tier 1 + Tier 2 only)
  skill-signal-research      → signal matrix C/I/N/U
      ↓
  skill-conviction-algorithm → conviction score + priority

STAGE 3 — OUTPUT (HIGH conviction only)
  skill-dossier-builder      → 4–5 page dossier
      ↓
  skill-contact-mapping      → who to call and how
      ↓
  skill-email-personalization → Email 1 + Email 2

STAGE 4 — CRM (all qualified accounts)
  skill-salesforce-export    → CSV for Salesforce / HubSpot
```

---

## Signal classification

| Code | Meaning | Counts toward score |
|------|---------|-------------------|
| C | Confirmed — real source (URL, filing, job posting) | ✅ Yes |
| I | Inferred — logical deduction | ❌ No |
| N | Not Found — searched, not found | ❌ No |
| U | Unknown — not covered in research | ❌ No |

## Conviction levels

| Level | Condition | Action |
|-------|-----------|--------|
| HIGH | ≥7 weighted points confirmed | Dossier immediately. Outreach this week. |
| MEDIUM | 3–6 weighted points confirmed | Research gaps. Next sprint queue. |
| PENDING | Strong ICP fit, 0–2 confirmed | Monitor. Define reactivation trigger. |
| LOW | Weak ICP fit, 0–2 confirmed | Do not prioritize. |

---

## How to use — quick start

**Option A — Full pipeline (recommended)**
1. Open Claude Code
2. Navigate to this repo folder
3. Run `/jumpseat-icp-onboarding` if setting up a new client
4. Run `/jumpseat-kickoff` and follow the routing

**Option B — Direct skill**
1. Open the skill file from `skills/base/`
2. Copy its content into your Claude chat
3. Follow the skill instructions
4. Read the `CASCADE → Next step` section at the end

---

## Active clients

Client profiles and pipeline outputs are stored locally — not in this repository.  
Each client folder lives in `skills/client/[client-name]/` and is excluded via `.gitignore`.

---

## Key rules

1. **Never skip ICP scoring.** Signal research requires a fit gate first.
2. **Every confirmed signal needs a real source.** URL, filing name, or named source — no exceptions.
3. **Base skills have zero client hardcoding.** All client context lives in `client-profile.yaml`.
4. **PENDING ≠ LOW.** PENDING = strong fit, wrong timing. Always set a reactivation trigger.

---

## Maintained by

JumpSeat GTM Engineering team  
Contact: [hareliortubia@gmail.com](mailto:hareliortubia@gmail.com)
