---
name: lead-qualifier
description: Qualify a prospect by researching their website and online presence, then scoring them against your offer and ICP. Trigger when the user wants to qualify a lead, check if a company fits their ICP, or decide whether a prospect is worth outreaching to. Can load ICP criteria from an icp.md file if one exists.
---

# Lead Qualifier

Research a prospect and score them against your offer and ICP. Outputs a qualification verdict, match score, key signals, and a recommended action.

---

## Step 1: Gather Inputs

Collect the following before researching. If anything is missing, ask.

**About the prospect (required — at least one):**
- Company name
- Website URL
- LinkedIn company URL
- Contact name and title (optional but helpful)

**About your offer and ICP (required):**

First, check if an `icp.md` file exists in the current directory or project root. If found, use it as the ICP definition.

If no `icp.md` exists and the user hasn't described their offer, ask:

> "To qualify this prospect I need to understand your offer and ideal customer. Can you describe:
> 1. What you sell and who it's for
> 2. The problem it solves
> 3. What your best customers look like (industry, size, role, signals)"

Accept any format — a paragraph, bullet points, a pasted sales deck description, or a named file.

---

## Step 2: Extract ICP Criteria

Parse the offer/ICP description into qualification criteria. Structure it as:

```
Offer: [one-line description of what is being sold]
Problem solved: [the core pain point]

ICP criteria:
- Industry: [target industries]
- Company size: [headcount or revenue range]
- Business model: [B2B / B2C / SaaS / services / ecommerce / etc.]
- Geography: [target regions]
- Role (decision-maker): [titles to look for]
- Tech stack signals: [tools they should use or not use]
- Growth stage: [startup / SMB / mid-market / enterprise]
- Disqualifiers: [what rules them out immediately]
```

If only partial ICP info is available, proceed with what you have and flag any criteria you couldn't assess.

---

## Step 3: Research the Prospect

Run research across these sources. Use WebFetch for website pages and WebSearch for external signals.

### 3a. Website

Fetch the homepage and 1-2 relevant pages (About, Pricing, Product/Services):

- What do they do and who do they serve?
- What's their business model (selling to businesses or consumers)?
- What is their apparent company size (headcount signals, team page, office locations)?
- What industry are they in?
- What tools or tech do they reference (integrations, stack mentions)?
- Any pricing visibility — budget signal?
- What stage do they appear to be at (early, growth, established)?

### 3b. Company Size and Funding

Search: `"[company name]" employees OR headcount OR team size`
Search: `"[company name]" funding OR raised OR Series A/B/C OR revenue`

Look for:
- LinkedIn headcount (visible in search results)
- Crunchbase or similar funding data
- Any press mentioning size or stage

### 3c. Growth Signals

Search: `"[company name]" hiring OR jobs OR "we're hiring"`
Search: `"[company name]" expanding OR launched OR partnership OR new office [current year]`

Look for:
- Active hiring in relevant departments (signals budget and growth)
- Recent product launches or market expansion
- New leadership hires (often triggers buying decisions)
- Funding announcements (budget signal)

### 3d. Pain Point Signals

Search: `"[company name]" reviews OR Glassdoor OR G2 OR Capterra`
Search: `site:reddit.com "[company name]"`

Look for:
- Customer complaints that your offer solves
- Operational challenges mentioned in reviews or posts
- Role-specific pain points that match your ICP's typical problems

### 3e. Decision-Maker Presence (if contact provided)

Search: `"[contact name]" "[company name]" site:linkedin.com`

Look for:
- Seniority and actual decision-making authority
- Recent posts or activity that reveal priorities
- Tenure (short tenure = often buying mode to prove impact)

---

## Step 4: Score the Prospect

Score each ICP criterion independently, then calculate an overall match.

### Scoring per criterion

| Score | Meaning |
|---|---|
| 3 | Strong match — confirmed evidence |
| 2 | Likely match — inferred from signals |
| 1 | Weak match — possible but unclear |
| 0 | No evidence |
| -1 | Disqualifier — evidence contradicts ICP |

### Core criteria to score

| Criterion | Score (0-3 or -1) | Evidence |
|---|---|---|
| Industry fit | | |
| Company size fit | | |
| Business model fit | | |
| Geography fit | | |
| Decision-maker accessible | | |
| Tech stack alignment | | |
| Growth/budget signals | | |
| Pain point match | | |
| No disqualifiers triggered | | |

**Maximum possible score:** 27 (9 criteria × 3)

### Qualification bands

| Score | Verdict |
|---|---|
| 22-27 | **Strongly Qualified** — prioritise outreach |
| 15-21 | **Qualified** — worth reaching out |
| 8-14 | **Borderline** — deprioritise or gather more info first |
| 1-7 | **Weak fit** — not worth outreach now |
| Any -1 | **Disqualified** — stop unless disqualifier can be overridden |

---

## Step 5: Output the Qualification Report

```markdown
# Qualification Report: [Company Name]

**Verdict:** [Strongly Qualified / Qualified / Borderline / Weak Fit / Disqualified]
**Match Score:** [X/27]
**Confidence:** [High / Medium / Low — based on how much data was available]

---

## ICP Match Summary

| Criterion | Score | Evidence |
|---|---|---|
| Industry | X/3 | [what you found] |
| Company size | X/3 | [what you found] |
| Business model | X/3 | [what you found] |
| Geography | X/3 | [what you found] |
| Decision-maker | X/3 | [what you found] |
| Tech stack | X/3 | [what you found] |
| Growth/budget signals | X/3 | [what you found] |
| Pain point match | X/3 | [what you found] |
| No disqualifiers | X/3 | [what you found] |

---

## Key Signals

**For:**
- [Signal 1 — why this prospect fits]
- [Signal 2]
- [Signal 3]

**Against:**
- [Signal 1 — concern or gap]
- [Signal 2]

---

## Recommended Action

[One of: Reach out now / Reach out with caveat / Gather more info first / Deprioritise / Disqualify]

**Reasoning:** [1-2 sentences explaining the recommendation]

---

## Suggested Talking Points

Based on what was found, the strongest angles for outreach:

1. **[Angle]** — [why it's relevant, what signal it's based on]
2. **[Angle]** — [why it's relevant]
3. **[Angle]** — [why it's relevant]

---

## Data Gaps

Criteria that couldn't be assessed due to limited data:
- [Gap 1]
- [Gap 2]

*Suggested: [how to fill the gap, e.g. "check LinkedIn for headcount", "look for a pricing page"]*
```

---

## Batch Qualification

If the user provides a list of companies, qualify each one and produce a ranked summary table:

```markdown
# Batch Qualification Results

| Company | Score | Verdict | Top Signal | Action |
|---|---|---|---|---|
| [Company A] | 24/27 | Strongly Qualified | Hiring RevOps roles | Reach out now |
| [Company B] | 17/27 | Qualified | Series B funded | Reach out |
| [Company C] | 11/27 | Borderline | Size unclear | More research |
| [Company D] | -1 | Disqualified | B2C business model | Skip |

**Recommended outreach order:** A → B → [gather more info on C]
```

Then offer to generate full reports for the top-ranked prospects.

---

## ICP File Format (icp.md)

If the user wants to save their ICP for future sessions, suggest creating an `icp.md` file:

```markdown
# ICP Definition

## Offer
[One-line description of what you sell]

## Problem Solved
[The core pain point your offer addresses]

## Ideal Customer Profile

**Industry:** [e.g. SaaS, Professional Services, Healthcare]
**Company size:** [e.g. 10-200 employees]
**Business model:** [e.g. B2B only]
**Geography:** [e.g. US and UK]
**Decision-maker title:** [e.g. Head of Sales, VP Revenue, Founder]
**Tech stack signals:** [e.g. uses Salesforce or HubSpot]
**Growth stage:** [e.g. Series A-C, or $1M-$20M ARR]

## Best Customer Examples
- [Company name] — [why they're a great fit]
- [Company name] — [why they're a great fit]

## Disqualifiers
- [e.g. B2C companies]
- [e.g. under 10 employees]
- [e.g. outside US/UK]
```
