# The Infusion Reimbursement Index — Methodology

**Issue 01, v2.0 · Vintage 2026-Q2-06 · Published 2026-08-12**

These are **contracted rates published in insurers' own machine-readable files — not amounts
paid on a claim.** Every figure in the report is a contracted rate unless explicitly labelled
otherwise. Nothing here describes what a practice actually collected.

---

## 1. Source and construction

Rates come from Transparency-in-Coverage machine-readable files published by 37 insurer
filings, vintage 2026-Q2-06. Each filing is mined, de-ghosted and rolled to a benchmark cube
at the grain **payer × state × billing code × billing class × negotiated type**.

The 37 filings are near-redundant snapshots of one corpus rather than disjoint shards: the same
cell appears in as many as 26 filings with an identical median rate to the cent and TIN counts
within 1%. Cells are therefore **collapsed across filings** (median of the filing medians, max
of the TIN counts), not pooled — pooling would inflate every count roughly 25×.

Per-filing reconciliation: our recomputed median is compared against that filing's own published
benchmark. All 37 reconciled at **100.0% exact** across ~21M cells.

## 2. Quality tiers

| Tier | Rule | Cells |
|---|---|---|
| PUBLISHABLE | n_tin ≥ 25, frac_ok ≥ 0.5, frac_penny < 0.2, rate within [0.5, 10]× ASP, real negotiated or fee-schedule type | 829,646 |
| PROVISIONAL | n_tin ≥ 10, rate within [0.3, 20]× ASP | 508,684 |
| SUPPRESSED | imputed (`derived`/`per diem`), thin, out-of-band, or unanchored to ASP | 2,615,117 |

**Every headline figure uses PUBLISHABLE cells only.** The suppressed share (66.1%) is higher
than the previous issue's 40.7% because the corpus grew 4× by adding all 37 filings, and the
added cells are disproportionately thin — publishable cells themselves rose from 328,877 to
829,646.

**Tail truncation is real and matters.** The publishable gate requires the rate to fall within
0.5–10× ASP, so percentiles and the underwater list are computed on a variable clipped at both
ends. Rates outside that band are not errors by definition, but we cannot distinguish a genuine
extreme contract from a unit error at that distance, so they are withheld.

## 3. Denominator

Rates are expressed as a multiple of **ASP** (average sales price). Medicare pays **ASP + 6%**
by statute, i.e. 1.06×, before sequestration; after sequestration the effective figure is about
1.043×. CMS's ODACS survey puts non-340B acquisition cost at about **ASP + 2.7%** (1.027×).
These three lines are the benchmarks used throughout.

Reading ×ASP as a margin proxy breaks in two known cases: **340B** pricing, where acquisition
cost is far below ASP, and drugs in **shortage**, where spot acquisition can exceed it.

Drugs are withheld from the practice-facing lookup where the ASP denominator is missing,
provisional or under $1.00, or where the code is an oral formulation — the price is real but
the ratio would not be.

## 4. Known defects corrected in v2.0

- **Biosimilar filter.** The flag matched any hyphen in a generic name, which is the FDA
  four-letter suffix carried by newly licensed *originators*. 117 brand biologics ($13.4B of
  Part B spend — Darzalex Faspro, Vabysmo, Enhertu, Padcev among them) were wrongly excluded
  from the headline universe. Corrected to the CMS Q5xxx range, which is the biosimilar
  convention; every hyphenated non-Q5 code in this corpus is an originator or antibody-drug
  conjugate.
- **Two drug labels** were wrong at source: J0585 named the cosmetic product rather than the
  therapeutic one, and J0129 named the self-administered autoinjector rather than the IV code.
- **Blue Shield of California** was published in v1.4 at 1.683× spend-weighted and described as
  the highest-paying insurer in the country. On the rebuilt corpus it is 1.047× with
  essentially unchanged drug coverage (733 → 728 drugs). The earlier figure was wrong. Any
  v1.4 citation of it is withdrawn.

## 4b. The headline universe

The flagship share ("drugs carrying 85% of Part B drug spend…") is computed **excluding skin
substitutes**, consistent with every other conclusion-bearing surface. Including them the figure
is 87.1% of $53.4B — the exclusion makes the headline smaller. Reference lines, **as originally published on an ASP = 100 scale** (see the correction below — the
index is actually payment-limit scaled): 56.5% of spend below 100, 85.0% below 102.7 (acquisition),
92.0% below 104.3, 94.3% below 106.

## 4c. Newer exhibits and their sources

- **Measured-margin check** — for drugs with a NADAC-measured (non-circular) acquisition
  estimate (n = 77), margin = median contracted index − measured acquisition index. NADAC
  refreshes monthly; the subset skews multi-source and is labeled as such.
- **The moving denominator** — CMS quarterly ASP files, 2022–2026 (preliminary quarters
  excluded). Spend-weighted payment-limit trend; the "double squeeze" list = drugs below the
  acquisition benchmark with a falling payment limit.
- **Fee-schedule census** — all 37 filings analyzed at practice (TIN) grain, professional
  class, cells with ≥25 practices. Modal share is approximate (row grain includes modifiers).
- **Drug pages** (28) and **payer pages** (57) render the same corpus at entity grain; drug
  pages carry per-payer tables, ASP history, and — only where measured — margin sections.
- **Refresh cadence:** MRF corpus quarterly · CMS ASP quarterly · NADAC monthly · policy
  snapshot on Clearance refresh · Part B utilization annually.

## 5. Statistics and their limits

**Dispersion.** The Same-Drug Spread was previously computed as max ÷ min across insurers in a
market. That estimator rises mechanically with the number of insurers filing — measured on this
corpus it climbs from 1.401× at 6 insurers to 1.937× at 20+, while p90/p10 stays flat
(1.256 → 1.181). Max/min therefore partly measures filing coverage rather than disagreement,
and is reported only as a labelled extremes statistic.

**Carrier attribution.** 119,260 publishable cells (14.4%) carry no resolvable carrier label.
They are retained in the Index and excluded from all payer-level tables.

**Tested and rejected.** A "shared pricing engine across nominally independent insurers" signal
did not survive removal of the drug × state main effect (residual correlation median 0.056,
p99 0.859) and is not published as a finding.

**Known open questions.** The site-of-care matched-pair count has not changed across a 4×
corpus expansion and is being re-derived. The preferred-vs-non-preferred steering result rests
on 19 policies and reverses sign when computed unpaired; it is reported paired, with its n, and
carries no directional claim.

## 6. What this data cannot tell you

A practice is paid `rate × (1 − denial) × collection − acquisition − wastage − prior-auth labor`.
This report publishes the first term only. No public dataset supports a drug-level denial
multiplier: CMS and KFF publish payer-level rates with no procedure-code field, and no public
days-to-pay or underpayment file exists at any grain. Administration codes (96365, 96413) are
billed separately and are not in this dataset, so a drug-line ratio is not a profitability
finding on its own.

## 7. Reproducing this

`report_data.json` carries every published figure. The corpus is rebuilt by aggregating each
filing to cube grain, collapsing across filings, applying the tier rules above, and re-running
the analysis scripts. Source URLs, their Wayback archive links and archive status are
listed in `sources.json` (37 payers; 27 archived, 10 unarchived at publication). SHA-256
checksums for every published artifact are in `sha256sums.txt`.

*Corrections: open an issue against the published dataset, or write to the address on the
report. Errata are recorded in the changelog on the page and in this file.*

## Correction — 22 September 2026

**The index is scaled to Medicare's payment limit (ASP + 6%) = 100, not to ASP = 100.**

Verified against the CMS July 2026 Part B Payment Limit File on six codes:

| Code | Median contracted rate | CMS payment limit | rate ÷ limit × 100 | Published index |
|---|---|---|---|---|
| J0129 | $44.77 | 45.859 | 97.6 | 97.6 |
| J0585 | $6.51 | 6.506 | 100.1 | 100.1 |
| J1306 | $12.69 | 12.905 | 98.3 | 98.3 |
| J3032 | $20.55 | 21.101 | 97.4 | 97.4 |
| J0717 | $3.87 | 3.444 | 112.4 | 113.5 |
| J3262 | $5.61 | 5.408 | 103.7 | 103.7 |

The published index equals rate ÷ payment limit. Because the payment limit already includes the
statutory 6%, every figure reads about 6% low against ASP.

**What changes.** Reference lines drawn on an ASP scale (acquisition 102.7, Medicare 106) were compared
against payment-limit-scaled rates. On the corrected basis, spend-weighted across 806 codes and $53.4B
of Part B drug spend:

| | As published | Corrected |
|---|---|---|
| Median index vs ASP | 101.5 | ~106 |
| Share of spend below acquisition (ASP+2.7%) | 85.0% | **~8%** |

**What does not change.** The NADAC comparison — 44% of the 77 directly measured drugs are contracted
below their own measured acquisition cost — puts both sides on the same scale, so it is unaffected.
Payer dispersion, p90/p10 spreads, grades and rankings are ratios and are likewise unaffected.

**Why it happened.** The upstream reference column is named `asp_per_unit` but holds the payment limit.
Its own builder documents this; the consumers did not. A separate defect in the practice benchmark
packages multiplies that column by 1.06 a second time; those packages were never sent to anyone.

**Fix in progress.** The anchor is being corrected at source and the issue regenerated, with a check
that fails any artifact whose reference disagrees with the CMS payment limit file by more than 0.5%.
