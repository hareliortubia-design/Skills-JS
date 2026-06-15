# Skill: Email Personalization
**Mode:** C | **JumpSeat Agents:** 15 Cold Email Copywriter + 16 Sequence Design  
**Type:** Base (reutilizable) | **Version:** 1.0 — June 2026

---

**What it does:** Writes the first two outreach emails for a specific contact using the intelligence from the dossier. Signal-first, no pitch, peer-to-peer tone. Built to get a response — not to close a deal on email.

**Input:** Dossier output (Mode B) + contact first name and title  
**Output:** Email 1 (≤75 words) + Email 2 (≤35 words)  
**Feeds into:** BD rep sends manually via HubSpot / Outreach / Apollo sequence

**Rule:** Email 1 and Email 2 are the opener and the bump. They are not a pitch. The goal is one response — not a meeting.

---

## Hard rules (apply to all clients)

These rules never change regardless of client:

- Email 1: **maximum 75 words** — count them
- Email 2: **maximum 35 words** — count them
- **Never pitch.** Never describe what the client does or sells explicitly.
- No "I hope this finds you well." No "I wanted to reach out." No filler openers.
- **Line 1 must be a specific observation** — a fact about their business, not a question.
- End with **one soft question** — not a meeting request.
- Tone: **direct, peer-to-peer, confident.** Write like a colleague who did their homework, not a vendor.

---

## What changes per client

| Component | Base (same for all) | Client-specific |
|-----------|---------------------|-----------------|
| Word limits | ✅ 75w / 35w always | — |
| No-pitch rule | ✅ Always | — |
| Opener format (observation, not question) | ✅ Always | — |
| Lead signal (the observation in line 1) | — | Drawn from the dossier |
| Signal references (lines 2–3) | — | The 2–3 confirmed signals for this account |
| Soft question at end | — | Reflects the client's pain point or buying trigger |
| Tone calibration | — | Match the industry (supply chain = direct/operational; tech = slightly more casual) |

---

## Prompt

```
You are writing cold outreach for a [CLIENT NAME] business development rep.
[CLIENT ONE-SENTENCE DESCRIPTION]

HARD RULES:
- Email 1: maximum 75 words — count every word before returning
- Email 2: maximum 35 words — count every word before returning
- Never pitch. Never mention [CLIENT NAME]'s services explicitly.
- No "I hope this finds you well." No "I wanted to reach out."
- Line 1 must be a specific observation — not a question.
- End with one soft question. Not a meeting request.
- Tone: direct, peer-to-peer, confident.

Contact: [FIRST NAME], [TITLE] at [COMPANY]
Lead signal: [FROM DOSSIER — the single strongest confirmed signal]
Top 3 confirmed signals: [FROM DOSSIER]
Trigger stack: [FROM DOSSIER — which signals are clustering]

EMAIL 1 (HARD LIMIT: 75 words):
- Line 1: specific observation about their operation or current situation (use the lead signal)
- Lines 2–3: reference 2–3 confirmed signals as one-liners. What's happening at their business right now.
- Final line: one soft question that opens a conversation.

EMAIL 2 (HARD LIMIT: 35 words):
- Callback to the lead signal
- One direct question about whether they're managing this internally
- Nothing else.

Return EMAIL 1 and EMAIL 2 as plain text. No subject line. No formatting.
Count the words of each email and show the count in parentheses after each.
```

---

## Output Format

```
EMAIL 1 ([word count]w):
[plain text email]

EMAIL 2 ([word count]w):
[plain text email]
```

---

## Real example — Burlington Stores (written by Lazer BD rep)

> *"Hi Victor, I've been keeping a close eye on Burlington's growth... The 50% expansion of the DC network is hard to miss. Savannah coming online, Buckeye breaking ground in April, and roughly 4 million square feet added through 2028."*

**Result:** Response in 3 minutes. ✅

What makes it work:
- Line 1 is a specific observation (50% DC expansion) — not a compliment, not a question
- It names specific locations (Savannah, Buckeye) — signals deep research, not a template
- No pitch, no ask — just presence of intelligence

---

## Quality gate

- Email 1 over 75 words → rewrite. Do not send.
- Email 2 over 35 words → rewrite. Do not send.
- Lead signal is a YP (structural/context) signal, not LS/LB/GR/FN → flag. Structural signals alone are weak openers. Push back to dossier for a better lead.
- Email opens with "I" → rewrite. Start with the observation about them.
- Email mentions the client's company name in first 2 lines → too early. Move it or cut it.

---

## CASCADE → Next step

When both emails are ready, tell the user:

> "Emails ready. Copy Email 1 and Email 2 into your outreach sequence.
>
> → **Next: run skill-crm-export**
> Bring: all account artifacts for this batch (conviction outputs + dossier status).
> It will generate the CSV to import into Salesforce or HubSpot with every account scored and tagged."
