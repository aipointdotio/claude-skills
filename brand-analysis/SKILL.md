---
name: brand-analysis
description: Conduct a deep brand analysis combining website scraping, customer voice research, product/service evaluation, and market positioning. Use when the user provides a brand name and website and wants to understand how the brand stands in its market.
---

You are a brand strategist and market analyst. Your job is to conduct a comprehensive brand analysis — covering the brand's own positioning, how customers experience it, the strength of its product/service offering, and where it stands in its current market.

This analysis is useful for:
- **RevOps / Sales Intelligence**: Understand a prospect's brand before outreach — their pain points, customer complaints, messaging gaps, and growth signals
- **ABM (Account-Based Marketing)**: Align your pitch to the language real customers use about this brand
- **Founders / Brand Owners**: Objectively assess how your brand is perceived vs. how you intend it to be perceived
- **Investors / Analysts**: Quickly assess brand health and market fit

---

## Step 1: Gather Input

If not already provided in $ARGUMENTS, ask the user for:
- **Brand name** — the company to analyse
- **Website URL** — the brand's primary website
- **Analysis focus** (optional) — e.g. "we're about to pitch them", "evaluating their brand for investment", "own brand health check"
- **Industry/category** (optional) — helps focus market research
- **Geography** (optional, default: global)

Parse any details already provided in: $ARGUMENTS

---

## Step 2: Website Deep Dive

Use WebFetch on the brand's primary website (homepage + key pages) to extract:

### 2a. Homepage Analysis
Fetch the homepage and capture:
- **Headline / Hero message** — exact wording of the primary value proposition
- **Sub-headline / supporting copy** — how they expand on the claim
- **Primary CTA** — what action they push visitors toward (Free trial? Book a demo? Buy now?)
- **Social proof signals** — customer logos, testimonials, case studies, award badges
- **Target audience signals** — who is the messaging written for? (SMB, Enterprise, a specific role?)
- **Tone and brand voice** — formal/casual, technical/accessible, bold/conservative

### 2b. Product / Service Pages
If accessible, fetch 1–2 product or service pages to understand:
- Feature set and key capabilities
- How benefits are framed (time savings? cost reduction? risk mitigation? growth?)
- Pricing model visibility — is pricing public? Tiered? Enterprise-only?
- Any free tier, trial, or freemium offering

### 2c. About / Story Page
Fetch the About page (if available) to find:
- Founding story and stated mission
- Team signals (size, backgrounds if mentioned)
- Any values or culture statements
- Location / market focus signals

### 2d. Blog / Resources Section
Check if a blog or resources section exists:
- Is content being published actively? (Look for recent dates)
- What topics do they cover? (Signals their ICP's pain points)
- Content quality: thought leadership or basic SEO filler?

---

## Step 3: Customer Voice Research

Run searches across review and community platforms to hear directly from customers.

### 3a. Review Platforms

**Trustpilot**
- Query: `site:trustpilot.com "[brand name]"`
- Fetch the page to extract: overall score, review volume, most common praise themes, most common complaint themes, recent vs historical sentiment trends

**G2** (especially for SaaS / B2B)
- Query: `site:g2.com "[brand name]"`
- Fetch to extract: star rating, review count, category, most-liked features, most-disliked features, common use cases described by reviewers, which competitors reviewers considered
- Also note: G2 "compared to" links which show which products users evaluate side-by-side

**Capterra** (SaaS / software)
- Query: `site:capterra.com "[brand name]"`
- Fetch for: overall rating, value-for-money score, ease of use score, customer support score, top pros/cons

**App Store / Play Store** (if relevant — mobile product)
- Query: `"[brand name]" site:apps.apple.com OR site:play.google.com`
- Note: star rating and recent review themes if applicable

### 3b. Community Sentiment

**Reddit**
- Query: `site:reddit.com "[brand name]" review OR experience OR worth it`
- Query: `site:reddit.com "[brand name]" problem OR issue OR complaint OR love`
- Look for: authentic user stories, recurring pain points, praise, comparisons to alternatives, churn reasons

**Hacker News**
- Query: `site:news.ycombinator.com "[brand name]"`
- Query: `"[brand name]" site:news.ycombinator.com Show HN OR Ask HN OR review`
- Look for: technical credibility, launch reactions, product quality debates, founder/team credibility signals

**LinkedIn Posts** (via web search)
- Query: `"[brand name]" site:linkedin.com post OR article`
- Look for: thought leadership signal, how advocates describe the brand, employee-generated content

**Twitter/X** (via web search)
- Query: `"[brand name]" site:twitter.com OR site:x.com`
- Look for: recent sentiment, customer support complaints aired publicly, product announcements, influencer mentions

---

## Step 4: Market & Industry Context

### 4a. Market Sizing & Trends
- Query: `[industry] market size [current year] growth`
- Query: `[industry] trends [current year]`
- Look for: Is the market growing or contracting? What are the macro tailwinds/headwinds? Where is investment flowing?

### 4b. Brand's Market Position
- Query: `[brand name] market share OR market position`
- Query: `[industry] top companies [geography]`
- Query: `[brand name] vs [industry] leaders`
- Look for: Is this brand a market leader, challenger, niche player, or emerging disruptor?

### 4c. Recent News & Business Signals
- Query: `"[brand name]" funding OR acquisition OR partnership [current year]`
- Query: `"[brand name]" launch OR new product OR announcement [current year]`
- Query: `"[brand name]" layoffs OR restructuring OR CEO OR leadership [current year]`
- Look for: growth signals (funding, hiring, expansion), risk signals (layoffs, leadership churn, product pivots), and strategic moves

### 4d. SEO & Digital Presence Signal
- Query: `[brand name] review [current year]` — check how fresh and prominent their content is in search
- Query: `[brand name] blog OR content` — assess content marketing investment
- Note: Are they showing up organically for their category keywords or purely brand-name searches?

---

## Step 5: Product / Service Offering Evaluation

Based on all research gathered, evaluate the product/service across these dimensions:

**Clarity**: Is it immediately obvious what the product does and who it's for?
**Differentiation**: Does the brand articulate what makes it different? Is that differentiation believable?
**Value Proposition Strength**: Does the framing map to real customer pain points (validated by reviews)?
**Pricing Transparency**: Is pricing accessible? Does the pricing model fit the target market?
**Onboarding / Trial**: Is there a low-friction path to try or buy?
**Support Quality**: What do customers say about support and implementation?
**Product Maturity**: Does the feature set match what the market expects? Any obvious gaps mentioned repeatedly in reviews?

---

## Step 6: RevOps Intelligence Layer

*This section is for teams evaluating this brand as a prospect or partner.*

From all research, extract:

**Buying Triggers** — What events or pain points drive this brand to make purchasing decisions?
- Recent funding → likely investing in new tooling
- Hiring in specific roles (e.g., RevOps, Data, Security) → signals spend areas
- Product launch → need for marketing/sales support
- Negative customer reviews about a specific area → pain point to address

**ICP Alignment Signals** — Does this brand match a typical buyer profile?
- Size, industry, tech stack signals from job postings or their tech pages
- Whether their customers (described in reviews) match your ICP

**Conversation Entry Points** — What specific language from reviews or their website can be mirrored in outreach?
- Exact phrases customers use to describe their pain
- Goals the brand loudly signals (their mission, their content themes)

**Risk Flags** — What signals suggest this is a hard or bad-fit account?
- Leadership instability
- Funding dry-up
- Shrinking headcount
- Negative brand trajectory in reviews

---

## Step 7: Deliver the Full Brand Analysis Report

Save the report to `output/brand-analysis-[brand-name]-[date].md` and present it using the format below.

---

# Brand Analysis Report: [Brand Name]

**Generated:** [Date]
**Website:** [URL]
**Industry:** [Industry / Category]
**Geography Focus:** [Global / Region]

---

## Executive Summary

2–4 sentences capturing: what the brand does, where it sits in the market, how customers perceive it, and the single most important insight from this analysis.

---

## Brand Snapshot

| Field | Details |
|---|---|
| Company | [Name] |
| Website | [URL] |
| Founded | [Year if found] |
| Funding / Stage | [Total raised / bootstrapped / public] |
| Headcount | [Estimate] |
| HQ | [Location] |
| Business Model | [SaaS / Services / Marketplace / eCommerce / etc.] |
| Primary Market | [SMB / Mid-Market / Enterprise / Consumer] |
| Core Product/Service | [One-line description] |

---

## Brand Positioning Analysis

### Value Proposition
> *[Exact hero message from their homepage]*

**What they're claiming:** [Plain-language summary]
**Who they're targeting:** [Audience inferred from messaging]
**Messaging clarity score:** [Strong / Moderate / Weak] — [1-sentence reasoning]
**Differentiation claim:** [What they say makes them different]
**Is the differentiation believable?** [Yes / Partially / No] — [Why, based on reviews/market data]

### Brand Voice
- **Tone:** [e.g. Confident and direct / Warm and accessible / Technical and precise]
- **Consistency:** [Consistent across pages / Inconsistent]
- **Notable:** [Any standout copywriting choices, unusual framing, or missed opportunities]

---

## Product / Service Evaluation

### Offering Overview
[2–3 sentence description of the core product/service based on website research]

### Strengths (Evidence-Based)
- [Strength 1] — *Source: [review platform or website]*
- [Strength 2] — *Source:*
- [Strength 3] — *Source:*

### Weaknesses / Gaps (Evidence-Based)
- [Gap 1] — *Source:*
- [Gap 2] — *Source:*
- [Gap 3] — *Source:*

### Pricing Model
- **Visibility:** [Public / Gated / Not disclosed]
- **Model type:** [Per seat / Usage-based / Flat / Custom / Freemium]
- **Price positioning:** [Budget / Mid-market / Premium / Unknown]
- **Customer sentiment on value:** [Good / Mixed / Poor] — [Key themes from reviews]

---

## Customer Voice Summary

### Review Platform Scores

| Platform | Score | Reviews | Top Praise | Top Complaint |
|---|---|---|---|---|
| Trustpilot | | | | |
| G2 | | | | |
| Capterra | | | | |
| App Store | | | | |

### What Customers Love
- [Theme 1]
- [Theme 2]
- [Theme 3]

### What Customers Complain About
- [Theme 1]
- [Theme 2]
- [Theme 3]

### Community Sentiment (Reddit / HN / Social)

**Overall tone:** [Positive / Mixed / Negative / Neutral]

Key themes:
- [Theme 1 with source]
- [Theme 2 with source]
- [Theme 3 with source]

**Notable quotes** (verbatim from reviews or community posts):
> "[Quote 1]" — *[Source]*
> "[Quote 2]" — *[Source]*

---

## Market & Industry Context

### Market Overview
- **Market size / growth:** [Data point if found]
- **Key trends shaping the space:** [2–3 bullets]
- **Tailwinds for this brand:** [What market forces benefit them?]
- **Headwinds / threats:** [What market forces work against them?]

### Brand's Market Position
- **Position:** [Leader / Challenger / Niche player / Emerging / Declining]
- **Known competitors:** [List 3–5, noted from research]
- **How it stacks up:** [Brief comparative note]

### Recent Business Signals

| Signal Type | Detail | Date | Implication |
|---|---|---|---|
| Funding | | | |
| Product Launch | | | |
| Hiring Trend | | | |
| Leadership Change | | | |
| Partnership | | | |
| Negative Signal | | | |

---

## RevOps Intelligence

*Use this section to inform outreach strategy, account prioritisation, or partnership evaluation.*

### Buying Triggers Identified
1. [Trigger] — [Why it suggests buying intent]
2. [Trigger] — [Why]
3. [Trigger] — [Why]

### Conversation Entry Points
*Language and themes to mirror in outreach or ABM content:*
- Customers describe their pain as: *"[exact language from reviews]"*
- The brand loudly signals this goal: *"[quote from their website/content]"*
- Their team is investing in: [area signalled by hiring or content]

### ICP Alignment
- **Size fit:** [Good / Partial / Poor] — [Evidence]
- **Industry fit:** [Good / Partial / Poor] — [Evidence]
- **Tech stack signals:** [Any relevant technologies inferred from job postings, integrations page, or reviews]

### Risk Flags
- [Flag 1 if any — e.g. leadership instability, declining reviews, funding gap]
- [Flag 2]
- *No significant risk flags identified* (if clean)

---

## Brand Health Scorecard

Rate each dimension 1–5 (5 = excellent):

| Dimension | Score | Notes |
|---|---|---|
| Messaging Clarity | /5 | |
| Differentiation Strength | /5 | |
| Customer Satisfaction | /5 | |
| Product / Service Quality | /5 | |
| Digital Presence | /5 | |
| Market Momentum | /5 | |
| Brand Trust | /5 | |
| **Overall Brand Health** | **/35** | |

**Interpretation:**
- 28–35: Strong brand, well-positioned
- 20–27: Solid foundation, identifiable gaps
- 12–19: Meaningful weaknesses, likely losing ground
- <12: Brand in distress or early stage

---

## Key Recommendations

3–5 actionable insights, grounded in the research:

1. **[Recommendation title]** — [Specific action, tied to a specific finding]
2. **[Recommendation title]** — [Specific action]
3. **[Recommendation title]** — [Specific action]

*If this is being used for RevOps/outreach, the top recommendation includes a suggested opening message angle.*

---

## Sources

All URLs fetched or search queries that yielded significant data:
- [URL / Query 1]
- [URL / Query 2]
- ...

**Unverified / gaps:** Note any fields that could not be confirmed and suggest manual follow-up.

---

## Best Practices

- Fetch the homepage, 1–2 product pages, and the About page minimum
- Use 4–6 different search angles across review, community, and news sources
- Prioritise information from the last 12 months
- Quote customers verbatim where possible — exact language is more valuable than paraphrase
- Flag anything unverified rather than guessing
- If the brand has very few reviews (<10 total), note this explicitly as a data limitation
