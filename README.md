#  Tech Layoffs — SQL Data Analysis

![SQL](https://img.shields.io/badge/SQL-MySQL-blue) ![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

A two-phase SQL project that cleans a real-world tech layoffs dataset and performs exploratory analysis to surface trends across companies, countries, industries, and time.

---

##  Project structure

```
 sql-layoffs-analysis
┣ 📄 Data_Cleaning_Project.sql
┗ 📄 Exploratory_Data_Analysis_Project.sql
```

---

##  Phase 1 — Data cleaning

Transforms the raw `layoffs` table into a clean, analysis-ready `layoffs_staging2` table.

### Steps performed

- **Duplicate removal** — Uses `ROW_NUMBER() OVER (PARTITION BY ...)` to identify and delete exact duplicates across all key columns.
- **Standardization** — Trims whitespace, normalizes variant spellings (e.g. `Crypto`, `CryptoCurrency` → `crypto`), and strips trailing periods from country names.
- **Date formatting** — Converts text-format dates to proper `DATE` type using `STR_TO_DATE()` and `ALTER TABLE … MODIFY COLUMN`.
- **Null handling** — Blanks set to `NULL`; a self-join fills missing industry values from matching company rows.
- **Row removal** — Drops rows where both `total_laid_off` and `percentage_laid_off` are `NULL`.
- **Cleanup** — Drops the helper `row_num` column after deduplication.

---

##  Phase 2 — Exploratory data analysis

### Key analyses

- Max layoffs and max percentage laid off in a single event
- Companies that laid off 100% of staff — sorted by funding raised
- Total layoffs per company, country, and year
- Layoffs by company stage (Series A, IPO, Post-IPO, etc.)
- Monthly layoff totals with a running cumulative total using `SUM() OVER (ORDER BY month)`
- Top 5 companies by layoffs per year using `DENSE_RANK()` in a nested CTE

### Sample — rolling monthly total

```sql
WITH Rolling_Total AS (
  SELECT SUBSTRING(`date`,1,7) AS `MONTH`,
         SUM(total_laid_off) AS total_off
  FROM layoffs_staging2
  WHERE SUBSTRING(`date`,1,7) IS NOT NULL
  GROUP BY `MONTH`
)
SELECT `MONTH`, total_off,
       SUM(total_off) OVER (ORDER BY `MONTH`) AS rolling_total
FROM Rolling_Total;
```

---

## 🛠 Setup

1. Import the raw `layoffs` table into your MySQL database.
2. Run `Data_Cleaning_Project.sql` to generate the clean staging table.
3. Run `Exploratory_Data_Analysis_Project.sql` to explore the results.

---

##  Skills demonstrated

`CTEs` · `Window functions` · `DENSE_RANK` · `Self-joins` · `Data type conversion` · `NULL handling` · `String manipulation` · `Deduplication` · `Aggregation` · `Rolling totals`

---
Tool: MySQL Workbench*
