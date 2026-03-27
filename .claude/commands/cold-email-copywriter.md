---
name: cold-email-copywriter
description: Write cold email outreach sequences (Email 1, 2, 3) using the 10 core copy rules — short, prospect-first, value-stacked, one pain per email. Trigger when the user wants to write a cold email, outreach sequence, follow-up email, or email campaign copy.
---

# Cold Email Copywriter

Write cold outreach sequences that get replies. Every email follows 10 non-negotiable rules derived from high-performing B2B campaigns.

---

## Step 1: Gather Input

If not already in $ARGUMENTS, ask the user for:

1. **What you offer** — your product, service, or value prop in one sentence
2. **Who you're targeting** — job title, industry, company type (e.g. "Heads of Sales at SaaS companies, 20-200 employees")
3. **The core pain or opportunity** — what problem do they have, or what result can you deliver?
4. **Lead magnets / offers** — what can you give them across 3 emails? (e.g. a list, script, case study, free audit, swipe file) — if they only have one, help them brainstorm 2 more
5. **Personalization data available** — what variables do they have? (e.g. job title, hiring signals, review data, competitor names)

Parse any details already in: $ARGUMENTS

---

## Step 2: The 10 Copy Rules (Apply to Every Email)

Before writing, internalise these rules. Violating any one of them = rewrite.

### Rule 1 — Keep it short (<= 75 words)
Every email must be 75 words or fewer. Count the words. If over, cut.

**What to cut first:** intros about yourself, filler transitions, feature lists, anything that doesn't directly serve the CTA.

> "Here's a long intro about us..." → "Saw your team scaling fast - quick idea for {X}"

---

### Rule 2 — First words = 90% of impact
The first line determines if they read on. Open with them, not you. Never open with your name, company, or what you do.

**Strong openers:**
- A hiring signal: "Hiring 10 AEs this quarter?"
- A pain: "Most {role}s waste 3 hours a day on {task}."
- A direct hook: "Saw you're expanding into {market}."
- A result: "Teams like {company} cut {X} by 40% doing this."

> "I'm Ima from AI Point..." → "Hiring 10 AEs this quarter?"

---

### Rule 3 — Make it about them, not you
Replace "we" with "you". Replace company descriptions with prospect outcomes. Use their language, their pain, their world.

> "We help companies like yours..." → "Your team could cut ramp time by 40% with X"

---

### Rule 4 — 1 problem per email
Each email has exactly one pain, one opportunity, or one angle. Do not list benefits. Do not mention multiple use cases.

Choose the single clearest pain for Email 1. Find a different angle for Email 2. A third for Email 3.

---

### Rule 5 — Clear, value-based CTA
End every email with one direct question that offers value, not a meeting. The ask should feel easy to say yes to.

**Strong CTAs:**
- "Want the workflow they used to double replies?"
- "Want me to send the script?"
- "Should I pull 50 lookalikes from your target market?"

> "Are you free for a call?" → "Want the workflow they used to double replies?"

---

### Rule 6 — Simple language, no fluff
Write like you're texting a smart colleague. No buzzwords. No adjectives that don't prove anything. No hedging.

**Words to avoid:** leverage, synergy, streamline, innovative, cutting-edge, empower, transform, revolutionize, solutions, seamless

**Never use dashes or em dashes (- or —) anywhere in email copy.** Use a period or break into two sentences instead.

> "We cut costs — fast." → "We cut costs. Fast."

**Tone check:** Read it out loud. If it sounds like a press release, rewrite it.

---

### Rule 7 — Subject lines: 2-4 lowercase words
Short. Conversational. No punctuation unless it's a question mark. Never capitalize unless a proper noun.

**Good examples:**
- `growth idea`
- `quick question`
- `hiring {role}?`
- `{pain} fix`
- `idea for {company}`

**Bad examples:**
- `Introducing Our Revolutionary Platform`
- `Following Up On My Last Email`

---

### Rule 8 — Avoid feature dumping
Never list features. Translate every feature into a prospect outcome.

> "We have AI, automation, integrations, and a dashboard" → "Here's how teams cut manual reporting by 60%"

---

### Rule 9 — No fluffy follow-ups
Every follow-up must add new value. "Just checking in" and "bumping this to the top of your inbox" are banned.

Each follow-up should:
- Reference a different pain point
- Offer a new lead magnet or resource
- Make it about them again

---

### Rule 10 — Stack offers across the sequence (Email 1, 2, 3 = each new value)
Each email in the sequence delivers a different, standalone offer. The sequence escalates value, not pressure.

| Email | Pattern |
|---|---|
| Email 1 | Hook + Pain 1 + Offer 1 (e.g. "Want 50 lookalikes?") |
| Email 2 | Pain 2 + Offer 2 (e.g. "Want our cold call script?") |
| Email 3 | Pain 3 + Offer 3 (e.g. "Want to scrape your competitor's comments?") |

---

## Step 3: Sequence Structure

### Email 1 — The Hook

```
Subject: [2-4 lowercase words]

[Opening line about them — signal, pain, or observation]

[1-2 lines: the opportunity or result you've seen]

[CTA: offer-based question, not a meeting ask]

[Your name]
```

### Email 2 — The Second Angle

```
Subject: [2-4 lowercase words, different angle]

[Reference a different pain — do NOT mention Email 1]

[1-2 lines: a new angle or proof point]

[New offer-based CTA]

[Your name]
```

### Email 3 — The Third Offer (or Breakup)

```
Subject: [2-4 lowercase words]

[New pain point or final value offer]

[1-2 lines max]

[Final CTA — either a new offer or a soft breakup: "Last one from me — want X before I go?"]

[Your name]
```

---

## Step 4: Write the Sequence

Write all 3 emails. After each one, verify it against the rules:

**Self-check before outputting each email:**
- [ ] Word count <= 75?
- [ ] First line is about them, not you?
- [ ] Only 1 problem or angle?
- [ ] CTA is an offer, not a meeting ask?
- [ ] Subject line is 2-4 lowercase words?
- [ ] No feature dumping?
- [ ] No buzzwords or corporate language?
- [ ] No dashes or em dashes (- or —) anywhere?
- [ ] Follow-ups add new value (not "just checking in")?
- [ ] Each email has a different offer?

If any box is unchecked, rewrite before moving on.

---

## Step 5: Output Format

Output the sequence in this format:

```
---
EMAIL 1
---
Subject: [subject]

[body]

---
EMAIL 2
---
Subject: [subject]

[body]

---
EMAIL 3
---
Subject: [subject]

[body]

---
```

After the sequence, include a short **Offer Stack** table:

| Email | Pain Angle | Offer |
|---|---|---|
| 1 | [pain] | [offer] |
| 2 | [pain] | [offer] |
| 3 | [pain] | [offer] |

And a **Personalization note** — flag any variables the user should fill in and what data they'd need.

---

## Brainstorming Lead Magnets (if user only has one offer)

If the user can only name one offer, help them build a 3-email offer stack by asking:

> "What data, templates, or resources do you have access to that your prospect would find useful?"
> "What do your best customers say was the most valuable thing you showed them early on?"
> "Is there a list, script, workflow, or case study you could share?"

Then suggest 2 additional offers based on what they sell. Examples:

| What they sell | Offer ideas |
|---|---|
| Sales tool | Cold call script, competitor analysis, lookalike list |
| Marketing service | Content calendar, hook library, ad swipe file |
| Recruiting | Outreach templates, job description teardown, sourcing playbook |
| Data/analytics | Free audit, benchmarks report, competitor data pull |
| Ops/automation | Workflow diagram, time savings calculator, integration checklist |

---

## Examples of Rule Violations and Fixes

| Violation | Rule | Fix |
|---|---|---|
| "I'm John from Acme and we provide..." | Rule 2, 3 | "Hiring {role} this quarter?" |
| "Our platform leverages AI to streamline workflows and deliver synergistic outcomes..." | Rule 6, 8 | "Teams using this cut {task} time by 60%." |
| 200-word email | Rule 1 | Cut to <=75 words, keep only the one pain and one CTA |
| "Just following up on my last email" | Rule 9 | New pain + new offer |
| "Would love to hop on a 30-min call to discuss!" | Rule 5 | "Want the playbook they used to hit X?" |
| Three features listed in one email | Rule 4, 8 | Pick one. Remove the rest. |
| Subject: "Introducing Our New AI-Powered Growth Solution" | Rule 7 | `growth idea` |
