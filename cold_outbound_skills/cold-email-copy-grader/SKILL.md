---
name: cold-email-copy-grader
description: Grade cold email campaign copy on a 0-100 scale. Paste draft sequences and targeting info to get a score, risk flags, benchmark comparisons, and rewrites for scores below 65. Trigger when the user pastes email copy, asks to review or grade a campaign, or wants copywriting feedback.
---

# Cold Email Copy Grader

Grade cold email campaign copy on a 0-100 scale using patterns from 1,000+ real B2B campaigns. Returns a Predicted Success Score, risk flags, benchmark comparisons, and full rewrites when the score is below 65.

---

## What You Need From the User

Before grading, collect:

1. **Email sequences** — subject lines + body copy for each step
2. **Targeting info** — job titles, industries, company size, geography
3. **Merge variables** — every `{{variable}}` they plan to use

If any of these are missing, ask before scoring.

**Exception — Quick Grade:** If the user says "quick check", "gut check", "how does this look", or pastes a single email without targeting details, run the Quick Grade (see below) instead.

---

## Quick Grade (Fast Path)

Run a single-pass review hitting only these 5 factors. Output a letter grade (A-F), 3 bullets, and one recommendation. Takes seconds.

1. **Subject line pattern** — question? lowercase start? under 50 chars?
2. **Body word count** — over or under 70 words?
3. **CTA type** — direct question, passive ask, or missing?
4. **Variable quality** — standard only, enriched, targeted AI, or generic AI trap?
5. **Sequence count** — how many emails? any bump-only follow-ups?

Output format:
```
Grade: [A/B/C/D/F]
Biggest strength: [one line]
Biggest risk: [one line]
One fix: [one line]
Recommendation: [one line]
```

---

## Full Grade: The #1 Rule Before Scoring

**Generic AI first-lines have a 71% poor campaign rate. They perform 4x worse than no personalization at all.**

Before running the full analysis, scan for this pattern:

> AI-generated description of what the prospect's company does, used as an opener.

Examples of what to flag immediately:
- `{{ai_generated_first_line}}`
- `{{personalized_line}}`
- "I saw that {{company_name}} helps customers with {{ai_summary}}..."
- "I noticed your company is doing great things in [industry]..."

If found: flag as **CRITICAL** and recommend replacing with targeted product-focused AI or specific enriched data.

---

## Full Grade: Scoring Formula

```
Predicted Success Score =
  (0.40 × CopywritingScore) +
  (0.35 × TargetingScore) +
  (0.25 × VariableScore)
```

Then apply the Risk Adjustment (see below).

---

### CopywritingScore (40% weight)

#### Subject Line (0-30 pts)

| Pattern | Points |
|---|---|
| Question format (ends with ?) | +10 |
| Lowercase start | +5 |
| Under 30 characters | +3 |
| Includes `{{company_name}}` | +5 |
| Fake reply (Re:/Fwd: on email 1) | -5 |

**Length benchmarks:**

| Tier | Avg length |
|---|---|
| Top performers | 42 chars |
| Average | 47 chars |
| Poor | 54 chars |

#### Body Quality (0-40 pts)

| Element | Points |
|---|---|
| Ends with a question or direct CTA | +10 |
| Observation opener ("I noticed...") | +5 |
| Question opener ("Quick question about...") | +5 |
| 50-150 words | +10 |
| Follows P-A-S or A-I-D-A framework | +10 |

**Opening hook performance:**

| Pattern | Top-performer rate |
|---|---|
| Observation ("I noticed {{company}}...") | 14% |
| Question ("Quick question about...") | 11% |
| Personalised ("As a {{job_title}} at...") | 10% |
| Direct pitch (no greeting) | 6% |
| Generic greeting ("Hope you're doing well") | 4% |

**CTA performance:**

| Pattern | Top-performer rate |
|---|---|
| Question ("Would it make sense to chat?") | 13% |
| Soft ask ("Thoughts?") | 11% |
| Meeting request ("15 minutes next week?") | 9% |
| Reply request ("Let me know") | 6% |
| Link click ("Check this out") | 5% |

#### Structure (0-20 pts)

| Element | Points |
|---|---|
| Single CTA (not three asks) | +10 |
| 2-3 paragraphs | +5 |
| Clean formatting, no HTML issues | +5 |

2-3 paragraphs = 12% top-performer rate. 6+ paragraphs = 4%.

#### Sequence Flow (0-10 pts)

| Element | Points |
|---|---|
| Value-add follow-ups (not just bumps) | +5 |
| Breakup email present | +3 |
| Logical escalation across sequence | +2 |

**Sequence count benchmarks:**

| Sequences | Top-performer rate |
|---|---|
| 1 | 2% |
| 2 | 5% |
| 3 | 9% |
| 4-5 | 11-12% |
| 6+ | 8% |

**Follow-up type performance:**

| Type | Top-performer rate |
|---|---|
| Value-add (new info, case study, insight) | 13% |
| Breakup ("last attempt") | 10% |
| Referral ("who else should I talk to?") | 8% |
| Bump ("just following up...") | 5% |

Recommended: 1 opener + 1-2 value-add follow-ups. Keep total follow-ups under 3. Space 2-5 days apart.

---

### TargetingScore (35% weight)

#### Decision-Maker Concentration (0-40 pts)

| DM percentage | Points |
|---|---|
| 80%+ | 40 |
| 60-79% | 30 |
| 40-59% | 20 |
| 30-39% | 10 |
| <30% | 0 |

Decision-maker titles: Owner, CEO, Founder, President, Partner, Managing Director, VP, Director, Head of.

**Title performance:**

| Level | Top-performer rate |
|---|---|
| Owner/CEO/Founder | 15% |
| VP/Director | 10% |
| Manager | 7% |
| Individual Contributor | 4% |

#### Industry Focus (0-30 pts)

| Top industry % | Points |
|---|---|
| 50%+ | 30 |
| 30-49% | 20 |
| 15-29% | 10 |
| <15% | 0 |

**Industry performance:**

| Industry | Top-performer rate |
|---|---|
| Restaurants / Food Service | 18% |
| Healthcare | 14% |
| Real Estate | 12% |
| Professional Services | 11% |
| Technology / SaaS | 9% |

#### Copy-Audience Alignment (0-30 pts)

| Alignment | Points |
|---|---|
| Strong (copy references specific industry/role pain points) | 30 |
| Moderate (generic but relevant value prop) | 20 |
| Weak (mismatch between copy and audience) | 10 |
| None | 0 |

**Company size note (informational only):**
SMB (1-50 employees) significantly outperforms enterprise. 1,000+ employee companies = 3% top-performer rate vs 13% for 1-10 employee companies.

---

### VariableScore (25% weight)

**This is the most counterintuitive component.** Not all personalization is equal — some AI personalization actively hurts campaigns.

#### Variable Types and Performance

| Type | Example | Top-performer % | Poor % |
|---|---|---|---|
| Targeted product-focused AI | `{{savings}}`, `{{case_study_line}}` | 63.9% | 5.6% |
| Conditional logic | `{{#if cuisine}}...{{else}}...{{/if}}` | 61.8% | 5.9% |
| Specific enriched data | `{{rating}}`, `{{review_line}}`, `{{competitor}}` | 44.2% | 4.7% |
| Standard variables only | `{{first_name}}`, `{{company_name}}` | 28.7% | 27.7% |
| **Generic AI first-line** | `{{ai_generated_first_line}}` | **4.5%** | **71.0%** |

#### Scoring Matrix

| Variables used | Points |
|---|---|
| No variables | 10 |
| 1 standard variable | 25 |
| 2 standard variables | 35 |
| 3+ standard variables | 45 |
| Any + 1 enriched | 55 |
| Any + 2+ enriched | 65 |
| Any + 1 targeted AI | 75 |
| Any + 2+ targeted AI | 85 |
| Conditional logic present | 95 |
| Generic AI first-line present | **15 (PENALISED)** |

#### The Core Rule

**AI should describe YOUR value for THEM — not describe THEIR company.**

| Generic AI (71% poor) | Targeted AI (63.9% top performer) |
|---|---|
| "I saw your company helps customers with X" | "{{company_name}} could save {{savings}} by switching to [approach]" |
| AI scrapes their website | AI calculates their potential benefit |
| Describes what they do | Describes what you can do for them |

**What works — targeted product-focused AI:**
- `{{company_name}} could save {{savings}} annually by switching to [your approach]...`
- `Saw a case study about {{case_study_line}} — thought it might resonate...`

**What works — specific enriched data:**
- `Saw {{company_name}} has a {{rating}}-star rating on Google...`
- `Noticed '{{review_line}}' in one of your reviews...`
- `Your competitor {{competitor}} recently started using...`

**What fails — generic AI observations:**
- `I saw that {{company_name}} helps customers with {{ai_summary}}...`
- `I noticed your company is doing great things in [industry]...`

---

## Risk Assessment

### Anti-Pattern Flags

#### Critical (+30 pts to risk score each)

**GENERIC_AI_COMPANY_OBSERVATION**
AI-generated description of the prospect's company used as an opener. Impact: 71% poor rate, 4x fewer positive replies than baseline. Fix: remove entirely, replace with targeted product AI or enriched data.

**SPAM_CRITICAL**
Multiple spam trigger words (act now, limited time offer, free money, guaranteed results, winner, congratulations). Impact: likely filtered to spam.

#### Error (+15 pts each)

**FAKE_REPLY** — Subject starts with "Re:" or "Fwd:" on first email.

**NO_PERSONALIZATION** — Zero `{{variables}}` in any sequence. 2-3x worse response rates.

**MISSING_CTA** — No question or clear ask at the end. Reduces response rate ~40%.

#### Warning (+7 pts each)

**WEAK_CTA** — Passive language: "if you're interested", "when you have time", "no rush", "feel free". 50% fewer responses than direct asks.

**EMAIL_TOO_LONG** — Average >300 words. Optimal is ~70 words.

**EMAIL_TOO_SHORT** — First email <30 words.

**BUMP_ONLY_FOLLOWUPS** — Follow-ups only say "following up" or "checking in" with no new value.

**NO_BREAKUP_EMAIL** — Final sequence doesn't signal closure.

**LOW_DM_CONCENTRATION** — Decision-maker % below 30%.

**SCATTERED_TARGETING** — Top industry below 15% of audience.

**ENTERPRISE_TARGET** — Primary target is 10,000+ employee companies.

**TOO_MANY_SEQUENCES** — More than 3 follow-ups.

**NO_FOLLOWUP_PLAN** — No second touch planned.

### Common Anti-Pattern Combinations

**"Spray and Pray":** SCATTERED_TARGETING + NO_PERSONALIZATION + BUMP_ONLY_FOLLOWUPS
Fix: focus targeting, add personalization, write value-add follow-ups.

**"Robot Writer":** AI_WHOLE_EMAIL + NO_PERSONALIZATION + WEAK_CTA
Fix: use AI for data points, not entire emails. Add human touches.

**"Enterprise Mistake":** ENTERPRISE_TARGET + EMAIL_TOO_LONG + TOO_MANY_SEQUENCES
Fix: target SMB, shorten emails, keep to 1-2 value-add follow-ups.

**"AI Personalization Trap":** GENERIC_AI_COMPANY_OBSERVATION + basic variables
Fix: remove generic AI observations. Use enriched data or targeted product-focused AI instead.

### Risk Score

```
Risk Score = (critical_flags × 30) + (error_flags × 15) + (warning_flags × 7)
```

| Score | Level | Action |
|---|---|---|
| 0-19 | Low | Proceed |
| 20-39 | Medium | Address issues before launch |
| 40-59 | High | Major revisions recommended |
| 60+ | Critical | Do not launch |

**Risk adjustments to final score:** Low: 0, Medium: -5, High: -10, Critical: -15

---

## Score to Rating

| Score | Rating | Action |
|---|---|---|
| 80-100 | Excellent | Launch with confidence |
| 65-79 | Above Average | Minor improvements possible |
| 50-64 | Average | Consider rewrites |
| 35-49 | Below Average | Rewrites recommended |
| 0-34 | Poor | Do not launch without rewriting |

---

## Output Report Format

```markdown
# Cold Email Copy Grade Report

## Executive Summary
- **Predicted Score**: XX/100
- **Rating**: [Excellent / Above Average / Average / Below Average / Poor]
- **Risk Level**: [Low / Medium / High / Critical]

## Score Breakdown

| Factor | Score | Weight | Contribution |
|---|---|---|---|
| Copywriting | XX/100 | 40% | XX pts |
| Targeting | XX/100 | 35% | XX pts |
| Variables | XX/100 | 25% | XX pts |
| **Total** | | | **XX/100** |

## Strengths
1. **[Strength]** — [explanation with data]
2. **[Strength]** — [explanation with data]

## Risk Factors
1. **[SEVERITY: FLAG_NAME]** — [explanation + how to fix]
2. **[SEVERITY: FLAG_NAME]** — [explanation + how to fix]

## Variable Analysis

| Variable | Type | Assessment |
|---|---|---|
| {{var}} | [standard/enriched/targeted AI/generic AI] | [Good/Flag] |

## Benchmark Comparison

| Metric | This Campaign | Top Performers | Average |
|---|---|---|---|
| Subject pattern | [detected] | question | ultra-short |
| Subject length | XX chars | 42 chars | 47 chars |
| Body word count | XX words | ~70 words | ~71 words |
| Sequences | X | 4-5 | 3 |
| Paragraphs per email | X | 2-3 | 3 |

---

## Rewrites (if score < 65)

### Improved Subject Line
**Original**: [original]
**Rewritten**: [new version — question format + personalization]
**Why**: [data-backed reason]

### Improved Email Body (Sequence 1)
**Issues**: [list]
**Rewritten copy**: [full rewrite]
**Key changes**: [change 1 with data backing], [change 2], [change 3]

### Improved Follow-ups
[Rewritten sequences 2-N]

### Predicted Score After Rewrites

| Factor | Before | After | Change |
|---|---|---|---|
| Copywriting | XX | XX | +XX |
| Targeting | XX | XX | +XX |
| Variables | XX | XX | +XX |
| **Total** | **XX** | **XX** | **+XX** |

---

## Quick Action Checklist
- [ ] [Most impactful fix]
- [ ] [Second most impactful fix]
- [ ] [Third fix]
```

---

## Rewrite Rules (When Score < 65)

**Subject lines:**
1. Convert to question format (+18% response rate)
2. Add `{{company_name}}` (+2x top-performer rate)
3. Lowercase start (+14%)
4. Under 50 characters
5. Never use "Re:" or "Fwd:" deceptively

**Body copy:**
1. Target ~70 words
2. Open with a specific observation, not generic AI
3. End with a direct question CTA
4. 2-3 paragraphs max
5. P-A-S structure: Problem, Agitate, Solve
6. Never use generic AI first-line variables

**Sequences:**
1. 1 opener + 1-2 value-add follow-ups max
2. Each follow-up adds new value (case study, different angle, proof)
3. Never write bump-only follow-ups
4. Final follow-up should be a breakup email
5. Space 2-5 days apart
6. Use conditional logic for missing data: `{{#if variable}}...{{else}}...{{/if}}`

---

## Quality Checklist Before Outputting

- [ ] Risk flags align with detected patterns
- [ ] Score components sum correctly with weights
- [ ] Risk adjustment applied to final score
- [ ] Rewrites use question-format subject line
- [ ] Rewrites target ~70 words
- [ ] Rewrites contain no generic AI company observations
- [ ] Benchmark comparison uses correct reference numbers
- [ ] Quick action checklist ordered by impact
