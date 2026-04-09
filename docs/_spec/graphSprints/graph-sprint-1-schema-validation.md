# Graph Sprint 1: Tool Schema and Validation

**References:** Letter2 Sections 3, 6

**Depends on:** Letter1 Sprint 2 (data model), Letter1 Sprint 3 (calculation engine), Letter1 Sprint 4 (MCP tool registration pattern)

---

## Sprint Goal

Define and implement the `get_spending_chart_data` MCP tool with strict Zod input/output schemas and full validation. After this sprint, the tool is registered, validates all inputs, and rejects invalid requests with safe errors — but does not yet perform aggregation or return real data.

---

## Implementation Tasks

1. **Define the input Zod schema**
   - `chartType`: enum `"bar"` | `"pie"` — required
   - `groupBy`: enum `"category"` | `"month"` — required
   - `startDate`: ISO date string — optional, validated format
   - `endDate`: ISO date string — optional, validated format
   - `year`: integer between 2000–2100 — optional
   - `includeCategories`: array of non-empty strings — optional
   - Enforce: if both `startDate` and `endDate` are provided, `startDate` must be before `endDate`

2. **Define the output Zod schema**
   - `title`: string
   - `chartType`: enum `"bar"` | `"pie"`
   - `labels`: array of strings
   - `values`: array of numbers (cents)
   - `currency`: string (e.g., `"USD"`)
   - `total`: number (cents)
   - `dateRange`: object `{ start: string, end: string }`
   - `appliedCategories`: array of strings | null
   - `highlights`: object `{ largestLabel: string, largestValue: number }` | null
   - Enforce: `labels` and `values` arrays must be the same length

3. **Register the tool in the MCP tool layer**
   - Tool name: `get_spending_chart_data`
   - Tool description for GPT: clear, concise description of what it does and when to use it
   - Input schema, output schema, and handler function registered

4. **Implement input validation logic**
   - Validate all parameters via Zod schema
   - Validate `includeCategories` entries against existing category names in the database
   - Return safe, structured error responses for invalid inputs — no internal details
   - Defend against prompt injection: enforce allowed enum values and valid formats server-side

5. **Implement a stub handler**
   - The handler validates inputs and returns a hardcoded valid output for now
   - Real aggregation logic is wired in Graph Sprint 2
   - This allows the schema and validation to be tested independently

---

## Expected Deliverables

- Zod input schema for `get_spending_chart_data`
- Zod output schema for `get_spending_chart_data`
- Tool registered in the MCP layer with description
- Input validation with safe error responses
- Category name validation against database
- Stub handler returning valid hardcoded output
- Prompt injection defenses active

---

## Dependencies

- Letter1 Sprint 2: categories table (for validating `includeCategories`)
- Letter1 Sprint 4: MCP tool registration system

---

## Risk Areas

- `year` vs. `startDate`/`endDate` conflict: define the precedence rule now. Recommendation: if `year` is provided, it sets the date range to Jan 1–Dec 31 of that year. If `startDate`/`endDate` are also provided, they take precedence over `year`. If neither is provided, default to the current year. Document this in the tool description.
- Category name matching: use the same normalization strategy as Letter1 Sprint 2 (e.g., lowercase + trim) when validating `includeCategories` against the database.

---

## Test Tasks

### Unit tests:
- Input schema accepts valid inputs (all fields, required only, various optional combos)
- Input schema rejects: missing `chartType`, missing `groupBy`, invalid `chartType` value, invalid `groupBy` value, `startDate` after `endDate`, `year` outside 2000–2100, empty string in `includeCategories`
- Output schema validates a correct output shape
- Output schema rejects: mismatched `labels`/`values` lengths, missing required fields

### Integration tests:
- Tool invocation with valid input → returns valid (stub) output
- Tool invocation with invalid `chartType` → safe error response
- Tool invocation with non-existent category in `includeCategories` → safe error response
- Tool invocation with `startDate` after `endDate` → safe error response

---

## Done Criteria

- [ ] Input Zod schema defined and tested
- [ ] Output Zod schema defined and tested
- [ ] Tool registered in MCP layer with GPT description
- [ ] All invalid inputs produce safe error responses
- [ ] `includeCategories` validated against database
- [ ] `year` vs. date range precedence documented
- [ ] Prompt injection defenses active on all parameters
- [ ] All unit and integration tests pass
