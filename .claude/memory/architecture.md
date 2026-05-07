# Duet Books — Architecture & Systems

## Version
- Current: v2.0.0 (April 2026)
- File: `DuetBooks.html` (single standalone file)

## Production Credit System (separate from commission)
- **Annuities**: 3% of paid premium
- **Life/Term/IUL**: 100% of annualized premium
- **CDs/Banking/Other**: $0

## 3MMA (3-Month Moving Average)
- Average of Production Credit over PRIOR 3 months
- **Excludes current month** (loop i=1 to 3, not 0 to 2)
- Uses `c.submitted` date (pay period month), NOT `c.paidDate` (import date)

## 4-Tier Thresholds
- T1: $0 – $3,333
- T2: $3,334 – $4,999
- T3: $5,000 – $6,666
- T4: $6,667+

## TIER_RATES
Complete rate table from official AAA Variable Incentive Plan PDF. Stored in `TIER_RATES` object with rates per product per tier (t1/t2/t3/t4).

## Product Category Detection — ORDER MATTERS
`getProductCategory(c)` checks in this order:
1. **Annuity keywords FIRST** (annuity, gia, landmark, legend, elite, platinum, safe return, premier) — prevents "Bonus Annuity" from being caught by the "bonus" keyword in step 2
2. **CD/Banking/Other** (certificate, cd, banking, bonus, other income, credit card, membership, deposit)
3. **Life/IUL** — checks `findProd()` catalog match first, then keyword fallback (term, whole life, giwl, iul, accumulator, etc.)

**Critical bug history**: `findProd()` fallback returns `products[0]` (AAA Accumulator IUL). If a product doesn't match the catalog exactly, the fallback's category is unreliable. The `isRealMatch` check prevents this.

## Carrier Phone Numbers
- **AAA Life**: (888) 444-7181 — Life, Term, IUL, AAA Annuities, Securian Eclipse
- **MassMutual/GA**: (800) 854-3649 — Legend, Landmark, Premier, Safe Return, GIA
- **CD Line**: (833) 586-2265 — Certificate of Deposit
- `getProductPhone()` does direct lookup then keyword fallback for Varicent-imported names (e.g. "GA" prefix instead of "MM")

## Key Functions
- `calcProductionCredit(c)` — returns PC based on category
- `calc3MMA()` — 3-month moving average of PC, excludes current month
- `tierText(mma)` / `tierClass(mma)` — returns T1-T4 text/CSS class
- `getTierRate(productName, tier)` — looks up rate from TIER_RATES
- `getCurrentTier()` — returns current tier based on 3MMA
- `ncProductChange()` — auto-fills tier-based commission rate when adding case
- `getProductPhone(productName)` — returns carrier phone number
- `callBtnHtml(productName)` — returns click-to-call button HTML

## Firebase Data Structure
```
/duetbooks/{agent_id}/
  /profile    — {name, branch, pin (hashed), created}
  /cases      — array of case objects
  /payperiods — array of pay period objects
  /settings   — {yourShare, partnerShare, yearlyGoal}
```

## Case Object Fields
id, client, phone, product, amount, rate, tierAtEntry, split, splitName, splitEmail, status, submitted, estPay, paidDate, actualCommission, notes, year, created

## Status Pipeline
pending → submitted → approved → issued → paid
