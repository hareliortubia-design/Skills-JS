# JumpSeat Skills Inventory
**Maintained by:** Hareli Ortubia | **Last updated:** June 2026
**Program:** JumpSeat Signal Intelligence Agent OS v3.0 (27 agents, 00–26)

---

## What is a skill?

A skill is a set of instructions that defines **what an agent does, how it does it, and with what parameters.**
The agent executes the work. The skill is the guide.

```
Skill → Agent → Artifact → next Skill → next Agent → next Artifact
```

There are two types:

| Type | Description | Who uses it |
|------|-------------|-------------|
| **Base skill** | Generic — same logic for every client, parameters vary | JumpSeat team (internal) |
| **Client skill** | Client-specific version of a base skill, with ICP, signals, and tone baked in | Client runs it in their own Claude environment (deliverable) |

---

## Full Pipeline Flow (v3.0)

```
INPUT: Client brief + account list
       ↓
┌──────────────────────────────────────────────────────────────────────┐
│ ONBOARDING — New client only                                         │
│ Skills: skill-jumpseat-kickoff → skill-icp-onboarding               │
│ Agents: 00 Memory + 01 Foundation                                    │
│ Artifact: client-profile.yaml (ICP, signals, scoring parameters)    │
└──────────────────────────────┬───────────────────────────────────────┘
                               ↓
┌──────────────────────────────────────────────────────────────────────┐
│ PHASE I — RESEARCH (per account)                                     │
│ Skill: skill-deep-research                                           │
│ Agent: 02 Research                                                   │
│ Artifact: research-brief.json                                        │
│           ↓                                                          │
│ Skill: skill-icp-fit-scoring                                         │
│ Agent: 03 Account Fit                                                │
│ Artifact: icp-fit-scoring.json (Tier 1 / Tier 2 / Not ICP)         │
│ → Not ICP: STOP. Route to 18 Objection & Reactivation.              │
└──────────────────────────────┬───────────────────────────────────────┘
                               ↓ (Tier 1 + Tier 2 only)
┌──────────────────────────────────────────────────────────────────────┐
│ PHASE II — SIGNAL PHASE (per qualified account)                     │
│ Skill: skill-signal-hunter (signal library workshop — runs once)    │
│ Agent: 04 Signal Hunter                                              │
│ Artifact: signal-library.md (30–50 candidates → client workshop)    │
│           ↓                                                          │
│ Skill: skill-signal-research                                         │
│ Agents: 04 Signal Hunter → 06 Signal Implication →                  │
│          05 Timing → 07 Conviction Scoring → 08 Prioritization       │
│ Artifact: signal-research.json (C/I/N/U + implications + timing)    │
│           ↓                                                          │
│ Skill: skill-conviction-algorithm                                    │
│ Agents: 07 Conviction Scoring + 08 Prioritization & Stacking        │
│ Artifact: conviction-output.json (score + level + stacking narrative)│
└──────────────────────────────┬───────────────────────────────────────┘
                               ↓
┌──────────────────────────────────────────────────────────────────────┐
│ PHASE III — SIGNAL QA & ACCOUNT LIST  [NEW]                         │
│ Skill: skill-signal-validation                                       │
│ Agents: 09 Signal QA → 10 Account List                              │
│ Artifact: signal-qa-[company].md (first client artifact)            │
│           sqal-[client]-[sprint].md (SQAL)                          │
│ → HIGH: ADVANCE to Phase IV                                          │
│ → MEDIUM: sprint queue                                               │
│ → LOW / DEPRIORITIZED / FLAG: monitor queue                          │
└──────────────────────────────┬───────────────────────────────────────┘
                               ↓ (HIGH accounts only)
┌──────────────────────────────────────────────────────────────────────┐
│ PHASE IV — DEEP RESEARCH / ACCOUNT DOSSIERS                         │
│ Skill: skill-dossier-builder                                         │
│ Agents: 11 Account Narrative + 12 Messaging Strategist              │
│ Artifact: account-narrative-[company].md + dossier-[company].md     │
└──────────────────────────────┬───────────────────────────────────────┘
                               ↓
┌──────────────────────────────────────────────────────────────────────┐
│ PHASE V — CONTACTS & PERSONAS                                        │
│ Skill: skill-contact-mapping                                         │
│ Agents: 13 Contact Mapping + 14 Persona Intelligence (run together) │
│ Artifact: contact-map-[company].md + persona-brief-[company].md     │
└──────────────────────────────┬───────────────────────────────────────┘
                               ↓
┌──────────────────────────────────────────────────────────────────────┐
│ PHASE VI — MESSAGING & SEQUENCE                                      │
│ Skill: skill-email-personalization                                   │
│ Agents: 15 Cold Email Copywriter + 16 Sequence Design               │
│ Artifact: emails-[company].md (3 variants) + sequence per contact   │
└──────────────────────────────┬───────────────────────────────────────┘
                               ↓
┌──────────────────────────────────────────────────────────────────────┐
│ PHASE VIII — PRODUCTION IMPLEMENTATION  [NEW]                        │
│ Skill: skill-qa-export                                               │
│ Agents: 22 Workflow Auditor → 23 Export & Delivery                  │
│ Artifact: qa-log + CRM export CSV + SE platform export              │
└──────────────────────────────────────────────────────────────────────┘

ALWAYS ON (every sprint)
  17 Feedback Loop → reply rate by signal → feeds 00 Memory
  18 Objection & Reactivation → DEPRIORITIZED accounts
  19 Analytics & Metrics → program metrics + System Health Score
  20 Deal Acceleration → active opportunities (post first meeting)
  21 Prospecting → net-new account candidates → feeds back to Phase I
  24 Signal Monitor → batch signal refresh (Phase IX)
  25 Program Calibration → quarterly methodology audit (Phase IX)

HVO (runs independently — not in the sprint pipeline)
  Skill: skill-hvo-pitch
  Agent: 26 HVO Pitch Builder
  Artifact: SI one-pager + HVO brief for prospect sales conversation
```

---

## Skills Status Table

### Base Skills (reutilizable across all clients)

All base skills read from `client-profile.yaml` at runtime. No client context is hardcoded.

| # | Skill File | AgentOS Name | Agents | Input | Artifact | Status |
|---|---|---|---|---|---|---|
| 0 | [skill-jumpseat-kickoff.md](base/skill-jumpseat-kickoff.md) | si-context-load (partial) | 00 + 01 | Nothing — surveys state | Route to correct skill | ✅ Built |
| 1 | [skill-icp-onboarding.md](base/skill-icp-onboarding.md) | si-context-load (partial) | 00 + 01 | Client website + closed-won data | `client-profile.yaml` | ✅ Built |
| 2 | [skill-deep-research.md](base/skill-deep-research.md) | si-account-enrichment | 02 Research | Company name + URL | `research-brief.json` | ✅ v2.0 |
| 3 | [skill-icp-fit-scoring.md](base/skill-icp-fit-scoring.md) | si-icp-fit-scoring | 03 Account Fit | `research-brief.json` | Fit Tier + Fit Score | ✅ Built |
| 4 | [skill-signal-hunter.md](base/skill-signal-hunter.md) | — | 04 Signal Hunter | `client-profile.yaml` | `signal-library.md` (workshop) | ✅ Built |
| 5 | [skill-signal-research.md](base/skill-signal-research.md) | si-signal-research | 04 → 06 → 05 → 07 → 08 | `research-brief.json` + `signal-library.md` | `signal-research.json` (C/I/N/U + implications + timing) | ✅ v2.0 |
| 6 | [skill-conviction-algorithm.md](base/skill-conviction-algorithm.md) | si-conviction-scoring | 07 + 08 | `signal-research.json` | `conviction-output.json` (score + level + stacking narrative) | ✅ Built |
| 7 | [skill-dossier-builder.md](base/skill-dossier-builder.md) | si-dossier-generation | 11 + 12 | `conviction-output.json` | `account-narrative.md` + `dossier-[company].md` | ✅ Built |
| 8 | [skill-contact-mapping.md](base/skill-contact-mapping.md) | si-stakeholder-mapping | 13 + 14 | Dossier + `client-profile.yaml` | `contact-map.md` + `persona-brief.md` | ✅ Built |
| 9 | [skill-email-personalization.md](base/skill-email-personalization.md) | si-outreach-generation | 15 + 16 | Dossier + contact + persona | Email ×3 variants + 4-touch sequence | ✅ Built |
| 10 | [skill-salesforce-export.md](base/skill-salesforce-export.md) | — (legacy) | 23 Export & Delivery | Signal matrix array | CRM-ready CSV | ✅ Built (legacy — superseded by skill-qa-export for new sprints) |
| 11 | [skill-signal-validation.md](base/skill-signal-validation.md) | si-signal-validation | 09 + 10 | `signal-research.json` + sources | `signal-qa-[company].md` + SQAL | ✅ Built |
| 12 | [skill-qa-export.md](base/skill-qa-export.md) | si-qa-and-export | 22 + 23 | All sprint output files | QA log + CRM export + SE export + delivery summary | ✅ Built |
| 13 | [skill-hvo-pitch.md](base/skill-hvo-pitch.md) | si-hvo-pitch | 26 | Prospect name + vertical + ICP | SI one-pager + HVO brief + vertical signal examples | ✅ Built |

### Client Configs

Each client has a folder with their specific parameters. Skills read these at runtime — client context never goes in the skill files.

| Client | Config File | Signal Library | Status |
|--------|-------------|----------------|--------|
| ISCO Industries | `client/isco/client-profile.yaml` | Pending — discovery session in progress | In progress |
| Lazer Logistics | `client/lazer/client-profile.yaml` | ✅ Active — 15 signals, 7 categories | Active |

---

## Skill-to-Agent Map

Complete mapping so any team member can navigate between the two registries:

| Phase | Agents | Skill |
|---|---|---|
| Onboarding | 00 Memory + 01 Foundation | skill-jumpseat-kickoff + skill-icp-onboarding |
| Phase I — Research | 02 Research | skill-deep-research |
| Phase I — Fit Gate | 03 Account Fit | skill-icp-fit-scoring |
| Phase II — Signal Library | 04 Signal Hunter | skill-signal-hunter |
| Phase II — Signal Research | 04 → 06 → 05 → 07 → 08 | skill-signal-research + skill-conviction-algorithm |
| Phase III — QA & List | 09 Signal QA + 10 Account List | skill-signal-validation |
| Phase IV — Dossier | 11 Account Narrative + 12 Messaging Strategist | skill-dossier-builder |
| Phase V — Contacts | 13 Contact Mapping + 14 Persona Intelligence | skill-contact-mapping |
| Phase VI — Outreach | 15 Cold Email + 16 Sequence Design | skill-email-personalization |
| Phase VII — Always on | 17 Feedback + 18 Reactivation + 19 Analytics + 20 Deal + 21 Prospecting | prompt only |
| Phase VIII — Export | 22 Workflow Auditor + 23 Export & Delivery | skill-qa-export |
| Phase IX — Monitoring | 24 Signal Monitor + 25 Program Calibration | prompt only (24 reads skill-signal-research) |
| HVO | 26 HVO Pitch Builder | skill-hvo-pitch |

---

## What makes a skill reutilizable vs. client-specific?

| Component | Reutilizable | Client-specific |
|-----------|-------------|-----------------|
| Research pipeline (web crawl, SEC, LinkedIn) | ✅ Same for all | — |
| ICP scoring criteria (revenue, size, vertical) | Base logic same | Parameters change per client |
| Signal list | Base structure same | Signal definitions change per industry |
| Signal weights + conviction algorithm | Base formula same | Weights change per client |
| Dossier sections + format | Template same | Narrative tone + sales context change |
| Email rules (75w/35w, no pitch, peer-to-peer) | ✅ Same for all | Lead signal + first-line observation change |
| CRM field mapping | Base mapping same | Custom field names change per CRM instance |

---

## Skill quality standard (definition of done)

A skill is **done** when it has all of the following:

- [ ] One-sentence description of what it does
- [ ] Input clearly defined (what it receives)
- [ ] Output clearly defined (what artifact it produces)
- [ ] Which agent(s) run it (with correct v3.0 numbers)
- [ ] Which skill it feeds next in the pipeline
- [ ] At least one example run (tested on a real account)
- [ ] Source validation rules (no inferred = confirmed, always cite source)
- [ ] Reutilizable vs. client-specific components labeled

---

## Next priorities (skills to build)

All 14 base skills are complete as of June 2026. ✅

| Priority | Skill | Status |
|---|---|---|
| 1 | ~~skill-signal-validation~~ | ✅ Done |
| 2 | ~~skill-qa-export~~ | ✅ Done |
| 3 | ~~skill-hvo-pitch~~ | ✅ Done |

**Dan's rule:** *"If you can't fully explain it, you probably shouldn't be doing it."*
Each skill must be explainable in one sentence before it's considered done.
