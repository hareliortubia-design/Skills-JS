# Skill: QA & Export
**AgentOS Name:** si-qa-and-export
**JumpSeat Agents:** 22 Workflow Auditor → 23 Export & Delivery
**Type:** Base — works for any client
**Version:** 1.0 | June 2026

> Runs at the end of every sprint. Audits the full pipeline for completeness, then packages every cleared account into the formats the client and the team actually use: Google Sheets tracker, CRM export, and SE platform export.

---

## Input
- All sprint output files — one folder per account, containing all artifacts from Phase I–VI
- `sqal-[client]-[sprint].md` — to know which accounts are HIGH (full pipeline required) vs MEDIUM/LOW
- `agents/AGENTS-REGISTRY.md` — required artifacts per agent, per tier
- `client-profile.yaml` → CRM field mapping, SE platform format, tracker column definitions

## Output / Artifact
- `qa-log-[client]-sprint[N].md` — Workflow audit: PASS / FAIL / SKIP per account + missing artifact list + known breakpoints
- `crm-export-[client]-sprint[N].csv` — Field-mapped, ready for CRM import. One row per contact.
- `se-export-[client]-sprint[N].csv` — SE platform export (if applicable). One row per contact per step.
- `delivery-[client]-sprint[N].md` — Delivery summary: what's in the package, how to use it, what's blocked and why.

## Feeds into
- Client and rep workflow (final delivery)
- Agent 17 Feedback Loop — sprint is now live, tracking begins
- Agent 00 Memory & Learning — session close

---

## Two-step process

### Step 1 — Workflow Audit (Agent 22)

Checks every account in the sprint against the required artifact checklist:

**HIGH accounts need:** research-brief + icp-fit-scoring + signal-research (pre and post QA) + conviction-output + signal-qa report + SQAL entry + account-narrative + dossier + contact-map + persona-brief + emails (3 variants) + sequence

**MEDIUM accounts need:** research-brief + icp-fit-scoring + signal-research + conviction-output + signal-qa report + SQAL entry

**Result:** Each account gets PASS (all artifacts present) / FAIL (gaps found) / SKIP (documented exception).

Only PASS accounts advance to export.

### Step 2 — Export & Delivery (Agent 23)

Takes every PASS account and produces three deliverables:

1. **Tracker update** — Google Sheets row per account: company, tier, conviction score, confirmed signals, lead signal, dossier link, contact, sequence status, QA status, export date
2. **CRM export** — CSV field-mapped to client's CRM. One row per contact. Client imports directly.
3. **SE platform export** — Contact + email steps, SE-platform-ready (if applicable)

---

## What this skill replaces

`skill-salesforce-export.md` covers Salesforce-only CRM export (legacy, still valid for Lazer).
`skill-qa-export.md` is the full Phase VIII implementation: audit first, then multi-format export for any client.

New sprints use `skill-qa-export`. Legacy Lazer export can still use `skill-salesforce-export` until migrated.

---

## Rule
The QA log is mandatory — never export without it. Exporting an account with a missing dossier or missing emails produces a half-baked delivery that damages client trust. The audit is what makes the export trustworthy.

---

## How to run

### Part 1 — Workflow Audit (Agent 22)

1. Read `sqal-[client]-[sprint].md` — load every account and its tier (HIGH / MEDIUM / LOW / DEPRIORITIZED / FLAG)
2. Read `agents/AGENTS-REGISTRY.md` — confirm required artifact list per agent
3. Read `client-profile.yaml` — confirm which phases were in scope for this sprint
4. For each account: check every required artifact against the tier checklist
5. Run the **Workflow Audit prompt** below
6. Save output as `qa-log-[client]-sprint[N].md`

### Part 2 — Export & Delivery (Agent 23)

1. Read `qa-log-[client]-sprint[N].md` — load the cleared account list
2. Read `client-profile.yaml` → `crm_field_mapping` + SE platform format + tracker column definitions
3. Collect all sprint artifacts for cleared accounts only
4. Run the **Export & Delivery prompt** below
5. Save outputs: `crm-export-[client]-sprint[N].csv`, `se-export-[client]-sprint[N].csv` (if applicable), `delivery-[client]-sprint[N].md`

---

## Prompt — Part 1: Workflow Audit

```
You are the Workflow Auditor for the JumpSeat Signal Intelligence program.
You run at the end of every sprint to verify that the pipeline produced complete, consistent,
handoff-ready outputs for every account.
You do not judge the quality of the research. You audit whether everything that was supposed to happen actually happened.

SPRINT SCOPE — load tier assignments:
[PASTE sqal-[client]-[sprint].md — full account list with tier per account]

SPRINT ARTIFACTS — folder structure available:
[LIST every file present in the sprint output folder, one per line]

CLIENT PROFILE — in-scope phases:
[PASTE client-profile.yaml — phases_in_scope section]

Today's date: [TODAY'S DATE]

---

Run the following audit steps. Show your work for every account.

STEP 1 — LOAD ACCOUNT LIST
From the SQAL, extract: every account name and its tier (HIGH / MEDIUM / LOW / DEPRIORITIZED / FLAG).
This is your audit scope.

STEP 2 — CHECK REQUIRED ARTIFACTS PER ACCOUNT
For each account, verify the following artifacts exist, based on tier:

HIGH accounts require:
  Phase I   | Agent 02 | research-brief.json
  Phase I   | Agent 03 | icp-fit-scoring.json
  Phase II  | Agent 04 | signal-research.json (pre-QA)
  Phase II  | Agents 05+06 | Timing flags + implication notes inside signal-research.json
  Phase II  | Agent 07 | conviction-output.json
  Phase II  | Agent 08 | Ranked signal stack inside conviction-output.json
  Phase III | Agent 09 | signal-qa-[company].md
  Phase III | Agent 10 | Entry in sqal-[client]-[sprint].md
  Phase IV  | Agent 11 | account-narrative-[company].md
  Phase IV  | Agent 12 | dossier-[company].md
  Phase V   | Agent 13 | contact-map-[company].md
  Phase V   | Agent 14 | persona-brief-[company].md
  Phase VI  | Agent 15 | emails-[company].md (must contain 3 variants)
  Phase VI  | Agent 16 | sequence-[company]-[contact].md

MEDIUM accounts require: Phase I–III only (research-brief + icp-fit-scoring + signal-research + conviction-output + signal-qa + SQAL entry)

LOW / DEPRIORITIZED / FLAG accounts require: research-brief.json + SQAL entry only

For each artifact: mark ✅ PASS / ❌ FAIL / ⏭️ SKIP (SKIP is valid only when a documented reason exists)

STEP 3 — FLAG BROKEN HANDOFFS
A broken handoff is:
  - An artifact exists but is in the wrong format (e.g., conviction-output.json missing the ranked signal stack)
  - An artifact references a file that doesn't exist (e.g., dossier references a contact-map that's missing)
  - A downstream artifact was created without its upstream artifact existing (stage skipped silently)

For each broken handoff: name the artifact pair, explain what's missing.

STEP 4 — DOCUMENT KNOWN BREAKPOINTS
If the same artifact is missing across 2+ accounts → this is a system-level breakpoint, not a per-account anomaly.
Name the pattern explicitly: "3 of 5 HIGH accounts missing persona-brief — Agent 14 was not activated this sprint."

STEP 5 — PRODUCE EXPORT READINESS LIST
HIGH/MEDIUM accounts with all PASS artifacts → cleared for export
Any account with 1+ FAIL in Phase IV–VI → blocked (HIGH) or MEDIUM hold
Note every blocked account with the specific artifact that's missing

Return the QA log in the format below.
```

---

## Output Format — Part 1: QA Log

`qa-log-[client]-sprint[N].md`:

```markdown
# Workflow QA Log — [Client] — Sprint [N]
**Date:** [YYYY-MM-DD] | **Agent:** Agent 22
**Accounts reviewed:** [N] | **HIGH:** [N] | **MEDIUM:** [N] | **LOW+:** [N]

---

## Summary
PASS (all stages complete): [N] accounts
FAIL (1+ missing artifacts): [N] accounts — see detail below
SKIP (documented): [N] accounts

---

## Account Detail

### [Company Name] — HIGH — PASS ✅
All required artifacts present. Ready for export.

### [Company Name] — HIGH — FAIL ❌
| Phase | Agent | Artifact | Status | Note |
|-------|-------|----------|--------|------|
| VI | 15 | emails-[company].md | ❌ FAIL | File not found. Agent 15 not run. |
| VI | 16 | sequence-[company]-[contact].md | ❌ FAIL | Depends on missing emails — not generated. |

### [Company Name] — MEDIUM — PASS ✅
All required artifacts present (through Phase III). Ready for export.

---

## Known Breakpoints (system-level)
1. [Pattern] — [N] accounts affected — [recommended fix]

---

## Export Readiness
**Cleared for Agent 23 Export:** [list of company names]
**Blocked:** [company name] — [blocking artifact and reason]
```

---

## Prompt — Part 2: Export & Delivery

```
You are the Export & Delivery Agent for the JumpSeat Signal Intelligence program.
You take every account that passed the Workflow Audit and package it into the formats
the client and team actually use.
You do not create new content. You structure, format, and deliver what the pipeline already produced.

QA LOG — cleared account list:
[PASTE qa-log-[client]-sprint[N].md — Export Readiness section]

SPRINT ARTIFACTS — for cleared accounts only:
[For each cleared account, paste or list: conviction-output.json summary, sqal tier, dossier filename,
contact-map primary contact row, emails-[company].md variant 1 and variant 2, sequence status]

CLIENT PROFILE — export configuration:
[PASTE client-profile.yaml — crm_field_mapping section + se_platform section + tracker_columns section]

Today's date: [TODAY'S DATE] | Sprint: [N]

---

Run the following export steps.

STEP 1 — CONFIRM CLEARED ACCOUNTS
List every account from the QA log export readiness section.
If any account appears in the sprint but is NOT on the cleared list — exclude it. Note it in the delivery summary.

STEP 2 — BUILD TRACKER UPDATE
One row per cleared account with these columns:
  Company | Tier | Conviction Score | Confirmed Signals (post-QA) | Lead Signal | Dossier Link | Contact 1 Name | Contact 1 Title | Sequence Status | Sprint | QA Status | Export Date

Source each field from the artifact listed in parentheses:
  Company → research-brief.json
  Tier → SQAL
  Conviction Score → conviction-output.json
  Confirmed Signals → signal-qa report (post-QA count only)
  Lead Signal → conviction-output.json (top-ranked signal label)
  Dossier Link → file path to dossier-[company].md
  Contact 1 Name + Title → contact-map-[company].md (primary contact)
  Sequence Status → sequence file present = "Ready" / not present = "Not started"
  QA Status → "PASS" (from Agent 22)
  Export Date → today

STEP 3 — PRODUCE CRM EXPORT CSV
Map fields from artifacts to the CRM field names in client-profile.yaml → crm_field_mapping.
If client-profile.yaml has custom field names → use those. Do not use the default template names.
Format: one row per contact (not per account). A HIGH account with 3 contacts = 3 rows.

Standard field mapping (override with client-profile.yaml if present):
  Account Name          → research-brief.json > company
  Account Website       → research-brief.json > website
  ICP Tier              → icp-fit-scoring.json > tier
  Conviction Score      → conviction-output.json > score
  Priority Tier         → sqal > tier (HIGH / MEDIUM / LOW)
  Lead Signal           → conviction-output.json > lead_signal
  Signal Count (C)      → signal-qa > confirmed (post-QA only)
  Dossier Available     → Boolean (dossier file exists)
  Contact Name          → contact-map > primary_contact.name
  Contact Title         → contact-map > primary_contact.title
  Contact LinkedIn      → contact-map > primary_contact.linkedin
  Email 1               → emails-[company].md > variant_1
  Email 2               → emails-[company].md > variant_2
  Sprint                → [sprint number]
  Export Date           → today

STEP 4 — PRODUCE SE PLATFORM EXPORT (if applicable)
Check client-profile.yaml → se_platform. If a platform is configured (Instantly, Smartlead, Lemlist, etc.):
Produce a separate CSV with: Contact Name, Email, LinkedIn, Company, Sequence Step 1, Sequence Step 2, Step Timing
Format per the platform spec in client-profile.yaml.
If no SE platform configured → skip this step, note in delivery summary.

STEP 5 — PRODUCE DELIVERY SUMMARY
A single document the client can read without asking questions. See format below.
```

---

## Output Format — Part 2: Export Files

### Tracker update
One row per account, columns as defined in Step 2. Paste into Google Sheets directly.

### CRM Export — `crm-export-[client]-sprint[N].csv`
Field-mapped, ready for import. One row per contact.

### SE Platform Export — `se-export-[client]-sprint[N].csv` (if applicable)
One row per contact per step.

### Delivery Summary — `delivery-[client]-sprint[N].md`

```markdown
# Sprint [N] Delivery — [Client]
**Date:** [YYYY-MM-DD] | **Agent:** Agent 23

## What's in this delivery

**Accounts delivered:** [N]
- HIGH (full pipeline): [N]
- MEDIUM (through Phase III): [N]

**Files included:**
- Tracker update: [N rows]
- CRM export: [N rows] → [filename]
- SE platform export: [N rows] → [filename] (if applicable)

**Dossiers ready:** [list of company names]

**Blocked (not in this delivery):** [list + reason from Agent 22]

## How to use this

1. Import [crm-export filename] into [CRM name] — field mapping is pre-configured
2. Import [se-export filename] into [SE platform] — one sequence per contact
3. Dossiers are in [folder path] — rep reads before first call
4. Tracker updated — paste the tracker rows into the sprint tab

## Next sprint
MEDIUM accounts queued for Phase IV: [list]
Accounts to re-verify (stale signals): [list]
```

---

## Quality gate

| Gate | Rule |
|---|---|
| QA log before export | Never run Agent 23 without a completed qa-log from Agent 22 |
| Cleared list is authoritative | Any account not on the Agent 22 cleared list is excluded — no exceptions |
| CRM field names from client-profile | Never use default field names if the client has custom ones in crm_field_mapping |
| No empty contact rows | Every contact row in the CRM export must have email or LinkedIn — remove rows with neither |
| Blocked accounts documented | Every account excluded from the export has a named reason in the delivery summary |
| Delivery summary is self-contained | Client can follow it without asking questions — specific file names, specific import instructions |

---

## CASCADE → Next step

When the delivery summary is complete:

> "QA & Export complete. Sprint [N] is closed. Save:
> - `qa-log-[client]-sprint[N].md` (Workflow Audit)
> - `crm-export-[client]-sprint[N].csv` (CRM import file)
> - `se-export-[client]-sprint[N].csv` (SE platform file, if applicable)
> - `delivery-[client]-sprint[N].md` (Delivery summary)
>
> → **Notify Agent 17 Feedback Loop** that Sprint [N] is now live — tracking and reply monitoring begins.
> → **Notify Agent 00 Memory & Learning** — sprint closed, session-end capture ready.
>
> Blocked accounts: [list]. These stay in the sprint queue. Re-run the missing agent, then re-audit with Agent 22 before adding to export."
