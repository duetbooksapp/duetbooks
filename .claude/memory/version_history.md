# Duet Books — Version History & Current State

## v2.0.0 (April 2026) — Current
### Features Built
- 4-tier Production Credit system (T1-T4) with 3MMA calculation
- Auto-fill tier-based commission rates when adding cases
- Tier Forecast dashboard with prior 3 months PC table and projection cards
- Varicent + Workday document upload with AI parsing (Anthropic API)
- Book of Business with sortable table, filters, status pipeline
- Pay Period tracking with commission matching
- Cash Flow dashboard with aging buckets (0-30, 30-60, 60+ days)
- Searchable Help system (? button, 10 topics)
- 6-step interactive onboarding for new agents
- Carrier phone numbers with click-to-call buttons
- Mobile responsive layout (card view for cases, short tab labels, stacked layouts)
- Firebase real-time sync with localStorage fallback
- GitHub Pages deployment with auto-push

### Bugs Fixed
- 3MMA showing $0: was using `paidDate` (import date) instead of `submitted` (pay period month)
- 3MMA wildly inflated ($394K): CDs misclassified as Life due to `findProd()` fallback
- Bonus Annuity misclassified as Other: "bonus" keyword caught before "annuity" — reordered checks
- T1 tier missing from UI: only had T2/T3/T4 originally
- Current month included in 3MMA: changed loop from i=0..2 to i=1..3

### Production Readiness
- All test/seed data removed (11 CRM pipeline cases deleted)
- Only production account: axel_maxim
- Data Management section removed from Settings
- Reset requires typing "yes delete"
- "Re-run setup wizard" link in Settings

## Remaining Phase 4 Items
- Testing with a second agent
- API key strategy (shared vs individual per agent)
- Standalone Workday import date patterns (lines ~2119, 2142)

## Backups
- `DuetBooks_backup_20260403_173550.html` — before tier system
- `DuetBooks_backup_20260403_203931.html` — tier system complete
- `DuetBooks_backup_20260403_214918.html` — production-ready
- `DuetBooks_backup_20260404_*.html` — before mobile responsive
