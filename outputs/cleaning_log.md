# Cleaning Log

## Source

- Input file: `raw_orders.xlsx`
- Source sheet: `raw_orders`
- Raw records reviewed: 932
- Source columns reviewed: 21
- The raw source file was not overwritten.

## Business Rule Decisions

### 1. Missing Region

- Rule: Replace a missing `region` with `Unknown`.
- Records affected in the raw data: 26.
- Quality treatment: Add `FLAG: Missing region filled as Unknown` to `data_quality_flag`.
- Reason: The record remains usable without inventing a geographic value.

### 2. Missing Ship Mode

- Rule: Replace a missing `ship_mode` with `Unknown`.
- Records affected in the raw data: 22.
- Quality treatment: Add `FLAG: Missing ship mode filled as Unknown` to `data_quality_flag`.
- Reason: The order is retained while the missing logistics information remains visible.

### 3. Missing Discount

- Rule: Treat a missing `discount` as `0` only when `quantity`, `unit_price`, `sales`, `cost`, and `profit` contain valid numeric values.
- Records with missing discounts in the raw data: 18.
- Quality treatment: Add `FLAG: Missing discount treated as 0` when the validation succeeds. Otherwise, retain the missing value and add `INVALID: Missing discount with invalid sales fields`.
- Reason: A zero discount is not assumed when the supporting sales fields are incomplete or invalid.

### 4. Invalid Discount

- Allowed range assumption: 0% to 50%, inclusive.
- Negative discounts found in the raw data: 16.
- Discounts above 50% found in the raw data: 15.
- Quality treatment:
  - Negative values: `INVALID: Negative discount`
  - Values above 50%: `INVALID: Discount above allowed range`
- Invalid discount records are retained for review and excluded from valid completed-sales summaries.

### 5. Cancelled Orders

- Cancelled orders found in the raw data: 146.
- Rule: Cancelled orders do not contribute to the completed-sales summary.
- Quality treatment: `EXCLUDE: Cancelled order`

### 6. Failed Payments

- Failed payments found in the raw data: 69.
- Rule: Failed payments do not contribute to the completed-sales summary, even if the order status says `Completed`.
- Quality treatment: `EXCLUDE: Failed payment`

### 7. Refunded Orders

- Refunded payment records found in the raw data: 72.
- Rule: Refunded records are excluded from completed sales and summarized separately.
- Quality treatment: `REFUND: Summarize separately`

### 8. Invalid Shipping Sequence

- Records where `ship_date` is earlier than `order_date`: 22.
- Rule: Retain the record but flag it as an invalid shipping record.
- Quality treatment: `INVALID: Ship date before order date`

### 9. Duplicate Handling

- Exact duplicate copies found: 20.
- Duplicate order IDs found: 31.
- Conflicting duplicate order IDs found: 12.
- Distinct conflicting records flagged for review: 24.
- Rule:
  - Retain the first occurrence of an exact row and remove only additional identical copies.
  - Retain every distinct record under a conflicting `order_id` and flag it for manual review.

## Completed-Sales Eligibility

A record is included in the final completed-sales summary only when:

1. `order_status` is `Completed`.
2. `payment_status` is `Paid`.
3. The record does not contain an `INVALID` quality flag.
4. The record is not an exact duplicate marked for removal.

Refunded, cancelled, returned, failed-payment, and invalid records are excluded from completed sales.

## Audit Principle

No conflicting record is deleted automatically. Replacements such as `Unknown` and assumed zero discounts remain documented through `data_quality_flag`.
