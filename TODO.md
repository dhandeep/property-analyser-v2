# RE Analyzer — TODO

Prioritized backlog. P1 = do next. P3 = nice to have.

---

## P1 — Fixes real model gaps

- [ ] **True IRR** — replace CAGR with proper IRR calculation (solve for discount rate where NPV
  of all cash flows including initial investment and exit proceeds = 0). Standard in institutional
  underwriting. Gives materially different result on negative CF deals.

- [ ] **Research notes panel** — the `_meta` block in saved JSON (confidence levels, notes, red
  flags) is parsed but silently dropped. Display it as a collapsible panel per property:
  confidence badges per field (High/Medium/Low), notes inline near relevant inputs, red flags
  prominently at the top of results.

- [ ] **Sensitivity heat map** — 2-axis table showing how monthly CF or total ROI changes across
  two variables (e.g. rent × appreciation rate, or price × interest rate). Excel version had
  this; web version doesn't. Should answer "what does this deal need to be true to work" visually.

- [ ] **Deal persistence** — `localStorage` save/load. "Save Deal" button writes current 4-property
  state as JSON. Deal history sidebar lists saved sessions by date + property name. No auth needed,
  browser-local only.

---

## P2 — Workflow improvements

- [ ] **Share as URL** — encode all 4 property inputs as base64 in URL query string. One-click
  copy link that reconstructs exact state. Useful for sharing a deal for review.

- [ ] **Export PDF** — one-page deal summary: address, key metrics, 10-yr chart, exit stack.
  Use `window.print()` with a print-specific CSS layout. No library needed.

- [ ] **Break-even hold period** — given current inputs, compute the minimum hold years for
  total profit to turn positive. Show as a computed output row in the exit section.
  Currently requires manual scrubbing of the hold input.

- [ ] **Amortization table** — year-by-year principal vs. interest split, equity build, and
  remaining balance. Useful for BRRRR timing and refi window planning. Could be a 4th tab.

- [ ] **BRRRR / refi scenario** — after N years, cash-out refi: new loan on appraised value,
  pull equity, reset loan balance, recalculate remaining hold. Modal with refi inputs:
  new rate, LTV, closing costs. High value for PNW investors where the thesis is often
  appreciation + pull equity.

---

## P3 — Data integrations

- [ ] **Live mortgage rate feed** — pull current 30-yr investment property rate from FRED or
  Freddie Mac weekly data. Show alongside the rate input as a reference. Rates move weekly
  and the difference between 6.75% and 7.25% is significant on a $600K loan.

- [ ] **King/Snohomish tax lookup** — given an address, hit county assessor API and return
  the actual current tax bill. The single highest-variance input. King County has a public
  API at `blue.kingcounty.gov`.

- [ ] **Rent comp auto-fill** — Zillow has a public Rent Zestimate endpoint. Given address,
  pre-fill rent and show the 3 nearest active rental comps inline as a sanity check.

- [ ] **Depreciation / tax overlay** — rental property depreciation is (price - land value)
  / 27.5 years. At a 35% marginal bracket that's real after-tax income. Add a rough
  after-tax cash flow row using: effective tax rate input + depreciation estimate.
  Changes the economics meaningfully on negative CF deals.

---

## P4 — Analysis depth

- [ ] **Scenario comparison chart** — bar chart showing total profit at Conservative / Base /
  Optimistic across all 4 properties. Lets you see which deal is most sensitive to assumptions.
  Currently scenarios only update the numbers, no visual diff.

- [ ] **Portfolio view** — if you own multiple properties, combined monthly cash burn, total
  capital deployed, blended cap rate, aggregate equity. Useful once past 1 deal.

- [ ] **CapEx inflation rate** — currently repairs/CapEx grow at the appreciation rate. Construction
  CPI runs ~4%/yr independently. Separate input or hardcoded 3.5% would be more accurate.

- [ ] **HOA escalation** — HOA fees modeled at 2%/yr (same as taxes/insurance). Many HOAs run
  3–5%/yr. Separate HOA growth rate input.

- [ ] **Vacancy lease-up** — currently vacancy is static. Year 1 of a new rental often runs higher
  (10%+). Allow year-1 vacancy override vs. steady-state rate.

---

## Done

- [x] Multi-property inputs (4 properties, tabs)
- [x] Live calculation on every keystroke
- [x] 10-year pro forma with rent growth, expense inflation, NOI, CF, property value
- [x] Exit analysis: appreciation-based sale + cap-rate floor, cumulative CF, true total profit
- [x] Annualized ROI (CAGR) including cumulative cash flows
- [x] Comparison table (all 4 properties side-by-side, best/worst highlighted)
- [x] Conservative / Base / Optimistic scenario presets
- [x] JSON paste + parser (handles both flat and nested Claude research output)
- [x] Load saved properties from data/ files (via file picker)
- [x] Tooltips on all input labels with PNW benchmarks
- [x] Color coding: teal = positive/good, red = negative/bad, yellow = borderline
- [x] SVG cash flow chart (annual CF + cumulative)
- [x] Dark theme, monospace numbers, responsive layout
