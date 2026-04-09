# Graph Sprint 3: GPT Integration

**References:** Letter2 Section 4, Letter1 Sections 4.4, 7

**Depends on:** Graph Sprint 2 (working tool), Letter1 Sprint 5 (GPT orchestration layer)

---

## Sprint Goal

Wire the `get_spending_chart_data` tool into the GPT orchestration layer so that GPT can select and invoke it based on natural-language user requests. After this sprint, a user can ask for a spending chart via chat and the system routes it through GPT → tool → response.

---

## Implementation Tasks

1. **Register tool definition with GPT**
   - Add `get_spending_chart_data` to the GPT tool definitions (alongside existing tools from Letter1 Sprint 5)
   - Include: tool name, description, and parameter schema in the format GPT expects
   - Description should clearly state when to use this tool (e.g., "Use this tool when the user asks for a chart, graph, or visual summary of their spending")

2. **Define GPT parameter extraction expectations**
   - GPT must extract: `chartType`, `groupBy`, date range or year, and category filters from the user's natural-language request
   - Examples of expected mappings:
     - "Show me a pie chart of my spending this year" → `{ chartType: "pie", groupBy: "category", year: 2026 }`
     - "Bar chart of monthly expenses for 2026" → `{ chartType: "bar", groupBy: "month", year: 2026 }`
     - "How much did I spend on food and rent?" → not a chart request — should use `get_spending_summary` instead
   - Document these examples in the tool description or system prompt

3. **Handle ambiguous graph requests**
   - If GPT cannot determine the chart type or grouping from the user's message, it should ask for clarification (Letter1 Section 4.4 ambiguity detection)
   - Examples of ambiguous input:
     - "Show me a graph" → missing chart type and grouping
     - "Chart my spending" → missing chart type
   - The orchestration layer returns a clarification request to the frontend

4. **Validate GPT-extracted parameters**
   - All parameters extracted by GPT must be validated by the tool's Zod input schema before execution (prompt injection defense)
   - If validation fails, return a safe error — do not execute the tool

5. **Pass tool result back to GPT for explanation**
   - After the tool returns chart data, send a summarized version back to GPT
   - GPT generates a natural-language explanation (e.g., "Your biggest expense this year was Rent at $12,000, making up 40% of your total spending")
   - Do not send raw chart data arrays to GPT — send a summary (total, top categories, date range)

6. **Update system prompt if needed**
   - Ensure the system prompt guides GPT to select the chart tool for visual/graph requests
   - Ensure GPT knows not to calculate chart data itself

---

## Expected Deliverables

- `get_spending_chart_data` registered as a GPT tool definition
- GPT can select and invoke the tool based on natural-language input
- Ambiguous chart requests trigger clarification
- Parameter validation enforced before tool execution
- Tool results summarized for GPT explanation
- System prompt updated if needed

---

## Dependencies

- Graph Sprint 2: tool returns real aggregated data
- Letter1 Sprint 5: GPT orchestration layer (API client, tool-call parsing, context management)

---

## Risk Areas

- Tool selection confusion: GPT might confuse `get_spending_chart_data` with `get_spending_summary`. The tool descriptions must clearly differentiate: summary = text breakdown, chart = visual data. Test this with varied phrasing.
- Over-extraction: GPT might hallucinate parameters (e.g., inventing a category name). The Zod validation catches this, but it will produce an error response. Consider having GPT retry or ask for clarification on validation failure.
- Summary for GPT: sending the full labels/values arrays to GPT wastes tokens. Send only: total, top 3 categories with values, date range, chart type.

---

## Test Tasks

### Unit tests:
- Tool definition is correctly formatted for the GPT API
- Parameter extraction produces expected schema from example mappings
- Summary generation for GPT produces a concise result (not raw arrays)

### Integration tests:
- User message "Show me a pie chart of my spending this year" → GPT selects `get_spending_chart_data` → tool returns valid chart data
- User message "Bar chart of monthly expenses" → GPT selects correct tool with `groupBy: "month"`
- User message "Show me a graph" (ambiguous) → GPT returns clarification request
- GPT extracts a non-existent category → validation rejects → safe error returned
- GPT extracts an invalid `chartType` → validation rejects → safe error returned
- Tool result passed to GPT → GPT generates a natural-language explanation

### E2E tests (deferred to Graph Sprint 4 when chart renders in UI):
- Full flow tested after frontend chart rendering is connected

---

## Done Criteria

- [ ] Tool registered with GPT and selectable
- [ ] GPT correctly maps natural-language requests to tool parameters
- [ ] Ambiguous requests produce clarification prompts
- [ ] All GPT-extracted parameters validated before execution
- [ ] Tool results summarized for GPT (not raw arrays)
- [ ] GPT generates useful natural-language explanations of chart data
- [ ] System prompt updated to guide chart tool selection
- [ ] All unit and integration tests pass
