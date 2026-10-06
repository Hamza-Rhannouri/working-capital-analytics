# Working Capital & Accounts Receivable Analytics

### From messy receivables data to cash-release opportunities

A finance analytics and analytics engineering project investigating **how much cash is tied up in accounts receivable, what collection behavior reveals, where exposure is concentrated, and what improvement opportunity management could pursue.**

The project takes a deliberately messy working-capital dataset through a complete analytical workflow:

**Python / Pandas → MySQL → Analytics Engineering → SQL → Power BI**

The final four-page executive report is designed for a **CFO / CEO / management audience**, moving from:

**Exposure → Collection Behavior → Concentration → Action**

---

## Business Question

> **How much cash is tied up in accounts receivable, what does collection behavior reveal, where is the exposure concentrated, and what cash-release opportunity could result from improving collections?**

---

## Executive Snapshot

- **14.8M MAD** and **735K EUR** in current open receivables exposure
- Eligible paid invoices average approximately **50 days** from invoice to payment
- More than **65%** of eligible paid invoices were classified as late
- A **5-day improvement in collection timing** could release approximately **1.48M MAD** and **74K EUR** from current open receivables exposure
- Source customer IDs contain **data-quality ambiguity**, requiring caution when interpreting individual customer-ID concentration

---

## Business Problem

Accounts receivable represents cash that has already been earned through invoicing but has not yet been collected.

For management, the question is not simply how much revenue has been invoiced. The more important questions are:

- How much cash remains tied up in receivables?
- How quickly are customers paying?
- How widespread is late payment behavior?
- Where is receivables exposure concentrated?
- What improvement in collection timing could translate into cash release?

This project answers those questions while also demonstrating how messy operational data can be transformed into a controlled analytical workflow.

---

## Key Findings

### 1. Receivables exposure is material

Current open receivables amount to approximately:

- **14.8M MAD**
- **735K EUR**

Open AR represents approximately **17.0% of MAD invoice value** and **15.3% of EUR invoice value**.

### 2. Collection takes roughly 50 days

Among eligible paid invoices, the average collection cycle is approximately **50 days** in both currencies.

This provides a useful baseline for evaluating collection performance.

### 3. Late payment is widespread

More than **65% of eligible paid invoices** were classified as late.

Late paid invoices averaged approximately **18 days past due**.

This suggests that collection friction is not limited to a small number of isolated cases.

### 4. Concentration matters

Open receivables are not evenly distributed across source customer IDs.

However, the source data contains customer-ID ambiguity, meaning individual IDs cannot always be interpreted as confirmed unique real-world customers.

The analysis therefore treats customer IDs as **source-system identifiers**, rather than making unsupported claims about unique customers.

### 5. Small improvements could release meaningful cash

Using current open AR and eligible collection days as the basis for a scenario analysis:

| Collection improvement | Approx. cash release |
|---|---:|
| 5 days | **1.48M MAD + 74K EUR** |
| 10 days | **2.96M MAD + 148K EUR** |
| 15 days | **4.44M MAD + 222K EUR** |

These are **scenario estimates, not forecasts**. They illustrate the potential cash impact of improving collection timing.

---

## Executive Dashboard

The Power BI report follows a four-page management story.

### 01 — AR Executive Overview

**Question:** How much cash is currently tied up in receivables?

The page establishes the size of the exposure and the overall collection baseline.

Key metrics include:

- Open AR as a percentage of invoice value
- Late payment rate
- Average collection days
- Open AR by currency

### 02 — Collections & Payment Behavior

**Question:** What does payment behavior reveal about the collection cycle?

The page examines:

- Payment timing distribution
- Late payment rate
- Average days late
- Collection behavior by currency

### 03 — Customer Exposure & Concentration

**Question:** Where is the open receivables exposure concentrated?

The page analyzes exposure across source customer IDs and highlights concentration while explicitly accounting for source-data ambiguity.

### 04 — Working Capital Actions

**Question:** What collection actions could release cash?

The page combines a collection-improvement scenario analysis with management recommendations focused on:

1. Prioritizing large exposures
2. Accelerating collections
3. Strengthening receivables controls

---

## Data & Data Quality

The project starts with a deliberately messy working-capital dataset containing **12,110 raw records across 14 source columns**.

The cleaning and validation process identified and handled issues including:

- Exact duplicate records
- Missing invoice amounts
- Invalid records
- Negative invoice amounts
- Missing payment information
- Payment amounts exceeding invoice amounts
- Incomplete open invoices
- Incomplete paid invoices
- Ambiguous customer mappings
- Ambiguous customer-region mappings
- Ambiguous payment-method mappings

The final analytical dataset contains **12,000 validated invoice records**.

Rather than silently removing problematic records, the project preserves data-quality information through explicit flags and business-rule classifications.

### Business-rule classification

Records are classified into statuses such as:

- `VALID`
- `REVIEW`
- `EXPECTED`
- `INCOMPLETE`

This allows downstream analysis to distinguish between usable financial data and records requiring caution.

---

## Technical Workflow

The project follows a layered analytical workflow:

```text
Dirty CSV
   │
   ▼
Python / Pandas
Profiling → Cleaning → Transformation → Validation
   │
   ▼
Repeatable ETL Pipeline
   │
   ▼
MySQL
Raw → Staging → Core
   │
   ▼
Analytics Engineering
Business-ready analytical models
   │
   ▼
SQL Financial Analysis
AR → Aging → Exposure → Payment Behavior → DSO → Cash Impact
   │
   ▼
Power BI
Executive dashboard and management recommendations
```

---

## Data Architecture

The MySQL database is organized into analytical layers:

```text
RAW
 │
 ▼
STAGING
 │
 ▼
CORE
 │
 ▼
ANALYTICS
```

The core layer contains the invoice-level fact data, while the analytical layer contains finance-oriented outputs used for reporting and analysis.

Key analytical tables include:

- `fin_ar_position`
- `fin_ar_aging`
- `fin_customer_exposure`
- `fin_payment_behavior`
- `fin_monthly_working_capital`
- `fin_dso_analysis`
- `fin_cash_impact_scenario`

The Power BI model then consumes the business-ready analytical outputs alongside the analytical-engineering invoice and customer-quality models.

---

## Analytics Engineering

The analytics-engineering stage transforms the cleaned/core data into business-ready analytical models.

The project includes:

- Invoice-level analytical modeling
- Customer-quality analysis
- Business-rule fields
- Data-quality flags
- Analytical model construction
- SQL-based transformations
- Model relationships
- Financial analysis outputs

The objective is not simply to produce tables, but to create a controlled path from operational records to management-level metrics.

---

## SQL Financial Analysis

The SQL analysis focuses on finance questions rather than generic database exercises.

Examples include:

- Accounts receivable position
- AR aging
- Customer exposure
- Payment behavior
- Collection days
- DSO analysis
- Cash-release scenarios

The SQL analysis is embedded in the project notebooks and executed against the MySQL analytical environment.

---

## Data Model

The Power BI model uses analytical invoice and customer-quality data together with finance-specific analytical tables.

Key relationships include:

```text
Customer Quality
       │
       ├──────────► AR Invoice
       │
       └──────────► Customer Exposure
```

A dedicated date dimension is also used for time-based analysis.

The model separates:

- Source/analytical invoice data
- Customer-quality information
- Financial analytical outputs
- Date and currency dimensions

This supports controlled filtering and avoids relying on ambiguous source relationships.

---

## Project Structure

```text
working-capital-analytics/
│
├── README.md
├── .gitignore
│
├── data/
│   ├── raw/
│   │   └── Working_capital_dirty.csv
│   └── processed/
│       └── Working_capital_cleaned.csv
│
├── notebooks/
│   ├── ANALYTICS-ENGINEERING.ipynb
│   ├── Data_Cleaning_and_Validation.ipynb
│   ├── Data_Profiling.ipynb
│   ├── Data_Quality.ipynb
│   ├── MYSQL-DATA-ARCHITECTURE & LOADING.ipynb
│   ├── REPEATABLE PYTHON ETL PIPELINE.ipynb
│   └── SQL-FINANCIAL-ANALYSIS.ipynb
│
├── powerbi/
│   └── Working_Capital_Analytics.pbix
│
└── screenshots/
    ├── page_1_ar_executive_overview.png
    ├── page_2_collections_payment_behavior.png
    ├── page_3_customer_exposure_concentration.png
    └── page_4_working_capital_actions.png
```

---

## Tools & Skills

### Data Analysis

- Python
- Pandas
- Jupyter
- Data profiling
- Data cleaning
- Data validation
- Business-rule validation

### SQL & Databases

- MySQL
- CTEs
- Window functions
- Aggregations
- Financial analysis queries
- Layered database architecture

### Analytics Engineering

- Analytical modeling
- Business-ready transformations
- Data-quality controls
- Model relationships
- Reproducible transformation workflow

### Business Intelligence

- Power BI
- Power Query
- DAX
- Data modeling
- KPI design
- Executive reporting
- Financial storytelling

### Finance

- Accounts receivable
- Working capital
- Collection cycle
- DSO
- Aging analysis
- Payment behavior
- Cash-release scenarios

---

## Reproducibility

The project is structured so that the analytical workflow can be followed from the original dirty dataset through to the final Power BI report.

### 1. Inspect the raw data

Start with:

```text
Data_Profiling.ipynb
```

This documents the initial state of the dataset.

### 2. Perform the data-quality audit

Run:

```text
Data_Quality.ipynb
```

This identifies the main quality issues and establishes the validation requirements.

### 3. Clean and validate the data

Run:

```text
Data_Cleaning_and_Validation.ipynb
```

This produces the cleaned analytical dataset and applies the business rules.

### 4. Run the repeatable ETL workflow

Run:

```text
REPEATABLE PYTHON ETL PIPELINE.ipynb
```

This demonstrates how the transformation process can be repeated rather than relying solely on manual cleaning.

### 5. Build the MySQL layers

Run:

```text
MYSQL-DATA-ARCHITECTURE & LOADING.ipynb
```

This creates and loads the database architecture.

### 6. Build the analytical models

Run:

```text
ANALYTICS-ENGINEERING.ipynb
```

This creates the business-ready analytical models.

### 7. Run the financial analysis

Run:

```text
SQL-FINANCIAL-ANALYSIS.ipynb
```

This generates the financial analysis tables used by the reporting layer.

### 8. Open the Power BI report

Finally, open:

```text
powerbi/Working_Capital_Analytics.pbix
```

to explore the four-page executive report.

---

## Management Recommendations

Based on the analysis, management should consider:

### 1. Prioritize the largest open exposures

Focus collection resources on the largest open receivables exposures rather than treating all invoices equally.

### 2. Accelerate collections

With more than 65% of eligible paid invoices classified as late, management should investigate recurring overdue behavior and prioritize accounts with persistent collection delays.

### 3. Strengthen customer-ID governance

The ambiguity in source customer IDs limits reliable customer-level exposure analysis.

Improving master-data governance would make concentration analysis more reliable and improve downstream reporting.

### 4. Track collection improvement as a cash KPI

Collection timing should be monitored not only as an operational metric but as a working-capital lever.

Even a modest improvement in collection timing can translate into meaningful cash-release potential.

---

## Important Analytical Limitations

This project intentionally avoids making claims that the available data cannot support.

### Currency

MAD and EUR exposures are analyzed separately.

The project does **not** apply an FX conversion because no authoritative exchange-rate assumption is included in the source data.

Therefore, MAD and EUR monetary values should not be added together into a single monetary exposure.

### Customer identity

Source customer IDs are not guaranteed to represent unique real-world customers.

Customer concentration results should therefore be interpreted as **source customer-ID exposure**, not definitive legal/customer master exposure.

### Cash-release scenarios

The 5-, 10-, and 15-day scenarios are illustrative calculations based on current open AR and eligible collection days.

They are **not forecasts** and do not represent guaranteed cash recovery.

---

## Why This Project Matters

This project demonstrates a complete finance analytics workflow rather than a standalone dashboard.

The objective was to connect:

**Messy operational data**

→ **validated data**

→ **structured analytical models**

→ **financial analysis**

→ **executive decision support**

The final output is designed to answer the question a finance leader ultimately cares about:

> **Where is cash tied up, why is it tied up, and what can management do about it?**
