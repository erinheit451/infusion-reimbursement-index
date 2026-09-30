# Infusion Reimbursement Index — Issue 01 (2026-Q2), open data

What commercial health insurers contract to pay for infused and injected (J-code) drugs, benchmarked to Medicare's payment limit. Built by [CareCost](https://carecostestimate.com) from the Transparency-in-Coverage machine-readable files that insurers must publish. Report: <https://carecostestimate.com/infusion-index>.

**These are contracted rates from insurers' own published files, not amounts paid on claims.**

## The scale: read this first

Every index column ends in `_pl100`. **100 = the CMS Medicare Part B payment limit for the drug (ASP + 6%) in the same quarter.**
- 100 means the insurer's median contracted rate equals what Medicare's payment limit is.
- 94.3 is about ASP (the drug's average sales price).
- 96.9 is about typical non-340B acquisition cost (ASP + 2.7%, CMS ODACS survey).

Report Issue 01 first labeled this scale as "ASP = 100". That label was wrong; it was corrected on 22 September 2026 (see `METHODOLOGY-issue01.md`, section "Correction — 22 September 2026"). This deposit uses the corrected label. Columns that compared against the old, mislabeled reference lines have been **removed**, not re-derived:
- `vs_acquisition_pct` (payer scorecard) — removed
- the "underwater" drug list and the ranked risk list — not included

Ratios are not affected by the scale: payer grades, p90/p10 spreads, fee-schedule census figures, and rankings by index are unchanged. Spot check against the CMS July 2026 Payment Limit File: J0129 97.6, J0585 100.1, J1306 98.3, J3032 97.4, J3262 103.7 — rate ÷ payment limit × 100.

## Files

| File | Rows | What it is |
|---|---|---|
| `data/drug-reference.csv` | 806 | Per drug (HCPCS code): median commercial contracted rate as an index to the payment limit, the low/high index across insurers, number of insurers, Medicare Part B spend (USD millions). `specialty` is as published in the report. |
| `data/payer-scorecard.csv` | 57 | Per insurer: grade, spend-weighted index (all drugs, brand, biosimilar), variability, and coverage counts. |
| `data/fee-schedule-census.csv` | 37 | Per insurer filing: how many distinct rates per market, share of practices on the modal rate, practice-level p90/p10 spread, large-vs-small practice ratio. |
| `METHODOLOGY-issue01.md` | — | Full method, quality tiers, known defects, and the correction note. |

## Source and method in brief

- 37 insurer filings, vintage 2026-Q2-06; cells at payer × state × code × billing class × negotiated type.
- Only PUBLISHABLE cells are used (n_tin ≥ 25, rate within 0.5–10× ASP, real negotiated or fee-schedule type): 829,646 of 3,953,447 cells.
- Drug prices from the CMS ASP / Payment Limit files; spend from Medicare Part B utilization.

## License and citation

CC BY 4.0. Cite as:

> CareCost (2026). *The Infusion Reimbursement Index, Issue 01 (2026-Q2), open data* [Data set]. https://carecostestimate.com/infusion-index

Corrections: corrections@carecostestimate.com. Press and custom pulls: press@carecostestimate.com.
