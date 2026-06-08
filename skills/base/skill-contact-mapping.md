# Skill: Contact Mapping
**JumpSeat Agent:** 09 Contact Mapping  
**Type:** Base — works for any client  
**Version:** 1.0 | June 2026

> Takes a completed account dossier and maps who to call first, why, and in what order — based on the confirmed signals, the buying window, and the client's buyer personas.

---

## Input
- `client-profile.yaml` — for buyer personas and deal profile
- Dossier output from `skill-dossier-builder` — the intelligence profile for the account

## Output / Artifact
- `contact-map.md` — prioritized list of 3–5 contacts with title, role in the decision, why they're the right entry point, and what angle opens the conversation.

## Feeds into
- `skill-email-personalization` — passes the primary contact + their angle

---

## How to run

1. Read `client-profile.yaml` — extract `client.buyers[]` and `client.deal_profile`
2. Receive the dossier for the account
3. Run the Contact Mapping prompt below
4. Output the contact map

---

## Prompt

```
You are a contact mapping analyst. Your job is to identify who to call at this
account, in what order, and with what angle — based on the dossier intelligence
and the client's typical buyer profile.

CLIENT PROFILE (buyers + deal):
[PASTE client-profile.yaml — sections: client.buyers, client.deal_profile]

ACCOUNT DOSSIER:
[PASTE dossier output from skill-dossier-builder]

---

Map the contacts for this account using this process:

STEP 1 — IDENTIFY AVAILABLE CONTACTS
From the dossier's KEY CONTACTS section, list everyone found.
For each: name, title, how they were found (LinkedIn, press, job posting), confidence level.
If the dossier has no contacts: say so clearly. Do not invent names.

STEP 2 — MATCH CONTACTS TO BUYER PERSONAS
Cross-reference each contact against the client's buyer personas.
Flag:
  PRIMARY — matches the ideal buyer title exactly
  SECONDARY — adjacent role, can be an influencer or referral path
  NOT A FIT — wrong function or level for this sale

STEP 3 — RANK BY ENTRY PRIORITY
Rank contacts 1 through N.
Entry priority is determined by:
  1. Match to primary buyer persona
  2. Connection to the lead signal (e.g., if LS-01 is the lead signal, the new leader is #1)
  3. Seniority vs. access — sometimes the #2 is a better first call than the C-suite
  4. Evidence of pain ownership (who is responsible for the problem this service solves)

STEP 4 — DEFINE THE ANGLE PER CONTACT
For each contact, define:
  - Why they care about the lead signal specifically (based on their role)
  - What they are likely worried about right now
  - What the first conversation should accomplish (not a pitch — a question or observation)
  - What NOT to say to this specific person

STEP 5 — FLAG GAPS
If critical buyer personas are missing from the contact list:
  - Say which role is missing
  - Suggest how to find them (LinkedIn search, referral path from another contact)

Return the full contact map in the format below.
```

---

## Output Format

```markdown
# Contact Map — [Company Name]
Account urgency: [CRITICAL / HIGH / MEDIUM]
Lead signal: [signal code + one-line finding]
Generated: [date]

---

## Contact Priority List

### #1 — PRIMARY ENTRY POINT
**Name:** [or "Not found — see gap note"]
**Title:** 
**Found via:** LinkedIn / press release / job posting / dossier research
**Persona match:** PRIMARY — matches [buyer persona from client profile]
**Why #1:** [connection to lead signal + role relevance]
**Their likely worry right now:** 
**Opening angle:** [what observation or question opens the conversation]
**Do NOT:** [what to avoid with this specific person]

### #2 — SECONDARY CONTACT
[same structure]

### #3 — INFLUENCER / REFERRAL PATH
[same structure]

---

## Contact Gaps
Missing personas:
- [Title] — not found. Suggested path: [LinkedIn search / referral from #1 / press search]

---

## Notes for the rep
[1–2 sentences on the dynamic between contacts, if relevant — e.g., "new leader (#1) likely hasn't met the incumbent vendor yet — ideal moment"]
```

---

## Quality gate

| Gate | Rule |
|------|------|
| No invented contacts | If a name isn't in the dossier or findable research, say "not found" |
| Lead signal drives #1 | The top contact must be connected to the lead signal — not just the most senior person |
| Angle is specific | "Talk about their DC expansion" not "discuss growth opportunities" |
| Gaps are flagged | Missing buyer personas must be named and a search path suggested |
| No pitch framing | "Opening angle" is an observation or question — never a product description |

---

## CASCADE → Next step

When the contact map is complete, tell the user:

> "Contact map ready. Save as `contact-map-[company-name].md`.
>
> → **Next: run skill-email-personalization**
> Bring: this contact map + the dossier.
> It will write Email 1 (≤75 words) and Email 2 (≤35 words) for the primary contact — ready to send."
