# Sprint 3: Financial Calculation Engine

**References:** Letter1 Sections 4.6, 6

**Depends on:** Sprint 2 (data model, data access layer)

---

## Sprint Goal

Build the deterministic financial calculation engine that all MCP tools will use. This is a pure logic layer — no HTTP, no GPT, no UI. It takes data in, computes results, and returns structured output. After this sprint, every financial calculation the chatbot needs is available as a tested, reusable function.

---

## Implementation Tasks

1. **Implement total calculations**
   - Sum all transaction amounts for a given set of transactions
   - All math uses integer cents — no floating-point arithmetic
   - Return totals in cents

2. **Implement category breakdown**
   - Group transactions by category
   - Sum amounts per category
   - Return an array of `{ category, total }` sorted by total descending

3. **Implement date-range filtering**
   - Filter a set of transactions to only those within a start/end date range
   - Support filtering by year only, month only, or custom range
   - Validate that start date is before end date

4. **Implement monthly grouping**
   - Group transactions by month (e.g., "2026-01", "2026-02")
   - Sum amounts per month
   - Return an array of `{ month, total }` sorted chronologically

5. **Implement average calculations**
   - Average spend per category over a date range
   - Average monthly spend over a date range
   - Handle edge case: zero transactions returns zero, not NaN or error

6. **Implement percentage calculations**
   - Percentage of total spend per category
   - Return as integer basis points (e.g., 2550 = 25.50%) or as a decimal with defined precision
   - Percentages must sum to 100% (handle rounding distribution)

7. **Implement budget vs. actual comparison**
   - Given a category and period, compare actual spend to budget amount
   - Return: budget amount, actual amount, difference, over/under flag
   - Handle case where no budget exists for a category

8. **Define typed interfaces for all inputs and outputs**
   - Every function takes typed input and returns typed output
   - Use Zod schemas or TypeScript interfaces (consistent with the rest of the codebase)

---

## Expected Deliverables

- Calculation functions: totals, category breakdown, date-range filter, monthly grouping, averages, percentages, budget comparison
- All functions operate on integer cents
- Typed interfaces for all inputs and outputs
- No side effects — pure functions that take data and return results
- Full test coverage

---

## Dependencies

- Sprint 2: data access layer (to fetch transaction data for integration tests)
- Sprint 2: Zod schemas (to validate inputs to calculation functions)

---

## Risk Areas

- Percentage rounding: when category percentages don't sum to exactly 100% due to rounding, use a distribution algorithm (e.g., largest remainder method) to adjust.
- Empty data sets: every function must handle zero transactions gracefully — return zero totals, empty arrays, or appropriate neutral values. Never throw on empty input.
- Integer overflow: unlikely at MVP scale, but ensure the math won't silently overflow for very large totals. TypeScript's `number` type handles integers up to 2^53, which is sufficient for MVP.

---

## Test Tasks

### Unit tests:
- Totals: sum of known amounts produces expected result
- Totals: empty array returns 0
- Category breakdown: groups and sums correctly with multiple categories
- Category breakdown: single category returns one entry
- Category breakdown: empty array returns empty result
- Date-range filter: includes boundary dates (start and end are inclusive)
- Date-range filter: excludes dates outside range
- Date-range filter: empty range returns empty result
- Monthly grouping: transactions across multiple months are grouped correctly
- Monthly grouping: sorted chronologically, not alphabetically
- Averages: correct average for known dataset
- Averages: zero transactions returns 0
- Percentages: known dataset produces expected percentages
- Percentages: single category returns 100%
- Percentages: percentages sum to exactly 100% after rounding
- Budget comparison: over budget returns correct difference and flag
- Budget comparison: under budget returns correct difference and flag
- Budget comparison: exactly on budget returns zero difference
- Budget comparison: no budget for category returns appropriate result

### Integration tests:
- Fetch transactions from database → pass to calculation functions → verify results match expected output from seed data

---

## Done Criteria

- [ ] All calculation functions implemented and typed
- [ ] All functions use integer cents — no floating-point money math
- [ ] All functions handle empty input gracefully
- [ ] Percentage rounding distributes correctly to sum to 100%
- [ ] Budget comparison handles all cases (over, under, exact, no budget)
- [ ] All unit tests pass
- [ ] Integration tests confirm correct results from seeded database
