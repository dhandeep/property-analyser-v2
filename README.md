# RE Analyzer — PNW

A single-file real estate investment calculator for Seattle/Eastside properties.
Built to work alongside a Claude research session: Claude researches a listing and
outputs JSON, you paste it in, the calculator runs all the math instantly.

No build step. No backend. Open `index.html` in a browser.

---

## How to use

### Option A — Paste Claude research JSON
1. In a new Claude session, paste the research prompt from `RESEARCH_PROMPT.md` + a Zillow/Redfin URL
2. Claude returns a JSON block
3. Open `index.html`, paste the JSON into the "Paste Claude Research JSON" box, click **Parse & Load**
4. All fields populate and results update instantly

### Option B — Load a saved property
1. Click **Load Saved** in the header
2. Pick any `.json` file from `data/` — your previously researched deals
3. Tweak inputs manually as needed

### Option C — Enter manually
Fill in the blue input fields directly. Every field has a tooltip (hover the label).
All % fields are plain numbers: enter `7` for 7%, not `0.07`.

---

## Tabs

### Left panel — Inputs
- **Property A / B / C / D** — up to 4 properties simultaneously
- Sections A–F match the field groups in the Excel sheet
- Auto-calculated fields (gray) update live as you type

### Right panel — Results

**Overview** — key metrics card + 10-year cash flow chart + exit analysis stack

**10-Yr Pro Forma** — year-by-year table: rent growth, expense inflation, NOI, cash flow, property value, cumulative CF

**Compare All** — all 4 properties side by side, best/worst highlighted

### Scenario bar
Toggles Conservative / Base / Optimistic across all properties at once:

| Scenario     | Appreciation | Rent Growth | Rate  | Vacancy | Exit Cap |
|---|---|---|---|---|---|
| Conservative | 3.0%         | 2.0%        | 7.5%  | 6%      | 5.5%     |
| Base         | 4.0%         | 3.0%        | 7.0%  | 5%      | 5.0%     |
| Optimistic   | 5.5%         | 4.5%        | 6.5%  | 4%      | 4.5%     |

---

## Key formulas

All % inputs stored as plain numbers (7 = 7%). All math divides by 100 internally.

```
Monthly P&I     = PMT(rate/12, term*12, loan)
NOI             = EGI - Total OpEx
EGI             = Gross Rent × (1 - Vacancy%)
Total OpEx      = Taxes + Insurance + HOA + Mgmt + Repairs + CapEx + Other
Cash Flow/mo    = (NOI - Annual P&I) / 12
Cap Rate        = NOI / Purchase Price
Cash-on-Cash    = Annual Cash Flow / Total Cash Invested
DSCR            = NOI / Annual P&I
GRM             = Price / Annual Gross Rent
Break-Even Occ  = (OpEx + Debt Service) / Gross Rent

Exit (appreciation-based, primary for PNW):
  Sale Price    = Price × (1 + Appx%)^Hold
  Net Proceeds  = Sale Price × (1 - Selling Costs%)
  Loan Balance  = PV of remaining payments at hold year
  Equity        = Net Proceeds - Loan Balance

Exit (cap-rate-based, shown as floor/reference):
  NOI at Exit   = NOI × (1 + Rent Growth%)^Hold
  Cap-Rate Value = NOI at Exit / Exit Cap Rate

Cumulative CF   = Sum of annual cash flows for each hold year (from pro forma)
Total Profit    = Equity at Sale + Cumulative CF - Total Cash Invested
Ann. Total ROI  = (Total Cash Invested + Total Profit) / Total Cash Invested)^(1/Hold) - 1
```

**Why cumulative CF matters:** standard YouTube spreadsheets show equity at sale and call it profit.
But a −$2,500/mo property over 5 years costs $150K in checks written beyond the down payment.
Total Profit accounts for this. Annualized ROI can be negative even with appreciation if the
cash burn exceeds the gain — that's not a bug, it's the honest PNW reality at current rates.

**Two exit methods:**
- Appreciation-based (primary): PNW buyers price off comps, not income — this is realistic
- Cap-rate-based (reference floor): what the income alone justifies — usually far below market in PNW

---

## Data files (`data/`)

Each file is a flat JSON object with all property inputs. Keys match internal field names exactly.

| File | Property |
|---|---|
| `bothell-sfh-103rd.json` | 19314 103rd Ave NE, Bothell — 4bd/3ba SFH, 1976 |
| `sixty-01-redmond-condo.json` | Sixty-01 #644 — 2bd/1ba condo, HOA $650/mo |
| `_template.json` | Blank template for new properties |

Each file includes a `_meta` block with source, confidence levels, notes, and red flags
from the Claude research session. These are displayed in the UI but not used in calculations.

### Field reference

All values are strings. All % fields are plain numbers (enter `7` not `0.07`).

| Field | Description | Default |
|---|---|---|
| `name` | Address or nickname | — |
| `type` | SFH / Condo / Townhouse / MFH | SFH |
| `price` | Purchase price $ | — |
| `arv` | After-repair value $ | — |
| `closing` | Closing costs $ (~2% in WA) | — |
| `reno` | Upfront renovation $ | 0 |
| `hoa_mo` | HOA monthly $ | 0 |
| `yr_built` | Year built | — |
| `sqft` | Interior sq ft | — |
| `beds` / `baths` | Bedrooms / bathrooms | — |
| `dp_pct` | Down payment % (enter 20) | 20 |
| `ir_pct` | Interest rate % (enter 7) | 7.0 |
| `term` | Loan term years | 30 |
| `rent_mo` | Monthly gross rent $ | — |
| `other_mo` | Other monthly income $ | 0 |
| `vac_pct` | Vacancy % (enter 5) | 5 |
| `ptax` | Property taxes $/yr | — |
| `ins` | Insurance $/yr | — |
| `mgmt_pct` | Property management % (enter 8) | 8 |
| `rep_pct` | Repairs % of value (enter 1) | 1.0 |
| `capex_pct` | CapEx reserve % of value (enter 0.5) | 0.5 |
| `landscape` | Landscaping/utilities $/yr | 0 |
| `other_exp` | Other expenses $/yr | 0 |
| `appx_pct` | Annual appreciation % (enter 3.5) | 3.5 |
| `hold` | Hold period years | 5 |
| `sell_cost_pct` | Selling costs % (enter 6) | 6 |
| `rent_grow_pct` | Annual rent growth % (enter 3) | 3.0 |
| `exit_cap_pct` | Exit cap rate % (enter 5) | 5.0 |

---

## PNW benchmarks

| Metric | Threshold | PNW Reality |
|---|---|---|
| Cap Rate | ≥ 5% good | 1–4% typical at current prices |
| Cash-on-Cash | ≥ 8% good | Usually negative at 7% rates |
| DSCR | ≥ 1.25× for lender | < 1.0 common in PNW |
| GRM | Lower = better | 16–22× typical Eastside |
| 1% Rule | ≥ 1% ideal | 0.35–0.5% typical PNW |
| Appreciation | — | 10yr CAGR ~4–5% King/Snohomish |
| Break-even hold | — | Usually 7–10yr at current rates + prices |

---

## Project structure

```
re-analyzer/
├── index.html          # Single-file app — open this in browser
├── README.md           # This file
├── TODO.md             # Feature backlog
├── RESEARCH_PROMPT.md  # Prompt to use with Claude for new property research
└── data/
    ├── _template.json              # Blank property template
    ├── bothell-sfh-103rd.json      # 19314 103rd Ave NE
    └── sixty-01-redmond-condo.json # Sixty-01 #644
```

---

## Known limitations / caveats

1. **Property tax post-sale jump** — WA reassesses after purchase. Senior exemptions don't transfer. Actual bill can be 20–40% higher than listing shows. Always verify at county assessor.
2. **Rent Zestimate skews high** — haircut Zillow rent estimate 5–8% for conservative case.
3. **Appreciation is the thesis** — at current rates, PNW deals are almost universally negative cash-flow. The deal only works if you hold long enough for appreciation to overcome the burn. Model both 5yr and 10yr hold.
4. **CapEx and repairs** use appreciation % to grow the reserve — construction CPI actually runs ~4%/yr, which would be a separate input in v2.
5. **No IRR** — annualized ROI uses CAGR approximation. True IRR (NPV=0 on cash flow stream) is planned for v2.
6. **No BRRRR / refi modeling** — planned for v2.
7. **HOA special assessments** not captured — one-time hits of $5K–$30K are possible in older buildings.
