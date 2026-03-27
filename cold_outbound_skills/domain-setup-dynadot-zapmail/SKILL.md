---
name: domain-setup-dynadot-zapmail
description: End-to-end cold email domain setup — generate domain names, check availability, purchase on Dynadot, switch nameservers, connect to Zapmail, create inboxes, and export to a sending platform. Trigger when the user wants to set up cold email domains, buy domains for outreach, or provision inboxes on Zapmail.
---

# Cold Email Domain Setup: Dynadot + Zapmail

Complete end-to-end workflow for setting up cold email sending domains. Takes you from a brand keyword to live, warmed inboxes ready to import into your sending platform.

**Total time from start to live inboxes: 5-7 hours** (mostly waiting on DNS propagation and Zapmail provisioning).

---

## Overview

```
Step 1: Generate domain candidates         instant
Step 2: Check availability on Dynadot      ~1 min per 100 domains
Step 3: Purchase available domains         ~1 min per 100 domains
          ↓
Step 4: Switch nameservers to Zapmail      ~1 min per 100 domains
          ↓ WAIT 15-20 min (DNS propagation)
Step 5: Connect domains on Zapmail         ~2 min per 100 domains
          ↓ WAIT 10-30 min (domains become assignable)
Step 6: Create inboxes                     ~3 min per 100 domains
          ↓ WAIT 4-6 HOURS (inbox provisioning)
Step 7: Export to sending platform         instant
```

---

## Prerequisites

### API Keys

You need two keys stored as environment variables:

```bash
# Add to ~/.zshrc or ~/.bashrc — never commit these
export DYNADOT_API_KEY=your_dynadot_key
export ZAPMAIL_API_KEY=your_zapmail_key
```

**Get your Dynadot key:**
1. Log in at dynadot.com → Tools → API
2. Enable API access, copy the key
3. Whitelist your current IP address (required — API calls will fail otherwise)

**Get your Zapmail key:**
1. Log in at zapmail.ai → Settings → API
2. Copy the key

### Verify Both APIs Work

```bash
# Dynadot — check wallet balance
curl "https://api.dynadot.com/api3.json?key=$DYNADOT_API_KEY&command=account_info"
# Look for "AccountBalance" in the response

# Zapmail — list assignable domains
curl -H "x-auth-zapmail: $ZAPMAIL_API_KEY" \
  "https://api.zapmail.ai/api/v2/domains/assignable?limit=5&page=1"
# Should return 200
```

---

## Step 1: Generate Domain Name Candidates

**Goal: short, clean domains that look like real brands.** Under 20 characters in the SLD is ideal.

### Strategy

Use a brand keyword combined with short prefixes or suffixes:

**Tier 1 — Prefix + Brand** (try first):
```
go{brand}    try{brand}    get{brand}    my{brand}
the{brand}   hey{brand}    use{brand}    run{brand}
```

**Tier 2 — Brand + Suffix:**
```
{brand}hq    {brand}hub    {brand}now    {brand}app
{brand}pro   {brand}lab    {brand}co     {brand}ai
```

**Tier 3 — Prefix + Brand + Suffix** (only if tiers 1-2 don't yield enough):
```
go{brand}hq    try{brand}hub    get{brand}pro
```

Full prefix list: `go, get, try, join, find, search, explore, reach, access, boost, team, send, email, connect, launch, start, build, discover, meet, use, hello, hey, my, the, top, best, run, open, live, grow, with, via, one, all, new, pro`

Full suffix list: `hub, hq, online, today, direct, teams, projects, outreach, works, signal, scope, flow, edge, radar, desk, global, core, base, systems, engine, link, next, source, circle, stack, zone, path, spot, wave, grid, point, shift, field, net, way, pulse, vault, peak, sync, lab`

### Filtering Rules
- Max SLD length: 40 characters (aim for under 20)
- Banned substrings: `mega`, `ultra`, `grp` (look spammy)
- Screen for unintended words (e.g. "therapists" contains "the rapist") — check each candidate
- Never reuse a domain across different senders or clients

### Recommended TLD
- `.info` — cheapest (~$3.00 on Dynadot), good deliverability
- `.co` — slightly pricier (~$8-12), more professional appearance
- Avoid `.xyz`, `.click`, `.top` — spam-associated

For brand "acme", Tier 1+2 generates ~76 candidates. Most campaigns need 10-30 domains.

---

## Step 2: Check Availability

Batch up to 100 domains per request.

```python
import urllib.parse, time, os, requests

DYNADOT_KEY = os.environ["DYNADOT_API_KEY"]

def check_availability(domains: list[str], max_price: float = 3.50) -> list[str]:
    available = []
    for i in range(0, len(domains), 100):
        batch = domains[i:i+100]
        params = {"key": DYNADOT_KEY, "command": "search", "show_price": "1", "currency": "USD"}
        for j, d in enumerate(batch):
            params[f"domain{j}"] = d
        resp = requests.get("https://api.dynadot.com/api3.json", params=params).json()
        for result in resp.get("SearchResponse", {}).get("SearchResults", []):
            if result.get("Available") == "yes":
                price = float(result.get("Price", "999").replace("$", ""))
                if price <= max_price:
                    available.append(result["DomainName"])
        time.sleep(1)  # avoid rate limiting
    return available
```

---

## Step 3: Purchase Domains

**Always check wallet balance first.**

```python
def get_balance() -> float:
    resp = requests.get(
        "https://api.dynadot.com/api3.json",
        params={"key": DYNADOT_KEY, "command": "account_info"}
    ).json()
    balance_str = resp.get("AccountInfoResponse", {}).get("AccountBalance", "$0")
    return float(balance_str.replace("$", ""))

def purchase_domains(domains: list[str]) -> list[str]:
    purchased = []
    for domain in domains:
        resp = requests.get(
            "https://api.dynadot.com/api3.json",
            params={"key": DYNADOT_KEY, "command": "register", "domain": domain, "duration": "1"}
        ).json()
        status = resp.get("RegisterResponse", {}).get("Status", "")
        if status == "success":
            purchased.append(domain)
            print(f"  Purchased: {domain}")
        else:
            print(f"  FAILED: {domain} — {resp}")
        time.sleep(0.5)
    return purchased
```

One domain per API call. `duration=1` = 1-year registration.

---

## Step 4: Switch Nameservers to Zapmail

**Zapmail's nameservers:**
```
pns61.cloudns.net
pns62.cloudns.com
pns63.cloudns.net
pns64.cloudns.uk
```

**Critical Python gotcha:** `urllib.parse.urlencode()` encodes commas as `%2C`, which breaks Dynadot's batch NS endpoint. Always pass `safe=','`:

```python
def switch_nameservers(domains: list[str]) -> None:
    for i in range(0, len(domains), 100):
        batch = domains[i:i+100]
        params = {
            "key": DYNADOT_KEY,
            "command": "set_ns",
            "domain": ",".join(batch),
            "ns0": "pns61.cloudns.net",
            "ns1": "pns62.cloudns.com",
            "ns2": "pns63.cloudns.net",
            "ns3": "pns64.cloudns.uk",
        }
        url = f"https://api.dynadot.com/api3.json?{urllib.parse.urlencode(params, safe=',')}"
        resp = requests.get(url).json()
        status = resp.get("SetNsResponse", {}).get("Status", "")
        print(f"  NS batch {i//100 + 1}: {status}")
        time.sleep(1)
```

After switching: **wait 15-20 minutes** before proceeding. DNS propagation is not instant.

---

## Step 5: Connect Domains on Zapmail

```python
ZAPMAIL_KEY = os.environ["ZAPMAIL_API_KEY"]
ZAPMAIL_HEADERS = {"x-auth-zapmail": ZAPMAIL_KEY, "Content-Type": "application/json"}

def connect_domains(domains: list[str]) -> None:
    for i in range(0, len(domains), 50):
        batch = domains[i:i+50]
        resp = requests.post(
            "https://api.zapmail.ai/api/v2/domains/connect-domain",
            headers=ZAPMAIL_HEADERS,
            json={"domainNames": batch}
        )
        print(f"  Connect batch {i//50 + 1}: {resp.status_code}")
        time.sleep(3)

def wait_for_assignable(domains: list[str], timeout_minutes: int = 30) -> list[dict]:
    """Poll until >95% of domains are assignable or timeout reached."""
    deadline = time.time() + timeout_minutes * 60
    while time.time() < deadline:
        assignable = []
        page = 1
        while True:
            resp = requests.get(
                f"https://api.zapmail.ai/api/v2/domains/assignable?limit=100&page={page}",
                headers=ZAPMAIL_HEADERS
            ).json()
            items = resp.get("data", [])
            if not items:
                break
            assignable.extend(items)
            page += 1

        assignable_names = {d["domainName"] for d in assignable}
        pct = len([d for d in domains if d in assignable_names]) / len(domains) * 100
        print(f"  {pct:.0f}% assignable ({len(assignable_names)}/{len(domains)})")

        if pct >= 95:
            return assignable
        time.sleep(300)  # poll every 5 minutes

    print("  Warning: timeout reached — some domains may not be assignable yet")
    return assignable
```

Typical timeline: 10 min → ~50% assignable, 30 min → ~99%.

---

## Step 6: Create Inboxes

Create 2 inboxes per domain for sending variety:
- `{firstname}@domain`
- `{firstnamelastname}@domain`

```python
def create_inboxes(assignable_domains: list[dict], first_name: str, last_name: str) -> None:
    # Build domain UUID map
    uuid_map = {d["domainName"]: d["id"] for d in assignable_domains}
    domain_names = list(uuid_map.keys())

    for i in range(0, len(domain_names), 25):
        batch = domain_names[i:i+25]
        payload = {}
        for domain in batch:
            uid = uuid_map[domain]
            username_short = first_name.lower()
            username_full = f"{first_name.lower()}{last_name.lower()}"
            payload[uid] = [
                {"firstName": first_name, "lastName": last_name,
                 "mailboxUsername": username_short, "domainName": domain},
                {"firstName": first_name, "lastName": last_name,
                 "mailboxUsername": username_full, "domainName": domain},
            ]

        resp = requests.post(
            "https://api.zapmail.ai/api/v2/mailboxes",
            headers=ZAPMAIL_HEADERS,
            json=payload
        )

        if resp.status_code == 400:
            # If batch fails (duplicate mailbox), retry domains one at a time
            print(f"  Batch {i//25 + 1} failed (400) — retrying individually...")
            for domain in batch:
                uid = uuid_map[domain]
                single_payload = {uid: payload[uid]}
                r = requests.post(
                    "https://api.zapmail.ai/api/v2/mailboxes",
                    headers=ZAPMAIL_HEADERS,
                    json=single_payload
                )
                print(f"    {domain}: {r.status_code}")
                time.sleep(1)
        else:
            print(f"  Batch {i//25 + 1}: {resp.status_code}")

        time.sleep(3)

    print("Inboxes queued. Wait 4-6 hours for provisioning before exporting.")
```

**After creating inboxes: wait 4-6 hours.** Zapmail returns 200 immediately but inboxes are "In Progress". Do not export until status is ACTIVE.

---

## Step 7: Export to Sending Platform

```python
def export_to_platform(
    sender_name_contains: str,
    app: str = "SMARTLEAD"
) -> None:
    resp = requests.post(
        "https://api.zapmail.ai/api/v2/exports/mailboxes",
        headers=ZAPMAIL_HEADERS,
        json={
            "apps": [app],
            "ids": [],
            "excludeIds": [],
            "tagIds": [],
            "contains": sender_name_contains.lower(),
            "status": "ACTIVE"  # critical — never export In Progress inboxes
        }
    )
    print(f"Export: {resp.status_code} — {resp.text[:200]}")
```

**Supported platforms:** `SMARTLEAD`, `INSTANTLY`, `REACHINBOX`, `REPLY_IO`, `QUICKMAIL`, `EMELIA`, `FIRSTQUADRANT`, `WARMY`, `SUPERAGI`, `LEMLIST`, `PIPL`, `SNOV`, `EMAILBISON`

**Important:** Your sending platform account must be linked in Zapmail's dashboard first, or via API:
```python
requests.post(
    "https://api.zapmail.ai/api/v2/exports/accounts/third-party",
    headers=ZAPMAIL_HEADERS,
    json={"email": "platform-login@example.com", "password": "platform-password", "app": "SMARTLEAD"}
)
```

---

## Full End-to-End Script

```python
import time

# Config
BRAND = "acme"
TLD = "info"
DOMAINS_NEEDED = 20
SENDER_FIRST = "Sarah"
SENDER_LAST = "Chen"
SENDING_APP = "SMARTLEAD"
OUTPUT_FILE = "domains.txt"

# 1. Generate & check availability
candidates = generate_candidates(BRAND, TLD)
available = check_availability(candidates, max_price=3.50)
domains = available[:DOMAINS_NEEDED]
print(f"Using {len(domains)} domains")

# 2. Verify balance
balance = get_balance()
total_cost = len(domains) * 3.00
if balance < total_cost:
    raise Exception(f"Insufficient balance: ${balance:.2f} < ${total_cost:.2f}")

# 3. Purchase
purchased = purchase_domains(domains)

# 4. Switch nameservers
switch_nameservers(purchased)

# 5. Wait for DNS
print("Waiting 20 minutes for DNS propagation...")
time.sleep(20 * 60)

# 6. Connect on Zapmail
connect_domains(purchased)

# 7. Wait for assignable
assignable = wait_for_assignable(purchased, timeout_minutes=30)

# 8. Create inboxes
create_inboxes(assignable, SENDER_FIRST, SENDER_LAST)

# 9. Wait for provisioning
print("Waiting 6 hours for inbox provisioning...")
time.sleep(6 * 3600)

# On macOS, notify when done:
# import subprocess; subprocess.run(["osascript", "-e", 'display notification "Inboxes ready!" with title "Domain Setup"'])

# 10. Export
export_to_platform(SENDER_FIRST.lower(), SENDING_APP)
```

---

## Common Gotchas

| Problem | Cause | Fix |
|---|---|---|
| API returns error for NS batch | Commas encoded as `%2C` | Always use `urllib.parse.urlencode(params, safe=',')` |
| Inbox batch returns 400 | One mailbox in the batch already exists | Retry failed domains one at a time |
| Export shows 0 inboxes | Inboxes still "In Progress" | Wait the full 4-6 hours; filter by `status: "ACTIVE"` |
| API calls fail silently | IP not whitelisted on Dynadot | Add your current IP in Dynadot → Tools → API |
| Domains not assignable after 30 min | DNS hasn't propagated | Wait longer; some domains take 45+ min |
| Rate limited by Zapmail | Too many requests too fast | Use 3s pause between Zapmail batches |

---

## API Quick Reference

| Action | Method | Endpoint |
|---|---|---|
| Check balance | GET | `https://api.dynadot.com/api3.json?key=KEY&command=account_info` |
| Search availability (batch 100) | GET | `https://api.dynadot.com/api3.json?key=KEY&command=search&show_price=1&domain0=...` |
| Register domain | GET | `https://api.dynadot.com/api3.json?key=KEY&command=register&domain=X&duration=1` |
| Switch nameservers (batch 100) | GET | `https://api.dynadot.com/api3.json?key=KEY&command=set_ns&domain=X,Y&ns0=...` |
| Connect domains (batch 50) | POST | `https://api.zapmail.ai/api/v2/domains/connect-domain` |
| Check assignable | GET | `https://api.zapmail.ai/api/v2/domains/assignable?limit=100&page=N` |
| Create inboxes (batch 25) | POST | `https://api.zapmail.ai/api/v2/mailboxes` |
| Export mailboxes | POST | `https://api.zapmail.ai/api/v2/exports/mailboxes` |
| Link sending platform | POST | `https://api.zapmail.ai/api/v2/exports/accounts/third-party` |
