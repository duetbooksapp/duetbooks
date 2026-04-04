# Duet Books — Technical Documentation
## Version 2.0.0 | Built 2026-04-03

---

## Overview

Duet Books is a standalone compensation tracker for AAA Life Specialist agents. It runs as a single HTML file hosted on GitHub Pages, with data stored in Firebase Realtime Database.

- **Live URL**: https://duetbooksapp.github.io/duetbooks
- **GitHub Repo**: https://github.com/duetbooksapp/duetbooks
- **Firebase**: https://duet-crm-default-rtdb.firebaseio.com/duetbooks
- **Local Dev File**: /Users/steve/Desktop/DuetBooks/DuetBooks.html

---

## Architecture

- **Single HTML file** with inline CSS and JavaScript (no external dependencies)
- **Firebase Realtime Database** (REST API) for cloud data storage
- **Claude AI API** (Anthropic) for reading uploaded Varicent/Workday documents
- **localStorage** for session data, API key storage, and onboarding state
- **GitHub Pages** for free static hosting

### Data Namespace
Each agent's data is stored at `/duetbooks/{agent_id}/` in Firebase with three keys:
- `profile` — name, branch, hashed PIN
- `cases` — array of all Book of Business cases
- `payperiods` — array of all pay period records
- `settings` — split percentages, yearly goal

---

## Tab Structure

| Tab | Panel ID | Description |
|-----|----------|-------------|
| Dashboard | p0 | Stat cards, tier display, tier forecast, status breakdown |
| Book of Business | p1 | Case management, add/edit cases, filters, sorting |
| Pay Periods | p2 | Varicent + Workday upload, AI extraction, matching |
| Cash Flow | p3 | Aging buckets (0-30, 30-60, 60+ days) |
| Rate Table | p4 | HIDDEN — commission rates locked from editing |
| Settings | p5 | API key, split percentages, PIN change, reset data |

---

## Features Built

### 1. Login & Registration
- PIN-based authentication (4-6 digits)
- PIN stored as SHA-256 hash in Firebase
- Session persists via sessionStorage
- Remembered agent names via localStorage

### 2. Dashboard (Tab 0)
- **Total Cases** — count for current year
- **Monthly Production** — latest month with data
- **YTD Paid** — total commissions received this year
- **Pending** — total pending commissions
- **3MMA Tier Card** — shows current tier (T1-T4) with progress bar
- **Status Breakdown** — count by status (pending/submitted/approved/issued/paid)
- **Tier Forecast Table** — prior 3 months Production Credit breakdown
- **Tier Projection Cards** — shows what agent needs in current month to reach T2, T3, T4

### 3. 3MMA Tier System
Completely separate from commission calculations. Never touches `actualCommission`.

**Production Credit Rules:**
- Annuities = 3% of Paid Premium
- Life/Term/IUL = 100% of Annualized Premium
- CDs/Banking/Other = $0

**3MMA Calculation:**
- Uses PRIOR 3 months, excludes current month
- Based on `submitted` date (pay period month), NOT `paidDate` (import date)
- Function: `calc3MMA()`

**4 Tier Thresholds:**
- T1: $0 – $3,333/mo (lowest rates)
- T2: $3,334 – $4,999/mo
- T3: $5,000 – $6,666/mo
- T4: $6,667+/mo (highest rates)

**Product Category Detection** (`getProductCategory()`):
- Checks annuity keywords FIRST (annuity, gia, landmark, legend, elite, platinum, safe return, premier)
- Then CD/banking/other keywords (certificate, deposit, cd, banking, bonus, credit card, membership)
- Then life keywords (term, whole life, giwl, iul, accumulator, ordinary life, etc.)
- Default: 'other' ($0 PC) — safe fallback

**Tier Rate Table** (`TIER_RATES`):
Complete rate table from official AAA Variable Incentive Plan PDF. Every product has rates for all 4 tiers. When agent adds a case, rate auto-fills based on current tier via `getTierRate()`.

### 4. Book of Business (Tab 1)
- **Add Case** — manual entry with product dropdown, tier-based rate auto-fill
- **Commission Preview** — shows current tier, commission, and production credit before saving
- **Tier at Entry** — stores `tierAtEntry` on each case
- **Edit Case** — click any case to modify details or advance status
- **Filters** — by status, carrier, category, and text search
- **Sortable columns** — click any header to sort
- **Status Pipeline** — Pending → Submitted → Approved → Issued → Paid
- **Non-commission products excluded** — Banking Incentive, Bonus, Other Income not shown

### 5. Pay Periods (Tab 2)
- **Document Upload** — drag/drop or click to browse, supports PDF and images
- **AI Extraction** — Claude Vision API reads each document separately
- **Varicent Processing** — extracts cases (client, product, plan code, paid premium, production credit, commission, split status)
- **Workday Processing** — extracts base pay, gross pay, banking, bonuses, deductions, taxes, net pay
- **3-Level Matching** — Varicent and Workday paired by: (1) exact period label, (2) month + period number, (3) month fallback
- **Retroactive Matching** — upload Varicent first, Workday later (or vice versa) — auto-pairs
- **Duplicate Protection** — file-level and case-level duplicate detection
- **Year Filter** — toggle between 2025/2026 pay periods
- **Unmatched Alerts** — shows when Varicent exists without Workday or vice versa
- **File Limits** — up to 4 Varicent + 4 Workday files per upload batch

**Duplicate Detection Logic:**
- Pay period level: checks if period with same label already exists with matching financial data
- Case level: counter-based approach comparing batch count vs already-imported count
- Handles identical transactions (e.g., same client, same product, same commission amount)

**Year Logic:**
- `getCaseDate(monthStr)` maps months after current month to prior year
- December uploaded in January → tagged as 2025, not 2026

### 6. Cash Flow (Tab 3)
- **Aging Buckets** — cases grouped by days since submitted
  - 0-30 days (green) — fresh
  - 30-60 days (amber) — may need follow-up
  - 60+ days (red) — overdue
- **Clickable Expansion** — click bucket number to see individual cases
- **Detail Table** — shows client, product, commission, status, days, view link
- **Year Filter** — matches dashboard year

### 7. Settings (Tab 5)
- **Agent Info** — name and branch (read-only)
- **AI Document Reader** — Anthropic API key input (stored in localStorage only)
- **Commission Split** — your share % and partner share % (default 80/20)
- **Change PIN** — requires current PIN verification
- **Reset Data** — requires typing "yes delete" to confirm
- **Re-run Setup Wizard** — link to replay onboarding flow

### 8. Help System
- Accessed via **?** button in top bar
- **Searchable** — filter topics by keyword
- **Topic Pills** — click to jump to specific topic
- **10 Topics**: Overview, Varicent Upload, Workday Upload, 3MMA System, Tier Levels, Tier Forecast, Book of Business, Pay Periods & Matching, Cash Flow, Settings
- Click outside modal to close

### 9. Onboarding Flow
- **6-step interactive wizard** shown once after account creation
- Step 1: Welcome — what Duet Books does
- Step 2: API Key — with inline input to save
- Step 3: Gather Varicent Statements — where to find them
- Step 4: Gather Workday Payslips — where to find them
- Step 5: Upload Documents — step-by-step instructions
- Step 6: You're All Set — dashboard overview
- **Skip** option on step 1
- **Progress dots** showing completion
- Stored in localStorage — only shows once per agent
- Re-run available from Settings

---

## Key Functions Reference

| Function | Purpose |
|----------|---------|
| `calcCase(c)` | Calculate commission for a case. If `actualCommission` exists, returns it directly (Varicent import). Otherwise calculates from amount × rate with split. |
| `calcProductionCredit(c)` | Calculate Production Credit (separate from commission). 3% for annuities, 100% for life, 0% for other. |
| `getProductCategory(c)` | Classify product as 'annuity', 'life', or 'other' by checking product name keywords. |
| `calc3MMA()` | 3-Month Moving Average of Production Credit. Prior 3 months, excludes current month. |
| `tierText(mma)` | Returns tier label (T1/T2/T3/T4) from 3MMA value. |
| `getTierRate(product, tier)` | Looks up commission rate for a product at a given tier from TIER_RATES table. |
| `getCurrentTier()` | Returns current tier based on calc3MMA(). |
| `getCaseDate(monthStr)` | Maps month string to correct year (Dec → prior year). |
| `processUploads()` | AI extraction pipeline — reads each uploaded file via Claude Vision API. |
| `openHelp()` / `closeHelp()` | Toggle help modal. |
| `showOnboarding()` | Launch onboarding wizard (checks localStorage flag). |

---

## Product Catalog

24 products across 3 carriers (AAA, MassMutual, Securian) in 4 categories:
- IUL / Universal Life (4 products)
- Annuities (12 products — external and internal)
- Life / Whole / Term (7 products)
- Banking / CD / Other (3 products)

Each product has tier-adjusted rates in the `TIER_RATES` object.

---

## Commission Calculation Flow

### Varicent-Imported Cases (actualCommission path)
1. AI extracts commission from Varicent PDF
2. Stored as `actualCommission` on the case
3. `calcCase()` returns it directly — no recalculation
4. This IS the agent's final commission (already includes split from AAA)

### Manually-Entered Cases (calculated path)
1. Agent selects product → rate auto-fills based on current tier
2. Agent enters premium amount
3. Commission = amount × rate%
4. If split: agent gets yourShare%, partner gets partnerShare%

---

## Data Flow: Document Upload to Dashboard

1. Agent uploads Varicent PDF → AI extracts period info + individual cases
2. Agent uploads Workday PDF → AI extracts pay details (base, gross, net, taxes)
3. System matches Varicent + Workday by pay period
4. Each Varicent case becomes a "paid" entry in Book of Business
5. Dashboard updates: commission totals, tier calculation, forecast
6. All data synced to Firebase

---

## Known Considerations

- **Bonus Annuity products**: The word "bonus" in "Bonus Annuity" was initially caught by the CD/banking check. Fixed by checking annuity keywords FIRST.
- **paidDate vs submitted**: `paidDate` is the import date (when agent uploaded), `submitted` is the pay period month. 3MMA uses `submitted`.
- **findProd fallback**: When a Varicent product name doesn't match the catalog, `findProd()` returns products[0] (AAA Accumulator IUL). Category detection relies on product NAME keywords, not the fallback catalog category.
- **Negative amounts**: Varicent can have chargebacks/reversals (negative premiums). These correctly reduce Production Credit.
- **API Key**: Stored in localStorage only — never sent to Firebase. Each agent needs their own key (or a shared one).

---

## Deployment

### Making Changes
1. Edit `/Users/steve/Desktop/DuetBooks/DuetBooks.html` (working file)
2. Test locally by refreshing `file:///Users/steve/Desktop/DuetBooks/DuetBooks.html`
3. Copy to `index.html`: `cp DuetBooks.html index.html`
4. Upload `index.html` to GitHub repo
5. Live at https://duetbooksapp.github.io/duetbooks within 30 seconds

### Backups
- Local backups: `/Users/steve/Desktop/DuetBooks/DuetBooks_backup_*.html`
- GitHub: full version history of every upload
- Firebase: agent data lives in the cloud

### Checking Registered Agents
```
https://duet-crm-default-rtdb.firebaseio.com/duetbooks.json?shallow=true
```

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| v1.0.0 | 2026-04-03 | Initial build — login, dashboard, book of business, pay periods, cash flow, settings |
| v1.1.0 | 2026-04-03 | Retroactive matching, duplicate protection, year filters, unmatched alerts, CD handling |
| v2.0.0 | 2026-04-03 | 4-tier system with Production Credit, tier forecast, help system, onboarding flow, seed data removed, deployed to GitHub Pages |
