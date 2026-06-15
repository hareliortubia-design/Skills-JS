# Skill: Dossier Builder
**Mode:** B | **JumpSeat Agents:** 11 Account Narrative + 12 Messaging Strategist  
**Type:** Base (reutilizable) | **Version:** 1.0 — June 2026

---

**What it does:** Takes the signal research output and builds a complete 4–5 page intelligence dossier that the sales rep reads before the first call. Answers the question: *"Why should we call this company right now, and what do we say?"*

**Input:** Signal research output (Mode A JSON)  
**Output:** Intelligence dossier (markdown or HTML) — account profile, confirmed signals, contacts, pitch angle, barriers, opening  
**Feeds into:** Email Personalization (Mode C) + BD rep for discovery call prep

**Rule:** The dossier is not a data dump. It tells a story. The first paragraph (Intelligence Narrative) must answer why THIS company, why NOW. If it doesn't, the dossier is not done.

---

## When to build a dossier

**Build immediately** if:
- Weighted conviction score ≥ 7.0, OR
- GR-01 (new DC opening) is confirmed, OR
- LS-01 (new supply chain leader) is confirmed

**Do not build yet** if:
- Weighted score < 7.0 AND no GR-01 or LS-01 confirmed → research remaining signal gaps first

---

## Dossier Sections

### 1. Header block
```
ACCOUNT: [COMPANY NAME]
CONVICTION: [HIGH/MEDIUM/PENDING] | SCORE: [X confirmed / X.X weighted]
URGENCY: [CRITICAL / HIGH / MEDIUM]
GENERATED: [DATE]
```

### 2. Intelligence Narrative (most important section)
Two to three sentences. Answer:
- Is this the most pressure-loaded outsourcing/buying moment this company has had in recent years?
- Why now? What's converging?

Write with confidence. This is what the sales rep reads first. If it doesn't create urgency in 3 sentences, rewrite.

### 3. Confirmed Signals
List only Confirmed (C) signals.
```
✅ [SIGNAL NAME]: [one-line finding with source in parentheses]
```

### 4. Inferred Signals
List only Inferred (I) signals.
```
~ [SIGNAL NAME]: [one-line reasoning — no source needed, clearly labeled as inferred]
```

### 5. Site Intelligence
- Primary facility: location
- Estimated sq footage (if known)
- Estimated dock doors (if known)
- Number of known locations
- States of operation

### 6. Trigger Stack
Which signals are clustering and what that means for the pitch.
```
[SIGNAL A] + [SIGNAL B] = [what this means — the sales rep's angle]
```

### 7. Barriers
What could slow or block the sale. One sentence each. Max 3.

### 8. Key Contacts
```
1. [Name] — [Title] — [Why they're the right first contact]
2. [Name] — [Title] — [Why they're the backup / secondary]
```
If contacts are not known, note "Research needed — pull from LinkedIn / Apollo."

### 9. Distraction Signals
List any confirmed DX signals. If none: "None identified."
```
⚠️ [SIGNAL]: [finding + source]
```

### 10. The Opening
Even when barriers exist, find the contrarian angle. One paragraph.
Answer: What changes about the play given the barriers? Is there a moment where the barrier becomes an opening?

### 11. Site Portfolio (optional)
Table of all known facilities with fit read.
```
| Site | Fit Read | Note |
|------|----------|------|
| [address] | CONFIRMED / VERIFY / RESEARCH | [what to check] |
```

### 12. Reactivation Triggers (PENDING accounts only)
List 3–5 specific events that move this account from PENDING to ACTIVE.
```
- [Event 1 — specific, observable trigger]
- [Event 2]
- [Event 3]
Monitoring cadence: check every [30/60/90] days
```

### 13. Outreach Approach
```
Lead signal: [the one signal to open with]
Angle: [cost reduction / labor relief / compliance / greenfield / M&A transition / operational autonomy]
Timing urgency: [why now vs. in 6 months]
Entry level: [executive / operational / plant-level]
First call objective: [what we want to learn or propose]
```

---

## Prompt

```
You are a senior intelligence analyst building an account dossier for [CLIENT NAME]'s business development team.

The sales rep will read this before their first call. Your job is to answer:
"Why should we call this company right now, and what do we say first?"

Company: [COMPANY_NAME]
Signal Research: [PASTE MODE A JSON OUTPUT]

Build the dossier using the template below. Follow the section structure exactly.

[PASTE DOSSIER SECTIONS TEMPLATE]

Rules:
- Intelligence Narrative must create urgency in 3 sentences or fewer.
- Only list Confirmed signals in section 3. Inferred signals go in section 4.
- Caveats (what is not verified) belong in Barriers or Inferred — not in Confirmed.
- The Opening section must find an angle even if barriers exist.
- If a contact is unknown, say so — do not invent contacts.
```

---

## Quality gate

| Gate | Condition | Action |
|------|-----------|--------|
| Dossier threshold | Weighted score < 7.0 AND no GR-01/LS-01 | Do not build yet — research gaps first |
| Intelligence Narrative | Reads like a data dump, no urgency | Rewrite — must answer "why now" in 3 sentences |
| Confirmed signals | No source listed | Move to Inferred |
| Contacts | Names invented without source | Flag as "research needed" |
| PENDING account | Reactivation triggers not defined | Add before delivering dossier |

---

## CASCADE → Next step

When the dossier is complete, tell the user:

> "Dossier ready. Save as `dossier-[company-name].md`.
>
> → **Next: run skill-contact-mapping**
> Bring: this dossier + client-profile.yaml (buyers section).
> It will identify who to call first, in what order, and with what angle."
