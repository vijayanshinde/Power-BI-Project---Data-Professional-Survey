# Power BI Project - Data Professional Survey

> Power BI portfolio project built from a real survey of data professionals (≈630 responses). The dataset was collected via social posts (LinkedIn, Twitter) and cleaned/transformed in Power Query. Visualized in Power BI Desktop.

---

## Table of contents

* [Overview](#overview)
* [Files in this repo](#files-in-this-repo)
* [Getting started](#getting-started)
* [Data cleaning & transformations (Power Query)](#data-cleaning--transformations-power-query)
* [Report visuals & explanation](#report-visuals--explanation)
* [Key measures & sample formulas](#key-measures--sample-formulas)
* [Theme & formatting](#theme--formatting)
* [Limitations & caveats](#limitations--caveats)


---

## Overview

This project demonstrates building a clean, interactive Power BI report from a raw survey CSV of data professionals. The goals were:

* Clean/transform raw survey data in **Power Query** (no Excel prep)
* Create useful aggregations and visuals (cards, bar charts, treemap, gauges, donut charts)
* Apply a custom theme and tidy formatting to produce a portfolio-ready dashboard

The raw survey includes responses to questions such as job title, yearly salary (ranges), favorite programming language, happiness ratings (0–10), country, gender, and difficulty breaking into the field.

---

## Files in this repo

```
/ (repo root)
├─ data/
│  └─ survey_responses.csv        # raw CSV (original survey export)
├─ powerbi/
│  └─ DataProfessionalSurvey.pbix # Power BI Desktop file (report + queries)
├─ README.md                      # this file
└─ .gitignore
```

> If you don't have the `.pbix` file, you can reproduce the report by opening `survey_responses.csv` in Power BI Desktop and following the steps in **Data cleaning** below.

---

## Getting started

1. Install **Power BI Desktop** (Windows).
2. Clone or download this repository and open `powerbi/DataProfessionalSurvey.pbix` in Power BI Desktop — or:

   * Open Power BI Desktop → **Get Data → Text/CSV** → select `data/survey_responses.csv`.
   * Click **Transform Data** to open Power Query and apply the transformations described next.
3. Close & Apply to load the cleaned table, then open the report canvas to view the visuals.

---

## Data cleaning & transformations (Power Query)

All transformations were performed in Power Query (Transform Data). Major steps taken:

1. **Remove unused columns**

   * Drop personally identifying / unnecessary columns (e.g., email, extra timestamps, survey system fields) to reduce clutter.

2. **Standardize job title** (`Which title fits you best`)

   * `Split Column` by delimiter `(` (left-most) — this separates entries like `Other (please specify)` from the main label.
   * Keep the left part as the canonical job title (e.g., `Data Analyst`, `Data Scientist`).
   * Collapse rare/unique entries into `Other` to avoid dozens of almost-identical categories.

3. **Standardize favorite programming language**

   * `Split Column` by delimiter `:` (left-most) to separate free-text `Other: ...` entries from the main choices.
   * Keep primary language label for visual counts.

4. **Make salary numeric (approximate)**

   * Duplicate the `Current yearly salary` column (we keep the original raw text and work on the copy).
   * `Split Column` using `Digit to Non-digit` to separate numeric parts from suffixes (e.g., `106-125000` into numeric pieces).
   * Remove trailing characters (`K`, `,`, `+`, `–`), replace `+` with a concrete number where helpful (e.g., `225+` → `225` for mid-point calculation), and convert parts to numeric type.
   * Add a **Custom Column** `AverageSalary` using the midpoint formula:

```m
= (Number.FromText([SalaryLow]) + Number.FromText([SalaryHigh])) / 2
```

* Remove intermediate helper columns once `AverageSalary` is created.
* **Note:** this midpoint is an approximation — it turns a range into a single numeric value so we can aggregate and plot.

5. **Country / Industry**

   * Split columns that included `Other` free-text values to isolate canonical values and keep `Other` as a bucket.

6. **Data types & formatting**

   * Ensure numeric columns (Age, AverageSalary, ratings 0–10) are correctly typed.
   * Convert `AverageSalary` to Decimal Number and format as USD in visuals.

7. **Track steps**

   * All steps are available in Query's `Applied Steps` so transformations are reproducible and editable.

---

## Report visuals & explanation

Below is a concise list of the visuals created on the report canvas and what they communicate.

* **Title** — `Data Professional Survey Breakdown` (top center)

* **Cards (KPIs)**

  * **Total survey takers** — `DISTINCTCOUNT(UniqueID)` (shows ~630 responses)
  * **Average age** — `AVERAGE(Age)` (approx. 30)

* **Average Salary by Job Title**

  * Bar / stacked bar chart showing `AverageSalary` aggregated by the cleaned job title.
  * Reveals which roles reported the highest average salary among respondents (e.g., Data Scientists ~93k in this sample).

* **Favorite Programming Languages**

  * Clustered column chart showing counts per language. Optionally legend-broken by job title to show who prefers what.

* **Country Treemap**

  * Treemap of countries with counts; clicking a country filters all visuals (useful because salary distributions vary strongly by country).

* **Gauges**

  * **Happiness with work-life balance** — gauge (scale 0–10), average value shown.
  * **Happiness with salary** — gauge (scale 0–10), average value shown.

* **Average salary by gender**

  * Donut chart comparing average salary (AverageSalary) for `Male` vs `Female` (shows sample-specific results).

* **Difficulty breaking into data**

  * Donut / pie chart using ordered categories (Very difficult → Very easy) with a custom color palette.

* **Slicers / Filters**

  * Country, Job Title, Programming Language (optional) to allow interactive drill-down.

---

## Key measures & sample formulas

Examples to reproduce core metrics in the model.

**DAX (report-level measures)**

```dax
Total Survey Takers = DISTINCTCOUNT('Survey'[UniqueID])
Average Age = AVERAGE('Survey'[Age])
Average Salary (all) = AVERAGE('Survey'[AverageSalary])
```

**Power Query (custom column for midpoint salary)**

```m
= (Number.FromText([SalaryLow]) + Number.FromText([SalaryHigh])) / 2
```

Format `AverageSalary` as currency (no decimals or 0 decimals) in the visual formatting pane.

---

## Theme & formatting

* Built-in theme: **Frontier** (muted, natural tones), then customized palette for category colors.
* Set consistent font sizes for titles and visuals; center the main report title.
* Adjust numeric formatting (no long decimals) for better readability.

---

## Limitations & caveats

* **Salary approximation**: converting ranges to midpoints loses distribution detail and is only an approximation.
* **Free-text answers**: many `Other` entries (job titles, languages, countries) are not fully standardized — some categories would benefit from fuzzy matching / manual mapping.
* **Currency & cost-of-living**: salaries are expressed in USD as provided — comparisons across countries are affected by exchange rate and purchasing power differences.
* **Survey sample bias**: this was a voluntary survey posted on social platforms — the sample is not necessarily representative of the global data-professional population.

---




