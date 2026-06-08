# JumpSeat Skills Inventory
**Maintained by:** Hareli Ortubia | **Last updated:** June 2026  
**Program:** JumpSeat Signal Intelligence Agent OS

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

## Full Pipeline Flow

```
INPUT: Company name + website URL
       ↓
┌─────────────────────────────────────────────────────────────────┐
│ STAGE 1 — RESEARCH                                              │
│ Skill: Deep Research (MODE R)                                   │
│ Agent: 03 Research                                              │
│ Artifact: Research Brief JSON                                   │
│ (web crawl, LinkedIn, OSHA, news, job postings)                 │
└──────────────────────────┬──────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────────┐
│ STAGE 2 — ICP FIT GATE                                          │
│ Skill: ICP Fit Scoring (MODE 0)                                 │
│ Agent: 04 Account Fit Review                                    │
│ Artifact: Fit Tier (Tier 1 / Tier 2 / Not ICP) + Fit Score     │
│ → Not ICP: STOP. Do not invest in signal research.              │
└──────────────────────────┬──────────────────────────────────────┘
                           ↓ (Tier 1 + Tier 2 only)
┌─────────────────────────────────────────────────────────────────┐
│ STAGE 3 — SIGNAL RESEARCH                                       │
│ Skill: Signal Research (MODE A)                                 │
│ Agents: 05 Signal Hunter → 06 Signal Implication               │
│         → 07 Signal Conviction → 08 Prioritization & Stacking  │
│ Artifact: 15 signals C/I/N/U + Conviction Score + Weighted Score│
│ → CRITICAL/HIGH → Stage 4                                       │
│ → MEDIUM → queue for next sprint                                │
│ → LOW/PENDING → monitor queue                                   │
└──────────────────────────┬──────────────────────────────────────┘
                           ↓ (HIGH + CRITICAL only)
┌─────────────────────────────────────────────────────────────────┐
│ STAGE 4 — DOSSIER                                               │
│ Skill: Dossier Builder (MODE B)                                 │
│ Agents: 03 Research + 09 Contact Mapping + 16 Persona Intel     │
│ Artifact: 4–5 page intelligence dossier (sales rep reads first) │
└──────────────────────────┬──────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────────┐
│ STAGE 5 — OUTREACH                                              │
│ Skill: Email Personalization (MODE C)                           │
│ Agent: 12 Cold Email Copywriter                                 │
│ Artifact: Email 1 (≤75w) + Email 2 (≤35w)                      │
└──────────────────────────┬──────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────────┐
│ STAGE 6 — CRM EXPORT                                            │
│ Skill: Salesforce Export (MODE D)                               │
│ Agent: 18 Analytics & Metrics                                   │
│ Artifact: CSV for Salesforce import (future: direct API write)  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Skills Status Table

### Base Skills (reutilizable across all clients)

All base skills read from `client-profile.yaml` at runtime. No client context is hardcoded.

| # | Skill | JumpSeat Agent(s) | Input | Artifact | Status | File |
|---|-------|------------------|-------|----------|--------|------|
| 0 | JumpSeat Kickoff | All (orchestrator) | Nothing — surveys state | Route to next skill | ✅ Built | [skill-jumpseat-kickoff.md](base/skill-jumpseat-kickoff.md) |
| 1 | ICP Onboarding | 01 Memory + 02 Foundation | Client website + closed-won accounts | `client-profile.yaml` | ✅ Built | [skill-icp-onboarding.md](base/skill-icp-onboarding.md) |
| 2 | Signal Hunter | 05 Signal Hunter | `client-profile.yaml` | `signal-library.md` (30–50 candidates → workshop) | ✅ Built | [skill-signal-hunter.md](base/skill-signal-hunter.md) |
| 3 | Deep Research | 03 Research | Company name + URL + `client-profile.yaml` | `research-brief.json` | ✅ v2.0 — JobSpy + edgartools + jina | [skill-deep-research.md](base/skill-deep-research.md) |
| 4 | ICP Fit Scoring | 04 Account Fit Review | `research-brief.json` + `client-profile.yaml` | Fit Tier + Fit Score | ✅ Built | [skill-icp-fit-scoring.md](base/skill-icp-fit-scoring.md) |
| 5 | Signal Research | 05 → 06 → 07 → 08 | `research-brief.json` + `signal-library.md` + `client-profile.yaml` | Signal matrix C/I/N/U | ✅ v2.0 — reads config, source hierarchy defined | [skill-signal-research.md](base/skill-signal-research.md) |
| 6 | Conviction Algorithm | 07 + 08 | Signal matrix + `client-profile.yaml` | `conviction-output.json` (full scoring breakdown) | ✅ Built | [skill-conviction-algorithm.md](base/skill-conviction-algorithm.md) |
| 7 | Dossier Builder | 03 + 09 + 16 | `conviction-output.json` | Intelligence dossier (4–5 pages) | ✅ Built | [skill-dossier-builder.md](base/skill-dossier-builder.md) |
| 8 | Contact Mapping | 09 Contact Mapping | Dossier + `client-profile.yaml` | `contact-map.md` (who to call, why, in what order) | ✅ Built | [skill-contact-mapping.md](base/skill-contact-mapping.md) |
| 9 | Email Personalization | 12 Cold Email Copywriter | Dossier + contact | Email 1 (≤75w) + Email 2 (≤35w) | ✅ Built | [skill-email-personalization.md](base/skill-email-personalization.md) |
| 10 | CRM Export | 18 Analytics & Metrics | Signal matrix array + `client-profile.yaml` | CRM-ready CSV | ✅ Built | [skill-salesforce-export.md](base/skill-salesforce-export.md) |
| 11 | Batch Signal Research | 05 → 08 (parallel) | 100+ accounts via subagent pattern | Batch signal matrices | 🔴 Phase 2 | — |
| 12 | Signal Re-verification | 05 Signal Hunter | Stale signal flags | Updated signal matrices | 🔴 Phase 2 | — |
| 13 | Reactivation | 10 Timing Agent | PENDING accounts + trigger conditions | Reactivated accounts → MEDIUM/HIGH | 🔴 Phase 2 | — |

### Client Configs

Each client has a folder with their specific config. Skills read these at runtime.

| Client | Config | Signal Library | Status |
|--------|--------|----------------|--------|
| Lazer Logistics | ✅ [client-profile.yaml](client/lazer/client-profile.yaml) | ✅ In monolith (needs extraction to signal-library.md) | Active |
| ISCO | ✅ [client-profile.yaml](client/isco/client-profile.yaml) (template) | 🔴 Pending — run signal-hunter once ICP onboarding complete | Pending docs from Dan |

---

## What makes a skill reutilizable vs. client-specific?

| Component | Reutilizable | Client-specific |
|-----------|-------------|-----------------|
| Research pipeline (web crawl, OSHA, LinkedIn) | ✅ Same for all | — |
| ICP scoring criteria (revenue, size, vertical) | Base logic same | Parameters change per client |
| Signal list (15 signals for Lazer) | Base structure same | Signal definitions change per industry |
| Signal weights + conviction algorithm | Base formula same | Weights change per client |
| Dossier sections + format | Template same | Narrative tone + sales context change |
| Email rules (75w/35w, no pitch, peer-to-peer) | ✅ Same for all | Lead signal + first-line observation change |
| Salesforce field mapping | Base mapping same | Custom field names change per SF instance |

---

## Skills Dan mentioned wanting (from June 2026 meetings)

From the Lazer program review + skill demo meeting:

1. **ICP Onboarding skill** — conversational intake for any new client → ✅ built as `skill-icp-onboarding.md`; produces `client-profile.yaml` + calibrated `icp-prompt.md`
2. **ICP Fit Scoring skill** — parameterized so any client's ICP can be loaded in → ✅ built as `skill-icp-fit-scoring.md`
3. **Email Personalization skill** — one deliverable per client; BD rep runs it in their own Claude → ✅ built as `skill-email-personalization.md`
4. **Signal Hunter (generic)** — give it the ICP and it proposes signals; client approves in workshop → 🔴 needs to be built
5. **Conviction Algorithm (standalone)** — currently embedded in Mode A, Dan wants it visible and explainable → 🟡 needs to be extracted + documented
6. **Contact Mapping** — who to call, why, in what order → 🔴 needs to be built (Agent 09)

**Dan's rule:** *"If you can't fully explain it, you probably shouldn't be doing it."*  
Each skill needs to be explainable in one sentence before it's considered done.

---

## Skill quality standard (definition of done)

A skill is **done** when it has all of the following:

- [ ] One-sentence description of what it does
- [ ] Input clearly defined (what it receives)
- [ ] Output clearly defined (what artifact it produces)
- [ ] Which JumpSeat agent runs it
- [ ] What skill it feeds next in the pipeline
- [ ] At least one example run (tested on a real account)
- [ ] Source validation rules (no inferred = confirmed, always cite source)
- [ ] Reutilizable vs. client-specific components labeled

---

## Next priorities

1. Extract base skill files from the Lazer skill (already done conceptually — needs clean standalone docs)
2. Build **Signal Hunter (generic)** — needed for ISCO onboarding
3. Build **Contact Mapping** — Agent 09 is unbuilt
4. Document **Conviction Algorithm** as standalone — Dan wants it explainable
5. Create `client/isco/` skill files once Dan sends discovery docs
