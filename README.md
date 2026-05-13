# Akamai-WAF-Lab
Local lab to learn Akamai WAF tuning and traffic analysis [Claude]

# Akamai App & API Protector — Interactive Lab

A self-contained, browser-based simulation of **Akamai App & API Protector** (formerly Kona Site Defender + Bot Manager). Built as a hands-on lab for practicing WAF tuning, alert triage, and security policy management without needing access to a production Akamai environment.

No server, no install, no dependencies — just open the HTML file in a browser.

---

## Why this exists

Most security engineers learn enterprise WAF tooling on the job, which means the first real tuning decisions are made on live customer traffic. This lab provides a low-stakes environment to:

- Practice alert tuning workflows before touching production
- Build intuition for sensitivity vs. false-positive tradeoffs
- Walk through a realistic vendor UI for interviews, demos, or training
- Demonstrate WAF concepts to stakeholders who have never seen the console

The traffic engine generates a mix of benign requests, real attack patterns, and deliberately tricky false-positive candidates so every tuning decision has visible consequences.

---

## Getting started

1. Download `akamai-waf-sim.html`
2. Double-click to open in Chrome, Firefox, Edge, or Safari
3. The guided tour launches automatically on first load

If your browser blocks `file://` execution, serve it locally:

```bash
python3 -m http.server 8000
# then open http://localhost:8000/akamai-waf-sim.html
```

---

## What's included

### Live traffic feed
Simulated requests hit the edge in real time against a fake site (`www.akamai-corp.example`):

- **~90%** benign traffic (product browsing, API calls, static assets, logins)
- **~6%** real attack patterns (SQLi, XSS, LFI, RFI, command injection, Log4Shell probes, webshells)
- **~4%** false-positive candidates (legitimate requests that look malicious — Markdown saves with HTML tags, names with apostrophes, SPA path patterns)

Click any row to open the **Request Inspector** with the decoded payload, matched rule group, severity, source metadata, and quick-action buttons.

### Alert Tuning
The core workflow. For each attack group (SQL Injection, XSS, Command Injection, LFI, RFI, Protocol Attack, PHP Injection, Trojan/Backdoor, Platform/CVE, Outbound Data Leakage) you can:

- Change the action: **Alert**, **Deny**, or **None**
- Adjust the **sensitivity slider** (1–10) — higher values catch more, including false positives
- Add **exceptions** scoped to a specific method + path
- See live trigger and false-positive counts

### Adaptive Security Engine
Akamai-style recommendations that surface based on traffic patterns. Examples:

- "High FP rate on XSS-942100 from your `/api/v1/notes` endpoint — add an exception"
- "100% of LFI triggers resulted in 4xx upstream — recommend escalating from `alert` to `deny`"
- "IP 203.0.113.42 triggered 11 rules in 5 minutes — Penalty Box candidate"

Apply or dismiss each one and watch the WAF behavior change.

### WAF Rules
The Kona Rule Set (KRS-3.3) signature catalog, linked to their attack groups. Real rule IDs from Akamai's published documentation (942100, 941100, 932100, etc.).

### Rate Controls
Define throttling policies with match path, threshold, time window, and action. Three preset policies model common use cases:

- API login brute force (5 / 60s → deny)
- Checkout flood (30 / 60s → alert)
- Per-IP burst limit (200 / 10s → alert)

### Custom Rules
Pre-built examples:

- Block known scanner user-agents (sqlmap, nikto, nuclei, wpscan)
- Allow internal CIDR (10.40.0.0/16)
- Geo-block sanctioned regions

Custom rules run **before** WAF and rate rules.

### Bot Manager
Categorize traffic across Search Engines, Site Monitoring, Web Scrapers, Inventory Scrapers, Vulnerability Scanners, and Headless Browsers, with per-category actions (Allow / Alert / Deny / Challenge).

### Reports
Aggregated session metrics: total requests, blocked count, alert count, false-positive count, and a triggers-by-attack-group bar chart.

### Match Targets
Four preset targets modeling a realistic deployment:

- Storefront Web (default policy)
- API Gateway (strict policy)
- Static CDN (no inspection)
- Admin Console (IP-locked)

### Version History & Activation
Every change increments a pending-changes counter. The Activate flow models real Akamai deployment: Staging first (~3 min), then Production (~10 min) with a propagation progress bar.

---

## Suggested learning path

### Walkthrough 1 — Triage an attack
1. Watch the live feed until you see a row matched by **SQL Injection**
2. Click the row to open the inspector
3. Note the highlighted payload, IP, country, and matched rule
4. Hit **Penalty Box IP** — see the toast confirmation

### Walkthrough 2 — Tune a false positive
1. Open **Alert Tuning**
2. Apply the recommendation about XSS on `/api/v1/notes`
3. Watch the feed — XSS triggers against that endpoint stop
4. Lower XSS sensitivity to **6** and observe the impact

### Walkthrough 3 — Build a rate limit
1. Open **Rate Controls** → "API Login Brute Force"
2. Lower the threshold from 5 to 2 attempts per 60s
3. Watch the rate triggers counter climb in the live feed

### Walkthrough 4 — Stage and activate
1. Make several tuning changes (note the pending-changes counter top-right)
2. Click **Activate**
3. Choose Staging, add a note, and watch the propagation progress bar

---

## Technical notes

| | |
|---|---|
| **File** | single HTML file, ~76 KB |
| **Dependencies** | none (Google Fonts loaded via CDN, optional) |
| **State** | in-memory only; refresh resets the lab |
| **Traffic engine** | client-side JavaScript, ~700ms tick rate, 1–3 events per tick |
| **Decision engine** | evaluates custom rules → rate rules → WAF signatures → exceptions, in that order |

---

## Mapping to real Akamai concepts

| Lab element | Real Akamai equivalent |
|---|---|
| Security Configuration | Security Configuration |
| Match Targets | Match Targets |
| Attack Groups | Attack Group Conditions |
| Sensitivity slider | Anomaly Scoring threshold |
| Exceptions | Selectable Conditions / Advanced Exceptions |
| Adaptive Security Engine | Adaptive Security Engine (ASE) |
| Penalty Box | Penalty Box / Client Reputation |
| Rate Controls | Rate Policies |
| KRS-3.3 | Kona Rule Set (versioned) |
| Bot Manager | Bot Manager Premier |
| Staging / Production | Staging / Production networks |

---

## Limitations

This is a teaching lab, not a Akamai emulator. It does **not**:

- Use real Akamai APIs or rule logic
- Persist state across page reloads
- Simulate edge latency, caching, or origin behavior
- Model every Akamai feature (Client Reputation scoring details, Site Shield, API Discovery, etc.)
- Reflect every nuance of how production tuning decisions propagate

For real Akamai certification or production work, refer to [Akamai TechDocs](https://techdocs.akamai.com/).

---

## License

Educational use. Not affiliated with or endorsed by Akamai Technologies.
