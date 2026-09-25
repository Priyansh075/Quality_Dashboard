# Process FMEA & SPC Quality Dashboard

A process-quality monitoring workbook for a simulated automotive sub-assembly
line, built to demonstrate a practical, end-to-end quality-engineering
workflow: **FMEA risk ranking → SPC process monitoring → Pareto root-cause
prioritization**, tied together in a single interactive dashboard.

## Why this project

Quality functions at automotive OEMs and Tier-1 plants (Production, Quality,
and Logistics support roles included) lean heavily on a small set of
standard tools — FMEA, control charts (SPC), and Pareto analysis — to decide
*where* to spend limited inspection and improvement effort. This project
rebuilds that workflow from scratch on a synthetic dataset to show the
mechanics behind each tool rather than just naming them on a resume.

## What's inside

| Sheet | What it shows |
|---|---|
| **Dashboard** | Key metrics at a glance — total inspections, total defects, top failure mode, highest RPN, and an estimated process capability (Cpk) |
| **Raw_SPC_Data** | 25 subgroups × 5 samples of a weld-nugget-diameter measurement, with per-subgroup mean (X-bar) and range (R) computed live, plus the control-limit calculations |
| **SPC_Charts** | X-bar chart and R chart with 3-sigma control limits (UCL/CL/LCL), for spotting process drift or instability |
| **Raw_Defect_Log** | 300 synthetic inspection records across 3 stations (BIW line, paint shop, final assembly) and 5 known failure modes |
| **Defect_Pareto** | Failure modes ranked by frequency with cumulative %, plus a Pareto chart, to identify the "vital few" causes driving most defects |
| **FMEA** | Process FMEA table (Severity × Occurrence × Detection → RPN) for the top 5 failure modes, with conditional-formatted risk levels and a recommended action for each |

## Methodology

- **FMEA** — each failure mode is scored 1–10 on Severity, Occurrence, and
  Detection (per standard AIAG practice), and Risk Priority Number
  `RPN = S × O × D` is calculated live via formula, not hardcoded. RPN ≥ 150
  flags red (high risk), 80–149 flags yellow (moderate), below 80 flags
  green.
- **SPC (control charts)** — X-bar and R control limits use standard
  Shewhart constants for a subgroup size of n = 5 (A2 = 0.577, D3 = 0,
  D4 = 2.114), applied to the subgroup means/ranges to compute
  `UCL = X̄̄ + A2·R̄` and `LCL = X̄̄ − A2·R̄`.
- **Process capability (Cpk)** — estimated using `σ̂ = R̄ / d2` (d2 = 2.326
  for n = 5) against an assumed ±0.5 mm tolerance band around the 6.00 mm
  target.
- **Pareto analysis** — failure-mode counts are pulled from the defect log
  with `COUNTIF`, ranked descending, with cumulative percentage tracked to
  identify the 80/20 split of causes vs. defect volume.

## Data

All data is **synthetically generated** (see `gen_data.py`) — no real
production or company data is used. The weld-diameter measurements include
a deliberate mild process drift starting at subgroup 19, so the control
chart has something realistic to detect. Failure-mode frequencies follow a
Pareto-like distribution (a few modes account for most defects), as is
typical on a real line.

## Tools used

Python (`pandas`, `numpy`) for data generation · `openpyxl` for building the
workbook with live formulas, conditional formatting, and native Excel charts
· LibreOffice headless (`recalc`) to verify every formula evaluates
correctly with zero errors before shipping.

## Files

```
gen_data.py                       # generates spc_raw_data.csv and defect_log.csv
build_workbook.py                 # builds Quality_FMEA_SPC_Dashboard.xlsx from the data
spc_raw_data.csv                  # raw SPC measurements (generated)
defect_log.csv                    # raw inspection defect log (generated)
Quality_FMEA_SPC_Dashboard.xlsx   # final deliverable
```

## Reproducing it

```bash
pip install pandas numpy openpyxl
python gen_data.py
python build_workbook.py
```

This regenerates the two CSVs and rebuilds the workbook with fresh formulas.
All charts and control limits recalculate automatically from the input data
— change a value in `Raw_SPC_Data` or `Raw_Defect_Log` and the Dashboard,
Pareto ranking, and FMEA risk colors update accordingly.

## Limitations & next steps

- Data is synthetic, sized for a clear demonstration rather than production
  scale.
- FMEA severity/occurrence/detection scores are illustrative judgment calls,
  not derived from real failure-rate data.
- A natural extension would be linking the Pareto's top failure mode
  directly back to the FMEA's highest-RPN row, and adding a second
  characteristic (e.g., paint thickness) to the SPC monitoring.
