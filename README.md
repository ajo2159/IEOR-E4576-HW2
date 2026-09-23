# IEOR E4576 — Assignment 2

**Data-Driven Models in Finance — Columbia University, Fall 2026**

This repository contains the analysis for Assignment 2, covering:

1. **CFTC Commitments of Traders (COT) positioning in WTI crude oil (CL)**
   - accounting-identity checks for legacy and disaggregated COT classifications;
   - managed-money net positioning, open-interest normalization, and the trailing three-year COT index;
   - commercial hedging pressure;
   - contemporaneous positioning/return analysis; and
   - predictive regressions at 1-, 4-, and 13-week horizons.

2. **Seasonal commodity calendar spreads into expiry**
   - Natural Gas: `NG[H][J0]` (March/April);
   - Corn: `ZC[U][Z0]` (September/December);
   - SRW Wheat: `ZW[K][N0]` (May/July); and
   - Lean Hogs: `HE[V][Z0]` (October/December).

The final written report is included as `IEOR_E4576_HW2.pdf`, and the executable analysis is in `IEOR_E4576_HW2.ipynb`.

## Repository structure

```text
IEOR-E4576-HW2/
├── IEOR_E4576_HW2.ipynb
├── IEOR_E4576_HW2.pdf
├── README.md
├── requirements.txt
└── futspread/
    ├── __init__.py
    ├── __main__.py
    ├── cli.py
    ├── codes.py
    ├── condense.py
    ├── download.py
    ├── instances.py
    ├── plotting.py
    ├── store.py
    └── universe.py
```

## Key results

### COT positioning

- Both the legacy and disaggregated COT accounting identities hold to within one contract in **100.0% of weeks** in the sample.
- Commercials are net short WTI crude in **96.7% of weeks**.
- The contemporaneous correlation between roll-consistent CL returns and the weekly change in managed-money net positioning is **+0.3959**.
- The contemporaneous regression slope is approximately **+1,497 contracts per +1% CL return**.
- Predictive evidence is weak after accounting for overlapping-return dependence. For example, the 13-week COT-index regression has an OLS t-statistic of **−2.87**, but a HAC/Newey-West t-statistic of **−1.22** under the assignment timing convention. A post-release timing robustness check gives a very similar HAC t-statistic of **−1.27**.

### Seasonal calendar spreads

| Spread | Expected move | Agreement | Mean P&L | t-stat |
|---|---:|---:|---:|---:|
| `NG[H][J0]` Mar/Apr | Down | 25/27 = 92.6% | −0.4183 | −2.77 |
| `ZC[U][Z0]` Sep/Dec | Down | 25/26 = 96.2% | −11.9327 | −4.83 |
| `ZW[K][N0]` May/Jul | Down | 21/26 = 80.8% | −12.8558 | −2.45 |
| `HE[V][Z0]` Oct/Dec | Up | 19/25 = 76.0% | +2.3450 | +2.53 |

P&L is measured in each spread's quoted price units over the common `(-120, 0)` trading-day window.

## Methodological controls

The notebook includes several controls intended to prevent silent data or alignment errors:

- **Front-month schedule:** non-overlapping CL holding intervals with rolls five observed CL sessions before last trading day.
- **Return construction:** a self-financing roll-consistent return index so old/new contract level gaps are not counted as outright returns.
- **COT index:** a strict 156-report history for the trailing three-year range, with an explicit attrition audit separating warm-up loss from missing-data loss.
- **Holiday alignment:** the latest prior settlement is accepted only within three calendar days.
- **Predictive horizons:** exact Friday + 7h-day endpoints for `h ∈ {1, 4, 13}`.
- **Inference:** requested OLS t-statistics plus HAC/Newey-West robustness statistics; lead/lag diagnostics also report HAC statistics.
- **Information timing:** the assignment's Friday-start regressions are preserved, with an additional robustness test beginning at the first CL settlement strictly after Friday publication.
- **Seasonal windows:** Question 2 uses the instructor-supplied `futspread` package unchanged. The notebook calls it with `min_coverage=1.0` and explicitly asserts complete coverage of all 121 offsets from −120 through 0 for every retained annual instance.
- **Validation:** explicit checks cover duplicate keys, invalid roll anchors, stale matches, non-monotone dates, forward-return timing, COT-index bounds, and seasonal-window completeness.

## Data access

The analysis uses licensed data accessed through **WRDS**, including CFTC COT data and futures contract/settlement data. Raw WRDS data are **not redistributed** in this repository.

A valid WRDS account is required to reproduce the data pulls. The notebook resolves the WRDS username in either of two ways:

1. set the `WRDS_USERNAME` environment variable; or
2. enter the username interactively when prompted.

No password or credential file should be committed to this repository.

## Setup

Create a clean Python environment, then install the dependencies:

```bash
python -m venv .venv
```

Activate it on Windows:

```bash
.venv\Scripts\activate
```

or on macOS/Linux:

```bash
source .venv/bin/activate
```

Then install the pinned analysis dependencies and notebook tooling:

```bash
pip install -r requirements.txt
```

The executed notebook records Python **3.13.9** as the submission environment.

Optionally set the WRDS username before launching Jupyter:

**Windows PowerShell**

```powershell
$env:WRDS_USERNAME="your_wrds_username"
```

**macOS/Linux**

```bash
export WRDS_USERNAME="your_wrds_username"
```

Launch the notebook:

```bash
jupyter lab IEOR_E4576_HW2.ipynb
```

Run the notebook from top to bottom. The notebook will query WRDS, construct the WTI nearby and roll-consistent return series, run the COT analyses, and load/build the seasonal spread analyses through the local `futspread` package.

## Reproducibility notes

Question 1 uses explicit source-data snapshot caps:

- CFTC COT observations: through **2026-09-08**
- CL settlement data used in Question 1: through **2026-04-03**

The notebook records row counts, date spans, Python/package versions, and SHA-256 fingerprints for the licensed COT and CL settlement extracts without redistributing vendor data.

Question 2 uses the instructor-supplied `futspread` package unchanged. The notebook records the package path/signature, resulting combo date spans, terminal-date source information, and verifies exact 121-point `[-120, 0]` coverage for every retained annual instance.

- The executed notebook is the authoritative executable analysis.
- The PDF report summarizes the final results and interpretation.
- `futspread/` is the instructor-supplied supporting package used for Question 2.
- Local caches, WRDS credentials, downloaded raw data, and Python bytecode are not committed.

## Data and licensing

WRDS and the underlying vendor datasets are subject to their respective license terms. This repository contains analysis code and derived results only; it does not redistribute licensed source data.
