# Claude Skills

A collection of Claude Code skills for B2B sales, cold outreach, and market research.

## Skills

### Brand Analysis
[`brand-analysis/`](brand-analysis/)

Deep brand analysis combining website scraping, customer voice research, and market positioning. Useful for sales intelligence before outreach, ABM campaigns, or evaluating a brand as a prospect or investment.

**Trigger:** Provide a brand name and website URL.

---

### Viral LinkedIn Post System
[`viral-linkedin-posts/`](viral-linkedin-posts/)

Writes LinkedIn posts using hook patterns and frameworks proven to generate high engagement. Covers 6 hook patterns, structure rules, tone guidelines, and ready-to-use templates.

**Trigger:** Ask to write a LinkedIn post, draft content, or get help with hooks and CTAs.

---

### Cold Outbound Skills
[`coldoutboundskills/`](coldoutboundskills/)

#### Lead Qualifier
[`coldoutboundskills/lead-qualifier/`](coldoutboundskills/lead-qualifier/)

Qualifies a prospect by researching their website, LinkedIn, funding, hiring signals, and reviews, then scoring them against your offer and ICP. Loads ICP criteria from an `icp.md` file if one exists, otherwise asks you to describe your offer. Works on single prospects or a batch list.

**Trigger:** Ask to qualify a lead, check ICP fit, or decide whether a prospect is worth reaching out to.

---

#### Personalization Hooks
[`coldoutboundskills/personalization-hooks/`](coldoutboundskills/personalization-hooks/)

Writes personalised opening lines for cold outreach using Strong Hook (direct quote) or Lite Hook (conceptual tie) patterns. Chooses the right type based on deal size and available research.

**Trigger:** Ask for a personalised opening line, a hook for an email or LinkedIn message, or help turning research into a first line.

---

#### Cold Email Copy Grader
[`coldoutboundskills/cold-email-copy-grader/`](coldoutboundskills/cold-email-copy-grader/)

Grades cold email campaign copy on a 0–100 scale using patterns from 1,000+ real B2B campaigns. Returns a score, risk flags, benchmark comparisons, and full rewrites when the score is below 65.

**Trigger:** Paste draft email sequences and targeting info.

---

#### Domain Setup: Dynadot + Zapmail
[`coldoutboundskills/domain-setup-dynadot-zapmail/`](coldoutboundskills/domain-setup-dynadot-zapmail/)

End-to-end workflow for setting up cold email sending domains: generates domain name candidates, checks availability, purchases on Dynadot, switches nameservers, connects to Zapmail, creates inboxes, and exports to your sending platform.

**Requires:** `DYNADOT_API_KEY` and `ZAPMAIL_API_KEY` environment variables.

---

#### Google Maps Scraper
[`coldoutboundskills/google-maps-scraper/`](coldoutboundskills/google-maps-scraper/)

Scrapes Google Maps business listings by query and location (zip code, city, or US state). Returns structured data including name, address, phone, website, rating, and review count. Exports to CSV. Can run as a CLI or be deployed as a web app.

**Requires:** `RAPIDAPI_KEY` — RapidAPI subscription to the [Maps Data API](https://rapidapi.com/alexanderxbx/api/maps-data).

---

#### Prospeo Full Export
[`coldoutboundskills/prospeo-full-export/`](coldoutboundskills/prospeo-full-export/)

Exports entire Prospeo people searches to CSV via the API. Handles pagination, rate limiting, deduplication, and automatic state-by-state splitting for US searches over 25K results.

**Requires:** `PROSPEO_API_KEY` environment variable.

---

### Prospect Finder
[`prospect-finder/`](prospect-finder/)

Finds and qualifies prospects matching your ICP.

---

## Setup

Most skills that call external APIs read credentials from environment variables. Add the relevant keys to your shell profile or a `.env` file in the project root:

```bash
export DYNADOT_API_KEY=your_key
export ZAPMAIL_API_KEY=your_key
export RAPIDAPI_KEY=your_key
export PROSPEO_API_KEY=your_key
```

Never commit `.env` files — they are excluded by `.gitignore`.
