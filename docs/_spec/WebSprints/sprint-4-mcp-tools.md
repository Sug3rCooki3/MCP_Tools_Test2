# Sprint 4: MCP Tool Layer

**References:** Letter1 Sections 4.5, 7

**Depends on:** Sprint 2 (data access layer), Sprint 3 (calculation engine)

---

## Sprint Goal

Build the MCP tool layer that exposes all financial operations as callable tools with strict Zod schemas. After this sprint, every operation the chatbot needs (CRUD, summaries, comparisons, chart data) is available as a validated, deterministic MCP tool that can be invoked by the GPT orchestration layer.

---

## Implementation Tasks

1. **Define the MCP tool registration pattern**
   - Establish how tools are registered, discovered, and invoked
   - Each tool must have: a name, a description (for GPT), an input Zod schema, an output Zod schema, and a handler function
   - Decide on the MCP server framework or implement a lightweight custom approach

2. **Implement Transaction CRUD tool**
   - `add_transaction` — parse and store a new transaction (auto-creates category if new)
   - `list_transactions` — list transactions with optional date-range and category filters
   - `update_transaction` — update an existing transaction by ID
   - `delete_transaction` — delete a transaction by ID
   - Input validation via Zod; reject invalid inputs with safe error messages

3. **Implement Budget CRUD tool**
   - `create_budget` — set a monthly budget for a category
   - `update_budget` — change an existing budget amount
   - `delete_budget` — remove a budget
   - `list_budgets` — list all budgets
   - Input validation via Zod

4. **Implement Spending Summary tool**
   - `get_spending_summary` — returns totals and category breakdowns for a date range
   - Calls the calculation engine (Sprint 3) for all math
   - Input: date range (optional), categories (optional)
   - Output: total, breakdown by category, count of transactions

5. **Implement Budget Comparison tool**
   - `compare_budget` — actual spend vs. budget for a category and period
   - Calls the calculation engine for comparison logic
   - Input: category, period (month/year)
   - Output: budget amount, actual amount, difference, over/under flag

6. **Implement Averages and Percentages tool**
   - `get_averages` — average spend per category or per month over a date range
   - `get_percentages` — percentage of total spend per category
   - Calls the calculation engine for all math

7. **Chart Data Generation tool — deferred to Graph Sprint series**
   - The `get_spending_chart_data` tool is fully specified and implemented in Graph Sprints 1–2
   - Do not implement it in this sprint — the Graph Sprint series handles schema, validation, stub, and real aggregation logic
   - Ensure the MCP registration system from Task 1 is ready for the graph sprints to register this tool

8. **Implement prompt injection defense for all tools**
   - Validate and constrain all incoming parameters server-side before executing any tool
   - Enforce allowed enum values, valid date formats, existing category names
   - Never trust that inputs from the GPT layer are safe
   - Return safe error responses for invalid inputs — no internal details

9. **Define tool descriptions for GPT**
   - Each tool needs a clear, concise description that GPT can use to decide when to invoke it
   - Include parameter descriptions and example usage in the tool metadata

---

## Expected Deliverables

- MCP tool registration system
- 6 tools implemented: Transaction CRUD, Budget CRUD, Spending Summary, Budget Comparison, Averages, Percentages
- Chart data tool deferred to Graph Sprint series (Graph Sprints 1–2)
- Zod schemas for every tool's input and output
- Prompt injection defenses on all tool inputs
- Tool descriptions for GPT consumption
- Full test coverage

---

## Dependencies

- Sprint 2: data access layer for all database operations
- Sprint 3: calculation engine for all math operations

---

## Risk Areas

- Tool naming conventions: establish a consistent pattern (e.g., `verb_noun`) and stick to it. GPT will use these names to select tools.
- MCP server choice: decide early whether to use an existing MCP SDK or build a lightweight custom layer. For MVP, a custom approach may be simpler.
- Tool granularity: resist the urge to combine tools. Separate tools are easier for GPT to select correctly and easier to test independently.

---

## Test Tasks

### Unit tests:
- Each tool's Zod input schema accepts valid inputs and rejects invalid inputs
- Each tool's Zod output schema validates the tool's actual return shape
- Tool handler calls the correct data access and calculation functions

### Integration tests:
- `add_transaction` with valid input → transaction exists in database
- `add_transaction` with new category → category auto-created
- `add_transaction` with invalid input → safe error response, nothing written to database
- `list_transactions` with date filter → returns only matching records
- `update_transaction` → changes persisted
- `delete_transaction` → record removed
- `create_budget` / `update_budget` / `delete_budget` → full lifecycle
- `get_spending_summary` → returns correct totals from seeded data
- `compare_budget` → returns correct comparison from seeded data
- `get_averages` / `get_percentages` → returns correct calculations from seeded data
- `get_spending_chart_data` → deferred to Graph Sprints (verify MCP registration system supports it)
- Prompt injection attempt (e.g., SQL injection in category name) → rejected safely

### Edge case tests:
- Tool called with empty optional parameters → uses defaults, returns valid result
- Tool called with a date range that matches no data → returns valid empty result
- `compare_budget` for a category with no budget → returns appropriate response
- `delete_transaction` with non-existent ID → returns appropriate error

---

## Done Criteria

- [ ] All 6 tools implemented with Zod schemas (chart data tool deferred to Graph Sprints)
- [ ] All tools callable through the MCP registration system
- [ ] All tools return validated, typed output
- [ ] Prompt injection defenses active on every tool
- [ ] Tool descriptions written for GPT
- [ ] All unit and integration tests pass
- [ ] No tool performs its own math — all delegate to the calculation engine
