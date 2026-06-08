# Skill: Salesforce Export
**Mode:** D | **JumpSeat Agent:** 18 Analytics & Metrics  
**Type:** Base (reutilizable) | **Version:** 1.0 — June 2026

---

**What it does:** Converts the signal research JSON array into a CSV ready for Salesforce Account object import. One row per account. All scoring, urgency, and next action logic applied automatically.

**Input:** Array of Mode A JSON outputs (all researched accounts)  
**Output:** CRM-ready CSV for Salesforce import  
**Feeds into:** Salesforce Account object → [CLIENT] BD team pipeline view

**Future:** Phase 2 = direct API write via Salesforce MCP (no CSV needed — button in SF triggers skill and writes fields directly).

---

## CSV Column Mapping

```
Account_Name          → Account Name (standard SF field)
Industry              → Industry (standard SF field)
State                 → Billing State (standard SF field)
Revenue               → Annual Revenue (standard SF field)
Fit_Tier              → [Client]_Fit_Tier__c (custom field)
Fit_Score             → [Client]_Fit_Score__c (custom field)
Conviction_Score      → [Client]_Conviction_Score__c (custom field)
Weighted_Score        → [Client]_Weighted_Score__c (custom field)
Urgency               → [Client]_Urgency__c (custom field)
Top_Signal            → [Client]_Lead_Signal__c (custom field)
Signal_Date           → [Client]_Signal_Date__c (custom field)
Confirmed_Signals     → [Client]_Confirmed_Signals__c (custom field)
Dossier_Status        → [Client]_Dossier_Status__c (custom field)
Next_Action           → [Client]_Next_Action__c (custom field)
Stack_Notes           → [Client]_Stack_Notes__c (custom field, text area 200 chars)
Last_Updated          → [Client]_Last_Updated__c (custom field)
```

---

## Next_Action logic (automated)

```
Urgency = CRITICAL                        → "Outreach this week"
Urgency = HIGH + Dossier_Status = Complete → "Outreach ready"
Urgency = HIGH + Dossier_Status = Pending  → "Build dossier"
Urgency = MEDIUM                           → "Research signal gaps"
Stale flags present                        → "Re-verify signals"
Conviction_Level = PENDING                 → "Monitor — set reactivation trigger"
```

---

## Prompt

```
Convert this array of signal research results into a CSV for Salesforce import.

Input: [PASTE ARRAY OF MODE A JSON OUTPUTS]

Rules:
- One row per account
- Use the exact column names in the mapping above (replace [Client] with [CLIENT NAME])
- Urgency values: CRITICAL, HIGH, MEDIUM, LOW only — no other values
- Dossier_Status: "Complete" if dossier exists, "Pending" if not
- Apply Next_Action logic as defined above
- Stack_Notes: max 200 characters
- Date format: YYYY-MM-DD

Return as CSV with headers. Plain text only. No extra formatting or markdown.
```

---

## Phase 2 — Direct Salesforce API (future sprint)

When volume justifies it, replace CSV import with direct API write:

```python
# Salesforce REST API via MCP integration
# Endpoint: /services/data/v59.0/sobjects/Account/[AccountId]
# Method: PATCH
# Payload: {
#   Lazer_Conviction_Score__c: 11,
#   Lazer_Urgency__c: "CRITICAL",
#   Lazer_Lead_Signal__c: "LS-01 — New VP Supply Chain hired March 2026",
#   Lazer_Next_Action__c: "Outreach this week",
#   Lazer_Last_Updated__c: "2026-06-05"
# }
```

Dan's vision: a **"Dossier" button in Salesforce** — BD rep clicks it on any account, the skill runs, and the dossier populates the record fields automatically.

---

## Quality gate

- Urgency column must only contain: CRITICAL, HIGH, MEDIUM, LOW
- Stack_Notes over 200 characters → truncate to 200
- Date columns must be YYYY-MM-DD format
- No null values in Account_Name, Urgency, Next_Action, Last_Updated
