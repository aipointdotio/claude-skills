---
name: google-maps-scraper
description: Set up and run a Google Maps business scraper using the RapidAPI Maps Data API. Returns structured business data (name, address, phone, website, rating, reviews, coordinates) exported to CSV. Trigger when someone wants to scrape Google Maps, find business listings, or build a prospect list from local businesses.
---

# Google Maps Scraper

Scrape business listings from Google Maps by query and location. Outputs a CSV with name, address, phone, website, star rating, and review count. Works as a CLI tool or optional web app.

## Prerequisites

1. **Node.js 18+** and **npm**
2. **RapidAPI key** with a subscription to the Maps Data API (`https://rapidapi.com/alexanderxbx/api/maps-data`)
3. Set the key as an environment variable: `export RAPIDAPI_KEY=your_key_here`

---

## Step 1: Understand What the User Wants

Ask (if not already provided):
- **What type of business?** (e.g. "dentist", "pizza restaurant", "gym")
- **What location?** (zip codes, city names, US state, or coordinates)
- **How many results?** (each location search returns up to 20 results)
- **Output file path?** (default: `./output/results.csv`)

For state-wide or large scrapes, remind the user this takes time — Texas has 2,500+ zip codes.

---

## Step 2: Project Setup

If the project doesn't exist yet, scaffold it:

```bash
mkdir google-maps-scraper && cd google-maps-scraper
npm init -y
npm install typescript bottleneck
npm install -D @types/node tsx
```

`package.json` scripts:
```json
{
  "scripts": {
    "scrape": "tsx src/index.ts",
    "serve": "tsx src/server.ts"
  }
}
```

`tsconfig.json`:
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "esModuleInterop": true,
    "strict": true,
    "outDir": "dist",
    "rootDir": "src",
    "skipLibCheck": true
  },
  "include": ["src"]
}
```

Add `.env` to `.gitignore`. Create `.env`:
```
RAPIDAPI_KEY=your_key_here
```

---

## Step 3: File Structure

```
google-maps-scraper/
  data/
    us-zip-codes.csv        # 42,734 US zip codes (bundled)
  src/
    types.ts                # TypeScript interfaces
    client.ts               # RapidAPI client with rate limiting + retries
    csv.ts                  # CSV export
    zips.ts                 # Zip code loader (filter by state, city, population)
    index.ts                # CLI entry point
    server.ts               # Optional web app (Express)
  .env
  package.json
  tsconfig.json
```

---

## Step 4: Source Files

### src/types.ts

```typescript
export interface SearchParams {
  query: string;
  lat?: number;
  lng?: number;
  limit?: number;    // max 20
  zoom?: number;     // default 13 = neighbourhood
  country?: string;  // default "us"
}

export interface Place {
  place_id: string;
  name: string;
  address: string;
  lat: number;
  lng: number;
  rating?: number;
  reviews_count?: number;
  phone?: string;
  website?: string;
  types?: string[];
  category?: string;
}
```

### src/client.ts

```typescript
import Bottleneck from 'bottleneck';
import type { SearchParams, Place } from './types.js';

interface RawResult {
  place_id?: string;
  title?: string;
  name?: string;
  address?: string;
  latitude?: number;
  longitude?: number;
  rating?: number;
  reviews?: number;
  phone?: string;
  website?: string;
  types?: string[];
  type?: string;
  category?: string;
}

export class GoogleMapsClient {
  private limiter: Bottleneck;
  private apiKey: string;
  private host = 'maps-data.p.rapidapi.com';
  private maxRetries: number;

  constructor(opts: { apiKey: string; requestsPerSecond?: number; maxRetries?: number }) {
    this.apiKey = opts.apiKey;
    this.maxRetries = opts.maxRetries ?? 3;
    this.limiter = new Bottleneck({
      maxConcurrent: 1,
      minTime: Math.floor(1000 / (opts.requestsPerSecond ?? 2)),
    });
  }

  async search(params: SearchParams): Promise<Place[]> {
    const response = await this.request<{ data?: RawResult[]; error?: string }>('searchmaps.php', {
      query: params.query,
      limit: String(params.limit ?? 20),
      country: params.country ?? 'us',
      ...(params.lat != null && { lat: String(params.lat) }),
      ...(params.lng != null && { lng: String(params.lng) }),
      ...(params.zoom != null && { zoom: String(params.zoom) }),
    });
    if (response.error) throw new Error(`Search failed: ${response.error}`);
    return (response.data ?? []).map(item => ({
      place_id: item.place_id ?? '',
      name: item.title ?? item.name ?? '',
      address: item.address ?? '',
      lat: item.latitude ?? 0,
      lng: item.longitude ?? 0,
      rating: item.rating,
      reviews_count: item.reviews,
      phone: item.phone,
      website: item.website,
      types: item.types ?? (item.type ? [item.type] : []),
      category: item.category ?? item.type,
    }));
  }

  async geocode(query: string, country = 'us'): Promise<{ lat: number; lng: number }> {
    const response = await this.request<{ latitude?: number; longitude?: number }>('geocoding.php', {
      query: `${query}, ${country.toUpperCase()}`,
    });
    if (!response.latitude || !response.longitude) throw new Error(`Could not geocode: ${query}`);
    return { lat: response.latitude, lng: response.longitude };
  }

  private async request<T>(endpoint: string, params: Record<string, string>): Promise<T> {
    return this.limiter.schedule(() => this.requestWithRetry<T>(endpoint, params));
  }

  private async requestWithRetry<T>(
    endpoint: string,
    params: Record<string, string>,
    attempt = 0
  ): Promise<T> {
    const url = new URL(`https://${this.host}/${endpoint}`);
    for (const [k, v] of Object.entries(params)) url.searchParams.set(k, v);

    try {
      const res = await fetch(url.toString(), {
        headers: { 'X-RapidAPI-Key': this.apiKey, 'X-RapidAPI-Host': this.host },
      });
      if (!res.ok) {
        const err: any = new Error(`API ${res.status}: ${res.statusText}`);
        err.statusCode = res.status;
        throw err;
      }
      return (await res.json()) as T;
    } catch (err: any) {
      const retryable =
        attempt < this.maxRetries &&
        (err.statusCode === 429 || err.statusCode >= 500 ||
          err.code === 'ECONNRESET' || err.code === 'ETIMEDOUT');
      if (retryable) {
        const delay = 1000 * Math.pow(2, attempt);
        console.log(`  Retry ${attempt + 1}/${this.maxRetries} in ${delay}ms...`);
        await new Promise(r => setTimeout(r, delay));
        return this.requestWithRetry<T>(endpoint, params, attempt + 1);
      }
      throw err;
    }
  }
}
```

### src/csv.ts

```typescript
import { writeFile, mkdir } from 'fs/promises';
import { dirname } from 'path';
import type { Place } from './types.js';

const HEADERS = ['place_id', 'name', 'address', 'phone', 'website', 'rating', 'reviews_count', 'lat', 'lng', 'category'];

function escape(val: string | number | undefined | null): string {
  if (val == null) return '';
  const s = String(val);
  return s.includes(',') || s.includes('"') || s.includes('\n')
    ? `"${s.replace(/"/g, '""')}"` : s;
}

export async function exportCSV(places: Place[], outputPath: string): Promise<void> {
  await mkdir(dirname(outputPath), { recursive: true });
  const lines = [
    HEADERS.join(','),
    ...places.map(p => HEADERS.map(h => escape(p[h as keyof Place])).join(',')),
  ];
  await writeFile(outputPath, lines.join('\n') + '\n', 'utf-8');
}
```

### src/zips.ts

```typescript
import { readFileSync } from 'fs';
import { join, dirname } from 'path';
import { fileURLToPath } from 'url';

export interface ZipEntry {
  zip: string;
  city: string;
  state: string;
  lat: number;
  lng: number;
  population: number;
}

let cache: ZipEntry[] | null = null;

function loadAll(): ZipEntry[] {
  if (cache) return cache;
  const __dirname = dirname(fileURLToPath(import.meta.url));
  const raw = readFileSync(join(__dirname, '..', 'data', 'us-zip-codes.csv'), 'utf-8');
  const lines = raw.trim().split('\n').slice(1);

  cache = lines.map(line => {
    const parts: string[] = [];
    let current = '';
    let inQuotes = false;
    for (const ch of line) {
      if (ch === '"') { inQuotes = !inQuotes; continue; }
      if (ch === ',' && !inQuotes) { parts.push(current); current = ''; continue; }
      current += ch;
    }
    parts.push(current);
    return {
      zip: parts[0]?.padStart(5, '0') ?? '',
      city: parts[1] ?? '',
      state: parts[2] ?? '',
      lat: parseFloat(parts[7]) || 0,
      lng: parseFloat(parts[8]) || 0,
      population: parseInt(parts[9]) || 0,
    };
  }).filter(z => z.zip.length === 5);

  return cache;
}

export function getZipsByState(stateCode: string): ZipEntry[] {
  return loadAll().filter(z => z.state.toUpperCase() === stateCode.toUpperCase());
}

export function getZipsByCity(city: string, state?: string): ZipEntry[] {
  const lower = city.toLowerCase();
  return loadAll().filter(z =>
    z.city.toLowerCase().includes(lower) &&
    (!state || z.state.toUpperCase() === state.toUpperCase())
  );
}

export function getZipsByMinPopulation(minPop: number, state?: string): ZipEntry[] {
  return loadAll().filter(z =>
    z.population >= minPop &&
    (!state || z.state.toUpperCase() === state.toUpperCase())
  );
}

export function getAllZips(): ZipEntry[] {
  return loadAll();
}
```

### src/index.ts

```typescript
import { GoogleMapsClient } from './client.js';
import { exportCSV } from './csv.js';
import { getZipsByState, getZipsByCity, getZipsByMinPopulation } from './zips.js';
import type { Place } from './types.js';

function dedup(places: Place[]): Place[] {
  const seen = new Set<string>();
  return places.filter(p => { if (seen.has(p.place_id)) return false; seen.add(p.place_id); return true; });
}

function getArg(args: string[], prefix: string): string | undefined {
  const match = args.find(a => a.startsWith(prefix));
  return match ? match.split('=').slice(1).join('=') : undefined;
}

async function main() {
  const args = process.argv.slice(2);
  const query   = getArg(args, '--query=');
  const zips    = getArg(args, '--zips=');
  const cities  = getArg(args, '--cities=');
  const state   = getArg(args, '--state=');
  const minPop  = getArg(args, '--min-pop=');
  const limit   = parseInt(getArg(args, '--limit=') ?? '20', 10);
  const output  = getArg(args, '--output=') ?? './output/results.csv';

  if (!query || (!zips && !cities && !state)) {
    console.log(`
Google Maps Scraper

USAGE:
  npm run scrape -- --query="pizza restaurant" --zips=10014,10013
  npm run scrape -- --query="dentist" --state=TX
  npm run scrape -- --query="dentist" --state=TX --min-pop=10000
  npm run scrape -- --query="gym" --cities="Austin TX,Dallas TX"

OPTIONS:
  --query=QUERY      Business type to search (required)
  --zips=ZIP,ZIP     Comma-separated zip codes
  --cities=CITY,CITY Comma-separated cities
  --state=XX         All zip codes in a US state (2-letter code)
  --min-pop=N        Only zips with population >= N (use with --state)
  --limit=N          Results per location, max 20 (default 20)
  --output=PATH      Output CSV path (default ./output/results.csv)

ENVIRONMENT:
  RAPIDAPI_KEY       Required — from https://rapidapi.com/alexanderxbx/api/maps-data
`);
    process.exit(1);
  }

  const apiKey = process.env.RAPIDAPI_KEY;
  if (!apiKey) {
    console.error('Error: RAPIDAPI_KEY environment variable is not set.');
    process.exit(1);
  }

  const locations: string[] = [];
  if (zips) locations.push(...zips.split(',').map(s => s.trim()));
  if (cities) locations.push(...cities.split(',').map(s => s.trim()));
  if (state) {
    const minPopNum = minPop ? parseInt(minPop, 10) : 0;
    const stateZips = minPopNum > 0
      ? getZipsByMinPopulation(minPopNum, state)
      : getZipsByState(state);
    locations.push(...stateZips.map(z => z.zip));
    console.log(`Loaded ${stateZips.length} zip codes for ${state.toUpperCase()}${minPopNum > 0 ? ` (pop >= ${minPopNum.toLocaleString()})` : ''}`);
  }

  if (locations.length === 0) {
    console.error('No locations to search.');
    process.exit(1);
  }

  console.log(`Scraping "${query}" across ${locations.length} location(s)...\n`);

  const client = new GoogleMapsClient({ apiKey, requestsPerSecond: 2 });
  const allPlaces: Place[] = [];
  const start = Date.now();

  for (let i = 0; i < locations.length; i++) {
    const loc = locations[i];
    process.stdout.write(`  [${i + 1}/${locations.length}] ${query} in ${loc}... `);
    try {
      const results = await client.search({ query: `${query} in ${loc}`, limit, country: 'us' });
      allPlaces.push(...results);
      console.log(`${results.length} results`);
    } catch (err: any) {
      console.log(`ERROR: ${err.message}`);
    }
  }

  const unique = dedup(allPlaces);
  console.log(`\nTotal: ${allPlaces.length} results, ${unique.length} unique after dedup`);

  await exportCSV(unique, output);
  console.log(`Saved to: ${output}`);
  console.log(`Done in ${((Date.now() - start) / 1000).toFixed(1)}s`);

  if (unique.length > 0) {
    const s = unique[0];
    console.log(`\nSample: ${s.name} — ${s.address}${s.phone ? ` — ${s.phone}` : ''}${s.rating ? ` — ${s.rating}★ (${s.reviews_count} reviews)` : ''}`);
  }
}

main().catch(err => { console.error('Fatal:', err.message); process.exit(1); });
```

---

## Step 5: Running the Scraper

```bash
# Dentists in specific NYC zip codes
npm run scrape -- --query="dentist" --zips=10014,10013,10012

# All pizza places in California (zips with pop >= 5000 only)
npm run scrape -- --query="pizza restaurant" --state=CA --min-pop=5000

# Every gym in Texas
npm run scrape -- --query="gym" --state=TX

# Gyms across specific cities
npm run scrape -- --query="gym" --cities="Austin TX,Dallas TX,Houston TX"

# Custom output
npm run scrape -- --query="dentist" --state=FL --output=./data/florida-dentists.csv
```

---

## Step 6: Optional Web App

For a browser UI, install Express and create `src/server.ts`:

```bash
npm install express
npm install -D @types/express
```

```typescript
import express from 'express';
import { GoogleMapsClient } from './client.js';
import { exportCSV } from './csv.js';
import { tmpdir } from 'os';
import { join } from 'path';
import { readFile, unlink } from 'fs/promises';

const app = express();
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

const client = new GoogleMapsClient({ apiKey: process.env.RAPIDAPI_KEY!, requestsPerSecond: 2 });

app.get('/', (_req, res) => {
  res.send(`<!DOCTYPE html>
<html><head><title>Google Maps Scraper</title></head>
<body style="font-family:sans-serif;max-width:600px;margin:40px auto;padding:0 20px">
  <h1>Google Maps Scraper</h1>
  <form method="POST" action="/scrape">
    <label>Search query:<br><input name="query" placeholder="pizza restaurant" style="width:100%;padding:8px;margin:4px 0 12px" required></label>
    <label>Locations (comma-separated zips or cities):<br><input name="locations" placeholder="10014, 10013, 10012" style="width:100%;padding:8px;margin:4px 0 12px" required></label>
    <button type="submit" style="padding:10px 24px">Scrape & Download CSV</button>
  </form>
</body></html>`);
});

app.post('/scrape', async (req, res) => {
  const { query, locations: locStr } = req.body;
  const locations: string[] = locStr.split(',').map((s: string) => s.trim()).filter(Boolean);
  const allPlaces: any[] = [];

  for (const loc of locations) {
    try {
      const results = await client.search({ query: `${query} in ${loc}`, limit: 20 });
      allPlaces.push(...results);
    } catch {}
  }

  const seen = new Set<string>();
  const unique = allPlaces.filter(p => { if (seen.has(p.place_id)) return false; seen.add(p.place_id); return true; });

  const tmpPath = join(tmpdir(), `scrape-${Date.now()}.csv`);
  await exportCSV(unique, tmpPath);
  const csv = await readFile(tmpPath, 'utf-8');
  await unlink(tmpPath);

  res.setHeader('Content-Type', 'text/csv');
  res.setHeader('Content-Disposition', `attachment; filename="maps-scrape-${Date.now()}.csv"`);
  res.send(csv);
});

const port = parseInt(process.env.PORT ?? '3000', 10);
app.listen(port, () => console.log(`Scraper running at http://localhost:${port}`));
```

Run with `npm run serve`.

**Deployment:**
- **Railway**: connect GitHub repo, set `RAPIDAPI_KEY` env var, start command: `npx tsx src/server.ts`
- **Render / Fly.io**: standard Node.js deploy, set env var
- **Docker**: `FROM node:20-slim` + `npm install` + `npx tsx src/server.ts`

---

## Tips

- **"query in zipcode"** format works best for US searches — no coordinates needed.
- **20 results per search** is the API max. Use multiple overlapping zip codes for more coverage.
- **Dedup by `place_id`** — the same business often appears in adjacent zip code searches.
- **Rate limit**: the client is set to 2 req/sec. Adjust `requestsPerSecond` if your RapidAPI plan allows more.
- **Filter results downstream**: use the `types` or `category` fields to remove irrelevant businesses (e.g. filter out "bar" when searching "restaurant").
- **Large state scrapes**: Texas has ~2,500 zip codes at 2 req/sec — that's ~20 minutes. Use `--min-pop=5000` to cut it down significantly.

---

## API Reference

Base URL: `https://maps-data.p.rapidapi.com`

| Endpoint | Purpose |
|---|---|
| `searchmaps.php` | Search businesses by query + location |
| `geocoding.php` | Convert address/zip to lat/lng |
| `nearby.php` | Search near a lat/lng point |
| `place.php` | Full details for one business by place_id |

Response fields per result: `place_id`, `name`, `address`, `phone`, `website`, `rating`, `reviews_count`, `lat`, `lng`, `types`, `category`.
