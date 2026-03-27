---
name: prospeo-full-export
description: Export an entire Prospeo people search to CSV via the API. Handles pagination, rate limiting, deduplication, and automatic state-by-state splitting for US searches over 25K results. Trigger when the user wants to export Prospeo results, pull a lead list from Prospeo, or run a bulk Prospeo search.
---

# Prospeo Full Search Export

Export your entire Prospeo search to CSV. Build the search in Prospeo's UI, describe the filters to Claude, and Claude generates and runs a TypeScript script that paginates through every result — including searches over 25,000.

---

## Step 1: Collect What You Need

Ask the user for:

1. **Filters** — what they set in the Prospeo UI (titles, location, industry, headcount, etc.)
2. **Result count** — total shown in the Prospeo UI (used to estimate credits and decide on state splitting)
3. **API key** — confirm `PROSPEO_API_KEY` is set as an environment variable

Then confirm the estimated credit cost before running.

---

## Step 2: Credit Cost Estimate

Show this before generating the script:

| Total Results | Approx Pages | Credits | Notes |
|---|---|---|---|
| 1,000 | 40 | ~40 | Simple run |
| 5,000 | 200 | ~200 | Simple run |
| 25,000 | 1,000 | ~1,000 | Max for single search |
| 25,000+ | varies | varies | Use state-by-state split |

1 credit = 1 API request = 25 results.

Ask: "This will use approximately X credits. Does that work for you?"

---

## Step 3: Filter Reference

Map the user's description to Prospeo API filter keys:

| What the user says | API filter key | Format |
|---|---|---|
| Job title | `person_job_title` | `{ include: ["CEO", "CTO"], exclude: ["Intern"] }` |
| Location | `person_location_search` | `{ include: ["California, United States #US"] }` |
| Industry | `company_industry` | `{ include: ["Information Technology"] }` |
| Company size / headcount | `company_headcount_custom` | `{ min: 11, max: 500 }` |
| Technology they use | `company_technology` | `{ include: ["Salesforce", "HubSpot"] }` |
| Revenue | `company_revenue_custom` | `{ min: 1000000, max: 50000000 }` |
| Founded year | `company_founding_year` | `{ min: 2015, max: 2024 }` |
| Company name | `company_name` | `{ include: ["Acme"] }` |
| Company domain | `company_domain` | `{ include: ["acme.com"] }` |
| Has verified email | `person_contact_details` | `{ email: ["VERIFIED"] }` |
| Has phone number | `person_contact_details` | `{ mobile: ["TRUE"] }` |
| Exact title match | `person_job_title` | `{ include: [...], match_only_exact_job_titles: true }` |

### Location Format

Must use this exact format — include the country code hash suffix:

- Country: `"United States #US"`, `"United Kingdom #GB"`, `"Canada #CA"`
- State: `"California, United States #US"`, `"Texas, United States #US"`
- City: `"San Francisco, California, United States #US"`

### Headcount Ranges

| Label | min | max |
|---|---|---|
| 1-10 | 1 | 10 |
| 11-50 | 11 | 50 |
| 51-200 | 51 | 200 |
| 201-500 | 201 | 500 |
| 501-1000 | 501 | 1000 |
| 1001-5000 | 1001 | 5000 |
| 5001+ | 5001 | (omit max) |

---

## Step 4: Generate and Run the Script

Create `prospeo-export.ts` with the filters filled in. Use this template:

```typescript
import { writeFileSync } from 'fs';

const API_KEY = process.env.PROSPEO_API_KEY;
if (!API_KEY) {
  console.error('Error: PROSPEO_API_KEY environment variable is not set.');
  process.exit(1);
}

const RATE_LIMIT_MS = 500; // 2 requests/sec
const MAX_RETRIES = 5;

interface ProspeoFilters {
  person_job_title?: { include?: string[]; exclude?: string[]; match_only_exact_job_titles?: boolean };
  person_location_search?: { include?: string[]; exclude?: string[] };
  company_headcount_custom?: { min?: number; max?: number };
  company_industry?: { include?: string[]; exclude?: string[] };
  company_technology?: { include?: string[]; exclude?: string[] };
  company_revenue_custom?: { min?: number; max?: number };
  company_founding_year?: { min?: number; max?: number };
  company_name?: { include?: string[]; exclude?: string[] };
  company_domain?: { include?: string[]; exclude?: string[] };
  person_contact_details?: { email?: string[]; mobile?: string[]; operator?: string };
}

const sleep = (ms: number) => new Promise(r => setTimeout(r, ms));

async function searchPage(filters: ProspeoFilters, page: number, retries = 0): Promise<any> {
  await sleep(RATE_LIMIT_MS);
  const res = await fetch('https://api.prospeo.io/search-person', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json', 'X-KEY': API_KEY! },
    body: JSON.stringify({ page, filters }),
  });

  if (res.status === 429 && retries < MAX_RETRIES) {
    const backoff = Math.min(2000 * Math.pow(2, retries), 60_000);
    console.log(`  Rate limited — waiting ${backoff / 1000}s...`);
    await sleep(backoff);
    return searchPage(filters, page, retries + 1);
  }

  if (!res.ok) throw new Error(`API error: ${res.status} ${res.statusText}`);
  return res.json();
}

// Dedup by LinkedIn URL and email
const seenLinkedIn = new Set<string>();
const seenEmail = new Set<string>();

function isDuplicate(person: any): boolean {
  const li = person.linkedin_url;
  const em = person.email;
  if (li && seenLinkedIn.has(li)) return true;
  if (em && seenEmail.has(em)) return true;
  if (li) seenLinkedIn.add(li);
  if (em) seenEmail.add(em);
  return false;
}

function escapeCSV(val: any): string {
  if (val == null) return '';
  const s = String(val);
  return s.includes(',') || s.includes('"') || s.includes('\n')
    ? `"${s.replace(/"/g, '""')}"` : s;
}

const CSV_HEADERS = [
  'first_name', 'last_name', 'full_name', 'job_title',
  'email', 'email_status', 'phone', 'linkedin_url',
  'person_city', 'person_state', 'person_country',
  'company_name', 'company_domain', 'company_linkedin', 'company_industry',
  'company_headcount', 'company_headcount_range', 'company_technologies',
  'company_city', 'company_state', 'company_country',
];

function resultToRow(r: any): string[] {
  const p = r.person ?? {};
  const c = r.company ?? {};
  return [
    p.first_name, p.last_name, p.full_name, p.current_job_title,
    p.email, p.email_status, p.phone, p.linkedin_url,
    p.location?.city, p.location?.state, p.location?.country,
    c.name, c.domain, c.linkedin_url, c.industry,
    c.headcount, c.headcount_range,
    (c.technologies ?? []).join('; '),
    c.location?.city, c.location?.state, c.location?.country,
  ];
}

function writeCSV(outputFile: string, rows: string[][]) {
  const lines = [CSV_HEADERS.join(','), ...rows.map(row => row.map(escapeCSV).join(','))];
  writeFileSync(outputFile, lines.join('\n'), 'utf-8');
  console.log(`\nDone.`);
  console.log(`  File: ${outputFile}`);
  console.log(`  Contacts: ${rows.length.toLocaleString()}`);
}

// State-by-state splitting for US searches > 20K results
const US_STATES = [
  'California', 'Texas', 'Florida', 'New York', 'Illinois', 'Pennsylvania',
  'Ohio', 'Georgia', 'North Carolina', 'Michigan', 'New Jersey', 'Virginia',
  'Washington', 'Arizona', 'Massachusetts', 'Tennessee', 'Indiana', 'Missouri',
  'Maryland', 'Wisconsin', 'Colorado', 'Minnesota', 'South Carolina', 'Alabama',
  'Louisiana', 'Kentucky', 'Oregon', 'Oklahoma', 'Connecticut', 'Utah', 'Iowa',
  'Nevada', 'Arkansas', 'Mississippi', 'Kansas', 'New Mexico', 'Nebraska',
  'Idaho', 'West Virginia', 'Hawaii', 'New Hampshire', 'Maine', 'Montana',
  'Rhode Island', 'Delaware', 'South Dakota', 'North Dakota', 'Alaska',
  'Vermont', 'Wyoming',
];

async function exportByState(filters: ProspeoFilters, outputFile: string, maxResults?: number) {
  const rows: string[][] = [];

  for (let i = 0; i < US_STATES.length; i++) {
    if (maxResults && rows.length >= maxResults) break;
    const state = US_STATES[i];
    const stateFilters: ProspeoFilters = JSON.parse(JSON.stringify(filters));
    stateFilters.person_location_search!.include =
      stateFilters.person_location_search!.include!.map(loc =>
        loc === 'United States #US' ? `${state}, United States #US` : loc
      );

    const first = await searchPage(stateFilters, 1);
    const total = first.pagination?.total_count ?? 0;
    const pages = first.pagination?.total_page ?? 0;

    if (total === 0) {
      console.log(`  [${i + 1}/50] ${state}: 0 results`);
      continue;
    }

    for (const r of first.results ?? []) {
      if (!isDuplicate(r.person)) rows.push(resultToRow(r));
    }

    for (let page = 2; page <= pages; page++) {
      if (maxResults && rows.length >= maxResults) break;
      const data = await searchPage(stateFilters, page);
      for (const r of data.results ?? []) {
        if (!isDuplicate(r.person)) rows.push(resultToRow(r));
      }
    }

    console.log(`  [${i + 1}/50] ${state}: ${total.toLocaleString()} — running total: ${rows.length.toLocaleString()}`);
  }

  writeCSV(outputFile, rows);
}

async function exportSearch(filters: ProspeoFilters, outputFile: string, maxResults?: number) {
  console.log('Checking result count...');
  const first = await searchPage(filters, 1);

  if (first.error) {
    console.error('API error:', first.message);
    process.exit(1);
  }

  const totalCount = first.pagination?.total_count ?? 0;
  const totalPages = first.pagination?.total_page ?? 0;
  const pagesToFetch = maxResults ? Math.min(Math.ceil(maxResults / 25), totalPages) : totalPages;

  console.log(`Found ${totalCount.toLocaleString()} results (${totalPages} pages)`);
  console.log(`Fetching ${pagesToFetch} pages (~${pagesToFetch} credits)`);

  // Switch to state-by-state if US-wide search exceeds 20K
  if (
    totalCount > 20_000 &&
    filters.person_location_search?.include?.includes('United States #US')
  ) {
    console.log('Over 20K results — switching to state-by-state mode...');
    await exportByState(filters, outputFile, maxResults);
    return;
  }

  const rows: string[][] = [];
  for (const r of first.results ?? []) {
    if (!isDuplicate(r.person)) rows.push(resultToRow(r));
  }
  console.log(`  Page 1/${pagesToFetch} — ${rows.length} contacts`);

  for (let page = 2; page <= pagesToFetch; page++) {
    if (maxResults && rows.length >= maxResults) break;
    const data = await searchPage(filters, page);
    for (const r of data.results ?? []) {
      if (!isDuplicate(r.person)) rows.push(resultToRow(r));
    }
    if (page % 50 === 0 || page === pagesToFetch) {
      console.log(`  Page ${page}/${pagesToFetch} — ${rows.length.toLocaleString()} contacts`);
    }
  }

  writeCSV(outputFile, rows);
}

// --- FILL IN FILTERS BELOW ---
const filters: ProspeoFilters = {
  // FILTERS_GO_HERE
};

const outputFile = 'prospeo-export.csv';
exportSearch(filters, outputFile);
```

Run with: `npx tsx prospeo-export.ts`

---

## Step 5: State-by-State Splitting (for 25K+ results)

Prospeo caps any single search at 25,000 results (1,000 pages × 25 per page). For US-wide searches exceeding this, the script automatically replaces `"United States #US"` with each of the 50 state locations and paginates through each.

This lets you extract hundreds of thousands of results from a single search definition. Deduplication happens by LinkedIn URL and email address across all states.

---

## Troubleshooting

| Error | Cause | Fix |
|---|---|---|
| `PROSPEO_API_KEY not set` | Missing env var | `export PROSPEO_API_KEY="your_key"` |
| `API error: 401` | Invalid key | Check key at prospeo.io/app/settings/api |
| `API error: 402` | Out of credits | Top up your Prospeo account |
| `API error: 429` | Rate limited | Script retries automatically; only run one export at a time |
| Results seem too low | Wrong location format | Must include `#US`, `#GB`, etc. (e.g. `"California, United States #US"`) |
| Results seem too low | Title mismatch | Remove `match_only_exact_job_titles` to use fuzzy matching |

---

## Requirements

- Node.js 18+ (native `fetch` support)
- `npx tsx` to run TypeScript directly (`npm install -g tsx`)
- Prospeo account with API credits and the Search Person API enabled
