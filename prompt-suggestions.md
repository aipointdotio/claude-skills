# GTM Prompt Suggestions
> Prompts for Claude Code GTM tasks — skills-based and general. Copy, adapt, and use.
> Skills are invoked with `/skill-name` syntax in the chat.

---

## Table of Contents
- [Brand Analysis](#brand-analysis)
- [Viral LinkedIn Posts](#viral-linkedin-posts)
- [Lead Qualifier](#lead-qualifier)
- [Personalization Hooks](#personalization-hooks)
- [Cold Email Copy Grader](#cold-email-copy-grader)
- [Prospect Finder](#prospect-finder)
- [Google Maps Scraper](#google-maps-scraper)
- [Prospeo Full Export](#prospeo-full-export)
- [Domain Setup: Dynadot + Zapmail](#domain-setup-dynadot--zapmail)
- [General GTM Strategy](#general-gtm-strategy)

---

## Brand Analysis

```
/brand-analysis

Run a brand analysis for [company name].
Website: [URL]
Focus: positioning, messaging clarity, differentiation vs competitors, and RevOps intelligence for outreach.
```

```
/brand-analysis

I'm about to pitch [company name] — run a full brand analysis before we reach out.
Website: [URL]
Use the RevOps Intelligence section to identify buying triggers and the best conversation entry points.
```

```
/brand-analysis

Analyse [company name] as a potential investment or partner.
Website: [URL]
Industry: [X]
I need the brand health scorecard and an executive summary I can share with my team.
```

---

## Viral LinkedIn Posts

```
/viral-linkedin-posts

Write a LinkedIn post announcing this system/tool/result:
Topic: [e.g. "I built 5 AI agents that replaced my SDR team"]
Audience: [e.g. founders, sales leaders, RevOps]
Key stat or proof point: [X]
Use the agent-based system template.
```

```
/viral-linkedin-posts

Write a LinkedIn post about this trend or shift:
Topic: [e.g. "GTM Engineers are replacing SDRs in B2B sales"]
Old model: [X]
New model: [Y]
My proof point: [X]
Use the trend/shift template with old vs new contrast format.
```

```
/viral-linkedin-posts

Write a LinkedIn post announcing a new feature or tool:
Tool: [e.g. "Claude Code"]
What it can now do: [X]
Why most people don't know about it: [Y]
Use the new feature/tool template.
```

```
/viral-linkedin-posts

I want to offer a free resource in a LinkedIn post to drive comments.
Resource: [e.g. "my full cold outreach playbook"]
Hook angle: [e.g. "I collected X things over Y months and I'm giving it away"]
CTA keyword: [e.g. "PLAYBOOK"]
```

---

## Lead Qualifier

```
/lead-qualifier

Qualify this prospect:
Company: [name]
Website: [URL]
Contact: [name, title] (optional)
```

```
/lead-qualifier

Qualify this prospect against my ICP:
Company: [name]
Website: [URL]
My offer: [describe what you sell and who it's for]
My ICP: [industry, company size, geography, decision-maker title, disqualifiers]
```

```
/lead-qualifier

Qualify this batch of prospects and rank them by fit:
1. [Company] — [website or LinkedIn URL]
2. [Company] — [URL]
3. [Company] — [URL]
My offer: [X]
```

```
/lead-qualifier

I have an icp.md file in this project. Use it to qualify:
Company: [name]
Website: [URL]
```

---

## Personalization Hooks

```
/personalization-hooks

Write a strong hook for this prospect:
Name: [X], Role: [X], Company: [X]
Research: [paste LinkedIn post, article snippet, or podcast quote]
My offer: [one sentence on what you do]
```

```
/personalization-hooks

Write 3 lite hooks for [role, e.g. "VP of Sales at Series B SaaS companies"].
My offer: [X]
Theme to connect to: [e.g. "SDR ramp time", "pipeline quality", "outbound at scale"]
```

```
/personalization-hooks

Turn this LinkedIn post into a strong hook opening line:
[paste post]
My offer: [X]
```

```
/personalization-hooks

I have research on 5 prospects. Write a hook for each — choose Strong or Lite based on what's available:
1. [Name] — [research notes or content]
2. [Name] — [research notes]
...
My offer: [X]
```

---

## Cold Email Copy Grader

```
/cold-email-copy-grader

Grade this cold email campaign:

Targeting: [job titles, industries, company size, geography]
Merge variables: [list all {{variables}} you're using]

--- Email 1 ---
Subject: [subject line]
Body: [email body]

--- Follow-up 2 ---
Subject: [subject]
Body: [body]

--- Break-up email ---
Subject: [subject]
Body: [body]
```

```
/cold-email-copy-grader

Quick check on this email:
[paste single email with subject line]
Targeting: [brief description]
```

```
/cold-email-copy-grader

I'm using AI personalisation in my cold email — grade this and tell me if my first-line approach is a generic AI trap or targeted product-focused AI.

Subject: [X]
Body: [paste email, highlight the AI-generated variable]
Targeting: [job titles, industries, company size]
```

---

## Prospect Finder

```
/prospect-finder

Find prospects matching this ICP:
Industry: [X]
Role/seniority: [e.g. CEO, Head of Sales]
Company size: [e.g. 10–200 employees]
Location: [e.g. United States]
Additional signals: [e.g. using HubSpot, recently funded]
Output: Company, First Name, Last Name, Job Title, Email, LinkedIn URL.
```

```
/prospect-finder

I need [X] qualified leads for this campaign:
Offer: [describe your product/service]
ICP: [describe ideal customer]
Geography: [X]
Priority: decision-makers only (Owner, CEO, VP, Director level).
```

---

## Google Maps Scraper

```
/google-maps-scraper

Set up the Google Maps scraper to find [business type, e.g. "dental practices"] in [location, e.g. "Texas"].
My RapidAPI key is set as RAPIDAPI_KEY.
Filter to zips with population >= [e.g. 5000].
Output to ./output/[filename].csv.
```

```
/google-maps-scraper

Scrape [business type] listings across these zip codes: [list zips].
I want name, address, phone, website, star rating, and review count.
Deduplicate by place_id and export to CSV.
```

```
/google-maps-scraper

Build the full Google Maps scraper project from scratch.
I want to scrape [business type] across [state/city].
Walk me through setup, then run the first scrape for [location] as a test.
```

---

## Prospeo Full Export

```
/prospeo-full-export

Export my Prospeo search to CSV.
My filters: [describe what you set in the Prospeo UI — job titles, location, industry, headcount, etc.]
Estimated result count from the UI: [X]
My PROSPEO_API_KEY is set as an environment variable.
```

```
/prospeo-full-export

I need to export a large US-wide Prospeo search.
Filters: [titles, industries, headcount range, contact details requirements]
The UI shows approximately [X] results — use state-by-state splitting.
Confirm credit cost estimate before running.
```

```
/prospeo-full-export

Export Prospeo results with these filters:
- Job titles: [e.g. CEO, Founder, Managing Director]
- Location: [e.g. United States]
- Company size: [e.g. 11–500 employees]
- Industry: [e.g. Information Technology]
- Must have verified email: yes
Tell me the estimated credit cost first.
```

---

## Domain Setup: Dynadot + Zapmail

```
/domain-setup-dynadot-zapmail

Set up [X] cold email domains for brand keyword "[your brand]".
TLD preference: .info
I need 2 inboxes per domain. Sender name: [First Last].
My DYNADOT_API_KEY and ZAPMAIL_API_KEY are set.
Run the full end-to-end workflow.
```

```
/domain-setup-dynadot-zapmail

Generate domain name candidates for brand "[X]" and check availability on Dynadot.
I need [X] clean, short domains (aim for tier 1 and tier 2 only).
Max price: $3.50 per domain. TLD: .info
Show me the available list before purchasing anything.
```

```
/domain-setup-dynadot-zapmail

I've already purchased domains on Dynadot. Pick up from step 4:
Switch nameservers to Zapmail, connect domains, create inboxes for sender [First Last], then export to [Smartlead / Instantly / other platform].
Domain list: [paste domains]
```

---

## General GTM Strategy

```
What's the best channel mix for reaching [ICP] with [offer]?
Consider: cold email, LinkedIn, cold call, paid ads.
Budget constraint: [X]. Team size: [X SDRs / solo].
```

```
I'm launching a new campaign for [segment].
Walk me through the full setup checklist: ICP, lead source, enrichment, messaging, sequence, and tracking.
```

```
My [open / reply / meeting booked] rate dropped from [X]% to [Y]%.
Campaign: [describe]. What are the most likely causes and what should I test first?
```

```
We're pivoting the offer for [segment] from [old offer] to [new offer].
What needs to change in: messaging, targeting, lead source, and sequence?
```

```
Write a one-pager for [offer] aimed at [ICP].
Include: problem, solution, proof/outcome, and CTA.
Keep it under 300 words. Tone: [e.g. direct, consultative].
```

```
Review this outreach strategy for [segment] and identify the top 3 weaknesses:
[paste your strategy or sequence outline]
```

---

> **Note:** Always specify Company, First Name, Last Name as separate columns in any list output.
