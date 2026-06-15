# JumpSeat Signal Intelligence — Agents Registry
**Version:** 3.0 | **Maintained by:** Hareli Ortubia | **Last updated:** June 2026
**Program:** JumpSeat Signal Intelligence Agent OS (27 agents, 00–26)

---

## What is an agent?

An agent is a specialized role that Claude adopts to do a specific job in the pipeline. Each agent has an identity, a defined scope of authority, a set of inputs it reads, a specific artifact it produces, and a checkpoint it enforces before handing off to the next.

A skill tells Claude *what to do*. An agent tells Claude *who to be, with what authority, at what checkpoint*.

```
Activate an agent by saying: "Activate AGENT-[XX] — load [client] client-profile.yaml"
The agent adopts its identity and runs until it produces its defined output and declares the handoff.
```

---

## The 27 Agents

| # | Agent | Phase | Status | Skill | Input | Output | File |
|---|---|---|---|---|---|---|---|
| 00 | Memory & Learning | Phase 0 (Always on) | Core | si-context-load | Previous sessions | methodology-log.md | [agent-00-memory.md](base/agent-00-memory.md) |
| 01 | Foundation | Phase I | Core | si-context-load | client-profile.yaml | Session frame + routing | [agent-01-foundation.md](base/agent-01-foundation.md) |
| 02 | Research | Phase I | Core | si-account-enrichment | Company name + URL | research-brief.json | [agent-02-research.md](base/agent-02-research.md) |
| 03 | Account Fit | Phase I | Core | si-icp-fit-scoring | research-brief.json | icp-fit-scoring.json (Tier 1 / Tier 2 / Not ICP) | [agent-03-account-fit.md](base/agent-03-account-fit.md) |
| 04 | Signal Hunter | Phase II | Core | si-signal-research | research-brief.json + signal-library | signal-research.json (C/I/N/U coded) | [agent-04-signal-hunter.md](base/agent-04-signal-hunter.md) |
| 05 | Timing | Phase II | Core | prompt only | signal-research.json | Perishability audit — staleness log, expiring-soon flags | [agent-08-timing.md](base/agent-08-timing.md) ¹ |
| 06 | Signal Implication | Phase II | Core | prompt only | signal-research.json | Implication brief — so-what per signal, fit-locus, B8 rating | [agent-05-signal-implication.md](base/agent-05-signal-implication.md) ¹ |
| 07 | Conviction Scoring | Phase II | Core | si-conviction-scoring | signal-research.json (enriched + timed) | conviction-output.json — Signal Stack − Barrier − Objection Penalty | [agent-06-prioritization-stacking.md](base/agent-06-prioritization-stacking.md) ¹ |
| 08 | Prioritization & Stacking | Phase II | Core | prompt only | conviction-output.json | Ranked signal stack — Lead / Support / Context / Hold / Drop | [agent-06-prioritization-stacking.md](base/agent-06-prioritization-stacking.md) ¹ |
| **09** | **Signal QA** | **Phase III** | **🔴 New** | **si-signal-validation** | signal-research.json + sources | **Signal QA report — C/I/N/U per account, sources, event dates. First client artifact. Rejection log.** | 🔴 To build |
| **10** | **Account List** | **Phase III** | **🔴 New** | **prompt only** | Signal QA report | **SQAL — Signal Qualified Account List, post-validation. Priority queue HIGH / MEDIUM / LOW / DEPRIORITIZED / FLAG.** | 🔴 To build |
| **11** | **Account Narrative** | **Phase IV** | **🔴 New** | **prompt only** | SQAL + research-brief.json | **Account narrative — situation, window, honest caveat (dossier sections 1–5). Site portfolio per account.** | 🔴 To build |
| 12 | Messaging Strategist | Phase IV | Core | si-dossier-generation | conviction-output.json + contacts | Account dossier — full 8-section dossier per HIGH account. Strategic brief per contact. | [agent-09-messaging-strategist.md](base/agent-09-messaging-strategist.md) ¹ |
| 13 | Contact Mapping | Phase V | Core | si-stakeholder-mapping | Dossier + client-profile.yaml | contact-map.md — 3–5 named contacts per HIGH account: DM, op owner, champion. Entry rationale. | [agent-07-contact-mapping.md](base/agent-07-contact-mapping.md) ¹ |
| 14 | Persona Intelligence | Phase V | Core | prompt only | contact-map.md + signals | Persona briefs — decision style, motivation, words they use for pain | [agent-14-persona.md](base/agent-14-persona.md) |
| 15 | Cold Email Copywriter | Phase VI | Core | si-outreach-generation | Dossier + contact + persona-brief | Email variants ×3 — signal-led, implication-led, problem-led — under 75 words each | [agent-10-cold-email-copywriter.md](base/agent-10-cold-email-copywriter.md) ¹ |
| 16 | Sequence Design | Phase VI | Core | prompt only | Emails + persona-brief | Contact + message + sequence bundle — 4-touch play per contact, SE-platform-ready | [agent-15-sequence-design.md](base/agent-15-sequence-design.md) ¹ |
| 17 | Feedback Loop | Phase VII (Always on) | Core | prompt only | All conviction outputs | Reply rate by signal report — what worked, by signal type and copy angle. Learning deposit to Agent 00. | [agent-11-feedback-loop.md](base/agent-11-feedback-loop.md) ¹ |
| 18 | Objection & Reactivation | Phase VII (Always on) | Core | prompt only | DEPRIORITIZED accounts + trigger conditions | Reactivation triggers — accounts re-promoted on new signal. Reactivation copy. | [agent-12-objection-reactivation.md](base/agent-12-objection-reactivation.md) ¹ |
| 19 | Analytics & Metrics | Phase VII (Always on) | Core | prompt only | Conviction outputs + reply data | Program metrics report — conviction-to-meeting conversion, queue health. System Health Score. | [agent-16-analytics.md](base/agent-16-analytics.md) ¹ |
| 20 | Deal Acceleration | Phase VII (Always on) | Core | prompt only | Active opportunities | 7-day deal playbook — five levers, one sequence per active opportunity | [agent-13-deal-acceleration.md](base/agent-13-deal-acceleration.md) ¹ |
| 21 | Prospecting | Phase VII (Always on) | Core | prompt only | ICP criteria + coverage audit | Net-new account candidates — pre-scored, enriched, fed back to Phase I | [agent-17-prospecting.md](base/agent-17-prospecting.md) ¹ |
| **22** | **Workflow Auditor** | **Phase VIII** | **🔴 New** | **si-qa-and-export** | All stage outputs | **QA log — missing files, incomplete stages, broken handoffs. Repeatability test results.** | 🔴 To build |
| **23** | **Export & Delivery** | **Phase VIII** | **🔴 New** | **si-qa-and-export** | QA log + all stage outputs | **Google Sheets tracker update. CRM / SE platform export — field-mapped, ready for ingest.** | 🔴 To build |
| **24** | **Signal Monitor** | **Phase IX** | **🔴 New** | **si-signal-research** | signal-research.json (existing) + perishability windows | **Batch signal update — new signals, expired windows, changed entity status. Re-research triggers.** | 🔴 To build |
| **25** | **Program Calibration** | **Phase IX** | **🔴 New** | **prompt only** | All sprints — conviction outputs + reply data | **Quarterly methodology audit — signal correlation, drift indicators. Updated signal weights.** | 🔴 To build |
| **26** | **HVO Pitch Builder** | **HVO** | **🔴 New** | **si-hvo-pitch** | Prospect name + vertical + ICP | **SI one-pager — prospect-specific: methodology, agents, live signals for their vertical. HVO brief — packaging, scope, pricing framing.** | 🔴 To build |

¹ *File exists but has old numbering. Content is valid — rename pending.*

---

## New Agents — Build Priority

| Priority | Agent | Why now |
|---|---|---|
| 1 | **09 Signal QA** | Needed for every new client from Phase III. Dan said "really important" — validates signals before they feed dossiers. |
| 2 | **26 HVO Pitch Builder** | Needed for iCreon pitch (June). Dan has examples ready. |
| 3 | **11 Account Narrative** | Fills the gap in dossier methodology Dan identified: "We didn't have an agent for the prompts." |
| 4 | **22 + 23 Workflow Auditor + Export** | Needed for iCreon handover — repeatability phase. |
| 5 | **24 + 25 Signal Monitor + Calibration** | Post-launch — needed once ISCO or Lazer activate outreach. |

---

## Pipeline Flow (v3.0 — 27 agents)

```
SESSION START
  └── AGENT-00 Memory & Learning (loads session, surfaces past learnings)
  └── AGENT-01 Foundation (loads client frame, routes to correct stage)

PHASE I — IDEAL CLIENT PROFILE
  └── AGENT-02 Research → research-brief.json
  └── AGENT-03 Account Fit → icp-fit-scoring.json
        → NOT ICP: STOP → log to AGENT-18 if barriers may resolve
        → Tier 1 / Tier 2: ADVANCE to Phase II

PHASE II — SIGNAL LIBRARY & SIGNAL VALIDATION (per qualified account)
  └── AGENT-04 Signal Hunter → signal-research.json (C/I/N/U coded)
  └── AGENT-05 Timing → perishability audit (adds staleness + expiry flags)
  └── AGENT-06 Signal Implication → so-what per signal + fit-locus + B8 rating
  └── AGENT-07 Conviction Scoring → conviction-output.json
  └── AGENT-08 Prioritization & Stacking → ranked signal stack

PHASE III — SIGNAL BASED ACCOUNT LIST  [NEW]
  └── AGENT-09 Signal QA → Signal QA report (first client artifact)
  └── AGENT-10 Account List → SQAL — Signal Qualified Account List
        → HIGH / MEDIUM / LOW / DEPRIORITIZED / FLAG

PHASE IV — DEEP RESEARCH / ACCOUNT DOSSIERS (HIGH accounts only)
  └── AGENT-11 Account Narrative → situation, window, caveat (sections 1–5)  [NEW]
  └── AGENT-12 Messaging Strategist → full 8-section dossier + strategic brief per contact

PHASE V — CONTACTS & PERSONAS
  └── AGENT-13 Contact Mapping → contact-map.md (3–5 contacts per account)  ┐ run together
  └── AGENT-14 Persona Intelligence → persona-brief per contact              ┘

PHASE VI — MESSAGING & SEQUENCE STRATEGY (per contact)
  └── AGENT-15 Cold Email Copywriter → 3 email variants (≤75w each)
  └── AGENT-16 Sequence Design → 4-touch sequence per contact

PHASE VII — LAUNCH, TRACKING & ANALYSIS (always on)
  └── AGENT-17 Feedback Loop → reply rate by signal report → feeds AGENT-00
  └── AGENT-18 Objection & Reactivation → reactivation triggers + copy
  └── AGENT-19 Analytics & Metrics → program metrics + System Health Score
  └── AGENT-20 Deal Acceleration → 7-day deal playbook (activates at first meeting)
  └── AGENT-21 Prospecting → net-new account candidates → feeds back to Phase I

PHASE VIII — PRODUCTION IMPLEMENTATION  [NEW]
  └── AGENT-22 Workflow Auditor → QA log — missing files, broken handoffs
  └── AGENT-23 Export & Delivery → CRM/SE platform export, field-mapped

PHASE IX — ONGOING SIGNAL MONITORING  [NEW]
  └── AGENT-24 Signal Monitor → batch signal update, expired windows, re-research triggers
  └── AGENT-25 Program Calibration → quarterly methodology audit, updated signal weights

HVO (runs independently)
  └── AGENT-26 HVO Pitch Builder → prospect-specific SI one-pager + HVO brief  [NEW]
```

---

## Conviction Tiers

| Tier | Criteria | Action |
|---|---|---|
| **HIGH** | 3+ confirmed signals, clean fit-locus, no hard barriers | Advance to Phase IV (dossier queue) |
| **HIGH*** | 3+ confirmed signals, provisional fit-locus | Flag for human review before dossier |
| **MEDIUM** | 2 confirmed signals, OR 3+ with mismatched locus or soft barrier | Sprint queue |
| **LOW** | 1 confirmed signal | Monitor queue |
| **DEPRIORITIZED** | Valid target, barrier or timing blocks it | AGENT-18 — monitor for reactivation |
| **FLAG** | Structural block today | AGENT-18 — hard barrier, needs clear-condition |

---

## Signal Codes

Signal codes and definitions live in each client's `client-profile.yaml`.
Format: `[CATEGORY]-[NUMBER]` (e.g., PJ-01 = Project signal #1).

Standard categories:
- **PJ** — Project (capital projects, expansions, facility investments)
- **LS** — Leadership (new hires, role changes, org shifts)
- **GR** — Growth (revenue signals, expansion news, acquisitions)
- **RC** — Regulatory / Compliance (permits, filings, certifications)
- **FN** — Financial (earnings, capex, budget signals)
- **SU** — Sustainability / ESG (reports, commitments, certifications)
- **CT** — Competitive (incumbent changes, RFP activity)
- **DX** — Distraction (signals that disqualify — FLAG or STOP)

---

## Client Configs

| Client | Config | Status |
|---|---|---|
| ISCO Industries | `skills/client/isco/client-profile.yaml` | v0.5 — gaps pending discovery session |
| Lazer Logistics | `skills/client/lazer/client-profile.yaml` | Active |

---

## Agent Quality Standard

An agent is done when it has all of the following:
- [ ] Identity declaration in first person
- [ ] Authority section (what it can AND cannot do)
- [ ] Inputs clearly defined with file names
- [ ] Process steps that Claude can follow exactly
- [ ] Output artifact with format specified
- [ ] Checkpoint before handoff (what must be true)
- [ ] Handoff declaration (which agent runs next, what to pass)
- [ ] At least one example run on a real account

**Dan's rule:** *"If you can't fully explain it, you probably shouldn't be doing it."*
Each agent must be explainable in one sentence before it's considered done.
