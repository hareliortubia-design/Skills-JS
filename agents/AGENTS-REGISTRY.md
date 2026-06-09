# JumpSeat Signal Intelligence — Agents Registry
**Version:** 2.0 | **Maintained by:** Hareli Ortubia | **Last updated:** June 2026
**Program:** JumpSeat Signal Intelligence (White Paper v2.0, May 2026)

---

## What is an agent?

An agent is a specialized role that Claude adopts to do a specific job in the pipeline. Each agent has an identity, a defined scope of authority, a set of inputs it reads, a specific artifact it produces, and a checkpoint it enforces before handing off to the next.

A skill tells Claude *what to do*. An agent tells Claude *who to be, with what authority, at what checkpoint*.

```
Activate an agent by saying: "Activate AGENT-[XX] — load [client] client-profile.yaml"
The agent adopts its identity and runs until it produces its defined output and declares the handoff.
```

---

## The 19 Agents

| # | Agent | Phase | Mode | Input | Output | File |
|---|---|---|---|---|---|---|
| 00 | Memory & Learning | Always on | Session-start + end | Previous sessions | methodology-log.md | [agent-00-memory.md](base/agent-00-memory.md) |
| 01 | Foundation | Setup | Session-start | client-profile.yaml | Session frame + routing | [agent-01-foundation.md](base/agent-01-foundation.md) |
| 02 | Research | Research | Per account | Company name + URL | research-brief.json | [agent-02-research.md](base/agent-02-research.md) |
| 03 | Account Fit | Funnel | Per account | research-brief.json | icp-fit-scoring.json | [agent-03-account-fit.md](base/agent-03-account-fit.md) |
| 04 | Signal Hunter | Signal | Per account | research-brief.json + signals | signal-research.json | [agent-04-signal-hunter.md](base/agent-04-signal-hunter.md) |
| 05 | Signal Implication | Signal | Per account | signal-research.json | signal-research.json (enriched) | [agent-05-signal-implication.md](base/agent-05-signal-implication.md) |
| 06 | Prioritization & Stacking | Ranking | Per account | signal-research.json (enriched + timed) | conviction-output.json | [agent-06-prioritization-stacking.md](base/agent-06-prioritization-stacking.md) |
| 07 | Contact Mapping | Activation | HIGH accounts only | conviction-output.json + dossier | contact-map.md | [agent-07-contact-mapping.md](base/agent-07-contact-mapping.md) |
| 08 | Timing | Always on | Before AGENT-06 | signal-research.json | Perishability audit (added to signal-research.json) | [agent-08-timing.md](base/agent-08-timing.md) |
| 09 | Messaging Strategist | Activation | HIGH accounts only | conviction + signals + contacts | dossier-[company].md + messaging-brief | [agent-09-messaging-strategist.md](base/agent-09-messaging-strategist.md) |
| 10 | Cold Email Copywriter | Outreach | Per contact | messaging-brief + persona-brief | emails-[company].md | [agent-10-cold-email-copywriter.md](base/agent-10-cold-email-copywriter.md) |
| 11 | Feedback Loop | Always on | After each sprint | All conviction outputs | coverage-audit.md | [agent-11-feedback-loop.md](base/agent-11-feedback-loop.md) |
| 12 | Objection & Reactivation | Always on | DEPRIORITIZED accounts | conviction outputs (D/F tier) | reactivation-queue.md | [agent-12-objection-reactivation.md](base/agent-12-objection-reactivation.md) |
| 13 | Deal Acceleration | Pipeline | After first SQL | Active opportunities | Pre-call brief + follow-up | [agent-13-deal-acceleration.md](base/agent-13-deal-acceleration.md) |
| 14 | Persona | Activation | Alongside AGENT-07 | contact-map.md + signals | persona-brief-[company].md | [agent-14-persona.md](base/agent-14-persona.md) |
| 15 | Sequence Design | Outreach | Per contact | emails + persona-brief | sequence-[company]-[contact].md | [agent-15-sequence-design.md](base/agent-15-sequence-design.md) |
| 16 | Analytics | Always on | After outreach activates | conviction outputs + reply data | crm-export.csv + program-metrics.md | [agent-16-analytics.md](base/agent-16-analytics.md) |
| 17 | Prospecting | Always on | Between sprints | ICP criteria + coverage audit | prospecting-candidates.md | [agent-17-prospecting.md](base/agent-17-prospecting.md) |

---

## Pipeline Flow

```
SESSION START
  └── AGENT-00 Memory & Learning (loads session, surfaces past learnings)
  └── AGENT-01 Foundation (loads client frame, routes to correct stage)

STAGE 1 — RESEARCH (per account)
  └── AGENT-02 Research → research-brief.json
  └── AGENT-03 Account Fit → icp-fit-scoring.json
        → NOT ICP: STOP → log to AGENT-12 if barriers may resolve
        → Tier 1 / Tier 2: ADVANCE

STAGE 2 — SIGNAL (per qualified account)
  └── AGENT-04 Signal Hunter → signal matrix C/I/N/U
  └── AGENT-05 Signal Implication → so-what + fit-locus
  └── AGENT-08 Timing → perishability check (stale signals → S)
  └── AGENT-06 Prioritization & Stacking → conviction-output.json
        → HIGH: ADVANCE to dossier queue
        → MEDIUM/LOW: sprint queue
        → DEPRIORITIZED/FLAG: AGENT-12

STAGE 3 — ACTIVATION (HIGH accounts only)
  └── AGENT-07 Contact Mapping → contact-map.md  ┐ run together
  └── AGENT-14 Persona → persona-brief.md         ┘
  └── AGENT-09 Messaging Strategist → dossier + messaging-brief

STAGE 4 — OUTREACH (per contact)
  └── AGENT-10 Cold Email Copywriter → emails (Email 1 + 2)
  └── AGENT-15 Sequence Design → 4-touch sequence

STAGE 5 — PIPELINE (when SQLs exist)
  └── AGENT-13 Deal Acceleration [OUT OF SCOPE — activates at first meeting]

ALWAYS ON (every sprint)
  └── AGENT-08 Timing → runs before AGENT-06 on every account
  └── AGENT-11 Feedback Loop → runs after each sprint batch
  └── AGENT-12 Objection & Reactivation → monitors deprioritized accounts
  └── AGENT-16 Analytics → CRM export + program metrics
  └── AGENT-17 Prospecting → surfaces new accounts between sprints
  └── AGENT-00 Memory & Learning → captures learnings at session end
```

---

## Conviction Tiers

| Tier | Criteria | Action |
|---|---|---|
| **HIGH** | 3+ confirmed signals, clean fit-locus, no hard barriers | Advance to dossier queue |
| **HIGH*** | 3+ confirmed signals, provisional fit-locus | Flag for human review before dossier |
| **MEDIUM** | 2 confirmed signals, OR 3+ with mismatched locus or soft barrier | Sprint queue |
| **LOW** | 1 confirmed signal | Monitor queue |
| **DEPRIORITIZED** | Valid target, barrier or timing blocks it | AGENT-12 — monitor for reactivation |
| **FLAG** | Structural block today | AGENT-12 — hard barrier, needs clear-condition |

---

## Signal Codes

Signal codes and definitions live in each client's `client-profile.yaml`.
The code format is: `[CATEGORY]-[NUMBER]` (e.g., PJ-01 = Project signal #1).

Standard categories used across all clients:
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

Each client has a `client-profile.yaml` in `skills/client/[client]/`.
This file is the foundation every agent reads. It never lives in the GitHub repo.

| Client | Config | Status |
|---|---|---|
| ISCO Industries | `skills/client/isco/client-profile.yaml` | v0.5 — 8 gaps pending discovery |
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
