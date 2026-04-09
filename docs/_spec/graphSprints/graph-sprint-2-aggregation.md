# Graph Sprint 2: Aggregation Logic

**References:** Letter2 Sections 2, 4, 5

**Depends on:** Graph Sprint 1 (tool schema, validation), Letter1 Sprint 3 (calculation engine)

---

## Sprint Goal

Replace the stub handler with real aggregation logic. After this sprint, the `get_spending_chart_data` tool queries the database, aggregates transaction data using the financial calculation engine, and returns correct, deterministic chart-ready JSON.

---

## Implementation Tasks

1. **Implement category-based aggregation**
   - When `groupBy` is `"category"`: group all matching transactions by category, sum amounts per category
   - Use the calculation engine's category breakdown function (Letter1 Sprint 3)
   - Return labels = category names, values = totals in cents
   - Sort by total descending (largest category first)

2. **Implement month-based aggregation**
   - When `groupBy` is `"month"`: group all matching transactions by month, sum amounts per month
   - Use the calculation engine's monthly grouping function (Letter1 Sprint 3)
   - Return labels = month strings (e.g., "2026-01", "2026-02"), values = totals in cents
   - Sort chronologically

3. **Implement date-range filtering**
   - Apply `startDate`/`endDate` filter to the database query
   - Apply `year` filter (Jan 1–Dec 31 of that year) if no explicit date range is provided
   - Default to current year if neither `year` nor date range is provided
   - Use the data access layer's date-range query (Letter1 Sprint 2)

4. **Implement category filtering**
   - When `includeCategories` is provided, only include transactions matching those categories
   - Category matching uses the same normalization as the data model (Letter1 Sprint 2)

5. **Implement total calculation**
   - Compute the sum of all `values` in the result set
   - Return as `total` in cents

6. **Implement highlights**
   - Find the label with the largest value
   - Return as `{ largestLabel, largestValue }`
   - If the result set is empty (no matching transactions), return `highlights: null`

7. **Implement title generation**
   - Generate a descriptive title based on the request parameters
   - Examples: "Spending by Category — 2026", "Monthly Spending — Jan 2026 to Jun 2026"

8. **Implement date range in output**
   - Return the actual applied date range in the `dateRange` field
   - If derived from `year`, return `{ start: "2026-01-01", end: "2026-12-31" }`

9. **Wire the handler into the registered tool**
   - Replace the stub handler from Graph Sprint 1 with the real implementation
   - Ensure the output passes the output Zod schema validation

---

## Expected Deliverables

- Real aggregation handler for `get_spending_chart_data`
- Category-based and month-based grouping
- Date-range and category filtering
- Total, highlights, title, and dateRange computation
- All output validated against the Zod output schema
- Deterministic: same input + same data = same output

---

## Dependencies

- Graph Sprint 1: tool registered with schemas and validation
- Letter1 Sprint 2: data access layer (date-range and category queries)
- Letter1 Sprint 3: calculation engine (category breakdown, monthly grouping, totals)

---

## Risk Areas

- Query performance: for MVP scale this is not a concern, but ensure the database indexes from Letter1 Sprint 2 (on `date` and `category`) are in place.
- Empty results: every code path must handle zero matching transactions — return valid empty output, not errors or crashes.
- Sorting consistency: define sort order explicitly (category: descending by total; month: chronological) so output is deterministic.

---

## Test Tasks

### Unit tests:
- Category aggregation: 3 categories with known amounts → correct labels, values, and total
- Category aggregation: single category → one-element arrays
- Category aggregation: sorted by total descending
- Monthly grouping: transactions across 4 months → correct labels and values
- Monthly grouping: sorted chronologically
- Highlights: returns the label with the largest value
- Highlights: returns null for empty result set
- Title: generates correct title for category grouping with year
- Title: generates correct title for monthly grouping with date range
- Date range output: correctly reflects applied range from year parameter
- Date range output: correctly reflects explicit startDate/endDate

### Integration tests:
- Seed database with known transactions → call tool with `groupBy: "category"` → verify labels, values, total match expected
- Seed database → call tool with `groupBy: "month"` → verify monthly totals match
- Call tool with `year: 2026` → only 2026 transactions included
- Call tool with `startDate` and `endDate` → only transactions in range included
- Call tool with `includeCategories: ["food", "rent"]` → only those categories in result
- Call tool with date range matching no transactions → valid empty result (labels: [], values: [], total: 0, highlights: null)
- Call tool → output passes Zod output schema validation

### Edge case tests:
- Single transaction in database → correct one-element result
- All transactions in the same category → one label, one value
- All transactions in the same month → one label, one value
- Transaction on boundary dates (startDate and endDate) → included in result

---

## Done Criteria

- [ ] Category-based aggregation returns correct results
- [ ] Month-based aggregation returns correct results
- [ ] Date-range filtering works (year, explicit range, default)
- [ ] Category filtering works
- [ ] Total, highlights, title, and dateRange computed correctly
- [ ] Empty results handled — valid empty output, not errors
- [ ] Output passes Zod output schema validation
- [ ] All math uses integer cents via the calculation engine
- [ ] All unit and integration tests pass
