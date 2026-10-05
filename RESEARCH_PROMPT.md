# Claude Research Prompt

Use this prompt in a new Claude session with one or more Zillow/Redfin URLs.
Claude will return a JSON block you can paste directly into the analyzer.

---

## Prompt

```
I am evaluating a rental property for investment. Here is the listing: [PASTE URL]

Research this property deeply and return a JSON block I can paste into my investment
analyzer. For each number: give your best single estimate (not a range), confidence
level (High / Medium / Low), and a one-line source or reasoning in the notes object.

Use this exact JSON structure:

{
  "name": "[full address]",
  "type": "[SFH / Condo / Townhouse / MFH]",
  "price": "[number]",
  "arv": "[number]",
  "closing": "[number]",
  "reno": "[number]",
  "hoa_mo": "[number]",
  "yr_built": "[year]",
  "sqft": "[number]",
  "beds": "[number]",
  "baths": "[number]",
  "dp_pct": "20",
  "ir_pct": "7.0",
  "term": "30",
  "rent_mo": "[number]",
  "other_mo": "0",
  "vac_pct": "[number]",
  "ptax": "[number]",
  "ins": "[number]",
  "mgmt_pct": "8",
  "rep_pct": "[0.75 or 1.0 or 1.25 based on age]",
  "capex_pct": "[0.25 or 0.5 or 0.75 or 1.0 based on type and age]",
  "landscape": "[number]",
  "other_exp": "0",
  "appx_pct": "[10yr zip CAGR, conservative]",
  "hold": "7",
  "sell_cost_pct": "6",
  "rent_grow_pct": "[3yr zip rent growth]",
  "exit_cap_pct": "[4.5 or 5.0 based on local market]",
  "_meta": {
    "source": "Claude research — [date]",
    "confidence_low": ["[field names with Low confidence]"],
    "notes": {
      "[field]": "[confidence + source + caveat]"
    },
    "red_flags": [
      "[item 1]",
      "[item 2]"
    ]
  }
}

RULES:
- All values as strings. All % fields as plain numbers (7.0 not 0.07).
- If researching multiple listings, return an array: [{...}, {...}]
- Leave a field "" only if truly unavailable — never guess wildly

RESEARCH INSTRUCTIONS BY FIELD:

price: Use listing price. Note if pending or recently reduced.

arv: Pull 3 comparable sales within 0.5 miles, same bed/bath count, sold in last
  6 months. Average them. If fewer than 3 comps, note this. Low confidence if
  no clean comps.

closing: Estimate 2% of purchase price for conventional loan in WA.

reno: Based on year built, listing photos, any disclosed condition issues.
  Use 0 if move-in ready. Low confidence without inspection.

hoa_mo: From listing. If condo, verify with HOA docs or Redfin fee field.
  Note what HOA covers (exterior, roof, water, etc.).

rent_mo: Check Zillow Rent Zestimate for this address. Also check active rental
  listings within 0.5 miles, same bed/bath. Give realistic market rent —
  haircut Zillow by 5-8% for conservative estimate. Medium confidence.

vac_pct: Use 4% for King/Snohomish detached. Use 5% for condos in large
  rental buildings. Use 6% if high-rise or lots of competing units.

ptax: CRITICAL — look up the actual current tax bill from the county assessor:
  King County: https://blue.kingcounty.gov (search by address)
  Snohomish County: https://snohomishcountywa.gov/assessor
  Do NOT estimate. Find the real number. Flag if senior/disability exemption
  is applied — it WILL NOT transfer to new owner and the bill will jump.

ins: Landlord policy estimate:
  Condo: $900/yr
  SFH under $700K: $1,800/yr
  SFH $700K–$1M: $2,200/yr
  SFH over $1M: $2,800/yr

rep_pct: Based on age of property:
  Under 15 years: 0.75
  15–25 years: 1.0
  Over 25 years: 1.25
  Condo (HOA covers exterior): 0.5

capex_pct: Based on type and age:
  Condo: 0.25 (interior only — HOA covers structural)
  SFH under 15 years: 0.5
  SFH 15–25 years: 0.75
  SFH over 25 years: 1.0

landscape: 0 for condos. $600/yr for SFH if tenant handles lawn.
  $1,200/yr if landlord handles.

appx_pct: Pull the 5-year AND 10-year median home price CAGR for this specific
  zip code from Zillow Research or Redfin historical data. Report both in notes.
  Use the 10-year figure as your base case. Low confidence.

rent_grow_pct: Pull 1-year and 3-year rent growth for this zip from Zillow
  Research, ApartmentList, or CoStar if available. Report both in notes.
  Use 3-year as base case. Low confidence.

exit_cap_pct: Research what cap rates comparable SFH or condo rentals are
  trading at in this zip. Check LoopNet, CoStar news summaries, or local
  market reports. Use 4.5% for detached SFH in King/Snohomish if no data.
  Use 5.0% for condos. Low confidence.

RED FLAGS to check and report:
- Any senior/veteran/disability tax exemptions on the current bill
- HOA pending litigation, special assessments, or reserve fund below 10%
- Permit history: unpermitted additions, open permits, code violations
- Listing history: days on market, price reductions, relisted
- Neighborhood: new supply pipeline, zoning changes, school rating trend
- Flood zone, environmental issues, proximity to flight path/highway
- Year built triggers: pre-1978 (lead), pre-1980 (asbestos), 1990s (Chinese drywall risk)
```

---

## Notes on using the output

- **Property taxes** are the highest-variance input. If the listing shows an unusually
  low tax bill, it's almost certainly an exemption that won't transfer. The real bill
  can be 5–10× higher. Always verify before trusting the model.

- **Rent estimate** — Claude's rent estimate should be treated as medium confidence.
  Cross-check against active Craigslist/Zillow rentals in the zip before plugging in.

- **Appreciation rate** — this single input drives most of the total ROI. Model at
  least two scenarios: the 10yr historical rate AND the current 1yr rate (which for
  Bothell/Kirkland is often negative right now). The deal should survive at conservative.

- **Hold period** — default in prompt is 7 years. At current rates, the math often
  doesn't work at 5 years. Model 5, 7, and 10 and look at where total profit turns positive.
