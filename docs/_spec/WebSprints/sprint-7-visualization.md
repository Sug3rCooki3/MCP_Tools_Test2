# Sprint 7: Visualization

**References:** Letter1 Section 4.7, Letter2 (full graph tool spec)

**Depends on:** Sprint 4 (chart data MCP tool), Sprint 6 (chat interface)

---

## Sprint Goal

Add chart rendering to the chat interface. After this sprint, when the user asks for a spending graph via chat, the system returns a rendered bar or pie chart alongside GPT's natural-language explanation. The chart data comes from the `get_spending_chart_data` MCP tool — the frontend only renders it.

---

## Implementation Tasks

1. **Install a chart library**
   - Choose Recharts or Chart.js (with a React wrapper)
   - Install and configure as a frontend dependency

2. **Build a chart rendering component**
   - Accepts the structured chart output from `get_spending_chart_data` (labels, values, chartType, title, currency, total)
   - Renders a bar chart or pie chart based on `chartType`
   - Displays the chart title above the chart
   - Displays the total below or beside the chart
   - Formats values from cents to display currency (e.g., 5000 cents → $50.00)

3. **Integrate chart rendering into the chat message flow**
   - When the backend returns a chart data result, render the chart component inline in the chat alongside GPT's explanation text
   - Non-chart responses continue to render as text/structured data (Sprint 6)
   - Detect the result type and switch rendering accordingly

4. **Implement empty state for charts**
   - When chart data has empty labels/values arrays and total of 0, display a message like "No spending data found for this period" instead of an empty chart
   - Do not render a chart with no data points

5. **Implement chart highlights display**
   - When `highlights` is not null, display the largest category/month and its value as a summary label (e.g., "Highest: Groceries — $450.00")
   - When `highlights` is null (empty result), skip this display

6. **Handle chart errors**
   - If the chart data is malformed or the chart library throws, display a user-friendly error in the chat instead of crashing
   - Log the error for debugging

---

## Expected Deliverables

- Chart rendering component (bar and pie)
- Chart integrated into chat message flow
- Value formatting from cents to currency display
- Empty state handling
- Highlights summary display
- Error handling for chart rendering

---

## Dependencies

- Sprint 4: `get_spending_chart_data` MCP tool
- Sprint 6: chat interface and message rendering system

---

## Risk Areas

- Chart library bundle size: Recharts and Chart.js are both sizable. For MVP this is acceptable, but note it for future optimization.
- Responsive charts: ensure charts render at a reasonable size within the chat message area. Don't let them overflow or be too small to read.
- Currency formatting: establish a single utility function for cents-to-currency formatting (e.g., `formatCents(5000)` → `"$50.00"`). Reuse across all chart and non-chart displays.

---

## Test Tasks

### Unit tests:
- Chart component renders a bar chart with valid labels/values
- Chart component renders a pie chart with valid labels/values
- Chart component formats cent values to currency correctly (e.g., 5000 → "$50.00")
- Empty data (labels: [], values: [], total: 0) → renders empty state message, not an empty chart
- Highlights display: shows largest label and formatted value when highlights is present
- Highlights display: hidden when highlights is null
- Malformed chart data → renders error message, does not crash

### Integration tests:
- User asks "Show me a pie chart of my spending this year" → backend returns chart data → chart renders in chat
- User asks "Bar chart of monthly expenses for 2026" → bar chart renders with correct labels and values
- User asks for chart with no matching data → empty state displayed

### E2E tests:
- Full flow: user requests spending chart via chat → GPT calls chart tool → chart renders correctly → GPT explanation displayed alongside
- Switch from bar to pie via follow-up chat message → new chart renders
- Category filter via chat → chart updates to show filtered data
- No data scenario → empty state message, no broken chart

---

## Done Criteria

- [ ] Bar chart renders correctly from tool output
- [ ] Pie chart renders correctly from tool output
- [ ] Charts display inline in chat messages
- [ ] Values formatted from cents to currency
- [ ] Empty state handled — no empty charts rendered
- [ ] Highlights displayed when present
- [ ] Chart rendering errors handled gracefully
- [ ] All unit, integration, and E2E tests pass
