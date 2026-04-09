Subject: Build Specification for Spending Graph MCP Tool

Dear Coding Agent,

This letter defines the spending graph feature for the finance chatbot MVP described in Letter1. It extends the existing MCP tool layer (Letter1 Section 4.5) and visualization support (Letter1 Section 4.7) with a detailed specification for the graph tool.

All architectural decisions, engineering principles, security rules, and scope boundaries from Letter1 apply here. This letter does not redefine them — it adds detail specific to the graph feature.

---

## 1. Purpose

Add a graph MCP tool that reads the user's saved financial data and returns structured, chart-ready JSON for frontend rendering. The tool supports bar and pie charts.

This tool helps the user understand:
- Category-based spending (e.g., how much went to food vs. rent)
- Time-based spending trends (e.g., monthly totals over a year)
- Relative proportion of expenses (e.g., pie chart of category distribution)

As with all MCP tools (Letter1 constraint): the language model explains the result in natural language but never performs the graph math itself. All totals, aggregations, percentages, and filters come from deterministic backend logic using integer cents or a decimal library.

---

## 2. Functional Requirements

The graph tool must support these use cases:

1. Show spending by category for a selected date range
2. Show monthly spending totals over time
3. Show yearly spending summaries
4. Show filtered results based on selected categories
5. Support both bar and pie chart output modes

**MVP minimum:**
- Yearly spending by category
- Monthly spending trend
- Pie chart of expense distribution
- Category filtering

All interaction is chat-driven (Letter1 design choice). The user requests graphs through the chat (e.g., "Show me a pie chart of my spending this year" or "Bar chart of monthly expenses for 2026"). GPT parses the intent and calls the graph tool. There are no form-based controls — the chat is the only interface.

---

## 3. Tool Design

### Tool name: `get_spending_chart_data`

### Input schema (validated with Zod):

| Parameter | Type | Required | Description |
|---|---|---|---|
| `chartType` | `"bar"` \| `"pie"` | Yes | Chart output mode |
| `groupBy` | `"category"` \| `"month"` | Yes | How to group the data |
| `startDate` | ISO date string | No | Start of date range filter |
| `endDate` | ISO date string | No | End of date range filter |
| `year` | number | No | Filter to a specific year |
| `includeCategories` | string[] | No | Limit results to these categories |

### Output schema:

| Field | Type | Description |
|---|---|---|
| `title` | string | Descriptive chart title (e.g., "Spending by Category — 2026") |
| `chartType` | `"bar"` \| `"pie"` | Echoes the requested chart type |
| `labels` | string[] | X-axis labels (categories or months) |
| `values` | number[] | Corresponding values in cents |
| `currency` | string | Currency code (e.g., "USD") |
| `total` | number | Sum of all values in cents |
| `dateRange` | `{ start: string, end: string }` | The applied date range |
| `appliedCategories` | string[] \| null | Categories included, or null if all |
| `highlights` | `{ largestLabel: string, largestValue: number }` \| `null` | Highest data point for quick summary, or null when the result set is empty |

The output must be deterministic: same input always produces the same output for the same data.

---

## 4. Architecture

This feature follows the existing architecture from Letter1. The graph tool fits into the stack as:

1. **Chat interface** — user requests a graph via chat message
2. **GPT orchestration** — parses intent, selects `get_spending_chart_data`, extracts parameters. If the request is ambiguous (e.g., no date range specified), GPT asks for clarification instead of guessing (Letter1 Section 4.4 ambiguity detection).
3. **MCP tool layer** — receives validated parameters, calls the business logic layer
4. **Financial calculation engine** — aggregates transaction data using deterministic math (integer cents)
5. **Persistence layer** — queries transactions from SQLite filtered by date range and categories
6. **Response** — structured JSON returned to frontend, rendered as a chart; GPT provides a natural-language summary alongside it

### Prompt injection defense (Letter1 Section 7):
Validate and constrain all GPT-generated tool-call parameters before executing the graph tool. Never trust GPT output as safe input — enforce allowed `chartType` values, valid date formats, and existing category names server-side.

---

## 5. Data Model Notes

This tool reads from the existing data model defined in Letter1 Section 4.2:
- **Transactions:** id, amount, category, date, description, created_at
- **Categories:** id, name (auto-created from transaction input)

No new tables are needed. The graph tool queries transactions and groups/aggregates them.

Ensure efficient queries:
- Index on `date` for date-range filtering
- Index on `category` for category-based grouping

---

## 6. Validation Rules

All inputs must be strictly validated before execution:

- `chartType` must be `"bar"` or `"pie"` — reject anything else
- `groupBy` must be `"category"` or `"month"` — reject anything else
- `startDate` and `endDate` must be valid ISO date strings; `startDate` must be before `endDate`
- `year` must be a reasonable integer (e.g., 2000–2100)
- `includeCategories` entries must match existing category names in the database
- If no transactions exist for the requested range, return a valid empty result (empty `labels` and `values` arrays, `total: 0`), not an error

Return safe, consistent error responses for invalid requests. Do not expose internal details.

---

## 7. Testing Requirements

Tests for this feature must cover positive, negative, and edge cases across all three levels.

### Unit tests:
- Category aggregation produces correct totals
- Monthly grouping produces correct totals
- Percentage calculations for pie charts are accurate
- Empty dataset returns valid empty output
- Label/value arrays are always the same length
- Integer cent math produces no rounding errors

### Integration tests:
- Tool execution returns correct chart data from seeded transactions
- Date-range filtering returns only matching transactions
- Category filtering returns only matching categories
- Invalid parameters return appropriate error responses
- Empty database returns valid empty result

### E2E tests:
- User asks for a spending graph via chat, receives a rendered chart
- Category filters applied via chat update the chart result
- Switching between bar and pie via chat works correctly
- User with no transaction data sees an empty state, not an error
- Ambiguous requests trigger a clarification prompt from GPT

---

## 8. Spec and Sprint Location

Create a dedicated spec for this feature in `docs/specs/` (consistent with Letter1 Section 3). The spec must follow the template defined in Letter1 Section 3 (feature name, user stories, functional requirements, acceptance criteria, test requirements, etc.) and include a sprint plan.

---

## 9. MVP Acceptance Criteria

This feature is complete at MVP level when:

1. The user can request a spending graph through the chat
2. The backend returns correct, deterministic chart-ready JSON
3. The frontend renders a bar or pie chart from the tool output
4. Category and date-range filtering work correctly via chat
5. GPT explains the graph result using tool data (not its own calculations)
6. Empty data produces a valid empty state, not an error
7. Invalid inputs are rejected with safe error responses
8. Prompt injection defenses are in place for tool parameters
9. Unit, integration, and E2E test coverage exists for this feature
10. The feature spec is documented in `docs/specs/`

---

## 10. Execution Guidance

Start by defining the tool schema, then the aggregation logic, then the frontend rendering contract, then the tests. Build tests alongside implementation, not after.

This feature is an extension of the existing MCP tool layer and visualization support from Letter1 — not a standalone system. Reuse the financial calculation engine (Letter1 Section 4.6) for all aggregation math.
