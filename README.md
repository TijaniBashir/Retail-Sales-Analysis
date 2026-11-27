# Retail Sales Analysis: Kakuma Fresh Mart

**A data analysis portfolio project turning a messy three-branch sales export into clear staffing and stock decisions.**

![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)
![pandas](https://img.shields.io/badge/pandas-data%20analysis-150458?logo=pandas&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-notebooks-F37626?logo=jupyter&logoColor=white)

---

## Executive Summary

Kakuma Fresh Mart operates three branches (Kakuma Town, Lodwar and Kitale). Over three weeks in October 2026 it recorded **2,509 raw sales lines** with duplicates, inconsistent labels, mixed date formats, missing values and impossible quantities. I explored and cleaned this data, then analysed it to find where revenue comes from, what drives it, how customers behave and when sales peak.

**The bottom line: no single branch is weak. Sales are concentrated in a few products and in the weekend, so staffing and stock should follow those.**

| Headline metric | Result |
|---|---|
| Net revenue (3 weeks) | **KES 949,214** across 2,464 sales lines |
| Average revenue per sales line | **KES 385** |
| Largest branch | **Kakuma Town, about 40% of revenue** |
| Revenue concentration | **5 of 19 products bring in 54%** of revenue |
| Weekend effect | Weekend days earn about **38% more** per day than weekdays |
| Loyalty effect | Members spend **no more** per line than other shoppers (KES 386 vs 384) |

## Business Problem

Management wanted to understand what drives sales so that staffing, stock and loyalty decisions are based on evidence. The analysis answers four questions:

1. **Where does revenue come from?** Which branch earns the most, and is it because customers spend more or because there are more of them?
2. **What drives revenue?** Which categories and products matter most by share of revenue, not just units sold?
3. **Do customers behave differently?** Do loyalty members spend differently from other shoppers, and how do customers pay?
4. **When do we sell most?** Which days perform best, and is the pattern consistent across branches?

## Dataset

| | |
|---|---|
| **Records** | 2,509 raw rows × 9 columns, reduced to 2,467 rows × 13 columns after cleaning |
| **Period** | 1–21 October 2026 |
| **Scope** | 3 branches, 19 products, 6 categories |
| **Grain** | One row per sales line (one product on one receipt), not per customer visit |
| **Key fields** | receipt, date, branch, product, category, quantity, price, payment method, customer ID |

The data file is not included in this repository. Place the raw CSV in a `Datasets/` folder to run the notebooks.

## Methodology

The project follows three stages, each in its own notebook.

### 1. Explore (`Exploring_Data_With_Pandas.ipynb`)

Profiled the raw data to find what needed fixing before any analysis:

- Checked shape, data types and missing values: 12 missing quantities, 44 missing prices, 61 missing payment methods and about 1,400 blank customer IDs.
- Found **30 duplicate rows**.
- Found prices stored as text, including a `TBC` placeholder, and **5 zero prices**.
- Found impossible and extreme quantities: negative values (returns) and unusually large orders of **999, 500 and 250** units.
- Found inconsistent labels: 9 spellings of 3 branches, 18 spellings of 6 categories, and M-Pesa written three ways.
- Found two date formats mixed together (`YYYY-MM-DD` and `DD/MM/YYYY`).

### 2. Clean (`Cleaning_Data_With_pandas.ipynb`)

Worked on a copy of the raw data and made each decision explicit:

| Problem | Decision |
|---|---|
| Duplicates | Removed (2,509 → 2,479 rows) |
| Branch, category and payment labels | Standardised case, spacing and spelling |
| Missing payment method | Filled with `unknown` |
| Missing quantity | Dropped 12 rows (2,479 → 2,467) |
| Text prices, `TBC` and zero prices | Converted to numeric; invalid values treated as missing |
| Missing prices | Filled with the **median price of the same product** |
| Returns, extreme quantities, non-members | **Flagged rather than deleted** using `is_return`, `unsual_qty` and `new_customer` |
| Imputed values | Tracked with an `imputed_price` flag for transparency |

Principle: **flag instead of delete**, so every cleaning choice stays visible and reversible.

### 3. Analyse (`Analysing_Data_With__Pandas.ipynb`)

- Created `revenue = qty × price` and excluded the three extreme-quantity lines to calculate net revenue.
- Compared branches using `groupby` and `agg` on revenue, sales lines and average revenue per line.
- Measured product and category concentration by share of revenue and compared it with units sold.
- Compared loyalty members with other shoppers on mean and median spend, and analysed payment mix by branch with `crosstab`.
- Compared daily revenue by weekday, then weekends against weekdays overall and by branch using `pivot_table`.

## Key Findings

### 1. Kakuma Town leads through volume, not bigger baskets
Kakuma Town earns about **40% of revenue** (KES 375,795 of 949,214). It has **1,000 sales lines against about 730** at Lodwar and Kitale, while its average per line (KES 376) is the **lowest** of the three branches (Lodwar 395, Kitale 388).

**So what:** its lead rests on traffic, so availability and staffing there protect the largest revenue base.

### 2. Revenue is concentrated in a few products
**Five of 19 products bring in 54% of revenue**, and grains, cooking and dairy together make up **72%**. Eggs is the top earner (about 15% of revenue) yet ranks only **tenth by units sold**, while Water is the top seller by units.

**So what:** units sold and revenue tell different stories. Stock decisions should follow revenue share.

### 3. Loyalty members do not spend more per line
Members average **KES 386** per line against **KES 384** for other shoppers, with medians of 250 and 260. There are over 1,000 lines in each group.

**So what:** the loyalty scheme does not currently lift spend per item, so it is worth asking what it is meant to achieve.

### 4. Cash and M-Pesa dominate payments everywhere
Cash and M-Pesa together account for about **86–89% of sales lines** at every branch, with M-Pesa alone at 40–45%.

**So what:** M-Pesa is nearly as important as cash, so its records need to be reliable.

### 5. Weekends outperform weekdays
Weekend days average about **KES 52,100** in revenue against about **KES 37,700** on weekdays, roughly **38% higher**. Saturday and Sunday are the two strongest days, and weekends are higher in every branch.

**So what:** schedule more staff and make sure stock is full for Saturday and Sunday.

## Business Recommendations

1. **Staff and stock for the weekend** ahead of weekdays.
2. **Keep top-earning products consistently in stock**, led by eggs and the grains, cooking and dairy categories.
3. **Clarify what the loyalty scheme should achieve**, since it does not currently lift spend per line.
4. **Keep M-Pesa records reliable.**

## Limitations and Next Steps

I have documented these openly because they affect how far the numbers can be trusted:

- **Undated rows:** the date fallback in the cleaning notebook uses a hyphen pattern (`%d-%m-%Y`), but the raw dates use slashes. As a result, 198 slash-dated rows are left without a date, which is about 7% of revenue (KES 70,424). These rows are excluded from the weekday and weekend analysis. Changing the pattern to `%d/%m/%Y` is the planned fix, after which the weekend figures should be re-run.
- **Three weeks of data:** each weekday appears only three times, so differences between individual weekdays are weak evidence.
- **Lines, not visits:** the file cannot show customer counts or visit frequency, so members may still visit more often even though they do not spend more per line.
- **Assumptions to confirm:** the three extreme quantities are treated as data-entry errors, and missing prices are filled with product medians.

**Next steps:** fix the date parsing and re-run the analysis, add charts to the analysis notebook, and obtain visit-level data to evaluate the loyalty scheme properly.

## Skills Demonstrated

| Area | What this project shows |
|---|---|
| **Data cleaning** | Deduplication, label standardisation, type conversion, handling missing and impossible values, median imputation |
| **Exploratory analysis** | Profiling with `shape`, `isna`, `describe`, `value_counts` and `unique` to find quality issues before analysing |
| **pandas** | `groupby` and `agg`, `transform`, `pivot_table`, `crosstab`, boolean filtering, datetime parsing, feature flags |
| **Data judgement** | Flagging instead of deleting, tracking imputed values, excluding outliers explicitly, noting what the data cannot show |
| **Business analysis** | Separating volume from basket size, revenue share against unit sales, and weekday against weekend patterns |
| **Communication** | Turning results into "so what" implications and prioritised recommendations for non-technical managers |
| **Critical thinking** | Documenting limitations and known issues honestly rather than overstating conclusions |


Run the notebooks in order: **Exploring → Cleaning → Analysing**. The cleaning notebook creates the clean CSV that the analysis notebook reads.

## Author

**Tijani Bashir**
[GitHub](https://github.com/your-username) | [LinkedIn](https://linkedin.com/in/your-profile) | your.email@example.com
