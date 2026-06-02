# Healthcare Staffing Account Manager — Case Study & Data Analysis

A end-to-end account prioritization analysis built for a healthcare staffing marketplace case study. Combines invoice data analysis, payment behavior profiling, and qualitative assessment to rank and develop action plans for 10 Skilled Nursing Facility (SNF) accounts.

---

## Project Overview

Given a dataset of 227 invoices across 10 SNF accounts spanning October 2024 to April 2025, the goal was to:

1. Identify the top 5 accounts requiring immediate attention
2. Rank them by combined financial risk and strategic value
3. Develop specific, evidence-based action plans for each

---

## Repository Structure

```
├── notebooks/
│   └── Case_Study_Analysis.ipynb       # Full analysis pipeline
├── data/
│   └── invoice_payment_data.xlsx       # Invoice & payment dataset
├── outputs/
│   └── case_study_findings.xlsx        # Exported scoring tables
├── report/
│   └── Account_Manager_Case_Study.pdf  # 3-page written deliverable
└── README.md
```

---

## Analysis Pipeline

### 1. Data Loading & Cleaning
- Loaded invoice dataset (227 rows, 7 columns)
- Parsed date columns, validated data types
- Checked for missing values and anomalies

### 2. Duplicate Invoice Correction
Identified 7 duplicate invoice pairs where revised invoices (e.g. `INV159` → `INV159-2`) had been issued and paid, while originals remained in the system as outstanding. Removing superseded originals corrected the total outstanding balance by ~$235K across the portfolio.

### 3. Payment Behavior Analysis
Split dataset into paid and unpaid subsets to compute separate metrics:

- **`avg_days_late_when_paying`** — for paid invoices only: how many days after the due date did payment arrive on average?
- **`avg_days_overdue`** — for unpaid invoices only: how aged is the current outstanding balance relative to due dates?
- **`pct_paid_late`** — what percentage of paid invoices were late?

This separation was deliberate — conflating paid and unpaid invoices in a single metric produces misleading results, particularly for accounts with long net terms (e.g. Net 45/60) where recent unpaid invoices may not yet be due.

### 4. Exposure Metrics
Two complementary risk metrics computed per SNF:

- **Avg exposure per invoice issued** = total outstanding ÷ all invoices (how much risk per billing event)
- **Avg unpaid invoice size** = total outstanding ÷ unpaid invoices only (typical size of an uncollected invoice)

### 5. Multi-Factor Scoring Model
Each SNF scored across 9 factors combining financial risk and strategic value:

| Factor | Source | Dimension |
|--------|---------|-----------|
| Net Terms | Case data | Risk |
| Account Status | Case data | Risk |
| Wallet Share | Case data | Opportunity |
| Volume Trend | Computed | Risk/Opportunity |
| Avg Days Late When Paying | Computed | Risk |
| Avg Exposure Per Invoice | Computed | Risk |
| Avg Unpaid Invoice Size | Computed | Risk |
| Harmonised Exposure Score | Computed (avg of above two) | Risk |
| Qualitative Disputes Score | Supplementary materials | Risk/Opportunity |

Higher score = higher urgency to act.

### 6. Final Rankings

| Rank | Facility | Score | Key Issue |
|------|----------|-------|-----------|
| 1 | SNF 3 | 22.5 | Suspended, 100% exposure, $168K, no response |
| 2 | SNF 9 | 19.5 | Pending status, 74.5% wallet share, AP cycle misalignment |
| 3 | SNF 2 | 18.5 | $1.15M outstanding, new account, zero prior contact |
| 4 | SNF 10 | 15.0 | Healthy billing but active clock-in operational risk |
| 5 | SNF 8 | 14.5 | Dispute pattern, $446K outstanding, 98% wallet share |

---

## Key Findings

**SNF 3** is the only suspended account with 100% billing exposure across 14 invoices. Declining invoice volume since December signals possible migration to an alternate provider, making the $168K collection window time-sensitive.

**SNF 9** was pended without investigation into root cause. Payment data shows consistent payments on the 17th of each month — confirmed by an AP email as their fixed monthly schedule. Pending was a misdiagnosis; the fix is renegotiating terms from Net 15 to Net 30 to align with their AP cycle.

**SNF 2** is a new account (onboarded December 2024) with exceptional growth trajectory ($0 to $387K/month within 4 months) but $1.15M outstanding across 15 of 17 invoices with no documented outreach. The low wallet share (3.1%) reflects recency, not ceiling.

**SNF 10** has healthy billing — all unpaid invoices within terms — but a March 2025 email from their administrator flagged HCPs not clocking in correctly. Left unresolved, this becomes a billing dispute on a high-wallet-share ($411K, 80%) account.

**SNF 8** had an active dispute resolution process under the previous AM: 6 disputed invoices, 4 already resolved via revised invoices at slight discounts, 2 in-flight. The $647K raw outstanding corrects to $446K once superseded originals are removed.

---

## Assumptions

- `Payment Date` is NaT → invoice was never paid (no partial payment data available in dataset)
- Monthly invoice amounts are used as a proxy for shift volume, per the case brief: *"The value of a facility's weekly invoice will fluctuate entirely based on the volume of shifts filled on the platform"*
- Supplementary materials (emails, voicemails, Slack) represent a complete excerpt of available communications for the period

---

## Tools & Methods

- **Python** — pandas, numpy, matplotlib
- **Jupyter Notebook** — exploratory analysis and visualisation
- **Excel** — source data
- **Data techniques** — groupby aggregation, time-series analysis, duplicate detection, payment aging buckets, cohort-style behavioral profiling

---

## Skills Demonstrated

- B2B account management thinking applied to a data problem
- Payment behavior analysis and AR aging methodology
- Multi-factor scoring model design
- Translating quantitative findings into qualitative business recommendations
- Data quality identification and correction (duplicate invoice pairs)
- Clear written communication of analytical findings under space constraints (3-page limit)
