# Order Data Cleaning and Pivot Summary Project

## 1. Problem Summary

The objective of this project was to convert a raw order dataset into a consistent, auditable and analysis-ready workbook.

The source contained mixed date formats, inconsistent capitalization and spacing, missing categorical values, invalid discounts, duplicate records, conflicting order IDs and contradictory order/payment statuses. These issues could materially distort sales, profit, refund and operational summaries.

The completed project:

- Preserves the original raw file.
- Produces a separate cleaned-order workbook.
- Removes only confirmed exact duplicate copies.
- Retains and flags conflicting duplicate records.
- Applies the supplied business rules.
- Creates calculated and quality-control fields.
- Produces a duplicate quality report.
- Produces six filtered and sorted pivot summary outputs.

## 2. Dataset Description

The raw dataset contained **932 order records** and **21 source columns**.

It covers Indian customer orders from **January 2024 through October 2025** across:

- Four principal regions: North, South, East and West.
- Customer segments: Consumer, Corporate, Home Office and Small Business.
- Product categories: Furniture, Office Supplies and Technology.
- Multiple sub-categories, cities, states and shipping modes.
- Order, payment, sales, cost, profit and discount information.

Important source fields include:

| Area | Fields |
|---|---|
| Identification | `order_id`, `customer_id` |
| Dates | `order_date`, `ship_date` |
| Customer | `customer_name`, `segment` |
| Geography | `region`, `state`, `city` |
| Product | `category`, `sub_category`, `product_name` |
| Fulfilment | `ship_mode`, `quantity` |
| Financial | `unit_price`, `discount`, `sales`, `cost`, `profit` |
| Status | `payment_status`, `order_status` |

## 3. Tools Used

- Microsoft Excel-compatible workbooks (`.xlsx`)
- Excel Tables, filters and sorting
- Excel formulas including `IF`, `SUMIFS`, `COUNTIFS`, `SEARCH`, `YEAR` and `TEXT`
- Conditional formatting for invalid records and eligibility
- Formula-backed pivot summary tables
- Markdown for the cleaning log and project documentation

## 4. Cleaning Steps Performed

1. Preserved `raw_orders.xlsx` without overwriting it.
2. Removed leading, trailing and repeated internal spaces.
3. Standardized capitalization for names, segments, regions, categories, sub-categories, shipping modes and statuses.
4. Converted mixed date representations into real Excel dates.
5. Standardized date display to `dd-mmm-yyyy`.
6. Filled missing `region` and `ship_mode` values with `Unknown`.
7. Converted percentage text into numeric discount values.
8. Treated a missing discount as zero only when the supporting sales fields were valid.
9. Removed 20 redundant exact duplicate copies.
10. Retained conflicting duplicate order IDs and flagged them for manual review.
11. Added:
    - `clean_discount`
    - `calculated_sales`
    - `calculated_profit`
    - `profit_margin`
    - `shipping_delay_days`
    - `order_month`
    - `order_year`
    - `data_quality_flag`
    - `completed_sales_eligible`
    - `refund_flag`
12. Applied conditional formatting to invalid discounts, invalid shipping sequences and quality flags.
13. Created separate quality, duplicate and pivot summary outputs.

## 5. Business Rules Applied

| Rule | Applied Decision |
|---|---|
| Missing region | Filled with `Unknown` and flagged |
| Missing ship mode | Filled with `Unknown` and flagged |
| Missing discount | Treated as 0 only when quantity, price, sales, cost and profit were valid |
| Negative discount | Retained but flagged as invalid |
| Discount above range | Discounts above 50% flagged as invalid |
| Cancelled orders | Excluded from completed-sales summaries |
| Failed payments | Excluded from completed-sales summaries |
| Refunded orders | Excluded from completed sales and summarized separately |
| Ship date before order date | Retained but flagged as an invalid shipping record |
| Exact duplicates | First occurrence retained; additional identical copies removed |
| Conflicting duplicate IDs | All distinct records retained and flagged for review |

A record contributes to completed-sales summaries only when:

- `order_status = Completed`
- `payment_status = Paid`
- It has no `INVALID` flag
- It has no unresolved duplicate `REVIEW` flag

## 6. Data Quality Issues Found

| Data-quality issue | Raw data | After exact duplicate removal |
|---|---:|---:|
| Total records | 932 | 912 |
| Exact duplicate copies | 20 | Removed |
| Duplicate order IDs | 31 | Reviewed |
| Conflicting duplicate IDs | 12 | Retained |
| Conflicting records requiring review | 24 | Flagged |
| Missing regions | 26 | 25 filled and flagged |
| Missing ship modes | 22 | 21 filled and flagged |
| Missing discounts | 18 | 18 treated as 0 after validation |
| Negative discounts | 16 | 15 flagged invalid |
| Discounts above 50% | 15 | 15 flagged invalid |
| Ship date before order date | 22 | 21 flagged invalid |
| Refunded records | 72 | 71 summarized separately |

The final cleaned dataset contains:

- **912 records**
- **562 completed-sales eligible records**
- **5,518,942.59** in eligible recorded sales

## 7. Final Pivot Reports

The workbook `pivot_summary.xlsx` contains the following outputs:

| Pivot output | Filter / sorting | Key result |
|---|---|---|
| Sales and profit by region | Eligible orders; Sales descending | South: Sales 1,456,095.75; Profit 415,674.73 |
| Sales and profit by category / sub-category | Eligible orders; Category A-Z and Sales descending | Technology / Copiers: Sales 642,193.24 |
| Order count by ship mode | All cleaned orders; Count descending | Standard Class: 242 orders |
| Profit margin by customer segment | Eligible orders; Margin descending | Home Office: 30.2% |
| Refunded / cancelled / failed by region | All cleaned orders; Status events descending | North: 88 total status events |
| Monthly sales trend | Eligible orders; Chronological | February 2025: Sales 394,607.07 |

Every pivot output is presented as a filterable Excel table. More than two outputs include explicit sorting and filtering.

## 8. Key Business Insights

1. **South generated the highest eligible regional sales**, narrowly ahead of West.
2. **Technology / Copiers was the strongest category and sub-category combination**, generating 642,193.24 in eligible sales.
3. **Small Business produced the highest segment sales** at 1,520,713.74, but its 28.1% margin was the lowest of the four segments.
4. **Home Office had the highest profit margin** at 30.2%.
5. **February 2025 was the strongest month**, with 394,607.07 in sales, 116,654.78 in profit and 39 eligible orders.
6. **Standard Class was the most frequently used shipping mode**, although First Class and Second Class volumes were close.
7. **North had the highest number of exception status events**, led by 48 cancelled orders.
8. Orders with an unknown region contributed approximately 3% of eligible sales, so improving geographic capture would strengthen regional analysis.

## 9. Assumptions and Limitations

### Assumptions

- Slash dates were interpreted as `MM/DD/YYYY`.
- Hyphenated dates were interpreted as `DD-MM-YYYY`, except ISO `YYYY-MM-DD`.
- Valid discounts were assumed to fall between 0% and 50%, inclusive.
- Missing discounts were set to zero only when the other sales fields were valid.
- Pivot sales and profit use the recorded `sales` and `profit` fields for eligible orders.
- Exception columns count status events; one order may appear in more than one exception column.

### Limitations

- Conflicting duplicate order IDs could not be resolved without an authoritative source.
- `Unknown` values were retained because no reliable lookup source was provided.
- Invalid discounts and shipping sequences were flagged rather than silently corrected.
- The dataset does not specify a currency, so monetary values are displayed without a currency symbol.
- The 2025 monthly trend is incomplete because the dataset ends in October 2025.
- Business insights reflect the supplied dataset and should not be treated as a complete company performance report.

## 10. Screenshots

### Cleaned Orders

![Cleaned order data](screenshots/cleaned_orders_preview.png)

### Data Quality Summary

![Data quality summary](screenshots/quality_summary.png)

### Duplicate Handling Summary

![Duplicate handling summary](screenshots/duplicate_summary.png)

### Pivot Report Index

![Pivot report index](screenshots/pivot_index.png)

### Regional Pivot Summary

![Sales and profit by region](screenshots/pivot_region.png)

### Monthly Sales Trend

![Monthly sales trend](screenshots/pivot_monthly.png)

## Final Deliverables

- [`cleaned_orders_fixed.xlsx`](fixed_cleaned_orders/cleaned_orders_fixed.xlsx)
- [`data_quality_report.xlsx`](duplicate_report/data_quality_report.xlsx)
- [`pivot_summary.xlsx`](pivot_summary.xlsx)
- [`cleaning_log.md`](cleaning_log.md)
- `README.md`
